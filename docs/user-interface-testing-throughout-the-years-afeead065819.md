# 多年来的用户界面测试

> 原文：<https://medium.com/compendium/user-interface-testing-throughout-the-years-afeead065819?source=collection_archive---------1----------------------->

一年半以前，我参与了我们开发的一个 web 应用程序的用户测试。我们有许多我们应该遵循的松散定义的测试场景，但是有足够的空间进行探索性测试来做一些猴子测试。有了一点安全背景，我尝试了一些经典的 SQL 和 HTML 注入测试，为了确保一切正常，我还在 web 应用程序中加入了一些 Unicode 字符。Unicode 字符存储得很好，即:至少是[基本多语言平面](https://en.wikipedia.org/wiki/Plane_(Unicode)#Basic_Multilingual_Plane)的一部分(或平面 0，直到 0xFFFF 的字符)。但是高于 0xFFFF 的表情符号没有通过测试。

这个小测试让我稍微思考了一下这些年来文本输入和存储的需求是如何变化的。在我职业生涯的初期，1997 年，基本要求是能够处理特定码表的字符。信不信由你，在挪威建立一个针对数据库的应用程序，用挪威语字母保存文本并不是一件小事，因为 [ASCII](https://en.wikipedia.org/wiki/ASCII) 仍然统治着世界。尝试一些挪威语曾经是您验证应用程序和数据库设置是否正确的第一批手动测试之一。

能够在同一个数据库中同时处理挪威语和匈牙利语文本是后来才出现的。混合拉丁语和希腊语或西里尔字母只是一个遥远的梦想。我们甚至不敢想中国人或日本人。你可以想象当互联网兴起时会发生什么，突然你无法控制人们在你的小 web 应用程序中输入文本时使用什么样的代码表。

![](img/a254b3a05911a701f2a4b063a9ad5656.png)

“[Unicode Mixed Bag](https://www.flickr.com/photos/svensson/40467591/)” by [Alexander Svensson](https://www.flickr.com/photos/svensson/) / [CC BY-NC 2.0](https://creativecommons.org/licenses/by-nc/2.0/)

幸运的是，随着 UTF-8、UTF-16 和 Unicode 的采用，情况很快变好了。将俄语文本与英语一起存储突然不再是一个问题——除非你被一个旧的遗留数据库困在你的后端深处，作为记录系统。现在的问题是找到一种合适的字体，将所有必要的字符都放在适当的位置。但是一旦你有了合适的字体，存储和显示像✀★✓这样有趣的字符也不是问题。

但是正如我在上面提到的用户测试中发现的，需求在不断变化。如今，飞机 0 已经不够好了。人们希望存储远高于 0xFFFF 标记的表情符号。此外，大多数用户不知道这样一个标记的存在，它意味着什么，以及几乎每个表情符号组都有字符在标记的两侧。结论:如果您正在开发一个 web 应用程序，您最好确保它完全支持 Unicode，并且您必须测试它是否支持 Unicode。与我在本文开始时提到的相比:为能够在本地桌面应用程序中编写和存储一些基本的挪威语文本而自豪。

哦，如果你完全支持 Unicode，那么你还有那些聪明的表情符号标志序列，它们使用一对[区域指示符号](https://en.wikipedia.org/wiki/Regional_Indicator_Symbol)。确保你也支持他们，因为迟早有人会想用那些可爱的小旗子🇬🇧 🇺🇸 …