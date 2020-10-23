https://netflixtechblog.com/reactive-programming-in-the-netflix-api-with-rxjava-7811c3a1496a

https://mp.weixin.qq.com/s/01SQuSYkNSHQz-0cbxzh_g

Reactive 和 Reactive programming

Reactive 直接翻译的意思是反应式，反应性。咋一看，似乎不太好懂。

举个例子：在 Excel 里，C 单元格上设置函数 Sum(A+B)，当你改变单元格 A 或者单元格 B 的数值时，单元格 C 的值同时也会发生变化。这种行为就是 Reactive。

在计算机编程领域，Reactive 一般指的是 Reactive programming。指的是一种面向数据流并传播事件的异步编程范式（asynchronous programming paradigm）。

先举个例子大家感受一下：

public static void main(String[] args) {
  FluxProcessor<Integer, Integer> publisher = UnicastProcessor.create();
  publisher.doOnNext(event -> System.out.println("receive event: " + event)).subscribe();

  publisher.onNext(1); // print 'receive event: 1'
  publisher.onNext(2); // print 'receive event: 2'
}
代码 1

以上例代码（使用 Reactor 类库）为例，publisher 产生了数据流 (1,2)，并且传播给了 OnNext 事件, 上例中 lambda 响应了该事件，输出了相应的信息。上例代码中生成数据流和注册/执行 lambda 是在同一线程中，但也可以在不同线程中。

注：如果上述代码执行逻辑有些疑惑，可以暂时将 lambda 理解成 callback 就可以了。

Reactive Manifesto

对于 Reactive 现在你应该大致有一点感觉了，但是 Reactive 有什么价值，有哪些设计原则，估计你还是有些模糊。这就是 Reactive Manifesto 要解决的疑问了。

使用 Reactive 方式构建的系统具有以下特征：

即时响应性 (Responsive)

只要有可能， 系统就会及时地做出响应。即时响应是可用性和实用性的基石， 而更加重要的是，即时响应意味着可以快速地检测到问题并且有效地对其进行处理。即时响应的系统专注于提供快速而一致的响应时间， 确立可靠的反馈上限， 以提供一致的服务质量。这种一致的行为转而将简化错误处理、 建立最终用户的信任并促使用户与系统作进一步的互动。

回弹性 (Resilient)

系统在出现失败时依然保持即时响应性。这不仅适用于高可用的、 任务关键型系统——任何不具备回弹性的系统都将会在发生失败之后丢失即时响应性。回弹性是通过复制、 遏制、 隔离以及委托来实现的。失败的扩散被遏制在了每个组件内部， 与其他组件相互隔离， 从而确保系统某部分的失败不会危及整个系统，并能独立恢复。每个组件的恢复都被委托给了另一个（外部的）组件， 此外，在必要时可以通过复制来保证高可用性。（因此）组件的客户端不再承担组件失败的处理。

弹性 (Elastic)

系统在不断变化的工作负载之下依然保持即时响应性。反应式系统可以对输入（负载）的速率变化做出反应，比如通过增加或者减少被分配用于服务这些输入（负载）的资源。这意味着设计上并没有争用点和中央瓶颈， 得以进行组件的分片或者复制， 并在它们之间分布输入（负载）。通过提供相关的实时性能指标， 反应式系统能支持预测式以及反应式的伸缩算法。这些系统可以在常规的硬件以及软件平台上实现成本高效的弹性。

消息驱动 (Message Driven)

反应式系统依赖异步的消息传递，从而确保了松耦合、隔离、位置透明的组件之间有着明确边界。这一边界还提供了将失败作为消息委托出去的手段。使用显式的消息传递，可以通过在系统中塑造并监视消息流队列， 并在必要时应用回压， 从而实现负载管理、 弹性以及流量控制。使用位置透明的消息传递作为通信的手段， 使得跨集群或者在单个主机中使用相同的结构成分和语义来管理失败成为了可能。非阻塞的通信使得接收者可以只在活动时才消耗资源， 从而减少系统开销。



注：
上面描述有很多专有名词，可能有些疑惑，可以看下相关名词解释。

为什么使用 Reactive 方式构建的系统会具有以上价值, 我稍后在 Reactor 章节中介绍。


Reactive Stream

