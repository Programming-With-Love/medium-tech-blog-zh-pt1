# 用 w3af 对 REST API 进行安全性测试

> 原文：<https://medium.com/quick-code/security-testing-for-rest-api-with-w3af-2c43b452e457?source=collection_archive---------0----------------------->

现在越来越多的公司提供 web APIs 来访问他们的服务。他们通常遵循 [REST](https://en.wikipedia.org/wiki/Representational_state_transfer) 风格。这样一个 RESTful web 服务看起来像一个常规的 web 应用程序。它接受一个 HTTP 请求，执行一些魔法，然后用一个 HTTP 响应进行回复。主要区别之一是回复通常不包含要在 web 浏览器中呈现的 HTML。相反，回复通常包含某种格式(例如 JSON 或 XML)的数据，这种格式更容易被另一个应用程序处理。