# Java流库（java.util.stream）

> 流提供了一种让我们可以在比集合更高的概念级别上指定计算的数据视图。通过使用流，我们可以说明想要完成什么任务，而不是说明如何去实现它。（这一点受益于lambda表达式）我们将操作的调度留给具体的实现去解决。

> 流大量的用到了lambda表达式（同根生于Java8）之后的篇幅将会说到lambda表达式



[TOC]

## 一、 流的特点

### 流的特点

1. 流并不储存元素。这些元素可能存储在底层的集合中，或这是按需生成。
2. 流的操作不会修改其数据源。例如，filter方法不会从新的流中移除元素，而是会生成一个新的流，其中不包含被过滤掉的元素。
3. 流的操作是尽可能惰性执行的。这意味着直至需要其结果时，操作才会执行。例如如果我们只想查找前5个长单词（长度大于x的String）而不是所有长单词，那么filter方法就会在匹配到第5个单词后停止过滤。**因此，我们甚至可以创建无限流。**

### 典型流程

1. 创建一个流。
2. 指定将初始流转换为其他流的中间操作，可能包含多个步骤。
3. 应用终止操作，从而产生结果。**这个操作会强制执行之前的惰性操作。从此之后，这个流就再也不能用了。**



## 二、 流的创建

> JavaAPI中有大量方法都可以产生流

### API

1. `java.util.Stream<T> 8`自身产生流的方法（需要通过现有流对象）
   - `Stream<T> filter(Predicate<? super T> p)`

     产生一个流，其中包含当前流中满足p的所有元素

     > 这里的Predicate<? super T> 非常灵性，保证了传入的p对象中的`boolean test(T t);`方法一定能接受流中的每个元素。因为保证了Predicate<T>中的T是Stream<T>的超类。

   - `Long count()`

     产生当前流中元素的数量。这是一个终止操作。（严格上不算创建）

		  `java.util.Collection 1.2`的`default方法`	

   -  `default Stream<E> stream() `

   - `default Stream<E> parallelStream() `

     产生当前集合中所有元素的顺序流或者并行流。

3. `java.util.Stream 8`的一些静态方法

   - `static <T> Stream<T> of(T... values)`

     产生一个元素为给定值的流。

   - `static <T> Stream<T> empty()`

     产生一个不包含任何元素的流。

   - `static <T> Stream<T> generate(Supplier<T> s)`

     产生一个无限流，它的值是通过反复调用函数s而构建的。

   - `static  <T> Stream<T> iterate(T seed, UnaryOperator<T> f)`

     产生一个无限流，他的元素包含种子、在种子上调用f产生的值、在前一个元素上调用f产生的值，等等。

4. `java.util.Arrays 1.2`的静态方法

   - `static <T> Stream<T> stream(T[] array, int startInclusive, int endExclusive) 8`

     产生一个流，他的元素是由数组中指定范围内的元素构成的。

5. `java.util.regex.Pattern 1.4`的静态方法

   - `Stream<String> splitAsStream(CharSequence input) 8`

     产生一个流，它的元素是输入中由该模式界定的部分。

6. `java.nio.file.Files 7`

   - `static Stream<String> lines(Path path) 8`

   - `static Stream<String> lines(Path path, Charset cs) 8`

     产生一个流，它的元素是只定文件中的行，该文件的字符集为UTF-8，或者为指定的字符集。

7. `java.util.function.Supplier<T> 8`

   - `T get()`

     提供一个值。

## 三、 filter、map和flatMap方法

> 流的转换会产生一个新的流，它的元素派生自另一个流中的元素。

#### filter：filter转换会产生一个流，它的元素与某种条件相匹配。filter的引元是Predicate<T> ，即从T到boolean的函数。

#### map：通常，我们想要按照某种方式来转换流中的值，此时，可以使用map方法并传递执行该转换的函数。

#### flatMap：见名之意，这是map方法的变种，可以将所有结果生成（摊平）在一个流里。即如果是在map方法中返回`Stream<Stream<T>>`，可以使用flatMap得到结果`Stream<T>`

### API`java.util.stream.Stream 8`

- `Stream<T> filter(Predicate<? super T> predicate)`

  产生一个流，它包含当前流中所有满足断言条件的元素。

