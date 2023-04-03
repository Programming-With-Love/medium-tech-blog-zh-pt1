# 在 Android 12 上使用工作管理器

> 原文：<https://medium.com/androiddevelopers/using-workmanager-on-android-12-f7d483ca0ecb?source=collection_archive---------0----------------------->

![](img/b7d63d650173c9bb576b591ae7594270.png)

Android 12 (API level 31)引入[前台服务启动限制](https://developer.android.com/about/versions/12/foreground-services)。由于这些限制，针对 Android 12 或更高版本的应用程序不再能够在后台运行时启动前台服务，除了[少数特殊情况](https://developer.android.com/guide/components/foreground-services#background-start-restriction-exemptions)。这意味着，如果您的应用程序当前不处于免除后台启动限制的状态，调用`[setForeground](https://developer.android.com/reference/androidx/work/ListenableWorker#setForegroundAsync(androidx.work.ForegroundInfo))`可能会导致[异常](https://developer.android.com/reference/android/app/ForegroundServiceStartNotAllowedException)。

因此，在 WorkManager 2.7 中，我们可以在遵守后台限制的同时轻松安排重要工作。有了[加急作业](https://developer.android.com/about/versions/12/foreground-services#expedited-jobs)，应用程序现在可以轻松地执行**短而高优先级的任务**，比如发送聊天消息或上传图片到社交网络。加速作业是启动任务的推荐方式，即使用户将应用程序放在后台，这些任务也应该立即运行并继续。

要快速安排工作，请使用工作请求生成器中的`setExpedited()`方法:

```
val request = OneTimeWorkRequestBuilder<HighPriorityWorker>()       
   .setExpedited(OutOfQuotaPolicy.RUN_AS_NON_EXPEDITED_WORK_REQUEST)   
   .build() WorkManager.getInstance(context).enqueue(request)
```

调用`setExpedited()`告诉框架这项工作很重要，应该优先于其他调度的工作。注意，我们还向`setExpedited()`传递了一个`[OutOfQuotaPolicy](https://developer.android.com/reference/androidx/work/OutOfQuotaPolicy)`参数。加急作业受到基于[应用待机桶](https://developer.android.com/topic/performance/appstandby)的配额的限制，因此`*OutOfQuotaPolicy*`参数告诉工作管理器，如果你的应用试图在超出配额时运行加急作业，该怎么办:要么完全放弃加急作业请求(`[DROP_WORK_REQUEST](https://developer.android.com/reference/androidx/work/OutOfQuotaPolicy#DROP_WORK_REQUEST)`)，要么退回到将作业视为常规作业请求(`[RUN_AS_NON_EXPEDITED_WORK_REQUEST](https://developer.android.com/reference/androidx/work/OutOfQuotaPolicy#RUN_AS_NON_EXPEDITED_WORK_REQUEST)`)。

> 将配额视为执行加急工作的时间限制。仅仅因为它重要，并不意味着它可以永远运行下去。

WorkManager 2.7 向后兼容，可以在 12 版之前的 Android 上运行。在运行 Android 11 或更老版本的设备上调用`setExpedited()`时，WorkManager 会默认使用前台服务，而不是加急作业。

要查看工作管理器的`setExpedited()` API，请查看我们官方的[工作管理器示例](https://github.com/android/architecture-components-samples/blob/efe4f716c8670f5c3ce5dd9eb252b80d9d4241db/WorkManagerSample/lib/src/main/java/com/example/background/ImageOperations.kt#L94)和关于加速作业的[文档。](https://developer.android.com/topic/libraries/architecture/workmanager/how-to/define-work#expedited-jobs)

你还可以在官方[发行说明](https://developer.android.com/jetpack/androidx/releases/work)中看到每个工作管理器版本中所做的更改和改进的详细列表，以及[工作管理器 2.6](https://developer.android.com/jetpack/androidx/releases/work#2.6.0) 和[工作管理器 2.7](https://developer.android.com/jetpack/androidx/releases/work#2.7.0) 的具体发行说明。

最后，如果您有任何与工作管理器相关的特性请求或问题，请随时[在我们的公共跟踪器](https://issuetracker.google.com/issues/new?component=409906)中提交问题。