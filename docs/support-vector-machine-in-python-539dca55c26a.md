# Python 中的支持向量机

> 原文：<https://medium.com/edureka/support-vector-machine-in-python-539dca55c26a?source=collection_archive---------0----------------------->

![](img/fd16637aa3b1eb7c966f337b2c9e11eb.png)

机器学习是计算机时代的新时代革命。我们可以使用正确的数据集和相关算法来处理数据，以获得最佳结果，从而完成人们只能梦想的任务。在本文中，我们将使用 python 实现机器学习中的一种分类算法，即 Python 中的支持向量机。本博客涵盖了以下主题:

*   机器学习导论
*   什么是支持向量机
*   SVM 是如何工作的？
*   SVM 核
*   支持向量机用例
*   SVM 的例子

# 机器学习导论

机器学习是向机器输入足够的数据来训练和预测使用海湾算法的可能结果的过程。输入机器的数据越多，机器的效率就越高。让我们用一个真实的例子来理解这一点。

![](img/1de12f5385ce10a153f7c139dccfaed2.png)

我相信你们大多数人都知道在任何重大比赛前对任何运动的预测。在这种情况下，让我们举一个足球点球环节的例子。

考虑到之前表现的数据，假设门将在最近扑出的 50 个点球中，所有的点球都扑到了他的右边。这些数据对于预测他是否会挽救接下来的点球面至关重要。还有其他因素需要考虑。

另一个例子是我们在网上冲浪时得到的建议，我们以前选择的数据经过处理，给我们最喜欢的内容，我们最有可能观看。

总之，机器学习不仅仅是给机器提供大量的数据，还有许多过程、算法和决定性因素来获得最佳结果。

在这篇博客中，我们将通过一个这样的支持向量机算法来理解它如何与 python 一起工作。在此之前，让我们也看看机器学习的类型

# 机器学习的类型

*   **监督学习** —学习以受控的方式进行，以相应地监督结果。顾名思义，它是在某种程度上被监督的，机器学习用户想要它学习的东西。
*   **无监督学习**——在这种情况下，机器只是探索给它的数据。数据有时是未标记和未分类的，机器在没有任何监督的情况下做出可能的参考和预测。
*   **强化学习**——基本意思是强制执行一种行为模式。机器需要在强化学习中建立系统的方法模式。

# 什么是支持向量机

支持向量机最早出现在 20 世纪 60 年代，后来在 20 世纪 90 年代得到了发展。这是一种监督学习的机器学习分类算法，由于其极其高效的结果，如今已经变得非常流行。

SVM 的实现方式与其他机器学习算法略有不同。它能够执行分类、回归和异常值检测。

支持向量机是一种由分离超平面形式化设计的判别分类器。它将示例表示为空间中的点，这些点被映射，使得不同类别的点被尽可能宽的间隙分隔开。除此之外，SVM 还可以执行非线性分类。让我们来看看支持向量机是如何工作的。

# SVM 的优势

*   在高维空间中有效
*   在维数大于样本数的情况下仍然有效
*   在决策函数中使用一个训练点子集，以提高记忆效率
*   可以为决策函数指定不同的核函数，这也使其具有通用性

# **SVM 的劣势**

*   如果特征的数量远大于样本的数量，在选择核函数和正则项时避免过拟合是至关重要的。
*   支持向量机不直接提供概率估计，这些是使用五重交叉验证计算的。

# SVM 是如何工作的？

支持向量机的主要目标是以最好的方式分离给定的数据。当分离完成后，最近的点之间的距离称为边距。该方法是在给定的数据集中选择一个在支持向量之间具有最大可能间隔的超平面。

![](img/ec6102c1b705d972b3538ef0c364f1f8.png)

为了在给定集合中选择最大超平面，支持向量机遵循以下集合:

*   生成超平面，以尽可能好的方式分离类
*   从最近的数据点中选择具有最大分离的右超平面

![](img/5d45a072a763ac67aa0c9c9752d50319.png)

# **如何处理不可分的非线性平面？**

在某些情况下，超平面可能不是很有效。在这些情况下，支持向量机使用内核技巧*将输入转换到更高维度的空间*。这样，分离这些点就变得更容易了。让我们来看看 SVM 玉米粒。

# SVM 核

SVM 核基本上是在低维空间中增加了更多的维度，从而更容易分离数据。它通过使用核技巧增加更多维度，将不可分问题转化为可分问题。支持向量机实际上是由内核实现的。内核技巧有助于制造更准确的分类器。让我们看看支持向量机中不同的核。

