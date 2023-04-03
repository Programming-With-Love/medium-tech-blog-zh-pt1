# 利用受限队列加快应用恢复

> 原文：<https://medium.com/square-corner-blog/faster-app-recovery-with-bounded-queues-d546820a0abc?source=collection_archive---------1----------------------->

> 注意，我们已经行动了！如果您想继续了解 Square 的最新技术内容，请访问我们的新家[https://developer.squareup.com/blog](https://developer.squareup.com/blog)

我们过去遇到的一个问题是，当一个 Ruby 应用程序由于下游问题而变得没有响应时。问题解决后，应用程序继续无响应或处理请求非常慢。虽然重启会立即清除，但我们恢复应用程序所需的手动步骤越少越好。

因为在产品上测试是不好的，我们需要一种简单的方法在本地复制它。

# 分身术

在 Square，我们的 Ruby 应用使用 [NGINX](https://www.nginx.com/) 和 [Puma](https://github.com/puma/puma) 来服务请求。我们写出一个简单的[服务器端测试用例](https://gist.github.com/zanker/e0ca717bcc9cd11859872c2c73505758)，我们可以通过使用`nginx -v ./nginx.conf`和`puma -C puma.rb`来运行它。然后我们设置客户端测试用例:

现在我们有一个测试端点，它在响应前休眠 5 秒钟，基准测试发出 10 个请求。使用我们的初始配置，基准看起来像:

```
Starting…
[0] 200 - 4.13 seconds elapsed
[1] 200 - 9.13 seconds elapsed
[2] 200 - 14.13 seconds elapsed
[3] 200 - 19.13 seconds elapsed
[4] 200 - 24.14 seconds elapsed
[5] 200 - 29.14 seconds elapsed
[6] 504 - 29.70 seconds elapsed
[7] 504 - 29.80 seconds elapsed
[8] 504 - 29.90 seconds elapsed
[9] 504 - 30 seconds elapsed
Took 30.0102 total, avg of 21.9242 in thread time, max 30, min 4.13
```

请求#0 到#5 返回了`HTTP 200 Success`，请求#6 到#9 返回了`HTTP 504 Gateway Timeout`。这是意料之中的，因为我们的 NGINX 测试用例已经设置了`proxy_read_timeout 30s`，并且 Puma 被配置为一次处理一个请求，处理前 6 个请求大约需要 30 秒，NGINX 中剩余的 4 个请求超时。

比较 Puma 日志，我们看到 Puma 看到了 10 个请求，它们都返回 HTTP 200，NGINX 看到了与客户端相同的请求，6 个请求使用 HTTP 200，4 个请求使用 HTTP 504。

这是一个有趣的陷阱。客户端花了 30 秒等待响应，但是服务器花了 50 秒来处理它。事情是这样的:

```
00:00 — [A] Client sends request A to Server
00:01 — [A] NGINX buffers the HTTP request into memory and sends it to Puma
00:01 — [A] Puma accepts the connection, reads the request into memory and queues it
00:03 — [B] Client times out, sends request B to Server
00:04 — [B] NGINX buffers the HTTP request into memory and sends it to Puma
00:04 — [B] Puma accepts the connection, reads the request into memory and queues it
00:05 — [A] Puma starts processing the request, and responds with it to NGINX
00:06 — [A] NGINX throws the response away since the client closed the connection for A
00:06 — [B] Puma starts processing the request, responds with it to NGINX
00:07 — [B] Client receives the response for B
```

如果客户端有自动重试功能就更好了。如果客户端在端点`/api/v1/update-user`上超时 500 毫秒，而该端点现在需要 1000 毫秒，则客户端每花费 500 毫秒，服务器就必须花费 1000 毫秒的处理时间。如果客户端重试 3 次，则客户端每花费 1，500 毫秒，服务器就会花费 3，000 毫秒。

尤其是在微服务架构中，一个端点可以调用另一个服务，后者调用另一个服务，如此类推。我们很快就给自己下了药，由于请求积压，导致停机比原来更严重。

理想情况下，我们的应用程序被配置为在过载时拒绝请求。它防止了上述级联故障的情况，并意味着应用程序可以恢复，而不必重新启动并清除所有长时间运行的请求。

在这种情况下，我们有一些杠杆可以调整。我们从最明显的开始，那就是彪马。

# 在 Puma 中限制请求

深入研究 Puma 代码，我们发现默认情况下，Puma 有一个无限制的队列，接受来自套接字的连接，然后将其排队以在 server.rb 中进行处理:

仔细检查 Puma 中的 thread_pool.rb，我们发现:

变量`@todo`是一个无限数组，它将 HTTP 请求存储在内存中，直到有线程可以处理为止。如果我们的应用程序跟不上，我们可能会有一个包含数百/数千个请求的队列，这些请求很久以前就超时了。该应用程序最终可能会赶上，但如果它正在处理的 95%的请求已经超时，我们不想等待 10 分钟才能恢复。

看看 server.rb 的代码，`pool.wait_until_not_full unless queue_requests`选项看起来很有前途。事实证明，这正是我们想要的选择！我们可以让 Puma 只接受有线程可用的连接。

注意，2017 年 6 月初发布的 Puma 3.9.0，现在只有*的*接受可以立即处理的连接。这个时间与我们的挖掘无关，是一个愉快的巧合。

在所有这些之后，我们将`queue_requests false`添加到我们的 Puma 配置中，并重新运行测试……得到完全相同的结果。Puma 处理了 10 个请求，基准测试中有 6 个成功，4 个失败。

根据以前使用套接字的经验，我们知道 TCP 套接字最终会使用 syscall [listen](https://linux.die.net/man/2/listen) (在 Linux 中)。查看文档，我们看到 backlog 参数“定义了 sockfd 的挂起连接队列可以增长到的最大长度”。

# 一路向下排队

TCP 的核心部分之一是缓冲。当客户端发送 HTTP 请求时，它们不会先连接，等待服务器接受，然后发送 HTTP 请求。它们试图一次发送所有内容，部分请求将存放在客户端和服务器之间的缓冲区中，直到服务器接受连接或超时。

这对性能有好处，但当我们需要可预测的请求队列时就不是这样了。在这一点上，我们向另一位 Square 工程师 Evan Miller 询问了 backlog 和 queues 的语义，从而了解到 Linux 有一个握手后套接字的 backlog(我们正在查看的 backlog ),然后还有一个握手前套接字。这涉及到 TCP 如何工作的语义，我不会在这里介绍，但是[维基百科](https://en.wikipedia.org/wiki/Transmission_Control_Protocol#Connection_establishment)有一篇文章解释了握手过程。

因为我们将 NGINX 放在 Puma 的前面，所以我们实际上有四个额外的队列:一个在 Puma 中是握手前和握手后，另一个在 NGINX 中。查看我们的服务器配置和文档，我们发现握手后队列的最大积压是 128。

我们将 Puma 配置为使用两倍于单个实例所能处理的积压。例如，如果我们的一个较小的应用程序可以同时处理 10 个请求，我们将 backlog 设置为 20。考虑到有多个 TCP 队列，这并不完美，但它有助于缩小请求排队的范围。

我们已经为 Puma 这边做了力所能及的事情，但是还是没有看 NGINX。

# 在 NGINX 中限制请求

在与其他工程师聊天后，他们提到了 [max_conns](http://nginx.org/en/docs/http/ngx_http_upstream_module.html#server) ，这是一个 NGINX 选项，在 1.11.5 中可用(付费版本更早一些)。

文档看起来很有希望，“限制代理服务器的最大同时活动连接数”，尽管它有一些进一步的警告，注意到由于保持活动连接，它可能会超过限制。

我们使用了与 backlog 相同的默认设置:每个实例可以同时处理两倍的请求。对于我们的测试，服务器一次处理一个请求，我们设置`max_conns=3`。重新运行测试，我们看到:

```
Starting…
[0] 200 - 4.13
[1] 200 - 9.14
[2] 200 - 14.14
[3] 502 - 0.01
[4] 502 - 0
[5] 502 - 0
[6] 502 - 0
[7] 502 - 0
[8] 502 - 0
[9] 502 - 0
Took 14.14 total, avg of 2.74 in thread time, max 14.14, min 0
```

Puma 日志只显示了 3 个请求，nginx 拒绝了另外 7 个请求，因为我们超过了最大连接数。

# 摘要

队列是有趣的，试图确保客户端和服务器之间的一切是复杂的。NGINX `max_conns`选项对于大多数情况来说已经足够好了，并且有一个额外的优势，就是在请求到达 Ruby 应用程序之前拒绝请求。TCP backlog 和 nginx 队列更改是额外的一层保护，减少了长期请求隐藏在 NGINX 和 Puma 之间的队列中的机会。

随着这些变化，当应用程序由于下游问题而变慢时，我们预计会看到更多确定性的故障模式。他们不会排队等待超出应用程序实际处理能力的请求，而是会更快地对错误做出响应。

这些方法并不完美，但是单个宿主变得不健康的速度越快，它就可以越快地退出循环，并有机会赶上并再次变得健康。