# 超级代表:更好的应用程序代表

> 原文：<https://medium.com/square-corner-blog/superdelegate-the-better-app-delegate-93a5b704b231?source=collection_archive---------4----------------------->

一个 Swift 框架，为所有 iOS SDKs 提供一致且无错误的应用程序委托 API。

> 注意，我们已经行动了！如果您想继续了解 Square 的最新技术内容，请访问我们的新家[https://developer.squareup.com/blog](https://developer.squareup.com/blog)

*写的* [写的*丹费德曼*](https://medium.com/u/5755ab427632?source=post_page-----93a5b704b231--------------------------------) *。*

# 我们值得更好的

如果您编写了一个 iOS 应用程序，那么您已经实现了一个 UIApplicationDelegate。UIApplicationDelegate 是我们的应用程序如何与 iOS 交互的核心，但 API 非常特殊。当由于通过 3D Touch 的快速动作而启动应用程序时，iOS 将通知应用程序在*应用程序(_:*FinishLaunching:)* 方法和*应用程序(_:performActionForShortcutItem:completion handler:)*中，除非应用程序通过返回 *false* 向 iOS 发出信号，表明它处理了*应用程序(_:willFinishLaunching:)* 中的快捷方式。处理 URL(以及推送通知和用户活动项)甚至更糟。当一个应用程序用 NSURL 启动时，它将在*应用程序(_:*FinishLaunching:)* 方法和*应用程序(_:openURL:options:)* 方法中被通知，并且没有办法阻止*应用程序(_:openURL:options:)* 被调用，即使 URL 在*应用程序(_:*FinishLaunching:)* 期间被处理。

此外，UIApplicationDelegate 协议没有回答一些关键问题。想知道客户是否点击了推送通知，或者通知是否由于内容可用标志而被发送？或者调用*registerUserNotificationSettings(_:)*是否会在不提示客户访问的情况下静默工作？每个应用程序都需要为这些问题设计自己的解决方案。

# 打破生命周期变化

更糟糕的是，每个 iOS 版本和 SDK 都可能将未记录的重大更改引入应用程序生命周期。在 iOS 8.4 上，*应用(_:handleWatchKitExtensionRequest:reply:)*在*应用(_:didFinishLaunching:)* 之前被调用，但在 iOS 8.0–8.3 上不是这样！如果一个应用程序试图在后台加载 UI，iOS 8.0–8.2 将会崩溃，除非该应用程序强制在*应用程序(_:didFinishLaunching:)* 内创建一个*sharedicontext*。这样的例子不胜枚举。

每次苹果在应用程序委托合同中引入一个新的怪癖，app store 中的每个应用程序都必须找到这个怪癖并解决它，以确保他们的客户不会受到负面影响。

# 让我们一起努力吧

让我们创建一个 UIApplicationDelegate 部落知识的中央存储库，而不是让每个开发人员都辛苦地学习这些怪癖。输入[超级代表](https://github.com/square/SuperDelegate/)。SuperDelegate 采用了我们在 Square 学到的关于 UIApplicationDelegate 的所有东西，并把它变成了一个干净的应用程序委托 API。SuperDelegate 在 iOS 8 和 9 的所有版本中都是稳定的，并为您处理样板应用程序委托代码。它完全是用 Swift 编写的，我们今天对它进行开源。

SuperDelegate 确保唯一的通知、URL、用户活动项目和快捷方式只被发送到一个应用程序一次。当一个应用第一次注册用户通知后，SuperDelegate 会自动在*应用(_:willetenter foreground:)*上注册用户通知，这样一个应用总是知道推送通知权限的当前状态。当应用程序收到推送通知时，SuperDelegate 会告诉应用程序客户是否点击了它。SuperDelegate 保证它将在转发任何其他 app delegate API 调用之前调用 *setupApplication()* 和*load interface(launch item:)*。采用这种干净、没有意外的 API 使得实现新的 iOS 功能变得更加容易和快速，并且将修复您现有实现中您甚至不知道的错误。

我们正在积极努力采用 Swift 3.0 和 iOS 10 SDK，而且我们是公开进行的。随着我们在 iOS 10 API 中发现新的古怪之处，我们将提出问题并更新我们的 [develop/v0.9](https://github.com/square/SuperDelegate/pull/2) 分支。我们欢迎您继续关注，并鼓励您让我们知道您在 API 中发现了什么！

[](/@dfed) [## 丹·费德曼

### 请关注丹·费德曼在 Medium 上的最新活动。57 个人在关注丹·费德曼，看看他们的故事…

medium.com](/@dfed)