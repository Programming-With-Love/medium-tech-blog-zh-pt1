# 深度探讨:MediaPlayer 最佳实践

> 原文：<https://medium.com/androiddevelopers/deep-dive-mediaplayer-best-practices-feb4d15a66f5?source=collection_archive---------3----------------------->

![](img/d1c9528cdbfa6a6206bd475b4f9aaaf1.png)

Photo by [Marcela Laskoski](https://unsplash.com/photos/YrtFlrLo2DQ?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

MediaPlayer 看起来似乎很容易使用，但复杂性隐藏在表面之下。例如，写下这样的内容是很有诱惑力的:

```
MediaPlayer.create(context, R.raw.cowbell).start()
```

这在第一次很好，可能在第二次、第三次甚至更多次都是如此。但是，每个新的 MediaPlayer 都会消耗系统资源，例如内存和编解码器。这可能会降低应用程序的性能，甚至可能会降低整个设备的性能。

幸运的是，只要遵循一些简单的规则，就有可能以既简单又安全的方式使用 MediaPlayer。

# 简单的例子

最基本的情况是，我们有一个声音文件，也许是一个原始资源，我们只想播放它。在这种情况下，我们将创建一个单独的播放器，每次需要播放声音时都可以重用它。播放器应该是这样创建的:

```
**private val** mediaPlayer = MediaPlayer().apply **{** setOnPreparedListener **{** start() **}** setOnCompletionListener **{** reset() **}
}**
```

播放器由两个监听器创建:

*   [OnPreparedListener](https://developer.android.com/reference/android/media/MediaPlayer.OnPreparedListener.html) ，播放器准备好后会自动开始播放。
*   [OnCompletionListener](https://developer.android.com/reference/android/media/MediaPlayer.OnCompletionListener.html) ，播放结束后自动清理资源。

创建播放器后，下一步是创建一个函数，该函数获取一个资源 ID 并使用该 MediaPlayer 来播放它:

```
**override fun** playSound(@RawRes rawResId: Int) {
    **val** assetFileDescriptor = context.*resources*.openRawResourceFd(rawResId) ?: **return** mediaPlayer.run **{** reset()
        setDataSource(assetFileDescriptor.fileDescriptor, assetFileDescriptor.startOffset, assetFileDescriptor.declaredLength)
        prepareAsync()
    **}** }
```

这个简短的方法中发生了很多事情:

*   资源 ID 必须转换成一个 [AssetFileDescriptor](https://developer.android.com/reference/android/content/res/AssetFileDescriptor.html) ，因为这是 MediaPlayer 用来播放原始资源的。空检查确保资源存在。
*   调用 reset()确保播放器处于初始化状态。无论玩家处于什么状态，这都是有效的。
*   为播放器设置数据源。
*   prepareAsync 准备播放器播放并立即返回，保持 UI 响应。这是因为附加的 OnPreparedListener 在源准备好之后开始播放。

需要注意的是，我们没有在播放器上调用 release()或者将其设置为 null。我们想再利用它！因此，我们调用 reset()，释放它正在使用的内存和编解码器。

播放声音就像打电话一样简单:

```
playSound(R.raw.cowbell)
```

简单！

# 更多牛铃

一次播放一个声音很容易，但是如果你想在第一个声音还在播放的时候开始另一个声音呢？像这样多次调用`playSound()`是行不通的:

```
playSound(R.raw.big_cowbell)
playSound(R.raw.small_cowbell)
```

在这种情况下，`R.raw.big_cowbell`开始准备，但是第二个调用在任何事情发生之前重置了播放器，所以只有你只听到`R.raw.small_cowbell`。

如果我们想同时播放多种声音呢？我们需要为每一个创建一个媒体播放器。最简单的方法是拥有一个活跃玩家的列表。也许是这样的:

```
**class** MediaPlayers(context: Context) {
    **private val context**: Context = context.*applicationContext* **private val playersInUse** = *mutableListOf*<MediaPlayer>()

    **private fun** buildPlayer() = MediaPlayer().apply **{** setOnPreparedListener **{** start() **}** setOnCompletionListener **{** it.release()
            playersInUse -= it
        **}
    }

    override fun** playSound(@RawRes rawResId: Int) {
        **val** assetFileDescriptor = context.resources.openRawResourceFd(rawResId) ?: **return
        val** mediaPlayer = buildPlayer()

        mediaPlayer.run **{** playersInUse += it
            setDataSource(assetFileDescriptor.fileDescriptor, assetFileDescriptor.startOffset,
                    assetFileDescriptor.declaredLength)
            prepareAsync()
        **}** }
}
```

现在每种声音都有自己的播放器，可以同时播放`R.raw.big_cowbell`和`R.raw.small_cowbell`！完美！

…嗯，几乎完美。我们的代码中没有任何东西限制一次可以播放的声音数量，MediaPlayer 仍然需要内存和编解码器。当它们用完时，MediaPlayer 会无声地失败，只在 logcat 中注明“`E/MediaPlayer: Error (1,-19)`”。

# 输入 MediaPlayerPool

我们希望支持同时播放多种声音，但我们不想耗尽内存或编解码器。管理这些事情的最好方法是有一个玩家池，然后当我们想要播放一个声音时选择一个来使用。我们可以这样更新我们的代码:

```
**class** MediaPlayerPool(context: Context, maxStreams: Int) {
    **private val context**: Context = context.*applicationContext* **private val mediaPlayerPool** = *mutableListOf*<MediaPlayer>().*also* **{
        for** (i **in** 0..maxStreams) **it** += buildPlayer()
    **}
    private val playersInUse** = *mutableListOf*<MediaPlayer>()

    **private fun** buildPlayer() = MediaPlayer().apply **{** setOnPreparedListener **{** start() **}** setOnCompletionListener **{** recyclePlayer(it) **}
    }** */**
     * Returns a* ***[MediaPlayer]*** *if one is available,
     * otherwise null.
     */* **private fun** requestPlayer(): MediaPlayer? {
        **return if** (!**mediaPlayerPool**.isEmpty()) {
            **mediaPlayerPool**.removeAt(0).also **{
                playersInUse** += it
            **}** } **else null** }

    **private fun** recyclePlayer(mediaPlayer: MediaPlayer) {
        mediaPlayer.reset()
        **playersInUse** -= mediaPlayer
        **mediaPlayerPool** += mediaPlayer
    }

    **fun** playSound(@RawRes rawResId: Int) {
        **val** assetFileDescriptor = **context**.*resources*.openRawResourceFd(rawResId) ?: **return
        val** mediaPlayer = requestPlayer() ?: **return** mediaPlayer.*run* **{** setDataSource(assetFileDescriptor.fileDescriptor, assetFileDescriptor.startOffset,
                    assetFileDescriptor.declaredLength)
            prepareAsync()
        **}** }
}
```

现在多种声音可以同时播放，我们可以控制同时播放的最大数量，以避免使用太多的内存或太多的编解码器。而且，因为我们正在回收实例，所以垃圾收集器不必运行来清理所有已经结束播放的旧实例。

这种方法有一些缺点:

*   在播放完 *maxStreams* 声音后，任何对 playSound 的额外调用都会被忽略，直到一个播放器被释放。你可以通过“偷”一个已经被用来播放新声音的播放器来解决这个问题。
*   调用 playSound 和实际播放声音之间可能会有明显的延迟。尽管 MediaPlayer 正在被重用，但它实际上是一个通过 JNI 控制底层 C++原生对象的瘦包装器。每次您调用`MediaPlayer.reset()`时，本机播放器都会被销毁，每当准备好 MediaPlayer 时，必须重新创建它。

在保持重用播放器的能力的同时改善延迟是很难做到的。幸运的是，对于某些类型的声音和需要低延迟的应用程序，我们下次会看到另一个选项: [SoundPool](https://developer.android.com/reference/android/media/SoundPool.html) 。