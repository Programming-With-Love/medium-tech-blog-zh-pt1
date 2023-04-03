# 在 Android 中构建视频播放器应用程序(第 3 / 5 部分)

> 原文：<https://medium.com/androiddevelopers/building-a-video-player-app-in-android-part-3-5-19543ea9d416?source=collection_archive---------5----------------------->

## 使用 ExoPlayer、播放列表、媒体会话、音频聚焦、画中画

![](img/2eed2a5c9bda533975efea4a4131b83e.png)

本系列文章的目标是让您开始使用 ExoPlayer 构建一个简单但功能丰富的视频播放器应用程序(支持播放列表、媒体会话、音频焦点和画中画)。

这是 5 部分系列的第 3 部分，包括:

*   [ExoPlayer 简介](/@nazmul/building-a-video-player-app-in-android-part-1-5-d95770ef762d)
*   [ExoPlayer 播放列表、用户界面定制和事件](/@nazmul/building-a-video-player-app-in-android-part-2-5-e5a5392879fa)
*   ExoPlayer 的 MediaSession 连接器扩展(**本文**)
*   [支持音频聚焦](/@nazmul/building-a-video-player-app-in-android-part-4-5-c69f12b49143)
*   [支持 PIP](/@nazmul/building-a-video-player-app-in-android-part-5-5-725c1ec2557a)

本系列的前一篇文章介绍了通过连接`MediaSource`创建播放列表所需的步骤，以及定制 ExoPlayer 播放控制 UI 的细节。本文将详细介绍如何使用 MediaSession 连接器扩展来无缝使用应用程序中的 MediaSession 功能。

# 媒体会话概述

MediaSession 允许播放器(运行您的媒体应用程序)中的信息暴露给 Android 操作系统的其他部分。

*   诸如播放器当前是否暂停、正在播放或者加载了什么媒体之类的事情都通过 MediaSession 广播到操作系统的其他部分、其他应用或系统组件。这就是如何将当前正在播放的内容及其播放状态发送给 [MediaStyle 通知](/google-developers/migrating-mediastyle-notifications-to-support-android-o-29c7edeca9b7)或感兴趣的蓝牙设备。甚至是谷歌助手。
*   其他应用程序和操作系统也可以通过绑定到 MediaSession 的`[MediaController.TransportControls](https://developer.android.com/reference/android/media/session/MediaController.TransportControls.html)`操作播放器(在您的应用程序中运行)。这使得蓝牙耳机、谷歌助手甚至其他应用程序上的媒体按钮能够播放、暂停或跳过媒体应用程序中的歌曲。

总之，MediaSession 有两个用途:发布关于应用程序中正在播放的当前媒体的元数据，以及允许其他组件(操作系统和其他应用程序)控制播放(在应用程序中)。蓝牙设备、Android Auto 和 Wear 甚至谷歌助手都使用元数据。除了操作系统之外，回放控制还可以作为元数据使用(这样有线耳机的按钮按键就可以转换成应用程序可以使用的命令)。

关于 MediaSession 的更多细节，这里有一些资源。

