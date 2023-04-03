# 发布了 Eclipse 集合 10.3

> 原文：<https://medium.com/oracledevs/eclipse-collections-10-3-released-1ee8ea3cf6e1?source=collection_archive---------1----------------------->

这是我们一直在等待的发布。

![](img/0537f0e1ce840a276f0500440ddc8466.png)

Mt. Fuji in 2006

# 感谢社区

Eclipse Collections[10.2](https://github.com/eclipse/eclipse-collections/releases/tag/10.2.0)于 2020 年 2 月发布，是继有点纪念意义的 [10.0 版本](https://github.com/eclipse/eclipse-collections/releases/tag/10.0.0)之后的一个相对较小的 bug 修复版本。我很高兴地说，六个月后， [10.3 发布版](https://github.com/eclipse/eclipse-collections/releases/tag/10.3.0)有许多由我们杰出的贡献者社区提交的新特性。

非常感谢所有贡献者，他们贡献了宝贵的时间，让 Eclipse 集合的功能更加丰富，质量更高。非常感谢你的努力。

如果你正在考虑为一个开源项目做贡献，但是还不确定，看看我们社区贡献者的这个伟大的博客。在博客[中，Sirisha Pratha](https://medium.com/u/8fe7c47c374f?source=post_page-----1ee8ea3cf6e1--------------------------------) 解释了她在成为 OSS 社区的积极贡献者时所体验到的一些好处。

[](/@pratha.sirisha/50-first-contributions-fa698a060b6c) [## 50 次首次投稿

### 2020 年 50 个开源贡献！

medium.com](/@pratha.sirisha/50-first-contributions-fa698a060b6c) 

我们一直在寻找新的贡献者加入 Eclipse 集合社区。如果您正在寻找一个项目开始您的旅程，请考虑为 Eclipse 集合做贡献。

## 承认

在 6 月份的一篇 Java 杂志文章中，Eclipse Collections 获得了一项令人惊讶又令人羞愧的荣誉，它被命名为“有史以来最伟大的 25 个 Java 应用程序”之一。

[](https://blogs.oracle.com/javamagazine/the-top-25-greatest-java-apps-ever-written) [## 有史以来最伟大的 25 个 Java 应用

### Java 的故事始于 1991 年，当时 Sun 微系统公司试图…

blogs.oracle.com](https://blogs.oracle.com/javamagazine/the-top-25-greatest-java-apps-ever-written) 

功劳属于整个 Eclipse Collections 社区。你对图书馆的辛勤工作和耐心得到了认可。祝贺并感谢我们整个社区。继续努力吧！你们都很棒！

## 大量新功能和几个贡献者博客

在 [Eclipse 集合](https://github.com/eclipse/eclipse-collections) 10.3 中包含了如此多的特性，以至于我要花更多的时间来编写利用所有这些特性的好例子。好消息是我已经从社区得到了一些帮助。在我开始写这篇博客之前，一些投稿人在博客上介绍了他们添加的功能。

[Dirk Fauth](https://twitter.com/fipro78) 写了一篇博客，他在博客中评估了在 [NatTable](https://www.eclipse.org/nattable/) 中是否会有任何性能和内存方面的改进，包括 Eclipse 集合。他还提出了一个缺失功能的问题，贡献了该功能，然后更新了博客！

[](http://blog.vogella.com/2020/06/25/nattable-eclipse-collections-performance-memory-improvements/) [## NatTable + Eclipse 集合=性能和内存改进？

### 前一段时间，我从 NatTable 用户那里得到报告，说在使用 NatTable 处理大型数据集时内存消耗很高…

blog.vogella.com](http://blog.vogella.com/2020/06/25/nattable-eclipse-collections-performance-memory-improvements/) 

我感谢 Dirk 对博客和代码的贡献，他在 Twitter 上回应说了一些我觉得非常鼓舞人心的话。

> 依我看，这就是开放源码软件的工作方式。用它，喜欢它，写它。发现问题，报告问题，解决问题，做出反馈。

做得好，说得好！这正是让我在开源中快乐工作的社区精神。

## 对未来版本和未来博客的建议

特色博客是我希望在未来看到更多来自贡献者的东西。这是确保您所贡献的特性被其他开发人员发现的好方法。这反过来可能会鼓励开发人员升级到最新版本。这也是帮助建立你自己的个人品牌的好方法，因为我会从发布博客链接到你的专题博客。我也很乐意喜欢和转发你的博客，并公开感谢你的贡献。你节省了我的时间来解释你在发布博客中添加的功能是如何工作的，所以相信我，我会非常感谢并乐意帮助你的辛勤工作社会化！

# 功能概述

## 贡献者分享的博客功能

1.  `[MapIterable](/@goldbal/ec-by-example-aggregateby-in-mapiterable-to-aggregate-on-key-and-value-55516f1b6d9c)` [中的](/@goldbal/ec-by-example-aggregateby-in-mapiterable-to-aggregate-on-key-and-value-55516f1b6d9c)`[AggregateBy](/@goldbal/ec-by-example-aggregateby-in-mapiterable-to-aggregate-on-key-and-value-55516f1b6d9c)` [对键和值](/@goldbal/ec-by-example-aggregateby-in-mapiterable-to-aggregate-on-key-and-value-55516f1b6d9c)进行聚合——由[亚历克斯·戈德堡](https://medium.com/u/da10a7c5350?source=post_page-----1ee8ea3cf6e1--------------------------------)
2.  `[FlatCollect](/@goldbal/ec-by-example-flatcollect-into-primitive-collections-43d40c16eb85)` [转化为原始集合](/@goldbal/ec-by-example-flatcollect-into-primitive-collections-43d40c16eb85)——作者[亚历克斯·戈德堡](https://medium.com/u/da10a7c5350?source=post_page-----1ee8ea3cf6e1--------------------------------)
3.  [实施](https://dev.to/brianverm/eclipse-collections-now-supports-triples-dkl) `[Triples](https://dev.to/brianverm/eclipse-collections-now-supports-triples-dkl)` —由[布莱恩·弗米尔](https://twitter.com/BrianVerm)
4.  [支持通过间接比较对原语列表进行排序](/@zakhav/eclipse-collections-now-supports-indirect-sorting-of-primitive-lists-e2447ca5dbc3)—[弗拉基米尔·扎哈罗夫](https://medium.com/u/7db07b72520d?source=post_page-----1ee8ea3cf6e1--------------------------------)
5.  [实现](/@nikhilnanivadekar/mapiterable-getordefault-new-but-not-so-new-api-1fbf2c38a508) `[MapIterable.getOrDefault()](/@nikhilnanivadekar/mapiterable-getordefault-new-but-not-so-new-api-1fbf2c38a508)` [以允许简单的互操作](/@nikhilnanivadekar/mapiterable-getordefault-new-but-not-so-new-api-1fbf2c38a508)—[Nikhil Nanivadekar](https://medium.com/u/4285d8a2ca86?source=post_page-----1ee8ea3cf6e1--------------------------------)
6.  [包含由](/javarevisited/fusing-methods-for-productivity-c15c9eb2d666?source=friends_link&sk=ddde162c6aab068bda4619266d3b3bfb)上的`[RichIterable](/javarevisited/fusing-methods-for-productivity-c15c9eb2d666?source=friends_link&sk=ddde162c6aab068bda4619266d3b3bfb)` —由[唐纳德·拉布](https://medium.com/u/df39b86e9f04?source=post_page-----1ee8ea3cf6e1--------------------------------)

## 新网站翻译

Eclipse 收藏网站的[印地语翻译](https://www.eclipse.org/collections/hi/index.html)。

## 新功能

1.  给 MutableMap 添加了`withMap()`。
2.  用 javadoc 将`forEachInBoth`添加到`ListIterable`。
3.  添加了新的 API`ofOccurrences`和`withOccurrences`，以打包可变和不可变的工厂。
4.  增加了`wrapCopy()`到原始列表来反映快速列表的功能。
5.  添加了不可变堆栈的单向链接实现。
6.  原始的`List`和`Set`工厂增加了`withInitialCapacity()`。
7.  向原始 iterables 添加了`toArray()`方法，该方法将一个数组作为参数来存储 iterable 的元素。
8.  添加默认的`aggregateBy`方法到 RichIterable 中，该方法接受一个目标地图。
9.  实现了`Tuples.identicalTwin()`、`Tuples.identicalTriplet()`。
10.  在原始列表中增加了`shuffleThis()`操作。
11.  在`Interval`中增加了`fromToExclusive`。
12.  在可变原语列表上实现了`swap()`方法。
13.  在`IntInterval`执行`subList()`。
14.  引入了`pitest`突变测试。
15.  已实施`LongInterval`。
16.  为`MapIterable`实现了`aggregateBy`,使用了一个变量来聚合键和值。
17.  通过函数实现空安全比较器。

# 其他改进

## 文档更新

1.  为 GitHub 动作构建向 README.md 添加徽章。
2.  添加了`Working with GitHub`维基页面。
3.  增加了`immutableObjectPrimitiveMap`、`immutablePrimitiveObjectMap`、`immutablePrimitivePrimitiveMap`的 Javadocs。
4.  增加了`mutableObjectPrimitiveMap`、`mutablePrimitiveObjectMap`、`mutablePrimitivePrimitiveMap`、`mutablePrimitiveValuesMap`的 Javadocs。
5.  增加了`primitiveObjectMaps`、`primitivePrimitiveMaps`、`primitiveValuesMaps`、`objectPrimitiveMaps`的 Javadocs。
6.  改进了`Function2`、`Function3`和`MutableCollection#injectIntoWith`的文档。
7.  增加了`README_EXAMPLES.md`。
8.  修正了网站中的等级依赖设置。

## 错误修复

1.  区间和区间内固定大小的边界情况问题。
2.  修复了 Javadoc 警告、代码生成错误。
3.  修复了检查、换行和空白违规。
4.  修正了多重映射工厂中工厂方法的对称性问题。
5.  添加了对部分换行的逗号分隔列表的 CheckStyle 检查。

## 技术债务削减

1.  在`BooleanArrayList`上优化`removeIf()`实现。
2.  增加了`reduceIfEmpty`对原始可迭代、`MapIterable.getIfAbsent*()`、`MultimapsTest`的测试覆盖率。
3.  尽可能使用`org.eclipse.collections.api.factory`代替`org.eclipse.collections.impl.factory`。
4.  使`primitive*HashMap.keySet()`可序列化。
5.  使`sun.misc`成为 OSGi 元数据中的可选导入包。
6.  将`primitiveSort.stg`移至 impl/utility。
7.  优化了原始不可变单体`Bag`、`Set`和`List`的收集方法。
8.  使用`forEachWithOccurrences`的箱包中`aggregateBy`的优化实施。
9.  拉起`ListIterable.binarySearch()`、`OrderedIterable.toStack()`、`RichIterable.groupByUniqueKey()`、`aggregateBy()`作为默认方法。
10.  将`with()`、`without()`、`withAll()`、`withoutAll()`作为默认方法执行。
11.  重构`PersonAndPetKatatTest`以使用更新的 API。
12.  移除重复的`forEach`覆盖。
13.  创建了一个简单的实用程序来帮助 Javadoc 的创建。
14.  更新了用于`BooleanArrayStack`代码生成的通用原语堆栈模板。
15.  使用直接公式计算`IntInterval`上的`sum()`、`mean()`和`average()`。
16.  在`IntInterval`和`Interval`上存储`size()`值。
17.  优化了快速列表和原语列表上的`toImmutable()`方法，以避免创建冗余数组副本。
18.  为`tap()`(和一个`toString()`)实现添加了`@Override`注释。

# 谢谢你

从整个 Eclipse 集合社区的所有贡献者和提交者…感谢您使用 Eclipse 集合。我们希望你喜欢 [10.3 版本](https://github.com/eclipse/eclipse-collections/releases/tag/10.3.0)中的所有新特性和改进。

*我是*[*Eclipse Collections*](https://github.com/eclipse/eclipse-collections)*OSS 项目在*[*Eclipse Foundation*](https://projects.eclipse.org/projects/technology.collections)*的项目负责人。* [*月食收藏*](https://github.com/eclipse/eclipse-collections) *开作* [*投稿*](https://github.com/eclipse/eclipse-collections/blob/master/CONTRIBUTING.md) *。如果你喜欢这个库，你可以在 GitHub 上让我们知道。*