# 使用动态时间弯曲的时间序列相似性-解释

> 原文：<https://medium.com/walmartglobaltech/time-series-similarity-using-dynamic-time-warping-explained-9d09119e48ec?source=collection_archive---------0----------------------->

![](img/d60723ea38a446c595a099f666094650.png)

Photo Credit : [Trend Comparison](https://pixabay.com/illustrations/graph-diagram-growth-written-report-3033203/)

了解为什么 DTW 是比较两个或更多时间序列信号的非常有用的技术，并将其添加到您的时间序列分析工具箱中！！

**1。简介**

在这个由物联网(IoT)主导的世界中，越来越需要理解安装在家庭、商场、工厂和办公室中的设备发出的信号。例如，任何语音助手都可以检测、认证和解释来自人类的命令，即使它是慢还是快。我们的声调在一天中的不同时间会有所不同。在我们起床后的清晨，与一天中的其他时间相比，我们以一种更慢、更沉重和更懒惰的语气互动。这些设备将信号视为时间序列，并通过考虑信号中不同的滞后和相位来比较峰值、谷值和斜率，从而得出相似性得分。最常用的算法之一是*动态时间扭曲(DTW)* 。通过忽略任何偏移和速度来比较两个或多个时间序列是一种非常稳健的技术。

作为沃尔玛房地产团队的一员，我致力于了解制冷机组、除湿机、照明等不同资产的能源消耗模式。安装在零售店。这将有助于提高从物联网传感器收集的数据的质量，检测和预防系统中的故障，并改善能耗预测和规划。这种分析涉及一天中不同时间的能源消耗时间序列，即一周中的不同日子、一个月中的不同星期和一年中的不同月份。当由于未知因素导致能源消耗的相位突然改变时，时间序列预测经常给出不好的预测。例如，如果除霜时间表、制冷机组的项目刷新程序或天气突然变化，并且没有被捕获以解释能耗的相移，则检测这些变化点是很重要的。

在下面的例子中，一家商店的商品更新程序在周二移动了 2 个小时，导致制冷设备的峰值能耗发生变化，我们无法获得许多此类商店的信息。

![](img/6ae8853a56734da24fe046a7aeb75409.png)

凌晨 2 点的高峰转移到了 4 点。当连续几天递归运行时，DTW 可以识别发生相移而信号形状没有太大变化的情况。

![](img/abf9360382cc64d35a0648cc5a7003e5.png)

在这种情况下，由于在星期二检测到相移，训练数据可以被限制到星期二以后，以改进对未来能量消耗的预测。这种设置大大改进了对那些转变原因不明的悬挂物的预测(> 50%)。这是传统的一对一信号比较方法所无法做到的。

在这篇博客中，我将解释 DTW 算法是如何工作的，并介绍两个时间序列之间的相似性得分的计算及其在 python 中的实现。这篇博客的大部分内容都来源于这篇[论文](https://ieeexplore.ieee.org/document/1163055)，在下面的参考资料部分也有提及。

**2。我们为什么需要 DTW？**

任何两个时间序列都可以用欧几里得距离或其他类似的距离在时间轴上进行一对一的比较。将时间 T 处的第一时间序列的幅度与时间 T 处的第二时间序列的幅度进行比较。这将导致非常差的比较和相似性得分，即使两个时间序列在形状上非常相似但在时间上不同相。

![](img/e6a1311989f01d27879ee0306996dbd8.png)

DTW 将时间 T 处的第一信号的幅度与时间 T+1 和 T-1 或 T+2 和 T-2 处的第二信号的幅度进行比较。这确保了对于具有相似形状和不同相位的信号，它不会给出低的相似性分数。

![](img/ecb102ac14defcd37d316b6652d6f06d.png)

**3。** **它是如何工作的？**

让我们取两个时间序列信号 P 和 Q

系列 1 (P) : 1，4，5，10，9，3，2，6，8，4

系列 2 (Q): 1，7，3，4，1，10，5，4，7，4

![](img/2a4718a54b10d2b1e11a02e3af4118e8.png)

***第一步:*创建空成本矩阵**

创建一个空的成本矩阵 M，用 x 和 y 标记作为要比较的两个序列的幅度。

![](img/7c5b9a5a8505a141cbe267b8fc6f04ae.png)

***第二步:成本计算***

从左下角开始，使用下面提到的公式填写成本矩阵。

**M(i，j) = |P(i) — Q(j)| + min ( M(i-1，j-1)，M(i，j-1)，M(i-1，j) )**

在哪里

m 是矩阵

I 是 P 系列的迭代器

j 是系列 Q 的迭代器

![](img/8fe34e9c72e9bb64ba7f85ee23cc5bb0.png)

让我们举几个例子(11、3 和 8)来说明下表中突出显示的计算。

![](img/e5acf31447c89eddc6a7165d8592f71a.png)

11 年来，

![](img/dac1c960d3dcfe2c9a69848b0837b4b0.png)

| 10–4 |+最小值(5，12，5)

= 6 + 5

= 11

类似地，对于 3，

| 4–1 |+最小值(0)

= 3+ 0

= 3

对于 8，

| 1–3 |+最小值(6)

= 2 + 6

= 8

整个表格将如下所示:

![](img/1f02239aa8837a3ae59e50eaf77e3f1d.png)

***第三步:*翘曲路径识别**

确定从矩阵右上角开始到左下角的扭曲路径。基于具有最小值的邻居来识别遍历路径。

在我们的示例中，它从 15 开始，并在其邻居 18、15 和 18 中寻找最小值，即 15。

![](img/ebdbad10e75b9c53af102d99bb9b5ba0.png)![](img/d12a6811b472b8b088a2273c25f267ef.png)

扭曲遍历路径中的下一个数字是 14。这个过程一直持续到我们到达表格的底部或左轴。

![](img/5bd9d0d6aea5844efdfddde0fb5a5b08.png)

最终的路径将如下所示:

![](img/f7e5c5cd36f9a0b995708e7d16598ea0.png)

设这个弯曲路径系列称为 d。

d = [15，15，14，13，11，9，8，8，4，4，3，0]

***第四步:*最终距离计算**

时间标准化距离

![](img/eb1d35b79991463f0583e1b4fe7ba494.png)

其中 k 是数列 d 的长度。

在我们的例子中 k = 12。

d =(15+15+14+13+11+9+8+8+4+4+3+0)/12

= 104/12

= 8.63

让我们再举一个例子，两个非常相似的时间序列有单位时间偏移差。

![](img/a5765049516607b50226d60e3e236876.png)

成本矩阵和扭曲路径会是这样的。

![](img/ac8a373a9661010e5ae3329120578dd8.png)

DTW 距离，D =

( 0 + 0 + 0 + 0 + 0 +0 +0 +0 +0 +0 +0 ) /11

= 0

零 DTW 距离意味着时间序列非常相似，这确实是图中观察到的情况。

**3。Python 实现**

python 中贡献了很多库。我已经分享了下面的链接。

[](https://pypi.org/project/dtw-python/) [## dtw-python

### 动态时间弯曲(DTW)算法的综合实现。DTW 计算最佳(最小累积…

pypi.org](https://pypi.org/project/dtw-python/) [](https://pypi.org/project/dtw/) [## dtw

### Dtw 是一个 Python 模块，用于计算动态时间弯曲距离。它可以用作…之间的相似性度量

pypi.org](https://pypi.org/project/dtw/) 

然而，为了更好地理解算法，按照下面的代码片段自己编写函数是一个很好的实践。

我没有过多关注这段代码的时间和空间复杂性。然而，DTW 的自然实现具有 O(M，N)的时间和空间复杂度，其中 M 和 N 是要在其间计算 DTW 距离的相应时间序列的长度。像 [PrunedDTW](http://sites.labic.icmc.usp.br/prunedDTW/) 、 [SparseDTW](https://arxiv.org/abs/1201.2969) 、 [FastDTW](https://cs.fit.edu/~pkc/papers/tdm04.pdf) 和 [MultiscaleDTW](https://www.researchgate.net/publication/334413562_Iterative_Multiscale_Dynamic_Time_Warping_IMs-DTW_A_tool_for_rainfall_time_series_comparison) 这样更快的实现也是可用的。

**4。应用程序**

*   语音助手中的语音识别和认证
*   用于电子设备中能耗异常检测的时间序列信号分割
*   监控健身带记录的信号模式，以检测行走和跑步过程中的心率

**5。参考文献**

 [## 口语单词识别的动态规划算法优化

### IEEE Xplore，提供世界上最高质量的工程和…

ieeexplore.ieee.org](https://ieeexplore.ieee.org/document/1163055) [](https://databricks.com/blog/2019/04/30/understanding-dynamic-time-warping.html) [## 理解动态时间扭曲

### 试试 Databricks 中的这个笔记本这个博客是我们两部分系列的第一部分。要进入第 2 部分，请转到使用动态时间…

databricks.com](https://databricks.com/blog/2019/04/30/understanding-dynamic-time-warping.html) [](/datadriveninvestor/dynamic-time-warping-dtw-d51d1a1e4afc) [## 动态时间扭曲(DTW)

### 时间序列分析算法

medium.com](/datadriveninvestor/dynamic-time-warping-dtw-d51d1a1e4afc) [](https://www.psb.ugent.be/cbd/papers/gentxwarper/DTWalgorithm.htm) [## GenTXWarper -挖掘基因表达时间序列

### 动态时间弯曲(DTW)是一种时间序列校准算法，最初是为语音识别开发的(1)。它…

www.psb.ugent.be](https://www.psb.ugent.be/cbd/papers/gentxwarper/DTWalgorithm.htm) 

[https://www.irit.fr/~Julien.pinquier/Docs/TP _ MABS/RES/DTW-sakoe-chiba 78 . pdf](https://www.irit.fr/~Julien.Pinquier/Docs/TP_MABS/res/dtw-sakoe-chiba78.pdf)

[](https://pypi.org/project/dtw-python/) [## dtw-python

### 动态时间弯曲(DTW)算法的综合实现。DTW 计算最佳(最小累积…

pypi.org](https://pypi.org/project/dtw-python/)