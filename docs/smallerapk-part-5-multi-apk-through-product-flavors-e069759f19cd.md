# #SmallerAPK，第 5 部分:产品风味的多重 APK

> 原文：<https://medium.com/androiddevelopers/smallerapk-part-5-multi-apk-through-product-flavors-e069759f19cd?source=collection_archive---------5----------------------->

![](img/16742a27007b85054b45771a39fbd376.png)

在[之前的文章](/@wkalicinski/smallerapk-part-4-multi-apk-through-abi-and-density-splits-477083989006)中，我解释了如何在 Android Studio 中使用*分割*机制来创建基于 ABI 和密度的多 APK。还有另一种更灵活的方法来为你的应用程序创建多 APK 配置，那就是使用产品风格。首先，快速回顾一下[文档](http://developer.android.com/tools/building/configuring-gradle.html#workBuildVariants):

> 构建系统使用产品风格来创建应用程序的不同产品版本。您的应用程序的每个产品版本可以有不同的功能或设备要求。

这似乎正是我们多 APK 所需要的！通过对不同风格的清单和资源进行小的更改，您可以从单个项目中获得许多版本(我们称之为构建变体)的应用程序。

实际上，使用香料来再造分裂是可能的。我们可以为每个密度桶创建一个风格，并使用 [*resConfigs*](http://google.github.io/android-gradle-dsl/current/com.android.build.gradle.internal.dsl.ProductFlavor.html#com.android.build.gradle.internal.dsl.ProductFlavor:resConfigs(java.lang.String[])) 选项来指定应该为每个风格保留哪些可抽牌。

> **注意**:从构建工具 v21 开始，resConfigs 的行为已经改变。您只能指定**一个**密度桶，AAPT 工具将自动计算出哪些抽屉需要进入 APK。为一个变量指定多个密度桶是错误的。

## Build.gradle

```
android {
    ...
    productFlavors {
        xhdpi {
            resConfigs "xhdpi"
            versionCode 300001
        }
        hdpi {
            resConfigs "hdpi"
            versionCode 200001
        }
        mdpi {
            resConfigs "mdpi"
            versionCode 100001
        }
        anydpi {
            versionCode 1
        }
    }
}
```

然后，在*app/src/[flavor name]/androidmanifest . XML*下为每种风格创建一个新的 *AndroidManifest.xml* ，并用正确的屏幕过滤指令填充它。因为我们是手动完成的，所以我们可以克服在*分割*中缺乏对数字密度桶的支持。您可以选择将它们与下一个更高的存储桶结合起来，例如，像下面的代码片段( *xhdpi* 是 320 dpi 屏幕的名称):

## app/src/xhdpi/Android manifest . XML

```
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android">
<compatible-screens>
<screen android:screenSize="small" android:screenDensity="xhdpi” />
<screen android:screenSize="normal" android:screenDensity="xhdpi" />
<screen android:screenSize="large" android:screenDensity="xhdpi" />
<screen android:screenSize="xlarge" android:screenDensity="xhdpi" /><screen android:screenSize="small" android:screenDensity="280" />
<screen android:screenSize="normal" android:screenDensity="280" />
<screen android:screenSize="large" android:screenDensity="280" />
<screen android:screenSize="xlarge" android:screenDensity="280" /></compatible-screens>
</manifest>
```

记住还要构建和发布一个通用的( *anydpi* 风格，在上面的例子中)APK，没有额外的清单条目和最低的*版本代码*，以支持未来的设备。

# 基于 MinSdkVersion 的多 APK

我将在#smallerAPK 文章的后续章节中讨论的许多优化都依赖于新的压缩格式或矢量图像支持，这些只是在最近的 Android 版本中引入的。如果我们想利用这些优势，同时仍然保持与旧设备的兼容性，通常我们必须多次包含相同的资源。

通过使用 flavors，我们可以创建多 APK 配置，而使用 splits 是不可能的。让我们考虑创建一个单独的应用程序版本，只针对较新的 Android 版本和一个支持旧设备的版本。

## Build.gradle

```
android {
    productFlavors {
        prelollipop {
            versionCode 1
        }
        lollipop {
            minSdkVersion 21
            versionCode 2
        }
    }
}
```

现在，通过将资源放置在*app/src/preroll pop/RES/*或 *app/src/lollipop/res* 中，您可以控制每个版本将打包哪些文件，例如，您可以仅针对 Android 5.0+版本包含您的 [VectorDrawables](/@wkalicinski/smallerapk-part-7-image-optimization-shape-and-vectordrawables-ed6be3dca3f#4cb7) ，针对 lollipop 之前的版本包含 PNG/ [WebP](/@wkalicinski/smallerapk-part-6-image-optimization-zopfli-webp-4c462955647d#2de9) 。

如果您需要二维或多维的产品风格，如屏幕密度和*最小版本*，您可以这样设置:

## Build.gradle

```
android {
    ...
    flavorDimensions “density”, “version”
    productFlavors {
        xhdpi {
            dimension "density"
            resConfigs "xhdpi"
            versionCode 4
        } //other densities here... anydpi {
            dimension "density"
            versionCode 1
        } prelollipop {
            dimension "version"
            versionCode 1
        }
        lollipop {
            dimension "version"
            minSdkVersion 21
            versionCode 2
        }
    }
}
```

这使得设置正确的版本号变得有点复杂，因为我们需要为每种组合风格计算版本号。请始终记住，可以安装在设备上的最高版本的 APK(即匹配 minSdkVersion 和其他过滤器)始终从 Play Store 下载。你可以找到一个方便的脚本，你可以适应你的需要[在这里](http://tools.android.com/tech-docs/new-build-system/tips)。

现在，为了一些完全不同的东西… [第 6 部分:图像优化，Zopfli & WebP](/@wkalicinski/smallerapk-part-6-image-optimization-zopfli-webp-4c462955647d)