# 泛型是 Go 的泛型

> 原文：<https://medium.com/capital-one-tech/generics-are-the-generics-of-go-3e0ef0cb9e04?source=collection_archive---------0----------------------->

## Go 可能很快就会包含它最受欢迎的功能

![](img/e690c0445680a8e4d857b9ecbabfe13c.png)

大约三年前，我在为 GitHub 开发一个名为 [Checks-Out](https://github.com/capitalone/checks-out) 的 pull request approval manager。在构建集成层时，我遇到了一种情况，Go 中的直接方法过于重复。理想的解决方案是使用泛型，但是它们不可用。

然而，我发现在很多情况下，可以使用闭包来传递值。我在一篇[博客文章](https://www.capitalone.com/tech/software-engineering/closures-are-the-generics-for-go/)和一篇[演讲](https://www.youtube.com/watch?v=5IKcPMJXkKs)中描述了我对这个问题的体验和我的解决方案，这两篇文章都被称为闭包，是 Go 的泛型。

***事情即将发生变化。***Go 团队发布了一篇[博客文章](https://blog.golang.org/generics-next-step)和一份[草案规范](https://go.googlesource.com/proposal/+/refs/heads/master/design/go2draft-type-parameters.md)，描述了一个潜在的 Go 泛型实现。提议草案使用参数化类型来添加人们所要求的大多数泛型功能，而不改变 Go 的基本特征。

# 在 Go 中引入泛型

[Go 是一种有意设计的小型语言，其设计偏好良好的工具性和可读性，而不是特性。](https://www.capitalone.com/tech/software-engineering/go-is-boring/)围棋团队也看重向后兼容性；旧代码应该继续工作，这样它可以被维护很多年。通用名草案就是考虑到这些目标而编写的。

我们将通过尝试最简单的情况来开始我们对 Go 泛型的研究:*一个用户定义的容器类型*。我们将使用一个链表作为我们的示例容器类型。下面是今天在 Go 中写一个的样子:

```
type LinkedList struct {
	value interface{}
	next  *LinkedList
}
```

下面是使用泛型时的情况:

```
type LinkedList[type T] struct {
	value T
	next  *LinkedList[T]
}
```

将此类型更改为泛型类型需要三个小的改动:

1.  将`[type T]`放在类型名之后和结构文字之前。`T`是一个名字，当我们的链表被使用时，我们将使用它作为任何类型的占位符。许多其他语言使用`T`作为泛型类型的占位符类型名，很可能 Go 也会采用同样的习惯。如果需要引用同一类型或函数中的其他类型，请使用其他大写字母；我们一会儿就能看到。
2.  使用`T`代替`interface{}`作为`value`字段的类型。
3.  将`next`指针的类型从`*LinkedList`改为`*LinkedList[T]`。使用泛型类型时，必须提供类型参数。省略它们是一个编译时错误。

让我们写一些方法来使用我们的泛型类型。我们将从查找链表长度的方法开始:

```
func (ll *LinkedList[T]) Len() int {
	count := 0
	for node := ll; node != nil; node = node.next {
		count++
	}
	return count
}
```

方法接收器使用`*LinkedList[T]`而不是`*LinkedList`，但是代码的其余部分与您在不使用泛型的情况下编写的代码是相同的。让我们编写一个引用参数化类型的方法:

```
func (ll *LinkedList[T]) InsertAt(pos int, value T) *LinkedList[T] {
	if ll == nil || pos <= 0 {
		return &LinkedList[T]{
			value: value,
			next:  ll,
		}
	}
	ll.next = ll.next.InsertAt(pos-1, value)
	return ll
}
```

这个方法接受一个类型为`[T]`的参数

(这个方法不是插入链表的最有效的方法，但是它足够短，可以作为一个很好的例子。还要注意是安全的；如果您为插入索引传递 0 或负数，它将添加到链表中，如果您传递一个大于长度的数，它将简单地追加。)

下面是一些对我们的链表有用的附加方法:

```
func (ll *LinkedList[T]) Append(value T) *LinkedList[T] {
	return ll.InsertAt(ll.Len(), value)
}func (ll *LinkedList[T]) String() string {
	if ll == nil {
		return "nil"
	}
	return fmt.Sprintf("%v->%v", ll.value, ll.next.String())
}
```

现在我们有了一些关于泛型类型的有用方法，让我们试一试:

```
var head *LinkedList[string]
head = head.Append("Hello")
fmt.Println(head.String())
fmt.Println(head.Len())
head = head.Append("Hola")
head = head.Append("हैलो")
head = head.Append("こんにちは")
head = head.Append("你好")
fmt.Println(head.String())
fmt.Println(head.Len())
```

(当传递一个值给`fmt.Println`时，我们不需要显式调用`String`，但是我想让它显式。更多信息见[https://tour.golang.org/methods/17](https://tour.golang.org/methods/17)。)

这看起来与现有的 Go 代码完全一样，只有一个变化:当声明一个类型为`*LinkedList`的变量时，我们提供了我们希望用于这个特定实例的类型。这段代码打印出来:

```
Hello->nil
1
Hello->Hola->हैलो->こんにちは->你好->nil
5
```

如果我们想使用不同类型的链表，我们只需在实例化不同的变量时提供不同的类型。如果我们有一个`type Person`:

```
type Person struct {
	Name string
	Age  int
}
```

我们可以编写以下代码:

```
var peopleList *LinkedList[Person]
peopleList = peopleList.Append(Person{"Fred", 23})
peopleList = peopleList.Append(Person{"Joan", 30})
fmt.Println(peopleList)
```

它打印出:

```
{Fred 23}->{Joan 30}->nil
```

正如[Go Playground](https://play.golang.org/)允许你尝试当前的 Go 代码一样，Go 团队已经把[变成了一个测试通用代码的新游戏场](https://go2goplay.golang.org/)。你可以在 https://go2goplay.golang.org/p/OUX5OKRMqQw[试试我们的链表。](https://go2goplay.golang.org/p/OUX5OKRMqQw)

让我们尝试一些新的东西。我们将在链表中添加另一个方法来告诉我们一个特定的值是否在链表中:

```
func (ll *LinkedList[T]) Contains(value T) bool {
	for node := ll; node != nil; node = node.next {
		if node.value == value {
			return true
		}
	}
	return false
}
```

不幸的是，这是行不通的。如果我们试图编译它，我们会得到错误:

```
cannot compare node.value == value (operator == not defined for T)
```

问题是我们的占位符类型`T`没有指定它能做什么。到目前为止，我们能做的就是存储和检索它。如果我们想做得更多，我们必须在`T`上指定一些约束。

因为很多(但不是全部！)Go 类型可以使用`==`和`!=`进行比较，Go generics 提案包括一个新的内置接口，名为`comparable`。如果我们回到链表类型的定义，我们可以做一点小小的改变来支持`==`:

```
type LinkedList[type T comparable] struct {
	value T
	next  *LinkedList[T]
}
```

我们将接口`comparable`添加到我们的类型参数定义子句中，现在我们可以使用`==`在`LinkedList`的方法中比较`T`类型的变量。

使用我们以前的数据，如果我们运行以下行:

```
fmt.Println(head.Contains("Hello"))
fmt.Println(head.Contains("Goodbye"))
fmt.Println(peopleList.Contains(Person{"Joan", 30}))
```

您会得到以下结果:

```
true
false
true
```

你可以看到这段代码运行在[https://go2goplay.golang.org/p/dO7npX6IeaQ](https://go2goplay.golang.org/p/dO7npX6IeaQ)。

但是，我们不能再给`LinkedList`分配不可比较的类型。如果我们试图创建一个函数链表:

```
var functionList *LinkedList[func()]
functionList = functionList.Append(func() { fmt.Println("What about me?") })
fmt.Println(functionList)
```

它将在编译时失败，并显示错误消息:

```
func() does not satisfy comparable
```

除了泛型类型，您还可以编写泛型函数。对 Go 最常见的抱怨之一是，你不能编写一个函数来处理任何类型的切片。让我们写三个:

```
func Map[type T, E](in []T, f func(T) E) []E {
	out := make([]E, len(in))
	for i, v := range in {
		out[i] = f(v)
	}
	return out
}func Reduce[type T, E](in []T, start E, f func(E, T) E) E {
	out := start
	for _, v := range in {
		out = f(out, v)
	}
	return out
}func Filter[type T](in []T, f func(T) bool) []T {
	out := make([]T, 0, len(in))
	for _, v := range in {
		if f(v) {
			out = append(out, v)
		}
	}
	return out
}
```

就像泛型一样，泛型函数也有类型参数部分。对于函数，它出现在函数名和函数参数之间。对于`Map`和`Reduce`，我们在函数中使用了两个类型参数，都在类型参数部分声明，并用逗号分隔。如果类型是特定的，函数体与您使用的函数体是相同的；唯一不同的是，我们在`Map`中将`[]E`传递给`make`，在`Filter`中将`[]T`传递给`make`。

当我们运行代码时:

```
strings := []string{"1", "2", "Fred", "3"}
numStrings := Filter(strings, func(s string) bool {
	_, err := strconv.Atoi(s)
	return err == nil
})
fmt.Println(numStrings)nums := Map(numStrings, func(s string) int {
	val, _ := strconv.Atoi(s)
	return val
})
fmt.Println(nums)total := Reduce(nums, 0, func(start int, val int) int {
	return start + val
})
fmt.Println(total)
```

我们得到输出:

```
1 2 3]
[1 2 3]
6
```

在 https://go2goplay.golang.org/p/ek3QTecbSL3 的[亲自尝试一下。](https://go2goplay.golang.org/p/ek3QTecbSL3)

需要注意的一点是:在调用函数时，我们没有明确指定类型。Go 泛型使用类型推断来判断函数调用使用哪种类型。有些情况下这不起作用(比如用于返回类型的类型参数，而不是输入参数)。在这些情况下，您需要指定所有类型参数。

让我们试着写另一个通用函数。Go 有一个`math.Max`函数，比较两个`float64`值并返回较大的一个。这样写是因为 Go 中几乎任何其他数值类型都可以转换为`float64`进行比较(琐事时间:需要超过 53 位来表示其值的`uint64`或`int64`在转换为`float64`时会失去精度)。来回转换是很难看的，所以让我们试着写一个通用函数来做这件事:

```
func Max[type T](v1, v2 T) T {
    if v1 > v2 {
        return v1
    }
    return v2
}
```

不幸的是，如果我们试图编译这个函数，我们会得到一个错误:

```
cannot compare v1 > v2 (operator > not defined for T)
```

这很像我们试图比较链表中的值时得到的错误，只是这次是使用了`>`操作符而不是`==`。Go 不打算提供一个内置接口来支持其他运营商。在这种情况下，我们必须使用*类型列表*编写自己的接口:

```
type Ordered interface {
    type string, int, int8, int16, int32, int64, float32, float64, uint, uint8, uint16, uint32, uintptr
}func Max[type T Ordered](v1, v2 T) T {
    if v1 > v2 {
        return v1
    }
    return v2
}
```

为了使用操作符，我们声明一个接口并列出支持我们想要使用的操作符的类型。请注意，有效的操作符是适用于所列类型的所有类型的操作符。例如，使用`Ordered`作为类型约束的通用函数或类型不能使用`-`或`*`，因为它们不是为`string`定义的。

既然我们已经有了接口约束，我们可以将任何这些指定类型(或任何用户定义类型，其底层类型是这些类型之一)的实例传递到`Max`:

```
fmt.Println(Max(100, 200))
fmt.Println(Max(3.5, 1.2))
fmt.Println(Max("sheep", "goat"))
```

这会产生以下输出:

```
200
3.5
sheep
```

你可以在 https://go2goplay.golang.org/p/g2N1Zjc4oBs 亲自尝试一下。

类型列表中指定的类型是*基础类型*。(参见[Go 语言规范](https://golang.org/ref/spec#Types)中的定义)。这意味着下面的代码也可以工作:

```
type MyInt intvar a MyInt = 10
var b MyInt = 20
fmt.Println(Max(a,b))
```

老实说，我不喜欢类型列表。然而，它们提供了一种非常简洁的方式来指定哪些操作符是可用的。它们还允许您指定可以将哪些文字赋给泛型类型的变量。正如可用的操作符是类型列表中所有类型的操作符的交集一样，可以赋值的文字就是可以赋给所有列出的类型的文字。在`Ordered`的情况下，您不能分配一个文本，因为没有可以同时分配给`string`和任何数字类型的文本。

您可以使用任何接口作为类型约束，而不仅仅是`comparable`或带有类型列表的接口。用作类型约束的接口可以包含方法和类型列表。但是，不能将具有类型列表的接口用作常规接口类型。

泛型方面的内容比我在这里介绍的要多得多。通读[Go Generics Draft](https://go.googlesource.com/proposal/+/refs/heads/master/design/go2draft-type-parameters.md)(正式名称为*Type Parameters——Draft Design*)以查看关于设计草案的更多细节，它没有涵盖的内容，以及附加的示例代码。

# 泛型与接口

虽然 Go 重用接口的概念来实现泛型非常好，但它确实会导致一点混乱。问题是:*什么时候用泛型，什么时候用接口？*

现在还为时尚早，所以模式还在开发中。有一些基本原则可能会被遵循。首要原则是什么都不做。如果您当前的代码使用接口，就不要去管它。把泛型留给那些不能单独用接口解决的情况:

1.  如果你有一个容器类型，当泛型可用时，考虑切换到泛型。为需要反射的情况保存接口{}。
2.  如果您一直在编写函数的多种实现来处理不同的数值类型或切片类型，请切换到泛型。
3.  如果你想写一个函数或者方法来创建一个新的实例，你需要使用泛型。

人们问的下一个问题是关于性能的。答案是:*暂时不用担心。*当前的原型工具使用的技术(将通用 Go 代码重写为标准 Go 代码)不会在任何产品发布中使用。有多种方法可以编译和实现泛型。一旦有了最终的工具，我们将能够看到权衡是什么。对于大多数程序来说，可能不会有显著的差别。

# 少了什么？

如果你是一个语言极客，你可能知道其他语言中泛型的其他特性。他们中的许多人可能会被排除在 Go 的泛型之外。其中包括:

*   专门化(为特定类型提供泛型函数的特例实现)。
*   元编程(编译时生成代码的代码)。
*   运算符方法(生成一个支持>、*或 Operator)等运算符的泛型类型)。
*   Currying(通过指定一些参数化类型，基于泛型类型创建新的类型或函数)。

# 下一步是什么？

泛型设计仍处于草案阶段；很可能会有进一步的调整。如果该设计成为一个提议，并且该提议被接受，那么包含泛型的最早版本可能是 Go 1.17。

现在还为时尚早，但我对这种设计的前景感到兴奋。它增加了最需要的特性，而不会使语言变得更加复杂。有些人会对其他高级功能被遗漏感到失望，但这不是正确的做法。Go 旨在成为一种易读、易学、易维护的简单语言。通过添加足够的泛型来解决最常见的问题，Go 继续满足这一理想。

*披露声明:2020 资本一。观点是作者个人的观点。除非本帖中另有说明，否则 Capital One 不隶属于所提及的任何公司，也不被这些公司认可。使用或展示的所有商标和其他知识产权是其各自所有者的财产。*