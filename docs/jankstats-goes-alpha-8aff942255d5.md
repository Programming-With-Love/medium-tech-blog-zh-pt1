# JankStats 变成 Alpha

> 原文：<https://medium.com/androiddevelopers/jankstats-goes-alpha-8aff942255d5?source=collection_archive---------3----------------------->

![](img/15b695e85caf4167b235e0b09798b88c.png)

Illustration by Virginia Poltrack

## 一个在现实世界中追捕 jank 的图书馆

> 邱建(名词):糟糕的应用程序性能会导致丢帧、不连续的用户界面动作和糟糕的用户体验。请参阅“不满意的用户”

调试性能问题是…困难的。通常，不清楚从哪里开始，使用什么工具，用户有什么问题，或者这些问题如何在现实世界的设备上表现出来。

Android 团队在过去几年中提供了更多工具来调试问题的不同部分，从分析[启动性能](/androiddevelopers/app-startup-part-1-34f57b65cacd)到测试[特定代码路径](https://developer.android.com/studio/profile/benchmark)到测试和优化特定[用例](https://developer.android.com/studio/profile/macrobenchmark-intro)到[IDE](https://developer.android.com/studio/profile/android-profiler)中的可视化分析器。所有这些都是为了开发时测试，以帮助您调试和修复您在本地看到的问题。

与此同时，Google Play 的 [Android Vitals](https://support.google.com/googleplay/android-developer/answer/9844486?visit_id=637793617854677762-3147968388&rd=1) 和 [Firebase](https://firebase.google.com/products/performance) 都提供了仪表盘，开发者可以在那里看到他们的应用在用户设备上的表现。

但是，仍然很难知道如何找到您的应用程序在真实情况下可能存在的问题，尤其是发生在用户设备上的问题，而不仅仅是您在舒适的椅子上使用的方便的开发机器上看到的情况。性能仪表板会有所帮助，但它们不一定能提供您需要的详细程度，让您知道当您的用户遇到问题时发生了什么。

进入 **JankStats** :第一个专门构建的 AndroidX 库，用于检测和报告用户设备上应用程序的性能问题。

JankStats 是一个相对较小的 API，主要有三个目标:捕获每帧的性能信息，在用户(不仅仅是开发)设备上运行您的应用程序，以及在应用程序出现性能问题时支持检测和报告应用程序中发生的情况。

# 每帧性能

Android 平台已经提供了获取帧性能数据的方法。例如，您可以从 API 24 开始使用 FrameMetrics，我们已经在最近的版本中添加了它，以提供更多的信息。如果您运行的是早期版本，有多种方法可以获得不太准确但仍然有用的计时信息。

因此，如果您想让您自己的帧持续时间逻辑在所有版本中工作，您需要在 API 版本中实现这些不同的机制。或者您可以使用单个的 JankStats API，它可以为您完成这项工作…此外还提供了更多的特性(继续阅读！).

JankStats 通过提供单个 API 来报告每帧持续时间，并在内部委托给适当的机制(API 24+上的`[FrameMetrics](https://developer.android.com/reference/android/view/FrameMetrics)`等)，从而简化了这一过程。您不必担心这些数据是从哪里来的，您只需让 JankStats 告诉您事情花了多长时间，您就可以得到带有这些信息的回调。

创建和监听 JankStats 数据本质上非常简单:创建数据，然后坐下来(好吧，代码坐下来)监听。以下是来自 JankStats 示例的这些步骤的示例， [JankLoggingActivity](https://github.com/android/performance-samples/blob/main/JankStatsSample/app/src/main/java/com/example/jankstats/JankLoggingActivity.kt) :

```
val jankFrameListener = JankStats.OnFrameListener { frameData ->
  // real app would do something more interesting than log this...
  Log.v("JankStatsSample", frameData.toString())
}jankStats = JankStats.createAndTrack(
    window,
    Dispatchers.Default.asExecutor(),
    jankFrameListener,
)
```

那个`Log.v()`调用是…不是你应该在你的应用中做的，它只是为了举例。相反，您可能应该聚合/存储/上传数据以供以后分析，而不是仅仅将数据放到日志中。无论如何，下面是它在 API 30 模拟器上运行时产生的结果的示例(为了清楚起见，删除了一些 logcat 噪声并添加了空白行):

```
JankStats.OnFrameListener: FrameData(frameStartNanos=827233150542009, frameDurationUiNanos=27779985, frameDurationCpuNanos=31296985, isJank=false, states=[Activity: JankLoggingActivity])JankStats.OnFrameListener: FrameData(frameStartNanos=827314067288736, frameDurationUiNanos=89903592, frameDurationCpuNanos=94582592, isJank=true, states=[RecyclerView: Dragging, Activity: JankLoggingActivity])JankStats.OnFrameListener: FrameData(frameStartNanos=827314167288732, frameDurationUiNanos=88641926, frameDurationCpuNanos=91526926, isJank=true, states=[RecyclerView: Settling, RecyclerView: Dragging, Activity: JankLoggingActivity])JankStats.OnFrameListener: FrameData(frameStartNanos=827314183945923, frameDurationUiNanos=4731405, frameDurationCpuNanos=8283405, isJank=false, states=[RecyclerView: Settling, Activity: JankLoggingActivity])
```

你可以在记录的`frameData`中看到一些有趣的片段:

*   有一些带`isJank=true`的框架。该日志取自示例应用程序`JankLoggingActivity`的运行，它位于下面链接的[示例](https://github.com/android/performance-samples/tree/main/JankStatsSample)中。那个应用强制一些长帧(谢谢，`Thread.sleep()`！)，这导致由 JankStats 进行这种 jank 确定。
*   帧持续时间信息具有 UI 和 CPU 数据。在 API 24 之前(引入`FrameMetrics`时)，只有 UI 持续时间。
*   日志是从我开始抛出 RecyclerView 的时候开始的。当 recycle view 开始移动(“拖动”)和滚动(“稳定”)时，我们可以看到关于 UI 状态的信息。下面更多关于提供这种 UI 状态的内容。

# 真实世界数据

与最近的基准测试库不同，JankStats 的创建是为了提供来自用户设备的结果。在你的开发机器上调试问题是很棒的，但是对于那些你的应用被真实世界中的真实的人在非常不同的设备上，在非常不同的约束下使用的情况，这没有帮助。

JankStats 提供了一个 API 来检测您的应用程序，以提供您需要的性能数据和一个报告机制，以便您可以获得这些数据来进行离线上传和分析。

# 精神状态

最后(听好了:这是这个库真正的新事物)，JankStats 提供了一种方法来了解当性能问题发生时，应用程序中实际发生了什么。我们经常听到的抱怨是，现有的工具、仪表板和方法没有为您提供足够的*上下文*来处理您的用户可能会看到的性能问题。

例如，FrameMetrics API(在 API 24 中引入，在 JankStats 中内部使用)可以告诉您绘制帧需要多长时间，您可以从中获得 jank 信息。但是它不能告诉你当时你的应用程序里发生了什么。当您尝试检测您的代码并将其与 FrameMetrics 或其他性能度量工具集成时，这个问题就留给您自己去解决了。但是每个人都有足够的事情要做，而不必在内部构建这种基础设施，所以 jank 通常不被测量，性能问题继续存在。

同样，Android Vitals 仪表板可以告诉你你的应用程序有性能问题，但它不能告诉你当这些问题发生时你的应用程序正在做什么。所以很难知道如何处理这些信息。

JankStats 引入了`PerformanceMetricsState` API，这是一组简单的方法，允许您通过成对的`String`随时告诉系统您的应用程序中正在发生什么。例如，您可能希望记录某个特定的`Activity`或`Fragment`何时处于活动状态，或者某个`RecyclerView`何时正在滚动。

例如，以下是来自 JankStats 示例的代码，显示了如何检测 RecyclerView 以向 JankStats 提供此信息:

```
val scrollListener = object : RecyclerView.OnScrollListener() {
  override fun onScrollStateChanged(recyclerView: RecyclerView,
                                    newState: Int)
  {
    val metricsState = metricsStateHolder?.state ?: return 
      when (newState) {
        RecyclerView.SCROLL_STATE_DRAGGING -> {
          metricsState.addState("RecyclerView", "Dragging")
        }
        RecyclerView.SCROLL_STATE_SETTLING -> {
          metricsState.addState("RecyclerView", "Settling")
        }
        else -> {
          metricsState.removeState("RecyclerView")
        }
     }
  }
}
```

这个状态可以从应用程序中的任何地方注入(甚至可以从另一个库中注入)，当 JankStats 报告结果时，它会被选中。这样，当您从 JankStats 获得报告时，您不仅会发现每一帧花费了多长时间，还会发现用户在该帧中做了什么，这可能是一个影响因素。

# 资源

这里有一些资源供您了解更多关于 JankStats 的信息:

**AndroidX 项目** : JankStats 在 AndroidX 的 [androidx.metrics 库中。](https://developer.android.com/jetpack/androidx/releases/metrics#1.0.0-alpha01)

**文档**:我们的开发者网站有一个新的[开发者指南](https://developer.android.com/studio/profile/jankstats)，描述了如何使用 JankStats。

**示例代码**:这个[项目](https://github.com/android/performance-samples/tree/main/JankStatsSample)有一些示例，展示了如何实例化和监听 JankStats 对象，以及如何用重要的 UI 状态信息来检测您的应用程序:

[](https://github.com/android/performance-samples/tree/main/JankStatsSample) [## 主 android 上的 performance-samples/jankstats sample/performance-samples

### 这个示例项目展示了如何使用 JankStats 库。它包括多个简单的示例活动…

github.com](https://github.com/android/performance-samples/tree/main/JankStatsSample) 

**bug，bug，bug**:如果你对库有任何问题，或者有 API 请求，请[提交 bug](https://issuetracker.google.com/issues/new?component=1109743&template=1621342) 。

# Alpha -> 1.0

JankStats 刚刚推出了它的第一个 alpha 版本，这意味着“我们认为这是对 1.0 版本有意义的 API 和功能，但请试用并让我们知道。”

未来我们还想对 JankStats 做一些其他的事情，包括添加某种聚合机制，甚至与现有的上传服务同步。但是我们想把第一个版本和基本的管道系统一起推出来，看看你如何使用它，以及你还想看到什么。我们希望它在目前的基本状态下是有用的；仅仅是能够方便地检测和记录 UI 状态信息就应该比没有这种能力要好。

所以[得到](https://developer.android.com/jetpack/androidx/releases/metrics#1.0.0-alpha01)它，玩玩它，[让我们知道你是否有任何问题](https://issuetracker.google.com/issues/new?component=1109743&template=1621342)。而且最重要的是；找到并修复那些性能问题！你的用户在等你——不要让他们等太久！