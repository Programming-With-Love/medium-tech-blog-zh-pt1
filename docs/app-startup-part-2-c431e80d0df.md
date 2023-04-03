# 应用程序启动，第 2 部分

> 原文：<https://medium.com/androiddevelopers/app-startup-part-2-c431e80d0df?source=collection_archive---------1----------------------->

![](img/05da256c9e79ffdc719a8dfe94953092.png)

Illustration by [Virginia Poltrack](https://twitter.com/VPoltrack)

## 惰性初始化

在[之前的文章](/androiddevelopers/app-startup-part-1-34f57b65cacd)中，我展示了内容提供者(出现在应用程序的合并清单文件中)如何在启动时自动加载一些库和模块。

在本文中，我将介绍如何使用 AndroidX [应用启动](https://developer.android.com/topic/libraries/app-startup)库来更好地控制何时以及如何加载这些库。也许，仅仅是也许，我们会看到如何在应用程序启动时节省时间。

# 使用应用程序启动库自动初始化

使用应用程序启动的最简单方法是隐式地使用它的内容提供者来初始化其他库。通过告诉应用程序启动如何初始化这些其他库，并从合并的清单中删除它们的内容提供者，可以做到这一点。这实质上将所有这些独立的内容提供者减少到一个用于应用启动的单一内容提供者，该内容提供者用于加载启动库，然后加载其他所有内容。

您可以通过三个步骤来完成所有这些:在 build.gradle 文件中添加 App Startup 作为依赖项，为每个需要初始化的库创建一个初始化器，并向 Manifest.xml 文件添加信息。

让我们再次看看我在第 1 部分中使用的 WorkManager 示例。为了通过应用程序启动加载 WorkManager，我首先将应用程序启动添加到我的应用程序的 build.gradle 文件中:

```
implementation “androidx.startup:startup-runtime:1.0.0”
```

接下来我创建了一个初始化器，是 App 启动提供的一个接口:

```
class MyWorkManagerInitializer : Initializer<WorkManager> {
    override fun create(context: Context): WorkManager {
        val configuration = Configuration.Builder().build()
        WorkManager.initialize(context, configuration)
        return WorkManager.getInstance(context)
    } override fun dependencies(): List<Class<out Initializer<*>>> {
        *// No dependencies on other libraries.* return *emptyList*()
    }
}
```

每个初始化器都有两个函数需要重写:`create()`和`dependencies()`。`dependencies()`用于建立初始化多个库的特定顺序。在这种情况下，我不需要那个功能，因为我只处理`WorkManager`。如果你在你的应用中使用几个库，查看[应用启动用户指南](https://developer.android.com/topic/libraries/app-startup)了解使用`dependencies()`的详细信息。

对于`create()`函数，我模仿了我在 [WorkManager 的内容提供者](https://cs.android.com/androidx/platform/frameworks/support/+/androidx-master-dev:work/workmanager/src/main/java/androidx/work/impl/WorkManagerInitializer.java;l=36?q=WorkManagerIniti&sq=&ss=androidx)中看到的内容。

顺便说一下，这往往是使用应用程序启动的这一部分的方式；库的内容提供者负责初始化，所以您通常可以使用该类中的代码作为如何手动完成初始化的提示。如果一些库调用隐藏的或私有的 API，可能会有问题，但幸运的是 WorkManager 没有，所以这适用于我的情况。希望它能为你工作。

最后，我在`Manifest.xml`的`<application>`块中添加了两个提供者标签。首先是这样的:

```
<provider
    android:name="androidx.work.impl.WorkManagerInitializer"
    android:authorities="${applicationId}.workmanager-init"
    android:exported="false"
    tools:node="remove" />
```

这个`WorkManagerInitializer`标签很重要，因为它告诉 Android Studio*移除*自动生成的提供者，该提供者来自于向`build.gradle`文件添加`WorkManager`依赖项。如果没有这个特殊的标签，库将继续在启动时自动初始化，当应用程序启动试图初始化它时，您可能会得到一个错误，因为它已经被初始化了。

这是我添加的第二个提供者标记:

```
<provider
    android:name="androidx.startup.InitializationProvider"
    android:authorities="${applicationId}.androidx-startup"
    android:exported="false"
    tools:node="merge">
    <meta-data     android:name="com.example.startuplibtest.MyWorkManagerInitializer"
    android:value="androidx.startup" />
</provider>
```

这个`InitializationProvider`标签几乎与通过简单地将启动依赖项添加到`build.gradle`文件中而自动生成的标签相同(您可以通过查看合并的清单文件来验证——参见[第 1 部分](/androiddevelopers/app-startup-part-1-34f57b65cacd)了解更多细节)。但是有两个重要的区别:

```
tools:node="merge"
```

这是一个由 Android Studio 管理的清单合并属性。它告诉工具在最终的合并清单中合并这个标签的多个实例。在这种情况下，它会将库依赖项自动生成的`<provider>`与这个版本的提供者合并，因此在最终的合并清单中只有一个。

下一个有趣的行包含元数据:

```
<meta-data     android:name="com.example.startuplibtest.MyWorkManagerInitializer"
    android:value="androidx.startup" />
```

提供者内部的元数据标记告诉应用程序启动库在哪里可以找到您的初始化器代码，它将在启动时运行以初始化库。请注意这种情况的不同之处:当您不使用应用程序启动时，初始化会自动发生，因为 Android 在该库中创建并运行内容提供者，然后它会初始化库本身。但是通过告诉 App Startup 你的初始化器，并从合并的清单中移除`WorkManager`的提供者，你是在告诉 Android 使用 App Startup 的内容提供者来加载`WorkManager`的库。如果您以这种方式初始化多个库，您可以通过这个应用程序启动内容提供者有效地汇集所有这些请求，而不是让每个库创建自己的库。

# 懒惰…如果你喜欢的话

当处理启动性能时，我们不能改变我们无法控制的代码中发生的事情。所以这里的想法不是加快我们使用的库的初始化时间，而是控制*何时*和*如何*初始化那些库。具体来说，我们可以决定对于任何给定的库，我们是否需要在启动时实际初始化它(或者使用库的默认机制将内容提供者添加到合并的清单，或者通过在 App Startup 的内容提供者中汇集初始化请求的技术)，或者我们是否希望稍后加载它们。

例如，您的应用程序中可能有一个特定的流需要一些内容提供者初始化的库，这不会在启动时立即发生。或者在某些用法中根本不会发生。在这种情况下，为什么要在启动时花时间初始化一个只在代码路径中需要的大型库呢？为什么不等到真正需要这个库来承担初始化成本呢？

这就是应用程序启动的亮点:它帮助您从合并的清单和启动过程中删除隐藏的内容提供者，并在以后更有意识地初始化这些库。

# Lazy-Init 与应用程序启动

所以现在我们知道了如何使用应用程序启动来自动加载和初始化库。但是让我们更进一步，看看如何懒惰地做这件事，以防你在启动时不想初始化。

实际上，上面的代码就快完成了:对于启动和您想要使用的任何其他库，您都需要在`build.gradle`中有相同的依赖项。并且您需要特殊的“remove”provider 标记来为每个库去除自动生成的内容提供者。此时，我们需要向清单文件添加一些信息，告诉它也删除应用程序启动提供程序。那么这些都不会在开始时发生，只要你认为时机合适，就由你来触发初始化。

为此，我将上一节中的`InitializationProvider`替换为下面的这个。我上面展示的代码告诉系统在哪里可以找到自动初始化内容提供者中的库的代码。现在我想跳过这一部分，让它删除自动生成的启动提供程序，因为稍后我会手动触发初始化:

```
<provider
    android:name="androidx.startup.InitializationProvider"
    android:authorities="${applicationId}.androidx-startup"
    tools:node="remove" />
```

在我做了这个更改之后，在合并的清单中不再有任何内容提供者，所以 App Startup 和 WorkManager 都不会在启动时自动初始化。

为了手动初始化这些库，我在应用程序的其他地方添加了以下代码:

```
val initializer = AppInitializer.getInstance(context)
initializer.initializeComponent(MyWorkManagerInitializer::class.java)
```

`AppInitializer`由 App 启动库提供，连接这些碎片。您用一个`context`对象创建了一个`AppInitializer`对象，然后将一个对您创建的用于初始化各种库的`Initializer`的引用传递给它。在这种情况下，我把它对准我的`MyWorkManagerInitializer`，我就完成了。

# 时机就是一切

我运行了几个测试(使用我的[测试应用程序启动性能](/androiddevelopers/testing-app-startup-performance-36169c27ee55)文章中介绍的计时技术)来比较我启动应用程序和初始化库的所有不同方式。我在没有任何库的情况下对应用程序的启动进行了计时，包括了`WorkManager`(使用默认的自动生成内容提供者)，启动时由应用程序启动自动初始化`WorkManager`，使用`AppInitializer`对`WorkManager`和应用程序启动进行延迟初始化。

请注意，如前所述，这些计时是在锁定时钟下进行的，正如在[测试文章](/androiddevelopers/testing-app-startup-performance-36169c27ee55)中所讨论的，因此这些持续时间比未锁定时钟下的持续时间长得多。它们只有在相互比较时才有意义，与任何真实世界的情况都没有关系。以下是我的发现:

*   不带工作管理器:1244 毫秒
*   通过内容提供者加载工作管理器:1311 毫秒
*   通过应用程序启动加载工作管理器:1315 毫秒
*   使用工作管理器(延迟加载，不在启动时):1268 毫秒

最后，我计算了用`AppInitalizer`手动初始化`WorkManager`的时间:

*   通过 AppInitializer 初始化工作管理器:51 毫秒

从这些数据中可以得出一些结论。首先，`WorkManager`在启动期间加载我的应用程序时，它的启动时间平均增加了 67 毫秒(1311–1244)。请注意，以常规方式(通过内容提供商)加载它与通过应用程序启动加载它所花费的时间大致相同(1315–1244 = 71 毫秒)。这是因为对于单库的情况，应用程序启动实际上并没有为我们节省任何东西；我们只是将工作转移到不同的代码路径中。通过应用程序启动加载几个库可能有好处，但是对于这里的单个库的情况，这种方法没有节省时间的好处。

与此同时，延迟初始化`WorkManager`允许我将大约 51 毫秒的时间延迟到稍后。

这对你来说足够重要吗？一如既往，答案是“视情况而定”。

1.3 秒中的 51 毫秒不到总时间的 4%，在一个比我的简单应用程序做得更多的真实应用程序中，这个时间会更少。在你的情况下，这段时间可能不值得你这么麻烦。另一方面，您可能会发现有些库的初始化时间要长得多。或者，更有可能的是，你可能和内容提供商一起使用几个库，每个库都会增加你的启动时间。如果你能把大部分或者所有这些推迟到一个更合适的时间，让它们离开启动路径，那么也许你能从应用程序启动中看到显著的启动时间优势。

像所有的绩效项目一样，你能做的最重要的事情是分析细节，衡量，然后决定:

*   看看你的合并清单。你看到多少个`<provider>`标签？
*   您能否使用应用启动从合并的清单中移除部分或全部内容提供者，并查看它如何影响启动时间？您能在不影响运行时行为的情况下这样做吗？(请注意，根据库的功能，您需要确保在应用程序隐式启动之前初始化库。

同时，祝性能测试和改进愉快。我会继续寻找更多的方法来分析和改善应用程序的性能，当我发现任何有价值的东西时，我会发布。有时间的时候。