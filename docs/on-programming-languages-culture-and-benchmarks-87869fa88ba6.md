# 关于编程语言、文化和基准

> 原文：<https://medium.com/capital-one-tech/on-programming-languages-culture-and-benchmarks-87869fa88ba6?source=collection_archive---------2----------------------->

## *4 Go 和 Java 的比较*

![](img/96866eff865056643dbe1e9d18cf592b.png)

非程序员通常会对编程感到惊讶的一点是，不同的语言有不同文化的不同社区。这些文化决定了大的(人们如何决定语言中增加什么新特性)和小的(制表符和空格)。它们也会以有趣的方式出现。

最近，我被拉进了一场关于 Java 中的反射成本与 Go 中的成本的讨论。我不知道答案，所以我写了一些基准测试，看看有什么不同。最有趣的部分不是结果，而是围绕基准测试的设计哲学，以及它揭示了各自语言的文化。这是我观察到的。

## ***1。基准测试是 Go 的核心部分，是 Java 的可选部分***

Go 的基准测试支持被集成到内置于标准库中的测试包中。关于编写和运行基准的文档作为标准文档的[部分包含在内。基准测试是作为正在进行基准测试的项目的一部分。](https://golang.org/pkg/testing/)

在 Go 中，使用命令`go test -bench=.`运行基准测试。`.`是一个正则表达式，表示您想要运行的基准函数的名称；圆点的意思是运行一切。还有一些额外的标志控制基准测试的其他方面，比如除了性能之外是否还要进行内存基准测试，或者运行基准测试多长时间。正如我们稍后将讨论的，与标准发行版的集成还有其他含义。

Java 的方法不同。标准的 Java 基准库是 [JMH](https://openjdk.java.net/projects/code-tools/jmh/) 。尽管它是由 Oracle 编写和维护的，但它没有与标准库捆绑在一起。使用 JMH 的推荐方法是创建一个单独的基准测试项目。然后，开发人员使用 maven(流行的第三方项目管理工具)来运行这个不那么简单的命令:

```
mvn archetype:generate -DinteractiveMode=false -DarchetypeGroupId=org.openjdk.jmh -DarchetypeArtifactId=jmh-java-benchmark-archetype -DgroupId=org.sample -DartifactId=test -Dversion=1.0
```

当需要运行您的 JMH 基准时，标准的方法是使用命令`mvn clean install; java -jar target/benchmarks.jar`。这将在您的基准项目中构建并运行基准。有很多选择。JMH 代码运行程序有超过 30 个命令行标志。如果这还不够，您可以编写自己的代码运行器并直接配置选项:

```
**public static void** main(String[] args) **throws** RunnerException {
    Options opt = **new** OptionsBuilder()
            .include(MyBenchmark.**class**.getSimpleName())
            .forks(1)
            .timeUnit(TimeUnit.NANOSECONDS)
            .mode(Mode.AverageTime)
            .build();
    **new** Runner(opt).run();
}
```

在 Go 中包含一个标准的基准测试运行器的一个效果是，我在 Go 中看到的基准测试的例子比在 Java 中看到的要多得多。

## **②*。Java 和 Go*** 之间声明基准惊人的相似

要在 Go 中创建一个基准，您需要在项目的测试文件中添加一个新函数。测试文件只是文件名以`_test.go`结尾的文件。每个基准函数的名字都以单词`Benchmark`开头，并接受一个类型为`*testing.B`的参数。这遵循了 Go 中的测试模式，该模式使用一个以单词`Test`开头的函数，并接受一个类型为`*testing.T`的参数。有趣的是，按函数名配置比通常的 Go 风格更“神奇”。作为一般的设计规则，Go 更喜欢显式调用而不是隐式调用。但是在测试和基准测试的情况下，Go 依赖于一个测试运行器，它寻找具有特定名称结构的函数，以知道它们应该被调用。这种风格在围棋中很突出，因为它太不常见了。

用 JMH 创建基准类似于围棋中的过程。您创建一个新的类来保存基准，然后用`@Benchmark`注释基准方法。由于基准测试与被测代码在不同的项目中，所以使用 maven 将代码作为库来引用。这是 Java 的一种常见模式；注释用于标记预期以特殊方式运行的方法，程序中有一部分的工作是扫描类路径并找到用注释标记的方法，以便可以执行它们。

## ***3。与 Java 相比，在 Go 中编写基准对开发人员的要求更高，但是给了他们更多的时间控制***

用 Go 编写基准比用 Java 编写要复杂一些。基准测试需要多次运行才能获得准确的测量结果。在 Go 中，您需要使用基准运行时提供的值为基准运行显式地设置循环。我还必须编写自己的`blackhole`函数来吃掉输出，这样它就不会被编译器优化掉。如果您想要在测试运行之前设置一些数据，或者如果您想要从计时中排除一些逻辑，您可以显式地停止、启动和重置计时器:

```
func BenchmarkNormalSetPointer(b *testing.B) {
        d := &Data{A: 10, B: “Hello”}
        b.ResetTimer()
        for i := 0; i < b.N; i++ {
                normalSetPointer(d)
        }
}func normalSetPointer(d *Data) {
        d.A = 20
        blackhole(d)
}
```

Java 的基准测试只需要实际的业务逻辑。循环已经替你完成了，JMH 提供了一个黑洞工具类来吞噬输出以防止优化掉它:

```
@Benchmark
public void normalSetPointer(Data data, Blackhole blackhole) {
    data.a = 20;
    blackhole.consume(data);
}
```

为了设置基准测试的数据，并从测量中排除设置时间，JMH 要求您创建一个静态内部类，并将其注释为“State”:

```
@State(Scope.Thread)
public static class Data {
    public int a = 1;
    public String b = “hello”; public String getB() {
        return b;
    }
}
```

当使用 JMH 时，我找不到一种方法来排除基准测试中的部分时间或者重置计时。

## ***4。Go 的基准配置有限，集成性好。Java 的则相反。***

Go 的基准测试不太容易配置。您可以指定基准运行特定次数、最短持续时间或特定数量的 CPU 核心。当您运行基准测试时，输出会以对基准测试工具有意义的单位写入控制台:

```
BenchmarkDoNothing-8          2000000000 0.29 ns/op
BenchmarkReflectInstantiate-8   20000000 110 ns/op
BenchmarkNormalInstantiate-8  2000000000 0.29 ns/op
BenchmarkReflectGet-8           10000000 156 ns/op
```

您还可以在 JSON 中获得结果:

```
{"Time":"2018–06–29T12:11:39.731321926–04:00","Action":"output","Package":"github.com/jonbodner/reflect-cost","Output":"BenchmarkDoNothing-8 \t"}
{"Time":"2018–06–29T12:11:40.355509283–04:00","Action":"output","Package":"github.com/jonbodner/reflect-cost","Output":"2000000000\t 0.30 ns/op\n"}
{"Time":"2018–06–29T12:11:40.355845048–04:00","Action":"output","Package":"github.com/jonbodner/reflect-cost","Output":"BenchmarkReflectInstantiate-8 \t"}
{"Time":"2018–06–29T12:11:42.667043237–04:00","Action":"output","Package":"github.com/jonbodner/reflect-cost","Output":"20000000\t 109 ns/op\n"}
```

不幸的是，JSON 输出不是很有用。首先，虽然每一行都是有效的 JSON，但是所有的行都没有包装数组或对象；你必须自己建造一个。您可能希望每个基准都会生成一个 JSON 记录，其中有单独的字段，分别表示基准的名称、获得稳定答案所花费的迭代次数、花费的时间以及单位。相反，记录有一个“输出”字段，这需要您合并连续记录的值来重建文本输出，然后需要在制表符和空格上拆分文本输出以找到所需的值。考虑到这些限制，放弃 JSON、将文本输出定向到一个文件并解析会更容易。

围棋基准并不局限于计时信息。它们与 Go 内置的代码覆盖率和分析支持相集成，为您提供显示内存分配信息的选项，并允许您将计时和内存信息写入分析文件，这些文件可以通过 Go 附带的 pprof 工具运行。

JMH 非常容易配置。您可以选择时间单位(纳秒、毫秒等。)，无论您想要吞吐量(ops/time)、平均时间(time/op)、采样时间还是单次运行时间。您可以获得文本、CSV、SCSV、JSON 或 LaTeX 格式的输出。您可以让它输出一些内存或线程分析结果。然而，我不知道有什么方法可以在另一个工具中使用这个输出。如果你想获得更详细的信息，你需要升级到别的东西。

## 编程语言文化很重要

作为一个花了几十年时间编写 Java，又花了几年时间编写 Go 的人，我发现这种比较非常有趣。最近，比起写 Java，我更喜欢写 Go。我认为 Go 的文化更好地反映了我喜欢如何编写软件，基准测试是 Go 的方法与我的想法相吻合的另一个领域。Go 在其标准库和工具中采用了“包含电池”的方法；作为标准发行版的一部分，你得到了很多，但这也意味着接受维护 Go 的团队所做的选择。通过将简单的基准测试支持作为标准库和工具的一部分，并将其与捆绑在 Go 开发工具中的分析工具包集成，您可以获得一个针对大多数常见情况的“足够好”的解决方案。但是它需要你做一些额外的工作(编写你自己的基准测试循环和黑洞函数)，并且不做 Go 团队认为不重要的事情(比如可用的 JSON 输出)。

如果你认同 Java 的设计哲学和 Java 开发的文化，那么 Java 的方法没有错。虽然 JDK 中不包括基准测试支持，但 Java 捆绑了一些分析工具，如 jhat、jstat 和 hprof。不幸的是，[它们要么被认为是实验性的，要么产生糟糕的结果](http://www.brendangregg.com/blog/2014-06-09/java-cpu-sampling-using-hprof.html)。其他工具，如 JVisualVM 和 Java Mission Control，[已经开源，未来的发展还不确定](https://www.infoq.com/news/2018/06/open-source-jmc)。最终结果是 Java 依赖第三方来提供其开发人员工具的大部分。这鼓励了一个强大的第三方生态系统，但如果你不知道从哪里开始，这种理念会让你更难开始。此外，有时很难让工具协同工作。Java 中的库往往有很多配置选择，因为 Java 生态系统关注的是可配置性。要理解 Java 和 Go 在可配置性上的不同态度，可能没有比看看它们的垃圾收集器更好的方法了。您可以设置 50 多个不同的标志来配置 JVM 中包含的多个垃圾收集器的行为。Go 只有一个垃圾收集器，并且该收集器只有一个配置标志。

在这两种情况下，这些选择都不是语言所固有的；它们完全是文化的产物。这就是语言战争有点愚蠢的原因。您更喜欢使用哪种语言更多地取决于最适合您的编程风格的文化，而不是语言提供的实际功能。也是曝光度的问题；如果你不尝试其他语言，你永远不会知道是否有更好的文化适合你。不要贬低其他语言；给他们一个尝试，看看他们如何为你工作。你可能会对自己的结局感到惊讶。

*声明:这些观点仅代表作者个人观点。除非本帖中另有说明，否则 Capital One 不属于所提及的任何公司，也不被其认可。使用或展示的所有商标和其他知识产权都是其各自所有者的所有权。本文为 2018 首都一。*