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

1.7中对key等于null的情况作了特殊处理，key==null默认存储在tale[0]的位置

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

10.零拷贝和netty的零拷贝

零拷贝：操作数据时, 不需要将数据 buffer 从一个内存区域拷贝到另一个内存区域. 因为少了一次内存的拷贝, 因此 CPU 的效率就得到的提升。

在操作系统层面上，零拷贝指用户态和内核态之间拷贝数据。Linux 提供的 `mmap` 系统调用, 它可以将一段用户空间内存映射到内核空间, 当映射成功后, 用户对这段内存区域的修改可以直接反映到内核空间; 同样地, 内核空间对这段区域的修改也直接反映用户空间. 正因为有这样的映射关系, 我们就不需要在用户态和内核态之间拷贝数据, 提高了数据传输的效率 。

netty的零拷贝是针对java层面上的，偏向于数据优化。

- Netty 提供了 `CompositeByteBuf` 类, 它可以将多个 ByteBuf 合并为一个逻辑上的 ByteBuf, 避免了各个 ByteBuf 之间的拷贝 。
- 通过 wrap 操作, 我们可以将 byte[] 数组、ByteBuf、ByteBuffer等包装成一个 Netty ByteBuf 对象, 进而避免了拷贝操作 
- ByteBuf 支持 slice 操作, 因此可以将 ByteBuf 分解为多个共享同一个存储区域的 ByteBuf, 避免了内存的拷贝 

11.CountDownLatch和CyclicBarrier

- 闭锁CountDownLatch唯一的构造函数CountDownLatch(int count)，在闭锁上调用countDown方法，闭锁计数器将减1，当闭锁计数器为0时，闭锁将打开，所有线程将通过闭锁。
- CyclicBarrier支持一个可选的Runnable参数，当线程当线程在CyclicBarrier对象上调用await()方法时，栅栏的计数器将增加1，当计数器为parties时，栅栏将打开

  闭锁不能重复使用，栅栏可以重复使用。

> 班级集体野炊，在大巴上等待所有同学到来才开始出发；一个班级集合完毕出发一辆大巴，这是CyclicBarrier。到达目的地后需要同学自行寻找食材，寻找到需要的所有食材后才开始做饭，一个班级做饭活动是一次性的，这是CountDownLatch。 

### spring

1.BeanFactory 和 FactoryBean 

BeanFactory是接口，提供了容器最基本的形式，给具体容器的实现提供了规范

FactoryBean在IOC容器的基础上给Bean的实现加上了一个简单工厂模式和装饰模式 ，可以通过getObject方法装饰bean实例

2.Spring IOC 的理解，其初始化过程 

spring通过配置文件描述bean与bean之间的关系，利用java反射实例化bean并建立bean之间的依赖关系

初始化过程：通过Resource定位bean的 定义的文件，bean配置信息的载入，封装成BeanDefinition,然后spring容器注册BeanDefinition，最终是注入到一个HashMap的容器中

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

工厂模式（BeanFactory），模板方法(BeanFactory),装饰模式（FactoryBean）,单利模式，代理模式（AOP）

9.BeanPostProcessor和BeanFactoryPostProcessor

BeanFactoryPostProcessor是在加载bean定义，但并未实例化bean对象时，覆盖或添加bean的属性；

BeanPostProcessor是在bean实例化阶段覆盖或添加bean的属性

10.如果理解控制翻转

spring容器管理所有注册的对象，同时对象依赖的其他对象会通过被动的方式传递过来，而不是对象自己去创建或查找依赖。

11.spring mvc工作机制

客户端发送请求-> 前端控制器 DispatcherServlet 接受客户端请求 -> 找到处理器映射 HandlerMapping 解析请求对应的 Handler-> HandlerAdapter 会根据 Handler 来调用真正的处理器开处理请求，并处理相应的业务逻辑 -> 处理器返回一个模型视图 ModelAndView -> 视图解析器进行解析 -> 返回一个视图对象->前端控制器 DispatcherServlet 渲染数据（Moder）->将得到视图对象返回给用户。

>RequestMappingHandlerMapping，主要做的工作是将Contoller的带RequestMapping方法，添加到处理方法映射器和路径方法解决器中 。

