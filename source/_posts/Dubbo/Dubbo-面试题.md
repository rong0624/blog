---
title: Dubbo-面试题
date: 2021-08-04 00:00:00
author: 神奇的荣荣
summary: ""
categories: Dubbo
tags: 
    - Dubbo
    - 分布式
    - 面试
---

# 配置

## Dubbo 核心配置有哪些

| 标签 | 用途 | 解释 |
| ---- | ---- | ----  |
| dubbo:service     | 服务配置      | 用于暴露一个服务，定义服务的元信息，一个服务可以用多个协议暴露，一个服务也可以注册到多个注册中心 |
| dubbo:reference   | 引用配置	    | 用于创建一个远程服务代理，一个引用可以指向多个注册中心
| dubbo:protocol    | 协议配置	    | 用于配置提供服务的协议信息，协议由提供方指定，消费方被动接受
| dubbo:application | 应用配置	    | 用于配置当前应用信息，不管该应用是提供者还是消费者
| dubbo:module      | 模块配置      | 用于配置当前模块信息，可选
| dubbo:registry    | 注册中心配置  | 用于配置连接注册中心相关信息
| dubbo:monitor     | 监控中心配置  | 用于配置连接监控中心相关信息，可选
| dubbo:provider    | 提供方配置    | 当 ProtocolConfig 和 ServiceConfig 某属性没有配置时，采用此缺省值，可选
| dubbo:consumer    | 消费方配置    | 当 ReferenceConfig 某属性没有配置时，采用此缺省值，可选
| dubbo:method	    | 方法配置	    | 用于 ServiceConfig 和 ReferenceConfig 指定方法级的配置信息
| dubbo:argument	| 参数配置	    | 用于指定方法参数配置

## Dubbo 配置原则

Dubbo 推荐在 Provider 上尽量多配置 Consumer 端属性。
1）作服务的提供者，比服务使用方更清楚服务性能参数，如调用的超时时间，合理的重试次数，等等
2）在Provider配置后，Consumer 不配置则会使用 Provider 的配置值，即 Provider 配置可以作为 Consumer 的缺省值。否则，Consumer 会使用 Consumer 端的全局设置，这对于 Provider 不可控的，并且往往是不合理的

## Dubbo 属性配置优先级

