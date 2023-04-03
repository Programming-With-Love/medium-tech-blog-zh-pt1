# Ruby 中的并发性:互斥与监控

> 原文：<https://medium.com/double-pointer/concurrency-in-ruby-mutex-vs-monitor-f72c6a8bb64d?source=collection_archive---------10----------------------->

在这一课中，我们来学习什么是监视器，以及它与互斥有什么不同。监视器是高级并发结构，特定于语言框架。

> **别忘了拿到你的** [**Ruby on Rails:用 Rails**](https://amzn.to/3HX0Zfl) **学习 Web 开发。**
> 
> **请考虑支持我们的** [**中型**](https://bit.ly/3OvimpR) **或结帐我们的合作伙伴之一**[**uda city**](https://bit.ly/3JIpvl4)**|**[**Coursera**](https://imp.i384100.net/zaYBB0)**|**[**plural sight**](https://pluralsight.pxf.io/Ao7GGK)

[![](img/5ab1de5b8118ce1de82a6dfb65cab063.png)](https://bit.ly/3bex1kP)

Master multi-threading in Ruby with: [**Ruby Concurrency for Senior Engineering Interviews**](https://bit.ly/3bex1kP)