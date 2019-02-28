### TCP-IP

1.知道HTTP1.0和1.1的区别

HTTP 1.0规定浏览器与服务器只保持短暂的连接，浏览器的每次请求都需要与服务器建立一个TCP连接，服务器完成请求处理后立即断开TCP连接。
HTTP/1.1默认使用持久连接(然而,HTTP/1.1协议的客户机和服务器可以配置成使用非持久连接)，多个HTTP请求可以复用同一个TCP连接。

2.HTTP的长连接和短连接

 HTTP的长连接和短连接本质上说的TCP连接，TCP连接是一个双向通道，可以保持一段时间不关闭。

HTTP1.1长连接请求头中 默认有Connection: keep-alive ，如果不希望用长连接，在请求头中加入Connection: close。

3.TCP的三次握手和四次挥手

建立连接的时候， 服务器在LISTEN状态下，收到建立连接请求的SYN报文后，把ACK和SYN放在一个报文里发送给客户端。 而关闭连接时，服务器收到对方的FIN报文时，仅仅表示对方不再发送数据了但是还能接收数据，而自己也未必全部数据都发送给对方了，所以己方可以立即关闭，也可以发送一些数据给对方后，再发送FIN报文给对方来表示同意现在关闭连接，因此，己方ACK和FIN一般都会分开发送，从而导致多了一次 。

TCP三次握手的原因：信息对等和防止超时。

4.TCP 有哪些状态，相应状态的含义 

| 状态         | 含义                                                        |
| ------------ | ----------------------------------------------------------- |
| LISTEN       | 服务端 侦听来自远方TCP端口的连接请求                        |
| SYN-SENT     | 客户端在发送连接请求后等待匹配的连接请求                    |
| SYN-RECEIVED | 服务端 在收到和发送一个连接请求后等待对连接请求的确认       |
| ESTABLISHED  | 代表一个打开的连接，数据可以传送给用户                      |
| FIN-WAIT-1   | 客户端等待远程TCP的连接中断请求，或先前的连接中断请求的确认 |
| FIN-WAIT-2   | 客户端 从远程TCP等待连接中断请求                            |
| CLOSE-WAIT   | 等待从本地用户发来的连接中断请求                            |
| CLOSING      | 等待远程TCP对连接中断的确认                                 |
| LAST-ACK     | 服务端等待原来发向远程TCP的连接中断请求的确认               |
| TIME-WAIT    | 客户端等待足够的时间以确保远程TCP接收到连接中断请求的确认   |
| CLOSED       | 没有任何连接状态                                            |



### java基础

1.List，Set，Map的区别

List,Set都继承Collection接口，Map是键值对的集合，不允许key重复

List元素有放入顺序，元素可以重复；Set元素无放入顺序，元素不可重复，重复的元素会被覆盖掉

2.HashSet 是如何保证元素不重复的 

HashSet底层通过HashMap实现，首选元素的hashCode获取元素的存储位置，如果当前位置没有元素则加入，如果已经存在，这时会调用 equals 方法来检查 hashcode 相等的对象是否真的相同 ，如果相同则不能加入，如果不同则可以加入。

3.HashMap是线程安全的

HashMap不是线程安全的，在多线程同时操作的情况下，有可能出现循环链表，造成cpu使用率100%

4.HashMap 的扩容过程 

HashMap使用的是懒加载，构造完HashMap对象后，只要不进行put 方法插入元素之前，HashMap并不会去初始化或者扩容table。扩容过程中新table大小变为原来的2倍，接下来将原table中的元素重新hash后已到新table中

5.HashMap 1.7 与 1.8 的 区别 

扩容后元素的存储位置和计算方式不同：1.7中直接使用元素的（hash值 & length-1)(这里也可以解释为什么扩容大小一定要是2的倍数，可以最大程度的减少哈希碰撞)；1.8中根据hash新增参与运算的位是0还是1直接计算出扩容后的存储位置，如果新增参与运算的位是0，则扩容后的位置等于原始位置，如果新增参与运算的位是1，扩容后的位置等于原始位置加上扩容钱table的大小。 
1.7的时候使用的是数组+ 单链表的数据结构。但是在1.8及之后时，使用的是数组+链表+红黑树的数据结构（当链表的深度达到8的时候，也就是默认阈值，就会自动扩容把链表转成红黑树的数据结构来把时间复杂度从O（n）变成O（nlogN）提高了效率）

6.强引用，软引用，弱引用

只要某个对象有强引用与之关联，JVM必定不会回收这个对象 

对于软引用关联着的对象，只有在内存不足的时候JVM才会回收该对象 

当JVM进行垃圾回收时，无论内存是否充足，都会回收被弱引用关联的对象 

7.说说数组和链表这两种数据结构有什么区别？ 

对于数组来说，存储空间是连续的，所以在加载某个下标的时候可以把以后的几个下标元素也加载到cpu缓存中，这样执行速度会快于存储空间不连续的链表存储 

