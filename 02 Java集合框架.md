### 集合框架底层数据结构总结

**Collection**

List：存储一组不唯一（可重复），有序的对象。

- Arraylist： Object数组
- Vector： Object数组
- LinkedList： 双向链表(JDK1.6之前为循环链表，JDK1.7取消了循环) 

Set：不允许重复的集合

- HashSet（无序，唯一）: 基于 HashMap 实现的，底层采用 HashMap 来保存元素
- LinkedHashSet： LinkedHashSet 继承 HashSet，并且其内部是通过 LinkedHashMap 来实现的。
- TreeSet（有序，唯一）： 红黑树(自平衡的排序二叉树)

Map：使用键值对存储，典型的Key是String类型，但也可以是任何对象

- HashMap： JDK1.8之前HashMap由数组+链表组成的，数组是HashMap的主体，链表则是主要为了解决哈希冲突而存在的。JDK1.8以后在解决哈希冲突时有了较大的变化，当链表长度大于阈值（默认为8）时，将链表转化为红黑树，以减少搜索时间
- LinkedHashMap： LinkedHashMap 继承自 HashMap，所以它的底层仍然是基于拉链式散列结构即由数组和链表或红黑树组成。另外，LinkedHashMap 在上面结构的基础上，增加了一条双向链表，使得上面的结构可以保持键值对的插入顺序
- Hashtable： 数组+链表组成的，数组是 Hashtable的主体，链表则是主要为了解决哈希冲突而存在的
- TreeMap： 红黑树（自平衡的排序二叉树）

### 如何选用集合

主要根据集合的特点来选用，比如我们需要根据键值获取到元素值时就选用Map接口下的集合，需要排序时选择TreeMap，不需要排序时就选择HashMap，需要保证线程安全就选用ConcurrentHashMap。当我们只需要存放元素值时，就选择实现Collection接口的集合，需要保证元素唯一时选择实现Set接口的集合比如TreeSet或HashSet，不需要就选择实现List接口的比如ArrayList或LinkedList，然后再根据实现这些接口的集合的特点来选用。

### ArrayList vs Vector 

Vector类的所有方法都是**同步**的，可以由多个线程安全地访问一个Vector对象，但是要在同步操作上耗费大量的时间。

Arraylist**不是同步**的，在不需要保证线程安全时时建议使用Arraylist。

### Arraylist vs LinkedList

- 是否保证线程安全： ArrayList 和 LinkedList 都是不同步的，**都不保证线程安全**
- 底层数据结构： Arraylist 底层使用的是**Object数组**；LinkedList 底层使用的是**双向链表**数据结构（JDK1.6之前为循环链表，JDK1.7取消了循环）
- 插入和删除时间复杂度是否受元素位置的影响： ArrayList 采用数组存储，LinkedList 采用链表存储；**链表插入，删除元素时间复杂度不受元素位置的影响，都是近似 O（1）而数组近似 O（n）**
- 是否支持快速随机访问： 快速随机访问就是通过元素的序号快速获取元素对象(对应于get(int index)方法)，**LinkedList 不支持，而 ArrayList 支持**
- 内存空间占用： ArrayList的空 间浪费主要体现在在list列表的结尾会预留一定的容量空间，而LinkedList的空间花费则体现在它的每一个元素要存放直接后继和直接前驱以及数据

### RandomAccess接口

```java
public interface RandomAccess{}
```

 RandomAccess 接口中什么都没有定义，RandomAccess 接口只是一个标识， 标识实现这个接口的类具有随机访问功能（通过元素的序号快速获取元素对象）。

ArrayList 实现了 RandomAccess 接口， 而 LinkedList 没有实现。

### list 的遍历方式选择

- 实现了RandomAccess接口的list，优先选择普通for循环 ，其次foreach
- 未实现RandomAccess接口的ist， 优先选择iterator（迭代器）遍历（foreach遍历底层也是通过iterator实现的），大size的数据，千万不要使用普通for循环

### HashSet如何检查重复

当把对象加入HashSet时，HashSet会先计算对象的hashcode值来判断对象加入的位置，同时也会与其他对象的hashcode值作比较，如果没有相符的hashcode，HashSet会假设对象没有重复出现。但是如果发现有相同hashcode值的对象，这时会调用equals（）方法来检查hashcode相等的对象是否真的相同。如果两者相同，HashSet就不会让加入操作成功。

###  HashSet vs HashMap

HashSet 底层就是基于 HashMap 实现的。HashSet 除了 clone()、writeObject()、readObject()是 HashSet 自己不得不实现之外，其他方法都是直接调用 HashMap 中的方法。

| **HashSet**                     | **HashMap**                      |
| ------------------------------- | -------------------------------- |
| 实现Set接口                     | 实现Map接口                      |
| 仅存储对象                      | 存储键值对                       |
| 调用 add（）向Set中添加元素     | 调用 put（）向map中添加元素      |
| HashSet使用成员对象计算hashcode | HashMap使用键（Key）计算Hashcode |

