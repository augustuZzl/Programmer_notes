##### 问题1

**写一个程序，尽量产生STW现象。给出代码和启动JVM参数。并附上GC的log日志，标出停顿的时间。**

STW：stop-the-world，全局停顿，所有Java代码停止，native代码可以执行，但不能和JVM交互，多半由于GC引起：Dump线程、死锁检查、堆Dump

1. **运行参数**：-Xmx512m -Xms512m -XX:+UseSerialGC -XX:+PrintGCDetails -Xmn1m，可以看到新生代几乎没有空间，所以对象都到老年代里去了。

2. **时间打印代码**：每 100 毫秒打印一次，如果发生线程暂停，那么会延迟。

   ```java
   class PrintThread extends Thread {
       private final long startTime = System.currentTimeMillis();
       @Override
       public void run(){
           try {
               while(true) {
                   long t = System.currentTimeMillis() - startTime;
                   System.out.println("time:" + t);
                   Thread.sleep(100);
               }
           } catch(InterruptedException e) {
               e.printStackTrace();
           }
       }
   }
   ```

3. **消耗内存代码**：大于 450m 清理内存

   ```java
   public class StopTheWorld extends Thread {
       List<byte[]> list = new ArrayList<>();
       @Override
       public void run(){
           try {
               while(true) {
                   if(list.size()*512/1024/1024 > 450) {
                       System.out.println("要清理了" + list.size());
                       list.clear();
                       System.out.println("清理完毕");
                   }
                   for(int i = 0; i < 100; i++){
                       list.add(new byte[512]);
                   }
                   Thread.sleep(1);
               }
           } catch(Exception e) {
               e.printStackTrace();
           }
       }
   }
   ```

4. **GCDetails**：

##### 问题3

可复活：一旦所有引用被释放，就是可复活状态，因为在finalize()中可能复活该对象

不可触及：在finalize()后，可能会进入不可触及状态，不可触及的对象不可能复活，可以回收

