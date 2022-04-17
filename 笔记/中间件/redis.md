##  什么是NoSQL

> NoSQL = Not Only SQL （不仅仅是SQL） 
>
> 泛指非关系型数据库

关系型数据库：表格

**NoSQL 特点**

1. 方便扩展（数据之间没有关系，很好扩展）
2. 大数据量高性能（NoSQL的缓存记录级，是一种细粒度的缓存）
3. 数据类型是多样型的（不需要事先设计数据库，随取随用）
4. 传统 RDBMS（关系型数据库）和 NoSQL区别

~~~bash
RDEMS
- 结构化组织
- SQL
- 数据和关系都存在单独的表中
- 操作(CRUD) 数据定义语言
- 严格的一致性
- 基础的事务
- .....
~~~

~~~bash
NoSql
- 不仅仅是数据
- 没有固定的查询语句
- 键值对存储，列存储、文档存储、图形数据库(社交关系)
- 最终一致性
- CAP定理 和 BASE (异地多活)
- 高性能、高可用、可拓展
- .....
~~~

#### NoSQL四大分类

**KV键值对：**

Redis...

**文档型数据库：**

- MongoDB
    - MongoDB 是一个基于分布式文件存储的数据库，主要用来处理大量的文档
    - MongoDB 是介于关系型数据库和非关系型数据库中间的产品！MongoDB是非关系型数据库中功能最丰富的，最像关系型数据库

**列存储数据库：**

- HBase
- 分布式文件系统

**图关系数据库：**

> 不是存图形，放的是关系。比如朋友圈社交网络，广告推荐

![](./images/redis01.png)

## Redis

>  Redis（**Re**mote **Di**ctionary **S**erver )，即远程字典服务。
>
> 是一个开源的使用 C语言编写、支持网络、可基于内存亦可持久化的日志型、Key-Value 数据库，并提供多种语言的API。

redis会周期性的把更新的数据写入磁盘或者把修改操作写入追加的记录文件，并且在此基础上实现了master-slave(主从)同步。

**Redis 能做什么**

> Redis是一个开源（BSD许可），内存存储的数据结构服务器，可用作**数据库**，**高速缓存**和**消息队列**代理。它支持字符串、哈希表、列表、集合、有序集合，位图，hyperloglogs等数据类型。内置复制、Lua脚本、LRU收回、事务以及不同级别磁盘持久化功能，同时通过Redis Sentinel提供高可用，通过Redis Cluster提供自动分区

1. 内存存储、持久化
2. 效率高，可以用于高速缓存
3. 发布订阅系统
4. 地图信息分析
5. 计时器、计数器（比如浏览量）
6. ........

