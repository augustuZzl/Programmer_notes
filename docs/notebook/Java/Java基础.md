### 数据类型

#### 基本类型

| 关键字  | 位数 |            范围            |
| :-----: | :--: | :------------------------: |
| boolean |  ~   |       true 或 false        |
|  byte   |  1   |         - 128 ~ 127         |
|  char   |  2   | 16 位 Unicode（0 ~ 65535） |
|  short  |  2   |      - 32768 ~ 32767      |
|   int   |  4   |- $2^{31}$ ~ $2^{31}$ - 1|
|  long   |  8   | - $2^{63}$ ~ $2^{63}$ - 1 |
|  float  |  4   | 大约 ± 3.4 × $10^{38}$，7 位有效数字 |
| double  |  8   | 大约 ± 1.7 × $10^{308}$，15 位有效数字 |

**boolean**：The `boolean` data type has only two possible values: `true` and `false`. Use this data type for simple flags that track true/false conditions. This data type represents one bit of information, but its "size" isn't something that's precisely defined（布尔数据类型只有两个可能的值：真/假，但它的大小并不是精确定义的）

**Java Virtual Machine Specification**：Although the Java Virtual Machine defines a `boolean` type, it only provides very limited support for it. There are no Java Virtual Machine instructions solely dedicated to operations on `boolean` values. Instead, expressions in the Java programming language that operate on `boolean` values are compiled to use values of the Java Virtual Machine `int` data type.  In Oracle’s Java Virtual Machine implementation,boolean  arrays in the Java programming language are encoded as Java Virtual Machine `byte` arrays, using 8 bits per `boolean` element（虽然定义了boolean这种数据类型，但是只对它提供了非常有限的支持。在Java虚拟机中没有任何供boolean值专用的字节码指令，Java语言表达式所操作的boolean值，在编译之后都使用Java虚拟机中的int数据类型来代替，而boolean数组将会被编码成Java虚拟机的byte数组，每个元素boolean元素占8位）

