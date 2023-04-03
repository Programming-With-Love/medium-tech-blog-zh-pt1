# 编写有效的、可伸缩的、动态的、抗错误的

> 原文：<https://medium.com/capital-one-tech/terraform-poka-yokes-writing-effective-scalable-dynamic-and-error-resistant-terraform-dcbd6a0ada6a?source=collection_archive---------0----------------------->

![](img/1a3a19a329dfb837c9ee7e2ff159c3e4.png)

防错(Poka-yoke)是一个日语术语，意思是“防错”或“无意的错误 [*预防*](https://en.wiktionary.org/wiki/prevention) *”。防差错控制是在任何过程中帮助设备操作员避免错误(防差错)的任何机制。其目的是通过预防、纠正或关注发生的* [*人为错误*](https://en.wikipedia.org/wiki/Human_error) *来消除产品缺陷。——*[*维基百科*](https://en.wikipedia.org/wiki/Poka-yoke)

提供软件——任何种类的软件——作为服务需要建立许多防差错机制。作为一名高级 DevOps 工程师/SRE，我的工作是为开发人员开发和管理他们的应用程序提供简单、自动化的方法。这意味着确保:1)我们提供可扩展的东西——Capital One 不应该以一对一的关系雇佣更多的我；2)开发人员在部署他们的应用程序时有良好的体验。

