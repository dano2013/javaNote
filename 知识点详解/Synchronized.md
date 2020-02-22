### 并发编程的三个特性

**可见性**：指当一个线程对共享变量进行了修改，那么另外的线程可以立即看到修改后的最新值。

**原子性**：在一次或多次操作中，要么所有的操作都执行并且不会中断，要么所有的操作都不执行。

**有序性**：指程序代码在执行过程中的先后顺序，由于java在编译期以及运行期的优化，导致了代码的执行顺序未必就是开发者编写代码时的顺序。

### 硬件内存架构和Java内存模型

**硬件内存架构**

- CPU：中央处理器，是计算机的控制和运算的核心，我们的程序最终都会变成指令让CPU去执行。

- 内存：我们的程序都是在内存中运行的，内存会保存程序运行时的数据，供CPU处理。

- 缓存：CPU的运算速度和内存的访问速度相差比较大，这就导致CPU每次操作内存都要消耗很多等待时间。内存的读写速度成为了计算机运行的瓶颈。于是就有了CPU和主内存之间增加缓存的设计。最靠近CPU的缓存称为L1，然后依次是L2，L3和主内存。

**Java内存模型**

Java memory model，不要和Java内存结构混淆。

Java内存模型是一套在多线程读写共享数据时，对共享数据的可见性、有序性、和原子性的规则和保障，描述了Java程序中各种变量（线程共享变量）的访问规则，以及在JVM中将变量存储到内存和从内存中读取变量这样的底层细节。Java内存模型是标准化的，屏蔽掉了底层不同计算机的区别。

**主内存**：主内存是所有线程都共享的，都能访问的。所有的共享变量都存储于主内存。

