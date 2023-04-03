# Android 上运行时分析的严格模式

> 原文：<https://medium.com/androiddevelopers/strictmode-for-runtime-analysis-on-android-f8d0a2c5667e?source=collection_archive---------7----------------------->

![](img/5763e5a4793b024f962b85ed34a2ec0f.png)

让你的 Android 应用程序编译只是第一步:仅仅因为它能编译并不意味着当你运行你的应用程序时它也能工作。令人欣慰的是，Android 已经投资建立分析工具来捕捉一些常见的 Android 特有的问题，这些问题弥合了编译错误和运行时崩溃之间的差距。其中 [lint](http://developer.android.com/tools/debugging/improving-w-lint.html?utm_campaign=android_series_strictmode_012116&utm_source=medium&utm_medium=blog) 提供静态代码分析， [*StrictMode*](http://developer.android.com/reference/android/os/StrictMode.html?utm_campaign=android_series_strictmode_012116&utm_source=medium&utm_medium=blog) 相当于运行时:在线程或虚拟机级别分析你的应用，并提醒你潜在的问题。

尽管传统上用于与性能相关的检查，但也有许多检查专门围绕共享文件和访问网络的一些最佳实践。

# 严格模式基础

要了解 *StrictMode* 的功能以及如何在您的应用中使用它，我强烈建议观看我们之前在 *StrictMode* 上的[视频。在这里，我们讨论了 *StrictMode* 的多个](https://www.youtube.com/watch?v=oGrXdxpWgyY?utm_campaign=android_series_strictmode_012116&utm_source=medium&utm_medium=blog) [*ThreadPolicy*](http://developer.android.com/reference/android/os/StrictMode.ThreadPolicy.html?utm_campaign=android_series_strictmode_012116&utm_source=medium&utm_medium=blog) 和 [*VmPolicy*](http://developer.android.com/reference/android/os/StrictMode.VmPolicy.html?utm_campaign=android_series_strictmode_012116&utm_source=medium&utm_medium=blog) 配置选项，归结起来就是在运行时检测某些条件，然后以惩罚的方式对这些条件做出反应——无论是记录日志、闪烁屏幕还是完全关闭您的应用程序。

# VmPolicy 的 detectFileUriExposure()

当向其他应用程序发送文件时，其他应用程序能够真正读取您的文件**非常重要**。事实证明，*file://*uri 实际上是一种非常糟糕的方式。正如我们在[分享内容帖子](/@ianhlake/2e6db9d1368b)中解释的，使用 *file://* Uri 意味着存储权限，它们实际上永远不会跨用户工作(比如 Android for Work)。

[*detectFileUriExposure()*](http://developer.android.com/reference/android/os/StrictMode.VmPolicy.Builder.html?utm_campaign=android_series_strictmode_012116&utm_source=medium&utm_medium=blog#detectFileUriExposure())strict mode 检查(在 API 18 — Jellybean MR2 中添加)试图捕捉这些情况:在应用发出的任何*意图*中寻找*file://*uri，然后应用您为 *VmPolicy* 选择的惩罚。要记住的一点是，这是对*file://*uri 的全面禁止——尽管在你自己的应用程序的组件之间发送*file://*uri 在技术上是安全的，但这种检查还不能区分谁在接收*意图*。

# VmPolicy 的 detectCleartextNetwork()

安全性，尤其是用户数据的安全性，并不仅仅止于你的应用程序——保护与服务器的通信同样重要，坦率地说，保护你的应用程序建立的每个连接也同样重要——仅仅因为你信任你的无线接入点和网络，并不意味着对所有用户都是如此。

Android 6.0 Marshmallow 以[*detectCleartextNetwork()*](http://developer.android.com/reference/android/os/StrictMode.VmPolicy.Builder.html?utm_campaign=android_series_strictmode_012116&utm_source=medium&utm_medium=blog#detectCleartextNetwork())的形式添加了一个新的 StrictMode 检查——一个 StrictMode 检查，它可以确保来自你的应用程序的所有网络流量都是加密的。甚至还有一个单独的[*penaltyDeathOnCleartextNetwork()*](http://developer.android.com/reference/android/os/StrictMode.VmPolicy.Builder.html?utm_campaign=android_series_strictmode_012116&utm_source=medium&utm_medium=blog#penaltyDeathOnCleartextNetwork())，它将确保一旦检测到明文网络操作，你的应用就会被杀死(防止任何用户数据泄露，即使你不希望你的应用因为其他严格模式检查而被杀死)。

虽然这种检查对 IPv4 和 IPv6 流量都有效，无论是 TCP 还是 UDP，但是根据文档有一个警告:

> 它可能会出现误报，例如当使用 STARTTLS 协议或 HTTP 代理时。

因此，如果你的应用程序使用这些技术来处理网络流量，请记住这一点。

# 简单分析？我不这么认为

由于 StrictMode 在线程或 VM 级别上工作，它比大多数运行时分析处于更低的级别。虽然这有它的优点和缺点——它确实非常彻底，但是可用的惩罚是硬编码的和不灵活的。唉，目前的 API 中没有 StrictMode 的自定义处理程序。

但是不要把这种不灵活性当成丧钟——只要仔细考虑您使用的严格模式检查，并确保您只在调试版本上启用它们—*build config。调试*标志对这些情况很有帮助:

```
if (BuildConfig.DEBUG) {
  // Enable StrictMode
  StrictMode.setVmPolicy(new StrictMode.VmPolicy.Builder()
    .detectFileUriExposure()
    .detectCleartextNetwork()
    .penaltyLog()
    .penaltyDeath()
    .build());
}
```

# BuildBetterApps

关注 [Android 开发模式集](https://plus.google.com/collection/sLR0p?utm_campaign=android_series_strictmode_012116&utm_source=medium&utm_medium=blog)了解更多！

![](img/ede78edee0069962aa0daa7cc8c85f02.png)