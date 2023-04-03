# Python-oracledb 瘦模式对象性能

> 原文：<https://medium.com/oracledevs/python-oracledb-thin-mode-object-performance-8a230fe2bf4?source=collection_archive---------0----------------------->

![](img/6460723b72a69ada1e226d95d660c976.png)

Photo by [Nicolas Hoizey](https://unsplash.com/@nhoizey?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

访问 Oracle 对象和集合可能需要在应用程序和数据库之间进行几次“往返”,以收集元数据和进行其他编组。所以对我来说，获取对象比获取简单的标量类型慢并不奇怪。对其他人来说，性能差异是显而易见的，并成为一个话题。我看到的最常见的对象性能问题来自 Oracle Spatial 的用户，他们正在获取`MDSYS.SDO_GEOMETRY`类型。

Anthony Tuininga 和我们的驱动团队刚刚在 [python-oracledb](https://oracle.github.io/python-oracledb/) 1.2 的瘦模式中增加了对 Oracle 对象和集合的支持。我想将这个新的实现与传统的胖模式(使用 Oracle 客户端库)进行比较。

由于已经有了一个来自之前调查的带有`SDO_GEOMETRY`列的数据集，我很快就能够编写一个简单的脚本来实现`"select geometry from t where rownum <= :n"`。我添加了一些计时和往返计数代码，并在我的笔记本电脑上用 VirtualBox 中运行的“本地”Oracle Database 23c Beta 实例运行了一次。代表性的结果是:

```
$ python objperf.py 
Thin mode: rows=190000 arraysize=1000 roundtrips=199 seconds=2.65

$ DRIVER_TYPE=thick python objperf.py 
Thick mode: rows=190000 arraysize=1000 roundtrips=216 seconds=6.02
```

这对于瘦模式来说非常好。

无论我获取了多少行，往返行程的减少都是恒定的。这只是反映了两种模式的初始元数据收集差异。在通过网络开始获取数据后，内部驱动程序处理会带来好处。无论有多少行，瘦模式的大约两倍的性能似乎都保持不变。当我将数据集放在一台非常远程的机器上时，这一点也很明显:

```
$ python objperf.py 
Thin mode: rows=3000 arraysize=1000 roundtrips=12 seconds=10.15

$ DRIVER_TYPE=thick python objperf.py 
Thick mode: rows=3000 arraysize=1000 roundtrips=29 seconds=19.66
```

总的来说，我们真的很高兴能够通过 python-oracledb 瘦模式为您带来这种性能提升。

最后，在处理对象时，其他一些通用的性能技巧包括保留从`connection.gettype()`中显式检索的元数据，因为这将需要几次往返来获取对象元数据。也许您可以在 SQL 语句中提取对象属性，并将其作为标量类型返回，这样就不会有对象提取了。或者您可以在数据库中完成所有的处理，例如在 PL/SQL 中？在某些慢速网络的情况下，使用具有大量属性的对象，您也许能够将查询中的对象字符串化，然后在应用程序中重新组合它们？如果不需要优化到第 n 次(原谅空间双关)，那就忽略这一切，直接简单的取对象。