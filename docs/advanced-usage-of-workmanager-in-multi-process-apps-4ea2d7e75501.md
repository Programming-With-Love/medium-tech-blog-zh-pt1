# WorkManager 在多进程应用中的高级用法

> 原文：<https://medium.com/androiddevelopers/advanced-usage-of-workmanager-in-multi-process-apps-4ea2d7e75501?source=collection_archive---------8----------------------->

![](img/b7d63d650173c9bb576b591ae7594270.png)

在 [WorkManager 2.5](/androiddevelopers/workmanager-2-5-0-stable-released-701b668cd064) 中，我们让多进程应用更容易接触到在指定进程中运行的特定工作管理器实例。

现在，在 WorkManager 2.6 中，我们更进了一步，增加了对 worker 在任何进程中运行的支持，并允许 worker 绑定到特定的进程。多进程支持对于需要在多个进程中运行工作者的应用程序特别有用。大多数应用程序在单个进程中运行良好。但是那些需要不止一个流程的企业过去很难管理流程之间的工作。直到现在！

从 WorkManager 2.6 开始，您可以使用 [RemoteListenableWorker](https://developer.android.com/topic/libraries/architecture/workmanager/advanced/listenableworker#remotelistenableworker) 或 [RemoteCoroutineWorker](https://developer.android.com/topic/libraries/architecture/workmanager/advanced/coroutineworker#remotecoroutineworker) 将一个工人绑定到一个特定的进程。对于在 Kotlin 中实现的 Worker，应该使用 RemoteCoroutineWorker。否则，您应该使用 RemoteListenableWorker。在本文中，我们将演示一个用 Kotlin 实现的 Worker，但是对于类似的 Java 实现，请查看下面链接的示例。

可以像[协同工作器](https://developer.android.com/topic/libraries/architecture/workmanager/advanced/coroutineworker)一样实现 RemoteCoroutineWorker。但是，您没有覆盖“doWork ”,而是覆盖了“do **Remote** Work ”,然后绑定到一个指定的进程，在构建工作请求时，提供两个额外的参数作为输入数据的一部分:ARGUMENT_CLASS_NAME 和 ARGUMENT_PACKAGE_NAME:

对于每个 RemoteWorkerService，您还需要在 AndroidManifest 中添加服务定义，如下所示:

要查看这些新功能，请查看新的[工作管理器多进程示例](https://github.com/android/architecture-components-samples/tree/main/WorkManagerMultiprocessSample)，它同时利用了 RemoteCoroutineWorker 和 RemoteListenableWorker。

您还可以在我们的发行说明[这里](https://developer.android.com/jetpack/androidx/releases/work#version_260_2)中看到我们在 WorkManager 2.6 中所做的更改和改进的详细列表。

最后，如果您有任何与工作管理器相关的功能请求或问题，请随时在我们的公共跟踪器中[提交问题。](https://issuetracker.google.com/issues/new?component=409906)