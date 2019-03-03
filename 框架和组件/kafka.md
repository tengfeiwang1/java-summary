# KAFKA

详解：http://www.cnblogs.com/likehua/p/3999538.html
事务：https://www.zybuluo.com/tinadu/note/949867
1. 为什么使用消息队列？
解耦，异步，削峰（避免大数据量造成系统宕机）

2. Zookeeper在kafka中作用
 1.broker注册；2.topic注册；3.生产者负载均衡；4.消费者信息；5.消费者和分区关系；6.消费者负载均衡；7.消费者offset提交（high level api，0.11版本转移到kafka的consumer_offset topic）
3. 多分区的设计的特点： 
  * 1 为了并发读写，加快读写速度； 
  * 2 是利用多分区的存储，利于数据的均衡； 
  * 3 是为了加快数据的恢复速率，一但某台机器挂了，整个集群只需要恢复一部分数据，可加快故障恢复的时间。

## Kafka快速写：
1. 以顺序追加的方式向各个分区中写入消息-消息顺序写入磁盘。
2. 同时，KAFKA采用了MMAP(Memory Mapped Files，内存映射文件)技术-利用操作系统的页缓存来实现文件到物理内存的直接映射。完成映射之后对物理内存的操作在适当时候会被同步到硬盘上。
## Kafka快速读：
1. 零拷贝-在Linux中，是通过sendfile系统调用来完成的。Java提供了访问这个系统调用的方法：FileChannel.transferTo API。
Kafka使用sendfile，只需要一次拷贝就行：允许操作系统将数据直接从页缓存发送到网络上。所以在这个优化的路径中，只有最后一步将数据拷贝到网卡缓存中是需要的。这种页缓存和sendfile组合，意味着KAFKA集群的消费者大多数都完全从缓存消费消息，而磁盘没有任何读取活动


## ack典型的值： 
* 0： 表示Producer从来不等待来自broker的确认信息。  这个选择提供了最小的时延但同时风险最大（因为当server宕机时，数据将会丢失）。 
* 1：表示获得Leader replica已经接收了数据的确认信息。  这个选择时延较小同时确保了server确认接收成功。 
* -1：Producer会获得所有同步replicas都收到数据的确认。  同时时延最大，然而，这种方式并没有完全消除丢失消息的风险，因为同步replicas的数量可能是1。如果你想确保某些replicas接收到数据，那么你应该在Topic-level设置中选项min.insync.replicas设置一下。

仅设置 acks= -1 也不能保证数据不丢失,当ISR列表中只有Leader时,同样有可能造成数据丢失。要保证数据不丢除了设置acks=-1，还要保证ISR的大小大于等于2。
▪具体参数设置： 
request.required.acks:设置为-1 等待所有ISR列表中的Replica接收到消息后采算写成功。 
min.insync.replicas: 设置为>=2,保证ISR中至少两个Replica。 
.kafka支持Gzip/snappy等多种压缩方式

## Exactly Once方案-自己保持offset
官方文档提供方案
1. 使用关系数据库，通过事务存取，如果consumer挂了，重启，消息也不会重复消费;
2. 搜索引擎：把offset和数据一起，建在索引里
tips:在消费端"Exactly Once"前提下，即使生产端有"重复发送"，也可以通过DB的offset判重来实现producer+consumer的"Exactly Once".
**自己保存offset前提条件是**
Configure enable.auto.commit=false   //禁用自动ack
Use the offset provided with each ConsumerRecord to save your position. //每次取到消息，把对应的offset存下来
On restart restore the position of the consumer using seek(TopicPartition, long).//下次重启，通过consumer.seek函数，定位到自己保存的offset，从那开始消费


# KAFKA STREAM
//todo
https://blog.csdn.net/CRISPY_RICE/article/details/79354144