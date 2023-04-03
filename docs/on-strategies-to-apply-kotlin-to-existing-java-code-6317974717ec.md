# 将 Kotlin 应用于现有 Java 代码的策略

> 原文：<https://medium.com/google-developer-experts/on-strategies-to-apply-kotlin-to-existing-java-code-6317974717ec?source=collection_archive---------0----------------------->

![](img/6c960de361e676a90ba0fe7f5fc18fec.png)

自从谷歌 I/O 上的最新公告以来，事情变得很疯狂。在 [Kotlin Weekly](http://www.kotlinweekly.net/) 的邮件列表中，我们的订户在过去两周内增加了 20%以上，提交的文章增加了 200%以上，在我组织的 Meetup([kot Lin Users Group Munich](https://www.meetup.com/Kotlin-User-Group-Munich/))上，我们的与会者人数大幅增加。所有这些与开发人员社区的普遍不满结合在一起。

![](img/b087ad96e3897c80b0f2fd94a0cb33dd.png)

A trend that will only continue to grow.

不知何故，现在很明显未来将走向何方。尽管谷歌承诺保持对 Java 的支持，谷歌和甲骨文之间的法律战以及 Kotlin 是一种更简洁、更有效和更强大的语言这一明显事实在阅读的海洋中指明了方向。我发现这条推文相当有预见性。

几个月前，当我在一个与 Kotlin 相关的环境中时，可能最常出现的问题是，现在是否是开始向 Kotlin 迁移的好时机。我的回答总是一成不变的**是的**。如果进行 Kotlin 迁移，好处很多，几乎没有缺点。我能想到的唯一技术缺陷是增加方法计数，因为 kotlin-stdlib(目前)增加了 [7191 个新方法](https://blog.jetbrains.com/kotlin/2016/03/kotlins-android-roadmap/)。考虑到摆在桌面上的新好处，这是一个不可接受的缺点。

既然这个问题的答案是肯定的，没有任何缓解措施，我意识到还有另一个问题在徘徊:*如何开始采用科特林？*

在这篇文章中，我的目的是为那些不知道从哪里开始或寻找新想法的人提供一些想法。

# 1.-从测试开始

是的，我知道测试在本质上是有限的。单元测试就是这个意思:你正在测试单个的单元和模块。当你只有一堆单独的类或者几个辅助类时，开发复杂的架构网络是很困难的。但是，这是一种非常廉价和有效的方式来建立意识和传播新语言的知识。

我听到的反对 Kotlin 的一个反复出现的论点是避免部署到用 Kotlin 编写的产品代码行中。尽管我认为这是一个高度渲染的问题，但我想向你强调一下。如果您从测试开始，实际上没有部署任何代码。相反，这些代码很可能在您的 CI 环境中使用，并作为扩展知识的一种方式。

开始用 Kotlin 写新的测试，他们可以合作，直接和其他 Java 类对话。当您有空闲时间时，[迁移 Java 类](https://www.jetbrains.com/help/idea/2017.1/converting-a-java-file-to-kotlin-file.html)，检查结果代码并应用您发现需要的任何转换。以我个人的经验，60%的转换代码是直接可用的，对于没有复杂功能的简单类，这个比例更大。我发现这第一步是一个非常安全的开始。

# 2.-迁移现有代码

你已经开始写一些代码了。你知道这种语言的一些基础知识。现在你是[科特林-准备生产](https://www.youtube.com/watch?v=-3uiFhI18g8)！

当你第一次开始处理你的生产代码时，我发现首先从影响较小的类开始是有效的:[dto](https://en.wikipedia.org/wiki/Data_transfer_object)和模型。这些类的影响非常小，并且可以在极短的时间内轻松重构。是了解[数据类](https://kotlinlang.org/docs/reference/data-classes.html)并大幅缩减代码库大小的最佳时机。

![](img/8f00bc4334d872b3619364bbfaa74f60.png)

This is the PR you want to send

在这之后，开始迁移酉类。也许是 **LanguageHelper** ，或者是 **Utils** 类。尽管被广泛使用，这种类倾向于提供一组在关系和影响上受限的功能。

在某一点上，你会感到足够舒服去处理你的架构的更大的和中心的类。不要害怕。特别注意**可空性**，这是 Kotlin 最重要的特性之一。如果你已经用 Java 编程几年了，这将需要一个新的思维模式，但是新的范式基本上会在你的头脑中形成，相信我。

请记住:您不需要担心迁移整个代码库。Kotlin 和 Java 可以无缝交互，现在不需要有 100%的 Kotlin 代码。一直做到感觉舒服为止。

# 3.-和科特林一起去野外

此时，您完全可以开始用 Kotlin 编写所有新代码。把这当成以前的关系，不要回头。在提到的**可空性**的顶部，当你开始用 pure Kotlin 编写你的第一个特性时，也要注意默认的参数。考虑扩展功能，而不是继承。做拉式请求和代码审查，并与你的同事讨论如何做得更好和改进。

最后一点，享受吧！

# 学习科特林的资源

以下是我试过的，可以推荐学习 Kotlin 的链接列表。我特别喜欢书，尽管有些人不喜欢书。我发现大声朗读、重写并在电脑上练习非常重要，这样可以更好地沉淀知识。

1.  许多 JetBrains 的人和 Kotlin 的爱好者在一起。
2.  [科特林周刊](http://kotlinweekly.net/):我管理的一个邮件列表，包含每周精选的科特林主题。
3.  [Kotlin Koans](https://kotlinlang.org/docs/tutorials/koans.html) :一系列在线练习来练习和巩固你的 Kotlin 技能。
4.  Kotlin 在行动:来自 JetBrains 的一些从事 Kotlin 工作的人的书。
5.  [kot Lin for Android developers](https://transactions.sendowl.com/stores/7146/39165):本书重点讲述如何应用本书进行 Android 开发。
6.  [学习科特林的资源](https://developer.android.com/kotlin/resources.html):谷歌网站有更多学习科特林的资源。

我在我的 [Twitter 账户](https://twitter.com/eenriquelopez)中写下我对软件工程和生活的想法。如果你喜欢这篇文章或者它确实帮助了你，请随意分享它，♥它和/或留下评论。这是给业余作家加油的货币。