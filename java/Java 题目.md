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


2019-1-31记录2
1. kafka streaming聚合统计操作的原理，自己如何实现
2. redis的持久化方案 aof  ，rdb
3. redis相关的内存数据库-mongodb，leveldb（写性能快于读，读也不慢），memcached(单节点)
4. 为什么选redis不选择其他数据库？
5. kafka为什么可以提供大量的，快速的数据传输？---顺序写入（快速写），内存transsendfile（快速读）
6. 流式处理如何设计--参考mapreduce
7. kafka topic partition 的架构

2019-1-31 设计记录3
1. 网口协议分析-流量分析（大数据量如何分析）
2. 员工获取到了高级权限-怎么去定位到这个员工，怎么解决这种问题
3. es yellow问题解决，分片问题解决--unassigined   主分片可用，副分片不可用
https://www.cnblogs.com/bonelee/p/7458221.html
4. es索引优化
5. es服务宕机处理
6. es缓存分配三种缓存机制
https://www.jianshu.com/p/3bdac5b0fed8

优化es https://horsemen.iteye.com/blog/2277050
