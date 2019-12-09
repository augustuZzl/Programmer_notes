# 下载安装

官网：<http://jmeter.apache.org/>，点击侧边栏：`Download Releases` ，进入下载页，下载压缩包：

![](https://ws1.sinaimg.cn/large/006DgVHEly1g51fypywdmj30np0amdgg.jpg)

解压，运行 `bin` 目录下的 `jmeter.bat` 就可以启动了

# 基本使用

## 语言主题

刚进入的时候，颜色主题是黑色的，语言是英文，可以将其改为白色、中文：

![](https://ws1.sinaimg.cn/large/006DgVHEly1g51g3brm66j30he06fq3e.jpg)
![](https://ws1.sinaimg.cn/large/006DgVHEly1g51g3hr7x4j30jh09ujrz.jpg)

## 模拟并发

### 添加线程组

![](https://ws1.sinaimg.cn/large/006DgVHEly1g51g5sdjhpj30f9035jro.jpg)

### 设置线程数

总共 10000 次请求

![](https://ws1.sinaimg.cn/large/006DgVHEly1g51g8pv3clj30hu079jrh.jpg)

### 添加 Http 请求

![](https://ws1.sinaimg.cn/large/006DgVHEly1g51g6prtvbj30gn02ymx6.jpg)

### 设置请求参数

请求商品列表数据

![](https://ws1.sinaimg.cn/large/006DgVHEly1g51ga0hlcuj30lj06tmx8.jpg)

### 添加聚合报告

聚合报告用来显示测试结果

![](https://ws1.sinaimg.cn/large/006DgVHEly1g51gbylj25j30km0axjsk.jpg)

### 运行

在 `WebMvcConfig` 类中先把拦截器注释掉：

```java
 @Override
public void addInterceptors(InterceptorRegistry registry) {
    //        registry.addInterceptor(loginInterceptor).excludePathPatterns("/login/do_login");
}
```

运行后端项目，运行 `Jmeter`：

 ![](https://ws1.sinaimg.cn/large/006DgVHEly1g51gqlbwkaj30lq05jdfz.jpg)

- **Throughput：**吞吐量，即反映我们程序的性能
- **Error：**竟然有错误，这些错误导致我们吞吐量下降，可查看项目控制台并没有报错，说明我们项目没有问题

> Windows 提供给 TCP/IP链接的端口为 1024-5000，并且要四分钟来循环回收他们。就导致我们在短时间内跑大量的请求时将端口占满了。

打开 `cmd` ，输入命令：`netstat -n`：查看结果，恐怖如斯：

 ![](https://ws1.sinaimg.cn/large/006DgVHEly1g51hu7n0i0j30ij09gdfw.jpg)

我的机器上从几万个端口都处于 `TIME_WAIT` 状态

注册表修改可用端口号以及等待时间：这时我们的吞吐量达到 `1300` 了，也就是我们系统每秒能够处理这些请求

![](https://ws1.sinaimg.cn/large/006DgVHEly1g51qe2ycyej30lf05zwej.jpg)

# 测试秒杀

1，把注释的拦截器代码打开

2，将 token 过期时间无限延长，避免一些繁琐的操作，我延长至一个月，以后就不用考虑这些了

`stringRedisTemplate.opsForValue().set(token, jsonUser, 30, TimeUnit.DAYS);`

- 生成 `1000` 个用户
- 让 `1000` 个用户登录，生成 `token`，保存下来，这时 `redis` 中也有了 `token`
- 让 `1000` 个用户携带 `token` 请求秒杀接口

## 生成 1000 个用户

在 `util` 包下新建 `UserUtil` 类：

```java
private static final String JDBC_DRIVER = "com.mysql.jdbc.Driver";
private static final String DB_URL = "jdbc:mysql://localhost:3306/seckill";
private static final String USER = "root";
private static final String PASS = "253432";

public static void createSeckillUser(int count) throws Exception{
    // 生成用户
    List<SeckillUser> users = new ArrayList<>(count);
    SeckillUser user = null;
    for(int i = 0; i < count; i++){
        user = new SeckillUser();
        user.setId(15200000000L + i);
        user.setNickname("user" + i);
        user.setSalt("1a2b3c4d");
        user.setPwd(MD5Util.MD5("123456" + "md5"));
        users.add(user);
    }
    // 插入数据库
    Class.forName(JDBC_DRIVER);
    Connection conn = DriverManager.getConnection(DB_URL,USER,PASS);
    String sql = "INSERT into seckill_user (id, nickname, salt, pwd) values (?, ?, ?, ?)";
    PreparedStatement ps = conn.prepareStatement(sql);
    for (SeckillUser user1 : users) {
        user = user1;
        ps.setLong(1, user.getId());
        ps.setString(2, user.getNickname());
        ps.setString(3, user.getSalt());
        ps.setString(4, MD5Util.MD5(user.getPwd() + user.getSalt()));
        ps.addBatch();
    }
    ps.executeBatch();
    ps.close();
    conn.close();

}
```

执行：`createSeckillUser(1000)`，数据库中成功生成：

![](https://ws1.sinaimg.cn/large/006DgVHEly1g51vy9y9n3j30pu06p3yz.jpg)

## 1000 个用户登录

将用户及生成的 `token` 保存到文件中

登录时提交的手机号和密码是放在请求体中的，而且登录成功后，token 直接放在 redis 中，并没有返回，所以新建一个处理方法，修改局部代码，使用完成后删除就行了：

在 `LoginController` 中新增 `doLogin1` 方法：

```java
@RequestMapping("/do_login1")
public Result<String> doLogin1(@RequestParam("mobile")String mobile,
                               @RequestParam("password")String password,
                               HttpServletResponse response){
    Map<String, String> map = new HashMap<>();
    map.put("mobile", mobile);
    map.put("password", password);
    String token = seckillUserService.doLogin1(map, response);
    if(token != null)
        return Result.success(token);
    else
        return Result.error(CodeMsg.USER_PASSWORD_ERROR);
}
```

- **新建请求路径：**`/login/do_login`

### 修改登录方法

在 SeckillUserService 中新增 `doLogin1` 方法：

```java
public String doLogin1(String mobileStr, String password, HttpServletResponse response) {
    try {
        if (StringUtils.isEmpty(mobileStr) || StringUtils.isEmpty(password)) {
            return null;
        }
        long mobile = Long.parseLong(mobileStr);
        // 3，从缓存中查
        String jsonUser = stringRedisTemplate.opsForValue().get(RedisKeyUtil.getSeckillUserKey(mobile));
        // 3.1，如果缓存中没有
        if (jsonUser == null) {
            System.out.println("数据库");
            // 从数据库中查
            SeckillUser user = seckillUserDao.findById(mobile);
            // 3.1.1,如果数据中没有,则用户名密码错误，返回
            if (user == null) {
                return null;
                // 3.1.2,如果数据库中有，判断密码是否一致
            } else {
                // 前端传入的密码，在做一次 MD5 加密
                String prepwd = MD5Util.MD5(password + user.getSalt());
                // 数据库密码
                String realpwd = user.getPwd();
                // 如果相等
                if (Objects.equals(prepwd, realpwd)) {
                    // 放置缓存中
                    String userString = objectMapper.writeValueAsString(user);
                    stringRedisTemplate.opsForValue().set(RedisKeyUtil.getSeckillUserKey(mobile), userString);
                    // 操作 token
                    String token = doToken(user, response);
                    // 返回成功
                    return token;
                    // 如果不等
                } else {
                    return null;
                }
            }
            // 3.2，如果缓存中有
        } else {
            System.out.println("缓存");
            // 解析成对象
            SeckillUser user = objectMapper.readValue(jsonUser, SeckillUser.class);
            // 判断密码是否一致
            if(Objects.equals(user.getPwd(), MD5Util.MD5(password + user.getSalt()))){
                String token = doToken(user, response);
                return token;
            } else {
                return null;
            }
        }
    } catch (Exception e) {
        e.printStackTrace();
    }
    return null;
}
```

- 原先登录方法返回 `SeckillUser` ，现在只是返回 `Token`

### 模拟登陆

```java
public static void createToken(int count) throws Exception {
    // 生成用户
    List<SeckillUser> users = new ArrayList<>(count);
    SeckillUser user = null;
    for(int i = 0; i < count; i++){
        user = new SeckillUser();
        user.setId(15200000000L + i);
        user.setPwd(MD5Util.MD5("123456" + "md5"));
        users.add(user);
    }

    String urlString = "http://localhost:8080/login/do_login1";
    File file = new File("D:/tokens.txt");
    RandomAccessFile raf = new RandomAccessFile(file, "rw");
    raf.seek(0);

    for (SeckillUser seckillUser : users) {
        user = seckillUser;
        URL url = new URL(urlString);
        HttpURLConnection co = (HttpURLConnection)url.openConnection();
        co.setRequestMethod("POST");
        co.setDoOutput(true);
        OutputStream out = co.getOutputStream();
        String params = "mobile=" + user.getId() + "&password=" + user.getPwd();
        out.write(params.getBytes());
        out.flush();
        InputStream inputStream = co.getInputStream();
        ByteArrayOutputStream bout = new ByteArrayOutputStream();
        byte[] buff = new byte[1024];
        int len = 0;
        while((len = inputStream.read(buff)) >= 0) {
            bout.write(buff, 0 ,len);
        }
        inputStream.close();
        bout.close();
        String response = new String(bout.toByteArray());

        JsonNode jsonNode = new ObjectMapper().readTree(response);
        String token = jsonNode.path("data").asText();
        String row = user.getId()+","+token;
        raf.seek(raf.length());
        raf.write(row.getBytes());
        raf.write("\r\n".getBytes());
    }
    raf.close();
    System.out.println("over");

}
```

- 在电脑新建文件：`D:/tokens.txt`，结果将保存到这里

查看 `D:/tokens.txt` 文件和 `Redis` 数据库，发现测试成功：

![](https://ws1.sinaimg.cn/large/006DgVHEly1g51wbq2j22j30fb05n74i.jpg)

![](https://ws1.sinaimg.cn/large/006DgVHEly1g51wbwab5mj30g307z74f.jpg)

## Jmeter 秒杀开始

### 添加 CSV 数据文件

![](https://ws1.sinaimg.cn/large/006DgVHEly1g51wh5ddgrj30o60asab3.jpg)

配置生成的 `token.txt` 文件，并为两列数据起名，这里我们会用到 `userToken` 这个数据

![](https://ws1.sinaimg.cn/large/006DgVHEly1g51wy4zr83j30li08qt8u.jpg)

### 添加 Cookie 管理器

![](https://ws1.sinaimg.cn/large/006DgVHEly1g51x063gnfj30lj0a60tt.jpg)

配置 `cookie` 值，`cookie` 名为 `token`，值为 `token.txt` 文件中 `userToken` 那列数据：

![](https://ws1.sinaimg.cn/large/006DgVHEly1g51x1dx4mdj30l807ajrd.jpg)

### 添加 Http 请求

秒杀第一个商品：

![](https://ws1.sinaimg.cn/large/006DgVHEly1g51x38b9bmj30l006wdfw.jpg)

### 运行

将线程数设为 `1000`，表示我们的 `1000` 个用户同时秒杀，还是循环 `10` 次，相当于一万次并发请求。

### 结果

由于我机器性能有限，便没有安装虚拟机来使用 `Linux` 环境，所有的运行环境都是在 `Win` 上的，无法显示真正的性能。但这不是我们的重点，我们关注的是优化前后的对比。

![](https://ws1.sinaimg.cn/large/006DgVHEly1g51yivskv6j30lp066jrg.jpg)

在看看数据库：秒杀成功

![](https://ws1.sinaimg.cn/large/006DgVHEly1g51ye38d3uj30pc03kmx9.jpg)

复原商品数量，清空订单表，多运行几次，注意每次运行时先等端口被释放，记录下吞吐量：

`395`、`991`、`1034`、`1166`、`1204`、`1164`、`1162`

看来第一次运行的时候 `395` 只是系统的热身，所以导致吞吐量很小，后面基本维持在：`1100` ~ `1200` 之间，

到时候再看看优化完成后差距会是多大。