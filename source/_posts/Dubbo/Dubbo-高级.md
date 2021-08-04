---
title: Dubbo-高级
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

Dubbo集群很简单，只是需要对服务提供者的配置文件进行修改，配置文件中的```dubbo.application.name```相同，Dubbo则会认为是同一集群。

<!-- more -->

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

### 集群容错配置

按照以下示例在服务提供方和消费方配置集群模式
```
<dubbo:service cluster="failsafe" />
或
<dubbo:reference cluster="failsafe" />
```

## 服务降级

### 服务降级概念

当服务器压力剧增的情况下，根据当前业务情况及流量对一些服务和页面有策略的降级（返回一个友好提示给客户端，不去执行主业务逻辑，调用fallBack本地方法），以此释放服务器资源以保证核心任务的正常运行。

### Dubbo 服务降级机制

Dubbo的服务降级采用的是mock机制；即当服务提供者出错时（抛出 RpcException），进行 mock 调用；  
同时也可以用于本地测试，用服务消费者端配置的 mock 服务替代要调用的远程服务，亦或者是对某个服务消费者屏蔽服务提供者，不让其进行远程调用。

其具有两种策略方式：
- fail：当服务消费者调用服务提供者失败后，会去执行配置的 mock 策略。 配置方式为 mock=“fail:策略” 或者 mock=“策略”。
- force：当服务消费者调用服务提供者时，会直接执行 mock 配置的策略，不会进行服务调用。

### fail 策略

当服务消费者调用服务提供者失败后，会去执行配置的 mock 策略。 配置方式为 mock=“fail:策略” 或者 mock=“策略”。

#### mock=“true”

```xml
<dubbo:reference id="userService" interface="com.oyr.user.service.UserService" mock="true" />

<dubbo:reference id="userService" interface="com.oyr.user.service.UserService" mock="fail:true" />
```
解释：指定 mock 策略为布尔类型，且为 true，此时需要消费端提供服务接口的 mock 实现类，该类的包名需要与服务接口一致，且类名格式为 [接口名 + Mock], 即 com.oyr.user.service.UserServiceMock, 当调用远程服务失败后, 就会执行 UserServiceMock的相同方法（远程调用是hello方法，那么即会调用mock的hello方法）, 如果不提供此 mock 类，dubbo 消费端会启动失败。

#### mock=“具体的mock实现类”

```xml
<dubbo:reference id="userService" interface="com.oyr.user.service.UserService" mock="com.oyr.user.service.mock.UserServiceMock" />
or
<dubbo:reference id="userService" interface="com.oyr.user.service.UserService" mock="fail:com.oyr.user.service.mock.UserServiceMock" />
```

解释：指定 mock 策略为具体的 mock 实现类, 当调用远程服务失败时, 就会执行 mock 实现类的相同方法.

#### mock=“抛出自定义异常”

```xml
<dubbo:reference id="userService" interface="com.oyr.user.service.UserService" mock="throw org.apache.dubbo.demo.consumer.exception.CustomException" />
or
<dubbo:reference id="userService" interface="com.oyr.user.service.UserService" mock="fail:throw org.apache.dubbo.demo.consumer.exception.CustomException" />
```

解释：指定 mock 策略为抛出自定义异常, 当远程服务调用失败后, 会给服务消费者抛出自定义异常.

#### mock=“返回 mock 数据”

```xml
<dubbo:reference id="userService" interface="com.oyr.user.service.UserService" mock="return null" />
or
<dubbo:reference id="userService" interface="com.oyr.user.service.UserService" mock="fail:return null" />
```

解释：指定 mock 属性值为返回 mock 数据，当远程服务调用失败后，就会给服务消费者返回null。

### force 策略

当服务消费者调用服务提供者时，会直接执行 mock 配置的策略，不会进行服务调用。
具体的策略跟 fail 策略一致，此处不再详细说明。

#### mock=“force:true”

```xml
<dubbo:reference id="demoService" mock="“force:true" interface="org.apache.dubbo.demo.DemoService"/>
```
#### mock=“force: 执行Mock实现类”

```xml
<dubbo:reference id="demoService" mock="force:com.apache.dubbo.demo.DemoServiceMock2" interface="org.apache.dubbo.demo.DemoService"/>
```

#### mock=“force:抛出自定义异常”

