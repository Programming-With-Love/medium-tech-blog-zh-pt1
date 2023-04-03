# Oracle 离线持久性工具包—提交客户机更改

> 原文：<https://medium.com/oracledevs/oracle-offline-persistence-toolkit-submitting-client-changes-661b9c556490?source=collection_archive---------8----------------------->

与 Oracle Offline Persistence toolkit 相关的一个重要主题是——当存在数据冲突时，将客户端更改提交到后端。如果数据是在后端更新的，而客户端是离线的，客户端想要提交他的更改——我们会告知冲突，并询问客户端真正想要做什么。如果客户端选择提交更改，这意味着我们应该用最新的更改指示器将客户端更改推送到后端。

有一种特殊情况，当客户端在离线时多次更新相同的数据时，在在线同步期间，我们需要确保在同步后检索更改指示符，并在同步前侦听器中应用，以确保后续请求正确执行。查看我以前的帖子，关于请求前同步监听器— [Oracle 离线持久性工具包—请求前同步监听器](http://andrejusb.blogspot.com/2018/10/oracle-offline-persistence-toolkit.html)。

示例—让我们更新一条记录并将更改提交到后端:

![](img/38ad3a7b4ec052586192b34725166238.png)

假设另一个用户离线并更新相同的记录:

![](img/801c67b32958a62e56c1a4357c30705a.png)

用户在上线前再次更新相同的记录。现在，同步队列中有两个请求:

![](img/e76c314daf0683a76bc8a19db1fa3ce8.png)

一旦上线，同步将被执行，我们将得到第一个请求的冲突(同一行已经被另一个用户更新)。此时，同步后监听器将获得有关冲突的信息，并将缓存从后端返回的最新更改指示器值。如果用户决定应用他的更改，则删除请求，用从后端接收的最新更改指示符值构建新请求，并将该请求插入同步队列:

![](img/ac65fcf1988fbd372ac261a766cf2dc0.png)

如果同一记录被更新多次，第二个请求也将失败，因为该请求尚未使用最新的更改指示符进行更新:

![](img/b827c08ed6794b00c3c62dbafe43627b.png)

假设用户也决定应用第二个请求的更改，我们将使用最新的更改指示符更新请求，并提交它进行同步。在同步后监听程序中，存储在本地缓存中的更改指示符值将被更新。

成功同步，更改指示= 296:

![](img/4f19243add4d10aa348246893db27653.png)

新的更改指示符值将在同步后监听程序中检索，并在同步前监听程序中应用于第二个请求，更新同一数据行:

![](img/807d081747dbf451346305dd9ae3ee90.png)

下面是代码，它允许用户将更改应用到后端。我们删除失败的请求，更新它并在同步队列中创建新的请求，恢复同步过程:

![](img/730d1e82973bca2fea1c91051a14442f.png)

从我的 [GitHub](https://github.com/abaranovskis-redsamurai/persistencejetapp) 资源库下载所描述用例的示例代码。

*原载于 2018 年 10 月 7 日*[*andrejusb.blogspot.com*](https://andrejusb.blogspot.com/2018/10/oracle-offline-persistence-toolkit_7.html)*。*