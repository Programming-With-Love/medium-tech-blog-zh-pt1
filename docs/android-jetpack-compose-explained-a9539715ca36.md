# Android Jetpack Compose 解释

> 原文：<https://medium.com/quick-code/android-jetpack-compose-explained-a9539715ca36?source=collection_archive---------5----------------------->

![](img/3b71350a01deb94c3a161ad0286ac734.png)

[http://unsplash.com/&utm_source=slidescarnival](http://unsplash.com/&utm_source=slidescarnival)

android 应用程序中关于视图更改的业务逻辑一直都是关于手动更改有状态视图。这意味着随着应用程序的变化，UI 层次结构会手动更新以显示当前数据。通常称为更改小部件的内部状态。

进一步来说， [Singh 的文章](https://blog.mindorks.com/state-management-in-jetpack-compose)将**状态**解释为一个对象，该对象包含映射到一个或多个小部件的某些数据。使用 state 对象的值，我们可以更新小部件中显示的数据。状态的值可以在运行时改变，这将帮助我们用更新的数据更新小部件。

下面是一个将 TextView 小部件(状态小部件)手动更改为不同背景的示例。

```
class MainActivity : AppCompatActivity() {
   lateinit var textView: TextView
   override fun onCreate(savedInstanceState: Bundle?) {
      super.onCreate(savedInstanceState)
      setContentView(R.layout.activity_main)
      title = "KotlinApp"
      textView = findViewById(R.id.textView)
 **textView.setOnClickListener {**         if (Build.VERSION.SDK_INT < 23) {
 **textView.setTextAppearance(applicationContext,     R.style.boldText)
         }**         else {
 **textView.setTextAppearance(R.style.boldText)**         }
 **textView.setBackgroundResource(R.color.highlightedTextViewColor)**      }
 **}**
}
```

我们现在看到了一种反应式编程的最新趋势，它解决了在 Android 应用程序中手动更改有状态视图的特殊挑战。此外，解释为什么从传统的系统设计架构如 [MVC](https://www.geeksforgeeks.org/mvc-model-view-controller-architecture-pattern-in-android-with-example/) / [MVP](https://www.raywenderlich.com/7026-getting-started-with-mvp-model-view-presenter-on-android) 转变为更具反应性的 [MVVM](https://blog.mindorks.com/mvvm-architecture-android-tutorial-for-beginners-step-by-step-guide) 和 [MVI](https://www.raywenderlich.com/817602-mvi-architecture-for-android-tutorial-getting-started) 方法。

Jetpack Compose 简介… Jetpack Compose 是作为一种改进 Android 工程师设计用户界面的方式而开发的，使他们更容易、更快速地编写和运行。

**PS:** Jetpack Compose 不是 Jetpack——Jetpack 附带了几个独立的库和资源，可以帮助 Android 开发人员设计应用程序，并处理它使用的数据和显示。

# 为什么选择 Jetpack Compose？

一个 [**组合**](https://developer.android.com/jetpack/compose/state#composition-and-recomposition) 是一个树形结构的用户界面组合集。运行 composables 会产生一个组合，它定义了用户界面。因此，当响应状态变化而改变的组件被重组时，这个事件就是动作**重组**。

例如，当一个可组合的函数显示一个按钮，并且每次单击该按钮时，调用者都会更新单击次数的值。
意思是，每次点击事件发生时，text 函数被重用来显示带有更新值的新文本。

从长远来看，重新组合整个 UI 树在计算上可能是昂贵的，这会使用计算能力和电池寿命。因此，Jetpack Compose 使用智能重组树来解决这个问题。一个可组合的函数可以用于多个状态视图。

# 使用 Jetpack Compose 的 3 个理由

1.  单一解决方案的冗余选项更少。创建整个布局的代码更少
2.  UI 模型中所有权和事件流的清晰状态。
3.  很容易更新(都是 Kotlin)也很容易测试。

这里有一些简单的步骤让你开始。您还可以在这里找到其他使用 Jetpack Compose [的示例应用程序。](https://github.com/android/compose-samples)

# **入门**

*   安装 [Android Studio 金丝雀](https://developer.android.com/studio/preview) -金丝雀版本获得所有最新版本(包括稳定版)。
*   打开 **app/build.gradle** 文件，并在每个块中添加如下代码行，如图所示。

```
**buildFeatures {** **compose true****}****composeOptions {** **kotlinCompilerExtensionVersion "${compose_version}"****}****dependencies {****implementation "androidx.compose.ui:ui:$compose_version"****implementation "androidx.activity:activity-compose:1.3.0-alpha05"****implementation "androidx.compose.material:material:$compose_version"****implementation "androidx.compose.ui:ui-tooling:$compose_version"****}**
```

*   替换 **MainActivity.kt** 中的现有调用 *setContentView()* ，以引用可组合函数。

```
**setContent {
    MaterialTheme {
     Greeting(name = "compose")
    }
}**
```

*   最后，对特性名称应用@Composable 注释，使其成为可组合的。

关于设置过程的更详细的解释，请使用 Google 的官方文档。

祝您探索 Jetpack Compose 时一切顺利！

谢谢你[大卫·奥达里](https://medium.com/u/69202beeb59c?source=post_page-----a9539715ca36--------------------------------)为我们提供的所有帮助。