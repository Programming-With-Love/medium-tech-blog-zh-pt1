# 编写 Kotlin 多平台移动应用的体验

> 原文：<https://blog.kotlin-academy.com/the-experience-writing-kotlin-multiplatform-mobile-apps-6f9677d7cc1f?source=collection_archive---------0----------------------->

![](img/fff66fde0ad6e65638d9263e9a4eab3b.png)

*去 https://github.com/itmaginationdemos/KMM-Sample-App*[](https://github.com/itmaginationdemos/KMM-Sample-App)*看看我们的知识库，里面有一个在 KMM 帮助下编写的示例应用*

*在进入文章的主要部分之前，我们想先解释一下我们的出发点。我们对 KMM (Kotlin [多平台](https://www.itmagination.com/clients-scope/mobile-application-development)移动)的早期知识知之甚少，完全不知情，我们遇到的一些问题是由于缺乏使用该框架的经验，因为它还是新鲜的。只需一些时间，您就可以很快熟悉这个工具。*

*顺便提一下，大公司已经意识到了新框架的好处——包括[飞利浦](https://kotlinlang.org/lp/mobile/case-studies/philips)、[网飞、](https://netflixtechblog.com/netflix-android-and-ios-studio-apps-kotlin-multiplatform-d6d4d8d25d23)和 [VMWare。](https://kotlinlang.org/lp/mobile/case-studies/vmware/)尽管在这个过程中有一些小问题，但 KMM 是大型组织中跨平台移动开发的一个非凡工具。*

# *建立*

*   *马科斯蒙特雷 12.5.1*
*   *Android Studio 电鳗金丝雀 10(但我们也尝试了海豚和花栗鼠)*
*   *XCode 14.0*
*   *格拉德*
*   *科特林 1.7.20-RC*

# *好人*

*当你输入短语“KMM 教程”时，谷歌搜索结果是[官方文件](https://kotlinlang.org/docs/multiplatform-mobile-getting-started.html#join-the-community)，它们非常有用。告诉读者我们应该将必要的代码放在哪个 Gradle 文件中会很有帮助，因为这并不明显(每个文件都被称为相同的)。这有点像你第一次开始学习 Android 编程和 Android Studio。*

*教程中提到的官方网站上的一个很棒的工具是 KDoctor，事实证明它对发现任何出现的问题都有很大的帮助。例如，当 KDoctor 在几秒钟内诊断出 KMM 插件被移除(似乎没有任何原因)时，有很多人挠头(尽管我们确实花了几个小时向好医生寻求帮助)。*

*基本的 [Koin](https://insert-koin.io/docs/reference/koin-mp/kmp/) 设置非常棒。它清晰而简洁。打开文档几分钟后，一切都井井有条。如果你需要任何进一步的解释，这里有一些 Android 和 iOS 的样本代码。有一个小问题，但在下一节中会有更多。*

# *坏事*

*开始一个新项目是势不可挡的。你必须掌握许多新概念。我们也受到大量不必要的文件夹/子集的轰炸。可以说，最初的几分钟可能会让新人却步。*

*让我们回到 Koin。正如我们已经提到的，基本设置是一个梦想，但有一个小问题——在 Android 和 iOS 之间共享代码，以防构造函数因任何原因而不同。更准确地说，当实例化 SQLDelight 数据库时，我们必须手动创建每个实例(这不是一个大问题，但可以添加到文档中)。*

*要创建 iOS 应用程序，您需要 XCode。不管出于什么原因，在 2022 年，我们在苹果方面的带宽有限，这使得下载需要 5 个小时(作为参考，Android Studio 下载需要 1 到 2 分钟)。反正就像 90 年代一样，睡觉前开始下载。*

# *丑陋的*

*我们采访了一些也尝试过 KMM 的人，有些人确实有这些问题，有些人有不同的问题，有些人没有。我们下面描述的经历可能和你的不一样。*

*遗憾的是，我们在 Gradle、Android Studio、Kotlin 和 KMM 插件本身上遇到了相当多的问题，很难准确指出是什么问题。从教程第一次开始到写这篇文章的时候，我们已经改变了所有的版本(它们定期改变)。*

*我们甚至尝试了 Android Studio 花栗鼠、海豚和电鳗，其中后者被证明是最稳定的(这很奇怪)。*

*此外，正如我们已经提到的，KMM 插件只是在某个时候被删除了，我们甚至没有注意到，直到我们调用 KDoctor。回想起来，这可能是这里最大的问题。没有它，许多奇怪的错误和假警报就会出现。例如，我们在共享的 iOS 和 Android 代码上有一堆错误，但是代码编译正确。*

*空的源集也有一些恼人的问题，它们被解释为模块。当 IDE(集成开发编辑器)确定我们缺少实现时，这就导致了使用 expect 关键字的问题(更多关于这个[这里](https://kotlinlang.org/docs/multiplatform-connect-to-apis.html))。*

*不确定这是否与 KMM 有关，但撰写导航也有一些问题。即使我们正确地遵循了说明，我们也无法导入 NavHostController。经过一番搜索，我们找到了一些清除 Gradle 缓存和重置 Java 类路径的建议。在无效缓存和重启的帮助下，40 分钟后，导入成功了。一点都不好玩。*

# *荣誉奖*

*我们想给这个项目添加一些更复杂的东西，所以我们开始录制语音笔记，就像这个应用程序的名字所暗示的那样。*

*我们一开始犯了一个新手的错误，那就是信任谷歌的文档。我们遇到的问题是写入外部存储。在最近几个 Android 版本中，这样做变得非常复杂，文档也没有太大帮助。文档的作者瞄准了忽略了大部分这些问题的 Android API 28。*

*此外，我们需要获得用户许可来录制语音消息，我们开始用老方法来做这件事(在 Compose 之前)。解决方案本来可以改进很多，但是很乱，我们不喜欢。所以，我们用谷歌搜索了一些更好的东西。输入[伴奏](https://github.com/google/accompanist)，这是一套与 Compose 一起使用的工具。在这方面，谷歌确实帮了我们一个大忙。这里有很多有用的特性，但是我们只使用了权限部分——它用三行代码处理语音权限。*

# *我们学到了什么*

## *开发商*

*也许当你读到这里时，你会觉得一切都不好，不值得。这与事实相去甚远。更应该把这看作是一张避免陷阱的地图(至少是其中的一些)。您将会遇到一些我们在这里没有涉及的潜在问题，这些问题大多与 iOS 上的多线程相关——但那是另一个时间的主题。*

*当你设置好一切后，你可以把它想象成一个多模块的 Android 应用程序，重点是与主模块(Android 和 iOS)共享尽可能多的代码。假设一个应用程序有一个相对简单的用户界面，但有很多逻辑(映射或离线优先)，你可以真正限制重复代码的数量，从而限制花在编写代码上的时间以及随之而来的不可避免的错误。*

## *经理*

***我们可能对较小的团队甚至个人有更偏见的看法，因为列出的一些问题在较大的团队中不是问题。当你有一个 10 人或更多开发人员的团队时，花几天时间设置好一切在大计划中并不算多。此外，代码重用的主要好处与更大的团队和更大的代码库相适应(共享模块中的代码越多，意味着跨平台重复的代码就越少)。***

*我们最近写了关于大型应用程序中代码可重用性的主题，你可以在这里查看。它的要点是，如果你有一个复杂的产品，你可能有许多复杂的代码和领域规则。这些东西必须写两遍(Android 和 iOS ),这意味着两倍的错误，两倍的成本，可能在功能上没有 100%的重叠。*

*最后，与其他多平台解决方案不同的是，通过设计，KMM 不会以任何方式触及应用程序的 UI 部分。UI 100%基于每个平台，因此它将具有原生应用程序的真实感觉。另一方面，你需要雇佣更多有编写原生 iOS 界面经验的开发人员。*

*我们鼓励你去尝试，你永远不知道，也许这正是你在寻找的。如果你需要在你的公司实施新应用的帮助，[请随时联系我们的专家。](https://itmagination.com/contact)*

*【https://www.itmagination.com】最初发表于[](https://www.itmagination.com/blog/the-experience-writing-kotlin-multiplatform-mobile-apps)**。***

**[![](img/7cc3e53c80b722128adb3c22f527646e.png)](https://kotlin-academy.us17.list-manage.com/subscribe?u=5d3a48e1893758cb5be5c2919&id=d2ba84960a)****![](img/e452e4fc7b1eea8b55443068d2db3db8.png)**