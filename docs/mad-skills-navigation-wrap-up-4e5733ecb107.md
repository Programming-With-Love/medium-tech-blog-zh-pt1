# MAD 技能导航:总结

> 原文：<https://medium.com/androiddevelopers/mad-skills-navigation-wrap-up-4e5733ecb107?source=collection_archive---------0----------------------->

![](img/b4649826decc660306b8105dcb0bacc7.png)

## 一个系列结束了，还有很多要做！

# 这是一个总结！

我们刚刚完成了关于现代 Android 开发的 [MAD Skills 系列视频和文章](https://developer.android.com/series/mad-skills)的第一个系列。这一次，主题是导航组件，即帮助您在应用程序中创建和编辑导航路径的 API 和工具。

视频和文章的伟大之处在于，与行为艺术不同，它们往往会留下来供以后欣赏。所以，如果你还没有机会看到这些，看看下面的链接，看看我们涵盖了什么。除了结尾的问答部分，每一集在视频和文章版本中都有基本相同的内容，所以使用您喜欢的任何格式进行内容消费。

# 第 1 集:概述

第一集提供了导航组件的快速、高层次的概述，包括如何创建一个具有导航功能的新应用程序(使用 Android Studio 的便捷应用程序模板)，详细介绍了支持导航的 UI 的包含层次结构，并解释了导航组件工作中涉及的一些主要 API 和部分。

或者以文章形式:

[](/androiddevelopers/navigation-component-an-overview-4697a208c2b5) [## 导航组件:概述

### 其中我介绍了导航组件工具和 API 的一些基本概念

medium.com](/androiddevelopers/navigation-component-an-overview-4697a208c2b5) 

# 第 2 集:对话目的地

第 2 集探讨了如何使用 API 导航到对话目的地。大多数导航发生在不同的片段目的地之间，这些目的地在 UI 中的 NavHostFragment 对象内部交换。但是也可以导航到外部目的地，包括位于 NavHostFragment 之外的对话框。

或者以文章形式:

[](/androiddevelopers/navigation-component-dialog-destinations-bfeb8b022759) [## 导航组件:对话目的地

### 导航组件不限于 NavHostFragment 内部的目的地

medium.com](/androiddevelopers/navigation-component-dialog-destinations-bfeb8b022759) 

# 第三集:安全

这一集将介绍 SafeArgs，它是导航组件为在目的地之间轻松传递数据而提供的工具。

或者以文章形式:

[](/androiddevelopers/navigating-with-safeargs-bf26c17b1269) [## 使用 SafeArgs 导航

### 我可能不会反驳你。但如果你这样做，我会用 SafeArgs。

medium.com](/androiddevelopers/navigating-with-safeargs-bf26c17b1269) 

# 第 4 集:深层链接

这一集是关于深度链接的，深度链接是导航组件提供的工具，用于帮助用户从应用程序外部的 UI 进入应用程序的更深层次。

或者以文章形式:

[](/androiddevelopers/navigating-with-deep-links-910a4a6588c) [## 使用深层链接导航

### 关于深层链接的深层思考…带有导航组件

medium.com](/androiddevelopers/navigating-with-deep-links-910a4a6588c) 

# 第五集:现场问答

最后，为了结束这个系列(正如我们计划为未来系列所做的那样)，我主持了一个与@Ian Lake 的问答环节。Ian 在 Twitter 和 YouTube 上回答了你的问题，我们讨论了所有的问题，从功能请求到多种背景支持(剧透:正在进行中！)到对 Jetpack Compose 的导航支持(剧透:这个的第一个版本刚刚发布！)人们关于导航、片段、Up-vs-Back、保存状态和其他主题的其他问题。这很有趣——更像是一个带摄像头的播客，而不是问答。

(这个没有文章；欣赏上面的视频)

# 示例应用程序:DonutTracker

上面大部分剧集使用的应用程序是 DonutTracker，你可以用它来跟踪你喜欢(或不喜欢)的甜甜圈的重要数据。或者你可以用它来检查这些导航功能的实现细节；你的选择。

[](https://github.com/android/architecture-components-samples/tree/main/MADSkillsNavigationSample) [## Android/架构-组件-示例

### 此示例显示了 MAD 技能系列中导航剧集突出显示的导航组件的功能…

github.com](https://github.com/android/architecture-components-samples/tree/main/MADSkillsNavigationSample)