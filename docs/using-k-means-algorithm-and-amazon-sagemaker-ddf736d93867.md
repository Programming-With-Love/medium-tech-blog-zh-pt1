# 一名开发人员走进亚马逊 SageMaker…

> 原文：<https://medium.com/capital-one-tech/using-k-means-algorithm-and-amazon-sagemaker-ddf736d93867?source=collection_archive---------3----------------------->

## 使用 K-Means 算法和 Amazon SageMaker 构建机器学习模型

![](img/48ace6f169d5f99fd826f1ca7b7f8dfa.png)

除非你生活在众所周知的技术岩石下，否则你可能在某个时候听说过“机器学习”这个词。近年来，机器学习已经从学术界和数据科学扩展到通用软件工程领域。这导致工程团队试图理解这个新领域，以及如何将其集成到传统编程模型中。对于许多用例来说，机器学习可能是不必要的，但是在某些场景中，机器学习可以比传统的编程模型提供更好的价值。

有大量的博客讨论什么是机器学习以及与之配合使用的各种工具。我为对机器学习感兴趣的受过传统训练的软件工程师写了这篇文章，并希望专注于一个他们可能感兴趣的工具——[Amazon sage maker](https://aws.amazon.com/sagemaker/)——以及一个使用它的用例。

# 亚马逊 SageMaker 是什么？

Amazon SageMaker 是由 AWS 提供的完全托管的机器学习服务，它为开发人员和数据科学家提供了构建、训练和部署他们的机器学习模型的工具。它于 2017 年 11 月在 AWS re:Invent 期间推出。虽然它为机器学习模型提供了端到端的工作流，但它的任何模块都可以独立运行。这使得团队可以灵活地只使用他们需要的部分。它还为包括 Java、C#和 Python 在内的各种语言的开发者提供了一个健壮的 [CLI](https://docs.aws.amazon.com/cli/latest/reference/sagemaker/index.html) 和一套[丰富的 SDK](https://aws.amazon.com/sagemaker/developer-resources/)。

# 一个简单的用例

如果你愿意，可以想象一个拥有数百万条业务领域记录的数据库。您拥有最高效的 SQL 查询来返回您想要的任何数据。在这个场景中，业务部门请求根据数据库中的数百万条记录来预测趋势或检测模式。用不了多久，您就会意识到，仅仅使用高效的 SQL 查询很难完成这项任务。这就是引导我去亚马逊 SageMaker 的用例类型。

我目前在 Capital One 的角色是一个六人团队，致力于为企业设计中间件系统。我们使用 Java 和 NodeJs 构建 API 和微服务，所有这些都在 AWS 生态系统内。我们的部分职责是研究新的工具、供应商和策略，这些工具、供应商和策略有可能为我们的项目服务。

大约一年半前，我的团队被分配到设计新的微服务的任务，这些微服务将取代为各种业务线处理事务的遗留系统。新系统需要的一个特征是能够检测某些类型的交易。当时，我们决定使用规则引擎，因为它更容易实现，周转时间更快。六个月前，在我们发布微服务并成功替换传统系统后，我们开始与业务团队一起审查一些功能增强。我们发现规则引擎很难检测到某些交易模式，因为可观察到的模式不遵循简单的算术或逻辑。

我的团队开始研究加强规则引擎的方法，不久我们就同意加入机器学习是一条可行的途径。然而，我们是一个软件工程团队，而不是数据科学家，所以我们真的不知道如何开始。我们希望研究并理想地找到一种新工具，它既可以被数据科学家和软件工程师使用，又符合 MRO 提供的严格准则。我们并没有寻找一个终极的解决方案，而是寻找一个新的工具，用于我们管理良好的建模系统生态系统中。那时我们走进了亚马逊 sage maker……

我们喜欢 SageMaker，因为它让用户可以通过完成大部分基础设施的繁重工作来轻松构建机器学习模型。尽管 SageMaker 加速了您的构建，但是您仍然需要对您的数据和建模框架有一个基本的了解，尤其是在数据准备方面。而且，如果您在像我们这样的大型企业中工作，您仍然必须确保您的模型符合您的法律和治理要求。但是 SageMaker 可以为各种用例工作，这些用例涉及大量需要模式检测、预测或标记的数据。面部识别和消费者购买行为甚至可以通过亚马逊 SageMaker 来完成。

## 基本规则

为了构建这一概念证明，我们制定了一些基本规则，以在此过程中保护和指导我们:

*   将基础设施作为代码(IaC)用于我们所做的一切，以保持事情符合良好架构框架的[原则。](https://aws.amazon.com/blogs/apn/the-5-pillars-of-the-aws-well-architected-framework/)这可能是[云形成](https://aws.amazon.com/cloudformation/)、 [SageMaker SDK](https://aws.amazon.com/sagemaker/developer-resources/) 或 [Lambdas。](https://aws.amazon.com/lambda/)
*   加密！加密！！加密！！！我们需要通过在每个 SageMaker SDK 中使用适当的 KMS 密钥来确保静态数据保持加密。
*   标记任何创建的资源，以确保易于管理。
*   确保该工具符合我们的企业需求。

尽管与直接在 AWS 控制台中执行任务相比，使用 IaC 看起来开销很大，但考虑到我们必须创建和运行模型的次数，这种方法很快就有了回报。

## 数据准备

这是数据转换(规范化、标准化等)的地方。)发生，包括异常值和偏差的去除。数据科学家中用于这些任务的流行工具是 Jupyter 笔记本。AWS 提供了一个[管理版本的 Jupyter 笔记本](https://docs.aws.amazon.com/dlami/latest/devguide/setup-jupyter.html)，它与 SageMaker 集成得非常好。不过需要注意的是，在亚马逊 SageMaker 中构建数据模型并不一定要使用 Jupyter 笔记本。任何支持数据操作语言的集成开发环境(IDE)都应该足够了。Julia，Python 和 R (Jupyter 懂了吗？)是为数据操作和数值分析而构建的流行语言。我选择 Python 是因为它对 [AWS SDK](https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/sagemaker.html) 的广泛支持(AWS SDK 目前不提供对 Julia 和 R 的支持)。

以下是用于训练和测试的分割数据的 Python 代码片段:

```
from sklearn.model_selection import train_test_split
# get Training and testing Data
df = pd.read_csv("TrainTest_RawData.csv", sep=",")# randomly select 20% of the data for testing and the other 80% for training
x_train, x_test, y_train, y_test = train_test_split(df.Amount, df.tranCode,test_size=0.2
```

## 模特培训

数据验证和理解是任何建模工作流中最耗时的部分，但是模型训练的配置需要对模型类型本身的理解，并且具有如下所示的多个方面。

**IAM 角色**
将用于构建这些模型的 IAM 角色必须满足以下标准:

*   你必须能够承担这个角色。
*   该角色必须附加有 SageMaker 信任关系。

```
{
    "Version": "2012-10-17",
    "Statement": [
      {
        "Effect": "Allow",
        "Principal": {
          "Service": [
            "sagemaker.amazonaws.com"
          ]
        },
        "Action": "sts:AssumeRole"
      }
    ]
}
```

*   该角色必须附有 SageMaker/KMS 策略。

```
{
    "Version": "2012-10-17",
    "Statement": [
      {
        "Action": "iam:PassRole",
        "Resource": "*",
        "Effect": "Allow",
        "Condition": {
          "StringEquals": {
            "iam:PassedToService": "sagemaker.amazonaws.com"
          }
        },
        "Sid": "GrantsIamPassRole"
      },
      {
        "Action": [
          "kms:DescribeKey",
          "kms:ListAliases"
        ],
        "Resource": "*",
        "Effect": "Allow",
        "Sid": "KmsKeysForCreateForms"
      },
      {
        "Action": [
          "iam:ListRoles"
        ],
        "Resource": "*",
        "Effect": "Allow",
        "Sid": "ListAndCreateExecutionRoles"
      },
      {
        "Action": [
          "kms:Encrypt",
          "kms:Decrypt",
          "kms:ReEncrypt*",
          "kms:GenerateDataKey*",
          "kms:DescribeKey",
          "kms:GetKeyPolicy",
          "kms:CreateGrant",
          "kms:ListGrants",
          "kms:RevokeGrant"
      ],
      "Resource": "KMS_KEY_ARN",
      "Effect": "Allow",
      "Sid": "GrantKMSAccess"
    },
    {
      "Action": [
        "sagemaker:CreateEndPoint",
        "sagemaker:DescribeEndpoint"
      ],
      "Resource": "*",
      "Effect": "Allow",
      "Sid": "GrantSageMakerCreate"
    }
  ]
}
```

**KMS 密钥**
将用于加密的 KMS 密钥必须附带一个策略，该策略将授予对将使用 SageMaker 的 IAM 角色的访问权限。

```
{
    "Version": "2012-10-17",
    "Id": "key-consolepolicy-3",
    "Statement": [
        {
            "Sid": "Enable IAM User Permissions",
            "Effect": "Allow",
            "Principal": {
                "AWS": "ARN_OF_ROOT"
            },
            "Action": "kms:*",
            "Resource": "*"
        },
        {
            "Sid": "Allow access for Key Administrators",
            "Effect": "Allow",
            "Principal": {
                "AWS": "ARN_OF_KEY_ADMINISTRATORS_ROLE"
            },
            "Action": [
                "kms:Create*",
                "kms:Describe*",
                "kms:Enable*",
                "kms:List*",
                "kms:Put*",
                "kms:Update*",
                "kms:Revoke*",
                "kms:Disable*",
                "kms:Get*",
                "kms:Delete*",
                "kms:TagResource",
                "kms:UntagResource",
                "kms:ScheduleKeyDeletion",
                "kms:CancelKeyDeletion"
            ],
            "Resource": "*"
        },
        {
            "Sid": "Allow use of the key",
            "Effect": "Allow",
            "Principal": {
                "AWS": [
                    "ARN_OF_SAGEMAKER_ROLE"
                ]
            },
            "Action": [
                "kms:Encrypt",
                "kms:Decrypt",
                "kms:ReEncrypt*",
                "kms:GenerateDataKey*",
                "kms:DescribeKey",
                "kms:GetKeyPolicy"
            ],
            "Resource": "*"
        },
        {
            "Sid": "Allow attachment of persistent resources",
            "Effect": "Allow",
            "Principal": {
                "AWS": [
                    "ARN_OF_SAGEMAKER_ROLE"
                ]
            },
            "Action": [
                "kms:CreateGrant",
                "kms:ListGrants",
                "kms:RevokeGrant"
            ],
            "Resource": "*",
            "Condition": {
                "Bool": {
                    "kms:GrantIsForAWSResource": "true"
                }
            }
        }
    ]
}
```

**培训工作**
此时，您已准备好将数据导入 SageMaker 进行培训。我们的用例非常适合集群。Amazon SageMaker 支持超过 15 ML 的算法，可以满足庞大的场景库。 *K-Means* 就是解决聚类问题的算法之一。

*我在首都的一个同事 Madison Schott 写了一篇关于 K-Means 算法的* [*博客*](/capital-one-tech/k-means-clustering-algorithm-for-machine-learning-d1d7dc5de882) *如果你想更深入的了解。*

有两种方法可以在 Amazon SageMaker 中为 K-Means 算法(以及它支持的大多数算法)构建训练作业:

*   **创建培训作业 API:** 当您非常清楚培训作业构建最佳模型所需的超参数(参数)时，可以使用该 API。它可以加快模型的构建速度，但是需要您具备数据的领域知识，以提供精确的超参数值来构建最佳模型。它在流程结束时产生一个培训工作。
*   **创建超参数调整作业 API:** 当您不知道产生最佳模型的准确超参数值时，使用该工具。您向它提供一系列超参数值以供使用，SageMaker 将为您构建一系列训练工作，并根据提供的范围标记最佳工作。对于 *K-Means* 算法，SageMaker 提供了一个推荐使用的超参数值范围。

对于我们的 POC，我们使用了超参数调整作业 API，这使我们能够灵活地尝试不同的培训作业场景，而不是一次构建一个。

以下 Python 代码可用于创建超参数优化作业:

```
import boto3
import osfrom sagemaker.amazon.amazon_estimator import get_image_urios.environ['AWS_PROFILE'] = 'YOUR_AWS_STS_PROFILE'# gets the K-Means docker image from AWS
image = get_image_uri(boto3.Session().region_name, 'kmeans')role = 'ARN_OF_ROLE'
bucket = 'S3_BUCKET'data_key = 'S3_TRAINING_DATA_LOCATION'
test_key = 'S3_TESTING_DATA_LOCATION'
output_key = 'S3_MODEL_OUTPUT_LOCATION'data_location = 's3://{}/{}'.format(bucket, data_key)
output_location = 's3://{}/{}'.format(bucket,output_key)
test_location = 's3://{}/{}'.format(bucket, test_key)sagemaker = boto3.client('sagemaker')response = sagemaker.create_hyper_parameter_tuning_job(
    HyperParameterTuningJobName='TRAINING_JOB_NAME',
    HyperParameterTuningJobConfig={
        'Strategy': 'Bayesian',
        'HyperParameterTuningJobObjective': {
            'Type': 'Minimize',
            'MetricName': 'test:msd'
        },
        'ResourceLimits': {
            'MaxNumberOfTrainingJobs': 50,
            'MaxParallelTrainingJobs': 2
        },
        'ParameterRanges': {
            'IntegerParameterRanges': [
                {
                    'Name': 'extra_center_factor',
                    'MinValue': '4',
                    'MaxValue': '10'
                },
                {
                    'Name': 'mini_batch_size',
                    'MinValue': '3000',
                    'MaxValue': '15000'
                },
            ],
            'CategoricalParameterRanges': [
                {
                    'Name': 'init_method',
                    'Values': [
                        'kmeans++', 'random'
                    ]
                },
            ]
        },
        'TrainingJobEarlyStoppingType' : 'Auto'
    },
    TrainingJobDefinition={
        'StaticHyperParameters': {
            'k': '10',
            'feature_dim': '2',
        },
        'AlgorithmSpecification': {
            'TrainingImage': image,
            'TrainingInputMode': 'File'
        },
        'RoleArn': role,
        'InputDataConfig': [
            {
                'ChannelName': 'train',
                'DataSource': {
                    'S3DataSource': {
                        'S3DataType': 'S3Prefix',
                        'S3Uri': data_location,
                        'S3DataDistributionType': 'FullyReplicated'
                    }
                },
                'ContentType': 'text/csv;label_size=0'
            },

            {
                'ChannelName': 'test',
                'DataSource': {
                    'S3DataSource': {
                        'S3DataType': 'S3Prefix',
                        'S3Uri': test_location,
                        'S3DataDistributionType': 'FullyReplicated'
                    }
                },
                'ContentType': 'text/csv;label_size=0'
            },
        ],
        'OutputDataConfig': {
            'KmsKeyId': 'KMS_KEY_ID',
            'S3OutputPath': output_location
        },
        'ResourceConfig': {
            'InstanceType': 'ml.m4.16xlarge',
            'InstanceCount': 1,
            'VolumeSizeInGB': 50,
            'VolumeKmsKeyId': 'KMS_KEY_ID'
        },
        'StoppingCondition': {
            'MaxRuntimeInSeconds': 60 * 60
        }
    }
)print(response)
```

**模型**
在创建了符合您的标准的培训工作后，您现在可以创建模型了。该模型接受训练作业和算法，并创建 Docker 配置，SageMaker(或任何平台)可以为您托管该配置。这是一个基本的概念证明，展示了如何构建模型，并掩盖了将模型投入生产的治理流程和合作关系。

以下 Python 代码示例可用于创建模型:

```
import boto3
import osfrom sagemaker.amazon.amazon_estimator import get_image_urios.environ['AWS_PROFILE'] = 'AWS_STS_PROFILE'sagemaker = boto3.client('sagemaker')role = 'ARN_IAM_ROLE'# gets the K-Means docker image from AWS
image = get_image_uri(boto3.Session().region_name, 'kmeans')job_name = 'TRAINING_JOB_NAME'
model_name = 'MODEL_NAME'info = sagemaker.describe_training_job(TrainingJobName=job_name)
print(info)
model_data = info['ModelArtifacts']['S3ModelArtifacts']primary_container = {
    'Image': image,
    'ModelDataUrl': model_data
}tags = [
        {'Key' : 'ResourceName', 'Value': 'KMeans_Model'}
    ]create_model_response = sagemaker.create_model(
    ModelName = model_name,
    ExecutionRoleArn = role,
    PrimaryContainer = primary_container,
    Tags = tags)print(create_model_response['ModelArn'])
```

**端点配置**
创建模型后的下一步是创建端点配置。这将创建用于构建 API 端点的配置，API 端点将最终托管模型。

以下 [](https://github.kdc.capitalone.com/gist/yrw063/29bfecf8f4a28866c8bd7d689e793414) 代码示例可用于创建端点配置:

```
import boto3
import osos.environ['AWS_PROFILE'] = 'AWS_STS_PROFILE'sagemaker = boto3.client('sagemaker')
model_name = 'MODEL_NAME'endpoint_config_name = 'ENDPOINT_CONFIG_NAME'
print(endpoint_config_name)tags = [
        {'Key' : 'ResourceName', 'Value': 'KMeans_EndPointConfig'}
    ]create_endpoint_config_response = sagemaker.create_endpoint_config(
    EndpointConfigName = endpoint_config_name,
    ProductionVariants=[{
        'InstanceType':'ml.m4.xlarge',
        'InitialInstanceCount':1,
        'ModelName':model_name,
        'VariantName':'AllTraffic'}],
    Tags= tags,
    KmsKeyId='KMS_KEY_ID')print("Endpoint Config Arn: " + create_endpoint_config_response['EndpointConfigArn'])
```

**端点**在用于创建端点的 SDK 中，没有用于分配将执行 SDK 的角色的参数。因此，您不能在本地执行*sage maker . create _ endpoint*。

我使用的解决方法包括创建一个 Lambda 函数，并将执行角色分配给在创建前面的资源时使用的 IAM 角色。

这是 Lambda 函数的示例代码:

```
import json
import boto3
import os def lambda_handler(event, context):
    sagemaker = boto3.client('sagemaker')

    endpoint_name = event['EndPointName']
    endpoint_config_name = event['EndPointConfigName'] 

    tags = [
            {'Key' : 'ResourceName', 'Value': 'Kmeans_Lambda'}
        ]

    print(endpoint_name)

    create_endpoint_response = sagemaker.create_endpoint(
        EndpointName=endpoint_name,
        EndpointConfigName=endpoint_config_name,
        Tags = tags)

    print(create_endpoint_response['EndpointArn'])

    resp = sagemaker.describe_endpoint(EndpointName=endpoint_name)
    status = resp['EndpointStatus']
    print("Status: " + status)

    try:
  sagemaker.get_waiter('endpoint_in_service').wait(EndpointName=endpoint_name)
    finally:
        resp = sagemaker.describe_endpoint(EndpointName=endpoint_name)
        status = resp['EndpointStatus']
        print("Arn: " + resp['EndpointArn'])
        print("Create endpoint ended with status: " + status)

        if status != 'InService':
            message = sagemaker.describe_endpoint(EndpointName=endpoint_name)['FailureReason']
            print('Create endpoint failed with the following error: {}'.format(message))
            raise Exception('Endpoint creation did not succeed')
```

## 确认

创建完端点之后，您就可以开始向端点发送测试数据并获取结果了。获取训练/测试数据段的最简单方法是在数据准备阶段统一绘制 80/20。这个方法的验证是一个严格的过程，为了简洁起见，我们跳过了这个过程。要调用您的端点，您可以使用您选择的语言的 SageMaker-runtime SDK。

这是我在 POC 中使用的 Python 片段。

```
import boto3
import os
import jsonos.environ['AWS_PROFILE'] = 'AWS_STS_PROFILE'runtime = boto3.Session().client('sagemaker-runtime',use_ssl=True)
endpoint_name = 'ENDPOINT'
payload = 'DATA_TO_SEND'response = runtime.invoke_endpoint(EndpointName=endpoint_name, 
                                   ContentType='text/csv', 
                                   Body=payload)result = json.loads(response['Body'].read())
print(result)
```

结果以 JSON 格式返回:

```
{"closest_cluster": 1.0, "distance_to_cluster": 7.6}
{"closest_cluster": 2.0, "distance_to_cluster": 3.2}
```

聚类 id 和到聚类的距离的含义与 K-Means 算法的数学解释有关。该模型不会直接告诉你一个点是否属于一个聚类，你必须提供逻辑来根据到聚类的距离进行推断。在我们的概念验证中，我们发现我们的数据点紧密地聚集在一起(非常短的距离),因为这些点真正属于同一个组。然而，对于不在业务线标准范围内的点，我们注意到聚类的距离比平均距离大得多。基于这一发现，我们开始构建我们的推理逻辑。

## 履行

一旦您对您的验证结果感到满意，那么这就意味着您已经有了一个可以接受管理机构验证的模型。当您的模型获得批准并准备投入生产时，AWS SageMaker 可以轻松地在 AWS 中托管和扩展您的模型。鉴于其模块的灵活性，您可以从 AWS 导出您的模型，并将其托管在 prem 或任何您想要的平台上。

# 结论

作为一名几乎没有数据科学背景的软件工程师，亚马逊 SageMaker 很容易就开始构建机器学习模型。我们仍在与我们的模型风险办公室合作，评估该模型并微调其结果的准确性。建立一个准确的模型需要时间、协作和整个工具生态系统，但一旦你有了一个可以检测复杂模式的模型，它就有回报了。

市场上有各种各样的工具来帮助工程师开始构建机器学习模型。我鼓励您做自己的研究，找出哪一个最适合您的用例及治理过程！

*披露声明:2020 资本一。观点是作者个人的观点。除非本帖中另有说明，否则 Capital One 不隶属于所提及的任何公司，也不被这些公司认可。使用或展示的所有商标和其他知识产权是其各自所有者的财产。*