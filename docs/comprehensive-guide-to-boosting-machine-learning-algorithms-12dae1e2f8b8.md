# 促进机器学习算法的综合指南

> 原文：<https://medium.com/edureka/comprehensive-guide-to-boosting-machine-learning-algorithms-12dae1e2f8b8?source=collection_archive---------2----------------------->

![](img/8ab351c1c6c117532dbda4f6e1fd3f25.png)

随着医疗保健、市场营销、商业等领域的诸多进步，开发更先进、更复杂的机器学习技术已成为一种需求。促进机器学习就是这样一种技术，可以用来解决复杂的、数据驱动的、现实世界的问题。本文完全专注于增强机器学习如何工作，以及如何实现它来提高机器学习模型的效率。

要获得人工智能和机器学习的深入知识，可以报名参加 Edureka 提供的全天候支持和终身访问的直播 ***机器学习工程师硕士项目*** 。

以下是本博客将涵盖的主题列表:

1.  为什么使用升压？
2.  什么是助推？
3.  Boosting 算法是如何工作的？
4.  增压的类型
5.  演示

# 为什么要用 Boosting？

为了解决复杂的问题，我们需要更先进的技术。假设给定一组包含猫和狗图像的图像数据集，要求你建立一个模型，将这些图像分为两个独立的类别。像其他人一样，你将通过使用一些规则来识别图像，如下所示:

1.  图像有尖尖的耳朵:猫
2.  图像有猫形状的眼睛:猫
3.  图像有更大的四肢:狗
4.  图像有锋利的爪子:猫
5.  图像有一个更宽的嘴结构:狗

所有这些规则都有助于我们识别一幅图像是一只狗还是一只猫，然而，如果我们基于一个单独的(单一的)规则来对一幅图像进行分类，预测将是有缺陷的。这些规则中的每一个都被称为弱学习器，因为这些规则不足以将图像分类为猫或狗。

因此，为了确保我们的预测更加准确，我们可以通过使用多数规则或加权平均来组合来自这些弱学习者中的每一个的预测。这构成了一个强大的学习者模型。

在上面的例子中，我们已经定义了 5 个弱学习者，并且这些规则中的大多数(即，5 个学习者中的 3 个预测图像为猫)给出了图像是猫的预测。因此，我们最后的输出是一只猫。

这就给我们带来了一个问题，

# 什么是助推？

*Boosting 是一种集成学习技术，使用一组机器学习算法将弱学习器转换为强学习器，以提高模型的准确性。*

![](img/fef756da127f7252e488bfee68cb9ff1.png)

*什么是 Boosting — Boosting 机器学习— Edureka*

就像我提到的 Boosting 是一种集成学习方法，但集成学习到底是什么？

# 机器学习中的系综是什么？

集成学习是一种通过组合多个学习器来提高机器学习模型性能的方法。与单一模型相比，这种类型的学习可以提高模型的效率和准确性。这就是为什么合奏方法被用来赢得市场领先的比赛，如网飞推荐比赛，卡格尔比赛等。

![](img/f0a0491a3a19b167c3cc5109574e4ebb.png)

*什么是集成学习——助推机器学习——edu reka*

下面我也讨论了增压和装袋的区别。

# 增压与装袋

集成学习可以以两种方式执行:

1.  **顺序合奏，**俗称 ***助推*** ，这里的弱学习者是在训练阶段顺序产生的。通过给先前分类不正确的样本分配较高的权重，模型的性能得到改善。增强的一个例子是 AdaBoost 算法。
2.  **并行合奏**，俗称 ***装袋*** ，这里弱学习者在训练阶段并行产生。通过在自举数据集上并行训练多个弱学习器，可以提高模型的性能。装袋的一个例子是随机森林算法。

# Boosting 算法是如何工作的？

boosting 算法工作的基本原理是生成多个弱学习器，并将它们的预测组合起来形成一个强规则。这些弱规则是通过对数据集的不同分布应用基础机器学习算法而生成的。这些算法为每次迭代生成弱规则。在多次迭代之后，弱学习器被组合以形成强学习器，该强学习器将预测更准确的结果。

