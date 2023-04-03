# 3.14 在 Oracle 云上使用 Kubernetes 集群自动缩放器的技巧

> 原文：<https://medium.com/oracledevs/3-14-tips-for-using-the-kubernetes-cluster-autoscaler-on-oracle-cloud-1e65011c2f1f?source=collection_archive---------0----------------------->

## 在本文中，我们将介绍 Oracle Cloud 集群自动伸缩节点组的两种实现，以及我们在*大规模使用它们时学到的一些技巧*

![](img/6ed93c7d44bb5f78a43b5f0ffd0a5677.png)

[Kubernetes 集群自动缩放器](https://github.com/kubernetes/autoscaler/tree/master/cluster-autoscaler) (CA)通过动态调整后台云提供商上的缩放组大小，根据工作负载自动调整集群中 Kubernetes 节点的数量。[许多云提供商](https://github.com/kubernetes/autoscaler/tree/master/cluster-autoscaler/cloudprovider)都有一个或多个*实施*，作为包括 Oracle 云基础设施(OCI)在内的集群自动伸缩的[节点组](https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#how-does-scale-up-work)。如果您正在运行[Oracle Container Engine for Kubernetes](https://docs.cloud.oracle.com/iaas/Content/ContEng/Concepts/contengoverview.htm)(OKE)，节点组将[实现为后端(`--cloud-provider=oke`)上的 OKE](https://docs.oracle.com/en-us/iaas/Content/ContEng/Tasks/contengusingclusterautoscaler.htm) [节点池](https://docs.oracle.com/en-us/iaas/Content/ContEng/Tasks/contengscalingclusters.htm)。对于那些在 OCI 基础设施上运行*非托管* Kubernetes 发行版的人，我们已经使用 OCI [实例池](https://docs.oracle.com/en-us/iaas/Content/Compute/Tasks/creatinginstancepool.htm) ( `--cloud-provider=oci`)开发了一个新的[实现](https://github.com/kubernetes/autoscaler/tree/master/cluster-autoscaler/cloudprovider/oci)。

为了庆祝 [Pi (π) Day](https://en.wikipedia.org/wiki/Pi_Day) ，我们认为我们应该看看在大规模自动扩展 Kubernetes 节点时正确配置的 3 个重要方面，包括后台[实例池](https://docs.oracle.com/en-us/iaas/Content/Compute/Tasks/creatinginstancepool.htm)和 OKE [节点池](https://docs.oracle.com/en-us/iaas/Content/ContEng/Tasks/contengscalingclusters.htm)的配置、集群自动扩展配置，以及在我们的生产集群中运行的应用 pod。在这些 **3** 区域中，我们选择了我们所学过的最重要的 **14** 技巧……**3.14、*明白了吗？***

*注意:如果您还没有注册，您可以今天就* [*注册甲骨文云免费层账户*](https://signup.cloud.oracle.com/?language=en&sourceType=:ow:de:te::::RC_WWMK220210P00062:Medium_314tipsKubernetes&intcmp=:ow:de:te::::RC_WWMK220210P00062:Medium_314tipsKubernetes) *。*

# 概括地说，集群自动扩展

在深入研究具体的技巧和建议之前，我们学到的第一件事是，通过熟悉 CA 如何工作，它如何*不*工作，以及它如何被调整和定制，您可以避免很多挫折。

**什么 CA *做* :**

1.  等待`*--scan-interval*`，检查 Kubernetes 集群中的`Pending`pod。
2.  对于 CA 配置中的每个节点组，要求提供商提供该组*中的新节点*的“模型”(即资源、标签、污点等)。).
3.  模拟 Kubernetes 调度程序逻辑，查看/预测`Pending`Pod*是否可以在该组中的这个假想的新节点上进行调度。*
4.  如果是，*和*该节点组未达到其最大或总节点数，则让云提供商执行调整大小(可能会将*多个*节点添加到*多个*组)。
5.  循环回到步骤 1。

**CA*没有*做的事情:**

1.  考虑节点上的*实际* CPU、内存或 GPU 利用率(只有组合的[资源*请求*](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/#requests-and-limits) )。
2.  向您的 Kubernetes 集群注册节点。
3.  安装软件或配置节点。
4.  给节点添加标签或污点。
5.  将窗格计划到节点。

GitHub 上的 CA [常见问题](https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md)有更多的信息，值得一读。

# 1.云提供商节点组配置提示

> **提示 1.1:** 配置 [CA 部署](https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/cloudprovider/oci/examples/oci-ip-cluster-autoscaler-w-config.yaml#L126)来管理**多个**节点组(即 OKE 节点池或 OCI 实例池)，每个节点组的作用域为**单个** [可用性域](https://docs.oracle.com/en-us/iaas/Content/General/Concepts/regions.htm) (AD)。*这种配置允许自动缩放器的调度模拟器根据 AD 精确地和*确定性地*按比例增加 Kubernetes 节点，例如当*[*topology.kubernetes.io/zone*](http://topology.kubernetes.io/zone)*的反亲缘关系是一个特定 Pod 待定/不可调度的原因时。*

```
kind: Deployment
metadata:
  name: cluster-autoscaler
  namespace: kube-system
   ...
 - command:
  - ./cluster-autoscaler
  - --cloud-provider=oci
  - --nodes=1:50:ocid1.instancepool.oc1.phx.ad1...
  - --nodes=0:75:ocid1.instancepool.oc1.phx.ad2...
  - --nodes=0:90:ocid1.instancepool.oc1.phx.ad3...
```

> **提示 1.2:** 在尽可能大的节点组上设置用户自定义的[最大节点数](https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-are-the-parameters-to-ca) `*--nodes=<min>:<****max****>:<id>*`。*由于扩展是一个耗时的过程，CA 会连续执行*和*，我们观察到，通过让它在*最少*的独立操作中提供*最多*个节点，性能得到了提高。*
> 
> **提示 1.3:** 将节点组的[最小节点计数](https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-are-the-parameters-to-ca)设置为 0 `*--nodes=****0****:<max>:<id>*`。*CA 能够完全缩减未使用的节点组**(即缩减至 0)当不需要组中的节点时，允许完全缩减以减少或消除云提供商上不需要的资源的成本是非常有用的。*
> 
> **提示 1.4:** 当使用多个节点组时，如果多个节点组满足 Pod 要求，使用[扩展器](https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/expander/priority/readme.md)来控制要扩展的节点组。*如前所述，我们几乎总是配置 CA 来管理多个*节点组* *。*我们也*更喜欢使用* [*灵活计算形状*](https://docs.oracle.com/en-us/iaas/Content/Compute/References/computeshapes.htm) *当它们可用时。* `*--expander=priority*` *让我们*将我们的节点组优先排序*到 CA，例如，这是一个将基于 flex 实例的池优先排序到基于虚拟机标准实例的池的扩展示例。**

```
kind: ConfigMap
metadata:
  name: cluster-autoscaler-priority-expander
  namespace: kube-system
data:
  priorities: |-
    50:
      - .*flex-nodepool.*
    20:
      - .*vm-standard-nodepool.*
```

# 2.集群自动缩放配置提示

> **提示 2.5:** 使用与集群 Autoscaler (CA)的 ***相同的*** **版本**作为您的 Kubernetes 服务器。*例如，如果你的集群正在运行*[*【Kubernetes】v 1.23*](https://github.com/kubernetes/kubernetes/releases/tag/v1.23.0)*，那么你应该运行* [*集群自动缩放器 v1.23*](https://github.com/kubernetes/autoscaler/releases/tag/cluster-autoscaler-1.23.0) *。原因是 CA* [*直接从 Kubernetes 本身导入调度逻辑*](https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/simulator/scheduler_based_predicates_checker.go#L28) *。为了避免难以诊断 CA 模拟器和实际调度程序之间的调度不匹配，请使用与 Kubernetes 相同的 CA 版本。此外，在您的部署中使用*自定义*调度程序之前，请考虑对 CA 的影响(即*调度策略*可能与 CA 的调度模拟器不同)。*
> 
> **技巧 2.6:** 将 CA Pod 与您的容器化工作负载隔离开来。*因为 CA 是 CPU 和内存密集型的，我们更喜欢在我们的 Kubernetes 主节点上运行它，让 CA 部署容忍我们主节点的* [*污点*](https://kubernetes.io/docs/concepts/scheduling-eviction/taint-and-toleration/) *。我们避免让 CA 部署在节点上与我们的应用程序池一起运行，因为应用程序负载会对 CA 的性能产生负面影响，或者更糟，导致 CA 被驱逐，使其无法采取扩展措施来解决争用问题。*
> 
> **提示 2.7:** 运行 CA 的多个副本。在任何给定时间，只有一个自动缩放副本可以成为主副本。*尽管如此，我们发现运行多个副本是有益的，因为它增加了一个副本总是健康和起作用的可能性。*
> 
> **提示 2.8:** 定制 CA 等待新节点被供应的[最大时间&使用`--max-node-provision-time`在**您的**云提供商上加入您的集群(默认为 15 分钟)。*即使在同一个提供者中，新节点被供应和注册本身所花费的时间也可能因各种因素而有所不同，这些因素包括操作系统和新节点加入集群的引导程序(*](https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-are-the-parameters-to-ca)[*OKE*](https://www.oracle.com/cloud-native/container-engine-kubernetes/)*、* [*、RKE*](https://rancher.com/docs/rke/latest/en/managing-clusters/) *、*[*kops*](https://github.com/kubernetes/kops/blob/master/addons/cluster-autoscaler/cluster-autoscaler.sh)*等)。).*
> 
> **提示 2.9:** 自定义[使用`--scan-interval`重新评估](https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-are-the-parameters-to-ca)集群的频率(默认为 10 秒)。*考虑您的 CA 响应要求与您的 Kubernetes API 服务器和云提供商所需的常规 API 调用量之间的权衡。您最不希望的事情就是 CA 在您需要它采取缩放操作的时候被抑制。在大多数云提供商上，配置节点需要几分钟时间。我们发现，我们可以将扫描间隔增加到 2 分钟以上，而不会明显影响 CA 的响应能力。*
> 
> **提示 2.10:** 监控`*cluster-autoscaler-status*`配置图、事件和 CA 的普罗米修斯风格[/度量](https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/proposals/metrics.md)端点。*除了 CA 容器日志，* `*cluster-autoscaler-status*` *configmap 对调试问题很有帮助。它包含 CA *感知的节点组*的状态，包括每个节点组的健康状况、请求了多少个节点以及实际加入集群的节点数量、伸缩事件等。CA 的内部* `*/metrics*` *端点包含更多的* [*详细指标*](https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/proposals/metrics.md)*(go-routine 的数量、CPU 和内存细节等。)以及与 CA 框架的各个部分所花费的时间相关的度量。*
> 
> **提示 2.11:** 与[水平 Pod 自动缩放器](https://github.com/jthomperoo/horizontal-pod-autoscaler) (HPA)或[集群比例自动缩放器](https://github.com/kubernetes-sigs/cluster-proportional-autoscaler) (CPA)一起运行集群自动缩放器(CA)，以获得不同的可扩展性。*对于某些工作负载，这些不同的 Kubernetes 自动缩放器可以解决不同的问题，并且在一起使用*时可以很好地互补*。HPA 和 CPA 都会动态增加部署副本数量，从而增加所需的资源。CA 介入并调整节点计数，以满足新添加的副本的新资源需求。*

# 3.应用程序窗格配置提示

> **提示 3.12:** 尽可能确保您的应用程序单元能够承受移动。*除了监视不可调度的 pod，CA 还会定期寻找机会*将 pod*移动到不同的节点，以减少集群中的节点数量(即删除未充分利用的节点)。重要的是要记住，删除一个给定的节点需要* 重新启动之前在其上运行的所有 Pods *。*
> 
> **提示 3.13:** 防止任何没有弹性的应用舱被移动。有些豆荚没有弹性，或者移动起来很贵。 *我们用* `*cluster-autoscaler.kubernetes.io/safe-to-evict=false*` *到* [*标注这样的 pod，防止它们*](https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-types-of-pods-can-prevent-ca-from-removing-a-node) *被 CA 强制到不同的节点上。*
> 
> **提示 3.14:** 确保你所有的豆荚都有[资源请求](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/)设置在它们的规格中。CA 不监控节点上的实际资源使用情况——只监控 pod 规范中的组合[资源请求。u*n 如果您的 pod 的所有*都设置为* `spec.containers[].resources.requests` *，CA 将无法准确了解节点的资源利用率，包括当未充分利用的节点的* `*--scale-down-utilization-threshold*`*](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/#resource-requests-and-limits-of-pod-and-container) *[*满足*](https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#how-does-scale-down-work) *时。一个没有使用资源请求的*单个 *Pod 将会丢弃它，因为它的资源没有反映在 CA 在该节点*上的 Pod 的总*资源请求*中。*

# 包扎

在本帖中，我们分享了运行集群自动缩放器的最重要和最艰难的技巧。我们还提供了 OCI 为托管和非托管 Kubernetes 选项自动扩展节点组(OKE 节点池、OCI 实例池)的两个实现的链接。我们希望你至少发现了一些有用的提示。请不要犹豫，留下你的评论或者在相关的 GitHub 项目中提出问题。

***快乐的霹日*！**

# 加入对话！

如果你对甲骨文开发人员在他们的自然栖息地发生的事情感到好奇，来[加入我们的公共休闲频道](https://join.slack.com/t/oracledevrel/shared_invite/zt-uffjmwh3-ksmv2ii9YxSkc6IpbokL1g?customTrackingParam=:ex:tb:::::RC_WWMK220210P00062:Medium_314tipsKubernetes)！我们不介意成为你的鱼缸🐠

*照片由* [*乌卡斯兹阿达*](https://unsplash.com/@lukaszlada?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) *上*[*Unsplash*](https://unsplash.com/s/photos/cloud-pie?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)