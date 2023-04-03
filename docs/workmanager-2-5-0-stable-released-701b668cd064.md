# 在多进程应用中使用工作管理器

> 原文：<https://medium.com/androiddevelopers/workmanager-2-5-0-stable-released-701b668cd064?source=collection_archive---------3----------------------->

![](img/04f37d6cf32867fc471946c8f217ac5d.png)

**📝最近发布的 WorkManager 2.5.0 支持在多进程环境中更轻松地使用，并提供了多项稳定性改进。**

因此，如果你有一个管理多个进程的应用程序，并且你需要一种健壮的方式来管理后台工作(*不再有初始化错误* ️️⚠)，那么这就是为你发布的版本。

您需要对代码进行一些更改，所以请继续阅读以了解更多信息。

> 在本文的最后，我还将列出这个版本的 WorkManager 库中的一些其他行为变化和最新添加的内容。

# 简介:工作-多重处理

新的多进程工件通过将作业调度统一到单个进程中来提高性能。若要开始，请将其添加到您的应用中。

```
Implementation "androidx.work:work-multiprocess:2.5.0"
```

现在您可以[选择](https://developer.android.com/topic/libraries/architecture/workmanager/advanced/custom-configuration)工作管理器用来将工作请求排队的指定进程，并运行其进程内调度程序。

使用`Configuration.Provider`的配置可以是这样的。

```
class MyApplication() : Application(), Configuration.Provider {  override fun getWorkManagerConfiguration() =
    Configuration.Builder()
      .setDefaultProcessName("***com.example:remote***")
      .build()
}
```

> **注意**:`setProcessName`上的参数要求您传递完全限定的进程名，包括您的应用程序的包名，后跟一个冒号，然后是主机的进程名，例如`com.example:remote`。

当使用工作多进程时，你也想使用`RemoteWorkManager`而不是`WorkManager`来管理你的工作请求。 [RemoteWorkManager](https://developer.android.com/reference/androidx/work/multiprocess/RemoteWorkManager) 将总是联系指定的进程来为您排队工作。这可以确保您不会在调用过程中意外初始化新的工作管理器。进程内调度程序也在同一个指定的进程中运行。

## **好处**

通过像这样配置 WorkManager 并使用 RemoteWorkManager 来调度您的作业，您可以在您的多进程应用程序中更快、更可靠地管理您的工作。这是因为 SQLite [争用](https://en.wikipedia.org/wiki/Resource_contention)可以大大减少(因为我们不再依赖基于文件的锁),并且跨进程的作业协调不再是必要的，因为您的应用程序将只有一个工作管理器实例在您可以指定的进程中运行。

# 行为改变🔀

## **工作对帐**

以前，当`ActivityManager`无法实例化`JobService`来启动一个作业时，该作业会因为平台中的一个底层错误而被悄悄丢弃。WorkManager 现在可以确保当通过协调`WorkRequest`对象和作业来创建`Application`实例时，每个`WorkRequest`都有一个后备调度程序作业。

## **限制内部数据库增长**

我们看到应用崩溃的原因之一是设备存储不足。这主要发生在存储容量低的设备上。然而，当应用程序为**安排了大量**工作时，WorkManager 对设备的低存储运行负有部分责任。

> 默认情况下，已完成的作业会在内部 WorkManager 数据库中记录 7 天。这个时间已经减少到 1 天，这大大减小了数据库的大小。

虽然我们缩短了缓冲持续时间，但是您可以通过使用`keepResultsForAtLeast()` API 来控制您的作业应该被记住多长时间。

# ✨新测试 API

如果您将`ListenableFuture`与 WorkManager 一起使用，那么测试会变得更容易——`TestListenableWorkerBuilder`kot Lin 扩展现在接受任何扩展`ListenableWorker`的类，在测试过程中为您提供更多的灵活性。

# 错误修复🐛

除了新功能之外，此版本还包含几个错误修复，可提高 WorkManager 的稳定性、可靠性和性能。您可以在[发行说明](https://developer.android.com/jetpack/androidx/releases/work#2.5.0)中阅读所有变更以及已修复的错误。

# 您可以做些什么来改进工作管理器

## **通过 GitHub 贡献给工作管理器👩‍💻**

WorkManager 以及其他几个 Jetpack 库可以通过 GitHub 接受投稿。

[Alan Viverette](https://medium.com/u/d42e45d76c8a?source=post_page-----701b668cd064--------------------------------) 写了一篇[全面的博文](/androiddevelopers/introducing-jetpack-on-github-c2c9f12e62a9)讲述了整个过程。

## **出问题时请告诉我们📝**

2.5.0 版本中修复的大多数错误都是通过[公共问题追踪器](https://issuetracker.google.com/issues?q=componentid:409906)输入的。

创造一个我们可以解决的问题的最好方法是通过[创造一个我们可以重现的问题](https://issuetracker.google.com/issues/new?component=409906)。为了帮助我们重现问题，我们建议您使用[工作管理器示例](https://github.com/android/architecture-components-samples/tree/main/WorkManagerSample)或者在问题描述中提供您自己的带有说明的示例代码。

现在是开始工作并管理更新应用程序中的库版本的最佳时机。