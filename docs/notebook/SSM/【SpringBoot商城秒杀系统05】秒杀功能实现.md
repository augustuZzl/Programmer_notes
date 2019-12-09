# 数据库设计

## 秒杀商品表

```sql
CREATE TABLE `seckill_goods` (
  `goods_id` bigint(20) NOT NULL AUTO_INCREMENT COMMENT '商品id',
  `goods_name` varchar(16) DEFAULT NULL COMMENT '商品名称',
  `goods_img` varchar(255) DEFAULT NULL COMMENT '商品图片',
  `goods_price` decimal(10,2) DEFAULT '0.00' COMMENT '商品价格',
  `goods_stock` int(11) DEFAULT '0' COMMENT '商品库存',
  `seckill_price` decimal(10,2) DEFAULT '0.00' COMMENT '秒杀价格',
  `start_date` datetime DEFAULT NULL COMMENT '秒杀开始时间',
  `end_date` datetime DEFAULT NULL COMMENT '秒杀结束事时间',
  PRIMARY KEY (`goods_id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;
```

手动插入两条数据：

![](https://ws1.sinaimg.cn/large/006DgVHEly1g50bulyjxmj30pf03pjrh.jpg)

## 商品订单表

```sql
CREATE TABLE `seckill_order` (
  `id` bigint(20) NOT NULL AUTO_INCREMENT COMMENT '订单id',
  `user_id` bigint(20) DEFAULT NULL COMMENT '下单用户id',
  `goods_id` bigint(20) DEFAULT NULL COMMENT '下单商品id',
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;
```

此订单表只是用来记录用户秒杀到的商品，其余信息就不包含了，简单一点。

# 商品列表实现

## 实体类

```java
import java.util.Date;

public class SeckillGoods {
    
    private Long goodsId;
    private String goodsName;
    private String goodsImg;
    private Double goodsPrice;
    private Integer goodsStock;
    private Double seckillPrice;
    private Date startDate;
    private Date endDate;
    
	// getter/setter
}
```

```java
public class SeckillOrder {

    private Long id;
    private Long userId;
    private Long goodsId;

    // getter/setter
}
```

## 后端查询商品列表数据

还是以前的三层架构写法，在此就只展示代码了：

### SeckillGoodsDao

```java
@Mapper
public interface SeckillGoodsDao {

    @Select("Select * from seckill_goods")
    List<SeckillGoods> seckillGoods();

}
```

### SeckillGoodsService

````java
@Service
public class SeckillGoodsService {

    @Autowired(required = false)
    private SeckillGoodsDao seckillGoodsDao;

    public List<SeckillGoods> getSeckillGoods(){
        return seckillGoodsDao.seckillGoods();
    }

}
````

### SeckillController

此处起名 `SeckillController` ，表示所有与秒杀有关的请求都放在这里，不在区分商品和订单了。

```java
@RestController
@RequestMapping("/seckill")
public class SeckillController {

    @Autowired
    private SeckillGoodsService seckillGoodsService;

    @RequestMapping("/goods_list")
    public Result<List<SeckillGoods>> seckillGoods(){
        List<SeckillGoods> seckillGoods = seckillGoodsService.getSeckillGoods();
        return Result.success(seckillGoods);
    }

}
```

运行前、后端程序，先登录确保 `token` 下发，再请求商品列表路径：

![](https://ws1.sinaimg.cn/large/006DgVHEly1g50ckyj0xhj30kv0i0mxn.jpg)

## 前端列表页面实现

新建 `Goods.vue` ，并在 `router/index.js` 中添加路径：

### router/index.js

```javascript
import Goods from '@/components/Goods'

export default new Router({
    mode: 'history',
    routes: [
        { path: '/', name: 'HelloWorld', component: HelloWorld },
        { path: '/login', name: 'Login', component: Login },
        { path: '/goods', name: 'Goods', component: Goods }
    ]
})
```

### Goods.vue

```vue
<template>
	<el-table :data="tableData" style="width: 100%">
		<el-table-column prop="goodsName" label="商品名称" width="180"></el-table-column>
		<el-table-column label="商品图片">
			<template slot-scope="scope">
				<el-image
				  style="width: 50px; height: 50px"
				  :src="scope.row.goodsImg"
				  :fit="fit"></el-image>
			</template>
		</el-table-column>
		<el-table-column prop="goodsPrice" label="商品原价"></el-table-column>
		<el-table-column prop="seckillPrice" label="秒杀价"></el-table-column>
		<el-table-column prop="goodsStock" label="库存数量"></el-table-column>
		<el-table-column label="操作">
			<template slot-scope="scope">
				<el-button size="mini" @click="handleEdit(scope.$index, scope.row)">详情</el-button>
			</template>
		</el-table-column>
    </el-table>
</template>

<script>
	export default {
		data() {
			return {
				tableData: []
			}
		},
		// 页面启动时自动执行该函数，请求后端，返回列表数据
		mounted() {
			this.$axios.get("/api/seckill/goods_list").then(res => {
				// 赋值给表格数据
				this.tableData = res.data.data;
			});
		},
		methods: {
            // 点击详情按钮
			handleEdit(index, row) {
				console.log(index, row);
			},
		},
	}
</script>

<style>
</style>
```

总之，用 `Element` 搭建页面以及处理一些事件真的很方便。

## 测试

将我们 `Login.vue` 的登录成功后代码改为转发至商品列表页：

```javascript
if(res.data.code==0){
    var jsonUser = JSON.stringify(res.data.data);
    localStorage.setItem("seckillUser", jsonUser);
    this.$router.push("/goods");
}else{
```

启动项目，输入账号密码，点击登录后，成功显示商品列表：

![](https://ws1.sinaimg.cn/large/006DgVHEly1g50evoimgdj30yb07sgm3.jpg)

# 商品详情实现

点击详情按钮，获取点击商品的 `id`，携带此 `id` 进入商品详情页面，页面启动时请求后台数据。

## 后端根据 id 查询商品

在 `SeckillGoodsDao` 接口中新增方法：

```java
@Select("Select * from seckill_goods where goodsId = #{id}")
SeckillGoods findById(@Param("id") long id);
```

在 `SeckillGoodsService` 中新增方法：

```java
public SeckillGoods findById(long id) { 
    return seckillGoodsDao.findById(id); 
}
```

在 `SeckillController` 中新增方法：

```java
@RequestMapping("/good/{id}")
public Result<SeckillGoods> findById(@PathVariable("id") Long id){
    SeckillGoods goods = seckillGoodsService.findById(id);
    return Result.success(goods);
}
```

运行，在浏览器中输入：`http://localhost:8080/seckill/good/1` ：

 ![](https://ws1.sinaimg.cn/large/006DgVHEly1g50k6agutkj30j30az74g.jpg)

## 前端页面实现

### router/index.js

新建 `Good.vue` ，并在 `router/index.js` 中添加路径：

```vue
import Good from '@/components/Good'

{ path: '/good/:goodsId', name: 'Good', component: Good }
```

- **/good/:goodsId：** `goodsId` 相当于路径参数，实际的地址像这种形式：`http://localhost:8081/good/1` ，这个路径参数可以是上一个页面传过来的值。

### Goods.vue

先在 `Goods.vue` 页面中的 `handleEdit` 函数处携带 `id` 进入 `Good.vue` 页面

```javascript
methods: {
    handleEdit(index, row) {
        // 点击商品的 goodId
        var goodsId = row.goodsId;
        this.$router.push({
            name: 'Good',
            params: {
                goodsId: goodsId
            }
        })
    },
},
```

### Good.vue

```vue
<template>
	<el-form label-position="right" label-width="80px" :model="goodDetail" v-if="goodDetail != null">
		<el-form-item label="商品名称">
			<el-input v-model="goodDetail.goodsName" :disabled="true"></el-input>
		</el-form-item>
		<el-form-item label="商品图片">
			<el-image style="width: 50px; height: 50px" :src="goodDetail.goodsImg"></el-image>
		</el-form-item>
		<el-form-item label="商品原价">
			<el-input v-model="goodDetail.goodsPrice" :disabled="true"></el-input>
		</el-form-item>
		<el-form-item label="秒杀价">
			<el-input v-model="goodDetail.seckillPrice" :disabled="true"></el-input>
		</el-form-item>
		<el-form-item label="商品数量">
			<el-input v-model="goodDetail.goodsStock" :disabled="true"></el-input>
		</el-form-item>
	</el-form>
</template>

<script>
	export default {
		data() {
			return {
				goodsId: this.$route.params.goodsId,
				goodDetail: null
			}
		},
		mounted() {
			this.$axios.get("/api/seckill/good/" + this.goodsId).then(res => {
				this.goodDetail = res.data.data;
			})
		}
	}
</script>

<style>
</style>
```

运行程序，点击详情按钮，成功进入商品详情页：

![](https://ws1.sinaimg.cn/large/006DgVHEly1g50l9tnk7fj30kz0azmxe.jpg)

# 秒杀倒计时

前面可以看到，我们的商品信息有秒杀开始时间和秒杀结束时间，所以根据这两个信息来实现秒杀倒计时功能。

## 界面设计

在 `Good.vue` 添加以下数据：

```vue
<div>
    <span style="font-size: 30px; margin-left: 10px;">倒计时：</span>
    <span style="font-size: 30px; color: red;">{{ countDownTime }}</span>
    <el-button type="primary" :disabled='!canSeckill' size="small" style="margin-left: 100px;">点击秒杀</el-button>
</div>

<script>
    export default {
        data() {
			return {
				goodDetail: null,
				canSeckill: false
			}
		},
    }
</script>
```

- **countDownTime：**倒计时时间，在稍后的函数中进行复制
- **canSeckill：**控制秒杀按钮可用状态，秒杀开始才能点击

## 控制函数

```javascript
mounted() {
    var _this = this; // 在某些回调函数中 this 含有另外意义，所以保存下她，就可以随处使用了
    var timeout;	// 循环函数句柄，可以用来取消循环
    _this.$axios.get("/api/seckill/good/" + _this.goodsId).then(res => {
        _this.goodDetail = res.data.data;
        // 1，将时间转换为时间戳
        // 1.1,开始时间
        var startTime = new Date(_this.goodDetail.startDate).getTime() / 1000;
        // 1.2,结束时间
        var endTime = new Date(_this.goodDetail.endDate).getTime() / 1000;
        // 每隔一秒执行一次，页面就显示倒计时了
        timeout = setInterval(function(){
            _this.SeckillCountDown(startTime, endTime, timeout)
        }, 1000);

    })
},
methods: {
    SeckillCountDown(startTime, endTime, timeout) {
        // 2，秒杀倒计时
        // 现在时间
        var nowTime = new Date().getTime() / 1000;
        // 2.1, 秒杀未开始
        if(nowTime < startTime) {
            var remainTime = startTime - nowTime;
            // 赋值倒计时时间
            this.countDownTime = Math.floor(remainTime / 3600) + " : " + Math.floor(remainTime / 60) + " : " + Math.floor(remainTime % 60);
        }
        // 2.2, 秒杀进行中
        if(startTime <= nowTime && nowTime <= endTime){
            // 按钮可以点了
            this.canSeckill = true;
            this.countDownTime = "秒杀进行中！"
            // 结束倒计时循环就行了
            clearInterval(timeout);
        }
        // 2.3, 秒杀已结束
        if(nowTime > endTime){
            // 按钮不能点了
            this.canSeckill = false;
            this.countDownTime = "秒杀已结束"
            // 结束倒计时循环就行了
            clearInterval(timeout);
        }
    }
},
```

## 测试

分别在数据库中修改开始秒杀时间，查看页面显示效果：这里随便演示一下：

![](https://ws1.sinaimg.cn/large/006DgVHEly1g50p3b30mnj30io0cu74n.jpg)

# 秒杀功能实现

## SeckillController

在 `SeckillController` 中添加秒杀方法

```java
@Autowired
private SeckillGoodsService seckillGoodsService;
@Autowired
private SeckillOrderService seckillOrderService;
@Autowired
private HomeHolder homeHolder;

@@RequestMapping("/do_seckill/{id}")
public Result<Boolean> doSeckill(@PathVariable("id") Long goodId){

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

}
```

## SeckillGoodsService

添加以下属性或方法：

```java
@Service
public class SeckillGoodsService {

    @Autowired(required = false)
    private SeckillGoodsDao seckillGoodsDao;
    @Autowired
    private SeckillOrderService seckillOrderService;

    @Transactional
    public long doSeckill(Long userId, Long goodId) {
        // 1, 减库存
        reduceStork(goodId);
        // 2, 下订单
        return seckillOrderService.createOrder(userId, goodId);

    }

    private void reduceStork(Long goodId) {
        seckillGoodsDao.reduceStrok(goodId);
    }

}
```

## SeckillOrderService And SeckillOrderDao

新建这两个文件，添加获取订单方法：

```java
@Service
public class SeckillOrderService {

    @Autowired(required = false)
    private SeckillOrderDao seckillOrderDao;

    public SeckillOrder findByUserIdAndGoodsId(Long userId, Long goodId) {
        return seckillOrderDao.findByUserIdAndGoodsId(userId, goodId);

    }
}

@Mapper
public interface SeckillOrderDao {

    @Select("Select * from seckill_order where user_id = #{userId} and goods_id = #{goodId} limit 1")
    SeckillOrder findByUserIdAndGoodsId(@Param("userId") Long userId, @Param("goodId") Long goodId);

}
```

## SeckillGoodsDao

添加以下方法：

```java
@Update("Update seckill_goods SET goods_stock = goods_stock -1  WHERE goods_id = #{goodId} and goods_stock > 0")
void reduceStrok(@Param("goodId") Long goodId);
```

## 前端点击秒杀按钮

```javascript
<el-button type="primary" :disabled='!canSeckill' size="small" style="margin-left: 100px;" @click="doSeckill">点击秒杀</el-button>

doSeckill(){
    this.$axios.get("/api/seckill/do_seckill/" + this.goodsId).then(res => {
        if(res.data.code == 0){
            this.$message({
                message: "秒杀成功",
                type: 'success'
            });
        } else {
            this.$message({
                message: res.data.msg,
                type: 'error'
            });
        }
    })
}
```

## 测试

运行项目，让我们从头到尾体验一遍：

![](https://ws1.sinaimg.cn/large/006DgVHEly1g50rrroi2nj30tr0dbjry.jpg)

至此基本功能已经实现完成了，这篇文章有史以来最长，但其中基本全是代码。

## 超卖

秒杀 `SQL`：

```sql
Update seckill_goods SET goods_stock = goods_stock -1  WHERE goods_id = #{goodId} and goods_stock > 0
```

`MySQL` 会对 `Update` 进行串行化处理，所以会保证不会超卖。

