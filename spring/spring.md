
Spring 官方文档： [Spring Framework Reference Documentation](https://docs.spring.io/spring/docs/4.3.13.RELEASE/spring-framework-reference/htmlsingle/)

# Spring 使用


## Spring 标签

* **context:component-scan**    
为了让 Spring 能够扫描类路径中的类并识别出 @Repository、@Component、@Service、@Constroller 注解，需要在 XML 配置文件中启用 Bean 的自动扫描功能
```java
<beans … > 
    ……
    <context:component-scan base-package=”bookstore.dao” /> 
    ……
</beans>
```
该配置项其实也包含了自动注入上述processor的功能，因此当使用<context:component-scan/>后，即可将<context:annotation-config/>省去。

* **context:annotation-config**     
将隐式地向 Spring 容器注册 4 个 BeanPostProcessor：

	  AutowiredAnnotationBeanPostProcessor
	  CommonAnnotationBeanPostProcessor
	  PersistenceAnnotationBeanPostProcessor
	  equiredAnnotationBeanPostProcessor

详情看[这里](http://www.cnblogs.com/iuranus/archive/2012/07/19/2599084.html)



## Spring 注解

参考： [Spring 注解依赖注入实现](https://www.ibm.com/developerworks/cn/opensource/os-cn-spring-iocannt/index.html)     

#### [@Configuration](https://www.tuicool.com/articles/M3MVr2)

明确指出该类是 Bean 配置的信息源。Spring 对标注 Configuration 的类有如下要求：  
* 配置类不能是 final 的；
* 配置类不能是本地化的，亦即不能将配置类定义在其他类的方法内部；
* 配置类必须有一个无参构造函数。


#### @RequestParam  
@RequestParam用于将请求参数区数据映射到功能处理方法的参数上。
例如： 请求中包含username参数（如/requestparam1?userName=zhang），则自动传入。 

      public String login(@RequestParam(value="username", required=true, defaultValue="zhang") String username)

多个参数传递，例如：url?userName=zhangsan&userName=wangwu, 多个数据之间使用“，”分割；参数入参的数据为“zhangsan,wangwu”, 使用如下方式来接收多个请求参数：

      public String login(@RequestParam(value="userName") String []  userNames)     

#### @PathVariable
当使用@RequestMapping URI template 样式映射时， 即 someUrl/{paramId}, 这时的paramId可通过 @Pathvariable注解绑定它传过来的值到方法的参数上。

      @RequestMapping("/blogs/{id}")  
      public void findBlog(@PathVariable String id, Model model) {      
        // implementation omitted  
      } 
上面代码把URI template 中变量 id 的值绑定到方法的参数上。若方法参数名称和需要绑定的uri template中变量名称不一致，需要在@PathVariable("name")指定uri template中的名称。

#### @RequestBody

作用： 
* 该注解用于读取Request请求的body部分数据，使用系统默认配置的HttpMessageConverter进行解析，然后把相应的数据绑定到要返回的对象上；
* 再把HttpMessageConverter返回的对象数据绑定到 controller中方法的参数上。

使用时机：

A) GET、POST方式提交时， 根据request header Content-Type的值来判断:

    application/x-www-form-urlencoded， 可选（即非必须，因为这种情况的数据@RequestParam, @ModelAttribute也可以处理，当然@RequestBody也能处理）；
    multipart/form-data, 不能处理（即使用@RequestBody不能处理这种格式的数据）；
    其他格式， 必须（其他格式包括application/json, application/xml等。这些格式的数据，必须使用@RequestBody来处理）；
B) PUT方式提交时， 根据request header Content-Type的值来判断:

    application/x-www-form-urlencoded， 必须；
    multipart/form-data, 不能处理；
    其他格式， 必须；
> 说明：request的body部分的数据编码格式由header部分的Content-Type指定；

#### @ResponseBody

作用： 
该注解用于将Controller的方法返回的对象，通过适当的HttpMessageConverter转换为指定格式后，写入到Response对象的body数据区。

使用时机：
返回的数据不是html标签的页面，而是其他某种格式的数据时（如json、xml等）使用

#### [@SessionAttributes](http://www.cnblogs.com/daxin/p/SessionAttributes.html)
SessionAttributes 注解应用到 Controller上面，可以将Model中的属性同步到session当中。
在默认情况下，ModelMap 中的属性作用域是 request 级别是，也就是说，当本次请求结束后，ModelMap 中的属性将销毁。如果希望在多个请求中共享 ModelMap 中的属性，必须将其属性转存到 session 中，这样 ModelMap 的属性才可以被跨请求访问。Spring 允许我们有选择地指定 ModelMap 中的哪些属性需要转存到 session 中，以便下一个请求属对应的 ModelMap 的属性列表中还能访问到这些属性。这一功能是通过类定义处标注 @SessionAttributes 注解来实现的。通过[实验](http://www.cnblogs.com/waytofall/p/3460533.html)验证一下。

      @Controller
      @RequestMapping("/Demo.do")
      @SessionAttributes(value={"attr1","attr2"})
      public class Demo {

          @RequestMapping(params="method=index")
          public ModelAndView index() {
              ModelAndView mav = new ModelAndView("index.jsp");
              mav.addObject("attr1", "attr1Value");
              mav.addObject("attr2", "attr2Value");
              return mav;
          }

          @RequestMapping(params="method=index2")
          public ModelAndView index2(@ModelAttribute("attr1")String attr1, @ModelAttribute("attr2")String attr2) {
              ModelAndView mav = new ModelAndView("success.jsp");
              return mav;
          }
      }

#### [@ModelAttribute](http://www.cnblogs.com/Hymen/p/3296952.html)
我们可以在需要访问 Session 属性的 controller 上加上 @SessionAttributes，然后在 action 需要的 User 参数上加上 @ModelAttribute，并保证两者的属性名称一致。SpringMVC 就会自动将 @SessionAttributes 定义的属性注入到 ModelMap 对象，在 setup action 的参数列表时，去 ModelMap 中取到这样的对象，再添加到参数列表。只要我们不去调用 SessionStatus 的 setComplete() 方法，这个对象就会一直保留在 Session 中，从而实现 Session 信息的共享。

@ModelAttribute注释方法
被@ModelAttribute注释的方法会在此controller每个方法执行前被执行，因此对于一个controller映射多个URL的用法来说，要谨慎使用。

#### @RestController 与 @Controller
@RestController注解相当于@ResponseBody ＋ @Controller合在一起的作用。

1)如果只是使用@RestController注解Controller，则Controller中的方法无法返回jsp页面，配置的视图解析器InternalResourceViewResolver不起作用，返回的内容就是Return 里的内容。
例如：本来应该到success.jsp页面的，则其显示success.

2)如果需要返回到指定页面，则需要用 @Controller配合视图解析器InternalResourceViewResolver才行。
3)如果需要返回JSON，XML或自定义mediaType内容到页面，则需要在对应的方法上加上@ResponseBody注解。


