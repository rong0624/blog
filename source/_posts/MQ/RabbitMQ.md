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

# RabbitMQ基础

## 简介

RabbitMQ是由erlang语言开发，基于AMQP（Advanced Message Queue 高级消息队列协议）协议实现的消息队列。  
AMQP的主要特征是面向消息、队列、路由(包括点对点和发布/订阅)、可靠性、 安全。AMQP协议更多用在企业系统内，对数据一致性、稳定性和可靠性要求很高的场景，对性能和吞吐量的要求还在其次。

RabbitMQ官方地址：http://www.rabbitmq.com

# RabbitMQ的安装

## Linux下安装

### 环境准备

环境相关：  
linux（CentOS 7.5）  
erlang 22.x  
rabbitmq 3.7.18

<!-- more -->

Erlang rpm下载：  
https://github.com/rabbitmq/rabbitmq-server/releases/tag/v3.7.18

Rabbitmq rpm下载：  
https://github.com/rabbitmq/rabbitmq-server/tags?after=v3.8.0-rc.3

### 安装Erlang

![erlang版本信息](https://rong0624.github.io/images/MQ/RabbitMQ/erlang版本.png)   
下载erlang时需要注意，要和rabbitmq版本兼容.

1）erlang rpm下载：
https://github.com/rabbitmq/rabbitmq-server/releases/tag/v3.7.18
erlang-22.0.7-1.el7.x86_64.rpm

2）rpm上传到系统中，安装erlang 
rpm -Uvh erlang-solutions-2.0-1.noarch.rpm 
yum install -y erlang

3）查看erlang版本
erl -v  
![erlang版本](https://rong0624.github.io/images/MQ/RabbitMQ/erlang查看版本.png)   
显示这样代表安装成功

### 安装socat

安装Erlang后直接安装RabbitMQ，需要安装socat。

安装socat：  
yum install -y socat

### 安装RabbitMQ

1）rabbitmq rpm下载  
https://github.com/rabbitmq/rabbitmq-server/tags?after=v3.8.0-rc.3
rabbitmq-server-3.7.18-1.el7.noarch.rpm 

2）rpm上传到系统中，并安装rabbitmq   
rpm -Uvh rabbitmq-server-3.7.18-1.el7.noarch.rpm   
yum install rabbitmq-server -y

3）启动服务并测试  
启动服务：  
systemctl start rabbitmq-server 

查看服务状态，如图：  
systemctl status rabbitmq-server.service 

4）常用命令
```
# 启动服务 
systemctl start rabbitmq-server 

# 查看服务状态
systemctl status rabbitmq-server.service 

# 开机自启动 
systemctl enable rabbitmq-server 

# 停止服务 
systemctl stop rabbitmq-server
```

## Windos下安装

## Mac下安装

# RabbitMQ管理界面

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