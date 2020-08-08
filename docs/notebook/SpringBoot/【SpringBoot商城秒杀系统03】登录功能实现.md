# 设计用户表

在数据库 `seckill` 中新建表：`seckill_user`

```sql
CREATE TABLE `seckill_user` (
  `id` bigint(20) NOT NULL AUTO_INCREMENT COMMENT '手机号',
  `nickname` varchar(255) NOT NULL COMMENT '昵称',
  `pwd` varchar(32) DEFAULT NULL COMMENT '密码',
  `salt` varchar(10) DEFAULT NULL COMMENT '盐',
  `head` varchar(128) DEFAULT NULL COMMENT '头像',
  `register_date` datetime DEFAULT NULL COMMENT '注册时间',
  `last_login_time` datetime DEFAULT NULL COMMENT '最后登录时间',
  `login_count` int(11) DEFAULT '0' COMMENT '登录次数',
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;
```

# 用户实体类

在 `domain` 包下新建类：`SeckillUser` ：

```java
public class SeckillUser {
    
    private Long id;
    private String nickname;
    private String pwd;
    private String salt;
    private String head;
    private Date registerDate;
    private Date lastLoginDate;
    private Integer loginCount;
    
    // getter/setter
}
```

# 用户缓存 key

在  `RedisKeyUtil` 类中添加：

```java
private static String SPLIT = "_";
private static String SECKILL_USER = "SECKILL_USER";

// 获取缓存 key：例如：SECKILL_USER_12345678901
public static String getSeckillUserKey(long id){
    return SECKILL_USER + SPLIT + id;
}
```

# 登录页面

登录注册功能几乎一样，只实现登录功能（也没有别人来注册），然后在数据库中自己编造数据就行了，这里实现登录一方面是演示密码加密，另一方面是涉及秒杀，通过登录来限制访问流量。

为了简洁，登录页面就不进行验证了，认为所有的输入都是正确的。

在 `src/component` 文件夹下新建 `Login.vue` ：

```vue
<template>
	<div class="container">
		<el-form :model="loginForm" ref="loginForm" label-width="80px" class="login-form">
		  <div style="text-align: center;"><h4>欢迎登陆</h4></div>
		  <el-form-item label="手机号" prop="mobile">
		  	<el-input v-model="loginForm.mobile" placeholder="请输入手机号"></el-input>
		  </el-form-item>
		  <el-form-item label="密码" prop="password">
			<el-input type="password" v-model="loginForm.password" placeholder="请输入密码"></el-input>
		  </el-form-item>
		  <el-form-item>
			<el-button type="primary" @click="submitForm('loginForm')">登录</el-button>
		  </el-form-item>
		</el-form>
	</div>
</template>

<script>
	export default {
		name: 'Login',
		data() {
			return {
				loginForm: {
					mobile: '',
					password: ''
				}
			}
		},
		methods: {
			submitForm(formName) {
				// 登录函数
			}
		}
	}
</script>

<style>
	.container {
		width: 100%;
		height: 100%;
		padding: 0;
		margin: 0;
		display: flex;
		justify-content: center;
		align-items: center;
		background-color: aliceblue;
	}
	.login-form {
		padding: 50px;
		position: fixed;
		width: 400px;
		box-shadow: 0 2px 4px rgba(0, 0, 0, .12), 0 0 6px rgba(0, 0, 0, .04);
		background-color: white;
	}
</style>
```

在 `src/router/index.js` 文件下添加此页面的路由：

```javascript
import Login from '@/components/Login';

routes: [
    { path: '/', name: 'HelloWorld', component: HelloWorld },
    { path: '/login', name: 'Login', component: Login }
]
```

此时启动前端项目，在浏览器中输入：`http://localhost:8081/login` ：

