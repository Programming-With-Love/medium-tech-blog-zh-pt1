# 在 APIM 为运行在 Azure 函数上的 Python APIs 配置 Auth 2.0

> 原文：<https://medium.com/bb-tutorials-and-thoughts/configuring-auth-2-0-in-apim-for-python-apis-running-on-azure-functions-63d074744bcf?source=collection_archive---------0----------------------->

## 包含示例项目的逐步指南

![](img/9905eb6088134a199cd07517ed14daa5.png)

当你在 Azure Functions 上部署 web 应用或 API 时，你可以直接从 Azure function endpoint 公开它们，也可以通过 Azure APIM 提供服务。使用 APIM 有几个优点，如根据上下文路径路由到不同的应用程序，实现…