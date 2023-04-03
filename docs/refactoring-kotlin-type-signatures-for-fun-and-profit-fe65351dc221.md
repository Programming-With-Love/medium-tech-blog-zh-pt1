# 重构 Kotlin 类型-为了乐趣和利益的签名

> 原文：<https://medium.com/google-developer-experts/refactoring-kotlin-type-signatures-for-fun-and-profit-fe65351dc221?source=collection_archive---------4----------------------->

在这篇文章中，我从一个意想不到的来源获得了 [http4k](https://http4k.org) 的核心类型签名，使用了重构和 FP 技术，这些技术通常与编码相关联。

# TL；速度三角形定位法(dead reckoning)

> ***“你可以应用简单的重构和函数技术，比如替换和奉承，来提炼函数签名。没有任何实现细节，以这种方式定义系统概念可能是一种令人耳目一新的强大技术。”***

![](img/43dd11442c9dcf90dcd616c2117f3c8b.png)

在最近的一集 [TalkingKotlin](https://talkingkotlin.com/http-as-a-function-with-http4k/) 探索 [http4k](https://http4k.org) 中，我们与 [Hadi Hariri](https://hadihariri.com/) 讨论了 http4k 的起源受到了最初的[“你的服务器作为一种功能”](https://monkey.org/~marius/funsrv.pdf) (SaaF)论文的启发。我注意到其中描述的模式与 Java [Servlet Filter](https://www.oracle.com/java/technologies/filters.html) API 中使用的模式相同，并且我们已经从中派生出了最终的 [http4k](https://http4k.org) 核心类型`HttpHandler`和`Filter`。

只是为了再次确认我没有记错，我觉得重温这个过程会很有趣。回过头来看，这与我们重构代码的过程非常相似，但是在更概念性的层面上。在这种情况下，我们可以将各个步骤映射到 IntelliJ 提供的强大重构功能上。

回顾一下，Java Servlet 过滤器 API 定义了以下内容:

如果我们把这些签名转换成科特林，我们得到:

由于 http4k 是一个受函数式编程启发很大的库，所以我们希望使我们的类型不可变，而 Servlet 类型依赖于改变 http 消息。为了保持函数的纯粹性和无副作用，我们应该从我们的服务中返回一个不可变的`ServletResponse`，而不是对其进行变异并返回`Unit`，所以让我们使用 ***更改签名(Cmd+F6)*** 来实现:

我们可以接着 ***改变签名(Cmd+F6)*** 再对`Filter`的参数重新排序:

一件在函数式语言中很常见的事情，但是我们目前不能在 IDE 中做(提示提示 JetBrains！)就是应用[curry](https://en.wikipedia.org/wiki/Currying)将`Filter`拆分成高阶函数(即。返回另一个函数的函数):

通过这样做，我们可以看到，当类型签名匹配时，我们可以将`FilterChain`替换为`Filter`:

最后，我们可以做一些 Kotlin 替换和 ***重命名(Shift+F6)*** 来让我们回到我们所知道和喜爱的 [http4k](https://http4k.org) 类型:

所以你有它。原来[一切旧的(其实)又是新的](https://www.youtube.com/watch?v=w36J7HDszcI)！

*喜欢这篇文章吗？你可以在*[***https://dentondav.id/writing***](https://dentondav.id/writing)*阅读我所有的科技文章。*