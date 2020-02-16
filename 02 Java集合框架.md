### 集合框架底层数据结构总结	

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

### 去掉 Vector 重复的元素

```java
Vector newVector =new Vector(); 
for(int i =0; i < vector.size(); i++){
    Object obj = vector.get(i);
    if(!newVector.contains(obj)){
        newVector.add(obj);
    }
}
// or
HashSet set =new HashSet(vector);
```

### Array vs ArrayList

1. Array只能存储同构的对象，而ArrayList可以存储异构的对象
2. Array 大小是固定的，ArrayList 的大小是动态变化的
3. ArrayList 提供了更多的方法和特性，比如：addAll()，removeAll()，iterator() 等，而 Array 只能一次获取或设置一个元素值

>同构的对象是指类型相同的对象，若声明为int[]的数组就只能存放整形数据，string[]只能存放字符型数据，但声明为object[]的数组除外。而ArrayList可以存放任何不同类型的数据，因为它里面存放的都是被装箱了的Object型对象，实际上ArrayList内部就是使用"object[] _items"这样一个私有字段来封装对象的。

### Vector vs  ArrayList

都是有序集合、允许元素重复。

Vector类的所有方法都是**同步**的，可以由多个线程安全地访问一个Vector对象，但是要在同步操作上耗费大量的时间。

Arraylist**不是同步**的，在不需要保证线程安全时时建议使用Arraylist。

Vector 扩容时增长为原来的两倍，而 ArrayList 扩容时增长为原容量的1.5倍。

### Arraylist vs LinkedList

- 是否保证线程安全： ArrayList 和 LinkedList 都是不同步的，**都不保证线程安全**
- 底层数据结构： Arraylist 底层使用的是**Object数组**；LinkedList 底层使用的是**双向链表**数据结构（JDK1.6之前为循环链表，JDK1.7取消了循环）
- LinkedList 在插入和删除数据时效率更高，ArrayList 在查找数据时效率更高
- 是否支持快速随机访问： 快速随机访问就是通过元素的序号快速获取元素对象(对应于get(int index)方法)，**LinkedList 不支持，而 ArrayList 支持**
- LinkedList 比 ArrayList 需要更多的内存，ArrayList的空间浪费主要体现在在list列表的结尾会预留一定的容量空间，而LinkedList的空间花费则体现在它的每一个元素要存放直接后继和直接前驱以及数据

### ArrayList线程不安全示例

```java
public void testList() {
    List<Integer> list = new ArrayList<>();
    Random random = new Random();
    for (int i = 0; i < 100; i++) {
        new Thread(() -> {
            list.add(random.nextInt(10));
            System.out.println(list);
        }).start();
    }
}
//并发修改导致异常 java.util.ConcurrentModificationException
//解决方案
//new Vector();
//new CopyOnWriteArrayList<>();
//Collections.synchronizedList(new ArrayList<>());
```

### CopyOnWriteArrayList 实现原理

从 `CopyOnWriteArrayList` 的名字就能看出`CopyOnWriteArrayList` 是满足`CopyOnWrite` 的 ArrayList，所谓`CopyOnWrite` 也就是说：在计算机，如果你想要对一块内存进行修改时，我们不在原有内存块中进行写操作，而是将内存拷贝一份，在新的内存中进行写操作，写完之后呢，就将指向原来内存指针指向新的内存，原来的内存就可以被回收掉了。

`CopyOnWriteArrayList` 类的所有可变操作（add，set  等等）都是通过创建底层数组的新副本来实现的。当 List  需要被修改的时候，我并不修改原有内容，而是对原有数据进行一次复制，将修改的内容写入副本。写完之后，再将修改完的副本替换原来的数据，这样就可以保证写操作不会影响读操作了。

```java
public boolean add(E e) {
    final ReentrantLock lock = this.lock;
    lock.lock();//加锁
    try {
        Object[] elements = getArray();
        int len = elements.length;
        Object[] newElements = Arrays.copyOf(elements, len + 1);//拷贝新数组
        newElements[len] = e;
        setArray(newElements);
        return true;
    } finally {
        lock.unlock();//释放锁
    }
}
```

### RandomAccess接口

```java
public interface RandomAccess{}
```

 RandomAccess 接口中什么都没有定义，RandomAccess 接口只是一个标识， 标识实现这个接口的类具有随机访问功能（通过元素的序号快速获取元素对象）。

ArrayList 实现了 RandomAccess 接口， 而 LinkedList 没有实现。

