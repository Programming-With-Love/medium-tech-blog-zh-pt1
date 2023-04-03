# Android 上的大型数据库查询

> 原文：<https://medium.com/androiddevelopers/large-database-queries-on-android-cb043ae626e8?source=collection_archive---------2----------------------->

![](img/8785b6b784c953e7ffa056f8e380fca1.png)

Photo by [Anastasia Zhenina on Unsplash](https://unsplash.com/photos/XOW1WqrWNKg)

## 机会之窗

SQLite 是在 Android 上持久化成千上万项数据的一种很好的方式，但是在 UI 中呈现这些巨大的数据集一直很困难，并且可能导致性能问题。在启动新的[分页库](https://developer.android.com/topic/libraries/architecture/paging.html)之前，我们研究了平台中现有的分页方法，尤其是 SQLiteCursor 中的潜在缺陷。

在这篇博文中，我们将回顾它的问题，以及为什么它会促使我们在 [Android 架构组件](https://developer.android.com/topic/libraries/architecture/index.html)中对[房间持久化](https://developer.android.com/topic/libraries/architecture/room.html)和[分页](https://developer.android.com/topic/libraries/architecture/paging.html)库使用小查询。

# SQLiteCursor 和 CursorAdapter

[SQLiteCursor](https://developer.android.com/reference/android/database/sqlite/SQLiteCursor.html) 是 Android SQLite 数据库查询的返回类型。它允许您以固定的初始加载成本查看大型查询结果。
第一次读取初始化一个 [CursorWindow](https://developer.android.com/reference/android/database/CursorWindow.html) ，一个通常为 2MB 大小的行缓冲区，包含来自数据库的内容。每当您请求一个不存在的行时，SQLiteCursor 都会刷新此窗口。这样，SQLiteCursor 就实现了分页，具有固定的页面大小。

[CursorAdapter](https://developer.android.com/reference/android/widget/CursorAdapter.html) 从 Android API 1 开始就存在了，它提供了一种简单的方法将数据从游标(通常是 SQLiteCursor)绑定到 ListView 中的项目。虽然它很好地完成了这个功能，但它在任何需要新加载的时候都会直接在 UI 线程上查询数据库。这本身对于一个现代的、反应灵敏的应用程序来说是不可接受的。所以有人可能会问:难道我们不能有一个基于光标的适配器，在后台线程上加载吗？毕竟，SQLiteCursor 内置了分页功能。

# SQLiteCursor 中的分页问题

SQLiteCursor 中分页的大部分问题来自于它使用窗口将内容分页时令人惊讶的行为。以下是我们在为[分页库](https://developer.android.com/topic/libraries/architecture/paging.html)试验 SQLiteCursor 的内部分页时发现的挑战列表:

## **SQLiteCursor 没有保持任何数据库事务打开**

当我开始研究分页时，我对 SQLite 没有经验，尤其是 Android 上的游标。我只是假设 SQLiteCursor 会在加载一个窗口后暂停查询，并在需要下一个窗口时继续。这样，访问第 10 个窗口将和第 1 个窗口一样有效。**这个不正确**。每次读取新窗口时，查询从位置 0 重新开始，并跳过填充窗口不需要的行，一次一行。这是因为 **SQLiteCursor 不能恢复查询**。

这就像访问链表中的 1000 到 1050 项一样——你必须跳过大量的项才能访问下一个要加载的页面。加载时，每个后续窗口都必须跳过越来越多的查询，这会降低速度。这相当于使用 SQL `OFFSET`关键字来跳过内容，这[不是分页内容](http://www.sqlite.org/cvstrac/wiki?p=ScrollingCursor)的最有效方式，但是当依赖于 SQLiteCursor 中的分页时，这是无法避免的。您可以在这里看到 SQLiteCursor 如何在新窗口[中翻页。](https://android.googlesource.com/platform/frameworks/base/+/fee4546fd648b519ad828ea1f950554c1054699d/core/jni/android_database_SQLiteConnection.cpp#695)

## **SQLiteCursor.getCount()是必需的，并且扫描整个查询**

在读取第一行之前，SQLiteCursor 调用`getCount()`进行边界检查。由于 **SQLite 必须扫描查询的整个结果集才能计数**(同样，像链表一样)，这可能会增加大量开销。如果您为了响应用户滚动而将一个大型查询逐渐分页到一个 UI 中，您可能不需要知道查询的整体大小，因此计数会增加不必要的前期工作。

## **SQLiteCursor.getCount()总是加载行的第一个窗口**

作为计算计数的一部分，在扫描结果集时，SQLiteCursor 从位置 0 开始主动填充其窗口，并假设将需要查询中的第一个项目。

它预先加载了这些项，这样它就可以提前大致知道一个窗口可以容纳多少行(下面将详细介绍)。如果您从查询的开始呈现数据，这种机会加载是合理的，但是从保存的实例状态恢复的位置可能会在列表的更下方开始索引，其中初始窗口是不相关的。如果您想从第三个内容窗口呈现数据，您必须先加载并丢弃 2MB 的数据。这个计数行为的代码在这里是。

## **SQLiteCursor 可能会加载您没有要求的数据**

`[Cursor.moveToPosition()](https://developer.android.com/reference/android/database/Cursor.html#moveToPosition(int))`保证被请求的行在窗口中，但是 **SQLiteCursor 没有在被请求的行**开始填充。因为 SQLiteCursor 并不假设应用程序正在向前读取，所以当它距离目标位置大约一个窗口的⅓时，它就开始填充它的窗口。这意味着一个 CursorAdapter 在一次窗口加载后向后滚动几行不会触发另一次窗口加载。这也意味着，在第一次加载之后，每加载 2MB 的数据会加载 650KB 或更多的数据，您已经看到了。你可以在这里看到这个行为[的代码和解释。](https://android.googlesource.com/platform/frameworks/base/+/fee4546fd648b519ad828ea1f950554c1054699d/core/java/android/database/DatabaseUtils.java#742)

## **SQLiteCursor 加载位置不可预测**

当 SQLiteCursor 试图加载一个目标位置时，它试图提前启动一个窗口的⅓。这意味着它必须猜测一个窗口中有多少行。为此，它使用第一次窗口加载时填充的行数。不幸的是，这意味着如果您的行大小不同(例如，如果您嵌入任意长度的用户注释字符串)，它的猜测可能是错误的。 **SQLiteCursor 可以低于目标位置**，用内容填充一个窗口——然后丢弃所有内容，并重新开始填充。例如，如果您正在扫描一个长查询，并到达一个需要重新加载窗口的行，那么加载可能只会捕获少量的新行。这里的清除窗口和重启代码是。

## **光标需要关闭**

必须使用`[close()](https://developer.android.com/reference/android/database/Cursor.html#close())`方法关闭游标，所以不管它们存储在哪里，都必须有代码在不再需要时清理它们。CursorAdapter 显然在这方面没有帮助，它把责任推给了应用程序开发人员。存储和重用游标需要开发人员编写代码来处理像活动停止这样的事件。

## **SQLiteCursor 不知道数据已经改变**

**在第一次窗口读取**(和第一次计数)后，SQLiteCursor 不会跟踪数据库是否发生了变化。这意味着，如果添加或删除了某些项，SQLiteCursor 的缓存大小是不正确的——这对于边界检查和希望加载的数据看起来一致来说都是一个问题。这可能会在移动到不再存在的行时导致异常，或者在某些情况下导致数据不一致。例如，如果您已经加载了第 N 行，并且在位置 0 插入了一个新项，然后您试图读取第 N+1 行，您将再次加载旧的第 N 行。

# 回避问题

上述问题告诉我们，SQLCursor 不能扩展到有成千上万个结果的查询。幸运的是，这些问题都有一个简单的解决方法:小查询。适合单个 CursorWindow 的查询避免了所有的问题，这就是为什么我们在分页和空间中如此强烈地支持它们。通常只配置十到二十个条目的页面大小，并且一次只查询那么多条目。

不过，选择页面大小有很多因素——最大到一个窗口的较大查询通常会提高性能，较小的查询会提高延迟和内存。10 个条目对于数据库不是瓶颈的高列表条目来说可能是有意义的，而如果列表条目很小或者查询很昂贵，那么 300 个条目可能会更好。

如果您依赖 SQLiteCursor 的内部分页来延迟加载大量结果，我们建议您切换到另一种方法。要么使用新的[分页库](https://developer.android.com/topic/libraries/architecture/paging.html)及其与 [Room Persistence 库](https://developer.android.com/topic/libraries/architecture/room.html)的集成，要么使用自定义实现，您自己处理分页，并确保您的查询结果足够小，可以放在单个 CursorWindow 中。

要使用新的分页库对大型 SQL 查询结果和小型查询进行分页，您可以更改:

```
@Dao
interface UserDao {
    // regular list query — falls over with too much data
    @Query(“SELECT * FROM user ORDER BY age DESC”)
    **LiveData<List<User>>** loadUsersByAgeDesc();
}
```

变成:

```
@Dao
interface UserDao {
    // paged query — handles arbitrarily large queries
    @Query(“SELECT * FROM user ORDER BY age DESC”)
    **DataSource.Factory<Integer, User>** loadUsersByAgeDesc();
}
```

然后将它传递给一个`[LivePagedListBuilde](https://developer.android.com/topic/libraries/architecture/livedata.html)r`以得到一个`[LiveData](https://developer.android.com/topic/libraries/architecture/livedata.html)<[PagedList](https://developer.android.com/reference/android/arch/paging/PagedList.html)>`来处理任意大的结果集:

```
**LiveData<PagedList<User>>** users = new LivePagedListBuilder<>(
        userDao.loadUsersByAgeDesc(), **/*page size*/ 20**).build();
```

在上面的代码中，我们获得了分页查询结果的 [LiveData](https://developer.android.com/topic/libraries/architecture/livedata.html) 版本，当数据库发生变化时，它还将更新任何订阅的[观察者](https://developer.android.com/reference/android/arch/lifecycle/Observer.html)。要了解更多关于使用架构组件从 SQLite 分页的信息，请参阅 Github 上的[分页介绍](https://developer.android.com/topic/libraries/architecture/paging.html)和[分页示例。](https://github.com/googlesamples/android-architecture-components/tree/master/PagingSample)

[](https://github.com/googlesamples/android-architecture-components/tree/master/PagingSample) [## Android-架构-组件/分页示例

### android 架构组件-Android 架构组件示例。

github.com](https://github.com/googlesamples/android-architecture-components/tree/master/PagingSample) 

## **安卓平台—安卓 P 更新！**

自从本文最初发表以来，我们已经在 Android P 开发者预览版中添加了新的 API 来改进上述行为。该平台现在允许应用程序[禁用窗口启发式](https://developer.android.com/reference/android/database/sqlite/SQLiteCursor.html#setFillWindowForwardOnly(boolean))和[配置光标或窗口大小](https://developer.android.com/reference/android/database/CursorWindow.html#CursorWindow(java.lang.String,%20long))。小查询仍然是避免上述所有问题的好方法，但 P 的变化给了应用程序更多的控制，我们将很快在 Room 中使用它们，以尽可能保持查询的效率。