```xml
<dubbo:reference id="demoService" mock="force:throw com.apache.dubbo.demo.XXXException" interface="org.apache.dubbo.demo.DemoService"/>
```

#### mock=“force:返回mock数据”

```xml
<dubbo:reference id="demoService" mock="force:return xxx" interface="org.apache.dubbo.demo.DemoService"/>
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

## Hystrix简介

Hystrix 旨在通过控制那些访问远程系统、服务和第三方库的节点，从而对延迟和故障提供更强大的容错能力。Hystrix具备拥有回退机制和断路器功能的线程和信号隔离，请求缓存和请求打包，以及监控和配置等功能。

## 整合Hystrix

### 提供者改造

1）导入hystrix依赖（spring boot官方提供了对hystrix的集成）  
直接在pom.xml里加入依赖：  
```xml
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-hystrix</artifactId>
        <version>2.2.9.RELEASE</version>
    </dependency>
```
注意：需要找到版本合适的netflix导入（springboot2.2.4.RELEASE & hystrix2.2.9.RELEASE & dubbo2.7.7）

2）修改代码  
修改userServiceImpl  
```java
@DubboService
public class UserServiceImpl implements UserService {

    public final static Map<String, List<UserAddressDto>> map;

    static {
        List<UserAddressDto> list1 = Stream.of(new UserAddressDto(1, "1", "江西赣州", "欧阳", "152*****", false),
                new UserAddressDto(2, "1", "广东深圳", "欧阳", "152*****", true),
                new UserAddressDto(1, "1", "浙江上海", "欧阳", "152*****", false))
                .collect(Collectors.toList());
        List<UserAddressDto> list2 = Stream.of(new UserAddressDto(3, "2", "江西赣州", "东华", "152*****", false),
                new UserAddressDto(4, "2", "广东深圳", "东华", "152*****", true),
                new UserAddressDto(5, "2", "浙江上海", "东华", "152*****", false))
                .collect(Collectors.toList());

        List<UserAddressDto> list3 = Stream.of(new UserAddressDto(6, "3", "江西赣州", "异常用户", "152*****", false))
                .collect(Collectors.toList());
        map = new HashMap<>();
        map.put("1", list1);
        map.put("2", list2);
        map.put("3", list3);
    }

    // 方法出现异常后，调用其他方法进行返回
    @HystrixCommand(fallbackMethod = "getUserAddressError")
    @Override
    public List<UserAddressDto> getUserAddress(String userId) {
        if (Math.random() > 0.5) {
            throw new RuntimeException("错误的参数");
        }
        return map.get(userId);
    }

    private List<UserAddressDto> getUserAddressError(String userId) {
        return map.get("3");
    }
}
```

3）开启hystrix  
在Application类上增加@EnableHystrix来开启：
```java
// 开启Hystrix，进行代理
@EnableHystrix
// 开启Dubbo，扫描Dubbo注解（扫描当前包和子包内容）
@EnableDubbo
@SpringBootApplication
public class Provider {

