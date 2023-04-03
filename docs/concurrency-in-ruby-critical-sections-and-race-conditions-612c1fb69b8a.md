# Ruby 中的并发性:关键部分和竞争条件

> 原文：<https://medium.com/double-pointer/concurrency-in-ruby-critical-sections-and-race-conditions-612c1fb69b8a?source=collection_archive---------8----------------------->

*本课展示了关键部分中不正确的同步会如何导致争用情况和错误代码。对临界截面和竞态条件的概念进行了深入的解释。还包括一个比赛条件的可执行示例。*

> **别忘了去拿你的《Ruby on Rails:学习 Rails Web 开发》一书的复印件。**
> 
> **恳请考虑在** [**中**](https://bit.ly/3OvimpR) 上支持我们，或在 [**结账时帮助我们的一位伴侣乌达城**](https://bit.ly/3JIpvl4)**|**[**Coursera**T27**|**T30**plural sight**](https://imp.i384100.net/zaYBB0)