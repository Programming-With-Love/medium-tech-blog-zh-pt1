# 在 Android 中构建视频播放器应用程序(第 1 / 5 部分)

> 原文：<https://medium.com/androiddevelopers/building-a-video-player-app-in-android-part-1-5-d95770ef762d?source=collection_archive---------1----------------------->

## 使用 ExoPlayer、播放列表、媒体会话、音频聚焦、画中画

![](img/2eed2a5c9bda533975efea4a4131b83e.png)

Android 媒体 API 允许您创建丰富的媒体体验，让您的用户沉浸在应用程序的音频或视频内容中。它们还为外部控制的蓝牙耳机、汽车音频系统、有线耳机，甚至谷歌助手和 Android Auto 提供控制。

本系列文章的目标是让您开始使用 ExoPlayer 和 MediaSession 构建一个简单但功能丰富的视频播放器应用程序。

该应用程序将支持以下功能:

*   从本地存储(APK 上的“资源”文件夹)或远程 HTTP 源回放视频(甚至音频文件)。
*   支持播放列表，这样你就可以把一系列视频串在一起，一个接一个地播放，并在它们之间跳转。
*   支持 MediaSession，因此外部蓝牙耳机可以控制您的媒体(播放、暂停、跳到下一个等)并查看当前正在播放的媒体(就像在您的车载蓝牙耳机上一样)。
*   支持音频聚焦，这样你就尊重 Android 的音频聚焦系统，如果有别的东西在播放，或者用户接到电话，就暂停播放。
*   Android Oreo 支持画中画(PIP)模式，因此当您使用其他应用程序时，您的应用程序的视频播放可以在最小化的窗口中继续..

我们将在这个应用程序中使用 Kotlin，信不信由你，整个应用程序将由 4 个大小适中的 Kotlin 文件构建而成！😃

这是 5 部分系列的第一部分，包括:

*   ExoPlayer 简介(**本文**)
*   [ExoPlayer 播放列表、用户界面定制和事件](/@nazmul/building-a-video-player-app-in-android-part-2-5-e5a5392879fa)
*   【ExoPlayer 的 MediaSession 连接器扩展
*   [支持音频聚焦](/@nazmul/building-a-video-player-app-in-android-part-4-5-c69f12b49143)
*   [支持 PIP](/@nazmul/building-a-video-player-app-in-android-part-5-5-725c1ec2557a)

# 使用 ExoPlayer 概述

为了使用 ExoPlayer 通过网络或从您的 APK 播放视频文件，您需要创建一个`SimpleExoPlayer`实例和一个`MediaSource`。您还需要一个包含一个实际呈现播放器加载的视频内容的`PlayerView`的`Activity`。

**玩家**

至少，为了创建一个`SimpleExoPlayer`,你需要提供一个音轨选择器(它根据带宽、设备、功能、语言等选择从你的媒体源加载音频、视频或文本的哪个音轨)。

**玩什么？**

您需要告诉播放器要加载什么媒体，以及从哪里加载。`MediaSource`允许你这样做。媒体源被表示为`Uri` s，它可以指向在你的 APK 的`assets`文件夹中或者通过 HTTP 的文件(`mp3`、`mp4`、`webm`、`mkv`等)。您提供的`Uri`被`MediaSource`用来实际加载和准备播放内容。`ExtractorMediaSource`(它实现了`MediaSource`)允许您处理这些源代码和格式。

> 注意—对于自适应格式，您可以使用`DashMediaSource`(对于 DASH 信号源)、`SsMediaSource`(对于 SmoothStreaming 信号源)和`HlsMediaSource`(对于 HLS 信号源)。

如果您通过 HTTP 加载媒体文件，则需要将以下权限添加到您的`AndroidManifest.xml`文件中。访问 APK ( `assets`文件夹)中的文件不需要添加权限。

```
<uses-permission android:name=”android.permission.INTERNET” />
```

**在哪里渲染回放？**

然后，您必须将播放器连接到一个`PlayerView`，它将视频呈现到您的 UI，并提供音频/视频播放的 UI 控件。最好在开始准备源代码(这是下一步)之前完成这项工作。如果您将视图附加到播放器，那么在您准备好源之后，媒体将被加载并可能在播放器有输出表面之前播放。

您还必须准备好播放器，让它开始加载数据(因为在开始播放之前，可能需要一段时间来通过网络缓冲数据)。您还必须将`playWhenReady`设置为 true，这将告诉 ExoPlayer 进行播放。

```
playWhenReady = true  -> PLAY (start after enough data is buffered)
playWhenReady = false -> PAUSE
```

