#### Name Server

> NameSever是专门为rocketmq设计的轻量级名称服务，具有简单，易扩展，无状态，集群之间互不通信的特点。

##### Name Server的作用

**Broker集群**

Broker用于接收生产者发送消息，或者消费者消费消息的请求。一个Broker集群由多组Master/Slave组成，Master可写可读，Slave只可以读，Master将写入的数据同步给Slave。每个Broker节点，在启动时，都会遍历NameServer列表，与每个NameServer建立长连接，注册自己的信息，之后定时上报

**Producer集群**

消息的生产者，通过NameServer集群获得Topic的路由信息，包括Topic下面有哪些Queue，这些Queue分布在哪些Broker上等。Producer只会将消息发送到Master节点上，因此只需要与Master节点建立连接。

**Consumer集群**

消息的消费者，通过NameServer集群获得Topic的路由信息，连接到对应的Broker上消费消息。注意，由于Master和Slave都可以读取消息，因此Consumer会与Master和Slave都建立连接。



##### Name Server相对于zookeeper的好处

RocketMQ的架构设计决定了只需要一个轻量级的元数据服务器就足够了，只需要保持最终一致，而不需要Zookeeper这样的强一致性解决方案，不需要再依赖另一个中间件，从而减少整体维护成本。



##### **NameServer如何保证数据的最终一致**

路由注册

对于Zookeeper、Etcd这样强一致性组件，数据只要写到主节点，内部会通过状态机将数据复制到其他节点，Zookeeper使用的是Zab协议，etcd使用的是raft协议。

RocketMQ采取的策略是，在Broker节点在启动的时候，轮训NameServer列表，与每个NameServer节点建立长连接，发起注册请求。NameServer内部会维护一个Broker表，用来动态存储Broker的信息。

同时，Broker节点为了证明自己是存活的，会将最新的信息上报给NameServer，然后每隔30秒向NameServer发送心跳包，心跳包中包含 BrokerId、Broker地址、Broker名称、Broker所属集群名称等等，然后NameServer接收到心跳包后，会更新时间戳，记录这个Broker的最新存活时间。

NameServer在处理心跳包的时候，存在多个Broker同时操作一张Broker表，为了防止并发修改Broker表导致不安全，路由注册操作引入了ReadWriteLock读写锁，这个设计亮点允许多个消息生产者并发读，保证了消息发送时的高并发，但是同一时刻NameServer只能处理一个Broker心跳包，多个心跳包串行处理。这也是读写锁的经典使用场景，即读多写少。

路由剔除

正常情况下，如果Broker关闭，则会与NameServer断开长连接，Netty的通道关闭监听器会监听到连接断开事件，然后会将这个Broker信息剔除掉。

异常情况下，NameServer中有一个定时任务，每隔10秒扫描一下Broker表，如果某个Broker的心跳包最新时间戳距离当前时间超多120秒，也会判定Broker失效并将其移除。

路由发现

客户端提供了定时拉取Topic最新路由信息的机制，客户端无法实时感知Broker服务器的宕机，需要通过重试机制来解决。