### list 的遍历方式选择

- 实现了RandomAccess接口的list，优先选择普通for循环 ，其次foreach
- 未实现RandomAccess接口的ist， 优先选择iterator（迭代器）遍历（foreach遍历底层也是通过iterator实现的），大size的数据，千万不要使用普通for循环

### 有序数组 vs 无序数组

有序数组最大的好处在于查找的时间复杂度是O(log n)，而无序数组是O(n)。

有序数组的缺点是插入操作的时间复杂度是O(n)，因为值大的元素需要往后移动来给新元素腾位置。相反，无序数组的插入时间复杂度是常量O(1)。

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

### HashMap 的长度为什么是2的幂次方

当我们根据key的hash值确定其在数组的位置时，如果n为2的幂次方，可以保证数据的均匀插入，如果n不是2的幂次方，可能数组的一些位置永远不会插入数据，浪费数组的空间，加大hash冲突。

### ConcurrentHashMap线程安全的底层实现

**JDK1.7的ConcurrentHashMap：**

<img src="C:\Users\WANG\AppData\Roaming\Typora\typora-user-images\5FDD59D305E740C1B90B872BF12703F7.png" style="zoom:20%;" />

首先将数据分为一段一段的存储，然后给每一段数据配一把锁，当一个线程占用锁访问其中一个段数据时，其他段的数据也能被其他线程访问。

一个 ConcurrentHashMap 里包含一个 Segment 数组。Segment 的结构和HashMap类似，是一种数组和链表结构，一个 Segment 包含一个 HashEntry 数组，每个 HashEntry 是一个链表结构的元素，每个 Segment 守护着一个HashEntry数组里的元素，当对 HashEntry 数组的数据进行修改时，必须首先获得对应的 Segment的锁。

Segment 实现了 ReentrantLock，所以 Segment 是一种可重入锁。HashEntry 用于存储键值对数据。

```java
static class Segment<K,V> extends ReentrantLock implements Serializable {}
```

**JDK1.8的ConcurrentHashMap**（TreeBin: 红黑二叉树节点 Node: 链表节点）：

![](C:\Users\WANG\AppData\Roaming\Typora\typora-user-images\3AD7080E8B404BF8AA7CF5F322D6375F.png)

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

<img src="C:\Users\WANG\AppData\Roaming\Typora\typora-user-images\7C499FAFE70B49C68711C34F91D96CF3.png" style="zoom: 40%;" />

### HashMap常见问题

1. 谈谈你理解的 HashMap，讲讲其中的 get put 过程。
2. 1.8 做了什么优化？
3. 是线程安全的嘛？
4. 不安全会导致哪些问题？
5. 如何解决？有没有线程安全的并发容器？
6. ConcurrentHashMap 是如何实现的？ 1.7、1.8 实现有何不同？为什么这么做？

### List、Set和Map的初始容量和加载因子

ArrayList的初始容量是10，加载因子为0.5； 扩容增量：原容量的 1.5倍；一次扩容后长度为15。

Vector初始容量为10，加载因子是1；扩容增量：原容量的 1倍，如 Vector的容量为10，一次扩容后是容量为20。

HashSet，初始容量为16，加载因子为0.75； 扩容增量：原容量的 1 倍，如 HashSet的容量为16，一次扩容后容量为32。

HashMap，初始容量16，加载因子为0.75； 扩容增量：原容量的 1 倍， 如 HashMap的容量为16，一次扩容后容量为32

### comparable vs Comparator

- comparable接口是出自java.lang包，它有一个 compareTo(Object obj)方法用来排序
- comparator接口是出自 java.util，包它有一个compare(Object obj1, Object obj2)方法用来排序

两种方法各有优劣,，用Comparable 简单，只要实现Comparable 接口的对象直接就成为一个可以比较的对象，但是需要修改源代码。 

用Comparator 的好处是不需要修改源代码,，而是另外实现一个比较器,，当某个自定义的对象需要作比较的时候，把比较器和对象一起传递过去就可以比大小了，并且在Comparator 里面用户可以自己实现复杂的可以通用的逻辑，使其可以匹配一些比较简单的对象。

### 集合的快速失败机制 “fail-fast”

是java集合的一种错误检测机制，当多个线程对集合进行结构上的改变的操作时，有可能会产生 fail-fast 机制。

