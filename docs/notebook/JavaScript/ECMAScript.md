### ECMA 简介

- 是由 ECMA 组织制定和发布的脚本语言规范
- JavaScript 是 ECMA 的实现，包含三个部分
  - ECMAScript
  - 浏览器端扩展：BOM(浏览器对象模型)和DOM(文档对象模型)
  - 服务器端扩展：Node.js
- 版本
  - ES5：09 年发布
  - ES6(ES2015)：15 年发布
  - ES7(ES2017)：16 年发布

### ES5

#### 严格模式

严格模式(strict mode)是为了消除 JavaScript 语法的一些不合理、不严谨之处，减少一些怪异行为的出现，为代码的安全运行保驾护航，同时为新版本做好铺垫

##### 使用

在**全局**(作用于全局)或**函数**(只作用于当前函数)的第一条语句定义：'use strict';

```javascript
'use strict';	// 全局开启严格模式

// code......
```

##### 语法和行为要求

- 必须使用 var 声明变量

  ```javascript
  'use strict';
  username = 'zzl';
  console.log(username);	// Uncaught ReferenceError: username is not defined
  ```

- 禁止自定义函数中的 this 指向 window

  ```javascript
  'use strict';
  function Person(name, age){
      this.name = name;
      this.age = age;
  }
  // 本想创建个对象，但忘了 new，那么就会自调用，this 指向了 window
  Person('zzl', 20);	// Uncaught TypeError: Cannot set property 'name' of undefined
  ```

- 创建 eval 作用域

  ```javascript
  // 如果不开启严格模式
  var str = 'zzl';
  eval("var str = 'hhh'; console.log(str)");	// hhh
  console.log(str);	// hhh
  // eval 没有作用域，作用于了全局，污染了同名变量，这就可能导致严重的问题。
  
  // 开启 'use strict'; 后，就会被限制在 eval 内了
  ```

- 对象不能有重名的属性

#### JSON

1. JSON.stringify(obj/arr)：js 对象(数组)转为 json 对象(数组)

2. JSON.parse(json)：json 对象(数组)转为 js 对象(数组)

   ```javascript
   var obj = {username: 'zzl'};
   obj = JSON.stringify(obj);	// string
   obj = JSON.parse(obj);	// object
   ```

#### Object

1. Object.create(prototype, [descriptors])

   - 以指定对象为原型创建新对象
   - 为新对象指定新的属性，并对属性进行描述
     - value：指定值
     - writable：当前属性是否可修改，默认 false
     - configurable：当前属性是否可删除，默认 false
     - enumerable：当前属性是否可 for in 枚举，默认 false

   ```javascript
   var obj = {username: 'zzl'};
   var creatObj = Object.create(obj, {
      sex: {
          value: '男',
          writable: true,
          configurable: true
      }
   });
   ```

   

2. Object.defineProperties(object, descriptors)

   - 为指定对象定义扩展多个属性
     - get：用来获取当前属性值的回调函数
     - set：修改当前属性值的触发回调函数，并且实参即为修改后的值
   - 存取器属性：setter，getter 一个用来存值，一个用来取值

   ```javascript
   var obj = {username: 'zzl'}
   Object.defineProperties(obj, {
       sex: {
           get: function(){
               return "男"
           },
           // 只有声明 set 才能修改属性值
           set: function(data){
               console.log("你修改了属性值" + data);
           }
       }
   })
   ```

   对象本身也有 get set 方法









































