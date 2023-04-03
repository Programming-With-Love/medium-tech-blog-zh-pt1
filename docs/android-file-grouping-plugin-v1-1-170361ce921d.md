# Android 文件分组插件 v1.1

> 原文：<https://medium.com/google-developer-experts/android-file-grouping-plugin-v1-1-170361ce921d?source=collection_archive---------3----------------------->

这个插件可以在项目结构视图中将你的文件显示为一组不同的文件夹。它有助于组织布局和其他资源。

![](img/59d9380318f1358f61eb81fb2bbad69d.png)

File grouping

**注:**

*   它**不**移动文件。
*   它**不**创建文件夹。

# 装置

1.  下载最新的 *Android 文件 Grouping.zip* 文件[这里](https://github.com/dmytrodanylyk/folding-plugin/releases)
2.  打开*安卓工作室*
3.  在工具栏菜单中选择*文件* | *设置* | *插件*
4.  点击*从磁盘安装*并选择*Android File grouping . zip*
5.  重启*安卓工作室*

# 使用

![](img/07a01d53bd02da881a9dedd63dff965b.png)

1.  瑞克点击*布局*文件夹(或任何其他)
2.  在上下文菜单中点击*组* / *解组*

# 限制

*Android 项目视图*定义了自己的结构，不允许通过任何扩展修改结构。确保您在*项目结构视图*和**中，而不是在 Android 项目视图**中。

# 有什么新鲜事？

感谢[*Serhiy Kyrylyuk*](https://github.com/skyrylyuk)Android 文件分组插件现在更新到 1.1 版，增加了两个新功能。

## 隐藏折叠前缀

![](img/24d70cf533e23f3408f1553916c8e452.png)

Without hiding folding prefix

![](img/04cdb36621ee6dd5d301a40299597c78.png)

With hiding folding prefix

此功能使您能够显示不带前缀的文件名。

这样的话

*   在工具栏菜单中选择*文件* | *设置*
*   选择*其他设置*
*   勾选*隐藏折叠前缀*
*   折叠和展开文件夹以刷新项目视图

![](img/2c6f417d9a2cadb9d9d552ab33873a2f.png)

Settings

## 自定义前缀模式

![](img/eadb6442373cb0940e321a65b7860f5e.png)

Without custom prefix pattern

![](img/55631d7333c055755876d216178306de.png)

With custom prefix pattern

此功能使您能够设置过滤时下划线的位置。

这样的话

*   在工具栏菜单中选择*文件* | *设置*
*   选择*其他设置*
*   勾选*使用自定义模式*并定义模式*
*   折叠和展开文件夹以刷新项目视图
*   **注意:**默认模式将采用文件名的前两个下划线来创建文件夹。

![](img/de1df597b8e6d2c814aeafee04a879dc.png)

Settings