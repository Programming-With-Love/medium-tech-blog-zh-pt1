# 有效的科特林-为什么这么久？

> 原文：<https://blog.kotlin-academy.com/effective-kotlin-why-so-long-a6e28413321c?source=collection_archive---------1----------------------->

![](img/698556582962689f097919a257f2f100.png)

我有一个坦白:我已经写了近[一半的书](https://leanpub.com/effectivekotlin)，我已经准备好[推出 EAP](/effective-kotlin-early-access-program-8e5d0ee9dbcd) 。现在我要重写几乎所有的东西。

等等？什么？为什么？

我会向你解释一切。首先，我需要从我的心态开始，对于一本书，就像对于一个代码一样，如果你想做一些长期的伟大的东西，你不应该害怕删除你已经做的东西。我想写一本有所作为的重要的书，我刚刚明白最初的公式并不是真正需要的。

第二，让我们思考这个问题:一个人能否用经典的有效 X 公式写一本关于科特林的书，这个公式会长期有效吗？(这个公式是一本书，其中每一章都给出了一个关于如何提高代码质量的提示。这些提示，称为项目，通常解释如何克服一些限制或来自语言设计的问题。我认为这肯定是，但后来我注意到一些事情改变了我的看法。

想到[有效的 Java](https://www.oreilly.com/library/view/effective-java-3rd/9780134686097/) 。大多数项目都是有效的，因为 Java 变化非常缓慢(尽管它仍然在变化，所以有效的 Java 必须适应新的版本)。Kotlin 是 Java 的一种进化，大多数有效的 Java 项目在 Kotlin 中都过时了。有的[绝对](/effective-java-in-kotlin-obsolete-items-thanks-to-kotlin-items-3-4-16-40-61-from-3rd-edition-31952da308f4)，有的只是部分(像[这个](/effective-java-in-kotlin-item-1-consider-static-factory-methods-instead-of-constructors-8d0d7b5814b2))。能直接应用到科特林的物品只有几件。它们通常代表更高层次的概念。

那么，为什么不用类似的公式写本书呢？Kotlin 也有一些限制和良好的实践。我开始收集它们并描述它们。我甚至[在 Kot 上分享了一些想法。学院](https://blog.kotlin-academy.com/effective-kotlin/home)。这些文章受到了读者的热烈欢迎，所以它激励着我前进。此时一切看起来都很好。虽然随着时间的推移，我注意到 Kotlin 及其生态系统(如 IDE 建议或与 IDE 集成的编译器)变化非常快，并对我和社区的建议做出了反应。

例如，我的一个章节“喜欢更少的处理步骤”建议使用`mapNotNull`代替`map`和`filterNotNull`，或者将映射转移到`jointToString`转换函数中。人们可能会对我们为什么需要这样做感兴趣。不过这并不重要。Android Studio 和 IntelliJ 现在在所有这些情况下都有很好的改进建议。此外，让他们变得更好比教育所有 Kotlin 开发者更容易。这些信息可能很有趣，但已经不重要了。我刚刚删除了整个项目。

更重要的是，科特林可以在下层改变。几乎所有的性能优化都可以由足够智能的编译器来完成。当我们谈论集合时，我们经常忘记那些总是可以在引擎盖下改变，并被一些更有效的版本所取代。

更大的问题也得到了解决，所以我被迫删除或修改了许多其他章节。有人可能认为这样的开发速度只是暂时的，随着时间的推移，Kotlin 将停止变化，我们将能够编写一套长期有效的实践。我不这么认为。在 KotlinConf 2018 [期间，Andrey Breslav 宣布 Kotlin 将保持现代化，并且不会害怕应用重要的变化(通过折旧循环和自动迁移)](https://www.youtube.com/watch?v=PsaFVLr8t4E)。这一切使我确信:

> Kotlin 在不断改进，没有一本建议如何克服 Kotlin 问题的书在出版几年后仍然有效。

这并不意味着没什么可写的。事实上，有很多重要的知识是人们需要的，而这些知识往往被大多数书所忽略。

首先，我认为我们真的需要更高层次的项目，比如“更喜欢组合而不是继承”或者“[考虑工厂方法](/effective-java-in-kotlin-item-1-consider-static-factory-methods-instead-of-constructors-8d0d7b5814b2)”。我总是最喜欢他们，我认为他们后来大多被记住。这些都是重要的编程思想，我们随处可见，但很少出现在一本书中。我决定，我们需要的是把它们中最重要的收集在一起，以及它们如何能在科特林中得到体现。

第二，Kotlin 不是一种实验性语言，而是一种实用语言。它旨在支持良好的编程风格。但是这是什么风格呢？当你深入了解 Kotlin 是如何设计的时候，当你观看 Kotlin 团队关于 Kotlin 设计的讲座时，或者当你咨询 Kotlin 设计师时，你可以解码它。我很高兴能够做到所有这些。基于此，我想描述可读性和习惯用法的良好实践。

这两个关键要素是我们需要的 Kotlin 最佳实践。很棒的一点是，自从我在我的公司研讨会上教他们之后，我能够和许多开发人员，从初级到非常有经验的高级，一起面对和讨论他们。

这就是为什么现在我几乎重写了所有我已经写过的东西。有些物品会保存下来，但有些已经被扔掉了。新公式也更难。指出一个问题很容易，但是写高层次的问题需要更多的努力。我将用其他书籍、权威和科学思想来对抗它。太好了，我在会议上遇到许多权威；)从长远来看，我相信读者和社区将从这个新公式中受益匪浅。

剩下要说的就是，如果你在等，很抱歉，但这需要更多的时间。质量比出版速度更重要。坏书很多，真正好的只有几本。我尽我所能让这个变得更好更有价值。

如果你想在我们出版这本书的时候得到通知，[订阅时事通讯](https://kotlin-academy.us17.list-manage.com/subscribe?u=5d3a48e1893758cb5be5c2919&id=d2ba84960a)，[关注推特](https://twitter.com/ktdotacademy)和/或[关注](http://blog.kotlin-academy.com/)。

[![](img/3146970f03e44cb07afe660b0d43e045.png)](https://kotlin-academy.us17.list-manage.com/subscribe?u=5d3a48e1893758cb5be5c2919&id=d2ba84960a)