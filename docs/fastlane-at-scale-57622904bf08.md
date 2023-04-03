# 大规模自动化移动交付

> 原文：<https://medium.com/capital-one-tech/fastlane-at-scale-57622904bf08?source=collection_archive---------5----------------------->

## 浪子的最佳实践

![](img/edf01cbacb23634af1cc3f2343e6c9dd.png)

我大规模使用*浪子的旅程始于为一个团队开发的 iOS 应用程序构建一个全自动的连续交付系统。这发展成了两个移动应用；由多个团队开发的一个 Android 应用程序和一个 iOS 应用程序。自然，这些团队使用不同的工具集、不同的测试环境和不同的应用程序配置。*

带着这个头衔，我被要求在两个平台上的许多应用上做同样的事情，包括许多开发团队、许多环境和许多外部资源。我提到“很多”了吗？

自动化几十个团队中数百名开发人员为几十个移动应用程序所做的工作非常困难。团队有不同的开发方法、架构、工具、发布节奏和门控需求。实现这些折衷的交付目标需要去除手动过程，增加自动化的可靠性，并使团队能够灵活地定义和支持他们不同的过程。

首先，让我们奠定浪子基础，然后深入研究解决这些不同目标的实现细节。

> 你可以在 GitHub 上找到完整的[代码样本。](https://github.com/jaleksynas/FastlaneAtScaleExamples)

*访问* [*从扩展 iOS CI/CD*](/capital-one-tech/4-lessons-learned-from-scaling-ios-ci-cd-60c2f0cd7c94) *中吸取的 4 个教训，了解这一旅程的其他方面，包括构建一个可扩展的 iOS 执行环境以及我们在此过程中面临的三个挑战(环境不一致、容量有限以及 CI/CD 不灵活)。*

# 浪子 101-基础

浪子是一个用 Ruby 实现的非常活跃的开源项目，目前由 Google 赞助。项目维护人员会对社区提出的问题做出快速反应，并热烈欢迎外界的贡献。浪子为定义 iOS 和 Android 应用程序的自动化流程提供了一个统一的框架。它甚至可以更好地将过程定义和利用这些过程的项目一起转移到版本控制中。

![](img/06e4758f9bfe6e0fc08dd86d32929e0d.png)

*“浪子是为您的 iOS 和 Android 应用程序自动化测试版部署和发布的最简单方式。🚀它处理所有繁琐的任务，如生成屏幕截图、处理代码签名和发布您的应用程序。”* [*—浪子文献*](https://docs.fastlane.tools/)

# 浪子术语

*   ***项目*** :利用浪子的代码库。
*   ***Fastfile*** :包含使用浪子领域特定语言(DSL)的项目编码过程的文件。
*   ***动作*** :快速文件([文档](https://docs.fastlane.tools/actions/#fastlane-actions))内使用的单个可配置操作。
*   ***lane*** :构成流程的动作集合。车道可以调用其他车道。
*   ***private_lane*** :只能被其他车道调用的车道，不能直接在命令行调用([文档](https://docs.fastlane.tools/advanced/lanes/#private-lanes))。
*   ***函数*** :封装了逻辑的 Ruby 函数，可以在单个 Fastfile 中重用，无需改变通道。
*   ***插件*** :一个红宝石宝石封装了额外的动作，不是开箱即用的。

Examples of the various concepts.

# 自动化可靠性

大多数过程沿着非常相似的轨迹发展。一开始，个人开发者有责任构建和发布移动应用。开发人员访问 Apple 和 Google web 控制台，更新配置，管理代码签名资产，以及管理发布工件和过程。这些手动过程可能对未提交的代码进行操作，由不同的人执行不同的步骤，需要在 IDE 中进行特殊的更改，并且需要在每个 web 控制台中进行不必要的提升访问。

稳定和扩展的首要目标应该是消除所有基于 web 的手动操作，集中管理共享资源([挑战#1](/capital-one-tech/4-lessons-learned-from-scaling-ios-ci-cd-60c2f0cd7c94) —环境不一致)。浪子提供了这些现成的功能，所以它是一个自然的选择。每个应用程序只需要在它们的 Fastfile 中添加几行代码，就可以统一应用程序开发人员用来配置、构建、发布和管理二进制文件的步骤。这个过程很容易转移到自动化的命令执行程序，将开发人员和他们的本地工作副本一起从构建和发布过程中移除。

考虑童年积木；有很多方法可以使用相同的积木来建造一个有效的房子。对建筑者来说，砖块的颜色纯粹是审美的。自动化的过程可以被认为是类似的；整个过程(房子)需要知道很少关于各种动作(砖块)的特定设置(颜色)，而是应该关注如何组织这些动作以达到期望的结果。动作的详细配置可能会因执行的不同而不同，但是流程的流程保持不变。

## **例如:**

这是一个在开发期间和发布期间执行的通用简化自动化流程。在同一流程的两次执行之间，操作的细节会发生显著变化。

![](img/7803c7fa42d5d195763eb098b821dec2.png)

## **开发期间:**

*   *构建*:使用**调试**构建设置
*   *二进制*:用**开发**证书签名
*   *部署*:测试人员**内部分配**的位置
*   *报告*:向**开发**聊天频道发送消息

## **发布期间:**

*   *构建*:使用**发布**构建设置
*   *二进制*:发布就绪，已签署**生产**证书
*   *部署*:一个**生产型分销**渠道
*   *报告*:向**发布**聊天频道发送消息

# 配置方法(TL；dr:使用 dotenv 和 lane_context。)

为了在规模上保持清晰，必须保持流程和流程配置的解耦。这也通过根据持续集成的租户尽可能频繁地测试过程本身来强化过程。浪子提供了几种动作配置方法；直接动作配置、特殊配置文件和“dotenv”。我将在下面逐一介绍。*TL；dr:使用 dotenv 和 lane_context。*

## **直接动作配置**

直接在 Fastfile 内的动作调用的参数上设置配置值实际上是将这些值硬编码为一个值且仅一个值。Ruby 语言可以直接在 Fastfile 中使用，以提供变化。然而，这很快就变成了复杂的代码，而不是简单的配置。

*参见 GitHub 上的* [*代码示例*](https://github.com/jaleksynas/FastlaneAtScaleExamples/blob/master/2_configuration_approaches/1_direct_action/fastlane/Fastfile) *。*

## **特殊配置文件:Appfile、Deliverfile、Scanfile**

为了更灵活一点，浪子提供了一系列的[配置“文件”](https://docs.fastlane.tools/advanced/Appfile/)，为主要的内置动作的一些设置提供一些配置。当您的流程不复杂时，这些文件是很好的。然而，随着过程的增长，这些文件很快就会失去效用。

这些文件不支持所有的设置，并且不能配置大量的动作和插件。最终，将需要多种配置方法，导致配置状态的清晰度降低。

*参见 GitHub* 上的 [*代码示例*](https://github.com/jaleksynas/FastlaneAtScaleExamples/tree/master/2_configuration_approaches/2_appfile/fastlane)

## ***dotenv***

*为了获得最大的灵活性，请使用基于环境变量的配置和相应的 dotenv 文件来实现配置变化。您也可以在调用浪子之前在 shell 中设置这些值。但是，dotenv 文件允许对配置进行正确的版本控制。要使用基于环境的配置文件，请使用附加参数执行浪子:`fastlane my_lane --env release`。即使没有附加的参数，您也可以使用默认的`.env`文件来存储您的配置值。*

**不需要引用通过 Fastfile 中的环境变量配置的变量，动作会自动选取它们！另外，请注意，任何直接在动作上配置的值都将取代所有漂亮的 dotenv 值。**

*复杂的数据类型，如数组或映射，使用环境变量会很棘手。避免开发需要复杂输入的操作，或者实现一个基本的解析器来保持配置的可管理性。在开箱即用的动作和现有的插件中，只有几个这样的实例，但是值得一提。*

**参见 GitHub 上的* [*代码示例*](https://github.com/jaleksynas/FastlaneAtScaleExamples/tree/master/2_configuration_approaches/3_dotenv/fastlane) *。**

## ***配置值的 dotenv 层级***

*首先要注意的是，默认的`.env`文件是*总是*加载的，然后根据命令行上指定的内容加载其他文件。*

*当没有在命令行上传递任何要加载的特定环境文件时(例如`fastlane my_lane`，同名的系统环境变量优先于默认值。env 文件根据 [dotenv 规则关于变量加载顺序](https://github.com/bkeepers/dotenv#why-is-it-not-overriding-existing-env-variables)。当您希望执行环境控制或更改某些配置时，这很方便。*

*当在命令行上传递特定的环境时，浪子通过使用`Dotenv.overload`稍微改变了规则。和以前一样，同名的系统环境变量优先于默认值。环境文件。然后是最后加载的。env 文件优先于所有其他文件。*

*尽管这些规则有细微差别，但它们是有限的。其他配置模型可以有各种各样的编程机制。一旦适当的可扩展配置模型就位，团队就可以开始构建和控制他们自己的过程。*

**参见 GitHub 上的* [*代码示例*](https://github.com/jaleksynas/FastlaneAtScaleExamples/tree/master/2_configuration_approaches/4_dotenv_hierarchy/fastlane) *。**

# *用浪子插件拥抱创新*

*拥有大量开发团队和项目的大型组织通常需要非常相似的特性(例如，安全性扫描、代码质量扫描、工件交付等)用于他们各自的自动化过程。此外，允许团队在构建他们的过程中掌握控制权会导致组织中不同但相似的浪子实现。*

*团队使用其他项目的副本创建新项目，并使用复制和粘贴添加功能。这些常见的开发实践导致了各种各样的困难，例如使项目与最佳实践保持同步，补救严重的失败，以及保持一致的支持，等等。无法发现由不同团队开发的可用特性也阻碍了特性的采用。*

*幸运的是，浪子已经做好了准备！浪子在 2016 年下半年添加了一个强大的插件架构，社区的回应是向 214 个开箱即用的快车道行动添加 350 个插件(大约 2019 年 2 月 1 日)。使用浪子插件进一步开发功能有助于缓解成长的烦恼。如果新功能非常具体，和/或一个功能还没有在[开源浪子插件库](https://docs.fastlane.tools/plugins/available-plugins/)中创建；该功能需要开发。为浪子插件采取一个进化的开发生命周期导致了最好的结果。*

# *浪子插件开发生命周期*

*首先，需要该功能的开发人员将特性*直接添加到 fast file*中，以获得最快的反馈周期。随着特性变得复杂并依赖于其他库或更详细的配置，这是相当有限的。*

*Develop features directly in the Fastfile*

*随着特性开发的继续，代码转移到项目的`fastlane`文件夹中的`actions`和`helpers`文件夹。在这里，动作的代码被转换成实际的动作类，输入和输出被布局和固化。在这个阶段，代码继续与应用程序一起交付，并作为正式动作从 Fastfile 中调用。在原始代码库之外共享这个特性实际上是不可能的。然而，代码现在被正确地格式化成一个插件，并通过 gems 交付动作，与其他用户共享。*

**参见 GitHub* 上的 [*代码示例*](https://github.com/jaleksynas/FastlaneAtScaleExamples/tree/master/3_plugin_development/2_as_action/fastlane)*

**随着与其他代码库共享操作变得越来越理想，使用浪子 [new_plugin](https://docs.fastlane.tools/plugins/create-plugin/#create-your-own-fastlane-plugin) 命令最终确定名称和元数据文件。然后，动作和辅助文件移出项目，进入锅炉板结构。开发者遵循文档[中的剩余步骤](https://docs.fastlane.tools/plugins/create-plugin/)来完成到可发布的 gem 的转换。**

***参见 GitHub* 上的 [*代码示例*](https://github.com/jaleksynas/FastlaneAtScaleExamples/tree/master/3_plugin_development/3_as_plugin)**

***最后，开发者将发布的 gem 集成到任何项目中(包括原始项目),就像任何其他浪子插件一样。***

***当开发不开源的插件时，确保它们被开发在一个容易被发现的地方是很有价值的；一个单独的 Github 组织工作得很好。***

# ***版本漂移***

***将特性打包成可共享的插件，浪子案例中的 Ruby gems，有助于集中重复数据删除工作，并提供安全且有针对性的更新策略。有价值的功能在外部维护，并被推广以满足许多人的需求，这将不可避免地导致消费者数量的增加。消费者有责任确保他们定期更新。版本漂移的问题不是浪子独有的；产生或消耗外部库是软件开发中常见的问题。***

# ***结论***

***这篇文章中详述的技术已经被证明非常成功地允许大量的团队为大量的应用程序做出贡献，同时保持了灵活性和易支持性。将流程从命令执行器中编码和分离出来，可以在不需要进行重大重构的情况下使用各种工具进行实验。最后，致力于进一步的集中化，同时仍然保持对可扩展性和所有权的严格关注，这将为开发人员快速、轻松地自动化他们的过程创造更多的机会。***

# ***附录 A:浪子提示和技巧***

## ***记录***

***注意您的日志输出。一些代码和设计选择会导致复杂且可读性较差的日志。例如，通道可以像方法一样调用其他通道并传递变量，但通道转换包括日志记录。您也可以简单地为其编写一个 Ruby 风格的方法，这样就避免了日志记录。***

***另一个日志缺陷是使用`sh`动作调用 shell 命令。在某些情况下，相关的日志记录可能有点长。有几种方法可以更好地控制这种情况。***

****参见 GitHub 上的* [*代码示例*](https://github.com/jaleksynas/FastlaneAtScaleExamples/blob/master/a_appendix/1_logging/fastlane/Fastfile) *。****

## **`Helper.is_ci?`**

**大部分应用程序开发和浪子流程开发都是在开发人员的机器上进行的，但是有一些操作和配置在本地或 CI 环境中运行没有意义或存在安全风险。在这两个位置，通道仍应在很大程度上按预期运行，以支持故障排除和持续开发。**

**当在插件开发中或在 Fastfile 中使用时，此标志可用于阻止或允许仅在 CI 系统中运行时发生的操作。它基于各种已知环境变量的存在。详见[来源](https://github.com/fastlane/fastlane/blob/master/fastlane_core/lib/fastlane_core/helper.rb#L72-L79)。超级用户可以通过在他们的环境中设置一个值，在绝对必要的情况下以 CI 的身份在本地执行。**

**Using Helper.is_ci?**

## **密码和敏感信息**

**动作选项通常可以包含密码或 API 令牌。这些选项类型使用特殊处理来避免记录和暴露敏感信息。您可以在 dotenv 配置文件中存储*安全*令牌，如上所述。安全令牌可以包括受限/只读认证令牌和/或消息传递集成令牌。大多数 CI 系统都提供了密码存储机制，可以将值适当地公开为环境变量。也可以集成几个独立的产品。**

***参见 GitHub 上的* [*代码示例*](https://github.com/jaleksynas/FastlaneAtScaleExamples/tree/master/a_appendix/3_secrets/fastlane) *。***

## **使用车道上下文**

**动作可以用几种不同的方式返回值。首先，对它们的调用只是返回一些值，这些值可以赋给 Fastfile 中的一个变量——就像任何方法调用一样。第二，对于浪子的执行，他们可以选择在 [lane_context](https://docs.fastlane.tools/advanced/lanes/#lane-context) 中存储重要信息。这允许您使用之前操作的输出来配置其他操作。例如，当执行构建时，构建操作存储生成的二进制文件的路径。通常这个值是其他操作的默认值，比如部署。尽可能避免为配置文件设置预先设想的静态路径。允许操作使用`lane_context`或返回值通知后续操作。**

***参见 GitHub 上的* [*代码示例*](https://github.com/jaleksynas/FastlaneAtScaleExamples/tree/master/a_appendix/4_lane_context/fastlane) *。***

## **文件系统路径**

**自动化流程通常处理各种文件。在处理路径时，浪子执行可能会很棘手，请参见[https://docs . fast lane . tools/advanced/fast lane/# directory-behavior](https://docs.fastlane.tools/advanced/fastlane/#directory-behavior)了解所有详细信息。**

# **相关:**

*   **[iOS CICD 升级的 4 个教训](/capital-one-tech/4-lessons-learned-from-scaling-ios-ci-cd-60c2f0cd7c94)**
*   **[鲁比，RVM，&德勒揭秘](/capital-one-tech/ruby-rvm-and-bundler-demystified-9f3f946230f1)**

***披露声明:这些观点是作者的观点。除非本帖中另有说明，否则 Capital One 不属于所提及的任何公司，也不被其认可。使用或展示的所有商标和其他知识产权都是其各自所有者的所有权。本文为 2019 首都一。***