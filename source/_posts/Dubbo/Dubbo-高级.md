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
广播调用所有提供者，逐个调用，任意一台报错则报错。通常用于通知所有提供者更新缓存或日志等本地资源信息。
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

# Dubbo SPI 机制

## SPI 是什么？

SPI（service provider interface）。  
SPI 就是通过动态加载机制实现面向接口编程;  
SPI 是Java提供的一套用来被第三方实现或者扩展的接口，它可以用来启用框架扩展和替换组件;

SPI 一般用在哪儿？  
主要在框架中使用，用于插件扩展的场景，比如说你开发了一个给别人使用的开源框架，如果你想让别人自己写个插件，插到你的开源框架里面，从而扩展某个功能，这个时候 SPI 思想就用上了。

举个例子：你有一个接口 A。A1/A2/A3 分别是接口A的不同实现。你通过配置 接口 A = 实现 A2，那么在系统实际运行的时候，会加载你的配置，用实现 A2 实例化一个对象来提供服务。

## API 和 SPI 的区别

API （Application Programming Interface）在大多数情况下，都是实现方制定接口并完成对接口的实现，调用方仅仅依赖接口调用，且无权选择不同实现。 从使用人员上来说，API 直接被应用开发人员使用。

SPI （Service Provider Interface）是调用方来制定接口规范，提供给外部来实现，调用方在调用时则选择自己需要的外部实现。  从使用人员上来说，SPI 被框架扩展人员使用。

## Java SPI

### Java SPI 简介

SPI 经典的思想体现，大家平时都在用，比如说 jdbc。
Java 定义了一套 jdbc 的接口，但是 Java 并没有提供 jdbc 的实现类。
但是实际上项目跑的时候，要使用 jdbc 接口的哪些实现类呢？一般来说，我们要根据自己使用的数据库，比如 mysql，你就将 mysql-jdbc-connector.jar 引入进来；oracle，你就将 oracle-jdbc-connector.jar 引入进来。
在系统跑的时候，碰到你使用 jdbc 的接口，他会在底层使用你引入的那个 jar 中提供的实现类。

### Java SPI 实现细节

Java SPI 约定在 Classpath 下的 META-INF/services/ 目录里创建一个**以服务接口命名的文件**，然后**文件里面记录的是此 jar 包提供的具体实现类的全限定名。**  
这样当我们引用了某个jar包的时候，就可以去找这个jar包的META-INF/services/目录，再根据接口名找到文件，然后读取文件里面的内容去进行实现类的加载与实例化。

