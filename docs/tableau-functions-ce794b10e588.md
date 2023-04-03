# 了解 Tableau 中的函数以及如何使用它们

> 原文：<https://medium.com/edureka/tableau-functions-ce794b10e588?source=collection_archive---------1----------------------->

![](img/b54cd3cd4a5896911cef0e1cd91256da.png)

Functions In Tableau — Edureka

***Tableau*** 是一个工具，不仅仅是为了漂亮的图形。***Tableau***中的函数对于最佳数据表示至关重要。幸运的是，这个工具有各种类别的内置函数，您可以直接应用到您上传的数据。如果你用过 ***MS Excel*** 或者 ***SQL*** ，这些应该对你来说很熟悉。

所以，下面是我们将在这个博客中讨论的各种功能。

*   **数字功能**
*   **字符串函数**
*   **日期功能**
*   **类型转换功能**
*   **聚合函数**
*   **逻辑功能**

# 数字函数

Tableau 中的这些内置函数允许您对字段中的数据值执行计算。数字函数只能用于包含数值的字段。以下是 Tableau 中的各种数字函数。

## 1.防抱死制动装置

这个函数返回给定数字的绝对值。

***语法***

`ABS(number)`

`ABS(-4) = 4`

## 2.应用控制操作系统

该函数返回给定弧度数的反余弦值。

***语法***

`ACOS(number)`

`ACOS(-1) = 3.14159265358979`

## 3.印度历的 7 月

该函数返回给定弧度数的反正弦值。

***语法***

`ASIN(number)`

`ASIN(1) = 1.5707963267949`

## 4.阿坦

该函数返回给定弧度数的反正切值。

**语法**

`ATAN(number)`

`ATAN(180) = 1.5652408283942`

## 5.天花板

此函数返回给定的四舍五入到最接近的等于或大于该值的整数。

**语法**

`CEILING(number)`

`CEILING(3.1415) = 4`

## 6.装货付款(Cash On Shipment)

该函数返回以弧度表示的给定角度的余弦值。

**语法**

`COS(number)`

`COS(PI()/4) = 0.707106781186548`

## 7.小屋

该函数返回以弧度表示的给定角度的余切值。

**语法**

`COT(number)`

`CO1(PI()/4) = 1`

## 8.度

这个函数返回给定角度的值，以度为单位。

**语法**

`DEGREES(number)`

`DEGREES(PI()/4) = 45`

## 9.差异

给定被除数和除数，此函数返回商的整数值。

**语法**

`DIV(integer1, integer2)`

`DIV(11,2) = 5`

## 10.经历

该函数返回 e 的给定幂的值。

**语法**

`EXP(number)`

`EXP(2) = 7.389
EXP(-[Growth Rate]*[Time])`

## 11.地面

该函数返回给定的四舍五入到最接近的整数的相等或更小的值。

**语法**

`FLOOR(number)`

`FLOOR(6.1415) = 6`

## 12.六邻体蛋白 X，Y

HEXBINX 和 HEXBINY 是六角仓的宁滨和绘图函数。此函数将 x，y 坐标映射到最近的六边形容器的 x 坐标。箱的边长为 1，因此可能需要对输入进行适当的缩放。

**语法**

`HEXBINX(number, number)`

`HEXBINX([Longitude], [Latitude])`

## 13.LN

这个函数返回给定数字的自然对数。

**语法**

`LN(number)`

`LN(1) = 0`

## 14.原木

该函数返回给定数字的以 10 为底的对数。

**语法**

`LOG(number, [base])`

`LOG(1) = 0`

## 15.马克斯(男子名ˌ等于 Maximilian)

该函数返回传递参数的最大值。

**语法**

`MAX(number, number)`

`MAX(4,7) = 7`
`MAX(Sales,Profit)`

## 16.部

该函数返回传递的参数的最小值。

**语法**

`MIN(number, number)`

`MIN(4,7) = 4`
`MIN(Sales,Profit)`

## 17.产品改进(Product Improve)

这个函数返回圆周率的值。

**语法**

`PI() = 3.142`

## 18.力量

该函数返回第一个参数的值乘以第二个参数的幂。

**语法**

`POWER(number, power)`

`POWER(2,10) = 1024`

## 19.弧度

该函数返回给定角度的弧度值。

**语法**

`RADIANS(number)`

`RADIANS(45) = 0.785397`

## 20.轮次

该函数返回舍入到指定小数位数的给定数字。

**语法**

`ROUND(number, [decimal place])`

`ROUND([Profit])`

## 21.符号

这个函数返回一个给定数字的符号。

**语法**

`SIGN(number)`

`SIGN(AVG(Profit)) = -1`

## 22.犯罪

该函数返回以弧度表示的给定角度的正弦值。

**语法**

`SIN(number)`

`SIN(PI()/4) = 0.707106781186548`

## 23.平方根计算

这个函数返回给定数字的平方根。

**语法**

`SQRT(number)`

`SQRT(25) = 5`

## 24.平方

这个函数返回给定数字的平方。

**语法**

`SQUARE(number)`

`SQUARE(5) = 25`

## 25.黝黑色

该函数返回以弧度表示的给定角度的正切值。

**语法**

`TAN(number)`

`TAN(PI()/4) = 1`

# **字符串函数**

Tableau 中的这些内置函数允许您操作字符串数据。您可以使用这些函数做一些事情，比如将所有客户的所有姓氏提取到一个新字段中。以下是 Tableau 中的各种字符串函数；

## 1.美国信息交换标准代码

这个函数返回所述字符串的第一个字符的 ASCII 码。

**语法**

`ASCII(string)`

`ASCII('A') = 65`

## 2.茶

这个函数返回由 ASCII 码表示的字符。

**语法**

`CHAR(ASCII code)`

`CHAR(65) = 'A'`

## 3.包含

如果字符串包含所述子串，该函数返回 true。

**语法**

`CONTAINS(string, substring)`

`CONTAINS(“Edureka”, “reka”) = true`

## 4.末端

给定以所述子串结尾的字符串，该函数返回 true。

**语法**

`ENDSWITH(string, substring)`

`ENDSWITH(“Edureka”, “reka”) = true`

## 5.发现

如果字符串包含所述子串，则该函数返回子串在字符串中的索引位置，否则为 0。如果添加了可选参数 start，该函数将忽略出现在索引位置 start 之前的子字符串的任何实例。

**语法**

`FIND(string, substring, [start])`

`FIND(“Edureka”, “reka”) = 4`

## 6.芬恩斯

如果字符串包含所述子串，该函数返回该子串在字符串中第 n 次出现的索引位置。

**语法**

`FINDNTH(string, substring, occurrence)`

`FIND(“Edureka”, “e”, 2) = 5`

## 7.左边的

该函数返回给定字符串中最左边的字符数。

**语法**

`LEFT(string, number)`

`LEFT(“Edureka”, 3) = "Edu"`

## 8.低输入联网（low-entry networking 的缩写）

这个函数返回给定字符串的长度。

**语法**

`LEN(string)`

`LEN(“Edureka”) = 7`

## 9.降低

这个函数以小写字母返回整个给定的字符串。

**语法**

`LOWER(string)`

`LOWER(“Edureka”) = edureka`

## 10.LTRIM

这个函数返回给定的字符串，前面没有任何空格。

**语法**

`LTRIM(string)`

`LTRIM(“ Edureka ”) = "Edureka "`

## **11。最大值**

该函数返回两个传递的字符串参数中的最大值。

**语法**

`MAX(a, b)`

`MAX ("Apple","Banana") = "Banana"`

## 12.中间的

这个函数从起始的索引位置返回给定的字符串。

**语法**

`MID(string, start, [length])`

`MID("Edureka", 3) = "reka"`

## 13.部

该函数返回两个传递的字符串参数中的最小值。

**语法**

`MIN(a, b)`

`MIN ("Apple","Banana") = "Apple"`

## 14.替换

此函数在给定的字符串中搜索子字符串，并用替换项替换它。

**语法**

`REPLACE(string, substring, replacement)`

`REPLACE("Version8.5", "8.5", "9.0") = "Version9.0"`

## 15.正确

该函数返回给定字符串中最右边的字符数。

**语法**

`RIGHT(string, number)`

`RIGHT(“Edureka”, 3) = "eka"`

