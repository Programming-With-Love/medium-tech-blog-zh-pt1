# 调试主机网络的提示和工具的音乐之旅

> 原文：<https://medium.com/oracledevs/a-musical-tour-of-hints-and-tools-for-debugging-host-networks-e3197f31366b?source=collection_archive---------6----------------------->

![](img/7049174eb6490a1e532b9b04cfe250e4.png)

来自 Oracle Linux 内核开发团队的 Shannon Nelson 提供了这些提示和技巧来帮助简化主机网络诊断。他还包括一个推荐的播放列表，用来陪伴你调试！

# “不要行为不端”(黛娜·华盛顿)

与许多调试情况一样，深入研究和解决基于网络的问题看起来像是纯粹的猜测和魔术。在网络领域，我们不仅要应对主机系统的进程和配置，还要应对令人兴奋但又常常令人沮丧的网络流量的异步性。

可能触发调试会话的一些问题包括数据包丢失、数据损坏、性能不佳，甚至随机系统崩溃。这些并不总是以实际的网络问题而告终，但是只要客户提到任何关于他们的布线架或路由器的事情，网络工程师就会被带到现场。

这篇文章并不打算作为调试任何特定网络问题的完整方法，而是我们在调查网络不当行为时使用的一些技巧和工具。

# 启动我(滚石乐队)

为了开始，可能是最重要的可用调试工具，是对不应该发生的事情的简明清晰的描述。这比人们想象的要难得多。你知道我的意思，对吧？客户可能会给我们任何东西，从“它坏了”到 3 页纸的论文，除了实际的问题。

我们通过问一些应该容易回答的简单问题来收集更清晰的描述。比如:

*   是谁发现的，工程联系人是谁？
*   它到底运行在什么设备上？
*   这种情况何时/多久发生一次？
*   涉及哪些机器/配置/网卡等？
*   这类机器都有这个问题，还是只有一两个？
*   是否涉及路由器和/或交换机？
*   是否涉及虚拟机、虚拟功能或容器？
*   是否有 macvlans、bridges、bonds 或团队参与其中？
*   是否涉及任何网络卸载？

有了这些信息，我们应该能够写出自己对问题的描述，看看客户是否同意我们的总结。一旦我们能够完善这一点，我们应该对需要研究的内容有更好的了解。

获取这些信息的一些最有价值的工具是简单的用户命令，用户可以在行为不当的系统上执行这些命令。这些应该有助于详细说明系统上的实际网卡和驱动程序以及它们的连接方式。

**uname-a**——这是一个很好的开始方式，至少可以让你对系统有一个基本的了解，内核使用了多长时间。这可以捕捉到客户没有运行受支持的内核的情况。

接下来的几个有助于找到系统上的所有内容以及它们是如何连接的:

**ip addr，ip link** —这些有助于查看已配置的网络端口，并可能指出离线或未设置正确地址的设备。这些也可以暗示什么债券或团队可能到位。这些命令取代了不推荐使用的“ **ifconfig** 命令。

**ip 路由** —显示哪些网络设备将处理传出的数据包。这在有许多网络端口的系统上非常有用。这取代了已弃用的“ **route** ”命令和类似的“ **netstat -rn** ”。

**brctl show** —列出设置的软件桥以及连接到它们的设备。

**netstat -i** —给出接口及其基本统计信息的汇总列表。这些也可通过“ **ip -s 链接**”获得，只是格式不同。