>RequestMappingHandlerAdapter的主要作用就是调用RequestMappingHandlerMapping所获取到的handler，然后将返回值封装为一个ModelAndView对象 。

12.springboot的运行原理

@SpringBootApplication注解实际上是一个组合注解 ，除了对应用开放的@ComponentScan注解（实现对开发者自定义的应用包扫描）外 ，最核心的注解就是**@EnableAutoConfiguration** ，在逻辑上实现了对所依赖的核心spring-boot-autoconfigture中(META-INF/spring.facotories)中的文件扫描，该文件声明了哪些配置需要被spring容器加载，同时基于条件注解，springboot能够自动加载核心配置，以及其他依赖项目的配置，完成应用的自动初始化。

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

1.服务雪崩的处理

>一个服务失败，导致整个链路服务失败的情形，称之为服务雪崩

服务熔断降级：当下游服务由于某种原因变得不可用或响应过慢，上游服务为了保证自己服务的整体性，不再调用目标服务，直接返回，快速释放资源，如果 目标服务转好则恢复调用。推荐使用Hystrix 实现。

```
//滑动窗口的大小，默认为20
circuitBreaker.requestVolumeThreshold 
//过多长时间，熔断器再次检测是否开启，默认为5000，即5s钟
circuitBreaker.sleepWindowInMilliseconds 
//错误率，默认50%
circuitBreaker.errorThresholdPercentage
```

每当20个请求中，有50%失败时，熔断器就会打开，此时再调用此服务，将会直接返回失败，不再调远程服务。直到5s钟之后，重新检测该触发条件，判断是否把熔断器关闭，或者继续打开 。

2.zookeeper的特性

- 顺序一致性（从同一个客户端发起的事务请求，会严格的按照发起顺序应用到zookeeper上。所有的更新都是全局有序的，每个更新都有一个唯一的时间戳，这个时间戳称为zxid ，而读请求只会相对于更新有序，也就是读请求的返回结果中会带有这个 Zookeeper 最新的 zxid 
- 原子性（所有事务请求的处理结果在整个集群中所有机器上的应用情况是一致的，即整个集群要么都成功应用了某个事务，要么都没有应用。 
- 单一视图（无论客户端连接的是哪个 Zookeeper 服务器，其看到的服务端数据模型都是一致的 
- 可靠性（一旦服务端成功地应用了一个事务，并完成对客户端的响应，那么该事务所引起的服务端状态变更将会一直被保留，除非有另一个事务对其进行了变更。 
- 实时性 （Zookeeper 保证在一定的时间段内，客户端最终一定能够从服务端上读取到最新的数据状态。 

3.zookeeper的watch机制

zookeeper中所有的读操作（getData,getChildren,exists)都可以设置watch选项，并且watch事件具有一次性触发的特性。

4.Dubbo的底层实现原理和机制 

dubbo是一个rpc框架，一个soa框架。作为rpc支持多种传输协议， 默认时dubbo协议，底层采用socket进行通讯，默认采用netty长连接进行传输。 作为soa提供了服务治理的功能，提供服务的注册和发现，使用zookeeper作为注册中心，启动时会把所有的服务接口注册到注册中心，并且订阅configurators,服务消费端订阅provide，configurators,routers,订阅变更时，zk会推送providers,configuators，routers,启动时注册长连接，进行通讯 同事提供各种容错机制和负载均衡策略 。

5.描述一个服务从发布到被消费的详细过程 

- start启动服务，底层是启动一个netty的server端
- register注册服务到注册中心
- subscribe向注册中心订阅服务（只订阅使用到的服务，首次拉取订阅的服务列表，缓存在本地），当服务发生变化时，获取最新的服务列表，更新本地缓存 
- Consumer 直接发起对 Provider 的调用，无需经过注册中心 ，通过动态代理实现对provider的本地调用，通过cluster组件负载均衡到服务的地址，底层启动netty的client端连接server端。

6.分布式系统怎么做服务治理 



7.接口的幂等性的概念 



8.消息中间件如何解决消息丢失问题 

