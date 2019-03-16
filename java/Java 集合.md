容器主要包括 Collection 和 Map 两种，Collection 存储着对象的集合，而 Map 存储着键值对（两个对象）的映射表。
## 1) Collection
1.  Set 
* TreeSet：基于红黑树实现，支持有序性操作，例如根据一个范围查找元素的操作。但是查找效率不如 HashSet，HashSet 查找的时间复杂度为 O(1)，TreeSet 则为 O(logN)。
* HashSet：基于哈希表实现，支持快速查找，但不支持有序性操作。并且失去了元素的插入顺序信息，也就是说使用 Iterator 遍历 HashSet 得到的结果是不确定的。
* LinkedHashSet：具有 HashSet 的查找效率，且内部使用双向链表维护元素的插入顺序。

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
* HashTable：和 HashMap 类似，但它是线程安全的，这意味着同一时刻多个线程可以同时写入 HashTable 并且不会导致数据不一致。它是遗留类，不应该去使用它。现在可以使用**ConcurrentHashMap** 来支持线程安全，并且 ConcurrentHashMap 的效率会更高，因为 ConcurrentHashMap 引入了分段锁。
* LinkedHashMap：使用双向链表来维护元素的顺序，顺序为插入顺序或者最近最少使用（LRU）顺序。

tips:accessOrder:决定了顺序，默认为 false，此时维护的是插入顺序。
accessOrder 为 true时:当一个节点被访问时，则会将该节点移到链表尾部。(也就是说指定为 LRU 顺序之后，在每次访问一个节点时，会将这个节点移到链表尾部，保证链表尾部是最近访问的节点，那么链表首部就是最近最久未使用的节点。)


 ## Treemap
[详解](https://www.cnblogs.com/chenssy/p/3746600.html)
**TreeMap的实现是红黑树算法的实现;**

1. TreeMap实现继承于AbstractMap，并且实现了NavigableMap接口。
2. TreeMap的本质是R-B Tree(红黑树)，它包含几个重要的成员变量： root, size, comparator。
   - root 是红黑数的根节点。它是Entry类型，Entry是红黑数的节点，它包含了红黑数的6个基本组成成分：key(键)、value(值)、left(左孩子)、right(右孩子)、parent(父节点)、color(颜色)。Entry节点根据key进行排序，Entry节点包含的内容为value。
   - 红黑数排序时，根据Entry中的key进行排序；Entry中的key比较大小是根据比较器**comparator**来进行判断的。
   - size是红黑数中节点的个数。

### put()
>> 在TreeMap的put()的实现方法中主要分为两个步骤，第一：构建排序二叉树，第二：平衡二叉树(fixAfterInsertion())。
对于排序二叉树的创建，其添加节点的过程如下：
1. 以根节点为初始节点进行检索。
2. 与当前节点进行比对，若新增节点值较大，则以当前节点的右子节点作为新的当前节点。否则以当前节点的左子节点作为新的当前节点。
3. 循环递归2步骤知道检索出合适的叶子节点为止。
4. 将新增节点与3步骤中找到的节点进行比对，如果新增节点较大，则添加为右子节点；否则添加为左子节点。

### delete()

### descendingMap()
反向TreeMap

## ConcurrentHashMap
- ConcurrentHashMap  http://ifeve.com/hashmap-concurrenthashmap-相信看完这篇没人能难住你%ef%bc%81/

* JDK 1.7 使用分段锁机制来实现并发更新操作，核心类为 Segment，它继承自重入锁 ReentrantLock，并发度与 Segment 数量相等。
* JDK 1.8 核心类为Node，使用了 CAS 操作来支持更高的并发度，在 CAS 操作失败时使用内置锁 synchronized。
并且 JDK 1.8 的实现也在链表过长时会转换为**红黑树**。

LinkedHashMap
