# jvm:
1. jvm内存模型，
2. jvm问题工具,jps,jinfo,jmap...
JVM自动内存管理，Minor GC与Full GC的触发机制
https://juejin.im/post/6844903666432868365

了解过JVM调优没，基本思路是什么

# 多线程
1. wait,sleep分别是谁的方法，区别
Lock.wait(). Thread.sleep()??

2. countLatch的await方法是否安全，怎么改造
https://zhuanlan.zhihu.com/p/41459021

3. 线程池参数，整个流程描述

4. 背后的底层原理aqs，cas

5. ThreadLocal原理，注意事项，参数传递

6. 还有Java的锁，内置锁，显示锁，各种容器

7. 及锁优化：锁消除，锁粗化，锁偏向，轻量级锁
8. synchronized与lock的区别，使用场景。看过synchronized的源码没
https://juejin.im/post/6844903670933356551
9. volatile关键字的如何保证内存可见性
当把共享变量声明为 volatile 类型后，线程对该变量修改时会将该变量的值立即刷新回主内存，
同时会使其它线程中缓存的该变量无效，从而其它线程在读取该值时会从主内中重新读取该值（参考缓存一致性）。
因此在读取 volatile 类型的变量时总是会返回最新写入的值。
https://blog.csdn.net/t894690230/article/details/50588129

10. happen-before原则
11. 如何保证内存可见性

11.  哪些class是线程安全的，哪些不是。
说说JVM内存模型。
说说java线程池的工作流程？

说说堆里面的垃圾回收算法？为什么新生代用复制算法，老年代用标记整理、标记压缩？
了解CMS这个垃圾回收器吗？说说它的工作流程？
CMS在并发标记的时候，用户线程也会不停的产生一些大对象，Remark再次标记的时候可能会花上很多时间，说说你的优化方案？（我：？？？）


12. 进程的五种状态以及如何进行切换的？

https://blog.csdn.net/pange1991/article/details/53860651

