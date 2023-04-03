# 荷兰国旗算法

> 原文：<https://medium.com/quick-code/dutch-flag-algorithm-3669af2b14fd?source=collection_archive---------0----------------------->

## 让我们来回答第三个问题

荷兰国旗算法简介。

![](img/72ee3a52432268da75001a4aee124ef6.png)

Photo by [Markus Spiske](https://unsplash.com/@markusspiske?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

荷兰旗算法(DFA)是数组最基本也是最重要的算法之一。它用于分离一个由线性时间复杂度的 3 个数组成的数组。

DFA 最坏的时间复杂度是 O(n ),算法的空间复杂度是 O(1)。问题陈述如下:

> 向您提供一个由 0、1 和 2 组成的数组。任务是编写一个函数，将所有的数字分离在一起。顺序可以是任何东西。

让我们假设提供的数组 A 是
A = [1，2，0，1，2，0，1，2，0]
，期望的输出是
A = [2，2，2，0，0，0，1，1]

要解决这个问题，我们需要首先选择订单。对于本例，我们选择 2，1，0。
现在我们需要 3 个指针，分别是 **start，end，**和 **P** ，指向数组的三个不同的索引。

**开始**将表示中间元素的第一个索引，即这里的 0。**端**将表示中间元素的最后一个索引，指针 **P** 将用于遍历数组。

当 **P** 不等于 **End 时，循环将运行。**当 P 遍历时，如果 P 遇到 2，它将与 **start** 交换并递增 start。同样，如果 P 遇到 1，它将与 end 交换，并递减 end。代码如下:-

为了更好地理解该算法，在纸上写下堆栈跟踪，并确保完成所有步骤。

如果您有任何疑问，或者希望我介绍任何其他算法，请随意评论这篇文章。

请继续关注更多这样的算法和概念。