# 实用指南剑柄与科特林

> 原文：<https://medium.com/androiddevelopers/a-pragmatic-guide-to-hilt-with-kotlin-a76859c324a1?source=collection_archive---------1----------------------->

![](img/b07555fbeb7cabf07ce1f73109da516b.png)

## 在 Android 应用中使用依赖注入的简单方法

[Hilt](https://developer.android.com/training/dependency-injection/hilt-android) 是建立在 [Dagger](https://developer.android.com/training/dependency-injection/dagger-basics) 之上的一个新的依赖注入库，简化了它在 Android 应用中的使用。本指南展示了一些代码片段的核心功能，以帮助您开始在项目中使用 Hilt。

# 设置刀柄

要在您的应用程序中设置 Hilt，请先遵循 [Gradle Build 设置](https://dagger.dev/hilt/gradle-setup)指南。

安装完所有的依赖项和插件后，用`@HiltAndroidApp`注释你的`Application`类以使用 Hilt。您不需要做任何其他事情或者直接调用它。

# 定义和注入依赖关系

当您编写使用依赖注入的代码时，有两个主要组件需要考虑:

1.  具有要注入的依赖项的类。
2.  可以作为依赖项注入的类。

这些并不相互排斥，在许多情况下，你的类既可注入又有依赖关系。

## 使依赖关系可注入

要在 Hilt 中创建可注入的东西，你必须告诉 Hilt 如何创建那个东西的一个实例。这些指令被称为*绑定*。

有三种方法来定义一个绑定。

1.  用`@Inject`标注构造函数
2.  在模块中使用`@Binds`
3.  在一个模块中使用`@Provides`

⮕ **用** `**@Inject**`标注构造器

任何类都可以有一个用`@Inject`注释的构造函数，这使得它可以作为项目中任何地方的依赖项。

⮕ **使用模块**

在 Hilt 中实现可注入的另外两种方法涉及到使用模块。

一个 [Hilt 模块](https://dagger.dev/hilt/modules)可以被认为是一个“配方”的集合，告诉 Hilt 如何创建一个没有构造器的东西的实例——比如一个接口或者一个系统服务。

此外，在您的测试中，任何模块都可以用不同的模块来替换。例如，这使得用模拟替换接口实现变得很容易。

模块安装在使用`@InstallIn`注释指定的[手柄组件](https://dagger.dev/hilt/components.html)中。稍后我会更详细地解释这一点。

*选项 1:使用* `*@Binds*` *为一个接口*创建绑定

如果您想在请求`Milk`时在代码中使用`OatMilk`，请在模块中创建一个抽象方法并用`@Binds`对其进行注释。注意，`OatMilk`本身必须是可注入的，这可以通过用`@Inject`注释其构造函数来实现。

*选项二:使用* `*@Provides*` *创建工厂功能*

当不能直接构造实例时，可以创建一个提供者。提供者是返回对象实例的工厂函数。

这方面的一个例子是需要从上下文中获得的系统服务，如`ConnectivityManager`。

默认情况下，`Context`对象是可注入的，只要用`@ApplicationContext`或`@ActivityContext`对其进行注释。

## 注入依赖性

一旦你的依赖项是可注入的，你可以用两种方式使用 Hilt 注入它们。

1.  作为构造函数参数
2.  作为字段

**⮕作为构造函数的参数**

如果构造函数标有`@Inject`，那么 Hilt 会根据您为这些类型定义的绑定注入所有的参数。

**⮕作为地头**

如果这个类是一个入口点，这里用`@AndroidEntryPoint`注释指定(下一节将详细介绍)，那么所有用`@Inject`注释的字段都会被注入。

用`@Inject`标注的字段必须是公共的。使它们成为`lateinit`也很方便，以避免使它们为空，因为它们在注入前的初始值是`null`。

注意，只有当你的类必须有一个不带参数的构造函数，比如`Activity`时，将依赖项作为字段注入才有用。在大多数情况下，您会希望通过构造函数参数注入。

# 其他重要概念

## 入口点

还记得我说过在很多情况下，你的类是通过注入*而*被注入依赖项而创建的吗？在某些情况下，你会有一个不是通过依赖注入*创建*的类，但是仍然有依赖注入其中。一个很好的例子就是 activities，它通常是由 Android 框架而不是 Hilt 创建的。

这些类是 [*到 Hilt 的依赖图的入口点*](https://dagger.dev/hilt/entry-points.html) ，Hilt 需要知道它们有需要注入的依赖。

**⮕安卓入口点**

您的大多数入口点将是这些所谓的 [Android 入口点](https://dagger.dev/hilt/android-entry-point.html)之一:

*   活动
*   碎片
*   视角
*   服务
*   广播接收机

如果是这样，用`@AndroidEntryPoint`注释。

**⮕其他切入点**

大多数应用程序只需要 Android 入口点，但是如果你正在与非 Dagger 库或者还不被 Hilt 支持的 Android 组件交互，你可能需要创建你自己的入口点来手动访问 Hilt 图。你可以阅读更多关于[把任意类变成入口点](https://dagger.dev/hilt/entry-points.html)的内容。

## 视图模型

一个`ViewModel`是一个特例:它没有被直接实例化，因为框架需要创建它们，但它也不是 Android 的入口点。相反，`ViewModel`使用特殊的`@ViewModelInject`注释，当使用`by viewModels()`创建依赖项时，允许 Hilt 将依赖项注入其中，类似于`@Inject`对其他类的工作方式。

如果您需要访问您的`ViewModel`中保存的状态，通过添加`@Assisted`注释注入一个`[SavedStateHandle](https://developer.android.com/reference/androidx/lifecycle/SavedStateHandle)`作为构造函数参数。

要使用`@ViewModelInject`，您需要添加更多的依赖项。更多信息见[手柄和喷射包集成](https://developer.android.com/training/dependency-injection/hilt-jetpack)。

## 成分

每个模块安装在一个用`@InstallIn(*<component>*)`指定的[手柄组件](https://dagger.dev/hilt/components.html)内。模块的组件主要用于防止在错误的位置意外注入依赖项。例如，`@InstallIn(ServiceComponent.class)`会阻止注释模块中的绑定和提供者在活动中使用。

此外，绑定的范围可以是模块所在的组件。这让我想到…

## 领域

默认情况下，绑定未被划分。在上面的例子中，这意味着每次你注入`Milk`，你会得到一个`OatMilk`的新实例。如果您添加了`@ActivityScoped`注释，您将把绑定范围扩大到`ActivityComponent`。

既然您的模块已经确定了范围，那么 Hilt 将只为每个活动实例创建一个`OatMilk`。此外，`OatMilk`实例将被绑定到活动的生命周期——它将在活动的`onCreate()`被调用时被创建，在活动的`onDestroy()`被调用时被销毁。

在这种情况下，`milk`和`moreMilk`都将指向同一个`OatMilk`实例。但是，如果您有多个`LatteActivity`实例，那么它们都有自己的`OatMilk`实例。

相应地，注入到该活动中的其他依赖项具有相同的范围，因此它们也将使用相同的`OatMilk`实例:

范围取决于您的模块安装在哪个组件中，例如`@ActivityScoped`只能应用于安装在`ActivityComponent`中的模块内部的绑定。

该范围还决定了注入实例的生命周期:在这种情况下，当`onCreate()`被`LatteActivity`调用时，创建由`Fridge`和`LatteActivity`使用的单个`Milk`实例，并在其`onDestroy()`中销毁。这也意味着我们的`Milk`不会在配置变更中“幸存”,因为这将涉及到在活动中调用`onDestroy()`。您可以通过使用生命周期更长的作用域来克服这个问题，比如`@ActivityRetainedScope`。

有关可用范围、它们对应的组件以及它们遵循的相应生命周期的列表，请参见[手柄组件](https://dagger.dev/hilt/components.html)。

## 提供商注入

有时，您希望对注入实例的创建进行更直接的控制。例如，根据您的业务逻辑，您可能希望仅在需要时注入某个东西的一个或几个实例。在这种情况下，可以使用`[dagger.Provider](https://dagger.dev/api/latest/dagger/Provider.html)`。

无论依赖项是什么以及如何注入，都可以使用提供者注入。任何可以被注入的东西都可以被包装在`Provider<…>`里面，让它使用 provider 注入来代替。

依赖注入框架(如 Dagger 和 [Guice](https://github.com/google/guice) )传统上与大型复杂项目相关联。然而，由于易于上手和设置，Hilt 将 Dagger 的所有功能打包在一个包中，可以被任何类型的应用程序使用，无论它的代码库有多大。

如果你想了解更多关于 Hilt 的信息，它是如何工作的，以及你可能会发现有用的其他特性，去[的官方网站](https://dagger.dev/hilt/)，在那里你可以找到更详细的概述和参考文档。