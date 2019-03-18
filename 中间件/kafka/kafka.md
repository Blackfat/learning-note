#### kafka运维脚本

| 脚本名称                            | 用途描述                                      |
| ----------------------------------- | --------------------------------------------- |
| connect-distributed.sh              | 连接kafka集群模式                             |
| connect-standalone.sh               | 连接kafka单机模式                             |
| kafka-acls.sh                       | todo                                          |
| kafka-broker-api-versions.sh        | todo                                          |
| kafka-configs.sh                    | 配置管理脚本                                  |
| kafka-console-consumer.sh           | kafka消费者控制台                             |
| kafka-console-producer.sh           | kafka生产者控制台                             |
| kafka-consumer-groups.sh            | kafka消费者组相关信息                         |
| kafka-consumer-perf-test.sh         | kafka消费者性能测试脚本                       |
| kafka-delegation-tokens.sh          | todo                                          |
| kafka-delete-records.sh             | 删除低水位的日志文件                          |
| kafka-log-dirs.sh                   | kafka消息日志目录信息                         |
| kafka-mirror-maker.sh               | 不同数据中心kafka集群复制工具                 |
| kafka-preferred-replica-election.sh | 触发preferred replica选举                     |
| kafka-producer-perf-test.sh         | kafka生产者性能测试脚本                       |
| kafka-reassign-partitions.sh        | 分区重分配脚本                                |
| kafka-replay-log-producer.sh        | todo                                          |
| kafka-replica-verification.sh       | 复制进度验证脚本                              |
| kafka-run-class.sh                  | todo                                          |
| kafka-server-start.sh               | 启动kafka服务                                 |
| kafka-server-stop.sh                | 停止kafka服务                                 |
| kafka-simple-consumer-shell.sh      | deprecated，推荐使用kafka-console-consumer.sh |
| kafka-streams-application-reset.sh  | todo                                          |
| kafka-topics.sh                     | topic管理脚本                                 |
| kafka-verifiable-consumer.sh        | 可检验的kafka消费者                           |
| kafka-verifiable-producer.sh        | 可检验的kafka生产者                           |
| trogdor.sh                          | todo                                          |
| zookeeper-security-migration.sh     | todo                                          |
| zookeeper-server-start.sh           | 启动zk服务                                    |
| zookeeper-server-stop.sh            | 停止zk服务                                    |
| zookeeper-shell.sh                  | zk客户端                                      |

 #### kafka基本命令

```bash
# 查看topic列表
bin/kafka-topics.sh --zookeeper 127.0.0.1:2181 --list
```



```bash
# 查看topic详情
bin/kafka-topics.sh --zookeeper 127.0.0.1:2181 -- describe -- topic ${topicName}
```



 ```bash
# 查看group列表
bin/kafka-consumer-groups.sh --bootstrap-server 127.0.0.1:9092 --list
 ```



``` bash
# 查看group详情
bin/kafka-consumer-groups.sh --bootstrap-server 127.0.0.1:9092 --describe --group ${groupName}
```



 ```bash
# 生产消息
bin/kafka-console-producer.sh --broker-list localhost:9092 --topic ${topicName}
 ```



``` bash
# 消费消息
bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 
--topic ${topicName} --from-beginning
```



#### kafka数据一致性和zookeeper数据一致性的区别

zookeeper数据一致性

zookeeper使用ZAB（zookeeper atomic broadcast）协议，保证leader，follower的一致性，leader负责数据的读写，follower只负责数据的读，如果follower遇到写操作，会提交leader，当leader宕机的话，会快速选举出新的leader，节点一开始都处于选举阶段，只要有一个节点数得到超半数节点的票数，它就可以当选leader。

zab协议

客户端每发送一个更新请求，zookeeper都会生成一个全局唯一的递增编号，这个编号反映所有事务操作的先后顺序，这个编号就是事物ID(ZXID)，只有更新的请求才算是事务请求。为保证按照事务的ZXID先后顺序来处理，Leader服务器会分别为每个Follower服务器创建一个队列，并将事务的先后顺序放入队列中，并按照FIFO的策略进行消息发送。收到需要处理的事务后，Follower服务器会首先以事务日志的形式写入服务器的磁盘中，写入成功后会向Leader服务器发送ACK响应。当Leader服务器收到超过一半的Follower服务器的ACK响应后，会向所有Follower服务器广播Commit消息，收到Commit消息的Follower服务器也会完成对事务的提交。类似于两阶段提交协议，区别在于，两阶段提交协议的第二阶段中，需要等到所有参与者的"YES"回复才会提交事务，只要有一个参与者反馈为"NO"或者超时无反馈，都需要中断和回滚事务。 

>在 ZAB 协议的事务编号 ZXID 设计中， ZXID 是一个 64 位的数字，低 32 位可以看作是一个简单的单调递增的计数器，针对客户端的每一个事务请求， Leader 服务器在产生一个新的事务 Proposal 的时候，都会对该计数器进行加1操作；高 32 位代表了 Leader 周期 epoch 的编号，每当选举产生一个新的 Leader 服务器，就会从这个 Leader 服务器上取出其本地日志中最大事务 Proposal 的 ZXID ,并从该 ZXID 中解析出对应的 epoch 值，然后再对其进行加1操作，之后就会以此编号作为新的 epoch, 并将低 32 位置0来开始生成新的 ZXID 。 

zookeeper的工作方式

- zookeeper集群包括一个leader，多个follower
- 所有follower都可提供读服务
- 所有写操作到会被forward到leader
- client和server通过nio通信
- 全局串行化所有的写操作
- 保证同一个客户端的执行被fifo执行

