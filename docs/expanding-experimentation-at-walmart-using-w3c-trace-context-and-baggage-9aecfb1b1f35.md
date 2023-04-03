# 使用 W3C 跟踪上下文和行李在沃尔玛扩展实验

> 原文：<https://medium.com/walmartglobaltech/expanding-experimentation-at-walmart-using-w3c-trace-context-and-baggage-9aecfb1b1f35?source=collection_archive---------0----------------------->

![](img/0a2381f3eab212843ad9d2c32b65bf77.png)

在沃尔玛于 2016 年收购的 Jet.com，我们有一个内部构建的 A/B 测试解决方案 Phaser，它利用了所有服务都需要传播的遥测头。Phaser 在头部注入了实验元数据，因此所有服务都可以免费进行开箱即用的实验。

在沃尔玛，A/B 测试系统 Expo 目前没有享受到这种奢侈，因此主要提供前端服务。为了将实验带给沃尔玛更广泛的服务受众，我们必须像 Phaser 一样将 Expo 集成到分布式跟踪系统中。

我们没有集成到沃尔玛现有的跟踪实现中，而是与沃尔玛遥测团队合作，实现了一个基于 W3C 跟踪上下文和行李规格的跟踪实现；其解决了对传播跟踪和相关信息的标准化方法的需求。

在本帖中，你将了解到跟踪环境、包袱，以及我们如何利用它们来推动沃尔玛的实验。

# **分布式追踪入门**

在介绍规格之前，我想用一个类比来简要概述一下分布式跟踪——点对点，你可以在谷物盒背面或儿童活动手册中找到的编号黑点网络，你可以用铅笔进行跟踪。黑点是您的微服务架构中的服务，铅笔痕迹是自始至终唯一的请求。分布式跟踪让您清楚地了解请求流中涉及的所有服务，并有助于将识别故障和瓶颈所需的信息联系在一起。

![](img/43790877cff27b34426a2b09498e0038.png)

