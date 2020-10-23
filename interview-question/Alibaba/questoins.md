# 阿里供应链
- [ ] 长期坚持、专注自己的深度、在考虑广度、基础要深一些
- [ ] 不要止步于文档，要阅读源代码
- [ ] 编码设计了解
- [ ] 前端call 后段reactive，来get一些数据，为何拿不到结果？ 来存储sessionId新建立的string没办法立即使用，为什么？String.local? 他的兄弟类？
- [ ] 软件设计原理？单一性？
- [ ] 如何界定微服务边界，如何划分？
- [ ] 如何保证contract 不 mismatch？ 测试？
- [ ] 集成测试的作用？
- [ ] Stream的一些常用方法 flatmap

```java

        List<List<Integer>> list = Arrays.asList(Arrays.asList(1,2,3), Arrays.asList(4,5,6));

        List<Integer> collect = list.stream()
                .flatMap(Collection::stream)
                .collect(Collectors.toList());

```

- [ ] reactive的一些方法
    concatmao and flatmap difference? 
- [ ] 如何克服困难？如何学习新的知识？
- [ ] Scrum的了解？最喜欢 最不喜欢？
- [ ] boxing unboxing
- [ ] 线程池原理  
- [ ] rxjava原理
微服务设计原理

serializa deserilazie


操作系统、网络痛惜、编解码、加密解密
云原声、容器化、虚拟化、微服务

主流框架、源码、底层原理、spring

    - [ ] 做的比较杂、深入了解比较浅、技术沉淀
    - [ ] 主动去问问题
    - [ ] 深层次得了解技术、闪光点亮出来
    - [ ] 任意一点能展现出来


虚拟机cpu 内存 怎么跑的、执行器
多线程、内存复用
编译器
Be active, no one would give you anything
工作是最好的练兵场


# 同城零售事业部 （原天猫超市和天猫app事业部）
    1. 供应链之上的业务部门
        业务中台， 供应链仓储、物流吧啦啦吧啦 服务于该部门    
    2. 天猫超市、手机天猫合并，新商业模式
        解决同城 社会上的商品库存进行整合，进行售卖
        解决履约服务 城市进行网格分割、1小时达。
        
        偏快消、服饰、自营，以及平台模式，给商家提供天猫平台的东西
        
        运维开发在做。 PAC（reactor java）的微服务框架，适合天猫导购链路，spring 的 阿里定制， pandora boot
        基于fast的框架的升级，ci/cd不完善，让业务团队可以做ci/cd定制 
        
        k8s 一般情况下， 云原声团队处理，不同特别管理，对知识要有了解
        
   kafka 怎么防止消息被重复消费、怎么确保至少被消费一次
        kafka 设置怎样定义是被消费的，确保一定被消费
        系统确保每个kafka message重复消费不会产生错误，可以通过ID来确保，之后给被修改object加锁
        
                
   哪些class是线程安全的，哪些不是。Concurrent hash map怎么实现的， hashmap/arrayList为啥不是线程安全的
   
   synchornized加在各个地方是锁定的什么？method，代码块, & 静态的方法
   https://juejin.im/post/6844903670933356551  
   
   vertx 和 rxjava 为何要用vertx， rxjava也可以实现event driven？
   从cpu utilizzation、非阻塞、async io来讲？
   
   rxjava 学习成本，使用习惯，很难理解的bug，如何应对 
   
    没有subscribe
    api比较多，比较难上手
    没必要用rx的一些function和操作在用rx
   
     http://konmik.com/post/when_to_not_use_rxjava/
     Do not use RxJava if there is no “event”. Just don’t. If something does not say: “this will happen at some point in time”, do not create observables.
     RxJava is not a data processing library. It is an event processing library.
     
     Reactive spaghetti
     Don't wire non-enevt together. reduce number of event if possible
     
     and more
   
   map/flatmap 的区别
    
    flatmap create another obersavable and activate it. but collecting all the result into another observable.
   
   load balancer，如何设计，怎样实现
   
   自己偏向的方向，业务架构、微服务框架 blablaba
   
   框架如何做云原生   
   
   框架如何提高了开发效率，围绕vertx 做了哪些二次开发？


