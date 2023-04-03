# 如何在 Java 中准确使用 BigDecimal

> 原文：<https://medium.com/quick-code/how-to-use-bigdecimal-in-java-accurately-f71ae959f9da?source=collection_archive---------0----------------------->

![](img/8dd63a592863deb1413c7ce83271463f.png)

想知道为什么 float 和 double 数据类型不应该用于浮点计算吗？查看这个快速概述，了解更多关于使用 BigDecimal 执行精确运算的信息。

# 介绍

大多数企业应用程序都使用浮点值。金融科技、电子商务、金融和其他应用每天都要处理浮点运算，需要对所有计算进行完全精确的控制。
开发人员需要知道什么样的 Java 数据类型适合表示浮点值，尤其是货币值。
让我们试着弄清楚。

# 为什么 double 不准确

首先想到的是使用 float 和 double 数据类型。
但是，如果需要精确的值，不建议使用这些数据类型。事实上，float 和 double 类型主要用于科学和工程计算。
他们实现了精心设计的二进制浮点运算，以快速获得大范围数值的正确近似值。
不幸的是，这些类型不能给出准确的结果，因此不适合货币计算:

为了更深入地理解浮点数是如何表示的，请看一下 IEEE 浮点运算标准。

# BigDecimal 如何保证准确性

回到引言，让我们看看 BigDecimal 是如何表示一个数的，以及它是如何保证精度的。
查看 BigDecimal 的源代码，我们可以发现，BigDecimal 是由一个未按比例缩放的值和一个比例表示的数字:

scale 字段表示 BigDecimal 的小数位数。
未缩放值使用稍微复杂一点的表示法。
当未缩放值超过阈值时(默认为 Long。MAX_VALUE)，intVal 字段用于存储值，intCompact 字段存储 Long。MIN_VALUE，用于指示有效位信息仅可从 intVal 获得。
否则，未缩放的值被紧凑地存储在 long 类型 intCompact 字段中，用于后续计算，并且 intVal 为空。

BigDecimal 还提供了一个 scale 方法来返回 BigDecimal 的小数位数:

该方法的注释详细描述了如何使用比例值:

> *返回此 BigDecimal 的小数位数。如果为零或正数，则小数位数是小数点右边的位数。如果为负，则数字的未缩放值乘以 10 的小数位数的负幂。例如，比例-3 表示未缩放的值乘以 1000。*

回到上面的示例，无法用二进制精确表示的数字 0.61 可以用 BigDecimal 表示，其值为 61，小数位数为 2。

# 如何正确创建 BigDecimal

查看 BigDecimal 的源代码，我们可以找出四个重要的构造函数:

以上四个构造函数创建的 BigDecimal 的小数位数是不一样的。`BigDecimal(int)`和`BigDecimal(long)`更直接，取整数输入参数，所以它们的小数位数都是 0。
`BigDecimal(double)`和`BigDecimal(String)`尺度需要更细致的考虑。

# BigDecimal(double)怎么了

BigDecimal 提供了一种从 double 创建 BigDecimal 的方法，但不推荐使用这种方法，因为结果可能有些不可预测。使用双精度值 0.61 创建的 BigDecimal 不完全等于 0.61，因为 0.61 不能精确地表示为有限长度的二进制数。实际表现为:

因此，使用 BigDecimal(double ),尤其是对于涉及金融交易的计算，是极其危险的，因为它可能会失去精度。

# 使用 BigDecimal(字符串)创建

`BigDecimal(String)`构造函数是完全可预测的。第`new BigDecimal("0.61")`行创建一个 BigDecimal，它正好等于 0.61:

不过必须注意的是`new BigDecimal("0.610000")`和新`BigDecimal("0.61")`的刻度分别是 6 和 2。这些大小数的 equals 方法比较结果为假。

相应地，有两种方法可以创建能准确表示 0.61 的 BigDecimal:

方法`BigDecimal.valueOf(double)`使用两步过程，通过调用`Double.toString(double)`方法实现。第一步是将 Double 转换为 String。第二步是将 String 转换为 BigDecimal:

# 结论

由于计算机使用二进制代码来存储和处理数据，许多浮点数不能精确地表示为任何有限长度的二进制分数。
因此，通过近似，采用了一种在计算机中表示浮点数的方式，比如单精度浮点和双精度浮点格式。
不幸的是，这些类型不能给出准确的结果，不适合精确计算，尤其是影响金融交易。
因此，使用`BigDecimal(double)`是极其危险的，因为它可能会失去精度和不可预测的结果。
因此，创建 BigDecimal 的普遍首选和强烈推荐的方法是使用`BigDecimal(String)`和`BigDecimal.valueOf(double)`方法。

# 参考

[1]链接到用于浮点运算的 IEEE 标准

*原载于 2022 年 8 月 18 日*[*http://argen 666 . github . io*](https://argen666.github.io/java/2022/08/18/how-to-use-bigdecimal-in-java-accurately.html)*。*