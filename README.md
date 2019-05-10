# java-basic
这里是 Java 知识点的笔记仓库。那么会记录些什么呢？

## 什么是Java?
Java 是由Sun Microsystems公司于1995年5月推出的高级程序设计语言。Java 以连续多年是最流行的后端编程语言，并且目前仍然是最受欢迎的。

![j2se5](./images/j2se5.gif)   
图：一张很经典的 java 组织架构图

## Java 集合框架

什么是集合框架?摘录了一段官方的说明：

A collections framework is a unified architecture for representing and manipulating collections. All collections frameworks contain the following:

* Interfaces: These are abstract data types that represent collections. Interfaces allow collections to be manipulated independently of the details of their representation. In object-oriented languages, interfaces generally form a hierarchy.
* Implementations: These are the concrete implementations of the collection interfaces. In essence, they are reusable data structures.
* Algorithms: These are the methods that perform useful computations, such as searching and sorting, on objects that implement collection interfaces. The algorithms are said to be polymorphic: that is, the same method can be used on many different implementations of the appropriate collection interface. In essence, algorithms are reusable functionality.

集合框架资料： 
* [集合框架 API](https://docs.oracle.com/javase/8/docs/technotes/guides/collections/index.html)    
* [集合框架文档](https://docs.oracle.com/javase/tutorial/collections/intro/index.html)    

## Java 新特性
Java 8 是当前的主流版本，也是继 Java 6 之后相对稳定的一个版本。Java8 增加了很多使用且能提高性能的功能，比如说流、Lambda 表达式。实际上，Java8 在 Java7 的基础上作了很大的改变，不但增加了很多新特性，而且对现有的代码也作了很多改进，另外，编程风格也完全不同了。

## Fork/Join
Fork-Join 是 Java7 中新增的功能，通过使用 Doug Lea 提供的 Fork/Join 框架，软件开发人员只需要关注任务的划分和中间结果的组合就能充分利用并行平台的优良性能。其他和并行相关的诸多难于处理的问题，例如负载平衡、同步等，都可以由框架采用统一的方式解决。

详细原理可参考官方的 API 说明：     
* [Implementation Overview](./syntax-point/fork-join.md)    

如何使用呢？
* [JDK 7 中的 Fork/Join 模式](https://www.ibm.com/developerworks/cn/java/j-lo-forkjoin/)   

### 流(Stream)
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
  
  Oracle Java 语言架构师 Brian Goetz
[State of the Lambda](http://cr.openjdk.java.net/~briangoetz/lambda/lambda-state-final.html)       
[State of the Lambda: Libraries Edition](http://cr.openjdk.java.net/~briangoetz/lambda/lambda-libraries-final.html)      
[Translation of Lambda Expressions](http://cr.openjdk.java.net/~briangoetz/lambda/lambda-translation.html)     
