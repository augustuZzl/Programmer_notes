### 插件配置

#### ESLint

一直听说 ESLint 的大名，也总想配置他，但一看网上的教程复杂多变，便退缩了，一直得过且过的写着代码。直到今天因为一个变量没有加修饰符 const，导致成了全局变量，我的天，在几个 js 文件中一通调试、查找，花费了太长的时间，要是再多几个文件，就要命了。不行不行，必须安排 ESLint。

1. 全局安装 EsLint：`npm install eslint -g`

2. VsCode 下载插件：ESLint

3. 在项目的终端输入 `eslint --init`，根据提示设置就行了

4. 设置完成后，会生成一个  .eslintrc.js  文件，其中的 "rules" 就是可以自己定义规则（不管也可以）

5. 在 VsCode 的 setting.json 中添加这个文件就 OK 了。

   ```json
   "eslint.options": {
       "configFile": "F:\\lk\\demo1\\.eslintrc.js"
   } 
   ```

