### Lambda 简介

Lambda 是一个匿名函数，可以将其理解为是一段可以传递的代码（将代码像数据一样进行传递）。我们可以写出更加简洁、更灵活的代码。作为一种更紧凑的代码风格，使 Java 的语言表达能力得到了提升。

### 简单对比示例

传统匿名内部类：

```java
Runnable r1 = new Runnable() {
    @Override
    public void run() {
        System.out.println("传统内部类实现");
    }
};
r1.run();
```

使用 Lambda 进行简化：

```java
Runnable r2 = () -> System.out.println("lambda 表达式实现");
r2.run();
```

运行结果：

```txt
传统内部类实现
lambda 表达式实现
```

可以看到代码确实减少了不小，可是有人会说，我用快捷键生成传统内部类也很快，确实，我们现在不熟悉 Lambda 表达式，所以对其并不敏感，他也确实没有给我们带来太大的兴奋感，下面我们结合案例进一步熟悉一下：

### 实例讲解

我们假设一个常用的员工场景，对其进行如下操作

1. 输出员工年龄大于30
2. 输出员工工资大于5000
3. .......

#### 实体类 Employee

```java
public class Employee{
    private String name;
    private Integer age;
    private Double salary;
    
    // get/set/constructor/toString 省略
}
```

#### 测试类 EmployeeDemo

```java
public class EmployeeDemo {
	
	List<Employee> emps = Arrays.asList(
		new Employee("张三", 29, 6666.55),
		new Employee("李四", 31, 4444.55),
		new Employee("王五", 24, 3333.55),
		new Employee("赵六", 33, 7777.55),
		new Employee("田七", 50, 9999.55)
	);
    
    //方法1
    
    //方法2
    
    //方法3
	
}
```

- 方法一：

很简单，我们只需提供两个方法，分别得到年龄>30和收入>5000 就行了

```java
// 年龄大于 30 的员工
public List<Employee> getEmpByAge(List<Employee> list){
    List<Employee> empList = new ArrayList<>();
    for (Employee emp : list) {
        if(emp.getAge() > 30){
            empList.add(emp);
        }
    }
    return empList;
}
// 收入大于 5000 的员工
public List<Employee> getEmpBySalary(List<Employee> list){
    List<Employee> empList = new ArrayList<>();
    for (Employee emp : list) {
        if(emp.getSalary() > 5000){
            empList.add(emp);
        }
    }
    return empList;
}
```

这是最平常的方法，可是这两个方法有大量的重复代码，所以我们可以想到提取公共代码来减少冗余。可这种方法好吗？不好，这种实现很简单，根本体现不出我们的技术，我们需要的是优雅(让别人看不懂)，且看方法二。

- 方法二：

我们使用高大上的设计模式来实现一下：

1. 设计一个接口来控制 Employee 过滤

   ```java
   public interface GetEmployee<T> {
   	// 是否符合获取的条件
   	public boolean get(T t);
   }
   ```

2. 按照年龄获取，即创建一个类来实现上面的接口

   ```java
   public class GetEmployeeByAge implements GetEmployee<Employee> {
   	@Override
   	public boolean get(Employee t) {
   		return t.getAge() > 30;
   	}
   }
   ```

3. 按照收入获取，同上

   ```java
   public class GetEmployeeBySalary implements GetEmployee<Employee> {
   	@Override
   	public boolean get(Employee t) {
   		return t.getSalary() > 5000;
   	}
   }
   ```

4. 我们只要创建一个方法就可以实现上面两个需求

   ```java
   public List<Employee> getEmp(List<Employee> list,GetEmployee<Employee> getEmp){
       List<Employee> empList = new ArrayList<>();
       for (Employee emp : list) {
           if(getEmp.get(emp)){
               empList.add(emp);
           }
       }
       return empList;
   }
   ```

5. 测试一下

   ```java
   @Test
   public void test1(){
       List<Employee> list = getEmp(emps,new GetEmployeeByAge());
       for (Employee employee : list) {
           System.out.println(employee);
       }
   }
   ```

可以看懂，我们将条件写在接口中，然后把接口的实现类当做参数传入。是不是越看越耐人寻味，越看越佩服，这就是设计模式的魅力，是前辈总结出来的精华。

