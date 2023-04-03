# 使用 OCI DevOps Composite 的 Terraform 计划创建建设和部署管道资源

> 原文：<https://medium.com/oracledevs/oci-devops-composite-creating-build-deploy-pipeline-resources-using-terraform-plans-7afcd6d0cae8?source=collection_archive---------2----------------------->

我最近用 Terraform 计划创建了一个 GitHub 存储库，用于创建我称之为 OCI 组合的东西——经常一起使用的资源组合，必须通过基础设施作为代码来创建。

我在这个报告中的第一个条目是几个 OCI 服务的组合:函数、容器映像和容器存储库。我在本文的[中介绍了这种复合材料。](https://technology.amis.nl/cloud/creating-building-and-invoking-a-function-on-oci-with-terraform/)