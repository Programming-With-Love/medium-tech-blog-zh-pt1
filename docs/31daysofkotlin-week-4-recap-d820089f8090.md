# # 31 日 sOfKotlin —第 4 周回顾

> 原文：<https://medium.com/androiddevelopers/31daysofkotlin-week-4-recap-d820089f8090?source=collection_archive---------6----------------------->

![](img/c6a8873718f7a90cfe9166b18cd1e191.png)

Illustration by [Virginia Poltrack](https://twitter.com/VPoltrack)

我们写的 Kotlin 代码越多，我们就越喜欢它！Kotlin 的现代语言功能和 [Android KTX](https://github.com/android/android-ktx) 一起让我们的 Android 代码更加简洁、清晰和令人愉快。我们( [@objcode](https://twitter.com/objcode) 和 [@FMuntenescu](https://twitter.com/FMuntenescu) )开始了 [#31DaysOfKotlin](https://twitter.com/search?q=%2331DaysOfKotlin) 系列，作为分享我们最喜欢的 Kotlin 和 Android KTX 功能的一种方式，并希望让更多的人像我们一样喜欢它。

第四周学习了更多的语言基础知识，然后深入研究了 Android KTX 让你的代码更加简洁易读的一些方法。

查看第 1 周、第 2 周和第 3 周的概要:

[](/google-developers/31daysofkotlin-week-1-recap-fbd5a622ef86) [## # 31 日 sOfKotlin —第 1 周回顾

### 我们写的 Kotlin 代码越多，我们就越喜欢它！Kotlin 的现代语言功能与 Android KTX 一起使…

medium.com](/google-developers/31daysofkotlin-week-1-recap-fbd5a622ef86) [](/google-developers/31daysofkotlin-week-2-recap-9eedcd18ef8) [## # 31 日 sOfKotlin —第 2 周回顾

### 我们写的 Kotlin 代码越多，我们就越喜欢它！Kotlin 的现代语言功能与 Android KTX 一起使…

medium.com](/google-developers/31daysofkotlin-week-2-recap-9eedcd18ef8) [](/google-developers/31daysofkotlin-week-3-recap-20b20ca9e205) [## # 31 日 sOfKotlin —第 3 周回顾

### 我们写的 Kotlin 代码越多，我们就越喜欢它！Kotlin 的现代语言功能与 Android KTX 一起使…

medium.com](/google-developers/31daysofkotlin-week-3-recap-20b20ca9e205) 

# *第 22 天:从 Java 编程语言调用 kot Lin*

在同一个项目中使用 Kotlin 和 Java？你有顶级函数或属性的类吗？默认情况下，编译器将类名生成为`YourFileKt`。通过用`@file:JvmName`注释文件来改变它。文档:[包级功能](https://kotlinlang.org/docs/reference/java-to-kotlin-interop.html#package-level-functions)

```
// File: ShapesGenerator.kt
package com.shapesfun generateSquare(): Square {…}
fun generateTriangle(): Triangle {…}// Java usage:
Square square = ShapesGeneratorKt.generateSquare();// File: ShapesGenerator.kt
@file:JvmName(“ShapesGenerator”)
package com.shapesfun generateSquare(): Square {…}
fun generateTriangle(): Triangle {…}// Java usage:
Square square = ShapesGenerator.generateSquare();
```

# 第 23 天:具体化

为了使具体化的概念具体化，一个例子是合适的:`Context.systemService()`在 Android 中，KTX 使用具体化来通过泛型传递“真实”类型。不再给`getSystemService`过课！单据:[具体化类型参数](https://kotlinlang.org/docs/reference/inline-functions.html#reified-type-parameters)

Android KTX:[context . system service()](https://github.com/android/android-ktx/blob/master/src/main/java/androidx/core/content/Context.kt#L37)

```
// the old way
val alarmManager =
        context.getSystemService(AlarmManager::class.java)// the reified way
val alarmManager: AlarmManager = context.systemService()// the magic from Android KTX… with “real” type T
inline fun <reified T> Context.systemService() =
         getSystemService(T::class.java)
```

# 第 24 天:代表

用`by`将你的工作委托给另一个班级。使用类委托支持复合而不是继承，使用委托者属性重用属性访问器逻辑。单据:[委托](https://kotlinlang.org/docs/reference/delegation.html)和[委托属性](http://kotlinlang.org/docs/reference/delegated-properties.html)

```
class MyAnimatingView : View( /* … */ ) {
    // delegated property. 
    // Uses the getter and setter defined in InvalidateDelegate
    var foregroundX by InvalidateDelegate(0f)
}// A View Delegate which invalidates
// View.postInvalidateOnAnimation when set.
class InvalidateDelegate<T : Any>(var value: T) {
    operator fun getValue(thisRef: View, 
                          property: KProperty<*>) = value operator fun setValue(thisRef: View, 
                          property: KProperty<*>, value: T) {
        this.value = value
        thisRef.postInvalidateOnAnimation()
    }
}
```

# 第 25 天:扩展功能

没有更多的实用类！使用`extension functions`扩展一个类的功能。将您正在扩展的类的名称放在您正在添加的方法的名称之前。Doc: [扩展功能](https://kotlinlang.org/docs/reference/extensions.html#extension-functions)

扩展功能:

*   不是成员函数
*   不要以任何方式修改原始类
*   在编译时由静态类型信息解析
*   被编译成静态函数
*   不要做多态性

示例: [String.toUri()](https://github.com/android/android-ktx/blob/master/src/main/java/androidx/core/net/Uri.kt#L36)

```
// Extend String with toUri
inline fun String.toUri(): Uri = Uri.parse(this)// And call it on any String!
val myUri = “www.developer.android.com".toUri()
```

# 第 26 天:Drawable.toBitmap()简单转换

如果你曾经将一个`Drawable`转换成一个`Bitmap`，那么你知道你需要多少样板文件。

Android KTX 有一套很棒的函数，可以让你的代码在处理图形包中的类时更加简洁。文档:[图形](https://github.com/android/android-ktx/tree/master/src/main/java/androidx/core/graphics)

```
// get a drawable from resources
val myDrawable = ContextCompat.getDrawable(context, R.drawable.icon)// convert the drawable to a bitmap
val bitmap = myDrawable.toBitmap()
```

# 第 27 天:序列、懒惰和生成器

序列是从来不存在的列表。一个`Sequence`是`Iterator`的表亲，一次懒洋洋地生成一个值。当使用`map`和`filter`时，这真的很重要——它们将创建序列，而不是为每一步复制列表！文档:[序列](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.sequences/index.html)

```
val sequence = List(50) { it * 5 }.asSequence()sequence.map { it * 2 }         // lazy (iterate 1 element)
        .filter { it % 3 == 0 } // lazy (iterate 1 element)
        .map { it + 1 }         // lazy (iterate 1 element)
        .toList()               // eager (make a new list)
```

您可以根据列表或通过指定下一个函数来生成序列。如果你从不终止一个序列，它可以无限长而不会耗尽内存。有了 Kotlin 中的协程，您还可以使用生成器！文件:[发电机](https://kotlinlang.org/docs/reference/coroutines.html#generators-api-in-kotlincoroutines)

```
// make a sequence from a list
list.asSequence().filter { it == “lazy” }// or, reach infinity functionally
val natural = generateSequence(1) { it + 1 }// or, cooperate with coroutines
val zeros = buildSequence {
    while (true) {
        yield (0)
    }
}
```

# 第 28 天:轻松跨越

强大但难以使用——这就是文本样式跨越 API 的感觉。

Android KTX 为一些最常见的跨度添加了扩展功能，并使 API 更易于使用。Android KTX:[span 启用字符串生成器](https://github.com/android/android-ktx/blob/master/src/main/java/androidx/core/text/SpannableStringBuilder.kt)

```
val string = buildSpannedString {
    append(“no styling text”)
    bold {
        append(“bold”)
        italic { append(“bold and italic”) }
    }
    inSpans(RelativeSizeSpan(2f), QuoteSpan()){
        append(“double sized quote text”)
    }
}
```

# 第 29 天:打包

喜欢`Parcelable`的速度，但不喜欢写那些代码？向`@Parcelize`问好。规格:[打包](https://github.com/Kotlin/KEEP/blob/master/proposals/extensions/android-parcelable.md)

```
@Parcelize
data class User(val name: String, val occupation: Work): Parcelable// build.gradle
androidExtensions {
    //Enable experimental Kotlin features 
    // in gradle to enable Parcelize
    experimental = true
}
```

# 第 30 天:更新填充扩展

用默认参数扩展现有 API 通常会让所有人都满意。Android KTX 允许您使用默认参数设置视图一侧的填充。一个单行函数，节省了这么多代码！Android KTX:[view . update padding](https://github.com/android/android-ktx/blob/master/src/main/java/androidx/core/view/View.kt#L117)

```
view.updatePadding(left = newPadding)
view.updatePadding(top = newPadding)
view.updatePadding(right = newPadding)
view.updatePadding(bottom = newPadding)
view.updatePadding(top = newPadding, bottom = newPadding)
```

# 第 31 天:确定`run`、`let`、`with`、`apply`

让我们用一些标准的 Kotlin 函数来运行！简短而强大，`run`、`let`、`with`、`apply`都有一个接收者(`this`)，可能有一个自变量(`it`)，可能有一个返回值。看到不同之处了吗

**运行**

```
val string = “a”
val result = string.run {
    // this = “a”
    // it = not available
    1 // Block return value
    // result = 1
}
```

**让**

```
val string = “a”
val result = string.let {
    // this = this@MyClass
    // it = “a”
    2 // Block return value
    // result = 2
}
```

**同**

```
val string = “a”
val result = with(string) {
    // this = “a”
    // it = not available
    3 // Block return value
    // result = 3
}
```

**应用**

```
val string = “a”
val result = string.apply {
    // this = “a”
    // it = not available
    4 // Block return value unused
    // result = “a”
}
```

本周讨论了更多的语言特性，比如互操作、重排序和序列，然后我们继续讨论 Android KTX，展示它帮助你编写简洁易读的代码的一些方法。为了结束这个系列，我们介绍了强大的 Kotlin scope 函数。

你已经开始使用科特林了吗？我们很想知道你还发现了哪些很棒的功能，以及你是如何在你的 Android 应用中使用它们的。

再次感谢我们的评审:[杰克](https://twitter.com/JakeWharton)、[罗曼](https://twitter.com/romainguy)、[尼克](https://twitter.com/crafty)、[詹姆斯](https://twitter.com/jmslau)、[唐](https://twitter.com/donturner)以及我们的设计师:[弗吉尼亚](https://twitter.com/VPoltrack)。