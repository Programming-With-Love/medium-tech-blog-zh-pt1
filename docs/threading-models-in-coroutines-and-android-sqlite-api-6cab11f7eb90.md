# 协程和 Android SQLite API 中的线程模型

> 原文：<https://medium.com/androiddevelopers/threading-models-in-coroutines-and-android-sqlite-api-6cab11f7eb90?source=collection_archive---------2----------------------->

![](img/577060d3b329729e4a004d51fb3ba724.png)

Illustration by [Virginia Poltrack](https://twitter.com/VPoltrack)

## 在房间中实施暂停交易

Room 2.1 现在允许您通过定义挂起的 DAO 函数来使用 Kotlin 协程。协程非常适合执行异步操作。它们允许您编写自然、有序的代码，这些代码可以在数据库等昂贵的资源上运行，而不必在线程之间显式地传递任务、结果和错误。Room 的协程支持为您带来了数据库操作中的并发范围、生命周期和嵌套的好处。

在 Room 中开发协程支持时，我们最终在协程的线程模型和 Android 的 SQL API 之间遇到了一些不可预见的问题。请继续阅读，了解有关这些问题、我们的解决方案和实施的更多信息。

# 无法预见的问题

看一下下面的代码片段，它看起来很安全，但实际上是坏的😮：

> Android 的 SQLite 事务是线程受限的

问题是 Android 的 SQLite 事务是线程受限的。当查询在当前线程上正在进行的事务中执行时，它被视为该事务的一部分，可以继续执行。如果查询是在不同的线程上执行的，那么它不会被视为该事务的一部分，并且会一直阻塞，直到另一个线程上的事务结束。这就是`beginTransaction`和`endTransaction` API 允许原子性的方式。当数据库任务完全在一个线程上运行时，这是一个合理的 API 然而，这对于协程来说是个问题，因为它们没有绑定到任何特定的线程。不能保证挂起后继续协同程序的线程与挂起点之前执行的线程是同一线程。

![](img/49e1e5e3ccfb6bf9324c41fb1ee2b98c.png)

Database transactions in coroutines can lead to deadlock.

# 一个简单的实现

为了解决 Android 的 SQLite 限制，我们需要一个类似于`runInTransaction`的 API 来接受一个暂停块。这个 API 的简单实现可以简单到使用一个单线程调度程序:

上面的实现只是一个开始，但是当在挂起块中使用不同的调度程序时，它很快就崩溃了:

通过接受挂起块，有可能使用不同的调度程序启动子协同例程，这可能会在意外的不同线程中执行数据库操作。因此，一个合适的实现应该允许使用标准的协程构建器，比如`async`、`launch`和`withContext`。实际上，只需要将数据库操作分派给单个事务线程。

# withTransaction 简介

为了实现这一点，我们构建了`[withTransaction](https://developer.android.com/reference/kotlin/androidx/room/package-summary.html#(androidx.room.RoomDatabase).withTransaction(kotlin.coroutines.SuspendFunction0))` API，它模仿了`withContext` API，但是提供了一个专门为安全房间事务构建的协同程序上下文。这允许您编写代码，例如:

当我们深入到 Room 的`withTransaction` API 的实现时，让我们回顾一下已经提到的一些协程概念。一个`[CoroutineContext](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.coroutines/-coroutine-context/index.html)`保存协程分派工作所需的信息。它携带当前的`[CoroutineDispatcher](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-coroutine-dispatcher/index.html)`、`[Job](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-job/)`，可能还有一些附加数据；但是它也可以扩展为包含更多的元素。`CoroutineContext`的一个重要特征是它们被同一协程范围内的子协程继承，例如`withContext`块中的范围。这种机制允许子协程继续使用同一个调度程序，或者当父协程作业被取消时，它们被取消。实质上，Room 的挂起事务 API 创建了一个专门的协同例程上下文，用于在事务范围内执行数据库操作。

在由`withTransaction` API 创建的上下文中有三个关键元素:

*   用于执行数据库操作的单线程调度程序。
*   一个上下文元素，帮助 DAO 函数识别它们是否在事务中。
*   一个`[ThreadContextElement](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-thread-context-element/)`标记在事务协程期间使用的调度线程。

# 事务调度程序

一个`CoroutineDispatcher`指示协程将在哪个线程中执行。例如，`[Dispatchers.IO](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-dispatchers/-i-o.html)`使用推荐用于卸载阻塞操作的共享线程池，而`[Dispatchers.Main](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-dispatchers/-main.html)`将在 Android 的主线程中执行协程。由 Room 创建的事务分派器能够分派到来自 [Room 的执行者](https://developer.android.com/reference/androidx/room/RoomDatabase.Builder.html#setQueryExecutor(java.util.concurrent.Executor))的单个线程——它没有使用任意的新线程。这一点很重要，因为执行器可由用户配置，并且可用于测试。在事务开始时，Room 将获得 executor 中一个线程的所有权，直到事务完成。在事务期间执行的数据库操作将被分派到事务线程中，即使为子协同例程更改了分派程序。

获取一个事务线程不是一个阻塞操作——不应该是，因为如果没有线程可用，我们应该挂起并让出调用者，以便其他协程可以继续。它还包括将 runnable 入队并等待它实际执行，这是一个线程变得可用的指示。函数`[suspendCancellableCoroutine](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/suspend-cancellable-coroutine.html)`帮助我们在基于回调的 API 和协程之间架起一座桥梁。在这种情况下，当线程可用时，我们的回调是执行排队的 runnable。一旦我们的 runnable 执行完毕，我们就使用`[runBlocking](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/run-blocking.html)`来启动一个事件循环，这个事件循环将获得线程的所有权。然后由`runBlocking`创建的分派器被提取出来，用于将块分派到获取的线程中。此外，使用一个`Job`来挂起和保持线程，直到事务完成。请注意，在协程被取消或无法获取线程时会采取预防措施。获取事务线程的代码片段如下:

# 事务上下文元素

有了 dispatcher，我们就可以创建一个事务元素，我们可以将它添加到我们的上下文中，并引用 dispatcher。这使我们能够将 DAO 函数重新路由到正确的线程，如果它们是从事务范围内调用的话。我们的交易要素如下:

`TransactionElement`函数`acquire`和`release`用于跟踪嵌套事务。由于对应的方法`beginTransaction`和`endTransaction`允许嵌套调用，我们理想地希望允许相同的行为；但是我们只需要在最外层的事务完成时释放事务线程。这些功能的用法将在`withTransaction`的实现中给出。

# 事务线程标记

前面提到的创建事务上下文所需的最后一个关键元素是一个`[ThreadContextElement](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-thread-context-element/)`。一个`CoroutineContext`中的这个元素类似于一个`[ThreadLocal](https://docs.oracle.com/javase/8/docs/api/java/lang/ThreadLocal.html)`，它跟踪线程中是否有正在进行的事务。该元素由一个实际的`ThreadLocal`支持；它的工作方式是为调度程序用来执行协程程序块的每个线程的`ThreadLocal`设置一个值。一旦线程执行完该块，该值将被重置。对于我们的用例，这个值是没有意义的；在 Room 中，这只是一个值是否存在的问题。如果协程上下文可以访问平台中存在的`[ThreadLocal<SQLiteSession>](https://android.googlesource.com/platform/frameworks/base/+/master/core/java/android/database/sqlite/SQLiteDatabase.java#96)`,我们可以从协程所在的任何线程向它分派 begin/ends。我们不能，只好阻塞线程，直到事务完成；但是我们仍然需要跟踪每个阻塞数据库方法应该在哪个事务上运行，从而跟踪哪个线程拥有平台事务。

Room 的`withTransaction` API 使用的`ThreadContextElement`标识阻塞的数据库函数。Room 中的阻塞函数，包括生成的 Dao 中的那些，现在在事务协程中被调用时有了特殊的处理，以确保它们不会在不同的调度程序上运行。这使得在一个`withTransaction`块中混合和匹配阻塞函数和挂起函数成为可能，因为您的 DAO 仍然混合了这两种类型的函数。通过将`ThreadContextElement`添加到协程上下文并从所有 DAO 函数中访问它，我们可以验证阻塞函数是否在正确的范围内。如果不是，那么**我们抛出一个错误，而不是导致死锁**。将来，我们计划将阻塞函数重新路由到事务线程中。

这三个要素的结合创造了我们的交易环境:

# 事务 API 实现

有了创建的事务上下文，我们现在终于可以为在协程中执行数据库事务提供一个安全的 API 了。剩下要做的是将这个上下文与通常的开始/结束事务模式结合起来:

Android 的 SQLite 线程限制是合理的，并且是在 Kotlin 不存在的时代设计的。同时，协程引入了一种新的范式，改变了传统 Java 并发的一些思维过程。解除 Android 的线程限制不是一个可行的解决方案，因为我们想提供一个向后兼容的解决方案。上面提到的东西的组合最终使我们在一个伴随着 coroutines-fluent API 的解决方案中具有创造性。