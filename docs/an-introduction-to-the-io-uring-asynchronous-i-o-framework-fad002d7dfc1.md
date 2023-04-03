# 异步输入输出框架简介

> 原文：<https://medium.com/oracledevs/an-introduction-to-the-io-uring-asynchronous-i-o-framework-fad002d7dfc1?source=collection_archive---------1----------------------->

![](img/424d6212c1650faff0941065a21453bb.png)

在这篇博客中，Oracle Linux 内核开发人员***Bijan Mottahedeh****谈到了 Unbreakable Enterprise Kernel 6 中包含的异步 I/O 框架。*

# 介绍

这篇博客文章简要介绍了 Unbreakable Enterprise Kernel(UEK)第 6 版中的异步 I/O 框架。它强调了引入新框架的动机，借助示例应用程序描述了它的系统调用和库接口，并提供了一个参考列表，进一步详细描述了该技术，包括更多的使用示例。

io_uring 异步 I/O (AIO)框架是一个新的 Linux I/O 接口，首次在上游 Linux 内核版本 5.1(2019 年 3 月)中引入。它为需要 AIO 功能但更喜欢内核执行 I/O 的应用程序提供了低延迟和功能丰富的接口。这可能是为了利用在文件系统上运行的优势，或者利用镜像和块级加密等功能。这与 SPDK 应用形成对比，例如，它们明确地不希望内核执行 I/O，因为它们实现了自己的文件系统和特性。

# 动机

本机 Linux AIO 框架受到各种限制的困扰，io uring 旨在克服这些限制:

*   它不支持缓冲 I/O，只支持直接 I/O。
*   它具有非确定性行为，在各种情况下可能会阻塞。
*   它有一个次优的 API，每个 I/O 至少需要两次系统调用，一次提交请求，一次等待请求完成。

# 通信电路

一个运行中的实例有两个环，一个提交队列(SQ)和一个完成队列(CQ ),在内核和应用程序之间共享。队列的大小是单个生产者、单个消费者和 2 的幂。

队列提供无锁访问接口，与内存屏障相协调。

应用程序创建一个或多个 SQ 条目(SQE)，然后更新 SQ 尾部。内核消耗 SQE，并更新 SQ 头。

内核为一个或多个已完成的请求创建 CQ 条目(CQE)，并更新 CQ 尾部。应用程序使用 cqe 并更新 CQ 报头。

完成事件可能以任何顺序到达，但是它们总是与特定的 SQE 相关联。

# 系统调用 API

