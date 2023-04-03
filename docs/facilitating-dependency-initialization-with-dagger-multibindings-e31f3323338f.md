# 使用 Dagger 多绑定简化依赖项初始化

> 原文：<https://medium.com/quick-code/facilitating-dependency-initialization-with-dagger-multibindings-e31f3323338f?source=collection_archive---------0----------------------->

![](img/8d2a7b6b6e2116d4affbcf874f29bccc.png)

*【注:本文假设你已经掌握了* [*匕首 2*](https://google.github.io/dagger/)*的基本知识*

*随着 Android 应用程序规模的增长，其依赖的数量也会增加。一些依赖关系需要在应用程序启动时进行一次性初始化，分析和跟踪库通常是这种依赖关系的嫌疑人。这些通常会被扔进你的自定义`Application`子类，结果是一大堆嘈杂的代码，除了分散开发人员的注意力和混淆潜在的错误之外，对开发人员没有任何意义。*

```
***class** SlipperySlopeApplication : Application() { **override fun** onCreate() {
        **super**.onCreate()

        **if** (BuildConfig.*DEBUG*) {
            Timber.plant(Timber.DebugTree())
        }

        Foo.initialize(**this**)

        *// ...ten thousand other dependency initializations*
    }
}*
```

*软件开发中的变化是不可避免的，能够减轻其影响是非常重要的。每当一个需要初始化的新依赖项被添加到你的应用程序中时，上面例子中的类都必须经历改变。这使得类膨胀，并有引入 bug 的风险。更糟糕的是，在某些时候，开发人员甚至不想再看这个类，当被迫尝试并理解如何导航它时，他们会发出痛苦的呻吟。*

*dagger[multi binding](https://google.github.io/dagger/multibindings.html)提供了一种简洁的方式让类抵制变化，并恢复你的应用程序及其开发者的理智。本文将介绍如何清理您的应用程序初始化代码，以最终生成一个类似于以下内容的`Application`子类:*

```
***class** LessBadApplication : Application() {

    **private val appComponent**: AppComponent = ... **override fun** onCreate() {
        **super**.onCreate()

        **appComponent**.appInitializer().initialize()
    }
}*
```

*为了获得上面代码的状态，我们将把每个依赖项的初始化算法包装到它自己的类中，向 Dagger 发送信号，告诉他我们想把它们绑定到一个集合中，然后遍历它，告诉每个依赖项执行它的初始化。这将在下面的代码示例中变得更加清晰。*

*首先，我们将从一个简单的接口开始，每个依赖项初始化包装器都将实现这个接口:*

```
***interface** Initializer {
    **fun** initialize()
}*
```

*现在让我们创建一个实现该接口的类，并提取本文第一个示例的 [Timber](https://github.com/JakeWharton/timber) 初始化代码:*

```
***class** TimberInitializer @Inject **constructor**() : Initializer {

    **override fun** initialize() {
        **if** (BuildConfig.*DEBUG*) {
            Timber.plant(Timber.DebugTree())
        }
    }
}*
```

*不要忘记包含一个`@Inject`构造函数。*

*让我们对组成的`Foo`初始化做同样的事情:*

```
***class** FooInitializer @Inject **constructor**(
    **private val context**: Context
) : Initializer {

    **override fun** initialize() {
        Foo.initialize(**context**)
    }
}*
```

*请注意，如果这些`Initializer`有自己的依赖项，这不是问题。如果它在你的 Dagger 图中可用，那么它也可以在这些构造函数中。*

*接下来，我们将定义多绑定魔法发生在哪里。像这样创建一个 Dagger 模块:*

```
*@Module
**abstract class** InitializerModule {

    @Binds
    @IntoSet
    **abstract fun** timberInitialize(
        timberInitializer: TimberInitializer
    ): Initializer

    @Binds
    @IntoSet
    **abstract fun** fooInitializer(
        fooInitializer: FooInitializer
    ): Initializer
}*
```

*使用`@IntoSet`注释，我们告诉 Dagger，我们希望所有这些新`Initializer`的实例都可以通过可注入的`Set<Initializer>`来使用。让我们将这样一个`Set`注入另一个定制类:*

```
***class** AppInitializer @Inject **constructor**(
    **private val initializers**: Set<@JvmSuppressWildcards Initializer>
) : Initializer {

    **override fun** initialize() {
        **initializers**.*forEach*(Initializer::initialize)
    }
}*
```

*然后这个`AppInitializer`类可以被注入或者暴露给你的定制`Application`子类，并调用它的`initialize()`方法——一劳永逸！注意，在 Kotlin 中，这个例子中的`@JvmSuppressWildcards`注释是编译成功的必要条件。你可以在这里阅读更多关于为什么[的内容](http://adavis.info/2017/08/jvmsuppresswildcards-biggest-annoyance-kotlin.html)。*

*现在，每当添加需要初始化的新依赖项时，只需将初始化算法封装到一个`Initializer`具体化中，并将其添加到`InitializerModule`绑定列表中。移除一个也同样简单。拥有`AppInitializer`实例的类将永远不会因为这些原因而改变，所有应用程序的依赖项初始化将整齐地包含在它们各自的文件中。*

*你可以在这里找到这个概念的完整例子:[https://github.com/nihk/DaggerMultibindingsFun](https://github.com/nihk/DaggerMultibindingsFun)*

## *2021/04/01 —更新*

*一个[对这篇文章的回复](https://proandroiddev.com/abusing-dagger-with-initializers-a1e742024ac8)提出了一个关于集合`Initializer`的顺序不被保证的很好的观点。如果您的一个`Initializer`依赖于另一个`Initializer`在它自己初始化之前被初始化(仍然跟随？)，那么你的应用可能不会像你期望的那样运行。例如，如果`FooInitializer`在其初始化期间记录到 Timber，并且在`TimberInitializer`之前被初始化，那么该日志将会丢失。*

*使用 Dagger 的 API 无法控制`@IntoSet`成员的顺序。然而，我不是那种把婴儿和洗澡水一起倒掉的人；我认为有一个现实的方法来解决这个问题，同时仍然使用匕首多绑定。*

*让我们首先考虑一下，如果我们不使用这种多绑定模式，我们将如何设置我们的代码，因为我们想要定义一个初始化顺序。我们将回到我在本文前面描述的臃肿的状态，但是现在加入了订单管理。膨胀使顺序变得模糊，不清楚哪些初始化调用是故意排序的，哪些调用的顺序实际上并不重要。以下面的例子为例:*

```
***class** SlipperierSlopeApplication : Application() { **override fun** onCreate() {
        **super**.onCreate()

        **if** (BuildConfig.*DEBUG*) {
            Timber.plant(Timber.DebugTree())
        }

        Foo.initialize(**this**)
        Bar.initialize(**this**)
        Baz.initialize() }
}*
```

*如果`Foo`的初始化记录了一些木材，那么木材种植需要在`Foo`的调用之前被调用。但是`Bar`和`Baz`初始化呢:它们是顺序相关的吗？不清楚。*

*如果我们坚持使用匕首多重捆绑，我们如何解决这个问题？回复文章中的一位评论者建议在`Initializer`界面上添加一个抽象的`sortOrder(): Int`函数来帮助指定顺序。我不喜欢这个想法，因为一个`Initializer`不应该单独关心或知道它的依赖项或兄弟`Initializer`的状态，而且不是每个`Initializer`都关心它应该以什么顺序初始化。不得不管理分散在大型项目中的`sortOrder`值也是站不住脚的。*

*我对这个问题提出的解决方案是简单地注入一种方法来告诉`AppInitializer`(本文前面提到过)如何对它的一组`Initializer`进行排序。一旦这个集合被排序，`AppInitializer`就可以遍历它的集合，并以一种安全且有意的方式对每个集合调用 initialize。自定义`Comparator`可以对器械包进行排序:*

```
***class** AppInitializerComparator(
    **private val priorities**: List<Class<**out** Initializer>>
) : Comparator<Initializer> {
    // ...
}*
```

*像这样使用:*

```
***class** AppInitializer @Inject **constructor**(
    **private val initializers**: Set<@JvmSuppressWildcards Initializer>,
    **private val comparator**: AppInitializerComparator
) : Initializer {

    **override fun** initialize() {
        **initializers** .*sortedWith*(**comparator**)
            .*forEach*(Initializer::initialize)
    }
}*
```

*传入`AppInitializerComparator`的`priorities`值定义了`Initializer`初始化的顺序。只有对订单感兴趣的人才需要成为该集合的一部分。对于我到目前为止一直在谈论的例子，`priorities`只有一个元素:对`TimberInitializer`的引用。Dagger 模块是定义什么进入那个`priorities`值的好地方，因为 Dagger 模块是负责配置的东西。对于这个例子，它看起来像这样:*

```
*@Provides
**fun** appInitializerComparator(): AppInitializerComparator {
    **val** priorities = *listOf*<Class<**out** Initializer>>(
        TimberInitializer::**class**.*java* )
    **return** AppInitializerComparator(priorities)
}*
```

*注意缺少对`Foo`或`Bar`或`Baz`初始化的引用。所有这些`priorities`值都表示“首先初始化木材”其他的`Initializer`我们就不用管了，现在`TimberInitializer`保证会被`AppInitializer`先调用。`Foo`可以在以后安全地初始化并成功地登录到 Timber。*

*`AppInitializerComparator`的`Comparator`实现可以以多种方式完成，但是它的要点是您想要比较每个元素的索引，就像这样:*

```
***class** AppInitializersComparator(
    **private val priorities**: List<Class<**out** Initializer>>
) : Comparator<Initializer> {
    **override fun** compare(first: Initializer, second: Initializer): Int {
        **val** firstIndex = **priorities**.indexOf(first::**class**.*java*).*handleNotFound*()
        **val** secondIndex = **priorities**.indexOf(second::**class**.*java*).*handleNotFound*()
        **return** firstIndex.compareTo(secondIndex)
    }

    **private fun** Int.handleNotFound(): Int {
        **return if** (**this** == -1) {
            Int.**MAX_VALUE** } **else** {
            **this** }
    }
}*
```

*需要订单集的不仅仅是`TimberInitializer`？然后简单地相应安排`priorities`集合。这个过程并不比在没有 Dagger 的情况下在您的`Application.onCreate`中设置它们的顺序更加手动，但是它更干净并且更容易管理。*