Dot-to-Dots worksheet of a Lion cub via [https://www.allkidsnetwork.com/](https://www.allkidsnetwork.com/)

跟踪是通过在跟踪头内部传递数据来实现的，这些数据后来被拼凑在一起，以帮助创建特定请求所采用的路径的可视化。传统上，这种头是由跟踪供应商设计的，导致部署多个跟踪供应商的系统缺乏互操作性。这是跟踪上下文和行李地址的问题之一。

# 简化的跟踪上下文

**用途:**跟踪上下文是两个头部的提议，当请求从一个服务传递到另一个服务时，这两个头部被更新和传播。报头用于分析请求通过系统的路径。

**它由什么组成:** `traceparent`和`tracestate`:

1.  `traceparent` ( *必选*)

标题是跟踪的核心和灵魂。它包含四条信息:*版本*、*跟踪 id* 、*父跨度 id* 和*标志*。跟踪 id 和父 span id 对于帮助识别上述瓶颈和故障非常重要。下面是一个关于`traceparent`的例子:

```
traceparent: 00-0af7651916cd43dd8448eb211c80319c-b7ad6b7169203331-01**where**:
version = 00
trace ID = 0af7651916cd43dd8448eb211c80319c
parent span ID = b7ad6b7169203331
flags = 01
```

该示例显示了接收服务所看到的`traceparent`头的一个实例。让我们称之为服务 B。然后，服务 B 向服务 C 发出请求，通过使用自己的 span id 作为新的父 span ID 来更新跟踪上下文规范之后的`traceparent`头，并保持 trace-id 不变。服务 C 接收的报头可以是:

```
traceparent: 00-0af7651916cd43dd8448eb211c80319c-149ad35ecefe7f0c-01**where:**
version = 00
trace ID = 0af7651916cd43dd8448eb211c80319c
parent span ID = **149ad35ecefe7f0c <- only change**
flags = 01
```

请注意，跟踪 ID 保持不变，只有父 span ID 发生了变化。跟踪 ID 标识整个请求，父 span ID 标识服务 B 中发出请求的特定代码。

2.`tracestate` ( *可选*)

`tracestate` 表头用于支持多个追踪系统的系统(即一个系统使用罗约，另一个系统使用刚果)。`tracestate`是他们传递平台特定信息的地方，允许这些系统将`traceparent`头与这些平台收集的数据相关联。下面是标题的一个示例:

```
tracestate: rojo=00f067aa0ba902b7,congo=t61rcWkgMzE
```

罗约和刚果都指定了他们希望附加到追踪中的信息。这条信息在他们各自的系统中使用。

**Trace Context 如何帮助推进实验:**这两个头文件本身并不支持实验——这就是行李规范的由来——但它们有助于推广标准的跟踪方法，从而更容易在整个技术组织中推广分布式跟踪。

采用跟踪上下文的另一个理由是 OpenTelemetry 社区推动了越来越多的跟踪上下文开源 SDK 实现。例子包括对流行语言的支持，如 [Java](https://github.com/open-telemetry/opentelemetry-java) 、 [Javascript](https://github.com/open-telemetry/opentelemetry-js) 、 [Python](https://github.com/open-telemetry/opentelemetry-python) 和 [Go](https://github.com/open-telemetry/opentelemetry-go) 。在沃尔玛，我们已经建立了自己的实现，将使 W3C TraceContext 兼容现有用户，但欢迎开发团队使用 W3C TraceContext 兼容的成熟社区构建库。

# 行李简化

**用途:**行李是一个单一标题的建议——行李——可用于向请求添加用户定义的属性。以下是规范中的一些用例示例:

1.  如果您的所有数据都需要发送到单个节点，您可以传播一个属性来表明:`baggage: serverNode=DF:28`
2.  如果在进行交易时需要记录原始用户 ID，可以任意深入跟踪:`baggage: userId=alice`
3.  如果您的非生产请求与生产请求流经相同的服务:`baggage: isProduction=false`

在沃尔玛，我们使用行李头来宣传实验单元。我们的标头可能会以下列方式使用:

`baggage: someID=someUUID`

这将支持沃尔玛所有服务的实验。

**它由什么组成:** `baggage`

`baggage`头遵循与浏览器中的 [Set-Cookie](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Set-Cookie) 相同的格式，一个逗号分隔的带有可选属性的键值对列表。

**行李如何帮助实验向前发展:**

在介绍中，我提到了如何在 Jet 使用 Phaser 进行实验。开箱即用的实验是成功的实验文化的关键，将我们的数据传播给所有服务是实现这一目标的良好开端。

沃尔玛的跟踪上下文和行李标题将是利用 Envoy 的系统的一部分。考虑到这一点，Expo 还将实现一个 Envoy Web Assembly 过滤器来解析`baggage`头 ID，然后将特定的请求分配给一组实验。这种方法将使我们不再在网络边缘分配用户，因此我们不再需要担心我们的头超过沃尔玛的指定阈值，因为分配将发生在服务级别。

# 实施行李限制

行李规范规定了以下限制:

*   名称-值对的最大数量:`180`。
*   每个名称-值对的字节数:`4096`。
*   所有名称-值对的最大总长度:`8192`。

然而，该规范并没有定义如何处理修改的策略，而是致力于定义非规范性的建议。这可能会导致应用程序开发人员在标题已满的情况下盲目丢弃密钥。给定我们的用例，一个随机化单元存在于传播这个头的所有服务中，重要的是我们预先定义那些指导方针。一些需要考虑的问题:

*   键列表应该排序吗？
*   密钥应该是可修改的吗？
*   特定的键可以删除吗？
*   如果列表已满，是否应该删除旧的键(假设列表是有序的)？还是应该不能添加新的密钥？

因为规范把这个留给了它的用户，我们正致力于推广一套指南来限制对某些键的修改。

# 结论

支持所有服务的实验是 Expo 团队的一个重要里程碑，随着沃尔玛采用 Trace Context 和行李，这一目标更近了一步。使跟踪上下文和包袱不同的是，由于它是 W3C 规范，并且有开源库支持，它变得更容易在整个组织中销售。这对于像沃尔玛这样的大公司来说很重要，在沃尔玛，向现代技术的转变可能比小公司需要更长的时间。实验在沃尔玛很重要，追踪背景和行李将在沃尔玛的持续发展中发挥至关重要的作用。