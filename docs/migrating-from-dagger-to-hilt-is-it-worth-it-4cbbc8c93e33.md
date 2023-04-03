# 从匕首到刀柄的迁移——值得吗？

> 原文：<https://medium.com/androiddevelopers/migrating-from-dagger-to-hilt-is-it-worth-it-4cbbc8c93e33?source=collection_archive---------6----------------------->

![](img/b07555fbeb7cabf07ce1f73109da516b.png)

Hilt 于 2020 年 6 月发布，作为一种标准化 Android 中依赖注入(DI)的方式。对于新项目，Hilt 提供了编译时正确性、运行时性能和可伸缩性(在这里阅读更多关于 T5 的内容)！但是，对于一个已经使用 Dagger 的应用程序来说，有什么好处呢？**你应该彻底迁移**你当前的应用吗？以下是您的团队是否应该投资从匕首到刀柄的迁移的一些原因。

# ✅安卓扩展公司

如果您已经使用 Dagger 处理 ViewModels 或 WorkManager，您会发现连接 ViewModelFactory 和 WorkerFactory 需要大量样板代码和 Dagger 知识。最常见的实现使用[多绑定](https://dagger.dev/dev-guide/multibindings.html)，这是 Dagger 中最复杂的特性之一，开发人员常常难以理解。通过删除样板代码，Hilt 使得使用 AndroidX 变得容易多了。更好的是，你甚至不需要在 Android 框架类中注入工厂，你调用它就好像 Hilt 不存在一样。有了`@ViewModelInject`，Hilt 为你生成了`@AndroidEntryPoint`活动和片段可以直接使用的正确的`ViewModelProvider.Factory`。

```
class PlayViewModel **@ViewModelInject** constructor(
  val db: MusicDatabase,
) : ViewModel() { ... }@AndroidEntryPoint
class PlayActivity : AppCompatActivity() { **val viewModel: PlayViewModel by viewModels()** override fun onCreate(savedInstanceState: Bundle) {
    super.onCreate(bundle)
    viewModel.play()
  }
}
```

# ✅测试 API

DI 应该使测试更容易，但是具有讽刺意味的是，让 Dagger 在测试中工作需要大量的工作。事实上，你必须同时维护刺和测试匕首图，这使得它明显比[刀柄的方法](https://developer.android.com/training/dependency-injection/hilt-testing)差。

Hilt 测试可以使用`[@UninstallModules](https://developer.android.com/training/dependency-injection/hilt-testing#replace-binding)`功能显式修改 DI 图。除此之外，您还可以获得其他额外的好处，比如`[@BindValue](https://developer.android.com/training/dependency-injection/hilt-testing#binding-new)`，它允许您轻松地将测试字段绑定到 DI 图中。

```
**@UninstallModules(AnalyticsModule::class)** @HiltAndroidTest
class ExampleTest { @get:Rule
  var hiltRule = HiltAndroidRule(this) **@BindValue @JvmField
  val analyticsRepository = FakeAnalyticsRepository()** @Test 
  fun myTest() { ... }
}
```

# ✅一致性

在 Dagger 中有多种方法可以实现相同的功能。Android 应用程序历史上缺乏指导(去年我们[在](https://developer.android.com/training/dependency-injection/dagger-basics)解决了这个问题)已经在社区中引起了多次辩论，并最终导致开发人员在他们的 Android 应用程序中使用和设置 Dagger 的方式不一致。

你可能会争辩说，你目前的匕首设置真的很好，你完全知道一切是如何工作的，一切是如何被注射的。所以，迁移到 Hilt 不值得！对你来说这可能是真的，但是对团队的其他人(以及潜在的未来同事)来说也是这样吗？当切换到一个新项目时，你会知道一切是如何工作的吗？理解 Dagger 在一个应用程序中的设置和使用可能是一项挑战和耗时的工作。

在你的应用程序中使用 Hilt 可以大大减少这个时间，因为所有的应用程序都使用相同的设置。一个新的开发人员加入你的团队不会对你的手柄设置感到惊讶，因为这和他们习惯的差不多。

# ✅定制组件

除了已定义的标准组件，Hilt 还提供了一种创建定制组件的方法，并将其添加到层次结构中，你可以在这里阅读更多关于[的内容](/androiddevelopers/hilt-adding-components-to-the-hierarchy-96f207d6d92d)。

即使定制组件降低了一致性，您仍然可以获得很多好处！模块自动发现功能(即`[@InstallIn](https://developer.android.com/training/dependency-injection/hilt-android#hilt-modules)`注释功能)以及测试替换功能也适用于定制组件。

然而，自定义组件和 Hilt 内置组件的区别在于，你失去了将那些组件自动注入 Android 框架类的能力(即`@AndroidEntryPoint`所做的)。

# ✅匕首和刀柄互操作

剑柄和匕首可以共存！如果你允许希尔特接管你的`SingletonComponent`，你可以在应用程序的某些部分受益于希尔特，同时保留其他最适合使用匕首的部分。这也意味着[移植到刀柄可以逐渐完成](https://codelabs.developers.google.com/codelabs/android-dagger-to-hilt#0)。

# ❌组件依赖关系

固执己见意味着它替你做决定。Hilt 为组件关系使用子组件，在这里阅读为什么。如果你坚信你的应用程序使用组件依赖会更好，那么 Hilt 并不适合你的应用程序。

在大多数项目中，从匕首移植到刀柄是值得的。Hilt 给你的应用程序带来的好处超过了升级的努力。但你不是一个人！我们提供了大量资源来帮助您完成这一旅程:

*   综合迁移[文档](https://dagger.dev/hilt/migration-guide)
*   从匕首到刀柄的迁移[代码实验室](https://codelabs.developers.google.com/codelabs/android-dagger-to-hilt#0)
*   迁移 Google I/O 应用程序以处理[博客文章](/androiddevelopers/migrating-the-google-i-o-app-to-hilt-f3edf03affe5)和[提交](https://github.com/google/iosched/commit/9c20fdd52d446e5fdb03369e50fb196c31ae16e3)
*   刀柄和辅助注射一起工作[要点](https://gist.github.com/manuelvicnt/437668cda3a891d347e134b1de29aee1)

如果你有任何问题或者你错过了更多的信息，请在下面留下你的评论！