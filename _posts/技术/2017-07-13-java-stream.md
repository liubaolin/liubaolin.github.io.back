---
layout: post
title: Java Stream
category: 技术
tags: Java高级
keywords: java8 stream
description:
---

# 1、Java Stream API 入门

## Java8引入函数式编程

- 代码简洁，使用stream接口让你从此告别for循环
- 多核友好，Java函数式编程使得编写并行程序从未如此简单，你需要的全部就是调用一下parallel()方法


## Java函数式编程的主角 Stream

> stream并不是某种数据结构，它只是==数据源的一种视图==。这里的数据源可以是一个数组，Java容器或I/O channel等。因如此要得到一个stream通常不会手动创建，而是调用对应的工具方法，比如：

- 调用Collection.stream()或者Collection.parallelStream()方法
- stream(T[] array)方法
- Stream 接口关系继承图

![image](http://orybhlel7.bkt.clouddn.com/image/png/stream-interface.png)

>图中4种stream接口继承自BaseStream，其中IntStream, LongStream, DoubleStream对应三种基本类型（int, long, double，注意不是包装类型），Stream对应所有剩余类型的stream视图

## Stream的特点

> 虽然大部分情况下stream是容器调用Collection.stream()方法得到的，但stream和collections有以下不同：

- ==无存储==。stream不是一种数据结构，它只是某种数据源的一个视图，数据源可以是一个数组，Java容器或I/O channel等。
- ==为函数式编程而生==。对stream的任何修改都不会修改背后的数据源，比如对stream执行过滤操作并不会删除被过滤的元素，而是会产生一个不包含被过滤元素的新stream。
- ==惰式执行==。stream上的操作并不会立即执行，只有等到用户真正需要结果的时候才会执行。
- ==可消费性==。stream只能被“消费”一次，一旦遍历过就会失效，就像容器的迭代器那样，想要再次遍历必须重新生成。

>对stream的操作分为为两类，==中间操作==(intermediate operations)和==结束操作==(terminal operations)，二者特点是：

1. 中间操作总是会惰式执行，调用中间操作只会生成一个标记了该操作的新stream，仅此而已。
2. 结束操作会触发实际计算，计算发生时会把所有中间操作积攒的操作以pipeline的方式执行，这样可以减少迭代次数。计算完成之后stream就会失效。


## Stream常见方法

> Stream接口的部分常见方法：


操作类型 | 接口方法
---|---
中间操作 | concat() distinct() filter() flatMap() limit() map() peek() skip() sorted() parallel() sequential() unordered()
结束操作 | allMatch() anyMatch() collect() count() findAny() findFirst() forEach() forEachOrdered() max() min() noneMatch() reduce() toArray()

**区分中间操作和结束操作最简单的方法，就是看方法的返回值，返回值为stream的大都是中间操作，否则是结束操作。**

## Stream方法的使用

> stream跟函数接口关系非常紧密，没有函数接口stream就无法工作。回顾一下：==函数接口是指内部只有一个抽象方法的接口。通常函数接口出现的地方都可以使用Lambda表达式==，所以不必记忆函数接口的名字。   

- forEach()    

```
Stream<String> stream = Stream.of("I","love","yout","too");
stream.forEach(str -> System.out.println(str));

```

  由于forEach()是结束方法，上述代码会立即执行，输出所有字符串。

- filter()    

    函数原型为Stream<T> filter(Predicate<? super T> predicate)，作用是返回一个只包含满足predicate条件元素的Stream。

```
Stream<String> stream = Stream.of("I","love","you","too");
stream.filter(str -> str.length() == 3).forEach(str -> System.out.println(str));

```

- distinck()

    函数原型为Stream<T> distinct()，作用是返回一个去除重复元素之后的Stream。

```
Stream<String> stream = Stream.of("I","love","you","too","too");
stream.distinct().forEach(str -> System.out.println(str));
```

- sorted()

    排序函数有两个，一个是用自然顺序排序，一个是使用自定义比较器排序，函数原型分别为Stream<T>　sorted()和Stream<T>　sorted(Comparator<? super T> comparator)。


```
Stream<String> stream = Stream.of("I","love","you","too");
stream.sorted((str1, str2) -> str1.length() - str2.length()).forEach(str -> System.out.println(str));
```

- map()

函数原型为<R> Stream<R> map(Function<? super T,? extends R> mapper)，作用是返回一个对当前所有元素执行执行mapper之后的结果组成的Stream。直观的说，就是对每个元素按照某种操作进行转换，转换前后Stream中元素的个数不会改变，但元素的类型取决于转换之后的类型。

```
Stream<String> stream = Stream.of("I","love","you","too");
stream.map(str -> str.toUpperCase()).forEach(str -> System.out.println(str));
```

- flatMap

    函数原型为<R> Stream<R> flatMap(Function<? super T,? extends Stream<? extends R>> mapper)，作用是对每个元素执行mapper指定的操作，并用所有mapper返回的Stream中的元素组成一个新的Stream作为最终返回结果。说起来太拗口，通俗的讲flatMap()的作用就相当于把原stream中的所有元素都”摊平”之后组成的Stream，转换前后元素的个数和类型都可能会改变。


```
Stream<List<Integer>> stream = Stream.of(Arrays.asList(1, 2), Arrays.asList(3, 4));
stream.flatMap(list -> list.stream()).forEach(i -> System.out.println(i));
```

# 2、Java Stream API 进阶

## Stream的规约操作

> 规约操作（reduction operation）又被称作折叠操作（fold），是通过某个连接动作将所有元素汇总成一个汇总结果的过程。元素求和、求最大值或最小值、求出元素总个数、将所有元素转换成一个列表或集合，都属于规约操作。Stream类库有两个通用的规约操作reduce()和collect()，也有一些为简化书写而设计的专用规约操作，比如sum()、max()、min()、count()等。

>最大或最小值这类规约操作很好理解（至少方法语义上是这样），我们着重介绍reduce()和collect()，这是比较有魔法的地方。

### 多面手reduce()

reduce操作可以实现从一组元素中生成一个值，sum()、max()、min()、count()等都是reduce操作，将他们单独设为函数只是因为常用。reduce()的方法定义有三种重写形式：

- Optional<T> reduce(BinaryOperator<T> accumulator)
- T reduce(T identity, BinaryOperator<T> accumulator)
- <U> U reduce(U identity, BiFunction<U,? super T,U> accumulator, BinaryOperator<U> combiner)

虽然函数定义越来越长，但语义不曾改变，多的参数只是为了指明初始值（参数identity），或者是指定并行执行时多个部分结果的合并方式（参数combiner）。reduce()最常用的场景就是从一堆值中生成一个值。用这么复杂的函数去求一个最大或最小值，你是不是觉得设计者有病。其实不然，因为“大”和“小”或者“求和”有时会有不同的语义。

需求：从一组单词中找出最长的单词。这里“大”的含义就是“长”。



```
Stream<String> stream = Stream.of("I","love","you","too");
Optional<String> longest = stream.reduce((str1, str2) -> str1.length() > str2.length() ? str1 : str2);
System.out.println(longest.get());

//等同于
Stream<String> stream = Stream.of("I","love","you","too");
Optional<String> longest = stream.max((str1, str2) -> str1.length() - str2.length());
System.out.println(longest.get());
```
上述代码会选出最长的单词love，其中Optional是（一个）值的容器，使用它可以避免null值的麻烦。当然可以使用Stream.max(Comparator<? super T> comparator)方法来达到同等效果，但reduce()自有其存在的理由。

需求：求出一组单词的长度之和。这是个“求和”操作，操作对象输入类型是String，而结果类型是Integer。


```
Stream<String> stream = Stream.of("I","love","you","too");
Integer lengthSum = stream.reduce(0, // 初始值 //(1)
        (sum, str) -> sum + str.length(),  //累加器 //(2)
        (a, b) -> a + b); //部分结果拼接器 //(3)
//stream.mapToInt(str -> str.length()).sum();
System.out.println(lengthSum);
```
上述代码标号(2)处将i. 字符串映射成长度，ii. 并和当前累加和相加。这显然是两步操作，使用reduce()函数将这两步合二为一，更有助于提升性能。如果想要使用map()和sum()组合来达到上述目的，也是可以的。

==reduce()擅长的是生成一个值==，如果想要从Stream生成一个集合或者Map等复杂的对象该怎么办呢？终极武器collect()横空出世！


### 终极武器collect()

不夸张的讲，如果你发现某个功能在Stream接口中没找到，十有八九可以通过collect()方法实现。collect()是Stream接口方法中最灵活的一个，学会它才算真正入门Java函数式编程。先看几个热身的小例子：



```
Stream<String> stream = Stream.of("I","love","you","too");
List<String> list = stream.collect(Collectors.toList());
//Set<String> set = stream.collect(Collectors.toSet());
//Map<String, Integer> map = stream.collect(Collectors.toMap(Function.identity(), String::length));
```

上述代码分别列举了如何将Stream转换成List、Set和Map。虽然代码语义很明确，可是我们仍然会有几个疑问：

- Function.identity()是干什么的？
- String::length是什么意思？
- Collectors是个什么东西？

#### ==接口的静态方法和默认方法==

Function是一个接口，那么Function.identity()是什么意思呢？这要从两方面解释：

1. Java 8允许在接口中加入具体方法。接口中的具体方法有两种，default方法和static方法，identity()就是Function接口的一个静态方法。
2. Function.identity()返回一个输出跟输入一样的Lambda表达式对象，等价于形如t -> t形式的Lambda表达式。

>上面的解释是不是让你疑问更多？不要问我为什么接口中可以有具体方法，也不要告诉我你觉得t -> t比identity()方法更直观。我会告诉你接口中的default方法是一个无奈之举，在Java 7及之前要想在定义好的接口中加入新的抽象方法是很困难甚至不可能的，因为所有实现了该接口的类都要重新实现。试想在Collection接口中加入一个stream()抽象方法会怎样？default方法就是用来解决这个尴尬问题的，直接在接口中实现新加入的方法。既然已经引入了default方法，为何不再加入static方法来避免专门的工具类呢！

#### ==方法引用==

诸如String::length的语法形式叫做方法引用（method references），这种语法用来替代某些特定形式Lambda表达式。如果Lambda表达式的全部内容就是调用一个已有的方法，那么可以用方法引用来替代Lambda表达式。方法引用可以细分为四类：


方法引用类别 | 举例
---|---
引用静态方法 | Integer::sum
引用某个对象的方法 | list::add
引用某个类的方法 |	String::length
引用构造方法 |HashMap::new

#### ==收集器==


收集器（Collector）是为Stream.collect()方法量身打造的工具接口（类）。考虑一下将一个Stream转换成一个容器（或者Map）需要做哪些工作？我们至少需要两样东西：

1. 目标容器是什么？是ArrayList还是HashSet，或者是个TreeMap。
2. 新元素如何添加到容器中？是List.add()还是Map.put()。如果并行的进行规约，还需要告诉collect()
3. 多个部分结果如何合并成一个。

结合以上分析，collect()方法定义为<R> R collect(Supplier<R> supplier, BiConsumer<R,? super T> accumulator, BiConsumer<R,R> combiner)，三个参数依次对应上述三条分析。不过每次调用collect()都要传入这三个参数太麻烦，收集器Collector就是对这三个参数的简单封装,所以collect()的另一定义为<R,A> R collect(Collector<? super T,A,R> collector)。Collectors工具类可通过静态方法生成各种常用的Collector。举例来说，如果要将Stream规约成List可以通过如下两种方式实现：



```
Stream<String> stream = Stream.of("I","love","you","too");
List<String> list = stream.collect(ArrayList::new, ArrayList::add, ArrayList::addAll);
//List<String> list = stream.collect(Collectors.toList());
System.out.println(list);
```

通常情况下我们不需要手动指定collect()的三个参数，而是调用collect(Collector<? super T,A,R> collector)方法，并且参数中的Collector对象大都是直接通过Collectors工具类获得。实际上传入的收集器的行为决定了collect()的行为。


==使用collect()生成Collection==

前面已经提到通过collect()方法将Stream转换成容器的方法，这里再汇总一下。将Stream转换成List或Set是比较常见的操作，所以Collectors工具已经为我们提供了对应的收集器，通过如下代码即可完成：


```
// 将Stream转换成List或Set
Stream<String> stream = Stream.of("I", "love", "you", "too");
List<String> list = stream.collect(Collectors.toList()); // (1)
Set<String> set = stream.collect(Collectors.toSet()); // (2)
```

上述代码能够满足大部分需求，但由于返回结果是接口类型，我们并不知道类库实际选择的容器类型是什么，有时候我们可能会想要人为指定容器的实际类型，这个需求可通过Collectors.toCollection(Supplier<C> collectionFactory)方法完成。


```
List<String> list = stream.collect(Collectors.toCollection(ArrayList::new)); //(3)
Set<String> set = stream.collect(Collectors.toCollection(HashSet::new));  //(4)
```
上述代码(3)处指定规约结果是ArrayList，而(4)处指定规约结果为HashSet。一切如你所愿。

#### ==使用collect()生成Map==

前面已经说过Stream背后依赖于某种数据源，数据源可以是数组、容器等，但不能是Map。反过来从Stream生成Map是可以的，但我们要想清楚Map的key和value分别代表什么，根本原因是我们要想清楚要干什么。通常在三种情况下collect()的结果会是Map：

1. 使用Collectors.toMap()生成的收集器，用户需要指定如何生成Map的key和value。
2. 使用Collectors.partitioningBy()生成的收集器，对元素进行二分区操作时用到。
3. 使用Collectors.groupingBy()生成的收集器，对元素做group操作时用到。


- 情况1：使用toMap()生成的收集器，这种情况是最直接的，前面例子中已提到，这是和Collectors.toCollection()并列的方法。如下代码展示将电脑列表转换成由<电脑，G价格>组成的Map。非常直观，无需多言。

```
public void TestCollectMap() {
    List<ComputerVO> computers = new ArrayList<>();
    computers.add(new ComputerVO("联想笔记本",5500.0));
    computers.add(new ComputerVO("华硕笔记本",3500.0));
    computers.add(new ComputerVO("苹果笔记本",6500.0));
    Map<ComputerVO,Double> map = computers.stream().collect(Collectors.toMap(Function.identity(), computerVO -> getComputerPrice(computerVO)));
}

private Double getComputerPrice(ComputerVO computerVO) {
    return computerVO.getPrice();
}
```

- 情况2：使用partitioningBy()生成的收集器，这种情况适用于将Stream中的元素依据某个二值逻辑（满足条件，或不满足）分成互补相交的两部分，比如男女性别、成绩及格与否等。下列代码展示将电脑分成价格大于等于3500和小于3500的两部分。


```
public void TestcollectMap2() {
    List<ComputerVO> computers = new ArrayList<>();
    computers.add(new ComputerVO("联想笔记本",5500.0));
    computers.add(new ComputerVO("华硕笔记本",3500.0));
    computers.add(new ComputerVO("苹果笔记本",6500.0));
    Map<Boolean,List<ComputerVO>> map = computers.stream().collect(Collectors.partitioningBy(c -> c.getPrice() >= 3500));
}
```

- 情况3：使用groupingBy()生成的收集器，这是比较灵活的一种情况。跟SQL中的group by语句类似，这里的groupingBy()也是按照某个属性对数据进行分组，属性相同的元素会被对应到Map的同一个key上。下列代码展示将电脑按照品牌进行分组：



```
List<ComputerVO> computers = new ArrayList<>();
computers.add(new ComputerVO("联想笔记本",5500.0));
computers.add(new ComputerVO("华硕笔记本",3500.0));
computers.add(new ComputerVO("苹果笔记本",6500.0));
Map<String,List<ComputerVO>> map = computers.stream().collect(Collectors.groupingBy(ComputerVO::getName));
```

以上只是分组的最基本用法，有些时候仅仅分组是不够的。在SQL中使用group by是为了协助其他查询，比如1. 先将电脑按照品牌分组，2. 然后统计每个品牌电脑的个数。Java类库设计者也考虑到了这种情况，增强版的groupingBy()能够满足这种需求。增强版的groupingBy()允许我们对元素分组之后再执行某种运算，比如求和、计数、平均值、类型转换等。这种先将元素分组的收集器叫做==上游收集器==，之后执行其他运算的收集器叫做==下游收集器==(downstream Collector)。



```
List<ComputerVO> computers = new ArrayList<>();
computers.add(new ComputerVO("联想笔记本",5500.0));
computers.add(new ComputerVO("华硕笔记本",3500.0));
computers.add(new ComputerVO("苹果笔记本",6500.0));
Map<String, Long> map = computers.stream().collect(Collectors.groupingBy(ComputerVO::getName, Collectors.counting()));
```

上面代码的逻辑是不是越看越像SQL？高度非结构化。还有更狠的，下游收集器还可以包含更下游的收集器，这绝不是为了炫技而增加的把戏，而是实际场景需要。考虑将员工按照部门分组的场景，如果我们想得到每个员工的名字（字符串），而不是一个个Employee对象，可通过如下方式做到：


```
// 按照部门对员工分布组，并只保留员工的名字
Map<Department, List<String>> byDept = employees.stream()
                .collect(Collectors.groupingBy(Employee::getDepartment,
                        Collectors.mapping(Employee::getName,// 下游收集器
                                Collectors.toList())));// 更下游的收集器
```

#### ==使用collect()做字符串join==

这个肯定是大家喜闻乐见的功能，字符串拼接时使用Collectors.joining()生成的收集器，从此告别for循环。Collectors.joining()方法有三种重写形式，分别对应三种不同的拼接方式。无需多言，代码过目难忘。



```
// 使用Collectors.joining()拼接字符串
Stream<String> stream = Stream.of("I", "love", "you");
//String joined = stream.collect(Collectors.joining());// "Iloveyou"
//String joined = stream.collect(Collectors.joining(","));// "I,love,you"
String joined = stream.collect(Collectors.joining(",", "{", "}"));// "{I,love,you}"
```

#### ==collect()还可以做更多==

除了可以使用Collectors工具类已经封装好的收集器，我们还可以自定义收集器，或者直接调用collect(Supplier<R> supplier, BiConsumer<R,? super T> accumulator, BiConsumer<R,R> combiner)方法，收集任何形式你想要的信息。不过Collectors工具类应该能满足我们的绝大部分需求，手动实现之间请先看看文档。


# 3、深入理解Java





http://www.importnew.com/24235.html

http://www.importnew.com/25315.html

# 参考文档

https://docs.oracle.com/javase/8/docs/api/java/util/stream/package-summary.html#package.description
https://docs.oracle.com/javase/tutorial/java/javaOO/methodreferences.html
https://docs.oracle.com/javase/8/docs/api/java/util/stream/Collector.html
https://docs.oracle.com/javase/8/docs/api/java/util/stream/Stream.html
https://docs.oracle.com/javase/8/docs/api/java/util/stream/Collectors.html

http://www.importnew.com/24237.html
http://www.importnew.com/24245.html
http://www.importnew.com/24235.html
