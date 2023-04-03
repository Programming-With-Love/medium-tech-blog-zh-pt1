# 使用按需模块进行本地开发和测试

> 原文：<https://medium.com/androiddevelopers/local-development-and-testing-with-fakesplitinstallmanager-57083e1840a4?source=collection_archive---------0----------------------->

![](img/42c66688dff04f70825601a33d4d71b3.png)

Header graphic by [Virginia Poltrack](https://medium.com/u/224e59676537?source=post_page-----57083e1840a4--------------------------------)

在过去的几个月里，我一直致力于解决我从那些在应用程序中使用[动态功能模块](https://developer.android.com/guide/app-bundle/dynamic-delivery)的开发者那里得到的一些反馈。一个常见的主题是缺乏良好的**测试支持**以及他们的**开发速度**受到必须将工件上传到 Play Store 才能尝试模块下载和安装流程的影响。

去年，在 Android Dev Summit 上，我和一些从事 Play 动态交付的同事给[做了一次演讲](https://www.youtube.com/watch?v=Nt8zsxNMFNY&vl=en)，在演讲中，我们梳理了 Play 核心库中一个名为`FakeSplitInstallManager`的新功能，该功能使得在本地执行一些操作成为可能，比如安装按需模块。

虽然`FakeSplitInstallManager`类已经推出有一段时间了(从 Play Core 1.6.4 开始)，但它花了一些时间来让开发者体验正确，并对`[bundletool](https://developer.android.com/studio/command-line/bundletool)`进行自动化过程所需的最终更改。

今天，您可以在我们的一个新样品中了解它的工作原理:

[](https://github.com/android/app-bundle-samples/tree/master/DynamicFeatureNavigation) [## Android/应用捆绑包-示例

### 了解如何使用动态功能模块导航。此示例展示了动态功能的以下用途…

github.com](https://github.com/android/app-bundle-samples/tree/master/DynamicFeatureNavigation) 

以前，每当我们发布展示可下载动态功能模块使用的示例时，我们都必须包含开发人员的说明，以便
1)更改示例应用程序的包名，
2)在您的 Play Console 帐户上创建一个应用程序条目，
3)将包上传到[内部应用程序共享](https://support.google.com/googleplay/android-developer/answer/9303479?hl=en)或内部测试轨道…如果您只是想体验使用 API，这非常麻烦！

**如果您只是想快速地使用这个示例，看看它在本地是如何工作的，那么以上所有这些都不再需要了。**

这要归功于 [bundletool 版本 0.13.0](https://github.com/google/bundletool/releases) 中新增的一个`--local-testing`标志。

当应用于`bundletool build-apks`命令时，该标志指示`bundletool`向生成的 APK 清单添加一小块元数据。然后，您可以通过两个简单的步骤来使用它:

*   用`bundletool install-apks`在测试设备(或模拟器)上安装你的应用。这将触发所有剥离 apk 到设备存储位置的额外推送。
*   运行应用程序，使用常用的工厂方法`[SplitInstallManagerFactory.create(Context)](https://developer.android.com/reference/com/google/android/play/core/splitinstall/SplitInstallManagerFactory)`创建一个`[SplitInstallManager](https://developer.android.com/reference/com/google/android/play/core/splitinstall/SplitInstallManager)`。

返回的实例现在自动检测本地测试元数据的存在，并在幕后使用`FakeSplitInstallManager`，从本地文件安装请求的模块，而不是从 Play 下载。**您必须使用 Play Core 1.6.5 或更高版本才能自动执行此操作。**

为了使它更简单，我在示例中添加了一个新的 Gradle 任务，以简化我提到的 bundletool 命令的运行:

```
./gradlew installApkSplitsForTestDebug
```

不幸的是，这一功能仍然无法从 Android Studio GUI 或直接从 Gradle 的 Android 插件中获得。您在这里看到的任务只是一个快速的破解，相当于在您的项目上运行以下命令:

```
./gradlew bundleDebugbundletool build-apks --overwrite **--local-testing** --bundle *path/to/bundle.aab* --output *path/to/apkset.apks*bundletool install-apks --apks *path/to/apkset.apks*
```

> *如果你想知道我是如何添加这些任务的，看看* [*构建脚本源码*](https://github.com/android/app-bundle-samples/blob/master/DynamicFeatureNavigation/app/build.gradle.kts#L65) *。* ***更新:看看*** [***我们更新的 PlayCoreKTX 示例***](https://github.com/android/app-bundle-samples/tree/main/PlayCoreKtx#usage) ***看看它是如何使用新的 Android Gradle 插件 API 完成的。***

**请注意**:不是所有的`SplitInstallManager`功能都在本地测试模式下或者直接调用`FakeSplitInstallManager`时实现(比如写测试时)。

您可以:

*   请求、监控和取消模块/语言安装。
*   [处理安装错误](https://developer.android.com/guide/playcore/testing-fakesplitinstallmanager#FakeSplitInstallManager)。
*   使用 [SplitCompat](https://developer.android.com/reference/com/google/android/play/core/splitcompat/SplitCompat) 立即访问模块。
*   使用 [Play Core KTX](https://developer.android.com/reference/com/google/android/play/core/ktx/package-summary) (Kotlin 扩展)。

由于技术限制，不支持什么(例如，我们目前没有计划添加此功能):

*   正在卸载模块。
*   请求延期安装/卸载。
*   为大型模块启动确认对话框[。](https://developer.android.com/guide/playcore#obtain_confirmation)
*   多个安装会话正在运行。

如需进一步阅读，请参考更新的[文档](https://developer.android.com/guide/playcore/testing-fakesplitinstallmanager)。

顺便说一下，我在本文中链接的[示例](https://github.com/android/app-bundle-samples/tree/master/DynamicFeatureNavigation)展示了我与 [Ben Weiss](https://medium.com/u/65fe4f480b1c?source=post_page-----57083e1840a4--------------------------------) 一起工作的另一个库，当使用[导航架构组件](https://developer.android.com/guide/navigation)时，它可以更容易地将按需模块集成到您的应用程序中。

动态特性导航器是基本导航库之上的一个扩展，它允许您定义来自动态特性模块的导航目的地(例如片段、活动)。

如果模块可以按需安装，那么库会使用 Play Core API 下载并安装它，甚至提供一个默认的进度 UI。同时，它可以根据您的需要进行定制。您可以在新的[文档页面](https://developer.android.com/guide/navigation/navigation-dynamic)中了解更多信息。

该库目前处于 alpha 版本，我们欢迎您的反馈。