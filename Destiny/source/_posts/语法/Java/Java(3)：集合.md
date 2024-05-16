

Java 集合，也叫作容器，主要是由两大接口派生而来：一个是 `Collection`接口，主要用于存放单一元素；另一个是 `Map` 接口，主要用于存放键值对。对于`Collection` 接口，下面又有三个主要的子接口：`List`、`Set` 、 `Queue`。
![image.png](https://cdn.jsdelivr.net/gh/destiny0118/picgo/pic2023/202405140957507.png)
- `List`(对付顺序的好帮手): 存储的元素是有序的、可重复的。
- `Set`(注重独一无二的性质): 存储的元素不可重复的。
- `Queue`(实现排队功能的叫号机): 按特定的排队规则来确定先后顺序，存储的元素是有序的、可重复的。
- `Map`(用 key 来搜索的专家): 使用键值对（key-value）存储，类似于数学上的函数 y=f(x)，"x" 代表 key，"y" 代表 value，key 是无序的、不可重复的，value 是无序的、可重复的，每个键最多映射到一个值。


List
- ArrayList
- vector
- LinkedList
Set
- HashSet
- LinkedHashSet
- TreeSet
Queue
- PriorityQueue
- DelayQueue
- ArrayDeque
Map
- HashMap：线程不安全
- LinkedHashMap
- Hashtable：线程安全
- TreeMap

`HashSet`，`TreeSet`，`ArrayList`,`LinkedList`,`HashMap`,`TreeMap` 都是线程不安全的。

# Set
## Comparable和Comparator
`Comparable` 接口和 `Comparator` 接口都是 Java 中用于排序的接口，它们在实现类对象之间比较大小、排序等方面发挥了重要作用：

- `Comparable` 接口实际上是出自`java.lang`包 它有一个 `compareTo(Object obj)`方法用来排序
- `Comparator`接口实际上是出自 `java.util` 包它有一个`compare(Object obj1, Object obj2)`方法用来排序

## Hashset、LinkedHashSet、TreeSet
- `HashSet`、`LinkedHashSet` 和 `TreeSet` 的主要区别在于底层数据结构不同。`HashSet` 的底层数据结构是哈希表（基于 `HashMap` 实现）。`LinkedHashSet` 的底层数据结构是链表和哈希表，元素的插入和取出顺序满足 FIFO。`TreeSet` 底层数据结构是红黑树，元素是有序的，排序的方式有自然排序和定制排序。
- 底层数据结构不同又导致这三者的应用场景不同。`HashSet` 用于不需要保证元素插入和取出顺序的场景，`LinkedHashSet` 用于保证元素的插入和取出顺序满足 FIFO 的场景，`TreeSet` 用于支持对元素自定义排序规则的场景。

# Map

## HashMap和TreeMap
**相比于`HashMap`来说， `TreeMap` 主要多了对集合中的元素根据键排序的能力以及对集合内元素的搜索的能力。**


## HashMap
### 扩容机制

[HashMap的扩容机制 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/114363420)
[HashMap实现原理， 扩容机制，面试题和总结_hashmap扩容机制面试-CSDN博客](https://blog.csdn.net/qq_49217297/article/details/126304736)
### HashMap多线程导致死循环
当一个桶位中有多个元素需要进行扩容时，多个线程同时对链表进行操作，`头插法`可能会导致链表中的节点指向错误的位置，从而形成一个环形链表，进而使得查询元素的操作陷入死循环无法结束。
为了解决这个问题，JDK1.8 版本的 HashMap 采用了尾插法而不是头插法来避免链表倒置，使得插入的节点永远都是放在链表的末尾，避免了链表中的环形结构。

[疫苗：Java HashMap的死循环 | 酷 壳 - CoolShell](https://coolshell.cn/articles/9606.html)

### HashMap为什么线程不安全
多线程环境下，`HashMap` 扩容时会造成`死循环` 和`数据丢失`的问题。
在 `HashMap` 中，多个键值对可能会被分配到同一个桶（bucket），并以链表或红黑树的形式存储。多个线程对`HashMap`的put操作会导致线程不安全，可能产生数据覆盖.

- 两个线程同时进行put操作，并且存在哈希冲突
- 由于线程首先都执行完了hash碰撞的判断，桶为空
- 每个线程再向空桶中插入元素
```java
public V put(K key, V value) {
    return putVal(hash(key), key, value, false, true);
}

final V putVal(int hash, K key, V value, boolean onlyIfAbsent,
                   boolean evict) {
    // ...
    // 判断是否出现 hash 碰撞
    // (n - 1) & hash 确定元素存放在哪个桶中，桶为空，新生成结点放入桶中(此时，这个结点是放在数组中)
    if ((p = tab[i = (n - 1) & hash]) == null)
        tab[i] = newNode(hash, key, value, null);
    // 桶中已经存在元素（处理hash冲突）
    else {
    // ...
}
```
多个线程同时put操作导致size不正确
- 两个线程都先获取size，在++size
- 添加两次元素，而size只增加1
```java
public V put(K key, V value) {
    return putVal(hash(key), key, value, false, true);
}

final V putVal(int hash, K key, V value, boolean onlyIfAbsent,
                   boolean evict) {
    // ...
    // 实际大小大于阈值则扩容
    if (++size > threshold)
        resize();
    // 插入后回调
    afterNodeInsertion(evict);
    return null;
}

```

[基于JDK8的HashMap实现(万字详解) - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/484653028)

### 底层实现
JDK1.8 之前 `HashMap` 底层是 **数组和链表** 结合在一起使用也就是 **链表散列**。HashMap 通过 key 的 `hashcode` 经过扰动函数处理过后得到 hash 值，然后通过 `(n - 1) & hash` 判断当前元素存放的位置（这里的 n 指的是数组的长度），如果当前位置存在元素的话，就判断该元素与要存入的元素的 hash 值以及 key 是否相同，如果相同的话，直接覆盖，不相同就通过拉链法解决冲突。

所谓扰动函数指的就是 HashMap 的 `hash` 方法。使用 `hash` 方法也就是扰动函数是为了防止一些实现比较差的 `hashCode()` 方法 换句话说使用扰动函数之后可以减少碰撞。


相比于之前的版本， JDK1.8 之后在解决哈希冲突时有了较大的变化，当链表长度大于阈值（默认为 8）（将链表转换成红黑树前会判断，如果当前数组的长度小于 64，那么会选择先进行数组扩容，而不是转换为红黑树）时，将链表转化为红黑树，以减少搜索时间。



## ConcurrentHashMap

### 1.7
>通过分段锁（Segmentation）实现线程安全。它将整个哈希表分成多个段（Segment），每个段都是一个小的哈希表，并且拥有自己的锁。这样，多个线程可以并发地访问不同的段，从而减少了锁的竞争，提高了并发性能。 


![image.png](https://cdn.jsdelivr.net/gh/destiny0118/picgo/pic2023/202404012148392.png)

### 1.8
>使用了一种更细粒度的锁策略，结合CAS（Compare-and-Swap）无锁算法来实现线程安全。在JDK 1.8中，ConcurrentHashMap将整个哈希表看作一个整体，不再进行分段。而是通过Node数组+链表+红黑树的结构来存储数据，并使用Synchronized和CAS来协调并发访问。  

锁粒度更细，`synchronized` 只锁定当前链表或红黑二叉树的首节点，这样只要 hash 不冲突，就不会产生并发，就不会影响其他 Node 的读写，效率大幅提升。
![image.png](https://cdn.jsdelivr.net/gh/destiny0118/picgo/pic2023/202404012149783.png)

### JDK 1.7 和 JDK 1.8 的 ConcurrentHashMap 实现有什么不同？
- **线程安全实现方式**：JDK 1.7 采用 `Segment` 分段锁来保证安全， `Segment` 是继承自 `ReentrantLock`。JDK1.8 放弃了 `Segment` 分段锁的设计，采用 `Node + CAS + synchronized` 保证线程安全，锁粒度更细，`synchronized` 只锁定当前链表或红黑二叉树的首节点。
- **Hash 碰撞解决方法** : JDK 1.7 采用拉链法，JDK1.8 采用拉链法结合红黑树（链表长度超过一定阈值时，将链表转换为红黑树）。
- **并发度**：JDK 1.7 最大并发度是 Segment 的个数，默认是 16。JDK 1.8 最大并发度是 Node 数组的大小，并发度更大。



[ConcurrentHashMap 源码分析 | JavaGuide](https://javaguide.cn/java/collection/concurrent-hash-map-source-code.html#_4-get)
[一文彻底搞懂CAS实现原理 & 深入到CPU指令 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/94976168)
[Java集合常见面试题总结(下) | JavaGuide](https://javaguide.cn/java/collection/java-collection-questions-02.html#map-%E9%87%8D%E8%A6%81)