# Oracle CloudWorld —许可说明

> 原文：<https://medium.com/version-1/oracle-cloudworld-licensing-notes-35985d273d2a?source=collection_archive---------4----------------------->

![](img/f33a0e0913922c724dabf0897d0c53ca.png)

Photo by [An Tran](https://unsplash.com/@vinhan?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/s/photos/cloud-computing?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

我们一直在筛选来自 Oracle 和 Cloud World 的版本 1 公告，并与他们交谈，寻找与许可相关的项目；以下是我们目前看到的情况:

**甲骨文验证的 SAM 计划**发布——我们目前正在撰写一篇关于此事的文章，敬请关注后续文章

**甲骨文合金** —公告[此处](https://www.oracle.com/news/announcement/ocw-oracle-alloy-brings-power-of-cloud-to-masses-2022-10-18/)；基本上是客户云(C@C ),但会有条款允许客户代表他们的客户使用云进行托管，允许 C@C 的白标/重标提供托管服务。会很有意思(对我们这种人来说！)查看此类协议背后的条款和条件。

**Oracle 23c Beta** —万众期待的[公告](https://www.oracle.com/news/announcement/ocw-database-innovations-simplify-development-enhance-protection-2022-10-18/)；23c 将是数据库的下一个长期支持版本；下面是值得注意的(与许可证相关的)功能。值得注意的是，客户将不得不从 19c 迁移到 23c 不可能从 11g 或 12c 直接迁移到 23c。

o**golden gate Free**——也许这将更好地命名为 GoldenGate XE，类似于“全功能”但有限的[数据库 XE](https://www.linkedin.com/pulse/oracle-21c-xe-free-database-released-paul-bullen/) 。如今，我们看到客户越来越多地使用 GoldenGate 它是一个非常有用的工具，可以为变更数据捕获(CDC)或数据库版本之间的迁移提供数据复制。鉴于它是一个强大而受欢迎的产品，不出所料，它有一些限制:它只能与 20Gb 以下的数据库一起工作(尽管这将如何管理还有待观察)，没有支持，不能与商业 GoldenGate 一起使用。它将作为 docker 容器提供，我认为这将为 DBA 和管理员提供一个很好的机会来习惯它作为一个产品。公告[此处](https://blogs.oracle.com/dataintegration/post/oracle-goldengate-free)

o **MongoDB 兼容性** —允许客户在 Oracle 中本地运行 MongoDB 工作负载。这似乎有点奇怪，因为一些组织正在从 Oracle DB 迁移到 MongoDB，但我可以看到一些客户试图在单个 DB 引擎上进行整合的使用案例，例如在 ULA 中。详情[此处](https://blogs.oracle.com/database/post/installing-database-api-for-mongodb-for-any-oracle-database)

o **燕尾服 22c**——这看起来有点令人惊讶；如今，随着越来越少的顾客使用它，我们在 Tuxedo 方面看不到太多的活动。这增加了部署调整，以便更快地创建和部署 Docker / Kubernetes 映像和云部署。公告[此处](https://blogs.oracle.com/database/post/announcing-oracle-tuxedo-22c)

o **对应用程序的自治数据库支持** — EBS 通过了自治数据库认证，减少了管理开销。没有这样的公告，但有一个自主概述[此处](https://www.oracle.com/autonomous-database/tools)

o **零数据丢失自主恢复服务**–好的，所以实际上与许可无关，但对那些使用 OCI 的人来说可能是一个有趣的服务；这是相当于本地产品的 Oracle 云。这种东西看起来销量不大，但在云中的可访问性可能更容易，并且可以提供关键的数据弹性。详情[此处](https://blogs.oracle.com/maa/post/introducing-recovery-service)

o **全堆栈灾难恢复(适用于 OCI)** —同样，许可相关人员的兴趣有限，但这是一个有趣的产品，它允许对 OCI 的云资源进行分组和管理—这允许“单击”即可迁移应用程序的所有组件。详情[此处](https://www.oracle.com/cloud/full-stack-disaster-recovery/)

作为 Oracle 许可和托管服务的专家，我们将继续为您提供 Oracle CloudWorld 2022 的最新发布信息。

一如既往，如果您对甲骨文授权有任何疑问，请访问我们的[网站](https://www.version1.com/it-service/software-asset-management/)或[直接联系](https://www.version1.com/contact/)我们。

**关于作者:** 保罗·布伦是[第 1 版](https://www.version1.com/)的首席甲骨文许可顾问兼实践负责人。