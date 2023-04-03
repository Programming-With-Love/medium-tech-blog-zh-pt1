# 简单的时间戳到可读的日期转换器

> 原文：<https://medium.com/quick-code/easy-timestamp-to-readable-date-converter-5b93959a3cf9?source=collection_archive---------4----------------------->

![](img/0cc3c92d763f2ddf796e99ddf60636c2.png)

处理时间戳到可读日期的转换有时会很麻烦。有这么多不同的对象可用 **NSDate** ， **Date** ， **NSCalendar** ， **Calendar** ， **DateFormatter** ，这样的例子不胜枚举。现在，我应该用哪一个🤔？它变得令人困惑。因此，我编写了这段代码来帮助自己(您也可以帮助自己)处理手头上比将时间戳转换为可读日期更大的问题😐。

上面代码的快速总结。如果日期是今天，则输出为时间格式，如“**2:07AM”**。接下来，如果日期是昨天或明天，那么它输出"**昨天**或"**明天"**"，是的，非常明显。接下来，如果日期在当前周内，则输出为日格式，如“**星期三**”。本周之外的任何事情都会有一个**日期**，比如“**2018 年 1 月 20 日**”。您可以查看以下链接，了解 **DateFormatter 的所有不同格式。**

[](http://nsdateformatter.com/) [## NSDateFormatter.com-方便快捷和客观的日期格式

### nsdateformatter.com 是用 Swift 3 编写的，作为学习开源 Swift、Swift 包管理器和…

nsdateformatter.com](http://nsdateformatter.com/)