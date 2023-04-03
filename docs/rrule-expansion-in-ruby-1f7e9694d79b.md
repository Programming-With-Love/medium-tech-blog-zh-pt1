# Ruby 中的规则扩展

> 原文：<https://medium.com/square-corner-blog/rrule-expansion-in-ruby-1f7e9694d79b?source=collection_archive---------5----------------------->

> 注意，我们已经行动了！如果您想继续了解 Square 的最新技术内容，请访问我们在 https://developer.squareup.com/blog[的新家](https://developer.squareup.com/blog)

在 Square Appointments 团队中，我们经常需要处理重复的事件——从简单的情况(比如每周一次的午餐会议)到更复杂的情况(比如每隔一个月的倒数第二个星期二理发，9 月 19 日除外)。幸运的是， [iCalendar 规范](https://tools.ietf.org/html/rfc5545)提供了一种简洁的方式来表示所有这些情况。我们面临的挑战是将一个文本规则转换成一组我们可以在应用程序中使用的实际日期。考虑到 Ruby 庞大的 gem 生态系统，我们期望立刻找到一些东西来执行我们的任务，但是找不到任何我们真正需要的东西。

## 现有的 Ruby 工具不太适合

Ruby 生态系统已经包含了处理重复出现的优秀宝石——[ice _ cube](https://github.com/seejohnrun/ice_cube)可能是最受欢迎的。`ice_cube`允许你通过一个流畅的 Ruby 界面创建递归，并且可以将 Ruby 中创建的递归转换成它的 RRULE 文本等价物。不幸的是，我们的问题有点不同。我们主要处理嵌入在从谷歌日历导入的重复事件中的规则，并将它们转换成一组 Ruby `Time`对象——本质上是`ice_cube`要解决的问题的逆过程。`ice_cube`确实提供了一些解析 RRULEs 的能力，但是不支持一些选项(比如 BYSETPOS，指定“一个月中的第二个和第四个星期三”的能力)，而且我们找不到一种方法在不大量修改 gem 的情况下整合我们需要的特性。因为我们处理来自 Google 的外部数据，这些数据不在我们的控制之下，并且可能包含任何有效的规则，所以我们需要尽可能地支持完整的 iCalendar 规范。

有*的*库正好满足我们的需求——但不幸的是，它们在 [Python](https://github.com/dateutil/dateutil) 和 [JS](https://github.com/jakubroztocil/rrule) 中。如果你曾经编写过日期/时间代码，你就会知道很容易出错——一个接一个的错误比比皆是(你的月份列表是 0 索引的还是 1 索引的？).在不引入细微错误的情况下，将其中一个库重写到 Ruby 中似乎是一项艰巨的任务。

## 救援的详尽测试

幸运的是，我们正试图解决一个非常适合数据驱动测试套件的界限分明的问题——我们有一组已经编写好的测试，我们的移动团队在几个月前已经用它们测试了 Objective-C 中的另一个 RRULE 解析实现。这个测试套件几乎涵盖了我们能想到的所有 RRULE 选项的组合。

这个测试套件让我们有信心对 Python 的 dateutil 库进行逐行重写(当它们明显适用时，添加一些 Ruby 习惯用法)。我承认，当我写下我们新图书馆的第一个版本时，我并不理解我抄写的大部分内容——但这没关系。只要测试通过了，我就知道它是有效的。一旦我们使用了 green，我们就可以重构成更容易理解和惯用的 Ruby，相信我们广泛的测试会保证我们的安全。

## 最终产品

我们刚刚发布了名为`rrule`的内部库，这是一个小宝石，完全专注于从给定的规则和循环开始日期生成`Time`实例的特定任务。它允许您做以下事情:

```
rule = RRule::Rule.new(‘FREQ=WEEKLY;INTERVAL=2;COUNT=8;WKST=SU;BYDAY=TU,TH’, dtstart: Time.parse(‘Tue Sep 2 06:00:00 PDT 1997’), tzid: ‘America/New_York’)
rule.all
=> [Tue, 02 Sep 1997 09:00:00 EDT -04:00,
Thu, 04 Sep 1997 09:00:00 EDT -04:00,
Tue, 16 Sep 1997 09:00:00 EDT -04:00,
Thu, 18 Sep 1997 09:00:00 EDT -04:00,
Tue, 30 Sep 1997 09:00:00 EDT -04:00,
Thu, 02 Oct 1997 09:00:00 EDT -04:00,
Tue, 14 Oct 1997 09:00:00 EDT -04:00,
Thu, 16 Oct 1997 09:00:00 EDT -04:00]
```

我们已经在 Square 的生产中使用它大约一年了，所以我们对它的准确性很有信心。目前，它支持我们在 Google data 中遇到的所有情况，但我们希望最终更进一步，支持整个 iCalendar 规范(它可以指定秒级的重复)。如果你正在编写一个 Ruby 应用，并且正在处理规则，我们希望`rrule`能对你有所帮助。你可以在 https://github.com/square/ruby-rrule 的[找到源代码。](https://github.com/square/ruby-rrule)