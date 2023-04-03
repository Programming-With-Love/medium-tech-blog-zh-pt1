# 警惕不谨慎的“if/else”语句！

> 原文：<https://medium.com/globant/on-guard-against-if-else-statements-1070a1104454?source=collection_archive---------0----------------------->

将保护语句引入 JavaScript 的三个步骤，以获得更安全、更好的代码，并放弃“else”语句的常见用法。

![](img/b5aab37cd9ebffe2496e8aa175554494.png)

Photo by [Roméo A.](https://unsplash.com/@gronemo?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

Edsger W. Dijkstra 是图灵奖获得者和 ACM 研究员，他是“[](https://en.wikipedia.org/wiki/Structured_programming)*”短语的发明者，也是计算机编程成为科学学科的第一批推动者之一，他对编程理论做出了许多贡献。在这篇文章中，我们将回到他的一个非常老的想法——从 1975 年！—讨论如何停止使用`else`语句(至少在某些形式下),转而应用*保护语句*来实现更安全、更清晰的编程。我们将展示什么是受保护的语句，然后我们将看到`if`语句可能存在什么问题，最后我们将看到如何用受保护的语句解决我们的担忧。*

# *谨慎的声明*

*什么是*保护语句*，它们是如何工作的？基本上，一个受保护的语句是一个普通的语句，前面有一个布尔保护，一个二进制测试；该语句仅在其 guard 为 true 时执行。*

```
**condition* → *statement**
```

*应该很明显，你并不总是需要或者使用警卫；受保护语句仅在*选择结构*(大致相当于`if`或`switch`语句)和*重复结构*(类似于`while`语句)的上下文中相关。我们在这里不讨论后者，而是关注前者，正如我们将看到的，前者有助于更清晰、更安全的 JavaScript 代码。*

## *谨慎选择*

*让我们先按照 Dijkstra 最初的风格，看一个他定义的*选择控制结构*；我们稍后会用 JavaScript 做一个(几乎)等价的版本。我们将使用 Dijskstra 自己使用的符号，用`if`和`fi`分隔完整的结构，以及“▯”字符(是的，一个正方形块！)用于分隔语句。*

```
***if** *condition*₁ → *statement*₁
 ▯ *condition*₂ → *statement*₂
 ▯ *condition*₃ → *statement*₃
 **...
** ▯ *conditionₙ* → *statementₙ*
**fi***
```

*每个受保护的语句由一个保护(评估为真或假的条件)加上一个语句组成；只有当一个语句的 guard 为 true 时，该语句才能被执行。(为什么是“可以”而不是“应当”？请继续阅读！)如果两个或更多的守卫为真，那么我们将有不确定性，并且*任何*的守卫语句(但只有一个！)将被执行。我们在下面的代码中有一个简单的例子，它将变量 *m* 设置为两个变量 *x* 和 *y* 的最大值。*

```
***if** x ≥ y → m := x 
 ▯ y ≥ x → m := y
**fi***
```

*如果 *x* 大于 *y* ，则执行第一条语句。如果 *y* 大于，则执行第二条语句。有趣的是，如果 *x* 和 *y* 相等，那么这两条语句中的任何一条都会被执行——但是在任何情况下，程序都会(应该！)要正确！这种不确定性可能看起来很奇怪，但它实际上有助于更好地理解程序；显而易见，它是正确的，代码中有一个对称性，与最大值定义中的对称性相匹配；优雅！*

> *JavaScript 没有不确定性(尽管我们可以假装)，我们也不会真的为它费心。对我们来说重要的是，您可以重新排序所有受保护的语句，而代码仍然可以正常工作。*

*另外重要的一点:在这个`if...fi`结构中，你总是可以说出在什么条件下会执行任何特定的语句；在普通的 JavaScript `if`语句中，您必须自己解决它。以下面的代码为例:*

```
*if (*condition1*) {
   *statement1*
} else if (*condition2*) {
   *statement2*
} else {
   *statement3*
}*
```

*第二条语句是什么时候执行的？你必须在精神上否定回答这个问题的第一个条件；不琐碎。而第三种说法是什么时候达成的？在这种情况下，你必须否定前面的两个条件，把这些结果结合起来才能得到答案；用力点。*

## *错过守卫时中止*

*有一个重要的限制条件，你可能没有想到:如果没有一个守卫是真的，`if...fi`结构将中止执行！如果存在没有一个保护为真的可能性，则提供一个`skip`语句。(在 JavaScript 中，一个注释或一个空块就足够了。)您可以找到如下代码:请特别注意最后一个选项。*

```
*if *condition*₁ → *statement*₁
 ▯ *condition*₂ → *statement*₂
 ▯ *condition*₃ → *statement*₃
 ...
 ▯ *conditionₙ* → **skip**
fi*
```

> *用逻辑术语来说，这相当于说所有条件的析取应该是一个重言式:*条件*₁*∩**条件*₂*∩**条件*₃*∩*……*∩**conditionₙ*8801*真*。另外，请记住，受保护语句的顺序并不重要；`skip`可能出现在任何位置。*

*为什么会这样呢？关键是，开发人员在编写选择语句时，应该涵盖所有条件——包括那些不应该做任何事情的条件。添加一个简单的注释到“*这里什么也不做*”只需要一分钟，不会以合理的方式影响性能，并且帮助每个未来的程序员阅读和理解这段代码，所以这是一个有效的想法。我们已经看到了一种新风格的`if`语句；现在让我们看看我们现有的有什么问题。*

# *if/else 语句的问题*

*如果你在网上四处看看，你可能会找到几篇建议替代`if/else`语句的文章，例如[使用](https://jraleman.medium.com/ternary-operators-vs-if-else-statements-6c26f7d034f7)[三进制](https://www.thoughtco.com/javascript-by-example-use-of-the-ternary-operator-2037394) `?:` [运算符](https://www.positronx.io/javascript-ternary-operator/)，或者[使用](/@michellekwong2/switch-vs-if-else-1d458e7b0711) `switch` [语句](/the-daily-standup/javascript-switch-statement-vs-if-else-if-ee3111793fa0)，或者应用模式匹配库[如 Z](https://z-pattern-matching.github.io/) 或其他……但是在这篇文章中，我建议最好的解决方案是使用(方式！)回到名为*受保护语句*的模式，继续使用`if`语句，尽管是以一种特殊的、受限制的方式。在考虑如何解决它们之前，让我们先来看看几个有问题的情况。*

## *二元期权真的是二元的吗？*

*让我们先做一个快速测试:这段代码有什么问题？是的，我承认这个问题有点棘手，因为代码可能没有问题，但是…*

```
*if (gender==="male") { 
    ... // *do something for males*
}*
```

*看着代码，我可能会问自己:“*我们不为女性做任何事情是对的吗？也许开发商忘记了这些？开发人员可以通过添加如下的`else`来增强代码的可理解性——即使添加的代码只是一个注释，解释不需要为女性做什么——也许他会发现自己忘记了一些重要的情况！**

```
*if (gender==="male") { 
    ... // *do something for males*
} **else {
    ... // *do something for females***
}*
```

*这更好——我们知道开发人员不只是忘记了一个案例，而是他考虑到了…即使什么都不做，我们也知道他考虑了所有选项。或者他有吗？如果`gender`字段来自数据库，并且没有存储任何值，那么`gender`可能是`null`会发生什么？(或者，另一种选择:如果`gender`字段来自调查中的下拉问题，其中增加了一个额外的选项“*宁愿不说*”？)我们会正确处理这个案例吗？解决这个问题的另一种尝试是:*

```
*if (gender==="male") { 
    ... // *do something for males*
} else **if (gender !== null)** {
    ... // *do something for females*
}*
```

*但是，为了避免您对此解决方案过于满意，请考虑在代码的其他地方可能会出现这样的情况:*

```
*if (gender==="female") { 
    ... // *do another thing for females*
} else if (gender !== null) {
    ... // *do another thing for males*
}*
```

*`else`选项太普通了，我们有麻烦了——如果`gender`缺失，我们甚至不能在代码的不同部分一致地处理它！我们最初的二元选择(男性/非男性或男性/女性)实际上有多个选项，我们的代码根本不够好。*

## *复杂条件是不是太简单了？*

*让我们考虑一个更复杂的情况。你觉得这个代码怎么样？*

```
*if (citizen==="yes" && age>21) { 
    // *do something for adult citizens*
} else {
    // *do something else for not adult citizens* 
}*
```

*好的，这和前面的问题一样；也许有些人属于`else`部分，不应该。你可以试着避免像这样的新版本的一些问题。*

```
*if (citizen==="yes" && age>21) { 
    // *do something for adult citizens*
} else if (citizen==="no" || age<=21) {
    // *do something else for not adult citizens*
}*
```

*这可能更容易理解——至少我不用在脑子里否定一个布尔表达式！—但这真的没有解决任何问题，甚至增加了错误逻辑的可能性；如果`age<=21`被写成`age<21`会发生什么？没错，正好 21 岁的人会“从缝隙中掉出来”，在任何地方都不会被处理！更糟的是，我们无法轻易侦破此案。同样，`else`部分太普通了，当它应该被执行时并没有真正检查好。换句话说，我们最初有一个复杂的条件，而`else`部分过于普通，过于简单，包括了不应该出现的情况。*

*JavaScript 中的测试总是二进制的(对/错)，但是现实生活中经常提供更多的可能性，二进制思维的`if`只是一个等待中的 bug。还有，要尽量具体一点:比如`gender==="female"`而不是笼统的`gender!=="male"`。所以，前面例子中的问题基本上是我们没有仔细检查在什么条件下做具体操作。我们不能就这样写`if`语句；我们需要一种更安全的方式来做到这一点。让我们把时钟拨回到半个世纪前，看看那些谨慎的声明。*

# *JavaScript 中保护选择的三个步骤*

*我们现在已经看到了谨慎语句和常见的 if 语句；我们如何将前者应用于更好、更安全的 JavaScript？只有三条规则可以遵循:*

*   *所有的`if`语句都必须以`else`结束，如果前面的守卫都不满足，T5 就会抛出异常。*

```
*if (...) {
    ... 
} **else throw new Error();***
```

*   *否则你不能使用普通的`else`;应该总是`else if`跟在另一个后卫后面。*
*   *如果你需要一个`skip`陈述，使用一个注释来给出更多关于为什么什么都不需要的解释。*

*考虑到这一点，我们可以用一种更安全的方式重做本文开头的例子。对于男性和女性，第一种情况是:*

```
*if (gender==="male") { 
    ... // *do something for males*
} else if (gender==="female") {
    ... // *do something for females*
} else throw new Error();*
```

*对于成年公民来说，第二种情况是:*

```
*if (citizen==="yes" && age>21) { 
    // *do something for adult citizens*
} else if (citizen==="no" || age<=21) {
    // *do something else for not adult citizens*
} else throw new Error();*
```

*请注意，这里的所有问题现在都已修复——或者更准确地说，至少它们是可以检测到的。如果`gender`不是“男性”或“女性”，将会抛出一个错误，开发人员将会意识到某些假设是错误的，并修正代码。在第二种情况下，如果`citizen`有另一个值，而不是“是”或“否”，或者如果程序员弄错了，写错了`age<21`，每当出现意外情况时，程序就会停止，而不是盲目前进，产生错误的结果。*

# *摘要*

*在本文中，我们已经讨论了一些由`if`语句引起的未检测到的错误情况的问题，并且我们已经看到了如何应用*保护语句*将至少允许我们识别问题，而不允许程序以一种意想不到的方式前进。*

*跟随 E. W. Dijkstra 的脚步，这篇文章可以被称为“ *If/else 语句被认为是有害的*”——但是我不想走那么远；毕竟，`if`语句不是问题所在；懒惰和忽略必要的测试是罪魁祸首。*

*以这种方式写声明只是增加了一点冗长，但我的感觉是额外的安全或警告是值得的；你怎么想呢?*

# *参考资料和进一步阅读*

*全部出自 Dijkstra:他的 1975 年 EWD 472 注原文可以[在这里看到](https://www.cs.utexas.edu/users/EWD/ewd04xx/EWD472.PDF)，一个转录[在这里](https://www.cs.utexas.edu/users/EWD/transcriptions/EWD04xx/EWD472.html)，他的 CACM(ACM 的通信)*守护命令、不确定性和程序的形式推导*文章[在这里](http://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.90.97&rep=rep1&type=pdf)。此外，他 1972 年的 ACM 图灵讲座“ [*《卑微的程序员*](https://www.cs.utexas.edu/~EWD/transcriptions/EWD03xx/EWD340.html) ”,不应该错过——如果你能得到它，他 1976 年的书“*《编程的一门学科》*”也值得一读。*

*![](img/a897b0aaee4d28895e6511013dc9699f.png)*

*Edsger Dijkstra*

*最后，不要错过他的 EWD 215 注，“ [*一个反对 GO TO 语句*](https://www.cs.utexas.edu/users/EWD/ewd02xx/EWD215.PDF) 的案例”，这是在 1968 年 3 月作为一封信发表的，标题为《【the GO TO 语句被认为有害》。在这封信中，Dijkstra 批评了“ [*意大利面条式代码*](https://en.wikipedia.org/wiki/Spaghetti_code) ”，转而提出了结构化编程范式。这封信引发了很多意见——包括一个相反的观点，回复是“[*‘GOTO 被认为有害’被认为有害*](https://web.archive.org/web/20090320002214/http://www.ecn.purdue.edu/ParaMount/papers/rubin87goto.pdf) ”！*