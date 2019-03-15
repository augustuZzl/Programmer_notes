### 了解 Stream

Stream 是 Java 8 中处理集合的关键抽象概念，可以执行非常复杂的查找、过滤和映射数据操作。简单来说，他提供了一种高效且易于使用的数据处理方式。

流（Stream） 是数据渠道，用于操作数据源（集合、数组等）生成的元素序列。

1. Stream 不会存储元素
2. Stream 不会改变原对象，他会返回一个持有结果的新 Stream
3. Stream 是延迟执行的，意味着他会等到需要结果时才运行

### Stream 创建

1. 通过 Collection 系列集合提供的 stream(串行流) 或 parallelStream(并行流)

   ```java
   List<String> list = new ArrayList<>();
   Stream<String> stream = list.stream();
   ```

2. 通过 Arrays 的静态方法获取数组流

   ```java
   Employee[] emps = new Employee[10];
   Stream<Employee> stream = Arrays.stream(emps);
   ```

3. 通过 Stream 的静态方法 of()

   ```java
   Stream<String> stream = Stream.of("a","b","c","d");
   ```

4. 创建无限流

   ```java
   // 迭代方式
   Stream<Integer> stream = Stream.iterate(0, (x) -> x + 2);
   ```

   第一个参数是种子，表示从这个数开始

   第二个参数是 `Function<T, R>` 的实现，相当于 `Function<T, T>`

   ```java
   // 生成方式
   Stream<Double> stream = Stream.generate(() -> Math.random());
   ```

   参数是供给型接口 `Supplier<T>`

### Stream 中间操作

> 多个中间操作可以连接成一个流水线，但代码不会执行，返回的还是 stream 。只有在终止操作时才一次性全部处理，成为“惰性求值”，也叫延迟执行。

1. 筛选与切片

   - filter：接受 Lambda，从流中排除某些元素，参数为断言型接口 `Predicate<T>`

     ```java
     Stream<Employee> stream = emps.stream().filter((e) -> e.getAge()>35);
     ```

   - limit：截断流，使其元素不超过指定数量，即取前 n 个元素，即当取出 n 个后，后面就不需要进行了。

     ```java
     Stream<Employee> stream = emps.stream().limit(2);
     ```

   - skip(n)：跳过元素，返回一个扔掉了前 n 个元素的流，即取出除前 n 个元素之外的元素。与 limit(n) 互补

   - distinct：筛选，通过流所生成元素的 hashcode() 和 equals() 去除重复元素

     ```java
     Stream<Employee> stream = emps.stream().distinct();
     ```

     你会发现并不起作用，其实是你的 Employee 没有重写 hashcode() 和 equals() 方法，使用 IDE 快捷键自动生成即可。

2. 映射

   - map：接受 Lambda ,将元素转换成其他形式或提取信息。接受一个函数作为参数，该函数会被应用到每个元素上，并将其映射成一个新的元素。参数为 Function<T,R> 类型接口。

     ```java
     emps.stream().map((e) -> e.getName());
     emps.stream().map(Employee::getName);
     ```

     我们可以使用终止操作 forEach() 来遍历结果：

     ```java
     // 将所有名字打印出来
     emps.stream().map((e) -> e.getName()).forEach(System.out::println);
     ```

   - flatMap：接受一个函数作为参数，将流中的每个值都换成另一个流，然后把所有流连成一个流。

     我们先看这样一个例子

     ```java
     public class StreamAPI{
         public static stream<Character> filterStr(String str){
             List<Character> list = new ArrayList<>();
             for(Character cha : str.toCharArray()){
                 list.add(cha);
             }
             return list.stream();
         }
         @Test
         public void test{
             List<String> list = Arrays.asList("aaa","bbb");
             Stream<Stream<Character>> stream = list.stream()
                 									.map(StreamAPI::filterStr);
         }
     }
     ```

     我们看到返回值中是流中嵌套着流，这样操作就麻烦了。这时候我们使用 flatMap：

     ```java
     @Test
     public void test{
         List<String> list = Arrays.asList("aaa","bbb");
         Stream<Character> stream = list.stream()
             							.flatMap(StreamAPI::filterStr);
     }
     ```

     可以看到最终合并成一个流了。我们可以再举一个例子加深理解：

     我们知道 List 中有这么两个方法：add() 和 addAll()

     假设有两个 List：list1 和 list2，我想将 list2 中的数据全部加到 list1 中：

     ```java
     @Test
     public void test1(){
         List<Object> list1 = Arrays.asList("a","b","c");
         List<Object> list2 = Arrays.asList("d","e","f");
     	
         List<Object> arrayList1 = new ArrayList<>(list1);
         List<Object> arrayList2 = new ArrayList<>(list2);
     
         arrayList1.add(arrayList2);
         System.out.println(arrayList1);
     
         arrayList1.addAll(arrayList2);
         System.out.println(arrayList1);
         
     } 
     ```

     通过这个例子，我们就可以看出，map 类似 add() ，flatMap 类似 addAll()。这样就好理解了

     >调用 Arrays.asList() 生产的 List 的 add、remove 方法时报异常java.lang.UnsupportedOperationException，这是由 Arrays.asList() 返回的是 Arrays 的内部类ArrayList， 而不是 java.util.ArrayList。Arrays 的内部类 ArrayList 和 java.util.ArrayList 都是继承AbstractList，remove、add等方法 AbstractList中 是默认 throw UnsupportedOperationException 而且不作任何操作。java.util.ArrayList 重写了这些方法而 Arrays 的内部类 ArrayList 没有重写，所以会抛出异常。我们要将其转为 java.util.ArrayList。

