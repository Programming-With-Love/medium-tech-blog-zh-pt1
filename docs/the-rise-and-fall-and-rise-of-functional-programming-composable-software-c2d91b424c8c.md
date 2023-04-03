# 函数式编程(组合软件)的兴衰与崛起

> 原文：<https://medium.com/javascript-scene/the-rise-and-fall-and-rise-of-functional-programming-composable-software-c2d91b424c8c?source=collection_archive---------2----------------------->

![](img/b5319c93f5a4237f1472d1686f5b1e6f.png)

Smoke Art Cubes to Smoke — MattysFlicks — (CC BY 2.0)

> **注:**这是《作曲软件》系列的一部分**s**[(现在一本书！)](https://leanpub.com/composingsoftware) 从基础开始学习 JavaScript 6+中的函数式编程和组合软件技术。敬请关注。还会有更多这样的事情发生！
> [*买书*](https://leanpub.com/composingsoftware) *|* [*索引*](/javascript-scene/composing-software-the-book-f31c77fc3ddc)*|*[*<上一篇*](/javascript-scene/the-dao-of-immutability-9f91a70c88cd) *|* [*下一篇>*](/javascript-scene/why-learn-functional-programming-in-javascript-composing-software-ea13afc7a257)

当我大约 6 岁的时候，我花了很多时间和我最好的朋友玩电脑游戏。他家有一屋子的电脑。对我来说，它们是不可抗拒的。魔法。我花了很多时间探索所有的游戏。有一天我问我的朋友，“我们怎么做一个游戏？”

他不知道，所以我们问他的爸爸，他伸手从一个高架子上拿下来一本用 Basic 语言写的游戏书。于是开始了我的编程之旅。等到公立学校开始教代数的时候，我已经很了解这个话题了，因为编程基本上就是代数。不管怎样，可能是。

# 函数式编程的兴起

在计算机科学的开端，在大多数计算机科学实际上在计算机上完成之前，有两位伟大的计算机科学家:阿隆佐·邱奇和艾伦·图灵。他们提出了两种不同但等价的通用计算模型。两个模型都可以计算任何可以计算的东西(因此，“通用”)。

阿隆佐·邱奇发明了λ微积分。Lambda 演算是基于函数应用的通用计算模型。艾伦·图灵因图灵机而闻名。图灵机是一种通用的计算模型，它定义了一种在一条带上操纵符号的理论装置。

他们一起合作证明了λ演算和图灵机在功能上是等价的。

Lambda 演算是关于函数合成的。从功能组合的角度思考是一种非常有表现力和说服力的软件组合方式。在本文中，我们将讨论功能组合在软件设计中的重要性。

有三个要点使 lambda 演算与众不同:

1.  函数总是匿名的。在 JavaScript 中，`const sum = (x, y) => x + y`的右边是*匿名*函数表达式`(x, y) => x + y`。
2.  lambda 演算中的函数只接受一个输入。他们一元。如果需要多个参数，函数将接受一个输入并返回一个接受下一个输入的新函数，依此类推。n 元函数`(x, y) => x + y`可以表示为一元函数，比如:`x => y => x + y`。这种从 n 元函数到一元函数的转换称为 currying。
3.  函数是一级的，意味着函数可以作为其他函数的输入，函数可以返回函数。

总的来说，这些特性形成了一个简单而富有表现力的词汇表，用于使用函数作为主要构件来编写软件。在 JavaScript 中，匿名函数和 curried 函数是可选的特性。虽然 JavaScript 支持 lambda 演算的重要特性，但它并不强制执行这些特性。

经典的函数组合将一个函数的输出作为另一个函数的输入。例如，组成:

```
f . g
```

可以写成:

```
compose2 = f => g => x => f(g(x))
```

你可以这样使用它:

```
double = n => n * 2
inc = n => n + 1compose2(double)(inc)(3)
```

`compose2()`函数将`double`函数作为第一个参数，将`inc`函数作为第二个参数，然后将这两个函数的组合应用于参数`3`。再看`compose2()`的签名，`f`是`double()`，`g`是`inc()`，`x`是`3`。函数调用`compose2(double)(inc)(3)`实际上是 3 个不同的函数调用:

1.  第一个通过`double`并返回一个新函数。
2.  返回的函数采用`inc`并返回一个新函数。
3.  下一个返回的函数取`3`并对`f(g(x))`求值，现在是`double(inc(3))`。
4.  `x`评估为`3`并进入`inc()`。
5.  `inc(3)`评估为`4`。
6.  `double(4)`评估为`8`。
7.  从函数中返回。

Lambda 演算对软件设计有着巨大的影响，在大约 1980 年之前，许多计算机科学的非常有影响力的图标正在使用函数组合来构建软件。Lisp 创建于 1958 年，深受 lambda 演算的影响。今天，Lisp 是仍在广泛使用的第二古老的语言。

我是通过 AutoLISP(AutoLISP:最流行的计算机辅助设计(CAD)软件 AutoCAD 中使用的脚本语言)了解它的。AutoCAD 如此受欢迎，几乎所有其他 CAD 应用程序都支持 AutoLISP，因此它们可以兼容。Lisp 也是计算机科学课程中流行的教学语言，原因有三:

1.  它的简单性使得在大约一天的时间内学习 Lisp 的基本语法和语义变得很容易。
2.  Lisp 完全是关于函数组合的，函数组合是构建应用程序的一种优雅方式。
3.  我所知道的最好的计算机科学教科书使用 Lisp: [计算机程序的结构和解释](https://www.amazon.com/Structure-Interpretation-Computer-Programs-Engineering/dp/0262510871/ref=as_li_ss_tl?ie=UTF8&linkCode=ll1&tag=eejs-20&linkId=4896ed63eee8657b6379c2acd99dd3f3)。

# 函数式编程的衰落

在 1970 年和 1980 年之间的某个时候，软件的编写方式脱离了简单的代数数学，而变成了一系列线性指令，供计算机在像 K&R C (1978)这样的语言和 20 世纪 70 年代和 80 年代早期家用计算机附带的微型 BASIC 解释程序中遵循。

1972 年，Alan Kay 的 Smalltalk 正式成立，对象作为组成的原子单位的想法开始扎根。Smalltalk 关于组件封装和消息传递的伟大思想在 20 世纪 80 年代和 90 年代被 C++和 Java 扭曲成了一个关于继承层次和 is-a 关系以实现特性重用的可怕思想。

尽管 Smalltalk 是一种函数式 OOP 语言，但当 C++和 Java 占据了人们的注意力时，函数式编程被降到了局外人和学术界的地位:编程极客、常春藤大厦中的教授以及一些逃脱了上世纪 90 年代(2010 年)Java 强迫性痴迷的幸运学生的幸福痴迷。

对于我们大多数人来说，30 年来，开发软件有点像一场噩梦。黑暗时代。；)

> *注意:我学会了用 Basic、Pascal、C++和 Java 编程。我使用 AutoLisp 来处理 3D 图形。只有 AutoLisp 可以正常工作。在我编程生涯的最初几年，我没有意识到函数式编程是矢量图形编程领域之外的一个实用选项。*

# 函数式编程的兴起

大约在 2010 年，一些伟大的事情开始发生:JavaScript 爆发了。大约在 2006 年之前，JavaScript 被广泛认为是一种玩具语言，用于在 web 浏览器中制作可爱的动画，但它隐藏着一些强大的功能。也就是 lambda 演算最重要的特征。人们开始私下谈论这个叫做“函数式编程”的很酷的新事物。

到了 2015 年，用功能组合来构建软件的想法再次流行起来。更简单地说，JavaScript 规范获得了十年来的第一次重大升级，并添加了箭头函数，这使得创建和读取函数、currying 和 lambda 表达式变得更加容易。

箭头函数就像是 JavaScript 中函数式编程爆炸的火箭燃料。今天，很少看到大型应用程序不使用大量函数式编程技术。

# 函数式编程一直都很活跃

尽管这是对流行语言的挖苦，函数式编程一直都很活跃。Lisp 和 Smalltalk 是 20 世纪 80 年代和 90 年代 C 在工业领域最大的竞争对手。Smalltalk 是包括 [**、摩根大通**](http://www.cincomsmalltalk.com/main/successes/financial-services/jpmorgan/) 在内的财富 500 强公司的一个流行的企业软件解决方案，Lisp 在**、美国国家航空航天局 JPL** 用于编程火星探测器。

Lisp 过去和现在都被斯坦福研究院(SRI)用于人工智能和生物信息学研究。(苹果 SIRI 虚拟助手背后的技术就是在 SRI 开发的)。硅谷最有影响力的风险投资公司之一，由保罗·格拉厄姆共同创立，他是 Lisp 社区的一个有影响力的人。热门科技新闻网站 [**黑客新闻**](https://news.ycombinator.com/) 是用 Ark(Lisp 的一种方言)写的。

Clojure 是一种 Lisp 方言，由 Rich Hickey 在 2007 年创建，并迅速在主要的科技公司中流行和使用，包括亚马逊、苹果、脸书和 T21。

Erlang 是一种流行的函数式编程语言，由爱立信公司开发，用于电话交换。它仍然被 T-Mobile 用于移动网络。亚马逊将 Erlang 用于云技术，包括 SimpleDB 和弹性计算云(EC2)。

尽管如此，作为 web 的标准语言，JavaScript 是世界上最流行的编程语言，JavaScript 让空前多的开发人员接触到了函数式编程范式。

> [*买书*](https://leanpub.com/composingsoftware) *|* [*索引*](/javascript-scene/composing-software-the-book-f31c77fc3ddc)*|*[*<上一张*](/javascript-scene/the-dao-of-immutability-9f91a70c88cd)*|**下一张>*

# 在 EricElliottJS.com 了解更多信息

EricElliottJS.com 会员可以参加互动代码挑战视频课程。如果你还不是会员，今天就注册吧。

***Eric Elliott****是一位分布式系统专家，著有书籍* [*【排版软件】*](https://leanpub.com/composingsoftware)*[*【编程 JavaScript 应用】*](https://ericelliottjs.com/product/programming-javascript-applications-ebook/) *。作为*[*devanywhere . io*](https://devanywhere.io/)*的联合创始人，他教授开发人员远程工作和拥抱工作/生活平衡所需的技能。他为加密项目组建开发团队并提供建议，为 Adobe Systems、****【Zumba Fitness、*** ***【华尔街日报、*******【ESPN、*******BBC、*** *等顶级录音艺术家提供软件体验****

**他和世界上最美丽的女人享受着与世隔绝的生活方式。**