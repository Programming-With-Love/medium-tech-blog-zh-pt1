# 通过 R8 使用 Kotlin 反射缩小 Kotlin 库和应用程序

> 原文：<https://medium.com/androiddevelopers/shrinking-kotlin-libraries-and-applications-using-kotlin-reflection-with-r8-6fe0a0e2d115?source=collection_archive---------1----------------------->

![](img/3c5d45b6e4e317f327273d7b5a72936a.png)

*莫滕·克罗-叶斯柏森和小麦·阿格合著*

[R8](https://r8.googlesource.com/r8) 是安卓系统默认的应用收缩器。R8 通过删除未使用的代码和优化剩余的代码来减少 Android 应用程序的大小。R8 也支持缩小 Android 库。除了产生更小的库，库收缩还可以用来隐藏库的新特性，直到你准备好发布它们或者公开谈论它们。

Kotlin 是编写 Android 应用程序和库的绝佳语言。然而，缩小使用 Kotlin 反射的 Kotlin 库或应用程序会带来一些挑战。Kotlin 在 Java 类文件中使用[元数据来标识 Kotlin 语言结构。如果您的应用程序收缩器不维护和更新 Kotlin 元数据，您的库或应用程序将无法工作。](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-metadata/)

R8 现在支持维护和重写 Kotlin 元数据，以完全支持使用 Kotlin 反射的 Kotlin 库和应用程序的收缩。Android Gradle 插件版本 4.1.0-beta03 中提供了该支持。请尝试一下，让我们知道它是如何为您工作的，并将您遇到的任何问题记录在[我们的公共错误跟踪器](https://issuetracker.google.com/issues/new?component=326788&template=1025938)中。

这篇博文的其余部分提供了关于 Kotlin 元数据和 R8 对 Kotlin 元数据重写的支持的信息。

# 科特林元数据

[Kotlin 元数据](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-metadata/)是存储在由 Kotlin JVM 编译器生成的 Java 类文件的注释中的额外信息。该元数据指定了类文件中给定的类或方法对应于哪种 Kotlin 语言结构。例如，Kotlin 元数据允许 Kotlin 编译器识别类文件中的方法实际上是一个 [Kotlin 扩展函数](https://kotlinlang.org/docs/reference/extensions.html)。

让我们看一个简单的例子。下面的库代码定义了一个假想的基本命令生成器，用于构建编译器命令。

然后我们可以在`CommandBuilderBase`之上定义一个具体的、假设的`D8CommandBuilder`来构建一个简化的 D8 命令。

该示例使用扩展函数，以确保如果您在`D8CommandBuilder`上调用`setMinApi` 方法，返回的对象类型将是`D8CommandBuilder`而不是`CommandBuilderBase`。这些扩展函数是顶级的，并且它们以文件类`CommandBuilderKt`为例。让我们看看使用(简化的)`javap`输出的那个类文件。

```
$ javap com/example/mylibrary/CommandBuilderKt.class
Compiled from "CommandBuilder.kt"
public final class CommandBuilderKt {
public static final <T extends CommandBuilderBase> T addInput(T,      String);
public static final <T extends CommandBuilderBase> T setMinApi(T, int);
...
}
```

`javap`输出显示扩展函数被编译成静态方法，这些静态方法采用额外的第一个参数，即扩展接收器。这些信息不足以让 Kotlin 编译器理解这些方法可以从 Kotlin 代码中用作扩展函数。因此，Kotlin 编译器也放了一个 [kotlin。类文件中的元数据标注](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-metadata/)。该注释包含元数据，其中包含该类的额外的 Kotlin 特定信息。如果我们使用带有`javap`的详细标志，注释就会出现。

```
$ javap -v com/example/mylibrary/CommandBuilderKt.class
...
RuntimeVisibleAnnotations:
  0: kotlin/Metadata(
   mv=[...],
   bv=[...],
   k=...,
   xi=...,
   d1=["^@.\n^B^H^B\n^B^X^B\n^@\n^B^P^N\n^B...^D"],
   d2=["setMinApi", ...])
```

元数据注释的 d1 字段包含协议缓冲区消息形式的大部分实际元数据。元数据的确切内容并不重要。重要的是，Kotlin 编译器读取这些元数据，并使用它来判断这些方法是扩展函数，如下面的`kotlinp`转储所示。

```
$ kotlinp com/example/mylibrary/CommandBuilderKt.class
package {// signature:   addInput(CommandBuilderBase,String)CommandBuilderBase
public final fun <T : CommandBuilderBase> T.addInput(input: kotlin/String): T// signature: setMinApi(CommandBuilderBase,I)CommandBuilderBase
public final fun <T : CommandBuilderBase> T.setMinApi(api: kotlin/Int): T...
}
```

该元数据允许在 Kotlin 用户代码中将这些函数用作 Kotlin 扩展函数:

# R8 过去是如何破坏科特林图书馆的

如前一节所示，库类文件的 Kotlin 元数据对于能够使用库的 Kotlin API 非常重要。然而，元数据在注释中，并被编码为协议缓冲区消息，R8 过去对此一无所知。因此，R8 过去常做两件事之一:

1.  扔掉元数据。
2.  保留原始元数据。

这两个方案都很糟糕。

如果元数据被丢弃，Kotlin 编译器将不再理解扩展函数是扩展函数。因此，对于我们的示例，当编译诸如`D8CommandBuilder().setMinApi(12)`这样的代码时，编译器将产生一个错误，指出不存在这样的方法。这很有意义，因为没有元数据，Kotlin 编译器只能看到一个带有两个参数的 Java 静态方法。

保留原始元数据也不好。Kotlin 元数据中记录的事情之一是类的超类型。所以，假设我们只希望`D8CommandBuilder`类在收缩库时保留它们的名字。这意味着`CommandBuilderBase`将被重命名，很可能是 a。如果我们保留原始的 Kotlin 元数据，Kotlin 编译器将寻找 Kotlin 元数据中记录的`D8CommandBuilder`的超类型。如果我们使用原始元数据，记录的超类型是`CommandBuilderBase`而不是`a.`，那么编译将会失败，并显示一个错误，指出超类型`CommandBuilderBase`不存在。

# R8·科特林元数据重写

为了解决这些问题，R8 扩展了维护和重写 Kotlin 元数据的能力。这是通过嵌入由 R8 JetBrains 开发的 [Kotlin 元数据库](https://github.com/JetBrains/kotlin/blob/master/libraries/kotlinx-metadata/jvm/ReadMe.md)来实现的。元数据库用于读取原始输入中的 Kotlin 元数据。元数据信息记录在 R8 的内部数据结构中。当 R8 完成对库或应用程序的优化和缩减后，它会为显式保留的所有 Kotlin 类合成新的正确的 Kotlin 元数据。

让我们看看我们的例子是什么样子的。我们将示例代码放入 Android Studio 库项目中。我们通过像往常一样在 gradle 构建文件中将`minifyEnabled`设置为 true [来启用收缩。我们更新收缩器配置以包含以下内容。](https://developer.android.com/studio/build/shrink-code#enable)

这告诉 R8 保持`D8CommandBuilder`和`CommandBuilderKt`上的所有扩展功能。它还告诉 R8 保留注释，特别是保留`kotlin.Metadata`注释。这些规则将只在显式保留的类上保留 Kotlin 元数据。因此，这将保持`D8CommandBuilder`和`CommandBuilderKt`上的元数据，但**不会保持`CommandBuilderBase`上的**。我们这样做是为了确保您不会在应用程序和库中传送大量无用的元数据。

现在，在启用收缩的情况下构建库会产生一个库，其中`CommandBuilderBase`已被重命名为`a`。此外，所有保留的类的 Kotlin 元数据已经被重写，因此任何对`CommandBuilderBase`的引用现在都是指`a`。这使得图书馆如预期的那样作为科特林图书馆工作。

最后一点，不在`CommandBuilderBase`上保存 Kotlin 元数据意味着 Kotlin 编译器会将输出中的结果类视为 Java 类。这可能会导致您的库中出现奇怪的 Kotlin 类的 Java 实现细节。为了避免这种情况，可以保留类。如果我们这样做，元数据将被保留。我们可以在 keep 规则上使用`allowobfuscation`修饰符来允许 R8 重命名该类，同时也为其生成 Kotlin 元数据，这样 Kotlin 编译器和 Android Studio 就会将该类视为 Kotlin 类。

到目前为止，我们一直在谈论库收缩以及 Kotlin 库如何需要 Kotlin 元数据。通过 kotlin-reflect 库使用 Kotlin 反射的应用程序也需要 Kotlin 元数据。这些问题与图书馆的问题完全一样。如果 Kotlin 元数据被删除或没有正确更新，kotlin-reflect 库将无法将代码理解为 kotlin 代码。

举个简单的例子，假设我们想在运行时查找并调用一个类的扩展函数。我们希望允许方法被重命名，因为我们不关心名称，我们只想找到它并在运行时调用它。

在我们的代码中，我们添加了一个调用:`reflect(ReflectOnMe())`。这将找到在`ReflectOnMe`上定义的扩展函数，并使用给定的`ReflectOnMe`实例作为接收者和字符串`“reflection”`作为扩展接收者来调用它。

现在，R8 正确地重写了所有保留类的 Kotlin 元数据，我们可以使用下面的 shrinker 配置来缩小应用程序。

这允许`ReflectOnMe`和`extension`都被重命名，它维护并重写 Kotlin 元数据，应用程序正常工作。

# 试试看！

请在 Kotlin 库项目以及使用 Kotlin 反射的 Kotlin 项目上尝试 R8 对 Kotlin 元数据重写的支持。从版本 4.1.0-beta03 开始，Android Gradle 插件中提供了这种支持。如果您在使用它时遇到任何问题，请在[我们的公共错误跟踪器](https://issuetracker.google.com/issues/new?component=326788&template=1025938)中提交错误报告。

快乐缩水！