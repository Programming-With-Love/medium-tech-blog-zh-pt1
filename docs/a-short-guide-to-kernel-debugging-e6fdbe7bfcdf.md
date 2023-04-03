# 内核调试简明指南

> 原文：<https://medium.com/square-corner-blog/a-short-guide-to-kernel-debugging-e6fdbe7bfcdf?source=collection_archive---------0----------------------->

## 一个关于在生产系统上发现内核错误的故事

本杰明·韦斯特写的。

> 注意，我们已经行动了！如果您想继续了解 Square 的最新技术内容，请访问我们在 https://developer.squareup.com/blog[的新家](https://developer.squareup.com/blog)

*这篇文章最初是一封内部邮件，描述了在 Square 的生产主机上发现的一个问题。这篇文章是作为调试 Linux 内核的工具和实践的介绍而写的，尽管它假设读者对 C 程序是如何构造的相当熟悉。*

当我在我们的部署基础设施上待命时，一个问题出现了:一个服务的最新版本没有正确部署。日志文件告诉我有一台主机似乎卡住了。登录机器后环顾四周，似乎有些事情很不对劲:

```
$ date
Thu Sep 10 16:04:00 UTC 2015
$ ps -fC cleanup_old.sh
UID    PID PPID  C STIME TTY      TIME CMD
root  5085 5081  0 Aug27 ?    00:00:10 /bin/sh ./cleanup_old.sh myapp
$ ps -fC rm
UID    PID PPID  C STIME TTY      TIME CMD
root  5109 5099  0 Aug27 ?    00:00:01 rm -rf /opt/myapp-1.0.4
$ sudo kill -9 5085 5109
$ ps -fC cleanup_old.sh
UID    PID PPID  C STIME TTY      TIME CMD
$ ps -fC rm
UID    PID PPID  C STIME TTY      TIME CMD
root  5109 5099  0 Aug27 ?    00:00:01 rm -rf /opt/myapp-1.0.4
```

杀死一个管理脚本会打乱部署过程，但会留下一个孤立的、几周前的、不可杀死的 rm。我眼前的问题得到了缓解，但显然还有一个更深层次的问题。发生了什么事？

我知道这个进程没有在用户空间中旋转——在我试图杀死它之前，这个进程没有在 top 的输出中显示为一个重度 CPU 用户，之后它也没有死亡——所以它一定是在对内核的系统调用中被阻塞了。通常，只要知道阻塞的是哪个函数，就可以快速诊断出阻塞的进程。为了寻找一个快速的解决方案，我查看了该进程的内核调用堆栈:

```
$ sudo cat /proc/5109/stack
[<ffffffff811979bc>] vfs_unlink+0x5c/0xf0
[<ffffffff8119aac3>] do_unlinkat+0x163/0x260
[<ffffffff8119adf2>] sys_unlinkat+0x22/0x40
[<ffffffff8100b072>] system_call_fastpath+0x16/0x1b
[<ffffffffffffffff>] 0xffffffffffffffff
```

当然，rm 被阻止解除文件链接；它没有做太多其他的事情。解除链接的哪一部分被阻止了？它要删除哪个文件？那个文件有什么特别的？这些答案依赖于这个进程的内核调用堆栈中的数据，而不仅仅是它的函数列表。我需要查看内核的内存。

在这一点上，很明显简单的诊断不能揭示问题。重启会很快“修复”它，但不会阻止这种情况再次发生。我叹了口气，留出一天的剩余时间去寻找根本原因。

调试 Linux 内核比调试普通进程更复杂。以一个要解决的有趣问题为例，让我们来看看一些有助于理解内核内部的工具、技术和数据结构。希望下次您自己的问题离开用户空间时，这个讨论可以成为一个起点。

我们来调试内核吧！

# 设置内核调试环境

检查进程状态的一个典型方法是给它附加一个调试器，比如 gdb。内核也没什么不同。就像用户空间进程一样，必须构建二进制文件来支持调试。我们正在调试的机器运行 RedHat 6.5，幸运的是，RedHat 使用一些调试选项集构建了它的内核。为了节省空间，调试符号与主内核二进制文件是分开的，所以我们需要安装它们。

