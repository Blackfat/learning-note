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

