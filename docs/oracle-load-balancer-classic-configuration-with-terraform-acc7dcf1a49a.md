# 使用 Terraform 的 Oracle 负载平衡器经典配置

> 原文：<https://medium.com/oracledevs/oracle-load-balancer-classic-configuration-with-terraform-acc7dcf1a49a?source=collection_archive---------0----------------------->

本文介绍了如何使用负载平衡器资源，通过 Terraform 提供和配置一个**Oracle Cloud infra structure Load Balancer Classic**实例

将负载平衡器经典资源与`[opc](https://www.terraform.io/docs/providers/opc/)` Terraform 提供程序一起使用时，必须在提供程序配置中设置`lbaas_endpoint`属性。

```
provider "opc" {
  version         = "~> 1.2"
  user            = "${var.user}"
  password        = "${var.password}"
  identity_domain = "${var.compute_service_id}"
  endpoint        = "${var.compute_endpoint}"
  **lbaas_endpoint  = "**[**https://lbaas-1111111.balancer.oraclecloud.com**](https://lbaas-148cba7050494081b95151c522617ba9.balancer.oraclecloud.com)**"**
}
```

首先，我们创建主**负载平衡器**实例资源。服务器池、侦听器和策略资源将被创建为与此实例关联的子资源。

```
resource "**opc_lbaas_load_balancer**" "lb1" {
  name        = "examplelb1"
  region      = "uscom-central-1"
  description = "My Example Load Balancer"
  scheme      = "INTERNET_FACING" permitted_methods = ["GET", "HEAD", "POST"]
  ip_network        = "/Compute-${var.domain}/${var.user}/ipnet1"
}
```

为了定义负载平衡器将流量导向的服务器集，我们创建了一个**服务器池**，有时也称为源服务器池。每台服务器都由目标 IP 地址或主机名和端口的组合来定义。为了这个例子的简洁，我们假设在一个现有的 IP 网络上已经有了几个实例，并且在端口`8080`上运行了一个 web 服务。

```
resource "**opc_lbaas_server_pool**" "serverpool1" {
  load_balancer = "${opc_lbaas_load_balancer.lb1.id}"
  name          = "serverpool1" servers  = ["192.168.1.2:8080", "192.168.1.3:8080"]
  vnic_set = "/Compute-${var.domain}/${var.user}/vnicset1"
}
```

**监听器**资源定义了负载平衡器将把什么样的传入流量定向到特定的服务器池。可以为单个负载平衡器实例定义多个服务器池和侦听器。现在，我们假设所有的流量都是 HTTP，包括负载平衡器和负载平衡器与服务器池之间的流量。我们稍后将研究如何保护与 HTTPS 的通信。在本例中，负载平衡器管理站点`http://mywebapp.example.com`的入站请求，并将它们定向到我们上面定义的服务器池。

```
resource "**opc_lbaas_listener**" "listener1" {
  load_balancer = "${opc_lbaas_load_balancer.lb1.id}"
  name          = "http-listener" balancer_protocol = "HTTP"
  port              = 80
  virtual_hosts     = ["mywebapp.example.com"] server_protocol = "HTTP"
  server_pool     = "${opc_lbaas_server_pool.serverpool1.uri}" policies = [
    "${opc_lbaas_policy.load_balancing_mechanism_policy.uri}",
  ]
}
```

策略用于定义侦听器如何处理传入流量。在监听器定义中，我们引用了一个**负载平衡机制策略**来设置负载平衡器如何在服务器池中的可用服务器之间分配流量。还可以定义附加策略类型来控制会话关联性

```
resource "**opc_lbaas_policy**" "load_balancing_mechanism_policy" {
  load_balancer = "${opc_lbaas_load_balancer.lb1.id}"
  name          = "roundrobin" load_balancing_mechanism_policy {
    load_balancing_mechanism = "round_robin"
  }
}
```

至此，我们的第一个基本负载平衡器配置就完成了。差不多吧。最后一步是配置 DNS CNAME 记录，将源域名(例如`mywebapp.example.com`)指向负载平衡器实例的规范主机名。具体步骤取决于您的 DNS 提供商。要获得`canonical_host_name`，添加以下输出。

```
output "canonical_host_name" {
  value = "${opc_lbaas_load_balancer.lb1.canonical_host_name}"
}
```

***有用提示*** *:如果您只是创建用于测试的负载平衡器，并且您没有访问 DNS 名称的权限，那么您可以重定向，一个解决方法是将监听器配置中的* `*virtual host*` *设置为负载平衡器的规范主机名，然后您可以将规范主机名直接用于入站服务 URL，例如*

```
resource "**opc_lbaas_listener**" "listener1" { 
  ...
 **virtual_hosts     = [
    "${opc_lbaas_load_balancer.lb1.canonical_host_name}"
  ]**  ...
}
```

# 为 HTTPS 配置负载平衡器

