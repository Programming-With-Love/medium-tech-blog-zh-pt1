# 使用 Oracle Container Engine for Kubernetes 实现集群节点自动伸缩

> 原文：<https://medium.com/oracledevs/cluster-node-autoscaling-with-oci-container-engine-for-kubernetes-11dd59eb4847?source=collection_archive---------5----------------------->

![](img/78e368c77d22fffb7f4a1214f531a9ca.png)

Oracle Container Engine for Kubernetes(OKE)减轻了创建和运营企业级 Kubernetes 集群的运营负担，这些集群用于将容器化的应用程序部署到云中。Oracle 管理您的集群资源，并自动执行重复的 Kubernetes 管理和扩展任务，以简化 Kubernetes 运营。到目前为止，OKE 客户一直使用 pod 级自动扩展来满足他们不断变化的资源需求。这种方法适用于许多用例，但是手动扩展底层节点资源的需求会给集群管理员带来不必要的负担。

为了满足这些需求，我们在 2021 年 3 月发布了 Kubernetes (OKE)的 OCI 容器引擎集群节点自动伸缩。现在，您可以使用集群自动缩放器根据工作负载需求动态扩展节点池。

# 自动缩放您的 Kubernetes 集群

应用程序面临动态需求。例如，一家公司的电子商务应用程序可能会因为假期或其产品突然流行而面临网站访问量的增加。他们的 Kubernetes 集群必须适应这些不断波动的需求，否则会面临与资源供应不足或过度供应的额外成本相关的可用性问题。

OKE 允许您使用来自 [Kubernetes Metrics Server](https://github.com/kubernetes-sigs/metrics-server) 或其他开源指标服务器的数据，通过水平或垂直扩展集群中的 pod 来调整应用程序的规模。 [Kubernetes 水平 Pod 自动缩放器](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) (HPA)调整部署中的 Pod 数量，而 [Kubernetes 垂直 Pod 自动缩放器](https://github.com/kubernetes/autoscaler/tree/master/vertical-pod-autoscaler)调整部署的 Pod 中运行的容器的资源请求和限制。这两种解决方案都根据 CPU 利用率进行调整，HPA 还支持自定义指标。它们解决了 pod 级别的自动缩放，但没有解决底层资源的缩放。OKE 目前允许您手动扩展那些底层资源:集群节点池中工作节点的形状和数量。手动扩展适用于可预测的工作负载，但可能不适合不可预测的工作负载，并且会给集群管理员带来不必要的负担。

你可以用 OKE 现在支持的 [Kubernetes 集群自动缩放器](https://github.com/kubernetes/autoscaler)来解决这个问题。Cluster Autoscaler 根据工作负载需求自动调整 Kubernetes 集群的节点池大小。它可以帮助您优化 OCI 计算资源的使用和成本。当需求增加时，节点的数量会增加以满足这些需求。当需求减少时，节点数量会减少，而不是将多余的资源分配给集群。

# 把这一切联系在一起

让我们回到我们提到的电子商务应用程序。负责应用程序的集群管理员[将 Kubernetes 度量服务器](https://docs.oracle.com/en-us/iaas/Content/ContEng/Tasks/contengdeployingmetricsserver.htm)部署到他们的集群，而[配置水平 Pod 自动缩放器](https://docs.oracle.com/en-us/iaas/Content/ContEng/Tasks/contengusinghorizontalpodautoscaler.htm) (HPA)以基于 CPU 和内存利用率创建他们的 Kubernetes 部署的副本。他们更进一步，使用[垂直 Pod 自动缩放器](https://docs.oracle.com/en-us/iaas/Content/ContEng/Tasks/contengusingverticalpodautoscaler.htm) (VPA)的推荐组件来确定其容器的理想 CPU 和内存请求值。

因为他们正在处理一个任务关键型应用程序，所以管理员指定了 [pod 中断预算](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/)，该预算限制了由于自愿中断而同时不可用的 pod 总数，并指定了其他 Kubernetes 功能来维护应用程序可用性。直到现在，自动化到此为止。管理员必须手动扩展节点池中节点的数量和大小。

随着 Cluster Autoscaler 的引入，节点扩展也可以自动化。集群管理员部署集群自动缩放器，并将其配置为使用无状态工作负载来扩展节点池。为了保持对如何在这些池中添加和删除节点的控制，他们选择不包括运行 statefulsets 和 kube-system pods 的节点池。

采用集群自动扩展后，下一次出现这些高流量时段时，HPA 会在单元级别触发扩展，当复制副本数量增加到节点无法处理且单元变得不可调度时，集群自动扩展会启动并为这些单元创建要调度到的节点。当高流量时期结束时，HPA 会将复制副本的数量自动调整到适当的级别，而群集自动调整会减少节点的数量。

运行 Kubernetes 版本 1.17 和更高版本的集群支持集群自动缩放。Cluster Autoscaler 作为部署在集群中运行，并扩展指定节点池中的工作节点。

# 想了解更多？

要了解更多信息，请使用以下资源:

*   如需了解更多关于 Kubernetes(OKE)OCI 集装箱发动机的信息，请查看我们的[产品详情](https://www.oracle.com/cloud-native/container-engine-kubernetes/)和[产品文档](https://docs.oracle.com/en-us/iaas/Content/ContEng/Concepts/contengoverview.htm)页面。
*   在 GitHub 上了解[集群自动缩放器](https://docs.oracle.com/en-us/iaas/Content/ContEng/Tasks/contengautoscalingclusters.htm)和 [Kubernetes 集群自动缩放器。](https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md)
*   了解有关甲骨文[云原生服务](https://www.oracle.com/cloud-native/)的更多信息。

最初发布于 blogs . Oracle . com:[https://blogs . Oracle . com/cloud-infra structure/cluster-node-auto scaling-with-OCI-container-engine-for-kubernetes](https://blogs.oracle.com/cloud-infrastructure/cluster-node-autoscaling-with-oci-container-engine-for-kubernetes)