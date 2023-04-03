# 与科特林相处三年:他们教会了我什么。

> 原文：<https://blog.kotlin-academy.com/three-years-with-kotlin-what-they-taught-me-573d29d1127e?source=collection_archive---------0----------------------->

## 还有“我是如何在不接触 Java 的情况下学会更好的 Java 的”。

![](img/eaef6b144d560d46db77fb729616600a.png)

我不想写一篇仅仅关于科特林的文章，我多么希望有一个“K”雕像在门口等我，就像巴尼·斯丁森家的冲锋队一样。这些东西是为狂热的人准备的(我正在存一些钱来买这座雕像)。

相反，我将谈论代码风格和概念，就像在宫城的“蜡上蜡下”培训中一样，Kotlin 教我在 Java 遗留代码中也要遵循。

## 再见空指针异常！(以及为什么您不应该避免使用棉绒。)

让我爱上 Kotlin 的特性之一是**可空性管理**。这个深奥的术语实际上是说:“我可以比你更好地管理 nulls 带着优越感看 Java。

让我们承认:谁没有面对过空指针异常呢？
使用 Kotlin，您可以避免空检查(实际上不是完全“避免”，而是委托给 JVM)。

```
// *a* is a variable that could be null. In Java, we have to make
// this:
**if** (a != null) {
    a.someMethod()
}// In Kotlin we can do simply:
a**?**.someMethod()
```

这是一个非常好的特性，因为在 Kotlin 中，可以有可空变量和不可空变量。

使用 Kotlin 多年后，我发现自己在用 Java 为遗留代码编程时，会痴迷地检查空值。这种困扰是好的，因为它允许我在空指针异常出现之前避免并修复它。

关于这一点，我有一个建议给所有开发者，新手和老开发者:

> 请不要忽视棉绒警告。

Lint 警告是发现可能的 bug 的好方法，而且如果有时候它们看起来过于迂腐，那么很多时候它们是正确的。

在 Java 中，我们没有很好的空指针处理，但是我们可以——而且应该！—使用`@NotNull`(或`@NonNull`)和`@Nullable`注释强制生成 lint 警告(并消除不需要的警告)。

## 拥抱永恒

解释什么是不变性超出了本文的范围(但是你可以在维基百科上找到它[)；但是我可以简单地说，当一个类的实例不能改变它内部的值时，它被认为是不可变的。](https://en.wikipedia.org/wiki/Immutable_object)

![](img/7a9cc5d3cef77ae347b378d10a3d9952.png)

Kotlin 中的不变性非常简单:只需使用`class`或(更好的)`data class`，用`val`而不是`var`指定主构造函数中的所有字段。

但是如果我没有 setters，那么改变值怎么办？
如果我们正在使用`data class`，Kotlin 会为我们生成 sugar 代码，创建一个`copy`方法，允许我们指定要更改的每个字段。对于一个普通的`class`对象，你必须自己创建一个类似的方法。

```
data class User(val username: String, val email: String, val )val user = User("myuser", "myuser@example.com")
val userWithDifferentEmail = user.copy(email = "myuser@test.com")
```

在 Java 中，你可以将所有类的结果设置为 final，并创建一个初始化所有变量的构造函数。您还可以利用[构建器模式](https://en.wikipedia.org/wiki/Builder_pattern#Java)。

使用不变性总是一个好主意。嗯，不变性只有一个缺点:编辑一个对象，你必须创建一个新的副本。和实例化对象都是有成本的。

但是如果你的模型不是太复杂——在 99%的情况下都不是——就用不可变的模型，因为使用这种范式你可以避免的每一个 bug 都值得为使用它而付出额外的工作:每一个可能的 bug 对开发人员(以及他工作的公司)来说都是非常昂贵的。

## 最终(只读或不可变)变量

在不可变类的同一轨道上，我们必须讨论在 Kotlin 中定义为`val`或在 Java 中定义为`final`的变量。

```
// a read-only variable in Java...
**final String** hello = "Hello!";// ...and this is the same code in lovable Kotlin <3
**val** hello = "Hello!"
```

在前面的代码中，你可以**确定**，也没有文档明确说明，变量 *hello* 在代码过程中不会改变。

使用 final 变量可以避免引入一些恼人的错误，并使代码更具可读性和自文档化。我们是人类，作为人类，我们需要一些限制来确保不犯错误；此外，如果最新的 Java 编译器非常聪明，我喜欢认为指定`final`关键字将有助于优化生成的字节码(我知道，我是一个浪漫的家伙)。

"但是亚历克斯，我什么时候应该使用最终变量？"
我认为最明智的问题是:“什么时候应该使用**非** final 变量？”。
答案是:如果你**需要**在代码的某一点改变那个变量的值，那么使用可变变量。在所有其他情况下，使用最终变量。

Kotlin 是一种非常好的编程语言，可以帮助我们成为更好的程序员。如果你用 Java 编程，而你还没有尝试过，那就去做吧。相信我。

我希望你喜欢这篇文章。如果是，按下**👏🏻**按钮**想按多少次就按多少次**:对你来说它们只是点击，但对我来说却意义重大(参见[多巴胺释放](http://uk.businessinsider.com/what-happens-to-your-brain-like-instagram-dopamine-2017-3))。

附言:不要偷文章标题上的苏联宣传海报，或者至少去要😁。

喜欢的话记得**拍**。注意，如果你按住鼓掌按钮，你可以留下更多的掌声！

了解卡帕头最新的重大新闻。学院、[订阅时事通讯](https://kotlin-academy.us17.list-manage.com/subscribe?u=5d3a48e1893758cb5be5c2919&id=d2ba84960a)、[观察 Twitter](https://twitter.com/ktdotacademy) 并在 medium 上关注我们。

[![](img/5ce68714efe3efc036e06786166954ff.png)](http://eepurl.com/diMmGv)