# 学习使用 Go 反射

> 原文：<https://medium.com/capital-one-tech/learning-to-use-go-reflection-822a0aed74b7?source=collection_archive---------0----------------------->

## [](/@jon_43067)**连发五发**

**![](img/2316aecb8df3329a624a49c51b8e902d.png)**

# ****什么是反思？****

**大多数时候，Go 中的变量、类型和函数都非常简单。当您需要一个类型时，您可以定义一个类型:**

**当你需要一个变量时，你定义一个变量:**

**当你需要一个函数时，你定义一个函数:**

**但是有时你想在运行时使用程序编写时不存在的信息来处理变量。也许您正试图将文件或网络请求中的数据映射到变量中。也许你想建立一个工具，与不同类型的作品。在那些情况下，你需要使用*反射*。反射使您能够在运行时检查类型。它还允许您在运行时检查、修改和创建变量、函数和结构。**

**[![](img/a94559d31aeb80a7fd55aff68f92d34c.png)](https://twitter.com/CapitalOneDevEx)**

**Go 中的反射是围绕三个概念构建的: ***类型、种类、值*** 。标准库中的反射包是在 Go 中实现反射的类型和函数的家。**

# ****找到你的类型****

**首先让我们看看类型。您可以使用反射，通过函数调用 varType := reflect 来获取变量 var 的类型。类型(变量)。这将返回反射类型的变量。类型，它的方法包含了定义传入变量的类型的各种信息。**

**我们要看的第一个方法是 Name()。毫不奇怪，这将返回类型的名称。一些类型，如切片或指针，没有名字，这个方法返回一个空字符串。**

**下一个方法，也是我认为第一个真正有用的方法，是 Kind()。种类是类型的组成部分——切片、映射、指针、结构、接口、字符串、数组、函数、int 或其他一些原始类型。种类和类型之间的区别很难理解，但是请这样想。*如果定义了一个名为 Foo 的 struct，则种类为 struct，类型为 Foo* 。**

**使用反射时需要注意的一点是:反射包中的所有东西都假设您知道自己在做什么，如果使用不当，许多函数和方法调用将会死机。例如，如果调用 reflect 上的方法。类型关联的类型与当前类型不同，您的代码将会出错。永远记住使用你的反射类型来知道哪些方法将会工作，哪些方法将会崩溃。**

**如果您的变量是指针、映射、切片、通道或数组，您可以使用 varType 找出包含的类型。Elem()。**

**如果您的变量是一个结构，您可以使用反射来获取该结构中的字段数，并获取反射中包含的每个字段的结构。结构字段结构。反映。StructField 为您提供了字段上的名称、顺序、类型和结构标记。**

**因为几行源代码抵得上千言万语，所以这里有一个简单的例子来转储各种变量的类型信息:**

**输出如下所示:**

```
Type is  and kind is slice
	 Contained type:
	 Type is int and kind is int
 Type is string and kind is string
 Type is  and kind is ptr
	 Contained type:
	 Type is string and kind is string
 Type is Foo and kind is struct
	 Field 1 name is A type is int and kind is int
		 Tag is tag1:"First Tag" tag2:"Second Tag"
		 tag1 is First Tag tag2 is Second Tag
	 Field 2 name is B type is string and kind is string
 Type is  and kind is ptr
	 Contained type:
	 Type is Foo and kind is struct
		 Field 1 name is A type is int and kind is int
			 Tag is tag1:"First Tag" tag2:"Second Tag"
			 tag1 is First Tag tag2 is Second Tag
		 Field 2 name is B type is string and kind is string
```

*****你可以在***[***【https://play.golang.org/p/lZ97yAUHxX】***](https://play.golang.org/p/lZ97yAUHxX)运行这个例子**

# ****创建新实例****

**除了检查变量的类型，您还可以使用反射来读取、设置或创建值。首先你需要使用 refVal := reflect。ValueOf(var)创建一个反射。变量的值实例。如果希望能够使用反射来修改值，就必须用 refPtrVal := reflect 获得一个指向变量的指针。value of(& var)；如果没有，可以使用反射读取值，但不能修改它。**

**一旦你有了反思。值，你可以得到反映。使用 Type()方法的变量的类型。**

**如果你想修改一个值，记住它必须是一个指针，并且你必须首先取消对指针的引用。您使用 refPtrVal。Elem()。Set(newRefVal)进行更改，传递给 Set()的值必须是一个反射。也值。**

**如果您想创建一个新值，可以使用函数调用 newPtrVal := reflect 来实现。New(varType)，传入一个 reflect.Type。这将返回一个指针值，然后您可以修改它。使用 Elem()。如上所述设置()。**

**最后，您可以通过调用 Interface()方法返回到普通变量。因为 [Go 没有泛型](/capital-one-developers/closures-are-the-generics-for-go-cb32021fb5b5)，所以变量的原始类型丢失；该方法返回接口{}类型的值。如果您创建了一个指针以便可以修改值，那么您需要使用 Elem()取消对反射指针的引用。接口()。在这两种情况下，您都需要将空接口转换为实际类型才能使用它。**

**以下是演示这些概念的一些代码:**

**输出如下所示:**

```
hello
goodbye
{A:20 B:Greetings}, 20, Greetings
```

*****你可以在***[***https://play.golang.org/p/PFcEYfZqZ8***](https://play.golang.org/p/PFcEYfZqZ8)运行这个例子**

# ****有制作无制作****

**除了生成内置和用户定义类型的实例之外，还可以使用反射来生成通常需要 make 函数的实例。您可以使用反射制作切片、贴图或通道。做一个切片，反射。制作地图，并反映出来。MakeChan 函数。在所有情况下，你提供一个反射。键入并得到一个反射。可以用反射操作的值，或者可以赋回标准变量的值。**

**此代码的输出是:**

```
[10]
map[hello:10]
```

*****你可以在***[](https://play.golang.org/p/z4tnyEf6bH)****运行例子。******

# *****制作功能*****

***反射不仅仅让你创造新的地方来存储数据。您可以使用反射来创建新的函数。MakeFunc 函数。这个函数需要反射。我们要创建的函数的类型，以及输入参数类型为[]reflect 的闭包。值，其输出参数也是[]reflect.Value 类型。下面是一个简单的例子，它为传递给它的任何函数创建一个计时包装器:***

***您可以在这里运行代码[https://play.golang.org/p/QZ8ttFZzGx](https://play.golang.org/p/QZ8ttFZzGx)并查看输出:***

```
*starting
ending
calling main.timeMe took 1s
starting
ending
calling main.timeMeToo took 2s
4*
```

# ***我想要一个新的结构***

***在 Go 中使用反射还可以做一件事。通过传递一个反射片，你可以在运行时创建全新的结构。要反射的 StructField 实例。函数的结构。这张有点奇怪。我们正在制造一个新的类型，但是我们还没有给它起一个名字，所以你不能真的把它变回一个“正常”的变量。您可以创建一个新的实例，并使用`Interface()` 将值放入 interface{}类型的变量中，但是如果您想要在其上设置任何值，则需要使用反射。***

***运行此代码将返回:***

```
*0
20

reflect me
[]
[1 2 3]*
```

***【https://play.golang.org/p/lJiTP6vYYN】*的代号是*[](https://play.golang.org/p/lJiTP6vYYN)***

# ******你有什么不能做的？******

****反射有一个很大的限制。虽然您可以使用反射来创建新的*函数*，但是无法在运行时创建新的*方法*。这意味着您不能在运行时使用反射来实现接口。这也意味着使用反射来创建一个新的结构可能会以奇怪的方式中断。当您从一部分结构字段中创建新结构时，与 Go 中我最喜欢的特性之一——通过匿名结构字段的委托——的交互会出现一些问题。****

****下面是对委托的快速回顾。大多数情况下，当结构中有一个字段时，需要给它一个名称。在本例中，我们有两种类型，Foo 和 Bar:****

****如果你在 https://play.golang.org/p/aeroNQ7bEI 运行这段代码，你会看到两件事。首先，Bar 中的 Foo 字段没有名字。这使得它成为一个匿名或嵌入字段。第二，Bar 被视为遇到 Doubler 接口，即使 Double 方法仅由 Foo 实现。这种能力叫做委托；在编译时，Go 会自动在 Bar 上生成与 Foo 上的方法相匹配的方法。这不是继承。如果您试图将一个 Bar 传递给一个需要 Foo 的函数，您的代码将无法编译。****

****然而，如果您使用反射来构建一个具有嵌入字段的结构，并试图访问这些字段上的方法，您会得到一些非常奇怪的行为。最好的办法是不要使用它们。Go GitHub 库中有一个问题要解决这个 https://github.com/golang/go/issues/15924 的[，另一个问题是要求用一组方法](https://github.com/golang/go/issues/15924)[https://github.com/golang/go/issues/16522](https://github.com/golang/go/issues/16522)定义一个新类型的通用能力。不幸的是，这两个问题暂时都没有任何进展。****

****这有什么大不了的？如果我们能动态实现接口，我们能做什么？嗯，就像我们能够通过利用 Go 对生成函数的支持来为函数生成包装器一样，我们也可以为接口生成包装器。在 Java 中，这种功能被称为动态代理。当与注释结合使用时，它提供了一种从命令式编程风格转变为声明式编程风格的强大方法。JDBI 就是一个很好的例子。它是一个 Java 库，允许您通过定义一个用 SQL 查询注释的接口来构建 DAO 层。通常为支持数据库交互而编写的所有样板代码都是在运行时动态生成的。那是强大的。****

# ******太好了，但是有什么意义呢？******

****但是即使有这个限制，反射仍然是一个强大的工具，每个 Go 开发人员都应该在他们的工具箱中拥有它。但是他们能用它做什么呢？在下一篇博文中，我将探索反射在现有库中的一些用途，并使用反射构建一些新的东西。****

## ****在此阅读第 2 部分[。](/capital-one-developers/learning-to-use-go-reflection-part-2-c91657395066)****

*****披露声明:这些观点是作者的观点。除非本帖中另有说明，否则 Capital One 不属于所提及的任何公司，也不被其认可。使用或展示的所有商标和其他知识产权是其各自所有者的所有权。本文为 2017 首都一。*****

# ****附加链接****

****[在不久的将来…](/capital-one-developers/in-the-not-too-distant-future-e2d0ad28e91)****

****[缓冲通道——它们有什么用？](/capital-one-developers/buffered-channels-in-go-what-are-they-good-for-43703871828)****

****闭包是 Go 的泛型****

****[在 Go 中构建无界通道](/capital-one-developers/building-an-unbounded-channel-in-go-789e175cd2cd)****

****[在 Go 中构建 REST API](https://developer.capitalone.com/blog-post/building-a-serverless-rest-api-in-go/)****