[Redis 官网](https://redis.io/) 

[Redis 中文网](http://www.redis.cn/)

#### 安装

> 不推荐在 windows下使用，建议在linux下使用

[Redis 安装 | 菜鸟教程 (runoob.com)](https://www.runoob.com/redis/redis-install.html)

**windows**

1. 下载安装包：[redis ](https://github.com/tporadowski/redis/releases)              [Rides](https://github.com/microsoftarchive/redis)

2. 解压缩

3. 开启Redis： `redis-server.exe`

4. 使用 `redis-cli.exe` 来连接

    输入`ping` 来测试连通，返回`PONG` 则ok

**linux**

1. 下载获取安装包

2. 解压到 `/opt/` 

    ~~~bash
    mv redis-6.2.6.tar.gz /opt
    cd /opt
    tar -zxvf redis...
    ~~~

3. 基本环境安装

~~~bash
yum install gcc-c++

make

make install
~~~

4. redis的默认安装路径`/usr/local/bin/ ` 目录下
5. 在 `usr/local/bin/` 目录下创建 一个文件夹`zconfig` 将`redis.conf` 配置文件复制进来，保证以后有问题可以恢复
6. redis默认不是后台启动，修改配置文件，使其默认后台启动   `daemonize` 改为 yes

~~~bash
################################# GENERAL #####################################

# By default Redis does not run as a daemon. Use 'yes' if you need it.
# Note that Redis will write a pid file in /var/run/redis.pid when daemonized.
# When Redis is supervised by upstart or systemd, this parameter has no impact.
daemonize yes
~~~

7. 启动Redis服务

~~~bash
[root@zyh bin]# redis-server zconfig/redis.conf 
~~~

8. 测试连接

~~~shell
[root@zyh bin]# redis-cli -p 6379
127.0.0.1:6379> ping
PONG
127.0.0.1:6379> 
~~~

9. 关闭Redis服务

~~~shell
[root@zyh bin]# redis-cli -p 6379
127.0.0.1:6379> shutdown
not connected> exit
~~~

#### 性能测试

**redis-benchmaek**  是一个压力测试工具

![](images/redis02.png)

测试：

~~~shell
# 测试100 个并发，十万个请求
redis-benchmark -h localhost -p 6379 -c 100 -n 100000
~~~

~~~shell
100000 requests completed in 2.16 seconds	# 100000个请求完成时间
100 parallel clients						# 100个并发客户端
3 bytes payload								# 每次 3 个字节
keep alive: 1								# 一台服务器处理这些请求
~~~

#### 基础的知识

redis默认有 16 个数据库，默认使用的是第 0个

**select**			切换数据库

**DBSIZE** 		查看当前数据库大小

~~~shell
127.0.0.1:6379> select 1
OK
127.0.0.1:6379[1]> DBSIZE
(integer) 0
~~~

**keys**		查看所有的key

~~~shell
127.0.0.1:6379[1]> set name zhang
OK
127.0.0.1:6379[1]> set name1 zzz
OK
127.0.0.1:6379[1]> keys *
1) "name1"
2) "name"
~~~

**flushdb**  **flushall**	清空数据库

~~~shell
127.0.0.1:6379[1]> FLUSHDB	# 清空当前数据库
OK
127.0.0.1:6379> FLUSHALL	# 清空所有数据库
OK
~~~

#### 单/多线程

Redis使用单线程的原因：

1. IO多路复用
2. 维护性高，多线程会出现并发问题，并且线程切换会增加资源消耗
3. redis主要瓶颈是内存、网络，并且由于内存速度快，因此无需多线程

Redis引入多线程的原因：

> Redis在4.0就出现了多线程的概念，比如通过多线程在后台删除对象。Redis6.0正式引入多线程，但默认关闭

1. 充分利用CPU多核，6.0之前主线程只能利用一个核
2. 多线程任务可以分摊Redis同步IO读写负荷。

#### Redis-key

**exists**   判断某个key是否存在

~~~shell
127.0.0.1:6379> set name zhagn
OK
127.0.0.1:6379> EXISTS name			# 存在返回 1
(integer) 1
127.0.0.1:6379> EXISTS name1		# 不存在返回 0
(integer) 0
~~~

**move**     移动某个key到指定的数据库

~~~shell
127.0.0.1:6379> move name 1			# 移动到 1 号数据库
(integer) 1
~~~

**del**  		删除某个key

~~~shell
127.0.0.1:6379> del name			# 当前数据库删除这个key
(integer) 1
~~~

**rename** 	修改某个key

~~~shell
127.0.0.1:6379> rename name name1
OK
~~~

**expire**		设置某个存在的key多少**秒**过期

~~~shell
127.0.0.1:6379> EXPIRE name 10		# 设置十秒过期
(integer) 1
~~~

**ttl**  			查看给定的key剩余生存时间

~~~shell
127.0.0.1:6379> ttl name			
(integer) 8
127.0.0.1:6379> ttl name			# key不存在了
(integer) -2
~~~

**type**			查看key所存储的值的类型

~~~shell
127.0.0.1:6379> type name1
string
~~~

## 五大数据类型

#### String（字符串）

String类似的使用场景：value除了是字符串还可以是数字

- 计数器
- 统计多单位的数量

~~~shell
# set
# get
# append
# strlen
127.0.0.1:6379> set key1 v1				# 设置值
OK
127.0.0.1:6379> get key1				# 获取值
"v1"
127.0.0.1:6379> append key1 "hello"		# 桌架字符串，如果key不存在，就相当于 set
(integer) 7
127.0.0.1:6379> strlen key1				# 获取字符串的长度
(integer) 7
~~~

~~~shell
# i++	i--
# incr
# decr
# incrby
# decrby
127.0.0.1:6379> set i 0
OK
127.0.0.1:6379> incr i			# 自增 1
(integer) 1
127.0.0.1:6379> incr i			# 自增 1
(integer) 2
127.0.0.1:6379> decr i			# 自减 1
(integer) 1
127.0.0.1:6379> INCRBY i 10		# 自增 10
(integer) 11
127.0.0.1:6379> DECRBY i 5		# 自减 5
(integer) 6
~~~

~~~shell
# 字符串范围
# getrange
# setrange
127.0.0.1:6379> set key1 "hello"
OK
127.0.0.1:6379> getrange key1 0 3	# 截取字符串 [0,3]
"hell"
127.0.0.1:6379> GETRANGE key1 0 -1	# 和 get key1一样
"hello"
# 替换
127.0.0.1:6379> SETRANGE key1 1 xx	# 从指定位置开始替换字符串
(integer) 5
127.0.0.1:6379> get key1
"hxxlo"
~~~

~~~shell
# setex(set with expire)	# 设置过期时间
# setnx(set if not exist) 	# 不存在再设置(分布式锁中常常使用)
127.0.0.1:6379> setex key2 30 "test"	# 设置key2的值为 test，30秒后过期
OK
127.0.0.1:6379> ttl key2
(integer) 26
127.0.0.1:6379> setnx mykey "redis"		# 不存在mykey 所以成功了
(integer) 1
127.0.0.1:6379> setnx mykey "11111"		# 存在mykey 所以失败了
(integer) 0
~~~

~~~shell
# mset			# 批量设置
# mget			# 批量获取
# msetnx		# 不存在再设置，msetnx是一个原子性的操作，要么一起成功，要么一起失败
127.0.0.1:6379> mset k1 v1 k2 v2 k3 v3		# 同时设置多个值
OK
127.0.0.1:6379> mget k1 k2 k3				# 同时获取多个值
1) "v1"
2) "v2"
3) "v3"
127.0.0.1:6379> msetnx k1 v1 k4 v4			# k1 存在，所以失败了
(integer) 0
~~~

