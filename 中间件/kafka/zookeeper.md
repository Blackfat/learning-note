#### zookeeper配置信息

```bash
#基本事件单元，以毫秒为单位。这个时间是作为zookeeper服务器之间或客户端与服务器间维持心跳的时间。也就是每#隔tickTime时间就会发送一个心跳
ticketTime=2000
#配置zookeeper接收客户端初始化连接能忍受多少个心跳时间间隔数
initLimit=10
#配置项标识Leader和Follower之间发送消息的时间间隔数
syncLimit=5
#端口号
clientPort=2181
#存储内存中数据库快照的位置
dataDir=/usr/local/cluster/zookeeper1/data
dataLogDir=/usr/local/cluster/zookeeper1/logs
#server.myid=IP:Port1:Port2, myid是服务器的编号，一个正整数，一般是0、1、2、3等待，port1表示的是服务 #器与集群中的Leader服务器交换信息的端口，一般用2288，Port2表示的是万一集群中的Leader服务器宕机了，需要# #一个端口来重新进行宣讲，选出一个新的Leader，一般用3388
server.1=127.0.0.1:2888:3888
server.2=127.0.0.1:2889:3889
server.3=127.0.0.1:2890:3890
```