1. 初始(NEW)：新创建了一个线程对象，但还没有调用start()方法。
2. 运行(RUNNABLE)：Java线程中将就绪（ready）和运行中（running）两种状态笼统的称为“运行”。
线程对象创建后，其他线程(比如main线程）调用了该对象的start()方法。该状态的线程位于可运行线程池中，等待被线程调度选中，获取CPU的使用权，此时处于就绪状态（ready）。就绪状态的线程在获得CPU时间片后变为运行中状态（running）。
3. 阻塞(BLOCKED)：表示线程阻塞于锁。
4. 等待(WAITING)：进入该状态的线程需要等待其他线程做出一些特定动作（通知或中断）。
5. 超时等待(TIMED_WAITING)：该状态不同于WAITING，它可以在指定的时间后自行返回。
6. 终止(TERMINATED)：表示该线程已经执行完毕。


https://zhuanlan.zhihu.com/p/143058286
https://blog.csdn.net/qwe6112071/article/details/70473905



13. Java多线程实现的几种方式，Runnable接口有哪些优势？
    1. 继承Thread类
    Thread类本质上是实现了Runnable接口的一个实例，代表一个线程的实例。启动线程的唯一方法就是通过Thread类的start()实例方法。start()方法是一个native方法，它将启动一个新线程，并执行run()方法
    
    2. 实现Runnable接口
    如果自己的类已经extends另一个类，就无法直接extends Thread，此时，可以实现一个Runnable接口
    ```java
    public class MyThread extends OtherClass implements Runnable {  
    　　public void run() {  
    　　 System.out.println("MyThread.run()");  
    　　}  
    }  
    //to start it 
    MyThread myThread = new MyThread();  
    Thread thread = new Thread(myThread);  
    thread.start();  
    ```
    3. 实现Callable接口通过FutureTask包装器来创建Thread线程
    ```java
    public interface Callable<V>   { 
      V call（） throws Exception;   } 

    
    Callable<V> oneCallable = new SomeCallable<V>();
       
    //由Callable<Integer>创建一个FutureTask<Integer>对象：   
    FutureTask<V> oneTask = new FutureTask<V>(oneCallable);  
     
    //注释：FutureTask<Integer>是一个包装器，它通过接受Callable<Integer>来创建，它同时实现了Future和Runnable接口。 
      //由FutureTask<Integer>创建一个Thread对象：   
    Thread oneThread = new Thread(oneTask);   
    oneThread.start();   
    //至此，一个线程就创建完成了。
    ```
    
    4. 使用ExecutorService、Callable、Future实现有返回结果的多线程。
    ExecutorService、Callable、Future三个接口实际上都是属于Executor框架。
    返回结果的线程是在JDK1.5中引入的新特征，有了这种特征就不需要再为了得到返回值而大费周折了。
    而且自己实现了也可能漏洞百出。
    
    **可返回值的任务必须实现Callable接口。类似的，无返回值的任务必须实现Runnable接口。**
    
    执行Callable任务后，可以获取一个Future的对象，在该对象上调用get就可以获取到Callable任务返回的Object了。
    **注意：get方法是阻塞的，即：线程无返回结果，get方法会一直等待。**
    
    再结合线程池接口ExecutorService就可以实现传说中有返回结果的多线程了。
    
    ```java
    public class Test {  
        public static void main(String[] args) throws ExecutionException,  
            InterruptedException {  
           System.out.println("----程序开始运行----");  
           Date date1 = new Date();  
          
           int taskSize = 5;  
           // 创建一个线程池  
           ExecutorService pool = Executors.newFixedThreadPool(taskSize);  
           // 创建多个有返回值的任务  
           List<Future> list = new ArrayList<Future>();  
           for (int i = 0; i < taskSize; i++) {  
            Callable c = new MyCallable(i + " ");  
            // 执行任务并获取Future对象  
            Future f = pool.submit(c);  
            // System.out.println(">>>" + f.get().toString());  
            list.add(f);  
           }  
           // 关闭线程池  
           pool.shutdown();  
          
           // 获取所有并发任务的运行结果  
           for (Future f : list) {  
            // 从Future对象上获取任务的返回值，并输出到控制台  
            System.out.println(">>>" + f.get().toString());  
           }  
          
           Date date2 = new Date();  
           System.out.println("----程序结束运行----，程序运行时间【"  
             + (date2.getTime() - date1.getTime()) + "毫秒】");  
        }  
    }  
    ```
    创建线程池的方法：
        public static ExecutorService newFixedThreadPool(int nThreads) 
        创建固定数目线程的线程池。
        public static ExecutorService newCachedThreadPool() 
        创建一个可缓存的线程池，调用execute 将重用以前构造的线程（如果线程可用）。如果现有线程没有可用的，则创建一个新线程并添加到池中。终止并从缓存中移除那些已有 60 秒钟未被使用的线程。
        public static ExecutorService newSingleThreadExecutor() 
        创建一个单线程化的Executor。
        public static ScheduledExecutorService newScheduledThreadPool(int corePoolSize) 
        创建一个支持定时及周期性的任务执行的线程池，多数情况下可用来替代Timer类。
    
ExecutoreService提供了submit()方法，传递一个Callable，或Runnable，返回Future。
如果Executor后台线程池还没有完成Callable的计算，这调用返回Future对象的get()方法，会阻塞直到计算完成。
14. 线程池的线程数怎么确定？如果是IO操作为主怎么确定？如果计算型操作又怎么确定？
    
    ```
    Number of threads = Number of Available Cores * (1 + Wait time / Service time)
    ```
    Waiting time - is the time spent waiting for IO bound tasks to complete, say waiting for HTTP response from remote service.
    
    (not only IO bound tasks, it could be time waiting to get monitor lock or time when thread is in WAITING/TIMED_WAITING state)
    
    Service time - is the time spent being busy, say processing the HTTP response, marshaling/unmarshaling, any other transformations etc.
    
    Wait time / Service time - this ratio is often called blocking coefficient.
    
    
    A computation-intensive task has a blocking coefficient close to 0, 
    in this case, the number of threads is equal to the number of available cores. 
    If all tasks are computation intensive, then this is all we need. 
    Having more threads will not help.


15. 手写一个[对象池](../../interview-coding/src/main/java/threadpool)?
 注意syncronized jobs， 加入job后notify。run的时候synchorized jobs，如果jobs是空的，wait & catch interruptedExceptions
https://www.jianshu.com/p/d5d7035b6f26
https://juejin.im/post/6844903757264732168


16. [sleep()，wait()，yield()和join](https://blog.csdn.net/xiangwanpeng/article/details/54972952)
    1. sleep
        sleep()使当前线程进入阻塞状态
        
        但不会释放“锁标志”
        sleep()既可以让其他同优先级或者高优先级的线程得到执行的机会，也可以让低优先级的线程得到执行机会。
    2. wait)_
        在其他线程调用对象的notify或notifyAll方法前，导致当前线程等待。
        wait()和notify()当前线程必须拥有当前对象锁。 必须在synchronized函数或synchronized block中进行调用
        如果当前线程不是此锁的拥有者，会抛出IllegalMonitorStateException异常。
        
        线程会释放掉它所占有的“锁标志”，从而使别的线程有机会抢占该锁。
        
        当调用某一对象的wait()方法后，会使当前线程暂停执行，并将当前线程放入对象等待池中，
        直到调用了notify()方法后，将从对象等待池中移出任意一个线程并放入锁标志等待池中，
        只有锁标志等待池中的线程可以获取锁标志，它们随时准备争夺锁的拥有权。
        
        to understand:
        但是如果使用的是ReenTrantLock实现同步，该如何达到这三个方法的效果呢？解决方法是使用ReenTrantLock.newCondition()获取一个Condition类对象，然后Condition的await()，signal()以及signalAll()分别对应上面的三个方法。
       
    3. yield
        yield()只是使当前线程重新回到可执行状态，所以执行yield()的线程有可能在进入到可执行状态后马上又被执行。
        yield()不会释放“锁标志”
        yield()只能使同优先级或更高优先级的线程有执行的机会
        
    4. join
        等待调用join方法的线程结束，再继续执行
        
18. reentrantLock
https://www.baeldung.com/java-concurrent-locks
```java
	public class SharedObject {
    //...
    ReentrantLock lock = new ReentrantLock();
    int counter = 0;
 
    public void perform() {
        lock.lock();
        try {
            // Critical section here
            count++;
        } finally {
            lock.unlock();
        }
    }
    //...
}

public void performTryLock(){
    //...
    boolean isLockAcquired = lock.tryLock(1, TimeUnit.SECONDS);
    
    if(isLockAcquired) {
        try {
            //Critical section here
        } finally {
            lock.unlock();
        }
    }
    //...
}

    Map<String,String> syncHashMap = new HashMap<>();
    ReadWriteLock lock = new ReentrantReadWriteLock();
    // ...
    Lock writeLock = lock.writeLock();
```


Condition
```java
ublic class ReentrantLockWithCondition {
 
    Stack<String> stack = new Stack<>();
    int CAPACITY = 5;
 
    ReentrantLock lock = new ReentrantLock();
    Condition stackEmptyCondition = lock.newCondition();
    Condition stackFullCondition = lock.newCondition();
 
    public void pushToStack(String item){
        try {
            lock.lock();
            while(stack.size() == CAPACITY) {
                stackFullCondition.await();
            }
            stack.push(item);
            stackEmptyCondition.signalAll();
        } finally {
            lock.unlock();
        }
    }
 
    public String popFromStack() {
        try {
            lock.lock();
            while(stack.size() == 0) {
                stackEmptyCondition.await();
            }
            return stack.pop();
        } finally {
            stackFullCondition.signalAll();
            lock.unlock();
        }
    }
}
```
    
17.电梯调度算法

答互斥锁、读写锁、自旋锁。