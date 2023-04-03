# 理解 Scala 中的 IO 单子

> 原文：<https://medium.com/walmartglobaltech/understanding-io-monad-in-scala-b495ca572174?source=collection_archive---------0----------------------->

![](img/df2064a3a89bc679244725d4b5099e2f.png)

source: [Google](https://lh3.googleusercontent.com/ldMu6jvZeDupPJMgOchaISqY0KWlZbqZ9WyUGdkDc3p1UZvd6PvInmTkroJdnNIID4AE=s162)

嘿伙计们！

在我的上一篇[帖子](/walmartlabs/type-safe-rest-services-in-scala-with-http4s-cats-io-288d6e23a90a)中，我们讨论了如何在 Scala 中建立一个简单的 REST 服务，其中我们使用了 cats-effect **IO Monad** 来包装我们的 ***effect-ful*** (或者你可以说***side-effect-ing***)代码，以确保它保持功能上的纯净(引用透明)。

现在，函数式编程世界的任何新手都会被这样的问题所困扰: