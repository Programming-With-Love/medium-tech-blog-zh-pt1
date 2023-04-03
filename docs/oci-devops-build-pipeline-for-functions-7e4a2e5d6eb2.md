# OCI 开发公司为职能部门建立管道

> 原文：<https://medium.com/oracledevs/oci-devops-build-pipeline-for-functions-7e4a2e5d6eb2?source=collection_archive---------1----------------------->

Oracle Cloud Infrastructure 提供了 Functions 服务，这是一种具有无服务器功能的 FaaS 产品，基于 FDK Fn 项目构建的容器映像。在之前的一篇文章中，我介绍了 OCI DevOps 的函数部署管道——这是 OCI 上的一项免费服务，它可以从基础容器映像中自动部署函数。部署管道实际上应该与构建管道结合使用，构建管道获取开发人员编写的源代码，将其转换为容器映像，将该映像存储在 OCI 容器映像注册表中，然后触发…