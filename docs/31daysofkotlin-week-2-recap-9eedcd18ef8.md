# # 31 日 sOfKotlin —第 2 周回顾

> 原文：<https://medium.com/androiddevelopers/31daysofkotlin-week-2-recap-9eedcd18ef8?source=collection_archive---------4----------------------->

![](img/cacbece860b904efd2bbad58bc8e6fed.png)

我们写的 Kotlin 代码越多，我们就越喜欢它！Kotlin 的现代语言功能与 [Android KTX](https://github.com/android/android-ktx) 一起，让我们的 Android 代码更加简洁、清晰和令人愉快。我们( [@objcode](https://twitter.com/objcode) 和 [@FMuntenescu](https://twitter.com/FMuntenescu) )开始了 [#31DaysOfKotlin](https://twitter.com/search?q=%2331DaysOfKotlin) 系列，作为分享我们最喜欢的 Kotlin 和 Android KTX 功能的一种方式，并希望让更多的人像我们一样喜欢它。

在第二篇文章中，我们继续探索 kot Lin——更深入地探讨密封类和内联等主题。

查看其他摘要:

[](/google-developers/31daysofkotlin-week-1-recap-fbd5a622ef86) [## # 31 日 sOfKotlin —第 1 周回顾

### 我们写的 Kotlin 代码越多，我们就越喜欢它！Kotlin 的现代语言功能与 Android KTX 一起使…

medium.com](/google-developers/31daysofkotlin-week-1-recap-fbd5a622ef86) [](/google-developers/31daysofkotlin-week-3-recap-20b20ca9e205) [## # 31 日 sOfKotlin —第 3 周回顾

### 我们写的 Kotlin 代码越多，我们就越喜欢它！Kotlin 的现代语言功能与 Android KTX 一起使…

medium.com](/google-developers/31daysofkotlin-week-3-recap-20b20ca9e205) [](/google-developers/31daysofkotlin-week-4-recap-d820089f8090) [## # 31 日 sOfKotlin —第 4 周回顾

### 我们写的 Kotlin 代码越多，我们就越喜欢它！Kotlin 的现代语言功能与 Android KTX 一起使…

medium.com](/google-developers/31daysofkotlin-week-4-recap-d820089f8090) 

# 第八天:能见度

在 Kotlin 中，默认情况下一切都是公开的！嗯，差不多了。Kotlin 有一组丰富的可见性修饰符可供您使用:`private`、`protected`、`internal`。它们中的每一种都以不同的方式降低了可见度。文档:[可见性修饰符](https://kotlinlang.org/docs/reference/visibility-modifiers.html)

```
// public by default
val *isVisible* = true// only in the same file
private val *isHidden* = true// internal to compilation ‘module’
internal val *almostVisible* = trueclass Foo {
  // public by default
  val isVisible = true // visible to my subclasses
  protected val isInheritable = true // only in the same class
  private val isHidden = true
}
```

# 第九天:默认参数

方法重载的数量是否失控？指定函数中的默认参数值。使用命名参数使代码更具可读性。文档:[默认参数](https://kotlinlang.org/docs/reference/functions.html#default-arguments)

```
// parameters with default values
class BulletPointSpan(
  private val bulletRadius: Float = DEFAULT_BULLET_RADIUS,
  private val gapWidth: Int = DEFAULT_GAP_WIDTH,
  private val color: Int = Color.BLACK
) {…}// using only default values
val bulletPointSpan = BulletPointSpan()// passing a value for the first argument, others default
val bulletPointSpan2 = BulletPointSpan(
    resources.getDimension(R.dimen.radius))// using a named parameter for the last argument, others default
val bulletPointSpan3 = BulletPointSpan(color = Color.RED)
```

# 第 10 天:密封课程

Kotlin `sealed`类让您轻松处理错误数据。当与`LiveData`结合使用时，你可以用一个`LiveData`来表示成功路径和错误路径。比使用两个变量要好得多。单据:[密封类](https://kotlinlang.org/docs/reference/sealed-classes.html)

```
sealed class NetworkResult
data class Success(val result: String): NetworkResult()
data class Failure(val error: Error): NetworkResult()// one observer for success and failure
viewModel.data.observe(this, Observer<NetworkResult> { data ->
  data ?: return@Observer // skip nulls
  when(data) {
    is Success -> showResult(data.result) // smart cast to Success
    is Failure -> showError(data.error) // smart cast to Failure
  }
})
```

您也可以在`RecyclerView`适配器中使用密封类。它们非常适合`ViewHolders`——有一组干净的类型可以显式地分派给每个持有者。用作表达式时，如果所有类型都不匹配，编译器将出错。

```
// use Sealed classes as ViewHolders in a RecyclerViewAdapter
override fun onBindViewHolder(
  holder: SealedAdapterViewHolder?, position: Int) {
  when (holder) { // compiler enforces handling all types
    is HeaderHolder -> {
      holder.displayHeader(items[position]) // smart cast here
    }
    is DetailsHolder -> {
      holder.displayDetails(items[position]) // smart cast here
    }
  }
}
```

对于`RecyclerViews`更进一步，如果我们有很多来自`RecyclerView`项目的回调，比如这个详细的点击、分享和删除动作，我们可以使用密封类。一个回调带一个密封类就可以处理所有的事情！

```
sealed class DetailItemClickEvent
data class DetailBodyClick(val section: Int): DetailItemClickEvent()
data class ShareClick(val platform: String): DetailItemClickEvent()
data class DeleteClick(val confirmed: Boolean):
     DetailItemClickEvent()class MyHandler : DetailItemClickInterface {
  override fun onDetailClicked(item: DetailItemClickEvent) {
    when (item) { // compiler enforces handling all types
      is DetailBodyClick -> expandBody(item.section)
      is ShareClick -> shareOn(item.platform)
      is DeleteClick -> {
        if (item.confirmed) doDelete() else confirmDetele()
      }
    }
  }
}
```

# 第 11 天:懒惰

偷懒就好！通过使用`lazy`，将昂贵的属性初始化成本推迟到真正需要的时候。计算出的值随后被保存并用于任何将来的调用。Docs: [懒](https://kotlinlang.org/docs/reference/delegated-properties.html#lazy)

```
val preference: String by lazy {
  sharedPreferences.getString(PREFERENCE_KEY) 
}
```

# 第 12 天:迟到

在 Android 中，`onCreate`或者其他回调初始化对象。在 Kotlin 中，非空 val 必须初始化。怎么办？输入`lateinit`。一言为定:以后初始化我！用它发誓它最终会是零安全的。文档: [lateinit](https://kotlinlang.org/docs/reference/properties.html#late-initialized-properties-and-variables)

```
class MyActivity : AppCompatActivity() {
  // non-null, but not initalized
  lateinit var recyclerView: RecyclerView

  override fun onCreate(savedInstanceState: Bundle?) {
    // … // initialized here
    recyclerView = findViewById(R.id.recycler_view)
  }
}
```

# 第 13 天:要求和检查

你的函数参数有效吗？使用前检查，用`require`。如果它们无效，就会抛出一个`IllegalArgumentException`。单据:[需要](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/require.html)

```
fun setName(name: String) {
  // calling setName(“”) throws IllegalArgumentException
  require(name.isNotEmpty()) { “Invalid name” }

  // …
}
```

你的封闭类的状态正确吗？使用`check`进行验证。如果检查的值是`false`，它将抛出一个`IllegalStateException`。文件:[检查](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/check.html)

```
fun User.logOut(){
  // When not authenticated, throws IllegalStateException
  check(isAuthenticated()) { “User $email is not authenticated” }
  isAuthenticated = false
}
```

# 第 14 天:内嵌

迫不及待想用 lambdas 做新的 API？排队吧。Kotlin 允许您将函数指定为`inline`——这意味着调用将被函数体所取代。呼吸，零开销地制作基于 lambda 的 API。文档:[内嵌函数](https://kotlinlang.org/docs/reference/inline-functions.html)

```
// define an inline function that takes a function argumentinline fun onlyIf(check: Boolean, operation: () -> Unit) {
  if (check) {
    operation()
  }
}// call it like this
onlyIf(shouldPrint) { // call: pass operation as a lambda
  println(“Hello, Kotlin”)
}// which will be inlined to this
if (shouldPrint) { // execution: no need to create lambda
  println(“Hello, Kotlin”)
}
```

本周深入探讨了 Kotlin 的特性:可见性、默认参数、密封类、lazy、lateinit、require 和 check，以及真正强大的内联。下周我们将深入更多的 Kotlin 特性，并开始探索 Android KTX。

你已经开始使用科特林了吗？我们很想知道你还喜欢哪些功能，以及你是如何在你的 Android 应用中使用这些功能的。