# GCP——在 GKE 自动驾驶仪上部署带有 NodeJS 后端的 React 应用

> 原文：<https://medium.com/bb-tutorials-and-thoughts/gcp-deploying-react-app-with-nodejs-backend-on-gke-autopilot-f14af242d1a7?source=collection_archive---------0----------------------->

## 包含示例项目的逐步指南

![](img/d9ef42b57976b6d385c691625274dd3a.png)

在这篇文章中，我们将使用 NodeJS 后端部署一个 React 应用程序。首先，我们对我们的应用程序进行 dockerize，并将该图像推送到谷歌容器注册表，并在谷歌 GKE 自动驾驶仪上运行该应用程序。我们将看到如何在谷歌 GKE 上建立 Kubernetes 集群，访问…