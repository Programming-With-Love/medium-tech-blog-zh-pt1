# 通过 AWS 认证安全专业考试

> 原文：<https://medium.com/capital-one-tech/passing-the-aws-certified-security-specialty-exam-6ff4c4ae3b8f?source=collection_archive---------4----------------------->

## 刚刚通过 AWS 安全专业考试的人提供的通过该考试的指南和提示

![](img/81b61e1ac53a7b1e0cd29bc5f646224e.png)

[AWS 安全专业认证](https://aws.amazon.com/certification/certified-security-specialty/)位于当今最热门的两个技术趋势的交汇点——云与安全。如果您在安全领域工作，那么将您的安全知识应用到云中是扩展您的专业知识的合乎逻辑的下一步。如果您像我一样，更多地从事云架构和开发方面的工作，那么理解云安全可以帮助您设计更安全的系统。从一开始就将安全性构建到应用程序中，比在应用程序构建完成后尝试改进安全性要容易得多。

在我们开始之前，让我给你一些我的背景，让你了解我对考试的看法，这可能与你自己的不同。我是第一资本公司的首席软件工程师。虽然安全性是我们构建的每个系统的一部分，但我的大部分精力都花在了应用程序开发和为我们的应用程序设计 AWS 资源上。我有 CISSP、CEH 和 GIAC GWEB 认证的安全背景，多年来从事从网络和防火墙配置到网络安全政策的各种安全工作。

# AWS 认证安全-专业考试

亚马逊[建议](https://aws.amazon.com/certification/certified-security-specialty/)在参加 AWS 认证安全专业考试之前，有五年的 IT 安全经验和至少两年的 AWS 安全实践经验。如果你参加过任何其他的 AWS 考试，你可能会惊讶地发现专业考试稍微贵一些，要 300 美元。考试分为五个领域:事件响应、日志记录和监控、基础架构安全、身份和访问管理以及数据保护。这些领域的权重不同，有些领域比其他领域有更多的问题。下表显示了 AWS 安全专业考试中每个安全领域的问题百分比。下一节将详细介绍这些领域。

```
╔════════════════════════════════╦══════════════════╗
║        Security Domain         ║ Number Questions ║
╠════════════════════════════════╬══════════════════╣
║ Incident Response              ║ 12%              ║
║ Logging and Monitoring         ║ 20%              ║
║ Infrastructure Security        ║ 26%              ║
║ Identity and Access Management ║ 20%              ║
║ Data Protection                ║ 22%              ║
╚════════════════════════════════╩══════════════════╝
```

此外，尽管有与助理考试相同的 65 个问题，但这些考试的时间是 170 分钟，而助理考试是 130 分钟。额外的时间是必要的，因为问题通常更复杂，在我看来，比助理考试的问题更难。我在规定的时间内完成了助理考试，但完成和复习 AWS 安全专业考试的问题几乎花去了全部分配的时间。

像所有的 AWS 考试一样，问题是基于场景的。虽然其他考试可能侧重于解决问题的最具成本效益的方式，但安全考试将为您提供侧重于解决问题的安全方式的问题。解决一个问题的答案可能不止一个，但有些答案可能不安全。重要的是，不仅要理解 AWS 上可用的各种安全服务，还要理解它们是如何工作的，以及它们处理和更重要的是不处理的安全方面。

# 关于 AWS 认证安全—专业考试的详细信息

AWS 安全专业[考试指南](https://d1.awsstatic.com/training-and-certification/docs-security-spec/AWS-Certified-Security-Specialty_Exam-Guide.pdf)描述了考试中涵盖的五个领域。以下是每个领域的详细信息:

## 事故响应

根据 AWS 滥用通知，评估可疑的受损实例或暴露的访问密钥。

*   验证事件响应计划是否包括相关的 AWS 服务。
*   评估自动警报的配置，并对安全相关事件和新出现的问题执行可能的补救措施。
*   亚马逊守卫

## 记录和监控

设计和实施安全监控和警报。

*   将事件传送到 Lambda 以自动监控响应
*   向社交网络发送事件以发送通知
*   VPC 交通监控

安全监控和警报故障排除。

*   记录日志和向其他服务发送警报所需的 IAM 策略

设计并实现一个日志解决方案。

*   使用 Athena 搜索 S3 的 CloudTrail 和其他日志

日志解决方案疑难解答。

## 基础设施安全

AWS 上的设计边缘安全性。

*   CloudFront 和 AWS WAF

设计和实施安全的网络基础设施。

*   VPC 构型
*   NAT 网关
*   虚拟专用网关
*   VPC 端点
*   S3 和 DynamoDB 的 VPC 网关端点

排除安全网络基础设施故障。

*   设计和实施基于主机的安全性。
*   安全组与网络访问控制列表
*   AWS 会话管理器
*   亚马逊检查员

## 身份和访问管理

设计并实现一个可伸缩的授权和认证系统来访问 AWS 资源。

*   认知

对访问 AWS 资源的授权和身份验证系统进行故障排除。

*   资源和角色之间的策略评估
*   明确拒绝的效果
*   保单条件条款

## 数据保护

设计和实现密钥管理和使用。

*   KMS 关键赠款
*   交叉帐户密钥访问权限

密钥管理故障排除

*   KMS 角色
*   KMS IAM 政策

为静态数据和传输中的数据设计和实施数据加密解决方案

*   KMS vs CloudHSM 以及何时使用它们

# 学习 AWS 认证安全—专业考试

AWS 安全专业考试没有正确的学习方法。人们学习的方式不同，适合我的不一定适合你。我参加 AWS 认证考试的方法是从参加在线课程开始，然后阅读 AWS 常见问题解答和服务白皮书。大多数在线课程都有实验室，让你有使用这些工具的实践经验。你可以通过广泛的学习和少量的实践经验来通过考试，但这只是例外。实践经验，尤其是在工作中，是学习材料的一种更直观的方式。当然，并不是每个人都在自己的公司中担任安全角色，在大公司中，您可能无法访问 AWS 中的所有安全特性。我建议在个人 AWS 帐户上尝试这些功能。由于 AWS 服务是按使用量收费的，所以您可以启用安全功能，测试它们，然后在完成后禁用这些功能，同时只需支付最少的费用。

## 班级

许多供应商提供许多在线培训认证培训课程。我发现下面的课程为我的材料打下了坚实的基础，但大多数课程都涵盖了基础知识，所以请使用您选择的供应商。没有哪门课会涵盖考试的每一个方面，所以我鼓励你不要一边上课一边马上参加考试。在考试学习准备中使用多种材料会比依靠单一方法让你对材料有更透彻的理解。

*   [云专家:AWS 认证安全专家](https://acloudguru.com/course/aws-certified-security-specialty)
*   [Udemy: AWS 认证安全专业 2021](https://www.udemy.com/course/aws-certified-security-specialty/)

## AWS 培训资源

亚马逊 YouTube 账户上有丰富的资源供你准备这次考试和其他 AWS 考试。有许多来自年度 re:Invent 会议的视频，其中首次介绍了新的 AWS 功能。此外，re:Inforce 会议致力于网络安全，这些视频值得在学习 AWS 安全专业考试时观看。以下是我发现在学习时特别有用的一些:

*   [AWS re:Inforce 2019:AWS 云安全基础(FND209-R)](https://youtu.be/-ObImxw1PmI)
*   [AWS re:Invent 2016: IAM 最佳实践指南(SAC317)](https://youtu.be/SGntDzEn30s)
*   [AWS re:Invent 2019:领导力会议:AWS security (SEC201-L)](https://youtu.be/oam8FDNJhbE)
*   [AWS re:Inforce 2019:为应用选择身份解决方案的最佳实践(FND215)](https://youtu.be/-xTs4MmQOo4)

## AWS 常见问题

AWS FAQs 是关于单个安全服务的极好的信息来源，包括基本的细节和限制。我建议关注的一些主要常见问题是:

*   [身份和访问管理(IAM)](https://aws.amazon.com/iam/faqs/)
*   [密钥管理服务(KMS)](https://aws.amazon.com/kms/faqs/)
*   [AWS CloudHSM](https://aws.amazon.com/cloudhsm/faqs/)
*   [S3 安全](https://aws.amazon.com/s3/faqs/)
*   [亚马逊 Macie](https://aws.amazon.com/macie/faq/)
*   [组织机构](https://aws.amazon.com/organizations/faqs/)

## AWS 安全白皮书

网上有几十份 [AWS 安全白皮书](https://aws.amazon.com/whitepapers/?whitepapers-main.sort-by=item.additionalFields.sortDate&whitepapers-main.sort-order=asc&awsf.whitepapers-tech-category=tech-category%23security-identity-compliance&awsm.page-whitepapers-main=1&awsf.whitepapers-content-type=content-type%23whitepaper)，但我发现以下内容在我的研究中特别有用:

*   [AWS 良好架构框架](https://docs.aws.amazon.com/wellarchitected/latest/security-pillar/welcome.html?did=wp_card&trk=wp_card)的安全支柱
*   [KMS 最佳实践](https://docs.aws.amazon.com/whitepapers/latest/kms-best-practices/welcome.html?did=wp_card&trk=wp_card)和 [KMS 细节](https://docs.aws.amazon.com/kms/latest/cryptographic-details/intro.html)
*   [DDoS 弹性的最佳实践](https://docs.aws.amazon.com/whitepapers/latest/aws-best-practices-ddos-resiliency/welcome.html?did=wp_card&trk=wp_card)

## 模拟考试

复习完材料后，您可以参加模拟考试，以确定您是否准备好参加真正的 AWS 安全专业考试，并帮助确定您需要进一步努力的薄弱环节。在线课程通常包括模拟考试，而有些课程允许您单独购买考试。作为培训课程的一部分，我参加了云专家和 Udemy 的模拟考试，但也参加了来自 Whizlabs 的单独模拟考试:

*   [云专家:AWS 认证安全专家](https://acloudguru.com/course/aws-certified-security-specialty)
*   [Udemy: AWS 认证安全专业 2021](https://www.udemy.com/course/aws-certified-security-specialty/)
*   [Whizlabs: AWS 认证安全—专业](https://www.whizlabs.com/aws-certified-security-specialty/)

此外，您可以直接通过 AWS 培训注册参加 20 美元[的实践考试。本模拟考试中的问题通常与真正的 AWS 安全专业考试问题有很大不同，但大多数模拟考试都是如此。](https://www.aws.training/certification?src=security-spec)

# 参加 AWS 认证安全—专业考试

有些人建议你报名参加考试，这样你就有了学习的期限和动力。我警告你不要过早这样做。在开始学习之前就确定考试日期通常是错误的。你不知道你不知道什么。也许您在 AWS 安全方面有多年的经验，在阅读这些资料时，您会发现这些资料对您来说都很熟悉。但是也许你复习了材料，发现有很多地方你需要花时间。你不会知道，直到你花时间复习所有的考试材料，也许参加一两次模拟考试。一旦你确定了自己对考试材料的理解程度，你就会对考试的进度有更好的了解。

在写这篇文章的时候，新冠肺炎仍然是一个问题，许多人都在远程考试。我参加过几次远程考试，过程类似于现场考试，但监考人的要求可能会有所不同。如果你参加远程考试，你需要清理你电脑周围的区域，并用你的摄像机显示该区域。考试时，您将被摄像头监控和记录，并且不能离开电脑。

我参加的第一次考试，我清理了桌子周围伸手可及的地方，监考人似乎对这种方法很满意。第二次测试，他们希望我的桌子完全干净。我不得不把所有的书和各种小摆设从我桌子上的书架上拿走。

# 结论

我希望这篇文章和我分享的想法能帮助你准备 AWS 安全专业考试。网络安全和云本身都是有趣的领域，但两者的结合带来了独特的机遇和挑战。无论您的具体工作职能是什么，安全性都是每个人的责任，您对云中的安全性了解得越多，您的系统就越安全。

祝你考试顺利！

*披露声明:2021 资本一。观点是作者个人的观点。除非本帖中另有说明，否则 Capital One 不隶属于所提及的任何公司，也不被这些公司认可。使用或展示的所有商标和其他知识产权是其各自所有者的财产。*