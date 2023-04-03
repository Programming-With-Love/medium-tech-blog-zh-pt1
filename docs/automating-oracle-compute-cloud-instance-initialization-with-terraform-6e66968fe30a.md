# 借助 Oracle 计算云上的 Terraform 实现实例初始化自动化

> 原文：<https://medium.com/oracledevs/automating-oracle-compute-cloud-instance-initialization-with-terraform-6e66968fe30a?source=collection_archive---------0----------------------->

本文概述了可用于自动化实例初始化的不同方法，作为使用 Terraform 的初始 Oracle 云基础架构 **Compute Classic** 实例资源调配的一部分。

具体来说，我们将在初始实例资源创建的基础上介绍两个不同的实例初始化和供应选项

*   在实例创建期间使用操作系统特定的 **opc-init** 或**云-init** 配置
*   使用 **Terraform 供应器**作为资源供应的一部分

选择使用哪种方法将取决于特定的资源调配要求。每种方法都有其优点和缺点，两种方法之间的主要区别在于，opc-init/cloud-init 方法在创建和启动期间通过云基础设施控制平面将初始化配置直接传递给实例，使实例能够自主处理初始化，而 Terraform Provisioner 必须连接到正在运行的实例，以在初始实例创建后执行额外的配置任务。这两种方法可以结合起来作为整个供应过程的一部分。

# 使用 opc-init 进行实例初始化

[opc-init](http://docs.oracle.com/en/cloud/iaas/compute-iaas-cloud/stcsg/automating-instance-initialization-using-opc-init.html) 支持默认包含在 Oracle 为 Oracle 计算云提供的 Oracle Linux 和 Microsoft Windows 机器映像中，并且可以[整合到自定义映像中](http://docs.oracle.com/en/cloud/iaas/compute-iaas-cloud/stcsg/automating-instance-initialization-using-opc-init.html#GUID-A0A107D6-3B38-47F4-8FC8-96D50D99379B)。opc-init 脚本在启动过程中向元数据服务查询用户数据，然后用于执行所需的预引导任务，包括:

*   **Linux** —设置 http 和 https 代理，执行预引导脚本，安装 yum 包，执行 Chef 供应任务
*   **Windows** —配置管理员帐户，创建用户，执行预引导脚本，启用远程管理，启用远程桌面

有关受支持的 opc-init 实例初始化功能的详细信息，请参见文档[使用 opc-init 自动初始化实例](http://docs.oracle.com/en/cloud/iaas/compute-iaas-cloud/stcsg/automating-instance-initialization-using-opc-init.html#GUID-C63680F1-1D97-4984-AB02-285B17278CC5)

# 示例:带有 opc-init 预引导的 Oracle Linux 实例

`opc_compute_instance` `[instance_attributes](https://www.terraform.io/docs/providers/opc/r/opc_compute_instance.html#attributes)`是一个 JSON 有效负载，直接传递给 Terraform 用来创建实例的 OPC 云启动计划。 [Heredoc](https://en.wikipedia.org/wiki/Here_document) 符号用于将 JSON 内容嵌入 Terraform HCL 配置中。

这个例子演示了一个简单的`pre-bootstrap`供应脚本，它附加到实例`motd`(每日消息)中。

# 示例:带有 opc-init 预引导的 Windows 服务器实例

此示例使用映像选项配置`userdata`,以设置初始管理员密码、启用远程桌面并执行从远程 URL 动态获取的预引导脚本。

在这个例子中，我们没有直接在实例资源中创建 JSON 有效负载字符串，而是使用一个`[template_file](https://www.terraform.io/docs/providers/template/d/file.html)`数据源来演示如何传递变量来动态构造定制的有效负载。

# 使用 cloud-init 进行实例初始化

Oracle Cloud Marketplace 中的几个第三方提供的基本操作系统和应用程序映像提供了 cloud-init 支持。类似于上面的 opc-init 示例，在`instance_attributes`中定义了额外的初始化配置，当创建实例资源时，这些配置被传递给启动计划。cloud-init `cloud-config`数据是 YAML 格式的，在`userdata`属性中传递。

有关支持的`cloud-config`选项的完整详细信息，请参考映像供应商文档和通用 cloud-init 规范。

**注意**由于`instance_attributes`期望一个包含 JSON 的字符串，所以确保 cloud-init `cloud-config` YAML 被正确编码/转义以作为 JSON 字符串在`userdata`字段中传递是很重要的。

# 示例:带有 cloud-init 的 Ubuntu 实例

我们将再次使用一个简单的示例来更新系统`motd`。

在本例中，`userdata`和`cloud-config`被分成两个独立的模板，以提高可读性。

# 使用 Terraform 供应器初始化实例

Terraform 支持几个[供应器](https://www.terraform.io/docs/provisioners/index.html)，这些供应器能够在初始实例资源创建后协调其他供应步骤。

# 示例:带有`remote-exec`置备程序的 Oracle Linux 实例

以上面的同一个例子为例，这次我们使用 Terraform Provisioner 来执行一个远程命令，更新当天的`motd`消息。

注意，`connection`块定义了 Terraform 如何连接到新创建的实例。为了简单起见，上面的例子使用了一个公开分配的 IP 地址预留，因此主机是可访问的，并且假设已经定义了`For-SSH-access`安全列表来允许 SSH 访问实例。

对于创建的没有公共 IP 地址的实例，Terraform 也支持[使用 SSH 通过 Bastion 主机连接](https://www.terraform.io/docs/provisioners/connection.html#connecting-through-a-bastion-host-with-ssh)。如果 Terraform 在同一个计算域中运行，或者可以通过 VPN 访问该域的共享专用网络，则可以使用实例`self.private_ip`，或者如果实例是通过专用 IP 网络上的接口启动的，则可以使用分配给`vnic`的专用 IP。

# 示例:带有`remote-exec`置备程序的 Windows 服务器实例

Terraform 支持`winrm`连接来提供基于 Windows 的实例。请注意，对于 Oracle 提供的用于 Oracle 计算云的 Microsoft Windows 映像，我们需要结合使用 **opc-init** 配置，以便设置初始管理员密码和配置`winrm`

与上面的“示例:带有 opc-init 预引导的 Windows Server 实例”相似，本示例演示了如何运行引导脚本。该示例没有让实例通过远程 URL 下载引导脚本，而是将脚本从本地环境上传到新实例，然后执行它。

必须定义`For-WinRM-access`安全列表以启用远程管理。用于 WS-Management 和 PowerShell remoting 的默认端口分别是用于 HTTP 和 HTTPS 连接的`5985`和`5986`

继续阅读第二部分

 [## 在 Oracle 云基础设施上使用 Terraform 自动化实例初始化:第 2 部分

### 作为上一篇关于在 Oracle 计算云上使用 Terraform 自动化实例初始化的文章的后续…

medium.com](/oracledevs/automating-instance-initialization-with-terraform-on-oracle-cloud-infrastructure-part-2-e1aa1a8710d)