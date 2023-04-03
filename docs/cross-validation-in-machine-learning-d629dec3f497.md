# 什么是机器学习中的交叉验证？

> 原文：<https://medium.com/edureka/cross-validation-in-machine-learning-d629dec3f497?source=collection_archive---------0----------------------->

![](img/1205fdb91b05c0a726be035c8e0576e3.png)

机器学习中验证技术的问题在于，它没有给出学习者将如何推广到看不见的数据的任何指示。这就是交叉验证发挥作用的地方。本文涵盖了机器学习中交叉验证的基本概念，本文讨论了以下主题:

1.  什么是交叉验证？

2.交叉验证的类型

*   k 倍交叉验证
*   保持方法
*   遗漏交叉验证
*   遗漏一项交叉验证

3.交叉验证应用编程接口

4.如何度量模型的偏差方差？

5.交叉验证的局限性

6.交叉验证应用程序

# 什么是交叉验证？

对于机器学习中的任何模型，*如果用独立的数据集对模型进行测试，则被认为是最佳实践。*通常情况下，任何预测模型都是基于一个已知的数据集，即训练集。

但在现实生活中，将使用完全不同和独特的数据集测试该模型的效率和准确性。在这种情况下，您希望您的模型足够高效，或者至少与它为训练集显示的效率相当。基本上，这种测试被称为机器学习中的交叉验证，因此它适用于未来的任何模型。

我们也可以称其为一种技术，用于**断言统计模型如何推广到独立的数据集**。既然我们已经知道了交叉验证的含义，让我们试着用简单的术语来理解交叉验证。

交叉验证的基本目的是评估模型在未知数据集下的表现。例如，你试图在一个空的目标中得分。这看起来很容易，你甚至可以从很远的地方得分。但是真正的考验是从一个守门员和一群防守队员开始的。这就是为什么你需要在一场真正的比赛中接受训练，面对所有的压力，仍然要进球。

类似地，统计模型的训练方式使得它在使用交叉验证处理其他未知数据集时效率更高。

# 交叉验证的类型

机器学习中有两种类型的交叉验证技术。

1.  **穷举交叉验证**——该方法基本上是通过将原始数据集划分为训练集和验证集，用尽一切可能的方式对模型进行测试。示例:遗漏交叉验证，遗漏交叉验证。
2.  **非穷尽性交叉验证**——该方法不将原始数据集分解为所有可能的排列和组合。示例:K 倍交叉验证，保持方法。

让我们更详细地了解机器学习中各种类型的交叉验证。

## k-折叠交叉验证

在机器学习中，永远没有足够的数据来训练模型。即使这样，如果我们删除一些数据，它也会对机器学习模型造成过度拟合的威胁。如果没有为训练阶段提供足够的数据，也可能无法识别主要模式。

通过减少数据，我们也面临着由于偏差引起的误差而降低精确度的风险。为了克服这个问题，我们需要一种方法来提供足够的训练数据和一些测试数据。k 倍交叉验证正是这样做的。

它是如何工作的？

在这种交叉验证技术中，数据被分成 k 个子集。我们从这些数据中选择一个子集，并将其作为模型的验证集。我们保留 k-1 个子集用于训练模型。

对所有“k 次试验”的误差估计进行平均，以获得模型的有效准备状态。每个 k 子集将在验证集中至少出现一次。它也包含在 k-1 训练集中至少一次。这大大降低了由偏置引起的误差。它还减少了方差，因为在验证中使用了 k 个子集的每一个。

**分层 K 倍交叉验证**

在这种技术中，k 倍交叉验证有一点小小的变化。它会发生变化，使得每个折叠在整个集合中具有每个目标类的大约相等百分比的样本。在预测问题的情况下，平均响应值在所有折叠中近似相等。

在某些情况下，响应变量存在很大的不平衡。让我们用一个例子来理解这一点。在房屋定价问题中，一些房屋的价格可能比其他房屋的价格高得多。此外，在分类问题中，样本的负样本可能比正样本多。为了解决这种差异，我们遵循机器学习中的分层 k-fold 交叉验证技术。

## 保持方法

这是所有方法中最简单的交叉验证方法。在这种方法中，我们将数据点随机分配给两个数据集。在这种情况下，大小无关紧要。

