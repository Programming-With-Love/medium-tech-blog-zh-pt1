# 如何用 Python 创建简单的图像识别？

> 原文：<https://medium.com/duomly-blockchain-online-courses/how-to-create-simple-image-recognition-with-python-684398edab2e?source=collection_archive---------0----------------------->

![](img/7e173a7e2ceadacb27a29c935e6369c8.png)

[www.duomly.com — programming online courses](https://www.duomly.com)

本文最初发布于:[https://www . blog . duomly . com/how-to-create-image-recognition-with-python/](https://www.blog.duomly.com/how-to-create-image-recognition-with-python/)

图像识别是最普遍的机器学习类问题之一。它旨在训练机器像人一样识别图像。

更准确地说，图像识别属于监督学习问题组，即分类问题。

本文介绍了一种相对简单的训练神经网络识别数字的方法。这种方法使用普通的前馈神经网络。使用其他技术可以进一步提高模型的准确性。

## 创建基本模型

创建基本模型时，您至少应该做以下五件事:

1.  **导入模块、类和函数**。在本文中，我们将使用 **Keras** 库来处理神经网络，使用 **scikit-learn** 来获取和准备数据。
2.  **加载数据**。这篇文章展示了如何识别手写的数字。来自 sklearn.datasets 的函数 load_digits()提供了 1797 个观察值。每个观察值有 64 个特征，代表 1797 张 8 像素高、8 像素宽的图片的像素。每个特征可以在 0-16 的范围内，取决于它的灰度。输出代表正确的数字，可以是 0-9 范围内的整数值。
3.  **转换和分割数据**。我们首先需要对输出进行二进制化，也就是说，将每个输出作为值为 0 和 1 的向量。然后，我们必须将整个数据集分成训练集和测试集。最后，我们将输入标准化。
4.  **创建分类模型并训练(fit)** 。最简单的模型有一个未显式添加的输入层、一个隐藏层和一个输出层。我们使用训练集来训练我们的神经网络。
5.  **测试分类模型**。最后，我们使用测试集测试网络的性能。

代码如下所示:

```
# 1\. Import modules, classes and functions
import keras
from keras.layers import Dense
from keras.models import Sequential
from sklearn.datasets import load_digits
from sklearn.model_selection import train_test_split
from sklearn.preprocessing import LabelBinarizer, StandardScaler# 2\. Load data
x, y = load_digits(n_class=10, return_X_y=True)

# 3\. Transform and split data
# Create the binary output
tr = LabelBinarizer(neg_label=0, pos_label=1, sparse_output=False)
y = tr.fit_transform(y)
# Split train and test data
x_train, x_test, y_train, y_test =\
    train_test_split(x, y, test_size=0.3, random_state=0)
# Standardize the input
sc = StandardScaler()
x_train, x_test = sc.fit_transform(x_train), sc.transform(x_test)

# 4\. Create the classification model and train (fit) it
cl = Sequential()
# Add the hidden layer
cl.add(Dense(units=500, activation='relu', use_bias=True,
             kernel_initializer='uniform', bias_initializer='zeros',
             input_shape=(x_train.shape[1],)))
# Add the output layer
cl.add(Dense(units=10, activation='softmax', use_bias=True,
             kernel_initializer='uniform', bias_initializer='zeros'))
# Compile the classification model
cl.compile(loss='categorical_crossentropy', optimizer='adam',
           metrics=['accuracy'])
# Fit (train) the classification model
cl.fit(x_train, y_train, epochs=100, batch_size=10)

# 5\. Test the classification model
result = cl.evaluate(x_test, y_test, batch_size=128)
for i in range(2):
    print(f'{cl.metrics_names[i]}: {result[i]}')
```

可以看到，模型的准确率大约是 97.8 %。结果可能有所不同！

您可以使用超参数，并更改隐藏层中的单元数、优化器、训练的代数数、批次大小等，尝试进一步提高网络的准确性。

## 让网络更深

深度神经网络有不止一个隐藏层。添加隐藏层可能会提高准确性。代码与前一种情况几乎相同，只是增加了一条语句来添加另一个隐藏层:

```
# 1\. Import modules, classes and functions
import keras
from keras.layers import Dense
from keras.models import Sequential
from sklearn.datasets import load_digits
from sklearn.model_selection import train_test_split
from sklearn.preprocessing import LabelBinarizer, StandardScaler

# 2\. Load data
x, y = load_digits(n_class=10, return_X_y=True)

# 3\. Transform and split data
# Create the binary output
tr = LabelBinarizer(neg_label=0, pos_label=1, sparse_output=False)
y = tr.fit_transform(y)
# Split train and test data
x_train, x_test, y_train, y_test =\
    train_test_split(x, y, test_size=0.3, random_state=0)
# Standardize the input
sc = StandardScaler()
x_train, x_test = sc.fit_transform(x_train), sc.transform(x_test)

# 4\. Create the classification model and train (fit) it
cl = Sequential()
# Add the first hidden layer
cl.add(Dense(units=500, activation='relu', use_bias=True,
             kernel_initializer='uniform', bias_initializer='zeros',
             input_shape=(x_train.shape[1],)))
# Add the second hidden layer
cl.add(Dense(units=500, activation='relu', use_bias=True,
             kernel_initializer='uniform', bias_initializer='zeros',
             input_shape=(x_train.shape[1],)))
# Add the output layer
cl.add(Dense(units=10, activation='softmax', use_bias=True,
             kernel_initializer='uniform', bias_initializer='zeros'))
# Compile the classification model
cl.compile(loss='categorical_crossentropy', optimizer='adam',
           metrics=['accuracy'])
# Fit (train) the classification model
cl.fit(x_train, y_train, epochs=100, batch_size=10)

# 5\. Test the classification model
result = cl.evaluate(x_test, y_test, batch_size=128)
for i in range(2):
    print(f'{cl.metrics_names[i]}: {result[i]}')
```

准确率略有提高，达到 98.3 %。

## 卷积神经网络和其他改进

图像识别问题的解决往往比我们在这里获得的精度更高。改进用于图像识别的网络的一种方法是通过添加卷积和汇集层，形成卷积神经网络。

此外，可以使用某种正规化，作为辍学。

有关如何使用 Keras 实现这一点的更多信息，您可以查看 Keras 的官方文档。

## 结论

本文介绍了如何使用 Python 及其机器学习库 Keras 和 scikit-learn 实现图像识别。图像识别是监督学习，即分类任务。

这仅仅是开始，还有许多技术可以提高所给出的分类模型的准确性。

![](img/d5ee4c5640193ff931b57af57d9cd1d4.png)

[www.duomly.com — programming online courses](https://www.duomly.com)

感谢您的阅读。

内容由我们的队友米尔科提供。