- `<R> Stream<R> map (Function<? super T,? extends R> mapper)`

  产生一个流，它包含将mapper应用于当前流中所有元素产生的结果。

  注1：如果返回值是流，则返回流的流（准确来说返回R就是R的流）

  注2：R和T的意思。R为返回值，所以返回值需要是R的子类。T是入参（原有Stream<T>中的T类型），所以接受参数需要是T的超类

- `<R> Stream<R> flatMap (Function<? super T,? extends Stream<? extends R>> mapper)`

  产生一个流，它是通过将mapper应用于当前流中所有元素所产生的结果连接到一起而获得的。

  注意：这里的每个结果都是一个流。

## 四、 抽取子流和连接流

##### 调用`stream.limit(n)`会返回一个新的流，它在n个元素之后结束。（如果原来的流更短，那么就会在流结束时结束）。**这个方法对于裁剪无限流的尺寸会显得特别有用。**

例如：

```java
Stream<Double> stream = Stream.generate(Math::random).limit(100L);
stream.forEach(System.out::println);
```



##### 调用`stream.skip(n)`正好相反：它会丢弃前n个元素。

例如：取前200自然数，然后去掉前100个

```java
Stream<Integer> stream = Stream.iterate(1, (n)->n+1);
stream = stream.limit(200).skip(100);
stream.forEach(System.out::println);
```

##### 我们可以用Stream类的静态方法concat方法将两个流连接起来

例如：

```java
Stream<? extends Object> result = Stream.concat(stream, stream2);
result.forEach(System.out::println);
```

**注意：第一个流不应该是无限的，否则第二个流永远都不会得到处理的机会。**

### API `java.util.stream.Stream 8`

- `Stream<T> limit(Long maxSize)`

  产生一个流，其中包含了当前流中最初的maxSize个元素。

- `Stream<T> skip(Long n)`

  产生一个流，它的元素时当前流中除了前n个元素之外的所有元素。

- `static <T> Stream<T> concat(Stream<? extends T> a, Stream<? extends T> b)`  

  产生一个流，它的元素时a的元素后面跟着b的元素。

## 五、 其他的流转换

#### 一、 流的去重，distinct方法，它的元素是从原有流中产生的，即原来的元素按照同样的顺序剔除重复元素后产生的。这个流显然能够记住它已经看到的元素

#### 二、 流的排序，有多种sorted方法的变体可用。其中一种用于操作Comparable元素的流，而另一种可以接受一个Comparator。

#### 三、 peek方法，peek方法会产生一个流，它的元素与原来流中的元素相同，但是在每次获取一个元素时，都会调用一个函数（你传入peek的函数`Consumer<T>`类型）。

### API `java.util.stream.Stream 8`

- `Stream<T> distinct()`

  产生一个流，包含当前流中所有不同元素。

- `Stream<T> sorted()`

- `Stream<T> sorted(Comparator<? super T> comparator)`

  产生一个流，它的元素时当前流中的所有元素按照顺序排列的。第一个方法要求元素是实现了Comparable类的实例。

- `Stream<T> peek(Comsumer<? super T> action)`

  产生一个流，它与当前流中的元素相同，在获取其中每个元素时，会见其传递给action。

## 六、 简单约简（重要）

> 约简是一种终结操作(terminal operation)，它们会将流约简为可以在程序中使用的非流值

### API `java.util.stream.Stream 8`

- `Optional<T> max(Comparator<? super T> comparator)`

- `Optional<T> min(Comparator<? super T> comparator)`

  分别产生这个流的最大元素和最小元素，使用由给定比较器定义的排序规则，如果这个流为空，会产生一个空的Optional对象。这些操作都是终结操作。

- `Optional<T> findFirst()`

- `Optional<T> findFirst()`

  分别产生这个流的第一个和任意一个元素，如果这个流为空，会产生一个空的Optional对象。这些操作都是中介操作。

- `boolean anyMatch(Predicate<? super T> predicate)`

- `boolean allMatch(Predicate<? super T> predicate)`

- `boolean noneMatch(Predicate<? super T> predicate)`

  分别在这个流中任意元素、所有元素和没有任何元素匹配给定断言时返回true。这些操作都是终结操作。

## 七、 收集结果

### API 

1. `java.util.stream.BaseStream 8`

   - `Iterator<T> oteratpr()`

     产生一个获取当前流中的各个元素的迭代器。这是一种终结操作。

