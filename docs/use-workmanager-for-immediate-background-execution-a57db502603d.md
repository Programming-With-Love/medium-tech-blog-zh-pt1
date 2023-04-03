# 使用工作管理器进行即时后台执行

> 原文：<https://medium.com/androiddevelopers/use-workmanager-for-immediate-background-execution-a57db502603d?source=collection_archive---------0----------------------->

![](img/9d75749f53126902cc9971fc5738b8f1.png)

Header image by [Virginia Poltrack](https://medium.com/u/224e59676537?source=post_page-----a57db502603d----------------------)

## 有些任务不应该被推迟

当你必须在后台执行长时间运行的任务时，你会遇到 Android 8.0 引入的后台执行限制。这些限制激励开发人员改善用户对整个平台的体验。
为了更容易适应不同的用例，我们还通过向 WorkManager 添加功能，改善了开发人员在处理后台限制时的体验。

> **我们建议您使用工作管理器执行长时间运行的*即时*任务**。

继续学习，了解使用 WorkManager 立即执行长时间运行的任务的好处，以及如何设置一切。

# API 简介

从 WorkManager [版本 2.3.0](https://developer.android.com/jetpack/androidx/releases/work#version_230_3) 开始，每个工作者都可以访问在前台服务中运行任务的方法。Worker 基类`[ListenableWorker](https://developer.android.com/reference/androidx/work/ListenableWorker)`提供了一个新的`[setForegroundAsync()](https://developer.android.com/reference/kotlin/androidx/work/ListenableWorker#setforegroundasync)`函数。

这篇文章使用`[CoroutineWorker](https://developer.android.com/reference/kotlin/androidx/work/CoroutineWorker)`作为示范。在协同工作器中，`setForegroundAsync()`被包装在一个挂起的`[setForeground()](https://developer.android.com/reference/kotlin/androidx/work/CoroutineWorker#setforeground)`函数中。该类还提供了挂起的`doWork`函数，该函数允许在主线程之外运行代码。但是这篇文章的所有内容也适用于其他工人阶级的相应功能。

当您使用 setForeground(Async)时，一旦满足约束，计划的任务将立即在前台服务中运行。另外，WorkManager 会为您处理服务的生命周期。并且，后台工作的 10 分钟时间限制不适用于您在前台服务中运行的 worker 中所做的工作。

# 立即开始执行

让我们看看如何让一个现有的工人在前台服务中执行工作。

我们的出发点是一个非常简化的`doWork()`函数。代码异步执行，根据成功与否，返回对应的`Result`。

在`doWork()`中，您还将告诉 WorkManager 任务应该立即在前台服务中运行。

为此，您必须创建一个`[ForegroundInfo](https://developer.android.com/reference/androidx/work/ForegroundInfo)`对象并将其提供给`setForeground()`。ForegroundInfo 接受一个通知 id 以及将作为参数显示的`[Notification](https://developer.android.com/reference/android/app/Notification)`。
一旦满足约束，该信息用于设置和运行前台服务。

# 设置前景信息

正确设置 ForegroundInfo 就像 1，2，3:

1.  创建通知
2.  创建通知渠道
3.  向 ForegroundInfo 提供通知

在下面的代码中，`createForegroundInfo()`调用`createNotification()`，后者依次填充通知并创建相应的通道。

# 在前台服务中运行工作

现在让我们把东西放在一起。因为我们已经有了一个实现的`doWork()`函数，我们可以调用`setForeground()`并通过调用`createForegroundInfo()`传递所需的信息。

> ⚠️⚠️⚠️
> 在你的长期运行任务开始之前调用`setForeground()` **。** 否则，您的员工将被视为非前台服务，直到`setForeground()`被调用，这可能会导致不想要的结果，如工作被取消。
> ⚠️⚠️⚠️

# 🐾后续步骤

既然你已经知道了何时以及如何使用长时间运行的工作者，你就可以开始在你的应用中实现它们了。

👩‍💻👨🏽‍💻要在示例中查看这一点，请查看 GitHub 上的[工作管理器示例。在前台服务中运行工作的代码可以在](https://github.com/android/architecture-components-samples/tree/master/WorkManagerSample) [BaseFilterWorker](https://github.com/android/architecture-components-samples/blob/master/WorkManagerSample/lib/src/main/java/com/example/background/workers/BaseFilterWorker.kt) 类和 [this commit](https://github.com/android/architecture-components-samples/commit/160f148b5ea4c943028c73acd4667fd134a8674e) 中找到。

🔖关于长时间运行的工人和前台服务的详细指南，请看一下[长时间运行工人的高级工作管理器指南](https://developer.android.com/topic/libraries/architecture/workmanager/advanced/long-running)。

🦮请务必查看更新的[后台处理指南](https://developer.android.com/guide/background)并通读 Android 上的 [Kotlin 协同程序](https://developer.android.com/kotlin/coroutines)以了解更多关于该主题的信息。

📚为了了解更多关于工作管理器的信息，[林珈安](/@lylalyla)和[皮埃特罗](/@pmaggi)创建了一个[博客系列](/androiddevelopers/introducing-workmanager-2083bcfc4712)来指导你从工作管理器的基础到高级特性。

🐛在 [Google IssueTracker](http://goo.gle/workmanager-issue) 上报告你面临的任何问题。这将有助于我们对新出现的功能和错误修复进行优先排序。

📝请在下面的评论中或者在推特上让我知道运行即时任务对你有什么帮助。