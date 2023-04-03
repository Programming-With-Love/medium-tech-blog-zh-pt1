# 不要争论默认的论点

> 原文：<https://medium.com/androiddevelopers/dont-argue-with-default-arguments-2245b2c752c?source=collection_archive---------0----------------------->

![](img/f1f064469350b2336414178de85f7e03.png)

*科特林词汇*

简短易用的默认参数允许你在没有样板文件的情况下实现函数重载。像许多 Kotlin 功能一样，这感觉像是魔术。你想知道它的秘密吗？请继续阅读，了解默认参数是如何工作的。

# 基本用法

如果需要重载一个函数，可以使用默认参数，而不是多次实现同一个函数:

默认参数也可以应用于构造函数:

# Java 互操作

缺省情况下，Java 不识别缺省值重载:

要指示编译器生成重载方法，请在 Kotlin 函数上使用`[@JvmOverloads](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.jvm/-jvm-overloads/)`注释:

# 在后台

让我们看看 Java 反编译的代码，看看编译器为我们生成了什么:`Tools -> Kotlin -> Show Kotlin Bytecode`然后按下`Decompile`按钮。

## 功能

我们看到编译器生成了两个函数:

*   `play` —有 1 个参数:`Toy`，在不使用默认参数时调用
*   合成方法`play$default`——有 3 个参数:`Toy`、一个`int`和一个`Object`；每当使用默认参数时都会调用它。`Object`参数总是`null`，但是 int 的值不同。让我们看看如何！

## int 参数

`play$default`的 int 参数的值是根据传入了默认参数的参数的编号和索引来计算的。根据这个参数的值，Kotlin 编译器知道用哪个参数来调用 play 函数。

在我们的`play()`示例调用中，索引 0 处的参数使用默认参数。因此，`play$default`用`int var1 = 2⁰`来称呼:

```
play$default((Toy)null, 1, (Object)null);
```

`play$default`实现知道`var0`的值应该被替换为默认值。

让我们举一个更复杂的例子来看看 int 参数的行为。让我们扩展我们的`play`函数，在调用点，使用`doggo`和`toy`的默认参数:

让我们看看反编译代码中发生了什么:

我们现在看到，我们的 int 参数是 5。这是如何计算的:位置 0 和 2 处的参数使用默认参数 so `var3 = 2⁰ + 2² = 5`。在[按位](https://en.wikipedia.org/wiki/Bitwise_operation#AND) `[&](https://en.wikipedia.org/wiki/Bitwise_operation#AND)` [运算](https://en.wikipedia.org/wiki/Bitwise_operation#AND)中，参数的计算如下:

*   `var3 & 1 != 0`是`true`所以`var0 = goodDoggo`
*   `var3 & 2 != 0`是`false`，所以`var1`不被替换
*   `var3 & 4 != 0`是`true`所以`var2 = SqueakyToy`

根据应用于`var3`的位掩码，编译器可以计算哪些参数应该替换为默认值。

# 对象参数

在上面的例子中，你可能已经注意到`Object`参数的值总是空的，并且它实际上从来没有在`play$default`函数中使用过。此参数与支持覆盖函数中的默认值有关。

# 默认参数和继承

当我们想用默认参数覆盖一个函数时会发生什么？

让我们改变上面的例子:

*   使`play`成为`Doggo`的`open`函数，使`Doggo`成为`open`类。
*   创建一个新的`PlayfulDoggo`类，扩展`Doggo`并覆盖`play`

当我们想在 PlayfulDoggo.play 中设置一个默认值时，我们看到我们不被允许:**一个覆盖函数不被允许为它的参数指定默认值**

如果我们删除`override`并检查反编译的代码，`PlayfulDoggo.play()`看起来像这样:

这是否意味着未来将支持带有默认参数的超级调用？我们只能等着瞧了。

# 构造器

对于构造函数来说，反编译的 Java 代码只有一个区别。让我们来看看:

构造函数也创建一个合成方法，但是构造函数使用一个用`null`调用的`[DefaultConstructorMarker](https://github.com/JetBrains/kotlin/blob/master/libraries/stdlib/jvm/runtime/kotlin/jvm/internal/DefaultConstructorMarker.java)`，而不是函数中使用的`Object`:

与主构造函数一样，带有默认参数的次构造函数也将使用`DefaultConstructorMarker`生成另一个合成方法:

# 结论

简单而有趣的是，默认参数减少了我们在处理重载方法时需要编写的样板代码的数量，允许我们为参数设置默认值。正如许多 Kotlin 关键字的情况一样，when 可以通过查看它为我们编写的代码来了解它们的魔力。查看我们的其他 Kotlin 词汇帖子了解更多。