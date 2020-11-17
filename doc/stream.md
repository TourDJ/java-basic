
## Java8 Stream 官方描述

支持元素流功能性操作的类，例如集合上的 map-reduce 转换。 例如：
```java
   int sum = widgets.stream()
                    .filter(b -> b.getColor() == RED)
                    .mapToInt(b -> b.getWeight())
                    .sum();  
```   
这里我们使用 widgets `Collection<Widget>` 作为流的源，然后在流上执行 filter-map-reduce 以获得红色小部件的权重之和。 （求和是一个reduction操作的例子）。

这个包中引入的关键抽象是流。 `Stream`，`IntStream`，`LongStream`和`DoubleStream`类是对象以及原始int，long和double类型的流。 流与集合有以下几种不同：

* 没有存储。 流不是存储元素的数据结构; 相反，它通过计算操作的流水线传送诸如数据结构，阵列，生成器功能或 I/O 通道的源的元件。
* 功能性质。 流上的操作产生结果，但不会修改其来源。 例如，过滤从Stream获得的Stream会生成新的Stream而不需要过滤的元素，而不是从源集合中删除元素。
* 懒惰寻求。 许多流操作（如过滤，映射或重复删除）可以懒惰地实现，从而暴露优化的机会。 例如，“找到具有三个连续元音的第一个String”不需要检查所有的输入字符串。 流操作分为中间（Stream生产）操作和终端（价值或副作用生成）操作。 中间操作总是懒惰。
* 可能无限。 虽然集合的大小有限，但流不需要。 诸如`limit(n)`或`findFirst()`之类的可以允许无限流上的计算在有限的时间内完成。
* 消耗品 流的元素只能在流的一生中访问一次。 像Iterator一样 ，必须生成一个新流来重新访问源的相同元素。

流可以通过多种方式获得。 一些例子包括：
* 从 Collection 通过 `stream()` 和 `parallelStream()` 方法;
* 从阵列通过 `Arrays.stream(Object[])` ;
* 从流类静态工厂的方法，如`Stream.of(Object[])`，`IntStream.range(int, int)`或`Stream.iterate(Object, UnaryOperator)`;
* 文件的行可以从`BufferedReader.lines()`获取 ;
* 文件路径的流可以从Files中的方法获得;
* 随机数流可以从`Random.ints()`获得;
* 许多JDK中其它的数据流的方法，包括`BitSet.stream()`， `Pattern.splitAsStream(java.lang.CharSequence`)和`JarFile.stream()`。

