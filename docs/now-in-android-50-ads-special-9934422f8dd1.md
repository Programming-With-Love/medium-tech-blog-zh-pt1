# 现在在 Android #50 中——广告概述第 1 部分

> 原文：<https://medium.com/androiddevelopers/now-in-android-50-ads-special-9934422f8dd1?source=collection_archive---------5----------------------->

![](img/1bba3dc826f4ef8a8f6a4f775b19050d.png)

Illustration by [Virginia Poltrack](https://twitter.com/VPoltrack)

## [隐私与安全](https://www.youtube.com/playlist?list=PLWz5rJ2EKKc_3tkP5FOQ9IgbcOxwLYMD5)、[大屏幕](https://www.youtube.com/playlist?list=PLWz5rJ2EKKc99PA-mKk2rz0jYXshN94sM)、 [Android 12](https://www.youtube.com/playlist?list=PLWz5rJ2EKKc9NzwBeLoHtKLLADjfGSkS8)

欢迎来到 Android 中的 Now，这是您对 Android 开发世界中新的和值得注意的事物的持续指导。本周是两部分系列的第一部分，涵盖了 2021 年 [Android 开发者峰会](https://developer.android.com/events/dev-summit)上发生的事情，包括 Android 团队围绕[隐私和安全](https://www.youtube.com/playlist?list=PLWz5rJ2EKKc_3tkP5FOQ9IgbcOxwLYMD5)、[大屏幕构建](https://www.youtube.com/playlist?list=PLWz5rJ2EKKc99PA-mKk2rz0jYXshN94sM)和 [Android 12](https://www.youtube.com/playlist?list=PLWz5rJ2EKKc9NzwBeLoHtKLLADjfGSkS8) 所做的更新。

# 隐私和安全

[产品经理 Sara N-Marandi](https://medium.com/u/255355c2c439?source=post_page-----9934422f8dd1--------------------------------) 和开发者关系工程师 Yacine Rezgui 提供了关于如何构建设计为私有的应用程序的指南和[最佳实践](https://developer.android.com/privacy/best-practices)，涵盖了 [Android 12](https://developer.android.com/about/versions/12) 中的新[隐私功能](https://developer.android.com/about/versions/12/behavior-changes-all#security-privacy)，并预览了即将推出的 Android 概念。

产品经理 Serban Constantinescu 谈到了从 Android 11 开始提供并在 Android 12 中继续发展的内存安全工具。这些工具可以帮助解决内存缺陷，提高应用程序的质量和安全性。

我写了一篇文章，介绍了新的[隐私仪表板](https://developer.android.com/about/versions/12/features#privacy-dashboard)，它为用户提供了在过去 24 小时内访问位置、麦克风和摄像头的应用程序的时间轴视图。用户可以确定访问发生的确切时间，并且可以选择撤销权限。我讲述了如何使用[数据访问审计 API](https://developer.android.com/about/versions/11/privacy/data-access-auditing)来跟踪应用程序中的数据访问，以及如何使用[许可意图 API](https://developer.android.com/training/permissions/explaining-access#privacy-dashboard-show-rationale) 向用户展示合理性。

[](/androiddevelopers/increasing-user-transparency-with-privacy-dashboard-23064f2d7ff6) [## 通过隐私控制面板提高用户透明度

### Android 一直在寻求保护用户隐私。在 Android 12 中，该平台通过以下方式增加了透明度…

medium.com](/androiddevelopers/increasing-user-transparency-with-privacy-dashboard-23064f2d7ff6) 

软件工程师 Lilian Young 展示了去年解决的最不寻常、最复杂、最有趣的安全问题。开发者和研究人员可以通过提交给 [Android 漏洞奖励计划](https://bughunters.google.com/about/rules/6171833274204160)来为 Android 平台的安全性做出贡献。

[Tina Sriskandarajah](https://medium.com/u/c439123f8022?source=post_page-----9934422f8dd1--------------------------------) 和 [Rustin Banks](https://medium.com/u/7ad1c2eb261c?source=post_page-----9934422f8dd1--------------------------------) 随后讲述了新的[数据安全部分](https://android-developers.googleblog.com/2021/10/launching-data-safety-in-play-console.html)将如何给你一个简单的方法来展示你的应用程序的整体安全性。它为您提供了一个地方，让用户更深入地了解您的应用程序的隐私和安全实践，并解释您的应用程序可能收集的数据及其原因，所有这些都在用户安装之前完成。

# 大屏幕建筑

[工程经理 Clara Bayarri](https://medium.com/u/7beec7f7997b?source=post_page-----9934422f8dd1--------------------------------) 和产品经理 Daniel Jacobson 谈论了生态系统的状况，重点介绍了新的设计指南、API 和工具，以帮助您在不同的屏幕尺寸上充分利用您的 UI。

它们涵盖了新的[窗口大小类别](https://developer.android.com/guide/topics/large-screens/support-different-screen-sizes#window_size_classes)，更新了现有的带有视图的应用程序， [Jetpack Compose](https://developer.android.com/jetpack/compose) 用于所有屏幕大小，新的 [Android Studio](https://developer.android.com/studio) 工具，以及如何在各种大小的不同设备上测试应用程序，所有这些都依赖于更新的[材料设计指南](https://m3.material.io/foundations/adaptive-design/large-screens/overview)用于布局。

Chrome 操作系统开发者倡导者 Emilie Roberts 和 Android 软件工程师 Andrii Kulian 分别介绍了一些新功能，这些新功能旨在让应用在大屏幕、可折叠和 Chrome 操作系统上看起来更棒。他们深入研究了 Jetpack [窗口管理器库](https://developer.android.com/reference/android/view/WindowManager)，以了解如何对可折叠姿态的变化做出反应，处理窗口尺寸并轻松支持多窗格布局。

用户希望在使用键盘、鼠标和手写笔时获得无缝体验。 [Emilie Roberts](https://medium.com/u/ec5410c2970b?source=post_page-----9934422f8dd1--------------------------------) 教我们如何处理常见的键盘和鼠标输入事件，以及如何开始使用更高级的支持，如键盘快捷键、低延迟触控笔、MIDI 等。

[开发商代言人弗朗西斯科·罗马诺](https://medium.com/u/263cf3ffa055?source=post_page-----9934422f8dd1--------------------------------)和 Zoom 产品经理威尔·陈(Will Chan)探讨了可折叠外形带来的全新用户体验，重点关注视频会议和媒体应用。了解如何使用 Jetpack 的 [WindowManager](https://developer.android.com/reference/android/view/WindowManager) API 和来自 [ConstraintLayout](https://developer.android.com/reference/androidx/constraintlayout/widget/ConstraintLayout) 的新 UI 组件处理姿势变化，以及处理视频和相机流的一些最佳实践。他们还深入研究了一些针对可折叠设备优化应用布局的案例。

谷歌最近推出了支持大屏幕的新材料指南。在这次演讲中，设计倡导者 Liam Spradlin 和开发者关系工程师 Jonathan Koren 谈到了如何设计和测试在各种设备类型和屏幕尺寸下外观和感觉都很棒的 Android 应用，从平板电脑到可折叠平板电脑再到 Chrome 操作系统。他们讨论了内容如何最好地适应这些不同屏幕的基本原理，介绍了作为设计起点的规范布局，展示了如何利用可折叠内容，并给出了如何使用响应式 UI 原则构建应用程序的提示。

[工程副总裁戴夫·伯克](https://twitter.com/davey_burke)写了一篇文章，内容涉及 12L 的[开发者预览，这是一个即将到来的功能下降，使 Android 12 在大屏幕上更加出色。通过预览，您可以尝试新的大屏幕功能，优化您的应用程序，并让我们知道您的反馈。12L 包括在大屏幕上的精致 UI，包括通知、快速设置、锁屏、概览、主屏幕等。大屏幕上的新任务栏使多任务处理更加强大和直观，允许用户即时切换到最喜欢的应用程序。在博文中了解更多关于 12L 的内容。](https://developer.android.com/about/versions/12/12L)

[](https://android-developers.googleblog.com/2021/10/12L-preview-large-screens.html) [## 12L 和面向大屏幕的新 Android APIs 和工具

### 有超过 2.5 亿台大屏幕设备在平板电脑、可折叠设备和 ChromeOS 设备上运行 Android

android-developers.googleblog.com](https://android-developers.googleblog.com/2021/10/12L-preview-large-screens.html) 

# 安卓 12

软件工程师孙宗民和 MediaPipe 工程师 Valentin Bazarevsky 讨论了最近在 [ML 套件](https://developers.google.com/ml-kit?hl=en)中发布的[文本识别](https://developers.google.com/ml-kit/vision/text-recognition/v2) & [姿态检测](https://developers.google.com/ml-kit/vision/pose-detection?hl=en)。这些先进的交钥匙 API 建立在同类最佳的模型和管道之上。您将了解这些 API 背后的内容，帮助您理解设备上的机器学习基础知识，以及如何更有效地使用它们。

研究表明，当用户获得新设备时，如果他们不得不再次登录他们最喜欢的应用程序，他们会感到沮丧，如果他们丢失了数据，他们会更加失望。这可能意味着较低的用户保留率和较差的播放商店评级。

在本次演讲中，工程经理 Martin Millmore 和软件工程师 [Ruslan Tkhakokhov](https://medium.com/u/4d383cd59f47?source=post_page-----9934422f8dd1--------------------------------) 探讨了使用[备份和恢复](https://developer.android.com/guide/topics/data/autobackup?hl=sl)以简单安全的方式将用户数据传输到新设备的好处。他们还研究了 Android 12 中引入的有趣的新功能以及备份和恢复方面的重大变化。

开发者关系工程师 [Kseniia Shumelchyk](https://twitter.com/kseniias) 和 [Slava Panasenko](https://twitter.com/slavkoder) 谈论 Android 12 的新[功能和变化](https://developer.android.com/about/versions/12/summary)。他们分享了工具和技术，以确保应用程序与下一个 Android 版本兼容，用户可以利用新功能，以及应用程序开发人员的成功故事。

从 UX 经理 [Mrinal Sharma](https://twitter.com/mrinalsharma) 和开发者关系工程师 [Amrit Sanjeev](https://twitter.com/amsanjeev) 那里学习帮助互联网新手用户打造卓越体验的原则。他们强调了新生用户和精通技术的用户之间的差距，并提出了改善整体用户体验的策略。诸如功能识字率低、默认为多语言、对数字不太自信以及之前没有互联网经验等因素要求我们重新思考为这些用户构建应用的方式。

# 那么现在…

这就是这次关于[隐私和安全](https://www.youtube.com/playlist?list=PLWz5rJ2EKKc_3tkP5FOQ9IgbcOxwLYMD5)、大屏幕和 [Android 12L 开发者预览](https://android-developers.googleblog.com/2021/10/12L-preview-large-screens.html)的[更新，以及关于 Android 12](https://www.youtube.com/playlist?list=PLWz5rJ2EKKc99PA-mKk2rz0jYXshN94sM) 的[深度报道。下周回来看 Android 开发者峰会的第二部分。](https://www.youtube.com/playlist?list=PLWz5rJ2EKKc9NzwBeLoHtKLLADjfGSkS8)