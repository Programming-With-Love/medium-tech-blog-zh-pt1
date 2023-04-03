# Swift 泛型和关联类型

> 原文：<https://medium.com/globant/swift-generics-and-associated-types-73aa2b184c7a?source=collection_archive---------0----------------------->

![](img/165835d03224a3a818fa108665cecec1.png)

# **简介**

泛型是 Swift 的一大特色，它允许实现支持多种类型的可重用和抽象代码。这避免了代码重复，使代码更易于维护。Swift 标准库中的数组、字典和其他功能都是使用泛型实现的。

# **泛型语法和例子**

下面是反转整数数组的实现:

Reverse Int array

要实现类似的字符串数组，函数定义如下:

Reverse String array

可以实现类似的数组方法来反转数组，而不管元素是 Int、String 类型还是任何使用泛型的类或结构。为占位符指定有意义的名称，而不是像“T”这样的随机名称。所以我们写了一个通用版本的函数来反转数组。每次调用通用函数时，确定用来代替*元素*的实际类型:

Generic reverse function

例如，如果我们必须返回数组的反向，排除一些相同类型的值，我们可以添加类型约束，否则编译器将返回错误:

![](img/83248483ddd41de6938cbfc9d09a187a.png)

Error when type constraint not added

现在我们将实现一个方法，该方法返回数组的逆矩阵，不包括数组中的某些值，即不等于某个值。
Swift 已经为此提供了等同的协议。符合等价协议的类型可以使用==比较相等性，或者使用“！= "运算符。
我们知道类型必须符合 Equatable，让我们看看指定它的语法:

Equatable 是一个协议，但是也可以在约束中指定类和结构类型。

# **通用类型**

Swift 允许定义通用类型，如自定义类、结构和枚举。通过编写要存储为尖括号内的值的类型，可以创建 ListNode 的新实例。添加扩展时，不需要指定占位符类型“元素”。

Creating generic types and extensions

# **通用 where 子句**

通用 where 子句允许向扩展添加新的需求。如果我们必须检查链表中是否存在列表节点，我们将必须检查两个列表节点对象是否相等。我们可以使用通用的 where 子句来指定。

Generics where clauses to add additional requirements

在扩展中使用 where 子句，仅当*节点值*为 *CustomStringConvertible 时才添加方法 printDescription()。*

# **关联类型**

由于引入了面向协议的编程，我们通常首先通过添加协议来开始实现。泛型可以通过 associatedtype 在协议中使用。

我们可以指定 Any/AnyObject 来代替泛型类型，但这会影响性能，因为对象的类型将在运行时确定。因为任何类型的任何对象都可以作为参数传递，所以我们需要添加代码来将 Any/AnyObject 类型转换为所需的具体类型。

一个*关联的类型*为一个类型提供一个占位符名称，该类型被用作协议的一部分。在采用该协议之前，不会指定关联类型的实际类型。

下面的代码示例包含与泛型 where 子句和泛型下标示例关联的类型。让我们讨论一下每个特性。在代码片段后有对每个提到的功能的描述:

Associated types in Swift

***type alias Element = Int***的定义将元素的抽象类型转化为 Int 的具体类型，用于此实现。

即使我们没有提到 typealias，由于 Swift 的类型推断，一切仍然有效，因为协议方法是用参数类型 Int 定义的。

我们仍然可以创建一个通用的链表类型结构，并通过添加 typealias 来确认“linked list”协议，如下所示:
**type alias**Node = list Node<NodeType>

我们还可以为协议中的关联类型指定类型约束和 where 子句，以要求确认类型满足这些约束。在这个例子中，*迭代器*上的通用 where 子句要求迭代器必须遍历链表中作为*节点的类型的元素。*

我们还可以通过在下标后的“<>”中写入占位符类型名称来添加通用下标。泛型下标的语法包含在链表示例中，该示例返回特定索引处的列表节点。

Swift 标准库广泛使用了泛型和相关类型。我在 Swift 中包含了协议的文档链接。

感谢阅读！

# **参考文献**

*   [https://swiftdoc.org](https://swiftdoc.org)