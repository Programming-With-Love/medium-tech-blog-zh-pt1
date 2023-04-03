# OCI 可重复使用的地形模块

> 原文：<https://medium.com/oracledevs/reusable-terraform-modules-for-oci-8fdc9ed1064b?source=collection_archive---------2----------------------->

我们最近在 GitHub 和 [Terraform registry](https://registry.terraform.io/providers/hashicorp/oci/latest) 上为 OCI 发布了一些可重用的 Terraform 模块。你可以在这里找到他们。

在这篇文章中，我将带你了解我正在研究的那些，它们的目的以及你如何在你的项目中使用它们。

## terraform-oci-vcn

[terraform-oci-vcn](https://github.com/oracle-terraform-modules/terraform-oci-vcn) 是一个创建 vcn 的模块。创建一个 VCN 很简单，那么这个模块有什么大不了的呢？它还允许你创建互联网、NAT 和服务网关，并相应地创建路由表。

让我们首先看看如何重用 VCN 模块。我们将使用注册表上的已发布版本:

```
module "vcn" {source  = "oracle-terraform-modules/vcn/oci"
version = "1.0.1"# provider parameters
region = var.region# general oci parameters
compartment_id = var.compartment_id
label_prefix   = var.label_prefix# vcn parameters
internet_gateway_enabled = var.internet_gateway_enabled
nat_gateway_enabled      = var.nat_gateway_enabled
service_gateway_enabled  = var.service_gateway_enabled
tags                     = var.tags
vcn_cidr                 = var.vcn_cidr
vcn_dns_label            = var.vcn_dns_label
vcn_name                 = var.vcn_name}
```

例如，如果您需要创建一个公共子网，您还需要创建一个路由表。假设您已经像上面那样创建了 VCN，那么您需要做的就是传递从 vcn 模块返回的非常有用的路由表 id:

```
resource "oci_core_subnet" "bastion" {  
route_table_id        = module.vcn.ig_route_id...
...}
```

## 地形-oci-bastion

帮助你添加一个堡垒主机到你的 VCN。就像 VCN 模块一样，你可以控制堡垒主机的所有方面，比如你的堡垒子网的子网掩码，堡垒主机的形状，你所在的时区等等。默认情况下，它使用 Oracle 的自主 Linux，因此您可以使用 [ksplice 来检测漏洞利用企图](https://blogs.oracle.com/linux/using-ksplice-to-detect-exploit-attempts)，并且您可以选择启用 OCI 通知主题。如果你愿意，也可以使用你自己的图像。

在开发过程中，有几个开发者特性会对你有所帮助

```
bastion_upgrade     = false
notification_enabled = false
```

通过将这些设置为 false，这将确保

1.  bastion 主机将不运行 yum 更新，因此它将更快地可用，以便任何依赖于*的*基础设施供应的剩余部分可以继续进行。
2.  通知主题不需要销毁。

它们一起可以提高安装和拆卸的速度，您也可以提高自己的工作效率。

使用 bastion 模块也很容易:

```
module "bastion" {source  = "oracle-terraform-modules/bastion/oci"
version = "1.0.2"# provider identity parameters
api_fingerprint      = var.oci_base_provider.api_fingerprint
api_private_key_path = var.oci_base_provider.api_private_key_path
region               = var.oci_base_provider.region
tenancy_id           = var.oci_base_provider.tenancy_id
user_id              = var.oci_base_provider.user_id# general oci parameters
compartment_id = var.oci_base_general.compartment_id
label_prefix   = var.oci_base_general.label_prefix# network parameters
availability_domain = var.oci_base_bastion.availability_domain
bastion_access      = var.oci_base_bastion.bastion_access
ig_route_id         = module.vcn.ig_route_id
netnum              = var.oci_base_bastion.netnum
newbits             = var.oci_base_bastion.newbits
vcn_id              = module.vcn.vcn_id# bastion parameters
bastion_enabled     = var.oci_base_bastion.bastion_enabled
bastion_image_id    = var.oci_base_bastion.bastion_image_id
bastion_shape       = var.oci_base_bastion.bastion_shape
bastion_upgrade     = var.oci_base_bastion.bastion_upgrade
ssh_public_key      = ""
ssh_public_key_path = var.oci_base_bastion.ssh_public_key_path
timezone            = var.oci_base_bastion.timezone# notification
notification_enabled  = var.oci_base_bastion.notification_enabled
notification_endpoint = var.oci_base_bastion.notification_endpoint
notification_protocol = var.oci_base_bastion.notification_protocol
notification_topic    = var.oci_base_bastion.notification_topic# tags
tags = var.oci_base_bastion.tags}
```

所有你需要确保的是你的变量值在你的变量文件中。

## terra form-OCI-运算符

[terraform-oci-operator](https://github.com/oracle-terraform-modules/terraform-oci-operator) 在其中创建一个私有子网和一个主机。主机预装了 oci-cli 和 [oci-python-sdk](https://oracle-cloud-infrastructure-python-sdk.readthedocs.io/en/latest/) ，非常有用。如果您愿意，也可以在以后添加您自己的工具或 SDK，就像我们在 [terraform-oci-oke](https://github.com/oracle-terraform-modules/terraform-oci-oke) 项目中所做的那样，在操作器上安装 kubectl 和 helm。类似地，在 [terraform-oci-olcne](https://github.com/oracle-terraform-modules/terraform-oci-olcne) 项目中，我们正在添加一系列 olcne 工具。

您可以授予[主机 instance_principal](https://docs.cloud.oracle.com/en-us/iaas/Content/Identity/Tasks/callingservicesfrominstances.htm) 访问权限，这意味着当您登录到操作员主机时，您不需要在那里存储您的 OCI 访问密钥来运行 oci-cli 命令或 oci-sdk 脚本。

默认情况下，操作员在您选择的区间内将全部权限授予主机，因此这是您应该仔细评估是否要启用的事情。此外，您可能希望降低权限级别。目前，您必须更改[代码来创建策略](https://github.com/oracle-terraform-modules/terraform-oci-operator/blob/master/instance_principal.tf)本身，但我们会在未来使其可配置。请注意，没有给操作员的通知；目前它只是一个占位符。如果您希望操作员主机有通知，您可以扩展并添加它。

```
module "operator" {source  = "oracle-terraform-modules/operator/oci"
version = "1.0.7"# provider identity parameters
api_fingerprint      = var.api_fingerprint
api_private_key_path = var.api_private_key_path
region               = var.region
tenancy_id           = var.tenancy_id
user_id              = var.user_id# general oci parameters
compartment_id = var.compartment_id
label_prefix   = var.label_prefix# network parameters
availability_domain = var.availability_domain
nat_route_id        = module.vcn.nat_route_id
netnum              = var.netnum
newbits             = var.newbits
vcn_id              = module.vcn.vcn_id# operator parameters
operator_enabled            = var.operator_enabled
operator_image_id           = var.operator_image_id
operator_instance_principal = var.enable_instance_principaloperator_shape              = var.operator_shape
operator_upgrade            = var.operator_upgradessh_public_key              = ""
ssh_public_key_path         = var.ssh_public_key_path
timezone                    = var.timezone# notification
notification_enabled  = var.notification_enabled
notification_endpoint = var.notification_endpoint
notification_protocol = var.notification_protocol
notification_topic    = var.notification_topic# tags
tags = var.oci_base_operator.tags}
```

我们创建操作员模块是因为:

1.  在某些情况下，我们需要运行调配后命令
2.  我们想给管理员一个地方，让他们可以安全地运行 oci 命令，而不需要在面向互联网的主机上传输和存储密钥
3.  我们希望做到(1)和(2)而不要求用户在本地安装额外的工具和插件，如 kubectl，helm

我们使用 [terraform-oci-oke](https://github.com/oracle-terraform-modules/terraform-oci-oke) 模块中的操作员主机安装附加插件，如 calico，或使用 [terraform-oci-olcne](https://github.com/oracle-terraform-modules/terraform-oci-olcne) 模块中的操作员主机完成安装步骤。

## 地形-oci-base

[terraform-oci-base](https://github.com/oracle-terraform-modules/terraform-oci-base) 是一个复合模块。它将上面的 3 个模块(vcn、bastion 和 operator)组装成 1 个，而不是你自己完成所有的组装。

与其他模块类似，基本模块易于使用:

```
module "base" {  
source  = "oracle-terraform-modules/base/oci"  
version = "1.2.3" # general oci parameters  
oci_base_general = local.oci_base_general # identity  
oci_base_provider = local.oci_base_provider # vcn parameters  
oci_base_vcn = local.oci_base_vcn # bastion parameters  
oci_base_bastion = local.oci_base_bastion # operator server parameters  
oci_base_operator = local.oci_base_operator }
```

terraform-oci-base 模块使用 [Terraform 复杂类型](https://www.terraform.io/docs/configuration/types.html#complex-types)作为输入。在您自己的项目中，您可以在简单类型中指定根变量，并将复杂类型创建为局部变量，然后您可以将它们用作参数，如上所示。

我们在哪里使用基本模块？目前，我们正在 [terraform-oci-oke](https://github.com/oracle-terraform-modules/terraform-oci-oke) 和 [terraform-oci-olcne](https://github.com/oracle-terraform-modules/terraform-oci-olcne) 中使用它。

## terraform-oci-oke

[terraform-oci-oke](https://github.com/oracle-terraform-modules/terraform-oci-oke) 是一个端到端 oke(托管 Kubernetes)供应模块。它设置了许多合理的默认值，但也允许您根据自己的需要进行配置。

它有很多特点:

*   公共/私营工人
*   支持混合工作负载
*   内部/公共或混合负载平衡器
*   安装流行的插件，如印花布
*   使用 Vault 中的密钥加密 etcd
*   OCIR 的秘密
*   或者工具如 kubectl 和 helm 上的操作者有相应的别名
*   在 Kubernetes 集群上创建可用于 CI/CD 的服务帐户

本模块的主要目的是改善 Kubernetes 管理员的生活。无论是供应、破坏还是重用现有基础设施，这些都是这个项目的主要指令。我们试图在添加功能和保持项目和 Kubernetes 集群尽可能精简之间取得平衡。

这样做，在项目本身或更高层次的项目中包含更多的插件变得更加容易。因此，您可以在此基础上在 OKE 集群中安装更多插件，如 OCI 服务代理，它将允许您使用 OCI 自治数据库、流和对象存储或您自己喜欢的入口控制器等服务。

## terraform-oci-olcne

terraform-oci-olcne 提供一个 [OLCNE](https://docs.oracle.com/en/operating-systems/olcne/) 环境。OLCNE 是一个自以为是的云原生产品，由用于编排的 Kubernetes 和作为服务网格的 Istio 等组成。目前，这个 terraform 模块是作为技术预览版提供的。目的是让那些有兴趣使用 OLCNE 的人使用 OCI 来做他们的评估。

## 重新使用

我们通过确保每个模块都有必要的输出来实现重用，例如，对于 vcn 模块，我们在输出中返回 VCN、NAT 网关、互联网路由表和 NAT 网关的 id。对于 bastion 模块，我们返回 bastion 主机的公共 IP 地址，对于 operator 模块，我们返回 operator 主机的私有 IP 地址。在基本模块中，因为它是一个复合模块，我们返回所有这些，但是我们也返回方便的 ssh 命令到堡垒或堡垒。

使用输出，我们现在可以做更复杂的事情，例如，对于 OKE 模块，我们希望在 Kubernetes 集群中安装 calico。在这种情况下，我们使用 null_resource 通过 bastion 对操作员进行 ssh，并为我们进行安装:

```
resource null_resource "calico_enabled" { connection {
    host        = **var.oke_operator.operator_private_ip
**    .... bastion_host        = **var.oke_operator.bastion_public_ip**
    bastion_user        = "opc"
    bastion_private_key =   
    .... 
  }
  ....
  ....  
}
```

类似地，在 OLCNE 模块中，我们没有创建单独的操作符主机，而是重用了在基本模块中创建的现有操作符。然后，我们通过额外的网络配置(如 NSG(网络安全组)、获取私有 ssh 密钥等)来扩展运营商。

## GitHub 克隆 vs GitHub 模块 vs 注册表

什么时候你会从 GitHub 克隆一个模块到你自己的 terraform 项目中，使用 GitHub 作为你的 Terraform 模块或者注册表的源？

这里有一个简单的准则:

*   当您需要更改代码、向底层模块添加现有功能时，克隆 GitHub repo
*   如果你需要一些改变，但是它们还没有添加到一个版本中，并且你没有耐心去尝试，那么就使用 GitHub 模块
*   如果您只想重用，请使用注册模块。

## IP 地址管理

设计云基础设施的一大挑战是 IP 地址管理。我们必须在可扩展性与[爆炸半径](https://blog.avinetworks.com/change-control-blast-radius)、[子网数量与大小](http://blog.itsjustcode.net/blog/2017/11/18/terraform-cidrsubnet-deconstructed/)之间、安全性与管理简易性之间取得平衡，同时避免子网重叠。此外，有时用户可能还想重用现有基础设施(vcn、子网)，与其他 vcn 对等，以混合模式部署或使用[中转路由](https://docs.cloud.oracle.com/en-us/iaas/Content/Network/Tasks/transitrouting.htm)。

因此，为您的虚拟专用网络和子网选择合适的 IP 范围非常重要。Terraform 提供了 [cidrsubnet](https://www.terraform.io/docs/configuration/functions/cidrsubnet.html) 功能来以编程方式创建您的网络。我强烈建议您阅读关于网络、子网和 CIDR 的复习资料以及关于使用 cidrsubnet 功能的优秀文章。

在 terraform-oci-oke 和 terraform-oci-olcne 模块中，我们展示了如何以编程方式使用该函数。

对于每个项目，我们需要以下子网(~括号中的大小):

*   OKE: bastion (3)、operator (3)、worker(5000-Kubernetes 支持的最大工作节点数)、内部(30)和公共负载平衡器(30)
*   OLCNE: bastion (3)、operator (3)、master (5)、worker (5000)、internal(30)和 public load 平衡器(30)

为什么堡垒和运营商需要 3 个 IP 地址？嗯，将来我们可能会想使用一个保留的公共 IP 地址、实例池和一个私有的浮动 IP 地址。至于负载平衡器，大约 30 的默认值给了我们很多选择。所有这些当然都取决于用户，但我们希望能够从一些合理的违约开始，同时给自己足够的增长空间。此外，我们需要记住，子网中的前 2 个和最后一个 IP 地址是在 OCI 保留的。

使用 IP Calc 工具和 terraform 控制台，我们可以开始划分子网。就地形而言，给定 VCN 的 CIDR 区块，我们要找的是每个子网的纽比特和 netnum 参数。

这是 terraform-oci-oke 的默认设置:

```
variable "netnum" { description = "0-based index of the subnet when the network is masked with the newbit. Used as netnum parameter for cidrsubnet function." default = {
    bastion  = 32
    int_lb   = 16
    operator = 33
    pub_lb   = 17
    workers  = 1
  }
  type = map
}variable "newbits" { description = "The masks for the subnets within the virtual network. Used as newbits parameter for cidrsubnet function." default = {
    bastion  = 13
    lb       = 11
    operator = 13
    workers  = 2
  }
  type        = map
}
```

我们在地图中设置默认值，现在我们可以使用这些值将它们各自的值传递给基本模块(基本模块随后将把它们传递给堡垒和操作员模块)，以及 terraform-oci-oke 中的网络模块。

在网络模块中，我们查找负载平衡器和 workers 的值，并使用 cidrsubnet 函数来创建它们:

```
# define in locals
worker_subnet  = cidrsubnet(var.oke_network_vcn.vcn_cidr, var.oke_network_vcn.newbits["workers"], var.oke_network_vcn.netnum["workers"])# reference the local variable
resource "oci_core_subnet" "workers" {
  cidr_block                 = local.worker_subnet
  ....}
```

现在我们有了一种雕刻 VCN 的方法，我们可以使用 terraform 变量文件来控制这些值。

## 结论

你可以尝试使用上面的模块，在它们的基础上构建并发布你自己的模块。他们负责大量重复性任务和基础设施需求。

如果您有其他要求，请通过 [GitHub](https://github.com/oracle-terraform-modules) 联系我们。非常感谢您的时间和贡献。