- 生成产消息丢失：rabbitmq生产者开启confirm模式，异步通知接受情况，接受失败重新发送。kafka设置
  - 给 topic 设置 `replication.factor` 参数：这个值必须大于 1，要求每个 partition 必须有至少 2 个副本。
  - 在 Kafka 服务端设置 `min.insync.replicas` 参数：这个值必须大于 1，这个是要求一个 leader 至少感知到有至少一个 follower 还跟自己保持联系，没掉队，这样才能确保 leader 挂了还有一个 follower 吧。
  - 在 producer 端设置 `acks=all` ：这个是要求每条数据，必须是 写入所有 replica 之后，才能认为是写成功了 。
  - 在 producer 端设置 `retries=MAX` （很大很大很大的一个值，无限次重试的意思）：这个是 要求一旦写入失败，就无限重试 ，卡在这里了。
- 消费者消息丢失：消费者手动ack，确保消息处理完毕。

9.Dubbo的服务请求失败怎么处理 

- failover 失败自动切换  默认重试2次，默认超时时间1s
- failfast  快速失败 ，适用于幂等操作
- failsafe  失败安全 仅会打印异常，而不会抛出异常 ，返回空结果忽略错误
- failback 失败自动恢复

10.如何实现负载均衡，有哪些算法可以实现 

- 随机算法
- 加权算法
- iphash算法

11.分布式集群下如何做到唯一序列号 

- UUID，没有排序，无法保证趋势递增 ，UUID往往是使用字符串存储，查询的效率比较低 ，存储空间比较大。
- 数据库自增
- 自定义（时间戳+业务id+随机数）  

12.dubbo服务发布流程

- 暴露本地服务
- 暴露远程服务
- 启动netty
- 连接zookeeper
- 在zookeeper注册
- 监听zookeeper

13.dubbo中zookeeper做注册中心,如果注册中心集群都挂掉,那发布者和订阅者还能通信吗? 

zookeeper的信息会缓存到本地作为一个缓存文件,并且转换成`properties`对象方便使用 

14.幂等性

- 唯一索引和唯一组合索引防止新增时存在脏数据

- token机制，防止页面重复提交  

  数据提交前要向服务的申请token，token放到redis ，token设置有效时间

  提交后后台校验token，同时删除token，生成新的token返回 

- 更新操作通过版本号实现乐观锁，乐观锁的更新操作，最好用主键或者唯一索引来更新,这样是行锁，否则更新时会锁表 

- 对外提供接口的幂等

  对外提供接口为了支持幂等调用，接口有两个字段必须传，一个是来源source，一个是来源方序列号seq，这个两个字段在提供方系统里面做联合唯一索引 

### 缓存

1.Redis作为消息队列与RabbitMQ的比较

- 可靠消费：没有相应的机制保证消息的消费，当消费者消费失败的时候，消息体丢失，需要手动处理 
- 可靠发布：没有发布确认功能，需要自行实现
- 持久化：redis整个实例的持久化，rabbitmq支持队列和消息都可以选择是否持久化
- 队列监控：redis不支持，rabbitmq后后天可以监控

2.Redis缓存穿透，缓存雪崩 

缓存穿透：故意去请求缓存中不存在的数据，导致所有请求都落到数据库上，导致数据库连接异常。

- 采用异步更新策略，无论key是否取到值，都直接返回。value值中维护一个缓存失效时间，缓存如果过期，异步起一个线程去读数据库，更新缓存。需要做**缓存预热**(项目启动前，先加载缓存)操作 
- 提供一个能迅速判断请求是否有效的拦截机制，比如，利用布隆过滤器，内部维护一系列合法有效的key。迅速判断出，请求所携带的Key是否合法有效。如果不合法，则直接返回 。

缓存雪崩：缓存同一时间大面积的失效 ，导致所有请求都落到数据库上，导致数据库连接异常。

- 给缓存的失效时间，加上一个随机值，避免集体失效 

3.redis事务

Redis Cluster集群架构，不同的key是有可能分配在不同的Redis节点上的，在这种情况下Redis的事务机制是不生效的。其次，Redis事务不支持回滚操作,所以基本不用 。

4.Redis的多数据库机制 

