# 在 Bitbucket 上为 Android 应用程序设置一个简单的 CI

> 原文：<https://medium.com/quick-code/a-simple-ci-setup-on-bitbucket-for-an-android-app-9e8fcfb87941?source=collection_archive---------5----------------------->

# 适用于设置简单配置项的初学者

> 当你开发任何类型的应用程序时，CI 工具都是很重要的。它负责构建、测试、部署甚至分发您的完整应用程序，无论是在 Android 还是 iOs 上。

在不到 2 分钟的时间内，您将能够设置您的。yml 文件。

首先，确保您的存储库中有所有必要的文件/文件夹，并确保***local . properties***文件是**而不是**上传到 repo 的(我花了一整天来调试这个)。

我们从指定 docker 映像开始，在 docker 容器中运行我们的构建。我们可以用 Java 运行 Gradle 文件。

```
pipelines: default: - step: name: Build an apk image: openjdk:8
```

每个**步骤**使用您的存储库的克隆启动一个新的 Docker 容器，然后运行**脚本**部分的内容，如下面完整的 bitbucket-pipelines.yml 文件所示。

特别感谢[伊万·季米特洛夫](/@ivan.dimitrov.work?source=follow_footer--------------------------follow_footer-)的[有益文章](/@ivan.dimitrov.work/integrate-hokeyapp-with-bitbucket-for-android-project-d7f3751b2bb4)。

![](img/ecb936f48ef848d1ad5a556433d6df7b.png)