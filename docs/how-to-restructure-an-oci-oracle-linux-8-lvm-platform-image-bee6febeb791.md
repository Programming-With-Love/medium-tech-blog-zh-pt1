# 如何重构 OCI Oracle Linux 8 LVM 平台映像

> 原文：<https://medium.com/oracledevs/how-to-restructure-an-oci-oracle-linux-8-lvm-platform-image-bee6febeb791?source=collection_archive---------0----------------------->

为单独的`/var`、`/tmp`、`/home`等重构一个 OCI Oracle Linux 8 LVM 平台镜像。

⚠️此过程是 ***破坏性的*** —强烈建议在副本、克隆或易于替换的引导卷上运行此过程。

![](img/c5a58c26097531efe93bae5d8c8c1f14.png)

## 背景

OCI 平台映像通过简单的分区方案交付，该方案简单易用；根(/)文件系统包含所有内容。然而，一些客户更喜欢(或要求)一种更具防御性的结构，它隔离文件系统的易变部分，以减少文件系统上的不利事件导致整个实例停机的可能性。例如，填充根(/)文件系统会导致各种各样的意外行为。当像`/tmp`、`/var`和`/home`这样的位置是根文件系统的一部分时，整个系统更容易暴露于流氓进程、过多的日志记录和人为错误。虽然可以通过主动监控和维护来降低一些风险，但是仍然有可能遇到问题。

> 虽然不常见，但完全填满的文件系统可能会导致管理员无法登录，或者实例可能无法成功启动，从而导致恢复过程更加复杂和耗时。

OCI Oracle Linux 8 平台映像的磁盘结构如下:

```
 $ lsblk -f
NAME FSTYPE LABEL UUID MOUNTPOINT
sda 
├─sda1 vfat 20D9-FA96 /boot/efi
├─sda2 xfs 70b15dc2–5ada-4399–9df5-b48c348cd4f1 /boot
└─sda3 LVM2_member VxgtY1–3gar-4ACy-JeJI-YAJf-A7MK-gFdAOT 
 ├─ocivolume-root xfs 25dae1d1-c678–45b2-bd0c-c308e8c950cc /
 └─ocivolume-oled xfs a8c9a3f3–5052–445e-8868–5c1249ae071e /var/oled

$ df -hP
Filesystem Size Used Avail Use% Mounted on
devtmpfs 302M 0 302M 0% /dev
tmpfs 343M 0 343M 0% /dev/shm
tmpfs 343M 14M 329M 4% /run
tmpfs 343M 0 343M 0% /sys/fs/cgroup
/dev/mapper/ocivolume-root 36G 6.6G 29G 19% /
/dev/mapper/ocivolume-oled 10G 119M 9.9G 2% /var/oled
/dev/sda2 1014M 777M 238M 77% /boot
/dev/sda1 100M 5.0M 95M 5% /boot/efi
tmpfs 69M 0 69M 0% /run/user/0
tmpfs 69M 0 69M 0% /run/user/987
tmpfs 69M 0 69M 0% /run/user/1000
```

