# AEM 内容同步

> 原文：<https://medium.com/globant/aem-content-synchronization-cb1e15b0c3f8?source=collection_archive---------1----------------------->

![](img/bc34b1339c640ee079f728fe522998c8.png)

## **了解如何在 AEM 实例之间移动内容和资产。**

O 我们在 AEM 工作中面临的最常见挑战之一是，当组织往往被海量数据淹没时，环境之间的内容迁移通常很难管理。

为了克服这个问题，作者、编辑和内容管理者可以使用一些工具在 AEM 实例之间移动内容和资产。根据业务和技术需求评估每个解决方案的“优点”和“缺点”非常重要。

接下来的部分包含了我们使用过的三个最重要的工具。

## **包装经理**

包管理器是一个 AEM 开箱即用的工具，允许用户进行各种操作，包括配置、构建、下载和安装内容包。包是一个 zip 文件，以文件系统序列化(称为“保险库”序列化)的形式保存存储库内容。这为文件和文件夹提供了一种易于使用和编辑的表示方式。

Package Manager 是安装新功能、在不同实例和环境之间传输内容的理想选择，也常用于备份存储库内容。

> **优点**

*   作者可以执行包构建和安装步骤。
*   在即将安装软件包之前，会创建一个快照软件包来包含将被覆盖的内容。如果/当您卸载软件包时，将会重新安装此快照。

> **缺点**

*   AEM 版本历史记录丢失
*   可以覆盖或删除现有内容。
*   通常在传输数据量很小时使用。
*   如果您正在安装数字资产，您必须:首先，停用 WorkflowLauncher。接下来，当安装完成时，重新激活 WorkflowLauncher。

## **VLT RCP**

vault RCP 提供了非常简单的 vault 远程复制任务管理，可以通过 JSON/HTTP 界面进行控制。 **vlt rcp** 只能用于**从远程存储库导入**内容。如果您试图移动数 GB 或数 TB 的数字资产，这一功能尤其有用。

Vault RCP 允许您使用 **vlt rcp** 跨网络指定源和目标目录。所有存储库数据将从一个实例下载到另一个实例。没有必要在防火墙上打开任何端口—所有流量都是 HTTP(S)。下面是 vlt rcp 命令的基本结构:

*。/vlt RCP-b 100-r-u-n { protocol }://用户名}:{ password } @ { AEM-source-hostname }:{ port }/crx/-/JCR:root/{ path } { protocol }://{ username }:{ password } @ { AEM-target-hostname }:{ port }/crx/-/JCR:root/{ path }*

从本地作者复制内容以进行发布的示例命令

*。/vlt RCP-b 100-r-u-n*[*http://admin:admin @ localhost:4502/crx/-/JCR:root/content/dam/geometrixx*](http://admin:admin@localhost:4502/crx/-/jcr:root/content/dam/geometrixx)*[*http://admin:admin @ localhost:4503/crx/-/JCR:root/content/dam/geometrixx-test*](http://admin:admin@localhost:4503/crx/-/jcr:root/content/dam/geometrixx-test)*

*运行该命令之前，请务必关闭格式副本工作流程。我们可以手动运行它或进行一些自动化。Adobe Performance Architect Gardner Buchanan 的测试表明，默认的批处理大小 1000 应该减少到 100 以获得更好的吞吐量，还建议针对单独的源树结构运行多个 **vlt rcp** 实例以并行化整个操作。*

> ***优点***

*   *VLT 是由 Adobe 维护的 Adobe 产品。*
*   *修订历史被保留。*
*   *AEM 源或目标服务器上不需要安装。*
*   *比包管理器快，不像包管理器那样消耗包空间。*

> ***缺点***

*   *运行 **vlt rcp** 命令前后启用和禁用的工作流程。*
*   *每个节点上的 HTTP 请求/响应以及网络上的延迟极大地损害了性能。*

## *格雷比特*

*名称 *Grabbit* 指的是从一个 CQ/AEM 实例获取内容并将其复制到另一个实例。然而，它也指“Jackrabbit”，即内容被复制到的引用 JCR 实现。*

*Grabbit 的目标是使 AEM 实例之间的内容同步更容易、更快。Grabbit 通过 HTTP(s)从 Grabbit 服务器(源)到 Grabbit 客户端(目的地)创建一个**数据流**。对于序列化，Grabbit 使用谷歌的协议缓冲区。协议缓冲区是一种极其高效的(就 CPU、内存和线路尺寸而言)二进制协议，包括压缩。*

*Grabbit 需要安装在两个 AEM 实例上。您通过向客户端提供配置文件来发起将内容从 Grabbit 服务器复制到 Grabbit 客户端的请求，配置可以以 YAML 和 JSON 格式开发。对于配置文件中需要同步的每个路径，在 Grabbit 客户端上会创建一个新的 Grabbit 作业。*

*Grabbit 还允许您监控客户端上的作业。监控 API 提供基本信息，如作业开始时间/结束时间、当前路径、写入的节点数量等。*

> ***优点***

*   *更少的 CPU/内存需求。*
*   *连续流以避免延迟问题。*
*   *比金库 RCP 快。*
*   *不需要手动启用/禁用工作流。*
*   *支持同步用户和组。*

> ***缺点***

*   *Adobe 不支持。*
*   *设置需要时间，故障排除可能会很困难。*
*   *源路径和目标路径相同，即复制时不能改变目标路径。*

## ***结论***

*根据上面列出的工具的优缺点，没有最好的内容同步选项，这完全取决于您的项目状态、实例大小和需求。如果你想移动一小块内容或者一个小的资产列表，Package Manager 是解决这个问题最简单的方法。但是，如果您尝试移动 GB 或 TB 的内容和资产，那么 Vault RCP 或 Grabbit 是最佳选择。有时，我们没有足够的权限在不同的实例上安装或进行故障排除。如果是这种情况，那么 Vault RCP 可能是最好的选择，因为只要没有任何防火墙规则阻止对源服务器和目标服务器的访问，您就可以从本地运行它。如果不是这样，那么 Grabbit 是更快同步的选项。*