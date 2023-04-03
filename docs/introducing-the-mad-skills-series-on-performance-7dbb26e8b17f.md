# 介绍关于绩效的 MAD 技能系列

> 原文：<https://medium.com/androiddevelopers/introducing-the-mad-skills-series-on-performance-7dbb26e8b17f?source=collection_archive---------1----------------------->

![](img/041053fb8512a9ec47c0393a13bd6ca1.png)

Illustration by Claudia Sanchez

性能跨越了 Android 开发的每一个方面，作为现代 Android 开发的一部分，我们的目标是使其更加平易近人和用户友好。我们还发现，改进的应用程序性能、用户满意度和业务指标之间存在直接关联。

所以，是时候开始另一系列关于性能的**疯狂技巧**了。

我们将在整个 8 月发布剧集，最后在 YouTube 上与致力于 Android 性能的工程师进行现场问答。

继续阅读本系列文章，了解现代 Android 性能。除了文章，我们还发布视频，第一个视频在这里。

MAD Skills Performance Trailer

# 下面是即将推出的内容

在本系列中，我们将向您介绍在处理性能时很重要的指标。我们将使用这些指标，向您介绍工具、库和最佳实践，帮助您检查、改进和监控应用的启动时间，并提供流畅的用户体验。

我们将分享如何创建和使用 [**基线配置文件**](https://developer.android.com/topic/performance/baselineprofiles) 。

这项新技术已经帮助几家顶级合作伙伴将应用启动时间平均缩短了 30%。使用基线配置文件可以提前编译关键的用户旅程，并在启动速度提高的基础上提供改进的运行时体验。

> 基线概要文件是生产就绪的，并且可以帮助您的代码运行得更快。

查看 Android 中的和[性能示例](http://github.com/android/performance-samples)，了解如何设置基线配置文件。您还可以遵循新的[基线配置文件 Codelab](https://goo.gle/baseline-profiles-codelab) 来获得创建自己的指导路径。

我们还将介绍 [Android Studio profilers](https://developer.android.com/studio/profile/android-profiler) 的新发展，以及 [Jetpack Macrobenchmark](https://developer.android.com/topic/performance/benchmarking/macrobenchmark-overview) 库如何帮助您跟踪应用程序的性能，并向您介绍更多有助于您在开发和生产过程中监控代码性能的库和产品。

我们将向您展示如何使用 [Firebase 性能监控](https://firebase.google.com/docs/perf-mon)以及 [Android Vitals](https://developer.android.com/topic/performance/vitals) 如何帮助您掌握现场指标。我们将让您先睹为快，看看如何使用新的 [JankStats](https://developer.android.com/topic/performance/jankstats) 库收集 jank 指标。

# 现代表演心理模型

我们通过引入新的绩效心智模型，使我们的指导更加平易近人。我们所有的指导现在都与三大支柱保持一致:

*   检查
*   改善
*   班长

这些支柱为您提供了一个封闭反馈回路中的结构化绩效方法。

![](img/519431fd7d098ba44fd3bd22a03b82d9.png)

Inspect -> Improve -> Monitor & repeat

**检查性能**帮助你了解你的应用程序正在发生什么。

这样你就可以看到你的应用程序正在发生什么，以及它与你期望发生的事情是如何一致的。

**提高性能**支柱由工具、库和指南组成，帮助您的代码获得性能提升。

当**监控性能**时，您能够验证实施的改进是否确实使性能指标朝着正确的方向发展。

它还可以为您的下一个检查周期提供更多数据。

## 还有更多

性能指标不仅仅包括改善应用程序的启动，以及创造流畅、免费的体验。

减少**电池消耗**，智能使用**数据**和缓存，减少**应用程序大小**，以及创建整体**无崩溃体验**在处理性能时也很重要。

虽然我们不会在这个 MAD 技能系列中涵盖所有这些领域，但你可以在我们定期更新的[开发者指南](https://developer.android.com/topic/performance/improving-overview#solving_common_problems)中找到详细信息。

# 合作伙伴成功案例

在与主要合作伙伴的合作中，我们一次又一次地看到，提高应用程序的性能会直接影响用户保留率、更高的评级和改进的业务指标。

Zomato 的启动速度平均提高了 30 %,并发现提高应用程序性能是业务增长的一个关键因素。
[看这里的故事](https://developer.android.com/stories/apps/zomato)。

谷歌地图使用基线配置文件将其应用程序的启动和运行时间提高了 40 %，这反过来又将搜索速度提高了 2 %以上。下面是完整的故事。

# 当你等待的时候

请务必查看我们关于性能调试的完整 MADSKills 系列，了解如何检查代码中发生的事情。

MAD Skills Performance Debugging Series

此外，我们改进的开发者[文档](http://d.android.com/performance)最近已经更新，所以去看看吧。

查看 GitHub 上的[样本](http://github.com/android/performance-samples)或使用[宏基准代码实验室](https://goo.gle/baseline-profiles-codelab)或[基线配置文件代码实验室](https://goo.gle/baseline-profiles-codelab)获得主题的实际指导。

此外，确保在视频评论或 Twitter 上提出你的问题，使用 [**#MADPerfQA**](https://twitter.com/search?q=%23MADPerfQA) 直接从致力于 Android 性能的工程师那里获得答案。