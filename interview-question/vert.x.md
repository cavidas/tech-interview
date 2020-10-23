https://zhuanlan.zhihu.com/p/33832486
https://juejin.im/entry/5c4ff6256fb9a04a0b22936d
https://vertxchina.github.io/vertx-translation-chinese/core/Core.html
To read:
https://segmentfault.com/a/1190000005713918
Vert.x:
====
#1.	What is vert.x

Eclipse Vert.x is a toolkit for building reactive applications on the JVM.
the core library defines the fundamental APIs for writing asynchronous networked applications
Vert.x is based on the Netty project, a high-performance asynchronous networking library for the JVM.
Because Vert.x was designed for asynchronous communications it can deal with more concurrent network connections with less threads than synchronous APIs such as Java servlets or java.net socket classes. 

# 2. Some concepts:
## what a verticle is,

Many networking libraries and frameworks rely on a simple threading strategy: 
each network client is being assigned a thread upon connection, 
and this thread deals with the client until it disconnects. 
This is the case with Servlet or networking code written with the java.io and java.net packages.
While this "synchronous I/O" threading model has the advantage of remaining simple to comprehend,
it hurts scalability with too many concurrent connections as system threads are not cheap, 
and under heavy loads an operating system kernel spends significant time just on thread scheduling management. 
In such cases we need to move to "asynchronous I/O" for which Vert.x provides a solid foundation.

The unit of deployment in Vert.x is called a Verticle. 
A verticle processes incoming events over an event-loop, 
where events can be anything like receiving network buffers, 
timing events, or messages sent by other verticles. 
Event-loops are typical in asynchronous programming models:

Each event shall be processed in a reasonable amount of time to not block the event loop. This means that thread blocking operations shall not be performed while executed on the event loop,  Vert.x offers mechanisms to deal with blocking operations outside of the event loop. In any case Vert.x emits warnings in logs when the event loop has been processing an event for too long, 

By default Vert.x attaches 2 event loops per CPU core thread.  a regular verticle always processes events on the same thread, so there is no need to use thread coordination mechanisms to manipulate a verticle state

a verticle can be deployed several times:
Incoming network data are being received from accepting threads then passed as events to the corresponding verticles. When a verticle opens a network server and is deployed more than once, then the events are being distributed to the verticle instances in a round-robin fashion which is very useful for maximizing CPU usage with lots of concurrent networked requests.

### Verticle 的类型
    •	Stardand Verticle 是最常用的一类 Verticle —— 它们永远运行在 Event Loop 线程上。
    •	Worker Verticle 会运行在 Worker Pool 中的线程上。一个实例绝对不会被多个线程同时执行。
    •	Multi-Threaded Worker Verticle也会运行在 Worker Pool 中的线程上。一个实例可以由多个线程同时执行（注：因此需要开发者自己确保线程安全）。 
    
### Standard Verticle
    •	当 Standard Verticle 被创建时，它会被分派给一个 Event Loop 线程，并在这个 Event Loop 中执行它的start方法。当您在一个 Event Loop 上调用了 Core API 中的方法并传入了处理器时，Vert.x 将保证用与调用该方法时相同的 Event Loop 来执行这些处理器。
    •	Verticle 实例中所有的代码都是在相同Event Loop中执行（只要您不创建自己的线程并调用它！）
    •	意味着您可以将您的应用中的所有代码用单线程方式编写，让 Vert.x 去考虑线程和扩展问题。您不用再考虑 synchronized 和 volatile 的问题，也可以避免传统的多线程应用经常会遇到的竞态条件和死锁的问题。

### Worker Verticle
    •	Worker Verticle 和 Standard Verticle 很像，但它并不是由一个 Event Loop 来执行，而是由Vert.x中的 Worker Pool 中的线程执行。
    •	Worker Verticle 被设计来调用阻塞式代码，它不会阻塞任何 Event Loop。
    •	如果您不想使用 Worker Verticle 来运行阻塞式代码，您还可以在一个Event Loop中直接使用 内联阻塞式代码。
    •	若您想要将 Verticle 部署成一个 Worker Verticle，您可以通过 setWorker 方法来设置。
    •	Worker Verticle 实例绝对不会在 Vert.x 中被多个线程同时执行，但它可以在不同时间由不同线程执行。

