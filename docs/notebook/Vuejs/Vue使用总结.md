> 直接在 B 站上看了一个使用 Vue.js 的项目，便照着葫芦画瓢做了自己也做了一个项目，在次记录 Vue.js 的使用

# 新建 Vue 项目

创建 Vue 项目需要 node.js 环境，关于 node.js 安装即可了。

1. 安装 vue-cli：这样以后就可以随地执行第二步创建 vue 项目了

   `npm install --gloable vue-cli`

2. 创建项目：在任意位置呼叫出 cmd 窗口：

   `vue init webpack 项目名`

   之后会出现各种条件让你选择：项目名、项目描述、作者等等：

   ```bash
   # 项目名
   ? Project name demo
   # 项目描述
   ? Project description A Vue.js project
   # 作者
   ? Author Augustu1 <1553543482@qq.com>
   ? Vue build standalone
   # 路由
   ? Install vue-router? Yes
   # Es代码检测
   ? Use ESLint to lint your code? No
   # 单元测试
   ? Set up unit tests No
   # 面对面测试？
   ? Setup e2e tests with Nightwatch? No
   # 安装依赖
   ? Should we run `npm install` for you after the project has been created? (recommended) npm
   ```

# 目录结构

## src/components

此文件夹用来创建 .vue 文件，每个 vue 文件可以看成一个 html，包括三部分：

- template：即标签，注意最外层只能有一个标签，例如：最外层不能有两个 div 并列。
- script：即 js 代码，export default 表示此 vue 文件就可以被其他文件引用了。
- style：标签的样式，scoped 表示样式只作用于此 vue 文件。

```vue
<template>
	<h1></h1>
</template>

<script>
	export default {
		
	}
</script>

<style scoped>
</style>
```

关于 export default 有以下几个结构：

```javascript
export default {
    // data 是这个 vue 文件的数据，可以在标签中调用，也可以在下面的 mounted 和 method 方法中通过 this.key 调用
    data() {
        return {
            key: value,
            key1: value1
        }
    },
    // 页面加载时执行此方法内的内容
    mounted() {
        
    },
    // 就是一个个方法了，可以在标签中调用，也可以在方法中通过 this.name() 调用
    methods: {
        name1() {
            
        }，
        name2() {
    
		}
    }
}
```

## src/router/index.js

路由文件，通过它定义控制路径访问 vue 文件：

### 定义路由

假如我们有一个 Test.vue 文件：

```javascript
import Test from '@/components/Test'

const router = new Router({
    mode: 'history', // 默认是 hash ，路径会被格式化为以 # 开头,使用 history 就不会有 # 了
    routes: [
        { path: '/', redirect: '/test' },
        { path: '/test', name: 'test', component: Test }
    ]
});
```

此时我们通过 `npm run dev` 启动项目，在浏览器中输入以下两个路径，都会展示 test.vue 这个界面了。

- http://localhost:8080/ 
- http://localhost:8080/test

### 路由守卫

就相当于过滤器的作用，可以对访问的路径做一些操作：例如限制某些页面的访问权限等，我就在此以登录限制功能来描述了。假如用户登录后，后台会发送给前端（我们的 vue 项目）一个 token ，我们将其保存在 localStorage 中，前台可以通过：`cnpm install jwt_decode --save` 这个包来解析 token。

```javascript
router.beforeEach((to, from, next) =>{
    // 如果是登录、注册页面
    if(to.path == '/login' || to.path == '/register'){
        // 放行
        next();
    } else {
        // 判断是否有 token，localStorage 是 js 的一个本地变量，eleToken 是我们保存 token 时起的名字
        const isLogin = localStorage.eleToken ? true : false;
        // 如果有 token
        if(isLogin){
            // jwt_decode 解析 token 判断是否过期等，进而操作是放行还是去登陆
            const token = jwt_decode(localStorage.eleToken);
            
        } else {// 没有 token
            // 去登陆吧
            next('/login');
        }
    }
});
```

## src/main.js

是个主文件，在此进行全局的配置，例如本项目我使用 ElementUI 搭建界面，使用 axios 进行 http 请求，则：

先安装这两个模块：`cnpm install element-ui` 、`cnpm install axios`

```javascript
import axios from 'axios'
import ElementUI from 'element-ui';
import 'element-ui/lib/theme-chalk/index.css';

Vue.use(ElementUI);	// 这样在别的页面就可以直接使用 element-ui 了
Vue.prototype.$axios = axios; // 别的页面可以直接使用 this.$axios 进行 http 请求
```

## config/index.js

在此进行项目相关配置，例如跨域请求、端口号等，在此记录跨域请求：

```javascript
proxyTable: {
    // 请求geoserver
    '/geoserver': {		// 配置完要重启
        target: 'http://localhost:8080',//设置你调用的接口域名和端口号
        changeOrigin: true,
        pathRewrite: {
            '^/geoserver': ''
        }
    },
}
```

配置完上面以后，比如我们有个接口地址：http://localhost:8080/add/usr  ，此时可以发送请求：

`this.$axios.post('/geoserver/add/usr', {key: value}).then(res => {}).catch(error => {})`

# ElementUI 的使用

前面已经记录了项目中如何引入 elementui，在此就记录如何使用 elementui：

> 主要就是去官网找组件，比如你要实现一个登录功能，那么你就需要一个表单，在组件中找到 from 表单，找到你感兴趣的样式，将他的代码复制粘贴到 vue 中，并使用官网提供的该组件的属性、方法、事件来实现想要的功能

# 问题踩坑

记录一下使用过程中碰到的问题：

- el-dropdown-menu 绑定 click 事件无效：

  ```vue
  <el-dropdown-menu slot="dropdown">
      <el-dropdown-item>个人资料</el-dropdown-item>
      <el-dropdown-item @click="logout">退出</el-dropdown-item>
  </el-dropdown-menu>
  ```

  改为：`@click.native="logout"` ，就好了。

- axios 发送请求传参问题：`this.$axios.post('/api/user/login', {name: 'zzl'})`

  后台使用 spring ：

  ```java
  // 无法接受参数
  @PostMapping("/login")
  public JSONResult doLogin(@RequestParam("name") String name){}
  
  // 通过 map(或者一个 pojo) 可以接受参数
  @PostMapping("/login")
  public JSONResult doLogin(@RequestBody Map map){
      String name = (String) map.get("name");
  }
  ```

- 页面之间传值：一个页面携带某个值到另一个页面：

  网上教程说有两种方法：query 和 param，可是我的 query 根本不起作用，只能用 param，这个 param 其实可以看做路径上的参数：

# 其他

## 引入自定义 js

以 echarts 迁徙图为例：`cnpm install echarts` ，新建一个 qanxi.js ：

```javascript
function returnChart(data) {
    var option = {
        // TODO
    };
    return option;
}
export {
	returnChart
}
```

在 vue 文件中引入 js ：


```javascript
import echarts from 'echarts';
import { returnChart } from './qianxi.js';

mounted() {
    var myChart = echarts.init(document.getElementById("main"));
    var qianxiOption = returnChart(this.qianxiData);
    myChart.setOption(qianxiOption);
}
```



