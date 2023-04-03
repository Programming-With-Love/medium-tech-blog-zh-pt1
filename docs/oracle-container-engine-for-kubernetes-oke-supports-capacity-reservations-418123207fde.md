# 用于 Kubernetes 的 Oracle 容器引擎(OKE)支持容量预留

> 原文：<https://medium.com/oracledevs/oracle-container-engine-for-kubernetes-oke-supports-capacity-reservations-418123207fde?source=collection_archive---------0----------------------->

![](img/74645910ef0e8f89ae6982b01e06ac5d.png)

Plan in advance to ensure worker node capacity is available for critical workloads

保留的 [Oracle 云基础设施(OCI)计算](https://www.oracle.com/cloud/compute)容量[现在可以用于](https://docs.oracle.com/en-us/iaas/releasenotes/changes/e80a6d99-b782-46d8-906e-d04cac798a3f/)为 Kubernetes (OKE) 工作节点创建 [Oracle 容器引擎。预留容量让用户确信，计算资源将在最需要的时候可用，例如从灾难或意外工作负载高峰中恢复。](https://docs.oracle.com/en-us/iaas/Content/ContEng/home.htm#top)

# 什么是容量预留？

[容量预留](https://docs.oracle.com/iaas/Content/Compute/Tasks/reserve-capacity.htm#default)使用户能够提前预留虚拟机和裸机计算实例容量，以确保资源可供租户在需要时使用。没有预留容量的用户可能会发现自己处于这样一种情况:某个区域内的特定[故障域](https://docs.oracle.com/en-us/iaas/Content/General/Concepts/regions.htm#fault)或[可用性域](https://docs.oracle.com/en-us/iaas/Content/General/Concepts/regions.htm)不再具有给定[计算形状](https://docs.oracle.com/en-us/iaas/Content/Compute/References/computeshapes.htm)的可用容量。在这种情况下，用户需要等到容量恢复到池中，才能调配额外的计算实例来支持他们的工作负载。很多时候，等待根本不是一个选项:例如，在故障转移到辅助位置时确保容量可用，或者为意外的工作负载高峰提供缓冲容量。

通过向 OKE 添加对容量预留的支持，具有容器化工作负载的用户现在可以高枕无忧，因为容量将在关键事件期间可用。

容量预留在成本优化中也发挥着有益的作用。由于容量预留的成本低于运行实例的成本，因此使用它们来维护上述用例的备份或缓冲容量，比使用始终在线热备盘或额外工作节点更具成本效益。可以随时修改、删除或创建容量预留，以满足不断变化的业务需求。没有最短时间承诺。

# 使用容量预留

用户可以选择使用现有的容量预留或[在计算服务](https://docs.oracle.com/en-us/iaas/Content/Compute/Tasks/reserve-capacity.htm#reservecapacity_managing_capacity_reservations)中创建新的容量预留。这样做之后，用户将匹配的容量预留 ID 作为放置配置中的参数传递到 OKE 节点池中，这也用于控制可用性域和 [OCI 虚拟云网络子网](https://docs.oracle.com/en-us/iaas/Content/Network/Tasks/managingVCNs_topic-Overview_of_VCNs_and_Subnets.htm)中的节点放置。务必确保节点池放置配置中的节点形状和可用性域分别与容量预留的实例类型和可用性域匹配，以确保实例按预期启动。指定容量预留后，将使用关联预留中的计算实例创建添加到节点池的节点。只要有足够的容量，这种情况就会持续下去。

为了创建具有容量预留的节点池，我在节点池的放置配置中包含了容量预留 ID:

```
oci ce node-pool create \
--cluster-id ocid1.cluster.oc1.iad.aaaaaaaaaf______jrd \
--name capacity-reservation-example \
--node-image-id ocid1.image.oc1.iad.aaaaaaaa6______nha \
--compartment-id ocid1.compartment.oc1..aaaaaaaay______t6q \
--kubernetes-version v1.21.5 \
--node-shape VM.Standard2.1 \
--placement-configs "[{\"availabilityDomain\":
\"IqDk:US-ASHBURN-AD-1\", \"capacityReservationId\":
\"ocid1.capacityreservation.oc1.iad.anuwcljt2ah______yeq\", \"subnetId\":
\"ocid1.subnet.oc1.iad.aaaaaaaa2xpk______zva\"}]" \
--size 1 \
--region=us-ashburn-1
```

# 默认预订

Kubernetes 的容器引擎还支持在启动 worker 节点时使用[默认容量预留](https://docs.oracle.com/en-us/iaas/Content/Compute/Tasks/reserve-capacity.htm#default)。默认预留是容量预留，适用于在与预留相关联的租用和可用性域中创建的所有实例。创建默认容量预留后，在该可用性域和租户中启动的所有计算实例(包括 Kubernetes 工作节点)将使用默认容量预留中的容量。

有时，用户可能不想从默认容量预留中调配工作节点。在这些情况下，用户可以在其节点池的放置配置参数中指定一个替代的容量预留，或者选择退出默认预留，并在使用[按需计算容量](https://docs.oracle.com/en-us/iaas/Content/Compute/Concepts/computeoverview.htm#capacity_types)的节点上启动。

# 结论

从预留容量中调配工作节点的能力可确保计算用户和在 Kubernetes 上运行容器化工作负载的用户在最需要的时候拥有足够的资源。不再需要等待其他用户释放按需容量或过度调配热备用工作节点主机来保证容量的可用性。现在，用户有了一种经济高效的方法来确保其集装箱化环境及其支持的业务的连续性。

使用以下资源了解更多信息:

*   访问 [OKE 资源中心](https://www.oracle.com/cloud-native/container-engine-kubernetes/)了解产品详情和评价。
*   从我们的[文档](https://docs.oracle.com/en-us/iaas/Content/ContEng/Concepts/contengoverview.htm)中了解更多关于 OKE 的信息。
*   [自己尝试使用容量预留](https://docs.oracle.com/en-us/iaas/Content/ContEng/Tasks/contengmakingcapacityreservations.htm)。
*   从我们的 [Oracle 云免费层](https://signup.cloud.oracle.com/?language=en&sourceType=:ex:tb:::::RC_WWMK220209P00057:Medium_okeCapacityReservations&SC=:ex:tb:::::RC_WWMK220209P00057:Medium_okeCapacityReservations&pcode=WWMK220209P00057)开始使用 Oracle 云基础架构。

# 加入对话！

如果你对甲骨文开发人员在他们的自然栖息地发生的事情感到好奇，请加入我们的[公共休闲频道](https://join.slack.com/t/oracledevrel/shared_invite/zt-uffjmwh3-ksmv2ii9YxSkc6IpbokL1g?customTrackingParam=:ex:tb:::::Medium_okeCapacityReservations)！我们不介意成为你的鱼缸🐠