# 导航:多个后台堆栈

> 原文：<https://medium.com/androiddevelopers/navigation-multiple-back-stacks-6c67ba41952f?source=collection_archive---------2----------------------->

![](img/78243efcf455993e6100439f3f7a559a.png)

欢迎阅读第二个 MAD 技能系列中关于导航的另一篇文章！在这篇文章中，我们将看看一个被高度要求的特性，导航的多后台支持。如果你更喜欢视频形式的内容，可以看看下面的内容:

# 介绍

假设你的 app 用的是`BottomNavigationView`。通过这种改变，当用户选择另一个选项卡时，当前选项卡的后堆栈将被保存，并且所选选项卡的后堆栈将被无缝恢复。

从版本 2.4.0-alpha01 开始，`NavigationUI`助手支持多个后台堆栈，无需任何代码更改。这意味着如果你的应用程序使用`BottomNavigationView`或`NavigationView`的`setupWithNavController()`方法，你需要做的就是更新依赖关系，默认情况下会启用多后台支持。

# 多后台支持

让我们使用[本报告](https://github.com/android/architecture-components-samples/tree/master/NavigationAdvancedSample)中的高级导航示例来看看这一点。

该应用程序由 3 个选项卡组成，每个选项卡都有自己的导航流程。为了在早期版本的导航中支持多个 back stacks，我们需要在这个示例的`[NavigationExtensions](https://github.com/android/architecture-components-samples/blob/8f4936b34ec84f7f058fba9732b8692e97c65d8f/NavigationAdvancedSample/app/src/main/java/com/example/android/navigationadvancedsample/NavigationExtensions.kt)`文件中添加一组助手。有了这些扩展，应用程序为每个标签保留了一个单独的`NavHostFragment`，并在用户从一个标签切换到另一个标签时在它们之间交换。

让我们看看如果我删除这些扩展函数会发生什么。为此，我删除了`NavigationExtensions`类并移除了所有的使用，从`NavigationUI`切换到标准的`setupWithNavController()`方法来将`BottomNavigationView`连接到`NavController`。

```
class MainActivity : AppCompatActivity() {

    private lateinit var navController: NavController
    private lateinit var appBarConfiguration: AppBarConfiguration

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.*activity_main*)

        val navHostFragment = *supportFragmentManager*.findFragmentById(
            R.id.*nav_host_container* ) as NavHostFragment
        **navController = navHostFragment.navController**

        // Setup the bottom navigation view with navController
        val bottomNavigationView = findViewById<BottomNavigationView>(R.id.*bottom_nav*)
        **bottomNavigationView.*setupWithNavController*(navController)**

        // Setup the ActionBar with navController and 3 top level destinations
        appBarConfiguration = *AppBarConfiguration*(
            *setOf*(R.id.*titleScreen*, R.id.*leaderboard*,  R.id.*register*)
        )
        val toolbar = findViewById<Toolbar>(R.id.*toolbar*)
        setSupportActionBar(toolbar)
        toolbar.*setupWithNavController*(navController, appBarConfiguration)
    }

    override fun onSupportNavigateUp(): Boolean {
        return navController.*navigateUp*(appBarConfiguration)
    }
}
```

我还通过使用`include`标签将 3 个独立的导航图合并成一个图。现在，我们的活动布局只包含一个带有单个图形的`NavHostFragment`。

```
<navigation
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:id="@+id/nav_graph"
    app:startDestination="@+id/home">

 **<include app:graph="@navigation/home"/>
    <include app:graph="@navigation/list"/>
    <include app:graph="@navigation/form"/>**

</navigation>
```

当我运行应用程序时，这一次底部标签没有保持它们的状态，并在我切换到其他标签时重置其后台堆栈。随着`NavigationExtentions`的移除，该应用失去了多种后台支持。

现在我更新导航和片段依赖的版本。

```
versions.fragment = "1.4.0-alpha01"
versions.navigation = "2.4.0-alpha01"
```

一旦 gradle sync 完成，我再次运行应用程序，我可以看到当我导航到另一个选项卡时，每个选项卡都保持其状态。请注意，这种行为在默认情况下是启用的。

最后，为了验证一切正常，让我们运行测试。这个应用程序已经有几个测试来验证多 back stack 行为。我运行 BottomNavigationTest，观察运行和测试底部导航行为的不同测试。

瞧，我们所有的测试都通过了！

# 摘要

就是这样！如果你的应用程序使用了`BottomNavigationView`或`NavigationView`，并且你一直在等待多后台支持，那么你需要做的就是更新你的导航和片段依赖。不需要修改代码！

如果你正在做一些更定制的事情，也有新的 API 来支持保存和恢复后台堆栈，你可以在[这篇文章](/androiddevelopers/multiple-back-stacks-b714d974f134)中了解更多。

如果您想了解更多关于底层 API 的信息，以及需要做哪些更改来支持多个后台堆栈，您可以查看本文。

感谢您关注这个导航系列！