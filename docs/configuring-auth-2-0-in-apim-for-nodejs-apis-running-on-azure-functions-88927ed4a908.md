# 在 APIM 为运行在 Azure 函数上的 NodeJS APIs 配置 Auth 2.0

> 原文：<https://medium.com/bb-tutorials-and-thoughts/configuring-auth-2-0-in-apim-for-nodejs-apis-running-on-azure-functions-88927ed4a908?source=collection_archive---------0----------------------->

## 包含示例项目的逐步指南

![](img/e1dbd5bab283ec304ce2195ee275d7d1.png)

当你在 Azure Functions 上部署 web 应用或 API 时，你可以直接从 Azure Function endpoint 公开它们，也可以通过 Azure APIM 提供服务。使用 APIM 有几个优点，如根据上下文路径路由到不同的应用程序，实现…