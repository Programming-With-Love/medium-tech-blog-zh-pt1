# JavaScript 快速提示—避免串行请求瀑布

> 原文：<https://medium.com/javascript-scene/javascript-quick-tip-avoid-serial-request-waterfalls-d03c4021d5fa?source=collection_archive---------2----------------------->

![](img/a1ebcd07e60eca2671352baa07e92ca8.png)

一个经常出现并对应用程序性能有严重影响的问题是，可能会意外地以串行方式获取本来可以并行获取的数据。不要在你使用承诺的任何地方丢下一个等待。相反，考虑一下抓取依赖项。如果你要获取多个东西，确保尽可能并行获取。这将极大地提高应用程序的性能。

这里有一些示例代码供您使用。

***埃里克·艾略特*** *是一位科技产品和平台顾问，《 [*【作曲软件】*](https://leanpub.com/composingsoftware)*[*【EricElliottJS.com】*](https://ericelliottjs.com)*[*devanywhere . io*](https://devanywhere.io)*的联合创始人，以及 dev 团队导师。他曾为 Adobe Systems、* ***、Zumba Fitness、*** ***【华尔街日报、*******【ESPN、*******【BBC】****等顶级录音艺人和包括* ***Usher、【Metallica】********

*他和世界上最美丽的女人享受着与世隔绝的生活方式。*