kafka数据一致性

kafka的leader负责读写，follower只负责备份，kafka动态维护了一个同步状态的副本集合，简称ISR（in-sync replica，已同步的副本）（具有f+1个节点）可以允许f个节点宕机的情况下不会丢失消息并正常提供服务。ISR的成员是动态的，如果一个节点被淘汰了，当它达到同步状态是，可以重新加入ISR。如果leader宕机了，直接从ISR中选择一个follower。

kafka 分区leader选举

kafka会在中zookeeper上针对每个Topic维护一个称为ISR的集合，该集合中是一些分区的副本。只有当这些副本都跟Leader中的副本同步了之后，kafka才会认为消息已提交，并反馈给消息的生产者。如果这个集合有增减，kafka会更新zookeeper上的记录 。如果某个分区的Leader不可用，Kafka就会从ISR集合中选择一个副本作为新的Leader。 显然通过ISR，kafka需要的冗余度较低，可以容忍的失败数比较高。假设某个topic有f+1个副本，kafka可以容忍f个服务器不可用。 

kafka控制器选举

kafka的控制器的选举依赖zookeeper，成功竞选为控制器的broker会在zookeeper中创建临时节点(/controller)，在任意时刻，集群中有且仅有一个控制器。zookeeper中还存在一个持久节点(/controller_epoch),用于记录控制器发生变更的次数。每个和控制器交互的请求都会携带controller_epoch字段，如果请求的controller_epoch小于内存中的controller_epoch值，则认为这个请求是向已经过期的控制器发送的请求，请求会判断无效，如果请求的controller_epoch大于内存的controller_epoch值，则说明已经有新的控制器当选了。通过controller_epoch保证了控制器的唯一性。

#### kafka基础知识

1.Kafka中的ISR、AR又代表什么？ISR的伸缩又指什么 

AR:分区中所有的副本

ISR:与leader副本保持一定程度同步的副本的集合( rerplica.lag.time.max.ms  min.insync.replicas request.required.asks )

2.Kafka中的HW、LEO、LSO、LW等分别代表什么？ 

HW:分区副本消息的偏移量，消费者只能拉取这个偏移量之前的消息

LEO:分区副本中当前日志文件下一条待写入消息的偏移量

LSO:分区副本日志文件的起始偏移

LW:AR集合中最小的logStartOffset，日志分段被清理或删除消息请求都可能促使LW的增长

3.Kafka中是怎么体现消息顺序性的 

通过message的key相同的message保证消费顺序，同时也要防止消息重试对消息顺序的影响（max.in.flight.requests.per.connection = 1 保证在后一条消息发送前，前一条的消息状态已经是可知的 ）

4.Kafka中的分区器、序列化器、拦截器是否了解？它们之间的处理顺序是什么 

序列化器：生产者需要用序列化器把对象转换成字节数组才能发给kafka，消费者需要用反序列化器把kafka中收到的字节数组转换成对象。

分区器：消息经过序列化后需要明确知道发往哪个分区，如果消息指定了分区，就不需要经过分区器，否则需要经过分区器。

拦截器：生产者拦截器，生产者在消息序列化和计算分区之前或消息被应答之前进行拦截操作。

​                消费者拦截器，消费者在消费消息之前或提交消费位移提交时进行拦截操作。

5.消费者再均衡

再均衡：分区的所属权，从一个消费者转移到另一个消费者，它为消费者组具有高可用和伸缩性提供保障，在再均衡期间，消费者组是不可以读取消息的。

6.Kafka生产者客户端中使用了几个线程来处理？分别是什么 

主线程：在主线程中由KafkaProducer创建消息，然后通过可能的拦截器，序列化器，分区器作用之后缓存累加到消息累加器中(RecordAccumulator)中。

发送线程:负责从消息累加器中获取消息将其发送到kafka中。消息累加器的缓存大小可以通过`buffer.memory`设置，如果生产者发送消息的速度超过发送到服务器的速度，这个时候send()方法要不被阻塞，要不抛出异常，可以通过`max.block.ms`配置。

7.Kafka的旧版Scala的消费者客户端的设计有什么缺陷 



8.消息重复消费和消息漏消费

消息漏消费：消费位移的提交在消费者拉取消息但并没有消费完之前，消费出现异常。

消息重复消费：消费位移的在消费完所有拉取的消息之后才执行，消费过程中出现异常。

9.KafkaConsumer是非线程安全的，那么怎么样实现多线程消费 



10.简述消费者与消费组之间的关系 

每个消费者对应着一个消费者组，默认为`test-consumer-group`，当消息发往主题后，只会被订阅它的消费者组中的一个消费者所消费。

11.topic的分区数可不可以增加？如果可以怎么增加？如果不可以，那又是为什么？ 



12.topic的分区数可不可以减少？如果可以怎么减少？如果不可以，那又是为什么？ 



13.创建topic时如何选择合适的分区数 



14.Kafka目前有那些内部topic，它们都有什么特征？各自的作用又是什么 



15.优先副本是什么？它有什么特殊的作用 



16.Kafka中的事务是怎么实现的

 

17.Kafka中怎么实现死信队列和重试队列 

死信队列：由于某些原因，消息无法被正确的投递，为了确保消息不会被无故的丢弃，一般将其置于一个特殊的队列。

重试队列：消费者端消费消息失败，为了防止消息无故的丢失而重新将消息回滚到broker中。

18.Kafka中的延迟队列怎么实现 

延迟队列：消息发送后不想立刻被消费，而是希望等待一定的时候后才被消费者发现消费。

19.怎么样才能确保Kafka极大程度上的可靠性 