# 菜鸟AI
## 1st round 
组合问题：
n个颜色（～1m）每个都有k个棋子（～ 20-30） 

（0，0）-（m,m）的网格，棋子可以堆叠
（0，0） -》（x,y)  出现的颜色
集合的cardinality？

把集合上的运算做掉，之后再做其他运算
假设k=1， 所有jiwei 1 
k=2，交集为-1，并集除去交集就是-1
k个点，有o（k）个交点

先o（nk + m^2）  查询为一
m很大的话就不能这样做

荣氏原理

-------
n ge N位的数字 001 为3
k 给定

o（n）
sol1: 扫描两遍 1. hash， b[k-a[i]] = i ，之后再扫描a看相应位子上是否有数字 
sol2: 桶排序 最后一位排序，在倒数第二位排序.....， o(N*n), 之后再前后指针往中间移动
----
CLM
Dominant convergence theorem
poisson什么情况下有解

内生变量 外生变量

## 2nd round
1. 排序算法，优缺点，时间复杂度
2. 图论：找最短路径
3. 做虚拟机，如何安排内存、CPU到物理机上。类似于装箱
    1. 如果是装快递，如何用最小表面积的箱子装所有的快递
    2. 如何做旋转什么的
4. 连续掷骰子直到总和=n， 概率是多少
http://www.mathchina.net/dvbbs/dispbbs.asp?boardid=5&id=7807

到投很多次的时候，概率趋向于平均值，所以每一次的结果平均是3.5，如果在某一个格子停下来=n的概率= 2/7

```python
import random

GOAL = 2020

def calculate(number):
    cumulate = 0
    while cumulate < GOAL:
        cumulate += random.randint(1, 6)
        if cumulate == GOAL:
            print("Got one")
            number += 1
            return number
        if cumulate > GOAL:
            print("Not able to" + str(cumulate))
            return number
        if cumulate < GOAL:
            continue

number = 0
for i in range(1, 100000):
    number = calculate(number)
    print(number)

```


Some Algo Questions
========= 
# Packing
To Read: 
https://anivian.github.io/pack-master/V2.pdf
## 构造法(Construetion Algorithms)
第一类为定序规则(orderingrules),它被用来确定待布局物体放入布局空间的先后顺序:
第二类为定位规则(Placement rules),它用来确定每一个布局物体在布局空间摆放的位置。

### 常用的定序规则有:
    (l)按照待装物体最短边边长递减的顺序进行定序:
    (2)按照待装物体最长边边长递减的顺序进行定序;
    (3)按照待装物体体积递减的顺序进行定序;
    (4)按照待装物体最小面积递减的顺序进行定序;
    (5)按照待装物体可行域递减的顺序进行定序:

### 常用的定位规则有:
    (1)占角策略,即将待装物体摆放在布局容器的某一角:
    (2)顺放策略,即从布局容器的某一角开始将待装物体沿着容器某一边摆放;
    (3)在底盘装载中,将待装物体先沿着边放置,最后摆放到底盘中心;
    (4)在三维规则装箱中,从布局容器的某一面墙开始,一层一层地布局;在某一面墙上,再确定一条边,最后归结为一个角:
    (5)在三维规则装箱中,先按“右、前、上”的顺序找寻剩余空间,然后按照“左、后、下”的顺序摆待装局物体:
    
## 现代优化方法
主要有遗传算法(GenetiC Algorithm,GA),模拟退火法(SimulatedAnnealing,SA)两种。

https://drwxyh.github.io/2018/08/15/Bin-Packing/


