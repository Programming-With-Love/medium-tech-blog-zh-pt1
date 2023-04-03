# 如何剥一只猫的皮:FizzBuzz 五种语言

> 原文：<https://medium.com/quick-code/how-to-skin-a-cat-fizzbuzz-5-ways-dc4ec811bfcb?source=collection_archive---------4----------------------->

![](img/7c24513e526ccfaad39f9d1f7cd75e80.png)

SKINAWHUTNOW?

**—更新** : *由于评论中一些有用的建议，我已经修改了我发表的这篇文章的原始版本，所以如果你第二次看到这篇文章，如果它读起来略有不同，不要感到惊讶。这也是我把名字从“FizzBuzz 5 种方式”改成“FizzBuzz 五种语言”的原因，因为这里的代码示例比我之前的要多得多。还有，更多猫的笑话和引用，就这样了。*

FizzBuzz 是我们许多人面临的第一年计算问题。规则非常简单:给定一个整数，如果该整数是 3 的倍数，则返回“Fizz”，如果该整数是 5 的倍数，则返回“Buzz”，如果是 3 和 5 的倍数，则返回“FizzBuzz”，如果不是两者的倍数，则返回一个空字符串。大多数人在这种情况下的做法是拿出一个简单的`if/else`条件，但老实说，这是小孩子的东西。为了增加难度，今天，我们将尝试使用尽可能少的条件语句来完成这个看似简单的任务。让事情变得更加困难的是，我们将用五种非常不同的语言来做这件事(这就是这篇文章的名字的由来…“剥一只猫的皮的方法不止一种…明白了吗？)

> 埃迪，你是个疯子，那是胡说八道！
> ~你。

我们走着瞧。

# **Javascript**

