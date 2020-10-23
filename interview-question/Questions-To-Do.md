
web方面：

servlet是否线程安全，如何改造

session与cookie的区别，get和post区别，tcp3次握手，文件上传用post还是get

session的存储

如何防止表单重复提交


数据库：

最重要的索性及底层实现

索性失效的场景

最左原则

查看执行计划

及carndiation

然后是锁的类型，行级表级

悲观乐观锁

解释数据库事物及特性

隔离级别

及实现，redo log .undo log

bin log主从复制

mvcc,Next-Key Lock


分布式：

问了CAP，跟base

zookeeper满足了CAP的哪些特性，paxos

缓存穿透怎么解决

redis的io模型

如果保证redis高可用

redis是单线程还是多线程

线上cpu占比过高怎么排查

一致性hash

分库分表

spring:

ioc,aop原理

ioc初始化流程

springmvc的流程

springboot,spring cloud相关组件




如何设计存储海量数据的存储系统

缓存的实现原理，设计缓存要注意

淘宝热门商品信息在JVM哪个内存区域

操作系统的页式存储


Lucene全文搜索的原理

你觉得自己适合哪方面的开发，为什么



AOP的

动态代理与cglib实现的区别（这个，醉得很厉害）

那么你说说代理的实现原理呗

看过Spring源码没，说说Ioc容器的加载过程吧

了解过字节码的编译过程吗（这个还真不知道）



https://docs.qq.com/doc/DWldvV2FBTmpKZFZS

kafka 怎么防止消息被重复消费、怎么确保至少被消费一次

算法领域、开发领域、业务运营 找好方向 做精做深做透、才有好处

工程领域要了解细节、不要太偏实用

java编程思想 spirng

网络、中间件

不要被公司决策左右、对工具选择要有自己的考虑


1、操作系统：

聊一聊操作系统的内存管理？具体一点，聊一聊虚拟内存？

线程和进程有什么区别？具体一点，谈一谈它们堆栈的关系（区别）？

怎么处理高并发问题？加锁、控制信号量、加条件？怎么处理死锁？


3、数据库：

什么是数据库的第一范式和第二范式？

谈一谈数据库索引为什么可以加快查找速度？数据库主键可以有很多个吗？为什么只能有一个主键？

4、算法：

算法题1：怎么最快的找到一堆大数组里面第十大的数？

算法题2：从一个素数到另一个素数有几种方式？

基本技能：
(1) Java 基础、JVM 调优、Servlet、网络编程。
(2) 熟悉 Spring、Spring MVC、Spring Cloud、Dubbo、MyBatis、Zookeeper 等主流开发框架。
(3) 熟悉数据库 MySQL 和 MongoDB、消息中间件 ActiveMQ，缓存 Redis，搜索引擎 Solr。
(4) 熟悉分布式高可用、分布式事务、常用的设计模式、架构模式、代码重构。
(5) 熟悉 Tomcat、Nginx。
(6) 熟悉云平台/容器化技术开发 Kubernetes、Docker、Etcd、ELK 等。
具备的外围技术：
UML、Linux Shell、HTTP1.1/2、NIO、Netty、Golang 、Protobuf、gRPC
工作流程必备技术：
Maven、Jenkins、svn、git、Power Designer、StarUML

Object
        讲了 Object 类的几个方法的用途，hashcode 方法是怎么算的。

Java 原子类型、包装类型
        包装类的常量池，包装类对象的大小比较，偏源码。

StringBuffer 和 StringBuilder
        底层数据结构，源码是如何 append 的，线程安全性。

Java 集合框架
        ArrayList 扩容的实现，讲到数组 Arrays.copyOf() 和 System.arraycopy()。

HashMap 和 ConcurrentHashMap
        数据结构，拉链结构，红黑树，ConcurrentHashMap 如何保证线程安全和高性能，CAS。

Java 多态性
        讲到面向接口编程的好处。

Java 异常体系
        讲了 Throwable 的子类 Error 和 Exception，Throwable 为什么要设计成实现 Serializable 接口。如何捕获和处理线程异常。

Java 动态代理、反射机制
        JDK 动态代理 reflect 包下的 InvocationHandler 接口的 invoke() 方法，Cglib 动态代理。Class 类相关的点。

Java 类加载双亲委派
        三种类加载器：Booststrap、Extendsion、Application ClassLoader，重写 loadClass 方法、线程上下文类加载器(Thread Context ClassLoader)、SPI 打破双亲委派。问了 OSGi 但是我不懂这块。

Java 锁机制

Java 并发机制 JUC、AQS

CAS 和 ABA 问题

JVM 结构

JVM 内存模式

JVM GC

JVM 调优参数

JVM 排错

Spring Bean 和 IOC

Spring AOP 实现原理

Spring 事务

SpringMVC 和 DispatcherServlet

Spring Cloud

Dubbo 原理、源码

session 和 cookie

TCP/IP 三次握手、四次挥手

git、maven 的常用命令

.Servlet 容器 Tomcat 启动过程，生命周期

Tomcat 调优

NIO、Reactor 模型

Netty 和 TCP 粘包、拆包

C10K

epoll 和 poll

https 如何建立连接

Linux 资源查询和管理

Redis 支持的数据类型

Redis 淘汰策略

Redis 数据安全

分布式唯一 ID

Redis 分布式锁

一致性 Hash

分布式事务 CAP

2PC、3PC、BASE、TCC、消息中间件

Paxos 算法和 ZAB 协议

限流策略

Java RPC

gRPC

Mybatis 原理

设计模式

MySQL

幻读、读脏

MVCC

InnoDB 及索引原理

SQL 优化

数据库中间件

MySQL 集群和 HA

消息中间件

Solr 索引原理、如何工作

HR 面试题

https://www.huahouye.com/topic/2/2018-%E5%B9%B4%E9%98%BF%E9%87%8C%E5%B7%B4%E5%B7%B4%E7%9A%84-java-%E9%9D%A2%E8%AF%95%E7%BB%8F%E5%8E%86

https://blog.csdn.net/qq_15029743/article/details/96434184