# 用于 Kubernetes 和注册表 FedRAMP 授权的 Oracle 容器引擎

> 原文：<https://medium.com/oracledevs/oracle-container-engine-for-kubernetes-and-registry-fedramp-authorization-309a32b8c8f8?source=collection_archive---------7----------------------->

![](img/8f74f6e2ad5cd35bc87b28a46a6f30b3.png)

我们很高兴地宣布，自 2020 年 12 月起[Oracle Container Engine for Kubernetes(OKE)](https://docs.oracle.com/en-us/iaas/Content/ContEng/Concepts/contengoverview.htm)和[Oracle Cloud infra structure Registry(OCIR)](https://docs.oracle.com/en-us/iaas/Content/Registry/Concepts/registryoverview.htm#Overview_of_Registry)已被添加到 FedRAMP 高级授权[Oracle Cloud infra structure(OCI)政府数据区域](https://www.oracle.com/cloud/data-regions.html#government)。这些服务已获得美国联邦风险和授权管理计划(FedRAMP)临时运营授权(P-ATOs)和 FedRAMP 定义的运营授权(ATOs)。

# 联邦风险和授权管理计划(FedRAMP)

FedRAMP 是一项美国政府范围内的计划，它提供了一种标准的方法来对云产品和服务进行安全评估、授权和持续监控。FedRAMP 有三个影响级别的授权:低、中、高。订阅这些政府区域的客户现在可以在托管的 Kubernetes 集群上运行需要 FedRAMP 高级认证的受监管的集装箱化工作负载。这种授权也可以用作其他受监管客户的安全基准，如金融机构、医疗机构和制造业。有关更多信息，请参考[甲骨文的联邦风险和授权管理计划第](https://www.oracle.com/industries/public-sector/federal/fedramp.html)页。

# 容器引擎和容器注册表

Oracle Container Engine for Kubernetes 是基于开源 Kubernetes 的 Oracle 托管容器编排服务，可以减少构建现代云原生应用程序的时间和成本。该服务包括自动化容器化应用程序的部署、扩展和管理的能力。OCI 集装箱注册中心是一个基于开放标准、由 Oracle 管理的 Docker 注册服务，用于安全地存储和共享集装箱图像。开发人员和 IT 团队可以一起使用这些服务来支持容器化的应用程序生命周期。

# 安全地管理和部署容器

针对 Kubernetes 和 OCI 集装箱登记处的 Oracle Container Engine 的 FedRAMP 高认证确保了物理和虚拟安全、维护、工具、人员和流程均符合当前和持续的合规标准。订阅 OCI 政府数据区的公共部门客户和其他受监管客户现在可以[从他们自己的受管映像注册中心](https://docs.oracle.com/en-us/iaas/Content/Registry/Tasks/registrypushingimagesusingthedockercli.htm)推拉 Docker 映像，并安全地[将这些映像部署到 Kubernetes 集群](https://docs.oracle.com/en-us/iaas/Content/ContEng/Tasks/contengcreatingclusterusingoke.htm)。

要了解更多关于 Oracle 云基础架构授权和合规性的信息，请访问 [Oracle 云合规性](http://www.oracle.com/cloud/cloud-infrastructure-compliance/)。

# 参考

*   有关 Kubernetes(OKE)OCI 集装箱发动机的更多信息，请查看我们的[产品详情](https://www.oracle.com/cloud-native/container-engine-kubernetes/)和[产品文档](https://docs.oracle.com/en-us/iaas/Content/ContEng/Concepts/contengoverview.htm)页面。
*   了解更多关于甲骨文的[云本地服务](https://www.oracle.com/cloud-native/)。
*   [甲骨文云基础设施容器注册中心](https://www.oracle.com/cloud-native/container-registry/)
*   [PAA 和 IAA 的数据中心区域](https://www.oracle.com/cloud/data-regions.html#government)
*   [甲骨文 FedRAMP 云解决方案](https://www.oracle.com/industries/public-sector/federal/fedramp.html)
*   [尝试甲骨文始终免费云服务，享受 30 天试用期](https://www.oracle.com/cloud/free/?source=:ow:o:s:feb::AppsCloudHero&intcmp=:ow:o:s:feb::AppsCloudHero)

最初发表在 blogs . Oracle . com:[https://blogs . Oracle . com/cloud-基建/container-engine-registry-fedramp](https://blogs.oracle.com/cloud-infrastructure/container-engine-registry-fedramp)