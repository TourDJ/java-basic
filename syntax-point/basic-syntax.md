
## JDK 安装配置

#### Deiban(Ubuntu) 下安装配置 JDK(OpenJDK)

1 下载安装jdk	

	$sudo apt-get install openjdk-8-jdk
2 查看当前系统中的JVM

	$sudo update-alternatives --display java
3、安装JVM路径	

	$sudo update-alternatives --install /usr/bin/java java /usr/local/jdk1.8.0_152/bin/java 1400
> 命令： update-alternatives --install link name path priority <br>
	link 为系统中功能相同软件的公共链接目录，比如/usr/bin/java(需绝对目录)	 <br>	
	name 为命令链接符名称,如java  <br>	 	
	path 为你所要使用新命令、新软件的所在目录  <br>		 
	priority 为优先级，当命令链接已存在时，需高于当前值，因为当alternative为自动模式时,系统默认启用priority高的链接;整数根据版本号设置的优先级（更改的优先级需要大于当前的）  <br>

4、更换系统 JVM

	$sudo update-alternatives --config java
5、配置环境变量
在文件 /etc/profile 中添加以下几行： 

    export JAVA_HOME=/usr/lib/jvm/java-1.6.0-openjdk
    export JRE_HOME=$JAVA_HOME/jre  
    export CLASSPATH=.:$JAVA_HOME/lib:$JRE_HOME/lib:$CLASSPATH  
    export PATH=$JAVA_HOME/bin:$JRE_HOME/bin:$JAVA_HOME:$PATH 
    
$source /etc/profile 使配置立刻生效

6、验证配置完成
$echo $JAVA_HOME
$java -version

#### linux 源码安装 jdk
1, 将源码文件 jdk-8u152-linux-x64.tar.gz 解压到 /usr/local 下
2, 配置环境变量
  在/etc/profile文件中，配置环境变量，编辑文件，在最后添加：

    export JAVA_HOME=/usr/java/jdk1.8.0_65 
    export JRE_HOME=$JAVA_HOME/jre 
    export CLASSPATH=.:$JAVA_HOME/lib:$JRE_HOME/lib 
    export PATH=$JAVA_HOME/bin:$PATH

  保存退出后，执行source /etc/profile是修改的环境变量生效。
  注： Debian 默认安装 OPENJDK，路径：
    
    lrwxrwxrwx 1 root root 22 11月  2 22:17 /usr/bin/java -> /etc/alternatives/java

#### linux 下安装 eclipse
1, 下载 eclipse http://www.eclipse.org/downloads/download.php?file=/technology/epp/downloads/release/oxygen/2/eclipse-jee-oxygen-2-linux-gtk-x86_64.tar.gz

2, 解压

	$ tar -zxvf eclipse-SDK-3.7.2-linux-gtk.tar.gz
3、将文件夹移动到安装目录

	$ sudo mv eclipse /usr/local/
4、创建桌面链接
$ sudo vim /usr/share/applications/eclipse.desktop

	[Desktop Entry]
	Encoding=UTF-8
	Name=Eclipse
	Comment=Eclipse IDE
	Exec=eclipse
	Icon=/usr/local/eclipse/icon.xpm
	Terminal=false
	Type=Application
	Categories=GNOME;Application;Development;
	StartupNotify=true


#### dt.jar、tools.jar [作用](http://www.cnblogs.com/jtlgb/p/6039809.html)
* tools.jar：工具类库，它跟我们程序中用到的基础类库没有关系。我们注意到在Path中变量值bin目录下的各个exe工具的大小都很小，一般都在27KB左右，这是因为它们实际上仅仅相当于是一层代码的包装，这些工具的实现所要用到的类库都在tools.jar中，用压缩软件打开tools.jar,你会发现有很多文件是和bin目录下的exe工具相对性的。编译和运行需要的都是toos.jar里面的类分别是   

	sun.tools.java.*;	   
	sun.tools.javac.*;	

* dt.jar：运行环境类库，主要是Swing包。

* rt.jar 是JAVA基础类库，默认就在Root Classloader的加载路径里面的，而在Claspath配置该变量是不需要的；同时jre/lib目录下的其他 jar:jce.jar、jsse.jar、charsets.jar、resources.jar 都在Root Classloader中。

