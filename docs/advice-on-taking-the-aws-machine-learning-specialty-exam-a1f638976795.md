# 关于参加 AWS 机器学习专业考试的建议

> 原文：<https://medium.com/capital-one-tech/advice-on-taking-the-aws-machine-learning-specialty-exam-a1f638976795?source=collection_archive---------2----------------------->

## 刚刚通过 AWS 机器学习专业考试的人提供的一些有用的指南和提示

![](img/efbf01a4ac5ae896daefd1d6d2b2cdd6.png)

大家好，我叫 [Fabrice Guillaume](mailto:fabrice.guillaume@capitalone.com) ，我最近通过了 [AWS 机器学习专业](https://aws.amazon.com/certification/certified-machine-learning-specialty/)考试。我想我会分享一些我的经验来帮助其他人准备这次考试，我会归类为“相当具有挑战性，但如果你准备充分，完全可以做到。”

这个博客有三个部分。首先，我将介绍为什么有人可能想要参加 AWS 机器学习专业考试。然后我会分享一些我发现对备考有用的网上资料。最后，我将提供一些通过考试的技巧。

# 1.你为什么应该参加 AWS 机器学习专业考试？

首先，跟上科技和人工智能的新发展。这些学科发展速度极快，并且正在*用一些真正有趣的功能在我们的日常生活中彻底改变我们的世界。例如，像[网飞](https://www.netflix.com/)这样的推荐引擎根据其他客户的选择建议下一场演出，Spotify 及其基于用户偏好和社区反馈的个性化音轨，苹果的个人助理 [Siri](https://www.apple.com/siri/) ，亚马逊 [Alexa](https://developer.amazon.com/en-US/alexa) 的智能家居中枢控制许多联网设备，以及特斯拉的[自动驾驶](https://www.tesla.com/autopilot)(自动驾驶)汽车功能。更不用说资本自己的智能助理[Eno](https://www.capitalone.com/applications/eno/)24/7 帮助照顾你和你的钱。一句话——人工智能革命已经在发生，它只会加速并进一步深入我们的日常生活。这意味着跟上这种不断移动和加速的高速列车*基于变革性/预测性/革命性人工智能* *的能力*在技术和机器学习工程领域至关重要。*

第二，更好地理解使用什么机器学习技术以及何时使用。ML/AI 工具箱一直在增长。开源社区非常活跃，来自行业和学术界的研究人员和从业者发布代码，在许可许可下培训/构建/部署和扩展模型，云供应商和技术公司在易于使用的 SaaS 模型中实现和扩展 ML 功能。尽管所有这些团队都在努力使模型的构建和部署变得更容易，但这真的变得“容易”了吗？虽然使用 SaaS 产品和几家公司提供的一些“自动 ML”功能可能会变得更容易，但我个人认为理解机器学习项目生命周期中正在发生的事情很重要，特别是在评估使用/提取/抑制/编码什么数据特征、适合什么算法、调整什么超参数、何时重新训练或重新调整已经在生产中的模型等等时。因此，关于 ML 构建模块、工具和库的进一步教育将让你知道使用什么、何时使用、如何使用和监控它；这些都很重要。

最后，这个考试在软件/数据/人工智能行业也很受重视。这不仅仅是一个高级水平的理论考试，它也非常适用，涵盖了最新的建模技术和云服务。虽然有些主题是 AWS 特有的，但这些知识在许多行业都是普遍适用的，这使得该认证对那些寻找能够支持机器学习项目整个生命周期的机器学习工程师的公司来说很有吸引力。

# 报名参加机器学习 AWS 专业考试之前的考虑事项

你可能对机器学习 AWS 专业考试是否适合你有疑问。我知道我做到了。以下是我对谁应该和不应该参加考试的想法，以及关于考试的形式和结构的一些想法。

## 推荐的过去知识和经验

为了通过机器学习 AWS 专业考试，有一个*需要一些 AWS 和机器学习方面的经验，即:*

*   1 到 2 年在 AWS 云上开发、设计或运行机器学习/深度学习工作负载的经验。
*   基本 ML 算法背后的理解和直觉。
*   执行基本超参数优化的经验。
*   有机器学习和深度学习框架的经验。
*   遵循模型训练最佳实践的能力。
*   遵循部署和运营最佳实践的能力。
*   有 AWS 经验，特别是 IAM 和 VPC。最好有流媒体服务(Kinesis 系列)和批处理服务(AWS 胶水、批处理、数据管道)的经验

然而，有一个*不需要*参加考试的是:

*   *Python 大师* —本质上不会有任何 Python 编码练习，但你需要能够阅读并在利用 SageMaker 的 Python SDK 和阅读开发人员指南时潜在地使用它。
*   *数据科学家大师*——尽管如果你是这样的话会有所帮助。虽然会有一些高级的建模问题，比如使用什么超参数以及何时使用。人们可以通过一些适合各种算法的练习来了解它。
*   *AWS 认证解决方案架构师*——尽管如果你是这样会有所帮助。将会有一些与安全相关的问题，如如何保护传输中的数据和静态数据，如何限制对笔记本电脑的访问，以及使用各种 AWS 数据服务进行 ETL 和存储。但是如果你还没有接触过它们，你可以通过各种练习来了解它们。

为了介绍一下我的背景经历，我会将自己归类为在数据、大数据、软件和云工程方面拥有丰富经验的机器学习工程师。七年前，我在一家本地初创公司开始涉足机器学习领域，三年前加入 Capital One，担任数据/机器学习工程总监，负责机器学习平台项目。我没有博士学位，但我有无线电信的硕士学位，主修数学。话虽如此，你不需要硕士或博士学位来通过考试，也不会真正深入数学。编码也是一样，你不需要编码，也不需要做代码审查，来为这个考试做准备。但是，它会有所帮助。

## 机器学习 AWS 专业考试期间涵盖的主题

根据 [AWS 考试指南](https://urldefense.com/v3/__https:/d1.awsstatic.com/training-and-certification/docs-ml/AWS*20Certified*20Machine*20Learning*20-*20Specialty_Exam*20Guide*20*281*29.pdf__;JSUlJSUlJSUl!!EFVe01R3CjU!M4wGIqq_JHhT1wKNtsAQeQ8D3cYyLUl5G2dlE-Mpu6IAqAzZ9-cT3CUEbK_33HEUH5bo%24)，您将在 4 个关键领域接受测验:

1.  **数据工程(20%):** S3(和 VPC 端点网关)、Kinesis(流、消防软管、数据分析、视频)、Glue(数据目录和爬虫)、Athena、AWS 数据存储(红移、RDS/Aurora、DynamoDB、ElasticSearch、ElastiCache)、AWS 数据管道、AWS 批处理、AWS DMS 和 AWS Step 函数。
2.  **探索性数据分析(24%):** 数据类型和分布、时间序列、亚马逊 Athena、Quicksight、Ground Truth、EMR、Spark、数据宁滨、变换、编码、缩放和洗牌、处理缺失数据、不平衡数据和离群值。
3.  **建模(36%):** CNN、RNN、调谐神经网络、正则化、梯度下降法、L1 和 L2 正则化、混淆矩阵(Precision、Recall、F1、AUC)、集成方法(Bagging 和 Boosting)、亚马逊 Sagemaker、亚马逊算法(线性学习器、XGBoost、Seq2Seq、BlazingText、DeepAR、Object2Vec、ObjectDetection、图像分类、语义分割、RCF、LDA、KNN、K-Means、PCA、因式分解机)、亚马逊 AI 服务(理解、翻译、转录、Polly、Rekognition
4.  **ML 实施和运营(20%):** SageMaker 生产变体、Neo、物联网 Greengrass、静态和传输加密、VPC、IAM、日志记录、监控、实例类型和现场实例、弹性推理、自动扩展、可用性区域、推理管道等

## 关于机器学习 AWS 的附加信息-专业考试

这项考试花费 300 美元，时长 3 小时(180 mn)。你将有 65 个多项选择的问题，有时有多个答案。就我个人而言，我发现我有足够的时间来回答问题，可能会标记出我不确定的问题，并在以后回顾它们。我不觉得我必须匆忙完成问题，并且总是在最后有时间回顾我已经标记的几个问题，并且在最后还有时间。

要通过考试，你需要在 0 到 1000 分的范围内获得最低 750 分。没有回答的问题是没有意义的。而且要小心！问题没有部分分数(如果你在 5 题中答对了 3 题中的 2 题，你就没有分数)。

最终得分是一个“比例”得分，问题是交替的。以下是关于分级评分的更多信息:“AWS 认证考试结果现在以 100 到 1000 分的形式报告。你的分数显示了你在考试中的整体表现以及你是否及格。分级评分模型用于使难度水平略有不同的多种考试形式的分数相等。”(来源 AWS [支持页面](https://aws.amazon.com/blogs/training-and-certification/demystifying-your-aws-certification-exam-score/))

# 2.机器学习 AWS 专业考试的在线资源和课程

以下是一些有用的在线资源。以下参考资料并不构成对其服务的认可或推荐。我建议你探索其他在线内容，找到适合你的。另外请记住，AWS 每年都会推出新的服务，因此会对这些在线课程进行添加/更新，并且会提供新的课程。

对于那些真正想专注于一部分课程的人，我建议至少参加两个课程(CloudGuru 和/或 Udemy，它们都提供培训和实验室实践)中的一个，以及 AWS 白皮书，作为“最低要求”。

## 云专家(付费课程，约 15 小时，1 次模拟测试)

[AWS 认证机器学习-专业 2020](https://acloudguru.com/course/aws-certified-machine-learning-specialty-2020) 视频培训将为您准备 AWS 认证机器学习专业考试，提供一些很棒的材料和动手实验。

在本课程中，您将学习:

*   AWS 认证机器学习专业考试的知识领域
*   使用 AWS 工具和平台进行数据工程、数据分析、机器学习建模、模型评估和部署的最佳实践
*   旨在挑战您的直觉、创造力和 AWS 平台知识的动手实验室

通过本课程，您将对 AWS 上可用于机器学习项目的服务和平台有一个坚实的了解，为通过认证考试打下基础，并感觉有能力在自己的现实世界应用中使用 AWS ML 产品组合。

我个人很喜欢这两位培训师(Scott Pletcher 和 Brock Tubre ),并发现他们使用的幻灯片内容丰富且切中要点。我又去了几次，重温了一些话题。此外，实验室很棒(也很有趣)。你可以跟着视频或者自己先试一下，如果你想动手的话，他们会给你数据集和代码。

## Udemy(付费课程，约 10 小时，1 次模拟测试)

[AWS 认证机器学习专业 2020 —动手！](https://www.udemy.com/course/aws-machine-learning/)在线课程是另一个很好的学习方式。

该课程由弗兰克·凯恩(Frank Kane)教授，他在亚马逊工作了九年，从事机器学习领域的工作。弗兰克第一次就参加并通过了考试，他知道通过考试需要什么。和弗兰克一起参加本课程的是夏羽·马雷克，他是 AWS 专家，也是 Udemy 上广受欢迎的 AWS 认证讲师。

除了 9 小时的视频课程之外，还包括一个 30 分钟的快速评估练习考试，其主题和风格与真正的考试相同。您还将获得四个动手实验，让您实践所学，并获得模型调整、特征工程和数据工程方面的宝贵经验。

## Whizlabs(付费，约 10 小时，2 次模拟考试)

最后， [Whizlabs 课程(和测试)](https://urldefense.com/v3/__https:/www.whizlabs.com/learn/course/aws-mls-practice-tests__;!!EFVe01R3CjU!M4wGIqq_JHhT1wKNtsAQeQ8D3cYyLUl5G2dlE-Mpu6IAqAzZ9-cT3CUEbK_33PeQ-s7M%24)是另一个很好的在线资源，特别是我发现它相当先进的模拟考试。我建议最后再做这些。

## AWS(大多数课程免费，使用 AWS 服务需付费)

有许多 AWS 在线课程和白皮书，你会希望阅读它们来熟悉亚马逊提供的各种人工智能/数据管道/安全服务；这些都是考试题目的一部分。

一种方法是遵循 [AWS 机器学习在线](https://urldefense.com/v3/__https:/aws.amazon.com/training/learn-about/machine-learning/__;!!EFVe01R3CjU!M4wGIqq_JHhT1wKNtsAQeQ8D3cYyLUl5G2dlE-Mpu6IAqAzZ9-cT3CUEbK_33He72PF7%24)课程库中的“开发人员”和“数据科学家”课程。

您还可以在笔记本上阅读/观看/使用:

此外，还有一些可靠的 Amazon 白皮书，您可能希望深入阅读，深入了解各种主题，例如:

最后，虽然考试可能不会测试您最近宣布或发布的(最近意味着“6 个月内”)服务，但它会让您跟上速度并了解 AWS 发布的内容。一个好办法是参加或观看 [AWS Re:invent 文章和视频](https://urldefense.com/v3/__https:/reinvent.awsevents.com/__;!!EFVe01R3CjU!M4wGIqq_JHhT1wKNtsAQeQ8D3cYyLUl5G2dlE-Mpu6IAqAzZ9-cT3CUEbK_33MJ0DO2V%24)。

## 斯坦福/Coursera 机器学习和深度学习(付费约 50-70 小时)

为了被介绍并对机器学习和深度学习有坚实的理解，我强烈推荐下面吴恩达博士的 Coursera/Stanford 课程。我发现它们是当今最好的 ML 和深度学习在线课程之一。Andrew 博士是一位优秀的讲师，他将通过手写/绘画和讲故事的方式详细讲解每个重要的概念，比如正则化、超参数调整等:

## YouTube(免费)

YouTube 上有几个视频，它们彻底解释了 ML 的基本概念，并引导观众了解 ML 的整个生命周期，分享如何利用 Amazon SageMaker、Jupyter 笔记本等。诸如此类的视频可能对那些不像他们希望的那样“亲自动手”的人有所帮助。他们还可以更好地感受 ML 工程师和数据科学家使用的用户体验和界面。以下是推荐的视频列表，但是请注意，这绝不是一个完整的列表，因为每天都会不断添加视频:

## Kindle(付费)

有各种各样的书籍会为你提供额外的信息，其中一些包含实际的实践考试。一个例子是:

## 创建您自己的—备忘单

由于有许多文章、视频和博客可以通过谷歌轻松搜索，我的建议是寻找并建立自己的备忘单。一旦你觉得你准备好参加考试，只需参考你的小抄快速刷新。

我根据我在模拟考试中得到的错误答案以及一些我不想忘记的细微差别和细节制作了自己的小抄。我将我的[小抄](https://github.com/FabG/ml-aws-specialty-lab/blob/master/aws-notes/aws-machine-learning-cheat-sheet-112020.pdf)作为 PDF 文件分享，但是，请记住这是一份高度个性化的小抄，浓缩了我的学习经验。我鼓励你建立你自己的。

# 3.关于参加机器学习 AWS 专业考试模拟考试的建议

要通过考试，你需要参加一些模拟测试。我强烈建议你通过尽可能多的模拟测试，最好每个测试几次。作为在线课程的一部分，上述每门课程都提供一个或多个模拟考试。有些可能会出售“额外”的实践考试，许多人认为这是值得的费用。

以下是一些提示:

*   [样本 AWS 10 题模拟考试(及答案)](https://urldefense.com/v3/__https:/d1.awsstatic.com/training-and-certification/docs-ml/AWS-Certified-Machine-Learning-Specialty_Sample-Questions.pdf__;!!EFVe01R3CjU!M4wGIqq_JHhT1wKNtsAQeQ8D3cYyLUl5G2dlE-Mpu6IAqAzZ9-cT3CUEbK_33L4q2oDm$)
*   [Udemy 全实践考试(65 题)](https://urldefense.com/v3/__https:/www.udemy.com/course/aws-machine-learning-practice-exam/__;!!EFVe01R3CjU!M4wGIqq_JHhT1wKNtsAQeQ8D3cYyLUl5G2dlE-Mpu6IAqAzZ9-cT3CUEbK_33O87s0g-$)
*   [备考 ML 专业实践考试(两次考试)](https://urldefense.com/v3/__https:/www.testpreptraining.com/aws-certified-machine-learning-specialty-practice-exam__;!!EFVe01R3CjU!M4wGIqq_JHhT1wKNtsAQeQ8D3cYyLUl5G2dlE-Mpu6IAqAzZ9-cT3CUEbK_33DeU8vWT$)注意:您可以参加没有时间限制的“实践”考试，也可以参加有时间限制的“考试模式”考试。此外，我发现第二个测试中的一些问题与我从另一个供应商那里得到的另一个测试重叠。
*   [Braincert — 3 次模拟测试(50 道题)](https://urldefense.com/v3/__https:/www.braincert.com/course/22419-AWS-Certified-Machine-Learning-**B-Specialty-Practice-Exams*__;4oCTIw!!EFVe01R3CjU!M4wGIqq_JHhT1wKNtsAQeQ8D3cYyLUl5G2dlE-Mpu6IAqAzZ9-cT3CUEbK_33KXTPmZm$)
*   [亚马逊实践考试](https://aws.amazon.com/certification/certified-machine-learning-specialty/) —您需要登录 [AWS 认证](https://www.aws.training/certification)门户网站并安排新的考试，就像您安排真正的考试一样(MLS-C01)，除非您将选择专业实践考试(MLS-P01)

作为参考，我在模拟测试中得分在 60%到 80%之间，最终接近 90%，但从未超过。最终，我在正式考试中得了 92 分。但是我们不能过度准备——这确实是一次困难的考试。

## 参加真实机器学习 AWS 的后勤—专业考试

当你准备好了，你可以[在这里安排你的考试](https://urldefense.com/v3/__https:/aws.amazon.com/certification/certified-machine-learning-specialty/__;!!EFVe01R3CjU!M4wGIqq_JHhT1wKNtsAQeQ8D3cYyLUl5G2dlE-Mpu6IAqAzZ9-cT3CUEbK_33OJ2tYRk%24) (MLS-C01)。由于新冠肺炎，你可以在家里完成这个考试。

这是我为考试做的。他们付出了巨大的努力来维护测试标准和复制现场测试环境。他们会让你拍照，然后用你的摄像头展示房间。你将不能有任何笔或纸。在你的电脑上运行的软件将会关闭除了它自己以外的所有应用程序并全屏运行。

一个重要的战术提示(至少对我来说)你不能带耳塞，除非你在考试前提出特殊要求。就我个人而言，这对我来说是一场斗争，因为我试图完全专注于考试，而没有听到来自我家人的任何噪音。

# 关于机器学习 AWS —专业考试的最终想法

我希望这篇文章对你准备机器学习专业认证有所帮助。成为人工智能和人工智能领域学术界、技术和商业融合的一部分令人兴奋。我希望你继续你的机器学习之旅，在个人和职业上提升自己。

最后但同样重要的是，我要感谢 [**Capital One**](https://www.capitalone.com/tech/) 是一家出色的技术驱动型公司，专注于为我们的客户和员工做正确的事情，并支持所有员工不断投资于自己。

如果您正在寻找一个使用最新的开源库和框架进行创新的地方，同时利用云、大数据和人工智能的力量，如果您重视支持和协作的环境、不懈的创新和追求卓越，请不要再找了！加入我们吧！我们总是雇佣顶尖人才。您可以在我们的 [Capital One Careers](https://www.capitalonecareers.com/) 门户网站上找到令人兴奋的机会。

祝大家好运！

*人物照片由* [*新闻摄影-www.freepik.com*](http://www.freepik.com)

*最初发表于*[*【https://www.capitalone.com】*](https://www.capitalone.com/tech/machine-learning/advice-for-taking-the-aws-machine-learning-specialty-exam/)*。*

*披露声明:2021 首都一号。观点是作者个人的观点。除非本帖中另有说明，否则 Capital One 不隶属于所提及的任何公司，也不被这些公司认可。使用或展示的所有商标和其他知识产权是其各自所有者的财产。*