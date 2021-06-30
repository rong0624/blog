---
title: MySQL日志系统
date: 2021-06-22 00:00:00
author: 神奇的荣荣
summary: ""
tags: MySQL
categories: MySQL
---

# MySQL日志分类

注意：  
设置系统参数方式修改，需要重新连接一个会话，如mysql重启即失效，如果要永久存在则需要修改配置文件。  
如果想要当前会话生效通过set sesison进行设置。

## 日志分类

Mysql有7种日志文件，分别是：  
1）errorlog（错误日志）  
2）generallog（普通日志）  
3）slow query log（慢查询日志）  
4）binlog（二进制日志）  
5）relaylog（中继日志）  
6）redolog（重做日志）  
7）undolog（回滚日志）

***

<!-- more -->

## 重要日志

slow query log：慢查询日志  
undolog-redolog：事务日志（innoDB存储引擎日志）  
binlog：二进制日志（server层日志）  
relaylog：中继日志（主从复制）

***

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

命令：
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

命令：
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

问：开启慢查询日志后，什么 sql 会被记录到慢查询日志里面呢？  
答：慢查询日志主要记录【响应时间超过阀值的sql语句】或【没有使用索引】的查询语句。

### 记录响应时间超过阀值的sql语句

时间阈值是由 long_query_time 控制的.  
long_query_time：设置慢查询的时间阈值，默认阈值是10s。可以使用命令修改，也可以在my.cnf参数里面修改。

命令：
```sql
show variables LIKE 'long_query_time%'; #查看long_query_time的值
set global long_query_time=5; #设置查询超过5秒则算慢查询sql
```

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
set global long_query_time=3; #设置查询超过3秒则算慢查询sql（注意，这里是全局命令设置，需要重新连接才生效）