# 数组找数字
## 从数组中找出只出现一次的1个数，数组中其他数都出现两次
1. 用hashmap
2. 找所有出现过的元素，sum*2 - 原有数组的sum
3. 位运算
```java
class Solution {
    public int singleNumber(int[] nums) {
        int single = 0;
        for (int num : nums) {
            single ^= num;
        }
        return single;
    }
}

```
## 从数组中找出只出现一次的两个数，数组中其他数都出现两次
1. 用HashMap
2. 位运算，看下面
https://blog.csdn.net/qq_27474589/article/details/77451009

## 第一个只出现一次的字符
1. 第一遍找只出现一次的字符，第二遍找出第一个
2. 用linkedHashMap，就不用找第二遍
https://leetcode-cn.com/problems/di-yi-ge-zhi-chu-xian-yi-ci-de-zi-fu-lcof/solution/mian-shi-ti-50-di-yi-ge-zhi-chu-xian-yi-ci-de-zi-3/

# 组合总和
Very Good Resource:
https://zhuanlan.zhihu.com/p/78080883
### 给定一个int数组A，数组中元素互不重复，给定一个数x，求所有求和能得到x的数字组合，组合中的元素来自A，可重复使用。

A = [3,2,6,7]
x = 7

其中一种输出结果：
[
    [7],
    [2,2,3]
]

solution: https://blog.csdn.net/qq_32131499/article/details/95940817

优化：
1. [5, 3], [3, 5] 与 [2, 3, 3], [3, 2, 3], [3, 3, 2] ，这两组答案都发生了重复，
仔细考虑前面的代码，其实不需要每次都将candidates完全遍历一遍，
举个具体的栗子：当有了答案 [2, 3, 3] 时，就不需要[3, 2, 3]和[3, 3, 2]了，
所以只要将传给下级递归函数的candidate中的元素2给移除就可以了
which is done in the solution link:
```java
 for(int i=index;i<nums.length;i++){ //i=index does the job
            if(target-nums[i]<0)
                return;
            tempList.add(nums[i]);
            getSetOfSum(nums,res,tempList,target-nums[i],i);
            tempList.remove(tempList.size()-1);
 
        }

```

### 输入两个整数n和sum，从数列1，2，3.......n 中随意取几个数，使其和等于sum，要求将其中所有的可能组合列出来。
#### 解法1 
注意到取n，和不取n个区别即可，考虑是否取第n个数的策略，可以转化为一个只和前n-1个数相关的问题。

如果取第n个数，那么问题就转化为“取前n-1个数使得它们的和为sum-n”，对应的代码语句就是sumOfkNumber(sum - n, n - 1)；
如果不取第n个数，那么问题就转化为“取前n-1个数使得他们的和为sum”，对应的代码语句为sumOfkNumber(sum, n - 1)。

#### 解法2
https://wizardforcel.gitbooks.io/the-art-of-programming-by-july/content/02.03.html
 

### 求出给定数组中某一段连续区间之和为某值的起始索引
https://blog.csdn.net/u014106644/article/details/84229125
双指针

j循环向后加时，防止越界；当区间和等于target，再向后遍历，可以i+或j+，但是j+可能会越界，因此选择i+

### 合并两个有序数组
1. 从头挨个比较
```java

 1class Solution { 2    public void merge(int[] nums1, int m, int[] nums2, int n) { 3        int [] result = new int[m+n]; 4        int i =0,j=0,k=0; 5        while(i < m && j < n) 6        { 7            if(nums1[i] < nums2[j]){ 8                result[k++] = nums1[i++]; 9            }else{10                result[k++] = nums2[j++];11            }12        }13        if(i != m)14        {15            while(i < m)16            {17                result[k++] = nums1[i++];18            }19        }20        if(j != n)21        {22            while(j < n)23            {24                result[k++] = nums2[j++];25            }26        }27        k = 0;28        for(i=0;i<nums1.length;i++)29            nums1[i] = result[k++];3031    }32}

```
2. 你可以假设 nums1 有足够的空间（空间大小大于或等于 m + n）来保存 nums2 中的元素。
直接比较nums1和nums2元素的大小，然后根据大小加入到nums1的末尾，最后还要考虑nums2的元素是否还有剩余即可。