3. 排序

   - sorted()：自然排序

     > 调用的是 Comparable，根据 Comparable 定义方式排序

     ```java
     List<String> list1 = Arrays.asList("a","b","c","aa");
     list1.stream().sorted().forEach(System.out::println);
     ```

   - sorted(Comparator)：定制排序，自己定义排序规则

     ```java
     emps.stream().sorted((e1, e2) -> {
         if(e1.getAge() == e2.getAge()){
             return e1.getName().compareTo(e2.getName());
         } else{
             return e1.getAge().compareTo(e2.getAge());
         }
     });
     ```

### Stream 终止操作

> 结束流，返回具体结果。

1. 查找与匹配
   - allMatch(Predicate)：是否匹配所有元素

     ```java
     boolean b = emps.stream().allMatch((e) -> e.getAge() == 38);//false
     ```

   - anyMatch(Predicate)：是否至少匹配一个元素

     ```java
     boolean b = emps.stream().anyMatch((e) -> e.getAge() == 38)//true 
     ```

   - noneMatch(Predicate)：是否没有匹配所有元素

   - findFirst：返回第一个元素

     ```java
     Optional<EMployee> op = emps.stream().findFirst();
     ```

     关于 Optional 我们以后学。主要用来避免空指针。

   - findAny：返回当前流中的任意元素

     ```java
     Optional<EMployee> op = emps.stream().findAny();
     ```

   - count：返回流中元素总个数

     ```java
     Long count = emps.stream().count;
     ```

   - max(Comparator)：返回流中最大值

     ```java
     Optional<EMployee> op = emps.stream()
         				.max((e1,e2) -> Integer.compare(e1.getAge(),e2.getAge()));
     ```

   - min：返回流中最小值

     ```java
     Option<Integer> op = emps.stream()
         					 .map(Employee::getAge).min(Integer::compare);
     ```

2. 规约

   reduce(T identity, BinaryOperator) / reduce(BinaryOperator)：将流中元素反复结合起来，得到一个值

   ```java
   List<Integer> list = Arrays.asList(1,2,3,4,5);
   Integer sum = list.stream().reduce(0, (x, y) -> x + y);
   ```

   identity 代表起始值

   BinaryOperator 实现的是 BiFunction(T, T, T)

   具体过程：先把 0 付给 x，从流中取出一个元素 1，执行 x + y 得到结果 1。再将结果 1 付给 x，从流中取出 2 付给 y ，在执行 x + y ，依次执行。

   ```java
   // 获取年龄总和
   List<Integer> list = Arrays.asList(1,2,3,4,5);
   Optional<Integer> op = list.stream().map(Employee::getAge).reduce(Integer::sum);
   ```

   因为没有初始值，所以返回结果可能为空，需要 Optional 接受。而上面那个方法有初始值，无论如何都不会为空。

   map 和 reduce 的连接通常称为 map-reduce 模式，Google 用其进行网络搜索而出名。 

