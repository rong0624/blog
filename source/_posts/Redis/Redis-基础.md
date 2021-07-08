---
title: Redis-基础
date: 2020-06-20 00:00:00
author: 神奇的荣荣
summary: ""
categories: Redis
tags: 
    - Redis
    - 中间件
---

# 入门概述

## Redis是什么？

是完全开源免费的，用C语言编写的，遵守BSD协议，是一个高性能的(key/value)分布式内存数据库，基于内存运行，并支持持久化的NoSQL数据库，是当前最热门的NoSql数据库之一，也被人们称为数据结构服务器。 

redis 的官网：redis.io

## Redis 与其他 key/value 缓存产品有以下三个特点

1）Redis支持数据的持久化，可以将内存中的数据保持在磁盘中，重启的时候可以再次加载进行使用  
2）Redis不仅仅支持简单的key-value类型的数据，同时还提供list，set，zset，hash等数据结构的存储  
3）Redis支持数据的备份，即master-slave模式的数据备份

## 应用场景（redis能干些什么）

1）内存存储和持久化：redis支持异步将内存中的数据写到硬盘上，同时不影响继续服务  
2）取最新N个数据的操作，如：可以将最新的10条评论的ID放在Redis的List集合里面  
3）模拟类似于HttpSession这种需要设定过期时间的功能  
4）发布、订阅消息系统（消息中间件）  
5）定时器、计数器  
6）可以实现session共享  
7）可以实现分布式锁

# Redis的安装

提示：  
由于企业里面做Redis开发，99%都是Linux版的运用和安装。
几乎不会涉及到Windows版，windows安装只是为了学习而已了。

# Redis启动后的杂项知识（重要）

1）单进程，redis速度很快。  
redis读写性能测试，redis官网测试读写能到10万左右。

2）默认16个数据库，类似数组下表从零开始，初始默认使用零号库。

3）select命令切换数据库。

4）dbsize查看当前数据库的key的数量。

5）flushdb：清空当前库key。

6）flushall：清空全部库。

7）统一密码管理，16个库都是同样密码，要么都OK要么一个也连接不上。

8）redis索引都是从零开始。

9）redis默认端口是6379。

# Redis五大数据类型

常用命令：   
ping：ping下redis    
dbsize：查看当前数据库的key的数量  
select 1：切换到下标为1的数据库中  
flushdb：清空当前库key  
flushall：清空全部库key

## Redis键的操作（常用）

查看当前数据库的所有key:  
Keys *

判断当前key是否存在：  
exists name

将当前key移动到2号库中：  
Move name 2

设置key在6秒后过期：  
expire name 6

查看当前key还有多久过期  
ttl name 

查看当前key是什么结构的类型  
type name

## String 类型（常用）

### 简介

String是redis最基本的类型，可以理解成一个key对应一个value。  
String类型是二进制安全的，意思是redis的string可以包含任何数据，比如jpg图片或者序列化对象。  
redis一个字符串value最多可以是512M。

### 操作

```
set name oyr：			给键name设置值为oyr

get name：				获取键name的值

del name：				删除建为name值

append name 123：		在name对应的值后面追加123

strlen name：			得到当前name对应的值的长度

incr age：				age+1，一定要是数字才能操作

incrby age 10：			age+10

decr age：				age-1，一定要是数字才能操作

decrby age 10： 		age-10

setex name 10 oyr：		
（set with expire）
设置key为name，过期时间为10秒，值为oyr

sexnx name ooo：
（sex if not exist）
设置键位name，值为ooo，只有不存在的时候才会设置进去

mset k1 v1 k2 v2 k3 v3：		一次设置多个值

mget k1 k2 k3：					一次获取多个值

msetnx k1 v1 k2 v2 k3 v3：		一次设置多个值，如果有一个键是存在的那么全部失效。
```

## Hash 类型（常用）

### 简介

Redis hash 是一个键值对集合。  
Redis hash是一个string类型的field和value的映射表，hash特别适合用于存储对象。  
类似Java里面的Map<String,Object>，K-V模式不变，但V是一个键值对。

### 操作

