# 使用和测试 Room Kotlin APIs

> 原文：<https://medium.com/androiddevelopers/using-and-testing-room-kotlin-apis-4d69438f9334?source=collection_archive---------4----------------------->

![](img/f54d5257763f1249e0482b52c4b4c9b2.png)

Room 是 SQLite 的一个包装器，它使得在 Android 上使用数据库变得更加容易，也是迄今为止我最喜欢的 Jetpack 库。在这篇文章中，我想告诉你如何使用和测试 Room Kotlin APIs，同时，我也将分享事情是如何在幕后工作的。

我们将使用带有 view codelab 的[房间作为基础。在这里，我们正在构建一个保存在数据库中并显示在屏幕上的单词列表。用户也可以向列表中添加单词。](https://developer.android.com/codelabs/android-room-with-a-view-kotlin#0)

# 定义数据库表

在我们的数据库中，我们只有一个表:包含单词的表。`Word`类表示表中的一个条目，它需要用`@Entity`进行注释。我们使用`@PrimaryKey`来定义表的主键。基于此，Room 将生成一个 SQLite 表，与类名同名。该类的每个成员都成为表中不同的列。列名和类型由每个字段的名称和类型给出。您可以使用`@ColumnInfo`注释来更改它。

我们建议您总是使用`@ColumnInfo`注释，因为它给了您更大的灵活性来重命名成员，而不必更改数据库列名。更改列名会导致数据库模式发生变化，因此您需要实现迁移。

# 访问表中的数据

为了访问表中的数据，我们创建了一个数据访问对象(DAO)。这将是一个名为`WordDao`的接口，标注为`@Dao`。我们想要从表中插入、删除和获取数据，所以这些将被定义为 DAO 中的抽象方法。使用数据库是一项耗时的 I/O 操作，因此需要在后台线程上完成。我们将使用与 Kotlin 协程和流的房间集成来实现这一点。

在这个 Kotlin 词汇视频中，我们已经介绍了使用协程的基础知识。查看[这个视频](https://youtu.be/emk9_tVVLcc?list=PLWz5rJ2EKKc_T0fSZc9obnmnWcjvmJdw_)了解心流的信息。

# 插入数据

要插入数据，创建一个抽象的 suspend 函数，将要插入的单词作为参数，并用@Insert 对其进行注释。Room 将生成在数据库中插入数据所需的所有工作，因为我们使函数挂起，所以 Room 将所有要做的工作转移到后台线程。因此，这个挂起函数是主安全的:从主线程调用是安全的。

```
@Insert
suspend fun insert(word: Word)
```

引擎盖下的房间生成道的实现。下面是我们的 insert 方法的实现:

调用函数，有 3 个参数:数据库，一个表示我们是否在事务中的标志和一个对象。`Callable.call()`包含处理数据库插入的代码。

如果我们检查`CoroutinesRoom.execute()` [实现](https://cs.android.com/androidx/platform/frameworks/support/+/androidx-master-dev:room/ktx/src/main/java/androidx/room/CoroutinesRoom.kt;l=47?q=CoroutinesRoom)，我们会看到房间将`callable.call()`移动到不同的`CoroutineContext`。这来源于您在构建数据库时提供的执行器，或者默认情况下将使用架构组件 IO 执行器。

# 查询数据

为了查询该表，我们将创建一个抽象函数并用`@Query`对其进行注释，传入获取所需数据所需的 SQL 查询:word 表中的所有单词，按字母顺序排序。

每当数据库中的数据发生变化时，我们都希望得到通知，所以我们返回一个`Flow<List<Word>>`。由于返回类型的原因，Room 在后台线程上运行查询。

```
@Query(“SELECT * FROM word_table ORDER BY word ASC”)
fun getAlphabetizedWords(): Flow<List<Word>>
```

引擎盖下，房间为我们生成了`getAlphabetizedWords()`:

我们看到一个`CoroutinesRoom.createFlow()`调用，有 4 个参数:数据库、一个指示我们是否在事务中的标志、应该观察变化的表列表(在我们的例子中只是`word_table`)和一个`Callback`。`Callback.call()`包含要触发的查询的实现。

如果我们检查`CoroutinesRoom.createFlow()` [实现](https://cs.android.com/androidx/platform/frameworks/support/+/androidx-master-dev:room/ktx/src/main/java/androidx/room/CoroutinesRoom.kt;l=99?q=CoroutinesRoom)，我们会看到这里查询调用也被移动到不同的`CoroutineContext`。与插入调用一样，这个调度程序是从构建数据库时提供的执行器派生出来的，或者默认情况下将使用架构组件 IO 执行器。

# 创建数据库

我们已经定义了要存储在数据库中的数据以及如何访问它，现在是时候定义数据库本身了。为此，我们创建一个扩展了`RoomDatabase`的抽象类，用`@Database`对其进行注释，传入`Word`作为要存储的实体，1 作为数据库版本。

我们将定义一个返回`WordDao`的抽象方法。一切都是抽象的，因为房间是为我们生成实现的地方。像这样，有许多逻辑我们不再需要实现。

剩下的唯一一步是构建数据库。我们希望确保不会同时打开多个数据库实例，并且我们需要应用程序上下文来初始化数据库。因此，处理这个问题的一种方法是在我们的类的伴生对象中定义一个 RoomDatabase 实例，以及一个构建数据库的 getDatabase 函数。如果我们希望在不同于由 Room 创建的 IO 执行器的执行器上执行 Room 查询，我们可以通过调用`[setQueryExecutor()](https://developer.android.com/reference/androidx/room/RoomDatabase.Builder#setQueryExecutor(java.util.concurrent.Executor))`在构建器中传递它。

# 测试道

为了测试 Dao，我们需要实现一个`AndroidJUnit`测试，因为 Room 在设备上创建了一个 SQLite 数据库。

当实现 Dao 测试时，在每个测试运行之前，我们创建数据库。在每个测试运行之后，我们关闭数据库。由于我们不需要将数据保存在设备上，因此在创建数据库时，我们可以使用内存中的数据库。由于这是一个测试，我们可以允许查询在主线程上运行。

为了测试单词是否成功插入，我们将创建一个单词，插入它，从按字母顺序排列的单词列表中获取第一个单词，并确保它与我们创建的单词相同。由于我们调用暂停函数，我们将在 runBlocking 块中运行测试。因为这是一个测试，所以我们不介意测试是否阻塞了测试线程。

Room 提供了比我们所介绍的更多的功能和灵活性——您可以定义 Room 应该如何处理数据库冲突，您可以存储原本使用 SQLite 无法存储的类型，[如](/androiddevelopers/room-time-2b4cf9672b98) `[Date](/androiddevelopers/room-time-2b4cf9672b98)`，通过创建`TypeConverters`，您可以实现复杂的查询，使用`JOIN`和其他 SQL 功能，[创建数据库视图](https://developer.android.com/training/data-storage/room/creating-views)，预先填充您的数据库或在创建或打开数据库时触发某些数据库操作。

查看我们的[房间文档](https://developer.android.com/training/data-storage/room)了解更多信息，查看[房间查看](https://developer.android.com/codelabs/android-room-with-a-view-kotlin) codelab 进行实践学习。