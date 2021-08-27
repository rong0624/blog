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
4. 订阅模型-Direct
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

```xml
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

    private final static String QUEUE_NAME = "Hello";

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
        channel.queueDeclare(QUEUE_NAME, false, false, false, null);

        /**
         * 5.发送消息
         * 参数1：交换机（不指定，使用默认交换机）
         * 参数2：路由键
         * 参数3：其他参数
         * 参数4：消息主体
         */
        String message = "hello world";
        channel.basicPublish("", QUEUE_NAME, null, message.getBytes());

        System.out.println("消息发送完成~");
    }

}
```

## 消息消费者

消息消费者：消费消息

```java
public class Consumer {

    private final static String QUEUE_NAME = "Hello";

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

        // 消费消息的程序
        DeliverCallback deliverCallback = (consumerTag, message) -> {
            // 模拟异常
            String msgBody = new String(message.getBody());
            System.out.println("消费消息，消息内容：" + msgBody);
        };

        // 消费消息失败的程序
        CancelCallback cancelCallback = (consumerTag) -> {
            System.out.println("消费消息失败了~");
        };

        /**
         * 4.接受消息
         * 参数1：监听的队列
         * 参数2：是否自动应答
         * 参数3：消费消息的程序
         * 参数4：消费消息失败的程序
         */
        channel.basicConsume(QUEUE_NAME, true, deliverCallback, cancelCallback);
    }

}
```

## 执行结果

1）启动生产者，发送消息