io_uring API 由三个系统调用组成:io_uring_setup(2)、io_uring_register(2)和 io_uring_enter(2)，下面几节将对此进行描述。系统调用的完整手册页可在[这里](https://github.com/axboe/liburing/tree/master/man)获得。

# 安装过程中

*设置执行异步 I/O 的上下文*

```
int io_uring_setup(u32 entries, struct io_uring_params *p);
```

io_uring_setup()系统调用使用至少*个条目*个元素设置提交队列和完成队列，并返回一个文件描述符，该文件描述符可用于在 io_uring 实例上执行后续操作。提交和完成队列在应用程序和内核之间共享，这消除了在启动和完成 I/O 时复制数据的需要。

*params* 由应用程序用来配置 io_uring 实例，并由内核传送回有关已配置的提交和完成环形缓冲区的信息。

可以在三种主要操作模式下配置运行实例:

*   **中断驱动** —默认情况下，io_uring 实例是为中断驱动 I/O 设置的。I/O 可以使用 io_uring_enter()提交，并且可以通过直接检查完成队列来获得。
*   **轮询** —执行忙碌等待 I/O 完成，而不是通过异步 IRQ(中断请求)获得通知。文件系统(如果有)和块设备必须支持轮询，这样才能工作。忙等待提供了更低的延迟，但可能比中断驱动的 I/O 消耗更多的 CPU 资源。目前，此功能仅在使用 *O_DIRECT* 标志打开的文件描述符上可用。当一个读或写被提交给轮询上下文时，应用程序必须通过调用 io_uring_enter()来轮询 CQ 环上的完成。在一个运行中的实例上混合和匹配轮询和非轮询 I/O 是非法的。
*   **内核轮询** —在这种模式下，创建一个内核线程来执行提交队列轮询。以这种方式配置的 I/O 过程实例使应用程序能够发出 I/O，而无需将上下文切换到内核中。通过使用提交队列来填充新的提交队列条目，并观察完成队列上的完成情况，应用程序可以提交并获取 I/o，而无需进行任何系统调用。如果内核线程空闲的时间超过了用户可配置的时间量，它将在首先通知应用程序后进入空闲状态。当这种情况发生时，应用程序必须调用 io_uring_enter()来唤醒内核线程。如果 I/O 保持忙碌，内核线程将永远不会休眠。

io_uring_setup()成功时返回一个新的文件描述符。然后，应用程序可以在后续的 mmap(2)调用中提供文件描述符，以映射提交和完成队列，或者映射到 io_uring_register()或 io_uring_enter()系统调用。

# io_uring_register

*异步 I/O 的寄存器文件或用户缓冲器*

```
int io_uring_register(unsigned int fd, unsigned int opcode, void *arg, unsigned int nr_args);
```

io_uring_register()系统调用注册由 *fd* 引用的 io_uring 实例中使用的用户缓冲区或文件。注册文件或用户缓冲区允许内核长期引用与文件相关联的内部内核数据结构，或者创建与缓冲区相关联的应用存储器的长期映射，只在注册期间进行一次，而不是在处理每个 I/O 请求期间，因此减少了每个 I/O 的开销。

注册的缓冲区将被锁定在内存中，并根据用户的 *RLIMIT_MEMLOCK* 资源限制收费。此外，每个缓冲区的大小限制为 1gb。目前，缓冲区必须是匿名的、非文件支持的内存，例如由 malloc(3)或 mmap(2)返回的设置了 *MAP_ANONYMOUS* 标志的内存。也支持大页面。请注意，整个巨大的页面将被固定在内核中，即使只使用了其中的一部分。

设置一个大的缓冲区，然后只使用它的一部分用于 I/O，只要范围在最初映射的区域内，这是完全有效的。

应用程序可以增加或减少已注册缓冲区的大小或数量，方法是首先取消注册现有缓冲区，然后使用新的缓冲区对 io_uring_register()发出新的调用。

应用程序可以动态更新已注册的文件集，而无需先注销它们。

可以使用 eventfd(2)来获得关于正在运行的实例的完成事件的通知。如果需要，可以通过这个系统调用注册一个 eventfd 文件描述符。

正在运行的应用程序的凭证可以向 io_uring 注册，io _ uring 返回与这些凭证相关联的 id。希望在不同的用户/进程之间共享环的应用程序可以在 SQE 个性字段中传递这个凭证 id。如果设置，将向特定的 SQE 颁发这些凭据。

# 输入命令(_ u)

*启动和/或完成异步输入/输出*

```
int io_uring_enter(unsigned int fd, unsigned int to_submit, unsigned int min_complete, unsigned int flags, sigset_t *sig);
```

io_uring_enter()用于通过调用 io_uring_setup()使用共享提交和完成队列来启动和完成 I/O。单个调用既可以提交新的 I/O，也可以等待由此调用或之前对 io_uring_enter()的调用启动的 I/O 完成。

*fd* 是 io_uring_setup()返回的文件描述符。 *to_submit* 指定从提交队列中提交的 I/o 数量。如果应用程序如此指示，系统调用将在返回之前尝试等待 *min_complete* 事件完成。如果 io_uring 实例是为轮询配置的，那么 *min_complete* 的含义略有不同。传递值 0 指示内核返回任何已经完成的事件，而不阻塞。如果 *min_complete* 为非零值，如果有任何完成事件可用，内核仍将立即返回。如果没有可用的事件完成，则调用将进行轮询，直到一个或多个完成可用，或者直到进程超过其调度器时间片。

注意，对于中断驱动的 I/O，应用程序可以检查事件完成的完成队列，而根本不进入内核。

io_uring_enter()支持多种操作，包括

*   打开、关闭和统计文件
*   读取和写入多个缓冲区或预映射缓冲区
*   套接字输入/输出操作
*   同步文件状态
*   异步监控一组文件描述符
*   创建与环中特定操作相关联的超时
*   试图取消当前正在进行的操作
*   创建 I/O 链
*   链内的有序执行
*   多个链的并行执行

当系统调用返回已经消耗并提交了一定数量的 SQE 时，在环中重用 SQE 条目是安全的。即使实际的 IO 提交必须被踢到异步上下文，也是如此，这意味着 SQE 可能实际上还没有被提交。如果内核需要以后使用某个特定的 SQE 条目，它会制作一个私有副本。

# Liburing

Liburing 为基本用例提供了一个简单的高级 API，并允许应用程序避免处理完整的系统调用实现细节。API 还避免了重复的操作代码，例如设置 io _ uring 实例。

例如，从 io_uring_setup()获取一个环形文件描述符后，应用程序必须始终调用 mmap()，以便映射访问的提交和完成队列，如 io_uring_setup()手册页中所述。整个序列有点长，但是可以通过下面的 liburing 调用来完成:

```
int io_uring_queue_init(unsigned entries, struct io_uring *ring, unsigned flags);
```

下面的示例应用程序包含在 liburing source 中，帮助说明了这些要点。

liburing 示例应用程序的源代码可从[这里](https://github.com/axboe/liburing/tree/master/examples)获得。

目前没有可用的 liburing API 文档，API 在 [liburing.h](https://github.com/axboe/liburing/blob/master/src/include/liburing.h) 头文件中描述。

# 测试期间的示例应用程序

io_uring-test 使用 4 个 SQEs 从用户指定的文件中读取最多 16KB。每个 SQE 都是一个从固定文件偏移量读取 4KB 数据的请求。然后，io-uring 会获取每个 CQE，并检查是否按照请求从文件中读取了完整的 4KB。

如果文件小于 16KB，仍会提交所有 4 个 SQE，但一些 CQE 结果会指示部分读取或零字节读取，具体取决于文件的实际大小。

io-ouring 最后报告它已经处理的 SQE 和 cqe 的数量。

下面列出了完整的源代码，后面是对 liburing 调用的描述。

```
/* SPDX-License-Identifier: MIT */
/*
 * Simple app that demonstrates how to setup an io_uring interface,
 * submit and complete IO against it, and then tear it down.
 *
 * gcc -Wall -O2 -D_GNU_SOURCE -o io_uring-test io_uring-test.c -luring
 */
#include <stdio.h>
#include <fcntl.h>
#include <string.h>
#include <stdlib.h>
#include <unistd.h>
#include "liburing.h"

#define QD  4

int main(int argc, char *argv[])
{
    struct io_uring ring;
    int i, fd, ret, pending, done;
    struct io_uring_sqe *sqe;
    struct io_uring_cqe *cqe;
    struct iovec *iovecs;
    off_t offset;
    void *buf;

    if (argc < 2) {
        printf("%s: file\n", argv[0]);
        return 1;
    }

    ret = io_uring_queue_init(QD, &ring, 0);
    if (ret < 0) {
        fprintf(stderr, "queue_init: %s\n", strerror(-ret));
        return 1;
    }

    fd = open(argv[1], O_RDONLY | O_DIRECT);
    if (fd < 0) {
        perror("open");
        return 1;
    }

    iovecs = calloc(QD, sizeof(struct iovec));
    for (i = 0; i < QD; i++) {
        if (posix_memalign(&buf, 4096, 4096))
            return 1;
        iovecs[i].iov_base = buf;
        iovecs[i].iov_len = 4096;
    }

    offset = 0;
    i = 0;
    do {
        sqe = io_uring_get_sqe(&ring);
        if (!sqe)
            break;
        io_uring_prep_readv(sqe, fd, &iovecs[i], 1, offset);
        offset += iovecs[i].iov_len;
        i++;
    } while (1);

    ret = io_uring_submit(&ring);
    if (ret < 0) {
        fprintf(stderr, "io_uring_submit: %s\n", strerror(-ret));
        return 1;
    }

    done = 0;
    pending = ret;
    for (i = 0; i < pending; i++) {
        ret = io_uring_wait_cqe(&ring, &cqe);
        if (ret < 0) {
            fprintf(stderr, "io_uring_wait_cqe: %s\n", strerror(-ret));
            return 1;
        }

        done++;
        ret = 0;
        if (cqe->res != 4096) {
            fprintf(stderr, "ret=%d, wanted 4096\n", cqe->res);
            ret = 1;
        }
        io_uring_cqe_seen(&ring, cqe);
        if (ret)
            break;
    }

    printf("Submitted=%d, completed=%d\n", pending, done);
    close(fd);
    io_uring_queue_exit(&ring);
    return 0;
}
```

# 描述

在默认的中断驱动模式下创建一个 io_uring 实例，仅指定环的大小。

```
ret = io_uring_queue_init(QD, &ring, 0);
```

接下来取出所有的环 SQE，并为 IORING_OP_READV 操作做准备，该操作为 readv(2)系统调用提供了异步接口。Liburing 提供了许多帮助函数来准备操作。

每个 SQE 将指向由 iovec 结构描述的分配的缓冲区。完成后，缓冲区将包含相应 readv 操作的结果。

```
sqe = io_uring_get_sqe(&ring); io_uring_prep_readv(sqe, fd, &iovecs[i], 1, offset);
```

通过对 io_uring_submit()的一次调用来提交 SQE，该调用返回提交的 SQE 的数量。

```
ret = io_uring_submit(&ring);
```

通过重复调用 io_uring_wait_cqe()来获取 cqe，并且通过 cqe->res 字段来验证给定提交是否成功；对 io_uring_cqe_seen()的每个匹配调用都通知内核给定的 cqe 已经被消耗。

```
ret = io_uring_wait_cqe(&ring, &cqe); io_uring_cqe_seen(&ring, cqe);
```

io uring 实例最终被拆除。

```
void io_uring_queue_exit(struct io_uring *ring)
```

# 示例应用程序链接-cp

link-cp 使用 SQE 链接功能复制文件。

如前所述，io ouring 支持创建 I/O 链。链中的 I/O 操作是顺序执行的，并且多个 I/O 链可以并行执行。

为了复制文件，link-cp 创建长度为 2 的 SQE 链。链中的第一个 SQE 是从输入文件到缓冲区的读请求。链接到第一个请求的第二个请求是从同一个缓冲区到输出文件的写请求。

```
/* SPDX-License-Identifier: MIT */
/*
 * Very basic proof-of-concept for doing a copy with linked SQEs. Needs a
 * bit of error handling and short read love.
 */
#include <stdio.h>
#include <fcntl.h>
#include <string.h>
#include <stdlib.h>
#include <unistd.h>
#include <assert.h>
#include <errno.h>
#include <inttypes.h>
#include <sys/types.h>
#include <sys/stat.h>
#include <sys/ioctl.h>
#include "liburing.h"

#define QD  64
#define BS  (32*1024)

struct io_data {
    size_t offset;
    int index;
    struct iovec iov;
};

static int infd, outfd;
static unsigned inflight;

static int setup_context(unsigned entries, struct io_uring *ring)
{
    int ret;

    ret = io_uring_queue_init(entries, ring, 0);
    if (ret < 0) {
        fprintf(stderr, "queue_init: %s\n", strerror(-ret));
        return -1;
    }

    return 0;
}

static int get_file_size(int fd, off_t *size)
{
    struct stat st;

    if (fstat(fd, &st) < 0)
        return -1;
    if (S_ISREG(st.st_mode)) {
        *size = st.st_size;
        return 0;
    } else if (S_ISBLK(st.st_mode)) {
        unsigned long long bytes;

        if (ioctl(fd, BLKGETSIZE64, &bytes) != 0)
            return -1;

        *size = bytes;
        return 0;
    }

    return -1;
}

static void queue_rw_pair(struct io_uring *ring, off_t size, off_t offset)
{
    struct io_uring_sqe *sqe;
    struct io_data *data;
    void *ptr;

    ptr = malloc(size + sizeof(*data));
    data = ptr + size;
    data->index = 0;
    data->offset = offset;
    data->iov.iov_base = ptr;
    data->iov.iov_len = size;

    sqe = io_uring_get_sqe(ring);
    io_uring_prep_readv(sqe, infd, &data->iov, 1, offset);
    sqe->flags |= IOSQE_IO_LINK;
    io_uring_sqe_set_data(sqe, data);

    sqe = io_uring_get_sqe(ring);
    io_uring_prep_writev(sqe, outfd, &data->iov, 1, offset);
    io_uring_sqe_set_data(sqe, data);
}

static int handle_cqe(struct io_uring *ring, struct io_uring_cqe *cqe)
{
    struct io_data *data = io_uring_cqe_get_data(cqe);
    int ret = 0;

    data->index++;

    if (cqe->res < 0) {
        if (cqe->res == -ECANCELED) {
            queue_rw_pair(ring, BS, data->offset);
            inflight += 2;
        } else {
            printf("cqe error: %s\n", strerror(cqe->res));
            ret = 1;
        }
    }

    if (data->index == 2) {
        void *ptr = (void *) data - data->iov.iov_len;

        free(ptr);
    }
    io_uring_cqe_seen(ring, cqe);
    return ret;
}

static int copy_file(struct io_uring *ring, off_t insize)
{
    struct io_uring_cqe *cqe;
    size_t this_size;
    off_t offset;

    offset = 0;
    while (insize) {
        int has_inflight = inflight;
        int depth;

        while (insize && inflight < QD) {
            this_size = BS;
            if (this_size > insize)
                this_size = insize;
            queue_rw_pair(ring, this_size, offset);
            offset += this_size;
            insize -= this_size;
            inflight += 2;
        }

        if (has_inflight != inflight)
            io_uring_submit(ring);

        if (insize)
            depth = QD;
        else
            depth = 1;
        while (inflight >= depth) {
            int ret;

            ret = io_uring_wait_cqe(ring, &cqe);
            if (ret < 0) {
                printf("wait cqe: %s\n", strerror(ret));
                return 1;
            }
            if (handle_cqe(ring, cqe))
                return 1;
            inflight--;
        }
    }

    return 0;
}

int main(int argc, char *argv[])
{
    struct io_uring ring;
    off_t insize;
    int ret;

    if (argc < 3) {
        printf("%s: infile outfile\n", argv[0]);
        return 1;
    }

    infd = open(argv[1], O_RDONLY);
    if (infd < 0) {
        perror("open infile");
        return 1;
    }
    outfd = open(argv[2], O_WRONLY | O_CREAT | O_TRUNC, 0644);
    if (outfd < 0) {
        perror("open outfile");
        return 1;
    }

    if (setup_context(QD, &ring))
        return 1;
    if (get_file_size(infd, &insize))
        return 1;

    ret = copy_file(&ring, insize);

    close(infd);
    close(outfd);
    io_uring_queue_exit(&ring);
    return ret;
}
```

# 描述

三个例程 copy_file()、queue_rw_pair()和 handle_cqe()实现了文件复制。

copy_file()实现了高级复制循环；它调用 queue_rw_pair()来构造每个 SQE 对

```
queue_rw_pair(ring, this_size, offset);
```

并在每次迭代中提交所有构建的 SQE 对，只需调用 io_uring_submit()。

```
if (has_inflight != inflight) io_uring_submit(ring);
```

只要仍有数据需要拷贝，copy_file()就会在运行中维护多达 QD 的 SQEs 在输入文件被完全读取后，它等待并获取所有 cqe。

```
ret = io_uring_wait_cqe(ring, &cqe); if (handle_cqe(ring, cqe))
```

queue_rw_pair()构造一个读写 SQE 对。IOSQE_IO_LINK 标志为读 SQE 置位，标志着链的开始。没有为标记链结束的写 SQE 设置标志。将两个 SQE 的用户数据字段设置为该对的相同数据描述符，并将在完成处理期间使用。

```
sqe = io_uring_get_sqe(ring); io_uring_prep_readv(sqe, infd, &data->iov, 1, offset); sqe->flags |= IOSQE_IO_LINK; io_uring_sqe_set_data(sqe, data); sqe = io_uring_get_sqe(ring); io_uring_prep_writev(sqe, outfd, &data->iov, 1, offset); io_uring_sqe_set_data(sqe, data);
```

handle_cqe()从 cqe 中检索 queue_rw_pair()保存的数据描述符，并将检索结果记录在描述符中。

```
struct io_data *data = io_uring_cqe_get_data(cqe); data->index++;
```

如果请求被取消，handle_cqe()重新提交读写对。

```
if (cqe->res == -ECANCELED) { queue_rw_pair(ring, BS, data->offset);
```

io_uring_enter()手册页中的以下摘录更详细地描述了链式请求的行为:

# **IOSQE_IO_LINK**

当这个标志被指定时，它与提交环中的下一个 SQE 形成一个链接。在这一个完成之前，下一个 SQE 不会开始。这实际上形成了一个 SQE 链，可以任意长。链的尾部由没有设置该标志的第一个 SQE 表示。此标志对以前的 SQE 提交没有影响，也不会影响链尾之外的 SQE。这意味着多个链可以并行执行，或者链和单个 SQE 可以并行执行。只有链内的成员被序列化。如果一个 SQE 链中的任何请求以错误结束，那么这个 SQE 链就会中断。io uring 认为任何意外结果都是错误。这意味着，例如，一个短读也将终止链的剩余部分。如果 SQE 链接链断开，链的剩余未启动部分将被终止，并以-ecanced 作为错误代码结束。

在处理完 cqe 对的两个成员后，handle_cqe()释放共享数据描述符。

```
if (data->index == 2) { void *ptr = (void *) data - data->iov.iov_len; free(ptr); }
```

handle_cqe()最后通知内核给定的 cqe 已经被使用了。

```
io_uring_cqe_seen(ring, cqe);
```

# Liburing API

io-uring-test 和 link-cp 使用 liburing API 的以下子集:

```
/*
 * Returns -1 on error, or zero on success. On success, 'ring'
 * contains the necessary information to read/write to the rings.
 */
int io_uring_queue_init(unsigned entries, struct io_uring *ring, unsigned flags);

/*
 * Return an sqe to fill. Application must later call io_uring_submit()
 * when it's ready to tell the kernel about it. The caller may call this
 * function multiple times before calling io_uring_submit().
 *
 * Returns a vacant sqe, or NULL if we're full.
 */
struct io_uring_sqe *io_uring_get_sqe(struct io_uring *ring);

/*
 * Set the SQE user_data field.
 */
void io_uring_sqe_set_data(struct io_uring_sqe *sqe, void *data);

/*
 * Prepare a readv I/O operation.
 */
void io_uring_prep_readv(struct io_uring_sqe *sqe, int fd,
                         const struct iovec *iovecs,
                         unsigned nr_vecs, off_t offset);

/*
 * Prepare a writev I/O operation.
 */
void io_uring_prep_writev(struct io_uring_sqe *sqe, int fd,
                          const struct iovec *iovecs,
                          unsigned nr_vecs, off_t offset);

/*
 * Submit sqes acquired from io_uring_get_sqe() to the kernel.
 *
 * Returns number of sqes submitted
 */
int io_uring_submit(struct io_uring *ring);

/*
 * Return an IO completion, waiting for it if necessary. Returns 0 with
 * cqe_ptr filled in on success, -errno on failure.
 */
int io_uring_wait_cqe(struct io_uring *ring,
                      struct io_uring_cqe **cqe_ptr);

/*
 * Must be called after io_uring_{peek,wait}_cqe() after the cqe has
 * been processed by the application.
 */
static inline void io_uring_cqe_seen(struct io_uring *ring,
                                     struct io_uring_cqe *cqe);

void io_uring_queue_exit(struct io_uring *ring);
```

# 参考

*   [带 IO 期间的高效 IO](https://kernel.dk/io_uring.pdf)
*   [一个新的异步输入输出 API](https://lwn.net/Articles/776703/)
*   [期间的快速增长](https://lwn.net/Articles/810414/)
*   [通过 io 期间更快的 I/O 速度](https://www.youtube.com/watch?v=-5T4Cjw46ys)
*   [系统调用 API](https://github.com/axboe/liburing/tree/master/man)
*   [Liburing API](https://github.com/axboe/liburing/blob/master/src/include/liburing.h)
*   [示例](https://github.com/axboe/liburing/tree/master/examples)
*   [创建库](https://github.com/axboe/liburing)

*原载于 https://blogs.oracle.com*[](https://blogs.oracle.com/linux/an-introduction-to-the-io_uring-asynchronous-io-framework)**。**