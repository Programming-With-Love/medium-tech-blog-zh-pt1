# 嘲笑不是火箭科学:嘲笑高级功能

> 原文：<https://blog.kotlin-academy.com/mocking-is-not-rocket-science-mockk-advanced-features-42277e5983b5?source=collection_archive---------0----------------------->

去年一年，MockK 在 Kotlin 世界飞速发展。用户通过提交问题和提出改进建议来积极地帮助改进它。上个月，[mock](https://mockk.io)推出了许多强大的功能，我很高兴与您分享它们。这篇文章并不短，因为我想分享很多你可以用来改进测试的新的可能性。

![](img/ac12efb1b7fb9faac1642ae749c9cf5d.png)

# 等级嘲讽

从最近的一个版本开始，1.9.1 MockK 支持所谓的分层嘲讽。例如，如果您有以下从属接口:

```
interface AddressBook {
    val contacts: List<Contact>
}interface Contact {
    val name: String
    val telephone: String
    val address: Address
}interface Address {
    val city: String
    val zip: String
}
```

并且需要模仿整个通讯录，可以使用`mockk`函数的初始化块来指定其内部的行为，并通过一个链将其他模仿放在`every`的`returns`中。这产生了一个很好 DSL 来定义依赖对象:

```
val addressBook = mockk<AddressBook> {
    every { contacts } returns listOf(
        mockk {
            every { name } returns "John"
            every { telephone } returns "123-456-789"
            every { address.city } returns "New-York"
            every { address.zip } returns "123-45"
        },
        mockk {
            every { name } returns "Alex"
            every { telephone } returns "789-456-123"
            every { address } returns mockk {
                every { city } returns "Wroclaw"
                every { zip } returns "543-21"
            }
        }
    )
}
```

你可以注意到这里的`address.city`和`address.zip`是通过链式调用被嘲笑的。这是另一种方法，由你决定选择哪一种。

当然，真正的层次结构将由真实对象和模拟对象混合组成。

```
val serviceLocator = *mockk*<ServiceLocator> {
    *every* { transactionRepository } returns *mockk* {
        *coEvery* {
            getTransactions()
        } returns Result.build {
            *listOf*(
                NoteTransaction(
                    creationDate = "28/10/2018",
                    contents = "Content of note1.",
                    transactionType = TransactionType.DELETE
                ),
                NoteTransaction(
                    creationDate = "28/10/2018",
                    contents = "Content of note2.",
                    transactionType = TransactionType.DELETE
                ),
                NoteTransaction(
                    creationDate = "28/10/2018",
                    contents = "Content of note3.",
                    transactionType = TransactionType.DELETE
                )
            )
        }

        *coEvery* {
            deleteTransactions()
        } returns Result.build { Unit }
    }

    *every* { remoteRepository } returns *mockk* {
        *coEvery* { getNotes() } returns Result.build {
            *listOf*(
                Note(
                    creationDate = "28/10/2018",
                    contents = "Content of note1.",
                    upVotes = 0,
                    imageUrl = "",
                    creator = User(
                        "8675309",
                        "Ajahn Chah",
                        ""
                    )
                ), Note(
                    creationDate = "28/10/2018",
                    contents = "Content of note2.",
                    upVotes = 0,
                    imageUrl = "",
                    creator = User(
                        "8675309",
                        "Ajahn Chah",
                        ""
                    )
                ), Note(
                    creationDate = "28/10/2018",
                    contents = "Content of note3.",
                    upVotes = 0,
                    imageUrl = "",
                    creator = User(
                        "8675309",
                        "Ajahn Chah",
                        ""
                    )
                )
            )
        }

        *coEvery* {
            synchronizeTransactions(any())
        } returns Result.build {
            Unit
        }
    }
}
```

*(采用，来源:*[*registerednotesourcestest . kt*](https://github.com/BracketCove/SpaceNotes/blob/master/domain/src/test/java/com/wiseassblog/domain/RegisteredNoteSourceTest.kt)*)*

![](img/88318544d009f943468788e25fe259cb.png)

# 协同程序

MockK 从一开始就支持协程，但在去年，内部引擎变得更加先进，并且也添加了一些语法子句。

首先，您需要记住的是，在 MockK 中所有使用协程的函数都是由常规函数+前缀`co`构建的。比如`every`变成了`coEvery`、`verify` — `coVerify`、`answers` — `coAnswers`。此类函数采用 suspend lambdas 代替常规 lambdas，并允许调用`suspend`函数。

```
*coEvery* **{** mock.divide(capture(slot), any()) 
**}** coAnswers **{** slot.captured * 11
**}**
```

所以第一眼看上去，一切都很像常规的嘲讽。但从内部来看，这并不容易。

第一件事是，当对`divide`函数的调用被拦截时，它将隐式 continuation 参数作为字节码级别的最后一个参数。Continuation 是一个回调函数，在结果准备好时使用。这意味着库需要将这个延续传递给`coAnswers`子句，所以它不仅仅是传递了`runBlocking`suspend lambda。

第二个区别是`divide`暂停功能可能决定暂停。这意味着在内部它将返回特殊的悬浮标记。当计算恢复时，再次调用`divide`。因此，每个这样的恢复调用都将被视为对`divide`函数的额外调用。这意味着您需要在验证过程中小心处理它。

了解这种内部情况并不是一件非常愉快的工作，但不幸的是，没有其他方法来处理它。

![](img/4dd583ec0ed5c6489b9e0209af21faeb.png)

# 验证超时

当处理协程时，人们经常忘记`launch`运行一个并行执行的任务，为了验证这种进程的结果，你需要一个进程等待另一个进程。这可以通过`join`来实现。但是接下来你需要显式地到处传递对`Job`的引用。

为了简化这类测试的编写，MockK 支持超时验证:

```
mockk<MockCls> {
    coEvery { sum(1, 2) } returns 4 launch {
        delay(2000)
        sum(1, 2)
    } verify(timeout = 3000) { sum(1, 2) }
}
```

简单地说，`verify`子句将退出，要么满足验证标准，要么在超时的情况下抛出异常。这样你就不需要明确地等待`launch`中的时刻计算完成。

![](img/36a456a1c87893264a2df8273ea955bf.png)

# 验证确认和记录排除

`verifyAll`和`verifySequence`是两种验证模式，在这两种模式下会验证一组专用的调用。不存在出错和调用次数超过需要的可能性。

一方面,`verifyOrder`和简单的`verify`不能这样工作。另一方面，他们给你更多的灵活性。

为了解决这个差异，您可以在所有验证块之后使用一个特殊的验证确认调用。

```
confirmVerified(object1, object2)
```

这将确保`object1`和`object2`的所有调用都包含在`verify`子句中。

如果您有一些不太重要的调用，您可以通过`excludeRecords`构造将它们从确认验证中排除。

```
excludeRecords { object1.someCall(andArguments) }
```

您可以使用参数匹配器排除一系列调用。

![](img/66e364acc53b73685eb76bf3be77aeba.png)

# 瓦拉格斯

对变量参数的简单处理出现在早期的 MockK 版本中，但是从版本 1.9.1 开始，实现了额外的匹配器。

让我用一个接受变量参数的简单函数来演示这一点:

```
interface VarargExample {
    fun example(vararg sequence: Int): Int
}
```

让我们模仿一下，看看有哪些模仿的可能性:

```
val mock = mockk<VarargExample>()
```

首先，可以将它用作简单的参数:

```
every { mock.example(1, 2, 3, more(4), 5) } returns 6
```

这意味着，如果变量参数数组的大小为 5，并且它匹配所提供的参数，那么`example`函数的结果就是 6。

如果你需要处理可变参数的可变长度，这当然不是很灵活。

为了克服这个限制，MockK 增加了三个匹配器:`anyVararg`、`varargAll`和`varargAny`

```
every { mock.example(*anyVararg()) } return 1
```

这将简单地匹配任何可能的变量参数数组。

您可以添加匹配器的任何前缀或后缀。

```
every { mock.example(1, 2, *anyVararg(), 3) } return 4
```

其将匹配从`1`、`2`开始到`3`结束的至少 3 个元素的数组。

为了进行更高级的匹配`varargAll`允许提供一个 lambda，该 lambda 将设置所有参数(除了前缀和后缀)都应该被满足的条件。

```
every { mock.example(1, 2, *varargAll { it > 5 }, 9) } returns 10
```

这与以下参数数组匹配:

```
mock.example(1, 2, 5, 9)mock.example(1, 2, 5, 6, 9)mock.example(1, 2, 5, 6, 7, 9)
```

以及:

```
mock.example(1, 2, 5, 5, 5, 9)
```

诸如此类。

此外，在这个 lambda 的范围内，`position`和`nArgs`属性可用于允许更复杂的匹配表达式。

```
every { mock.example(1, *varargAll { nArgs > 5}) } returns 6
```

只有在向第一个参数为`1`的`example`传递`6`或更多参数时才会匹配

`varargAny`是一个类似的构造，需要至少一个元素来匹配 lambda 中传递的条件。前缀、后缀、参数`position`和`nArgs`等其他都是一样的。

![](img/74dc22aa649dd9ed2d06edaec03c1843.png)

# 扩展和顶级函数

为了成功地模仿扩展和顶级函数，你需要理解它是如何工作的。

## 顶级功能

首先，让我展示如何模拟顶级函数。

Kotlin 将这些函数翻译成为您的源代码片段创建的特殊类的`static`方法。因此，如果在字节码级别的`Code.kt`源文件中有`lowercase`，它将使用`lowercase`静态方法创建`CodeKt`类。

例如，下面的例子:

```
// Code.kt source filepackage pkgfun lowercase(str: String): String {...}
```

翻译过来就是:

```
package pkg;class CodeKt {
    public static String lowercase(String str) {...}
}
```

现在因为你不能在你的 Kotlin 代码中引用类`CodeKt`，你需要通过使用字符串参数告诉 MockK 这个类名给`mockkStatic`。之后`lowercase`可能会用在不同的嘲讽表达中。

```
mockkStatic("pkg.CodeKt")every { lowercase("A") } returns "lowercase-abc"
```

要确切地知道像`lowercase`这样的函数将在哪里登陆，你需要检查构建产生的实际类文件。

有时候名字很花哨。例如:

```
mockkStatic("kotlin.io.FilesKt__UtilsKt")
every { File("abc").endsWith(any<String>()) } returns **true**
```

在 Kotlin 中有一个特殊的指令来指导编译器把这样的顶级函数放在什么文件中。它叫做`@file:JvmName`

```
@file:JvmName("KHttp")**package** khttp
// ... KHttp code
```

在`mockkStatic`中，你只需要使用这个名字:

```
mockkStatic("khttp.KHttp")
```

## 附加到类或对象的扩展函数

模仿绑定到类或对象的扩展函数也需要一些解释。

您需要理解，字节码级别的分派接收器(`this`与包含扩展函数声明的类或对象相关)是作为 JVM `this`传递的。

扩展接收器(`this`与我们正在执行的类扩展相关)将是字节码级别的第一个参数。这样就有可能为扩展接收器放置一个参数匹配器。在字节码层次上，它将是最后的第一个参数。

```
class ExtensionExample {
    fun String.concat(other: String): String
}val mock = mockk<ExtensionExample>()with (mock) {
    every { any<String>().concat(any<String>()) } returns "result"
}
```

## 顶级扩展函数

要模仿顶级扩展函数，您需要结合这两种方法。

```
// Code.kt source file
package pkgfun String.concat(other: String): String
```

然后在测试中，您需要创建静态模拟，并调用带有参数匹配器的函数作为参数:

```
mockkStatic("pkg.CodeKt")
every { any<String>().concat(any<String>()) } returns "fake value"
```

通常，您可以使用常量或常量和匹配器的组合来代替参数:

```
mockkStatic("pkg.CodeKt")
every { "abc".concat("def") } returns "abc-and-def"
```

希望这个解释能让嘲笑扩展和顶级函数的尝试不那么痛苦。如果没有，请让我在评论中知道什么是不清楚的，我会努力调整文章，以便更好地理解。

![](img/ebfd5834b6553858e67bc2165dd54fa3.png)

# 对象和枚举模拟

嘲讽对象很简单。你只需将一个对象传递给`mockkObject`，对象就变成了`spy`。这使得仍然可以像使用原始对象一样使用它，但是您可以存根、记录和验证行为。

```
object ExampleObject {
    fun sum(a: Int, b: Int): Int
}mockkObject(ExampleObject)
every { ExampleObject.sum(5, 7) } returns 10
```

这种方法可以应用于任何对象。从类、由`object`子句声明的对象、伴随对象或`enum class`元素创建。

![](img/61bc2717de59c17572f7336bb22cb8ef.png)

# 单元返回函数的模拟松弛

严格是一件好事，但是有时候有很多语句模拟函数返回`Unit`而没有真实的行为或结果是没有意义的。在这种情况下，MockK 在完全严格和完全宽松之间提供了一种折衷。

你可以创建一个所谓的“单元返回函数的模拟松弛”:

```
val mock = mockk<ExampleClass>(relaxUnitFun = true)
```

对于这种返回`Unit`的模拟函数，您没有义务指定行为。这样，您可以减少样板文件，并在所有其他情况下保持严格性。

# 模拟函数不返回任何内容

返回`Nothing`是 Kotlin 语言中的特例。

例如，您有一个不返回任何内容的函数:

```
fun quit(status: Int): Nothing {
    exitProcess(status)
}
```

为了嘲弄它而不滥用类型系统和运行时，你唯一的选择就是`throw`一个异常:

```
every { quit(1) } throws Exception("this is a test")
```

# 构造函数模拟

要将刚刚创建的(用构造函数初始化的)对象转换成对象模仿，可以使用所谓的构造函数模仿。通常，您应该避免这样的设计，或者将对象或工厂注入到被测试的对象中，但是有时别无选择。

```
class MockCls {
  fun add(a: Int, b: Int) = a + b
}mockkConstructor(MockCls::class)every { anyConstructed<MockCls>().add(1, 2) } returns 4MockCls().add(1, 2) // returns 4
```

这里你可以看到，使用了`anyConstructed`来表示为某个类构造的所有对象。没有更好的粒度，您无法区分构造函数。尽管在 Kotlin 中通常只创建一个主构造函数。

![](img/91d9d12e381db59d3e203fc2bcdc6e2c.png)

# 私有函数模拟

在极少数情况下，可能需要模仿私有函数。这很乏味，因为你不能直接调用这样的函数。通常，不建议使用这种方法。科特林有一个很好的`internal`修改器，从测试中可以看出。

无论如何，情况可能不同，说这根本不应该使用是对复杂世界的过度简化。

主要问题是这样一个`private`函数应该被动态引用，并且参数的类型应该完全匹配以通过反射来选择它:

```
every { mock["sum"](any<Int>(), 5) } returns 25
```

另一件事是，这样的呼叫在默认情况下不会被记录，否则它们会影响`verifyAll`、`verifySequenece`或`confirmVerified`构造。要启用录制，请通过`recordPrivateCalls = true` 至`mockk`或`spyk`功能。

```
**val** mock = spyk(ExampleClass(), recordPrivateCalls = **true**)
```

此外，还有[]运算符的替代表达式:

```
every {
    mock invoke "openDoor" withArguments listOf("left", "rear")
} returns "OK"verify {
    mock invoke "openDoor" withArguments listOf("left", "rear")
}
```

![](img/9a45dafc64ee609442fbaf12dc162791.png)

# 属性模拟

通常，您可以模仿属性，就好像它是`get/set…`函数或`field`访问一样。但是如果您有`private`属性，或者您需要更详细的语法，或者您需要访问`field`，您可以使用替代方法:

```
every { mock getProperty "speed" } returns 33
every { mock setProperty "acceleration" value less(5) } just Runsverify { mock getProperty "speed" }
verify { mock setProperty "acceleration" value less(5) }every {
   mock.property
} answers { fieldValue + 6 }every {
    mock.property = any()
} propertyType Int**::**class answers { fieldValue += value }every {
    mock getProperty "property"
} propertyType Int**::**class answers { fieldValue + 6 }every {
    mock setProperty "property" value any<Int>()
} propertyType Int**::**class answers  { fieldValue += value }every {
    mock.property = any()
} propertyType Int**::**class answers {
    fieldValue = value + 1
} andThen {
    fieldValue = value - 1
}
```

在这里，你可以看到`propertyType`是一种特殊的属性提示，以防编译器不直接向 MockK 提供该信息。

![](img/60afa8820b4832f3e3222ec512ef541b.png)

# 班级模拟

当你需要创建一个由反射`KClass`对象指定的任意类的模拟时，你可以使用`mockkClass`函数。

```
val mock = mockkClass(ExampleClass::class)
```

# 设置

MockK 支持很少的全局开关。要激活它们，您应该根据这个模板将`io/mockk/settings.properties`放在您的类路径中:

```
relaxed=true|false
relaxUnitFun=true|false
recordPrivateCalls=true|false
```

这允许在默认情况下放松模拟，或者为返回函数的单元放松模拟，或者记录私有调用。

# 清除

到目前为止，关于正确清理有许多问题。我意识到这是一个重要的话题，我想提出一个很好的解决方案。虽然现在我还不了解所有的情况，但这可能会是突破性的改变。

这里我将只告诉当前的函数集在做什么。`clear`函数删除与模仿相关的对象的内部状态，`unmock`函数返回类的转换。

`clearAllMocks` —清除所有嘲笑。如果您有简单的顺序测试，这将是一个最佳选择。

`clearMocks` —清除特别指定的规则或对象模拟。在并行测试中很有价值，因为`clearAllMocks`可能会干扰你的其他测试。

`clearStaticMockk`、`clearConstructorMockk` —类似地清理特别指定的静态或构造函数模拟。我认为如果你在并行测试中使用相同的类，你会遇到麻烦。测试框架需要用不同的类加载器隔离测试。我不确定是否有框架在这么做。

`unmockkObject` —返回特定规则或对象模拟的转换。

`unmockkAll` —同样，如果您只有顺序测试，并且需要将所有内容恢复到初始状态，这可能是最佳选择。

`unmockkStatic`、`unmockkConstructor` —静态模拟的返回转换。类似于`clearStaticMock`的情况——对相同的类进行并行测试可能不安全。

`mockkObject`、`mockkStatic`、`mockkConstructor`有最后一个参数为λ的版本。之后会做相应的`unmockk`操作:

```
inline fun mockkStatic(vararg classes: String, block: () -> Unit) {
    mockkStatic(*classes)
    try {
        block()
    } finally {
        unmockkStatic(*classes)
    }
}
```

清理工作到此为止。

![](img/52ce02b040bc50b5ea473924fc085616.png)

在这一系列文章中，我们讨论了与嘲讽和[嘲讽](https://mockk.io)相关的所有主题。从最基本的到高级功能。这篇文章是关于高级特性的。mock 有一个非常广泛的集合:分层模拟、协程、验证超时、验证确认&记录排除、变量、扩展&顶级函数、对象&枚举模拟、单元返回函数的模拟宽松、不返回任何内容的模拟函数、构造函数模拟、私有函数模拟、属性模拟、类模拟、设置和清理。

谢谢大家！

[![](img/5cf9179effdb3e8ab1a9bf39598fdfd2.png)](https://kt.academy/article)

## 单击👏说“谢谢！”并帮助他人找到这篇文章。

了解卡帕头最新的重大新闻。学院，[订阅时事通讯](https://kotlin-academy.us17.list-manage.com/subscribe?u=5d3a48e1893758cb5be5c2919&id=d2ba84960a)，[观察推特](https://twitter.com/ktdotacademy)并在媒体上关注我们。

[![](img/bf0a5f94aadb4cca1bc11e05f6dfcb64.png)](https://twitter.com/ktdotacademy)