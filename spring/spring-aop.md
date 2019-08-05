
- [Spring AOP](#spring-aop)     



## <a id="spring-aop">Spring AOP</a>
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
