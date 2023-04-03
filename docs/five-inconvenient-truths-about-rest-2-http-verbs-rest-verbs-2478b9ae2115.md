# 关于休息的五个不方便的真相:2 — HTTP 动词≦“休息动词”

> 原文：<https://medium.com/compendium/five-inconvenient-truths-about-rest-2-http-verbs-rest-verbs-2478b9ae2115?source=collection_archive---------0----------------------->

重用通常是一件好事，但前提是您的需求和工具提供的功能之间的不匹配相对较小。将 HTTP 动词作为“REST 动词”重用肯定有很多优点，但也有很多缺点。关于何时使用 POST 或 PUT 的普遍困惑只是一个例子，但却是可以克服的。使用 HTTP 动词作为 REST 动词还有其他更严重的问题。

有两个证据证明 HTTP 动词有一个很大的问题，尤其是在应用于 RESTful webservices 时。第一部分是维基百科页面上关于 [REST](https://en.wikipedia.org/wiki/Representational_state_transfer) 的“[URL 和 HTTP 方法](https://en.wikipedia.org/wiki/Representational_state_transfer#Relationship_between_URL_and_HTTP_Methods)之间的关系”部分，以及其他网站和教程上的类似部分。在维基百科中，有一个表格试图解释四个主要的 HTTP 动词 GET、POST、PUT 和 DELETE 在 RESTful API 的基本用例中应该如何使用。尽管这个表格很好地解释了这四个动词应该如何使用，但是大多数新手还是会弄错，并且在他们的第一个 API 中犯很多错误。我知道我做到了，我也看到很多其他人这样做。

特别是动词 PUT 和 POST 的使用造成了很多混乱。在谷歌上搜索关键词 [REST PUT POST](https://www.google.com/search?q=REST+PUT+POST) ，这是我第二次发现 HTTP 动词存在很大问题的证据:它产生了大约 4.91 亿个结果(在我撰写本文时)，其中许多热门结果都是论坛上关于何时使用 PUT 和 POST 的问题的链接。

我不知道在 HTTP 动词只是 HTTP 动词的时代，PUT 和 POST 有多混乱。但我的感觉是，这没多大关系，因为当有人把这两者混在一起时，并没有太大的区别。只要服务器能够接收和处理 HTML 表单，(几乎)没有人关心它是使用 PUT 还是 POST 完成的。反正没有人发布 HTTP APIs，所以你必须对一个网站进行抓取才能发现这样的错误。即使这样，你可能也不会太在意。

随着 REST 的出现，这种情况发生了变化。突然，人们开始发布他们的 REST APIs，使用正确的 HTTP 动词成为一个更大的问题，因为它携带了 API 中方法的部分语义。混淆两个动词，或者以这样或那样的方式错误地使用其中一个，突然意味着你的 REST API 变得更难阅读和理解。这与强迫用户用错误的 HTTP 方法提交 HTML 表单是不同的，只要它发生在幕后。对于 HTTP，什么工作得很好——或者我们应该说，至少“足够好”——成了 REST 中的一个大问题。

人们可能会想，如果 REST 创建了自己的一组动词，而不是重用 HTTP 动词，会发生什么。我们会不会以同样的方式结束，在 PUT 的 REST 版本和 POST 的 REST 版本之间产生很多混淆？或者我们会不会找到一组更容易理解的动词？一种更接近于 CRUD 的方法肯定会解决 PUT 和 POST 的混淆，但话说回来，CRUD 可能会产生另一组更大的问题。

也许从头开始的设计会产生一个更具可扩展性的方法？还记得我在[上一篇文章](https://grensesnittet.computas.com/five-inconvenient-truths-about-rest-1-get-calls-are-never-nullipotent/)中的例子吗，在那里我讨论了一次性下载的正确 HTTP 动词是什么。能够用自己的意思定义一个新的动词(比如一次性获取或获取并删除)比重用 GET 更干净。但这样做也带来了自身的问题。理论上，您可以用自己的动词扩展 HTTP，但这通常是一件非常冒险的事情。任何试图在 REST API(比如 PATCH)中使用 GET、PUT、POST 和 DELETE 之外的其他 HTTP 动词的人都知道这有多困难。当您的客户端和服务器之间有代理和防火墙时，尤其如此。我已经尝试过几次了，我非常确定在我尝试实现一次性 GET 之前，我宁愿选择稍微偏离 GET 的方式。

其结果是，当你设计 RESTful webservices 时，你的域模型中的方法倾向于收敛或者至少包含 GET、PUT、POST 和 DELETE 的等价物。从领域驱动设计(DDD)的角度来看，这有点微不足道，因为你宁愿在你的设计中避免这样的外力。Leonhard Richardson 和 Sam Ruby 的书 [RESTful Web Services](http://shop.oreilly.com/product/9780596529260.do) 的第 228 页上的“资源之间的关系一节是一个很好的例子，说明了这会导致什么。

尽管如此，我还是很高兴 REST 最终使用了 HTTP 动词，尽管围绕 PUT 和 POST 出现了混乱。毕竟，HTTP 动词确实有一个定义明确且被广泛接受的含义，任何其他动词集合都可能会带来混乱。但更重要的是:由于 HTTP 动词的重用，以及一般的 HTTP 协议，只要 HTTP 运行良好，REST 就能运行良好。它确实很好地处理了我遇到的 99.99%的情况。