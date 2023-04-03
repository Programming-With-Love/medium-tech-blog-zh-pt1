# Kotlin 的零成本*抽象

> 原文：<https://medium.com/androiddevelopers/zero-cost-abstractions-in-kotlin-19b953f3a936?source=collection_archive---------1----------------------->

![](img/32f9975fd0c3198a5203e0e15df3b9e1.png)

## `inline`科特林词汇:类

**条款和条件适用*

> ⚠️*【2021 年 5 月更新】内联类的内联修饰符现在已经过时了！* ⚠️

*警告:这篇博文涵盖了 Kotlin 的一个实验性特性，可能会有变化。本文是使用 Kotlin 1.3.50 编写的。*

类型安全防止我们犯错误或者以后不得不调试它们。对于 Android 资源类型，如`String`、`Font`或`Animation`资源，我们可以使用`androidx.annotations`，如`@StringRes`、`@FontRes`，Lint 强制我们传递正确类型的参数:

```
fun myStringResUsage(@StringRes string: Int){ }// Error: expected resource of type String
myStringResUsage(1)
```

如果我们的 id 不是 Android 资源，而是像`Doggo`或`Cat`这样的域对象的 id，那么区分这两个`Int`id 就不容易了。为了实现类型安全，即编码狗的 id 与猫的 id 不同，您必须将您的 id 包装在一个类中。这样做的缺点是，你要付出性能代价，因为需要实例化一个新对象，而实际上，你所需要的只是一个原语。

