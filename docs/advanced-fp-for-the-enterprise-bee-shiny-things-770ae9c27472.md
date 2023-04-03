# 面向企业蜜蜂的高级 FP:闪亮的东西

> 原文：<https://medium.com/google-developer-experts/advanced-fp-for-the-enterprise-bee-shiny-things-770ae9c27472?source=collection_archive---------2----------------------->

![](img/ee5f77ff9beb92f20abc197e633c0056.png)

Vendor at a Honey Festival in France

# 介绍

这是我们八部分系列的最后一篇文章。我们已经为好奇而又注重实践的 Kotlin 开发人员探索了高级函数式编程。我希望你觉得这是值得的。

以下是迄今为止所有内容的回顾:

*   [第一部分](/google-developer-experts/advanced-fp-for-the-enterprise-bee-traverse-b5e4e8b7b8e4):横移作业
*   [第二部分](/google-developer-experts/advanced-fp-for-the-enterprise-bee-applicatives-be76e4b6803c):使用应用程序
*   [第 3 部分](/google-developer-experts/advanced-fp-for-the-enterprise-bee-higher-kinded-types-c6742e24527):更高级的种类
*   [第 4 部分](/google-developer-experts/advanced-fp-for-the-enterprise-bee-kleisli-1d0de0fa82d9):克莱斯利型
*   [第 5 部分](/google-developer-experts/advanced-fp-for-the-enterprise-bee-typeclasses-2addc232ae23):使用类型类
*   [第 6 部分](/google-developer-experts/advanced-fp-for-the-enterprise-bee-optics-2ccc444d409b):使用光学器件
*   [第 7 部分](/google-developer-experts/advanced-fp-for-the-enterprise-bee-state-4f8fd2d8098b):状态类型

在这最后一篇文章中，我想:

1.  向你展示一些额外的 FP 操作
2.  对箭头 1 中的新特性提供一些见解
3.  提供一些我认为有用的资源的链接

# 第 1 部分:来自 Arrow 的附加操作

我们已经看到 Arrow 可以大大简化您的日常编码工作。但是我们的调查远非全面。以下是我发现在日常开发中有用的一些附加操作。

## 开始吧

我们的大多数例子都是从类似下面的代码开始的。我们迭代 1 到 6 之间的整数范围，并将它们映射到右*或左*的**

```
[Left(1), Right(2), Left(3), Right(4), Left(5), Right(6)]
```

## 从地图到 Bimap

我们已经看到*地图*与*或者*都是右倾的。左侧的*内的值被忽略。但是如果你想对左*和右都应用变换，那么你可以使用`bimap`。

