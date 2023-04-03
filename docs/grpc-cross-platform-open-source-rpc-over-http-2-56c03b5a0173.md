# gRPC —跨平台开源 RPC over HTTP/2

> 原文：<https://medium.com/square-corner-blog/grpc-cross-platform-open-source-rpc-over-http-2-56c03b5a0173?source=collection_archive---------1----------------------->

## 在开源 gRPC 上与 Google 合作

*由* [撰写*曼尼克*](https://medium.com/u/cb858ba411a5?source=post_page-----56c03b5a0173--------------------------------) *。*

> 注意，我们已经行动了！如果您想继续了解 Square 的最新技术内容，请访问我们的新家[https://developer.squareup.com/blog](https://developer.squareup.com/blog)

几个月前，我们评估了开源我们专有的内部 RPC 库(基于协议缓冲区)。同时，我们还决定将这个框架转移到 [HTTP/2](https://http2.github.io/) 。幸运的是，在付出太多努力之前，我们了解到谷歌正在尝试开源类似的东西——而且进展很快。我们暂停了努力，向谷歌寻求帮助。

Google 把我们圈了进来，我们参与了 [gRPC](http://grpc.io/) ，这是一个开源( [BSD 许可的](https://github.com/grpc/grpc/blob/master/LICENSE))跨平台库，用于进行远程过程调用。(这对于微服务架构中的客户端-服务器通信以及服务器-服务器通信都很有用。)讨论设计、尝试早期实现以及合作开源 gRPC 是一个有趣的过程。

您可能会想，“另一个 RPC 库？JSON-over-REST 怎么了？”嗯，没什么——除了二进制协议在带宽和 CPU 效率、类型安全和内存占用方面有固有的优势。此外，在最近最终确定的 [HTTP/2](https://http2.github.io/) 规范的基础上，我们获得了双向流、多路复用连接、流量控制和报头压缩。

gRPC 也是跨平台的，支持 C、C++、Java、Go、Node.js、Python 和 Ruby，Objective-C、PHP 和 C#的库也在计划之中。

该项目完全在 GitHub 上的[上游主存储库的开放环境下运行。通信(](http://github.com/grpc) [IRC](http://webchat.freenode.net/?channels=grpc) 、 [Google Group](https://groups.google.com/forum/#!forum/grpc-io) )和[文档](https://github.com/grpc/grpc-common)都是公开的。请用“grpc”标记 StackOverflow 问题。

谷歌[也在博客上发布了这个版本](http://googledevelopers.blogspot.com/2015/02/introducing-grpc-new-open-source-http2.html)。

我们很想听听你对 gRPC 的看法。通过分叉和提交拉请求来投稿，与我们聊天，关注 [@grpcio](https://twitter.com/grpcio) 。

干杯
马尼克

[](/@maniksurtani) [## Manik Surtani - Profile

### SSL 和虚假的安全感我最近和一位同事谈到了他在网上预订酒店时遇到的一个问题…

medium.com](/@maniksurtani)