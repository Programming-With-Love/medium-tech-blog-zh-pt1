# Oracle 客户端功能资源

> 原文：<https://medium.com/oracledevs/oracle-client-feature-resources-4a0bda55db51?source=collection_archive---------0----------------------->

面向 C 和脚本语言开发人员的 Oracle 数据库资源。

![](img/4ab253b6035328fb90b6259e657e0977.png)

Photo by [Clyde He](https://unsplash.com/@clyde_he?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

**编者按:**与我们通常的技术内容不同，这篇文章在一个方便的地方收集了与现代 Oracle 开发人员相关的文档。把这篇文章当作参考指南，而不是演示。与此同时，如果你想演示[甲骨文云基础设施的免费层](https://signup.cloud.oracle.com/?language=en)，你可以今天就做(当然是免费的)。

Oracle 数据库为应用程序开发人员提供了许多功能。我的团队负责“客户端”,包括 C 和脚本语言的客户端库和语言驱动程序。这里是一个选择，在我们的领域相关的链接很少。

# 开源语言驱动程序

**Python Python-Oracle 数据库的 oracledb 驱动**

[OTN Python 开发者中心](https://www.oracle.com/database/technologies/appdev/python.html)

[python-oracledb 主页](http://127.0.0.1:5500/python/oracle.github.io/python-oracledb)

[python-oracledb 文档](https://python-oracledb.readthedocs.io/en/latest/)

[python-oracledb 源代码库](https://github.com/oracle/python-oracledb)

教程:

*   [快速入门:为 Oracle 数据库开发 Python 应用](https://www.oracle.com/database/technologies/appdev/python/quickstartpythononprem.html)
*   [快速入门:为 Oracle 自治数据库开发 Python 应用](https://www.oracle.com/database/technologies/appdev/python/quickstartpython.html)
*   [Python 和 Oracle Database LiveLab 入门](https://apexapps.oracle.com/pls/apex/r/dbpm/livelabs/view-workshop?wid=3482)
*   [Python 和 Oracle 数据库教程](https://oracle.github.io/python-oracledb/samples/tutorial/Python-and-Oracle-Database-The-New-Wave-of-Scripting.html)

**node . js node-Oracle 数据库的 oracledb 驱动**

[节点-oracledb 主页](http://oracle.github.io/node-oracledb)

[节点-oracledb npm 包](https://www.npmjs.com/package/oracledb)

[节点-oracledb 源代码 GitHub](https://github.com/oracle/node-oracledb)

[节点-oracledb 用户手册](http://oracle.github.io/node-oracledb/doc/api.html)

**Go gor Oracle 数据库驱动程序**

[Godror 源代码](https://github.com/godror/godror)

[goror 文档](https://pkg.go.dev/github.com/godror/godror?tab=doc)

[首页](https://golang.org/)

[转到 SQL 包文档](https://golang.org/pkg/database/sql/)

**用于 Oracle 数据库的 Ruby 和 Ruby on Rails**

[活动记录的 Oracle 增强适配器](https://github.com/rsim/oracle-enhanced)

[红宝石 oci8 宝石](https://rubygems.org/gems/ruby-oci8/)

[红宝石-oci8](https://github.com/kubo/ruby-oci8)

轨道上的红宝石

**Oracle 数据库的 PHP OCI8 驱动程序**

[地下 PHP 和 Oracle 手册](http://127.0.0.1:5500/php/201212-ug-php-oracle-1884760.pdf)

OTN PHP 论坛

[OTN 的 PHP 开发中心](https://www.oracle.com/database/technologies/appdev/php.html)

[PHP OCI 8 手册](http://php.net/manual/book.oci8.php)

[PHP 主页](http://php.net)

[PECL OCI8](http://pecl.php.net/package/oci8)

**用于 Oracle 数据库的 Perl DBD::Oracle 驱动程序**

[DBD::甲骨文网站](https://metacpan.org/pod/DBD::Oracle)

[DBD::甲骨文代码库](https://github.com/perl5-dbi/DBD-Oracle)

**Oracle 数据库的 R 和 ROracle 驱动程序**

[什么是 ROracle？](http://127.0.0.1:5500/roracle/201404_Session_6_ROracle.pdf)

[容器包装](https://www.oracle.com/database/technologies/roracle-downloads.html)

[转到 oracle.com 上的 R](http://oracle.com/goto/R)

# Oracle 客户端的主要特性

**应用连续性**

[Oracle 应用连续性主页](https://www.oracle.com/au/database/technologies/high-availability/app-continuity.html)

[文档:应用程序连续性概述](https://www.oracle.com/pls/topic/lookup?ctx=dblatest&id=GUID-0B463F72-73C9-4EB6-B98D-5EC828CDB1E7)

[技术简介:使用自治数据库的应用程序的连续可用性最佳实践—专用](https://www.oracle.com/technetwork/database/options/clustering/applicationcontinuity/continuous-service-for-apps-on-atpd-5486113.pdf)

[文档:了解应用程序连续性](https://docs.oracle.com/en/database/oracle/oracle-database/21/racad/ensuring-application-continuity.html#GUID-F86F480F-E2AE-422D-B226-40AEE19A3FF6)

[技术简介:Oracle 数据库的连续可用性应用连续性](https://www.oracle.com/docs/tech/database/applicationcontinuityformaa.pdf)

[技术概要:MAA 解决方案持续服务申请清单](https://www.oracle.com/a/tech/docs/application-checklist-for-continuous-availability-for-maa.pdf)

**CMAN-TDM**

[流量控制器模式下的 Oracle 连接管理器](https://www.oracle.com/pls/topic/lookup?ctx=dblatest&id=GUID-1F5A0587-3BCD-492C-BF86-ECBF7EE07A3E)

[甲骨文 CMAN-TDM 技术简介](https://download.oracle.com/ocomdocs/global/CMAN_TDM_Oracle_DB_Connection_Proxy_for_scalable_apps.pdf)

[将 CMAN-TDM 连接到 Oracle 云自主数据库](https://blogs.oracle.com/opal/post/connecting-cman-traffic-director-mode-to-an-oracle-cloud-autonomous-database)

**DRCP 连接池**

[技术简介:通过数据库常驻连接池实现极致的 Oracle 数据库连接可扩展性(DRCP)](https://www.oracle.com/docs/tech/drcp-technical-brief.pdf)

[技术简介:PHP 可伸缩性和高可用性:数据库常驻连接池和快速应用通知](http://127.0.0.1:5500/pooling/php_drcp_fan_white_paper.pdf)

[数据库开发指南](https://www.oracle.com/pls/topic/lookup?ctx=dblatest&id=GUID-015CA8C1-2386-4626-855D-CC546DDC1086)

# 其他 Oracle 客户端功能

# 令牌认证

[认证和授权 IAM 用户使用 Oracle DBaaS 数据库](https://docs.oracle.com/en/database/oracle/oracle-database/19/dbseg/authenticating-and-authorizing-iam-users-oracle-dbaas-databases.html#GUID-466A8800-5AF1-4202-BAFF-5AE727D242E8)

[为 Oracle 数据库认证和授权 Microsoft Azure Active Directory 用户](https://docs.oracle.com/en/database/oracle/oracle-database/19/dbseg/authenticating-and-authorizing-microsoft-azure-active-directory-users-oracle-databases.html#GUID-2712902B-DD07-4A61-B336-31C504781D0F)

[为微软活动目录命名配置 Oracle 数据库客户端](https://www.oracle.com/a/otn/docs/database/oracle-net-active-directory-naming.pdf)

[节点-oracledb:基于令牌的认证](https://oracle.github.io/node-oracledb/doc/api.html#tokenbasedauthentication)

[python-oracledb:基于令牌的认证](https://python-oracledb.readthedocs.io/en/latest/user_guide/connection_handling.html#token-based-authentication)

**简单的 Oracle 文档访问(SODA)**

[简单的 Oracle 文档访问(SODA)](https://docs.oracle.com/en/database/oracle/simple-oracle-document-access/)

[python-Oracle db 中的 SODA](https://python-oracledb.readthedocs.io/en/latest/user_guide/soda.html)

[SODA in node . js node-Oracle db](https://oracle.github.io/node-oracledb/doc/api.html#sodaoverview)

**分片**

[分片资源中心](https://www.oracle.com/database/sharding/)

[Oracle 分片概述](https://www.oracle.com/pls/topic/lookup?ctx=dblatest&id=GUID-0F39B1FB-DCF9-4C8A-A2EA-88705B90C5BF)

[python-oracledb:连接到分片数据库](https://python-oracledb.readthedocs.io/en/latest/user_guide/connection_handling.html#connecting-to-sharded-databases)

**客户端部署文件和自动调优**

[关于 Oracle Call Interface 程序员指南中的 ora access . XML](https://www.oracle.com/pls/topic/lookup?ctx=dblatest&id=LNOCI-GUID-CD599644-135A-4116-8B3B-40A9BA172E5C)

**隐含结果**

[OCI 支持隐结果](https://www.oracle.com/pls/topic/lookup?ctx=dblatest&id=GUID-66AAD7B2-B609-4CBC-94F7-161FB3294495)

[Python Python-Oracle db cursor . getimplicitresults()方法](https://python-oracledb.readthedocs.io/en/latest/user_guide/plsql_execution.html#implicit-results)

[PHP OCI 8 OCI _ get _ implicit _ resultset()](http://php.net/manual/en/function.oci-get-implicit-resultset.php)

# 软件和 SDK

**Oracle 即时客户端**

[即时客户端主页](https://www.oracle.com/database/technologies/instant-client.html)

[Linux 安装 Oracle 即时客户端文档](https://www.oracle.com/pls/topic/lookup?ctx=dblatest&id=GUID-3AD5FA09-8A7C-4757-8481-7A6A6ADF479E)

[Oracle Linux 即时客户端渠道](https://yum.oracle.com/oracle-instant-client.html)

[Oracle 即时客户端的容器](https://github.com/oracle/docker-images/tree/main/OracleInstantClient)

**Oracle 数据库 C 编程接口**

[首页](https://oracle.github.io/odpi/)

[文档](https://oracle.github.io/odpi/doc/index.html)

[源代码](https://github.com/oracle/odpi)

**Oracle 云自由层**

[首页](https://www.oracle.com/cloud/free/)

**甲骨文数据库 21c XE**

[首页](https://www.oracle.com/database/technologies/appdev/xe.html)

**容器**

[许多 Oracle 产品的 docker 文件](https://github.com/oracle/docker-images)

[Oracle 即时客户端文档](https://github.com/oracle/docker-images/tree/main/OracleInstantClient)

[Oracle GitHub 容器注册表:Oracle Linxux 7 的即时客户端映像](https://github.com/oracle/docker-images/pkgs/container/oraclelinux7-instantclient)

[Oracle GitHub 容器注册表:Oracle Linux 8 的即时客户端映像](https://github.com/oracle/docker-images/pkgs/container/oraclelinux8-instantclient)

[甲骨文容器注册中心](https://container-registry.oracle.com/ords/f?p=113:10::::::)

[开源项目流行的 Oracle 数据库 XE 21c 容器](https://hub.docker.com/r/gvenzl/oracle-xe)

# Oracle 驱动程序和预编译器

**Oracle 数据库的 Oracle 调用接口(OCI)**

[Oracle 调用接口程序员指南](https://www.oracle.com/pls/topic/lookup?ctx=dblatest&id=LNOCI)

[Oracle C 和 C++接口](https://www.oracle.com/database/technologies/appdev/oci.html)

**Oracle 数据库的 Oracle C++调用接口(OCCI)**

[Oracle C++调用接口程序员指南](https://www.oracle.com/pls/topic/lookup?ctx=dblatest&id=LNCPP)

[Oracle C 和 C++接口](https://www.oracle.com/database/technologies/appdev/oci.html)

**Oracle 数据库的 ODBC**

[Oracle C 和 C++接口](https://www.oracle.com/database/technologies/appdev/oci.html)

[Oracle 即时客户端 ODBC 发行说明](https://www.oracle.com/database/technologies/releasenote-odbc-ic.html)

[Oracle 数据库开发指南](https://www.oracle.com/pls/topic/lookup?ctx=dblatest&id=GUID-7931EDFB-7A70-4BBE-903E-8A2BB09DBE9D)

[Oracle 数据库 ODBC 驱动程序发行说明发布](https://www.oracle.com/pls/topic/lookup?ctx=dblatest&id=ODBCR)

**Pro*C**

[Oracle 预编译器的 Oracle 数据库程序员指南](https://www.oracle.com/pls/topic/lookup?ctx=dblatest&id=ZZPRE)

**Pro*COBOL**

[Oracle 预编译器的 Oracle 数据库程序员指南](https://www.oracle.com/pls/topic/lookup?ctx=dblatest&id=ZZPRE)

# 可扩展性和性能

**语句缓存**

[Oracle 调用接口中的语句缓存(OCI)](https://www.oracle.com/pls/topic/lookup?ctx=dblatest&id=GUID-1ECC306F-D6DB-435A-A46D-65E9D2571ED6)

[Oracle 调用接口(OCI)客户端语句缓存自动调优](https://www.oracle.com/pls/topic/lookup?ctx=dblatest&id=GUID-82D4AFAE-136B-4B83-B9F8-765111642B4F)

[PHP OCI8 语句缓存](https://www.php.net/manual/en/oci8.configuration.php#ini.oci8.statement-cache-size)

[节点-oracledb:语句缓存](https://oracle.github.io/node-oracledb/doc/api.html#stmtcache)

[python-oracledb:语句缓存](https://python-oracledb.readthedocs.io/en/latest/user_guide/tuning.html#statement-caching)

**客户端查询结果缓存**

[Oracle 数据库性能调优指南中的客户端结果缓存概念](https://www.oracle.com/pls/topic/lookup?ctx=dblatest&id=GUID-39C521D4-5C6E-44B1-B7C7-DEADD7D9CAF0)

[客户端 _ 结果 _ 缓存 _ 延迟](https://www.oracle.com/pls/topic/lookup?ctx=dblatest&id=GUID-78EDC1F7-8834-43FA-AC5A-98B1E1A3DC64)

[客户端结果缓存大小](https://www.oracle.com/pls/topic/lookup?ctx=dblatest&id=GUID-BC78F286-9D61-481F-8A37-2E4DC379E67C)

**预取和数组提取**

[关于设置预取计数](https://www.oracle.com/pls/topic/lookup?ctx=dblatest&id=GUID-7AE9DBE2-5316-4802-99D1-969B72823F02)

[Python-oracledb:调优获取性能](https://python-oracledb.readthedocs.io/en/latest/user_guide/tuning.html#tuning-fetch-performance)

**数组 DML**

节点-oracledb 文档:批处理语句执行和批量加载

[Python-oracledb:执行批处理语句和批量加载](https://python-oracledb.readthedocs.io/en/latest/user_guide/batch_statement.html)

# 监测和追踪

**自动工作量储存库**

[Oracle 数据库性能调优指南](https://www.oracle.com/pls/topic/lookup?ctx=dblatest&id=TGDBA)

**客户端可诊断性**

[Oracle 数据库管理员指南中的管理诊断数据](https://www.oracle.com/pls/topic/lookup?ctx=dblatest&id=GUID-8DEB1BE0-8FB9-4FB2-A19A-17CF6F5791C3)

[关于 Oracle Call Interface 程序员指南中 OCI 的故障可诊断性](https://www.oracle.com/pls/topic/lookup?ctx=dblatest&id=GUID-A2945AF1-36DE-4C87-8C19-DA82B352F176)

**端到端追踪**

[端到端应用追踪的目的](https://www.oracle.com/pls/topic/lookup?ctx=dblatest&id=GUID-E9F88E73-FB9A-40CF-B133-B5BF30F2BC56)

[使用 Oracle 数据库进行 PHP Web 审计、授权和监控](https://www.oracle.com/technetwork/articles/dsl/php-web-auditing-171451.html)

[Python Python-Oracle db Oracle 数据库端到端跟踪](https://python-oracledb.readthedocs.io/en/latest/user_guide/tracing.html#oracle-database-end-to-end-tracing)

[Node.js node-oracledb 端到端跟踪、中间层验证和审计](https://oracle.github.io/node-oracledb/doc/api.html#endtoend)