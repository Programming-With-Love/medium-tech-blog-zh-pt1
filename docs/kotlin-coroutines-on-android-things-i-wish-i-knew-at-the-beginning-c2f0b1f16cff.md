# Android 上的 Kotlin 协同程序:我希望一开始就知道的事情

> 原文：<https://medium.com/capital-one-tech/kotlin-coroutines-on-android-things-i-wish-i-knew-at-the-beginning-c2f0b1f16cff?source=collection_archive---------0----------------------->

![](img/2a218c76884fb9f89f0303bc70d57198.png)

协程是 Kotlin 的一个伟大的新特性，它允许您以顺序的方式编写异步代码。与任何新的异步解决方案一样，都有一个学习曲线。但在我看来，没有像 RxJava 那样陡峭。然而，像 RxJava 一样，协程有许多细微之处，您最终会在开发期间自己学会，或者从其他人那里学到一些技巧。

我们的团队已经使用协程几个月了，总的来说这是一次美妙的经历。我想写下我们一路上学到的一些东西，如果我们在旅程开始时就知道，就会导致我们做出一些不同的架构决策，并减少一些调试难题。

如果你是第一次使用协同程序，我强烈建议你仔细阅读罗曼·埃利扎罗夫在 KotlinConf 2017 上的演讲，并阅读 GitHub 官方页面上的主要指南:

*   Coroutines @ KotlinConf
    https://www.youtube.com/watch?v=_hfBv0a09Jc 简介