## 16.RTRIM

这个函数返回给定的字符串，后面没有空格。

**语法**

`RTRIM(string)`

`RTRIM(“ Edureka ”) = " Edureka"`

## 17.空间

该函数返回由指定数量的空格组成的字符串。

**语法**

`SPACE(number)`

`SPACE(1) = " "`

## 18.使分离

这个函数从一个字符串中返回一个子字符串，使用一个分隔符将字符串分成一系列标记。

**语法**

`SPLIT(string, delimiter, token number)`

`SPLIT (‘a-b-c-d’, ‘-‘, 2) = ‘b’
SPLIT (‘a|b|c|d’, ‘|‘, -2) = ‘c’`

## 19.开始于

给定以所述子串开始的字符串，该函数返回 true。

**语法**

`STARTSWITH(string, substring)`

`STARTSWITH(“Edureka”, “Edu”) = true`

## 20.整齐

这个函数返回给定的字符串，前后没有空格。

**语法**

`TRIM(string)`

`TRIM(“ Edureka ”) = "Edureka"`

## 21.上面的

这个函数以大写字母返回整个给定的字符串。

**语法**

`UPPER(string)`

`UPPER(“Edureka”) = EDUREKA`

# **日期功能**

Tableau 中的这些内置函数允许您操作数据源中的日期，如年、月、日、日和/或时间。以下是 Tableau 中的各种日期函数:

## 1.DATEADD

此函数返回指定日期，指定日期的一部分加上指定的数字间隔。

**语法**

`DATEADD(date_part, interval, date)`

`DATEADD('month', 3, #2019-09-17#) = 2019-12-17 12:00:00 AM`

## 2.DATEDIFF

此函数返回以日期部分的单位表示的两个日期之间的差值。一周的开始可以根据用户的需要进行调整。

**语法**

`DATEDIFF(date_part, date1, date2, [start_of_week])`

`DATEDATEDIFF('week', #2019-12-15#, #2019-12-17#, 'monday')= 1`

## 3.日期名称

该函数以字符串形式返回日期的日期部分。

**语法**

`DATENAME(date_part, date, [start_of_week])`

`DATENAME('month', #2019-12-17#) = December`

## 4.日期部分

该函数以整数形式返回日期的日期部分。

**语法**

`DATEPART(date_part, date, [start_of_week])`

`DATEPART('month', #2019-12-17#) = 12`

## 5.DATETRUNC

此函数以日期部分指定的精度返回指定日期的截断形式。通过这个函数，您基本上可以返回一个新的日期。

**语法**

`DATETRUNC(date_part, date, [start_of_week])`

`DATETRUNC('quarter', #2019-12-17#) = 2019-07-01 12:00:00 AM
DATETRUNC('month', #2019-12-17#) = 2019-12-01 12:00:00 AM`

## 6.天

该函数以整数形式返回给定日期的第几天。

**语法**

`DAY(Date)`

`DAY(#2019-12-17#) = 17`

## 7.ISDATE

给定一个有效日期的字符串，该函数返回 true。

**语法**

`ISDATE(String)`

`ISDATE(December 17, 2019) = true`

## 8.MAKEDATE

此函数返回由指定的年、月和日期构造的日期值。

**语法**

`MAKEDATE(year, month, day)`

`MAKEDATE(2019, 12, 17) = #December 17, 2019#`

## 9.MAKEDATETIME

该函数返回由指定的年、月、日以及小时、分钟和秒构造的日期和时间值。

**语法**

`MAKEDATETIME(date, time)`

`MAKEDATETIME("2019-12-17", #11:28:28PM#) = #12/17/2019 11:28:28 PM#
MAKEDATETIME([Date], [Time]) = #12/17/2019 11:28:28 PM#`

## 10.MAKETIME

该函数返回由指定的小时、分钟和秒构造的时间值。

**语法**

`MAKETIME(hour, minute, second)`

`MAKETIME(11, 28, 28) = #11:28:28#`

## 11.月

该函数以整数形式返回给定日期的月份。

**语法**

`MONTH(Date)`

`MONTH(#2019-12-17#) = 12`

## 12.现在

该函数返回当前日期和时间。

**语法**

`NOW()`

