# Dubbo面试总结

### 什么叫dubbo?

阿里开源项目，国内很多互联网公司都在用，已经经过很多线上考验。**内部使用了 Netty、Zookeeper**，保证了**高性能高可用性**，**分布式架构可以承受更大规模的并发流量**。

Dubbo 可以将**核心业务抽取**出来，作为**独立的服务**，逐渐形成稳定的**服务中心**，可用于提高**业务复用灵活扩展**，使前端应用能更快速的响应多变的市场需求。

![图片展示](https://camo.githubusercontent.com/e11a2ff9575abc290657ba3fdbff5d36f1594e7add67a72e0eda32e449508eef/68747470733a2f2f647562626f2e6170616368652e6f72672f696d67732f6172636869746563747572652e706e67)
#### 节点角色说明

| 节点      | 角色说明                               |
| --------- | -------------------------------------- |
| Provider  | 暴露服务的服务提供方                   |
| Consumer  | 调用远程服务的服务消费方               |
| Register  | 服务注册与发现的注册中心               |
| Moniter   | 统计服务的调用次数和调用时间的监控中心 |
| Container | 服务运行容器                           |

#### 调用关系说明

0 服务容器负责启动，加载，运行服务提供者。

1 服务提供者在启动时，向注册中心注册自己提供的服务。

2 服务消费者在启动时，向注册中心订阅自己所需的服务。

3 注册中心返回服务提供者地址列表给消费者，如果有变更，注册中心将基于长连接推送变更数据给消费者。

4 服务消费者，从提供者地址列表中，基于软负载均衡算法，选一台提供者进行调用，如果调用失败，再选另一台调用。

5 服务消费者和提供者，在内存中累计调用次数和调用时间，定时每分钟发送一次统计数据到监控中心。

### 

### dubbo简单实例入门

1. 本地服务Spring配置文档 local.xml

```xml
<bean id=“xxxService” class=“com.xxx.XxxServiceImpl” />
<bean id=“xxxAction” class=“com.xxx.XxxAction”>
    <property name=“xxxService” ref=“xxxService” />
</bean>
```

2. 远程服务Spring配置   

在本地服务中,将local.xml文档变为两份,将服务定义部分放在服务提供方remote-provider.xml,将服务引用部分放在服务消费方remote-consumer.xml.

注意: 在服务定义方需要暴露服务配置<<dubbo:service>>,在消费方增加引用服务配置<<dubbo:reference>>

remote-provider.xml

```xml
<!-- 和本地服务一样实现远程服务 -->
<bean id=“xxxService” class=“com.xxx.XxxServiceImpl” /> 
<!-- 增加暴露远程服务配置 -->
<dubbo:service interface=“com.xxx.XxxService” ref=“xxxService” /> 
```

remote-consumer.xml

```xml
<!-- 增加引用远程服务配置 -->
<dubbo:reference id=“xxxService” interface=“com.xxx.XxxService” />
<!-- 和本地服务一样使用远程服务 -->
<bean id=“xxxAction” class=“com.xxx.XxxAction”> 
    <property name=“xxxService” ref=“xxxService” />
</bean>
```

