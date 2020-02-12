### 同步 vs 异步

同步和异步通常用来形容一次方法调用。

同步方法调用一旦开始，调用者必须等到方法返回后，才能继续后续的行为；而异步方法更像是一个消息传递，一旦开始，方法调用就会立即返回，调用者就可以继续后续的操作。

### 并发 vs 并行

- 并行：在同时刻，有多个指令在多个CPU上==同时==执行
- 并发：在同时刻，有多个指令在单个CPU上==交替==执行（线程之间的切换是随机的）

### 进程 vs 线程

**进程**是程序的一次执行过程，是系统运行程序的基本单位，因此进程是动态的。系统运行一个程序即是一个进程从创建、运行到消亡的过程。

**线程**与进程相似，但线程是一个比进程更小的执行单位。一个进程在其执行的过程中可以产生多个线程。与进程不同的是同类的多个线程共享进程的**堆**和**方法区**资源，但每个线程有自己的**程序计数器**、**虚拟机栈**和**本地方法栈**，线程也被称为轻量级进程。

下图是 Java 内存区域，通过下图我们从 JVM 的角度来说一下线程和进程之间的关系。

<img src="C:\Users\WANG\AppData\Roaming\Typora\typora-user-images\17257a5b841f4ff4bc8486db2d19f36f.png" alt="img" style="zoom: 67%;" />

从上图可以看出：一个进程中可以有多个线程，多个线程共享进程的**堆**和**方法区 (JDK1.8 之后的元空间)**资源，但是每个线程有自己的**程序计数器**、**虚拟机栈** 和 **本地方法栈**。

**总结：** <u>线程 是 进程 划分成的更小的运行单位。线程和进程最大的不同在于基本上各进程是独立的，而各线程则不一定，因为同一进程中的线程极有可能会相互影响。线程执行开销小，但不利于资源的管理和保护，而进程正相反。</u>

### 阻塞 vs 非阻塞

阻塞和非阻塞通常用来形容多线程间的相互影响。比如一个线程占用了临界区资源，那么其他所有需要这个资源的线程就必须在这个临界区中进行等待。等待会导致线程挂起，这种情况就是阻塞。此时，如果占用资源的线程一直不愿意释放资源，那么其他所有阻塞在这个临界区上的线程都不能工作。

非阻塞的意思与之相反，它强调没有一个线程可以妨碍其他线程执行。所有的线程都会尝试不断前向执行。

临界区是什么？

临界区用来表示一种公共资源或者说是共享资源，可以被多个线程使用。但是每一次只能有一个线程使用它，一旦临界区资源被占用，其他线程要想使用这个资源，就必须等待。

### 程序计数器为什么是私有的

程序计数器主要有下面两个作用：

1. 字节码解释器通过改变程序计数器来依次读取指令，从而实现代码的流程控制，如：顺序执行、选择、循环、异常处理。
2. 在多线程的情况下，程序计数器用于记录当前线程执行的位置，从而当线程被切换回来的时候能够知道该线程上次运行到哪儿了。

所以，程序计数器私有主要是为了**线程切换后能恢复到正确的执行位置**。

### 虚拟机栈和本地方法栈为什么是私有的

- **虚拟机栈：**每个 Java 方法在执行的同时会创建一个栈帧用于存储局部变量表、操作数栈、常量池引用等信息。从方法调用直至执行完成的过程，就对应着一个栈帧在 Java 虚拟机栈中入栈和出栈的过程。
- **本地方法栈：**和虚拟机栈所发挥的作用非常相似，区别是： **虚拟机栈为虚拟机执行 Java 方法 （也就是字节码）服务，而本地方法栈则为虚拟机使用到的 Native 方法服务。** 在 HotSpot 虚拟机中和 Java 虚拟机栈合二为一。

所以，为了**保证线程中的局部变量不被别的线程访问到**，虚拟机栈和本地方法栈是线程私有的。

### 线程有几种状态

六种，并且某个时刻 Java 线程只能处于其中的一种状态。

1. 新建（NEW）状态：表示新创建了一个线程对象，而此时线程并没有开始执行。
2. 可运行（RUNNABLE）状态：线程对象创建后，其它线程（比如 main 线程）调用了该对象的 start() 方法，才表示线程开始执行。当线程执行时，处于 RUNNBALE 状态，表示线程所需的一切资源都已经准备好了。该状态的线程位于可运行线程池中，等待被线程调度选中，获取 cpu 的使用权。
3. 阻塞（BLOCKED）状态：如果线程在执行过程中遇到了 synchronized 同步块，就会进入 BLOCKED 阻塞状态，这时线程就会暂停执行，直到获得请求的锁。
4. 等待（WAITING）状态：当线程等待另一个线程通知调度器一个条件时，它自己进入等待状态。在调用Object.wait方法或Thread.join方法，或者是等待java.util.concurrent库中的Lock或Condition时，就会出现这种情况。
5. 计时等待（TIMED_WAITING）状态：Object.wait、Thread.join、Lock.tryLock和Condition.await 等方法有超时参数，还有 Thread.sleep 方法、LockSupport.parkNanos 方法和 LockSupport.parkUntil 方法，这些方法会导致线程进入计时等待状态，如果超时或者出现通知，都会切换回可运行状态。
6. 终止（TERMINATED）状态：当线程执行完毕，则进入该状态，表示结束。

### 堆和方法区

堆和方法区是所有线程共享的资源，其中堆是进程中最大的一块内存，主要用于存放新创建的对象 (所有对象都在这里分配内存)，方法区主要用于存放已被加载的类信息、常量、静态变量、即时编译器编译后的代码等数据。

### 单线程 vs 多线程

1. 在单核 CPU 中，将 CPU 分为很小的时间片，在每一时刻只能有一个线程在执行，是一种微观上轮流占用 CPU 的机制。
2. 多线程会存在线程上下文切换，会导致程序执行速度变慢，即采用一个拥有两个线程的进程执行所需要的时间比一个线程的进程执行两次所需要的时间要多一些。

结论：由于线程有创建和上下文切换的开销，采用多线程不会提高程序的执行速度，但是应为CPU利用率的提升，整体响应速度变快了。

### 什么是上下文切换

