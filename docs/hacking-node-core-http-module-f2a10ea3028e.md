# 黑客节点核心 HTTP 模块

> 原文：<https://medium.com/walmartglobaltech/hacking-node-core-http-module-f2a10ea3028e?source=collection_archive---------3----------------------->

O 我在 [@WalmartLabs](https://medium.com/u/c884135151a4?source=post_page-----f2a10ea3028e--------------------------------) 写的支持 NodeJS 的一个模块是我出于需要不得不做的。它对 NodeJS 核心中的`http`模块进行了黑客攻击和猴子修补。在这篇博客中，我将回顾一下为什么和如何做到这一点。

## 大写和小写 HTTP 头

我们有一个用 Java 实现的 SOA 微服务架构。它支持注册和自动发现、客户端验证和服务代理。该实现添加了一些自定义的 HTTP 头。

我们的 NodeJS 应用程序平台需要提供的一个东西是连接到我们内部微服务的标准方式。我很快遇到了一个问题；我测试的服务都返回了 HTTP 500 错误，表明我的请求缺少自定义头。

经过一些健全性检查和手工制作 cURL 请求的尝试后，我意识到 Java SOA 库只允许定制的大写 HTTP 头。不幸的是，Node core 自动将所有的 HTTP 头转换成小写，这很好，因为根据 RFC 2616,[HTTP 头不区分大小写。](https://www.w3.org/Protocols/rfc2616/rfc2616-sec4.html)

我尝试的第一件事是为拥有 SOA 的团队寻找联系人。一段时间后，很明显，修复并不简单，因为:

1.  SOA v1.0 版被部署到所有服务中，更新所有服务需要一些时间。
2.  SOA 团队正在开发 2.0 版，没有可用的周期来修复这个问题。

这让我处于非常困难的境地。我们的努力受阻了吗？

## 猴子补丁来拯救

JavaScript 的动态特性导致了一种被称为[猴子补丁](https://en.wikipedia.org/wiki/Monkey_patch)的现象。它允许您替换或更改不打算公开的代码。一般来说，这是不好的，但有时，这是通过关键拦截的唯一方式。

鉴于 HTTP 头的小写是在节点核心中硬编码的，很明显 monkey patch 是我唯一的选择。所以我开始研究核心的`http`模块。

当时，NodeJS 正准备发布 4.2.4。没有太多的装饰，我很容易地找到了位置[传出的 HTTP 头被转换成小写字母的位置](https://github.com/nodejs/node/blob/d3a40c51c5770b4def9a6033df0fa46c6f9f0cc7/lib/_http_outgoing.js#L369)。

Monkey 修补代码也很简单。我只是复制了`setHeader`的方法，打了补丁，替换了原来的版本:

```
var http = require("http");http.OutgoingMessage.prototype.setHeader = function(name, value) {
  // original code etc var key;
  if (!name.startsWith("FOO_")) {
    key = name.toLowerCase();
  } else {
    key = name;
  } // original code that updates this._headers
};
```

这就成功了。NodeJS 中任何以`FOO_`开头的 HTTP 头的大写字母。当然，我们实际的内部头使用另一个前缀签名。

## 测试

单元测试这有点棘手。节点核心还将所有传入的 HTTP 头转换为小写。因此，测试用类似的猴子补丁`http` `IncomingMessage`来捕获和验证原始形式的 HTTP 报头。

实际的测试使用 [mitm](https://www.npmjs.com/package/mitm) 来伪造一个 HTTP 服务器，然后向它发出一个请求，请求中使用了一些大写的自定义 HTTP 头。然后，服务器的请求处理程序验证它们没有小写。

## 节点 8.3.0

NodeJS 8.3.0 的发布带来了全新的 v8 发动机涡轮风扇，优化效果更好。我立即测试了我们应用的 React `renderToString`性能。结果是我们的一个生产应用的渲染时间持续减少了 60%。这是一个巨大的收获。所以我们立即着手用 Node 8.3.0 运行回归。

然而，Node 8 对`http`模块有一些重大改变。其中之一是`OutgoingMessage`对象现在隐藏了`_headers`字段。不是`this._headers`，而是`this[outHeadersKey]`，其中`outHeadersKey`是这里创建的一个`Symbol` [。更重要的是，这是一个用户代码无法访问的内部模块。因为一个](https://github.com/nodejs/node/blob/05a7fc5b5d5c703d77f01bf77809fb404d249c71/lib/internal/http.js#L24)[符号总是唯一的](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Symbol)并且我没有办法获得对内部`outHeadersKey`符号的引用，所以我不能再用 monkey 补丁修改输出头了。

我花了一些时间查看做出这些更改的[拉请求](https://github.com/nodejs/node/pull/10941)，以理解它们背后的基本原理。我还查看了官方为用户代码访问 HTTP 头添加的方法。对于改变硬编码的小写行为，我似乎无能为力。

意识到这一点后，我举了一下手。我的猴子补丁就这样结束了吗？我们的 SOA v2.0 已经发布，但我不确定它的采用情况。这是否意味着使用节点 8 在一段时间内是不可行的？如果我们不能利用所有的性能提升，那将是一个巨大的失败。

## 符号 API

在没有明确选择的情况下，我开始研究新的`Symbol` [API 文档。](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Symbol)

最初除了`[Symbol.for](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Symbol/for)` [方法](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Symbol/for)之外，没有什么突出的东西。我认为这是我获得核心的`outHeadersKey`符号参考的关键，所以我甚至没有阅读文档，并立即尝试它。嗯，5 分钟后没有运气，我回去真的读了文件。我找到了原因:

> 与`Symbol()`相反，`Symbol.for()`函数创建一个在全局符号注册表中可用的符号。

啊，是*全局符号注册表列表*，对！此时我以为游戏结束了，但我继续浏览`Symbol`上的文档。

然后我注意到了`[Object.getOwnPropertySymbols](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/getOwnPropertySymbols)` [API](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/getOwnPropertySymbols) 。不确定这是否可行，我去试了一下。

长话短说，它成功了，下面是我如何设法进入内部的`outHeadersKey`符号。

```
const http = require("http");
const assert = require("assert");function hackFinity() {
  const x = new http.OutgoingMessage();
  const s = Object.getOwnPropertySymbols(x);
  assert(typeof s[0] === "symbol" &&
    s[0].toString() === "Symbol(outHeadersKey)"); return s[0];
}const outHeadersKey = hackFinity();
```

咻，monkey 修补了业务，NodeJS 中出现了大写的 HTTP 头。

## mitm

因为 mitm 在核心层攻击 Node，所以它在 Node 8 中停止工作。毫不奇怪，单元测试停止工作也是因为它们依赖于 mitm。所以我不得不修改测试，在本地主机上启动一个真正的 HTTP 服务器，监听一个随机端口，然后向它发送带有自定义 HTTP 头的请求以进行验证。

## 结论

Monkey patch 不是一个好主意，但它是我发现的使用 JS 的好处之一，它让我在一些情况下摆脱了困境。只有在绝对必要时，才应尽量少用。Node 8 的小小恐慌为我敲响了警钟，我意识到我应该与 SOA 团队一起跟进，以确保有修复该问题的计划。就目前而言，我们仍然在用猴子补丁来传递这个拦截器。到目前为止，我还没有注意到任何负面影响，但这是一件需要进一步研究的事情。