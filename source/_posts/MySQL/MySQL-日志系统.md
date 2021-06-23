---
title: MySQL日志系统
date: 2021-06-22 00:00:05
author: 神奇的荣荣
summary: ""
tags: MySQL
categories: MySQL
---

# error log（错误日志）

## 定义

error log 是 MySQL 的错误日志。  
主要记录 MySQL 服务实例每次启动，停止的详细信息，以及 MySQL 实例运行过程中产生的警告或者错误信息。  
和其他的日志不同，MySQL的error日志必须开启，无法关闭。

**注意：默认情况下，错误日志的文件名为：主机名.err。 但 error 日志并不会记录所有的错误信息，只有MySQL服务实例运行过程中发声的关键错误（critical）才会被记录下来。**

***

## 设置错误日志

查看当前的错误日志文件  
没有设置错误日志文件，默认指定了一个的错误日志文件
```
mysql> show variables like 'log_error';
+---------------+----------------------+
| Variable_name | Value                |
+---------------+----------------------+
| log_error     | ./VM-16-4-centos.err |
+---------------+----------------------+
1 row in set (0.00 sec)
```

命令：
```sql
show variables like 'log_error'; #查看当前的错误日志文件，如果没有指定，默认有一个错误日志文件
set global log_error = '/var/lib/mysql/error.log'; #设置错误日志文件
```

修改配置文件：  
修改 my.cnf 文件，在 [mysqld] 下增加或修改参数
```
[mysqld]
log-error='/var/lib/mysql/error.log'
```

当前通过修改配置文件指定错误配置文件，重启服务配置生效  
可以看到当启动服务后，打印出Starting MySQL.Logging to '/var/lib/mysql/error.log'  
这代表设置成功！
```
[root@VM-16-4-centos mysql]# service mysql stop
Shutting down MySQL. SUCCESS! 
[root@VM-16-4-centos mysql]# service mysql start
Starting MySQL.Logging to '/var/lib/mysql/error.log'.
. SUCCESS! 
```

查看错误日志文件：  
发现 mysql 实例启动的日志【验证了错误日志会记录实例每次启动，停止的详细信息】
```
[root@VM-16-4-centos mysql]# cat /var/lib/mysql/error.log 
210621 18:35:41 [Note] Plugin 'FEDERATED' is disabled.
210621 18:35:41 InnoDB: The InnoDB memory heap is disabled
210621 18:35:41 InnoDB: Mutexes and rw_locks use GCC atomic builtins
210621 18:35:41 InnoDB: Compressed tables use zlib 1.2.11
210621 18:35:41 InnoDB: Using Linux native AIO
210621 18:35:41 InnoDB: Initializing buffer pool, size = 128.0M
210621 18:35:41 InnoDB: Completed initialization of buffer pool
210621 18:35:41 InnoDB: highest supported file format is Barracuda.
210621 18:35:41  InnoDB: Waiting for the background threads to start
210621 18:35:42 InnoDB: 5.5.62 started; log sequence number 1598913
210621 18:35:42 [Note] Server hostname (bind-address): '0.0.0.0'; port: 3306
210621 18:35:42 [Note]   - '0.0.0.0' resolves to '0.0.0.0';
210621 18:35:42 [Note] Server socket created on IP: '0.0.0.0'.
210621 18:35:42 [Note] Event Scheduler: Loaded 0 events
210621 18:35:42 [Note] /usr/sbin/mysqld: ready for connections.
Version: '5.5.62'  socket: '/var/lib/mysql/mysql.sock'  port: 3306  MySQL Community Server (GPL)
```

***

# general log（普通日志）

## 定义

general log 是 MySQL 的普通日志。  
主要记录 MySQL 服务实例所有的操作，如：select，update，insert，delete等操作，无论操作是否成功执行都会记录。还记录 MySQL 客户端与 MySQL 服务端连接及断开的相关信息，无论连接成功还是失败。

**注意：由于普通日志几乎记录了MySQL的所有操作，对于数据访问频繁的数据库服务器而言，
如果开启MySQL的普通查询日志将会大幅度的降低数据库的性能，因此建议关闭普通查询日志。
只有在特殊时期，如需要追踪某些特殊的查询日志，可以临时打开普通的查询日志。**

***

## 相关参数

普通日志相关参数：
* general_log：是否开启普通日志
* general_log_file：普通日志文件的存放路径

***

## 开启普通日志

