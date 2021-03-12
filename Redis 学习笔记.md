## Redis 学习笔记

**学习安排**

<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201125103956696.png" alt="image-20201125103956696" style="zoom:50%;" />

<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201125104018936.png" alt="image-20201125104018936" style="zoom:50%;" />

<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201125104139078.png" alt="image-20201125104139078" style="zoom:50%;" />

​      

### 1.  Nosql 概述



为什么要用nosql？

网站80%的情况下都是在读，每次都要去查询数据库的话十分麻烦！所以说我们希望减轻数据的压力，我们可以用缓存来保证效率！

**阶段一：** 分库分表  读写分离 垂直拆分

发展过程: 优化数据结构和索引 --> 文件缓存（IO）--> Memcached(当时最热技术)

![image-20201208160247116](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201208160247116.png)

​    

**阶段二**: 分库分表 水平拆分  mysql集群



早些年：MYISAM  ：            表锁   十分影响效率！高并发下就会出现严重的锁问题 

转战       Innodb     :             行锁

![image-20201208161119525](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201208161119525.png)



**阶段三**：如今

Mysql等关系型数据库不够用了！数据量很多，变化很快！

mysql有的用来存储一些比较大的文件，博客，图片！数据库表很大，效率就低了！

如果有一种数据库专门处理这种数据。可以缓解mysql的压力！

大数据的IO压力下，表几乎没法更大！

>目前一个基本的互联网项目



![image-20201208162722806](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201208162722806.png)



> 为什么要用NoSQL ？

用户的个人信息，社交网络，地理位置，用户自己产生的数据，用户日志等等爆发式增长！

这时候就需要使用nosql数据库。



> 什么是Nosql

Nosql = not only sql   不仅仅是sql  泛指非关系型数据库 

用户的个人信息，社交网络，地理位置。这些数据类型的存储不需要一个固定的格式！

> Nosql 特点

* 解耦
* 方便扩展（数据之间没有关系）
* 大数据量高性能  (Redis 一秒写8万次，读取11万)
* 细粒度的缓存
* 数据类型多样

> 传统的 RDBMS
>
> * 结构化组织
>
> * sql
>
> * 数据和关系都存在单独的表中
> * 数据操作，数据定义语言
> * 严格的一致性
> * 基础的事务

