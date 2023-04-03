# 使用基线配置文件提高性能

> 原文：<https://medium.com/androiddevelopers/improving-performance-with-baseline-profiles-fdd0db0d8cc6?source=collection_archive---------0----------------------->

基线概况的快速概述

![](img/c6be17fbd54b58cfe76cb05c3f5c417a.png)

Illustration by Claudia Sanchez

在这篇关于**使用基线配置文件提高性能的 MAD 技能文章**中，您将了解什么是基线配置文件，以及如何使用它们来改善应用启动和加快运行时间。

> 基线配置文件通过提前优化关键代码路径，帮助您的应用程序更快地启动和运行。这允许更平滑的用户体验。

由于使用了基线配置文件，我们已经看到了显著的应用启动和运行时改进。你可以阅读[谷歌地图如何在引入基线资料后将应用启动时间缩短了 40 %](https://android-developers.googleblog.com/2022/01/improving-app-performance-with-baseline.html) 。

你可以在这里观看这篇文章的视频。

Improving Performance with Baseline Profiles

# 简而言之，基线剖面

基线配置文件是提前编译并随应用程序一起安装的类和方法的列表。这意味着在使用应用程序时，您的代码不需要使用实时(JIT)编译器进行解释。这转化为启动时间的改进、jank 的减少以及最终用户运行时性能的整体提高。

可以为应用程序生成基准配置文件，并与交付给用户的应用程序捆绑在一起。但是图书馆也可以创建他们自己的基线配置文件，并与他们的 AAR 一起发布。来自库的基线配置文件将被合并到应用程序的基线配置文件中，然后编译成一个文件，该文件随应用程序一起提供。

为了加速开发过程，基线概要文件不是为调试版本安装的，而是为发布版本安装的。因此，要查看您的概要文件的性能增益，请始终对照发布版本进行验证。

> 总是在发布版本中验证性能增益。

Jetpack Compose 是一个受欢迎的库示例，它由[提供基线概要文件](https://developer.android.com/jetpack/compose/performance)。通过向库消费者提供这种配置文件，Jetpack Compose 可以最大限度地减少作为应用程序发布版本的非捆绑 UI 工具包的影响。

# 生成基线配置文件

宏基准库附带了一个规则[来为您生成基准概要文件](https://developer.android.com/reference/kotlin/androidx/benchmark/macro/junit4/BaselineProfileRule)。通过使用 UIAutomator，您可以在应用程序的关键用户旅程中驱动配置文件生成器。应用程序中的 API 调用将被捕获并集成到宏基准库创建的基线配置文件中。

基线概要文件生成器的最小可行设置如下所示。下面的
代码将为您生成一个基线概要文件，您可以实现`clickThroughUserJourney()`来指导生成器处理更复杂的场景。

```
@ExperimentalBaselineProfilesApi
@RunWith(AndroidJUnit4::class)
class BaselineProfileGenerator {
    @get:Rule
    val baselineProfileRule = BaselineProfileRule()

    @Test
    fun startup() = baselineProfileRule.collectBaselineProfile(
        packageName = "com.example.app"
    ) {
        pressHome()
        startActivityAndWait()
        clickThroughUserJourney()
    }
}
```

在 GitHub 的示例应用程序中，您可以看到一个简单的基线概要文件生成器是如何设置的[。Android 示例应用](https://github.com/android/performance-samples/blob/main/MacrobenchmarkSample/macrobenchmark/src/main/java/com/example/macrobenchmark/TrivialBaselineProfileBenchmark.kt)中的[有更复杂的基线配置文件设置。](https://github.com/android/nowinandroid/blob/main/benchmark/src/main/java/com/google/samples/apps/nowinandroid/baselineprofile/BaselineProfileGenerator.kt)

要了解关于 Macrobenchmark 库以及如何设置基准的更多信息，请阅读本系列的前一篇文章，或者观看视频。

[](/androiddevelopers/inspecting-performance-95b76477a3d7) [## 检查性能

### 这篇关于检查性能的 MAD 技巧文章向您介绍了一些工具和方法，当您的代码…

medium.com](/androiddevelopers/inspecting-performance-95b76477a3d7) 

Inspecting Performance

用于创建基线配置文件的 [codelab 通过最新的最佳实践指导您入门，并为您留下第一个基线配置文件。](https://goo.gle/baseline-profiles-codelab)

# 在您等待下一篇文章的时候

下一篇文章都是关于**监控 app 性能**。

去看看我们改进的开发者[文档](http://d.android.com/performance)，我们已经用 MAD 指南更新了它。

要获得更详细的代码，请查看 GitHub 上的[示例](http://github.com/android/performance-samples)或参加[宏基准代码实验室](https://goo.gle/baseline-profiles-codelab)或[基线配置文件代码实验室](https://goo.gle/baseline-profiles-codelab)以获得主题的实际指导。

确保在视频评论或 Twitter 上提出你的问题，使用 [#MADPerfQA](https://twitter.com/search?q=%23MADPerfQA) 在 9 月 1 日的 Q & A 会议上直接从致力于 Android 性能的工程师那里获得答案

查看关于性能调试的完整 MAD 技能系列，了解如何检查代码中发生的事情。

Performance Debugging