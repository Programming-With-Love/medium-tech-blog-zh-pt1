# 工作管理器—kot Lin API

> 原文：<https://medium.com/androiddevelopers/workmanager-kotlin-apis-a0fb9dfbfeb6?source=collection_archive---------0----------------------->

![](img/f54d5257763f1249e0482b52c4b4c9b2.png)

[WorkManager](https://developer.android.com/topic/libraries/architecture/workmanager) 提供了一组 API，可以轻松调度异步任务，以便立即或延迟执行，即使应用程序关闭或设备重启，这些任务也有望运行。对于 Kotlin 用户，WorkManager 为协程提供了一流的支持。在这篇文章中，我将通过构建[工作管理器代码实验室](https://developer.android.com/codelabs/android-workmanager#0)向你展示带有协程的工作管理器的基础知识。所以让我们开始吧！

# 工作管理器的基础知识

WorkManager 库是任何应该继续运行的任务的推荐选择，即使用户导航离开特定屏幕、用户将应用程序置于后台或设备重启。常见任务可能是:

*   上传日志或报告数据
*   将滤镜应用于图像并保存图像
*   定期将本地数据与网络同步

如果当用户离开某个范围(比如屏幕)时，您的即时任务会结束，我们建议您直接使用 Kotlin 协程。

WorkManager codelab 会模糊图像并将结果保存在磁盘上。让我们看看需要什么来实现这一点。

我们添加了`work-runtime-ktx`依赖项。

```
implementation "androidx.work:work-runtime-ktx:$work_version"
```

我们从实现自己的工人类开始。这是我们为您希望在后台执行的实际工作放置代码的地方。您将扩展 Worker 类并覆盖 doWork()方法。因为这是最重要的一门课，我们稍后会详细讲解。下面是初始实现的样子。

然后，我们构建我们的工作请求，在我们的例子中，我们希望只执行一次工作，所以我们使用一个`[OneTimeWorkRequest.Builder](https://developer.android.com/reference/androidx/work/OneTimeWorkRequest.Builder)`。作为输入，我们设置我们想要模糊的图像的`Uri`。

Kotlin 提示:要创建输入数据，我们可以使用创建数据构建器的`[workDataOf](https://developer.android.com/reference/kotlin/androidx/work/package-summary#workdataof)`函数，放置键-值对并为我们创建数据。

为了调度工作并使其运行，我们使用 WorkManager 类。我们可以提供要完成的任务以及对这些任务的约束。

# 让工人做这项工作

当你使用一个`[Worker](https://developer.android.com/reference/androidx/work/Worker?hl=en)`时，WorkManager 会自动在后台线程上调用`Worker.doWork()`。从`doWork()`返回的`Result`通知工作管理器服务工作是否成功，如果失败，是否应该重试工作。

`Worker.doWork()`是一个同步调用——你应该以一种阻塞的方式完成所有的后台工作，并在方法退出时完成。如果您在 doWork()中调用异步 API 并返回结果，您的回调可能无法正常运行。

# 但是如果我们想做异步工作呢？

让我们把我们的例子复杂化，假设我们想保存数据库中所有模糊文件的 uri。

为此我创造了:

*   一个简单的模糊图像实体
*   用于插入和获取图像的 dao 类
*   数据库

点击查看实现[。](https://github.com/googlecodelabs/android-workmanager/pull/213)

如果你必须做异步工作，比如在数据库中保存数据或者做网络请求，在 Kotlin 中，我们推荐使用`[CoroutineWorker](https://developer.android.com/reference/kotlin/androidx/work/CoroutineWorker)`。

协同工作器允许我们使用 Kotlin 协同程序进行异步工作。

`doWork()`方法是一种`suspend`方法。这意味着我们可以很容易地在这里调用我们的悬浮道。

默认情况下`doWork()`使用`Dispatchers.Default`。您可以用您需要的调度程序覆盖它。在我们的例子中，我们不需要这样做，因为 Room 已经将插入工作转移到了不同的调度程序上。查看 Kotlin APIs 发布的房间以了解更多细节。

开始使用`CoroutineWorker`执行即使用户关闭你的应用也需要完成的异步工作。

如果您想了解更多关于 WorkManager 的知识，请继续关注未来的系列文章，专门深入探讨它。在此之前，请查看我们的代码实验室和文档:

*   [工作管理器文档](https://developer.android.com/topic/libraries/architecture/workmanager/)
*   [使用工作管理器](https://developer.android.com/codelabs/android-workmanager#0) codelab
*   [高级工作管理器](https://developer.android.com/codelabs/android-adv-workmanager#0)代码实验室