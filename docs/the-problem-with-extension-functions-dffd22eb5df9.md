# 扩展函数的问题是

> 原文：<https://blog.kotlin-academy.com/the-problem-with-extension-functions-dffd22eb5df9?source=collection_archive---------3----------------------->

![](img/d7ac68c5fcecbc32cf157dcc4580f583.png)

扩展函数——像成员函数一样使用，但是在类之外定义的函数——是一个很好的特性，受到很多人的喜爱。虽然它们开始被过度使用，但导致了我们还不知道如何解决的问题。相反，你可以看到一些图书馆是如何使用棘手而糟糕的解决方案来避免这些问题的。这是关于扩展函数的黑暗面的故事。

```
**fun** CharSequence.isBlank(): Boolean = 
    **length** == 0 || *indices*.*all* **{ this**[**it**].*isWhitespace*() **}****"  "**.*isBlank*() *// true* **" :) "**.*isBlank*() *// false*
```

![](img/23164fa051ae361715b2aed9af583921.png)

# 为什么我们需要扩展函数？

在某种程度上，扩展函数的行为类似于成员函数。有一些不同——比如扩展是在类型上，而不是在类上(所以我们可以在`String?`或`List<Int>`上进行扩展)，或者它们不是虚拟的(所有的不同都在 [Effective Kotlin](https://leanpub.com/effectivekotlin/) 的第 40 项中详细解释)。尽管通常扩展的使用类似于成员函数。例如，所有的收集处理功能都是作为`List<T>`的扩展来实现的:

```
listOf(1,2,3)
    .filter **{** it % == 2 **}** .map **{** it * 2 **}** .sort()**fun** <T> Iterable<T>.filter(predicate: (T) -> Boolean): List<T> {
    **return** *filterTo*(ArrayList<T>(), predicate)
}
```

修改可变集合也是如此:

```
**val** mutable = *arrayListOf*(4,2,3)
mutable.sort()**fun** <T : Comparable<T>> MutableList<T>.sort() {
    **if** (**size** > 1) java.util.Collections.sort(**this**)
}
```

它们还用于定义类外部的运算符重载:

```
*listOf*(1,2) + *listOf*(3,4)**operator fun** <T> Collection<T>.plus(elements: Iterable<T>): List<T>{
    **if** (elements **is** Collection) {
        **val** result = ArrayList<T>(**this**.size + elements.size)
        result.addAll(**this**)
        result.addAll(elements)
        **return** result
    } **else** {
        **val** result = ArrayList<T>(**this**)
        result.addAll(elements)
        **return** result
    }
}
```

虽然还有一个更重要的扩展函数使用。**它们是 Kotlin** 中 DSL 定义的基础。在 DSLs 函数中，打开 lambda 表达式，在它们内部，有其他函数被另一个 lambda 表达式调用。但问题是这个函数调用的本地化很重要，因为它们在修改父函数。想想下面这个简单的 DSL:

```
**fun** View.makeView() = *table* **{** *tr* **{** *td* **{** +**"A" }** *td* **{** +**"B" }
    }** *tr* **{** *td* **{** +**"C" }** *td* **{** +**"D" }
    }
}**
```

需要注意的是`tr`只能用在`table`的范围内，而且它修饰了它(所以有关系)。与`td`和`tr`相同。这是可能的，因为`table`、`tr`和`td`中的函数不是普通函数，而是带有接收器的函数类型，并且`table`、`tr`和`td`是接收器上的方法(成员或扩展函数)。

```
**fun** View.makeView() = this@makeView.*table* **{** this@*table*.*tr* **{** this@*tr*.*td* **{** +**"A" }** this@*tr*.*td* **{** +**"B" }
    }** this@*table*.*tr* **{** this@*tr*.*td* **{** +**"C" }** this@*tr*.*td* **{** +**"D" }
    }
}****fun** View.table(init: Table.()->Unit) { /*...*/ }
**fun** Table.tr(init: Tr.()->Unit) { /*...*/ }
**fun** Table.td(init: Td.()->Unit) { /*...*/ }
```

为了更好地理解这个概念，下面是一个非常简单的 Kotlin DSL 的完整实现:

```
**abstract class** View(
   **var childrens**: List<View> = *emptyList*()
) {
    *// This already must be a member extension
    // because we need 2 receivers: Text and String* **operator fun** String.unaryPlus() {
        **childrens** += Text(**this**)
    }
}**class** Table: View()
**class** Tr: View()
**class** Td: View()
**class** Div: View() 
**class** Text(**var text**: String): View()**fun** View.table(init: Table.()->Unit) {
    **val** view = Table()
    view.init()
    **childrens** += view
}**fun** View.div(init: Div.()->Unit) {
    **val** view = Div()
    view.init()
    **childrens** += view
}**fun** Table.tr(init: Tr.()->Unit) {
    **val** view = Tr()
    view.init()
    **childrens** += view
}**fun** Table.td(init: Td.()->Unit) {
    **val** view = Td()
    view.init()
    **childrens** += view
}**fun** View.makeView() = *div* **{** *table* **{** *tr* **{** *td* **{** +**"A" }** *td* **{** +**"B" }
        }** *tr* **{** *td* **{** +**"C" }** *td* **{** *div* **{** +**"D" } }
        }
    }
}**
```

(更好的是，我们可以使用 apply 来简化这种语法，这是 DSL 的一种流行的习语)

```
**fun** View.table(init: Table.()->Unit) {
    **childrens** += Table().*apply*(init)
}**fun** View.div(init: Div.()->Unit) {
    **childrens** += Div().*apply*(init)
}**fun** Table.tr(init: Tr.()->Unit) {
    **childrens** += Tr().*apply*(init)
}**fun** Table.td(init: Td.()->Unit) {
    **childrens** += Td().*apply*(init)
}
```

我们可以创建`table`、`div`、`tr`和`td`成员函数，但这只是指定接收者的不同方式。问题是，现在接收者在表达关系中有一个非常重要的角色，问题之一是我们只有一个(或两个)接收者的空间，不能再多了。

[![](img/0742a8ad0cfd3851db2d28061bf6f214.png)](https://leanpub.com/effectivekotlin/c/3YYtCtqCC6a4)

# 许多关系

如果你在 DSL 中有一些重复的行为，你可以使用一个函数来提取它，但是为了保持与父类的关系，它必须是一个扩展函数。

例如，当我们在 HTML DSL 中有一个重复块时:

```
**body {
    div**(classes = **"main-head"**) **{** +**"Title1"
    }
}****body {
    div**(classes = **"main-head"**) **{** +**"Title2"
    }
}**
```

我们可以用一个函数提取它:

```
**body {** *head*(**"Title1"**)
**}****body {** *head*(**"Title2"**)
**}****fun** BODY.head(title: String) {
    **div**(classes = **"main-head"**) **{** +title
    **}** }
```

同样，当我们在 Ktor DSL 中有一个重复响应模块时:

```
**get**(**"endpoint1"**) **{** ok()
**}****get**(**"endpoint2"**) **{** ok()
**}****suspend fun** PipelineContext<Unit, ApplicationCall>.ok() {
    *call*.*respond*(HttpStatusCode.**OK**)
}
```

但是如果我们想要提取一个属于 2 DSLs 的行为呢？比如构建 HTML 和使用关于呼叫的数据？

```
**get**(**"endpoint1"**) **{** *call*.*respondHtml* **{
        body {
            val** name = *call*.**parameters**[**"name"**]
            *application*.**environment**.**log**.info(**"Parameter name is $**name**"**)
            **div**(classes = **"param-display"**) **{** +**"Parameter name is $**name**"
            }
        }
    }
}
get**(**"endpoint2"**) **{** *call*.*respondHtml* **{
        body {
            val** surname = *call*.**parameters**[**"surname"**]
            *application*.**environment**.**log**.info(**"Parameter surname is $**surname**"**)
            **div**(classes = **"param-display"**) **{** +**"Parameter surname is $**surname**"
            }
        }
    }
}**
```

我们只能隐式传递一个接收者。另一个需要作为参数显式传递(如果你真的想这么做的话)。

```
**get**(**"endpoint1"**) **{** *call*.*respondHtml* **{
        body {** *parameterDisplay*(**this**@get, **"name"**)
        **}
    }
}
get**(**"endpoint2"**) **{** *call*.*respondHtml* **{
        body {** *parameterDisplay*(**this**@get, **"surname"**)
        **}
    }
}****private fun** BODY.parameterDisplay(
    pipelineContext: PipelineContext<Unit, ApplicationCall>,
    paramName: String
) {
    **val** value = pipelineContext.*call*.**parameters**[paramName]
    pipelineContext.*application*.**environment**.**log**.info(**"Parameter $**paramName **is $**value**"**)
    **div**(classes = **"param-display"**) **{** +**"Parameter $**paramName **is $**value**"
    }** }
```

这看起来像一个可怕的反模式，但这样的想法听起来并不不可思议。我们越来越频繁地使用 DSL，我们想要提取功能，但是我们已经有了这样一种情况，即一个接收器已经被占用，而我们不能按照我们想要的方式来做。

# 只是多了一个接收器

有一种方法可以再隐藏一个接收者——使用成员扩展。它已经在`table`的例子中使用了。`unaryPlus`需要成为`String`上的运算符，并且需要修改类型`View`的父类型。唯一的解决方案是使它成为成员扩展:

```
**abstract class** View(
   **var childrens**: List<View> = *emptyList*()
) {
    *// This already must be a member extension
    // because we need 2 receivers: Text and String* **operator fun** String.unaryPlus() {
        **childrens** += Text(**this**)
    }
}*td* **{** +**"A" }** *div* **{** +**"B" }**
```

总的来说，这是一个可怕的模式，我在[有效科特林](https://leanpub.com/effectivekotlin/)的第 41 项中警告过它。这个技巧应该只在某些 DSL 内部使用，并且只在我们真正需要的时候使用。不应该成为一个标准。

# 结构化并发

一个流行的 DSL 例子是 Kotlin 协程中的结构化并发。它的工作方式是除了`runBlocking`(所以`launch`，`async`)之外的所有协程构建器都是`CoroutineScope`的扩展:

```
*runBlocking* **{** *launch* **{ 
        val** a = *async* **{** *getValue1*() **}
        val** b = *async* **{** *getValue2*() **}** *print*(a.await() + b.await())
    **}
}****public fun** CoroutineScope.launch(
    context: CoroutineContext = EmptyCoroutineContext,
    start: CoroutineStart = CoroutineStart.DEFAULT,
    block: **suspend** CoroutineScope.() -> Unit
): Job {
    **val** newContext = newCoroutineContext(context)
    **val** coroutine = **if** (start.isLazy)
        LazyStandaloneCoroutine(newContext, block) **else** StandaloneCoroutine(newContext, active = **true**)
    coroutine.start(start, coroutine, block)
    **return** coroutine
}**public fun** <T> CoroutineScope.async(
    context: CoroutineContext = EmptyCoroutineContext,
    start: CoroutineStart = CoroutineStart.**DEFAULT**,
    block: **suspend** CoroutineScope.() -> T
): Deferred<T> {
    **val** newContext = *newCoroutineContext*(context)
    **val** coroutine = **if** (start.**isLazy**)
        LazyDeferredCoroutine(newContext, block) **else** DeferredCoroutine<T>(newContext, active = **true**)
    coroutine.start(start, coroutine, block)
    **return** coroutine
}
```

这个`CoroutineScope`是一个由父节点生成，由子节点使用的作用域。因此，它形成了作为结构化并发基础的父子关系。它在每个协程函数中都是需要的，或者至少曾经是需要的，因为这个协程作用域正在建立这种关系。

尽管有必要在`CoroutineScope`上扩展所有这些功能，但这确实是一种限制。默认情况下，接收器位置被锁定。例如，看到集合处理是如何实现的(在`Iterable<T>`上的扩展函数)，您可能会怀疑我们应该为 Kotlin 通道提供类似的东西。尽管这不是官方文档建议实现处理器的方式:

```
fun CoroutineScope.produceNumbers() = produce<Int> {
   var x = 1 // start from 1
   while (true) {
   send(x++) // produce next
   delay(100) // wait 0.1s
   }
}fun CoroutineScope.launchProcessor(id: Int, channel: ReceiveChannel<Int>) = launch {
   for (msg in channel) {
   println(“Processor #$id received $msg”)
   } 
}val producer = produceNumbers()
repeat(5) { launchProcessor(it, producer) }
delay(950)
producer.cancel() // cancel producer coroutine and thus kill them all
```

你可能会说，这是因为信道代表了一种不同的抽象，它现在已经在`Flows`中很好地(实验性地)实现了:

```
flow.*filter* **{ it** % 2 == 0 **}** .*map* **{ it** * 2 **}**
```

这种关系显然存在。您可以在示例中看到，因为我在不同的上下文中启动了`collect`，所以它在不同的调度程序中执行，我也可以从外部取消它:

```
**import** kotlinx.coroutines.*
**import** kotlinx.coroutines.flow.*
@UseExperimental(InternalCoroutinesApi::**class**)
**fun** main() {
    *runBlocking* **{
        val** foo: Flow<Int> = *flow* **{
            for** (i **in** 1..100) {
                **val** threadName = Thread.currentThread().*name
                println*(**"Emitting $**i **at $**threadName**"**)
                emit(i)
                *delay*(500)
            }
        **}** .*filter* **{ it** % 2 == 0 **}** .*map* **{ it** * 2 **}

        val** consume: **suspend** (Int) -> Unit = **{** value: Int **->** *println*(**"Consumed $**value **at ${**Thread.currentThread().*name***}"**)

        **}

        val** job = *Job*()
        *launch*(job + *newSingleThreadContext*(**"MyThread"**)) **{** foo.*collect*(consume)
            */*
            Emitting 1 at MyThread
            Emitting 2 at MyThread
            Consumed 4 at MyThread
            Emitting 3 at MyThread
            */* **}** *delay*(1200)
        job.*cancelAndJoin*()
    **}** }
```

为什么为了使它成为可能，我们需要在这个`collect`功能和上下文之间有一些联系。它过去是通过`CoroutineScope`接收器传递的，但现在显然不是了。答案是这个问题以一种有点狡猾的方式被回避了。`CoroutineContext`(这是`CorouitneScope`接口的唯一组件)出现在 continuation 中(隐式传递给挂起函数)，并从那里获取。制作这种把戏的一种方法是暂停函数以获取上下文，然后立即恢复它。这就是`coroutineScope`目前的实现方式:

```
**suspend fun** <R> coroutineScope(block: **suspend** CoroutineScope.() -> R): R =
    *suspendCoroutineUninterceptedOrReturn* **{** uCont **->
        val** coroutine = ScopeCoroutine(uCont.**context**, uCont)
        coroutine.*startUndispatchedOrReturn*(coroutine, block)
    **}**
```

嗯……这是一个棘手的方法，但由于这一点，我们不需要接收者，但仍然与父母有关系。

```
**suspend fun** fakeOperation(): Int = **try** {
    *delay*(Long.**MAX_VALUE**)
    42
} **finally** {
    *println*(**"Cancelled"**)
}

**suspend fun** fakeOperations() = *coroutineScope* **{
    val** a = *async* **{** *fakeOperation*() **}
    val** b = *async* **{** *fakeOperation*() **}** a.await() + b.await()
**}

suspend fun** main() = *coroutineScope* **{
    val** job = *Job*()
    *launch*(job) **{
        val** ret = *fakeOperations*()
        *print*(ret)
    **}** *delay*(1000)
    job.*cancelAndJoin*()
    *// Cancelled
    // Cancelled* **}**
```

好吃吗？修正了一些重要的用例，但是使得子节点和父节点之间的关系更加隐含。不久前，Kotlin 为此推出了一个更强大的工具。现在，您可以在 Kotlin stdlib 中找到以下扩展属性:

```
*/**
 * Returns the context of the current coroutine.
 */* @SinceKotlin(**"1.3"**)
@Suppress(**"WRONG_MODIFIER_TARGET"**)
@InlineOnly
**public suspend inline val** *coroutineContext*: CoroutineContext
    **get**() {
        **throw** NotImplementedError(**"Implemented as intrinsic"**)
    }
```

由于这个原因，我们可以得到每个挂起函数的上下文:

```
**suspend fun** a() {
    *coroutineContext* }
```

示例用途:

```
**import** kotlin.coroutines.*coroutineContext***suspend fun** a() {
    *print*(*coroutineContext*[CoroutineName]?.**name**)
}**fun** main() {
    *runBlocking* **{** *withContext*(CoroutineName(**"A"**)) **{** *a*() *// A* **}** *withContext*(CoroutineName(**"B"**)) **{** *a*() *// B* **}
    }** }
```

它之所以存在，是因为挂起函数将延续作为一个参数传递给彼此，并且它包含这个上下文。所以我们可以说我们找到了传递另一个隐式值的方法。它解决了一般的问题吗？不完全是。

注意，挂起函数的实现方式有一些限制。挂起函数需要延续，所以只能在挂起函数中调用。这在非内联的 lambdas 中也可能是个问题。比方说，在 Ktor 框架中，我想基于来自数据库的数据构建 HTML:

```
**get**(**"editPublicWorkshop/{key}"**) **{
    val** key = *requireParameter*(**"key"**)
    *call*.*respondHtml* **{
        body {
            form {
                for** (lang **in** Languages.values()) {
                    **val** publicWorkshop = publicWorkshopsRepo.getPublicWorkshop(key, lang.**key**) // ERROR
                    +lang.**key
                    textArea**(cols = **"60"**, rows = **"5"**) **{
                        name**=lang.**key** +(publicWorkshop?.*toJson*() ?: **""**)
                    **}** }
                **button**(type = ButtonType.**submit**) **{** +**"Submit" }
            }
        }
    }
}**
```

看起来不错，但是不工作，因为`getPublicWorkshop`是一个悬浮函数。它可以在`get`的范围内工作，但不能在`form`的范围内工作。这是解决问题的方法:

```
**get**(**"editPublicWorkshop/{key}"**) **{
    val** key = *requireParameter*(**"key"**)
    **val** langToPublicWorkshop = Languages.values()
        .*associate* **{** lang **->** lang *to* publicWorkshopsRepo.getPublicWorkshop(key, lang.**key**) **}***call*.*respondHtml* **{
        body {
            form {
                for** (lang **in** Languages.values()) {
                    +lang.**key
                    textArea**(cols = **"60"**, rows = **"5"**) **{
                        name**=lang.**key** +(langToPublicWorkshop[lang]?.*toJson*() ?: **""**)
                    **}** }
                **button**(type = ButtonType.**submit**) **{** +**"Submit" }
            }
        }
    }
}**
```

# Jetpack 撰写

另一个著名的 DSL 构建器是 Jetpack Compose:

```
@Composable
**fun** Counter() {
    **val** amount = +state **{** 0 **}**Column **{** Text(text = **"Counter demo"**)
        Button(text = **"Add"**, onClick = **{** amount.*value*++ **}**)
        Button(text = **"Subtract"**, onClick = **{** amount.*value*-- **}**)
        Text(text = **"Clicks: ${**amount.*value***}"**)
    **}** }
```

你可能想知道为什么它没有扩展接收器。它确实和许多其他元素在一起，但它们都很复杂，而`Composable`注释是作为一种更简单的方式引入的(在这里[阅读](http://intelligiblebabble.com/compose-from-first-principles/))。因此，该位置已经被锁定，您可以忘记`Composable`扩展功能。

[![](img/3abcc7bf2c50c85e442eda42ef18d587.png)](https://kt.academy/workshop/automaticTestingForBeginners)

# 关闭

在我的演讲“Kotlin 禁止做的事情”中，我把“隐藏太多”列为 Kotlin 开发者最大的错误之一。隐式传递 parent 的接收器在编程界是一个相对较新的想法，但它已经受到 Kotlin 开发人员的喜爱和高度使用。虽然我们开始触及边界，但我们只能通过一个接收器。这个限制会阻止我们以我们已经习惯的方式进行代码提取。

形式上，语言设计者可以允许一个以上的隐式接收者(比如在成员扩展中)，但是这个想法听起来非常可怕。不清楚什么东西来自哪里，修改了什么，优先级是什么…成员扩展已经是一个坏主意，允许更多的接收者就像增加了所有的问题。

另一方面，在一些用例中，受阻挡的接收器位置会造成限制。对于协程程序，创建者设法用更隐含的技巧来避免这个问题。现在`coroutineContext`为既有趣又有点可怕的可能性打开了大门。

目前，对于这种设计限制还没有通用的解决方案。我们的感知在进化，我们在寻找更好的方法。库设计者慢慢了解到强迫用户在接收器上操作的代价。这是一个有趣的话题，我会观察它的发展。

# 单击👏说“谢谢！”并帮助他人找到这篇文章。

了解卡帕头最新的重大新闻。学院，[订阅时事通讯](https://kotlin-academy.us17.list-manage.com/subscribe?u=5d3a48e1893758cb5be5c2919&id=d2ba84960a)，[观察 Twitter](https://twitter.com/ktdotacademy) 并在 medium 上关注我们。

如果您需要 Kotlin 工作室，请查看我们如何帮助您: [kt.academy](https://kt.academy/) 。

[![](img/3146970f03e44cb07afe660b0d43e045.png)](https://kotlin-academy.us17.list-manage.com/subscribe?u=5d3a48e1893758cb5be5c2919&id=d2ba84960a)