# 升级到 Terraform 0.12 的经验教训-第 2 部分

> 原文：<https://medium.com/oracledevs/lessons-learned-when-upgrading-to-terraform-0-12-part-2-b33ade13f20c?source=collection_archive---------0----------------------->

在之前的一篇文章中，我列出了升级到 Terraform 0.12 要做的事情。这些包括:

1.  修复重大变更
2.  使用一流的表达式
3.  使用改进的条件句
4.  使用动态块减少代码重复
5.  升级独立模块

在这篇文章中，我将主要关注使用丰富类型和[类型约束](https://www.terraform.io/docs/configuration/types.html)。

## 原始类型

正如定义所说，基本类型是一种简单类型，它不是由任何其他类型构成的。基本类型是字符串、数字和布尔值。

Terraform 会在需要时自动将数字和布尔值转换为字符串，反之亦然。然而，明确定义变量类型以避免歧义是一个好习惯。

## 复杂类型

一个[复杂类型](https://www.terraform.io/docs/configuration/types.html#complex-types)是一种*将多个值*组合成一个值的类型。

有两种复杂类型:

1.  集合:列表、地图、集合
2.  结构化:对象，元组

集合类型*通常是*同一类型*的多个值*分组在一个集合下，例如

```
// list of integers
gitlab_lb_ports = [22, 80, 443]// map of [string,integers]
variable "availability_domains" {
  description = "ADs where to provision non-OKE resources"
  type        = "map"
  default = {
    bastion = 1
  }
}
```

偶尔，你也可能需要混合类型。您可以创建自定义的结构类型，也可以使用元组。我们将很快回到结构类型，但是让我们看看元组给我们提供了什么。

元组允许你定义一系列的元素和它们各自的类型，例如

```
//vcn_name, cidr, create_nat_gateway         
variable vcn = tuple([string,string,bool])
```

使用元组时，您的值必须按照指定的顺序匹配相同数量和类型的参数，例如

```
// bad tuple based on the above definition
vcn = [false,"my_vcn","10.0.0.0/16"]//good tuple
vcn = ["my_vcn","10.0.0.0/16",false]
```

如您所见，变量的顺序和类型与元组有关。

结构类型允许你创建任何对象模式

```
variable "oke_identity" {
  type = object({
    compartment_id = string
    user_id        = string
  })
}variable "oke_bastion" {
  type = object({
    bastion_public_ip         = string
    create_bastion            = bool
    enable_instance_principal = bool
  })
}
```

此外，结构类型还可以包含集合类型的属性，例如

```
variable "oke_general" {
  type = object({
    **ad_names     = list(string)**
    label_prefix = string
    region       = string
  })
}variable "oke_network_vcn" {
  type = object({
    ig_route_id                = string
    is_service_gateway_enabled = bool
    nat_route_id               = string
    **newbits                    = map(number)**
    **subnets                    = map(number)**
    vcn_cidr                   = string
    vcn_id                     = string
  })
}
```

当属性由集合类型组成时，您还必须指定上面突出显示的值的类型。

有时，您可能还需要传递一个混合类型的集合，然后您可以通过[集合函数](https://www.terraform.io/docs/configuration/functions.html)检索该集合。在这种情况下，你可以使用'*任何*类型例如

```
variable "node_pools" {
  **type        = map(any)**
  description = "number of node pools"
}
```

允许我们传递这样的值:

```
node_pools = {
  "np1" = ["VM.Standard.E2.1", 3]
  "np2" = ["VM.Standard2.1", 4]
  "np3" = ["VM.Standard.E2.2", 5]
}
```

现在，您已经知道如何定义类型，剩下 3 个最后的问题:

1.  如何创建结构类型
2.  当使用模块/子模块时，如何使用/重用它们
3.  如何指定根变量

## 创建结构类型

如果你熟悉面向对象(OOP)原则，你可以相应地定义你的类型。例如，在 [terraform-oci-oke](https://github.com/oracle-terraform-modules/terraform-oci-oke) 项目中，我们定义了一组对变量进行分组的对象:

```
variable "oci_base_identity" {
  type = object({
    api_fingerprint      = string
    api_private_key_path = string
    compartment_id       = string
    compartment_name     = string
    tenancy_id           = string
    user_id              = string
  })
}variable "oci_base_ssh_keys" {
  type = object({
    ssh_private_key_path = string
    ssh_public_key_path  = string
  })
}
```

您通常会将特定对象/资源的属性或相关参数分组在一起，例如，oci_base_identity 包含与身份相关的属性，而 oci_base_ssh_keys 包含公共和私有 ssh 密钥对路径。

## 将结构类型用于模块/子模块

如果你有几个模块，我发现在变量名的前面加上模块名的前缀*是一个好习惯，这样我就知道这些变量在哪里被使用。*

```
//base module variables **oci_base**_identity = {
...
}**oci_base**_ssh_keys = {
...
}**oci_base**_general = {
...
}//oke_module variables
**oke**_general = {
...
}**oke**_bastion = {
...
}//oke_network variables
**oke_network**_vcn = {
...
}
```

## 指定根变量

这一条可能会有点争议，但这是我的观点:

1.  如果您的 terraform 项目是一个可重用模块，并且将被另一个项目重用，请在根变量中使用结构类型
2.  如果您的 terraform 项目将用于实际创建基础设施，而不是为了重用，请尽可能使用平面结构和简单/集合类型

在第一种情况下，开发人员将针对您的根变量(实际上是您的模块的 API)编写代码。因此，他们应该理解你的变量和它们的类型，以便有效地调用和重用你的模块。他们甚至可能阅读你的代码。

使用结构类型还有助于减少他们必须编写的样板代码的数量，例如在 Terraform 0.11 中，如果没有对象类型，我们必须这样做来定义和重用基本模块:

```
module "base" {  
  source = "./modules/base"   

  # identity   
  api_fingerprint      = "${var.api_fingerprint}"
  api_private_key_path = "${var.api_private_key_path}"
  compartment_name     = "${var.compartment_name}"  
  compartment_ocid     = "${var.compartment_ocid}"  
  tenancy_ocid         = "${var.tenancy_ocid}"  
  user_ocid            = "${var.user_ocid}"  
  ssh_private_key_path = "${var.ssh_private_key_path}"
  ssh_public_key_path  = "${var.ssh_public_key_path}"     # general    label_prefix = "${var.label_prefix}"  
  region       = "${var.region}"     # networking    newbits                = "${var.newbits}"  
  subnets                = "${var.subnets}"  
  vcn_cidr               = "${var.vcn_cidr}"  
  vcn_dns_name           = "${var.vcn_dns_name}"  
  vcn_name               = "${var.vcn_name}"  
  create_nat_gateway     = "${var.create_nat_gateway}"
  nat_gateway_name       = "${var.nat_gateway_name}"  
  create_service_gateway = "${var.create_service_gateway}"  
  service_gateway_name   = "${var.service_gateway_name}"   # bastion  
  bastion_shape               = "${var.bastion_shape}"   
  create_bastion              = "${var.create_bastion}"  
  enable_instance_principal   ="${var.enable_instance_principal}"  
  image_ocid                  = "${var.image_ocid}"  
  image_operating_system      = "${var.image_operating_system}"   
  image_operating_system_version = "${var.image_operating_system_version}" # availability_domains
availability_domains = "${var.availability_domains}"}
```

使用对象类型时，请在 Terraform 0.12 中进行比较:

```
module "base" {  
  source = "./modules/base"   

  # identity  
  oci_base_identity = local.oci_base_identity # ssh keys  
  oci_base_ssh_keys = local.oci_base_ssh_keys  # general oci parameters  
  oci_base_general = local.oci_base_general     # vcn parameters  
  oci_base_vcn = local.oci_base_vcn     # bastion parameters  
  oci_base_bastion = local.oci_base_bastion
}
```

你可以在本地做脏活:

```
locals {     oci_base_identity = {    
    api_fingerprint      = var.api_fingerprint    
    api_private_key_path = var.api_private_key_path      
    compartment_name     = var.compartment_name    
    compartment_id       = var.compartment_id    
    tenancy_id           = var.tenancy_id    
    user_id              = var.user_id  
  }

  oci_base_ssh_keys = {    
    ssh_private_key_path = var.ssh_private_key_path       
    ssh_public_key_path  = var.ssh_public_key_path  
  }
 ....
}
```

最后，当您向模块添加新特性并将新版本发布到注册中心时，您可以使用[语义版本控制](https://semver.org/)来确保模块的用户及其下游用户不会遇到兼容性问题。这是我们在可重用的 [terraform-oci-base](https://github.com/oracle-terraform-modules/terraform-oci-base) 模块中采用的方法。重用它将如下所示:

```
module "base" {
  source  = "mybase"
 **version = "1.0.1"**
}
```

在第二种情况下，最终用户不是开发人员，他们需要在运行 Terraform 时创建变量文件。因此，如果结构是平面的，他们更容易创建变量文件。我对这种方法的灵感来自于 [TOML](https://github.com/toml-lang/toml) ，它“旨在成为最小化的配置文件格式，由于明显的语义而易于阅读。”

虽然 terraform 变量文件结构并不完全符合 TOML，但这里的目的是让用户可以轻松地创建配置文件，因此在这种情况下，为根变量保持一个平面结构是更可取的。这是我们在 [terraform-oci-oke](https://github.com/oracle-terraform-modules/terraform-oci-oke) 模块中采用的方法。

## Terraform 提示:使用 for 对数字列表求和

我在试图计算一系列数字的总和时发现了这个[小宝石](https://github.com/hashicorp/terraform/issues/17239)。

## 摘要

使用富类型时，请使用以下方法:

1.  如果你的项目是一个可重用的模块，在你的根变量中使用丰富的类型。否则，使用扁平结构，使最终用户使用起来简单。
2.  显式指定变量的类型
3.  使用 OOP 原则对变量进行分组
4.  在变量名前面加上使用它们的模块名
5.  尽可能使用集合，并指定它们将使用的值类型
6.  [漂亮的机器人](https://medium.com/u/5771c68b8656?source=post_page-----b33ade13f20c--------------------------------)建议[在本地](/@nicerobot/i-will-argue-that-when-possible-concatenation-should-be-done-in-locals-76444655537f)做拼接。更进一步，在局部变量中进行对象初始化。