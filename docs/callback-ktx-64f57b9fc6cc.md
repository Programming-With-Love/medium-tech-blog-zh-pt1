# 回拨-ktx

> 原文：<https://medium.com/google-developer-experts/callback-ktx-64f57b9fc6cc?source=collection_archive---------4----------------------->

![](img/57a9a087d2cff92fcb663fb7a5e774af.png)

Photo by [CJ Dayrit](https://unsplash.com/@cjred?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/s/photos/ideas?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

这篇博客是关于 [**图书馆**](https://github.com/sagar-viradiya/callback-ktx) 的，是在阅读了 [Chris Banes](https://medium.com/u/9303277cb6db?source=post_page-----64f57b9fc6cc--------------------------------) 关于 [**暂停浏览**](https://chris.banes.dev/suspending-views/) 的博客系列后产生的。这个博客背后的意图是揭示这个库，并从社区获得反馈。所以享受这一小段吧！:)

安卓❤️s 回电。所有的框架和 jetpack 库 API 都使用它来通知事件，但是当你有嵌套的回调时，它就变得混乱了。很难理解和衡量。此外，当您处理嵌套回调时，出现错误的几率非常高。

好的一面是，我们可以通过 Kotlin 的协程 API 轻松避免它。 [**callback-ktx**](https://github.com/sagar-viradiya/callback-ktx) 是试图将潜在的框架和基于 jetpack 回调的 API 包装成挂起的扩展函数。在多次回调的情况下，它暴露`Flow`来观察所有回调。

如果我解释将回调封装到挂起函数中会带来的所有好处，那就有点多余了，因为 Chris Banes [博客系列](https://chris.banes.dev/suspending-views/)已经很好地介绍了这些好处。如果你还没有读过，我建议你在继续读这本书之前先读一读。

将基于回调的 API 封装到挂起函数中并不困难。然而，具有挑战性的是要检查所有的框架和 jetpack APIs，并找出哪个 API 可以从中受益。让我们看看这个库现在能做什么。

## 这是什么？

目前，该库涵盖了来自 framework 和 jetpack 的以下 API。

*   动画
*   位置
*   回收视图
*   传感器
*   视角
*   小部件(文本视图)

让我们看一个观察位置更新的例子。

要得到最后一个位置，你需要做的就是调用`awaitLastLocation`，它是`[FusedLocationProviderClient](https://developers.google.com/android/reference/com/google/android/gms/location/FusedLocationProviderClient)`的一个扩展函数

Last location through callback-ktx

为了观察位置更新，它公开了位置流。

Location updates through callback-ktx

请注意，扩展需要生命周期所有者和位置请求。这使得流量生命周期感知，所以它不会在应用程序处于后台时触发位置更新。可以访问 GitHub 上的[库](https://github.com/sagar-viradiya/callback-ktx)查看更多例子。

在将回调封装到挂起函数中以避免潜在的内存泄漏和协程不必要的资源使用时，有两件重要的事情

1.  如果协程由于某种原因被取消，回调需要被取消注册。
2.  如果期望回调的异步操作被取消，则需要终止协程。

图书馆负责这两者。此外，回调扩展根据它们所属的类别被划分到不同的模块中。例如，所有的框架 API 都属于核心模块。任何与框架无关的东西都在单独的模块中。因此，根据需要，用户可以依赖特定的 maven 工件。

## 下一步是什么？

正如我前面提到的，使用这个库的最大挑战是找出所有可以受益的 API。我希望看到社区在提交新扩展、错误修复和优化方面的贡献。

[](https://github.com/sagar-viradiya/callback-ktx) [## GitHub-Sagar-vira diya/callback-ktx:Android 基于回调的 API 的扩展函数…

### Android 基于回调的 API 上的扩展函数允许在协程中以顺序方式编写它们…

github.com](https://github.com/sagar-viradiya/callback-ktx) 

请在下面的评论中告诉我你的想法或任何反馈。直到下次👋🏻