多线程编程中一般线程的个数都大于 CPU 核心的个数，而一个 CPU 核心在任意时刻只能被一个线程使用，为了让这些线程都能得到有效执行，CPU 采取的策略是为每个线程分配时间片并轮转的形式。当一个线程的时间片用完的时候就会重新处于就绪状态让给其他线程使用，这个过程就属于一次上下文切换。

概括来说就是：当前任务在执行完 CPU 时间片切换到另一个任务之前会先保存自己的状态，以便下次再切换会这个任务时，可以再加载这个任务的状态。**任务从保存到再加载的过程就是一次上下文切换**。

上下文切换通常是计算密集型的。也就是说，它需要相当可观的处理器时间，在每秒几十上百次的切换中，每次切换都需要纳秒量级的时间。所以，上下文切换对系统来说意味着消耗大量的 CPU 时间，事实上，可能是操作系统中时间消耗最大的操作。

Linux 相比与其他操作系统（包括其他类 Unix 系统）有很多的优点，其中有一项就是，其上下文切换和模式切换的时间消耗非常少。

### 为什么要使用多线程

- 线程可以比作是轻量级的进程，是程序执行的最小单位，线程间的切换和调度的成本远远小于进程。
- 进程之前不能共享内存，而线程之间共享内存(堆内存)则很简单。
- 系统创建进程时需要为该进程重新分配系统资源，创建线程则代价小很多，因此实现多任务并发时多线程效率更高。
- 提高 CPU 的利用率。

### 实现多线程的三种方式

|                           | 优点                                         | 缺点                                       |
| ------------------------- | -------------------------------------------- | ------------------------------------------ |
| 实现Runable、Callable接口 | 扩展性强，实现该接口的同时还可以继承其他的类 | 编程相对复杂，不能直接使用Thread类中的方法 |
| 继承Thread类              | 编程比较简单，还可以直接使用Thread类中的方法 | 扩展性比较差，不能再继承其他的类           |

### 如何指定多个线程的执行顺序

面试官会给你举个例子，如何让 10 个线程按照顺序打印 0123456789？

1. 设定一个 orderNum，每个线程执行结束之后，更新 orderNum，指明下一个要执行的线程。并且唤醒所有的等待线程。
2. 在每一个线程的开始，要 while 判断 orderNum 是否等于自己的要求值！！不是，则 wait，是则执行本线程。

### 使用多线程可能带来什么问题

内存泄漏、上下文切换、死锁还有受限于硬件和软件的资源闲置问题。

### 多线程中的忙循环是什么

答：忙循环就是程序员用循环让一个线程等待，不像传统方法 wait()、sleep() 或yield() 它们都放弃了 CPU 控制权，而忙循环不会放弃 CPU，它就是在运行一个空循环，这么做的目的是为了保留 CPU 缓存。

在多核系统中，一个等待线程醒来的时候可能会在另一个内核运行，这样会重建缓存，为了避免重建缓存和减少等待重建的时间就可以使用它了。

### 什么是多线程环境下的伪共享

伪共享发生在不同处理器的上的线程对变量的修改依赖于相同的缓存和行，如下图所示：

<img src="C:\Users\WANG\AppData\Roaming\Typora\typora-user-images\7896890-3cbee585a5f68f02.png" style="zoom:50%;" />

伪共享问题很难被发现，因为线程可能访问完全不同的全局变量，内存中却碰巧在很相近的位置上。如其他诸多的并发问题，避免伪共享的最基本方式是仔细审查代码，根据缓存行来调整你的数据结构。

### 线程的生命周期和状态

Java 线程运行的生命周期中的指定时刻只可能处于下面 6 种不同状态的其中一个状态（图源《Java 并发编程艺术》4.1.4 节）。

| 状态名称     | 说明                                                         |
| ------------ | ------------------------------------------------------------ |
| new          | 初始状态，线程被构建，但是还没有调用start()方法              |
| runnable     | 运行状态，Java线程将操作系统中的就绪和运行两种状态笼统地称作“运行中” |
| blocked      | 阻塞状态，表示线程阻塞于锁                                   |
| waiting      | 等待状态，表示线程进入等待状态，进入该状态表示当前线程需要等待其他线程做出一些特定动作（通知或中断） |
| time_waiting | 超时等待状态，该状态不同于waiting，它是可以在指定的时间自行返回的 |
| terminated   | 终止状态，表示当前线程已经执行完毕                           |

线程在生命周期中并不是固定处于某一个状态而是随着代码的执行在不同状态之间切换。Java 线程状态变迁如下图所示（图源《Java 并发编程艺术》4.1.4 节）：

![Java 线程状态变迁](C:\Users\WANG\AppData\Roaming\Typora\typora-user-images\c7eeb95311294f04ac87342ff6e22550.png)

由上图可以看出：线程创建之后它将处于 **NEW（新建）** 状态，调用 start() 方法后开始运行，线程这时候处于 **READY（可运行）** 状态。可运行状态的线程获得了 CPU 时间片（timeslice）后就处于 **RUNNING（运行）** 状态。

操作系统隐藏 Java 虚拟机中的 READY和 RUNNING 状态，它只能看到 RUNNABLE 状态，所以 Java 系统一般将这两个状态统称为 **RUNNABLE（运行中）** 状态 。

当线程执行 wait()方法之后，线程进入 **WAITING（等待）**状态。进入等待状态的线程需要依靠其他线程的通知才能够返回到运行状态，而 **TIME_WAITING(超时等待)** 状态相当于在等待状态的基础上增加了超时限制，比如通过 sleep（long millis）方法或 wait（long millis）方法可以将 Java 线程置于 TIMED WAITING 状态。当超时时间到达后 Java 线程将会返回到 RUNNABLE 状态。当线程调用同步方法时，在没有获取到锁的情况下，线程将会进入到 **BLOCKED（阻塞）** 状态。线程在执行 Runnable 的run()方法之后将会进入到 **TERMINATED（终止）** 状态。

### 怎么理解线程优先级

优先极高的线程在竞争资源时会更有优势，更可能的抢占资源，但这只是一个概率问题，如果运行不好，高优先级线程可能也会抢占失败。

由于线程的优先级调度和底层操作系统有密切的关系，在各个平台上表现不一，并且这种优先级产生的后果也可能不容易预测，无法精准控制，比如一个低优先级的线程可能一直抢占不到资源，从而始终无法运行，而产生饥饿（虽然优先级低，但是也不能饿死它啊）。因此，在要求严格的场合，还是需要自己在应用层解决线程调度的问题。

