# 房间🔗协同程序

> 原文：<https://medium.com/androiddevelopers/room-coroutines-422b786dc4c5?source=collection_archive---------1----------------------->

![](img/922aac9373cefb037b18d992d11d7277.png)

Illustration by [Virginia Poltrack](https://twitter.com/vpoltrack)

## 给你的数据库增加一些悬念

Room 2.1 [增加了](https://developer.android.com/jetpack/androidx/releases/room)对 Kotlin 协程的支持。现在可以将 DAO 方法标记为挂起，以确保它们不会在主线程上执行。请继续阅读，了解如何使用它，它是如何工作的，以及如何测试这个新功能。

# 给你的数据库增加一些悬念

要在您的应用程序中使用协程和 Room，请更新到 Room 2.1 并将新的依赖项添加到您的`build.gradle`文件中:

```
implementation "androidx.room:room-coroutines:${versions.room}"
```

你还需要 Kotlin 1.3.0 和[协程](https://kotlinlang.org/docs/reference/coroutines-overview.html) 1.0.0 或更新版本。

现在，您可以更新您的 DAO 方法以使用挂起函数:

DAO with `suspend` methods

`[@Transaction](https://developer.android.com/reference/android/arch/persistence/room/Transaction)`方法也可以是挂起的，它们可以调用其他挂起的 DAO 函数:

DAO with suspend transaction function

您还可以在一个事务中从不同的 Dao 调用挂起函数:

Calling different DAO suspending functions in a transaction

您可以提供执行器(在构建数据库时通过调用`[setTransactionExecutor](https://developer.android.com/reference/androidx/room/RoomDatabase.Builder#setTransactionExecutor(java.util.concurrent.Executor))`或`[setQueryExecutor](https://developer.android.com/reference/androidx/room/RoomDatabase.Builder#setQueryExecutor(java.util.concurrent.Executor))`来控制它们运行的线程)。默认情况下，这将是用于在后台线程上运行查询的同一个执行器。

# 测试 DAO 悬挂功能

测试 DAO 挂起函数与测试任何其他挂起函数没有什么不同。例如，为了检查在插入一个用户后我们是否能够检索它，我们将测试包装在一个`[runBlocking](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/run-blocking.html)`块中:

Testing DAO suspend functions

# 在后台

为了了解幕后的情况，让我们看一下实现室为同步插入和挂起插入生成的 DAO 类:

Synchronous and suspending insert functions

对于同步插入，生成的代码启动一个事务，执行插入，将事务标记为成功，然后结束它。同步方法将只在调用它的线程上执行插入。

Room synchronous insert generated implementation

现在让我们看看添加 suspend 修改器是如何改变事情的:

Room suspending insert generated implementation

生成的代码确保插入发生在 UI 线程之外。在我们的挂起函数实现中，来自同步插入方法的相同逻辑被包装在一个“可调用”中。Room 调用“CoroutinesRoom.execute”挂起函数，该函数根据数据库是否打开以及我们是否在事务中切换到后台调度程序。下面是该函数的实现:

CoroutinesRoom.execute implementation

**案例一。数据库被打开，我们在一个交易**

这里我们只是立即执行 callable——即用户在数据库中的实际插入

**案例二。否则**

Room 确保在 Callable#call 方法中完成的工作是在后台线程上执行的。

房间将使用不同的调度员进行交易和查询。这些是从您在构建数据库时提供的执行器派生出来的，或者默认情况下将使用架构组件 IO 执行器。这是 LiveData 用来做后台工作的同一个执行器。

如果您有兴趣查看实现，请查看[CoroutinesRoom.java](https://android.googlesource.com/platform/frameworks/support/+/refs/heads/androidx-master-dev/room/ktx/src/main/java/androidx/room/CoroutinesRoom.kt)和 [RoomDatabase.kt](https://android.googlesource.com/platform/frameworks/support/+/refs/heads/androidx-master-dev/room/ktx/src/main/java/androidx/room/RoomDatabase.kt)

开始在你的应用程序中使用 Room 和 coroutines，数据库工作保证在非 UI 调度程序上运行。用`suspend`修饰符标记您的 DAO 方法，并从其他挂起函数或协程中调用它们！