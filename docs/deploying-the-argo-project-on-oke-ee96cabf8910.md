# 在 OKE 上部署阿尔戈项目

> 原文：<https://medium.com/oracledevs/deploying-the-argo-project-on-oke-ee96cabf8910?source=collection_archive---------3----------------------->

![](img/4ee556dfb6d88b4394eebd3afff43d4a.png)

Image source: [https://github.com/cncf/artwork/blob/master/projects/argo/icon/color/argo-icon-color.png](https://github.com/cncf/artwork/blob/master/projects/argo/icon/color/argo-icon-color.png)

我非常激动地得知， [Argo 项目](https://argoproj.github.io/)最近被[接受为 CNCF 的孵化器级项目](https://www.cncf.io/blog/2020/04/07/toc-welcomes-argo-into-the-cncf-incubator/)。

简而言之，阿尔戈项目有 4 个主要组成部分:

*   [Argo Workflows](https://argoproj.github.io/projects/argo/) :在 Kubernetes 上编排并行作业的本地工作流引擎
*   Argo CD :用于 Kubernetes 的声明式 GitOps 连续交付工具