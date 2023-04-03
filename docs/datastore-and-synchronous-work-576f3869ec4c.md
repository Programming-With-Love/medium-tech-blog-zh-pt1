# 数据存储和同步工作

> 原文：<https://medium.com/androiddevelopers/datastore-and-synchronous-work-576f3869ec4c?source=collection_archive---------7----------------------->

![](img/2299991abb14db357c1c152c75cb62f3.png)

在我们的 [**Jetpack 数据存储系列**](/androiddevelopers/introduction-to-jetpack-datastore-3dc8d74139e7) 的以下帖子中，我们将涵盖几个额外的概念，以了解数据存储如何与其他 API 交互，以便您可以在**生产环境**中使用它。在这篇文章中，我们将重点关注与数据存储库的**同步工作。我们将参考 [**首选项**](https://developer.android.com/codelabs/android-preferences-datastore#0) **codelab** ，获取代码示例。**

# 做同步工作

在整个系列中，我们提到了 DataStore 的**完全异步 API** ，来自其内部使用的 **Kotlin 协程和流**。为了防止在 UI 线程上执行较重的 I/O 操作时发生潜在的 ANRs 和 UI jank，数据存储**不提供随时可用的同步支持**。DataStore 将其数据集保存在一个文件中，除非另有说明，否则它会在`**Dispatchers.IO**` **、**上执行所有数据操作，从而保持 UI 线程的畅通。这种 API 结构是 DataStore 相对于其前身`SharedPreferences`的主要优势之一，也是您在大多数情况下使用 DataStore 的方式。

然而，如果您发现您的代码需要您与数据存储同步工作，无论是因为对另一个运行在主线程上的 API 的依赖，还是因为您当前的设置需要您为您的 UI 设置检索一些持久化的值，您都可以使用`[**runBlocking()**](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/run-blocking.html)`协程构建器来同步读取数据存储**。这将**阻塞调用线程**，直到数据存储返回:**

**如果您发现自己处于需要使用这种方法的情况，请花些时间弄清楚是否绝对有必要**阻塞主线程**。考虑如何使用提供的**数据存储异步替代方案**或重构当前代码以避免`runBlocking()`，例如通过异步预加载数据:**

**如果这是不可能的，确保你用错误处理、[取消和超时](https://kotlinlang.org/docs/cancellation-and-timeouts.html)或者一些漂亮的视觉效果来覆盖所有潜在的 UI jank 场景，让用户体验尽可能的流畅。**

# **待续**

**我们已经介绍了如何对数据存储执行**同步工作。**虽然这不是推荐的数据存储方法，但是如果您当前的设置确实需要您对它进行同步调用，您可以结合使用`.runBlocking()`和`.first()`操作符。**

**加入我们系列的下一篇文章，我们将探讨如何进行**数据存储到数据存储的迁移**。**

**您可以在这里找到我们的 Jetpack DataStore 系列的所有帖子:
[Jetpack DataStore 简介](/androiddevelopers/introduction-to-jetpack-datastore-3dc8d74139e7)
[所有关于首选项 DataStore](/androiddevelopers/all-about-preferences-datastore-cc7995679334)
[所有关于 Proto DataStore](/androiddevelopers/all-about-proto-datastore-1b1af6cd2879)
[DataStore 和依赖注入](/androiddevelopers/datastore-and-dependency-injection-ea32b95704e3)
[DataStore 和 Kotlin 序列化](/androiddevelopers/datastore-and-kotlin-serialization-8b25bf0be66c)
[DataStore 和同步工作](/androiddevelopers/datastore-and-synchronous-work-576f3869ec4c)
[DataStore 和数据迁移](/androiddevelopers/datastore-and-data-migration-fdca806eb1aa)
[DataStore 和测试](/androiddevelopers/datastore-and-testing-edf7ae8df3d8)**