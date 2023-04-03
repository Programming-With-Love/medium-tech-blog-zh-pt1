# 什么是 WebAssembly？

> 原文：<https://medium.com/javascript-scene/what-is-webassembly-the-dawn-of-a-new-era-61256ec5a8f6?source=collection_archive---------0----------------------->

## 新时代的开始

网络平台的未来看起来比以往更加光明。

> 昨天， [**Brendan Eich 在 web 开发社区投下了一颗炸弹**](https://brendaneich.com/2015/06/from-asm-js-to-webassembly/):web 平台正在获得一种新的低级二进制编译格式，作为编译器目标，它将比 JavaScript 做得更好。

谷歌、微软、Mozilla 和其他一些人已经秘密地在一个新的 [W3C WebAssembly 社区组](https://www.w3.org/community/webassembly/)中辛勤工作，他们所做的事情可不是小事。

*更多深度请看后续文章，* [*《我们为什么需要 web assembly:Brendan Eich 访谈》*](/javascript-scene/why-we-need-webassembly-an-interview-with-brendan-eich-7fb2a60b0723) *。*

# **WebAssembly 是:**

*   **对 JavaScript 的改进:**在 wasm 中实现您的性能关键内容，并像标准 JavaScript 模块一样导入它。
*   **一种新的语言:** WebAssembly 代码定义了一个以**二进制格式**表示的 AST(抽象语法树)。你可以用文本格式**创作和调试**以便阅读。
*   **浏览器改进:** **浏览器将理解二进制格式**，这意味着我们将能够编译比我们今天使用的文本 JavaScript 更小的二进制包。更小的有效载荷意味着更快的交付。根据编译时优化的机会，WebAssembly 捆绑包也可能比 JavaScript 运行得更快！
*   **一个编译目标:**一种让其他语言获得跨整个 web 平台栈的一流二进制支持的方式。

# 这对 JavaScript 意味着什么？

在回答这个问题之前，我们先回顾一下。让我带你回到过去…回到…回到…回到 React 之前，Angular 之前，Backbone 之前，jQuery 之前…

啊，我们到了。

> 网络是散布在公告栏系统上的超文本文档，还没有互联。第一台网络服务器正在欧洲粒子物理研究所的下一个工作站上一起被黑客攻击…

![](img/d08516ec9b4d2b670f2b970dfd337295.png)

NeXT Computer used by [Tim Berners-Lee](https://en.wikipedia.org/wiki/Tim_Berners-Lee) at [CERN](https://en.wikipedia.org/wiki/CERN)

那是 1991 年，我的头发还没有变白。我正在拼凑我的第一万个文字冒险游戏*(类似的东西，我不算)。*

我为此做了一个特殊的语言选择。我厌倦了用 BASIC 和 Pascal 工作。我想用 C，但我仍在为我的第一个*Borland Turbo c++****盒装套装*** (它们实际上是装在装有手册和安装盘的盒子里)攒钱。我甚至还没有涡轮汇编程序。

我用汇编语言编写，并用 DOS *`debug`* 命令行工具“编译”成可执行文件。如果这听起来很疯狂，相信我，确实如此。我敢打赌，即使是那些用过 DOS 的人可能也没有意识到，你可以用 debug 来*汇编指令、*以及*反汇编*(逆向工程)现有代码。

听起来很酷？我讨厌它。我迫不及待地想得到 Borland Turbo C++，这样我就可以像人一样写代码了。最终，我得到了它作为礼物。得分！

但是**关于 Borland Turbo C++我最喜欢的事情**之一是**它与 Borland Turbo 汇编程序**捆绑在一起。什么？！当你的工具箱里有一个像 C++这样的高级面向对象语言时，你为什么还要用汇编语言写代码呢？

**有时候你想接近裸露的金属，**或者在不融化大脑的情况下尽可能接近它。我有没有提到在接触 C++之前我也写了很多机器语言？

> 我失去了理智。

![](img/1fe58373832783b1efc5e158f88a0f2f.png)

Losing My Mind — Mark Auer (CC BY-NC 2.0)

当你直接用汇编语言编写时，要完成真正的工作要困难得多。那么**我们为什么需要这个 WebAssembly 呢？**

**我们需要 WebAssembly** ,因为尽管 JavaScript 很灵活，但用 JavaScript 表达我们想要表达的许多东西还是太难了，我们需要使之变得简单的功能可能会给已经让许多用户困惑的语言增加复杂性。

WebAssembly 让我们可以访问一组低级的构建模块，我们可以用它们来构建你能想象到的任何东西。

*这和 JavaScript 有什么不同？*关键词是**低级**。它定义了原语，包括[一系列类型](https://github.com/WebAssembly/design/blob/master/AstSemantics.md#local-and-memory-types)和对这些类型的操作、它们的文字形式、控制流、调用、堆等等…

这些是非常简单的原语。没什么特别的。**没有复杂的对象系统**(原型或其他)。**没有内置的自动垃圾收集器**跟在你身边，定期停下来清理你的垃圾。

# WebAssembly 到底是什么？

WebAssembly 定义了一个[抽象语法树](https://github.com/WebAssembly/design/blob/master/AstSemantics.md#abstract-syntax-tree-semantics) (AST)，它以 [**二进制格式**](https://github.com/WebAssembly/design/blob/master/BinaryEncoding.md) 存储。二进制很棒，因为这意味着我们可以创建[更小的应用捆绑包](https://github.com/WebAssembly/design/blob/master/BinaryEncoding.md#why-a-binary-encoding-instead-of-a-text-only-representation)。您可能想知道我们将如何调试二进制语言格式。

幸运的是，当我们在调试浏览器中不可避免会出现的调试器时，AST 将以(适度)友好的 [**文本格式**](https://github.com/WebAssembly/design/blob/master/TextFormat.md) 表示。我很想给你看一些例子，但是现在有点缺乏。它可能比手写的 JavaScript 可读性差一点，但是和编译的 asm.js 一样可读。也许可读性更强。我们走着瞧。

# WebAssembly 有什么用途？

除此之外，表达像线程和 **SIMD 这样的东西将变得很容易——这是一个有趣的词，意味着你可以将多个数据块一个接一个地排列起来，并调用一条指令同时对它们进行操作。它代表单个的 T21 结构，多个的数据。**

这意味着实时视频流效果处理器的并行处理管道。如果您已经掌握了脉搏，您可能已经听说过在 JS 中这样做，但是我总是发现尝试使用 JavaScript 现有的类型系统来做这样的低级工作有点笨拙。

在这种情况下，您可能想忘记对象系统、垃圾收集器和所有有趣的动态东西。只需将一些原始数据排成小行，并尽可能快地处理它们。

## 向我展示应用程序

游戏、虚拟现实和增强现实就是明显的例子。目前大多数 WebAssembly 演示使用 Unity 或 Unreal Engine，这两种引擎都已经支持编译到 asm.js。目前，像 [**Ableton Live**](https://www.ableton.com/) 这样的音乐制作应用程序和像 [**Adobe Premier Pro**](http://www.adobe.com/products/premiere.html) 这样的视频制作应用程序移植到 web 上仍然有点笨拙。不是不可能，提醒你一下，只是*尴尬。仍然有很多问题需要解决，比如为数据密集型实时应用提供更好的时间保证。*

我们还将看到通过处理功能网络传输大通道数据的应用程序，如 [**吉他效果踏板**](http://dashersw.github.io/pedalboard.js/demo/) 。这些是传统上大多数人在想到 JavaScript 时不会想到的东西。很多人可能会认为这种尝试是疯狂的。

但是 **JavaScript 真的是一种很棒的语言**来构建你能想到的任何应用程序所需的大部分代码**。**

> **WebAssembly 填补了用 JavaScript 填充
> 会很尴尬的空白**
> 。

这些差距不是秘密。即使是 JavaScript 的忠实粉丝也会承认，我们有时会超出 G-forces JavaScript 可以处理的范围。直到昨天，我还认为计划是通过增加 JavaScript 语言本身的复杂性来填补这些空白。Brendan Eich 在一次流畅的会议主题演讲中谈到了这一点，我对此表示赞赏:

但是我们中的一些人真正缺少的是用一种令人惊叹的高级语言编写大部分代码的能力，并且当我们真正需要提升时，仍然能够偶尔使用一种专门的裸机汇编语言。

> WebAssembly 是 JavaScript 的
> **nitrous boost！**

我确信今天还有 200 篇关于 WebAssembly 的文章在网上发布。我敢打赌，他们中的大多数人会关注 WebAssembly 的另一面，对此我同样感到兴奋…

# WebAssembly 为 Web 平台带来了语言多样性

我们并不真的需要 WebAssembly 来将其他语言引入 web 平台。**我们已经有了最好的** [**AAA 游戏**](https://www.unrealengine.com/what-is-unreal-engine-4) [**引擎**](https://unity3d.com/unity/multiplatform) 通过**编译成 JavaScript** 以惊人的质量流畅运行。

> 如果你听说过
> JavaScript 很慢……
> ***事实并非如此。***

WebAssembly 增加了大多数 JS 开发人员都会同意的 JavaScript 中不需要的东西。我们可能仍然会得到这些东西，但不是因为 JavaScript 代码需要它们。我们将让它们支持从使用它们的其他语言编译。

WebAssembly 为我们提供了一个**可选的编译目标—** 一个专门为此目的而设计的目标。

现在移植严重依赖共享内存线程等特性的代码将变得更加容易。我敢打赌，为 WebAssembly 编写编译器的过程没有为 JavaScript 编写编译器复杂，因为从源语言特性到目标 AST 有更好的映射。

虽然听到我们所知道和喜爱的所有旧语言现在都将在 web 平台上运行是一件很棒的事情，但 WebAssembly 还意味着一件非常重要的事情:

> *WebAssembly 是一个对开发者建筑的公开邀请* 
> 
> 未来的编程语言。

网络平台的未来从未如此光明。你最好把墨镜拿出来。

# 更新/常见问题

## wasm 是什么？

简称 **W** eb **为** se **m** bly。

## 为什么不使用 JVM？

我们以前尝试过通过插件将 JVM 放入浏览器。结果不太好。JavaScript 已经有一个虚拟机了。另一个 VM 意味着第二套 API 挂钩，让 VM 访问 DOM、网络、传感器、输入设备等…这不是免费的。像垃圾收集这样的虚拟机进程如何竞争相同的资源？这可能比听起来更难。

最初，WebAssembly 将在 asm.js polyfill 上运行，这意味着它可以利用**现有的 JavaScript VM 基础。**设计将在此基础上发展，因此有一个比其他虚拟机更平滑的浏览器集成路径。

## 问:这是不是意味着其他语言会取而代之？这样不会造成碎片化吗？

答: **JavaScript 在服务器端和小型硬件和机器人的嵌入式编程方面一直有很强的竞争力**，但是尽管有许多现有的、可行的替代方案，有成熟的软件包生态系统和许多训练有素的开发人员， **Node 仍然在以惊人的速度接管创业和企业网络服务器。**

JavaScript 在生态系统和开发者社区中也有重要的网络锁定。我喜欢张贴模块计数图，因为它每次都会给人留下更深刻的印象:

![](img/a6827b8ce732994dba43602d4e35f544.png)

那条绿线是 **npm，【JavaScript 的标准包库。它与 Node 捆绑在一起。**

JavaScript 也正在成为游戏编程、机器人和物联网设备的热门选择，所有这些都面临着来自 C、C++和 Java 的强大竞争。这和 JavaScript 作为网页浏览器语言的主导地位关系不大。那些开发者有选择，他们选择 JavaScript 是因为他们喜欢它。

## 文本格式是什么样的？

最流行的是类似 lisp 的 S 表达式形式。要体验一下，请查看 [WebAssembly 游乐场](http://ast.run/)。

## 我可以在哪里了解更多信息？

参见后续文章[“我们为什么需要 web assembly:Brendan Eich 访谈”](/javascript-scene/why-we-need-webassembly-an-interview-with-brendan-eich-7fb2a60b0723)。

2015 年 LLVM 开发者大会，JF·巴斯蒂恩和丹·戈曼演讲

## 我怎样才能留在圈子里？

*   [W3C WebAssembly 社区组](https://www.w3.org/community/webassembly/)
*   [邮件列表](http://lists.w3.org/Archives/Public/public-webassembly/)
*   IRC:IRC://IRC . w3 . org:6667/# web assembly
*   [GitHub](https://github.com/webassembly)
*   谁参与了？

> [跟随
> 埃里克·艾略特](https://ericelliottjs.com)学习 JavaScript

***埃里克·艾略特*** *是一位科技产品和平台顾问，《 [*【作曲软件】*](https://leanpub.com/composingsoftware)*[*【EricElliottJS.com】*](https://ericelliottjs.com)*[*devanywhere . io*](https://devanywhere.io)*的联合创始人，以及 dev 团队导师。他曾为 Adobe Systems、* ***、Zumba Fitness、*** ***【华尔街日报、*******【ESPN、*******【BBC】****等顶级录音艺人和包括* ***Usher、【Metallica】********

*他和世界上最美丽的女人享受着与世隔绝的生活方式。*