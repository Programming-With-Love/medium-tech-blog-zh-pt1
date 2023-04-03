# Oracle 支持。NET 6 和实体框架核心 6:方向声明

> 原文：<https://medium.com/oracledevs/oracle-support-for-net-6-and-entity-framework-core-6-statement-of-direction-cfa5d1c59ff8?source=collection_archive---------0----------------------->

![](img/801c663fb6f3ccfb1bc33057f9897f43.png)

随着计划中的。NET 6 和实体框架 Core 6 今年 11 月 9 日，Oracle。NET 团队想要概述我们支持这两个新软件版本的计划。

## 。网络 6

甲骨文将迅速跟进。NET 6 发布，NuGet Gallery 上提供了经过认证的 ODP.NET Core 21c 版本，计划在 11 月发布。

如果您想今天就开始，您已经可以使用最新的。NET 6 预览版带【ODP.NET】的[Core 21c](https://www.nuget.org/packages/Oracle.ManagedDataAccess.Core/3.21.1)。我们已经针对运行时运行了我们的 ODP.NET 功能测试套件，没有发现任何新的问题。

官方说法，我们不会支持。NET 6 直到 ODP.NET 核心 11 月发布。尽管如此，如果你发现了问题，请在 [GitHub](https://github.com/oracle/dotnet-db-samples/issues) 或[ODP.NET 论坛](https://community.oracle.com/tech/developers/categories/odp.net)上告诉我们。我们会看看的。

## 实体框架核心 6

实体框架核心(EF 核心)6 需要更多的开发工作，而不是保证。NET 6 与 ODP.NET 核心提供商的兼容性。因此，支持新的 EF 核心版本需要更长的时间。我们预计在 2021 年底之前在 NuGet Gallery 上发布 Oracle EF Core 6 provider。

我们不打算发布测试版。与 Oracle EF Core 5 版本类似，版本 6 支持将直接投入生产。

这篇博文代表了我们对 ODP.NET 何时能够认证和支持微软即将推出的。NET 6 和 EF Core 6 发布。如果您有任何问题或意见，请直接联系我(alex.keh[at]oracle.com)。