## MySQL常用引擎
1. InnoDB
   InnoDB的存储文件有两个，后缀分别是.frm和.idb，其中.frm是表的定义文件，而.idb是数据文件。
   InnoDB中存在**表锁和行锁**，不过行锁是在命中索引的情况下才会起作用。
   InnoDB**支持事务**，且支持四种隔离级别（读未提交，读已提交，可重复读，串行化），默认的为可重复读；而在Oracle数据库中，只支持串行化和读已提交这两种级别，其中默认为读已提交级别。
>> InnoDB支持行锁（锁定字段含有索引的情况下，否则走表锁），但锁定方式并非简单的锁定指定行上的索引，而是分为3种锁定算法：
1）记录锁（Record Locks）：锁定指定行的索引项
2）Gap Locks：锁定某一个范围内的索引，但不包括记录本身
3）间隙锁定（Next-Key Locks）：锁定一个范围内的索引，并且锁定记录本身   Next-Key Locks = Record Locks + Gap Locks
A next-key lock is a combination of a record lock on the index record and a gap lock on the gap before the index record

 避免幻读：
 1. 使用一种next-key-locking的策略来避免幻读;
 2. MVCC:多版本并发控制

2. Myisam
   Myisam的存储文件有三个，后缀分别是.frm, .MYD, .MYI，其中.frm是表的定义文件，.MYD是数据库文件，.MYI是索引文件。
   Myisam**只支持表锁**，且**不支持事务**。Myisam由于有单独的索引文件，在读取数据方面的性能很高。

## 存储结构
InnoDB和Myisam都是用B+Tree来存储数据的。

### InnoDB索引结构

### Myisam索引结构


范式

事务
优化