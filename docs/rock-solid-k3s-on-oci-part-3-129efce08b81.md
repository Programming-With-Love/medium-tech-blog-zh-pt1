# 坚如磐石的 Solid K3s 第三部分

> 原文：<https://medium.com/oracledevs/rock-solid-k3s-on-oci-part-3-129efce08b81?source=collection_archive---------1----------------------->

![](img/1f6e556f001ac716126f961fbadbfdc6.png)

Photo by Leon Macapagal: [https://www.pexels.com/photo/aerial-view-of-city-buildings-beside-water-6622898/](https://www.pexels.com/photo/aerial-view-of-city-buildings-beside-water-6622898/)

这是在 OCI 免费帐户中使用 K3s 系列的第三部分。如果您错过了前面的部分，您真的需要从头开始:

*   [第 1 部分—简介和 OCI 基础设施](/@timclegg/rock-solid-k3s-on-oci-part-1-dbfeaa69d670)
*   [第二部分— K3s 安装](/@timclegg/rock-solid-k3s-on-oci-part-2-4f7b95faca88)

我们现在准备设置我们新创建的 K3s 环境与 OCI 的集成。这是一个令人兴奋的方面，因为它将允许管理 OCI 负载平衡器、块存储卷等。

# 添加 OCI 容器注册凭据

[OCI 集装箱登记处](https://docs.oracle.com/en-us/iaas/Content/Registry/home.htm) (OCIR)将用于存储集装箱图像。我们将为将要构建/部署的容器化应用程序使用私有容器注册中心。这意味着 K3s 将需要 OCI 证书，以便与私人集装箱登记处合作。

```
kubectl create secret docker-registry ocirsecret --docker-server=<region>.[ocir.io](http://ocir.io/) --docker-username='<namespace>/<username>' --docker-password='<OCI_auth_token>' --docker-email='<your_oci_email>'
```

查看一下 [OCIR 文档](https://docs.oracle.com/en-us/iaas/Content/Registry/Concepts/registryprerequisites.htm#regional-availability)，了解您所在地区的不同地区 URL。

确保使用正确的用户名格式( *<名称空间> / <用户名>* 或者如果您使用的是 IDCS， *<名称空间>/Oracle identitycloudservice/<用户名>* )。密码将是与您的帐户相关联的身份验证令牌。查看 [OCI 文档](https://docs.oracle.com/en-us/iaas/Content/Registry/Tasks/registrypullingimagesfromocir.htm)了解更多信息。

# 设置 [OCI 云控制器管理器](https://github.com/oracle/oci-cloud-controller-manager)

[OCI 云控制器管理器](https://github.com/oracle/oci-cloud-controller-manager)与 K8s(在这种情况下是 K3s 实现)和您的 OCI 帐户交互，管理 OCI 可能需要的负载平衡器(LBs)。

首先创建几个目录:

```
sudo mkdir /etc/kubernetes
sudo mkdir /etc/oci
```

将以下内容放入*/etc/OCI/cloud-provider . YAML*(此时这应该是新创建的文件)。

```
# Copied from https://raw.githubusercontent.com/oracle/oci-cloud-controller-manager/master/manifests/provider-config-instance-principals-example.yaml
useInstancePrincipals: true
compartment: <OCID of compartment>
vcn: <OCID of k3s VCN>

loadBalancer:
  # subnet1 configures one of two subnets to which load balancers will be added.
  subnet1: <OCID of LB Subnet>
  securityListManagementMode: None

rateLimiter:
  rateLimitQPSRead: 20.0
  rateLimitBucketRead: 5
  rateLimitQPSWrite: 20.0
  rateLimitBucketWrite: 5
```

示例(使用 nano):

```
sudo nano /etc/oci/cloud-provider.yaml
```

然后将内容(如上)与您的环境的值(OCIDs)粘贴在一起。

现在在 K3s 中创建秘密:

```
kubectl create secret generic oci-cloud-controller-manager -n kube-system --from-file=cloud-provider.yaml=/etc/oci/cloud-provider.yaml
```

# 安装 OCI 云控制器管理器

在 server1 实例上运行以下命令:

```
export RELEASE=$(curl -s "https://api.github.com/repos/oracle/oci-cloud-controller-manager/releases" | jq .[0].tag_name | tr -d "\"")
kubectl apply -f https://github.com/oracle/oci-cloud-controller-manager/releases/download/${RELEASE}/oci-cloud-controller-manager-rbac.yaml

curl -L https://github.com/oracle/oci-cloud-controller-manager/releases/download/${RELEASE}/oci-cloud-controller-manager.yaml | sed 's/        node-role.kubernetes.io\/control-plane: ""/        node-role.kubernetes.io\/control-plane: "true"/g' > oci-cloud-controller-manager.yaml
kubectl apply -f oci-cloud-controller-manager.yaml
```

运行以下命令以获取运行窗格:

```
kubectl -n kube-system get po
```

它应该显示如下内容:

![](img/e8bf235ce4b9353f8d2eb846e2c966b8.png)

# 配置 SELinux

您可能会在某个时候看到一些 SELinux 错误，类似于以下内容(参见 */var/log/messages* ):

```
Aug 26 05:45:54 server1 setroubleshoot[226499]: failed to retrieve rpm info for /sys/fs/cgroup
Aug 26 05:45:55 server1 setroubleshoot[226499]: SELinux is preventing ip6tables from ioctl access on the directory /sys/fs/cgroup.
```

这可以通过运行以下命令来解决:

```
sudo ausearch -c 'ip6tables' --raw | audit2allow -M my-ip6tables
sudo semodule -X 300 -i my-ip6tables.pp
```

# 安装 nginx-ingress

我们将设置一个 nginx 入口控制器，它将允许我们通过一个负载均衡器路由不同服务的请求。我们将跟随

让我们从下载最新的 nginx ingress 清单开始。沿着[方向](https://kubernetes.github.io/ingress-nginx/deploy/#oracle-cloud-infrastructure)，运行:

```
curl https://raw.githubusercontent.com/kubernetes/ingress-nginx/$(curl -s "https://api.github.com/repos/kubernetes/ingress-nginx/releases" | jq '.[] | [select(.tag_name | startswith("controller-"))]' | jq -cs '.[0] | .[].tag_name' | tr -d "\"")/deploy/static/provider/cloud/deploy.yaml > ingress-nginx-test.yml
```

我们需要修改我们刚刚创建的文件( *ingress-nginx.yml* )中的服务，添加配置将要创建的 OCI LB 所需的注释:

```
<snip>
---
apiVersion: v1
kind: Service
metadata:
  labels:
    app.kubernetes.io/component: controller
    app.kubernetes.io/instance: ingress-nginx
    app.kubernetes.io/name: ingress-nginx
    app.kubernetes.io/part-of: ingress-nginx
    app.kubernetes.io/version: 1.3.1
  name: ingress-nginx-controller
  namespace: ingress-nginx
  annotations:
    oci.oraclecloud.com/load-balancer-type: "lb"
    service.beta.kubernetes.io/oci-load-balancer-shape: "flexible"
    service.beta.kubernetes.io/oci-load-balancer-shape-flex-min: "10"
    service.beta.kubernetes.io/oci-load-balancer-shape-flex-max: "10"
    service.beta.kubernetes.io/oci-load-balancer-subnet1: "<LB Subnet OCID >"
    oci.oraclecloud.com/oci-network-security-groups: "<LB NSG OCID>"
    service.beta.kubernetes.io/oci-load-balancer-security-list-management-mode: "None"
    oci.oraclecloud.com/node-label-selector: "k3s.io/role=agent"
spec:
<snip>
```

通过应用以下程序安装它:

```
kubectl apply -f ingress-nginx.yml
```

此时，您应该能够看到服务:

```
kubectl get service -n ingress-nginx
```

您应该会看到如下所示的内容:

```
$ kubectl get service -n ingress-nginx
NAME                                 TYPE           CLUSTER-IP      EXTERNAL-IP      PORT(S)                      AGE
ingress-nginx-controller-admission   ClusterIP      10.43.2.45      <none>           443/TCP                      9h
ingress-nginx-controller             LoadBalancer   10.43.142.214   <public_ip>      80:30173/TCP,443:30297/TCP   9h
$
```

*入口 nginx 控制器*上的*外部 IP* 字段将用于访问我们将要部署的不同应用。这是 OCI 负载平衡器(LB)使用的公共 IP 地址。

# 安装 OCI 集装箱存储接口(CSI)(可选)

这是一个可选步骤。如果您希望能够让 OCI 云控制器管理器管理 OCI 数据块卷(可能连接到一个 pod)，这可能是您感兴趣的。

以下步骤基于 [CSI 指示](https://github.com/oracle/oci-cloud-controller-manager/blob/master/container-storage-interface.md):

```
kubectl create secret generic oci-volume-provisioner -n kube-system --from-file=config.yaml=/etc/oci/cloud-provider.yaml

export RELEASE=export RELEASE=$(curl -s "https://api.github.com/repos/oracle/oci-cloud-controller-manager/releases" | jq .[0].tag_name | tr -d "\"")
kubectl apply -f https://github.com/oracle/oci-cloud-controller-manager/releases/download/${RELEASE}/oci-csi-node-rbac.yaml

curl -L https://github.com/oracle/oci-cloud-controller-manager/releases/download/${RELEASE}/oci-csi-controller-driver.yaml | sed 's/        node-role.kubernetes.io\/master: ""/        node-role.kubernetes.io\/master: "true"/g' > oci-csi-controller-driver.yaml
kubectl apply -f oci-csi-controller-driver.yaml

kubectl apply -f https://github.com/oracle/oci-cloud-controller-manager/releases/download/${RELEASE}/oci-csi-node-driver.yaml
kubectl apply -f https://raw.githubusercontent.com/oracle/oci-cloud-controller-manager/master/manifests/container-storage-interface/storage-class.yaml
```

应用以上清单后，检查 pod 是否正在运行(可能需要一两分钟完成):

```
kubectl -n kube-system get po | grep csi-oci-controller
kubectl -n kube-system get po | grep csi-oci-node
```

# 包装它

哇，我们在这个系列的这一部分已经完成了很多！我们已经设置了 OCI 云控制器，以及 nginx 入口控制器。接下来的事情是部署几个应用程序来看看这一切的行动。让我们在下一部分这样做…

与此同时，请在 [Oracle 开发人员闲置频道](https://bit.ly/odevrel_slack)与我们聊天！