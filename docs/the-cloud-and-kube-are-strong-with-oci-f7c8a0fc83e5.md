# 云和 Kube 对 OCI 来说很强大

> 原文：<https://medium.com/oracledevs/the-cloud-and-kube-are-strong-with-oci-f7c8a0fc83e5?source=collection_archive---------0----------------------->

![](img/b59c0794d02e12deb0f2aca64161e312.png)

甲骨文[第四季度财务结果出炉，云收入上升](https://www.oracle.com/news/announcement/q4fy22-earnings-release-2022-06-13/)。一整套[新品最近发布](https://blogs.oracle.com/cloud-infrastructure/category/oci-product-news)，尤其是[更小的 OCI 专用区域，价位更低](https://blogs.oracle.com/cloud-infrastructure/post/dedicated-regions-now-available-with-smaller-footprint-and-lower-price-point)。是啊，我们一直很忙。如果您错过了公告，您可以点播观看:

在所有这些兴奋中，很容易忘记 OCI 最近的云本地开发。别害怕，我会掩护你的。

## 支持将 Oracle Linux 8 用于工作节点

年初，我们为运行 Kubernetes 1.20 及更高版本的集群创建节点池时，增加了使用 Oracle Linux 8 的能力。Oracle Linux 8 支持 [FIPS](https://www.nist.gov/standardsgov/compliance-faqs-federal-information-processing-standards-fips) ，这是一套针对联邦计算机系统的标准和指南。不过，一般来说，Oracle Linux 8 更精简，这意味着它的启动速度更快，因此您的节点池变得更快可用。

## 支持为工作节点定制云初始化脚本

然后，我们添加了在您的节点池中定制云初始化脚本的功能。直到最近，您才能够将 cloud-init 用于普通的计算实例，但是您不能为工作节点定制底层的 cloud-init 脚本。他们在那里，但我们没有向你暴露他们。现在我们有了，您可以自定义节点池来满足您的需求。

您可以使用 cloud-init 脚本来配置额外的 kubelet 参数，配置 SELinux 策略，安装您的组织强制的防病毒和其他安全工具。重要的是，您还可以拥有特定于节点池的脚本。在 [terraform-oci-oke](https://github.com/oracle-terraform-modules/terraform-oci-oke) [模块](https://registry.terraform.io/modules/oracle-terraform-modules/oke/oci/latest)中，我们包含了[一个所有节点池通用的默认脚本](https://github.com/oracle-terraform-modules/terraform-oci-oke/blob/main/modules/oke/cloudinit/worker.template.sh)。它还允许您设置首选时区，如果您在节点池参数中设置了较高的启动卷，它也会自动进行配置。如果您有一个很大的节点池，那么连接到所有节点池并运行 [oci-growfs](https://docs.oracle.com/en-us/iaas/oracle-linux/oci-utils/index.htm#oci-growfs) 来将引导卷扩展到其配置的大小并不有趣。相反，我们使用 cloud-init 来为您完成繁重的工作。

您也可以覆盖默认的通用脚本:

```
cloudinit_nodepool_common = "/tmp/commoncloudinit.sh"
```

或者为每个节点池设置一个特定的脚本:

```
cloudinit_nodepool = {  
  np1 = "/tmp/np1cloudinit.sh"
  np3 = "/tmp/np3cloudinit.sh"
}
```

## 存储增强

随后，我们对 OKE 进行了几项存储增强:

*   添加由 OCI 块存储服务支持的工作节点引导卷和 Kubernetes 持久卷(PVs)的加密。这允许客户通过使用存储在 OCI 保险库中的密钥进行加密来满足他们的合规性和安全性标准。
*   增加由 OCI 文件存储服务(FSS)支持的永久卷(PVs)的官方支持。这是一个漫长的过程。Prasad 的[博客文章](https://blogs.oracle.com/cloud-infrastructure/post/using-file-storage-service-with-container-engine-for-kubernetes)已经为我们服务了一段时间，但它需要为 FSS 手动创建一个存储类。新机制使用 CSI，因此不需要定义新的存储类。您需要做的就是指定驱动程序。此外，您还可以使用它来加密 FSS 传输中的数据和静态数据。

```
apiVersion: v1
kind: PersistentVolume
metadata:
  name: fss-pv
spec:
  capacity:
    storage: 50Gi
  volumeMode: Filesystem
  accessModes:
    - ReadWriteMany
  persistentVolumeReclaimPolicy: Retain
  csi:
    driver: fss.csi.oraclecloud.com
    volumeHandle: ocid1.filesystem.oc1.iad.aaaa______j2xw:10.0.0.6:/FileSystem1
```

## OKE 相关资源的标记

当您使用 OKE 时，会使用和创建大量相关的 OCI 资源:计算实例、负载平衡器、块卷、节点池等。很难跟踪所有这些，更不用说理解它花费了你多少钱。我们现在添加了对标记的 OKE 支持，这意味着您现在可以标记集群使用和创建的资源。有了标签，您就可以在成本分析和预算中使用它们来创建 OCI 使用情况报告。

## 支持容量预留

我们还向 OKE 添加了[容量预留支持](https://blogs.oracle.com/cloud-infrastructure/post/container-engine-kubernetes-capacity-reservations)。这允许您保留虚拟机或裸机容量，以确保这些资源在您需要时可用。

可以在节点池的基础上添加容量预留，因此您可以选择要预留的形状，尤其是当您在集群中运行混合工作负载时。

## 保护您工作负载的安全特性

我的同事 Greg 也[发布了 9 个安全特性](https://blogs.oracle.com/cloud-infrastructure/post/oke-nine-security-features)，你可以很容易地实现它们来改善你的 OKE 工作负载的安全状况。

## 支持扩展数据块存储支持的 PVs

您现在还可以扩展由 OCI 数据块存储服务支持的 PVs 的大小。最重要的是，您可以在不停机的情况下完成这项工作。例如，假设您创建了一个 100GB 大小的 PV:

```
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
 name: my-pvc
spec:
 storageClassName: oci-bv
 resources:
  requests:
    storage: **100Gi**
 volumeName: pvc-bv1
```

现在你想把它的大小翻倍。您可以更改清单并再次应用它:

```
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
 name: my-pvc
spec:
 storageClassName: oci-bv
 resources:
  requests:
    storage: **200Gi**
 volumeName: pvc-bv1 
```

## 支持网络负载平衡器

OCI [去年推出了灵活的网络负载平衡器](https://blogs.oracle.com/cloud-infrastructure/post/announcing-oracle-cloud-infrastructure-flexible-network-load-balancer)，这是一种非代理负载平衡器，可执行第 3 层和第 4 层(TCP、UDP/ICMP)负载的直通负载平衡。在 NLB 的支持下，你可以在 OKE 上公开需要 UDP 的服务，如 VoIP、实时视频流、在线游戏和物联网等。

## OCI 集群 API，又名卡波奇

我们还开源了 [CAPOCI](https://oracle.github.io/cluster-api-provider-oci/) ，一个用于 OCI 的[集群 API](https://github.com/kubernetes-sigs/cluster-api) 实现。Cluster API 是 Kubernetes 的一个子项目，它提供 API 和工具来简化使用不同基础设施提供者的多个 Kubernetes 集群的供应、升级和管理。通过使用一个通用的 API，Cluster API 使得在许多基础设施提供商之间管理 Kubernetes 集群的生命周期的体验变得一致。

集群 API 真的很酷。到目前为止，如果你想创建一个新的自我管理的 Kubernetes 集群，你必须使用 Terraform 或者新的闪亮的 Pulumi OCI 提供者 T1，一些 bash 脚本。使用集群 API，您可以在管理集群中运行 CAPOCI，管理集群可以是任何符合 Kubernetes 的集群。使用管理集群，您可以提供一个自我管理的 Kubernetes 集群，而不必手动创建计算、脚本等。您可以在 OKE 上运行管理集群来创建自我管理的集群，或者如果您有更复杂的场景，例如混合/多云，也可以在其他基础设施提供商上创建 Kubernetes 集群。该项目目前正在 [GitHub/oracle](https://github.com/oracle/cluster-api-provider-oci) 上开发和维护，但我们希望尽快将其转移到 [kubernetes-sig](https://github.com/kubernetes-sigs/) 组织。

## 私有 OKE 集群的安全部署

众所周知，OKE 既可以部署在公共集群中，也可以部署在私有集群中。公共集群的 API 服务器可以公开访问，而私有集群的 API 服务器只能在 VCN 内访问，或者只能访问 VCN 通过 VPN、FastConnect 或星型模型扩展到的网络。

直到最近，如果您使用 DevOps 服务，您将需要使用公共集群，但这已经是过去的事情了。现在，您也可以将 [DevOps 与私有集群](https://blogs.oracle.com/cloud-infrastructure/post/oci-devops-secure-deployments-to-private-kubernetes-clusters)一起使用。这意味着您的代码、构建和部署永远不必通过公共互联网，从而增强了部署过程的安全性。

## OCI 服务网

![](img/b75fc7ed49ac78d7fef864f3cc017aa7.png)

Source: National History Museum

服务网正在经历某种寒武纪大爆发。CNCF 自己就有 5 个，这还是在 Istio 被接受之前。最近，我们还[宣布了 OCI 服务网格](https://blogs.oracle.com/cloud-infrastructure/post/announcing-oci-service-mesh-general-availability)。它支持在 OKE 上运行的任何应用程序。我还没有尝试过，所以我不会再详细说明了。

## OKE 的负载平衡器节点选择器

就在几天前，我的同事 Ajay [做了一个更好的工作来解释它](https://blogs.oracle.com/cloud-infrastructure/post/announcing-load-balancer-node-label-selector)和它的好处。

## 优化的工人图像

这也是最近才发布的。在调配节点池时，您可以根据所需的 CPU 体系结构(x86_64、aarch64、x86_64/CUDA)选择 Oracle Linux 映像，或者选择基于自定义映像的映像。在幕后，Kubernetes 包将从头开始安装，一旦这个过程完成，那么工人节点将加入集群。不幸的是，调配新的节点池也需要很长的时间，这令人恼火。

通过优化的工作映像，Kubernetes 包被预安装。这意味着工作节点可以跳过 Kubernetes 安装，并在完成引导和 Kubernetes 进程启动后加入集群。这大大缩短了节点池资源调配时间。从 [4.2.4 版本](https://github.com/oracle-terraform-modules/terraform-oci-oke/releases/tag/v4.2.4)开始，我们已经在 [terraform-oci-oke](https://github.com/oracle-terraform-modules/terraform-oci-oke) 模块中支持该功能。

## 块卷性能级别

现在，您还可以控制块卷的性能级别。默认情况下，它们被配置为平衡级别(10 个 vpu)。通过使用这个特性，您可以配置运行在更高性能级别的 PVC。首先，定义一个存储类。你会注意到它使用了 CSI 卷插件:

```
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: oci-high
provisioner: blockvolume.csi.oraclecloud.com
parameters:
  **vpusPerGB: "20"**
reclaimPolicy: Delete
volumeBindingMode: WaitForFirstConsumer
allowVolumeExpansion: true
```

请注意，在参数部分，我们指定了更高的 vpu(卷性能单位)。您可以指定高达[高性能](https://docs.oracle.com/en-us/iaas/Content/Block/Concepts/blockvolumeperformance.htm)的 vpu，即 20 个。

创建新的存储类后，您可以创建新的 PVC:

```
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: oci-pvc-high
spec:
  **storageClassName: oci-high**
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 5Gi
```

VPUs 是一种通过增加 IOPS/GB 来提高数据块卷性能的方法。您也可以选择购买更少的 vpu，这不仅会降低性能，还会降低成本。因此，这是一种权衡。通过增加配置 vpu 的能力，您现在可以根据您的应用需求和预算有意识地自己做出选择。

## 摘要

这些是我们已经公开提供的功能。更多令人兴奋的事情即将到来，我们会尽快与您分享。

同时，加入我们的公共休闲活动吧！