# 流比迭代方法更好吗(例如对于循环)？

> 原文：<https://medium.com/walmartglobaltech/is-stream-better-than-an-iterative-approach-like-for-loop-624481d8ccae?source=collection_archive---------2----------------------->

![](img/69e1e6bb4a0f38d270082529f1a38faa.png)

Source: [https://www.incimages.com/uploaded_files/image/1920x1080/getty_506903004_200013332000928076_348061.jpg](https://www.incimages.com/uploaded_files/image/1920x1080/getty_506903004_200013332000928076_348061.jpg)

在当今数据丰富和集成的世界中，我们经常与流打交道，尤其是如果您是 java 程序员。然而，我们当中有多少人(java 程序员)知道流的内部工作方式，或者对流是否比简单的 **For** 循环更好有什么看法？

如果你知道这些事情，那么我相信这个博客可能与你无关。如果没有，那我们再深入挖掘一下。

既然我问了你这个问题，那么让我们从每个循环的**内部工作开始。
***for(int x:list)***
这是一个包裹了内部迭代代码的糖衣行。**

```
*Iterator<Integer> iterator = list.iterator();
 while(iterator.hasNext()){
 Integer x=iterator.next();
 }*
```

这就是当我们为循环调用**时所发生的情况。这背后的逻辑是迭代器是在你的应用程序代码上创建的，对于每次迭代，它将去收集并收集它需要的任何东西，然后返回到应用程序代码。
如果我们使用 For 循环，就不可能以并行方式处理集合的元素。使用 For 循环，不容易实现在其上执行多个操作的东西。例如，如果你想把一个列表的元素改为大写，从中过滤出一些东西，然后把它存储在一个列表中，那么这是很有挑战性的。**

```
List<String> updatedList=new ArrayList<>();
for(String value:list){
    String valueInUpperCase = value.toUpperCase();
    if(isMatching(valueInUpperCase)){
        updatedList.add(valueInUpperCase);
    }
}
```

所有这些困难都存在，因为迭代是在代码上完成的，而不是在集合上。这里 stream 带有简单的逻辑，迭代将在内部通过集合完成，而不是在应用程序代码上。用技术术语来说，这是内部迭代，而 for 循环是应用程序代码的外部迭代。这种逻辑将缓解上述两个问题。由于流是在集合内部进行迭代，因此可以快速并行处理，因为一个元素不依赖于另一个元素，而且对于流，我们可以很容易地进行多个操作。

对于同一个示例，单个 For 循环很难做到，我们可以调用 streams 并将其转换为大写，然后根据需要进行过滤。

```
*list.stream().map(x ->x.toUpperCase()).filter(ismatching()).collect(Collectors.toList());* 
```

与 For 循环实现相比，它的可读性更好。

考虑到所有这些因素，您可能会认为我们应该从 java 中移除 For 循环，在任何地方都使用 streams。然而，事实并非如此。溪流也有它的缺点。

> **性能** - >当然，就性能而言，简单的 For 循环比 stream 要好，因为 streams 有相当多的开销。
> **可读性** - >一个简单的 for 循环我们早就熟悉了，和 For 循环比起来，streams 也没那么老。这是我们认为简单 For 循环可读性更强的原因之一，在某种程度上，这是正确的。例如，当我们在循环中使用 2，一个在另一个里面，它是可读的。然而，当我们必须在流中做同样的事情时，你会觉得它很复杂。所以我认为这完全取决于我们对溪流的熟悉程度。简单的 For 循环比流更容易调试，因为传统的调试器没有配备流。

总之，我觉得 streams 和 For loop 各有所长，我们开发人员需要根据自己的需求考虑使用哪一个。我们需要做好相关的权衡，并决定何时使用流，何时进行 for 循环。