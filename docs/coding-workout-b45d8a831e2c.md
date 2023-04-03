# 编码训练

> 原文：<https://medium.com/globant/coding-workout-b45d8a831e2c?source=collection_archive---------1----------------------->

## 提高团队硬技能

![](img/f7a4824cfb60cddd371c6d641b895dcc.png)

Photo by [Edgar Chaparro](https://unsplash.com/@echaparro?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

要达到精通，练习是最好的。编码没有什么不同，所以每个开发人员都应该定期做一些练习。

练习和采用解决问题的心智能力的更好方法是通过解决问题。但是什么样的问题呢？

编码人员可以用 [katas](https://en.wikipedia.org/wiki/Kata) 来练习和提高他们的编码技能。编码形是由迪夫·托马斯创造的，他是“[实用主义程序员”“](https://www.goodreads.com/book/show/4099.The_Pragmatic_Programmer)的合著者，来自日本武术，战士们一遍又一遍地练习他们的动作，直到他们精通为止。

编码形解决了一个简单的问题，但是让开发人员共享不同版本的解决方案，并学习新的方法来提高效率和性能。

我们开发人员喜欢变化和不断学习新事物。周五是和团队一起表演编码形的绝佳时机；临近周末的时候可以放松一点，享受学习的乐趣，鼓励他们在接下来的一周练习新知识。

接下来，我将展示一些用 Javascript 练习编码技巧的形练习。目标是获得问题解决方案的更好版本。

一些建议:

1.  理解练习。
2.  检查测试标准。输入是什么？产量是多少？
3.  制作符合测试标准的第一个版本。
4.  改进版本以学习新概念。

## 创建假数据集

一个程序给定一个数字 N 输入，返回一个 [JSON](https://es.wikipedia.org/wiki/JSON) 数组大小 N 或随机对象，其属性如姓名和电话号码具有一致的值。不能使用任何库或 npm 包。

```
Input: Number of records to generate (N)
Output: JSON array with N different records 
```

目标:使用数组、[单责任原则](https://en.wikipedia.org/wiki/Single-responsibility_principle)，并从几个种子中获取数据。

解决方案的一个版本:

One solution version with random functions and Array.from method

## 在另一个数组中搜索数组元素

当输入数组(1，000 个元素)中的 id 匹配时，从另一个大数组(100 万个元素)数据集中返回一个具有 id 和名称的数组的程序。

```
Input: 
    - 1,000,000 elements array with {name, phone, id}
    - 1,000 elements array with idsOutput: JSON array with matched elements 
```

目标:数量级算法、[时间复杂性概念](https://en.wikipedia.org/wiki/Time_complexity)、使用哈希表提高性能，以及[映射](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Map)对象使用。

**解决方案 1:** O(MN)使用数组方法搜索，如果 M 是大数组大小，N 是小数组大小。

结果:

```
Search array elements in a big array
Size of data: 1,000,000
Size of elements to search: 1,000
[...]
Memory usage: 183 MB
Time of process: 1.308s
```

**方案二:** O(M)+O(N)使用地图对象搜索；O(M)是地图创建，O(N)是搜索。

结果:

```
Search array elements in a a big data Map
Size of data: 1,000,000
Size of elements to search: 1,000
[...]
Memory usage: 310 MB
Time of process: 345.812ms
```

现在，如果我们运行这几个程序，在一个一百万元素的数组中搜索一万个元素。结果会更有意义。

**解 1 结果:** O(MN)搜索

```
Search array elements in a a big data Map
 Size of data: 1,000,000
 Size of elements to search: 10,000
[...]
Memory usage: 186 MB
Time of process: 12.710s
```

**方案二:** O(M)+O(N)搜索

```
Search array elements in a a big data Map
Size of data: 1,000,000
Size of elements to search: 10,000
[...]
Memory usage: 310 MB
Time of process: 346.414ms
```

# 结论

在软件工程团队中，编码卡塔是促进知识转移和卓越工作的非常好的工具。技术领导和工程经理可以领导这种练习，作为团队硬技能成熟过程的一部分。

根据你的团队的需要，准备你的目标明确的形。一些有用的问题建议链接如下:

*   [牛逼的武士道](https://github.com/gamontal/awesome-katas)
*   [形对数](https://kata-log.rocks/pair-programming)
*   [编码道场](https://codingdojo.org/kata/)