不久前，我读到一篇很棒的文章，将基础设施描述为代码(IaC ),称为基础设施-应用程序模式(I-A)。马丁·阿特肯斯博客[提供了完整的六篇系列文章，用实际例子描述了一般的实现。我想扩展本文中的思想，讨论 I-A 模式的其他可能的好处和实现，并描述允许您构建防差错的规则。](https://apparently.me.uk/terraform-environment-application-pattern/overview.html)

作为一个快速的概述，I-A 模式代表了一种编写 IaC 和管理所述基础设施的方法，它抽象出共享的基础设施，并允许开发人员控制特定于应用程序的基础设施。对于我的团队来说，这意味着将 ECS 群集、ALB、安全组、R53 规则、数据库和 S3 存储桶的管理抽象到平台/SRE 团队可以管理的共享平台中。这使开发人员能够控制和负责他们的单个应用程序，包括路由规则、端口、内存/cpu、环境变量以及存储在 S3 和数据库等数据存储中的实际数据。在 Capital One，这一点对我们来说更加重要，因为为了大规模管理 AWS，我们有团队来管理 VPC、子网和其他大规模共享基础架构。因此，我们的 I-A 模式成为共享基础架构的多层金字塔，允许我们使用可扩展的共享责任模型来管理它。

与马丁·阿特肯斯的原始文章相比，我选择不使用 Consul 作为基础设施级别之间共享数据的存储。相反，我选择直接对提供者 API 进行远程数据调用。这在理论上允许使用 CloudFormation、Terraform、AWS CDK 或其他 DSL 来管理帐户基础架构，允许我的团队简单地编写 Terraform，然后最终允许开发人员使用其他东西来管理他们的应用程序。在这种情况下，我们不必依赖与 Consul 的 DSL 接口，而是可以允许 DSL 直接从 AWS 获取和过滤。在我们的案例中，应用团队还使用 Terraform 来管理他们的应用部署，这使得数据呼叫更加有用。

很像马丁·阿特肯斯，我喜欢用实际例子来补充宽泛、抽象的概念。让我们看一个单独部署和管理的示例 ECS 应用程序和群集。当我们走过的时候，我会分享一些我发现的通用规则，以便更容易地管理地形。

# 规则 1:使用 Terraform 模块将特定的基础设施抽象成逻辑分组

在 [gruntworks.io](https://www.gruntwork.io/) 的好心人( *Terragrunt、Terratest 和 Terraform 的作者:Up & Running！*)已经差不多完善了这一点。如果你在 [Github](https://github.com/gruntwork-io) 上查看他们的模块，你会看到两个不同级别的模块。

首先是一个低级的非固执己见的模块。这不一定是单个资源(尽管取决于所述资源的大小或复杂性),而是一组组合在一起的有用资源。这方面的一个例子如下:

```
resource "aws_ecs_service" "service" {
    name = "${var.service-name}"
    cluster = "${data.aws_ecs_cluster.ecs-cluster.id}"
    task_definition = "${aws_ecs_task_definition.task_definition.arn}"
    iam_role = "${var.ecs_service_role}" load_balancer {
        target_group_arn = "${aws_lb_target_group.target_group.arn}"
        container_name = "${var.service-name}"
        container_port = "${var.container-port}" } ordered_placement_strategy {
        type = "spread"
        field = "attribute:ecs.availability-zone"
    }
}resource "aws_appautoscaling_target" "service_asg" {
    max_capacity = "${var.asg_max}"
    min_capacity = "${var.asg_min}"
    resource_id = "service/${var.cluster-name}/${aws_ecs_service.service.name}"
    role_arn = "${var.ecs_service_role}"
    scalable_dimension = "ecs:service:DesiredCount"
    service_namespace = "ecs"
}resource "aws_appautoscaling_policy" "up" {
    name = "${aws_ecs_service.service.name}_scale_up"
    service_namespace = "ecs"
    resource_id = "service/${var.cluster-name}/${aws_ecs_service.service.name}"
    scalable_dimension = "ecs:service:DesiredCount" step_scaling_policy_configuration {
        adjustment_type = "ChangeInCapacity"
        cooldown = 60
        metric_aggregation_type = "Maximum"
        step_adjustment {
            metric_interval_lower_bound = 0
            scaling_adjustment = 1
        }
    } depends_on = ["aws_appautoscaling_target.service_asg"]
}resource "aws_appautoscaling_policy" "down" {
    name = "${aws_ecs_service.service.name}_scale_down"
    service_namespace = "ecs"
    resource_id = "service/${var.cluster-name}/${aws_ecs_service.service.name}"
    scalable_dimension = "ecs:service:DesiredCount"
    step_scaling_policy_configuration {
        adjustment_type = "ChangeInCapacity"
        cooldown = 60
        metric_aggregation_type = "Maximum"
        step_adjustment {
            metric_interval_lower_bound = 0
            scaling_adjustment = -1
        }
    }
    depends_on = ["aws_appautoscaling_target.service_asg"]
}/* metric used for auto scale */resource "aws_cloudwatch_metric_alarm" "service_mem_high" {
    alarm_name = "${aws_ecs_service.service.name}_memory_high"
    comparison_operator = "GreaterThanOrEqualToThreshold"
    evaluation_periods = "2"
    metric_name = "MemoryUtilization"
    namespace = "AWS/ECS"
    period = "60"
    statistic = "Maximum"
    threshold = "50"
    dimensions {
        ServiceName = "${aws_ecs_service.service.name}"
    }
    alarm_actions = ["${aws_appautoscaling_policy.up.arn}"]
    ok_actions = ["${aws_appautoscaling_policy.down.arn}"]
}
```

这个模块可以称为 ecs-service。它创建了一个 ECS 服务(使用 EC2 作为服务类型)、一个自动伸缩目标和两个指标(自动伸缩策略)。这是一个适度非自以为是的模块，可以在许多地方重用，特别是在更自以为是的模块中，这些模块基于您环境中的应用程序或基础架构创建服务的逻辑分组。

然后，您可以在特定于应用程序的另一个更加固执己见的模块中调用它。它可能包括通知的 SNS 主题或 SQS 队列。这些模块应该更加固执己见。它们应该形成以标准方法部署团队应用程序所需的基本模块的逻辑分组。

这是第一个防差错装置。 *通过要求使用这些更加自以为是的模块来创建应用程序，你可以大大有助于确保最佳实践。*例如:要求加密，或防止不良安全组，管理 IAM 角色或保护 S3 存储桶上的 ACL。显然，您也可以允许开发人员或团队添加 Terraform。然而，正确地构建这些模块应该可以防止大量的错误配置问题。

# 规则 2:使用 Terraform 数据调用来提供信息

我的科技职业生涯始于一只服务台猴子。我总是以一堆机器或服务器或电话或其他设备的列表结束，这些列表总是过时。我很快了解到 1)一致的命名和 2)使用 API 动态查找数据的价值，而不是依靠人类来收集准确的数据。

**这是第二个防错措施。不要强迫你的开发人员维护无止境的数据或变量列表。查一下就知道了。人类不是为重复性任务而生的；电脑是。利用这些优势，并在您的 terraform 中内置查找和过滤器。使用过滤器查找可接受的 ami。这里有一个例子来查找已经标记了 AmazonLinux-x64-HVM-Enc- <日期>的定制 ami。**

