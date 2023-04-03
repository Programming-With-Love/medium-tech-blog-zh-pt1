# Splunk 初学者教程:使用 Splunk 探索机器数据

> 原文：<https://medium.com/edureka/splunk-tutorial-3e1b5a22e6fe?source=collection_archive---------3----------------------->

![](img/07f6706cff7edb42822f1618242eee74.png)

Splunk Tutorial — Edureka

在本文中，我将介绍一款最受欢迎的分析工具 Splunk，它在大数据和运营智能领域赢得了众多青睐。它是一种横向技术，用于应用程序管理、安全性和合规性，以及业务和 Web 分析。Splunk 是一个完整的解决方案，可以帮助*搜索*、*分析*和*可视化*不同机器生成的日志。通过本 Splunk 教程，我将向您介绍 Splunk 的各个方面，并帮助您了解一切是如何结合在一起的，以便从中获得真知灼见。

但在我开始之前，让我列出我将讨论的主题:

*   Splunk 及其优势
*   体系结构
*   Splunk 定价
*   配置文件

# Splunk 简介

在开始使用 [Splunk](https://www.edureka.co/blog/what-is-splunk?utm_source=medium&utm_medium=content-link&utm_campaign=splunk-tutorial) 之前，您是否意识到非结构化数据和实时日志带来的挑战？例如，实时客户查询、不断增加的日志数量，通过这些日志，数据集的大小每分钟都在波动。如何解决所有这些问题？在这里，Splunk 来了。

Splunk 是一站式解决方案，因为它自动从各种来源提取数据，并接受任何格式的数据，如 CSV、JSON、配置文件等。此外，Splunk 是最容易安装的工具，支持搜索、分析、报告以及可视化机器数据等功能。它在 It 基础设施和业务方面有着巨大的市场。该行业的许多大公司都在使用 Splunk，如多米诺、Adobe、博世、沃达丰、可口可乐等。

![](img/6537d5e22cabe8b129b086bbd21121fe.png)

如上图所示，Splunk 有一些非常酷的优势:

*   Splunk 从多个系统实时收集数据
*   它接受任何形式的数据，例如日志文件。csv、json、config 等。
*   Splunk 可以从数据库、云和任何其他操作系统中提取数据
*   它分析并可视化数据以获得更好的性能
*   Splunk 提供警报/事件通知
*   提供实时可见性
*   它满足了行业需求，如水平可伸缩性(并行使用许多系统)

继续 Splunk 教程，让我们了解内部工作原理。

# Splunk 架构

Splunk 的架构由各种组件及其功能组成。请参考下图，该图给出了流程中涉及的组件的综合视图:

![](img/9435080513527fd6eaae95e644b65f8e.png)

如上图所示，splunk CLI/ Splunk web 界面或任何其他界面都会与搜索头进行交互。这种通信通过 Rest API 进行。然后，您可以使用 search head 进行分布式搜索，为运营智能设置知识对象，执行计划/警报，以及创建可视化报告或仪表板。您还可以运行脚本来自动将数据从远程 Splunk 转发器转发到预定义的网络端口。之后，您可以实时监控即将到来的文件，分析是否有任何异常，并相应地设置警报/提醒。您还可以在数据存储到索引器之前，对来自转发器的数据执行路由、克隆和负载平衡。您还可以创建多个用户来对索引数据执行各种操作。

# Splunk 定价

在索引数据时，首先会出现的问题是“这要花多少钱？”。这完全取决于您要索引的卷。所以，简而言之:

> **“索引数据越多，付费越少”**

Splunk 主要有两个版本:

*   在 Splunk 免费版中，您每天可以收集和索引高达 500 MB 的数据。它只能由一个用户使用，您可以在其中搜索、分析和可视化数据。
*   Splunk 企业版每月起价 225 美元。对用户没有限制，您可以扩展每天的无限数据量。您可以获得企业级支持，也可以在自己的云中进行内部部署，或者使用 Splunk 云服务。您可以执行无限制的搜索，并相应地对其进行监控。企业版有很多这样的优势。

我们有不同类型的许可证，参考下面的截图。

![](img/0319545e83adab025dddfaa8a82609fc.png)

*   **企业试用许可证**:每天可以索引 500MB，但是这个许可证的有效期只有 60 天。您已经启用了所有功能，例如警报、多用户访问、分布式搜索、集群等。现在 60 天后，它会将此许可证转换为免费许可证。
*   **免费许可**:在免费许可中，你将没有任何用户访问控制，它将只适用于一个用户(管理员)。将不会有任何用户帐户可用，集群，分布式搜索，甚至通知将被禁用。因此，它仅用于执行日志收集&日志分析。
*   **转发器许可证**:无论何时设置重型转发器，都应该在其上安装转发器许可证，这样只有 Splunk 实例会变成重型转发器。

接下来，让我们继续学习 Splunk 教程，了解配置文件。

# 配置文件

配置文件在 Splunk 环境的运行中起着非常重要的作用。这些配置文件包含 Splunk 系统设置、配置设置和应用程序配置设置。您可以编辑这些文件，相应的更改将反映在您的 Splunk 环境中。但是，只有重新启动 Splunk 实例，对配置文件所做的更改才会生效。

这些配置文件可以在以下位置找到:

*   $ SPLUNK _ HOME/etc/system/default
*   $SPLUNK_HOME/etc/system/local
*   $SPLUNK_HOME/etc/apps/

这些配置文件的存储路径在所有操作系统中都是一致的。它们总是存储在安装 SPLUNK 的目录 SPLUNK _ HOME 中。还有一个存储配置文件的路径: **$SPLUNK_HOME/etc/users** 。在此文件夹中，将存储用户界面中的用户特定设置、用户特定配置和首选项。作为管理员，您还可以存储多个 Splunk 用户的用户特定设置。

您在 UI 中看到的一切都可以通过配置文件进行配置/修改。事实上，有很多选项不能通过 UI 编辑，但是可以通过 CLI 或直接编辑配置文件来编辑。继续 splunk 教程，让我们了解并讨论这些配置文件的结构。

## **配置文件结构**

每个配置文件由一个或多个节组成。每个节都以方括号中的节头开始，后面是文件设置。每个设置都是一个属性值对，其中属性区分大小写。下面是包含多个节的配置文件的基本模式。

```
[stanza1] 
<attr1> = <value> 
<attr2> = <value>
```

**比如:**

```
[SSL] 
serverCert = <pathname> 
password = <password>
```

在这篇 Splunk 教程博客的最后一节，我将讨论 Splunk 中最常见的配置文件:

*   输入. conf
*   输出. conf
*   道具. conf
*   savedsearches.conf
*   索引. conf
*   认证. conf
*   authorize.conf

这就把我们带到了本文的结尾。我敢肯定，到现在为止，你们中的大多数人已经理解了 Splunk 的基本原理，所以你们可以开始索引数据并从中获得见解。

如果你想查看更多关于人工智能、DevOps、道德黑客等市场最热门技术的文章，你可以参考 Edureka 的官方网站。

请留意本系列中解释 Splunk 其他各方面的其他文章。

> 1.[什么是 Splunk？](/edureka/what-is-splunk-26b3cccab039)
> 
> 2. [Splunk vs. ELK vs. Sumo 逻辑](/edureka/splunk-vs-elk-vs-sumologic-58138f6cb7f)
> 
> 3. [Splunk 用例:多米诺的成功故事](/edureka/splunk-use-case-da8ac0340f13)
> 
> 4. [Splunk 架构](/edureka/splunk-architecture-c9910b34c745)
> 
> 5. [Splunk 知识对象](/edureka/splunk-events-event-types-and-tags-686244b447b5)
> 
> 6. [Splunk 查找和字段](/edureka/splunk-lookup-and-fields-6751930ce7b3)

*原载于 2016 年 12 月 21 日*[*www.edureka.co*](https://www.edureka.co/blog/splunk-tutorial)*。*