在 Java 中，使用 1 到 10 表示线程优先级，数字越大则优先级越高，但有效范围在 1 到 10 之间，默认的优先级为 5 。

### 什么是线程死锁

线程死锁：多个线程同时被阻塞，它们中的一个或者全部都在等待某个资源被释放。由于线程被无限期地阻塞，因此程序不可能正常终止。

如下图所示，线程 A 持有资源 2，线程 B 持有资源 1，他们同时都想申请对方的资源，所以这两个线程就会互相等待而进入死锁状态。

<img src="C:\Users\WANG\AppData\Roaming\Typora\typora-user-images\ccd8adb6f69b4e29958bc7bfa119c082.png" style="zoom: 67%;" />

### 如何避免线程死锁

产生死锁必须具备以下四个条件：

- 互斥条件：一个资源每次只能被一个线程使用；
- 请求与保持条件：一个线程因请求资源而阻塞时，对已获得的资源保持不放；
- 不剥夺条件：进程已经获得的资源，在未使用完之前，不能强行剥夺；
- 循环等待条件：若干线程之间形成一种头尾相接的循环等待资源关系。

我们只要破坏产生死锁的四个条件中的其中一个就可以了

**破坏互斥条件**：这个条件我们没有办法破坏，因为我们用锁本来就是想让他们互斥的（临界资源需要互斥访问）。

**破坏请求与保持条件**：一次性申请所有的资源。

**破坏不剥夺条件**：<u>占用部分资源的线程进一步申请其他资源时，如果申请不到，可以主动释放它占有的资源。</u>

**破坏循环等待条件**：<u>按某一顺序申请资源，释放资源则反序释放。</u>

### 如何停止一个线程

Java 提供了很丰富的 API 但没有为停止线程提供 API 。

JDK 1.0 本来有一些像 stop()，suspend() 和 resume() 的控制方法但是由于潜在的死锁威胁因此在后续的 JDK 版本中它们被弃用了，之后就没有提供一个兼容且线程安全的方法来停止任何一个线程。

当 run() 或者 call() 方法执行完的时候线程会自动结束，如果要手动结束一个线程，你可以用 volatile 布尔变量来退出 run() 方法的循环或者是取消任务来中断线程。

### sleep() vs wait()

1. sleep 方法：是 Thread 类的静态方法，当前线程将睡眠 n 毫秒，线程进入阻塞状态。当睡眠时间到了，会解除阻塞，进行可运行状态，等待 CPU 的到来。睡眠不释放锁（如果有的话）；
2. wait 方法：是 Object 的方法，必须与 synchronized 关键字一起使用，线程进入阻塞状态，当 notify 或者 notifyall 被调用后，会解除阻塞。但是，只有重新占用互斥锁之后才会进入可运行状态。睡眠时，释放互斥锁。

### start() vs run()

start() 方法会新建一个线程并让这个线程执行 run() 方法；而直接调用 run() 方法知识作为一个普通的方法调用而已，它只会在当前线程中，串行执行 run() 中的代码。

## synchronized

### 对 synchronized 的了解

synchronized关键字解决的是多个线程之间访问资源的同步性，<u>synchronized关键字可以保证被它修饰的方法或者代码块在任意时刻只能有一个线程执行。</u>

在 Java 早期版本中，synchronized属于重量级锁，效率低下，因为监视器锁（monitor）是依赖于底层的操作系统的 Mutex Lock 来实现的，Java 的线程是映射到操作系统的原生线程之上的。如果要挂起或者唤醒一个线程，都需要操作系统帮忙完成，而操作系统实现线程之间的切换时需要从用户态转换到内核态，这个状态之间的转换需要相对比较长的时间。

JDK1.6对锁的实现引入了大量的优化，如自旋锁、适应性自旋锁、锁消除、锁粗化、偏向锁、轻量级锁等技术来减少锁操作的开销。synchronized是一个几种锁的封装。

### 对 synchronized 的使用

synchronized关键字最主要的三种使用方式：

- **修饰实例方法:** 作用于当前对象实例加锁，进入同步代码前要获得当前对象实例的锁
- **修饰静态方法:** 也就是给当前类加锁，会作用于类的所有对象实例，因为静态成员不属于任何一个实例对象，是类成员（ static 表明这是该类的一个静态资源，不管new了多少个对象，只有一份）。所以如果一个线程A调用一个实例对象的非静态 synchronized 方法，而线程B需要调用这个实例对象所属类的静态 synchronized 方法，是允许的，不会发生互斥现象，因为访问静态 synchronized 方法占用的锁是当前类的锁，而访问非静态 synchronized 方法占用的锁是当前实例对象锁。
- **修饰代码块:** 指定加锁对象，对给定对象加锁，进入同步代码块前要获得给定对象的锁。

**总结：** synchronized 关键字加到 static 静态方法和 synchronized(class)代码块上都是是给 Class 类上锁。synchronized 关键字加到实例方法上是给对象实例上锁。尽量不要使用 synchronized(String a) 因为JVM中，字符串常量池具有缓存功能。

### synchronized 的底层原理

**synchronized 同步语句块的实现使用的是 monitorenter 和 monitorexit 指令，其中 monitorenter 指令指向同步代码块的开始位置，monitorexit 指令则指明同步代码块的结束位置。** 当执行 monitorenter 指令时，线程试图获取锁也就是获取 monitor(monitor对象存在于每个Java对象的对象头中，synchronized 锁便是通过这种方式获取锁的，也是为什么Java中任意对象可以作为锁的原因) 的持有权。当计数器为0则可以成功获取，获取后将锁计数器设为1也就是加1。相应的在执行 monitorexit 指令后，将锁计数器设为0，表明锁被释放。如果获取对象锁失败，那当前线程就要阻塞等待，直到锁被另外一个线程释放为止。

synchronized 修饰的方法并没有 monitorenter 指令和 monitorexit 指令，取得代之的确实是 ACC_SYNCHRONIZED 标识，该标识指明了该方法是一个同步方法，JVM 通过该 ACC_SYNCHRONIZED 访问标志来辨别一个方法是否声明为同步方法，从而执行相应的同步调用。

### synchronized 做了哪些优化