```
data “aws_ami” “latest” {
    most_recent = true
    owners = [
        “self”,
    ] filter {
        name = “state”
        values = [
            “available”,
        ]
    }
    filter {
        name = “tag:release”
        values = [
            “ga”,
        ]
    }
    name_regex = “AmazonLinux-x64-HVM-Enc-[0–9]{6}(-[0–9]+)?$”
}
```

不要让开发者维护集群或者 ALB 引用。去查一下。根据提供商的 API 查找所需的信息非常容易。

```
data “aws_ecs_cluster” “ecs-cluster” {
    cluster_name = “${var.cluster-name}”
}data “aws_lb” “alb” {
    name = “${var.alb-name}”
}
```

这些都是简单的例子，但这是因为神奇之处在于如何命名您的基础架构以及如何将这些名称组合在一起。

# 规则 3:对插值和拼接发生的地方要聪明

如果您遵循规则 2，您将很快意识到保持变量映射以保持跨环境的一致性是一种容易出错的方法。有人用胖手指一抄一贴，或者名字什么的。为每个环境管理不同的 Terraform 文件可能会导致更大的问题，因为变量必须在多个地方进行更改、多次复制等。相反，我们可以使用插值来动态构建变量和基础设施名称。只是不要在模块内部这样做。

如果您想要一致的命名，您可以这样做:

```
resource “aws_ecs_cluster” “cluster” {
    name = “${var.app-name}-cluster-${var.env}”
}
```

这便于查找。基于一些输入参数提供 env，将 app-name 作为用于各种命名的变量，您将很快获得一致命名的资源，并且可以通过控制台或 CLI 轻松找到这些资源。

但是，如果你完全按照我上面列出的去做，你会很快发现它没有伸缩性。因为如果你需要改变插值，那么一切都会崩溃。然后你就可以永远更新依赖你的每一个平台。

相反，当您调用模块时，请考虑这种插值(理想情况下)。

```
resource “aws_ecs_cluster” “cluster” {
    name = “${var.cluster-name}”
}module “ecs-cluster” {
   source = “<path>”
   cluster-name = “${var.app-name}-cluster-${var.env}”
}
```

现在，如果连接发生变化，您可能还有一些地方需要更新，但您不会同时破坏所有内容。

如果您需要在一个模块内连接(以提供更好的开发人员体验或要求特定的命名),请确保在您的变量中自由而明智地使用默认定义。设置一个有用的默认值，你也可以最小化你引入的破坏性变化。

# 规则 4:实现状态锁定以便于部署

这是 3 号防差错装置。 *状态锁定是防止人为错误和/或在可能出现问题时发出通知的完美例子。*

[Jessica G 有一篇关于为 Terraform](/@jessgreb01/how-to-terraform-locking-state-in-s3-2dc9a5665cb6) 使用状态锁定的很棒的文章，除了解释这样做的重要性之外，我不会在这里重复它。如果不使用状态锁定可能会有些头疼。如果没有其他方法(比如限制同时运行的 jenkins 作业的数量)来防止同时更新，状态锁定就变得更加重要了。状态锁定可以说是管理它的唯一合理的方法。所以请执行。只需 5 分钟就能实现，并能防止许多令人头疼的问题。

防差错的概念应该驱动我们做出很多决定。DevOps 是一种心态。它建立在增加控制和责任的原则上，防差错允许开发人员快速、早期地失败，并且不会破坏他们不应该破坏的东西。我们已经在 IaC 中为我们支持的六个团队实施了防错 I-A 模式一段时间了。他们在各种集群中运行大约 75 个以上的微服务，具有各种需求和流量。

他们部署了很多。非生产环境可能会在高峰日看到大约 100 次部署。在 Terraform 中构建防错功能可以提供更好的开发体验，减少停机时间和沟通失误。

我的团队可以为基础设施提供可扩展的支持，同时允许开发人员保留对其应用程序的控制。此外，允许开发人员在整个生产过程中保持对其应用程序的控制是 DevOps 的根本目的。肯定有一些额外的复杂性，但这种复杂性可以通过良好的结构和编写的 Terraform 来管理。更重要的是，这种复杂性是任何试图授予开发人员控制权的系统所固有的，其中很多代表了人的问题，而不是技术问题。技术永远不能解决人的问题，只能减轻它们。

*披露声明:2020 资本一。观点是作者个人的观点。除非本帖中另有说明，否则 Capital One 不隶属于所提及的任何公司，也不被这些公司认可。使用或展示的所有商标和其他知识产权是其各自所有者的财产。*