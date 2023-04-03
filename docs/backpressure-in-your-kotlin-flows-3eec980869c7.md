# 科特林流中的背压

> 原文：<https://medium.com/google-developer-experts/backpressure-in-your-kotlin-flows-3eec980869c7?source=collection_archive---------0----------------------->

我应该什么时候开始担心？

![](img/8f232800c9f1751c16fc371e81f2e3c2.png)

Photo by [CHUTTERSNAP](https://unsplash.com/@chuttersnap?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

在普通的 Android 应用程序中，你并不经常需要担心背压。事件一般不会在我们弱小的移动设备上扩展到背压变得重要的程度。不是说不可能，只是在我的经历中不常见。然而，正如我在我的[上一篇文章](https://nickskelton.medium.com/converting-firebase-api-callbacks-to-kotlin-flows-3eea87429ee9)中发现的那样，在对`callbackFlow`进行实验时，不仅是流中事件的规模，还有事件本身的结构和顺序，以及我们对它们将如何到达所做的假设，都可能导致与背压相关的错误。

# 什么是背压？

你可能很熟悉的一个概念是“缓冲”。当试图将背压概念化时，这是一个很好的起点。每个开发人员都曾在某个时候与一个`File`交互过:创建了一个`ByteBuffer`和一个 `FileInputStream`，并设法将文件的字节流传输到一个`String` 或什么东西中。流非常类似，只是它是一个事件流——事件可以保存任何类型的数据，包括字节。就像文件一样，如果你有一个流，你需要一个缓冲区，以防数据产生的速度比它被消耗的速度快…这就是背压的定义:当数据产生的速度比它被消耗的速度快。

然而，当人们在反应流环境中谈论背压时，他们通常谈论的是“背压策略”:

*   缓冲区有多大？(大小= 0，1，64，100000)
*   当缓冲区已满时，我们如何处理新事件？

“不幸的是，没人知道什么是背压。你得自己去看。”

是时候下载代码来阅读本文的其余部分了:

[](https://github.com/shredderskelton/callbackflow) [## shredderskelton/call 回流

### 跟 callbackFlow 混在一起。创建一个帐户，为 shredderskelton/call reflow 的发展作出贡献…

github.com](https://github.com/shredderskelton/callbackflow) 

在[上一篇文章](https://nickskelton.medium.com/converting-firebase-api-callbacks-to-kotlin-flows-3eea87429ee9)中，我使用`callbackFlow`将一些 Firebase 回调转换为 Kotlin 流，并重点关注如何*为我们的流产生*数据。直到我开始*消耗*这些事件(在收集器上)的时候，我遇到了一个潜在的问题，徘徊到了背压。

注意:Firebase 实现并不重要——我下面探讨的所有内容都是针对 Kotlin 流的。

# 为什么不只是`collect`？

`collectLatest`、`collect`或`collectIndexed`……选哪个？

还有为什么`collect()`都是红色的？

当我第一次遇到这个选择时，我对自己说，`collectLatest`听起来差不多是对的，我是对的。巧合吗？

通过选择`collectLatest`，我并不知道这一点，但我刚刚做出了一个关于我打算如何处理背压的决定。事实上，如果我们想深入了解，我实际上是被 Flow 的设计者谨慎地引导做出这个决定的——他们设计(并限制)了这些集合选项，以这样的方式引导我选择 99%正确的选项。换句话说，他们做了可用性功课，发现大多数人只对收集“最新”事件感兴趣。

`collect()`是红色的，因为 JetBrains 强烈反对你使用它，但没有完全禁止它。这是为了给高级用户建议新用例的机会。来源注释/文档:

*此类限制可确保不违反上下文保留属性，并防止大多数与并发性、不一致的流调度程序和取消相关的开发人员错误*

乍一看，`collectIndex()`似乎只有在您对每个事件的索引感兴趣时才有用，然而，还有一个重要的原因让您可能想要选择`collectIndex()`——它处理背压的方式与`collectLatest()`不同。稍后我会解释。

现在，让我们看看`collectLatest()`会发生什么

# 丢弃的数据包

让我们模拟一些背压。

`delay(5000)`正在模拟一个收集器(也称为消费者或客户端，取决于您与谁交谈),它需要 5 秒钟来处理每个结果。回想一下，当我们*消耗的*比我们*生产的*慢时，就会产生背压。*延期是为了夸大萎靡的消费者，制造一个超级慢热的催收员。*

然而，由于我们选择了`collectLatest`，我们是安全的，因为当另一个值被“提供”(也就是产生)时，`collectLatest`例程将有效地停止它正在做的事情，并使用*最新的*结果重新开始。

这有一个危险的副作用，即`updateScreen()`通常在最终发行前不会被调用。如果 Firebase 发送进度更新的频率超过每 5 秒，那么我们的 UI 将冻结，直到最后一次提供后 5 秒。不太好。

因此`collectLatest`对流程的内容做了一个重要的假设:提供给流程的最终价值是最重要的，我们真的不想错过它。

然而，如果“放弃”的结果也很重要呢？如果每次提供东西的时候`updateScreen()`都要叫*真的很重要怎么办？*

让我们尝试使用不同的收集器，一个不会在新的报价到来时退出的收集器。让我们看看`collectIndexed`会发生什么，尽管我们对`index`并不感兴趣。

这个管用！我们对屏幕的所有更新都延迟了 5 秒，正如我们所料，*和*最后一个值通过了 ok。为什么？

首先，我们已经告诉流程，我们对所有的*产品感兴趣，而不仅仅是最新的*…这是一个好的开始。但是为什么没有东西坏掉呢？**

# *默认就可以了*

*一切正常，因为`callbackFlow`的默认缓冲区大小是 64。所以 Firebase 可以在`offer()`开始返回`false`之前进行 64 次提供(`false`表示缓冲区已满，提供失败)。*

*由于 Firebase 没有足够的“回调”来填充默认缓冲区，我决定模拟一次洪水。每次 Firebase 发送进度报告时，我都会给出 50 个相同的报价。终于有东西破了！*

*然而，这一次，已经在流动的产品仍然存在。当缓冲区已满时，丢失的是“最新”的项目——这是一个重要的区别。如果流程中的最后一个产品很重要，不能错过，该怎么办？*

*然后你需要更多地控制你的背压和你接触这个重要的小流量算子的时间:*

```
*.buffer()*
```

*这是所有与背压相关的东西的神奇操作符，试验起来非常有趣，因为它很容易上手和使用:*

```
*buffer(capacity: Int, onBufferOverflow: BufferOverflow)*
```

*当缓冲区溢出时，JetBrains 为我们提供了三个选项:*

```
*public enum class BufferOverflow {
 *  SUSPEND*,
 *  DROP_OLDEST*,
 *DROP_LATEST* }*
```

*`DROP_OLDEST`和`DROP_LATEST`非常简单，`SUSPEND`很有趣。对于我们的`FileInputStream`类比，这可能意味着“停止从硬盘读取”。这在某些情况下可能是好的(当流式传输视频剪辑时，一旦媒体播放器缓冲区已满，就停止从服务器请求帧)，但在其他情况下可能是坏的(当从麦克风录制音频时，如果停止从麦克风获取数据，就会丢失正在发生的任何声音，并且音频会有间隙)。*

# *分手建议*

*如果 Flow API 本身的结构不够地道，那么源代码文档在 Kotlin 库中确实是首屈一指的。他们真的付出了很多努力来使文档简洁和有用。如果你不确定一个操作员做什么——按住 CTRL 键并花 5 分钟阅读半页手册。时间花得值。*

*我希望这篇文章能帮助您为您的流量选择正确的背压策略，并理解背压为什么重要。*

*非常感谢恩里克·洛佩斯·马斯和 T2·弗洛里纳·芒特内斯库审阅本文。传奇。*

*在[推特](https://twitter.com/nshred)上给我留言——我张开双臂拥抱荣誉和胜利。*