查看普通日志的当前配置
```
mysql> show variables like '%general_log%';
+------------------+-----------------------------------+
| Variable_name    | Value                             |
+------------------+-----------------------------------+
| general_log      | OFF                               |
| general_log_file | /var/lib/mysql/VM-16-4-centos.log |
+------------------+-----------------------------------+
2 rows in set (0.00 sec)
```

命令：(动态修改，重启失效)
``` sql
show variables like '%general_log%'; #查看普通日志是否开启
show variables like '%general_log_file%'; #查看普通日志文件的存放路径

set global general_log=1; #开启普通日志
set global general_log_file='/var/lib/mysql/general.log'; #指定普通日志文件的存放路径
```

修改配置文件：(永久开启)  
修改 my.cnf 文件，在 [mysqld] 下增加或修改参数
```
[mysqld]
general_log=1
general_log_file=/var/lib/mysql/general.log
```

当前通过修改配置文件指定普通日志配置，重启服务后查看普通日志。  
结论：可以看到，普通日志记录了客户端登录，查询数据库，查询表的所有操作。
```
[root@VM-16-4-centos mysql]# cat /var/lib/mysql/general.log 
/usr/sbin/mysqld, Version: 5.5.62-log (MySQL Community Server (GPL)). started with:
Tcp port: 3306  Unix socket: /var/lib/mysql/mysql.sock
Time                 Id Command    Argument
210621 21:51:45	    1 Connect	root@localhost on 
		    1 Connect	Access denied for user 'root'@'localhost' (using password: NO)
210621 21:52:01	    2 Connect	root@localhost on 
		    2 Query	select @@version_comment limit 1
210621 21:52:51	    2 Query	SELECT DATABASE()
		    2 Init DB	test
210621 21:53:00	    2 Query	select * from a
210621 21:53:03	    2 Query	select * from a
```

***

## 普通日志文件处理

//TODO

***


# slow query log（慢查询日志）

## 定义

slow query log 是 MySQL 的慢查询日志。  
主要记录 MySQL 中【响应时间超过阀值的sql语句】 或【没有使用索引】的查询语句。

**注意：慢查询日志与普通查询日志不同，区别在于：慢查询日志只包含成功执行过的查询语句。**  

***

## 相关参数

以下是慢查询日志相关的参数：
* slow_query_log：慢查询日志是否开启
* slow_query_log_file：慢查询日志文件的存放路径，如果没有指定参数slow_query_log_file的话，系统默认会给一个缺省的文件host_name-slow.log。
* long_query_time：设置慢查询的时间阈值，默认阈值是10s。
* log_quries_not_using_indexes：是否将不适用索引的查询语句记录到慢查询日志中，无论查询速度有多快。
* slow_queries：记录当前慢查询sql条数

***

## 开启慢查询日志

查看慢查询日志的当前配置.  
通过查看变量来判断是否开启慢查询日志.  
默认情况下slow_query_log的值为OFF，表示慢查询日志是禁用的。
```
mysql> show variables like '%slow_query_log%';
+---------------------+----------------------------------------+
| Variable_name       | Value                                  |
+---------------------+----------------------------------------+
| slow_query_log      | OFF                                    |
| slow_query_log_file | /var/lib/mysql/VM-16-4-centos-slow.log |
+---------------------+----------------------------------------+
2 rows in set (0.00 sec)
```

命令：（动态修改，重启失效）
``` sql
show variables like '%slow_query_log%'; #查看是否开启
show variables like '%slow_query_log_file%'; #查看慢查询日志文件的存放路径

set global slow_query_log=1; #设置开启慢查询日志
```

修改配置文件：(永久开启)
修改 my.cnf 文件，在 [mysqld] 下增加或修改参数
```
[mysqld]
slow_query_log=1
slow_query_log_file=/var/lib/mysql/atguigu-slow.log
```

***

## 什么 sql 会被记录到慢查询日志

问题：开启慢查询日志后，什么 sql 会被记录到慢查询日志里面呢？  
答：慢查询日志主要记录【响应时间超过阀值的sql语句】或【没有使用索引】的查询语句。

### 记录响应时间超过阀值的sql语句

时间阈值是由 long_query_time 控制的.  
long_query_time：设置慢查询的时间阈值，默认阈值是10s。可以使用命令修改，也可以在my.cnf参数里面修改。

命令：
```sql
show variables LIKE 'long_query_time%'; #查看long_query_time的值
set global long_query_time=5; #设置查询超过5秒则算慢查询sql
```
注意：当前设置系统参数方式，mysql重启即失效，如果要永久存在则需要修改配置文件。


