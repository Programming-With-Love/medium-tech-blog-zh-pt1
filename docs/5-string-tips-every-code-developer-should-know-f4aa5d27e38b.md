# 每个代码开发人员都应该知道的 5 个字符串提示

> 原文：<https://medium.com/quick-code/5-string-tips-every-code-developer-should-know-f4aa5d27e38b?source=collection_archive---------7----------------------->

这是用 C#编写字符串代码的 5 个基本技巧。

![](img/6566826cdf5352a777dbe57f31d7a117.png)

Photo by [Clément Hélardot](https://unsplash.com/@clemhlrdt?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

# 1#向字符串添加变量更容易:

在字符串的开头添加' $ '将允许您添加整型、浮点型、双精度型和布尔型，只需在里面写{}和变量。

假设你有一个变量叫 points，你想向用户展示他有多少分，你可以写:
Console。WriteLine($“你得到了{points}点)；
而不是写:
控制台。WriteLine("你得到了"+points+" points ")；

# 2#向字符串添加变量更容易第 2 部分:

你可以写{}，里面写数字从 0 开始往上，
在你关闭字符串之后，你添加变量，用逗号分隔。
T5 例如:
int int 1 = 12；
bool bool 1 = false；
控制台。WriteLine("int 1- {0}，bool 1- {1})。，int1，bool 1)；
**代替**
控制台。WriteLine("int 1- " + int1 +"，bool 1- " + bool1 +"。");

# 3#将字符串添加到字符串:

可以像加数字一样给其他字符串加一个字符串:
string string1 = "句子一，"；
string string2 = "句子二"；
string 1+= string 2；

string1 现在是——“句子一，句子二”。

# 4#将字符数组转换为字符串:

如果你有一个字符数组，你可以把它们转换成一个字符串:
char[] chars = { 'w '，' o '，' r '，' d ' }；
string string1 =新字符串(chars)；

string1 现在是——“word”。

# 5#重复一个字符:

要重复同一个字符，只需写下字符和你想要重复的次数:
string string1 = new string('d '，7)；

string1 现在是——“dddddd”。