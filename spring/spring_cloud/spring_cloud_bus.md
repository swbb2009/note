# spring_cloud_bus

Spring Cloud Bus 将分布式系统的节点与轻量级消息代理链接。这可以用于广播状态更改（例如配置更改）或其他管理指令。一个关键的想法是，Bus 就像一个扩展的 Spring Boot 应用程序的分布式执行器，但也可以用作应用程序之间的通信渠道。

—— Spring Cloud Bus 官方解释

**下面是通过RabbitMQ中间件来实现bus功能的配置**
```
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-bus-amqp</artifactId>
</dependency>
```
```
spring:
  rabbitmq:
    host: localhost
    port: 32775
    username: guest
    password: guest
```

**P.S一个小发现，在controller上不加入@RefreshScope注解，也可以通过 localhost:8080/actuator/bus-refresh达到更新配置的目的**
```
原因是spring.cloud 默认有新增了ConfigurationPropertiesRebinder类，它监听了EnvironmentChangeEvent事件,它在发生EnvironmentChange事件后，
会重新构造原来的加了@ConfigurationProperties注解的Bean对象。这个是Spring Cloud的默认实现.
```
**另**
```
如果要单一的刷新   可以访问actuator/channels来获取destination
然后通过 http://localhost:3302/actuator/bus-refresh/{destination}进行单独更新，但这样貌似没有办法广播到多端口实例。
```