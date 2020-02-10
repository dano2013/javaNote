



# Spring

### 列举一些重要的Spring模块

<img src="D:\pic\markdown\面试题总结\05 Mybatis、Spring、Spring MVC 、Spring boot\4d851e51106f4bc4b11c1cd0b3d4a4c4.png" alt="Spring主要模块" style="zoom: 80%;" />

- Spring Core： Spring 其他所有的功能都需要依赖于该类库，主要提供 IoC 依赖注入功能
- Spring AOP ：提供了面向切面的编程实现
- Spring JDBC : Java数据库连接
- Spring ORM : 用于支持Hibernate等ORM工具
- Spring JMS ：Java消息服务
- Spring Web : 为创建Web应用程序提供支持
- Spring Test : 提供了对 JUnit 和 TestNG 测试的支持

### Spring 用到了哪些设计模式

- 工厂设计模式：Spring使用工厂模式通过 BeanFactory、ApplicationContext 创建 bean 对象。
- 代理设计模式：Spring AOP 功能的实现。
- 单例设计模式：Spring 中的 Bean 默认都是单例的。
- 模板方法模式：Spring 中 jdbcTemplate、hibernateTemplate 等以 Template 结尾的对数据库操作的类，它们就使用到了模板模式。
- 包装器设计模式：这种模式让我们可以根据客户的需求能够动态切换不同的数据源。
- 观察者模式：Spring 事件驱动模型就是观察者模式很经典的一个应用。
- 适配器模式：Spring AOP 的增强或通知(Advice)使用到了适配器模式、spring MVC 中也是用到了适配器模式适配Controller。

## 事务

事务是逻辑上的一组操作，要么都执行，要么都不执行。

### 管理事务的方式

1. 编程式事务，在代码中硬编码(不推荐使用)
2. 声明式事务，在配置文件中配置（推荐使用）
   - 基于XML的声明式事务
   - 基于注解的声明式事务

### 事务隔离级别

TransactionDefinition 接口中定义了五个表示隔离级别的常量：

- ISOLATION_DEFAULT：使用后端数据库默认的隔离级别，Mysql 默认采用的 REPEATABLE_READ隔离级别，Oracle 默认采用的 READ_COMMITTED隔离级别。
- ISOLATION_READ_UNCOMMITTED：最低的隔离级别，允许读取尚未提交的数据变更，可能会导致脏读、幻读或不可重复读。
- ISOLATION_READ_COMMITTED：允许读取并发事务已经提交的数据，可以阻止脏读，但是不可重复读和幻读仍有可能发生。
- ISOLATION_REPEATABLE_READ: 对同一字段的多次读取结果都是一致的，除非数据是被本身事务自己所修改，可以阻止脏读和不可重复读，但幻读仍有可能发生。
- ISOLATION_SERIALIZABLE: 最高的隔离级别，完全服从ACID的隔离级别。所有的事务依次逐个执行，这样事务之间就完全不可能产生干扰，也就是说，该级别可以防止脏读、不可重复读以及幻读。但是这将严重影响程序的性能，通常情况下也不会用到该级别。

### 事务传播行为

支持当前事务的情况：

- PROPAGATION_REQUIRED： 如果当前存在事务，则加入该事务；如果当前没有事务，则创建一个新的事务。
- PROPAGATION_SUPPORTS： 如果当前存在事务，则加入该事务；如果当前没有事务，则以非事务的方式继续运行。
- PROPAGATION_MANDATORY： 如果当前存在事务，则加入该事务；如果当前没有事务，则抛出异常。（mandatory：强制性）

不支持当前事务的情况：

- PROPAGATION_REQUIRES_NEW： 创建一个新的事务，如果当前存在事务，则把当前事务挂起。
- PROPAGATION_NOT_SUPPORTED： 以非事务方式运行，如果当前存在事务，则把当前事务挂起。
- PROPAGATION_NEVER： 以非事务方式运行，如果当前存在事务，则抛出异常。

其他情况：

- PROPAGATION_NESTED： 如果当前存在事务，则创建一个事务作为当前事务的嵌套事务来运行；如果当前没有事务，则该取值等价于PROPAGATION_REQUIRED。

## IoC & AOP

### IoC

IoC（Inverse of Control：控制反转）是一种解耦的设计思想。<u>将原本在程序中手动创建对象的控制权、对象之间的相互依赖关系交给 IoC 容器来管理，并由 IoC 容器完成对象的注入，去除了高层模块对低层模块的依赖。</u>当我们需要创建一个对象的时候，只需要配置好配置文件/注解即可，完全不用考虑对象是如何被创建出来的。IOC 容器负责创建对象，将对象连接在一起，配置这些对象，并从创建中处理这些对象的整个生命周期，直到它们被完全销毁。

