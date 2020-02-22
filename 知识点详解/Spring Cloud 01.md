## 01、系统架构演变

​		随着互联网的发展，网站应用的规模不断扩大。需求的激增，带来的是技术上的压力。系统架构也因此也不断的演进、升级、迭代。从单一应用，到垂直拆分，到分布式服务，到SOA，以及现在火热的微服务架构，还有在Google带领下来势汹涌的Service Mesh。



### 1.1 单一应用构架

------

当网站流量很小时，只需一个应用，将所有功能都部署在一起，以减少部署节点和成本。此时，用于简化增删改查工作量的数据访问框架(ORM)是关键。

<img src="D:/pic/markdown/SpringCloud/1571629658566.png" alt="1571629658566" style="zoom: 80%;" /> 

**存在的问题：**

**- 代码耦合，开发维护困难**

**- 无法针对不同模块进行针对性优化**

**- 无法水平扩展**

**- 单点容错性差，并发能力差**

### 1.2 垂直应用架构

------

当访问量逐渐增大，单一应用增加机器带来的加速度越来越小，将应用拆成互不相干的几个应用，以提升效率。此时，用于加速前端页面开发的Web框架(MVC)是关键。

<img src="D:/pic/markdown/SpringCloud/1571629692382.png" alt="1571629692382" style="zoom:50%;" /> 

**优点：**

**- 系统拆分实现了流量分担，解决了并发问题**

**- 可以针对不同模块进行优化**

**- 方便水平扩展，负载均衡，容错性提高**

**缺点：**

**- 系统间相互独立，会有很多重复开发工作，影响开发效率**



### 1.3 分布式服务架构

------

当垂直应用越来越多，应用之间交互不可避免，将核心业务抽取出来，作为独立的服务，逐渐形成稳定的服务中心，使前端应用能更快速的响应多变的市场需求。此时，用于提高业务复用及整合的分布式服务框架(RPC)是关键。

<img src="D:/pic/markdown/SpringCloud/1571629942430.png" alt="1571629942430" style="zoom:50%;" /> 

**优点：**

**- 将基础服务进行了抽取，系统间相互调用，提高了代码复用和开发效率**

**缺点：**

**- 系统间耦合度变高，调用关系错综复杂，难以维护**



### 1.4 面向服务架构(SOA)

------

当服务越来越多，容量的评估，小服务资源的浪费等问题逐渐显现，此时需增加一个调度中心基于访问压力实时管理集群容量，提高集群利用率。此时，用于提高机器利用率的资源调度和治理中心(SOA)是关键。

![img](D:/pic/markdown/SpringCloud/wps4.jpg) 

以前出现了什么问题？

\- 服务越来越多，需要管理每个服务的地址

\- 调用关系错综复杂，难以理清依赖关系

\- 服务过多，服务状态难以管理，无法根据服务情况动态管理

 

**服务治理要做什么？**

**- 服务注册中心，实现服务自动注册和发现，无需人为记录服务地址**

**- 服务自动订阅，服务列表自动推送，服务调用透明化，无需关心依赖关系**

**- 动态监控服务状态监控报告，人为控制服务状态**

 

**缺点：**

**- 服务间会有依赖关系，一旦某个环节出错会影响较大**

**- 服务关系复杂，运维、测试部署困难，不符合DevOps思想**

> DevOps（Development和Operations的组合词）是一组过程、方法与系统的统称，用于促进开发（应用程序/软件工程）、技术运营和质量保障（QA）部门之间的沟通、协作与整合 



### 1.5 微服务架构

------

**微服务架构**是一种使用一套小服务来开发单个应用的方式或途径，每个服务运行在自己的进程中，并使用轻量级机制通信，通常是HTTP API，这些服务基于业务能力构建，并能够通过自动化部署机制来独立部署，这些服务使用不同的编程语言实现，以及不同数据存储技术，并保持最低限度的集中式管理。 



微服务，似乎也是服务，都是对系统进行拆分。因此两者比较类似，但其实也有一些差别： 

微服务的特点： 

+ 单一职责：微服务中每一个服务都对应唯一的业务能力，做到**单一职责** 
+ 微：微服务的服务拆分**粒度很小**，例如一个用户管理就可以作为一个服务。每个服务虽小，但“五脏俱全”。 
+ 面向服务：**面向服务**是说每个服务都要对外暴露Rest风格服务接口API。并不关心服务的技术实现，做到与平台和语言无关，也不限定用什么技术实现，只要提供Rest的接口即可。 
+ 自治：自治是说**服务间互相独立**，互不干扰 
  + 团队独立：每个服务都是一个独立的开发团队，人数不能过多。 
  + 技术独立：因为是面向服务，提供Rest接口，使用什么技术没有别人干涉 
  + 前后端分离：采用前后端分离开发，提供统一Rest接口，后端不用再为PC、移动端开发不同接口 
  + 数据库分离：每个服务都使用自己的数据源 
  + 部署独立：服务间虽然有调用，但要做到服务重启不影响其它服务。有利于持续集成和持续交付。每个服务都是独立的组件，可复用，可替换，降低耦合，易维护

**微服务架构图：**

<img src="D:/pic/markdown/SpringCloud/wps6.jpg" alt="img" style="zoom:67%;" /> 



## 02、远程调用方式介绍

### 2.1 RPC&HTTP

------

无论是微服务还是SOA，都面临着服务间的远程调用。那么服务间的远程调用方式有哪些呢？ 

常见的远程调用方式有以下2种： 

+ **RPC**：Remote Produce Call远程过程调用，**RPC基于Socket，工作在会话层。自定义数据格式**，速度快，效率高。早期的webservice，现在热门的dubbo，都是RPC的典型代表 。

+ **Http**：http其实是**一种网络传输协议，基于TCP，工作在应用层，规定了数据传输的格式**。现在客户端浏览器与服务端通信基本都是采用Http协议，也可以用来进行远程服务调用。缺点是消息封装臃肿，优势是对服务的提供和调用方没有任何技术限定，自由灵活，更符合微服务理念。 

  现在热门的Rest风格，就可以通过http协议来实现。 

**区别**：RPC的机制是根据语言的API（language API）来定义的，而不是根据基于网络的应用来定义的。 

如果你们公司全部采用Java技术栈，那么使用Dubbo作为微服务架构是一个不错的选择。 

相反，如果公司的技术栈多样化，而且你更青睐Spring家族，那么Spring Cloud搭建微服务是不二之选。在我们的项目中，会选择Spring Cloud套件，因此会使用Http方式来实现服务间调用。 

### 2.2 Http客户端工具 

------

既然微服务选择了Http，那么我们就需要考虑自己来实现对请求和响应的处理。不过开源世界已经有很多的http客户端工具，能够帮助我们做这些事情，例如： 

