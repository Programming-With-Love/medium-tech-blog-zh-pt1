# 开源 Pinterest MySQL 管理工具

> 原文：<https://medium.com/pinterest-engineering/open-sourcing-pinterest-mysql-management-tools-7a9f90cffc9?source=collection_archive---------1----------------------->

Robert Wultsch | Pinterest 工程师

过去，我们分享过[你为什么应该喜欢 MySQL](https://engineering.pinterest.com/blog/learn-stop-using-shiny-new-things-and-love-mysql) 以及它如何通过切分帮助 [Pinterest 扩大规模。在今天的](https://engineering.pinterest.com/blog/sharding-pinterest-how-we-scaled-our-mysql-fleet)[甲骨文开放世界](https://events.rainfocus.com/oow15/catalog/oracle.jsp?search=[CON3654]&search.event=openworldEvent&search.day=20151028)大会上，我们宣布开源我们维护 MySQL 基础设施的绝大部分自动化。在本帖中，我们将详细介绍我们的 MySQL 环境，用于管理它的工具，以及如何实现它们来自动化您的 MySQL 基础架构。

![](img/c24965affe82267f52c7fbd2eadcbf4b.png)

## Pinterest 上的 MySQL 基础知识

历史上，我们使用 MySQL 存储一些最重要的数据，包括 pin、电路板、图像元数据和 Pinners 的证书。

最近，我们增加了以下使用案例:

*   [PinLater](https://engineering.pinterest.com/blog/pinlater-asynchronous-job-execution-system) :部分由于[内核优化](https://engineering.pinterest.com/blog/mysql-performance-optimization-50-more-work-60-less-latency-variance)，MySQL 已经取代 Redis，成为我们的异步作业执行引擎唯一支持的后端。
*   [Zen](https://www.youtube.com/watch?v=yI0vHfgK6oI) : MySQL 已经加入 HBase，作为我们图形存储引擎的支持后端。

对于 Pinterest 上的所有 MySQL 用例，从管理角度来看，环境是相同的:

*   一个主服务器带一个或两个从服务器:历史上，MySQL 用于一个副本集中的多个可写实例。这种拓扑容易出错，已经简化为一个主设备和一个或多个从设备。
*   [Zookeeper 提供服务发现](https://engineering.pinterest.com/blog/serving-configuration-data-scale-high-availability):管理工具和 MySQL 应用之间的契约是 Zookeeper。除了少数例外，ZooKeeper 为客户提供数据库主机名、用户名和密码。

## 云中 MySQL 实例的生命周期

Pinterest 上的 MySQL 服务器启动、存活和死亡都只伴随着最罕见的配置变化。升级内核、MySQL 版本和任何其他需要重启数据库的更改都不会就地完成。相反，这些操作总是根据需要通过服务器更换和故障切换/从属升级来执行。这种选择消除了管理中间状态的需要，极大地简化了我们的自动化。我们称这种心态为“运营佛教”，意思是我们不会依赖我们的服务器，因为它们可能明天就不在了。

我们最重要的脚本之一是*launch _ replacement _ db _ host . py*。在最简单的情况下，*launch _ replacement _ db _ host . py*唯一需要的参数是失败的从服务器的主机名。检查现有实例，计算新服务器所需的所有参数，然后启动新服务器。对于其他更改，如 MySQL 升级、硬件升级/降级和数据中心迁移，有可选参数。

在新服务器启动并从我们的供应系统接收到初始基本配置后，cron 作业将注意到数据目录为空，并运行*MySQL _ restore _ xtra backup . py*。基于来自我们的服务发现(ZooKeeper)的信息，这个脚本将尝试找到一个数据库备份，恢复它，设置复制，并将新的 MySQL 实例添加到目录中。与*launch _ replacement _ db _ host . py*一样，*MySQL _ restore _ xtra backup . py*接受许多非标准用途的可选参数。

如果 MySQL 主服务器需要更换，则必须运行 *mysql_failover.py* 脚本来将主要从服务器升级为主服务器。该脚本处理活着的或死去的初始主机，然后修改 MySQL 复制拓扑并更新服务发现。

当一个服务器从 ZooKeeper 中移除后，它将被一个退役队列系统所控制。该系统有几个导致服务器最终终止的步骤:

*   ZooKeeper 中不存在的服务器被认为没有被使用，并且将重置几个状态计数器。
*   一天后，将检查服务器，查看是否有任何活动导致状态计数器增加。如果计数器已经递增，则退出过程中止。如果计数器没有增加，MySQL 实例将被发送一个关闭命令。
*   又过了一天，如果服务器的数据库没有重新启动，服务器将被终止。

## 其他公用事业

我们已经构建了各种各样的其他实用程序，并且也是开源的:

*   *MySQL _ replica _ mappings . py*:这个脚本以一种易于用于 shell 脚本的格式，为管理员提供了生产中的内容的快速视图(我们将“生产中”定义为“ZooKeeper 中”)。
*   mysql_backup_xtrabackup.py :这是我们 mysql 的主要备份系统。这些备份被 *mysql_restore_xtrabackup* 用来构建新的 mysql 实例。
*   *archive_mysql_binlogs.py* :我们备份 mysql 复制日志，以便在副本集中的所有服务器都丢失的情况下执行时间点恢复。
*   这个脚本管理我们的数据库用户。这是我们最古老的自动化技术之一，也是我们最受限制的技术之一。它暂时满足了我们的需求，但迟早需要大幅扩展。
*   *mysql_cnf_builder.py* :该脚本基于全局默认值构建 mysql 配置文件，然后覆盖工作负载类型、硬件和 MySQL 版本。包括几个示例配置文件。
*   *mysql_checksum.py* 和*get _ recent _ checksum . py*:每天，我们对一部分碎片运行 pt-checksum，以确定主设备和从设备是否不同步，如果是，同步程度如何。(大多数时候，我们的复制漂移是零或者非常接近零。)mysql_checksum.py 脚本运行校验和并存储结果。*get _ recent _ checksums . py*脚本将检索所有最近的校验和结果，并以用户友好的方式显示数据。
*   还有一堆！

## 不是万灵药

这些工具紧密集成到我们的服务发现机制中，并且可能需要对从服务发现中读取和写入的代码进行适度的修改。与我们已经开源的其他技术不同，这些实用程序有一些遗留限制，例如缺少对两个以上从设备的支持。我们希望这些工具对希望为 MySQL 基础设施创建自动化的其他人有用。

## 人类效率

由于我们工具的有效性，我们能够用不到两名专门的工程师来维护具有数百 TB 数据的 MySQL 环境。

## 代码

我们的公共代码库现已上线，可以在位于[https://github.com/pinterest/mysql_utils](https://github.com/pinterest/mysql_utils)的 [GitHub](https://github.com/pinterest) 上找到我们的其他开源项目

鸣谢:Ernie Souhrada 也为 MySQL 管理工具做出了贡献。