# 在协程中测试两次连续的 LiveData 发射

> 原文：<https://medium.com/androiddevelopers/testing-two-consecutive-livedata-emissions-in-coroutines-5680b693cbf8?source=collection_archive---------1----------------------->

![](img/769e68b9f4278ed822bf44d65714a12a.png)

*Illustration by* [*Kiran Puri*](https://twitter.com/_kiranpuri)

这篇文章是关于我们如何在开源[格子应用](https://github.com/android/plaid)中通过暂停和恢复协程的`CoroutineDispatcher`来单元测试两个连续的 LiveData 发射。

不要忘记查看文章末尾的良好实践部分，以使您的测试准确、快速和可靠。

# 问题是

我们想要测试两个连续的 LiveData 发射(其中一个是在一个协程中执行的)，但是这是不可能的，因为我们曾经使用[注入](https://github.com/android/plaid/pull/695/files#diff-0c645491d198785a12b5fa5afd6598f5) `[Dispatchers.Unconfined](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-dispatchers/-unconfined.html)`来立即执行所有的协程。因此，当我们可以断言单元测试中的排放时，第一个 LiveData 排放被遗漏了，我们只能检查第二个排放。更多详情如下:

![](img/cd75c5bb296b9382f0c43f3efb928bc1.png)

*Dribbble Shot details screen in Plaid*

在[Dribbble](http://www.dribbble.com)(Plaid 的数据源之一)详细信息屏幕上，我们希望尽可能快地显示屏幕，但是有些元素在显示之前可能需要一些时间来处理(由于延迟地将 markdown 格式化为`Spannables`)。为了解决这个问题，我们决定快速发出 UI 状态的简化版本，然后启动一个后台操作来产生处理后的版本，然后发出它。

为此，我们使用 LiveData 和协同例程:LiveData 用于 UI 通信，协同例程用于执行主线程之外的操作。当 ViewModel 启动时，我们向 UI 观察的 LiveData 发出一个基本的 UI 模型。然后，我们调用`[CreateShotUiModel](https://github.com/android/plaid/blob/master/dribbble/src/main/java/io/plaidapp/dribbble/domain/CreateShotUiModelUseCase.kt)` [用例](https://github.com/android/plaid/blob/master/dribbble/src/main/java/io/plaidapp/dribbble/domain/CreateShotUiModelUseCase.kt)，它将执行移到后台并创建完整的 UI 模型。当用例完成时，ViewModel 像以前一样将完整的 UI 模型发送到相同的 LiveData。这可以在下面的代码中看到:

[*参见此处*](https://github.com/android/plaid/blob/master/dribbble/src/main/java/io/plaidapp/dribbble/ui/shot/ShotViewModel.kt) 的完整代码

我们想要测试两种 UI 状态都被发送到 UI。但是，我们无法验证第一次发射，因为两个 UI 状态是连续发射的，而 LiveData 实例只包含第二次发射。发生这种情况是因为在我们的测试中，由于`[Dispatchers.Unconfined](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-dispatchers/-unconfined.html)`被注入，协同程序在`processUiModel`中启动并同步执行。

Plaid 使用一个名为`[CoroutinesDispatcherProvider](https://github.com/android/plaid/blob/master/core/src/main/java/io/plaidapp/core/data/CoroutinesDispatcherProvider.kt)`的类将协程调度程序注入到使用协程的类中。

> LiveData 只保存它收到的最后一个值。为了在测试中测试 LiveData 的内容，我们使用了`[LiveData.getOrAwaitValue()](https://github.com/android/architecture-components-samples/blob/master/LiveDataSample/app/src/test/java/com/android/example/livedatabuilder/util/LiveDataTestUtil.kt)`扩展函数。

我们要求的以下单元测试失败了:

我们如何测试这种行为？

# 解决方案

我们使用来自协程库(`kotlinx.coroutines.test`包)的新`[TestCoroutineDispatcher](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-test/kotlinx.coroutines.test/-test-coroutine-dispatcher/)`来暂停和恢复由`ViewModel`创建的协程的`CoroutineDispatcher`。

> *免责声明* : TestCoroutineDispatcher 还是一个实验性的 API。

使用注入的`TestCoroutineDispatcher`实例，我们可以控制协程何时开始执行。测试逻辑如下:

1.  在测试开始之前，暂停调度程序并检查在 ViewModel init 期间是否发出了 fast 结果。
2.  恢复启动视图模型中`processUiModel`方法的协程的测试调度程序。
3.  验证是否发出了慢速结果。

[*完整代码见此处*](https://github.com/android/plaid/blob/master/dribbble/src/test/java/io/plaidapp/dribbble/ui/shot/ShotViewModelTest.kt#L159)

在此处查看测试[中注入](https://github.com/android/plaid/pull/695/files#diff-0c645491d198785a12b5fa5afd6598f5)`[CoroutinesDispatcherProvider](https://github.com/android/plaid/blob/master/core/src/main/java/io/plaidapp/core/data/CoroutinesDispatcherProvider.kt)`的差异。

我们在`testCoroutineDispatcher.[runBlockingTest](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-test/kotlinx.coroutines.test/run-blocking-test.html)` body lambda 中包含测试体和断言，因为与`[runBlocking](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/run-blocking.html)`类似，它将同步执行使用`testCoroutineDispatcher`的协程。

> *免责声明 2* :为了避免每次测试都必须建立和拆除一个`TestCoroutineDispatcher`，您可以像在其他[测试](https://github.com/android/plaid/blob/master/search/src/test/java/io/plaidapp/search/ui/SearchViewModelTest.kt)中一样使用这个 [JUnit 测试规则](https://github.com/android/plaid/blob/master/test_shared/src/main/java/io/plaidapp/test/shared/MainCoroutineRule.kt)。

## 另一种方法

你可以选择其他方法来解决这个问题。当我们面临这个问题时，我们选择了我们认为最好的一个，因为它不需要更改应用程序代码。

另一种实现方式是在 ViewModel 中使用新的[liveData coroutines builder](https://developer.android.com/topic/libraries/architecture/coroutines#livedata)来发出这两个项目，在测试中，使用`[LiveData.asFlow()](https://android.googlesource.com/platform/frameworks/support/+/refs/heads/androidx-master-dev/lifecycle/lifecycle-livedata-ktx/src/main/java/androidx/lifecycle/FlowLiveData.kt?source=post_page---------------------------%2F%2F%2F%2F%2F&autodive=0%2F%2F%2F%2F%2F%2F%2F%2F%2F%2F%2F%2F%2F%2F#86)`扩展函数来断言这些元素，正如您在[这个 PR](https://github.com/android/plaid/pull/770) 中看到的。这种方法避免了在测试中停止调度程序，并有助于将测试从实现中分离出来，但需要更改 ViewModel 实现以使用生命周期协程扩展中可用的最新 API。

# 以及良好的实践

为了使您的测试准确、快速和可靠，您应该:

## 始终注入调度器！

如果调度程序没有被注入到允许我们在测试中使用`TestCoroutineDispatcher`的 ViewModel 中，我们不可能解决这个问题。

作为一个好的实践，**总是将调度程序注入到那些使用它们的类中**。你不应该在你的类中直接使用 coroutines 库附带的预定义调度程序(例如`Dispatchers.IO`)，因为这会增加测试的难度，把它们作为依赖项传递。

看到这些在任何类中被直接使用都是一种代码味道，如下面的代码所示:

具体说一下 [AAC ViewModels](https://developer.android.com/topic/libraries/architecture/viewmodel) 以及默认为`Dispatchers.Main`的`viewModelScope`的用法，由于`viewModelScope`代码不能更改，所以不是注入，而是需要覆盖。我们通过将这个 JUnit 规则与注入到其他类中的同一个`TestCoroutineDispatcher`实例一起使用[来做到这一点，参见](https://github.com/android/plaid/blob/master/test_shared/src/main/java/io/plaidapp/test/shared/MainCoroutineRule.kt)[这里的一个例子](https://github.com/android/plaid/blob/master/search/src/test/java/io/plaidapp/search/ui/SearchViewModelTest.kt#L72)。要了解更多，请阅读这篇关于 `[viewModelScope](/androiddevelopers/easy-coroutines-in-android-viewmodelscope-25bffb605471)`的[文章。](/androiddevelopers/easy-coroutines-in-android-viewmodelscope-25bffb605471)

## 注入 TestCoroutineDispatcher 而不是 Dispatchers。自由的

将一个`[TestCoroutineDispatcher](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-test/kotlinx.coroutines.test/-test-coroutine-dispatcher/)`实例注入到您的类中，并使用`[runBlockingTest](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-test/kotlinx.coroutines.test/run-blocking-test.html)`方法在您的测试中同步运行使用该调度程序的协同程序。您也可以使用`TestCoroutineDispatcher`来恢复和暂停协程。

作为一般规则，在三个预定义的调度程序中注入相同的`TestCoroutineDispatcher`实例(即`Main`、`Default`、`IO`)。如果您需要验证多线程场景中任务的计时(例如，您想要测试同时运行的协程的排列)，那么为每个预定义的调度程序创建并注入一个不同的`TestCoroutineDispatcher`实例。

## **调度员呢。无约束？**

如果希望同步执行协程内部的代码，也可以注入`Dispatchers.Unconfined`进行测试(`kotlinx-coroutines`目前使用它进行测试)。然而，`Unconfined`给你的灵活性比`TestCoroutineDispatcher`小:你不能暂停`Unconfined`，而且它仅限于立即派遣。

它还将打破使用不同调度程序的代码的假设和计时。这在测试并行计算时更加明显，例如，它们将在编写它们的另一个程序中执行，你不能测试那些在不同时间完成的计算的不同排列。

`Unconfined`和`TestCoroutineDispatcher`都明确避免并行执行。然而，`TestCoroutineDispatcher`让您对暂停模式下并发执行的顺序有了更多的控制，但是它本身不足以测试每一种排列。常规的测试建议在这里适用，如果你在做复杂的并发行为，你需要在设计代码时考虑到可测试性。

## 测试 LiveData

有关测试 LiveData 的更多最佳实践，请查看 [Jose Alcerreca](https://twitter.com/ppvi) 关于这个的[帖子。](/androiddevelopers/unit-testing-livedata-and-other-common-observability-problems-bb477262eb04)

*此处* *见全格子 PR*[*。*](https://github.com/android/plaid/pull/695)