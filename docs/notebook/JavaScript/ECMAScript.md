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

1. JSON.stringify(obj/arr)：js 对象/数组转为字符串

2. JSON.parse(json)：字符串转为 js 对象/数组

   ```javascript
   var obj = {username: 'zzl'};
   var jsonStr = JSON.stringify(obj);
   obj = JSON.parse(jsonStr);
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
   
   // obj 作为新对象的原型
   var cloneObj1 = Object.create(obj);
   console.log(cloneObj1.__proto__);	// {username: 'zzl'}
   
   // 为新对象添加自己的属性 sex、可修改、删除
   var creatObj2 = Object.create(obj, {
      sex: {
          value: '男',
          writable: true,
          configurable: true
      }
   });
   console.log(creatObj2);	// {sex: '男'}
   creatObj2.sex = '女';
   delete createObj2.sex;
   ```

   

2. Object.defineProperties(object, descriptors)

   - 为指定对象定义扩展多个属性
     - get：用来获取当前属性值的回调函数
     - set：修改当前属性值的回调函数，并且实参即为修改后的值
   - 存取器属性：setter，getter 一个用来存值，一个用来取值

   ```javascript
   var obj = { username: 'zzl', sex: 'super man' };
   Object.defineProperties(obj, {
       desc: {
           // 惰性求值，当打印对象时，desc 为 undefined，只有调用 desc 时才返回
           get: function () {
               return "I am " + this.username + ", i am " + this.sex;
           },
           set: function(data) {
               this.sex = data;
           }
       }
   })
   console.log(obj.desc);  // I am zzl, i am super man
obj.desc = "super star"; 
   console.log(obj.desc);  // I am zzl, i am super star
   ```
   
   js 对象本身也有两个方法
   
   - get propertyName() {}
   - set propertyName() {}
   
   ```javascript
   var obj = {
       name: 'zzl',
       get userName(){
           return this.name
       },
       set userName(){
           this.name = "augustu"
       }
   }
   ```

#### Array

1. Array.prototype.indexof(value)：值在数组中的第一个下标
2. Array.prototype.lastIndexOf(value)：值在数组中的最后一个下标
3. Array.prototype.forEach(function(item, index){})：遍历数组
4. Array.prototype.map(function(item, index){})：遍历数组，返回新的数组，返回加工后的值
5. Array.prototype.filter(function(item, index){})：遍历过滤出一个新的子数组，返回条件为 true 的值

```javascript
var arr = [3, 5, 2, 4, 3, 4];
arr.indexOf(4);	// 3
arr.lastIndexOf(4);	// 5
arr.forEach(function(item, index){
    // ...
})
var arr1 = arr.map(function(item, index){
    return item + 10;
})	// [13, 15, 12, 14, 13, 14]
var arr2 = arr.filter(function(item, index){
    return item > 3;
})	// [5, 4, 4]
```

#### Function

Function.prototype.bind(obj)：将函数的 this 绑定为 obj，并将函数返回

```javascript
var obj = {username: 'zzl'};
function foo(data){
    console.log(this, data);
}
foo();	// Window undefined
// 参数直接写
foo.call(obj, 'man');	// Object "man"
// 参数要放在数组中
foo.apply(obj, ['man']);	// Object "man"

// bind 也可以完成这个功能，但只是返回绑定后的函数，还需调用执行
foo.bind(obj, 'man')();

// 应用
setTimeout(function(){
    console.log(this);
}.bind(obj), 1000);
```

### ES6

#### let

- 定义一个变量
- 在块作用域内有效
- 不能重复声明
- 不会**预处理**，不存在变量提升

```html
<button>按钮1</button>
<button>按钮2</button>
<button>按钮3</button>

<script>
	let btns = document.querySelector("button");
    // 不管哪个按钮点击，都 alert(2)
    for(var i = 0; i < btns.length; i++){
        btns[i].onclick = function(){
            alert(i);
        }
    }
    // 使用 let，各自 alert 0,1,2
    for(let i = 0; i < btns.length; i++){
        btns[i].onclick = function(){
            alert(i);
        }
    } 
</script>
```





































