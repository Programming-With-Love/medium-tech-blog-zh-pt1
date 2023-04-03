# 升级到 Terraform 0.12 时的经验教训

> 原文：<https://medium.com/oracledevs/lessons-learned-when-upgrading-to-terraform-0-12-6d894d3ab20e?source=collection_archive---------0----------------------->

Terraform 0.12 [最近发布](https://www.hashicorp.com/blog/announcing-terraform-0-12)，是一次重大更新，提供了大量改进和功能。

虽然你可能想赶紧使用新功能，但我会带你浏览一下我们在升级 [terraform-oci-oke](https://github.com/oracle-terraform-modules/terraform-oci-oke) 项目时学到的一些经验。

这绝不是一份详尽的清单。随着我们理解和使用越来越多的 0.12 新特性，我将对此进行更新。

1.  [阅读博客系列](/@lmukadam/lessons-learned-when-upgrading-to-terraform-0-12-6d894d3ab20e#aa41)
2.  [先修复中断更改](/@lmukadam/lessons-learned-when-upgrading-to-terraform-0-12-6d894d3ab20e#7a42)