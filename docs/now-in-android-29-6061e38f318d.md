# 现在在 Android #29 中

> 原文：<https://medium.com/androiddevelopers/now-in-android-29-6061e38f318d?source=collection_archive---------8----------------------->

![](img/3302b2f64d329292394512ce9359043b.png)

Illustration by [Virginia Poltrack](https://twitter.com/VPoltrack)

## 应用捆绑包、AndroidX 稳定版本、Kotlin 文章和视频以及新的 ADB 播客

欢迎来到 Android 中的 Now，这是您对 Android 开发世界中新的和值得注意的事物的持续指导。

# 视频和播客形式的 NiA29

这个*现在在 Android* 中也以视频和播客的形式提供。内容是一样的，但是需要的阅读量更少。文章版本(继续阅读！)仍然是链接到所有内容的地方。

# 录像

# 播客

点击下面的链接，或者在你最喜欢的客户端应用程序中订阅播客。

# 疯狂技能:应用捆绑包

![](img/191f9a04136e836873a74d498cf46f0a.png)

[疯狂技能](https://developer.android.com/series/mad-skills)系列继续滚动，关于现代 Android 开发的技术内容。 [Wojtek Kaliciński](https://medium.com/u/b913acc64439?source=post_page-----6061e38f318d--------------------------------) 和 [Ben Weiss](https://medium.com/u/65fe4f480b1c?source=post_page-----6061e38f318d--------------------------------) 在 App Bundles 上发布了第二季的几集。到目前为止，我们已经看到了关于 Play 应用程序签名、构建您的第一个应用程序捆绑包和 Play 功能交付的内容。查看下面的视频和文章，了解更多信息。

## 第 1 集:关于 Play 应用程序签名的所有知识

在一个关于 Android 应用捆绑包的系列中，很自然地会谈到应用签名，因为 Play 会生成 apk 供下载到用户设备，而这些 apk 必须经过签名才能安装。此视频将带您了解在 Play 控制台上启用应用程序签名的步骤，包括让 Google 生成密钥或上传您自己的密钥的选项。

请务必查看 [Wojtek](https://medium.com/u/b913acc64439?source=post_page-----6061e38f318d--------------------------------) 关于 Play App 签名常见问题的相关文章:

[](/androiddevelopers/answers-to-common-questions-about-app-signing-by-google-play-b28fef836af0) [## Google Play 应用签名常见问题解答

### 根据我们的开发者关系团队听到的问题，以下是一些关于启用 Google Play 应用签名的建议…

medium.com](/androiddevelopers/answers-to-common-questions-about-app-signing-by-google-play-b28fef836af0) 

## 第 2 集:构建您的第一个应用捆绑包

[Ben](https://medium.com/u/65fe4f480b1c?source=post_page-----6061e38f318d--------------------------------) 的视频向你展示了创建应用捆绑包的步骤，你可以在 Android Studio 或命令行中完成，然后将它上传到 Play 控制台。他还展示了如何使用 Play Console 中的工具来探索关于上传的包的信息。

## 第 3 集:为播放功能交付配置您的应用

这一集展示了如何使用 Android Studio 将你的应用模块化，以及如何在安装时(用可选条件决定是否安装)或按需选择要下载的模块。[本](https://medium.com/u/65fe4f480b1c?source=post_page-----6061e38f318d--------------------------------)还详细介绍了如何使用 API 请求按需模块安装。

或者以文章形式:

[](/androiddevelopers/configuring-your-app-for-play-feature-delivery-f15a93856a0d) [## 为播放功能交付配置您的应用

### 这篇文章有视频，链接在文章的末尾。Android 应用捆绑包是一种发布格式…

medium.com](/androiddevelopers/configuring-your-app-for-play-feature-delivery-f15a93856a0d) 

## 第 4 集:使用 bundletool 和 Play Console 进行测试

在这最后一集， [Wojtek](https://medium.com/u/b913acc64439?source=post_page-----6061e38f318d--------------------------------) 解释了如何使用可用的工具来测试你的包和生成的 apk，包括用于本地测试的 bundletool 和用于测试上传的 Play Console。

有几篇文章和文档是从视频描述中链接出来的，请务必查看这些文章和文档，以获得关于该主题的更多信息。

请继续关注下周的最终应用捆绑内容，届时我们将在下周四举办现场问答(届时 YouTube 直播链接将出现在播放列表中，另外我们将在此之前在推特上发出提问通知)。

对于正在进行的内容，一定要查看 YouTube 上的 [MAD 技能播放列表](https://www.youtube.com/playlist?list=PLWz5rJ2EKKc91i2QT8qfrfKgLNlJiG1z7)，Medium 上的[文章](https://medium.com/androiddevelopers/tagged/mad-skills)，或者指向所有内容的[这个方便的登陆页面](https://developer.android.com/series/mad-skills)。下个系列下周开始:敬请期待！

# 安卓克斯

在 AndroidX 库的众多增量 alpha、beta 和 RC 版本中，最近推出了一些重要的稳定版本，我想引起您的注意。

*   [ConstraintLayout 2.0.4](https://developer.android.com/jetpack/androidx/releases/constraintlayout#2.0.4) :这里最重要的变化实际上是在版本 2.0.2(也是最近的版本)中，它有重要的性能优化。但从那以后，请选择最新版本来修复额外的错误。
    性能提升来自于 ConstraintLayout 在何时可以跳过运行解算器并避免多次测量和解算过程方面变得更加智能。这允许在许多常见情况下获得巨大的性能优势。例如，具有大量视图和使用匹配约束(`layout_[height|width]=”0dp”`)的视图的层次结构可以显著加快布局。
*   Startup 1.0.0 :开发应用启动库是为了让应用在启动时以一种比其他方式更优化的方式初始化组件。事实证明，初始化单个 ContentProvider 需要大量的时间，许多应用程序会初始化不止一个——通常是*很多*多个。启动允许您的应用程序初始化组件，而无需创建几个 ContentProvider(剧透:库为所有请求创建和使用一个 content provider)，允许您的应用程序跳过大部分开销。
*   [跟踪 1.0.0](https://developer.android.com/jetpack/androidx/releases/tracing#1.0.0) :这个库将跟踪事件写入系统跟踪缓冲区。使用跟踪事件检测应用程序可用于提供有关性能的增强信息，然后您可以使用 Perfetto 工具(或旧设备上的 Systrace)查看这些信息。参见[系统跟踪指南](https://developer.android.com/topic/performance/tracing)了解更多关于如何操作的细节。
    这个“新”库本质上并没有提供新的功能，但它以一种非常有针对性的方式提供了现有 [TraceCompat](https://developer.android.com/reference/kotlin/androidx/core/os/TraceCompat) 类的功能，因此您不必为这种有限的功能而引入所有的 androidx.core。还有一个有用的 [KTX 扩展函数](https://developer.android.com/reference/kotlin/androidx/tracing/package-summary?hl=en#trace),它使得围绕给定的代码块添加跟踪变得简单。

# 文章和视频:科特林、科特林、科特林和科特林

我们并不总是谈论科特林，但当我们这样做时，我们会说很多。最近发布了几篇关于科特林的文章和视频:

## 经典数据

弗洛里纳·蒙特内斯库在正在进行的科特林词汇系列中增加了另一集，这次是关于科特林数据类的。数据类允许您用较少的样板代码轻松创建一个保存数据的结构，同时依靠 Kotlin 自动生成适当的`equals()`和`hashCode()`函数。除了`copy()`之外，您还可以获得开箱即用的类属性的[析构](https://youtu.be/VIrIl03kn44)。和科特林词汇集一样，弗洛里纳探索了数据类的反编译字节码，解释了它是如何工作的。

或者以文章形式:

[](/androiddevelopers/data-classes-the-classy-way-to-hold-data-ab3b11ea4939) [## 数据类——保存数据的经典方式

### 科特林词汇:数据类

medium.com](/androiddevelopers/data-classes-the-classy-way-to-hold-data-ab3b11ea4939) 

## 内置委托

说到科特林的词汇，[穆拉特·耶纳](https://medium.com/u/e947fef0dfe0?source=post_page-----6061e38f318d--------------------------------)贴出了他之前关于科特林代表性特征的文章的续篇。这一次，他讨论了由 [Kotlin 标准库](https://kotlinlang.org/api/latest/jvm/stdlib/)提供的代表:`lazy`、`observable`、`vetoable`和`notNull`。

[](/androiddevelopers/built-in-delegates-4811947e781f) [## 内置委托

### 科特林词汇:代表第二部分

medium.com](/androiddevelopers/built-in-delegates-4811947e781f) 

## 我应该学习 Android 版的 Kotlin 吗？(和其他常见问题)

简短的回答是…是的！

但是为了更长的解释，[弗洛里纳](https://medium.com/u/d5885adb1ddf?source=post_page-----6061e38f318d--------------------------------)发布了这篇文章来回答我们从开发者那里得到的关于投资科特林教育和发展的一些最重要的问题，并且包括重要学习资源的链接。

[](/androiddevelopers/should-i-learn-kotlin-for-android-and-other-faqs-88a2bb281a60) [## 我应该为 Android 和其他常见问题学习 Kotlin 吗

### 自从我们在 2017 年宣布支持 Kotlin 以来，我们一直收到很多关于 Android 上 Kotlin 的问题:你…

medium.com](/androiddevelopers/should-i-learn-kotlin-for-android-and-other-faqs-88a2bb281a60) 

## Kotlin 带来更少的崩溃和更高的稳定性

在这篇文章中，[弗洛里纳](https://medium.com/u/d5885adb1ddf?source=post_page-----6061e38f318d--------------------------------)讨论了 Kotlin 应用程序比不是用 Kotlin 编写的应用程序更不容易出错的一些原因。她指出了一些具体的应用和用例来支持这种说法，但也讨论了这种语言允许更健壮的代码的一些原因，包括可空性。= `equals()`，还有更多。查看帖子了解所有细节。

[](/androiddevelopers/fewer-crashes-and-more-stability-with-kotlin-b606c6a6ac04) [## Kotlin 带来更少的崩溃和更高的稳定性

### 用户希望对你的应用有一个无缝的体验。崩溃会导致差评、卸载…

medium.com](/androiddevelopers/fewer-crashes-and-more-stability-with-kotlin-b606c6a6ac04) 

# 播客剧集

![](img/96836907fab238d8b2edf998acab72d7.png)

自从上一期《现在》在安卓发布以来，又有一集[安卓开发者后台](http://androidbackstage.blogspot.com/)发布。请点击下面的链接，或在您最喜欢的播客客户端查看:

## ADB 152:使用线圈加载图像

[Romain Guy](https://medium.com/u/c967b7e51f8b?source=post_page-----6061e38f318d--------------------------------) ， [Tor Norbye](https://medium.com/u/8251a5f98c9d?source=post_page-----6061e38f318d--------------------------------) 我和 Instacart 的 [Colin White](https://twitter.com/colinwhi) 聊了聊他的开源图片加载库，Coil。我们讨论了图像加载、性能、开源，以及使用 Kotlin 和协程来创建这个 Kotlin 优先的库。

[](https://androidbackstage.blogspot.com/2020/11/episode-151-image-loading-with-coil.html) [## 第 152 集:用线圈加载图像

### 本周，Tor、Romain 和 Chet 加入了一位特别嘉宾:来自 Instacart 的 Colin White。科林是线圈的作者…

androidbackstage.blogspot.com](https://androidbackstage.blogspot.com/2020/11/episode-151-image-loading-with-coil.html) 

# 那么现在…

这次到此为止。所以去了解一下[应用捆绑包](https://www.youtube.com/watch?v=hTC0rKllhIw&list=PLWz5rJ2EKKc91i2QT8qfrfKgLNlJiG1z7&index=4)！下载[最新 AndroidX 版本](https://developer.android.com/jetpack/androidx/versions/all-channel)！阅读最近关于科特林的文章！听最新的[亚行播客](https://androidbackstage.blogspot.com/2020/11/episode-151-image-loading-with-coil.html)集！请尽快回到这里，收听 Android 开发者世界的下一次更新。