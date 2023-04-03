# 从我的第一个多平台 Kotlin 项目中得到的教训

> 原文：<https://blog.kotlin-academy.com/lessons-from-my-first-multiplatform-kotlin-project-d4e311f15874?source=collection_archive---------0----------------------->

我刚刚完成了 [good 多平台架构](/architecture-for-multiplatform-development-in-kotlin-cc770f4abdfd)的 [Kotlin 多平台项目](https://github.com/MarcinMoskala/KotlinAcademyApp)，有三个不同的客户:

*   [安卓](https://play.google.com/store/apps/details?id=org.kotlinacademy.mobile)
*   [Web in React](https://github.com/JetBrains/create-react-kotlin-app)
*   [中的桌面 TornadoFX](https://github.com/edvin/tornadofx) (该零件由 [Edvin Syse](https://twitter.com/edvinsyse) 制造)

这个解决方案的美妙之处在于，所有这些客户端都共享业务逻辑！(查看[这篇文章](/architecture-for-multiplatform-development-in-kotlin-cc770f4abdfd)描述了这是如何实现的)。在执行过程中，我不得不挑战自己很多。我开始有一些想法和理解，但我不得不在实施过程中更新它们。这些是我想与你们分享的宝贵经验。我相信它们很重要，因为 Kotlin 多平台开发将会很大。它提供了代码共享的巨大可能性，同时保持了所有平台的原生性。希望有一天你也会加入这个潮流。现在，享受我所学到的；)

![](img/eea18fc69cf1efbe11134f798d355142.png)

# 预期声明背后的想法

在 Kotlin 公共模块中，我们可以定义`expected`声明，为此我们需要在每个平台模块中指定`actual`声明(参见[文档](https://kotlinlang.org/docs/reference/multiplatform.html)或[本文](/multiplatform-native-development-in-kotlin-now-with-ios-a8546f436eec)以了解公共、平台和常规模块)。这是非常重要和强大的机制，因为没有它，我们就不能在公共模块中使用特定于平台的类型。

项目的例子是`DateTime` —代表具体时间点的对象。由于我[在](https://github.com/MarcinMoskala/KotlinAcademyApp/blob/master/common/src/main/kotlin/org/kotlinacademy/DateTime.kt) `[common](https://github.com/MarcinMoskala/KotlinAcademyApp/blob/master/common/src/main/kotlin/org/kotlinacademy/DateTime.kt)` [模块](https://github.com/MarcinMoskala/KotlinAcademyApp/blob/master/common/src/main/kotlin/org/kotlinacademy/DateTime.kt)中将其定义为 `[expected](https://github.com/MarcinMoskala/KotlinAcademyApp/blob/master/common/src/main/kotlin/org/kotlinacademy/DateTime.kt)` [声明，并且我在](https://github.com/MarcinMoskala/KotlinAcademyApp/blob/master/common/src/main/kotlin/org/kotlinacademy/DateTime.kt)`[common-jvm](https://github.com/MarcinMoskala/KotlinAcademyApp/blob/master/common-jvm/src/main/kotlin/org/kotlinacademy/DateTimeJvm.kt)`和`[common-js](https://github.com/MarcinMoskala/KotlinAcademyApp/blob/master/common-js/src/main/kotlin/org/kotlinacademy/DateTimeJs.kt)`中提供了`actual`声明，所以我可以在数据模型的`[News](https://github.com/MarcinMoskala/KotlinAcademyApp/blob/master/common/src/main/kotlin/org/kotlinacademy/data/News.kt)`类中使用它。这听起来可能是无辜的，但是`[News](https://github.com/MarcinMoskala/KotlinAcademyApp/blob/master/common/src/main/kotlin/org/kotlinacademy/data/News.kt)`在这个项目中几乎无处不在！它用于所有层中的所有客户端。我在 API(我必须为此定义序列化)和后台逻辑中使用它。我可以自由地为这样的对象使用特定于平台的类型，这一点非常重要。它极大地提高了我们的数据模型的表达能力，如果没有它，我将被迫在每次使用之前序列化或反序列化这样的对象。

# 平台模块是 JS，JVM 或者 Native。不是安卓也不是 iOS

当我开始我的项目时，我设想我将省略`common-js`模块，直接在`web`模块中提供`expected`声明。这可能是有利可图的，因为它目前是这个项目中唯一的 Kotlin/JS 模块。事情不是这样的。`web`、`android`、`ios`、`desktop`都是常规模块，不能是平台模块(没有[变通](https://youtrack.jetbrains.com/issue/KT-18462))。

最初我把它当作一个问题——这迫使我提取额外的模块。当我将网络存储库定义为预期声明时，它迫使我在 Android 和桌面之间共享网络。我担心这会引起一些问题。它实际上没有，我发现这实际上是有益的！它迫使我们在平台模块中定义元素，而不是为每个客户定义它们。另一个好处是，我必须在单独的模块中提取 Kotlin/JS 库，现在我知道当我添加 Firefox 和 Chrome 扩展客户端时，我已经实现了联网。这种解决方案使整个结构稍微复杂了一点，但同时也为新平台带来了更好的代码可重用性和更好的可伸缩性。

# MVP 与多平台设计完美结合

我想知道 [MVP 架构](https://medium.com/@marcinmoskala/mvc-vs-mvp-vs-mvvm-vs-mvi-ce72907d330)是否适合这样的大型多平台项目。担忧是合理的——不同的平台代表了完全不同的思维方式。这就是为什么当我发现这种设计模式如此适合所有当前实现的客户端时，我感到震惊。几乎所有的演示者最初都是为 Android 编写的，然后很容易在所有客户端重用。

你可以在本文中找到更多关于它的内容，但是为了给你一些 MVP 如何适应不同模块的直觉，让我们看看在加载状态下我们是如何表达视图的。只有演示者可以决定视图是否处于这种状态，他们通过更改其`view`属性来发出信号:

```
**class** NewsPresenter(**val view**: NewsView) : BasePresenter() {

    **fun** onRefresh() {
        **view**.**refresh** = **true
        ...
    }

    ...
}**
```

为了使视图具有通用性和可测试性，它们被隐藏在接口后面。`loading`在这里表示为`Boolean`类型属性:

```
**interface** NewsView: BaseView {
    **var loading**: Boolean
    **...**
}
```

在 Android 中，我使用[KotlinAndroidViewBinding](https://github.com/MarcinMoskala/KotlinAndroidViewBindings)将该属性与进度可见性绑定:

```
**override var** loading **by** bindToVisibility(R.id.*progressView)*
```

在 [TornadoFX](https://github.com/edvin/tornadofx) 中，有称为属性的对象用于表示视图状态。我们可以很容易地将它们与科特林的财产捆绑在一起:

```
**private val loadingProperty** = SimpleBooleanProperty()
**override var loading by loadingProperty**
```

进度栏可见性绑定到此属性:

```
*progressbar* **{** *removeWhen*(**loadingProperty**.not())
**}**
```

在 React 中，视图状态由每个组件的单个对象表示(这就是我们如何调用 React 视图)。每次在该对象中应用一些改变时，显示的视图也会改变(改变必须在`setState`块中完成)。在这个架构中，我们可以使用`observable` delegate 在属性值改变时总是改变状态:

```
**override var loading**: Boolean **by** observable(**false**) **{** _, _, n **->** *setState* **{ state**.**loading** = n **}
}**
```

当`state`的`loading`属性为`true`时，我们显示加载视图:

```
**override fun** RBuilder.render(): ReactElement? = **when** {
    **state**.**loading** != **false** -> *loadingView*()
    **state**.**error** != **null** -> *errorView*(**state**.**error**!!)
    **state**.**newsList** != **null** -> *newsListView*()
    **else** -> *div* **{ }** }
```

正如你所看到的，MVP 以完全不同的方式适合每一种架构，但是它实际上对所有的架构都很有效。

# 双向沟通的方式

不同类型的模块之间有不同的通信方式。有 3 种类型的模块:

*   通用模块(例如`common`、`common-client`)
*   平台模块(例如`common-js`、`common-jvm`)
*   常规模块(例如`android`、`desktop`、`web`)

每个模块都可以使用更高层模块中的所有东西。在常规模块中，我们可以使用来自所有相关平台模块和所有相关公共模块的所有内容。类似地，平台模块可以使用它所依赖的公共模块中的任何东西。

在其他方向上更难沟通。如果上层需要下层的东西怎么办？当公共模块需要平台模块的东西时，我们可以通过指定`expected`声明来处理。语言将迫使我们为每个平台模块指定`actual`声明。

当我需要只能在常规模块中指定的公共和平台模块中的值时，我遇到了更大的问题:

*   `UI` —协程的用户界面上下文。
*   `BaseURL`—API 的基本 URL 地址。它不同是因为在 Android 模拟器中不同的地址需要被用来指导计算机本地主机。

有两种方法可以解决这个问题:

*   所有上层元素都是由常规模块创建的，因此我们可以将这些值传递给所有需要它们的对象(在创建过程中，我们可以通过构造函数将`UI`传递给所有演示者)。遗憾的是，这种解决方案不具备真正的可扩展性。
*   我们可以用`notNull` delegate 定义公共顶级属性或对象声明属性，并在常规模块初始化时填充它们。

这就是我在这种情况下的结局。我将`UI`定义为顶级属性:

```
**var** *UI*: CoroutineContext **by** notNull()
```

在初始化过程中，它由特定于客户端的对象填充:

```
**class** App: Application() {

    **override fun** onCreate() {
        **super**.onCreate()
        *UI* = *AndroidUI
        ...*
    }
}
```

注意，可以很容易地为单元测试指定不同值:

```
@Before
**fun** setUp() {
    *UI* = Unconfined
}
```

尽管这种解决方案看起来不错，但不应该过于频繁地使用。以这种方式指定值并不直观，我们应该首先考虑将这样的对象作为参数传递。

# 摘要

这些是关于多平台 Kotlin 编程的重要经验。我希望它能帮助每一个在这方面开始冒险的人。重要的一课——优化代码重用和项目可伸缩性和可测试性的架构——可以在[这篇文章](/architecture-for-multiplatform-development-in-kotlin-cc770f4abdfd)中找到。

[](/architecture-for-multiplatform-development-in-kotlin-cc770f4abdfd) [## Kotlin 中多平台本机开发的有效架构

### 我的导师经常说“如果你在一个项目中使用 Ctrl-C Ctrl-V，你就做错了”。这个…

blog.kotlin-academy.com](/architecture-for-multiplatform-development-in-kotlin-cc770f4abdfd) 

## 学到了什么？单击👏说“谢谢！”并帮助他人找到这篇文章。

如果你认为这很重要，与他人分享。

你需要 Kotlin 工作室吗？访问[我们的网站](https://www.kt.academy/)，看看我们能为您做些什么。

要在 Twitter 上提到我，请使用 [@marcinmoskala](https://twitter.com/marcinmoskala) 。

[![](img/5ce68714efe3efc036e06786166954ff.png)](http://eepurl.com/diMmGv)

喜欢的话记得**拍**。请注意，如果您按住鼓掌按钮，您可以留下更多的掌声。

![](img/f36a792ac0eb95fc577e6f4125dba956.png)