为什么叫做“反转”呢？在引入依赖注入思想前，我们代码的高层模块内部包含有低层模块，也就是说我们高层模块从内部依赖于低层模块，然而现在高层模块的内部并没有任何关于低层模块的实现、实例化和销毁，反而是低层模块需要根据配置决定它要被注入到哪里，也就是所谓的“控制反转”。

Spring IoC的初始化过程：

<img src="D:\pic\markdown\面试题总结\05 Mybatis、Spring、Spring MVC 、Spring boot\4a7ef6d2553a4179a6f2c73722e730ea.png" alt="Spring IoC的初始化过程" style="zoom:80%;" />

Spring 时代我们一般通过 XML 文件来配置 Bean，后来开发人员觉得 XML 文件来配置不太好，于是 SpringBoot 的注解配置就慢慢开始流行起来。

**DI(Dependecy Inject,依赖注入)是实现控制反转的一种设计模式，依赖注入就是将实例变量传入到一个对象中去。**

### AOP

AOP(Aspect-Oriented Programming：面向切面编程)<u>能够将那些与业务无关，却为业务模块所共同调用的功能代码（例如事务处理、日志管理、权限控制等）从业务逻辑代码中分离出来</u>，便于减少系统的重复代码，降低模块间的耦合度，并有利于未来的可拓展性和可维护性。

### AOP 的实现方式

JDK 动态代理实现和 cglib 实现

- 如果目标对象实现了接口，默认情况下会采用 JDK 的动态代理实现，也可以强制使用 cglib 实现；
- 如果目标对象没有实现接口，必须采用 cglib 库，Spring 会自动在 JDK 动态代理和 cglib 之间转换。

JDK 动态代理原理是通过在运行期间创建一个接口的实现类来完成对目标对象的代理。

1. 定义一个实现接口 InvocationHandler 的类；
2. 通过构造函数，注入被代理类；
3. 实现 invoke（ Object proxy, Method method, Object[] args）方法；
4. 在主函数中获得被代理类的类加载器；
5. 使用 Proxy.newProxyInstance( ) 产生一个代理对象；
6. 通过代理对象调用各种方法。

### Spring AOP vs AspectJ AOP 

**Spring AOP 属于运行时增强，而 AspectJ 是编译时增强。** Spring AOP 基于代理(Proxying)，而 AspectJ 基于字节码操作(Bytecode Manipulation)。

 Spring AOP 已经集成了 AspectJ  ，AspectJ  应该算的上是 Java 生态系统中最完整的 AOP 框架了。AspectJ  相比于 Spring AOP 功能更加强大，但是 Spring AOP 相对来说更简单，

如果我们的切面比较少，那么两者性能差异不大。但是，当切面太多的话，最好选择 AspectJ ，它比Spring AOP 快很多。

## bean

### @Component vs @Bean

1. 作用对象不同：@Component 注解作用于类，而@Bean注解作用于方法。
2. @Component通常是通过类路径扫描来自动侦测以及自动装配到Spring容器中的；@Bean 通常是我们在标有该注解的方法中定义产生这个 bean，@Bean告诉了Spring这是某个类的示例，当我需要用它的时候还给我。
3. @Bean 比 @Component 的自定义性更强，而且很多地方我们只能通过 @Bean 注解来注册bean。比如当我们引用第三方库中的类需要装配到 Spring容器时，则只能通过 @Bean来实现。

### 声明为 bean 的注解有哪些

我们一般使用 @Autowired 注解自动装配 bean，要想把类标识成可用于 @Autowired 注解自动装配的 bean 的类，采用以下注解可实现：

- @Component ：通用的注解，可标注任意类为 Spring 组件。如果一个Bean不知道属于哪个层，可以使用@Component 注解标注。
- @Repository : 对应持久层即 Dao 层，主要用于数据库相关操作。
- @Service : 对应服务层，主要涉及一些复杂的逻辑，需要用到 Dao层。
- @Controller : 对应 Spring MVC 控制层，主要用于接受用户请求并调用 Service 层返回数据给前端页面。

### bean 的作用域

- singleton : 唯一 bean 实例，Spring 中的 bean 默认都是单例的。
- prototype : 每次请求都会创建一个新的 bean 实例。
- request : 每一次HTTP请求都会产生一个新的bean，该bean仅在当前HTTP request内有效。
- session : 每一次HTTP请求都会产生一个新的 bean，该bean仅在当前 HTTP session 内有效。
- global-session： 全局session作用域，仅仅在基于portlet的web应用中才有意义，Spring5已经没有了。Portlet是能够生成语义代码(例如：HTML)片段的小型Java Web插件。它们基于portlet容器，可以像servlet一样处理HTTP请求。但是，与 servlet 不同，每个 portlet 都有不同的会话。

