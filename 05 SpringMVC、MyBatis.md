# SpringMVC

### SpringMVC 介绍

SpringMVC 框架是以请求为驱动，围绕 Servlet 设计，将请求发给控制器，然后通过模型对象、分派器来展示请求结果视图。其核心类是实现了 Servlet 接口的 DispatcherServlet。

### SpringMVC 工作流程

SpringMVC的核心控制器是DispatcherServlet。

<img src="image\7b948963f66748798d25ad194708bc0c.png" alt="SpringMVC运行原理" style="zoom:80%;" />

**流程说明：**

1. 客户端（浏览器）发送请求，直接请求到 DispatcherServlet。
2. DispatcherServlet 根据请求信息调用 HandlerMapping，解析请求对应的 Handler。
3. 解析到对应的 Handler（也就是我们平常说的 Controller 控制器）后，开始由 HandlerAdapter 适配器处理。
4. HandlerAdapter 会根据 Handler 来调用真正的处理器处理请求。
5. 处理器处理完业务后，会返回一个 ModelAndView 对象，Model 是返回的数据对象，View 是个逻辑上的 View。
6. ViewResolver 会根据逻辑 View 查找实际的 View。
7. DispatcherServlet 把返回的 Model 传给 View（视图渲染）。
8. 把 View 返回给请求者（浏览器）。

### SpringMVC 重要组件说明

1、前端控制器DispatcherServlet

Spring MVC 的入口函数。接收请求、响应结果，相当于转发器、中央处理器。它就相当于mvc模式中的c，DispatcherServlet是整个流程控制的中心，由它调用其它组件处理用户的请求，DispatcherServlet的存在降低了组件之间的耦合性。

2、处理器映射器HandlerMapping

根据请求的url查找Handler即处理器（Controller）。SpringMVC提供了不同的映射器实现不同的映射方式，例如：配置文件方式、实现接口方式、注解方式等。

3、处理器适配器HandlerAdapter

按照特定规则（HandlerAdapter要求的规则）去执行Handler，这是适配器模式的应用，通过扩展适配器可以对更多类型的处理器进行执行。

4、处理器Handler

编写Handler时按照HandlerAdapter的要求去做，这样适配器才可以去正确执行Handler，Handler 是继DispatcherServlet前端控制器的后端控制器，在DispatcherServlet的控制下Handler对具体的用户请求进行处理。

5、视图解析器View resolver

进行视图解析，根据逻辑视图名解析成真正的视图（view）。View  Resolver首先根据逻辑视图名解析成物理视图名即具体的页面地址，再生成View视图对象，最后对View进行渲染将处理结果通过页面展示给用户。 springmvc框架提供了很多的View视图类型，包括：jstlView、freemarkerView、pdfView等。

6、视图View

View是一个接口，实现类支持不同的View类型（jsp、freemarker、pdf...）。

注意：处理器Handler（Controller控制器）以及视图层view都是需要我们自己手动开发的。其他的一些组件比如：前端控制器DispatcherServlet、处理器映射器HandlerMapping、处理器适配器HandlerAdapter等等都是框架提供给我们的，不需要自己手动开发。

### Cookie vs Session

Cookie 和 Session都是用来跟踪浏览器用户身份的会话方式，但是两者的应用场景不太一样。

1. 一般网站都会有保持登录也就是说下次再访问网站的时候就不需要重新登录了，这是因为用户登录的时候我们可以存放了一个 Token 在 Cookie 中，下次登录的时候只需要根据 Token 值来查找用户即可(为了安全考虑，重新登录一般要将 Token 重写)；

2. 登录一次网站后访问网站其他页面不需要重新登录。Session 的主要作用就是通过服务端记录用户的状态， 典型的场景是购物车，当你要添加商品到购物车的时候，系统不知道是哪个用户操作的，因为 HTTP 协议是无状态的。服务端给特定的用户创建特定的 Session 之后就可以标识这个用户并且跟踪这个用户了。

3. Cookie 存储在客户端中，而Session存储在服务器上，相对来说 Session 安全性更高。

# MyBatis

### 占位符#、$ 的区别

1. #将传入的数据都当做一个字符串，会对传入的数据加一个双引号；
2. $将传入的数据直接显示在 SQL 中；
3. #存在预编译的过程，对问号赋值，防止 SQL 注入；
4. $是直译的方式，一般用在 order by ${列名}语句中；
5. 能用#号就不要用 $ 符号。