```
（1）hset
hset user name oyr			给键user的name属性设置值

（2）hget
hget user name				获取键user的name值

（3）hmset
hmset user age 18 sex nan  		同时设置多个值

（4）hmget
hmget user name age sex			同时获取多个属性值

（5）hgetall
hgetall user 					获取键user中的所有键和值

（6）hdel
hdel user name					删除键user中的name属性

（7）hlen
hlen user						获取键user下有几个属性

（8）hexists
hexists user nane				判断键user下是否有name属性

（9）hkeys
hkys user						获取键user下的所有属性

（10）hvals
hvals user 					 	获取键user下的所有值

（11）hsetnx
hsetnx user name "oyr"			如果user对象里存在 name 属性，则不做操作，不存在，创建并赋值。

（12）hincrby 
hincrby  user age 10			给键user里的age 属性添加10
```

## Lists 类型（双向链表）

### 简介

Redis 列表是简单的字符串列表，按照插入顺序排序。你可以添加一个元素导列表的头部（左边）或者尾部（右边）。  
它的底层实际是个链表，而且是双向链表。注意：先进后出，后进先出

### 操作

```
lpush list1 a b c d			给list1链表添加数据a b c d,从头部添加

lrange list1 0 -1			获取list1链表所有数据

rpush list2 1 2 3			给链表list2尾部插入元素

lpop list1					出栈 mylist，出栈后，元素消失（从头部出）

rpop list1					出栈 mylist，出栈后，元素消失（从尾部出）

lindex list1 3				获取链表的3索引位置的值

	list1					获取list1 链表的长度

lset list2 2 s				给链表索引2的位置设置值为 s

lrem list3 2 d				删除链表2个d元素

ltrim list2 2 5			从索引2截取到索引5，其他元素被遗弃

linsert list2 before/after s u		在链表f元素前面或者后面插入 h 元素

rpoplpush list1 list2 		从list1链表中尾部出站，出栈元素添加给list2链表
```

### 性能总结

它是一个字符串链表，left、right都可以插入添加；  
如果键不存在，创建新的链表；  
如果键已存在，新增内容；  
如果值全移除，对应的键也就消失了。  
链表的操作无论是头和尾效率都极高，但假如是对中间元素进行操作，效率就很惨淡了。

## Set 类型（不能重复）

### 简介

Redis的Set是string类型的无序集合。它是通过HashTable实现实现的，

### 操作

```
sadd set1 a b c d 				给set1集合添加元素

smembers set1					查看set1集合的所有元素

sismember set1 a				判断set1集合中是否有a元素
scard set1						获取集合set1 的元素个数

srem set1 a						删除集合set1中的元素（可以一次删除多个）

srandmember set2 3				在集合set2中随机出3个元素

diff set3 set4					差集，取set3中存在但是set4中不存在的元素

sinter set3 set4				交集，取set3和set4都存在的元素

sunion set3 set4				并集
```

## Sortedsets 类型

### 简介

zset 和 set 一样也是string类型元素的集合,且不允许重复的成员。  
不同的是每个元素都会关联一个double类型的分数。  
redis正是通过分数来为集合中的成员进行从小到大的排序。zset的成员是唯一的,但分数(score)却可以重复。

### 操作

```
zadd zset1 60 a 70 b 80 c 90 d 100 f	给zset1 有序集合设置元素，同时设置元素分数。

zrange zset1 0 -1 withscores		查询集合所有元素,0:开始,-1:结束,withscores显示分数

zrange zset1 0 2						查询集合下标0到下标2的元素

zcount zset1 70 90 						统计分数在 70 到 90 之间元素，闭区间。

zcount zset1 (70 90 		    统计分数在70到90之间元素，左边开区间，右边闭区间

zcount zset1 -inf +inf		    统计所有元素  -inf:最小值 +inf:最大值

zrangebyscore mysset 12 19 withscores limit 0 1     根据分数查询12到19集合，从坐标0开始。每页显示1条，12-19都是闭区间

zrem zset1 a b			集合删除元素a b
```

# Redis配置文件

## Redis配置文件在哪?

redis.conf是redis的配置文件，它在那？
在redis安装包解压出来的目录下。

## 常用配置（redis.conf）