JDK1.6 对锁的实现引入了大量的优化，如偏向锁、轻量级锁、自旋锁、适应性自旋锁、锁消除、锁粗化等技术来减少锁操作的开销。

锁主要存在四中状态，依次是：无锁状态、偏向锁状态、轻量级锁状态、重量级锁状态，他们会随着竞争的激烈而逐渐升级。注意锁可以升级不可降级，这种策略是为了提高获得锁和释放锁的效率。

**偏向锁**

**引入偏向锁的目的和引入轻量级锁的目的很像，他们都是为了在没有多线程竞争的前提下，减少传统的重量级锁使用操作系统互斥量产生的性能消耗。但是不同的是轻量级锁在无竞争的情况下使用 CAS 操作去代替使用互斥量，而偏向锁在无竞争的情况下会把整个同步都消除掉**。

偏向锁的“偏”就是偏心的偏，它的意思是会偏向于第一个获得它的线程，如果在接下来的执行中，该锁没有被其他线程获取，那么持有偏向锁的线程就不需要进行同步。

但是对于锁竞争比较激烈的场合，偏向锁就失效了，因为这样场合极有可能每次申请锁的线程都是不相同的，因此这种场合下不应该使用偏向锁，否则会得不偿失，需要注意的是，偏向锁失败后，并不会立即膨胀为重量级锁，而是先升级为轻量级锁。

**轻量级锁**

倘若偏向锁失败，虚拟机并不会立即升级为重量级锁，它还会尝试使用一种称为轻量级锁的优化手段。**轻量级锁不是为了代替重量级锁，它的本意是在没有多线程竞争的前提下，减少传统的重量级锁使用操作系统互斥量产生的性能消耗，因为使用轻量级锁时，不需要申请互斥量。另外，轻量级锁的加锁和解锁都用到了CAS操作。**

**轻量级锁能够提升程序同步性能的依据是“对于绝大部分锁，在整个同步周期内都是不存在竞争的”，这是一个经验数据。如果没有竞争，轻量级锁使用 CAS 操作避免了使用互斥操作的开销。但如果存在锁竞争，除了互斥量开销外，还会额外发生CAS操作，因此在有锁竞争的情况下，轻量级锁比传统的重量级锁更慢，如果锁竞争激烈，那么轻量级将很快膨胀为重量级锁。**

**自旋锁和自适应自旋锁**

轻量级锁失败后，虚拟机为了避免线程真实地在操作系统层面挂起，还会进行一项称为自旋锁的优化手段。

互斥同步对性能最大的影响就是阻塞的实现，因为挂起线程/恢复线程的操作都需要转入内核态中完成（用户态转换到内核态会耗费时间）。

**一般线程持有锁的时间都不是太长，所以仅仅为了这一点时间去挂起线程/恢复线程是得不偿失的。** 所以，虚拟机的开发团队就这样去考虑：“我们能不能让后面来的请求获取锁的线程等待一会而不被挂起呢？看看持有锁的线程是否很快就会释放锁”。**为了让一个线程等待，我们只需要让线程执行一个忙循环（自旋），这项技术就叫做自旋**。

自旋锁在 JDK1.6 之前其实就已经引入了，不过是默认关闭的，需要通过--XX:+UseSpinning参数来开启。JDK1.6及1.6之后，就改为默认开启的了。

需要注意的是，自旋等待不能完全替代阻塞，因为它还是要占用处理器时间。如果锁被占用的时间短，那么效果当然就很好了，相反，自旋等待的时间必须要有限度。如果自旋超过了限定次数任然没有获得锁，就应该挂起线程。**自旋次数的默认值是10次，用户可以通过****--XX:PreBlockSpin****来更改**。

另外，**在 JDK1.6 中引入了自适应的自旋锁。自适应的自旋锁带来的改进就是：自旋的时间不在固定了，而是和前一次同一个锁上的自旋时间以及锁的拥有者的状态来决定，虚拟机变得越来越“聪明”了**。

**锁消除**

锁消除理解起来很简单，它指的就是在虚拟机中即使编译器在运行时，如果检测到那些共享数据不可能存在竞争，那么就执行锁消除。锁消除可以节省毫无意义的请求锁的时间。

**锁粗化**

原则上，我们在编写代码的时候，总是推荐将同步快的作用范围限制得尽量小——只在共享数据的实际作用域才进行同步，这样是为了使得需要同步的操作数量尽可能变小，如果存在锁竞争，那等待线程也能尽快拿到锁。

大部分情况下，上面的原则都是没有问题的，但是如果一系列的连续操作都对同一个对象反复加锁和解锁，那么会带来很多不必要的性能消耗。

什么是锁粗化？当JVM检测到一连串细小的操作都在使用同一个对象加锁，那就将同步代码块的范围放大，放到这串操作的外面，这样只需要加一次锁即可。

### synchronized vs Lock

- synchronized是关键字，而Lock是一个接口。
- synchronized是不可中断的，Lok可以中断也可以不中断。
- 通过Lock可以知道线程有没有拿到锁，而 synchronized不能。
- synchronized能锁住方法和代码块，而Lock只能锁住代码块。
- Lock可以使用读锁提高多线程读的效率。
- synchronized的加锁和解锁都由JVM实现，出现异常会自动释放锁，Lock需要手动释放锁，并且必须在finally中释放。
- Lock可以使用非阻塞方式，尝试获得锁，如果没有获得，直接返回。synchronized拿不到锁会阻塞在那里。

### Synchronized vs ReenTrantLock 

**两者都是可重入锁**

“可重入锁”概念是：自己可以再次获取自己的内部锁。比如一个线程获得了某个对象的锁，此时这个对象锁还没有释放，当其再次想要获取这个对象的锁的时候还是可以获取的，如果不可锁重入的话，就会造成死锁。同一个线程每次获取锁，锁的计数器都自增1，要等到锁的计数器下降为0时才能释放锁。

**synchronized 依赖于 JVM 而 ReenTrantLock 依赖于 API**

synchronized 是依赖于 JVM 实现的，JDK1.6 中为 synchronized 关键字进行了很多优化，但是这些优化都是在虚拟机层面实现的，并没有直接暴露给我们。

ReenTrantLock 是 JDK 层面实现的（也就是 API 层面，需要 lock() 和 unlock 方法配合 try/finally 语句块来完成），所以我们可以通过查看它的源代码，来看它是如何实现的。

