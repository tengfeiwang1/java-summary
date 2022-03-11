容器主要包括 Collection 和 Map 两种，Collection 存储着对象的集合，而 Map 存储着键值对（两个对象）的映射表。
## 1) Collection
1.  Set 
* TreeSet：基于红黑树实现，支持有序性操作，例如根据一个范围查找元素的操作。但是查找效率不如 HashSet，HashSet 查找的时间复杂度为 O(1)，TreeSet 则为 O(logN)。
* HashSet：基于哈希表实现，支持快速查找，但不支持有序性操作。并且失去了元素的插入顺序信息，也就是说使用 Iterator 遍历 HashSet 得到的结果是不确定的。
* LinkedHashSet：具有 HashSet 的查找效率(底层调用的LinkedHashMap)，且内部使用双向链表维护元素的插入顺序。

2.  List
* ArrayList：基于动态数组实现，支持随机访问。
* Vector：和 ArrayList 类似，但它是线程安全的。
* LinkedList：基于双向链表实现，只能顺序访问，但是可以快速地在链表中间插入和删除元素。不仅如此，LinkedList 还可以用作栈、队列和双向队列。

3.  Queue
* LinkedList：可以用它来实现双向队列。
* PriorityQueue：基于堆结构实现，可以用它来实现优先队列。
  

## 2) Map
* TreeMap：基于红黑树实现。
* HashMap：基于哈希表实现。
* HashTable：和 HashMap 类似，但它是线程安全的，这意味着同一时刻多个线程可以同时写入 HashTable 并且不会导致数据不一致。它是遗留类，不应该去使用它。现在可以使用**ConcurrentHashMap** 来支持线程安全，并且 ConcurrentHashMap 的效率会更高，因为 ConcurrentHashMap 引入了分段锁(jdk 1.7)。
* LinkedHashMap：使用**双向链表**来维护元素的顺序，顺序为插入顺序或者最近最少使用（**LRU**）顺序。

>> tips:accessOrder:决定了顺序，默认为 false，此时维护的是插入顺序。
accessOrder 为 true时:当一个节点被访问时，则会将该节点移到链表尾部。(也就是说指定为 LRU 顺序之后，在每次访问一个节点时，会将这个节点移到链表尾部，保证链表尾部是最近访问的节点，那么链表首部就是最近最久未使用的节点。)


## Treemap
[详解](https://www.cnblogs.com/chenssy/p/3746600.html)
**TreeMap的实现是红黑树算法的实现;**

1. TreeMap实现继承于AbstractMap，并且实现了NavigableMap(导航(范围查询)，有序)接口。
2. TreeMap的本质是R-B Tree(红黑树)，它包含几个重要的成员变量： root, size, comparator。
   - root 是红黑树的根节点。它是Entry类型，Entry是红黑树的节点，它包含了红黑树的6个基本组成成分：key(键)、value(值)、left(左孩子)、right(右孩子)、parent(父节点)、color(颜色)。Entry节点根据key进行排序，Entry节点包含的内容为value。
   - 红黑树排序时，根据Entry中的key进行排序；Entry中的key比较大小是根据比较器**comparator**来进行判断的。
   - size是红黑数中节点的个数。

### put()
>> 在TreeMap的put()的实现方法中主要分为两个步骤，第一：构建排序二叉树，第二：平衡二叉树(fixAfterInsertion())。
对于排序二叉树的创建，其添加节点的过程如下：
1. 以根节点为初始节点进行检索。
2. 与当前节点进行比对，若新增节点值较大，则以当前节点的右子节点作为新的当前节点。否则以当前节点的左子节点作为新的当前节点。
3. 循环递归2步骤直到检索出合适的叶子节点为止。
4. 将新增节点与3步骤中找到的节点进行比对，如果新增节点较大，则添加为右子节点；否则添加为左子节点。

### delete()

### descendingMap()
反向TreeMap

## HashMap实现原理
[参考](https://blog.csdn.net/hefenglian/article/details/79763634)
在JDK1.6，JDK1.7中，HashMap采用**位桶(数组)+链表**实现，即使用链表处理冲突,同一hash值的键值对会被放在同一个位桶里，当桶中元素较多时，通过key值查找的效率较低。

而JDK1.8中，HashMap采用**位桶+链表+红黑树**实现，当链表长度超过阈值（8）,时，将链表转换为红黑树，这样大大减少了查找时间。

## ConcurrentHashMap
- ConcurrentHashMap  http://ifeve.com/hashmap-concurrenthashmap-相信看完这篇没人能难住你%ef%bc%81/
[ConcurrentHashMap相信看完这篇没人能难住你](./pic/ConcurrentHashMap相信看完这篇没人能难住你.png)

上边图片看不清楚，这里使用pdf
https://cloud.tencent.com/developer/article/1188023
[ConcurrentHashMap相信看完这篇没人能难住你](./pic/ConcurrentHashMap相信看完这篇没人能难住你.pdf)

https://www.cnblogs.com/zhaojj/p/8942647.html

* JDK 1.7 使用分段锁机制来实现并发更新操作，核心类为 Segment，它继承自重入锁 ReentrantLock，并发度与 Segment 数量相等。
* JDK 1.8 核心类为Node，使用了 CAS 操作来支持更高的并发度，在 CAS 操作失败时使用内置锁 synchronized。
并且 JDK 1.8 的实现也在链表过长时会转换为**红黑树**。


### ConcurrentHashMap -get,put
   - put
根据 key 计算出 hashcode 。
判断是否需要进行初始化。
f 即为当前 key 定位出的 Node，如果为空表示当前位置可以写入数据，利用 **CAS 尝试写入**，失败则自旋保证成功。
如果当前位置的 hashcode == MOVED == -1,则需要进行扩容。
如果都不满足，则利用 **synchronized 锁**写入数据。
如果数量大于 TREEIFY_THRESHOLD 则要**转换为红黑树**。

   - get
根据计算出来的 hashcode 寻址，如果就在桶上那么直接返回值。
如果是红黑树那就按照树的方式获取值。
都不满足那就按照链表的方式遍历获取值。

### HashMap -get,put
   - GET
1. 首先将 key hash 之后取得所定位的桶。
2. 如果桶为空则直接返回 null 。
3. 否则判断桶的第一个位置(有可能是链表、红黑树)的 key 是否为查询的 key，是就直接返回 value。
4. 如果第一个不匹配，则判断它的下一个是红黑树还是链表。
5. 红黑树就按照树的查找方式返回值。
6. 不然就按照链表的方式遍历匹配返回值。

   - PUT
1. 判断当前桶是否为空，空的就需要初始化（resize 中会判断是否进行初始化）。
2. 根据当前 key 的 hashcode 定位到具体的桶中并判断是否为空，为空表明没有 Hash 冲突就直接在当前位置创建一个新桶即可。
3. 如果当前桶有值(**Hash 冲突**)，那么就要比较当前桶中的 key、key 的 hashcode 与写入的 key 是否相等，相等就赋值给 e,在4第 8 步的时候会统一进行赋值及返回。
4. 如果当前桶为红黑树，那就要按照红黑树的方式写入数据。
5. 如果是个链表，就需要将当前的 key、value 封装成一个新节点写入到当前桶的后面（形成链表）。
6. 接着判断当前链表的大小是否大于预设的阈值，大于时就要转换为红黑树(并平衡)。
7. 如果在遍历过程中找到 key 相同时直接退出遍历。
8. 如果 e != null 就相当于存在相同的 key,那就需要将值覆盖。
9. 最后判断是否需要进行扩容。



## AQS--必考重点 [参考Java并发--AQS]