需要使用`sda1`和`sda2`分区来引导实例，但是操作系统的其余部分安装在`sda3`中，这又是一个 LVM 卷组(`ocivolume`，具有多个逻辑卷(`root`和`oled`)。操作系统存在于`root`中，`oled`用于 Oracle `oswatcher`和崩溃日志；`oled`的分离与我们想要对`/tmp`、`/home`和剩余的`/var`进行的分离相同。为了改变这种结构，我们面临许多挑战:

*   整个卷组被分配(没有空闲空间)
*   XFS 文件系统**不能**收缩(对于 EXT 和其他一些文件系统不是这样)
*   由于以上几点，重构文件系统将是破坏性的，至少是部分破坏性的
*   SELinux(默认强制)会不会对这个结果感到高兴

## 方法

> 附录中有一个示例脚本，可以自动执行步骤 3-12。它非常基础，应该谨慎使用。

对于新的结构，我们希望将`/tmp`、`/var`、`/var/log`和`/home`分成独特的逻辑卷和文件系统。我们不希望重新创建 LVM 物理卷或重新创建现有的逻辑卷——这将消除我们在重构后重新配置`grub`的需要。

*   `/tmp`将是一个独立的 4Gb 文件系统，将在名为`tmp`
    的逻辑卷中创建。`/var`将是一个独立的 1Gb 文件系统，将在名为`var`
    的逻辑卷中创建。`/var/log`将是一个独立的 1Gb 文件系统，将在名为`varlog`
    的逻辑卷中创建。`/home`将是一个独立的 1Gb 文件系统，将在名为`home`的逻辑卷中创建

> 默认引导卷大约为 50Gb，根(/)文件系统的内容可能在 7–10Gb 范围内。如果您添加的额外逻辑卷需要调整大小，或者可能需要增长到一个大小，这将耗尽默认的大约 50Gb 的引导卷，那么在对其进行资源调配时，可能值得考虑增加源实例的引导卷的大小。

为了完成这项工作，我们将使用两个计算实例；第一个是源 OL8 实例，第二个是临时工作实例，我们将使用它来修改源 OL8 实例的引导卷。

> 值得注意的是，OCI 计算实例是克隆，因此，从同一平台映像创建的实例将具有相同的磁盘 UUIDs、磁盘标签和 LVM 标签(如果使用 LVM 方案)。这对用户和软件来说都会变得非常混乱。因此，建议 worker 基于完全不同的平台映像。在这个例子中，我将使用 Ubuntu 20.04 平台映像。

高级步骤包括:

1.  创建源 OL8 实例，停止源实例并分离源实例的引导卷
2.  创建工作实例，确保安装了所有必要的工具，并将源实例启动卷作为块(非启动)卷附加
3.  检查并备份工作实例中的源实例根(/)文件系统
4.  调整源实例`root`逻辑卷的大小，并重新创建`root`逻辑卷文件系统
5.  在源实例卷组(`ocivolume`)中创建额外的逻辑卷(`tmp`、`var`、`varlog` 和`home`，并在其上创建文件系统
6.  将源实例根(/)文件系统恢复到调整后的`root`逻辑卷
7.  为新的`tmp`、`var`、`varlog`、`home`和`agent`逻辑卷创建临时挂载点，并挂载它们
8.  将`/tmp`、`/var`、`/var/log`和`/home`从源根(/)文件系统复制到临时挂载点上的新文件系统
9.  重命名原始的`/tmp`、`/var`和`/home`目录，并替换为新的挂载点
10.  更新源实例`fstab`以在引导时挂载新的文件系统
11.  确保 SELinux 会开心
12.  从 worker 中卸载并分离源实例引导卷，并连接到源实例
13.  试验

> 如果 SELinux 不需要在重构后强制执行，可以在执行这些步骤之前禁用它，但不建议这样做。

## 步伐

**1。创建并准备源 OL8 实例**

使用 OCI 控制台、CLI 或其他机制创建新的 OL8 计算实例。形状和 CPU 架构是不相关的(这些步骤也适用于 ARM 实例)，因为我们只是在处理引导卷。创建实例后，您可能希望登录并更新实例，或者在继续之前进行任何其他配置更改，但这不是必需的。

使用 OCI 控制台或您选择的工具停止源 OL8 实例并分离其引导卷。

**2。创建并准备工人实例**

在这个练习中，使用了一个 Ubuntu 20.04 实例，运行在一个具有 16Gb 内存的 E4-Flex 1 OCPU 图形上。也可以使用不同的形状。记得添加您的 SSH 密钥！

一旦 worker 实例启动，登录并确保安装了 XFS 工具。

```
$ sudo apt install -y xfsprogs xfsdump
Reading package lists… Done
Building dependency tree 
Reading state information… Done
xfsprogs is already the newest version (5.3.0–1ubuntu2).
Suggested packages:
  acl attr quota
The following NEW packages will be installed:
  xfsdump
0 upgraded, 1 newly installed, 0 to remove and 0 not upgraded.
Need to get 183 kB of archives.
After this operation, 692 kB of additional disk space will be used.
Get:1 http://ap-sydney-1-ad-1.clouds.ports.ubuntu.com/ubuntu-ports focal/main arm64 xfsdump arm64 3.1.6+nmu2build1 [183 kB]
Fetched 183 kB in 2s (91.5 kB/s) 
Selecting previously unselected package xfsdump.
(Reading database … 148592 files and directories currently installed.)
Preparing to unpack …/xfsdump_3.1.6+nmu2build1_arm64.deb …
Unpacking xfsdump (3.1.6+nmu2build1) ...
Setting up xfsdump (3.1.6+nmu2build1) ...
Processing triggers for man-db (2.9.1–1) ...
```

使用 OCI 控制台或您选择的工具，将源实例的引导卷作为**半虚拟化的**块卷附加到工作实例。

**3。检查并备份源实例根(/)文件系统**

一旦附加了源实例的引导卷，最好确保工作实例可以正确地看到它:

```
$ sudo vgchange -a y
  2 logical volume(s) in volume group "ocivolume" now active

$ sudo lvdisplay
  --- Logical volume ---
  LV Path                /dev/ocivolume/oled
  LV Name                oled
  VG Name                ocivolume
  LV UUID                QYLO3I-YbXn-d92M-Jhaa-xJoo-hujB-A70hHe
  LV Write Access        read/write
  LV Creation host, time localhost.localdomain, 2022-05-18 23:33:06 +0000
  LV Status              available
  # open                 0
  LV Size                10.00 GiB
  Current LE             2560
  Segments               1
  Allocation             inherit
  Read ahead sectors     auto
  - currently set to     4096
  Block device           253:0

  --- Logical volume ---
  LV Path                /dev/ocivolume/root
  LV Name                root
  VG Name                ocivolume
  LV UUID                asKqqn-xF8Z-T3me-XYSi-ZUSN-17Rm-dUahdB
  LV Write Access        read/write
  LV Creation host, time localhost.localdomain, 2022-05-18 23:33:07 +0000
  LV Status              available
  # open                 0
  LV Size                35.47 GiB
  Current LE             9081
  Segments               1
  Allocation             inherit
  Read ahead sectors     auto
  - currently set to     4096
  Block device           253:1

$ sudo lsblk -f
NAME               FSTYPE      FSVER    LABEL           UUID                                   FSAVAIL FSUSE% MOUNTPOINTS
loop0              squashfs    4.0                                                                   0   100% /snap/core18/2409
loop1              squashfs    4.0                                                                   0   100% /snap/core20/1434
loop2              squashfs    4.0                                                                   0   100% /snap/lxd/22923
loop3              squashfs    4.0                                                                   0   100% /snap/oracle-cloud-agent/36
loop4              squashfs    4.0                                                                   0   100% /snap/snapd/15534
sda                                                                                                           
├─sda1             ext4        1.0      cloudimg-rootfs 96695e07-a270-493b-b66c-ce28d94e1409     42.9G     5% /
├─sda14                                                                                                       
└─sda15            vfat        FAT32    UEFI            9DB3-1174                                99.1M     5% /boot/efi
sdb                                                                                                           
├─sdb1             vfat        FAT16                    2314-8847                                             
├─sdb2             xfs                                  a619a666-d067-48a1-84c0-597037623f97                  
└─sdb3             LVM2_member LVM2 001                 5QtWJg-49vg-4Ces-2Iv3-MRP5-dNdG-nLQwgd                
  ├─ocivolume-oled xfs                                  0950a10f-ab7a-4ade-81df-abfe0b973620                  
  └─ocivolume-root xfs                                  b74c7d6e-a842-4ab7-a47e-2ac332edaa3d 
```

既然源实例数据块卷已连接，那么在开始更改之前验证文件系统是一个好主意:

```
$ sudo xfs_repair /dev/mapper/ocivolume-root
Phase 1 - find and verify superblock...
Phase 2 - using internal log
        - zero log...
        - scan filesystem freespace and inode maps...
        - found root inode chunk
Phase 3 - for each AG...
        - scan and clear agi unlinked lists...
        - process known inodes and perform inode discovery...
        - agno = 0
        - agno = 1
        - agno = 2
        - agno = 3
        - process newly discovered inodes...
Phase 4 - check for duplicate blocks...
        - setting up duplicate extent list...
        - check for inodes claiming duplicate blocks...
        - agno = 0
        - agno = 1
        - agno = 2
        - agno = 3
clearing reflink flag on inodes when possible
Phase 5 - rebuild AG headers and trees...
        - reset superblock...
Phase 6 - check inode connectivity...
        - resetting contents of realtime bitmap and summary inodes
        - traversing filesystem ...
        - traversal finished ...
        - moving disconnected inodes to lost+found ...
Phase 7 - verify and correct link counts...
done
```

现在我们知道我们有一个干净的文件系统，让我们挂载它:

```
$ sudo mount /dev/mapper/ocivolume-root /mnt/
```

> 注意，linux 设备映射器为 LVM 创建条目为 <volume_group>- <logical_volume>，因此`ocivolume`(卷组)和`root`(逻辑卷)变成了`ocivolume-root`。</logical_volume></volume_group>

我们现在需要使用 xfsdump 备份整个根(/)卷:

> 注意，在这个例子中，源实例没有被定制，因此文件系统转储并不大，可以安全地放在 worker 实例的`/tmp`中。如果您的源实例已经过定制或安装了其他软件，请考虑将转储放在`/tmp`之外的其他地方，例如，考虑向工作实例添加另一个块卷，或将一些文件系统服务(FSS)存储附加到工作节点以临时保存备份。

```
$ sudo xfsdump -L "" -M "" -0uf /tmp/ol8_root_backup /mnt
xfsdump: using file dump (drive_simple) strategy
xfsdump: version 3.1.9 (dump format 3.0) - type ^C for status and control
xfsdump: WARNING: no session label specified
xfsdump: WARNING: most recent level 0 dump was interrupted, but not resuming that dump since resume (-R) option not specified
xfsdump: level 0 dump of worker:/mnt
xfsdump: dump date: Mon Jun 13 06:51:40 2022
xfsdump: session id: c078bec3-1852-463b-ab5a-4e94943eb219
xfsdump: session label: ""
xfsdump: ino map phase 1: constructing initial dump list
xfsdump: ino map phase 2: skipping (no pruning necessary)
xfsdump: ino map phase 3: skipping (only one dump stream)
xfsdump: ino map construction complete
xfsdump: estimated dump size: 8501529728 bytes
xfsdump: WARNING: no media label specified
xfsdump: creating dump session media file 0 (media 0, file 0)
xfsdump: dumping ino map
xfsdump: dumping directories
xfsdump: dumping non-directory files
xfsdump: ending media file
xfsdump: media file size 8263474064 bytes
xfsdump: dump size (non-dir files) : 8158542168 bytes
xfsdump: dump complete: 232 seconds elapsed
xfsdump: Dump Summary:
xfsdump:   stream 0 /tmp/ol8_root_backup OK (success)
xfsdump: Dump Status: SUCCESS
```

根据源实例的根(/)文件系统的大小，备份可能需要几分钟时间。

备份成功完成后，从`/mnt`卸载源实例根(/)文件系统:

```
$ sudo umount /mnt
```

**4。调整源实例“根”逻辑卷的大小，并重新创建“根”逻辑卷文件系统**

现在我们需要调整源实例的`root`逻辑卷的大小。使用`lvreduce`执行收缩操作，我们将其减少 15Gb ( `-L -15G`)。选择 15Gb 是因为 4+1+1+1 Gb 用于新文件系统，还有一些空间(8Gb)留给未来的文件系统(如其他代理软件)。在本例中,`root`逻辑卷可能会缩减超过 10Gb，但是请记住，需要保留足够的空间来恢复备份的根(/)文件系统。

⚠️:下面的步骤是破坏性的！

首先，使用测试模式(`-t`)检查调整大小是否成功:

```
$ sudo lvreduce -ft -L -15G /dev/ocivolume/root
  TEST MODE: Metadata will NOT be updated and volumes will not be (de)activated.
  WARNING: Reducing active logical volume to 20.47 GiB.
  THIS MAY DESTROY YOUR DATA (filesystem etc.)
  Size of logical volume ocivolume/root changed from 35.47 GiB (9081 extents) to 20.47 GiB (5241 extents).
  Logical volume ocivolume/root successfully resized.
```

如果调整大小操作测试正常，实际调整大小:

```
$ sudo lvreduce -f -L -15G /dev/ocivolume/root
  WARNING: Reducing active logical volume to 20.47 GiB.
  THIS MAY DESTROY YOUR DATA (filesystem etc.)
  Size of logical volume ocivolume/root changed from 35.47 GiB (9081 extents) to 20.47 GiB (5241 extents).
  Logical volume ocivolume/root successfully resized.
```

然后在源实例的`root`逻辑卷(`ocivolume-root`)上重新创建 XFS 文件系统:

```
$ sudo mkfs.xfs -f /dev/mapper/ocivolume-root
meta-data=/dev/mapper/ocivolume-root isize=512    agcount=4, agsize=1341696 blks
         =                       sectsz=4096  attr=2, projid32bit=1
         =                       crc=1        finobt=1, sparse=1, rmapbt=0
         =                       reflink=1    bigtime=0 inobtcount=0
data     =                       bsize=4096   blocks=5366784, imaxpct=25
         =                       sunit=0      swidth=0 blks
naming   =version 2              bsize=4096   ascii-ci=0, ftype=1
log      =internal log           bsize=4096   blocks=2620, version=2
         =                       sectsz=4096  sunit=1 blks, lazy-count=1
realtime =none                   extsz=4096   blocks=0, rtextents=0
```

**5。创建额外的逻辑卷，并在其上创建文件系统**

创建新的逻辑卷:

```
$ sudo lvcreate ocivolume -L 4G -n tmp
  Logical volume "tmp" created.
$ sudo lvcreate ocivolume -L 1G -n var
  Logical volume "var" created.
$ sudo lvcreate ocivolume -L 1G -n varlog
  Logical volume "varlog" created.
$ sudo lvcreate ocivolume -L 1G -n home
  Logical volume "home" created.
```

使用`mkfs.xfs`创建新的文件系统:

> 请记住，设备映射器将以 <volume_group>- <logical_volume>的形式创建新条目。</logical_volume></volume_group>

*   对于新的`/tmp` — `sudo mkfs.xfs -f /dev/mapper/ocivolume-tmp`
*   为新的`/var` — `sudo mkfs.xfs -f /dev/mapper/ocivolume-var`
*   为了新的`/var/log`——`sudo mkfs.xfs -f /dev/mapper/ocivolume-varlog`
*   为了新的`/home`——`sudo mkfs.xfs -f /dev/mapper/ocivolume-home`

这应该是这样的:

```
$ sudo mkfs.xfs -f /dev/mapper/ocivolume-tmp
meta-data=/dev/mapper/ocivolume-tmp isize=512    agcount=4, agsize=262144 blks
         =                       sectsz=4096  attr=2, projid32bit=1
         =                       crc=1        finobt=1, sparse=1, rmapbt=0
         =                       reflink=1    bigtime=0 inobtcount=0
data     =                       bsize=4096   blocks=1048576, imaxpct=25
         =                       sunit=0      swidth=0 blks
naming   =version 2              bsize=4096   ascii-ci=0, ftype=1
log      =internal log           bsize=4096   blocks=2560, version=2
         =                       sectsz=4096  sunit=1 blks, lazy-count=1
realtime =none                   extsz=4096   blocks=0, rtextents=0

$ sudo mkfs.xfs -f /dev/mapper/ocivolume-var
meta-data=/dev/mapper/ocivolume-var isize=512    agcount=4, agsize=65536 blks
         =                       sectsz=4096  attr=2, projid32bit=1
         =                       crc=1        finobt=1, sparse=1, rmapbt=0
         =                       reflink=1    bigtime=0 inobtcount=0
data     =                       bsize=4096   blocks=262144, imaxpct=25
         =                       sunit=0      swidth=0 blks
naming   =version 2              bsize=4096   ascii-ci=0, ftype=1
log      =internal log           bsize=4096   blocks=2560, version=2
         =                       sectsz=4096  sunit=1 blks, lazy-count=1
realtime =none                   extsz=4096   blocks=0, rtextents=0

$ sudo mkfs.xfs -f /dev/mapper/ocivolume-varlog
meta-data=/dev/mapper/ocivolume-varlog isize=512    agcount=4, agsize=65536 blks
         =                       sectsz=4096  attr=2, projid32bit=1
         =                       crc=1        finobt=1, sparse=1, rmapbt=0
         =                       reflink=1    bigtime=0 inobtcount=0
data     =                       bsize=4096   blocks=262144, imaxpct=25
         =                       sunit=0      swidth=0 blks
naming   =version 2              bsize=4096   ascii-ci=0, ftype=1
log      =internal log           bsize=4096   blocks=2560, version=2
         =                       sectsz=4096  sunit=1 blks, lazy-count=1
realtime =none                   extsz=4096   blocks=0, rtextents=0

$ sudo mkfs.xfs -f /dev/mapper/ocivolume-home
meta-data=/dev/mapper/ocivolume-home isize=512    agcount=4, agsize=65536 blks
         =                       sectsz=4096  attr=2, projid32bit=1
         =                       crc=1        finobt=1, sparse=1, rmapbt=0
         =                       reflink=1    bigtime=0 inobtcount=0
data     =                       bsize=4096   blocks=262144, imaxpct=25
         =                       sunit=0      swidth=0 blks
naming   =version 2              bsize=4096   ascii-ci=0, ftype=1
log      =internal log           bsize=4096   blocks=2560, version=2
         =                       sectsz=4096  sunit=1 blks, lazy-count=1
realtime =none                   extsz=4096   blocks=0, rtextents=0
```

6。将源实例根(/)文件系统恢复到调整大小后的“根”逻辑卷

将源实例`root`逻辑卷(现在已格式化且为空)挂载回`/mnt`:

```
$ sudo mount /dev/mapper/ocivolume-root /mnt
$ df -hP
Filesystem                  Size  Used Avail Use% Mounted on
tmpfs                       1.6G  1.1M  1.6G   1% /run
/dev/sda1                    45G  9.5G   36G  21% /
tmpfs                       7.9G     0  7.9G   0% /dev/shm
tmpfs                       5.0M     0  5.0M   0% /run/lock
/dev/sda15                  105M  5.3M  100M   5% /boot/efi
tmpfs                       1.6G  4.0K  1.6G   1% /run/user/1001
/dev/mapper/ocivolume-root   21G  179M   21G   1% /mnt
```

调整大小后的空源实例的`root`逻辑卷装载到`/mnt`后，我们恢复文件系统备份:

```
$ sudo xfsrestore -f /tmp/ol8_root_backup /mnt
xfsrestore: using file dump (drive_simple) strategy
xfsrestore: version 3.1.9 (dump format 3.0) - type ^C for status and control
xfsrestore: searching media for dump
xfsrestore: examining media file 0
xfsrestore: dump description: 
xfsrestore: hostname: worker
xfsrestore: mount point: /mnt
xfsrestore: volume: /dev/mapper/ocivolume-root
xfsrestore: session time: Mon Jun 13 00:28:36 2022
xfsrestore: level: 0
xfsrestore: session label: ""
xfsrestore: media label: ""
xfsrestore: file system id: b74c7d6e-a842-4ab7-a47e-2ac332edaa3d
xfsrestore: session id: 08bc394b-465a-4f67-b301-8364ad848d9b
xfsrestore: media id: 15cccbfe-fcde-4b71-9122-52f6500610f8
xfsrestore: using online session inventory
xfsrestore: searching media for directory dump
xfsrestore: reading directories
xfsrestore: 18592 directories and 170057 entries processed
xfsrestore: directory post-processing
xfsrestore: restoring non-directory files
xfsrestore: restore complete: 253 seconds elapsed
xfsrestore: Restore Summary:
xfsrestore:   stream 0 /tmp/ol8_root_backup OK (success)
xfsrestore: Restore Status: SUCCESS
```

**7。为新的逻辑卷创建挂载点并挂载它们**

新的(即替换的)`tmp`(`/mnt/tmp-new`)`var`(`/mnt/var-new`)`varlog`(`/mnt/var-new/log`)和`home` ( `/mnt/home-new`)位置需要临时挂载点。

> 请注意，新的目标`/var/log`位置是一个新的文件系统，必须相对于新的`/var`位置进行挂载。因此，在新的`var`逻辑卷已经被挂载到`/mnt/var-new`之后，新的`varlog`逻辑卷将被挂载到`/mnt/var-new/log`用于复制数据**。**

```
$ sudo mkdir /mnt/tmp-new /mnt/home-new /mnt/var-new

$ sudo mount /dev/mapper/ocivolume-tmp /mnt/tmp-new
$ sudo chmod 1777 /mnt/tmp-new                         # Set correct permissions for a "/tmp"
$ sudo mount /dev/mapper/ocivolume-var /mnt/var-new
$ sudo mount /dev/mapper/ocivolume-home /mnt/home-new
$ sudo mkdir /mnt/var-new/log                          # Place the new "/var/log" mount point into the new "/var"
$ sudo mount /dev/mapper/ocivolume-varlog /mnt/var-new/log
```

**8。将**`**/tmp**`**`**/var**`**`**/var/log**`**和** `**/home**` **从源根(/)文件系统复制到新文件系统上的临时挂载点******

****从恢复的根(/)文件系统复制(并保留属性)到新逻辑卷的临时挂载:****

```
**$ sudo cp -rp /mnt/home/. /mnt/home-new/
$ sudo cp -rp /mnt/tmp/. /mnt/tmp-new/
$ sudo cp -rp /mnt/var/. /mnt/var-new/**
```

******9。重命名原始的** `**/tmp**` **、** `**/var**` **和** `**/home**` **目录，并替换为新的挂载点******

****下一部分是将现有目录转移到“备份位置”,并重新定位临时挂载点以替换那些原始目录。****

****首先，我们需要从临时挂载点卸载逻辑卷，以便可以对它们以及原始位置进行重命名:****

```
**$ sudo umount /mnt/home-new /mnt/var-new/log /mnt/var-new /mnt/tmp-new**
```

****然后，重新排列旧位置和新挂载点:****

```
**$ sudo mv /mnt/home /mnt/home-old && sudo mv /mnt/home-new /mnt/home
$ sudo mv /mnt/tmp /mnt/tmp-old && sudo mv /mnt/tmp-new /mnt/tmp
$ sudo mv /mnt/var /mnt/var-old && sudo mv /mnt/var-new /mnt/var**
```

****10。更新源实例 `**fstab**` **以在引导时挂载新的文件系统******

****为了确保新的逻辑卷和文件系统在引导时挂载，请将以下条目添加到源实例的`fstab`中的`/var/oled`条目之前:****

```
**/dev/mapper/ocivolume-tmp /tmp                     xfs     defaults        0 0
/dev/mapper/ocivolume-var /var                     xfs     defaults        0 0
/dev/mapper/ocivolume-varlog /var/log              xfs     defaults        0 0
/dev/mapper/ocivolume-home /home                   xfs     defaults        0 0**
```

****因此，编辑源 OL8 图像的`fstab`(当前位于`/mnt/etc`)文件，使其内容反映如下:****

```
**$ sudo vi /mnt/etc/fstab 

$ cat /mnt/etc/fstab

#
# /etc/fstab
# Created by anaconda on Wed May 18 23:33:09 2022
#
# Accessible filesystems, by reference, are maintained under '/dev/disk/'.
# See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info.
#
# After editing this file, run 'systemctl daemon-reload' to update systemd
# units generated from this file.
#
/dev/mapper/ocivolume-root /                       xfs     defaults        0 0
UUID=a619a666-d067-48a1-84c0-597037623f97 /boot                   xfs     defaults        0 0
UUID=2314-8847          /boot/efi               vfat    defaults,uid=0,gid=0,umask=077,shortname=winnt 0 2
/dev/mapper/ocivolume-tmp /tmp                     xfs     defaults        0 0
/dev/mapper/ocivolume-var /var                     xfs     defaults        0 0
/dev/mapper/ocivolume-varlog /var/log              xfs     defaults        0 0
/dev/mapper/ocivolume-home /home                   xfs     defaults        0 0
/dev/mapper/ocivolume-oled /var/oled               xfs     defaults        0 0
tmpfs                   /dev/shm                tmpfs   defaults,nodev,nosuid,noexec      0 0
######################################
## ORACLE CLOUD INFRASTRUCTURE CUSTOMERS
##
## If you are adding an iSCSI remote block volume to this file you MUST
## include the '_netdev' mount option or your instance will become
## unavailable after the next reboot.
## SCSI device names are not stable across reboots; please use the device UUID instead of /dev path.
##
## Example:
## UUID="94c5aade-8bb1-4d55-ad0c-388bb8aa716a"   /data1    xfs       defaults,noatime,_netdev      0      2
##
## More information:
## https://docs.us-phoenix-1.oraclecloud.com/Content/Block/Tasks/connectingtoavolume.htm
/.swapfile none swap sw,comment=cloudconfig 0 0**
```

****11。保证 SELinux 会开心****

****如果 SELinux 被启用并且`enforcing`它不会高兴文件系统被修改。虽然实例可以启动，但它不太可能正常工作，用户将无法远程登录。为了确保这种情况不会发生，最简单的方法是在下次引导时强制重新标记实例。为此，在源实例的根(/)文件系统中创建一个标志文件(`.autorelabel`)(再次提醒，源实例根(/)当前被挂载到`/mnt`):****

```
**$ sudo touch /mnt/.autorelabel**
```

****这是所有应该要求的。下次引导时，SELinux 应该将所有标签重新应用到文件系统。****

> ****⚠️重新标记整个文件系统结构可能需要相当长的时间，所以在第一次启动时要有耐心。一旦完成，标记文件将自动删除，随后的引导将是正常的。****

****12。从工作实例中卸载并分离源实例引导卷，并连接回源实例****

****剩下的工作就是卸载并分离源实例的`root`逻辑卷，将其从 worker 实例中分离出来，将其连接回源实例，并启动源实例进行测试。****

```
**$ sudo umount /mnt**
```

*******可选*** 检查所有新的文件系统:****

```
**$ sudo xfs_repair /dev/mapper/ocivolume-root
...
$ sudo xfs_repair /dev/mapper/ocivolume-tmp
...
$ sudo xfs_repair /dev/mapper/ocivolume-var
...
$ sudo xfs_repair /dev/mapper/ocivolume-varlog
...
$ sudo xfs_repair /dev/mapper/ocivolume-home
...**
```

****满意地完成所有工作并且卸载了源实例的`root`逻辑卷后，使用 OCI 控制台或您选择的工具将源实例的引导卷从工作实例中分离出来。然后将源实例的引导卷挂回到源 OL8 实例，作为其引导卷。****

******13。测试******

****在使用重新构建的引导卷启动源实例之前，建议为该实例创建一个串行控制台连接，并使用另一个会话连接到该实例，以监控引导过程中的任何问题。****

****启动源实例后，登录并验证新的文件系统结构。****

****`/tmp`、`/var`和`/home`的原结构仍为`/tmp-old`、`/var-old`和`/home-old`。如果一切正常，移除这些先前的结构:****

```
**$ sudo rm -rf /tmp-old /var-old /home-old**
```

****如果一切正常，可以考虑创建一个定制的映像，以便于重用新的文件系统结构。参见[管理自定义图像](https://docs.oracle.com/en-us/iaas/Content/Compute/Tasks/managingcustomimages.htm)。****

****大概就是这样！****

****如果你对 Oracle 开发人员在他们的自然环境中发生的事情感到好奇，来[加入我们的公共休闲频道](https://bit.ly/odevrel_slack)！****

## ****附录—脚本****

****⚠️ ***慎用！*******

****下面的脚本非常简单，一旦源实例的启动卷被连接，如果在 worker 实例上运行，它将执行步骤 3 到 12(除了 detatch)。检查非常有限，所有值都是硬编码的。如果 OL8 的平台映像发生变化，或者针对不同的平台映像运行，脚本很可能会失败，导致重构不完整或目标引导卷损坏。强烈建议创建备份或使用目标启动卷的副本。****

****⚠️ ***慎用！*******

******refactor.sh** —用法`sudo refactor.sh`交叉手指…****

```
**#!/bin/bash

if [ "${EUID}" -ne 0 ]; then
  printf "Please run as root.\n"
  exit 1
fi

printf "Refreshing volume groups...\n%s\n\n" "$(vgchange -a y)"
printf "These are the logical volumes...\n%s\n\n" "$(lvdisplay)"
printf "The block devices...\n%s\n\n" "$(lsblk -f)"

printf "Sanity checking the root file system...\n"
printf "%s\n\n" "$(xfs_repair /dev/mapper/ocivolume-root 2>&1)"

printf "Mounting root file system... "
mount /dev/mapper/ocivolume-root /mnt
if [ ${?} -ne 0 ]; then
  printf "mounting failed!\nAbort...\n"
  exit 1
else
  printf "done.\n\n"
fi

printf "Backing up root file system...\n"
OUTPUT=$(xfsdump -L \"\" -M \"\" -0uf /tmp/ol8_root_backup /mnt 2>&1)
if [ ${?} -ne 0 ]; then
  printf "%s\n\nBackup failed!\nAbort...\n" "${OUTPUT}"
  umount /mnt
  exit 1
else
  printf "%s\n\n" "${OUTPUT}"
fi

printf "Unmounting root file system... "
umount /mnt
if [ ${?} -ne 0 ]; then
  printf "unmounting failed!\nAbort...\n"
  exit 1
else
  printf "done.\n\n"
fi

printf "Testing resize of root file system...\n"
OUTPUT=$(lvreduce -ft -L -15G /dev/ocivolume/root 2>&1)
if [ ${?} -ne 0 ]; then
  printf "%s\n\nTest failed!\nAbort...\n" "${OUTPUT}"
  exit 1
else
  printf "%s\n\nTest ok, procceding.\n\n" "${OUTPUT}"
fi

printf "Resize of root file system...\n"
OUTPUT=$(lvreduce -f -L -15G /dev/ocivolume/root)
if [ ${?} -ne 0 ]; then
  printf "%s\n\nResize failed!\n Abort...\n" "${OUTPUT}"
  exit 1
else
  printf "%s\n\nResized!\n\n" "${OUTPUT}"
fi

printf "Recreate root file system...\n%s\n\n" "$(mkfs.xfs -f /dev/mapper/ocivolume-root 2>&1)"

printf "Creating new logical volumes...\n"
lvcreate ocivolume -L 4G -n tmp
lvcreate ocivolume -L 1G -n var
lvcreate ocivolume -L 1G -n varlog
lvcreate ocivolume -L 1G -n home

printf "Creating new file systems...\n"
mkfs.xfs -f /dev/mapper/ocivolume-tmp
mkfs.xfs -f /dev/mapper/ocivolume-var
mkfs.xfs -f /dev/mapper/ocivolume-varlog
mkfs.xfs -f /dev/mapper/ocivolume-home

printf "Remounting root file systems... "
mount /dev/mapper/ocivolume-root /mnt
if [ ${?} -ne 0 ]; then
  printf "mounting failed!\nAbort...\n"
  exit 1
else
  printf "done.\n\n"
fi

printf "Restoring root file system...\n"
OUTPUT=$(xfsrestore -f /tmp/ol8_root_backup /mnt 2>&1)
if [ ${?} -ne 0 ]; then
  printf "%s\n\nRestore failed!\nAbort...\n" "${OUTPUT}"
  umount /mnt
  exit 1
else
  printf "%s\n\n" "${OUTPUT}"
fi

printf "Creating temporary mount points...\n"
mkdir /mnt/tmp-new /mnt/home-new /mnt/var-new /mnt/agent

printf "Mount volumes... "
mount /dev/mapper/ocivolume-tmp /mnt/tmp-new && chmod 1777 /mnt/tmp-new && mount /dev/mapper/ocivolume-var /mnt/var-new && mount /dev/mapper/ocivolume-home /mnt/home-new && mount /dev/mapper/ocivolume-agent /mnt/agent && mkdir /mnt/var-new/log && mount /dev/mapper/ocivolume-varlog /mnt/var-new/log
if [ ${?} -ne 0 ]; then
  printf "mounting and mapping failed!\nAbort...\n"
  exit 1
else
  printf "done.\n\n"
fi

printf "Copying files to volumes...\n"
cp -rp /mnt/home/. /mnt/home-new/
cp -rp /mnt/tmp/. /mnt/tmp-new/
cp -rp /mnt/var/. /mnt/var-new/

printf "Unmounting volumes... "
umount /mnt/home-new /mnt/var-new/log /mnt/var-new /mnt/tmp-new /mnt/agent
if [ ${?} -ne 0 ]; then
  printf "unmounting failed!\nAbort...\n"
  exit 1
else
  printf "done.\n\n"
fi

printf "Shuffling locations and mount points... "
mv /mnt/home /mnt/home-old && sudo mv /mnt/home-new /mnt/home && mv /mnt/tmp /mnt/tmp-old && sudo mv /mnt/tmp-new /mnt/tmp && mv /mnt/var /mnt/var-old && sudo mv /mnt/var-new /mnt/var
if [ ${?} -ne 0 ]; then
  printf "failed!\nAbort...\n"
  exit 1
else
  printf "done.\n\n"
fi

printf "Touching marker file for SELinux relabelling...\n"
touch /mnt/.autorelabel

printf "Changing fstab...\n"
sed -i-$(date +%Y%m%d) -e'/.*\/var\/oled.*/i /dev/mapper/ocivolume-tmp /tmp                     xfs     defaults        0 0\n/dev/mapper/ocivolume-var /var                     xfs     defaults        0 0\n/dev/mapper/ocivolume-varlog /var/log              xfs     defaults        0 0\n/dev/mapper/ocivolume-home /home                   xfs     defaults        0 0\n/dev/mapper/ocivolume-agent /agent                 xfs     defaults        0 0' /mnt/etc/fstab

cat /mnt/etc/fstab

printf "Unounting root file system... "
umount /mnt
if [ ${?} -ne 0 ]; then
  printf "unmounting failed!\nAbort...\n"
  exit 1
else
  printf "done.\n\n"
fi

printf "\nAll done.\n\nPlease detach the volume and test.\n"**
```