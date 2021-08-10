---
title: Dubbo-基础
date: 2021-06-25 00:00:00
author: 神奇的荣荣
summary: ""
categories: Dubbo
tags: 
    - Dubbo
    - 分布式
---

# 扩展知识

## 什么是分布式系统

分布式系统原理与泛型中定义：  
分布式系统是n多个计算机的集合，这些计算机对用户来说就像单个相关系统

随着互联网的发展，网站应用的规模不断扩大，常规的垂直应用架构已无法应用，分布式服务架构以及流动计算架构势在必行，需要一个治理系统确保架构有条不紊的演进。

<!-- more -->

## 发展演变

![架构发展演变](https://rong0624.github.io/images/Dubbo/架构发展演变.png)

单一应用架构：  
当网站流量很小时，只需一个应用，将所有功能都部署在一起，以减少部署节点和成本。此时，用于简化增删改查工作量的 数据访问框架(ORM) 是关键。

垂直应用架构：  
当访问量逐渐增大，单一应用增加机器带来的加速度越来越小，将应用拆成互不相干的几个应用，以提升效率。   
此时，用于加速前端页面开发的 Web框架(MVC) 是关键。

分布式服务架构：  
当垂直应用越来越多，应用之间交互不可避免，将核心业务抽取出来，作为独立的服务，逐渐形成稳定的服务中心，使前端应用能更快速的响应多变的市场需求。  
此时，用于提高业务复用及整合的 分布式服务框架(RPC) 是关键。

流动计算架构：  
当服务越来越多，容量的评估，小服务资源的浪费等问题逐渐显现，此时需增加一个调度中心基于访问压力实时管理集群容量，提高集群利用率。 
此时，用于 提高机器利用率的资源调度和治理中心(SOA) 是关键。

# Dubbo入门

## 简介

Dubbo 是一款高性能、轻量级的开源Java RPC框架。  
Dubbo 是分布式服务治理框架。

提供了三大核心能力：  
面向接口的远程方法调用  
集群容错（容错与负债均衡）  
服务自动注册与发现

官网：http://dubbo.apache.org/

## Dubbo能做什么

问题:  
服务的URL管理非常困难(rmi://、http:*、)、F5负载均衡器的单点压力(硬件成本)  
各个服务之间依赖管理非常复杂  
各个服务之间如何进行监控

（1）面向接口的远程方法调用：  
就像调用本地方法一样调用远程方法，只需简单  配置，没有任何API侵入。

（2）智能容错与负债均衡：  
可在内网替代F5等硬件负鞭均衡器，降低成本，减少单点。

（3）服务自动注册与发现：  
不再需要写死服务提供方地址，基于注册中心查询服务提供者的IP地址，并且能够平滑添加或删除服务提供者。

（4）服务监控：  
监控消费者与提供者的累计调用次数和调用时间，提供数据可以分析瓶颈。

## Dubbo架构

![dubbo架构图](https://rong0624.github.io/images/Dubbo/dubbo架构图.png)  

### 节点角色说明

服务提供者（Provider）：  
暴露服务的服务提供方，服务提供者在启动时，向注册中心注册自己提供的服务。

服务消费者（Consumer）:   
调用远程服务的服务消费方，服务消费者在启动时，向注册中心订阅自己所需的服务，服务消费者，从提供者地址列表中，基于软负载均衡算法，选一台提供者进行调用，如果调用失败，再选另一台调用。

注册中心（Registry）：  
注册中心返回服务提供者地址列表给消费者，如果有变更，注册中心将基于长连接推送变更数据给消费者

监控中心（Monitor）：  
服务消费者和提供者，在内存中累计调用次数和调用时间，定时每分钟发送一次统计数据到监控中心

### 调用关系说明

（1）服务容器负责启动，加载，运行服务提供者。  
（2）服务提供者在启动时，向注册中心注册自己提供的服务。  
（3）服务消费者在启动时，向注册中心订阅自己所需的服务。  
（4）注册中心返回服务提供者地址列表给消费者，如果有变更，注册中心将基于长连接推送变更数据给消费者。  
（5）服务消费者，从提供者地址列表中，基于软负载均衡算法，选一台提供者进行调用，如果调用失败，再选另一台调用。  
（6）服务消费者和提供者，在内存中累计调用次数和调用时间，定时每分钟发送一次统计数据到监控中心。

### 特点

Dubbo 架构具有以下几个特点，分别是连通性、健壮性、伸缩性、以及向未来架构的升级性。

#### 连通性

- 注册中心负责服务地址的注册与查找，相当于目录服务，服务提供者和消费者只在启动时与注册中心交互，注册中心不转发请求，压力较小
- 监控中心负责统计各服务调用次数，调用时间等，统计先在内存汇总后每分钟一次发送到监控中心服务器，并以报表展示
- 服务提供者向注册中心注册其提供的服务，并汇报调用时间到监控中心，此时间不包含网络开销
- 服务消费者向注册中心获取服务提供者地址列表，并根据负载算法直接调用提供者，同时汇报调用时间到监控中心，此时间包含网络开销
- 注册中心，服务提供者，服务消费者三者之间均为长连接，监控中心除外
- 注册中心通过长连接感知服务提供者的存在，服务提供者宕机，注册中心将立即推送事件通知消费者
- 注册中心和监控中心全部宕机，不影响已运行的提供者和消费者，消费者在本地缓存了提供者列表
- 注册中心和监控中心都是可选的，服务消费者可以直连服务提供者

#### 健壮性

- 监控中心宕掉不影响使用，只是丢失部分采样数据
- 数据库宕掉后，注册中心仍能通过缓存提供服务列表查询，但不能注册新服务
- 注册中心对等集群，任意一台宕掉后，将自动切换到另一台
- 注册中心全部宕掉后，服务提供者和服务消费者仍能通过本地缓存通讯
- 服务提供者无状态，任意一台宕掉后，不影响使用
- 服务提供者全部宕掉后，服务消费者应用将无法使用，并无限次重连等待服务提供者恢复

#### 伸缩性

- 注册中心为对等集群，可动态增加机器部署实例，所有客户端将自动发现新的注册中心
- 服务提供者无状态，可动态增加机器部署实例，注册中心将推送新的服务提供者信息给消费者

#### 升级性

当服务集群规模进一步扩大，带动IT治理结构进一步升级，需要实现动态部署，进行流动计算，现有分布式服务架构不会带来阻力。

下图是未来可能的一种架构：  
![dubbo未来可能的架构](https://rong0624.github.io/images/Dubbo/1627982970159.jpg)

# Hello world

## 环境准备

Dubbo学习前必须掌握以下内容：  
Zookeeper的使用经验  
Spring框架的使用经验  
熟悉使用Maven进行项目构建和依赖管理  
熟练使用eclipse或Idea开发工具

环境约束：  
Jdk8  
Maven3.x  
Idea2019  
Dubbo 2.7.7  
Zookeeper 3.4.11

注册中心选型，我们使用zookeeper当注册中心，需要提前安装好，这里就不演示了。

## 提出需求

某个电商系统，订单服务需要调用用户服务获取某个用户的所有地址；

我们现在需要创建两个服务模块进行测试：  
订单服务模块在A服务器，用户服务模块在B服务器，A可以远程调用B的功能。  
order-web（订单模块，web，服务消费者）  
user-service（用户模块，service，服务提供者）

## 工程架构

根据 dubbo《服务化最佳实践》 

### 分包

建议将服务接口，服务模型，服务异常等均放在 API 包中，因为服务模型及异常也是 API 的一部分，同时，这样做也符合分包原则：重用发布等价原则(REP)，共同重用原则(CRP)。

如果需要，也可以考虑在 API 包中放置一份 spring 的引用配置，这样使用方，只需在 spring 加载过程中引用此配置即可，配置建议放在模块的包目录下，以免冲突，如：com/alibaba/china/xxx/dubbo-reference.xml。

### 粒度

服务接口尽可能大粒度，每个服务方法应代表一个功能，而不是某功能的一个步骤，否则将面临分布式事务问题，Dubbo 暂未提供分布式事务支持。  
服务接口建议以业务场景为单位划分，并对相近业务做抽象，防止接口数量爆炸。  
不建议使用过于抽象的通用接口，如：Map query(Map)，这样的接口没有明确语义，会给后期维护带来不便。

### 结构

user模块：  
user-api 提供接口  
user-impl 接口实现层，服务提供者


order模块：  
order-web：服务消费者

## 服务提供者

user项目总览：  
创建user项目，这是一个聚合工程。  
创建user-api，这是user公共接口层（提供接口）。  
创建user-imp，这是user接口的实现层（服务提供者）。

### user-api（提供user公共接口）

（1）用户地址DTO：
```java
public class UserAddressDto implements Serializable {

    private Integer id;
    private String userId;
    private String userAddress;
    private String consignee;
    private String phone;
    private String isDefault;
}
```

（2）用户地址接口定义
```java
public interface UserService {
    List<UserAddressDto> getUserAddress(String userId);
}
```

### user-impl（接口实现，服务提供者）

（1）pom.xml导入依赖：
```xml
<dependencies>
    <!-- 依赖user-api -->
    <dependency>
        <groupId>com.oyr</groupId>
        <artifactId>user-api</artifactId>
        <version>${parent.version}</version>
    </dependency>

    <!-- dubbo -->
    <dependency>
        <groupId>org.apache.dubbo</groupId>
        <artifactId>dubbo</artifactId>
        <version>2.7.7</version>
    </dependency>
    <!--
        由于我们使用zookeeper作为注册中心，所以需要操作zookeeper
        dubbo 2.6以前的版本引入zkclient操作zookeeper
        dubbo 2.6及以后的版本引入curator操作zookeeper
        下面两个zk客户端根据dubbo版本2选1即可
    -->
    <dependency>
        <groupId>org.apache.curator</groupId>
        <artifactId>curator-recipes</artifactId>
        <version>2.12.0</version>
    </dependency>
</dependencies>
```

（2）实现用户地址接口：
```java
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

        map = new HashMap<>();
        map.put("1", list1);
        map.put("2", list2);
    }

    @Override
    public List<UserAddressDto> getUserAddress(String userId) {
        return map.get(userId);
    }

}
```

（3）服务提供者配置：  
新增provider.xml文件
```xml
<?xml version="1.0" encoding="utf-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:dubbo="http://code.alibabatech.com/schema/dubbo"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
       http://code.alibabatech.com/schema/dubbo
       http://code.alibabatech.com/schema/dubbo/dubbo.xsd ">

    <!-- 提供方应用信息，用于计算依赖关系 -->
    <dubbo:application name="user-provider" />

    <!-- 指定注册中心地址，使用zookeeper暴露服务地址 -->
    <dubbo:registry address="zookeeper://127.0.0.1:2181" />

    <!-- 用dubbo协议，将服务暴露在20880端口 -->
    <dubbo:protocol name="dubbo" port="20880" />

    <!-- 声明需要暴露的服务接口 -->
    <dubbo:service interface="com.oyr.user.service.UserService"
                   ref="userServiceImpl" />

    <!-- 将接口实现类提交到容器中 -->
    <bean id="userServiceImpl" class="com.oyr.user.service.impl.UserServiceImpl" />
</beans>
```

（4）启动服务提供者
```java
public class Provider {

    public static void main(String[] args) throws Exception {
        ClassPathXmlApplicationContext context =
                new ClassPathXmlApplicationContext("classpath:provider.xml");
        System.in.read();
    }

}
```
注意：服务提供者不要关闭，服务消费者需要调用服务提供者，如果服务提供者不存在，那么服务消费者会报错。

## 服务消费者

（1）pom.xml导入依赖
```xml
<!-- 依赖user-api -->
<dependency>
    <groupId>com.oyr</groupId>
    <artifactId>user-api</artifactId>
    <version>${parent.version}</version>
</dependency>

<!-- dubbo -->
<dependency>
    <groupId>org.apache.dubbo</groupId>
    <artifactId>dubbo</artifactId>
    <version>2.7.7</version>
</dependency>
<!--
    由于我们使用zookeeper作为注册中心，所以需要操作zookeeper
    dubbo 2.6以前的版本引入zkclient操作zookeeper
    dubbo 2.6及以后的版本引入curator操作zookeeper
    下面两个zk客户端根据dubbo版本2选1即可
-->
<dependency>
    <groupId>org.apache.curator</groupId>
    <artifactId>curator-recipes</artifactId>
    <version>2.12.0</version>
</dependency>
```

（2）服务消费者配置  
新增consumer.xml文件
```xml
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:dubbo="http://code.alibabatech.com/schema/dubbo"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://code.alibabatech.com/schema/dubbo http://code.alibabatech.com/schema/dubbo/dubbo.xsd">

    <!-- 消费方应用名，用于计算依赖关系，不要与提供方一样 -->
    <dubbo:application name="order-consumer" />

    <!-- 指定注册中心，通过注册中心发现服务提供者地址 -->
    <dubbo:registry protocol="zookeeper" address="zookeeper://127.0.0.1:2181" />

    <!-- 生成远程服务代理，可以和本地bean一样使用userService -->
    <dubbo:reference id="userService" interface="com.oyr.user.service.UserService" />

</beans>
```

（3）启动消费者，尝试调用服务提供者
```java
public class Consumer {

    public static void main(String[] args) {
        ClassPathXmlApplicationContext context =
                new ClassPathXmlApplicationContext("classpath:consumer.xml");
        UserService userService = context.getBean(UserService.class);
        List<UserAddressDto> result = userService.getUserAddress("1");
        result.forEach(System.out::println);
    }

}
```

在order-web（服务消费者），调用user（服务提供者）的UserService.getUserAddress方法，成功打印出用户地址列表信息，调用成功。  
说明服务提供者（order-web）成功消费服务提供者（user），远程调用成功。

## 注解版

@DubboComponentScan  
使用dubbo提供的DubboComponentScan注解，指定扫描的包。  
会扫描当前包和子包下的@DubboService & @DubboReference并让其生效。

@DubboService  
使用dubbo提供的DubboService注解，指定服务提供者暴露该服务

@DubboReference  
使用dubbo提供的DubboReference注解，指定服务消费者引用远程服务

# 整合Spring Boot

基于hello world项目改造成spring boot项目。

## 环境准备

环境约束：  
Jdk8  
Maven3.x  
Idea2019  
Dubbo 2.7.7  
Zookeeper 3.4.11  
Spring Boot 2.2.4.RELEASE

## 服务提供者改造

user-api不改动，改动user-impl，以下改动都是user-impl的。

（1）pom.xml 导入依赖
```xml
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-parent</artifactId>
            <version>2.2.4.RELEASE</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>

<dependencies>
    <!-- 依赖user-api -->
    <dependency>
        <groupId>com.oyr</groupId>
        <artifactId>user-api</artifactId>
        <version>${parent.version}</version>
    </dependency>

    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter</artifactId>
    </dependency>

    <!-- dubbo -->
    <dependency>
        <groupId>org.apache.dubbo</groupId>
        <artifactId>dubbo-spring-boot-starter</artifactId>
        <version>2.7.7</version>
    </dependency>

    <!-- 操作zookeeper -->
    <dependency>
        <groupId>org.apache.curator</groupId>
        <artifactId>curator-recipes</artifactId>
        <version>2.12.0</version>
    </dependency>
</dependencies>
```

（2）修改接口实现UserServiceImpl （类上加入DubboService）
```java
@DubboService // 暴露当前服务
public class UserServiceImpl implements UserService {
}
```

（3）application.properties新增配置文件
```properties
#dubbo 配置
dubbo.application.name=user-provider
dubbo.registry.protocol=zookeeper
dubbo.registry.address=127.0.0.1:2181
dubbo.protocol.name=dubbo
dubbo.protocol.port=20888
```

（4）修改启动类，并且启动服务提供者
```java
// 开启Dubbo，扫描Dubbo注解（扫描当前包和子包内容）
@EnableDubbo
@SpringBootApplication
public class Provider {

    public static void main(String[] args) {
        SpringApplication.run(Provider.class, args);
    }

}
```

修改后，启动成功！！！

## 服务消费者改造

改动order-web（服务消费者）

（1）pom.xml 导入依赖
```xml
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-parent</artifactId>
            <version>2.2.4.RELEASE</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>

<dependencies>
    <!-- 依赖user-api -->
    <dependency>
        <groupId>com.oyr</groupId>
        <artifactId>user-api</artifactId>
        <version>1.0-SNAPSHOT</version>
    </dependency>

    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter</artifactId>
    </dependency>

    <!-- dubbo -->
    <dependency>
        <groupId>org.apache.dubbo</groupId>
        <artifactId>dubbo-spring-boot-starter</artifactId>
        <version>2.7.7</version>
    </dependency>
    <!-- 操作zookeeper -->
    <dependency>
        <groupId>org.apache.curator</groupId>
        <artifactId>curator-recipes</artifactId>
        <version>2.12.0</version>
    </dependency>
</dependencies>
```

（2）新增controller
```java
@RestController
public class DubboController {

    @DubboReference
    private UserService userService;

    @GetMapping("/dubbo")
    public void dubbo() {
        List<UserAddressDto> result = userService.getUserAddress("1");
        result.forEach(System.out::println);
    }

}
```

（3）application.properties新增配置文件
```properties
#dubbo 配置
dubbo.application.name=cms-wms
dubbo.registry.protocol=zookeeper
dubbo.registry.address=127.0.0.1:2181
dubbo.protocol.name=dubbo
dubbo.consumer.client=netty
dubbo.consumer.check=false
```

（4）修改启动类
```java
@EnableDubbo // 开启dubbo，扫描dubbo注解
@SpringBootApplication
public class Consumer {

    public static void main(String[] args) {
        SpringApplication.run(Consumer.class, args);
    }

}
```

（5）测试，尝试远程调用是否成功  

访问http://localhost:8080/dubbo  
后台成功打印出客户地址列表  
远程调用成功！！！！

## 配置解释

@EnableDubbo（组合型注解）  
开启dubbo，扫描dubbo注解。  
里面有dubbo提供的DubboComponentScan注解，指定扫描的包。  
会扫描当前包和子包下的@DubboService & @DubboReference并让其生效。

@DubboService  
使用dubbo提供的DubboService注解，指定服务提供者暴露该服务

@DubboReference  
使用dubbo提供的DubboReference注解，指定服务消费者引用远程服务

服务提供者配置：
```properties
#dubbo 配置
dubbo.application.name=user-provider
dubbo.registry.protocol=zookeeper
dubbo.registry.address=127.0.0.1:2181
dubbo.protocol.name=dubbo
dubbo.protocol.port=20888
```

服务消费者配置：
```properties
#dubbo 配置
dubbo.application.name=order-consumer
dubbo.registry.protocol=zookeeper
dubbo.registry.address=127.0.0.1:2181
dubbo.protocol.name=dubbo
```

配置解释：  
dubbo.application.nam：就是服务名，不能和其他服务提供者重复  
dubbo.registry.protocol：指定注册中心协议  
dubbo.registry.address：指定注册中心访问地址（地址加端口号）  
dubbo.protocol.name：指定使用协议，默认是dubbo  
dubbo.protocol.port：指定服务提供者暴露的端口

# Dubbo 配置详解

注意：以下针对xml配置讲解，注解版差不多一致。

## 重试次数

失败自动重试：  
当出现失败，重试其他服务提供者服务器，重试会带来更长的延迟，可通过’retries=2’来设置重试次数（不含第一次），’retries=-1’表示不进行重试。

### 服务提供者

```xml
<!-- 提供者全局配置重试 -->
<dubbo:provider retries="2" />

<!-- 提供者指定接口重试 -->
<dubbo:service retries="2" />

<!-- 提供者指定接口某个方法设置重试 -->
<dubbo:service interface="com.oyr.user.service.UserService" ref="userServiceImpl" >
    <dubbo:method name="getUserAddress" retries="2" />
</dubbo:service>
```

### 服务消费者

```xml
<!-- 服务消费者全局配置重试 -->
<dubbo:consumer retries="2" />

<!-- 服务消费者指定接口设置重试 -->
<dubbo:reference id="userService" interface="com.oyr.user.service.UserService" retries="2"/>
```

## 超时时间

由于网络或服务端不可靠，会导致调用出现一种不确定的中间状态（超时）。为了避免超时导致客户端资源（线程）挂起耗尽，必须设置超时时间。

### 服务提供者

```xml
<!-- 提供者全局配置超时时间 -->
<dubbo:provider timeout="5000" />

<!-- 提供者指定接口以及某个方法配置超时时间 -->
<dubbo:service interface="com.oyr.user.service.UserService" ref="userServiceImpl" timeout="5000" >
    <dubbo:method name="getUserAddress" timeout="10000" />
</dubbo:service>
```

### 服务消费者

```xml
<!-- 消费者全局配置超时时间 -->
<dubbo:consumer timeout="3000"/>

<!-- 消费者指定接口和某个方法配置超时时间 -->
<dubbo:reference id="userService" interface="com.oyr.user.service.UserService" timeout="5000">
    <dubbo:method name="getUserAddress" timeout="10000"/>
</dubbo:reference>
```

## 版本号

当一个接口实现，出现不兼容升级时，可以用版本号过渡，版本号不同的服务相互间不引用。

可以按照以下的步骤进行版本迁移：  
在低压力时间段，先升级一半提供者为新版本  
再将所有消费者升级为新版本  
然后将剩下的一半提供者升级为新版本

```xml
老版本服务提供者配置：
<dubbo:service interface="com.foo.BarService" version="1.0.0" />

新版本服务提供者配置：
<dubbo:service interface="com.foo.BarService" version="2.0.0" />

老版本服务消费者配置：
<dubbo:reference id="barService" interface="com.foo.BarService" version="1.0.0" />

新版本服务消费者配置：
<dubbo:reference id="barService" interface="com.foo.BarService" version="2.0.0" />

如果不需要区分版本，可以按照以下的方式配置：
<dubbo:reference id="barService" interface="com.foo.BarService" version="*" />
```

## 配置原则

Dubbo推荐在Provider上尽量多配置Consumer端属性。  
1）作服务的提供者，比服务使用方更清楚服务性能参数，如调用的超时时间，合理的重试次数，等等  
2）在Provider配置后，Consumer不配置则会使用Provider的配置值，即Provider配置可以作为Consumer的缺省值。否则，Consumer会使用Consumer端的全局设置，这对于Provider不可控的，并且往往是不合理的

## 属性配置优先级

![属性配置优先级](https://rong0624.github.io/images/Dubbo/属性配置覆盖规则.png)

1）方法级配置别优于接口级别，接口级别优于全局配置，即小Scope优先 
2）Consumer端配置优于 Provider配置
3）最后是Dubbo Hard Code的配置值（见配置文档）

## 配置文件优先级

![配置文件优先级](https://rong0624.github.io/images/Dubbo/配置文件覆盖规则.png)

1）JVM 启动 -D 参数优先，这样可以使用户在部署和启动时进行参数重写，比如在启动时需改变协议的端口。  
2）XML 次之，如果在 XML 中有配置，则 dubbo.properties 中的相应配置项无效。  
3）Properties 最后，相当于缺省值，只有 XML 没有配置时，dubbo.properties 的相应配置项才会生效，通常用于共享公共配置，比如应用名。

# Dubbo-Admin监控中心

## 监控中心简介

监控中心：
图形化的访问管理页面，安装时需要指定注册中心的地址，即可从注册中心中获取到所以的提供者和消费者进行配置管理，并且可以监控与分析提供者与消费者中的远程调用。

监控中心：
是为了让用户更好的管理监控众多的dubbo服务，官方提供了一个可视化的监控测试程序，不过这个监控即使不装也不影响使用。

## 搭建监控中心

注意：dubbo-admin有多种启动方式  
通过jar包方式启动，jar包形式。  
通过war包方式启动，依赖tomcat容器。  
通过导入eclipse或idea启动

最新的dubbo-admin是前后端分离的（新版本），可以看教程：  
https://blog.csdn.net/muriyue6/article/details/109304584

**注意：当前讲解的是前后端不分离的dubbo-admin搭建方式（老版本）**

下载dubbo-admin项目：https://github.com/apache/incubator-dubbo-ops
![incubator-dubbo-ops](https://rong0624.github.io/images/Dubbo/incubator-dubbo-ops.png)

### war包启动

1）先准备好dubbo-admin项目

2）准备好tomcat，将dubbo-admin放到webapps目录下

3）修改dubbo.properties文件内容
```properties
# 指定注册中心地址
dubbo.registry.address=zookeeper://120.25.217.122:2181
# 指定root账号密码
dubbo.admin.root.password=root123
# 指定guest账号密码
dubbo.admin.guest.password=guest123
```

4）启动tomcat测试，尝试访问监控中心  
在tomcat下，bin目录，通过startup.bat启动tomcat。  
访问http://localhost:8080/dubbo-admin  
![dubbo监控中心登录页面](https://rong0624.github.io/images/Dubbo/dubbo监控中心登录页面.png)  
注意：登录时遇到需要密码来登录，密码设置在dubbo.properties文件中设置。  
![dubbo监控中心页面](https://rong0624.github.io/images/Dubbo/dubbo监控中心页面.png)  
最后成功进入监控中心界面！！！

### jar包启动

1）先准备好dubbo-admin项目

2）修改application.properties配置
```properties
# 指定注册中心地址
dubbo.registry.address=zookeeper://120.25.217.122:2181
# 指定root账号密码
dubbo.admin.root.password=root
# 指定guest账号密码
dubbo.admin.guest.password=guest
```

3）打包dubbo-admin
mvn clean package -Dmaven.test.skip=true

4）运行dubbo-admin.jar，尝试访问监控中心  
java -jar dubbo-admin-0.0.1-SNAPSHOT.jar  
使用root/root登录  
![dubbo监控中心页面](https://rong0624.github.io/images/Dubbo/dubbo监控中心页面.png)

# 通讯协议


## Dubbo支持哪些协议

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

## 多协议

Dubbo 允许配置多协议，在不同服务上支持不同协议 或者 同一服务上同时支持多种协议。

### 不同服务上不同协议

不同服务在性能上适用不同协议进行传输，比如大数据用短连接协议，小数据大并发用长连接协议；

```xml

<dubbo:application name="world"  />
<dubbo:registry id="registry" address="10.20.141.150:9090" username="admin" password="hello1234" />

<!-- 多协议配置 -->
<dubbo:protocol name="dubbo" port="20880" />
<dubbo:protocol name="rmi" port="1099" />

<!-- 使用dubbo协议暴露服务 -->
<dubbo:service interface="com.alibaba.hello.api.HelloService" version="1.0.0" ref="helloService" protocol="dubbo" />
<!-- 使用rmi协议暴露服务 -->
<dubbo:service interface="com.alibaba.hello.api.DemoService" version="1.0.0" ref="demoService" protocol="rmi" /> 
```

### 同一服务上不同协议

同一服务上同时支持多种协议；

```xml
<dubbo:application name="world"  />
<dubbo:registry id="registry" address="10.20.141.150:9090" username="admin" password="hello1234" />

<!-- 多协议配置 -->
<dubbo:protocol name="dubbo" port="20880" />
<dubbo:protocol name="hessian" port="8080" />

<!-- 使用多个协议暴露服务 -->
<dubbo:service id="helloService" interface="com.alibaba.hello.api.HelloService" version="1.0.0" protocol="dubbo,hessian" />
```

# 序列化

## Dubbo支持哪些序列化

![支持的序列化](https://rong0624.github.io/images/Dubbo/1627984641381.jpg)
Dubbo 支持 Hession，Dubbo，Json、Java 多种序列化方式。但是 Hessian 是其默认的序列化方式。

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

# 通讯框架

## Dubbo支持哪些通讯框架

![支持的通讯框架](https://rong0624.github.io/images/Dubbo/1627983850471.jpg)  
Dubbo 支持 Netty、Mina、Grizzly 多种通讯框架，Dubbo推荐并默认使用 Netty。

# 注册中心

## Dubbo支持哪些注册中心

![支持的注册中心](https://rong0624.github.io/images/Dubbo/1627983532373.jpg)  
Zookeeper、Redis、Multicast、Simple 都可以作为Dubbo的注册中心，Dubbo官方推荐使用 Zookeeper。

## 多注册中心

Dubbo 支持同一服务向多注册中心同时注册，或者不同服务分别注册到不同的注册中心上去，甚至可以同时引用注册在不同注册中心上的同名服务。  
另外，注册中心是支持自定义扩展的。

### 多注册中心注册

案例：中文站有些服务来不及在青岛部署，只在杭州部署，而青岛的其它应用需要引用此服务，就可以将服务同时注册到两个注册中心。

```xml
<dubbo:application name="world"  />

<!-- 多注册中心配置 -->
<dubbo:registry id="hangzhouRegistry" address="10.20.141.150:9090" />
<dubbo:registry id="qingdaoRegistry" address="10.20.141.151:9010" default="false" />

<!-- 向多个注册中心注册 -->
<dubbo:service interface="com.alibaba.hello.api.HelloService" version="1.0.0" ref="helloService" registry="hangzhouRegistry,qingdaoRegistry" />
```

### 不同服务使用不同注册中心

CRM 有些服务是专门为国际站设计的，有些服务是专门为中文站设计的。

```xml
<dubbo:application name="world"  />

<!-- 多注册中心配置 -->
<dubbo:registry id="chinaRegistry" address="10.20.141.150:9090" />
<dubbo:registry id="intlRegistry" address="10.20.154.177:9010" default="false" />

<!-- 向中文站注册中心注册 -->
<dubbo:service interface="com.alibaba.hello.api.HelloService" version="1.0.0" ref="helloService" registry="chinaRegistry" />
<!-- 向国际站注册中心注册 -->
<dubbo:service interface="com.alibaba.hello.api.DemoService" version="1.0.0" ref="demoService" registry="intlRegistry" />
```

### 多注册中心引用

案例：CRM 需同时调用中文站和国际站的 PC2 服务，PC2 在中文站和国际站均有部署，接口及版本号都一样，但连的数据库不一样。

```xml
<dubbo:application name="world"  />

<!-- 多注册中心配置 -->
<dubbo:registry id="chinaRegistry" address="10.20.141.150:9090" />
<dubbo:registry id="intlRegistry" address="10.20.154.177:9010" default="false" />

<!-- 引用中文站服务 -->
<dubbo:reference id="chinaHelloService" interface="com.alibaba.hello.api.HelloService" version="1.0.0" registry="chinaRegistry" />
<!-- 引用国际站站服务 -->
<dubbo:reference id="intlHelloService" interface="com.alibaba.hello.api.HelloService" version="1.0.0" registry="intlRegistry" />
```

如果只是测试环境临时需要连接两个不同注册中心，使用竖号分隔多个不同注册中心地址：
```xml
<dubbo:application name="world"  />

<!-- 多注册中心配置，竖号分隔表示同时连接多个不同注册中心，同一注册中心的多个集群地址用逗号分隔 -->
<dubbo:registry address="10.20.141.150:9090|10.20.154.177:9010" />
<!-- 引用服务 -->
<dubbo:reference id="helloService" interface="com.alibaba.hello.api.HelloService" version="1.0.0" />
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