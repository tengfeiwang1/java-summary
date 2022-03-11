- [概念](#概念)
- [原子操作](#原子操作)
  - [CAS](#cas)
- [多线程](#多线程)
  - [多线程开发良好的实践](#多线程开发良好的实践)
- [并发包(J.U.C)](#并发包juc)
  - [AQS--AbstractQueuedSynchronizer](#aqs--abstractqueuedsynchronizer)
    - [框架](#框架)
    - [AQS源码详解](#aqs源码详解)
  - [JUC下的同步器](#juc下的同步器)
  - [并发集合---concurrent](#并发集合---concurrent)
  - [原子类--Atomic](#原子类--atomic)
  - [BlockingQueue--阻塞队列](#blockingqueue--阻塞队列)
  - [ThreadLocal](#threadlocal)
    - [原理](#原理)

# 概念
  - 串行
  就是按照顺序一个个执行任务。

  - 并行
  指两个或两个以上事件或任务在同一时刻发生。（每个CPU运行一个任务）

  - 并发
   是在同一个cpu(线路)上同时（不是真正的同时，而是看来是同时，因为cpu要在多个程序间切换）运行多个任务。(一个CPU(线路)运行多个任务)

# 原子操作
  原子操作是指一个不受其他操作影响的操作任务单元。原子操作是在多线程环境下避免数据不一致必须的手段。

## CAS
  CAS（Compare and swap）比较和替换是设计并发算法时用到的一种技术。

- 底层实现:
    使用Unsafe类，都是native方法，unsafe底层实现是汇编的cmpxchg 实现，处理器执行cmpxchg是原子性操作（**如果是多处理器，也需要对cmpxchg操作使用lock指令加锁**）。
    
    CAS基于硬件实现，不需要进入内核，不需要切换线程，操作自旋几率较少，因此可以获得更高的性能。

- ABA问题-AtomicStampedReference

  AtomicStampedReference原子类是一个带有时间戳的对象引用，在每次修改后，AtomicStampedReference不仅会设置新值而且还会记录更改的时间。
  当AtomicStampedReference设置对象值时，对象值以及时间戳都必须满足期望值才能写入成功，这也就解决了反复读写时，无法预知值是否已被修改的窘境。

# 多线程

[线程池参考"线程模块"](./线程.md)


## 多线程开发良好的实践
 1. 给线程起个有意义的名字，这样可以方便找 Bug。
 2. 缩小同步范围，从而减少锁争用。例如对于 synchronized，应该尽量使用同步块而不是同步方法。
 3. 多用同步工具少用 wait() 和 notify()。
   首先，CountDownLatch, CyclicBarrier, Semaphore 和 Exchanger 这些同步类简化了编码操作，而用 wait() 和 notify() 很难实现复杂控制流；
   其次，这些同步类是由最好的企业编写和维护，在后续的 JDK 中还会不断优化和完善。
 4. 使用 BlockingQueue 实现生产者消费者问题。
 5. 多用并发集合少用同步集合，例如应该使用 ConcurrentHashMap 而不是 Hashtable。
 6. 使用本地变量和不可变类来保证线程安全。
 7. 使用线程池而不是直接创建线程，这是因为创建线程代价很高，线程池可以有效地利用有限的线程来启动任务。

# 并发包(J.U.C)
![参考文档](./pri/知识点20181126.docx)
## AQS--AbstractQueuedSynchronizer
AQS = state + CLH队列

[参考基础资料](https://github.com/Snailclimb/JavaGuide/blob/main/docs/java/concurrent/aqs.md)
[尚硅谷面试第三季整理的笔记](https://iridescent-english-b5d.notion.site/2021-Java-java-dc6997437ba3471c8d92f0165ec333c0#c34b3b39abc24c028b38617c7c0cf3c9)

### 框架
![CLH队列(FIFO)](./pic/CLH队列(FIFO).png)
  它维护了一个**volatile int state（代表共享资源）**和一个FIFO线程等待队列（多线程争用资源被阻塞时会进入此队列）。这里volatile是核心关键词，具有volatile的语义，在此不述。state的访问方式有三种:
    
    getState()
    setState()
    compareAndSetState()

  >> state状态的值：

    CANCELLED，值为1，表示当前的线程被取消；
    SIGNAL，值为-1，表示当前节点的后继节点包含的线程需要运行，也就是unpark；
    CONDITION，值为-2，表示当前节点在等待condition，也就是在condition队列中；
    PROPAGATE，值为-3，表示当前场景下后续的acquireShared能够得以执行；
    值为0，表示当前节点在sync队列中，等待着获取锁。

 - AQS定义两种资源共享方式：Exclusive（独占，只有一个线程能执行，如ReentrantLock）和Share（共享，多个线程可同时执行，如Semaphore/CountDownLatch）。

- 不同的自定义同步器争用共享资源的方式也不同。自定义同步器在实现时只需要实现共享资源state的获取与释放方式即可，至于具体线程等待队列的维护（如获取资源失败入队/唤醒出队等），AQS已经在顶层实现好了。自定义同步器实现时主要实现以下几种方法：

    - isHeldExclusively()：该线程是否正在独占资源。只有用到condition才需要去实现它。

    - tryAcquire(int)：独占方式。尝试获取资源，成功则返回true，失败则返回false。(这个方法的实现需要查询当前状态是否允许获取，然后再进行获取（使用compareAndSetState来做）状态。)

    - tryRelease(int)：独占方式。尝试释放资源，成功则返回true，失败则返回false。

    - tryAcquireShared(int)：共享方式。尝试获取资源。负数表示失败；0表示成功，但没有剩余可用资源；正数表示成功，且有剩余资源。

    - tryReleaseShared(int)：共享方式。尝试释放资源，如果释放后允许唤醒后续等待结点返回true，否则返回false。

  -  以ReentrantLock为例，state初始化为0，表示未锁定状态。A线程lock()时，会调用tryAcquire()独占该锁并将state+1。此后，其他线程再tryAcquire()时就会失败，直到A线程unlock()到state=0（即释放锁）为止，其它线程才有机会获取该锁。当然，释放锁之前，A线程自己是可以重复获取此锁的（state会累加），这**就是可重入的概念**。但要注意，获取多少次就要释放多么次，这样才能保证state是能回到零态的。

  - 再以CountDownLatch以例，任务分为N个子线程去执行，state也初始化为N（注意N要与线程个数一致）。这N个子线程是并行执行的，每个子线程执行完后countDown()一次，state会CAS减1。等到所有子线程都执行完后(即state=0)，会unpark()主调用线程，然后主调用线程就会从await()函数返回，继续后余动作。

　　一般来说，自定义同步器要么是独占方法，要么是共享方式，他们也只需实现tryAcquire-tryRelease、tryAcquireShared-tryReleaseShared中的一种即可。但AQS也支持自定义同步器同时实现独占和共享两种方式，如ReentrantReadWriteLock。

[CLH和MCS](https://www.cnblogs.com/doit8791/p/9098188.html)

### AQS源码详解
[AQS源码分析参考链接](http://www.cnblogs.com/waterystone/p/4920797.html)



>> LockSupport是AQS的底层线程实现类
- todo 查看视频，记录笔记

[[AQS源码解析](https://www.bilibili.com/video/BV1Hy4y1B78T?p=16)

## JUC下的同步器
  1. ReentrantLock-独享模式
  2. ReentrantReadWriteLock-独享、共享模式交叉
  3. CountDownLatch-共享模式
  4. Semaphore(信号量)-共享模式


## 并发集合---concurrent
http://ifeve.com/concurrent-collections-1/

    阻塞集合：这种集合包括添加和删除数据的操作。如果操作不能立即进行，是因为集合已满或者为空，该程序将被阻塞，直到操作可以进行。
    非阻塞集合：这种集合也包括添加和删除数据的操作。如果操作不能立即进行，这个操作将返回null值或抛出异常，但该线程将不会阻塞。

  - 非阻塞队列：
  
    ConcurrentHashMap:多个并发线程同时使用同一个HashMap对象时，会出现“程序假死”状态，**因为HashMap是线程不安全的，不能被多个线程所操作**，而**HashTable虽然是线程安全的**，但是在**多线程iterator()循环中调用remove()时会报异常**，解决办法就是使用ConcurrentHashMap 代替。
    ConcurrentSkipListMap:ConcurrentHashMap不支持排序，**而ConcurrentSkipListMap是ConcurrentHashMap的可排序版本**。
    ConcurrentSkipListSet:支持排序且不允许重复的元素
    ConcurrentLinkedQueue:提供**并发环境的队列操作**，常用方法：poll()、element()、peek()、add()
    ConcurrentLinkedDeque:**ConcurrentLinkedQueue 仅支持表头操作，ConcurrentLinkedDeque 支持队列头和队列尾双向操作**
    CopyOnWriteArrayList:ArrayList是非线程安全类，如果想在并发中实现安全，需要使用CopyOnWriteArrayList，**CopyOnWriteArrayList 可以在循环中删除元素**
    CopyOnWriteArraySet:HashSet的线程安全实现

  - 阻塞队列：
　　ArrayBlockingQueue    有界阻塞队列
　　PriorityBlockingQueue    并发情况下的有限队列
　　LinkedBlockingQueue    与 ArrayBlockingQueue 功能一致，但是 LinkedBlockingQueue 是无界的
　　LinkedBlockingDueue    LinkedBlockingQueue 的双向队列版本
　　SynchronousQueue    同步阻塞队列，每个插入操作必须等待另一个线程的对应移除操作，反之亦然。
　　DelayQueue    延时执行任务的队列
　　LinkedTransferQueue    提供与 SynchronousQueue 类似的功能，但具有嗅探功能，可以尝试性地添加一些数据
   
   
  >> tips:非阻塞列表，使用ConcurrentLinkedDeque类。
    阻塞列表，使用LinkedBlockingDeque类。
    用在生产者与消费者数据的阻塞列表，使用LinkedTransferQueue类。
    使用优先级排序元素的阻塞列表，使用PriorityBlockingQueue类。
    存储延迟元素的阻塞列表，使用DelayQueue类。
    非阻塞可导航的map，使用ConcurrentSkipListMap类。
    随机数，使用ThreadLocalRandom类
    原子变量，使用AtomicLong和AtomicIntegerArray类

- ConcurrentHashMap  http://ifeve.com/hashmap-concurrenthashmap-相信看完这篇没人能难住你%ef%bc%81/
[参考Java集合](./Java 集合.md)


## 原子类--Atomic
http://ifeve.com/concurrent-collections-8/

  - AtomicInteger：volatile value计算，cas更新数据，效率低于LongAdder
  
    //在同一个代码块中使用两个或以上atomic变量操作，会导致原子性丢失
    同一个代码块中，对单个atomic变量进行2次或2次以上的操作，会导致原子性丢失
  - LongAdder(JDK 1.8): cells[]分段计算，依赖cas,最后sum汇总，高并发时效率最高
    add方法会动态扩展：根据并发数量/线程数量进行扩展，并且动态扩容
https://www.cnblogs.com/rickzhai/p/7936758.html
[LongAdder源码](https://blog.csdn.net/zqz_zqz/article/details/70665941)

## BlockingQueue--阻塞队列
  它主要用于实现生产者-消费者问题。
java.util.concurrent.BlockingQueue 接口有以下阻塞队列的实现：
* FIFO 队列 ：LinkedBlockingQueue、ArrayBlockingQueue（固定长度）

**ArrayBlockingQueue**: 基于数组的阻塞队列实现；
内部是通过一个可重入锁ReentrantLock和两个Condition条件对象来实现阻塞，所以ArrayBlockingQueue中的元素存在公平访问与非公平访问的区别

**LinkedBlockingQueue**: 一个由链表实现的有界队列阻塞队列，需要设置长度，不然默认值为Integer.MAX_VALUE会造成机器负载或者内存爆满等情况。
链表队列的吞吐量要高于基于数组的队列（ArrayBlockingQueue），因为其内部实现添加和删除操作使用的两个ReentrantLock(putLock,takeLock)来控制并发执行，而ArrayBlockingQueue内部只是使用一个ReenterLock控制并发，因此LinkedBlockingQueue的吞吐量要高于ArrayBlockingQueue.

**PriorityBlockingQueue**: 优先级队列 

## ThreadLocal
每一个ThreadLocal能够放一个线程级别的变量，可是它本身能够被多个线程共享使用，并且又能够达到线程安全的目的，且绝对线程安全。
### 原理
[ThreadLocal](./pic/ThreadLocal.png)

ThreadLocalMap对象在Thread里面作为私有的变量而存在，所以是线程安全的。
ThreadLocal通过Thread.currentThread()获取当前的线程就能得到这个Map对象。同一时候将自身作为Key发起写入和读取，因为将自身作为Key，所以一个ThreadLocal对象就能存放一个线程中相应的Java对象。通过get也自然能找到这个对象。
