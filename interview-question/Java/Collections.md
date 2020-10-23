Java基础：

# Collections
## Map
To read: 
https://www.processon.com/view/link/5c126926e4b0ed122da58975
very good articles：
https://zhuanlan.zhihu.com/p/76735726
1. 你都知道哪些常用的Map集合?
HashMap、HashTable、LinkedHashMap、ConcurrentHashMap、SynchronizedMap
2. Collection集合接口和Map接口有什么关系？
   没关系，Collection是List、Set父接口不是Map父接口。
   
3. HashMap是线程安全的吗？线程安全的Map都有哪些？性能最好的是哪个？
    HashMap不是线程安全的。
    线程安全的有HashTable、ConcurrentHashMap、SynchronizedMap，性能最好的是ConcurrentHashMap。

4. 使用HashMap有什么性能问题吗？
    使用HashMap要注意避免集合的扩容，它会很耗性能，根据元素的数量给它一个初始大小的值。

5. HashMap的数据结构是怎样的？默认大小是多少？内部是怎么扩容的？
    HashMap是数组和链表组成的，默认大小为16，当hashmap中的元素个数超过数组大小*loadFactor（默认值为0.75）
    时就会把数组的大小扩展为原来的两倍大小，然后重新计算每个元素在数组中的位置。比较费时间  
    ```java
        /**
         * The table, initialized on first use, and resized as
         * necessary. When allocated, length is always a power of two.
         * (We also tolerate length zero in some operations to allow
         * bootstrapping mechanics that are currently not needed.)
         */
        transient Node<K,V>[] table;
    
        static final class TreeNode<K,V> extends LinkedHashMap.Entry<K,V> {
            TreeNode<K,V> parent;  // red-black tree links
            TreeNode<K,V> left;
            TreeNode<K,V> right;
            TreeNode<K,V> prev;    // needed to unlink next upon deletion
    ```
    
6. 怎么按添加顺序存储元素？怎么按A-Z自然顺序存储元素？怎么自定义排序？
    按添加顺序使用LinkedHashMap,
    按自然顺序使用TreeMap,
    自定义排序TreeMap(Comparetor c)。

7. HashMap的链表结构设计是用来解决什么问题的？
    HashMap的链表结构设计是用来解决key的hash冲突问题的。

8. HashMap的键、值可以为NULL吗？HashTable呢？
    HashMap的键值都可以为NULL，HashTable不行。
    
    in HashMap
    ```java
            static final int hash(Object key) {
                int h;
                return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
            }
    ```
    
    in hastable
        
    ```java
       
            * @throws NullPointerException if the specified key is null
            * @see     #put(Object, Object)
            */
           @SuppressWarnings("unchecked")
           public synchronized V get(Object key) {
               Entry<?,?> tab[] = table;
               int hash = key.hashCode();
               int index = (hash & 0x7FFFFFFF) % tab.length;
               for (Entry<?,?> e = tab[index] ; e != null ; e = e.next) {
                   if ((e.hash == hash) && e.key.equals(key)) {
                       return (V)e.value;
                   }
               }
               return null;
           }
    
    ```

9. HashMap使用对象作为key，如果hashcode相同会怎么处理？
    key的hash冲突，如果key equals一致将会覆盖值，不一致就会将值存储在key对应的链表中。
    1. hashmap结构；什么对象能做为key
        have valid hascode() and equal() implemented
        因为get的时候会调用hashcode， 之后再调用好equal来具体判断是否存在该key
        
10. HashMap中的get操作是什么原理？
    先根据key的hashcode值找到对应的链表，再循环链表，根据key的hash是否相同且key的==或者equals比较操作找到对应的值。