![属性配置优先级](https://rong0624.gitee.io/images/Dubbo/属性配置覆盖规则.png)

1）方法级配置别优于接口级别，接口级别优于全局配置，即小Scope优先 
2）Consumer端配置优于 Provider配置
3）最后是Dubbo Hard Code的配置值（见配置文档）

## Dubbo 配置文件优先级

![配置文件优先级](https://rong0624.gitee.io/images/Dubbo/配置文件覆盖规则.png)

1）JVM 启动 -D 参数优先，这样可以使用户在部署和启动时进行参数重写，比如在启动时需改变协议的端口。  
2）XML 次之，如果在 XML 中有配置，则 dubbo.properties 中的相应配置项无效。  
3）Properties 最后，相当于缺省值，只有 XML 没有配置时，dubbo.properties 的相应配置项才会生效，通常用于共享公共配置，比如应用名。

# 通讯协议

## Dubbo 支持哪些通讯协议？

### Dubbo 官方文档

支持4种，分别是：Dubbo，Hessian，RMI，HTTP；

### Dubbo 2.7.7

通过分析 dubbo 2.7.7版本协议实现，支持11种；
分别是：Dubbo，Hessian，RMI，HTTP，WebService，Thrift，Memcached，Redis，Rest，XmlRpc，Grpc；

## Dubbo 通讯协议的特点

### Dubbo协议

Dubbo协议采用单一长连接和NIO异步通讯，适合于小数据量大并发的服务调用，以及服务消费者机器数远大于服务提供者机器数的情况。Dubbo缺省协议不适合传送大数据量的服务，比如传文件，传视频等，除非请求量很低。
Dubbo默认使用Dubbo协议；

基于Dubbo的远程调用协议：
连接个数：单连接
连接方式：长连接  
传输协议：TCP  
传输方式：NIO异步传输  
序列化：Hessian二进制序列化  
适用范围：传入传出参数数据包较小（建议小于100K），消费者比提供者个数多，单一消费者无法压满提供者，尽量不要用dubbo协议传输大文件或超大字符串。  
适用场景：常规远程服务方法调用

### Hessian协议

Hessian协议用于集成Hessian的服务，Hessian底层采用Http通讯，采用Servlet暴露服务，Dubbo缺省内嵌Jetty作为服务器实现。  
Hessian是Caucho开源的一个RPC框架：http://hessian.caucho.com，其通讯效率高于WebService和Java自带的序列化。

基于Hessian的远程调用协议:
连接个数：多连接  
连接方式：短连接  
传输协议：HTTP  
传输方式：同步传输  
序列化：Hessian二进制序列化  
适用范围：传入传出参数数据包较大，提供者比消费者个数多，提供者压力较大，可传文件。  
适用场景：页面传输，文件传输，或与原生hessian服务互操作

### RMI协议

Java标准的远程调用协议，采用JDK标准的java.rmi.*实现，阻塞式短连接和JDK标准序列化方式

基于RMI协议的远程调用协议:  
连接个数：多连接  
连接方式：短连接  
传输协议：TCP  
传输方式：同步传输
序列化：Java标准二进制序列化  
适用范围：传入传出参数数据包大小混合，消费者与提供者个数差不多，可传文件。  
适用场景：常规远程服务方法调用，与原生RMI服务互操作

### HTTP协议

此协议采用 spring 的HttpInvoker的功能实现，

基于HTTP的远程调用协议:  
连接个数：多连接  
连接方式：长连接  
连接协议：http  
传输方式：同步传输  
序列化：表单序列化(JSON)  
适用范围：传入传出参数数据包大小混合，提供者比消费者个数多，可用浏览器查看，可用表单或URL传入参数，暂不支持传文件。  
适用场景：需同时给应用程序和浏览器JS使用的服务。

### Thrift协议

基于Thrift实现PRC协议

### Redis协议

基于redis实现RPC协议

### Memcached协议

基于Memcached实现RPC协议

## Dubbo支持服务多协议吗？

Dubbo 支持多协议；  
Dubbo 在不同服务上支持不同协议 或者 同一服务上同时支持多种协议。

# 序列化

## Dubbo 支持哪些序列化

![支持的序列化](https://rong0624.gitee.io/images/Dubbo/1627984641381.jpg)
Dubbo 支持 Hession，Dubbo，Json、Java自带序列化 多种序列化方式。但是 Hessian 是其默认的序列化方式。

## Dubbo 序列化特点

### Hession

是一种跨语言的高效二进制序列化方式。但这里实际不是原生的hessian2序列化，而是阿里修改过的，它是dubbo RPC默认启用的序列化方式。

### Dubbo

是阿里尚未开发成熟的高效java序列化实现，阿里不建议在生产环境使用它。

### Json

目前有两种实现，一种是采用的阿里的fastjson库，另一种是采用dubbo中自己实现的简单json库，但其实现都不是特别成熟，而且json这种文本序列化性能一般不如上面两种二进制序列化。

### Java自带序列化

主要是采用JDK自带的Java序列化实现，性能很不理想。

## Hessian 的数据结构

Hessian 的对象序列化机制有 8 种原始类型：
- 原始二进制数据
- boolean
- 64-bit date（64 位毫秒值的日期）
- 64-bit double
- 32-bit int
- 64-bit long
- null
- UTF-8 编码的 string

另外还包括 3 种递归类型：
- list for lists and arrays
- map for maps and dictionaries
- object for objects

还有一种特殊的类型：
- ref：用来表示对共享对象的引用。

## 什么是 BP？

可能有一些同学比较习惯于 JSON or XML 数据存储格式，对于 Protocol Buffer 还比较陌生。  
Protocol Buffer 其实是 Google 出品的一种轻量并且高效的结构化数据存储格式，性能比 JSON、XML 要高很多。

其实 PB 之所以性能如此好；主要得益于两个；  
第一，它使用 proto 编译器，自动进行序列化和反序列化，速度非常快，应该比 XML 和 JSON 快上了 20~100 倍；  
第二，它的数据压缩效果好，就是说它序列化后的数据量体积小。因为体积小，传输起来带宽和速度上会有优化。

# 通讯框架

## Dubbo 正常哪些通信框架，推荐使用什么？

![支持的通讯框架](https://rong0624.gitee.io/images/Dubbo/1627983850471.jpg)  
Dubbo 支持 Netty、Mina、Grizzly 多种通讯框架，Dubbo 推荐并默认使用 Netty。

# 注册中心

## Dubbo 支持哪些注册中心？

![支持的注册中心](https://rong0624.gitee.io/images/Dubbo/1627983532373.jpg)  
Zookeeper、Redis、Multicast、Simple 都可以作为Dubbo的注册中心，Dubbo官方推荐使用 Zookeeper。

## Dubbo 的注册中心挂掉，服务提供者和服务消费者之间还能通信么？

可以通讯。启动 Dubbo 时，消费者会从注册中心拉取生产者的地址接口等数据，缓存在本地。每次调用时，按照本地存储的地址进行调用。

```
健壮性
监控中心宕掉不影响使用，只是丢失部分采样数据
数据库宕掉后，注册中心仍能通过缓存提供服务列表查询，但不能注册新服务
注册中心对等集群，任意一台宕掉后，将自动切换到另一台
注册中心全部宕掉后，服务提供者和服务消费者仍能通过本地缓存通讯
服务提供者无状态，任意一台宕掉后，不影响使用
服务提供者全部宕掉后，服务消费者应用将无法使用，并无限次重连等待服务提供者恢复
```

## Dubbo 直连模式

Dubbo 的直连模式，可以完全跳过注册中心，直接指定服务提供者的地址进行通讯；

配置方式：
```xml
<dubbo:reference id="userService" 
    interface="com.zang.gmall.service.UserService" url="dubbo://localhost:20880" />
```

## Dubbo 支持多注册中心吗？

Dubbo 支持多注册中心；  
Dubbo 支持同一服务向多注册中心同时注册，或者不同服务分别注册到不同的注册中心上去，甚至可以同时引用注册在不同注册中心上的同名服务。  

# 服务容器

## Dubbo内置了哪几种服务容器？

### Dubbo 官方文档

dubbo 内置了 spring, jetty, log4j 等服务容器；
支持自己扩展服务容器进行加载；

### 2.7.7 版本

dubbo 内置了 spring, log4j, logback 等服务容器；
```
spring=org.apache.dubbo.container.spring.SpringContainer
log4j=org.apache.dubbo.container.log4j.Log4jContainer
logback=org.apache.dubbo.container.logback.LogbackContainer
```

## 服务启动

Dubbo 服务启动只是一个简单的 Main 方法，加载一个简单的 Spring 容器，用于暴露服务。
在服务启动时，调用容器的 start() 方法，在服务停止时调用 stop() 方法。

# 高可用

## Dubbo 负债均衡

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
一致性 Hash 均衡算法，相同参数的请求总是发到同一提供者。
当某一台提供者挂时，原本发往该提供者的请求，基于虚拟节点，平摊到其它提供者，不会引起剧烈变动。算法参见：http://en.wikipedia.org/wiki/Consistent_hashing
缺省只对第一个参数 Hash，如果要修改，请配置 <dubbo:parameter key="hash.arguments" value="0,1" />
缺省用 160 份虚拟节点，如果要修改，请配置 <dubbo:parameter key="hash.nodes" value="320" />
```

## Dubbo 集群容错

```
Failover Cluster
失败自动切换，当出现失败，重试其它服务器。通常用于读操作，但重试会带来更长延迟。可通过 retries="2" 来设置重试次数(不含第一次)。

Failfast Cluster
快速失败，只发起一次调用，失败立即报错。通常用于非幂等性的写操作，比如新增记录。

Failsafe Cluster
失败安全，出现异常时，直接忽略。通常用于写入审计日志等操作。

Failback Cluster
失败自动恢复，后台记录失败请求，定时重发。通常用于消息通知操作。

Forking Cluster
并行调用多个服务器，只要一个成功即返回。通常用于实时性要求较高的读操作，但需要浪费更多服务资源。可通过 forks="2" 来设置最大并行数。

Broadcast Cluster
广播调用所有提供者，逐个调用，任意一台报错则报错。通常用于通知所有提供者更新缓存或日志等本地资源信息。
```

## Dubbo 服务降级

dubbo 服务降级是 mock 机制，即当服务提供者出错时（抛出 RpcException），进行 mock 调用；

有两种策略方式：
- fail：当服务消费者调用服务提供者失败后，会去执行配置的 mock 策略。 配置方式为 mock=“fail:策略” 或者 mock=“策略”。
- force：当服务消费者调用服务提供者时，会直接执行 mock 配置的策略，不会进行服务调用。

# 运维管理

## Dubbo 如何优雅停机？

Dubbo 是通过 JDK 的 ShutdownHook 来完成优雅停机的，所以如果使用kill -9 PID 等强制关闭指令，是不会执行优雅停机的，只有通过 kill PID 时，才会执行。

## Dubbo telnet 命令能做什么？

dubbo 服务发布之后，我们可以利用 telnet 命令进行调试、管理。Dubbo2.0.5 以上版本服务提供端口支持 telnet 命令。

## 服务上线怎么兼容旧版本？

可以用版本号（version）过渡，多个不同版本的服务注册到注册中心，版本号不同的服务相互间不引用。这个和服务分组的概念有一点类似。

# SPI

## SPI 是什么

SPI 全称：Service Provider Interface;  
SPI 就是通过动态加载机制实现面向接口编程;  
SPI 是Java提供的一套用来被第三方实现或者扩展的接口，它可以用来启用框架扩展和替换组件;

SPI 一般用在哪儿？  
主要在框架中使用，用于插件扩展的场景，比如说你开发了一个给别人使用的开源框架，如果你想让别人自己写个插件，插到你的开源框架里面，从而扩展某个功能，这个时候 SPI 思想就用上了。

举个例子：你有一个接口 A。A1/A2/A3 分别是接口A的不同实现。你通过配置 接口 A = 实现 A2，那么在系统实际运行的时候，会加载你的配置，用实现 A2 实例化一个对象来提供服务。

## Dubbo SPI 和 Java SPI 区别？

Java SPI
- JDK 标准的 SPI 会一次性加载所有的扩展实现，如果有的扩展很耗时，但也没用上，很浪费资源。所以只希望加载某个的实现，就不现实了

Dubbo SPI：
- 对 Dubbo 进行扩展，不需要改动 Dubbo 的源码
- 延迟加载，可以一次只加载自己想要加载的扩展实现。
- 增加了对扩展点 IOC 和 AOP 的支持，一个扩展点可以直接 setter 注入其它扩展点。
- Dubbo 的扩展机制能很好的支持第三方 IoC 容器，默认支持 Spring Bean。

# RPC 架构

详情看 Dubbo-高级（RPC模块）

# Dubbo 架构

详情看 Dubbo-高级（Dubbo 底层解析模块）

# 其他

## Dubbo 支持服务降级吗？

dubbo 自己有提供服务降级，但兼容性不是很好。  
可以自己整合服务熔断框架，列如：Hystrix。

## Dubbo 支持链路追踪吗？

dubbo 目前暂时不支持链路追踪。  
可以自己整合链路追踪框架，列如：Skywalking。

## Dubbo 支持分布式事务吗？

dubbo 目前暂时不支持分布式事务。  
可以自己整合分布式事务框架，列如：Seata。