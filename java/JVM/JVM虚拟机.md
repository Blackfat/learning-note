#### 虚拟机中的垃圾回收算法

新生代的垃圾回收器：Serial，Parallel Scavenge 和 Parallel New。这三个采用的都是标记 - 复制算法。其中，Serial 是一个单线程的，Parallel New 可以看成 Serial 的多线程版本。Parallel Scavenge 和 Parallel New
类似，但更加注重吞吐率。此外，Parallel Scavenge 不能与 CMS 一起使用。

老年的垃圾回收器：Serial Old 和 Parallel Old，以及 CMS。Serial Old 和 Parallel Old 都是标记 - 压缩算法。同样，前者是单线程的，而后者可以看成前者的多线程版本。CMS 采用的是标记 - 清除算法，并且是并发的。除了少数几个操作需要 Stop-the-world 之外，它可以在应用程序运行过程中进行垃圾回收。在并发收集失败的情况下，Java 虚拟机会使用其他两个压缩型垃圾回收器进行一次垃圾回收。

>堆是共享的，所有的线程都会在堆上分配内存，那么jvm是如何保证内存分配是线程安全的？答案是，每
>个线程都会有TLAB(thread local allocation buffer)，这个是线程私有的。首先会在TLAB上分配，如果
>TLAB不够用，就需要通过加锁的方式，申请TLAB。

Minor GC 的另外一个好处是不用对整个堆进行垃圾回收,但可能存在老年代对象可能引用新生代的对象。这个时候理论上是需要扫描老年代的对象，但jvm做了优化。在进行 Minor GC 的时候，可以不用扫描整个老年代，而是在卡表中寻找脏卡，并将脏卡中的对象加入到 Minor GC 的 GC Roots 里。当完成所有脏卡的扫描之后，Java 虚拟机便会将所有脏卡的标识位清零。

>HotSpot 给出的解决方案是一项叫做卡表（Card Table）的技术。该技术将整个堆划分为一个个大小为 512 字节的卡，并且维护一个卡表，用来存储每张卡的一个标识位。这个标识位代表对应的卡是否可能存有指向新生代对象的引用。如果可能存在，那么我们就认为这张卡是脏的。