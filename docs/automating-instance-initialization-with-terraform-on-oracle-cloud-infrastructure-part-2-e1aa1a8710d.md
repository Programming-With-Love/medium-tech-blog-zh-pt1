# 在 Oracle 云基础设施上使用 Terraform 自动化实例初始化:第 2 部分

> 原文：<https://medium.com/oracledevs/automating-instance-initialization-with-terraform-on-oracle-cloud-infrastructure-part-2-e1aa1a8710d?source=collection_archive---------0----------------------->

作为上一篇关于在 Oracle 计算云上使用 Terraform 实现实例初始化自动化的文章[(该文章重点介绍了如何将`opc` Terraform provider 与 Oracle 云基础设施**经典计算**)的后续文章，在本文中，我们将探讨与用于 **Oracle 云基础设施**服务的`oci` Terraform provider 相同的一些选项，以及在配置多个实例时一些更高级的初始化选项。](/oracledevs/automating-oracle-compute-cloud-instance-initialization-with-terraform-6e66968fe30a)

与之前类似，在初始化 Oracle 云基础架构实例时，有两个不同的配置选项可供选择:

*   在实例启动期间传递 **cloud-init** 用户数据进行初始化
*   使用 **Terraform Provisioners** 在实例运行后执行配置任务。

# 使用 cloud-init 进行实例初始化

在 Oracle 云基础设施环境中，提供的 Linux 操作系统映像(包括 Oracle Linux)通过在 Terraform `oci_core_instance`资源定义的`metadata`块中提供`user_data`条目来支持 **cloud-init** 初始化。

`user_data`属性的内容必须是一个 **base64** **编码的**脚本，采用正在启动的操作系统支持的任何 cloud-init 格式，包括:

*   `#cloud-config`云配置数据
*   `#!`用户数据脚本(例如`#!/bin/bash`)
*   `#include`包含文件
*   `#cloud-boothook`云 Boothook

## 示例:带有 cloud-init 的 Linux 实例

在本例中，使用`template_file`数据源和 heredoc 符号在 Terraform 配置中定义了一个`#cloud-config`初始化脚本，以声明脚本内容。

用户数据脚本内容也可以直接从本地文件加载

```
user_data = "${base64encode(file("./mybootscript.sh"))}"
```

# 使用 Terraform 供应器初始化实例

Terraform 支持几个[供应器](https://www.terraform.io/docs/provisioners/index.html)，这些供应器能够在初始实例资源创建后协调其他供应步骤。对于下面的例子，我们将使用一个简单的`**remote-exec**` provisioner。使用 Terraform `**chef**` provisioner 的更完整示例可以在[这里](https://github.com/oracle/terraform-provider-oci/tree/master/docs/solutions/chef)找到。

## 示例:带有`remote-exec`供应器的 Linux 实例

注意在连接块中使用特殊的`${self.**public_ip**}`引用从当前实例中获取公共 IP 地址属性。如果实例没有公共 IP 地址，而 Terraform 运行的环境可以直接(或 VPN)访问私有网络，那么可以使用`${self.**private_ip**}`作为替代。

如果实例没有公共 IP 地址，并且 Terraform 没有到实例专用网络的直接连接，Terraform 通过在连接块中添加堡垒主机细节来支持通过堡垒主机的[连接。](https://www.terraform.io/docs/provisioners/connection.html#connecting-through-a-bastion-host-with-ssh)

```
resource "oci_core_instance" "example" {
  ...
  connection {
    type        = "ssh"
    host        = "${self.**private_ip**}"
    user        = "opc"
    private_key = "${file(var.ssh_private_key_file)}"

    **bastion_host        = "${oci_core_instance.bastion.public_ip}"
    bastion_user        = "opc"
    bastion_private_key = "${file(var.ssh_private_key_file)}"**
  }
  **...**
}
```

# 使用带`null_resource`的地形供应器

在某些情况下，将供应配置与实例资源定义分开是很有用的。Terraform `[**null_resource**](https://www.terraform.io/docs/provisioners/null_resource.html)`特别允许配置与单个现有资源没有直接关联的置备程序。

一个很好的例子是，在资源之间存在供应时(而不是创建时)依赖性的情况下，`null_resource`对于供应很有用。

让我们考虑一个带有数据库(DB)实例和中间件(MW)实例的配置示例。MW 实例配置要求在配置文件中设置 DB 实例的 IP 地址值。

我们声明如下:

```
resource "oci_core_instance" "**DB**" {
  ...
}resource "oci_core_instance" "**MW**" {
  ...
  provisioner "remote-exec" {
    inline = [
      "echo ${**oci_core_instance.DB.private_ip**} > /tmp/database.ip",
    ]
  }
}
```

但是通过在 MW 实例供应内容中包含对`${oci_core_instance.DB.public_ip}`的引用，MW 实例的创建现在依赖于 DB 实例，因此 Terraform 将不会开始创建 MW 资源，直到创建了 DB 实例之后。

通过将该配置的供应分离到单独的`null_resource`中，DB 和 MW 资源的创建将并行开始，并且`null_resource`供应程序将仅在两者都创建之后执行。

```
**resource "null_resource"** "config-MW" {
  triggers {
    db_private_ip = "${oci_core_instance.DB.private_ip}"
    mw_instance_id = "${oci_core_instance.MW.id}"
  } connection {
    host = "${**oci_core_instance.MW.private_ip**}"
    ...
  } provisioner "remote-exec" {
    inline = [
      "echo ${**oci_core_instance.DB.private_ip**} > /tmp/database.ip",
    ]
  }
}
```

`connection`区块和`provisioner`声明与之前相同。`triggers`部分定义了导致`null_resource`被重新创建的条件(即供应器被重新执行)。在此示例中，如果 DB **私有 IP** 地址发生变化，或者 MW **实例 ID** 发生变化(即 MW 实例被重新创建)，则重新执行供应脚本

可以在一个配置中定义多个`null_resources`来处理更复杂的实例供应需求。

## 不要重复你自己

最后一个例子展示了如何定义一个`null_resource`来在多个实例上运行同一个 provisioner，这有助于保持配置干燥

```
resource "null_resource" "cluster" {
  **count = 2**connection {
    type        = "ssh"
    host        = "${**element(list(oci_core_instance.DB.public_ip, oci_core_instance.MW.public_ip), count.index)**}"
    user        = "opc"
    private_key = "${file(var.ssh_private_key_file)}"
  } provisioner "remote-exec" {
    inline = [
      "echo 'This instance was provisioned by Terraform.' | sudo tee /etc/motd",
    ]
  }
}
```

这是通过提供资源计数**和基于计数索引从主机列表中选择要连接(和供应)的特定主机来实现的**