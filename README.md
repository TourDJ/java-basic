# java-basic
这里是 Java 知识点的笔记仓库。那么会记录些什么呢？

## 什么是Java?
Java 是由Sun Microsystems公司于1995年5月推出的高级程序设计语言。Java 以连续多年是最流行的后端编程语言，并且目前仍然是最受欢迎的。

![j2se5](./images/j2se5.gif)   
图：一张很经典的 java 组织架构图

## Java 基础知识
### Java 集合框架

什么是集合框架?摘录了一段官方的说明：

A collections framework is a unified architecture for representing and manipulating collections. All collections frameworks contain the following:

* Interfaces: These are abstract data types that represent collections. Interfaces allow collections to be manipulated independently of the details of their representation. In object-oriented languages, interfaces generally form a hierarchy.
* Implementations: These are the concrete implementations of the collection interfaces. In essence, they are reusable data structures.
* Algorithms: These are the methods that perform useful computations, such as searching and sorting, on objects that implement collection interfaces. The algorithms are said to be polymorphic: that is, the same method can be used on many different implementations of the appropriate collection interface. In essence, algorithms are reusable functionality.

集合框架资料： 
* [集合框架 API](https://docs.oracle.com/javase/8/docs/technotes/guides/collections/index.html)    
* [集合框架文档](https://docs.oracle.com/javase/tutorial/collections/intro/index.html)    

### Java 新特性
Java8 增加了很多使用且能提高性能的功能，比如说流、Lambda 表达式。实际上，Java8 在 Java7 的基础上作了很大的改变，不但增加了很多新特性，而且对现有的代码也作了很多改进，另外，编程风格也完全不同了。

### Fork/Join
Fork-Join 是 Java7 中新增的功能，通过使用 Doug Lea 提供的 Fork/Join 框架，软件开发人员只需要关注任务的划分和中间结果的组合就能充分利用并行平台的优良性能。其他和并行相关的诸多难于处理的问题，例如负载平衡、同步等，都可以由框架采用统一的方式解决。

详细原理可参考官方的 API 说明：     
* [Implementation Overview](./syntax-point/fork-join.md)    

如何使用呢？
* [JDK 7 中的 Fork/Join 模式](https://www.ibm.com/developerworks/cn/java/j-lo-forkjoin/)   

### 流(Stream)
Java 8 中的 Stream 是对集合（Collection）对象功能的增强，它专注于对集合对象进行各种非常便利、高效的聚合操作（aggregate operation），或者大批量数据操作 (bulk data operation)。Stream API 借助于同样新出现的 Lambda 表达式，极大的提高编程效率和程序可读性。同时它提供串行和并行两种模式进行汇聚操作，并发模式能够充分利用多核处理器的优势，使用 fork/join 并行方式来拆分任务和加速处理过程。通常编写并行代码很难而且容易出错, 但使用 Stream API 无需编写一行多线程的代码，就可以很方便地写出高性能的并发程序。

流的操作类型分为两种：

* Intermediate：一个流可以后面跟随零个或多个 intermediate 操作。其目的主要是打开流，做出某种程度的数据映射/过滤，然后返回一个新的流，交给下一个操作使用。这类操作都是惰性化的（lazy），就是说，仅仅调用到这类方法，并没有真正开始流的遍历。
* Terminal：一个流只能有一个 terminal 操作，当这个操作执行后，流就被使用“光”了，无法再被操作。所以这必定是流的最后一个操作。Terminal 操作的执行，才会真正开始流的遍历，并且会生成一个结果，或者一个 side effect。

#### collectors
Stream.collect 是一个终端操作,它接收的参数是将流中的元素累积到汇总结果的各种方式，称为收集器。Collectors 中定义了许多采用收集器并生成新收集器的函数方法。

|方法|返回类型|说明|示例|
|-------|----|---------|----------------|
|toList|List<T>|把流中所有元素收集到List中|```List<Menu> menus=Menu.getMenus.stream().collect(Collectors.toList())```|
|toSet|Set<T>|把流中所有元素收集到Set中,删除重复项|```Set<Menu> menus=Menu.getMenus.stream().collect(Collectors.toSet())```|
|toCollection|Collection<T>|把流中所有元素收集到给定的供应源创建的集合中|```ArrayList<Menu> menus=Menu.getMenus.stream().collect(Collectors.toCollection(ArrayList::new))```|
|Counting|Long|计算流中元素个数|```Long count=Menu.getMenus.stream().collect(counting)```|
|SummingInt|Integer|对流中元素的一个整数属性求和|```Integer count=Menu.getMenus.stream().collect(summingInt(Menu::getCalories))```|
|averagingInt|Double|计算流中元素integer属性的平均值|```Double averaging=Menu.getMenus.stream().collect(averagingInt(Menu::getCalories))```|
|Joining|String|连接流中每个元素的toString方法生成的字符串|```String name=Menu.getMenus.stream().map(Menu::getName).collect(joining(“, ”))```|
|maxBy|Optional<T>|一个包裹了流中按照给定比较器选出的最大元素的optional,如果为空返回的是Optional.empty()|```Optional<Menu> fattest=Menu.getMenus.stream().collect(maxBy(Menu::getCalories))```|
|minBy|Optional<T>|```一个包裹了流中按照给定比较器选出的最大元素的optional,如果为空返回的是Optional.empty()|```Optional<Menu> lessest=Menu.getMenus.stream().collect(minBy(Menu::getCalories))```|
|Reducing|归约操作产生的类型|从一个作为累加器的初始值开始,利用binaryOperator与流中的元素逐个结合,从而将流归约为单个值|```int count=Menu.getMenus.stream().collect(reducing(0,Menu::getCalories,Integer::sum))```|
|collectingAndThen|转换函数返回的类型|包裹另一个转换器,对其结果应用转换函数|```Int count=Menu.getMenus.stream().collect(collectingAndThen(toList(),List::size))```|
|groupingBy|Map<K,List<T>>|根据流中元素的某个值对流中的元素进行分组,并将属性值做为结果map的键|```Map<Type,List<Menu>> menuType=Menu.getMenus.stream().collect(groupingby(Menu::getType))```|
|partitioningBy|Map<Boolean,List<T>>|根据流中每个元素应用谓语的结果来对项目进行分区|Map<Boolean,List<Menu>> menuType=Menu.getMenus.stream().collect(partitioningBy(Menu::isType))```|

#### 参考资料
[Java 8 中的 Streams API 详解](https://www.ibm.com/developerworks/cn/java/j-lo-java8streamapi/)      

### Lambda
Java 8 开始 支持 Lambda 了，最早接触 Lambda 是在 C# 中，还有 Javascript 中的箭头函数。

Java 中的 Lambda 实现原理，可以看看 Java 语言架构师 Brian Goetz 写的关于 Lambda 的三部曲：
* [State of the Lambda](http://cr.openjdk.java.net/~briangoetz/lambda/lambda-state-final.html)       
* [State of the Lambda: Libraries Edition](http://cr.openjdk.java.net/~briangoetz/lambda/lambda-libraries-final.html)      
* [Translation of Lambda Expressions](http://cr.openjdk.java.net/~briangoetz/lambda/lambda-translation.html)     


### 函数式接口
函数式接口是指在接口里面只能有一个抽象方法。
```java
    @FunctionalInterface
    interface GreetingService
    {
        void sayMessage(String message);
    }
```
Java 8为函数式接口引入了一个新注解`@FunctionalInterface`，主要用于编译级错误检查，加上该注解，当你写的接口不符合函数式接口定义的时候，编译器会报错。加不加`@FunctionalInterface`对于接口是不是函数式接口没有影响。

#### 参考资料：
[JAVA 8 函数式接口](https://www.cnblogs.com/chenpi/p/5890144.html)      
[Java 8函数式接口的秘密](https://colobu.com/2014/10/28/secrets-of-java-8-functional-interface/)   

### 方法引用

#### 参考资料：
[Java Method References](https://www.javatpoint.com/java-8-method-reference)      

### 经典场景
 - [ThreadLocal 何时用？](./scene/threadlocal.md)    
 - [Java中的弱引用](./scene/java-reference.md)        

## Java 生态圈
### Eclipse
* [Eclipse 常用快捷键](./ecosystem/eclipse.md)    

### Maven
- [Maven](./ecosystem/maven.md)     

### Spring
- [Spring 基础知识](./spring/spring-base.md)       
- [Spring Boot](./spring/spring-boot.md)      



## 技术资料

* [Java command](https://docs.oracle.com/javase/8/docs/technotes/tools/unix/java.html)    
* [Java HotSpot VM](https://www.oracle.com/technetwork/java/javase/index-137495.html)     
* [The Java8 Language Specification](https://docs.oracle.com/javase/specs/jls/se8/html/index.html)     
* [The Java8 Virtual Machine Specification](https://docs.oracle.com/javase/specs/jvms/se8/html/index.html)     


