# Angular —如何在运行时读取 CI/CD 的环境信息

> 原文：<https://medium.com/bb-tutorials-and-thoughts/angular-how-to-read-environment-info-at-runtime-for-ci-cd-9a788478aa9b?source=collection_archive---------2----------------------->

## 如果您使用 NGINX 作为 web 服务器和 Kubernetes 进行部署

![](img/755dce5088d653bd12c72505eb911b40.png)

Photo by [Tim Gouw](https://unsplash.com/@punttim?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

Angular 在构建时提供配置选项，这意味着您需要为每个环境定义不同的环境文件，Angular 在构建项目时通过向`ng`提供`--configuration`标志来进行适当的配置…