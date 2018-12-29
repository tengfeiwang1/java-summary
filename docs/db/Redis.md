
### Redis
介绍：高性能的key-value数据库。
Redis 与其他 key - value 缓存产品有以下三个特点：
 Redis支持数据的持久化，可以将内存中的数据保存在磁盘中，重启的时候可以再次加载进行使用。 
 Redis不仅仅支持简单的key-value类型的数据，同时还提供list，set，zset，hash等数据结构的存储。
 Redis支持数据的备份，即master-slave模式的数据备份。

持久化方式RDB，AOF：
AOF会消耗一部分性能，但是可以提高缓存一致性
RDS 提高频繁写性能，不启用备份来换取性能（可以通过save命令做备份）

1.解决redis aof文件过大的问题 
执行BGREWRITEAOF命令对redis的AOF进行重写
redis-cli BGREWRITEAOF

AOF重写作用：
(1) 随着AOF文件越来越大，里面会有大部分是重复命令或者可以合并的命令（100次incr = set key 100）
(2) 重写的好处：减少AOF日志尺寸，减少内存占用，加快数据库恢复时间。