这背后的基本思想是从您的训练集中删除一部分，并使用它从基于其余数据训练的模型中获得预测。这种方法有很大的差异，因为它只需要运行一次就可以完成所有这些。它也可能给出误导性的结果。

## 遗漏交叉验证

在这种方法中， **p** 个数据点被排除在训练数据之外。假设数据集中有 **m** 个数据点，那么 **m-p** 个数据点用于训练阶段。并且将 **p** 数据点保存为验证集。

这种技术相当详尽，因为对原始数据集中所有可能的组合重复上述过程。为了检查模型的整体有效性，对所有试验的误差进行平均。

这在计算上变得不可行，因为模型需要为所有可能的组合和相当大的 **p** 进行训练和验证。

## 留一交叉验证

这种交叉验证的方法类似于不考虑 p 的交叉验证，但唯一的区别是在这种情况下 **p = 1** 。它实际上节省了很多时间，这是一个很大的优势。

尽管如果样本数据太大，仍然会花费很多时间。但是它仍然比留 p-out 交叉验证法要快。

既然我们已经讨论了不同类型的交叉验证技术，让我们来看看交叉验证 API。

# 交叉验证 API

我们不必手动实现交叉验证，Python 中的 Scikit-Learn 库提供了一个简单的实现，可以相应地拆分数据。根据不同的交叉验证策略，可以使用交叉验证迭代器。

*   k-fold 交叉验证:KFold() scikit-learn 类
*   Leave-one-out 交叉验证:LeaveOneOut() scikit-learn 类
*   Leave-p-out 交叉验证:LeavePOut() scikit-Learn 类
*   分层 K 折叠交叉验证:StratifiedKFold() scikit-learn 类

例如，让我们尝试使用 python 中的 Kfold 来创建训练集和验证集。

```
**from** numpy **import** array
**from** sklearn.model_selection **import** KFold# sampling the data
data **=** array([0.10, 0.22, 0.31, 0.43, 0.52, 0.63,0.72,0.85,0.92,0.99])# Splittinf the data
kfold **=** KFold(3, True, 1)# enumerating the splits
**for** train, test **in** kfold.split(data):
print('train: %s, test: %s' **%** (data[train], data[test]))
```

**输出:**

![](img/0d5a86f7f32f7d9302b0b88b1022f834.png)

类似地，我们可以根据需求和数据类型选择其他交叉验证迭代器。现在让我们试着理解如何计算模型的偏差和方差。

# 如何度量模型的偏差-方差

如果做 k 重交叉验证，会得到 k 个不同的估计误差。在理想的情况下，这些误差总和为零，但是得到这样的结果是非常不可能的。为了得到偏差，我们取所有估计误差的平均值。

为了计算模型的方差，我们取所有误差的标准差。如果我们得到一个较低的标准差值，这意味着我们的模型不会随着不同的训练数据集而有很大的变化。

重点应该是保持模型的偏差和方差之间的平衡。这可以通过将方差减小到最小并控制偏差来实现。这种权衡通常会产生更好的预测模型。

但是交叉验证也有一些限制。让我们看看交叉验证的各种限制。

# 交叉验证的局限性

1.  在理想情况下，交叉验证会产生最佳结果。但是在**数据**不一致的情况下，结果可能会大相径庭。模型会遇到什么样的数据是相当不确定的。
2.  预测建模通常需要在数据方面进行**进化**，这可以极大地改变训练和验证集。
3.  结果可能**因数据集**的特征而异。假设我们制作了一个预测模型来检测一个人的疾病，然后用一组特定的人群来训练它。它可能随一般人群而变化，导致不一致和效率降低。

以下是交叉验证面临的一些限制:

# 交叉验证应用程序

1.  我们可以用它来比较一组预测建模过程的性能。
2.  交叉验证在医学研究领域表现出色。
3.  它可以用于荟萃分析，因为许多数据分析师已经在使用交叉验证。

对于防止机器学习模型过拟合和欠拟合的过度应用，下面列出了交叉验证的几个其他应用:

1.  我们可以用它来比较一组预测建模过程的性能。
2.  交叉验证在医学研究领域表现出色。
3.  它可以用于荟萃分析，因为许多数据分析师已经在使用交叉验证。

