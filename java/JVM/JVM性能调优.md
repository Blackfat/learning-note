#### jps

> **jps主要用来输出JVM中进程状态信息**   

jps -l 输出完全的包名，应用主类名，jar的完全路径名 

jps -v 显示JVM参数

#### jstat

> JVM运行了多久

ps -p pid -o etime 

> jstat主要用来输出JVM统计监测信息

jstat -gcutil pid  3s 60 

分析进程的gc情况,每隔3秒打印一次，打印60次停止

```bash
S0     S1     E      O      M     CCS    YGC     YGCT    FGC    FGCT     GCT
  0.00  36.51  15.31   3.54  96.57  94.81      3    0.306     0    0.000    0.306
  0.00  36.51  15.31   3.54  96.57  94.81      3    0.306     0    0.000    0.306
  0.00  36.51  15.31   3.54  96.57  94.81      3    0.306     0    0.000    0.306
  0.00  36.51  15.31   3.54  96.57  94.81      3    0.306     0    0.000    0.306
```

```java
S0：幸存1区当前使用比例
S1：幸存2区当前使用比例
E：伊甸园区使用比例
O：老年代使用比例
M：元数据区使用比例
CCS：压缩使用比例
YGC：年轻代垃圾回收次数
FGC：老年代垃圾回收次数
FGCT：老年代垃圾回收消耗时间
GCT：垃圾回收消耗总时间
```

jstat -gc pid 垃圾回收统计

```
 S0C    S1C    S0U    S1U      EC       EU        OC         OU       MC     MU    CCSC   CCSU   YGC     YGCT    FGC    FGCT     GCT
81920.0 81920.0  0.0   29909.7 655360.0 117718.8 1277952.0   45176.2   78720.0 76023.1 10112.0 9586.8      3    0.306   0      0.000    0.306
81920.0 81920.0  0.0   29909.7 655360.0 117718.8 1277952.0   45176.2   78720.0 76023.1 10112.0 9586.8      3    0.306   0      0.000    0.306
81920.0 81920.0  0.0   29909.7 655360.0 117718.8 1277952.0   45176.2   78720.0 76023.1 10112.0 9586.8      3    0.306   0      0.000    0.306
```

```java
- S0C：第一个幸存区的大小
- S1C：第二个幸存区的大小
- S0U：第一个幸存区的使用大小
- S1U：第二个幸存区的使用大小
- EC：伊甸园区的大小
- EU：伊甸园区的使用大小
- OC：老年代大小
- OU：老年代使用大小
- MC：方法区大小
- MU：方法区使用大小
- CCSC:压缩类空间大小
- CCSU:压缩类空间使用大小
- YGC：年轻代垃圾回收次数
- YGCT：年轻代垃圾回收消耗时间
- FGC：老年代垃圾回收次数
- FGCT：老年代垃圾回收消耗时间
- GCT：垃圾回收消耗总时间
```



#### jmap

jmap -heap pid

>查看整个jvm的内存情况

jmap -histo pid | head -n10

>jvm堆中对象的前10的详细占用情况

jmap -dump:format=b,file=heap.hprof pid

> 导出整个JVM 中内存信息，可以利用其它工具打开dump文件分析 

#### JVM参数
-Xms:指定JVM初始化内存
-Xmx:指定JVM堆的最大内存，在JVM启动以后，会分配-Xmx参数指定大小的内存给JVM，但是不一定全部使用，JVM会根据-Xms参数来调节真正用于JVM的内存
-Xmn：参数设置了新生代的大小；老年代等于-Xmx减去-Xmn
-XX:PermSize :设置永久代初始大小
-XX:MaxPermSize :设置永久代最大大小
-XX:+UseParallelGC:       使用并行垃圾收集
-XX:+UseConcMarkSweepGC: 使用并发标志扫描收集 (Introduced in 1.4.1)
-XX:+UseSerialGC : 使用串行垃圾收集 (Introduced in 5.0.)



#### GC优化的原则

- 多数的Java应用不需要在服务器上进行GC优化；
- 多数导致GC问题的Java应用，都不是因为我们参数设置错误，而是代码问题；
- 在应用上线之前，先考虑将机器的JVM参数设置到最优（最适合）；
- 减少创建对象的数量；
- 减少使用全局变量和大对象；
- GC优化是到最后不得已才采用的手段；
- 在实际使用中，分析GC情况优化代码比优化GC参数要多得多



#### GC优化的目的

- 减少使用全局变量和大对象；
- 调整新生代的大小到最合适；
- 设置老年代的大小为最合适；
- 选择合适的GC收集器；



#### GC优化的指标

- `Minor GC`执行时间不到`50ms`，`Minor GC`执行不频繁，约`10`秒一次；
- `Full GC`执行时间不到`1s`，`Full GC`执行频率不算频繁，不低于`10`分钟1次；

