### Types

- 基本类型：当你使用基本类型的时候，可以直接操作他的值

  - string
  - number
  - boolean
  - null
  - undefined
  - symbol

  ```javascript
  const foo = 1;
  let bar = foo;
  
  bar = 9;
  
  console.log(foo, bar);	// 1, 9
  ```

  - Symbol cannot be faithfully polyfilled，所以在不能原生支持 Symbol 的环境中禁止使用

- 复杂类型：在使用一个复杂类型的时候，操作的是他的值的一个引用

  - object
  - array
  - function

  ```javascript
  const foo = [1, 2];
  const bar = foo;
  
  bar[0] = 9;
  console.log(foo[0], bar[0]);	// 9, 9
  ```

### References

- 所有的赋值都用 const，避免使用 var

  > 这保证了你不能改变你的初始值，否则可能会导致 bug 和难以理解的代码

  ```javascript
  // bad
  var a = 1;
  var b = 2;
  
  // good
  const a = 1;
  const b = 2;
  ```

- 如果一定要重新赋值，使用 let ，而不是 var

  > let 是块作用域，而 var 是函数作用域

  ```javascript
  // bad
  var count = 1;
  if(true){
      count += 1;
  }
  
  // good
  let count = 1;
  if(true){
      count += 1;
  }
  ```

- 注意：let 和 const 都是块作用域

  ```javascript
  // a 和 b 只在定义的块中起作用
  {
      let a = 1;
      const b = 1;
  }
  console.log(a);	// ReferenceError
  console.log(b);	// ReferenceError
  ```

### Objects

- 使用字面量创建对象

  ```javascript
  // bad
  const item = new Object();
  
  // good
  const item = {};
  ```

- 当创建一个带有动态属性名的对象时，用计算后的属性名

  > 这可以使你将定义的所有属性放在对象的一个地方

  ```javascript
  function getKey(k) {
      return `the key named ${k}`;
  }
  
  // bad
  const obj = {
      id: 5,
      name: 'shandong',
  };
  obj[getKey('enabled')] = true;
  
  // good，getKey('enables')是动态属性名
  const obj = {						// const obj = {
  	id: 5,							// 		id: 5,
      name: 'shandong',				//		name: 'shandong',
      [getKey('enabled')]: true,		//		the key named enables: true
  };									// }
  ```

- 对象方法简写

  ```javascript
  // bad
  const atom = {
      value: 1,
      
      addValue: function(value) {
          return atom.value + value;
      }
  }
  
  // good
  const atom = {
      value: 1,
      
      addValue(value) {
          return atom.value + value;
      }
  }
  ```

- 属性值简写

  ```javascript
  const name = 'zzl';
  
  // bad
  const obj = {
      name: name,
  };
  
  // good
  
  const obj = {
      name,
  }
  ```

- 将所有的简写放在最开始

  ```javascript
  const name = 'zzl';
  const age = 22;
  
  // bad
  const obj = {
      sex: 'man',
      name,
      address: 'shandong',
      age,
  }
  
  // good
  const obj = {
      name,
      age,
      sex: 'man',
      address: 'shandong',
  }
  ```

- 仅对那些无效的标识使用引号 `''`

  ```javascript
  //bad
  const bad = {
      'name': 'zzl',
      'age': 18,
      'data-len': 5,
  }
  
  // good
  const good = {
      name: 'zzl',
      age: 18,
      'data-len': 5,
  }
  ```

- 不要直接调用 Object.prototype 上的方法，如 hasOwnProperty，propertyIsEnumerable，isPrototypeOf。因为这些方法可能被屏蔽。

  ```javascript
  // bad
  console.log(object.hasOwnProperty(key));
  
  // good
  console.log(Object.prototype.hasOwnProperty.call(obbject, key));
  
  // best
  const has = Object.prototype.hasOwnProperty;	// 在模块内做一次缓存
  console.log(has.call(object, key));
  ```

- 对象浅拷贝时，推荐使用扩展运算符 `...`，而不是 Object.assign。获取对象指定的几个属性时，用对象的 rest 解构运算符 `...` 更好。

  ```javascript
  // vary bad(引用同一对象)
  const original = { a: 1, b: 2 };
  const copy = Object.assign(original, { c: 3 });
  delete copy.a;
  
  // bad(两个对象不同)
  const original = { a: 1, b: 2 };
  const copy = Object.assign({}, origianl, {c: 3 });
  
  // good es6扩展
  const original = { a: 1, b: 2 };
  // 浅拷贝
  const copy = { ...original, c: 3 };
  // rest 赋值运算符
  const {a, ...noA } = copy;	// { b: 2, c: 3 }
  ```

### Arrays











































