# 在 OCI 使用 Helm 部署入口 Nginx

> 原文：<https://medium.com/oracledevs/deploy-ingress-nginx-using-helm-in-oci-c3ff4d9d5450?source=collection_archive---------0----------------------->

![](img/43a5cd928c27711279a6df30bc30ce4c.png)

有多种方法可以在 Kubernetes (OKE)的 OCI 容器引擎上安装 Nginx 控制器。你可以按照官方[文件](https://docs.oracle.com/en-us/iaas/Content/ContEng/Tasks/contengsettingupingresscontroller.htm)的建议，使用 YAML 清单，用`kubectl apply`安装它，或者利用[图表](https://github.com/kubernetes/ingress-nginx/tree/main/charts/ingress-nginx)安装 Helm。

我使用幂等命令来安装基于官方[快速入门](https://kubernetes.github.io/ingress-nginx/deploy/#quick-start)的图表。幂等性意味着如果它还没有安装，它将被安装，或者如果已经存在，它将被升级。简单的命令如下所示:

```
helm upgrade --install ingress-nginx ingress-nginx \
  --repo https://kubernetes.github.io/ingress-nginx \
  --namespace ingress-nginx --create-namespace
```

尽管如此，如果没有定制，例如负载平衡器形状、带宽和其他基于云控制管理器[注释](https://github.com/oracle/oci-cloud-controller-manager/blob/master/docs/load-balancer-annotations.md)的定制，控制器将不会很方便。完整的命令，包括负载平衡器的形状和带宽，现在如下所示:

```
helm upgrade --install ingress-nginx ingress-nginx --repo https://kubernetes.github.io/ingress-nginx --namespace ingress-nginx --create-namespace \
   --set controller.service.annotations.service\\.beta\\.kubernetes\\.io/oci-load-balancer-shape=flexible \
   --set controller.service.annotations.service\\.beta\\.kubernetes\\.io/oci-load-balancer-shape-flex-min=10 \
   --set controller.service.annotations.service\\.beta\\.kubernetes\\.io/oci-load-balancer-shape-flex-max=50
```

每个注释都设置有配置参数`controller.service.annotations`。不要忘记用双反斜杠对注释点进行转义，瞧，您已经部署好了 Nginx 入口控制器。

如果你对甲骨文开发人员在他们的自然栖息地发生的事情感到好奇，来[加入我们的公共休闲频道](https://bit.ly/odevrel_slack)！

如果您还没有这样做，您可以立即[注册 Oracle 云免费层帐户](https://signup.cloud.oracle.com/?language=en)。如果你对如何开始学习 OCI 感兴趣，注册是第一步，没有必要跟着这篇文章走。