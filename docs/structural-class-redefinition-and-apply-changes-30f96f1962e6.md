# 结构类重定义和应用更改

> 原文：<https://medium.com/androiddevelopers/structural-class-redefinition-and-apply-changes-30f96f1962e6?source=collection_archive---------0----------------------->

![](img/9831dc74d3995e1e89eefd2526678c5c.png)

# **简介**

应用更改是 Android Studio 中的一个功能，[我们在 Android Studio 3.5](/androiddevelopers/android-studio-project-marble-apply-changes-e3048662e8cd) 中引入了该功能，以帮助您快速迭代您对应用程序所做的更改。应用变更依赖于 [JVMTI API](https://docs.oracle.com/javase/7/docs/platform/jvmti/jvmti.html) 来指导以这种方式可以应用什么变更。在 Android 11 中，Android 运行时(ART)向 JVMTI API 引入了一个名为[的结构类重定义](/androiddevelopers/structural-class-redefinition-6fc0cbab9161)的扩展。这个扩展为在开发中利用 Android 11 设备应用更改开辟了一个用例类别。更复杂的编辑现在可以在应用程序仍在运行时通过应用更改快速部署。这包括:

*   添加方法(Android Studio 4.1)
*   添加资源文件(Android Studio 4.2)
*   添加静态字段(Android Studio 4.2)

这使您可以减少开发期间的周转时间，并最大限度地提高生产率。在本帖中，我们将探索我们如何在 Android Studio 中实现这一点。

# **Android Studio 实现**

通过自动利用新的特性和功能，Apply Changes 从一开始就被设计为随着 Android 运行时的每次新迭代而不断发展。

在结构化类重定义的情况下，添加了方法的类被发送到 ART，这与之前的 Android 版本没有什么不同。添加了一个新的 API 入口点，因此您需要将 Android Studio 升级到 4.1 版或更高版本，以便动态地利用静态和虚拟的添加方法。

然而，添加变量需要在 Android Studio 中进行新的分析。当一个新的变量被添加时，ART 并不试图决定应该赋予它什么值。(敬请关注未来关于 ART 的结构类重定义实现细节的 Medium 博客)。相反，添加的变量将只被初始化为默认的原始值或 null，这将由 Android Studio 来决定它应该如何初始化。

这个过程是不平凡的。考虑这样一种情况，向一个类添加一个静态 long y，初始赋值发生在类加载期间。考虑一下这个:

```
public class example { public final static long x = System.currentTimeMillis(); public final static long y = System.currentTimeMillis();}
```

如果加载这个类，`x`和`y`的值应该非常接近。在添加`y`作为应用代码变更调用的情况下，`y`的正确值不容易计算。事实上，`y`的值应该分配给什么是有争议的，因为它最接近地模仿了`y`被初始化的程序，在那里类被加载。由于两个`currentTimeMillis()`都是在静态初始化(`<clinit>`方法)期间被调用的，应用更改将继续遵循不重新运行`<clinit>`方法的任何部分的策略。因此，增加值 y 将获得值 0。

幸运的是，Apply Changes [已经使用 D8 进行 DEX 文件分析](/androiddevelopers/android-studio-project-marble-apply-changes-e3048662e8cd)，作为该过程的一部分，在最新版本的 Android Studio 中，Apply Changes 能够利用 D8 新引入的 [Inspector](https://r8.googlesource.com/r8/+/refs/heads/master/src/main/java/com/android/tools/r8/inspector/) API。这个轻量级检查 API 能够计算一些额外的信息，作为 DEX 比较过程的一部分，只增加很少的开销(只检查更改的 Java 类)。关于新添加的变量的一组元信息被附加到将改变应用到设备的请求 ProtoBuf。

在设备上，在 Android Studio 将我们的更改传达给 VM 之前，Java 代理会检查当前加载的要替换的类。通过比较即将被替换的类和新编译的类的字段，计算新添加的字段列表和它们各自的初始值。然后，代理会暂时挂起所有其他线程，以防止线程在执行交换之前访问任何新添加的未初始化字段。如果交换请求成功，它将使用适当的变量初始化新添加的字段。

# **限制和即将推出的功能**

截至 Android Studio 4.2 Canary 3，该特性仅在添加了新的静态原语的情况下可用。作为副产品，这有助于在 R.class 中添加值，使 Apply Changes 能够添加新资源。

有一点要记住，那就是应用更改的所有用法都是正确的:程序的语义永远不会与您重新构建和重新启动程序的语义相同。想想构造函数被改变的情况；用旧构造函数构造的对象不会被重新构造。这也适用于静态变量，因为`<clinits>`不会被重新调用。

我们希望每个人都能够利用 Android Studio 的这一新功能来提高工作效率。一如既往，我们欢迎每个人在我们的[问题跟踪器](https://issuetracker.google.com/issues/new?component=192708)中为我们提供关于应用更改的反馈，并让我们知道您希望看到哪些改进。