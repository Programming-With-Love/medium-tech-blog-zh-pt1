# 关于 REST 的五个不方便的真相:3 — HTTP 响应代码≦“REST 响应代码”

> 原文：<https://medium.com/compendium/five-inconvenient-truths-about-rest-3-http-response-codes-rest-response-codes-9d48e4e1f1e6?source=collection_archive---------3----------------------->

在我的上一篇文章中，我讨论了将 HTTP 动词作为 REST 动词重用的利弊。这样做的好处是，当你想学习 REST 的时候，你不必学习一套新的动词。缺点是 HTTP 动词并不总是符合 REST 服务的需求。HTTP 响应代码也是如此:重用它们使您不必学习一组新的响应代码，但是缺点是不匹配，您必须在 REST API 中进行补偿。

让我们从最常见的 HTTP 响应代码开始。当你用浏览器上网时，你最常遇到的响应代码可能是 [200 OK](https://httpstatuses.com/200) 。通常你看不到它，因为它表明一切正常(就像你加载这个页面时一样)，因此它被你的浏览器隐藏了。人们可能更熟悉的一个响应代码是 [404 Not Found](https://httpstatuses.com/404) ，当你试图打开一个不存在的页面时(例如，因为你打错了它的 URL)。在许多情况下，浏览器只是显示一个标准的消息，但是一些网站以创建一个带有一些艺术的特殊页面为荣，例如 [GitHub](https://github.com/) 的[页面没有找到](https://github.com/404)页面。

当你看到 HTTP 响应代码的[列表时，很容易变得不知所措。幸运的是，并不是所有的都经常使用，其中一个其实只是一个](https://en.wikipedia.org/wiki/List_of_HTTP_status_codes)[笑话](https://httpstatuses.com/418)。但是如果您仔细看看这个列表，您会注意到许多 HTTP 响应代码根本与 REST 无关。以 [417 Expectation Failed](https://httpstatuses.com/417) 为例:我很难想象一个 REST webservice 应该用那个响应代码主动响应的情况。

因此，REST webservices 显式使用的响应代码集相当短。我会说，前面提到的 200 OK 和 404 Not Found，以及 [401 Unauthorized](https://httpstatuses.com/401) 和 [403 Forbidden](https://httpstatuses.com/403) 将是最常见的。 [201 Created](https://httpstatuses.com/201) 应该在创建新资源时返回，但是初学者经常会犯错误，返回一个 200 OK。同样的情况经常发生在 [202 接受了](https://httpstatuses.com/202)，而 [204 没有内容](https://httpstatuses.com/204)的情况下。

当 REST 调用的有效载荷不符合 XSD 时，例如，由于语法问题，应该用 [400 错误请求](https://httpstatuses.com/400)响应拒绝该调用。通常，如果您选择了一个好的 REST webservices 框架或平台，这已经由基础设施解决了。但是如果有效载荷有语义问题呢？并不是对调用内容的所有约束都可以用 XSD 来表达，因此由 REST 接口中的 XML 验证来处理。

假设您有一个 REST 服务来创建一个 Foo 类型的新资源，在调用内容的某个地方，应该有一个对已经存在的 Bar 类型资源的引用。很明显，这个约束不能嵌入到 XSD 中，因为这样的话，每次创建或删除 Bar 资源时，它都必须被更新并传播到所有客户端。那么，当对 Bar 资源的引用存在且格式良好，但没有指向实际存在的 Bar 资源时，响应代码是什么呢？用 400 Bad 请求来响应是不正确的，因为消息的语法是好的。有人可能会认为 404 Not Found 可能是合适的，因为找不到 Bar 资源。然而，当找不到*请求的*资源，而不是有效载荷中引用的资源*之一时，应该使用 404 Not Found。一个 [500 内部服务器错误](https://httpstatuses.com/500)也是不正确的，因为我们不是在处理一个内部服务器错误——我们实际上是在防止一个错误在以后发生。*

我所知道的最好的解决方案是使用响应代码 [422 不可处理的实体](https://httpstatuses.com/422)，用一个错误消息解释问题的原因。维基百科对响应代码的解释是，当“请求格式良好，但由于语义错误而无法执行”时，应该使用响应代码，这很好地概括了我们的情况。我使用 422 不可处理实体的其他情况包括无法或不应该在 XSD 中编码的非法值导致的错误(例如，不正确的邮政编码)，选择或资源的无效组合(例如，试图与两次相同的人注册婚姻)，或任何其他没有意义的事情(例如，试图将一个男人注册为孩子的亲生母亲)。

一旦您开始使用 REST webservices，您将很快发现应用程序业务逻辑的许多异常部分将在您的 REST API 中解析为 422 不可处理的实体。这有点讽刺，因为您最常处理的 HTTP 响应代码之一甚至不是最初的 HTTP 响应代码集的一部分，而是后来由 HTTP 的扩展 [WebDAV](https://en.wikipedia.org/wiki/WebDAV) 添加的。但是更严重的是，您需要创建一个错误消息类型来解释到底哪里出错了。客户端必须检查该错误消息，以便决定下一步做什么。但是等一下，使用 HTTP 响应代码不是应该避免我们这样做吗，即创建我们自己的一组异常代码，并将它们作为响应的有效载荷发送给客户端？