2. `java.util.stream.Stream 8`

   - `void forEach(Consumer<? super T> action)`

     在流的每个元素上调用action。这是一种终结操作。

   - `Object[] toArray()`

   - `<A> A[] toArray(IntFunction<A[]> generator)`

     产生一个对象数组，或者在将引用A[]::new传递给构造器时，返回一个A类型的数组。这些都是终结操作。

   - `<R,A> R collect(Collector<? super T,A,R> collector)`

     使用给定的收集器来收集当前流中的元素。Collectors类有用于多种收集齐的工厂方法。例如

     ```java
     Set<Integer> set = stream.collect(Collectors.toSet());
     TreeSet<Integer> treeSet = stream.collect(Collectors.toCollection(TreeSet::new));
     // 第二种方法可以控制获得的集的种类
     ```

3. `java.util.stream.Collectors 8`

   > 注：Collectors全部是static方法，建议使用静态导入

   - `static <T> Collector<T,?,List<T>> toList()`

   - `static <T> Collector<T,?,Set<T>> toSet()`

     产生一个将元素收集到列表或集中的收集器。

   - `static <T,C extends Collection<T>> Collector<T,?,C> toCollection(Supplier<C> collectionFactory)`

     产生一个将元素收集到任意集合中早的收集器。可以传递一个诸如`TreeSet::new`的构造引用。

   - `static Collector<CharSequence,?,String> joining()`

   - `static Collector<CharSequence,?,String> joining(CharSequence delimiter)`

   - `static Collector<CharSequence,?,String> joining(CharSequence delimiter,CharSequence prefix,CharSequence suffix)`

     产生一个连接字符串的收集齐。分隔符会置于字符串之间，而第一个字符串之前可以有前缀，最后一个字符串之后可以有后缀。如果没有指定，那么它们都为空。

   - `static <T> Collector<T,?,IntSummaryStatistics> summarizingInt(ToIntFunction<? super T> mapper)`

   - `static <T> Collector<T,?,LongSummaryStatistics> summarizingInt(ToLongFunction<? super T> mapper)`

   - `static <T> Collector<T,?,DoubleSummaryStatistics> summarizingInt(ToDoubleFunction<? super T> mapper)`

     产生能够生成(Int|Long|Double)SummaryStatistics对象的收集齐，通过它可以获得将mapper应用于每个元素后产生结果的个数、总和、平均值、最大值和最小值。例如：

     ```java
     Stream<Double> stream = Stream.generate(Math::random).limit(100L);
     
     DoubleSummaryStatistics summary = stream.collect(Collectors.summarizingDouble((D)->D*10));
     summary.getAverage();
     summary.getCount();
     summary.getMax();
     summary.getMin();
     summary.getSum();
     ```

4. `IntSummaryStatistics 8`

   `LongSummaryStatistics 8`

   `DoubleSummaryStatistics 8`

   - `Long getCount()`

     产生汇总后的元素个数。

   - `(int|long|double) getSum()`

   - `double getAverage()`

     产生汇总后元素的总和或平均值，或者在没有任何元素时返回0。

   - `(int|long|double) getMax()`

   - `(int|long|double) getMin()`

     产生汇总后的元素的最大值和最小值，或者在没有任何元素时，产生(Integer|Long|Double).(MAX|MIN)_VALUE。

## 八、 收集到映射表中（Map）

### API `java.util.stream.Collectors 8`

- `static<T,K,U,M extends Map<K,U>> Collector<T,?,M> toMap(Function<? supper T,? extends K> keyMapper, Function<? super T,? extends U> valueMapper, BinaryOperator<U> mergeFunction, Supplier<M> mapSupplier)`

- `static<T,K,U,M extends ConcurrentMap<K,U>> Collector<T,?,M> toConcurrentMap(Function<? supper T,? extends K> keyMapper, Function<? super T,? extends U> valueMapper, BinaryOperator<U> mergeFunction, Supplier<M> mapSupplier)`

  产生一个收集器，它会产生一个映射表(Map)或并发映射表(ConcurrentMap)。keyMapper和valueMapper函数会应用到每个收集到的元素上，从而在所产生的映射表中生成一个键/值项。默认情况下，当两个元素产生相同的键时，会抛出一个IllegalStateException异常。你可以提供一个mergeFunction来合并具有相同键的值。默认情况下，其结果是一个HashMap或ConcurrentHashMap。你可以提供一个mapSupplier，它会产生所期望的映射表实例。

  > 注，最后的两个参数(mergeFunction,mapSupplier)可以没有，API中也提供了没有这俩个参数的函。

  