可是我们又看到每实现一个需求，就要创建一个类，这样不是更麻烦吗？这样我们可以使用匿名内部类来实现，且看方法三。

在此记录一下使用 junit 报的错误：

导入 junit 包后，运行方法，报错 iniatialzationError ，原来是少了 hamcrest.core.jar 这个包，他们两个要在一起才行。其实我们根本不需要自己导包，在 eclipse 中，右键项目名---Build Path---Add Libraries---Junit 就行了。

- 方法三

  我们使用匿名内部类来改变方法二

  方法还是 **getEmp** 这个方法，只是我们不用再创建实现类了。直接在测试方法中实现就 ok 了：

  ```java
  @Test
  public void test2(){
      List<Employee> list = getEmp(emps,new GetEmployee<Employee>() {
          public boolean get(Employee t) {
              return t.getAge() > 30;
          }
      });
      for (Employee employee : list) {
          System.out.println(employee);
      }
  }
  ```

- 方法四

  其实方法四不算一个方法，只是用我们的主角 Lambda 来简化方法三。

  ```java
  @Test
  public void test2(){
      List<Employee> list = getEmp(emps,(t) -> t.getAge() > 30);
      list.forEach(System.out::println);
  }
  ```

  现在应该多少对 Lambda 有些理解了吧。连遍历都可以写成一句话！

#### 尝鲜

给你段代码，自己感受去吧！(涉及到 stream ，后面学)

假设我们只有上面 emps 这五个数据，其他的什么函数、接口等等都没有，来个狠得：

```java
@Test
public void test2(){
    emps.stream()
        .filter((e) -> e.getAge() > 30)
        .forEach(System.out::println);
}
```

什么感觉？再来个：

```java
emps.stream().map(Employee::getName).forEach(System.out::println);
```

这句话就是把所有员工姓名打印出来。

这还是我大 Java 吗？颠覆我对 Java 的认知啊！

### Lambda 基础语法

Java 8 中引入了一个操作符：`->`，即箭头操作符或 Lambda 操作符，将 Lambda 表达式分为了两部分：

左侧：Lambda 表达式参数列表

右侧：Lambda 表达式要执行的语句，即 Lambda 体

语法表较简单，主要包括以下几种：

1. 无参数，无返回值

   `() -> System.out.println("Hello Lambda!");`

2. 有一个参数，无返回值（参数的括号可以不写）

   `(x) -> System.out.println(x);`

   或  `x -> System.out.println(x);`

3. 多个参数，有返回值，并且 Lambda 体中有多条语句

   ```java
   Comparator<Integer> c = (x, y) -> {
       System.out.println("函数值接口");
       return Integer.compare(x, y);
   };
   ```

   若只有一条语句，则 return 和 大括号都可以不写：

   `Comparator<Integer> c = (x, y) -> Integer.compare(x, y);`

你有没有发现一个问题，为什么参数不用写类型呢？怎么越来越像 js 这种语言了呢！

其实是因为：JVM 编译器通过上下文推断出数据类型，即"类型推断"。当然，你也可以加上数据类型：

`(Integer x, Integer y) -> Integer.compare(x, y);`因为这更像 Java。那加好还是不加好呢？我认为人家好不容易给你实现可以不加，你却要加上，这不是辜负人家的好意嘛！

此时此刻我想做对一首：

上联：左右逢一括号省

下联：左侧推断类型省

横批：能省则省

### Lambda 表达式练习

使用 Collection.sort() 方法对 Employee 排序（先按年龄比，年龄相同按姓名比）

```java
@Test
public void test1(){
    Collections.sort(emps, (e1, e2) -> {
        if(e1.getAge() == e2.getAge()){
            return e1.getName().compareTo(e2.getName());
        }else{
            return e1.getAge().compareTo(e2.getAge());
        }
    });
    for (Employee employee : emps) {
        System.out.println(employee);
    }
}
```

 ### 函数式接口

有没有发现，Lambda 表达式适用于只有一个抽象方法的接口，这种接口成为“函数式接口”。我们可以使用注解：@FunctionalInterfce 来修饰。

