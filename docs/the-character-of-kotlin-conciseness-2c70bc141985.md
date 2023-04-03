# 科特林的性格:简洁

> 原文：<https://blog.kotlin-academy.com/the-character-of-kotlin-conciseness-2c70bc141985?source=collection_archive---------5----------------------->

在上一篇文章中，我已经解释了为什么科特林像钢铁侠。现在我想继续这个比喻，展现科特林的简洁。

在漫威宇宙中，自从钢铁侠诞生以来，其他国家都在尝试制作自己的版本。如果你比较真实的钢铁侠和复制它的微弱尝试，你可以看到他的宇航服小得多，但更强大。看看在现实世界中重现钢铁侠的尝试有多大:

钢铁侠的强大之处在于，它不是一艘飞船，也不是一辆坦克，相反，它是简洁而又强大的结构。每一个额外的元素都是成本，因为它需要移动、供电和维护。由于钢铁侠的服装很小，托尼·斯塔克可以轻松击败使用更多能量和资源的巨大飞船。

![](img/0634ea4049eacba48716f611edefa315.png)

这也是 Kotlin 的一个巨大的威力:它是高度简洁的语言。让我们来看看支持它的一些重要特性。

# 班级创建

让我们讨论如何定义数据模型。我们需要定义一个有 id，名字，姓氏和年龄的用户。这是我们在 Java 中定义它的方式:

```
// Java 
**public class** User {
    **private int id**;
    **private** String **name**;
    **private** String **surname**;
    **private int age**;

    **public** User(**int** id, String name, String surname, **int** age) {
        **this**.**id** = id;
        **this**.**name** = name;
        **this**.**surname** = surname;
        **this**.**age** = age;
    }

    **public int** getId() {
        **return id**;
    }

    **public void** setId(**int** id) {
        **this**.**id** = id;
    }

    **public** String getName() {
        **return name**;
    }

    **public void** setName(String name) {
        **this**.**name** = name;
    }

    **public** String getSurname() {
        **return surname**;
    }

    **public void** setSurname(String surname) {
        **this**.**surname** = surname;
    }

    **public int** getAge() {
        **return age**;
    }

    **public void** setAge(**int** age) {
        **this**.**age** = age;
    }
}
```

这是 Kotlin 中相同类的外观:

```
**class** User(
        **var id**: Int,
        **var name**: String,
        **var surname**: String,
        **var age**: Int
)
```

看起来不可能？然而，我们仍然有构造函数、封装字段等。科特林哲学的一部分是，普通的事情应该简单。定义数据模型真的很常见，所以 Kotlin 被设计成支持它也就不足为奇了。让我们一步一步地分析过渡。

首先，我们不需要 setters 和 getters，因为它们是[内置属性](/kotlin-programmer-dictionary-field-vs-property-30ab7ef70531)。我们总是可以改变物业服务或设定价值的方式:

```
**var** *name*: String = **""
    get**() = **field**.*toUpperCase*()
    **set**(value) {
        **if**(value.*isNotBlank*()) {
            **field** = value
        }
    }
```

因此，Java 类 User 的直接等效将如下所示:

```
**class** User {
    **var id**: Int
    **var name**: String
    **var surname**: String
    **var age**: Int

    **constructor**(id: Int, name: String, surname: String, age: Int) {
        **this**.**id** = id
        **this**.**name** = name
        **this**.**surname** = surname
        **this**.**age** = age
    }
}
```

因为对象有一种主要的构造方式是很常见的，所以 Kotlin 引入了称为主构造函数的特性。基本上，是在类名之后定义的构造函数指定了在类创建期间可以使用的字段。这是我们在课堂上使用它的方法:

```
**class** User(id: Int, name: String, surname: String, age: Int) {
    **var id**: Int = id
    **var name**: String = name
    **var surname**: String = surname
    **var age**: Int = age
}
```