`NOW() = 2019-12-17 11:28:28 PM`

## 13.今天

该函数返回当前日期。

**语法**

`TODAY()`

`TODAY() = 2019-12-17`

## 14.年

该函数以整数形式返回给定日期的年份。

**语法**

`YEAR(Date)`

`YEAR(#2019-12-17#) = 2019`

# 类型转换函数

Tableau 中的这些内置函数允许您将字段从一种数据类型转换为另一种数据类型，例如，您可以将数字转换为字符串，以防止或启用 Tableau 的聚合。以下是 Tableau 中的各种类型转换函数；

## 1.日期

给定一个数字、字符串或日期表达式，该函数返回一个日期。

**语法**

`DATE(expression)`

`DATE([Employee Start Date])`
`DATE("December 17, 2019") = #December 17, 2019#`
`DATE(#2019-12-17 14:52#) = #2019-12-17#`

## 2.日期时间

给定一个数字、字符串或日期表达式，该函数返回一个日期时间。

**语法**

`DATETIME(expression)`

`DATETIME(“December 17, 2019 07:59:00”) = December 17, 2019 07:59:00`

## 3.日期解析

给定一个字符串，此函数返回指定格式的日期时间。

**语法**

`DATEPARSE(format, string)`

`DATEPARSE ("dd.MMMM.yyyy", "17.December.2019") = #December 17, 2019#`
`DATEPARSE ("h'h' m'm' s's'", "11h 5m 3s") = #11:05:03#`

## 4.漂浮物

该函数用于将其参数转换为浮点数。

**语法**

`FLOAT(expression)`

`FLOAT(3)`=`3.000`


## 5.（同 Internationalorganizations）国际组织

该函数用于将其参数转换为整数。对于某些表达式，它还会将结果截断为最接近零的整数。

**语法**

`INT(expression)`

`INT(8.0/3.0) = 2`
`INT(4.0/1.5) = 2`
`INT(-9.7) = -9`

## 6.线

该函数用于将其参数转换为字符串。

**语法**

`STR(expression)`

`STR([Date])`

# 聚合函数

Tableau 中的这些内置函数允许您汇总或更改数据的粒度。以下是 Tableau 中的各种聚合函数:

## 1.属性

如果所有行都只有一个值，此函数将返回表达式的值，忽略空值，否则返回星号。

**语法**

`ATTR(expression)`

## 2.AVG

该函数返回表达式中所有值的平均值，忽略空值。AVG 只能用于数值字段。

**语法**

`AVG(expression)`

## 3.收集

这是一个聚合计算，它将参数字段中的值合并在一起，忽略空值。

**语法**

`COLLECT(Spatial)`

## 4.CORR

此计算返回两个表达式的皮尔逊相关系数。

***皮尔逊相关*** 衡量两个变量之间的线性关系。结果范围从-1 到+1(包括-1 和+1 ),其中 1 表示精确的正线性关系，因为一个变量的正变化意味着另一个变量相应幅度的正变化，0 表示方差之间没有线性关系，1 表示精确的负关系。

**语法**

`CORR(expr1, expr2)`

## 5.数数

这是一个函数，用于返回一个组中的项目数，忽略空值。也就是说，如果同一个项目有多个编号，该函数会将其计为单独的项目，而不是单个项目。

**语法**

`COUNT(expression)`

## 6.COUNTD

这是一个函数，用于返回一个组中项目的不同计数，忽略空值。也就是说，如果同一个项目有多个编号，该函数会将其计为一个项目。

**语法**

`COUNTD(expression)`

## 7.柯伐合金

这是一个返回两个表达式的样本协方差**的函数。**

两个变量一起变化的性质可以使用**协方差**进行量化。正协方差表示变量趋向于同向移动，当一个变量的值趋向于变大时，另一个变量的值也趋向于变大。当数据是用于估计较大总体协方差的随机样本时，样本协方差是合适的选择。

**语法**

`COVAR(expr1, EXPR2)`

## 8.COVARP

这是一个返回两个表达式的**总体协方差**的函数。

当所有感兴趣的项目都有整个总体的数据，而不仅仅是一个样本的数据时，总体协方差是合适的选择。

**语法**

`COVARP(expr1, EXPR2)`

