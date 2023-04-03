# 使用 Terraform 为 Oracle 云基础架构经典实例配置永久块存储附件

> 原文：<https://medium.com/oracledevs/using-terraform-to-provision-oracle-cloud-infrastructure-classic-instances-with-persistent-block-ef47fb90c2b0?source=collection_archive---------0----------------------->

本文通过使用`[opc](https://www.terraform.io/docs/providers/opc/index.html)`提供者，研究了在使用持久引导和数据存储卷时，使用 Terraform 配置和管理 Oracle 云基础架构计算经典实例生命周期的选项。

首先让我们看看 Terraform 如何提供一个基本的计算实例

```
resource "opc_compute_instance" "storage-example" {
  name  = "storage-example"
  image = "/oracle/public/OL_7.2_UEKR4_x86_64"
  shape = "oc3"
}
```

由于没有明确定义存储资源，Terraform 将创建一个*临时*实例，用于本地引导存储。当实例被销毁时，本地存储就消失了，并且不可恢复。当没有本地持久性需求时，临时实例是理想的，但是如果您需要在错误或中断后启动/停止、调整大小或轻松恢复实例，那么实例将需要持久性块存储。

# 永久启动卷

让我们扩展基本实例定义，添加一个持久引导卷。

```
**resource "opc_compute_storage_volume" "boot-volume" {
  size = "20"
  name = "boot-volume"
  bootable = true
  image_list = "/oracle/public/OL_7.2_UEKR4_x86_64"
  image_list_entry = 1
}**resource "opc_compute_instance" "storage-example" {
  name = "storage-example"
  shape = "oc3"
  **storage {
    index = 1
    volume = "${opc_compute_storage_volume.boot-volume.name}"
  }
  boot_order = [ 1 ]** }
```

现在，Terraform 将使用所请求的基本映像创建一个可引导块存储卷，创建附加了引导卷的实例，并从该卷引导实例。使用`**boot_order**`属性识别哪个存储附件用作引导卷。

使用单独的实例和存储资源，现在可以修改**实例资源**(例如，将所需状态从运行设置为暂停，或者更改形状)，而存储卷将保持不变。修改**存储资源**定义(例如，更改大小、存储类型或映像)仍然是破坏性事件。Terraform `**prevent_destroy**`标志可用于降低意外破坏存储资源的几率。

```
resource "opc_compute_storage_volume" "boot-volume" {
  size = "20"
  name = "boot-volume"
  bootable = true
  image_list = "/oracle/public/OL_7.2_UEKR4_x86_64"
  image_list_entry = 1
 **lifecycle {
    prevent_destroy = true
  }**
}
```

# 持久数据卷

除了启动卷，我们还可以附加多个数据卷。这有助于将操作系统从应用程序和数据分区中分离出来，并且更容易调整大小。让我们向实例定义中添加一个新的数据量。

```
resource "opc_compute_storage_volume" "boot-volume" {
...
}**resource "opc_compute_storage_volume" "data-volume" {
  size = "20"
  name = "data-volume"
}**resource "opc_compute_instance" "storage-example" {
  name = "storage-example"
  shape = "oc3"
  storage {
    index = 1
    volume = "${opc_compute_storage_volume.boot-volume.name}"
  }
  **storage {
    index = 2
    volume = "${opc_compute_storage_volume.data-volume.name}"
  }**
  boot_order = [ 1 ]}
```

很好，现在有了一个包含持久引导和数据卷的实例。如前所述,`**prevent_destroy**`选项可用于进一步防止资源被意外删除。如果您在上面的配置上运行`terraform apply`，您会注意到实例资源被销毁并重新创建，这是因为在实例定义中声明的存储附件是启动定义的一部分，并且是在引导时间附加的*。*

您通常希望在引导时连接存储，使启动脚本能够自动启动任何应用程序并访问存储卷上的数据，但是让我们考虑这样一种情况:您希望将存储卷连接到一个已经运行的实例，而不停止并重新创建该实例。

