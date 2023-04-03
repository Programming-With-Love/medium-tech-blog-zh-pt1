# OkHttp 3.5 中的 Web Sockets 现已发布！

> 原文：<https://medium.com/square-corner-blog/web-sockets-now-shipping-in-okhttp-3-5-463a9eec82d1?source=collection_archive---------0----------------------->

> 注意，我们已经行动了！如果您想继续了解 Square 的最新技术内容，请访问我们的新家[https://developer.squareup.com/blog](https://developer.squareup.com/blog)

与传统的 HTTP 请求/响应模型不同，web 套接字提供完全双向的消息流。这意味着客户机和服务器都可以在任何时候向对方发送任意数量的消息。在今天的 3.5 版本中，OkHttp 现在提供了对 web sockets 的本地支持！

通过向`newWebSocket()`方法传递请求以及服务器发送消息的监听器来连接 web 套接字。

```
OkHttpClient client = new OkHttpClient();Request request = //...
WebSocketListener listener = //...WebSocket ws = client.newWebSocket(request, listener);
```

分别通过调用`send(String)`或`send(ByteString)`将文本或二进制消息排队。因为 OkHttp 使用自己的线程来发送消息，所以你可以从任何线程(甚至是 Android 的主线程)调用`send`。

从服务器接收到的消息将作为`String`或`ByteString`传递给监听器的`onMessage`回调。监听器也有回调，通知您连接的生命周期。

我们很高兴终于能够在 OkHttp 中共享一个稳定的 web socket API。通过将以下内容添加到您的`build.gradle`，在您的应用中使用 3.5 版:

```
compile 'com.squareup.okhttp3:okhttp:3.5.0
```

此版本的完整变更日志可在[此处](https://github.com/square/okhttp/blob/master/CHANGELOG.md#version-350)获得。

精明的 OkHttp 用户在阅读这篇文章时可能会有点困惑，因为 web sockets 已经被稍微支持了一段时间。一个名为`okhttp-ws`的独立产品已经问世近两年，但它的 API 被认为不稳定。甚至在此之前，一些实现 web socket 协议的类已经存在于“内部”包中。

web sockets 和 OkHttp 的历史可以追溯到三年前尚未发布的 Android 版本 [PonyDebugger](https://github.com/square/PonyDebugger) ，这是 Chrome DevTools 协议的一个实现，用于检查和更改设备上运行的应用程序的调试版本。遗憾的是，有人抢先我们为 Android 开源了这个工具，所以我们的工具可能永远不会问世。

所有这些内部工作以及一些使用早期实现的公司的支持最终导致了一个发布版本。感谢这些年来贡献或归档 bug 的所有人。