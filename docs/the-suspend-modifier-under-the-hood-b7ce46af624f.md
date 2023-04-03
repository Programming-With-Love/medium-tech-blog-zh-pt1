# “暂停”修改器—在引擎盖下

> 原文：<https://medium.com/androiddevelopers/the-suspend-modifier-under-the-hood-b7ce46af624f?source=collection_archive---------0----------------------->

![](img/7dbdff2f6243de6e06b0425e311eb83b.png)

## 科特林词汇:协程

科特林协同程序在我们作为 Android 开发者的日常生活中引入了 ***暂停修饰符*** 。你想知道引擎盖下发生了什么吗？编译器如何转换代码以能够暂停和恢复协程的执行？

了解这一点将有助于您更好地理解为什么挂起函数在它启动的所有工作完成之前不会返回，以及代码如何在不阻塞线程的情况下挂起。

> **TL；DR；Kotlin 编译器将为每个挂起函数创建一个状态机，为我们管理协程的执行！**

📚不熟悉 Android 上的协程？查看这些协同程序代码实验室:

*   [在你的 Android 应用中使用协程](https://codelabs.developers.google.com/codelabs/kotlin-coroutines/#0)
*   [具有 Kotlin 流和实时数据的高级协同程序](https://codelabs.developers.google.com/codelabs/advanced-kotlin-coroutines/#0)

如果你喜欢看关于这个的视频，看看这个:

# 协程 101

协程简化了 Android 上的异步操作。正如在[文档](https://developer.android.com/kotlin/coroutines)中所解释的，我们可以使用它们来管理异步任务，否则这些任务可能会阻塞主线程并导致应用程序冻结。

协程也有助于用命令式代码替换基于回调的 API。例如，看看这个使用回调的异步代码:

```
// Simplified code that only considers the happy path
fun loginUser(userId: String, password: String, userResult: Callback<User>) {
  // Async callbacks
  userRemoteDataSource.logUserIn { user ->
    // Successful network request
    userLocalDataSource.logUserIn(user) { userDb ->
      // Result saved in DB
      userResult.success(userDb)
    }
  }
}
```

可以使用协程将这些回调转换为顺序函数调用:

```
**suspend** fun loginUser(userId: String, password: String): User {
  val user = userRemoteDataSource.logUserIn(userId, password)
  val userDb = userLocalDataSource.logUserIn(user)
  return userDb
}
```

在协程代码中，我们向函数添加了 **suspend** 修饰符。这告诉编译器这个函数需要在协程中执行。作为一名开发人员，您可以将挂起函数视为一个常规函数，它的执行可能会被挂起，并在某个时候恢复。

与回调不同，协程提供了一种在线程间交换和处理异常的简单方法。

但是，当我们将函数标记为*暂停*时，编译器实际上在做什么呢？

# 悬挂在引擎盖下

回到`loginUser`暂停函数，注意它调用的其他函数也是暂停函数:

```
**suspend** fun loginUser(userId: String, password: String): User {
  val user = userRemoteDataSource.logUserIn(userId, password)
  val userDb = userLocalDataSource.logUserIn(user)
  return userDb
}// UserRemoteDataSource.kt
**suspend** fun logUserIn(userId: String, password: String): User// UserLocalDataSource.kt
**suspend** fun logUserIn(userId: String): UserDb
```

简而言之，Kotlin 编译器会使用一个 [**有限状态机**](https://en.wikipedia.org/wiki/Finite-state_machine) (我们将在后面介绍)将挂起函数转换成回调的优化版本。

你答对了，**编译器会为你写那些回调函数**！

## 延续接口

挂起函数相互通信的方式是通过`[Continuation](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.coroutines/-continuation/index.html)`对象。一个`[Continuation](https://github.com/JetBrains/kotlin/blob/master/libraries/stdlib/src/kotlin/coroutines/Continuation.kt)`只是一个带有一些额外信息的通用回调接口。正如我们将在后面看到的，它将代表一个挂起函数的生成状态机。

让我们来看看它的定义:

```
interface Continuation<in T> {
  public val context: CoroutineContext
  public fun resumeWith(value: Result<T>)
}
```

*   `context`将是在该延续中使用的`CoroutineContext`。
*   `resumeWith`用`[Result](https://github.com/Kotlin/kotlinx.coroutines/blob/master/stdlib-stubs/src/Result.kt)`恢复协程的执行，它可以包含一个导致挂起的计算结果值，也可以包含一个异常。

注意:从 Kotlin 1.3 开始，你也可以使用扩展函数`[resume](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.coroutines/resume.html)(value: T)`和`[resumeWithException](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.coroutines/resume-with-exception.html)(exception: Throwable)`，它们是`resumeWith`调用的特殊版本。

编译器将在函数签名中用额外的参数`completion`(类型`Continuation`)替换 suspend 修饰符，该函数签名将用于将挂起函数的结果传递给调用它的协程:

```
fun loginUser(userId: String, password: String, **completion: Continuation<Any?>**) {
  val user = userRemoteDataSource.logUserIn(userId, password)
  val userDb = userLocalDataSource.logUserIn(user)
  **completion.resume(userDb)**
}
```

为了简单起见，我们的例子将返回`Unit`而不是`User`。`User`对象将被“返回”到添加的`Continuation`参数中。

挂起函数的字节码实际上返回了`Any?`，因为它是`T | COROUTINE_SUSPENDED`的联合类型。这允许函数在可能的时候同步返回。

> **注意**:如果你用 suspend 修饰符标记一个不调用其他 suspend 函数的函数，编译器会添加额外的 Continuation 参数，但不会对它做任何事情，函数体的字节码看起来就像一个常规函数。

在其他地方也可以看到`Continuation`界面:

*   当使用`[suspendCoroutine](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.coroutines/suspend-coroutine.html)`或`[suspendCancellableCoroutine](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/suspend-cancellable-coroutine.html)`将基于回调的 API 转换为协同程序时(您应该总是更喜欢使用这两种方法)，您可以直接与`Continuation`对象交互，以恢复在运行作为参数传递的代码块后被挂起的协同程序。
*   您可以在挂起函数上使用`[startCoroutine](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.coroutines/start-coroutine.html)`扩展函数来启动协程。它将一个`Continuation`对象作为参数，当新的协程以结果或异常结束时，该对象将被调用。

## 使用不同的调度程序

您可以在不同的调度程序之间切换，以便在不同的线程上执行计算。Kotlin 如何知道在哪里恢复挂起的计算？

有一个名为`[DispatchedContinuation](https://github.com/Kotlin/kotlinx.coroutines/blob/master/kotlinx-coroutines-core/common/src/internal/DispatchedContinuation.kt)`的`Continuation`子类型，它的 resume 函数调用`CoroutineContext`中可用的`Dispatcher`。除了`Dispatchers.Unconfined`之外，所有调度程序都将调用 dispatch，其`isDispatchNeeded`函数覆盖(在`dispatch`之前调用)总是返回`false`。

# 生成的状态机

> **免责声明**:本文剩余部分显示的代码不会与编译器生成的字节码完全匹配。它将是足够精确的 Kotlin 代码，使您能够理解内部真正发生了什么。这种表示是由 Coroutines 1 . 3 . 3 版生成的，可能会在库的未来版本中发生变化。

Kotlin 编译器将识别函数何时可以内部挂起。每个悬挂点将被表示为有限状态机中的一个状态。这些状态由编译器用标签表示:

```
fun loginUser(userId: String, password: String, completion: Continuation<Any?>) {
  // **Label 0** -> first execution
  val user = userRemoteDataSource.logUserIn(userId, password) // **Label 1** -> resumes from userRemoteDataSource
  val userDb = userLocalDataSource.logUserIn(user) // **Label 2** -> resumes from userLocalDataSource
  completion.resume(userDb)
}
```

为了更好地表示状态机，编译器将使用一个`when`语句来实现不同的状态:

```
fun loginUser(userId: String, password: String, completion: Continuation<Any?>) {
  when(label) {
    **0 -> { // Label 0 -> first execution**
        userRemoteDataSource.logUserIn(userId, password)
    }
 **1 -> { // Label 1 -> resumes from userRemoteDataSource**
        userLocalDataSource.logUserIn(user)
    }
 **2 -> { // Label 2 -> resumes from userLocalDataSource**
        completion.resume(userDb)
    }
    else -> throw IllegalStateException(...)
  }
}
```

这个代码是不完整的，因为不同的州没有办法共享信息。编译器将在函数中使用相同的`Continuation`对象来实现。这就是为什么`Continuation`的泛型是`Any?`而不是原函数的返回类型(即`User`)。

此外，编译器将创建一个私有类，1)保存所需的数据，2)递归调用`loginUser`函数以恢复执行。您可以查看下面生成的类的近似值。

> **免责声明**:注释不是由编译器生成的。我添加它们是为了解释它们做什么，并使遵循代码更容易。

```
fun loginUser(**userId: String?, password: String?, completion: Continuation<Any?>**) { class **LoginUserStateMachine**(
    // completion parameter is the callback to the function 
    // that called loginUser
    completion: Continuation<Any?>
  ): **CoroutineImpl(completion)** { // Local variables of the suspend function
    var user: User? = null
    var userDb: UserDb? = null // Common objects for all CoroutineImpls
    var result: Any? = null
    var label: Int = 0 // this function calls the loginUser again to trigger the
    // state machine (label will be already in the next state) and
    // result will be the result of the previous state's computation
    override fun invokeSuspend(result: Any?) {
      this.result = result
      **loginUser(null, null, this)**
    }
  }
  ...
}
```

由于`invokeSuspend`将仅使用`Continuation`对象的信息再次调用`loginUser`，所以`loginUser`函数签名中的其余参数变得可空。此时，编译器只需要添加如何在状态之间移动的信息。

它需要做的第一件事是知道 1)这是第一次调用该函数，还是 2)该函数已经从先前的状态恢复。它通过检查传入的延续是否属于类型`LoginUserStateMachine`来实现:

