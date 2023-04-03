# Oracle 云基础设施 DevOps 功能部署管道—Oracle 云上的自动化

> 原文：<https://medium.com/oracledevs/oci-devops-deployment-pipeline-for-functions-automation-on-oracle-cloud-6edfd2ffe32b?source=collection_archive---------0----------------------->

Oracle 云基础设施(OCI)有一个免费的 DevOps 云服务，除了源代码和工件存储库之外，还包括构建和部署管道。

我在之前的文章中已经写过关于 [OCI DevOps 部署管道](/oracledevs/oci-devops-free-automated-cloud-native-application-deployment-to-oracle-cloud-1461b3a8c08d)和它的[构建管道](/oracledevs/oci-devops-build-pipelines-and-code-repositories-for-ci-44b9395febaa)。

在本文中，我将向您展示 OCI DevOps 上一个函数的部署管道。OCI 的无服务器函数服务使用在 OCI 打包成容器映像的函数定义…