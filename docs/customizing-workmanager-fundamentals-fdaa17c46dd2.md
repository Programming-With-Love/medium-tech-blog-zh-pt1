# 自定义工作管理器—基础

> 原文：<https://medium.com/androiddevelopers/customizing-workmanager-fundamentals-fdaa17c46dd2?source=collection_archive---------0----------------------->

![](img/4e277e9d51988f5c5dfbd1e500de6c9c.png)

Illustration by [Virginia Poltrack](https://twitter.com/VPoltrack)

欢迎来到我们的工作管理器系列的第五篇文章。 [WorkManager](https://developer.android.com/topic/libraries/architecture/workmanager/) 是一个 [Android Jetpack](https://developer.android.com/jetpack/) 库，当工作的约束得到满足时，它运行可推迟的、有保证的后台工作。这是目前在 Android 上进行此类工作的最佳实践。

如果您一直在关注，我们已经讨论了:

*   [什么是工作管理器，何时使用工作管理器](/androiddevelopers/introducing-workmanager-2083bcfc4712)
*   [如何使用工作管理器 API 调度工作](/androiddevelopers/workmanager-basics-beba51e94048)
*   [工作管理器和科特林](/androiddevelopers/workmanager-meets-kotlin-b9ad02f7405e)
*   [工作管理器周期](/androiddevelopers/workmanager-periodicity-ff35185ff006)

在本文中，我们将讨论定制配置，包括:

*   为什么我们可能需要定制配置
*   如何定义自定义配置
*   什么是`WorkerFactory`,为什么我们需要定制的
*   什么是`DelegatingWorkerFactory`

[第六篇博文将这些概念扩展到了依赖注入，特别是 Dagger](/androiddevelopers/customizing-workmanager-with-dagger-1029688c0978)，涵盖了:

*   用匕首给我们的`WorkerFactory`注入参数
*   按需初始化

这两篇文章是有联系的，第二篇文章需要这篇文章中介绍的知识。

# 陈述问题

使用工作管理器时，定义`[Worker](https://developer.android.com/reference/androidx/work/Worker)` / `[CoroutineWorker](https://developer.android.com/reference/kotlin/androidx/work/CoroutineWorker)`或任何其他`[ListenableWorker](https://developer.android.com/reference/androidx/work/ListenableWorker)`派生类是你的责任。WorkManager 在适当的时候实例化您的工人，独立于让您的应用程序在前台运行或根本不运行。为了实例化你的工人类，WorkManager 使用了一个`[WorkerFactory](https://developer.android.com/reference/androidx/work/WorkerFactory)`。

默认`WorkerFactory`创建的工人只能访问 2 个参数:

*   应用程序的`Context`
*   `[WorkerParameters](https://developer.android.com/reference/androidx/work/WorkerParameters)`

如果您需要将额外的参数传递给 worker 的构造函数，您需要一个自定义的`WorkerFactory`。

> ***出于好奇*** *:我们说默认* `*WorkerFactory*` *使用反射来实例化右边的* `*ListenableWorker*` *类。如果我们的工人类名被 R8(或 ProGuard)最小化，这可能会失败。为了避免这种情况，WorkManager 包含了一个* `[*proguard-rules.pro*](https://cs.android.com/androidx/platform/frameworks/support/+/androidx-master-dev:work/workmanager/proguard-rules.pro?q=workmanager%20proguard&ss=androidx)` *文件，可以避免混淆您的* `*Worker*` *类名。*

# 自定义配置和工人工厂

WorkManager 类遵循[单例模式](https://en.wikipedia.org/wiki/Singleton_pattern)，只能在实例化之前配置。这意味着，如果您想要自定义配置，您需要首先禁用默认配置。

如果您尝试使用`[initialize()](https://developer.android.com/reference/androidx/work/WorkManager#initialize(android.content.Context,%20androidx.work.Configuration))` 方法第二次初始化工作管理器，将会抛出一个异常(在 [v1.0.0](https://developer.android.com/jetpack/androidx/releases/work#1.0.0-alpha11) 中添加)。要避免这种情况，请禁用默认初始化。然后，您可以在应用程序的`onCreate`方法中配置和初始化 WorkManager。

在 [v2.1.0](https://developer.android.com/jetpack/androidx/releases/work#2.1.0-alpha01) 中增加了一个更新、更好的初始化工作管理器的方法。您可以通过在您的`Application`类中实现 WorkManager 的`[Configuration.Provider](https://developer.android.com/reference/androidx/work/Configuration.Provider)`接口来使用一个**按需初始化**。然后你只需要使用`[getInstance(context)](https://developer.android.com/reference/androidx/work/WorkManager#getInstance(android.content.Context))`获得实例，工作管理器将使用你的定制配置初始化工作管理器。

## 可配置参数

正如我们已经说过的，您可以配置用于创建工人的`WorkerFactory`，但是您也可以定制其他参数。参数的完整列表在`[Configuration.Builder](https://developer.android.com/reference/androidx/work/Configuration.Builder)`的工作管理器参考指南中。这里我想调用两个额外的参数:

*   记录级别
*   `JobId`范围

当我们需要了解 WorkManager 的情况时，修改日志记录级别会很方便。我们有一个关于这个主题的文档页面。您可以查看一下[Advanced work manager code lab](https://codelabs.developers.google.com/codelabs/android-adv-workmanager/#0)，看看这是如何在一个真实的示例中实现的，以及您可以获得什么样的信息。

如果我们在应用程序中使用 WorkManager 和 JobScheduler API，我们可能希望定制`JobId`范围。在这种情况下，您希望避免在两个地方使用相同的`JobId`范围。还有一个新的 [Lint 规则](https://android.googlesource.com/platform/frameworks/support/+/androidx-master-dev/work/workmanager-lint/src/main/java/androidx/work/lint/SpecifyJobSchedulerIdRangeIssueDetector.kt)涵盖了 v2.4.0 中引入的这种情况。

# 工作经理的工厂

我们已经知道 WorkManager 有一个默认的`WorkerFactory`，它使用反射来根据我们在`WorkRequest`中传递的`Worker`类名找到要实例化的类。

> ⚠️ *如果你创建了一个* `*WorkRequest*` *然后你用一个不同的类名称为你的工人重构应用程序，WorkManager 将无法找到正确的类，并将抛出一个* `*ClassNotFoundException*` *。*

您可能希望向 worker 的构造函数添加其他参数。想象一下，有一个工作人员希望引用一个需要与远程服务器通信的改进服务:

如果我们对应用程序进行这种更改，它仍然可以编译，但是，一旦我们执行它，并且 WorkManager 尝试实例化这个`CoroutineWorker`类，应用程序将关闭并出现异常，抱怨无法找到正确的 init 方法来实例化:

```
Caused by java.lang.NoSuchMethodException: <init> [class android.content.Context, class androidx.work.WorkerParameters]
```

我们需要一个定制的`WorkerFactory`！

但是，不要这么快:我们已经看到，有几个步骤。让我们回顾一下我们必须做的事情，然后深入了解每个项目的细节:

1.  禁用默认初始化
2.  实现自定义`WorkerFactory`
3.  创建自定义配置
4.  初始化工作管理器

## 禁用默认初始化

如[工作管理器文档](https://developer.android.com/topic/libraries/architecture/workmanager/advanced/custom-configuration#remove-default)所述，必须在`AndroidManifest.xml`文件中禁用，默认情况下从工作管理器库中移除自动合并的节点。

## 实现自定义 WorkerFactory

我们现在需要编写自己的工厂，用正确的参数创建我们的工人:

## 创建自定义工作人员配置

接下来，我们必须在工作管理器的定制配置中注册我们的工厂:

## 初始化工作管理器

如果您的应用程序中只有一个 Worker 类类型，这就是您所需要的。如果你有不止一个，或者你期望将来有更多，更好的解决方案是使用 [v2.1](https://developer.android.com/jetpack/androidx/releases/work#2.1.0) 中引入的`[DelegatingWorkerFactory](https://developer.android.com/reference/androidx/work/DelegatingWorkerFactory)`。

# 委派工人工厂

我们可以使用一个`DelegatingWorkerFactory`并使用它的`[addFactory()](https://developer.android.com/reference/androidx/work/DelegatingWorkerFactory#addFactory(androidx.work.WorkerFactory))`方法向它添加我们自己的`WorkerFactory`,而不是配置 WorkManager 来直接使用我们的工厂。然后你可以有多个工厂，每个工厂照顾一个或多个工人。用`DelegatingWorkerFactory`注册你的工厂，它会负责协调多个工厂。

在这种情况下，您的工厂需要检查作为参数传递的`workerClassName`是否是它知道如何处理的东西。如果没有，它返回`null`并且`DelegatingWorkerFactory`将移动到下一个注册的工厂。如果注册的工厂都不知道如何处理一个类，那么它将退回到使用反射的默认工厂。

下面是我们的工厂修改后返回的`null`，如果它不知道如何处理一个`workerClassName`:

我们的工作管理器配置变成:

如果您有不止一个需要不同参数的`Worker`，您可以创建第二个工厂并再次调用`addFactory`添加它。

# 结论

WorkManager 是一个强大的库，能够用它的默认配置覆盖很多常见的用例。但是，在某些情况下，您需要提高其调试级别，或者需要向您的工作人员传递附加参数。在这些情况下，您需要自定义配置。

我希望这篇文章已经让你很好地概述了这个主题，如果你有任何问题，请在评论中告诉我，或者直接在 twitter 上联系我，地址是 [pfmaggi@](https://twitter.com/pfmaggi) 。

下一篇文章将介绍如何在工作管理器定制配置中使用 Dagger:“[使用 Dagger 定制工作管理器](/androiddevelopers/customizing-workmanager-with-dagger-1029688c0978)”。