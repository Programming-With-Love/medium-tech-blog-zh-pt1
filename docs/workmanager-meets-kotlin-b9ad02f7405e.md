# 工作经理会见科特林

> 原文：<https://medium.com/androiddevelopers/workmanager-meets-kotlin-b9ad02f7405e?source=collection_archive---------2----------------------->

![](img/739581b125423e435cd00a9f47a7b6e1.png)

Illustration by [Virginia Poltrack](https://twitter.com/VPoltrack)

欢迎来到我们的工作管理器系列的第三篇文章。 [WorkManager](https://developer.android.com/topic/libraries/architecture/workmanager/) 是一个 [Android Jetpack](https://developer.android.com/jetpack/) 库，它使得调度可推迟的、必须可靠运行的异步任务变得容易。这是目前 Android 上大多数后台工作的最佳实践。

如果您一直在关注，我们已经讨论了:

*   [什么是工作管理器，何时使用工作管理器](/androiddevelopers/introducing-workmanager-2083bcfc4712)
*   [如何使用工作管理器 API 调度工作](/androiddevelopers/workmanager-basics-beba51e94048)

在这篇博文中，我将介绍:

*   在 Kotlin 中使用工作管理器
*   `[CoroutineWorker](https://developer.android.com/reference/kotlin/androidx/work/CoroutineWorker)`阶级
*   如何使用`[TestListenableWorkerBuilder](https://developer.android.com/reference/androidx/work/testing/TestListenableWorkerBuilder)`测试你的`CoroutineWorker`类？

# 科特林的工作经理

这篇博文中的代码片段在 Kotlin 中，使用了 [KTX 库](https://developer.android.com/kotlin/ktx) (KoTlin 扩展)。WorkManager 的 KTX 版本为更简洁和惯用的 Kotlin 提供了扩展函数。您可以使用工作管理器的 KTX 版本向您的`build.gradle`文件添加对`androidx.work:work-runtime-ktx`工件的依赖，如工作管理器的[发行说明](https://developer.android.com/jetpack/androidx/releases/work)中所述。这个构件包括`CoroutineWorker`和其他对 WorkManager 有用的扩展方法。

# 更简洁、更地道

当您需要构建一个数据对象来传入或传出一个`Worker`类时，WorkManager 的 KTX 提供了一个更好的语法。在这种情况下，Java 语法如下所示:

```
Data myData = new Data.Builder()
                      .putInt(KEY_ONE_INT, aInt)
                      .putIntArray(KEY_ONE_INT_ARRAY, aIntArray)
                      .putString(KEY_ONE_STRING, aString)
                      .build();
```

在 Kotlin 中，我们可以使用`[workDataOf](https://developer.android.com/reference/kotlin/androidx/work/package-summary#workdataof)`辅助函数做得更好:

```
inline fun workDataOf(vararg pairs: Pair<String, Any?>): Data
```

这允许将前面的 Java 表达式写成:

```
val data = workDataOf(
        KEY_MY_INT to myIntVar,
        KEY_MY_INT_ARRAY to myIntArray,
        KEY_MY_STRING to myString
    )
```

# `[CoroutineWorker](https://developer.android.com/reference/kotlin/androidx/work/CoroutineWorker.html)`

除了 Java 中可用的 worker 类(`[Worker](https://developer.android.com/reference/androidx/work/Worker.html)`、`[ListenableWorker](https://developer.android.com/reference/androidx/work/ListenableWorker)`和`[RxWorker](https://developer.android.com/reference/androidx/work/RxWorker.html)`)，还有一个 Kotlin only 类，它使用 [Kotlin 的协程](https://kotlinlang.org/docs/reference/coroutines-overview.html)为您工作。

`Worker`类和`CoroutineWorker`的主要区别在于`CoroutineWorker`中的`doWork()`方法是一个挂起函数，可以运行异步任务，而`Worker`的`doWork()`只能执行同步对话。另一个`CoroutineWorker`特性是它自动处理停止和取消，而工人类需要实现`onStopped()`方法来处理这些情况。

关于这些不同选项的完整内容，您可以参考工作管理器指南中的[线程。](https://developer.android.com/topic/libraries/architecture/workmanager/advanced/threading)

在这里，我想把重点放在什么是`CoroutineWorker`上，涵盖一些微小但重要的差异，然后深入研究如何使用 WorkManager v2.1 中引入的新测试特性来测试您的`CoroutineWorker`类。

正如我们之前写的，`[CoroutineWorker#doWork()](https://developer.android.com/reference/kotlin/androidx/work/CoroutineWorker.html#dowork)`只是一个暂停函数。默认情况下，这在`Dispatchers.Default`启动:

```
class MyWork(context: Context, params: WorkerParameters) :
        CoroutineWorker(context, params) {override suspend fun doWork(): Result {return try {
            // Do something
            Result.success()
        } catch (error: Throwable) {
            Result.failure()
        }
    }
}
```

当使用`CoroutineWorker`代替`Worker`或`ListenableWorker`时，理解这一点很重要:

> *与* `*Worker*` *不同，此代码不会在您的工作管理器配置中指定的* `*Executor*` *上运行。*

我们刚才说过，`CoroutineWorker#doWork()`默认为`Dispatchers.Default`。您可以使用`withContext()`对此进行定制:

```
class MyWork(context: Context, params: WorkerParameters) :
        CoroutineWorker(context, params) {override suspend fun doWork(): Result = withContext(Dispatchers.IO) {return try {
            // Do something
            Result.success()
        } catch (error: Throwable) {
            Result.failure()
        }
    }
}
```

> 很少需要改变你的`CoroutineWorker`正在使用的`Dispatcher`，并且`Dispatchers.Default`在大多数情况下是一个好的选择。

要了解更多关于如何在 Kotlin 中使用 WorkManager 的信息，你可以试试[这个 codelab](https://codelabs.developers.google.com/codelabs/android-workmanager-kt/) 。

# 测试工作类

WorkManager 有几个额外的构件，允许轻松地测试您的工作。您可以在[工作管理器的测试文档页面](https://developer.android.com/topic/libraries/architecture/workmanager/how-to/testing)和新的“[使用工作管理器 2.1.0 进行测试](https://developer.android.com/topic/libraries/architecture/workmanager/how-to/testing-210)”指南中了解更多信息。这个测试助手的最初实现使得定制 WorkManager 成为可能，这样它就可以同步工作，然后您可以使用`[WorkManagerTestInitHelper#getTestDriver()](https://developer.android.com/reference/kotlin/androidx/work/testing/WorkManagerTestInitHelper#gettestdriver)`来模拟延迟和测试周期性工作。

这里的关键点是，您正在修改驱动您的`Worker`类的行为或工作管理器，以使测试它们成为可能。

WorkManager v2.1 添加了一个新的`TestListenableWorkerBuilder`功能，它引入了一种新的方法来测试您的`Worker`类。

这对于`CoroutineWorker`来说是一个非常重要的更新，因为使用`TestListenableWorkerBuilder`你实际上是直接运行你的工人类来测试他们的逻辑。

```
@RunWith(JUnit4::class)
class MyWorkTest {
    private lateinit var context: Context @Before
    fun setup() {
        context = ApplicationProvider.getApplicationContext()
    } @Test
    fun testMyWork() {
        // Get the ListenableWorker
        val worker = 
            TestListenableWorkerBuilder<MyWork>(context).build() // Run the worker synchronously
        val result = worker.startWork().get() assertThat(result, `is`(Result.success()))
    }
}
```

这里重要的是运行`CoroutineWorker`的结果是同步获得的，您可以直接检查您的 Worker 类逻辑行为是否正确

使用`TestListenableWorkerBuilder`你可以将输入数据传递给你的`Worker`或者设置`runAttemptCount`，这对测试你工作中的重试逻辑是有用的。

举个例子，如果你上传一些数据到服务器，你可能想要添加一些重试逻辑来考虑连接问题。

```
class MyWork(context: Context, params: WorkerParameters) :
        CoroutineWorker(context, params) { override suspend fun doWork(): Result {
        val serverUrl = inputData.getString("SERVER_URL") return try {
            // Do something with the URL
            Result.success()
        } catch (error: TitleRefreshError) {
            if (runAttemptCount <3) {
                Result.retry()
            } else {
                Result.failure()
            }
        }
    }
}
```

然后，您可以使用`TestListenableWorkerBuilder`在您的测试中测试这个重试逻辑:

```
@Test
fun testMyWorkRetry() {
    val data = workDataOf("SERVER_URL" to "[http://fake.url](http://fake.url)") // Get the ListenableWorker with a RunAttemptCount of 2
    val worker = TestListenableWorkerBuilder<MyWork>(context)   
                     .setInputData(data)
                     .setRunAttemptCount(2)
                     .build() // Start the work synchronously
    val result = worker.startWork().get() assertThat(result, `is`(Result.retry()))
}@Test
fun testMyWorkFailure() {
    val data = workDataOf("SERVER_URL" to "[http://fake.url](http://fake.url)") // Get the ListenableWorker with a RunAttemptCount of 3
    val worker = TestListenableWorkerBuilder<MyWork>(context)
                     .setInputData(data)
                     .setRunAttemptCount(3)
                     .build() // Start the work synchronously
    val result = worker.startWork().get() assertThat(result, `is`(Result.failure()))
}
```

# 结论

随着 WorkManager v2.1 的发布和 workmanager-testing 中的新特性，`CoroutineWorker`因其简单性和功能性而大放异彩。测试现在真的很容易，在 Kotlin 的整体体验非常棒。

如果你还没有尝试过`CoroutineWorker`和`workmanager-runtime-ktx`中包含的其他扩展，我强烈建议你在自己的项目中尝试一下。当我和 Kotlin 一起工作时(现在总是这样)，这是我使用 WorkManager 的首选方式！

我希望这篇文章对你有用，我很想知道你是如何使用 WorkManager 的，或者 WorkManager 的哪些特性可以得到更好的解释或记录。

你可以在推特 [@pfmaggi](https://twitter.com/pfmaggi) 上找到我。

# 资源

*   [程序员指南](https://developer.android.com/topic/libraries/architecture/workmanager/)
*   [参考指南](https://developer.android.com/reference/androidx/work/package-summary)
*   [Codelab:工作管理器的后台工作](https://codelabs.developers.google.com/codelabs/android-workmanager-kt/)
*   [工作管理器公共发行跟踪器](https://issuetracker.google.com/issues?q=componentid:409906)
*   [发行说明](https://developer.android.com/jetpack/androidx/releases/work)
*   [堆栈溢出【android-workmanager】标签](https://stackoverflow.com/questions/tagged/android-workmanager)
*   [工作管理器源代码(AOSP 的一部分)](https://android.googlesource.com/platform/frameworks/support/+/master/work)