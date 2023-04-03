# 将反向代理从 Netty 3 升级到 4

> 原文：<https://medium.com/square-corner-blog/upgrading-a-reverse-proxy-from-netty-3-to-4-878ec407665a?source=collection_archive---------2----------------------->

## Tracon 是由 Netty 支持的反向 HTTP 代理。我们最近完成了对 Netty 4 的升级，希望分享我们的经验。

由克里斯·康罗伊和马特·达文波特撰写。

> 注意，我们已经行动了！如果您想继续了解 Square 的最新技术内容，请访问我们的新家[https://developer.squareup.com/blog](https://developer.squareup.com/blog)

# Tracon: Square 的反向代理

Tracon 是我们的反向 HTTP 代理，由 [Netty](http://netty.io/) 提供支持。几年前，当我们开始转向微服务架构时，我们意识到我们需要一个反向代理来协调 API 从我们遗留的 monolith 到我们快速扩展的微服务集的迁移。

我们选择在 Netty 的基础上构建 Tracon，是为了获得高效的性能以及进行安全和复杂定制的能力。我们还能够利用大量的共享 Java 代码与我们的堆栈的其余部分，以提供坚如磐石的服务发现，配置和生命周期管理，以及更多！

Tracon 是用 Netty 3 写的，已经生产了三年。在其生命周期中，代码库已经增长到 20，000 行代码和测试。很大程度上要感谢 Netty 库，这个代理应用程序的核心已经被证明是如此可靠，以至于我们已经将其应用扩展到了其他应用程序中。同一个库支持我们的内部认证公司代理。Tracon 与我们内部动态服务发现系统的集成将很快为 Square 的所有服务对服务通信提供动力。除了路由逻辑之外，我们还可以捕获大量关于流入数据中心的流量的统计数据。

# 为什么现在升级到 Netty 4？

Netty 4 是三年前发布的。与 Netty 3 相比，线程和内存模型已经进行了彻底的改进，以提高性能。也许更重要的是，它还为 HTTP/2 提供了一流的支持。虽然我们对迁移到这个库很感兴趣，但我们已经推迟了升级，因为这是一个引入了一些重大突破性变化的重大升级。

现在，Netty 4 已经存在了一段时间，Netty 3 也已经走到了生命的尽头，我们认为对这一关键基础设施进行彻底检查的时机已经成熟。我们希望允许我们的移动客户端使用 [HTTP/2](https://github.com/netty/netty/blob/4.1/codec-http2/src/main/java/io/netty/handler/codec/http2/Http2Codec.java) ，并且正在重组我们的 RPC 基础设施以使用 [gRPC](http://www.grpc.io/) ，这将要求我们的基础设施代理 HTTP/2。我们知道这将是一项历时数月的努力，而且在前进的道路上会有坎坷。现在升级已经完成，我们想分享一下我们遇到的一些问题以及我们是如何解决它们的。

# 遇到的问题

# 单线程通道:这应该很简单！

与 Netty 3 不同，在 Netty 4 中，出站事件与入站事件发生在同一个线程上。这允许我们通过删除确保线程安全的代码来简化一些出站处理程序。然而，由于这种变化，我们也遇到了意外的竞争情况。

我们的许多测试都是在 echo 服务器上运行的，我们断言客户机接收的正是它发送的内容。在我们的一个涉及分块消息的测试中，我们发现我们偶尔会收到除了一个块之外的所有块。丢失的部分从来不在消息的开头，但是它从中间到结尾是变化的。

在 Netty 3 中，所有与管道的交互都是线程安全的。但是，在 Netty 4 中，所有管道事件都必须发生在事件循环中。因此，源自事件循环之外的事件由 Netty 异步调度。

在 Tracon 中，我们将流量从入站服务器通道代理到单独的出站通道。因为我们将出站连接放在一起，所以出站通道不会绑定到入站事件循环。来自每个事件循环的事件导致这个代理试图同时对*写*。这段代码在 Netty 3 中是安全的，因为每个*写*调用都会在返回之前完成。在 Netty 4 中，我们必须更加小心地控制什么事件循环可以调用*写*来防止乱序写。

**当从 Netty 3 升级一个应用程序时，仔细审计任何可能从事件循环之外触发的事件代码:这些事件现在将被异步调度。**

# 什么时候一个渠道是真正连通的？

在 Netty 3 中， *SslHandler* “重新定义”一个 *channelConnected* 事件，在 TLS 握手而不是套接字上的 TCP 握手完成时进行门控。在 Netty 4 中，处理程序不会阻塞 *channelConnected* 事件，而是触发一个更细粒度的用户*事件:SSL handshakecompletionevent*。注意 Netty 4 将*通道连接的*替换为*通道活动的*。

对于大多数应用程序来说，这是一个无关紧要的变化，但是 Tracon 使用相互认证的 TLS 来验证它与之对话的服务的身份。当我们第一次升级时，我们发现在相互认证*通道活动*处理程序中缺少预期的 *SSLSession* 。**解决方法很简单:监听握手完成事件，而不是假设 TLS 设置在*通道活动*** 完成

```
@Override public void userEventTriggered(ChannelHandlerContext ctx, Object evt) throws
 if (evt.equals(SslHandshakeCompletionEvent.SUCCESS)) {
     Principal peerPrincipal = engine.getSession().getPeerPrincipal();
     // Validate the principal
     // ...
 }
 super.userEventTriggered(ctx, evt);}
```

# 回收的缓冲区泄漏 NIO 内存

除了正常的 JVM 监控之外，我们通过导出 JMX bean*Java . NIO:type = buffer pool，name=direct* 添加了对 NIO 分配的大小和数量的监控，因为我们希望能够了解新的池化分配器的直接内存使用情况并发出警报。

在一个集群中，我们能够使用这些数据观察到 NIO 内存泄漏。Netty 提供了一个泄漏检测框架来帮助捕获管理缓冲区引用计数中的错误。我们没有得到任何泄漏检测错误，因为这个泄漏实际上不是一个[引用计数](http://netty.io/wiki/reference-counted-objects.html)错误！

Netty 4 引入了一个线程本地回收器*作为通用对象池。默认情况下，回收程序最多可以保留 262k 个对象。*如果小于 64kb，ByteBufs* 默认情况下会被池化:这意味着每个缓冲区回收器最多有 17GB 的 NIO 内存。*

在正常情况下，很少会分配足够的 NIO 缓冲区。然而，如果没有足够的背压，一个慢速的阅读器就可能导致内存使用膨胀。即使在写入了慢速读取器的缓冲数据之后，回收器也不会使旧对象过期:属于该线程的 NIO 内存永远不会被释放出来供另一个线程使用。我们发现回收器完全耗尽了我们的 NIO 内存空间。

我们已经将这些问题通知了 Netty 项目，并且有几个即将到来的修复提供了 [saner 默认值](https://github.com/netty/netty/pull/5589)并限制了对象的增长:

*   [允许限制每个线程 WeakOrderQueue 实例的最大数量](https://github.com/netty/netty/pull/5592)
*   [在回收器中引入分配/汇集比率](https://github.com/netty/netty/pull/5594)
*   [端口 SendBufferPooled 来自 netty 3.10，如果使用非池化 ByteBufAllocator，则使用该端口。](https://github.com/netty/netty/pull/5642)

**我们鼓励 Netty 的所有用户根据可用内存、线程数量和应用程序配置来配置他们的回收器设置。**通过设置*-dio . netty . recycler . max capacity*可以配置每个回收器的对象数量，池的最大缓冲区大小由-*dio . netty . threadlocaldirdbuffersize*配置。通过将*-dio . netty . recycler . max capacity*设置为 0 来完全禁用回收器是安全的，对于我们的应用程序，我们没有观察到使用回收器有任何性能优势。

为了应对这个问题，我们做了另一个小但非常重要的改变:我们修改了我们的全局 *UncaughtExceptionHandler* 以在进程遇到错误时终止进程，因为一旦遇到 *OutOfMemoryError* 错误，我们就无法合理地恢复。这将有助于减轻未来任何潜在泄漏的影响。

```
class LoggingExceptionHandler implements Thread.UncaughtExceptionHandler { private static final Logger logger = Logger.getLogger(LoggingExceptionHandler.class); /** Registers this as the default handler. */
 static void registerAsDefault() {
   Thread.setDefaultUncaughtExceptionHandler(new LoggingExceptionHandler());
 } @Override public void uncaughtException(Thread t, Throwable e) {
   if (e instanceof Exception) {
     logger.error("Uncaught exception killed thread named '" + t.getName() + "'.", e);
   } else {
     logger.fatal("Uncaught error killed thread named '" + t.getName() + "'." + " Exiting now.", e);
     System.exit(1);
   }
 }
}
```

限制回收器修复了漏洞，但这也揭示了单个慢速读取器会消耗多少内存。这对于 Netty 4 来说并不新鲜，但是我们能够使用*channelwritalitchanged*事件轻松地添加背压。每当我们将两个通道绑定在一起时，我们只需添加这个处理程序，当通道解除链接时，我们只需移除它。

```
/*** Observe the writability of the given inbound pipeline and set the {@link ChannelOption#AUTO_READ}
* of the other channel to match. This allows our proxy to signal to the other side of a proxy
* connection that a channel has a slow consumer and therefore should stop reading from the
* other side of the proxy until that consumer is ready.
*/public class WritabilityHandler extends ChannelInboundHandlerAdapter { private final Channel otherChannel; public WritabilityHandler(Channel otherChannel) {
   this.otherChannel = otherChannel;
 } @Override public void channelWritabilityChanged(ChannelHandlerContext ctx) throws Exception {
   boolean writable = ctx.channel().isWritable();
   otherChannel.config().setOption(ChannelOption.AUTO_READ, writable);
   super.channelWritabilityChanged(ctx);
 }
}
```

在发送缓冲区填充到高水位线后，通道的可写性将变为不可写，并且在低于低水位线之前，它不会再次被标记为可写。默认情况下，高水位线为 64kb，低水位线为 32kb。根据您的流量模式，您可能需要调整这些值。

# 如果一个承诺被打破了，而且没有监听器，你刚才是把/dev/null 构建成一个服务吗？

在调试一些测试失败时，我们意识到一些写操作正在无声地失败。出站操作会将任何失败通知给它们的未来，但是如果每个写入失败都有共享的失败处理，您可以改为连接一个处理程序来覆盖所有写入。我们添加了一个简单的处理程序来记录任何失败的写操作:

```
@Singleton
@Sharable
public class PromiseFailureHandler extends ChannelOutboundHandlerAdapter { private final Logger logger = Logger.getLogger(PromiseFailureHandler.class); @Override public void write(ChannelHandlerContext ctx, Object msg, ChannelPromise promise)
     throws Exception {
   promise.addListener(future -> {
     if (!future.isSuccess()) {
       logger.info("Write on channel %s failed", promise.cause(), ctx.channel());
     }
   }); super.write(ctx, msg, promise);
 }
}
```

# HTTPCodec 更改

Netty 4 有一个改进的 HTTP 编解码器，它有一个更好的 API 来管理分块消息内容。我们能够删除一些自定义的块处理代码，但是在这个过程中我们也发现了一些惊喜！

在 Netty 4 中，每个 HTTP 消息都被转换成一个分块消息。即使对于零长度的消息也是如此。虽然从技术上讲，长度为 0 的分块消息是有效的，但这确实有点傻！我们安装了对象聚合器，将这些消息转换成非分块编码。Netty 只为入站管道提供了一个聚合器:我们为出站管道添加了一个自定义聚合器，并将为其他 Netty 用户提供这个聚合器。

新的编解码器模型有一些细微差别。值得注意的是，*lashttpcontent*也是一个 *HttpContent* 。这听起来很明显，但是如果你不小心的话，你可能会两次处理一条消息！此外，一个 *FullHttpResponse* 也是一个 *HttpResponse* 、一个 *HttpContent* 和一个*lashttpcontent*。我们发现，我们通常希望将它作为一个 *HttpResponse* 和一个*lastttpcontent*来处理，但是我们必须小心确保不会通过管道转发消息两次。

**不要这样做**

```
if (msg instanceof HttpResponse) {
  ...
}if (msg instanceof HttpContent) {
  ...
}if (msg instanceof LastHttpContent) {
  … // Duplicate handling! This was already handled above!
}
```

我们在一些测试代码中发现的另一个细微差别:*lashttpcontent*可能在接收方已经收到完整的响应之后触发，如果没有主体的话。在这种情况下，最后的内容是作为一个哨兵，但最后的字节已经出去了！

# 当飞机在空中时更换发动机

总的来说，我们迁移到 Netty 4 的更改涉及了 100 多个文件和 8k 多行代码。如此大的变化再加上新的线程和内存模型，必然会遇到一些问题。由于我们 100%的外部流量都流经该系统，我们需要一个流程来验证这些更改的安全性。

我们的大型单元和集成测试套件在验证初始实现方面非常有价值。

一旦我们在测试中建立了信心，我们就从“黑暗部署”开始，在这个部署中，我们在禁用状态下部署代理。虽然它没有占用任何流量，但我们能够通过 Netty 管道运行健康检查来检查下游服务的状态，从而测试大量的新代码。我们强烈推荐这种技术来安全地推出任何大的变更。

当我们慢慢地将新代码推广到产品中时，我们还依赖大量的指标来比较新代码的性能。一旦我们解决了所有的问题，我们发现使用*unpoedbytebufallocator*的 Netty 4 性能实际上与 Netty 3 相同。我们期待在不久的将来使用池式分配器来获得更好的性能。

# 谢谢

我们要感谢所有参与 Netty 项目的人。我们要特别感谢诺曼·莫勒/[@诺曼·莫勒](https://twitter.com/normanmaurer)的帮助和回应！

# 参考

*   Netty 4.0 中新的和值得注意的:[http://netty.io/wiki/new-and-noteworthy-in-4.0.html](http://netty.io/wiki/new-and-noteworthy-in-4.0.html)
*   Netty 最佳实践更快==更好:[http://Norman Maurer . me/presentations/2014-Facebook-eng-netty/slides . html](http://normanmaurer.me/presentations/2014-facebook-eng-netty/slides.html)
*   Netty in Action:[https://www . Amazon . com/Netty-Action-Norman-Maurer/DP/1617291471/qid = 1470353221](https://www.amazon.com/Netty-Action-Norman-Maurer/dp/1617291471/qid=1470353221)