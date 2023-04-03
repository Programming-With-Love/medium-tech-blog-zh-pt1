# 使用 Python 从 Flipkart 网站抓取数据

> 原文：<https://medium.com/edureka/web-scraping-with-python-d9e6506007bf?source=collection_archive---------0----------------------->

![](img/9da970ba3944b434c9ff9aa13205e168.png)

Wen Scraping with Python - Edureka

想象一下，你必须从网站上获取大量的数据，并且你想尽可能快地完成它。如果不手动访问每个网站并获取数据，你会怎么做？嗯，“网络抓取”就是答案。网络抓取只是使这项工作更容易和更快。

在这篇关于使用 Python 进行 web 抓取的文章中，您将简要了解 Web 抓取，并通过演示了解如何从网站中提取数据。我将涉及以下主题:

*   为什么是网络抓取？
*   什么是网页抓取？网络抓取合法吗？
*   为什么使用 Python 进行网络抓取？
*   网页抓取是如何工作的？
*   用于网页抓取的库
*   演示:抓取 Flipkart 网站

# 为什么是网络抓取？

网络抓取用于从网站上收集大量信息。但是为什么一定要有人从网站上收集这么大的数据呢？为了了解这一点，让我们来看看网络抓取的应用:

> **价格比较:**parse hub 等服务使用网络抓取从在线购物网站收集数据，并使用它来比较产品的价格。
> 
> **电子邮件地址收集:**许多使用电子邮件作为营销媒介的公司使用网络搜集来收集电子邮件 ID，然后发送批量电子邮件。
> 
> **社交媒体抓取:**网络抓取用于从 Twitter 等社交媒体网站收集数据，以了解流行趋势。
> 
> **研发:**网页抓取用于收集大量数据(统计、一般信息、温度等。)从网站上下载，对其进行分析并用于调查或研发。
> 
> **职位列表:**从不同的网站收集有关职位空缺、面试的详细信息，然后在一个地方列出，以便用户可以轻松访问。

# 什么是网页抓取？网络抓取合法吗？

网络抓取是一种用于从网站中提取大量数据的自动化方法。网站上的数据是非结构化的。Web 抓取有助于收集这些非结构化数据，并以结构化形式存储。有不同的方法来抓取网站，如在线服务、API 或编写自己的代码。在本文中，我们将看到如何用 python 实现 web 抓取。

![](img/eedfe031cb70a5d6865053015f68c5fe.png)