Redis支持多个数据库，并且每个数据库的数据是隔离的不能共享，单机下的redis可以支持16个数据库 。

在Redis Cluster集群架构下只有一个数据库空间，即db0 。

5.Redis的批量操作 

Redis Cluster集群架构，不同的key会划分到不同的slot中，因此直接使用mset或者mget等操作是行不通的，如果每个键都位于同一个节点，则可以正常支持。 

6.高可用集群故障机制

-  集群中的每个节点都会定期的向其它节点发送PING命令，并且通过有没有收到回复判断目标节点是否下线 
-  集群中每一秒就会随机选择几个节点，然后选择其中最久没有响应的节点放PING命令 
- 如果一定时间内目标节点都没有响应，那么该节点就认为目标节点**疑似下线** 
- 当集群中的节点超过半数认为该目标节点疑似下线，那么该节点就会被标记为**下线** 
- **当集群中的任何一个节点下线，就会导致插槽区有空档，不完整，那么该集群将不可用** 
-  如果该下线的节点使用了主从模式,那么该节点(master)宕机后,集群会将该节点的从库(slave)提升为(master)继续完成集群服务. 

7.单线程的redis为什么这么快（单进程单线程）

- redis是纯内存操作，单线程操作避免了频繁的上线文切换
- 采用了非阻塞i/o多路复用机制。（通过调用多路复用API同时处理多个通道描述符的读写事件）
- redis通信协议resp

8.redis的过期策略以及内存淘汰机制

过期策略：**定期删除+惰性删除** 

- noeviction 不会继续服务写请求 (DEL 请求可以继续服务)，读请求可以继续进行。这样可以保证不会丢失数据，但是会让线上的业务不能持续进行。这是默认的淘汰策略。 
- volatile-lru 尝试淘汰设置了过期时间的 key，最少使用的 key 优先被淘汰。没有设置过期时间的 key 不会被淘汰，这样可以保证需要持久化的数据不会突然丢失。（这个是使用最多的） 
- volatile-ttl 跟上面一样，除了淘汰的策略不是 LRU，而是 key 的剩余寿命 ttl 的值，ttl 越小越优先被淘汰。 
- volatile-random 在设置了过期时间的键空间中，随机移除某个key 。 
- allkeys-lru 区别于 volatile-lru，这个策略要淘汰的 key 对象是全体的 key 集合，而不只是设置了过期时间的 key 集合。这意味着没有设置过期时间的 key 也会被淘汰。 
- allkeys-random 跟上面一样，不过淘汰的策略是随机的 key。allkeys-random 跟上面一样，不过淘汰的策略是随机的 key。 

### 数据库

1.mysql主从互备的原理

主库有一个`log dump`线程，将`binlog`传给从库 从库有两个线程，一个I/O线程，一个SQL线程，I/O线程读取主库传过来的`binlog`内容并写入到`relay log`,SQL线程从`relay log`里面读取内容，写入从库的数据库。 

2.避免使用子查询

- 子查询的结果集无法使用索引，通常子查询的结果集会被存储到临时表中，不论是内存临时表还是磁盘临时表都不会存在索引，所以查询性能 会受到一定的影响 
-  由于子查询会产生大量的临时表也没有索引，所以会消耗过多的CPU和IO资源，产生大量的慢查询 

3.mysql开发规范

- 避免数据类型的隐式转换 
- 禁止使用SELECT \* 必须使用SELECT <字段列表> 查询
- 避免使用子查询，可以把子查询优化为join操作
- 对应同一列进行or判断时，使用in代替or
- 禁止使用order by rand() 进行随机排序
- WHERE从句中禁止对列进行函数转换和计算 

4.mysql隔离级别

mysql的隔离级别有读未提交，读提交，可重复读，串行化，默认是可重复读。默认为可重复读，主要是读提交的隔离级别下，binlog为statement格式会出现主从不一致。

选个读提交隔离级别的原因：

- 在RR隔离级别下，存在间隙锁，导致出现死锁的概率比RC下大
- 在RR隔离级别下，添加未命中索引会锁表，而RC隔离级别下只会锁 行
- 在RC隔离基本下，binlog设置为row格式更好。

### kafka



### JVM