我们在使用 Lambda 的时候，可能还要自己编写一个函数式接口，还要知道接口中函数的参数类型、返回值等信息。其实 Java 已经给我们内置了四大核心函数式接口供我们使用：

1. `Consumer<T>`：消费型接口

   `void accept(T t);`

   ```java
   @Test
   public void test(){
       happy(1000, (m) -> System.out.println("消费" + m + "元"));
   }
   public void happy(double money, Consumer<Double> con){
       con.accept(money);
   }
   ```

2. `Supplier<T>`：供给型接口

   `T get();`

3. `Function<T, R>`：函数型接口

   `R apply(T t);`

   ```java
   @Test
   public void test(){
       // 输出字符串长度
       strLong("augustu", (s) -> s.length());
   }
   public Long strLong(String str, Function<String, Long> fun){
       return fun.apply(str);
   }
   ```

4. `Predicate<T>`：断言型接口

   `boolean test(T t);`

除了上述的 4 种类型的接口外还有其他的一些接口供我们使用：

​	1）.`BiFunction<T, U, R> `

　　　　参数类型有2个，为T，U，返回值为R，其中方法为R apply(T t, U u)

　　2）.`UnaryOperator<T>`(Function子接口)

　　　　参数为T，对参数为T的对象进行一元操作，并返回T类型结果，其中方法为T apply(T t)

　　3）.`BinaryOperator<T>`(BiFunction子接口)

　　　　参数为T，对参数为T得对象进行二元操作，并返回T类型得结果，其中方法为T apply（T t1， T t2）

　　4）.`BiConsumcr<T, U> `

　　　　参数为T，U无返回值，其中方法为 void accept(T t, U u)

　　5）.`ToIntFunction<T>`、`ToLongFunction<T>`、`ToDoubleFunction<T>`

　　　　参数类型为T，返回值分别为int，long，double，分别计算int，long，double得函数。

　　6）.`IntFunction<R>`、`LongFunction<R>`、`DoubleFunction<R>`

　　　　参数分别为int，long，double，返回值为R。

​	等等。。。

### 方法引用与构造器引用

#### 方法引用

若 Lambda 体中的内容有方法已经实现了，便可以使用“方法引用”，通俗来说，方法引用是 Lambda 表达式的另一种表现形式。

1. 对象：：实例方法名

   ```java
   Consumer<String> con = (x) -> System.out.println(x);
   Consumer<String> con1 = System.out::println;
   ```

   这样写是有限制的：

   println() 的参数列表和返回值必须和接口中的抽象方法 accept() 的参数列表和返回值一样。这句话能明白吧。

   在来一个例子，比如我们之前的 Employee 类

   ```java
   Employee emp = new Employee();
   Supplier<String> su = () -> emp.getName();
   Supplier<String> su1 = emp::getName;
   ```

   现在应该有更深的了解了吧！

2. 类：：静态方法名

   这种就很容易理解了，和上面一样

   ```java
   Comparator<Integer> com = (x, y) -> Integer.compare(x, y);
   Comparator<Integer> com1 = Integer::compare;
   ```

3. 类：：实例方法名

   这个就不一样了

   ```java
   BiPredicate<String, String> bp = (x, y) -> x.equals(y);
   BiPredicate<String, String> bp2 = String::equals;
   ```

   若参数列表中第一个参数是实例方法的调用者，而第二个参数是实例方法的参数时，可以使用这种形式。

#### 构造器引用

ClassName：：new

假设我们 Employee 中如下构造函数

```java
public Employee(){
}
public Employee(int id){
    this.id = id;
}
```

我们可以进行如下操作：

```java
Supplier<Employee> su = () -> new Employee;
Supplier<Employee> su1 = Employee::new;

Function<Integer, Employee> fun = (x) -> new Employee(x);
Function<Integer, Employee> fun1 = Employee::new;
```

可以看到，他可以根据函数式接口自动匹配构造方法。

#### 数组引用

```java
Function<Integer, String> fun = (x) -> new String[x];
Function<Integer, String> fun2 = String[]::new;
```

> 也许你会觉得别扭，当用的多了就好了。