select sleep(4) #模拟一次查询，查询耗时4秒
show global status like '%slow_queries%'; #查询当前慢查询sql条数命令
```

去mysql的data目录下找到慢查询日志文件：  
我没有去配置日志文件名，所以是一个默认的文件名：localhost-slow.log  
![慢查询日志图片](https://rong0624.github.io/images/MySQL/1624261484667.jpg)

可以看到当前慢查询日志中会记录查询超过了阈值的sql，我们刚刚的select sleep(4)就在当中，而且可以明确的看到当前sql，当前查询时间，锁的时间，一共有多少数据。

***

## 日志查询分析器（mysqldumpslow）

日志查询分析器的体现：  
在生产环境中，如果要手工分析日志，查找、分析SQL，显然是个体力活，MySQL提供了日志分析工具mysqldumpslow。  
![日志查询分析器帮助信息图片](https://rong0624.github.io/images/MySQL/日志查询分析器使用.jpg)

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

# bin log（二进制日志）

## 定义

bin log 是 MySQL 的二进制文件，也叫归档日志，是Mysql Server层记录的。  
主要记录 MySQL 数据库中的所有更新操作，如：use，insert，delete，update，create，alter，drop等操作。不改变数据的sql不会记录，比如 select 语句一般不会被记录，因为他们不会对数据产生任何改动。  

**用一句更简介易懂的话概况就是：所有涉及数据变动的操作，都会记录到二进制日志文件中。**

***

## 应用场景

1）数据恢复  
做数据恢复。因为binlog详细的记录了所有修改数据的sql，在某个时间段因操作导致数据出现问题，或数据库党纪数据丢失，那么就可以通过binlog来恢复历史数据。
2）mysql主从复制  
做数据备份和读写分离。在 master 端开启 bin log，master 把它的二进制日志传递给 slaves 来达到master-slave数据一致的目的。  

***

## 二进制日志文件常用操作命令

1）查看是否启动bin log 日志
show variables like 'log_bin';
```
mysql> show variables like 'log_bin';
+---------------+-------+
| Variable_name | Value |
+---------------+-------+
| log_bin       | OFF   |
+---------------+-------+
1 row in set (0.00 sec)
```

2）查看binlog的目录  
show global variables like "%log_bin%";


3）查看主库的日志文件，以及position信息
show master logs;
```
mysql> show master logs;
+------------------+-----------+
| Log_name         | File_size |
+------------------+-----------+
| mysql-bin.000001 |       314 |
| mysql-bin.000002 |       639 |
+------------------+-----------+
2 rows in set (0.00 sec)
```

4）查看master状态，即最后（最新）一个bin log日志的编号名称，及其最后一个操作事件pos结束点(Position)值。  
show master status;
```
mysql> show master status;
+------------------+----------+--------------+------------------+
| File             | Position | Binlog_Do_DB | Binlog_Ignore_DB |
+------------------+----------+--------------+------------------+
| mysql-bin.000002 |      639 |              |                  |
+------------------+----------+--------------+------------------+
1 row in set (0.00 sec)
```

5）flush 刷新log日志，自此刻开始产生一个新编号的bin log日志文件;  
flush logs;  
注意：每当mysqld服务重启时，会自动执行此命令，刷新bin log日志；在mysqlddump备份数据时加-F选项也会刷新bin log日志；

6）重置（清空）所有bin log日志;  
reset master;

***

## 开启bin log

注意：mysql 8.0 版本之前，默认不开启，建议开启。

查看二进制日志的当前配置：  
可以看到，二进制日志默认是不开启的
```
mysql> show variables like 'log_bin';
+---------------+-------+
| Variable_name | Value |
+---------------+-------+
| log_bin       | OFF   |
+---------------+-------+
1 row in set (0.00 sec)
```

修改配置文件：(永久开启)  
修改 my.cnf 文件，在 [mysqld] 下增加或修改参数
```
[mysqld] 
log-bin=mysql-bin
server-id=001
```

修改配置文件后，重启服务配置生效  
查看 bin log 日志文件  
![bin log 日志文件](https://rong0624.github.io/images/MySQL/1624350274231.jpg)

***

## bin log 的写入时机

对支持事务的引擎如InnoDB而言，必须要提交了事务才会记录binlog。binlog 什么时候刷新到磁盘跟参数 sync_binlog 相关。

sync_binlog参数讲解：  
1）如果设置为0，则表示MySQL不控制binlog的刷新，由文件系统去控制它缓存的刷新；  
2）如果设置为不为0的值，则表示每 sync_binlog 次事务，MySQL调用文件系统的刷新操作刷新binlog到磁盘中。  
3）设为1是最安全的，在系统故障时最多丢失一个事务的更新，但是会对性能有所影响。

如果 sync_binlog=0 或 sync_binlog大于1，当发生电源故障或操作系统崩溃时，可能有一部分已提交但其binlog未被同步到磁盘的事务会被丢失，恢复程序将无法恢复这部分事务。

在MySQL 5.7.7之前，默认值 sync_binlog 是0，MySQL 5.7.7和更高版本使用默认值1，这是最安全的选择。一般情况下会设置为1或者0，牺牲一定的一致性来获取更好的性能。

***

## bin log 文件以及扩展

二进制日志包含两种文件：  
* 二进制日志索引文件（文件名后缀.index），用于记录索引的二进制文件  
* 二进制日志文件（文件名后缀为.00000*）记录数据库所有的DDL和DML语句事件

binlog是一个二进制文件集合，每个binlog文件以一个4字节的魔数开头，接着是一组Events:  
* 魔数：0xfe62696e对应的是0xfebin； 
* Event：每个Event包含header和data两个部分；header提供了Event的创建时间，哪个服务器等信息，data部分提供的是针对该* Event的具体信息，如具体数据的修改；
* 第一个Event用于描述binlog文件的格式版本，这个格式就是event写入binlog文件的格式；
* 其余的Event按照第一个Event的格式版本写入；
* 最后一个Event用于说明下一个binlog文件；
* binlog的索引文件是一个文本文件，其中内容为当前的binlog文件列表

当遇到以下3种情况时，MySQL会重新生成一个新的日志文件，文件序号递增：
* MySQL服务器停止或重启时
* 使用 flush logs 命令；
* 当 binlog 文件大小超过 max_binlog_size 变量的值时；

max_binlog_size 的最小值是4096字节，最大值和默认值是 1GB (1073741824字节)。事务被写入到binlog的一个块中，所以它不会在几个二进制日志之间被拆分。因此，如果你有很大的事务，为了保证事务的完整性，不可能做切换日志的动作，只能将该事务的日志都记录到当前日志文件中，直到事务结束，你可能会看到binlog文件大于 max_binlog_size 的情况。

注意：bin log与数据库文件在同目录中。  

***

## bin log 的日志格式

记录在二进制日志中的事件的格式取决于二进制记录格式。支持三种格式类型：
* STATEMENT：基于SQL语句的复制（statement-based replication, SBR）
* ROW：基于行的复制（row-based replication, RBR）
* MIXED：混合模式复制（mixed-based replication, MBR）

**注意：在 MySQL 5.7.7 之前，默认的格式是 STATEMENT，在 MySQL 5.7.7 及更高版本中，默认值是 ROW。日志格式通过 binlog-format 指定，如 binlog-format=STATEMENT、binlog-format=ROW、binlog-format=MIXED。**

### Statement

每一条会修改数据的sql都会记录在binlog中

优点：不需要记录每一行的变化，减少了binlog日志量，节约了IO, 提高了性能。

缺点：由于记录的只是执行语句，为了这些语句能在slave上正确运行，因此还必须记录每条语句在执行的时候的一些相关信息，以保证所有语句能在slave得到和在master端执行的时候相同的结果。另外mysql的复制，像一些特定函数的功能，slave与master要保持一致会有很多相关问题。

### Row

5.1.5版本的MySQL才开始支持 row level 的复制,它不记录sql语句上下文相关信息，仅保存哪条记录被修改。

优点： binlog中可以不记录执行的sql语句的上下文相关的信息，仅需要记录那一条记录被修改成什么了。所以row的日志内容会非常清楚的记录下每一行数据修改的细节。而且不会出现某些特定情况下的存储过程，或function，以及trigger的调用和触发无法被正确复制的问题.

缺点:所有的执行的语句当记录到日志中的时候，都将以每行记录的修改来记录，这样可能会产生大量的日志内容。

注：将二进制日志格式设置为ROW时，有些更改仍然使用基于语句的格式，包括所有DDL语句，例如CREATE TABLE， ALTER TABLE，或 DROP TABLE。

### Mixed

从5.1.8版本开始，MySQL提供了Mixed格式，实际上就是Statement与Row的结合。  
在Mixed模式下，一般的语句修改使用statment格式保存binlog，如一些函数，statement无法完成主从复制的操作，则采用row格式保存binlog，MySQL会根据执行的每一条具体的sql语句来区分对待记录的日志形式，也就是在Statement和Row之间选择一种。

### bin log企业模式的选择

互联网公司使用MySQL的功能较少（不用存储过程、触发器、函数），选择默认的Statement level；
用到MySQL的特殊功能（存储过程、触发器、函数）则选择Mixed模式；
用到MySQL的特殊功能（存储过程、触发器、函数），又希望数据最大化一直则选择Row模式；

## 查看二进制日志文件

bin log是二进制文件，普通文件查看器cat、more、vim等都无法打开，必须使用自带的 mysqlbinlog 命令查看。  

### mysqlbinlog 工具查看

mysqlbinlog 是 MySQL 中自带的工具，具体位置在 MySQL 的 bin 目录下。  
在Mysql5.5以下版本使用mysqlbinlog命令时如果报错，就加上"--no-defaults"选项

mysqlbinlog 使用语法：
```bash
# mysqlbinlog 的执行格式
mysqlbinlog [options] log_file ...
-s                          以精简的方式显示日志内容
-v                          以详细的方式显示日志内容
-d=数据库名                  只显示指定数据库的日志内容
-o=n                        忽略日志中前n行MySQL命令
-r=file                     将指定内容写入指定文件
--start-datetime  
                            显示指定时间范围内的日志内容
