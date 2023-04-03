# 作为 Java 开发人员学习 Kotlin

> 原文：<https://medium.com/version-1/learning-kotlin-as-a-java-developer-ee0339126822?source=collection_archive---------6----------------------->

## 介绍

这篇文章将详细介绍我在学习科特林时学到的东西。

从 20 世纪 90 年代末开始，我就一直在使用 Java(断断续续地使用)(JDK 1.1，GUI 使用 AWT)。我记得从 AWT 到 Swing 的转变，以及理解不同的做事方式有多难。让我的头脑思考 Swing 所需的不同模式需要很大的努力。

从那以后，我使用过几种编程语言和 GUI 框架，有了这些经验，我希望过渡到 Kotlin 会比从 AWT 过渡到 Swing 更容易。

## 为什么要学科特林？

我问自己的一个问题是“为什么要学科特林？”。跟上 Java 生态系统(框架、库、构建工具、云等等)的变化需要相当多的时间和精力。学习科特林只会让我有更多的时间去了解吗？嗯，它可能会给我提供更多的信息，但由于 Kotlin 运行在 JVM 上，并且被设计为与 Java、相同的框架、库等互操作。都可以和 Kotlin 一起使用，所以我只是在学习这种语言，而不是一个全新的生态系统。

我学习 Kotlin 的最大原因之一是，谷歌似乎正在将 Android 开发从 Java 过渡到 Kotlin。

## 从哪里开始？

我被指向几门课程，让我开始学习科特林:

1.  [LinkedIn — Kotlin 基础培训](https://www.linkedin.com/learning/kotlin-for-Java-developers)
2.  [Coursera——面向 Java 开发者的 Kotlin](https://www.coursera.org/learn/kotlin-for-Java-developers/home/welcome)

还有 Android 开发网站和 Kotlin 语言网站:

1.  [安卓开发者网站](https://developer.android.com/kotlin/learn)
2.  [科特林网页](https://kotlinlang.org/docs/getting-started.html)

我第一次尝试 LinkedIn 课程，因为它只有大约 3 个小时长。

*   该课程很好地介绍了 Kotlin，但是(正如 3 小时课程所预期的那样)并没有详细介绍所涉及的任何特性。
*   本课程的结构很好，每一部分都有一个快速的挑战来测试你对一些概念的理解程度。
*   它确实涵盖了所有的基础知识，但是如果你已经了解 Java，那么大部分内容都很容易掌握。

在我完成 LinkedIn 课程后，我参加了 LinkedIn Kotlin 技能评估，并在评估参与者中获得了“高于 63%”的分数。不幸的是，这意味着我没有获得 LinkedIn 技能徽章，因为我需要击败另外 7%或 8%的评估者。

这向我表明，虽然 LinkedIn 课程是一个很好的入门课程，但它并没有足够详细地介绍 Kotlin，以允许你在完成该课程后继续开发 Kotlin。

有很多方法可以继续学习 Kotlin，但我接下来被引导到 Coursera 课程。

Coursera 课程是由 JetBrains(Kotlin 的创建者)编写的，因此，这是一个进入 kot Lin 开发的好方法。这是一个为期 5 周的课程，因此比 LinkedIn 课程需要更多的投入，但它对 Kotlin 的所有领域都有更详细的介绍。Coursera 课程是免费的，但是如果你想要一个证书，那么你需要付费。

## 怎么学？

仅仅通过阅读一本书或观看一些视频，我学得并不好，我需要尽快将这种学习付诸实践，让我的头脑记住它。

两个 Kotlin 课程都很好，因为它们都有实用的元素，可以让你练习所教授的功能。这有助于巩固知识，并展示如何在代码中使用该特性，但示例是非常小的片段，并没有展示代码如何与其他框架/库集成。

编程语言本身是非常有限的。仅仅知道一门语言不会让你走得很远(除非你在裸机引导加载程序上工作，在那之后你有可用的库)。

为了在真实的应用程序中看到 Kotlin，我决定编写一个简单的 Spring Boot CRUD API 来检索日志提示。(见这篇 [BBC 文章](https://www.bbc.co.uk/bitesize/articles/z3hshcw)了解日志的重要性。)

(本文不打算介绍那个应用程序的代码。有兴趣可以[在 GitHub](https://github.com/mrspaceman/KtJournalPrompts) 上查看代码。)

Spring 框架已经支持 Kotlin 好几年了， [Spring 参考文档](https://docs.spring.io/spring-boot/docs/current/reference/html/features.html#features.kotlin)包括 Java 和 Kotlin 的样本代码。

我使用 Spring Initializer 在 IntelliJ 中创建了一个新的 Spring Boot 项目，它允许你选择 Java、Kotlin 和 Groovy 作为开发语言。你可以通过导航到 [spring initializr 网页](https://start.spring.io/)来独立访问 Spring Initializer。

因为这是一个简单的 CRUD 项目，没有真正的业务逻辑，所以编写起来很简单，但是在我创建项目的时候就出现了一些问题。

首先，我习惯于使用 lombok 来降低样板代码的数量，所以我自动选择它作为初始化器中的一个库，并开始使用它来记录日志等。这很好，但是当我需要创建一个实体类时，我会自动使用`@Data` lombok 注释。这在 IDE 中是允许的，但是我不能让它工作。

我开始在谷歌上搜索 Kotlin 中使用 lombok 注释，并不断看到比较 Kotlin 和 lombok 的链接。这让我意识到我应该使用 Kotlin 特性，而不是 lombok 注释。这让我用 Kotlin `data`类替换了 lombok `@Data`注释。一旦我意识到我应该使用数据类而不是 lombok 注释，我决定完全删除 lombok 库。

我还遇到了一些奇怪的问题，我找不到解决方法。这些原来与我的*Spring Boot/科特林/玛文*项目使用的目录结构有关。源代码已经在`src/main/Java`中自动创建，虽然代码编译成功，但当我试图运行项目时，却出现了一些意外的异常。我花了一段时间考虑将我的类文件移动到一个 T4 目录中，但是当我成功编译代码时。

对于 Java 开发人员来说，用 Kotlin 编写应用程序非常容易。事实上，您可以像使用 Java 一样继续使用 Kotlin，但这并没有充分利用 Kotlin 语言的全部功能。

用 Kotlin 编写应用程序不仅仅是与 Java 的语法差异。你需要记住使用 Kotlin 给你的特性，如果你是混合语言，那么这可能会很困难。如果你已经知道 C，这与学习 C++很相似。你可以用 C 风格编写 C++程序，但是你会错过 C++带来的一些特性。

## 结论

当学习任何一门新的语言时，很难把已经学过的概念放在一边。进入新语言的“思维模式”可能是最难的部分。当学习一门使用不同[范例](https://en.wikipedia.org/wiki/List_of_programming_languages_by_type)的编程语言时尤其如此(例如，Forth、Postscript 等约束编程语言……)。

学习新的语言可能是一个挑战，但它也可以证明是有益的，即使你最终不使用该语言，因为它将帮助你保持开放的思维，以不同的方式使用编程语言。

![](img/4fa54a729e15aaf73c9c00343dbabbbb.png)

**关于作者:** Andy Aspell Clark 是 Version 1 的 Java 技术主管。