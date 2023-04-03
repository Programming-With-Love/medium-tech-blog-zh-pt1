# 多租户环境中的秘密管理

> 原文：<https://medium.com/pinterest-engineering/secret-management-in-multi-tenant-environments-debc9236a744?source=collection_archive---------2----------------------->

Jeremy Krach |软件工程师，基础设施安全

Pinterest 的开源秘密管理服务 Knox ，现在支持 SPIFFE x509 身份文档作为一种认证方法。这一更新使 Knox 能够在 Kubernetes 这样的多租户环境中管理机密。

Knox 在 Pinterest 的基础设施中被广泛用于向生产系统提供秘密。在一台主机对应一个“服务”身份的环境中，Knox ACLs 是完全表达性的。然而，今天的微服务基础设施利用 Docker 和 Kubernetes 等工具将多个容器化的服务部署到单个多租户主机上。在这个世界上，Knox 需要重新评估它的服务概念。

幸运的是，SPIFFE 的人们已经做了大量的工作来定义一个模式，为服务提供一个身份([详细的规范在这里](https://spiffe.io))。在这个上下文中，主要思想是可以将 URI 主题替换名称添加到典型的 x509 证书中，以提供具有唯一身份的服务。一般格式如下所示:sp iffe://trust-domain/arbitrary-path

Knox 目前通过检查标准 x509 客户端证书的主机名来验证机器。通过此更新，Knox 可以通过检查证书中的 URI San 来验证任何具有 SPIFFE 身份的服务。以下是包含 SPIFFE 身份的证书示例:

如果一个客户端使用这个证书向 Knox 认证，Knox 会将其身份存储为 sp iffe://Pinterest . com/service _ a。

我们还添加了通过 Knox 客户端在 ACL 中指定这些服务身份的功能。可以使用-S 参数并提供 SPIFFE 标识来指定新的条目类型，如下例所示。

通过在您的环境中指定 KNOX_SERVICE_AUTH 而不是 KNOX_MACHINE_AUTH，KNOX 将知道使用 SPIFFE 标识符而不是主机名:

使用上面具有 spiffe://example.com/service_a 标识的证书，该请求将被批准。相反，如果我们运行下面的示例，我们将无法使用示例证书获得密钥。

然而，service_b(可能在同一个系统上)可以使用它自己的具有正确身份的证书来获得这个密钥。

为了在生产中使用它，您需要一种机制来为多租户系统上运行的每个服务提供证书。 [SPIRE](https://github.com/spiffe/spire) ，SPIFFE 的参考实现，是一个很好的起点。

在 Pinterest，我们已经有了一个内部 PKI，它使用 AWS 向主机提供证书来证明身份。通过用 Kubernetes 证明层扩展这个 PKI，单个 pod 现在能够请求包含嵌入式 SPIFFE 身份的证书。

在 GitHub 上看看这些变化和由诺克斯主演的[即将出现的新变化。](https://github.com/pinterest/knox)

![](img/c2e708437034ef3a06c845ca00e483ca.png)