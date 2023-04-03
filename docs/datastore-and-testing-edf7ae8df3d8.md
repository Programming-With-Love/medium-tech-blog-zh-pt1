# 数据存储和测试

> 原文：<https://medium.com/androiddevelopers/datastore-and-testing-edf7ae8df3d8?source=collection_archive---------2----------------------->

![](img/1fea125b5da043ce725eedc85b702dab.png)

在我们的 [**Jetpack 数据存储系列**](/androiddevelopers/introduction-to-jetpack-datastore-3dc8d74139e7) 的最后一篇文章中，我们将讲述如何**成功测试您的数据存储。**

# 测试数据存储

每一个好故事都需要好的检验！为了总结我们的系列，我们将回顾如何使用**测试您的数据存储库**。同样，我们将参考 [**首选项 codelab**](https://developer.android.com/codelabs/android-preferences-datastore#0) 作为起点。但是，请记住，您可以使用此材料来设置[**Proto DataStore**](https://developer.android.com/codelabs/android-proto-datastore#0)测试，因为它与 Preferences 非常相似。

要全面测试数据存储，首先需要完成 [**仪器测试**](https://developer.android.com/training/testing/unit-testing/instrumented-unit-tests) 的设置。这将使我们能够验证**实际更新**正在根据我们的预期对我们的存储进行，因为我们将从**实际文件**中写入和读取。

也有可能在单元测试时仅仅**模仿你的数据存储实例**并将其作为依赖注入到另一个类，但是你不能在数据存储本身上运行**任何真正的验证检查**。

## 首选项数据存储测试

在我们的 codelab 中，负责向我们的数据存储库读写数据的类是`UserPreferencesRepository`，所以我们想要验证这个类，以及它与数据存储库交互的**函数，是否按预期工作。**

我们创建一个`UserPreferencesRepositoryTest`类:

现在我们已经有了测试的框架，让我们开始创建我们的测试主题，`UserPreferencesRepository`，并以我们的方式向后工作！

为了获得`UserPreferencesRepository`的实例，我们需要传递一个数据存储实例:

我们将构建一个快速的`testDataStore`实例，它将创建一个单独的测试文件，我们可以进一步使用它来设置和验证虚拟数据:

我们使用`PreferenceDataStoreFactory`创建一个首选项数据存储实例，并传递:

*   `testCoroutineScope` —我们的测试操作将在其中执行的协程作用域
*   `produceFile` —使用来自`ApplicationProvider`的`testContext`，构建一个我们将用于写入和读取测试数据的测试文件:

由于数据存储基于 Kotlin 协程，我们需要确保我们的测试有正确的协程设置。为此，我们需要添加:

这是一个执行协程的`CoroutineDispatcher`，默认情况下，它是立即执行的。这意味着任何计划运行的任务都会立即执行。

这是一个为测试提供对协程执行的详细控制的范围。添加的`Job`允许我们在每次测试后，作为常规清理的一部分，轻松地取消协程。

设置完成后，我们的测试类应该如下所示:

我们的第一个测试将只是在创建`testDataStore`时验证我们数据的状态。我们将在没有 **订阅流**的情况下获取数据**的快照，并根据我们的预期结果进行快速检查。在我们的`UserPreferencesRepository`中，这是我们想要测试的第一个函数:**

当第一次创建存储库时，它检查数据存储是否为空，并在这种情况下设置我们的数据的默认实例:

如果我们考虑这个测试用例的必要步骤，我们需要:

1.  创建一个保存了默认值的`testDataStore`——完成✅
2.  创建测试主题`UserPreferencesRepository` —完成✅
3.  设置代表我们期望在`testDataStore`中发现什么的`expectedUserPreferences`——还没有完成！
4.  呼叫`repository.fetchInitialPreferences()` —还没完成！
5.  验证返回值是否与我们预期的结果相匹配——还没有完成！

按照这些步骤，我们的测试应该如下所示:

> *💡你可能会注意到 Android Studio 抱怨“*挂起函数‘fetchInitialPreferences’应该只能从协程或另一个挂起函数*中调用”。*
> 
> *事实上，我们的* `*fetchInitialPreferences()*` *是一个暂停函数，但是因为我们是从测试中调用它，所以我们需要确保避免任何实时延迟。* `*kotlinx.coroutines.test*` *用一个非常简单的测试解决方案再次挽救了局面——用* `*testCoroutineScope.runBlockingTest{}*` *将它包围起来(注意，如果你使用的是 Kotlin 1.6，你可以用* `[*runTest{}*](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-test/kotlinx.coroutines.test/run-test.html)` *)*

一个搞定了，还有一个。我们的下一个测试用例通过启用或禁用存储的`SortOrder`值来验证我们的存储库是否对数据存储库进行了正确的更改:

我们遵循类似的模式:

1.  创建一个保存默认值的`testDataStore`—完成✅
2.  创建测试主题`UserPreferencesRepository` —完成✅
3.  用新值调用`repository.enableSortByDeadline()`——还没完成！
4.  验证来自`repository.userPreferencesFlow`的`testDataStore`值与我们的预期结果相匹配——尚未完成！

添加最后两个测试步骤将如下所示:

这涵盖了我们的基本测试用例。您可以遵循相同的步骤来增加这个存储库类的测试覆盖率。

现在剩下要做的就是维护和清理一个健康的测试环境。最后，让我们看一下现在已经完成的整个测试类:

# 就这样结束了！🎬

在我们关于[**Jetpack DataStore**](/androiddevelopers/introduction-to-jetpack-datastore-3dc8d74139e7)的整个系列中，我们涵盖了许多不同的主题，所有这些主题对于深入理解 DataStore 并在**生产环境**中正确使用它都是至关重要的**。**

我们已经了解了[数据存储是如何工作的，](/androiddevelopers/introduction-to-jetpack-datastore-3dc8d74139e7)它给`SharedPreferences`带来了什么好处，以及它的[偏好](/androiddevelopers/all-about-preferences-datastore-cc7995679334)和[原型](/androiddevelopers/all-about-proto-datastore-1b1af6cd2879)实现。我们也看到了使用哪种[序列化方法，](/androiddevelopers/datastore-and-kotlin-serialization-8b25bf0be66c)如何用刀柄注入[以及最后如何测试。现在就看你自己去尝试了！您还可以从我们的](/androiddevelopers/datastore-and-dependency-injection-ea32b95704e3) [**MAD Skills 视频系列**](https://www.youtube.com/playlist?list=PLWz5rJ2EKKc8to3Ere-ePuco69yBUmQ9C) 中了解数据存储，并参加我们即将举行的现场 Q & A，我们将回答您可能有的所有问题。

您可以在这里找到我们的 Jetpack 数据存储系列的所有帖子:
[Jetpack 数据存储简介](/androiddevelopers/introduction-to-jetpack-datastore-3dc8d74139e7)
[所有关于首选项数据存储](/androiddevelopers/all-about-preferences-datastore-cc7995679334)
[所有关于原型数据存储](/androiddevelopers/all-about-proto-datastore-1b1af6cd2879)
[数据存储和依赖注入](/androiddevelopers/datastore-and-dependency-injection-ea32b95704e3)
[数据存储和 Kotlin 序列化](/androiddevelopers/datastore-and-kotlin-serialization-8b25bf0be66c)
[数据存储和同步工作](/androiddevelopers/datastore-and-synchronous-work-576f3869ec4c)
[数据存储和数据迁移](/androiddevelopers/datastore-and-data-migration-fdca806eb1aa)
[数据存储和测试](/androiddevelopers/datastore-and-testing-edf7ae8df3d8)