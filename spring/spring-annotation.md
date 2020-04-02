
- [Spring 注解](#spring-annotation)       
  - [@Configuration](#annotation-Configuration)     
  - [@Bean](#annotation-bean)     
  

## <a id="spring-annotation">Spring 注解</a>

基于注解的容器初始化。

`AnnotationConfigApplicationContext` 提供了三个构造函数用于初始化容器。 
* AnnotationConfigApplicationContext()：该构造函数初始化一个空容器，容器不包含任何 Bean 信息，需要在稍后通过调用其 register() 方法注册配置类，并调用 refresh() 方法刷新容器。
* AnnotationConfigApplicationContext(Class... annotatedClasses)：这是最常用的构造函数，通过将涉及到的配置类传递给该构造函数，以实现将相应配置类中的 Bean 自动注册到容器中。
* AnnotationConfigApplicationContext(String... basePackages)：该构造函数会自动扫描以给定的包及其子包下的所有类，并自动识别所有的 Spring Bean，将其注册到容器中。它不但识别标注 @Configuration 的配置类并正确解析，而且同样能识别使用 @Repository、@Service、@Controller、@Component 标注的类。

除了使用上面第三种类型的构造函数让容器自动扫描 Bean 的配置信息以外，`AnnotationConfigApplicationContext` 还提供了 scan() 方法，其功能与上面也类似，该方法主要用在容器初始化之后动态增加 Bean 至容器中。调用了该方法以后，通常需要立即手动调用 refresh() 刷新容器，以让变更立即生效。 

### @Scope

### <a id="annotation-Configuration">@Configuration</a>

在用于指定配置信息的类上加上 [@Configuration](https://www.tuicool.com/articles/M3MVr2) 注解，明确指出该类是 Bean 配置的信息源。Spring 对标注 Configuration 的类有如下要求：  
* 配置类不能是 final 的；
* 配置类不能是本地化的，亦即不能将配置类定义在其他类的方法内部；
* 配置类必须有一个无参构造函数。

### <a id="annotation-bean">@Bean</a>

`AnnotationConfigApplicationContext` 将配置类中标注了 `@Bean` 的方法的返回值识别为 Spring Bean，并注册到容器中，受 IoC 容器管理。`@Bean` 的作用等价于 XML 配置中的 标签。示例如下：
```java
@Configuration 
public class BookStoreDaoConfig{ 

  @Bean 
  public UserDao userDao(){ return new UserDaoImpl();} 

  @Bean 
  public BookDao bookDao(){return new BookDaoImpl();} 

} 
```
Spring 在解析到以上文件时，将识别出标注 @Bean 的所有方法，执行之，并将方法的返回值 ( 这里是 UserDaoImpl 和 BookDaoImpl 对象 ) 注册到 IoC 容器中。默认情况下，Bean 的名字即为方法名。

`@Bean` 具有以下四个属性： 
* name -- 指定一个或者多个 Bean 的名字。这等价于 XML 配置中 的 name 属性。
* initMethod -- 容器在初始化完 Bean 之后，会调用该属性指定的方法。这等价于 XML 配置中 的 init-method 属性。
* destroyMethod -- 该属性与 initMethod 功能相似，在容器销毁 Bean 之前，会调用该属性指定的方法。这等价于 XML 配置中 的 destroy-method 属性。
* autowire -- 指定 Bean 属性的自动装配策略，取值是 Autowire 类型的三个静态属性。
  * Autowire.BY_NAME
  * Autowire.BY_TYPE
  * Autowire.NO。

与 XML 配置中的 autowire 属性的取值相比，这里少了 constructor，这是因为 constructor 在这里已经没有意义了。`@Bean` 没有直接提供指定作用域的属性，可以通过 `@Scope` 来实现该功能，关于 @Scope 的用法已在上文列举。 

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

@Conditional(TestCondition.class)
标注在类上面，表示该类下面的所有@Bean都会启用配置，也可以标注在方法上面，只是对该方法启用配置。

@ConditionalOnBean（仅仅在当前上下文中存在某个对象时，才会实例化一个Bean）
@ConditionalOnClass（某个class位于类路径上，才会实例化一个Bean）
@ConditionalOnExpression（当表达式为true的时候，才会实例化一个Bean）
@ConditionalOnMissingBean（仅仅在当前上下文中不存在某个对象时，才会实例化一个Bean）
@ConditionalOnMissingClass（某个class类路径上不存在的时候，才会实例化一个Bean）
@ConditionalOnNotWebApplication（不是web应用，才会实例化一个Bean）
@ConditionalOnBean：当容器中有指定Bean的条件下进行实例化。
@ConditionalOnMissingBean：当容器里没有指定Bean的条件下进行实例化。
@ConditionalOnClass：当classpath类路径下有指定类的条件下进行实例化。
@ConditionalOnMissingClass：当类路径下没有指定类的条件下进行实例化。
@ConditionalOnWebApplication：当项目是一个Web项目时进行实例化。
@ConditionalOnNotWebApplication：当项目不是一个Web项目时进行实例化。
@ConditionalOnProperty：当指定的属性有指定的值时进行实例化。
@ConditionalOnExpression：基于SpEL表达式的条件判断。
@ConditionalOnJava：当JVM版本为指定的版本范围时触发实例化。
@ConditionalOnResource：当类路径下有指定的资源时触发实例化。
@ConditionalOnJndi：在JNDI存在的条件下触发实例化。
@ConditionalOnSingleCandidate：当指定的Bean在容器中只有一个，或者有多个但是指定了首选的Bean时触发实例化。

### @ImportResource


## 参考资料
[Spring 注解依赖注入实现](https://www.ibm.com/developerworks/cn/opensource/os-cn-spring-iocannt/index.html)     

