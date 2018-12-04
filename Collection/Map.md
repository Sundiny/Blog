# 第2节：Map

![map](C:\Users\dell\Desktop\gitbook\Collection\map.png)

Map集合框架图如上图所示，Map集合提供三大类

1 HashTable

key 和value均不能为null值，元素的排列顺序时无序的，初始化容量为11，扩容时容量为两倍加1，且不要求底层数组数组容量为2 的整数幂。

HashTable其方法函数都是同步的（采用synchronized修饰），不会出现两个线程同时对数据进行操作的情况，因此保证了线程安全性。也正因为如此，在多线程运行环境下效率表现非常低下。因为当一个线程访问HashTable的同步方法时，其他线程也访问同步方法就会进入阻塞状态。比如当一个线程在添加数据时候，另外一个线程即使执行获取其他数据的操作也必须被阻塞，大大降低了程序的运行效率，在新版本中已被废弃，不推荐使用。

分析：HashTable的key 和value为什么不能为null

HashTable是支持并发编程的，当key为null的，通过get（key）获得的value的值也是null，这样以来你就无法判断这个value的null是key为null时对应的null值，还是这个key没有进行hash而得到的null值。如果通过调用contains（key）或者get（key）来判断是否key，因为是支持并发编程的，那个调用对象可能已经被修改了

HashMap是不支持并发编程的，可以通过contains（key）是否包含key，因此调用对象不会改变，所以调用contains（）方法就可以判断，因此key和value都可以是null

2 TreeMap

TreeMap中当未实现 Comparator 接口时，key 不可以为null；当实现 Comparator 接口时，若未对null情况进行判断，则key不可以为null，反之亦然。

TreeMap是利用红黑树来实现的（树中的每个节点的值，都会大于或等于它的左子树种的所有节点的值，并且小于或等于它的右子树中的所有节点的值），实现了SortMap接口，能够对保存的记录根据键进行排序。所以一般需要排序的情况下是选择TreeMap来进行，默认为升序排序方式（深度优先搜索），可自定义实现Comparator接口实现排序方式。

3 HashMap

![Hashmap](C:\Users\dell\Desktop\gitbook\Collection\Hashmap.png)

内部结构如上图，其存储结构可以看做是数组加链表组成的符合存储结构，数组被分成一个一个桶（bucket），通过哈希值决定了键值对在这个数组的寻址；哈希值相同的键值对，则以链表形式存储，如果链表大小超过阈值（TREEIFY_THRESHOLD, 8），链表会发生树化。树化是会出现无线占用cpu问题和size（）不准的问题。HashMap默认容量为16，且要求容量一定为2的整数次幂，HashMap扩容将容量变为原来的2倍。HashMap不支持线程的同步，即任一时刻可以有多个线程同时写HashMap;可能会导致数据的不一致。如果需要同步可以用 Collections的synchronizedMap方法。

HashMap基于哈希思想，实现对数据的读写。当我们将键值对传递给put()方法时，它调用键对象的hashCode()方法来计算hashcode，让后找到bucket位置来储存值对象。当获取对象时，通过键对象的equals()方法找到正确的键值对，然后返回值对象。HashMap使用链表来解决碰撞问题，当发生碰撞了，对象将会储存在链表的下一个节点中。 HashMap在每个链表节点中储存键值对对象。当两个不同的键对象的hashcode相同时，它们会储存在同一个bucket位置的链表中，可通过键对象的equals()方法用来找到键值对。

其中hashcode和equals方法有一些约定：

（1）equals相等hashcode也相等

（2）重写hashcode也要重写equals

（3）hashCode 需要保持一致性，状态改变返回的哈希值仍然要一致

（4）equals 的对称、反射、传递等特性

HashMap初始化容量是16 最大容量是2的30次幂，负载因子是0.75

（1）初始化容量为16的原因

话不多说看源码中给的提示：

```
static final int hash(Object key) {
    int h;
    return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
}
```



在计算key.hashCode()并传播(XORs)更高的散列位，降低仅在当前掩码之上的位上，变化的散列将会改变总是发生碰撞，所以我们应用一个变换来分散更高位的影响向下。这是jdk源码注释源码注释翻译的意思。总而言之通过过合适的初始化容量和负载因子合理的取值，来让哈希表更好的散列。

而源码注释也间接告诉了为什么取值是16，HashMap中通过h&(length-1)的方法来代替取模，length为2的整数次幂的话，h&(length-1)就相当于对length取模，这样便保证了散列的均匀，同时也提升了效率；其次，length为2的整数次幂的话，为偶数，这样length-1为奇数，奇数的最后一位是1，这样便保证了h&(length-1)的最后一位可能为0，也可能为1（这取决于h的值），即与后的结果可能为偶数，也可能为奇数，这样便可以保证散列的均匀性，而如果length为奇数的话，很明显length-1为偶数，它的最后一位是0，这样h&(length-1)的最后一位肯定为0，即只能为偶数，这样任何hash值都只会被散列到数组的偶数下标位置上，这便浪费了近一半的空间，因此，length取2的整数次幂，是为了使不同hash值发生碰撞的概率较小，这样就能使元素在哈希表中均匀地散列

（2）负载因子是0.75的原因

源码给出的解释如下，理想情况下，在随机hashCodes情况下下，频率为区间中的节点遵循泊松分布带有默认大小调整的平均参数约为0.5，阈值为0.75，虽然因为有很大的差异调整粒度。 忽略方差，预期列表大小k的出现是（exp（-0.5）* pow（0.5，k）/factorial（k））。（http://en.wikipedia.org/wiki/Poisson_distribution）

在理想情况下，使用随机哈希吗，节点出现的频率在hash桶中遵循泊松分布，同时给出了桶中元素的个数和概率的对照表。从上表可以看出当桶中元素到达8个的时候，概率已经变得非常小，也就是说用0.75作为负载因子，每个碰撞位置的链表长度超过8个是几乎不可能的。

（3）链表存储树化

final void treeifyBin(Node<K,V>[] tab, int hash) {
​    int n, index; Node<K,V> e;
​    if (tab == null || (n = tab.length) < MIN_TREEIFY_CAPACITY)
​        resize();
​    else if ((e = tab[index = (n - 1) & hash]) != null) {
​        // 树化改造逻辑
​    }
}

树化的代码如上

本质上这是个安全问题。因为在元素放置过程中，如果一个对象哈希因为在元素放置过程中，如果一个对象哈希冲突，都被放置到同一个都被放置到同一个桶里，则会形成一个链表，我们知道链表查询是线则会形成一个链表，我们知道链表查询是线性的，会严重影响存取的性能，而在现实世界，构造哈希冲突的数据并不是非常复杂的事情，恶意代码利用这些数据与服务器进行交互，大量占用cpu资源，引起cpu占用百分之百，size（）不准的问题

HashMap在并发问题的如下（https://bugs.java.com/bugdatabase/view_bug.do?bug_id=6423457）

产生的原因分析（https://mailinator.blogspot.com/2009/06/beautiful-race-condition.html）或者书耗子叔的这篇文章（https://coolshell.cn/articles/9606.html）

总而言之这个问题的产生是本身由链表的存储结构造成的，链表成环引发了循环引用，而在HashMap扩容的的时候元素，扩容之后的元素的位置发生了改变，在单线程的情况下是没有问题的，而在多线程的情况下，对位置的操作顺序是无法控制的，因而造成了成环，引发了死锁。具体的过程可以看上面的两篇文章。









​      