Kotlin [**内联类**](https://kotlinlang.org/docs/reference/inline-classes.html) 允许你创建这些包装器类型**而没有**性能成本。这是 Kotlin 1.3 中添加的一个实验性特性。内联类必须正好有一个属性。在编译时，内联类实例在可能的情况下被替换为它们的底层属性(取消装箱)，从而降低了常规包装类的性能成本。这对于被包装的对象是原始类型的情况更为重要，因为编译器已经对它们进行了优化。因此，如果可能的话，在内联类中包装基元类型会导致在运行时将值表示为基元值。

```
inline class DoggoId(val id: Long)
data class Doggo(val id: DoggoId, … )// usage
val goodDoggo = Doggo(DoggoId(doggoId), …)
fun pet(id: DoggoId) { … }
```

# 获得内嵌

内联类的唯一作用是作为一个类型的包装器，因此 Kotlin 实施了许多限制:

*   不超过一个参数(对类型没有限制)
*   没有[支持字段](https://kotlinlang.org/docs/reference/properties.html#backing-fields)
*   没有初始化块
*   没有扩展类

但是，内联类可以:

*   从接口继承
*   具有属性和功能

```
interface Id
inline class DoggoId(val id: Long) : Id {

    val stringId
    get() = id.toString() fun isValid()= id > 0L
}
```

> ⚠️警告: [**Typealias**](https://kotlinlang.org/docs/reference/type-aliases.html) 可能看起来类似于内联类，但是，类型别名只是为现有类型提供了一个替代名称，而内联类**创建了一个新类型**。

# 表示—包装或不包装？

由于内联类相对于手动包装类的最大优势是内存分配的影响，重要的是要记住这很大程度上取决于您在哪里以及如何使用内联类。一般规则是，如果内联类用作另一种类型，参数将被包装(装箱)。

> 当用作另一种类型时，参数被装箱

例如，如果您在需要一个对象或任何对象时使用它，例如在集合、数组中或作为可空对象。根据您如何检查两个内联类的结构相等性，它们中的一个或一个都不会被装箱:

```
val doggo1 = DoggoId(1L)val doggo2 = DoggoId(2L)
```

`doggo1 == doggo2`—doggo 1 和 doggo2 都没有被装箱

`doggo1.equals(doggo2)` — doggo1 被用作原语，但 doggo2 被装箱

# 在后台

让我们来看一个简单的内联类:

```
interface Id
inline class DoggoId(val id: Long) : Id
```

让我们一步一步地看看反编译的 Java 编程语言代码是什么样子，以及它们对使用内联类有什么影响。你可以在这里找到完整的反编译代码。

# 引擎盖下——构造函数

DoggoId 有两个构造函数:

*   私人合成建造商`DoggoId(long id)`
*   一次公开`constructor-impl`

创建对象的新实例时，使用公共构造函数:

```
val myDoggoId = DoggoId(1L)// decompiled
static final long myDoggoId = DoggoId.constructor-impl(1L);
```

如果我们尝试用 Java 创建 doggo id，我们会得到一个错误:

```
DoggoId u = new DoggoId(1L);
// Error: DoggoId() in DoggoId cannot be applied to (long)
```

> 你不能从 Java 实例化一个内联类

参数化的构造函数是私有的，第二个构造函数的名称中包含一个`-`，这在 Java 中是一个无效字符。这意味着内联类不能从 Java 实例化。

# 引擎盖下—参数用法

id 以两种方式公开:

*   作为原语，通过`getId`
*   通过一个创建 DoggoId 新实例的`box_impl`方法作为一个对象

当在可以使用原语的地方使用内联类时，Kotlin 编译器将知道这一点，并将直接使用原语:

```
fun walkDog(doggoId: DoggoId) {}// decompiled Java code
public final void walkDog_Mu_n4VY(**long** doggoId) { }
```

> 当需要一个对象时，Kotlin 编译器将使用我们原语的装箱版本，每次都会创建一个新的对象。

当需要一个对象时，Kotlin 编译器将使用我们原语的装箱版本，每次都会创建一个新的对象**。例如:**

## 可空对象

```
fun pet(**doggoId: DoggoId?**) {}// decompiled Java code
public static final void pet_5ZN6hPs/* $FF was: pet-5ZN6hPs*/(@Nullable InlineDoggoId doggo) {}
```

因为只有对象可以为空，所以使用装箱的实现。

## 收集

```
val doggos = listOf(myDoggoId)// decompiled Java code
doggos = CollectionsKt.listOf(**DoggoId.box-impl(myDoggoId)**);
```

`CollectionsKt.listOf`的签名是:

```
fun <T> listOf(element: T): List<T>
```

因为这个方法需要一个对象，所以 Kotlin 编译器将我们的原语打包，确保使用了一个对象。

## 基类

```
fun handleId(id: Id) {}fun myInterfaceUsage() {
    handleId(myDoggoId)
}// decompiled Java code
public static final void myInterfaceUsage() {
    handleId(**DoggoId.box-impl(myDoggoId)**);
}
```

因为这里我们期望一个超类型，所以使用了装箱的实现。

# 引擎盖下—平等检查

Kotlin 编译器尽可能使用 unboxed 参数。为此，内联类有 3 种不同的等式实现:一个 equals 的重写和 2 个生成的方法:

`**doggo1.equals(doggo2)**`

equals 方法调用一个生成的方法:`equals_impl(long, Object)`。由于 equals 需要一个对象，因此 doggo2 值将被装箱，但 doggo1 将被用作原语:

```
DoggoId.equals-impl(doggo1, DoggoId.box-impl(doggo2))
```

`**doggo1 == doggo2**`

使用`==`生成:

```
DoggoId.equals-impl0(doggo1, doggo2)
```

所以在`==`的情况下，原语将被用于 doggo1 和 doggo2。

`**doggo1 == 1L**`

如果 Kotlin 能够确定 doggo1 实际上是一个 long 类型，那么您可以预期这个等式检查会起作用。但是，因为我们使用内联类是为了它们的类型安全，那么，编译器要做的第一件事就是检查我们要比较的两个对象的类型是否相同。而既然不是，我们会得到一个编译错误:*运算符* `*==*` *不能应用于 long 和 DoggoId* 。最后，对于编译器来说，这就好像我们说了`cat1 == doggo1`，这肯定不是真的。

`**doggo1.equals(1L)**`

这种相等性检查确实可以编译，因为 Kotlin 编译器使用 equals 实现，该实现需要一个 long 和一个 Object。但是，因为这个方法做的第一件事是检查对象的类型，这个等式检查将是假的，因为对象不是 DoggoId。

# 用原语和内联类参数重写函数

Kotlin 编译器允许使用原始的和不可空的内联类作为参数来定义函数:

```
fun pet(doggoId: **Long**) {}
fun pet(doggoId: **DoggoId**) {}// decompiled Java code
public static final void pet(long id) { }
public final void **pet_Mu_n4VY**(long doggoId) { }
```

在反编译的代码中，我们可以看到两个函数都使用了原语。

为了实现这一功能，Kotlin 编译器将把内联类作为一个参数，从而破坏函数的名称。

# 在 Java 中使用内联类

我们已经看到，我们不能在 Java 中实例化一个内联类。使用它们怎么样？

## ✅将内联类传递给 Java 函数

我们可以将它们作为参数传递，用作对象，我们可以获得它们包装的属性。

```
void myJavaMethod(DoggoId doggoId){
    long id = doggoId.getId();
}
```

## 在 Java 函数中使用内联类实例的✅

如果我们有定义为顶级对象的内联类实例，我们可以在 Java 中将它们作为原语进行引用，如

```
// Kotlin declaration
val doggo1 = DoggoId(1L)// Java usage
long myDoggoId = GoodDoggosKt.getU1();
```

## 将内联类作为参数的✅& ❌calling·科特林函数

如果我们有一个接收内联类参数的 Java 函数，并且我们想调用一个接受内联类的 Kotlin 函数，我们会得到一个编译错误:

```
fun pet(doggoId: **DoggoId**) {}// Java
void petInJava(doggoId: DoggoId){
    **pet(doggoId)** 
    // compile error: pet(long) cannot be applied to pet(DoggoId)
}
```

对于 Java 来说，`DoggoId`是一个新类型，但是编译器生成的是`pet(long)`，`pet(DoggoId)`不存在。

但是，我们能够传递底层类型:

```
fun pet(doggoId: **DoggoId**) {}// Java
void petInJava(doggoId: DoggoId){
    pet(doggoId.**getId**)
}
```

如果在同一个类中，我们用内联类和底层类型重写了一个函数，当我们从 Java 中调用这个函数时，我们会得到一个错误，因为编译器无法判断我们实际上想要调用哪个函数:

```
fun pet(doggoId: **Long**) {}fun pet(doggoId: **DoggoId**) {}// Java
TestInlineKt.pet(1L);Error: Ambiguous method call. Both pet(long) and pet(long) match
```

# 内嵌还是不内嵌

类型安全有助于我们编写更健壮的代码，但从历史上看，这可能会影响性能。内联类提供了两个世界最好的东西，类型安全*而没有*成本——你应该总是使用它们吗？

内联类带来了一系列限制，确保您创建的对象只扮演一个角色:作为包装器。这意味着在将来，不熟悉代码的开发人员将无法像处理数据类那样，通过向构造函数添加其他参数来错误地增加类的复杂性。

在性能方面，我们已经看到 Kotlin 编译器尽可能使用底层类型，但在许多情况下仍然会创建新的对象。

使用 Java 有几个注意事项，因此，如果您还没有完全迁移到 Kotlin，您可能会遇到无法使用内联类的情况。

最后，这仍然是一个实验性的特性，一旦它稳定下来，它的实现是否会保持不变，以及它是否真的会升级到稳定，这都带来了不确定性。

所以现在您已经理解了内联类的好处和限制，您可以就是否以及何时使用它们做出明智的决定。