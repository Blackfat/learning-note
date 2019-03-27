### elasticsearch

- 单个节点由于物理机硬件限制，存储的文档是有限的，如果一个索引包含海量文档，则不能在单个节点存储。ES提供分片机制，同一个索引可以存储在不同分片（数据容器）中，这些分片又可以存储在集群中不同节点上
- 分片分为 主分片(primary shard) 以及 从分片(replica shard) 
- 主分片与从分片关系：从分片只是主分片的一个副本，它用于提供数据的冗余副本 
- 从分片应用：在硬件故障时提供数据保护，同时服务于搜索和检索这种只读请求 
- 是否可变：索引中的主分片的数量在索引创建后就固定下来了，但是从分片的数量可以随时改变 
- 索引默认创建的分片：默认设置5个主分片和一组从分片（即每个主分片有一个从分片对应），但是从分片没有被启用 （主从分片在同一个节点上没有意义），因此集群健康值显示为黄色(yellow)

### logstash

Logstash 提供了一个 shell 脚本叫 logstash 方便快速运行 。

> -e  

```bash
bin/logstash -e 'input { stdin { } } output { stdout {} }'
```

>--config 或 -f 

```bash
bin/logstash -f agent.conf 
```

> --configtest 或 -t   测试配置文件能否正常解析

>--log 或 -l  存储日志

```
bin/logstash -l logs/logstash.log
```

### filebeat

```bash
# 测试配置文件
./filebeat -configtest 
```

```bash
# filebeat启动
./filebeat -e -c filebeat.yml
```







