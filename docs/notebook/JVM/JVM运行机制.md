##### 参考资料

[Java8内存模型—永久代(PermGen)和元空间(Metaspace)](https://www.cnblogs.com/paddix/p/5309550.html)

##### 问题1

**写一个程序，让程序在运行之后，最终抛出由于Perm区溢出引起的OOM，给出运行的jdk版本，程序源码，运行参数，以及系统溢出后的截图、程序所依赖的jar包说明，并说明你的基本思路**

- JDK 8

JDK 7 中的 PermSize 和 MaxPermGen 在 JDK 8 中已经无效，已经不存在永久代，取而代之的是元空间，位于本地内存中，那么大小默认就只受本地内存的限制了。为了实现这个元空间溢出我真是费劲心血呀！踩了不少坑呀。

1. 运行参数：-XX:MetaspaceSize=4596k，-XX:MaxMetaspaceSize=4596k
2. 使用 jar 包：commons-io-2.6.jar，要解压
3. 先贴出代码，我修改了无数次才得到这么个能成功运行的代码，无奈啊！

```java
public static void main(String[] args){
    File jarPath = new File("D:\\commons-io-2.6");
    URL url = jarPath.toURI().toURL();
    URL[] urls = {url};
    
	File classPath = new File("D:\\commons-io-2.6\\org\\apache\\commons\\io");
    for (File file : classPath.listFiles()) {
        if(file.isFile()){
            ClassLoader loader = new URLClassLoader(urls);
            // 加载class要以包名开始，用点号分割
            loader.loadClass("org.apache.commons.io."+file.getName().split("\\.")[0]);
        }
    }
}
```

- File 的路径要写成：**"D:\\commons-io-2.6"**，我最初写成：**"D:/commons-io-2.6"**，根本无效。
- 加载 class 文件的时候必须不带后缀名。
- 为什么url要写父路径，不直接写class所在的目录呢？看下面例子就明白了：

```java
File classPath = new File("D:\\commons-io-2.6\\org\\apache\\commons\\io");
URL url = classPath.toURI().toURL();
URL[] urls = {url};

for (File file : classPath.listFiles()) {
    if(file.isFile()){
        ClassLoader loader = new URLClassLoader(urls);
        loader.loadClass(file.getName().split("\\.")[0]);
    }
}
```

```text
Exception in thread "main" java.lang.NoClassDefFoundError: ByteOrderMark (wrong name: org/apache/commons/io/ByteOrderMark)
```

- 由于加载类要从包名开始，而我直接进入到了class目录，导致包名的分隔符变为了：/，不是点了。

4. 运行结果：

![](https://ws1.sinaimg.cn/large/006DgVHEly1g3eo7i1bfjj30mh04574l.jpg)

##### 问题2

**你能想到有什么办法，可以让一个程序的函数调用层次变的更深。比如，你在一个递归调用中，发生了stack的溢出，你可以做哪些方面的尝试，使系统尽量不溢出？阐述你的观点和原因。**

- 设置 Xss 来增到栈空间，通常几百k

  1. Java 代码，一个循环就行了

  ```java
  public class OutMemory {
  
      private static int count = 0;
  
      private void call(){
          count++;
          call();
      }
  
      public static void main(String[] args) {
          OutMemory o = new OutMemory();
          try {
              o.call();
          } catch (Throwable e) {
              System.out.println("##################" + count);
              e.printStackTrace();
          }
      }
  }
  ```

  2. IDEA 中配置临时运行参数，第一次 256k，第二次 2560k

  ![](https://ws1.sinaimg.cn/large/006DgVHEly1g3elj8y3poj30ja0460sr.jpg)

  3. count结果分别是：3180 	和		63676

- 可以减小局部变量表，比如 少用double，long，减少参数个数，局部变量在使用的时候，注意作用域。
  在作用域开外的，局部变量，是可以被重用的，以此减少局部变量表的大小。

  局部变量表包含**参数**和**局部变量**，如果是实例方法还含有**this**的引用

##### 问题3

**我们再来模拟一下堆空间溢出**

由于字符串常量池在 JDK 8 中移到了堆中，所以我们可以不断创建字符串来导致堆空间溢出，当然也可以不断创建对象，例如数组等。

1. 运行参数：-Xms10m -Xmx10m
2. 代码：

```java
// 增加字符串常量池
public static void main(String[] args) {
    String base = "String";
    List<String> list = new ArrayList<String>();
    for(int i = 0; i < Integer.MAX_VALUE; i++){
        String str = base + base;
        list.add(str.intern());
        base = str;
    }
}
// 创建对象
public static void main(String[] args) {
    List<byte[]> list = new ArrayList<>();
    int i=0;
    while(true){
        list.add(new byte[5*1024*1024]);
    }
}
```

- 必须接受住 str.intern 的引用，否则 str.intern 会无效。

3. 运行结果

```text
Exception in thread "main" java.lang.OutOfMemoryError: Java heap space
```
