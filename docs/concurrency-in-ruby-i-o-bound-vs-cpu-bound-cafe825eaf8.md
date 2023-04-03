# Ruby 中的并发性:I/O 受限与 CPU 受限

> 原文：<https://medium.com/double-pointer/concurrency-in-ruby-i-o-bound-vs-cpu-bound-cafe825eaf8?source=collection_archive---------9----------------------->

在这一课中，我们将深入研究具有不同资源使用概况的程序的特征，以及这些特征如何影响程序设计选择。

> **别忘了拿到你的** [**Ruby on Rails:用 Rails**](https://amzn.to/3HX0Zfl) **学习 Web 开发。**
> 
> **请考虑支持我们的** [**中型**](https://bit.ly/3OvimpR) **或结帐我们的合作伙伴之一**[**uda city**](https://bit.ly/3JIpvl4)**|**[**Coursera**](https://imp.i384100.net/zaYBB0)**|**[**plural sight**](https://pluralsight.pxf.io/Ao7GGK)

[![](img/5ab1de5b8118ce1de82a6dfb65cab063.png)](https://bit.ly/3bex1kP)

Master multi-threading in Ruby with: [**Ruby Concurrency for Senior Engineering Interviews**](https://bit.ly/3bex1kP)