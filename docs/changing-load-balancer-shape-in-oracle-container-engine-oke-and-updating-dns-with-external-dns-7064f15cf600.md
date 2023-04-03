# 更改 Oracle 容器引擎(OKE)中的负载平衡器形状并使用外部 DNS 更新 DNS

> 原文：<https://medium.com/oracledevs/changing-load-balancer-shape-in-oracle-container-engine-oke-and-updating-dns-with-external-dns-7064f15cf600?source=collection_archive---------0----------------------->

![](img/332b78c88aa834cce0515c5b895d0b0c.png)

在[之前的帖子](/oracledevs/loadbalancer-service-oracle-container-engine-oke-and-oci-dns-d7b1f7b4f9bd)中，我们做了以下工作:

1.  部署应用程序
2.  部署了一个负载平衡器服务，为您提供一个公共 IP 地址
3.  使用基于主机的规则创建入口

为了使这个服务可以通过 DNS 公开解析，我们还手动创建了一个 DNS 区域，以及一个 DNS“A”记录，并将其指向负载平衡器的公共 IP 地址。默认情况下，创建的 OCI 负载平衡器的形状是“100 Mbps”。

## 打扫

首先对之前的帖子做一点清理:

1.  在 OCI 控制台中，导航到边缘服务、DNS 区域管理，然后单击您的 DNS 区域名称
2.  通过选中 DNS“A”记录旁边的复选框，从中选择该记录，然后选择操作>删除。然后，单击发布更改以使更改生效。
3.  删除负载平衡器服务:

```
kubectl delete svc nginx-ingress-controller
```

## 更改负载平衡器的形状

比方说，您的应用程序正在处理增加的负载，您想要更改负载平衡器的形状。

您可以通过添加注释来做到这一点。你可以在这里查看可用的注释。

下载 hello-ingresscontroller.yaml:

curl-o hello-ingress controller . YAML[https://raw . githubusercontent . com/Hyder/oke samples/master/load balancer/hello-ingress controller . YAML](https://raw.githubusercontent.com/hyder/okesamples/master/loadbalancer/hello-ingresscontroller.yaml)

将以下注释添加到 hello-ingresscontroller.yaml 中

```
apiVersion: v1
kind: Service
metadata:
  name: nginx-ingress-controller
  namespace: default
  labels:
    app: nginx-ingress-controller
  **annotations:
    service.beta.kubernetes.io/oci-load-balancer-shape: "400Mbps"**
spec:
  type: LoadBalancer
  ports:
  - port: 80
    nodePort: 30021
    name: http
  - port: 443
    nodePort: 30022
    name: https
  selector:
    app: nginx-ingress-controller
```

如果您希望指定负载平衡器所需的两个子网，也可以这样做。如果没有，系统会自动为您选择它们。

应用更改:

```
kubectl apply -f hello-ingresscontroller.yaml
```

验证负载平衡器的形状现在已更改为 400Mbps:

![](img/774293cf72c630cda8cdc220953138b0.png)

为了让您的 FQDN 解析到新的 IP 地址，您需要编辑之前创建的 DNS“A”记录并更新 IP 地址。

手动进行所有这些更改和更新可能会很乏味。如果能自动更新这个不是很好吗？向前一步[外部 DNS](https://github.com/kubernetes-incubator/external-dns) 。

## 外部 DNS

ExternalDNS 通过将 DNS 记录与预期的入口点同步来为您处理这最后一点，特别是当您使用基于请求主机的 Ingresses 时。

外部 DNS 使 Kubernetes 资源(如服务和入口)可以使用公共 DNS 服务器被发现。它配置公共 DNS 服务器并更新所需的 DNS 记录，因此您不必手动完成。

![](img/6fe8b628bb1837bfce47d0bc048a5fb1.png)

首先，创建以下 yaml 并将其保存到 oci.yaml:

```
auth:
  region: us-phoenix-1
  tenancy: ocid1.tenancy.oc1...
  user: ocid1.user.oc1...
  key: |
    -----BEGIN RSA PRIVATE KEY-----
    -----END RSA PRIVATE KEY-----
  fingerprint: af:81:71:8e...
compartment: ocid1.compartment.oc1...
```

该密钥必须与您的 api 私钥相匹配。如果你已经使用[**terra form-OCI-OKE**](https://github.com/oracle-terraform-modules/terraform-oci-oke)创建了你的 OKE 集群，你将已经拥有这些。如果没有，按照[指令](https://github.com/oracle-terraform-modules/terraform-oci-oke/blob/master/docs/instructions.md)创建密钥并上传到 OCI。

您现在可以创建一个秘密:

```
kubectl create secret generic external-dns-config --from-file=oci.yaml
```

接下来，为 ExternalDNS 创建 ServiceAccount、ClusterRoleBinding 和部署:

```
kubectl apply -f   [https://raw.githubusercontent.com/hyder/okesamples/master/loadbalancer/externaldns-rbac.yaml](https://raw.githubusercontent.com/hyder/okesamples/master/loadbalancer/externaldns-rbac.yaml)
```

编辑更新负载平衡器的注释:

```
apiVersion: v1
kind: Service
metadata:
  name: nginx-ingress-controller
  namespace: default
  labels:
    app: nginx-ingress-controller
  annotations:
    service.beta.kubernetes.io/oci-load-balancer-shape: "400Mbps"
    **external-dns.alpha.kubernetes.io/hostname: www.example.org**
spec:
  type: LoadBalancer
  ports:
  - port: 80
    nodePort: 30021
    name: http
  - port: 443
    nodePort: 30022
    name: https
  selector:
    app: nginx-ingress-controller
```

并再次应用它:

```
kubectl apply -f hello-ingresscontroller.yaml
```

验证以下内容:

1.  已经使用负载平衡器的 IP 地址创建了 DNS“A”记录
2.  您可以使用 nslookup 公开解析您的 FQDN，或者在此应用程序的情况下，使用您的浏览器，您可能需要等待几分钟才能更新更改。

想了解更多关于 OCI 外部域名的信息，你可以查看 GitHub 上的[教程](https://github.com/kubernetes-incubator/external-dns/blob/master/docs/tutorials/oracle.md)。

如果你用的是 Oracle Dyn，可以参考[本教程](https://github.com/kubernetes-incubator/external-dns/blob/master/docs/tutorials/dyn.md)来代替。