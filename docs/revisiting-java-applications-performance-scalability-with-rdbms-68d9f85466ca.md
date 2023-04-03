# 重新审视使用 RDBMSes 的 Java 应用程序的性能和可伸缩性

> 原文：<https://medium.com/oracledevs/revisiting-java-applications-performance-scalability-with-rdbms-68d9f85466ca?source=collection_archive---------0----------------------->

*我最近的* [*博文*](http://db360.blogspot.com/2018/03/optimizing-performance-scalability-of.html) 的再版

这里有大量关于 Java 性能的文献(书籍、文章、博客、网站等等)；谷歌搜索返回超过 500 万次点击。举几个例子，*有效的 Java 编程语言指南*， *Java 性能权威指南*， *Java 性能调优简讯*及其相关的[网站](http://www.javaperformancetuning.com)。
这篇博文回顾了已知的加速和扩展 Java 应用程序数据库操作的最佳实践，然后讨论了新的机制，如数据库代理和异步数据库访问(ADBA)提议。

# 加速 Java 应用程序中的 RDBMS 操作

优化 Java 应用程序的数据库操作包括:加速数据库连接、加速 SQL 语句处理、优化网络流量和就地处理。

## 加速数据库连接

连接建立是最昂贵的数据库操作；Java 开发人员多年来一直使用的明显优化是连接池，它避免在运行时创建连接(除非您耗尽了池容量。

***客户端连接池***
Java 连接池，如 Apache Commons DBCP、C3P0、[Oracle Universal Connection Pool(UCP)](https://docs.oracle.com/database/122/JJUCP/toc.htm)等，是用作独立 Java 应用程序的一部分或用作 Java EE 容器(如 Tomcat、Weblogic、WebSphere 等)的数据源的一部分的库。Java EE 容器嵌入了它们自己的连接池，但也允许第三方池(例如，将 UCP 与 Tomcat 一起使用[，将 UCP 与 Weblogic 一起使用](http://www.oracle.com/technetwork/database/application-development/planned-unplanned-rlb-ucp-tomcat-2265175.pdf))。

大多数 Java 应用程序使用客户端或中间层连接池来支持中小型工作负载，但是，这些连接池仅限于 JRE/JDK 实例(即，不能超出其边界共享)，并且在部署数万个中间层或 Web 服务器时不切实际。即使每个 web 层上的池非常小，RDBMS 服务器也会被数以千计的预分配连接所淹没，这些连接在 90%以上的时间都处于空闲状态。

***代理连接池***

数据库代理，如 [MySQL 路由器](https://dev.mysql.com/doc/mysql-router/8.0/en/)、流量管理器模式下的 Oracle 数据库连接管理器( [CMAN-TDM](http://www.oracle.com/technetwork/database/enterprise-edition/cman-overview-084817.html) )、 [NGINX](http://NGINX | High Performance Load Balancer, Web Server, & Reverse ... https://www.nginx.com/) 等，是位于数据库客户机(即 Java 应用程序、Web 层)和 RDBMS 之间的代理服务器。这允许数千个中间层共享一个公共连接池。参见本博客第二部分的数据库代理。

此外，Oracle 数据库还提供了数据库端连接池，如[共享服务器](https://gerardnico.com/db/oracle/shared_server)和[数据库常驻连接池](https://docs.oracle.com/cd/E85694_01/ODPNT/featConnecting.htm#ODPNT-GUID-757A465B-4025-4550-8F13-EA92FE0C1B5A)(DRCP)；这些就不在本帖讨论了。

***杂项连接优化***

其他连接优化功能包括:推迟连接健康检查，并降低故障节点的优先级。

*推迟连接运行状况检查*
连接池将连接运行状况检查[推迟一段时间](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/jjucp/validating-ucp-connections.html#GUID-139508B9-1456-41CA-A860-2AFD9352C1E6)的能力，可以加快连接检查(即 *getConnection()* 返回速度)。
*取消失败节点的优先级* 在 Oracle RAC 等多实例集群数据库环境中，[此功能](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/jjdbc/JDBC-getting-started.html#GUID-D5BB4E1F-59FD-4C5E-876C-71420F2DAE9B)在定义的时间段内为失败的实例分配低优先级(也就是说，避免尝试从失败的实例获取连接)，从而加快连接检查。

## 加速 SQL 语句处理

处理 SQL 语句需要几个步骤，包括:解析(至少一次)、绑定变量、执行、获取结果集(如果是查询)以及提交或回滚事务(如果是 DML，即插入、更新或删除)。JDBC 提供了几个 APIs 旋钮来优化 SQL 语句处理，包括:预处理语句、语句缓存和结果集缓存。

***禁用默认提交***

自动提交每个 DML 是 JDBC 的默认/隐式事务模式。除非此模式符合您的需要，否则您应该在连接对象上显式禁用它，并用显式提交或回滚调用来区分您的事务(DML +查询)。
*即 conn.setAutoCommit(假)；*

***准备好的报表***

在执行 SQL 语句之前，必须对其进行解析(如果尚未解析的话)。解析(即[硬解析](https://asktom.oracle.com/pls/asktom/f?p=100:11:0::::P11_QUESTION_ID:2588723819082))是处理 SQL 语句时开销最大的操作。最佳实践包括使用[准备好的语句](https://docs.oracle.com/javase/tutorial/jdbc/basics/prepared.html)，在为绑定变量设置新值后，这些语句被解析一次，然后在后续调用中被多次重用。预准备语句的一个安全副产品是预防 SQL 注入。

***报表缓存***

可以指示 JDBC 驱动程序在 *smt.close()* 上自动缓存 SQL 语句(PreparedStatements 和 CallableStatements)。在相同语句的后续调用中，驱动程序指示 RDBMS 使用现有语句(即“ *use statement #2* ”)，而不发送语句字符串，从而避免了软解析(词法分析、语法解析)和潜在的网络往返。

在 connection 对象或 datasource 对象上启用了隐式语句缓存(注意:语句缓存是每个物理连接的一个数组)。

## 带有更改通知的结果集缓存—困难的方式

缓存 JDBC 结果集可以避免重复执行相应的 SQL 查询，从而显著提高 Java 应用程序的性能。RDBMSes 允许在服务器端缓存结果集，但是应用程序需要往返于数据库来检索它。本主题将在 [Oracle 数据库性能调优指南](https://docs.oracle.com/en/database/oracle/oracle-database/18/tgdba/database-performance-tuning-guide.pdf)的第 15 章中详细讨论。

通过进一步优化，这些结果集可以被推送到驱动程序(Java、C/C++、PHP、C#等)中，由应用程序使用，而无需数据库往返。如果结果集变得陈旧，与 RDBMS 表中的实际数据不同步，该怎么办？RDBMSes 提供了维护最新结果集的机制，从而确保了缓存结果集的一致性。例如，Oracle 数据库的查询更改通知允许向 RDBMS 注册 SQL 查询，并在其他线程提交的 DML 导致结果集不同步时接收通知。

Java 应用程序可以通过以下步骤显式实现带有更改通知的结果集缓存:

0)先决条件:必须启用服务器端结果集缓存，并且必须授予数据库用户模式“更改通知”权限。
如*向人力资源部授予变更通知*；//可能需要您的数据库管理员的帮助。

1.  在连接对象上创建“注册”

2)将查询与注册相关联

3)听通知

请参阅《甲骨文 JDBC 开发者指南》的第 26 章了解更多详情。

## 带有更改通知的结果集缓存—更简单的方法

您最好使用以下步骤，以更简单的方式启用客户端带无效的恢复集缓存(在 Oracle JDBC 驱动程序 18.3 版及更高版本中可用)

1.  在数据库配置文件(也称为 INIT.ORA)中设置以下参数。

2)将 JDBC 连接属性*Oracle . JDBC . enablequeryresultcache*设置为 true(默认值)。

3)将以下提示添加到 SQL 查询字符串" */*+ RESULT_CACHE */* "

如果不能通过修改 Java/JDBC 源代码来添加 SQL 提示，可以指示 RDBMS 缓存与特定表相关的所有查询的结果集，可以在创建表时(默认模式)缓存，也可以在以后(强制模式)缓存；这就是所谓的“表格标注”。

Oracle RDBMS 提供了一些视图，如用于监控结果集缓存有效性的 V$RESULT_CACHE_STATISTICS 和一个*CLIENT _ RESULT _ CACHE _ STATS $*表。有关配置服务器端结果集缓存的更多详细信息，请参见[性能调整指南](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/tgdba/database-performance-tuning-guide.pdf)中的第 15 节

## 数组提取

当从结果集中检索大量行时，必须进行数组提取。可以在语句、PreparedStatement、CallableStatement 或 ResultSet 对象上指定提取大小。
示例:pstmt . setfetchsize(20)；

当使用 Oracle 数据库时，该数组大小受 RDBMS 的内部缓冲区(称为会话数据单元(SDU ))的限制。SDU 缓冲区用于通过网络将数据从表传输到客户机。该缓冲区的大小(以字节为单位)可以在 JDBC URL 中指定，如下所示。

或者在网络服务配置文件 *sqlnet.ora* 和 *tnsnames.ora* 中的服务级别。根据 RDBMS 版本的不同，有一个硬限制:DB 12c 和更高版本为 2MB，DB 11.2 为 64K，DB 11.2 之前为 32K。
总之，即使您将数组获取设置为一个较大的数字，它也不能在每次往返中检索到比 SDU 允许的更多的数据。

## 数组 DML(更新批次)

JDBC 规范允许发送一批相同的 DML 操作(即，阵列插入、阵列更新、阵列删除)以便在服务器上顺序执行，从而减少网络往返。

更新批处理包括显式调用 *addBatch* 方法，该方法将一条语句添加到一个操作数组中，然后显式调用 *executeBatch* 方法发送该语句，如下例所示。

## 优化网络流量

这里有两种机制可以帮助优化 Java 代码和 RDBMS 之间的网络流量:网络压缩和会话多路复用。

***网络数据压缩***

通过局域网或广域网压缩 Java 应用程序和 RDBMS 之间传输的数据的能力减少了数据量、传输时间和往返次数。

***会话多路复用*** *Oracle 数据库连接管理器(又名 CMAN)提供了在单个网络连接上汇集多个数据库连接的能力，从而节省了操作系统资源。在[网络服务管理指南](https://docs.oracle.com/en/database/oracle/oracle-database/18/netag/net-services-administrators-guide.pdf)中查看更多详细信息。*

## *就地处理*

*如前所述，SQL 语句处理涉及数据库客户机(即 Java 中间层/web 服务器)和 RDBMS 之间的多次往返。如果将 Java 代码移近或移入 RDBMS 会话/进程，就减少了构成大部分延迟的网络流量。
好吧，存储过程是过时的，所以是七十年代的，但是现代数据处理，如 Hadoop 或 Spark，将处理和数据放在一起以实现低延迟。*

*如果你的目标是效率，你应该考虑在数据绑定模块中使用 Java 存储过程。我在我的书的第一章[中讨论了存储过程的利弊。我要补充的是，在现代基于微服务的架构中，REST 包装的存储过程是数据绑定服务的良好设计选择。](http://www.amazon.com/exec/obidos/ASIN/1555583296)*

*所有 RDBMSes 都提供各种语言的存储过程，包括专有过程语言、Java、JavaScript、PHP、Perl、Python 和 TCL。Oracle 数据库提供 Java 和 [PL/SQL 存储过程](https://github.com/oracle/oracle-db-examples/tree/master/plsql)。数据库中的 Java 是最好的无名 Oracle 数据库宝石之一；参见 GitHub 上的一些代码示例。*

# *横向扩展 Java 工作负载*

*在这篇博文的第二部分，我将讨论扩展 Java 工作负载的各种机制，包括分片和多租户数据库、数据库代理和异步 Java 数据库访问 API 提议。*

## *使用分片数据库的 Java 应用程序的水平伸缩*

*分片数据库——跨越几个数据库对表进行水平分区——已经存在一段时间了。使用分片数据库的 Java 应用程序必须:
(i)定义哪些字段用作分片键(和超级分片键)
(ii)设置值并构建键，然后请求连接到数据源。
Java SE 9 提供了[标准 API](https://docs.oracle.com/javase/9/docs/api/java/sql/ShardingKey.html)来构建分片和超级分片密钥。*

*如果没有进一步的优化，所有碎片感知连接请求都将转到一个中央机制，该机制维护碎片键的映射或拓扑，从而导致每个请求多一跳。*

*Oracle 通用连接池(UCP)得到了增强，可以透明地收集映射到特定碎片的所有密钥。一旦 UCP 获得了密钥范围，它就根据碎片密钥将连接请求定向到适当的碎片。*

## *扩展多租户 Java 应用程序*

*完全多租户 Java 应用程序必须使用多租户 RDBMS，其中一组租户或每个租户都有自己的数据库(用 Oracle 的说法，它有自己的*可插拔数据库或 PDB* )。对于成千上万的租户，使用成千上万的数据库(或 pdb ),一种简单的方法是为每个数据库分配一个池；我们已经见证了每个租户一个连接的幼稚架构。
Oracle UCP 已经过增强，可以使用一个池来管理所有(数万)数据库。在向特定数据库发出连接请求时，如果没有连接到该数据库的空闲/可用连接，UCP 会透明地重新利用池中的一个空闲连接，该连接连接到另一个要重新连接到该数据库的数据库，从而允许使用一小组池连接来服务所有租户。*

*参见[UCP 文档](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/jjucp/shared-pool-for-multitenant-data-sources.html#GUID-7C397AA8-9C78-4DB0-AAF4-15BBF9AFFB85)了解关于每个租户使用一个数据源或所有租户使用一个数据源的更多详细信息。*

## *数据库代理*

*代理是运行在数据库和其客户端之间的中间人软件，例如 Java 应用程序。市场上有几种代理产品；举几个例子: [MySQL 路由器](https://dev.mysql.com/doc/mysql-router/8.0/en/)、流量管理器模式下的 Oracle 数据库连接管理器( [CMAN-TDM](http://www.oracle.com/technetwork/database/enterprise-edition/cman-overview-084817.html) )、ProxySQL 等等。
Oracle CMAN-TDM 是 Oracle 数据库 18c 中的新增功能；它是现有 Oracle 连接管理器(也称为 CMAN)的扩展，提供了以下新功能*

*   *对应用程序完全透明*
*   *将数据库流量路由到正确的实例(计划)*
*   *隐藏数据库计划内和计划外停机，以支持零应用程序停机*
*   *优化数据库会话使用和应用程序性能*
*   *增强数据库安全性*

*CMAN-TDM 是客户端无关的，它支持所有的数据库客户端应用程序，包括:Java，C，C++，DotNET，Node.js，Python，Ruby，r*

*Java 应用程序将连接到 CMAN-TDM，后者又使用最新的驱动程序和库连接到数据库，然后透明地提供应用程序只有使用最新的驱动程序和 API 才能获得的服务质量。*

*在 [CMAN 登录页面](http://www.oracle.com/technetwork/database/enterprise-edition/cman-overview-084817.html)和登录页面链接的网络服务文档中查看更多详细信息。*

## ***异步数据库访问 API (ADBA)***

*现有的 JDBC API 导致线程阻塞、线程调度和争用；它不适合反应式应用程序或高吞吐量和大规模部署。有第三方异步 Java 数据库访问库，但是 Java 社区需要一个标准 API，用户线程在其中提交数据库操作并返回。*

*基于 Java . util . concurrent . completion stage 接口的新 API 提案；它可以从 OpenJDK 沙箱@[http://tinyurl.com/java-async-db.](http://tinyurl.com/java-async-db.)下载。API 实现负责执行操作，完成 CompletableFutures。
您可以通过[最新的演示和示例](https://www.slideshare.net/oracledevs/silicon-valley-jug-meetup-july-18-2018/oracledevs/silicon-valley-jug-meetup-july-18-2018)感受 ADBA API。*

****ADBA 战胜 JDBC****

*为了帮助社区了解 ADBA，它的一个测试/功能版本(没有异步行为)在 JDBC 运行——我们称之为 AoJ 在 ADBA 而不是 JDBC —@[https://github . com/Oracle/Oracle-d b-examples/tree/master/Java/AoJ](https://github.com/oracle/oracle-db-examples/tree/master/java/AoJ)。我鼓励读者去玩[张博的例子](https://github.com/oracle/oracle-db-examples/tree/master/java/AoJ/test/com/oracle/adbaoverjdbc/test)。*

*随着将纤程和 Java 延续引入 JVM 的项目 Loom 的发布，我们将再次回顾使用 RDBMSes 的 Java 应用程序的性能和可伸缩性。*

**原载于 2018 年 3 月 29 日*[*db360.blogspot.com*](http://db360.blogspot.com/2018/03/optimizing-performance-scalability-of.html)*。**