*   深入研究 JVM @ KotlinConf 上的协程:
    [https://www.youtube.com/watch?v=YrrUCSi72E8](https://www.youtube.com/watch?v=YrrUCSi72E8)
*   主 GitHub 指南:
    [https://GitHub . com/kot Lin/kot linx . coroutines/blob/master/coroutines-guide . MD](https://github.com/Kotlin/kotlinx.coroutines/blob/master/coroutines-guide.md)

自从我们开始使用以来，GitHub 协程指南已经更新了很多次，并且包括了一些我们自己学到的内容。一旦你对协程有了感觉，通读问题列表[也是有帮助的，因为维护人员已经非常积极地以一种非常有教育意义的方式回应(和添加)它们。](https://github.com/Kotlin/kotlinx.coroutines/issues)

# 名单

***更新:*** *自本文撰写以来，该列表中的许多项目都发生了变化或演变。自从我们最初集成到 1.0 之前的版本/实验版以来，协程已经走过了漫长的道路！*

## 您可以将执行器转换为协同调度器

执行器通常用作管理各种线程池的一种方式。如果您正在将协程集成到现有的应用程序中，您可能需要一种重用现有池的方法。幸运的是，协程依赖在执行器上添加了一个方便的扩展函数，您可以将它们转换成协程调度器:

```
val executorService = Executors.newFixedThreadPool(100)
val coroutineDispatcher = executorService.*asCoroutineDispatcher*()

*launch* (coroutineDispatcher) **{** // ...
**}**
```

在 Android 上，这对于将 AsyncTask 线程池转换为 dispatcher 来允许 Espresso 测试在您的协程上等待也非常有用:

```
*launch* (AsyncTask.*THREAD_POOL_EXECUTOR*.*asCoroutineDispatcher*()) **{** // ...
**}**
```

我建议将您喜欢的调度程序保存在类似匕首图的东西中，以便您可以根据您的情况来交换它们。

## 您可以创建一个“根”协程父级

***更新:*** *这个现在换成了* [*范围界定*](https://kotlinlang.org/docs/reference/coroutines/coroutine-context-and-dispatchers.html#coroutine-scope) *！*

如果您习惯于使用 RxJava 并通过单个对象管理可处置资源，那么在协程中也有一个等效的方法，即创建一个空白父作业并将其用于多个协程:

```
val rootParent = *Job*()

fun foo() {
  *launch*(*UI*, parent = rootParent) **{** // ...
  **}** }

fun bar() {
  *launch*(CommonPool, parent = rootParent) **{** // ...
  **}** }

fun destroy() { rootParent.cancel() }
```

当你想取消所有的下游子节点时，你可以只取消你的根父节点，假设所有的子节点都是可协同取消的，它们将会自动清除。

## 公共池的大小非常有限

如果在启动协程时没有指定调度程序，那么 CommonPool 就是当前的“默认”调度程序。它*还*包装了一个 numProcessors-1 大小的线程池(最小为 1)，如[源代码](https://github.com/Kotlin/kotlinx.coroutines/blob/eb98bc8e84db9ed620cba9d88384244dea92d002/core/kotlinx-coroutines-core/src/main/kotlin/kotlinx/coroutines/experimental/CommonPool.kt#L56)所示:

```
private fun createPlainPool(): ExecutorService {
    val threadId = AtomicInteger()
    return Executors.newFixedThreadPool(defaultParallelism()) **{** Thread(it, "CommonPool-worker-${threadId.incrementAndGet()}").apply **{** isDaemon = true **}
    }** }

private fun defaultParallelism() = (Runtime.getRuntime().availableProcessors() - 1).coerceAtLeast(1)
```

根据你的 Android 应用支持的 API 等级，这可能是一个真正的问题。有相当多的旧设备型号是单核或双核的，这意味着公共池的大小只有 1。如果你习惯于 AsyncTask 线程池，这也是*不同的*，async task 线程池最少有[两个线程](https://github.com/aosp-mirror/platform_frameworks_base/blob/0c4f6fffd60c832d06cbbccfeac7b3f15d8a751e/core/java/android/os/AsyncTask.java#L183-L187)。

根据您的应用程序对协程的处理，这可能会导致特定于设备的死锁问题，这是一个很难调试的问题。

幸运的是，这个修复并不太糟糕——当没有达到最小线程数要求时，您可以创建自己的线程池，并把它放在一个集中的地方。例如，以下将使用 CommonPool，但在双核或单核设备上除外，在这种情况下将使用大小为 2 的单独线程池:

```
val backgroundPool: CoroutineDispatcher by *lazy* **{** val numProcessors = Runtime.getRuntime().availableProcessors()
  when {
    numProcessors <= 2 -> *newFixedThreadPoolContext*(2, "background")
    else -> CommonPool
  }
**}**
```

你也可以选择完全抛弃公共池，一直使用你自己的。正如我前面提到的，如果您将首选的协程调度程序集中在某个地方，并将其注入到您的应用程序中，以后就很容易切换出来。

## 如果不小心的话，异步可能会“吞下”异常

如果在一个*异步*块中抛出一个异常，这个异常实际上并没有立即抛出。相反，它会在你调用 wait 的时候抛出。

```
// This won't crash...
val deferred = *async* **{** throw Exception("Something failed")
**}** *launch* **{** *delay*(5000)
  // ... until 5  seconds have elapsed and we call await()
  deferred.await()
**}**
```

如果您忘记调用 await()或者它在一个被遗漏的代码分支中，那么异常就被有效地吞掉了(这可能是也可能不是一件坏事)。

这里的建议是，除非您的特定用例需要，否则不要使用异步(或者，至少要意识到这种行为)。协程的好处在于，您或多或少可以编写顺序异步代码，因此通常不需要使用面向未来的编码。

另一方面，这可能也是您感兴趣的一个特性——将异常处理推迟到以后。

## **你可以在一个挂起函数**中获得对周围协同上下文的引用

***更新:*** *感谢* [*路易 CAD*](https://medium.com/u/9862bd834329?source=post_page-----c2f0b1f16cff--------------------------------) *告诉我这个特性现在已经可以在 Kotlin 标准库中使用了*[*1 . 2 . 30*](https://github.com/JetBrains/kotlin/blob/v1.2.30/libraries/stdlib/src/kotlin/coroutines/experimental/CoroutinesLibrary.kt#L108)*！*

现在，您可以简单地在挂起函数中引用`coroutineContext`来访问当前协程的上下文。

感谢上面的说明，你可以更新到 Kotlin 1.2.30，但下面是旧的解决方案，并对问题进行了解释，以防对任何人有所帮助。

协同取消协程要求您在协程之间建立父子关系:

```
*launch* **{** *launch* (coroutineContext) **{** // Now the inner coroutine will cancel with the outer coroutine
  **}
}**
```

但是如果你有这样的情况呢:

```
fun foo() {
  *launch* **{** bar()
  **}** }

suspend fun bar() {
  *launch* **{** // ...
  **}** }
```

取消 foo 的协同程序不会取消 bar 的。相反，您必须实际传递 CoroutineContext 或 parent-to-use 作为参数，这不是很好:

```
fun foo() {
  *launch* **{** bar(coroutineContext)
  **}** }

suspend fun bar(coroutineContext: CoroutineContext) {
  *launch*(coroutineContext) **{** // ...
  **}** }
```

然而，目前有一个公开的问题，增加了从挂起函数中轻松获取上下文的支持:
[https://youtrack.jetbrains.com/issue/KT-17609](https://youtrack.jetbrains.com/issue/KT-17609)

目前，您可以使用以下解决方法:

```
import kotlin.coroutines.experimental.intrinsics.suspendCoroutineOrReturnsuspend fun coroutineContext(): CoroutineContext =   
  *suspendCoroutineOrReturn* **{** cont **->** cont.context **}**
```

像这样使用它:

```
suspend fun bar() {
  val context = coroutineContext()
  *launch*(context) **{** // ...
  **}** }
```

这可以让你保持你的参数列表的整洁，并且还可以让你很容易地设置取消链！

协程是 Kotlin 的一个很棒的新特性，可以帮助您比以往更容易地编写异步代码。我们的团队和他们有过很好的经历，我希望分享一些微妙和技巧也能改善你和他们的经历！