~~~shell
# getset  			# 先get然后再set
127.0.0.1:6379> getset key1 test	# 如果不存在值，则返回nil
(nil)
127.0.0.1:6379> get key1		
"test"
127.0.0.1:6379> getset key1 hello	# 如果存在值，获取原来的值，并设置新的值
"test"
127.0.0.1:6379> get key1
"hello"
~~~

#### List（列表）

> 在redis里面，可以将list变成 栈、队列、阻塞队列

命令是用 `L` 开头 只有push和pop才分左右，其余的不分

~~~shell
# lpush			# 将一个或多个值插入到列表头部
# rpush			# 在列表中添加一个或多个值
# lrange		# 获取列表指定范围内的元素
127.0.0.1:6379> lpush list1 one		# 插入到头部
(integer) 1
127.0.0.1:6379> lpush list1 two
(integer) 2
127.0.0.1:6379> lpush list1 three
(integer) 3
127.0.0.1:6379> lrange list1 0 -1
1) "three"
2) "two"
3) "one"
127.0.0.1:6379> rpush list1 four	# 插入到尾部
(integer) 4
127.0.0.1:6379> lrange list1 0 -1
1) "three"
2) "two"
3) "one"
4) "four"
~~~

~~~shell
# lpop				# 移出并获取列表的第一个元素 - 头
# rpop				# 移除并获取列表最后一个元素 - 尾
127.0.0.1:6379> lpop list1 1			# 移除头部
1) "three"
127.0.0.1:6379> rpop list1 1			# 移除尾部
1) "four"
127.0.0.1:6379> lrange list1 0 -1
1) "two"
2) "one"
~~~

~~~shell
# lindex            # 通过索引获取列表中的元素
# lrem				# 移除集合中指定个数的value
127.0.0.1:6379> lindex list1 0		
"two"
127.0.0.1:6379> lrem list1 1 one
(integer) 1
~~~

~~~shell
# llen				# 返回列表的长度
# ltrim				# 对一个列表进行修剪(trim)，就是说，让列表只保留指定区间内的元素，不在指定区间之内的元素都将被删除。
127.0.0.1:6379> LRANGE list1 0 -1
1) "five"
2) "four"
3) "three"
4) "two"
5) "one"
127.0.0.1:6379> ltrim list1 0 1		# 通过下标截取指定的长度
OK
127.0.0.1:6379> LRANGE list1 0 -1
1) "five"
2) "four"
~~~

~~~shell
# rpoplpush			# 	移除列表的最后一个元素，并将该元素添加到另一个列表并返回
127.0.0.1:6379> lpush key1 one two three four
(integer) 4
127.0.0.1:6379> rpoplpush key1 key2
"one"
127.0.0.1:6379> LRANGE key1 0 -1
1) "four"
2) "three"
3) "two"
127.0.0.1:6379> LRANGE key2 0 -1
1) "one"
~~~

~~~shell
# lset				# 通过索引设置列表元素的值，不存在会报错
127.0.0.1:6379> LRANGE list1 0 -1
1) "three"
2) "two"
3) "one"
127.0.0.1:6379> lset list1 0 test		# 将 list1 的索引 0 元素替换为 test
OK
127.0.0.1:6379> LRANGE list1 0 -1
1) "test"
2) "two"
3) "one"
~~~

~~~shell
# linsert			# 在列表的元素前或者后插入元素
127.0.0.1:6379> LRANGE list1 0 -1
1) "three"
2) "two"
3) "one"
127.0.0.1:6379> linsert list1 before two test	# 往 two 的前面(栈顶方向)插入
(integer) 4
127.0.0.1:6379> LRANGE list1 0 -1
1) "three"
2) "test"
3) "two"
4) "one"
~~~

**小结：**

- 实际上是一个链表，before Node after，left，right都可以插入值
- 如果key不存在，创建新的链表
- 如果key存在，新增内容
- 如果移除了所有值，变成了空链表，也就不存在了
- 在两边插入或者改动值，效率最高

lpush rpop	消息队列

lpush lpop	栈
