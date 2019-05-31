#### Trace跟踪参数

- **-XX:+printGC**：打印GC的简要信息
- **-XX:+PrintGCDetails**：打印GC详细信息
- **-XX:+PrintGCTimeStamps**：打印CG发生的时间戳
- **-Xloggc:log/gc.log**：指定GC log的位置，以文件输出
- **-XX:+PrintHeapAtGC**：每次一次GC后，都打印堆信息
- **-XX:+TraceClassLoading**：监控类的加载
- **-XX:+PrintClassHistogram**：打印类的信息

#### 堆参数

- **-Xmx**：最大堆 

  -Xmx3550m：设置 JVM 最大可用内存为3550m。

- **-Xms**：最小堆

  -Xms3550m：设置 JVM 促使内存为3550m。此值可以设置与 -Xmx 相同，以避免每次垃圾回收完成后 JVM 重新分配内存。

- **-Xmn**：设置新生代大小

- **-XX:NewRatio**：新生代（eden+2*s）和老年代的比值，4 表示 新生代:老年代=1:4，即年轻代占堆的1/5

- **-XX:SurvivorRatio**：设置两个Survivor区和eden的比，8表示 两个Survivor :eden=2:8，即一个Survivor占年轻代的1/10

- **-XX:+HeapDumpOnOutOfMemoryError**：OOM时导出堆到文件

- **-XX:+HeapDumpPath**：导出OOM的路径

  -XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=d:/a.dump

- **-XX:OnOutOfMemoryError**：在OOM时，执行一个脚本
- **总结：**

```text
根据实际事情调整新生代和幸存代的大小
官方推荐新生代占堆的3/8
幸存代占新生代的1/10
在OOM时，记得Dump出堆，确保可以排查现场问题
```

##### 问题1

**jre的运行时主要jar文件rt.jar都很大，这导致了用java做的桌面客户端程序很难发布绑定jre发布。这在很大程度上限制了java桌面软件的分发。可是，jre并不是在所有的用户计算机上都有安装，即使安装了，也未必我们期望的版本。因此，对jre做精简，减少体积是有必要的。请你给出一个方案，来说说如何给jre减肥，以方便我们的桌面程序绑定jre发布。并给出一个基本的实现。对这个实现的要求是：对于任意给定java程序A，应用你的方案和实现，可以从一个完整的jre中，抽取这个程序A的必要部分，从而实现最小体积的发布。在本题中，要求你详述你的方案，并提交你实现的代码。**

1. 使用 -XX:+TraceClassLoading 参数打印程序需要使用的类，例如我仿写的 Tomcat 这个应用，仅贴出部分信息，可以发现全是在 rt.jar 下的类。

```text
[Opened E:\ProgramFiles\Java\jdk1.8.0_144\jre\lib\rt.jar]
[Loaded java.lang.Object from E:\ProgramFiles\Java\jdk1.8.0_144\jre\lib\rt.jar]
[Loaded java.io.Serializable from E:\ProgramFiles\Java\jdk1.8.0_144\jre\lib\rt.jar]
[Loaded java.lang.Comparable from E:\ProgramFiles\Java\jdk1.8.0_144\jre\lib\rt.jar]
[Loaded java.lang.CharSequence from E:\ProgramFiles\Java\jdk1.8.0_144\jre\lib\rt.jar]
[Loaded java.lang.String from E:\ProgramFiles\Java\jdk1.8.0_144\jre\lib\rt.jar]
......
```

2. 解压 rt.jar，仅保留需要使用的类，重新压缩就可以了。

##### 问题2

**给出一个gc输出，要求给出一个你认为最可能的启动JVM参数，并说明为什么？**

```text
Heap
 def new generation   total 6464K, used 115K [0x34e80000, 0x35580000, 0x35580000)
  eden space 5760K,   2% used [0x34e80000, 0x34e9cd38, 0x35420000)
  from space 704K,   0% used [0x354d0000, 0x354d0000, 0x35580000)
  to   space 704K,   0% used [0x35420000, 0x35420000, 0x354d0000)
 tenured generation   total 18124K, used 8277K [0x35580000, 0x36733000, 0x37680000)
   the space 18124K,  45% used [0x35580000, 0x35d95758, 0x35d95800, 0x36733000)
 compacting perm gen  total 16384K, used 16383K [0x37680000, 0x38680000, 0x38680000)
   the space 16384K,  99% used [0x37680000, 0x3867ffc0, 0x38680000, 0x38680000)
    ro space 10240K,  44% used [0x38680000, 0x38af73f0, 0x38af7400, 0x39080000)
    rw space 12288K,  52% used [0x39080000, 0x396cdd28, 0x396cde00, 0x39c80000)
```

- 新生代：观察边界已经用满了，6464k = 7m
- 老年代：(0x37680000 - 0x35580000) / 1024 / 1024 = 34603008 / 1024 / 1024 = 33m
- 永久带：观察边界已经用满了，16384k = 16m

可能结果：-Xmx40m -Xmn7m -XX:MaxPermSize=16m

无法得出 -Xms 和 -XX:PermSize 这些初始化大小