# Kubernetes —学习初始化容器模式

> 原文：<https://medium.com/bb-tutorials-and-thoughts/kubernetes-learn-init-container-pattern-7a757742de6b?source=collection_archive---------0----------------------->

## 通过一个示例项目理解 Init 容器模式

![](img/bcd18c21372c0624b5fd9af0670ca351.png)

Photo by [Judson Moore](https://unsplash.com/@judsonlmoore?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

Kubernetes 是一个开源的容器编排引擎，用于自动化容器化应用程序的部署、伸缩和管理。pod 是 kubernetes 应用程序的基本构建块。Kubernetes 管理 pod 而不是容器，pod 封装容器。一个 pod 可能…