# 如何使用 Java 流，解释

> 原文：<https://medium.com/capital-one-tech/how-to-use-java-streams-explained-a2836e3a75f2?source=collection_archive---------1----------------------->

## 让流、中间操作和终端操作在 Java 中工作

![](img/aa44f97f90a16222592c3ef6e9f6280e.png)

流是 Java 集成函数式编程和面向对象风格的方式。在 Java 中使用流有很多好处，比如能够在更抽象的层次上编写函数，这可以减少代码错误，将函数压缩到更少更易读的代码行中，以及它们为并行化提供的便利。Java 流是众所周知的，但是并不是每个人都知道如何充分利用它们的优点，包括创建流、中间操作和终端操作的优点。在这篇博客中，我们将使用一个简单的例子来解释 Java 流是如何工作的，这将导致更少的冗长、更直观和更少的易错代码。

# Java 流如何工作的一个例子

考虑 Java 流最简单的方式，也是对我帮助最大的方式，是想象一个彼此不相连的对象列表，一次一个地进入管道。您可以控制进入管道的对象数量、在管道内对这些对象做什么，以及在这些对象退出管道时如何捕捉它们。

先看大图，然后再分解，学习 Streams 要容易得多。为此，让我们编写一个程序，取一定数量的 4 的倍数，对每个倍数求平方，然后取所有不能被 10 整除的平方的和:

```
protected static int example1(int numberOfMultiples){
    return Stream.*iterate*(4, multipleOfFour -> multipleOfFour + 4)
            .limit(numberOfMultiples)
            .map(multipleOfFour -> multipleOfFour * multipleOfFour)
            .filter(multipleOfFourSquared -> multipleOfFourSquared % 10 == 0)
            .reduce(0, Integer::*sum*);
}
```

即使你不知道流在 Java 中是如何工作的，你也很有可能能够很快弄清楚上面的代码在做什么。除了第一行 *(* `*Stream.iterate(4, multipleOfFour -> multipleOfFour + 4)*` *)* ，其余代码读起来几乎就像一组指令。

让我们一次一件来。

# λ表达式

```
multipleOfFour -> multipleOfFour + 4
```

