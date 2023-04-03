# 从 Java 到 Kotlin 并返回(I) —从 Java 调用 Kotlin

> 原文：<https://medium.com/google-developer-experts/from-java-to-kotlin-and-back-i-java-calling-kotlin-9abfc6496b04?source=collection_archive---------0----------------------->

![](img/e2b661caf6c49ecb8e0e782e7fbdcb39.png)

本文是系列文章的一部分。您可以在这里找到本系列的其余文章:

[从 Java 到 Kotlin 并返回(II):从 Java 调用 kot Lin](/google-developer-experts/from-java-to-kotlin-and-back-ii-calling-kotlin-from-java-3bdf72da6e52)

[从 Java 到科特林并返回(三):从科特林调用 Java](https://enriquelopezmanas.medium.com/from-java-to-kotlin-and-back-iii-calling-java-from-kotlin-f33f5c246d69)

我目前正在进行一个多模块项目，该项目结合了各种 Java 和 Kotlin 代码，所以我决定将我的想法和笔记作为一个系列文章发表。这可能对我的日志实践有所帮助，也希望能帮助其他面临同样问题的潜在读者找到一些建议。

Android 开发者普遍意识到 Java 可以相对无缝地与 Kotlin 交互。Kotlin 从一开始就被设计成完全与 Java 互操作，JetBrains 和 Google 都在朝这个方向努力。例如，IntelliJ 和 Android Studio 允许用户将文件从 Java 转换成 Kotlin。这种转换并不总是没有问题的，但它通常做得很好。

![](img/c30a8d7aa65e612cbd2c39d4f96d55b9.png)

Converting from Java to Kotlin in IntelliJ-based IDEs.

当我们处理需要两种语言相互交互的项目时，互用性规则是关键。在 Android 中，这很可能会经常发生。

一个典型的例子是 SDK，它的 API 可能被各种应用程序调用。SDK 开发人员可能希望用 Kotlin 编写库来充分利用这种语言，但最终用户可能会将 SDK 集成到用 Java 编写的应用程序中。

另一个例子是具有不同代码库的单个应用程序。由于 Java 和 Kotlin 可以在同一个代码库上共存，因此 Kotlin 和 Java 代码有可能在一段不确定的时间内共存。鉴于 Android 生态系统中模块化的趋势，这尤其可能，在 Android 生态系统中，模块由多个团队开发，尽管他们正在开发同一产品，但彼此独立操作，一直到语言的选择。

交互可以双向进行:Kotlin 代码调用 Java 代码，Java 代码调用 Kotlin 代码。因此，记住一些规则以使系统在两个方向上正常工作是很重要的。在本系列的第一篇文章中，我将重点介绍一些我们在处理 Kotlin 代码并将其公开给 Java 时(或者当 Java 访问 Kotlin 代码时)需要记住的技巧

## 使用 JvmName 注释

让我们看一下下面的文件，它用 Kotlin 声明了一个类和一个函数:

Kotlin file without JvmName annotation

当从 Java 访问这个文件时(由于 myFunction 是一个顶级函数)，所需的代码必须编写如下:

Accessing code from Java

可以使用包名来引用该类，但是必须使用带有后缀 Kt 的原始文件名来引用该函数(例如，如果该文件名为 file，我们引用的是名为 FileKt 的 Java 类的静态方法，因为 Java 中不存在顶级函数)。

当然，这是不舒服的。通过使用注释 **JvmName** ，我们可以确保我们定义了生成的 Java 文件的名称:

如果在 Kotlin 文件上有一个顶级函数可能会向 Java 用户公开，请确保正确使用了 JvmName。

JvmName 还避免了签名类，这在我们进行[类型擦除](https://en.wikipedia.org/wiki/Type_erasure)时可能会发生。例如，看看下面这段代码:

Kotlin signature collision

在 Java 中，这些函数不能并排定义，因为 JVM 签名是相同的:相同的返回类型，属于 List 类，相同的名称(filter valid(Ljava/util/List；)Ljava/util/List；).手动解决方案是编写不同的名称，但更好的解决方案是使用 JvmName 在编译成 JVM 时更改名称:

Avoiding signature collision with JvmName

## 使用**JVM multipileclass**

我们可能经常面临的另一个问题是用相同的 Java 名称生成多个文件(想想一个类的常用名称，比如 Utils)。编译器不喜欢这样，会抛出一个错误。然而，注解**jvmmmultipileclass**允许编译器将多个类合并成一个同名的类。如果您正在处理一个您认为在生成 Java 文件时会与其他类的名称空间发生冲突的类，那么可以考虑使用**JVM multipileclass。**

First class using the annotation JvmMultifileClass

Another class using the annotation JvmMultifileClass

Both will happily coexist in Java

## 使用 JVMOverloads

Kotlin 包含了一些不错的特性，允许我们编写更符合习惯的代码。例如，我们可以定义默认参数，因此用户不需要指定它们。与参数命名一起，函数名现在更容易阅读，也更不容易出错。例如，让我们检查以下假设的某种自定义 HTTP 服务的构造函数:

Constructor without JvmOverloads

对于构造函数的每个实例化，像名称、主机等字段可能是唯一的，但其他一些可能不是，它们初始化起来可能也很复杂(我们不能总是应用依赖注入来解决这个问题)。通过声明缺省参数，我们只需要指定没有缺省值的属性。

然而，Java 不支持缺省参数，所以与我们的服务交互的 Java 文件需要声明所有的参数。真是浪费时间。

注解 JvmOverloads 在这里起了作用:

Constructor-with-JvmOverloads.kt

JvmOverloads 命令 Kotlin 编译器为此函数生成替换默认参数值的重载。

当然，JvmOverloads 与函数一起工作也很好:

Function with JvmOverloads

如果您的 Kotlin 函数可以在 Java 中使用，并且您有默认的参数，请考虑使用 JvmOverloads。

## 可见性等价

在我的[上一篇文章](/google-developer-experts/considerations-when-creating-android-libraries-c80940d79ae)中，我写了开发库时的注意事项，其中一个话题是 Kotlin 1.4 中的严格库模式。如果默认可见性意味着可见性修饰符将是公共的，则此方法强制可见性修饰符。如果您在用 Kotlin 开发库时激活了这种模式，那么您可能已经准备好了，但是尽管如此，您还是应该意识到 Kotlin 和 Java 之间的可见性等价性。

*   Kotlin 中的`private`成员被编译成 Java 中的`private`成员
*   顶级的`private`声明将被编译成包局部声明
*   `protected`停留在`protected`
*   `internal`声明在 Java 中是`public`。
*   `public`遗体`public`

## 例外

与 Java 不同，Kotlin 没有检查异常。在 JVM 级别没有被检查的异常。让我们检查 Kotlin 中的以下文件:

Exception in Kotlin

在 Java 中，我们将使用类似下面的语句调用:

Exception in Java

这将删除 Java 中的一个编译错误。我们的函数`myFunction()`不会在任何时候声明`IOException`或任何其他类型的异常。如果我们想让它在 Java 中工作，我们需要在 Kotlin 中使用`[@Throws](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-throws/index.html)`注释:

Exception and Throw annotation

额外收获:Kotlin 函数可能抛出任何异常类型，包括独立于`@Throws`注释使用的捕获异常。看看我的火箭科学家兼同事尤金的这篇文章，他探讨了潜在的副作用。

## Kotlin 中的伴随函数

当我们需要编写一个不需要类的实例就可以调用的函数，但仍然可以访问同一个类的内部时，我们在 Kotlin 中编写配套函数。例如，看看下面的类:

如果我们需要在 Java 中使用它，结果如下:

Companion with no annotation in Java

如果我们使用`@JvmStatic`注释，它将作为静态方法公开。

以及由此产生的 Java 代码:

Kotlin companion with annotation

如果您正在使用伴随函数，记得用`@JvmStatic`注释标记它们。

## 伴随常数

与上一节相关，在伴随常量的情况下最好使用注释`@JvmField`，因为`@JvmStatic`创建了一个奇怪的 getter。例如，考虑下面的伴随常数:

Companion constant

用`@JvmField`注释伴随值将再次产生更加全面的 Java 代码:

Companion constant in Java

如果您想知道如果我们使用`@JvmStatic`注释会是什么样子，这是为 Java 生成的访问:

Companion constant using JvmStatic annotation

## 摘要

下面的文章从 Kotlin 的角度介绍了 Java 和 Kotlin 之间的互操作性的一些技巧(例如，Kotlin 代码必须如何准备自己，以便促进对 Java 方面的有效访问。在接下来的文章中，我们将继续探索我们需要对 Kotlin 代码做些什么。

感谢[尤金·佩特连科](https://twitter.com/jonnyzzz?lang=en)和[尼克·斯凯尔顿](https://twitter.com/nshred)给我们的反馈。

我在我的 [Twitter 账户](https://twitter.com/eenriquelopez)上写下我对软件工程和生活的想法。如果你喜欢这篇文章或者它对你有帮助，请随意分享，👏和/或发表评论。这是给业余作家加油的货币。