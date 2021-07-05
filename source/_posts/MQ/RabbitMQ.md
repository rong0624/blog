---
title: RabbitMQ
date: 2021-06-22 00:00:00
author: 神奇的荣荣
summary: ""
tags: MQ
categories: MQ
---

# RabbitMQ下载与安装

## linux下安装

### 环境准备

环境相关：
linux（CentOS 7.5）
erlang 22.x
rabbitmq 3.7.18

Erlang rpm下载：
https://github.com/rabbitmq/rabbitmq-server/releases/tag/v3.7.18

Rabbitmq rpm下载：
https://github.com/rabbitmq/rabbitmq-server/tags?after=v3.8.0-rc.3

### 安装Erlang

![]()
下载erlang时需要注意，要和rabbitmq版本兼容.

1）erlang rpm下载：
https://github.com/rabbitmq/rabbitmq-server/releases/tag/v3.7.18
erlang-22.0.7-1.el7.x86_64.rpm

2）rpm上传到系统中，安装erlang 
rpm -Uvh erlang-solutions-2.0-1.noarch.rpm 
yum install -y erlang

3）查看erlang版本
erl -v  
![]()  
显示这样代表安装成功

### 安装socat

安装Erlang后直接安装RabbitMQ，需要安装socat。

安装socat  
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

# RabbitMQ管理界面

# 入门案例

## 快速入门

# RabbitMQ消息模型

# 整合SpringBoot

## 订阅模式-Direct 

## 订阅模式-Fanout

## 订阅模式-Topic