--stop-datetime         

--start-position        
                            显示指定位置间隔内的日志内容
--stop-position     

# 查看bin-log二进制文件（shell方式）
mysqlbinlog -v --base64-output=decode-rows /var/lib/mysql/master.000003

# 查看bin-log二进制文件（带查询条件）
mysqlbinlog -v --base64-output=decode-rows /var/lib/mysql/master.000003 \
    --start-datetime="2019-03-01 00:00:00"  \
    --stop-datetime="2019-03-10 00:00:00"   \
    --start-position="5000"    \
    --stop-position="20000"
```

查看二进制日志文件：mysqlbinlog -v --base64-output=decode-rows mysql-bin.000002 
```
# at 391
#210622 17:06:40 server id 1  end_log_pos 501   Query   thread_id=2     exec_time=0     error_code=0
SET TIMESTAMP=1624352800/*!*/;
insert into admin_info values(1, "admin", 100) #执行的sql
/*!*/;
```
解释：  
* position: 位于文件中的位置，即第一行的（# at 391）,说明该事件记录从文件第391个字节开始
* timestamp: 事件发生的时间戳，即第二行的（#210622 17:06:40）
* server id: 服务器标识（1）
* end_log_pos 表示下一个事件开始的位置（即当前事件的结束位置+1）
* thread_id: 执行该事件的线程id （thread_id=2）
* exec_time: 事件执行的花费时间
* error_code: 错误码，0意味着没有发生错误
* type：事件类型Query

### 命令查看

mysqlbinlog 查看取出 bin log 日志的全文内容比较多，不容易分辨查看到pos点信息  
介绍一种更为方便的查询命令 show bin log events

命令解析 show bin log events [IN 'log_name'] [FROM pos] [LIMIT [offset,] row_count];    
参数解析：  
a、IN 'log_name':指定要查询的bin log文件名（不指定就是第一个bin log文件  
b、FROM pos:指定从哪个pos起始点开始查起（不指定就是从整个文件首个pos点开始算）  
c、LIMIT【offset】：偏移量(不指定就是0)  
d、row_count :查询总条数（不指定就是所有行）  

show bin log events查询：
```
mysql> show bin log events in'mysql-bin.000002';
+------------------+-----+-------------+-----------+-------------+---------------------------------------------------------------------------------+
| Log_name         | Pos | Event_type  | Server_id | End_log_pos | Info                                                                            |
+------------------+-----+-------------+-----------+-------------+---------------------------------------------------------------------------------+
| mysql-bin.000002 |   4 | Format_desc |         1 |         107 | Server ver: 5.5.62-log, bin log ver: 4                                           |
| mysql-bin.000002 | 107 | Query       |         1 |         192 | create database admin                                                           |
| mysql-bin.000002 | 192 | Query       |         1 |         322 | use `admin`; create table admin_info(id int(11), name varchar(50), age int(11)) |
| mysql-bin.000002 | 322 | Query       |         1 |         391 | BEGIN                                                                           |
| mysql-bin.000002 | 391 | Query       |         1 |         501 | use `admin`; insert into admin_info values(1, "admin", 100)                     |
| mysql-bin.000002 | 501 | Xid         |         1 |         528 | COMMIT /* xid=7 */                                                              |
| mysql-bin.000002 | 528 | Query       |         1 |         639 | use `admin`; create table role(id int(11), name varchar(25))                    |
+------------------+-----+-------------+-----------+-------------+---------------------------------------------------------------------------------+
7 rows in set (0.00 sec)
```

***

## 利用二进制日志恢复数据

// TODO

***

# relay log（中继日志）

## 定义

// TODO

# MySQL 事务日志（redolog & undolog）

请看MySQL事务日志分析博客