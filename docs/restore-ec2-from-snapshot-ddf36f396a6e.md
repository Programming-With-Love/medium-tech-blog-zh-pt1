# 如何从快照恢复 EC2？

> 原文：<https://medium.com/edureka/restore-ec2-from-snapshot-ddf36f396a6e?source=collection_archive---------0----------------------->

![](img/80e0a315f2ce293de27fd53ecd6a09a3.png)

亚马逊 EC2 和 S3 是亚马逊使用最广泛的服务。EC2 是一种计算服务，S3 是一种轻量级存储服务。为您的 EC2 实例创建备份总是一个好的做法，这样，如果您的实例被删除或停止工作，您可以从创建的快照恢复 EC2。

创建备份和恢复模块是灾难管理的最佳方法之一。在数据丢失的情况下，您可以随时使用备份，这样您的工作和业务就不会受到影响。既然您已经知道为什么备份和恢复模块是必要的，那么让我来引导您完成我将在本文中讨论的主题，即“*如何从快照*恢复 EC2”。

*   什么是 EC2 和 S3？
*   什么是 EBS 体积？
*   什么是 EBS 快照？
*   演示:(创建一个实例，删除它并从快照恢复 EC2)

# 什么是 EC2 和 S3？

亚马逊网络服务(Amazon Web Services)俗称 AWS，是亚马逊的子公司，提供按需云计算平台。AWS 提供许多服务，其中 EC2 和 S3 是常用的服务。

亚马逊 EC2 构成了亚马逊云计算平台的核心部分。亚马逊允许个人租用虚拟电脑运行自己的应用程序。这些虚拟计算机被称为 EC2 实例。AWS 中的 EC2 实例是用最常用的操作系统预定义的，您也可以根据自己的需求创建操作系统。

亚马逊 S3 是亚马逊提供的“简单存储服务”，在 web 界面中提供对象存储。S3 使用可扩展的存储基础架构为其全球电子商务网络中的客户提供存储。

在本文中，我们将主要关注 EC2 和 S3。现在您已经知道了什么是 Amazon EC2 和 Amazon S3，让我们看看如何在使用快照恢复 EC2 实例的过程中使用它。

# 什么是 EBS 体积？

Amazon EBS(弹性块存储)提供原始块级存储，可以附加到 Amazon EC2，也可以在 Amazon RDS 中使用。亚马逊 EBS 于 2008 年 8 月推出。EBS 用于以下情况:

1.  频繁的数据更改
2.  需要长期保存的数据
3.  读写操作比较频繁的数据库。
4.  需要不断更新的数据
5.  数据库应用存储

# 什么是 EBS 快照？

EBS 快照用于通过拍摄即时快照将数据从 EBS 卷备份到 S3。快照只不过是增量备份。EBS 快照的性质与原始卷的性质相同(加密或未加密)，EBS 快照创建的卷的性质与快照的性质相同(加密或未加密)。

![](img/b2f87ad0a7abd785d7645c8e994ec823.png)

您可以通过拍摄时间点快照将 EBS 卷中的数据备份到亚马逊 S3。快照是增量备份。增量备份是指仅包含更新文件的副本。这有助于最大限度地减少创建备份所需的时间。

现在，您已经了解了与 Amazon 提供的存储服务相关的所有事情，让我们学习如何实际实现上面给出的信息。

# 演示:(创建一个实例，删除它并从快照恢复 EC2)

在关于如何从快照恢复 EC2 的文章中，我将做以下事情

1.  创建 EC2 实例
2.  在从快照恢复 EC2 的过程之后，创建要验证的文件
3.  为实例创建 EBS 快照
4.  删除 EC2 实例
5.  从快照恢复 EC2

*   从 EBS 快照创建 AMI
*   启动创建的 AMI

6.验证文件是否存在？

让我们仔细看看每个步骤。

## **创建一个亚马逊 EC2 实例**

*使用 AWS 控制台启动 EC2 实例。*

![](img/a867b2c96b92483ffb5d6016be30b282.png)

*选择实例的类型。*

![](img/e050199c98fce9c723407b1b1710cc38.png)

*选择存储类型。*

![](img/305066bbe149cd7f352aa498dea300ff.png)

*选择您希望实例所在的 VPC 和子网。*

![](img/d38256c0dfefef278bd7709241e44095.png)

*添加存储。*

![](img/4a343d7cc046430515456e72f5c0067c.png)

*向实例添加标签。*

![](img/4a343d7cc046430515456e72f5c0067c.png)

*配置安全组。*

![](img/f19086f55d9542183026c817d58c3b8a.png)

*核实详情。点击启动。*

![](img/241bb08465c543486ce54d549c017f9e.png)

*选择密钥对以访问您的实例。您的实例已创建。现在是时候访问实例并创建文件了。*

![](img/71a4fa2f8bd11b1a98f7b17e5dc38214.png)

## **创建文件以便以后验证**

创建一个名为 EdurekaDemo 的目录。移动到目录。创建一个名为 edurekademotext.txt 的文件，打开文本文件。

![](img/77e109fd9a9806f16c48aa4ff4929057.png)

写一些文字，这样你就可以核实了。

![](img/3d035d9b218837f0abd384ad0ccf70fe.png)

## **为实例创建 EBS 快照**

*定位卷。*