知道了 Reactive 的概念，特征和价值后，是否有相关的产品或者框架来帮助我们构建 Reactive 式系统呢？在早些时候有一些类库 (Rxjava 1.x, Rx.Net) 可以使用，但是规范并不统一，所以后来 Netfilx, Pivotal 等公司就制定了一套规范指导大家便于实现它（该规范也是受到早期产品的启发），这就是 Reactive Stream 的作用。

Reactive Stream 是一个使用非阻塞 back pressure（回压）实现异步流式数据处理的标准。目前已经在 JVM 和 JavaScript 语言中实现同一套语意的规范；以及尝试在各种涉及到序列化和反序列化的传输协议（TCP, UDP, HTTP and WebSockets）基础上，定义传输 reactive 数据流的网络协议。

The purpose of Reactive Streams is to provide a standard for asynchronous stream processing with non-blocking backpressure.


Reactive Streams 解决的问题场景

当遇到未预料数据流时，依然可以在可控资源消耗下保持系统的可用性。

Reactive Streams 的目标

控制在一个异步边界的流式数据交换。例如传递一个数据到另外一个线程或者线程池，确保接收方没有 buffer（缓存）任意数量的数据。而 back pressure（回压）是解决这种场景的不可或缺的特性。

Reactive Streams 规范适用范围

此标准只描述通过回压来实现异步流式数据交换的必要的行为和实体，最小接口，例如下方的 Publisher, Subscriber。Reactive Streams 只关注在这些组件之间的流式数据中转，并不关注流式数据本身的组装，分割，转换等行为, 例如 map, zip 等 operator。Reactive Streams 规范包括：

Publisher



产生一个数据流（可能包含无限数据）, Subscriber 们可以根据它们的需要消费这些数据。


public interface Publisher<T> {    
  public void subscribe(Subscriber<? super T> s);
}

Subscriber



Publisher 创建的元素的接收者。监听指定的事件，例如 OnNext, OnComplete, OnError 等。


publicinterface Subscriber<T> {
  public void onSubscribe(Subscription s);
  public void onNext(T t);
  public void onError(Throwable t);
  public void onComplete();
}

Subscription



是 Publisher 和 Subscriber 一对一的协调对象。Subscriber 可以通过它来向 Publisher 取消数据发送或者 request 更多数据。


public interface Subscription {  
  public void request(long n);  
  public void cancel();
}

Processor



同时具备 Publisher 和 Subscriber 特征。代码1中 FluxProcessor 既可以发送数据(OnNext)，也可以接收数据 (doOnNext)。


public interface Processor<T, R> extends Subscriber<T>, Publisher<R> {}

为什么规范强调使用非阻塞异步方式而不是阻塞同步方式?

同步方式一般通过多线程来提高性能，但系统可创建的线程数是有限的，且线程多以后造成线程切换开销。


同步方式很难进一步提升资源利用率。


同步调用依赖的系统出现问题时，自身稳定性也会受到影响。


实现非阻塞的方式有很多种，为什么规范会选择上述的实现方式呢?

Thread

thread 不是非常轻量（相比下面几种实现方案）。

thread 数量是有限的，最终可能会成为主要瓶颈。

有一些平台可能不支持多线程。例如：JavaScript。

调试，实现上有一定复杂性。


Callback

多层嵌套 callback 比较复杂，容易形成"圣诞树" (callback hell)。

错误处理比较复杂。

多用于 event loop 架构的语言中，例如：JavaScript。


Future

无法逻辑组合各种行为，支持业务场景有限。

错误处理依然复杂。


Reactive Extensions (Rx)

和 Future 很相似。Future 可以认为返回一个独立的元素，而 Rx 返回一个可以被订阅的 Stream。

多平台支持同一套规范。

同一套 API 同时支持异步、同步。

错误处理方便。


Coroutines


why rxjava?

我们一般写的程序 统称为命令式程序，是以流程为核心的，每一行代码实际上都是机器实际上要执行的指令。而Rxjava这样的编程风格，称为函数响应式编程。函数响应式编程是以数据流为核心，处理数据的输入，处理以及输出的。这种思路写出来的代码就会跟机器实际执行的指令大相径庭。

作者：小7
链接：https://www.jianshu.com/p/c165e0c14d4f
来源：简书
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。

kotlin coroutine 和 goroutine 在语法层面上提供异步支持, 而且比Rx更简洁，但无法跨多个语言平台形成统一的规范。


Reactive 的实现原理个人认为还是回调，kotlin 协程实现原理同样也是回调。但实现回掉的方式不一样。一个是通过事件传播, 一个是通过状态机。但 coroutine 编程的易用性明显强于 Rx，后面有空我会专门写篇文章介绍 kotlin coroutine 的实现原理。

Reactor

有了 Reactive Stream 这个规范，就会有相应实现该规范的类库。Reactor 就是其中之一。

Reactor 是遵守 Reactive Stream 规范构建非阻塞应用的 Java 语言 Reactive 类库，已经在 spring 5 中集成，与他相似的类库有 RxJava2, RxJs, JDK9 Flow 等。

阿里内部的 Faas 系统目前使用 Reactor 来构建整个系统，包括函数应用和各种核心应用(逻辑架构)。根据我们压测结果显示，使用 Reactive 方式构建的系统确实会有这些特点：

回弹性 (Resilient)：当函数出现严重超时时 (rt >= 10s)，函数上游的 broker, gateway 应用几乎无任何影响。


及时响应性：不管是高并发场景（资源足够），还是正常场景，RT 表现一致。


另外从原理上，我认为资源利用率和吞吐量也会高于非反应式的应用。

为什么 Reactive 的架构系统有这些特点?

阿里内部的 Faas 系统主要做了两件事情：

涉及到 IO 的地方几乎全异步化。例如中间件（HSF, MetaQ 等提供异步 API）调用。

IO 线程模型变化。使用较少（一般 CPU 核数）线程处理所有的请求。


传统 Java 应用 IO 线程模型

参考 Netty 中 Reactor IO (worker thread pool) 模型，下方伪代码（kotlin）进行了简化。



// 非阻塞读取客户端请求数据(in), 读取成功后执行lambda.
inChannel.read(in) {
    workerThreadPool.execute{
      // 阻塞处理业务逻辑(process), 业务逻辑在worker线程池中执行，同步执行完后，再向客户端返回输出(out)
      val out = process(in)
      outChannel.write(out)
    }  
}

Reactive 应用 IO 线程模型

IO 线程也可以执行业务逻辑 (process)，可以不需要 worker 线程池。


// 非阻塞读取客户端请求数据(in), 读取成功后执行lambda
inChannel.read(in) {
    // IO线程执行业务逻辑(process),  然后向客户端返回输出(out). 这要求业务处理流程必须是非阻塞的.
    process(in){ out->
        outChannel.write(out) {
            // this lambda is executed when the writing completes
        ...
        }
    }
}

如何开始 Reactive Programing

以 Reactive 方式构建的系统有很多值得学习和发挥价值的地方，但坦白讲 Reactive programing 方式目前接受程度并不高。特别是使用 Java 语言开发同学，我个人也感同身受，因为这和 Java 面向命令控制流程的编程思维方式有较大差异。所以这里以 Reactor (Java) 学习为例：

Reactor 基础文档

Reactive Streams 规范文档

Operator


总结

反应式的系统有很多优点，但是完整构建反应式的系统却并不容易。不仅仅是语言上的差异，还有一些组件就不支持非阻塞式的调用方式，例如：JDBC。但是有一些开源组织正在推动这些技术进行革新，例如：R2DBC。另外，为了方便构建反应式系统，一些组织/个人适配了一些主流技术组件 reactor-core, reactor-netty, reactor-rabbimq, reactor-kafka 等，来方便完整构建反应式系统。

当你的系统从底层到上层，从系统内部到依赖外部都变成了反应式，这就形成了 Reactive 架构。

这种架构价值有多大？未来可期。

参考



https://www.reactivemanifesto.org/

https://www.reactive-streams.org/

https://kotlinlang.org/docs/tutorials/coroutines/async-programming.html

https://projectreactor.io/docs/core/release/reference/index.html


To read:
https://www.veskoiliev.com/40-rxjava-interview-questions-and-answers/

Cold & hot observables

Observables are functions that tie an observer to a producer. 

Cold Observables: Producers created *inside*
Hot Observables: Producers created *outside*

A Cold Observable is one which starts emitting upon request(subscription), whereas a Hot Observable is one that emits regardless of subscriptions.
https://alligator.io/rxjs/hot-cold-observables/

To read:
https://awesomeopensource.com/project/xiaomeixw/NoRxJava?categoryPage=9