在下面的例子中，我们将奇数(包含在左*和右*中)乘以 10，将偶数(包含在右*和 100:*

```
[Left(10), Right(200), Left(30), Right(400), Left(50), Right(600)]
```

## 折叠再探

在以前的文章中，我们使用`fold`作为终端操作。我们提供了两个副作用函数，以便处理*左*和*右*结果。这可能像`result.fold(::println, ::println)`一样简单

然而，这并不是*折叠*的唯一可能用途。与`bimap`不同的是， *fold* 操作返回一个值而不是一个容器。因此，将`bimap`应用于*或者*会产生另一个*或者*，而应用`fold`会产生一个值。

下面是同样的例子，改写成使用`fold`。如您所见，输出是一个数字列表，而不是一个*列表*

```
[10, 200, 30, 400, 50, 600]
```

Fold 有许多有趣的变化。例如`bifoldLeft`允许我们在函数中注入一个附加值。

让我们用十作为我们的附加值。我们将这个值加到奇数上，同时乘以偶数:

```
[11, 20, 13, 40, 15, 60]
```

## 交换价值

在有两个结果的容器中，可以通过`swap`操作在选项之间进行切换。在这种情况下，所有的*右*项都变成了*左、*项，反之亦然:

```
[Left(1), Right(2), Left(3), Right(4), Left(5), Right(6)]
[Right(1), Left(2), Right(3), Left(4), Right(5), Left(6)]
```

## 重温半群

在以前的文章中，我们看到一个*半群*是一个具有`combine`方法的容器。大多数函数类型是*半群*，包括*或者*。

`maybeCombine`方法组合两个*或者*实例，假设它们都是*左*或者*右*。这里我们将向列表中的每个*右*实例添加 100，但是*左*实例将不受影响:

```
[Left(1), Right(102), Left(3), Right(104), Left(5), Right(106)]
```

## 重温应用程序

我们系列中的[第二篇文章详细研究了*应用程序*。列表算作*应用程序*，因此提供一个`ap`操作。](/google-developer-experts/advanced-fp-for-the-enterprise-bee-applicatives-be76e4b6803c)

如果您提供一个包含单个函数的列表，那么该函数将应用于原始列表中的每一项，如下面的 *result1* 所示。如果您提供多个函数，那么每个函数都与每个项目相关联，如 *result2:* 所示

```
[10, 20, 30, 40]
[100, 1000, 200, 2000, 300, 3000, 400, 4000]
```

# 第 2 部分:箭头 1 中的新特性

当 Arrow 第一次被创建时，它是一个非常志愿者驱动的努力，看看一个类似于[猫](https://typelevel.org/cats/)或 Haskell base 库的库是否可以在 Kotlin 中工作。令人高兴的是，答案是肯定的！但是必须做一些调整，因为更高级的类型和类型类不是 Kotlin 固有的。详见本系列[第三篇](/google-developer-experts/advanced-fp-for-the-enterprise-bee-higher-kinded-types-c6742e24527)和[第五篇](/google-developer-experts/advanced-fp-for-the-enterprise-bee-typeclasses-2addc232ae23)。

随着 Arrow 接近重要的 1.0 版本，焦点已经转移到最大化它的可用性和充分利用 Kotlin 独有的语言特性。这导致了一些重大变化。最重要的是，在可预见的未来，更高级的类型将被放弃，直到 Kotlin IDEA 插件意识到社区 Kotlin 编译器插件。

正如我们已经看到的，HKT 要求呼唤`fix`，这是学习的一个障碍。避免这些调用的唯一方法是通过编译器插件和适当的 IDEA 支持。这可以在需要的地方插入下导管，而不需要任何人工干预。不幸的是，虽然 Kotlin 1.4 为所有编译器提供了一个通用的后端，但是编译器插件的标准 API 还没有出现。

好消息是已经有了更好的选择。当你用`suspend`声明一个函数时，编译器实现一个基于延续的状态机来处理函数的暂停和恢复。我的同事 Eamonn 和我去年写了一篇关于这个的文章。

这种机制不仅可用于协同例程的并发。研究表明，悬浮函数可以用来实现我们在本系列中讨论的所有类型的计算模块。所以*或者*，*有效*，*状态*等等，相关的效果操作符可以实现为挂起函数，不需要 HKT 或者类型类。

这次重新设计的重点是悬浮，不仅适用于 Arrow Core，还适用于 Optics 和 Fx，它们现在提供了与悬浮一起使用的最流行的 FP 操作符的内嵌版本。随着这一变化， *IO* 类型将被弃用，因为暂停功能涵盖了 *IO* 的所有用例，而没有分配和 ADT 成本。

你可以在箭头网站上深入了解这一重新设计。由于减少了分配与 Typeclasses、ADT 和滥用堆相关的实例的需要，使这些模式成为堆栈安全的，所以它提供了性能上的实质性改进。核心类型也将变得更容易使用——例如，当调用`traverse`时，你将不再需要传入相关的*可应用的*实例。

所有这些新功能将在两个即将发布的版本中实现。箭头 0.12(已经在预览版中可用)将反对 HKT 的和类型类，而箭头 0.13 将完全删除它们。它们将在未来几周内同时问世。

不使用 kinds 或者不介意中断更改的用户可以直接使用 0.13。这一版本将构成 Arrow 1.0 的基础，预计将于今年夏天发布。它将得到长期支持，并更容易被业界采用。

# 第 3 部分:附加资源

这里有一些我特别推荐的书籍、论文和讲座。我根据作者对 FP 知识的掌握程度，按升序列出了它们:

*   弗里斯比教授的函数式编程指南已经足够了。一本优秀的 FP 入门书，免费提供 JavaScript 示例。
*   Kevlin Henney 的精彩演讲，讲述了 Lambdas 和物体是如何相互对立的。事实上，它们是重叠和可替代的。
*   罗纳·比雅纳松([‘红色 Scala book’](https://www.amazon.co.uk/Functional-Programming-Scala-Paul-Chiusano/dp/1617290653)的作者)关于纯函数式编码如何实现大型系统组合的[主题演讲。](https://skillsmatter.com/skillscasts/10746-keynote-composing-programs)
*   即将推出的 [Kotlin / Arrow 版](http://Functional Programming in Kotlin)上面提到的那本书。
*   Eric Torreborre 关于 Haskell 的特性是如何逐渐被主流语言采用的演讲。
*   Scott Wlaschin 讲述了如何使用[功能模式来促进面向铁路的编程](https://www.youtube.com/watch?v=srQt1NAHYC0)。
*   劳尔·拉贾·马丁内斯在科特林孔夫对《箭头》的介绍。
*   [菲利普·瓦德勒介绍范畴理论](https://www.youtube.com/watch?v=V10hzjgoklA)，以及与之相关的情况。

# 结论

就是这样，伙计们！我真的希望这个系列对你有用，并且帮助你增强了对 FP 概念的了解。反馈总是受欢迎的——无论是在这里，[在 Twitter 上](https://twitter.com/GarthGilmour)和[在工作中](https://instil.co/enquiries/)。编码快乐！

# 谢谢

感谢 [Richard Gibson](https://twitter.com/rickityg) 和 [Instil 培训团队](https://instil.co/training/team/)对这一系列文章的评论、评论和鼓励。在此，我要特别感谢来自 47 度的 Raul Raja，他和我以及 Richard 一起讨论了他们对 Arrow 1 的看法。所有的错误当然是我自己的。