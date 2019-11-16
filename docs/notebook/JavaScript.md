### 数据类型

#### 基本类型

* String：任意字符串
* Number：任意数字
* Boolean：true/false
* Symbol：符号，ES2015(ES6) 新增 
* undefined：undefined
* null：null

#### 引用类型

* Object：任意对象
* Function：函数
* Array：数组
* Date：日期
* RegExp：正则表达式

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

#### 资料

-  https://www.cnblogs.com/zhuyeshen/p/10997893.html 

### 数字

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

### 字符串

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