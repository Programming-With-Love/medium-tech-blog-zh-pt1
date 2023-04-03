# Python 中的 NumPy 是什么— NumPy 简介—NumPy 教程

> 原文：<https://medium.com/edureka/python-introduction-to-numpy-numpy-tutorial-4ac06c717971?source=collection_archive---------0----------------------->

![](img/244d1f2af7143d7db4a14c16d14bf9bf.png)

NumPy 正变得越来越流行，并在各种商业系统中被使用。因此，理解这个库将要提供什么是很重要的。NumPy 是最强大的 Python 库之一，因为它的语法紧凑、功能强大，同时又富有表现力。它使用户能够管理向量、矩阵和更高维数组中的数据，并且它还被用于数组计算行业。本文将概述什么是 Python 中的 NumPy 以及 NumPy 库的核心特性。

*   *NumPy 是什么？*
*   *NumPy 的历史*
*   *Python 中为什么用 NumPy？*
*   *NumPy 的特点*
*   *NumPy 的安装*
*   *NumPy —数据类型*
*   *NumPy 的例子*
*   *使用 NumPy 的操作*
*   *NumPy 中的数组*
*   *NumPy —数学函数*
*   NumPy 的应用

**Python 中的 NumPy 是什么？**

NumPy(数字 Python)是一个由多维数组对象和一组操作它们的函数组成的库。它是科学计算中最常用的 Python 包之一，因为它允许您对数组执行数学和逻辑运算。NumPy 是一种 Python 脚本语言。

# NumPy 的历史

Travis Oliphant 在 2005 年通过大量修改 Numeric 并结合竞争对手 Numarray 的功能创建了 NumPy。NumPy 的前身 Numeric 是由 Jim Hugunin 在许多其他开发者的帮助下于 1995 年创建的。NumPy 开发人员 Travis Oliphant 成功地将社区聚集在一个单一的数组包后面，因此他将 Numarray 的功能转移到 Numeric，并在 2006 年发布了 NumPy 1.0。现在我们知道了 Python 中的 NumPy 是什么，以及它的历史。现在让我们开始了解我们为什么使用它。

# Python 中为什么要用 NumPy？

我们在 Python 中有充当数组的列表，但是它们处理起来很慢。NumPy 的目标是提供一个比传统 Python 列表快 50 倍的数组对象。它可用于进行各种基于数组的数学运算。它用先进的分析结构扩展了 Python，确保用数组和矩阵进行快速计算，以及用这些数组和矩阵工作的大型高级数学函数库。与列表不同，NumPy 数组保存在内存中一个连续的位置，允许程序快速访问和操作它们。

# NumPy 的特点

![](img/79d7b1a9bd4f9799492338fb096fc648.png)

*   **高性能:**

NumPy 通过提供多维数组和函数以及在数组上高效操作的操作符，部分解决了速度慢的问题。

*   **集成来自 C/C++、Fortran 的代码:**

我们可以使用 NumPy 中的函数来处理用其他语言编写的代码。因此，我们可以集成各种编程语言中可用的功能。

*   **多维容器:**

ndarray 是包含相同类型和大小的项目的(通常是固定大小的)多维容器。数组中的维数和项数由其形状定义，形状是一个由 N 个非负整数组成的元组，指定每个维的大小。数组中项的类型由单独的数据类型对象(dtype)指定，其中一个对象与每个 ndarray 相关联。

*   **广播功能:**

术语广播描述了 NumPy 在算术运算中如何处理不同形状的数组。当我们处理不均匀形状的数组时，这是一个非常有用的概念。它根据较大数组广播较小数组的形状。

*   **附加线性代数:**

它能够执行复杂的元素运算，如线性代数、傅立叶变换等。

*   **使用不同的数据库:**

我们可以处理不同数据类型的数组。我们可以使用 dtype 函数来确定数据类型，从而清楚地了解可用的数据集。

# 在 Python 中安装 NumPy

