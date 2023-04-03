# 通过单元测试学习 Kotlin

> 原文：<https://medium.com/androiddevelopers/learn-kotlin-through-unit-tests-914106d2d8c5?source=collection_archive---------3----------------------->

![](img/094e17b2de29db977ffd6d8a98125ead.png)

在 2019 年的 I/O 大会上，我们[宣布【Android 将以 Kotlin 为先。然而，一些开发者提到他们仍然不确定如何进行。开始编写 Kotlin 代码是令人害怕的第一步，尤其是如果团队中没有人熟悉它的话。](https://android-developers.googleblog.com/2019/05/google-io-2019-empowering-developers-to-build-experiences-on-Android-Play.html)

我们这些 Android Studio profilers 团队的成员是渐进的。第一步是要求我们所有的单元测试都用 Kotlin 编写。因为这些类是从产品代码中分离出来的，所以我们最初犯的任何错误都会被包含在内。

在此期间，我通过收集我们团队在几次代码评审中观察到的常见问题，整理出了以下指南。我在下面复制它，希望它能帮助更广泛的 Android 社区中的其他人。

*注意:这篇文章是针对科特林初学者的。如果你和你的团队已经在有效地编写 Kotlin 代码，这篇文章可能不会为你提供太多新的信息。然而，如果你读了它，并认为你会包括一些我没有的东西，考虑留下评论吧！*

# IDE 操作:将 Java 文件转换为 Kotlin 文件

如果你正在使用 Android Studio，开始学习 Kotlin 最简单的方法是用 Java 编写你的测试类，然后通过从菜单栏中选择`Code → Convert Java File to Kotlin File`将其转换成 Kotlin。

此操作可能会询问您*"在执行此转换后，项目剩余部分中的一些代码可能需要更正。是否也要找到这样的代码并纠正它？”我建议选择“*否*”，这样你就可以一次只关注一个文件。*

虽然这个动作会生成 Kotlin 代码，但是产生的代码通常还有改进的空间。接下来的部分强调了我们从几十个自动生成代码的代码评审中收集到的常见技巧。当然，Kotlin 语言远不止下面讨论的内容，但是为了不离题，本指南只关注我们观察到的重复出现的问题。

# 高级语言比较

Java 和 Kotlin 在高层次上看起来非常相似。下面是一个先用 Java，再用 Kotlin 编写的框架测试类。

```
/// Java
**public** class ExampleTest {
  @Test
  **public** **void** testMethod1() **throws Exception** {} @Test
  **public** **void** testMethod2() **throws Exception** {}
}/// Kotlin
class ExampleTest {
  @Test
  fun testMethod1() {} @Test
  fun testMethod2() {}
}
```

值得注意的是 Kotlin 中的精简内容:

*   默认情况下，方法和类是公共的。
*   `void`返回类型不需要显式声明。
*   没有已检查的异常。

# 分号是可选的

这是一开始可能会感觉很不舒服的变化之一。实际操作中，不需要太担心。只需编写您的代码，如果您出于习惯使用分号，代码仍会编译，IDE 会指出它们。提交前全部删除即可。

不管你喜不喜欢这个选择，Java 已经在某些地方去掉了分号，如果你对比 C++(更多地方需要分号)，你会注意到这一点。

```
/// C++
std::thread([this] { this->DoThreadWork()**;** })**;**/// Java
new Thread(() -> doThreadWork())**;**/// Kotlin
Thread { doThreadWork() }
```

# 类型在末尾声明

```
/// Java
**int** value = 10;
**Entry** add(**String** name, **String** description)/// Kotlin
var value: **Int** = 10
fun add(name: **String**, description: **String**): **Entry**
```

就像可选的分号一样，如果您不习惯，这种变化可能会很难接受。这与许多人在编程生涯中根深蒂固的顺序完全相反。

然而，这种语法的优点是，当可以推断类型时，可以更容易地省略它们。这将在后面的章节*省略变量类型*中进一步讨论。

这种语法也更强调变量本身，而不是它的类型。我发现大声谈论代码时，这个顺序听起来更自然:

```
/// Java
int result;     **// An integer that is a variable called "result".**/// Kotlin
var result: Int **// A variable called "result" that is an integer.**
```

关于这种语法，我要说的最后一点是，尽管开始使用起来很别扭，但随着时间的推移，你会习惯它的。

# 不带“new”的构造函数

Kotlin 在构造函数调用之前不需要 *new* 关键字。

```
/// Java
... = **new** SomeClass();/// Kotlin
... = SomeClass()
```

乍一看，这可能感觉像是丢失了重要的信息，但实际上不是 Java 中的许多函数在幕后分配内存，而您从来不在乎。许多库甚至有静态创建方法，如下所示:

```
/// Java
Lists.newArrayList();
```

所以，真的，科特林只是让这更一致。任何函数都可能分配内存，也可能不分配内存。

这也清理了你分配一个临时类只是为了调用它的一个函数而没有把它分配给任何东西的模式。

```
/// Java
**new** Thread(...).start(); // Awkward but valid/// Kotlin
Thread(...).start()
```

# 可变性和不变性

默认情况下，Java 中的变量是可变的，需要使用`final`关键字使它们不可变。相比之下，Kotlin 没有`final`关键字。相反，您需要用`val`来标记属性，以指示一个*值*(不可变的)或`var`来指示一个*变量*(可变的)。

```
/// Java
private **final** Project project; // Cannot reassign after init
private Module activeModule;   // Can reassign after init/// Kotlin
private **val** project: Project     // Cannot reassign after init
private **var** activeModule: Module // Can reassign after init
```

通常在 Java 中，你会遇到许多本可以是`final`的字段，但是这个关键字被省略了(可能是偶然的，因为它很容易被忘记)。在 Kotlin 中，您必须在您声明的每个字段中明确说明这个决定。如果你不确定它应该是什么，就默认标记为`val`，以后如果需求发生变化，就改为`var`。

顺便说一下，在 Java 中，函数参数总是可变的，并且像字段一样，可以通过使用`final`使其不可变。在 Kotlin 中，函数参数总是不可变的——也就是说，它们被隐式标记为`val`。

```
/// Java
public void log(**final** String message) { … }/// Kotlin
fun log(message: String) { … } // "message" is immutable
```

# 可空性

Kotlin 废弃了`@NotNull`和`@Nullable`注释。如果一个值可以是 null，你只需要用一个问号来声明它的类型。

```
/// Java
[**@Nullable**](http://twitter.com/Nullable) **Project** project;
[**@NotNull**](http://twitter.com/NotNull) **String** title;/// Kotlin
val project: **Project?**
val title: **String**
```

在某些情况下，如果你确定一个*可空的*值将总是*非空的*，你可以使用`!!`操作符来断言。

```
/// Kotlin
// 'parse' could return null, but this test case always works
val result = parse("123")**!!** // The following line is not necessary. !! already asserts.
❌ assertThat(result).isNotNull()
```

如果不正确地应用`!!`，会导致代码抛出一个`NullPointerException`。在单元测试中，这只会导致测试失败，但是在产品代码中使用时应该格外小心。事实上，许多人认为生产代码中的`!!`是一种潜在的代码味道(尽管这里有足够的细微差别，这一点可能需要自己的博客文章)。

在单元测试中，使用`!!`操作符来断言一个特定的案例是有效的更容易被接受，因为如果这个假设不再成立，测试就会失败，而你可以修复它。

如果您确定使用`!!`操作符是有意义的，那么您应该尽快应用它。例如，执行以下操作:

```
/// Kotlin
**val result = parse("...")!!**
result.doSomething()
result.doSomethingElse()
```

但是*不要*这样做:

```
/// Kotlin (auto-generated)
val result = parse("...")
❌ result**!!**.doSomething()
❌ result**!!**.doSomethingElse()
```

# 省略变量类型

在 Java 中，你会看到很多这样的代码:

```
/// Java
**SomeClass** instance1 = new **SomeClass**();
**SomeGeneric<List<String>>** instance2 = new **SomeGeneric<>**();
```

在 Kotlin 中，这些类型声明被认为是多余的，不需要写两次:

```
/// Kotlin
val instance1 = **SomeClass**()
val instance2 = **SomeGeneric<List<String>>**()
```

“但是等等！”你大声说，“有时我打算声明那些类型！”例如:

```
/// Java
**BaseClass** instance = new **ChildClass**(); // e.g. List = new ArrayList
```

这可以在 Kotlin 中使用以下语法来完成:

```
/// Kotlin
val instance: **BaseClass** = **ChildClass**()
```

# 没有已检查的异常

与 Java 不同，Kotlin 不要求它的方法声明它们抛出什么异常。*检查的*异常和*运行时*异常之间不再有区别。

```
/// Java
public void readFile() **throws IOException** { … }/// Kotlin
fun readFile() { … }
```

然而，为了让 Java 能够调用 Kotlin 代码，Kotlin 支持使用`[@Throws](http://twitter.com/Throws)`注释间接声明异常。`Java → Kotlin`动作为了安全起见，总是包含这个信息。

```
/// Kotlin (auto-converted from Java)
[**@Throws**](http://twitter.com/Throws)**(Exception::class)**
fun testSomethingImportant() { … }
```

但是你永远不必担心你的单元测试被 Java 类调用。因此，您可以保存一些行，并安全地删除这些有干扰的异常声明:

```
/// Kotlin
fun testSomethingImportant() { … }
```

# 在 lambda 调用中省略括号

在 Kotlin 中，如果你想给一个变量分配一个闭包，你需要显式声明它的类型:

```
val sumFunc: (Int, Int) -> Int = **{ x, y -> x + y }**
```

然而，如果一切都可以推断，这可以缩短为只是

```
**{ x, y -> x + y }**
```

举个例子，

```
val intList = listOf(1, 2, 3, 4, 5, 6)
val sum = intList.fold(0, **{ x, y -> x + y }**)
```

注意，在 Kotlin 中，如果函数的最后一个参数是 lambda 调用，那么可以在括号外写闭包。上述内容与以下内容相同:

```
val sum = intList.fold(0) **{ x, y -> x + y }**
```

然而，仅仅因为你能，并不意味着你应该。有人可能会说上面的盖牌听起来很奇怪。其他时候，这种语法可以减少一些视觉干扰，特别是当方法的唯一参数是闭包时。假设我们想计数偶数。比较以下内容:

```
intList.filter({ x -> x % 2 == 0 }).count()
```

随着

```
intList.filter { x -> x % 2 == 0 }.count()
```

或者比较:

```
Thread({ doThreadWork() })
```

随着

```
Thread { doThreadWork() }
```

不管你认为什么是最好的方法，你都会在 Kotlin 代码中看到这种语法，它可以由`Java → Kotlin`动作自动生成，所以你应该确保在看到它的时候明白是怎么回事。

# 等于、==、和===

Kotlin 在相等测试上偏离了 Java。

在 Java 中，double-equals ( `==`)用于实例比较，这与`equals`方法不同。虽然这在理论上听起来不错，但在实践中，开发人员很容易在打算使用`equals`的时候不小心使用了`==`。这可能会引入微妙的错误，并需要几个小时来发现或调试。

在 Kotlin 中，`==`本质上和 equals 是一样的——唯一的区别是它也正确地处理了 null 情况。例如，`null == x`计算正确，而`null.equals(x)`抛出 NPE。

如果需要在 Kotlin 中进行实例比较，可以使用 triple-equals ( `===`)来代替。这种语法更难被误用，也更容易被发现。

```
/// Java
Color first = new Color(255, 0, 255);
Color second = new Color(255, 0, 255);
assertThat(**first.equals(second)**).**isTrue**();
assertThat(**first == second)**.**isFalse();**/// Kotlin
val first = Color(255, 0, 255)
val second = Color(255, 0, 255)
assertThat(**first.equals(second)**).**isTrue**()
assertThat(**first == second**).**isTrue**()
assertThat(**first === second**).**isFalse**()
```

大多数时候你写 Kotlin 代码，你会想要使用`==`，因为对`===`的需求相对较少。作为预防措施，Java 到 Kotlin 的转换器总是将`==`转换为`===`。出于可读性和意图，您应该考虑尽可能恢复到`==`。例如，这在枚举比较中很常见。

```
/// Java
if (day == DayOfWeek.MONDAY) { … }/// Kotlin (auto-converted from Java)
❌ if (day === DayOfWeek.MONDAY) { … }/// Kotlin
if (day == DayOfWeek.MONDAY) { … }
```

# 删除字段前缀

在 Java 中，私有字段与公共 getter 和 setter 配对是很常见的，许多代码库在字段上附加了前缀，这是匈牙利符号[的遗迹](https://en.wikipedia.org/wiki/Hungarian_notation)。

```
/// Java
private String **myName**;
// or private String **mName**;
// or private String **_name**;
public String getName() { … }
public void setName(String name) { … }
```

这个前缀是一个有用的标记，只对类的实现可见，使得区分类的局部字段和传递给函数的参数变得更加容易。

在 Kotlin 中，字段和 getter/setter 合并成一个概念。

```
/// Kotlin
class User {
  **val id**: String   // represents field and getter
  **var name**: String // represents field, getter, and setter
}
```

然而，当您自动转换代码时，Java 前缀有时会被带走，曾经隐藏在类内部的细节可能会泄漏到它的公共接口中。

```
/// Kotlin (auto-converted from Java)
class User {
❌ val **myId**: String
❌ var **myName**: String
}
```

为了防止前缀泄露，建议养成去掉前缀以保持一致性的习惯。

没有前缀的字段可能会使偶尔使用 web 工具进行的代码审查变得有点难以阅读(例如，在一个太大的类中的一个太长的函数中)。然而，当您阅读 ide 中的代码时，通过语法突出显示，可以清楚地看出哪些值是字段，哪些是参数。移除前缀也可能鼓励围绕编写更集中的方法和类的更好的编码习惯。

# 结束语

希望这篇指南能帮助你开始学习 Kotlin。首先，您将从编写 Java 并将其转换为 Kotlin 开始，然后您将编写类似 Java 的 Kotlin，最后，用不了多久，您将像专业人员一样编写地道的 Kotlin 代码！

这篇文章只是触及了 Kotlin 的皮毛。它的目标是为那些没有很多时间，只需要快速启动和运行他们的第一个 Kotlin 测试的人提供一个最小的笔记集，同时仍然向他们介绍相当数量的语言基础。

然而，它可能不会涵盖你需要的一切。为此，请考虑官方文档:

**语言参考:**[**【https://kotlinlang.org/docs/reference/】**](https://kotlinlang.org/docs/reference/) **互动教程:**[**https://try.kotlinlang.org/**](https://try.kotlinlang.org/)

语言参考非常有用。它涵盖了 Kotlin 中的所有主题，但没有深入到让人不知所措的程度。

教程会给你一个练习使用语言的机会，它们还包括 koans(一系列简短的练习)来帮助确认你新获得的知识。

最后，请查看我们的官方代码实验室，了解对 Kotlin 的重构。它涵盖了这篇博文中介绍的主题，以及更多更多的内容。