```
$ uname -r
2.6.32-431.23.3.el6.x86_64
$ sudo yum --enablerepo=debug install kernel-debuginfo-2.6.32-431.23.3.el6 kernel-debuginfo-common-x86_64-2.6.32-431.23.3.el6
```

如果我们想了解我们所看到的，我们必须安装内核的源代码。调试包已经在/usr/src/debug/…中安装了源代码。如果您想要不带调试包的源代码(可能在另一台机器上)，它可以从源 RPM 生成。 [(1)](https://corner.squareup.com/2015/10/short-guide-to-kernel-debugging.html#correction-1-text)

```
$ curl -LO ftp://ftp.redhat.com/.../kernel-2.6.32-431.23.3.el6.src.rpm
$ rpm -Uvh kernel-2.6.32-431.23.3.el6.src.rpm
$ sudo yum install rpm-build yum-utils
$ sudo yum-builddep rpmbuild/SPECS/kernel.spec
$ [rpmbuild](http://www.rpm.org/max-rpm-snapshot/rpmbuild.8.html) -pb rpmbuild/SPECS/kernel.spec
```

现在，我们如何连接 gdb？根据机器的稳定性、是否可以暂停/重启以及是否在虚拟机中运行，可以使用许多方法。这是一台裸机，我不想拆卸，所以我们将使用 RedHat 的[崩溃](http://people.redhat.com/anderson/crash_whitepaper/)工具。Crash 是围绕 gdb 的一个包装器，它为理解内核数据结构增加了一些通用函数。它还能够在不干扰内核的情况下调试正在运行的内核:这正是我们所需要的。

```
$ sudo crash
  KERNEL: /usr/lib/debug/lib/modules/2.6.32-431.23.3.el6.x86_64/vmlinux
DUMPFILE: /dev/crash
    DATE: Thu Sep 10 17:35:01 2015
 RELEASE: 2.6.32-431.23.3.el6.x86_64
 VERSION: #1 SMP Thu Jul 31 17:20:51 UTC 2014
 MACHINE: x86_64
     PID: 3943
 COMMAND: "crash"
    TASK: ffff880494dd6080  [THREAD_INFO: ffff88094a1ea000]
     CPU: 7
   STATE: TASK_RUNNING (ACTIVE)crash>
```

# 了解内核数据和 x86 ASM

让我们从一个简单的任务开始:我们将验证程序的堆栈仍然和以前一样:

```
crash> [set](http://people.redhat.com/anderson/crash_whitepaper/help_pages/set.html) 5109
    PID: 5109
COMMAND: "rm"
   TASK: ffff8802d0e5a080  [THREAD_INFO: ffff88057f85a000]
    CPU: 18
  STATE: TASK_UNINTERRUPTIBLE
crash> [bt](http://people.redhat.com/anderson/crash_whitepaper/help_pages/bt.html)
PID: 5109  TASK: ffff8802d0e5a080  CPU: 18  COMMAND: "rm"
 #0 [ffff88057f85bce8] schedule at ffffffff81528bc0
 #1 [ffff88057f85bdb0] __mutex_lock_slowpath at ffffffff8152a36e
 #2 [ffff88057f85be20] mutex_lock at ffffffff8152a20b
 #3 [ffff88057f85be40] vfs_unlink at ffffffff811979bc
 #4 [ffff88057f85be70] do_unlinkat at ffffffff8119aac3
 #5 [ffff88057f85bf70] sys_unlinkat at ffffffff8119adf2
 #6 [ffff88057f85bf80] system_call_fastpath at ffffffff8100b072
    RIP: 00000033b36dc68d  RSP: 00007ffff7f1ec48  RFLAGS: 00010202
    RAX: 0000000000000107  RBX: ffffffff8100b072  RCX: 0000000000000032
    RDX: 0000000000000000  RSI: 0000000001982a70  RDI: 0000000000000006
    RBP: 00007ffff7f1efa0   R8: 0000000000000003   R9: 0000000000000000
    R10: 0000000000000100  R11: 0000000000000246  R12: ffffffff8119adf2
    R13: ffff88057f85bf78  R14: 0000000000000000  R15: 0000000001979060
    ORIG_RAX: 0000000000000107  CS: 0033  SS: 002b
```

当一个进程进行系统调用时，它所有的寄存器都保存在其栈底；这就是我们在这里看到的。我们可以用这个来使用 [amd64 系统调用约定](http://lxr.free-electrons.com/source/arch/x86/kernel/entry_64.S?v=2.6.32#L431)重构系统调用参数。我们还可以看到进程的 [struct task_struct](http://lxr.free-electrons.com/source/include/linux/sched.h?v=2.6.32#L1217) 和 [struct thread_info](http://lxr.free-electrons.com/source/arch/x86/include/asm/thread_info.h?v=2.6.32#L26) 指针。这两个结构是内核用来跟踪系统中每个进程/线程的。(在 Linux 中，“线程”只是碰巧共享相同地址空间、异常处理程序、文件表等的另一个进程..)

查看整个堆栈，可以清楚地看到进程正在等待获取一个互斥体(mutex_lock()调用)。现在有行号了:

```
crash> bt -l
PID: 5109  TASK: ffff8802d0e5a080  CPU: 18  COMMAND: "rm"
...
 #2 [ffff88057f85be20] mutex_lock at ffffffff8152a20b
    /arch/x86/include/asm/thread_info.h: 216
 #3 [ffff88057f85be40] vfs_unlink at ffffffff811979bc
    /fs/namei.c: 2810
...
```

如果我们查看“/fs/namei.c”来找出它阻塞了哪个 mutex_lock()调用:

```
crash> l fs/namei.c:2810
...
2809            mutex_lock(&dentry->d_inode->i_mutex);
2810            if (d_mountpoint(dentry))
...
```

2810 线堵？不，回溯显示返回地址在 2810 行。互斥调用在那之前，在第 2809 行。

一个 [struct dentry](http://lxr.free-electrons.com/source/include/linux/dcache.h?v=2.6.32#L89) 是 Linux 用来跟踪和缓存文件系统名称的数据结构。它将某个目录中的名称与保存数据的实际文件 [struct inode](http://lxr.free-electrons.com/source/include/linux/fs.h#L584) 相关联。通常，文件在文件系统中只有一个名称(和一个对应的条目)。硬链接为单个文件创建附加名称；删除一个打开的文件允许文件没有名字(直到文件句柄关闭，它才真正被删除)。

所以我们的进程阻止获取一个 inode 的互斥体。哪个 inode？我们可以从 sys_unlinkat()的参数中提取文件名，但这不会将我们引向 inode 数据结构。让我们看看能否从调用堆栈中访问一些函数参数。

```
crash> up
up = $1 =
 {void (struct semaphore *)} 0xffffffff810a0f30 <up>
crash> gdb up
crash: prohibited gdb command: up
```

Crash 不支持 gdb 的标准“up”命令来遍历调用栈。相反，我们可以打印一个包含堆栈内存的“完整”回溯。

```
crash> bt -f
PID: 5109  TASK: ffff8802d0e5a080  CPU: 18  COMMAND: "rm"
 #0 [ffff88057f85bce8] schedule at ffffffff81528bc0
    ffff88057f85bcf0: 0000000000000086 0000000000000000
    ffff88057f85bd00: ffffffffa0230da4 ffff88057f85bd18
...
```

通常情况下，gdb 会为我们解释内存。二进制的[调试信息](http://www.dwarfstd.org/)告诉 gdb 如何从任何指令中找到任何保存的变量或寄存器的位置。然而，crash 没有公开这个功能，迫使我们在寻找有用的上下文时手动展开调用堆栈。

根据 [amd64 调用约定](https://en.wikipedia.org/wiki/X86_calling_conventions#System_V_AMD64_ABI)，大多数函数参数都是通过寄存器传递的，而不会显式保存在其他任何地方。我们可以访问任务的堆栈，但是不能获得中间寄存器状态。我们恢复上下文的最大希望是在调用堆栈中寻找一个函数，这个函数碰巧将一个重要的变量保存到堆栈中。

在查看了几个反汇编的函数之后，我们发现 do_unlinkat()调用 vfs_unlink():

```
crash> [dis](http://people.redhat.com/anderson/crash_whitepaper/help_pages/dis.html) do_unlinkat
...
0xffffffff8119aaa2 <do_unlinkat+322>:   mov    -0xc8(%rbp),%rax
0xffffffff8119aaa9 <do_unlinkat+329>:   **mov    %rdx,%rsi**
0xffffffff8119aaac <do_unlinkat+332>:   mov    0x10(%rax),%rdi
0xffffffff8119aab0 <do_unlinkat+336>:   mov    %rcx,-0xe8(%rbp)
0xffffffff8119aab7 <do_unlinkat+343>:   **mov    %rdx,-0xe0(%rbp)**
0xffffffff8119aabe <do_unlinkat+350>:   callq  0xffffffff81197960 <vfs_unlink>
0xffffffff8119aac3 <do_unlinkat+355>:   mov    -0xe0(%rbp),%rdx
...
```

这个程序集块对应于下面的代码段。

```
crash> [sym](http://people.redhat.com/anderson/crash_whitepaper/help_pages/sym.html) 0xffffffff8119aaa2
ffffffff8119aaa2 (t) do_unlinkat+322 fs/namei.c: 2869
crash> l fs/namei.c:2869
2869:         error = vfs_unlink(nd.path.dentry->d_inode, **dentry**);
2870: exit2:
2871:         dput(dentry);
```

vfs_unlink()的第二个参数是一个 dentry 指针，它可以告诉我们哪个文件正在被删除。我们从调用约定中知道，第二个参数是在%rsi 寄存器中传递的。它的值来自%rdx，在函数调用之前保存到堆栈中，我们可以在那里恢复它。 [(2)](https://corner.squareup.com/2015/10/short-guide-to-kernel-debugging.html#correction-2-text)

现在，我们可以查看任务堆栈的完整内存转储，提取帧指针(FP ),并检索 dentry:

```
crash> bt -f
PID: 5109  TASK: ffff8802d0e5a080  CPU: 18  COMMAND: "rm"
...
    ffff88057f85be68: **ffff88057f85bf68** ffffffff8119aac3 <- last frame's FP
 #4 [ffff88057f85be70] do_unlinkat at ffffffff8119aac3
    ffff88057f85be78: ffff88004b3d2248 ffff8808a3dbf588
    ffff88057f85be88: **ffff8809c6c82a40** ffff880b92bd4000 <- FP - 0xe0
...
```

现在我们可以转储相关的文件系统数据结构:

```
crash> [struct](http://people.redhat.com/anderson/crash_whitepaper/help_pages/struct.html) dentry.d_name,d_parent,d_inode **ffff8809c6c82a40**
  d_name = {
    len = 7,
    name = 0xffff8809c6c82ae0 "fire.js"
  }
  d_parent = **0xffff8809c6c82b00**
  d_inode = **0xffff8808a3dbf588**
crash> struct dentry.d_name **0xffff8809c6c82b00**
  d_name = {
    len = 2,
    name = 0xffff8809c6c82ba0 "js"
  }
crash> struct inode.i_mutex **0xffff8808a3dbf588**
  i_mutex = {
    count = {
      counter = -1
    },
    wait_lock = {
      raw_lock = {
        slock = 196611
      }
    },
    wait_list = {
      next = 0xffff880749a05c48,
      prev = 0xffff88057f85bdc8
    },
    owner = **0xffff88076b78c000**
  }
```

所以文件是“js”目录下的“fire.js”。很高兴知道这一点。现在，让我们看看哪个进程拥有互斥体:

```
crash> struct thread_info.task **0xffff88076b78c000**
  task = 0xffff8807ef98aae0
crash> [ps](http://people.redhat.com/anderson/crash_whitepaper/help_pages/ps.html) 0xffff8807ef98aae0
   PID  PPID  CPU       TASK        ST  %MEM     VSZ    RSS  COMM
  4723  4087   2  ffff8807ef98aae0  UN   0.3  641736 137204  ruby
```

# 向前一步，向后一步

所以，我们现在知道哪个进程有互斥。它现在在做什么？

```
crash> bt 4723
PID: 4723  TASK: ffff8807ef98aae0  CPU: 2   COMMAND: "ruby"
 #0 [ffff88076b78da08] schedule at ffffffff81528bc0
 #1 [ffff88076b78dad0] rwsem_down_failed_common at ffffffff8152b275
 #2 [ffff88076b78db30] rwsem_down_write_failed at ffffffff8152b3d3
 #3 [ffff88076b78db70] call_rwsem_down_write_failed at ffffffff8128f383
 #4 [ffff88076b78dbd0] xfs_ilock at ffffffffa02006fc [xfs]
 #5 [ffff88076b78dc00] xfs_setattr at ffffffffa021fdcd [xfs]
 #6 [ffff88076b78dcc0] xfs_vn_setattr at ffffffffa022c00b [xfs]
 #7 [ffff88076b78dcd0] notify_change at ffffffff811a7cf8
 #8 [ffff88076b78dd40] do_truncate at ffffffff81186f74
 #9 [ffff88076b78ddb0] do_filp_open at ffffffff8119c051
#10 [ffff88076b78df20] do_sys_open at ffffffff81185c39
#11 [ffff88076b78df70] sys_open at ffffffff81185d50
...
```

它正在等待获取读写信号量的写锁。哪一个？使用上面的相同方法，我们可以恢复框架#4 的第一个参数:

```
xfs_ilock(struct xfs_inode *ip = **0xffff8808a3dbf400**, ??)
```

因此，一个内核可以处理许多不同的文件系统，有一个主虚拟文件系统层(VFS)可以理解的通用结构 inode，但是每个不同的文件系统也有自己特定的 inode 数据结构。XFS 使用一个[结构 xfs_inode](http://lxr.free-electrons.com/source/fs/xfs/xfs_inode.h?v=2.6.32#L238) ，如果它的字段是嵌入式通用 inode，则使用一个。对于低级同步，它包含一个二进制信号量 i_iolock，这个进程在试图获取该信号量时被阻塞。

事实证明，我们以前见过这个 inode:

```
crash> struct -ox xfs_inode.i_vnode **0xffff8808a3dbf400**
struct xfs_inode {
  [**ffff8808a3dbf588**] struct inode i_vnode;
}
```

它是“fire.js”索引节点，其 i_mutex 锁已经被这个进程持有。它现在在同一个 inode 上获取一个特定于 XFS 的信号量。

不幸的是，信号量不像互斥体那样携带“所有者”字段(在这种配置中)。那我们怎么找到它的主人呢？在这种情况下，我们可以幸运地首先假设我们正在调查一个死锁(尽管我们还没有完全找到它)，其次假设这个进程是那个死锁的一个关键部分。如果我们是对的，另一个有趣的进程可能正在等待这个进程的互斥体。

```
crash> struct -ox inode.i_mutex **0xffff8808a3dbf588**
struct inode {
  [ffff8808a3dbf640] struct mutex i_mutex;
}
crash> struct -ox mutex.wait_list ffff8808a3dbf640
struct mutex {
  [ffff8808a3dbf648] struct list_head wait_list;
}
crash> [list](http://people.redhat.com/anderson/crash_whitepaper/help_pages/list.html) -s mutex_waiter.task -H ffff8808a3dbf648
ffff880749a05c48
  task = 0xffff88076dfe2040
ffff88057f85bdc8
  task = 0xffff8802d0e5a080
crash> ps 0xffff88076dfe2040 0xffff8802d0e5a080
   PID  PPID  CPU       TASK        ST  %MEM     VSZ    RSS  COMM
  4724     1   6  ffff88076dfe2040  UN   0.3  468676 138156  ruby
  5109  5099  18  ffff8802d0e5a080  UN   0.0    4144    696  rm
```

我们从进程 5109 开始我们的会话，但是另一个进程 4724 是什么？

```
crash> bt 4724
PID: 4724  TASK: ffff88076dfe2040  CPU: 6   COMMAND: "ruby"
 #0 [ffff880749a05b68] schedule at ffffffff81528bc0
 #1 [ffff880749a05c30] __mutex_lock_slowpath at ffffffff8152a36e
 #2 [ffff880749a05ca0] mutex_lock at ffffffff8152a20b
 #3 [ffff880749a05cc0] generic_file_splice_write at ffffffff811b8c7a
 #4 [ffff880749a05d50] xfs_file_splice_write at ffffffffa02277b0 [xfs]
 #5 [ffff880749a05dc0] do_splice_from at ffffffff811b873e
 #6 [ffff880749a05e00] direct_splice_actor at ffffffff811b8790
 #7 [ffff880749a05e10] splice_direct_to_actor at ffffffff811b8a66
 #8 [ffff880749a05e80] do_splice_direct at ffffffff811b8bad
 #9 [ffff880749a05ed0] do_sendfile at ffffffff8118936c
#10 [ffff880749a05f30] sys_sendfile64 at ffffffff81189404
#11 [ffff880749a05f80] system_call_fastpath at ffffffff8100b072
...
```

嗯，我看到 XFS 特有的代码回调到通用的 VFS 代码。实际上，查看 [xfs_file_splice_write()](http://lxr.free-electrons.com/source/fs/xfs/linux-2.6/xfs_lrw.c?v=2.6.32#L308) ，您可以看到它在哪里抓取 XFS i_iolock，然后调用 generic_file_splice_write()，后者然后抓取泛型 i_mutex。

# 结论

现在有足够的数据来拼凑出一幅完整的画面。大多数 XFS 写操作首先获取文件的 i_mutex，然后获取 i_iolock。sys_open()调用就是这种排序的一个例子。然而，拼接写操作的编码不同。它获取 i_iolock，然后获取 i_mutex。我们在 sys_sendfile64()调用中看到了这一点。因此，每当两个进程并发写入并拼接到同一个 inode 时，每个进程都会获得一个锁，然后阻塞另一个锁，使它们在不可中断的睡眠中死锁，并使 inode 中毒。

我们发现了一个 XFS 病毒。

搜索“xfs splice deadlock ”,会出现一个 2011 年的电子邮件线程,描述了这个问题。然而，平分内核源代码库表明，直到 2014 年 4 月( [8d02076](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=8d0207652cbe27d1f962050737848e5ad4671958) )在 Linux 3.16 中发布，这个错误才真正得到解决。

那么，为什么我们只是花了这么多时间来调试一个长期已知且已修复的问题呢？RedHat 在他们的企业 Linux 产品中没有使用最新版本的内核。当您的目标是长期稳定性时，使用旧的、久经沙场的版本是一件好事。然而，错误仍然会被发现，它们必须被移植到稳定版本中。RedHat 只是还没有包括这个特定的修复。Square 正在与他们合作，以达成解决方案。在此之前，RHEL/CentOS 6 和 CentOS 7 的所有当前版本都容易受到此错误的影响。*2016 年 9 月 2 日更新:RedHat 现在已经发布了更新的内核，其中包含对该死锁的修复:RHEL 6 的版本 2 . 6 . 32–642 和 RHEL 7 的版本 3 . 10 . 0–327 . 22 . 2*。

至于启动这个死锁的进程，它是一个普通的 MRI Ruby 2.2.2 解释器。它的核心 I/O 功能已经过优化，尽可能使用零拷贝操作。自 2014 年初在 Linux 上，从标准库中调用 FileUtils.copy_file()将使用 sendfile64()。因此，有一个相当紧凑的触发器:

```
ruby -rfileutils -e'FN=ARGV.shift; Thread.new { loop { FileUtils.copy_file "/etc/issue", FN } }; loop {File.new FN, File::CREAT|File::TRUNC|File::RDWR }' /xfs/somefile
```

我怀疑 Square 也看到过 Java 进程以这种方式死锁。这些程序正在尝试做正确的事情，我希望随着更多的库和运行时的成熟，它们也会尝试通过采用零拷贝操作来优化它们的 I/O 行为。

*更正:* [*(1)*](https://corner.squareup.com/2015/10/short-guide-to-kernel-debugging.html#correction-1) *:增加了说明你是随 kernel-debuginfo-common 包一起获得内核源代码的。*[*(2)*](https://corner.squareup.com/2015/10/short-guide-to-kernel-debugging.html#correction-2)*:第二个参数以%rsi 传递，而不是%rdx。*