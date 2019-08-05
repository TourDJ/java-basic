## Spring Boot

- [Spring Boot 是什么](#whatisspring)      
- [Starter](#starter)      



### <a id="whatisspring">Spring Boot 是什么？</a>
  
<pre>Spring Boot 使您能轻松地创建独立的、生产级的、基于 Spring 且能直接运行的应用程序。我们对 Spring 平台和第三方库有自己的看法，所以您从一开始只会遇到极少的麻烦。</pre>

Spring Boot 是一个轻量级框架，可以完成基于 Spring 的应用程序的大部分配置工作。目的是提供一组工具，以便快速构建容易配置的 Spring 应用程序。


### <a id="starter">Starter</a>
starter 实际上是一组依赖项（比如 Maven POM），这些依赖项是 starter 所表示的应用程序类型所独有的。所有 starter 都使用以下命名约定：spring-boot-starter-XYZ，其中 XYZ 是想要构建的应用程序类型。以下是一些流行的 Spring Boot starter：

* spring-boot-starter-web 用于构建 RESTful Web 服务，它使用 Spring MVC 和 Tomcat 作为嵌入式应用程序容器。
* spring-boot-starter-jersey 是 spring-boot-starter-web 的一个替代，它使用 Apache Jersey 而不是 Spring MVC。
* spring-boot-starter-jdbc 用于建立 JDBC 连接池。它基于 Tomcat 的 JDBC 连接池实现。

您可以访问Spring Boot starter 参考页面来了解每个 starter 的 POM 和依赖项:

* [Spring Boot starter 参考页面](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#using-boot-starter)    



### Spring boot 配置

#### spring-boot-starter-jdbc
Spring 对数据库的操作在jdbc上面做了深层次的封装，使用spring的注入功能，可以把DataSource注册到JdbcTemplate之中。 
JdbcTemplate 是在JDBC API基础上提供了更抽象的封装，并提供了基于方法注解的事务管理能力。 通过使用SpringBoot自动配置功能并代替我们自动配置beans。
在maven中，我们需要增加spring-boot-starter-jdbc模块
```java
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-jdbc</artifactId>
</dependency>
```
通过这个模块为我们做了以下几件事
* tomcat-jdbc－{version}.jar为我们自动配置DataSource.
* 如果你没有定义任何DataSource，SpringBoot将会自动配置一个内存的数据库资源设置
* 如果没有设置任一个beans，SpringBoot会自动注册它
* 初始化数据库


#### spring-boot-configuration-processor
spring默认使用yml中的配置，但有时候要用传统的xml或properties配置，就需要使用spring-boot-configuration-processor了
```java
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-configuration-processor</artifactId>
    <optional>true</optional>
</dependency>
```
再在你的配置类开头加上@PropertySource("classpath:your.properties")，其余用法与加载yml的配置一样



*参考文档：*     
[Spring Boot 基础](https://www.ibm.com/developerworks/cn/java/j-spring-boot-basics-perry/index.html)      

