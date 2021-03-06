## 一

### Java vs C++

1. 都是面向对象的语言，都支持封装、继承和多态；
2. Java 不提供指针来直接访问内存，程序内存更加安全；
3. Java 的类是单继承的，C++ 支持多重继承；虽然 Java 的类不可以多继承，但是接口可以多继承；
4. Java 有自动内存管理机制，不需要程序员手动释放无用内存。

### Java 面向对象的三大特征

**封装**是指将对象的实现细节隐藏起来，然后通过公共的方法来向外暴露出该对象的功能。

**继承**是使用已存在的类的定义作为基础建立新类的技术，新类的定义可以增加新的数据或新的功能，也可以用父类的功能，但不能选择性地继承父类。

1. 子类拥有父类对象所有的属性和方法（包括私有属性和私有方法），但是父类中的私有属性和私有方法子类是无法访问的，只是拥有。
2. 子类可以拥有自己属性和方法，即子类可以对父类进行扩展。
3. 子类可以用自己的方式实现父类的方法（重写）。

**多态**，在Java中有两种形式可以实现多态：继承（多个子类对同一方法的重写）和接口（实现接口并覆盖接口中同一方法）。

### 面向过程 vs 面向对象

面向过程是一种<u>以事件为中心</u>的编程思想，编程的时候把解决问题的步骤分析出来，然后用函数把这些步骤实现，在一步一步的具体步骤中再按顺序调用函数。

面向对象是一种<u>以对象为中心</u>的编程思想，把要解决的问题分解成各个对象，建立对象的目的不是为了完成一个步骤，而是为了描叙某个对象在整个解决问题的步骤中的属性和行为。面向对象是以功能来划分问题，而不是以步骤解决。

一般面向过程性能比面向对象高，比如单片机、嵌入式开发、Linux/Unix等一般采用面向过程开发。但是面向过程没有面向对象易维护、易复用、易扩展。 

### JVM、JDK 和 JRE 

JVM 是运行 Java 字节码的虚拟机。当我们运行一个程序时，JVM 负责<u>将字节码转换为特定机器代码</u>，JVM 提供了内存管理、垃圾回收和安全机制等功能。独立于硬件和操作系统，是 Java 程序可以一次编写多处执行的原因。

JDK 是Java Development Kit，java 开发工具包，是 java <u>开发环境的核心组件</u>，并提供编译、调试和运行一个 java 程序所需要的所有工具。它能够创建和编译程序。

JRE 是 Java运行时环境，提供了<u>运行 java 程序的平台</u>，JRE 包含了 JVM，但是不包含 java 编译器、调试器之类的开发工具，它不能用于创建新程序。

区别：

1. JDK 用于开发，JRE 用于运行 java 程序；
2. JDK 和 JRE 中都包含 JVM；
3. JVM 是 java 编程语言的核心并且具有平台独立性。

### JDK 中常用的包

java.lang、java.util、java.io、java.net、java.sql

### 八种基本数据类型

六种数字类型（四个整数型，两个浮点型），一种字符类型，还有一种布尔型。

除了基本类型（primitive type），剩下的都是引用类型（reference type）， Java 5 以后引入的枚举类型也算是一种比较特殊的引用类型。

byte			8位

short		16位

int			 32位

long		 64位

float		 32位

double	64位

char		  16位

boolean	  1位

### 字节码的优点

在 Java 中，<u>JVM可以理解的代码就叫做字节码（即扩展名为 .class 的文件）</u>，它不面向任何特定的处理器，只面向虚拟机。Java 语言通过字节码的方式，在一定程度上解决了传统解释型语言执行效率低的问题，同时又保留了解释型语言可移植的特点。所以 Java 程序运行时比较高效，而且，<u>由于字节码并不针对一种特定的机器，Java程序无须重新编译便可在多种不同操作系统的计算机上运行</u>。

字节码和不同系统的 JVM 实现是 Java 语言“一次编译，随处运行”的关键所在。

Java 程序从源代码到运行一般有下面3步：

![](image/3FFF5A36E61246A9B45737B1C8F2D5AF.png)

### 泛型的优点

