# PySpark —本地文件读写如何提升性能

> 原文：<https://medium.com/capital-one-tech/pyspark-how-local-file-reads-writes-can-help-performance-capital-one-a2add4b3037c?source=collection_archive---------3----------------------->

## 解决数据帧中内存堆和垃圾收集问题的快速指南

![](img/2ad605593c56b98f93384f6f64fbe198.png)

想象一下…

*你正在一个数据集上写一长串曲折的火花变换。一切都很顺利，但是在大约十次连接和数百行代码之后，您会注意到数据集从需要几分钟变成了几个小时，最终导致垃圾收集、堆空间或其他内存问题。您开始读取并尝试不同的 Spark 调用，调整数据的混洗方式，重新写入，重新分配本地内存，所有这些都无济于事。*

如果你在 [PySpark](https://spark.apache.org/docs/latest/api/python/index.html) (或者一般的 [Spark](https://spark.apache.org/) )中工作，Spark *应该*在幕后做很多优化。然而，如果您在不同的数据集上有许多连接或其他昂贵的计算，Spark 可能会感到困惑。

如果 Spark 无法优化您的工作，您可能会遇到垃圾收集或堆空间问题。如果您已经尝试调用了`repartition`、`coalesce`、`persist`和`cache`，但都没有成功，那么可能是时候考虑让 Spark 将数据帧写入本地文件并读回它了。将数据帧写入文件可以帮助 Spark 清除由于 Spark 延迟评估而导致的内存消耗。

*然而，作为一个警告，如果你将一个中间数据帧写到一个文件中，你不能一直重复使用相同的路径。*由于数据无法流入您正在尝试覆盖的同一目录，因此尝试对您正在覆盖的同一路径进行读写会产生问题。这是因为 Spark 只将一小部分数据读入内存，而将大部分数据留在磁盘上，直到需要的时候。如果您读取一个数据集，然后试图将它写出到相同的路径，就会在 Spark 通常的操作顺序中产生冲突。

# 我是如何第一次遇到这个问题的

我是 Capital One 的软件工程师，偶尔会使用 Python 和 Spark。我最近遇到了一个 Spark 转换的问题，它看起来在逻辑上是正确的，但却导致了内存和性能问题。有很多转变，但都和我想象的一样简单。如果不是堆空间问题，那就是垃圾收集；无论是在转换还是测试中。在 Jupyter 笔记本上一个接一个地运行命令，仍然很难找到性能受到影响的地方。在运行测试时，扩展我的笔记本使用的内存，或者 Pycharm 使用的内存，并没有产生任何影响。

下面的功能很小，只有几行代码，但是可以解决很多困惑。我结合了 StackOverflow 的建议，以及 Capital One 其他更有经验的开发人员的帮助，设法解决了这个问题。和往常一样，这个函数在某些地方不起作用，但是我们会在看完代码后详细讨论。

# 把密码给我！

考虑下面的 Python 代码。为了方便起见，我尝试将 PEP8 格式化，所以如果你是这门语言的新手，你可以向你的团队保证你知道并遵循 PEP8 标准(并且肯定不是来自不遵循这些标准的 Java 团队):

```
def clear_computation_graph(data_frame, spark_session): 
    """Returns a 'cleared' dataframe after saving it for PySpark to work from     This will 'clear' the computation graph for you since
    occasionally spark will poorly optimize commands. 
    This is useful to avoid having too many nested operations
    especially more complex ones that tank performance. 
    Keyword arguments: 
    data_frame -- the dataframe you want to clear the graph for
    spark_session -- your current spark session 
    """ 
    with tempfile.TemporaryDirectory() as path:  
        data_frame.write.parquet(path, mode="overwrite") 
        data_frame = spark_session.read.parquet(path) 
        data_frame.cache() 
        data_frame.count() 
        return data_frame
```

# 说明

*我们为什么要做这些事情？*

这样做的目的是创建一个[临时目录](https://docs.python.org/3/library/tempfile.html#tempfile.TemporaryDirectory)，它只为这个函数而存在。它将在返回后删除自身及其内容。然后，它会将您的数据帧写入一个 parquet 文件，并立即读取出来。然后，它将数据帧缓存到本地存储器，执行[动作](https://spark.apache.org/docs/latest/rdd-programming-guide.html#actions)，并返回数据帧。

写入删除自身的[临时目录](https://docs.python.org/3/library/tempfile.html#tempfile.TemporaryDirectory)可以避免产生内存泄漏。此外，我们并不真的关心我们写到什么目录，所以我们得到了一个额外的好处，不必了解我们当前的目录结构，或者传递一个位置来保存东西的额外工作。

写入一个拼花文件并立即读回会“清除”计算图，以帮助 Spark 从新开始到该点。

缓存是一个延迟求值的操作，这意味着 Spark 不会运行那个命令，直到调用了一个“[动作](https://spark.apache.org/docs/latest/rdd-programming-guide.html#actions)”。动作会导致火花图计算到该点。Count 是一个操作，用于确保 Spark 实际运行至此为止的所有命令，并将数据帧缓存在内存中。我们不关心这里有什么动作。缓存和计数使数据帧在内存中保持可用，因为磁盘上的目录会自行清理，否则您会丢失内容。

# 这种解决方案在哪些地方不起作用？

在某些情况下，此解决方案不起作用:

*   如果你已经有了 Spark 优化工具(Spark 不能像这样优化用户定义的函数)。
*   如果你没有足够的内存来写一个本地文件。
*   如果你的数据帧太大，以至于你不能让 Spark 执行一个[动作](https://spark.apache.org/docs/latest/rdd-programming-guide.html#actions)，或者在内存中缓存你的数据帧。
*   如果您的 Spark 转换很简单，并且通过调整可以更好地解决性能问题，比如移除[循环](/@david.mudrauskas/looping-over-spark-an-antipattern-e10ac54824a0)。

缓存应该只保留给非常小的数据集，这些数据集可能会像小型 CSV 表一样被反复读取。如果您正在处理一个大型的 Parquet 数据集，这将干扰 Spark 的本机优化，并使您的查询效率更低，并可能导致内存耗尽问题。

另一种方法是只写入随机或临时目录，忽略缓存/计数。在某些情况下，与内存相比，扩展磁盘空间非常便宜。所以看起来像这样

```
def saveandload(df, path): 
    """ Save a Spark dataframe to disk and immediately read back.    
    This can be used as part of a checkpointing scheme as well as  
    breaking Spark's computation graph. 
    """ 
    df.write.parquet(path, mode="overwrite") 
    return spark.read.parquet(path) 
my_df = saveandload(my_df, "/tmp/abcdef")
```

*但是，等等，这到底是为什么？这些手术相当昂贵。*

从理论上讲，与仅仅缓存相比，这个函数的效率会很低，Spark 的工作方式让你不需要这么做。用户定义的函数对 Spark 来说是困难的，因为它不能优化它们内部发生的事情。保存到内存和调用一个动作本身已经是很昂贵的操作了。但是，在某些情况下，尝试缓存和持久存储数据帧实际上并不会中断所有的计算。如果有很多复杂的连接或其他逻辑，Spark 可能需要硬中断，从它认为的新数据帧开始。

# 让这个解决方案成为你自己的

这其中很多都是可以互换的——如果不能将数据帧写入本地，可以写入 S3 存储桶。您不必将数据帧保存为拼花文件，甚至不必使用[覆盖](https://spark.apache.org/docs/latest/sql-data-sources-load-save-functions.html#save-modes)。你应该可以使用任何火花动作来代替计数。`[Cache](http://spark.apache.org/docs/latest/rdd-programming-guide.html#rdd-persistence)` [可以切换为任意存储级别](http://spark.apache.org/docs/latest/rdd-programming-guide.html#rdd-persistence)。

如果我调用这个方法会是什么样子呢？

*大概是这样:*

[你有一个 spark 会话](https://spark.apache.org/docs/latest/sql-getting-started.html):

```
from pyspark.sql import SparkSession val spark_session = SparkSession 
    .builder() 
    .appName("Spark SQL basic example") 
    .config("spark.some.config.option", "some-value") 
    .getOrCreate()val complex_dataframe = spark.read.csv("/src/resources/file.csv") ... 
#Some transformations on complex_dataframe 
...
```

一旦您在运行时发现内存问题，您只需调用来清除数据帧:

```
complex_dataframe = clear_computation_graph(complex_dataframe, spark_session)
```

这就是你所需要的！然后，您可以继续在数据帧上运行转换。

# 非常感谢我在第一资本的同事

第一资本拥有不可思议的开发人员和工程师。我有幸与他们中的一些人一起工作，并在他们的关注、经验和指导下找到了这个解决方案。我特别要感谢 Robin Neufeld 和 Cleland Loszewski 在这方面的帮助。谢谢大家！

*披露声明:2021 资本一。观点是作者个人的观点。除非本帖中另有说明，否则 Capital One 不隶属于所提及的任何公司，也不被这些公司认可。使用或展示的所有商标和其他知识产权是其各自所有者的财产。*

*最初发表于*[T5【https://www.capitalone.com】](https://www.capitalone.com/tech/software-engineering/pyspark-performance-optimization-local-file/)*。*