# 动态存储附件

使用**存储附件**资源，我们可以将新的存储资源关联到已经创建/运行的实例。将以下内容添加到之前的配置中

```
resource "opc_compute_storage_volume" "attached-volume" {
  size = "20"
  name = "attached-volume"
}resource "opc_compute_storage_attachment" "storage-attachment" {
  instance = "${opc_compute_instance.storage-example.name}"
  index = 3
  storage_volume = "${opc_compute_storage_volume.attached-volume.name}"
}
```

这里的一个关键区别是，存储附件仅在实例创建完成之后与实例*相关联，包括运行可能是实例定义一部分的任何置备程序。新卷的检测和安装将特定于实例操作系统(参见[安装和卸载存储卷](https://docs.oracle.com/en/cloud/iaas/compute-iaas-cloud/stcsg/mounting-and-unmounting-storage-volume.html))。对于运行 Oracle Linux 的实例，新的存储附件将自动显示为块设备`**/dev/xvdd**`*

使用存储附件资源中的`**remote-exec**`置备程序，可以在 Terraform 配置中管理存储卷的格式化和安装。

*注意:此处我们假设实例已经配置了 ssh 访问，使用了适当提供的 ssh 密钥，并且可以访问本地网络上的 Terraform 为了简洁和不离题，配置了适当的密钥、bastion 主机、vpn、ip 网络和/或公共 ip 预留等。留给读者一个练习。*

```
resource "opc_compute_storage_attachment" "storage-attachment" {
  instance = "${opc_compute_instance.storage-example.name}"
  index = 3
  storage_volume = "${opc_compute_storage_volume.attached-volume.name}" **connection {
    type = "ssh"
    host = "${opc_compute_instance.storage-example.ip_address}"
    user = "opc"
    private_key = "${file(var.ssh_private_key_file)}"
    timeout = "10m"
  }** **provisioner "remote-exec" {
    inline = [
      "****sudo mkfs -t ext3 /dev/xvdd****",     # FORMAT THE VOLUME
      "****sudo mkdir /mnt/store",           # CREATE THE MOUNT POINT
      "sudo mount /dev/xvdd /mnt/store"  # MOUNT THE VOLUME** **]
  }**
}
```

此示例将在每次(重新)连接存储时重新格式化存储，如果您使用的存储卷已经包含数据，则需要删除该步骤(例如，以前连接到不同实例或从快照恢复的卷)

初始化和安装存储对于使用存储卷显然很重要，但可能更重要的是确保干净地分离存储卷，以避免潜在的数据损坏。销毁存储附件资源相当于在没有首先弹出的情况下拔出 USB 驱动器。

Terraform 提供了一个特殊的[销毁时间供应器](https://www.terraform.io/docs/provisioners/index.html#destroy-time-provisioners)选项，用于在销毁而非创建期间执行命令。要确保在分离之前卸载卷，请将以下附加的`**remote-exec**`置备程序添加到存储附件资源定义中

```
 **provisioner "remote-exec" {
    when = "destroy"
    inline = [** **"sudo umount /mnt/store"  # UNMOUNT THE VOLUME** **]
  }**
```

**然而**，请务必注意 Terraform 文档中的以下片段:

> 销毁时置备程序只有在销毁资源时仍在配置中才能运行。如果从配置中完全删除带有销毁时置备程序的资源块，其置备程序配置也会随之删除，因此销毁置备程序将不会运行。要解决此问题，可以使用销毁时置备程序通过多步过程安全删除资源:
> 
> 更新资源配置以包括`count = 0`。
> 
> 应用配置以销毁资源的任何现有实例，包括运行销毁置备程序。
> 
> 从配置中完全删除资源块及其`provisioner`块。
> 
> 再次申请，此时不应采取进一步的行动，因为资源已经被破坏。