1. What is boxing
    Java中基础数据类型与它们的包装类进行运算时，编译器会自动帮我们进行转换，转换过程对程序员是透明的，这就是装箱和拆箱，装箱和拆箱可以让我们的代码更简洁易懂
2. When
    进行 = 赋值操作（装箱或拆箱）
    进行+，-，*，/混合运算 （拆箱）
    进行>,<,==比较运算（拆箱）
    调用equals进行比较（装箱）
    ArrayList,HashMap等集合类 添加基础类型数据时（装箱）

装箱过程是通过调用包装器的valueOf方法实现的，而拆箱过程是通过调用包装器的 xxxValue方法实现的。

Questions:

1. 
```java
public class Main {
    public static void main(String[] args) {
         
        Integer i1 = 100;
        Integer i2 = 100;
        Integer i3 = 200;
        Integer i4 = 200;
         
        System.out.println(i1==i2); // true
        System.out.println(i3==i4); // false
    }
}
```

becasue 

```java
public static Integer valueOf(int i) {
        if(i >= -128 && i <= IntegerCache.high)
            return IntegerCache.cache[i + 128];
        else
            return new Integer(i);
    }
``` 
从这2段代码可以看出，在通过valueOf方法创建Integer对象的时候，如果数值在[-128,127]之间，便返回指向IntegerCache.cache中已经存在的对象的引用；否则创建一个新的Integer对象。

　　上面的代码中i1和i2的数值为100，因此会直接从cache中取已经存在的对象，所以i1和i2指向的是同一个对象，而i3和i4则是分别指向不同的对象。

注意，Integer、Short、Byte、Character、Long这几个类的valueOf方法的实现是类似的。

Double、Float的valueOf方法的实现是类似的。 (No Cache implemented yet. But may be optimization in the future)
    上述例子结果是false false

Boolean 结果是 true true
    它并没有创建对象，而是返回在内部已经提前创建好两个静态全局对象，因为它只有两种情况，这样也是为了避免重复创建太多的对象。

2. 谈谈Integer i = new Integer(xxx) 和Integer i =xxx; 这两种方式的区别。
   　　当然，这个题目属于比较宽泛类型的。但是要点一定要答上，我总结一下主要有以下这两点区别:
   　　1）第一种方式不会触发自动装箱的过程；而第二种方式会触发；
   　　2）在执行效率和资源占用上的区别。第二种方式的执行效率和资源占用在一般性情况下要优于第一种情况（注意这并不是绝对的）, 因为caching。
   
3. 当 "=="运算符的两个操作数都是包装器类型的引用，则是比较指向的是否是同一个对象，
    而如果其中有一个操作数是primitive/表达式（即包含算术运算）则比较的是数值（即会触发自动**拆箱**的过程）。

对于包装器类型，equals方法并不会进行类型转换
equals 它必须满足两个条件才为 true：
1） 类型相同
2） 内容相同
