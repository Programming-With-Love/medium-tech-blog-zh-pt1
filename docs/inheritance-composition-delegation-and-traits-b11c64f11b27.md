# 继承、组成、授权和特征

> 原文：<https://blog.kotlin-academy.com/inheritance-composition-delegation-and-traits-b11c64f11b27?source=collection_archive---------0----------------------->

![](img/84e4af783487975452b35c91c6bc02e6.png)

共享公共行为是编程中最重要的事情之一。通过编程，我们代表了随时间变化的知识。相同知识的实例越多，改变它就越难。为了操作一个具体的例子，让我们以`onDestroy`中 RxJava 订阅收集和取消订阅的常见行为为例:

```
var subscriptions: List<Subscription> = listOf()fun onDestroy() {
    subscriptions.forEach { it.unsubscribe() }
}
```

我们需要在所有实现一些逻辑的类中使用它，因为它们都产生订阅。我们还不知道，但很快 RxJava 2 就会来到我们的项目中，我们需要将其更改为以下模式:

```
val subscriptions = CompositeDisposable()fun onDestroy() {
    subscriptions.dispose()
}operator fun CompositeDisposable.plus(d: Disposable) {
    subscriptions.add(d)
}
```

这解释了为什么把以前的模式复制粘贴到我们使用的每个类中是一个非常糟糕的主意。在所有的职业中改变这一点是非常困难的。太好了，我们很聪明，我们记得代码重用；)今天我们来讨论一下我们的替代方案。

