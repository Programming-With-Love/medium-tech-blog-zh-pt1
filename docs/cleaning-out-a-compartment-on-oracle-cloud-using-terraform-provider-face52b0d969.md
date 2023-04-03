# 使用 Terraform provider 清理 Oracle 云上的隔离专区

> 原文：<https://medium.com/oracledevs/cleaning-out-a-compartment-on-oracle-cloud-using-terraform-provider-face52b0d969?source=collection_archive---------0----------------------->

*情况*:OCI 上的一个隔间要拆了。或者至少应该清除其所有资源。或者至少应该移除大部分资源。

*挑战*:没有可以删除隔离专区中资源的“清除隔离专区”。逐个删除每个资源的工作量相当大；需要很长时间，而且非常非常枯燥。

*解决方案*:使用 Terraform OCI 提供商发现资源，然后使用 Terraform 摧毁所有资源，使用…