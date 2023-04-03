# 如何在 OKE 中读取入口和负载平衡器后面的远程 IP 地址

> 原文：<https://medium.com/oracledevs/how-to-read-a-remote-ip-address-behind-ingress-and-load-balancer-in-oke-ea71dedcbbec?source=collection_archive---------0----------------------->

![](img/738260b04a8f05fb4721081c903d212a.png)

# 介绍

[适用于 Kubernetes](https://www.oracle.com/cloud/cloud-native/container-engine-kubernetes/) 的 Oracle 容器引擎，也称为 OKE，是功能强大的 Kubernetes 托管服务，是 Oracle 云基础设施中云原生堆栈的尖端。它与核心云组件紧密集成，如负载平衡器和网络。每一个现代的云原生部署都意味着您的容器化应用程序在 Kubernetes 中执行，通过入口控制器和其上的负载平衡器向外界公开。更重要的是，ingress 将使用[证书管理器](https://cert-manager.io/)来管理每个域和路径的证书。OKE 集成了 OCI 负载均衡器，你可以轻松安装多个 ingress 控制器，比如 [Ingress Nginx 控制器](https://kubernetes.github.io/ingress-nginx/)。

在高级场景中，您可能想从容器或入口控制器中知道远程客户端 IP 地址。这对于蓝/绿部署可能是必要的，在蓝/绿部署中，您将允许特定的源 IP 地址预览一些 canary 功能。典型的入口 Nginx 控制器安装与云无关，并采用默认的 OCI 负载平衡器设置。OCI 负载平衡器是一种灵活的基于代理的负载平衡器，运行在 OSI 模型的第 4 层和第 7 层。默认设置意味着负载平衡器监听器不通过`x-real-ip`或`x-forwarded-for`头传播远程 IP 地址，因为监听器最初使用 TCP。TCP 层没有报头，对吗？然后，您可以使用 [OCI 控制台](http://cloud.oracle.com)(或将[自定义注释](https://github.com/oracle/oci-cloud-controller-manager/blob/master/docs/load-balancer-annotations.md)添加到入口控制器清单中)并将负载平衡器监听器更改为使用 HTTP 或 HTTPS。这将解决问题，并在标头中暴露远程 IP 地址，但可能会导致另一个问题——在负载平衡器级别强制 SSL 终止。如果你想用`cert-manager`，这不是最愉快的选择。

一种解决方案是在入口控制器之前实现 OCI 网络负载平衡器。OCI 网络负载平衡器是一种替代解决方案，它是一种运行在 OSI 模型第 4 层的非代理负载平衡器。这意味着它不会做 SSL 终止，但它能够转发客户端源地址，如果配置这样做。

# 安装入口 Nginx 控制器，OCI 网络负载平衡器在前面

## 1

遵循 Oracle 官方指南设置 Nginx 控制器:[https://docs . Oracle . com/en-us/iaas/Content/ContEng/Tasks/contengsettingupingresscontroller . htm](https://docs.oracle.com/en-us/iaas/Content/ContEng/Tasks/contengsettingupingresscontroller.htm)

## 2

当您到达[部署*ingress-nginx-controller*](https://docs.oracle.com/en-us/iaas/Content/ContEng/Tasks/contengsettingupingresscontroller.htm#settingupcontroller__creatingserviceaccount)的步骤时，就在应用`kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.1.3/deploy/static/provider/cloud/deploy.yaml`之前，停止并下载[部署清单](https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.1.3/deploy/static/provider/cloud/deploy.yaml)。通过添加注释来修改它，以供应 OCI 网络负载平衡器，而不是默认的那个(在下面的代码片段中标记为粗体)。

```
apiVersion: v1
kind: Service
metadata:
  labels:
    helm.sh/chart: ingress-nginx-3.23.0
    app.kubernetes.io/name: ingress-nginx
    app.kubernetes.io/instance: ingress-nginx
    app.kubernetes.io/version: 0.44.0
    app.kubernetes.io/managed-by: Helm
    app.kubernetes.io/component: controller
  name: ingress-nginx-controller-nlb
  namespace: ingress-nginx
  annotations:
    **oci.oraclecloud.com/load-balancer-type: "nlb"**
spec:
  type: LoadBalancer
  **externalTrafficPolicy: Local**
  ports:
    - name: http
      port: 80
      protocol: TCP
      targetPort: http
    - name: https
      port: 443
      protocol: TCP
      targetPort: https
  selector:
    app.kubernetes.io/name: ingress-nginx
    app.kubernetes.io/instance: ingress-nginx
    app.kubernetes.io/component: controller
```

自定义注释`oci.oraclecloud.com/load-balancer-type: “nlb”`和规范属性`externalTrafficPolicy: Local` 将启用 Nginx 控制器内部的远程 IP 地址直通，一直到容器。

## 3

使用`kubectl apply -f downloaded_manifest.yaml`执行下载并更新的清单，并从第一步开始完成指南。

## 4

因为规范属性`externalTrafficPolicy: Local`将远程 IP 地址作为工作节点的源地址，所以要确保相应地更新工作节点子网安全列表。您很可能需要添加安全列表入口规则，以允许源 0.0.0.0/0(远程客户端 IP)到达工作节点端口(30000–32767)。你可以在[官方文件](https://docs.oracle.com/en-us/iaas/Content/ContEng/Tasks/contengcreatingloadbalancer.htm#contengcreatingnetworkloadbalancer_topic_Preserving_client_IP)中找到更多信息。

## 5

调用您的应用程序，检查您是否可以从容器中读取`x-real-ip`或`x-forwarded-for`头。在我的例子中，它非常有效，将远程客户端的 IP 地址写在头中。

```
{"host":"remoteip-nlb.micro.ivandelic.com","x-real-ip":"74.34.156.180","x-forwarded-for":"74.34.156.180","x-forwarded-host":"remoteip-nlb.micro.ivandelic.com","x-forwarded-port":"443","x-forwarded-proto":"https"...}
```

# 如果你在 Cloudflare Proxy 后面怎么办？

由于我们使用的是在第 4 层运行的 OCI 网络负载平衡器，因此 Cloudflare 设置的所有报头都会转发到入口 Nginx 控制器。这意味着每个 HTTP 请求中都存在`CF-Connecting-IP`头。默认情况下，Nginx 使用头`X-Forwarded-For`的内容来获取关于客户端 IP 地址的信息。这对我们的场景很不方便，因为`X-Forwarded-For`拥有 Cloudflare 代理的 IP 地址。这种行为的原因是 OCI 网络负载平衡器不识别 Cloudflare 代理的存在，并将代理 IP 地址设置为远程 IP 地址。显而易见的解决方法是配置 Nginx 使用`CF-Connecting-IP`报头来获取真正的远程 IP 地址，而不是`X-Forwarded-For`。

要使其工作，通过在 ConfigMap 中添加自定义配置来修改下载的入口 Nginx 清单，如下例所示。

```
apiVersion: v1
kind: ConfigMap
metadata:
  labels:
    helm.sh/chart: ingress-nginx-3.23.0
    app.kubernetes.io/name: ingress-nginx
    app.kubernetes.io/instance: ingress-nginx
    app.kubernetes.io/version: 0.44.0
    app.kubernetes.io/managed-by: Helm
    app.kubernetes.io/component: controller
  name: ingress-nginx-controller
  namespace: ingress-nginx
data:
  proxy-real-ip-cidr: "173.245.48.0/20,103.21.244.0/22,103.22.200.0/22,103.31.4.0/22,141.101.64.0/18,108.162.192.0/18,190.93.240.0/20,188.114.96.0/20,197.234.240.0/22,198.41.128.0/17,162.158.0.0/15,104.16.0.0/13,104.24.0.0/14,172.64.0.0/13,131.0.72.0/22,2400:cb00::/32,2606:4700::/32,2803:f800::/32,2405:b500::/32,2405:8100::/32,2a06:98c0::/29,2c0f:f248::/32"
  use-forwarded-headers: "true"
  forwarded-for-header: "CF-Connecting-IP"
```

注意:如果您还没有这样做，您可以[立即注册 Oracle 云免费层帐户](https://signup.cloud.oracle.com/?language=en)。如果你对如何开始学习 OCI 感兴趣，注册是第一步，没有必要跟着这篇文章走。

如果你对 Oracle 开发人员在他们的自然环境中发生的事情感到好奇，请加入我们的公共休闲频道！