这就是 Java 中定义 [Lambda 表达式的方式。Lambda 表达式提供了一种更加紧凑和简化的方法来定义函数。本质上，你可以想象左边的 multipleOfFour 是输入，- >是 Lambda，右边的`multipleOfFour + 4`是返回值。想象一下这个读数为“给我一个输入(multipleOfFour)，我将返回给你那个输入+ 4。”注意，如果 Java 中的一个 Lambda 表达式需要多行，那么需要用花括号“`{}`”将右边(Lambda - >之后)括起来，并添加一个 return 语句。](https://www.w3schools.com/java/java_lambda.asp)

# 流创建

```
Stream.*iterate*(4, multipleOfFour -> multipleOfFour + 4)
```

`[Stream.iterate()](https://mkyong.com/java8/java-8-stream-iterate-examples/)`是一个函数，它通过获取一个种子(起点)和一个一元运算符(可以为其传入一个 Lambda 表达式)来创建一个无限流。从 4 开始，这个特定的迭代将返回一个流:4，8，12，16，20，24，28，…

# 限制

```
.limit(numberOfMultiples)
```

这是流管道的下一部分，它[将](https://howtodoinjava.com/java8/java-stream-limit-method-example/)前一个流限制为限制中指定的数量(在本例中为`numberOfMultiples`)。因此，我们的流不再是无限流，现在只包含第一个`numberOfMultiples`，在这种情况下，它是 4 的倍数。

# 高阶函数

[高阶函数](https://en.wikipedia.org/wiki/Higher-order_function)是以函数为自变量，或者作用于给定函数或者返回函数的函数。当使用 Java 的函数接口时，高阶函数非常有用，因为它们允许开发者达到更高的抽象层次。

# 地图

```
.map(multipleOfFour -> multipleOfFour * multipleOfFour)
```

`[Map](https://en.wikipedia.org/wiki/Map_(higher-order_function))` [是高阶函数](https://en.wikipedia.org/wiki/Map_(higher-order_function))。它接受一个 Lambda 表达式，转换它所作用的流中的每个值，并将其写入 Lambda 中指定的不同流中。在这种情况下，它取原始流中的每一个 4 的倍数，并创建一个包含所有这些 4 的平方的倍数的新流。虽然在这种情况下，我们要去`Stream<Integer> -> Stream<Integer>`，你可以使用地图完全转换你的流到一个不同类型的流！

例如，如果我运行:

```
.map(multipleOfFour -> String.*valueOf*(multipleOfFour * multipleOfFour))
```

它会转换我的`*Stream<Integer> -> Stream<String>*`。这是非常强大的，如果你要实现像这种面向对象的风格，代码将不会直接和优雅。您还需要不必要地创建有限范围的变量和对象。这是面向对象的版本:

```
List<String> multiplesOfFourStrings = new ArrayList<>();
for (Integer multipleOfFour : multiplesOfFour){
    multiplesOfFourStrings.add(String.*valueOf*(multipleOfFour * multipleOfFour));
}
```

# 过滤器

```
.filter(multipleOfFourSquared -> multipleOfFourSquared % 10 == 0)
```

与 map 类似，`[filter](https://en.wikipedia.org/wiki/Filter_(higher-order_function))` [是另一个高阶函数](https://en.wikipedia.org/wiki/Filter_(higher-order_function))。Filter [接受一个谓词](https://www.baeldung.com/java-predicate-chain)，这是 lambda 表达式的一种表达方式，它在某些条件下返回 true，在其他条件下返回 false。它过滤流，这样流中只剩下那些导致谓词返回 true 的对象。我阅读这样一个过滤器的方式是*“过滤我的流中 4 的平方的倍数，这样只有那些 4 的平方的倍数% 10 == 0 的对象。”*

# 减少

```
.reduce(0, Integer::*sum*);
```

到目前为止，map 和 filter 是我们称之为[的中间操作](https://dzone.com/articles/become-a-master-of-java-streams-part-2-intermediat)——它们接受一个流并返回一个新的流。`[Reduce](http://zetcode.com/java/streamreduce/#:~:text=Java%20Stream%20reduction,the%20elements%20of%20a%20stream.)` [是一个终端操作](http://zetcode.com/java/streamreduce/#:~:text=Java%20Stream%20reduction,the%20elements%20of%20a%20stream.)的例子，顾名思义，它终止流并返回其他东西，不管是集合还是值。Reduce 有两个参数:一个 identity(通常称为“accumulator”)和一个 BinaryOperator，用于简化为一个值。理解这个特定 reduce 函数的方法是*“从数字 0 开始，对流中的所有整数求和，并返回这个和。”*

# 创建 Java 流的示例

直接从系列制作流:

```
List<Integer> arrayList = new ArrayList<>(Arrays.*asList*(1,2,3));
Stream<Integer> intStream = arrayList.stream();Map<Integer, Integer> map = new HashMap<Integer,Integer>(){{
    put(1,1);
    put(2,2);
    put(3,3);
}};
Stream<Map.Entry<Integer,Integer>> mapStream = map.entrySet().stream();
```

# Java 流中的中间操作示例

`.sorted()`:用给定的比较器对你的流进行排序:

```
List<int[]> list = new ArrayList<int[]>(){{
    add(new int[] {1,20});
    add(new int[] {5,15});
    add(new int[] {7,18});
    add(new int[] {3,25});
}};
list = list.stream()
        .sorted(Comparator.*comparingInt*(i -> i[1]))
        .collect(Collectors.*toList*());
```

`*Comparator.comparingInt()*`在给定函数的情况下比较流中的项目，该函数比较这些项目的整数部分——这与您使用`*.sorted((i,j) -> Integer.compare(i[1], j[1]))*`的效果相同。这个函数按照两个数数组中的第二个值对创建的数组流进行排序。至于 collect，这将在下面的其他终端操作示例中介绍。

# Java 流中终端操作的两个例子

**终端操作示例 1** `.collect()`:将你的流中的项目收集到某个集合中:

```
List<int[]> list = new ArrayList<int[]>(){{
    add(new int[] {1,20});
    add(new int[] {5,15});
    add(new int[] {7,18});
    add(new int[] {3,25});
}};
list = list.stream()
        .sorted(Comparator.*comparingInt*(i -> i[1]))
        .collect(Collectors.*toList*());
```

在前一个例子的基础上，`*collect()*`是 Java 流中另一个非常重要的终端操作。根据您指定的收集器，流中的项目将被收集到该集合中。上面的例子将流中的项目收集到一个列表中。运行之后，list 现在包含了我们添加的数组列表，按照每个数组中的第二个项目进行升序排序。还可以通过指定集合工厂，将流中的项目收集到映射、集合、并发映射或任何其他集合中。

这里还有一个例子，我们以前在同一个列表上使用 collect，但是这次我们将整数数组列表收集到一个映射中，其中每个数组中的第一项是键，第二项是每个`<key,value>` 对的值！

```
List<int[]> list = new ArrayList<int[]>(){{
    add(new int[] {1,20});
    add(new int[] {5,15});
    add(new int[] {7,18});
    add(new int[] {3,25});
}};
Map<Integer, Integer> map = list.stream()
        .collect(Collectors.*toMap*(i -> i[0], i -> i[1]));
```

# 终端操作示例 2

**终端操作示例 2** `.forEach()`:对您的流中的每个项目执行一个操作

```
List<int[]> list = new ArrayList<int[]>(){{
    add(new int[] {1,20});
    add(new int[] {5,15});
    add(new int[] {7,18});
    add(new int[] {3,25});
}};
list.stream().forEach(i -> i[0] += 10);
```

是的，流也有一个`forEach()`实现，而且是一个终端操作！与 java 中典型的 for-each 循环一样，For each 将遍历流并对每个项目执行一个操作。重要的是要注意有一个`collection.stream.forEach()`实现和一个`collection.forEach()` 实现。它们非常相似，区别在于流中每个项目的处理顺序不是在流版本中定义的，而是在集合版本中定义的，因为它是为特定集合实现的。

# 那么我为什么要使用 Java 流呢？

现在我们已经很好地理解了流是如何工作的，让我们回到最初的例子。除了这一次，让我们将它与我们在不使用 Java 流的情况下实现相同功能所编写的代码进行比较。

# 使用 Java 流的代码:

```
protected static int example1(int numberOfMultiples){
    return Stream.*iterate*(4, multipleOfFour -> multipleOfFour + 4)
            .limit(numberOfMultiples)
            .map(multipleOfFour -> multipleOfFour * multipleOfFour)
            .filter(multipleOfFourSquared -> multipleOfFourSquared % 10 == 0)
            .reduce(0, Integer::*sum*);
}
```

# 不使用 Java 流的代码:

```
protected static int example2(int numberOfMultiples){
    int sum = 0;
    for (int multipleOfFour = 4; multipleOfFour <= numberOfMultiples * 4; multipleOfFour += 4){
        int multipleOfFourSquared = multipleOfFour * multipleOfFour;
        if (multipleOfFourSquared % 10 == 0){
            sum += multipleOfFourSquared;
        }
    }
    return sum;
}
```

这只是一个很小的例子，但是它已经显示了流的许多优点。这包括不太冗长的代码、更直观的代码和不太容易出错的代码。

# Java 流的优势——代码更少

对于这个例子来说，使用 streams 减少了两行代码。根据我的经验，streams 能够将一个 15–20 行的函数压缩成一个易于理解的 3–4 行代码并不罕见。

你还会注意到，由于我们使用的所有 lambda 表达式，我们没有创建只存在于函数范围内的不必要的新变量。为了使没有流的代码可读，我们必须为整个 For 循环的运行总和创建一个新的整数，在 for 循环的声明中为 4 的倍数创建一个新的整数，并为 4 的平方的倍数创建一个整数。

# Java 流的优势——更直观的代码

使用流的另一个好处是代码最终更直观，需要更少的思考来理解正在发生的事情。您可以注意到，使用 streams 的代码看起来就像是计算过程中的一系列步骤。在没有流的示例中，您需要考虑 for 循环声明中的变量是什么，为什么我们要将它递增 4，然后有时在递增之间，我们检查它的平方是否能被 10 整除，如果能，我们追加总和。我们开始将不同的功能混合在一起，而不是一步一步地进行计算，随着您使用的功能变得越来越复杂，这变得越来越难以推理。

# Java 流的优势——代码更少出错

这方面的一个例子可以在<= in the for-loop in the second example. It is very common to have for-loops that are exclusive in their comparison check, so a developer working on a function like this has a real chance of setting that comparison as

# Wrapping It Up

To recap, in this article we went over:

*   Streams
*   Lambda Expressions
*   Stream Creation
*   Map, Filter, Reduce, and other examples of intermediate and terminal operations
*   Some of the pros of using streams

Another important characteristic of streams is that intermediate operations are lazy, which means that a processing based on an intermediate operation will only be done if it is needed, which can be a significant optimization with streams. [中看到，这篇文章有一个很棒的部分是关于 java 流中惰性求值是如何工作的](https://stackify.com/streams-guide-java-8/)。

如果您有兴趣深入研究，一个很好的起点是[学习更多的中级和终端操作](https://javaconceptoftheday.com/java-8-stream-intermediate-and-terminal-operations/)，并练习使用 streams！如果你也想学习并行化，[肯尼斯·寇森的这个演讲](https://www.youtube.com/watch?v=x5akmCWgGY0)是关于并行流和可完成未来如何工作的一个很好且容易理解的解释。

使用 Java 中的流，您可以编写更优雅、无错误的 Java 代码，随着您使用流的技能的提高，您会发现某些操作变得更容易实现！

*披露声明:2021 首创一号。观点是作者个人的观点。除非本帖中另有说明，否则 Capital One 不隶属于所提及的任何公司，也不被这些公司认可。使用或展示的所有商标和其他知识产权是其各自所有者的财产。*

*原载于*[*https://www.capitalone.com*](https://www.capitalone.com/tech/software-engineering/java-streams-explained-simple-example/)*。*