例如：假设存在两个线程，线程1通过Iterator在遍历集合A中的元素，在某个时候线程2修改了集合A的结构（是结构上面的修改，而不是简单的修改集合元素的内容），那么这个时候程序就会抛出 ConcurrentModificationException 异常，从而产生fail-fast机制。

原因：迭代器在遍历时直接访问集合中的内容，并且在遍历过程中使用一个 modCount 变量。集合在被遍历期间如果内容发生变化，就会改变modCount的值。每当迭代器使用hashNext()/next()遍历下一个元素之前，都会检测modCount变量是否为expectedmodCount值，是的话就返回遍历；否则抛出异常，终止遍历。

解决办法：

- 在遍历过程中，所有涉及到改变modCount值得地方全部加上synchronized。
- 使用CopyOnWriteArrayList来替换ArrayList。

## 非阻塞队列 ConcurrentLinkedQueue 

一个基于链接节点的无界线程安全队列。此队列按照 FIFO（先进先出）原则对元素进行排序。队列的头部 是队列中时间最长的元素。队列的尾部 是队列中时间最短的元素。新的元素插入到队列的尾部，队列获取操作从队列头部获得元素。当多个线程共享访问一个公共 collection 时，ConcurrentLinkedQueue 是一个恰当的选择。此队列不允许使用 null 元素。

## 阻塞队列 BlockingQueue

阻塞队列（BlockingQueue）被广泛使用在“生产者-消费者”问题中，其原因是 BlockingQueue 提供了可阻塞的插入和移除的方法。当队列容器已满，生产者线程会被阻塞，直到队列未满；当队列容器为空时，消费者线程会被阻塞，直至队列非空时为止。

BlockingQueue 是一个接口，继承自 Queue，所以其实现类也可以作为 Queue 的实现来使用，而 Queue 又继承自 Collection 接口。

### ArrayBlockingQueue

ArrayBlockingQueue 是 BlockingQueue 接口的有界队列实现类，底层采用**数组**来实现。ArrayBlockingQueue 一旦创建，容量不能改变。其并发控制采用可重入锁来控制，不管是插入操作还是读取操作，都需要获取到锁才能进行操作。当队列容量满时，尝试将元素放入队列将导致操作阻塞;尝试从一个空队列中取一个元素也会同样阻塞。[代码示例](https://crossoverjie.top/JCSprout/#/thread/ArrayBlockingQueue)

ArrayBlockingQueue 默认情况下不能保证线程访问队列的公平性。如果保证公平性，通常会降低吞吐量。如果需要获得公平性的  ArrayBlockingQueue，可采用如下代码： 

```java
private static ArrayBlockingQueue<Integer> blockingQueue = new ArrayBlockingQueue<Integer>(10,true);
```

### LinkedBlockingQueue 

**LinkedBlockingQueue** 底层基于**单向链表**实现的阻塞队列，可以当做无界队列也可以当做有界队列来使用，同样满足 FIFO 的特性，与 ArrayBlockingQueue 相比起来具有更高的吞吐量，为了防止 LinkedBlockingQueue  容量迅速增大，损耗大量内存，通常在创建 LinkedBlockingQueue 对象时，会指定其大小，如果未指定，容量等于  Integer.MAX_VALUE。

```java
/**
 *某种意义上的无界队列
 */
public LinkedBlockingQueue() {
    this(Integer.MAX_VALUE);
}
/**
 *有界队列
 */
public LinkedBlockingQueue(int capacity) {
    if (capacity <= 0) throw new IllegalArgumentException();
    this.capacity = capacity;
    last = head = new Node<E>(null);
}
```

### PriorityBlockingQueue

**PriorityBlockingQueue** 是一个支持优先级的无界阻塞队列。默认情况下元素采用自然顺序进行排序，也可以通过自定义类实现 `compareTo()` 方法来指定元素排序规则，或者初始化时通过构造器参数 `Comparator` 来指定排序规则。

PriorityBlockingQueue 并发控制采用的是 **ReentrantLock**，队列为无界队列（ArrayBlockingQueue 是有界队列，LinkedBlockingQueue 也可以通过在构造函数中传入 capacity 指定队列最大的容量，但是  PriorityBlockingQueue 只能指定初始的队列大小，后面插入元素的时候，**如果空间不够的话会自动扩容**）。

简单地说，它就是 PriorityQueue 的线程安全版本。不可以插入 null 值，同时，插入队列的对象必须是可比较大小的（comparable），否则报  ClassCastException 异常。它的插入操作 put 方法不会 block，因为它是无界队列（take  方法在队列为空的时候会阻塞）。

