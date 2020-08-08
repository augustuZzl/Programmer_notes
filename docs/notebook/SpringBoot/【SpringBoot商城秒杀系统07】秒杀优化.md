# 秒杀过程

我们秒杀的逻辑是：

```java
// 1, 获取商品，判断库存
SeckillGoods goods = seckillGoodsService.findById(goodId);
int count = goods.getGoodsStock();
// 没有库存，返回信息
if(count <= 0) {
    throw new MyException(CodeMsg.MIAOSHA_OVER_ERROR);
}
// 2, 判断是否秒杀过（即只让秒杀一次）
SeckillOrder order = seckillOrderService.findByUserIdAndGoodsId(homeHolder.getSeckillUser().getId(), goodId);
if(order != null){
    throw new MyException(CodeMsg.REPEATE_MIAOSHA);
}
// 3, 进入秒杀
long res = seckillGoodsService.doSeckill(homeHolder.getSeckillUser().getId(), goodId);
return res == 1 ? Result.success(true) : Result.error(CodeMsg.MIAOSHA_FAIL);
```

1. 查询数据库，判断库存
2. 查询数据库，判断是否重复秒杀
3. 查询数据库，在数据库层面控制超卖

我们知道限制系统性能的主要瓶颈就是数据库，尤其是我们的 `MySQL` 数据库，性能十分有限。但我们却将所有的秒杀处理全部交给数据库，所以这是效率最低的处理方法。

# 优化思路

主要思想就是减少对数据库访问，减少数据库压力

1. 系统初始化，将商品库存数量加载到 Redis 缓存中
2. 收到秒杀请求，Redis 预减库存，如果库存不足，直接返回失败，这样后面大量请求都不会给系统带来压力
3. 库存充足，且不是重复秒杀的请求封装成消息后进入队列，同时给前端返回一个 `CodeMsg` ，表示正在处理中
4. 秒杀请求出队列，执行秒杀过程：判断库存、重复秒杀、减库存、生成订单
5. 客户端轮询请求服务器是否秒杀成功

为什么要重复进行

# RabbitMQ

## 安装 Erlang

下载地址：<http://erlang.org/download/otp_win64_22.0.exe>

然后一步步安装就可以了。

添加环境变量：

