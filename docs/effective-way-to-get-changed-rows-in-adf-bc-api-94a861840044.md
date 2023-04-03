# 在 ADF BC API 中获取已更改行的有效方法

> 原文：<https://medium.com/oracledevs/effective-way-to-get-changed-rows-in-adf-bc-api-94a861840044?source=collection_archive---------6----------------------->

您是否想过如何在不遍历整个行集的情况下获取事务中所有已更改的行？事实证明，使用 ADF BC API 方法非常简单—*getAllEntityInstancesIterator*，它被调用用于附加到当前 VO 的实体定义。

方法工作得很好，它从不同的行集页返回更改的行，而不仅仅是从当前的。在我的实验中，我更改了第一页中的几行:

![](img/2ec006710af97423d8aff2283a6cf685.png)

第五页有几行。我还删除了行并创建了一个:

![](img/6a4c7e8aa2b6f0e67049380094921271.png)

方法返回有关所有已更改行以及已删除行和新行的信息:

![](img/073a3a763b7928f4fd274352317dd6d0.png)

在 VO Impl 类中使用*getAllEntityInstancesIterator*方法的示例。这种方法有助于获取当前事务中所有已更改的行，非常方便:

![](img/ea83057b61600aa602f9405e72ac7a3f.png)

示例应用程序源代码可在 [GitHub](https://github.com/abaranovskis-redsamurai/ADFChangedRowProcessingApp) 上获得。

*原载于 2018 年 5 月 29 日*[*andrejusb.blogspot.com*](http://andrejusb.blogspot.com/2018/05/effective-way-to-get-changed-rows-in.html)*。*