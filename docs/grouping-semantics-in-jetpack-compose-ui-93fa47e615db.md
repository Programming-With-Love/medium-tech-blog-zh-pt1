# Jetpack 撰写 UI 中的分组语义

> 原文：<https://medium.com/google-developer-experts/grouping-semantics-in-jetpack-compose-ui-93fa47e615db?source=collection_archive---------1----------------------->

语义修饰符让我们改变 Jetpack Compose UI 中的[语义树](https://developer.android.com/jetpack/compose/semantics)的各个方面——这是一种对可访问性服务和测试框架有帮助的 UI 表示。

我们可以使用这些修饰符将许多小部件组合成一个逻辑元素，这样就可以使用诸如 TalkBack 之类的可访问性服务更快地浏览相似元素的列表。

例如，在 YouTube Music 中，当前播放列表表示为一个曲目列表，每个曲目包含:

*   一个标题
*   艺术家
*   轨道长度
*   播放/暂停动作
*   查看更多/菜单操作
*   (状态—正在播放)

![](img/de38850b2e2f72c9342826554a9b7a49.png)

像 TalkBack 这样的屏幕阅读器会用一个内在的描述(文本)或动作来关注每个元素，这意味着从一个轨道导航到下一个轨道会花费用户~5 个手势。

## 重写语义

我们可以重新编写轨道的语义信息，以便将其表示为单个逻辑元素:

使用`clearAndSetSemantics()`修改器是**必需的**；在这种情况下，使用`semantics()`修饰符是不够的。

## Modifier.clearAndSetSemantics()

`Modifier.clearAndSetSemantics()`将清除所有后代节点的语义信息，并用给定的属性更新当前节点。

该函数的文档说明如下:

> 这可以用来提供更好的屏幕阅读器体验:例如，清除一组小按钮的语义，并在包含它们的卡片上设置等效的动作。

这正是我们想要的！但是，清除后代节点的语义有一些含义:

*   任何基于文本的后代将不再是焦点，也不会包含在内容描述中。我们的内容描述必须是对整行的忠实描述。
*   任何可点击的后代将不再是焦点。相反，我们必须在行上公开这些操作(`customActions`)。

## Modifier.semantics()

`Modifier.semantics()`让我们给当前节点添加语义信息。我们的目标是将每个轨迹的表示简化为一个节点，所以这在这里没有帮助。

事实上，如果让整行都成为可聚焦的(除了我们已经拥有的)，情况会更糟。

## modifier . semantics(merge descendants = true)

使用`Modifier.semantics(mergeDescendants = true)`稍微有用一些，因为它会减少可聚焦元素的数量。

将`mergeDescendants`设置为`true`将会:

*   移除所有后代节点(除非他们也在使用`mergeDescendants = true`)
*   尽可能将属性合并在一起

带有`clickable`修饰符的元素不会被删除，因为它们使用了`mergeDescendants = true`。在我们的例子中，这意味着播放/暂停和菜单动作仍然是焦点。

标题、艺术家和曲目长度节点不可点击。这里，将尝试合并来自这些节点的语义信息，这将导致重复的信息，因为我们手动设置内容描述。

## 概述

`Modifier.clearAndSetSemantics()`在 Compose UI 中，我们可以对节点及其后代的语义表示进行大量控制。虽然它比`Modifier.semantics()`更强大，但我们也需要更加小心。

根据经验，考虑将其用于集合中的元素(列表或网格),并始终测试其行为。

如果你喜欢这篇文章，有任何问题，评论或更正，请联系[推特](https://twitter.com/ataulm/status/1485180047778537476)。