这就把我们带到了本文的结尾，在这里我们学习了机器学习中的交叉验证。我希望你清楚本教程中与你分享的所有内容。如果你想查看更多关于 Python、DevOps、Ethical Hacking 等市场最热门技术的文章，那么你可以参考 [Edureka 的官方网站。](https://www.edureka.co/blog/?utm_source=medium&utm_medium=content-link&utm_campaign=cross-validation-in-machine-learning)

请留意本系列中的其他文章，它们将解释数据科学的各个方面。

> *1。* [*数据科学教程*](/edureka/data-science-tutorial-484da1ff952b)
> 
> *2。* [*数据科学的数学与统计*](/edureka/math-and-statistics-for-data-science-1152e30cee73)
> 
> *3。*[*R 中的线性回归*](/edureka/linear-regression-in-r-da3e42f16dd3)
> 
> *4。* [*数据科学教程*](/edureka/data-science-tutorial-484da1ff952b)
> 
> *5。*[*R 中的逻辑回归*](/edureka/logistic-regression-in-r-2d08ac51cd4f)
> 
> *6。* [*分类算法*](/edureka/classification-algorithms-ba27044f28f1)
> 
> *7。* [*随机森林*](/edureka/random-forest-classifier-92123fd2b5f9)
> 
> *8。* [*决策树中的 R*](/edureka/a-complete-guide-on-decision-tree-algorithm-3245e269ece)
> 
> *9。* [*机器学习入门*](/edureka/introduction-to-machine-learning-97973c43e776)
> 
> 10。 [*朴素贝叶斯在 R*](/edureka/naive-bayes-in-r-37ca73f3e85c)
> 
> *11。* [*统计与概率*](/edureka/statistics-and-probability-cf736d703703)
> 
> *12。* [*如何创建一个完美的决策树？*](/edureka/decision-trees-b00348e0ac89)
> 
> 13。 [*关于数据科学家角色的 10 大误区*](/edureka/data-scientists-myths-14acade1f6f7)
> 
> *14。*[*5 大机器学习算法*](/edureka/machine-learning-algorithms-29eea8b69a54)
> 
> 15。 [*数据分析师 vs 数据工程师 vs 数据科学家*](/edureka/data-analyst-vs-data-engineer-vs-data-scientist-27aacdcaffa5)
> 
> *16。* [*人工智能的类型*](/edureka/types-of-artificial-intelligence-4c40a35f784)
> 
> *17。*[*R vs Python*](/edureka/r-vs-python-48eb86b7b40f)
> 
> *18。* [*人工智能 vs 机器学习 vs 深度学习*](/edureka/ai-vs-machine-learning-vs-deep-learning-1725e8b30b2e)
> 
> *19。* [*机器学习项目*](/edureka/machine-learning-projects-cb0130d0606f)
> 
> *20。* [*数据分析师面试问答*](/edureka/data-analyst-interview-questions-867756f37e3d)
> 
> *21。* [*面向非程序员的数据科学和机器学习工具*](/edureka/data-science-and-machine-learning-for-non-programmers-c9366f4ac3fb)
> 
> *22。* [*十大机器学习框架*](/edureka/top-10-machine-learning-frameworks-72459e902ebb)
> 
> *23。* [*用于机器学习的统计*](/edureka/statistics-for-machine-learning-c8bc158bb3c8)
> 
> *24。* [*随机森林中的 R*](/edureka/random-forest-classifier-92123fd2b5f9)
> 
> *25。* [*广度优先搜索算法*](/edureka/breadth-first-search-algorithm-17d2c72f0eaa)
> 
> *26。*[*R 中的线性判别分析*](/edureka/linear-discriminant-analysis-88fa8ad59d0f)
> 
> 27。 [*机器学习的先决条件*](/edureka/prerequisites-for-machine-learning-68430f467427)
> 
> 28。 [*互动 WebApps 使用 R 闪亮*](/edureka/r-shiny-tutorial-47b050927bd2)
> 
> *29。* [*十大机器学习书籍*](/edureka/top-10-machine-learning-books-541f011d824e)
> 
> 三十岁。 [*无监督学习*](/edureka/unsupervised-learning-40a82b0bac64)
> 
> *31.1* [*0 最佳数据科学书籍*](/edureka/10-best-books-data-science-9161f8e82aca)
> 
> 32。 [*监督学习*](/edureka/supervised-learning-5a72987484d0)

*原载于 2019 年 12 月 23 日 https://www.edureka.co*[](https://www.edureka.co/blog/cross-validation-in-machine-learning/)**。**