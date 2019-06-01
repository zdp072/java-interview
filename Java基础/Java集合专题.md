## ArrayList的特点
实现了List接口，底层实现为数组，超出长度会动态进行扩容，其实就是对数组进行复制；在日常使用时最好指定大小，尽量减少扩容。

## ArrayList是如何进行扩容的
ArrayList扩容的本质就是计算出新的扩容数组的size后实例化，并将原有数组内容复制到新数组中去，容量会扩展为原来的1.5倍

## ArrayList线程不安全有什么解决办法
1. new Vector();
2. Collections.synchronizedList(new ArrayList());
3. new CopyOnWriteArrayList();

## Vector的特点
实现了List接口，底层数据结构和ArrayList类似，也是一个动态数组，但是它在调用add方法的时候使用了synchronized进行同步，开销较大，所以Vector是一个同步容器，并不是一个并发容器。

## LinkedList的特点
实现了List接口，底层基于双向链表实现。插入或删除只需要操作前后节点引用，所以效率很高；查找需要进行遍历，效率较低。

## HashMap的特点
1）特点
底层是数组+链表实现，底层为非synchronized，所以性能较高。

2）工作原理
```
简化后数据模型：
Node[] node = new Node[16]; // 散列桶初始化
class Node {
    hash; //hash值
    key; //键
    value; //值
    node next; //用于指向链表的下一层（产生冲突，用拉链法）
}
```
往map里put一个元素的过程：对key求hashcode得到数组的下标，如果没有碰撞，则直接放入数组中；如果碰撞了，则以链表的方式链接到后面，如果链表的长度超过阈值（8），就把链表转成红黑树。如果节点已经存在就替换旧值，如果数组满了，则扩容两倍。
HashMap是一个线程不安全的容器，主要体现在发生扩容时，出现环形链表从而导致死循环。

3）为什么hashmap的get、put方法不是线程安全的？
多线程的场景下，put元素有可能会被覆盖。

## 为什么HashMap中链表长度超过8会转换成红黑树
HashMap在jdk1.8之后引入了红黑树的概念，表示若桶中链表元素超过8时，会自动转化成红黑树；若桶中元素小于等于6时，树结构会还原成链表形式，转换成树后查找相对更快。

## HashMap如何进行扩容
当hashmap中的元素个数超过数组大小乘于loadFactor时，就会进行数组扩容，loadFactor的默认值为0.75，也就是说，默认情况下，数组大小为16，那么当hashmap中元素个数超过16 x 0.75=12的时候，就把数组的大小扩展为2*16=32，即扩大一倍，然后重新计算每个元素在数组中的位置，而这是一个非常消耗性能的操作，所以如果我们已经预知hashmap中元素的个数，那么预设元素的个数能够有效的提高hashmap的性能。

## HashMap线程不安全体现在哪
HashMap初始容量为16，当有数据要插入时，都会检查容量有没有超过设定值，如果超过则需要增大Hash表的尺寸，这样一来，整个Hash表里的元素都需要被重算一遍，这叫rehash，这个成本很高。

## ConcurentHashMap的特点
首先将数据分成一段一段的存储（Segment数组），然后给每一段数据（hashMap）配一把锁，当一个线程占用锁访问其中一个段数据的时候，其他段的数据也能被其他线程访问。

ConcurrentHashMap默认将hash表分为16个桶，诸如get、put、remove等常用操作只锁住当前需要用到的桶。这样，原来只能一个线程进入，现在却能同时有16个写线程执行，并发性能的提升是显而易见的。

## HashSet的特点
HashSet是一个不允许存储重复元素的集合，它几乎全部借助于HashMap来实现，只使用HashMap的key来实现各种特性，所以HashMap会出现的问题，HashSet也无法避免。HashSet的add方法底层调用HashMap的add方法，key为add的值，value为一个object常量。

## LinkedHashMap的特点
HashMap是一个无序的Map，LinkedHashMap是有序的，它是由一个双向链表来实现。

