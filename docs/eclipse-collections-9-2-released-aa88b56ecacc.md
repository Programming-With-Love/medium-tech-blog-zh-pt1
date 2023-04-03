# 发布了 Eclipse 集合 9.2

> 原文：<https://medium.com/oracledevs/eclipse-collections-9-2-released-aa88b56ecacc?source=collection_archive---------3----------------------->

新功能，更对称的同情和社区贡献。

![](img/696af97aed286bdec793efd0a2ea2b11.png)

Good symmetry at St. Paul’s

> [Eclipse Collections](https://github.com/eclipse/eclipse-collections) 是一个 Java 的集合框架。它用丰富流畅的 API 优化了列表、集合和映射实现。该库提供了 JDK 中没有的附加数据结构，如 Bags、Multimaps 和 BiMaps。该框架还提供了列表、集合、包、栈和地图的原始版本，具有丰富而流畅的 API。支持库中所有容器的可变和不可变版本。

有九个开发人员对 Eclipse 集合 9.2.0 版本做出了贡献。我要感谢每一个做出贡献的人。如果这是您对开源项目和/或 Eclipse 集合的第一次贡献，那么祝贺并欢迎您！

Eclipse 集合 9.2.0 的发行说明在这里是。

# 特色，特色，特色！

我们都喜欢框架中的新特性，尤其是我们日常使用的新特性。自从我们在 Eclipse Collections 8.0 中开始使用 Java 8 进行编译以来，我们已经享受到了通过使用默认方法在小版本中添加新 API 的新灵活性。下面是 Eclipse 集合 9.2 中添加的一些新的 API。

## 欢迎:flatCollectWith

对称性改进的第一个故事以`flatCollectWith` 的形式到达`RichIterable`。从框架的 1.0 版本开始，我们就有了`flatCollect`。我们也有过`collect`和`collectWith`T23。现在我们改进了对称性，同时拥有`flatCollect`和`flatCollectWith`。下面是一个使用`flatCollectWith`的代码示例。

```
@Test
public void flatCollectWith()
{
    MutableList<Integer> list = 
            Lists.***mutable***.with(5, 4, 3, 2, 1);
    MutableList<Integer> result = 
            list.flatCollectWith(Interval::*fromTo*, 1);

    MutableList<Integer> expected = *mList*(
            5, 4, 3, 2, 1,
            4, 3, 2, 1,
            3, 2, 1,
            2, 1,
            1);
    Assert.*assertEquals*(expected, result);
}
```

## 欢迎:toSortedMapBy

我们的第二个对称故事以`toSortedMapBy` 的形式到达了`RichIterable`。我们从 6.0 开始就有了`toSortedBagBy`。从 1.0 开始，我们不得不使用`toSortedListBy`和`toSortedSetBy`。

```
@Test
public void toSortedMapBy()
{
    MutableList<Integer> list =
            Lists.***mutable***.with(4, 3, 2, 1);
    MutableSortedMap<Integer, Integer> map =
            list.toSortedMapBy(i -> i % 2, k -> k, v -> v);
    Assert.*assertEquals*(
            SortedMaps.***mutable***.with(
                    Comparators.*byFunction*(i -> i % 2),
                    4, 2, 3, 1),
            map);
}
```

## 欢迎:bag . select 重复项

从 3.0 开始，我们在`Bag`上已经有了`selectByOccurrences`。现在我们有了一个选择所有大于 1 的事件的捷径。

```
@Test
public void selectDuplicates()
{
    MutableBag<Integer> bag =
            Bags.***mutable***.with(1, 2, 2, 3, 3, 3);
    MutableBag<Integer> duplicates =
            bag.selectDuplicates();

    Assert.*assertEquals*(
            Bags.***mutable***.with(2, 2, 3, 3, 3),
            duplicates);
}
```

## 欢迎:Bag.selectUnique

到`selectDuplicates`的对称方法是`selectUnique`。与`selectDuplicates`返回一个`Bag`不同，`selectUnique`返回一个`Set`。

```
@Test
public void selectUnique()
{
    MutableBag<Integer> bag =
            Bags.***mutable***.with(1, 2, 2, 3, 3, 3);
    MutableSet<Integer> unique =
            bag.selectUnique();

    Assert.*assertEquals*(
            Sets.***mutable***.with(1),
            unique);
}
```

## 欢迎:chunk(用于原始集合)

这幅画的对称性很强。从 1.0 版本开始，我们就有了用于对象集合的`chunk`。

```
@Test
public void chunk()
{
    IntList list = IntInterval.*oneTo*(10);
    RichIterable<IntIterable> chunks = list.chunk(2);

    Verify.*assertSize*(5, chunks);
    String string = chunks.makeString(**"/"**);
    Assert.*assertEquals*(
            **"[1, 2]/[3, 4]/[5, 6]/[7, 8]/[9, 10]"**,
            string);
}
```

## 欢迎:newEmpty(用于原始集合)

从 1.0 开始，我们的对象集合就有了`newEmpty`。方法`newEmpty`给出了相同集合类型的空版本。现在我们用同样的方法处理原始集合。

```
@Test
public void newEmpty()
{
    MutableIntList empty1 = IntLists.***mutable***.empty();
    MutableIntList empty2 = empty1.newEmpty();

    Assert.*assertEquals*(empty1, empty2);
    Assert.*assertNotSame*(empty1, empty2);
}
```

## 欢迎:OrderedMapAdapter

这是某种对称性的缺失。我们从 1.0 开始就有了`List`、`Set`、`SortedSet`、`Collection`、`Map`的适配器。虽然 Java 中没有`OrderedMap`接口，但是有一个`OrderedMap`实现，叫做`LinkedHashMap`。你总是可以把一个`LinkedHashMap`改编成一个`MutableMap`。现在你可以对它进行修改，得到一个`OrderedMap`或者一个`MutableOrderedMap`。当然，我们最终想要在`RichIterable`上看到的下一个东西是`toOrderedMap`。

```
@Test
public void orderedMapAdapter()
{

    MutableList<Integer> keys = Lists.***mutable***.with(3, 2, 1);
    MutableOrderedMap<Object, Object> orderedMap =
            OrderedMaps.*adapt*(
                    keys.injectInto(
                            new LinkedHashMap<>(),
                            (map, each) -> {
                                map.put(each, each);
                                return map;
                            }));

    LinkedHashMap<Object, Object> expected = new LinkedHashMap<>();
    expected.put(3, 3);
    expected.put(2, 2);
    expected.put(1, 1);

    Assert.*assertEquals*(expected, orderedMap);
    Assert.*assertEquals*(keys, orderedMap.keysView().toList());
    Assert.*assertEquals*(Interval.*fromTo*(3, 1), orderedMap.toList());
}
```

**更新:**这里有一个使用`forEachWithIndex`来填充`orderedMap`的例子。

```
@Test
public void orderedMapAdapter()
{
    MutableOrderedMap<Integer, Integer> orderedMap =
            OrderedMaps.*adapt*(new LinkedHashMap<>());

    MutableList<Integer> keys = Lists.***mutable***.with(3, 2, 1);
    keys.forEachWithIndex(orderedMap::put);

    Map<Object, Object> expected = new LinkedHashMap<>();
    expected.put(3, 0);
    expected.put(2, 1);
    expected.put(1, 2);

    Assert.*assertEquals*(expected, orderedMap);
    Assert.*assertEquals*(keys, orderedMap.keysView().toList());
    Assert.*assertEquals*(Interval.*zeroTo*(2), orderedMap.toList());
}
```

## 欢迎:toStringOfItemToCount(针对原始包)

从 3.0 版本开始，我们就在对象包上提供了这种方法。现在`toStringOfItemToCount`可用于所有的原语`Bag`实现。

```
@Test
public void toStringOfItemToCount()
{
    MutableIntBag bag = IntBags.***mutable***.with(1, 2, 2, 3, 3, 3);
    String string = bag.toStringOfItemToCount();

    Assert.*assertEquals*(**"{1=1, 2=2, 3=3}"**, string);
}
```

## 欢迎:Collectors2 上的两个新收藏者

我们现在在`Collectors2`上有`aggregateBy`和`countByEach`。方法`aggregateBy`使我们在`Collectors2`上对称，同样的方法在`RichIterable`上对称。另一方面，方法`countByEach`目前在`RichIterable` API 中没有等价的实现。这种方法本质上是实验性的，如果证明足够有用，我们可能会在未来的版本中把它作为一个特性添加到`RichIterable`中。

```
@Test
public void countByEach()
{
    MutableBag<Integer> bag = Arrays.*asList*(5, 4, 3, 2, 1)
            .stream()
            .collect(Collectors2.*countByEach*(Interval::*oneTo*));

    MutableBag<Integer> expected = Bags.***mutable***.empty();
    expected.addOccurrences(5, 1);
    expected.addOccurrences(4, 2);
    expected.addOccurrences(3, 3);
    expected.addOccurrences(2, 4);
    expected.addOccurrences(1, 5);

    Assert.*assertEquals*(expected, bag);
}
```

## 还有更多…

看看[发行说明](https://github.com/eclipse/eclipse-collections/releases/tag/9.2.0)中列出的其他一些特性和改进。我们期待在未来看到更多的 Eclipse 集合贡献者。

享受在 Java 项目中使用 Eclipse Collections 9.2 和所有新特性的乐趣吧！

*我是*[*Eclipse Collections*](https://github.com/eclipse/eclipse-collections)*OSS 项目在*[*Eclipse Foundation*](https://projects.eclipse.org/projects/technology.collections)*的项目负责人和提交人。* [*月食收藏*](https://github.com/eclipse/eclipse-collections) *为* [*投稿*](https://github.com/eclipse/eclipse-collections/blob/master/CONTRIBUTING.md) *打开。如果你喜欢这个库，你可以在 GitHub 上让我们知道。*