### java并发

1.synchronized 的实现原理以及锁优化 

当声明synchronized代码块时， 编译而成的字节码将包含 monitorenter 和 monitorexit 指令 。当执行 monitorenter 时，如果目标锁对象的计数器为 0，那么说明它没有被其他线程所持有。 在这个情况下，Java 虚拟机会将该锁对象的持有线程设置为当前线程，并且将其计数器加 1。 在目标锁对象的计数器不为 0 的情况下，如果锁对象的持有线程是当前线程，那么 Java 虚拟机 可以将其计数器加 1，否则需要等待，直至持有线程释放该锁。 锁一共有四种状态，无状态锁，偏向锁，轻量级锁，重量级锁，锁状态会随着锁竞争情况逐渐升级，锁状态可以升级但不能降级。

2.volatile的实现原理

编译器会在volatile读写前后插入一些内存屏障的指令，可以强制刷新处理器的写缓存(在碰到内存写操作时，处理器并不会等待该指令结束，而是直接开始下一指令，并且依赖于写缓 存将更改的数据同步至主内存（main memory）之中。 )。强制刷新写缓存，将使得当前线程写入volatile的值同步至主内存中，由于内存写操作同时会无效化其他处理器所持有的、指向同一内存地址的缓存行，因此可以认为 其他处理器能够立即见到该 volatile 字段的最新值。 

3.cas，cas的缺陷，如何解决

cas比较交换来鉴别线程是否出现冲突，出现冲突就重试当前操作直到没有冲突为止。 

缺点：

会出现ABA问题，可以通过加版本号或时间戳解决

自旋时间过长

4.synchronized 和 lock 有什么区别 

1.Lock是一个接口，而synchronized是java 的关键字，synchronized是内置的语义实现的
2.synchronized在发生异常时，会自动释放锁，而Lock在发生异常，如果没有主动通过unlock释放锁，可能造成死锁
3.Lock可以让等待锁的线程响应中断，而synchronized时，等待的线程会一直等待下去，不能响应中断
4.通过Lock可以知道有没有成功获得锁，而synchronized的不行
5.Lock可以提高多个线程并发读操作的效率
6.synchronized是非公平锁，无法保证线程获取锁的顺序。而Lock可以设置为公平锁

5.AQS 

AQS：多线程访问共享资源的同步器框架，资源分为独占式资源（只有一个线程能执行）代表实现有ReentrantLock,需要实现tryAcquire-tryRelease接口；共享资源（允许多个线程同时执行）代表实现有Semaphore,CountDownLatch，需要实现tryAcquireShared-tryReleaseShared；CyclicBarrier内部是通过ReentrantLock实现的；ReentranReadWriteLock同时实现了独占式和共享方式。

6.如何检测死锁？怎么预防死锁 

当有线程获取锁的时候，记录下来，相同，如果有线程请求锁，也会记录下来，当有线程请求锁失败时，需要检测是否已经产生死锁。

预防死锁：设置锁的有效时间

7.Java 内存模型 

为了保证共享内存的正确性（可见性、有序性、原子性），内存模型定义了共享内存系统中多线程程序读写操作行为的规范。 

8.线程池的种类，区别和使用场景 

线城池原理先判断线程池的核心线程池是否已满，如果已经未满，则创建一个线程来执行任务；如果已经满了，判断当前阻塞队列是否已满，如果未满，则将任务提交到阻塞队列中；如果满了，判断当前线程池线程数是否已经超过最大线程数，如果没有则创建一个新的线程来执行任务，如果满了交给饱和策略进行处理，默认的饱和策略是跑出异常，阻止系统正常运行。

线程池核心参数：

1. corePoolSize：指定了线程池中的核心线程数量。 
2. maximumPoolSize：指定了线程池中的最大线程数量。 
3. keepAliveTime：当前线程池数量超过corePoolSize时，多余的空闲线程的存活时间，即多次时间内会被销毁。 
4. unit：keepAliveTime的单位。
5. workQueue：任务队列，被提交但尚未被执行的任务,任务队列主要为ArrayBlockingQueue, LinkedBlockingQueue, SynchronousQueue
6. threadFactory：线程工厂，用于创建线程，一般用默认的即可。 
7. handler：拒绝策略，当任务太多来不及处理，如何拒绝任务。
   - AbortPolicy： 直接拒绝所提交的任务，并抛出**RejectedExecutionException**异常 
   - CallerRunsPolicy：只用调用者所在的线程来执行任务 
   - DiscardPolicy：不处理直接丢弃掉任务 
   - DiscardOldestPolicy：丢弃掉阻塞队列中存放时间最久的任务，执行当前任务

9.ThreadLocal原理，用的时候需要注意什么 

通过threadLocal.set方法将对象实例保存在每个线程所拥有的threadLocalMap中，这个每个线程使用自己的对象实例，彼此不会影响互相隔离。每次使用完ThreadLocal，都调用它的remove()方法，清除数据 ，防止内存泄露。

