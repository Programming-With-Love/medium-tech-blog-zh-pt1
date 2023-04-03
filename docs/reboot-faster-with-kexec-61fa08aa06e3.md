# 使用 kexec 加快重启速度

> 原文：<https://medium.com/oracledevs/reboot-faster-with-kexec-61fa08aa06e3?source=collection_archive---------4----------------------->

## Oracle Linux 内核开发人员 Steve Sistare 撰写了这篇关于加速开发和生产系统内核重启的文章。

![](img/442792abfeea72b1a02ab5f860557baa.png)

kexec 命令加载一个新内核并直接跳转到它，绕过固件和 grub。它通常用作生成故障转储的第一步，但也可以用于…