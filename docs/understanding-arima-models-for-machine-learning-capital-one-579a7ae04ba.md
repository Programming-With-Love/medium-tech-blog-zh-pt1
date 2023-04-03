# 理解机器学习的 ARIMA 模型

> 原文：<https://medium.com/capital-one-tech/understanding-arima-models-for-machine-learning-capital-one-579a7ae04ba?source=collection_archive---------4----------------------->

理解自回归综合移动平均的简单介绍

![](img/b1069eb403bae29b8e2c44d50da63fae.png)

如果你是拥有股票的 50%美国人中的一员，我相信你会因为思考你投资的未来价格而失眠。你可以试着通过阅读经济学家和其他投资专家的预测来平息你的恐惧，但是他们是如何得出他们的预测的呢？一种方法是使用自回归综合移动平均(ARIMA)模型。

# 什么是自回归综合移动平均线？

自回归综合移动平均(ARIMA)模型在许多行业中有许多用途。它被广泛应用于[需求预测](https://journals.sagepub.com/doi/10.1177/1847979018808673)，比如确定食品制造业的未来需求。这是因为该模型为管理者提供了做出供应链相关决策的可靠指南。ARIMA 模型也可以用来根据过去的价格预测你的股票的未来价格。请注意，虽然它们可能会帮助你预测标准普尔 500 指数的价格随时间的变化，但我很抱歉地说，它不会帮助你通过预测像 Gamestop (GME)这样的[病毒性股票何时会在下次](https://www.cnbc.com/2021/04/26/gamestop-shares-jump-after-the-reddit-favorite-raises-more-than-500-million-in-stock-sales.html)飙升来赚快钱。

这是因为 ARIMA 模型是一种用于预测时间序列数据的通用模型。ARIMA 模型通常表示为 ARIMA (p，d，q)，其中 p 是自回归模型的阶，d 是差分的程度，q 是移动平均模型的阶。ARIMA 模型使用差分法将非平稳时间序列转换为平稳时间序列，然后根据历史数据预测未来值。这些模型使用数据中残差的“自动”相关性和移动平均值来预测未来值。

# 使用 ARIMA 模型的潜在优势

*   仅需要时间序列的先验数据来概括预测。
*   在短期预测中表现出色。
*   模拟非平稳时间序列。

# 使用 ARIMA 模型的潜在缺点

*   很难预测转折点。
*   在确定模型的(p，d，q)阶时涉及到相当多的主观性。
*   计算开销很大。
*   长期预测的表现较差。
*   不能用于季节性时间序列。
*   比[指数平滑](https://en.wikipedia.org/wiki/Exponential_smoothing)更难解释。

# 如何建立一个 ARIMA 模型

假设你想用 ARIMA 模型预测一家公司的股票价格。首先，你必须下载该公司过去几年——比如说十年——的公开股票价格。一旦有了这些数据，就可以开始训练 ARIMA 模型了。根据数据的趋势，您将选择该模型所需的差分(d)阶。接下来，根据自相关和部分自相关，可以确定回归的阶数(p)和移动平均的阶数(q)。可以使用[赤池信息标准(AIC)](https://en.wikipedia.org/wiki/Akaike_information_criterion) 、[贝叶斯信息标准(BIC)](https://en.wikipedia.org/wiki/Bayesian_information_criterion) 、最大似然和标准误差作为性能度量来选择适当的模型。

# 理解 ARIMA 模式是如何运作的

如前所述，ARIMA(p，d，q)是最流行的[计量经济学模型](https://en.wikipedia.org/wiki/Econometric_model)之一，用于预测时间序列数据，如股票价格、[需求预测](https://journals.sagepub.com/doi/10.1177/1847979018808673)，甚至是传染性疾病的[传播。ARIMA 模型基本上是一个拟合在 d 阶差分时间序列上的](https://www.sciencedirect.com/science/article/pii/S2352340920302341) [ARMA 模型](https://en.wikipedia.org/wiki/Autoregressive%E2%80%93moving-average_model),使得最终差分时间序列是平稳的。

平稳时间序列的统计特性如均值、方差、自相关等。都是不变的。平稳化的序列相对容易预测——您只需简单地预测它的统计特性在未来会和过去一样！

要了解 ARIMA 模型的工作原理，您需要更好地理解其名称中的三个术语:

让我们来看看 Rob J Hyndman 和 George Athanasopoulos 的《预测:原理与实践》(第二版)中的两个图表。左边的图(a)是谷歌连续 200 天的股价。这是非平稳时间序列。右边的图(b)是谷歌股价连续 200 天的每日变化。图像(b)是静止的，因为它的值不依赖于观察时间。在这个例子中，差分的阶是一，因为一阶差分序列是稳定的。

将上述所有三种类型的模型结合起来，就产生了 ARIMA(p，d，q)模型。

# 结论

ARIMA 方法是一种统计方法，用于分析和建立预测模型，该模型通过模拟数据中的相关性来最好地代表时间序列。由于纯粹的统计方法，ARIMA 模型只需要一个时间序列的历史数据来概括预测，并设法提高预测精度，同时保持模型简约。

尽管很节俭，使用 ARIMA 模型还是有很多潜在的缺点。其中最重要的是源于识别 p 和 q 参数的主观性。尽管使用了自相关和部分自相关，p 和 q 的选择取决于模型开发者的技能和经验。此外，与简单的指数平滑法和霍尔特温特斯法相比，ARIMA 模型更复杂，因此解释力较低。

最后，类似于所有的预测方法，由于向后看，ARIMA 模型不擅长长期预测，也不擅长预测转折点。它们在计算上也很昂贵。

因此，仅使用时间序列数据，ARIMA 模型就可以轻松、准确地用于短期预测，但要找到每个用例的最佳参数集，可能需要一些经验和实验。

要获得更多资源，请查看一些使用 ARIMA 方法的项目:

*   [如何用 Python 创建时间序列预测的 ARIMA 模型](https://machinelearningmastery.com/arima-for-time-series-forecasting-with-python/)
*   [ARIMA 模型 Python 中时间序列预测完全指南](https://www.machinelearningplus.com/time-series/arima-model-time-series-forecasting-python/)
*   [R 中使用 ARIMA 模型的时间序列分析](https://datascienceplus.com/time-series-analysis-using-arima-model-in-r/)

参考

1.  [投资媒体](https://www.investopedia.com/terms/a/autoregressive-integrated-moving-average-arima.asp)
2.  《预测:原理与实践》(第二版),作者 Rob J Hyndman 和 George Athanasopoulos
3.  [https://libres.uncg.edu/ir/uncw/f/zhai2005-2.pdf](https://libres.uncg.edu/ir/uncw/f/zhai2005-2.pdf)
4.  [预测坦桑尼亚的牲畜消费量](https://www.tandfonline.com/doi/full/10.1080/23311932.2019.1607430)
5.  [需求预测](https://journals.sagepub.com/doi/10.1177/1847979018808673)
6.  [ARIMA 模型在 COVID-2019 流行病数据集上的应用](https://www.sciencedirect.com/science/article/pii/S2352340920302341)

**Neha Bora** ，数据科学首席助理

Neha 拥有爱荷华州立大学的应用数学硕士学位和浦那印度科学教育研究所的物理学双学士/硕士学位。她与数据科学、业务、产品和软件工程团队一起工作，并热衷于将良好管理的软件工程原则应用于数据科学。在 Capital One，她专注于开发一个风险模型，帮助恢复业务优化与客户的合作。

*披露声明:2021 首创一号。观点是作者个人的观点。除非本帖中另有说明，否则 Capital One 不隶属于所提及的任何公司，也不被这些公司认可。使用或展示的所有商标和其他知识产权是其各自所有者的财产。*

*原载于*[*https://www.capitalone.com*](https://www.capitalone.com/tech/machine-learning/understanding-arima-models/)*。*