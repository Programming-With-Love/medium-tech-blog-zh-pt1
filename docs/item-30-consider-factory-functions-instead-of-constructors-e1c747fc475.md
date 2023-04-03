# 第 30 条:考虑工厂函数而不是构造函数

> 原文：<https://blog.kotlin-academy.com/item-30-consider-factory-functions-instead-of-constructors-e1c747fc475?source=collection_archive---------1----------------------->

![](img/b84f5bfef04c53c790c24c717c1ae2a6.png)

**更新:** [**这里的**](https://kt.academy/article/ek-factory-functions) **是这篇文章的最新版本。**

> 这是本书[有效科特林](https://leanpub.com/effectivekotlin/)的一部分。从概念上讲，这是同一作者对本文的扩展，基于 Joshoua Bloch 的《有效的 Java》一书的第一条。

在 Kotlin 中，类允许客户端获取实例的最常见方式是提供一个主构造函数:

```
class MyLinkedList<T>(val head: T, val tail: MyLinkedList<T>?)val list = MyLinkedList(1, MyLinkedList(2, null))
```

虽然构造函数不是创建对象的唯一方法。对象实例化有许多创造性的设计模式。它们中的大多数都围绕着这样一个想法，即函数可以为我们创建对象，而不是直接创建对象。例如，下面的顶级函数创建了一个`MyLinkedList`的实例:

```
fun <T> myLinkedListOf(vararg elements: T): MyLinkedList<T>? {
   if(elements.isEmpty()) return null
   val head = elements.first()
   val elementsTail = elements.copyOfRange(1, elements.size)
   val tail = myLinkedListOf(*elementsTail)
   return MyLinkedList(head, tail)
}val list = myLinkedListOf(1, 2)
```

作为构造函数的替代函数被称为工厂函数，因为它们产生一个对象。使用工厂函数代替构造函数有很多优点，包括:

*   **与构造函数不同，函数有名字。**名字解释了一个对象是如何被创建的，参数是什么。比如说你看到下面这段代码:`ArrayList(3)`。你能猜出这个论点是什么意思吗？它应该是新创建的列表中的第一个元素，还是列表的大小？肯定不是不言自明的。在这种情况下，一个像`ArrayList.withSize(3)`这样的名字可以消除任何困惑。名称非常有用:它们解释了对象创建的参数或特有方式。命名的另一个原因是它解决了具有相同参数类型的构造函数之间的冲突。
*   **与构造函数不同，函数可以返回其返回类型的任何子类型的对象。**这可以用来为不同的情况提供更好的对象。当我们想要将实际的对象实现隐藏在接口后面时，这一点尤其重要。想到 stdlib(标准库)里的`listOf`。它声明的返回类型是`List`，这是一个接口。它真正的回报是什么？答案取决于我们使用的平台。对于 Kotlin/JVM、Kotlin/JS 和 Kotlin/Native 来说是不同的，因为它们各自使用不同的内置集合。这是 Kotlin 团队做出的重要优化。这也给了 Kotlin 创建者更多的自由，因为列表的实际类型可能会随着时间的推移而改变，只要新对象仍然实现接口`List`并以同样的方式运行，一切都会好的。
*   与构造函数不同，函数不需要在每次被调用时都创建一个新对象。这很有帮助，因为当我们使用函数创建对象时，我们可以包含一个缓存机制来优化对象创建，或者在某些情况下确保对象重用(比如在 Singleton 模式中)。我们还可以定义一个静态工厂函数，如果不能创建对象，它将返回`null`。比如`Connections.createOrNull()`，当`Connection`由于某种原因无法创建时，它返回`null`。
*   **工厂函数可以提供可能尚不存在的对象。**这被基于注释处理的库的创建者大量使用。这样，程序员可以在不构建项目的情况下，通过代理对将要生成或使用的对象进行操作。
*   **当我们在对象外部定义工厂函数时，我们可以控制它的可见性。**例如，我们可以使一个顶级工厂函数只能在同一个文件或同一个模块中访问。
*   **工厂函数可以内联，因此它们的类型参数可以具体化。**
*   **工厂函数可以构造原本可能很难构造的对象。**
*   **构造函数需要立即调用超类的构造函数或者主构造函数。当我们使用工厂函数时，我们可以推迟构造函数的使用:**

```
fun makeListView(config: Config) : ListView {
   val items = … // Here we read items from config
   return ListView(items) // We call actual constructor
}
```

工厂函数的使用有一个限制:它不能在子类构造中使用。这是因为在子类构造中，我们需要调用超类构造函数。

```
class IntLinkedList: MyLinkedList<Int>() {
   // Supposing that MyLinkedList is open constructor(vararg ints: Int): myLinkedListOf(*ints) // Error
}
```

这通常不是问题，因为如果我们已经决定使用工厂函数创建超类，为什么我们要为它的子类使用构造函数呢？我们应该考虑为这样的类实现一个工厂函数。

```
class MyLinkedIntList(
   head: Int, 
   tail: MyLinkedIntList?
): MyLinkedList<Int>(head, tail)fun myLinkedIntListOf(vararg elements: Int): MyLinkedIntList? {
   if(elements.isEmpty()) return null
   val head = elements.first()
   val elementsTail = elements.copyOfRange(1, elements.size)
   val tail = myLinkedIntListOf(*elementsTail)
   return MyLinkedIntList(head, tail)
}
```

上面的函数比前一个构造函数长，但是它有更好的特性——灵活性、类的独立性和声明可空返回类型的能力。

工厂函数背后有强大的理由，尽管需要理解的是**它们不是主构造函数**的竞争对手。工厂函数仍然需要在其主体中使用构造函数，因此构造函数必须存在。如果我们真的想使用工厂函数强制创建，它可以是私有的，但是我们很少这样做( *Item 31:考虑带有命名可选参数*的主构造函数)。**工厂函数主要是对二级构造函数的竞争**，纵观 Kotlin 项目，它们通常以二级构造函数取胜，而很少使用。**他们之间也存在竞争，因为有各种不同的工厂职能。**让我们讨论不同的 Kotlin 工厂功能:

1.  伴随对象工厂函数
2.  扩展工厂功能
3.  顶级工厂功能
4.  假构造函数
5.  工厂类上的方法

[![](img/c92f7d012bc612fef84f3edd03fe40a1.png)](https://kt.academy/workshop/abTesting)

## 伴随对象工厂函数

定义工厂函数最流行的方法是在一个伴随对象中定义它:

```
class MyLinkedList<T>(val head: T, val tail: MyLinkedList<T>?) { companion object {
      fun <T> of(vararg elements: T): MyLinkedList<T>? { /*...*/ }
   }
}// Usage
val list = MyLinkedList.of(1, 2)
```

Java 开发人员应该非常熟悉这种方法，因为它直接等同于静态工厂方法。尽管其他语言的开发人员可能也很熟悉它。在一些语言中，如 C++，它被称为*命名构造函数习语*，因为它的用法类似于构造函数，但有一个名称。

在 Kotlin 中，这种方法也适用于接口:

```
class MyLinkedList<T>(
   val head: T, 
   val tail: MyLinkedList<T>?
): MyList<T> {
   // ...
}interface MyList<T> {
// ... companion object {
      fun <T> of(vararg elements: T): MyList<T>? {
         // ...
      }
   }
}// Usage
val list = MyList.of(1, 2)
```

请注意，上述函数的名称并不是真正的描述性的，但是对于大多数开发人员来说应该是可以理解的。原因是有一些来自 Java 的约定，多亏了它们，一个简短的单词就足以理解参数的意思。以下是一些常见的名称及其描述:

*   `from` —采用单个参数并返回相同类型的对应实例的类型转换函数，例如:

`val date: Date = Date.from(instant)`

*   `of` —采用多个参数并返回包含这些参数的同一类型实例的聚合函数，例如:

`val faceCards: Set<Rank> = EnumSet.of(JACK, QUEEN, KING)`

*   `valueOf`——比`from`和`of`更详细的替代方案，例如:

`val prime: BigInteger = BigInteger.valueOf(Integer.MAX_VALUE)`

*   `instance`或`getInstance` —用在单例中以获得唯一的实例。参数化时，将返回由参数参数化的实例。通常，当参数相同时，我们可以预期返回的实例总是相同的，例如:

`val luke: StackWalker = StackWalker.getInstance(options)`

*   `createInstance`或`newInstance`——类似于`getInstance`，但是这个函数保证每个调用返回一个新的实例，例如:

`val newArray = Array.newInstance(classObject, arrayLen)`

*   `getType` —与`getInstance`相似，但如果工厂功能在不同的类中使用。Type 是工厂函数返回的对象的类型，例如:

`val fs: FileStore = Files.getFileStore(path)`

*   `newType` —与`newInstance`相似，但如果工厂功能在不同的类中使用。Type 是工厂函数返回的对象的类型，例如:

`val br: BufferedReader = Files.newBufferedReader(path)`

许多经验不足的 Kotlin 开发人员将伴随对象成员视为需要分组到单个块中的静态成员。然而，伴随对象实际上要强大得多:例如，伴随对象可以实现接口和扩展类。因此，我们可以实现如下所示的通用伴随对象工厂函数:

```
**abstract class** ActivityFactory {
   **abstract fun** getIntent(context: Context): Intent **fun** start(context: Context) {
      **val** intent = getIntent(context)
      context.startActivity(intent)
   } **fun** startForResult(activity: Activity, requestCode: Int) {
      **val** intent = getIntent(activity)
      activity.startActivityForResult(intent, requestCode)
   }
}**class** MainActivity : AppCompatActivity() {
   *//...* **companion object**: ActivityFactory() {
      **override fun** getIntent(context: Context): Intent =
         Intent(context, MainActivity::**class**.*java*)
   }
}// Usage
**val** intent = MainActivity.getIntent(context)
MainActivity.start(context)
MainActivity.startForResult(activity, requestCode)
```

请注意，这样的抽象伙伴对象工厂可以保存值，因此它们可以实现缓存或支持测试的伪创建。在 Kotlin 编程社区中，伴随对象的优势没有得到很好的利用。尽管如此，如果你看看 Kotlin 团队产品的实现，你会发现伴随对象被大量使用。例如，在 Kotlin 协同程序库中，几乎每一个协同程序上下文的伴随对象都实现了一个接口`CoroutineContext.Key`，因为它们都是我们用来识别这个上下文的关键字。

## 扩展工厂功能

有时，我们希望创建一个工厂函数，其行为类似于一个现有的伴随对象函数，而我们要么不能修改这个伴随对象，要么只想在一个单独的文件中指定一个新函数。在这种情况下，我们可以利用伴随对象的另一个优势:我们可以为它们定义扩展函数。

假设我们不能改变`Tool`接口:

```
interface Tool {
   /*...*/ companion object { /*...*/ }
}
```

尽管如此，我们可以在其伴随对象上定义一个扩展函数(当这个类或接口声明了任何伴随对象时，至少是一个空对象):

```
fun Tool.Companion.createBigTool( /*...*/ ) : BigTool { /*...*/ }
```

在调用站点，我们可以写:

```
Tool.createBigTool()
```

这是一个强大的可能性，让我们用自己的工厂方法扩展外部库。一个问题是，要对伴随对象进行扩展，必须有一些(甚至是空的)伴随对象:

```
interface Tool {
   /*...*/ companion object {}
}
```

## 顶级功能

创建对象的一种流行方法是使用顶级工厂函数。一些常见的例子有`listOf`、`setOf`和`mapOf`。类似地，库设计者指定用于创建对象的顶级函数。顶级工厂函数被广泛使用。比如在 Android 中，我们有定义一个函数来创建一个`Intent`来开始一个活动的传统。在 Kotlin 中，`getIntent()`可以写成一个伴随对象函数:

```
class MainActivity: Activity { companion object {
      fun getIntent(context: Context) =
         Intent(context, MainActivity::class.java)
   }
}
```

在 Kotlin Anko 库中，我们可以使用具体化类型的顶级函数`intentFor`来代替:

```
intentFor<MainActivity>()
```

该函数也可用于传递参数:

```
intentFor<MainActivity>("page" to 2, "row" to 10)
```

使用顶级函数创建对象对于像`List`或`Map`这样的小而常用的对象来说是一个完美的选择，因为`listOf(1,2,3)`比`List.of(1,2,3)`更简单，可读性更好。但是，公共顶级函数需要谨慎使用。公共顶级函数有一个缺点:它们随处可用。很容易混淆开发人员的 IDE 提示。当顶级函数像类方法一样命名并且与类方法混淆时，问题变得更加严重。这就是为什么顶级函数应该被明智地命名。

## 假构造函数

Kotlin 中构造函数的用法与顶级函数相同:

```
class Aval a = A()
```

它们的引用也与顶级函数相同(构造函数引用实现函数接口):

```
val reference: ()->A = ::A
```

从用法的角度来看，大写是构造函数和函数的唯一区别。按照惯例，类以大写字母开头；函数是小写字母。虽然技术上函数可以以大写字母开头。这个事实被用在不同的地方，例如，在 Kotlin 标准库的情况下。`List`和`MutableList`是接口。他们不能有构造函数，但是 Kotlin 开发者希望允许下面的`List`构造:

```
List(4) { "User$it" } // [User0, User1, User2, User3]
```

这就是 Collections.kt 中包含以下函数(从 Kotlin 1.1 开始)的原因:

```
public inline fun <T> List(
   size: Int,
   init: (index: Int) -> T
): List<T> = MutableList(size, init)public inline fun <T> MutableList(
   size: Int,
   init: (index: Int) -> T
): MutableList<T> {
   val list = ArrayList<T>(size)
   repeat(size) { index -> list.add(init(index)) }
   return list
}
```

这些顶级函数的外观和行为都像构造函数，但它们拥有工厂函数的所有优点。许多开发人员没有意识到他们是幕后的顶级功能。这就是为什么他们经常被称为*假施工人员*。

开发人员选择假构造函数而不是真构造函数的两个主要原因是:

*   拥有接口的“构造函数”
*   具有具体化的类型参数

除此之外，伪构造函数的行为应该像普通构造函数一样。它们看起来像构造函数，它们应该这样表现。如果你想包含缓存，返回一个可空类型或者返回一个可以被创建的类的子类，考虑使用一个有名字的工厂函数，比如一个伴随对象工厂方法。

还有一种方法可以声明伪构造函数。使用带有`invoke`操作符的伴随对象可以获得类似的结果。看一下下面的例子:

```
class Tree<T> { companion object {
      operator fun <T> invoke(size: Int, generator: (Int)->T): Tree<T>{
         //...
      }
   }
}// Usage
Tree(10) { "$it" }
```

然而，在一个伴随对象中实现`invoke`来制造一个假的构造函数是很少使用的，我不推荐这样做。首先，因为它打破了*第 12 项:根据名字使用运算符方法*。调用伴随对象是什么意思？请记住，可以使用名称来代替运算符:

```
Tree.invoke(10) { "$it" }
```

调用是与对象构造不同的操作。以这种方式使用运算符与其名称不一致。更重要的是，这种方法比一个顶级函数更复杂。看他们的倒影就能看出这种复杂性。比较一下当我们在一个伴随对象中引用一个构造函数、伪构造函数和`invoke`函数时，反射是什么样子:

构造者:

```
val f: ()->Tree = ::Tree
```

假构造器:

```
val f: ()->Tree = ::Tree
```

在伴随对象中调用:

```
val f: ()->Tree = Tree.Companion::invoke
```

当你需要一个假的构造函数时，我推荐使用标准的顶级函数。当我们不能在类本身中定义构造函数时，或者当我们需要构造函数没有提供的功能时(比如具体化的类型参数)，应该谨慎地使用这些方法来建议典型的类似构造函数的用法。

## 工厂类上的方法

有许多与工厂类相关的创造模式。例如，抽象工厂或原型。他们每个人都有一些优点。

我们将会看到，其中一些方法在科特林是不合理的。在下一个项目中，我们将看到伸缩构造器和构建器模式在 Kotlin 中很少有意义。

工厂类比工厂函数更有优势，因为类可以有状态。例如，这个非常简单的工厂类生成具有下一个 id 号的学生:

```
data class Student(
   val id: Int,
   val name: String,
   val surname: String
)class StudentsFactory {
   var nextId = 0

   fun next(name: String, surname: String) =
      Student(nextId++, name, surname)
}val factory = StudentsFactory()
val s1 = factory.next("Marcin", "Moskala")
println(s1) // Student(id=0, name=Marcin, Surname=Moskala)
val s2 = factory.next("Maja", "Markiewicz")
println(s2) // Student(id=1, name=Maja, Surname=Markiewicz)
```

工厂类可以有属性，这些属性可以用来优化对象创建。当我们可以保持一种状态时，我们可以引入不同种类的优化或功能。例如，我们可以使用缓存或通过复制以前创建的对象来加速对象的创建。

[![](img/3860f92ecd05def9ec2568695cfc2895.png)](https://leanpub.com/effectivekotlin/c/3YYtCtqCC6a4)

## 摘要

如您所见，Kotlin 提供了多种指定工厂函数的方法，它们都有各自的用途。当我们设计对象创建时，我们应该把它们记在心里。它们中的每一个对于不同的情况都是合理的。其中一些应该谨慎使用:伪构造函数、顶级工厂方法和扩展工厂函数。定义工厂函数最通用的方法是使用一个伴随对象。对于大多数开发人员来说，它是安全且非常直观的，因为它的用法与 Java 静态工厂方法非常相似，而且 Kotlin 主要继承了 Java 的风格和实践。

对于审查这一项目，我想感谢艾伦凯恩，法比奥科里尼，杰夫·福尔克和约旦汉森。这是 Marcin Moskał a 的有效 Kotlin 的一部分:

[](https://leanpub.com/effectivekotlin/) [## 有效科特林

### 最佳实践有效的 Kotlin 总结了 Kotlin 社区的最佳实践和经验，以及一个…

leanpub.com](https://leanpub.com/effectivekotlin/) 

# 单击👏说“谢谢！”并帮助他人找到这篇文章。

了解卡帕头最新的重大新闻。学院，[订阅时事通讯](https://kotlin-academy.us17.list-manage.com/subscribe?u=5d3a48e1893758cb5be5c2919&id=d2ba84960a)，[观察推特](https://twitter.com/ktdotacademy)并在媒体上关注我们。

如果你需要一个科特林工作室，看看我们如何能帮助你: [kt.academy](https://kt.academy/) 。

[![](img/3146970f03e44cb07afe660b0d43e045.png)](https://kotlin-academy.us17.list-manage.com/subscribe?u=5d3a48e1893758cb5be5c2919&id=d2ba84960a)