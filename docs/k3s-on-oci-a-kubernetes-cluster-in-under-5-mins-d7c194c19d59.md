# K3s:不到 5 分钟的库伯内特星团

> 原文：<https://medium.com/oracledevs/k3s-on-oci-a-kubernetes-cluster-in-under-5-mins-d7c194c19d59?source=collection_archive---------0----------------------->

![](img/a69c2790c87111b648b80460aee17041.png)

[K3s](https://k3s.io/) 是一个[沙盒](https://www.cncf.io/sandbox-projects/) [CNCF](https://www.cncf.io/) 项目，它提供了一个轻量级的 [Kubernetes](https://kubernetes.io/) ，该项目针对边缘和物联网用例进行了优化，或者适用于当您想要运行 Kubernetes 但不想花那么多钱、时间或者您不想这么做的时候 [Kubernetes —艰难的方式](https://github.com/kelseyhightower/kubernetes-the-hard-way)。

让我们在 OCI 身上试试。

## 具有嵌入式数据库的单服务器设置

在这篇文章中，我们想看看它是否有效。这里的想法是做一个简单的部署，稍微打探一下。或者，如果您需要在 Oracle Cloud Free Tier[上运行，在这种情况下，您也可以尝试在 ARM 上运行 K3s。](https://www.oracle.com/cloud/free/)

对于快速和肮脏，我们将需要以下:

*   一个 VCN
*   互联网网关
*   允许 SSH 的公共子网和附加安全列表
*   通往 Internet 网关的公共子网的路由表
*   计算虚拟机

因此，让我们使用 terraform vcn 模块来为我们做更脏的工作:

```
module "vcn" { source = "oracle-terraform-modules/vcn/oci"
  version = "3.0.0" # general oci parameters
  compartment_id = var.compartment_id
  label_prefix = var.label_prefix # gateways
  create_internet_gateway = true
  create_nat_gateway = false
  create_service_gateway = true # vcn
  vcn_cidrs = ["10.0.0.0/16"]
  vcn_dns_label = "k3s"
  vcn_name = "k3s"
  lockdown_default_seclist = false
}
```

运行 terraform apply 创建 VCN。接下来，登录 OCI 控制台，导航至**网络** > **虚拟云网络**，点击您的 VCN。创建一个子网，确保您选择了 internet 路由表以及 DHCP 和安全列表的默认选项。也给它一个 CIDR 块，例如 10.0.0.0/24。

创建子网后，导航至**计算** > **实例**。单击创建实例，选择 Oracle Linux 8、k3s VCN 和您刚刚创建的公共子网。确保选择了“分配公共 IPv4 地址”,并上传/生成 ssh 密钥。对于引导卷，我们给它一个 100GB 的大容量(尽管我们不需要那么多)。如果你更倾向于 ARM，可以把形状改成安培，选择 VM。标准。A1。也是 Flex。

实例可用后，通过 ssh 连接到虚拟机:

```
ssh -i /path/to/private/key opc@public_ip_address
```

登录后，我们可以安装 K3s:

```
curl -sfL https://get.k3s.io | sh -
```

更改 k3s.yaml 文件的权限:

```
sudo chmod go+r /etc/rancher/k3s/k3s.yaml
```

运行 kubectl:

```
kubectl get nodes
```

这应该行得通。

让我们安装 Kubernetes 仪表板:

```
GITHUB_URL=https://github.com/kubernetes/dashboard/releasesVERSION_KUBE_DASHBOARD=$(curl -w ‘%{url_effective}’ -I -L -s -S ${GITHUB_URL}/latest -o /dev/null | sed -e ‘s|.*/||’)k3s kubectl create -f [https://raw.githubusercontent.com/kubernetes/dashboard/${VERSION_KUBE_DASHBOARD}/aio/deploy/recommended.yaml](https://raw.githubusercontent.com/kubernetes/dashboard/${VERSION_KUBE_DASHBOARD}/aio/deploy/recommended.yaml)
```

创建管理用户和集群角色绑定，并保存到文件“dashboard.admin-user.yaml”:

```
apiVersion: v1
kind: ServiceAccount
metadata:
  name: admin-user
  namespace: kubernetes-dashboard---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: admin-user
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- kind: ServiceAccount
  name: admin-user
  namespace: kubernetes-dashboard
```

创建具有管理员权限的用户并获取不记名令牌:

```
kubectl create -f dashboard.admin-user.yaml
```

退出 ssh 会话并设置端口转发:

```
ssh -L 8001:localhost:8001 opc@public_ip
```

获取不记名令牌并运行 kubectl 代理:

```
k3s kubectl -n kubernetes-dashboard describe secret admin-user-token | grep ‘^token’k3s kubectl proxy
```

使用您的浏览器访问位于[的仪表板，http://localhost:8001/API/v1/namespace/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/](http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/)

确保您选择了“令牌”选项来登录并粘贴您在上面获取的无记名令牌的值。

您现在可以登录您的 Kubernetes 仪表板了。

## 结论

嗯，这非常简单:一个 Kubernetes 集群在不到 10 分钟的时间内启动并运行，同时输入这个。不可否认，它是所有东西的单一节点，所以我想看看我能通过一点自动化实现什么:4 分钟和 11 分钟。在以后的文章中，我们将更多地尝试其他部署选项，并使用其他 OCI 服务。

参考资料:[https://rancer . com/docs/k3s/last/en/installation/kube-dashboard/](https://rancher.com/docs/k3s/latest/en/installation/kube-dashboard/)