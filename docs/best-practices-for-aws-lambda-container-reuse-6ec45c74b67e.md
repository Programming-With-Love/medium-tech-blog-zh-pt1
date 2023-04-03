# AWS Lambda 容器重用的最佳实践

> 原文：<https://medium.com/capital-one-tech/best-practices-for-aws-lambda-container-reuse-6ec45c74b67e?source=collection_archive---------0----------------------->

## 将 AWS Lambda 连接到其他服务时优化热启动

![](img/2d7476d004b324a020b1353955aab08a.png)

由于是无服务器和无状态的，AWS Lambda 提供了高可伸缩性，允许 Lambda 函数的许多副本瞬间产生(如这里的[所述](https://docs.aws.amazon.com/lambda/latest/dg/programming-model-v2.html))。然而，在编写应用程序代码时，您可能希望访问一些有状态的数据。这意味着…