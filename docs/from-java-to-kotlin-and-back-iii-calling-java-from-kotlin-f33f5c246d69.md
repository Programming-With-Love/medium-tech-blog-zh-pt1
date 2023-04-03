# 从 Java 到 Kotlin 并返回(III):从 Kotlin 调用 Java

> 原文：<https://medium.com/google-developer-experts/from-java-to-kotlin-and-back-iii-calling-java-from-kotlin-f33f5c246d69?source=collection_archive---------8----------------------->

![](img/7769aa8cef057eb59eadbe240e1038b1.png)

本文是系列文章的一部分。您可以在这里找到本系列的其余文章:

[从 Java 到 Kotlin 并返回(I) —从 Java 调用 kot Lin](/google-developer-experts/from-java-to-kotlin-and-back-i-java-calling-kotlin-9abfc6496b04)

[从 Java 到 Kotlin 并返回(II):从 Java 调用 kot Lin](/google-developer-experts/from-java-to-kotlin-and-back-ii-calling-kotlin-from-java-3bdf72da6e52)

在本系列的最后一章中，我们将评估从 Kotlin 调用 Java 代码时的注意事项。

有人可能会说，即使这种情况经常发生，记住一些可能遗留下来的代码也是不实际的。Kotlin 在设计时也考虑到了互操作性，因此 Kotlin 的 Java 代码比其他代码更“可调用”，因为这种设计从一开始就已经考虑在内了。但是，在编写 Java 代码时，有几点需要记住，我们将在本文中解释这些要点。

# 释文

由于 Java 缺少一些我们在 Kotlin 上已经拥有的强大特性，我们将非常依赖一些注释来准备我们的 Java 代码与 Kotlin 接口。

## 可空性

尽管可空性是 Kotlin 的核心特性之一，但 Java 并没有提供现成的支持。Java 中的每个参数都应该有一个可空性注释。否则，它们被理解为“[平台类型](https://p5v.medium.com/platform-types-in-kotlin-5caceeb556ad)”，这种平台类型有一种模糊的方式来确定可空性，并可能触发运行时错误，这正是 Kotlin nullability 想要避免的。例如，考虑下面这段代码:

如果我们试图用 false 调用函数 *doNotUseAnnotation* ，代码将触发一个异常。因为我们没有添加任何注释，Kotlin 暗示该函数不可为空。在 Java 中，我们将如下设置可空性:

最常见的是@Nullable 和@NotNull，它们出现在包 *org.jetbrains.annotation* 中，也出现在 *com.android.annotation* 和其他包中，比如 Lombok。

通常，没有正确注释的 Java 代码会导致不可预测的行为。如果你在 Android 领域有一些经验，框架是用 Java 编写的，你会不断地依赖注释，而注释并不总是在它们应该在的地方。

如果您正在处理可能会暴露给 Kotlin 的 Java 代码，请记住使用可空性注释来促进栅栏另一边的生活。

## @欠迁移注释

@UnderMigration 注释在我们维护一些 Java 代码时非常有用，我们希望通知我们的潜在客户它的当前状态(请注意，注释可以在一个单独的工件中找到， *kotlin-annotations-jvm*

@UnderMigration status 可以有三个不同的值，类似于 Kotlin 1.4 中引入的[显式 API 模式](https://blog.jetbrains.com/kotlin/2020/06/kotlin-1-4-m2-released/#explicit-api-mode):

*   `MigrationStatus.STRICT`会在错误使用注释时产生编译错误，导致代码失败。
*   `MigrationStatus.WARN`:与上一个类似，但只是删除一个警告
*   `MigrationStatus.IGNORE`完全忽略注释的用法。

# Java 中的 Getters 和 setters

Kotlin 使用属性访问，而不是像 Java 一样使用 getters 和 setters。getter 的定义是一个名称以 *get* 开头的 no 参数，setter 的定义是一个名称以 *set* 开头的单参数方法。布尔 getters 以 is 开始，所以我们可以使用更自然的人类语言来询问属性的值( *isClosed* ，is *Finished* 等等)。在设计 Java 类时要记住这一点，尽管这是非常标准的。有趣的是，如果你的属性只提供了一个 setter，那么它将不会作为一个属性可见，因为 Kotlin 不支持 set-only 属性(这通常是一个[反模式](https://softwareengineering.stackexchange.com/questions/50554/why-it-is-not-recommended-to-have-set-only-property))。

下面的 Java 类:

将像 Kotlin 中的以下块一样被访问:

# 科特林关键词

Kotlin 有几个硬关键字，我们不应该在我们的 Java 代码中使用，因为 Kotlin 的对应部分需要反勾号来调用它们。

Protip:在 Kotlin 中命名方法和变量时，可以到处使用这些反勾号，而不仅仅是在硬关键字或测试的情况下。

# 摘要

这最后一篇文章介绍了设计和处理我们的 Java 代码的技巧，稍后将在 Kotlin 中使用这些代码。Java 代码可以用比其他方式更少的努力向 Kotlin 公开代码，但是所探讨的要点将能够帮助您实现更有效的互操作代码。

我在我的推特账户上写下我对软件工程和生活的想法。如果你喜欢这篇文章或者它对你有帮助，请随意分享，👏和/或发表评论。这是给业余作家加油的货币。