**ReenTrantLock 比 synchronized 增加了一些高级功能**

相比synchronized，ReenTrantLock增加了一些高级功能：**等待可中断；可实现公平锁；可实现选择性通知（锁可以绑定多个条件）。**

- **ReenTrantLock提供了一种能够中断等待锁的线程的机制**，通过lock.lockInterruptibly()来实现这个机制，也就是说正在等待的线程可以选择放弃等待，改为处理其他事情。
- **ReenTrantLock可以指定是公平锁还是非公平锁，而synchronized只能是非公平锁。所谓的公平锁就是先等待的线程先获得锁。** ReenTrantLock默认情况是非公平的，可以通过 ReenTrantLock类的ReentrantLock(boolean fair)构造方法来制定是否是公平的。
- synchronized关键字与wait()和notify/notifyAll()方法相结合可以实现等待/通知机制，ReentrantLock类也可以实现，但是需要借助于Condition接口与newCondition() 方法。**线程对象可以注册在指定的Condition中，从而可以有选择性的进行线程通知，在调度线程上更加灵活。 在使用notify/notifyAll()方法进行通知时，被通知的线程是由 JVM 选择的，用ReentrantLock类结合Condition实例可以实现“选择性通知”** 。而synchronized关键字就相当于整个Lock对象中只有一个Condition实例，所有的线程都注册在它一个身上。如果执行notifyAll()方法的话就会通知所有处于等待状态的线程，这样会造成很大的效率问题，而Condition实例的signalAll()方法，只会唤醒注册在该Condition实例中的所有等待线程。

**性能已不是选择标准**

在JDK1.6之前，synchronized 的性能是比 ReenTrantLock 差很多。具体表示为：synchronized 关键字吞吐量随线程数的增加，下降得非常严重，而ReenTrantLock 基本保持一个比较稳定的水平。**JDK1.6 之后，synchronized 和 ReenTrantLock 的性能基本是持平了。性能已经不再是选择synchronized和ReenTrantLock的影响因素了，而且虚拟机在未来的性能改进中会更偏向于原生的synchronized，所以还是提倡在synchronized能满足你的需求的情况下，优先考虑使用synchronized关键字来进行同步。优化后的synchronized和ReenTrantLock一样，在很多地方都是用到了CAS操作**。



## volatile

### 对 volatile 的了解

 **volatile** 关键字的主要作用就是<u>保证变量的可见性（不保证原子性），防止指令重排序。</u>

在 JDK1.2 之前，Java的内存模型实现总是从**主内存**（即共享内存）读取变量。而在当前的 Java 内存模型下，线程可以把变量保存**本地内存**，比如机器的寄存器中，而不是直接在主内存中进行读写。这就可能造成一个线程在主存中修改了一个变量的值，而另外一个线程还继续使用它在寄存器中的变量值的拷贝，造成**数据的不一致**。

![img](C:\Users\WANG\AppData\Roaming\Typora\typora-user-images\515eea4401174d69bfdde5e894ff5709.png)

要解决这个问题，就需要把变量声明为**volatile**，这就指示 JVM 这个变量是不稳定的，每次使用它都到主存中进行读取。

<img src="C:\Users\WANG\AppData\Roaming\Typora\typora-user-images\12bdfa6ce1584d40b7c4909e644dccc6.png" alt="img" style="zoom:75%;" />

### synchronized vs volatile

- volatile关键字主要用于解决变量在多个线程之间的可见性，而 synchronized关键字解决的是多个线程之间访问资源的同步性。
- volatile关键字能保证数据的可见性，但不能保证数据的原子性。synchronized关键字两者都能保证。
- 多线程访问volatile关键字不会发生阻塞，而synchronized关键字可能会发生阻塞。
- volatile关键字是线程同步的**轻量级实现**，所以volatile性能肯定比synchronized关键字要好。但是volatile关键字只能用于变量而synchronized关键字可以修饰方法以及代码块。

### volatile 能使得一个非原子操作变成原子操作吗

能。

一个典型的例子是在类中有一个 long 类型的成员变量。如果你知道该成员变量会被多个线程访问，如计数器、价格等，你最好是将其设置为 volatile。为什么？因为 Java 中读取 long 类型变量不是原子的，需要分成两步，如果一个线程正在修改该 long 变量的值，另一个线程可能只能看到该值的一半（前 32 位），但是对一个 volatile 型的 long 或 double 变量的读写是原子操作。

volatile 修饰符有过什么实践

1. 一种实践是用 volatile 修饰 long 和 double 变量，使其能按原子类型来读写。double 和 long 都是64位宽，因此对这两种类型的读是分为两部分的，第一次读取第一个 32 位，然后再读剩下的 32 位，这个过程不是原子的，但 Java 中 volatile 型的 long 或 double 变量的读写是原子的。
2. volatile 修复符的另一个作用是提供内存屏障（memory barrier），例如在分布式框架中的应用。简单的说，就是当你写一个 volatile 变量之前，Java 内存模型会插入一个写屏障（write barrier），读一个 volatile 变量之前，会插入一个读屏障（read barrier）。意思就是说，在你写一个 volatile 域时，能保证任何线程都能看到你写的值，同时，在写之前，也能保证任何数值的更新对所有线程是可见的，因为内存屏障会将其他所有写的值更新到缓存。

## ThreadLocal

### 对 ThreadLocal 的了解

通常情况下，我们创建的变量是可以被任何一个线程访问并修改的。ThreadLocal类就是让每个线程绑定自己的值，可以将ThreadLocal类形象的比喻成存放数据的盒子，盒子中可以存储每个线程的私有数据。

<u>如果你创建了一个ThreadLocal变量（线程局部变量），那么访问这个变量的每个线程都会有这个变量的本地副本</u>，这也是ThreadLocal变量名的由来。他们可以使用get（）和set（）方法来获取默认值或将其值更改为当前线程所存的副本的值，从而避免了线程安全问题。

### ThreadLocal 原理

1. 每个线程内部都会维护一个类似 HashMap 的对象，称为 ThreadLocalMap，里边会包含若干了 Entry（K-V 键值对），相应的线程被称为这些 Entry 的属主线程；
2. Entry 的 Key 是一个 ThreadLocal 实例，Value 是一个线程特有对象。Entry 的作用即是：为其属主线程建立起一个 ThreadLocal 实例与一个线程特有对象之间的对应关系；
3. Entry 对 Key 的引用是弱引用；Entry 对 Value 的引用是强引用。

