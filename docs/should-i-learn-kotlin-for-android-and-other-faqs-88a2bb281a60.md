# 我应该为 Android 和其他常见问题学习 Kotlin 吗

> 原文：<https://medium.com/androiddevelopers/should-i-learn-kotlin-for-android-and-other-faqs-88a2bb281a60?source=collection_archive---------5----------------------->

![](img/fd5a81d754516ffe32ebb9aa1cda5d3d.png)

自从我们在 2017 年宣布支持 Kotlin 以来，我们已经收到了很多关于 Android 上 Kotlin 的问题:你想知道是否是时候学习它，将它引入你的应用程序，学习 Kotlin 的最佳课程或教程是什么，谷歌是否在内部使用 Kotlin，以及我们对 Java 编程语言的计划是什么。在这篇文章中，我想回答其中的一些问题。

# 问:我应该为 Android 学习 Kotlin 吗？

我们最常遇到的问题都在同一条线上:

*   *“对于一个初学者来说，在 Kotlin 和 Java 编程语言之间应该选择学习什么？”*
*   *“我已经完成了 Java 编程语言的基础知识，现在是我转用 Kotlin 进行 Android 开发的时候了吗？”*
*   *“对于一个想要学习 Android 开发的老 Java 开发者，你推荐直接去 Kotlin 还是学习 Java？”*

简短回答:

> 是啊！开始学习和使用 Kotlin 吧！

长回答:

# 科特林和安卓

2017 年，我们在 Google I/O 上宣布了 Kotlin 支持。那时，我们开始朝着确保我们的 API、文档和样本对 Kotlin 友好迈出了第一步。2019 年，Android 成为 Kotlin-first，因此我们开始更加依赖 Kotlin 的功能。例如，协程成为我们推荐的运行异步工作的解决方案。以下是我们所做的其他工作:

## 科特林第一图书馆

我们首先为我们的几个 Android Jetpack APIs(如 Room、LiveData、ViewModel 和 WorkManager)添加了对 Kotlin 协同例程的一流支持，改变了我们在 Android 上进行异步操作的方式。Firebase Android SDK 和许多 Jetpack 库都有 [Kotlin 扩展库](https://developer.android.com/kotlin/ktx) (KTX ),使得在 Kotlin 中使用它们更加流畅。

现在，我们的许多库，如 Paging 3.0 和 DataStore，都是 Kotlin 优先的。Jetpack Compose ，我们新的、非捆绑的、声明式 UI 工具包，是用 Kotlin 从头开始编写的。

## 工具作业

开发生产力来自于优秀的工具。因此，我们已经为 Kotlin 对编译工具链进行了许多改进，包括 Kotlin JVM 编译器的增强、Kotlin 特定的 R8 优化，甚至开发像 [Kotlin 符号处理](http://goo.gle/ksp)这样的新工具。我们添加了内置的 Android Kotlin Live 模板，允许您使用速记将常见的 Android 结构添加到您的 Kotlin 应用程序中。同时，新的特定于 Kotlin 的 Lint 检查有助于使 Kotlin 代码更符合语言习惯。这在您从 Java 编程语言过渡到 Kotlin 时尤其有用。

# 问:谷歌在内部使用 Kotlin 吗？

在谷歌内部，我们也在加倍押注科特林。我们的 60 多个应用程序(如 Google Home、Drive、Maps 等)已经将 Kotlin 添加到它们的代码库中。我们庞大的内部代码库包含超过 200 万行 Kotlin 代码。

# 问:我应该把我的应用程序迁移到 Kotlin 吗？

我们经常会得到这个问题，但这里的答案取决于你。如果您对当前的代码库和技术栈满意，能够熟练地使用您的解决方案来管理异步任务，并且有一种有效的方法来捕捉错误，那么迁移可能并不适合您

如果你喜欢你所看到的 Kotlin，无论是尝试它还是通过下面提到的一些课程学习该语言，并且你还想利用最新的 Jetpack APIs，那么你应该考虑将 Kotlin 添加到你的应用程序中。Kotlin 的优势之一是它与 Java 编程语言的良好互操作性。你可以采取小的、渐进的步骤来采用它——也许首先在测试中尝试它，然后在新的特性上，然后你可以在接触它的时候尝试转换一些旧的代码。

为了向惯用的 Kotlin 迈出第一步，请查看我们的[转换为 Kotlin codelab](https://codelabs.developers.google.com/codelabs/java-to-kotlin#0) 。

# 问:Android 中的 Java 编程语言呢？

除了 Java 之外，我们还添加了 Kotlin 支持，因为它们都编译成相同的字节码，并且可以共存。我们喜欢 Kotlin，因为它表达性强，编写代码的方式更安全。我们继续[维护和发展我们的 Java 支持](https://developer.android.com/studio/write/java8-support)。例如，在 Android 11 中，我们添加了对从较新的 OpenJDK 版本到版本 13 的许多 API 的支持，Android Studio 甚至允许您在所有 Android 设备上使用其中一些 API，而不管它们的操作系统版本如何。点击阅读更多关于支持新语言 API[的信息。](/androiddevelopers/support-for-newer-java-language-apis-bca79fc8ef65)

# 问:学习科特林的最好方法是什么？

采用一种新语言不是一件容易的事，但我们正努力让它变得尽可能简单:

*   从培训[课程](https://developer.android.com/kotlin/campaign/learn)开始——它们面向所有级别的开发人员，从初学者到专业人员，将帮助你提高你的 kot Lin Android 技能，从[kot Lin 中的 Android 基础知识](https://developer.android.com/courses/android-basics-kotlin/course)，一个面向没有编程经验的人的新在线课程**，到教你如何使用协程的高级教程。**
*   **我们所有的文档页面都包含 Kotlin 代码片段，所以你可以很容易地比较我们的 API 在两种语言中是如何工作的，并且我们所有的[示例](http://github.com/android)都有 Kotlin 版本。**
*   **查看我们的[文章](http://goo.gle/kotlin-posts)和[视频](http://goo.gle/kotlin-videos)，这些文章和视频教你广泛的 Kotlin 主题。**
*   **在我们的[developers.android.com/kotlin](http://developers.android.com/kotlin)页面上，阅读给想要[切换到 Kotlin](https://developer.android.com/kotlin/add-kotlin) 的[开发者](https://developer.android.com/kotlin/learn)和[团队](https://developer.android.com/kotlin/adopt-for-large-teams)的指南。**

**自从 3 年前正式增加对 Kotlin 的支持以来，我们一直在加强对这种令人敬畏的语言和生态系统的支持。与 JetBrains 一起，我们已经为 Kotlin 建立了一个基础，以确保该语言能够很好地老化，例如通过一个仔细的过程来审查突破性的变化。我们的贡献不止于此:谷歌有一个为 Kotlin 编译器做贡献的工程师团队，这是他们的全职工作，我们正在构建的 Jetpack APIs 不仅支持 Kotlin，而且它们是 Kotlin 优先的，我们致力于让 Android 上的 Kotlin 成为无缝体验。**

***Java 是甲骨文和/或其附属公司的注册商标。***