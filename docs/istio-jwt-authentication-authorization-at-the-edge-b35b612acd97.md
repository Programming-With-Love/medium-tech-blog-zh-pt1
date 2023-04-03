# 边缘的 Istio JWT 认证和授权

> 原文：<https://medium.com/globant/istio-jwt-authentication-authorization-at-the-edge-b35b612acd97?source=collection_archive---------0----------------------->

![](img/aca1d7458e8e35e4c3e98b358338f497.png)

曾经想知道如何使用 [JWT](https://jwt.io) 令牌来认证来自 API 网关的&授权请求吗？但是发现它很混乱，您找到的信息也很分散，您想知道它们是如何组合在一起的？3
不要害怕！我也经历过这些场景，为此我建立了自己的[操场](https://github.com/JorgeReus/istio-jwt)，我将带你经历这个过程、技巧以及如何在你的用例中实现它。

本文将回顾以下场景:

*   **首先，什么是 JWT，为什么要关注它？**
*   **搭建操场**
*   **剖析 Istio 的 JWT 边缘认证&授权**
*   **如何为 istio 构建外部 authz 服务**

# 首先，什么是 JWT，为什么要关注它？

JWT(JSON Web Token 的缩写)是一种在双方之间共享声明的 Web 标准。许多系统都使用 jwt，你可能会去你最喜欢的网站，检查持久存储(本地存储、cookies、会话存储等)。)你会找到 JWT

它们看起来像这样

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJPbmxpbmUgSldUIEJ1aWxkZXIiLCJpYXQiOjE2NTM4NzU4MDUsImV4cCI6MTY4NTQxMTgwNSwiYXVkIjoid3d3LmV4YW1wbGUuY29tIiwic3ViIjoianJvY2tldEBleGFtcGxlLmNvbSIsIkdpdmVuTmFtZSI6IkpvaG5ueSIsIlN1cm5hbWUiOiJSb2NrZXQiLCJFbWFpbCI6Impyb2NrZXRAZXhhbXBsZS5jb20iLCJSb2xlIjpbIk1hbmFnZXIiLCJQcm9qZWN0IEFkbWluaXN0cmF0b3IiXX0.3KtBCvZAieEJvZou7-49vjcrmd4sU-RypSqlqBGm4v
```

它们由 3 个 base 64 编码部分组成，每个部分由一个点(.)
1。割台
2。有效载荷
3。签名

它们的美妙之处在于，签名是由报头中指定的算法生成的，因此我们可以确保令牌没有被篡改。你可以在[这里](https://jwt.io/introduction)找到更多信息

JWT 的介绍到此为止，让我们把手弄脏吧。

# 搭建操场

要求:

*   git(推荐 2.35.1)
*   golang(推荐 1.18)
*   docker(如果别名是 docker，建议使用 20.10.12，另一个容器管理器就足够了)
*   k3d(建议使用 k3s v1.22.7-k3s1 版本的 v5.4.1)
*   地形(~> 1.0.0)
*   kubectl(相应地匹配 clus[https://TL 7 x 52 xzircx 5 gp v3 bmkhkxvp 4 . app sync-API . us-east-1 . amazonaws . com/graph QL](https://tl7x52xzircx5gpv3bmkhkxvp4.appsync-api.us-east-1.amazonaws.com/graphql)ter，在本例中为 1.22)
*   [任务](https://taskfile.dev/installation/)(推荐 3.12.0 版)
*   jq(推荐 1.6)

## 克隆[回购](https://github.com/JorgeReus/istio-jwt)

运行`git clone https://github.com/JorgeReus/istio-jwt`

## 开始游乐场

你只需要运行`task setup`

等待几分钟，您将拥有一个完整的 k8s playground，其中应用了 istio 和所有必需的服务和配置。

## 测试 jwt

*   您可以使用`task get-valid-jwt`生成一个有效的 JWT
*   和一个无效的带有`task get-invalid-jwt`的 JWT
*   用`task get-istio-ip`获得 istio 网关 ip

你可以一起跑:

如您所见，使用有效的 JWT，您将获得一个带有 200 响应代码的 HTML 响应。
对于无效的 JWT，您将得到消息*您的角色没有所需的权限*，状态代码为 403。让我们来分析一下发生了什么

## 好吧，发生了什么？

首先，task 是一个任务运行器(奇怪的是)，这将允许我们通过简单地指定要运行的任务来运行命令，简单的事情是我们可以设置任务之间的依赖关系，因此通过简单的一个命令我们就可以设置开发环境。
运行`task setup`所执行的任务如下

*   create-cluster
    创建一个包含 3 个节点的 k3d 集群，并在端口 5050 中设置一个容器注册表
*   推送所有映像
    推送我们将在环境中使用的服务的映像
*   install-istio
    使用 terraform 通过舵图安装 istio
*   configure-istio
    配置 istio 网关并设置环境所需的所有自定义资源

## 剖析 Istio 的 JWT 边缘认证和授权

**Authn(认证)**

Istio 有请求认证的概念，它将 JWT 规则应用于来自集群内部工作负载的请求或来自集群外部的请求。您可以查看[参考文件](https://istio.io/latest/docs/reference/config/security/request_authentication/)了解更多信息。

现在让我们看看我们在 [repo](https://github.com/JorgeReus/istio-jwt) 中定义的请求认证清单，它位于 [terraform/ops/main.tf](https://github.com/JorgeReus/istio-jwt/blob/main/terraform/ops/main.tf#L93) 中。

首先，你可以看到我们在规范中有一个 jwtRules 数组，每个 jwtRules 包含一个 issuer 和一个 jwksUri。

发行者映射到 JWT 中称为 *iss* 的字段，它是创建 JWT 的“一方”, istio 将解码 JWT 并将 *iss* 字段与此字段进行比较。

jwksUri 是一个可解析的 URL，它包含一个公共的 JWT 密钥集，istio 用它来验证令牌是否由可信的私有 JWT 密钥集签名。

实际上，该规则规定任何被评估的 JWT 必须具有值为“my.jwt.issuer”的 *iss* 字段，并且应该由[http://auth-service.default.svc.cluster.local/jwk/public](http://auth-service.default.svc.cluster.local/jwk/public)中存在的密钥的私有部分的任何密钥来签名。
请记住，这将创建策略，但是要应用到网关，我们必须使用[授权策略](https://istio.io/latest/docs/reference/config/security/authorization-policy/)

你可以在这里找到[中负责评估规则的代码。](https://github.com/istio/istio/blob/master/pilot/pkg/security/authn/v1beta1/policy_applier.go)

**Authz(授权)**

在 istio 中，您可以使用[授权策略](https://istio.io/latest/docs/reference/config/security/authorization-policy/)配置对网格、名称空间和工作负载的访问控制。

在这个 CRD 中，我们将应用上一步中的请求认证，我们将进一步解码 jwt 并评估其他字段。

首先，我们将看看如何编写一个应用程序来进行自定义授权。
为什么？
因为 istio 的 JWT 授权策略是静态的，所以用普通策略不可能从数据库中提取数据。

Istio 支持使用外部服务来应用我们的定制授权逻辑的方法，这在我们希望以动态方式管理访问控制时非常有用。

为了配置外部授权，我们需要提供一个定制的网格配置

istio 支持两种协议来与您的定制 authz 服务通信:http & grpc，对于这两种协议，您都需要提供一个端口、服务的主机名，并且可以选择在 http 中提供您希望从请求中传递的头。

**如何为 istio 构建外部 authz 服务**

由于 istio 是开源的，我们可以使用相同的库来开发服务，我们将看到一些显示重要部分的片段。

对于 grpc，istio 公开了 API v2 和 v3 的两个版本，如果您有不同版本的 istio 并希望在其中任何一个版本中使用，您可以实现这两个版本。

在这里，我们可以看到如何从请求中获取标题并处理它们。在本例中，我们得到了`Authorization: Bearer <jwt>`头，解码 jwt 并应用一个定制的验证器函数。

当使用普通 http 时，更简单，我们只需要实现一个函数并应用出来

在服务部署并准备好之后，您可以通过在授权策略中设置提供者来使用

实际上，通过这种配置，策略将请求转发给自定义授权服务，以决定是允许还是拒绝请求。

**授权策略评估规则**

由于我们对同一路径应用了多个策略，istio 应用了一些内部规则来确定是允许还是拒绝请求，这些规则如下:

1.  如果有任何自定义策略与请求匹配，则评估请求，如果评估结果为拒绝，则拒绝请求。
2.  如果有任何拒绝策略与该请求匹配，则拒绝该请求。
3.  如果工作负荷没有允许策略，则允许请求。
4.  如果任何允许策略与该请求匹配，则允许该请求。
5.  拒绝请求。

在这种特定情况下，将首先调用授权服务，然后请求身份验证策略。你可以在这里找到更多信息

## 结论

在本文中，我们深入研究了 istio 如何使用 JWTs 处理认证和授权，JWTs 是一个广泛使用的标准，学习 JWT 非常重要，istio 为我们提供了一个强大而简单的方法，将我们自己的规则应用于几种类型的工作负载的认证和授权。

## 额外内容！

想知道如何在 AWS 中完全无服务器授权吗？
看看这个[回购](https://github.com/JorgeReus/aws-api-gateway-jwt-lab)