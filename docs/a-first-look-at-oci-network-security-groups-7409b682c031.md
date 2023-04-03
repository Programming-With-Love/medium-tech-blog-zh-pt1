# OCI 网络安全组织初探

> 原文：<https://medium.com/oracledevs/a-first-look-at-oci-network-security-groups-7409b682c031?source=collection_archive---------4----------------------->

OCI 平台提供商`terraform-provider-oci v3.33.0`引入对**网络安全组织**的支持。在我看来，这是如何在您的平台配置中定义 [IaaS](https://www.oracle.com/cloud/what-is-iaas/) 安全规则的游戏规则改变者。

[](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/networksecuritygroups.htm) [## 网络安全组

### 了解如何使用网络安全组在数据包级别控制特定虚拟网卡组的流量。

docs.cloud.oracle.com](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/networksecuritygroups.htm) 

使用当前的 VCN 安全列表，安全规则将应用于使用该安全列表的子网内的所有 ***实例虚拟网卡。相比之下，网络安全组安全规则适用于整个 VCN 中定义的 vNIC 子集，而不管 vNIC 位于哪个子网。安全上下文与网络拓扑的这种分离允许更细粒度的安全控制。***

*对于即将使用* ***Oracle 云基础架构经典版、*** *的用户来说，这有点类似于定义 IP 网络虚拟网卡集*

让我们考虑几个可以使用新网络安全组的潜在使用案例:

**将数据库和应用层实例整合到一个子网中**。一种常见的安全体系结构最佳实践是将数据库和应用程序层的访问分成单独的数据库和应用程序子网，并使用子网安全列表来控制两者之间允许的流量。通过网络安全组，数据库和应用程序实例可以部署在同一个*子网中，同时维护不同的安全规则集。*

***跨子网的细粒度访问控制**。即使应用程序架构在数据库和应用层之间保持网络隔离，网络安全组也可以确保只允许需要直接访问特定数据库实例的应用程序层实例的子集，防止不属于安全上下文的其他应用程序层实例访问数据库。*

***跨所有子网的通用实例类型的单个安全组**。如果您混合使用 Windows 和 Linux 服务器，您可以定义一个 Windows 特定的网络安全组，以支持对 Windows 特定端口(如 WinRM)的访问，这些端口只能应用于 Windows 实例，而不管它们部署在哪个子网中。同一子网中(而不是安全组中)的任何 Linux 实例都不会启用 Windows 特定的端口访问。*

***将特定于应用的安全规则从基础设施中分离出来**。使用网络安全组可以消除基线网络供应和应用程序资源供应之间的紧密耦合。模块，甚至单独的配置，可以在现有网络上覆盖应用特定的安全规则，而不需要修改基线网络安全列表和子网资源。*

# ***使用 Terraform 配置网络安全组***

*使用新的网络安全组资源非常简单。我们从创建`[**oci_core_network_security_group**](https://www.terraform.io/docs/providers/oci/r/core_network_security_group.html)`资源开始。*

```
*resource "**oci_core_network_security_group**" "example" {
  compartment_id = var.compartment_id
  vcn_id         = oci_core_vcn.vcn1.id
  display_name   = "Example Security Group"
}*
```

*我们现在可以向安全组添加一个或多个安全规则。与 VCN 安全列表规则不同，每个网络安全组安全规则都被定义为单独的`[**oci_core_network_security_group_security_rule**](https://www.terraform.io/docs/providers/oci/r/core_network_security_group_security_rule.html)` 资源。*

```
*resource "**oci_core_network_security_group_security_rule**" "https" {
  network_security_group_id = oci_core_network_security_group.example.id

  description = "HTTPS"
  direction   = "INGRESS"
  protocol    = 6
  source_type = "CIDR_BLOCK"
  source      = "0.0.0.0/0" tcp_options {
    destination_port_range {
      min = 443
      max = 443
    }
  }
}*
```

*值得强调的是，除了为网络或服务 CIDR 设置规则`source`(或出口规则`destination`)之外，网络安全组安全规则还有一些独特的功能，允许规则控制同一 VCN 中两个独立网络安全组之间的流量，或规则自己的网络安全组控制同一组内 vNICs 之间的流量。*

*最后，我们通过在实例资源或辅助 vNIC 的 vNIC 附件资源的`create_vnic_details`中添加`[**nsg_ids**](https://www.terraform.io/docs/providers/oci/r/core_instance.html#nsg_ids)`属性，将网络安全组与实例的特定 vNIC 相关联。*

```
*resource "**oci_core_instance**" "instance1" {
  availability_domain = local.availability_domain
  compartment_id      = var.compartment_id
  display_name        = "instance1"
  ... **create_vnic_details** {
    subnet_id        = oci_core_subnet.vcn1_subnet1.id
    assign_public_ip = true
    hostname_label   = "instance1"
    **nsg_ids** = [
      **oci_core_network_security_group.example.id**
    ]
  }
}*
```

****注意*** *:根据所使用的基本操作系统，可能需要适当修改本地实例防火墙规则，以允许安全规则启用的接口/端口上的流量。**

*可以将新的网络安全组添加到现有接口，而无需强制重新配置实例。*

## *限制*

*永远知道自己的极限。此处介绍了网络安全组资源的注意限制[。总而言之:](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/securityrules.htm#limits)*

*   *您可以在 VCN 定义多达 **1000** 个网络安全组*
*   *您可以在一个网络安全组中定义多达 **120** 条安全规则*
*   *单个虚拟网卡最多可以加入 **5 个**网络安全组*

# *相关链接*

*[](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/securityrules.htm) [## 安全规则

### 了解如何使用安全规则在数据包级别控制 VCN 资源的流量。

docs.cloud.oracle.com](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/securityrules.htm)*