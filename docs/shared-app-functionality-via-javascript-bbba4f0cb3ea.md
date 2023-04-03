# 通过 JavaScript 共享应用功能

> 原文：<https://medium.com/square-corner-blog/shared-app-functionality-via-javascript-bbba4f0cb3ea?source=collection_archive---------0----------------------->

## 我们如何创建一种灵活的方式来离线查看 Square 现金支付。

*由* [*阿兰·鲍林*](https://medium.com/u/bdc2de5d06c0?source=post_page-----bbba4f0cb3ea--------------------------------) *撰写。*

> 注意，我们已经行动了！如果您想继续了解 Square 的最新技术内容，请访问我们的新家[https://developer.squareup.com/blog](https://developer.squareup.com/blog)

在构建 [Square Cash app](https://cash.me/) 的 2.9 版本时，我们遇到了一个有趣的挑战。该应用程序正在从我们的服务器获取支付对象，需要显示给用户。这基本上包括为每笔付款选择一个模板并格式化数据。为简单起见，假设数据包括日期、金额和收件人姓名，并且需要生成一个可显示的字符串，如:“您在下午 3:00 向 Erin Hills 发送了 20 美元。”

我们首先考虑将逻辑和模板烘焙到应用程序中，但这将导致一个不灵活的系统，我们需要启动新版本的应用程序来适应变化。旧版本将无法正确显示支付，并且逻辑需要在每个支持的平台(iOS 和 Android)上重复。我们的工程速度将会大大降低。

很长一段时间，我们通过让服务器返回完整呈现的数据而不是原始数据来解决这个问题。这实现了灵活性，但代价是一些大的缺点。最重要的问题是应用程序需要一个网络连接，并且需要不断地请求显示支付的视图(因为显示的数据是时间敏感的)。这导致了过度的数据使用，滞后可能会导致过时。当我们致力于开发离线支付功能时，我们知道这种方法不再可行。

# 解决办法

我们找到的解决方案是在应用中使用 JavaScript 引擎，从原始对象中产生呈现的字符串。这非常有效。在 JavaScript 引擎中运行的 JavaScript 库实际上包含一系列模板和一个翻译函数，该函数使用原始对象，然后应用适当的模板，最后输出呈现的数据。

下面是一个从服务器获取的原始对象的简化示例，该对象从应用程序传递到 JavaScript 库:

```
{
  amount_cents: 2000,
  recipient_name: “Erin Hills”
  date : “2016-01-19T15:00:00Z”
}
```

JavaScript 库生成以下对象，并将其传递回应用程序:

```
{
  description: “You sent $20 to Erin Hills at 3:00 pm”
}
```

显示的数据对当前日期和时区敏感。如果用户一天后在没有网络连接的情况下打开应用程序，相同的 JavaScript 可能会产生:

```
{
  description: “You sent $20 to Erin Hills yesterday”
}
```

更新字符串或添加对新支付类型的支持可以通过更改 JavaScript 来完成。这里有一个简单的例子，说明我们何时增加了对非营利组织捐款的支持。原始数据:

```
{
  amount_cents: 2000,
  recipient_name: “Wikipedia”
  date: “2016-01-19T15:00:00Z”,
  type: “NON-PROFIT”
}
```

JavaScript 会将其转换成:

```
{
  description: “You donated $20 to Wikipedia yesterday. No goods or services were provided in exchange for this contribution.”
}
```

# 履行

iOS 7 通过其 [JavaScriptCore](https://developer.apple.com/library/mac/documentation/Carbon/Reference/WebKit_JavaScriptCore_Ref/) 框架引入了对无浏览器 JavaScript 引擎的支持。这就形成了一个简单的开箱即用的解决方案，只需很少的时间就可以启动并运行。

在 Android 中，标准 API 中没有内置框架。因此，我们构建了一个[开源](https://github.com/square/duktape-android) Java 绑定到 [Duktape](http://duktape.org/) ，一个可嵌入的 JavaScript 引擎。我们内置了对时区的支持，将 JavaScript 堆栈跟踪转换为 Java 堆栈跟踪(当 JavaScript 抛出时)，以及其他功能。

应用程序调用的 JavaScript 函数(实际上是本机代码和 JavaScript 之间的接口)需要使用原语对象。实际上，我们的库向应用程序公开的函数都有一个或多个输入字符串参数，并且都返回一个字符串。JavaScript 库负责将输入解析成 JSON 对象。这些应用程序负责将字符串化 JSON 的输出序列化为本机对象。

服务器将 JavaScript 库作为单个文件托管，应用程序定期检查更新版本。这个机制的实现使用 ETags 来重新下载 JavaScript 文件。

我们发现有用的一个实现细节是将原始对象作为不透明字符串而不是强类型对象传递给应用程序。这使得服务器可以完全控制原始对象和使用它的 JavaScript。新字段可以添加到原始对象，而应用程序不会知道或受到影响。

# 额外好处

JavaScript 的一个好处是它不局限于本地应用。同样的库也可以在 web 中使用。这可能是这种方法最好的方面之一:逻辑位于一个位置(服务器)，而不是跨 iOS、Android 和 web 复制。这有助于确保一致的体验，减少所需的测试数量，并提高我们的工程速度。

# 结论

共享 JavaScript 库的使用使得 Square 现金支付可以离线查看，同时允许演示文稿独立于新的应用程序版本进行更新。我们很高兴将这种方法扩展到应用程序的其他领域。感谢 Michael Druker、Dan Federman、Matt Precious、Alec Strong、Shawn Welch、Jesse Wilson 和 Shawn Zurbrigg 为此做出的贡献。

[](/@alanpaulin) [## 艾伦·波林-简介

### 关注 Alan Paulin - Profile 在 Medium 上的最新活动，查看他们的故事和推荐。

medium.com](/@alanpaulin)