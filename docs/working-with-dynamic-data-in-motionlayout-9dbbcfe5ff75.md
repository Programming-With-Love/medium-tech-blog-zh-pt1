# 在 MotionLayout 中使用动态数据

> 原文：<https://medium.com/androiddevelopers/working-with-dynamic-data-in-motionlayout-9dbbcfe5ff75?source=collection_archive---------0----------------------->

![](img/5007c514b0aefc93100c7303eacb5ba5.png)

Illustration by [Virginia Poltrack](/@VPoltrack).

可以使用 MotionLayout 来创建数据的动态动画吗？这是任何你在编译时不知道的数据——比如用户输入。是啊！绝对的。您可以使用 MotionLayout 代码 API 在代码中动态创建 MotionScene。

在这篇文章中，我们将使用 MotionLayout 开发一个动态直方图来显示它的变化。如何构建一个直方图视图小部件，它可以获取任意数据，并能够使用 MotionLayout 进行显示、排序:

此示例旨在使用高度动态的数据来探索如何使用 MotionLayout 在代码中实现动画。

让我们开始吧！

下面例子的源代码可以在[这里](https://github.com/android/views-widgets-samples/tree/master/ConstraintLayoutExamples/motionlayout/src/main/java/com/google/androidstudio/motionlayoutexample/histogramdemo)找到:

## 在开始之前

本文假设了 MotionLayout、ConstraintLayout、ConstraintSet 和 Android UI 的基本知识。如果你是 MotionLayout 的新手，请查看 [MotionLayout codelab](https://codelabs.developers.google.com/codelabs/motion-layout/index.html) ，学习使用 MotionScene、过渡、约束和约束集构建动画的基础知识。

很少有关于 MotionLayout 的文章对该系统进行概述。我们强烈建议您在阅读本文之前先从它们开始:

*   MotionLayout 介绍([第一部](/p/29208674b10d)
*   自定义属性、图像过渡、关键帧([第二部分](/p/a31acc084f59))
*   在现有布局中利用 motion layout(coordinator layout、DrawerLayout、ViewPager) ( [第三部分](/p/47cd64d51a5))

建议至少通读上述文章的第一部分。

# 柱状图

## 开始:

我们将从一个自定义 ConstraintLayout 开始，它可以以编程方式创建和设置直方图 UI。我们将使用[nonanimatinghistogramgwidget . kt](https://github.com/android/views-widgets-samples/blob/master/ConstraintLayoutExamples/motionlayout/src/main/java/com/google/androidstudio/motionlayoutexample/histogramdemo/NonAnimatingHistogramWidget.kt)作为我们的主干。

使用这个小部件，我们可以显示任意数据，但是看起来很无聊:

这个小部件能够使用 ConstraintLayout 显示动态直方图。您应该花点时间来探索它是如何工作的。

1.  在 onFinishInflate 中，通过调用 createBars 来创建和添加条。这将实例化 mSize 按钮，将它们添加到视图中，并创建一个水平链来布局它们。
2.  当数据改变时，调用 setData。这负责更新约束以匹配新数据。请记住，我们没有设置动画，所以只有一个约束集(最终集或所需的屏幕)。对其应用新高度后，ConstraintLayout 会立即将条形跳转到新数据。

## 第一步。创建占位符转场

首先，我们需要将 ConstraintLayout 转换为 MotionLayout。MotionLayout 要求我们有一个 MotionScene 才能运行，所以我们将在代码中创建一个。占位符过渡将从当前布局到当前布局——这还不是一个非常激动人心的动画，但它将为我们提供我们需要的代码，以在一秒钟内构建更多的动画..

我们可以通过编程来创建这个过渡+运动场景:

让我们一步一步来。

1.  在像前面一样调用 createBars(它设置了包含水平链的默认约束集)之后，我们准备开始创建一个过渡。
2.  然后，我们创建开始和结束约束集，并使其与当前布局相同。您可能已经注意到我们正在创建的约束集 Id(例如 startSetId 和 endSetId)。这些 id 是 MotionScene 所理解的。
3.  然后创建占位符过渡。这里，Transition 也获得了一个 ID。
4.  现在我们有了一个过渡，我们可以创建一个 MotionScene，应用该过渡，然后将其设置为该 MotionLayout 的 MotionScene。

每个 MotionLayout 都需要一个 MotionScene 和过渡，因此您需要将类似的代码应用到您构建的任何自定义 MotionLayout 类中。

MotionLayout 要求所有约束集和过渡实例都有唯一的 ID。`View.generateViewId` 是在您的应用程序中动态生成这些内容的便捷方式。

如果由于 API 版本限制，您的应用程序无法使用 View.generateViewId，您可以创建一个空的 motion scene.xml，并将其设置为如下所示的`app:layoutdescription` :

xml/empty_scene.xml:

在 HistogramWidget.kt 中:

这与以编程方式创建占位符过渡是一样的。

# 第二步。制作条形高度的动画

在这一步，我们将学习如何使用占位符过渡。

让我们看看我们需要在 setData 中进行的更改:

与非动画 HistogramWidget 不同，动画 HistogramWidget 需要两个约束集，当前(`startSet`)状态和下一个(`endSet`)状态。幸运的是，我们只是在转换中创建了两个约束集。当我们浏览数据时，我们可以通过在开始和结束约束集上设置不同的高度来创建过渡。

它看起来是这样的:

这很简单。您正在设置从开始到结束的转换，然后，我们将结束状态克隆到开始状态。当我们从 setData 中重新执行 currentHeight 时，这可能是不必要的，但在后面的示例中它会派上用场。

通过这些更改，您应该能够看到 setData 正常工作:

我们简要地研究了当结束状态接收动态数据时如何制作动画。我们能对起始状态做同样的事情吗？

在 setData 方法中，我们使用基于先前动画结果的 currentBars 重置 startSet。如果我们将它替换为当前的条形高度，这是动态的，这将允许动画被中断。新的动画可以在上一个动画还没有完成之前开始！基本上，起始状态也可以在代码中动态调整。

下面是如何使 setData 可中断的示例:

为了保持简洁，我们不会进一步讨论可中断动画，但是我们鼓励你看看“setData”中的例子，并实现你自己的“动画可中断排序”！

# 第三步。动画排序

本节我们将实现排序并学习动态数据动画的最佳实践。

首先，这里有一个关于排序实现的例子:

与 setData 类似，我们基本上是在更新开始和结束约束集。排序后，我们将依靠 mNextBars 来确定链 id 顺序。

动画小部件代码根本不需要修改。

查看 github 代码，看看[nonanimatinghistogramgwidget . kt](https://github.com/android/views-widgets-samples/blob/master/ConstraintLayoutExamples/motionlayout/src/main/java/com/google/androidstudio/motionlayoutexample/histogramdemo/NonAnimatingHistogramWidget.kt)还有什么变化。通过对数据进行一些调整，您应该能够看到排序是如何工作的:

# 结论

你有它！要处理动态数据，

1.  我们已经创建了一个占位符过渡，使用克隆布局的开始约束集和结束约束集。
2.  我们根据想要显示的动画更新了开始和结束约束集
3.  我们从头到尾触发了动画

MotionLayout 可以让你构建丰富的动画，使用 Motion Editor 和 XML 来构建漂亮的动画相当有趣。当你深入幕后时，你会发现你可以完全用代码构建复杂的 MotionLayout 动画！

我迫不及待地想看看你做了什么。