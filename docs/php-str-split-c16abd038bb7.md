# PHP 中的 split:str _ split()函数是什么？

> 原文：<https://medium.com/edureka/php-str-split-c16abd038bb7?source=collection_archive---------0----------------------->

![](img/a45b35a45a2b252df528505909b05416.png)

Split Function in PHP -Edureka

脚本语言是一种在运行时解释脚本的语言。脚本的目的通常是增强应用程序的性能或执行日常任务。这篇 PHP 中的 split 文章将按以下顺序为您提供 split 函数的替代方法的详细知识:

*   在 PHP 中拆分
*   什么是 preg_split()？
*   什么是 explode()？
*   str_split()是什么？

# 在 PHP 中拆分

PHP 5.3.0 不支持 split 函数，并且在 [PHP 7](https://www.php.net/downloads.php) 中移除了该函数。此功能还有其他选择，它们是:

*   **preg_split()**
*   **爆炸()**
*   **str_split()。**

# 什么是 preg_split()？

这是 PHP 中的一个内置函数，用于将给定的字符串转换成数组。字符串被分割成长度由用户使用该函数指定的更小的子字符串。比方说，指定了限制，然后子字符串通过数组返回，直到限制。

**语法:**

数组 preg_split(模式、主题、限制、标志)

*   **模式:**为字符串类型，用于搜索模式，否则分隔元素。
*   **subject:** 是用来存储输入字符串的变量。
*   **极限:**表示极限。如果指定了限制，则返回的子字符串必须达到限制。如果限制为 0 或-1，则表示该标志使用“无限制”。
*   **flag:**
    **PREG _ SPLIT _ NO _ EMPTY**——PREG _ SPLIT()
    **PREG _ SPLIT _ DELIM _ CAPTURE**——分隔符模式中的括号表达式也将被捕获并返回。
    **PREG _ SPLIT _ OFFSET _ CAPTURE**—对于每个出现的匹配，还将返回附加字符串偏移量。

如果你想用任意数量的逗号或空格字符分割短语:

```
<pre>
<?php $variable = preg_split("/[s,]+/", "ashok tarun, charan sabid"); print_r($variable); ?>
</pre>
```

**输出:**

```
Array
(
    [0] => ashok
    [1] => tarun
    [2] => charan
    [3] => sabid
)
```

这样，我们将一个字符串分割成**个组成字符**:

**输出:**

```
Array
(
    [0] => a
    [1] => s
    [2] => h
    [3] => o
    [4] => k
)
```

这样，我们将一个字符串分成**个匹配项**和它们的偏移量:

**输出:**

```
Array
(
    [0] => Array
        (
            [0] => ashok
            [1] => 0
        )[1] => Array
        (
            [0] => is
            [1] => 6
        )[2] => Array
        (
            [0] => a
            [1] => 9
        )[3] => Array
        (
            [0] => student
            [1] => 11
        ))
```

## **什么是 explode()？**

这是一个内置函数，它将一个字符串分解成一个数组。

**语法:**

分解(分隔符、字符串、限制)

*   **分隔符:**该字符决定要拆分的字符串。
*   **字符串:**这个输入字符串将被分割成一个数组。
*   **Limit:** 这是可选的，指定数组中元素的数量。

**举例:**

```
<pre>
<?php $var_str = 'sunday,monday,tuesday,wednesday'; print_r(explode(',',$var_str,0)); //zero limit print_r(explode(',',$var_str,2));// positive limit print_r(explode(',',$var_str,-1));// negative limit ?>
</pre>
```

**输出:**

```
Array
(
    [0] => sunday,monday,tuesday,wednesday
)
Array
(
    [0] => sunday
    [1] => monday,tuesday,wednesday
)
Array
(
    [0] => sunday
    [1] => monday
    [2] => tuesday
)
```

# str_split()是什么？

该函数用于使用**长度参数**将一个字符串拆分成一个数组。

**语法:**

str_split(string，length())。

*   **String:** 指定要拆分的字符串。
*   **Length:** 它指定了每个单独数组元素的长度。默认值为 1。

**示例:**

```
<pre>
<?php print_r(str_split("ashok",4)); ?>
</pre>
```

**输出:**

```
Array
(
    [0] => asho
    [1] => k
)
```

本文到此结束，我希望你已经了解了所有三个 split 函数 preg_split()、explode()、str_split()和示例。如果你想查看更多关于人工智能、DevOps、道德黑客等市场最热门技术的文章，那么你可以参考 [Edureka 的官方网站。](https://www.edureka.co/blog/?utm_source=medium&utm_medium=content-link&utm_campaign=php-str-split)

请留意本系列中的其他文章，它们将解释 PHP 的各个方面。

> 1. [PHP 教程](/edureka/php-tutorial-beginners-guide-to-php-f78a189de6f)
> 
> 2.[PHP 中如何解密 md5 密码？](/edureka/decrypt-md5-password-php-c9cb0f927922)

*原载于 2019 年 7 月 16 日 https://www.edureka.co**的* [*。*](https://www.edureka.co/blog/php-str-split/)