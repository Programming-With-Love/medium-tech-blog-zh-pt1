# 在 APIM 为运行在 Azure 函数上的 Java APIs 配置 Auth 2.0

> 原文：<https://medium.com/bb-tutorials-and-thoughts/configuring-auth-2-0-in-apim-for-java-apis-running-on-azure-functions-7eda2c151566?source=collection_archive---------0----------------------->

## 包含示例项目的逐步指南

![](img/2106da42bee86fd70c324410d2d11967.png)

当你在 Azure Functions 上部署 web 应用或 API 时，你可以直接从 Azure function endpoint 公开它们，也可以通过 Azure APIM 提供服务。使用 APIM 有几个优点，如根据上下文路径路由到不同的应用程序，实现…