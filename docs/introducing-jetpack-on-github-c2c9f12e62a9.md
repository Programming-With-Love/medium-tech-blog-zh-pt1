# GitHub 上的 Jetpack 简介

> 原文：<https://medium.com/androiddevelopers/introducing-jetpack-on-github-c2c9f12e62a9?source=collection_archive---------0----------------------->

![](img/b25d1578fcb6f5a2234746203fab30ba.png)

对查看 Jetpack 库的源代码或为其做贡献感兴趣，并且您喜欢 Github？我们有东西给你。

2018 年，Jetpack 库开发转移到了 [Android 开源项目](https://android.googlesource.com/platform/frameworks/support/)，以增加透明度并支持外部贡献。从那以后，我们继续投资于提高 Jetpack 贡献者的生活质量，同时将更多正在进行的更改、功能讨论和错误转移到公开可见的组件。我们甚至看到专门的外部开发人员学习工作流程并贡献补丁；然而，我们想探索使这个过程更容易和对开发者更友好的方法。

今天，我们开始了一个项目，以满足他们的开发者:GitHub。

这是一个早期的努力，我们希望它将使探索、试验和贡献于 Jetpack 库变得更加容易。我们从小处着手，鼓励对 Room 和 WorkManager 的贡献，并支持通过 Android Studio 在 Mac OS 和 Linux 环境中进行开发。

**投稿工作流程**

要开始 Jetpack 开发，首先分叉 [AndroidX/androidx](https://github.com/androidx/androidx) 库，就像您对任何其他 GitHub 项目所做的那样，然后在本地克隆您的分叉:

```
git clone git@github.com:<username>/androidx.git .
```

接下来，参考我们的[GitHub contribution](https://github.com/androidx/androidx/blob/androidx-master-dev/CONTRIBUTING.md)文件，了解自动配置合适的 Android Studio 开发环境、进行和验证更改以及发送 pull 请求以供审查和预提交批准的详细信息。

请注意，我们目前正在接受 GitHub 对 Room 和 WorkManager 的 pull 请求。我们仍然鼓励通过标准的 AOSP Jetpack 工作流程向其他图书馆投稿，详情请见[这里](https://cs.android.com/androidx/platform/frameworks/support)。

**提供反馈**

虽然我们的 GitHub 探索范围仍然非常有限，但我们鼓励开发人员针对我们的公共 AOSP 问题跟踪器的 [Jetpack >基础架构> GitHub](https://issuetracker.google.com/issues/new?component=923725) 组件提交功能请求和错误。