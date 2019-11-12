### 数据类型

#### 基本类型

* string：任意字符串
* number：任意数字
* boolean：true/false
* undefined：undefined
* null：null

#### 引用类型

* Object：任意对象
* Function：函数
* Array：数组

#### 类型判断

* typeof：返回数据类型的字符串表示
* instanceof：判断对象的类型
* ===

```javascript
var a;
console.log(a, typeof a);   // undefined "undefined"
console.log(a === undefined, typeof a === "undefined"); // true true
```

不能用 typeof 来判断 null :

```javascript
var a = null;
console.log(a === null);    // true
console.log(typeof a);  // "object"
```

```javascript
var b1 = {
    b2: [1, "2", console.log],
    b3: function(){
        console.log('b3');
    }
}

console.log(b1 instanceof Object);  // true
console.log(b1.b2 instanceof Array, b1.b2 instanceof Object);    // true true
console.log(b1.b3 instanceof Function, b1.b3 instanceof Object); // true true

console.log(typeof b1.b2);  // "object"
console.log(typeof b1.b2[2]);  // "function"
b1.b2[2](4);    // 调用这个方法，输出 4
console.log(typeof b1.b3);  // "function"
```

#### 资源

-  https://www.cnblogs.com/zhuyeshen/p/10997893.html 



