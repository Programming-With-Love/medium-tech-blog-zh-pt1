# 在 Terraform 中使用 OCI 标签默认值

> 原文：<https://medium.com/oracledevs/working-with-oci-tag-defaults-in-terraform-d07608564eaf?source=collection_archive---------0----------------------->

![](img/30dcac9adcfda375ffc1a5895bbb9b03.png)

对资源进行标记对于您的 OCI 部署来说是一件非常方便的事情。[标签默认值](https://docs.oracle.com/en-us/iaas/Content/Tagging/Tasks/managingtagdefaults.htm)提供了一种自动化资源标签的方法，但是在使用 Terraform 时要小心。

一般来说，标记有助于从成本管理到定位部署的相关资源的所有事情。有许多方法可以手动和编程地将标签应用到 OCI 中的资源，但是使用自动[标签默认值](https://docs.oracle.com/en-us/iaas/Content/Tagging/Tasks/managingtagdefaults.htm)和区间是应用符合层次结构的标签策略的好方法，而不需要实际显式地添加标签；在给定隔离专区中创建的任何资源都将自动应用该隔离专区和任何父隔离专区的任何标记默认值( ***注*** :对于没有定义默认值的标记默认值，您仍然必须显式提供标记和值)。您可能已经在使用这个特性而没有意识到它——Oracle 提供了`[Oracle-Tags](https://docs.oracle.com/en-us/iaas/Content/Tagging/Concepts/understandingautomaticdefaulttags.htm)`标记名称空间，该名称空间提供了`CreatedBy`和`CreatedOn`标记，这些标记被配置为根隔离专区中的默认标记，并且可能应用于您已经创建的所有资源。

当手动创建资源时，利用标签默认值特别有效，并且可以很容易地适应作为代码场景的基础设施；即仅提供非默认的标签和标签值。然而，如果您的 Terraform 项目的寿命超过一次应用，那么 Terraform 就会出现问题。问题是 Terraform 维护一个状态文件，其中包含它创建的资源的详细信息，这包括资源参数，包括标记。对于最初的 Terraform 资源创建，一切都很好——创建了资源并应用了标签默认值。Terraform 知道它创建了哪些资源，以及创建这些资源时的参数是什么，但是当 Terraform `plan`随后运行时，Terraform 将默认标签识别为它没有提供的参数，因此很可能想要删除它们。在这种情况下，如果运行`apply`，标签将被删除。这不是一个很好的结果，如果你是默认的成本跟踪标签！

# 用代码修复它

## 资源特定方法

一个显而易见的解决方案是，在使用 Terraform 时不要依赖标签默认值，而是显式定义所有必需的标签。

以编程方式设置所有必需的标签更容易被接受，并且可以被编入编码和测试周期中，以确保它们被创建。问题是，这对于通常使用[标签变量](https://docs.oracle.com/en-us/iaas/Content/Tagging/Tasks/usingtagvariables.htm#Using_Tag_Variables)来设置的`Oracle-Tags`标签来说效果不好；该变量不等于最终应用的值，因此 Terraform 对后续运行不满意，将使用标签变量重新应用标签，这肯定会改变`CreatedOn`并可能改变`CreatedBy`。更好的方法是让 OCI 自动设置`Oracle-Tags`，然后在地形中忽略它们。下面的示例代码创建了一个虚拟云网络，但也表明 Terraform 不应该担心`CreatedBy`和`CreatedOn`标签。有了这个位，任何后续的 Terraform 运行都不会改变这些标签。这可以根据需要扩展到包括其他自动默认的标签:

```
resource "oci_core_vcn" "vcn" {
  display_name   = var.vcn_name
  compartment_id = var.compartment_id
  cidr_blocks    = var.vcn_cidr_blocks
  dns_label      = var.dns_label
  is_ipv6enabled = false
  defined_tags   = var.defined_tags

  lifecycle {
    ignore_changes = [
      defined_tags["Oracle-Tags.CreatedBy"],
      defined_tags["Oracle-Tags.CreatedOn"],
      defined_tags["My-Tags.AwesomeTag"]
    ]
  }
}
```

注意，这种方法并不意味着任何可能应用了标签默认值的资源都需要有一个`lifecycle`块来屏蔽所应用的标签默认值。

## 提供商方法

另一种可能更好的方法是利用 OCI 平台提供商的一个特性，即`ignore_defined_tags`参数。这将适用于由提供者实例管理的任何资源。您的提供者配置可能如下所示:

```
provider "oci" {
  tenancy_ocid        = var.tenancy_ocid
  region              = var.region
  user_ocid           = var.user_ocid
  fingerprint         = var.fingerprint
  private_key         = var.private_key

  ignore_defined_tags = [
    "Oracle-Tags.CreatedBy",
    "Oracle-Tags.CreatedOn",
    "My-Tags.AwesomeTag"
  ]
}
```

这些方法中的任何一种，甚至两者的组合，都可以扩展到您的环境中定义的其他标签默认值。

显然，标签有巨大的潜力，所以在使用 Terraform 这样的工具时，了解一些潜在的陷阱是很重要的。想了解更多？请访问我们的开发人员 Slack[与使用相同工具的人聊天！](https://bit.ly/odevrel_slack)