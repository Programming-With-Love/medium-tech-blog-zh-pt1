# Terraform 骨骼简介

> 原文：<https://medium.com/globant/introduction-to-terraform-skeleton-dc6858302756?source=collection_archive---------0----------------------->

一次又一次，我不得不面对同一个问题:“我应该如何减少我的 ***terraform*** 代码以充分利用它？”。

无论你是一名新手还是一名经验丰富的 CloudOps 工程师，你都会立即同意，拥有一种方法或者更确切地说是一种方法论是缓解学习曲线以及加快游戏速度的关键。

在这篇文章中，我展示了一种方法和一个模板:创建、管理和扩展你的 ***terraform*** 项目。

都在模块里: ***terraform*** 模块是处理 IaC 时的基本积木。虽然还有其他感兴趣的领域，如 ***环境*** 和 ***组件*** 。

举例来说，假设我们想要管理一个名为 ***acme*** 的组织，我们需要应对四个不同的组成部分:

*   ***iam*** :保存角色和服务账户管理的信息和定义。例如，RACI 矩阵的实现。
*   ***计算*** :主机定义虚拟机和计算能力相关的任务。
*   **联网**:支持网络定义、子网以及防火墙。
*   **存储**:控制存储相关任务的定义和行为。

为了创建一个空的文件夹结构，我们提供了一个[***terra former . py***](https://github.com/globant/gcp-sandbox/blob/wip_terraformer/terraformer/terraformer.py)脚本，当按如下方式执行时，它会自动填充这个结构:

```
**#python terraformer.py --project acme --subsystems compute iam networking storage --environments dev prod****#tree acme**
acme/
├── compute
│   ├── environments
│   │   ├── dev
│   │   │   ├── main.tf
│   │   │   ├── outputs.tf
│   │   │   ├── terraform.tfvars
│   │   │   └── variables.tf
│   │   └── prod
│   │       ├── main.tf
│   │       ├── outputs.tf
│   │       ├── terraform.tfvars
│   │       └── variables.tf
│   ├── modules
│   │   └── README.md
│   └── shared
│       └── remotes.tf
├── iam
│   ├── environments
│   │   ├── dev
│   │   │   ├── main.tf
│   │   │   ├── outputs.tf
│   │   │   ├── terraform.tfvars
│   │   │   └── variables.tf
│   │   └── prod
│   │       ├── main.tf
│   │       ├── outputs.tf
│   │       ├── terraform.tfvars
│   │       └── variables.tf
│   ├── modules
│   │   └── README.md
│   └── shared
│       └── remotes.tf
├── modules
│   └── README.md
├── networking
│   ├── environments
│   │   ├── dev
│   │   │   ├── main.tf
│   │   │   ├── outputs.tf
│   │   │   ├── terraform.tfvars
│   │   │   └── variables.tf
│   │   └── prod
│   │       ├── main.tf
│   │       ├── outputs.tf
│   │       ├── terraform.tfvars
│   │       └── variables.tf
│   ├── modules
│   │   └── README.md
│   └── shared
│       └── remotes.tf
└── storage
    ├── environments
    │   ├── dev
    │   │   ├── main.tf
    │   │   ├── outputs.tf
    │   │   ├── terraform.tfvars
    │   │   └── variables.tf
    │   └── prod
    │       ├── main.tf
    │       ├── outputs.tf
    │       ├── terraform.tfvars
    │       └── variables.tf
    ├── modules
    │   └── README.md
    └── shared
        └── remotes.tf
```

自动化结构背后的思想是最大化组件的可重用性，同时保持环境和组件的通用性。

*   **acme/modules** :托管项目范围的 ***terraform*** 模块，以便全面重用。
*   **acme/{ component }/modules**:容纳 ***terraform*** 组件级模块。
*   **acme/{ component }/{ environment }**:包含任何给定环境的此类模块的定义和实例化。

我们有意在我们的[存储库代码](https://github.com/globant/gcp-sandbox/tree/wip_terraformer/terraformer/acme)下留了一个例子，以显示一个基本的基础设施，包含一个 VPC、一个子网、一个服务帐户、一个桶以及一个基于 debian 的 VM。

对于外部模块，有几种方法来填充它们:

*   将它们托管在一个单独的 git 存储库中。这将允许您将指向您现有存储库的 ***acme/modules*** 文件夹初始化为 ***git 子模块*** 。
*   将它们托管在共享文件系统中。允许你将 ***symlink*** 你的 ***acme/modules*** 文件夹给它。
*   将它们作为一个更大项目的一部分托管在一个 ***git 存储库*** (我们的例子)中。以这种方式，您可以通过从外部来源提取模块来填充 ***acme*** 的模块。要实现这一点，您可以使用 git 资源库(在我们的例子中是 GitHub [分支](https://github.com/globant/gcp-sandbox/tree/wip_terraformer/terraform-modules))支持的 ***svn*** 原语( ***svn ls*** 和 ***svn export*** )。

如果你查看 GitHub，你可以通过使用下面的代码片段(结合了 ***svn ls*** 和 ***svn export*** )来检查 ***acme*** 的模块是从我们的 ***git 库*** 中提取的。

```
#cd acme/modules
#for dir in $(**svn ls** [https://github.com/globant/gcp-sandbox/branches/wip_terraformer/terraform-modules/](https://github.com/globant/gcp-sandbox/branches/wip_terraformer/terraform-modules/)); do **svn export** [https://github.com/globant/gcp-sandbox/branches/wip_terraformer/terraform-modules/$dir](https://github.com/globant/gcp-sandbox/branches/wip_terraformer/terraform-modules/$dir); done
```

acme 项目的完整展示如下:

```
acme/
├── compute
│   ├── environments
│   │   ├── dev
│   │   │   ├── main.tf
│   │   │   ├── outputs.tf
│   │   │   ├── remotes.tf
│   │   │   ├── terraform.tfvars
│   │   │   └── variables.tf
│   │   └── prod
│   │       ├── main.tf
│   │       ├── outputs.tf
│   │       ├── remotes.tf
│   │       ├── terraform.tfvars
│   │       └── variables.tf
│   ├── modules
│   │   └── README.md
│   └── shared
│       └── remotes.tf
├── iam
│   ├── environments
│   │   ├── dev
│   │   │   ├── main.tf
│   │   │   ├── outputs.tf
│   │   │   ├── terraform.tfvars
│   │   │   └── variables.tf
│   │   └── prod
│   │       ├── main.tf
│   │       ├── outputs.tf
│   │       ├── terraform.tfvars
│   │       └── variables.tf
│   ├── modules
│   │   └── README.md
│   └── shared
│       └── remotes.tf
├── modules
│   ├── README.md
│   ├── gcs_bucket
│   │   ├── main.tf
│   │   ├── outputs.tf
│   │   └── variables.tf
│   ├── gcs_compute
│   │   ├── main.tf
│   │   ├── outputs.tf
│   │   └── variables.tf
│   ├── gcs_service_account
│   │   ├── main.tf
│   │   ├── outputs.tf
│   │   └── variables.tf
│   ├── gcs_subnet
│   │   ├── main.tf
│   │   ├── outputs.tf
│   │   └── variables.tf
│   └── gcs_vpc
│       ├── main.tf
│       ├── outputs.tf
│       └── variables.tf
├── networking
│   ├── environments
│   │   ├── dev
│   │   │   ├── main.tf
│   │   │   ├── output.tf
│   │   │   ├── terraform.tfvars
│   │   │   └── variables.tf
│   │   └── prod
│   │       ├── main.tf
│   │       ├── output.tf
│   │       ├── terraform.tfvars
│   │       └── variables.tf
│   ├── modules
│   │   └── README.md
│   └── shared
│       └── remotes.tf
└── storage
    ├── environments
    │   ├── dev
    │   │   ├── main.tf
    │   │   ├── output.tf
    │   │   ├── terraform.tfvars
    │   │   └── variables.tf
    │   └── prod
    │       ├── main.tf
    │       ├── output.tf
    │       ├── terraform.tfvars
    │       └── variables.tf
    ├── modules
    │   └── README.md
    └── shared
        └── remotes.tf
```

当您深入研究任何一个***acme/{ component }/{ environment }/main . TF***时，您可以看到它保存了现有模块的实例化信息，当与***terraform . TF vars***一起使用时，它可以非常灵活。

```
...
provider "google" {
...
}
terraform {
  backend "gcs" {
  ...
  }
}
module "default_vm" {
  **source             = "../../../modules/gcs_compute"**
  ...
}
```

**结论**

总之，我们展示了一种初始化新的或未来的 terraform 项目的方法，同时为重构现有项目腾出了空间。利用通用 terraform 模块结构是跨多个组织甚至多个云提供商实现可重用性的关键。