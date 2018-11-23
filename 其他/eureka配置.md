### eureka手动维护

#### eureka查看服务

```http  get
http://hxtrust:hxtrust@192.168.11.100:8761/eureka/apps/{application.name}
```

#### eureka停用服务

```http delete
http://hxtrust:hxtrust@192.168.11.100:8761/eureka/apps/{application.name}/{instanceId}
```
### eureka参数配置

>## eureka.client.registry-fetch-interval-seconds

表示eureka client间隔多久去拉取服务注册信息，默认为30秒，对于api-gateway，如果要迅速获取服务注册状态，可以缩小该值，比如5秒 

>## eureka.instance.lease-renewal-interval-in-seconds

leaseRenewalIntervalInSeconds，表示eureka client发送心跳给server端的频率。如果在leaseExpirationDurationInSeconds后，server端没有收到client的心跳，则将摘除该instance。除此之外，如果该instance实现了HealthCheckCallback，并决定让自己unavailable的话，则该instance也不会接收到流量。

 默认30秒

>## eureka.instance.lease-expiration-duration-in-seconds

leaseExpirationDurationInSeconds，表示eureka server至上一次收到client的心跳之后，等待下一次心跳的超时时间，在这个时间内若没收到下一次心跳，则将移除该instance。 

- 默认为90秒
- 如果该值太大，则很可能将流量转发过去的时候，该instance已经不存活了。
- 如果该值设置太小了，则instance则很可能因为临时的网络抖动而被摘除掉。
- 该值至少应该大于leaseRenewalIntervalInSeconds

>## eureka.server.enable-self-preservation

当Eureka Server节点在短时间内丢失过多客户端时（可能发生了网络分区故障），那么这个节点就会进入自我保护模式。一旦进入该模式，Eureka Server就会保护服务注册表中的信息，不再删除服务注册表中的数据（也就是不会注销任何微服务）。当网络故障恢复后，该Eureka Server节点会自动退出自我保护模式。

 >## eureka.server.eviction-interval-timer-in-ms

eureka server清理无效节点的时间间隔，默认60000毫秒，即60秒 



