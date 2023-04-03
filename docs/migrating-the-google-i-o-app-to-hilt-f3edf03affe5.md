# 将 Google I/O 应用程序移植到 Hilt

> 原文：<https://medium.com/androiddevelopers/migrating-the-google-i-o-app-to-hilt-f3edf03affe5?source=collection_archive---------0----------------------->

![](img/f91553a6295ce307f0a2ece8aff97b29.png)

Illustration by Claudia Sanchez

Hilt 是建立在 Dagger 之上的新库，它简化了 Android 应用程序中的依赖注入(DI)。但是，*它简化了多少*？我们迁移了 Google I/O app ( [iosched](https://github.com/google/iosched/commit/9c20fdd52d446e5fdb03369e50fb196c31ae16e3) )一探究竟，它已经用 Dagger 搭配 [dagger.android](https://dagger.dev/api/latest/dagger/android/package-summary.html) 。

在本文中，我将介绍我们迁移这个特定应用程序的经验。有关正确和全面的说明，请查看[刀柄迁移指南](https://dagger.dev/hilt/migration-guide)。

# -2000, +500

我们用 500 行代码代替了 2000 行 DI 代码。这不是唯一的成功标准，但是很有希望！

这种减少怎么可能呢？我们使用 dagger.android，它也承诺在 android 中减少一些样板文件。**不同的是，希尔特要固执得多**。它已经实现了一些适用于 Android 应用的概念。

例如，您不需要定义 AppComponent。刀柄自带一堆[预定义组件](https://dagger.dev/api/latest/dagger/hilt/android/components/package-summary.html)，包括`ApplicationComponent`、`ActivityComponent`或`FragmentComponent`。你仍然可以[创造你自己的](https://dagger.dev/hilt/custom-components)当然，剑柄只是匕首上面的一个包装。

让我们深入了解细节:

# Android 组件和范围

Android 中依赖注入的一个问题(实际上，一个普遍的烦恼)是组件，像活动一样，是由框架创建的。所以，为了注入依赖，你必须在创建之后以某种方式注入。通过让你调用`AndroidInjection.inject` (this)来简化这个过程，我们通过扩展`DaggerAppCompatActivity`或`DaggerFragment`来做到这一点。除此之外，我们有一个模块(`[ActivityBindingModule](https://github.com/google/iosched/blob/d5b168fc724b53bbe602cf92978274d07f37ba3b/mobile/src/main/java/com/google/samples/apps/iosched/di/ActivityBindingModule.kt)`)将定义 dagger.android 应该创建哪些子组件，它们的范围和所有包含在其中的模块，使用`@ContributesAndroidInjector`用于活动和片段:

`[ActivityBindingModule.kt](https://github.com/google/iosched/blob/d5b168fc724b53bbe602cf92978274d07f37ba3b/mobile/src/main/java/com/google/samples/apps/iosched/di/ActivityBindingModule.kt)`:

此外，每个片段都有自己的子组件，也是用`@ContributesAndroidInjector`在它们自己的模块中生成的:

`[OnboardingModule.kt](https://github.com/google/iosched/blob/d5b168fc724b53bbe602cf92978274d07f37ba3b/mobile/src/main/java/com/google/samples/apps/iosched/ui/onboarding/OnboardingModule.kt)`

如果你看看不同的 dagger.android 项目，它们都有相似的样板文件。

使用 Hilt，您只需移除`@ContributesAndroidInjector`绑定，并为所有需要注入依赖关系的 Android 组件(活动、片段、视图、服务和广播接收器)添加`@AndroidEntryPoint`注释。

我们拥有的大部分模块，超过 20 个，都可以被删除，因为它们只包含`@ContributesAndroidInjector`(像 [OnboardingModule](https://github.com/google/iosched/blob/d5b168fc724b53bbe602cf92978274d07f37ba3b/mobile/src/main/java/com/google/samples/apps/iosched/ui/signin/SignInDialogModule.kt) )和 ViewModel 绑定(稍后会详细介绍)。现在您只需要将片段注释为入口点:

`[OnboardingFragment.kt](https://github.com/google/iosched/blob/9c20fdd52d446e5fdb03369e50fb196c31ae16e3/mobile/src/main/java/com/google/samples/apps/iosched/ui/onboarding/OnboardingFragment.kt)`:

```
**@AndroidEntryPoint**
class OnboardingFragment : Fragment() {...
```

对于其他类型的绑定，我们仍然使用模块来定义它们，它们需要用`@InstallIn`来注释。

`[SessionViewPoolModule.kt](https://github.com/google/iosched/blob/9c20fdd52d446e5fdb03369e50fb196c31ae16e3/mobile/src/main/java/com/google/samples/apps/iosched/ui/sessioncommon/SessionViewPoolModule.kt)`:

```
**@InstallIn(FragmentComponent::class)**
@Module
internal class SessionViewPoolModule {
```

作用域的工作就像你所期望的那样，使用熟悉的预定义的[作用域](https://dagger.dev/api/latest/dagger/hilt/android/scopes/package-summary.html)，比如`ActivityScoped`、`FragmentScoped`、`ServiceScoped`等。

`[SessionViewPoolModule.kt](https://github.com/google/iosched/blob/9c20fdd52d446e5fdb03369e50fb196c31ae16e3/mobile/src/main/java/com/google/samples/apps/iosched/ui/sessioncommon/SessionViewPoolModule.kt)`:

```
 **@FragmentScoped**
    @Provides
    @Named("sessionViewPool")
    fun providesSessionViewPool(): RecyclerView.RecycledViewPool = RecyclerView.RecycledViewPool()
```

另一个去除样板文件的工具是预定义的限定符，比如`@ApplicationContext`或`@ActivityContext`，这样你就不必在所有应用程序中创建相同的绑定。

```
 @Singleton
    @Provides
    fun providePreferenceStorage(
        **@ApplicationContext** context: Context
): PreferenceStorage = SharedPreferenceStorage(context)
```

# Android 架构组件

Hilt 真正闪光的地方是它与架构组件的集成。支持[视图模型](https://developer.android.com/topic/libraries/architecture/viewmodel)和工作管理器[工作器](https://developer.android.com/reference/kotlin/androidx/work/Worker)的注入。

在使用 Hilt 之前，这样做需要对 Dagger 有深刻的理解(或者良好的复制粘贴技能，因为大多数项目都有相同的设置)。

首先，我们为片段和活动提供了一个视图模型工厂，以通过视图模型提供者获得视图模型:

使用 Hilt，我们可以用一行代码获得视图模型的片断:

```
private val viewModel: AgendaViewModel by viewModels()
```

或者，如果您想将范围扩大到父活动:

```
private val mainActivityViewModel: MainActivityViewModel by activityViewModels()
```

其次，在使用 Hilt 之前，在视图模型中使用注入的依赖项需要使用一个复杂的多绑定设置`ViewModelKey`:

在每个模块中，您应该这样提供:

`[SessionDetailModule.kt](https://github.com/google/iosched/blob/d5b168fc724b53bbe602cf92978274d07f37ba3b/mobile/src/main/java/com/google/samples/apps/iosched/ui/sessiondetail/SessionDetailModule.kt)`:

使用 Hilt，我们将`@ViewModelInject`注释添加到 ViewModel 的构造函数中。就是这样。无需在模块中定义它们，也无需将它们添加到魔法地图中。

```
class SessionDetailViewModel **@ViewModelInject** constructor(...) { … }
```

注意，这是 [Hilt 和 Jetpack 集成](https://developer.android.com/training/dependency-injection/hilt-jetpack)的一部分，你需要定义额外的依赖来使用它们。

# 测试

## 单元测试

单元测试不会改变。您的架构应该允许独立于您如何创建对象图来测试您的类。

## 仪表测试—测试运行器设置

相对于 Dagger 来说，使用带柄的仪器化测试有所改变。这一切都从一个定制的测试运行程序开始，它允许您定义一个不同的测试应用程序:

之前:

后的[:](https://github.com/google/iosched/blob/9c20fdd52d446e5fdb03369e50fb196c31ae16e3/mobile/src/androidTest/java/com/google/samples/apps/iosched/tests/CustomTestRunner.kt)

在`newApplication`方法中，我们需要返回`CustomTestRunner_Application`，而不是返回一个带有不同匕首图的测试应用程序。实际的测试应用程序在`@CustomTestApplication`注释中定义。只有当有一些重要的初始化工作要做时，才需要这个类。在我们的例子中，它是 AndroidThreeTen，我们还添加了木材。

之前，我们必须告诉 Dagger 使用哪个`AndroidInjector`，并且我们可以扩展主应用程序:

有了 Hilt，`MainTestApplication`不能扩展你现有的应用程序，因为它已经用`@HiltAndroidApp`注释过了。我们需要创建一个新的`Application`，并在这里定义重要的初始化步骤:

测试到此为止。运行仪器测试时，该应用程序将取代`MainApplication`。

# 仪表测试—测试类别

实际的测试类也各不相同。由于我们不再有`AppComponent`(或`TestAppComponent`)，所有安装在预定义的`ApplicationComponent`中的模块和依赖项在测试时都是可用的。然而，您经常想要替换其中的一些模块。

例如，在 iosched 中，我们将`CoroutinesModule`替换为`TestCoroutinesModule`,使执行变平，因此它是同步的、可重复的和一致的。这个`TestCoroutinesModule`只是简单地添加到`androidTest`目录中，通常安装在`ApplicationComponent`中:

然而，此时我们会有“重复绑定”的错误，因为两个模块(`CoroutinesModule`和`TestCoroutinesModule`)可以提供相同的依赖关系。为了解决这个问题，我们只需使用`@UninstallModules`注释卸载测试类中的生产模块。

```
@HiltAndroidTest
**@UninstallModules(CoroutinesModule::class)** @RunWith(AndroidJUnit4::class)
class AgendaTest {...
```

此外，我们需要向测试类添加`@HiltAndroidTest`注释和`@HiltAndroidRule` JUnit 规则。但是有一件事你必须考虑到:

# HiltAndroidRule 顺序

需要注意的一点是，必须在活动启动之前处理`HiltAndroidRule`。在任何其他规则之前运行它可能是一个好主意。

在 JUnit 4.13 之前，你可以使用`RuleChain`来定义顺序，但是我个人不喜欢外部/内部规则的概念。在 4.13 中，一个简单的`order`参数被添加到`@Rule`注释中，使得它们更具可读性:

请记住，如果您不定义顺序，您将引入一个竞争条件和一个微妙的错误，它可能会在最糟糕的时候出现。

# 应该用刀柄吗？

像 Jetpack 中发布的所有东西一样，Hilt 是一种让你更快编写 Android 应用程序的方法，但它仍然是可选的。如果你对你的匕首技能有信心，你可能没有理由迁移。然而，如果你在一个多样化的团队中工作，不是每个人都吃多绑定的早餐，你应该考虑使用 Hilt 来简化你的代码库。构建时间相似，dex 方法计数与 dagger.android 相似。

同样，如果你不使用阿迪框架，现在正是时候。我推荐从 [codelab](https://codelabs.developers.google.com/codelabs/android-hilt) 开始，它没有假设任何匕首知识！