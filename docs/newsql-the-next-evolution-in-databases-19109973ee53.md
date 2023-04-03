# new SQL——数据库的下一次发展

> 原文：<https://medium.com/capital-one-tech/newsql-the-next-evolution-in-databases-19109973ee53?source=collection_archive---------0----------------------->

## TiDB、CockroachDB、FaunaDB 和 Vitess 之间的一些异同

![](img/6d23f58711502ec30660010c4c986273.png)

你可能想知道这个新的流行词是什么？为了理解它，我们必须简要回顾一下数据库的发展历史。

# SQL 和 RDBMS 时代

70 年代初，IBM 推出了用于操作数据的 [**SQL**](https://www.w3schools.com/sql/) ( **S** 结构化 **Q** 查询 **L** 语言)。这个想法开始流行，很多公司采用了 SQL 介绍自己实现的[**RDBMS**](https://en.wikipedia.org/wiki/Relational_database_management_system)(**R**relational**D**ATA**b**ase**M**management**S**systems)。这包括 Oracle 公司的 Oracle、IBM 公司的 Informix、DB2 和开源的 MySQL。

在 RDBMS 中，数据是以行和列的结构化表格格式存储的，目的是满足[](https://en.wikipedia.org/wiki/ACID_(computer_science))**(**A**tomi city，**C**consistency， **I** solation， **D** urability)属性。RDBMS/SQL 能够实现 ACID 属性，因为数据保存在一个大型数据库服务器中，这意味着在这些情况下数据一致性不是问题。但是随着 90 年代互联网的爆炸式发展，产生的数据量呈指数级增长。数据的激增导致了一种新的范式— **NoSQL****

# **NoSQL 时代**

**NoSQL 数据库是围绕在分布式环境中扩展数据的概念而设计的。在 NoSQL 数据库中，在不同的远程机器上总是有数据的副本。这些数据库是使用 [CAP 定理](https://en.wikipedia.org/wiki/CAP_theorem)作为主干设计的。这个定理表明，在*一致性*、*可用性*和*分区容差*之间，在任何给定的时间只能实现这三个方面中的两个。这些数据库利用[最终一致性](https://en.wikipedia.org/wiki/Eventual_consistency)保证读取操作接收最近的写入。**

**数据的发展和分布式系统的成熟导致 NoSQL 数据库慢慢渗透到 RDBMS 市场。 [MongoDB](https://en.wikipedia.org/wiki/MongoDB) 、 [Cassandra](https://en.wikipedia.org/wiki/Apache_Cassandra) 、 [Redis](https://en.wikipedia.org/wiki/Redis) 都是大家可能比较熟悉的 NoSQL 热门数据库。**

# **NewSQL 简介**

**尽管 NoSQL 数据库的各种变体仍在继续使用，但与 NoSQL 并行出现了另一种范式— **NewSQL** 。NewSQL 承诺结合 RDBMS 的好处(强一致性)和 NoSQL 的好处(可伸缩性)；它主要通过新的架构模式和高效的 SQL 存储引擎来实现这一点。**

**大多数当前的 NewSQL 数据库都是基于 Google 的 [Spanner 数据库](https://static.googleusercontent.com/media/research.google.com/en//archive/spanner-osdi2012.pdf)和耶鲁大学的[Calvin:Fast Distributed Database Systems](http://cs.yale.edu/homes/thomson/publications/calvin-sigmod12.pdf)等学术论文中的理论。Spanner 是 Google 的可扩展、多版本、全球分布、同步复制的数据库。它是第一个在全球范围内分发数据并支持外部一致的分布式事务的系统。耶鲁大学的 *Calvin* 学术论文是关于利用确定性来保证分布式事务的主动复制和完全 ACID 合规性，而无需两阶段提交。**

**[TiDB](https://github.com/pingcap/tidb) ，[cocroach db](https://github.com/cockroachdb/cockroach)， [FaunaDB](https://fauna.com/faunadb) ， [Vitess](https://github.com/vitessio/vitess) 是几个领先的 NewSQL 数据库。对于如何确保与可伸缩架构的高度一致性，每个数据库实现都有自己的看法。让我们深入了解一下。**

****TiDB****

**TiDB 是一个开源数据库，支持分布式 HTAP(混合事务和分析处理)，并与 MySQL 兼容。 [PingCap](https://pingcap.com/en/) 是支持 TiDB 的公司。初始版本于 2017 年 10 月发布，其当前稳定版本为 2.1.9。**

**TiDBs 的突出特点是:**

*   ****混合** — TiDB 支持分析处理(OLAP)和事务处理(OLTP)工作负载。这意味着不需要进行从应用事务数据库到分析数据库的 ETL。TiDB 的存储层 [TiKV](https://github.com/tikv/tikv) 由用于 OLTP 的 TiDB 集群访问，由用于 OLAP 的本机支持的 [TiSpark](https://github.com/pingcap/tispark) 访问。**
*   ****Cloud Native** — TiDB 设计为在云中运行(公共、私有和混合)，其存储层 [TiKV](https://github.com/tikv/tikv) 已被 Cloud Native Computing Foundation 接受为沙盒项目。**
*   ****MySQL 兼容** —应用程序可以将 TiDB 视为 MySQL 服务器，并使用其现有的客户端库进行连接，而无需从应用程序端进行任何更改。**
*   ****更少的 ETL** —由于 TiDB 同时作为 OLTP 和 OLAP 运行，因此不需要从 OLTP 到 OLAP 进行 ETL。**

# **CockroachDB**

***“cocroach db 是一个分布式 SQL 开源数据库，构建在事务性和强一致性的键值存储之上。它水平缩放；在磁盘、机器、机架甚至数据中心故障中幸存下来，只需最少的中断和手动干预；支持强一致性的 ACID 事务；并为结构化、操作和查询数据提供了熟悉的 SQL API。”*——[来自蟑螂 GitHub](https://github.com/cockroachdb/cockroach) 。**

**[蟑螂实验室](https://www.cockroachlabs.com/)是支持 CockroachDB 的公司。初始版本于 2015 年 9 月发布，其当前稳定版本为 19.1.1。**

**CockroachDB 的显著特点是:**

*   ****SQL 兼容** —尽管 CockroachDB 拥有分布式的、强一致性的、事务性的键值存储，但它的外部 API 是标准的 SQL 兼容的。**
*   ****多活动可用性**—cocroach db 的可用性模型被称为*“多活动可用性”*。多活动可用性提供了无冲突地读写群集中每个节点的好处。多个副本运行相同的服务，流量被路由到所有副本。如果任何一个副本出现故障，其他副本只需处理路由到它的流量。**
*   ****在线模式更改**—cocroach db 提供了一个内置的在线模式更改特性；一种更新表模式的简单方法，不会给应用程序带来任何负面影响。对表模式的更改在数据库运行时发生。架构更改作为后台进程运行，不锁定基础表数据。这使得应用程序查询可以正常执行，而不会对读/写产生任何影响。**

# **FaunaDB**

***“FaunaDB 是一个现代分布式操作数据库，用于以云和容器为中心的环境。它是世界上第一个受 Calvin 启发的商业数据库，Calvin 是一个用于多区域环境的严格可序列化的事务协议。”*——[来自 FaunaDB 网站](https://docs.fauna.com/fauna/current/introduction.html)。**

**[Fauna](https://fauna.com/company) 是支持 FaunaDB 的公司，他们提供 FaunaDB 的本地、云和无服务器产品。**

**FaunaDB 的显著特征是:**

*   ****主动-主动** — FaunaDB 支持无主控、多云、主动-主动架构，可帮助应用实现 100%的数据库正常运行时间。**
*   ****多模型** — FaunaDB 可以管理多种数据模型，如关系、图形和文档。**
*   ****数据暂时性** — FaunaDB 提供基于快照的存储引擎，可在可配置的时间段内保留历史数据，并允许纠正快照中的数据错误。**
*   ****水平可扩展性** — FaunaDB 支持水平可扩展性，允许您在不中断同一站点或全球数据中心内的应用服务的情况下添加和删除节点。**

# **维特斯**

***“Vitess 是一个开源的数据库集群系统，通过广义分片实现 MySQL 的横向扩展。”* — [来自 Vitess GitHub](https://github.com/vitessio/vitess) 。**

**Vitess 诞生于 YouTube 的扩展需求，目前支持其后端。T21 是支持开源项目的公司。Vitess 目前的稳定版本是 3.0。**

**Vitess 的显著特点是:**

*   ****可扩展的 MySQL** —它引入了 SQL 的所有特性(连接、索引、聚合等)和 NoSQL 的所有优势。**
*   ****轻量级连接**——与 MySQL 连接相比，Vitess 的连接非常轻量级，可以轻松扩展。**
*   ****拓扑服务** — [拓扑服务](https://vitess.io/docs/user-guides/topology-service)是一个元数据存储(ETCD 或 Zookeeper ),它包含关于运行服务器、分片方案和复制图的信息。由于拓扑服务，集群视图对于不同的客户端总是最新的和一致的。**

# **NewSQL 的未来**

**就像 NoSQL 在互联网时代获得的动力一样，这篇博客中讨论的 NewSQL 数据库也在获得动力，并且在公共云时代有很大的潜力。希望这个博客提供了一个关于 NewSQL 数据库的高层次概述，以帮助你开始这个旅程！**

*******

***披露声明:2019 首创一号。观点是作者个人的观点。除非本帖中另有说明，否则 Capital One 不隶属于所提及的任何公司，也不被这些公司认可。使用或展示的所有商标和其他知识产权是其各自所有者的财产。***