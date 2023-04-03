# 将 Android 项目迁移到 Kotlin

> 原文：<https://medium.com/androiddevelopers/migrating-an-android-project-to-kotlin-f93ecaa329b7?source=collection_archive---------5----------------------->

不久前，我们开源了 Android 问答应用程序 [Topeka](https://github.com/googlesamples/android-topeka) 。
使用[集成测试](https://github.com/googlesamples/android-topeka/tree/master/app/src/androidTest/java/com/google/samples/apps/topeka)和[单元测试](https://github.com/googlesamples/android-topeka/tree/master/app/src/test/java/com/google/samples/apps/topeka)进行测试。
而且是纯 Java 写的。嗯……的确是

# 圣彼得堡海岸边的那个岛叫什么名字？_ _ _ _ _ _

在 Google I/O 2017 上，我们宣布了对 Kotlin 编程语言的官方[支持。那是我开始从 Java 迁移代码库的时候，**在途中学习 Kotlin。**](https://blog.jetbrains.com/kotlin/2017/05/kotlin-on-android-now-official/)

> 从技术角度来看，这种迁移并不是必要的。这个应用程序很坚固，但主要是为了满足我的好奇心；托皮卡作为我学习新语言的载体。

如果你好奇，你可以直接跳到 GitHub 上的[源代码。有一段时间，代码在一个单独的分支上，但现在它被合并到主代码中了。](https://github.com/googlesamples/android-topeka/)

这篇文章收集了我在迁移过程中发现的一些关键部分。它还展示了我在学习新的 Android 开发编程语言时发现的一些有用的东西。

![](img/d0638212947874fe55db7c15d4b75b25.png)

It still looks the same

# 🔑关键外卖

*   Kotlin 是一种有趣而强大的语言
*   测试让你安心
*   特定于平台的习惯用法很少

# 最初迁移到科特林

It’s not as easy as Bad Android Advice put it, but it’s a good starting point.

第 1 步和第 2 步对于 Kotlin 的入门是有效的。不过，我会弄清楚第三步会如何进行。

## 对托皮卡来说，路线更像这样:

1.  仔细阅读 Kotlin 的[基本语法](https://kotlinlang.org/docs/reference/basic-syntax.html)
2.  通过考研来获得对语言的基本熟悉
3.  转换文件，一个接一个，通过“⌥⇧⌘K”，确保测试仍然通过
4.  仔细检查科特林的文件，让他们更地道
5.  重复第 4 步，直到您和您的代码审查人员都满意为止
6.  装运它

# 互用性

循序渐进是明智的做法。Kotlin 编译成 Java 字节码，这两种语言可以互操作。同样，在同一个项目中使用两种语言也是可能的。所以没有必要将所有代码移植到另一种语言。

但是如果这是你的目标，那么迭代地这样做是有意义的。这样，在整个迁移过程中维护一个稳定的应用程序并在迁移过程中不断学习就更加可行。

# 测试让你放松心情

拥有一套单元和集成测试有很多好处。
在大多数情况下，他们的存在是为了让人们相信变化并没有破坏现有的行为。

对我来说，从不太复杂的数据类开始是明确的选择。
它们在整个项目中被使用，然而它们的复杂性相对较低。这使得它们成为开启新语言之旅的理想起点。

在使用内置于 Android Studio 中的 Kotlin 代码转换器迁移了其中一些代码之后，我执行测试并使它们通过，直到最终将测试本身迁移到 Kotlin。

如果没有测试，我会被要求在每次更改后检查接触到的特性，并手动验证它们。
通过自动化，在代码库中移动、移植代码变得更快更容易了。

所以，如果你还没有对你的应用程序进行适当的测试，这里有一个更好的理由来这样做。👆

# ‼️生成的代码并不总是好看的

在第一轮*主要是*自动化迁移之后，我继续阅读[科特林风格指南](https://kotlinlang.org/docs/reference/coding-conventions.html)。这一页让我明白，前方的路还很长。

总的来说，转换器做得很好。然而，在自动化过程中，有许多语言习惯用法和特征没有被考虑在内。这可能是更好的，因为翻译是棘手的。尤其是如果一种语言包含更多的功能或者以不同的方式实现类似的东西。

为了进一步了解科特林转换器，[本杰明·巴克斯特](https://medium.com/u/9a3b7ded03e0?source=post_page-----f93ecaa329b7--------------------------------)写了他的经历:

[](/google-developers/lessons-learned-while-converting-to-kotlin-with-android-studio-f0a3cb41669) [## 使用 Android Studio 转换到 Kotlin 的经验教训

### 使用 Android Studio 提升您的 Kotlin 转换

medium.com](/google-developers/lessons-learned-while-converting-to-kotlin-with-android-studio-f0a3cb41669) 

# ‼️ ⁉

在自动转换之后，我得到了很多的`?`和`!!`。
这些用于使一个值可为空，并断言某个值不为空。这反过来又会导致一个`NullPointerException`。
我不禁想起一句非常贴切的名言:

> “多个感叹号，”他摇着头继续说道，“是精神不正常的明显标志。——[*特里·普拉切特*](https://wiki.lspace.org/mediawiki/Multiple_exclamation_marks)

在许多情况下，值不必为空，因此可以删除空检查。甚至没有必要直接在构造函数中初始化所有的值。相反，可以使用`lateinit`或委托初始化。

然而，这并不适用于所有情况:

Sometimes vars have to be nullable nonetheless

所以我必须返回并使我的视图成员可空。

在这些和其他情况下，你仍然需要检查是否有东西是`null`。如果有`supportActionBar`，使用`*supportActionBar*?.setDisplayShowTitleEnabled(false)`只执行问号后面的部分。
这意味着少了很多基于`if`语句的空支票。🔥

另外，直接在非空变量上执行带有一些 stdlib 函数的代码也很方便:

let it scale, let it scaaaale…

# 逐渐变得更加地道

仔细检查生成的代码，使其更加符合习惯，以及获得评审者的反馈，这些都表明 Kotlin 是一种强大的语言。它让事情变得可读和简洁。

让我们来看看我遇到的一些例子。

## 读书少并不总是一件坏事

让我们以适配器的`getView`为例:

getView in Java

getView in Kotlin

这两个代码片段做*同样的事情* :
检查`convertView`是否为`null`，并在`createView(...)`内创建一个新视图或返回`convertView`。两者也叫`bindView(...)`。

这两段文字同样清晰可辨。把东西从 8 行精简到只有 2 行？使我印象深刻。

## 数据类是神奇的🦄

为了让 Kotlin 的简洁更加明显，数据类很容易摆脱一些冗长:

现在，让我们看看科特林:

没错，就是少了 55 行代码，表达同样的东西。这就是数据类的神奇之处。

## 扩展功能

从传统 Android 开发者的角度来看，这就是事情变得有点奇怪的地方。Kotlin 允许在给定的范围内创建自己的 DSL。

**让我们看看它是如何工作的**

有时在 Topeka 内部，在`Parcel`中传递布尔值是有意义的。Android 框架 API 并不直接支持这一点。在最初的实现中，有必要显式地调用一个实用类的方法，比如`ParcelableHelper.writeBoolean(parcel, value)`。
有了 Kotlin，[扩展函数](https://kotlinlang.org/docs/reference/extensions.html)一劳永逸地解决了这个问题:

将这些写在一个地方，使得直接调用`parcel.writeBoolean(value)`和`parcel.readBoolean()`成为可能，就好像它是框架的一部分。如果 Android Studio 不会以不同的方式突出扩展功能，它们几乎不会引人注意。

**扩展功能让东西更容易阅读。**让我们看看另一个例子:替换视图层次结构中的片段

在 Java 世界中，这看起来像这样:

那其实也不算太差。但是你必须写这段代码，在*每一个片段将被替换的位置*。或者在某个地方创建一个方法，例如在另一个 Utils 类中。

有了 Kotlin，一个扩展函数使得简单地调用`replaceFragment(R.id.container, MyFragment())`来替换项目中任何`FragmentActivity`内的一个片段成为可能，通过添加以下代码:

Replacing Fragments in a single line

## 少一些仪式，多一些功能

**高阶函数**让我大吃一惊。
是的，我知道这总的来说不是一个新概念。但是对于传统的 Android 开发者来说，事实上是这样的。我以前听说过它们，也看过它们的编写，但是在我自己的代码中使用它们是另一回事。

在托皮卡的几个地方，我依靠`OnLayoutChangeListener`来注入行为。在 Kotlin 之前的世界中，这通常会导致一个匿名类，带有一些重复的代码。

迁移后，需要调用的是:
`view.onLayoutChange { myAction() }`

围绕这一点的仪式已经被封装在这个扩展函数中:

Higher order function to reduce boilerplate

再举一个例子，这种行为也可以应用于数据库事务:

Less ceremony for database transactions

现在，这个 API 的所有用户只需调用`db.transact { operation() }`，而不是执行整个舞蹈来开始和结束一个事务。

[通过 Twitter](https://twitter.com/pacoworks/status/885147451757350912) 更新:使用`SQLiteDatabase.()`而不仅仅是`()`来传递函数允许直接在`operation()`中操作数据库。🔥

我可以继续说下去，但你已经知道要点了。

> 高阶函数和扩展很方便，通过删除不必要的冗长，添加功能和隐藏实现细节，使项目更容易阅读，工作更有趣。

# 有待发现的事物

在整个转换过程中，我还没有遇到很多 Android 开发的最佳实践。到目前为止，我一直坚持使用风格指南和代码约定。

这可能是因为我还不熟悉这门语言，或者是因为还没有太多的投资来收集和宣传这些信息。也许有一个我还没有碰到的集合，但是看起来有相当大的空间来存放特定于平台的习惯用法。
如果你知道这样的收藏，请将它们添加到评论中。