```
参数说明
redis.conf 配置项说明如下：
1. Redis默认不是以守护进程的方式运行，可以通过该配置项修改，使用yes启用守护进程
  daemonize no
2. 当Redis以守护进程方式运行时，Redis默认会把pid写入/var/run/redis.pid文件，可以通过pidfile指定
  pidfile /var/run/redis.pid
3. 指定Redis监听端口，默认端口为6379，作者在自己的一篇博文中解释了为什么选用6379作为默认端口，因为6379在手机按键上MERZ对应的号码，而MERZ取自意大利歌女Alessia Merz的名字
  port 6379
4. 绑定的主机地址
  bind 127.0.0.1
5.当 客户端闲置多长时间后关闭连接，如果指定为0，表示关闭该功能
  timeout 300
6. 指定日志记录级别，Redis总共支持四个级别：debug、verbose、notice、warning，默认为verbose
  loglevel verbose
7. 日志记录方式，默认为标准输出，如果配置Redis为守护进程方式运行，而这里又配置为日志记录方式为标准输出，则日志将会发送给/dev/null
  logfile stdout
8. 设置数据库的数量，默认数据库为0，可以使用SELECT <dbid>命令在连接上指定数据库id
  databases 16
9. 指定在多长时间内，有多少次更新操作，就将数据同步到数据文件，可以多个条件配合
  save <seconds> <changes>
  Redis默认配置文件中提供了三个条件：
  save 900 1
  save 300 10
  save 60 10000
  分别表示900秒（15分钟）内有1个更改，300秒（5分钟）内有10个更改以及60秒内有10000个更改。
 
10. 指定存储至本地数据库时是否压缩数据，默认为yes，Redis采用LZF压缩，如果为了节省CPU时间，可以关闭该选项，但会导致数据库文件变的巨大
  rdbcompression yes
11. 指定本地数据库文件名，默认值为dump.rdb
  dbfilename dump.rdb
12. 指定本地数据库存放目录
  dir ./
13. 设置当本机为slav服务时，设置master服务的IP地址及端口，在Redis启动时，它会自动从master进行数据同步
  slaveof <masterip> <masterport>
14. 当master服务设置了密码保护时，slav服务连接master的密码
  masterauth <master-password>
15. 设置Redis连接密码，如果配置了连接密码，客户端在连接Redis时需要通过AUTH <password>命令提供密码，默认关闭
  requirepass foobared
16. 设置同一时间最大客户端连接数，默认无限制，Redis可以同时打开的客户端连接数为Redis进程可以打开的最大文件描述符数，如果设置 maxclients 0，表示不作限制。当客户端连接数到达限制时，Redis会关闭新的连接并向客户端返回max number of clients reached错误信息
  maxclients 128
17. 指定Redis最大内存限制，Redis在启动时会把数据加载到内存中，达到最大内存后，Redis会先尝试清除已到期或即将到期的Key，当此方法处理 后，仍然到达最大内存设置，将无法再进行写入操作，但仍然可以进行读取操作。Redis新的vm机制，会把Key存放内存，Value会存放在swap区
  maxmemory <bytes>
18. 指定是否在每次更新操作后进行日志记录，Redis在默认情况下是异步的把数据写入磁盘，如果不开启，可能会在断电时导致一段时间内的数据丢失。因为 redis本身同步数据文件是按上面save条件来同步的，所以有的数据会在一段时间内只存在于内存中。默认为no
  appendonly no
19. 指定更新日志文件名，默认为appendonly.aof
   appendfilename appendonly.aof
20. 指定更新日志条件，共有3个可选值： 
  no：表示等操作系统进行数据缓存同步到磁盘（快） 
  always：表示每次更新操作后手动调用fsync()将数据写到磁盘（慢，安全） 
  everysec：表示每秒同步一次（折衷，默认值）
  appendfsync everysec
 
21. 指定是否启用虚拟内存机制，默认值为no，简单的介绍一下，VM机制将数据分页存放，由Redis将访问量较少的页即冷数据swap到磁盘上，访问多的页面由磁盘自动换出到内存中（在后面的文章我会仔细分析Redis的VM机制）
   vm-enabled no
22. 虚拟内存文件路径，默认值为/tmp/redis.swap，不可多个Redis实例共享
   vm-swap-file /tmp/redis.swap
23. 将所有大于vm-max-memory的数据存入虚拟内存,无论vm-max-memory设置多小,所有索引数据都是内存存储的(Redis的索引数据 就是keys),也就是说,当vm-max-memory设置为0的时候,其实是所有value都存在于磁盘。默认值为0
   vm-max-memory 0
24. Redis swap文件分成了很多的page，一个对象可以保存在多个page上面，但一个page上不能被多个对象共享，vm-page-size是要根据存储的 数据大小来设定的，作者建议如果存储很多小对象，page大小最好设置为32或者64bytes；如果存储很大大对象，则可以使用更大的page，如果不 确定，就使用默认值
   vm-page-size 32
25. 设置swap文件中的page数量，由于页表（一种表示页面空闲或使用的bitmap）是在放在内存中的，，在磁盘上每8个pages将消耗1byte的内存。
   vm-pages 134217728
26. 设置访问swap文件的线程数,最好不要超过机器的核数,如果设置为0,那么所有对swap文件的操作都是串行的，可能会造成比较长时间的延迟。默认值为4
   vm-max-threads 4
27. 设置在向客户端应答时，是否把较小的包合并为一个包发送，默认为开启
  glueoutputbuf yes
28. 指定在超过一定的数量或者最大的元素超过某一临界值时，采用一种特殊的哈希算法
  hash-max-zipmap-entries 64
  hash-max-zipmap-value 512
29. 指定是否激活重置哈希，默认为开启（后面在介绍Redis的哈希算法时具体介绍）
  activerehashing yes
30. 指定包含其它的配置文件，可以在同一主机上多个Redis实例之间使用同一份配置文件，而同时各个实例又拥有自己的特定配置文件
  include /path/to/local.conf
```

