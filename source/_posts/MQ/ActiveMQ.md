---
title: ActiveMQ
date: 2021-06-15 00:00:00
author: 神奇的荣荣
summary: ""
categories: MQ
tags: 
	- MQ
	- 中间件
---

# ActiveMQ基础

## 简介

ActiveMQ 是Apache出品，最流行的，能力强劲的开源消息总线。  
ActiveMQ 是一个完全支持JMS1.1和J2EE 1.4规范的 JMS Provider实现。  
尽管JMS规范出台已经是很久的事情了，但是JMS在当今的J2EE应用中间仍然扮演着特殊的地位。

## 特点

1）支持来自Java，C，C ++，C＃，Ruby，Perl，Python，PHP的各种跨语言客户端和协议  
2）完全支持JMS客户端和Message Broker中的企业集成模式  
3）支持许多高级功能，如消息组，虚拟目标，通配符和复合目标  
4）完全支持JMS 1.1和J2EE 1.4，支持瞬态，持久，事务和XA消息  
5）Spring支持，以便ActiveMQ可以轻松嵌入到Spring应用程序中，并使用Spring的XML配置机制进行配置  
6）专为高性能集群，客户端 - 服务器，基于对等的通信而设计  
7）CXF和Axis支持，以便ActiveMQ可以轻松地放入这些Web服务堆栈中以提供可靠的消息传递  
8）可以用作内存JMS提供程序，非常适合单元测试JMS  
9）支持可插拔传输协议，例如in-VM，TCP，SSL，NIO，UDP，多播，JGroups和JXTA传输  
10）使用JDBC和高性能日志支持非常快速的持久性

# ActiveMQ的安装

# ActiveMQ的消息形式