在集合中存储对象并在使用前进行类型转换非常不方便，泛型防止了那种情况的发生。它<u>提供了编译期的类型安全，确保你只能把正确类型的对象放入集合中</u>，避免了在运行时出现ClassCastException。

### 限定通配符和非限定通配符 

限定通配符对类型进行了限制。有两种限定通配符，一种是`<? extends T>`它通过确保类型必须是T的子类来设定类型的上界，另一种是`<? super T>`它通过确保类型必须是T的父类来设定类型的下界。泛型类型必须用限定内的类型来进行初始化，否则会导致编译错误。

另一方面`<?>`表示了非限定通配符，因为`<?>`可以用任意类型来替代。

### 反射及应用场景

反射机制指在运行状态中，对于任意一个类，都能够知道这个类的所有属性和方法；对于任意一个对象，都能够调用它的任意一个方法和属性；这种动态获取信息以及动态调用对象的方法的功能称为java语言的反射机制。

静态编译和动态编译

- 静态编译：编译时确定类型，绑定对象
- 动态编译：运行时确定类型，绑定对象

反射机制优缺点

- 优点： 运行期类型的判断，动态加载类，提高代码灵活度。
- 缺点： 性能瓶颈，反射相当于一系列解释操作，性能比直接的java代码要慢很多。

**反射的应用场景**

反射是框架设计的灵魂。例如模块化的开发，通过反射去调用对应的字节码；动态代理设计模式也采用了反射机制，还有我们日常使用的 Spring／Hibernate 等框架也大量使用到了反射机制。

举例：

①我们在使用JDBC连接数据库时使用Class.forName()通过反射加载数据库的驱动程序；

②Spring框架也用到很多反射机制，最经典的就是xml的配置模式。Spring 通过 XML 配置模式装载 Bean 的过程：

1. 将程序内所有 XML 或 Properties 配置文件加载入内存中;
2. 解析xml或properties里面的内容，得到对应实体类的字节码字符串以及相关的属性信息; 
3. 使用反射机制，根据这个字符串获得某个类的Class实例; 
4. 动态配置实例的属性。

## 二

### 接口 vs 抽象类

