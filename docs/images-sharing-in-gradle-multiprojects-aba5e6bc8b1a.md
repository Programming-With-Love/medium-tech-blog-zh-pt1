# 梯度多项目中的图像共享

> 原文：<https://blog.kotlin-academy.com/images-sharing-in-gradle-multiprojects-aba5e6bc8b1a?source=collection_archive---------0----------------------->

Gradle 多项目非常棒！我们可以用机器人，后端，前端，以及我们需要的任何其他单个项目。[在上一篇文章](/architecture-for-multiplatform-development-in-kotlin-cc770f4abdfd)中，我展示了如何在 Gradle 上进行大规模多平台多项目，以及如何使用 Kotlin 公共模块实现大量代码共享。虽然很多元素不能这样共享。有些是图像。

![](img/a968242cd96f227f544abbd8b987f6c5.png)

Image from [licdn.com](https://media.licdn.com/mpr/mpr/AAEAAQAAAAAAAALKAAAAJGEzNDNlNWZmLTcwMTMtNDhlZi04ZjY0LWYxNTgxZTJjOWYwNw.jpg)

当我们有多平台项目，我们将总是需要在不同的客户端相同的图像。图像的问题是，在不同的模块中，我们需要不同密度的不同图像。更复杂的是，一些客户端，如 Android 或 iOS，需要不同尺寸设备的不同版本的图像。这使得实现图像共享机制变得更加困难。但这是可能的，而且绝对是值得的！以下是一些利润:

*   机制会将资源复制到所有需要它的客户端。
*   机制将为我们处理不同的图像变体创建。比如创建 mdpi，hdpi，xhdpi 等等。对于 Android 和 1x，2x 等。对于 iOS。
*   当我们改变图像时，我们只需要在一个单独的文件夹中改变它，而不是通过搜索客户端来找到它在其他地方被使用。

请注意，您并不真的需要多个模块来从这种机制中获益。即使你有一个模块，里面有不同版本的图片，这也是很有帮助的。

我希望这是令人信服的。让我们直接看代码。

# 履行

在这个 Kotlin 多平台项目中可以找到完全实现的机制。具体实现放在`imagesShare.gradle`文件中。为了将它包含到项目中，我们必须使用`build.gradle`中的`apply`函数:

```
apply **from**: **'imagesShare.gradle'**
```

为了实现该机制，我们需要一种方法来实现调整图像大小的任务。有很多不同的库可以用于此，但我决定使用 [gradle-imagemagick](https://github.com/eowise/gradle-imagemagick) 。为了包含它，我们需要添加以下声明:(您还需要安装 [ImageMagick](https://github.com/ImageMagick/ImageMagick) ，但是大多数 Linux 都默认安装了它，并且很容易安装到其他系统中)

```
buildscript {
    repositories {
        jcenter()
    }

    dependencies {
        classpath **'com.eowise:gradle-imagemagick:0.4.0'** }
}
```

接下来，我们需要定义我们想要放置调整过大小的图像的位置。定义这些信息的简单方法是使用 Gradle 集合文字。怎么可能呢？记住 Groovy 脚本是用 Gradle 编写的，所以我们可以使用它的所有语法。我们可以把它们定义为常数:

```
**def** androidMobileResDir = **"android/mobile/src/main/res"
def** androidWearResDir = **"android/wear/src/main/res/drawable"
def** webImgDir = **"web/src/main/web/img"
def** desktopImgDir = **"desktop/src/main/resources/images"**
```

下面是我们如何定义我们想要在不同的项目类型中包含什么样的图像:(这是一个从字符串到字符串列表的映射)

```
**def** imagesInclude = [
        **"Android"**: [**"banner.jpg"**,
                    **"icon_notification.png"**,
                    **"talk_icon.png"**,
                    **"launcher_icon.png"**,
                    **"launcher_circle_icon.png"**,
                    **"share_icon.png"**],
        **"Web"** : [**"banner.jpg"**,
                    **"error.png"**,
                    **"facebook_icon.png"**,
                    **"logo.png"**,
                    **"spinner.gif"**,
                    **"talk_icon.png"**,
                    **"thank_you.jpg"**,
                    **"twitter_icon.png"**],
        **"Desktop"**: [**"banner.jpg"**],
]
```

最后，我们应该明确任务。`resizeTasksConfig`说明什么是目的地，它们是什么类型，以及每个任务的预期密度是多少。它还指定了`taskName`,以防我们只需要运行一个任务。

```
**def** resizeTasksConfig = [
        [**type**: **"Android"**, **taskName**: **'PrepareImagesAndroidMobileMdpi'**, **to**: **"**$androidMobileResDir**/drawable-mdpi"**, **density**: **'160'**],
        [**type**: **"Android"**, **taskName**: **'PrepareImagesAndroidMobileHdpi'**, **to**: **"**$androidMobileResDir**/drawable-hdpi"**, **density**: **'240'**],
        [**type**: **"Android"**, **taskName**: **'PrepareImagesAndroidMobileXhdpi'**, **to**: **"**$androidMobileResDir**/drawable-xhdpi"**, **density**: **'320'**],
        [**type**: **"Android"**, **taskName**: **'PrepareImagesAndroidMobileXxdpi'**, **to**: **"**$androidMobileResDir**/drawable-xxhdpi"**, **density**: **'480'**],
        [**type**: **"Android"**, **taskName**: **'PrepareImagesAndroidMobileXxxdpi'**, **to**: **"**$androidMobileResDir**/drawable-xxxhdpi"**, **density**: **'640'**],
        [**type**: **"Android"**, **taskName**: **'PrepareImagesAndroidWear'**, **to**: androidWearResDir, **density**: **'240'**],
        [**type**: **"Web"**, **taskName**: **'PrepareImagesWeb'**, **to**: webImgDir, **density**: **'800'**],
        [**type**: **"Desktop"**, **taskName**: **'PrepareImagesDesktop'**, **to**: desktopImgDir, **density**: **'500'**]
]
```

对于`resizeTasksConfig`的每个元素，我们需要创建一个任务来处理调整到新目的地的大小。如果我们记住任务创建只是调用`task`函数，那么实现起来就很简单。因此，我们可以通过迭代`resizeTasksConfig`列表来定义所有这些任务:

```
resizeTasksConfig.each { item ->
    task(item.**taskName**, **type**: com.eowise.imagemagick.tasks.Magick) {
        convert **'commonImages'**, { include imagesInclude[item.**type**] }
        into item.**to** actions {
            -background(**'none'**)
            inputFile()
            -density(item.**density**)
            outputFile()
        }
    }
}
```

所有这些任务都是`Magic`类型的——由于这一点，我们可以用具体的行动来转换。我们指定从`commonImages`文件夹中获取图像，但只包括由`imagesInclude`为项目`type`指定的文件。然后我们指定转换后的文件应该放在哪里，应该调用什么覆盖动作。对于动作，我们指定我们期望的密度。

现在任务已经创建，我们可以调用其中的每一个任务:

```
./gradlew PrepareImagesAndroidMobileXxdpi
```

尽管一个接一个地手动调用它们并不明智。相反，我们希望用单个任务来调用它们。我们可以这样定义它:

```
task shareImages {
    dependsOn resizeTasksConfig.collect { it.**taskName** }
}
```

`resizeTasksConfig`是一个列表，`collect`是 Java、Kotlin 或 Scala `map`的 Groovy 等价物——它将列表中的每个元素映射到其他元素。这里是映射到字符串列表的映射列表，由`dependsOn`函数接受。这就是为什么`shareImages`总是依赖于`resizeTasksConfig`中指定的所有任务。

这是不够的，因为如果我们需要删除一些图像怎么办？我们不仅要创建不同的图像，还要清理所有创建的文件夹。这就是我们如何实施任务，从我们的图像共享任务的所有目的地清除所有内容:

```
task cleanImages << {
    resizeTasksConfig.each { delete it.**to** }
}
```

下一篇文章:

[](/extracting-multiplatform-common-modules-in-android-4a564cc03e0a) [## Android 中多平台通用模块的提取

### 你知道如何制作 Android 项目，但现在你想让它们多平台化。你听说了新的可能性…

blog.kotlin-academy.com](/extracting-multiplatform-common-modules-in-android-4a564cc03e0a) 

希望这篇文章有帮助。我决定把这篇文章的推广工作留给那些觉得自己学到了新东西的读者。因此，如果你认为你从这篇文章中受益，如果你能在你最喜欢的[脸书](https://www.facebook.com/)和 [Reddit](https://www.reddit.com/) 群组或 [Twitter](https://twitter.com/) 中分享它，那会很好。同样**鼓掌**会帮助其他人在 medium 上找到这篇文章。

科特林学院包含了更多有用的提示和想法，所以如果你想了解最新信息，就关注这个媒体吧。我也在 Twitter 上发布有用的东西[，所以如果你使用它，你可能想](https://twitter.com/marcinmoskala)[关注我](https://twitter.com/marcinmoskala)。为了提用 [@MarcinMoskala](https://twitter.com/marcinmoskala) 。

如果你需要格雷迪的帮助，请记住，我愿意接受咨询。

[![](img/5ce68714efe3efc036e06786166954ff.png)](http://eepurl.com/diMmGv)![](img/f36a792ac0eb95fc577e6f4125dba956.png)