- [Primitive Data Types](<https://docs.oracle.com/javase/tutorial/java/nutsandbolts/datatypes.html>)
- [Java Language and Virtual Machine Specifications](<https://docs.oracle.com/javase/specs/index.html>)

#### 包装类型

| 基本类型 | 包装类型  |
| :------: | :-------: |
| boolean  |  Boolean  |
|   byte   |   Byte    |
|   char   | Character |
|  short   |   Short   |
|   int    |  Integer  |
|   long   |   Long    |
|  float   |   Float   |
|  double  |  Double   |

将基本类型使用包装类包装起来，使其具有对象的性质

每个基本类型都有对应的包装类，基本数据类型基于值，包装类型基于引用，他们之间使用自动装箱、拆箱完成转换

```java
Integer i = 1;	// 自动装箱 调用了 Integer.valueof(1);
int j = i;		// 自动拆箱 调用了 i.intValue();
```

#### 缓存池

`new Integer(123)` 和 `Integer.valueOf(123)` 的区别在于：

- `new Integer(123)` 每次都会创建一个对象
- `Integer.valueOf(123)` 会使用缓存池中的对象，多次调用会取得同一对象的引用

```java
Integer x = new Integer(123);
Integer y = new Integer(123);
System.out.println(x == y);		// false
Integer X = Integer.valueOf(123);
Integer Y = Integer.valueOf(123);
System.out.println(X == Y);		// true
```

`Integer.valueOf()` 方法的实现原理：如果在缓存池中直接返回缓存池中的内容，否则新建对象

```java
public static Integer valueOf(int i) {
    if (i >= IntegerCache.low && i <= IntegerCache.high)
        return IntegerCache.cache[i + (-IntegerCache.low)];
    return new Integer(i);
}
```

`Java 8` 缓存池：

```java
private static class IntegerCache {
    static final int low = -128;
    static final int high;
    static final Integer cache[];

    static {
        // high value may be configured by property
        int h = 127;
        String integerCacheHighPropValue =
            sun.misc.VM.getSavedProperty("java.lang.Integer.IntegerCache.high");
        if (integerCacheHighPropValue != null) {
            try {
                int i = parseInt(integerCacheHighPropValue);
                i = Math.max(i, 127);
                // Maximum array size is Integer.MAX_VALUE
                h = Math.min(i, Integer.MAX_VALUE - (-low) -1);
            } catch( NumberFormatException nfe) {
                // If the property cannot be parsed into an int, ignore it.
            }
        }
        high = h;

        cache = new Integer[(high - low) + 1];
        int j = low;
        for(int k = 0; k < cache.length; k++)
            cache[k] = new Integer(j++);

        // range [-128, 127] must be interned (JLS7 5.1.7)
        assert IntegerCache.high >= 127;
    }

    private IntegerCache() {}
}
```

- Cache to support the object identity semantics of autoboxing for values between -128 and 127
- The size of the cache may be controlled by the `-XX:AutoBoxCacheMax=<size>` option，During VM initialization, java.lang.Integer.IntegerCache.high property may be set and saved in the private system properties in the sun.misc.VM class.

所以在 -128 ~ 127 之间的值都会直接引用缓存池中的对象

```java
Integer x = 123;
Integer y = 123;
System.out.println(x == y);		// true
Integer X = 200;
Integer Y = 200;
System.out.println(X == Y);		// false
```

其他基本类型对应的缓存池范围：

- boolean：true and false
- byte：all
- short：-128 ~ 127
- long：-128 ~ 127
- char：\u0000 ~ \u007F（从 0 到 127 个字符）
- float and double no cache

在使用这些基本类型对应的包装类型时，如果数值范围在缓冲池中，就可以直接使用缓冲池中的对象

#### 基本类型与包装类型的异同

1. **包装类型可以为 `null`，基本类型不可以**

   在数据库编程中，`POJO` 对象的属性必须用包装类型，因为数据库查询结果可能为 `null`，如果使用基本数据类型，在自动拆箱的时候就会抛出 `NullPointerException` 异常

2. **包装类可用于泛型，而基本类型不可以**

   与集合类合作只能使用包装类

   ```java
   // List<int> list = new ArrayList<>();	// Type argument cannot be of primitive type
   List<Integer> list = new ArrayList<>();
   ```

   ```java
   int[] array1 = new int[]{1, 2, 3};
   Integer[] array2 = new Integer[]{1, 2, 3};
   // List<Integer> list1 = Arrays.asList(array1); // Incompatible types
   List<Integer> list2 = Arrays.asList(array2);
   ```

3. **基本比包装类型更高效**

   基本类型可以直接存储数值，而包装类型则是堆中的一个引用，占用更大的空间

4. 留作补充

### String

#### 概念

`String` 被声明为 `final` ，因此不可以被继承。（基本类型的包装类也是这个样子）

在 `Java 8` 中，`String` 的内部结构：

```java
public final class String
    implements java.io.Serializable, Comparable<String>, CharSequence {
    /** The value is used for character storage. */
    private final char value[];

    /** Cache the hash code for the string */
    private int hash; // Default to 0
    
    ......
}
```

- `value` 被声明为 `final` ，这意味着 `value` 数组不能再引用其他数组，并且 `String` 内部也没有改变 `value` 数组的方法，所以 `String` 是不可变的

#### 不可变的好处

1. **可以缓存 `hash` 值**

   因为 `String` 的 `hash` 值经常被使用，例如使用 `String` 作为 `HashMap` 的 `key`。不可变保证 `hash` 不可变，不必重复计算。

2. **安全性**

   `String` 被大量作为参数使用，如果可变的话，那么就会导致很多错误。例如网络编程中的地址、集合类中的 `key` 等等。

3. **线程安全**

   `String` 不可变，因此可以在多个线程中安全的使用

4. **String Pool 的需要**

   如果一个 `String` 对象已被创建过了，那么就会直接从 `String Pool` 中得到引用，因此只有不可变，才能使用 `String Pool`

5. 留作补充

#### String Pool

`JVM` 为了提升性能和减少内存开销，避免字符串的重复创建，所维护的一块特殊的内存空间

字符串常量池保存着所有字符串字面量，这些字面量在编译时期就确定了。不仅如此，还可以使用 `String` 的 `intern()` 方法在运行时期将字符串添加进去。

当一个字符串调用 `intern()` 方法时，如果 `String Pool` 中已经存在一个字符串和该字符串值相等（使用 `equals()` 方法进行确定），那么就会返回 `String Pool` 中字符串的引用；否则，就会在 `String Pool` 中添加一个新的字符串，并返回这个新字符串的引用。

```java
String s1 = new String("aaa");
String s2 = new String("aaa");
System.out.println(s1 == s2);           // false
String s3 = s1.intern();
String s4 = s1.intern();
System.out.println(s3 == s4);           // true
```

如果是采用 "bbb" 这种字面量的形式创建字符串，会自动地将字符串放入 String Pool 中

```java
String s5 = "bbb";
String s6 = "bbb";
System.out.println(s5 == s6);  // true
```

在 `Java 7` 之前，`String Pool` 被放在运行时常量池中，它属于永久代。而在 `Java 7`，`String Pool` 被移到堆中。这是因为永久代的空间有限，在大量使用字符串的场景下会导致 `OutOfMemoryError` 错误。

![](<https://images2018.cnblogs.com/blog/1031933/201808/1031933-20180813222315549-299937191.png>)

#### new String("abc")

使用这种方式一共会创建两个字符串对象（前提是 `String Pool` 中还没有 `"abc"` 字符串对象）

- `"abc"` 属于字符串字面量，因此编译时期会在 `String Pool` 中创建一个字符串对象，指向这个 `"abc"` 字符串字面量
- 使用 `new` 会在对堆中创建一个字符串对象

以下是 `String` 构造函数的源码：在使用一个字符串对象作为构造函数的参数时，指向的是同一个 `value` 数组

```java
public String(String original) {
    this.value = original.value;
    this.hash = original.hash;
}
```

编写一个测试类，使用这种方法创建一个字符串对象

```java
public class Main {
    public static void main(String[] args) {
        String s = new String("abc");
    }
}
```

使用 `javap -v Main` 反编译：

```
Constant pool:
   ...
   #2 = Class              #16            // java/lang/String
   #3 = String             #17            // abc
   ...
  #16 = Utf8               java/lang/String
  #17 = Utf8               abc
   ...

public static void main(java.lang.String[]);
    descriptor: ([Ljava/lang/String;)V
    flags: ACC_PUBLIC, ACC_STATIC
    Code:
      stack=3, locals=2, args_size=1
         0: new           #2                  // class java/lang/String
         3: dup
         4: ldc           #3                  // String abc
         6: invokespecial #4                  // Method java/lang/String."<init>":(Ljava/lang/String;)V
         9: astore_1
	...
```

在 `Constant pool` 中 `#17` 存储字符串的字面量 `"abc"`，`#3` 是字符串常量池中的字符串对象，他指向 `#17` 这个字符串字面量。在 `main` 方法中，`0：`使用 `new`  在堆中创建了一个字符串对象，并使用 `ldc #3` 这个在常量池中的字符串作为构造参数。

#### StringBuilder & StringBuffer

1. 两者与 `String` 不同，都是可变的
2. 线程安全
   1. `String` 不可变，线程安全
   2. `StringBuilder` 线程不安全
   3. `StringBuffer` 内部使用 `synchronized` 进行同步，线程安全

#### 问题一

判断一下字符串对象是否相等：

```java
String s1 = "abc";
String s2 = "a" + "b" + "c";
String temp1 = "ab";
final String temp2 = "ab";
String s3 = temp1 + "c";
String s4 = temp2 + "c";

// result
System.out.println(s1 == s2);	// true
System.out.println(s1 == s3);	// false
System.out.println(s1 == s4);	// true
```

- 字符串字面量的拼接操作是在**编译期**就已经完成的了，所以 `s2` 直接作为 `abc` 放入常量池中了
- 字符串引用的拼接是在**运行期**完成的，他会在堆中创建字符串对象，所以 `s3` 引用的是堆中的对象
- `temp2` 被 `final` 修饰，那么在编译期就直接被替换为 `"ab"` 了，所以 `s4` 的情况就与 `s2` 一样了

### 运算

#### 参数传递

`Java` 的参数是以值传递的形式传入方法中，而不是引用传递，看下面例子：

`dog` 是一个指针，指向 `Dog` 对象的地址。在将其作为一个参数传入方法时，实际上是将对象的地址以值的方式传递到方法的参数 `dog` 中，此时两个 `dog` 指针，都指向同一个对象，所以打印结果不变。在方法内这个 `dog` 指针又指向了 `new Dog("B")` 这个对象，此时这两个指针指向不同的对象，互不影响。

```java
public class Main {
	
	static class Dog {
		
		String name;
		
		Dog(String name){
			this.name = name;
		}
		
	}
	
	private static void fun(Dog dog) {
		System.out.println(dog);	// Main$Dog@15db9742
		System.out.println(dog.name);	// A
		dog = new Dog("B");
		System.out.println(dog);	// Main$Dog@6d06d69c
		System.out.println(dog.name);	// B
	}
	
	public static void main(String[] args) {
		Dog dog = new Dog("A");
		System.out.println(dog);	// Main$Dog@15db9742
		fun(dog);
		System.out.println(dog);	// Main$Dog@15db9742
		System.out.println(dog.name);	// A
	}
	
}
```

![](pic\参数传递.png)

#### float 和 double

Java 不能隐式执行向下转型，因为这会使精度降低。

`1.1` 字面量属于 `double` 类型，不能直接将其赋值给 `float` 变量，因为这是向下转型

`float f = 1.1; // Incompatible types, Required:float,Found:double `

可以使用：`float f = 1.1f`

#### 隐式类型转换

先看下面的例子：

```java
short s = 1;
// s = s + 1;
```

这是因为运算的时候，s 与 1 运算得到的结果是 int 类型的，这时如果在赋值给 short 变量，这是不允许的。这时我们可以使用强制类型转换：`s = (short) (s + 1)`。

但是在执行某些特殊的运算时，他会自动进行向下转型：

```java
s += 1;
s++;
```

但他的本质还是：`i = (type of i) (i + j)`

#### switch

`switch` 支持类型：`char, byte, short, int, Character, Byte, Short, Integer, String, or an enum`

从 Java7 开始，可以在 `switch` 条件判断语句中使用 `String` 对象

```java
String s = "a";
switch (s) {
    case "a":
        System.out.println("aaa");
        break;
    case "b":
        System.out.println("bbb");
        break;
}
```

我们使用 `jad -sjava test.class` 将 class 文件反编译一下，看看他实际是怎么操作的：

```java
public class Test01 {

    public Test01() {}

    public static void main(String args[]) {
        String s = "a";
        String s1 = s;
        byte byte0 = -1;
        switch (s1.hashCode()) {
            case 97: // 'a'
                if(s1.equals("a"))
                    byte0 = 0;
                break;

            case 98: // 'b'
                if(s1.equals("b"))
                    byte0 = 1;
                break;
        }
        switch (byte0) {
            case 0: // '\0'
                System.out.println("aaa");
                break;

            case 1: // '\001'
                System.out.println("bbb");
                break;
        }
    }
}
```

说实话，我还是没有明白，为什么要添加 `s1`， `byte0` 这个变量。

















