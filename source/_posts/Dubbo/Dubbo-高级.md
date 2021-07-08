---
title: Dubbo高级
date: 2021-07-8 00:00:00
author: 神奇的荣荣
summary: ""
categories: Dubbo
tags: 
    - Dubbo
    - 分布式
---

# Dubbo 高可用

## 集群

Dubbo集群很简单，只是需要对服务提供者的配置文件进行修改，配置文件中的application.name相同，Dubbo则会认为是同一集群。

<!-- more -->

## 集群容错

集群调用失败时，Dubbo 提供了多种容错方案，缺省为 failover 重试。

### 集群容错

```
Failover Cluster
失败自动切换，当出现失败，重试其它服务器。通常用于读操作，但重试会带来更长延迟。可通过 retries="2" 来设置重试次数(不含第一次)。

重试次数配置如下：
<dubbo:service retries="2" />
或
<dubbo:reference retries="2" />
或
<dubbo:reference>
    <dubbo:method name="findFoo" retries="2" />
</dubbo:reference>

Failfast Cluster
快速失败，只发起一次调用，失败立即报错。通常用于非幂等性的写操作，比如新增记录。

Failsafe Cluster
失败安全，出现异常时，直接忽略。通常用于写入审计日志等操作。

Failback Cluster
失败自动恢复，后台记录失败请求，定时重发。通常用于消息通知操作。

Forking Cluster
并行调用多个服务器，只要一个成功即返回。通常用于实时性要求较高的读操作，但需要浪费更多服务资源。可通过 forks="2" 来设置最大并行数。

Broadcast Cluster
广播调用所有提供者，逐个调用，任意一台报错则报错 [2]。通常用于通知所有提供者更新缓存或日志等本地资源信息。
```

### 集群模式配置

按照以下示例在服务提供方和消费方配置集群模式
```
<dubbo:service cluster="failsafe" />
或
<dubbo:reference cluster="failsafe" />
```

## 负债均衡策略

集群负载均衡时，Dubbo 提供了多种均衡策略，缺省为 random 随机调用。

### 负载均衡策略

```
Random LoadBalance
随机均衡算法，按权重设置随机概率。
在一个截面上碰撞的概率高，但调用量越大分布越均匀，而且按概率使用权重后也比较均匀，有利于动态调整提供者权重。

RoundRobin LoadBalance
权重轮循均衡算法，按公约后的权重设置轮循比率。
存在慢的提供者累积请求的问题，比如：第二台机器很慢，但没挂，当请求调到第二台时就卡在那，久而久之，所有请求都卡在调到第二台上。

LeastActive LoadBalance
最少活跃调用数均衡算法，相同活跃数的随机，活跃数指调用前后计数差。
使慢的提供者收到更少请求，因为越慢的提供者的调用前后计数差会越大。

ConsistentHash LoadBalance
一致性 Hash均衡算法，相同参数的请求总是发到同一提供者。
当某一台提供者挂时，原本发往该提供者的请求，基于虚拟节点，平摊到其它提供者，不会引起剧烈变动。算法参见：http://en.wikipedia.org/wiki/Consistent_hashing
缺省只对第一个参数 Hash，如果要修改，请配置 <dubbo:parameter key="hash.arguments" value="0,1" />
缺省用 160 份虚拟节点，如果要修改，请配置 <dubbo:parameter key="hash.nodes" value="320" />
```

### 负债均衡配置

负债均衡配置很简单。
服务端和客户端都可以配置服务级别或者方法级别的策略。

服务提供者：
```xml
<!-- 全局配置负债均衡 -->
<dubbo:provider loadbalance="roundrobin" />

<!-- 接口级别配置负债均衡 -->
<dubbo:service interface="com.oyr.user.service.UserService" ref="userServiceImpl" loadbalance="roundrobin" >
    <dubbo:method name="getUserAddress" />
</dubbo:service>

<!-- 方法级别配置负债均衡 -->
<dubbo:service interface="com.oyr.user.service.UserService" ref="userServiceImpl" >
    <dubbo:method name="getUserAddress" loadbalance="roundrobin" />
</dubbo:service>
```

服务消费者：
```xml
<!-- 全局配置负债均衡 -->
<dubbo:consumer timeout="3000" loadbalance="roundrobin"/>

<!-- 接口级别配置负债均衡 -->
<dubbo:reference id="userService" interface="com.oyr.user.service.UserService" loadbalance="roundrobin">
    <dubbo:method name="getUserAddress" />
</dubbo:reference>

<!-- 方法级别配置负债均衡 -->
<dubbo:reference id="userService" interface="com.oyr.user.service.UserService" >
    <dubbo:method name="getUserAddress" loadbalance="roundrobin" />
</dubbo:reference>
```

## 注册中心宕机与Dubbo直连

### 注册中心宕机问题

在实际生产中，假如zookeeper注册中心宕掉，一段时间内服务消费方还是能够调用提供方的服务的，实际上它使用的本地缓存进行通讯，这只是dubbo健壮性的一种。

健壮性  
监控中心宕掉不影响使用，只是丢失部分采样数据  
数据库宕掉后，注册中心仍能通过缓存提供服务列表查询，但不能注册新服务  
注册中心对等集群，任意一台宕掉后，将自动切换到另一台  
注册中心全部宕掉后，服务提供者和服务消费者仍能通过本地缓存通讯  
服务提供者无状态，任意一台宕掉后，不影响使用  
服务提供者全部宕掉后，服务消费者应用将无法使用，并无限次重连等待服务提供者恢复

### 直连模式

注册中心的作用在于保存服务提供者的位置信息，我们可以完全可以绕过注册中心——采用dubbo直连，即在服务消费方配置服务提供方的位置信息。  
点对点直连方式，将以服务接口为单位，忽略注册中心的提供者列表，A 接口配置点对点，不影响 B 接口从注册中心获取列表。

xml配置方式：
```xml
<dubbo:reference id="userService" 
    interface="com.zang.gmall.service.UserService" url="dubbo://localhost:20880" />
```

注解方式：
```java
@Reference(url = "127.0.0.1:20880")
UserService userService;
```

# Hystrix 断路器

## 服务降级

服务降级概念：  
当服务器压力剧增的情况下，根据当前业务情况及流量对一些服务和页面有策略的降级（返回一个友好提示给客户端，不去执行主业务逻辑，调用fallBack本地方法），以此释放服务器资源以保证核心任务的正常运行。

## 服务熔断

服务熔断概念：  
我们在各种场景下都会接触到熔断这两个字。高压电路中，如果某个地方的电压过高，熔断器就会熔断，对电路进行保护。股票交易中，如果股票指数过高，也会采用熔断机制，暂停股票的交易。

同样，在微服务架构中，熔断机制也是起着类似的作用。当扇出链路的某个微服务调用慢或者有大量超时，会进行服务的降级，进而熔断该节点微服务的调用，快速返回错误的响应信息。当检测到该节点微服务调用响应正常后，恢复调用链路。

服务熔断机制和服务降级是一起使用。

# Dubbo原理

## RPC原理

## Dubbo原理

