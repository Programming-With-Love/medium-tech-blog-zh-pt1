# FragmentFactory 和 Android 碎片

> 原文：<https://medium.com/capital-one-tech/android-fragmentfactory-75823af015fd?source=collection_archive---------1----------------------->

![](img/cf8185116693c31871229c0c88c09013.png)

Photo by [Jenna Beekhuis](https://unsplash.com/@jennabee?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

片段提供核心 Android 应用功能。随着 [Android Jetpack](https://developer.android.com/jetpack) 中 [AndroidX](https://developer.android.com/jetpack/androidx) 的引入，片段得到了一次彻底的检查，在这种情况下，片段实例化的内部机制发生了变化。

在 AndroidX 之前，开发人员在他们的自定义片段类中包含了一个默认的、无参数的片段构造函数，因此操作系统可以在必要时重新实例化该片段…