NumPy 很容易[安装](https://www.edureka.co/blog/install-numpy/)，只需在终端窗口输入几个命令，它可以在 Linux、MacOS 和 Windows 上运行。只需遵循下面概述的说明。

**第一步:检查 Python 版本**

在安装 NumPy 之前，您必须首先确定您拥有哪个 Python 版本。大多数操作系统都预装了 Python，只有 Windows 例外，它需要手动安装。

运行以下命令，看看您是否有 Python 2。

`python -V`

对于 Python 3:

`python3 -V`

版本号应该出现在终端的输出中。

![](img/7c7b0a47b10a514614a6f033ea651e32.png)

**第二步:安装 Pip**

Pip 是一个 Python 包管理器，允许您安装和管理 Python 软件包。在大多数操作系统上，Pip 没有预装。因此，您需要为您正在使用的 Python 版本安装包管理器。如果您有两个版本的 Python，请安装两个 Pip 版本。在 Windows 中，我们的设置如下:

下载 PIP get-pip.py

1.启动命令提示符。为此，请在 Windows 搜索框中键入 cmd，然后单击图标。

2.然后，使用以下命令获取 get-pip.py 文件:

`curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py`

在 Windows 上安装 PIP:

`python get-pip.py`

验证安装:

```
pip help
```

**第三步:安装 NumPy**

一旦安装了 pip，运行下面的命令来安装 NumPy。

在 Python2 中:

`pip install numpy`

在 python 3:`pip3 install numpy`

成功安装的 numpy 版本将在终端上显示为输出。

**步骤 4:验证 NumPy 安装**

运行以下命令检查 NumPy 是否已经安装，并且现在是 Python 包的一部分。

对于 Python2:

`pip show numpy`

对于 Python3:

`pip3 show numpy`

输出应该验证您已经安装了 NumPy，以及包的版本和位置。

**第五步:导入 NumPy 包**

安装 NumPy 后，您现在可以导入软件包并给它一个别名。

首先，输入以下命令之一进入 python 提示符:

`python`

或者

`python3`

在 python 或 python3 提示符下，您可以导入新的包并给它一个别名。

`import numpy as np`

# NumPy —数据类型

下图描述了 NumPy 中使用的不同的[数据类型](https://www.edureka.co/blog/python-numpy-tutorial/)。这些数据类型被写成

numpy.datatype

![](img/c4a59a76b3a367e176915f462aa27980.png)

# 数字的例子

**例 1:**

`**import**` `numpy as np`

`arr **=**` `np.array([1, 2, 3, 4, 6])`

`print(arr)`

**输出:**

`**[1 2 3 4 6]**`

**例 2:**

`**import**` `numpy as np`

`a **=**` `np.array([[1, 2, 3, 4], [5, 6, 7, 8], [9, 10, 11, 12]])`

`print(a[2])`

**输出:**

`**[ 9 10 11 12]**`

**例三:**

`**import**` `numpy as np`

`a **=**` `np.array([1, 2, 3, 4, 5, 6])`

`print(a[5])`

**输出:**
`**6**`

**例 4:**

`**import**` `numpy as np`

`dt **=**` `np.dtype(np.int32)`

`print`

**输出:**

`**int32**`

`**[1 2 3 4 6]**`

# Python 中使用 NumPy 的操作

# NumPy 中的数组

NumPy 的基本对象是齐次多维数组。它是 NumPy 库的主要数据结构。数组是一个值的矩阵，它提供关于原始数据的信息，以及如何定位和解释元素。它由可以用多种方式索引的元素集合组成。正整数元组用于索引它。轴是以数字为单位的尺寸。轴的数量被称为秩。NumPy 中的[数组](https://www.edureka.co/blog/python-numpy-tutorial/)类被称为 ndarray。

**创建数组:**

在 NumPy 中创建数组有多种方法。让我们用一个例子来理解:

**举例:**

`**import**` `numpy as np`

`a **=**` `np.array([[1, 2, 4], [5, 8, 7]], dtype **=**` `'float')`

`print`

`b **=**` `np.array((1` `, 3, 2))`

`print` `("nArray created using passed tuple:n", b)`

`c **=**`

`print` `("nAn array initialized with all zeros:n", c)`

`d **=**` `np.full((3, 3), 6, dtype **=**` `'complex')`

`print` `("nAn array initialized with all 6s.""Array type is complex:n", d)`

`e **=**` `np.random.random((2, 2))`

`print` `("nA random array:n", e)`

`f **=**` `np.arange(0, 30, 5)`

`print` `("nA sequential array with steps of 5:n", f)`

`g **=**`

`print` `("nA sequential array with 10 values between""0 and 5:n", g)`

`arr **=**` `np.array([[1, 2, 3, 4],[5, 2, 4, 2],[1, 2, 0, 1]])`

`newarr **=**`

`print`

`print`

`arr **=**`

`flarr **=**` `arr.flatten()`

`print`

`print` `("Fattened array:n", flarr)`

**输出:**

![](img/f28b009d91eec8f18fac6ce15a79cf8b.png)![](img/d74e33459a581ba2a428d9631a6d29ed.png)

**数组索引:**

数组索引与访问数组元素是一样的。您可以通过引用数组元素的索引号来访问它。例如

`**import**` `numpy`

`arr **=**`

`print(arr[0])`

**输出:**

`**1**`

**数组切片:**

python 中的切片意味着将元素从一个给定的索引带到另一个给定的索引。例如:

`**import**`

`arr **=**` `np.array([1, 2, 3, 4, 5, 6, 7])`

`print(arr[1:5])`

**输出:**

`**[2 3 4 5]**`

**数组中使用的基本函数:**

![](img/835f5899a39d3bf778784048bc3cbee1.png)

# NumPy —数学函数

**三角函数**

NumPy 中的标准三角函数返回给定弧度角的三角比。arcsin、arcos 和 arctan 函数返回给定角度的 sin、cos 和 tan 的三角倒数。这些方法的结果可以使用 numpy.degrees()函数进行验证，该函数将弧度转换为角度。

**例如:**

`**import**` `numpy as np`

`a **=**`

`print` `("Sine of different angles:")`

`print` `(np.sin(a*****np.pi**/**180))`

`print`

`print`

`print`

`print`

`print` `("Tangent values for given angles:")`

`print`

**输出:**

![](img/fd5a54cd8c3ce8a99a3ce16e694d95b3.png)

**舍入函数** 该函数返回舍入到指定精度的值。这里用的函数是
numpy.around(a，小数)
例子:

`**import**` `numpy as np`

`in_array **=**` `[.5, 1.5, 2.5, 3.5, 4.5, 10.1]`

`print`

`round_off_values **=**` `np.round_(in_array)`

`print`

`in_array **=**` `[.53, 1.54, .71]`

`print` `("nInput array : n", in_array)`

`round_off_values **=**` `np.round_(in_array)`

`print` `("nRounded values : n", round_off_values)`

`in_array **=**`

`print` `("nInput array : n", in_array)`

`round_off_values **=**` `np.round_(in_array, decimals **=**` `3)`

`print` `("nRounded values : n", round_off_values)`

**输出:**

![](img/d2064df46a90d6df94f6b3f17d9a907c.png)

**NumPy —统计函数**

NumPy 提供了许多有用的统计函数，用于确定数组中元素的最小值、最大值、百分位标准偏差和方差等。这些功能如下:

numpy.amin()和 numpy.amax()numpy.amin()和 numpy.amax()

**示例:**

`**import**`

`a **=**` `np.array([[3,7,5],[8,4,3],[2,4,9]])`

`print` `("Our array is:")`

`print` `(a)`

`print` `("n")`

`print` `("Applying amin() function:")`

`print`

`print` `("n")`

`print` `("Applying amin() function again:")`

`print` `(np.amin(a,0))`

`print` `("n")`

`print` `("Applying amax() function:")`

`print` `(np.amax(a))`

`print` `("n")`

`print` `("Applying amax() function again:")`

`print` `(np.amax(a, axis **=**` `0))`

**输出:**

![](img/1db129e3ec28fcf0b12b926d459e5c64.png)

# NumPy 在 Python 中的应用

*   NumPy 优化并简化了科学计算中常用的众多数学过程，例如:
*   向量乘法
*   矩阵-矩阵和矩阵-向量的乘法
*   向量和矩阵元素运算(即加、减、乘和除以一个数)
*   元素或数组之间的比较
*   对向量/矩阵元素逐个应用函数(如 pow、log 和 exp)
*   NumPy 包含大量的线性代数运算。
*   利纳尔格
*   统计、简化以及更多
*   它保持最小的内存
*   它被用于机器学习

这是对 NumPy 库的一个简要概述，在这里我们了解了 NumPy，它的历史，它的重要性，它的特性，以及如何使用它。我希望这已经以清晰易懂的方式为您澄清了一些事情。亲自尝试一下，提高你的数字知识。如果你想成为一名数据科学家，向全球顶尖的数据科学家学习数据科学技能，那就去做 checkout Edureka 的使用 Python 的数据科学认证培训吧，给你的职业生涯插上翅膀！

如果你想查看更多关于 Python、DevOps、Ethical Hacking 等市场最热门技术的文章，你可以参考 Edureka 的官方网站。

请留意本系列中的其他文章，它们将解释数据科学的各个方面。

> *1。* [*数据科学教程*](/edureka/data-science-tutorial-484da1ff952b)
> 
> *2。*[*R 中的机器学习*](/edureka/machine-learning-with-r-c7d3edf1f7b)
> 
> *3。* [*机器学习算法*](/edureka/machine-learning-algorithms-29eea8b69a54)
> 
> *4。*[*R 中的线性回归*](/edureka/linear-regression-in-r-da3e42f16dd3)
> 
> *5。*[*R 中的逻辑回归*](/edureka/logistic-regression-in-r-2d08ac51cd4f)
> 
> *6。* [*分类算法*](/edureka/classification-algorithms-ba27044f28f1)
> 
> *7。* [*决策树中的 R*](/edureka/a-complete-guide-on-decision-tree-algorithm-3245e269ece)
> 
> *8。* [*随机森林中的 R*](/edureka/random-forest-classifier-92123fd2b5f9)
> 
> *9。* [*机器学习入门*](/edureka/introduction-to-machine-learning-97973c43e776)
> 
> 10。[*R 中的朴素贝叶斯*](/edureka/naive-bayes-in-r-37ca73f3e85c)
> 
> 11。 [*统计与概率*](/edureka/statistics-and-probability-cf736d703703)
> 
> *12。* [*如何创建一个完美的决策树？*](/edureka/decision-trees-b00348e0ac89)
> 
> *13。* [*关于数据科学家角色的十大神话*](/edureka/data-scientists-myths-14acade1f6f7)
> 
> 14。 [*顶级数据科学项目*](/edureka/data-science-projects-b32f1328eed8)
> 
> 15。 [*数据分析师 vs 数据工程师 vs 数据科学家*](/edureka/data-analyst-vs-data-engineer-vs-data-scientist-27aacdcaffa5)
> 
> 16 岁。 [*人工智能的种类*](/edureka/types-of-artificial-intelligence-4c40a35f784)
> 
> *17。*[*R vs Python*](/edureka/r-vs-python-48eb86b7b40f)
> 
> *18。* [*人工智能 vs 机器学习 vs 深度学习*](/edureka/ai-vs-machine-learning-vs-deep-learning-1725e8b30b2e)

*原载于 2019 年 2 月 26 日*[*www.edureka.co*](https://www.edureka.co/blog/math-and-statistics-for-data-science/)*。*