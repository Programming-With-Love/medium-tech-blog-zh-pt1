# 将 Go 应用程序连接到 Oracle 数据库

> 原文：<https://medium.com/oracledevs/connecting-go-application-to-oracle-database-on-prem-and-autonomous-with-and-without-oracle-c5535bbec135?source=collection_archive---------0----------------------->

在本文中，我将引导您完成将 Go 应用程序连接到本地 Oracle 数据库和 OCI 的自治数据库(内部和自治数据库，有和没有 Oracle 客户端库)。

听起来很多，但我保证比听起来容易。

一个应用程序只使用*包 go_ora* ，不需要 Oracle Instant Client 库。另一个使用软件包*或*(也称 goracle)，并需要 Oracle Instant Client。为了连接到本地数据库，两个应用程序都只使用简单的连接…