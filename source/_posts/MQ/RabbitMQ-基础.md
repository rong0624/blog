---
title: RabbitMQ-基础
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

## 消息模型

![消息模型](https://rong0624.github.io/images/MQ/RabbitMQ/1626769058993.jpg)

1. 基本消息模型（simple）
2. 工作消息模型（work）
3. 订阅模型-Fanout
4. 订阅模式-Direct
5. 订阅模型-Topic

**注意：订阅模型-Fanout，订阅模式-Direct，订阅模型-Topic都属于发布/订阅模型类型。**

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

![guest登录](https://rong0624.github.io/images/MQ/RabbitMQ/20210721213622.png)  
说明：rabbitmq有一个默认账号和密码是：guest/guest，但guest默认情况只能在localhost本机下访问，所以需要添加一个远程登录的用户。

1）新增用户并授权：
```
#新增用户
rabbitmqctl add_user admin 123

#设置用户分配操作权限
rabbitmqctl set_user_tags admin administrator

#为用户添加资源权限
#set_permissions [-p <vhostpath>] <user> <conf> <write> <read>
#解释：用户 admin 具有 / 这个 virtual host 中所有资源的配置、写、读权限
rabbitmqctl set_permissions -p "/" admin ".*" ".*" ".*"
```

2）使用admin登录管理页面
登录成功：  
![admin登录](https://rong0624.github.io/images/MQ/RabbitMQ/20210721215337.png)  

### 小结

管理用户常见命令如下：
```
# 查看用户列表
rabbitmqctl list_users

# 新增账号[并设置密码]
rabbitmqctl add_user 账号 密码

# 修改密码
rabbitmqctl change_password 账号 新密码

# 删除账号
rabbitmqctl delete_user 账号

# 给账号设置角色
rabbitmqctl set_user_tags 账号 角色

# 给账号设置权限
rabbitmqctl set_permissions -p "/" 账号 ".*" ".*" ".*"
```

# hello world（简单消息模型）

## 环境准备

在学习RabbitMQ前必须掌握以下内容：  
熟悉使用Java  
熟悉使用Maven进行项目构建和依赖管理  
熟练使用eclipse或Idea开发工具  

环境约束：  
Jdk8  
Maven3.x  
Idea2019  
RabbitMQ 3.8.8

## 图解

![简单消息模型](https://rong0624.github.io/images/MQ/RabbitMQ/20210721223009.png)  
在上图的模型中，有以下概念：
1. 生产者，也就是要发送消息的程序
2. 消费者：消息的接受者，会一直等待消息到来。
3. 消息队列：图中红色部分。类似一个邮箱，可以缓存消息；生产者向其中投递消息，消费者从其中取出消息。

## 导入依赖

```
    <!-- rabbitmq 依赖客户端 -->
    <dependency>
        <groupId>com.rabbitmq</groupId>
        <artifactId>amqp-client</artifactId>
        <version>5.8.0</version>
    </dependency>
```

## 消息生产者

消息生产者：生产消息

```java
public class Producer {

    private final static String queueName = "Hello";

    public static void main(String[] args) throws Exception {
        // 1.创建连接工厂
        ConnectionFactory factory = new ConnectionFactory();
        factory.setHost("106.52.180.14");
        factory.setPort(5672);
        factory.setUsername("admin");
        factory.setPassword("123");

        // 2.创建连接
        Connection connection = factory.newConnection();

        // 3.创建通道（实现了自动 close 接口 自动关闭 不需要显示关闭）
        Channel channel = connection.createChannel();

        /**
         * 4.声明队列
         * 参数1：队列名称
         * 参数2：是否持久化队列，不持久化的队列重启访问后丢失
         * 参数3：是否独占队列
         * 参数4：是否自动删除队列，当最后一个消费者退订后即被删除
         * 参数5：其他参数
         */
        channel.queueDeclare(queueName, false, false, false, null);

        /**
         * 5.发送消息
         * 参数1：交换机（不指定，使用默认交换机）
         * 参数2：路由键
         * 参数3：其他参数
         * 参数4：消息主体
         */
        String message = "hello world";
        channel.basicPublish("", queueName, null, message.getBytes());

        System.out.println("消息发送完成~");
    }

}
```

## 消息消费者

消息消费者：消费消息

```java
public class Consumer {

    private final static String queueName = "Hello";

    public static void main(String[] args) throws Exception {
        // 1.创建连接工厂
        ConnectionFactory factory = new ConnectionFactory();
        factory.setHost("106.52.180.14");
        factory.setPort(5672);
        factory.setUsername("admin");
        factory.setPassword("123");

        // 2.创建连接
        Connection connection = factory.newConnection();

        // 3.创建通道（实现了自动 close 接口 自动关闭 不需要显示关闭）
        Channel channel = connection.createChannel();

        /**
         * 4.接受消息
         * 参数1：监听的队列
         * 参数2：是否自动应答
         * 参数3：消费消息的程序
         * 参数4：消费消息失败的程序
         */
        channel.basicConsume(queueName, true, (consumerTag, message) -> {
            String msgBody = new String(message.getBody());
            System.out.println("消费消息，消息内容：" + msgBody);
        }, (consumerTag) -> {
            System.out.println("消费消息失败了~");
        });
    }

}

```

## 执行结果

// TODO

# 工作消息模型（work）

## 图解

![工作消息模型](https://rong0624.github.io/images/MQ/RabbitMQ/20210721224055.png)  
当有多个消费者时，我们的消息会被哪个消费者消费呢，我们又该如何均衡消费者消费信息的多少呢？  
主要有两种模式：
1. 轮询分发：一个消费者一条，按均分配；
2. 公平分发：根据消费者的消费能力进行公平分发，处理快的处理的多，处理慢的处理的少；

工作队列(又称任务队列)的主要思想是避免立即执行资源密集型任务，而不得不等待它完成。  
相反我们安排任务在之后执行。我们把任务封装为消息并将其发送到队列。  
在后台运行的工作进程将弹出任务并最终执行作业。当有多个工作线程时，这些工作线程将一起处理这些任务。

## 轮询分发

### 抽取工具类

```java
public class RabbitMqUtils {

    public static Channel getChannel() throws Exception {
        // 1.创建连接工厂
        ConnectionFactory factory = new ConnectionFactory();
        factory.setHost("106.52.180.14");
        factory.setPort(5672);
        factory.setUsername("admin");
        factory.setPassword("123");

        // 2.创建连接
        Connection connection = factory.newConnection();

        // 3.创建通道（实现了自动 close 接口 自动关闭 不需要显示关闭）
        Channel channel = connection.createChannel();

        return channel;
    }

}
```

### 生产者

```java
public class Producer {

    private final static String QUEUE_NAME = "work_queue";

    public static void main(String[] args) throws Exception {
        // 获取通道
        Channel channel = RabbitMqUtils.getChannel();

        /**
         * 声明队列
         * 参数1：队列名称
         * 参数2：是否持久化队列，不持久化的队列重启访问后丢失
         * 参数3：是否独占队列
         * 参数4：是否自动删除队列，当最后一个消费者退订后即被删除
         * 参数5：其他参数
         */
        channel.queueDeclare(QUEUE_NAME, false, false, false, null);

        // 发送消息
        Scanner scanner = new Scanner(System.in);
        while (scanner.hasNext()) {
            String message = scanner.next();
            channel.basicPublish("", QUEUE_NAME, null, message.getBytes());
            System.out.println("发送消息：" + message);
        }
    }

}

```

### 消费者

```java
public class Consumer {

    private final static String QUEUE_NAME = "work_queue";

    public static void main(String[] args) throws Exception {
        // 获取通道
        Channel channel = RabbitMqUtils.getChannel();

        // 监听队列，获取消息
        channel.basicConsume(QUEUE_NAME, true, (consumerTag, message) -> {
            String msgBody = new String(message.getBody());
            System.out.println("消费消息，消息内容：" + msgBody);
        }, (consumerTag) -> {
            System.out.println("消费消息失败了~");
        });
    }

}
```

### 执行结果

启动消费者进行发送消息。

启动两个消息者线程，模拟两个消费者在监听队列消息消息：  
![消费者1](https://rong0624.github.io/images/MQ/RabbitMQ/20210721232358.png)  
![消费者2](https://rong0624.github.io/images/MQ/RabbitMQ/20210721232422.png)  

通过程序执行发现生产者总共发送 4 个消息，  
消费者 1 和消费者 2 分别分得两个消息，并且是按照有序的一个接收一次消息：  
![工作消息模型](https://rong0624.github.io/images/MQ/RabbitMQ/20210721232422.png)  

## 公平分发（能者多劳）



# 交换机

# 死信队列

# 延迟队列


# 整合SpringBoot

## 订阅模式-Fanout

## 订阅模式-Direct

## 订阅模式-Topic