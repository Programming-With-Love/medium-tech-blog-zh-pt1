# Kubernetes —学习边车集装箱模式

> 原文：<https://medium.com/bb-tutorials-and-thoughts/kubernetes-learn-sidecar-container-pattern-6d8c21f873d?source=collection_archive---------0----------------------->

## 通过一个实例项目理解边车集装箱模式

![](img/730b5c5b9d02338e4f25fba5ef7cb29b.png)

Photo by [hidde schalm](https://unsplash.com/@hdsfotografie95?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

Kubernetes 是一个开源的容器编排引擎，用于自动化容器化应用程序的部署、伸缩和管理。pod 是 kubernetes 应用程序的基本构建块。Kubernetes 管理 pod 而不是容器，pod 封装容器。一个…