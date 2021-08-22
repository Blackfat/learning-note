### 核心架构

Broker消息服务器在启动的时候向所有的NameServer注册，消息生产者Producter在发送消息之前需要从NameServer获取Broker服务器的地址列表，然后根据负载均衡算法选择的消息服务器进行消息发送。

NameServer本身的高可用是通过部署多台服务器来实现的，但彼此之间互不通信。

### 路由注册、故障剔除

路由元信息RouteInfoManager

```java
    // topic消息队列的路由信息
    private final HashMap<String/* topic */, List<QueueData>> topicQueueTable;
    // broker基础信息，包含brokername， 所属集群名称，准备broker地址
    private final HashMap<String/* brokerName */, BrokerData> brokerAddrTable;
    // broker集群信息
    private final HashMap<String/* clusterName */, Set<String/* brokerName */>> clusterAddrTable;
    // broker状态信息，nameserver收到心跳包会替换该信息
    private final HashMap<String/* brokerAddr */, BrokerLiveInfo> brokerLiveTable;
    // broker上的filterserver列表
    private final HashMap<String/* brokerAddr */, List<String>/* Filter Server */> filterServerTable;
```

