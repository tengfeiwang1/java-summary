## 面试题目

1.

1. es 一个shard和十个shard的使用磁盘大小是否一样
2. 动态规划-二叉树最大
3. static synchronizer 锁类
4. 设计模式
5. 分布式一致性算法原理 ：zookeeper实现数据一致性的核心是ZAB协议（Zookeeper原子消息广播协议）
6. zk选举leader算法：FastLeaderElection(默认)，FastLeaderElection选举算法是标准的Fast Paxos算法实现，可解决LeaderElection选举算法收敛速度慢的问题。



CAP原则又称CAP定理，指的是在一个分布式系统中，Consistency（一致性）、 Availability（可用性）、Partition tolerance（分区容错性），三者不可兼得。 

记录1
如何排查服务可用性问题：
http://www.importnew.com/27436.html

2019-1-31记录2
1. kafka streaming聚合统计操作的原理，自己如何实现---提交job
2. redis的持久化方案 aof  ，rdb
3. redis相关的内存数据库-mongodb，leveldb（写性能快于读，读也不慢），memcached(单节点)
4. 为什么选redis不选择其他数据库？
5. kafka为什么可以提供大量的，快速的数据传输？---顺序写入（快速写），内存transsendfile（快速读）
6. 流式处理如何设计--参考mapreduce
7. kafka topic partition 的架构
Broker：Kafka节点，一个Kafka节点就是一个broker，多个broker可以组成一个Kafka集群。
Topic：一类消息，消息存放的目录即主题，例如page view日志、click日志等都可以以topic的形式存在，Kafka集群能够同时负责多个topic的分发。
Partition：topic物理上的分组，一个topic可以分为多个partition，每个partition是一个有序的队列
Segment：partition物理上由多个segment组成，每个Segment存着message信息
Producer : 生产message发送到topic
Consumer : 订阅topic消费message, consumer作为一个线程来消费
Consumer Group：一个Consumer Group包含多个consumer, 这个是预先在配置文件中配置好的。各个consumer（consumer 线程）可以组成一个组（Consumer group ），partition中的每个message只能被组（Consumer group ） 中的一个consumer（consumer 线程 ）消费，如果一个message可以被多个consumer（consumer 线程 ） 消费的话，那么这些consumer必须在不同的组。Kafka不支持一个partition中的message由两个或两个以上的consumer thread来处理，即便是来自不同的consumer group的也不行。它不能像AMQ那样可以多个BET作为consumer去处理message，这是因为多个BET去消费一个Queue中的数据的时候，由于要保证不能多个线程拿同一条message，所以就需要行级别悲观锁（for update）,这就导致了consume的性能下降，吞吐量不够。而kafka为了保证吞吐量，只允许一个consumer线程去访问一个partition。如果觉得效率不高的时候，可以加partition的数量来横向扩展，那么再加新的consumer thread去消费。这样没有锁竞争，充分发挥了横向的扩展性，吞吐量极高。这也就形成了分布式消费的概念。

2019-1-31 设计记录3
1. 网口协议分析-流量分析（大数据量如何分析）
2. 员工获取到了高级权限-怎么去定位到这个员工，怎么解决这种问题--流量过滤，外加方法流和分布式
3. es yellow问题解决，分片问题解决--unassigined   主分片可用，副分片不可用
https://www.cnblogs.com/bonelee/p/7458221.html
4. es索引优化-
5. es服务宕机处理 --一个节点宕机，磁盘占满情况下，需要迁移节点数据目录到大容量的机器上，并需要reroute
https://blog.csdn.net/u012386386/article/details/78811618
6. es缓存分配三种缓存机制---
es配置jvm占用内存最大32G,lucene heap占用系统内存（不超过系统可用内存的一半）
filter cache(query cache)  10% heap内存
request cache

https://www.jianshu.com/p/3bdac5b0fed8
https://blog.csdn.net/abcCL6/article/details/77171270

优化es https://horsemen.iteye.com/blog/2277050