#### @ConditionalOnClass
判断当前classpath下是否存在指定类，若是则将当前的配置装载入spring容器。

#### @ConditionalOnProperty
Spring Boot 中有个注解 @ConditionalOnProperty，这个注解能够控制某个configuration是否生效。具体操作是通过其两个属性name以及havingValue来实现的。
* name： 用来从application.properties中读取某个属性值，如果该值为空，则返回false;如果值不为空，则将该值与havingValue指定的值进行比较，如果一样则返回true;否则返回false。如果返回值为false，则该configuration不生效；为true则生效。
* havingValue： 
* matchIfMissing： 

#### @ConditionalOnMissingBean
如果存在指定name的bean，则该注解标注的bean不创建


#### @EnableConfigurationProperties

开启属性注入,有此注解就可以通过 @autowired 注入， 是配合 @ConfigurationProperties 使用的。如果没有 @EnableConfigurationProperties，则使用 @ConfigurationProperties 注解的类上还需要添加 @Component 一类组件。

#### @ConfigurationProperties

读取配置信息并自动封装成实体类，能够批量注入配置文件的属性。@Value 只能单个指定。

#### @AutoConfigureAfter
#### @EnableAspectJAutoProxy

#### @profile
@profile 注解是spring提供的一个用来标明当前运行环境的注解。使用DI来依赖注入的时候，能够根据当前制定的运行环境来注入相应的bean。

@Transactional 
@EnableTransactionManagement


***


## Spring MVC