启动生产者，发送消息：  
![生产者发送消息](https://rong0624.github.io/images/MQ/RabbitMQ/1626949235794.jpg)

查看管理页面，可以发现：新建了一个队列：Hello，并且队列里有一条消息等待读取：  
![管理页面，查看队列](https://rong0624.github.io/images/MQ/RabbitMQ/1626949728101.jpg)

进入队列详情，还可以看到消息主题内容：  
![管理页面，查看消息主体内容](https://rong0624.github.io/images/MQ/RabbitMQ/1626949629441.jpg)

2）启动消费者，消费消息

启动消费者，消费消息：  
![消费者消费消息](https://rong0624.github.io/images/MQ/RabbitMQ/1626949896540.jpg)

查看管理页面，可以发现：Hello队列，消息已经被消费了：  
![管理页面，查看队列](https://rong0624.github.io/images/MQ/RabbitMQ/1626949947003.jpg)

# 工作消息模型（work）

工作队列(又称任务队列)的主要思想是避免立即执行资源密集型任务，而不得不等待它完成。
相反我们安排任务在之后执行。我们把任务封装为消息并将其发送到队列。在后台运行的工作进
程将弹出任务并最终执行作业。当有多个工作线程时，这些工作线程将一起处理这些任务。

## 图解

![工作消息模型](https://rong0624.github.io/images/MQ/RabbitMQ/20210721224055.png)  

思考：当有多个消费者时，我们的消息会被哪个消费者消费呢，我们又该如何均衡消费者消费信息的多少呢？  
主要有两种模式：
1. 轮询分发：一个消费者一条，按均分配；
2. 不公平分发：根据消费者的消费能力进行分发，处理快的处理的多，处理慢的处理的少；

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

        /**
         * 接受消息
         * 参数1：监听的队列
         * 参数2：是否自动应答
         * 参数3：消费消息的程序
         * 参数4：消费消息失败的程序
         */
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

1）启动两个消息者线程，模拟两个消费者在监听队列消息消息：  
![消费者1](https://rong0624.github.io/images/MQ/RabbitMQ/20210721232358.png)  
![消费者2](https://rong0624.github.io/images/MQ/RabbitMQ/20210721232422.png)  

2）启动生产者进行发送消息。

3）结论
通过生产者总共发送 4 个消息；  
消费者 1 和消费者 2 分别分得两个消息，并且是按照有序的一个接收一次消息：  
![工作消息模型](https://rong0624.github.io/images/MQ/RabbitMQ/20210721232715.png)  

## 不公平分发（能者多劳）

以上RabbitMQ 分发消息采用的轮询分发，但是在某种场景下这种策略并不是很好。  
比方说：有两个消费者在处理任务，其中有个消费者 1 处理任务的速度非常快，而另外一个消费者 2 处理速度却很慢，这个时候我们还是采用轮询分发的话，  
这处理速度快的这个消费者很大一部分时间处于空闲状态，而处理慢的那个消费者一直在干活，这种分配方式在这种情况下其实就不太好，但是 RabbitMQ 并不知道这种情况，它依然很公平的进行分发。

现在想要做的是：不公平分发，消费越快的人，消费的越多，怎么实现呢？
```java
Integer prefetchCount = 1
channel.basicQos(prefetchCount);
```

![不公平分发图](https://rong0624.github.io/images/MQ/RabbitMQ/1626919721516.jpg)

解释：如果这个任务我还没有处理完或者我还没有应答你，你先别分配给我，我目前只能处理一个任务，  
然后 rabbitmq 就会把该任务分配给没有那么忙的那个空闲消费者，当然如果所有的消费者都没有完成手上任务，  
队列还在不停的添加新任务，队列有可能就会遇到队列被撑满的情况，这个时候就只能添加新的 worker 或者改变其他存储任务的策略。

# Queue 队列机制

# Message 消息机制

## 消息属性

AMQP 模型中的消息（Message）对象是带有属性（Attributes）的。有些属性及其常见，以至于 AMQP 0-9-1 明确的定义了它们，并且应用开发者们无需费心思思考这些属性名字所代表的具体含义。

例如：  
1）Content type（内容类型）  
2）Content encoding（内容编码）  
3）Routing key（路由键）  
4）Delivery mode (persistent or not)  
5）投递模式（持久化 或 非持久化）  
6）Message priority（消息优先权）  
7）Message publishing timestamp（消息发布的时间戳）  
8）Expiration period（消息有效期）  
9）Publisher application id（发布应用的 ID）  

有些属性是被 AMQP 代理所使用的，但是大多数是开放给接收它们的应用解释器用的。有些属性是可选的也被称作消息头（headers）。他们跟 HTTP 协议的 X-Headers 很相似。消息属性需要在消息被发布的时候定义。

## 消息主体

AMQP 的消息除属性外，也含有一个有效载荷 - Payload（消息实际携带的数据），它被 AMQP 代理当作不透明的字节数组来对待。

消息代理不会检查或者修改有效载荷。消息可以只包含属性而不携带有效载荷。它通常会使用类似 JSON 这种序列化的格式数据，为了节省，协议缓冲器和 MessagePack 将结构化数据序列化，以便以消息的有效载荷的形式发布。AMQP 及其同行者们通常使用 “content-type” 和 “content-encoding” 这两个字段来与消息沟通进行有效载荷的辨识工作，但这仅仅是基于约定而已。

## 消息持久化

### 概念

消息能够以持久化的方式发布，RabbitMQ 会将此消息存储在磁盘上。如果服务器重启，持久化消息不会丢失。
将消息发送给一个持久化的交换机或者路由给一个持久化的队列，并不会使得此消息具有持久化性质：它完全取决与消息本身的持久模式（persistence mode）。将消息以持久化方式发布时，会对性能造成一定的影响（就像数据库操作一样，健壮性的存在必定造成一些性能牺牲）。

## 消息应答

### 概念

消费者完成一个任务可能需要一段时间，如果其中一个消费者处理一个长的任务并仅只完成了部分突然它挂掉了，会发生什么情况。RabbitMQ 一旦向消费者传递了一条消息，  
便立即将该消息标记为删除。在这种情况下，突然有个消费者挂掉了，我们将丢失正在处理的消息。

为了保证消息在发送过程中不丢失，rabbitmq 引入消息应答机制，消息应答就是：**消费者在接收到消息并且处理该消息之后，告诉 rabbitmq 它已经处理了，rabbitmq 可以把该消息删除了。**

rabbitmq有两种消息应答机制：
* 自动应答
* 手动应答

### 自动应答

消息发送后立即被认为已经传送成功，这种模式需要在高吞吐量和数据传输安全性方面做权衡，因为这种模式如果消息在接收到之前，消费者那边出现连接或者 channel 关闭，
那么消息就丢失了,当然另一方面这种模式消费者那边可以传递过载的消息，没有对传递的消息数量进行限制，当然这样有可能使得消费者这边由于接收太多还来不及处理的消息，
导致这些消息的积压，最终使得内存耗尽，最终这些消费者线程被操作系统杀死，所以这种模式仅适用在消费者可以高效并以某种速率能够处理这些消息的情况下使用。

#### 自动应答演示

注意：之前的案例都是自动应答。

#### 自动应答存在的问题

生产者不做任何修改，直接运行，消息发送成功，如下：  
![生产者发送消息](1627022937994.jpg)

修改消费者，添加异常，代码如下：
```java
public class Consumer {

    private final static String QUEUE_NAME = "work_queue";

    public static void main(String[] args) throws Exception {
        // 获取通道
        Channel channel = RabbitMqUtils.getChannel();

        /**
         * 接受消息
         * 参数1：监听的队列
         * 参数2：是否自动应答
         * 参数3：消费消息的程序
         * 参数4：消费消息失败的程序
         */
        channel.basicConsume(QUEUE_NAME, true, (consumerTag, message) -> {
            // 模拟异常
            int i = 1 / 0;
            String msgBody = new String(message.getBody());
            System.out.println("消费消息，消息内容：" + msgBody);
        }, (consumerTag) -> {
            System.out.println("消费消息失败了~");
        });
    }

}
```

启动消费者，消费消息：  
可以发现消息消费失败了，并且队列里面的消息消失了，
而且消费者还一直处于监听状态，一直在获取消息消费，但都消费不成功，这样就导致了消息的丢失。

### 手动应答

#### 手动应答方法

* Channel.basicAck(long deliveryTag, boolean multiple)
    - 用于肯定确认，RabbitMQ 已知道该消息并且成功的处理消息，可以将其丢弃了
* Channel.basicNack(long deliveryTag, boolean multiple, boolean requeue)
    - 用于否定确认
* Channel.basicReject(long deliveryTag, boolean requeue)
    - 用于否定确认
    - 与 Channel.basicNack 相比少一个参数，不处理该消息了直接拒绝，可以将其丢弃了

#### Multiple 的解释

multiple 表示批量应答； 
```
void basicAck(long deliveryTag, boolean multiple) throws IOException;

void basicReject(long deliveryTag, boolean requeue) throws IOException;
```

multiple 的 true 和 false 代表不同意思
* true
    - 代表批量应答 channel 上未应答的消息
    - 比如说 channel 上有传送 tag 的消息 5,6,7,8 当前 tag 是 8 那么此时5-8 的这些还未应答的消息都会被确认收到消息应答
* false
    - 只会应答 tag=8 的消息 5,6,7 这三个消息依然不会被确认收到消息应答

![批量应答](1628672592616.jpg)

#### 手动应答演示

手动应答只需要改动消费者：
```java
public class Consumer {

    private final static String QUEUE_NAME = "work_queue";

    public static void main(String[] args) throws Exception {
        // 获取通道
        Channel channel = RabbitMqUtils.getChannel();

        // 消费消息的程序
        DeliverCallback deliverCallback = (consumerTag, message) -> {
            // 模拟异常
            String msgBody = new String(message.getBody());
            System.out.println("消费消息，消息内容：" + msgBody);
            /**
             * 手动ack应答
             * 参数1：消息标记tag
             * 参数2：批量应答通道内未应答的消息
             */
            channel.basicAck(message.getEnvelope().getDeliveryTag(), false);
        };

        // 消费消息失败的程序
        CancelCallback cancelCallback = (consumerTag) -> {
            System.out.println("消费消息失败了~");
        };

        /**
         * 接受消息
         * 参数1：监听的队列
         * 参数2：是否自动应答
         * 参数3：消费消息的程序
         * 参数4：消费消息失败的程序
         */
        // 手动应答
        boolean autoAck = false;
        channel.basicConsume(QUEUE_NAME, autoAck, deliverCallback, cancelCallback);
    }

}
```

### 消息重新入队

如果消费者由于某些原因失去连接(其通道已关闭，连接已关闭或 TCP 连接丢失)，导致消息未发送 ACK 确认，
RabbitMQ 将了解到消息未完全处理，并将对其重新排队。如果此时其他消费者可以处理，它将很快将其重新分发给另一个消费者。
这样，即使某个消费者偶尔死亡，也可以确保不会丢失任何消息。

![消息重新入队](1628673375555.jpg)


## 预取消息

# 持久化

# 交换机

# 死信队列

# 延迟队列


# 整合SpringBoot

## 订阅模式-Fanout

## 订阅模式-Direct

## 订阅模式-Topic