![](https://ws1.sinaimg.cn/large/006DgVHEly1g52x1ak09xj30i5053aa0.jpg)

在 `path` 中添加：

![](https://ws1.sinaimg.cn/large/006DgVHEly1g52x3y2xq2j30en0fn0tc.jpg)

查看是否安装成功：

![](https://ws1.sinaimg.cn/large/006DgVHEly1g52x4xjqtyj30d604e745.jpg)

## 安装 RabbitMQ

下载地址：http://www.rabbitmq.com/install-windows.html，注意版本要与 Erlang 对应。

我下载的是：`rabbitmq-server-3.7.16.exe`，一步步安装就行了。

打开服务，发现已被安装为服务了：

![](https://ws1.sinaimg.cn/large/006DgVHEly1g52xn2rt2zj30q8079q48.jpg)

配置可视化管理界面：`rabbitmq-plugins enable rabbitmq_management`

![](https://ws1.sinaimg.cn/large/006DgVHEly1g52xvzzfj6j30mv09ot8s.jpg)

在浏览器中输入：http://localhost:15672，账户密码都是 `guest`：

![](https://ws1.sinaimg.cn/large/006DgVHEly1g52xy2dhe3j311y0h70ub.jpg)

## SpringBoot 集成

引入依赖并添加配置：

````xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-amqp</artifactId>
</dependency>
````

```properties
spring.rabbitmq.host=127.0.0.1
spring.rabbitmq.port=5672
spring.rabbitmq.username=guest
spring.rabbitmq.password=guest
spring.rabbitmq.virtual-host=/
#消费者数量
spring.rabbitmq.listener.simple.concurrency=10
#消费者最大数量
spring.rabbitmq.listener.simple.max-concurrency=10
#消费，每次从队列中取多少个,取多了，可能处理不过来
spring.rabbitmq.listener.simple.prefetch=1
#消费失败的数据重新压入队列
spring.rabbitmq.listener.simple.default-requeue-rejected=true
#发送,队列满的时候，发送不进去，启动重置
spring.rabbitmq.template.retry.enabled=true
#一秒钟之后重试
spring.rabbitmq.template.retry.initial-interval=1000
#
spring.rabbitmq.template.retry.max-attempts=3
#最大间隔 10s
spring.rabbitmq.template.retry.max-interval=10000
spring.rabbitmq.template.retry.multiplier=1.0
```

新建 `rabbit` 包，创建消息发送者、接受者和配置类

### MQConfig

```java
import org.springframework.amqp.core.Queue;

@Configuration
public class MQConfig {

    static final String QUEUE = "direct_queue";

    @Bean
    public Queue directQueue() {
        // 第一个参数是队列名字， 第二个参数是指是否持久化
        return new Queue(QUEUE, true);
    }

}
```

### MQSender

```java
@Service
public class MQSender {

    @Autowired
    private AmqpTemplate amqpTemplate;

    public void sendMessage(Object message) {
        amqpTemplate.convertAndSend(MQConfig.QUEUE, message);
    }

}
```

### MQReciver

```java
@Service
public class MQReciver {

    Logger logger = LoggerFactory.getLogger(MQReciver.class);

    @RabbitListener(queues = MQConfig.QUEUE)
    public void receiveMessage(Object message){
        logger.info("接收到消息：{}", message);
    }

}
```

### HelloCOntroller 

在 HelloController 中添加测试方法：

```java
@Autowired
MQSender mqSender;

@RequestMapping("/test/mq")
public Result<Boolean> testMQ(){
    mqSender.sendMessage("测试成功");
    return Result.success(true);
}
```

### 测试

启动程序，访问：`http://localhost:8080/test/mq`

控制台打印：测试成功

![](https://ws1.sinaimg.cn/large/006DgVHEly1g53114fztij30qp04v0sv.jpg)

# 消息队列处理秒杀

## 封装消息

将用户信息及要秒杀的商品信息封装消息，用来提交给消息队列，新建 `SeckillMessage` 实体类：

```java
public class SeckillMessage implements Serializable {

    private long userId;
    private long goodsId;

    // getter/setter
}
```

- 此处为了省事，就直接实现序列化接口了

## 配置队列

新建 `SeckillMQConfig` 类：

```java
@Configuration
public class SeckillMQConfig {

    private static final String QUEUE = "seckill_queue";

    @Bean
    public Queue SeckillQueue() {
        // 第一个参数是队列名字， 第二个参数是指是否持久化
        return new Queue(QUEUE, true);
    }

}
```

## 消息发送者

新建 `SeckillMQSender` 类：

```java
@Service
public class SeckillMQSender {

    @Autowired
    private AmqpTemplate amqpTemplate;

    public void sendSeckillMessage(SeckillMessage message) {
        amqpTemplate.convertAndSend(SeckillMQConfig.QUEUE, message);
    }

}
```

## 消息处理者

新建 `SeckillMQReceiver` 类：

```java
@Service
public class SeckillMQReceiver {

    @Autowired
    private SeckillGoodsService seckillGoodsService;
    @Autowired
    private SeckillOrderService seckillOrderService;

    @RabbitListener(queues = SeckillMQConfig.QUEUE)
    public void receiveSeckillMessage(SeckillMessage message){
        long userId = message.getUserId();
        long goodsId = message.getGoodsId();

        // 1, 获取商品，判断库存
        SeckillGoods goods = seckillGoodsService.findById(goodsId);
        int count = goods.getGoodsStock();
        // 没有库存，返回信息
        if(count <= 0) {
            throw new MyException(CodeMsg.MIAOSHA_OVER_ERROR);
        }

        // 2, 判断是否秒杀过（即只让秒杀一次）
        SeckillOrder order = seckillOrderService.findByUserIdAndGoodsId(userId, goodsId);
        if(order != null){
            throw new MyException(CodeMsg.REPEATE_MIAOSHA);
        }

        // 3, 进入秒杀
        seckillGoodsService.doSeckill(userId, goodsId);

    }

}
```

# Redis 预减库存

将商品库存放到缓存中，避免对数据库的访问，并且能在没库存时拦住大量请求

## 商品数量存入缓存

在项目初始化时，存入缓存中，给 `SeckillController` 类实现接口 `InitializingBean`，实现 `afterPropertiesSet()` 方法

```java
@RequestMapping("/seckill")
public class SeckillController implements InitializingBean {
    
    // 项目初始化时将库存数量加载至 redis 中
    @Override
    public void afterPropertiesSet() throws Exception {
        List<SeckillGoods> seckillGoods = seckillGoodsService.getSeckillGoods();
        for (SeckillGoods seckillGood : seckillGoods) {
            stringRedisTemplate.opsForValue()
                    .set(RedisKeyUtil.getSeckillGoodsStockKey(seckillGood.getGoodsId()), String.valueOf(seckillGood.getGoodsStock()));
        }
    }
}
```

- 同样设置好存入 `reids` 中的 `key`：`RedisKeyUtil.getSeckillGoodsStockKey(seckillGood.getGoodsId())`

## 预减库存

```java
// 1，redis 中预减库存
Long remainStock = stringRedisTemplate.opsForValue().decrement(RedisKeyUtil.getSeckillGoodsStockKey(goodId));
if(remainStock != null && remainStock < 0) {
    throw new MyException(CodeMsg.MIAOSHA_OVER_ERROR);
}
```

- 这样就能利用 Redis  拦住大量无用请求了

# 修改秒杀请求接口

上面的工作基本做完了，现在只需修改请求接口就行了

```java
@RequestMapping("/do_seckill/{id}")
public Result<Boolean> doSeckill(@PathVariable("id") Long goodId){

    // 1，redis 中预减库存
    Long remainStock = stringRedisTemplate.opsForValue().decrement(RedisKeyUtil.getSeckillGoodsStockKey(goodId));
    if(remainStock != null && remainStock < 0) {
        throw new MyException(CodeMsg.MIAOSHA_OVER_ERROR);
    }

    // 2, 判断是否秒杀过（即只让秒杀一次）
    SeckillOrder order = seckillOrderService.findByUserIdAndGoodsId(homeHolder.getSeckillUser().getId(), goodId);
    if(order != null){
        throw new MyException(CodeMsg.REPEATE_MIAOSHA);
    }
    // 3, 异步进入秒杀
    SeckillMessage seckillMessage = new SeckillMessage();
    seckillMessage.setUserId(homeHolder.getSeckillUser().getId());
    seckillMessage.setGoodsId(goodId);
    seckillMQSender.sendSeckillMessage(seckillMessage);

    // 4, 返回排队中
    return Result.error(CodeMsg.MIAOSHA_QUEUE);

}
```

# 轮询请求结果

## 后端结果接口

```java
@RequestMapping("/seckillResult/{id}")
public Result<Boolean> success(@PathVariable("id") Long goodId){
    // 获取订单
    SeckillOrder order = seckillOrderService.findByUserIdAndGoodsId(homeHolder.getSeckillUser().getId(), goodId);
    // 有订单，表示秒杀成功
    if (order != null) {
        return Result.success(true);
    }
    // 如果订单数量为 10 说明秒杀已结束，返回失败
    if(seckillOrderService.getCount() != 10) {
        return Result.error(CodeMsg.MIAOSHA_FAIL);
    }
    // 否则继续返回排队中
    return Result.error(CodeMsg.MIAOSHA_QUEUE);
}
```

## 前端修改

```javascript
doSeckill(){
    this.$axios.get("/api/seckill/do_seckill/" + this.goodsId).then(res => {
        if(res.data.code == 500502){
            this.$message({
                message: res.data.msg,
                type: 'success'
            });
            // 开始轮询
            setTimeout(function(){
                _this.getResult();
            }, 1000)
        } else {
            this.$message({
                message: res.data.msg,
                type: 'error'
            });
        }
    })
},
getResult(){
	this.$axios.get("/api/seckill/seckillResult/" + this.goodsId).then(res => {
		if(res.data.code == 0){
			this.$message({
				message: "秒杀成功",
				type: 'success'
			});
		} else if(res.data.code == 500502){
			_this.getResult();
		} else {
			this.$message({
				message: res.data.msg,
				type: 'error'
			});
		}
	})
}

```

# 测试

启动前后端项目，进行秒杀：

![](https://ws1.sinaimg.cn/large/006DgVHEly1g536kktac6j30tc0d8t9h.jpg)

使用 `Jmeter` 测试：

565、1017、1128、1196

为什么没有变化呢？我服了：我们再来测试一下别的接口：`/hello/1`：

```java
@RequestMapping("/hello/{id}")
public Result<String> hello(@PathVariable("id") Integer id){
    System.out.println("来了" + id);
    if(id == 1)
        return Result.success("Hello World");
    else
        return Result.error(CodeMsg.SERVER_ERROR);
}
```

这个接口相当于请求完服务器直接返回，不做任何操作，结果：1211、1150

可以看到，一开始我们就错了。