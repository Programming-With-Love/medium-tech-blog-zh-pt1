# OCI devo PS-为 CI 构建管道和代码库

> 原文：<https://medium.com/oracledevs/oci-devops-build-pipelines-and-code-repositories-for-ci-44b9395febaa?source=collection_archive---------1----------------------->

这篇文章介绍了 OCI devo PS(2021 年 10 月 26 日发布)中的构建管道和代码库——补充了 2021 年 7 月首次推出的工件注册和部署管道，我在这篇[文章](https://lucasjellema.medium.com/oci-devops-free-automated-cloud-native-application-deployment-to-oracle-cloud-1461b3a8c08d)中讨论了它们。

构建[软件]显然是一个特定过程的清晰指示。有了明确的结果。或者是？构建通常是一个自动化的活动，有时是手动触发的，但通常是由代码库中的特定更改自动触发的。提交或(更常见的)拉…