**工作内存**：每一个线程有自己的工作内存，工作内存只存储该线程对共享变量的副本。线程对变量的所有的操作(读，取）都必须在工作内存中完成，而不能直接读写主内存中的变量，不同线程之间也不能直接访问对方工作内存中的变量。

多线程的执行最终都会映射到硬件处理器上进行执行，但Java内存模型和硬件内存架构并不完全一致。对于硬件内存来说只有寄存器、缓存內存、主内存的概念，并没有工作内存和主内存之分，也就是说Java内存模型对内存的划分对硬件内存并没有任何影响，因为JMM只是一种抽象的概念，是一组规则，不管是工作内存的数据还是主内存的数据，对于计算机硬件来说都会存储在计算机主内存中，当然也有可能存储到CPU缓存或者寄存器中，因此总体上来说，Java内存模型和计算机硬件内存架构是一个相互交叉的关系，是一种抽象概念划分与真实物理硬件的交叉。

![img](C:\Users\WANG\Documents\YoudaoNote\m18588930828@163.com\730c97c294514b1ea545664117b23f13\clipboard.png)

### 主内存与工作内存之间的交互

Java内存模型中定义了以下8种操作来完成，主内存与工作内存之间具体的交互协议，即一个变量如何从主内存拷贝到工作内存、如何从工作内存同步回主内存之类的实现细节，虚拟机实现时必须保证下面提及的每一种操作都是原子的、不可再分的。

![img](C:\Users\WANG\Documents\YoudaoNote\m18588930828@163.com\806811ec9c604f85bbd3b63435f31ae3\clipboard.png)

1.如果对一个变量执行lock操作，将会清空工作内存中此变量的值

2.对一个变量执行 unlock操作之前，必须先把此变量同步到主内存中

### **synchronized保证三大特性**

```java
synchronized (锁对象) {
    //受保护的资源
}
```

#### synchronized与原子性

synchronized保证只有一个线程拿到锁，能够进入同步代码块。

```java
public class Test {
    //1.定义一个共享变量number
    private static int number = 0;
    private static Object obj = new Object();

    public static void main(String[] args) throws InterruptedException {
        //2. 对number进行1000次++操作
        Runnable increment = () -> {
            for (int i = 0; i < 1000; i++) {
                synchronized (obj) {
                    number++;
                }
            }
        };

        List<Thread> list = new ArrayList<>();
        //3.使用5个线程来执行
        for (int i = 0; i < 5; i++) {
            Thread t = new Thread(increment);
            t.start();
            list.add(t);
        }

        for (Thread t : list) {
            t.join();
        }

        System.out.println("number=" + number);
    }
}
```

#### synchronized与可见性

执行synchronized时，对应的lock原子操作会刷新工作内存中共享变量的值。

```java
public class Test2 {
    //多个线程都会访问的数据，称之为线程的共享数据
    private static boolean run = true;

    public static void main(String[] args) throws InterruptedException {
        Thread t1 = new Thread(() -> {
            while (run){
                // 增加对象共享数据的打印，println是同步方法
                System.out.println("run=" + run);
            }
        });

        t1.start();
        sleep(1000);

        Thread t2 = new Thread(() -> {
            run = false;
            System.out.println("时间到，线程2设置为false");
        });
        t2.start();
    }
}
```

#### synchronized与有序性

为了提高程序的执行效率，编译器和CPU会对程序中代码进行重排序。

不管编译器和CPU如何重排序，必须保证在单线程的情况下程序的结果是正确的。编译器和处理器不会对存在数据依赖关系的操作做重排序，因为这种重排序会改变执行结果。但是，如果操作之间不存在数据依赖关系，这些操作就可能被编译器和处理器重排序。

加 synchronized后，依然会发生重排序，但是可以保证只有一个线程执行同步代码中的代码，保证有序性。

```java
public class TestJMM {	
  	// 指定使用并发测试
    @JCStressTest
    // 预测的结果与类型，附加描述信息
    @Outcome(id = {"0, 1", "1, 0", "1, 1"}, expect = ACCEPTABLE, desc = "Trivial under sequential consistency")
    @Outcome(id = "0, 0", expect = ACCEPTABLE_INTERESTING, desc = "Violates sequential consistency")
    // 标注需要测试的类
    @State
    public static class PlainExecutionOrder {
        private Object obj = new Object();
        int x, y;
        int i, j;
        
        // 标记方法使用多线程
        @Actor
        public void actor1(II_Result r) {
            synchronized (obj) {
                x = 1;
                r.r2 = y;
            }
        }

        @Actor
        public void actor2(II_Result r) {
            synchronized (obj) {
                y = 1;
                r.r1 = x;
            }
        }
    }
}
```

### synchronized的特性

#### 可重入性

一个线程可以多次执行synchronized，重复获取同一把锁。

synchronized的锁对象中有一个计数器，会记录线程获得几次锁，在执行完同步代码块时，计数器的数量会-1，直到计数器的数量为0，就释放这个锁。

**可重入的好处：**

可以避免死锁；可以让我们更好的封装代码。

```java
public class Test3 {
    public static void main(String[] args) {
        new Mythread().start();
        new Mythread().start();
    }
}

class Mythread extends Thread {
    @Override
    public void run() {
        synchronized (Mythread.class) {
            System.out.println(getName() + "进入了同步代码块1");

            synchronized (Mythread.class) {
                System.out.println(getName() + "进入了同步代码块2");
            }
        }
    }
}
```

#### 不可中断性

**什么是不可中断**：一个线程获得锁后，另一个线程想要获得锁，必须处于阻塞或等待状态，如果第一个线程不释放锁，第二个线程会一直阻塞或等待，不可被中断。

synchronized不可中断

```java
public class Test4 {
    private static Object obj = new Object();
    public static void main(String[] args) throws InterruptedException {
        //1.定义一个 Runnab1e
        Runnable run = ()->{
            //2.在 Runnable定义同步代码块
            synchronized (obj){
                System.out.println(Thread.currentThread().getName() + "进入同步代码块");
                // 保证不退出同步代码块
                try {
                    Thread.sleep(3333);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        };

        // 3.先开启一个线程来执行同步代码块
        Thread t1 = new Thread(run);
        t1.start();
        Thread.sleep(1000);

        //4.后开启一个线程来执行同步代码块（阻塞状态）
        Thread t2 = new Thread(run);
        t2.start();

        //5.停止第二个线程
        System.out.println("停止线程前");
        //结果显示还是处于blocked或runnable状态，由于不可中断特性强行中断没有生效
        t2.interrupt();
        System.out.println("停止线程后");

        System.out.println(t1.getState());
        System.out.println(t2.getState());
    }
}
```

ReentrantLock可被中断

Lock的lock方法不可被中断

Lock的tryLock方法可被中断

```java
public class Test5 {
    private static Lock lock = new ReentrantLock();

    public static void main(String[] args) throws InterruptedException {
        test();
    }

    public static void test() throws InterruptedException {
        Runnable run =()->{
            String name = Thread.currentThread().getName();
            try {
//              lock.lock();
                boolean b = lock.tryLock(3, TimeUnit.SECONDS);
                if (b) {
                    System.out.println(name + "获得锁，进入锁执行");
                    Thread.sleep(8888);
                } else {
                    System.out.println("在指定时间没有得到锁");
                }
            } catch (InterruptedException e) {
                e.printStackTrace();
            } finally {
                lock.unlock();
                System.out.println(name + "释放锁");
            }
        };

        Thread t1 = new Thread(run);
        t1.start();
        Thread.sleep(1000);

        Thread t2 = new Thread(run);
        t2.start();

//        System.out.println("停止t2线程前");
//        t2.interrupt();
//        System.out.println("停止t2线程后");
//
//        Thread.sleep(1000);
//        System.out.println(t1.getState());
//        System.out.println(t2.getState());
    }
}
```

### synchronized的原理

每一个对象都会和一个监视器 monitor关联。监视器被占用时会被锁住，其他线程无法来获取该 monitor，当JVM执行某个线程的某个方法内部的 monitorenter时，它会尝试去获取当前对象对应的 monitor的所有权。

**monitor监视器锁执行流程**：

1. 通过CAS尝试把 monitor的 owner字段设置为当前线程。

2. 如果设置之前的 owner指向当前线程，说明当前线程再次进入 monitor，即重入锁，执行 recursions++，记录重入的次数。

3. 如果当前线程是第一次进入该 monitor，设置 recursions为1， owner为当前线程，该线程成功获得锁并返回。

4. 若其他线程已经占有 monitor的所有权，那么当前尝试获取 monitor的所有权的线程会被阻塞，直到 monitor的进入数变为0，才能重新尝试获取 monitor的所有权。

### synchronized 与 Lock

1. synchronized是关键字，而Lock是一个接口。
2. synchronized是不可中断的，Lock可以中断也可以不中断。
3. 通过Lock可以知道线程有没有拿到锁，而 synchronized不能。
4. synchronized能锁住方法和代码块，而Lock只能锁住代码块。
5. Lock可以使用读锁提高多线程读的效率。
6. synchronized的加锁和解锁都由JVM实现，出现异常会自动释放锁，Lock需要手动释放锁，并且必须在finally中释放。

### synchronized 与 ReenTrantLock 

1. 两者都是可重入锁

2. synchronized 依赖于 JVM 而 ReenTrantLock 依赖于 API

   synchronized 是依赖于 JVM 实现的，JDK1.6 中为 synchronized 关键字进行了很多优化，但是这些优化都是在虚拟机层面实现的，并没有直接暴露给我们。

   ReenTrantLock 是 JDK 层面实现的（也就是 API 层面，需要 lock() 和 unlock() 配合 try/finally 语句块来完成），所以我们可以通过查看它的源代码，来看它是如何实现的。

3. ReenTrantLock 比 synchronized 增加了一些高级功能

   相比synchronized，ReenTrantLock增加了一些高级功能：等待可中断；可实现公平锁；可实现选择性通知（锁可以绑定多个条件）。

   - ReenTrantLock提供了一种能够中断等待锁的线程的机制，正在等待的线程可以选择放弃等待，改为处理其他事情。
   - ReenTrantLock可以指定是公平锁还是非公平锁，而synchronized只能是非公平锁。
   - synchronized在使用notify/notifyAll()方法进行通知时，被通知的线程是由 JVM 选择的；用ReentrantLock类结合Condition实例可以实现“选择性通知” 。

4. 性能已不是选择标准

   在JDK1.6之前，synchronized 关键字吞吐量随线程数的增加，下降得非常严重，而ReenTrantLock 基本保持一个比较稳定的水平。JDK1.6 之后，synchronized 和 ReenTrantLock 的性能基本是持平了，而且虚拟机在未来的性能改进中会更偏向于原生的synchronized，所以还是提倡在synchronized能满足你的需求的情况下，优先考虑使用synchronized关键字来进行同步。

### JDK1.6 之后的底层优化

JDK1.6 对锁的实现引入了大量的优化，如偏向锁、轻量级锁、自旋锁、适应性自旋锁、锁消除、锁粗化等技术来减少锁操作的开销。

锁主要存在四中状态，依次是：无锁状态、偏向锁状态、轻量级锁状态、重量级锁状态，他们会随着竞争的激烈而逐渐升级。注意锁可以升级不可降级，这种策略是为了提高获得锁和释放锁的效率。

#### 偏向锁

偏向锁的“偏”就是偏心的偏，它的意思是会偏向于第一个获得它的线程，如果在接下来的执行中，该锁没有被其他线程获取，那么持有偏向锁的线程就不需要进行同步。

轻量级锁在无竞争的情况下使用 CAS 操作去代替使用互斥量，而偏向锁在无竞争的情况下会把整个同步都消除掉。

#### 轻量级锁

轻量级锁能够提升程序同步性能的依据是“对于绝大部分锁，在整个同步周期内都是不存在竞争的”，这是一个经验数据。如果没有竞争，轻量级锁使用 CAS 操作避免了使用互斥操作的开销。但如果存在锁竞争，除了互斥量开销外，还会额外发生CAS操作，因此在有锁竞争的情况下，轻量级锁比传统的重量级锁更慢，如果锁竞争激烈，那么轻量级将很快膨胀为重量级锁。

#### 自旋锁

轻量级锁失败后，为了避免线程真实地在操作系统层面挂起，还会进行一项称为自旋锁的优化手段。

一般线程持有锁的时间都不是太长，所以仅仅为了这一点时间去挂起线程/恢复线程是得不偿失的。为了让一个线程等待，我们只需要让线程执行一个忙循环（自旋），这项技术就叫做自旋。自旋等待不能完全替代阻塞，因为它还是要占用处理器时间。如果自旋超过了限定次数仍然没有获得锁，就应该挂起线程。自旋次数的默认值是10次，用户可以通过--XX:PreBlockSpin来更改。

另外，在 JDK1.6 中引入了自适应的自旋锁。自适应的自旋锁带来的改进就是：自旋的时间不再固定了，而是和前一次同一个锁上的自旋时间以及锁的拥有者的状态来决定。

#### 锁消除

指的就是在虚拟机中即使编译器在运行时，如果检测到那些共享数据不可能存在竞争，那么就执行锁消除。锁消除可以节省毫无意义的请求锁的时间。

#### 锁粗化

什么是锁粗化？当JVM检测到一连串细小的操作都在反复使用同一个对象加锁、解锁，那就将同步代码块的范围放大，放到这串操作的外面，这样只需要加一次锁即可。