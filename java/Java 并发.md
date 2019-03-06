- [原子操作](#%E5%8E%9F%E5%AD%90%E6%93%8D%E4%BD%9C)
- [多线程](#%E5%A4%9A%E7%BA%BF%E7%A8%8B)
- [3.并发包](#3%E5%B9%B6%E5%8F%91%E5%8C%85)
  - [3. 1 J.U.C](#3-1-juc)
    - [AQS--AbstractQueuedSynchronizer](#aqs--abstractqueuedsynchronizer)
    - [并发集合---concurrent](#%E5%B9%B6%E5%8F%91%E9%9B%86%E5%90%88---concurrent)
    - [原子性--Atomic](#%E5%8E%9F%E5%AD%90%E6%80%A7--atomic)
  - [3. 2 BlockingQueue--阻塞队列](#3-2-blockingqueue--%E9%98%BB%E5%A1%9E%E9%98%9F%E5%88%97)
- [4.synchronized关键字](#4synchronized%E5%85%B3%E9%94%AE%E5%AD%97)
- [5.volatile关键字](#5volatile%E5%85%B3%E9%94%AE%E5%AD%97)
  - [多线程开发良好的实践](#%E5%A4%9A%E7%BA%BF%E7%A8%8B%E5%BC%80%E5%8F%91%E8%89%AF%E5%A5%BD%E7%9A%84%E5%AE%9E%E8%B7%B5)


# 原子操作
吧
# 多线程
# 3.并发包
## 3. 1 J.U.C
![参考文档](./pri/知识点20181126.docx)
### AQS--AbstractQueuedSynchronizer
http://www.cnblogs.com/waterystone/p/4920797.html


CLH和MCS mcs:https://www.cnblogs.com/doit8791/p/9098188.html
### 并发集合---concurrent
//todo
### 原子性--Atomic
//todo

CountDownLatch

信号量-Semaphore


## 3. 2 BlockingQueue--阻塞队列

java.util.concurrent.BlockingQueue 接口有以下阻塞队列的实现：
* FIFO 队列 ：LinkedBlockingQueue、ArrayBlockingQueue（固定长度）

**ArrayBlockingQueue**: 基于数组的阻塞队列实现；
内部是通过一个可重入锁ReentrantLock和两个Condition条件对象来实现阻塞，所以ArrayBlockingQueue中的元素存在公平访问与非公平访问的区别

**LinkedBlockingQueue**: 一个由链表实现的有界队列阻塞队列，需要设置长度，不然默认值为Integer.MAX_VALUE会造成机器负载或者内存爆满等情况。
链接队列的吞吐量要高于基于数组的队列（ArrayBlockingQueue），因为其内部实现添加和删除操作使用的两个ReentrantLock(putLock,takeLock)来控制并发执行，而ArrayBlockingQueue内部只是使用一个ReenterLock控制并发，因此LinkedBlockingQueue的吞吐量要高于ArrayBlockingQueue.

**PriorityBlockingQueue**: 优先级队列 


http://www.cnblogs.com/hapjin/p/5492880.html

## 多线程开发良好的实践
给线程起个有意义的名字，这样可以方便找 Bug。
缩小同步范围，从而减少锁争用。例如对于 synchronized，应该尽量使用同步块而不是同步方法。
多用同步工具少用 wait() 和 notify()。首先，CountDownLatch, CyclicBarrier, Semaphore 和 Exchanger 这些同步类简化了编码操作，而用 wait() 和 notify() 很难实现复杂控制流；其次，这些同步类是由最好的企业编写和维护，在后续的 JDK 中还会不断优化和完善。
使用 BlockingQueue 实现生产者消费者问题。
多用并发集合少用同步集合，例如应该使用 ConcurrentHashMap 而不是 Hashtable。
使用本地变量和不可变类来保证线程安全。
使用线程池而不是直接创建线程，这是因为创建线程代价很高，线程池可以有效地利用有限的线程来启动任务。