[![](img/0742a8ad0cfd3851db2d28061bf6f214.png)](https://leanpub.com/effectivekotlin/c/3YYtCtqCC6a4)

## 继承问题

![](img/580e5458e5214949f6461f00a98eb298.png)

Classes are fashionable and look good, but they are not really liked by programmers anymore.

继承是 OOP(面向对象编程)中重用代码最直观的方式。我们可以用它来解决这个问题，并在一个开放类中定义我们的模式:

```
open class RxJavaUser {
    var subscriptions: List<Subscription> = listOf() fun onDestroy() {
        subscriptions.forEach { it.unsubscribe() }
    }
}
```

虽然遗传有很多不好的地方。例如，我们不能在 JVM 中扩展一个以上的类。结果，我们最终用`Base`类来表示一切:`BaseActivity`，`BasePresenter`，`BaseViewAdapter`，…它们中的每一个集合了子类中经常使用的方法和属性。虽然不是好模式。在一个类中混合多种功能并不好。我们可能还需要在许多基类中包括我们的订阅和取消订阅功能，如`BaseActivity`、`BaseFragment`等。此外，获取不需要的能力也是一种不好的做法。

继承的问题比较多。最大的问题是继承打破了封装。看一下下面的类:

```
**class** CountingHashSet<T> : HashSet<T>() {

    **var addCount** = 0
        **private set

    override fun** add(o: T): Boolean {
        **addCount** += 1
        **return super**.add(o)
    }

    **override fun** addAll(collection: Collection<T>): Boolean {
        **addCount** += collection.**size
        return super**.addAll(collection)
    }
}
```

下面是用法:

```
val countingSet = CountingHashSet()
countingSet.addAll(listOf(1,2,3))
print(countingSet.addCount)
```

结果如何呢？

不，不是“3”。它打印“6”。

原因是`addAll`在引擎盖下用了`add`的方法。那么，也许我们可以删除`addAll`中的加法？是的，我们可以，而且我们可能应该这样做——我们和许多其他开发者和库创建者。Java 创造者知道这一点，他们也知道他们不能再改变`HashSet`实现了。它只会破坏所有依赖于其内部实现的库，并且会导致意想不到的和难以发现的错误。这就是为什么我们应该使我们的类成为最终类，以及为什么我们应该选择替代而不是继承。最常见的选择是合成。

## 作文

![](img/b9d7f743ab88bc5407ea9193053234f0.png)

[组合](https://en.wikipedia.org/wiki/Object_composition)是一个复杂的词，实际上意味着一个类包含另一个类的实例，并使用它的功能。例如，订阅收集和取消订阅功能可以保存在一个单独的类中，并通过组合来使用:

```
class SubscriptionsCollector {
    private var subscriptions: List<Subscription> = listOf() fun add(s: Subscription) {
        subscriptions += s
    } fun onDestroy() {
        subscriptions.forEach { it.unsubscribe() }
    }
}class MainPresenter {
    val subscriptionsCollector = SubscriptionsCollector() fun onDestroy() {
       subscriptionsCollector.onDestroy()
    } //...
}
```

注意我们的`SubscriptionsCollector`和 RxJava 2 `CompositeDisposable`是同一个概念。这意味着我们将来会有额外的包装。不过还不错。优势在于我们可以控制这种包装，如果我们需要迁移到 RxJava 3，那么我们只需在一个类中替换用法。

但是使用起来并不容易。我们每次都需要声明`onDestroy`，并且我们每次想要添加另一个订阅时都需要使用`subscriptionsCollector`。继承给出了更简单的用法。它也给出了多态行为。例如，我们可以用下面的方式使用组合来定义`CountingHashSet`:

```
**class** CountingHashSet<T> {
    **val set** = HashSet<T>()
    **var addCount** = 0
        **private set

    fun** add(o: T): Boolean {
        **addCount** += 1
        **return set**.add(o)
    }

    **fun** addAll(collection: Collection<T>): Boolean {
        **addCount** += collection.**size
        return set**.addAll(collection)
    }
}
```

虽然这个类不是一个`MutableSet`并且除了`add`和`addAll`它没有实现任何其他功能。我们可以让它实现`MutableSet`，但是我们会以下面的怪物结束:

```
**class** CountingHashSet<T>: MutableSet<T> {

    **val set** = HashSet<T>()
    **var addCount** = 0
        **private set

    override fun** add(element: T): Boolean {
        **addCount** += 1
        **return set**.add(element)
    }

    **override fun** addAll(elements: Collection<T>): Boolean {
        **addCount** += elements.**size
        return set**.addAll(elements)
    }

    **override fun** clear() {
        **set**.clear()
    }

    **override fun** iterator(): MutableIterator<T> {
        **return set**.iterator()
    }

    **override fun** remove(element: T): Boolean {
        **return set**.remove(element)
    }

    **override fun** removeAll(elements: Collection<T>): Boolean {
        **return set**.removeAll(elements)
    }

    **override fun** retainAll(elements: Collection<T>): Boolean {
        **return set**.retainAll(elements)
    }

    **override val size**: Int
        **get**() = **set**.**size

    override fun** contains(element: T): Boolean {
        **return set**.contains(element)
    }

    **override fun** containsAll(elements: Collection<T>): Boolean {
        **return set**.containsAll(elements)
    }

    **override fun** isEmpty(): Boolean {
        **return set**.isEmpty()
    }
}
```

这是委托的一个例子——使用组合的模式——kot Lin 支持它并允许更简单的符号。

[![](img/018370a2476e1ce49e6d3299428b4f2a.png)](https://www.kt.academy/#workshops-offer)

## 委托

![](img/43cf736999403087cd4ab3a8db2cfd10.png)

上面的巨大类可以用下面的实现来代替:

```
**class** CountingHashSet<T>(
        **val innerSet**: MutableSet<T> = HashSet<T>()
) : MutableSet<T> **by innerSet** {

    **var addCount** = 0
        **private set

    override fun** add(element: T): Boolean {
        **addCount** += 1
        **return innerSet**.add(element)
    }

    **override fun** addAll(elements: Collection<T>): Boolean {
        **addCount** += elements.**size
        return innerSet**.addAll(elements)
    }
}
```

它做的完全一样——没有实现的来自`MutableSet`的方法将由只使用`innerSet`的主体生成。结果，我们有了多态行为(我们实现了`MutableSet`)、代码重用和简短声明。这太完美了。虽然这种模式并不常用。我们的订阅管理怎么样？我们能在那里使用它吗？嗯，我们或许可以:

```
interface SubscriptionsCollector {
    var subscriptions: List<Subscription>
    fun onDestroy()
}class SubscriptionsCollectorImpl {
    var subscriptions: List<Subscription> = listOf() fun onDestroy() {
        subscriptions.forEach { it.unsubscribe() }
    }
}class MainPresenter(
    val subscrCollector = SubscriptionsCollectorImpl()
): SubscriptionsCollector by subscrCollector {
    //...
}
```

这种用法很可怕，它扰乱了我们的论点。定制`onDestroy`也不直观。唯一的好处是我们可以像使用继承一样添加订阅。我绝对不会使用授权支持来解决这个问题！

## 特征

做了这么多尝试，仍然很难找到一个好的替代方案来解决我们的问题。虽然，还有另一种技术应该是每个程序员工具箱的一部分。我们可以利用特征。在 Kotlin 中，接口是特征(历史上甚至使用关键字`trait`而不是`interface`)。在实践中，这意味着我们可以为方法定义默认体并声明属性(但是没有任何实际值，所以它们仍然必须在非抽象类中被覆盖):

```
interface NumberHolder {
   val number: Int
   fun doubled() = number * 2
}class TenNumberHolder: NumberHolder {
    override val number = 10
}print(TenNumberHolder().doubled()) // 20
```

(mixins 是一个类似的概念，但是在 Mixins 中，我们也可以持有状态。例如，我们可以用默认值来声明属性。他们还没有得到科特林的支持。)

特质的概念非常强大。让我们玩一会儿吧。假设我们写了一个格斗模拟器。每一个战士都表现为一个阶级。他们都有自己的职业，同时也有自己的特点。也有不是人物的怪物。我们希望允许这些角色之间的战斗模拟。

![](img/feace41122baf6044f418bef3f89f248.png)

先说作战能力。我们可以用一个界面来表达战斗能力:

```
**interface** Fighter {
    **var lifePoints**: Int
    **fun** attack(opponent: Fighter)
    **fun** turnEnd() {}
}
```

怪物只是战士:

```
**data class** Goblin(**override var lifePoints**: Int = 50) : Fighter {

    **override fun** attack(opponent: Fighter) {
        *println*(**"Goblin attack (5)"**)
        opponent.**lifePoints** -= 5
    }
}
```

还有人物。假设他们还有名字:

```
**interface** Character : Fighter {
    **val name**: String
        **get**() = **this**.*javaClass*.*name* }
```

基于类名构造名字的行为已经是一种特征。虽然这只是一个开始。有几类人物。每个人都可以成为`Sorcerer`和/或`Warrior`。因为我们不能扩展一个以上的类，所以我们不能使用继承来表达`Warrior`和`Sorcerer`功能。我们将使用特征来代替。假设`Warrior`可以用`meleeAttack`。生命值取决于他的力量，所以我们也需要这样的属性:

```
**interface** Warrior : Character {
    **val strength**: Int

    **fun** meleeAttack(opponent: Fighter) {
        *println*(**"$name melee attack with power $strength"**)
        opponent.**lifePoints** -= **strength** }
}
```

`Sorcerer`反之，可以`castSpell`。他需要一些法术和法力点。

```
**interface** Sorcerer : Character {
    **val spell**: Spell
    **var manaPoints**: Int

    **fun** canCastSpell() = **manaPoints** > **spell**.**manaCost

    fun** castSpell(opponent: Fighter) {
        **if** (**manaPoints** < **spell**.**manaCost**) {
            *println*(**"$name tried to cast spell but not enough mana"**)
            **return** }
        *println*(**"$name cast spell ${spell**.**strength}"**)
        **manaPoints** -= **spell**.**manaCost** opponent.**lifePoints** -= **spell**.**strength** }

    **override fun** turnEnd() {
        **manaPoints** += 1
    }
}
```

注意这里也声明了一个默认的恢复法力点的方式(在`turnEnd`)。现在我们可以用上面的职业来声明一些角色。假设我们有明斯克，强大的战士，所以他使用`meleeAttack`战斗:

```
**data class** Minsk(
        **override var lifePoints**: Int = 60
) : Warrior { **override val strength**: Int = 15

    **override fun** attack(opponent: Fighter) {
        meleeAttack(opponent)
    }
}
```

我们还有亚瑟，他既是战士又是巫师。他同时使用法术和近战攻击:

```
**data class** Artur(
        **override var lifePoints**: Int = 80,
        **override var manaPoints**: Int = 10
) : Warrior, Sorcerer { **override var spell** = Spell(4, 17)
    **override val strength**: Int = 5

    **override fun** attack(opponent: Fighter) {
        **if** (canCastSpell()) {
            castSpell(opponent)
        } **else** {
            meleeAttack(opponent)
        }
    }
}
```

我们可以模拟他们之间的打斗:

```
**fun** simulateCombat(c1: Fighter, c2: Fighter) {
    **while** (c1.**lifePoints** > 0 && c2.**lifePoints** > 0) {
        c1.attack(c2)
        c2.attack(c1)
        c1.turnEnd()
        c2.turnEnd()
    }
    **val** text = **when** {
        c1.**lifePoints** > 0 -> **"$**c1 **won"**
        c2.**lifePoints** > 0 -> **"$**c2 **won"**
        **else** -> **"Both $**c1 **and $**c2 **are dead"** }
    *println*(text)
}*simulateCombat*(Artur(), Minsk())
Artur cast spell 17
Minsk melee attack with power 15
Artur cast spell 17
Minsk melee attack with power 15
Artur melee attack with power 5
Minsk melee attack with power 15
Artur cast spell 17
Minsk melee attack with power 15
Artur melee attack with power 5
Minsk melee attack with power 15
Artur(lifePoints=5, manaPoints=3) won*simulateCombat*(Goblin(), Minsk())
Goblin attack (5)
Minsk melee attack with power 15
Goblin attack (5)
Minsk melee attack with power 15
Goblin attack (5)
Minsk melee attack with power 15
Goblin attack (5)
Minsk melee attack with power 15
Minsk(lifePoints=40) won
```

这种模式非常强大，并在许多情况下得到应用。但是它有一个问题。特征不能保持状态。它们只能用于重用**行为**。如果我们回到订阅问题，我们将被迫在每个使用我们特征的类中实现`subscriptions`:

```
interface RxJavaUser {
    var subscriptions: List<Subscription> fun onDestroy() {
        subscriptions.forEach { it.unsubscribe() }
    }
}class MainPresenter(): RxJavaUser {
    var subscriptions: List<Subscription> = listOf()
}
```

随着向 RxJava 2 的过渡，我们将被迫改变每个类中的订阅定义。不太好。

那么最好的解决方案是什么？对不起，但是我没有一个完美的答案。我们已经看到了一些不同的尝试，它们都有一些优点和缺点。尽管我们也已经看到了这些尝试是如何以非常有用的方式使用的。我们已经看到了组合如何帮助我们克服遗传问题，表达更健康的关系。我们已经看到了委托是如何给我们带来多态行为的。最后，我们已经看到了特征，以及当我们需要表达类似于几个类的行为时，它们是多么强大。我希望你能记住继承的所有替代品，因为它们是重要的工具，能让你的代码更好，更有表现力。

[![](img/87c508a2627eaa3d0e472518952dc75a.png)](https://blog.kotlin-academy.com/write-for-kotlin-academy-abebd70937ce)

# 关于作者

[马尔金·莫斯卡兹拉](http://marcinmoskala.com/)([@马尔金莫斯卡拉](https://twitter.com/marcinmoskala))是一名培训师兼顾问，目前专注于给**在 Android 和高级 Kotlin 工作坊** ( [填写表格](https://marcinmoskala.typeform.com/to/iwKnN9)，我们可以谈谈你的需求)。他还是一名演讲者，撰写了关于 kot Lin Android 开发的文章和书籍。

你需要 Kotlin 工作室吗？访问[我们的网站](https://www.kt.academy/)看看我们能为您做些什么。

了解最新的 [Kt 重大新闻。学院](https://blog.kotlin-academy.com/)、[订阅时事通讯](https://kotlin-academy.us17.list-manage.com/subscribe?u=5d3a48e1893758cb5be5c2919&id=d2ba84960a)、[观察推特](https://twitter.com/ktdotacademy)并关注。

[![](img/3146970f03e44cb07afe660b0d43e045.png)](http://eepurl.com/diMmGv)

我想感谢杰夫·福尔克的语言审查。