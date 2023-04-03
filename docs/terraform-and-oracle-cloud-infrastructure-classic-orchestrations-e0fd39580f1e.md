# Terraform 和 Oracle 云基础架构经典业务流程

> 原文：<https://medium.com/oracledevs/terraform-and-oracle-cloud-infrastructure-classic-orchestrations-e0fd39580f1e?source=collection_archive---------0----------------------->

面向 Oracle 云基础架构 Classic 和 Oracle 云客户的`[opc](https://www.terraform.io/docs/providers/opc/index.html)` Terraform provider 的`1.0.1`版本引入了对使用 Compute Classic 本机编排功能的配置实例的初始支持。

在本文中，我们将了解如何使用编排作为 Terraform 配置的一部分来提供实例，并探讨“编排的”实例和 Terraform 本地创建的实例之间的一些差异。

# Oracle 计算云经典业务流程

[编排](https://docs.oracle.com/en/cloud/iaas/compute-iaas-cloud/stcsg/orchestrations-v2.html)是 Oracle Compute Classic 本机资源调配和编排功能，在某种程度上，编排可被视为 Terraform 的替代方案。

> 流程编排定义了计算经典中计算、网络和存储资源集合的属性和相互依赖关系。您可以使用流程编排来自动化整个虚拟计算拓扑的配置和生命周期操作。

大多数用户对 Oracle 计算服务的第一次体验是使用控制台 UI 启动实例。使用 UI 时，总是使用编排来创建实例。编排计划也可以直接以 JSON 格式定义，使用 API、CLI 提供，或者上传到 UI 中。

用于创建实例的本机业务流程模板示例如下所示:

这个例子假设引用的安全列表、ip 预留、ssh 密钥等。但是它们也可以被定义为同一个编排计划中的额外的**对象**，就像一个地形配置可以定义多个资源一样。在 Oracle Compute Classic 中创建流程编排计划时，内部流程编排框架将管理对象(资源)的创建和生命周期。

# 地形与编排

Terraform 也管理基础设施协调和供应，以上面的实例为例，可以在 Terraform HCL 中定义相同的配置

再次引用安全列表、ip 预留、ssh 密钥等。假设已经被创建，但是它们也可以被定义为相同地形配置中的附加资源。

当使用 Terraform 应用此配置时，`opc`提供商使用 Oracle Compute Classic [启动计划](https://docs.oracle.com/en/cloud/iaas/compute-iaas-cloud/stcsg/creating-instances-using-launch-plans.html)创建`opc_compute_instance`资源。启动计划直接在计算中创建实例，并使用流程编排。

从编排计划创建的实例与使用启动计划创建的实例之间的主要区别之一是，编排对象是主动管理的，因此在实例出现故障时，Compute Classic 编排框架会自动检测出错的实例状态，并尝试重新创建实例，从而支持中断恢复，而无需引入外部监控工具。

# 使用 Terraform 创建“编排”实例

为了支持创建主动管理的实例，`opc` Terraform provider 版本`1.0.1`引入了一个`**opc_compute_orchestrated_instance**`资源，专门用于在编排计划中创建实例。

Terraform 有效地将实际的实例创建和管理委托给了本机编排框架。需要注意以下几点:

*   由`opc_compute_orchestrated_instance`资源创建的编排计划仅包含实例对象定义，所有其他相关资源(如实例块存储卷)继续由 Terraform 直接管理，因此不是编排定义的一部分。
*   将编排`desired_state`设置为`inactive`(如果没有设置实例`persistent`选项，则设置为`suspend`)将导致实例**被销毁。**当编排设置回`active`时，创建一个具有新实例 UUID 的新实例
*   如果由于实例故障，某个实例被业务流程自动重新创建，则重新创建的实例的 UUID 将不同于原始实例的实例 ID。Terraform 状态文件将与实例细节不同步，直到下一个 Terraform `refresh`、`plan`或`apply`更新状态。

这样一来，让我们看看定义一个`opc_compute_orchestrated_instance`资源的简单例子。

```
resource "opc_compute_orchestrated_instance" "my-instance" {
  name = "my-instance-orchestration"
  description = "my-instance-orchestration"
  desired_state = "active"

  instance {
    persistent = true
    name       = "my-orchestrated-instance"
    shape      = "oc3"
    image_list = "/oracle/public/OL_7.2_UEKR4_x86_64"
  }
}
```

`**name**`、`**description**`和`**desired_state**`属性适用于 Terraform 创建的编排资源。

资源定义中的`**instance**`块定义了编排定义中的实例编排对象模板。`instance`块与常规的`opc_compute_instance`资源具有相同的一般属性和结构，增加了`persistent`选项来设置如果编排被挂起，实例对象是否应该持续。

与常规`opc_compute_instance`一样，任何对持久启动存储、网络、安全等的依赖。可在 Terraform 配置中声明为单独的资源，并使用标准 Terraform 插值语法进行引用。这些额外的资源继续由 Terraform 直接管理，不属于编排的一部分。

## 当心意想不到的后果

最后一点需要注意。`opc_compute_orchestrated_instance`资源支持在单个编排中声明多个`instance`块。**但是，作为一个警告点**，请注意，如果在资源内进行了任何配置更改，从而强制重新创建编排对象，则编排声明内的所有实例都将被销毁并重新创建。