+ HttpClient (<http://hc.apache.org/>)

  ![1571633337739](D:/pic/markdown/SpringCloud/1571633337739.png)

+ OkHttp (<https://square.github.io/okhttp/>)

  ![1571633234904](D:/pic/markdown/SpringCloud/1571633234904.png)

+ URLConnection (JDK原生) 

> 说明：不同的客户端，API各不相同



### 2.3 Spring的RestTemplate 

------

spring-web模块提供了一个RestTemplate模板工具类，对基于Http的客户端进行了封装，并且实现了对象与json的序列化和反序列化，非常方便。RestTemplate并没有限定Http的客户端类型，而是进行了抽象，目前常用的3种都有支持： 

+ HttpClient 
+ OkHttp 
+ URLConnection

> 说明：URLConnection为JDK原生，也是RestTemplate默认的选择。

## 03、RestTemplate远程调用

**目标**

> 学会使用RestTemplate实现远程调用

**实现步骤**

+ 搭建项目(http-demo)

  ![1571670646861](D:/pic/markdown/SpringCloud/1571670646861.png)&nbsp;

+ 配置部分

  + pom.xml

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <project xmlns="http://maven.apache.org/POM/4.0.0"
             xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
             xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
             http://maven.apache.org/xsd/maven-4.0.0.xsd">
        <modelVersion>4.0.0</modelVersion>
        <parent>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-parent</artifactId>
            <version>2.1.6.RELEASE</version>
        </parent>
        <groupId>cn.itcast</groupId>
        <artifactId>http-demo</artifactId>
        <version>1.0-SNAPSHOT</version>
    
        <dependencies>
            <!-- web启动器 -->
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-web</artifactId>
            </dependency>
            <!-- test启动器 -->
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-test</artifactId>
                <scope>test</scope>
            </dependency>
        </dependencies>
    </project>
    ```

  + application.yml

    ```yml
    server:
      port: 8080
    ```

+ 编程部分

  + 运行主类：cn.itcast.Application.java

  ```java
  @SpringBootApplication
  public class Application {
      public static void main(String[] args){
          SpringApplication.run(Application.class, args);
      }
      @Bean
      public RestTemplate restTemplate(){
          return new RestTemplate();
      }
  }
  ```

  + 测试类：cn.itcast.RestTemplateTest.java

    ```java
    @RunWith(SpringRunner.class)
    @SpringBootTest
    public class RestTemplateTest {
        @Autowired
        private RestTemplate restTemplate;
        /** 发送get请求 */
        @Test
        public void sendGet() {
            String content = restTemplate.getForObject("https://www.jd.com",
                    String.class);
            System.out.println("content = " + content);
        }
        /** 发送post请求 */
        @Test
        public void sendPost() {
            String content = restTemplate.postForObject("https://www.jd.com",
                    HttpEntity.EMPTY, String.class);
            System.out.println("content = " + content);
        }
    }
    ```

**小结**

> get请求：restTemplate.getForObject("请求url", "响应数据类型");
>
> post请求：restTemplate.postForObject("请求url","请求头|请求体","响应数据类型");
>
> 说明：如果响应数据为json字符串，响应数据类型可以直接用实体类接收，已经帮我们进行了反序列化

## 04、SpringCloud介绍

微服务是一种架构方式，最终肯定需要技术架构去实施。 

微服务的实现方式很多，但是最火的莫过于Spring Cloud了。为什么？ 

+ 后台硬：作为Spring家族的一员，有整个Spring全家桶靠山，背景十分强大。 
+ 技术强：Spring作为Java领域的前辈，可以说是功力深厚。有强力的技术团队支撑，一般人还真比不了
+ 群众基础好：可以说大多数程序员的成长都伴随着Spring框架，试问：现在有几家公司开发不用Spring？Spring Cloud与Spring的各个框架无缝整合，对大家来说一切都是熟悉的配方，熟悉的味道。 
+ 使用方便：相信大家都体会到了SpringBoot给我们开发带来的便利，而Spring Cloud完全支持Spring Boot的开发，用很少的配置就能完成微服务框架的搭建。

### 4.1 简介

------

D:/pic/markdown/SpringCloud是Spring旗下的项目之一，官网地址：

<https://spring.io/projects/spring-cloud> 

![1571813318337](D:/pic/markdown/SpringCloud/1571813318337.png)

Spring最擅长的就是集成，把世界上最好的框架拿过来，集成到自己的项目中。

Spring Cloud也是一样，它将现在非常流行的一些技术整合到一起，实现了诸如：配置管理，服务发现，智能路由，负载均衡，熔断器，控制总线，集群状态等功能；协调分布式环境中各个系统，为各类服务提供模板性配置。其主要涉及的组件包括：

+ Eureka：注册中心 
+ Zuul、Gateway：服务网关 
+ Ribbon：负载均衡 
+ Feign：服务调用 
+ Hystix：熔断器 

 以上只是其中一部分，架构图：

![1571812412767](D:/pic/markdown/SpringCloud/1571812412767.png)

### 4.2 版本

------

Spring Cloud的版本命名比较特殊，因为它不是一个组件，而是许多组件的集合，它的命名是以A到Z的为首字母的一些单词（其实是伦敦地铁站的名字）组成： 

![1571814169723](D:/pic/markdown/SpringCloud/1571814169723.png)

我们在项目中，使用最新稳定的Greenwich版本。

Greenwich版本包含的组件，也都有各自的版本，如下表:

![1571814721258](D:/pic/markdown/SpringCloud/1571814721258.png)

## 05、搭建微服务模拟场景

### 5.1 创建父工程

------

微服务中需要同时创建多个项目，为了方便课堂演示，先创建一个父工程，然后后续的工程都以这个工程为父，实现maven的聚合。这样可以在一个窗口看到所有工程，方便讲解。**在实际开发中，每个微服务可独立一个工程**。 

![1571815268138](D:/pic/markdown/SpringCloud/1571815268138.png)

编写项目信息： 

![1571815365792](D:/pic/markdown/SpringCloud/1571815365792.png)

编写保存位置： 

![1571815415545](D:/pic/markdown/SpringCloud/1571815415545.png)

配置pom.xml:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
         http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>cn.itcast</groupId>
    <artifactId>D:/pic/markdown/SpringCloud-demo</artifactId>
    <version>1.0-SNAPSHOT</version>
    <!-- 配置父级 -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.1.6.RELEASE</version>
    </parent>

    <!-- 配置全局属性 -->
    <properties>
        <java.version>1.8</java.version>
        <spring-cloud.version>Greenwich.SR2</spring-cloud.version>
        <mapper.version>2.1.5</mapper.version>
        <mysql.version>5.1.47</mysql.version>
    </properties>

    <dependencyManagement>
        <dependencies>
            <!-- spring-cloud (导入pom文件)
                 scope: import 只能在<dependencyManagement>元素里面配置
             -->
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>${spring-cloud.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
            <!-- 通用mapper启动器 -->
            <dependency>
                <groupId>tk.mybatis</groupId>
                <artifactId>mapper-spring-boot-starter</artifactId>
                <version>${mapper.version}</version>
            </dependency>
            <!-- mysql驱动 -->
            <dependency>
                <groupId>mysql</groupId>
                <artifactId>mysql-connector-java</artifactId>
                <version>${mysql.version}</version>
            </dependency>
        </dependencies>
    </dependencyManagement>

    <dependencies>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <!-- 配置spring-boot的maven插件 -->
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>
</project>
```

说明：这里已经对大部分要用到的依赖的版本进行了管理，方便后续使用。

![1571816322635](D:/pic/markdown/SpringCloud/1571816322635.png)



### 5.2 服务提供者

------

新建一个模块user-service，对外提供查询用户的服务。

**创建模块**

选中父工程：D:/pic/markdown/SpringCloud-demo

![1571817432283](D:/pic/markdown/SpringCloud/1571817432283.png)

![1571817525684](D:/pic/markdown/SpringCloud/1571817525684.png)

注意: 子模块要在父工程的下级目录

![1571817613071](D:/pic/markdown/SpringCloud/1571817613071.png)

项目结构：

![1571818796480](D:/pic/markdown/SpringCloud/1571818796480.png)

**配置依赖**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
         http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <parent>
        <artifactId>D:/pic/markdown/SpringCloud-demo</artifactId>
        <groupId>cn.itcast</groupId>
        <version>1.0-SNAPSHOT</version>
    </parent>
    <modelVersion>4.0.0</modelVersion>
    <artifactId>user-service</artifactId>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>tk.mybatis</groupId>
            <artifactId>mapper-spring-boot-starter</artifactId>
        </dependency>
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
        </dependency>
    </dependencies>
</project>
```

**编写代码**

属性文件,这里我们采用了yaml语法(application.yml)，而不是properties： 

```properties
server:
  port: 9001
spring:
  datasource:
    driver-class-name: com.mysql.jdbc.Driver
    url: jdbc:mysql://localhost:3306/D:/pic/markdown/SpringCloud
    username: root
    password: root
mybatis:
  configuration:
    map-underscore-to-camel-case: true
  type-aliases-package: cn.itcast.user.pojo
```

使用工具创建 D:/pic/markdown/SpringCloud 数据库，将 资料\tb_user.sql 导入。

启动类： 

```java
package cn.itcast.user;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import tk.mybatis.spring.annotation.MapperScan;

@SpringBootApplication
@MapperScan("cn.itcast.user.mapper")
public class UserApplication {

    public static void main(String[] args){
        SpringApplication.run(UserApplication.class, args);
    }
}
```

实体类： 

```java
package cn.itcast.user.pojo;

import lombok.Data;
import tk.mybatis.mapper.annotation.KeySql;
import javax.persistence.Id;
import javax.persistence.Table;
import java.util.Date;

/** 实体类 */
@Table(name = "tb_user")
@Data
public class User {
    @Id
    @KeySql(useGeneratedKeys = true)
    private Long id;
    // 账号
    private String username;
    // 密码
    private String password;
    // 姓名
    private String name;
    // 年龄
    private Integer age;
    // 性别
    private Integer sex;
    // 生日
    private Date birthday;
    // 创建日期
    private Date created;
    // 修改日期
    private Date updated;
}
```

mapper: 

```java
package cn.itcast.user.mapper;

import cn.itcast.user.pojo.User;
import tk.mybatis.mapper.common.Mapper;

/** 数据访问接口 */
public interface UserMapper extends Mapper<User> {
}
```

service：

```java
package cn.itcast.user.service;

import cn.itcast.user.mapper.UserMapper;
import cn.itcast.user.pojo.User;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;

/** 业务层 */
@Service
@Transactional
public class UserService {

    @Autowired(required = false)
    private UserMapper userMapper;

    /** 根据主键id查询用户 */
    public User findOne(Long id){
        return userMapper.selectByPrimaryKey(id);
    }
}
```

添加一个对外查询的接口： 

```java
package cn.itcast.user.controller;

import cn.itcast.user.pojo.User;
import cn.itcast.user.service.UserService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
@RequestMapping("/user")
public class UserController {

    @Autowired
    private UserService userService;
    
    /** 根据主键id查询用户 */
    @GetMapping("/{id}")
    public User findOne(@PathVariable("id")Long id){
        return userService.findOne(id);
    }
}
```

项目结构： 

<img src="D:/pic/markdown/SpringCloud/1571822761843.png" alt="1571822761843" style="zoom:50%;" />&nbsp;

**启动测试**

启动项目，访问接口：http://localhost:9001/user/1

![1571823023031](D:/pic/markdown/SpringCloud/1571823023031.png)

### 5.3 服务消费者

------

**创建模块**

与上面类似，这里不再赘述，需要注意的是，我们调用user-service的功能，因此不需要mybatis相关依赖了。 

![1571823843833](D:/pic/markdown/SpringCloud/1571823843833.png)

![1571823915499](D:/pic/markdown/SpringCloud/1571823915499.png)

pom.xml:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
         http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <parent>
        <artifactId>D:/pic/markdown/SpringCloud-demo</artifactId>
        <groupId>cn.itcast</groupId>
        <version>1.0-SNAPSHOT</version>
    </parent>
    <modelVersion>4.0.0</modelVersion>
    <artifactId>user-consumer</artifactId>
    
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
    </dependencies>
</project>
```

application.yml

```properties
server:
  port: 8080
```

模块结构： 

![1571842190944](D:/pic/markdown/SpringCloud/1571842190944.png)&nbsp;

**编写代码**

首先在启动类中注册 RestTemplate:

```java
package cn.itcast.consumer;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.annotation.Bean;
import org.springframework.web.client.RestTemplate;

@SpringBootApplication
public class ConsumerApplication {
    
    public static void main(String[] args){
        SpringApplication.run(ConsumerApplication.class, args);
    }
    @Bean
    public RestTemplate restTemplate(){
        return new RestTemplate();
    }
}
```

实体类： 

```java
/** 实体类 */
@Data
public class User {
    // 编号
    private Long id;
    // 账号
    private String username;
    // 密码
    private String password;
    // 姓名
    private String name;
    // 年龄
    private Integer age;
    // 性别
    private Integer sex;
    // 生日
    private Date birthday;
    // 创建日期
    private Date created;
    // 修改日期
    private Date updated;
}
```

编写controller，在controller中直接调用RestTemplate，远程访问user-service的服务接口： 

```java
package cn.itcast.consumer.controller;

import cn.itcast.consumer.pojo.User;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.client.RestTemplate;

@RestController
@RequestMapping("/consumer")
public class ConsumerController {

    @Autowired
    private RestTemplate restTemplate;
    /** 根据主键id查询用户 */
    @GetMapping("/{id}")
    public User findOne(@PathVariable("id")Long id){

        String url = "http://localhost:9001/user/" + id;
        return restTemplate.getForObject(url, User.class);
    }
}
```

项目结构： 

![1571825478422](D:/pic/markdown/SpringCloud/1571825478422.png)&nbsp;

**启动测试**

访问：http://localhost:8080/consumer/1

![1571825588631](D:/pic/markdown/SpringCloud/1571825588631.png)&nbsp;

### 5.4 存在问题分析

------

> 简单回顾一下，刚才我们写了什么:

user-service：对外提供了查询用户的接口 

user-consumer：通过RestTemplate访问 http://locahost:9001/user/{id}接口，查询用户数据 

> 存在什么问题:

+ 在user-consumer中，我们把url地址硬编码到了代码中，不方便后期维护 
+ user-consumer需要记忆user-service的地址，如果出现变更，可能得不到通知，地址将失效 
+ user-consumer不清楚user-service的状态，服务宕机也不知道
+ user-service只有1台服务，不具备高可用性 
+ 即便user-service形成集群，user-consumer还需自己实现负载均衡 

> 其实上面说的问题，概括一下就是分布式服务必然要面临的问题： 

+ 服务管理 
  + 如何自动注册和发现 
  + 如何实现状态监管 
  + 如何实现动态路由
+ 服务如何实现负载均衡 
+ 服务如何解决容灾问题 
+ 服务如何实现统一配置 

> 说明：以上的问题，都将在D:/pic/markdown/SpringCloud中得到答案。



## 06、Eureka介绍

### 6.1 初识Eureka

------

首先我们来解决第一问题，服务的管理。

> 问题分析 

在刚才的案例中，user-service对外提供服务，需要对外暴露自己的地址。而user-consumer（消费者）需要记录服务提供者的地址。将来地址出现变更，还需要及时更新。这在服务较少的时候并不觉得有什么，但是在现在日益复杂的互联网环境，一个项目肯定会拆分出十几，甚至数十个微服务。此时如果还人为管理地址，不仅开发困难，将来测试、发布上线都会非常麻烦，这与DevOps的思想是背道而驰的。

> 网约车

   这就好比是网约车出现以前，人们出门叫车只能叫出租车。一些私家车想做出租却没有资格，被称为黑车。而很多人想要约车，但是无奈出租车太少，不方便。私家车很多却不敢拦，而且满大街的车，谁知道哪个才是愿意载人的。一个想要，一个愿意给，就是缺少引子，缺乏管理啊。 

   此时滴滴这样的网约车平台出现了，所有想载客的私家车全部到滴滴注册，记录你的车型（服务类型），身份信息（联系方式）。这样提供服务的私家车，在滴滴那里都能找到，一目了然。 

   此时要叫车的人，只需要打开APP，输入你的目的地，选择车型（服务类型），滴滴自动安排一个符合需求的车到你面前，为你服务，完美！ 

> Eureka做什么？

   Eureka就好比是滴滴，负责管理、记录服务提供者的信息。服务调用者无需自己寻找服务，而是把自己的需求告诉Eureka，然后Eureka会把符合你需求的服务告诉你。 

   同时，服务提供方与Eureka之间通过 “心跳” 机制进行监控，当某个服务提供方出现问题，Eureka自然会把它从服务列表中剔除。 

这就实现了服务的自动注册、发现、状态监控。

### 6.2 原理图

------

> 基本架构

![1571886244487](D:/pic/markdown/SpringCloud/1571886244487.png)

+ Eureka：就是服务注册中心（可以是一个集群），对外暴露自己的地址 
+ 提供者：启动后向Eureka注册自己信息（地址，提供什么服务） 
+ 消费者：向Eureka订阅服务，Eureka会将对应服务的所有提供者地址列表发送给消费者，并且定期更新 
+ 心跳(续约)：提供者定期通过http方式向Eureka刷新自己的状态

> 注意：Eureka分两个部分： Eureka服务端  +  Eureka客户端(服务提供者或服务消费者)



## 07、Eureka服务端：注册中心

**目标**

> 搭建eureka-server模块，作为注册中心。

**实现步骤**

+ 创建模块: eureka-server

  ![1571887419180](D:/pic/markdown/SpringCloud/1571887419180.png)

  ![1571887490439](D:/pic/markdown/SpringCloud/1571887490439.png)

+ 配置依赖: pom.xml

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <project xmlns="http://maven.apache.org/POM/4.0.0"
           xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
           http://maven.apache.org/xsd/maven-4.0.0.xsd">
      <parent>
          <artifactId>D:/pic/markdown/SpringCloud-demo</artifactId>
          <groupId>cn.itcast</groupId>
          <version>1.0-SNAPSHOT</version>
      </parent>
      <modelVersion>4.0.0</modelVersion>
      <artifactId>eureka-server</artifactId>
  
      <dependencies>
          <!-- 配置eureka服务端启动器(集成了web启动器) -->
          <dependency>
              <groupId>org.springframework.cloud</groupId>
              <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
          </dependency>
      </dependencies>
  </project>
  ```

+ 启动类: EurekaServerApplication

  ```java
  package cn.itcast;
  
  import org.springframework.boot.SpringApplication;
  import org.springframework.boot.autoconfigure.SpringBootApplication;
  import org.springframework.cloud.netflix.eureka.server.EnableEurekaServer;
  
  @EnableEurekaServer // 声明当前应用为eureka服务(启用eureka服务)
  @SpringBootApplication
  public class EurekaServerApplication {
      public static void main(String[] args){
          SpringApplication.run(EurekaServerApplication.class, args);
      }
  }
  ```

+ 配置文件: application.yml

  ```properties
  server:
    port: 8761 # eureka服务端，默认端口
  spring:
    application:
      name: eureka-server # 应用名称，会在Eureka中作为服务的id标识（serviceId）
  eureka:
    client:
      service-url: # Eureka服务的地址，现在是自己的地址，如果集群，需要写其它服务的地址。
        defaultZone: http://localhost:8761/eureka
      fetch-registry: false #不拉取服务(自已拉取自己的服务没有必要)
      register-with-eureka: false # 不注册自己(自已注册到自己没有必要)
  ```

+ 启动服务，并访问：http://127.0.0.1:8761

  ![1571889233646](D:/pic/markdown/SpringCloud/1571889233646.png)

  ![1571889610181](D:/pic/markdown/SpringCloud/1571889610181.png)

**小结**

+ Eureka服务端开发三要素

  + eureka服务端启动器

    ![1571888764490](D:/pic/markdown/SpringCloud/1571888764490.png)

  + eureka服务端注解

    ![1571888802216](D:/pic/markdown/SpringCloud/1571888802216.png)

  + eureka服务端配置

    ![1571888853725](D:/pic/markdown/SpringCloud/1571888853725.png)

## 08、Eureka客户端：服务注册

**目标**

> 实现user-service服务注册到eurekaServer中。(客户端注册到服务端)

**实现步骤**

+ 在user-service模块中添加eureka客户端启动器依赖

  ```xml
  <!-- 配置eureka客户端启动器 -->
  <dependency>
      <groupId>org.springframework.cloud</groupId>
      <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
  </dependency>
  ```

+ 在启动类上开启Eureka客户端，添加 @EnableDiscoveryClient 来开启Eureka客户端 

  ```java
  @SpringBootApplication
  @MapperScan("cn.itcast.user.mapper")
  @EnableDiscoveryClient // 开启Eureka客户端 
  public class UserApplication {
      
      public static void main(String[] args){
          SpringApplication.run(UserApplication.class, args);
      }
  }
  ```

+ 修改application.yml，添加eureka客户端配置

  ```properties
  server:
    port: 9001
  spring:
    datasource:
      driver-class-name: com.mysql.jdbc.Driver
      url: jdbc:mysql://localhost:3306/D:/pic/markdown/SpringCloud
      username: root
      password: root
    application:
      # 应用名称(服务id)
      name: user-service
  mybatis:
    configuration:
      map-underscore-to-camel-case: true
    type-aliases-package: cn.itcast.user.pojo
  # 配置eureka
  eureka:
    client:
      service-url:
        defaultZone: http://localhost:8761/eureka
  ```

  > 注意：这里我们添加了spring.application.name属性来指定应用名称，将来会作为服务的id使用。

+ 重启项目，访问Eureka监控页面查看:

  ![1571891351141](D:/pic/markdown/SpringCloud/1571891351141.png)

  说明：我们发现user-service服务已经注册成功了。

**小结**

+ Eureka客户端开发三要素

  + eureka客户端启动器

    ![1575106301907](D:/pic/markdown/SpringCloud/1575106301907.png) 

  + eureka客户端注解

    ![1571891598042](D:/pic/markdown/SpringCloud/1571891598042.png)

  + eureka客户端配置

    ![1571891650709](D:/pic/markdown/SpringCloud/1571891650709.png)

## 09、Eureka客户端：服务发现

**目标**

> 实现user-consumer消费者从eurekaServer中发现服务。(通过服务id发现服务)

**实现步骤**

+ 在user-consumer模块中添加eureka客户端启动器依赖

  ```xml
  <!-- 配置eureka客户端启动器 -->
  <dependency>
      <groupId>org.springframework.cloud</groupId>
      <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
  </dependency>
  ```

+ 在启动类上开启Eureka客户端，添加 @EnableDiscoveryClient 来开启Eureka客户端

  ```java
  package cn.itcast.consumer;
  
  import org.springframework.boot.SpringApplication;
  import org.springframework.boot.autoconfigure.SpringBootApplication;
  import org.springframework.cloud.client.discovery.EnableDiscoveryClient;
  import org.springframework.context.annotation.Bean;
  import org.springframework.web.client.RestTemplate;
  
  @SpringBootApplication
  @EnableDiscoveryClient // 开启Eureka客户端
  public class ConsumerApplication {
  
      public static void main(String[] args){
          SpringApplication.run(ConsumerApplication.class, args);
      }
      @Bean
      public RestTemplate restTemplate(){
          return new RestTemplate();
      }
  }
  ```

+ 修改application.yml，添加eureka客户端配置

  ```properties
  server:
    port: 8080
  spring:
    application:
      name: user-consumer # 应用名称
  eureka:
    client:
      service-url: # eurekaServer地址
        defaultZone: http://localhost:8761/eureka
  ```

+ 修改代码，用DiscoveryClient类的方法，根据服务id，获取服务实例

  ```java
  package cn.itcast.consumer.controller;
  
  import cn.itcast.consumer.pojo.User;
  import org.springframework.beans.factory.annotation.Autowired;
  import org.springframework.cloud.client.ServiceInstance;
  import org.springframework.cloud.client.discovery.DiscoveryClient;
  import org.springframework.web.bind.annotation.GetMapping;
  import org.springframework.web.bind.annotation.PathVariable;
  import org.springframework.web.bind.annotation.RequestMapping;
  import org.springframework.web.bind.annotation.RestController;
  import org.springframework.web.client.RestTemplate;
  
  import java.util.List;
  
  @RestController
  @RequestMapping("/consumer")
  public class ConsumerController {
  
      /** 注入发现者 */
      @Autowired
      private DiscoveryClient discoveryClient;
      @Autowired
      private RestTemplate restTemplate;
      
      /** 根据主键id查询用户 */
      @GetMapping("/{id}")
      public User findOne(@PathVariable("id")Long id){
  
          // 根据服务id获取该服务的全部服务实例
          List<ServiceInstance> instances = discoveryClient
                  .getInstances("user-service");
          // 获取第一个服务实例(因为目前我们只有一个服务实例)
          ServiceInstance serviceInstance = instances.get(0);
  
          // 获取服务实例所在的主机
          String host = serviceInstance.getHost();
          // 获取服务实例所在的端口
          int port = serviceInstance.getPort();
  
          // 定义服务实例访问URL
          String url = "http://" + host + ":" + port + "/user/" + id;
          System.out.println("服务实例访问URL: " + url);
          return restTemplate.getForObject(url, User.class);
      }
  }
  ```

+ Debug跟踪运行

  ![1571893317891](D:/pic/markdown/SpringCloud/1571893317891.png)

**小结**

> Eureka客户端开发三要素
>
> - eureka客户端启动器
> - eureka客户端注解
> - eureka客户端配置
>
> 说明：**服务注册与服务发现 都属于Eureka客户端**，只是我们人为的把Eureka客户端分成了服务注册与服务发现两个角色。**实际上Eureka只有服务端(注册中心)与客户端(服务注册或服务发现)**。在实际项目开发中不会存在这样的客户端，不然控制器就会出现重复，这只是入门示例。会在服务中调用服务。



## 10、Eureka服务端：高可用

### 10.1 基础架构

Eureka架构中的三个核心角色： 

+ 服务注册中心(**Eureka服务端**)

  Eureka的服务端应用，提供服务注册和发现功能，就是刚刚我们建立的eureka-server 

+ 服务提供者(**Eureka客户端**)

  提供服务的应用，可以是SpringBoot应用，也可以是其它任意技术实现，只要对外提供的是Rest风格服务即可。本例中就是我们实现的user-service 

+ 服务消费者(**Eureka客户端**)

  消费应用从注册中心获取服务列表，从而得知每个服务方的信息，知道去哪里调用服务方。本例中就是我们实现的user-consumer  

### 10.2 高可用介绍

Eureka Server即服务的注册中心，在刚才的案例中，我们只有一个EurekaServer，事实上EurekaServer也可以是一个集群，形成高可用的Eureka注册中心。

> 服务同步

多个Eureka Server之间也会互相注册为服务，当服务提供者注册到Eureka Server集群中的某个节点时，该节点会把服务的信息同步给集群中的每个节点，从而实现数据同步。因此，无论客户端访问到Eureka Server集群中的任意一个节点，都可以获取到完整的服务列表信息。

而作为客户端，需要把信息注册到每个Eureka中：

![1571923955820](D:/pic/markdown/SpringCloud/1571923955820.png)&nbsp;

如果有三个Eureka，则每一个EurekaServer都需要注册到其它几个Eureka服务中，例如：有三个分别为8761、8762、8763，则： 

+ 8761要注册到8762和8763上 
+ 8762要注册到8761和8763上 
+ 8763要注册到8761和8762上

### 10.3 高可用配置

**目标**

> 实现Eureka服务端注册中心高可用配置

我们假设要搭建两个EurekaServer的集群，端口分别为：8761和8762

**实现步骤**

+ 修改eureka-server的配置(application.yml)

  ```properties
  server:
    port: ${port:8761} # eureka服务端，默认端口
  spring:
    application:
      name: eureka-server # 应用名称，会在Eureka中作为服务的id标识（serviceId）
  eureka:
    client:
      service-url: # Eureka服务地址；如果是集群则是其它服务地址，后面要加/eureka
        defaultZone: ${defaultZone:http://localhost:8761/eureka}
      fetch-registry: true # 拉取服务(自已拉取对方的服务)
      register-with-eureka: true # 注册自己(自已注册到对方)
  ```

  所谓的高可用注册中心，其实就是把EurekaServer自己也作为一个服务，注册到其它EurekaServer上，这样多个EurekaServer之间就能互相发现对方，从而形成集群。因此我们做了以下修改： 

  > 注意把fetch-registry和register-with-eureka修改为true或者注释掉(默认为true)
  >
  > 在上述配置文件中的${}表示在jvm启动时候若能找到对应port或者defaultZone参数则使用，若无则使用后面的默认值。

  说明：把service-url的值改成了另外一台EurekaServer的地址，而不是自己。

+ 另外一台在启动的时候指定端口port和defaultZone配置

  ![1571925268139](D:/pic/markdown/SpringCloud/1571925268139.png)&nbsp;

  ![1571925607908](D:/pic/markdown/SpringCloud/1571925607908.png)

  复制一份并修改:

  ![1571925711584](D:/pic/markdown/SpringCloud/1571925711584.png)

+ 启动测试

  ![1571925989273](D:/pic/markdown/SpringCloud/1571925989273.png)

+ 客户端注册服务到集群

  因为EurekaServer不止一个，因此user-service项目注册服务或者user-consumer获取服务的时候，service-url参数需要变化：

  ```properties
  # 配置eureka
  eureka:
    client:
      service-url: # EurekaServer地址,多个地址以','隔开
        defaultZone: http://localhost:8761/eureka,http://localhost:8762/eureka
  ```

**小结**

+ 高可用配置

  + Eureka服务端

  ![1571926522277](D:/pic/markdown/SpringCloud/1571926522277.png)

  + Eureka客户端

    ![1571926550121](D:/pic/markdown/SpringCloud/1571926550121.png)

## 11、Eureka客户端：服务提供者的其他配置

服务提供者要向EurekaServer注册服务，并且完成服务续约等工作。 

> 服务注册

服务提供者在启动时，会检测配置属性中的： eureka.client.register-with-eureka=true 参数是否正确，事实上默认就是true。如果值确实为true，则会向EurekaServer发起一个Rest请求，并携带自己的元数据信息，Eureka Server会把这些信息保存到一个双层Map结构中。 

+ 第一层Map的Key就是服务id，一般是配置中的 spring.application.name 属性 

+ 第二层Map的key是服务的实例id。一般host+ serviceId + port，例如：locahost:user-service:9001

  值则是服务的实例对象，也就是说一个服务，可以同时启动多个不同实例，形成集群。 

  Map<String,Map<String,Object>>

默认注册时使用的是主机名，如果想用ip进行注册，可以在user-service中添加配置：

```properties
# 配置eureka
eureka:
  instance:
    ip-address: 127.0.0.1 # ip地址
    prefer-ip-address: true # 更倾向于使用ip，而不是host名称
```

修改完后先后重启user-service和user-consumer![1571931610212](D:/pic/markdown/SpringCloud/1571931610212.png)

> 服务续约 

在注册服务完成以后，服务提供者会维持一个心跳（定时向EurekaServer发起Rest请求），告诉EurekaServer：“我还活着”。这个我们称为服务的续约（renew）

有两个重要参数可以修改服务续约的行为： 

```properties
# 配置eureka
eureka:
  instance:
    lease-renewal-interval-in-seconds: 30 # 服务续约(renew)的间隔时间，默认为30秒
    lease-expiration-duration-in-seconds: 90 # 服务失效时间，默认值90秒 
```

也就是说，默认情况下每个30秒服务会向注册中心发送一次心跳，证明自己还活着。如果超过90秒没有发送心跳，EurekaServer就会认为该服务宕机，会从服务列表中移除，这两个值在生产环境不要修改，默认即可。



## 12、Eureka客户端：服务消费者的其他配置

> 获取服务列表

当服务消费者启动时，会检测 eureka.client.fetch-registry=true 参数的值，如果为true，则会从Eureka Server服务的列表只读备份，然后缓存在本地。并且 **每隔30秒** 会重新获取并更新数据。可以通过下面的参数来修改：

```properties
eureka:
  client:
    registry-fetch-interval-seconds: 30 # 获取服务间隔时间(默认30秒)
```



## 13、Eureka服务端：失效剔除、自我保护

> 服务下线

当服务进行正常关闭操作时，它会触发一个服务下线的REST请求给Eureka Server，告诉服务注册中心：“我要下线了”。服务中心接受到请求之后，将该服务置为下线状态。

> 失效剔除

有时我们的服务可能由于内存溢出或网络故障等原因使得服务不能正常的工作，而服务注册中心并未收到“服务下线”的请求。相对于服务提供者的“服务续约”操作，服务注册中心在启动时会创建一个定时任务，默认每隔一段时间（默认为60秒）将当前清单中超时（默认为90秒）没有续约的服务剔除，这个操作被称为失效剔除。 

可以通过eureka.server.eviction-interval-timer-in-ms参数对其进行修改，单位是毫秒。 

> 自我保护

我们关停一个服务，就会在Eureka面板看到一条警告： 

![1571933329264](D:/pic/markdown/SpringCloud/1571933329264.png)

这是触发了Eureka的自我保护机制。当服务未按时进行心跳续约时，Eureka会统计服务实例最近5分钟心跳续约的比例是否低于了85%。在生产环境下，因为网络延迟等原因，心跳失败实例的比例很有可能超标，但是此时就把服务剔除列表并不妥当，因为服务可能没有宕机。Eureka在这段时间内不会剔除任何服务实例，直到网络恢复正常。生产环境下这很有效，保证了大多数服务依然可用，不过也有可能获取到失败的服务实例，因此服务调用者必须做好服务的失败容错。



可以通过下面的配置来关停自我保护：

```properties
eureka:
  server:
    enable-self-preservation: false # 关闭自我保护模式（缺省为打开）
```

![1571933631927](D:/pic/markdown/SpringCloud/1571933631927.png)



## 14、Ribbon负载均衡

在刚才的案例中，我们启动了一个user-service，然后通过DiscoveryClient来获取服务实例信息，然后获取ip和端口来访问。 

但是实际环境中，往往会开启很多个user-service的集群。此时获取的服务列表中就会有多个，到底该访问哪一个呢？

+ 一般这种情况下就需要编写负载均衡算法，在多个实例列表中进行选择。
+ 不过Eureka中已经集成了负载均衡组件：Ribbon，简单修改代码即可使用。 

什么是Ribbon?

![1571933956241](D:/pic/markdown/SpringCloud/1571933956241.png)

**目标**

> 使用Ribbon实现服务实例负载均衡

**操作步骤**

+ 启动两个服务实例(user-service)

  首先我们配置启动两个user-service实例，一个9001，一个9002。

  ![1571995452729](D:/pic/markdown/SpringCloud/1571995452729.png)

  ![1571995508887](D:/pic/markdown/SpringCloud/1571995508887.png)

  ![1571995688849](D:/pic/markdown/SpringCloud/1571995688849.png)

  ![1571996724908](D:/pic/markdown/SpringCloud/1571996724908.png)&nbsp;

  Eureka监控面板：

  ![1571996772658](D:/pic/markdown/SpringCloud/1571996772658.png)

+ 开启负载均衡(user-consumer)

  因为Eureka中已经集成了Ribbon，所以我们无需引入新的依赖。直接修改代码： 

  在RestTemplate的配置方法上添加 @LoadBalanced 注解：

  ```java
  @Bean
  @LoadBalanced
  public RestTemplate restTemplate(){
      return new RestTemplate();
  }
  ```

  修改ConsumerController调用方式，不再手动获取ip和端口，而是直接通过服务名称调用： 

  ```java
  /** 根据主键id查询用户 */
  @GetMapping("/{id}")
  public String findOne(@PathVariable("id")Long id){
      /*// 根据服务id获取该服务的全部服务实例
      List<ServiceInstance> instances = discoveryClient
                  .getInstances("user-service");
      // 获取第一个服务实例(因为目前我们只有一个服务实例)
      ServiceInstance serviceInstance = instances.get(0);
  
      // 获取服务实例所在的主机
      String host = serviceInstance.getHost();
      // 获取服务实例所在的端口
      int port = serviceInstance.getPort();
  
      // 定义服务实例访问URL
      String url = "http://" + host + ":" + port + "/user/" + id;*/
  
      // 定义服务实例访问URL
      String url = "http://user-service/user/" + id;
      return restTemplate.getForObject(url, String.class);
  }
  ```

  为了方便在控制台，查看负载均衡效果，修改user-service模块的application.yml文件增加日志的输出：

  ![1571998441043](D:/pic/markdown/SpringCloud/1571998441043.png)

  访问页面，查看结果；并可以在9001和9002的控制台查看执行情况：

  ​    你会发现 9001 与 9002 轮询访问

  > 了解: Ribbon默认的负载均衡策略是轮询(com.netflix.loadbalancer.RoundRobinRule)
  >
  > SpringBoot也帮提供了修改负载均衡规则的配置入口在user-consumer的配置文件中添加如下，就变成随机的了： 

  ```properties
  user-service:
    ribbon:
      NFLoadBalancerRuleClassName: com.netflix.loadbalancer.RandomRule
  ```

  格式是： {服务名称}.ribbon.NFLoadBalancerRuleClassName

  Ribbon负载均衡策略类都实现了IRule接口：

  ![1572075063123](D:/pic/markdown/SpringCloud/1572075063123.png)

  

+ 源码跟踪(负载均衡策略改成RoundRobinRule)

  为什么只输入了service名称就可以访问了呢？之前还要获取ip和端口。 

  显然是有组件根据service名称，获取到了服务实例的ip和端口。它就是LoadBalancerInterceptor ，这个类会在对RestTemplate的请求进行拦截，然后从Eureka根据服务id获取服务列表，随后利用负载均衡算法得到真实的服务地址信息，替换服务id。 

  我们进行源码跟踪(**Ctrl + Shift + N** 搜索LoadBalancerInterceptor)：

  ![1572072930140](D:/pic/markdown/SpringCloud/1572072930140.png)

  继续跟入execute方法：发现获取了9001或9002端口的服务

  ![1572075792053](D:/pic/markdown/SpringCloud/1572075792053.png)

  ![1572074236857](D:/pic/markdown/SpringCloud/1572074236857.png)

  接下来，再按 Step Over 两次：

  ![1572075937712](D:/pic/markdown/SpringCloud/1572075937712.png)

  ![1572074431540](D:/pic/markdown/SpringCloud/1572074431540.png) 

  再跟下一次，发现获取的是9001、9002之间切换：

  ![1572074574474](D:/pic/markdown/SpringCloud/1572074574474.png)

  果然实现了负载均衡: 

  ![1572075621984](D:/pic/markdown/SpringCloud/1572075621984.png)

**小结**

+ 负载均衡注解: @LoadBalanced

  ![1572076330378](D:/pic/markdown/SpringCloud/1572076330378.png)&nbsp;

+ 调用服务时，使用服务id

  ![1572076266835](D:/pic/markdown/SpringCloud/1572076266835.png)



## 15、Hystrix熔断器：简介

Hystix，英文意思是豪猪，全身是刺，看起来就不好惹，是一种保护机制。 

Hystrix也是Netflix公司的一款组件。 

访问地址：<https://github.com/Netflix/Hystrix> 

![1572077313705](D:/pic/markdown/SpringCloud/1572077313705.png)

那么Hystix的作用是什么呢？具体要保护什么呢？ 

Hystix是Netflix开源的一个延迟和容错库，用于隔离访问远程服务、第三方库，防止出现级联失败。

**作用**：请求熔断、服务降级、线程隔离、请求缓存、请求合并

## 16、Hystrix熔断器：雪崩问题

微服务中，服务间调用关系错综复杂，一个请求，可能需要调用多个微服务接口才能实现，会形成非常复杂的调用链路：

![1562598446267](D:/pic/markdown/SpringCloud/002.png)&nbsp;

如图，一次业务请求，需要调用A、P、H、I四个服务，这四个服务又可能调用其它服务。 

如果此时，某个服务出现异常：

![1562598446267](D:/pic/markdown/SpringCloud/003.png)&nbsp;

例如微服务I发生异常，请求阻塞，用户不会得到响应，则tomcat的这个线程不会释放，于是越来越多的用户请求到来，越来越多的线程会阻塞：

![1562598446267](D:/pic/markdown/SpringCloud/004.png)&nbsp;

服务器支持的线程和并发数有限，请求一直阻塞，会导致服务器资源耗尽，从而导致所有其它服务都不可用，形成雪崩效应。 

这就好比，一个汽车生产线，生产不同的汽车，需要使用不同的零件，如果某个零件因为种种原因无法使用，那么就会造成整台车无法装配，陷入等待零件的状态，直到零件到位，才能继续组装。  此时如果有很多个车型都需要这个零件，那么整个工厂都将陷入等待的状态，导致所有生产都陷入瘫痪。一个零件的波及范围不断扩大。

Hystix解决雪崩问题的手段主要是服务降级，包括： 

+ 线程隔离 
+ 服务熔断



## 17、Hystrix熔断器：线程隔离原理

线程隔离示意图: 

![1562598446267](D:/pic/markdown/SpringCloud/005.png)  

+ Hystrix为每个依赖服务调用分配一个小的线程池，如果线程池已满调用将被立即拒绝，默认不采用排队，加速失败判定时间。 
+ 用户的请求将不再直接访问服务，而是通过线程池中的空闲线程来访问服务，如果线程池已满，或者请求超时，则会进行降级处理，什么是服务降级？

> 服务降级：优先保证核心服务，而非核心服务不可用或弱可用。

+ 用户的请求故障时，不会被阻塞，更不会无休止的等待或者看到系统崩溃，至少可以看到一个执行结果（例如返回友好的提示信息）。 
+ 服务降级虽然会导致请求失败，但是不会导致阻塞，而且最多会影响这个依赖服务对应的线程池中的资源，对其它服务没有影响。
+ 触发Hystix服务降级的情况
  + 线程池已满 
  + 请求超时
  
  

## 18、Hystrix熔断器：动手实践线程隔离

tip:这节只是实现了服务降级，并不是真正意义上的线程隔离

**实现步骤**

+ 引入依赖

  + 在user-consumer消费端系统的pom.xml文件添加如下依赖： 

    ```xml
    <!-- 配置hystrix启动器 -->
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-hystrix</artifactId>
    </dependency>
    ```

+ 开启熔断器

  + 在启动类上添加注解：@EnableCircuitBreaker 

    ```java
    @SpringBootApplication
    @EnableDiscoveryClient // 开启Eureka客户端
    @EnableCircuitBreaker // 开启熔断器
    public class ConsumerApplication {
    	// ......
    }
    ```

    可以看到，我们类上的注解越来越多，在微服务中，经常会引入上面的三个注解，于是Spring就提供了一个组合注解：@D:/pic/markdown/SpringCloudApplication

    ![1572081430584](D:/pic/markdown/SpringCloud/1572081430584.png)&nbsp;

    因此，我们可以使用这个组合注解来代替之前的3个注解:

    ```java
    @D:/pic/markdown/SpringCloudApplication
    public class ConsumerApplication {
    	// ......
    }
    ```

+ 编写降级逻辑

  + 当目标服务的调用出现故障，我们希望快速失败，给用户一个友好提示。因此需要提前编写好失败时的降级处理逻辑，要使用@HystixCommond注解来完成。改造ConsumerController：

  ```java
  @RestController
  @RequestMapping("/consumer")
  @Slf4j
  public class ConsumerController {
  
      /** 注入发现者 */
      @Autowired
      private DiscoveryClient discoveryClient;
      @Autowired
      private RestTemplate restTemplate;
  
      /** 根据主键id查询用户 */
      @GetMapping("/{id}")
      @HystrixCommand(fallbackMethod = "findOneFallback")
      public String findOne(@PathVariable("id")Long id){
  
          /*// 根据服务id获取该服务的全部服务实例
          List<ServiceInstance> instances = discoveryClient
                  .getInstances("user-service");
          // 获取第一个服务实例(因为目前我们只有一个服务实例)
          ServiceInstance serviceInstance = instances.get(0);
  
          // 获取服务实例所在的主机
          String host = serviceInstance.getHost();
          // 获取服务实例所在的端口
          int port = serviceInstance.getPort();
  
          // 定义服务实例访问URL
          String url = "http://" + host + ":" + port + "/user/" + id;*/
  
          // 定义服务实例访问URL
          String url = "http://user-service/user/" + id;
          return restTemplate.getForObject(url, String.class);
      }
      public String findOneFallback(Long id){
          log.error("查询用户信息失败。id：{}", id);
          return "对不起，网络太拥挤了！";
      }
  }
  ```

  要注意，因为熔断的降级逻辑方法必须跟正常逻辑方法保证：相同的参数列表和返回值声明。失败逻辑中返回User对象没有太大意义，一般会返回友好提示。所以把findOne的方法改造为返回String，反正也是Json数据。这样失败逻辑中返回一个错误说明，会比较方便。

  说明： 

  + @HystrixCommand(fallbackMethod="findOneFallBack")：用来声明一个降级逻辑的方法

  测试： 

  + 当user-service正常提供服务时，访问与以前一致。但是当将user-service停机时，会发现页面返回了降级处理信息： 

    ![1572083152674](D:/pic/markdown/SpringCloud/1572083152674.png)

+ 默认fallback

  + 刚才把fallback写在了某个业务方法上，如果这样的方法很多，那岂不是要写很多。所以可以把fallback配置加在类上，实现默认fallback:

```java
package cn.itcast.consumer.controller;

import com.netflix.hystrix.contrib.javanica.annotation.DefaultProperties;
import com.netflix.hystrix.contrib.javanica.annotation.HystrixCommand;
import lombok.extern.slf4j.Slf4j;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.cloud.client.discovery.DiscoveryClient;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.client.RestTemplate;

@RestController
@RequestMapping("/consumer")
@Slf4j
@DefaultProperties(defaultFallback = "defaultFallback")
public class ConsumerController {

    /** 注入发现者 */
    @Autowired
    private DiscoveryClient discoveryClient;
    @Autowired
    private RestTemplate restTemplate;

    /** 根据主键id查询用户 */
    @GetMapping("/{id}")
    @HystrixCommand
    public String findOne(@PathVariable("id")Long id){

        /*// 根据服务id获取该服务的全部服务实例
        List<ServiceInstance> instances = discoveryClient
                .getInstances("user-service");
        // 获取第一个服务实例(因为目前我们只有一个服务实例)
        ServiceInstance serviceInstance = instances.get(0);

        // 获取服务实例所在的主机
        String host = serviceInstance.getHost();
        // 获取服务实例所在的端口
        int port = serviceInstance.getPort();

        // 定义服务实例访问URL
        String url = "http://" + host + ":" + port + "/user/" + id;*/

        // 定义服务实例访问URL
        String url = "http://user-service/user/" + id;
        return restTemplate.getForObject(url, String.class);
    }

    public String findOneFallback(Long id){
        log.error("查询用户信息失败。id：{}", id);
        return "对不起，网络太拥挤了！";
    }

    public String defaultFallback(){
        return "默认提示：对不起，网络太拥挤了！";
    }
}
```

+ @DefaultProperties(defaultFallback = "defaultFallBack")：在类上指明统一的失败降级方法；该类中所有方法返回类型要与处理失败的方法的返回类型一致。

  ![1572083756286](D:/pic/markdown/SpringCloud/1572083756286.png)
  
+ 超时配置

  + 在之前的案例中，请求在超过1秒后都会返回错误信息，这是因为Hystix的默认超时时长为1秒，我们可以通过配置修改这个值(项目中一般使用它的默认值1秒)：

    ```properties
    # 线程隔离
    hystrix:
      command:
        default:
          execution:
            isolation:
              thread:
                timeoutInMilliseconds: 2000
    ```

    这个配置会作用于全局所有方法。 

    为了触发超时，可以在user-service中休眠2秒： 

```java
@Service
@Transactional
public class UserService {
    @Autowired(required = false)
    private UserMapper userMapper;
    /** 根据主键id查询用户 */
    public User findOne(Long id){
        try{
            Thread.sleep(2000);
        }catch (Exception ex){
            ex.printStackTrace();
        }
        return userMapper.selectByPrimaryKey(id);
    }
}
```

测试：

![1572084683334](D:/pic/markdown/SpringCloud/1572084683334.png)

可以发现，请求的时长已经到了2s+，证明配置生效了。 

如果把休眠时间修改到2秒以下，又可以正常访问了。

**小结**

+ 消费端才需要做线程隔离(user-consumer)。

+ 线程隔离需要用到: hystrix启动器

    spring-cloud-starter-netflix-hystrix

+ 线程隔离需要用到注解:

  + @EnableCircuitBreaker 启用熔断器
  + @HystrixCommand(fallbackMethod = "findOneFallback")
  + @DefaultProperties(defaultFallback = "defaultFallback")
  + 提供降级方法
  
  

## 19、Hystrix熔断器：服务熔断原理

熔断器，也叫断路器，其英文单词为：Circuit Breaker

![1572100760209](D:/pic/markdown/SpringCloud/006.png)&nbsp;

Hystix的熔断状态机模型：

![1572100760209](D:/pic/markdown/SpringCloud/007.png)

状态机有3个状态： 

+ Closed：关闭状态（断路器关闭），所有请求都正常访问。 
+ Open：打开状态（断路器打开），所有请求都会被降级。Hystix会对请求情况计数，当一定时间内失败请求百分比达到阈值，则触发熔断，断路器会完全打开。默认失败比例的阈值是50%，请求次数最少不低于20次。 
+ Half Open：半开状态，open状态不是永久的，打开后会进入休眠时间（默认是5S）。随后断路器会自动进入半开状态。此时会释放1次请求通过，若这个请求是健康的，则会关闭断路器，否则继续保持打开，再次进行5秒休眠计时。



## 20、Hystrix熔断器：动手实践服务熔断

**实现步骤**

- 为了能够精确控制请求的成功或失败，在user-consumer的调用业务中加入一段逻辑

  ```java
   /** 根据主键id查询用户 */
  @GetMapping("/{id}")
  @HystrixCommand
  public String findOne(@PathVariable("id")Long id){
      if (id == 1){
          throw new RuntimeException("太忙了！");
      }
      // 定义服务实例访问URL
      String url = "http://user-service/user/" + id;
      return restTemplate.getForObject(url, String.class);
  }
  ```

  说明：这样如果参数是id为1，一定失败，其它情况都成功。（不要忘了注释user-service中的休眠逻辑）

  ![1572160513795](D:/pic/markdown/SpringCloud/1572160513795.png)

- 我们准备两个请求窗口： 

  + 一个请求：http://localhost:8080/consumer/1，注定失败 
  + 一个请求：http://localhost:8080/consumer/2，肯定成功

  当我们疯狂访问id为1的请求时（超过20次），就会触发熔断。断路器会打开，一切请求都会被降级处理。 

  此时你访问id为2的请求，会发现返回的也是失败，而且失败时间很短，只有5秒左右；因进入半开状态之后2是可以的。

  ![1572161697652](D:/pic/markdown/SpringCloud/1572161697652.png)

  不过，默认的熔断触发要求较高，休眠时间窗较短，为了测试方便，我们可以通过配置修改熔断策略： 

  ```properties
  circuitBreaker.requestVolumeThreshold=10
  circuitBreaker.sleepWindowInMilliseconds=20000
  circuitBreaker.errorThresholdPercentage=50
  ```

  + requestVolumeThreshold：触发熔断的最小请求次数，默认20 
  + sleepWindowInMilliseconds：休眠时长，默认是5000毫秒
  + errorThresholdPercentage：触发熔断的失败请求最小占比，默认50% 

   默认配置：com.netflix.hystrix.HystrixCommandProperties

  ![1572162913791](D:/pic/markdown/SpringCloud/1572162913791.png)

- 配置服务熔断(user-consumer)

  ```java
  /** 根据主键id查询用户 */
  @GetMapping("/{id}")
  @HystrixCommand(commandProperties = {
     @HystrixProperty(name="circuitBreaker.requestVolumeThreshold",value="10"),
     @HystrixProperty(name="circuitBreaker.sleepWindowInMilliseconds",value="20000"),
     @HystrixProperty(name="circuitBreaker.errorThresholdPercentage",value="50")
  })
  public String findOne(@PathVariable("id")Long id){
      if (id == 1){
          throw new RuntimeException("太忙了！");
      }
      // 定义服务实例访问URL
      String url = "http://user-service/user/" + id;
      return restTemplate.getForObject(url, String.class);
  }
  ```

  测试：

  + 请求 http://localhost:8080/consumer/1 10次
    
  + 请求 http://localhost:8080/consumer/2 1次(失败)，必须等到20秒后才能成功。
    
    

**小结**

+ 服务熔断了解，一般使用默认的熔断策略: 20次请求、50%失败、直接进入open休眠5秒。



## 21、课堂总结

+ Eureka注册中心：服务端与客户端
+ Ribbon负载均衡: 实现服务调用负载均衡
+ Hystrix熔断器：线程隔离与服务熔断