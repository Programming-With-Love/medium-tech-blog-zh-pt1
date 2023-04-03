# 使用 Terraform 部署多个环境

> 原文：<https://medium.com/capital-one-tech/deploying-multiple-environments-with-terraform-kubernetes-7b7f389e622?source=collection_archive---------0----------------------->

![](img/259d4eb9694126bb6eb7d58127f9d780.png)

Terraform 是提供不可变基础设施的一个很好的工具。它允许您以声明的方式以编程方式创建基础结构，同时跟踪基础结构的状态。对于更复杂的部署和更可重用的代码，必须让 Terraform 为它们服务。幸运的是，Terraform 提供了实现这一目标所需的大部分工具。在这篇文章中，我将展示如何让 Terraform 为我工作，同时成功地将基础设施部署到多个环境中。

# **背景**

当我开始这个 Kubernetes 基础设施项目时，我以前从未使用过 Terraform，尽管我对它很熟悉。 [Terraform](https://wwwa.terraform.io/) 是由 [HashiCorp](https://www.hashicorp.com/) 开发的工具，提供开源和企业版本。它是用 Go 编写的，有一个专有的 DSL 用于用户交互。

过去，我使用过许多其他工具，如 Puppet、Ansible、Foreman 和 CloudFormation，以及其他围绕各种 SDK 和库的“自己开发”的工具。通过这个项目，我想学习一些新的东西；输入 Terraform。

该项目有一些简单的要求，特别是我们需要能够将基础架构部署到以下环境中:

*   发展
*   质量保证
*   脚手架
*   生产

它还需要能够从开发人员的机器上运行，部署个人基础设施，最后，用户体验需要简单。这意味着命令行参数需要保持最小，并且不需要复杂的配置文件。

# **构建工具**

如上所述，我想为这个项目学习一个新的工具，一些我熟悉但从未亲自使用过的东西。我对自己的另一个挑战是尽可能地尝试和实施 [DRY 原则。](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself)

DRY 原则旨在减少模式和代码中的重复。坚持这一点意味着我不会为我想要部署的每个环境重复 Terraform 代码。我会编写最少的代码来实现最大的可重用性。

在对 Terraform 做了大量阅读后，我决定使用一些特性来满足需求。我的 Terraform 项目将围绕使用**远程状态**、**工作区**、**模块**、**局部变量**和**变量**的想法来构建。

## **工作区**

Terraform 工作空间是 Terraform 环境的继承者。工作区允许您分离状态和基础结构，而无需更改代码中的任何内容。这是构建我的工具的一个很好的开始，因为我希望将完全相同的代码库部署到多个环境中，而不会出现重叠。我决定所有的工作空间名称都将受到该工具的支持，并且每个工作空间都将被视为一个环境。

对于开发人员的个人基础设施，我不会根据我想出的一些任意的约定来限制命名。我决定支持开发人员想要使用的任何工作区名称，并将其视为开发环境。

## **远程状态**

在远程文件中保存地形状态是必须的。我不应该去探究原因。相信我，去做吧。我决定将我的项目状态存储在 S3，因为它设置起来既快又容易。使用远程状态文件时，有两个要点需要记住。

1.  这是 Terraform 用来计算基础设施变化的文件。如果这些信息没有保存在一个集中的位置，那么您将无法负责任地管理您的基础架构。
2.  我的每个环境都需要单独的状态文件来避免冲突。通过使用工作区，我能够将工作区名称添加到状态文件的路径前面，确保每个工作区(环境)都有自己的状态。反过来，这意味着每个环境都有自己独立的基础设施；其中也包括开发人员的个人集群。

## **当地人**

Terraform 有一个称为本地值(locals)的特性。局部变量有一个分配给表达式的名称；例如，映射查找或三元。这些值可以被认为是函数的局部变量。我确信它们有很好的用例，但是我主要把它们作为其他模块的输入。

## **变量**

就像任何工具或语言一样，Terraform 支持变量。所有典型的数据类型都被支持，这使得它们非常有用和灵活。我在 Terraform 中发现的一个好东西是输入变量的概念。输入变量本质上是 Terraform 模块的参数。这些输入变量可用于填充资源的配置输入，甚至确定其他变量的值。

在我的项目中，我决定在 Terraform 中使用独特的变量。我将制作用于确定环境和基础设施规模的变量，以及容纳所有资源配置变量的专用模块。

## **模块**

如果 Terraform 代码不能重用和模块化，就不值得使用。因此，像现在的其他配置工具一样，Terraform 支持模块。模块只不过是通用的、高度参数化的代码，可以很容易地在多个用例中重用。对于我的用例，我制作了一个如上所述的变量模块。

## **综合考虑**

我做的第一件事是为项目创建一组变量。这些变量存在于`variables.tf`文件中。我创建了一个变量，将 Terraform `workspace`名称映射到环境名称。这就创造了一套“已知环境”，允许支持开发、qa、试运行和生产，但不允许支持个人开发基础设施。

```
variable “workspace_to_environment_map” {
  type = “map” default = {
    dev     = “dev”
    qa      = “qa”
    staging = “staging”
    prod    = “prod”
  }
}
```

现在已经知道了环境，下一个变量是将环境映射到基础设施的大小。为此，我选择了旧的“t 恤尺寸”。每种大小都将映射到基础架构的不同大小的实例。例如:`small`可能是`t2.small`，`medium`可能是`t2.medium`。

```
variable “environment_to_size_map” {
  type = “map” default = {
    dev     = “small”
    qa      = “medium”
    staging = “large”
    prod    = “xlarge”
  }
}
```

既然“已知环境”得到了支持，我就需要支持开发人员的个人基础设施。为此，我创建了一个变量映射 Terraform 工作空间的大小。没有周围的环境，这没有多大意义，但相信我，它来了。

```
variable “workspace_to_size_map” {
  type = “map” default = {
    dev = “small"
  }
}
```

既然“已知环境”得到了支持，我就需要支持开发人员的个人基础设施。为此，我创建了一个变量映射 Terraform 工作空间的大小。没有周围的环境，这没有多大意义，但相信我，它来了。

```
variable “workspace_to_size_map” {
  type = “map” default = {
    dev = “small”
  }
}
```

现在我已经有了基本变量，我需要找到在代码库中使用它们的方法…进入`locals`。

我在 `main.tf`为环境和`size`创造了两个当地人。每一个都有自己的表达式来计算正确的值。

```
locals {
  environment = “${lookup(var.workspace_to_environment_map, terraform.workspace, “dev”)}”
  size = “${local.environment == “dev” ? lookup(var.workspace_to_size_map, terraform.workspace, “small”) : var.environment_to_size_map[local.environment]}”
}
```

**在继续之前，让我们把它分解一下……**

`environment`局部值是使用 Terraform `workspace`名称对`workspace_to_environment_map`的查找。如果地形`workspace`名称在地图中不存在，它将默认为环境开发。

本地值`size`是一个带有查找的三元值，并使用前述的`environment`本地值。如果`environmen` t 的本地值是 dev，那么它将使用 Terraform 工作区名称查找`workspace_to_size_map`。如果它不在地图中，它将默认为小尺寸。如果`environment`本地值不是 dev，那么它将得到`environment_to_size_map`的值为`environment`本地值的值。

就像这样，我们现在支持所有“已知环境”以及开发人员想给他们的个人基础设施命名的任何工作区名称。信不信由你，这是最难的部分。艰苦的工作已经完成，现在剩下的唯一事情就是构建变量模块。

因为我的项目的所有变量都是配置值，所以我选择将它们与项目代码完全分开。这允许在不接触实际代码库的情况下更新配置。

在`main.tf`中，我使用 GitHub URL 作为源代码声明了一个变量模块。该模块配置了两个输入，`environment`和`size`，它们是上述`locals`的值。

```
module “variables” {
  source = “git::https://github.com/project/config//variables" environment = “${local.environment}”
  size        = “${local.size}”
}
```

一旦声明，模块中的任何变量都可以通过引用为`module.variables.<variable name>`在项目中使用。由于输入被传递到模块，这允许项目动态地检索所有变量的正确值。

在变量模块代码中，有一个`inputs.tf`声明了模块的输入变量。这些输入用于查找正确的值。

```
variable “environment” {
  description = “The cluster deployment environment”
}variable “size” {
  description = “The size of the instances”
}
```

当在模块中构建变量时，我需要考虑哪些变量需要根据环境来检索，哪些变量需要根据大小来检索。

根据环境编写变量的一个例子是子网 id。

```
variable “subnet_map” {
  description = “A map from environment to a comma-delimited list of     the subnets”
  type = “map”default = {
    dev     = “subnet-c59403abe,subnet-69483bdb33c”
    qa      = “subnet-e48unjd9a1,subnet-c085uhd93a4”
    staging = “subnet-65489uuhfn9,subnet-448hjdh86b”,
    prod    = “subnet-6dfjn2344f,subnet-0f4u3bjbd47”
  }
}output “subnets” {
  value = [“${split(“,”, var.subnet_map[var.environment])}”]
}
```

基于大小编写变量的一个例子是实例类型。

```
variable “instance_type_map” {
  description = “A map from environment to the type of EC2 instance”
  type = “map” default = {
    small  = “t2.large”
    medium = “t2.xlarge”
    large  = “m4.large”
    xlarge = “m4.xlarge”
  }
}output “instance_type” {
  value = “${var.instance_type_map[var.size]}”
}
```

一旦在模块中创建了所有变量，现在就可以在 Terraform 代码中引用它们作为资源和其他模块的输入。这使得在将基础设施部署到具有不同配置和需求的不同环境时，核心代码库完全是动态的。

# **胜负**

在构建了这个设计并通过一些开发周期运行了 Kubernetes 集群之后，它看起来对于所有环境都工作得很好，很可靠。Kubernetes 集群已经在 CI/CD 环境中使用 Terraform 构建的基础架构投入生产运行了四个多月，没有出现任何问题。

也许你正在使用 Terraform，这是一个新的设计模式，你可以在你的代码库中实现。也许您正在使用另一种工具，这激发了您探索 Terraform 如何融入您的环境的兴趣。无论哪种方式，我都鼓励你去探索 Terraform 和其他人是如何使用它的，你永远不知道从长远来看，什么时候采用一种新工具会有回报。

## 相关:

*   [建筑特征切换成地形](/capital-one-tech/building-feature-toggles-into-terraform-d75806217647)
*   [使用 Terraform 进行多区域部署](/capital-one-tech/multi-region-deployments-with-terraform-kubernetes-a1f51bb96974)
*   [像对待应用程序一样对待你的 Terraform:第 1 部分——为什么 Terraform 要放在 Docker 容器中？](/capital-one-tech/treating-your-terraform-like-an-application-why-terraform-in-a-docker-container-31e802314b4)
*   像对待一个应用程序一样对待你的地形:第 2 部分——如何对接地形

*披露声明:这些观点是作者的观点。除非本帖中另有说明，否则 Capital One 不属于所提及的任何公司，也不被其认可。使用或展示的所有商标和其他知识产权都是其各自所有者的所有权。本文为 2018 首都一。*