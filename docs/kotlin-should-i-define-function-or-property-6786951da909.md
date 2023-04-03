# 科特林:我应该定义功能还是属性？

> 原文：<https://blog.kotlin-academy.com/kotlin-should-i-define-function-or-property-6786951da909?source=collection_archive---------0----------------------->

![](img/2c53677ad3284253a5686669d4ffba5e.png)

Photo by [grafixart grafixart_photo](https://unsplash.com/photos/zGEaKWQqYY4?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/search/photos/close?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

最近，我意识到在属性和函数的使用上存在混淆。科特林引入属性的概念是有充分理由的。问题出现了——什么时候使用一个而不是另一个？我建议你遵循的最简单的规则是:

*   属性描述状态
*   **功能**描述行为

让我们仔细看看他们两个。**属性**代表一种可以描述对象状态的数据结构，例如: **Person** object 可以有 ***name*** 、 ***lastName*** 和 ***weight*** 属性。

我们还可以拥有[派生属性](https://www.uml-diagrams.org/derived-property.html)——通过组合 ***名称*** 和 ***姓氏*** 创建的动态计算的属性，例如 ***全名*** 。

在上面的例子中，属性值只被评估一次——在 *Person* object 创建期间。有时这是一种可取的行为，但在这种情况下，当我们改变一个 *name* 或 *lastName* 属性的值时，存储在 *fullName* 中的值将不会被更新。幸运的是，Kotlin 还让我们能够使用 *getter* 或 *setter* 来定义属性。在下面的例子中， *fullName* 属性的值将在每次访问时被评估。

**功能**，另一方面，都是关于对象可以执行的行为或动作。我们的**人**类本来可以有*****走()*** 和**跳()**的方法。**

**重要的是要注意，方法可能(并且经常会)间接地修改对象的状态，作为执行动作的副作用，例如，每次我们调用**jump()**person**weight**减少 0.1。**

**如果您仍然不清楚何时使用属性来运行，那么从外部客户的角度来考虑它们可能会有所帮助。我们应该暂时忘记内部实现(属性存储在哪里或者行为是如何实现的),而只看外部 API(类似于将第三方库添加到我们的项目中，我们只访问 API)。**

**科特林引入属性有几个很好的理由。其中之一是*属性访问语法*(我们使用*高度*而不是*getHeight*/*setHeight*)更简洁，另一个是 getter 和 setter 更接近于属性声明，不像 Java 通常将 property 放在类的顶部，getter/setter 放在底部。我最喜欢的特性是*属性委托*，它增加了重用代码的能力。我们可以在需要的时候初始化一个变量([惰性委托](https://kotlinlang.org/docs/reference/delegated-properties.html#lazy))，在属性值改变的时候执行一些动作[(可观察委托](https://kotlinlang.org/docs/reference/delegated-properties.html#observable))或者使用一个自定义委托将我们的属性存储在另一个对象中(Android 共享首选项、地图、浏览器会话、数据库……)。**

> **好的一面是 Kotlin 允许我们在接口中使用函数(技术上的方法)和属性。**

**需要注意的是，来自 Java 的开发人员(在 Java 中我们没有属性的概念)倾向于过度使用函数。这里有一个很好的重构到属性的候选者:**

**当我们看到一个以 **set** 前缀开始的方法(例如 *setWeight* )有一个参数，并将其分配给私有字段，或者以 **get** 开始的方法返回私有字段值(getWeight)时，我们可能应该定义 property。**

## **指导方针:如何做决定？**

**每次你想声明新函数的时候，问自己两个问题:**

*   **“它描述了一种行为吗？”—描述行为的**函数**的候选函数，例如 ***run()*** *、****walk()****和 **jump()*****
*   ***“它描述了一种状态吗？”—**属性的候选项**描述状态，如 ***姓名*** 、 ***姓氏*** 、 ***体重******

**还有一组额外的指导原则(如果我没记错的话，来自 Effective Java)帮助我们判断属性是否优于功能:**

*   **不引发异常**
*   **计算成本低(或在第一次运行时缓存)**
*   **多次调用后返回相同的结果**

**上面的指导方针应该给你一个很好的主意，不管特定的成员应该是函数还是属性。在开始的时候，定义属性可能有点不自然(特别是对于 Java 开发人员来说)，但是相信我——过一段时间，这个决定就会很明显了。**

**如果你喜欢这篇文章，请在这里和 twitter 上关注我👏。我很想在评论区听到你的想法。**

**要了解最新的精彩文章，你还可以[订阅时事通讯](https://kotlin-academy.us17.list-manage.com/subscribe?u=5d3a48e1893758cb5be5c2919&id=d2ba84960a)。**

**[![](img/5ce68714efe3efc036e06786166954ff.png)](http://eepurl.com/diMmGv)**