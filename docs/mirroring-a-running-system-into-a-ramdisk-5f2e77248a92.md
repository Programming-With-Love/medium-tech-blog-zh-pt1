# 将正在运行的系统镜像到内存磁盘

> 原文：<https://medium.com/oracledevs/mirroring-a-running-system-into-a-ramdisk-5f2e77248a92?source=collection_archive---------2----------------------->

## Oracle Linux 内核开发人员 William Roche 提出了一种将正在运行的系统镜像到内存磁盘的方法。

有些情况下，系统可以正确引导，但一段时间后，可能会失去对系统盘的访问，例如，iSCSI 系统盘配置出现网络问题，或任何其他磁盘驱动程序问题。一旦系统磁盘不再可访问，我们很快就会面临 I/O 故障导致的挂起情况，而不可能在这台机器上进行本地调查。I/O 错误…