```
fun loginUser(userId: String?, password: String?, completion: Continuation<Any?>) {
  ... val **continuation** = completion as? LoginUserStateMachine ?: LoginUserStateMachine(completion) ...
}
```

如果是第一次，它将创建一个新的`LoginUserStateMachine`实例，并将接收到的`completion`实例存储为一个参数，以便它记住如何恢复调用这个实例的函数。如果不是，它将继续执行状态机(挂起功能)。

现在，让我们看看编译器为在状态之间移动和共享信息而生成的代码。

花些时间浏览上面的代码，看看是否能发现与前面代码片段的不同之处。让我们看看编译器生成了什么:

*   `when`语句的参数是来自`LoginUserStateMachine`实例内部的`label`。
*   每次处理一个新状态时，都会进行一次检查，以防这个函数被挂起时出现故障。
*   在调用下一个挂起函数(即`logUserIn`)之前，`LoginUserStateMachine`实例的`label`更新到下一个状态。
*   当这个状态机内部调用另一个挂起函数时，`continuation`(类型为`LoginUserStateMachine`)的实例作为参数传递。要调用的 suspend 函数也已经被编译器转换了，它是另一个像这样的状态机，它接受一个 continuation 对象作为参数！当该挂起功能的状态机完成时，它将恢复该状态机的执行。

最后一个状态是不同的，因为它必须继续执行调用这个状态的函数，正如您在代码中看到的，它调用存储在`LoginUserStateMachine`中的`cont`变量上的 resume(在构造时):

如您所见，Kotlin 编译器为我们做了很多事情！从这个暂停功能:

```
suspend fun loginUser(userId: String, password: String): User {
  val user = userRemoteDataSource.logUserIn(userId, password)
  val userDb = userLocalDataSource.logUserIn(user)
  return userDb
}
```

编译器为我们生成了这一切:

Kotlin 编译器将每个挂起函数转换为状态机，每次函数需要挂起时使用回调进行优化。

现在知道了编译器在幕后做什么，你就能更好地理解为什么一个挂起函数在它启动的所有工作完成之前不会返回。还有，代码如何在不阻塞线程的情况下挂起:函数恢复时需要执行什么的信息存储在`Continuation`对象中！