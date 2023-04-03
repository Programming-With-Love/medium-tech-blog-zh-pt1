# 使用 Cert-Manager、NGINX Ingress 和 Let's Encrypt 保护您的 Kubernetes 服务

> 原文：<https://medium.com/oracledevs/secure-your-kubernetes-services-using-cert-manager-nginx-ingress-and-lets-encrypt-888c8b996260?source=collection_archive---------0----------------------->

![](img/8384489b26a37b6f6c55923d5db3fd57.png)

# **简介**

不可否认，我们生活在微服务和容器的时代。对于正确的用例，大多数组织正在从整体架构转向微服务架构。微服务通过将大型整体应用程序拆分为松散耦合的相互协调的服务，实现了复杂应用程序的快速开发。容器非常适合部署和运行这些微服务。然而，微服务或容器带来了一系列新的挑战。这些挑战在任何分布式架构中都是固有的——从跨服务边界的服务间通信和事务到测试、部署和监控。进入 [Kubernetes](https://kubernetes.io/) 。Kubernetes 是一个开源编排工具，可以处理部署在容器中的微服务的操作复杂性。它有助于自动化部署、扩展目标服务和容器的一般管理。

这一切都很棒，但是请考虑一下:您开发了一个基于微服务的应用程序，并将其部署在 Kubernetes 集群上。您还在一个面向公众的 IP 地址上公开了由您的微服务或 REST APIs 支持的单页面 web 应用程序。在上线之前，下一个合乎逻辑的步骤是启用 HTTPS，并将您的公共 IP 绑定到您的注册域名。感谢[让我们加密](https://letsencrypt.org/)，现在获得数字证书(免费)和为您的服务和网站启用 HTTPS (SSL/TLS)比以往任何时候都更容易。本文将向您介绍在您的公共 Kubernetes 服务上自动启用 TLS 所需的配置步骤。

# **先决条件**

要遵循本文中的步骤，您需要以下内容:

*   kubernetes 集群版本 1.8+在某个地方运行，比如在您选择的云提供商上运行。

> 无耻的推广:你可以在 [Oracle Cloud](https://cloud.oracle.com/en_US/containers) 上注册一个免费账户来运行一个 Kubernetes 引擎(OKE)。

*   安装并配置了 kube CTL CLI 来与您的 Kubernetes 集群对话。您可以通过运行以下命令快速检查您的当前上下文:

```
kubectl config current-context
```

*   一个集群角色绑定，用于将管理权限授予用于运行 kubectl 命令的用户主体。如果您没有适当的权限，请运行以下命令(替换 user_id 令牌):

```
kubectl create clusterrolebinding cluster-admin-binding \
    --clusterrole cluster-admin \
    --user <user_id>
```

*   在 Kubernetes 集群中运行的一个或多个服务，其规范类型设置为“ClusterIP”。作为参考，这里有一个示例 yaml 文件，用于创建名为“my-webapp”的服务:

```
apiVersion: v1
kind: Service
metadata:
  name: my-webapp
  labels:
    app: my-webapp
    tier: frontend
spec:
  type: ClusterIP
  ports:
  — port: 80
    targetPort: 8080
  selector:
    app: my-webapp
    tier: frontend
```

# **第一步:安装舵**

[Helm](https://helm.sh/) 是官方的 kubernetes 原生包经理。您可以使用 helm 来安装和升级 kubernetes 应用程序。Helm 使用了一个名为 charts 的概念——描述安装和运行应用程序所需的 kubernetes 资源的文件集合。如果您是 mac/brew 用户，请运行以下命令:

```
brew install kubernetes-helm
```

要在不同的操作系统上或通过其他方式安装 Helm，请遵循 Helm [文档](https://docs.helm.sh/using_helm/#installing-helm)中概述的说明。

# **第二步:安装舵杆**

Tiller 是 Helm 的服务器端组件，通常运行在 Kubernetes 集群中。出于本文的目的，我们将在启用了 [RBAC](https://kubernetes.io/docs/reference/access-authn-authz/rbac/) 的系统名称空间上安装 tiller。

首先，创建一个用于运行 Tiller 的服务帐户。使用以下命令:

```
kubectl create serviceaccount tiller --namespace kube-system
```

接下来，创建一个名为“tiller-clusterrolebinding.yaml”的 yaml 文件，并向其中添加以下文本:

```
kind: ClusterRoleBinding
apiVersion: rbac.authorization.k8s.io/v1beta1
metadata:
  name: tiller-clusterrolebinding
subjects:
  — kind: ServiceAccount
    name: tiller
    namespace: kube-system
roleRef:
  kind: ClusterRole
  name: cluster-admin
  apiGroup: ""
```

现在运行以下命令创建群集角色绑定，并授予“tiller”服务帐户群集范围的管理权限:

```
kubectl create -f tiller-clusterrolebinding.yaml
```

最后，通过运行以下命令安装 Tiller:

```
helm init --service-account tiller
```

或者，通过运行以下命令并检查其输出，验证 Tiller 是否已安装并处于服务状态:

```
kubectl get pods --namespace kube-system
```

# **步骤 3:安装证书管理器**

[Cert-Manager](https://cert-manager.readthedocs.io/en/latest/index.html) 是一个 kubernetes 本地证书管理器。Cert-Manager 提供的最重要的功能之一是它能够自动提供 TLS 证书。根据 Kubernetes 入口资源中的注释，证书管理器将代表您的服务与 Let's Encrypt 和 acquire a certificate 对话。

运行以下命令安装 Cert-Manager:

```
helm install \
  --name cert-manager \
  --namespace kube-system \
  --set ingressShim.defaultIssuerName=letsencrypt-staging \
  --set ingressShim.defaultIssuerKind=ClusterIssuer \
  stable/cert-manager \
  --version v0.3.0
```

现在让我们创建一个 ClusterIssuer 资源。ClusterIssuer 代表一个证书颁发机构，如 Let's Encrypt，可以从中获取签名证书。至少应有一个 ClusterIssuer 资源，证书管理器才能开始颁发证书。

首先创建一个名为“letsencrypt-staging.yaml”的 yaml 文件，并向其中添加以下文本:

```
apiVersion: certmanager.k8s.io/v1alpha1
kind: ClusterIssuer
metadata:
  name: letsencrypt-staging
spec:
  acme:
    # The ACME server URL
    server: [https://acme-staging-v02.api.letsencrypt.org/directory](https://acme-staging-v02.api.letsencrypt.org/directory)
    # Email address used for ACME registration
    email: <me@example.com>
    # Name of a secret used to store the ACME account private key
    privateKeySecretRef:
      name: letsencrypt-sec-staging
    # Enable HTTP01 validations
    http01: {}
```

用您用来注册域的电子邮件地址替换电子邮件字段的值。请注意，我们已经为这个集群颁发者配置了一个 http 质询提供程序。你可以在这里阅读更多关于其他挑战的信息[。](http://letsencrypt.readthedocs.io/en/latest/challenges.html)

现在，通过运行以下命令创建 ClusterIssuer 资源:

```
kubectl create -f letsencrypt-staging.yaml
```

> 让我们对加密生产服务施加严格的限制。我建议你使用他们的暂存服务来测试一下。准备就绪后，创建一个新的指向生产的 cluster issuer:【https://acme-v02.api.letsencrypt.org/directory】T2

# **第四步:安装 NGINX 入口**

在 Kubernetes 的世界中，Ingress 是一个管理集群内服务的外部访问的对象。入口资源提供负载平衡和 SSL 端接。 [NGINX 入口](https://kubernetes.github.io/ingress-nginx/)控制器基于入口资源。我们将配置这个控制器作为 HTTPS 负载平衡器，并将请求转发给 Kubernetes 集群中的特定服务。

运行以下命令，将 NGINX 入口控制器安装到启用 RBAC 的系统命名空间中:

```
helm install stable/nginx-ingress \
  --name uck-nginx \
  --set rbac.create=true \
  --namespace kube-system
```

# **步骤 5:创建入口**

让我们创建入口资源，并指定一个规则来将请求转发到服务“my-webapp”。我们需要注释资源定义，以便 Cert-Manager 可以自动从 Let's Encrypt 获取所需的 TLS 证书。

首先创建一个名为“my-ingress.yaml”的 yaml 文件，并向其中添加以下文本:

```
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: my-ingress
  annotations:
    kubernetes.io/ingress.class: nginx
    certmanager.k8s.io/cluster-issuer: letsencrypt-staging
    kubernetes.io/tls-acme: “true”
spec:
  rules:
  — host: app.example.com
    http:
      paths:
      — path: /
        backend:
          serviceName: my-webapp
          servicePort: 80
  tls:
  — secretName: tls-staging-cert
    hosts:
    — app.example.com
```

现在，作为压轴戏，通过运行以下命令创建入口资源:

```
kubectl create -f my-ingress.yaml
```

坐下来，放松，等待证书管理器开始工作，并为您的域获取证书。完成后，您可以通过 HTTPS 从互联网访问您的服务。有了上面的样品入口，那将是[https://app.example.com](https://app.example.com)。

如果你有多个子域，如 console.example.com，api.example.com 等，你可以得到一个通配符证书来简化事情。例如，在上面的 yaml 中，只需将“*.example.com”指定为“tls”下“hosts”列表中的单个元素。

# **结论**

使用 Cert-Manager、NGINX Ingress 和 Let's Encrypt 简化了安全公开运行在 Kubernetes 集群上的微服务的过程。

就这些了，伙计们。

> 本文表达的观点是我个人的，不代表我的雇主的观点。