![](img/7b0d85061ca74ddec66c8782ddd29f02.png)

*Boosting 算法如何工作——Boosting 机器学习——edu reka*

算法是这样工作的:

**步骤 1:** 基础算法读取数据，并为每个样本观察值分配相等的权重。

**步骤 2:** 识别基础学习者做出的错误预测。在下一次迭代中，这些错误的预测被分配给下一个基础学习者，该基础学习者对这些不正确的预测具有较高的权重。

**第三步:**重复第二步，直到算法能够正确分类输出。

因此，Boosting 的主要目的是更多地关注分类错误的预测。

现在我们知道了 boosting 算法是如何工作的，让我们来理解不同类型的 boosting 技术。

# 增压的类型

有三种主要的方法可以实现增压:

1.  自适应升压或 AdaBoost
2.  梯度推进
3.  XGBoost

我将讨论每种类型背后的基础知识。

## 自适应增压

*   AdaBoost 是通过将几个弱学习器组合成一个强学习器来实现的。
*   AdaBoost 中的弱学习器考虑单个输入特征，并绘制出称为决策树桩的单个分裂决策树。在画出第一个决策图时，每个观察值被同等地加权。
*   来自第一个决策树桩的结果被分析，并且如果任何观察被错误地分类，它们被分配更高的权重。
*   张贴这个，一个新的决策树桩被认为是更重要的观察与更高的权重。
*   同样，如果任何观察值被错误分类，它们被给予更高的权重，并且这个过程继续，直到所有观察值落入正确的类别。
*   Adaboost 可用于分类和基于回归的问题，但它更常用于分类目的。

## 梯度推进

梯度提升也基于顺序集成学习。这里，基本学习器以这样的方式顺序地产生，即当前的基本学习器总是比前一个更有效，即，整个模型随着每次迭代顺序地改进。

这种类型的提升的不同之处在于，错误分类结果的权重不会增加，相反，梯度提升方法试图通过添加新模型来优化前一个学习器的损失函数，该新模型添加了弱学习器，以便减少损失函数。

这里的主要思想是克服前一个学习者预测中的错误。这种类型的升压有三个主要部分:

1.  需要改进的损失函数。
2.  **弱学习器**用于计算预测和形成强学习器。
3.  一个**加法模型**，它将正则化损失函数。

像 AdaBoost 一样，梯度提升也可以用于分类和回归问题。

# XGBoost

XGBoost 是梯度增强方法的高级版本，字面意思是极端梯度增强。XGBoost 由陈天琦开发，属于分布式机器学习社区(DMLC)的范畴。

该算法的主要目的是提高计算速度和效率。梯度下降提升算法以较慢的速率计算输出，因为它们按顺序分析数据集，因此 XGBoost 用于提升或极大地提升模型的性能。

![](img/e816f4a912f83b76dd9955109b1c3cb9.png)

*XGBoost —助推机器学习— Edureka*

XGBoost 旨在关注计算速度和模型效率。XGBoost 提供的主要特性有:

*   并行创建决策树。
*   实现评估大型复杂模型的分布式计算方法。
*   使用核外计算来分析庞大的数据集。
*   实现缓存优化以充分利用资源。

这些是不同类型的增强机器学习算法。为了让事情变得有趣，在下一节中，我们将运行一个演示来看看如何在 Python 中实现 boosting 算法。

# 在 Python 中推进机器学习

一个简短的声明:我将使用 Python 来运行这个演示，所以如果你不知道 Python，你可以浏览下面的文章:

1.  Python 教程——学习 Python 编程的完整指南
2.  如何从头开始学习 Python 3——初学者指南
3.  Python 编程语言—从 Python 基础开始
4.  Python 函数初学者指南

现在是时候把手弄脏并开始编码了。

**问题陈述:**研究蘑菇数据集，建立一个机器学习模型，通过分析蘑菇的特征，可以将蘑菇分类为有毒或无毒。

