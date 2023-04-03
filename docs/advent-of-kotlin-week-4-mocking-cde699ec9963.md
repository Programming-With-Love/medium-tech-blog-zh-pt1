# 科特林的来临，第四周:界面模拟

> 原文：<https://blog.kotlin-academy.com/advent-of-kotlin-week-4-mocking-cde699ec9963?source=collection_archive---------0----------------------->

![](img/fd7d3a1d1787e71c9e404b328fc80daa.png)

欢迎来到科特林降临的最后一周[。后面还有许多巨大的挑战，前面还有最后一个也是最具挑战性和最开放的任务。这次我们会觉得有点像嘲讽库的开发者。](/the-advent-of-kotlin-2018-week-1-229e442a143)

我们每天都在使用类似 [Mockito](https://site.mockito.org/) 或[mock](https://mockk.io/)这样的嘲讽库。我们信任它们，并期望它们总是正确工作。我们经常把嘲讽当成开发必备，也经常忘记实现一个嘲讽库有多难。老实说，自从我开始深入研究这些库，我就有一种感觉，它们的存在以及它们的良好运行是一个奇迹。很高兴有人花了这么多时间让这一切成为可能。一部分是为了表示敬意，一部分是因为对你使用的所有东西的工作原理有所了解是有好处的，本周我们将实现一个非常简单的接口模仿 JVM。

要弄清楚的是:类模仿更复杂，通常使用一些额外的库如 ByteBuddy 来完成。尽管由于 Java 的支持，界面模仿变得更加容易。我们将使用的对象是[代理](https://docs.oracle.com/javase/7/docs/api/java/lang/reflect/Proxy.html)。

# 界面模拟

我们想要实现的是一种创建对象 mock 的方法，它的行为类似于一个接口，但它不是实现一个接口，而是基于某种规范来操作。例如，在[mock](https://mockk.io/)中，我们可以用以下方式指定它:

如你所见，在`every`中，我们指定当我们请求方法时应该返回什么。我们没有实现一个库，也不需要 DSL，所以我们可以用一个更简单的 API 来代替它:

这里我们可以看到方法`setReturnValue`只说明应该返回什么，方法`setBody`可以用来指定这个函数的整体。我们也应该能够使用论点。

# 接口代理

这个任务相对简单，因为 Java 内置了对代理接口的支持。什么是接口代理？它是一个伪装成接口的对象，它不实现方法，而是将所有调用指向同一个函数。让我们看一个例子:

如何使用这个代理的一个很好的例子是[改造](https://square.github.io/retrofit/)。在这个库中，我们使用方法和注释在接口中定义网络调用:

使用这些定义，我们可以创建实例并使用它们进行互联网呼叫:

正如您所猜测的，方法返回一个代理。[在这里你可以看到它的代码](https://github.com/square/retrofit/blob/master/retrofit/src/main/java/retrofit2/Retrofit.java#L128)。每个用法都被传递给同一个处理程序，然后根据接口中使用的注释，构造网络调用。

同样的方式，我们可以实现界面模拟。对于每个实例，我们可以只保存关于每个调用应该如何被服务的信息，并在使用这个方法时使用它。最大的问题是我们如何传递这些信息？答案是你需要允许一个特殊的录制模式。

[![](img/0742a8ad0cfd3851db2d28061bf6f214.png)](https://leanpub.com/effectivekotlin/c/3YYtCtqCC6a4)

# 记录方式

最大的问题是如何传递一条应该返回或调用的信息。我们不能将它直接传递给对象，因为它已经被模仿了，并且它没有任何方法，除了接口上的那些方法。我们需要在调用这些方法时传递这些信息:

怎么会？最简单的回答就是这次切换一些录音模式。这并不漂亮，也不是线程安全的，但是如果我们假设在同一时间只有一个测试运行，并且这些是测试而不是生产代码，这就很好了。

那么在我们的代理中，我们会说:

如果你有更好的主意，就去做吧。请记住，这里的首要任务是让它发挥作用。这是一个困难且非常不寻常的问题，需要一些黑客技术。Java 不是为支持模仿而构建的。

# 工作

实现简单的接口模拟来实现这一点:

如果你更有野心，你可以推进它，也允许一些争论。我们可以假设它们作为列表`Any`传递给函数:

用单一和多种方法测试不同的接口。当一个体被两次设置为相同的方法时会发生什么？

# 应用挑战

本周末，我们将分享我们最喜欢的解决方案。如果你想让我们考虑你的解决方案，你需要在 Twitter 上分享一个代码链接(可以是 GitHub 片段或 Kotlin REPL 的链接)，标签为#AdventOfKotlin18。或者，您可以将您的解决方案发送到电子邮件 contact@kt.academy。最后期限是 12 月 23 日中午 12 点(欧洲中部时间)。更多详情[此处](/the-advent-of-kotlin-2018-week-1-229e442a143)，祝好运:)

# 结果

这是一项深思熟虑的任务，然而还是有英雄解决了它。我要向他们每一个人表示祝贺。我从以下方面得到了很好的回答:

*   何塞·伊格纳西奥·阿辛波佐( [SO](https://stackoverflow.com/users/6783451/jose-ignacio-acin-pozo) ， [GitHub](https://github.com/Ganet) )
*   克里斯蒂安·福赫斯
*   巴拉兹·内梅思
*   彼得·古利亚斯
*   奥利弗·佩雷斯

每个人都正确地实现了基本的模仿功能。这个功能可以用这个极简的实现来展示:

然后可以添加一些 DSL，如 Oliver Perez 的实现:

在[他的知识库](https://github.com/olivierperez/AdventOfCode2018)上查看一下。

这个挑战最难的部分是使用`any()`而不是一个参数。最后，我们需要使用这个函数，所以需要传递一些东西。应该是什么？

最简单的想法是让用户决定它应该是什么:

这个解决方案是克里斯蒂安·福赫斯发来的，我必须说，这个技巧的简单性给我留下了深刻的印象。整个实现都在 Github 上:

[](https://github.com/fuchsch1234/AdventOfKotlin2018) [## fuchsch1234/AdventOfKotlin2018

### 通过在 GitHub 上创建帐户，为 fuchsch1234/AdventOfKotlin2018 开发做出贡献。

github.com](https://github.com/fuchsch1234/AdventOfKotlin2018) 

我最喜欢的实现是何塞·伊格纳西奥·阿信·波佐发的( [SO](https://stackoverflow.com/users/6783451/jose-ignacio-acin-pozo) ， [GitHub](https://github.com/Ganet) )。这是我们这周的冠军。令人惊讶的是，这是第一个 send 实现。整个代码组织良好，功能强大，这就是他如何解决获取任何值的问题:

换句话说:如果它是一个原语或者一个已知类型，使用一个特殊映射中的实例。否则，使用不带任何参数的构造函数创建实例。此外，缓存值不会为已创建的类型创建新的实例。这近乎完美。通过检查这个构造函数是否存在，如果不存在，就用多一个参数检查一个构造函数，这可能会向前推进一步。然后用同样的方法创建随机值来填充这些参数。不完美，但足够好了。如你所见，编写嘲讽库绝对不容易。

该解决方案以及之前的所有解决方案都可以在 GitHub 上找到:

[](https://github.com/Ganet/AdventOfKotlin2018) [## Ganet/AdventOfKotlin2018

### Kt 的 AdventOfKotlin 2018 每周练习。学院- Ganet/AdventOfKotlin2018

github.com](https://github.com/Ganet/AdventOfKotlin2018) 

干得好！

## 单击👏说“谢谢！”并帮助他人找到这篇文章。

你需要 Kotlin 工作室吗？访问我们的网站[看看我们能为您做些什么。](https://www.kt.academy/)

了解卡帕头最新的重大新闻。Academy，[订阅时事通讯](https://kotlin-academy.us17.list-manage.com/subscribe?u=5d3a48e1893758cb5be5c2919&id=d2ba84960a)，[观察 Twitter](https://twitter.com/ktdotacademy) 并在 medium 上关注我们。

[![](img/3146970f03e44cb07afe660b0d43e045.png)](http://eepurl.com/diMmGv)