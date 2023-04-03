# 如何通过 Minikube 使用自己的本地 Docker 图像

> 原文：<https://medium.com/bb-tutorials-and-thoughts/how-to-use-own-local-doker-images-with-minikube-2c1ed0b0968?source=collection_archive---------1----------------------->

## 包含示例项目的逐步指南

![](img/381855c7e84b75b06fc4a8e88be52624.png)

Photo by [Clem Onojeghuo](https://unsplash.com/@clemono2?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

我们在本地机器上使用 minikube 时经常遇到这个问题，因为 minikube 无法找到本地 docker 图像。当我们安装 minikube 时，它在自己的虚拟机上运行，当我们运行部署时，它总是从公共或私有 docker 注册表中提取 Docker 映像。