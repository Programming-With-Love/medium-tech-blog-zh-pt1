# 面向企业蜂的高级 FP:Kleisli

> 原文：<https://medium.com/google-developer-experts/advanced-fp-for-the-enterprise-bee-kleisli-1d0de0fa82d9?source=collection_archive---------5----------------------->

![](img/4f44c45fa352474084f48a0a84373d1e.png)

An (Non-Kleisli) Arrow Pointing to Honey

# 介绍

这是本系列的第四篇文章，为注重实践的 Kotlin 开发人员探索高级 FP 概念。到目前为止，我们已经看了[遍历](/google-developer-experts/advanced-fp-for-the-enterprise-bee-traverse-b5e4e8b7b8e4)、[应用](/google-developer-experts/advanced-fp-for-the-enterprise-bee-applicatives-be76e4b6803c)和[高级类型](/google-developer-experts/advanced-fp-for-the-enterprise-bee-higher-kinded-types-c6742e24527)。这次我们调查的是 Kleisli 型。

我非常不愿意接近这个概念，主要是因为这个术语是从范畴理论借用来的。所以我以为会很曲折。幸运的是，Kleisli 比我们已经讨论过的许多概念都要简单。

# 简单的问题

让我们从重温第一篇文章中唯一的朋友开始。这个函数试图读取一个 JVM 属性，在一个*或者*中返回结果。

假设我们需要浏览一系列的财产价值:

*   我们得到了一个 JVM 属性的键 **A**
*   属性 **A** 的值将成为键 **B**
*   属性 **B** 的值将成为键 **C**
*   这个过程必须重复 N 次

让我们创建两个助手方法，一个创建这样的链，另一个打印出所有的 JVM 属性:

作为样本数据，让我们使用沙丘书中阿崔迪斯家族的血统:

当我们运行以上代码时，输出包含以下信息:

```
 Vladimir | Jessica
             Jessica | Paul
                Paul | Ghanima
             Ghanima | Moneo
               Moneo | Siona
```

我们的任务是通过 FP 和 Arrow 导航这个关联链。

# 程序性解决方案

这是如何以程序方式解决问题的:

运行此代码会产生正确的输出:

```
Right(Siona)
```

程序方法有三个问题:

*   我们不得不将 propertyViaJVM 封装在一个新的函数中，这个函数要么接受 T21，要么接受 T22
*   为了实现这个功能，我们需要注意*或*的规则
*   应用该函数需要嵌套和/或临时变量

# 克莱斯利溶液

下面是使用 Kleisli 类型编写的解决方案。我们仍然包装了 *propertyViaJVM* 函数，但是有一些重要的区别:

*   通过`Kleisli { … }`为我们进行包装
*   包装的函数接受一个常规字符串作为输入
*   *或者*的实现是从我们这里抽象出来的

然后，我们使用*将所有需要的调用链接在一起。*这将调用组合成一个单一的序列，当我们调用 *run* 时执行该序列。需要调用 *fix* 来执行向下转换，原因[在前一篇文章](/google-developer-experts/advanced-fp-for-the-enterprise-bee-higher-kinded-types-c6742e24527)中讨论过。

如果你熟悉 Java 线程库中的 *CompletableFuture* 类型，这是一个非常相似的设计。让我们看看它如何处理正确和错误的数据:

它给了我们想要的东西:

```
Right(Siona)
Left(No JVM property: Alia)
```

因为我们只包装了一个函数，所以可以通过用方法引用替换 lambda 来简化代码:

# Kleisli 中的附加功能

到目前为止，我们已经看到 Kleisli 类型简化了函数的组成，其中函数返回但不接受像*这样的一元类型，或者*或者*验证过的*。还有一些有用的实用方法。让我们稍微修改一下输入数据:

除了现有要求，我们现在还想:

*   将“Harkonnen”追加到客户端给定的初始值。该功能由*本地*功能提供。
*   向调用 *run* 产生的最终值追加‘at reides’。正如您所料，这种能力是由 *map* 函数提供的。

这又一次给了我们想要的输出:

```
Right(Siona Atreides)
Left(No JVM property: Alia)
```

# 为什么不用单子？

当我遇到克莱斯利时，我的第一个问题是“为什么不直接用一元合成？”。事实证明，我们总是可以的——事实上，这就是 Kleisli 的实现方式。

你可能已经注意到了，我们把任一单子传递给了*和*。结果是，如果你已经实现了一元类型，那么你就可以免费得到 Kelisli。因此，您可以选择最适合的方法。

下面是直接使用一元合成解决的问题:

这给出了我们想要的结果。但是与 Kleisli 解决方案相比，有更多的“管道”。我们必须记住:

*   暂停我们的功能
*   使用 bang 运算符(或等效运算符)
*   为中间结果声明变量

# 什么是克莱斯利箭？

在纯函数式编程和范畴理论中，函数的组合都是通过绘制(或编码)箭头来表示的。这是菲利普·瓦德勒的精彩演讲，充满了箭头图。Kleisli 箭头表示一元上下文中的功能组合。在我们的例子中，单子是*或者*，但是它也可以是*有效的*、*写者*、*状态*等等...

这就是为什么在语言允许的情况下，函数库通常提供一个箭头符号来替代*和*方法。这是我们用 Scala 和 [Scalaz](https://github.com/scalaz/scalaz) 库重写的例子。

请注意，在第 31 行，我们使用了 *> = >* 操作符来组合函数:

# 结论

尽管 Kleisli 这个术语来源于范畴论，但它既不复杂也不不实用。无论何时，克莱斯利都是一元作曲的绝佳替代品:

*   一个或多个函数返回一个容器(又名。Monad)但采用常规参数。
*   要求将这些功能链接在一起以获得最终结果。

# 运行示例代码

像往常一样，列出的所有代码都可以在这个位存储桶 repo 中找到[。然而，所使用的 Kleisli 类型仍处于试验阶段，因此可以在](https://bitbucket.org/instilco/advanced-fp-gde/src/master/)[Arrow 孵化器项目](https://github.com/arrow-kt/arrow-incubator)中找到。要运行我的‘UsingKleisli’项目，你必须首先克隆并构建 *arrow-incubator* ，然后将 *arrow-mtl* 和 *arrow-mtl-data* 库复制到 *libs* 文件夹中。

# 谢谢

感谢 [Richard Gibson](https://twitter.com/rickityg) 和 [Instil 培训团队](https://instil.co/training/team/)对这一系列文章的评论、评论和鼓励。所有的错误当然是我自己的。