![](img/20db029cd1a8c793a122f6a5a03484df.png)

*创建快照。*

![](img/49a588ce550c513cf7a66fe7498c3114.png)

*描述您的快照并创建快照。*

![](img/97f4fcbb5bea6d6b6796e473f73681f8.png)

## **删除 EC2 实例**

*终止实例。*

![](img/d8eeafb7b286f9f5e115646d2bc2d94b.png)

## **从快照恢复 EC2**

**从 EBS 快照创建 AMI**

*从 EBS 快照创建映像。*

![](img/e255b19e0c301b838c64842d6bb3a351.png)

*选择名称和描述，不要忘记选择硬件辅助虚拟化。*

![](img/b1cbd2b6d522dd6abfff258c13b9dbf0.png)

*您的图像创建请求已被处理，将在几分钟内创建完毕。*

![](img/de39cea515b8eb107b53e5a603bf5c1c.png)

**启动已创建的 AMI**

*启动时创建，并重复创建* *EC2 实例时完成的所有步骤。*

![](img/da0e619f7d501d28e29efd02771b5553.png)

*您的 EC2 实例已恢复。*

![](img/1e12cb8d370056eae587362a0198c560.png)

*您的 EC2 实例已创建。*

![](img/1d4fbcc793a977509101f3ca30c60867.png)

## **验证文件是否存在？**

*验证我们创建的文件是否存在:*

![](img/8a19872167fa1b636b685735a6104fc7.png)

经过验证，我创建的目录和文件存在于恢复的 EC2 实例中。

![](img/a6ca0a16bd8b3e2d0138fb90a39f6181.png)

这是使用快照成功恢复 EC2 实例的过程。我希望你明白演示。最好的学习方法是实施它。去执行吧。

如果你想查看更多关于人工智能、DevOps、道德黑客等市场最热门技术的文章，你可以参考 Edureka 的官方网站。

如果你想查看更多关于人工智能、DevOps、道德黑客等市场最热门技术的文章，那么你可以参考 [Edureka 的官方网站。](https://www.edureka.co/blog/?utm_source=medium&utm_medium=content-link&utm_campaign=aws-resume)

请留意本系列中的其他文章，它们将解释 AWS 的各个方面。

> *1。* [*AWS 教程*](/edureka/amazon-aws-tutorial-4af6fefa9941)
> 
> *2。* [*AWS EC2*](/edureka/aws-ec2-tutorial-16583cc7798e)
> 
> *3。*[*AWS Lambda*](/edureka/aws-lambda-tutorial-cadd47fbd39b)
> 
> *4。* [*AWS 弹性豆茎*](/edureka/aws-elastic-beanstalk-647ae1d35e2)
> 
> *5。* [*AWS S3*](/edureka/s3-aws-amazon-simple-storage-service-aa71c664b465)
> 
> *6。* [*AWS 控制台*](/edureka/aws-console-fd768626c7d4)
> 
> *7。* [*AWS RDS*](/edureka/rds-aws-tutorial-for-aws-solution-architects-eec7217774dd)
> 
> *8。* [*AWS 迁移*](/edureka/aws-migration-e701057f48fe)
> 
> *9。*[*AWS Fargate*](/edureka/aws-fargate-85a0e256cb03)
> 
> *10。* [*亚马逊 Lex*](/edureka/how-to-develop-a-chat-bot-using-amazon-lex-a570beac969e)
> 
> *11。* [*亚马逊*](/edureka/amazon-lightsail-tutorial-c2ccc800c4b7)
> 
> *12。* [*AWS 定价*](/edureka/aws-pricing-91e1137280a9)
> 
> *13。* [*亚马逊雅典娜*](/edureka/amazon-athena-tutorial-c7583053495f)
> 
> *14。* [*AWS CLI*](/edureka/aws-cli-9614bf69292d)
> 
> *15。* [*亚马逊 VPC 教程*](/edureka/amazon-vpc-tutorial-45b7467bcf1d)
> 
> 15。 [*AWS vs Azure*](/edureka/aws-vs-azure-1a882339f127)
> 
> 17。 [*内部部署 vs 云计算*](/edureka/on-premise-vs-cloud-computing-f9aee3b05f50)
> 
> 18。 [*亚马逊迪纳摩 DB 教程*](/edureka/amazon-dynamodb-tutorial-74d032bde759)
> 
> *19。* [*AWS 简历*](/edureka/aws-resume-7453d9477c74)
> 
> 20。[*AWS code commit*](/edureka/aws-codecommit-31ef5a801fcf)
> 
> *21。* [*顶级 AWS 架构师面试问题*](/edureka/aws-architect-interview-questions-5bb705c6b660)
> 
> *二十二。* [*如何从快照恢复 EC2？*](/edureka/restore-ec2-from-snapshot-ddf36f396a6e)
> 
> *23。* [*使用 AWS 创建网站*](/edureka/create-websites-using-aws-1577a255ea36)
> 
> *24。* [*亚马逊路线 53*](/edureka/amazon-route-53-c22c470c22f1)
> 
> *25。* [*用 AWS WAF 保护 Web 应用*](/edureka/secure-web-applications-with-aws-waf-cf0a543fd0ab)

*原载于 2019 年 1 月 18 日*[*https://www.edureka.co*](https://www.edureka.co/blog/restore-ec2-from-snapshot/)*。*