#### [ViewResolver](http://blog.csdn.net/prince2270/article/details/5891085)
Spring MVC使用ViewResolver来根据controller中返回的view名关联到具体的View对象。使用View对象来渲染返回值以生成最终的视图，如html,json或pdf等。

[ContentNegotiatingViewResolver](http://www.open-open.com/lib/view/open1417705219152.html) 视图解析器,利用他就可以配置多种返回值。
[Migrating spring 3.2 REST to Spring 4](https://javattitude.com/2014/04/20/migrating-spring-3-2-rest-to-spring-4/)

***

## Spring web 自动配置

### @EnableAutoConfiguration

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

## Spring 拦截器

Spring 中实现自定义拦截器只需要3步： 
1、 创建我们自己的拦截器类并实现 HandlerInterceptor 接口。 
2、 创建一个Java类继承 WebMvcConfigurerAdapter 或 WebMvcConfigurationSupport, 并重写 addInterceptors 方法。 
3、 实例化我们自定义的拦截器，然后将对像手动添加到拦截器链中（在addInterceptors方法中添加）。

Spring MVC 中的Interceptor 拦截请求是通过HandlerInterceptor 来实现的。在SpringMVC 中定义一个Interceptor 非常简单，主要有两种方式:
* 第一种方式是要定义的Interceptor类要实现了Spring 的HandlerInterceptor 接口，或者是这个类继承实现了HandlerInterceptor 接口的类，比如Spring 已经提供的实现了HandlerInterceptor 接口的抽象类HandlerInterceptorAdapter ；
* 第二种方式是实现Spring 的 WebRequestInterceptor 接口，或者是继承实现了 WebRequestInterceptor 的类。


TransactionInterceptor

***

## Spring 消息转换器


## Spring 属性管理

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

## Spring AOP
AOP（Aspect Orient Programming），作为面向对象编程的一种补充，广泛应用于处理一些具有横切性质的系统级服务，如事务管理、安全检查、缓存、对象池管理等。
> 横向抽取代码复用: 基于代理技术,在不修改原来代码的前提下,对原有方法进行增强。

#### AOP相关术语
|术语	 | 中文  |描述   |
| ----- | --------- | :------ |
|joinpoint |连接点  |指那些被拦截到的点。在Spring中,这些点指方法(因为Spring只支持方法类型的连接点)。|
|pointcut |切入点  |指需要(配置)被增强的 joinpoint。|
|advice |通知/增强  |指拦截到joinpoint后要做的操作。通知分为前置通知/后置通知/异常通知/最终通知/环绕通知等。|
|aspect |切面	|切入点和通知的结合。|
|target |目标对象|  需要被代理(增强)的对象.|
|proxy |代理对象  |目标对象被AOP 织入 增强/通知后,产生的对象。|
|weaving |织入  |指把增强/通知应用到目标对象来创建代理对象的过程，(Spring采用动态代理织入,AspectJ采用编译期织入和类装载期织入)。|
|introduction |引介  |一种特殊通知,在不修改类代码的前提下,可以在运行期为类动态地添加一些Method/Field(不常用)。|

#### Spring aop 通知
AOP联盟为通知Advice定义了org.aopalliance.aop.Advice接口, Spring在Advice的基础上,根据通知在目标方法的连接点位置,扩充为以下五类：   

|通知	 | 接口  | 注解  |描述   | 备注   | 
| -------- | --------- | ------- | :------ |------- | 
|前置通知 |MethodBeforeAdvice  |@Before  |在目标方法执行前实施增强| 权限控制 |
|后置通知 |AfterReturningAdvice  |@AfterReturning  |在目标方法执行后实施增强| |
|环绕通知 |MethodInterceptor  |@Around |在目标方法执行前后实施增强| 权限控制/性能监控/缓存实现/事务管理 |
|异常抛出通知 |ThrowsAdvice	|@AfterThrowing  |在目标方法抛出异常后实施增强| 发生异常后,记录错误日志 |
|引介通知 |IntroductionInterceptor  |@DeclareParents  |在目标类中添加新的方法和属性| |
|最终final通知 |- |@After  | 不管是否异常,该通知都会执行 | 释放资源 |

#### 切面表达式
|AspectJ 指示器 | 描述 |
|:--- | --------- |
|args()	 | 限制连接点匹配参数为执行类型的执行方法 |
|@args()	 | 限制连接点匹配参数由执行注解标注的执行方法 |
|execution()	 | 匹配连接点的执行方法 |
|this()	 | 限制连接点匹配AOP代理的Bean引用类型为指定类型的Bean |
|target()	 | 限制连接点匹配目标对象为指定类型的类 |
|@target()	 | 限制连接点匹配目标对象被指定的注解标注的类 |
|within()	 | 限制连接点匹配匹配指定的类型 |
|@within()	 | 限制连接点匹配指定注解标注的类型 |
@annotation	 | 限制匹配带有指定注解的连接点 |
最常用的是 execution。

execution 函数定义语法： 	   	

	execution(modifiers-pattern? ret-type-pattern declaring-type-pattern? name-pattern(param-pattern) hrows-pattern?)
	execution(<访问修饰符> <返回类型><方法名>(<参数>)<异常>) 
例如:

      execution(public * *(..)) # 匹配所有public方法. 
      execution(* com.tang.dao.*(..)) # 匹配指定包下所有类方法(不包含子包) 
      execution(* com.tang.dao..*(..)) # 匹配指定包下所有类方法(包含子包) 
      execution(* com.tang.service.impl.OrderServiceImple.*(..)) # 匹配指定类所有方法 
      execution(* com.tang.service.OrderService+.*(..)) # 匹配实现特定接口所有类方法 
      execution(* save*(..)) # 匹配所有save开头的方法
***

> Spring 的 AOP 代理由 Spring 的 IoC 容器负责生成、管理，其依赖关系也由 IoC 容器负责管理。因此，AOP 代理可以直接使用容器中的其他 Bean 实例作为目标，这种关系可由 IoC 容器的依赖注入提供。

#### Spring 原始 AOP
配置文件：

	<bean id="service" class="com.tang.springaop.OrderServiceImpl" />
	<bean id="advice" class="com.tang.springaop.ConcreteInterceptor" />
	<bean id="serviceProxy" class="org.springframework.aop.framework.ProxyFactoryBean">
		<property name="target" ref="service" />
		<property name="interceptorNames" value="advice" />
		<property name="proxyTargetClass" value="false" />
	</bean>
测试代码：

      @RunWith(SpringJUnit4ClassRunner.class)
      @ContextConfiguration(locations = "classpath:applicationContext.xml")
      public class AOPClient {

          @Autowired
          @Qualifier("serviceProxy")
          private OrderService service;

          @Test
          public void client() {
              ...
          }
      }
 
> Spring AOP 框架对 AOP 代理类的处理原则是：如果目标对象的实现类实现了接口，Spring AOP 将会采用 JDK 动态代理来生成 AOP 代理类；如果目标对象的实现类没有实现接口，Spring AOP 将会采用 CGLIB 来生成 AOP 代理类。
     
#### Spring AOP XML 使用
在Spring的配置文件中，所有的切面和通知器都必须定义在 <aop:config> 元素内部。<aop:config> 内部包括 aop:pointcut， aop:advisor， aop:aspect 标签。
* aop:pointcut : 切入点定义
* aop:advisor: 定义Spring传统AOP的切面,只支持一个pointcut/一个advice
* aop:aspect : 定义AspectJ切面的,可以包含多个pointcut/多个advice

配置文件：

      <bean id="advice" class="com.tang.springaop.ConcreteInterceptor" />
	<aop:config proxy-target-class="true">
		<aop:pointcut expression="execution(* com.tang.springaop.OrderServiceImpl.*(..))" id="pointcut"/>
		<aop:advisor advice-ref="advice" pointcut-ref="pointcut"/>	
	</aop:config>

或者

	<context:component-scan base-package="com.tang.springaop" />
	<bean id="advice" class="com.tang.aspect.advice.Aspect" />
	<aop:config>
		<aop:aspect ref="advice">
			<aop:pointcut expression="execution(* com.tang.springaop.OrderServiceImpl.*(..))" id="pointcut" />
			<aop:before method="before" pointcut-ref="pointcut" />
                  <aop:after-returning method="afterReturning" returning="result" pointcut-ref="pointcut" />
                  <aop:around method="around" arg-names="point" pointcut-ref="pointcut" />
                  <aop:after-throwing method="afterThrowing" throwing="ex" pointcut-ref="pointcut" />
                  <aop:after method="after" pointcut-ref="pointcut" />
		</aop:aspect>
	</aop:config>
*method 对应 Aspect 类中的方法*

> Spring 依然采用运行时生成动态代理的方式来增强目标对象，所以它不需要增加额外的编译，也不需要 AspectJ 的织入器支持；而 AspectJ 在采用编译时增强，所以 AspectJ 需要使用自己的编译器来编译 Java 文件，还需要织入器。

#### Spring AOP 注解使用
使用AspectJ注解AOP需要在 applicationContext.xml 文件中开启注解自动代理功能。

	<context:component-scan base-package="com.tang.aspect.advice" />
	<aop:aspectj-autoproxy/>

注解使用：

      /**
       * @Aspect: 指定是一个切面
       * @Component: 指定可以被Spring容器扫描到
       */
      @Aspect
      @Component
      public class AnnoAspect {

            @Before("execution(* com.tang.springaop.OrderServiceImpl.*(..))")
            public void before(JoinPoint point) {
              System.out.printf("before %s%n", point.getKind());
          }

            @AfterReturning(value = "execution(* com.tang.springaop.OrderServiceImpl.*(..))", returning = "result")
            public void afterReturning(JoinPoint point, Object result) {
                System.out.printf("afterReturning 结果为 %s%n", result);
            }

            @Around("execution(* com.tang.springaop.OrderServiceImpl.*(..))")
            public Object around(ProceedingJoinPoint point) throws Throwable {
                long start = System.currentTimeMillis();
                Object result = point.proceed(point.getArgs());
                long time = System.currentTimeMillis() - start;

                System.out.printf("method %s invoke consuming %d ms%n", point.toLongString(), time);

                return result;
            }

            @AfterThrowing(value="execution(* com.tang.springaop.OrderServiceImpl.*(..))", throwing="ex")
            public void afterThrowing(JoinPoint point, Throwable ex) {
                String message = new StringBuilder("method ").append(point.getSignature().getName()).append(" error").toString();
                System.out.println(message);
            }

            @After("execution(* com.tang.springaop.OrderServiceImpl.*(..))")
            public void after(JoinPoint point) {
                System.out.println("After");
            }
      }

**@Pointcut定义切点**  
对于重复的切点,可以使用@Pointcut进行定义, 然后在通知注解内引用。

定义切点方法

      public class SelfPointcut {

          @Pointcut("execution(* com.tang.springaop.OrderServiceImpl.*(..))")
          public void pointcut() {
          }
      }

引用切点,在Advice上像调用方法一样引用切点:
      
      @After("SelfPointcut.pointcut()")
      public void after(JoinPoint point) {
          System.out.println("after");
      }

> AOP 广泛应用于处理一些具有横切性质的系统级服务，AOP 的出现是对 OOP 的良好补充，它使得开发者能用更优雅的方式处理具有横切性质的服务。不管是那种 AOP 实现，不论是 AspectJ、还是 Spring AOP，它们都需要动态地生成一个 AOP 代理类，区别只是生成 AOP 代理类的时机不同：AspectJ 采用编译时生成 AOP 代理类，因此具有更好的性能，但需要使用特定的编译器进行处理；而 Spring AOP 则采用运行时生成 AOP 代理类，因此无需使用特定编译器进行处理。由于 Spring AOP 需要在每次运行时生成 AOP 代理，因此性能略差一些。

**知识点**  
[Java JDK代理、CGLIB、AspectJ代理分析比较](https://zhuanlan.zhihu.com/p/28870960)  
[Spring 实践：AOP](http://www.importnew.com/19041.html)  
[Spring AOP 实现原理与 CGLIB 应用](https://www.ibm.com/developerworks/cn/java/j-lo-springaopcglib/index.html)  

AOP的基本概念
* 切面（Aspect）：业务流程运行的某个特定步骤，也就是应用运行过程的关注点，关注点通常会横切多个对象，因此常被称为横切关注点
* 连接点（JoinPoint）：程序执行过程中明确的点，如方法调用，或者异常抛出。Spring AOP中，连接点总是方法调用。
* 增强处理（Advice）：AOP框架在特定的切入点执行的增强处理。处理有around，before，after等类型。
* 切入点（PointCut）：可以插入增强处理的连接点。

Spring AOP 无需使用任何特殊命令对 Java 源代码进行编译，它采用运行时动态地、在内存中临时生成“代理类”的方式来生成 AOP 代理。

Spring 允许使用 AspectJ Annotation 用于定义切面（Aspect）、切入点（Pointcut）和增强处理（Advice），Spring 框架则可识别并根据这些 Annotation 来生成 AOP 代理。Spring 只是使用了和 AspectJ 5 一样的注解，但并没有使用 AspectJ 的编译器或者织入器（Weaver），底层依然使用的是 Spring AOP，依然是在运行时动态生成 AOP 代理，并不依赖于 AspectJ 的编译器或者织入器。

当启动了 @AspectJ 支持后，只要我们在 Spring 容器中配置一个带 @Aspect 注释的 Bean，Spring 将会自动识别该 Bean，并将该 Bean 作为切面 Bean 处理。
```java
// 定义一个切面
@Aspect
@Component
public class LogAspect {

    @Pointcut("execution(* com.tang.service..*.bookFlight(..))")
    private void logPointCut() {
    }

    @AfterReturning(pointcut = "logPointCut()", returning = "retVala")
    public void logBookingStatus(boolean retVala) { 
        if (retVala) {
            System.out.println("booking flight succeeded!");
        } else {
            System.out.println("booking flight failed!");
        }
    }
}
```

#### 切点表达式
切点的功能是指出切面的通知应该从哪里织入应用的执行流。切面只能织入公共方法。

在Spring AOP中，使用 AspectJ 的切点表达式语言定义切点其中 excecution() 是最重要的描述符，其它描述符用于辅助excecution()。

excecution()的语法如下
```java
	execution(modifiers-pattern? ret-type-pattern declaring-type-pattern? name-pattern(param-pattern) throws-pattern?)
```
这个语法看似复杂，但是我们逐个分解一下，其实就是描述了一个方法的特征：
* modifier-pattern：表示方法的修饰符
* ret-type-pattern：表示方法的返回值
* declaring-type-pattern?：表示方法所在的类的路径
* name-pattern：表示方法名
* param-pattern：表示方法的参数
* throws-pattern：表示方法抛出的异常

注意事项

* 其中后面跟着“?”的是可选项。
* 在各个pattern中，可以使用"*"来表示匹配所有。
* 在param-pattern中，可以指定具体的参数类型，多个参数间用“,”隔开，各个也可以用“*”来表示匹配任意类型的参数，如:
	(String)表示匹配一个String参数的方法；    
	(\*,String)表示匹配有两个参数的方法，第一个参数可以是任意类型，而第二个参数是String类型。    
	(..)表示零个或多个任意的方法参数。
> 使用&&符号表示与关系，使用||表示或关系、使用!表示非关系。在XML文件中使用and、or和not这三个符号。

Spring还提供了一个bean()描述符，用于在切点表达式中引用Spring Beans。例如：
```java
	excecution(* com.tianmaying.service.BlogService.updateBlog(..))  and bean('Blog')
```
这表示将切面应用于BlogService的updateBlog方法上，但是仅限于ID为tianmayingBlog的Bean。

也可以排除特定的Bean：
```java
	excecution(* com.tianmaying.service.BlogService.updateBlog(..))  and !bean('Blog')
```
可用的描述符包括：

* args() 表示方法的参数属于一个特定的类
* @args()
* execution()
* this() 是用来限定方法所属的类，比如this(com.tianmaying.service.BlogServiceInterface)表示实现了com.tianmaying.service.BlogServiceInterface的所有类。如果this括号内是具体类而不是接口的话，则表示单个类。
* target() 表示方法所属的类
* @target()
* within() 表示方法属于一个特定的类
* @within()
* @annotation 表示具有某个标注的方法

> 它们对应的加了@的版本则表示对应的类具有某个标注。

AspectJ提供了五种定义通知的标注：

* @Before：前置通知，在调用目标方法之前执行通知定义的任务
* @After：后置通知，在目标方法执行结束后，无论执行结果如何都执行通知定义的任务
* @After-returning：后置通知，在目标方法执行结束后，如果执行成功，则执行通知定义的任务
* @After-throwing：异常通知，如果目标方法执行过程中抛出异常，则执行通知定义的任务
* @Around：环绕通知，在目标方法执行前和执行后，都需要执行通知定义的任务

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