**lseth** —这是一个非标准命令，它结合了上述命令的大量输出，给出了一个很好的总结。(见[http://vcojot . blogspot . com/2015/11/introducing-lsethlsnet . html](http://vcojot.blogspot.com/2015/11/introducing-lsethlsnet.html))

# 《监视侦探》(埃尔维斯·考斯特罗)

一旦我们知道了涉及的具体设备，以下命令可以帮助我们收集有关该设备的更多信息。这可以为我们提供一个初步的线索，了解设备是否以一种健康的方式进行了配置。

**ethtool<ethX>**—列出驱动程序和连接属性，如当前速度连接和是否检测到链接。

**ethtool-I<ethX>**—列出设备驱动程序信息，包括内核驱动程序和固件版本，有助于确保客户使用正确的软件；和 PCIe 设备总线地址，有利于跟踪低层系统硬件接口。

**ethtool-l<ethX>**—显示设置的 Tx 和 Rx 队列的数量，通常应与要使用的 CPU 内核数量相匹配。

**ethtool-g<ethX>**—显示每个 Tx 和 Rx 队列的数据包缓冲区数量；太多的话我们会浪费内存，太少的话我们会在巨大的吞吐量压力下冒丢包的风险。

**lspci-s<bus:dev:func>-vv**—列出有关 NIC 硬件及其属性的详细信息。你可以从“ **ethtool -i** ”中获得接口的< bus:dev:func >。

# 日记(面包)

系统日志文件中通常有一些关于在调查问题时可能发生了什么的线索。" **dmesg** "给出了直接的内核日志消息，但是要注意，这是一个有限大小的缓冲区，随着时间的推移，它可能会溢出并丢失历史记录。在较旧的 Linux 发行版中，系统日志可以在 **/var/log** 中找到，最有用的是在 **/var/log/messages** 或 **/var/log/syslog** 中，而较新的基于 **systemd** 的系统使用 **journalctl** 来访问日志消息。无论哪种方式，通常会发现一些有趣的痕迹来帮助描述这种行为。

需要注意的一点是，当客户发送日志摘录时，通常是不够的。他们经常会捕捉到类似内核恐慌消息的信息，但不会捕捉到导致恐慌的几行信息。更有用的是完整日志文件的副本，或者至少是事件发生前几个小时的日志。

一旦我们有了完整的文件，就可以在其中搜索错误消息、带有 ethX 名称或 PCI 设备地址的任何日志消息，以寻找更多的提示。有时浏览文件就能发现相关的行为模式。

# 捏造事实(西蒙和加芬克尔乐队)

有了目前收集的信息，我们应该有机会创造一个简单的复制品。很多时候我们不能去戳客户的运行系统，而是需要在我们自己的实验室系统上演示问题和修复。当然，我们没有相同的环境，但是通过足够简洁的问题描述，我们很有可能找到一个显示相同行为的简单案例。

一些有助于重现问题的流量生成器工具包括:

**ping** —发送一个或几个数据包，或者向网卡发送数据包泛洪。它有大小、时间和其他发送参数的标志。

**iperf** —适用于交通繁忙的锻炼，可以并行运行几个以在接收器上获得更好的 RSS 传播。

**pktgen** —这个内核模块可以用来生成比用户级程序多得多的流量，部分原因是数据包不必穿过发送方的网络堆栈。数据包形状和吞吐速率也有多种选择。

scapy —这是一个 Python 工具，允许编写特制包的脚本，有助于确保某些数据模式正是您特定测试所需要的。

# 沿着了望塔(吉米·亨德里克斯的经验)

有了我们自己的问题模型，我们可以开始更深入地观察系统，看看发生了什么:查看吞吐量统计数据和实际的数据包内容。这些工具可以帮助您轻松收集统计数据:

**ethtool-S<ethX>**—大多数网卡设备驱动程序通过 **ethtool** 的“-S”选项提供 Tx 和 Rx 数据包计数以及错误数据。这种设备特定的信息是一个很好的窗口，可以了解 NIC 认为它正在做什么，并可以显示 NIC 何时发现低级问题，包括格式错误的数据包和错误的校验和。

**netstat-s<ethX>**—它给出上层网络堆栈的协议统计信息，例如 TCP 连接、重新传输的段以及其他相关计数器。

**IP-s link show<ethX>**—获取流量计数器摘要的另一种方法，包括一些丢弃的数据包。

**grep<ethX>/proc/interrupts**—查看中断计数器可以更好地了解处理在可用 CPU 内核中的分布情况。对于某些负载，我们可以预期会有很大的分散，而其他负载最终可能会有一个内核比其他内核负载更重。

**/proc/net/*** —这里有许多由内核网络堆栈公开的数据文件，它们可以显示网络堆栈操作的许多不同方面。许多命令行实用程序直接从这些文件中获取信息。有时候，编写自己的脚本来从这些文件中提取您需要的非常具体的数据是很方便的。

**观察** —上述工具给出了当前状态的快照，但有时我们需要更好地了解事情是如何随着时间的推移而运转的。“ **watch** ”实用程序可以通过重复运行 snapshot 命令并显示输出来提供帮助，甚至可以突出显示自上次快照以来哪些地方发生了变化。示例用途包括:

```
#         See the interrupt activity as it happens 
watch "grep ethX /proc/interrupts"#        Watch all of the NIC's non-zero stats 
watch "ethtool -S ethX | grep -v ': 0'"
```

对于捕捉飞行中的数据也很有用的是 **tcpdump** 和它的兄弟 **wireshark** 和 **tcpreplay** 。这对于从网络上捕捉数据包、分析发送和接收的确切内容以及重放对话进行测试来说是无价的。这些都有完整的教程，所以我不会在这里详述，但这里有一个从单个网络数据包输出的示例:

```
23:12:47.471622 IP (tos 0x0, ttl 64, id 48247, offset 0, flags [DF], proto TCP (6), length 52) 
    14.0.0.70.ssh > 14.0.0.52.37594: Flags [F.], cksum 0x063a (correct), seq 2358, ack 2055, win 294, options [nop,nop,TS val 2146211557 ecr 3646050837], length 0 
    0x0000:  4500 0034 bc77 4000 4006 61d3 0e00 0046 
    0x0010:  0e00 0034 0016 92da 21a8 b78a af9a f4ea 
    0x0020:  8011 0126 063a 0000 0101 080a 7fec 96e5 
    0x0030:  d952 5215
```

# 照片和记忆(吉米·克罗斯)

一旦我们做到这一步，我们有一些想法，这可能是一个特定的网络设备驱动程序的问题，我们可以做一点研究驱动程序的历史。一个好的网络搜索是一个无价的朋友。例如，在网上搜索“bnxt_en dropping packets ”,会出现一些关于 Nitro A0 硬件错误修复的参考信息——也许这与我们看到的数据包丢失问题有关？

如果我们有一个 Linux 内核 git 存储库的克隆，我们可以在补丁历史中搜索关键词。如果 macvlan 过滤器出现异常，这将指出一些可能与该问题相关的补丁。例如，以下是 4.18 版中修复的驱动程序重置的 macvlan 问题:

```
$ git log --oneline drivers/net/ethernet/intel/ixgbe | grep -i macvlan | grep -i reset 
8315ef6 ixgbe: Avoid performing unnecessary resets for macvlan offload 
e251ecf ixgbe: clean macvlan MAC filter table on VF reset

$ git describe --contains 8315ef6 
v4.18-rc1~114^2~380^2
```

# 在岁月中旋转(斯迪利·丹)

几个例子可以显示这些工具在现实生活中是如何使用的。当然，当你身处其中的时候，事情从来没有听起来那么简单。

# 通过网桥从 sunvnet 丢失/损坏带有 TSO 的数据包

在对 sunvnet 网络驱动程序(SPARC Linux 内核中的一个虚拟网卡)进行一些性能测试时，我们发现启用 TSO 实际上会大大降低吞吐量，而不是帮助远程系统。在使用 netstat 和 ethtool -S 发现通过基本机器的物理网络有许多丢失的数据包和重试之后，我们在 NIC 上和内部软件桥的不同点上使用 tcpdump 来发现数据包在哪里被破坏和丢弃。我们还在 netdev 邮件列表中发现了关于 TSO 包在进入软件桥时变得混乱的问题的评论。我们对进入主桥的数据包关闭了 TSO，性能问题得到了解决。

# 日志文件指出行为不当的进程

在几台服务器上的网卡硬件随机冻结的情况下，我们发现计算服务守护程序最近更新了一个损坏的版本，该版本会立即停止，并在几十台服务器上同时每秒钟重启几次，并且每次都会重置网卡。一旦守护进程被修复，网卡重置停止，网络问题消失。

# 带回家吧

这只是对用于调试网络问题的一些工具的快速概述。每个人都有自己喜欢的工具和不同的用途，我们在这里只涉及了几个。它们都很方便，但都需要我们的想象力和毅力来帮助我们找到问题的根源。同样有用的还有为收集特定数据集而编写的快速 shell 脚本，以及在查找特定内容时处理各种数据的 shell 脚本。更多的想法，请看下面的链接。

有时候，当我们已经挖了很久，还没有找到金子时，最好是从键盘上站起来，散散步，吃点零食，听听好音乐，让思绪自由驰骋。

狩猎愉快。

# 相关页面

Linux 网络故障排除与调试:[https://UNIX . stack exchange . com/questions/50098/Linux-network-trouble shooting-and-debugging](https://unix.stackexchange.com/questions/50098/linux-network-troubleshooting-and-debugging)

跟踪 NFS:Beyond tcpdump:[https://blogs . Oracle . com/Linux/tracing-NFS % 3a-Beyond-tcpdump-v2](https://blogs.oracle.com/linux/tracing-nfs%3a-beyond-tcpdump-v2)

在 Oracle Linux 上用 DTrace 跟踪 Linux 网络:[https://blogs . Oracle . com/Linux/tracing-Linux-Networking-with-DTrace-on-Oracle-Linux-v2](https://blogs.oracle.com/linux/tracing-linux-networking-with-dtrace-on-oracle-linux-v2)

iproute2 用途:【https://baturin.org/docs/iproute2/】T4

一个带例子的 tcpdump 教程和入门:[https://danielmiessler.com/study/tcpdump/](https://danielmiessler.com/study/tcpdump/)

搜索 git 代码和日志:

[https://git-scm.com/book/en/v2/Git-Tools-Searching](https://git-scm.com/book/en/v2/Git-Tools-Searching)
[https://git-scm.com/docs/git-log#git-log-Sltstringgt](https://git-scm.com/docs/git-log#git-log--Sltstringgt)

Wireshark 用户指南:[https://www.wireshark.org/docs/wsug_html/](https://www.wireshark.org/docs/wsug_html/)

systemd:使用日志:[https://fedoramagazine.org/systemd-using-journal/](https://fedoramagazine.org/systemd-using-journal/)

*原载于*[*https://blogs.oracle.com*](https://blogs.oracle.com/linux/a-musical-tour-of-hints-and-tools-for-debugging-host-networks)*。*