要在您的应用程序中添加对 ExoPlayer 的支持，请确保在`build.gradle`文件中添加以下行。有关仅包含特定 ExoPlayer 组件的更多信息，请参考[开发者指南](https://google.github.io/ExoPlayer/guide.html)和这篇[中型文章](/google-exoplayer/exoplayers-new-modular-structure-a916c0874907)。

```
ext { ver = “2.7.0” }
dependencies {
  implementation “com.google.android.exoplayer:exoplayer-core:$ver”
  implementation “com.google.android.exoplayer:exoplayer-ui:$ver”
}
```

添加 ExoPlayer 依赖项不会显著增加 APK 的大小。在运行 Proguard 之前，您可能会看到 APK 的大小增加了大约 100K。在应用 ProGuard 之后，您可以预期这个大小会减小。

# 创建 ExoPlayer 实例

这段代码展示了如何使用默认选项创建一个播放器，并将其附加到一个`[PlayerView](https://goo.gl/eroZVT)`(必须在您想要显示视频的`Activity`的 XML 布局中声明)。

应该在活动的布局 XML 中声明`PlayerView`。例如:

# 使用 ExoPlayer 从 APK 本地加载文件

`[DefaultDataSource](https://google.github.io/ExoPlayer/doc/reference/com/google/android/exoplayer2/upstream/DefaultDataSource.html)`允许通过以下`Uri`加载本地文件:

*   `file:///`
*   `asset:///`
*   `content:///`
*   `rtmp`
*   `data`
*   `http(s)`

您可以通过以下方式从资产中加载文件(您可以在`assets`文件夹下创建嵌套文件夹):

*   `Uri.parse(“file:///android_asset/video/video.mp4”)`
*   `Uri.parse(“asset:///video/video.mp4”)`

请注意:

*   ExoPlayer 不允许使用`Uri.parse(“android.resource://${packageName}/${R.raw.id}”)`从`res`文件夹加载文件。还有，Android 不允许你在`res`文件夹中添加文件夹(不像`assets`)。
*   你可以使用`[RawResourceDataSource](https://google.github.io/ExoPlayer/doc/reference/com/google/android/exoplayer2/upstream/RawResourceDataSource.html)`构建一个`Uri`，它指向`res`文件夹中的一个`resource id`，例如:`RawResourceDataSource.buildRawResourceUri(R.raw.my_media_file)`。

# 释放 ExoPlayer 资源

播放完毕后，请务必停止播放器，因为它会消耗网络、内存和系统编解码器等资源。编解码器是手机上的全球共享资源，根据特定的手机和操作系统版本，它们的数量可能有限，因此在不使用它们时将其释放非常重要。如果您愿意，下面的代码允许您再次重用同一个 ExoPlayer 实例。

这将释放玩家持有的所有资源(编解码器、`MediaSources`等)。为了再次使用播放器，调用`prepare(MediaSource)`并设置`playWhenReady`，如上面的列表所示。

`ExoPlayer.release()`是一个与`stop()`做同样事情的方法，除了一个例外:它也停止播放线程，防止 ExoPlayer 实例被重用。当播放器不再被使用时，例如在`Service.onDestroy()`或`Activity.onDestroy()`中，应该调用 Release。

# 活动生命周期集成

为了创建、使用和发布播放器，您必须集成 Android 活动生命周期。这里有一个简单的例子。

# 保存 onStart()和 onStop()之间的播放器状态

`PlayerState`数据类(如下所示)用于在第一次运行之前加载玩家的状态信息。当玩家被释放时，玩家的一些状态被保存到这个类的一个对象中。当一个新的播放器被创建时，这个简单的状态对象被用来配置播放器从实例先前停止的地方恢复。

从 UX 的角度来看，这意味着当你运行应用程序，播放一些媒体，并点击主页按钮，播放器资源被释放。当您切换回该应用程序时，播放器会再次初始化，并且会恢复以前的状态信息，以便用户可以从他们停止的地方继续播放(以前播放的位置，以及他们正在使用的播放列表中的项目，这是一个窗口索引)。

在释放播放器的资源之前，播放器的`currentWindowIndex`、`currentPosition`、`playWhenReady`以及播放列表或媒体项信息被保存到`PlayerState`对象中。一旦播放器被重新初始化，状态就被恢复。下面是来自`PlayerHolder`类的方法，演示了如何做到这一点。

# 对玩家创造有更多的控制

你也可以使用不同的`ExoPlayerFactor.newSimpleInstance(…)`工厂方法来定制你的播放器。例如，即使在使用`DefaultLoadControl`类时，你也可以改变 ExoPlayer 的缓冲策略以更好地满足你的需求。

对于更复杂的用例，您可以为传递给`ExoPlayerFactory.newSimpleInstance(…)`工厂方法的所有参数提供自己的实现，这为您使用 ExoPlayer 做什么提供了很大的灵活性。

# GitHub 上的源代码

通过遵循这 5 篇文章，你应该能够创建一个视频播放器应用程序，类似于我们已经创建的这个示例(你也可以从 Android Studio 获得)。

[](https://github.com/googlesamples/android-VideoPlayer) [## 谷歌样品/安卓视频播放器

### 在 GitHub 上创建一个帐户，为 android 视频播放器的开发做出贡献。

github.com](https://github.com/googlesamples/android-VideoPlayer) 

# 进一步学习的资源

**ExoPlayer**

*   [IO17 ExoPlayer codelab](https://codelabs.developers.google.com/codelabs/exoplayer-intro/#0)
*   [IO14 ExoPlayer 介绍视频](https://www.youtube.com/watch?v=6VjF638VObA)
*   [IO17 ExoPlayer 会话视频](https://www.youtube.com/watch?v=jAZn-J1I8Eg)
*   [为什么选择 ExoPlayer？](/google-exoplayer/exoplayer-2-x-why-what-and-when-74fd9cb139)
*   [exo player v 2 . 6 . 1 的最新变化](/google-exoplayer/exoplayer-2-6-1-whats-new-a9e54bffffc5)

**媒体会话，音频焦点**

*   [exo player 的 MediaSession 扩展](/google-exoplayer/the-mediasession-extension-for-exoplayer-82b9619deb2d)
*   [什么是媒体会话](/google-developers/understanding-mediasession-part-1-3-e4d2725f18e4)
*   [什么是音频焦点](/google-developers/audio-focus-1-6b32689e4380)

破折号，HLS

*   [破折号，HLS](https://goo.gl/r9fXXf)
*   [Dash 优于 HLS 等](https://goo.gl/SNvMgQ)

**画中画**

*   [奥利奥中的画中画模式](/google-developers/making-magic-moments-with-picture-in-picture-e02964bf75ae)