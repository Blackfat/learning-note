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

S0  — Heap上的 Survivor space 0 区已使用空间的百分比
S1  — Heap上的 Survivor space 1 区已使用空间的百分比
E   — Heap上的 Eden space 区已使用空间的百分比
O   — Heap上的 Old space 区已使用空间的百分比
P   — Perm space 区已使用空间的百分比
YGC — 从应用程序启动到采样时发生 Young GC 的次数
YGCT– 从应用程序启动到采样时 Young GC 所用的时间(单位秒)
FGC — 从应用程序启动到采样时发生 Full GC 的次数
FGCT– 从应用程序启动到采样时 Full GC 所用的时间(单位秒)
GCT — 从应用程序启动到采样时用于垃圾回收的总时间(单位秒)

#### jmap

jmap -heap pid

>查看整个jvm的内存情况

jmap -histo pid

>jvm堆中对象的详细占用情况

jmap -dump:format=b,file=heap.bin pid

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