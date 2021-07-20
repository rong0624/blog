---
title: RabbitMQ
date: 2021-07-13 00:00:00
author: 神奇的荣荣
summary: ""
categories: MQ
tags: 
    - MQ
    - 中间件
---

# RabbitMQ

## 简介

RabbitMQ是由erlang语言开发，基于AMQP（Advanced Message Queue 高级消息队列协议）协议实现的消息中间件。  

RabbitMQ 是一个消息中间件：它接受并转发消息。你可以把它当做一个快递站点，当你要发送一个包裹时，
你把你的包裹放到快递站，快递员最终会把你的快递送到收件人那里，按照这种逻辑 RabbitMQ 是一个快递站，
一个快递员帮你传递快件。RabbitMQ 与快递站的主要区别在于，它不处理快件，而是接收，存储和转发消息数据。

RabbitMQ官方地址：http://www.rabbitmq.com

<!-- more -->

## 核心概念

![AMQP模型](https://rong0624.github.io/images/MQ/amqp模型.png)

1）Broker  
表示消息队列服务器实体（一个进程）。  
一个server，接受客户端的连接，上线AMQP实体服务。  

2）Connection  
连接  
应用程序与broker的网络连接，TCP/IP套接字连接。  

3）Channel  
消息通道  
几乎所有的操作都在Channel中进行，Channel是进行消息读写的通道，客户端可以建立对多个  
Channel，每个Channel代表一个会话任务。

4）Message  
消息，消息是不具名的，它由消息头和消息体组成。消息是不透明的，而消息头则由一系列的可选属性组成，这些属性包括routing-key（路由键）> ，priority（相对于其他消优先权），delivery-mode（指出该消息可能需要持久性存储）等

5）Exchange  
交换机，用来接受生产者发送的消息，并将这些消息路由转发到某个队列。

6）Queue  
消息队列，存储消息，用于发送给消费者。  
它是消息的容器，也是消息的终点。一个消息可以投入多个队列。  
消息一直在队列里面，等待消费者连接到这个队列将其取走。

7）Binding  
绑定，消息队列和交换器之间的关联。  
一个绑定就是基于路由键将交换器和消息队列连接起来的路由规则，所以可以将交换器理解成一个由绑定构成的路由表。

8）Routing Key  
路由关键字  
一个消息头，交换机可以用这个消息头决定如何路由某条消息。

9）Publisher  
消息生产者，是一个向交换器发布消息的客户端应用程序（进程）。

10）Consumer  
消息消费者，是一个从消息队列中取得消息的客户端应用程序（进程）。

11）Virtual Host  
虚拟主机

## 核心部分

![核心部分](https://rong0624.github.io/images/RabbitMQ/1626769058993.jpg)

# RabbitMQ的安装

## 相关版本

```
erlang 21.3.x
rabbitmq 3.8.8
```

Erlang rpm下载：  
https://github.com/rabbitmq/erlang-rpm/releases

Rabbitmq rpm下载：  
https://github.com/rabbitmq/rabbitmq-server/releases/download/v3.8.8/rabbitmq-server-3.8.8-1.el7.noarch.rpm

## Linux下安装

### 环境准备

```
linux（CentOS 7.5）
erlang 21.3.x
rabbitmq 3.8.8
```

### 安装Erlang

![erlang版本信息](https://rong0624.github.io/images/MQ/RabbitMQ/erlang版本.png)   
下载erlang时需要注意，要和rabbitmq版本兼容.

1）erlang rpm下载：
https://github.com/rabbitmq/erlang-rpm/releases/download/v21.3.1/erlang-21.3.1-1.el7.x86_64.rpm
erlang-21.3.1-1.el7.x86_64.rpm

2）rpm上传到系统中，安装erlang 
rpm -ivh erlang-21.3-1.el7.x86_64.rpm

3）查看erlang版本
erl -v

### 安装socat

安装Erlang后直接安装RabbitMQ，需要安装socat。

安装socat：  
yum install socat -y

### 安装RabbitMQ

```
1）rabbitmq rpm下载  
https://github.com/rabbitmq/rabbitmq-server/releases/download/v3.8.8/rabbitmq-server-3.8.8-1.el7.noarch.rpm

2）rpm上传到系统中，并安装rabbitmq  
rpm -ivh rabbitmq-server-3.8.8-1.el7.noarch.rpm 

3）启动服务并测试  
# 启动服务 
service rabbitmq-server start

# 查看服务状态
service rabbitmq-server status

4）常用命令

# 启动服务 
service rabbitmq-server start 

# 查看服务状态
service rabbitmq-server status

# 停止服务 
service rabbitmq-server stop

# 重启服务 
service rabbitmq-server restart

# 开机自动启动 
chkconfig rabbitmq-server on
```

## Windos下安装

## Mac下安装

## RabbitMQ管理界面及授权操作

### RabbitMQ管理界面

1）默认情况下，是没有安装web端的客户端插件，需要安装才可以生效。
```shell
rabbitmq-plugins enable rabbitmq_management
```
注意：管理界面会在15672端口提供服务

2）安装完毕以后，重启服务即可
```shell
service rabbitmq-server restart
```

3）在浏览器访问  

```
# 关闭防火墙服务
## 关闭防火墙
systemctl stop firewalld
## 关闭防火墙开机启动
systemctl disable firewalld

# 注意：一定要记住，在对应服务器（阿里云，腾讯云等）的安全组中开放15672端口

# 访问web管理界面
http://106.52.180.14:15672
```

成功访问：![RabbitMQ管理界面](https://rong0624.github.io/images/MQ/RabbitMQ/1626777167279.jpg)

### 授权账号和密码

### 小结


# 入门案例

## 快速入门（hello world）

实现功能：  
1）生产者发送消息，也就是要发送消息的程序  
2）消费者消费消息，会一直等待消息到来。  
3）mq接受到消息后，发送给消费者

如图：  
![快速入门图](https://rong0624.github.io/images/MQ/RabbitMQ/快速入门图.png)

### 环境准备

在学习RabbitMQ前必须掌握以下内容：  
熟悉使用Java  
熟悉使用Maven进行项目构建和依赖管理  
熟练使用eclipse或Idea开发工具  

环境约束：  
Jdk8  
Maven3.x  
Idea2019  
RabbitMQ 3.7.18

### 创建一个Maven工程

这里就不演示了，设置maven工程打包方式为jar包。

### 导入依赖

pom.xml 导入依赖
```xml
```

### 生产者发布消息

### 消费者消费消息（监听并消费消息）

### 启动测试


# RabbitMQ消息模型

## 简介

RabbitMQ提供了6种消息模型。但是第6种其实是RPC，并不是MQ，因此不予学习。那么也就剩下5种。但其实3、4、5这三种都属于订阅模型，只不过进行路由的方式不同。

![消息模型](https://rong0624.github.io/images/MQ/RabbitMQ/消息模型.png)

1）基本消息模式  
2）工作模式  
3）发布订阅模式  
4）路由模式  
5）主题模式  
6）RPC模式

## 基本模式

## 工作模式

## 订阅模式-Direct

## 订阅模式-Fanout

## 订阅模式-Topic

# 整合SpringBoot

## 订阅模式-Direct 

## 订阅模式-Fanout

## 订阅模式-Topic

RabbitMQ是由erlang语言开发，基于AMQP（Advanced Message Queue 高级消息队列协议）协议实现的消息队列，它是一种应用程序之间的通信方法，消息队列在分布式系统开发中应用非常广泛。

RabbitMQ官方地址：
http://www.rabbitmq.com

RabbitMQ是一个开源的遵循 AMQP协议实现的基于 Erlang语言编写，支持多种客户端（语言），用于在分布式系统中存储消息，转发消息，具有高可用，高可扩性，易用性等特征入门及安装。