1. 接口的方法默认是 public，所有方法在接口中不能有实现(Java 8 开始接口方法可以有默认实现），而抽象类可以有非抽象的方法。
2. 抽象类中可以有普通的成员变量；接口中的变量必须是 static final 类型的，必须被初始化 , 接口中只有常量，没有变量。
3. 一个类可以实现多个接口，但最多只能实现一个抽象类。
4. 抽象类只能单继承，接口可以继承多个父接口；
5. 一个类实现接口的话要实现接口的所有方法，而抽象类不一定。
6. 接口不能用 new 实例化，可以声明，但是必须引用一个实现该接口的对象。
7. 从设计层面来说，抽象是对类的抽象，是一种模板设计，而接口是对行为的抽象，是一种行为的规范。

**抽象类必须要有抽象方法吗**

抽象类中不一定包含抽象方法，但是包含抽象方法的类一定要被声明为抽象类。

**抽象类能使用 final 修饰吗**

抽象类不能用final来修饰。当用final修饰一个类时，表明这个类不能被继承。final类中的所有成员方法都会被隐式地指定为final方法，这明显违背了抽象类存在的意义。

**抽象类和接口如何选择**

1. 如果要创建不带任何方法定义和成员变量的基类，那么就应该选择接口而不是抽象类。
2. 如果知道某个类应该是基类，那么第一个选择的应该是让它成为一个接口，只有在必须要有方法定义和成员变量的时候，才应该选择抽象类。因为抽象类中允许存在一个或多个被具体实现的方法，只要方法没有被全部实现该类就仍是抽象类。

### 重载 vs 重写

**重载：** 发生在同一个类中，方法名必须相同，参数类型、个数、顺序不同，方法返回值和访问修饰符可以不同。 　　

**重写：** 发生在父子类中，方法名、参数列表必须相同，返回值范围小于等于父类，抛出的异常范围小于等于父类，访问修饰符范围大于等于父类；如果父类方法访问修饰符为 private 则子类就不能重写该方法。

在讲继承的时候我们就知道父类的私有属性和构造方法并不能被继承，所以 Constructor 也就不能被 override（重写）,但是可以 overload（重载）,所以你可以看到一个类中有多个构造函数的情况。

### "abc"、new String("abc")

一个是常量池中的对象、一个是堆内存中的对象，推荐使用第一种方式创建字符串。

- 第一种方式先检查字符串常量池中有没有"abc"，如果没有则创建一个，然后指向常量池中的对象；
- 第二种方式是直接在堆内存空间创建一个新的对象。

### String  vs StringBuilder vs StringBuffer 

**可变性**

String 类中使用 final 关键字修饰字符数组来保存字符串，所以 String 对象是不可变的。

```java
private　final　char[]　value
```

StringBuilder 与 StringBuffer 都继承自 AbstractStringBuilder 类，在 AbstractStringBuilder 中也是使用字符数组保存字符串 `char[] value` 但是没有用 final 关键字修饰，所以这两种对象都是可变的。

**线程安全性**

String 中的对象是不可变的，也就可以理解为常量，线程安全。

StringBuffer 对方法加了同步锁或者对调用的方法加了同步锁，所以是线程安全的。StringBuilder 并没有对方法进行加同步锁，所以是非线程安全的。

**性能**

每次对 String 类型进行改变的时候，都会生成一个新的 String 对象，然后将指针指向新的 String 对象。StringBuffer 每次都会对 StringBuffer 对象本身进行操作，而不是生成新的对象并改变对象引用。相同情况下使用 StringBuilder 相比使用 StringBuffer 仅能获得 10%~15% 左右的性能提升，但却要冒多线程不安全的风险。

**总结：**

1. 操作少量的数据: 适用String
2. 单线程操作大量数据: 适用StringBuilder
3. 多线程操作大量数据: 适用StringBuffer

### == vs equals	

**==** : 它的作用是<u>判断两个对象的地址是不是相等</u>。即，判断两个对象是不是同一个对象(基本数据类型比较的是值，引用数据类型比较的是内存地址)。

equals() : 判断两个对象是否相等，但它一般有两种使用情况：

- 类没有覆盖 equals() 方法，等价于通过“==”比较这两个对象
- 类覆盖了 equals() 方法

==是指对内存地址进行比较 ，equals()是对地址所指向的内存空间的值进行比较。

> String 中的 equals 方法是被重写过的，因为 object 的 equals 方法是比较的对象的内存地址，而 String 的 equals 方法比较的是对象的值。

### i++ vs ++i

实际上，不管是前置 ++，还是后置 ++，都是先将变量的值加 1，然后才继续计算的。

二者之间真正的区别是：前置 ++ 是将变量的值加 1 后，<u>使用增值后的变量进行运算</u>的；而后置 ++ 是首先将变量赋值给一个临时变量，接下来对变量的值加 1，然后<u>使用那个临时变量进行运算</u>。

### exception vs error 

exception 和 error 都是 Throwable 的子类。

exception 用于程序<u>可以捕获的异常</u>情况；error 定义了<u>不期望被程序捕获的异常</u>。

exception 表示一种设计的问题，也就是说只要程序正常运行，从不会发生的情况；而 error 表示恢复不是不可能但是很困难的情况下的一种严重问题，比如内存溢出，不可能指望程序处理这样的情况。

![img](image\PPjwP.png)

### throw vs throws 

throw 关键字用来在程序中明确的<u>抛出异常</u>，相反，throws 语句用来<u>表明方法不能处理的异常</u>。每一个方法都必须要指定哪些异常不能处理，方法的调用者才能够确保处理可能发生的异常，多个异常是用逗号分隔的。

###  final 关键字

final关键字主要用在三个地方：<u>变量、方法、类</u>。

1. 对于一个final变量，如果是基本数据类型的变量，则其数值一旦在初始化之后便不能更改；如果是引用类型的变量，则在对其初始化之后便不能再让其指向另一个对象。
2. 使用final方法的原因是把方法锁定，以防任何继承类修改它的含义；
3. 当用final修饰一个类时，表明这个类不能被继承。final类中的所有成员方法都会被隐式地指定为final方法。

### 四种访问修饰符

| 修饰符    | 当前类 | 当前包 | 子类 | 其他包 |
| --------- | ------ | ------ | ---- | ------ |
| public    | √      | √      | √    | √      |
| protected | √      | √      | √    | ×      |
| default   | √      | √      | ×    | ×      |
| private   | √      | ×      | ×    | ×      |

类的成员不写访问修饰时默认为default。

### IO 流的分类

- 按照流的**流向**分为输入流和输出流；
- 按照**操作单元**划分为字节流和字符流；
- 按照流的**角色**划分为节点流和处理流。

 Java I0流的40多个类都是从如下4个抽象类基类中派生出来的。

- InputStream/Reader: 所有输入流的基类，前者是字节输入流，后者是字符输入流。
- OutputStream/Writer: 所有输出流的基类，前者是字节输出流，后者是字符输出流。

### 字节流 vs 字符流

不管是文件读写还是网络发送接收，信息的最小存储单元都是字节，那为什么 I/O 流操作要分为字节流操作和字符流操作呢？

字符流是由 Java 虚拟机将字节转换得到的，问题就出在这个过程还算是非常耗时，并且，如果我们不知道编码类型就很容易出现乱码问题。所以， I/O  流就干脆提供了一个直接操作字符的接口，方便我们平时对字符进行流操作。如果音频文件、图片等媒体文件用字节流比较好，如果涉及到字符的话使用字符流比较好。

### BIO vs NIO vs AIO

- BIO (Blocking I/O)：**同步阻塞**I/O模式，<u>数据的读取写入必须阻塞在一个线程内等待其完成</u>。在活动连接数不是特别高（小于单机1000）的情况下，这种模型是比较不错的，可以让每一个连接专注于自己的 I/O  并且编程模型简单，也不用过多考虑系统的过载、限流等问题。线程池本身就是一个天然的漏斗，可以缓冲一些系统处理不了的连接或请求。但是，当面对十万甚至百万级连接的时候，传统的 BIO 模型是无能为力的。因此，我们需要一种更高效的 I/O 处理模型来应对更高的并发量。
- NIO (New I/O)：**同步非阻塞**的I/O模型，在Java 1.4 中引入了NIO框架，对应 java.nio 包，提供了 Channel ,  Selector，Buffer等抽象。NIO中的N可以理解为Non-blocking，不单纯是New。它支持面向缓冲的、基于通道的I/O操作方法。 NIO提供了两种不同的套接字通道实现，两种通道都支持阻塞和非阻塞两种模式。阻塞模式使用就像传统中的支持一样，比较简单，但是性能和可靠性都不好；非阻塞模式正好与之相反。对于低负载、低并发的应用程序，可以使用同步阻塞I/O来提升开发速率和更好的维护性；对于高负载、高并发的（网络）应用，应使用 NIO 的非阻塞模式来开发。
- AIO (Asynchronous I/O)：AIO 也就是  NIO 2。在 Java 7 中引入了 NIO 的改进版 NIO 2，它是**异步非阻塞**的IO模型。异步 IO  是基于事件和回调机制实现的，也就是应用操作之后会直接返回，不会堵塞在那里，当后台处理完成，操作系统会通知相应的线程进行后续的操作。AIO  是异步IO的缩写，虽然 NIO 在网络操作中，提供了非阻塞的方法，但是 NIO 的 IO 行为还是同步的。

### 编码与解码

编码就是把字符转换为字节，而解码是把字节重新组合成字符。

如果编码和解码过程使用不同的编码方式那么就出现了乱码。

- GBK 编码中，中文字符占 2 个字节，英文字符占 1 个字节；
- UTF-8 编码中，中文字符占 3 个字节，英文字符占 1 个字节；
- UTF-16be 编码中，中文字符和英文字符都占 2 个字节。

UTF-16be 中的 be 指的是 Big Endian，也就是大端。相应地也有 UTF-16le，le 指的是 Little Endian，也就是小端。

Java 的内存编码使用双字节编码 UTF-16be，这不是指 Java 只支持这一种编码方式，而是说 char 这种类型使用 UTF-16be  进行编码。char 类型占 16 位，也就是两个字节，Java 使用这种双字节编码是为了让一个中文或者一个英文都能使用一个 char 来存储。
