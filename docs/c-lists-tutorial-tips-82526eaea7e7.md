# C#列表教程和技巧

> 原文：<https://medium.com/quick-code/c-lists-tutorial-tips-82526eaea7e7?source=collection_archive---------5----------------------->

![](img/3715c849274e6deec6bff0ee5531c61d.png)

Photo by [Chris Ried](https://unsplash.com/es/@cdr6934?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

列表就像数组的更好版本。
它们功能相似，但列表有更多选项。

# 列表设置:

在代码页的最上面添加:
**使用系统。集合。泛型；**

现在你可以开始添加列表了。

# 添加列表:

创建一个新的列表你写:
**List <** ( *你要用的可验证的* ) **>** ( *你的列表的名字* ) **=新列表<** ( *可验证的再一次*)**>()；**

例如:
`List<int> listName = new List<int>();`

如果你想创建一个预先填充了数字、字符、字符串等的列表
，你只需要用{}替换 **()** ，并在里面添加用逗号分隔的值。

例:
`List<int> listName = new List<int> { 1, 4, 2, 7, 5, 3, 6, 3, 8 };`

# 列表。添加:

列表。添加((*值*))；
将值添加到列表的末尾。
例如:

```
List<int> listName = new List<int> { 1, 4, 2, 7, 5, 3, 6, 3, 8 };
listName.Add(5);
```

新的列表现在将是
{ 1，4，2，7，5，3，6，3，8， **5** }。

# 列表。计数:

如果你需要得到一个列表中值的个数，那么
listName。Count 将返回一个 int。这在组合列表和查找时非常有用。

# 用 ForLoop 合并列表:

列表和 ForLoops 是一对完美的搭档，
例如**你可以"*打印"*里面的每一个值**

```
for (int i = 0; i < listName.Count; i++)
{
   Console.WriteLine(listName[i]);
}
```

**你可以用它们将列表值组合成一个字符串:**

```
List<int> listName = new List<int> { 1, 4, 2, 7, 5, 3, 6, 3, 8 };
string string1 = "";for (int i = 0; i < listName.Count; i++)
{
   string1 += listName[i];
}
Console.WriteLine(string1);
```

**你可以做一个 int，它等于列表中整数(数字)的和:**

```
List<int> listName = new List<int> { 1, 4, 2, 7, 5, 3, 6, 3, 8 };
int sum = 0;for (int i = 0; i < listName.Count; i++)
{
   sum += listName[i];
}
Console.WriteLine(sum);
```

你可以做更多的事情，只是玩和实验。

# 列表。清除:

您可以通过写入 listName 来清除 wholw 列表。clear()；

# 列表。移除:

通过写 listName。删除((*值*))；这将删除他找到的第一个匹配。
例如:

```
List<int> listName = new List<int> { 1, 4, 2, 7, 5, 3, 6, 3, 8 };
listName.Remove(3);
```

这将只删除前 3 个，
，新列表将如下所示:
{ 1，4，2，7，5，6，3，8 }。免责声明:如果它找不到匹配，它不会显示错误。