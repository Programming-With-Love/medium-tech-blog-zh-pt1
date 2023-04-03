# #SmallerAPK，第 4 部分:通过 ABI 和密度分割实现多 APK

> 原文：<https://medium.com/androiddevelopers/smallerapk-part-4-multi-apk-through-abi-and-density-splits-477083989006?source=collection_archive---------3----------------------->

![](img/16742a27007b85054b45771a39fbd376.png)

[**更新 1**](#4e2d) **:** 添加了一个 Gradle 脚本来修复自定义密度设备的分割。

[**更新 2**](#4f51) :如何减少 ABI 拆分的 apk 数量。

Android 是一个多样化的生态系统，设备从手机到平板电脑甚至电视，都有自己的硬件特征，如屏幕尺寸、像素密度和 CPU 架构。

尽管在[文档](http://developer.android.com/google/play/publishing/multiple-apks.html)中，我们鼓励你创建一个单独的包来支持所有这些设备，但有时最大的空间节省来自于将你的应用分成多个 apk。如果您以这种方式准备您的应用程序，使用 ARM 处理器的手机用户将不必下载 x86 CPUs 的本机代码，或者使用中等密度屏幕的用户将不会存储超高密度资产并浪费空间和带宽。

您可以在这里找到关于多 APK 支持如何工作、Play Store 支持哪种过滤器以及关于版本编号的一些重要规则[的详细信息。理解多 APK 背后的理论是至关重要的，这样你作为一个开发者可以决定你是否想要增加你的发布过程的复杂性，以及它是否对你的用户有益。](http://developer.android.com/google/play/publishing/multiple-apks.html#HowItWorks)

给你的应用添加多 APK 支持的最简单的方法是通过一个叫做[](http://tools.android.com/tech-docs/new-build-system/user-guide/apk-splits)*的配置选项。Splits 是您可以添加到您的 *build.gradle* 文件中的一个部分，它可以为不同的屏幕密度或 ABI(即 CPU 架构)创建单独的 apk，作为通常构建过程的一部分。*

# *密度分裂*

*如果你需要一个示例应用程序，在你开始将拆分添加到你的应用程序之前，我建议你查看一下 Github 上的 [Topeka，它已经包含了许多密度的图像资源——非常适合尝试拆分！](https://github.com/googlesamples/android-topeka)*

*打开 *app/build.gradle* 文件，在 android 部分添加以下几行:*

## *build.gradle*

```
*android {
    ...
    splits {
        density {
            enable true
            exclude 'ldpi', 'tvdpi', 'xxxhdpi'
//alternatively use the following two lines to only include:
//            reset()
//            include 'mdpi', 'hdpi', 'xhdpi', 'xxhdpi'
            compatibleScreens 'small', 'normal', 'large', 'xlarge'
        }
    }
}*
```

*配置选项非常简单。首先，您可以根据屏幕密度启用拆分，然后您可以排除(或包括)创建特定配置的拆分，最后指定您的应用程序兼容哪些屏幕，这通常应该是所有四个类别—从小到特大。*

*默认情况下，还会创建一个包含所有密度的通用 APK。至关重要的是，你在 Play Store 上发布的这个 APK 的版本号要低于所有其他特定密度的软件包。这是必要的，原因有二:*

1.  *用于针对特定设备的机制是清单中的 *<兼容屏幕>* 部分，它不是面向未来的。它要求显式列出每个支持的密度，并且没有包罗万象的存储桶。当新设备出现时，有时会引入新的屏幕密度(正如 T4 的 Nexus 5X 和 6P 的 T5 所发生的那样)，如果没有通用 APK 后备，这些设备将无法下载你的应用程序。*
2.  *遗憾的是，目前只有命名密度可以使用 include/exclude 语句，因此您无法创建针对 280/360/420/480/560 dpi 设备的 APK。我已经为这个[提交了一个 bug。](https://code.google.com/p/android/issues/detail?id=198393)*

***更新**:我已经想出了一种方法来解决上面提到的问题(2)，通过使用 Gradle 脚本将缺失的密度添加到生成的 AndroidManifest.xml 中。它可能并不优雅，但在 Android Gradle 插件缺少支持的情况下，它确实可以完成工作。在本例中，280dpi 设备将获得 *xhdpi* APK，420/400/360 dpi 设备将获得 *xxhdpi* 等等。*

## *build.gradle*

```
*ext.additionalDensities = ['xhdpi': ['280'], 'xxhdpi': ['420', '400', '360'], 'xxxhdpi': ['560']]
import com.android.build.OutputFile

android.applicationVariants.all { variant ->
    // assign different version code for each output
    variant.outputs.each { output ->
        if (output.getFilter(OutputFile.DENSITY) != null && project.ext.additionalDensities.containsKey(output.getFilter(OutputFile.DENSITY))) {
            output.processManifest.doFirst {
                def manifestFile = new File(project.buildDir, "intermediates" + File.separator + "manifests" + File.separator + "density" + File.separator + output.getFilter(OutputFile.DENSITY) + File.separator + variant.buildType.name + File.separator + "AndroidManifest.xml")
                def manifestText = manifestFile.text
                for (String density : project.ext.additionalDensities.get(output.getFilter(OutputFile.DENSITY))) {
                    manifestText = manifestText.replaceAll("</compatible-screens>", "<screen android:screenSize=\"small\" android:screenDensity=\"${density}\" />\n" +
                            "<screen android:screenSize=\"large\" android:screenDensity=\"${density}\" />\n" +
                            "<screen android:screenSize=\"xlarge\" android:screenDensity=\"${density}\" />\n" +
                            "<screen android:screenSize=\"normal\" android:screenDensity=\"${density}\" />\n </compatible-screens>")
                }
                manifestFile.text = manifestText
            }
        }
    }
}*
```

*您可以在项目文件夹中的中间文件中检查由生成系统创建的清单项:*

```
*/app/build/intermediates/manifests/density/hdpi/debug/AndroidManifest.xml*
```

*它们是部分清单文件，只包含与主清单文件合并的 *<兼容屏幕>* 部分:*

```
*<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android" package="">
 <compatible-screens>
 <screen android:screenSize="small" android:screenDensity="hdpi" />
 <screen android:screenSize="normal" android:screenDensity="hdpi" />
 <screen android:screenSize="large" android:screenDensity="hdpi" />
 <screen android:screenSize="xlarge" android:screenDensity="hdpi" />
 </compatible-screens>
</manifest>*
```

*Play Store 将使用它来过滤掉不在定义的密度桶中的设备，并向请求安装您的应用程序的设备发送正确的 APK。*

> ***注意**:如果您希望所有可用密度中包含任何图像，以便在分割过程中不会被剥离，您应该将它们放入 mipmap 资源文件夹中。这通常用于应用程序图标，因为一些启动器可能使用来自更高密度桶的图标。*

*启用拆分后，Topeka 应用程序可以节省大量成本。以下是 APK 的尺码:*

```
*Original (universal) APK:   3.5 MB
MDPI devices:               1.1 MB (2.4 MB savings)
HDPI devices:               1.2 MB (2.3 MB savings)
XHDPI devices:              1.3 MB (2.2 MB savings)*
```

*当然，这将因应用程序而异，取决于图像资源的大小。*

# *ABI 分裂了*

*与基于密度的拆分类似，您也可以为具有不同 CPU 体系结构的设备配置 ABI 拆分:*

```
*splits {
    abi {
        enable true
        reset()
        include 'x86', 'armeabi-v7a', 'mips'
        universalApk false
    }
}*
```

*如果你想建造一个通用的 APK，你可以控制，但这通常是不必要的。记得给 x86_64 和 x86 比 ARM 更高的版本号，因为许多 x86 设备可以通过仿真层运行 ARM 代码，尽管性能较低。通过正确设置版本编号，您可以确保他们获得最佳的库。*

***更新:**如果您不希望管理太多 APK(每个 ABI 一个，可能会乘以密度分割数)，有一种方法可以利用通用 APK。如果你的应用有任何分析数据，看看哪个 ABIs 上有多少用户。将最流行的(通常是 ARM，也可能是 x86)作为 APK 的目标，将通用的 APK 提供给其他人。这样你可以确保 90%的用户获得最佳版本，而其他人仍然可以下载和使用你的应用。*

*当然，为每个用户提供最佳版本是最好的，但是和所有事情一样，你需要决定作为一个开发人员要花多少时间来维护版本，以及有多少用户将从优化中受益。*

# *设置版本代码*

*这里有一个代码片段，可以帮助您为输出的 apk 设置不同的版本代码，以便您可以将它们作为一个列表上传到 Play Store。*

```
*// map for the version codes
ext.versionCodes = ['mdpi':1, 'hdpi':2, 'xhdpi':3].withDefault {0}import com.android.build.OutputFileandroid.applicationVariants.all { variant ->
// assign different version code for each output
    variant.outputs.each { output ->
        output.versionCodeOverride = project.ext.versionCodes.get(output.getFilter(OutputFile.DENSITY)) * 1000000 + android.defaultConfig.versionCode
    }
}*
```

*本例使用 3 个密度分割，版本编号计算为*密度版本代码* 1000000 + app 版本代码*。所以版本号应该是这样的:*

```
*Universal:       1,      2,       3 ...
MDPI:      1000001,1000002, 1000003 ...
HDPI:      2000001,2000002, 2000003 ...
XHDPI:     3000001,3000002, 3000003 ...*
```

*这样，只有不符合任何特定密度的设备才能获得通用 APK。您可以使用类似的方法来设置 ABI 分割的版本代码，通过改变*输出文件。密度*至*输出文件。ABI* 并在代码中使用 ABI 的名字，如‘x86’和‘arme ABI-v7a’而不是‘mdpi’、‘hdpi’和‘xhdpi’。*

*如果您需要多 APK 设置的更多灵活性，请查看[第 5 部分:多 APK 产品风格](/@wkalicinski/smallerapk-part-5-multi-apk-through-product-flavors-e069759f19cd)*