我们将开始使用的第一种语言是大多数初学者都熟悉的语言:Javascript。如今，Javascript 在软件开发领域无处不在，以至于如果有人说“我是一名程序员”，他们很可能写的是 JS 而不是其他语言。过去被“真正的程序员”嘲笑为“脚本语言”的流行程度的激增可以归因于 node.js 及其 npm 库生态系统的出现。如今，你很难找到一种不能用 Javascript 编写的程序。虽然在过去，JS 仅限于制作低劣的动画和网页上的简单交互，但现在可以用来编写整个 UI 框架、网络服务器、实时聊天应用程序，甚至自动化和/或机器人控制器，如用 [Raspberry pi](https://www.raspberrypi.org/) 创建的那些。

在你说任何事情之前，我会故意省略类型检查和错误处理，以防你们中的任何人在学校里认为这篇文章是作弊的好方法。

用 JS 实现 FizzBuzz 的最基本代码是这样的:

Simple, right?

先来几个解释:
1。如果你刚刚开始，你可能想知道那个`%`操作符在做什么……那实际上并不意味着“百分比”。`%`是模数运算符，模数是我们将在本文中大量使用的函数。模数是一个函数，用于计算一个数除以另一个数的余数。在这种情况下，我们希望检查数字是否能被 3、5 或两者整除，因此我们检查数字 mod 3 或 5 是否等于零。

2.不，你没有看到三，那些三等于号是我们在 JS 中检查等价性的方法。大多数语言使用双等号检查等价性，使用单等号进行变量赋值(例如:`let x = "foo"`)。在 JS 中，double-equals 也被使用，但不像`==`操作符那样允许类型转换；换句话说，一个字符串值`'1'`将被评估为等同于一个数字值`1`。如果您要将标签的 innerHTML 与脚本中的数字进行比较，这是很有用的，但通常是不明智的。`===`操作符是一个严格的等价比较，意味着`'1' === 1`的计算结果是`false`。

3.第三个条件分支中的双&符号(`&&`)是一个逻辑符号`AND`，但是在本文的剩余部分我们不会再使用它，所以这将是我最后一次提到它。

这段代码会运行得相当好，并做它应该做的事情。但是，也可以这样写:

Fun fact… the guy who introduced me to Immediately Invoked Function Expressions (IIFEs) refers to the parens at the end as ‘donkey balls’. Note how the whole thing is wrapped in parens, then is followed by two empty parens… this makes the JS interpreter view the whole thing as a function that’s immediately executed when loaded, as the name implies.

在这个代码片段中，代码看起来更加简洁。不幸的是，因为我们需要实例化 Object 两次，所以它的运行速度比第一个例子慢 82%。如果它很慢，你可能会问，我为什么要给你看呢？好吧，性能不是这次演示的重点，但既然你问了，考虑一下来自[的这个测试](https://jsperf.com/switch-case-vs-if-else/13)的结果，在这个测试中，我比较了使用对象映射与 if/else、case/switch 的结果(我使用的是 Chrome——在 Chrome、Edge 和 Opera 之间，它们都使用‘Blink’渲染引擎，你在浏览器市场中占有压倒性的份额——处理对象映射比条件慢的浏览器(Firefox、Safari 和 IE)加起来只占市场的四分之一多一点，相比之下)。在您需要比较静态结果的情况下，表格发生了变化，对象映射现在比 if/else 快 80%,甚至比 switch/case 还要快。为什么要翻转？在这种情况下，if/else 是每次运行操作时都需要运行比较的对象，而该对象被用作二叉查找树…它只取和并在其结构中查找键。这就是为什么有时利用这种类型的结构比迭代加载条件要快得多。

**—编辑:** *当我写这篇文章的时候，我知道一定有更好的方法，评论者 Paul B .好心地指出有一个更简单、更优雅的解决方案——字符串插值:*

Yes, it’s different than Paul B.’s example in the comments. This is because, while he was quick to point out that most versions of FizzBuzz ask you to return the number instead of Fizz, Buzz or FizzBuzz if the number is neither a multiple of 3 or 5, 1) I’m asking for an empty string, not the number, to set mine apart from most coding tests that use FizzBuzz, and 2) His solution didn’t quite check out, for one reason… He’s using type coercion under the hood to take any number returned from the modulus to be ‘true’ and zero returned by the modulus to eval as ‘false’, which is why his returns are flipped in his example. If you fail to give a param in his version, (or give it a string) the modulus statements will return ‘undefined’… which JS will eval to ‘false’. This means that if you give no param or an incorrect param for the function, it will still give you ‘FizzBuzz’, rather than an empty string, (or, in his case, ‘FizzBuzz, instead of the param itself). That’s me breaking my own rules there, as I said I wasn’t including error checking, again to set it apart from coding tests, but I thought it was worth mentioning. Still, string interpolation was a REALLY good suggestion, as you can see by the js-perf results here: [https://jsperf.com/fizzbuzz-if-else-vs-object2](https://jsperf.com/fizzbuzz-if-else-vs-object2)

在这个例子中，我们使用一个字符串文字将模数计算的结果直接插入到字符串中，从而将运算精简到最基本的程度，并完全去掉对象。该功能是作为 ES2015 的一部分引入的，同时还有我在示例中使用的“胖箭头”功能符号。的确，它仍然依赖于条件，这违背了本文的初衷，但是，这比对象映射方法和 if/else 都要快，也更简洁。

看不出有条件吗？靠近一点看…

你能发现它吗？是这样的:

```
num % 3 === 0 ? 'Fizz' : '';
```

那是一个**三元运算符**。大多数现代语言都支持它们。这里发生的事情是，如果问号之前的条件评估为' true ',我们将返回' Fizz ',否则返回一个空字符串。这个问题是`if`的简写，冒号代表`else`。这就是商业界所谓的“语法糖”。

# **Python**

回到键/值对方法，让我们在 Python 中尝试一下:

Ooh, that’s short and sweet!

![](img/e706bb5d0b6d1a1e91f53e63a2c29438.png)

Python is kinda fun. (Image courtesy of [https://www.xkcd.com/353/](https://www.xkcd.com/353/))

在 Python 中，我们看到我们可以再次使用 reduce 函数，许多部分几乎可以从我们之前看到的 JS 版本中识别出来，尽管语法已经发生了很大的变化。这里，我们使用 lambda 列表理解将键简化为一个字符串。此外，在 Python 中，空格很重要…这就是为什么我使用四个空格来缩进我的代码，而不是两个，就像在我的其余示例中一样——在许多其他语言中，空格是一个好东西，但把它搞砸了，这没什么大不了的。在 Python 中却不是这样，如果你的格式稍有偏差，就会给你的代码带来大问题。(正如评论者 nleamba 指出的，在 Python 中，技术上可以使用两个空格而不是四个，但是他们自己的 [API 文档](https://www.python.org/dev/peps/pep-0008/#indentation)推荐的间距是四个。)

我的 Python 例子比 JS 版本的代码少，但是我不能不使用那个`if`语句，即使它是在一个 guard 子句中，而不是在一个标准的条件语句中。还要注意在我们的模数检查器中用于确定等价性的双等号——`===`在 Python 中并不存在，但这是规范，而 JS 实际上是个例外，正如我们将在其余的例子中看到的……

Paul B 的建议让我开始思考…我能不能使用字符串插值从我的代码高尔夫游戏中去掉一些笔画，以用于我的其余示例？从 Python 3.6 开始，我们可以通过`f`函数做到。然而，Python 的三元组被定义为一个保护子句，而不是使用我们在 JS 中看到的`x ? y :z`格式，这意味着我们依靠 if/else 语句:

Whoa! One line? NICE

Python 确实给了我们一个简化的三元组，我们可以用它来避免 if/else:

Shorter, but kinda hard to tell what’s going on in this version.

…然而，大多数 pythony 爱好者并不推荐速记版本，因为它不像大多数人希望的那样“pythony”。简而言之，我们使用元组(一种用于定义变量对的 Python 数据类型)来定义结果，并将条件放在返回值后面的括号中。这有点像薛定谔的猫……元组是你的回归，以真假并存的状态存在，直到被背后的条件性观察到。

![](img/5d967a3405326f79c9ed873becb6c86b.png)

Hooray! Sciency cat joke!

首先定义三元速记元组中的“false”返回，这有点令人困惑，因为大多数人倾向于按照“true 或 false”而不是“false 或 true”来思考。此外，简写版本对元组中的两个值运行评估，这意味着无论如何条件都将运行两次。if/else 结构的三元组遵循标准的 if/else 逻辑，不会遇到同样的问题。(更多详情，请参见此处的

# C++

还记得在我们的第一个 Python 例子中，我们是如何导入 reduce 语句的吗？这让我想起了 C++……事实上，让我们考虑一下 c++:

AAAAAAAAAAIIIIIIIIIIIIGGGGGGGGHHHHHHH!!!!!!

胡说八道，太多代码了！

![](img/759a14e5dd6cf8ac56619b744cb57113.png)

Wouldn’t it be more thematic to say, ‘HOLY CATS!’?

当然，我不得不添加一个带有操作的入口点(`int main()`)来接受输入和输出结果，(`cin`和`cout`)，因为 C++是一种编译语言。JS 和 Python 是解释型语言，可以在运行时调用它们的函数，而无需任何准备，但是因为我需要能够将 C++代码作为 shell 脚本运行，所以要么这样做，要么以从命令行获取 number param 的方式编写脚本(对不起，我只是不喜欢这样做，如果不这样做的话，已经很长时间了)。您会注意到，尽管语法看起来类似于 JS，用括号将参数和方法括起来，但我们需要在 C++中做些别的事情，那就是定义类型，不仅为我们使用的每个变量，而且为每个方法的返回值。我们也没有找到定义键/值对列表的最简单的方法，这里称为`map`。在 C++中，一个`vector`代替了一个数组，我们需要在定义它的时候设置向量的最大大小。这是因为 C++迫使你更加关注你的程序所使用的内存。这也是我们需要在文件顶部包含程序中要使用的每种数据类型的库的原因，因为 C++只包含编译所需的最少的库。我们还声明了一个名称空间:`std`。这是因为我们包含的所有库都来自“标准”库，如果我们不声明名称空间，我们最终会在使用这些类型的变量的每个声明前写出`std.`。如果我们愿意，我们可能已经用 C++写了同样的程序，使用了和我们第一个 JS 例子中相同的算法，它看起来会非常相似，但是仍然需要更多的设置和代码。JS 和 Python 都是动态类型的，这意味着它们从赋给变量的数据中推断出变量的类型。另一方面，如果你试图将一个字符串赋给一个已经定义为 int 的变量，C++会抛出一个错误。最后一点要注意的是，尽管我很努力，我还是无法摆脱至少一个条件语句，即我在 JS 部分提到的三元运算符。

但是 C++能插值吗，兄弟？

简答？号码

然而，我不打算让这阻止我想出比我最初写的 Lovecraftian 庞然大物更好的东西，所以我做了一些挖掘。你可以添加一些库到 C++中，比如 [Boost](https://www.boost.org/) ，它包含了字符串格式，但是据我所知，包含 Boost 的库会降低你的编译速度，我不想这样。我宁愿想出一种使用标准库的方法，最终，我找到了一种方法…

Still YUGE by comparison with the others, but way better than the original, right?

在这个例子中，我包含了`sstream`头，它允许我们使用一个基本的 stringstream 来构建我们想要返回的字符串。这样做，我们可以减少大约一半的代码行。仍然没有我们的其他语言漂亮，但嘿，它比以前好。

好了，现在关于 C++已经说得够多了，我需要一个味觉清洁剂。让我们继续讨论 Clojure。

# Clojure

Clojure 是一种函数式语言，是可以在 JVM 中运行的 Common Lisp 的一种方言，并且可以利用 Java 库。我们现在不会涉及任何 Java 的东西，因为在这个例子中我们真的不需要它。不过，请做好准备，尽管 Clojure 为我们提供了这个列表中最短的例子，也是看起来最奇怪的例子之一:

WHOA.

是的，除去函数名，整个方法只有两行代码，但是到底发生了什么？
第一件事是整个事情被包装在一组括号中，(Clojure 读作‘closure’，毕竟……)，我们的函数声明以一个`defn`语句开始。还要注意，我们将要输入的参数(`num`)是用括号括起来的，而不是括号。您应该注意的另一件事是我们的`tbl` var 是用一个`def`语句定义的，没有冒号将键和值分开。那些价值观呢？在 Clojure 中，我们不是使用`%`操作符，而是使用`mod`函数来获取模数，它出现在两个参数之前……不是写`num % 3 == 0`，而是写`mod num 3`，这将导致 0。为什么我们的做法与其他例子不同？嗯，在我们用来过滤值的 list comprehension 中，你会看到我们正在使用`comp`(比较)函数将值与零进行比较。如果你仔细看，这个函数会让你想起 Python 例子的最后一行……语法可能不同，但是这一行的结构非常相似。

为了与我的其他修订保持一致，这里有一个 Clojure 中的示例，它只使用一行代码来构建一个字符串:

Neat, huh?

在这里，我们利用 Clojures 的`str`函数来构建一个字符串，以及`when`条件，只有满足条件时才会返回值。

# 红宝石

我最后的例子来自 Ruby。Ruby 让我微笑，因为它让我们可以做一些真正聪明的事情。看看你是否能发现它。

See it yet?

嘿，你可能会说，这看起来没什么特别的…它比这个方法的 C++版本都长，但在其他方面看起来和 JS 版本没什么不同…等等，什么？那个类定义在那里做什么？你的`num`情人呢？那叫什么`self`？你做了什么？？？！！！

![](img/cdd8e8b41fca60ce162c34b1a6d8e951.png)

I iz skurd.

Ruby 虽然也是一种解释型的动态类型语言，如 JS 和 Python，也是面向对象的，如 C++，但它给了我们一些在其他语言中无法轻易实现的自由。其中之一是能够重新打开任何类并向其定义中添加方法。在这个例子中，我已经重新打开了 Integer 类，并直接向它添加了`fizzbuzziness`方法。这允许直接在我们想要得到‘fizzbuzzity’的数字上调用方法，而不是用数字作为参数调用方法(例如:`5.fizzbuzziness`而不是`fizzbuzziness(5)`)。以上帝的名义，你为什么要重新打开一个语言的核心类并给它添加方法呢？那不是很危险吗？算是吧。你真的不应该经常这样做，因为你添加的方法可能会与后来添加到标准 Ruby 库中的方法发生冲突，这可能会导致你的应用程序出现大量的代码中断。还有一种可能是，您可能会覆盖类中的任何方法，最终得到完全无意义的东西，就像这样:

If you do, I don’t know you, you never got this from me.

正是因为这个原因，即使通过用 Javascript 修改一个类的原型也有可能实现类似的行为，但是他们在 API 文档中用大而全大写的字母告诉你，永远不要用标准的库类来做这件事。然而……在 Ruby 中，如果你有一个像我这样的不太可能被添加到标准库中的方法，它也会非常有用，因为它有助于你的应用程序以一种更符合 Ruby 编程习惯的方式坚持“[告诉，不要问](https://www.martinfowler.com/bliki/TellDontAsk.html)”的原则。

我们也可以用字符串插值和三元运算符来实现，就像这样:

I love Ruby, so much.

所以，哦，我的天啊，我们到了文章的结尾。不同的编程语言，不同的方法，相同的结果。一个第一年的编码问题，在它生命的一英寸之内用肉嫩化剂敲打，并在显微镜下检查。您可能不像我一样喜欢钻研不同的语言来比较它们是如何工作的，但是我希望您在阅读这篇文章时和我在研究和撰写这篇文章时一样开心。下次见！