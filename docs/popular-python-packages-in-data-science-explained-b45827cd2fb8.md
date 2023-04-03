# 数据科学中流行的 Python 包解释

> 原文：<https://medium.com/capital-one-tech/popular-python-packages-in-data-science-explained-b45827cd2fb8?source=collection_archive---------8----------------------->

## 介绍如何使用 NumPy、Matplotlib、Pandas 和 Scikit-Learn 对数据进行操作和建模

![](img/52812583ea9191084e900eba7b21b916.png)

作为一名试图进入数据科学的软件工程师，我一直在探索各种数据科学课程和项目教程。当我浏览它们的时候，我注意到它们之间的一个相似之处:*每一个都缺乏对正在使用的库——以及它们的功能——的适当解释。*学习全新的信息有时会令人困惑，但了解这些核心库以及不同类型问题的解决方法非常重要。

因此，我想为自己和其他刚刚学习数据科学领域所用技术的初学者创建一个易于使用的指南。本指南比较了数据科学家使用的一些最流行的 Python 包，并分解了它们最有用的函数、每个函数中使用的参数以及它们的用例。

# NumPy

[NumPy](https://numpy.org/) 是 Python 的开源库，支持数组和矩阵的数学运算。它对线性代数特别有用，通常用于随机数生成。它有许多有用的特性，允许你创建和操作数组和计算各种统计数据。让我们深入了解一下。

**导入库**

```
import numpy as np 
```

**创建数组**

*   用指定的数字创建数组

```
a = np.array([3, 6, 9, 12])# creates an array of numbers
```

*   通过指定样本数来创建均匀间隔的数组

```
b = np.linspace(0, 5, 20)# 0 would be the start value and 5 the end value # 20 is number of samples between 0 and 5 that will be produced 
```

*   通过指定跳过值来创建均匀间隔的数组

指定开始和结束值以及跳过值，以创建均匀间隔的数组。例如，这段代码将创建一个包含 0、5、10、15 和 20 的数组。

```
c = np.arange(0, 20, 5)# 0 would be the start value and 20 the end value # 5 is the skip value between the start and end numbers
```

**操纵数组**

通过操作已经存在的数组来创建新数组。 *Copy* 将精确复制一个数组，而不改变原来的数组。*排序*将从最低到最高列出数组中的数字。*转置*将沿对角线翻转矩阵，切换行和列索引。 *Reshape* 改变数组的维数而不改变数据。

*   复制

```
d = np.copy(c)
```

*   分类

```
e = d.sort()
```

*   移项

```
transposed_e = np.transpose(e)
```

*   使再成形

```
np.reshape(a, (2, 4))# first param is array you wish to reshape# second param is dimension of the shape you want; (rows, columns)
```

**执行数学运算**

计算具体的统计量，如*均值*、*标准差*和*相关系数*，这些可以为您提供有关您正在处理的数组的大量信息。*标准差*有利于寻找方差，*相关系数*有利于寻找数组内的关系，*均值*有助于寻找平均值。

*   平均

```
# can use either format to find the meannp.mean(a)a.mean()
```

*   标准偏差

```
np.std(a)a.std()
```

*   相关系数

```
np.corrcoef(a)a.corrcoef()
```

这里的是最常用的 NumPy 函数的超级有用的备忘单。

# Matplotlib

[Matplotlib](https://matplotlib.org/) 被认为是用 Python 制作所有类型的绘图、图表和图形的首选开源库。它的主要用途是以一种易于理解的方式显示数据，因为数据可视化通常用于交流您可能看不到的模式。Matplotlib 是一个很好的库，当您对正在处理的数据有所感觉时，或者当您向业务中的非技术人员展示您的发现时，可以使用它。

**导入库**

```
import matplotlib.pyplot as plt
```

**为绘图设置显示**

*   图形-确定绘图所在窗口的尺寸。

```
fig = plt.figure(figsize=(8, 12))# creates a figure object that is 8 rows by 12 columns# use when you want your plot to be a customized size
```

*   轴(axes )-向当前图形添加轴。

```
plt.axes()# adds axes to the current figure
```

**创建地块**

通过在图形上绘制点或线并指定轴，可以创建不同种类的图。创建一个情节返回一个 2D 图片。

*   xlabel-x 轴的标签。

```
plt.xlabel('This is the x-axis')# gives the x-axis a title
```

*   y Label-y 轴的标签。

```
plt.ylabel('This is the y-axis')# gives the y-axis a title
```

*   x ticks-x 轴的刻度线。

```
plt.xticks(np.arange(0, 50, 5))# creates the tick marks on the x-axis# use arange from Numpy library to ensure ticks are evenly spaced
```

*   y ticks-y 轴的刻度线。

```
plt.yticks(np.arange(0, 100, 10))# we can do the same thing with the ticks on the y-axis
```

*   标题-绘图的标题。

```
plt.title('This is my graph')# set the title of your plot
```

*   显示(show )-在创建的图形上显示绘图。

```
plt.show()# display your plot
```

**创建支线剧情**

创建并显示多个并排的地块。如果您需要比较不同的数据集或分类变量的不同类别，这是很好的。

```
fig, axes = plt.subplots(2, 2, sharex=True, sharey=True)# create 4 subplots # first parameter represents number of columns# second parameter represents number of rows # sharex and sharey refer to the design of the subplots
```

*   绘图(plot )-在其中一个绘图上显示数据。

```
axes[0, 0].plot(x)# plots x in the top left subplot# this is the plot at position (0, 0) 
```

*   标题(title )-设置其中一个图的标题。

```
axes[0, 0].set_title('This is the first subplot')# set the title of a subplot # this sets the title of the plot at (0, 0)
```

[https://python 4 天文学家. github . io/plotting/matplotlib . html](https://python4astronomers.github.io/plotting/matplotlib.html)

# 熊猫

[pandas](https://pandas.pydata.org/) 是一个从 NumPy 构建的开源 Python 库，它提供了数据结构和分析这些结构的工具。它非常适合读入数据并在进行分析之前清理数据。

**导入库**

```
import pandas as pd 
```

**读入 CSV**

为了操作和分析数据集，您必须将其导入到您正在工作的空间中。有许多不同类型的文件可以使用，但CSV 是最常见的。

```
file = pd.read_csv('example.csv')
```

**清理数据**

您希望清理正在使用的数据框，以便它仅显示您希望在分析中使用的信息。您还希望确保每一列都有正确的数据类型，并且没有空值。

*   删除-从数据框中删除轴或列。

```
new_file = file.drop('Name', axis=1)# axis=1 indicates you are dropping a columnfile.drop('Name', axis=1, inplace=True)# allows you to drop column without saving file to a new variablenew_file = file.drop(['row1', 'row2'])# default of axis=0; drops rows rather than columns# must assign to a new variable when not using 'inplace=True'
```

*   数据类型-在数据框的列中查找数据的数据类型。

```
df.dtypes# gives data types of all the columns in your data frame
```

*   作为类型—更改特定列中所有数据的数据类型。

```
df.Name = df.Name.astype('str')# changes data in 'Name' column to be strings df.Household = df.Name.astype('int')# changes data in 'Household' column to be integers
```

**选择数据帧的子集**

当您只想使用数据框的几列时，可以选择数据的子集。如果您想一次查看一列或更改存储在其中的数据类型，这也很好。

*   指定要选择的列的名称。

```
df[['Name', 'Address', 'Location']]# selecting the Name, Address and Location columns in data frame
```

*   按列号或行号选择数据。

```
df.iloc[:, 0]# first parameter is for row selection# second is for column selection # this expression selects all rows and first column df.iloc[:, 0:2]# select all rows and first two columns
```

*   通过标签或布尔数组选择数据

```
df.loc['Madison']# returns the row of a data frame as a Series# first param is the row namedf.loc['Madison', 'age']# returns a single label for row and column# second parameter is the column name 
```

*   Head 显示数据集的前 5 行。

```
df.head()
```

*   总和-查找数据框中值的总和。

```
df.sum()
```

*   描述-给出数据框的汇总统计数据。

```
df.describe()
```

[这里的](http://datacamp-community-prod.s3.amazonaws.com/dbed353d-2757-4617-8206-8767ab379ab3)是熊猫最常用功能的超级有用的备忘单。

# sci kit-学习

[Scikit-learn](https://scikit-learn.org/stable/) 是一个开源 Python 库，包含机器学习、预处理、分类和可视化算法。每当你寻找一个特定类型的模型，并希望在一组数据上使用它时，你就有机会使用这个库。

**将你的数据分成训练集和测试集**

您需要将您的数据集分割成*训练*和*测试*数据，以便符合您的模型，然后测量其准确性。x 和 Y 变量被分成*序列*和*测试*。数据科学家通常将他们的数据分成 80/20 份(80%用于训练，20%用于测试)。

*   导入训练 _ 测试 _ 分割包

```
from sklearn.model_selection import train_test_split
```

*   将数据分成训练集和测试集

```
X_train, X_test, y_train, y_test = train_test_split(X,
                                                    y,
                                                    test_size=0.2,
                                                    random_state=4)# test data will be 20% of the data set and train data will be 80%# random state represents the seed used to ensure your splits can be reproduced 
```

**标准化和规范化您的数据**

当您比较具有不同单位或比例的要素时，您会想要标准化您的数据(查看[标准化或规范化？Python 中的示例](/@rrfd/standardize-or-normalize-examples-in-python-e3f174b65dfc)了解更多信息)。当你将它们标准化时，你的值会以 0 为中心。

*   `**StandardScaler()**` 计算训练数据的平均值和标准偏差，用于缩放。然后使用`**Scaler.transform()**` 根据标准化器对数据进行缩放。在模型中使用训练和测试数据之前，需要对其进行标准化。

```
from sklearn.preprocessing import StandardScalerscaler = StandardScaler().fit(X_train)# computes mean and std of the X_train data to be used for scalingstandardized_X = scaler.transform(X_train)# standardizes the X_train data based on the scalerstandardized_X_test = scaler.transform(X_test)# standardizes the X_test data based on the scaler 
```

*   为了获得系数的精确测量值，您可能希望对数据进行归一化，从而降低数据对要素比例的敏感度(查看[标准化还是归一化？Python 中的例子](/@rrfd/standardize-or-normalize-examples-in-python-e3f174b65dfc)了解更多信息)。规格化后，值变成 0 到 1 之间的数字。`Normalizer()`查找数据的单位规范，`scaler.transform()`与`Standardizer()`相似，缩放数据以适应规范器。

```
from sklearn.preprocessing import Normalizerscaler = Normalizer().fit(X_train)# finds the unit norms of the X_train data normalized_X = scaler.transform(X_train)# normalizes the X_train data based on the scalernormalized_X_test = scaler.transform(X_test)
```

**使用机器学习算法**

*   线性回归-当变量之间存在线性关系时使用。

```
import sklearn.linear_model import LinearRegressionlr = LinearRegression(normalize=True)# normalize=True eliminates the need to normalize before building the model 
```

*   支持向量机(SVM) —用于监督数据的分类和回归。

```
from sklearn.svm import SVCsvc = SVC(kernel='linear')# kernel specifies the kernel type to be used# kernel options include linear, poly, sigmoid, rbf, or precomputed 
```

*   朴素贝叶斯-用于在假设独立性的情况下对数据进行分类。点击了解更多关于朴素贝叶斯算法[的信息。](/capital-one-tech/naives-bayes-classifiers-for-machine-learning-2e548bfbd4a1)

```
from sklearn.naive_bayes import GaussianNBgnb = GaussianNB()
```

*   KNN-用于通过查找“最近邻”对监督数据进行分类和回归。点击了解更多关于 KNN 算法[的信息。](/capital-one-tech/k-nearest-neighbors-knn-algorithm-for-machine-learning-e883219c8f26)

```
from sklearn import neighborsknn = neighbors.KNeighborsClassifier(n_neighbors=5)# adjust n_neighbors to find the model with the highest accuracy
```

*   KMeans 最常用于非监督数据的聚类分析。点击了解更多关于 KMeans 算法[的信息。](/capital-one-tech/naives-bayes-classifiers-for-machine-learning-2e548bfbd4a1)

```
from sklearn.cluster import KMeansk_means = KMeans(n_clusters=3, random_state=3)# adjust number of clusters to increase the accuracy of your model# random state represents the seed used to ensure your splits can be reproduced
```

**将数据拟合到模型中**

创建模型后，必须使数据符合模型。当您使用的模型受到监督时，您必须输入 X 和 Y 训练数据。如果是无监督模型，只需输入 x 训练数据。

```
knn.fit(X_train, y_train)# for supervised learningk_means.fit(X_train)# for unsupervised learning 
```

**根据您的模型预测结果**

拟合模型后，您可以使用它们来预测某个标注的 Y 值或概率。

```
y_pred = knn.predict(X_test)# use the model to predict the y valuesy_pred = knn.predict_proba(X_test)# use the model to predict the probability of a label
```

**计算分类指标**

不同类型的分类指标，如*准确度分数*、*混淆矩阵*和*分类报告*可以用来告诉你你的模型有多准确。

*   预测实际 Y 测试值与模型预测值之间的准确性

```
from sklearn.metrics import accuracy_scoreaccuracy_score(y_test, y_pred)# predict the accuracy between the actual y test values and what the model predicted they would be 
```

*   描述模型的性能

```
from sklearn.metrics import confusion_matrixconfusion_matrix(y_test, y_pred)# describe the model's performance by comparing actual and predicted values
```

*   分类报告(精确度、召回率、f1 分数)

```
from sklearn.metrics import classification_reportclassification_report(y_test, y_pred)# gives you the precision, recall, f1-score, and report of the model
```

# 结论

我知道通读一门课程或教程而不理解作者所使用的 Python 包的上下文细节是多么困难。这可能会令人沮丧，您会发现自己花在研究和比较 Python 包上的时间比学习数据科学技术本身的时间还多。我希望本指南能为您提供阅读教程和观看课程所需的资源和信心，以便更好地理解如何解决数据科学问题。

披露声明:2019 首创一。观点是作者个人的观点。除非本帖中另有说明，否则 Capital One 不隶属于所提及的任何公司，也不被这些公司认可。使用或展示的所有商标和其他知识产权是其各自所有者的财产。