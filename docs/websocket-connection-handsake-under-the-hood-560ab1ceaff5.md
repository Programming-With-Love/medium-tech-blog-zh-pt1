# 引擎盖下的 Websocket 连接手柄

> 原文：<https://medium.easyread.co/websocket-connection-handsake-under-the-hood-560ab1ceaff5?source=collection_archive---------3----------------------->

![](img/b3e93fef6e1cd1945d009e306906426d.png)

Photo by [claudiasoraya](https://unsplash.com/@claudiasoraya) on [Unsplash](https://unsplash.com/)

**WebSocket** 是一种计算机[通信协议](https://en.wikipedia.org/wiki/Communications_protocol)，通过单一 [TCP](https://en.wikipedia.org/wiki/Transmission_Control_Protocol) 连接提供[全双工](https://en.wikipedia.org/wiki/Full-duplex)通信通道。WebSocket 协议在 2011 年被 [IETF](https://en.wikipedia.org/wiki/Internet_Engineering_Task_Force) 标准化为 [RFC](https://en.wikipedia.org/wiki/Request_for_Comments) 6455， [Web IDL](https://en.wikipedia.org/wiki/Web_IDL) 中的 WebSocket [API](https://en.wikipedia.org/wiki/Application_programming_interface) 正在被 [W3C](https://en.wikipedia.org/wiki/World_Wide_Web_Consortium) 标准化。WebSocket 提供全双工通信。简单地说:在客户机和服务器之间有一个持久的连接，双方可以在任何时候开始发送数据。

我用 WebSocket 已经 4 年了。我知道 HTTP 是如何工作的，但不知道 WebSocket 是如何工作的。所以我决定深入调查一下。通过这篇文章，我将帮助您理解持久连接在 WebSocket 协议中是如何工作的。这很简单，也很容易理解。

# **概述**

Websocket 连接从客户端到服务器的 HTTP GET 请求开始。该请求带有一个特殊的头，告诉服务器它想要创建一个 WebSocket 连接。以下是标题规格:

```
GET **/chat** **HTTP**/1.1
Host: server.example.com
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Key: x3JJHMbDL1EzLkh9GBhXDw==
Sec-WebSocket-Version: 13
```

服务器响应:

```
**HTTP**/1.1 101 **Switching Protocols**
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Accept: HSmrc0sMlYUkAGmm5OPpG2HaGWk=
```

客户端必须发送一个包含 [base64](https://en.wikipedia.org/wiki/Base64) 编码的随机字节的`**Sec-WebSocket-Key**`头，服务器回复一个`**Sec-WebSocket-Accept**`头中关键字的[散列](https://en.wikipedia.org/wiki/Hash_function)。你感到困惑吗？别急，我会在实现部分多做解释。

## 履行

是时候让我们的手变脏了！我将使用 golang 作为编程语言。因此，请确保您已经在工作站中设置了 golang 环境。

*   **HTPP 服务器**

我将使用 net/HTTP 库作为 HTTP 服务器库。

初始化时，将使用我们的主机和端口选项创建一个`**HttpServer**`对象。

*   **连接升级**

你必须处理握手过程。创建初始 HTTP/1.1 连接后，您需要通过向标准请求添加`**Upgrade**`和`[**Connection**](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Connection)`头来请求升级。将 HTTP/1.1 会话升级到 WebSocket 连接时，需要在 HTTP/1.1 升级响应**中生成`**Sec-WebSocket-Accept**` 值。**

它通过获取`**Sec-WebSocket-Key**`的值并将其与`**"258EAFA5-E914-47DA-95CA-C5AB0DC85B11"**`(一个在[协议规范](https://tools.ietf.org/html/rfc6455#page-60)中定义的“神奇字符串”)连接来实现这一点。它接受这个连接，创建一个 SHA1 摘要，然后用 Base64 编码这个摘要。我们可以使用内置的`**crypto/sha1**`和`**encoding/base64**`库来做到这一点。

现在我们可以使用这个键，并向套接字写入我们期望的响应。这个响应包括状态代码`**"101 Switching Protocols"**`，表示服务器和客户端现在将通过 WebSocket 进行通信。这还包括客户端发送给我们的相同的`**Upgrade**`和`**Connection**`头，以及适当的`**Sec-WebSocket-Accept**`键和值。升级连接机制后，我们需要确保连接不会立即关闭。因此我们将创建一个事件循环。所有传入的消息/请求都将在事件循环中被接收。但我会在另一篇文章中解释。

好吧！我们来做个客户端测试吧！首先，您需要打开浏览器，然后打开 javascript 控制台并尝试这段代码。

```
**new WebSocket("ws://localhost:8080");**
```

在浏览器中打开您的网络部分，您会发现一个新的 WebSocket 连接。

万岁，到此为止🤗

我希望你喜欢它。请在下面留下评论。如果你面临任何问题，留下你的评论，我会帮助你😉。