修改配置文件：(永久开启)  
修改 my.cnf 文件，在 [mysqld] 下增加或修改参数
```
[mysqld]
long_query_time=5
```

### 记录没有使用索引的查询语句

log_quries_not_using_indexes：是否将不使用索引的查询语句记录到慢查询日志中，无论查询速度有多快。

命令：
```sql
show variables like '%log_queries_not_using_indexes%'; #查看值
set global log_queries_not_using_indexes=1; #设置开启记录没有使用索引的查询语句
```

修改配置文件：（永久开启）  
修改 my.cnf 文件，在 [mysqld] 下增加或修改参数
```
log_queries_not_using_indexes=1
```

## 慢查询 sql 案例

``` sql
set global slow_query_log=1; #设置开启慢查询日志
set global long_query_time=3; #设置查询超过3秒则算慢查询sql
select sleep(4) #模拟一次查询，查询耗时4秒
show global status like '%slow_queries%'; #查询当前慢查询sql条数命令
```

去mysql的data目录下找到慢查询日志文件：  
我没有去配置日志文件名，所以是一个默认的文件名：localhost-slow.log

![慢查询日志图片](https://niubilityoyr.github.io/images/MySQL/1624261484667.jpg)

可以看到当前慢查询日志中会记录查询超过了阈值的sql，我们刚刚的select sleep(4)就在当中，而且可以明确的看到当前sql，当前查询时间，锁的时间，一共有多少数据。

***

## 日志查询分析器（mysqldumpslow）

日志查询分析器的体现：  
在生产环境中，如果要手工分析日志，查找、分析SQL，显然是个体力活，MySQL提供了日志分析工具mysqldumpslow。

![日志查询分析器帮助信息图片](https://niubilityoyr.github.io/images/MySQL/日志查询分析器使用.jpg)

mysqldumpslow --help  查看mysqldumpslow的帮助信息

* s：表示按照何种方式排序
* c：访问次数
* l：锁定时间
* r：返回记录
* t：查询时间
* al：平均锁定时间
* ar：平均返回记录数
* at：平均查询时间
* t：即为返回前面多少条数据
* g：后边搭配一个正则匹配模式，大小写不敏感。

分析器常用的方式：
``` sql
#得到返回数据集最多的10个SQL
mysqldumpslow -s r -t 10 /var/lib/mysql/localhost-slow.log

#得到访问次数最多的10个SQL
mysqldumpslow -s c -t 10 /var/lib/mysql/atguigu-slow.log

#得到按照时间排序的前10条里面含有左连接的查询sql
mysqldumpslow -s t -t 10 -g "left join" /var/lib/mysql/atguigu-slow.log

#另外建议在使用这些命令时结合 | 和more 使用 ，否则有可能出现爆屏情况
mysqldumpslow -s r -t 10 /var/lib/mysql/atguigu-slow.log | more
```

***

# bin log（归档日志）

## 定义

1111

***

## 相关参数

* log_bin：指定 bin log是否打开
* log_bin_basename：指定的是 bin log 的基本文件名，后面会追加标识来表示每一个文件
* log_bin_index：指定的是 bin log 文件的索引文件，这个文件管理了所有的 bin log 文件的目录

***

# redo log（重做日志）

## 定义

redo log 是 MySQL 的物理日志，也叫重做日志，记录存储引擎 InnoDB 的事务日志。

MySQL 每执行一条 SQL 更新语句，不是每次数据更改都立刻写到磁盘，而是先将记录写到 redo log 里面，并更新内存（这时内存与磁盘的数据不一致，将这种有差异的数据称为脏页），一段时间后，再一次性将多个操作记录写到到磁盘上，这样可以减少磁盘 io 成本，提高操作速度。先写日志，再写磁盘，这就是 MySQL 里经常说到的 WAL 技术，即 Write-Ahead Logging，又叫预写日志。MySQL 通过 WAL 技术保证事务的持久性。

***

# undo log（回滚日志）

## 定义


***

## 相关参数

undo log相关参数：
* innodb_undo_logs :设置回滚日志的回滚段大小，默认为128k
* innodb_undo_directory: 设置回滚日志存放的目录。
* innodb_undo_tablespace:设置了回滚日志由多少个回滚日志文件组成，默认为0.

***

# relay log（中继日志）

## 定义

relay-log中继日志是连接master和slave的核心.

***