# 在 Android 中构建视频播放器应用程序(第 4 / 5 部分)

> 原文：<https://medium.com/androiddevelopers/building-a-video-player-app-in-android-part-4-5-c69f12b49143?source=collection_archive---------8----------------------->

## 使用 ExoPlayer、播放列表、媒体会话、音频聚焦、画中画

![](img/2eed2a5c9bda533975efea4a4131b83e.png)

本系列文章的目标是让您开始使用 ExoPlayer 构建一个简单但功能丰富的视频播放器应用程序(支持播放列表、媒体会话、音频焦点和画中画)。

这是 5 部分系列的第 4 部分，包括:

*   [ExoPlayer 简介](/@nazmul/building-a-video-player-app-in-android-part-1-5-d95770ef762d)
*   [ExoPlayer 播放列表、用户界面定制和事件](/@nazmul/building-a-video-player-app-in-android-part-2-5-e5a5392879fa)
*   【ExoPlayer 的 MediaSession 连接器扩展
*   支持音频聚焦(**本文**)
*   [支持 PIP](/@nazmul/building-a-video-player-app-in-android-part-5-5-725c1ec2557a)

本系列的前一篇文章介绍了将 ExoPlayer 实例连接到 MediaSession 所需的步骤。这篇文章详细介绍了在应用程序中支持音频聚焦的细节。

# 音频焦点

Android 允许许多应用程序通过自动混合所有音频流来同时播放音频。然而，如果用户听到多个应用程序同时播放音频，就会产生干扰。这就像许多人试图同时与一个用户交谈，其结果是他们无法真正听到或注意到任何一个人。为了避免这一点，Android 提供了一个 [API](https://developer.android.com/guide/topics/media-apps/audio-focus.html) ，允许应用程序共享音频焦点，在任何给定时间只有一个应用程序可以保持音频焦点。

您可以从以下来源了解有关音频焦点的详细信息以及如何实现它。

*   [了解媒体上的音频焦点文章](/google-developers/audio-focus-1-6b32689e4380)。这篇文章深入探讨了什么是音频聚焦，什么是闪避，什么是音频属性，以及你应该为你的应用考虑哪些重要的用例。
*   [使用音频聚焦的 Android 示例应用](https://github.com/googlesamples/android-MediaBrowserService)。有一个处理音频焦点的`[AudioFocusHelper](https://github.com/googlesamples/android-MediaBrowserService/blob/ed830016fdf702c65698924b5d617d496e08606e/Application/src/main/java/com/example/android/mediasession/service/PlayerAdapter.java)` [类](https://github.com/googlesamples/android-MediaBrowserService/blob/ed830016fdf702c65698924b5d617d496e08606e/Application/src/main/java/com/example/android/mediasession/service/PlayerAdapter.java)(用 Java 编写)。

# 使用 ExoPlayer 实现 Kotlin

在 Kotlin 中很容易使用委托模式来创建一个子类，该子类使用超类的一个实例来提供所有继承的行为。这样，您就可以只关注子类中的差异，而无需编写任何样板代码。下面的代码展示了如何扩展`SimpleExoPlayer`类来处理音频焦点。您可以像使用`SimpleExoPlayer`一样使用这个类，通过传递您的应用程序所需的音频焦点类型(使用`AudioAttributes`)。

下面是为播放音乐的应用程序创建音频属性的代码片段(视频文件的音轨)。

下面是扩展了`SimpleExoPlayer`的`AudioFocusWrapper`类的代码片段。

# 画中画和音频焦点

到目前为止，我们的应用程序不能在后台运行。一旦任何其他应用程序启动，或者用户切换到主屏幕，播放就会停止。因此，在这种情况下，音频焦点并没有多大用处。然而，一旦你为应用程序添加了将自己最小化到一个小窗口并在其他应用程序运行时播放媒体的功能，音频焦点就变得非常重要。

想象一下这个场景，你启动视频播放器应用程序并开始播放视频，然后将其最小化为 PIP 窗口，然后启动 YouTube。此时，用户可以看到 YouTube 和视频播放器应用程序。

*   当您选择在 YouTube 应用程序中播放视频时，这应该会暂停示例应用程序中的播放。
*   当您选择在示例应用程序中播放视频时，这应该会暂停视频应用程序中的播放。

我们将在本系列的下一篇(也是最后一篇)文章中探讨这个问题👍。

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