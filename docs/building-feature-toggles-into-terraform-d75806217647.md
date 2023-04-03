# 建筑特征切换成地形

> 原文：<https://medium.com/capital-one-tech/building-feature-toggles-into-terraform-d75806217647?source=collection_archive---------0----------------------->

![](img/ae64b10366b8f00cf5c739f305061753.png)

正如你从我之前的两篇文章中所知道的，分别是[使用 Terraform 部署多个环境](/capital-one-tech/deploying-multiple-environments-with-terraform-kubernetes-7b7f389e622)和[使用 Terraform 进行多区域部署](/capital-one-tech/multi-region-deployments-with-terraform-kubernetes-a1f51bb96974)，我们项目的最终目标是能够使用 Terraform 将 Kubernetes 集群部署到任何环境和/或具有单一代码库的区域。到目前为止，在这个系列中，我们已经成功地解决了部署到多个环境和多个地区的问题。随着我们旅程的继续，我们会遇到更复杂的挑战； ***并非所有环境都需要相同的特性。***

输入特征切换…

# 什么需要拨动开关？

传统上，功能切换已经在软件开发中用于打开和关闭软件功能。因为我们正在部署一个 Kubernetes 集群并为我们的用户运行一个平台，所以我们的特性切换有一个稍微不同的用例。我们维护着一套与每个 Kubernetes 集群一起部署的掌舵图。然而，并非我们支持的每个环境都需要所有这些舵图。因此，根据我们部署 Kubernetes 的位置，我们希望能够部署或不部署给定的舵图。

除了掌舵图之外，我们还有一些额外的基础架构(更像是一种特殊的使用情形)需要部署到一些选定的环境中。因为我们部署的大多数 Kubernetes 集群都不需要这个基础设施，所以我们希望能够部署或不部署给定的基础设施。

# Terraform 计数参数

正如您可能从标题中猜到的那样，从 0.11.x 开始，Terraform 不具备围绕特定资源或资源组编写切换的能力。Terraform 附带了一个可以在所有资源上使用的`count`参数。`count`参数告诉 Terraform 要构建多少个资源；是的，零被接受，在这种情况下，它不会建立任何东西。作为第一步，您可能决定只添加`*count*`参数，并根据需要在`0`和`*1*`之间翻转值。

***这样不会创建 S3 桶对象:***

```
*resource “aws_s3_bucket_object” “some_s3_objct” {
 count = 0
 bucket = “${aws_s3_bucket.bucket.id}”
 key = “some_s3_object_path/some_s3_object”
 content = “This is an S3 object”
 server_side_encryption = “AES256”
}*
```

***这将创建 S3 桶对象:***

```
*resource “aws_s3_bucket_object” “some_s3_objct” {
 count                  = 1
 bucket                 = “${aws_s3_bucket.bucket.id}”
 key                    = “some_s3_object_path/some_s3_object”
 content                = “This is an S3 object”
 server_side_encryption = “AES256”
}*
```

通过简单地添加`*count*`参数，我们可以得到我们想要的行为，但是我们没有得到功能。为了让这种模式工作，每次资源的存在发生变化时，都需要对代码进行修改。幸运的是，`*count*`参数允许插值，这为决定是否应该创建资源提供了一些更健壮的选项。

# 插值计数参数

通过内插`*count*`参数，你可以潜在地将该值链接到你的代码库中其他变量的值；`*environment*`比如。这将允许您基于您正在构建的环境来动态地决定是否构建资源。这里需要注意的是，插值比普通的字符串插值稍微复杂一些，不适合多种使用情况。为了让这个模式工作，你必须实现一个`*ternary*`来做决定。

***如果 var.environment 等于 some_env:*** 这将创建 S3 桶对象

```
*resource “aws_s3_bucket_object” “some_s3_objct” {
 count                  = “${var.environment == “some_env” ? 1 : 0}”
 bucket                 = “${aws_s3_bucket.bucket.id}”
 key                    = “some_s3_object_path/some_s3_object”
 content                = “This is an S3 object”
 server_side_encryption = “AES256”
}*
```

