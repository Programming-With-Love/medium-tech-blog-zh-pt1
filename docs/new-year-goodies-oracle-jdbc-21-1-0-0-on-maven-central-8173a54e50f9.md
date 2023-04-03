# 新年礼物 Maven Central 上的 Oracle JDBC 21.1.0.0

> 原文：<https://medium.com/oracledevs/new-year-goodies-oracle-jdbc-21-1-0-0-on-maven-central-8173a54e50f9?source=collection_archive---------3----------------------->

![](img/46b1e444f346f3b65f71729210eb79cb.png)

# 承诺的令人兴奋的事情有哪些

在我之前的[帖子](https://kuassimensah.medium.com/oracle-jdbc-19-9-0-0-on-maven-central-2e77840a9861?source=your_stories_page-------------------------------------)中，我提到了即将到来的 DB21c JDBC 驱动程序中令人兴奋的新特性以及一月初的可用性。我们到了，已经过了一月中旬，那么那些所谓的令人兴奋的事情是什么呢？

新特性可以分为以下几类:支持流行的 Java 框架、性能和可伸缩性、支持云原生应用、可诊断性和跟踪。

在这篇博文中，我只给出一个项目符号列表；如果你对某个特定的特性感兴趣，请阅读我的标题为“[DB21c 对 Java 开发者有什么好处](https://www.oracle.com/a/otn/docs/what-is-in-db21c-for-java-developers.pdf)”的技术简报。

# 支持 Java 框架

*   作为 JBoss 数据源的 UCP:通用连接池(UCP)有一个新的 Java 类，可以使用 CDI 与 JBoss 无缝集成
*   UCP 作为 Spring 数据源；设置应用程序属性文件中的配置

# 性能和可扩展性

*   虚拟线程的驱动程序支持(project Loom)
*   JDBC 驱动程序中的反应式扩展，内置于标准 Java 流订阅者和发布者类型，允许带背压支持的异步数据库访问。该扩展可以与流行的反应流库互操作。适用于 JDK 9 及更高版本。
*   为 GraalVM 本机映像预先配置
*   一种分片数据源，允许访问分片数据库，而无需显式提供和构建分片键
*   一个反应式流接收(RSI)库，为将大量流数据高速接收到 Oracle 数据库中提供了一条更快的路径；作为单独的 jar 文件(rsi.jar)提供，用于 JDK 9 及更高版本。

# 支持云原生应用

*   通过 *oracle.sql.json* 包对原生 JSON 类型和二进制格式存储的 JDBC 支持
*   从非文件系统(即内存等)加载 Wallet
*   采用微服务框架的 JDBC 和 UCP 配置(技术简介包含每个框架的链接)。

# 可诊断性和追踪

*   在以前的版本中，ClientInfo 和动态监控系统(DMS)指标是使用插装的 DMS jars 生成的。
*   在此版本中，您可以使用 JDBC 连接字符串(CONNECTION_ID = <value>)中的名称/值对来设置用于日志记录的连接标识符</value>

# 材料清单(BOM)

自从 [19.7.0.0 发布](/oracledevs/your-own-way-oracle-jdbc-drivers-19-7-0-0-on-maven-central-9a7dbb648995)以来，你可以使用 BOM 文件拉一个特定的或者多个工件；或者，您可以拉取一组预定义的工件，这些工件是生产、调试、可观察性和带可观察性的调试所需要的。

JDBC DB 21.1.0.0 工件的完整列表可以在[这里](https://search.maven.org/artifact/com.oracle.database.jdbc/ojdbc-bom/21.1.0.0/pom)找到。该列表由核心工件和伴随工件组成。

*   核心神器，一次只能一个，不能混用的有: *ojdbc8，ojdbc8_g，ojdbc8dms，ojdbc8dms_g，ojdbc11，ojdbc11_g，ojdbc11dms 和 ojdbc11dms_g*
*   可以与任何核心构件一起使用的配套构件有: *ucp、oraclepki、osdt_core、osdt_cert、ons、simplefan、xdb、xmlparserv2、orai18n、dms 和 rsi。*

# 风味布丁

**OJ DBC 11-生产，OJ DBC 11-调试，OJ DBC 11-可观察性，OJ DBC 11-可观察性-调试**:这些工件是用 JDK 11 构建/编译的，支持 JDBC 4.3 规范，并用于 JDK 11、12、13、14 和 15。

**OJ DBC 8-生产，OJ DBC 8-调试，OJ DBC 8-可观察性，OJ DBC 8-可观察性-调试**:这些 artifactIds 是用 JDK 8 构建/编译的，支持 JDBC 4.2 规范，并与 JDK 8 **，** 11、12、13、14 和 15 一起使用。

# Maven Central 上的 Oracle JDBC 开发人员指南

请参考我们在 Maven Central 上的[Oracle JDBC 开发人员指南](https://www.oracle.com/database/technologies/maven-central-guide.html)和 [19.7.0.0 博客文章](/oracledevs/your-own-way-oracle-jdbc-drivers-19-7-0-0-on-maven-central-9a7dbb648995)以获得每个工件的完整描述(用 ojdbc11 替换 OJ DBC 10)——除了上面简要描述的 rsi.jar(更多细节请参考技术概要)。

# (用于命令)等待下面发表的消息

我还承诺提供 Oracle-R2DBC 驱动程序的第一个版本；这些部分都准备好了，但我们需要得到一些法律和安全方面的批准。一月下旬是一个很好的预期。

观看 **@kmensah** 获取通知。