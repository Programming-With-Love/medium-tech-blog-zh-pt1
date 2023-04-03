# Hack:将 Gitlab 与浪子集成到 Android 中

> 原文：<https://medium.com/quick-code/hack-integrating-gitlab-with-fastlane-for-android-4af914f1cbe8?source=collection_archive---------0----------------------->

到目前为止，我很喜欢使用[浪子](https://fastlane.tools/)作为在我的 Android 项目上部署 apk 的 CI 工具。这篇文章是建立在我的朋友[罗杰](https://proandroiddev.com/@thedancercodes)在他的详细的 3 部分系列文章“使用浪子自动化 Android 构建和发布过程”上的。

![](img/449b525dbdd12f1fa0ab3886865d2664.png)

我记得我花了几个小时的研究试图找到一个 *config.yml* 文件，它可以很容易地为 Gitlab 特别工作。

如果你打算将浪子与 Gitlab 集成，这里有一个配置可以为你工作。在你设置的时候，我会写一些简短的评论来指导你，但是如果你遇到困难，你可以随时联系我。

**主参考:**

[*https://proandroiddev . com/automating-the-Android-build-and-release-process-using-fast lane-part-3-9357414688 a2*](https://proandroiddev.com/automating-the-android-build-and-release-process-using-fastlane-part-3-9357414688a2)

> *分享就是关爱！我也不介意拍几下:)*