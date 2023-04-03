# 我们来回答一下:什么是数据结构？

> 原文：<https://medium.com/quick-code/what-is-data-structures-e9553f3bb949?source=collection_archive---------0----------------------->

## 让我们来回答第一个问题

## 让我们一起揭开数据结构的神秘面纱

![](img/c57b2c4768d11a8976ef44ff4cafa41d.png)

Photo by [Blake Connally](https://unsplash.com/@blakeconnally?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

## 什么是数据结构？

*简单来说*

> 数据结构是在运行时内存中存储数据的方式，以便有效地使用数据。

例如:假设给你一个由 10 个数字组成的列表，要求你按升序排列这 10 个数字。每行一个数字格式的输入。下面提到的是一些方法。

```
**Approach 1:**
**Store each number in a different integer and manually compare the values.**
This will be done in constant time complexity, if you are able to write the code because there are 3628800 or 10 factorial *if conditions to be coded. This is not practically possible.*
```

```
**Approach 2:**
**Store the list of numbers in an array.**
This will estalish a link between all the numbers and we will be able to rearrange the numbers in ascending order. The worst time complexity could be O(n^2)and can be optimized to O(nlog n). There are various algorithm for sorting the numbers. Some of them are Bubble Sort, Seletion Sort, Merge Sort, quick Sort and many more. This is one of the most popular ways of solving the problem.
```

```
**Approach 3:**
**Storing the elements in a Binary Search Tree.** Another less popular way of sorting numbers is to store the integers in a binary search tree and traverse the tree in a pre-order manner. The worst time complexity is O(n^2).
```

## 为什么数据结构很重要？

数据结构很重要，因为它增加了

*   **效率**:使用适当的数据结构将使程序员能够编写消耗更少时间和空间的代码，因此是内存友好的。
*   **可重用性**:使用标准数据结构使开发者能够创建特定于数据结构的库，从而使程序员能够抽象出在各种数据结构上执行的基本操作。

## 数据结构的基本操作

有 6 种基本操作可以在任何数据结构上执行。它们是:

*   **搜索**:查找数据结构中的一个元素。
*   **排序**:按升序或降序重新排列所有元素。
*   **遍历**:以特定方式打印所有元素。
*   **插入**:在数据结构的任意位置插入一个元素。
*   **删除**:从数据结构中删除任何元素。
*   **更新**:更新数据结构中的任意元素。

## 数据结构的类型

大体上有两种类型的数据结构:

*   线性数据结构
*   非线性数据结构

## 线性数据结构

那些以线性方式(即一个接一个或简单地以一维方式)存储数据的数据结构被称为线性数据结构。

它们也有两种类型:

*   静态:数据结构一旦声明，其大小就不能改变。例如:数组。
*   动态:即使在声明之后，数据结构的大小也可以改变。例如:链表。

**非线性数据结构**

那些以多维方式存储数据的数据结构被称为非线性数据结构。

它们也有两种类型:

*   图表
*   哈希表

![](img/d5d07df165127c6747924f9b0de88cdc.png)

A diagram to explain the types of Data Structures. Picture from: GeeksForGeeks.com

文章中提到的每个数据结构都有不同的子类型。

每种数据结构都将在不同的文章中与实践问题和常用算法一起深入讨论。

请继续关注更多这样的文章，如果你想要某个特定主题的文章，请给我发邮件。