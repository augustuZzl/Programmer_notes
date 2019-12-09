# 创建 SpringBoot 项目

## 项目创建

1，打开 IDEA -> New Project -> Spring Initializr：

![](https://ws1.sinaimg.cn/large/006DgVHEly1g4v570yve1j30q808ut92.jpg)

2，填写项目基本信息：

![](https://ws1.sinaimg.cn/large/006DgVHEly1g4v58yxkfjj30l30akjrk.jpg)

3，先只选择 Web 模块：

![](https://ws1.sinaimg.cn/large/006DgVHEly1g4v5ab6pfzj30py07oq37.jpg)

4，这就完成了，等待加载就行了。

## 编写测试类

1，在 `src/main/java/com/augustu/seckill` 下新建文件夹 `controller` ，并在 `controller` 文件夹下新建一个类：`HelloController`：

![](https://ws1.sinaimg.cn/large/006DgVHEly1g4v5ijz3sqj30aj07qjtd.jpg)

2，HelloController.java：

```java
@Controller
public class HelloController {

    @RequestMapping("/hello")
    @ResponseBody
    public String hello(){
        return "Hello World";
    }

}
```

- **@Controller：**表示此类是控制器类，用来接受请求
- **@RequestMapping：**代表能够处理的请求路径，例如本例就可处理 `/hello` 请求
- **@ResponseBody：**表示返回给客户请求的结果是你 `return` 的东西，比如本例就返回一个字符串，而如果不加这个注解，则默认会返回页面，比如本例他就会拿着 `hello World` 这个字面量去 `template` 文件夹下去找 `Hello World.html` ，当然找不到了，那就会报错了。

3，HelloController.java：

```java
@RestController
public class HelloController {

    @RequestMapping("/hello")
    public String hello(){
        return "Hello World";
    }

}
```

- **@RestController：**相当于 `@Controller + @ResponseBody` ，这样更方便了。

## 启动项目

运行 `src/main/java/com/augustu/seckill/SeckillApplication` 类的 `main` 方法就行了。

在浏览器中输入：`http://localhost:8080/hello`，可以看到返回了：`Hello World`

![](https://ws1.sinaimg.cn/large/006DgVHEly1g4v5vd5tmij30b005c3yi.jpg)

怎么样，一个 web 应用就这么快速的搭建完成了，很神奇吧！

# 封装数据输出 json 格式

在处理完请求后，要返回给前端数据，告诉自己的处理结果，所以就可以统一输出的接口形式，一般包括：

```json
{
    "code": "状态码",
    "msg": "状态码代表的信息",
    "data": "响应的数据"
}
```

例如，前台查询某个用户信息，那么就可以输出：

```json
{
    "code": 200,
    "msg": "OK",
    "data": {
    	"name": "zzl",
    	"age": 21
	}
}
```

## 创建状态信息类 CodeMsg

新建 `result` 包，在 `result` 包下新建 `CodeMsg` 类，用来定义状态码及对应的信息：

```java
/**
 * 成功时返回统一格式：0, "success", data
 * 失败时返回：code, msg, null
 * 我们将 code, msg 封装成一个类
 */
public class CodeMsg {

    private int code;
    private String msg;

    //通用异常
    public static CodeMsg SERVER_ERROR = new CodeMsg(500100, "服务端异常!");
    public static CodeMsg REQUEST_ILLEAGAL = new CodeMsg(500102, "非法请求!");
    public static CodeMsg MIAOSHA_FAIL = new CodeMsg(500103, "秒杀失败!");
    public static CodeMsg ACCESS_LIMIT = new CodeMsg(500104, "达到访问限制次数，访问太频繁!");
    //登录模块异常
    public static CodeMsg SESSION_ERROR = new CodeMsg(500210, "session失效!");
    public static CodeMsg USER_PASSWORD_ERROR = new CodeMsg(500211, "用户名或密码错误!");
    //订单模块异常
    public static CodeMsg ORDER_NOT_EXIST = new CodeMsg(500410, "订单不存在!");
    //秒杀模块异常
    public static CodeMsg MIAOSHA_OVER_ERROR = new CodeMsg(500500, "商品秒杀完毕，库存不足!");
    public static CodeMsg REPEATE_MIAOSHA = new CodeMsg(500501, "不能重复秒杀!");
    public static CodeMsg MIAOSHA_QUEUE = new CodeMsg(500502, "排队中!");


    public CodeMsg(int code, String msg) {
        this.code = code;
        this.msg = msg;
    }

    public int getCode() {
        return code;
    }

    public String getMsg() {
        return msg;
    }

}
```

## 新建 Result 泛型类

在 `result` 包下新建 `Result` 类，用来返回给前台数据：

```java
public class Result<T> {

    private int code;
    private String msg;
    private T data;

    // 成功
    public static <T> Result<T> success(T data){
        return new Result<T>(data);
    }
    private Result(T data){
        this.code = 0;
        this.msg = "success";
        this.data = data;
    }
    // 失败
    public static <T> Result<T> error(CodeMsg codeMsg){
        return new Result<T>(codeMsg);
    }
    private Result(CodeMsg codeMsg){
        if(codeMsg == null)
            return;
        this.code = codeMsg.getCode();
        this.msg = codeMsg.getMsg();
    }

    // getter/setter
    
}
```

## 测试

我们改写 `helloController.java`

```java
@RestController
public class HelloController {

    @RequestMapping("/hello/{id}")
    public Result<String> hello(@PathVariable("id") Integer id){
        // 如果请求的 id 是 1，返回 “Hello World”
        if(id == 1)
            return Result.success("Hello World");
        // 如果不是 1，返回 “服务器端错误”
        else
            return Result.error(CodeMsg.SERVER_ERROR);
    }

}
```

- **@PathVariable("id")：**表示获取路径变量，比如我们的请求路径是：`/hello/2019` ，那么 `id` 就是 `2019`

启动项目，分别在浏览器中输入：`http://localhost:8080/hello/1` 和 `http://localhost:8080/hello/2` :

![](https://ws1.sinaimg.cn/large/006DgVHEly1g4w1826drcj30a105rt8n.jpg)

![](https://ws1.sinaimg.cn/large/006DgVHEly1g4w18a33ogj309c05kq2v.jpg)

# 统一异常处理

程序发生运行时异常时会导致程序终止，所以我们可以拦截这些异常，进行必要的处理，以免影响用户体验，此外还可以自定义异常，在任何地方抛出，使用异常拦截器进行处理，好处多多：

## 自定义异常

在 `result` 包下新建 `MyException` 类：

```java
public class MyException extends RuntimeException {

    private CodeMsg codeMsg;

    public MyException(CodeMsg codeMsg){
        this.codeMsg = codeMsg;
    }

    public CodeMsg getCodeMsg(){
        return codeMsg;
    }

}
```

## 异常处理器

在 `result` 包下新建 `GlobalExceptionHandle` 类：

```java
@ControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(value = Exception.class)
    @ResponseBody
    public Result handle(Exception e){
        // 如果是手动抛出的异常，返回手动设置的错误
        if(e instanceof MyException){
            MyException myException = (MyException) e;
            return Result.error(myException.getCodeMsg());
        // 如果是别的异常，统一返回 服务器错误
        } else {
            e.printStackTrace();
            return Result.error(CodeMsg.SERVER_ERROR);
        }
    }

}
```

- **@ControllerAdvice：**

# 创建 Vue 项目

关于 Vue 的使用 ，之前写了一点小总结：[点我传送](/2019/07/09/Vue使用总结/)

右键项目名，选择：`Open in Terminal`，我们直接把 `Vue` 项目创建在我们的后台项目中：

![](https://ws1.sinaimg.cn/large/006DgVHEly1g4wbapdvwdj30og0e1wm3.jpg)

找到 `src/App.vue` 文件，删除 `<img src="./assets/logo.png">` 这句话，并将样式修改为：

```css
html, body, #app {
    width: 100%;
    height: 100%;
    padding: 0;
    margin: 0;
}
```

## 修改端口号

我们的后台是 `8080`， 前端就修改为 `8081`

把 `config/index.js` 的 `port` 修改为 `8081` 即可

## 配置跨域

前后端之间是跨域请求，我们要配置跨域，同样是 `config/index.js` 这个文件：

```javascript
proxyTable: {
    '/api': {
        target: 'http://localhost:8080',
        changeOrigin: true,
        pathRewrite: {
            '^/api': ''
        }
    },
}
```

这样配置完成后，当要请求 `http://localhost:8080/hello` 时，只需要写：`/api/hello` 就可以了。

## 添加模块

### Element

执行：`cnpm install element-ui --save`

在 `src/mian.js` 文件下添加：

```javascript
import ElementUI from 'element-ui';
import 'element-ui/lib/theme-chalk/index.css';

Vue.use(ElementUI);  
```

### axios

执行：`cnpm install axios --save`

在 `src/mian.js` 文件下添加：

```javascript
import axios from 'axios';
Vue.prototype.$axios = axios;
```