JDK目录结构和文件作用[介绍](http://www.cnblogs.com/sunxucool/p/3489314.html)

***

## J2SE
## Java 语言特性

链接： [java 新特性](https://segmentfault.com/a/1190000004417830)

#### Java 修饰符

Java关于访问权限的关键字有三个：private、protected以及public，同时Java还有一个默认的访问权限——包权限，所以Java总共有四种访问权限控制。

| 访问修饰符  | 同类    | 同包  | 不同包，子类  | 不同包，非子类  |
| -------- | :-----: | :----: | :-----: | :----: |
| private     | √    |  -    | -    |  -    |
| default     | √    |  √    | -    |  -    |
| protected   | √    |  √    | √    |  -    |
| public      | √    |  √    | √    |  √    |


修饰符的修饰范围

| 修饰符                | 类    | 成员方法  | 构造方法  | 成员变量  | 局部变量  |
| -------- | :-----: | :----: | :-----: | :----: | :----: |
| abstract（抽象的）    | √      |  √    | -      |  -    |  -    |
| static（静态的）      | -      |  √    | -      |  √    |  -    |
| public（公共的）      | √      |  √    | √      |  √    |  -    |
| protected（受保护的） | -      |  √    | √      |  √    |  -    |
| private（私有的）     | -      |  √    | √      |  √    |  -    |
| synchronized（同步的）| -      |  √    | -      |  -    |  -    |
| native（本地的）      | -      |  √    | -      |  -    |  -    |
| transient（暂时的）   | -      |  -    | -      |  √    |  -    |
| volatie（易失的）     | -      |  -    | -      |  √    |  -    |
| final(不要改变的)     | √      |  √    | -      |  √    |  √    |

**类修饰符** 
* public: 可以在其他任何类中使用
* abstract: 抽象类，不能被实例化，可以包含抽象方法，抽象方法没有被实现，无具体功能，只能衍生子类。
* final: 不能再声明子类

**构造函数修饰符**  
* public: 可以从所有的类中访问
* protected: 只能从自己的类和它的子类中访问
* private: 只能在本类中访问

**成员变量修饰符**  
一个类的成员变量的声明必须在类体中，而不能在方法中，方法中声明的是局部变量。
* public: 可以从所有的类中访问
* protected: 只能从本类和它的子类中访问
* private: 只能从本类中访问它
* static: 对该类的所有实例只能有一个域值存在
* transient: 不是一个对象持久状态的一部份
* volatile: 可以被异步的线程所修改
* final: 必须对它赋予初值并且不能修改它

**局部变量修饰符** 
* final: 必须对它赋予初值并且不能修改它

**方法修饰符** 
* public: 可以从所有的类中访问它
* protected: 只能从本类及其子类中访问它
* private: 只能从本类中访问它
* abstract: 没有方法体，属于一个抽象类
* final: 子类不能覆盖它
* static: 被绑定于类本身而不是类的实例
* native: 该方法由其他编程语言实现
* asynchronized: 在一个线程调用它之前必须先给它加


final的 [知识点](http://www.importnew.com/7553.html)
1. final关键字可以用于成员变量、本地变量、方法以及类。
2. final成员变量必须在声明的时候初始化或者在构造器中初始化，否则就会报编译错误。
3. 你不能够对final变量再次赋值。
4. 本地变量必须在声明时赋值。
5. 在匿名类中所有变量都必须是final变量。
6. final方法不能被重写。
7. final类不能被继承。
8. final关键字不同于finally关键字，后者用于异常处理。
9. final关键字容易与finalize()方法搞混，后者是在Object类中定义的方法，是在垃圾回收之前被JVM调用的方法。
10. 接口中声明的所有变量本身是final的。
11. final和abstract这两个关键字是反相关的，final类就不可能是abstract的。
12. final方法在编译阶段绑定，称为静态绑定(static binding)。
13. 没有在声明时初始化final变量的称为空白final变量(blank final variable)，它们必须在构造器中初始化，或者调用this()初始化。不这么做的话，编译器会报错“final变量(变量名)需要进行初始化”。
14. 将类、方法、变量声明为final能够提高性能，这样JVM就有机会进行估计，然后优化。
15. 按照Java代码惯例，final变量就是常量，而且通常常量名要大写。

#### 泛型
Java 语言中的[泛型](https://www.ibm.com/developerworks/cn/java/j-jtp01255.html)基本上完全在编译器中实现，由编译器执行类型检查和类型推断，然后生成普通的非泛型的字节码。这种实现技术称为 擦除（erasure）（编译器使用泛型类型信息保证类型安全，然后在生成字节码之前将其清除）。

[通配符](https://www.ibm.com/developerworks/cn/java/j-jtp04298.html)在类型系统中具有重要的意义，它们为一个泛型类所指定的类型集合提供了一个有用的类型范围。

#### 引用对象
引用对象 封装了对另一个对象的引用，这样就可以像其他任何对象一样检查和操作引用自身。有三种类型的引用对象，按从弱到强依次为： 软引用、 弱引用和 虚引用。正如下面定义的那样，每种类型对应于一个不同的可到达性级别。软引用适用于实现内存敏感的缓存，弱引用适用于实现无法防止其键（或值）被回收的规范化映射，而虚引用则适用于以某种比 Java 终结机制更灵活的方式调度 pre-mortem 清除操作。

#### NIO
[NIO](https://www.ibm.com/developerworks/cn/education/java/j-nio/j-nio.html) 的创建目的是为了让 Java 程序员可以实现高速 I/O 而无需编写自定义的本机代码。NIO 将最耗时的 I/O 操作(即填充和提取缓冲区)转移回操作系统，因而可以极大地提高速度。

通道 和 缓冲区 是 NIO 中的核心对象，几乎在每一个 I/O 操作中都要使用它们。

#### HashMap
基于哈希表的 Map 接口的实现。此实现提供所有可选的映射操作，并允许使用 null 值和 null 键。（除了非同步和允许使用 null 之外，[HashMap](http://www.importnew.com/27043.html) 类与 Hashtable 大致相同。）此类不保证映射的顺序，特别是它不保证该顺序恒久不变。

此实现假定哈希函数将元素适当地分布在各桶之间，可为基本操作（get 和 put）提供稳定的性能。迭代 collection 视图所需的时间与 HashMap 实例的“容量”（桶的数量）及其大小（键-值映射关系数）成比例。所以，如果迭代性能很重要，则不要将初始容量设置得太高（或将加载因子设置得太低）。

HashMap 的实例有两个参数影响其性能：初始容量 和加载因子。容量 是哈希表中桶的数量，初始容量只是哈希表在创建时的容量。加载因子 是哈希表在其容量自动增加之前可以达到多满的一种尺度。当哈希表中的条目数超出了加载因子与当前容量的乘积时，则要对该哈希表进行 rehash 操作（即重建内部数据结构），从而哈希表将具有大约两倍的桶数。

通常，默认加载因子 (.75) 在时间和空间成本上寻求一种折衷。加载因子过高虽然减少了空间开销，但同时也增加了查询成本（在大多数 HashMap 类的操作中，包括 get 和 put 操作，都反映了这一点）。在设置初始容量时应该考虑到映射中所需的条目数及其加载因子，以便最大限度地减少 rehash 操作次数。如果初始容量大于最大条目数除以加载因子，则不会发生 rehash 操作。

如果很多映射关系要存储在 HashMap 实例中，则相对于按需执行自动的 rehash 操作以增大表的容量来说，使用足够大的初始容量创建它将使得映射关系能更有效地存储。

注意，此实现不是同步的。如果多个线程同时访问一个哈希映射，而其中至少一个线程从结构上修改了该映射，则它必须 保持外部同步。（结构上的修改是指添加或删除一个或多个映射关系的任何操作；仅改变与实例已经包含的键关联的值不是结构上的修改。）这一般通过对自然封装该映射的对象进行同步操作来完成。如果不存在这样的对象，则应该使用 Collections.synchronizedMap 方法来“包装”该映射。最好在创建时完成这一操作，以防止对映射进行意外的非同步访问，如下所示：

   	Map m = Collections.synchronizedMap(new HashMap(...));
由所有此类的“collection 视图方法”所返回的迭代器都是快速失败 的：在迭代器创建之后，如果从结构上对映射进行修改，除非通过迭代器本身的 remove 方法，其他任何时间任何方式的修改，迭代器都将抛出 ConcurrentModificationException。因此，面对并发的修改，迭代器很快就会完全失败，而不冒在将来不确定的时间发生任意不确定行为的风险。

注意，迭代器的快速失败行为不能得到保证，一般来说，存在非同步的并发修改时，不可能作出任何坚决的保证。快速失败迭代器尽最大努力抛出 ConcurrentModificationException。因此，编写依赖于此异常的程序的做法是错误的，正确做法是：迭代器的快速失败行为应该仅用于检测程序错误。


java [内存](https://www.ibm.com/developerworks/cn/java/j-codetoheap/index.html)使用

## class 字节码文件
class [字节码](http://www.cnblogs.com/ivantang/p/6236711.html)文件是一组以8位字节为基础单位的二进制流，各个数据项目严格按照顺序紧凑地排列在Class文件之中，中间没有添加任何分隔符，这使得整个Class文件中存储的内容几乎全部都是程序运行的必要数据，没有空隙存在。当遇到需要占用8位字节以上的空间的数据项时，则会按照高位在前的方式分割成若干个8位字节进行存储。

## JVM 内部原理的相关特性
JVM([Java虚拟机](http://www.cnblogs.com/ivantang/p/5530787.html))一种用于计算设备的规范，可用不同的方式（软件或硬件）加以实现。编译虚拟机的指令集与编译微处理器的指令集非常类似。Java虚拟机包括一套字节码指令集、一组寄存器、一个栈、一个垃圾回收堆和一个存储方法域。Java虚拟机(JVM)是可运行Java代码的假想计算机。只要根据[JVM规格](http://www.cnblogs.com/ivantang/p/5531580.html)描述将解释器移植到特定的计算机上，就能保证经过编译的任何Java代码能够在该系统上运行。Java虚拟机是一个想象中的机器,在实际的计算机上通过软件模拟来实现。Java虚拟机有自己想象中的硬件,如处理器、[堆栈](http://www.cnblogs.com/ivantang/p/5531501.html)、寄存器等,还具有相应的指令系统。

为什么要分代?
[分代](http://www.cnblogs.com/ivantang/p/5530889.html)的垃圾回收策略，是基于这样一个事实：不同的对象的生命周期是不一样的。因此，不同生命周期的对象可以采取不同的收集方式，以便提高回收效率。

#### HotSpot
Java [HotSpot](http://www.cnblogs.com/ivantang/p/5531587.html) 虚拟机是Sun公司的Java平台一个高效的[虚拟机实现](http://www.cnblogs.com/ivantang/p/5531592.html)。Java HotSpot技术提供Java标准平台的基础设施，提供对快速开发、部署关键业务的桌面和企业应用程序的解决方案。Java SE可应用在Solaris操作环境、Linux和Windows下，也可以应用在其它通过Java技术认证的平台下。

#### 执行引擎
java 虚拟机字节码[执行引擎](http://www.cnblogs.com/sheeva/p/6347577.html)是jvm最核心的组成部分之一，它做的事情很简单：输入的是字节码文件，处理过程是字节码解析的等效过程，输出的是执行结果。

在jvm的内存结构里，存在这一块称为虚拟机栈的内存区域，虚拟机栈中的元素就是栈帧。每个栈帧主要包含4个部分：

局部变量表
操作数栈
动态连接
方法返回地址

#### java 沙箱
Java安全模型的核心就是[Java沙箱](https://yq.aliyun.com/articles/8620)（sandbox），什么是沙箱？沙箱是一个限制程序运行的环境。限制程序运行一方面是为了保护系统资源，同时另一方面也为了保护程序自己。沙箱主要限制系统资源访问，那系统资源包括什么？——CPU、内存、文件系统、网络。不同级别的沙箱对这些资源访问的限制也可以不一样。

组成Java沙箱的基本组件如下：
* 类加载体系结构
* class文件检验器
* 内置于Java虚拟机（及语言）的安全特性
* 安全管理器及Java API

#### java 类加载器
[类加载器](https://www.ibm.com/developerworks/cn/java/j-lo-classloader/index.html)（class loader）用来加载 Java 类到 Java 虚拟机中。
系统提供的类加载器主要有下面三个：
* 引导类加载器（bootstrap class loader）：它用来加载 Java 的核心库，是用原生代码来实现的，并不继承自 java.lang.ClassLoader。
* 扩展类加载器（extensions class loader）：它用来加载 Java 的扩展库。Java 虚拟机的实现会提供一个扩展库目录。该类加载器在此目录里面查找并加载 Java 类。
* 系统类加载器（system class loader）：它根据 Java 应用的类路径（CLASSPATH）来加载 Java 类。一般来说，Java 应用的类都是由它来完成加载的。可以通过 ClassLoader.getSystemClassLoader()来获取它。

#### class 文件校验器
class [文件校验器](http://blog.csdn.net/u013361114/article/details/25034077)就是为了保证class文件有正确的结构，能够正确运行。通过四趟扫描，保证了class文件正确 
* 第一趟：class文件的结构检查
* 第二趟：类型数据的语义检查
* 第三趟：字节码验证
* 第四趟：符号引用的验证

#### java 安全管理器
[安全管理器](https://yq.aliyun.com/articles/57223)(SecurityManager)在Java语言中的作用就是检查操作是否有权限执行。是Java沙箱的基础组件。

#### 策略和保护域
[策略](http://www.importnew.com/17093.html)是一组权限的总称，用于确定权限应该用于哪些代码源。保护域可以理解为代码源和相应权限的一个组合。

***
## Java 内存模型
[JVM内存模型](http://www.cnblogs.com/ivantang/p/5530945.html)是Java的核心技术之一，现在很多编程语言都引入了类似Java JVM的[内存](http://www.cnblogs.com/ivantang/p/5531534.html)模型和垃圾收集器的[机制](http://www.cnblogs.com/ivantang/p/5531496.html)。

#### [Java 内存模型](http://blog.csdn.net/beiyetengqing/article/details/49583381)
Java Memory Model (JAVA 内存模型）描述线程之间如何通过内存(memory)来进行交互。 具体说来， JVM中存在一个主存区（Main Memory或Java Heap Memory），对于所有线程进行共享，而每个线程又有自己的工作内存（Working Memory），工作内存中保存的是主存中某些变量的拷贝，线程对所有变量的操作并非发生在主存区，而是发生在工作内存中，而线程之间是不能直接相互访问，变量在程序中的传递，是依赖主存来完成的。

Java 内存模型（JMM）描述了 Java 程序中各种变量的访问规则，以及在 JVM 中将变量存储到内存和从内存中读取出变量。

> 我们在安装64位JDK时，一般都是server模式运行程序的(默认)。32位不支持server模式。
> server模式与client模式的区别是：server模式启动时，速度较慢，但是一旦运行起来后，性能将会有很大的提升。原因是当虚拟机运行在-client模式的时候,使用的是一个代号为C1的轻量级编译器, 而-server模式启动的虚拟机采用相对重量级,代号为C2的编译器，这个编译器对代码做了很多的优化。

#### [指令的重排序](https://yq.aliyun.com/articles/348581)
编译器或者运行时环境为了优化程序程序性能，可能会对指令进行重新排序。  

重排序的分类
* 编译器优化的重排序：编译器在不改变单线程程序执行结果的前提下，可以重新安排语句的执行顺序。这里需要注意的是：不改变单线程程序的语义（as-if-serial）。  
* 指令级并行的重排序：现代处理器采用了指令级并行技术(ILP)来将多条指令重叠执行。在单线程和单处理器中，如果两个操作之间不存在数据依赖，处理器可以改变语句对应机器指令的执行顺序。  
* 内存系统的重排序：由于处理器使用缓存和读/写缓冲区，处理器会重排对内存的读/写操作的执行顺序。

> [重排序规则表](http://blog.csdn.net/moshenglv/article/details/62423142)

#### happen—before（“先行发生”）规则

[Happens-before](https://www.jianshu.com/p/0909ec597e02) 关系是对在一个线程内执行的操作在另一个线程内的操作的可见性保证。Happens-before 定义程序中所有操作的偏序关系。Happens-before 不仅仅是在时序上对操作进行重排序，它也是对内存读写顺序的保证。

happens-before 的规则：  
* 单线程规则： 单个线程的所有操作都 happens-before 在同一线程中的后续操作  
* 锁定规则： 锁的 unlock（存在于 synchronized 方法或代码块）happens-before 对这个锁的后续获取操作  
* volatile 变量规则：对 volatile 字段的写操作 happens-before 其后对这个字段的所有读操作。 
* 线程启动规则： 在一个线程中对 Thread.start() 的调用 happens-before 被其启动的线程。  
* 线程join规则：线程中的所有操作 happens-before 从这个线程的 join 成功返回的所有其他线程。  
* 传递性 如果 A happens-before B， B happens-before C，那么 A happens-before C。
* 中断规则：当一个线程在另一个线程上调用 interrupt 时，必须在中断线程检测到 interrupt 调用之前执行（通过抛出 InterruptedException，或者调用 isInterrupted 和 interrupted）。
* 终结器规则：对象的构造函数必须在启动该对象的终结期之前执行完成。

#### [内存屏障](http://www.cnblogs.com/chenyangyao/p/5269622.html)
内存屏障（Memory Barrier，或有时叫做内存栅栏，Memory Fence）是一种CPU指令，用于控制特定条件下的重排序和内存可见性问题。Java编译器也会根据内存屏障的规则禁止重排序。
      
内存屏障可以被分为以下几种类型
* LoadLoad 屏障：对于这样的语句Load1; LoadLoad; Load2，在Load2及后续读取操作要读取的数据被访问前，保证Load1要读取的数据被读取完毕。
* StoreStore 屏障：对于这样的语句Store1; StoreStore; Store2，在Store2及后续写入操作执行前，保证Store1的写入操作对其它处理器可见。
* LoadStore 屏障：对于这样的语句Load1; LoadStore; Store2，在Store2及后续写入操作被刷出前，保证Load1要读取的数据被读取完毕。
* StoreLoad 屏障：对于这样的语句Store1; StoreLoad; Load2，在Load2及后续所有读取操作执行前，保证Store1的写入对所有处理器可见。它的开销是四种屏障中最大的。        
> 在大多数处理器的实现中，toreLoad 屏障是个万能屏障，兼具其它三种内存屏障的功能。

[as-if-serial](http://www.cnblogs.com/houziwty/archive/2016/08/05/5742448.html) 语义
as-if-serial 语义的意思指：不管怎么重排序（编译器和处理器为了提高并行度），（单线程）程序的执行结果不能被改变。编译器，runtime 和处理器都必须遵守 as-if-serial 语义。

为了遵守 as-if-serial 语义，编译器和处理器不会对存在数据依赖关系的操作做重排序，因为这种重排序会改变执行结果。但是，如果操作之间不存在数据依赖关系，这些操作可能被编译器和处理器重排序。

## Java 多线程
可见性：一个线程对共享变量的修改能够及时被其它线程看到。
共享变量： 如果一个变量在多个线程的工作内存中都存在内存副本，那么这个变量就是这几个线程的共享变量。

#### 可见性
实现原理  
线程1对共享变量想被线程2看到需要进行以下操作：  
1. 把工作内存中更新的共享变量及时刷新到主内存中  
2. 将主内存中最新的共享变量值更新到工作内存2中  

java语言层面实现可见性的两种方式:  
1. synchnoized  
2. volatile  
synchnoized 能够实现：原子性（同步），可见性；volatile 不能保证原子性。

JMM 关于synchnoized的规定：  
1. 线程解锁之前必须把共享变量的最新值刷新到主内存中。  
2. 线程加锁时将清空工作内存中共享变量的值，从而使用共享变量时需要从主内存中重新读取最新的共享变量的值（注意：加锁与解锁需要时同一把锁）。
3. 线程解锁前对共享变量的修改在下次加锁时对其他线程可见。    

执行的完整流程：    
1. 获得互斥锁  
2. 清空工作内存  
3. 从主内存中拷贝最新的变量副本到工作内存中  
4. 执行代码  
5. 将更改后的共享变量值刷新到主内存中  
6. 释放互斥锁  

> Java 内存模型允许 JVM 将没有被 volatile 修饰的64位数据类型的读写操作划分为两次32的读写操作来执行。

#### ThreadLocal
ThreadLocal类用来提供线程内部的局部变量。这种变量在多线程环境下访问(通过get或set方法访问)时能保证各个线程里的变量相对独立于其他线程内的变量。ThreadLocal实例通常来说都是private static类型的，用于关联线程和线程的上下文。

ThreadLocal的作用是提供线程内的局部变量，这种变量在线程的生命周期内起作用，减少同一个线程内多个函数或者组件之间一些公共变量的传递的复杂度。

知识点：    
[JAVA多线程和并发基础面试问答](http://blog.jobbole.com/76308/)  
[Java中的多线程你只要看这一篇就够了](http://www.importnew.com/21089.html)  
[Java 程序中的多线程](https://www.ibm.com/developerworks/cn/java/multithreading/index.html)  
[java中volatile关键字的含义](http://www.cnblogs.com/aigongsi/archive/2012/04/01/2429166.html)  
[正确使用 Volatile 变量](https://www.ibm.com/developerworks/cn/java/j-jtp06197.html)  
[聊聊并发（一）——深入分析Volatile的实现原理](http://www.infoq.com/cn/articles/ftf-java-volatile)  
[Java 多线程 相关概念](https://juejin.im/post/58de675ea22b9d0058606fb8)
***

## Java 异常

#### Exception

Throwable 类是 Java 语言中所有错误或异常的超类。它有两个子类：Error和Exception。

* Error：用于指示合理的应用程序不应该试图捕获的严重问题。

* Exception：它指出了合理的应用程序想要捕获的条件。Exception又分为两类：一种是CheckedException，一种是UncheckedException。这两种Exception的区别主要是CheckedException需要用try...catch...显示的捕获，而UncheckedException不需要捕获。通常UncheckedException又叫做RuntimeException。
Runtime异常无须显式声明抛出，如果程序需要捕捉Runtime异常，也可以使用try...catch块来捕捉Runtime异常。

> 异常链的使用：保存异常信息，在抛出另外一个异常的同时不丢失原来的异常。
    
    public Throwable initCause(Throwable cause)

#### throw 与 throws

* throw：是语句抛出异常。    
* throws： 是方法可能抛出异常的声明。

> throws可以单独使用，但throw不能， throw要么和try-catch-finally语句配套使用，要么与throws配套使用。但throws可以单独使 用，然后再由处理异常的方法捕获。  

摘自[这里](http://www.cnblogs.com/focusj/archive/2011/12/26/2301524.html)

***

## J2EE

### Web 项目初始化过程
* 在启动Web项目时，容器(比如Tomcat)会读web.xml配置文件中的两个节点 listener 和 contex-param。   
* 接着容器会创建一个 ServletContext(上下文),应用范围内即整个WEB项目都能使用这个上下文。  
* 接着容器会将读取到&lt;context-param&gt; 转化为键值对,并交给 ServletContext。 
* 容器创建 listener 中的类实例,即创建监听（备注：listener定义的类可以是自定义的类但必须需要继承 ServletContextListener）。   
* 在监听的类中会有一个contextInitialized(ServletContextEvent event)初始化方法，在这个方法中可以通过

    event.getServletContext().getInitParameter("contextConfigLocation") 
  来得到context-param 设定的值。在这个类中还必须有一个contextDestroyed(ServletContextEvent event) 销毁方法.用于关闭应用前释放资源，比如说数据库连接的关闭。 
* 得到这个context-param的值之后,你就可以做一些操作了.注意,这个时候你的WEB项目还没有完全启动完成.这个动作会比所有的Servlet都要早。 
    
> 由上面的初始化过程可知容器对于web.xml的加载过程是 context-param -> listener  -> filter  -> servlet 。

> 如何使用
>  页面中: ${initParam.contextConfigLocation}
>  Servlet中: String paramValue=getServletContext().getInitParameter("contextConfigLocation")

### web.xml 标签
#### context-param

    <context-param>  
        <param-name>contextConfigLocation</param-name>  
        <param-value>contextConfigLocationValue></param-value>  
    </context-param>  
    
作用：该元素用来声明应用范围(整个WEB项目)内的上下文初始化参数。
    param-name 设定上下文的参数名称。必须是唯一名称
    param-value 设定的参数名称的值


#### url-pattern
<url-pattern> 元素指定对应于 Servlet 的URL路径，该路径是相对于Web应用程序上下文根的路径。
> Servlet 2.5规范允许<servlet-mapping>的<url-pattern>子元素出现多次，之前的规范只允许一个<servlet-mapping>元素包含一个<url-pattern>子元素。

	<servlet-mapping>
		<servlet-name>welcome</servlet-name>
		<url-pattern>/en/welcome</url-pattern>
		<url-pattern>/zh/welcome</url-pattern>
	</servlet-mapping>

**Servlet 的 url 映射**       	  
当Servlet容器接收到一个请求，它首先确定该请求应该由哪一个Web应用程序来响应。这是通过比较请求URI的开始部分与Web应用程序的上下文路径来确定的。映射到Servlet的路径是请求URI减去上下文的路径，Web应用程序的Context对象在去掉请求URI的上下文路径后，将按照下面的路径映射规则的顺序对剩余部分的路径进行处理，并且在找到第一个成功的匹配后，不再进行下一个匹配。

* 容器试着对请求的路径和Servlet映射的路径进行精确匹配，如果匹配成功，则调用这个Servlet来处理请求。
* 容器试着匹配最长的路径前缀，以斜杠（/）为路径分隔符，按照路径树逐级递减匹配，选择最长匹配的Servlet来处理请求。
* 如果请求的URL路径最后有扩展名，如.jsp，Servlet容器会试着匹配处理这个扩展名的Servlet。
* 如果按照前面3条规则没有找到匹配的Servlet，容器会调用Web应用程序默认的Servlet来对请求进行处理，如果没有定义默认的Servlet，容器将向客户端发送HTTP 404错误信息（请求资源不存在）。

*使用下面的语法来定义映射*: 

* 以/开始并且以 /* 结束的字符串用来映射路径       
* 以*.为前缀的字符串用来映射扩展名         
* 以一个单独的/指示这个Web应用程序默认的Servlet    
* 所有其他的字符被用于精确匹配   

> [问题](http://blog.sina.com.cn/s/blog_7d0b04e70101mclr.html)： 为什么定义”/*.action”这样一个看起来很正常的匹配会错？  
  因为这个匹配即属于路径映射，也属于扩展映射，导致容器无法判断。
***

### Servlet
[servlet](http://www.cnblogs.com/doit8791/p/4209442.html)是一种运行服务器端的java应用程序，具有独立于平台和协议的特性，并且可以动态的生成web页面，它工作在客户端请求与服务器响应的中间层。

#### ServletConfig
代表当前 Servlet 在web.xml中的配置信息。    
例如：

        <servlet>
            <servlet-name>UploadServlet</servlet-name>
            <servlet-class>com.tang.Servlet</servlet-class>
            <init-param>
                <param-name>name</param-name>
                <param-value>value</param-value>
            </init-param>
            <init-param>
                <param-name>encode</param-name>
                <param-value>utf-8</param-value>
            </init-param>
        </servlet>

> 在Servlet的配置文件中，可以使用一个或多个<init-param>标签为servlet配置一些初始化参数。  
  当servlet配置了初始化参数后，web容器在创建servlet实例对象时，会自动将这些初始化参数封装到ServletConfig对象中，并在调用servlet的init方法时，将ServletConfig对象传递给servlet。进而，程序员通过ServletConfig对象就可以得到当前servlet的初始化参数信息。

#### ServletContext
[ServletContext](http://www.cnblogs.com/smyhvae/p/4140877.html)代表当前web应用。WEB容器在启动时，它会为每个WEB应用程序都创建一个对应的ServletContext对象。ServletConfig对象中维护了ServletContext对象的引用。

1.做为域对象可以在整个web应用范围内共享数据。

* 域对象：在一个可以被看见的范围内共享数据用到对象
* 作用范围：整个web应用范围内共享数据
* 生命周期：当服务器启动web应用加载后创建出ServletContext对象后，域产生。当web应用被移除出容器或服务器关闭，随着web应用的销毁域销毁。

2、获取WEB应用的初始化参数

通过<init-param>标签为某一个单独的servlet加配置信息，这种配置信息在其他的Servlet中是无法访问到的。可如果我们使用<context-param>标签（与Servlet标签并列）为整个Web应用配置属性的话，那所有的Servlet就都能访问里面的参数了。例如：可以把数据库的配置信息放在这里。

* 请求参数 parameter --- 浏览器发送过来的请求中的参数信息
* 初始化参数 initparameter --- 在web.xml中为Servlet或ServletContext配置的初始化时带有的基本参数
* 域属性 attribute --- 四大作用域中存取的键值对

***

### Filter
[Filter 简介](http://www.open-open.com/lib/view/open1350703788524.html)   
Servlet API中提供了一个Filter接口，开发web应用时，如果编写的Java类实现了这个接口，则把这个java类称之为过滤器Filter。通过Filter技术，开发人员可以实现用户在访问某个目标资源之前，对访问的请求和响应进行拦截。简单说，就是可以实现web容器对某资源的访问前截获进行相关的处理，还可以在某资源向web容器返回响应前进行截获进行处理。

例如：

	<filter>
		<filter-name>logfilter</filter-name>
		<filter-class>com.tang.filter.LogFilter</filter-class>
	</filter>
	<filter-mapping>
		<filter-name>logfilter</filter-name>
		<url-pattern>/*</url-pattern>
	</filter-mapping>

> [提示](http://www.cnblogs.com/therunningfish/p/6639192.html) FilterChain接口的doFilter方法用于通知Web容器把请求交给Filter链中的下一个Filter去处理，如果当前调用此方法的Filter对象是Filter链中的最后一个Filter，那么将把请求交给目标Servlet程序去处理。

Filter [分类](http://www.cnblogs.com/hh6plus/p/5548049.html)：一共分为四种过滤器：REQUEST, FORWARD, INCLUDE, ERROR，在web.xml的<filter-mapping>域中<dispatcher>进行配置。默认是REQUEST，可复选。



### Listener
[Listener](http://blog.csdn.net/u012228718/article/details/41730799) 是 Servlet 的监听器，它可以监听客户端的请求、服务端的操作等。
Listener 主要用于对 Session、Request、Context 进行监控。	

在Servlet 2.4和JSP 2.0 规范中一共有八个 listener 接口和六个 [event](http://www.blogjava.net/i369/articles/236313.html) 事件类。当一个事件发生的时候我们可以使用listener中的某个方法去对事件做出相应的响应，这些方法的参数都是java.util.EventObject的子类。	
	
8种监听器可以分为三类：
1）监听 Session、request、context 的创建于销毁，分别为  

	HttpSessionLister、ServletContextListener、ServletRequestListener
2）监听对象属性变化，分别为：

	HttpSessionAttributeLister、ServletContextAttributeListener、ServletRequestAttributeListener 
3）监听Session 内的对象，分别为

	HttpSessionBindingListener 和 HttpSessionActivationListener。
与上面六类不同，这两类 Listener 监听的是 Session 内的对象，而非 Session 本身，不需要在 web.xml中配置。


### Interceptor
[Interceptor](http://blog.csdn.net/ggibenben1314/article/details/45341855)是在面向切面编程的时候，在你的service的一个方法前调用一个方法，或者在方法后调用一个方法。两者的本质区别：拦截器是基于java的反射机制的，而过滤器是基于函数回调。
> Interceptor 是框架自己造的概念，Filter是servlet规范里的。

***

## Java8 usage

去掉记录中重复的编号的记录
```java
List<Person> newPersons = persons.stream().collect(Collectors.collectingAndThen
	(Collectors.toCollection(() -> new TreeSet<>
	(Comparator.comparing(Person::getNo))), ArrayList::new)
);
```

```java
public static <T> Predicate<T> distinctByKey(Function<? super T, Object> keyExtractor) {
        Map<Object, Boolean> map = new ConcurrentHashMap<>();
        return t -> map.putIfAbsent(keyExtractor.apply(t), Boolean.TRUE) == null;
    }
```

排序
```java
Comparator<InventoryVO> byFull = Comparator.comparing(InventoryVO::getIsfull);
Comparator<InventoryVO> byNo = Comparator.comparing(InventoryVO::getNo);
Comparator<InventoryVO> byFullAndNo = byFull.thenComparing(byNo);

if(FuncUtils.validList(inventoryVOs)) {
	inventoryVOs = inventoryVOs.stream().sorted(byFullAndNo).collect(toList());
}
```




[Java Programming Tutorial](https://www3.ntu.edu.sg/home/ehchua/programming/java/J2_Basics.html)     
[programming notes](https://www3.ntu.edu.sg/home/ehchua/programming/index.html)       


