# C# For 循环教程&技巧

> 原文：<https://medium.com/quick-code/c-for-loop-tutorial-tips-3358ae374781?source=collection_archive---------8----------------------->

当您想要多次重复一项任务时，可以使用 For 循环。

![](img/eb8892cb45e94add8d9b4bf16edcb56a.png)

Photo by [Javier Garcia Chavez](https://unsplash.com/@javchz?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

# 简单 For 循环:

例如，如果您想要多次运行同一任务，
您可以创建一个 For 循环，该循环将一直运行到您在 if 语句中指定的次数:

```
for (int i = 0; i < (**length**); i++)
{
   (**Task**)
}
```

这将运行(**任务** ) ( **长度**)次。

# For 循环的提示:

您可以在 for 循环中使用"**I**",
它在许多场景中都很有用，例如:
您可以显示任务已经运行的回合或次数。

```
for (int i = 1; i <= 4; i++)
{
    Console.WriteLine(“This is round “ + i);
}
```

这个 for 循环将运行 4 次，并显示每次运行的回合。

**输出:** 这是第 1 轮
这是第 2 轮
这是第 3 轮
这是第 4 轮

您也可以将它用于数组或列表

```
int[] numbers = { 1, 4, 2, 7, 3 };
for (int i = 0; i < numbers.Length; i++)
{
   Console.WriteLine(numbers[i]);
}
```

**输出:** 1
4
2
7
3

你也可以把一个列表分成两个，
对于这个你需要有

```
using System.Collections.Generic;
```

示例:

```
List<int> firstArray = new List<int>{ 1, 4, 2, 7, 5, 3, 6, 3, 8};
List<int> secondArray = new List<int>();
List<int> thirdArray = new List<int>();for (int i = 0; i < firstArray.Count; i++)
{
   if (i % 2 == 0)
   {
      secondArray.Add(firstArray[i])
   } if (i % 2 == 1)
   {
      thirdArray.Add(firstArray[i]);
   }
}
```