
## Spring 使用

- [Spring 标签](#spring-label)     
- [Spring 注解](./spring-annotation.md#spring-annotation)       
- [Spring MVC](#spring-mvc)      
- [Spring web 自动配置](#spring-web)    
- [Spring 拦截器](#spring-interceptor)     
- [Spring 消息转换器](#spring-message)     
- [Spring 属性管理](#spring-property)      




### <a id="spring-label">Spring 标签</a>

#### **context:component-scan**    
为了让 Spring 能够扫描类路径中的类并识别出 @Repository、@Component、@Service、@Constroller 注解，需要在 XML 配置文件中启用 Bean 的自动扫描功能
```java
<beans … > 
    ……
    <context:component-scan base-package=”bookstore.dao” /> 
    ……
</beans>
```
该配置项其实也包含了自动注入上述processor的功能，因此当使用<context:component-scan/>后，即可将<context:annotation-config/>省去。

#### **context:annotation-config**     
将隐式地向 Spring 容器注册 4 个 BeanPostProcessor：

	  AutowiredAnnotationBeanPostProcessor
	  CommonAnnotationBeanPostProcessor
	  PersistenceAnnotationBeanPostProcessor
	  equiredAnnotationBeanPostProcessor

详情看[这里](http://www.cnblogs.com/iuranus/archive/2012/07/19/2599084.html)





### <a id="spring-mvc">Spring MVC</a>

#### [ViewResolver](http://blog.csdn.net/prince2270/article/details/5891085)
Spring MVC使用ViewResolver来根据controller中返回的view名关联到具体的View对象。使用View对象来渲染返回值以生成最终的视图，如html,json或pdf等。

[ContentNegotiatingViewResolver](http://www.open-open.com/lib/view/open1417705219152.html) 视图解析器,利用他就可以配置多种返回值。
[Migrating spring 3.2 REST to Spring 4](https://javattitude.com/2014/04/20/migrating-spring-3-2-rest-to-spring-4/)

***

### <a id="spring-web">Spring web 自动配置</a>

#### @EnableAutoConfiguration

注解 EnableAutoConfiguration 实现了自动装配，相关的类有 WebMvcAutoConfiguration 。
当 WebMvcConfigurationSupport 类不存在的时候，自动装配类 WebMvcAutoConfiguration 才会创建出来，所以增加 @EnableWebMvc 注解以后 WebMvcAutoConfiguration 中配置就不会生效，你需要自己来配置需要的每一项。这种情况下的配置方法建议参考 WebMvcAutoConfiguration 类。

### @EnableWebMvc 、 WebMvcConfigurationSupport 和 WebMvcConfigurerAdapter
使用了 @EnableWebMvc 注解等于扩展了 WebMvcConfigurationSupport 类的功能, 但是没有重写任何方法, 如果需要自定义一些配置，则可以实现接口 WebMvcConfigurer 重写一些相关的方法；如果不需要配置一些特殊的配置属性，则可以直接继承 WebMvcConfigurationSupport 类，而不需要添加 @EnableWebMvc 注解 。
 
有以下几种使用方式：

* @EnableWebMvc + extends WebMvcConfigurerAdapter, 在扩展的类中重写父类的方法即可，这种方式会屏蔽 Spring Boot 的 @EnableAutoConfiguration 中的设置；
* extends WebMvcConfigurationSupport，在扩展的类中重写父类的方法即可，这种方式会屏蔽 Spring Boot 的 @EnableAutoConfiguration 中的设置；
* extends WebMvcConfigurerAdapter，在扩展的类中重写父类的方法即可，这种方式依旧使用 Spring Boot 的 @EnableAutoConfiguration 中的设置。

> 在 WebMvcConfigurationSupport（@EnableWebMvc）和 @EnableAutoConfiguration 这两种方式都有一些默认的设定，而 WebMvcConfigurerAdapter 则是一个abstract class。
> WebMvcConfigurerAdapter 已过时，现在直接实现 WebMvcConfigurer 接口。


***

### <a id="spring-interceptor">Spring 拦截器</a>

Spring 中实现自定义拦截器只需要3步： 
1、 创建我们自己的拦截器类并实现 HandlerInterceptor 接口。 
2、 创建一个Java类继承 WebMvcConfigurerAdapter 或 WebMvcConfigurationSupport, 并重写 addInterceptors 方法。 
3、 实例化我们自定义的拦截器，然后将对像手动添加到拦截器链中（在addInterceptors方法中添加）。

Spring MVC 中的Interceptor 拦截请求是通过HandlerInterceptor 来实现的。在SpringMVC 中定义一个Interceptor 非常简单，主要有两种方式:
* 第一种方式是要定义的Interceptor类要实现了Spring 的HandlerInterceptor 接口，或者是这个类继承实现了HandlerInterceptor 接口的类，比如Spring 已经提供的实现了HandlerInterceptor 接口的抽象类HandlerInterceptorAdapter ；
* 第二种方式是实现Spring 的 WebRequestInterceptor 接口，或者是继承实现了 WebRequestInterceptor 的类。


TransactionInterceptor

***

### <a id="spring-message">Spring 消息转换器</a>


### <a id="spring-property">Spring 属性管理</a>

PropertySource: 属性源，用于存放 key-value 对象的抽象，子类需要实现 getProperty(String name) 返回对应的 value 方法，其中value可以是任何类型不局限在字符串
* EnumerablePropertySource: 增加了一个方法用于返回所有 name 值 getPropertyNames，同时重写的 containsProperty方法，通过getPropertyNames 返回的 key 值进行判断，有助于提升性能
* MapPropertySource：其中的source是以Map形式存放的,重写了getProperty和getPropertyNames
* PropertiesPropertySoruce：同MapPropertySource,只是构造函数的参数不同

PropertySources: 用于存放PropertySource的集合
* MutablePropertySources：用linkList实现PropertySources，可以方便向List链中首位、末位、中间位置增加或替换或删除一个key-value属性值。每次增加或替换时，都会判断这个PropertySource是否存在，如果存在，先删除，保证整个List中name的唯一

PropertyResolver：属性解析器，用于解析相应key的value

Environment: 环境。 比如JDK环境，Servlet 环境，Spring环境等等；每个环境都有自己的配置数据，如System.getProperties()、System.getenv()等可以拿到JDK环境数据；ServletContext.getInitParameter()可以拿到Servlet环境配置数据等等。Environment 本身是一个PropertyResolver，但是提供了Profile特性，即可以根据环境得到相应数据（即激活不同的Profile，可以得到不同的属性数据，比如用于多环境场景的配置（正式机、测试机、开发机DataSource配置））
* MockEnvironment：模拟的环境，用于测试时使用；
* StandardEnvironment：标准环境，普通Java应用时使用，会自动注册System.getProperties() 和 System.getenv()到环境；
* StandardServletEnvironment：标准Servlet环境，其继承了StandardEnvironment，Web应用时使用，除了StandardEnvironment外，会自动注册ServletConfig（DispatcherServlet）、ServletContext及JNDI实例到环境；默认除了StandardEnvironment的两个属性外，还有另外三个属性：servletContextInitParams（ServletContext）、servletConfigInitParams（ServletConfig）、jndiProperties（JNDI）。

Profile：剖面，我们程序可能从某几个剖面来执行应用，比如正式机环境、测试机环境、开发机环境等，每个剖面的配置可能不一样（比如开发机可能使用本地的数据库测试，正式机使用正式机的数据库测试）等；因此呢，就需要根据不同的环境选择不同的配置；只有激活的剖面的组件/配置才会注册到Spring容器，类似于maven中profile

profile 有两种：
* 默认的：通过“spring.profiles.default”属性获取，如果没有配置默认值是“default”
* 明确激活的：通过“spring.profiles.active”获取

查找顺序是：先进性明确激活的匹配，如果没有指定明确激活的（即集合为空）就找默认的；配置属性值从Environment读取。
设置profile属性:    
* 启动Java应用时，通过-D传入系统参数
```java
	-Dspring.profiles.active=dev  
```
* 如果是web环境，可以通过上下文初始化参数设置	
```xml
	<context-param>  
	    <param-name>spring.profiles.active</param-name>  
	    <param-value>dev</param-value>  
	</context-param>
```  
* 通过自定义添加PropertySource
```java
	Map<String, Object> map = new HashMap<String, Object>();  
	map.put("spring.profiles.active", "dev");  
	MapPropertySource propertySource = new MapPropertySource("map", map);  
	env.getPropertySources().addFirst(propertySource); 
```
* 直接设置Profile
```java
	env.setActiveProfiles("dev", "test");
```


***
***

# Spring Boot 使用

Spring boot 博文摘录：

@ SpringBoot理念就是零配置编程，但是如果绝对需要使用XML的配置，我们建议您仍旧从一个@Configuration类开始，你可以使用@ImportResouce注解加载XML配置文件

@ 在使用springboot的时候一般是极少需要添加配置文件的(application.properties除外)，但是在实际应用中也会存在不得不添加配置文件的情况，例如集成其他框架或者需要配置一些中间件等，在这种情况下，我们就需要引入我们自定义的xml配置文件了。

@ Spring Boot 不单单从 application.properties 获取配置，所以我们可以在程序中多种设置配置属性。按照以下列表的优先级排列： 1.命令行参数 2.java:comp/env 里的 JNDI 属性 3.JVM 系统属性 4.操作系统环境变量 5.RandomValuePropertySource 属性类生成的 random.* 属性 6.应用以外的 application.properties（或 yml）文件 7.打包在应用内的 application.properties（或 yml）文件 8.在应用 @Configuration 配置类中，用 @PropertySource 注解声明的属性文件 9.SpringApplication.setDefaultProperties 声明的默认属性

## Spring Boot 配置

Spring Boot 所提供的配置优先级顺序比较复杂。按照优先级从高到低的顺序，具体的列表如下所示。

* 命令行参数
	java -jar example-1.0.jar --server.port=8080 --spring.profiles.active=pro
* 从 java:comp/env 得到的 JNDI 属性。
* 通过 System.getProperties() 获取的 Java 系统参数。
	java -Dname="isea533" -jar app.jar
* 操作系统环境变量。
* 通过 RandomValuePropertySource 生成的 random.* 属性。
* jar包外部的application-{profile}.properties或application.yml(带spring.profile)配置文件
* jar包内部的application-{profile}.properties或application.yml(带spring.profile)配置文件
* jar包外部的application.properties或application.yml(不带spring.profile)配置文件
* jar包内部的application.properties或application.yml(不带spring.profile)配置文件
* 在应用配置 Java 类（包含 @Configuration 注解的 Java 类）中通过 @PropertySource 注解声明的属性文件。
* 通过 SpringApplication.setDefaultProperties 声明的默认属性。


## Spring Boot 静态资源处理

Spring boot默认对 '\/\*\*' 的访问可以直接访问四个目录下的文件：

	* classpath:/public/
	* classpath:/resources/
	* classpath:/static/
	* classpath:/META-INF/resouces/
优先级顺序为：META-INF/resources > resources > static > public

在这几个目录下放置静态的 html 文件可以直接访问，classpath 路径指 src/main/resources 。

## Spring Boot 动态页面


***

## Spring Boot 缓存配置

### 集成 EhCache
EhCache 是一个纯Java的进程内缓存框架，具有快速、精干等特点，是Hibernate中默认的CacheProvider。

EhCache 提供了多种缓存策略，主要分为内存和磁盘两级，所以无需担心容量问题。

使用 Maven 引入:
```java
<dependency>
    <groupId>net.sf.ehcache</groupId>
    <artifactId>ehcache</artifactId>
    <version>2.10.5</version>
</dependency>
```
创建 ehcache.xml 文件：
```xml
<?xml version="1.0" encoding="UTF-8"?>
<ehcache xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:noNamespaceSchemaLocation="http://ehcache.org/ehcache.xsd"
         updateCheck="false">
    <defaultCache
            eternal="false"
            maxElementsInMemory="1000"
            overflowToDisk="false"
            diskPersistent="false"
            timeToIdleSeconds="0"
            timeToLiveSeconds="600"
            memoryStoreEvictionPolicy="LRU" />
 
    <!-- 这里的 users 缓存空间是为了下面的 demo 做准备 -->
    <cache
            name="users"
            eternal="false"
            maxElementsInMemory="100"
            overflowToDisk="false"
            diskPersistent="false"
            timeToIdleSeconds="0"
            timeToLiveSeconds="300"
            memoryStoreEvictionPolicy="LRU" />
</ehcache>
```
ehcache.xml 文件配置详解：

* diskStore：为缓存路径，ehcache分为内存和磁盘两级，此属性定义磁盘的缓存位置。
* defaultCache：默认缓存策略，当ehcache找不到定义的缓存时，则使用这个缓存策略。只能定义一个。
* cache：自定缓存策略，为自定义的缓存策略。

name:缓存名称。
maxElementsInMemory:缓存最大数目
maxElementsOnDisk：硬盘最大缓存个数。
eternal:对象是否永久有效，一但设置了，timeout将不起作用。
overflowToDisk:是否保存到磁盘，当系统当机时
timeToIdleSeconds:设置对象在失效前的允许闲置时间（单位：秒）。仅当eternal=false对象不是永久有效时使用，可选属性，默认值是0，也就是可闲置时间无穷大。
timeToLiveSeconds:设置对象在失效前允许存活时间（单位：秒）。最大时间介于创建时间和失效时间之间。仅当eternal=false对象不是永久有效时使用，默认是0.，也就是对象存活时间无穷大。
diskPersistent：是否缓存虚拟机重启期数据 Whether the disk store persists between restarts of the Virtual Machine. The default value is false.diskSpoolBufferSizeMB：这个参数设置DiskStore（磁盘缓存）的缓存区大小。默认是30MB。每个Cache都应该有自己的一个缓冲区。
diskExpiryThreadIntervalSeconds：磁盘失效线程运行时间间隔，默认是120秒。
memoryStoreEvictionPolicy：当达到maxElementsInMemory限制时，Ehcache将会根据指定的策略去清理内存。默认策略是LRU（最近最少使用）。你可以设置为FIFO（先进先出）或是LFU（较少使用）。
clearOnFlush：内存数量最大时是否清除。
memoryStoreEvictionPolicy:可选策略有：LRU（最近最少使用，默认策略）、FIFO（先进先出）、LFU（最少访问次数）。

Spring缓存注解@Cacheable、@CacheEvict、@CachePut使用  
```java
@Cacheable(value="users")
public List<User> list(User user) {
}

@CacheEvict(cacheNames = "users", key = "#user.id")
```

***

## Spring Cloud 版本
spring cloud 的版本并不是用数字来表示的，而是用伦敦地铁名来命名，并且是按字母顺序递增的。

|Cloud代号|	Boot版本(train)|	Boot版本(tested)|	lifecycle|
|---|---|---|----|
|Angle	  | 1.2.x	| incompatible with 1.3	| EOL in July 2017|
|Brixton	| 1.3.x	| 1.4.x	| 2017-07卒|
|Camden	  | 1.4.x	| 1.5.x	| -|
|Dalston	| 1.5.x	| not expected 2.x	| -|
|Edgware	| 1.5.x	| not expected 2.x | 	-|
|Finchley	| 2.x	| not expected 1.5.x	| -|


***

HandlerMethodArgumentResolver 定义参数转换器


addArgumentResolvers：添加自定义方法参数处理器


***

## 参考资料
* [Spring Framework Reference Documentation](https://docs.spring.io/spring/docs/4.3.13.RELEASE/spring-framework-reference/htmlsingle/)
