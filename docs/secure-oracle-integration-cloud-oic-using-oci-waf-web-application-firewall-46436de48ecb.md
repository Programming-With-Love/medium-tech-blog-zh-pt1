# 使用 OCI WAF (Web 应用防火墙)保护 Oracle 集成云(OIC)

> 原文：<https://medium.com/oracledevs/secure-oracle-integration-cloud-oic-using-oci-waf-web-application-firewall-46436de48ecb?source=collection_archive---------0----------------------->

![](img/61a9fd35b244be5fc4c1696cca31aa32.png)

对于一些客户来说，保护面向公众的集成平台(如 OIC)有点困难。OIC 产品团队最近添加了一个 IP 白名单特性，限制从特定 IP 地址(或 VCN CIDR 块)对实例的访问。

尽管如此，这个特性对一些客户来说还不够，因为他们希望围绕一个 OIC 实例添加更多的网络安全规则(更多信息请参考 [OCI WAF 文档](https://docs.oracle.com/en-us/iaas/Content/WAF/Concepts/overview.htm))。

OCI WAF 支持两种类型的实施点，如负载平衡器或 web 应用程序域名(边缘实施)。在本文中，我将使用一个负载平衡器实施点。

**注意:**如果您还没有注册，那么[现在就可以注册甲骨文云自由层账户](https://signup.cloud.oracle.com/?language=en)。如果你对如何开始学习 OCI 感兴趣，注册是第一步，没有必要跟着这篇文章走。

# 体系结构

这是该解决方案的高级架构:

![](img/e339048803698d054e46cda55df3ced1.png)

这些是实施该体系结构的高级步骤:

*   为 OIC 实例配置虚拟 URL(例如 oic.aaa.bbb)。
*   在公共子网中配置公共负载平衡器，并使用 OIC 公共 IP 和端口 443 配置后端集。此外，使用 OIC 主机名(oic.aaa.bbb)配置端口 443 上的侦听器。
*   在 DNS 服务器中为负载平衡器的公共 IP 地址上的 OIC DNS 名称(oic.aaa.bbb)创建一个记录。
*   配置 WAF 策略，并将在前面步骤中配置的负载平衡器设置为防火墙实施点。
*   为 OIC 实例创建网络访问规则，以便只接受来自负载平衡器实例的流量。

# 实施步骤

**1。供应 OIC 实例**

在我们的第一步中，您应该在您选择的 OCI 地区提供一个 OIC 实例。只需导航到 OCI 控制台，选择“Developer Services”下的“Integration ”,并在您选择的隔离专区下创建一个新的集成实例。

![](img/403909046808f4520dab34b383f6d559.png)

注意:您也可以使用 terraform 来提供 OIC 实例。请参考[集成资源提供者](https://registry.terraform.io/providers/oracle/oci/latest/docs/resources/integration_integration_instance)文档。

**2。为 OIC 实例创建自定义 URL**

为您的实例选择自定义主机名，并在 DNS 服务器上注册它。然后为您的主机名创建 SSL 证书，并使用证书颁发机构(CA)进行签名(或者使用自签名证书进行测试)。

最后，您应该创建一个 OCI 保险库来存储使用以下格式的证书(作为 OCI 秘密)

```
{
  "key": "-----BEGIN PRIVATE KEY-----\n…..-----END PRIVATE KEY-----\n",
  "cert": "-----BEGIN CERTIFICATE-----\n….-----END CERTIFICATE-----\n",
  "intermediates": [
    "-----BEGIN CERTIFICATE-----\n….-----END CERTIFICATE-----\n",
    "-----BEGIN CERTIFICATE-----\n….-----END CERTIFICATE-----\n"
  ],
  "passphrase": "<private key password if encrypted key is provided>"
}
```

您可以关注 [OCI 文档](https://docs.oracle.com/en/cloud/paas/integration-cloud/oracle-integration-oci/configure-custom-endpoint-instance.html)了解更多信息。

![](img/efb53e85607031b933423ffa3c1e4ace.png)![](img/a5958a9a2c09229ff0537ca97cfc3873.png)![](img/880d8946d47e7cfa169ec89b0cef297a.png)

**3。创建 OCI 证书**

创建一个 OCI 证书，导入在上一步中生成的证书，并使用该证书创建一个 CA 包(我们将在提供负载平衡器时使用这两个证书)。

![](img/b433b064f24e332b58b76e4731649ef9.png)![](img/cc753153c14523ca7512860e80975285.png)

**4。提供网络资源**

要实施此解决方案，我们需要提供以下网络资源:

*   VCN
*   公共子网
*   互联网网关
*   NAT 网关
*   路由表并使用以下两条规则连接到公共子网:1-将 0.0.0.0/0 流量路由到互联网网关 2-将 OIC 实例的公共 IP 地址路由到 NAT 网关(如果不创建此规则，负载平衡器将无法通过互联网网关与 OIC 实例通信)。

![](img/6b6b1fc65fc8595a9f08bede3a29c076.png)

*   安全列表并连接到公共子网，以接受来自公共互联网(0.0.0.0/0)的端口 443 上的流量，以及 CIDR 块 0.0.0.0/0 上所有协议的出口规则

**5。提供 OCI 负载平衡器**

按照以下规格设置公共 OCI 负载平衡器:

*   端口 443 上的侦听器。
*   将 OCI 证书附在监听器上。
*   为 OIC 自定义 DNS 创建主机名，并将其附加到侦听器。
*   在端口 443 上为 OIC 公共 IP 地址创建后端集，并使用 OCI CA 捆绑包为此后端集启用 SSL。

![](img/36d545b942f3ad3db4baeb5509c8ff1c.png)![](img/db15d2ace81626112d334498afdda31d.png)

**6。供应晶圆政策**

调配 WAF 策略，并添加在步骤 5 中调配的负载平衡器作为 WAF 策略中的实施点。

![](img/1e86100c643fa2663c8d595ee8157353.png)

您可以创建自己的访问控制、保护控制等。WAF 政策的规则。

**7。更新 DNS**

在 DNS 服务器中创建一个记录，将 OIC 自定义 DNS 映射到公共负载平衡器 IP 地址(您应该删除之前为 OIC 自定义 DNS 创建的到 OIC 主机名的 CNAME)。

![](img/bbdbc37bac6193f92f2d03b102d88112.png)

**8。创建对 OIC 实例的网络访问**

在 OIC 实例中创建网络访问策略，以便只接受来自 NAT 网关公共 IP 地址的请求。使用此策略，用户不能再使用 OIC 默认 URL。

![](img/c8df234cda808c3ce7a4b38e892ab7c3.png)

您可以在 NAT 网关详细信息中找到 NAT 网关公共 IP 地址。

![](img/5ce054e29fbdb6d854a13112e852c63e.png)

# 结论

通过本文，您使用 WAF 策略保护 OIC 实例免受攻击者的攻击。

加入对话！

如果你对 Oracle 开发人员在他们的自然环境中发生的事情感到好奇，就来参加我们的公共休闲频道吧！我们不介意成为你的鱼缸🐠

# 参考

OIC 自定义 URL:[https://docs . Oracle . com/en/cloud/PAAs/integration-cloud/Oracle-integration-OCI/configure-Custom-endpoint-instance . html](https://docs.oracle.com/en/cloud/paas/integration-cloud/oracle-integration-oci/configure-custom-endpoint-instance.html)

OCI 证书服务:[https://docs . Oracle . com/en-us/iaas/Content/certificates/overview . htm](https://docs.oracle.com/en-us/iaas/Content/certificates/overview.htm)

OCI 负载均衡器:[https://docs . Oracle . com/en-us/iaas/Content/certificates/overview . htm](https://docs.oracle.com/en-us/iaas/Content/certificates/overview.htm)

OCI WAF:[https://docs . Oracle . com/en-us/iaas/Content/WAF/Concepts/overview . htm](https://docs.oracle.com/en-us/iaas/Content/WAF/Concepts/overview.htm)