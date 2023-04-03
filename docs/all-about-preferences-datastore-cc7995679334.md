# 关于首选项数据存储的所有信息

> 原文：<https://medium.com/androiddevelopers/all-about-preferences-datastore-cc7995679334?source=collection_archive---------0----------------------->

![](img/b6b4fbababc3e21bb09fb33126f1ce92.png)

在这篇文章中，我们将看看 [**偏好数据存储**](https://developer.android.com/topic/libraries/architecture/datastore?gclid=CjwKCAiA55mPBhBOEiwANmzoQtX8aFaxx5WFTDOpYVN429tF3U8X3BnZu8ZMfJhRqGtyme_PzaypHhoCQDsQAvD_BwE&gclsrc=aw.ds#datastore-preferences) ，两个[数据存储](https://developer.android.com/topic/libraries/architecture/datastore)实现之一。我们将讨论如何**创建它，读取和写入数据，以及如何处理异常**，希望所有这些都能为您提供足够的信息来决定它是否是您应用程序的正确选择。

Preferences DataStore 使用**键值对**来存储较小的数据集，而无需预先定义模式。这可能会让你想起`SharedPreferences`，但只是在它构建你的数据模型的方式上。数据存储相对于其前身有多种优势。请随意快速跳回我们之前的[](/androiddevelopers/introduction-to-jetpack-datastore-3dc8d74139e7)****，看看我们在那里做的详细对比。接下来，除非另有说明，我们将把`Preferences DataStore`简称为`Preferences`。****

****快速回顾一下:****

*   ****提供一个完全异步的 API 来检索和保存数据，使用 Kotlin 协同程序的能力****
*   ******不提供现成的同步支持**——它直接避免做任何阻塞 UI 线程的工作****
*   ****依赖于 Flow 的内部错误信号机制，允许您在读取或写入数据时安全地捕获和处理异常****
*   ****在**原子读-修改-写**操作中以事务方式处理数据更新，提供强大的 [ACID](https://en.wikipedia.org/wiki/ACID) 保证****
*   ****支持**简单快速的数据迁移******
*   ****希望**在**改动最小**的情况下将**从`SharedPreferences`快速迁移，并且在没有全类型安全的情况下也有足够的自信？选择**首选项**而不是 Proto****

**现在让我们深入一些代码，了解应该如何实现首选项。**

**我们将使用[**Preferences DataStore**](https://github.com/googlecodelabs/android-datastore/tree/preferences_datastore)codelab 代码。如果您对更具实践性的实现方法感兴趣，我们真的鼓励您自己完成 [**使用首选项数据存储库**](https://developer.android.com/codelabs/android-preferences-datastore#0) codelab。**

**这个示例应用程序显示了一个任务列表，用户可以选择按照任务的完成状态进行过滤，或者按照优先级和截止日期进行排序。我们希望存储他们的选择——一个用于已完成任务的**布尔值**和一个用于数据存储的**排序顺序枚举**。**

# **相关性设置**

**让我们从添加必要的依赖项开始:**

****💡**快速提示——如果您想缩小您的构建，请确保向您的`proguard-rules.pro`文件添加一个额外的规则，以防止您的字段被删除:**

# **创建首选项数据存储**

**您通过一个`DataStore<Preferences>`实例与保存在数据存储中的数据进行交互。`DataStore`是一个允许访问持久化信息的接口。 [Preferences](https://developer.android.com/reference/kotlin/androidx/datastore/preferences/core/Preferences) 是一个类似于通用映射的抽象类，专门用于数据存储的 Preferences 实现，以跟踪数据的键值对。我们在讨论写数据的时候会谈到它的`MutablePreferences`子类。**

**要创建这个实例，建议使用委托`preferencesDataStore`并传递一个强制的`name`参数。这个委托是一个 [Kotlin 扩展属性](https://kotlinlang.org/docs/extensions.html#extension-properties)，它的接收方类型必须是`Context`的一个实例，随后需要构造`File`对象，数据存储在这里存储数据:**

**您不应该为一个给定的文件创建多个数据存储实例，**因为这样做会破坏所有的数据存储功能。**因此，您可以在 Kotlin 文件的顶层添加一次委托构造，并在整个应用程序中使用它，以便将其作为 **singleton** 传递。在后面的文章中，我们将看到如何通过依赖注入来实现这一点。**

## **定义键**

**DataStore 为不同的数据类型提供了一种快速构造键的方法，比如`booleanPreferencesKey`、`intPreferencesKey`等等——您只需要将**键名**作为值传递。虽然这确实对数据类型施加了一些约束，但是请记住**它并没有提供明确的类型安全**。通过指定某种类型的首选项，我们希望得到最好的结果，并依赖于我们的假设，即**某种类型的值将被返回**。如果你觉得你的代码在某种程度上可以安全地处理这个问题，请放心地继续使用 Preferences。如果没有，可以考虑使用 Preferences 的兄弟，[**Proto DataStore**](https://developer.android.com/topic/libraries/architecture/datastore?gclid=CjwKCAiA55mPBhBOEiwANmzoQtX8aFaxx5WFTDOpYVN429tF3U8X3BnZu8ZMfJhRqGtyme_PzaypHhoCQDsQAvD_BwE&gclsrc=aw.ds#datastore-typed)，因为它提供了**全类型安全**。**

**在我们应用程序的`[UserPreferencesRepository](https://github.com/googlecodelabs/android-datastore/blob/preferences_datastore/app/src/main/java/com/codelab/android/datastore/data/UserPreferencesRepository.kt)`中，我们指定了构建持久化数据的键值对所需的所有键:**

# **读取数据**

**为了读取存储的数据，在`UserPreferencesRepository`中，我们从`dataStore.data`暴露一个`Flow<Preferences>`。这提供了对**最新保存状态**和**的有效访问，并随着每次更改**而发出。使用 Kotlin 数据类，我们可以观察任何排放并将提供的 DataStore `Preferences`对象转换成我们自己的`UserPreferences`模型，只使用我们感兴趣的**键值对**:**

**当试图从磁盘中读取时，该流将总是发出一个值或者**抛出一个异常**。我们将在后面的章节中研究异常处理。DataStore 还确保工作总是在`**Dispatchers.IO**`上执行，因此您的 UI 线程不会被阻塞。**

**🚨**不要创建任何缓存存储库**来镜像您的首选项数据的当前状态。这样做会使 DataStore 对数据一致性的保证失效。如果您需要数据的单个快照，而不订阅进一步的流发射，则首选使用`**dataStore.data.**[**first()**](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.flow/first.html)`:**

# **写入数据**

**对于写数据，我们将使用一个暂停`DataStore<Preferences>.edit(transform: suspend (MutablePreferences) -> Unit)` 函数。让我们来分解一下:**

*   **`DataStore<Preferences>`接口——我们目前使用数据存储作为具体的`Preferences`实现**
*   **`transform: suspend (MutablePreferences) -> Unit` —一个暂停块，用于将指定的更改应用到我们的持久化数据**
*   **`MutablePreferences` —`Preferences`的一个可变子类，类似于`MutableMap`，允许我们对键值对进行修改**

**例如，我们将更改我们的`SHOW_COMPLETED`标志:**

**编辑数据是在 [**原子读-修改-写操作**](https://en.wikipedia.org/wiki/Read%E2%80%93modify%E2%80%93write) 中以事务方式完成的。这意味着数据处理操作的特定顺序保证了**一致性**和**防止竞争情况**，在此期间，数据被其他线程锁定。只有在`transform`和`edit`协程成功完成后，数据才会持久保存到磁盘，并且`datastore.data`流会反映更新。**

**🚨请记住，这是更改数据存储状态的唯一方式。保留一个`MutablePreferences`引用并在`transform`完成**后手动改变它不会改变数据存储中的持久化数据**，所以你不应该试图在`transform`块之外修改`MutablePreferences`。**

**如果写入操作由于任何原因失败，事务将被中止并引发异常。**

# **从 SharedPreferences 迁移**

**如果您之前在应用程序中使用过`SharedPreferences`，并且想要安全地将其数据传输到偏好设置，您可以使用`[**SharedPreferencesMigration**](https://developer.android.com/reference/kotlin/androidx/datastore/migrations/SharedPreferencesMigration)`。它需要一个上下文、`SharedPreferences`名称和一组您希望迁移的可选键(或者只保留默认的**`**MIGRATE_ALL_KEYS**`**值**)。****

****查看`SharedPreferencesMigration`的实现，您会看到一个`getMigrationFunction()`，它负责获取所有需要的、存储的键-值对，然后使用**相同的键**将它们添加到首选项中。通过`preferencesDataStore`委托的`produceMigrations`参数将`SharedPreferencesMigration`传递给**轻松迁移**:****

****`produceMigrations`将确保`getMigrationFunction()`在对数据存储库的任何潜在数据访问之前运行**。这意味着您的迁移**必须在 DataStore 发出任何进一步的值之前和开始对数据进行任何新的更改之前已经成功**。一旦成功迁移，就可以安全地停止使用`SharedPreferences`，因为键只被**迁移一次**，然后**从`SharedPreferences`中移除**。******

**`produceMigrations`接受`[DataMigration](https://developer.android.com/reference/kotlin/androidx/datastore/core/DataMigration)`的列表。我们将在后面的章节中看到如何将它用于其他类型的数据迁移。如果你不需要迁移，你可以忽略它，因为它已经有了一个默认的**`**listOf()**`**。******

# ******异常处理******

******与`SharedPreferences`相比，DataStore 的一个主要优势是其用于捕获和处理异常的**简洁机制**。虽然`SharedPreferences`将解析错误作为运行时异常抛出，为意外的、未捕获的崩溃留出了空间，但是当读/写数据发生错误时，数据存储抛出`**IOException**`。******

****我们可以通过在`map()`和发射`emptyPreferences()`之前使用`catch()`流操作符来安全地处理这个问题:****

****或者用简单的`try-catch`块书写:****

****如果抛出了不同类型的异常，最好重新抛出。****

# ****待续****

****我们已经介绍了 [**数据存储的首选项**](https://developer.android.com/topic/libraries/architecture/datastore?gclid=CjwKCAiA55mPBhBOEiwANmzoQtX8aFaxx5WFTDOpYVN429tF3U8X3BnZu8ZMfJhRqGtyme_PzaypHhoCQDsQAvD_BwE&gclsrc=aw.ds#datastore-preferences) 实现——何时以及如何使用它来读写数据，如何处理错误以及如何从`SharedPreferences`迁移。在下一篇文章中，我们将讨论与[**Proto DataStore**](https://developer.android.com/topic/libraries/architecture/datastore?gclid=CjwKCAiA55mPBhBOEiwANmzoQtX8aFaxx5WFTDOpYVN429tF3U8X3BnZu8ZMfJhRqGtyme_PzaypHhoCQDsQAvD_BwE&gclsrc=aw.ds#datastore-typed)实现相同的主题，所以不要走开。****

****您可以在这里找到我们的 Jetpack 数据存储系列的所有帖子:
[Jetpack 数据存储简介](/androiddevelopers/introduction-to-jetpack-datastore-3dc8d74139e7)
[所有关于首选项数据存储](/androiddevelopers/all-about-preferences-datastore-cc7995679334)
[所有关于原型数据存储](/androiddevelopers/all-about-proto-datastore-1b1af6cd2879)
[数据存储和依赖注入](/androiddevelopers/datastore-and-dependency-injection-ea32b95704e3)
[数据存储和 Kotlin 序列化](/androiddevelopers/datastore-and-kotlin-serialization-8b25bf0be66c)
[数据存储和同步工作](/androiddevelopers/datastore-and-synchronous-work-576f3869ec4c)
[数据存储和数据迁移](/androiddevelopers/datastore-and-data-migration-fdca806eb1aa)
[数据存储和测试](/androiddevelopers/datastore-and-testing-edf7ae8df3d8)****