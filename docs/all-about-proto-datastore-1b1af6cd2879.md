# 关于原始数据存储的所有信息

> 原文：<https://medium.com/androiddevelopers/all-about-proto-datastore-1b1af6cd2879?source=collection_archive---------2----------------------->

![](img/b6b4fbababc3e21bb09fb33126f1ce92.png)

在本帖中，我们将了解两个[数据存储实现](/androiddevelopers/introduction-to-jetpack-datastore-3dc8d74139e7)之一的 [**原型数据存储**](https://developer.android.com/topic/libraries/architecture/datastore#datastore-typed) 。我们将讨论如何**创建它，读取和写入数据，以及如何处理异常**，以更好地理解使 Proto 成为最佳选择的场景。

Proto DataStore 使用由 [**协议缓冲区**](https://developers.google.com/protocol-buffers) 支持的类型化对象来存储较小的数据集，同时提供**类型安全。**它消除了使用键值对的需要，使其**在结构上**不同于其`SharedPreferences`前身及其兄弟实现[首选项数据存储](/androiddevelopers/all-about-preferences-datastore-cc7995679334)。然而，这还不是全部——数据存储为**带来了**超过`SharedPreferences`的许多其他改进。请随意快速跳回我们 [**系列的第一篇文章**](/androiddevelopers/introduction-to-jetpack-datastore-3dc8d74139e7) ，看看我们在那里做的详细比较。接下来，除非另有说明，我们将把`Proto DataStore`简称为`Proto`。

总而言之:

*   提供一个完全异步的 API 来检索和保存数据，使用 Kotlin 协同程序的能力
*   **不提供现成的同步支持**——它直接避免做任何阻塞 UI 线程的工作
*   依赖于 Flow 的内部错误信号机制，允许您在读取或写入数据时安全地**捕捉和处理异常**
*   在**原子读取-修改-写入操作**中安全处理数据更新，提供强大的 [ACID](https://en.wikipedia.org/wiki/ACID) 保证
*   允许**轻松简单的数据迁移**
*   需要**全类型安全**并且你的数据需要处理更多的**复杂类**，比如枚举或列表？这对于[偏好](https://developer.android.com/topic/libraries/architecture/datastore#datastore-preferences)是不可能的，所以选择**原型**来代替

# 协议缓冲区介绍

要使用 Proto DataStore，您需要熟悉 [**协议缓冲区**](https://developers.google.com/protocol-buffers)**——一种语言中立、平台中立的机制，用于**序列化结构化数据**。它比 XML 更快、更小、更简单、更明确，并且比其他类似的数据格式更容易阅读。**

**您定义了一个关于您希望如何构建数据的模式，并指定了一些选项，比如使用哪种语言来生成代码。然后，编译器根据您的规范生成类。这让你可以轻松地从各种数据流中读写结构化数据，在不同平台间共享，使用多种不同的语言，如[科特林](https://developers.google.com/protocol-buffers/docs/kotlintutorial)。**

**`.proto`文件中某些数据的模式示例:**

**如何使用生成的 Kotlin 代码构建您的数据模型:**

**或者，您可以尝试新发布的 [**Kotlin DSL 对协议缓冲区的支持**](https://developers.googleblog.com/2021/11/announcing-kotlin-support-for-protocol.html) ，以更习惯的方式构建您的数据模型:**

**投入更多的时间来学习这种新的序列化机制绝对是值得的，因为它带来了**类型安全性、改进的可读性和整体代码简单性**。**

# **原始数据存储依赖关系设置**

**现在让我们看一些代码，了解一下 **Proto** 是如何工作的。**

**我们将使用[**Proto DataStore**](https://github.com/googlecodelabs/android-datastore/tree/proto_datastore)codelab 示例。如果您对更具实践性的实施方法感兴趣，我们真的鼓励您自己完成使用 Proto DataStore codelab 的 [**。**](https://developer.android.com/codelabs/android-proto-datastore#0)**

**这个示例应用程序显示了一个任务列表，用户可以选择按照任务的完成状态进行过滤，或者按照优先级和截止日期进行排序。我们希望存储他们的选择—一个用于显示已完成任务的**布尔值**和一个在 Proto 中的**排序顺序枚举**。**

**我们将首先添加 Proto 依赖项和一些基本的 protobuf 设置到你的模块的`build.gradle`。如果你对 Protobuf 编译的更高级定制感兴趣，请查看 Gradle notes 的 [**Protobuf 插件:**](https://github.com/google/protobuf-gradle-plugin)**

> ***💡快速提示——如果您想缩小您的构建，请确保在您的* `*proguard-rules.pro*` *文件中添加一个额外的规则，以防止您的字段被删除:***

# **Proto DataStore 的 Protobuf 设置**

**我们的 Proto 之旅从在一个`.proto`文件中定义你的**持久化数据**的结构开始。可以把它想象成你的可读模式和编译器的蓝图。我们将命名我们的`user_prefs.proto`，并将其添加到`app/src/main/proto`目录中。**

**遵循 [Protobuf 语言指南](https://developers.google.com/protocol-buffers/docs/overview)，在这个文件中我们将为我们想要序列化的每个数据结构添加一个**消息，然后为消息中的每个字段指定一个**名称**和一个**类型**。为了有助于形象化，让我们看一下 Kotlin 数据类和相应的 protobuf 模式。****

**`UserPreferences` —科特林数据类别:**

**`UserPreferences` — `.proto` 模式:**

**如果您以前没有使用过 protobufs，您可能也会对模式中的前几行感到好奇。让我们来分解一下:**

*   **`syntax` —指定您正在使用`[proto3](https://developers.google.com/protocol-buffers/docs/proto3)`语法**
*   **`java_package` —为您生成的类指定**包声明**的文件选项，这有助于防止不同项目之间的**命名冲突****
*   **`java_multiple_files` —文件选项，指定是否仅为该`.proto`生成具有嵌套子类的**单个文件**(设置为 false 时)，或者是否为每个顶级消息类型生成**单独的文件**(设置为 true 时)；默认情况下，它为假**

**接下来是我们的消息定义。消息是一个包含**一组类型化字段**的集合。许多标准的简单数据类型可以作为字段类型，包括`bool`、`int32`、`float`、double 和`string`。您还可以通过使用**其他消息类型作为字段类型**来为您的消息添加进一步的结构，就像我们对`SortOrder`所做的那样。**

**每个元素上的`= 1`、`= 2`标记标识字段在二进制编码中使用的唯一“标签”——就像一个排序 ID。**一旦您的消息类型被使用，这些号码就不能更改。****

**当您在`.proto,`上运行协议缓冲编译器时，编译器会以您选择的语言生成代码。在我们的具体例子中，当编译器运行时，这将导致在您的应用程序的`build/generated/source/proto` …目录中生成`UserPreferences`类:**

> ***💡快速提示——您还可以尝试新发布的* [***Kotlin DSL 对协议缓冲区的支持***](https://developers.googleblog.com/2021/11/announcing-kotlin-support-for-protocol.html) *，以使用更习惯的方式构建您的数据模型。***

**现在我们已经有了`UserPreferences`，我们需要指定**指南**来说明 Proto 应该如何读写它们。我们通过数据存储库`Serializer`来做到这一点，数据存储库决定了数据存储时的最终格式**以及如何正确地访问它。这需要覆盖:****

*   **`defaultValue` —找不到数据返回什么**
*   **`writeTo` —如何将我们的数据对象的内存表示转换成适合存储的格式**
*   **`readFrom` —与上述相反，如何从存储格式转换成相应的内存表示**

**为了尽可能保证代码的安全，**处理** `**CorruptionException**`以避免当文件由于格式损坏而无法反序列化时令人不快的意外。**

> ***💡快速提示—如果在任何时候您的 AS 都找不到任何与* `*UserPreferences*` *相关的东西，* ***清理并重建*** *您的项目以启动 protobuf 类的生成。***

# **创建原型数据存储**

**您通过一个`DataStore<UserPreferences>`实例与 Proto 交互。`DataStore`是**授权访问持久化信息**的接口，在我们的例子中是以生成的`UserPreferences`的形式。**

**要创建这个实例，建议使用委托`dataStore`并传递强制的`fileName`和`serializer`参数:**

**`fileName`用于创建一个`File`用于存储数据。这就是为什么`dataStore`委托是一个 [Kotlin 扩展属性](https://kotlinlang.org/docs/extensions.html#extension-properties)，它的接收者类型必须是`Context`的实例，因为这是通过`[applicationContext.filesDir](https://developer.android.com/training/data-storage/app-specific#internal-access-store-files)`创建文件所需要的。避免在 Proto 之外以任何方式使用该文件，因为这会破坏数据的一致性。**

**在`dataStore`委托中，您可以再传递一个可选参数— `corruptionHandler`。当**数据无法反序列化**时，如果序列化程序抛出`CorruptionException`，则调用该处理程序。`corruptionHandler`然后会指示 Proto 如何替换损坏的数据:**

**您不应该为一个给定文件创建多个数据存储实例，**因为这样做会破坏所有数据存储功能。**因此，您可以在 Kotlin 文件的顶层添加一次委托构造，并在整个应用程序中使用它，以便将其作为 **singleton** 传递。在后面的文章中，我们将看到如何通过依赖注入来实现这一点。**

# **读取数据**

**为了读取存储的数据，在`UserPreferencesRepository`中，我们从`userPreferencesStore.data`暴露一个`Flow<UserPreferences>` 。这提供了对**最新保存状态**的有效访问，并且**随着每次改变**而发出。这是 Proto 的**最大优势**之一——你的`Flow`的值已经以生成的`UserPreferences`的形式出现。这意味着**您不需要像使用`SharedPreferences`或`Preferences DataStore`那样，从保存的数据到 Kotlin 数据类模型做任何额外的转换**:**

**当试图从磁盘中读取时，该流将总是发出一个值或者**抛出一个异常**。我们将在后面的章节中研究异常处理。DataStore 还确保工作总是在`**Dispatchers.IO**`上执行，所以您的 UI 线程不会被阻塞。**

**🚨**不要创建任何缓存存储库**来镜像您的原始数据的当前状态。这样做会使 DataStore 对数据一致性的保证失效。如果您需要您的数据的单个快照，而不订阅进一步的流发射，则首选使用`**userPreferencesStore.data**.[first()](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.flow/first.html)`:**

# **写入数据**

**对于写数据，我们将使用一个暂停`DataStore<UserPreferences>.updateData(transform: suspend (t: T) -> T)`函数。**

**让我们来分解一下:**

*   **`DataStore<UserPreferences>`接口——我们目前使用`userPreferencesStore`作为具体的原型实现**
*   **`transform: suspend (t: T) -> T)` —一个挂起块，用于将指定的更改应用到我们的 T 类型的持久化数据**

**同样，您可能会注意到与`Preferences DataStore`的不同之处，它依赖于使用`Preferences`和`MutablePreferences`，类似于`Map`和`MutableMap`，作为默认的数据表示。**

**我们现在可以用它来改变我们的`showCompleted`布尔值。**协议缓冲区也简化了这一过程**，消除了与数据类之间的任何手动转换需求:**

**有几个步骤需要分析:**

*   **`toBuilder()` ——获取我们的`currentPreferences`的`Builder`版本，该版本“解锁”它以进行更改**
*   **`.setShowCompleted(completed)` —设置新值**
*   **`.build()` —通过将其转换回`UserPreferences`来完成更新过程**

**在 [**原子读-修改-写操作**](https://en.wikipedia.org/wiki/Read%E2%80%93modify%E2%80%93write) 中以事务方式完成数据更新。这意味着数据处理操作的特定顺序保证了**一致性**和**防止竞争条件**，在此期间数据被其他线程锁定。只有在`transform`和`updateData`协程成功完成后，数据才会持久保存到磁盘，并且`userPreferencesStore.data`流会反映更新。**

**🚨请记住，这是更改数据存储状态的唯一方式。保留一个`UserPreferences`引用并在`transform`完成**后手动改变它不会改变 Proto 中的持久化数据**，所以你不应该试图在`transform`块之外修改`UserPreferences`。**

**如果写入操作由于任何原因失败，事务将被中止并引发异常。**

# **从 SharedPreferences 迁移**

**如果你之前已经在你的应用中使用了`SharedPreferences`，并且想要安全地将其数据传输到 Proto，你可以使用`[SharedPreferencesMigration](https://developer.android.com/reference/kotlin/androidx/datastore/migrations/SharedPreferencesMigration)`。它需要一个上下文、`SharedPreferences`名称和一个关于如何将`SharedPreferences`键值对转换成`migrate`参数中的`UserPreferences`的指令。通过`dataStore`委托的`produceMigrations`参数传递它，以便于迁移:**

**在这个例子中，我们经历了构建`UserPreferences`并将其`sortOrder`设置为先前存储在相应的`SharedPreferences`键-值对中的值，或者简单地默认为 NONE。**

**`produceMigrations`将确保`migrate()` 在对数据存储库的任何潜在数据访问之前运行**。这意味着您的迁移**必须在数据存储发出任何进一步的值之前和开始对数据进行任何新的更改之前成功完成**。一旦你成功迁移，停止使用`SharedPreferences`是安全的，因为键**只被迁移一次**，然后**从`SharedPreferences`中移除**。****

**`produceMigrations`接受`[DataMigration](https://developer.android.com/reference/kotlin/androidx/datastore/core/DataMigration)`的列表。我们将在后面的章节中看到如何将它用于其他类型的数据迁移。如果不需要迁移，可以忽略这一点，因为它已经有了一个默认的**`**listOf()**`**提供的**。****

# ****异常处理****

****DataStore 相对于`SharedPreferences`的一个主要优势是其用于捕获和处理异常的**简洁机制**。虽然`SharedPreferences`将解析错误作为运行时异常抛出，为意外的、未捕获的崩溃留出了空间，但是当读/写数据发生错误时，数据存储抛出`**IOException**`。****

****我们可以通过使用`catch()`流操作符并发出`getDefaultInstance()`来安全地处理这个问题:****

****或者用简单的`try-catch` 块书写:****

****如果抛出了不同类型的异常，最好重新抛出。****

# ****待续****

****我们已经介绍了 [**协议缓冲区**](https://developers.google.com/protocol-buffers) 和 [**数据存储的 Proto**](https://developer.android.com/topic/libraries/architecture/datastore?gclid=CjwKCAiA55mPBhBOEiwANmzoQtX8aFaxx5WFTDOpYVN429tF3U8X3BnZu8ZMfJhRqGtyme_PzaypHhoCQDsQAvD_BwE&gclsrc=aw.ds#datastore-typed) 实现——何时以及如何使用它来读写数据，如何处理错误以及如何从`SharedPreferences`迁移。在下一篇也是最后一篇文章中，我们将更进一步，看看数据存储如何适应你的**应用的架构**、**如何用柄**注入它，当然还有**如何测试它**。回头见！****

****您可以在这里找到我们的 Jetpack 数据存储系列的所有帖子:
[Jetpack 数据存储简介](/androiddevelopers/introduction-to-jetpack-datastore-3dc8d74139e7)
[所有关于首选项数据存储](/androiddevelopers/all-about-preferences-datastore-cc7995679334)
[所有关于原型数据存储](/androiddevelopers/all-about-proto-datastore-1b1af6cd2879)
[数据存储和依赖注入](/androiddevelopers/datastore-and-dependency-injection-ea32b95704e3)
[数据存储和 Kotlin 序列化](/androiddevelopers/datastore-and-kotlin-serialization-8b25bf0be66c)
[数据存储和同步工作](/androiddevelopers/datastore-and-synchronous-work-576f3869ec4c)
[数据存储和数据迁移](/androiddevelopers/datastore-and-data-migration-fdca806eb1aa)
[数据存储和测试](/androiddevelopers/datastore-and-testing-edf7ae8df3d8)****