## 9.马克斯(男子名ˌ等于 Maximilian)

此函数返回所有记录中表达式的最大值，忽略空值。

**语法**

`MAX(expression)`

## 10.中位数

此函数返回所有记录的表达式的中值，忽略空值。

**语法**

`MEDIAN(expression)`

## 11.部

该函数返回所有记录中表达式的最小值，忽略空值。

**语法**

`MIN(expression)`

## 12.百分位

该函数返回给定表达式的百分比值。返回的数字必须介于 0 和 1 之间，例如 0.34，并且必须是数字常量。

**语法**

`PERCENTILE(expression, number)`

## 13.标准差（standarddeviation）

Tableau 中的函数根据总体样本返回给定表达式中所有值的统计标准偏差**。**

****语法****

**`STDEV(expression)`**

## **14.STDEVP**

**Tableau 中的函数根据有偏总体返回给定表达式中所有值的统计标准偏差**。****

******语法******

****`STDEVP(expression)`****

## ****15.总和****

****Tableau 中的函数返回表达式中所有值的总和，忽略空值。SUM 只能用于数值字段。****

******语法******

****`SUM(expression)`****

## ****16.增值转销公司****

****给定基于总体样本的表达式，此函数返回所有值的统计方差。****

******语法******

****`VAR(expression)`****

## ****17.VARP****

****给定基于总体的表达式，此函数返回所有值的统计方差。****

******语法******

****`VARP(expression)`****

# ****逻辑函数****

****Tableau 中的这些内置函数允许您确定某个条件是真还是假(布尔逻辑)。以下是 Tableau 中的各种逻辑函数:****

## ****1.和****

****此函数对两个表达式执行逻辑 AND(合取)。对于和，要返回 true，必须满足指定的两个条件。****

******语法******

****`IF <expr1> AND <expr2> THEN <then> END`****

****`IF (ATTR([Market]) = "Asia" AND SUM([Sales]) > [Emerging Threshold] )THEN "Well Performing"`****

## ****2.情况****

****Tableau 中的这个函数执行逻辑测试并返回适当的值，类似于大多数常见编程语言中的 SWITCH CASE。****

****当值与给定表达式中指定的条件匹配时，CASE 返回相应的返回值。如果没有找到匹配项，则使用默认的返回表达式。如果没有默认返回，也没有匹配的值，则该函数返回 NULL。****

****CASE 通常比 IIF 或 IF THEN ELSE 更容易使用。****

******语法******

****`CASE <expression> WHEN <value1> THEN <return1> WHEN <value2> THEN <return2> ... ELSE <default return> END`****

****`CASE [Region] WHEN 'West' THEN 1 WHEN 'East' THEN 2 ELSE 3 END`****

## ****3.否则&如果，那么****

****Tableau 中的这个函数测试一系列输入，为满足 IF 条件的第一个表达式返回 THEN 值。****

******语法******

****`IF <expr> THEN <then> ELSE <else> END`****

****`IF [Profit] > 0 THEN 'Profit' ELSE 'Loss' END`****

## ****4.埃尔塞夫****

****Tableau 中的这个函数测试一系列输入，为满足 ESLEIF 条件的第一个表达式返回 THEN 值。****

******语法******

****`IF <expr> THEN <then> [ELSEIF <expr2> THEN <then2>...] ELSE <else> END`****

****`IF [Profit] > 0 THEN 'Profit' ELSEIF [Profit] = 0 THEN 'No Profit No Loss' ELSE 'Loss' END`****

## ****5.结束****

****这个函数结束一个表达式。****

******语法******

****`IF <expr> THEN <then> [ELSEIF <expr2> THEN <then2>...] ELSE <else> END`****

****`IF [Profit] > 0 THEN 'Profit' ELSEIF [Profit] = 0 THEN 'No Profit No Loss' ELSE 'Loss' END`****

## ****6.IFNULL****

****这个 Tableau 函数返回 expr1 not NULL，否则返回 expr2。****

******语法******

****`IFNULL(expr1, expr2)`****

****`IFNULL ([Profit], 0)`****

## ****7.间接免疫荧光****

****这个 Tableau 函数检查条件是否满足，如果为真，则返回一个值，如果为假，则返回另一个值，如果未知，则返回第三个值或 NULL。****

