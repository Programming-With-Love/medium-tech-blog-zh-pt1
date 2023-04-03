# 科特林本地人。新的内存管理模型

> 原文：<https://medium.com/google-developer-experts/kotlin-native-new-memory-management-model-7191fa30db30?source=collection_archive---------1----------------------->

![](img/9982e95287268f37c04f2aa5884cbc32.png)

在之前的帖子中，我们描述了如何使用本地并发机制[](/p/373663bf5a09/)**和*[](/google-developer-experts/kotlin-native-multithreading-without-coroutines-56599ea33620)**】。现在我们来谈谈新的内存管理模型。***

**8 月 31 日，Jetbrains 公司为 Kotlin/Native 推出了新的内存管理模型。主要关注点是线程安全、线程间的安全上下文和数据共享、内存泄漏修复以及无需使用特殊注释的工作。还有几个协程的改进。因为现在可以在不需要冻结的情况下在上下文之间切换。Ktor 在新版本中支持所有这些更新。**

**让我们总结一下建议的内存模型的新特性:**

**1.无冻结的多线程()。据称，我们可以从代码中移除所有的 freeze()块，甚至从后台工作人员中移除，并且可以在上下文和线程之间切换，而不会出现任何阻塞和问题。**

**2.atomic references/FreezableAtomicReference 不会产生任何泄漏。**

**3.使用全局常量时不需要 ShareImmutable。**

**4.Worker.execute 的生成器不再返回依赖关系的孤立图。**

**还有一些细微差别和副作用:**

1.  **我们仍然必须对原子引用使用 freeze()。若要处理它，请改用 FreezableAtomicReference。我们也可以用 ***AtomicRef*** 从 ***atomicfu*** 。**

**2.所有全局常量都是延迟启动的。在以前的版本中，所有的全局变量都在开始时立即初始化。在新版本中使用***eagerinization****来保持这样的行为。***

***3.不能保证挂起函数会将完成处理程序返回到主线程中。所以我们需要将它包装在 iOS 的*dispatch queue . main . async {…}*中。***

**4.*Swift/ObjC 对象的 Deinit* 可以在其他线程中调用。**

**说到协程，也有一些改进和变化。您可以在支持新内存模型的特殊版本分支中查看它们:**

**1.我们可以与工人、渠道和流程一起工作，而不会冻结。与 native-mt 版本相比，频道的所有内容可能会意外冻结。**

**2.*调度员。默认*现在被绑定到全局队列。**

**3. *newSingleThreadContext* 和*newFixedThreadPoolContext*可用于创建新的协程调度程序，支持一个或几个工作者的池。**

**4.*调度员。主*与*绑定*达尔文*的调度主队列*和其他本地平台的特殊工人。建议不要用于单元测试，因为在主线程队列中不会调用任何东西。**

*因此，有一堆不同的改进，变化，细微差别，也有一些性能缺陷和问题。所有这些都是已知的，并在文档中进行了描述[。目前它只是一个预览版本，而不是 Alpha 版本，Jetbrains 命令仍然在改进和发展它。](https://github.com/JetBrains/kotlin/blob/master/kotlin-native/NEW_MM.md)*

*好了，让我们将所有新特性应用到我们的代码示例中。
首先，我们将为协程和 Kotlin 安装正确的版本:*

*从协程中添加正确的依赖关系:*

**重要！您必须安装 Xcode 12.5 或更高版本。它与 1 . 6 . 0-M1–139 版本最低兼容。如果您已经安装了多个版本，您需要使用 xcode-select 切换到正确的版本。然后关闭 Kotlin 多平台项目，调用 Invalidate cache 并重启。**

*现在我们将从非协同程序代码中删除所有的 freeze()块:*

*从我们在 NSUrlSession 中使用的所有参数中删除所有的 freeze()。记住，我们处理的是本地网络客户端:*

*我们还需要从 AtomicReference 切换到 FreezableAtomicReference:*

*将更改应用于代码:*

*我们的代码是干净和新鲜的，我们的应用程序在飞，尽管 GC 仍然不能完美地工作。
现在让我们调整一下协程示例:*

*我们将使用默认可用的标准调度程序。为了检查全局队列，我们需要从 ioDispatcher 输出一个关于协程上下文的信息。*

*现在，我们从流和通道中移除所有的冻结()。*

*它工作得又好又快。不要忘记在主线程中发送答案:*

**重要！为了防止 iOS 端的内存泄漏，在 autoreleasepool* 中用 lot Swift/ObjC 包装块会很有用*

*我们来查几个案例。我们将从 MainScope 发出请求，并使用 *newSingleThreadContext* 指定一些其他后台调度程序:*

*工作没有任何麻烦。似乎新的内存管理模型将是所有开发人员的完美解决方案，并简化我们的工作。*

*但是！这可能是目前不支持新 mm 的库的一些问题。在某些情况下，可能会出现*invalidmatabilityexception*或 *FreezingException* 。
为了处理它们和*科特林 1.6.0-M1* 或更新版本我们必须禁用嵌入式冻结:*

*更多信息请点击此处:[https://github . com/JetBrains/kot Lin/blob/master/kot Lin-native/NEW _ mm . MD](https://github.com/JetBrains/kotlin/blob/master/kotlin-native/NEW_MM.md)*

*部分片段示例:
[https://github . com/anioutkazharkova/kot Lin _ native _ network _ client/tree/feature/1.6-kn/sample](https://github.com/anioutkazharkova/kotlin_native_network_client/tree/feature/1.6-kn/sample)*