### bean 的生命周期

- Bean 容器找到配置文件中 Spring Bean 的定义。
- Bean 容器利用 Java Reflection API 创建一个Bean的实例。
- 如果涉及到一些属性值利用 set()方法设置一些属性值。
- 如果 Bean 实现了 BeanNameAware 接口，调用 setBeanName()方法，传入Bean的名字。
- 如果 Bean 实现了 BeanClassLoaderAware 接口，调用 setBeanClassLoader()方法，传入 ClassLoader对象的实例。
- 如果Bean实现了 BeanFactoryAware 接口，调用 setBeanClassLoader()方法，传入 ClassLoader对象的实例。
- 与上面的类似，如果实现了其他 *.Aware接口，就调用相应的方法。
- 如果有和加载这个 Bean 的 Spring 容器相关的 BeanPostProcessor 对象，执行postProcessBeforeInitialization() 方法
- 如果Bean实现了InitializingBean接口，执行afterPropertiesSet()方法。
- 如果 Bean 在配置文件中的定义包含 init-method 属性，执行指定的方法。
- 如果有和加载这个 Bean的 Spring 容器相关的 BeanPostProcessor 对象，执行postProcessAfterInitialization() 方法。
- 当要销毁 Bean 的时候，如果 Bean 实现了 DisposableBean 接口，执行 destroy() 方法。
- 当要销毁 Bean 的时候，如果 Bean 在配置文件中的定义包含 destroy-method 属性，执行指定的方法。

<img src="https://i.loli.net/2018/09/20/5ba2e83a54fd9.jpeg" style="zoom:80%;" />

> Spring 容器可以管理  singleton 作用域下 bean 的生命周期，在此作用域下，Spring  能够精确地知道bean何时被创建，何时初始化完成，以及何时被销毁。而对于 prototype 作用域的bean，Spring只负责创建，当容器创建了 bean 的实例后，bean  的实例就交给了客户端的代码管理，Spring容器将不再跟踪其生命周期，并且不会管理那些被配置成prototype作用域的bean的生命周期。

### 单例 bean 的线程安全问题

当多个线程操作同一个对象的时候，对这个对象的非静态成员变量的写操作会存在线程安全问题。

常见的有两种解决办法：

1. 在Bean对象中尽量避免定义可变的成员变量（不太现实）
2. 在类中定义一个ThreadLocal成员变量，将需要的可变成员变量保存在 ThreadLocal 中

### @RestController vs @Controller

- @Controller 返回一个页面


单独使用 @Controller 不加 @ResponseBody 的话一般使用在要返回一个视图的情况，这种情况属于比较传统的Spring MVC 应用，对应于前后端不分离的情况。

- @RestController 返回JSON 或 XML 形式数据


@RestController只返回对象，对象数据直接以 JSON 或 XML 形式写入 HTTP 响应(Response)中，这种情况属于 RESTful Web服务，这也是目前日常开发所接触的最常用的情况（前后端分离）。

如果你需要在Spring4之前开发 RESTful Web服务的话，你需要使用@Controller 并结合@ResponseBody注解，也就是说<u>@Controller +@ResponseBody= @RestController</u>（Spring 4 之后新加的注解）。

@ResponseBody 的作用是将 Controller 的方法返回的对象通过适当的转换器转换为指定的格式之后，写入到HTTP 响应(Response)对象的 body 中，通常用来返回 JSON 或者 XML 数据。

### @Transactional

当@Transactional注解作用于类上时，该类的所有 public 方法将都具有该类型的事务属性，同时我们也可以在方法级别使用该标注来覆盖类级别的定义。如果类或者方法加了这个注解，那么这个类里面的方法抛出异常时就会回滚，数据库里面的数据也会回滚。

在@Transactional注解中如果不配置rollbackFor属性,那么事物只会在遇到RuntimeException的时候才会回滚,加上rollbackFor=Exception.class，可以让事务在遇到非运行时异常时也回滚。

### 如何使用JPA在数据库中非持久化一个字段

```java
Entity(name="USER")
public class User {
    private String secrect;	//未修改前
    static String transient1; 			// not persistent because of static
    final String transient2 = “Satish”; // not persistent because of final
    transient String transient3; 		// not persistent because of transient
    @Transient
    String transient4; 					// not persistent because of @Transient
}
```

# SpringMVC

### SpringMVC 介绍

