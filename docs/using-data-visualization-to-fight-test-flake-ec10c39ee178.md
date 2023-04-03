# 利用数据可视化对抗测试碎片

> 原文：<https://medium.com/walmartglobaltech/using-data-visualization-to-fight-test-flake-ec10c39ee178?source=collection_archive---------6----------------------->

![](img/c49aa576bb1d84cd6a60ce0027842564.png)

By Robert Simmon (NASA Earth Observatory) [Public domain], via [Wikimedia Commons](https://commons.wikimedia.org/wiki/File:Unrest_at_Turrialba_Volcano,_Costa_Rica.jpg)

古怪。非确定性。不靠谱。

这些都是端到端测试的常用术语。在[沃尔玛实验室](http://walmartlabs.com/)，我们知道建立一个连续的交付管道依赖于一个完全可靠的测试自动化系统。系统中任何可察觉的缺陷都会削弱开发人员对我们工具的信心。

但是，当非确定性行为出现时，我们如何打开黑盒并确定来源？

在下面分享的演讲中，最初是最近在 [SeleniumConf UK](http://2016.seleniumconf.co.uk/) 上发表的，我们展示了 *Bloop* 的一个潜行高峰，这是 [TestArmada](http://testarmada.github.io) 的一个新组件，我们很快就会以开源形式发布。Bloop 收集关于测试的微观和宏观层面的统计数据，允许我们“缩小”并发现测试性能和可靠性的趋势，这些趋势在单个测试套件层面上并不总是可见的。使用开源数据可视化工具允许我们对数据进行切片和切块，以确定根本原因。

在此视频中，我们分享了这些数据和趋势如何提供即时的见解，以及提出新的和神秘的问题，我们希望与社区合作来回答这些问题。

要分享您的观点并了解 TestArmada 的最新动态，请在 Twitter 上关注我们！