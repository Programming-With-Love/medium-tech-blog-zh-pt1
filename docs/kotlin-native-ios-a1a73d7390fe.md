# 科特林/原生 iOS

> 原文：<https://medium.com/quick-code/kotlin-native-ios-a1a73d7390fe?source=collection_archive---------1----------------------->

## 3.协程和 K/N 的不变性

# 协同程序

什么是协程？这个[官方指南](https://kotlinlang.org/docs/reference/coroutines/coroutines-guide.html)对于理解什么是协程以及如何使用协程非常有帮助。

本章的主题是在 K/N 中使用协程及其当前状态。

# 更新梯度

要使用 K/N 中的协同程序，请更新`sharedNative/build.gradle`中的依赖关系。

`kolinx-coroutines-core-common`和`kotlinx-coroutines-core-native`是新的。一更，在`settings.gradle` [加一行](https://github.com/Kotlin/kotlinx.coroutines/blob/master/native/README.md) `enableFeaturePreview('GRADLE_METADATA')`。

```
include ':app'
include ':sharedNative'

enableFeaturePreview('GRADLE_METADATA')
```

然后，`Sync Now`。

# 使用 iOS 中的协程

现在，您可以在公共模块中使用协程 API。在`actual.kt`中，定义这个`CoroutineDispatcher`。

```
private class MainDispatcher: CoroutineDispatcher() {
    override fun dispatch(context: CoroutineContext, block: Runnable) {
        *dispatch_async*(*dispatch_get_main_queue*()) **{** block.run() **}** }
}
```

协程调度程序确定相应的协程使用什么线程来执行。

接下来，定义`CoroutineScope`。我们需要一个运行在 iOS 事件循环上的作用域。

```
internal class MainScope: CoroutineScope {
    private val dispatcher = MainDispatcher()
    private val job = *Job*()

    override val coroutineContext: CoroutineContext
        get() = dispatcher + job
}
```

作用域用于生成协程。我不想让它在 iOS 中可见，所以用`internal`定义了`MainScope`类。但是访问级别取决于您。

> 协程作用域是新协程的作用域。这意味着每个协程构建器都继承了作用域的`coroutineContext`。由于这个原因，上下文元素和**取消**都被自动传播。例如，作用域的取消调用作用域内启动的协程的取消。

让我们试着调用协程。在`actual.kt`中定义`showHelloCoroutine`功能，在`common.kt`中定义`helloCoroutine`暂停功能。`launch`函数是`CoroutineScope`类的扩展函数。这是一个协同程序生成器。

```
fun showHelloCoroutine() {
    MainScope().launch {
        helloCoroutine()
    }
}---above: actual.kt--------below: common.kt------internal suspend fun helloCoroutine() {
    println("Hello Coroutines!")
}
```

我们可以这样称呼它。

```
ActualKt.showHelloCoroutine()// Hello Coroutines!
```

这里我分成两个函数。一个类似于协程包装函数，另一个是挂起函数。

在大多数情况下，挂起函数有业务逻辑，我们希望共享它们。所以在这里，我在`common.kt`中定义了`helloCoroutine`暂停功能，以便在 Android 中使用。

另一方面，挂起函数还不能直接从 iOS 中使用，所以我为挂起函数准备了 smth，就像一个包装函数。

> 暂停功能此时不会导出到框架中。但是关于这个有一个[问题](https://github.com/JetBrains/kotlin-native/issues/2438)。

# Ktor 客户端的 HTTP 请求。

上面的例子有点无聊，只是在控制台上打印。我们将在这里用 [Ktor](https://github.com/ktorio/ktor) 实现 HTTP 请求。

## 更新梯度

(2019 年 1 月 16 日更新)

## 简单的请求

在`common.kt`中创建一个 API 类

```
class Api {
    private val client = HttpClient()

    internal suspend fun request(urlString: String): String {
        val result: String = client.*call*(urlString) **{** method = HttpMethod.Get
        **}**.response.*readText*()
        return result
    }
}
```

HttpClient 类包含在 Ktor 库中。请求挂起函数用`Get` http 方法调用`client.call`。

接下来，为这个 suspend 函数定义一个包装函数，作为`actual.kt`中 Api 类的扩展函数。

```
fun Api.request(completion: (String) -> Unit) {
    MainScope().*launch* **{** val result = request("https://tools.ietf.org/rfc/rfc8216.txt")
        completion(result)
    **}** }
```

这将 https://tools.ietf.org/rfc/rfc8216.txt 作为一个参数传递。RFC8216 注册了什么？你很快就会看到。

让我们尝试在 ViewController.swift 中调用它

```
Api().request {
    print($0)
    return .init()
}
```

Kotlin lambda 返回单元是作为闭包导出的，返回`KotlinUnit`，不是 Void。因此需要最后一次返回。

无论如何，这将异步执行并打印 HTTP 实时流文档。我们可以使用来自 iOS 的 http 请求🎉

> 目前，协程仅支持**主线程**。但是，支持**的多线程**协程是[发布的](https://github.com/Kotlin/kotlinx.coroutines/issues/462)。

# K/N 不变性

## 冰冻的

在 K/N 中，*不变性*是一个运行时属性。这可以通过使用`kotlin.native.concurrent`中的`freeze()`功能来实现。我们可以使用`isFrozen`检查冻结状态。

```
package kotlin.native.concurrent

public val kotlin.Any?.*isFrozen*: kotlin.Boolean /* compiled code */
```

这是`kotlin.native.concurrent`套装的一部分。`isFrozen`属性在这里定义。`Any?`类型显然具有`isFrozen`属性。

K/N 保证了重要的不变量，`**mutable XOR global**`。这意味着对象要么是不可变的，要么可以从单线程访问。

有些对象在默认情况下是冻结的，例如

*   科特林等原始类型。弦乐，kotlin.Int
*   `object`单胎
*   枚举类

这些状态很容易检查，如下所示。在`actual.kt`中，

```
**primitive int              isFrozen: true
primitive string           isFrozen: true
class object               isFrozen: false
class object with freeze() isFrozen: true
object singleton           isFrozen: true
enum class                 isFrozen: true**
```

如果一个突变操作被应用到一个冻结的物体上，`InvalidMutabilityException`被抛出。

## **不正确的引用异常**

默认情况下，类对象不被冻结。并且 K/N 保证`mutable XOR global`。

如果我们访问一个没有冻结的对象会发生什么？试试这个，在`actual.kt`中定义下面的函数。

```
fun passNotFrozenObject(completion: (Hello) -> Unit) {
    val hello = Hello()
    completion(hello)
}
```

在`ViewController.swift`中。

```
ActualKt.passNotFrozenObject { hello in
    DispatchQueue.global(qos: .background).async {
        print(hello)
    }
    return .init()
}
```

这段代码崩溃了

```
**Uncaught Kotlin exception: kotlin.native.IncorrectDereferenceException: illegal attempt to access non-shared org.kotlin.mpp.mobile.Hello@19f3d08 from other thread**
```

从另一个线程访问对象时抛出。

通过 freeze()修复此崩溃，例如

```
fun passNotFrozenObject(completion: (Hello) -> Unit) {
    val hello = Hello().freeze()
    completion(hello)
}
```

这将起作用，控制台上将显示以下内容

```
**org.kotlin.mpp.mobile.Hello@1e470c8**
```

这些参考资料可能有助于我们理解

*   [https://github . com/JetBrains/kot Lin-native/blob/cfe 2d 7 ce 4 AEF 41 b 45747d 7 C4 d2d 6238 FD 1808d/runtime/src/main/CPP/memory . CPP # L467](https://github.com/JetBrains/kotlin-native/blob/cfe2d2b7ce4aef41b45747d7c4d2d6238fd1808d/runtime/src/main/cpp/Memory.cpp#L467)
*   [https://github . com/JetBrains/kot Lin-native/blob/328413337 b 9 dcab 1 DBA 6 DD 4d 3 cf 5975d 617 e 60d 1/runtime/src/main/CPP/memory . h # L30](https://github.com/JetBrains/kotlin-native/blob/328413337b9dcab1dba6dd4d3cf5975d617e60d1/runtime/src/main/cpp/Memory.h#L30)

## @ThreadLocal 和@ SharedImmutable

当您使用非原始类型的顶级全局变量时，ThreadLocal 或 SharedImmutable 注释有时可能会有所帮助。

试举一个简单的例子。在`actual.kt`中定义这个顶层变量。

```
val hello = Hello()
```

并从 ViewController.swift 中的不同线程访问它。

```
print(Thread.current)
print(ActualKt.hello)
DispatchQueue.global(qos: .background).async {
    print(Thread.current)
    print(ActualKt.hello)
}// output on console
**<NSThread: 0x600002e9e8c0>{number = 1, name = main}
org.kotlin.mpp.mobile.Hello@3ba2e28
<NSThread: 0x600002e16640>{number = 3, name = (null)}
Uncaught Kotlin exception: kotlin.native.IncorrectDereferenceException:**
```

一个类对象没有被冻结，所以会抛出异常。用这个怎么样？

```
val hello = Hello().freeze()
```

即使这个`hello`对象被冻结也没有变化！！

事实上，对于顶级变量，需要使用`@SharedImmutable`或`@ThreadLocal`注释。

*   SharedImmutable:使对象冻结(不可变)并可从另一个线程访问
*   ThreadLocal:使对象状态成为线程本地的和可变的(改变后的状态不会反映到其他线程)。

```
@SharedImmutable
val hello = Hello()
```

然后，控制台上成功显示以下行。

```
**<NSThread: 0x600002c468c0>{number = 1, name = main}
org.kotlin.mpp.mobile.Hello@39032e8
<NSThread: 0x600002c92080>{number = 3, name = (null)}
org.kotlin.mpp.mobile.Hello@39032e8**
```

> 这些注释可能会在未来版本中消失。

# 摘要

我介绍了协程和 K/N 的不变性。

我想在下一章展示一个使用反应式编程和架构框架的例子。(*即将推出*)

# 参考

*   [https://kot linlang . org/docs/reference/coroutines/coroutine-context-and-dispatchers . html](https://kotlinlang.org/docs/reference/coroutines/coroutine-context-and-dispatchers.html)
*   https://ktor.io/quickstart/quickstart/gradle.html
*   [https://kot linlang . org/docs/reference/native/immut ability . html](https://kotlinlang.org/docs/reference/native/immutability.html)