SpringMVC 框架是以请求为驱动，围绕 Servlet 设计，将请求发给控制器，然后通过模型对象、分派器来展示请求结果视图。其核心类是实现了 Servlet 接口的 DispatcherServlet。

### SpringMVC 工作流程

SpringMVC的核心控制器是DispatcherServlet。

<img src="D:\pic\markdown\面试题总结\05 Mybatis、Spring、Spring MVC 、Spring boot\7b948963f66748798d25ad194708bc0c.png" alt="SpringMVC运行原理" style="zoom:80%;" />

**流程说明：**

1. 客户端（浏览器）发送请求，直接请求到 DispatcherServlet。
2. DispatcherServlet 根据请求信息调用 HandlerMapping，解析请求对应的 Handler。
3. 解析到对应的 Handler（也就是我们平常说的 Controller 控制器）后，开始由 HandlerAdapter 适配器处理。
4. HandlerAdapter 会根据 Handler来调用真正的处理器处理请求。
5. 处理器处理完业务后，会返回一个 ModelAndView 对象，Model 是返回的数据对象，View 是个逻辑上的 View。
6. ViewResolver 会根据逻辑 View 查找实际的 View。
7. DispatcherServlet 把返回的 Model 传给 View（视图渲染）。
8. 把 View 返回给请求者（浏览器）。

### SpringMVC 重要组件说明

1、前端控制器DispatcherServlet

作用：Spring MVC 的入口函数。接收请求，响应结果，相当于转发器，中央处理器。有了 DispatcherServlet  减少了其它组件之间的耦合度。用户请求到达前端控制器，它就相当于mvc模式中的c，DispatcherServlet是整个流程控制的中心，由它调用其它组件处理用户的请求，DispatcherServlet的存在降低了组件之间的耦合性。

2、处理器映射器HandlerMapping

作用：根据请求的url查找Handler。HandlerMapping负责根据用户请求找到Handler即处理器（Controller），SpringMVC提供了不同的映射器实现不同的映射方式，例如：配置文件方式，实现接口方式，注解方式等。

3、处理器适配器HandlerAdapter

作用：按照特定规则（HandlerAdapter要求的规则）去执行Handler，通过HandlerAdapter对处理器进行执行，这是适配器模式的应用，通过扩展适配器可以对更多类型的处理器进行执行。

4、处理器Handler

注意：编写Handler时按照HandlerAdapter的要求去做，这样适配器才可以去正确执行Handler，Handler 是继DispatcherServlet前端控制器的后端控制器，在DispatcherServlet的控制下Handler对具体的用户请求进行处理。

5、视图解析器View resolver

作用：进行视图解析，根据逻辑视图名解析成真正的视图（view），View Resolver负责将处理结果生成View视图，View  Resolver首先根据逻辑视图名解析成物理视图名即具体的页面地址，再生成View视图对象，最后对View进行渲染将处理结果通过页面展示给用户。 springmvc框架提供了很多的View视图类型，包括：jstlView、freemarkerView、pdfView等。 一般情况下需要通过页面标签或页面模版技术将模型数据通过页面展示给用户，需要由工程师根据业务需求开发具体的页面。

6、视图View

View是一个接口，实现类支持不同的View类型（jsp、freemarker、pdf...）

注意：处理器Handler（也就是我们平常说的Controller控制器）以及视图层view都是需要我们自己手动开发的。其他的一些组件比如：前端控制器DispatcherServlet、处理器映射器HandlerMapping、处理器适配器HandlerAdapter等等都是框架提供给我们的，不需要自己手动开发。

### Cookie vs Session

Cookie 和 Session都是用来跟踪浏览器用户身份的会话方式，但是两者的应用场景不太一样。

①我们在 Cookie 中保存已经登录过的用户信息，下次访问网站的时候页面可以自动帮你把登录的一些基本信息给填了；

②一般的网站都会有保持登录也就是说下次你再访问网站的时候就不需要重新登录了，这是因为用户登录的时候我们可以存放了一个 Token 在 Cookie 中，下次登录的时候只需要根据 Token 值来查找用户即可(为了安全考虑，重新登录一般要将 Token 重写)；

③登录一次网站后访问网站其他页面不需要重新登录。Session 的主要作用就是通过服务端记录用户的状态， 典型的场景是购物车，当你要添加商品到购物车的时候，系统不知道是哪个用户操作的，因为 HTTP 协议是无状态的。服务端给特定的用户创建特定的 Session 之后就可以标识这个用户并且跟踪这个用户了。

Cookie 存储在客户端中，而Session存储在服务器上，相对来说 Session 安全性更高。

