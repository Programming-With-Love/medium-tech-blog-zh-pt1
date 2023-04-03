# 数据存储和依赖注入

> 原文：<https://medium.com/androiddevelopers/datastore-and-dependency-injection-ea32b95704e3?source=collection_archive---------0----------------------->

![](img/2299991abb14db357c1c152c75cb62f3.png)

在我们的 [**Jetpack 数据存储系列**](/androiddevelopers/introduction-to-jetpack-datastore-3dc8d74139e7) 的以下帖子中，我们将涵盖几个额外的概念，以了解数据存储如何与其他 API 交互，以便您可以在**生产环境**中使用一切。在这篇文章中，我们将重点关注**对** [**柄**](https://developer.android.com/training/dependency-injection/hilt-android) 的依赖注入。在这篇文章中，我们将参考**数据存储库** [**首选项**](https://developer.android.com/codelabs/android-preferences-datastore#0) 和[**Proto**](https://developer.android.com/codelabs/android-proto-datastore#0)codelabs 获取代码示例。

# 带句柄的依赖注入

在这些 codelabs 中，我们在 Kotlin 文件的顶层通过**首选项**和 **Proto** **委托构造**与我们的数据存储实例进行了一次交互。然后，我们将在整个应用程序中使用同一个实例，作为**单例**:

我们需要单例，因为为一个给定文件**创建多个数据存储实例**会破坏所有数据存储功能。然而，在生产环境中，我们通常会通过**依赖注入**来获得数据存储实例。因此，让我们来看看 DataStore 如何与 [**柄**](https://developer.android.com/training/dependency-injection/hilt-android) 一起工作，这是一个依赖注入库，它将帮助我们**减少进行手动依赖管理的样板文件**。如果您不熟悉 Hilt，我们鼓励您首先在您的 Android 应用程序 codelab 中通过 [**使用 Hilt 来学习`Components``Modules`等基本概念。**](https://developer.android.com/codelabs/android-hilt#0)

## 刀柄设置

首先，确保您已经添加了所有必要的设置步骤来启用 Hilt:

所有使用 Hilt 的应用程序都必须包含一个用`@HiltAndroidApp`注释的`Application`类来触发 Hilt 的代码生成，包括一个作为应用程序级依赖容器的应用程序基类。在我们的例子中，我们将创建一个简单的`TasksApp`，并将其添加到我们的`AndroidManifest.xml`:

## 注入首选项数据存储

为了通过注入获得一个 **Preferences DataStore 实例**，我们需要告诉 Hilt 如何正确地创建它。我们使用`PreferenceDataStoreFactory`并将其添加到一个[刀柄模块](https://developer.android.com/training/dependency-injection/hilt-android#hilt-modules):

我们之前在本系列中提到过这些参数，但让我们快速回顾一下:

*   `corruptionHandler`(可选)—当数据无法反序列化时，如果序列化程序抛出`CorruptionException`，指示数据存储如何替换损坏的数据，则调用此函数
*   `migrations`(可选)—用于将以前的数据移入数据存储的`DataMigration`列表
*   `scope`(可选)—IO 操作和转换功能将执行的范围；在这种情况下，我们重用了与数据存储 API 默认作用域相同的作用域
*   `produceFile` —根据提供的`Context`和`name`为首选项数据存储生成`File`对象，存储在`this.applicationContext.filesDir` + `datastore/`子目录中

现在我们有了一个模块来指导 Hilt 如何创建我们的数据存储，我们需要再做一些调整来成功构建。

Hilt 可以提供对其他具有`@AndroidEntryPoint`注释的 Android 类的依赖，以及对`Application`的`@HiltAndroidApp`和对`ViewModel`类的`@HiltViewModel`。如果你用`@AndroidEntryPoint`注释一个 Android 类，那么你也必须注释依赖于它的其他 Android 类。例如，如果您注释了一个`Fragment`，那么您也必须注释您使用那个`Fragment`的任何活动。

这意味着我们需要添加以下内容:

在这个示例中，我们没有任何其他的**复杂注入**，比如定制构建器、工厂或接口实现。所以我们可以依赖于句柄和构造函数注入来获得我们需要传递的任何其他依赖项。我们将在一个类的构造函数中使用`@Inject`注释来指示 Hilt 如何提供它的实例:

最后，让我们将`preferencesDataStore`委托从`TasksActivity`顶部完全移除，因为我们不再需要它了。我们还将改变我们的`viewModel`在`TaskActivity` `onCreate`中的提供方式，因为它的依赖项现在将被注入:

## 注入原始数据存储

**Proto DataStore** 将遵循非常相似的模式——您还需要提供一个`UserPreferencesSerializer`和一个关于如何从`SharedPreferences`迁移的精确指令，我们已经在[**Proto DataStore post**](/androiddevelopers/all-about-proto-datastore-1b1af6cd2879)**:**中对此进行了更详细的介绍

就是这样！现在，您将能够运行应用程序，并验证所有的依赖项现在都被正确地注入了。

# 待续

我们已经讲述了**如何使用 Hilt** 注入数据存储以实现更好的依赖管理的步骤，设置 Hilt，使用`DataStoreModule`中的`PreferenceDataStoreFactory`来指导 Hilt 如何正确地提供我们的数据存储实例，最后，将它提供给需要它的类。

加入我们系列的下一篇文章，我们将研究如何将**数据存储与 Kotlin 序列化**一起使用。

你可以在这里找到我们 Jetpack DataStore 系列的所有帖子:
[Jetpack DataStore 简介](/androiddevelopers/introduction-to-jetpack-datastore-3dc8d74139e7)
[所有关于首选项 DataStore](/androiddevelopers/all-about-preferences-datastore-cc7995679334)
[所有关于原型 DataStore](/androiddevelopers/all-about-proto-datastore-1b1af6cd2879)
[DataStore 和依赖注入](/androiddevelopers/datastore-and-dependency-injection-ea32b95704e3)
[DataStore 和 Kotlin 序列化](/androiddevelopers/datastore-and-kotlin-serialization-8b25bf0be66c)
[DataStore 和同步工作](/androiddevelopers/datastore-and-synchronous-work-576f3869ec4c)
[DataStore 和数据迁移](/androiddevelopers/datastore-and-data-migration-fdca806eb1aa)
[DataStore 和测试](/androiddevelopers/datastore-and-testing-edf7ae8df3d8)