3. 收集

   collect()：将流转化为其他形式，接受一个 Collector 接口的实现，用于给 Stream 中元素做汇总的方法

   - 分组

   ```java
   List<Employee> emps = Arrays.asList(
       new Employee("张三", 30, 6666.55),
       new Employee("李四", 30, 4444.55),
       new Employee("李四", 30, 3333.55),
       new Employee("田七", 24, 9999.55)
   );
   // 按照年龄分组
   Map<Integer, List<Employee>> map1 = emps.stream().collect(Collectors.groupingBy(Employee::getAge));
   
   // 先按年龄分组，再按姓名分组
   Map<Integer, Map<String, List<Employee>>> map2 = emps.stream().collect(Collectors.groupingBy(Employee::getAge, Collectors.groupingBy(Employee::getName)));
   ```

   结果：`System.out.println(map);`

   ```txt
   {
   	24=[Employee [name=田七, age=24, salary=9999.55]], 
   	30=[Employee [name=张三, age=30, salary=6666.55], 
   		Employee [name=李四, age=30, salary=4444.55], 
   		Employee [name=李四, age=30, salary=3333.55]]
   }
   ```

   结果：`System.out.println(map2);`

   ```txt
   {
   	24={田七=[Employee [name=田七, age=24, salary=9999.55]]}, 
   	30={李四=[Employee [name=李四, age=30, salary=4444.55], 
   			 Employee [name=李四, age=30, salary=3333.55]], 
           张三=[Employee [name=张三, age=30, salary=6666.55]]}
   }
   ```

   - 分区

   ```java
   Map<Boolean, List<Employee>> map3 = emps.stream().collect(Collectors.partitioningBy((e) -> e.getAge()> 26));
   ```

   结果：`System.out.println(map3);`

   ```txt
   {
   	false=[Employee [name=田七, age=24, salary=9999.55]], 
   	true=[Employee [name=张三, age=30, salary=6666.55], 
   		  Employee [name=李四, age=30, salary=4444.55], 
   		  Employee [name=李四, age=30, salary=3333.55]]
   }
   ```

   - 其他（方法还有许多，有兴趣可以自己尝试）

   ```java
   // 获取 age 的相关信息
   IntSummaryStatistics collect = emps.stream().collect(Collectors.summarizingInt(Employee::getAge));
   System.out.println(collect.getAverage());//28.5
   System.out.println(collect.getCount());//4
   System.out.println(collect.getSum());//114
   System.out.println(collect.getMax());//30
   ```

   ```java
   String str = emps.stream().map(Employee::getName).collect(Collectors.joining());
   System.out.println(str);//张三李四李四田七
   
   String str = emps.stream().map(Employee::getName).collect(Collectors.joining(","));
   System.out.println(str);//张三,李四,李四,田七
   
   String str = emps.stream().map(Employee::getName).collect(Collectors.joining(",","-","+"));
   System.out.println(str);//-张三,李四,李四,田七+
   ```

### 并行流

并行流就是把一个内容分成多个数据块，并用不同的线程分别处理每个数据块的流。

Stream API 可以声明性的通过 parallel() 和 sequential() 在并行流和顺序流之间切换。

例如我们计算 1 个亿的和所用时间：

```java
Instant start = Instant.now();
LongStream.rangeClosed(0, 100000000L).parallel().reduce(0, Long::sum);
Instant end = Instant.now();
System.out.println("并行花费："+Duration.between(start, end).toMillis());// 282
```

```java
Instant start = Instant.now();
long sum = 0;
for(long i = 0; i<= 100000000L; i++){
    sum += i;
}
Instant end = Instant.now();
System.out.println("普通花费"+Duration.between(start, end).toMillis());//71
```

```txt
并行花费：282
普通花费：71
```

我们发现并行流竟然比普通 for 循环还慢，其实这是因为并行流用的是 Fork/Join 框架。

Fork/Join 就是将一个大任务，进行拆分（fork）成若干个小任务，再将小任务运算的结果进行（join）汇总。

所以我们计算 1 亿个数时，进行拆分便花费了许多时间。

我们将其改为 500 亿，再看计算结果：（其实 for 循环运行挺快的）

```txt
并行花费：15124
普通花费：20218
```

