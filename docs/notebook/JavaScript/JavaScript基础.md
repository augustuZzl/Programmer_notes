### 数据类型

基本类型如下：

* String：字符串
* Number：数字
* Boolean：true/false
* Symbol：符号，ES2015(ES6) 新增 
* undefined：undefined
* null：null

引用类型如下：

* Object：对象
* Function：函数
* Array：数组
* Date：日期
* RegExp：正则表达式

#### 数字

JavaScript 不区分整数和浮点数，所有数值均用浮点数值来表示，那么就会出现如下所示精度问题：

```javascript
0.1 + 0.2 = 0.30000000000000004
```

内置函数  parseInt() 可以将字符串转化为整数，第二个参数表示字符串表示数字的进制：

```javascript
parseInt("123", 10); // 123
parseInt("010", 10); // 10
```

同理 parseFloat() 可以转换浮点数字符串，但只能转换十进制字符串。

parseInt() 这个函数对特定的非全数字字符串的解析也很棒：

```javascript
parseInt("a123");	// NaN
parseInt("123a");	// 123
```

还有一个函数 Number() 也可以用来转换字符串，却没有这个功能： parseInt("123a")；

```javascript
Number("123");	// 123
Number("123a");	// NaN
Number("a123");	// NaN
```

单元运算符 + 也可以起到 Number() 的功能：

```javascript
+ "123";	// 123
+ "123a";	// NaN
+ "a123";	// Nan
```

注意 NaN 也是一个 Number 类型，也可以参与运算：

```javascript
NaN + 5;	// NaN
```

可以使用内置函数 isNaN() 来判断一个变量是否是 NaN

```javascript
isNaN(NaN);	// true
```

JavaScript 还有两个特殊值： Infinity(正无穷) 和 -Infinity(负无穷)：

```javascript
1 / 0; //  Infinity
-1 / 0; // -Infinity
```

可以使用内置函数 isFinite() 来判断一个变量是否是有个有穷数：

```javascript
isFinite(1 / 0);	// false
isFinite(Infinity);	// false
isFinite(-Infinity);	// false
isFinite(NaN);	// false
```

#### 字符串

JavaScript 中的字符串是一串 Unicode 序列，是一串UTF-16编码单元的序列，每一个编码单元由一个 16 位二进制数表示。 

```javascript
"Hello".length;	// 5
```

字符串也是一种 JavaScript 对象：

```javascript
"Hello".charAt(0);	// "H"
"Hello World".replace("World", "you");	// "Hello you"
"Hello".toUpperCase();	// HELLO
```

#### 数组

创建数组的传统方法是：

```javascript
var array = new Array();
array[0] = 1;
array[1] = "2";
array.length = 2;
```

使用数组字面量法创建更方便：

```javascript
var array = [1, "2"];
array[5] = "zzl";	// 空出的部分为 undefind 
array.length;	// 6，数组的长度是比数组最大索引值多一的数，而不是其中元素的个数
```

注意在遍历数组时可以使用 for...of :

```javascript
for(const a of array){
    // a 为数组内元素
}
```

但是 for...in 循环遍历数组得到的确实数组索引，所以不推荐使用它来比那里数组。

还有一个 fforEach() 方法来遍历数组：

```javascript
["zzl", "augustu"].forEach(function(value, index, array){
    // value or array[index]
})
```

数组的常用方法：

```javascript
array.toString();	// 返回形式："zzl, augustu"
array.concat(arr[, arr[, ...);		// 返回连接后的新数组，原数组不变
array.pop();	// 删除并返回原数组中的最后一个元素
array.push(item, ...);	//添加到原数组后面
array.reverse();	// 翻转原数组
array.shift();	// 删除并返回原数组的第一个元素
array.unshift(item, ...);	// 插入数组头部
array.slice(start, end);	// 返回这个范围的子数组
array.splice(start, deleteCount);	// 删除原数组 start 位置开始的 deleteNum 个元素
array.sort([fun]);	// 根据可选的排序函数排序，如果没有函数，则默认按照字符顺序比较（注意!!!）


```



#### 其他类型

null 表示一个空值，必须用 null 关键字才能访问。undefind 表示未定义类型的对象，即允许声明一个变量，但不对其赋值。

布尔类型只有两个值，true 和 false，JavaScript 在需要一个布尔变量时(比如在 if 条件中)会隐式的将某个变量转为布尔类型：

1. false、0、""(空字符串)、NaN、null、undefined 都会被转为 false
2. 其他的值都会被转为 true

当然也可以使用 Boolean 进行显示转换，不过一般没有必要：

```javascript
Boolean("");	// false
Boolean(123);	// true
```

#### 类型判断

* typeof：返回数据类型的字符串表示
* instanceof：判断对象的类型
* ===
* == ：会进行类型自动转换再比较

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

-  https://www.cnblogs.com/zhuyeshen/p/10997893.html 

### 变量

在 JavaScript 中声明一个变量有三个关键字：var、let 和 const

let 声明一个块级作用域的本地变量：

```javascript
// i 在这里不可见 

for (let i = 0; i < 5; i++) {
	// i 只在这里起作用
}

// i 在这里不可见 
```

const 声明一个不可变的常量：

```javascript
const PI = 3.14;
PI = 1;		// Uncaught TypeError: Assignment to constant variable.
```

var 是最常见的声明变量的关键字，没有其他两个关键字的种种限制，var 在块中(例如 if 、for 等)是没有作用域的，在整个函数中都可见，所以要使用块级作用域时用 let 或 const：

```javascript
// i 在这里可见 

for (var i = 0; i < 5; i++) {
	
}

// i 在这里可见 
```

### 控制结构

JavaScript 的空值结构与 Java 类似，比如 if 、else、while 等，但有两种独特的 for 循环：

for...of

```javascript
for (let value of array) {
  // do something with value
}
```

for...in

```javascript
for (let property in object) {
  // do something with object property
}
```

### 对象

有两种方法可以创建一个空对象：

```javascript
var obj = new Object();
var obj = {};	// 对象字面量法，JSON 格式，优先使用
```

可以在对象实例中定义一个对象：

```javascript
var obj = {
    name: "zzl",
    details: {
        color: "yello",
        size: 18
    }
}
```

对象的属性可以通过链式来访问：

```javascript
obj.details.color;	// "yello"
obj["details"]["size"];	// 18
```

下面创建一个对象原型 Person 以及这个原型的实例 you

```javascript
// 对象原型，相当于 Java 中的类，为区别于函数，首字母大写
function Person(name, age){
    this.name = name;
    this.age = age;
}
// 实例
var you = new Person("zzl", 23);

// 访问属性
you.name;	// "zzl"
```