谈论网页抓取是否合法，有些网站允许网页抓取，有些不允许。想知道一个网站是否允许网页抓取，可以看看网站的“robots.txt”文件。您可以通过将“/robots.txt”附加到您想要抓取的 URL 来找到该文件。对于这个例子，我正在抓取 Flipkart 网站。所以，要看“robots.txt”文件，网址是[www.flipkart.com/robots.txt.](http://www.flipkart.com/robots.txt)

# 为什么使用 Python 进行网络抓取？

你可能听说过 Python 有多棒。但是，其他语言也是如此。那我们为什么要选择 Python 而不是其他语言进行网页抓取呢？

下面是 Python 的特性列表，这些特性使它更适合 web 抓取。

> **易用性:** Python 编码简单。您不必添加分号“；”或花括号“{}”的任何位置。这使得它不那么凌乱，易于使用。
> 
> **大量的库:** Python 拥有大量的库，如 Numpy、Matlplotlib、Pandas 等。，它为各种目的提供方法和服务。因此，它适用于 web 抓取和提取数据的进一步操作。
> 
> **动态类型:**在 Python 中，你不必为变量定义数据类型，你可以在任何需要的地方直接使用变量。这样可以节省时间，让你的工作更快。
> 
> 易于理解的语法: Python 语法易于理解主要是因为阅读 Python 代码与阅读英语语句非常相似。它富有表现力，易于阅读，Python 中使用的缩进也有助于用户区分代码中不同的范围/块。
> 
> **小代码，大任务:**使用网页抓取节省时间。但是你花再多时间写代码又有什么用呢？嗯，你不需要。在 Python 中，你可以编写小代码来完成大任务。因此，即使在编写代码时，您也节省了时间。
> 
> **社区:**写代码的时候卡住了怎么办？你不用担心。Python 社区有一个最大最活跃的社区，你可以在那里寻求帮助。

# 网页抓取是如何工作的？

当您运行 web 抓取代码时，会向您提到的 URL 发送一个请求。作为对请求的响应，服务器发送数据并允许您读取 HTML 或 XML 页面。然后，代码解析 HTML 或 XML 页面，找到数据并提取出来。

要通过 python 使用 web 抓取来提取数据，您需要遵循以下基本步骤:

1.  找到您想要抓取的 URL
2.  检查页面
3.  查找要提取的数据
4.  写代码
5.  运行代码并提取数据
6.  以要求的格式存储数据

现在让我们看看如何使用 Python 从 Flipkart 网站提取数据。

# 用于网页抓取的库

众所周知，Python 被用于各种应用，并且有不同的库用于不同的目的。在我们的进一步演示中，我们将使用以下库:

*   **Selenium** : Selenium 是一个 web 测试库。它用于自动化浏览器活动。
*   **Beautiful Soup**:Beautiful Soup 是一个解析 HTML 和 XML 文档的 Python 包。它创建解析树，有助于轻松提取数据。
*   Pandas 是一个用于数据操作和分析的库。它用于提取数据并以所需的格式存储数据。

# 演示:抓取 Flipkart 网站

先决条件:

*   安装了 **Selenium** 、 **BeautifulSoup、pandas** 库的 Python 2.x 或 Python 3.x
*   谷歌浏览器
*   Ubuntu 操作系统

*让我们开始吧！*

## 第一步:找到你想要抓取的网址

在这个例子中，我们将抓取 **Flipkart** 网站来提取笔记本电脑的价格、名称和评级。本页面网址为[https://www . flipkart . com/laptops/~ buy back-guarantee-on-laptops-/pr？sid = 6bo % 2Cb5g&uniqbstoreparam 1 = val 1&wid = 11 . product card . PMU _ V2](https://www.flipkart.com/laptops/~buyback-guarantee-on-laptops-/pr?sid=6bo%2Cb5g&uniqBStoreParam1=val1&wid=11.productCard.PMU_V2)。

## **第二步:检查页面**

数据通常嵌套在标签中。因此，我们检查页面，看看我们想要抓取的数据嵌套在哪个标签下。要检查页面，只需右键单击元素，然后单击“检查”。

![](img/29f97d25de44ac84f82556d76b3292f3.png)

当你点击“检查”标签，你会看到一个“浏览器检查框”打开。

![](img/54b18b92359d395b59ad1a6fd8224b68.png)

## 步骤 3:找到您想要提取的数据

让我们分别提取嵌套在“div”标签中的价格、名称和评级。

## 步骤 4:编写代码

首先，让我们创建一个 Python 文件。为此，在 Ubuntu 中打开终端，并键入 gedit <your file="" name="">。py 扩展名。</your>

我将把我的文件命名为“web-s”。命令如下:

```
gedit web-s.py
```

现在，让我们在这个文件中编写代码。

首先，让我们导入所有必需的库:

```
**from** selenium **import** webdriver
**from** BeautifulSoup **import** BeautifulSoup
**import** pandas as pd
```

要配置 web 驱动程序使用 Chrome 浏览器，我们必须设置 chromedriver 的路径

```
driver = webdriver.Chrome("/usr/lib/chromium-browser/chromedriver")
```

请参考下面的代码来打开 URL:

```
products=[] #List to store name of the product
prices=[] #List to store price of the product
ratings=[] #List to store rating of the product
driver.get("https://www.flipkart.com/laptops/~buyback-guarantee-on-laptops-/pr?sid=6bo%2Cb5g&amp;amp;amp;uniqBStoreParam1=val1&amp;amp;amp;wid=11.productCard.PMU_V2")
```

现在我们已经编写了打开 URL 的代码，是时候从网站中提取数据了。如前所述，我们想要提取的数据嵌套在

标签中。因此，我将找到具有相应类名的 div 标签，提取数据并将数据存储在一个变量中。参考下面的代码:

```
content = driver.page_source
soup = BeautifulSoup(content)
for a in soup.findAll('a',href=True, attrs={'class':'_31qSD5'}):
name=a.find('div', attrs={'class':'_3wU53n'})
price=a.find('div', attrs={'class':'_1vC4OE _2rQ-NK'})
rating=a.find('div', attrs={'class':'hGSR34 _2beYZw'})
products.append(name.text)
prices.append(price.text)
ratings.append(rating.text)
```

## 步骤 5:运行代码并提取数据

要运行代码，请使用下面的命令:

```
python web-s.py
```

# 步骤 6:以所需的格式存储数据

提取数据后，您可能希望以某种格式存储它。这种格式根据您的要求而有所不同。在本例中，我们将以 CSV(逗号分隔值)格式存储提取的数据。为此，我将在代码中添加以下代码行:

```
df **=** pd.DataFrame({'Product Name':products,'Price':prices,'Rating':ratings})df.to_csv('products.csv', index**=**False, encoding**=**'utf-8')
```

现在，我将再次运行整个代码。

创建一个文件名“products.csv ”,该文件包含提取的数据。

![](img/ddc9fc70c7444e7a63e8c1f5387d2084.png)

我希望你们喜欢这篇关于“使用 Python 进行 Web 抓取”的文章。我希望这篇博客能给你提供信息，并增加你的知识价值。现在继续尝试网络抓取。尝试 Python 的不同模块和应用程序。

我希望我的博客“用 Python 进行 Web 抓取”对你有所帮助。如果你想查看更多关于人工智能、DevOps、道德黑客等市场最热门技术的文章，你可以参考 Edureka 的官方网站。

请留意本系列中的其他文章，它们将解释 Python 和数据科学的各个方面。

> 1. [Python 教程](/edureka/python-tutorial-be1b3d015745)
> 
> 2. [](/edureka/python-functions-f0cabca8c4a) [Python 编程语言](/edureka/python-programming-language-fc1015de7a6f)
> 
> 3. [Python 函数](/edureka/python-functions-f0cabca8c4a)
> 
> 4.[Python 中的文件处理](/edureka/file-handling-in-python-e0a6ff96ede9)
> 
> 5. [](/edureka/scikit-learn-machine-learning-7a2d92e4dd07) [Python Numpy 教程](/edureka/python-numpy-tutorial-89fb8b642c7d)
> 
> 6. [Scikit 学习机器学习](/edureka/scikit-learn-machine-learning-7a2d92e4dd07)
> 
> 7. [Python 熊猫教程](/edureka/python-pandas-tutorial-c5055c61d12e)
> 
> 8. [Matplotlib 教程](/edureka/python-matplotlib-tutorial-15d148a7bfee)
> 
> 9. [Tkinter 教程](/edureka/tkinter-tutorial-f655d3f4c818)
> 
> 10.[请求教程](/edureka/python-requests-tutorial-30edabfa6a1c)
> 
> 11. [PyGame 教程](/edureka/pygame-tutorial-9874f7e5c0b4)
> 
> 12. [OpenCV 教程](/edureka/python-opencv-tutorial-5549bd4940e3)
> 
> 13. [PyCharm 教程](/edureka/pycharm-tutorial-d0ec9ce6fb60)
> 
> 14.[机器学习教程](/edureka/machine-learning-tutorial-f2883412fba1)
> 
> 15.[Python 中从头开始的线性回归算法](/edureka/linear-regression-in-python-e66f869cb6ce)
> 
> 16.[面向数据科学的 Python](/edureka/learn-python-for-data-science-1f9f407943d3)
> 
> 17. [Python 正则表达式](/edureka/python-regex-regular-expression-tutorial-f2d17ffcf17e)
> 
> 18.[Python 中的循环](/edureka/loops-in-python-fc5b42e2f313)
> 
> 19. [Python 项目](/edureka/python-projects-1f401a555ca0)
> 
> 20.[机器学习项目](/edureka/machine-learning-projects-cb0130d0606f)
> 
> 21.[Python 中的数组](/edureka/arrays-in-python-14aecabec16e)
> 
> 22.[在 Python 中设置](/edureka/sets-in-python-a16b410becf4)
> 
> 23.[Python 中的多线程](/edureka/what-is-mutithreading-19b6349dde0f)
> 
> 24. [Python 面试问题](/edureka/python-interview-questions-a22257bc309f)
> 
> 25. [Java vs Python](/edureka/java-vs-python-31d7433ed9d)
> 
> 26.[如何成为一名 Python 开发者？](/edureka/how-to-become-a-python-developer-462a0093f246)
> 
> 27. [Python Lambda 函数](/edureka/python-lambda-b84d68d449a0)
> 
> 28.[网飞如何使用 Python？](/edureka/how-netflix-uses-python-1e4deb2f8ca5)
> 
> 29.[Python 中的套接字编程是什么](/edureka/socket-programming-python-bbac2d423bf9)
> 
> 30. [Python 数据库连接](/edureka/python-database-connection-b4f9b301947c)
> 
> 31. [Golang vs Python](/edureka/golang-vs-python-5ac32e1ef2)
> 
> 32. [Python Seaborn 教程](/edureka/python-seaborn-tutorial-646fdddff322)
> 
> 33. [Python 职业机会](/edureka/python-career-opportunities-a2500ce158de)

*原载于 2018 年 11 月 14 日*[*www.edureka.co*](https://www.edureka.co/blog/web-scraping-with-python/)*。*