看个案例：看下MySql是怎么做的：  
![MySql SPI实现1](https://rong0624.github.io/images/Dubbo/MySql_SPI实现1.png)
再看下文件里的内容：  
![MySql SPI实现2](https://rong0624.github.io/images/Dubbo/MySql_SPI实现2.png)

### Java SPI 缺点

Java SPI 在查找扩展实现类的时候遍历 SPI 的配置文件并且将实现类全部实例化，假设一个实现类初始化过程比较消耗资源且耗时，但是你的代码里面又用不上它，这就产生了资源的浪费。

所以说 Java SPI 无法按需加载实现类。

## Dubbo SPI

### Dubbo SPI 简介

Dubbo 也用了 SPI 思想，不过没有用 jdk 的 SPI 机制，是自己实现的一套 SPI 机制。
Dubbo SPI：是按需加载实现类的，按需加载的话首先你得给个名字，通过名字去文件里面找到对应的实现类全限定名然后加载实例化即可。Dubbo 就是这样设计的，配置文件里面存放的是键值对。

我们先来看一下 Dubbo 对配置文件目录的约定，不同于 Java SPI ，Dubbo 分为了三类目录：
- META-INF/services/ 目录：该目录下的 SPI 配置文件是为了用来兼容 Java SPI 。
- META-INF/dubbo/ 目录：该目录存放用户自定义的 SPI 配置文件。
- META-INF/dubbo/internal/ 目录：该目录存放 Dubbo 内部使用的 SPI 配置文件。

注意：Dubbo SPI 除了可以按需加载实现类之外，增加了 IOC 和 AOP 的特性，还有个自适应扩展机制。

### Dubbo SPI 实现细节

注意：当前拿 Dubbo Protocol 来演示。

Protocol 接口，在系统运行的时候，dubbo 会判断一下应该选用这个 Protocol 接口的哪个实现类来实例化对象来使用。
它会去找一个你配置的 Protocol，将你配置的 Protocol 实现类，加载到 jvm 中来，然后实例化对象，就用你的那个 Protocol 实现类就可以了。
```java
@SPI("dubbo")  
public interface Protocol {  
      
    int getDefaultPort();  
  
    @Adaptive  
    <T> Exporter<T> export(Invoker<T> invoker) throws RpcException;  
  
    @Adaptive  
    <T> Invoker<T> refer(Class<T> type, URL url) throws RpcException;  

    void destroy();  
  
}  
```

在 dubbo 自己的 jar 里，在/META_INF/dubbo/internal/com.alibaba.dubbo.rpc.Protocol文件中：
```
dubbo=org.apache.dubbo.rpc.protocol.dubbo.DubboProtocol
injvm=org.apache.dubbo.rpc.protocol.injvm.InjvmProtocol
http=org.apache.dubbo.rpc.protocol.http.HttpProtocol
rmi=org.apache.dubbo.rpc.protocol.rmi.RmiProtocol
hessian=org.apache.dubbo.rpc.protocol.hessian.HessianProtocol
org.apache.dubbo.rpc.protocol.webservice.WebServiceProtocol
thrift=org.apache.dubbo.rpc.protocol.thrift.ThriftProtocol
native-thrift=org.apache.dubbo.rpc.protocol.nativethrift.ThriftProtocol
memcached=org.apache.dubbo.rpc.protocol.memcached.MemcachedProtocol
redis=org.apache.dubbo.rpc.protocol.redis.RedisProtocol
rest=org.apache.dubbo.rpc.protocol.rest.RestProtocol
xmlrpc=org.apache.dubbo.xml.rpc.protocol.xmlrpc.XmlRpcProtocol
grpc=org.apache.dubbo.rpc.protocol.grpc.GrpcProtocol
```

所以说，这就看到了 dubbo 的 SPI 机制默认是怎么玩儿的了，其实就是 Protocol 接口，@SPI("dubbo") 说的是，通过 SPI 机制来提供实现类，实现类是通过 dubbo 作为默认 key 去配置文件里找到的，配置文件名称与接口全限定名一样的，通过 dubbo 作为 key 可以找到默认的实现类就是 com.alibaba.dubbo.rpc.protocol.dubbo.DubboProtocol。

如果想要动态替换掉默认的实现类，需要使用 @Adaptive 接口，Protocol 接口中，有两个方法加了 @Adaptive 注解，就是说那俩接口会被代理实现。
比如这个 Protocol 接口搞了俩 @Adaptive 注解标注了方法，在运行的时候会针对 Protocol 生成代理类，这个代理类的那俩方法里面会有代理代码，代理代码会在运行的时候动态根据 url 中的 protocol 来获取那个 key，默认是 dubbo，你也可以自己指定，你如果指定了别的 key，那么就会获取别的实现类的实例了。

### Dubbo 自定义扩展组件

这里展示一下，如何去扩展 Dubbo Protocol 的组件；

#### 新开一个maven工程（protocol-demo），打包方式为jar；

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.oyr</groupId>
    <artifactId>protocol-demo</artifactId>
    <version>1.0-SNAPSHOT</version>
    <packaging>jar</packaging>

    <dependencies>
        <!-- dubbo -->
        <dependency>
            <groupId>org.apache.dubbo</groupId>
            <artifactId>dubbo-spring-boot-starter</artifactId>
            <version>2.7.7</version>
        </dependency>
    </dependencies>

</project>
```

#### Protocol 自定义实现

以下代码，是将 Dubbo RmiProtocol 的实现复制过来的，当做是自定义的 Protocol 实现
```java
package com.oyr.dubbo.protocol;

import org.apache.dubbo.common.URL;
import org.apache.dubbo.rpc.RpcException;
import org.apache.dubbo.rpc.protocol.AbstractProxyProtocol;
import org.apache.dubbo.rpc.protocol.rmi.RmiRemoteInvocation;
import org.apache.dubbo.rpc.service.GenericService;
import org.apache.dubbo.rpc.support.ProtocolUtils;
import org.springframework.remoting.RemoteAccessException;
import org.springframework.remoting.rmi.RmiProxyFactoryBean;
import org.springframework.remoting.rmi.RmiServiceExporter;
import org.springframework.remoting.support.RemoteInvocation;

import java.io.IOException;
import java.net.SocketTimeoutException;
import java.rmi.RemoteException;

import static org.apache.dubbo.common.Version.isRelease263OrHigher;
import static org.apache.dubbo.common.Version.isRelease270OrHigher;
import static org.apache.dubbo.common.constants.CommonConstants.DUBBO_VERSION_KEY;
import static org.apache.dubbo.common.constants.CommonConstants.RELEASE_KEY;
import static org.apache.dubbo.rpc.Constants.GENERIC_KEY;

public class MyProtocol extends AbstractProxyProtocol {

    public static final int DEFAULT_PORT = 1099;

    public MyProtocol() {
        super(RemoteAccessException.class, RemoteException.class);
        System.out.println("MyProtocol Init");
    }

    @Override
    public int getDefaultPort() {
        return DEFAULT_PORT;
    }

    @Override
    protected <T> Runnable doExport(final T impl, Class<T> type, URL url) throws RpcException {
        RmiServiceExporter rmiServiceExporter = createExporter(impl, type, url, false);
        RmiServiceExporter genericServiceExporter = createExporter(impl, GenericService.class, url, true);
        return new Runnable() {
            @Override
            public void run() {
                try {
                    rmiServiceExporter.destroy();
                    genericServiceExporter.destroy();
                } catch (Throwable e) {
                    logger.warn(e.getMessage(), e);
                }
            }
        };
    }

    @Override
    @SuppressWarnings("unchecked")
    protected <T> T doRefer(final Class<T> serviceType, final URL url) throws RpcException {
        final RmiProxyFactoryBean rmiProxyFactoryBean = new RmiProxyFactoryBean();
        final String generic = url.getParameter(GENERIC_KEY);
        final boolean isGeneric = ProtocolUtils.isGeneric(generic) || serviceType.equals(GenericService.class);
        /*
          RMI needs extra parameter since it uses customized remote invocation object

          The customized RemoteInvocation was firstly introduced in v2.6.3; The package was renamed to 'org.apache.*' since v2.7.0
          Considering the above two conditions, we need to check before sending customized RemoteInvocation:
          1. if the provider version is v2.7.0 or higher, send 'org.apache.dubbo.rpc.protocol.rmi.RmiRemoteInvocation'.
          2. if the provider version is v2.6.3 or higher, send 'com.alibaba.dubbo.rpc.protocol.rmi.RmiRemoteInvocation'.
          3. if the provider version is lower than v2.6.3, does not use customized RemoteInvocation.
         */
        if (isRelease270OrHigher(url.getParameter(RELEASE_KEY))) {
            rmiProxyFactoryBean.setRemoteInvocationFactory(methodInvocation -> {
                RemoteInvocation invocation = new RmiRemoteInvocation(methodInvocation);
                if (isGeneric) {
                    invocation.addAttribute(GENERIC_KEY, generic);
                }
                return invocation;
            });
        } else if (isRelease263OrHigher(url.getParameter(DUBBO_VERSION_KEY))) {
            rmiProxyFactoryBean.setRemoteInvocationFactory(methodInvocation -> {
                RemoteInvocation invocation = new com.alibaba.dubbo.rpc.protocol.rmi.RmiRemoteInvocation(methodInvocation);
                if (isGeneric) {
                    invocation.addAttribute(GENERIC_KEY, generic);
                }
                return invocation;
            });
        }
        String serviceUrl = url.toIdentityString();
        if (isGeneric) {
            serviceUrl = serviceUrl + "/" + GENERIC_KEY;
        }
        rmiProxyFactoryBean.setServiceUrl(serviceUrl);
        rmiProxyFactoryBean.setServiceInterface(serviceType);
        rmiProxyFactoryBean.setCacheStub(true);
        rmiProxyFactoryBean.setLookupStubOnStartup(true);
        rmiProxyFactoryBean.setRefreshStubOnConnectFailure(true);
        rmiProxyFactoryBean.afterPropertiesSet();
        return (T) rmiProxyFactoryBean.getObject();
    }

    @Override
    protected int getErrorCode(Throwable e) {
        if (e instanceof RemoteAccessException) {
            e = e.getCause();
        }
        if (e != null && e.getCause() != null) {
            Class<?> cls = e.getCause().getClass();
            if (SocketTimeoutException.class.equals(cls)) {
                return RpcException.TIMEOUT_EXCEPTION;
            } else if (IOException.class.isAssignableFrom(cls)) {
                return RpcException.NETWORK_EXCEPTION;
            } else if (ClassNotFoundException.class.isAssignableFrom(cls)) {
                return RpcException.SERIALIZATION_EXCEPTION;
            }
        }
        return super.getErrorCode(e);
    }

    private <T> RmiServiceExporter createExporter(T impl, Class<?> type, URL url, boolean isGeneric) {
        final RmiServiceExporter rmiServiceExporter = new RmiServiceExporter();
        rmiServiceExporter.setRegistryPort(url.getPort());
        if (isGeneric) {
            rmiServiceExporter.setServiceName(url.getPath() + "/" + GENERIC_KEY);
        } else {
            rmiServiceExporter.setServiceName(url.getPath());
        }
        rmiServiceExporter.setServiceInterface(type);
        rmiServiceExporter.setService(impl);
        try {
            rmiServiceExporter.afterPropertiesSet();
        } catch (RemoteException e) {
            throw new RpcException(e.getMessage(), e);
        }
        return rmiServiceExporter;
    }

}
```

#### Dubbo规定目录下配置

1）在工程 resources 目录下新建 ETA-INF/services 目录；  
2）在 ETA-INF/services 目录下，新建一个 com.alibaba.dubbo.rpc.Protocol 文件；  
3）指定 com.alibaba.dubbo.rpc.Protocol 文件内容：  
```
my=com.oyr.dubbo.protocol.MyProtocol
```

#### 修改服务提供者，使用自定义的 Protocol 实现

1）把自定义实现的jar依赖到服务提供者中

```xml
    <dependency>
        <groupId>com.oyr</groupId>
        <artifactId>protocol-demo</artifactId>
        <version>1.0-SNAPSHOT</version>
    </dependency>
```

2）修改配置，协议使用自定义实现
```properties
#dubbo 配置
dubbo.application.name=user-provider
dubbo.registry.protocol=zookeeper
dubbo.registry.address=127.0.0.1:2181
# 使用自定义实现的协议
dubbo.protocol.name=my
dubbo.protocol.port=20888
```

3）启动服务提供者，查看效果；
启动服务提供者，控制台应该打印出：MyProtocol Init；  
这既代表已经使用了自定义实现的协议；

#### 总结

服务提供者启动的时候，就会加载 my=com.bingo.MyProtocol 这行配置里，接着会根据你的配置使用你定义好的 MyProtocol 了；

dubbo 里面提供了大量的类似上面的扩展点，就是说，你如果要扩展一个东西，只要自己写个 jar，让你的 consumer 或者是 provider 工程，依赖你的那个 jar，在你的 jar 里指定目录下配置好接口名称对应的文件，里面通过 key=实现类。
然后对于对应的组件，类似 <dubbo:protocol> 用你的那个 key 对应的实现类来实现某个接口，你可以自己去扩展 dubbo 的各种功能，提供你自己的实现。

## 参考博客

https://shishan100.gitee.io/docs/#/./docs/distributed-system/dubbo-spi

https://blog.csdn.net/qq_35190492/article/details/108256452?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522162808615116780262585211%2522%252C%2522scm%2522%253A%252220140713.130102334.pc%255Fblog.%2522%257D&request_id=162808615116780262585211&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~blog~first_rank_v2~rank_v29-1-108256452.pc_v2_rank_blog_default&utm_term=spi&spm=1018.2226.3001.4450

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