下一个重要的改进是属性值经常通过主构造函数传递。这就是为什么 Kotlin 提出了更简洁的符号，允许在主构造函数中定义属性。当我们使用它时，我们有我们的最终符号:

```
**class** User(
        **var id**: Int,
        **var name**: String,
        **var surname**: String,
        **var age**: Int
)
```

当然，我们不需要一步一步的做这个过渡。IDE 可以为我们做到:

你可能会问“如果我需要定义其他构造函数怎么办？”。例如，假设我们想要一个不传递 id 的构造函数，但是我们想要它自动生成。我们可以使用普通的构造函数(在 Kotlin 中称为“secondary”)来实现:

```
**class** User(
        **var id**: Int, 
        **var name**: String, 
        **var surname**: String, 
        **var age**: Int
) {

    **constructor**(name: String, surname: String, age: Int):
            **this**(UsersRepo.getNextId(), name, surname, age)
}
```

我们还可以为该属性提供默认值:

```
**class** User(
        **var name**: String,
        **var surname**: String,
        **var age**: Int,
        **var id**: Int = UsersRepo.getNextId()
)
```

我把它移到最后一个位置，这样用户就可以通过`User("Marcin", "Moskala", 25)`创建一个对象。如果没有它，如果我们想省略第一个参数`User(name = "Marcin", surname = "Moskala", age = 25)`，我们将不得不使用命名参数语法。

关于这一点，你可以注意到我们不需要使用`new`关键字来使用构造函数。也代表了科特林的简洁，想想也有道理。构造函数就像一个创建对象的函数，在 Kotlin 中它被当作一个函数。如果引用构造函数，其实就是实现函数接口。这意味着它确实是一个函数。这就是我们如何使用构造函数引用将字符串列表映射到对象列表:

```
**class** Person(**val name**: String)
**val** names = *listOf*(**"Tony"**, **"Bruce"**)
**val** persons = names.*map*(::Person) // Person(**"Tony"**), Person(**"Bruce"**)
```

