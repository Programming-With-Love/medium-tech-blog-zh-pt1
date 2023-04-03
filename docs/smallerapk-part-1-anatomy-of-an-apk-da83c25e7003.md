# #SmallerAPK，第 1 部分:APK 剖析

> 原文：<https://medium.com/androiddevelopers/smallerapk-part-1-anatomy-of-an-apk-da83c25e7003?source=collection_archive---------0----------------------->

![](img/16742a27007b85054b45771a39fbd376.png)

Has your app let itself go? Time to get that APK on a diet!

All articles in a talk at Google I/O

[**更新 1**](#1646) :我们不再推荐使用 Zopfli 来压缩你的 APK

如果我问一群开发人员他们的应用程序有多大，我敢肯定大多数人会查看 Android Studio 生成的 APK 文件，并告诉我它在他们的计算机上占用了多少磁盘空间。这是最直截了当的答案，从技术上来说*是正确的，但也许我应该问更好的问题。以这些为例:*

*   你的应用安装在用户设备上时会占用多少空间*？*
*   用户下载安装你的 app 需要支付多少*网络数据*？
*   现有用户的应用程序更新下载量有多大？
*   运行您的应用程序会占用多少内存(就使用的 RAM 而言)？

如果你是一名开发人员，拥有一部高端手机，使用“吃到饱”的数据套餐，你可能想知道是否值得花时间为 APK 尺寸进行优化…记住，并非所有设备都有相同的存储、内存或网络连接。

在世界上有些地方，用户必须为下载的每兆数据付费，而且并非每个角落都有 Wi-Fi 热点。

一些设备没有内部存储容量(磁盘空间)来让用户安装他们需要的所有应用程序，这意味着他们在安装或更新任何软件之前必须三思而行。谁知道呢，这些可能是你下一个成千上万的用户，所以让我们试着把 APK 变小吧！这对每个人都有好处。

当然，考虑到我们都必须处理的各种需求和约束，很难选择一个通用的解决方案。有时，牺牲初始下载大小会加快后续更新。在其他情况下，与直觉相反，在 APK 中保持文件未压缩可以减少设备上占用的最终磁盘空间。我将努力强调这些权衡，并在适用的地方提供解释，但最终还是取决于您，即开发人员，来选择对您的应用程序和用户有意义的技术组合。

运行时内存占用超出了本系列文章的范围。提出的优化可能对内存使用和性能有一些小的副作用，既有正面的也有负面的。我会尽量在适用的时候提到任何明显的缺点，但这是留给读者衡量你的应用程序的性能特征并做出最终决定的。记住，#perfmatters！

# APK 里有什么

在我开始谈论如何精简一个应用程序之前，让我们先来看看文件格式本身。APK 实际上只是一个 ZIP 存档，包含组成应用程序的文件。通常在 APK 你会发现这样的条目:

## classes.dex

包含编译的应用程序代码，转换成 Dex 字节码。如果你使用 *multidex* 来克服[65536 方法限制](http://developer.android.com/tools/building/multidex.html#about)，你可能会在你的 APK 中看到不止一个 DEX 文件。从引入 ART 运行时的 Android 5.0 开始，这些文件在安装时由提前编译器编译成 OAT 文件，并放在设备的数据分区上。你可以在[第二部分:缩小代码](/@wkalicinski/smallerapk-part-2-minifying-code-554560d2ed40)中学习如何缩小你的 dex 代码

## res/

该文件夹包含大多数 XML 资源(如布局)和 drawables(如 PNG、JPEG ),文件夹中有各种限定符，如-mdpi 和-hdpi 表示密度，-sw600dp 或-large 表示屏幕尺寸，-en，-de，-pl 表示语言。请注意，res/中的任何 XML 文件在编译时都被转换为更紧凑的二进制表示形式，因此您无法在 APK 中用文本编辑器打开它们。

[第 3 部分:删除未使用的资源](/@wkalicinski/smallerapk-part-3-removing-unused-resources-1511f9e3f761)展示了如何确保项目中没有陈旧的资源而浪费空间。在[第 4 部分:多 APK、ABI 和密度分割](/@wkalicinski/smallerapk-part-4-multi-apk-through-abi-and-density-splits-477083989006)和[第 5 部分:通过产品风格的多 APK](/@wkalicinski/smallerapk-part-5-multi-apk-through-product-flavors-e069759f19cd)中，我们讨论了如何在多个 APK 上划分资产，这些 apk 基于硬件特性针对特定的设备组。[第 6 部分:图像优化，Zopfli & WebP](/@wkalicinski/smallerapk-part-6-image-optimization-zopfli-webp-4c462955647d) 和[第 7 部分:图像优化，Shape 和 VectorDrawables](/@wkalicinski/smallerapk-part-7-image-optimization-shape-and-vectordrawables-ed6be3dca3f) 处理使图像变小的各种优化技术。

## resources.arsc

一些资源和标识符被编译并平铺到这个文件中。它通常存储在没有压缩的 APK 中，以便在运行时更快地访问。手动压缩这个文件看起来似乎轻而易举，但实际上并不是一个好主意，至少有两个原因。首先，Play Store 会压缩任何传输数据；其次，在 APK 中压缩文件会浪费系统资源(RAM)和性能(尤其是应用程序启动时间)。

第 3 部分将展示两种方法,通过只包含对你的应用有意义的语言字符串，使这个文件变得更精简。

## AndroidManifest.xml

与其他 XML 资源类似，您的应用程序清单在编译期间被转换为二进制格式。Play Store 使用 AndroidManifest 中包含的某些信息来决定是否可以在设备上安装 APK，检查允许的密度或屏幕大小以及可用的硬件和功能(如触摸屏)。如果您想在编译后检查这些清单条目，可以使用 Android SDK 中的 aapt 工具:

```
$ aapt dump badging your_app.apk
```

## libs/

任何本地库(*。所以文件)会放在以 ABI 命名的子文件夹中(CPU 架构，例如 *x86* 、 *x86_64* 、 *armeabi-v7a* )，它们的目标在 *libs/* 文件夹下。通常，它们在安装时从 APK 复制到您的 */data* 分区。然而，由于 APK 本身在用户设备上时从不改变，这实质上是任何本地库所需空间的两倍。[本文的第 8 部分(本地库，在 APK 开放)](/@wkalicinski/smallerapk-part-8-native-libraries-open-from-apk-fc22713861ff)为 Android 6.0+上的这个问题提供了一个解决方案，在旧设备上也可以节省网络带宽。

## 资产/

此文件夹用于任何不会用作 Android 类型资源的文件资产。最常见的是字体文件或游戏数据，如关卡和纹理，以及任何其他你想直接作为文件流打开的应用程序数据。

## 元信息/

此文件夹存在于已签名的 APK 中，包含 APK 中所有文件及其签名的列表。Android 中的签名目前的工作方式是，它根据存档中的*未压缩的*文件内容逐一验证签名。

这产生了一些有趣的结果。因为 ZIP 文件中的每个条目都是单独存储的，这意味着您可以更改单个文件的压缩级别，而无需重新签名。但是，如果在签名后从存档中删除任何文件，签名验证将会失败。

关于如何创建签名的 APK，还有一点需要注意，即 *zipalign* 工具被用作构建的最后阶段。如果你手动修改了存档的内容，通常你需要重新签名，然后 *zipalign* 才能将 APK 上传到游戏商店。

# 使用 Zopfli 重新压缩 APK(不要这样做)

**更新:**之前，这篇文章包含了一个关于使用更强的压缩算法 Zopfli 在你的 APK 中再压缩文件的章节。**从 Android Studio 2.2 开始，这项功能将从我们的构建工具中移除，不再推荐使用，**因为它可能会干扰未来使 Play Store 增量更新更小的计划。

如果你仍然不相信在你的 APK 上停止使用 Zopfli，**读者提醒我，某些 Android 5.0.1 设备可能会在读取 Zopfli 压缩的 apk 时出现问题，甚至会使你的应用崩溃。**

无论如何，本指南的[接下来的](/@wkalicinski/smallerapk-part-2-minifying-code-554560d2ed40)章节应该是你的主要关注点，它比使用 Zopfli 实现 APK 节省了 10 倍。

接下来，[第二部分:缩小代码](/@wkalicinski/smallerapk-part-2-minifying-code-554560d2ed40)！