![](https://ws1.sinaimg.cn/large/006DgVHEly1g4yichv0g0j311y0h93yk.jpg)

# MD5 加密

所谓的两次 MD5 加密，就是在前端做一次，后端做一次：

- 前端：pwd = MD5(明文密码 + 固定盐)

- 后端：pwd = MD5(前端密码 + 随机盐)

1. 前端往后端传送数据时，如果是 `https`， 那么很安全，但如果是 `http` 那么很容易就被截获了，这时如果密码是明文，根据**社会学**，你多个网站通用一个密码，那么你就炸了。
2. 后端在进行一次加密，防止数据库被攻陷
3. 其实，发现做了这两次加密并不能真正的保证安全，有很多破解方法。只不过导致破解的难度成指数级增长，这些都是密码学的知识，博大精深，我等是不能参透的，跟着来就行了......

## 前端加密

安装 `MD5` 包：`cnpm install --save js-md5`：先import MD5，然后编写提交表单函数：

```javascript
import md5 from 'js-md5'

methods: {
    submitForm(formName) {
        this.$axios.post('/api/login/do_login', {
            mobile: this.loginForm.mobile,
            password: md5(this.loginForm.password + 'md5')
        }).then(res => {
            if(res.data.code==0){
                this.$router.push("/");
            }else{
                this.$message({
                    message: res.data.msg,
                    type: 'error'
                });
            }
        });
    }
}
```

- 将密码加密后发送给后台，`/api/login/do_login` 为之前配置的跨域请求，实际请求：`http://localhost:8080/login/do_login`
- 响应成功：如果响应 `code` 是 `0`，表示 `success` ，跳转到主页，其他状态码表示错误，弹出错误信息即可。

## 后端加密

在 `util` 包，创建 `MD5Util` 类：这个工具类直接拷贝就行

```java
public class MD5Util {

    public static String MD5(String key) {
        char[] hexDigits = {
                '0', '1', '2', '3', '4', '5', '6', '7', '8', '9', 'A', 'B', 'C', 'D', 'E', 'F'
        };
        try {
            byte[] btInput = key.getBytes();
            // 获得MD5摘要算法的 MessageDigest 对象
            MessageDigest mdInst = MessageDigest.getInstance("MD5");
            // 使用指定的字节更新摘要
            mdInst.update(btInput);
            // 获得密文
            byte[] md = mdInst.digest();
            // 把密文转换成十六进制的字符串形式
            int j = md.length;
            char[] str = new char[j * 2];
            int k = 0;
            for (byte byte0 : md) {
                str[k++] = hexDigits[byte0 >>> 4 & 0xf];
                str[k++] = hexDigits[byte0 & 0xf];
            }
            return new String(str);
        } catch (Exception e) {
            return null;
        }
    }

}
```

之后处理后端业务就跟之前的三层架构，创建 `LoginController`、`SeckillUserService`、`SeckillUserDao` 3 个类或接口。

### LoginController

```java
import java.util.Map;

@RestController
@RequestMapping("/login")
public class LoginController {

    @Autowired
    private SeckillUserService seckillUserService;

    @RequestMapping("/do_login")
    public Result<SeckillUser> doLogin(@RequestBody Map map){
        SeckillUser user = seckillUserService.doLogin(map);
        if(user != null)
            return Result.success(user);
        else
            return Result.error(CodeMsg.USER_PASSWORD_ERROR);
    }

}
```

### SeckillUserService

```java
@Service
public class SeckillUserService {

    @Autowired(required = false)
    private SeckillUserDao seckillUserDao;
    @Autowired
    private StringRedisTemplate stringRedisTemplate;
    @Autowired
    private ObjectMapper objectMapper;

    public SeckillUser doLogin(Map map) {
        try {
            // 1，得到前端传入的参数
            String mobileStr = (String) map.get("mobile");
            String password = (String) map.get("password");
            // 2，验证用户名和密码格式，例如不为空等等。
            if (StringUtils.isEmpty(mobileStr) || StringUtils.isEmpty(password)) {
                return null;
            }
            long mobile = Long.parseLong(mobileStr);
            // 3，从缓存中查
            String jsonUser = stringRedisTemplate.opsForValue().get(RedisKeyUtil.getSeckillUserKey(mobile));
            // 3.1，如果缓存中没有
            if (jsonUser == null) {
                // 从数据库中查
                SeckillUser user = seckillUserDao.findById(mobile);
                // 3.1.1,如果数据中没有,则用户名密码错误，返回
                if (user == null) {
                    return null;
                    // 3.1.2,如果数据库中有，判断密码是否一致
                } else {
                    // 前端传入的密码，在做一次 MD5 加密
                    String prepwd = MD5Util.MD5(password);
                    // 数据库密码
                    String realpwd = user.getPwd();
                    // 如果相等
                    if (Objects.equals(prepwd, realpwd)) {
                        // 放置缓存中
                        String userString = objectMapper.writeValueAsString(user);
                        stringRedisTemplate.opsForValue().set(RedisKeyUtil.getSeckillUserKey(mobile), userString);
                        // 返回成功
                        return user;
                        // 如果不等
                    } else {
                        return null;
                    }
                }
                // 3.2，如果缓存中有
            } else {
                // 解析成对象
                SeckillUser user = objectMapper.readValue(jsonUser, SeckillUser.class);
                // 判断密码是否一致
                if(Objects.equals(user.getPwd(), MD5Util.MD5(password + user.getSalt()))){
                    return user;
                } else {
                    return null;
                }
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
        return null;
    }
}
```

### SeckillUserDao

```java
@Mapper
public interface SeckillUserDao {

    @Select("Select * from seckill_user where id = #{id} limit 1")
    public SeckillUser findById(@Param("id") long id);

}
```

# 测试

在后端写一个测试方法，用来生成 MD5 密码，手动录入数据库中，然后测试登录：

```java
public class test {
    public static void main(String[] args) {
        // 用户密码是 123456
        // 前端固定盐 md5,一次加密，传入 prepwd
        String prepwd = MD5Util.MD5("123456" + "md5");
        // 后端随机盐 1a2b3c,一次加密，保存至数据库
        String dbpwd = MD5Util.MD5(prepwd + "1a2b3c");
        System.out.println(dbpwd);
    }
}
sout：685398e6baf734f8ffba1b342bf52e50
```

录入数据库中：

![](https://ws1.sinaimg.cn/large/006DgVHEly1g4z7hy16m3j30oi02x74b.jpg)

启动q后、前端程序：输入账号：`12345678911`，密码：`123456`，点击登录，查看结果，成功进入首页：

![](https://ws1.sinaimg.cn/large/006DgVHEly1g4z7l8whwtj30f90a4jrs.jpg)

再到 `redis` 缓存数据库中查看：也保存成功了。

![](https://ws1.sinaimg.cn/large/006DgVHEly1g4z7o7iy0gj30r003lq2t.jpg)



