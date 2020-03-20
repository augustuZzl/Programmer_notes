### Vue.js 引入

- CDN 引入

  ```html
  <!-- 开发环境版本，包含了有帮助的命令行警告 -->
  <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
  
  <!-- 生产环境版本，优化了尺寸和速度 -->
  <script src="https://cdn.jsdelivr.net/npm/vue"></script>
  ```

- 下载

  ```html
  开发环境：https://vuejs.org/js/vue.js
  生产环境：https://vuejs.org/js/vue.min.js
  ```

- NPM 安装（nodejs 方式）：通过 webpack 和 CLI 使用

### Vue 初体验

```html
<div id="app">
    <h1>{{msg}}</h1>
    <ul>
        <li v-for="item in movies">{{item}}</li>
    </ul>
    <h3>当前计数：{{counter}}</h3>
    <button v-on:click="AddCounter">+</button>
    <button @click="counter--">-</button>
</div>

<script src="vue.js"></script>
<script>
    const app = new Vue({
        el: "#app", // 挂载要管理的元素
        data: {     // 定义数据
            msg: "Hello World",
            movies: ['a', 'b', 'c'],
            counter: 0
        },
        methods: {	// 定义方法
            AddCounter() {
                this.counter++	// 通过this引用data
            }
        }
    })
</script>
```

### Vue 中的 MVVM

#### View层

- 视图层
- 在前端中看，通常就是 DOM 层
- 主要作用就是给用户展示各种信息

#### Model层

-  数据层
- 数据可能是我们固定的死数据，更多是来自服务器，从网络中请求来的数据

#### VueModel层

- 视图模型层
- 视图模型层是 View 和 Model 沟通的桥梁
- 他实现了 Data Binding，即数据绑定，将 Model 的改变实时的反映到 View 中
- 他实现了 DOM Listener，即 DOM 监听，当 DOM 发生一些事件(点击、滚动等)时，可以监听到，并在需要的情况下改变对应的 Data

### Vue 中的 options

在创建 Vue 实例的时候，会传入一个对象 options

这个 options 可以包含：[Vue 实例选项]( [https://cn.vuejs.org/v2/api/#%E9%80%89%E9%A1%B9-%E6%95%B0%E6%8D%AE](https://cn.vuejs.org/v2/api/#选项-数据) )

上面的例子只用到以下三个 options

- el：
  - 类型：string|HTMLElement
  - 作用：Vue 实例管理哪一个 DOM
- data：
  - 类型：Object|Function(在组件中必须是一个函数)
  - 作用：Vue 实例对应的数据对象
- methosd：
  - 类型：{ [key: string]: Function }
  - 作用：定义属于 Vue 的一些方法，可以在其他地方调用，也可以在指令中 

### 生命周期



