[![](img/018370a2476e1ce49e6d3299428b4f2a.png)](https://www.kt.academy/#workshops-offer)

# 数据修改器

这并不是 Kotlin 高度提高简洁性的特性的终结。在像 Java 这样的面向对象语言中，我们经常需要定义表示一些数据的对象。以上类，`User`就是一个完美的例子。当我们有一些用户时，我们希望对他们进行一些操作，比如在集合中保存他们，比较他们或者显示他们的数据以便调试。这就是为什么定义一组函数是一种非常常见的模式，它允许:

*   `toString` —用于显示对象或在调试期间以更清晰的方式表示对象。
*   `equals` —用于检查两个对象中的数据是否相同，也需要将一个对象保留在`Set`上或者调用`distinct`方法。
*   `hashCode` —需要将对象保存在不同种类的集合中，如`HashMap`或`HashSet`。

这些函数经常被添加到类中，因此 IDEA IntelliJ 对它们有特殊的支持:

科特林让它变得更加简单。你所需要做的就是在一个类前添加数据修饰符:

```
**data class** User(
        **var name**: String,
        **var surname**: String,
        **var age**: Int,
        **var id**: Int = UsersRepo.getNextId()
)
```

有了它，您可以使用上面列出的方法:

```
**data class** User(**var name**: String?, **var surname**: String?, **var age**: Int)**val** user = User(**"Marcin"**, **"Moskala"**, 24)
*println*(user) *// Prints: User("Marcin", "Moskala", 24)
println*(user.toString()) *// Prints: User("Marcin", "Moskala", 24)**println*(user.equals(User(**"Marcin"**, **"Moskala"**, 24))) *// Prints: True
println*(user.equals(User(**"Maciej"**, **"Moskala"**, 26))) *// Prints: False**println*(user.hashCode()) *// Prints: 184607782
println*(user.hashCode()) *// Prints: 184607782*
```

`data`修改器还创建了两个更重要的特性。首先，它允许对象解构:

```
**val** user = User(**"Marcin"**, **"Moskala"**, 24)
**val** (name, surname, age) = user
*print*(**"$**name **$**surname **of age $**age**"**) *// Marcin Moskala of age 24*
```

这样，数据类可以像元组一样使用，我们并不真正需要原生元组支持。`data`修改器给出的另一个方法是`copy`。它创建具体值已更改的新实例:

```
**val** user = User(**"Marcin"**, **"Moskala"**, 25)
**val** user2 = user.copy(name = **"Maciej"**, age =  27)
user.**age** = 26*print*(user) *// User(name=Marcin, surname=Moskala, age=26)
print*(user2) *// User(name=Maciej, surname=Moskala, age=27)*
```

这个特性非常重要，因为有了它，我们可以很容易地使对象成为不可变的，并且我们可以很容易地用改变的属性制作副本。当我们用函数式风格编程时，这是非常有用的。

[![](img/87c508a2627eaa3d0e472518952dc75a.png)](https://blog.kotlin-academy.com/write-for-kotlin-academy-abebd70937ce)

# 经营者

另一个提高简洁性和可读性的特性是[操作符重载](https://kotlinlang.org/docs/reference/operator-overloading.html)。基本上我们更喜欢用`==`而不是`equals`的方法。它可读性更强，也更简洁。加法和更多其他运算符也是如此。这就是为什么 Kotlin 制定了一个约定，即具有具体名称的函数可以像操作符一样使用。例如，我们可以使用`==`而不是`equals`来比较数据类对象:

```
**val** user = User(**"Marcin"**, **"Moskala"**, 25)*println*(user == User(**"Marcin"**, **"Moskala"**, 24)) *// Prints: True
println*(user == User(**"Maciej"**, **"Moskala"**, 26)) *// Prints: False*
```

举个更大的例子，假设我们需要一个表示复数的类。这就是我们如何用一些基本运算符来定义它:

```
**data class** Complex(**val real**: Double, **val img**: Double) {

    **operator fun** plus(i: Double) 
        = Complex(**real** + i, **img**)

    **operator fun** plus(c: Complex) 
        = Complex(**real** + c.**real**, **img** + c.**img**)

    **operator fun** times(i: Double) 
        = Complex(**real** * i, **img** * i)

    **operator fun** times(c: Complex) 
        = Complex(**real** * c.**real** - **img** * c.**img**, **real** * c.**img** + **img** * c.**real**)
}// Usage **val** c1 = Complex(1.0, 2.0)
**val** c2 = Complex(2.0, 3.0)
*println*(c1 + 1.0) *// Prints: Complex(real=2.0, img=2.0)
println*(c1 + c2) *// Prints: Complex(real=3.0, img=5.0)
println*(c1 * 2.0) *// Prints: Complex(real=2.0, img=4.0)
println*(c1 * c2) *// Prints: Complex(real=-4.0, img=7.0)*
```

这种符号简单、简洁，可读性很强。就像整个科特林一样。

![](img/6c4b257603bed9784ec1a6c399eb5cde.png)

你需要 Kotlin 工作室吗？请访问我们的网站，看看我们能为您做些什么。

了解卡帕头最新的重大新闻。学院，[订阅时事通讯](https://kotlin-academy.us17.list-manage.com/subscribe?u=5d3a48e1893758cb5be5c2919&id=d2ba84960a)，[观察 Twitter](https://twitter.com/ktdotacademy) 并在 medium 上关注我们。

在 Twitter 上引用我，用 [@MarcinMoskala](https://twitter.com/marcinmoskala) 。

[![](img/3146970f03e44cb07afe660b0d43e045.png)](http://eepurl.com/diMmGv)

喜欢的话记得**拍**。请注意，如果您按住鼓掌按钮，您可以留下更多的掌声。

![](img/f36a792ac0eb95fc577e6f4125dba956.png)