附加流源可以由第三方库提供，使用[these techniques](https://docs.oracle.com/javase/8/docs/api/java/util/stream/package-summary.html#StreamSources) 。

### 流的操作和管道
流操作分为中间和终端操作，并且组合形成流管道。 流管道由数据源（例如Collection，阵列，发生器功能或I/O通道）组成; 紧跟着零个或多个中间操作，如`Stream.filter`或`Stream.map`; 以及一个诸如`Stream.forEach`或`Stream.reduce`的终端操作。

中间操作返回一个新的流。 他们总是很懒惰。 执行诸如`filter()`之类的中间操作实际上并不执行任何过滤，而是创建一个新的流，该新流在遍历时将包含与给定谓词匹配的初始流的元素。 在执行管道的终端操作之前，不会开始遍历管道源。

诸如`Stream.forEach`或`IntStream.sum`终端操作可以遍历流以产生结果或副作用。 在执行终端操作之后，流管道被认为被消耗，并且不能再使用; 如果您需要再次遍历相同的数据源，则必须返回到数据源以获取新的流。 在几乎所有情况下，终端操作都是渴望的 ，在返回之前完成对数据源的遍历和处理。 只有终端操作`iterator()`和`spliterator()`不是; 在现有操作不足以满足任务的情况下，这些提供为“逃生舱”，以允许任意客户端控制的管道遍历。

处理流的懒惰实现了显着的效率; 在诸如上述的过滤器 - 映射和示例的流水线中，过滤，映射和求和可以融合到数据的单次传递中，具有最小的中间状态。 懒惰也允许避免在没有必要时检查所有数据; 对于诸如“找到长度超过1000个字符的第一个字符串”等操作，只需检查足够的字符串即可找到具有所需特性的字符串，而无需检查源中可用的所有字符串。 （当输入流是无限大而不仅仅是大的时候，这种行为变得更加重要）

中级操作进一步分为无状态和有状态操作。 
* 无状态操作（例如filter和map ）在处理新元素时不保留先前看到的元素的状态 - 每个元素可以独立于其他元素的操作进行处理。 
* 诸如distinct和sorted有状态操作可以在处理新元件时结合先前看到的元素的状态。

在产生结果之前，状态操作可能需要处理整个输入。 例如，在流已经看到流的所有元素之前，不能产生排序流的任何结果。 因此，在并行计算下，包含有状态中间操作的一些流水线可能需要对数据进行多遍，或者可能需要缓冲重要的数据。 包含无状态中间操作的流水线可以在一次通过中处理，无论是顺序还是并行，具有最少的数据缓冲。

此外，一些操作被认为是短路操作。 如果使用无限输入，则可以产生有限流，因此中间操作是短路的。 如果当无限输入呈现时，终端操作可能会在有限时间内终止。 在流水线中进行短路操作是处理无限流在有限时间内正常终止的必要但不足够的条件。

### 并行性
具有显式for循环的处理元素本质上是串行的。 流通过将计算重构为聚合操作的管道来促进并行执行，而不是作为每个单独元素的必要操作。 所有流操作可以串行或并行执行。 除非明确要求并行性，否则JDK中的流实现将创建串行流。 例如， Collection具有方法`Collection.stream()`和`Collection.parallelStream() `，其分别产生顺序流和并行流; 诸如`IntStream.range(int, int)`等其他流承载方法产生顺序流，但是可以通过调用它们的`BaseStream.parallel()`方法来有效地并行化这些流。 要并行执行先前的“小部件权重总和”查询，我们可以这样做：
```java
  int sumOfWeights = widgets.parallelStream()
                            .filter(b -> b.getColor() == RED)
                            .mapToInt(b -> b.getWeight())
                            .sum();
```
此示例的串行和并行版本之间的唯一区别是创建初始流，使用 “parallelStream()” 而不是 “stream()” 。 当终端操作被启动时，根据被调用的流的方向，顺序或并行地执行流管道。 流是否以串行或并行方式执行可以使用`isParallel()`方法确定，并且可以使用`BaseStream.sequential()`和`BaseStream.parallel()`操作修改流的方向。  

启动终端操作后，将根据调用流的方式依次或并行执行流管道。

除了确定为非确定性的操作，如`findAny()` ，流是顺序还是并行执行，不应该改变计算结果。

大多数流操作接受描述用户指定行为的参数，这些行为通常是lambda表达式。 为了保持正确的行为，这些行为参数必须是非干扰的 ，在大多数情况下必须是无状态的 。 这样的参数总是[functional interface](https://docs.oracle.com/javase/8/docs/api/java/util/function/package-summary.html)的例子 ，如[Function](https://docs.oracle.com/javase/8/docs/api/java/util/function/Function.html) ，并且通常是lambda表达式或方法引用。

### Non-interference
Streams使您能够对各种数据源执行可能并行的聚合操作，包括甚至非线程安全的集合，如ArrayList 。 这是可能的，只有我们能预防甲流的管道的执行过程中与数据源的干扰 。 除了逃生舱操作iterator()和spliterator() ，当调用终端操作时开始执行，终端操作完成时结束。 对于大多数数据源，防止干扰意味着确保在流管线的执行期间完全不修改数据源。 其中值得注意的例外是其源是并发集合的流，它们专门用于处理并发修改。 并发流源是Spliterator报告CONCURRENT特征。
因此，源流可能不并发的流管道中的行为参数不应该修改流的数据源。 据说行为参数会干扰非并发数据源，如果它修改或导致修改流的数据源。 不干扰的需要适用于所有管道，而不仅仅是平行管道。 除非流源是并发的，否则在流管道的执行期间修改流的数据源可能会导致异常，错误的答案或不合规的行为。 对于行为良好的流源，可以在终端操作开始之前对源进行修改，这些修改将反映在被覆盖的元素中。 例如，考虑以下代码：

   List<String> l = new ArrayList(Arrays.asList("one", "two")); Stream<String> sl = l.stream(); l.add("three"); String s = sl.collect(joining(" "));  
首先创建一个由两个字符串组成的列表：“一个”; 和“二”。 然后从该列表创建流。 接下来，通过添加第三个字符串“three”修改列表。 最后，流的元素被收集并连接在一起。 由于列表在终端collect操作开始之前被修改，所以结果将是一个“一二三”的字符串。 从JDK集合返回的所有流和大多数其他JDK类都以这种方式表现良好; 对于其他库生成的流，请参阅Low-level stream construction ，了解构建良好流的要求。
Stateless behaviors
如果流操作的行为参数是有状态的，流管道结果可能不确定或不正确。 有状态的lambda（或实现适当的功能接口的其他对象）是结果取决于在流管道的执行期间可能改变的任何状态。 有状态的lambda的一个例子是map()的map() ：
   Set<Integer> seen = Collections.synchronizedSet(new HashSet<>()); stream.parallel().map(e -> { if (seen.add(e)) return 0; else return e; })...  
这里，如果并行执行映射操作，由于线程调度的差异，相同输入的结果可能因运行而异，而使用无状态的lambda表达式，结果总是相同的。
还要注意，尝试从行为参数访问可变状态会给您带来安全和性能方面的不良选择; 如果您不同步对该状态的访问，则会有数据竞争，因此您的代码已损坏，但是如果您同步访问该状态，则冒有争议的风险有可能破坏您正在寻求的并行性。 最好的方法是避免有状态的行为参数完全流式传输; 通常有一种重组流管道以避免状态的方法。

副作用
一般而言，流行为的行为参数的副作用是不鼓励的，因为它们经常会导致无意识地违反无状态要求以及其他线程安全危害。
如果行为参数确实有副作用，除非明确说明，有没有保证，而在visibility的那些副作用给其他线程，也没有任何保证相同的流管道内的“相同”的元素在不同的操作在同一个线程中执行。 此外，这些效果的排序可能是令人惊讶的。 即使当管道被限制以产生与流源的遇到顺序一致的结果 （例如， IntStream.range(0,5).parallel().map(x -> x*2).toArray()必须产生[0, 2, 4, 6, 8] ）时，不保证将映射器功能应用于各个元件的顺序，或者在什么线程中为给定元素执行任何行为参数。

许多可能诱惑使用副作用的计算可以更安全有效地表达而无副作用，例如使用reduction代替可变累加器。 但是，对于调试目的使用println()等副作用通常是无害的。 少量流操作，如forEach()和peek() ，只能通过副作用进行操作; 这些应谨慎使用。

作为如何将不正确地使用副作用的流管道转换为不适用的流管道的示例，以下代码搜索与给定正则表达式匹配的字符串，并将匹配放在列表中。

   ArrayList<String> results = new ArrayList<>(); stream.filter(s -> pattern.matcher(s).matches()) .forEach(s -> results.add(s)); // Unnecessary use of side-effects!  
此代码不必要地使用副作用。 如果并行执行， ArrayList的非线程安全性将导致不正确的结果，并且添加所需的同步将导致争用，从而破坏并行性的好处。 此外，这里使用副作用是完全不必要的; forEach()可以简单地替换为更安全，更高效，更适合并行化的还原操作：
   List<String>results = stream.filter(s -> pattern.matcher(s).matches()) .collect(Collectors.toList()); // No side-effects!  
Ordering
流可能有也可能没有定义的遇到顺序 。 流是否有遇到顺序取决于源和中间操作。 某些流源（如List或数组）是本质上有序的，而其他数据源（如HashSet ）不是。 一些中间操作（例如sorted() ）可以在其他无序流上施加遇到命令，而其他中间操作可以使有序流无序，例如BaseStream.unordered() 。 此外，一些终端操作可能会忽略遇到的顺序，如forEach() 。

如果一个流被命令，大多数操作被限制为在遇到的顺序中对元素进行操作; 如果流的源是List含有[1, 2, 3] ，然后执行的结果map(x -> x*2)必须是[2, 4, 6] 。 然而，如果源没有定义的遇到顺序，则值[2, 4, 6]任何[2, 4, 6]将是有效结果。

对于顺序流，遇到顺序的存在或不存在不影响性能，仅影响确定性。 如果流被排序，在相同的源上重复执行相同的流管线将产生相同的结果; 如果没有订购，重复执行可能会产生不同的结果。

对于并行流，放宽排序约束有时可以实现更有效的执行。 某些聚合操作，例如过滤重复（ distinct() ）或组合减少（ Collectors.groupingBy() ）可以更有效地实现，如果元素的排序不相关。 类似地，本质上与遇到顺序相关的操作，如limit()可能需要缓冲以确保正确排序，从而破坏并行性的好处。 另外，当流有遭遇订单，但用户并不特别在意那次偶遇秩序的情况下，明确地去订货与流unordered()可以提高某些状态或终端操作的并行性能。 然而，大多数流管线，例如上面的例子的“权重之和”，仍然在订购限制下有效地并行化。

Reduction operations
缩减操作（也称为折叠 ）采用一系列输入元素，并通过重复应用组合操作将其组合成单个摘要结果，例如查找一组数字的和或最大值，或将元素累加到列表中。 该流的类具有普遍减少操作，所谓的多种形式reduce()和collect() ，以及多个专业化还原的形式，如sum() ， max() ，或count() 。
当然，这样的操作可以容易地被实现为简单的顺序循环，如：

   int sum = 0; for (int x : numbers) { sum += x; }  
然而，有一个很好的理由喜欢减少操作超过如上所述的突变积累。 不仅减少“更抽象” - 它在整个流上运行，而不是单个元素 - 但正确构造的减少操作本质上是可并行的，只要用于处理元素的功能是associative和stateless 。 例如，给定一个我们要找到的数字的数据流，我们可以写：
   int sum = numbers.stream().reduce(0, (x,y) -> x+y);  
要么：
   int sum = numbers.stream().reduce(0, Integer::sum);  
这些还原操作可以安全地并行运行，几乎没有修改：

   int sum = numbers.parallelStream().reduce(0, Integer::sum);  
减少并行化很好，因为实现可以并行地对数据的子集进行操作，然后组合中间结果以获得最终的正确答案。 （即使该语言具有“每个并行”结构，变异累积方法仍然需要开发人员向共享累加变量sum提供线程安全更新，然后所需的同步可能消除并行性的任何性能增益。）使用reduce()代替删除reduce()操作的所有负担，并且库可以提供高效的并行实现，而不需要额外的同步。

前面显示的“widgets”示例显示了如何将reduce与其他操作结合来替代大量操作的循环。 如果widgets是Widget对象的集合，它们具有getWeight方法，我们可以找到最重的小部件：

   OptionalInt heaviest = widgets.parallelStream() .mapToInt(Widget::getWeight) .max();  
在其更一般的形式中， reduce上类型的元素的操作<T>得到类型的结果<U>需要三个参数：

   <U> U reduce(U identity, BiFunction<U, ? super T, U> accumulator, BinaryOperator<U> combiner);  
这里， 身份元素既是减少的初始种子值，也是没有输入元素的默认结果。 累加器函数获得部分结果和下一个元素，并产生新的部分结果。 组合器功能组合两个部分结果以产生新的部分结果。 （组合器在并行减少中是必需的，其中输入被分割，为每个分区计算的部分累加，然后组合部分结果以产生最终结果。）
更正式地， identity值必须是组合器功能的标识 。 这意味着，对于所有u ， combiner.apply(identity, u)等于u 。 此外， combiner功能必须是associative ，并必须与兼容accumulator功能：对所有u和t ， combiner.apply(u, accumulator.apply(identity, t))必须equals()到accumulator.apply(u, t) 。

三参数形式是双参数形式的泛化，将映射步骤纳入积累步骤。 我们可以使用更一般的形式重写简单的权重权重示例，如下所示：

   int sumOfWeights = widgets.stream() .reduce(0, (sum, b) -> sum + b.getWeight()) Integer::sum);  
虽然显式的map-reduce表单更易读，因此通常应该是首选。 通过将映射和缩小组合成单个功能，可以优化远程工作的广泛形式。
Mutable reduction
可变缩减操作将输入元素累加到可变结果容器中，例如Collection或StringBuilder ，因为它处理流中的元素。
如果我们想采取一串字符串并将它们连接成一个长的字符串，我们可以通过普通的减少来实现这一点：

   String concatenated = strings.reduce("", String::concat)  
我们会得到所需的结果，甚至可以并行工作。 但是，我们可能不会对演出感到高兴！ 这样的实现将执行大量的字符串复制，并且运行时间将是字符数为O（n ^ 2） 。 更有效的方法是将结果累积到一个StringBuilder ，它是一个用于累积字符串的可变容器。 我们可以使用相同的技术并行化可变减少，就像我们用普通的减少一样。

可变缩减操作称为collect() ，因为它将所需结果汇总到结果容器（如Collection 。 A collect操作需要三个功能：供应商功能构造结果容器的新实例，将输入元素合并到结果容器中的累加器函数，以及将一个结果容器的内容合并到另一个中的组合函数。 这种形式与普通缩减的一般形式非常相似：

   <R> R collect(Supplier<R> supplier, BiConsumer<R, ? super T> accumulator, BiConsumer<R, R> combiner);  
与reduce() ，以抽象的方式表达collect的好处是可以直接适应并行化：只要积累和组合函数满足适当的要求，我们可以并行累积部分结果，然后将它们组合起来。 例如，要将流中的元素的String表示形式收集到一个ArrayList ，我们可以为每个形式写出明显的顺序：

   ArrayList<String> strings = new ArrayList<>(); for (T element : stream) { strings.add(element.toString()); }  
或者我们可以使用可并行化的收集表单：
   ArrayList<String> strings = stream.collect(() -> new ArrayList<>(), (c, e) -> c.add(e.toString()), (c1, c2) -> c1.addAll(c2));  
或者将绘图操作拉出累加器函数，我们可以更简洁地表达：
   List<String> strings = stream.map(Object::toString) .collect(ArrayList::new, ArrayList::add, ArrayList::addAll);  
在这里，我们的供应商只是ArrayList constructor ，累加器将一个字符串元素添加到一个ArrayList ，组合器只需使用addAll将字符串从一个容器复制到另一个容器中。
collect - 供应商，蓄能器和组合器的三个方面紧密耦合。 我们可以使用Collector的抽象来捕获所有三个方面。 将字符串收集到List中的上述示例可以使用标准Collector ：

   List<String> strings = stream.map(Object::toString) .collect(Collectors.toList());  
将可变减少包装到收集器中有另一个优点：可组合性。 Collectors类包含了一些收集器的预定义工厂，包括将一个收集器转换成另一个收集器的组合器。 例如，假设我们有一个收集器来计算员工流量的总和，如下所示：

   Collector<Employee, ?, Integer> summingSalaries = Collectors.summingInt(Employee::getSalary);  
（该? 。对于第二类参数仅仅表明我们不关心这个收集器所使用的中间表示）如果我们想创造一个收藏家制表工资由部门的总和，我们可以重用summingSalaries使用groupingBy ：
   Map<Department, Integer> salariesByDept = employees.stream().collect(Collectors.groupingBy(Employee::getDepartment, summingSalaries));  
与正常的缩减操作一样， collect()操作只能在符合条件的情况下进行并行化。 对于任何部分累积的结果，将其与空结果容器组合必须产生等效结果。 也就是说，部分累加结果p即任何系列累加器和组合器的调用的结果， p必须等同于combiner.apply(p, supplier.get()) 。

此外，然而，计算是分裂的，它必须产生等效的结果。 对于任何输入元素t1和t2 ，下面的计算中的结果r1和r2必须是等效的：

   A a1 = supplier.get(); accumulator.accept(a1, t1); accumulator.accept(a1, t2); R r1 = finisher.apply(a1); // result without splitting A a2 = supplier.get(); accumulator.accept(a2, t1); A a3 = supplier.get(); accumulator.accept(a3, t2); R r2 = finisher.apply(combiner.apply(a2, a3)); // result with splitting  
这里，等效通常意味着根据Object.equals(Object) 。 但是在某些情况下，可以放松等价以解决顺序上的差异。

Reduction, concurrency, and ordering
对于一些复杂的减少操作，例如一个collect() ，其产生Map ，如：
   Map<Buyer, List<Transaction>> salesByBuyer = txns.parallelStream() .collect(Collectors.groupingBy(Transaction::getBuyer));  
实际上并行执行操作可能会适得其反。 这是因为组合步骤（将一个Map合并成另一个按键）对于一些Map实现可能是昂贵的。
然而，假设在此减少中使用的结果容器是可同时修改的集合，例如ConcurrentHashMap 。 在这种情况下，累加器的并行调用实际上可以将它们的结果同时存入相同的共享结果容器中，从而无需组合器来合并不同的结果容器。 这可能会提高并行执行性能。 我们称之为并发减少。

甲Collector支持并发还原标有Collector.Characteristics.CONCURRENT特性。 然而，并发收集也有缺点。 如果多个线程将结果并入到共享容器中，则结果存入的顺序是非确定性的。 因此，只有对于正在处理的流的顺序不重要，并发减少才是可能的。 该Stream.collect(Collector)实施将只执行并发如果减少

流平行;
收藏家具有Collector.Characteristics.CONCURRENT特征，
流是无序的，或者收集器具有Collector.Characteristics.UNORDERED的特征。
您可以使用BaseStream.unordered()方法确保流无序 。 例如：
   Map<Buyer, List<Transaction>> salesByBuyer = txns.parallelStream() .unordered() .collect(groupingByConcurrent(Transaction::getBuyer));  
（其中Collectors.groupingByConcurrent(java.util.function.Function<? super T, ? extends K>)是同时相当于groupingBy ）。
请注意，如果给定键的元素以它们在源中显示的顺序显示很重要，那么我们不能使用并发缩减，因为排序是并发插入的一种伤害。 然后，我们将被限制为执行顺序减少或基于并行的并行还原。

Associativity
如果以下条件成立，操作员或功能op是关联的：
   (a op b) op c == a op (b op c)  
如果我们将其扩展为四个术语，那么可以看出这一点对于并行评估的重要性：
   a op b op c op d == (a op b) op (c op d)  
所以我们可以评估(a op b)与(c op d)并行，然后调用op的结果。
关联操作的示例包括数字加法，最小和最大值以及字符串连接。

Low-level stream construction
到目前为止，所有流示例都使用了Collection.stream()或Arrays.stream(Object[])等方法来获取流。 这些流动方法如何实施？
StreamSupport课程有许多低级的创建流的方法，全部使用某种形式的Spliterator 。 甲spliterator是的并行模拟Iterator ; 它描述了（可能是无限的）元素集合，支持顺序前进，批量遍历，并将输入的一部分分解成可并行处理的另一个分割器。 在最底层，所有的流都由一个分流器驱动。

在实现拼接器方面有许多实现选择，几乎所有这些都是实现简单性和使用该拼接器的流的运行时性能之间的权衡。 使用Spliterators.spliteratorUnknownSize(java.util.Iterator, int)从创建一个迭代器创建一个最简单的但是性能最差的方法。 虽然这样的分配器将会工作，但由于我们丢失了大小的信息（底层数据集有多大）以及被限制在简单的分割算法上，所以可能会提供较差的并行性能。

更高质量的spliterator将提供均衡的和已知的尺寸分割，准确大小的信息，以及许多其它characteristics可以由实现中使用，以优化执行spliterator或数据。

可变数据源的分流器有另外的挑战; 因为数据可能在创建分割器的时间和流管线的执行时间之间改变，所以与数据的绑定时间。 理想情况下，流的分流器将报告IMMUTABLE或CONCURRENT ; 如果不是应该是late-binding 。 如果源不能直接提供推荐的拼接器，则可以使用Supplier间接提供Supplier ，并通过Supplier接受的版本stream()构建流 。 只有在流量管道的终端运行开始之后，才能从供应商获得分配器。

这些要求大大降低了流源的突变和流管线的执行之间的潜在干扰的范围。 基于具有所需特性的分流器或使用基于供应商的工厂形式的流的流在终端操作开始之前不受数据源的修改（提供到流操作的行为参数满足非标准的要求标准）干扰和无国籍）。 详见Non-Interference 。

### 参考资料
[Java8 API 中文版](http://www.matools.com/api/java8)       
