# 使用 Rocket.chat 和 React 创建聊天解决方案—第 1 部分

> 原文：<https://medium.com/quick-code/creating-a-chat-solution-using-rocket-chat-and-react-part-1-2dd4335b19f3?source=collection_archive---------4----------------------->

![](img/3658633b371f60db583ef22b22f152aa.png)

从很长一段时间来看，聊天一直是任何产品最重要的部分之一。无论是考虑个人信息应用程序、客户服务聊天解决方案还是像 Slack 这样的团队管理产品

找到一个正确的聊天解决方案可能会很棘手，因为你会发现很多用不同技术创建聊天的教程，但它们几乎没有可扩展性。

您可以选择预构建的解决方案，但大多数都是付费的，或者在可扩展性或灵活性方面有一定的限制。

如果我必须给你一行介绍，那么 [Rocket Chat](https://rocket.chat/) 是 Slack 的开源替代品。

现在，你可以在任何平台上下载他们的应用程序，并像 Slack 一样使用它，但事实上，它的开源给了它更多的灵活性，其中之一是你可以在完全的管理控制下运行你的火箭聊天服务器

最棒的是 rocket chat 为你提供了 Rest 和 socket API 来创建一个健壮的聊天应用。

这意味着您可以使用 rocket chat 作为聊天服务器，并在其上构建您的前端客户端。这就是我们接下来要做的。

如果还不清楚，让我再解释一下一般步骤。我们将

1.  在本地主机上运行我们的 Rocket 聊天服务器
2.  使用 React 连接服务器并与之交互

该过程的第一步是在您的本地机器上安装 Rocket chat 服务器。下面是根据安装的操作系统在本地机器上运行 rocket chat 服务器的方法

# Linux 操作系统

在 Linux 上运行它非常简单。只需安装火箭聊天使用快照使用给定的链接[火箭聊天快照安装](https://docs.rocket.chat/installation/snaps)

# mac 操作系统

最简单的方法是使用 docker-compose。只需在您的系统中安装 docker 和 docker-compose，然后按照给定的步骤操作

1.  只需创建一个名为 **docker-compose.yml** 的文件，并将这个链接 [Rocket chat docker-compose 文件](https://github.com/RocketChat/Rocket.Chat/blob/develop/docker-compose.yml)的内容复制到新创建的文件中
2.  从创建文件的同一个目录中运行命令 **docker-compose up -d**
3.  它会自动获取最新的 docker 图像，然后运行它
4.  您可以查看 docker 仪表板中运行的 docker 容器

# Windows 操作系统

对于 windows，您还有多种选择

1.  如果你安装了 windows professional，你可以像运行 mac os 一样运行 docker
2.  你也可以使用 VMware 运行 Ubuntu，然后按照上面给出的步骤安装 rocket chat

您可以在前端使用任何东西，只要您可以进行 API 调用并连接到 WebSocket 服务器

正如你可以从标题本身猜到的，我们将使用 react 作为前端客户端

我创建了一个小的 React 挂钩，用于本地连接到 WebSocket 服务器，并以最独特的方式命名(useWebsocket … lol)

所以事不宜迟，这里是完整的钩子

代码非常简单明了。但是让我补充一些额外的要点

1.  我们创建了一个状态变量 connected 来表示与 rocket 聊天服务器的连接状态
2.  我们使用 ref 而不是 state 来存储 WebSocket 对象，因为 ref 不会像 state 那样在发生变化时触发重新呈现
3.  有些任务需要在 onopen 处理程序中执行，这就是我们留出空间的原因
4.  经过一些定制后，上面这段代码可以用于连接任何 WebSocket 服务器
5.  如果您在运行 rocket chat 服务器时遇到任何问题，您可以在评论中询问，或者从他们的文档 [Rocket chat 安装指南](https://docs.rocket.chat/installation)中查看在您的机器上运行服务器的其他方式

**就这样**在本系列的下一部分，我们将了解 Rocket chat 的流程以及连接到服务器所需的所有 API。

在那之前，快乐编码😃

*原载于 2020 年 10 月 2 日 https://codingshogun.com*[](https://codingshogun.com/connect-to-rocket-chat-server-using-react-part-1/)**。**