# 开发者也是用户—简介

> 原文：<https://medium.com/androiddevelopers/developers-are-users-too-introduction-fefdb42f05a?source=collection_archive---------4----------------------->

![](img/ae2104eef4f71fbc58c71419f7249fa1.png)

Images: [Virgina Poltrack](https://twitter.com/VPoltrack)

## 可用性——从 UI 中学习，在 API 中应用

当谈到 ***可用性*** 时，我们往往会想到用户界面，可能是地图、消息或照片共享应用程序。我们希望我们的用户界面有几个特点，例如地图应用程序应该:

*   **直观**——能够轻松地知道如何从 A 地导航到 b 地
*   **高效**——快速获取导航方向。
*   **纠正**——获得从 A 到 B 的正确路径，没有任何路障或其他障碍。
*   提供**适当的功能** —能够浏览地图、放大、缩小和导航。
*   提供**对功能**的适当访问——只需捏捏地图就能缩小。

我们应该期望我们所使用的 API 也具有同样的品质。如果 UI 是用户和功能之间的接口，那么 API 就是使用它的开发人员和实现该功能的代码体之间的接口。像 ui 一样，API 也需要是可用的。

库、框架、SDK——API 无处不在！每当您将代码分成模块时，模块公开的类和方法就变成了 API。是其他开发者(和‘未来的你’)！)需要合作。

可用性可以用理解如何使用某物所需时间的倒数来衡量。初学者和专家开发人员都花费大量时间学习如何使用新的 API。可用性低会导致 API 的不正确使用，从而导致错误甚至安全问题。这些问题最终不仅会影响集成这些 API 的开发人员，还会影响这些应用程序的用户。这就是为什么提供一个可用的 API 是至关重要的。

Nielsen 和 Molich 创建了一套众所周知的 [UI 可用性试探法](https://www.nngroup.com/articles/ten-usability-heuristics/)，可以很容易地应用于任何产品，包括 API，并且可以结合 Bloch 的[指南](https://dl.acm.org/citation.cfm?id=1176622)来设计一个好的 API。

1.  [系统状态的可见性](/google-developers/developers-are-users-too-part-1-c753483a50dc#a062)
2.  [系统与现实世界的匹配](/google-developers/developers-are-users-too-part-1-c753483a50dc#fd9a)
3.  [用户控制和自由](/google-developers/developers-are-users-too-part-1-c753483a50dc#52bc)
4.  [一致性和标准](/google-developers/developers-are-users-too-part-1-c753483a50dc#7d0b)
5.  [错误预防](/google-developers/developers-are-users-too-part-1-c753483a50dc#6f9b)
6.  [承认而不是回忆](/google-developers/developers-are-users-too-part-2-96e03fe17535#b705)
7.  [灵活性和使用效率](/google-developers/developers-are-users-too-part-2-96e03fe17535#0709)
8.  [唯美极简设计](/google-developers/developers-are-users-too-part-2-96e03fe17535#3033)
9.  [帮助用户识别、诊断错误并从中恢复](/google-developers/developers-are-users-too-part-2-96e03fe17535#d40e)
10.  [帮助和文档](/google-developers/developers-are-users-too-part-2-96e03fe17535#e86b)

在接下来的文章中，我们将深入了解这些原则，并研究它们如何应用于 API 设计。敬请期待！