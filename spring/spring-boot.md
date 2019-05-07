## Spring Boot
### 什么是 Spring Boot?
官方描述：    
Spring Boot 使您能轻松地创建独立的、生产级的、基于 Spring 且能直接运行的应用程序。

Spring Boot 是一个轻量级框架，可以完成基于 Spring 的应用程序的大部分配置工作。目的是提供一组工具，以便快速构建容易配置的 Spring 应用程序。


### Starter
starter 实际上是一组依赖项（比如 Maven POM），这些依赖项是 starter 所表示的应用程序类型所独有的。所有 starter 都使用以下命名约定：spring-boot-starter-XYZ，其中 XYZ 是想要构建的应用程序类型。以下是一些流行的 Spring Boot starter：

* spring-boot-starter-web 用于构建 RESTful Web 服务，它使用 Spring MVC 和 Tomcat 作为嵌入式应用程序容器。
* spring-boot-starter-jersey 是 spring-boot-starter-web 的一个替代，它使用 Apache Jersey 而不是 Spring MVC。
* spring-boot-starter-jdbc 用于建立 JDBC 连接池。它基于 Tomcat 的 JDBC 连接池实现。

您可以访问Spring Boot starter 参考页面来了解每个 starter 的 POM 和依赖项:

* [Spring Boot starter 参考页面](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#using-boot-starter)    

*参考文档：*     
[Spring Boot 基础](https://www.ibm.com/developerworks/cn/java/j-spring-boot-basics-perry/index.html)      

