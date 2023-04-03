# 在 Android 中构建一个简单的音频应用程序(第 3/3 部分)

> 原文：<https://medium.com/androiddevelopers/building-a-simple-audio-app-in-android-part-3-3-ead4a0e10673?source=collection_archive---------3----------------------->

## 与搜索栏同步

![](img/1391c0629e8d0926ef82ca2f50204131.png)

# 介绍

本系列文章的目标是通过创建一个名为“简单的 MediaPlayer”的非常基本的音频播放应用程序，让您开始使用 Android `MediaPlayer` API。这是 3 部分系列的最后一部分，包括:

1.  [MediaPlayer 简介](/@nazmul/building-a-simple-audio-app-in-android-part-1-3-c14d1a66e0f1)
2.  [构建应用](/@nazmul/building-a-simple-audio-app-in-android-part-2-3-a514f6224b83)
3.  **与 SeekBar** 同步(*本文*)

# 第 3/3 部分—与搜索栏同步

本文将涵盖以下内容:

*   如何用`SeekBar`同步音频播放位置和持续时间。
*   如何在播放过程中使用`SeekBar`跳转到音频中的不同时间位置。

# app 长什么样，源代码在哪里？

请查看[系列的第 2 部分](/@nazmul/introduction-to-mediaplayer-part-2-3-a514f6224b83)以查看应用程序的运行情况，并在 [GitHub](https://github.com/googlesamples/android-SimpleMediaPlayer) 上获取源代码。

[](https://github.com/googlesamples/android-SimpleMediaPlayer) [## Google samples/Android-简单媒体播放器

### 在 GitHub 上创建一个帐户，为 android-SimpleMediaPlayer 开发做出贡献。

github.com](https://github.com/googlesamples/android-SimpleMediaPlayer) 

# 与搜索栏同步

该应用的用户界面也有一个`[SeekBar](https://developer.android.com/reference/android/widget/SeekBar.html)`。这做了两件事:

1.  它在媒体播放过程中向用户提供视觉反馈(通过在音频播放时向前移动`SeekBar`中的搓擦器)。
2.  允许用户通过触摸和拖动搓擦器跳到媒体中的任何特定时间位置。这最终会调用`MediaPlayer`中的`seekTo()`来改变其音频播放位置。

# 1.Scrubber 更新 UI 以反映播放位置— PlaybackInfoListener 接口

为了让`[MediaPlayerHolder](https://github.com/googlesamples/android-SimpleMediaPlayer/blob/53fbbbafc9ab14d51041fc52ced2a6926484c274/app/src/main/java/com/example/android/mediaplayersample/MediaPlayerHolder.java)`向 UI(在`[MainActivity](https://github.com/googlesamples/android-SimpleMediaPlayer/blob/53fbbbafc9ab14d51041fc52ced2a6926484c274/app/src/main/java/com/example/android/mediaplayersample/MainActivity.java)`中)提供回放位置正在进行的更新，使用了`[PlaybackInfoListener](https://github.com/googlesamples/android-SimpleMediaPlayer/blob/53fbbbafc9ab14d51041fc52ced2a6926484c274/app/src/main/java/com/example/android/mediaplayersample/PlaybackInfoListener.java)`接口。`MainActivity`实现这个回调。它允许`MediaPlayerHolder`和`MainActivity`去耦。

1.  `MediaPlayerHolder`报告它的进度和持续时间，而不关心 UI 如何显示这些信息。
2.  `MediaPlayerHolder`还报告其当前状态(可以是`PLAYING`、`PAUSED`、`RESET`、`COMPLETED`)。
3.  `MainActivity`负责通过`SeekBar`在 UI 中显示持续时间和进度。

# 1.1.设置搜索栏最大值以匹配播放持续时间

`SeekBar`用于显示沿水平线的数值范围。为了配置`SeekBar`(也就是`MainActivity`)，我们需要告诉它它的最大值应该是多少。在我们简单的应用程序中，我们将设置音轨持续时间的最大值，以毫秒为单位。在`MediaPlayer`被创建并且 MP3 文件被加载后，这在`MediaPlayerHolder`中发生如下。

# 1.2.随着音频回放的进行，提供视觉反馈

随着音频播放的进行，`SeekBar’s` `setProgress(time)`方法必须根据播放过程中经过的时间来调用。没有可以连接到`MediaPlayer`的监听器来获取这些信息，所以我们必须投票。有两种轮询策略——使用`Handler`或`Executor`。这个应用程序使用了一个`Executor`，因为它使代码更容易阅读。

An `[SingleThreadScheduledExecutor](https://docs.oracle.com/javase/7/docs/api/java/util/concurrent/Executors.html#newSingleThreadScheduledExecutor())`每 500 ms 调度一个`Runnable`执行(`PLAYBACK_POSITION_REFRESH_INTERVAL_MS`)。这意味着在这个规则的时间间隔执行这个任务，用当前位置`MediaPlayer`位置绘制用户界面。您可以尝试将其更改为不同的数字，并按下`PLAY`按钮，看看这如何改变 UI 行为。

# 2.用户移动洗涤器-播放器适配器界面

为了改变播放位置，您可以使用`PlayerAdapter`界面。`MediaPlayerHolder`实现了这个接口，下面是它的部分外观。

# 2.1.使用 seekTo 跳转到媒体中的任何位置

当应用程序播放音频时，如果用户在`SeekBar`(在`MainActivity`)中拖动搓擦器，会发生两件事:

1.  必须终止正在运行以提供回放进度更新的`Executor`。否则，它会移动擦洗器，并在用户试图自己移动擦洗器时与用户发生冲突。
2.  一旦用户完成拖动洗涤器，必须使用`seekTo()`将位置报告给`MediaPlayer`。

处理这两种情况的方法在`PlayerAdapter`接口中提供，由`MediaPlayerHolder`实现。

下面是来自`MainActivity`的代码，其中处理了用户移动擦洗器的情况。注意:`mPlayerAdapter`属于`PlayerAdapter`类型，是由`MediaPlayerHolder`实现的接口。

# 状态机

`MediaPlayer`有一个复杂的状态机。不需要完全理解就可以入门。要更深入地集成到`MediaPlayer’s`功能中，请参考 developers.android.com 的[上的状态机图。](https://developer.android.com/reference/android/media/MediaPlayer.html#StateDiagram)

# Android 媒体资源

*   [了解 MediaSession](/google-developers/understanding-mediasession-part-1-3-e4d2725f18e4)
*   [Android 媒体 API 指南——媒体应用概述](https://developer.android.com/guide/topics/media-apps/media-apps-overview.html)
*   [Android 媒体 API 指南—使用媒体会话](https://developer.android.com/guide/topics/media-apps/working-with-a-media-session.html)