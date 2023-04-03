# 开发人员在 Mendix 中常犯的 6 个日期时间错误

> 原文：<https://medium.com/mendix/common-date-time-errors-in-mendix-16e6e1180161?source=collection_archive---------1----------------------->

![](img/b4c44f54a3583c000332e0521e23136e.png)

日期时间操作是编程中普遍存在的话题之一，但却没有得到太多关注。因此，许多开发人员在处理日期-时间时会犯一些愚蠢的错误。在这篇博客文章中，我们将特别关注[解析/格式化日期时间时的一些错误。](https://docs.mendix.com/refguide/parse-and-format-date-function-calls)

根据来自 real Mendix 应用程序的数据，按出现频率从最不常见到最常见(和微妙)进行排序。

**答:月对分**

小写的“mm”代表一小时中的一分钟，大写的“MM”代表一年中的一个月。它们不应该混在一起。月份应与年或日一起使用，而分钟应与小时一起使用。

示例:

`**parseDateTime('2015-05-21', 'yyyy-mm-dd')
parseDateTime('13:37', 'HH:MM')**`

B:现在等一会儿

小写的“S”代表秒，但大写的“S”代表毫秒，这种用法很少使用。

示例:

`parseDateTime('13:37:01', 'HH:mm:SS')`

今天是星期几？当然，今天是 2 月 42 日。

小写的“dd”代表一个月中的某一天，大写的“DD”代表一年中的某一天。尽管有后者的有效用例，但小写版本使用得更频繁。

示例:

`parseDateTime('2020-02-11', 'yyyy-MM-DD')`

现在是下午 17 点钟

小写字母“a”用于表示 AM 或 PM。这仅在与小写字母“h”结合使用时才有意义，h 的范围是 1–12。将“a”与大写的“H”结合使用没有什么意义

示例:

`formatDateTime($DateTime, 'HH:mm a')`

**E:小时的乐趣**

大写字母“H”的范围是 0–23，而“H”的范围是 1–12。另一方面，“K”在 0–11 的范围内，但“K”在“1–24”的范围内，这是非标准的，应该避免。

小写的“h”通常单独使用(而不是要求两位数)，并且总是与 AM/PM 说明符结合使用——否则，它是不明确的。“H”通常被用作“HH ”,例如，5 am 被表示为“05”。

示例:

`parseDateTime('13:37', 'hh:mm')`

你说谁软弱？

这是我最喜欢的一个。不像列表中的其他错误，通过一些测试就可以很容易地发现，这个错误几乎是不可能被发现的。这是因为，对于大多数日期来说，星期年(大写的“Y”)和年(小写的“Y”)是相同的。为了捕捉这个 bug，测试人员需要为 Y 和 Y 的值不相同的特定年份的第一周或最后一周编写案例。我甚至不会试图解释这背后的原因，所以这里有一个[链接](https://docs.oracle.com/javase/7/docs/api/java/util/GregorianCalendar.html)与确切的描述。

示例:

`formatDateTime(dateTime(1987,12,31), ‘dd MMM YYYY’)`1988 年 12 月 31 日结果**🤯🤯🤯**

我希望你喜欢读这篇文章，它能帮助你在未来少犯错误！

# Mendix 的自动代码审查程序

Mendix 的自动代码审查程序检查 100 多条不同类别的规则，如*安全性*、*性能*和*可维护性*。还有 [40 *可靠性*规则](https://sdf-docs.mansystems.com/docs/acr-rules/reliability/)它们的目标是在你的应用程序中发现如上所述的错误。

不要等待，免费注册，直接体验自动化代码审查的力量:[https://content.mansystems.com/acr-trial-request](https://content.mansystems.com/acr-trial-request)。不需要信用卡。

*最初发布于*[*https://www . ignition . so/gaj Duk/Finding-bugs-in-Mendix-modules-6ab 2707 fa 68 f 4 e 60 b 29 DDD 4 Fe 0a 23748*](https://www.notion.so/gajduk/Common-date-time-errors-in-Mendix-a5fd80fb1ca04affa44b6e6dc674ce20)*。*