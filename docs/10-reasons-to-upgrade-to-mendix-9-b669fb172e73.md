# 升级到 Mendix 9 的 10 大理由

> 原文：<https://medium.com/mendix/10-reasons-to-upgrade-to-mendix-9-b669fb172e73?source=collection_archive---------0----------------------->

![](img/0f1f09fb0d3fb3e92d3d0332e90de1e9.png)

Mendix 9 发布了，它带来了许多令人惊叹的新功能，让 Mendix 的制作者们更有效率。随着 Mendix 9 的正式发布，应用程序开发的门槛再次提高——生产力比以前更高了！

我是 Mohammed Siddiqui，是 Mendix 最有价值专业人员(MVP)和认证的 Mendix 专家，在 AuraQ 担任首席业务工程师/建筑师。在这篇博客中，我分享并盘点了我个人在 Mendix 9 中发布的 10 大特性:

## 10.本机依赖管理

您是否开发了原生移动应用程序，并发现在您的 Mendix 项目中添加和管理 react 原生依赖项很困难？嗯，在 Mendix 9 中，在您的原生移动应用程序中添加额外的 react 原生模块变得更加简单。[更多详情](https://docs.mendix.com/releasenotes/studio-pro/9.0#native-dependency-management)。

## 9.循环时

您的 Mendix 项目中是否需要 while 循环，并且以前必须通过创建占位符列表来执行可重复的操作，从而在微流中解决这个问题？好消息是，您不再需要做这些工作了，因为 Mendix 9 附带了一个名为 While 的条件循环。[更多详情](https://docs.mendix.com/refguide/loop#while)。

## 8.渐进式网络应用(PWA)

需要使用原生移动设备功能构建离线优先的移动应用程序，而无需发布到任何中间应用程序商店？Mendix 9 为您提供保护！现在有一个新的导航配置文件叫做 PWA，这将允许您快速建立一个离线优先的 PWA，并利用 web 浏览器支持的必要离线功能。[更多详情](https://docs.mendix.com/releasenotes/studio-pro/9.0#progressive-web-apps)。

## 7.文本的力量

一个非常常见的用例是在页面中使用[文本模板](https://docs.mendix.com/refguide7/text#general-properties)，但是它们应该基于条件显示——为了满足这个要求，我们许多人使用条件可见性。好消息，Mendix 让我们更轻松了！在 Mendix 9 中，[表达式](https://docs.mendix.com/refguide/expressions)可以作为文本模板参数使用。是的，这是真的。您可以使用 if-then-else 函数、父数据视图的上下文以及关联检索。[更多详情](https://docs.mendix.com/releasenotes/studio-pro/9.0#power-to-the-text)。

## 6.新的 Atlas UI 文件夹结构

我们中的许多人构建可重用的模块，并希望拥有模块特定的页面布局和自定义 UI。有几个解决办法，但我们中的一些人创建了一个只包含样式(CSS)的小部件，它与模块一起支持自定义 UI。从 Mendix 9 开始不再工作。随着 Mendix 9， [Atlas UI 3.0](https://www.mendix.com/atlas/) 发布。还引入了新的文件夹结构，简化了主题的更新和扩展。它允许您创建和共享包含特定布局和模板的模块。您可以为每个模块定义本机和 web 样式、样式资源和设计属性。此外，另一个好消息是，你不需要使用像 [Calypso](https://docs.mendix.com/howto8/front-end/calypso) 这样的外部工具来编译 [Sass](https://sass-lang.com/) ，因为从 Studio Pro 9.0.0 开始，Mendix 会在需要时负责编译 Sass。Mendix 9 发布了许多新的主题特性！[更多详情](https://docs.mendix.com/releasenotes/studio-pro/9.0#new-atlas-ui-folder-structure)。

![](img/dad7076f5ddd5951cfcddce5ec525198.png)

[https://bit.ly/MXW21](https://bit.ly/MXW21)

## 5.MxAssist 性能机器人

性能是任何应用程序都关心的一个方面，如果不遵循 Mendix 开发最佳实践，事情可能会出错。有时您很容易错过一些东西，尤其是如果您是 Mendix 的新手，并且担心引入性能问题。不要再担心了， [MxAssist Performance Bot](https://docs.mendix.com/refguide/mx-assist-performance-bot) 与 Mendix 9 一起发布，它是一个智能虚拟合作开发机器人。该 bot 根据 Mendix 开发最佳实践检查您的模型，以帮助提高您的应用程序的性能。[更多详情](https://docs.mendix.com/releasenotes/studio-pro/9.0#mxassist-performance-bot)。

## 4.按表达式过滤列表

在 Mendix 9 之前，在[列表操作](https://docs.mendix.com/refguide/list-operation)活动中不支持按表达式过滤/查找。为此，你必须使用[循环](https://docs.mendix.com/refguide/loop)或多重列表操作。在 Mendix 9 中，列表操作活动得到了进一步扩展，增加了过滤/查找表达式，使您的工作变得更加轻松。[更多详情](https://docs.mendix.com/releasenotes/studio-pro/9.0#list-filtering-by-expression)。

## 3.新合并算法

在 Mendix 9 之前，app 模型中的冲突解决并不是细粒度的。例如，如果在一个文档中有冲突的更改，那么您必须选择您的更改或应用于整个文档的其他开发人员的更改。在 Mendix 9 中，新的合并算法能够在执行更新或合并时合并应用模型中的更改。您可以通过选取单个部分的更改来解决冲突，而无需替换整个文档。新的合并算法允许细粒度的冲突解决，并能够对小部件列表进行并行更改。[更多详情。](https://docs.mendix.com/releasenotes/studio-pro/9.0#new-merge-algorithm)

## 2.任务排队

我们几乎所有人都使用过 Mendix [Marketplace](https://marketplace.mendix.com/) 的队列模块来满足在后台运行长微流和并行处理的需求，大多数情况下，它用于提高应用程序的性能。有了 Mendix 9，不再需要使用队列市场模块。从 Studio Pro 9.0.0 开始，[任务队列](https://docs.mendix.com/refguide/task-queue)可以作为一个水平可扩展的解决方案在后台执行微流。[更多详情](https://docs.mendix.com/releasenotes/studio-pro/9.0#task-queue)。

最后…

## 1.工作流编辑器

工作流是最大的增强之一，也是我个人最喜欢的 Mendix 9 的新功能。每一个其他的项目都需要某种案例管理或者工作流逻辑。传统上，为了实现这些工作流需求，您必须开发大量的微流和 UI 屏幕，这不仅耗时，而且会变得非常复杂。嗯，随着 Mendix 9 的发布，一个新的可视化[工作流编辑器](https://docs.mendix.com/refguide/workflows)作为测试版功能在 Mendix Studio 和 Studio Pro 中可用。[更多详情](https://docs.mendix.com/releasenotes/studio-pro/9.0#workflows)。

这是我的 10 大功能，但我还可以继续 Mendix 9 还有更多功能，下面是一些没有列在我的列表中的特殊功能，但也值得一提，以提高您的工作效率:

*   **纳流调试:**您现在可以使用纳流调试器为所有应用程序(web、native 和 PWA)调试纳流。
*   **数据网格 2.0:** 一个叫做[数据网格 2.0](https://marketplace.mendix.com/link/component/116540) 的新模块在 Mendix marketplace 上可用，它非常灵活和强大。
*   **复制粘贴多个实体和注释:**可以在领域模型中复制粘贴多个实体和注释。

![](img/76762b35c0a233d4d4cd67f202efd9e3.png)

要了解更多新功能和激动人心的产品发布，请务必注册 Mendix World 2021。我和我的许多同事将参加今年最大的低代码活动之一，该活动定于 2021 年 9 月 7 日至 9 日举行——[您可以在此免费注册](https://bit.ly/MXW21)！

## 阅读更多

[](https://bit.ly/MXW21) [## Mendix World 2021 |召集您的应用开发团队 2021 年 9 月 7 日至 9 日

### 好像你需要说服…在一个全球制造商社区，他们想通过探索什么来相互学习…

bit.ly](https://bit.ly/MXW21) [](https://www.mendix.com/mendix-world/tracks/) [## 曲目|门迪克斯世界 2021

### 在今年 Mendix World 开幕之前，手工制作您的议程。浏览专为您量身定制的 8 个专题讲座中的 85 个以上专题讲座…

www.mendix.com](https://www.mendix.com/mendix-world/tracks/) 

*来自出版商-*

*如果你喜欢这篇文章，你可以在我们的* [*媒体页面*](https://medium.com/mendix) *或我们自己的* [*社区博客网站*](https://developers.mendix.com/community-blog/) *找到更多类似的文章。*

*希望入门的创客，可以注册一个* [*免费账号*](https://signup.mendix.com/link/signup/?source=direct) *，通过我们的* [*学苑*](https://academy.mendix.com/link/home) *即时获取学习。*

有兴趣更多地参与我们的社区吗？你可以加入我们的 [*Slack 社区频道*](https://join.slack.com/t/mendixcommunity/shared_invite/zt-hwhwkcxu-~59ywyjqHlUHXmrw5heqpQ) *或者想更多参与的人，看看加入我们的*[*Meet ups*](https://developers.mendix.com/meetups/#meetupsNearYou)*。*