# Redis的持久化

Redis的持久化一共有两种：  
rdb持久化  
aof持久化（推荐使用）

## RDB（Redis Database）

### 什么是rdb持久化

在指定的时间间隔内将内存中的数据集快照写入磁盘.   
也就是行话讲的Snapshot快照，它恢复时是将快照文件直接读到内存里。

Redis官方解释：  
Redis会单独创建（fork）一个子进程来进行持久化，会先将数据写入到一个临时文件中，
待持久化过程都结束了，再用这个临时文件替换上次持久化好的文件。  
整个过程中，主进程是不进行任何IO操作的，这就确保了极高的性能。
如果需要进行大规模数据的恢复，且对于数据恢复的完整性不是非常敏感，那RDB方
式要比AOF方式更加的高效。RDB的缺点是最后一次持久化后的数据可能丢失。
（因为可能出现redis宕机的情况）

### Fork的解释

fork的作用是复制一个与当前进程一样的进程。新进程的所有数据（变量、环境变量、程序计数器等）数值都和原进程一致，但是是一个全新的进程，并作为原进程的子进程


## AOF（Append Only File）

# Redis的事物

## 事物介绍

Redis 事务可以一次执行多个命令， 并且带有以下两个重要的保证：  
1）批量操作在发送 EXEC 命令前被放入队列缓存。  
收到 EXEC 命令后进入事务执行，事务中任意命令执行失败，其余的命令依然被执行。（并不回滚）  
2）在事务执行过程，其他客户端提交的命令请求不会插入到事务执行命令序列中。

**注意：redis单命令是原子性的，但redis事物并不是原子性。Redis事务可以理解为一个打包的批量执行脚本，但批量指令并非原子化的操作，中间某条指令的失败不会导致前面已做指令的回滚，也不会造成后续的指令不做。**

Redis的一个事务从开始到执行会经历以下三个阶段：
1）开始事务。
2）命令入队。
3）执行事务。

## 事物常用命令

```
multi						标记一个事物块的开始

exec：						执行所有事物块内的命令

discard： 					取消事物，放弃执行事物块的所有命令

watch key [key ...]：			
监视一个（或多个）key，如果在事物执行前这个(或这些) key 被其他命令所改动，那么事务将被打断。

unwatch						取消watch命令对所有key的监控
```

// TODO