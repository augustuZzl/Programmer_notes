# 概述

`Token` 只是一个字符串，需要做的就是在这个字符串中保存信息，用来在客户端和服务器中传递这些信息。我听到最多的就是 `JWT（JSON Web Token）` 了，功能强大，使用起来也很方便，可以控制过期时间，保存用户信息等，不过我们还是不用他，自己实现一个 `token` 功能。

- 用户登录成功后，随机生成一个 ` token` ，以 `token:user` 形式保存在 `redis` 缓存中，可以轻松利用 redis 过期时间，并通过 `cookie` 下发到用户浏览器
- 用户下一次请求，携带 `cookie`，后台能从缓存中取出用户，则表示用户已登录，否则，拒绝用户的请求。

# 保存用户信息

我们在前后端操作的时候，无时无刻不需要用户的信息，所以如何将用户信息更有效的保存下来很关键，不能实时去数据库中查吧。

## 前端保存

之前用户登录成功之后，后台会把用户信息响应给前端，我们就可以将其保存在 `localStroge` 中：

```javascript
if(res.data.code==0){
    var jsonUser = JSON.stringify(res.data.data);
    localStorage.setItem("seckillUser", jsonUser);
    this.$router.push("/");
}else{
```

- 很简单的一个函数就可以保存了，不过都是以字符串形式保存的，所以要将对象转为字符串

## 后端保存

后端就用到一个很重要的东西：`ThreadLocal` ，在 domain 包下创建类：`HomeHolder` ：

```java
@Component
public class HomeHolder {

    private static ThreadLocal<SeckillUser> users = new ThreadLocal<>();

    public SeckillUser getSeckillUser(){
        return users.get();
    }

    public void setSeckillUser(SeckillUser seckillUser) {
        users.set(seckillUser);
    }

    public void clear(){
        users.remove();
    }

}
```

- **@Component：**可以在要用到用户信息的地方使用 **@Autowride** 注入，然后调用`homeHolder.getSeckillUser()` 就行了

# 下发 Token

在 `SeckillUserService` 类中添加一个私有方法：

```java
private void doToken(SeckillUser user, HttpServletResponse response){
    try {
        // 随机生成格式：1067c269-3d80-40b6-88d1-dc88ad1857f8
        String uuid = UUID.randomUUID().toString();
        // 将 “-” 去掉
        String token = uuid.replaceAll("-", "");
        // 1, 保存 token:user 至缓存中,设置过期时间 30 分钟
        String jsonUser = objectMapper.writeValueAsString(user);
        stringRedisTemplate.opsForValue().set(token, jsonUser, 30, TimeUnit.MINUTES);
        // 2, 下发 cookie
        Cookie cookie = new Cookie("token", uuid);
        // 有效期 半个小时
        cookie.setMaxAge(30 * 60 * 1000);
        cookie.setPath("/");
        response.addCookie(cookie);
    } catch (Exception e) {
        e.printStackTrace();
    }

}
```

在 `doLogin` 方法的合适位置（登录成功位置）中调用此方法就行了。

# 发送 Token

客户端在发送请求时，会自动携带 `Token` ，我们只需要设置一个拦截器来拦截就行了：

新建 `interceptor` 包，在包下新建 `LoginInterceptor` 类：

```java
@Component
public class LoginInterceptor implements HandlerInterceptor {

    @Autowired
    private StringRedisTemplate stringRedisTemplate;
    @Autowired
    private ObjectMapper objectMapper;
    @Autowired
    private HomeHolder homeHolder;

    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        // 1，获取用户传来的 cookie
        Cookie[] cookies = request.getCookies();
        // 2，获取 token
        String token = null;
        if(cookies != null) {
            for (Cookie cookie : cookies) {
                if(cookie.getName().equals("token")) {
                    token = cookie.getValue();
                    break;
                }
            }
        }
        // 3，如果获取到了 token
        if(token != null) {
            // 并从缓存中获取到了用户对象
            String jsonUser = stringRedisTemplate.opsForValue().get(token);
            if(jsonUser != null) {
                SeckillUser user = objectMapper.readValue(jsonUser, SeckillUser.class);
                // 前面我们定义了 HomeHolder,现在就可以将 user 保存进去了，HomeHolder 这样就可以用了
                homeHolder.setSeckillUser(user);
                // 放行
                return true;
            }
        }
        // 4，到这里表示 token 无，过期（token过期浏览器也会传过来）等,抛出自定义异常
        throw new MyException(CodeMsg.SESSION_ERROR);
    }

    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {

    }

    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        // 用户的一次请求完成后，就清空保存的信息，避免内存泄露
        homeHolder.clear();
    }
}
```

定义完拦截器后，还要注册到项目中：新建 `config` 包，创建一个 `WebMvcConfig`  类：

```java
@Configuration
public class WebMvcConfig implements WebMvcConfigurer {

    @Autowired
    LoginInterceptor loginInterceptor;

    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(loginInterceptor).excludePathPatterns("/login/do_login");
    }
}
```

- **@Configuration：**相当于这个类是配置类，才能被 `SpringBoot` 扫描
- **excludePathPatterns：**这些路径对拦截器无效

# 测试

启动项目，在浏览器中请求我们之前的测试路径：`http://localhost:8080/hello/1` ：

![](https://ws1.sinaimg.cn/large/006DgVHEly1g4zr4nmfvxj30b8061aa0.jpg)

由于我们没有登录过，所以任意请求都会被登录拦截器拦截，然后抛出自定义异常，然后被统一异常拦截器拦截，返回 `CodeMsg` 信息，表示测试成功。

这是启动前端项目，登陆一下，然后再次请求：`http://localhost:8080/hello/1`：

![](https://ws1.sinaimg.cn/large/006DgVHEly1g4zra9chjkj30bb05n0so.jpg)

返回成功，说明 token 下发成功，请求就成功了。