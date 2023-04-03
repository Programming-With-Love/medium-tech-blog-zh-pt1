# 宣布椰子-生成

> 原文：<https://medium.com/square-corner-blog/announcing-cocoapods-generate-66df4a382663?source=collection_archive---------5----------------------->

![](img/6c87dd52796468da864e6071d6d3539e.png)

> 注意，我们已经行动了！如果您想继续了解 Square 的最新技术内容，请访问我们的新家[https://developer.squareup.com/blog](https://developer.squareup.com/blog)

在 Square，我们使用 CocoaPods 作为我们 iOS 开发工作流的核心部分，除了在我们的测试套件中使用的库之外，仅在 Square 销售点应用程序中就有大量的库。许多这样的库存在于我们主要的 iOS monorepo 中，随着应用程序的增长，构建整个程序所需的时间也在增长。

CocoaPods 允许我们将所有的库配置保存在易读的 podspec 文件中，这比 Xcode 项目更容易在 pull 请求中查看。我们的项目配置很少在 Xcode 内部管理，这确保了我们在文件系统和 Xcode 中看到的内容之间的一致性。

然而，将所有这些模块集成到一个项目中有一些缺点。虽然我们已经投入了精力来提高安装依赖项的整体性能，但是运行单个 pod install 命令可能需要一两分钟的时间，当快速迭代单个库时，这个时间会增加。使用 CocoaPods 还会生成一个非常大的 Pods 项目，这可能会导致 Xcode 响应速度变慢，并且需要很长时间进行索引。

随着这种爆炸式的增长，很明显我们需要一种更好的方式让开发人员工作。我们希望能够独立地处理模块，只构建正在处理的模块，只运行附带的测试套件。

输入 [pod gen](https://github.com/square/cocoapods-generate) 。

我们构建了一个 CocoaPods 插件，它允许 iOS 开发人员轻松地在独立的库上工作，特别是在 monorepo 的上下文中。运行此插件将生成一个存根应用程序项目，该项目集成并链接一个指定的库及其依赖项。我们的 full Pods 项目需要大约 95 秒来生成，并且是 356，000 行。如果我们在我们的 UI 组件库上运行 pod gen，它需要 5 秒钟并生成一个 9 千行的项目文件。这使得迭代这些基本库变得更快，并且工具可以轻松处理较小的规模。gen 最酷的一点是，它自动从现有的 Podfile 和 Podfile.lock 中引入依赖关系限制，因此所有配置都反映在生成的库工作区中。

这不仅仅在独立开发的 monorepo 环境中有用。当与从 podspec 中描述测试的能力[结合使用时，它在独立的存储库中也工作得很好。这使得独立库可以避免签入和维护自己的 Xcode 项目，因为 CocoaPods 可以管理它。这意味着您不再需要处理 PRs 中的 Xcode 冲突，因为。xcodeproj 只是一个用于开发的生成工件。podspec 本身成为描述您的库的真实来源，从中您可以用一个简单的 pod gen 命令生成一个工作区。](https://blog.cocoapods.org/CocoaPods-1.3.0/)

测试可以简单地放在文件系统的适当文件夹中，并作为 CI 的一部分运行。例如，我们设置了这个[演示回购](https://github.com/segiddins/pod-gen-demo)，我们需要检查的唯一配置是 podspecs。我们已经在内部使用这个宝石几个月了，我们非常高兴与世界分享它！