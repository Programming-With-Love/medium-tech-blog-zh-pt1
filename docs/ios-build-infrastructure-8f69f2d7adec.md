# iOS 构建基础设施

> 原文：<https://medium.com/square-corner-blog/ios-build-infrastructure-8f69f2d7adec?source=collection_archive---------0----------------------->

## 我们如何配置我们的 Mac minis 来运行构建

*由* [撰写*迈克尔*](https://medium.com/u/22c9903aa9f1?source=post_page-----8f69f2d7adec--------------------------------) *。*

> 注意，我们已经行动了！如果您想继续了解 Square 的最新技术内容，请访问我们的新家[https://developer.squareup.com/blog](https://developer.squareup.com/blog)

Square 有几十个 iOS 工程师在做 [Square 寄存器](https://squareup.com/register)。此外，Square 向 App Store 发布了多个 iOS 应用，并在内部分发了几个 iOS 应用。为了支持我们的应用程序员的工作，我们投资了一个持续集成和测试集群。

我们最近更换了该集群的后端，以便它可以扩展到超过 8 台机器。作为这一承诺的直接结果，在过去的一年中，我们增加了构建的[可靠性](https://corner.squareup.com/2015/06/build-stability.html),并且对于每个工程师对注册库的贡献，我们能够运行的测试数量几乎翻了两番。这对我们的特性团队的速度产生了显著的影响，并在我们发布代码之前通知了我们许多重大的倒退。

当谈到持续集成时，iOS 应用程序有点像特殊的雪花。这篇文章是关于我们为支持 iOS 团队所做的后端基础设施选择。

# 詹金斯

我们使用 [Jenkins](http://jenkins-ci.org/) ，以及一个自定义插件 [Stash](https://www.atlassian.com/software/stash) ，形成我们 CI 系统的主干。这个插件类似于 Palantir 的 Stashbot 当各种各样的 git 推送发生时，它充当 Jenkins 的通知器。我们构建集群中的构建奴隶使用 [swarm](https://wiki.jenkins-ci.org/display/JENKINS/Swarm+Plugin) 插件连接到 Jenkins master。所有构建都在从属机器上运行。主 Jenkins 实例负责在内部为 Jenkins web 接口提供服务，并将工件和日志移入和移出构建奴隶。

# 五金器具

iOS 应用程序必须建立在 OSX 之上。由于虚拟化构建基础架构的诸多优势，我们曾短暂考虑过使用它；然而在实践中，我们发现它对我们来说缺乏性能。构建通常需要对小文件进行多次读写。这给调度程序和文件系统驱动程序带来了相当大的压力。对于虚拟化系统，有两个调度程序和两个磁盘驱动程序。在 Linux 上，这些系统可以很好地协同工作，但是我们发现在 OSX 上并非如此。

经过一些测试后，我们决定采用以下裸机配置，目前每个构建奴隶的价格约为 1200 美元:

*   2.6Ghz 双核英特尔酷睿 i5
*   16GB 内存
*   256GB PCIe 存储

我们还购买了[假的 hdmi 显示器](http://www.amazon.com/gp/product/B00FLZXGJ6/ref=as_li_qf_sp_asin_il_tl)，这样使用 iOS 模拟器的测试将使用显卡，而不是软件渲染。这种优化为我们的测试提供了 10%的加速。

我们希望仍然有可能从苹果商店购买 2012 年的四核配置。目前，只有双核配置必须作为新配置购买。我们的工作负载是高度可并行化的，希望在迷你外形中拥有更多计算单元。

# 结构管理

为了从机器中获得可靠且可重复的构建结果，我们希望确保它们的配置是相同的。我们使用 [DeployStudio](http://www.deploystudio.com/Home.html) 通过网络镜像新的 Mac minis，然后用 [Ansible](http://www.ansible.com/home) 做最后的设置。Ansible 允许我们拥有一个机器配置的检入记录，以及在任何给定时间我们有哪些机器在使用。当我们测试新的配置或者试图理解发生在构建奴隶上的问题时，这是非常宝贵的。

对 ansible repo 的更改也要经过代码审查和测试。即使对于复杂的剧本，我们也坚持将它们写成等幂的片段，这样我们可以针对整个集群快速运行整个剧本，而不会导致任何更改。

# 软件

我们发现 10.10.4 Yosemite 是 OSX Xcode 6 及其模拟器最稳定的版本。在撰写本文时，我们正在我们的集群上使用 Xcode 6.1.1，并且正在逐步升级 Xcode 6 . 3 . 2——同时测试可靠性。

除了安装 Xcode、OSX 和 Xcode 命令行工具之外，还有许多系统设置调整，使 Xcode 和 iOS 模拟器在命令行上比开箱即用更可靠。我们希望能够从 OSX 和 Xcode 获得可靠的行为，这些配置调整已经帮助我们做到了这一点。

# 登录和图形用户界面

我们已经配置了所有的构建奴隶来自动登录构建用户。这为我们提供了运行 iOS 模拟器的 GUI 上下文，意味着我们可以将 Jenkins 代理作为用户启动代理来启动。这允许 Jenkins 启动的任何进程访问该 gui 上下文。远程运行 iOS 模拟器的许多问题都源于缺乏通常的 OSX 登录中的 API 设施。

我们通过在 Jenkins 从代理上运行[咖啡因](https://developer.apple.com/library/mac/documentation/Darwin/Reference/ManPages/man8/caffeinate.8.html)来增强这一功能，这样 mini 就永远不会休眠。OSX 上有很多睡眠设置，每个版本都会添加新的设置。我们发现使用咖啡因来设置 power assert 是防止构建奴隶进入睡眠状态的最可靠的方法。

构建用户被自动记录意味着构建用户的密码[在文件系统上很容易被发现](http://apple.stackexchange.com/a/50680)。对于创建发布二进制文件的构建奴隶，我们禁用带密码的 SSH 访问，并完全关闭 VNC。公司中受信任的小组可以访问代码签名机来部署我们的代码签名证书，但是不允许其他远程访问。构建用户对从机本身有 sudo 访问权，我们打开 [DevToolsSecurity](https://developer.apple.com/library/mac/documentation/Darwin/Reference/ManPages/man8/DevToolsSecurity.8.html) ，这样 Xcode 就可以正常工作了。

# OS X 无障碍通道

Xcodebuild 使用 OSX 的可访问性访问挂钩来控制 iOS 模拟器。在 Mavericks (10.9)中，OSX 访问这些钩子的方式已经获得了应用程序级的粒度。以前，它是一个系统级参数，允许所有进程相互访问。如果访问未被授权，可能会导致令人抓狂的问题，xcodebuild 在从 Terminal.app 运行时会完美运行，但在从 Jenkins 或 SSH 启动时会莫名其妙地失败。

Mavericks (10.9)和 Yosemite (10.10)通过访问进程的父进程来决定一个进程是否可以访问可访问性钩子。通过将 [launchd](https://en.wikipedia.org/wiki/Launchd) 放入允许的进程列表中，通过 SSH 或 Jenkins 启动的进程可以访问整个系统的可访问性挂钩。为此，您可以根据本[要点](https://gist.github.com/mtauraso/b4839ee262ab6f612805)修改 TCC 数据库。需要重新启动才能使更改生效。

# 连接到詹金斯

如前所述，我们使用 [Jenkins Swarm 插件](https://wiki.jenkins-ci.org/display/JENKINS/Swarm+Plugin)来配置我们的从机。这与正常的 Jenkins 用法相反，因为每个从节点都连接到主节点，而不是从 Jenkins 配置进行管理。这使我们在添加或删除从节点，或者更改它们的 IP 或 DNS 名称时，不必维护主节点上的配置。

swarm 插件中需要注意的主要问题是 Jenkins remoting 组件中的不匹配。这通常发生在 Jenkins 升级期间，需要向奴隶推送一个新的虫罐。因为未能连接到 Jenkins 的从机不会出现在列表中，所以建议从外部监控机器以确保它们保持连接。

# 当前工作

我们为集群保持了高标准的[构建稳定性](https://corner.squareup.com/2015/06/build-stability.html)，目前我们运营着 24 台 Mac minis。维护集群的某些方面还不太顺利。

Xcode 升级还是有点工作量的。很难预测新版本的稳定性。在大多数情况下，点版本号越高，Xcode 就越可靠。我们经常发现自己归档雷达，并为新升级的行为寻找解决办法。当 Xcode 6.1 是新版本时，我们决定在给定的机器上只有单一的 Xcode 配置(因为测试版与旧版本的 Xcode 互操作时有一些错误)。这种支持已经变得更好了，我们正在改变我们的配置，以允许在一个构建奴隶上有多个版本的 Xcode。随着时间的推移，在单个主机上支持多个 iOS 模拟设备也变得越来越容易，我们也在努力使我们的安装不透气。

我们希望这些信息对为 iOS 构建持续集成系统的人有用。随着它的不断变化，我们将根据我们当前的配置实践更新这个角落。我们还对您使用苹果软件构建平台的经历感兴趣。在这个设置过程中，Stackoverflow 问题和博客帖子是我们最好的老师，我们非常感谢花时间来教育我们的每一个人。此外，如果你对构建和发布工具感兴趣，并对苹果平台充满热情，给我们写封短信，[我们正在招聘](http://hire.jobvite.com/CompanyJobs/Careers.aspx?c=q8Z9VfwV&page=Jobs)。

[](/@mtauraso) [## 迈克尔·陶拉索-简介

### 请关注迈克尔·陶拉索的最新活动-在媒体上发布个人资料。96 人正在关注迈克尔·陶拉索的个人资料…

medium.com](/@mtauraso)