### Multi-threaded Worker Verticle
    •	一个 Multi-threaded Worker Verticle 近似于普通的 Worker Verticle，但是它可以由不同的线程同时执行。
    •	Multi-threaded Worker Verticle 是一个高级功能，大部分应用程序不会需要它。由于这些 Verticle 是并发的，您必须小心地使用标准的Java多线程技术来保持 Verticle 的状态一致性。

### Actor并发模型
    •	在Actor并发模型中，它的主角是Actor，实际上它类似于一种Worker线程，它们之间通过消息相互通信（在Vert.x中，它们之间的消息介质就是Event Bus），这些消息的发送是异步的，可并行。
    •	所有Actor的状态由Actor自身维护，且外部不可访问它，除非它自己暴露了访问自身状态的接口；
    •	Actor和Actor之间的通信使用的是消息；
    •	一个Actor可发送消息给另外一个Actor，也可以响应另外一个Actor发过来的消息，同时它可以改变自身的内部状态；
    •	Actor可能阻塞自己（就是内部调用阻塞IO的代码，但Actor不应该阻塞它运行的线程）。
    •	Actor模型的代表：Akka/Erlang、以及Vert.x都是使用的Actor模型。
    •	https://juejin.im/entry/5c4ff6256fb9a04a0b22936d



## How the event bus allows verticles to communicate.
The event-bus allows passing any kind of data, 
although JSON is the preferred exchange format since it allows verticles written in different languages to communicate, 
and more generally JSON is a popular general-purpose semi-structured data marshaling text format.
The event bus supports the following communication patterns:
1.	point-to-point messaging, and
2.	request-response messaging and
3.	publish / subscribe for broadcasting messages.


The event bus allows verticles to transparently communicate not just within the same JVM process:
    
    •	when network clustering is activated, the event bus is distributed so that messages can be sent to verticles running on other application nodes,
    •	the event-bus can be accessed through a simple TCP protocol for third-party applications to communicate,
    •	the event-bus can also be exposed over general-purpose messaging bridges (e.g, AMQP, Stomp),
    •	a SockJS bridge allows web applications to seamlessly communicate over the event bus from JavaScript running in the browser by receiving and publishing messages just like any verticle would do.


Some Eventbus consumer https://leokongwq.github.io/2017/11/17/vertx-event-bus.html

异步的问题
基于全异步Java服务器Netty。
异步无锁编程。

2.	Why do you use it
3.	What problem does it solve

    它利用Netty的Eventloop为开发者提供了更友好的编程模型。
    Netty作为一个网络I/O工具，使用起来随时都能清晰的感受到你在操作网络，在操作字节等这些与业务不太相关的东西。
    很明显，Netty解决的同高吞吐的网络I/O问题，而不是编程问题。
    只解决了如何用少量线程支撑起大量连接的问题，你连接是建立起来了，但是你的业务处理逻辑并没有因此而变的更快(吞吐量更高)。
    
    Vert.x鼓励你将JDBC调用、Redis查询、文件I/O等所有可能会导致线程阻塞的操作全都异步化以进一步压榨CPU利用率。
    为了实现这一点，Vert.x官方就提供了很多常见服务的异步化实现，
    如jdbc-client, redis-client, mongodb-client, web-client等，用起来就跟node.js一样，
    都是在发起操作时注册一个回调，当处理完成后回调方法就会得到执行。
    与此同时，Vert.x还提供了很多好用的异步协调工具, e.g. 允许你将多个异步调用组合起来. 
    Netty将业务逻辑异步化时Netty所欠缺的组件
    不仅如此，在工程上面Vert.x还提供了"一站式"的工程解决方案.  
    如 Event Bus, 允许你将Verticle部署到多台服务器上(集群)，每个实例通过Event Bus通信
    断路器Vert.x Circuit Breaker等
    
4.	How do they do it
5.	Difficulties while using
RxJava	
1.	So far we have explored various areas of the Vert.x stack, using the callback-based APIs. 
It just works and this programming model is well-known from developers in many languages. 
However, it can become a bit tedious, especially when you combine several sources of events, or deal with complex data flows.
2.	

保证消息一致性

