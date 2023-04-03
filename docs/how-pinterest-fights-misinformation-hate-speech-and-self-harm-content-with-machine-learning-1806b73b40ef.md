# Pinterest 如何用机器学习对抗错误信息、仇恨言论和自残内容

> 原文：<https://medium.com/pinterest-engineering/how-pinterest-fights-misinformation-hate-speech-and-self-harm-content-with-machine-learning-1806b73b40ef?source=collection_archive---------2----------------------->

Vishwakarma Singh |信任与安全机器学习主管& Dan Lee |信任与安全数据科学家主管

Pinterest 是一个灵感网络，人们在这里寻找积极和新的想法，没有任何有害内容的灵感。作为一个拥有数千亿个 pin 的视觉发现引擎，我们必须使用最新的机器学习来快速消除有害内容，并反击那些试图传播有害内容的人。

为了保持 Pinterest 的安全性和启发性，我们通过版主调查和自动化系统主动对违反我们的[社区准则](https://policy.pinterest.com/en/community-guidelines)的内容采取行动，此外还通过[实时规则引擎](/pinterest-engineering/fighting-spam-with-guardian-a-real-time-analytics-and-rules-engine-938e7e61fa27)打击垃圾邮件。我们的机器学习模型可以识别违反我们政策的内容，从健康误导到仇恨言论、自残和图形暴力。多年来，我们还利用 Spark、LSH 和 TensorFlow 对[检测类似图像的能力进行了改进](/pinterest-engineering/detecting-image-similarity-using-spark-lsh-and-tensorflow-618636afc939)，这种技术已被应用于信任和安全工作，以采取大规模行动。

**自 2019 年秋季引入该技术以来，使用机器学习模型在报告前自动检测不安全的内容，每印象违反政策的内容报告下降了 52%。此外，自 2019 年 4 月以来，自残内容的报告减少了 80%。**

与错误信息等领域的斗争是复杂的，并且总是在不断发展，因此我们不断努力改进我们的技术和政策方法，以通过混合使用人类和机器来保持我们的平台安全和鼓舞人心。

# 使用机器学习模型执行策略

当跨 pin 执行策略时，我们将所有具有[相似图像](/pinterest-engineering/detecting-image-similarity-using-spark-lsh-and-tensorflow-618636afc939)的 pin 分组在一起，并通过称为图像签名的唯一散列来识别它们。我们的机器学习模型为每个图像签名生成分数，这些分数存储在基于 rocksDB 的键值存储[中，用于在线服务。然后，我们基于这些分数对具有相同图像签名的所有 pin 应用相同的实施决定。](https://github.com/pinterest/rocksplicator)

由于 Pinners 通常将主题相关的 pin 保存在一起，作为关于食谱等主题的板上的集合，我们也有一个机器学习模型来为板产生分数。我们使用模型的分数来实施董事会过滤。

今天，我们的模型检测和过滤违反政策的成人内容、仇恨活动、医疗错误信息、药物、自残和图形暴力。我们计划在下一次模型迭代中明确地建模其他类别。

我们有互补的批处理和在线模型来主动检测违反策略的 pin。当创建新的 Pin 时，我们的技术会查找 Pin 的图像签名，以查看是否存在批量模型分数。如果它存在于商店中，则使用相应的分数来强制执行，否则我们依靠在线模型生成的分数。强制执行系统的鸟瞰图如图 1 所示。

![](img/f869345e3b30e06ac6fa1d6c1ffc7038.png)

# 机器学习模型

下面是我们检测有害引脚和电路板的机器学习模型的概述。

# Pin 批量模型

我们的 Pin 批处理评分模型是一个前馈网络，如图 2 所示，它输出六个建模违规类别和一个安全类别的分布。该模型消耗两个特征: [PinSage 嵌入](/pinterest-engineering/pinsage-a-new-graph-convolutional-neural-network-for-web-scale-recommender-systems-88795a107f48)和通过光学字符识别(OCR)提取的图像文本。PinSage 是基于其[关键字](/pinterest-engineering/understanding-pins-through-keyword-extraction-40cf94214c18)和[图像嵌入](/pinterest-engineering/unifying-visual-embeddings-for-visual-search-at-pinterest-74ea7ea103f0)的引脚的强信息表示，其通过使用引脚和电路板的二分图的图形卷积网络聚集。我们使用标准的监督学习技术在 Jupyter 的单个 GPU 实例上进行交互式训练。

![](img/55912ee412a03865a8cfd7b2aec13c0f.png)

我们的训练集包括数百万人工审核的 pin，包括用户报告和来自语料库的基于主动模型的采样。我们的信任和安全运营团队分配类别并对违规内容采取行动，这些内容构成了培训和评估我们模型的标签。

每天使用 Spark、Spark SQL 和 PySpark Pandas UDFs 进行推理，以对我们的数十亿个 pin 的整个语料库进行评分。我们推理工作的核心如图 3 所示。

![](img/c085c1e69d8632a97a1368ae628f905e.png)

# Pin 在线模型

为了优化 Pin 通过离线管道流向我们的评分工作流所需的时间，我们采用了 Lambda 架构和 Pin 批处理模型的在线变体(具有相同的架构),后者使用来自 PinSage 在线变体的嵌入。我们的在线模型实时生成新 pin 的分类分数分布，从而使我们能够立即阻止违反政策的 pin 的分布。在线模型以性能换取速度——在线 PinSage 不使用 Pin-Board 图，因此与批处理版本相比，它不太精确，但几乎是实时可用的。由 Kafka 中的事件触发的推理在使用 TensorFlow Java 库的 Flink 作业中执行，并且输出分数被持久化到我们的内部 Galaxy 平台以供存储和服务。

# 纸板批量模型

我们使用仅使用 PinSage 嵌入进行训练的 Pin 模型来生成公告板的内容安全分数。我们通过聚集保存到其中的最新管脚的管脚群嵌入来为每个电路板构建一个嵌入。这些电路板嵌入也在 PinSage 嵌入空间中，因此我们将它们输入到基于 PinSage 的 Pin 模型中，以获得每个电路板的分类分数分布。这允许我们识别违反政策的董事会，而不需要为董事会训练模型。

我们还将纸板分数扇出到图像签名，并使用它们来过滤违反策略的 pin。图像签名分数分布是包含属于图像签名的图钉的纸板分数的类别平均值，如图 4 所示。

![](img/35328827b19f5841943a7283fbc28a76.png)

# 尺寸

不安全的内容在 Pinterest 的展示中只占很小的比例，这使得精确测量流行程度变得困难且昂贵。我们衡量成功的一种方法是查看用户报告，其中包含确认的违规案例，以提供对不安全内容峰值的可见性，这通常是由于新类型的趋势内容可能对我们的模型构成挑战。

# 结论

Pinterest 致力于让互联网成为一个更积极、更安全的地方。我们在机器学习方面进行了大量投资，以大规模检测和执行有问题的内容，利用多种模型来保护我们的用户免受有害体验的影响。我们一直致力于开发技术和执行政策，以创造一个积极的环境，符合人们来到 Pinterest 的目的——获得灵感。这是在这些积极体验下运行的工作，确保有害内容被识别和删除，激励内容上升到顶部..

![](img/e9b591bc19a8dcbae6ad2baa6c6ad95d.png)

在高层次上，我们的信任和安全工程组织由信号、工具和平台团队组成。由工程师、数据科学家和应用科学家组成的信号团队开发并推出机器学习信号，平台团队构建基础设施以确保大规模实施，工具团队构建工具以使主题专家能够准确地审查和标记内容。

我们一直在努力改进我们的内容安全技术和实践，并与业内其他公司合作。我们最近举办了一次[信任和安全机器学习峰会](https://pinterestmachinelearning.splashthat.com/)，来自 Youtube/Google、脸书、LinkedIn、Snap、Yelp 和微软等公司的行业同事参加了会议，数百人参加了会议。如果你对 Pinterest Engineering 的最新新闻和事件感兴趣，请在 Twitter 上关注我们@ [PinterestEng](https://twitter.com/PinterestEng) 。如果您有兴趣加入我们的团队，致力于解决信任和安全工程领域的这些挑战，请访问我们的[职业网站](https://www.pinterestcareers.com/)。

# 感谢

非常感谢宋远方、李玟·臧、奥拉迪波·奥西特鲁、凯特·弗莱明和丹尼斯·霍特对本文的贡献！感谢哈里·沙曼斯基帮助发表这篇博文！