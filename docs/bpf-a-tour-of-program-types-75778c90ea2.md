# Bpf —程序类型之旅

> 原文：<https://medium.com/oracledevs/bpf-a-tour-of-program-types-75778c90ea2?source=collection_archive---------0----------------------->

## ***Oracle Linux 内核开发人员 Alan Maguire 介绍了这个由六部分组成的关于 BPF 的系列文章，其中他深入介绍了内核的“Berkeley 包过滤”——这是一个有用的、可扩展的内核功能，不仅仅用于包过滤。***

如果你关注 Linux 内核开发的讨论和博客文章，你可能最近经常听到 BPF 提到。它正被用于高性能负载平衡、DDoS 缓解和防火墙、内核和用户空间代码的安全检测等等！BPF 通过在许多不同的上下文中支持安全灵活的编程环境来做到这一点；网络数据路径、内核探测、性能事件等等。安全是关键——在大多数环境中，添加内核模块会带来巨大的风险。然而，BPF 程序在程序加载时被验证，以确保不发生越界访问等。此外，BPF 支持即时将其字节码编译成本机指令集，因此 BPF 程序也很快。如果你对快速数据包处理和可观测性感兴趣，学习 BPF 绝对应该在你的任务清单上。

在这里，我们试图给 BPF 一个指南，涵盖一系列的主题，希望能帮助开发人员努力抓住写 BPF 程序。本指南基于 Linux 4.14(这是 Oracle Linux UEK5 的内核)，所以请记住这一点，因为自那以后，BPF 已经进行了大量的更改，并且一些软件包名称等可能会因其他发行版而有所不同。

因为 Linux 中的 BPF 是一个快速移动的目标，所以我将试着把您指向内核代码库中的相关位置，这可能有助于您了解该技术能做什么。samples/bpf 目录是一个很好的地方，可以看看其他人做了什么，但是这里我们也将深入研究实现作为参考，因为它可能会给你一些关于如何创建新的 bpf 程序的想法。这里的目的不是深入探究 BPF 的内部，而是给出代码中揭示 BPF 功能的一些地方。我用来参考的源代码树是我们的 UEK5 版本，基于 Linux 4.14.35。参见 https://github.com/oracle/linux-uek/tree/uek5/master[。所描述的大多数功能都可以在任何最新的内核中找到。bpf-next 树(bpf 内核开发发生的地方)可以在](https://github.com/oracle/linux-uek/tree/uek5/master)

