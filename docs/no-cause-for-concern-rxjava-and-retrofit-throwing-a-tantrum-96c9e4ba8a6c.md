# 没有理由担心——rx Java 和翻新会发脾气

> 原文：<https://medium.com/square-corner-blog/no-cause-for-concern-rxjava-and-retrofit-throwing-a-tantrum-96c9e4ba8a6c?source=collection_archive---------0----------------------->

> 注意，我们已经行动了！如果您想继续了解 Square 的最新技术内容，请访问我们的新家[https://developer.squareup.com/blog](https://developer.squareup.com/blog)

上周，我们在 JDK 的`Throwable`类中发现了一个有趣的 API 设计问题，这个问题导致了 RxJava 中的 bug 和改型。这是一篇关于我们如何发现这些错误的文章。

# 装配跟踪

周一早上， [Nelson Osacky](https://medium.com/u/85419ad6ecd1?source=post_page-----96c9e4ba8a6c--------------------------------) 打开一个 pull 请求，在 Square Register Android 的 **debug** 构建中启用 RxJava [汇编跟踪](http://reactivex.io/RxJava/javadoc/rx/plugins/RxJavaHooks.html#enableAssemblyTracking())。

> 程序集跟踪通过报告失败的观察点是在哪里创建的，使得 Rx 调试更加容易。

```
public class RegisterDevelopmentApp extends RegisterApp {
  @Override public void onCreate() {
    **RxJavaHooks.enableAssemblyTracking();**
    super.onCreate();
  }
}
```

根据`RxJavaHooks.enableAssemblyTracking()` Javadoc:

> 当源或操作符被实例化时，设置捕获当前 stacktrace 的钩子，将它保存在一个字段中以便调试，并改变传递的异常以保留此 stacktrace。

这里有一个例子([感谢大卫](https://mobile.twitter.com/dwursteisen/status/773842208164311040)):

```
Observable.*just*(1, 2, 3)
  .single()
  .subscribe();
```

该接收链出现故障:

```
rx.exceptions.OnErrorNotImplementedException: Sequence contains too many elements
  at ... lots of Rx internals
  at rx.**Observable.subscribe**(Observable.java:9989)
  at com.squareup.Example.test(Example.java:116)
Caused by: java.lang.**IllegalArgumentException: Sequence contains too many elements**
  ... 38 more
```

当我们调用 subscribe 时会引发一个异常，所以 stacktrace 显示在 subscribe 时发生了错误**。我们试图理解错误消息，并弄清楚所有这些 Rx 内部类是关于什么的。**

让我们启用装配跟踪:

```
RxJavaHooks.**enableAssemblyTracking()**;
Observable.just(1, 2, 3)
  .single()
  .subscribe();
```

最后我们得到了一个额外的原因:

```
rx.exceptions.OnErrorNotImplementedException: Sequence contains too many elements
  at ... lots of Rx internals
  at rx.Observable.subscribe(Observable.java:9989)
  at com.squareup.Example.test(Example.java:116)
  at ...
Caused by: java.lang.IllegalArgumentException: Sequence contains too many elements
  ... 38 more
Caused by: rx.exceptions.AssemblyStackTraceException: **Assembly trace**:
  at rx.Observable.create(Observable.java:98)
  at rx.Observable.lift(Observable.java:251)
  at rx.**Observable.single**(Observable.java:9337)
  at com.squareup.Example.test(Example.java:115)
```

现在很清楚，这一失败是由于创造了`single()`可观测性。`single()`期望源可观测物仅发射单一项目。

让我们启用装配跟踪！

# 构建失败

```
public class RegisterDevelopmentApp extends RegisterApp {
  @Override public void onCreate() {
    **RxJavaHooks.enableAssemblyTracking();**
    super.onCreate();
  }
}
```

啊哈，这个一行的拉请求破坏了我的团队写的一个 UI 测试。我看着失败的代码:

```
connectService.getClientSettings(clientId)
  .subscribe(
    (response) -> { ... },
    (throwable) -> {
      if (throwable instanceof RetrofitError) {
        ...
      } else {
        **Timber.d(throwable);**
        **throw new AssertionError("Should not happen", throwable);**
      }
    }
  );
```

奇怪的是，一个翻新(1.x)调用应该只引发一个`RetrofitError`，而我们得到的是别的东西。以下是日志:

```
java.lang.IllegalStateException: Cause already initialized
    at rx.internal.operators.OnSubscribeOnAssembly$OnAssemblySubscriber
.onError(**OnSubscribeOnAssembly.java:118**)
    at ...
    at com.android.internal.os.ZygoteInit.main(ZygoteInit.java:616)
Caused by: rx.exceptions.**AssemblyStackTraceException**: Assembly trace:
    at ...
    at com.android.internal.os.ZygoteInit.main(ZygoteInit.java:616)
```

`IllegalStateException`的实际堆栈跟踪丢失。

# 深入 RxJava

我看看`OnSubscribeOnAssembly.java` [第 118 行](https://github.com/ReactiveX/RxJava/blob/4ae4a40a2f493978851035e18a6159380f15573f/src/main/java/rx/internal/operators/OnSubscribeOnAssembly.java#L118):

```
@Override public void onError(Throwable e) {
  **new AssemblyStackTraceException(**stacktrace**).attachTo(**e**);**
  ...
}
```

呃。这个`AssemblyStackTraceException`是什么？

> 一个 RuntimeException，它是无堆栈的，但通过跟踪操作符的程序集位置来保持文本堆栈跟踪。

有意思。`AssemblyStackTraceException.attachTo()`是做什么的？

```
public void attachTo(Throwable exception) {
  for (;;) {
    if (exception.getCause() == null) {
      exception.initCause(this);
      return;
    }
    exception = exception.getCause();
  }
}
```

每当一个`Observable`被创建时，`OnSubscribeOnAssembly`就会捕获一个堆栈跟踪并持有它。然后，当在相应的 Rx 链中抛出异常时，会在异常链的底部添加一个`AssemblyStackTraceException` 作为原因，并带有一个消息字符串，其中包含创建`Observable`的堆栈跟踪。整洁！

我们在最初的例子中看到了这样的结果:

```
rx.exceptions.OnErrorNotImplementedException: Sequence contains too many elements
  at ... lots of Rx internals
  at rx.Observable.subscribe(Observable.java:9989)
  at com.squareup.Example.test(Example.java:116)
  at ...
Caused by: java.lang.IllegalArgumentException: Sequence contains too many elements
  ... 38 more
Caused by: rx.exceptions.AssemblyStackTraceException: **Assembly trace**:
  at rx.Observable.create(Observable.java:98)
  at rx.Observable.lift(Observable.java:251)
  at rx.**Observable.single**(Observable.java:9337)
  at com.squareup.Example.test(Example.java:115)
```

# 原因已经初始化

既然我已经理解了程序集跟踪是如何工作的，我再次查看日志:

```
java.lang.**IllegalStateException: Cause already initialized**
    at rx.internal.operators.OnSubscribeOnAssembly$OnAssemblySubscriber
.onError(OnSubscribeOnAssembly.java:118)
    at ...
    at com.android.internal.os.ZygoteInit.main(ZygoteInit.java:616)
Caused by: rx.exceptions.AssemblyStackTraceException: Assembly trace:
    at ...
    at com.android.internal.os.ZygoteInit.main(ZygoteInit.java:616)
```

我仍然不知道这个`IllegalStateException`来自哪里，因为我没有它的堆栈。消息称**原因已经初始化**，我们刚刚看到一段代码试图这样做:

```
public void attachTo(Throwable exception) {
  for (;;) {
    if (exception.getCause() == null) {
      exception.**initCause**(this);
      return;
    }
    exception = exception.getCause();
  }
}
```

我打开`Throwable.initCause()`:

```
public void initCause(Throwable cause) {
 if (**this.cause != this**) {
   throw new IllegalStateException("Cause already initialized");
 }
 this.cause = cause;
}
```

Tada！我刚发现了我们的神秘例外。`cause`和`this`到底是怎么回事？让我们看看`Throwable`是如何构造的:

```
public class Throwable {
  /**
   * The throwable that caused this throwable to get thrown, or null
   * if this throwable was not caused by another throwable, or if 
   * the causative throwable is unknown. **If this field is equal to
   * this throwable itself, it indicates that the cause of this
   * throwable has not yet been initialized**.
   */
  private Throwable **cause = this**; public Throwable() {
  } public Throwable(Throwable cause) {
    this.cause = cause;
  }
}
```

可以在没有原因的情况下构造一个`Throwable`，在这种情况下`cause`字段被设置为`this`以标记原因尚未初始化。然后，您可以稍后调用`initCause()`…但前提是原因尚未初始化。

> 可抛出的原因只能设置一次。

我们崩溃是因为我们在呼叫`initCause()`但是原因已经设定好了。让我们再看一下代码:

```
public void attachTo(Throwable exception) {
  for (;;) {
    if (**exception.getCause() == null**) {
      exception.initCause(this);
      return;
    }
    exception = exception.getCause();
  }
}
```

我们将沿着异常链往下走，直到找到一个有`null`原因的异常。然后我们在上面调用`initCause()`。`getCause()`什么时候返回 null？

```
public class Throwable {
  /**
   * Returns the cause of this throwable or **null if the**
   * **cause is nonexistent or unknown**.
   */
  public Throwable getCause() {
    return (cause == this ? null : cause);
  }
}
```

等一下。原因可能是不存在的**或**也可能是未知的。那是两码事！

> 当原因字段设置为自身时，原因不存在。当原因字段设置为空时，原因未知。

就 Java 代码而言，区别如下:

```
new Throwable(); // nonexistent cause (not initialized)
Throwable cause = null;
new Throwable(cause); // unknown cause (initialized to null)
```

在这两种情况下，`getCause()`都返回`null`。但是，如果原因未知(初始化为`null`，那么`initCause()`会抛出一个`IllegalStateException`。

# 注定要失败的努力

能否修复 RxJava，使其在原因未知的情况下不调用`initCause()`？嗯，事实证明，这是没有办法检查的。

> JDK 没有提供 API 来确定可抛出的原因是不存在还是未知。

唯一的选择是尝试报告失败。我在 RxJava 中打开一个[拉请求](https://github.com/ReactiveX/RxJava/pull/4740)。

```
public void attachTo(Throwable exception) {
  for (;;) {
    if (exception.getCause() == null) {
      try {
        exception.initCause(this);
      } **catch (IllegalStateException ignored)** {
        onError(new RuntimeException("Unknown cause", exception);
      }
      return;
    }
    exception = exception.getCause();
  }
}
```

在阅读了 Javadoc 之后，我意识到`initCause()`的存在是为了向后兼容的问题，在这种情况下，遗留异常类缺少一个构造函数。

> initCause()应该只在构造一个异常后调用，它不是为向异常链添加元数据而设计的。

虽然汇编跟踪似乎是建立在一个黑客，好处显然超过了这种未知原因的边缘情况的缺点。

# 根本原因分析

既然 RxJava 在程序集跟踪失败时有了适当的错误报告，我就可以弄清楚触发这次调查的 UI 测试失败中发生了什么:

```
connectService.getClientSettings(clientId)
    .subscribe()
```

我们正在测试一个 HTTP 错误场景，所以 retrieval 创建了一个带有`RetrofitError.httpError()`的异常:

```
public class RetrofitError extends RuntimeException {
  public static RetrofitError httpError(Response response) {
    return new RetrofitError(response, **null**);
  } RetrofitError(Response response, Throwable exception) {
    super(**exception**);
    this.response = response;
  }
}
```

答对了。`RetrofitError.httpError()` 创建一个原因未知的异常，这就是装配跟踪失败的原因。

`RetrofitError`只存在于改型 1.x 中，所以我提交了一个 [pull 请求](https://github.com/square/retrofit/pull/2057)在 1.x 分支上修复它。不会有新的公开发布，因为 Retrofix 2.x 现在已经出来两年了。

Square Register 是我们最后一个没有迁移到改型 2 的应用程序。我们将很快完成迁移工作。与此同时，我们将使用一个改进 1.x 的内部版本。

# 收场白

调查一次 UI 测试失败让我们踏上了一段有趣的旅程！今天，我们了解到:

*   一个`Throwable`可以有一个不存在或未知的原因；那是两个不同的东西，没有 API 来判断哪个是哪个。
*   程序集跟踪使 Rx 调试更容易，并以一种非预期的方式使用`Throwable` API 将元数据附加到 stracktrace。
*   将`null`作为一个原因传递给`Throwable`构造函数看起来是无害的，但多年后会导致微妙的错误。

欢迎提供更多见解或提出问题！