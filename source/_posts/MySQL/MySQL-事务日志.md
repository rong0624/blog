---
title: MySQL事务日志
date: 2021-06-23 00:00:00
author: 神奇的荣荣
summary: ""
tags: MySQL
categories: MySQL
---

# MySQL 事务日志（redolog & undolog）

InnoDB事务日志包括 redo log 和 undo log。redo log是重做日志，提供前滚操作，undo log是回滚日志，提供回滚操作。

1）redo log通常是物理日志，记录的是数据页的物理修改，而不是某一行或某几行修改成怎样，他用来恢复提交后的物理数据页（恢复数据页，且只能恢复到最后一次提交的位置）。用来保证事务的持久性。  
2）undo log用来回滚行记录到某个版本。undo log一般是逻辑日志，记录每行记录进行修改。用来保证事务的原子性以及InnoDB的MVCC。

<!-- more -->

# redo log（重做日志）

## 定义

redo log 是 MySQL 的物理日志，也叫重做日志，提供前滚操作。是InnoDB存储引擎的事务日志。  
用来保证事务的持久性。防止在发生故障的时间点，尚有脏页未写入磁盘，在重启mysql服务的时候，根据redo log进行重做，从而达到事务的持久性这一特性

MySQL 每执行一条 SQL 更新语句，不是每次数据更改都立刻写到磁盘，而是先将记录写到 redo log 里面，并更新内存（这时内存与磁盘的数据不一致，将这种有差异的数据称为脏页），一段时间后，再一次性将多个操作记录写到到磁盘上，这样可以减少磁盘 io 成本，提高操作速度。先写日志，再写磁盘，这就是 MySQL 里经常说到的 WAL 技术，即 Write-Ahead Logging，又叫预写日志。MySQL 通过 WAL 技术保证事务的持久性。

***

## redo log的基本概念

redo log包含两部分：  
一个是内存中的日志缓存（redo log buffer），该部分日志是易失性的；  
二是磁盘上的重做日志文件（redo log file），该部分日志是持久的；

在概念上，innodb通过force log at commit机制实现事务的持久性，即在事务提交的时候，必须先将该事务的所有事务日志写入到磁盘上的redo log file和undo log file中进行持久化。

## redo log（重做日志） vs bin log（二进制日志）

// TODO

redo log 和 bin log是不同的日志文件，虽然bin log也记录了InnoDB表的很多重做，也能实现重做的功能，但它们之间有很大的区别。

### 产生的地方不一样

bin log是在MySQL服务层产生的，不管是什么存储引擎，对数据库进行了修改都会记录bin log。  
redo log是在InnoDB存储引擎产生的，记录该存储引擎中表的修改，存储引擎在服务层下层，所以bin log 优先于redo log被记录。

### 记录的内容不同

bin log是逻辑日志，记录的是这个这个语句的原始逻辑，比如“给id=2这一行的c字段加1”。  
redo log是物理日志，记录该数据页更新的内容。

### 写入的方式不同

bin log是追加写，写到一定大小的时候会更换下一个文件，不会回覆盖。  
redo log是循环写，日志空间大小固定。

# undo log（回滚日志）

## 定义

redo log 是 MySQL 的逻辑日志，也叫回滚日志，提供回滚操作。是InnoDB存储引擎的事务日志。    
保存事务发生之前数据的一个版本，用于回滚，保证事务的原子性。  
同时可以提供多版本并发控制下的读（MVCC），也即非锁定读。

undo log 和 redo log记录的物理日志不一样，它是逻辑日志。  
可以认为当delete一条记录时，undo log中会记录一条对应的insert记录，反之亦然，当update一条记录时，它记录一条对应相反的update记录。

当rollback（事务回滚）时，就可以从undo log中的逻辑记录读取到相应的内容进行回滚。  
有时候应用到行版本控制的时候，也是通过undo log来实现的：当读取某一行被其他事务锁定时，他可以从undo log中分析出该行记录以前是什么，从而提供该行版本信息，让用户实现非锁定一致性读取。

**undo log是采用段（segment）的方式来记录的，每个undo操作在记录的时候占用一个undo log segment。
另外，undo log也会产生redo log，因为undo log也要实现持久性保护。**

**保存了事务发送之前的数据的一个版本，可以用于回滚，同时可以提供多版本并发控制下的读（MVCC），也即非锁定读。**

***

## 


