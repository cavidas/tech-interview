# String 
1. String,StringBuilder,StringBuffer
   1. Overall commons and difference
    * 都是final类，都不允许被继承；
    * String类长度是不可变的，StringBuffer和StringBuilder类长度是可以改变的；
    * StringBuffer类是线程安全的，StringBuilder不是线程安全的；
   2. String 和 StringBuffer：
      1. String类型和StringBuffer类型的主要性能区别：
        String是不可变的对象，因此每次在对String类进行改变的时候都会生成一个新的string对象，
        然后将指针指向新的string对象，所以经常要改变字符串长度的话不要使用string,
        因为每次生成对象都会对系统性能产生影响，特别是当内存中引用的对象多了以后，JVM的GC就会开始工作，性能就会降低；
      
      2. 使用StringBuffer类时，每次都会对StringBuffer对象本身进行操作，而不是生成新的对象并改变对象引用，
      所以多数情况下推荐使用StringBuffer，特别是字符串对象经常要改变的情况；

      3. 在某些情况下，String对象的字符串拼接其实是被Java Compiler编译成了StringBuffer对象的拼接，
      所以这些时候String对象的速度并不会比StringBuffer对象慢
      
    3. StringBuilder
    5.0新增的，此类提供一个与 StringBuffer 兼容的 API，但不保证同步。
    用作 StringBuffer 的一个简易替换，用在字符串缓冲区被单个线程使用的时候（这种情况很普遍），比 StringBuffer 要快。两者的方法基本相同
    
    4. 使用策略
       1.基本原则：
           如果要操作少量的数据，用String ；
           单线程操作大量数据，用StringBuilder ；
           多线程操作大量数据，用StringBuffer。 
       2.不要使用String类的”+”来进行频繁的拼接，因为那样的性能极差的，应该使用StringBuffer或StringBuilder类，
       这在Java的优化上是一条比较重要的原则
       3. StringBuilder一般使用在方法内部来完成类似”+”功能，因为是线程不安全的，所以用完以后可以丢弃。
       StringBuffer主要用在全局变量中
       
       4. 相同情况下使用 StirngBuilder 相比使用 StringBuffer 仅能获得 10%~15% 左右的性能提升，但却要冒多线程不安全的风险。
       而在现实的模块化编程中，负责某一模块的程序员不一定能清晰地判断该模块是否会放入多线程的环境中运行，
       因此：除非确定系统的瓶颈是在 StringBuffer 上，并且确定你的模块不会运行在多线程模式下，才可以采用StringBuilder；否则还是用StringBuffer
2. String 为何immutable
https://juejin.im/post/6844903501630275597
    1. String 类是 final 修饰的，满足第二条原则：保证类不会被扩展。
    2. private final char value[] : 可以看到 Java 还是使用字节数组来实现字符串的，并且用 final 修饰，保证其不可变性。这就是为什么 String 实例不可变的原因。
    3. 正如第三条 使所有的域都是 final 的。 和第四条 使所有的域都成为私有的 所描述的。
    4. 不要提供任何会修改对象状态的方法, 在 String 中有许多对字符串进行操作的函数，例如 substring concat replace replaceAll, 都没有直接修改对象
3. String 对象在内存中的位置
    我们都知道字符串常量池的概念，JVM 为了字符串的复用，减少字符串对象的重复创建，特别维护了一个字符串常量池。
    ```java
    String str1 = "123";
    String str2 = new String("123");
    ```
    第一种字面量形式的写法, 会直接在字符串常量池中查找是否存在值 123，若存在直接返回这个值的引用，
    若不存在创建一个值为 123 的 String 对象并存入字符串常量池中。
    而使用 new 关键字，则会直接在堆上生成一个新的 String 对象，并不会理会常量池中是否有这个值。  
    
    intern() 用来将这个对象加入字符串常量池.
    
    常量池是方法区的一部分，用于存放编译期生成的各种字面量和符号引用，运行期间也有可能将新的常量放入池中。
    在 Java 虚拟机规范中把方法区描述为堆的一个逻辑部分，但它却有一个别名叫 Non-Heap，目的应该是为了和 Java 堆区分开。
    
    * 好处
    不可变类比较简单。
    不可变对象本质上是线程安全的，它们不要求同步。不可变对象可以被自由地共享。
    不仅可以共享不可变对象，甚至可以共享它们的内部信息。
    不可变对象为其他对象提供了大量的构建。
    不可变类真正唯一的缺点是，对于每个不同的值都需要一个单独的对象。
4. String类中的hashCode计算方法还是比较简单的，
   就是以31为权，每一位为字符的ASCII值进行运算，用自然溢出来等效取模。
   哈希计算公式可以计为s[0]*31^(n-1) + s[1]*31^(n-2) + ... + s[n-1] 
   
   那为什么以31为质数呢?
   主要是因为31是一个奇质数，所以31*i=32*i-i=(i<<5)-i，这种位移与减法结合的计算相比一般的运算快很多。
   
String为啥不可变，在内存中的具体形态。
String：字符串常量，字符串长度不可变。
StringBuffer：字符串变量（Synchronized，即线程安全）。如果要频繁对字符串内容进行修改，出于效率考虑最好使用StringBuffer，如果想转成String类型，可以调用StringBuffer的toString()方法。
StringBuilder：字符串变量（非线程安全）。在内部，StringBuilder对象被当作是一个包含字符序列的变长数组。
StringBuilder与StringBuffer有公共父类AbstractStringBuilder(抽象类)。       