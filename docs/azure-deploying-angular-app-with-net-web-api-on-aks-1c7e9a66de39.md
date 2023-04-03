# azure——部署 Angular 应用程序。AKS 上的. NET Web API

> 原文：<https://medium.com/bb-tutorials-and-thoughts/azure-deploying-angular-app-with-net-web-api-on-aks-1c7e9a66de39?source=collection_archive---------0----------------------->

## 包含示例项目的逐步指南

![](img/ee4650194067a15cf834481e70c5f495.png)

在这篇文章中，我们将使用 NET web API 部署一个 Angular 应用程序。首先，我们对我们的应用程序进行 dockerize，并将该映像推送到 Azure 容器注册表，并在 Azure AKS 上运行该应用程序。我们将看到如何在 Azure AKS 上构建 Kubernetes 集群，从外部访问集群…