**线性核** —线性核可用作任意两个给定观察值之间的标准点积。两个向量之间的乘积是每对输入值的乘积之和。下面是线性核方程。

![](img/59b7abde1c704f6f6de468d512cfc92e.png)

**多项式内核** —它是线性内核的一种相当一般化的形式。它可以区分弯曲或非线性输入空间。以下是多项式核方程。

![](img/63e03896124131650de00ee8eb03d169.png)

**径向基函数核** —径向基函数核常用于 SVM 分类，它可以映射无限维空间。下面是 RBF 核方程。

![](img/ec5462c27e55064fa055276de24d9cba.png)

# 支持向量机用例

*   人脸检测
*   文本和超文本分类
*   图像分类
*   生物信息学
*   蛋白质折叠和远程同源性检测
*   手写识别
*   广义预测控制

# SVM 的例子

现在让我们尝试使用 scikit-learn 来实现我们到目前为止在 python 中所学的内容。要制作支持向量机分类器，我们将遵循以下步骤。

*   加载数据
*   探索数据
*   拆分数据
*   生成模型
*   模型评估

## **加载数据**

我们正在使用 sklearn 库中的癌症数据集，我们将制作一个分类器来预测癌症是恶性还是良性。我们可以用以下方式加载数据集。

```
from sklearn import datasets

cancer_data = datasets.load_breast_cancer()
print(cancer_data.data[5]
```

**输出**:

![](img/3bd5f5ec352841c44d37b96d54090c90.png)

在此之后，我们将探索数据。看看数据集中的各种值。检查目标变量等。

## **探索数据**

该形状意味着该数据集有 569 行和 30 列。

```
print(cancer_data.data.shape) 
#target set 
print(cancer_data.target)
```

**输出**:

![](img/9fe4144f131ea11aa6f889260e8eb896.png)

其中，0 代表恶性，1 代表良性。

## **拆分数据**

我们将数据集分为训练集和测试集，以获得准确的结果。之后，我们将使用 train_test_split()函数拆分数据。我们将需要 3 个参数，如下例所示。训练模型、目标和测试集大小的特征。

```
from sklearn.model_selection import train_test_split

cancer_data = datasets.load_breast_cancer()

X_train, X_test, y_train, y_test = train_test_split(cancer_data.data, cancer_data.target, test_size=0.4,random_state=109)
```

## **生成模型**

为了生成模型，我们将首先从 sklearn 导入 SVM 模块，通过传递自变量内核作为线性内核，在 svc()中创建支持向量分类器。

然后，我们将使用 set()训练数据集，并使用 predict()函数进行预测。

```
from sklearn import svm
#create a classifier
cls = svm.SVC(kernel="linear")
#train the model
cls.fit(X_train,y_train)
#predict the response
pred = cls.predict(X_test)
```

## 评估模型

这样，我们可以预测模型或分类器预测患者是否患有心脏病的准确性。因此，我们将计算评估的准确度、召回率和精确度。

```
from sklearn import metrics
#accuracy
print("acuracy:", metrics.accuracy_score(y_test,y_pred=pred))
#precision score
print("precision:", metrics.precision_score(y_test,y_pred=pred))
#recall score
print("recall" , metrics.recall_score(y_test,y_pred=pred))
print(metrics.classification_report(y_test, y_pred=pred))
```

**输出**:

![](img/5c10c0edbe072d9ab90e070f74de7c8e.png)

我们得到的准确度、精确度和召回值分别为 0.96、0.96 和 0.97，这是极不可能的。因为我们的数据集非常具有描述性和决定性，所以我们能够得到如此精确的结果。通常，任何高于 0.7 的准确度分数都是好分数。

让我们看另一个例子来理解我们如何以不同的方式使用支持向量机分类算法。

# **用支持向量机进行字符识别**

在本例中，我们将使用现有的数字数据集并训练分类器。在此之后，我们将使用分类器来预测一个数字，并将图像绘制得更加清晰。

```
import matplotlib.pyplot as plt
from sklearn import datasets
from sklearn import svm
#loading the dataset
letters = datasets.load_digits()
#generating the classifier
clf = svm.SVC(gamma=0.001, C=100)
#training the classifier
X,y = letters.data[:-10], letters.target[:-10]
clf.fit(X,y)
#predicting the output 
print(clf.predict(letters.data[:-10]))
plt.imshow(letters.images[6], interpolation='nearest')
plt.show()
```

**输出**:

![](img/8a11fa8c00c9bf8d660cc05bd51cdbd1.png)