```java
class Solution:
    def merge(self, nums1, m, nums2, n):
        """
        :type nums1: List[int]
        :type m: int
        :type nums2: List[int]
        :type n: int
        :rtype: void Do not return anything, modify nums1 in-place instead.
        """
        pos = m + n - 1
        m -= 1
        n -= 1
        while m >= 0 and n >= 0:
            if nums1[m] > nums2[n]:
                nums1[pos] = nums1[m]
                pos -= 1
                m -= 1
            else:
                nums1[pos] = nums2[n]
                pos -= 1
                n -= 1
// 如果1 有剩余，所在位置就是正确的
//如果2 有剩余，放到num1的位置去
        while n >= 0:
            nums1[pos] = nums2[n]
            pos -= 1
            n -= 1
```

### 求众数
#### 1. 绝对众数
1. 按顺序加入Hashmap
2. 投票法， 如果有绝对众数，从数组中删掉两个不同元素，绝对众数不变。我们维护一个计数器，如果遇到一个我们目前的候选众数，就将计数器加一，否则减一。只要计数器等于 0 ，我们就将 nums 中之前访问的数字全部 忘记 ，并把下一个数字当做候选的众数。
```java
func majorityElement(nums []int) int {
    var (
        selectNum int
        count int
    )
    
    for _, num := range nums {
        if count == 0 {
            selectNum = num
        }
        
        if selectNum == num {
            count += 1
        } else {
            count -= 1
        }
    }
    
    return selectNum
}
```
3. 排序，中间的数字肯定是众数
#### 2. 1/K 众数， 该数字数量> N/K
e.g. k=3
```java

 void FindMode(const int *a, int size, vector<int>& mode){
    int m,n;//候选值
    int cm = 0, cn = 0;//候选值m、n的个数
    int i;
    for(i=0; i<size; i++){
        if(cm == 0){
            m = a[i];
            cm = 1;
        }else if(cn == 0){
            n = a[i];
            cn = 1;
        }else if(m == a[i]){
            cm++;
        }else if(n == a[i]){
            cn++;
        }else{
            cm--;
            cn--;
        }
    }
    //↑ 运行到此处时的m、n一定是众数，同时也是可能存在的1/3众数。
    
    cm = cn = 0;//为确保一定存在（因为1/3众数可能不存在），一定要重新遍历统计出现次数
    for(i=0; i<size; i++){
        if(m == a[i]){
            cm++;
        }else if(n == a[i]){
            cn++;
        }
    }
    if(cm > size/3){
        mode.push_back(m);
//        cout<< m<<" ";
    }
    if(cn > size/3){
        mode.push_back(n);
//        cout<< n<<" ";
    }
```

# 矩阵
## [找数字](https://codechina.org/2019/05/leetcode-240-search-a-2d-matrix-ii-java/)
写一个高效的算法，从一个m×n的整数矩阵中查找出给定的值，矩阵具有如下特点：
    每一行从左到右递增。
    每一列从上到下递增。

1. 解法1：
先删掉不符合的列 之后每一行判断最后一个值的大小，如果小，换到下一行  
```java
        int i,j,rows = matrix.size(),cols = matrix[0].size();

        i = 0; 
        j = cols-1; 
        while(i < rows && j >= 0)
        {
            if(matrix[i][j] == target) return true;
            else if(matrix[i][j] > target) j--;
            else i++;
        }
```
2. 解法2：二分法
首先把矩阵竖向分割，分为上下，分后在竖向分割为上下，直到只剩下一个行，则在行里面使用二分查找。
只利用了矩阵的一条属性，就是从左到右生序排列。纵向的顺序我们没有利用到。方法一比较好

3. 四块分别查找,分成左小右大，上小下大，根据几个关键点判断target是否存在来判断这个子矩阵要不要进入
每个子矩阵的左上和右下都是关键点。这样可以判断是否在这个range 里


