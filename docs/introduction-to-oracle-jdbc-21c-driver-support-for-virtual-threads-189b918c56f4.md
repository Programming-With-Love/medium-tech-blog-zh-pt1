# Oracle JDBC 21c 驱动程序对虚拟线程的支持简介

> 原文：<https://medium.com/oracledevs/introduction-to-oracle-jdbc-21c-driver-support-for-virtual-threads-189b918c56f4?source=collection_archive---------0----------------------->

![](img/42824ee0119bfee9ec22434834baed28.png)

[Develop Java apps with Oracle Database](https://www.oracle.com/database/technologies/appdev/jdbc.html)

华雷斯少年

## 介绍

这篇博客文章介绍了 Oracle 的 [JDBC 21c 驱动程序](https://www.oracle.com/database/technologies/appdev/jdbc-downloads.html)对虚拟线程的支持。

Oracle 从 21.1.0.0 版开始构建其 JDBC 驱动程序，以支持使用虚拟(轻量级)线程的同步 [JDBC](https://docs.oracle.com/en/database/oracle/oracle-database/21/jajdb/index.html) 访问。