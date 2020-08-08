### 安装 Vue 脚手架

### 通过脚手架创建项目

1. 在你想创建项目的地方打开命令行窗口，输入： vue create 项目名
2. 提示：Please pick a preset：，这是让你选择一个创建项目的方式，就像安装软件一样，你可以默认安装，也可以自定义安装。这里选择：Manually select feature 自定义方式
3. 出现一系列选项让你选择，例如 Babel、TypeScript 等等，通过空格选择。这里我们选择：Babel、Router、Linter/Formatter
4. Use history mode for router：n
5. Pick a linter/formatter config：ESLint + Standard config
6. Pick additional lint feature，选择 Lint on save，即每次保存都会校验格式
7. Where do you prefer placing config...，询问你是插件的配置保存在哪个位置，这里我选择是：In dedicated config files，即保存在各自的配置文件中
8. Save this as a preset for future projects，是否将这个配置保存起来，下次创建项目可以直接使用这个配置，再次我选择 n

创建完毕后，我使用 Vs Code 打开这个项目，另外推荐个 Vs Code 编写 Vue 程序的插件：Vetur

### 修改默认页面

#### src/App.vue

```vue
<template>
  <div id="app">
    <!-- 路由占位符 -->
    <router-view></router-view>
  </div>
</template>

<script>

export default {
  name: 'app'
}
</script>

<style>
</style>
```

#### src/router/index.js

```js
import Vue from 'vue'
import VueRouter from 'vue-router'

Vue.use(VueRouter)

const routes = [

]

const router = new VueRouter({
  routes
})

export default router
```

#### src/views

删除此文件夹

#### src/components/HelloWorld.vue

删除此文件

### 项目启动

输入：npm run serve

浏览器：http://localhost:8080

### 设置全局 CSS 样式

新建 CSS 文件：src/assets/css/global.css

```css
html, body, #app {
    height: 100%;
    padding: 0;
    margin: 0;
}
```

在 src/mian.js 中导入全局样式

```js
import './assets/css/global.css'
```

### 配置 Element-UI

element 特地为新版的 vue-cli 准备了相应的 Element 插件，非常方便使用

在 Vs Code 中使用快捷键：Ctrl + ` 即可打开终端

1. 输入：vue add element
2. 提示：How do you want to import Element?，选择 Import on demand 按需引入，避免引入太多组件导致项目太臃肿
3. Choose the locale you want to load，选择 zh-CN 中文就可了

### 配置 axios 库

1. 先输入：npm install axios --save 进行安装

2. 在 src/main.js 中进行配置，https://www.liulongbin.top:8888/api/private/v1/  这是教程发布者提供的后台数据接口

   ```js
   import axios from 'axios'
   // 配置请求的根路径
   axios.defaults.baseurl = 'https://www.liulongbin.top:8888/api/private/v1/'
   Vue.prototype.$http = axios
   ```


### 托管到 Github

1. 在 Github 上创建一个仓库
2. git add .
3. git commit -m "add"
4. git remote add origin https://github.com/augustuZzl/vue_element_shop.git
5. git push -u origin master
6. 做完第 5 步，你也许会出错：failed to push some refs to ... ，使用 git push -u origin master -f  即可解决

### 后台

yuanjudao.com

