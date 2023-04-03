# 从 Java 到 Kotlin 并返回(II):从 Java 调用 Kotlin

> 原文：<https://medium.com/google-developer-experts/from-java-to-kotlin-and-back-ii-calling-kotlin-from-java-3bdf72da6e52?source=collection_archive---------6----------------------->

![](img/56a3906ec245aecc70a64cc313c09c17.png)

本文是系列文章的一部分。您可以在这里找到本系列的其余文章:

[从 Java 到 Kotlin 并返回(II):从 Java 调用 kot Lin](/google-developer-experts/from-java-to-kotlin-and-back-ii-calling-kotlin-from-java-3bdf72da6e52)

[从 Java 到 Kotlin 并返回(III):从 Kotlin 调用 Java](https://enriquelopezmanas.medium.com/from-java-to-kotlin-and-back-iii-calling-java-from-kotlin-f33f5c246d69)

在上一篇文章中，我们探讨了 Java 和 Kotlin 如何相互交互，以及这方面的一些注意事项。在第二版中，我们将继续思考 Java 调用 Kotlin 时需要考虑的一些相关方面。

# 类别中的属性

当我们听到语言和数据类时，第一个突出的 Kotlin 特性可能是。数据类主要用来保存数据，并允许一些额外的功能，因此被称为数据类。我们喜欢它们是因为它们会自动生成一些函数(getters、setters、`toString()`、`copy()`、`equals()`和`hashCode()`，否则我们需要手动创建这些函数。例如，一个只包含`var name : String`的数据类将在 Java 中编译成如下代码:

Data class compiled in Java

如果变量的名称以 is 开头，那么得到的 getter 将以前缀 is 开头。比如说。如果我们有`var isYoung: Boolean`，那么产生的 setter 将是:

Resulting setter of an “is” attribute

请记住，这不仅适用于布尔类型，也适用于任何类型。

# 用 JvmName 命名

我们在上一篇文章中探讨了`@JvmName`的一些用法，我想提供一些更多的想法。

例如，尽管 Kotlin 支持可选值，但在 Java 中我们并不这样命名。因此，如果我们正在设计可能被 Java 调用的扩展函数，我们可能希望为它们提供一个不同的名称，以便它们对我们的 Java 代码来说足够地道。让我们检查以下代码:

Kotlin class with idiomatic naming

很可能会让我们的 Java 同行感到困惑，他们在寻找的是一个变量是否可能为空。因此，我们指定`@JvmName(“ofNullable”)`，来改变产生的 JVM 名称。这将在 Java 中被调用如下:

Java calling JvmName function

还要注意，Java 可以访问不同类型的密封类。

# Java 接口中的默认方法

从 Java 1.8 开始，Java 接口可以包含默认方法。无需太多细节，它们使您能够向现有接口添加新功能，确保与为上述接口的旧版本编写的代码兼容。下面的[链接](https://docs.oracle.com/javase/tutorial/java/IandI/defaultmethods.html)详细解释了它们的工作原理。

如果我们想让 Kotlin 接口的所有非抽象成员成为实现它们的任何 Java 类的默认成员，我们需要用以下选项编译代码:

`-Xjvm-default=all`

让我们在实践中看到这一点。考虑下面这个带有默认方法的接口，我们正在用`-Xjvm-default=all`进行编译:

Default interface Kotlin

这将在 Java 类中实现，如下所示:

Java class implementing default interface

当然，Java 将能够调用接口的所有功能:

Java class calling default methods

一个有趣的调整是，当然，Java 也可以覆盖所有的默认函数。因此，如果在我们的例子中，`functionA()`需要在实现它的类中有一个自定义实现，我们可以安全地覆盖它。

# Getter 和 Setter 重命名

偶尔我们可能想要重命名我们的 getters 和 setters。一个典型的例子是，当返回一个属性时，可以通过对一些其他属性的操作来组合(例如，返回一个带有`getName()`的名称，它添加了某种前缀或评估来确定返回的完整字符串)。我们可以很容易地用注释`@get:JvmName`和`@set:JvmName`来做，如下所示:

Changing getters and setters

# 零安全

Kotlin 是空安全的，Java 不是空安全的。当我们从 Java 调用 Kotlin 函数时，我们总是可以传递一个`null`作为非空参数。Kotlin 为所有需要非空值的公共函数生成运行时检查。这立即在 Java 代码中引发了一个`NullPointerException`。在函数中定义可空参数和不可空参数时要小心，因为在 Kotlin 中令人愉快的体验可能会变成 Java 代码中的运行时异常。

# 在 Java 中使用 Nothing 类型

或者从技术上来说，不使用它，因为在 Java 世界中没有自然的对等物。其实很有意思，因为 java Void 接受 null，但没有什么不接受的。事实上，这是计算机科学中的一个复杂问题，我们可以从哲学上用另一种方法来解决，因为我们试图用一些东西来表示任何东西，而我们是处理我们世界中物品的物理表现的生物。抛开虚无不谈，在 Java 中，虚无类型用原始类型表示，所以在处理虚无时要记住这一点:

Nothing in Java

# 摘要

本文探讨了更多从 Java 调用 Kotlin 代码的技巧。本系列的下一篇文章将探索河流的反面，我们将了解 Kotlin 如何使用遗留 Java 代码进行编码，以及我们需要记住哪些注意事项。

我在我的推特账户上写下我对软件工程和生活的想法。如果你喜欢这篇文章或者它对你有帮助，请随意分享，👏和/或发表评论。这是给业余作家加油的货币。