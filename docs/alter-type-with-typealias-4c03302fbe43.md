# 用 typealias 改变类型

> 原文：<https://medium.com/androiddevelopers/alter-type-with-typealias-4c03302fbe43?source=collection_archive---------2----------------------->

![](img/32f9975fd0c3198a5203e0e15df3b9e1.png)

## 科特林词汇:`typealias`

当类型定义因为不可读、不具表达性或者太长而偏离了代码的含义时，Kotlin 正好有适合你的特性:`[typealias](https://kotlinlang.org/docs/reference/type-aliases.html)`！Typealias 允许您在不引入新类型的情况下为类或函数类型提供替换名称。

# Typealias 用法

您可以使用类型别名来命名函数类型:

```
typealias TeardownLogic = () -> Unit
fun onCancel(teardown : TeardownLogic){ }private typealias OnDoggoClick = (dog: Pet.GoodDoggo) -> Unit
val onClick: OnDoggoClick
```

这样做的缺点是名称隐藏了传递给函数的参数，降低了可读性:

```
typealias TeardownLogic = () -> Unit //or
typealias TeardownLogic = (exception: Exception) -> Unitfun onCancel(teardown : TeardownLogic){
// can’t easily see what information we have 
// available in TeardownLogic
}
```

`Typealias`允许您缩短长的通用名称:

```
typealias Doggos = List<Pet.GoodDoggo>fun train(dogs: Doggos){ … }
```

虽然这是可能的，但是问问你自己你是否真的应该做这件事。使用类型别名真的能让你的代码更有意义和可读性吗？

> 问问自己，使用类型别名是否会让代码更容易理解。

如果您正在使用一个长类名，您可以使用`typealias`来缩短它:

```
typealias AVD = AnimatedVectorDrawable
```

但是对于这个用例，更好的选择是使用 [**导入别名**](https://kotlinlang.org/docs/reference/packages.html#imports) :

```
import android.graphics.drawable.AnimatedVectorDrawable **as** AVD
```

在这种情况下，使用快捷方式并不能真正提高可读性，IDE 可以帮助自动完成类名。

但是，当您需要区分来自不同包的同名类时，导入别名变得特别有用:

```
import io.plaidapp.R **as** appRimport io.plaidapp.**about**.R
```

类型别名是在类之外定义的，所以在使用它们的时候一定要考虑它们的可见性。

# `Typealias`用于多平台项目

当使用[多平台项目](https://kotlinlang.org/docs/reference/platform-specific-declarations.html)时，您可以在公共代码中指定接口，然后在平台代码中实现这些接口。为了使这更容易，Kotlin 提供了一种预期和实际声明的机制。

公共代码中的接口是预期的声明，使用`expect`关键字定义。平台代码中的实现使用`actual`关键字定义。如果实现已经存在于其中一个平台中，并且所有期望的方法都具有完全相同的签名，那么您可以使用`typealias`将类名映射到期望的名称。

```
expect annotation class Testactual typealias Test = org.junit.Test
```

# 在后台

类型别名不会引入新类型。例如，`train`函数的反编译代码将只使用一个列表:

```
// Kotlin
typealias Doggos = List<Pet.GoodDoggo>
fun train(dogs: Doggos) { … }// Decompiled Java code
public static final void train(@NotNull List dogs) { … }
```

> 类型别名不会引入新类型。

您不应该依赖类型别名来进行编译时类型检查。相反，您应该考虑使用不同的类型或内联类。例如，假设我们有以下函数:

```
fun play(dogId: Long)
```

当我们试图传递错误的 id 时，为`Long`创建类型别名不会帮助我们防止错误:

```
typealias DogId = Longfun pet(dogId: DogId) { … }fun usage() {
    val cat = Cat(1L)
    pet(cat.catId) // compiles
}
```

类型别名是为现有类型提供更短或更有意义的名称的一种方式。如果你正在寻找的是额外的安全性，你可能想创造一种新的类型。