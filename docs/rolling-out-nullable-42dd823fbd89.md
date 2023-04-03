# 转出@Nullable

> 原文：<https://medium.com/square-corner-blog/rolling-out-nullable-42dd823fbd89?source=collection_archive---------1----------------------->

> 注意，我们已经行动了！如果您想继续了解 Square 的最新技术内容，请访问我们的新家[https://developer.squareup.com/blog](https://developer.squareup.com/blog)

我们很多人对 null 有强烈的看法。我不认为这是邪恶的，或者说使用 null 是草率的。我只是想慎重考虑:只要参数或返回值可以为空，对它的解释就应该是显式的。

```
/** 
 * Consumes the next line of text and returns it.
 * Returns null if there are no more lines. 
 */
String readUtf8Line() throws IOException;
```

Kotlin 很棒，因为它要求我们在使用 null 时要明确。在 Kotlin 中声明相同的方法会将 null 放入类型系统中。

```
*/**
 * Consumes the next line of text and returns it.
 * Returns null if there are no more lines.
 */* fun readUtf8Line(): String?
```

但是当我们想从 Kotlin 调用 Java APIs 时会发生什么呢？我们需要一个注释来表明我们的返回值可以是 null。但是，令人沮丧的是，Java 没有内置的`@Nullable`注释。相反，我们需要选择第三方库来提供它:

*   Android 的 Nullable 对于纯 Android 项目来说是一个很好的选择，但是我们正在编写需要在 Android 和 Java 上工作的库。
*   Checker 框架的 Nullable 支持 Java 8 的类型注释，这允许它表达类似于`ArrayList<@Nullable String>`的概念。
*   [JSR 305 的可空包](https://jcp.org/en/jsr/detail?id=305)有一个标准的`javax.annotation`包名。这是开源 Java 的黄金标准 [Guava](https://github.com/google/guava) 使用的。

我们正在跟随 Guava 的脚步，并将在我们的开源项目中采用`javax.annotation.Nullable`。Java 用户可以从他们的 ide 和静态分析工具中获得额外的支持。Kotlin 用户得到的更多:编译器强制的空安全。如果您当前的 Kotlin 代码不是空安全的，那么您需要在集成这些更新时修复它。

这种依赖只是编译时的，所以您的构建脚本不会改变，您的二进制文件也不会变得更大。

今天我们发布了 [Okio 1.13](https://github.com/square/okio/blob/master/CHANGELOG.md) ，增加了`@Nullable`来帮助你避免 null 带来的问题。在接下来的几天里，我们将继续发布其他开源库。尽情享受吧！

这篇文章是 Square 的“ [Square 开源♥s·科特林](/square-corner-blog/square-open-source-loves-kotlin-c57c21710a17)系列的一部分。