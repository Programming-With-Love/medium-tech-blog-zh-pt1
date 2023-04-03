# 一位架构师对云架构未来发展的看法

> 原文：<https://medium.com/capital-one-tech/one-architects-view-on-what-s-next-in-cloud-architecture-412a93968071?source=collection_archive---------0----------------------->

## 2021 年云架构回顾，2022 年展望

![](img/1c24dbdf0d4dcdfaab4e88cc8517cbfa.png)

你能相信 AWS 公共云已经存在[十五](https://www.youtube.com/watch?v=WGA2P_oH5Xc)年了吗？幸运的是，对于云爱好者来说，技术创新的势头和云的可能性没有减缓的迹象。我属于云爱好者一类，因为在过去的几年里，我在[企业架构](https://www.google.com/url?q=https://www.capitalone.com/tech/software-engineering/effective-enterprise-architecture-manifesto/&sa=D&source=docs&ust=1638555862690000&usg=AOvVaw10TivMPUxITGM9wB5B0pxx)岗位上一直是云架构的实践者和倡导者。我看到组织从迁移到云发展到执行云原生开发，而这一旅程还没有结束！随着组织对云的使用不断成熟，AWS 不断提供新的进步。在本文中，我回顾了 2021 年的主要云架构主题，以及它们在 2022 年可能会如何演变。

# 无服务器—持续扩展，与容器互补

自从 2014 年[无服务器咆哮着登上云舞台，无服务器的采用](https://www.datadoghq.com/state-of-serverless/)一直在上升。无服务器降低了管理基础设施和集成的复杂性，允许团队专注于业务逻辑并快速添加新功能。以前，这种好处在某种程度上是通过集装箱运输实现的。无服务器并不意味着容器正在消失；容器作为[无服务器计算选项](https://www.google.com/url?q=https://www.capitalone.com/tech/cloud/3-considerations-for-containers--serverless-compute-options/&sa=D&source=docs&ust=1638555744683000&usg=AOvVaw2O_RhdmzwmsgsAL8sKd9d4)的打包机制非常重要。例如，AWS Lambda 支持容器作为一种打包机制，AWS Fargate 依赖它们来集成 AWS ECS 和 AWS EKS。集装箱的持续使用是巨大的，因为许多公司已经投资于集装箱供应链。

在 2021 年 Re:Invent 上，AWS 宣布了一些服务的无服务器预览，如[亚马逊 MSK、](https://aws.amazon.com/about-aws/whats-new/2021/11/amazon-msk-serverless-public-preview/) [亚马逊红移](https://aws.amazon.com/about-aws/whats-new/2021/11/amazon-redshift-serverless/)和[亚马逊 EMR](https://aws.amazon.com/about-aws/whats-new/2021/11/amazon-emr-serverless-preview/) 。我预计这一趋势将会继续，更多的服务将会提供无服务器产品。对于基于云的架构来说，这是一个好消息，这些架构超越了典型的 web 应用程序或微服务，可以利用更广泛的 AWS 产品。现在他们也可以实现完全无服务器的架构。

此外， [AWS Step Functions](https://aws.amazon.com/about-aws/whats-new/2021/09/aws-step-functions-200-aws-sdk-integration/) 等服务宣布了更多与其他 AWS 服务的原生集成。这减少了为事件驱动集成维护定制解决方案的需要。有了这样的无缝集成，我希望无服务器事件驱动架构将在更多领域扎根，比如机器学习。

# DevOps —将重心转移到代码基础设施上

对我来说，为云设计健壮的架构是自动化的同义词。DevOps 强调通过代码为、[持续集成(CI)](https://aws.amazon.com/devops/continuous-integration/) 和[持续交付(CD)](https://aws.amazon.com/devops/continuous-delivery/) 的[基础设施实现自动化，以实现更快、更高质量、更安全的发布。](https://docs.aws.amazon.com/whitepapers/latest/introduction-devops-aws/infrastructure-as-code.html)

2021 年，我们看到了这一领域的进一步发展，从用于代码自动化基础设施的[亚马逊的 CDK](https://aws.amazon.com/about-aws/whats-new/2021/12/aws-cloud-development-kit-cdk-generally-available/) 和 [SAM pipelines](https://aws.amazon.com/about-aws/whats-new/2021/11/ci-cd-configuration-aws-serverless-applications-system-general-availability/) ，到 [AWS Proton](https://aws.amazon.com/about-aws/whats-new/2021/06/aws-announces-the-general-availability-of-aws-proton/) 中基于 ML 的 DevOps。

能够自信而快速地释放的重要性是不可低估的。这是实现敏捷性的关键。我预计在 2022 年及以后，我们将在这一领域看到更多进步，因为我们将合规性左移(如钩子)，通过区分 CI 和 CD 来简化管道，并进行扩展以处理更大程度的应用程序复杂性。

# 弹性—增强数据持久性、混沌工程和基于单元的架构

云的一个关键原则是可扩展性和可用性。此外，AWS 已经认识到数据持久性是稳定性等式的重要组成部分，因为故障事件中的数据恢复是可靠性的关键。作为应用程序构建和部署的一部分，测试故障的能力对于确保应用程序能够从故障中恢复也是至关重要的。

2021 年，AWS 继续为其服务发布[SLA](https://aws.amazon.com/legal/service-level-agreements/?aws-sla-cards.sort-by=item.additionalFields.serviceNameLower&aws-sla-cards.sort-order=asc&awsf.tech-category-filter=*all)，并扩展 AWS 备份等数据持久性能力(如[文件系统备份](https://aws.amazon.com/about-aws/whats-new/2021/04/amazon-fsx-aws-backup-announce-support-copying-file-system-backups-across-regions-accounts/)、 [RDS](https://aws.amazon.com/about-aws/whats-new/2021/03/aws-backup-adds-support-for-continuous-backup-and-point-in-time-recovery-of-amazon-rds-instances/) )。他们还通过选择服务上的[故障注入模拟器](https://aws.amazon.com/about-aws/whats-new/2021/03/aws-announces-service-aws-fault-injection-simulator/)涉足混沌工程。混沌工程是弹性测试的一种机制，这是[将云和 DevOps 配对以提高弹性的关键要素](https://www.capitalone.com/tech/cloud/4-steps-for-pairing-the-cloud-and-devops-to-improve-resiliency/)。我预计在 2022 年及以后，支持数据持久性和混沌工程的技术将会继续进步。

此外，我相信 AWS 在 2018 re:invent 中分享的[基于单元的架构](https://www.youtube.com/watch?v=swQbA4zub20)，将是未来云架构必不可少的一部分。基于单元的架构是将故障域概念应用于应用程序的自然发展；与断层域是一个地带或区域不同，基于单元的体系结构可以不知道这两者。我预计 AWS 将继续提供改进，使基于单元的架构更容易采用(例如 [Amazon Route53 应用恢复控制器](https://aws.amazon.com/blogs/aws/amazon-route-53-application-recovery-controller/))。

# 优化—实现最佳工作负载性能和成本

随着越来越多的组织成功进入云计算，问题出现了“现在怎么办？”一个词:*优化*。

从 [CloudWatch Insights](https://aws.amazon.com/about-aws/whats-new/2021/11/amazon-cloudwatch-metrics-insights-preview/) 到[成本管理资源](https://aws.amazon.com/aws-cost-management/)，AWS 已经趋向于提供更加成熟的见解来实现工作负载优化。这些功能对于实现性能和[云成本优化](https://www.capitalone.com/tech/cloud/cloud-cost-optimization/)至关重要。说到成本，AWS 还提供了更多的功能，允许灵活的成本控制；例如，[弹性搜索](https://aws.amazon.com/about-aws/whats-new/2021/05/amazon-elasticsearch-service-announces-a-new-lower-cost-storage-tier/)和 [AWS S3](https://aws.amazon.com/about-aws/whats-new/2021/11/s3-intelligent-tiering-archive-instant-access-tier/) 等服务的分层存储。

我预计我们将继续看到服务的成本杠杆变得更加透明和可配置，以使交付团队能够优化他们的工作负载。也就是说，良好的应用程序设计仍然是至关重要的——领域驱动的架构、微服务、CAP 定理等都需要得到很好的理解，并整合到有效的工作负载设计和实施中。

# 2022 年将延续 2021 年观察到的云动力

无服务器和开发运维有助于实现弹性等关键架构特性。优化正走向前沿，因为仅仅在云中是不够有利的；这都是关于在云中**和**做好它。15 年过去了，对于云架构师来说，这仍然是一个激动人心的时刻。2021 年带来了该领域的一些巨大进步，我期待在 2022 年看到更多进步。

*披露声明:2022 首都一号。观点是作者个人的观点。除非本帖中另有说明，否则 Capital One 不隶属于所提及的任何公司，也不被这些公司认可。使用或展示的所有商标和其他知识产权是其各自所有者的财产。*

【https://www.capitalone.com】最初发表于[](https://www.capitalone.com/tech/cloud/whats-new-in-cloud-architecture-for-2022/)**。**