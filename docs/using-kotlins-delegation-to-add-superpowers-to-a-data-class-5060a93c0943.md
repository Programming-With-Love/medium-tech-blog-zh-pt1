# 使用 Kotlin 的委托为数据类添加超级能力

> 原文：<https://medium.com/capital-one-tech/using-kotlins-delegation-to-add-superpowers-to-a-data-class-5060a93c0943?source=collection_archive---------2----------------------->

![](img/1bf542653e91a6e3fd4676fd26ec59f0.png)

如果你已经用 Kotlin 写了一段时间的代码，你几乎肯定会遇到[委托](https://kotlinlang.org/docs/reference/delegation.html)。委托模式最常见的用法之一是 lazy 的[，它强制一个值直到被请求时才被计算，并且这个值在后续请求时不会被重新计算。](https://kotlinlang.org/docs/reference/delegated-properties.html#lazy)

委托模式的另一个常见用法是当它被用来实现一个`interface` `by`一些注入的参数，比如`CoroutineScope by injectedScope`。这允许我们在注入的参数上调用公共成员，而不必持续地直接引用它，类似于我们使用`with(something)`时的情况。

# 增强固定大小的状态容器

直到最近，我在授权方面的经验仅限于上面的两个例子。然而，后来我遇到了一种优雅的方式，通过使用委托来赋予固定大小的数据容器`Collection`的权力。

在我的职业生涯中，固定大小的数据容器是我经常遇到的一个需求。通常，这些是在编译时已知的一组或一列实体，但是需要通过 API 响应或其他运行时输入来处理或实现各种字段。这种容器的一个例子是用户有资格使用的一套特征。潜在的功能在编译时是已知的，但用户是否有资格使用该功能以及每个功能的所有特征直到应用程序运行时才知道。

在 Kotlin 中，我们通常使用以下两种方式之一来表示这种类型的数据:数据类或`Set<T>`。

# 数据类

在设计这样的容器时，数据类是一个很好的起点。

```
data class FeatureSuite(
  val featureOne: Feature.TypeA.FeatureOne,
  val featureTwo: Feature.TypeA.FeatureTwo,
  val featureThree: Feature.TypeB.FeatureThree,
  ...
)
```

拥有这些特性的`data class`表示的一个主要好处是，我们可以保证在构建套件时考虑到每个特性。这是因为每个特性都是我们类的构造函数的必需参数。此外，作为套件的公共属性，我们对每个特性都有不可空的访问权，所以如果我们需要检查给定的特性是否被启用，我们必须做的就是`suite.featureOne.isEnabled()`。顺便说一下，在普通的`class`上使用`data class`确保了每个参数都必须是`val`或`var`，这有助于加强该域实体的容器特征。

然而，`data class`也有不好的一面。如果我们想获得类型为`TypeA`的特性列表，除了创建并返回所有已知`TypeA`特性列表的“硬编码”公共方法之外，我们别无选择。如果我们要向套件中添加一个新的`TypeA`特性，我们很可能会忘记将其添加到列表中，这也意味着我们要在两个不同的地方跟踪相同的数据，这增加了复杂性。捕捉这一点的单元测试将是脆弱的和尽力而为的，这不是我们在写测试时所渴望的。

# 设定<feature></feature>

我们可以采取的第二种方法是使用一个`Set<Feature>`作为我们特性的容器。这可能类似于:

```
typealias FeatureSuite = Set<Feature>
```

然后，我们可以通过执行以下操作来创建一个`FeatureSuite`:

```
val featureSuite = setOf(
  Feature.TypeA.FeatureOne(...),
  Feature.TypeA.FeatureTwo(...),
  Feature.TypeB.FeatureThree(...),
  ...
)
```

通过使用一个`Set`，我们现在能够像`filterIsInstance`一样获得类型`TypeA`的所有特性，而不必担心如果我们添加一个新特性会丢失一个。

然而，我们已经失去了`data class`的好处！我们不再能保证在创建套件时每个特性都被传入，并且我们不能对每个特性进行空安全访问，因为不能保证特性存在于`Set`中！这种零安全性的缺乏将会传播到套件的所有使用中，如果请求了一个没有添加到套件中的特性，可能会导致`RuntimeException`。

那么，我们如何获得`data class` **和**`Set`的好处，都在一个实体中呢？你猜对了，代表团！

# 代表团，你的新超级大国！

我们应该将`FeatureSuite`定义如下:

```
data class FeatureSuite(
  val featureOne: Feature.TypeA.FeatureOne,
  val featureTwo: Feature.TypeA.FeatureTwo,
  val featureThree: Feature.TypeB.FeatureThree,
  ...
) : Set<Feature> by setOf(
  featureOne,
  featureTwo,
  featureThree,
  ...
)
```

注意，我们现在通过使用由所有特性参数组成的`Set`的委托，同时拥有了`data class`和`Set`的优点。

*   确保所有特性都通过构造函数传递到套件中
*   通过`featureSuite.someFeature`对功能进行零安全访问
*   能够`filterIsInstance`并接收给定类型的所有特性，而不用担心会丢失一个

我们也可以开始添加额外的功能来使呼叫站点更加整洁:

```
val typeAFeatures: List<Feature.TypeA> by lazy {
  filterIsInsance(Feature.TypeA::class.java)
}val enabledFeatures: List<Feature> by lazy {
  filter { it.isEnabled() }
}
```

# 测试

我们应该通过单元测试来执行一些事情，以减少任何未来开发人员错误的风险:

*   所有的特性都应该映射到底层的`Set`(需要确保我们没有遗漏任何一个！).这是最重要的测试。
*   传递给套件的特性实际上与底层`Set`中的特性完全相同(对象相等)

第一个测试很简单。因为`Set`本质上不允许重复，我们只需要确保创建的`Set`的大小与套件本身的参数数量相同。

```
// Notes: `primaryConstructor` is made available through `kotlin-reflect` and `FakeFeatureSuite` is just a convenience method to create a (real) test object that can be re-used in other tests.@Test
fun `All features mapped to Set`() { val featuresRequiredInConstructor =
    FeatureSuite::class.primaryConstructor!!.parameters.size val featuresAddedToSet = FakeFeatureSuite().size  assertThat(featuresAddedToSet)
    .isEqualTo(featuresRequiredInConstructor)}
```

第二个测试不那么可靠，但仍然值得一试:

```
@Test
fun `Object equality of features`() { val featureSuite = FakeFeatureSuite() val featureOneFromSet = featureSuite.single {
    it is Feature.TypeA.FeatureOne
  } assertThat(featureSuite.featureOne)
    .isSameAs(featureOneFromSet)}
```

这个测试的风险是一个新的特性会被添加到套件中，但是不会被添加到这个测试中。我们可以尝试防止这种情况的一种方法是检查断言的数量是否总是等于套件中特性的数量。

```
val assertions = listOf<AbstractAssert<*,*>>(
  // Wrap our assertions in a List assertThat(featureSuite.featureOne)
    .isSameAs(featureOneFromSet)
)val expectedAssertionCount =
  FeatureSuite::class.primaryConstructor!!.parameters.sizeassertThat(assertions.size)
  .`as`( "Ensure all constructor params checked for object equality").isEqualTo(expectedAssertionCount)
```

现在，如果添加了一个新特性，但是没有添加到我们的断言中，这个测试将会失败。我们现在唯一能弄乱它的方法是偶然地复制列表中的断言，而不是为一个新特性添加一个唯一的断言。

# 结论

通过结合普通`data class`和`Set`的优点，我们能够以一种避免`RuntimeException` s 的安全方式为我们的功能套件提供所有需要的功能。一如既往，如果您对我如何改进这一实现有任何建议，请按我的方式提出！

*披露声明:2020 资本一。观点是作者个人的观点。除非本帖中另有说明，否则 Capital One 不隶属于所提及的任何公司，也不被这些公司认可。使用或展示的所有商标和其他知识产权是其各自所有者的财产。*

*原载于 2020 年 5 月 27 日 https://brandontrautmann.com*[](https://brandontrautmann.com/using-delegation-to-add-superpowers-to-a-data-class/)**。**