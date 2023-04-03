# 关于协程的一点思考

> 原文：<https://blog.kotlin-academy.com/a-little-reflection-about-coroutines-34050cbc4fe6?source=collection_archive---------3----------------------->

![](img/b752fa3c0d918ae6eb9b90321252dccd.png)

Code reflection

最近有人问我，如果后端服务不再存在， [HttpMocker](https://github.com/speekha/httpmocker) 是否有可能自动模仿现有 Android 应用的 REST API。我在[的上一篇文章](/httpmock-my-first-oss-library-5bae8adbccf4)中解释了如何定义相应的场景，但这里的想法是这样的:假设我们已经有了[改进的](https://square.github.io/retrofit/)接口来访问服务，并且用于映射 JSON 响应的数据类都有默认参数，我们是否可以找到一种方法来返回所有 API 调用的默认对象，而无需为每个调用编写特定的场景？

让我们用一个具体的例子来说明:

*   我们有一个使用协程的服务接口来发出网络请求并解析结果:

```
interface ApiEndPoints { @GET("/simplePath")
    suspend fun simplePath(): SimpleObject}
```

*   和一个看起来像这样的`SimpleObject`数据类:

```
data class SimpleObject(
    val stringField: String = "a simple object"
)
```

经过一点小修小补，似乎有可能实现一个针对 *HttpMocker* 的通用场景，可以解决大多数可能性，但是我认为这个选项并不是最理想的:它意味着对返回类型的一些了解，最重要的是，它意味着将数据类序列化为 JSON 的一种方式(这不一定是这样的:您可能使用了一些特定的注释或解析器来将 JSON 流转换为对象，但是不包括用于反向操作的相应代码)。

如果您必须研究*改进*服务来创建返回对象，为什么要麻烦序列化只是为了立即反序列化它们呢？在这种情况下，更有意义的做法是完全放弃 *HttpMocker* (它在 *OkHttp* 级别工作:我将在另一篇文章中讨论该选项),而是通过提供自己的服务接口实现，在*改进*级别拦截调用。**换句话说，我们可以通过使用反射编写我们自己的服务接口实现来尝试模仿 refuge 所做的事情。**那么，让我们来看看如何做到这一点。

# 实现接口

因为我们有一个接口来定义我们的服务，但是没有具体的实现(它是由*翻新*动态提供的)，所以用我们的来替换那个调用是相当容易的。所以让我们从简单的开始，去掉接口中的关键字`suspend`,这样我们就不用担心协程了:

```
interface ApiEndpoints { @GET("/simplePath")
    fun simplePath(): SimpleObject}
```

我们的应用运行在一个 JVM 兼容的平台上，这意味着我们可以使用该接口的一个`Proxy`实例(这也是*改进*实际上所做的)。`Proxy`是来自 JVM 的一个类，可以动态实现任何接口。每当在该代理上调用一个方法时，它会将调用委托给一个`InvocationHandler`，后者必须根据方法签名和参数提供正确的结果。

在我们的例子中，这个处理程序相当简单:它检索预期的返回类型并不带参数地调用其构造函数，以便对每个字段使用默认值。

因此，一个简单的实现可能如下所示:

```
inline fun <reified T : Any> createService(): T {
    val service = T::class.java
    return **Proxy.newProxyInstance(**
        service.classLoader,
        *arrayOf*(service)
    **)** **{** _: Any, method: Method, _: Array<out Any> **->
        val type = method.returnType
        val constructor = type.constructors.first { it.parameterCount == 0 }
        constructor.newInstance()**
    **}** as T
}
```

到目前为止，一切顺利:调用该代码有效，对`service.simplePath()` 的调用返回一个`stringField`值为`"a simple object"`的`SimpleObject`。

# 用花冠调味

到目前为止这很简单，但是*改型*不提供那样的同步接口。通常，如果您使用 *RxJava* ，您需要返回一个`Call<SimpleObject>`(您可以同步或异步调用它)，一个`Single<SimpleObject>`，或者如果您喜欢使用协程，只需将您的方法声明为 suspendable 即可。

后一种语法似乎是最容易使用的，因为它不包含任何额外的泛型类型。让我们把关键字`suspend`放回去，运行我们的代码:

`java.lang.ClassCastException: java.lang.Object cannot be cast to SimpleObject`

看起来我们的代码设法找到了一个返回类型并调用了它的构造函数。但是为什么我们的结果不再是一个`SimpleObject`而是一个`Object`？这与幕后的协程有关。

在 Kotlin 方面，看起来方法签名并没有改变:相同的名称、相同的参数、相同的返回类型……但是在 JVM 方面，它改变了:增加了一个`Continuation`参数，返回类型变成了`Object`。这两种变化都允许函数随意停止和启动。当协程中断时，会返回协程的当前状态，并将其用作恢复执行的参数。

由于这种编译魔法，参数和返回类型不再能通过 [Java 反射](https://docs.oracle.com/javase/8/docs/technotes/guides/reflection/index.html)被信任。现在是时候通过将 Java `Method`对象转换为 Kotlin `KFunction`来切换到 [Kotlin 反射](https://kotlinlang.org/docs/reference/reflection.html)了。谢天谢地，一个`kotlinFunction`扩展帮我们做到了:

```
val function: KFunction<*>? = method.kotlinFunction
```

Kotlin 反射 API 类似于 Java 自省 API，但它允许根据 Kotlin 编译器添加的所有额外元数据，从 Kotlin 的角度操作您的代码。

读取返回类型现在会返回在 Kotlin 代码中声明的类型(作为一个`KType`)，不管字节代码看起来像什么。此外，寻找不带参数的构造函数不再有效，因为我们的类只有一个带一个(可选)参数的构造函数。但是，您可以直接访问主构造函数，而不提供任何参数:

```
inline fun <reified T : Any> createService(): T {
    val service = T::class.java
    return Proxy.newProxyInstance(
        service.classLoader,
        *arrayOf*(service)
    ) {_: Any, method: Method, _: Array<out Any> -> **method.kotlinFunction?.let {
            val type: KClass<*> = it.returnType.jvmErasure
            val constructor: KFunction<Any> = type.primaryConstructor ?: error("No primary constructor") 
            constructor.callBy(*emptyMap*())**
        }
    }as T
}
```

老实说，我们可以通过直接调用不带参数的`createInstance`来完全跳过构造函数解析(同样的方法，我们可以在 Java 实现上调用`newInstance()`，而不是查找构造函数)，但是值得注意的是，Kotlin 处理带可选参数的构造函数(或方法)的方式与 Java 不同，因为这个概念在 Java 中并不存在。所以我们可以进一步简化我们的代码:

```
inline fun <reified T : Any> createService(): T {
    val service = T::class.java
    return Proxy.newProxyInstance(
        service.classLoader,
        *arrayOf*(service)
    ) {_: Any, method: Method, _: Array<out Any> -> **method.kotlinFunction
            ?.returnType
            ?.jvmErasure
            ?.createInstance()**
    }as T
}
```

顺便提一下，如果我们没有`kotlinFunction`扩展，通过匹配名称和参数，根据 JVM 方法签名识别正确的 Kotlin 函数仍然很容易，只要我们注意到一些差异:

*   在 Kotlin 中，`KFunctions`有一个额外的第一个参数`INSTANCE`来指定函数本身。
*   因为我们的函数是 suspendable，所以会在 JVM 端添加`Continuation`参数。

最后，使用 Kotlin 反射而不是 Java 反射的好处是，我们的代码现在可以同时用于函数的同步和异步版本。

# 结论

反射是一种以通用方式解决某些问题的聪明方法，因为 Java 和 Kotlin 都提供了操作类、对象、属性、函数等的功能。当您的代码只在 JVM 上运行时，很容易认为您可以很容易地混合它们。但事实是，由于 Kotlin 中对象和函数工作方式的一些差异，有一些微妙的特性使得在两个世界之间导航很危险。将协程添加到组合中更进了一步，因为它揭示了给我们带来这些强大特性的巧妙的字节码操作。

另一方面， [Kotlin reflection](https://kotlinlang.org/docs/reference/reflection.html) 提供了一个 API，它不仅与你的 Kotlin 代码一致，还公开了最初说服我们放弃 Java 的语言特性。不过，代价是这个 API 不是默认 stdlib 的一部分，需要你在项目中包含一个额外的包`[kotlin-reflect.jar](https://mvnrepository.com/artifact/org.jetbrains.kotlin/kotlin-reflect)`，到目前为止，这意味着你的应用程序增加了 2.7MB，而 Java reflection API 是 JVM 的一部分(虽然有[较小的替代包](https://github.com/Kotlin/kotlinx.reflect.lite)，但功能有限)。

合乎逻辑的结论是，如果您试图限制应用程序使用的磁盘空间，在使用 Kotlin 反射之前，您可能需要三思。但是，如果反射是不可避免的途径，那么与旧的、有些不足的 Java 反射相比，它可能值得额外的重视。

# 单击👏说“谢谢！”并帮助他人找到这篇文章。

了解卡帕头最新的重大新闻。学院，[订阅时事通讯](https://kotlin-academy.us17.list-manage.com/subscribe?u=5d3a48e1893758cb5be5c2919&id=d2ba84960a)，[观察 Twitter](https://twitter.com/ktdotacademy) 并在 medium 上关注我们。

如果您需要 Kotlin 工作室，请查看我们如何帮助您: [kt.academy](https://www.kt.academy/) 。

[![](img/3146970f03e44cb07afe660b0d43e045.png)](https://kotlin-academy.us17.list-manage.com/subscribe?u=5d3a48e1893758cb5be5c2919&id=d2ba84960a)