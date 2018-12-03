# 第1节：List

# ![](C:\Users\dell\Desktop\gitbook\Collection\Collection.png)

狭义的集合框架如上图所示，collection是所有集合的根，扩展开来提供三大类

（1）List  有序集合提供方便的访问插入删除操作

（2）Set Set不允许重复元素，也就不存在两个对象equals为true

（3）Queue 标准队列实现，除了集合功能之外还提供先进先出和先进后出的特定操作

1.List

（1）Vector是线程安全数组，采用Synchronized锁来保证线程安全，内部采用数组保存对象，数组满的时候会创建新的数组，并拷贝原有数组数据，扩容的容量是100%。初始化时会创建一个容量为10的object数组，并将capacityIncrement设置为0，当插入元素数组大小不够时，如果capacityIncreament大于0，Object数组容量扩容为size+capatityIncrement，否则扩容一倍

  (2)ArrayList是线程不安全的动态数组，扩容时容量增加50%，扩容时调用System.arraycopy（）方法，删除元素不会减少容量，调用trimToSize（）方法可以减少容量

（3）LinkList是双向链表，线程不安全，也不需要扩容，增删元素速度快，检索慢

2.Set

(1)HashSet是利用哈希算法，在不考虑碰撞的情况下，正常散列，可以提供常数时间的添加删除包含操作，但是不能保证有序，其实现也是以HashMap为基础是实现的

（2）TreeSet是利用TreeMap实现的，通过java类库创建一个Dummy对象临时对象作为值，将插入元素以键的形式放入TreeMap里面，其支持自然顺序的访问，，但删除添加包含操作的时间复杂度为logn

（3）LinkHashSet内部构建了一个记录插入删除的双向链表，所以能够按照插入顺序遍历，也能保证常数时间的添加删除包含操作，因为要维护链表操作的开销，性能低于HashSet

3排序

提供了Arrays.sort()和Collections.sort()(底层调用Arrays.sort)，排序算法的选择根据数据类型和数据量进行选择

（1）原始数据类型使用双轴快速排序（Dual-Pivot QuickSort），是一种改进的快速排序算法，源码见代码包

（2）对象类型的数据类型，则采用TimSort排序，思想上二分插入排序和归并排序，通过查找数据中已经排好序的分区，然后合并已经排好序的分区来实现的排序。源码见代码包

4.java 8 新特性

支持Lambda，Stream,Spliterator，parallerSort，支持集合创建相应的steam或者parallelStream的方法实现，和遍历时的并行迭代器，排序时的并行排序，底层利用fork—join框架，利用现代多核处理器的计算能，在处理万和百万以上数据时性能提升巨大

例如Collcetion OpenJDK中截取的源码如下

     default Spliterator<E> spliterator() {
        return Spliterators.spliterator(new Collection[]{this}, 0);
    }
    default Stream<E> stream() {
        return StreamSupport.stream(spliterator(), false);
    }
    default Stream<E> parallelStream() {
        return StreamSupport.stream(spliterator(), true);
    }


