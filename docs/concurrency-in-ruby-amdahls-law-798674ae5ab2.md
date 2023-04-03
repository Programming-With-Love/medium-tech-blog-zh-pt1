# Ruby 中的并发性:阿姆达尔定律

> 原文：<https://medium.com/double-pointer/concurrency-in-ruby-amdahls-law-798674ae5ab2?source=collection_archive---------15----------------------->

盲目地增加线程来加速程序执行可能并不总是一个好主意。在这一课中，让我们来看看阿姆达尔定律是如何解释程序并行化的。

> **别忘了拿到你的** [**Ruby on Rails:用 Rails**](https://amzn.to/3HX0Zfl) **学习 Web 开发。**
> 
> **请考虑支持我们的** [**中型**](https://bit.ly/3OvimpR) **或结帐我们的合作伙伴之一**[**uda city**](https://bit.ly/3JIpvl4)**|**[**Coursera**](https://imp.i384100.net/zaYBB0)**|**[**plural sight**](https://pluralsight.pxf.io/Ao7GGK)

[![](img/5ab1de5b8118ce1de82a6dfb65cab063.png)](https://bit.ly/3bex1kP)

Master multi-threading in Ruby with: [**Ruby Concurrency for Senior Engineering Interviews**](https://bit.ly/3bex1kP)