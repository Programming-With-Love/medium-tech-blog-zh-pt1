# RxJava 到 Kotlin 协同程序

> 原文：<https://medium.com/androiddevelopers/rxjava-to-kotlin-coroutines-1204c896a700?source=collection_archive---------0----------------------->

## 观察吊带

![](img/9b70afbd676d30efd44663d704c596f8.png)

[Threads](https://flic.kr/p/7TkwCt) by [Eric LaMontagne](https://www.flickr.com/photos/46435106@N06/)

好吧，我知道这是一个有点扣人心弦的标题，但这是我能想到的最好的了。这篇文章总结了我如何将一个大量使用 [RxJava](https://github.com/ReactiveX/RxJava) 的应用重构为一个也使用 Kotlin [协程](https://kotlinlang.org/docs/reference/coroutines.html)的应用。具体来说，我将讨论将[单个](http://reactivex.io/RxJava/2.x/javadoc/io/reactivex/Single.html) / [可能是](http://reactivex.io/RxJava/2.x/javadoc/io/reactivex/Maybe.html) / [可完成的](http://reactivex.io/RxJava/2.x/javadoc/io/reactivex/Completable.html)源代码切换到协程。

## 该应用程序

首先，简单介绍一下[应用](https://github.com/chrisbanes/tivi)的架构。我的大部分业务逻辑都建立在所谓的'[调用](https://github.com/chrisbanes/tivi/blob/master/calls/src/main/java/me/banes/chris/tivi/calls/Call.kt)'中:

```
**interface** Call<**in** Param, Output> {
    **fun** data(param: Param): Flowable<Output>
    **fun** refresh(param: Param): Completable
}
```

如您所见，每个电话有两个主要职责:

1.  它的`data()`方法公开了与调用相关的数据流。这就返回了一个[易流](http://reactivex.io/RxJava/2.x/javadoc/io/reactivex/Flowable.html)，而大部分时候只是一个易流从[室捣](https://developer.android.com/reference/android/arch/persistence/room/Dao.html)。然后一个视图模型订阅它并将数据传递给 UI，等等。
2.  其`refresh()`法。希望这是不言自明的，它触发了数据的刷新。大多数实现将从网络获取、映射实体，然后更新[房间](https://developer.android.com/topic/libraries/architecture/room.html)数据库。这是当前返回的一个[Completable](http://reactivex.io/RxJava/2.x/javadoc/io/reactivex/Completable.html) ,[ViewModel](https://developer.android.com/reference/android/arch/lifecycle/ViewModel.html)将订阅该 Completable 以启动“动作”。

## 那么我打算在哪里安装协程呢？

我的目标是让`refresh()`成为一个暂停函数:

```
**interface** Call<**in** Param, Output> {
    **fun** data(param: Param): Flowable<Output>
    **suspend fun** refresh(param: Param)
}
```

一旦我做了那个改变，所有的调用实现都开始抱怨，因为函数签名改变了。幸运的是，[**kot linx-coroutines-rx2**](https://github.com/Kotlin/kotlinx.coroutines/tree/master/reactive/kotlinx-coroutines-rx2)扩展库为 RxJava 类 single 类型提供了扩展方法，允许我们`await()`它们的完成。因此，在每个实现的 Rx 链末端快速粘贴`.await()`就可以修复构建。

我们现在对协程的使用非常粗糙和愚蠢！但是，嘿，这是一个开始，一切仍然工作。

下一步是开始转换`refresh()`协程感知下的所有代码，并移除不需要的 RxJava。在这一点上，你可能想知道我说的“协程感知”是什么意思，这都与线程有关。

## 穿线

使用 RxJava，我有不同的[调度器](https://github.com/ReactiveX/RxJava/wiki/Scheduler)用于不同类型的任务。这是用一个数据类实现的，这个数据类被注入到将 Rx 操作符链接在一起的任何地方。

```
**data class** AppRxSchedulers(
    **val database**: Scheduler,
    **val disk**: Scheduler,
    **val network**: Scheduler,
    **val main**: Scheduler
)@Singleton
@Provides
**fun** provideRxSchedulers() = AppRxSchedulers(
        *database* = Schedulers.single(),
        *disk* = Schedulers.io(),
        *network* = Schedulers.io(),
        *main* = AndroidSchedulers.mainThread()
)
```

我认为最重要的是数据库调度程序。这是因为我想强制单线程读取，确保数据完整性，不锁定 SQLite。

对于协程，我想做同样的事情，确保 RxJava 和协程都使用相同的线程池。事实证明，使用[**kot linx-coroutines-rx2**](https://github.com/Kotlin/kotlinx.coroutines/tree/master/reactive/kotlinx-coroutines-rx2)扩展库相对容易。它在 Scheduler 上添加了一个扩展方法，将它包装到一个协同调度器中。使用它，我将我的调度程序转换成调度程序，并注入它们。

```
**data class** AppCoroutineDispatchers(
    **val database**: CoroutineDispatcher,
    **val disk**: CoroutineDispatcher,
    **val network**: CoroutineDispatcher,
    **val main**: CoroutineDispatcher
)@Singleton
@Provides
**fun** provideDispatchers(schedulers: AppRxSchedulers) = 
    AppCoroutineDispatchers(
        *database* = schedulers.**database**.*asCoroutineDispatcher*(),
        *disk* = schedulers.**disk**.*asCoroutineDispatcher*(),
        *network* = schedulers.**network**.*asCoroutineDispatcher*(),
        *main* = *UI* )
```

如您所见，我目前使用 RxJava 调度程序作为源代码。将来，我可能会把它调换一下，让调度程序从调度程序中派生出来。

## 更换线程

所以我让我的调度程序和调度程序共享相同的线程，但是在我们的操作中使用它们怎么样呢？

RxJava 通过它的`subscribeOn()`和`observeOn()`方法使得将不同的线程观察器链接在一起变得非常容易。这里有一个`refresh()`方法的例子，其中我使用我的`network`调度程序获取网络响应并将其映射到内部实体，然后使用`database`调度程序存储结果。

```
**override fun** refresh(): Completable {
    **return** trakt.users().profile(UserSlug.ME).toRxSingle()
            .subscribeOn(schedulers.network)
            .map **{** TraktUser(username = **it**.**username**, name = **it**.**name**)
            **}**
            .observeOn(schedulers.database)
            .doOnSuccess **{
                dao**.insert(it)
            **}**
            .toCompletable()
}

**override fun** data(): Flowable<TraktUser> {
    **return** dao.getTraktUser()
            .subscribeOn(schedulers.database)
}
```

希望你能看到这是非常标准的 RxJava 代码。现在我需要将它转换成协程。

## 第一次尝试

在您阅读完[协程指南](https://github.com/Kotlin/kotlinx.coroutines/blob/master/coroutines-guide.md)之后，您可能会想到两个函数:`[launch()](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.experimental/launch.html)`和`[async()](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.experimental/async.html)`。正如你可能猜到的，我的第一次尝试集中在使用这些链接在一起:

```
**override suspend fun** refresh(param: Unit) {
    *// Fetch network response on network dispatcher* **val** networkResponse = *async*(**dispatchers**.**network**) **{
        trakt**.users().profile(UserSlug.*ME*).execute().body()
    **}**.await() *// await the result* *// Map to our entity* **val** entity = TraktUser(
        username = networkResponse.**username**,
        name = networkResponse.**name** )

    *// Save to the database on the database dispatcher
    async*(**dispatchers**.**database**) **{
        dao**.insert(entity)
    **}**.await()  *// Wait for the insert to finish*
}
```

这实际上是可行的，但是有点浪费。这里我们实际上启动了三个协程:1)网络调用，2)数据库调用，3)主机协程调用`data()`(在视图模型中)。

我相信你可以想象出一个更复杂的 Rx 链，它会做像`flatMap`ing`Iterable`或其他疯狂的事情。当不总是需要时，您将要创建的协程的数量会显著增加，就像上面的例子一样。我们在这里做的一切都是顺序的，所以没有必要启动一个新的协程。我们需要的是改变 dispatcher 的方法，幸运的是，coroutines 团队为我们提供了一种方法:`[withContext()](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.experimental/with-context.html)`。

## 第二次尝试

我在[协程指南](https://github.com/Kotlin/kotlinx.coroutines/blob/master/coroutines-guide.md#thread-confinement-fine-grained)的一个小代码示例中偶然发现了`[withContext()](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.experimental/with-context.html)`。我的第二个(也是当前的)尝试集中在用`withContext()`代替`subscribeOn()`和`observeOn()`，因为它确实做了我们想要的:

> 这个函数立即从新的上下文应用 dispatcher，将块的执行转移到块内的不同线程中，并在完成时返回。

考虑到这一点，示例变成了:

```
**override suspend fun** refresh(param: Unit) {
    *// Fetch network response on network dispatcher* **val** networkResponse = *withContext*(**dispatchers**.**network**) **{
        trakt**.users().profile(UserSlug.*ME*).execute().body()
    **}** *// Map to our entity* **val** entity = TraktUser(
        username = networkResponse.**username**,
        name = networkResponse.**name** )

    *// Save to the database on the database dispatcher
    withContext*(**dispatchers**.**database**) **{
        dao**.insert(entity)
    **}** }
```

你可以看到我们现在已经移除了`async`调用，这意味着我们不再创建任何新的协程。我们只是移动主机协程来使用我们特定的调度程序(和线程)。

## 但是医生说协程是非常轻量级的。为什么我不能异步/启动？

协程是非常轻量级的，但是创建它们仍然是有成本的。你应该记得在 Android 上我们运行在一个资源受限的系统上，所以我们需要尽一切可能减少我们的足迹。与使用`async`或`launch`创建一个新的协程相比，使用`withContext`只需一次函数调用和最少的对象分配就能满足我们的需求。

还有一个事实是，`async`和`launch`是针对异步任务的。大多数情况下，您会有一个异步的主任务，但是在其中您会调用同步的子任务。通过使用`async`和`launch`，你被迫做额外的`await()`或`join()`，这是不必要的复杂阅读。

另一方面，如果您的子任务是不相关的，那么让它们用“async”并发运行是一种有效的方法。

## 更复杂的 Rx 链呢？

这里有一个例子，每当我看到它时，我都会感到困惑:

```
**override fun** refresh(param: Unit): Completable {
    **trakt**.users().watchedShows(UserSlug.*ME*).*toRxSingle*()
            .subscribeOn(**schedulers**.**network**)
            .toFlowable()
            .flatMapIterable **{ it }** .flatMapSingle **{
                showFetcher**.load(**it**) **}** .toList()
            .observeOn(**schedulers**.**database**)
            .doOnSuccess **{
                databaseTransactionRunner**.runInTransaction **{
                    dao**.deleteAll()
                    **it**.*forEach* **{ dao**.insert(**it**) **}
                }
            }** .*toCompletable*()
}
```

当你分解它时，它实际上并不比我们上面使用的例子复杂多少。最大的区别在于，它处理的是实体的集合，而不是单一的实体。为此，它使用 Flowable 的`[flatMapIterable()](http://reactivex.io/documentation/operators/flatmap.html)`为每个项目展开地图，然后使用`[toList()](http://reactivex.io/documentation/operators/to.html)`将所有结果再次组合到一个列表中，然后保存到数据库中。

*我实际上使用了一个不同的类(* `*showFetcher*` *)来为扇出提供操作符，在这种情况下，它返回一个 Single。暂时忽略这一点。*

我们在这里实际描述的是一个并行图，其中每个`map()`都是并发运行的。JDK 8 提供了与`list.parallel().map(*/* map function */*).collect(toList())`类似的东西。我们可以使用那个功能，但是我们不会使用协程！

Kotlin 中没有使用协程的内置版本(我能找到)，但幸运的是它很容易实现:

```
**suspend fun** <A, B> Collection<A>.parallelMap(
    context: CoroutineContext = DefaultDispatcher,
    block: **suspend** (A) -> B
): Collection<B> {
    **return** map **{
        // Use async to start a coroutine for each item** async(context) **{** block(it)
        **}
    }**.map **{
        // We now have a map of Deferred<T> so we await() each** it.await()
    **}** }
```

*注意:这可能不是一个完美的实现。不过，对我来说很有效。*

通过使用`parallelMap()`，我们复杂的 RxJava 链变成如下:

```
**override suspend fun** refresh(param: Unit) {
    **val** networkResponse = *withContext*(**dispatchers**.**network**) **{
        trakt**.users().watchedShows(UserSlug.*ME*).execute().body()
    **}

    val** shows = networkResponse.*parallelMap* **{
        showFetcher**.load(**it**)
    **}** *// Now save the list to the database
    withContext*(**dispatchers**.**database**) **{
        databaseTransactionRunner**.runInTransaction **{
            dao**.deleteAll()
            shows.*forEach* **{ dao**.insert(**it**) **}
        }
    }** }
```

希望你可以看到，这是一个更清晰的阅读。这个`showFetcher`职业仍然需要被转换，但是现在它可以 *a* 等待。🤦

如果你感兴趣，你可以在这里看到将应用程序转换成协程的 PR:

[](https://github.com/chrisbanes/tivi/pull/135/) [## 通过 chrisbanes Pull 请求#135 chrisbanes/tivi 从 RxJava 迁移到协程

### GitHub 是 2000 多万开发人员的家园，他们一起工作来托管和审查代码、管理项目和构建…

github.com](https://github.com/chrisbanes/tivi/pull/135/) 

## 后续步骤

希望您可以看到，从 RxJava 的 Single/Maybe/Completable 切换到协程实际上是相对容易的。

目前，我仍在使用 RxJava 进行流媒体观看，但我可能会转向单独使用 [LiveData](https://developer.android.com/reference/android/arch/lifecycle/LiveData.html) 或[协程频道](https://github.com/Kotlin/kotlinx.coroutines/blob/master/coroutines-guide.md#channels)。不过这是下一次的任务。