对于消息的传递有两种类型：  
1）点对点（p2p），即一个生产者和一个消费者一一对应；  
2）发布/订阅，即一个生产者产生消息并进行发送后，可以由多个消费者进行接收；  
如图所示：  
![ActiveMQ的消息类型](https://rong0624.github.io/images/MQ/ActiveMQ/ActiveMQ的消息类型.png)

JMS定义了五种不同的消息正文格式，以及调用的消息类型，允许你发送并接收以一些不同形式的数据，提供现有消息格式的一些级别的兼容性。
* StreamMessage -- Java原始值的数据流
* MapMessage -- 一套名称-值对
* TextMessage -- 一个字符串对象
* ObjectMessage -- 一个序列化的 Java对象
* BytesMessage -- 一个字节的数据流

# ActiveMQ的使用

导入maven依赖：
```xml
<!-- activemq依赖的jar -->
<dependency>
    <groupId>org.apache.activemq</groupId>
    <artifactId>activemq-all</artifactId>
    <version>5.11.2</version>
</dependency>
```

## Queue

### Producer

生产者：发布消息。

使用步骤：  
第一步：创建ConnectionFactory对象，需要指定服务端ip及端口号。  
第二步：使用ConnectionFactory对象创建一个Connection对象。  
第三步：开启连接，调用Connection对象的start方法。  
第四步：使用Connection对象创建一个Session对象。  
第五步：使用Session对象创建一个Destination对象（topic、queue），此处创建一个Queue对象。  
第六步：使用Session对象创建一个Producer对象。  
第七步：创建一个Message对象，创建一个TextMessage对象。  
第八步：使用Producer对象发送消息。  
第九步：关闭资源。

```java
    @Test
	public void testQueueProducer() throws Exception {
		// 第一步：创建ConnectionFactory对象，需要指定服务端ip及端口号。
		//brokerURL服务器的ip及端口号
		ConnectionFactory connectionFactory = new ActiveMQConnectionFactory("tcp://192.168.25.168:61616");
		// 第二步：使用ConnectionFactory对象创建一个Connection对象。
		Connection connection = connectionFactory.createConnection();
		// 第三步：开启连接，调用Connection对象的start方法。
		connection.start();
		// 第四步：使用Connection对象创建一个Session对象。
		//第一个参数：是否开启事务。true：开启事务，第二个参数忽略。
		//第二个参数：当第一个参数为false时，才有意义。消息的应答模式。1、自动应答2、手动应答。一般是自动应答。
		Session session = connection.createSession(false, Session.AUTO_ACKNOWLEDGE);
		// 第五步：使用Session对象创建一个Destination对象（topic、queue），此处创建一个Queue对象。
		//参数：队列的名称。
		Queue queue = session.createQueue("test-queue");
		// 第六步：使用Session对象创建一个Producer对象。
		MessageProducer producer = session.createProducer(queue);
		// 第七步：创建一个Message对象，创建一个TextMessage对象。
		/*TextMessage message = new ActiveMQTextMessage();
		message.setText("hello activeMq,this is my first test.");*/
		TextMessage textMessage = session.createTextMessage("hello activeMq,this is my first test.");
		// 第八步：使用Producer对象发送消息。
		producer.send(textMessage);
		// 第九步：关闭资源。
		producer.close();
		session.close();
		connection.close();
	}
```

### Consumer

## Topic

### Producer

生产者：发布消息。

使用步骤：  
第一步：创建ConnectionFactory对象，需要指定服务端ip及端口号。  
第二步：使用ConnectionFactory对象创建一个Connection对象。  
第三步：开启连接，调用Connection对象的start方法。  
第四步：使用Connection对象创建一个Session对象。  
第五步：使用Session对象创建一个Destination对象（topic、queue），此处创建一个Topic对象。  
第六步：使用Session对象创建一个Producer对象。  
第七步：创建一个Message对象，创建一个TextMessage对象。  
第八步：使用Producer对象发送消息。  
第九步：关闭资源。

```java
    @Test
	public void testTopicProducer() throws Exception {
		// 第一步：创建ConnectionFactory对象，需要指定服务端ip及端口号。
		// brokerURL服务器的ip及端口号
		ConnectionFactory connectionFactory = new ActiveMQConnectionFactory("tcp://192.168.25.168:61616");
		// 第二步：使用ConnectionFactory对象创建一个Connection对象。
		Connection connection = connectionFactory.createConnection();
		// 第三步：开启连接，调用Connection对象的start方法。
		connection.start();
		// 第四步：使用Connection对象创建一个Session对象。
		// 第一个参数：是否开启事务。true：开启事务，第二个参数忽略。
		// 第二个参数：当第一个参数为false时，才有意义。消息的应答模式。1、自动应答2、手动应答。一般是自动应答。
		Session session = connection.createSession(false, Session.AUTO_ACKNOWLEDGE);
		// 第五步：使用Session对象创建一个Destination对象（topic、queue），此处创建一个topic对象。
		// 参数：话题的名称。
		Topic topic = session.createTopic("test-topic");
		// 第六步：使用Session对象创建一个Producer对象。
		MessageProducer producer = session.createProducer(topic);
		// 第七步：创建一个Message对象，创建一个TextMessage对象。
		/*
		 * TextMessage message = new ActiveMQTextMessage(); message.setText(
		 * "hello activeMq,this is my first test.");
		 */
		TextMessage textMessage = session.createTextMessage("hello activeMq,this is my topic test");
		// 第八步：使用Producer对象发送消息。
		producer.send(textMessage);
		// 第九步：关闭资源。
		producer.close();
		session.close();
		connection.close();
	}
```

### Consumer

消费者：接收消息。

使用步骤：
第一步：创建一个ConnectionFactory对象。  
第二步：从ConnectionFactory对象中获得一个Connection对象。  
第三步：开启连接。调用Connection对象的start方法。  
第四步：使用Connection对象创建一个Session对象。  
第五步：使用Session对象创建一个Destination对象。和发送端保持一致topic，并且话题的名称一致。  
第六步：使用Session对象创建一个Consumer对象。  
第七步：接收消息。  
第八步：打印消息。  
第九步：关闭资源

```java
    @Test
	public void testTopicConsumer() throws Exception {
		// 第一步：创建一个ConnectionFactory对象。
		ConnectionFactory connectionFactory = new ActiveMQConnectionFactory("tcp://192.168.25.168:61616");
		// 第二步：从ConnectionFactory对象中获得一个Connection对象。
		Connection connection = connectionFactory.createConnection();
		// 第三步：开启连接。调用Connection对象的start方法。
		connection.start();
		// 第四步：使用Connection对象创建一个Session对象。
		Session session = connection.createSession(false, Session.AUTO_ACKNOWLEDGE);
		// 第五步：使用Session对象创建一个Destination对象。和发送端保持一致topic，并且话题的名称一致。
		Topic topic = session.createTopic("test-topic");
		// 第六步：使用Session对象创建一个Consumer对象。
		MessageConsumer consumer = session.createConsumer(topic);
		// 第七步：接收消息。
		consumer.setMessageListener(new MessageListener() {

			@Override
			public void onMessage(Message message) {
				try {
					TextMessage textMessage = (TextMessage) message;
					String text = null;
					// 取消息的内容
					text = textMessage.getText();
					// 第八步：打印消息。
					System.out.println(text);
				} catch (JMSException e) {
					e.printStackTrace();
				}
			}
		});
		System.out.println("topic的消费端03。。。。。");
		// 等待键盘输入
		System.in.read();
		// 第九步：关闭资源
		consumer.close();
		session.close();
		connection.close();
	}
```

# ActiveMQ消息数据持久化

场景问题：服务器断电重启，未被消费的消息是否会在重启之后被继续消费？

两种选择：非持久性模式/持久性模式  
1）非持久性模式：服务器断电(关闭)之后，使用非持久性模式时，没有被消费的消息不会继续消费全部丢失；
程序会报一个连接关闭异常停止运行，继续启动服务器运行程序，不会接收任何消息。  
2）持久性模式：服务器断电(关闭)后，使用持久性模式时，没有被消费的消息会继续消费；
程序也会报连接关闭异常，但再次启动服务器和程序后，接收方还能继续原来的消息再次接收。

PERSISTENT：
指示JMS provider持久保存消息，以保证消息不会因为JMS provider的失败而丢失

NON_PERSISTENT:
不要求JMS provider持久保存消息

在消息提供者设置消息持久化：`producer.setDeliveryMode(DeliveryMode.PERSISTENT);`

# JMS可靠消息机制

JMS消息只有在被确认之后，才认为已经被成功的消费了。   
消息的成功消费通常包含三个阶段：客户接收消息，客户处理消息和消息被确认。

在事务性会话中，当一个事务被提交的时候，确认自动发生。在非事务性会话中，消息何时被确认取决于创建会话时的应答模式。  
改参数有三个可选值：
1. Session.AUTO_ACKNOWLEDGE：自动签收  
当客户成功的从receive方法返回的时候，或者从MessageListener.onMessage方法成功返回的时候，会话自动确认客户收到的消息。
2. Session.CLIENT_ACKNOWLEDGE：手动签收  
客户通过调用消息的acknowledge方法确认消息。需要注意的是，在这种模式中，确认是在会话层上进行，确认一个被消费的消息，将自动确认所有已被会话消费的消息。例如，如果一个消息消费者消费了10个消息，然后确认第5个消息，那么所有10个消息都被确认。
3. Session.DUPS_ACKNOWLEDGE：  
该选择只是会话迟钝的确认消息的提交。如果JMS Provider失败，那么可能会导致一些重复的消息。如果是重复的消息，那么JMS provider必须把消息头的JMSRedelivered字段设置为true
4. 带事物的session：  
完成操作后需要手动的commit

