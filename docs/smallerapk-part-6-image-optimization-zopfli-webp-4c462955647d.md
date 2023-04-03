# #SmallerAPK，第 6 部分:图像优化，Zopfli & WebP

> 原文：<https://medium.com/androiddevelopers/smallerapk-part-6-image-optimization-zopfli-webp-4c462955647d?source=collection_archive---------6----------------------->

![](img/16742a27007b85054b45771a39fbd376.png)

每个 app 在某个点上必然包含图片资源，哪怕只是几个图标。图像对应用程序大小的贡献有两种方式。首先，我们在 APK 把它们包装成资源和资产。其次，应用程序在运行时从服务器下载图像，并通常为这些图像保留一个磁盘缓存，以便更快地访问。请记住，我在这一章中提到的任何优化也可以应用在服务器端，为您节省设备上的磁盘空间和网络带宽。

首先，一些基础知识。两种最流行的图像压缩格式是 JPEG 和 PNG。何时使用的快速提醒:

**JPEG** —照片、背景。不支持透明。大小和质量随选择的压缩级别而变化很大，所以如果你坚持使用 JPEG，尝试用它来降低文件大小。

**PNG** —标志、图形、图标、艺术线条；基本上任何有鲜明边缘和对比的东西。支持透明。具有大面积连续颜色的图像压缩效果较好，渐变和摄影图像往往会生成较大的文件。

# WebP

还有一种你可能想为你的 Android 应用考虑的格式，那就是 [WebP](https://developers.google.com/speed/webp/) (发音为“weppy”)。它倾向于产生更小(平均 30%)的文件大小，适合在您的应用程序中替换 JPEG 和 PNG，因为它可以处理各种图像数据。

当然，也有一些负面影响。Android 4.0+ 设备上[保证有损 WebP 支持(适合替换大部分 JPEGs 和部分 png)。从 Android 4.2.1+](http://developer.android.com/guide/appendix/media-formats.html#core) 开始支持更新的 WebP 特性(透明、无损、适合 png)。

> **注意** : WebP 图像在运行时解压缩也需要更多的 CPU 处理能力，这可能意味着加载时间稍长。
> 
> **注 2** :您不能将 WebP 用于应用程序启动器图标。

# Zopfli 压缩的 PNGs

如果您还没有准备好采用 WebP，那么在优化 png 时，您可能还想探索另一条途径。作为 Android 构建系统的一部分，cruncher 已经做了一些常见的优化，如丢弃图像中的元数据，尽可能将调色板转换为 8 位，但如果我们用 Zopfli 预优化 png，我们可以做得更好。

Zopfli 是谷歌的工程师创造的一种压缩算法。它兼容流行的用于压缩 ZIP 文件的 DEFLATE 规范，以及…你猜对了，PNGs 中的图像数据流！

Zopfli 压缩数据可以通过标准的 ZIP(或 PNG)阅读器读取，因此不需要修改代码。你可以使用一些现有的工具(AdvPNG， [ZopfliPNG](https://github.com/google/zopfli) )来帮助你 *zopflify* 构建之前的图像(你只需要为每个图像文件做一次)。

不幸的是，目前 Android 工具还没有自动支持这种优化。此外，**在 2.2** 之前的 Android Gradle 插件版本上，您必须通过在您的版本中添加以下代码行来禁用内置的图像处理器。

## build.gradle

```
android {
    ...
    aaptOptions {
        cruncherEnabled = false
    }
}
```

这是必要的，因为 AAPT 工具不会检查它生成的文件是否小于源文件，这可能会抵消 zopfli 优化图像节省的一些空间。

> 注意:cruncher 仍然会处理 9 补丁映像，因为它们在构建时需要特殊处理

将你的图标缩小到第七部分的一小部分:图像优化，形状和矢量图