**数据集描述:**该数据集根据 23 种香菇提供了假设样本的详细描述。每个种类都被分为可食用蘑菇和不可食用(有毒)蘑菇。

**逻辑:**通过使用一种 Boosting 算法来建立机器学习模型，以预测蘑菇是否可以食用。

**第一步:导入需要的包**

```
from sklearn.ensemble import AdaBoostClassifierfrom sklearn.preprocessing import LabelEncoderfrom sklearn.tree import DecisionTreeClassifierimport pandas as pd# Import train_test_split functionfrom sklearn.model_selection import train_test_split#Import scikit-learn metrics module for accuracy calculationfrom sklearn import metrics
```

**第二步:导入数据集**

```
# Load in the datadataset = pd.read_csv('C://Users//NeelTemp//Desktop//mushroomsdataset.csv')
```

**第三步:数据处理**

```
#Define the column namesdataset.columns = ['target','cap-shape','cap-surface','cap-color','bruises','odor','gill-attachment','gill-spacing','gill-size','gill-color','stalk-shape','stalk-root','stalk-surface-above-ring','stalk-surface-below-ring','stalk-color-above-ring','stalk-color-below-ring','veil-type','veil-color','ring-number','ring-type','spore-print-color','population','habitat']for label in dataset.columns:dataset[label] = LabelEncoder().fit(dataset[label]).transform(dataset[label])#Display information about the data setprint(dataset.info())Int64Index: 8124 entries, 6074 to 686Data columns (total 23 columns):target 8124 non-null int32cap-shape 8124 non-null int32cap-surface 8124 non-null int32cap-color 8124 non-null int32bruises 8124 non-null int32odor 8124 non-null int32gill-attachment 8124 non-null int32gill-spacing 8124 non-null int32gill-size 8124 non-null int32gill-color 8124 non-null int32stalk-shape 8124 non-null int32stalk-root 8124 non-null int32stalk-surface-above-ring 8124 non-null int32stalk-surface-below-ring 8124 non-null int32stalk-color-above-ring 8124 non-null int32stalk-color-below-ring 8124 non-null int32veil-type 8124 non-null int32veil-color 8124 non-null int32ring-number 8124 non-null int32ring-type 8124 non-null int32spore-print-color 8124 non-null int32population 8124 non-null int32habitat 8124 non-null int32dtypes: int32(23)memory usage: 793.4 KB
```

**第四步:数据拼接**

```
X = dataset.drop(['target'], axis=1)Y = dataset['target']X_train, X_test, Y_train, Y_test = train_test_split(X, Y, test_size=0.3)
```

**第五步:建立模型**

```
model = DecisionTreeClassifier(criterion='entropy', max_depth=1)AdaBoost = AdaBoostClassifier(base_estimator=model, n_estimators=400, learning_rate=1)
```

在上面的代码片段中，我们实现了 AdaBoost 算法。“AdaBoostClassifier”函数有三个重要参数:

*   base_estimator:默认情况下，基本估计器(弱学习器)是决策树
*   n_estimator:该字段指定要使用的基础学习者的数量。
*   learning_rate:这个字段指定了学习率，我们已经将其设置为默认值，即 1。

```
#Fit the model with training databoostmodel = AdaBoost.fit(X_train, Y_train)
```

**第六步:模型评估**

```
#Evaluate the accuracy of the modely_pred = boostmodel.predict(X_test)predictions = metrics.accuracy_score(Y_test, y_pred)#Calculating the accuracy in percentageprint('The accuracy is: ', predictions * 100, '%')The accuracy is: 100.0 %
```

我们收到了 100%的准确率，这是完美的！

就这样，我们结束了这个推进机器学习的博客。如果你想了解更多关于机器学习的知识，你可以看看这些博客:

1.  什么是机器学习？面向初学者的机器学习
2.  初学者的机器学习教程
3.  机器学习算法
4.  机器学习的 10 大应用:机器学习在日常生活中的应用

