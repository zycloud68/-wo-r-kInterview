# Dubbo面试总结

### 什么叫dubbo?

阿里开源项目，国内很多互联网公司都在用，已经经过很多线上考验。**内部使用了 Netty、Zookeeper**，保证了**高性能高可用性**，**分布式架构可以承受更大规模的并发流量**。

Dubbo 可以将**核心业务抽取**出来，作为**独立的服务**，逐渐形成稳定的**服务中心**，可用于提高**业务复用灵活扩展**，使前端应用能更快速的响应多变的市场需求。

[图片展示](https://camo.githubusercontent.com/e11a2ff9575abc290657ba3fdbff5d36f1594e7add67a72e0eda32e449508eef/68747470733a2f2f647562626f2e6170616368652e6f72672f696d67732f6172636869746563747572652e706e67)

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