为 HTTPS 流量配置负载平衡器有两个独立的方面，第一个方面是启用对负载平衡器的入站 HTTPS 请求，通常称为 SSL 或 TLS 终止或卸载。第二种是对负载平衡器和原始服务器池中的服务器之间的流量使用 HTTPS。

## HTTPS SSL/TLS 终止

要配置负载平衡器侦听器以接受客户端和负载平衡器之间加密流量的入站 HTTPS 请求，请创建一个**服务器证书**，为 CA 证书链提供 PEM 编码的证书和私钥，以及 PEM 编码的级联证书集。

```
resource "**opc_lbaas_certificate**" "cert1" {
  name = "server-cert"
  type = "**SERVER**"
  private_key = "${var.private_key_pem}"
  certificate_body = "${var.cert_pem}"
  certificate_chain = "${var.ca_cert_pem}"
}
```

现在更新现有的，或者为 HTTPS 创建一个新的监听器

```
resource "**opc_lbaas_listener**" "listener2" {
  load_balancer = "${opc_lbaas_load_balancer.lb1.id}"
  name          = "https-listener" **balancer_protocol = "HTTPS"
  port              = 443**  **certificates      = ["${opc_lbaas_certificate.cert1.uri}"]**
  virtual_hosts     = ["mywebapp.example.com"] server_protocol = "HTTP"
  server_pool     = "${opc_lbaas_server_pool.serverpool1.uri}" policies = [
    "${opc_lbaas_policy.load_balancing_mechanism_policy.uri}",
  ]
}
```

请注意，服务器池协议仍然是 HTTP，在此配置中，流量仅在客户端和负载平衡器之间加密。

## HTTP 到 HTTPS 重定向

许多 web 应用程序需要的一个常见模式是确保任何通过 HTTP 的初始传入请求都被重定向到 HTTPS，以实现安全的站点通信。要做到这一点，我们可以用一个新的重定向策略来更新上面创建的原始 HTTP 侦听器

```
resource "**opc_lbaas_policy**" "**redirect_policy**" {
  load_balancer = "${opc_lbaas_load_balancer.lb1.id}"
  name          = "example_redirect_policy" **redirect_policy {
    redirect_uri = "**[**https://${var.dns_name**](https://${var.dns_name)**}"
    response_code = 301
  }** }resource "**opc_lbaas_listener**" "**listener1**" {
  load_balancer = "${opc_lbaas_load_balancer.lb1.id}"
  name          = "http-listener" balancer_protocol = "HTTP"
  port              = 80
  virtual_hosts     = ["mywebapp.example.com"] server_protocol = "HTTP"
  server_pool     = "${opc_lbaas_server_pool.serverpool1.uri}" **policies = [
    "${opc_lbaas_policy.redirect_policy.uri}",
  ]** }
```

## 负载平衡器和服务器池之间的 HTTPS

如果通过公共互联网访问服务器池，则应使用负载平衡器和服务器池之间的 HTTPS，当通过专用 IP 网络访问 Oracle 云基础架构中的服务器时，该技术也可用于提高安全性。

此配置假设后端服务器已经配置为通过 HTTPS 提供内容服务。

要配置负载平衡器以安全地与后端服务器通信，请创建一个**可信证书**，为后端服务器提供 PEM 编码的证书和 CA 授权证书链。

```
resource "**opc_lbaas_certificate**" "cert2" {
  name = "trusted-cert"
  type = "**TRUSTED**"  certificate_body = "${var.cert_pem}"
  certificate_chain = "${var.ca_cert_pem}"
}
```

接下来创建一个引用可信证书的**可信证书策略**

```
resource "**opc_lbaas_policy**" "trusted_certificate_policy" {
  load_balancer = "${opc_lbaas_load_balancer.lb1.id}"
  name          = "example_trusted_certificate_policy" trusted_certificate_policy {
    trusted_certificate = "${opc_lbaas_certificate.cert2.uri}"
  }
}
```

最后，将侦听器服务器池配置更新为 HTTPS，添加可信证书策略

```
resource "**opc_lbaas_listener**" "listener2" {
  load_balancer = "${opc_lbaas_load_balancer.lb1.id}"
  name          = "https-listener" balancer_protocol = "HTTPS"
  port              = 443
  certificates      = ["${opc_lbaas_certificate.cert1.uri}"]  virtual_hosts     = ["mywebapp.example.com"] **server_protocol = "HTTPS"**  server_pool     = "${opc_lbaas_server_pool.serverpool1.uri}" policies = [
    "${opc_lbaas_policy.load_balancing_mechanism_policy.uri}",
 **"${opc_lbaas_policy.trusted_certificate_policy.uri}**  ]
}
```

# 更多信息

*   [负载平衡器 Classic 的地形配置示例](https://github.com/oracle/terraform-examples/tree/master/examples/opc/loadbalancer-classic)
*   [Oracle 云基础设施负载平衡经典入门](https://docs.oracle.com/en/cloud/iaas/load-balancer-cloud/index.html)
*   [甲骨文云基础设施经典平台提供商](https://www.terraform.io/docs/providers/opc/#)