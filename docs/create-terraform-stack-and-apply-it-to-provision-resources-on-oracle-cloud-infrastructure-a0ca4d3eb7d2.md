# 创建 Terraform 堆栈，并将其用于在 Oracle 云基础架构上调配资源

> 原文：<https://medium.com/oracledevs/create-terraform-stack-and-apply-it-to-provision-resources-on-oracle-cloud-infrastructure-a0ca4d3eb7d2?source=collection_archive---------0----------------------->

Terraform 是在 Oracle 云基础设施(IaC)上自动供应云资源的首选工具。在最近的两篇文章中，我展示了[如何创建一个由 Terraform 的 OCI 提供者](https://technology.amis.nl/oracle-cloud/terraform-from-cloud-shell-for-oci-resource-creation-through-infra-as-code/)处理的带有资源定义的计划，以及[如何(重新)使用一个 Terraform 模块](https://technology.amis.nl/oracle-cloud/terraform-modules-for-each-file-set-on-oracle-cloud/)来允许根据动态定义的参数创建多个资源。两篇文章都展示了 Terraform 在云外壳环境中的应用——手动启动自动配置流程。