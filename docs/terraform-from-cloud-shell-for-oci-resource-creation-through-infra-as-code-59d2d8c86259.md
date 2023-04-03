# 来自云壳的 Terraform，用于通过基础架构代码创建 OCI 资源

> 原文：<https://medium.com/oracledevs/terraform-from-cloud-shell-for-oci-resource-creation-through-infra-as-code-59d2d8c86259?source=collection_archive---------1----------------------->

理想情况下，在 Oracle 云基础设施上创建云资源就像使用 Terraform(计划)的代码一样。无需手触摸控制台，无需向个人用户授予权限，所有操作都可以预览、测试和轻松重复。OCI 对 Terraform 有很深的支持——从创建、更新和删除基于 Terraform 资源的资源(通过 Terraform 的 OCI 提供商)到创建、管理和执行堆栈(基于 Terraform 的 OCI 资源的预定义集合…