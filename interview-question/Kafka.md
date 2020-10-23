Kafka Learning (Source: https://kafka.apache.org/intro and https://www.jianshu.com/p/abaa6c01d723)
================
#Concept

- Kafka is run as a cluster on one or more servers that can **span multiple datacenters**.
- The Kafka cluster stores streams of records in categories called **topics**.
- Each record consists of a key, a value, and a timestamp.

#APIs
Source 
- The **Producer** API allows an application to publish a stream of records to one or more Kafka topics.
- The **Consumer** API allows an application to subscribe to one or more topics and process the stream of records produced to them.
- The **Streams** API allows an application to act as a stream processor, consuming an input stream from one or more topics and producing an output stream to one or more output topics, effectively transforming the input streams to output streams.
- The **Connector** API allows building and running reusable producers or consumers that connect Kafka topics to existing applications or data systems. For example, a connector to a relational databaseinteraction might capture every change to a table.

#Topic
- Topics in Kafka are always multi-subscriber; that is, a topic can have zero, one, or many consumers that subscribe to the data written to it.
## Partition
- For each topic, the Kafka cluster maintains a partitioned log that looks like this:
![Topic](https://kafka.apache.org/23/images/log_anatomy.png)
- Each partition is an **ordered, immutable** sequence of records that is continually appended to—a structured commit log. The records in the partitions are each assigned a sequential id number called the **offset** that uniquely **identifies each record within the partition.**
- purposes:
    - scale beyond a size that will fit on a single server. Each individual partition must fit on the servers that host it, but a topic may have many partitions so it can handle an arbitrary amount of data. 
    - they act as the unit of parallelism—more on that in a bit.
- The partitions distributed over the servers in cluster. Each server handling data and requests for a share of the partitions. Each partition is replicated across a configurable number of servers for fault tolerance.
- Each partition, one "leader", zero or more "followers". The leader handles all read and write requests for the partition while the followers passively replicate the leader.
- Kafka only provides a total order over records within a partition, not between different partitions in a topic.
- Per-partition ordering combined with the ability to partition data by key is sufficient for most applications. However, if you require a total order over records this can be achieved with a topic that has only one partition, though this will mean only one consumer process per consumer group.
## Offset
- Offset is controlled by the consumer: since the position is controlled by the consumer, it can consume records in any order it likes. 
-  a consumer can reset to an older offset to reprocess data from the past or skip ahead to the most recent record and start consuming from "now".

## Retention Period
- if the retention policy is set to two days, then for the two days after a record is published, it is available for consumption, after which it will be discarded to free up space.

# Producer and Consumer
## Producer
- The producer is responsible for choosing which record to assign to which partition within the topic. Mechanism can be chosen

## Consumer
- Consumers label themselves with a consumer group name, and each record published to a topic is delivered to one consumer instance within each subscribing consumer group. Consumer instances can be in separate processes or on separate machines.
- If all the consumer instances have the same consumer group, then the records will effectively be load balanced over the consumer instances.
- If all the consumer instances have different consumer groups, then each record will be broadcast to all the consumer processes.
![consumer](https://kafka.apache.org/23/images/consumer-groups.png)

- The way consumption is implemented in Kafka is by dividing up the partitions in the log over the consumer instances so that each instance is the exclusive consumer of a "fair share" of partitions at any point in time.
- This process of maintaining membership in the group is handled by the Kafka protocol dynamically. If new instances join the group they will take over some partitions from other members of the group; if an instance dies, its partitions will be distributed to the remaining instances.


# Usage
Messaging
Storage System
Stream Processing



# [Message Delivery Semantic](https://kafka.apache.org/documentation/#semantics)
Clearly there are multiple possible message delivery guarantees that could be provided:

* At most once—Messages may be lost but are never redelivered.
* At least once—Messages are never lost but may be redelivered.
* [Exactly once](https://www.confluent.io/blog/exactly-once-semantics-are-possible-heres-how-apache-kafka-does-it/) —
and [this](https://www.cnblogs.com/jasongj/p/7912348.html)
this is what people actually want, each message is delivered once and only once.

1. Producer
Message commited to logs. Once committed, never lost, as long as the partition is replicated
    1. 幂等(idempotence) by enable.idempotence = true: 
    
        Since 0.11.0.0, the Kafka producer also supports an idempotent delivery option which guarantees that resending will not result in duplicate entries in the log.
        **The broker assigns each producer an ID and deduplicates messages using a sequence number that is sent by the producer along with every message.**
   
        上述设计解决了0.11.0.0之前版本中的两个问题：
  
       Broker保存消息后，发送ACK前宕机，Producer认为消息未发送成功并重试，造成数据重复
       前一条消息发送失败，后一条消息发送成功，前一条消息重试后成功，造成数据乱序
       
       more in detail: https://blog.csdn.net/qq_35078688/article/details/86082858
   
    2.  事务(Transaction)
        
        上述幂等设计只能保证单个Producer对于同一个<Topic, Partition>的Exactly Once语义。
        它并不能保证写操作的原子性——即多个写操作，要么全部被Commit要么全部不被Commit。
    
        the producer supports the ability to send messages to multiple topic partitions using **transaction-like semantics**: 
        i.e. either all messages are successfully written or none of them are. 
        The main use case for this is exactly-once processing between Kafka topics (described below).
        
        1. For uses which are latency sensitive we allow the producer to specify the durability level it desires. 
            If the producer specifies that it wants to wait on the message being committed this can take on the order of 10 ms
        2. the producer can also specify that it wants to perform the send completely asynchronously or 
            that it wants to wait only until the leader (but not necessarily the followers) have the message.
        e.g. 
            ```java
            producer.initTransactions();
            try {
              producer.beginTransaction();
              producer.send(record1);
              producer.send(record2);
              producer.commitTransaction();
            } catch(ProducerFencedException e) {
              producer.close();
            } catch(KafkaException e) {
              producer.abortTransaction();
            }
            ```
           
    首先使用tid请求任意一个broker（代码中写的是负载最小的broker），找到对应的transaction coordinator。
    
    请求transaction coordinator获取到对应的pid，和pid对应的epoch，这个epoch用于防止僵死进程复活导致消息错乱，
    当消息的epoch比当前维护的epoch小时，拒绝掉。tid和pid有一一对应的关系，这样对于同一个tid会返回相同的pid。
    
    client先请求transaction coordinator记录<topic,partition>的事务状态，初始状态是BEGIN，
    如果是该事务中第一个到达的<topic,partition>，同时会对事务进行计时；
    client输出数据到相关的partition中；client再请求transaction coordinator记录offset的<topic,partition>事务状态；
    client发送offset commit到对应offset partition。
    client发送commit请求，transaction coordinator记录prepare commit/abort，然后发送marker给相关的partition。
    全部成功后，记录commit/abort的状态，最后这个记录不需要等待其他replica的ack，因为prepare不丢就能保证最终的正确性了。
    
    链接：https://www.jianshu.com/p/d3e963ff8b70 
    
    而从consumer的角度来看，有两种策略去读取事务写入的消息，通过"isolation.level"来进行配置：
    
    read_committed：可以同时读取事务执行过程中的部分写入数据和已经完整提交的事务写入数据；
    
    read_uncommitted：完全不等待事务提交，按照offsets order去读取消息，也就是兼容0.11.x版本前Kafka的语义；
    
    我们必须通过配置consumer端的配置isolation.level，来正确使用事务API，
    通过使用 new Producer API并且对一些unique ID设置transaction.id（该配置属于producer端），该unique ID用于提供事务状态的连续性。
    
2. Consumer
    1. All replicas have the exact same log with the same offsets. The consumer controls its position in this log.
    2. if the consumer fails and we want this topic partition to be taken over by another process the new process will need to choose an appropriate position from which to start processing.
    3. Options:
        1. At Most Once: can read the messages, then save its position in the log, and finally process the messages.
        2. At Least Once: It can read the messages, process the messages, and finally save its position. 
    4. Solution:
        1. **Kafka Streaming**(processing.guarantee=exactly_once): In 0.11.0.0, The consumer's position is stored as a message in a topic. If the transaction is aborted, the consumer's position will revert to its old value and the produced data on the output topics will not be visible to other consumers.
        2. When writing to an external system: two-phase commit between the storage of the consumer position and the storage of the consumers output. If 2 phase is not supported. think a Kafka Connect connector which populates data in HDFS along with the offsets of the data it reads so that it is guaranteed that either data and offsets are both updated or neither is.
        
    5. So effectively Kafka supports exactly-once delivery in Kafka Streams, and the transactional producer/consumer can be used generally to provide exactly-once delivery when transferring and processing data between Kafka topics. Exactly-once delivery for other destination systems generally requires cooperation with such systems, but Kafka provides the offset which makes implementing this feasible (see also Kafka Connect).


# Replicaton
The unit of replication is the topic partition. 

ypically, there are many more partitions than brokers and the leaders are evenly distributed among brokers. The logs on the followers are identical to the leader's log—all have the same offsets and messages in the same order

Followers consume messages from the leader just as a normal Kafka consumer would and apply them to their own log. Having the followers pull from the leader has the nice property of allowing the follower to naturally batch together log entries they are applying to their log.

For Kafka node liveness/(in sync) has two conditions

* A node must be able to maintain its session with ZooKeeper (via ZooKeeper's heartbeat mechanism)
* If it is a follower it must replicate the writes happening on the leader and not fall "too far" behind

The leader keeps track of the set of "in sync" nodes. If a follower dies, gets stuck, or falls behind, the leader will remove it from the list of in sync replicas. (configured by replica.lag.time.max.ms configuration)

a message is considered committed when all in sync replicas for that partition have applied it to their log. Only committed messages are ever given out to the consumer. 

Producers, on the other hand, have the option of either waiting for the message to be committed or not, depending on their preference for tradeoff between latency and durability. 


The fundamental guarantee a log replication algorithm must provide is that if we tell the client a message is committed, and the leader fails, the new leader we elect must also have that message. --> This yields a tradeoff: if the leader waits for more followers to acknowledge a message before declaring it committed then there will be more potentially electable leaders. --> If you choose the number of acknowledgements required and the number of logs that must be compared to elect a leader such that there is guaranteed to be an overlap, then this is called a Quorum.

-->  majority vote for both the commit decision and the leader election

 Instead of majority vote, Kafka dynamically maintains a set of in-sync replicas (ISR) that are caught-up to the leader. Only members of this set are eligible for election as leader. A write to a Kafka partition is not considered committed until all in-sync replicas have received the write  With this ISR model and f+1 replicas, a Kafka topic can tolerate f failures without losing committed messages.
 
 The ability to commit without the slowest servers is an advantage of the majority vote approach. However, we think it is ameliorated by allowing the client to choose whether they block on the message commit or not, and the additional throughput and disk space due to the lower required replication factor is worth it.
 
 Kafka does not require that crashed nodes recover with all their data intact.  Our protocol for allowing a replica to rejoin the ISR ensures that before rejoining, it must fully re-sync again even if it lost unflushed data in its crash.

# Cloud Solution
https://aws.amazon.com/msk/?nc1=h_ls
https://aws.amazon.com/cn/blogs/aws/amazon-managed-streaming-for-apache-kafka-msk-now-generally-available/
https://cn.aliyun.com/product/kafka

#Streaming
(processing.guarantee=exactly_once) this includes making both the processing and also all of the materialized state created by the processing job that is written back to Kafka, exactly once.
Exactly once only for its internal process, not to other state changing process: Note that exactly-once semantics is guaranteed within the scope of Kafka Streams’ internal processing only; 
for example, if the event streaming app written in Streams makes an RPC call to update some remote stores, or if it uses a customized client to directly read or write to a Kafka topic, the resulting side effects would not be guaranteed exactly once.

The key, when recovering a failed instance, is to resume processing in exactly the same state as before the crash.
## How does it do it
Now, stream processing is nothing but a read-process-write operation on a Kafka topic; a consumer reads messages from a Kafka topic, some processing logic transforms those messages or modifies state maintained by the processor, and a producer writes the resulting messages to another Kafka topic.
Exactly-once stream processing is **simply the ability to execute a read-process-write operation exactly one time** 



## how do design an imdenpodent system?

任意多次执行所产生的影响均与一次执行的影响相同，这是幂等性的核心特点。
其实在我们编程中主要操作就是CURD，其中读取（Retrieve）操作和删除（Delete）操作是天然幂等的，
受影响的就是创建（Create）、更新（Update）

对于业务中需要考虑幂等性的地方一般都是接口的重复请求，重复请求是指同一个请求因为某些原因被多次提交。导致这个情况会有几种场景：

1. 前端重复提交：提交订单，用户快速重复点击多次，造成后端生成多个内容重复的订单。
2. 接口超时重试：对于给第三方调用的接口，为了防止网络抖动或其他原因造成请求丢失，这样的接口一般都会设计成超时重试多次。
3. 消息重复消费：MQ消息中间件，消息重复消费。

### 幂等性实现方式

#### Token机制
主要流程就是：

服务端提供了发送token的接口。我们在分析业务的时候，哪些业务是存在幂等问题的，就必须在执行业务前，先去获取token，服务器会把token保存到redis中。（微服务肯定是分布式了，如果单机就适用jvm缓存）。
然后调用业务接口请求时，把token携带过去，一般放在请求头部。
服务器判断token是否存在redis中，存在表示第一次请求，可以继续执行业务，执行业务完成后，最后需要把redis中的token删除。
如果判断token不存在redis中，就表示是重复操作，直接返回重复标记给client，这样就保证了业务代码，不被重复执行。

#### 数据库去重表
往去重表里插入数据的时候，利用数据库的唯一索引特性，保证唯一的逻辑。唯一序列号可以是一个字段，例如订单的订单号，也可以是多字段的唯一性组合。例如设计如下的数据库表。

```shell script
CREATE TABLE `t_idempotent` (
  `id` int(11) NOT NULL COMMENT 'ID',
  `serial_no` varchar(255)  NOT NULL COMMENT '唯一序列号',
  `source_type` varchar(255)  NOT NULL COMMENT '资源类型',
  `status` int(4) DEFAULT NULL COMMENT '状态',
  `remark` varchar(255)  NOT NULL COMMENT '备注',
  `create_by` bigint(20) DEFAULT NULL COMMENT '创建人',
  `create_time` datetime DEFAULT NULL COMMENT '创建时间',
  `modify_by` bigint(20) DEFAULT NULL COMMENT '修改人',
  `modify_time` datetime DEFAULT NULL COMMENT '修改时间',
  PRIMARY KEY (`id`)
  UNIQUE KEY `key_s` (`serial_no`,`source_type`, `remark`)  COMMENT '保证业务唯一性'
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COMMENT='幂等性校验表';
```
由于数据建立了 serial_no,source_type, remark 三个字段组合构成的唯一索引，所以可以通过这个来去重达到接口的幂等性，具体的代码设计如下，

另外，使用数据库防重表的方式它有个严重的缺点，那就是系统容错性不高，如果幂等表所在的数据库连接异常或所在的服务器异常，则会导致整个系统幂等性校验出问题。如果做数据库备份来防止这种情况，又需要额外忙碌一通了啊。

#### [Redis实现](https://img2018.cnblogs.com/blog/1162587/201905/1162587-20190526224227719-1701958206.png)
Redis实现的方式就是将唯一序列号作为Key，value可以是你想填的任何信息。
当然这里需要设置一个 key 的过期时间，否则 Redis 中会存在过多的 key。

由于企业如果考虑在项目中使用 Redis，因为大部分会拿它作为缓存来使用，那么一般都会是集群的方式出现，至少肯定也会部署两台Redis服务器。所以我们使用Redis来实现接口的幂等性是最适合不过的了。


#### 状态机
对于很多业务是有一个业务流转状态的，每个状态都有前置状态和后置状态，以及最后的结束状态。例如流程的待审批，审批中，驳回，重新发起，审批通过，审批拒绝。订单的待提交，待支付，已支付，取消。

以订单为例，已支付的状态的前置状态只能是待支付，而取消状态的前置状态只能是待支付，通过这种状态机的流转我们就可以控制请求的幂等。

假设当前状态是已支付，这时候如果支付接口又接收到了支付请求，则会抛异常或拒绝此次处理。

#### 1.3 消费者丢失
消费者丢失的情况，其实跟消费者位移处理不当有关。消费者位移提交有一个参数，enable.auto.commit，默认是true，决定是否要让消费者自动提交位移。如果开启，那么consumer每次都是先提交位移，再进行消费，比如先跟broker说这5个数据我消费好了，然后才开始慢慢消费这5个数据。
这样处理的话，好处是简单，坏处就是漏消费数据，比如你说要消费5个数据，消费了2个自己就挂了。那下次该consumer重启后，在broker的记录中这个consumer是已经消费了5个的。
所以最好的做法就是将enable.auto.commit设置为false，改为手动提交位移，在每次消费完之后再手动提交位移信息。当然这样又有可能会重复消费数据，毕竟exactly once处理一直是一个问题呀（/摊手）。
遗憾的是kafka目前没有保证consumer幂等消费的措施，如果确实需要保证consumer的幂等，可以对每条消息维持一个全局的id，每次消费进行去重，当然耗费这么多的资源来实现exactly once的消费到底值不值，那就得看具体业务了。

-------
producer的acks设置位-1，同时min.insync.replicas设置大于1。并且使用带有回调的producer api发生消息。
默认副本数replication.factor设置为大于1，或者创建topic的时候指定大于1的副本数。
unclean.leader.election.enable 设置为false，防止定期副本leader重选举
消费者端，自动提交位移enable.auto.commit设置为false。在消费完后手动提交位移
### Reference:

1. https://www.cnblogs.com/jajian/p/10926681.html
2. 分布式高并发系统如何保证对外接口的幂等性？ - kimmking的回答 - 知乎 
 https://www.zhihu.com/question/27744795/answer/905254616 
3. To Read: 
    1. https://tech.meituan.com/2016/09/29/distributed-system-mutually-exclusive-idempotence-cerberus-gtis.html 
    2. http://www.jasongj.com/kafka/transaction/
4. https://www.hellojava.com/a/87230.html


9. kafka 内部储存方式， 如何只产生一次，如何只消费一次。如果produce后leader down了怎么办
https://www.cnblogs.com/zmoumou/p/10313348.html