### spring

1.BeanFactory 和 FactoryBean 

BeanFactory是接口，提供了容器最基本的形式，给具体容器的实现提供了规范

FactoryBean在IOC容器的基础上给Bean的实现加上了一个简单工厂模式和装饰模式 ，可以通过getObject方法装饰bean实例

2.Spring IOC 的理解，其初始化过程 

spring通过配置文件描述bean与bean之间的关系，利用java反射实例化bean并建立bean之间的依赖关系

初始化过程：通过Resource定位bean的 定义的文件，bean配置信息的载入，封装成BeanDefinition,先spring容器注册BeanDefinition，最终是注入到一个HashMap的容器中

3.BeanFactory 和 ApplicationContext 

BeanFactory是接口，定义了容器的基本规范，ApplicationContext实现了BeanFactory接口

4.Spring Bean 的生命周期，如何被管理的 

![1350888580_1225](https://user-images.githubusercontent.com/13096375/53216740-8c630180-3690-11e9-9719-5822d0166131.jpg)

5.Spring Bean 的加载过程是怎样的 

6.Spring 是如何管理事务的，事务管理机制 

　Spring的事务管理机制实现的原理，就是通过这样一个动态代理对所有需要事务管理的Bean进行加载 ，然后通过代理对象中加入事务的逻辑处理代码

7.Spring 的不同事务传播行为有哪些，干什么用的 

propagation.required:如果存在一个事务则支持当前事务，没有事务则开启

propagation.required_new:总是开启一个新的事务，如果已经存在一个事务则将其挂起

propagation.mandatory:如果存在一个事务则支持当前事务，如果没有事务则抛出异常

propagation.supports:如果存在一个事务则支持当前事务，没有事务则非事务的执行

propagation.never:总是非事务的执行，如果存在事务则抛出异常

propagation.not_supported:总是非事务的执行并挂起任何事务 

propatation.nested:如果已经存在一个事务则运行一个嵌套事务(和父事务一起提交， 如果父事务回滚，其也要回滚)

8.Spring 中用到了那些设计模式 

工厂模式（BeanFactory），模板模式(BeanFactory),装饰模式（FactoryBean）,单利模式，代理模式（AOP）

9.BeanPostProcessor和BeanFactoryPostProcessor

BeanFactoryPostProcessor是在加载bean定义，但并未实例化bean对象时，覆盖或添加bean的属性；

BeanPostProcessor是在bean实例化阶段覆盖或添加bean的属性

10.如果理解控制翻转

spring容易管理所有注册的对象，同时对象依赖的其他对象会通过被动的方式传递过来，而不是对象自己去创建或查找依赖。

### mybatis

1.通常一个Xml映射文件，都会写一个Dao接口与之对应，请问，这个Dao接口的工作原理是什么？Dao接口里的方法，参数不同时，方法能重载？

调用接口方法时，接口全限名+方法名拼接字符串作为key值，可唯一定位一个MappedStatement 

Dao接口里的方法，是不能重载的，因为是全限名+方法名的保存和寻找策略 

Dao接口的工作原理是JDK动态代理，Mybatis运行时会使用JDK动态代理为Dao接口生成代理proxy对象，代理对象proxy会拦截接口方法，转而执行MappedStatement所代表的sql，然后将sql执行结果返回 

2.简述Mybatis的插件运行原理

Mybatis仅可以编写针对ParameterHandler、ResultSetHandler、StatementHandler、Executor这4种接口的插件，Mybatis使用JDK的动态代理，为需要拦截的接口生成代理对象以实现接口方法拦截功能，每当执行这4种接口对象的方法时，就会进入拦截方法，具体就是InvocationHandler的invoke()方法，当然，只会拦截那些你指定需要拦截的方法。 实现Mybatis的Interceptor接口并复写intercept()方法，然后在给插件编写注解，指定要拦截哪一个接口的哪些方法即可。

3.sql的执行流程图

![181036_sgdu_2727738](https://user-images.githubusercontent.com/13096375/53230230-335f9180-36c1-11e9-819e-3d5d2e93734d.jpg)

- MapperProxy:使用jdk的动态代理功能，间接的实例化Mapper的Proxy对象
- MapperMethod ：解析Mapper接口的方法，并封装成MapperMethod对象 ； 将Sql命令，正确路由到恰当的SqlSession的方法上 
- mybatis的拦截器只能拦截ParameterHandler、ResultSetHandler、StatementHandler、Executor共4个接口对象内 

### 分布式相关

### 缓存

1.Redis作为消息队列与RabbitMQ的比较

- 可靠消费：没有相应的机制保证消息的消费，当消费者消费失败的时候，消息体丢失，需要手动处理 

- 可靠发布：没有发布确认功能，需要自行实现

- 持久化：redis整个实例的持久化，rabbitmq支持队列和消息都可以选择是否持久化

- 队列监控：redis不支持，rabbitmq后后天可以监控


### JVM