[https://git . kernel . org/pub/SCM/Linux/kernel/git/bpf/bpf-next . git](https://git.kernel.org/pub/scm/linux/kernel/git/bpf/bpf-next.git)

一个重要的警告；同样，下面描述的是 4.14 内核的状态。从那以后发生了很多变化；但是希望有了指向代码的指针，您将能够更好地理解这些变化是什么！

这里的目的是能够开始和 BPF 一起研究有趣的问题。然而，在我们到达那里之前，让我们看看各种各样的部分，以及它们是如何组合在一起的。

首先要问的问题是，我们能拿 BPF 怎么办？我们能写什么样的程序？

为了对此有所了解，让我们检查一下 include/uapi/Linux/bpf . h[https://github . com/Oracle/Linux-uek/blob/uek 5/master/include/uapi/Linux/bpf . h # L117](https://github.com/oracle/linux-uek/blob/uek5/master/include/uapi/linux/bpf.h#L117)中的枚举类型定义

```
enum bpf_prog_type {
 BPF_PROG_TYPE_UNSPEC,
 BPF_PROG_TYPE_SOCKET_FILTER,
 BPF_PROG_TYPE_KPROBE,
 BPF_PROG_TYPE_SCHED_CLS,
 BPF_PROG_TYPE_SCHED_ACT,
 BPF_PROG_TYPE_TRACEPOINT,
 BPF_PROG_TYPE_XDP,
 BPF_PROG_TYPE_PERF_EVENT,
 BPF_PROG_TYPE_CGROUP_SKB,
 BPF_PROG_TYPE_CGROUP_SOCK,
 BPF_PROG_TYPE_LWT_IN,
 BPF_PROG_TYPE_LWT_OUT,
 BPF_PROG_TYPE_LWT_XMIT,
 BPF_PROG_TYPE_SOCK_OPS,
 BPF_PROG_TYPE_SK_SKB,
};
```

所有这些程序类型是什么？为了理解这一点，我们将针对每种计划类型提出同样的问题:

*   我该如何处理这种程序类型？
*   我如何为这种项目类型附加我的 BPF 项目？
*   向我的程序提供了什么上下文？这里我们指的是提供给我们工作的论据和数据。
*   附加程序什么时候开始运行？理解这一点很重要，因为它给出了过滤器在网络堆栈中的应用位置。

我们现在不会担心你如何创建程序；在各种程序类型中，这方面的情况相对来说是一致的。

## 1.与套接字相关的程序类型—套接字 _ 过滤器、SK_SKB、SOCK_OPS

首先，让我们考虑与套接字相关的程序类型，它们允许我们过滤、重定向套接字数据和监视套接字事件。过滤用例与 BPF 的起源有关。在观察网络时，我们只想看到网络流量的一部分，例如，来自一个有问题的系统的所有流量。过滤器用于描述我们希望看到的流量，理想情况下，我们希望它很快，我们希望给用户一个开放式的过滤选项集。但是我们有一个问题:我们希望尽早丢弃不需要的数据，为此我们需要在内核环境中进行过滤。考虑内核解决方案的替代方案——产生将数据包复制到用户空间并在那里过滤的成本。这将非常昂贵，尤其是如果我们只想看到网络流量的一部分，而丢弃其余部分。为了实现这一点，发明了一种安全的迷你语言来将高级过滤器翻译成内核可以使用的字节码程序(称为经典 BPF，c BPF)。该语言的目标是支持一组灵活的过滤选项，同时又快速安全。用这种类似汇编语言编写的过滤器可以由 tcpdump 之类的用户空间程序来完成内核过滤。https://www.tcpdump.org/papers/bpf-usenix93.pdf 见

…关于描述这项工作的经典论文。现代 eBPF 采用了这些概念，扩展了寄存器和指令集，增加了称为映射的数据结构，极大地扩展了我们可以附加的事件种类，等等！

对于套接字过滤，常见的情况是附加到原始套接字(SOCK_RAW ),事实上，您会注意到大多数进行套接字过滤的程序都有这样一行:

```
s = socket(AF_PACKET,SOCK_RAW,htons(ETH_P_ALL));
```

创建这样一个套接字，我们指定域(AF_PACKET)、套接字类型(SOCK_RAW)和协议(所有包类型)。在 Linux 内核中，原始数据包的接收是由 raw_local_deliver()函数实现的。它由 ip_local_deliver_finish()调用，就在调用相关 ip 协议的处理程序之前，在那里数据包被传递到 TCP、UDP、ICMP 等。所以在这一点上，流量还没有与特定的套接字相关联；这发生在稍后，当 IP 堆栈找出从数据包到第 4 层协议，然后到相关套接字(如果有的话)的映射时。通过使用-d 选项，可以看到 tcpdump 生成的 cBPF 字节码。这里我想在 wlp4s0 接口上运行 tcpdump，只过滤 TCP 流量:

```
# tcpdump -i wlp4s0 -d 'tcp'
(000) ldh      [12]
(001) jeq      #0x86dd          jt 2    jf 7
(002) ldb      [20]
(003) jeq      #0x6             jt 10    jf 4
(004) jeq      #0x2c            jt 5    jf 11
(005) ldb      [54]
(006) jeq      #0x6             jt 10    jf 11
(007) jeq      #0x800           jt 8    jf 11
(008) ldb      [23]
(009) jeq      #0x6             jt 10    jf 11
(010) ret      #65535
(011) ret      #0
```

没有深入的知识，我们也能感觉到这里发生了什么。在行 000 上，我们加载以太网报头的偏移+12；以太网报头协议类型。在第 001 行，如果匹配 ETH_P_IPv6 (0x86dd) (jt 2)，我们跳转到 002，否则跳转到 007，如果不匹配(jf 7)(处理 IPv4 的情况)。

让我们先看看 IPv6 案例。在第 003 行，我们跳到 010 —成功—如果 IPv6 协议(偏移量+ 20)是 6 (IPPROTO_TCP) —第 010 行返回 65535，这是最大长度，因此我们接受数据包。否则我们跳到 004。这里我们与 0x2c 进行比较，这表明有一个 IPv6 片段头。如果是这样，我们检查片段头(偏移量 54)是否将下一个协议值指定为 IPPROTO_TCP，如果是，我们跳到 10(成功)或 11(失败)。返回 0 表示出于过滤目的丢弃数据包。

处理 IPv4 更简单；在 007 上(通过 001 上的“jf”到达)，我们检查 ETH_P_IPV4，如果找到，我们验证 IPPROTO 是 TCP。我们完事了。请记住，虽然这是 cBPFeBPF 有一个类似 x86_64 的扩展指令/操作集和额外的寄存器。

另一件需要注意的事情是——套接字过滤不同于基于 netfilter 的过滤。Netfilter 使用 NF_HOOK()定义定义了自己的一组钩子，基于 netfilter 的技术(如 ipfilter)也可以使用这些钩子来过滤流量。你可能会想——我们不能在那里也使用 eBPF 吗？你是对的！在更新的 Linux 内核中，bpfilter 正在取代 ipfilter。

记住所有这些，让我们回到检查与套接字相关的程序类型。

## 1.1 BPF _ 程序 _ 类型 _ 套接字 _ 过滤器

*   我该拿它怎么办？过滤操作包括丢弃数据包(如果程序返回 0)或修剪数据包(如果程序返回的长度小于原始长度)。见 sk_filter_trim_cap()及其对 bpf_prog_run_save_cb()的调用。请注意，我们没有修剪或丢弃原始数据包，它仍然会完整地到达预期的套接字；我们正在处理数据包元数据的副本，原始套接字可以访问该副本以实现可观察性。除了过滤流向我们套接字的数据包，我们还可以做一些有副作用的事情；例如在 BPF 地图中收集统计数据。
*   我如何附加我的程序？可以通过 SO_ATTACH_BPF setsockopt()将 BPF 程序附加到套接字上，该函数向程序传递一个文件描述符。
*   提供了什么语境？指向包含数据包元数据/数据的 struct __sk_buff 的指针。这个结构是在 include/linux/bpf.h 中定义的，包含了来自真实 sk_buff 的关键字段。bpf 验证器将对有效 __sk_buff 字段的访问转换成“真实”sk_buff 的偏移量，更多细节参见[https://lwn.net/Articles/636647/](https://lwn.net/Articles/636647/)。
*   它什么时候运行？套接字过滤器为 sock_queue_rcv_skb()中的 receive 运行，它由各种协议(TCP、UDP、ICMP、原始套接字等)调用，可用于过滤入站流量。

为了给程序看起来像什么的感觉，这里我们将创建一个过滤器，它根据协议类型修剪我们过滤的数据包数据；对于 IPv4 TCP，我们只获取 IPv4 + TCP 报头，而对于 UDP，我们只获取 IPv4 和 UDP 报头。我们不会处理 IPv4 选项，因为这是一个简单的例子，所以在所有其他情况下，我们返回 0(丢弃数据包)。

```
#include <linux/bpf.h>
#include <linux/in.h>
#include <linux/types.h>
#include <linux/string.h>
#include <linux/if_ether.h>
#include <linux/if_packet.h>
#include <linux/ip.h>
#include <linux/tcp.h>
#include <linux/udp.h>
#include "bpf_helpers.h"#ifndef offsetof
#define offsetof(TYPE, MEMBER) ((size_t) &((TYPE *)0)->MEMBER)
#endif/*
 * We are only interested in TCP/UDP headers, so drop every other protocol
 * and trim packets after the TCP/UDP header by returning length of
 * ether header + IPv4 header + TCP/UDP header.
 */SEC("socket")
int bpf_prog1(struct __sk_buff *skb)
{
        int proto = load_byte(skb, ETH_HLEN + offsetof(struct iphdr, protocol));
        int size = ETH_HLEN + sizeof(struct iphdr);switch (proto) {
        case IPPROTO_TCP:
             size += sizeof(struct tcphdr);
             break;
        case IPPROTO_UDP:
             size += sizeof(struct udphdr);
             break;
        default:
             size = 0;
             break;
        }
        return size;
}char _license[] SEC("license") = "GPL";
```

通过指定“bpf”的 arch，可以使用 LLVM/clang 将该程序编译成 BPF 字节码，一旦完成，它将包含一个带有称为“socket”的 ELF 部分的对象。这是我们的节目。下一步是使用 BPF 系统调用为程序分配一个文件描述符，然后将它附加到套接字上。在 samples/bpf 中，可以看到 bpf_load.c 扫描的是 ELF 部分，名称以“socket”为前缀的部分被识别为 BPF_PROG_TYPE_SOCKET_FILTER 程序。如果你要添加一个样本，我建议你加入 bpf_load.h，这样你就可以在你的 bpf 程序中调用 load_bpf_file()。例如，在 samples/bpf/sockex1_user.c 中，我们获取程序的文件名(sockex1)并加载 sockex1 _ kern.o 相关的 BPF 计划。然后，我们打开一个原始套接字到 loopback (lo)并将程序附加到那里:

```
snprintf(filename, sizeof(filename), "%s_kern.o", argv[0]);
        if (load_bpf_file(filename)) {
                printf("%s", bpf_log_buf);
                return 1;
        }
        sock = open_raw_sock("lo");
        assert(setsockopt(sock, SOL_SOCKET, SO_ATTACH_BPF, prog_fd,
                          sizeof(prog_fd[0])) == 0);
```

*   我该拿它怎么办？附加一个 BPF 程序来捕捉套接字操作，如连接建立，重新传输超时等。一旦捕获选项也可以通过 bpf_setsockopt()设置，例如，从不在同一子网上的系统被动建立连接时，我们可以降低 MTU，这样我们就不必担心中间路由器分割数据包。程序可以返回成功(0)或失败(负值)，并且可以设置一个应答值来指示套接字选项的期望值(例如 TCP rwnd)。参见[https://lwn.net/Articles/727189/](https://lwn.net/Articles/727189/)了解全部细节，并在 include/net/tcp.h 中查找 tcp_call_bpf()的内联定义，以了解 tcp 如何处理此类程序的执行。另一个用例是与 BPF_PROG_TYPE_SK_SKB 程序结合使用的 sockmap 更新；传递到 BPF_PROG_TYPE_SOCK_OPS 程序中的 bpf_sock_ops 结构指针用于更新 sockmap，为该套接字关联一个值。稍后的 sk_skb 程序可以引用这些值来指定通过 bpf_sk_redirect_map()调用重定向到哪个套接字。如果这听起来令人困惑，我建议看看 samples/sockmap 中的代码。
*   我如何附加我的程序？它使用 BPF 组操作附加类型附加到组文件描述符。
*   提供了什么语境？提供的参数是上下文，struct bpf_sock_ops *..Op 字段指定操作，BPF _ 袜子 _OPS_RWND_INIT，BPF _ 袜子 _OPS_TCP_CONNECT_CB 等。回复字段可用于向呼叫者指示参数集的新值。

```
/* User bpf_sock_ops struct to access socket values and specify request ops
 * and their replies.
 * Some of this fields are in network (bigendian) byte order and may need
 * to be converted before use (bpf_ntohl() defined in samples/bpf/bpf_endian.h).
 * New fields can only be added at the end of this structure
 */
struct bpf_sock_ops {
    __u32 op;
    union {
        __u32 reply;
        __u32 replylong[4];
    };
    __u32 family;
    __u32 remote_ip4;    /* Stored in network byte order */
    __u32 local_ip4;    /* Stored in network byte order */
    __u32 remote_ip6[4];    /* Stored in network byte order */
    __u32 local_ip6[4];    /* Stored in network byte order */
    __u32 remote_port;    /* Stored in network byte order */
    __u32 local_port;    /* stored in host byte order */
};
```

*   它什么时候运行？根据上面的文章，与期望在代码库中的特定位置被调用的其他 BPF 程序类型不同，SOCK_OPS 程序可以在不同的位置被调用，并使用“op”字段来指示该上下文。请参见 include/uapi/linux/bpf.h，了解列举的 BPF_SOCK_OPS_*定义，但它们包括像重新传输超时、连接建立等事件。

## 1.3 BPF_PROG_TYPE_SK_SKB

*   我该拿它怎么办？允许用户访问 skb 和套接字详细信息，如端口和 IP 地址，以支持 skb 在套接字之间的重定向。参见[https://lwn.net/Articles/731133/](https://lwn.net/Articles/731133/)。此功能与 sockmap 结合使用，sock map 是一种特殊用途的 BPF 地图，包含对套接字结构和相关值的引用。sockmaps 用于支持重定向。程序被附加，bpf_sk_redirect_map()助手可用于执行重定向。一般方法是，我们用 sock_ops BPF 程序捕获套接字创建事件，将这些事件的值与 sockmap 相关联，然后使用 sk_skb 检测点的数据来通知套接字重定向——这称为裁决，该程序通过 BPF _ SK _ SKB _ 流 _ 裁决附加到 sockmap。判断可以是 __SK_DROP、__SK_PASS 或 __SK_REDIRECT。这种程序类型的另一个用例是在 strparser 框架中([https://www . kernel . org/doc/Documentation/networking/strparser . txt](https://www.kernel.org/doc/Documentation/networking/strparser.txt))。BPF 程序可用于通过读操作、判断和读完成的回调来解析消息流。TLS 和 KCM 使用流解析。
*   我如何附加我的程序？重定向程序作为 BPF _ SKB _ 流 _ 判决附加到一个 sockmap 它应该返回 bpf_sk_redirect_map()的结果。strparser 程序通过 BPF _ SK _ SKB _ 流 _ 解析器连接，应该返回解析的数据长度。
*   提供了什么语境？指向包含数据包元数据/数据的 struct __sk_buff 的指针。然而，sk_skb 程序类型可以访问更多的字段。include/linux/bpf.h 中记录了一组额外的可用字段，如下所示:

```
/* Accessed by BPF_PROG_TYPE_sk_skb types from here to ... */
__u32 family;
__u32 remote_ip4;   /* Stored in network byte order */
__u32 local_ip4;    /* Stored in network byte order */
__u32 remote_ip6[4];    /* Stored in network byte order */
__u32 local_ip6[4]; /* Stored in network byte order */
__u32 remote_port;  /* Stored in network byte order */
__u32 local_port;   /* stored in host byte order */
/* ... here. */
```

因此，从上面我们可以看到，我们可以收集关于套接字的信息，因为上面代表了唯一标识套接字的关键信息(协议已经在 struct __sk_buff 的全局可访问部分中可用)。

*   它什么时候运行？流解析器可以通过 sockmap 的 BPF _ SKB _ 流解析器附件连接到套接字，解析器通过 kernel/bpf/sockmap.c 中的 smap_parse_func_strparser()在套接字接收上运行。BPF_SK_SKB_STREAM_VERDICT 也连接到 sockmap，并通过 smap_verdict_func()运行。

## 2.交通控制子系统程序

接下来让我们检查一下与 TC 内核包调度子系统相关的程序类型。有关一般介绍，请参见 tc(8)联机帮助页，有关 bpf 的详细信息，请参见 tc-bpf(8)。

## 2.1 tc_cls_act : qdisc 分类器

*   我该拿它怎么办？tc_cls_act 允许我们使用 BPF 程序作为 Linux QoS 子系统 tc 中的分类器和动作。更好的是，tc(8)命令还支持 eBPF，因此我们可以直接加载 BPF 程序作为入站(入口)和出站(出口)流量的分类器和操作。参见[http://man7.org/linux/man-pages/man8/tc-bpf.8.html](http://man7.org/linux/man-pages/man8/tc-bpf.8.html)了解如何使用 tc 的 BPF 功能。tc 程序可以分类、修改、重定向或丢弃数据包。
*   我如何附加我的程序？可以使用 TC(8)；详见 tc-bpf(8)。基础是我们为网络设备创建一个“cls act”qdisc，并通过指定 BPF 对象和相关的 ELF 部分来添加入口和出口分类器/操作。例如，从 myprog _ kernel . o(bpf-字节码编译的目标文件)向 ELF 节 my_elf_sec 中的 eth0 添加入口分类器:

```
# tc qdisc add dev eth0 clsact
# tc filter add dev eth0 ingress bpf da obj myprog_kernel.o sec my_elf_sec
```

*   提供了什么语境？指向 struct __sk_buff 数据包元数据/数据的指针。
*   它什么时候开始运行？如上所述，分类器 qdisc 必须添加，一旦它是我们可以附加 BPF 程序来分类入站和出站流量。在实现方面，act_bpf.c 和 cls_bpf.c 实现动作/分类器模块。在 ingress/gress sch _ handle _ ingress()/egress()上调用 tcf_classify()。在入口的情况下，我们通过核心网络接口接收功能进行分类，因此我们在驱动程序处理数据包之后，但在 IP 等看到它之前获取数据包。在出口端，过滤在提交到设备队列进行传输之前完成。

## 3.xdp:Xpress 数据路径

XDP 的主要设计目标是在网络数据路径中引入可编程性。目的是提供尽可能靠近设备的 XDP 挂钩(在操作系统创建 sk_buff 元数据之前),以最大化性能，同时支持跨设备的公共基础设施。要像这样支持 XDP，需要改变驱动程序。有关示例，请参见 drivers/net/Ethernet/Broadcom/bnxt/bnxt _ xdp . c，添加了一个 bpf 网络设备 op (ndo_bpf)。对于 bnxt，它支持 XDP _ 设置 _ 程序和 XDP _ 查询 _ 程序操作；前者为 XDP 配置设备，保留振铃并将程序设置为活动。后者返回 BPF 程序 id。如果需要，实际的发送/接收函数提供并调用特定于 BPF 的发送和接收函数。

## 3.1 BPF_PROG_TYPE_XDP

*   我该拿它怎么办？XDP 允许在分配数据包元数据(struct sk_buff)之前尽早访问数据包数据。因此，这是一个进行 DDoS 缓解或负载平衡的有用地方，因为这样的活动通常可以避免 sk_buff 分配的昂贵开销。XDP 通过 BPF 钩子支持内核的运行时编程，但是通过与内核本身协同工作；即不是内核旁路机制。支持的操作包括 XDP _ 传递(照常传递到网络处理)、XDP _ 丢弃(丢弃)、XDP _ 发送(传输)和 XDP _ 重定向。有关“enum xdp_action”，请参见 include/uapi/linux/bpf.h。
*   我如何附加我的程序？通过 netlink 套接字消息。创建并绑定一个 netlink 套接字 socket(AF_NETLINK，SOCK_RAW，NETLINK_ROUTE)，然后我们发送一个 NLA _ F _ NESTED | 43；类型的 NETLINK 消息。这指定了 XDP 消息。该消息包含 BPF fd、接口索引(ifindex)。有关示例，请参见 samples/bpf/bpf_load.c。
*   提供了什么语境？xdp 元数据指针；结构 xdp_md *。XDP 元数据是故意轻量级的；来自 include/uapi/linux/bpf.h:

```
/* user accessible metadata for XDP packet hook
 * new fields must be added to the end of this structure
 */
struct xdp_md {
 __u32 data;
 __u32 data_end;
};
```

*   它什么时候开始运行？“真正的”XDP 是在驱动程序级别实现的，发送/接收环资源被预留给 XDP 使用。对于驱动程序不支持 XDP 的情况，可以选择使用“通用”XDP，它在 net/core/dev.c 中实现。这样做的缺点是我们不能绕过 skb 分配，它只允许我们对这样的设备使用 XDP。

## 4.k 探测器、跟踪点和性能事件

kprobes、tracepoints 和 perf 事件都提供了内核工具。

kprobes—[https://www.kernel.org/doc/Documentation/kprobes.txt](https://www.kernel.org/doc/Documentation/kprobes.txt)—允许插装特定的函数—可以通过 k probes 监控函数的进入，以及函数中的大多数指令，或者可以通过 kretprobe 插装进入/返回。当这些探测器中的一个被启用时，启用点处的代码被保存，并用断点指令替换。当这个断点被命中时，一个 trap 指令被生成，寄存器被保存，我们转移到相关的检测处理程序。例如，kprobe 由 kprobe_dispatcher()处理，它获取 kprobe 和 register 上下文的地址作为参数。kretprobes 通过 kprobes 实现；kprobe 在进入时触发并修改返回地址，保存原始地址并用检测处理程序的位置替换它。

**跟踪点**—[https://www . kernel . org/doc/Documentation/trace/trace points . rst](https://www.kernel.org/doc/Documentation/trace/tracepoints.rst)—类似，但不同于在特定指令处被启用，它们在代码中的站点处被显式标记，并且如果被启用，可以用于收集那些感兴趣的站点处的调试信息。同一个跟踪点可以在多个地方声明；例如，在 net/mac80211/driver-ops.c 中的多个地方调用了 trace_drv_return_int()。

性能事件—[https://perf.wiki.kernel.org/index.php/Main_Page](https://perf.wiki.kernel.org/index.php/Main_Page)——是 eBPF 支持这些程序类型的基础。BPF 本质上是在现有的基础设施上进行事件采样，允许我们将程序附加到感兴趣的性能事件，包括 kprobes、uprobes、tracepoints 等，以及其他软件事件，实际上也可以监控硬件事件。

正是这些插装点赋予了 BPF 成为通用跟踪工具的能力，以及支持最初以网络为中心的用例(如套接字过滤)的手段。

## 4.1 BPF _ 程序 _ 类型 _ k 探针

*   我该拿它怎么办？通过 kprobe 的任何内核函数(除了少数例外)中的工具代码，或者通过 kretprobe 的工具进入/返回。k[ret]probe_perf_func()执行一个附加到探测点的 BPF 程序。请注意，该程序类型也可用于附加到 u[ret]探测器——详情请参见[https://www . kernel . org/doc/Documentation/trace/uprobetracer . txt](https://www.kernel.org/doc/Documentation/trace/uprobetracer.txt)
*   我如何附加我的程序？当 kprobe 通过 sysfs 创建时，它有一个与之关联的 id，存储在/sys/kernel/debug/tracing/events/[uk]probe//id，/sys/kernel/debug/tracing/events/[uk]ret probe/probe name/id 中。[https://www . kernel . org/doc/Documentation/trace/kprobetrace . txt](https://www.kernel.org/doc/Documentation/trace/kprobetrace.txt)包含如何使用 sysfs 创建 kprobe 的详细信息。例如，要在 TCP _ return _ skb()的入口创建一个名为“myprobe”的探测器并检索其 id:

```
# echo 'p:myprobe tcp_retransmit_skb' > /sys/kernel/debug/tracing/kprobe_events
# cat /sys/kernel/debug/tracing/events/kprobes/myprobe/id
2266
```

我们可以使用该探测器 id 打开一个性能事件，启用它，并将该性能事件的 BPF 程序设置为我们的程序。请参阅 load_and_attach()函数中的 samples/bpf/bpf_load.c，了解如何对 k[ret]探测器执行此操作。代码可能看起来像这样:

```
struct perf_event_attr attr;
int eventfd, programfd;
int probeid;/* Load BPF program and assign programfd to it; and get probeid of probe from sysfs */
attr.type = PERF_TYPE_TRACEPOINT;
attr.sample_type = PERF_SAMPLE_RAW;
attr.sample_period = 1;
attr.wakeup_events = 1;
attr.config = probeid;
eventfd = sys_perf_event_open(&attr, -1, 0, programfd, 0);
if (eventfd < 0)
        return -errno;
if (ioctl(eventfd, PERF_EVENT_IOC_ENABLE, 0)) {
        close(eventfd);
        return -errno;
}
if (ioctl(eventfd, PERF_EVENT_IOC_SET_BPF, programfd)) {
        close(eventfd);
        return -errno;
}
```

*   提供了什么语境？一个结构 pt_regs *ctx，可以通过它访问寄存器。其中大部分是特定于平台的，但也存在一些通用函数，如 regs_return_value(regs)，它返回寄存器的值，而不是保存函数返回值(x86 上的 regs→ax)。
*   它什么时候开始运行？当探测器被启用并且命中断点时，k[ret]probe_perf_func()执行一个通过 trace_call_bpf()附加到探测点的 BPF 程序。u[ret]probe_perf_func()的情况类似。

## 4.2 BPF _ 程序 _ 类型 _ 跟踪点

*   我该拿它怎么办？内核代码中的工具跟踪点。跟踪点可以像 kprobes 一样通过 sysfs 以类似的方式启用。可以在/sys/kernel/debug/tracing/events 下看到跟踪事件的列表。
*   我如何附加我的程序？正如我们在上面看到的，当跟踪点通过 sysfs 创建时，它有一个与之相关联的 id。我们可以使用该探测器 id 打开一个性能事件，启用它，并将该性能事件的 BPF 程序设置为我们的程序。有关如何对跟踪点执行此操作的信息，请参见 load_and_attach()函数中的 samples/bpf/bpf _ load . c；上面的 kprobes 代码片段也适用于跟踪点。作为显示如何启用跟踪点的示例，我们在这里将 net/net_dev_xmit 跟踪点启用为“myprobe2 ”,并检索其 id:

```
# echo ‘p:myprobe2 trace:net/net_dev_xmit’ > /sys/kernel/debug/tracing/kprobe_events
# cat /sys/kernel/debug/tracing/events/kprobes/myprobe2/id
2270
```

*   提供了什么语境？特定跟踪点提供的上下文；参数和数据类型与跟踪点定义相关联。
*   它什么时候开始运行？当启用并命中跟踪点时，perf_trace_()(参见 include/trace/perf.h 中的定义)调用 perf_trace_run_bpf_submit()，该函数将通过 trace_call_bpf()调用 bpf 程序。

## 4.3 BPF _ 程序 _ 类型 _ PROG _ 事件

*   我该拿它怎么办？仪器软件和硬件性能事件。这些事件包括系统调用、定时器到期、硬件事件采样等。硬件事件包括 PMU 事件(处理器监控单元),它告诉我们完成了多少条指令等。性能事件监视可以针对特定的进程或组、处理器，并且可以为分析指定一个采样周期。
*   我如何附加我的程序？与上述相似的模型；我们用属性集调用 perf_event_open()，通过 PERF_EVENT_IOC_ENABLE ioctl()启用 perf 事件，通过 PERF_EVENT_IOC_SET_BPF ioctl()设置 bpf 程序。有关 PMU(处理器监控单元)性能事件示例，请参见 samples/bpf/sampleip_user.c 中的这些片段:

```
...
struct perf_event_attr pe_sample_attr = {
        .type = PERF_TYPE_SOFTWARE,
        .freq = 1,
        .sample_period = freq,
        .config = PERF_COUNT_SW_CPU_CLOCK,
        .inherit = 1,
};
        ...
pmu_fd[i] = sys_perf_event_open(&pe_sample_attr, -1 /* pid */, i,
                                -1 /* group_fd */, 0 /* flags */);
if (pmu_fd[i] < 0) {
        fprintf(stderr, "ERROR: Initializing perf sampling\n");
        return 1;
}assert(ioctl(pmu_fd[i], PERF_EVENT_IOC_SET_BPF,
             prog_fd[0]) == 0);
assert(ioctl(pmu_fd[i], PERF_EVENT_IOC_ENABLE, 0) == 0);
...
```

*   它什么时候开始运行？取决于性能事件触发和选择的采样速率，由性能事件属性结构中的 freq 和 sample_period 字段指定。

## 5.与 cgroups 相关的程序类型

CGroups 用于处理资源分配，允许或拒绝进程组访问系统资源，如 CPU、网络带宽等。cgroups 的一个关键用例是容器；容器的资源访问是通过 cgroups 来限制的，而它的活动是通过不同类别的名称空间(网络名称空间、进程 ID 名称空间等)来隔离的。在 BPF 的环境中，我们可以创建允许或拒绝访问的 e BPF 程序。在 include/linux/bpf-cgroup.h 中，我们可以看到执行 socket/skb 程序的定义，其中 __cgroup_bpf_run_filter_skb 被调用，并被包装在检查 cgroup BPF 是否已启用中:

```
#define BPF_CGROUP_RUN_PROG_INET_INGRESS(sk, skb)                  \
({                                                                 \
    int __ret = 0;                                                 \
    if (cgroup_bpf_enabled)                                        \
        __ret = __cgroup_bpf_run_filter_skb(sk, skb,               \
                            BPF_CGROUP_INET_INGRESS);              \
                                                                   \
    __ret;                                                         \
})#define BPF_CGROUP_RUN_SK_PROG(sk, type)                           \
({                                                                 \
    int __ret = 0;                                                 \
    if (cgroup_bpf_enabled) {                                      \
        __ret = __cgroup_bpf_run_filter_sk(sk, type);              \
    }                                                              \
    __ret;                                                         \
})
```

如果启用了 cgroups，我们将我们的程序附加到 cgroup，它将在相关的钩子点执行。要了解钩子的完整列表，请参考 include/uapi/linux/bpf.h，并检查 BPF_CGROUP_*定义的枚举类型“bpf_attach_type”。

## 5.1 BPF _ 程序 _ 类型 _ 群组 _SKB

*   我该拿它怎么办？允许或拒绝 IP 出口/入口(BPF _ 群组 _ 网络 _ 入口/BPF _ 群组 _ 网络 _ 出口)上的网络访问。BPF 程序应该返回 1 以允许访问。任何其他值都会导致函数 __cgroup_bpf_run_filter_skb()返回-EPERM，这将传播到调用者，从而丢弃数据包。
*   我如何附加我的程序？程序被附加到一个特定的 cgroup 的文件描述符上。
*   提供了什么语境？相关的 skb。
*   它什么时候开始运行？对于 inet ingress，sk_filter_trim_cap()(见上)包含对 BPF _ CGROUP _ RUN _ PROG _ INET _ INGRESS(sk，skb)的调用；如果返回非零值，则将错误传播给调用者(例如 __sk_receive_skb())，并丢弃和释放数据包。在 egress 上采取了类似的方法，但是在 ip[6]_finish_output()中。

## 5.2 BPF _ 程序 _ 类型 _ 群组 _ 袜子

*   我该拿它怎么办？在各种与套接字相关的事件中允许或拒绝网络访问(BPF_CGROUP_INET_SOCK_CREATE，BPF_CGROUP_SOCK_OPS)。如上所述，BPF 程序应该返回 1 以允许访问。任何其他值都会导致函数 __cgroup_bpf_run_filter_sk()返回-EPERM，这将传播到调用者，从而丢弃数据包。
*   我如何附加我的程序？程序被附加到一个特定的 cgroup 的文件描述符上。
*   提供了什么语境？相关插座(sk)。
*   它什么时候开始运行？在套接字创建时，在 inet_create()中，我们用套接字作为参数调用 BPF_CGROUP_RUN_PROG_INET_SOCK()，如果该函数失败，套接字将被释放。

## 6.轻量级隧道程序类型。

轻量级隧道[https://lwn.net/Articles/650778/](https://lwn.net/Articles/650778/)是一种通过将封装指令附加到路由上来实现隧道的简单方法。补丁描述中的例子让事情更清楚:iproute 示例(不含 BPF):

```
VXLAN:
  ip route add 40.1.1.1/32 encap vxlan id 10 dst 50.1.1.2 dev vxlan0MPLS:
  ip route add 10.1.1.0/30 encap mpls 200 via inet 10.1.1.1 dev swp1
```

例如，我们告诉 Linux，对于发往 40.1.1.1/32 地址的流量，我们希望使用 VXLAN ID 10 和目的 IPv4 地址 50.1.1.2 进行封装。BPF 程序可以在出站/传输时进行封装(入站数据包是只读的)。详见 https://lwn.net/Articles/705609/的[。与 tc 类似，iproute eBPF 支持允许我们直接附加 eBPF 程序 ELF 部分:](https://lwn.net/Articles/705609/)

```
ip route add 192.168.253.2/32 \
     encap bpf out obj lwt_len_hist_kern.o section len_hist \
     dev veth0
```

## 6.1 BPF_PROG_TYPE_LWT_IN

*   我该拿它怎么办？检查入站数据包的轻量级隧道解封装。
*   我如何附加我的程序？通过“ip 路由添加”。

```
# ip route add <route+prefix> encap bpf in obj <bpf object file.o> section <ELF section> dev <device>
```

*   提供了什么语境？指向 sk_buff 的指针。
*   它什么时候开始运行？via lw tunnel _ input()；该函数支持多种封装类型，包括 BPF。BPF 案例在 net/core/lwt_bpf.c 中运行 bpf_input，不允许重定向。

## 6.2 BPF_PROG_TYPE_LWT_OUT

*   我该拿它怎么办？为出站的特定目的地路由实现轻量级隧道封装。禁止封装
*   我如何附加我的程序？通过“ip 路由添加”:

```
# ip route add <route+prefix> encap bpf out obj <bpf object file.o> section <ELF section> dev <device>
```

*   提供了什么语境？指向结构 __sk_buff 的指针
*   它什么时候开始运行？通过 lwtunnel_output()。

## 6.3 BPF _ 程序 _ 类型 _LWT_XMIT

*   我该拿它怎么办？在传输时为轻量级隧道实现封装/重定向。
*   我如何附加我的程序？通过“ip 路由添加”

```
# ip route add <route+prefix> encap bpf xmit obj <bpf object file.o> section <ELF section> dev <device>
```

*   提供了什么语境？指向结构 __sk_buff 的指针
*   它什么时候开始运行？通过 lwtunnel_xmit()。

## 摘要

希望这个程序类型的总结是有用的。您已经看到了 BPF 安全的内核可编程环境可以以各种有趣的方式使用！在下一期文章中，我将讲述在各种程序类型中有哪些 [BPF 助手函数可用。](http://blogs.oracle.com/linux/notes-on-bpf-2)

*本帖原载于 blogs.oracle.com/linuxkernel*[](https://blogs.oracle.com/linux/notes-on-bpf-1)**…**