为了提高精度，我们可以改变 SVC 参数中的 gamma 值或 C 值，但这也会影响速度。如果我们增加伽玛值，精确度会降低，但速度会增加。

这就把我们带到了本文的结尾，在这里我们学习了如何在 Python 中使用支持向量机。我希望你清楚本教程中与你分享的所有内容。

如果你想查看更多关于人工智能、DevOps、道德黑客等市场最热门技术的文章，那么你可以参考 [Edureka 的官方网站。](https://www.edureka.co/blog/?utm_source=medium&utm_medium=content-link&utm_campaign=support-vector-machine-in-python)

请留意本系列中的其他文章，它们将解释 Python 和数据科学的各个方面。

> 1.[Python 中的机器学习分类器](/edureka/machine-learning-classifier-c02fbd8400c9)
> 
> 2. [Python Scikit-Learn 备忘单](/edureka/python-scikit-learn-cheat-sheet-9786382be9f5)
> 
> 3.[机器学习工具](/edureka/python-libraries-for-data-science-and-machine-learning-1c502744f277)
> 
> 4.[用于数据科学和机器学习的 Python 库](/edureka/python-libraries-for-data-science-and-machine-learning-1c502744f277)
> 
> 5.[Python 中的聊天机器人](/edureka/how-to-make-a-chatbot-in-python-b68fd390b219)
> 
> 6. [Python 集合](/edureka/collections-in-python-d0bc0ed8d938)
> 
> 7. [Python 模块](/edureka/python-modules-abb0145a5963)
> 
> 8. [Python 开发者技能](/edureka/python-developer-skills-371583a69be1)
> 
> 9.[哎呀面试问答](/edureka/oops-interview-questions-621fc922cdf4)
> 
> 10.[Python 开发者简历](/edureka/python-developer-resume-ded7799b4389)
> 
> 11.[Python 中的探索性数据分析](/edureka/exploratory-data-analysis-in-python-3ee69362a46e)
> 
> 12.[带 Python 的乌龟模块的贪吃蛇游戏](/edureka/python-turtle-module-361816449390)
> 
> 13. [Python 开发者工资](/edureka/python-developer-salary-ba2eff6a502e)
> 
> 14.[主成分分析](/edureka/principal-component-analysis-69d7a4babc96)
> 
> 15. [Python vs C++](/edureka/python-vs-cpp-c3ffbea01eec)
> 
> 16.[刺儿头教程](/edureka/scrapy-tutorial-5584517658fb)
> 
> 17.[蟒蛇 SciPy](/edureka/scipy-tutorial-38723361ba4b)
> 
> 18.[最小二乘回归法](/edureka/least-square-regression-40b59cca8ea7)
> 
> 19. [Jupyter 笔记本小抄](/edureka/jupyter-notebook-cheat-sheet-88f60d1aca7)
> 
> 20. [Python 基础知识](/edureka/python-basics-f371d7fc0054)
> 
> 21. [Python 模式程序](/edureka/python-pattern-programs-75e1e764a42f)
> 
> 22.[Python 中的生成器](/edureka/generators-in-python-258f21e3d3ff)
> 
> 23. [Python 装饰器](/edureka/python-decorator-tutorial-bf7b21278564)
> 
> 24. [Python Spyder IDE](/edureka/spyder-ide-2a91caac4e46)
> 
> 25.[在 Python 中使用 Kivy 的移动应用](/edureka/kivy-tutorial-9a0f02fe53f5)
> 
> 26.[十大最佳学习书籍&练习 Python](/edureka/best-books-for-python-11137561beb7)
> 
> 27.[用 Python 实现机器人框架](/edureka/robot-framework-tutorial-f8a75ab23cfd)
> 
> 28.[Python 中的贪吃蛇游戏使用 PyGame](/edureka/snake-game-with-pygame-497f1683eeaa)
> 
> 29. [Django 面试问答](/edureka/django-interview-questions-a4df7bfeb7e8)
> 
> 30.[十大 Python 应用](/edureka/python-applications-18b780d64f3b)
> 
> 31.[Python 中的哈希表和哈希表](/edureka/hash-tables-and-hashmaps-in-python-3bd7fc1b00b4)
> 
> 32. [Python 3.8](/edureka/whats-new-python-3-8-7d52cda747b)
> 
> 33. [Python Visual Studio](/edureka/python-visual-studio-cef3ad98a9e2)
> 
> 34. [Python 教程](/edureka/python-tutorial-be1b3d015745)

*原载于 2019 年 11 月 27 日 https://www.edureka.co*[](https://www.edureka.co/blog/support-vector-machine-in-python/)**。**