*   [了解 MediaSession 媒体篇](/google-developers/understanding-mediasession-part-1-3-e4d2725f18e4)
*   [与 d.android.com 媒体会议合作](https://developer.android.com/guide/topics/media-apps/working-with-a-media-session.html)
*   [使用 MediaSession 的 Android 示例代码](https://github.com/googlesamples/android-MediaBrowserService)

# 使用 MediaSession 连接器扩展

确保将[梯度依赖](https://github.com/google/ExoPlayer/tree/release-v2/extensions/mediasession)添加到您的`build.gradle`文件中。

```
ext { ver = “2.7.0” }
dependencies {
  implementation 
    “com.google.android.exoplayer:extension-mediasession:$ver”
}
```

然后，您可以创建会话并将其附加到播放器(在您的`VideoActivity`中)，如下所示。

**关于 Java varargs 和 Kotlin 的注意事项** — ExoPlayer 和 MediaSession 连接器扩展是用 Java 编写的，`[setPlayer()](https://goo.gl/M7pwmg)`方法接受 varargs 作为第三个参数。当从 Kotlin 调用这个方法时，如果没有传递第三个参数，那么在使用 Java 时，一切都会如您所料。然而，如果您将 null 作为第三个参数传递，那么这将抛出一个`NullPointerException`。如果您有一个想要传递的参数列表，那么您必须使用[扩展操作符*](https://goo.gl/y13f9G) 来将一个参数列表传递给 Java varargs 参数。这里有一个例子。

```
val uriList = mutableListOf<MediaSource>()
val mediaSource = ConcatenatingMediaSource(*uriList.toTypedArray())
```

这样做将启用基本的一组[回放动作](https://developer.android.com/reference/android/media/session/PlaybackState.html#constants) ( `ACTION_PLAY_PAUSE`、`ACTION_PLAY`、`ACTION_PAUSE`、`ACTION_SEEK_TO`、`ACTION_FAST_FORWARD`、`ACTION_REWIND`、`ACTION_STOP`)。这意味着连接器将能够处理通过 MediaSession 从媒体按钮生成的这些操作，例如蓝牙耳机中允许暂停、播放、快进和快退的按钮。

然而，如果你想提供对播放列表的控制，那么你也必须告诉连接器通过添加一个`[QueueNavigator](http://google.github.io/ExoPlayer/doc/reference/index.html?com/google/android/exoplayer2/ext/mediasession/MediaSessionConnector.QueueNavigator.html)`来处理`ACTION_SKIP_*`命令。这是一个可能的例子。

使用这个例子，你也可以改变创建你的 ExoPlayer 和它的播放列表的代码，这样播放列表就可以从这个相同的'`MediaCatalog`'列表对象中加载。加载播放列表的代码可能如下所示。

您还可以告诉连接器，除了上述操作之外，您还想处理其他 MediaSession 操作。您可以在这篇[中型文章](/google-exoplayer/the-mediasession-extension-for-exoplayer-82b9619deb2d)中找到关于以下各项的更多信息。

*   `MediaSessionConnector.PlaybackPreparer` —这允许你处理像`ACTION_PREPARE_FROM_SEARCH`和`ACTION_PREPARE_FROM_URI`这样的动作，如果你想与 Android Auto 或 Google Assistant 集成，这是必需的。有关允许 Google Assistant 通过 MediaSession 控制您的媒体应用程序的更多信息，请参考这篇关于[developers.android.com](https://developer.android.com/guide/topics/media-apps/interacting-with-assistant.html)的文章。
*   `MediaSessionConnector.PlaybackController` —这允许您处理基本的回放控制器动作，例如`ACTION_PLAY_PAUSE`和`ACTION_SEEK_TO`。这是可选的，如果你不用这个来拦截这样的呼叫，就用一个`[DefaultPlaybackController](https://google.github.io/ExoPlayer/doc/reference/index.html?com/google/android/exoplayer2/ext/mediasession/DefaultPlaybackController.html)`。
*   `MediaSessionConnector.QueueEditor` —这允许你处理`ACTION_SET_RATING`和其他`MediaSessionCompat.FLAG_HANDLES_QUEUE_COMMAND`命令。`TimelineQueueEditor`是与`DynamicConcatenatingMediaSource`一起工作的实现。
*   `MediaSessionConnector.CustomActionProvider` —处理您希望在应用程序中提供的任何自定义操作，例如提供一种方法来控制应用程序中的[重复模式](https://google.github.io/ExoPlayer/doc/reference/com/google/android/exoplayer2/ext/mediasession/RepeatModeActionProvider.html)。

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
*   [为什么是 ExoPlayer？](/google-exoplayer/exoplayer-2-x-why-what-and-when-74fd9cb139)
*   [exo player v 2 . 6 . 1 的最新变化](/google-exoplayer/exoplayer-2-6-1-whats-new-a9e54bffffc5)

**媒体会话，音频焦点**

*   [exo player 的 MediaSession 扩展](/google-exoplayer/the-mediasession-extension-for-exoplayer-82b9619deb2d)
*   [什么是 MediaSession](/google-developers/understanding-mediasession-part-1-3-e4d2725f18e4)
*   [什么是音频焦点](/google-developers/audio-focus-1-6b32689e4380)

**破折号，HLS**

*   [破折号，HLS](https://goo.gl/r9fXXf)
*   [Dash 优于 HLS 等](https://goo.gl/SNvMgQ)

**画中画**

*   [奥利奥中的画中画模式](/google-developers/making-magic-moments-with-picture-in-picture-e02964bf75ae)