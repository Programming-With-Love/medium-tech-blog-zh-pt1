# Oracle 云基础架构上的某种有状态无服务器功能—实施 DIY 缓存

> 原文：<https://medium.com/oracledevs/somewhat-stateful-serverless-functions-on-oracle-cloud-infrastructure-implementing-a-diy-cache-433af41ce28d?source=collection_archive---------5----------------------->

我们称之为无服务器，我们认为他们是无状态的。Oracle 云基础设施(以及其他云平台)上的功能。当然，两者都不是。它们运行在服务器上。它们可以承载一些状态，尽管很大程度上是机会主义的，不可靠。

OCI 上的函数在一个容器内运行。当第一次调用函数时，容器被启动。并且让容器运行一段时间(几分钟),以便可能服务于附加功能…