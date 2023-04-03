# PySpark 基础—创建测试数据框架

> 原文：<https://medium.com/version-1/pyspark-basics-create-a-test-dataframe-7b39d3ba9c51?source=collection_archive---------0----------------------->

![](img/9ba424435e998951f9aee9dd0cadfe9e.png)

当我学习一项新技术时，我喜欢在将它们用于生产之前，在简单的测试数据上尝试新的特性和功能。

所以…

# 我们如何在 Spark 中创建一个简单的测试数据框架？

这里有一些快速简单的方法来创建少量的测试数据来测试一些 PySpark 函数。

**注意** —我在这些例子中使用了数据块，我还没有在其他平台上测试过。

## **方法 1** —来自 Python 列表的数据帧

*spark.createDataFrame* 允许我们从 Python 列表中创建数据帧。

```
people = [
  (10, "blue"),
  (13, "red"),
  (15, "blue"),
  (99, "red"),
  (67, "blue")
]peopleDf = spark.createDataFrame(people,["age","fave_colour"])
peopleDf.show()+---+------+
|age|colour|
+---+------+
| 10|  blue|
| 13|   red|
| 15|  blue|
| 99|   red|
| 67|  blue|
+---+------+
```

注意——我们在 createDataFrame 调用中指定了列的名称，我们也可以执行其他设置工作，比如模式——参见 Spark [文档](https://spark.apache.org/docs/3.1.1/api/python/reference/api/pyspark.sql.SparkSession.createDataFrame.html)。

## **方法 2** —使用范围

这是创建大量数据的快捷方式，但所有行都是一样的，除非你有创意，我们在这里尽量保持简单…

***spark.range*** 调用这里的键，并根据指定范围的大小创建 dataframe，然后我们可以添加更多的列，使事情变得更令人兴奋！

```
from pyspark.sql import functions as F# Create test frame
dateDF = spark.range(3)\
  .withColumn("today", F.current_date())\
  .withColumn("timestamp",F.current_timestamp())\
  .withColumn("num",7+F.col("id"))\dateDF.show(100,False)+---+----------+-----------------------+---+
|id |today     |timestamp              |num|
+---+----------+-----------------------+---+
|0  |2022-02-12|2022-02-12 17:14:22.096|7  |
|1  |2022-02-12|2022-02-12 17:14:22.096|8  |
|2  |2022-02-12|2022-02-12 17:14:22.096|9  |
+---+----------+-----------------------+---+
```

这里有几点—我们使用 ***pyspark.sql*** 库来使用日期函数—***current _ date()***和***current _ timestamp()***

***withColumn*** 用于在我们的基本数据框架中添加一个新的命名列，并为该列中的数据设置一个值。

此外，请注意 ***show()*** 方法的参数，在这种情况下，我们指定返回的最大行数为 100，尽管 dataframe 只有 3 行，因此在这里没有影响。 ***False*** 参数停止 show 方法截断\切断输出。

同样，使用 spark.range()创建数据帧时，更多选项参见[文档](https://spark.apache.org/docs/latest/api/python/reference/api/pyspark.sql.SparkSession.range.html)，例如，控制 id 值。

就是这样！我希望这是有用的，我将在以后的博客中更详细地讨论在数据帧中创建随机数据和探索 PySpark 日期函数。

![](img/0d5e1fa621cf70c90f08f4091e81000e.png)

**关于作者:**

Mike Knee 是第 1 版的 Azure 数据开发人员。