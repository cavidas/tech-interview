# Copy
1. 对象的深浅复制
    浅拷贝(Shallow Copy)、深拷贝(Deep Copy)、延迟拷贝(Lazy Copy)
   1. 浅拷贝:
    * What: 如果属性是基本类型，拷贝的就是基本类型的值；
         如果属性是内存地址（引用类型），拷贝的就是内存地址 ，因此如果其中一个对象改变了这个地址，就会影响到另一个对象。
    * How: 让要拷贝的类Student实现了Clonable接口并重写Object类的clone()方法，然后在方法内部调用super.clone()方法。
    * Why:
        为什么浅拷贝不会拷贝其引用的对象？
        1、不给其他类强加意义
        这个就好比，User类为了能进行浅拷贝就实现了Cloneable 接口，但是其引用对象Teacher没有实现Cloneable。
        也许说明他本身就不想被拷贝，如果在拷贝User的情况下，同时也把Teacher拷贝了，这不就等于干了一件没有遵循他人同意的事，
        干了之后人家还不知道，傻傻的以为没人可以通过clone来拷贝出另外一个Teacher。
        
        2、不破坏其原来对象的代码逻辑
        如果User引用的Teacher 是个单例模式的对象，那如果在User拷贝的时候同时也拷贝出了一个Teacher 
        那是不是就会破坏Teacher这个单例模式对象的逻辑初衷。
   2. 深拷贝
    * What: 深拷贝会拷贝所有的属性,并拷贝属性指向的动态分配的内存。当对象和它所引用的对象一起拷贝时即发生深拷贝。
        深拷贝相比于浅拷贝速度较慢并且花销较大。
    * How: 很容易发现clone()方法中的一点变化。因为它是深拷贝，所以你需要创建拷贝类的一个对象。
        因为在Student类中有对象引用，所以需要在Student类中实现Cloneable接口并且重写clone方法。
   3. 延迟拷贝
    * What: 是浅拷贝和深拷贝的一个组合，实际上很少会使用。
        当最开始拷贝一个对象时，会使用速度较快的浅拷贝，还会使用一个计数器来记录有多少对象共享这个数据。
        当程序想要修改原始的对象时，它会决定数据是否被共享（通过检查计数器）并根据需要进行深拷贝。
        延迟拷贝从外面看起来就是深拷贝，但是只要有可能它就会利用浅拷贝的速度。
        当原始对象中的引用不经常改变的时候可以使用延迟拷贝。
        由于存在计数器，效率下降很高，但只是常量级的开销。而且, 在某些情况下, 循环引用会导致一些问题。
   4. 如何选择拷贝方式
      * 如果对象的属性全是基本类型的，那么可以使用浅拷贝。
      * 如果对象有引用属性，那就要基于具体的需求来选择浅拷贝还是深拷贝。
      * 意思是如果对象引用任何时候都不会被改变，那么没必要使用深拷贝，只需要使用浅拷贝就行了。
      * 如果对象引用经常改变，那么就要使用深拷贝。没有一成不变的规则，一切都取决于具体需求。
   5. 序列化进行拷贝
    * What: 通过序列化来实现深拷贝
         可以序列化是干什么的?它将整个对象图写入到一个持久化存储文件中并且当需要的时候把它读取回来, 
         这意味着当你需要把它读取回来时你需要整个对象图的一个拷贝。
    * 当你通过序列化进行深拷贝时，必须确保对象图中所有类都是可序列化的。 
    * How: 只需要实现Serializable这个接口。Android中还可以实现Parcelable接口。
        确保对象图中的所有类都是可序列化的
        创建输入输出流
        使用这个输入输出流来创建对象输入和对象输出流
        将你想要拷贝的对象传递给对象输出流
        从对象输入流中读取新的对象并且转换回你所发送的对象的类
    * Limitation:
        * 因为无法序列化transient变量, 使用这种方法将无法拷贝transient变量。
        * 再就是性能问题。创建一个socket, 序列化一个对象, 通过socket传输它, 然后反序列化它，
        这个过程与调用已有对象的方法相比是很慢的。
    
   6. 数组的拷贝
    数组除了默认实现了clone()方法之外，还提供了Arrays.copyOf方法用于拷贝，这两者都是浅拷贝。
   7. 集合的拷贝
    一般情况下，我们都是用浅拷贝来实现，即通过构造函数或者clone方法。
    Arrays.asList is a shallow copy
    To be verified: New ArrayList<T>(list) would create a new array list, but the element inside would be shallow copy
    深拷贝：
```java
public static List<Dog> cloneList(List<Dog> list) {
    List<Dog> clone = new ArrayList<Dog>(list.size());
    for (Dog item : list) clone.add(item.clone());
    return clone;
}
// dog class should override the clone method to be deep copy
```
    
    