### ThreadLocal 内存泄露问题

ThreadLocalMap 中使用的 key 为 ThreadLocal 的弱引用,而 value 是强引用。所以，如果 ThreadLocal 没有被外部强引用的情况下，在垃圾回收的时候会 key 会被清理掉，而 value 不会被清理掉。这样一来，ThreadLocalMap 中就会出现key为null的Entry。假如我们不做任何措施的话，value 永远无法被GC 回收，这个时候就可能会产生内存泄露。ThreadLocalMap实现中已经考虑了这种情况，在调用 set()、get()、remove() 方法的时候，会清理掉 key 为 null 的记录。使用完 ThreadLocal方法后 最好手动调用remove()方法。

```java
static class Entry extends WeakReference<ThreadLocal<?>> {
    /** The value associated with this ThreadLocal. */
    Object value;

    Entry(ThreadLocal<?> k, Object v) {
    	super(k);
    	value = v;
    }
}
```

**弱引用介绍：**

如果一个对象只具有弱引用，那就类似于**可有可无的生活用品**。

弱引用与软引用的区别在于：只具有弱引用的对象拥有更短暂的生命周期。在垃圾回收器线程扫描它所管辖的内存区域的过程中，一旦发现了只具有弱引用的对象，不管当前内存空间足够与否，都会回收它的内存。不过，由于垃圾回收器是一个优先级很低的线程， 因此不一定会很快发现那些只具有弱引用的对象。

弱引用可以和一个引用队列（ReferenceQueue）联合使用，如果弱引用所引用的对象被垃圾回收，Java虚拟机就会把这个弱引用加入到与之关联的引用队列中。



## 线程池

### 什么是线程池

java.util.concurrent.ThreadPoolExecutor 类就是一个线程池。客户端调用 ThreadPoolExecutor.submit(Runnable task)  提交任务，线程池内部维护的工作者线程的数量就是该线程池的线程池大小，有 3 种形态：

> - 当前线程池大小 ：表示线程池中实际工作者线程的数量；
> - 最大线程池大小 （maxinumPoolSize）：表示线程池中允许存在的工作者线程的数量上限；
> - 核心线程大小 （corePoolSize ）：表示一个不大于最大线程池大小的工作者线程数量上限。

1. 如果运行的线程少于 corePoolSize，则 Executor 始终首选添加新的线程，而不进行排队；
2. 如果运行的线程等于或者多于 corePoolSize，则 Executor 始终首选将请求加入队列，而不是添加新线程；
3. 如果无法将请求加入队列，即队列已经满了，则创建新的线程，除非创建此线程超出 maxinumPoolSize， 在这种情况下，任务将被拒绝。

### 为什么要用线程池

- **降低资源消耗。** 通过重复利用已创建的线程降低线程创建和销毁造成的消耗。
- **提高响应速度。** 当任务到达时，任务可以不需要等到线程创建就能立即执行。
- **提高线程的可管理性。** 线程是稀缺资源，如果无限制的创建，不仅会消耗系统资源，还会降低系统的稳定性，使用线程池可以进行统一的分配，调优和监控。

### Runnable 接口 vs Callable 接口

如果想让线程池执行任务的话需要实现的 Runnable 接口或 Callable 接口，两者的区别在于 Runnable 接口不会返回结果但是 Callable 接口可以返回结果。

备注： 工具类Executors可以实现Runnable对象和Callable对象之间的相互转换。

### execute() vs submit() 

execute() 方法用于提交不需要返回值的任务，因此无法判断任务是否被线程池执行成功与否；

submit() 方法用于提交需要返回值的任务。线程池会返回一个 Future 类型的对象，通过这个 Future 对象可以判断任务是否执行成功，并且可以通过 future 的 get() 方法来获取返回值，get() 方法会阻塞当前线程直到任务完成，而使用 get（long timeout，TimeUnit unit）方法则会阻塞当前线程一段时间后立即返回，这时候有可能任务还没有执行完。

### 如何创建线程池

**方式一：通过构造方法实现**

<img src="C:\Users\WANG\AppData\Roaming\Typora\typora-user-images\089dc1c798b74db6a745d70378589f99.png" alt="通过构造方法实现" style="zoom:80%;" />

**方式二：通过 Executor 框架的工具类 Executors 来实现**

我们可以创建三种类型的 ThreadPoolExecutor：

- **FixedThreadPool** ： 该方法返回一个固定线程数量的线程池。该线程池中的线程数量始终不变。当有一个新的任务提交时，线程池中若有空闲线程，则立即执行。若没有，则新的任务会被暂存在一个任务队列中，待有线程空闲时，便处理在任务队列中的任务。
- **SingleThreadExecutor：** 该方法返回一个只有一个线程的线程池。若多余一个任务被提交到该线程池，任务会被保存在一个任务队列中，待线程空闲，按先入先出的顺序执行队列中的任务。
- **CachedThreadPool：** 该方法返回一个可根据实际情况调整线程数量的线程池。线程池的线程数量不确定，但若有空闲线程可以复用，则会优先使用可复用的线程。若所有线程均在工作，又有新的任务提交，则会创建新的线程处理任务。所有线程在当前任务执行完毕后，将返回线程池进行复用。

<img src="C:\Users\WANG\AppData\Roaming\Typora\typora-user-images\e8d3f68a9b854258bae53744a1a415e5.png" alt="通过 Executor 框架的工具类 Executors 来实现" style="zoom:80%;" />

《阿里巴巴 Java 开发手册》中强制线程池不允许使用 Executors 去创建，而是通过 ThreadPoolExecutor 的方式。Executors 返回线程池对象的弊端如下：

- FixedThreadPool 和 SingleThreadExecutor ： 允许请求的队列长度为 Integer.MAX_VALUE ，可能堆积大量的请求，从而导致 OOM。
- CachedThreadPool 和 ScheduledThreadPool ： 允许创建的线程数量为 Integer.MAX_VALUE ，可能会创建大量线程，从而导致 OOM。

## tomic 原子类

### 介绍一下 Atomic 原子类

