# 今天我学习了，在 Python 对象中检查属性存在

> 原文：<https://medium.easyread.co/today-i-learn-check-attribute-existence-in-python-object-2ebec0b27df7?source=collection_archive---------2----------------------->

## 原来字典键不是对象属性。

![](img/869fbed14615f220503b8f2ec67179bf.png)

Photo by [Syd Wachs](https://unsplash.com/@videmusart?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

在过去的一周里，我和我的同事正在学习在工作中使用 Python 编程语言，并决定用它编写一个简单的 worker。工人正在从外部 API 检索数据，用 HTTP 请求和 JSON 作为响应体，如果成功，数据将存储在我们自己的数据存储中。

当我们需要使用光标时，问题就来了，这样我们就可以从 API 中获取下一个数据。当当前请求之后有数据时，API 响应将包括一个`**cursor**`字段。为了检索下一页数据，我们需要将光标附加到请求的 URL 上，我们决定使用光标字段作为标识符。

我 **google** 关于`***how to check whether an attribute exists in python object***`的方法，得到了[结果](https://stackoverflow.com/questions/610883/how-to-know-if-an-object-has-an-attribute-in-python)使用 Python `**hasAttribute**`内置函数。我们试图在函数中使用它，如下所示:

在我尝试运行 worker 之后，不知何故它总是中断迭代，即使响应对象中存在`**cursor**`字段。

就像许多其他程序员一样，解决这个问题的下一步是**用更好的关键字**搜索解决方案，这次我用关键字`***hasattr python not working json object***`搜索，最后，我得到了我的[答案](https://stackoverflow.com/questions/30871419/python-requests-json-object-as-dictionary-does-not-recognize-its-own-keys-with)。

原来**字典键不是属性**。该属性存在于用 Python 中的**类**语法创建的**对象**中。因此，我检查**字典**中的**键**是否存在的问题的解决方案是使用一个简单的`**in**`操作符。

最后，我的员工可以检索下一个数据😬

希望这篇简单的笔记能帮助大家解决类似的问题，*快乐编码！*

# 参考

[](https://stackoverflow.com/questions/610883/how-to-know-if-an-object-has-an-attribute-in-python) [## 如何在 Python 中知道一个对象是否有属性

### 另一个可能的选项，但这取决于您之前所说的:undefined = object()class Widget:def…

stackoverflow.com](https://stackoverflow.com/questions/610883/how-to-know-if-an-object-has-an-attribute-in-python) [](https://stackoverflow.com/questions/30871419/python-requests-json-object-as-dictionary-does-not-recognize-its-own-keys-with) [## Python requests.json()对象作为字典无法识别其自身具有 hasattr()或 value 的键…

### 摘要:dictionary/json 对象表示它没有给定的键(使用 hasattr 调用或…中的值)

stackoverflow.com](https://stackoverflow.com/questions/30871419/python-requests-json-object-as-dictionary-does-not-recognize-its-own-keys-with) [](https://www.digitalocean.com/community/tutorials/how-to-construct-classes-and-define-objects-in-python-3) [## 面向对象编程:Python 中的类和对象|数字海洋

### Python 是一种面向对象的编程语言。面向对象编程(OOP)专注于创建可重用的…

www.digitalocean.com](https://www.digitalocean.com/community/tutorials/how-to-construct-classes-and-define-objects-in-python-3)