    public static void main(String[] args) {
        SpringApplication.run(Provider.class, args);
    }

}
```

### 消费者改造

当前是在服务提供者做熔断，所以消费者不需要改造。
（如果需要在消费者中做熔断，改造步骤和提供者一致）

### 启动查看效果

启动提供者和消费者，访问：http://localhost:8080/dubbo

正常访问：  
![服务熔断正常访问](https://rong0624.github.io/images/Dubbo/1627894884676.jpg)

异常访问：  
![服务熔断异常访问](https://rong0624.github.io/images/Dubbo/1627894974462.jpg)

结论：Hystrix有强大的容错能力，无论是超时还是错误，都可以调用备用方法返回。

# Dubbo SPI机制

## 什么是SPI？

SPI（service provider interface）。  
是什么意思呢？比如你有个接口，现在这个接口有 3 个实现类，那么在系统运行的时候对这个接口到底选择哪个实现类呢？这就需要 spi 了，需要根据指定的配置或者是默认的配置，去找到对应的实现类加载进来，然后用这个实现类的实例对象。

举个例子：你有一个接口 A。A1/A2/A3 分别是接口A的不同实现。你通过配置 接口 A = 实现 A2，那么在系统实际运行的时候，会加载你的配置，用实现 A2 实例化一个对象来提供服务。

spi 机制一般用在哪儿？  
主要在框架中使用，用于插件扩展的场景，比如说你开发了一个给别人使用的开源框架，如果你想让别人自己写个插件，插到你的开源框架里面，从而扩展某个功能，这个时候 spi 思想就用上了。

## Java SPI


# RPC

## 什么是RPC

RPC【Remote Procedure Call】是指远程过程调用，是一种进程间通信方式，他是一种技术的思想，而不是规范。  
它允许程序调用另一个地址空间（通常是共享网络的另一台机器上）的过程或函数，而不用程序员显式编码这个远程调用的细节。即程序员无论是调用本地的还是远程的函数，本质上编写的调用代码基本相同。

## RPC模型

![rpc模型](https://rong0624.github.io/images/Dubbo/rpc通讯.png)  

1. Client：客户端；服务调用方。
2. Client Stub：客户端存根；存放服务端地址信息，将客户端的请求信息进行编码，再通过网络传输给发送端 or 接受服务端的响应数据，进行解码。
3. Server：服务端；服务提供方。
4. Server Stub：服务端存根；接收客户端发送的请求信息并解码，然后调用本地服务进行处理，并将处理结果进行编码后返回。

**RPC两个核心模块：通讯（网络传输），序列化（编码，解码）。**

## RPC调用过程

![rpc序列化](https://rong0624.github.io/images/Dubbo/rpc序列化.png)

```
一次完整的RPC调用流程（同步调用，异步另说）如下： 
1）服务消费方以本地调用方式调用服务；（client）
2）client stub接收到调用后负责将方法、参数等组装成能够进行网络传输的消息体；
3）client stub找到服务地址，并将消息发送到服务端；
4）server stub收到消息后进行解码； 
5）server stub根据解码结果调用本地的服务； 
6）server本地服务执行并将结果返回给server stub；
7）server stub将返回结果打包成消息并发送至消费方； 
8）client stub接收到消息，并进行解码； 
9）服务消费方得到最终结果。
RPC框架的目标就是要2~8这些步骤都封装起来，这些细节对用户来说是透明的，不可见的。
```

# Dubbo 底层解析

## Dubbo-架构设计

![dubbo框架设计](https://rong0624.github.io/images/Dubbo/1627979178965.jpg)

### 分类

- Business 实现层
- RPC 远程调用层
- Remoting 网络通讯层

### 各层说明

- service 接口层：给服务提供者和服务消费者来实现的（留给开发人员来实现）
- config 配置层：对外配置接口，以 ServiceConfig, ReferenceConfig 为中心，可以直接初始化配置类，也可以通过 spring 解析配置生成配置类
- proxy 服务代理层：服务接口透明代理，生成服务的客户端 Stub 和服务器端 Skeleton, 以 ServiceProxy 为中心，扩展接口为 ProxyFactory
- registry 注册中心层：封装服务地址的注册与发现，以服务 URL 为中心，扩展接口为 RegistryFactory, Registry, RegistryService
- cluster 路由层：封装多个提供者的路由及负载均衡，并桥接注册中心，以 Invoker 为中心，扩展接口为 Cluster, Directory, Router, LoadBalance
- monitor 监控层：RPC 调用次数和调用时间监控，以 Statistics 为中心，扩展接口为 MonitorFactory, Monitor, MonitorService
- protocol 远程调用层：封装 RPC 调用，以 Invocation, Result 为中心，扩展接口为 Protocol, Invoker, Exporter
- exchange 信息交换层：封装请求响应模式，同步转异步，以 Request, Response 为中心，扩展接口为 Exchanger, ExchangeChannel, ExchangeClient, ExchangeServer
- transport 网络传输层：抽象 mina 和 netty 为统一接口，以 Message 为中心，扩展接口为 Channel, Transporter, Client, Server, Codec
- serialize 数据序列化层：可复用的一些工具，扩展接口为 Serialization, ObjectInput, ObjectOutput, ThreadPool

## Dubbo-启动解析，加载配置

![启动解析](https://rong0624.github.io/images/Dubbo/1627981581425.jpg)

## Dubbo-服务暴露

![服务暴露](https://rong0624.github.io/images/Dubbo/1627981689580.jpg)

## Dubbo-服务引用

![服务引用](https://rong0624.github.io/images/Dubbo/1627981770108.jpg)

## Dubbo-服务调用

![服务调用](https://rong0624.github.io/images/Dubbo/1627981894490.jpg)