这种模式似乎给了我们想要的行为和功能，允许我们在不改变代码的情况下动态地创建或不创建 S3 桶对象——但前提是逻辑不太复杂。例如，如果需要在多个环境中创建`*some_s3_object*` ，或者如果不止一个变量有特定的值，那么这个`*ternary*`仍然可以完成工作，但是不干净。

# 构建切换

既然我们已经看到了更简单的实现所面临的限制，我们可以专注于构建一个由输入外部管理的完整的特性切换。`*count*`参数和`*ternary*`仍然会被使用，但是我们添加了一个输入变量和一个可选的输出。输入变量本质上只是一个接受“真”或“假”字符串的“使能”变量。输出是一个可选的资源，它向用户提供信息，告诉用户是否已经创建了什么。我们可以保持单个资源的代码基本相同，但是我们确实需要在它周围添加一些额外的代码。

```
*variable “create_s3_object” {
 description = “Whether or not to create the S3 object”
 value = “true”
}**output “s3_object_created” {
 value = ${var.create_s3_object == “true” ? “S3 object created” : “S3 object not created”}
}**resource “aws_s3_bucket_object” “some_s3_objct” {
 count                 = “${var.create_s3_object == “true” ? 1 : 0}”
 bucket                 = “${aws_s3_bucket.bucket.id}”
 key                    = “some_s3_object_path/some_s3_object”
 content                = “This is an S3 object”
 server_side_encryption = “AES256”
}*
```

上面的代码块现在有了为一个资源创建一个特性切换所必需的两个东西，以及一个选项输出，为用户提供一些关于所部署内容的上下文。输入变量有一个缺省值，在这个例子中是“true ”,但是如果需要的话可以设置为“false”。

# 使用切换

现在，我们已经为 Terraform 构建了一个合适的功能切换，我们可以在任何环境中使用这个新功能作为我们部署的一部分。即使输入变量有默认值，我们也可以根据需要轻松地覆盖它，而不必更改底层代码。我们可以直接在命令行或者通过使用一个`*auto.tfvars*`文件将输入变量及其值传递给 Terraform。

根据您的使用情况，这两种方式都可能是首选的，重要的是这种模式非常适合本地部署，以及通过 CI/CD 工具的自动化部署。

以下示例说明了如何在命令行将输入变量传递给 Terraform。在我们的简单例子中，这似乎是合理的；然而，随着切换次数的增加，`*-var*`参数的数量也会增加。

```
*terraform apply -var create_s3_object=true*
```

创建一个`*auto.tfvars*`文件非常简单。您可以随意命名它(在本例中为`*toggles.auto.tfvars*`)，Terraform 会在运行时自动读入它。内容也非常简单，因为它只是一个键/值对的列表，其中键是变量名，值显然是您想要传递的值。

```
*# toggles.auto.tfvars
create_s3_object = false*
```

# 判决

使用这种模式，我的团队目前正在跨九个环境和两个地区向我们的 Kubernetes 集群部署 31 个独立的特性切换。个人平台开发人员能够根据本地开发的需要打开和关闭特性，我们的 CI/CD 充分利用渲染的`*auto.tfvars*`文件来管理部署的切换。

然而，这并不是我们旅程的终点；只是第一站。未来的开发将实现依赖切换，这将允许我们为我们的平台提供的所有功能创建一个依赖关系树，以及关闭以前打开的功能的能力。

# 相关:

*   [使用 Terraform 部署多个环境](/capital-one-tech/deploying-multiple-environments-with-terraform-kubernetes-7b7f389e622)
*   [使用 Terraform 进行多区域部署](/capital-one-tech/multi-region-deployments-with-terraform-kubernetes-a1f51bb96974)
*   像对待应用程序一样对待 Terraform:第 1 部分——为什么要在 Docker 容器中使用 Terraform？
*   [像对待应用程序一样对待地形:第 2 部分——如何将地形码头化](/capital-one-tech/treating-your-terraform-like-an-application-how-to-dockerize-terraform-5d7edac741fc)

*披露声明:这些观点是作者的观点。除非本帖中另有说明，否则 Capital One 不属于所提及的任何公司，也不被其认可。使用或展示的所有商标和其他知识产权均为其各自所有者所有。本文为 2019 首都一。*