******语法******

****`IIF(test, then, else, [unknown])`****

****`IIF([Profit] > 0, 'Profit', 'Loss', 0)`****

## ****8.ISDATE****

****该函数检查给定的字符串是否是有效的日期，如果是，则返回 true。****

******语法******

****`ISDATE(String)`****

****`ISDATE("2004-04-15") = True`****

## ****9.ISNULL****

****该函数检查给定的表达式是否包含有效数据，如果包含，则返回 true。****

******语法******

****`ISNULL(expression)`****

****`ISNULL ([Profit])`****

## ****10.不****

****此函数对给定的表达式执行逻辑 NOT(否定)运算。****

******语法******

****`IF NOT <expr> THEN <then> END`****

****`IF NOT [Profit] > 0 THEN "No Profit" END`****

## ****11.运筹学****

****该函数对两个表达式执行逻辑或(析取)运算。要使或返回 true，必须满足指定的两个条件中的任何一个。****

******语法******

****`IF <expr1> OR <expr2> THEN <then> END`****

****`IF [Profit] < 0 OR [Profit] = 0 THEN "Needs Improvement" END`****

## ****12.当...的时候****

****该函数查找满足给定表达式中条件的第一个值，并返回相应的返回值。****

******语法******

****`CASE <expr> WHEN <Value1> THEN <return1> ... [ELSE <else>] END`****

****`CASE [RomanNumberals] WHEN 'I' THEN 1 WHEN 'II' THEN 2 ELSE 3 END`****

## ****13.锌****

****Tableau 中的这个函数返回给定的表达式，如果它不为 NULL，否则返回零。****

******语法******

****`ZN(expression)`****

****`ZN([Profit])`****

****这些都是 Tableau 中的基本功能，可以让你更好地了解 Tableau 以及与之相关的各种概念。如果你想查看更多关于人工智能、DevOps、道德黑客等市场最热门技术的文章，你可以参考 [Edureka 的官方网站。](https://www.edureka.co/blog/?utm_source=medium&utm_medium=content-link&utm_campaign=tableau-functions)****

****请留意这个系列中的其他文章和视频，它们会帮助你理解 Tableau 的各种概念。****

> ****1. [Tableau 教程](/edureka/tableau-tutorial-37d2d6a9684b)****
> 
> ****2.[什么是 Tableau？](/edureka/what-is-tableau-1d9f4c641601)****
> 
> ****3. [Tableau 图表](/edureka/tableau-charts-111758e2ea97)****
> 
> ****4. [Tableau 仪表盘](/edureka/tableau-dashboards-3e19dd713bc7)****
> 
> ****5.[Tableau 中的 LOD 表达式](/edureka/tableau-lod-2f650ca1503d)****
> 
> ****6. [Tableau 提示和技巧](/edureka/tableau-tips-and-tricks-a18bf8991afc)****
> 
> ****7.[循序渐进指导学习 Tableau 公共](/edureka/tableau-public-942228327953)****
> 
> ****8. [Tableau 桌面 vs Tableau 公共 vs Tableau 阅读器](/edureka/tableau-desktop-vs-tableau-public-vs-tableau-reader-fbb2a3aa0bac)****
> 
> ****9.[如何在 Tableau 中创建和使用参数？](/edureka/parameters-in-tableau-ac552e6b0cde-ac552e6b0cde)****
> 
> ****10.[Tableau 中的集合是什么，如何创建它们](/edureka/sets-in-tableau-39befe9b7fa1)****
> 
> ****11.[数据混合](/edureka/tableau-lod-2f650ca1503d)****
> 
> ****12 .[Tableau 中的圆环图](/edureka/donut-chart-in-tableau-a2e6fadf6534)****
> 
> ****13.[2020 年你必须准备的 50 大 Tableau 面试问题](/edureka/tableau-interview-questions-and-answers-4f80523527d)****
> 
> ****14.[如何以及何时使用不同的 Tableau 图表](/edureka/tableau-charts-111758e2ea97)****

*****原载于 2019 年 5 月 15 日 https://www.edureka.co*[](https://www.edureka.co/blog/tableau-functions/)**。******