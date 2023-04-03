# 内置委托

> 原文：<https://medium.com/androiddevelopers/built-in-delegates-4811947e781f?source=collection_archive---------4----------------------->

![](img/fc82c9fa6e27477c74910d831194b1ca.png)

代表第二部分

委托帮助你将任务委托给其他对象，并提供更好的代码重用，你可以在[这篇文章](/androiddevelopers/delegating-delegates-to-kotlin-ee0a0b21c52b)中了解更多。Kotlin 不仅支持用关键字`by`实现委托的简单方法，还在 Kotlin 标准库中提供了内置的委托，如`lazy()`、`observable()`、`vetoable()`和`notNull()`。让我们看看这些内置的委托以及它们是如何工作的。

# 懒惰()

`[lazy()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/lazy.html#kotlin$lazy(kotlin.Function0((kotlin.lazy.T))))`函数是一个属性委托，它帮助你延迟初始化属性，也就是在第一次访问它们的时候。`lazy()`对于制作昂贵的物品非常有用。

`lazy()`接受两个参数，一个`LazyThreadSafetyMode`枚举值和一个 lambda。

`LazyThreadSafetyMode`参数指定了初始化如何在不同线程之间同步，而`lazy()`使用默认值`LazyThreadSafetyMode.SYNCHRONIZED`，这意味着初始化是线程安全的，代价是显式同步会对性能产生轻微影响。

lambda 在第一次访问属性时执行，然后存储其值以供将来访问。

# 在后台

当检查反编译的 Java 代码时，我们看到 Kotlin 编译器为惰性委托创建了一个类型为`Lazy`的引用。

这个委托由`LazyKt.lazy()`函数用您指定的 lambda 和线程安全模式参数初始化。

我们来看看`[lazy()](https://github.com/JetBrains/kotlin/blob/master/libraries/stdlib/src/kotlin/util/Lazy.kt)`的源代码。由于`lazy()`函数默认使用`LazyThreadSafetyMode.SYNCHRONIZED`，它返回一个`SynchronizedLazyImpl`类类型的`Lazy`对象。

```
public actual fun <T> lazy(initializer: () -> T): Lazy<T> =
   SynchronizedLazyImpl(initializer)
```

当第一次访问委托属性时，调用`SynchronizedLazyImpl`的`getValue()`函数，该函数在`synchronized`块中初始化属性。

```
override val value: T
   get() {
       val _v1 = _value
       if (_v1 !== UNINITIALIZED_VALUE) {
           @Suppress("UNCHECKED_CAST")
           return _v1 as T
       }

       return synchronized(lock) {
           val _v2 = _value
           if (_v2 !== UNINITIALIZED_VALUE) {
               @Suppress("UNCHECKED_CAST") (_v2 as T)
           } else {
               val typedValue = initializer!!()
               _value = typedValue
               initializer = null
               typedValue
           }
       }
   }
```

这保证了惰性对象以线程安全的方式初始化，但是增加了`synchronized`块的性能成本。

> 注意:如果你确定这个资源会被单线程初始化，可以将 `LazyThreadSafetyMode.NONE`传递给`lazy()`，函数在惰性初始化时不会使用`synchronized`块。然而，请记住`LazyThreadSafetyMode.NONE`不会改变惰性初始化的同步特性。由于惰性初始化是同步的，所以它仍然需要与第一次访问时对象的非惰性初始化相同的时间。这意味着，如果从 UI 线程访问对象，需要很长时间初始化的对象仍然会阻塞 UI 线程。
> 
> `**val lazyValue**: String **by** *lazy*(LazyThreadSafetyMode.*NONE*) **{“lazy”}**`

惰性初始化有助于初始化昂贵的资源。但是，对于简单的对象，比如字符串，`lazy()`通过生成其他对象，比如`Lazy`和`KProperty`，增加了开销。

# 可观察量

`[Delegates.observable()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.properties/-delegates/observable.html)`是 Kotlin 标准库的另一个内置代表。Observer 是一种设计模式，在这种模式中，一个对象维护其依赖者的列表，称为*观察者*，当其状态改变时会自动通知它们。当一个值发生变化时，需要通知多个对象，而不是让每个依赖对象定期调用并检查资源是否更新，这种模式非常有用。

`observable()`有两个参数:初始值和一个侦听器处理程序，当值被修改时，将调用这个处理程序。`observable()`创建一个`[ObservableProperty](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.properties/-observable-property/)`对象，该对象在每次调用 setter 时执行您传递给委托的 lambda。

查看反编译的`Person`类，我们看到 Kotlin 编译器生成了一个扩展了`[ObservableProperty](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.properties/-observable-property/)`的类。这个类还实现了一个名为`afterChange()`的函数，它具有传递给可观察委托的 lambda 函数。

父类`ObservableProperty`的设置器调用`afterChange()`函数。这意味着每次调用者为`address`设置一个新值时，设置者将自动调用`afterChange()`函数，导致所有的监听器被通知这个变化。

您还可以在反编译的代码中看到对`beforeChange()`的调用。`beforeChange()`不是由可观察委托使用的，而是下一个委托`vetoable()`的基础。

# 可否决

`[vetoable()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.properties/-delegates/vetoable.html)`是另一个内置委托，其中属性委托对其值的否决权。类似于`observable()`委托，`vetoable()`接受两个参数:初始值和一个监听器处理程序，当任何调用者想要修改属性值时，将调用这个处理程序。

如果 lambda 条件返回`true`，属性值将被修改，否则值将保持不变。在这种情况下，如果调用者试图用小于 15 个字符的内容更新地址，则当前值将被保留。

查看反编译的`Person`类，Kotlin 生成了一个扩展了`ObservableProperty`的新类。生成的类包含了我们在`beforeChange()`函数中传递的 lambda，在设置值之前，setter 会调用它。

# notNull

Kotlin 标准库提供的最后一个内置委托是`[Delegates.notNull()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.properties/-delegates/not-null.html)`。`notNull()`只是允许在以后初始化一个属性。`notNull()`类似于`[lateinit](https://kotlinlang.org/docs/reference/properties.html#late-initialized-properties-and-variables)`。在大多数情况下，`lateinit`是首选，因为`notNull()`为每个属性创建了一个额外的对象。但是，您可以将`notNull()`用于基本类型，而`lateinit`不支持这种类型。

```
**val fullname**: String **by** Delegates.notNull<String>()
```

`notNull()`使用一种特殊类型的`ReadWriteProperty`命名为`NotNullVar`。

查看反编译的代码，`fullname`属性是用`notNull()`函数初始化的。

```
**this**.**fullname$delegate** = Delegates.***INSTANCE***.notNull();
```

该函数返回一个`NotNullVar`对象。

```
**public fun** <T : Any> notNull(): ReadWriteProperty<Any?, T> = NotNullVar()
```

`NotNullVar`类只是保存一个通用的可空内部引用，如果有代码在值初始化之前调用 getter，就会抛出`IllegalStateException()` 。

Kotlin 标准库提供了一组内置的委托，因此您不需要编写、维护和重新发明它们。内置委托延迟初始化字段，允许稍后初始化基元类型，观察并在值更改时得到通知，甚至否决这些更改。