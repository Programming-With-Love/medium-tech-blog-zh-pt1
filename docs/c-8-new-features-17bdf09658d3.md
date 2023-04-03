# C# 8 —新功能

> 原文：<https://medium.com/globant/c-8-new-features-17bdf09658d3?source=collection_archive---------0----------------------->

C#的新特性和增强功能概述

![](img/d78366434eeed9e0a477b240d22f3a42.png)

Photo by [Joshua Aragon](https://unsplash.com/@goshua13?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

在使用 c# 20 年后，自第一个版本以来，已经发生了很多变化，C# 8 在 2019 年 9 月发布最新稳定版本后，正在积极进入创新世界。因为这个策略，微软让很多开发者处于一种迷茫的状态。目前，开发人员对 C# 8 的一些新特性有非常有争议的看法。

在本文中，您可以对 C# 8 的新特性有一个简要的概述；我会分别描述重要的特性并用一个例子来演示，我也会简单处理不太重要的特性。我还将写下每个即将到来的重要特性的优缺点。我希望在读完这篇文章后，你会对 C# 8 的特性有一个清晰的认识。

# C# 8 的新特性

这些是我们将要讨论的 C#的新特性。

*   默认接口方法
*   可为空的引用类型
*   模式匹配增强
*   异步流/异步一次性
*   使用声明
*   插值逐字字符串的增强
*   零合并赋值
*   静态局部函数
*   指数和范围
*   非托管构造类型
*   只读成员
*   一次性引用结构

# 默认接口方法

[默认接口方法](https://docs.microsoft.com/en-us/dotnet/csharp/whats-new/csharp-8#default-interface-methods)允许你添加新的功能到你的库的接口，并确保向后兼容为那些接口的老版本编写的代码。请参见下面的示例。

```
interface IWriteLine  
{  
   public void WriteLine()  
   {  
     Console.WriteLine("Wow C# 8!");  
   }  
}
```

这是一个很有争议的特性，它在。网络社区。这里没有什么新东西。这个概念已经在许多语言中得到应用，并且是从 Java 中克隆出来的。它基于[特征技术](https://en.wikipedia.org/wiki/Trait_(computer_programming)#:~:text=In%20computer%20programming%2C%20a%20trait,the%20functionality%20of%20a%20class.)，特征是面向对象编程中一种经过验证的强大技术。作为一名专业人士，我认为你可以在不破坏旧版本界面兼容性的情况下给界面添加新功能，但应该小心使用。否则，很容易导致违反单一责任原则。

# 可空引用类型

如果一个不能为空的变量被赋值为空，一个[可为空的引用类型](https://docs.microsoft.com/en-us/dotnet/csharp/whats-new/csharp-8#nullable-reference-types)会发出一个编译器警告或错误。

```
string? nullableString = null;  
Console.WriteLine(nullableString.Length); // WARNING: may be null! Take care!
```

一般来说，这个特性在。网络社区。这是一项经过验证的技术，也是微软的一项很好的创新。它帮助您消除 NullReferenceException。此外，它还能帮助你解决代码中的问题，就像末日金字塔一样。然而，在复杂的场景中，这个特性可能会在引用类型和使用“？”方面带来一些混乱更多信息，请阅读来自[乔恩·斯基特博客](https://codeblog.jonskeet.uk/2019/02/10/nullableattribute-and-c-8/)的这篇文章。

# 高级模式匹配

[高级模式匹配](https://docs.microsoft.com/en-us/dotnet/csharp/whats-new/csharp-8#more-patterns-in-more-places)提供了解构匹配对象的能力，让你可以访问它们的部分数据结构。C#提供了一组丰富的模式，可用于匹配。请参见下面的示例。

```
var point = new 3DPoint(1, 2, 3); //x=1, y=2, z=3  
if (point is 3DPoint(1, var myY, _))  
{  
  // Code here will be executed only if 
  // the point .X == 1, myY is a new variable  
  // that can be used in this scope.  
}
```

即使这里没有什么新鲜的东西，它也很受欢迎。网络社区。递归模式匹配帮助您以一种非常方便、简洁的语法来分解和导航数据结构。而模式匹配在概念上类似于一系列(if，then)语句，所以它将帮助您以函数式风格编写代码。但是对于复杂的表达式，语法可能会很复杂，很难理解。[模式匹配](https://docs.scala-lang.org/tour/pattern-matching.html)是一项经过验证的已知技术，已经使用了很多年，尤其是在函数式编程中。

# 异步流

允许使用“async”迭代集合。

```
await foreach (var x in enumerable)  
{  
  Console.WriteLine(x);  
}
```

[异步流](https://docs.microsoft.com/en-us/dotnet/csharp/whats-new/csharp-8#asynchronous-streams)提供了一种极好的方式来表示可由消费者控制的异步数据源。例如，当从 web 下载数据时，我们希望创建一个异步集合，在数据可用时以块的形式返回数据。这是一个广为接受的功能，但这里没有什么新的东西！这种技术已经在许多其他语言(AKA)流中使用。

# 范围

[Ranges](https://docs.microsoft.com/en-us/dotnet/csharp/whats-new/csharp-8#indices-and-ranges) 在访问数据序列或从集合中获取切片时是一个非常强大的构造。该特征由两部分组成，

**第一部分索引**

可用于从开头或结尾获取集合，如下例所示。

```
Index i1 = 3; // number 3 from beginning  
Index i2 = ^4; // number 4 from endint[] a = { 0, 1, 2, 3, 4, 5, 6, 7, 8, 9 };  
Console.WriteLine($"{a[i1]}, {a[i2]}"); // "3, 6"
```

**第二部分范围**

从集合中访问子集合(切片),如下例所示。

```
var slice = a[i1..i2]; // { 3, 4, 5 }
```

提供了一个很好的语法来访问集合中的数据序列。这是广为接受的，但一次又一次，没有什么是新的。很多类似的技术已经在其他语言中使用，比如 [Python](https://www.geeksforgeeks.org/python-range-function/) 。

# 零合并赋值

[零合并赋值](https://docs.microsoft.com/en-us/dotnet/csharp/whats-new/csharp-8#null-coalescing-assignment)简化了一个常见的编码模式，如果一个变量为空，就给它赋值。

常见的代码形式有:

```
**if** (variable == **null**)  
{  
  variable = expression; // C# 1..7  
}variable ??= expression; // C# 8
```

它提供了一个很好的语法，但只会为您节省几行代码，没什么特别的。

# 替代内插逐字字符串

[与$@"hello "(当前内插逐字字符串)相比，备选内插逐字字符串](https://docs.microsoft.com/en-us/dotnet/csharp/whats-new/csharp-8#enhancement-of-interpolated-verbatim-strings)将扩展对象初始化器以允许@$"hello "作为逐字内插字符串。

```
var file = $@"c:\temp\{filename}"; // C# 7  
var file = @$"c:\temp\{filename}"; // C# 8
```

我会认为这是一个固定的错误，而不是一个新的功能，这就是为什么我不会进一步讨论它。

# 使用声明

[使用声明](https://docs.microsoft.com/en-us/dotnet/csharp/whats-new/csharp-8#using-declarations)增强了‘Using’操作符对模式的使用，使其更加自然。

```
// C# Old Style  
using (var repository = new Repository())    
{    
} // repository is disposed here!

// vs.C# 8       
using var repository = new Repository();    
Console.WriteLine(repository.First());    
// repository is disposed here!
```

您需要小心使用这个特性，否则当您仍然需要它时，您的对象将被处理掉。

# 一次性引用结构

[可处理的引用结构](https://docs.microsoft.com/en-us/dotnet/csharp/whats-new/csharp-8#disposable-ref-structs)允许你使用带有‘using’模式的引用结构/只读引用结构。

对 ref 结构使用基于模式的

```
ref struct Test {  
   public void Dispose() { ... }  
}  
using var local = new Test();  
// local is disposed here!
```

# 静态局部函数

[静态局部函数](https://docs.microsoft.com/en-us/dotnet/csharp/whats-new/csharp-8#static-local-functions)允许你给局部函数添加“静态”修饰符。

```
int AddFiveAndSeven()  
{  
  int y = 5; 
  int x = 7;  
  return Add(x, y); static int Add(int left, int right) => left + right;  
}
```

此功能将帮助您修复警告，使其不会从封闭范围中捕获对任何变量的引用。

# 非托管构造类型

在 C# 7.3 和更早版本中，构造类型(至少包含一种参数类型的类型)不能是非托管类型。从 C# 8.0 开始，如果构造值类型只包含非托管类型的字段，则该类型是非托管的。

```
Public **struct** Foo<T>   
{   
 **public** T Var1;   
 **public** T Var2;   
}
```

Bar <int>类型在 C# 8.0 中是一个非托管类型。与任何非托管类型一样，您可以创建一个指向该类型变量的指针，或者在堆栈上为该类型的实例分配一块内存:</int>

```
// Pointervar foo = **new** Foo <**int**> { Var1 = 0, Var2 = 0 }, 
var bar = &foo; // C# 8 // Block of memory 
Span< Foo<**int**>> bars = **stackalloc**[] 
{ 
 **new** Foo <**int**> { Var1 = 0, Var2 = 0 }, 
 **new** Foo <**int**> { Var1 = 0, Var2 = 0 } 
};
```

该功能是一种性能增强。如果构造值类型只包含非托管类型的字段，则它们现在是非托管的。这个特性意味着你可以在堆栈上分配实例。阅读下面的[链接](https://docs.microsoft.com/en-us/dotnet/csharp/whats-new/csharp-8#unmanaged-constructed-types)。

# 只读成员

新的[只读成员](https://docs.microsoft.com/en-us/dotnet/csharp/whats-new/csharp-8#readonly-members)允许你将只读修饰符应用于结构的任何成员。它表示该成员不修改状态。

```
**public** **struct** XValue 
{ 
 **private** **int** X { **get**; **set**; }  **public** **readonly** **int** IncreaseX() 
 { 
 // This will not compile: C# 8 
 // X = X + 1; 
 var newX = X + 1; // OK 
 **return** newX; 
 } 
}
```

此功能允许您指定您的设计意图，以便编译器可以实施它，并基于该意图进行优化。

# 摘要

C# 8 有许多有用的新特性，在社区中被广泛接受。不幸的是，创新仍然很低，没有达到许多开发人员的期望，就像在 C#中一劳永逸地融入特征，只是在这里和那里有一些小的变化，没有创造出真正大的功能。我们已经看到了 C# 8 提出的特性及其优缺点的概述，我希望这将有助于您在未来更好地使用它们。