学习任务

Java的面试大概就分成

Java本身         

Spring等各种框架

RPC （Remote Procedure Call）远程过程调用

消息队列中间件 （Redis Rocketmq ElasticSearch Logstash Canal Dubbo ）

数据库

计算机网络

操作系统

这几方面来考





Java的一些基础知识，比如hashMap实现，ArrayList实现，ThreadLocal实现等就去看[源码](https://www.nowcoder.com/jump/super-jump/word?word=源码)就好了

然后就是Java的一些**并发**相关的知识看《Java并发编程艺术》基本就够了，这本书称得上是面试神书，基本考并发的内容答案都能在这本书上找到，当然书本本身并不是很好理解。《深入理解Java虚拟机》同样也是面试神书，基本JVM虚拟机的内容答案都在这本书上。

至于Java的IO相关的知识其实主要就是NIO和BIO两种，不过现在很多也是在使用**Netty**框架了，推荐大家可以去看看闪电侠的文章，这里把网址推给大家： https://juejin.im/book/6844733738119593991

Spring的话IOC和AOP  自己实现一个简易版的IOC和AOP也是一个极佳的做法 

http://www.tianxiaobo.com/2018/01/18/%E8%87%AA%E5%B7%B1%E5%8A%A8%E6%89%8B%E5%AE%9E%E7%8E%B0%E7%9A%84-Spring-IOC-%E5%92%8C-AOP-%E4%B8%8A%E7%AF%87/



**MySQL**的学习的话推荐大家去看看尚硅谷的MySQL高级课程，基本可以对于MySQL有个大概的了解，小白的话可以先看看《MySQL必知必会》，同时加上牛客网的MySQL练习题应该可以快速入门

Redis就直接看《Redis设计与实现》好了，这本书真的是通俗易懂，读起来几乎没什么障碍，小白的话应该也可以直接上手

至于**RPC**，**消息队列**等，推荐大家就去使用一下，然后针对某个方面的[源码]()去看就好了，要把[源码]()整个看掉还是比较难得。比如RPC，现在一般的方法都是 

 服务器发表服务到zoo[keep]()er上 --> [客户端]()去zoo[keep]()er上申请服务 --> 申请成功后[客户端]()调用方法 -->实质是通过代理模式传递packet --> 通过Neety传递packet到服务端 --> 服务端处理请求之后通过Netty写回 大概就是整个过程，大家可以自己实现一下

**消息队列**可以看看尚硅谷的kafka入门： https://www.bilibili.com/video/BV1a4411B7V9?from=search&seid=15264133011023976835

这部分的话也没什么太多的诀窍，就把

[剑指offer]

LeetCode top100liked

top interviewed 

[leetcode](https://www.nowcoder.com/jump/super-jump/word?word=leetcode)前三百

刷熟吧，面试时候还是会有很多原题的



但是对于在校生而言实际工程也许会比较难我比较推荐大家看看RPC[源码]()，然后深入看Netty[源码]()看看网络通信是怎么做的，这两方面感觉会和实际关联非常紧密。 

 或者看看Spring的[源码]()，实际Java工程几乎都是必须要用到的，也是一个加分项







java

*  spring                   spring实战（第四版） spring5核心原理手写30个类  
*   spring boot         玩转spring全家桶   
*  网络编程               nio netty   
*  jvm虚拟机          《深入理解Java虚拟机》
*  java 并发编程    《Java并发编程艺术》
* mysql                    尚硅谷的MySQL高级课程
* redis                      《Redis设计与实现》

  

 （以上都为研一需要掌握）



算法与数据结构

计算机组成原理

计算机网络



100篇面经笔记。研二







作者：冥牙
链接：https://www.nowcoder.com/discuss/533053?type=all&order=time&pos=&page=1&channel=-2&source_id=search_all
来源：牛客网

1. 线程安全本质是什么，Java如何保证线程安全，callable，runnable有什么区别，线程不正常终止会发生什么，线程占用的空间具体是哪，是寄存器还是内存还是什么 
2.  Java的线程和Linux的线程有什么区别，为什么需要Java的线程    
3.  volatile具体实现原理，内存重[排序]()都会发生在哪，为什么要内存重[排序]() 
4.  使用过哪些Java并发包 
5.  简述BIO，NIO的具体使用及原理 
6.  concurrentHashMap1.8和之前版本有什么区别，HashMap的具体实现，[红黑树]()和平衡[二叉树]()的区别，为什么不用B+树
7.  Java的GC整体过程，垃圾收集[算法]()，流程，垃圾收集器，强引用，弱引用，虚引用等概念等，如果我要设置一个内存缓冲区，让垃圾收集器不对其进行操作怎么办
8.  JVM内存分区，。假设Java 的GC时间过长，简述应该如何做来排查解决这个问题，jamp的dump命令比较重，有什么代替的方法。
9.  代码实现每五分钟一次Minor GC，如果要FullGC呢 
10.  ThreadLocal的具体是怎样的，为什么会有内存泄漏问题，怎样避免 
11.  Java的异常种类有哪些，平时自己是怎么处理异常的 
12.  Spring的IOC和AOP及MVC机制，Sping中的单例bean是否可以依赖多例bean 
13.  Springboot起步依赖有什么好处，为什么使用MyBatis不适用数据库连接池 
14.  tomcat如果有两个[项目]()，两个[项目]()里面如果有相同的class，那么tomcat是如何对其进行区别 
15.  简述MySQL的ACID性质及实现，ACID有哪些一致性种类，乐观锁怎么实现的，简述MySQL的隔离机制及实现，简述MySQL索引结构及实现
     
16.  如果MySQL要插入十万条数据，有什么较好的方法 
17.  Redis支持集群的几种方式，[redis]()的跳表要实现快速查找第k小的元素怎么做 
18.  Netty 拆包粘包的实质，Netty线程池中的线程建立连接之后，这条连接是不是始终属于这个请求，对于Netty来说是不是只占用服务端的一个套接字，了解zero copy嘛
      
19.  简述TCP三次握手四次挥手的状态转移，ip层的mtu是什么，如何探测机器之间的mtu
     
20.  进程和线程的区别，进程的通信方式，线程空间中的内容，进程中打开文件其他线程可以直接读写那个文件嘛。用户态和内核态对于读写文件的操作有什么区别
     
21.  内存为什么分页，虚拟内存有什么用，内存中的脏页是由硬件完成还是软件完成
     
22.  怎样用单线程实现2kQPS对于服务器压测，1s均匀发2k条消息，可以异步返回
     
23.  Nginx和Redis能同时处理很多条请求，万级别，是不是都是通过Linux中的IO模型的的类似信号的机制.想这样的C10K问题，也就是单机10000条并发连接应该如何去处理
     
24.  设计高并发秒杀系统，里面的阻塞队列是如何实现的
     
25.  在王者荣耀里面，当渲染地图时，考虑网络波动的情况应该怎样渲染地图才能显得不卡，这回导致什么问题
     
26.  如何实现cookie和session机制
     
27.  token在分布式服务器上的应用，用什么框架来验证token；除了[redis]()还可以怎么做异步框架
     

  28.mybatis的一级二级缓存 

  通过这个问到hashmap 

   hashmap和hashtable的区别 

29、知道哪些集合 

  30、ArrayList和linkedlist区别 

  31、线程的创建方式 

  32、线程的 状态

作者：杨子琦
链接：https://www.nowcoder.com/discuss/515742?type=post&order=time&pos=&page=1&channel=1009&source_id=search_post
来源：牛客网



1. GC相关： 

- 垃圾回收[算法]()有哪些？ 
- 可达性[算法]()怎么理解？哪些是Root对象？ 
- 各种引用（强引用/弱引用/软引用）与垃圾回收的关系？ 

1. JVM内存相关： 

- 内存区域划分（线程/进程相关） 
- 为什么要有元空间？ 

1. 类加载：类加载的过程/双亲委派模型 
2. Redis：数据类型？[redis]()分布式锁怎么实现？还能怎么实现分布式锁？ 
3. ConcurrentHashMap怎么实现线程安全？1.7和1.8有什么区别？还有哪些线程安全的集合？