12. Concurrent hashmap怎么实现的， 
    * 底层采用分段的数组+链表实现，线程安全
    * 通过把整个Map分为N个Segment，可以提供相同的线程安全，但是效率提升N倍，默认提升16倍。
    (读操作不加锁，由于HashEntry的value变量是 volatile的，也能保证读取到最新的值。)
    Hashtable的synchronized是针对整张Hash表的，即每次锁住整张表让线程独占，
    ConcurrentHashMap允许多个修改操作并发进行，其关键在于使用了锁分离技术
    扩容：段内扩容（段内元素超过该段对应Entry数组长度的75%触发扩容，不会对整个Map进行扩容），插入前检测需不需要扩容，有效避免无效扩容
    
    * [ConcurrentHashMap Java 1.7 vs 1.8](https://juejin.im/post/6844903813892014087)       
        * Lazy init， init 通过sizectl变量来判断是否在初始化。
            这个方法首先判断sizeCtl是否小于0，如果小于0，直接将当前线程变为就绪状态的线程。
            当sizeCtl大于等于0时，当前线程会尝试通过CAS的方式将sizeCtl的值修改为-1。
            在new Node[]之前，要再检查一遍table是否为空，这里做双重检查的原因在于，如果另一个线程执行完#1代码后挂起，此时另一个初始化的线程执行完了#6的代码，此时sizeCtl是一个大于0的值，那么再切回这个线程执行的时候，是有可能重复初始化的。关于这个问题会在下图的并发场景中说明。
            然后初始化hash表，并重新计算sizeCtl的值，最终返回初始化好的hash表。
        * put：
            不断循环，
            * 当前hash表对应当前key的index上没有元素时
            如果获取的是空，尝试用cas的方式在数组的指定index上创建一个新的Node。
            * 之后确定没有在扩容，确保put没有发生在扩容的过程中，fh=-1时表示正在扩容，如果检测到桶结点是 ForwardingNode 类型，协助扩容。
            * 当前hash表对应当前key的index上已经存在元素时(hash碰撞)
                也就是说，当发生hash碰撞时，会以链表的头结点作为锁
                之后判断指定index上的Node仍然是该节点，这里就是为了确保在put的过程中，没有收到rehash的影响。
                之后在末尾追加元素，或插入树，
            * 之后判断是否treeify            

        * 扩容
           put方法的最后一步是统计hash表中元素的个数，如果超过sizeCtl的值，触发扩容。
           调用核心方法transfer
           首先new一个新的hash表(nextTable)出来，大小是原来的2倍。后面的rehash都是针对这个新的hash表操作，不涉及原hash表(table)。
           每个新参加进来扩容的线程必然先进 while 循环的最后一个判断条件中去领取自己需要迁移的桶的区间
           如果某个桶结点中所有节点都已经迁移完成了（已经被转移到新表 nextTable 中了），
           那么会在原 table 表的该位置挂上一个ForwardingNode 结点，说明此桶已经完成迁移。
           如果遇到桶的头结点是空的，那么使用 ForwardingNode 标识该桶已经被处理完成了。如果遇到已经处理完成的桶，直接跳过进行下一个桶的处理。
           如果是正常的桶，对桶首节点加锁，正常的迁移即可，迁移结束后依然会将原表的该位置标识位已经处理。
           然后会对原hash表(table)中的每个链表进行rehash，此时会尝试获取头节点的锁。这一步就保证了在rehash的过程中不能对这个链表执行put操作。  
           通过sizeCtl控制，使扩容过程中不会new出多个新hash表来。
           最后，将所有键值对重新rehash到新表(nextTable)中后，用nextTable将table替换。这就避免了HashMap中get和扩容并发时，可能get到null的问题。
           在整个过程中，共享变量的存储和读取全部通过volatile或CAS的方式，保证了线程安全
        * 元素数量
            用CAS的方法来更新basecount，高并发更新失败的时候会调用fullAddCount。将这些失败的结点包装成一个 CounterCell 对象，保存在 CounterCell 数组中。
            那么整张表实际的 size 其实是 baseCount 加上 CounterCell 数组中元素的个数。
    to do: https://www.cnblogs.com/yangming1996/p/8031199.html
    http://www.jasongj.com/java/concurrenthashmap/
    https://www.cnblogs.com/study-everyday/p/6430462.html
    
13. [hashmap 为啥不是线程安全的？](https://blog.csdn.net/mydreamongo/article/details/8960667#:~:text=hashMap%E5%87%BA%E7%8E%B0%E7%BA%BF%E7%A8%8B%E4%B8%8D%E5%AE%89%E5%85%A8,%E6%AC%A1%E6%A3%80%E6%9F%A5%E3%80%81%E5%8A%A0...)
    1. 内部数据实现
    2. 基本操作的举个例子
        1. put
            ```java
                void addEntry(int hash, K key, V value, int bucketIndex) {
                    Entry<K,V> e = table[bucketIndex];
                        table[bucketIndex] = new Entry<K,V>(hash, key, value, e);
                        if (size++ >= threshold)
                            resize(2 * table.length);
                    }
            ```    
            在hashmap做put操作的时候会调用到以上的方法。现在假如A线程和B线程同时对同一个数组位置调用addEntry，
            两个线程会同时得到现在的头结点，然后A写入新的头结点之后，B也写入新的头结点，
            那B的写入操作就会覆盖A的写入操作造成A的写入操作丢失
       2. remove
            ```java

            ``` 

14. hashtable,concurrentHashMap,hashtable比较
    https://www.cnblogs.com/heyonggang/p/9112731.html
    https://yuanrengu.com/2020/ba184259.html
    https://blog.csdn.net/SEU_Calvin/article/details/52653711
    hastable 迭代器强一致性，concurrentHashMap弱一致性
    https://my.oschina.net/hosee/blog/675423

15. LinkedHashMap：了解基本原理、哪两种有序、如何用它实现LRU
Hash table and linked list implementation of the Map interface, with predictable iteration order. 
This implementation differs from HashMap in that it maintains a doubly-linked list running through all of its entries. 
This linked list defines the iteration ordering, which is normally the order in which keys were inserted into the map (insertion-order).
每个node还会有before、after的feild 相当于维护了一个double-linkedlist

    * hashmap中有几个post-actions
    ```java
        // Callbacks to allow LinkedHashMap post-actions
        void afterNodeAccess(Node<K,V> p) { }
        void afterNodeInsertion(boolean evict) { }
        void afterNodeRemoval(Node<K,V> p) { }
    ```
    LinkedHashMap继承于HashMap，因此也重新实现了这3个函数，顾名思义这三个函数的作用分别是：节点访问后、节点插入后、节点移除后做一些事情。

    * put & get 没有重写
    值得注意的是，在accessOrder模式下，只要执行get或者put等操作的时候，就会产生structural modification。官方文档是这么描述的：
    ```text
    A structural modification is any operation that adds or deletes one or more mappings or, 
    in the case of access-ordered linked hash maps, affects iteration order. 
    In insertion-ordered linked hash maps, 
    merely changing the value associated with a key that is already contained in the map is not a structural modification. 
    In access-ordered linked hash maps, merely querying the map with get is a structural modification.
    ```
    TODO: more on resizing https://www.jianshu.com/p/8f4f58b4b8ab\

15. TreeMap：了解数据结构、了解其key对象为什么必须要实现Compare接口、如何用它实现一致性哈希

16. jdk1.8之前并发操作hashmap时为什么会有死循环的问题？
    a->b->c
    -->
    b->a
ab would be dead loop

17. hashmap的数组长度为什么要保证是2的幂？hashmap扩容时每个entry需要再计算一次hash吗？链接：https://www.jianshu.com/p/e2f75c8cce01
    h&(length-1)
h是key的hashCode值，计算好hashCode之后，使用上面的方法来对桶的数量取模，将这个数据记录落到某一个桶里面。
当然取模是java7中的做法，java8进行了优化，做得更加巧妙，因为我们的length总是2的n次幂，
所以在一次resize之后，当前位置的记录要么保持当前位置不变，要么就向前移动length就可以了。
所以java8中的HashMap的resize不需要重新计算hashCode。

18. [put 方法和resize](https://upload-images.jianshu.io/upload_images/7853175-a8950349acda7799.png?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp)
    * 先插入，再resize
    * 先转换红黑树再插入
    * resize
        HashMap的扩容机制就是重新申请一个容量是当前的2倍的桶数组，然后将原先的记录逐个重新映射到新的桶里面，然后将原先的桶逐个置为null使得引用失效。
        HashMap之所以线程不安全，就是resize这里出的问题。
            1. put的时候导致的多线程数据不一致。
                这个问题比较好想象，比如有两个线程A和B，首先A希望插入一个key-value对到HashMap中，
                首先计算记录所要落到的桶的索引坐标，然后获取到该桶里面的链表头结点，此时线程A的时间片用完了，
                而此时线程B被调度得以执行，和线程A一样执行，只不过线程B成功将记录插到了桶里面，
                假设线程A插入的记录计算出来的桶索引和线程B要插入的记录计算出来的桶索引是一样的，
                那么当线程B成功插入之后，线程A再次被调度运行时，它依然持有过期的链表头但是它对此一无所知，
                以至于它认为它应该这样做，如此一来就覆盖了线程B插入的记录，这样线程B插入的记录就凭空消失了，
                造成了数据不一致的行为。
            2. 另外一个比较明显的线程不安全的问题是HashMap的get操作可能因为resize而引起死循环（cpu100%），具体分析如下：
               https://www.jianshu.com/p/e2f75c8cce01

14. 扩容时机、扩容时避免rehash的优化
    https://www.jianshu.com/p/3797c6f83d4f
    下标的计算方法，对于一个 Key，如原Hash值 key1 = 0001 1001 key2 = 0000 1001
    扩容前 hash & (length - 1) :
    key1 : 0001 1001 & 0000 1111 -> 0000 1001
    key2 : 0000 1001 & 0000 1111 -> 0000 1001
    
    扩容后 hash & (length - 1) :
    key1 : 0001 1001 & 0001 1111 -> 0001 1001
    key2 : 0000 1001 & 0001 1111 -> 0000 1001
    
    因此，我们在扩充HashMap的时候，不需要像JDK1.7的实现那样重新计算hash，
    **只需要看原来的hash值在扩容后新增的那一位是1还是0，如果是0的话索引没变，是1的话索引变成“原索引+oldCap” 。
    
    在JAVA8代码中，抽象为一行if判断代码 ：
    if ((e.hash & oldCap) == 0）
    即如果位与原来的capacity结果为0 则代表hash值新增的高位为0。
    
    在JAVA7中rehash旧链表迁移新链表的时候，如果在新表的数组索引位置相同，则链表元素会倒置，但是从上图可以看出，而在JAVA8中不会倒置。
    
15. 解决hash冲突的方法
    1. open addressing开发定址
        1. linear probing
        2. Qaudratic
        3. Double Hashing h(k,i) = (h1(k) + i (h2(k))) mod m
    2. 链地址法 （类似hashMap）
    3. 再哈希法, 扩大数组的数量
    4. 公共溢出区域， 这种方法的基本思想是：将哈希表分为基本表和溢出表两部分，凡是和基本表发生冲突的元素，一律填入溢出表。
    
16. 那ArrayList，底层也是数组，查找也快啊，为啥不用ArrayList?

    因为采用基本数组结构，扩容机制可以自己定义，HashMap中数组扩容刚好是2的次幂，在做取模运算的效率高。 
    而ArrayList的扩容机制是1.5倍扩容，那ArrayList为什么是1.5倍扩容这就不在本文说明了。

17. 为什么为什么要先高16位异或低16位再取模运算? 
hashmap这么做，只是为了降低hash冲突的几率。https://zhuanlan.zhihu.com/p/76735726

18. 知道hashmap中put元素的过程是什么样么?
    对key的hashCode()做hash运算，计算index; 如果没碰撞直接放到bucket里； 如果碰撞了，以链表的形式存在buckets后； 
    如果碰撞导致链表过长(大于等于TREEIFY_THRESHOLD)，就把链表转换成红黑树(JDK1.8中的改动)； 
    如果节点已经存在就替换old value(保证key的唯一性) 如果bucket满了(超过load factor*current capacity)，就要resize。

19. 知道hashmap中get元素的过程是什么样么?
    对key的hashCode()做hash运算，计算index; 如果在bucket里的第一个节点里直接命中，则直接返回； 
    如果有冲突，则通过key.equals(k)去查找对应的Entry;
    若为树，则在树中通过key.equals(k)查找，O(logn)；
    若为链表，则在链表中通过key.equals(k)查找，O(n)。

20. 知道jdk1.8中hashmap改了啥么?
    由数组+链表的结构改为数组+链表+红黑树。
    优化了高位运算的hash算法：h^(h>>>16)
    扩容后，元素要么是在原位置，要么是在原位置再移动2次幂的位置，且链表顺序不变。
    hashmap在1.8中，不会在出现死循环问题。维护了两个链表，一个在原位置，一个在新位置，多线程也就是把东西放过去
    
21. 为什么在解决hash冲突的时候，不直接用红黑树?而选择先用链表，再转红黑树? 
    因为红黑树需要进行左旋，右旋，变色这些操作来保持平衡，而单链表不需要。 
    当元素小于8个当时候，此时做查询操作，链表结构已经能保证查询性能。
    当元素大于8个的时候，此时需要红黑树来加快查询速度，但是新增节点的效率变慢了。  
    
22. 为何是8？
    平均查找速度是N/2 vs ln， N= 6.64 相等。 8 变树， 6 变链表。 中间有个差值7可以防止链表和树之间频繁的转换。

23. 你一般用什么作为HashMap的key?
    
    一般用Integer、String这种不可变类当HashMap当key，而且String最为常用。
    (1)因为字符串是不可变的，所以在它创建的时候hashcode就被缓存了，不需要重新计算。这就使得字符串很适合作为Map中的键，字符串的处理速度要快过其它的键对象。这就是HashMap中的键往往都使用字符串。
    (2)因为获取对象的时候要用到equals()和hashCode()方法，那么键对象正确的重写这两个方法是非常重要的,这些类已经很规范的覆写了hashCode()以及equals()方法。     

24. 我用可变类当HashMap的key有什么问题?
    hashcode可能发生改变，导致put进去的值，无法get出，
    
## List
1. arrayList为啥不是线程安全的
```java
    transient Object[] elementData; 

    /**
     * 列表大小，elementData中存储的元素个数
     */
    private int size;
```
两个都无锁，而且add function

```java
public boolean add(E e) {

    /**
     * 添加一个元素时，做了如下两步操作
     * 1.判断列表的capacity容量是否足够，是否需要扩容
     * 2.真正将元素放在列表的元素数组里面
     */
    ensureCapacityInternal(size + 1);  // Increments modCount!!
    elementData[size++] = e;
    return true;
}
```
ensureCapacityInternal 不安全，
elementData[size++] = e; 也不安全


2. ArrayList与LinkedList的实现和区别
    1.ArrayList是实现了基于动态数组的数据结构，LinkedList基于链表的数据结构。 
    2.对于随机访问get和set，ArrayList觉得优于LinkedList，因为LinkedList要移动指针。 
    3.对于新增和删除操作add和remove，LinedList比较占优势，因为ArrayList要移动数据

3. ArrayList与Vector的区别
不要说vector是线程安全的，要说vector在add的时候使用了同步函数，方法上加上了synchronized关键字，ArrayList的add方法是没有加上这个关键字的。
当存储空间不足的时候，ArrayList默认增加为原来的50%，Vector默认增加为原来的一倍。    



      
    