## 九、 群组和分区

### API `java.util.stream.Collectors 8`

- `static <T,K> Collector<T,?,Map<K,List<T>>> groupingBy(Function<? super T,? extends K> classifier)`

- `static <T,K> Collector<T,?,ConcurrentMap<K,List<T>>> groupingByConcurrent(Function<? super T,? extends K> classifier)`

  产生一个收集器，它会产生一个映射表或并发映射表，其键是将classifier应用于所有收集到的元素上所产生的结果，而值是由具有相同键的元素构成的一个个列表。

- `static <T> Collector<T,?,Map<Boolean,List<T>>> partitioningBy(Preditcate<? super T> predicate)`

  产生一个收集器，它会产生一个映射表，其键是true/false，而值是由满足/不满足断言的元素构成的列表。

## 十、 下游收集器

Collectors提供了很多强大的功能，其中下游收集器可以让我们轻松（可能吧）地处理下游数据（Map<K,V>中的V）

作为下游收集器，可以直接在groupingBy方法中使用，例如：

```java
Stream<Double> stream = Stream.generate(Math::random).limit(10000L).map((k)->k*10);
Map<Integer, Long> collect = stream.collect(Collectors.groupingBy((k)->{return k.intValue();},Collectors.counting()));
collect.forEach((k,v)->System.out.println("K:"+k+"	V:"+v));
```



### API `java.util.stream.Collectors 8`

- `static <T> Collector<T,?,Long> counting()`

  产生一个可以对收集到的元素进行计数的收集器。

- `static<T> Cikkectir<T,?,Integer> summingInt(ToIntFunction<? super T> mapper)`

- `static<T> Cikkectir<T,?,Long> summingLong(ToLongFunction<? super T> mapper)`

- `static<T> Cikkectir<T,?,Double> summingDouble(ToIntFunction<? super T> mapper)`

  产生一个收集器，对将mapper应用到收集到的元素上之后产生的值计算总和。

  例如：

  ```java
  Map<Integer, Integer> collect2 = stream.collect(Collectors.groupingBy((k)->{return k.intValue();},Collectors.summingInt((k)->k.intValue())));
  collect2.forEach((k,v)->System.out.println("K:"+k+"	V:"+v));
  // 这将产生以0-9分开的10各组，v为每个组中所有数的合
  ```

- `static <T> Collector<T,?,Optional<T>> maxBy(Comparator<? super T> comparator)`

- `static <T> Collector<T,?,Optional<T>> minBy(Comparator<? super T> comparator)`

  产生一个收集器，使用comparator指定的排序方法，计算收集到的元素中的最大值和最小值。

- `static <T,U,A,R> Collector<T,?,R> mapping(Function<? super T,? extends U> mapper, Collector<? super U,A,R> downstream)`

  产生一个收集器，它会产生一个映射表，其键是将mapper应用到收集到的数据上而产生的，其值是使用downstream收集器收集到的具有相同键的元素。



## 十一、 约简操作

> reduce方法是一种用于从流中计算某个值的通用机制，其最简单的形式将接受一个二元函数，并从前两个元素开始持续应用它。

> 注意：如果收集操作是并行的，reduce就不是线程安全的，小心使用。

### API `java.util.stream.Stream 8`

- `Optional<T> reduce(BinaryOperator<T> accumulator)`

- `T reduce(T identity, BinaryOperator<T> accumulator)`

- `<U> U reduce(U identity,BiFunction<U, ? super T, U> accumulator,BinaryOperator<U> combiner)`

  > 注意：如果reduce是并行流，那么reduce会产生多个结果，需要提供第二个函数（组合器）将结果合并。

  用给定的accumulator函数产生流中元素的积累总和。如果提供了幺元，那么第一个被累计的元素就是该幺元。如果提供了组合器，那么它可以用来将分别累计的各个部分整合成总和。例如：

  ```java
  int result = words.reduce(0,(total,word)->total+word.length(), (total1, total2)->total1+total2);
  ```

- `<R> R collect(Supplier<R> supplier, BiConsumer<R, ? super T> accumulator, BiConsumer<R, R> combiner)`

  将元素收集到类型R的结果中。在每个部分上，都会调用cupplier来提供初始结果，调用accumulator来交替的将元素添加到结果中，并调用combiner来整合两个结果。