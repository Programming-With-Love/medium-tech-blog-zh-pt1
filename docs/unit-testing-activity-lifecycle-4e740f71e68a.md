# 用 Kotlin 对受保护的生命周期方法进行单元测试

> 原文：<https://medium.com/google-developer-experts/unit-testing-activity-lifecycle-4e740f71e68a?source=collection_archive---------1----------------------->

![](img/c26bebb54f15c260c67d8744b2f0e861.png)

[https://www.flickr.com/photos/frosch50/13186200564](https://www.flickr.com/photos/frosch50/13186200564/in/photolist-m6dJ2A-kwcXyz-iu6par-9H1XE9-dkgwmL-4Tcrbg-cgwYAq-8HzfZA-87hipg-9GY5bR-epR51-53ztX7-WpHuPX-ouuo2D-kLtd1D-8gYDbE-6y3NFm-6xV2ux-iNiEMr-6JKCZQ-pCVgHJ-6FNE9J-SH6zhq-deMmGo-95D8wL-efUWNa-8xn66X-TepKGd-WpJabg-hw8Rdd-UFPEZa-Xf5Jbz-213G3GT-7FT2xd-wbwpc-VPwQ1J-YhLwU6-8kmkoG-Verhpq-9GY3xP-8kmkm9-aiyRiY-8kj6oX-X5FqSX-eavcZZ-8knhRj-8kmkeA-eWipDq-ofVP8N-yjMM7)

开发人员经常讨论在哪里停止单元测试。是不是应该只考 Java 水平，到了一个 Android 类就停下来？我们需要做浓缩咖啡测试，对吗？

当我进行测试驱动开发时，我不想停下来。停下来会让我慢下来。甚至执行 UI 测试也意味着减慢我的速度。

因此，当编写一个 Android 应用程序，或者向其中添加一个新特性时，我想要测试驻留在*活动*中的代码。当然这段代码应该非常有限。所有代码都应该转移到特定的类中，比如演示者。但是仍然有代码留下。即使应用我最喜欢的模型:MVVM 与 Android 数据绑定*活动*仍然必须将模型和布局绑定在一起。或者它可能会转发某些生命周期事件。

这不是一个大问题，因为在相当长的一段时间里，我们可以对许多 android 类进行单元测试，即使不需要 [Robolectric](https://github.com/robolectric/robolectric) 。你可以从纯 java 单元测试中调用一个*活动*的 *onCreate()* 方法，猜猜会发生什么？没什么！因为所有代码都被删除了，所有的最终修饰符也是如此。自己看吧，只要打开 *build/generated* 文件夹，你就会发现一个*可模仿的 android jar* 文件，它是为你的测试动态生成的。

假设我有一个这样的测试:

```
@Test
fun `should inflate layout`() {
    val tested = *spy*(MainActivity())
    **tested.onCreate**(null)
    **verify**(tested).**setContentView**(R.layout.*activity_main*)
}
```

这里，我们希望确保*活动*扩展了一个特定的布局(我还检查了 onCreate 是否可以处理一个 null bundle)。这是我在做 TDD 时会写的东西，因为我不得不强迫自己写测试，强迫自己写特定的代码。

但是当你试着写这个的时候，你会看到:

> ***无法访问‘onCreate’:它在‘main activity’***中受保护

这完全说得通。

在 Java 中，欺骗系统是很容易的。让测试类和我们正在测试的*活动*在同一个包中。

科特林让我们的生活变得更艰难，它不再接受这一点。这是正确的，这个方法被标记为 *protected* 以从子类中覆盖它，但是它不能从位于其中一个子类的同一个包中的每个类中访问。

但是我们如何解决这个测试呢？

我可以在子类中改变这些方法的可见性。但我会为考试做些事情。我们应该避免的事情。

有一个更好的方法:包规则仍然适用于在最初的*活动*类中声明方法的地方。所以 *android.app* 包里的所有人还是可以调用 *onCreate()* 。

当然，您不希望将所有的测试都转移到那个系统包中。所以我们需要的是一座桥，当我们使用 Kotlin 时，我们可以建造一座优雅的桥:

```
**package android.app**

import android.os.Bundle

fun **Activity.onCreate**(bundle: Bundle?) = **this**.**onCreate**(bundle)
```

这个代码片段定义了一个与我们的生命周期方法同名的扩展函数，然后调用它。请记住，扩展函数只是静态函数，因此对于 JVM 来说，不存在名称冲突。

我们在测试中需要做的就是导入这个方法，并保持代码的其余部分不变:

```
import **android.app.onCreate**@Test
fun `should inflate layout`() {
    val tested = *spy*(MainActivity())
    **tested.*onCreate***(null)
    **verify**(tested).**setContentView**(R.layout.*activity_main*)
}
```

现在您正在调用的 *onCreate* 正在使用我们的小间接方式，而不会损害测试的可读性。

如果你懒得自己写这个，请随意使用我的版本，它为所有受保护的方法实现了一个桥，包括 *onActivityResult* :

```
repositories {
    maven {url **"https://jitpack.io"**}
}testImplementation **'com.github.dpreussler:android-tdd-utils:v0.1'**
```

PS:在尝试调用*appcompactivity*的 *onCreate()* 时会遇到一些问题。原因:可模仿的罐子在这方面没有帮助。我们将在以后的博客中研究如何解决这个问题。敬请关注。