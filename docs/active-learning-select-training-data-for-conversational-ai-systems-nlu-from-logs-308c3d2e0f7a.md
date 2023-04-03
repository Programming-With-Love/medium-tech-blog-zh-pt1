# 主动学习:从日志中选择对话式人工智能系统 NLU 的训练数据

> 原文：<https://medium.com/walmartglobaltech/active-learning-select-training-data-for-conversational-ai-systems-nlu-from-logs-308c3d2e0f7a?source=collection_archive---------7----------------------->

对话 AI /虚拟助理系统的响应依赖于在带标签的训练数据上训练的机器学习模型。根据新的现实生活数据不断重新训练虚拟助理模型可以提高它们的性能。然而，注释所有数据或随机样本是昂贵的、缓慢的和低效的。因此，我们希望通过新的主动学习算法(称为 Majority-ensemble CRF:Majority-CRF 的修改版本( [Peshterliev et al .，2019](https://arxiv.org/abs/1810.03450v2) )只标注最有信息量的话语，以降低 NLU(自然语言理解)系统的错误率。

![](img/2f21a06df5a7aa18b6d8ffc983c6c700.png)

Photo credit: [Active Learning](https://cdn.pixabay.com/photo/2017/01/04/07/25/bowling-1951472_1280.jpg)

## 什么是主动学习？

主动学习是一种半监督的机器学习算法，它选择它想要学习的训练数据，以便 NLU 可以用较少的数据提高其准确性。它迭代地与用户交互，以扩展 NLU 类训练数据集，从而提高预测精度。这是一种人在回路中的技术。

在意图分类模型中，具有低置信度/更多不确定性的文本话语示例是重要的。此外，给这些模型喂食/训练它们应该如何被贴上标签也是有益的。更高不确定性的例子将在模型中给出更多的信息增益。自动选择这些例子称为主动学习。

让我们来看看主动学习系统对一个 NLU 类/技能的信息训练数据预测。

## 实施细节:

**预训练模型**:首先，你需要使用标记数据训练你的 NLU 系统，这样它就可以对意图进行分类并提取实体。您的意图分类器模型可以基于模型的集合，包括 Sklearn SVC ( [sklearn.svm](https://scikit-learn.org/stable/modules/generated/sklearn.svm.SVC.html) )、StarSpace:Embed All Things！( [StarSpace](https://arxiv.org/abs/1709.03856) )，BERT ( [BERT](https://github.com/google-research/bert#fine-tuning-with-bert) )，对于实体，seq-seq 标注模型条件随机场(CRF)，BERT ( [BERT](https://github.com/google-research/bert#fine-tuning-with-bert) )。

**预处理**:从未标注的现场话语池中，去除当前 NLU 系统评分为高置信度(0.8)的话语。现在，剩余的池数据被传递到下一步。

**建模**:训练具有{1，2，3 } gram 特征的 hinge、squared、logistic 等不同损失函数的二元分类器。该模型的训练数据将是 NLU 技能/类别的正面和负面数据。在 Vowpal Wabbit 中实现的分类器( [Langford 等人，2007](https://en.wikipedia.org/wiki/Vowpal_Wabbit) )。一旦模型集准备好了，下一步就是过滤。

**过滤**:仅从意图分类器集合中提取多数肯定预测，进入过滤后的话语池。

**评分**:对于过滤池中的每个池话语，分数 *s* 计算为逻辑分类器概率乘以 NER 系统概率。对于给定的 NLU 技能，优先考虑得分最低的话语，作为更具信息性的例子。

**手动标注**:我们对过滤后的池中话语按照得分最小的 *s* 进行优先级排序，并在标注前对话语进行去重处理。对于给定的 NLU 技能的信息训练样本的最小批量(k ): N 个信息话语的范围是从(0≤N≤k)。

**重复**:重复这个主动学习的全过程，直到你获得 NLU 意向的良好转化率。

**影响区域**:通用对话 AI 系统。

参考资料:

[1]佩什泰利耶夫，科尔尼，贾格那塔，基斯，马苏卡斯(2019)。自然语言理解新领域的主动学习。[https://arxiv.org/abs/1810.03450v2](https://arxiv.org/abs/1810.03450v2)

[2] Devlin，j .，Chang，m .，Lee，k .，& Toutanova，K. (2018 年)。BERT:用于语言理解的深度双向转换器的预训练。[https://arxiv.org/abs/1810.04805](https://arxiv.org/abs/1810.04805)

[3]吴，费希尔，乔普拉，亚当斯，博尔德斯，韦斯顿。2017.星际空间:嵌入所有的东西！。[https://arxiv.org/abs/1709.03856](https://arxiv.org/abs/1709.03856)

[4]约翰·兰福德、李立宏和亚历克斯·斯特雷尔。2007.沃帕尔·瓦比特