<u>Atomic 是指一个操作是不可中断的，即使是在多个线程一起执行的时候，一个操作一旦开始，就不会被其他线程干扰。所以原子类就是具有原子操作特征的类。</u>可以使基本数据类型以原子的方式实现自增自减等操作。

并发包 java.util.concurrent 的原子类都存放在java.util.concurrent.atomic下，如下图所示。

![JUC 原子类概览](C:\Users\WANG\AppData\Roaming\Typora\typora-user-images\17894be993784d5cb50a3ffedfa3c3f8.png)



### JUC 包中的原子类是哪 4 类

**基本类型**

使用原子的方式更新基本类型

- AtomicInteger：整形原子类
- AtomicLong：长整型原子类
- AtomicBoolean：布尔型原子类

**数组类型**

使用原子的方式更新数组里的某个元素

- AtomicIntegerArray：整形数组原子类
- AtomicLongArray：长整形数组原子类
- AtomicReferenceArray：引用类型数组原子类

**引用类型**

- AtomicReference：引用类型原子类
- AtomicStampedRerence：原子更新引用类型里的字段原子类
- AtomicMarkableReference ：原子更新带有标记位的引用类型

**对象的属性修改类型**

- AtomicIntegerFieldUpdater：原子更新整形字段的更新器
- AtomicLongFieldUpdater：原子更新长整形字段的更新器
- AtomicStampedReference：原子更新带有版本号的引用类型。该类将整数值与引用关联起来，可用于解决原子的更新数据和数据的版本号，可以解决使用 CAS 进行原子更新时可能出现的 ABA 问题
  - ABA：如果内存值V初次读取的时候为A，在将要赋值的时候再次检查还是A，能说明V没有改变过吗



### 讲讲 AtomicInteger 的使用

**AtomicInteger 类常用方法**

```java
public final int get() //获取当前的值
public final int getAndSet(int newValue)//获取当前的值，并设置新的值
public final int getAndIncrement()//获取当前的值，并自增
public final int getAndDecrement() //获取当前的值，并自减
public final int getAndAdd(int delta) //获取当前的值，并加上预期的值
boolean compareAndSet(int expect, int update) //如果输入的数值等于预期值，则以原子方式将该值设置为输入值（update）
public final void lazySet(int newValue)//最终设置为 newValue,使用 lazySet 设置之后可能导致其他线程在之后的一小段时间内还是可以读到旧的值
```

使用 AtomicInteger 之后，不用对 increment() 方法加锁也可以保证线程安全。

### AtomicInteger 原子类的原理

## AQS

### AQS 介绍

AQS 的全称为（AbstractQueuedSynchronizer），抽象队列同步器，这个类在 java.util.concurrent.locks 包下面。

<img src="C:\Users\WANG\AppData\Roaming\Typora\typora-user-images\8951d2bd3a61451090cc56469d383283.png" alt="AQS" style="zoom:75%;" />

AQS 是一个用来构建锁和同步器的框架，使用 AQS 能简单且高效地构造出应用广泛的大量的同步器，比如我们提到的 ReentrantLock，Semaphore，其他的诸如 ReentrantReadWriteLock，SynchronousQueue，FutureTask 等等皆是基于 AQS 的。我们自己也能利用 AQS 非常轻松容易地构造出符合我们自己需求的同步器。

### AQS 原理分析

AQS 核心思想是，如果被请求的共享资源空闲，则将当前请求资源的线程设置为有效的工作线程，并且将共享资源设置为锁定状态。如果被请求的共享资源被占用，那么就需要一套线程阻塞等待以及被唤醒时锁分配的机制，这个机制 AQS 是用 CLH 队列锁实现的，即将暂时获取不到锁的线程加入到队列中。

CLH(Craig,Landin,and Hagersten) 队列是一个虚拟的双向队列（虚拟的双向队列即不存在队列实例，仅存在结点之间的关联关系）。AQS 是将每条请求共享资源的线程封装成一个 CLH 锁队列的一个结点（Node）来实现锁的分配。

AQS(AbstractQueuedSynchronizer) 原理图：

<img src="C:\Users\WANG\AppData\Roaming\Typora\typora-user-images\2bc517b5aa9b443ca6b9b031979136bd.png" alt="AQS原理图" style="zoom:80%;" />

AQS 使用一个 int 成员变量来表示同步状态，通过内置的 FIFO(First Input First Output，先进先出) 队列来完成获取资源线程的排队工作。AQS 使用 CAS(Compare and Swap，比较再交换) 对该同步状态进行原子操作实现对其值的修改。

状态信息通过 protected 类型的 getState，setState，compareAndSetState 进行操作

```java
private volatile int state;//共享变量，使用 volatile 修饰保证线程可见性

//返回同步状态的当前值
protected final int getState() {  
    return state;
}
// 设置同步状态的值
protected final void setState(int newState) { 
    state = newState;
}
//原子地（CAS 操作）将同步状态值设置为给定值 update 如果当前同步状态的值等于 expect（期望值）
protected final boolean compareAndSetState(int expect, int update) {
    return unsafe.compareAndSwapInt(this, stateOffset, expect, update);
}
```

### AQS 对资源的共享方式

- **Exclusive**（独占）：只有一个线程能执行，如 ReentrantLock，又可分为公平锁和非公平锁：
  - 公平锁：按照线程在队列中的排队顺序，先到者先拿到锁
  - 非公平锁：当线程要获取锁时，无视队列顺序直接去抢锁，谁抢到就是谁的
- **Share**（共享）：多个线程可同时执行，如 Semaphore/CountDownLatch。

ReentrantReadWriteLock 可以看成是组合式，因为 ReentrantReadWriteLock 也就是读写锁允许多个线程同时对某一资源进行读。

自定义同步器在实现时只需要实现共享资源 state 的获取与释放方式即可，至于具体线程等待队列的维护（如获取资源失败入队/唤醒出队等），AQS 已经在底层实现好了。

### AQS 使用的设计模式

**AQS 使用了模板方法模式，自定义同步器时需要重写下面几个 AQS 提供的模板方法：**

```java
isHeldExclusively()//该线程是否正在独占资源，只有用到 condition 才需要去实现它。
tryAcquire(int)//独占方式。尝试获取资源，成功则返回 true，失败则返回 false。
tryRelease(int)//独占方式。尝试释放资源，成功则返回 true，失败则返回 false。
tryAcquireShared(int)//共享方式。尝试获取资源，负数表示失败；0 表示成功，但没有剩余可用资源；正数表示成功，且有剩余资源。
tryReleaseShared(int)//共享方式。尝试释放资源，成功则返回 true，失败则返回 false。
```