>Nosql
>
>- 不仅仅是数据
>- 没有固定的查询语言
>- 键值对存储，列存储，文档存储，图形数据库(社交关系)
>- 最终一致性
>- CAP定理和BASE      (异地多活）
>- 高性能 高可用 高可扩
>
>

 

```bash
# 1.商品的基本信息
    名称、价格、商家信息
    关系型数据库就可以解决
# 2.商品的描述、评论（文字比较多）
    文档型数据库，MongoDB
# 3.图片
    分布式文件系统 FastDFS
    -淘宝   TFS
    -Google GFS
    -Hadoop HDFS
    -阿里云  oss
    
# 4.商品的关键字（搜索）
   - 搜索引擎 solr elasticsearch
   - ISearch 阿里 多隆
# 5.商品热门波段信息
   - 内存数据库
   -redis  tair  memache

# 6.商品的交易，外部的支付接口
   - 第三方应用
```



#### 1.1 Nosql的四大类

**KV键值对:**

* Redis
* Tair

**文档型数据库（bson格式和json一样）：**

* MongoDB
  * MongoDB是一个基于分布式文件存储的数据库   主要用来处理大量的文档！
  * MongoDB是一个介于 关系型数据库 和 非关系型数据库中间的产品！MongoDB 是非关系型数据库中功能最丰富的，最想关系型数据库的

**列存储数据库：**

* HBase
* 分布式文件系统

**图关系数据库：**

* 存储的不是图片，而是关系。 比如： 朋友圈社交网络，广告推荐
* Neo4j,infoGrid

![image-20201209150219158](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201209150219158.png)



#### 1.2 Redis 入门

> Redis是什么？

Redis  (**Re**mote **Di**ctionary **S**erver)      **远程字典服务**

是一个开源的使用ANSI [C语言](https://baike.baidu.com/item/C语言)编写、支持网络、可基于内存亦可持久化的日志型、Key-Value[数据库](https://baike.baidu.com/item/数据库/103728)，并提供多种语言的API。

> Redis能干嘛？

1. 内存存储、持久化，内存中是断电即失，所以说持久化很重要。**（rdb、aof）**
2. 效率高，可以用于高速缓存
3. 发布订阅系统
4. 地图信息分析
5. 计时器、计数器（浏览量）

> 特性

1、多样的数据类型

2、持久化

3、集群

4、事务



#### 1.3  Linux安装Redis



1.下载安装包 放到服务器home下

2.解压安装包     程序简易放在opt下

![image-20201210150736788](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201210150736788.png)





得到redis文件

![image-20201210150815093](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201210150815093.png)





3.进入解压后的文件，可以看到redis的配置文件

![image-20201210151014802](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201210151014802.png)



4.基本的环境安装

```bash
在  cd redis目录下
1.yum install gcc-c++        对于redis6.0以下的版本


对于redis 6。0版本以上
#升级到 5.3及以上版本
yum -y install centos-release-scl
yum -y install devtoolset-9-gcc devtoolset-9-gcc-c++ devtoolset-9-binutils
 
scl enable devtoolset-9 bash
 
#注意：scl命令启用只是临时的，推出xshell或者重启就会恢复到原来的gcc版本。
#如果要长期生效的话，执行如下：
echo "source /opt/rh/devtoolset-9/enable" >>/etc/profile


在  cd redis目录下  
2.make

3.make install
```

执行成功

![image-20201210152152399](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201210152152399.png)

​     

第一次make需要很多依赖比较慢

再make一下就很快了

![image-20201210152333603](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201210152333603.png)



```bash
make install
```



![image-20201210152428973](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201210152428973.png)





5. redis的默认安装路径  usr/local/bin

![image-20201210152745527](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201210152745527.png)



6.将redis配置文件复制到当前目录下

先建一个目录方便管理



![image-20201210154928080](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201210154928080.png)



7. redis 默认不是后台启动的，要修改配置文件！



![image-20201210155723364](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201210155723364.png)





8.启动redis服务

​       cd ..

​       回到在/usr/local/bin 下  

```bash
redis-server cqpconfig/redis.conf
```



![image-20201210160134704](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201210160134704.png)





9. 用redis-cli客户端连接

```bash
redis-cli -p 6379
```

![image-20201210160436363](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201210160436363.png)





10.查看redis进程是否开启

  ```bash
ps -ef|grep redis
  ```



![image-20201210160632178](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201210160632178.png)



11. 关闭redis服务器



![image-20201210160807995](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201210160807995.png)



12.再次查看进程

 ```bash
ps -ef|grep redis
 ```



![image-20201210160814667](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201210160814667.png)





13.后面可以单机多redis启动集群测试





#### 1.4 性能测试

redis-benchmark 是一个压力测试工具

![image-20201210161325544](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201210161325544.png)



```bash
# 测试  100个并发  100000请求
redis-benchmark -h localhost -p 6379 -c 100 -n 100000
```



![image-20201210164400139](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201210164400139.png)



![image-20201210164510576](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201210164510576.png)



#### 1.5 基础知识

redis默认有16个数据库。

可以使用select切换数据库

```bash
127.0.0.1:6379> select 3
OK
127.0.0.1:6379[3]> DBSIZE
(integer) 0
127.0.0.1:6379[3]> set name chenqipeng
OK
127.0.0.1:6379[3]> DBSIZE
(integer) 1
127.0.0.1:6379[3]> select 7
OK
127.0.0.1:6379[7]> DBSIZE
(integer) 0
127.0.0.1:6379[7]> select 3
OK
127.0.0.1:6379[3]> get name
"chenqipeng"
127.0.0.1:6379[3]> keys *       查看当前数据库所有key
1) "name"

127.0.0.1:6379[3]> flushdb      清除当前数据库的数据
OK
127.0.0.1:6379[3]> keys *
(empty array)
127.0.0.1:6379[3]> flushall    清除所有数据库的内容
OK

```



> redis是单线程的。 那为什么redis还这么快?

因为多线程cpu会上下文切换，对于性能有影响。

redis是把所有的数据全部存放在**内存**中的，对于内存系统来说，没有上下文切换效率就是最高的，多次读写都是在一个cpu上。





### 2.Redis五大数据类型

Redis 是一个开源（BSD许可）的，内存中的数据结构存储系统，它可以用作数据库、缓存和消息中间件。 它支持多种类型的数据结构，如 [字符串（strings）](http://www.redis.cn/topics/data-types-intro.html#strings)， [散列（hashes）](http://www.redis.cn/topics/data-types-intro.html#hashes)， [列表（lists）](http://www.redis.cn/topics/data-types-intro.html#lists)， [集合（sets）](http://www.redis.cn/topics/data-types-intro.html#sets)， [有序集合（sorted sets）](http://www.redis.cn/topics/data-types-intro.html#sorted-sets) 与范围查询， [bitmaps](http://www.redis.cn/topics/data-types-intro.html#bitmaps)， [hyperloglogs](http://www.redis.cn/topics/data-types-intro.html#hyperloglogs) 和 [地理空间（geospatial）](http://www.redis.cn/commands/geoadd.html) 索引半径查询。 Redis 内置了 [复制（replication）](http://www.redis.cn/topics/replication.html)，[LUA脚本（Lua scripting）](http://www.redis.cn/commands/eval.html)， [LRU驱动事件（LRU eviction）](http://www.redis.cn/topics/lru-cache.html)，[事务（transactions）](http://www.redis.cn/topics/transactions.html) 和不同级别的 [磁盘持久化（persistence）](http://www.redis.cn/topics/persistence.html)， 并通过 [Redis哨兵（Sentinel）](http://www.redis.cn/topics/sentinel.html)和自动 [分区（Cluster）](http://www.redis.cn/topics/cluster-tutorial.html)提供高可用性（high availability）。

```bash
127.0.0.1:6379> expire name 10
(integer) 1
127.0.0.1:6379> ttl name
(integer) 5
127.0.0.1:6379> ttl name
(integer) 3
127.0.0.1:6379> ttl name
(integer) -2
127.0.0.1:6379> get name
(nil)
127.0.0.1:6379>
```

#### 2.1 String类型

append

```bash
########################################################        set get
127.0.0.1:6379> set key1 v1
OK
127.0.0.1:6379> append key1 "hello"
(integer) 7
127.0.0.1:6379> get k1
(nil)
127.0.0.1:6379> get key1
"v1hello"
127.0.0.1:6379>
########################################################       增加 减少  
127.0.0.1:6379> set views 0
OK
127.0.0.1:6379> get views
"0"
127.0.0.1:6379> incr views
(integer) 1
127.0.0.1:6379> incr views
(integer) 2
127.0.0.1:6379> get views
"2"
127.0.0.1:6379> decr views
(integer) 1
127.0.0.1:6379> decr views
(integer) 0
127.0.0.1:6379> decr views
(integer) -1
127.0.0.1:6379> get views
"-1"
127.0.0.1:6379> incrby views 10
(integer) 9 
########################################################  字符串范围
127.0.0.1:6379> set key1 "hello,chenqipeng"
OK
127.0.0.1:6379> get key1
"hello,chenqipeng"
127.0.0.1:6379> GETRANGE key1 0 4
"hello"
127.0.0.1:6379> GETRANGE key1 0 -1
"hello,chenqipeng"

127.0.0.1:6379> set key2 abcdefg
OK
127.0.0.1:6379> get key2
"abcdefg"
127.0.0.1:6379> SETRANGE key2 2 xxxxxxxx              #替换指定位置的字符串
(integer) 10
127.0.0.1:6379> get key2
"abxxxxxxxx"
127.0.0.1:6379>
######################################################## 
# setex (set with expire) 设置过期时间
# setnx (set if not exist) 不存在再设置  (在分布式锁中常常使用)   如果之前有就会设置不成功
127.0.0.1:6379> SETEX key3 30 "hello"
OK
127.0.0.1:6379> ttl key3
(integer) 20
127.0.0.1:6379> ttl key3
(integer) -2
127.0.0.1:6379> setnx mykey "redis"
(integer) 1
127.0.0.1:6379> setnx mykey "MongoDB"   #如果mykey存在，创建失败！
(integer) 0
127.0.0.1:6379> get mykey
"redis"
########################################################    批量设置值
127.0.0.1:6379> keys *
(empty array)
127.0.0.1:6379> mset k1 v1 k2 v2 k3 v3         # 批量设置值
OK
127.0.0.1:6379> mget k1 k2 k3                  # 批量获取
1) "v1"
2) "v2"
3) "v3"
127.0.0.1:6379> msetnx k1 v1 k4 v4             # k1已存在所以设置失败  msetnx是宇哥原子性的操作，要么一起成功，要么一起失败
(integer) 0
127.0.0.1:6379> get k4
(nil)
127.0.0.1:6379>
########################################################  对象

# 对象 
 set user:1 {name:zhangsan,age:3}   设置一个user:1 对象 值为json字符来保存一个对象！
127.0.0.1:6379> mset user:1:name zhangsan user:1:age 24
OK
127.0.0.1:6379> mget user:1:name user:1:age
1) "zhangsan"
2) "24"
 
########################################################     先拿值再set
127.0.0.1:6379> getset db redis
(nil)
127.0.0.1:6379> get db
"redis"
127.0.0.1:6379> getset db mongoDB
"redis"
127.0.0.1:6379> get db
"mongoDB"
127.0.0.1:6379>
```



#### 2.2 List类型

```bash
############################################
127.0.0.1:6379> LPUSH list one                   # 将一个值插入到列表头部 左边加入
(integer) 1
127.0.0.1:6379> LPUSH list two
(integer) 2
127.0.0.1:6379> LPUSH list three
(integer) 3
127.0.0.1:6379> LRANGE 0 -1
(error) ERR wrong number of arguments for 'lrange' command
127.0.0.1:6379> LRANGE list 0 -1
1) "three"
2) "two"
3) "one"
127.0.0.1:6379> LRANGE list 0 1
1) "three"
2) "two"
127.0.0.1:6379> RPUSH list r
(integer) 4
127.0.0.1:6379> LRANGE list 0 -1         # 将一个值插入到列表尾部 右边加入
1) "three"
2) "two"
3) "one"
4) "r"
############################################    移除
LPOP
RPOP

127.0.0.1:6379> LRANGE list 0 -1
1) "three"
2) "two"
3) "one"
4) "r"
127.0.0.1:6379> LPOP list
"three"
127.0.0.1:6379> RPOP list
"r"
127.0.0.1:6379> LRANGE list 0 -1
1) "two"
2) "one"
127.0.0.1:6379>
############################################ 
Lindex        # 通过下标获取值    Lindex list -1 获取最后一个值

127.0.0.1:6379> lindex list 1
"one"
127.0.0.1:6379> lindex list 0
"two"
127.0.0.1:6379>
############################################ 
Llen        #返回list长度

127.0.0.1:6379> Lpush list one
(integer) 1
127.0.0.1:6379> Lpush list two
(integer) 2
127.0.0.1:6379> Lpush list three
(integer) 3
127.0.0.1:6379> Llen list
(integer) 3
############################################  移除list指定个数的值，精确匹配。

#     取关 uid

Lrem 

127.0.0.1:6379> LRANGE list 0 -1
1) "three"
2) "three"
3) "two"
4) "one"
127.0.0.1:6379> Lrem list 1 one
(integer) 1
127.0.0.1:6379> LRANGE list 0 -1
1) "three"
2) "three"
3) "two"
127.0.0.1:6379> Lrem list 2 three    # 移除两个three
(integer) 2
127.0.0.1:6379> LRANGE list 0 -1
1) "two"
127.0.0.1:6379>
############################################   Ltrim 修剪   截取中间的数据


127.0.0.1:6379> Rpush mylist "hello"
(integer) 1
127.0.0.1:6379> Rpush mylist "hello2"
(integer) 2
127.0.0.1:6379> Rpush mylist "hello3"
(integer) 3
127.0.0.1:6379> Ltrim mylist 1 2
OK
127.0.0.1:6379> LRANGE mylist 0 -1
1) "hello2"
2) "hello3"
127.0.0.1:6379>
############################################ 
rpoplpush         #移除列表最后一个元素，将他移动到新的列表中

127.0.0.1:6379> rpush mylist "hello"
(integer) 1
127.0.0.1:6379> rpush mylist "hello1"
(integer) 2
127.0.0.1:6379> rpush mylist "hello2"
(integer) 3
127.0.0.1:6379> rpush mylist "hello3"
(integer) 4
127.0.0.1:6379> rpoplpush mylist myotherlist
"hello3"
127.0.0.1:6379> lrange mylist 0 -1
1) "hello"
2) "hello1"
3) "hello2"
127.0.0.1:6379> lrange myotherlist 0 -1
1) "hello3"
127.0.0.1:6379>
############################################ 
lset 设置指定位置的值（覆盖）   如果不存在就报错 

127.0.0.1:6379> exists list
(integer) 0
127.0.0.1:6379> lset list 0 item
(error) ERR no such key
127.0.0.1:6379> lpush list v1
(integer) 1
127.0.0.1:6379> lrange list 0 0
1) "v1"
127.0.0.1:6379> lset list 0 item
OK
127.0.0.1:6379> lrange list 0 0
1) "item"
############################################   在指定数据前或者后增加

127.0.0.1:6379> Lrange mylist 0 -1
1) "hello"
2) "world"
127.0.0.1:6379> LINSERT mylist before "world" "my"
(integer) 3
127.0.0.1:6379> Lrange mylist 0 -1
1) "hello"
2) "my"
3) "world"
127.0.0.1:6379> LINSERT mylist after "world" "!"
(integer) 4
127.0.0.1:6379> Lrange mylist 0 -1
1) "hello"
2) "my"
3) "world"
4) "!"
127.0.0.1:6379>




############################################ 

```



> 小结

* 实际上是一个链表
* 消息队列 （LPush Rpop）, 栈 (Lpush Lpop)



#### 2.3 Set（集合）

set中的值是不能重复的！

set 无序不重复集合！

```bash
######################################## 
127.0.0.1:6379> sadd myset "hello"                #  set集合中添加元素
(integer) 1
127.0.0.1:6379> sadd myset "chenqipeng"
(integer) 1
127.0.0.1:6379> sadd myset "iu"
(integer) 1
127.0.0.1:6379> sadd myset "pikachu"
(integer) 1
127.0.0.1:6379> SMEMBERS myset           #  查看指定set的所有值
1) "hello"
2) "pikachu"
3) "chenqipeng"
4) "iu"
127.0.0.1:6379>
127.0.0.1:6379> SISMEMBER myset hello      #  判断某一个值是不是在set集合中
(integer) 1
127.0.0.1:6379> SISMEMBER myset cqp
(integer) 0
####################################

127.0.0.1:6379> scard myset                # 获得set集合中的元素个数
(integer) 4
####################################
127.0.0.1:6379> Srem myset hello          # 移除
(integer) 1
127.0.0.1:6379> SMEMBERS myset
1) "pikachu"
2) "chenqipeng"
3) "iu"
127.0.0.1:6379>
####################################
127.0.0.1:6379> SRANDMEMBER myset        # 随机选一个数据     抽奖系统
"iu"
127.0.0.1:6379> SRANDMEMBER myset
"iu"
127.0.0.1:6379> SRANDMEMBER myset
"pikachu"
127.0.0.1:6379> SRANDMEMBER myset
"pikachu"
####################################
127.0.0.1:6379> SMEMBERS myset           # 随机删除一个数据   set本身就是无序的 所以删除是随机的
1) "pikachu"
2) "chenqipeng"
3) "iu"
127.0.0.1:6379> spop myset
"pikachu"
127.0.0.1:6379> spop myset
"chenqipeng"
127.0.0.1:6379> SMEMBERS myset
1) "iu"
127.0.0.1:6379>
####################################    将一个指定的值，移动到另外一个set集合
127.0.0.1:6379> sadd myset "hello"
(integer) 1
127.0.0.1:6379> sadd myset "world"
(integer) 1
127.0.0.1:6379> sadd myset "chenqipeng"
(integer) 1
127.0.0.1:6379> smove myset myset2 "chenqipeng"
(integer) 1
127.0.0.1:6379> SMEMBERS myset
1) "world"
2) "hello"
127.0.0.1:6379> SMEMBERS myset2
1) "chenqipeng"
127.0.0.1:6379>
####################################                                
微博 b站 共同关注  （交集）

A用户将所有关注的人放在一个set集合中！粉丝也是！

127.0.0.1:6379> sadd set1 a   
(integer) 1
127.0.0.1:6379> sadd set1 b
(integer) 1
127.0.0.1:6379> sadd set1 c
(integer) 1
127.0.0.1:6379> sadd set2 c
(integer) 1
127.0.0.1:6379> sadd set2 d
(integer) 1
127.0.0.1:6379> sadd set2 e
(integer) 1
127.0.0.1:6379> sdiff set1 set2            # 以set1为参考 找出set1独有的
1) "a"
2) "b"
127.0.0.1:6379> sinter set1 set2           # 交集
1) "c"
127.0.0.1:6379> sunion set1 set2           # 并集
1) "a"
2) "b"
3) "c"
4) "e"
5) "d"

####################################
```



#### 2.4  Hash

存的是map集合



```bash
####################################

127.0.0.1:6379> hset myhash field1 cqp   # 设置一个具体key-value
(integer) 1
127.0.0.1:6379> hget myhash field1
"cqp"
127.0.0.1:6379> hmset myhash field1 hello field2 world     # 设置多个具体的key-value
OK
127.0.0.1:6379> hmget myhash field1 field2
1) "hello"
2) "world"
127.0.0.1:6379> hgetall myhash                   # 获取全部的数据
1) "field1"
2) "hello"
3) "field2"
4) "world"
127.0.0.1:6379> hdel myhash field1            # 删除
(integer) 1
127.0.0.1:6379> hgetall myhash
1) "field2"
2) "world"
127.0.0.1:6379>
####################################
127.0.0.1:6379> hgetall myhash
1) "field2"
2) "world"
3) "field1"
4) "hello"
127.0.0.1:6379> hlen myhash                 # 获取hash表的字段数量
(integer) 2
127.0.0.1:6379>

####################################      # 查看hash中指定字段是否存在
127.0.0.1:6379> hexists myhash field1
(integer) 1
127.0.0.1:6379> hexists myhash field3
(integer) 0
127.0.0.1:6379>
####################################
# 只获得所有field
# 只获得所有value
127.0.0.1:6379> hkeys myhash
1) "field2"
2) "field1"
127.0.0.1:6379> hvals myhash
1) "world"
2) "hello"
####################################
#  加减
127.0.0.1:6379> hset myhash field3 5
(integer) 1
127.0.0.1:6379> HINCRBY myhash field3 1
(integer) 6
127.0.0.1:6379> HINCRBY myhash field3 -1
(integer) 5

####################################
127.0.0.1:6379> hsetnx myhash field4 hello     # 如果不存在则设置成功
(integer) 1
127.0.0.1:6379> hsetnx myhash field4 world     # 如果存在则设置失败
(integer) 0
```



hash应用： 变更的数据   user  name age 尤其是用户信息的保存，经常变动的信息，hash更适合对象的存储！ 



```bash
127.0.0.1:6379> hset user:1 name cqp
(integer) 1
127.0.0.1:6379> hget user:1 name
"cqp"
127.0.0.1:6379>
```



#### 2.5 Zset(有序集合)

在set的基础上增加了一个值  set k1 v1  zset k1 score1 v1

```bash
####################################
127.0.0.1:6379> zadd myzset 1 one
(integer) 1
127.0.0.1:6379> zadd myzset 2 two
(integer) 1
127.0.0.1:6379> zadd myzset 3 three
(integer) 1
127.0.0.1:6379> ZRANGE myzset 0 -1
1) "one"
2) "two"
3) "three"
127.0.0.1:6379>
####################################

127.0.0.1:6379> zadd salary 2500 iu
(integer) 1
127.0.0.1:6379> zadd salary 5000 pikachu
(integer) 1
127.0.0.1:6379> zadd salary 200 cqp
(integer) 1
127.0.0.1:6379> ZRANGEBYSCORE salary -inf +inf             # 显示所有的用户 从小到大
1) "cqp"
2) "iu"
3) "pikachu"
127.0.0.1:6379> ZRANGEBYSCORE salary -inf +inf withscores  # 显示所有的用户 并且附带成绩
1) "cqp"
2) "200"
3) "iu"
4) "2500"
5) "pikachu"
6) "5000"
127.0.0.1:6379> ZRANGEBYSCORE salary -inf 2500 withscores
1) "cqp"
2) "200"
3) "iu"
4) "2500" 
127.0.0.1:6379> ZREVRANGE salary 0 -1       # 从高到低排序
1) "pikachu"
2) "iu"
####################################
#  移除
127.0.0.1:6379> ZRANGE salary 0 -1
1) "cqp"
2) "iu"
3) "pikachu"
127.0.0.1:6379> zrem salary cqp
(integer) 1
127.0.0.1:6379> ZRANGE salary 0 -1
1) "iu"
2) "pikachu"
127.0.0.1:6379> ZCARD salary        # 获取有序集合中的个数
(integer) 2
####################################

127.0.0.1:6379> zadd myzset 1 hello 2 world 3 cqp 
(integer) 3
127.0.0.1:6379> zcount myzset 1 3       # 获取指定区间的成员数量
(integer) 3
127.0.0.1:6379> zcount myzset 1 2
(integer) 2
127.0.0.1:6379>

####################################

```



查文档是最好的学习方法！http://www.redis.cn/commands.html

zset案例思路: set可以的zset也可以

​                      存储班级成绩

 					    普通消息1  重要消息2  带权重进行判断

​                      排行榜









### 3.Redis三种特殊数据类型

#### 3.1 geospatial  地理位置



朋友的定位 地理位置  附近的人 打车距离计算

Redis 的 Geo在redis3.2版本就推出了！这个功能可以推算出地理位置的信息，两地之间的距离，方圆几里的人！



![image-20201216191507077](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201216191507077.png)



> geoadd

```bash
######################################
#     添加城市地理位置数据    我们一般会下载城市数据，直接通过java程序一次性导入
#     纬度、经度、名称
127.0.0.1:6379> geoadd china:city 116.40 39.90 beijin
(integer) 1
127.0.0.1:6379> geoadd china:city 121.47 31.23 shanghai
(integer) 1
127.0.0.1:6379> geoadd china:city 106.50 29.53 chongqing 114.05 22.52 shengzhen
(integer) 2
127.0.0.1:6379> geoadd china:city 120.16 20.24 hangzhou 108.96 34.26 xian
(integer) 2
```

> geopos： 从`key`里返回所有给定位置元素的位置（经度和纬度）。

```bash
127.0.0.1:6379> geopos china:city beijin
1) 1) "116.39999896287918091"
   2) "39.90000009167092543"
127.0.0.1:6379> geopos china:city beijin chongqing
1) 1) "116.39999896287918091"
   2) "39.90000009167092543"
2) 1) "106.49999767541885376"
   2) "29.52999957900659211"
127.0.0.1:6379>
```



> geodist:  返回两个给定位置之间的距离。

如果两个位置之间的其中一个不存在， 那么命令返回空值。

指定单位的参数 unit 必须是以下单位的其中一个：

- **m** 表示单位为米。
- **km** 表示单位为千米。
- **mi** 表示单位为英里。
- **ft** 表示单位为英尺。

如果用户没有显式地指定单位参数， 那么 `GEODIST` 默认使用米作为单位。

```bash
127.0.0.1:6379> geodist china:city beijin shanghai
"1067378.7564"
127.0.0.1:6379> geodist china:city beijin shanghai km
"1067.3788"
127.0.0.1:6379>
```



> georadius  以给定的经纬度为中心,找出某一半径内的元素



范围可以使用以下其中一个单位：

- **m** 表示单位为米。
- **km** 表示单位为千米。
- **mi** 表示单位为英里。
- **ft** 表示单位为英尺。



我附近的人？(获得所有附近的人的地址，定位) 通过半径来查询！

```bash
127.0.0.1:6379> GEORADIUS china:city 110 30 1000 km      # 以100 30 经纬度为中心，寻找方圆1000km内的城市 
1) "chongqing"
2) "xian"
3) "shengzhen"
127.0.0.1:6379> GEORADIUS china:city 110 30 500 km
1) "chongqing"
2) "xian"
127.0.0.1:6379>
127.0.0.1:6379> GEORADIUS china:city 110 30 500 km withdist withcoord count 1   #筛选查出一个
1) 1) "chongqing"
   2) "341.9374"
   3) 1) "106.49999767541885376"
      2) "29.52999957900659211"
127.0.0.1:6379>
127.0.0.1:6379> georadius china:city 110 30 500 km withdist         # 显示到中间距离的位置
1) 1) "chongqing"
   2) "341.9374"
2) 1) "xian"
   2) "483.8340"
127.0.0.1:6379> georadius china:city 110 30 500 km withcoord        # 显示他人的定位信息
1) 1) "chongqing"
   2) 1) "106.49999767541885376"
      2) "29.52999957900659211"
2) 1) "xian"
   2) 1) "108.96000176668167114"
      2) "34.25999964418929977"
127.0.0.1:6379>

```



> GEORADIUSBYMEMBER :  中心点是由给定的位置元素决定的

```bash
127.0.0.1:6379> GEORADIUSBYMEMBER china:city beijin 1000 km
1) "beijin"
2) "xian"
127.0.0.1:6379> GEORADIUSBYMEMBER china:city shanghai 400 km
1) "shanghai"
127.0.0.1:6379>
```



> geohash：返回一个或多个位置元素的 Geohash 表示

该命令将返回11个字符的Geohash字符串！

```bash
127.0.0.1:6379> geohash china:city beijin chongqing
1) "wx4fbxxfke0"
2) "wm5xzrybty0"
127.0.0.1:6379>
```



> geo 底层的实现原理其实就是Zset ！ 我们可以用zset命令来操作geo!

```bash
127.0.0.1:6379> ZRANGE china:city 0 -1
1) "hangzhou"
2) "chongqing"
3) "xian"
4) "shengzhen"
5) "shanghai"
6) "beijin"
127.0.0.1:6379> zrem china:city beijin   # 移除指定元素
(integer) 1
127.0.0.1:6379> ZRANGE china:city 0 -1
1) "hangzhou"
2) "chongqing"
3) "xian"
4) "shengzhen"
5) "shanghai"
127.0.0.1:6379>
```



#### 3.2 Hyperloglog

> 什么是基数？

A{1,3,5,7,8,7}

B{1,3,5,7,8}



基数（不重复的元素） = 5

应用场景：基数统计

**网页的uv（页面访问量）一个人访问网站多次但是还是算作一个人**

优点：占用内存是固定的，2^64 不同的元素的基数，只需要12kb的内存！从内存角度，hypelohlog为首选

传统的方式用set  保存用户的id 因为set不允许重复,然后可以统计set中的元素数量作为标准判断

这个方式如果保存大量的用户id，就会比较麻烦，占内存。我们的目的是为了计数，而不是为了保存用户id

0.81%错误率，是可以忽略不计的。



```bash
#############################
127.0.0.1:6379> pfadd mykey1 a b c d e f g h i j   # 创建第一组元素
(integer) 1
127.0.0.1:6379> pfcount mykey1                     # 统计mykey1 元素的基数个数
(integer) 10
127.0.0.1:6379> pfadd mykey2 i j z x c v b n m     # 创建第二组元素 mykey2
(integer) 1
127.0.0.1:6379> pfcount mykey2
(integer) 9
127.0.0.1:6379> pfmerge mykey3 mykey1 mykey2       # 合并两组 mykey1 mykey2 =》mykey3 并集
OK
127.0.0.1:6379> pfcount mykey3                     # 看并集的数量！
(integer) 15
```

如果允许容错，那么一定可以使用hyperloglog！

如果不允许容错，就使用set或者自己的数据类型即可！



#### 3.3 Bitmaps

> 位存储

统计用户信息（00001001） 活跃 不活跃 登录 未登录 打卡 365天打卡

bitmaps 位图 数据结构！都是操作二进制位来进行记录，就只有0和1两个状态！

365 天 =365bit 1字节=8bit 46个字节左右



使用bitmap来记录周一到周日的打卡

```bash
127.0.0.1:6379> setbit sign 0 1
(integer) 0
127.0.0.1:6379> setbit sign 1 0
(integer) 0
127.0.0.1:6379> setbit sign 2 0
(integer) 0
127.0.0.1:6379> setbit sign 3 1
(integer) 0
127.0.0.1:6379> setbit sign 4 1
(integer) 0
127.0.0.1:6379> setbit sign 5 0
(integer) 0
127.0.0.1:6379> setbit sign 6 0
(integer) 0
```

查看某一天有无打卡

```bash
127.0.0.1:6379> getbit sign 3
(integer) 1
127.0.0.1:6379> getbit sign 6
(integer) 0
```

统计操作，统计打卡的天数

```bash
127.0.0.1:6379> bitcount sign     # 统计这周的打卡记录，就可以看到是否有全勤！
(integer) 3
```







### 4.Redis 事务

原子性：要么同时成功，要么同时失败



**redis单条命令是保证原子性的。但是事务不保证原子性！**



Redis事务本质：一组命令的集合！一个事务中的所有命令都会被序列化，在事务执行过程中，会按照顺序执行！



一次性 顺序性 排他性 

```bash
---------队列  set set set 执行-------
```

Redis事务没有隔离级别的概念！

所有的命令在事务中，并没有直接被执行！只有发起执行命令的时候才会被执行！exec



redis 的事务

* 开启事务（multi）
* 命令入队（.....）
* 执行事务（exec）

```bash
##############################
127.0.0.1:6379> multi         #   开启事务
OK
127.0.0.1:6379> set k1 v1         # 命令入队
QUEUED
127.0.0.1:6379> set k2 v2
QUEUED
127.0.0.1:6379> get k2
QUEUED
127.0.0.1:6379> set k3 v3
QUEUED
127.0.0.1:6379> exec         # 执行事务
1) OK
2) OK
3) "v2"
4) OK
127.0.0.1:6379>
```

> 放弃事务

```bash
127.0.0.1:6379> multi          #   开启事务
OK
127.0.0.1:6379> set k1 v1
QUEUED
127.0.0.1:6379> set k2 v2
QUEUED
127.0.0.1:6379> set k4 v4
QUEUED
127.0.0.1:6379> DISCARD      #   取消事务
OK
127.0.0.1:6379> get k4       # 事务队列中命令都不会被执行
(nil)
127.0.0.1:6379>
```



> 编译型异常 （代码有问题！命令有错！） 事务中所有的命令都不会执行！

```bash
##################################
127.0.0.1:6379> multi
OK
127.0.0.1:6379> set k1 v1
QUEUED
127.0.0.1:6379> set k2 v2
QUEUED
127.0.0.1:6379> set k3 v3   
QUEUED
127.0.0.1:6379> getset k3             # getset命令先获取再设值  命令错误 全部都不执行
(error) ERR wrong number of arguments for 'getset' command  
127.0.0.1:6379> set k4 v4
QUEUED
127.0.0.1:6379> set k5 v5
QUEUED
127.0.0.1:6379> exec        # 执行事务报错 
(error) EXECABORT Transaction discarded because of previous errors.
127.0.0.1:6379> get k1
(nil)
127.0.0.1:6379> get k5     # 所有的命令都不会执行
(nil)
127.0.0.1:6379>
```







> 运行时异常 （1/0）, 如果事务中存在语法性错误，那么执行命令的时候。其他的命令是可以正常执行的，错误命令会抛出异常

```bash
127.0.0.1:6379> set k1 "v1"
OK
127.0.0.1:6379> multi
OK
127.0.0.1:6379> incr k1
QUEUED
127.0.0.1:6379> set k2 v2
QUEUED
127.0.0.1:6379> set k3 v3
QUEUED
127.0.0.1:6379> get k3
QUEUED
127.0.0.1:6379> exec
1) (error) ERR value is not an integer or out of range   # 虽然第一条命令报错 但是依旧正常执行成功了
2) OK
3) OK
4) "v3"
```

​                                             编译型错误                           运行时错误

就像买车 一开始就看到车子是坏的就不会买来开了，但是如果是开着开着坏了，但是已经跑了不少路了！





### 5. Redis实现乐观锁

> 监控！Watch   （面试常问）



**悲观锁：**

* 很悲观，什么时候都会出问题，无论做什么都会加锁！

**乐观锁：**

* 很乐观，认为什么都不会出问题，所以不会上锁，更新数据的时候去判断一下，在此期间是否有人修改过这个数据。
* 获取 version
* 更新的时候比较version

> redis  watch   监视测试

正常执行成功！

```bash
127.0.0.1:6379> set money 100
OK
127.0.0.1:6379> set out 0
OK
127.0.0.1:6379> watch money      # 监视money对象
OK
127.0.0.1:6379> multi            # 事务正常结束，数据期间没有发生变动，这个时候就正常执行成功
OK
127.0.0.1:6379> decrby money 20
QUEUED
127.0.0.1:6379> incrby out 20
QUEUED
127.0.0.1:6379> exec
1) (integer) 80
2) (integer) 20
127.0.0.1:6379>

```



测试多线程修改值，监视事务失败！

使用watch可以当做 redis 的乐观锁操作

![image-20201217155152432](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201217155152432.png)



![image-20201217155131548](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201217155131548.png)



解除监听

```bash
127.0.0.1:6379> unwatch   
OK     
```



![image-20201217160907035](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201217160907035.png)



### 6. Jedis

我们要使用java来操作 Redis

> jedis 是redis 官方推荐的java连接开发工具！ 使用java操作redis中间件！如果你要使用java操作redis
>
> 那么就要对jedis十分的熟悉！



> 测试



1. idea新建空project

![image-20201217162533995](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201217162533995.png)



![image-20201217162556247](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201217162556247.png)





2. 新建普通maven项目

![image-20201217162743032](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201217162743032.png)

![image-20201217162834343](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201217162834343.png)



![image-20201217162948524](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201217162948524.png)





![image-20201217163102559](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201217163102559.png)



![image-20201217163240729](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201217163240729.png)



好了项目可以正常用了





> 开始使用jedis

1.导包

```xml
<!--导入 jedis的包    -->
<dependencies>
    <!-- https://mvnrepository.com/artifact/redis.clients/jedis -->
    <dependency>
        <groupId>redis.clients</groupId>
        <artifactId>jedis</artifactId>
        <version>3.2.0</version>
    </dependency>
    
   <!--fastjson-->
    <dependency>
        <groupId>com.alibaba</groupId>
        <artifactId>fastjson</artifactId>
        <version>1.2.74</version>
    </dependency>
</dependencies>
```

2.编码测试

* 连接数据库

1. 本地redis

   ```bash
   chenqipeng@chenqipengdeMacBook-Pro ~ % brew ls
   openssl@1.1	redis
   chenqipeng@chenqipengdeMacBook-Pro ~ % brew services start redis
   Service `redis` already started, use `brew services restart redis` to restart.
   chenqipeng@chenqipengdeMacBook-Pro ~ % brew services list
   Name  Status  User       Plist
   redis started chenqipeng /Users/chenqipeng/Library/LaunchAgents/homebrew.mxcl.redis.plist
   chenqipeng@chenqipengdeMacBook-Pro ~ % redis-cli -h 127.0.0.1
   127.0.0.1:6379> ping
   PONG
   127.0.0.1:6379>
   ```

​    关闭redis 直接在redis-cli里面 shutdown

```java
public class TestPing {
    public static void main(String[] args) {
        // 1. new jedis 对象即可
        Jedis jedis = new Jedis("127.0.0.1",6379);
        // jedis 所有的命令就是我们之前学过的所有指令
        System.out.println(jedis.ping());
    }
}
```



![image-20201217185623744](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201217185623744.png)

连接成功



2. 连接远程阿里云的redis服务

   **远程阿里云无法连接的问题： 只需要把配置文件里的bind 127.0.0.1 改为 bind 0.0.0.0即可。亲测有效。**

   ```java
   public class TestPing {
       public static void main(String[] args) {
           // 1. new jedis 对象即可
           Jedis jedis = new Jedis("47.110.154.185",6379);
           // jedis 所有的命令就是我们之前学过的所有指令
           System.out.println(jedis.ping());
       }
   }
   ```









```java
public class TestKey {
    public static void main(String[] args) {
        Jedis jedis = new Jedis("47.110.154.185",6379);
        System.out.println("清空数据："+jedis.flushDB());
        System.out.println("判断某个键是否存在:"+jedis.exists("username"));
        System.out.println("新增键值对<username cqp>"+jedis.set("username","cqp"));
        System.out.println("新增键值对<password 123456>"+jedis.set("password","123456"));
        System.out.println("系统中所有的键如下：");
        Set<String> keys = jedis.keys("*");
        System.out.println(keys);
        System.out.println("删除password:"+jedis.del("password"));
        System.out.println("判断键password是否存在："+jedis.exists("password"));
        System.out.println("查看键username所存储的类型:"+jedis.type("username"));
        System.out.println("随机返回key空间的一个："+jedis.randomKey());
        System.out.println("重命名可以："+jedis.rename("username","name"));
        System.out.println("取出改后的name:"+jedis.get("name"));
        System.out.println("按索引查询："+jedis.select(0));
        System.out.println("删除当前选择数据库中的所有key:"+jedis.flushDB());
        System.out.println("返回当前数据库中key的数目："+jedis.dbSize());
        System.out.println("删除所有数据库中的所有key:"+jedis.flushAll());
    }
}
```

常用的API

* String

```java
public static void main(String[] args) {
        Jedis jedis = new Jedis("47.110.154.185",6379);
        jedis.flushDB();
        System.out.println("=======增加数据========");
        System.out.println(jedis.set("key1","value1"));
        System.out.println(jedis.set("key2","value2"));
        System.out.println(jedis.set("key3","value3"));
        System.out.println("删除键key2:"+jedis.del("key2"));
        System.out.println("获取键key2："+jedis.get("key2"));
        System.out.println("修改键key1："+jedis.set("key1","value1changed"));
        System.out.println("在key3后面加入值:"+jedis.append("key3","nd"));
        System.out.println("key3的值:"+jedis.get("key3"));
        System.out.println("增加多个键值对："+jedis.mset("key01","value01","key02","value02"));
        System.out.println("获取多个键值对："+jedis.mget("key01","key02"));
        System.out.println("删除多个键值对："+jedis.del("key01","key02"));
        System.out.println("获取多个键值对："+jedis.mget("key01","key02"));

        jedis.flushDB();
        System.out.println("=====清除数据 新增数据=====");
        System.out.println(jedis.setnx("key1","value1"));
        System.out.println(jedis.setnx("key2","value2"));     // 没有值就成功 有值就不成功 用于分布式锁
        System.out.println(jedis.setnx("key2","value2-new"));
        System.out.println("key1:"+jedis.get("key1"));
        System.out.println("key2:"+jedis.get("key2"));

        System.out.println("=====新增键值对并设置有效时间======");
        System.out.println(jedis.setex("key3",2,"value3"));
        System.out.println("未被销毁的key3:"+jedis.get("key3"));
        try{
            TimeUnit.SECONDS.sleep(3);
        }catch (InterruptedException e){
            e.printStackTrace();
        }
        System.out.println("3s后的key3："+jedis.get("key3"));


        System.out.println("====获取原值，更新为新值====");
        System.out.println(jedis.getSet("key2","key2GetSet"));
        System.out.println(jedis.get("key2"));
        System.out.println("或得key2的值的字符串:"+jedis.getrange("key2",2,4));
    }
```



* List

```java
public static void main(String[] args) {
    Jedis jedis = new Jedis("47.110.154.185",6379);
    jedis.flushDB();
    System.out.println("===添加一个list===");
    jedis.lpush("collections","ArrayList","Vector","Stack","Hashmap","WeakHashmap","LinkedList");
    jedis.lpush("collections","HashSet");
    jedis.lpush("collections","TreeSet");
    jedis.lpush("collections","TreeMap");
    jedis.lpush("collections","Hashmap");
    System.out.println("collections的内容："+jedis.lrange("collections",0,-1));
    System.out.println("collections区间0-3的元素"+jedis.lrange("collections",0,3));
    System.out.println("===============");
    // 删除列表指定的值，第二个参数为删除的个数（有重复的个数时），后add进去的值先被删，类似于出栈
    System.out.println("删除指定元素的个数："+jedis.lrem("collections",2,"Hashmap"));
    System.out.println("collections的内容："+jedis.lrange("collections",0,-1));
    System.out.println("删除下标0-3之外的元素:"+jedis.ltrim("collections",0,3));
    System.out.println("collections的内容："+jedis.lrange("collections",0,-1));
    System.out.println("collections列表出栈（左端）"+jedis.lpop("collections"));
    System.out.println("collections的内容："+jedis.lrange("collections",0,-1));
    System.out.println("collections添加元素从右端:"+jedis.rpush("collections","HashMap"));
    System.out.println("collections的内容："+jedis.lrange("collections",0,-1));
    System.out.println("修改colections内容从下标1的位置："+jedis.lset("collections",1,"LinkedList"));
    System.out.println("collections的内容："+jedis.lrange("collections",0,-1));
    System.out.println("==================");
    System.out.println("collections的长度:"+jedis.llen("collections"));
    System.out.println("获取collections下标为2的元素："+jedis.lindex("collections",2));
    System.out.println("=================");
    jedis.lpush("sortedList","3","6","2","2","7","4");
    System.out.println("sortedlist排序前:"+jedis.lrange("sortedList",0,-1));
    System.out.println(jedis.sort("sortedList"));
    System.out.println("sortedList排序后:"+jedis.lrange("sortedList",0,-1));
}
```

* Set

```java
public static void main(String[] args) {
    Jedis jedis = new Jedis("47.110.154.185",6379);
    jedis.flushDB();
    System.out.println("=====向集合中添加元素（不重复）=====");
    System.out.println(jedis.sadd("eleSet","e1","e2","e4","e0","e8","e7","e5"));
    System.out.println(jedis.sadd("eleSet","e6"));
    System.out.println(jedis.sadd("eleSet","e6"));
    System.out.println("eleSet的所有元素为:"+jedis.smembers("eleSet"));
    System.out.println("删除一个元素e0:"+jedis.srem("eleSet","e0"));
    System.out.println("eleSet的所有元素为:"+jedis.smembers("eleSet"));
    System.out.println("删除两个元素e7和e6:"+jedis.srem("eleSet","e7","e6"));
    System.out.println("eleSet的所有元素为:"+jedis.smembers("eleSet"));
    System.out.println("随机移除集合中的一个元素:"+jedis.spop("eleSet"));
    System.out.println("随机移除集合中的一个元素:"+jedis.spop("eleSet"));
    System.out.println("eleSet的所有元素为:"+jedis.smembers("eleSet"));
    System.out.println("eleSet中包含元素的个数为:"+jedis.scard("eleSet"));
    System.out.println("e3是否在eleSet中:"+jedis.sismember("eleSet","e3"));
    System.out.println("e1是否在eleSet中:"+jedis.sismember("eleSet","e1"));
    System.out.println("e5是否在eleSet中:"+jedis.sismember("eleSet","e5"));
    System.out.println("===============");
    System.out.println(jedis.sadd("eleSet1","e1","e2","e4","e3","e0","e8","e7","e5"));
    System.out.println(jedis.sadd("eleSet2","e1","e2","e4","e3","e0","e8"));
    System.out.println("将eleSet1中删除e1并存入eleSet3中:"+jedis.smove("eleSet1","eleSet3","e1"));
    System.out.println("将eleSet1中删除e2并存入eleSet3中:"+jedis.smove("eleSet1","eleSet3","e2"));
    System.out.println("eleSet1的所有元素为:"+jedis.smembers("eleSet1"));
    System.out.println("eleSet3的所有元素为:"+jedis.smembers("eleSet3"));
    System.out.println("=======集合运算========");
    System.out.println("eleSet1中的元素:"+jedis.smembers("eleSet1"));
    System.out.println("eleSet2中的元素:"+jedis.smembers("eleSet2"));
    System.out.println("eleSet1和eleSet2中的交集:"+jedis.sinter("eleSet1","eleSet2"));
    System.out.println("eleSet1和eleSet2中的并集:"+jedis.sunion("eleSet1","eleSet2"));
    System.out.println("eleSet1和eleSet2中的差集:"+jedis.sdiff("eleSet1","eleSet2"));  // eleSet1中有 但是eleSet2中没有
    jedis.sinterstore("eleSet4","eleSet1","eleSet2");  //  求交集并将交集保存到eleSet4中
    System.out.println("eleSet4中的元素:"+jedis.smembers("eleSet4"));
}
```

* Hash

```java
public static void main(String[] args) {
    Jedis jedis = new Jedis("47.110.154.185",6379);
    jedis.flushDB();
    Map<String,String> map = new HashMap<>();
    map.put("key1","value1");
    map.put("key2","value2");
    map.put("key3","value3");
    map.put("key4","value4");
    // 添加名称为hash的hash元素
    jedis.hmset("hash",map);
    // 向 map中添加key为key5，value5元素
    jedis.hset("hash","key5","value5");
    System.out.println("散列hash的所有键值对为:"+jedis.hgetAll("hash"));
    System.out.println("散列hash的所有键为:"+jedis.hkeys("hash"));
    System.out.println("散列hash的所有值为:"+jedis.hvals("hash"));
    System.out.println("将key6保存的值加上一个整数，如果key6不存在则添加key6:"+jedis.hincrBy("hash","key6",2));
    System.out.println("散列hash的所有键值对为:"+jedis.hgetAll("hash"));
    System.out.println("将key6保存的值加上一个整数，如果key6不存在则添加key6:"+jedis.hincrBy("hash","key6",2));
    System.out.println("散列hash的所有键值对为:"+jedis.hgetAll("hash"));
    System.out.println("删除一个或者多个键值对:"+jedis.hdel("hash","key2"));
    System.out.println("散列hash的所有键值对为:"+jedis.hgetAll("hash"));
    System.out.println("散列中键值对的个数:"+jedis.hlen("hash"));
    System.out.println("判断hash中是否存在key2:"+jedis.hexists("hash","key2"));
    System.out.println("判断hash中是否存在key3:"+jedis.hexists("hash","key3"));
    System.out.println("获取hash中的值:"+jedis.hmget("hash","key3"));
    System.out.println("获取hash中的值:"+jedis.hmget("hash","key3","key4"));
}
```

* Zset



### 7.  Jedis 事务

```java
package cqp;

import com.alibaba.fastjson.JSONObject;
import redis.clients.jedis.Jedis;
import redis.clients.jedis.Transaction;

public class TestTS {
    public static void main(String[] args) {
        Jedis jedis = new Jedis("47.110.154.185",6379);
        jedis.flushDB();

        JSONObject jsonObject = new JSONObject();
        jsonObject.put("hello","world");
        jsonObject.put("name","chenqipeng");

        //开启事务
        Transaction multi = jedis.multi();
        String result = jsonObject.toJSONString();
        try{
            multi.set("user1",result);
            multi.set("user2",result);
            int i = 1/0;         // 代码抛出异常，事务执行失败！
            multi.exec();       // 执行事务
        }catch (Exception e){
            multi.discard();   // 放弃事务
            e.printStackTrace();
        }finally {
            System.out.println(jedis.get("user1"));
            System.out.println(jedis.get("user2"));
            jedis.close(); // 关闭连接
        }
    }
}
```



### 8.  Springboot 整合 redis



jedis是原生的api



SpringBoot 操作数据：**spring-data** jpa jdbc mongodb redis 

SpringData 也是和SpringBoot 齐名的项目 



搭建测试环境

1. new module

2. spring initialize

![image-20201219131157945](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201219131157945.png)

![image-20201219131311954](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201219131311954.png)





​    **记得改名**



![image-20201219131347892](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201219131347892.png)





> 整合测试

说明:在springBoot 2.x 之后，原来使用的jedis被替换为了lettuce?

![image-20201219132547957](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201219132547957.png)

 

jedis：采用的是直连，多个线程操作的话，是不安全的，如果想要避免不安全，使用jedis pool 连接池！更像 BIO 模式

lettuce: 采用netty，实例可以在多个线程中共享，不存在线程不安全的情况！可以减少线程数据了，更像       NIO 模式

>springboot 所有的配置类，都有一个自动配置类        RedisAutoConfiguration
>
>自动配置类都会绑定一个 properties 配置文件           RedisProperties

![image-20201219133959839](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201219133959839.png)

![image-20201219134242336](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201219134242336.png)

​     

​      redis 默认本地连接

![image-20201219134409376](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201219134409376.png)





```bash
RedisAutoConfiguration #源码分析
```

```java
@Configuration(proxyBeanMethods = false)
@ConditionalOnClass(RedisOperations.class)
@EnableConfigurationProperties(RedisProperties.class)
@Import({ LettuceConnectionConfiguration.class, JedisConnectionConfiguration.class })
public class RedisAutoConfiguration {

   @Bean
   @ConditionalOnMissingBean(name = "redisTemplate")   // 我们可以自己定义一个redisTemplate来替换这个默认的！
   @ConditionalOnSingleCandidate(RedisConnectionFactory.class)
   public RedisTemplate<Object, Object> redisTemplate(RedisConnectionFactory redisConnectionFactory) {
     
     // 默认的 RedisTemplate 没有过多的设置，redis 对象都需要序列化！
     // 两个泛型都是 object, object的类型，我们后使用需要强制转换<string,Object>
     
     RedisTemplate<Object, Object> template = new RedisTemplate<>();
      template.setConnectionFactory(redisConnectionFactory);
      return template;
   }

   @Bean
   @ConditionalOnMissingBean  // 由于Strings是redis中最常用的类型，所以说单独提出了一个bean!
   @ConditionalOnSingleCandidate(RedisConnectionFactory.class)
   public StringRedisTemplate stringRedisTemplate(RedisConnectionFactory redisConnectionFactory) {
      StringRedisTemplate template = new StringRedisTemplate();
      template.setConnectionFactory(redisConnectionFactory);
      return template;
   }

}
```

> 测试一下



1.导入依赖

2.配置连接

![image-20201219150618911](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201219150618911.png)

3.测试

```java
@Autowired
   private RedisTemplate redisTemplate;
   @Test
   void contextLoads() {
      // redisTemplate  操作不同的数据类型
      // opsForValue  操作字符串  类似string
      // opsForList
      // opsForSet
      // opsForHash
      // opsForGeo

      // 除了基本的操作，常用的方法都可以直接通过redisTemplate操作，比如事务和基本的crud
      redisTemplate.opsForList();

      // 获取redis的连接对象

//    RedisConnection connection =redisTemplate.getConnectionFactory().getConnection();
//    connection.flushDb();
//    connection.flushAll();
      redisTemplate.opsForValue().set("mykey","陈启鹏:chenqipeng");
      System.out.println(redisTemplate.opsForValue().get("mykey"));

   }
```

![image-20201219154849802](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201219154849802.png)



![image-20201219154932597](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201219154932597.png)







![image-20201219155103949](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201219155103949.png)



### 9.编写自己的 RedisTemplate



```java
package com.cqp.config;
import com.fasterxml.jackson.annotation.JsonAutoDetect;
import com.fasterxml.jackson.annotation.PropertyAccessor;
import com.fasterxml.jackson.databind.ObjectMapper;
import com.fasterxml.jackson.databind.jsontype.impl.LaissezFaireSubTypeValidator;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.data.redis.connection.RedisConnectionFactory;
import org.springframework.data.redis.core.RedisTemplate;
import org.springframework.data.redis.serializer.Jackson2JsonRedisSerializer;
import org.springframework.data.redis.serializer.StringRedisSerializer;

@Configuration
public class RedisConfig {
    // 编写我们自己的 redisTemplate
    @Bean
    public RedisTemplate<String, Object> redisTemplate(RedisConnectionFactory redisConnectionFactory) {
        // 为了自己开发方便，一般直接使用 <string,value>
        RedisTemplate<String, Object> template = new RedisTemplate<>();
        template.setConnectionFactory(redisConnectionFactory);

        // 对象 Json序列化
        Jackson2JsonRedisSerializer<Object> jackson2JsonRedisSerializer = new Jackson2JsonRedisSerializer(Object.class);
        ObjectMapper om = new ObjectMapper();
        om.setVisibility(PropertyAccessor.ALL, JsonAutoDetect.Visibility.ANY);
        om.activateDefaultTyping(LaissezFaireSubTypeValidator.instance, ObjectMapper.DefaultTyping.NON_FINAL);
        jackson2JsonRedisSerializer.setObjectMapper(om);

        // String 的序列化
        StringRedisSerializer stringRedisSerializer = new StringRedisSerializer();

        // key 采用String的序列化方式
        template.setKeySerializer(stringRedisSerializer);
        // hash的key也采用String的序列化方式
        template.setHashKeySerializer(stringRedisSerializer);
        // value序列化方式采用jackson
        template.setValueSerializer(jackson2JsonRedisSerializer);
        // hash 的value序列化方式也用jackson
        template.setHashValueSerializer(jackson2JsonRedisSerializer);
        template.afterPropertiesSet();
        return template;
    }
}
```



这时候加上泛型测试

```java
@SpringBootTest
class Redis02SpringbootApplicationTests {

   @Autowired   
   private RedisTemplate<String,Object> redisTemplate;
   @Test
   void contextLoads() {

  
      redisTemplate.opsForList();

      // 获取redis的连接对象

      RedisConnection connection =redisTemplate.getConnectionFactory().getConnection();
      connection.flushDb();
//    connection.flushAll();
      redisTemplate.opsForValue().set("mykey","陈启鹏:chenqipeng");
      System.out.println(redisTemplate.opsForValue().get("mykey"));
   }
}
```

在企业开发中我们会写一个RedisUtils

```java
package com.cqp.utils;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.data.redis.core.RedisTemplate;
import org.springframework.stereotype.Component;
import org.springframework.util.CollectionUtils;

import java.util.Arrays;
import java.util.List;
import java.util.Map;
import java.util.Set;
import java.util.concurrent.TimeUnit;

@Component
public final class RedisUtil {

    @Autowired
    private RedisTemplate<String, Object> redisTemplate;

    // =============================common============================
    /**
     * 指定缓存失效时间
     * @param key  键
     * @param time 时间(秒)
     */
    public boolean expire(String key, long time) {
        try {
            if (time > 0) {
                redisTemplate.expire(key, time, TimeUnit.SECONDS);
            }
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }

    /**
     * 根据key 获取过期时间
     * @param key 键 不能为null
     * @return 时间(秒) 返回0代表为永久有效
     */
    public long getExpire(String key) {
        return redisTemplate.getExpire(key, TimeUnit.SECONDS);
    }


    /**
     * 判断key是否存在
     * @param key 键
     * @return true 存在 false不存在
     */
    public boolean hasKey(String key) {
        try {
            return redisTemplate.hasKey(key);
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }


    /**
     * 删除缓存
     * @param key 可以传一个值 或多个
     */
    @SuppressWarnings("unchecked")
    public void del(String... key) {
        if (key != null && key.length > 0) {
            if (key.length == 1) {
                redisTemplate.delete(key[0]);
            } else {
                List<String> list = Arrays.asList(key.clone());
                redisTemplate.delete(list);
                //redisTemplate.delete(CollectionUtils.arrayToList(key));  把array转换成list
            }
        }
    }


    // ============================String=============================

    /**
     * 普通缓存获取
     * @param key 键
     * @return 值
     */
    public Object get(String key) {
        return key == null ? null : redisTemplate.opsForValue().get(key);
    }

    /**
     * 普通缓存放入
     * @param key   键
     * @param value 值
     * @return true成功 false失败
     */

    public boolean set(String key, Object value) {
        try {
            redisTemplate.opsForValue().set(key, value);
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }


    /**
     * 普通缓存放入并设置时间
     * @param key   键
     * @param value 值
     * @param time  时间(秒) time要大于0 如果time小于等于0 将设置无限期
     * @return true成功 false 失败
     */

    public boolean set(String key, Object value, long time) {
        try {
            if (time > 0) {
                redisTemplate.opsForValue().set(key, value, time, TimeUnit.SECONDS);
            } else {
                set(key, value);
            }
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }


    /**
     * 递增
     * @param key   键
     * @param delta 要增加几(大于0)
     */
    public long incr(String key, long delta) {
        if (delta < 0) {
            throw new RuntimeException("递增因子必须大于0");
        }
        return redisTemplate.opsForValue().increment(key, delta);
    }


    /**
     * 递减
     * @param key   键
     * @param delta 要减少几(小于0)
     */
    public long decr(String key, long delta) {
        if (delta < 0) {
            throw new RuntimeException("递减因子必须大于0");
        }
        return redisTemplate.opsForValue().increment(key, -delta);
    }


    // ================================Map=================================

    /**
     * HashGet
     * @param key  键 不能为null
     * @param item 项 不能为null
     */
    public Object hget(String key, String item) {
        return redisTemplate.opsForHash().get(key, item);
    }

    /**
     * 获取hashKey对应的所有键值
     * @param key 键
     * @return 对应的多个键值
     */
    public Map<Object, Object> hmget(String key) {
        return redisTemplate.opsForHash().entries(key);
    }

    /**
     * HashSet
     * @param key 键
     * @param map 对应多个键值
     */
    public boolean hmset(String key, Map<String, Object> map) {
        try {
            redisTemplate.opsForHash().putAll(key, map);
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }


    /**
     * HashSet 并设置时间
     * @param key  键
     * @param map  对应多个键值
     * @param time 时间(秒)
     * @return true成功 false失败
     */
    public boolean hmset(String key, Map<String, Object> map, long time) {
        try {
            redisTemplate.opsForHash().putAll(key, map);
            if (time > 0) {
                expire(key, time);
            }
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }


    /**
     * 向一张hash表中放入数据,如果不存在将创建
     *
     * @param key   键
     * @param item  项
     * @param value 值
     * @return true 成功 false失败
     */
    public boolean hset(String key, String item, Object value) {
        try {
            redisTemplate.opsForHash().put(key, item, value);
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }

    /**
     * 向一张hash表中放入数据,如果不存在将创建
     *
     * @param key   键
     * @param item  项
     * @param value 值
     * @param time  时间(秒) 注意:如果已存在的hash表有时间,这里将会替换原有的时间
     * @return true 成功 false失败
     */
    public boolean hset(String key, String item, Object value, long time) {
        try {
            redisTemplate.opsForHash().put(key, item, value);
            if (time > 0) {
                expire(key, time);
            }
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }


    /**
     * 删除hash表中的值
     *
     * @param key  键 不能为null
     * @param item 项 可以使多个 不能为null
     */
    public void hdel(String key, Object... item) {
        redisTemplate.opsForHash().delete(key, item);
    }


    /**
     * 判断hash表中是否有该项的值
     *
     * @param key  键 不能为null
     * @param item 项 不能为null
     * @return true 存在 false不存在
     */
    public boolean hHasKey(String key, String item) {
        return redisTemplate.opsForHash().hasKey(key, item);
    }


    /**
     * hash递增 如果不存在,就会创建一个 并把新增后的值返回
     *
     * @param key  键
     * @param item 项
     * @param by   要增加几(大于0)
     */
    public double hincr(String key, String item, double by) {
        return redisTemplate.opsForHash().increment(key, item, by);
    }


    /**
     * hash递减
     *
     * @param key  键
     * @param item 项
     * @param by   要减少记(小于0)
     */
    public double hdecr(String key, String item, double by) {
        return redisTemplate.opsForHash().increment(key, item, -by);
    }


    // ============================set=============================

    /**
     * 根据key获取Set中的所有值
     * @param key 键
     */
    public Set<Object> sGet(String key) {
        try {
            return redisTemplate.opsForSet().members(key);
        } catch (Exception e) {
            e.printStackTrace();
            return null;
        }
    }


    /**
     * 根据value从一个set中查询,是否存在
     *
     * @param key   键
     * @param value 值
     * @return true 存在 false不存在
     */
    public boolean sHasKey(String key, Object value) {
        try {
            return redisTemplate.opsForSet().isMember(key, value);
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }


    /**
     * 将数据放入set缓存
     *
     * @param key    键
     * @param values 值 可以是多个
     * @return 成功个数
     */
    public long sSet(String key, Object... values) {
        try {
            return redisTemplate.opsForSet().add(key, values);
        } catch (Exception e) {
            e.printStackTrace();
            return 0;
        }
    }


    /**
     * 将set数据放入缓存
     *
     * @param key    键
     * @param time   时间(秒)
     * @param values 值 可以是多个
     * @return 成功个数
     */
    public long sSetAndTime(String key, long time, Object... values) {
        try {
            Long count = redisTemplate.opsForSet().add(key, values);
            if (time > 0)
                expire(key, time);
            return count;
        } catch (Exception e) {
            e.printStackTrace();
            return 0;
        }
    }


    /**
     * 获取set缓存的长度
     *
     * @param key 键
     */
    public long sGetSetSize(String key) {
        try {
            return redisTemplate.opsForSet().size(key);
        } catch (Exception e) {
            e.printStackTrace();
            return 0;
        }
    }


    /**
     * 移除值为value的
     *
     * @param key    键
     * @param values 值 可以是多个
     * @return 移除的个数
     */

    public long setRemove(String key, Object... values) {
        try {
            Long count = redisTemplate.opsForSet().remove(key, values);
            return count;
        } catch (Exception e) {
            e.printStackTrace();
            return 0;
        }
    }

    // ===============================list=================================

    /**
     * 获取list缓存的内容
     *
     * @param key   键
     * @param start 开始
     * @param end   结束 0 到 -1代表所有值
     */
    public List<Object> lGet(String key, long start, long end) {
        try {
            return redisTemplate.opsForList().range(key, start, end);
        } catch (Exception e) {
            e.printStackTrace();
            return null;
        }
    }


    /**
     * 获取list缓存的长度
     *
     * @param key 键
     */
    public long lGetListSize(String key) {
        try {
            return redisTemplate.opsForList().size(key);
        } catch (Exception e) {
            e.printStackTrace();
            return 0;
        }
    }


    /**
     * 通过索引 获取list中的值
     *
     * @param key   键
     * @param index 索引 index>=0时， 0 表头，1 第二个元素，依次类推；index<0时，-1，表尾，-2倒数第二个元素，依次类推
     */
    public Object lGetIndex(String key, long index) {
        try {
            return redisTemplate.opsForList().index(key, index);
        } catch (Exception e) {
            e.printStackTrace();
            return null;
        }
    }


    /**
     * 将list放入缓存
     *
     * @param key   键
     * @param value 值
     */
    public boolean lSet(String key, Object value) {
        try {
            redisTemplate.opsForList().rightPush(key, value);
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }


    /**
     * 将list放入缓存
     * @param key   键
     * @param value 值
     * @param time  时间(秒)
     */
    public boolean lSet(String key, Object value, long time) {
        try {
            redisTemplate.opsForList().rightPush(key, value);
            if (time > 0)
                expire(key, time);
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }

    }


    /**
     * 将list放入缓存
     *
     * @param key   键
     * @param value 值
     * @return
     */
    public boolean lSet(String key, List<Object> value) {
        try {
            redisTemplate.opsForList().rightPushAll(key, value);
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }

    }


    /**
     * 将list放入缓存
     *
     * @param key   键
     * @param value 值
     * @param time  时间(秒)
     * @return
     */
    public boolean lSet(String key, List<Object> value, long time) {
        try {
            redisTemplate.opsForList().rightPushAll(key, value);
            if (time > 0)
                expire(key, time);
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }


    /**
     * 根据索引修改list中的某条数据
     *
     * @param key   键
     * @param index 索引
     * @param value 值
     * @return
     */

    public boolean lUpdateIndex(String key, long index, Object value) {
        try {
            redisTemplate.opsForList().set(key, index, value);
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }


    /**
     * 移除N个值为value
     *
     * @param key   键
     * @param count 移除多少个
     * @param value 值
     * @return 移除的个数
     */

    public long lRemove(String key, long count, Object value) {
        try {
            Long remove = redisTemplate.opsForList().remove(key, count, value);
            return remove;
        } catch (Exception e) {
            e.printStackTrace();
            return 0;
        }

    }

}
```

```java
@Test
public void test1(){
   Map map=new HashMap();
   map.put("name","cqp");
   map.put("age","24");
   redisUtil.hmset("user:1",map);
   System.out.println(redisUtil.hmget("user:1"));
   redisUtil.del("name","age","sex");
}
```



### 10.  redis.conf配置详解

启动的时候，就通过配置文件来启动！

```bash
[root@iZbp1alfqlbdka40hbj17rZ ~]# cd ..
[root@iZbp1alfqlbdka40hbj17rZ /]# ls
bin  boot  dev  etc  home  lib  lib64  lost+found  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
[root@iZbp1alfqlbdka40hbj17rZ /]# cd usr
[root@iZbp1alfqlbdka40hbj17rZ usr]# ls
bin  etc  games  include  lib  lib64  libexec  local  sbin  share  src  tmp
[root@iZbp1alfqlbdka40hbj17rZ usr]# cd local
[root@iZbp1alfqlbdka40hbj17rZ local]# cd bin
[root@iZbp1alfqlbdka40hbj17rZ bin]# ls
cqpconfig  dump.rdb  redis-benchmark  redis-check-aof  redis-check-rdb  redis-cli  redis-sentinel  redis-server
[root@iZbp1alfqlbdka40hbj17rZ bin]# cd cqpconfig
[root@iZbp1alfqlbdka40hbj17rZ cqpconfig]# ls
redis.conf
[root@iZbp1alfqlbdka40hbj17rZ cqpconfig]# vim redis.conf
```



> 单位

![image-20201221180940528](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201221180940528.png)

1.配置文件unit单位对大小写不敏感



> 包含

![image-20201221181145557](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201221181145557.png)



> 网络

```bash
bind 0.0.0.0      # 绑定的ip
protected-mode yes       # 保护模式
port 6379             # 端口设置
```

> 通用General

```bash
daemonize yes  # 以守护进程(后台)的方式运行，默认是no  我们需要自己开启为yes！ 不然一退出进程就结束了

pidfile /var/run/redis_6379.pid  # 如果以后台的方式运行，我们就需要指定一个 pid 文件！

#日志
# Specify the server verbosity level.
# This can be one of:
# debug (a lot of information, useful for development/testing)      测试开发阶段
# verbose (many rarely useful info, but not a mess like the debug level)
# notice (moderately verbose, what you want in production probably)    生产环境
# warning (only very important / critical messages are logged)
loglevel notice
logfile ""    # 日志的文件位置名  为空就是标准输出
databases 16   # 数据库数量 默认16
always-show-logo yes  # 是否总是显示logo

```



> 快照 SNAPSHOTTING

持久化，在规定的时间内，执行了多少次操作，则会持久化到文件 .rdb  .aof

redis是内存数据库，如果没有持久化，那么数据断电即失！

```bash

save 900 1       # 如果900s内，如果至少有一个key进行了修改，我们就进行持久化操作。
save 300 10      # 如果300s内，如果至少10个key进行了修改，我们就进行持久化操作
save 60 10000    # 如果60s内，如果至少10000key进行了修改，我们即进行持久化操作

#我们只后学习持久化，会设置自己的去测试

stop-writes-on-bgsave-error yes  # 持久化如果出错，是否还需要继续工作！
rdbcompression yes               # 是否压缩rdb文件，需要消耗一些cpu资源！
rdbchecksum yes                  # 保存rdb文件的时候，进行错误的检查校验！
dbfilename dump.rdb              # rdb文件名称
dir ./                           # rdb文件保存的目录

```



> REPLICATION  复制  我们后面讲解主从复制的时候再进行讲解！















> SECURITY 安全

```bash
#                          获取redis 密码   默认为空
127.0.0.1:6379> ping
PONG
127.0.0.1:6379> config get requirepass
1) "requirepass"
2) ""
127.0.0.1:6379>

```

![image-20201221190631718](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201221190631718.png)



```bash
127.0.0.1:6379> config set requirepass "123456"     # 设置redis密码
OK
127.0.0.1:6379> config get requirepass              
1) "requirepass"
2) "123456"
127.0.0.1:6379> exit
[root@iZbp1alfqlbdka40hbj17rZ ~]#
[root@iZbp1alfqlbdka40hbj17rZ ~]# redis-cli
127.0.0.1:6379> ping
(error) NOAUTH Authentication required. 
127.0.0.1:6379> auth 123456                        # 登录
OK
127.0.0.1:6379>
```



> CLIENTS 客户端限制

```bash
# maxclients 10000                 能连接上redis的最大客户端数量
# maxmemory <bytes>                redis能配置的最大内存容量
# maxmemory-policy noeviction      内存到达上限后的处理策略       
        
        #移除一些过期的key
        #报错
        #。。。
        
redis.conf中的默认的过期策略是 volatile-lru

maxmemory-policy 六种方式
1、volatile-lru：只对设置了过期时间的key进行LRU（默认值） 

2、allkeys-lru ： 删除lru算法的key   

3、volatile-random：随机删除即将过期key   

4、allkeys-random：随机删除   

5、volatile-ttl ： 删除即将过期的   

6、noeviction ： 永不过期，返回错误

```



> APPEND ONLY MODE 模式  aof配置 

ps:  默认开启Rdb 不开启aof

```bash
appendonly no   # 默认是不开启aof模式的，默认是使用rdb方式持久化的，在大部分所有的情况下，rdb完全够用！
appendfilename "appendonly.aof"    # 持久化的文件的名字

# appendfsync always        # 每次修改都会 sync 消耗性能
appendfsync everysec        # 每秒执行一次 sync , 可能会丢失这1S的数据
# appendfsync no            # 不执行 sync 这个时候操作系统自己同步数据  redis性能最好
```

具体的配置在后面的redis持久化中详细描述。



### 11.  Redis持久化 （重点）



Redis 是内存数据库，如果不把内存中的数据库状态保存到磁盘，那么一旦服务器进程退出，服务器中的数据库状态也会消失。所以redis提供了持久化功能！

#### **11.1RDB** (Redis DataBase)

> 什么是RDB

在主从复制中，rdb就是备用的。

![image-20201222150437477](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201222150437477.png)



在指定的时间间隔内将内存中的数据集快照写入磁盘，恢复时将快照文件直接读到内存里。

Redis会单独创建（fork）一个子进程来进行持久化，会先将数据写入到一个临时文件中。待持久化过程结束，再用这个临时文件替换上次持久化好的文件。

整个过程中，主进程不进行任何IO操作。这就确保了极高的性能。如果需要进行大规模数据的恢复，且对于数据恢复的完整性不是非常敏感，那RDB方式要比AOF方式更加高效。

RDB的缺点是最后一次持久化后的数据可能丢失。

我们默认就是RDB,一般情况下不需要修改这个配置！

有时候，在生产环境我们会将这个文件进行备份！

**rdb保存的文件是dump.rdb**  都是在我们的配置文件快照中进行配置的







> 触发机制

1. save的规则满足的情况下，会自动触发rdb规则！
2. 执行 flushall命令，也会触发我们的rdb规则！
3. 退出redis,也会产生rdb文件
4. 备份就自动生成一个 dump.rdb

> 恢复 RDB 文件

只需要将rdb文件放在我们redis启动目录就可以，redis 启动的时候会自动检查 dump.rdb 恢复其中的数据！

```bash
127.0.0.1:6379> config get dir        # 查看redis 目录 把dump.rdb就在这
1) "dir"
2) "/usr/local/bin"
127.0.0.1:6379>
```

优点:

1.适合大规模的数据恢复！dump

2.如果对数据的完整性要求不高

缺点:

1.需要一定的时间间隔，如果redis意外宕机了，这个最后一次修改数据就没有的了！

2.fork进程的时候，会占用一定的内容空间



#### 11.2 AOF （Append Only File）

将我们所有的命令都记录下来！history ,恢复的时候把文件再执行一遍！

![image-20201222162152138](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201222162152138.png)



以日志的形式来记录每个写操作，将Redis执行过的所有指令记录下来（读操作不记录），只允许追加文件但不可以改写文件，redis

启动之初会读取该文件重新构建数据，换言之，redis重启的话就根据日志文件的内容将写指令从前到后执行一次以完成数据的恢复工作。

![image-20201222162716894](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201222162716894.png)



默认不开启，我们需要手动进行配置。 将no改为yes即可。

测试实验

1.配置文件改为yes 保存退出

2.删除bin下的dump.rdb

```bash
[root@iZbp1alfqlbdka40hbj17rZ bin]# ls
cqpconfig  dump.rdb  redis-benchmark  redis-check-aof  redis-check-rdb  redis-cli  redis-sentinel  redis-server
[root@iZbp1alfqlbdka40hbj17rZ bin]# rm -rf dump.rdb
[root@iZbp1alfqlbdka40hbj17rZ bin]# ls
cqpconfig  redis-benchmark  redis-check-aof  redis-check-rdb  redis-cli  redis-sentinel  redis-server
```



3.重启redis服务

```bash
127.0.0.1:6379> shutdown
not connected> exit
[root@iZbp1alfqlbdka40hbj17rZ bin]# redis-server cqpconfig/redis.conf
[root@iZbp1alfqlbdka40hbj17rZ bin]# redis-cli
127.0.0.1:6379> ping
PONG
```



4.查看bin目录下

```bash
[root@iZbp1alfqlbdka40hbj17rZ bin]# ls
appendonly.aof  cqpconfig  dump.rdb  redis-benchmark  redis-check-aof  redis-check-rdb  redis-cli  redis-sentinel  redis-server
```



5. 对redis进行操作

   ```bash
   127.0.0.1:6379> set k1 v1
   OK
   127.0.0.1:6379> set k1 v1
   OK
   127.0.0.1:6379> set k2 v2
   OK
   127.0.0.1:6379> set k3 v3
   OK
   127.0.0.1:6379>
   
   ```

   6.查看 appendonly.aof文件

   ![image-20201222164907843](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201222164907843.png)



ps: 如果aof文件被破坏，我们是启动不了redis的

![image-20201222165148985](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201222165148985.png)

我们可以用这个redis-check-aof去修复aof文件(在bin下执行)

redis-check-aof --fix appendonly.aof

> 重写规则



aof 默认就是文件的无限追加，文件会越来越大！

重写是为了将文件变得更小，降低文件占用空间，以便更快被redis加载。保存内存快照，再重写写aof文件。

![image-20201222183551893](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201222183551893.png)







> 优点和缺点

```bash
appendonly no   # 默认是不开启aof模式的，默认是使用rdb方式持久化的，在大部分所有的情况下，rdb完全够用！
appendfilename "appendonly.aof"    # 持久化的文件的名字

# appendfsync always        # 每次修改都会 sync 消耗性能
appendfsync everysec        # 每秒执行一次 sync , 可能会丢失这1S的数据
# appendfsync no            # 不执行 sync 这个时候操作系统自己同步数据  redis性能最好
```

优点:

1.每一次修改都同步，文件的完整会更好

2.每秒同步一次，可能会丢失一秒的数据

3.从不同步，效率最高。

缺点:

1.相对于数据文件来说。aof远远大于rdb,修复的速度也比rdb慢！

2.Aof 运行效率也比rdb慢，所以我们redis默认的配置是rdb持久化！





![image-20201222184656666](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201222184656666.png)

![image-20201222184725912](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201222184725912.png)



### 12.Redis 发布订阅

消息队列 （rabbitMq rocketMq）

Redis 发布订阅（pub/sub）是一种**消息通信模式**：发送者（pub）发送消息，订阅者（sub）接收消息。微信，微博，关注系统。

Redis 客户端可以订阅任意数量的频道。

订阅/发布消息图:

![image-20201222185229912](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201222185229912.png)





![image-20201222190515891](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201222190515891.png)





| 序号 | 命令及描述                                                   |
| :--- | :----------------------------------------------------------- |
| 1    | [PSUBSCRIBE pattern [pattern ...\]](https://www.runoob.com/redis/pub-sub-psubscribe.html) 订阅一个或多个符合给定模式的频道。 |
| 2    | [PUBSUB subcommand [argument [argument ...\]]](https://www.runoob.com/redis/pub-sub-pubsub.html) 查看订阅与发布系统状态。 |
| 3    | [PUBLISH channel message](https://www.runoob.com/redis/pub-sub-publish.html) 将信息发送到指定的频道。 |
| 4    | [PUNSUBSCRIBE [pattern [pattern ...\]]](https://www.runoob.com/redis/pub-sub-punsubscribe.html) 退订所有给定模式的频道。 |
| 5    | [SUBSCRIBE channel [channel ...\]](https://www.runoob.com/redis/pub-sub-subscribe.html) 订阅给定的一个或多个频道的信息。 |
| 6    | [UNSUBSCRIBE [channel [channel ...\]]](https://www.runoob.com/redis/pub-sub-unsubscribe.html) 指退订给定的频道。 |

> 测试

订阅端:

```bash
127.0.0.1:6379> SUBSCRIBE mychannel               # 订阅一个频道 mychannel
Reading messages... (press Ctrl-C to quit)       
1) "subscribe"
2) "mychannel"
3) (integer) 1                 # 等待读取信息
1) "message"                   # 消息
2) "mychannel"                 # 哪个频道的消息
3) "hello,mychannel"           # 消息的内容
```





![image-20201222191227170](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201222191227170.png)





发送端：

```bash
127.0.0.1:6379> PUBLISH mychannel "hello,mychannel"        # 发送者发布消息到频道
(integer) 1
```



![image-20201222191214873](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201222191214873.png)

> 原理

![image-20201222192521517](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201222192521517.png)



![image-20201222193415668](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201222193415668.png)



使用场景：

1. 实时消息系统

2. 实时聊天 （频道当聊天室）

3. 订阅，关注系统都是可以的

   稍微复杂的场景我们都会使用 消息中间件MQ（）



### 13.主从复制  （哨兵模式）

 

![image-20201222194328633](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201222194328633.png)



![image-20201222194043869](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201222194043869.png)

  主从复制，读写分离！  80%的情况下都是在进行读操作！减缓服务器压力！架构中经常使用！一主二从！

 只要在公司中，主从复制就是必须的，因为在真实的项目中不可能单机使用redis!



> 环境配置



只配置从库，不配置主库！redis 默认自己本身就是一个主库！

```bash
127.0.0.1:6379> info replication                # 查看当前库的信息
# Replication
role:master                                     # 角色 master
connected_slaves:0                              # 没有从机
master_replid:4622e1bdb97c4d9ddc82702ef33309b98e11683e
master_replid2:0000000000000000000000000000000000000000
master_repl_offset:0
second_repl_offset:-1
repl_backlog_active:0
repl_backlog_size:1048576
repl_backlog_first_byte_offset:0
repl_backlog_histlen:0
```





![image-20201223141412570](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201223141412570.png)



1.打开四个窗口模拟四台服务器。都切换到/usr/local/bin 目录下 复制配置文件



```bash
 ls
redis.conf
[root@iZbp1alfqlbdka40hbj17rZ cqpconfig]# cp redis.conf redis79.conf            # 复制配置文件
[root@iZbp1alfqlbdka40hbj17rZ cqpconfig]# cp redis.conf redis80.conf
[root@iZbp1alfqlbdka40hbj17rZ cqpconfig]# cp redis.conf redis81.conf
[root@iZbp1alfqlbdka40hbj17rZ cqpconfig]# ls
redis79.conf  redis80.conf  redis81.conf  redis.conf
[root@iZbp1alfqlbdka40hbj17rZ cqpconfig]# vim redis79.conf                     
```



2.修改第一个 6379端口的配置文件 只修改logfile 和 dbfilename 不然会重名

![image-20201223142015217](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201223142015217.png)

![image-20201223142114747](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201223142114747.png)



3.修改其余的配置文件    （端口号 后台进程文件 日志文件 rdb文件）

​       修改redis80.conf

![image-20201223142527915](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201223142527915.png)

![image-20201223142623422](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201223142623422.png)

![image-20201223142701053](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201223142701053.png)

![image-20201223142757700](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201223142757700.png)



修改redis81.conf 同上 把80改成81即可



4.启动服务

三个窗口分别启动服务

![image-20201223143547455](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201223143547455.png)

![image-20201223143555977](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201223143555977.png)



![image-20201223143604655](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201223143604655.png)



![image-20201223143621034](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201223143621034.png)



环境搭建完成！3个redis



开始测试

> ### **一主二从**

**默认情况下，每台redis服务器都是主节点。** 我们一般情况下只用配置从机就好了。

一主（79）二从（80，81）



分别连上客户端

```bash
127.0.0.1:6380> SLAVEOF 127.0.0.1 6379     # 配置从机 找老大
OK
```

![image-20201223145442072](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201223145442072.png)



在主机中查看

![image-20201223145542362](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201223145542362.png)

81同理配置



ps： 真实的配置应该是在配置文件中配置的，这样的话是永久的！用命令配置的话是暂时的！

![image-20201223145924638](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201223145924638.png)



> 细节

主机可以写，从机不能写只能读！主机中的所有信息和数据，都会自动被从机保存！

![image-20201223150219397](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201223150219397.png)



![image-20201223150228840](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201223150228840.png)



![image-20201223151649083](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201223151649083.png)



> **宕机后手动配置主机**

![image-20201224093327994](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201224093327994.png)



层层链路：上一个master连接下一个slave,这样也可以完成主从复制！

![image-20201224094830766](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201224094830766.png)



> ### **哨兵模式**    （重点）   

自动选取老大

概述

![image-20201224095335663](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201224095335663.png)



![image-20201224095510611](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201224095510611.png)



![image-20201224095823699](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201224095823699.png)

> 测试！

我们现在是一主二从





![image-20201224095620157](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201224095620157.png)







1.在config目录下，新建哨兵配置

![image-20201224100619931](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201224100619931.png)

配置哨兵

![image-20201224100755301](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201224100755301.png)



```bash
# sentinel monitor <master-group-name> <ip> <port> <quorum>                
sentinel monitor myredis 127.0.0.1 6379 1   
```

后面的这个数字1，代表主机挂了，slave投票看让谁接替成为主机，票数最多的，就会成为主机！ 有一个哨兵检测到了主机没响应了，就把主机从 SDOWN 变为 ODOWN 将会开启 failover 切换。



2. 启动哨兵

```bash
redis-sentinel cqpconfig/sentinel.conf
```



![image-20201224104429361](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201224104429361.png)



如果master节点断了。这个时候哨兵就会从从机中投票选举一个主机。          

![image-20201224104858356](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201224104858356.png)

 如果主机回来了，只能归并到新的主机下，当做从机，这就是哨兵模式规则。



 优点：

1.哨兵集群，基于主从复制，所有的主从配置优点，它全有。

2.主从可以切换，故障可以转移，系统的可用性会更好。

3.哨兵模式就是主从模式的升级，手动到自动，更加健壮！



缺点：

1. Redis 不会在线扩容，集群容量达到上限，在线扩容很麻烦。
2. 实现哨兵模式的配置其实是很麻烦的，里面有很多选择。

> 哨兵模式的全部配置















> 哨兵原理介绍

**SDOWN**:subjectively down    ”主观”失效   即当前sentinel实例认为某个redis服务为”不可用”状态.

**ODOWN**:objectively down    ”客观”失效    即多个sentinel实例都认为master处于”SDOWN”状态,那么此时master将处于ODOWN,ODOWN可以简单理解为master已经被**集群**确定为”不可用”,将会开启 **failover** (切换)

每个sentinel实例在启动后,都会和已知的slaves/master以及其他sentinels建立**TCP连接**,并周期性发送PING(默认为1秒),在交互中,如果redis-server无法在”down-after-milliseconds”时间内响应或者响应错误信息,都会被认为此redis-server处于SDOWN状态.

* 主机宕机了，那么此时sentinel实例将会向其他sentinel间歇性(一秒)发送”is-master-down-by-addr ”指令并获取响应信息,如果足够多的sentinel实例检测到master处于SDOWN,那么此时当前sentinel实例标记master为SDOWN…其他sentinel实例做同样的交互操作.配置项”sentinel monitor ”,如果检测到master处于SDOWN状态的slave个数达到,那么此时此sentinel实例将会认为master处于ODOWN.

* 每个sentinel实例将会间歇性(10秒)向master和slaves发送”INFO”指令,如果master失效且没有新master选出时,每1秒发送一次”INFO”;”INFO”的主要目的就是获取并确认当前集群环境中slaves和master的存活情况.

  经过上述过程后,所有的sentinel对master失效达成一致后,开始failover.

* 在sentinel的配置文件中,都指定了port,此port就是sentinel实例侦听其他sentinel实例建立链接的端口.在集群稳定后,最终会每个sentinel实例之间都会建立一个tcp链接,此链接中发送”PING”以及类似于”is-master-down-by-addr”指令集,可用用来检测其他sentinel实例的有效性以及”ODOWN”和”failover”过程中信息的交互.在sentinel之间建立连接之前,sentinel将会尽力和配置文件中指定的master建立连接.sentinel与master的连接中的通信主要是基于pub/sub来发布和接收信息,发布的信息内容包括当前sentinel实例的侦听端口。





### 14.Redis 缓存穿透和雪崩 （面试高频，工作常用）



缓存穿透，缓存雪崩和缓存击穿。

![image-20201224144344355](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201224144344355.png)







#### 14.1  缓存穿透  (多个key 查不到)

用户想要查询一个数据，redis内存数据中没有，于是向持久层数据库查询。发现也没有，于是本次查询失败。

当用户很多的时候，缓存都没有命中，于是都去请求持久层数据库，这会给持久层数据库造成很大的压力。



**解决方案1:**    布隆过滤器

布隆过滤器是一种数据结构，对所有可能查询的参数以hash形式存储，在控制层先进行校验，不符合则丢弃，从而避免对底层存储系统的查询压力。



本质上布隆过滤器是一种数据结构，比较巧妙的`概率型数据结构`，特点是`高效地插入和查询`。根据查询结果可以用来告诉你 `某样东西一定不存在或者可能存在` 这句话是该算法的核心。

相比于传统的 List、Set、Map 等数据结构，它更高效、占用空间更少，但是缺点是其返回的结果是概率性的，而不是确切的，同时布隆过滤器还有一个缺陷就是

`数据只能插入不能删除`。

![image-20201224144846976](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201224144846976.png)





**解决方案2：** 缓存空对象



![image-20201225104802838](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201225104802838.png)



当存储层（mysql数据库）不命中后，即使返回空对象也缓存起来，同时设置个一个过期时间，之后再访问这个数据将从缓存中获取，保护后端数据源。

但是这种方法存在两个问题:

1. 缓存需要更多的空间存储更多的键，因为其中可能有很多空值的键。
2. 即使对空值设置了过期时间，还是会存在缓存层和存储层的数据会有一段时间窗口的不一致，这对于需要保持一致性的业务会有影响。



#### 14.2 缓存击穿 （单个key 一直查 过期就完了）

微博服务器宕机 （60s 60.1s恢复  0.1s）

> 概述

缓存击穿，是指一个key非常热门，在不停的扛着大并发，大并发集中对这一个点进行访问，当这个key在失效的瞬间，持续的大并发就击穿缓存，直接请求数据库。

当某个key在**过期**的瞬间，会有大量的请求并发访问，这类数据一般是热点数据，由于缓存过期，会同时访问数据库来查询最新数据，并且回写缓存，会导致数据库瞬间压力过大。

> 解决方案

1. 设置热点数据永不过期



2. **互斥锁**

   分布式锁，保证对于每个key同时只有一个线程去查询后端服务，其他线程没有获得分布式锁的权限，因此只需要等待即可。

   这种方式将高并发的压力转移到了分布式锁，因此对分布式锁的考验很大。





### 14.3 缓存雪崩

> 概念

缓存雪崩，在某一个时间段，缓存集中过期失效。或者 Redis 宕机！

例子：双十二零点，很快迎来一波抢购，这波商品时间比较集中放入缓存，假设一个小时。那么到凌晨一点钟的时候，这批商品缓存过期了，而对这批商品的访问查询都落在了数据库上，

对于数据库而言，就会产生周期性的压力波峰。于是所有的请求都会到达存储层，存储层调用量暴增，挂掉！

![image-20201225113458539](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201225113458539.png)



其实集中过期，不是非常致命，比较致命的是缓存服务器某个节点宕机或者断网。



双十一: 停掉一些服务，保证主要服务可用。 比如第二天才可以申请退款。



![image-20201225113928186](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201225113928186.png)











学习资料:https://www.bilibili.com/video/BV1S54y1R7SB?p=36







### 扩展(阿里云服务器)

1.ssh登录

```bash
chenqipeng@chenqipengdeMacBook-Pro ~ % ssh root@47.110.154.185
Last login: Wed Dec  9 17:08:54 2020 from 115.236.9.89

Welcome to Alibaba Cloud Elastic Compute Service !

[root@iZbp1alfqlbdka40hbj17rZ ~]# cd .ssgh
-bash: cd: .ssgh: 没有那个文件或目录
[root@iZbp1alfqlbdka40hbj17rZ ~]# cd .ssh
[root@iZbp1alfqlbdka40hbj17rZ .ssh]# ls
authorized_keys
[root@iZbp1alfqlbdka40hbj17rZ .ssh]# vim authorized_keys
[root@iZbp1alfqlbdka40hbj17rZ .ssh]#
```

```bash
Last login: Wed Dec  9 17:01:48 on ttys000
chenqipeng@chenqipengdeMacBook-Pro ~ % cd .ssh
chenqipeng@chenqipengdeMacBook-Pro .ssh % ls
id_rsa		id_rsa.pub	known_hosts
chenqipeng@chenqipengdeMacBook-Pro .ssh % tail id_rsa.pub
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQDbfUsULjElv/4fJgdj4JdQcreUyQ5C/aFuyEpCCJRy5dSTFfhRPBKFyGSO/kh7jt+Pse9JXeCnq6mTC6LrpHnjBFmFrZTpikhNbY+p/EJSFCfQwdLFXzqs70NY+AmAbdxX6cwdwrhHAFGeZm7LUlb8fwRErrlmTIPp6F63CiqZug33Z6ls6pKKK6C4cj0j6faBIWVFq5hvjjnXFtecEaEgzy2Pa7Z1RSfoxfcFg1cBZOlQCtitPXzHEIMLU5Cb91Mn/zmEHFxLXheoZCw/nLGwzCfJwyJhwWGO0yJbU2FFwCEgEVZ4WGf2NvVa4rO78c1RBOIZS0QrFDPpqlft7xKxR3w/e7WoLhUURK3ADutSgQZ8QOrPhr1VPNKM+GnIvMVOAC91FKUgKzEN5ehe/ikjSfdjXQBaPJrbPKm6Ody/Enj9dJ3XZgWwlgN/l/gitUOUJDG35n2otLC9R4UytD2Bzwy6eG6n6FL1TWvVAuEExwCPmJGH3j3ylur4tcv8Otc= 2111053717@qq.com
chenqipeng@chenqipengdeMacBook-Pro .ssh %
```



2.删除公钥



>vim ~/.ssh/known_hosts





3.Linux下编程难免会开启多次vim编辑， 同一个文件如果在上一次编辑时未进行保存，则在下一次想要进行编辑时就会出现：

swap file "*.swp" already exists!

[O]pen Read-Only, (E)dit anyway, (R)ecover, (D)elete it, (Q)uit, (A)bort:

原因：

*使用\*vim\*编辑文件实际是先\*copy\*一份临时文件并映射到内存给你编辑， **编辑的是临时文件， **当执行：\*w\*后才保存临时文件到原文件，执行：\*q后\*才删除临时文件。*************

*每次启动编辑时都会检索这个文件是否已经存在临时文件， **有则询问如何处理，就会出现如上情景。***

 解决方法（删除这个临时文件即可）：

> rm .*.swp





4.目前redis最新版本已经是6.0.8了。![[笑哭]](https://i0.hdslb.com/bfs/emote/c3043ba94babf824dea03ce500d0e73763bf4f40.png@100w_100h.webp) Redis6以上需要gcc版本在7以上。
运行下面命令升级gcc
\#第一步
sudo yum install centos-release-scl
\#第二步
sudo yum install devtoolset-7-gcc*
\#第三步
scl enable devtoolset-7 bash



5.  用Jedis连接阿里云等服务器上的redis

一. 配置redis.conf
    1.设置访问redis的密码：requirepass 要设置密码
    2.注释bind 127.0.0.1
    (重启redis-server服务,进入redis后要先验证密码,用这个命令：auth 密码 ,然后ping一下看有没有配置成功)
二 . idea访问时添加auth密码
  Jedis jedis = new Jedis("服务器的外网ip",6379);
  jedis.auth("redis的密码");
  System.out.println(jedis.ping());
（输出PONG的话就成功了）



三.远程阿里云无法连接的问题： 只需要把配置文件里的bind 127.0.0.1 改为 bind 0.0.0.0即可。亲测有效。









 nohup java -jar lab-0.0.1-SNAPSHOT.jar >lab_log.txt &

netstat -alnp | grep 18080











 