# MySQL 和 Druid

我们使用 `MySQL` 数据库存储数据，使用 `Druid` 作为数据库连接池

到 Maven 中心仓库找寻所需要的依赖：[mvnrepository](<https://mvnrepository.com/>)

新建数据库：`seckill`

在 `pom.xml` 中添加依赖：

```xml
<!--MySQL-->
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>5.1.46</version>
</dependency>
<!-- Druid -->
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>druid</artifactId>
    <version>1.1.10</version>
</dependency>
```

在 `src/main/resources/application.properties` 中进行配置：

```properties
# 数据库地址
spring.datasource.url=jdbc:mysql://localhost/seckill?useUnicode=true&characterEncoding=utf8&useSSL=false
# 数据库用户名
spring.datasource.username=root
# 数据库密码
spring.datasource.password=253432
# 数据库驱动类
spring.datasource.driver-class-name=com.mysql.jdbc.Driver
# 使用 Druid 连接池
spring.datasource.type=com.alibaba.druid.pool.DruidDataSource
spring.datasource.initialSize=100
spring.datasource.minIdle=500
spring.datasource.maxActive=1000
spring.datasource.maxWait=60000
spring.datasource.timeBetweenEvictionRunsMillis=60000
spring.datasource.minEvictableIdleTimeMillis=30000
spring.datasource.validationQuery=select 'x'
spring.datasource.testWhileIdle=true
spring.datasource.testOnBorrow=false
spring.datasource.testOnReturn=false
spring.datasource.poolPreparedStatements=true
spring.datasource.maxPoolPreparedStatementPerConnectionSize=20
```

# MyBatis

```xml
<!-- Mybatis -->
<dependency>
    <groupId>org.mybatis.spring.boot</groupId>
    <artifactId>mybatis-spring-boot-starter</artifactId>
    <version>2.0.1</version>
</dependency>
```

```properties
# 实体类所在的包
mybatis.type-aliases-package=com.augustu.seckill.domain
# 下划线转换为驼峰
mybatis.configuration.map-underscore-to-camel-case=true
mybatis.configuration.default-fetch-size=100
# ms --3000ms--->3s
mybatis.configuration.default-statement-timeout=3000
# Mapper 文件位置
mybatis.mapper-locaitons=classpath:com/augustu/seckill/dao/*.xml
```

- 基于此配置我们需要创建 `domain` 包和 `dao` 包

# 测试

## 查询测试

### 创建表

新建一个测试表 `test` 在其中手动插入一条数据，然后使用程序进行查询： 

```sql
CREATE TABLE `test` (
  `test_id` int(11) NOT NULL AUTO_INCREMENT,
  `test_name` varchar(255) DEFAULT NULL,
  PRIMARY KEY (`test_id`)
) ENGINE=InnoDB AUTO_INCREMENT=2 DEFAULT CHARSET=utf8mb4;
```

```sql
INSERT INTO test (test_id, test_name) values(1, 'zzl');
```

### 创建实体类

在 `domain` 包下创建 `Test` 类来对应 `test` 表：

```java
public class Test {
    private Integer testId;
    private String testName;

    // getter/setter
}
```

### 三层架构(摘自百度)

将业务应用划分为：界面层（`User Interface layer`）、业务逻辑层（`Business Logic Layer`）、数据访问层（`Data access layer`）。

#### 表示层：

表示层又称表现层UI，位于三层构架的最上层，与用户直接接触，主要是B/S信息系统中的Wed浏览页面。作为Wed浏览页面，表示层的主要功能是实现系统数据的传入与输出，在此过程中不需要借助逻辑判断操作就可以将数据传送到BLL系统中进行数据处理，处理后会将处理结果反馈到表示层中。换句话说，表示层就是实现用户界面功能，将用户的需求传达和反馈，并用BLL或者是Models进行调试，保证用户体验。

`SpringBoot` 提供的注解有：`@Controller`

#### 业务逻辑层：

业务逻辑层BLL的功能是对具体问题进行逻辑判断与执行操作，接收到表现层UI的用户指令后，会连接数据访问层DAL，访问层在三层构架中位于表示层与数据层中间位置，同时也是表示层与数据层的桥梁，实现三层之间的数据连接和指令传达，可以对接收数据进行逻辑处理，实现数据的修改、获取、删除等功能，并将处理结果反馈到表示层UI中，实现软件功能。

`SpringBoot` 提供的注解有：`@Service`

#### 数据访问层：

数据访问层DAL是数据库的主要操控系统，实现数据的增加、删除、修改、查询等操作，并将操作结果反馈到业务逻辑层BLL。在实际运行的过程中，数据访问层没有逻辑判断能力，为了实现代码编写的严谨性，提高代码阅读程度，一般软件开发人员会在该层中编写DataAccessCommon，保证数据访问层DAL数据处理功能。

`SpringBoot` 提供的注解有：`@Mappper` 、`@Repository`

### 创建数据访问层

在 `dao` 包下创建 `TestDao` 接口，来操作数据库，并添加查询方法：

```java
@Mapper
public interface TestDao {

    @Select("Select * from test where test_id = #{id}")
    Test findById(@Param("id") Integer id);

}
```

### 创建业务逻辑层

创建 `service` 包，并在包下创建 `TestService` 类，注入 `TestDao` 进行查询：

```java
@Service
public class TestService {

    @Autowired(required = false)
    private TestDao testDao;

    public Test findById(Integer id){
        return testDao.findById(id);
    }

}
```

- **@Autowired：**将` TestDao` 装配进来，就可以使用 `TestDao` 的方法了。

### 创建表示层

在 `controller` 包下，我们还是写在 `HelloController` 中，注入`TestService` 就行了：

```java
@RestController
public class HelloController {

    @Autowired
    private TestService testService;

    @RequestMapping("/test/{id}")
    public Result<Test> findById(@PathVariable("id") Integer id){
        Test test = testService.findById(id);
        return Result.success(test);
    }

}
```

### 启动测试

启动项目，在浏览器中输入：`http://localhost:8080/test/1` ，成功：

![](https://ws1.sinaimg.cn/large/006DgVHEly1g4x38hkhsbj30a007naa1.jpg)

### 总结

这一步演示了一个基本的功能实现的步骤，以后开发功能基本都是这个流程，`controller` 负责与前端交互，`service` 负责业务逻辑处理，`dao` 负责与数据库交互。

## 事务测试

再来测试一下 `SpringBoot` 提供的事务管理，用起来也是非常简便。

关于事务，有许多理论，在此简单说一下：保证某个操作是原子性的，即某个操作要么全部执行成功，要么全部失败，不能出现成功一部分失败一部分。拿经典的转账操作，一般分为两步：在你的账户上减钱，在他的账户上加钱，不能出现你的账户减了钱，他的却没加钱这种情况，所以就要在一个事务中执行。

在 `TestDao` 新增一个插入数据的方法：

````java
@Insert("Insert into test (test_id, test_name) values(#{testId}, #{testName})")
int insertTest(Test test);
````

在 `TestService` 中新增方法执行插入数据：

```java
public boolean insertTest(){
    Test test2 = new Test();
    test2.setTestId(2);
    test2.setTestName("asd");
    testDao.insertTest(test2);

    Test test1 = new Test();
    test1.setTestId(1);
    test1.setTestName("asd");
    testDao.insertTest(test1);

    return true;
}
```

- 先插入 `id` 为 `2` 的数据，这条不会出现什么问题
- 再插入 `id` 为 `1` 的数据，由于 `id` 是主键，且数据库中已经存在 `id=1` 的数据，所以执行这条会抛异常

在 `HelloController` 中添加方法：

```java
@RequestMapping("/test/testTx")
public Result<Boolean> testTx(){
    Boolean bool = testService.insertTest();
    return Result.success(bool);
}
```

### 不开启事务

当发起请求：`http://localhost:8080/test/testTx` ，执行到插入 id 为 1 的数据时，肯定会抛出异常，先看看结果吧：

![](https://ws1.sinaimg.cn/large/006DgVHEly1g4x3uo9t61j30qb090aay.jpg)

但这时看看数据库：![](https://ws1.sinaimg.cn/large/006DgVHEly1g4x3vnlskpj308j03umx2.jpg)

`id` 为 `2` 的数已经插入了，但 `id` 为 `1` 的数据却插入失败了，这不是所希望的，就相当于你的账户少了钱，但他的账户却没加钱情况。

### 开启事务

在 `TestService` 的方法上加一个注解就 `ok`了：

```java
@Transactional
public boolean insertTest(){
 	......   
}
```

先删除数据库中 `id` 为 `2` 的数据，再发起一次请求，同样会抛出异常，但这时看数据库：

![](https://ws1.sinaimg.cn/large/006DgVHEly1g4x49h9abij308n038746.jpg)

这样说明事务就成功了。

# Redis

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
<!-- 高版本 redis 的 lettuce 连接池需要 commons-pool2 -->
<dependency>
    <groupId>org.apache.commons</groupId>
    <artifactId>commons-pool2</artifactId>
    <version>2.6.2</version>
</dependency>
```

```properties
# Redis
# 服务器地址，即安装 Redis 电脑的 ip 的地址
spring.redis.host=192.168.43.101
# 服务器端口
spring.redis.port=6379
# 数据库索引，Redis 共有 16 个数据库，所以从 0 开始，这里使用第 1 个数据库
spring.redis.database=0
# 连接超时时间
spring.redis.timeout=10000ms
# 连接池最大连接数（使用负值表示没有限制,默认8）
spring.redis.jedis.pool.max-active=8
# 连接池中的最大空闲连接,默认8
spring.redis.lettuce.pool.max-idle=8
# 连接池中的最小空闲连接，默认0
spring.redis.lettuce.pool.min-idle=0
# 连接池最大阻塞等待时间（默认使用负值表示没有限制）
spring.redis.lettuce.pool.max-wait=-1ms
```

## 测试

将 `TestService` 的 `findById` 方法查询出的数据保存至 `Redis` 数据库 

```java
@Autowired
private StringRedisTemplate stringRedisTemplate;

@Autowired
private ObjectMapper objectMapper;

public Test findById(Integer id){
    Test test = null;
    try {
        // 查询出数据
        test = testDao.findById(id);
        // 将数据转为 json 字符串
        String value = objectMapper.writeValueAsString(test);
        // 存入 Redis 中
        stringRedisTemplate.opsForValue().set("test", value);
    } catch (JsonProcessingException e) {
        e.printStackTrace();
    }
    return test;
}
```

- **StringRedisTemplate：**`SpringBoot` 提供的操作 `Redis` 的类
- **opsForValue()：**操作字符串类型
- **ObjectMapper：**`Jackson` 将 `java` 对象转为字符串的类，`SpringBoot` 已集成 `Jackson`

启动项目，请求：`http://localhost:8080/test/1` ：![](https://ws1.sinaimg.cn/large/006DgVHEly1g4x38hkhsbj30a007naa1.jpg)

可以看到操作成功了，这时到 Redis 数据库中查看一下：![](https://ws1.sinaimg.cn/large/006DgVHEly1g4xgdenqc4j309a02qq2r.jpg)

- 上面使用 `StringRedisTemplate` 是以字符串序列化的方式，所以我们存储一个对象时要先把他转为字符串形式进行存储，获取他时又要在转为一个对象，相对繁琐，所以他适合存储字符串类型的数据
- 所以还有一种 `RedisTemplate<K, V>` 可以用来存储对象，不过需要配置才能使用，我也不会配，就先放在这里吧。
- 先自找麻烦使用 `StringRedisTemplate` 吧。

## 缓存 key 设计

可以看到缓存是以键值对的形式保存，键很重要，所以键的名字必须统一规范，避免出现混淆导致错误：

创建 `util` 包，新建 `RedisKeyUtil` 类，以后需要缓存的时候，在往这个类中添加东西。