默认情况下，每个方法都抛出 UnsupportedOperationException。 这些方法的实现必须是内部线程安全的，并且通常应该简短而不是阻塞。AQS 类中的其他方法都是 final ，所以无法被其他类使用，只有这几个方法可以被其他类使用。

以 ReentrantLock 为例，state 初始化为 0，表示未锁定状态。A 线程 lock() 时，会调用 tryAcquire() 独占该锁并将 state+1。此后，其他线程再 tryAcquire() 时就会失败，直到 A 线程 unlock() 到 state=0（即释放锁）为止，其它线程才有机会获取该锁。当然，释放锁之前，A 线程自己是可以重复获取此锁的（state 会累加），这就是可重入的概念。但要注意，获取多少次就要释放多么次，这样才能保证 state 是能回到零态的。

再以 CountDownLatch 以例，任务分为 N 个子线程去执行，state 也初始化为 N（注意 N 要与线程个数一致）。这 N 个子线程是并行执行的，每个子线程执行完后 countDown() 一次，state 会 CAS(Compare and Swap) 减 1。等到所有子线程都执行完后 (即 state=0)，会 unpark() 主调用线程，然后主调用线程就会从 await() 函数返回，继续后余动作。

一般来说，自定义同步器要么是独占方法，要么是共享方式，他们也只需实现tryAcquire-tryRelease、tryAcquireShared-tryReleaseShared中的一种即可。但 AQS 也支持自定义同步器同时实现独占和共享两种方式，如ReentrantReadWriteLock。

### AQS 组件总结

- **Semaphore（信号量 允许多个线程同时访问）：** synchronized 和 ReentrantLock 都是一次只允许一个线程访问某个资源，Semaphore(信号量) 可以指定多个线程同时访问某个资源。
- **CountDownLatch （倒计时器）：** CountDownLatch 是一个同步工具类，用来协调多个线程之间的同步。这个工具通常用来控制线程等待，它可以让某一个线程等待直到倒计时结束，再开始执行。
- **CyclicBarrier（循环栅栏）：** CyclicBarrier 和 CountDownLatch 非常类似，它也可以实现线程间的技术等待，但是它的功能比 CountDownLatch 更加复杂和强大。主要应用场景和 CountDownLatch 类似。CyclicBarrier 的字面意思是可循环使用（Cyclic）的屏障（Barrier）。它要做的事情是，让一组线程到达一个屏障（也可以叫同步点）时被阻塞，直到最后一个线程到达屏障时，屏障才会开门，所有被屏障拦截的线程才会继续干活。CyclicBarrier 默认的构造方法是 CyclicBarrier(int parties)，其参数表示屏障拦截的线程数量，每个线程调用 await() 方法告诉 CyclicBarrier 我已经到达了屏障，然后当前线程被阻塞。

### 线程安全的单例模式

1. 饿汉式单例

饿汉式单例是指在方法调用前，实例就已经创建好了。

```java
public class Singleton {

    private static Singleton instance = new Singleton();

    private Singleton (){}

    public static Singleton getInstance() {
        return instance;
    }
}
```

2. 加入 synchronized 的懒汉式单例

所谓懒汉式单例模式就是在调用的时候才去创建这个实例。

```java
public class Singleton {    

    private static Singleton instance;    

    private Singleton (){}    

    public static synchronized Singleton getInstance() {    
        if (instance == null) {    
            instance = new Singleton();    
    	}    
    	return instance;    
    }    
}  
```

3. 使用静态内部类的方式创建单例

这种方式利用了 classloder 的机制来保证初始化 instance 时只有一个线程，它跟饿汉式的区别是：饿汉式只要 Singleton 类被加载了，那么 instance 就会被实例化（没有达到 lazy loading 的效果），而这种方式是 Singleton 类被加载了，instance 不一定被初始化。只有显式通过调用 getInstance() 方法时才会显式装载 SingletonHoder 类，从而实例化 singleton。

```java
public class Singleton {

    private Singleton() {}

    private static class SingletonHolder {// 静态内部类  
        private static Singleton singleton = new Singleton();
    }

    public static Singleton getInstance() {
        return SingletonHolder.singleton;
    }
}
```

4. 双重校验锁

为了达到线程安全，又能提高代码执行效率，我们这里可以采用DCL的双检查锁机制来完成，代码实现如下：

```java
public class Singleton {  
  
    private static Singleton singleton;  

    private Singleton() {  
    }  

    public static Singleton getInstance(){  
        if (singleton == null) {  
            synchronized (Singleton.class) {  
                if (singleton == null) {  
                    singleton = new Singleton();  
                }  
            }  
        }  
        return singleton;  
    }  
} 
```

这种是用双重判断来创建一个单例的方法，那么我们为什么要使用两个if判断这个对象当前是不是空的呢 ？因为当有多个线程同时要创建对象的时候，多个线程有可能都停止在第一个if判断的地方，等待锁的释放，然后多个线程就都创建了对象，这样就不是单例模式了，所以我们要用两个if来进行这个对象是否存在的判断。

5. 使用 static 代码块实现单例

静态代码块中的代码在使用类的时候就已经执行了，所以可以应用静态代码块的这个特性来实现单例设计模式。

```java
public class Singleton{  
       
    private static Singleton instance = null;  
       
    private Singleton(){}  
  
    static{  
        instance = new Singleton();  
    }  
      
    public static Singleton getInstance() {   
        return instance;  
    }   
}  
```

6. 使用枚举数据类型实现单例模式

枚举enum和静态代码块的特性相似，在使用枚举时，构造方法会被自动调用，利用这一特性也可以实现单例：

```java
public class ClassFactory{   
      
    private enum MyEnumSingleton{  
        singletonFactory;  
          
        private MySingleton instance;  
          
        private MyEnumSingleton(){//枚举类的构造方法在类加载是被实例化  
            instance = new MySingleton();  
        }  
   
        public MySingleton getInstance(){  
            return instance;  
        }  
    }   
   
    public static MySingleton getInstance(){  
        return MyEnumSingleton.singletonFactory.getInstance();  
    }  
}  
```

