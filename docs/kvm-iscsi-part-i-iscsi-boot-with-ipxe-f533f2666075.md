# KVM + iSCSI 第一部分:带 iPXE 的 iSCSI 引导

> 原文：<https://medium.com/oracledevs/kvm-iscsi-part-i-iscsi-boot-with-ipxe-f533f2666075?source=collection_archive---------0----------------------->

![](img/d0705fce3596f2d25fa341b46edd1b04.png)

Computer Keyboard copyright [marciecasas](https://www.flickr.com/photos/marciecasas/) [CC BY](https://creativecommons.org/licenses/by/2.0/legalcode)

虽然可以将 [kvm](https://www.linux-kvm.org/page/Main_Page) 连接到 [iSCSI](https://en.wikipedia.org/wiki/ISCSI) 目标，并将其用作虚拟机的备份磁盘，但直接从 iSCSI 目标引导可能会有好处。它从磁盘读取和写入中移除了几层间接层，这可以显著改善延迟。为了做到这一点，需要一个助手从 bios 跳到远程内核。这可以通过一个叫做 [iPXE](http://ipxe.org/) 的开源软件来实现。

## 先决条件

首先，安装以下支持软件:

*   QEMU-系统-x86
*   QEMU-utils(fedora 上的 qemu-img)
*   ipxe-QEMU(fedora 上的 ipxe-roms-qemu)
*   targetcli
*   iproute2(大多数 linux 发行版都安装了这个)
*   vnc 客户端

## 网络安装程序

首先为虚拟机创建一个隔离的网桥:

```
sudo ip link add virbr0 type bridge
sudo ip link set up virbr0
```

接下来，允许 qemu 使用桥:

```
echo "allow virbr0" | sudo tee -a /etc/qemu/bridge.conf
```

最后，在桥上给虚拟机管理程序一个地址:

```
sudo ip addr add 192.168.10.1/24 dev virbr0
```

## iSCSI 目标设置

您将需要一个简单的 linux 映像来进行测试。Cirros 是一个非常小的基于 busybox 的发行版，非常适合测试:

```
curl -O  [http://download.cirros-cloud.net/0.3.4/cirros-0.3.4-x86_64-disk.img](http://download.cirros-cloud.net/0.3.4/cirros-0.3.4-x86_64-disk.img)
```

接下来，将其从 qcow 格式转换为原始驱动器:

```
qemu-img convert -O raw cirros-0.3.4-x86_64-disk.img cirros.raw
```

将转换后的图像设置为背景存储:

```
sudo targetcli /backstores/fileio/ \
create cirros $PWD/cirros.raw 100M false
```

然后，使一个新的 iSCSI lun 在虚拟机管理程序 ip 上可用:

```
sudo targetcli /iscsi create iqn.2016-01.com.example:cirros
sudo targetcli \
/iscsi/iqn.2016-01.com.example:cirros/tpg1/luns \
create /backstores/fileio/cirros
sudo targetcli \
/iscsi/iqn.2016-01.com.example:cirros/tpg1/portals \
create 192.168.10.1
```

最后，设置 ACL 并确保驱动器是可写的:

```
sudo targetcli \
/iscsi/iqn.2016-01.com.example:cirros/tpg1 set attribute \ authentication=0 demo_mode_write_protect=0 \
generate_node_acls=1 cache_dynamic_acls=1
```

## 踢

现在设置完成了，用一些合理的选项运行 qemu:

```
sudo qemu-system-x86_64                `# funky name for kvm` \
-smp cpus=2                            `# the more, the better` \
-display vnc=0.0.0.0:0                 `# to access the display` \
-boot order=n                          `# boot from the NIC` \
-netdev bridge,br=virbr0,id=virtio0    `# use our bridge` \
-device virtio-net-pci,netdev=virtio0  `# use a virtio-net device` \
```

可以使用 dhcp 选项将 ipxe 配置为引导，但我们将手动配置接口。这意味着我们必须通过 vnc 控制台连接到我们的来宾才能使用 ipxe 命令行。您应该能够使用大多数 vnc 软件连接到虚拟机管理程序机器的 ip 上的端口 5900。我在 macbook pro 上用的是[鸡](https://sourceforge.net/projects/chicken/)。

```
ifopen net0
set net0/ip 192.168.10.10
set net0/netmask 255.255.255.0
sanboot iscsi:192.168.10.1::::iqn.2016-01.com.example:cirros
```

这将为 guest 虚拟机设置一个 ip 地址，并告诉它从您之前创建的 iSCSI 目标启动。您应该会看到 cirros 在 vnc 控制台中启动。您已经直接从 iSCSI 目标启动了一个虚拟机。恭喜你！如果你想提高网络性能，你可以在[第二部分](/@vishvananda/kvm-iscsi-part-ii-pci-passthrough-a-virtual-function-1fbc1ed40311)中学习如何用 [SR-IOV](http://blog.scottlowe.org/2009/12/02/what-is-sr-iov/) 做同样的事情。