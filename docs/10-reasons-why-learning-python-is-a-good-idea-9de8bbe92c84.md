# 学习 Python 是个好主意的 10 个理由

> 原文：<https://medium.com/duomly-blockchain-online-courses/10-reasons-why-learning-python-is-a-good-idea-9de8bbe92c84?source=collection_archive---------7----------------------->

![](img/679e17a9e075cde4ee5ed73df39914d2.png)

[Duomly — programming online courses](https://www.duomly.com)

本文最初发表于:[https://www.blog.duomly.com/why-you-should-learn-python/](https://www.blog.duomly.com/why-you-should-learn-python/)

Python 是当今最流行和使用最广泛的编程语言之一。它适用于初学者和专业人士、初创公司和大公司、学术界和商业实体。作为一种通用和多平台的编程语言，它有多种应用。

Python 及其生态系统中的大多数东西都是免费和开源的。有一个大型的、专注的、友好的开发者和教育工作者社区，他们支持 Python 和相关库的开发，并帮助人们学习和掌握 Python。

Python 有一个漂亮优雅的语法，允许您编写简洁易读的代码。开发过程通常非常快，因为语言特性、许多领域中大量有用的库、优秀的社区支持、对大多数主要集成开发环境的优秀支持等等。

本文解释了 Python 如此受欢迎和吸引人的一些因素。

# 1.Python 是免费和开源的

Python 解释器本身和大多数最流行的第三方库都是免费和开源的。Python 是在非常宽松的 Python 软件基金会(PSF)许可下获得许可的。其他软件通常根据相当宽松的许可证进行许可，如 PSF (BitArray)、BSD 3-Clause (NumPy、SciPy、Pandas、Django、Flask、IPython、Jupyter、Bokeh、PyTorch、Scikit-Learn)、MIT (BeautifulSoup4、Flake8、PEP8、IP、Spyder)、Apache 2.0 (TensorFlow、ElasticSearch、NLTK、Tornado)、LGPL 3.0 (Qt)等等。

# 2.Python 很受欢迎，很受喜爱，也很受欢迎

Python 是世界上最流行的语言之一。这也是开发人员喜欢使用的语言。2019 年 8 月 Tiobe 指数排行榜上，Python 第三，排在 Java 和 C 之后，C++和 C#之前。根据 Stack Overflow 2019 年的年度开发者调查，Python 仅次于 JavaScript(还有 HTML/CSS 和 SQL)。根据同一调查，它是最受欢迎的编程语言，仅次于 Rust。

Python 第三方库也很受欢迎。根据上面提到的调查，Pandas 在所有的框架、库和工具中排名第四，第十二。Django 和 Flask 是前八个最受欢迎和最受欢迎的 Web 框架。

Python 对于初学者、专业程序员以及来自其他领域的专家(如数学家和非计算机科学工程师)来说非常方便。它被初创公司、学术界以及大公司和平台广泛使用，如谷歌、Dropbox、脸书、Instagram、Quora、Reddit、Spotify、网飞、优步、Pinterest、Mozilla、英特尔等。

# 3.Python 有一个友好且专注的社区

许多人致力于 Python 和第三方库的开发。很多程序员写的博客都和 Python 有关。

如果你在用 Python 编程时遇到了问题，很可能已经有人在这个大型社区中提出并解决了这个问题。只要用你最喜欢的搜索引擎搜索，你就有可能在 StackOverflow 或者类似的平台上，或者一些博客上找到答案。你也可以将你的问题提交给 StackOverflow，你很可能会很快得到回复。

Python 一直在变化，变得越来越复杂、越来越快，并且能够应对今天和明天的挑战。Python 增强建议(PEP)是宣布改进建议的地方。这对每个长期接触 Python 的人来说都至关重要。

最后，还有一组建议或核心价值观，由 Tim Peters 撰写，称为 Python 的禅，代表了 Python 开发的指导原则。您总是可以使用 import this 命令获得它:

```
>>> import this
The Zen of Python, by Tim PetersBeautiful is better than ugly.
Explicit is better than implicit.
Simple is better than complex.
Complex is better than complicated.
Flat is better than nested.
Sparse is better than dense.
Readability counts.
Special cases aren't special enough to break the rules.
Although practicality beats purity.
Errors should never pass silently.
Unless explicitly silenced.
In the face of ambiguity, refuse the temptation to guess.
There should be one-- and preferably only one --obvious way to do it.
Although that way may not be obvious at first unless you're Dutch.
Now is better than never.
Although never is often better than *right* now.
If the implementation is hard to explain, it's a bad idea.
If the implementation is easy to explain, it may be a good idea.
Namespaces are one honking great idea -- let's do more of those!
```

在使用 Python 时，这些原则是有益的，但是您可能会发现其中一些原则很有启发性。

# 4.Python 拥有优雅简洁的语法

Python 被设计成可读的。与类 C 语言的语法相比，它的语法更接近自然语言或伪代码。它使用空白:换行符来结束语句(我们可以使用分号，虽然很少使用)和水平空格或制表符来标记代码块。

这是 if 语句的一个示例:

```
if x < 0:
    print('x is negative')
elif x >> 0:
   print('x is positive')
else:
   print('x is zero')
```

while 循环如下所示:

```
i = 0
while i < 10:
    print(i)
    i += 1
```

这是 for 循环:

```
for i in range(10):
    print(i)
```

这是 Python 如何处理异常的一个例子:

```
try:
    f(x, y)
except TypeError as e:
    print(e)
else:
    g(x, y)
finally:
    clean()
```

有许多方便的代码构造，如三元 if-else 运算符:x = y if condition else z。

# 5.Python 是多平台的

Python 解释器可以在许多操作系统上工作。在大多数情况下，用 Python 编程时，除了文件路径，您不会注意到操作系统之间的区别。然而，还是有一些微妙的区别。许多第三方库也能在 Linux、Windows 和 Mac OS 上很好地工作。

# 6.Python 支持多种编程范式

Python 是一种多范式编程语言。它支持多种范例，如过程式、面向对象式和函数式。它有一个内置的软件包 **functools** ，方便了函数式编程。

Python 还使您能够直观地混合编程范式。你需要找到最适合你的路径。

# 7.Python 提供了有用的内置库

无论您想要执行单元测试、启用多处理功能、使用正则表达式、操作日期和时间、生成伪随机数，还是进行一些数学或统计计算，Python 都提供了有用的内置库。还有很多。

Python 内置库经过了良好的测试，非常可靠。它们是开源的，所以你可以去 GitHub 看看实现。

# 8.Python 有许多第三方包

虽然有内置的库，但是不能涵盖程序员需要的全部。Python 程序员开发了大量你可以获得的免费开源库。您可以通过 Python 软件包索引(PyPI )( Python 软件的存储库)找到其中的许多。

Python 提供了名为 PIP 的默认包安装程序。您可以使用它方便地下载、安装、更新和删除包，管理虚拟环境，等等。

Anaconda 是一个第三方 Python 生态系统。它提供了 Python，名为 Conda 的包管理器，以及许多第三方库，如 NumPy，SciPy，Matplotlib，Flask 等等。Conda 可用于下载、安装、更新和删除软件包，以及管理虚拟环境。它擅长管理库之间的依赖关系。

使用 Anaconda，您还可以安装集成开发环境，如 Spyder、Jupyter Notebooks 和 Jupyter Labs。

NumPy 是有效操作数组的基本库。与纯 Python 相比，它带来了巨大的性能提升和更简洁的代码。SciPy 构建在 NumPy 之上，包含许多数学、统计和科学计算的函数和类。Pandas 也依赖于 NumPy，并允许非常方便地处理带标签的一维和二维数据集。Matplotlib 和 Bokeh 非常适合可视化数据。

Scikit-learn 通常是主要的机器学习包之一。它支持许多方法和算法。它也是建立在 NumPy 之上的。StatsModels 是高级统计的库，也可以应用于机器学习。Tensorflow、Theano、PyTorch、Kears 和其他方法可用于神经网络。BeautifulSoup 用于从网站中提取数据。NLTK(自然语言工具包)是最流行的自然语言处理工具之一。

Python 有几个 Web 开发框架。最受欢迎的是姜戈。这是一个全面的框架，遵循“包含电池”的方法。使用 Django，您可以非常快速地构建一个包含许多常用功能的大型 web 应用程序。Flask 是另一个 Python Web 框架。这是一个微框架，有许多外部扩展。对于较小的应用程序来说更容易。只需几行代码，您就可以获得一个可用的 Web 应用程序。还有其他框架:金字塔、瓶子、Web2py、CherryPy、Tornado 等等。

您可以使用 Tkinter 或 PyQt 来构建桌面应用程序，Kivy for android 应用程序，以及许多其他用于各种作业的库。

# 9.Python 是一种通用编程语言

Python 适用于各种应用，如数据科学和机器学习、Web 开发、物联网设备、桌面和移动应用开发，以及自动化重复任务。这是由于语言和解释器的简单性和灵活性。还有，由于大量第三方开源库的存在。

# 10.Python 和其他人玩得很好

Python 在某些情况下会很慢。然而，如果是这样的话，你很可能会找到一个用 C 或 C++编写的合适的库，或者可能是带有非常快的 Python 包装器的 Fortran。

你也可以用 C 创建 Python 例程，Python 是用 C 写的，用 C 扩展非常方便(当然前提是你熟悉它)。Cython 是一种静态编译器和编程语言，为纯 Python 提供了一些额外的功能。它用于以类似 Python 的风格编写快速代码，并将 Python 的灵活性与 c 的强大功能结合起来。

您还可以将 Python 与其他框架和编程语言结合起来，如。网还是锈。

# 结论

本文解释了使 Python 如此有用和流行的一些最重要的因素。

无论你是对数据科学、机器学习、web 开发、桌面应用、移动应用、科学计算感兴趣，还是仅仅对自动化感兴趣，Python 都值得考虑。

![](img/e1658326b5829189d024fbecd79166ad.png)

[Duomly — programming online courses](https://www.duomly.com)

感谢您的阅读。

本文由我们的队友米尔科提供。