### HashMap vs Hashtable 

1. 线程是否安全： HashMap 是非线程安全的，HashTable 是线程安全的；HashTable 内部的方法基本都经过synchronized 修饰。（如果要保证线程安全可以使用 ConcurrentHashMap）；
2. 效率： 因为线程安全的问题，HashMap 要比 HashTable 效率高一点。另外，HashTable 基本被淘汰，不要在代码中使用它；
3. 对Null key 和Null value的支持： HashMap 中null 可以作为键，这样的键只有一个，可以有一个或多个键所对应的值为 null。但是在 HashTable 中 null不能作为键；
4. 初始容量大小和每次扩充容量大小的不同 ： ①创建时如果不指定容量初始值，Hashtable 默认的初始大小为11，之后每次扩充容量变为原来的2n+1。HashMap 默认的初始化大小为16，之后每次扩充容量变为原来的2倍。②创建时如果给定了容量初始值，那么 Hashtable 会直接使用给定的大小，而 HashMap 会将其扩充为2的幂次方大小，也就是说 HashMap 总是使用2的幂作为哈希表的大小。
5. 底层数据结构： JDK1.8 以后的 HashMap 在解决哈希冲突时有了较大的变化，当链表长度大于阈值（默认为8）时，将链表转化为红黑树，以减少搜索时间。Hashtable 没有这样的机制。

### ConcurrentHashMap线程安全的底层实现

**JDK1.7的ConcurrentHashMap：**

![](D:\pic\markdown\面试题总结\02 Java集合框架\5FDD59D305E740C1B90B872BF12703F7.png)

首先将数据分为一段一段的存储，然后给每一段数据配一把锁，当一个线程占用锁访问其中一个段数据时，其他段的数据也能被其他线程访问。

一个 ConcurrentHashMap 里包含一个 Segment 数组。Segment 的结构和HashMap类似，是一种数组和链表结构，一个 Segment 包含一个 HashEntry 数组，每个 HashEntry 是一个链表结构的元素，每个 Segment 守护着一个HashEntry数组里的元素，当对 HashEntry 数组的数据进行修改时，必须首先获得对应的 Segment的锁。

Segment 实现了 ReentrantLock，所以 Segment 是一种可重入锁。HashEntry 用于存储键值对数据。

```java
static class Segment<K,V> extends ReentrantLock implements Serializable {}
```

**JDK1.8的ConcurrentHashMap**（TreeBin: 红黑二叉树节点 Node: 链表节点）：

![](D:\pic\markdown\面试题总结\02 Java集合框架\3AD7080E8B404BF8AA7CF5F322D6375F.png)

取消了Segment分段锁，采用CAS和synchronized来保证并发安全。数据结构跟HashMap1.8的结构类似，数组+链表/红黑二叉树。在链表长度超过一定阈值（8）时将链表（寻址时间复杂度为O(N)）转换为红黑树（寻址时间复杂度为O(log(N))）。

synchronized只锁定当前链表或红黑二叉树的首节点，这样只要hash不冲突，就不会产生并发，效率又提升N倍。

### ConcurrentHashMap vs Hashtable 

区别主要体现在实现线程安全的方式上不同。

**底层数据结构：** 

JDK1.7的 ConcurrentHashMap 底层采用 **分段的数组+链表** 实现，JDK1.8 采用的数据结构跟HashMap1.8的结构一样，数组+链表/红黑二叉树。

Hashtable 采用 **数组+链表** 的形式，数组是 Hashtable  的主体，链表主要为了解决哈希冲突而存在；

**实现线程安全的方式（重要）：**

 ① **在JDK1.7的时候，ConcurrentHashMap（分段锁）** 对整个桶数组进行了分割分段(Segment)，每一把锁只锁容器其中一部分数据，多线程访问容器里不同数据段的数据，就不会存在锁竞争，提高并发访问率。 **到了 JDK1.8 的时候已经摒弃了Segment的概念，而是直接用 Node 数组+链表+红黑树的数据结构来实现，并发控制使用 synchronized 和 CAS 来操作。整个看起来就像是优化过且线程安全的 HashMap，虽然在JDK1.8中还能看到 Segment 的数据结构，但是已经简化了属性，只是为了兼容旧版本；**

② **Hashtable(同一把锁)** 使用 synchronized 来保证线程安全，效率非常低下。当一个线程访问同步方法时，其他线程也访问同步方法，可能会进入阻塞或轮询状态，如使用 put 添加元素，另一个线程不能使用 put 添加元素，也不能使用 get，竞争会越激烈效率越低。

HashTable

![](D:\pic\markdown\面试题总结\02 Java集合框架\7C499FAFE70B49C68711C34F91D96CF3.png)

### comparable vs Comparator

- comparable接口实际上是出自java.lang包，它有一个 compareTo(Object obj)方法用来排序
- comparator接口实际上是出自 java.util，包它有一个compare(Object obj1, Object obj2)方法用来排序

