# 使用 CloudFormation 创建 DynamoDB 全局表

> 原文：<https://medium.com/capital-one-tech/using-cloudformation-to-create-dynamodb-global-tables-2af8597f8a0e?source=collection_archive---------5----------------------->

## 使用 CloudFormation 在 AWS 中自动创建 DynamoDB 基础设施

![](img/5d14c16af0df83a17e25a1c59ee66b1e.png)

2021 年 5 月，AWS 发布了对 DynamoDB 全局表的云形成支持。这个备受期待的特性对于那些希望自动化 DynamoDB 部署的人来说是一个福音。

DynamoDB 有许多很棒的特性，这使它成为 AWS 平台上增长最快的数据存储之一。DynamoDB 的主要特性之一是[全局表](https://aws.amazon.com/dynamodb/global-tables/)，它允许您跨区域无缝复制数据。我以前在这个博客上写过 DynamoDB 和全局表的许多[好处。然而，到目前为止，DynamoDB 还缺少一个关键特性:*全局表的自动部署。*](https://www.capitalone.com/tech/software-engineering/comparing-dynamodb-and-aurora-global-database-and-aurora-multi-master/)

# 将 DynamoDB 与 CloudFormation 一起使用

如果您不熟悉 DynamoDB 全局表，这个特性允许您将 DynamoDB 数据库实时复制到其他区域。全局表格提供了几个好处:

*   可以在本地区域访问数据，从而提高性能。
*   全局表的主动-主动特性允许每个区域中的更新实时自动复制到其他区域。
*   由于每个区域都有一份数据副本，提高了数据存储的可用性和持久性，因此获得了跨区域容错能力。

CloudFormation 允许您通过使用模板文件来提供 AWS 资源，提供基础设施即代码功能。虽然本文专门关注 DynamoDB，但是 CloudFormation 允许您自动创建大多数 AWS 资源。

虽然你可以自己阅读 [CloudFormation 文档](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-dynamodb-globaltable.html)，但是有一些重要的概念需要记住。全局表被视为顶级组件，等同于标准 DynamoDB 表，但与标准 dynamo db 表不同。全局表配置与 DynamoDB 表共享许多相同的元素，但是有些元素是在副本内部基于每个区域配置的，比如标记。在 CloudFormation 模板(CFT)中，每个副本都被配置为一个对等体，每个表都在一个列表中定义。这在概念上不同于在 AWS 控制台中设置 DynamoDB 表，在 AWS 控制台中，您通常创建一个初始表，然后在初始表的其他区域中添加全局表。

# 创建 CloudFormation 模板的注意事项

在为 DynamoDB 全局表创建 CloudFormation 模板时，需要注意一些限制和特性。

*   CloudFormation 仅支持 2019.11.21 版本的全局表，目前不支持 2017.11.29 版本。
*   如果您试图创建一个与同一区域中现有表同名的全局表，则现有表可能会被删除。
*   在部署 CFT 堆栈的同一区域中，必须至少存在一个副本。例如，如果您从 us-east-1 部署 CFT 堆栈，则至少有一个副本也必须在 us-east-1 中。

# 配置全局表的注意事项

在配置全局表时，一些属性已经在主配置和各个副本之间进行了拆分。

*   使用服务器端加密时，属性 SSEEnabled 和 SSEType 在模板的主要部分指定，但 KMSMasterKeyId 在每个副本下指定。当您考虑到 KMS 键是特定于地区的，并且在每个地区可能有不同的名称时，这是有意义的。尽管为了清楚起见，坚持地区之间的通用命名转换是有意义的。
*   最初创建 CFT 时，只能指定两个复本。如果需要两个以上的副本，则需要更新 CFT，但每次更新只能添加或移除一个副本。

# DynamoDB 全局表示例

以下模板在 us-east-1 和 us-west-1 中创建了一个名为 *mytable* 的 DynamoDB 全局表。

```
AWSTemplateFormatVersion: 2010-09-09
Description: AWS CloudFormation Template to create global tables
Parameters: {}
Resources:
  globalTableExample:
    Type: 'AWS::DynamoDB::GlobalTable'
    Properties:
      TableName: mytable
      AttributeDefinitions:
        - AttributeName: PK
          AttributeType: S
      KeySchema:
        - AttributeName: PK
          KeyType: HASH
      BillingMode: PAY_PER_REQUEST
      StreamSpecification:
      StreamViewType: NEW_AND_OLD_IMAGES
      SSESpecification:
      SSEEnabled: true
      SSEType: "KMS"
      Replicas:
        - Region: us-east-1
          PointInTimeRecoverySpecification:
          PointInTimeRecoveryEnabled: true
          SSESpecification:
            KMSMasterKeyId: alias/dynamodb-key-east
          Tags:
            - Key: Name
              Value: mytable
            - Key: Region
              Value: east
        - Region: us-west-1
          PointInTimeRecoverySpecification:
          PointInTimeRecoveryEnabled: true
          SSESpecification:
            KMSMasterKeyId: alias/dynamodb-key-west
          Tags:
            - Key: Name
              Value: mytable
            - Key: Region
              Value: west
```

正如我前面提到的，有些属性是全局表共有的，包括一些特定于区域的属性，还有一些属性的完整定义分布在通用属性和特定于区域的属性之间。

*   表名在所有地区都是通用的，无论是通过 CloudFormation、AWS 控制台还是其他方法创建，都是如此。
*   标签是特定于区域的，并在每个副本的定义下声明。
*   SSESpecifiction 部分位于模板的主要部分，部分位于每个副本中。

阅读和理解模板定义很重要，因为将元素放在错误的部分会导致 CloudFormation 失败。当我将测试模板从常规的 DynamoDB 表转换为全局表时，我自己也犯了这个错误。

如果您是 DynamoDB 的新手，并且希望自动化您的表创建过程，CloudFormation 是一个可以考虑使用的好工具。如果您像我一样使用 DynamoDB 已经有一段时间了，这是一个受欢迎的新增功能，最终允许以前需要手动步骤或定制脚本的流程实现自动化。在我个人看来，自动化程度越高越好！

*最初发表于*[T5【https://www.capitalone.com】](https://www.capitalone.com/tech/cloud/dynamodb-global-tables-with-cloudformation/)*。*