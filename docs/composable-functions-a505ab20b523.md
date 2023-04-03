# 可组合函数

> 原文：<https://medium.com/androiddevelopers/composable-functions-a505ab20b523?source=collection_archive---------0----------------------->

![](img/52145c9370bb785e22eb90da1e9e801a.png)

在之前的 MAD Skills Compose Basics [文章](/androiddevelopers/thinking-in-compose-c4ef150bb7cf)中，您学习了如何在 Compose 中思考——您在 Kotlin 中将您的 UI 描述为函数。不需要更多的 XML！在本文中，我们将深入研究这些函数，以及如何用它们来构建 UI。

> 提醒一下，我们将在 10 月 13 日的现场问答环节中回答您关于作曲基础的问题。请务必在这里、YouTube 或 Twitter 上使用#MADCompose 标签发表评论。

为了理解这些函数是如何工作的，让我们看看如何构建 [Jetsurvey](https://github.com/android/compose-samples/tree/main/Jetsurvey) 的单选问题屏幕，这是我们的[撰写示例](http://goo.gle/compose-samples)之一。

![](img/bff620912ceb55b668b82f2fc497a2a2.png)

你可以在这里观看本文的视频:

# 简单的可组合函数

调查中的单个答案可以编写为包含一行图像、文本和单选按钮的函数。

要在 Compose 中创建 UI 组件，我们必须用`**@Composable**`注释来注释函数。这个注释告诉 Compose 编译器，这个函数的目的是将数据转换成 UI，从而将答案转换成 UI。

带有该注释的函数也被称为**可组合** **函数**，或简称为 composables。这些函数是 Compose 中 UI 的构建块。添加这个注释很快也很容易，它鼓励您将 UI 组织成一个可重用的元素库。

例如，要实现一个可供选择的可能答案列表，我们可以定义一个名为`SingleChoiceQuestion`的新函数，它接受一个答案列表，然后调用我们刚刚定义的`SurveyAnswer`函数。

`SingleChoiceQuestion`接受允许由应用程序逻辑配置的参数。在这种情况下，它接受一个可能答案的列表，以便可以向 UI 显示这些选项。注意，composable 不返回任何东西(它返回‘unit ’),而是发出 UI 。具体来说，它发出了`Column`布局 composable，这是垂直排列项目的 Compose toolkit 的一部分。在该列中，它为每个答案发出一个可组合的`SurveyAnswer`。

组件是不可变的。您不能保存对它们的引用—就像保存对单个答案的引用一样—然后更新其内容。当你调用它的时候，你需要传递所有的信息作为参数。

注意，由于函数是用 Kotlin 编写的，我们可以使用完整的 Kotlin 语法和控制流来生成我们的 UI。这里我们使用`forEach`来遍历每个答案，并调用`SurveyAnswer`来显示它们。如果我们想有条件地显示其他内容，那么使用 If 语句就很简单。不需要`View.visibility = View.GONE`或`View.INVISIBLE`。对于像 Compose 这样的声明式 UI 框架，如果您希望您的 UI 根据所提供的输入而有不同的外观，那么 composable 必须描述您的 UI 对于每个可能的输入值应该是什么样子。使用类似下面代码片段的条件语句可以实现这一点。

组件必须**快速且无副作用**。当用同一个参数多次调用它时，它的行为必须相同，并且它不应该修改属性或全局变量。我们说具有这种性质的函数是*幂等的*。该属性对于所有组件都是必需的，以便在用新值重新调用函数时可以正确发出 UI。

注意，提供给函数**的参数完全由** **控制 UI** 。这就是我们所说的将状态转化为 UI。函数中的逻辑将保证 UI 永远不会失去同步。如果答案列表发生变化，那么通过再次执行该函数并在必要时重新绘制 UI，从新的答案列表中生成一个新的 UI。

这个在状态改变时重新生成 UI 的过程叫做**重组。**由于组合是不可变的，重组是用新状态更新 UI 的机制。

# 可组合函数的重组和状态

当用不同的函数参数重新调用一个可组合函数时，就会发生重组。发生这种情况是因为状态函数依赖于变化。

例如，假设`SurveyAnswer`组件接受一个参数`isSelected`，该参数决定答案是否被选中。最初，没有选择答案:

在视图世界中，通过点击其中一个答案 UI 元素进行交互会在视觉上切换它，因为视图拥有自己的状态。然而，在 Compose 世界中，因为 **false** 被提供给所有的`SurveyAnswer` composables，所以尽管用户交互，所有的答案都将保持未被选择。为了使它们在视觉上响应用户交互，需要重新组合可组合组件，以便可以用新的状态重新生成 UI。

为此，需要引入一个新的变量来保存所选的答案。此外，变量需要是一个`[MutableState](https://developer.android.com/reference/kotlin/androidx/compose/runtime/MutableState)`——一个集成在 Compose 运行时中的可观察类型。对状态的任何更改都会自动为读取它的任何组件安排一个重组。可以用`[mutableStateOf](https://developer.android.com/reference/kotlin/androidx/compose/runtime/package-summary#mutableStateOf(kotlin.Any,androidx.compose.runtime.SnapshotMutationPolicy))`方法创建一个新的`MutableState`，如下所示:

请注意，在上面的代码片段中，`isSelected`的值已经更新，以便将当前答案与`selectedAnswer`进行比较。由于`selectedAnswer`属于`MutableState`类型，我们必须使用 value 属性来获取所选答案。当该值改变时，Compose 将自动重新执行`SurveyAnswer`，以便突出显示所选答案。

然而，上面的片段不太适用。需要在整个重组过程中记住`selectedAnswer`的值，以便在`SingleChoiceQuestion`被重新调用时不会被覆盖。为了解决这个问题，`mutableStateOf`调用应该在一个调用中执行。这保证了当可组合重新组合时，该值被记住，而不是被重置。为了记住配置变化的值，我们也可以使用`rememberSaveable`:

通过在`selectedAnswer`变量上使用 Kotlin 的 delegated property 语法，可以进一步细化上面的代码片段。这样会将类型从`MutableState<Answer?>`更改为简单的`Answer?`。这种语法非常好，因为我们可以直接处理底层状态的值——不再需要调用`MutableState`对象的`value`属性:

使用我们新引入的状态，我们可以为`onAnswerSelected`参数传递一个 lambda 函数，这样我们就可以在用户做出选择时执行一个操作。在这个 lambda 的定义中，我们可以将`selectedAnswer`的值设置为新值:

如果您还记得上一篇文章，事件是状态更新的机制。这里，当用户通过点击答案进行交互时，将调用事件`onAnswerSelected`。

Compose 运行时自动跟踪状态读取发生的位置，以便它可以智能地重新组合依赖于该状态的可组合组件。因此，**你不需要显式地观察状态，或者手动更新 UI** 。

# 组件的行为和属性

您还应该知道可组合函数的其他一些行为属性。由于这些行为，**重要的是，您的可组合函数没有副作用，并且在使用相同的参数**多次调用时表现相同。

1.  [组件可以以任何顺序执行](https://developer.android.com/jetpack/compose/mental-model#any-order)

查看下面的代码片段，您可能会认为代码是按顺序运行的。但这不一定是真的。Compose 可以识别出某些 UI 元素比其他元素优先级高，因此这些元素可能会先被绘制。比方说，您有一段代码，它在一个选项卡布局中绘制了三个屏幕。您可能会假设首先执行`StartScreen`，但是，这些执行可以以任何顺序发生。

2.[可组合功能可以并行运行](https://developer.android.com/jetpack/compose/mental-model#parallel)

Composables 可以并行运行，从而利用多个内核来提高屏幕的渲染性能。在下面的代码片段中，代码运行时没有副作用，并将输入列表转换为 UI。

但是，如果函数写入局部变量，比如下面的代码片段，代码就不再被认为是没有副作用的。做类似于下面代码的事情可能会导致你的用户界面出现奇怪的行为。

3.[尽可能重组跳过](https://developer.android.com/jetpack/compose/mental-model#skips)

Compose 尽力只重组 UI 中需要更新部分。如果一个可组合不使用触发重组的状态，那么它将被跳过。在代码片段中，如果名称字符串发生变化，那么`Header`和`Footer`组合将不会被重新执行，因为它不依赖于那个状态。

4.[重组乐观](https://developer.android.com/jetpack/compose/mental-model#optimistic)

重组是乐观的—这意味着 Compose 期望在参数再次改变之前完成重组。如果某个参数在重组完成之前发生了更改，Compose 可能会取消重组，并使用新参数重新启动它。

5.[可组合功能可能会频繁运行](https://developer.android.com/jetpack/compose/mental-model#frequent)

最后，可组合函数可能会频繁运行。如果您的可组合函数包含需要为每一帧执行的动画，可能会出现这种情况。这就是为什么确保可组合函数快速运行以避免丢帧是很重要的。

如果需要执行长时间运行的操作，不要在 composable 函数中执行，而是在 UI 线程之外执行，并且只使用 composable 函数中的结果。

# 摘要

我们涵盖了很多！总结一下:

*   您可以使用`@Composable` 注释来创建可组合的函数
*   创建 composables 既快速又简单，鼓励您将 UI 组织成一个可重用组件库。
*   组件可以也应该接受参数来配置它们的行为
*   **MutableState，remember** 和 **rememberSaveable** 可用于存储组件的状态，并让 Compose 自动跟踪和重新编写更改
*   成分应该是无副作用的

我们还学习了一些有趣的组合属性。组件可以:

*   以任何顺序执行
*   并行运行
*   被跳过
*   经常跑步

Compose toolkit 提供了许多基础的、强大的组件，帮助您构建漂亮的应用程序。这就是我们接下来要讨论的内容。如果您想提前了解，请查看以下资源:

*   [构成中的基本布局](https://developer.android.com/codelabs/jetpack-compose-layouts)
*   [Jetpack 撰写主题](https://developer.android.com/codelabs/jetpack-compose-theming)
*   [构图中的主题化](https://developer.android.com/jetpack/compose/themes)
*   [合成样本](http://goo.gle/compose-samples)

有什么问题吗？请在下面留下评论或使用 Twitter 上的#MADCompose 标签，我们将在 10 月 13 日的直播问答中回答您的问题。敬请期待！

不是所有用`@Composable`标注的函数都返回 UI。例如，调用`remember`的函数不会返回 UI，但是，它们会在编写 UI 树中产生一个节点。