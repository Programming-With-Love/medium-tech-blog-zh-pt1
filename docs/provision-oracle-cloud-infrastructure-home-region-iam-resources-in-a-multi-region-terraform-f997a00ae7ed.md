# 在多区域平台配置中调配 Oracle 云基础架构主区域 IAM 资源

> 原文：<https://medium.com/oracledevs/provision-oracle-cloud-infrastructure-home-region-iam-resources-in-a-multi-region-terraform-f997a00ae7ed?source=collection_archive---------1----------------------->

本文涵盖了一个我一直想发布的常见配置模式。在 Oracle Cloud Infrastructure (OCI)中调配 [IaaS](https://www.oracle.com/cloud/what-is-iaas/) 资源时，一些资源，具体来说是 **IAM** 资源，只能在 [**主区域**](https://docs.cloud.oracle.com/iaas/Content/Identity/Tasks/managingregions.htm#home) 中创建。

如果配置需要创建 IAM 资源，如新的隔离专区或部署特定策略，则 Terraform 配置需要以多个地区为目标。Terraform 允许通过声明额外的提供者并分配一个[提供别名](https://www.terraform.io/docs/configuration/providers.html#alias-multiple-provider-instances)来提供给多个地区，因此举一个简单的例子

```
**# default provider for the configuration**
provider "oci" {
  region = var.region
}**# provider for home region for IAM resource provisioning**
provider "oci" {
  **alias  = "home"**
  region = "us-ashburn-1"
}**# create the compartment in the Home region**
resource oci_identity_compartment compartment {
 **provider       = oci.home** compartment_id = var.tenancy_id
  name           = "My_Compartment"
  description    = "My Compartment"
}
```

这将在主区域`us-ashburn-1`中创建区间，配置中的任何其他资源都将在`var.region`中创建。但是并不是每个租户的家都在阿什本，所以让我们看看如何使它更具可移植性，而不需要向配置变量添加一个单独的`home_region`输入参数。

`oci_identity_tenancy`数据源可用于查找租赁的家庭区域。

```
data oci_identity_tenancy tenancy {
  tenancy_id = var.tenancy_ocid
}output home_region {
  value = oci_identity_tenancy.tenancy.home_region_key
}**home_region = IAD**
```

但是这给了我们一个三个字母的地区代码，例如`IAD`。为了将它转换成 provider `region`属性所期望的格式，我们需要一个 home region 键到 region ids 的映射。使用`oci_identity_regions`数据源，我们得到一个完整的区域列表，从中我们可以创建一个查找映射，并使用 home region 键查找值。

```
data oci_identity_regions regions {
}locals {
  region_map = {
    for r in data.oci_identity_regions.regions.regions :
    r.key => r.name
  } home_region = lookup(
    local.region_map, 
    data.oci_identity_tenancy.tenancy.home_region_key
  )
}output home_region {
  value = local.home_region
}**home_region = us-ashburn-1**
```

现在我们可以在提供者定义中使用`local.home_region`:

```
**# provider for home region for IAM resource provisioning**
provider "oci" {
  alias  = "home"
  region = **local.home_region** }
```

## 等待 IAM 资源跨区域传播

调配 IAM 资源时的一个重要注意事项是，在主区域中创建、更新或删除的资源需要一段时间才能传播到租赁的所有其他区域。如果在目标(非主)区域中供应的资源依赖于新的 IAM 资源，例如，在新的隔离专区中供应，或者依赖于新创建的策略，如果新资源没有及时传播，供应可能(将)失败。

等待新资源传播的一种方法是为在主区域中创建的同一资源添加一个数据源。依赖于 IAM 资源的资源将引用数据源，而不是直接引用 IAM 资源。在上述示例的基础上，我们添加了隔离专区数据源和要在隔离专区中创建的 VCN:

```
**# create the compartment in the Home region**
resource oci_identity_compartment compartment {
 **provider = oci.home**  compartment_id = var.tenancy_id
  name           = "My_Compartment"
  description    = "My Compartment"
}**# data source for compartment**
data oci_identity_compartment mycompartment {
  id = oci_identity_compartment.mycompartment.id
}**# vcn in new compartment**
resource oci_core_vcn myvcn {
  compartment_id = **data**.oci_identity_compartment.mycompartment
  display_name   = "My VCN"
  cidr_block     = "10.0.0.0/16"
}
```

当我们进行配置时，我们看到隔离专区数据源等待隔离专区在目标区域中可用，然后再继续进行 VCN 配置。

```
oci_identity_compartment.mycompartment: Creating...
oci_identity_compartment.mycompartment: Still creating... [10s elapsed]
oci_identity_compartment.mycompartment: Creation complete after 10s [id=ocid1.compartment.oc1..aaaaaaaap7xfr4byri4phkykeazgyoexxdii2xtxx2o6ric4ok267lcm66aq]
**data.oci_identity_compartment.mycompartment: Refreshing state...**
**oci_core_vcn.myvcn: Creating...**
oci_core_vcn.myvcn: Creation complete after 1s [id=ocid1.vcn.oc1.ca-toronto-1.aaaaaaaav4fospzcnk3nqfcdb7ehd4nidk7ulfjhuroucy5dqwtj5i47ug3q]

**Apply complete! Resources: 2 added, 0 changed, 0 destroyed.**
```