一旦你知道了机器学习的基础知识，你是否想知道如何进步？看一看 Edureka 的机器学习认证，它将帮助你走上正确的道路，在这个迷人的领域取得成功。学习机器学习的基础，机器学习的步骤和方法，包括无监督和有监督的学习，数学和启发式方面，以及创建算法的动手建模。你会为机器学习工程师的职位做好准备。

如果你正试图在深度学习方面发展你的职业生涯，请查看我们的深度学习课程。本课程为学生提供了推进职业发展所需的工具、技术和工具的相关信息。

如果你想查看更多关于人工智能、DevOps、道德黑客等市场最热门技术的文章，你可以参考 Edureka 的官方网站。

请留意本系列中的其他文章，它们将解释深度学习的各个其他方面。

> *1。* [*张量流教程*](/edureka/tensorflow-tutorial-ba142ae96bca)
> 
> *2。* [*PyTorch 教程*](/edureka/pytorch-tutorial-9971d66f6893)
> 
> *3。* [*感知器学习算法*](/edureka/perceptron-learning-algorithm-d30e8b99b156)
> 
> *4。* [*神经网络教程*](/edureka/neural-network-tutorial-2a46b22394c9)
> 
> *5。* [*什么是反向传播？*](/edureka/backpropagation-bd2cf8fdde81)
> 
> *6。* [*卷积神经网络*](/edureka/convolutional-neural-network-3f2c5b9c4778)
> 
> *7。* [*胶囊神经网络*](/edureka/capsule-networks-d7acd437c9e)
> 
> *8。* [*递归神经网络*](/edureka/recurrent-neural-networks-df945afd7441)
> 
> *9。* [*自动编码器教程*](/edureka/autoencoders-tutorial-cfdcebdefe37)
> 
> *10。* [*受限玻尔兹曼机教程*](/edureka/restricted-boltzmann-machine-tutorial-991ae688c154)
> 
> *11。*[*py torch vs tensor flow*](/edureka/pytorch-vs-tensorflow-252fc6675dd7)
> 
> *12。* [*深度学习用 Python*](/edureka/deep-learning-with-python-2adbf6e9437d)
> 
> *13。* [*人工智能教程*](/edureka/artificial-intelligence-tutorial-4257c66f5bb1)
> 
> *14。* [*张量流图像分类*](/edureka/tensorflow-image-classification-19b63b7bfd95)
> 
> *15。* [*人工智能应用*](/edureka/artificial-intelligence-applications-7b93b91150e3)
> 
> 16。 [*如何成为一名人工智能工程师？*](/edureka/become-artificial-intelligence-engineer-5ac2ede99907)
> 
> 17。 [*问学*](/edureka/q-learning-592524c3ecfc)
> 
> 18。 [*Apriori 算法*](/edureka/apriori-algorithm-d7cc648d4f1e)
> 
> *19。* [*马尔可夫链与 Python*](/edureka/introduction-to-markov-chains-c6cb4bcd5723)
> 
> 20。 [*人工智能算法*](/edureka/artificial-intelligence-algorithms-fad283a0d8e2)
> 
> *21。* [*最适合机器学习的笔记本电脑*](/edureka/best-laptop-for-machine-learning-a4a5f8ba5b)
> 
> *二十二。*[*12 大人工智能工具*](/edureka/top-artificial-intelligence-tools-36418e47bf2a)
> 
> *23。* [*人工智能面试问题*](/edureka/artificial-intelligence-interview-questions-872d85387b19)
> 
> *24。*[*Theano vs tensor flow*](/edureka/theano-vs-tensorflow-15f30216b3bc)
> 
> *25。* [*什么是神经网络？*](/edureka/what-is-a-neural-network-56ae7338b92d)
> 
> *26。* [*模式识别*](/edureka/pattern-recognition-5e2d30ab68b9)
> 
> *27。* [*人工智能中的阿尔法贝塔修剪*](/edureka/alpha-beta-pruning-in-ai-b47ee5500f9a)

*原载于 2022 年 1 月 5 日 https://www.edureka.co*[](https://www.edureka.co/blog/boosting-machine-learning/)**。**