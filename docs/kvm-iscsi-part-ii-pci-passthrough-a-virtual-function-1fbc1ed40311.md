# KVM + iSCSI 第二部分:PCI-通过虚拟函数

> 原文：<https://medium.com/oracledevs/kvm-iscsi-part-ii-pci-passthrough-a-virtual-function-1fbc1ed40311?source=collection_archive---------1----------------------->

![](img/c95d042d04591298a9f717b124f13cf6.png)

Image copyright [ibmphoto24](https://www.flickr.com/photos/ibm_media/) [CC-BY-NC-ND](https://creativecommons.org/licenses/by-nc-nd/2.0/legalcode)

## 先决条件

首先，确保完成[第一部分](/@vishvananda/kvm-iscsi-part-i-iscsi-boot-with-ipxe-f533f2666075)，设定你的 iSCSI 目标。为了遵循本教程，您将需要一些额外的东西:

*   一个 [SR-IOV](http://blog.scottlowe.org/2009/12/02/what-is-sr-iov/) 能力的网卡。
*   [IOMMU 在 BIOS 中启用](https://www.techwalla.com/articles/how-to-enable-iommu-bios)
*   [在内核命令行中启用 IOMMU】](https://wiki.archlinux.org/index.php/PCI_passthrough_via_OVMF#Setting_up_IOMMU)
*   [iPXE 构建先决条件已安装](http://ipxe.org/download)

## 设置虚拟功能

我们必须做的第一件事是确保您启用了虚函数。`PARENT`是支持虚拟功能的物理设备的名称。以下命令适用于英特尔网卡。您可能需要查阅卡的手册，以了解如何在卡上设置虚拟功能:

```
PARENT=ens2f0 # make sure to replace this with your NIC
echo 4 | sudo tee /sys/class/net/$PARENT/device/sriov_numvfs
```

现在为下一系列命令设置几个 bash 变量:

```
PCI=$(basename $(readlink /sys/class/net/$PARENT/device/virtfn0))
DRIVER=$(lspci -v -s $PCI | grep modules | awk '{print $NF}')
VENDOR=$(cat /sys/class/net/$PARENT/device/virtfn0/vendor)
DEVICE=$(cat /sys/class/net/$PARENT/device/virtfn0/device)
```

接下来，将虚拟函数绑定到 vfio_pci 驱动程序:

```
sudo modprobe vfio_pci
echo $PCI | sudo tee /sys/bus/pci/drivers/$DRIVER/unbind
echo ${VENDOR##*x} ${DEVICE##*x} \
> /sys/bus/pci/drivers/vfio-pci/new_id
echo $PCI | sudo tee /sys/bus/pci/drivers/vfio-pci/bind
```

## 重新配置虚拟网络

将虚拟功能放在它自己的 vlan 上，这样您就不必担心虚拟机管理程序流量了:

```
sudo ip link $PARENT set vf 0 vlan 10
sudo ip link add vlan10 link $PARENT type vlan id 10
sudo ip link set up vlan10
sudo ip link set master virbr0 vlan10
```

将虚拟机管理程序 ip 从网桥移动到 vlan 设备上:

```
sudo ip addr del 192.168.10.1/24 dev virbr0
sudo ip addr add 192.168.10.1/24 dev vlan10
```

您可能会发现流量无法在虚拟功能和虚拟机管理程序 vlan 设备之间正常传输。如果发生这种情况，您可以告诉设备将数据包传输到交换机，并让交换机将它们环回:

```
sudo bridge link set dev $PARENT hwmode vepa
```

请注意，只有当交换机设置为传输 vlan 10 且端口安全关闭时，上述操作才有效。

## 启动来宾

此时，您应该能够成功地将虚拟函数传递给 guest 虚拟机:

```
sudo qemu-system-x86_64 \
-smp cpus=2 \
-display vnc=0.0.0.0:0 \
-boot order=n \
-net none \
-device vfio-pci,host=$PCI
```

但是，如果您连接到 vnc 控制台，您会注意到没有 iPXE。您的网络设备没有预建的 ipxe rom。

## 构建 iPXE

您将不得不从源代码构建 iPXE，所以抓住它:

```
git clone git://git.ipxe.org/ipxe.git
cd ipxe
```

因为您是自己构建的，所以可以嵌入一个脚本来避免捕捉 iPXE 提示符和手动键入命令:

```
cat > script.ipxe << "EOF"
ifopen net0
set net0/ip 192.168.10.10
set net0/netmask 255.255.255.0
sanboot iscsi:192.168.10.1::::iqn.2016-01.com.example:cirros
EOF
```

使用针对特定虚函数的脚本构建一个 rom:

```
ID=${VENDOR##*x}${DEVICE##*x}
export EMBED=$PWD/script.ipxe
(cd src && make bin/$ID.rom)
```

如果出现错误，ipxe 可能不支持您的网卡。您可以使用通用网络驱动程序(undionly)来代替:

```
(cd src && make bin/undionly.rom)
```

# 使用自定义 iPXE 启动

使用自定义 ipxe 映像再次启动 qemu:

```
sudo qemu-system-x86_64 \
-smp cpus=2 \
-display vnc=0.0.0.0:0 \
-boot order=n \
-net none \
-device vfio-pci,host=$PCI,romfile=$PWD/src/bin/$ID.rom
```

如果您被迫使用通用网络驱动程序，您可能需要使用稍微不同的命令行来指定它:

```
sudo qemu-system-x86_64 \
-smp cpus=2 \
-display vnc=0.0.0.0:0 \
-boot order=n \
-net none \
-device vfio-pci,host=$PCI \
-option-rom $PWD/src/bin/undionly.rom
```

无论哪种情况，您都应该看到网络设备自动配置自己并引导 cirros 映像。恭喜你，你已经使用超快的虚拟函数通过网络启动了一个虚拟机！