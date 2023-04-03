# 知道如何在网站上使用 JMeter 进行压力测试

> 原文：<https://medium.com/edureka/stress-testing-using-jmeter-e6b3c64299d0?source=collection_archive---------1----------------------->

![](img/8e408634a5d455a3713ade25d2ab15d5.png)

Stress Testing Using JMeter — Edureka

大多数系统都是在正常操作条件下开发的。重要的是确保它在重载下不会损坏。软件测试确保系统在紧急情况下不会崩溃。这篇使用 JMeter 进行压力测试的文章将指导您如何按照以下顺序执行压力测试:

*   性能测试简介
*   什么是压力测试？
*   为什么我们需要压力测试？
*   执行压力测试的步骤
*   压力测试类型和工具
*   使用 JMeter 进行压力测试

# 性能测试简介

[软件测试](http://www.edureka.co/blog/what-is-software-testing?utm_source=medium&utm_medium=content-link&utm_campaign=stress-testing-using-jmeter)为我们所有关于机器按照我们希望的方式运行的问题提供了解决方案。不同的[类型的软件测试](https://www.edureka.co/blog/types-of-software-testing?utm_source=medium&utm_medium=content-link&utm_campaign=stress-testing-using-jmeter)帮助测试人员为应用程序确定正确的测试选择。

![](img/b19d18d2d88a68ffd84d2e3d287f66a9.png)

[性能测试](https://www.edureka.co/blog/performance-testing-tutorial?utm_source=medium&utm_medium=content-link&utm_campaign=stress-testing-using-jmeter)是一种软件测试，确保应用程序在工作负载下运行良好。它进一步分为六种不同的类型，例如:

![](img/e3f32f22da9d6e5dac5300f389413957.png)

本文关注压力测试，我们将看看如何使用 JMeter 执行压力测试。

# **什么是压力测试？**

压力测试是一种验证系统的**稳定性** & **可靠性**的软件测试。该测试主要确定系统在极端负载条件下的健壮性和错误处理能力。

![](img/7c099bc97190676644f7fe3f60966eb3.png)

压力测试确保系统在紧急情况下不会崩溃。它在正常工作点之外进行测试，并评估系统在这些极端条件下的工作情况。它还检查系统是否表现出有效的错误管理。

# 为什么我们需要压力测试？

每当出现**高峰**或**流量突然激增**时，对网站进行压力测试是很重要的。如果你不能适应这种突然的交通，它可能会导致收入和声誉的损失。出于以下原因，也需要进行压力测试:

*   检查系统是否在**异常**条件下工作。
*   当系统处于压力下时，显示适当的**错误信息**。
*   极端条件下的系统故障可能导致巨大的**收入损失**。
*   通过执行压力测试，为极端条件下的网站做好准备。

# 执行压力测试的步骤

在任何网站上进行压力测试时，你都需要遵循 5 个步骤:

![](img/38d46272c82c0bef4526e7d029256f45.png)

1.  **计划压力测试** —在这一步中，您收集系统数据，分析系统并定义压力测试目标。
2.  **创建自动化脚本—** 这里，您需要创建压力测试自动化脚本，并为压力场景生成测试数据。
3.  **脚本执行—** 在第三步**，**中，您运行压力测试自动化脚本并存储压力结果。
4.  **结果分析** —存储结果后，现在您需要分析压力测试结果并识别瓶颈。
5.  **调整和优化** —在最后一步，您微调系统，更改配置，并优化代码以满足期望的基准。

现在，您已经了解了压力测试过程中涉及的不同步骤。那么，让我们继续，看看不同类型的压力测试和所需的工具。

# 压力测试类型和工具

不同类型的压力测试包括:

*   **分布式压力测试—** 在分布式客户端-服务器系统中，您可以从服务器跨所有客户端执行测试。压力服务器的作用是向所有压力客户端分发一组压力测试，并跟踪客户端的状态。
*   **应用程序压力测试—** 该测试用于发现应用程序中与数据锁定和阻塞、网络问题和性能瓶颈相关的缺陷。

![](img/601ee90f86af309db17f4ff65353f73a.png)

*   **事务压力测试** —您需要这种测试来对两个或更多应用程序之间的大量事务执行压力测试。它用于微调和优化系统。
*   **系统压力测试** —这种测试是集成的，可以跨运行在同一台服务器上的多个系统进行测试。您还可以发现一个应用程序数据阻碍另一个应用程序的缺陷。
*   **探索性压力测试** —这用于测试具有不寻常参数或条件的系统，这些参数或条件在真实场景中不太可能发生。你可以在任何危急的情况下发现缺陷。

这些是一些不同类型的压力测试。现在，要执行这些测试，您需要一些工具。那么，让我们来看看一些用于压力测试的重要工具。

# 用于压力测试的工具

[测试工具](https://www.edureka.co/blog/performance-testing-tools?utm_source=medium&utm_medium=content-link&utm_campaign=stress-testing-using-jmeter)确保您的应用在高峰流量和极端压力条件下运行良好。以下是一些最常用的压力/网络测试工具:

![](img/691fc1a7588c0aa23f208eb859855b79.png)

*   **Apache JMeter** — Jmeter 是一个开源测试工具。这是一个用于压力和性能测试的纯 Java 应用程序。
*   **LoadRunner** —惠普的 LoadRunner 是一款广泛使用的测试工具。Loadrunner 提供的压力测试结果被视为基准。
*   **NeoLoad** —该工具用于模拟成千上万的用户，以评估压力下的应用性能并分析响应时间。

您可能会感到困惑，因为有许多工具可用于压力测试。但是 JMeter 是在网站上进行压力测试的最受欢迎的工具之一。那么，我们来看看使用 JMeter 进行压力测试的过程中需要的步骤。

# 使用 JMeter 进行压力测试

我已经在之前的文章中详细讨论了关于 [Apache JMeter](https://www.edureka.co/blog/jmeter-tutorial?utm_source=medium&utm_medium=content-link&utm_campaign=stress-testing-using-jmeter) 以及 JMeter 的[安装步骤。因此，让我们从执行压力测试的步骤开始。](https://www.edureka.co/blog/how-to-install-jmeter?utm_source=medium&utm_medium=content-link&utm_campaign=stress-testing-using-jmeter)

**步骤 1** —首先，您必须**在 JMeter 中创建**您自己的**测试计划**。

![](img/40feb2431784901b1d3c4757a4ff9ede.png)

**第二步** —插入一个**线程组**并添加一个 **HTTP 请求**，其中包含您想要执行压力测试的**网站**的服务器名称。

![](img/30e5a6e7818d4629072c327b5497c117.png)

**步骤 3** —接下来，需要在线程组内部添加一个**监听器**，查看**测试结果**。它将显示已经进行的测试的状态。

![](img/77af2956997c267d9ad8fe9a68333f1c.png)

**步骤 4** —现在您需要在线程组中添加一个**响应断言**。这是最重要的元素之一，因为它帮助你在你的软件负载测试计划中断言请求的**响应。**

我们将使用响应断言来断言**响应代码 200** 。响应代码 200 表示您的 HTTP 请求成功。如果您将该值从 200 更改为任何其他数字，测试的状态将更改为不成功。

![](img/b4a1c087e1a0a26f290acac6b2123f9b.png)

现在，您可以添加一些用户或线程，并增加循环次数。所以这会增加你服务器的压力。但是你可以继续进行测试并查看结果，以便在一定的压力下检查你的网站的状态。

如果你想查看更多关于 Python、DevOps、Ethical Hacking 等市场最热门技术的文章，那么你可以参考 [Edureka 的官方网站。](https://www.edureka.co/blog/?utm_source=medium&utm_medium=content-link&utm_campaign=stress-testing-using-jmeter)

请留意本系列中的其他文章，它们将解释软件测试的各个方面。

> 1.[移动应用测试](/edureka/mobile-application-testing-51140ebe4a87)
> 
> 2.[软件测试工具](/edureka/software-testing-tools-ebd9ebac6f29)
> 
> 3.[软件测试的类型](/edureka/types-of-software-testing-d7aa29090b5b)
> 
> 4. [Appium 教程](/edureka/appium-tutorial-28e604aebeb)
> 
> 5. [Appium 工作室教程](/edureka/appium-studio-tutorial-8a13ee9662d6)
> 
> 6. [JMeter 教程](/edureka/jmeter-tutorial-774856163ee9)
> 
> 7.[使用 JMeter 进行负载测试](/edureka/load-testing-using-jmeter-3da837c11a02)
> 
> 8.[自动化测试教程](/edureka/automation-testing-tutorial-157d269e60db)
> 
> 9.什么是功能测试？
> 
> 10.[功能测试与非功能测试](/edureka/functional-testing-vs-non-functional-testing-a08bc732fbdd)
> 
> 11. [JMeter vs LoadRunner](/edureka/jmeter-vs-loadrunner-c1ab63acd935)
> 
> 12.[回归测试](/edureka/regression-testing-b913b7064824)
> 
> 13.[性能测试生命周期](/edureka/performance-testing-life-cycle-d4242d39a5aa)
> 
> 14. [JMeter 插件](/edureka/jmeter-plugins-1bceec7f6226)
> 
> 15. [Appium 架构](/edureka/appium-architecture-505f70bf3484)

*最初于 2019 年 4 月 26 日发表于 https://www.edureka.co。*