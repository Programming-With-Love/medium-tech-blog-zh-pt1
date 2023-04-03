# 颤动:向资源添加图像

> 原文：<https://medium.com/globant/flutter-add-an-image-to-the-assets-3c6cb1d933f6?source=collection_archive---------5----------------------->

![](img/f0dfb1d38bc7592a6ea4a24ec4bcb09f.png)

*   为了在 flutter 应用程序中添加图像，我们使用资产。
*   我们从资产中加载图像。
*   资产是您在应用程序中本地添加的资源。
*   在任何 flutter 应用程序中，如果你想添加任何图像，你必须在 **pubspec** 中添加该图像。 **yaml** 文件。
*   在 **pubspec** 。 **yaml** 文件中你可以看到其中的一段线

```
*# To add assets to your application, add an assets section, like this:
# assets:
#  - images/a_dot_burr.jpeg
#  - images/a_dot_ham.jpeg*
```

*   以上这几行总是在 **pubspec** 里。 **yaml** 假设你想添加一张 cat.jpeg 的图片
*   您应该遵循的步骤

1.  转到**公共规范**。 **yaml** 归档并取消对资产代码的注释，并确保左边只有两个空格。(这些空格非常重要)
2.  然后定义你的图像名，假设你想加载 cat.png

```
*assets:
 - images/*cat.png
```

3.在顶部，您可以看到一条消息，说明 pubspec 已被编辑，因此点击**获取依赖项。**

*   该图像被加载到 **pubspec** 中。 **yaml** 文件。
*   您也可以创建一个文件夹，并放置您的图像，假设您创建了一个名为 assets 的文件夹。
*   您可以在代码中使用此图像:
*   假设您有一个扩展无状态小部件的类
*   在 Widget 构建方法中，您可以使用 AssetImage 类代码

```
AssetImage assetImage = AssetImage(assets/cat.png)Image image = Image(image : assetImage)
```

*   你应该注意**public spec**中资产的空间。 **yaml** 文件。

快乐阅读:)