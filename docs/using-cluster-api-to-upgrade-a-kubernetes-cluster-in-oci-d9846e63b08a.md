# 使用集群 API 升级 OCI 的 Kubernetes 集群

> 原文：<https://medium.com/oracledevs/using-cluster-api-to-upgrade-a-kubernetes-cluster-in-oci-d9846e63b08a?source=collection_archive---------0----------------------->

![](img/2755fb9ccfa0d8a6edf853ebf7a28337.png)

这次有点不寻常的帖子。我代表我的同事和队友 Joe 发这个帖子。显然，他更喜欢我来处理剪辑。我不知道为什么，但他是一个伟大的队友，有着邪恶的幽默感，我从他身上学到了很多，所以我很乐意帮忙。我们开始吧:

在这篇文章中，我将介绍[集群 API](https://cluster-api.sigs.k8s.io/) 的一个非常棒的特性:对 Kubernetes 集群进行滚动升级的能力。集群 API 使其成为一个简单且可重复的过程。我会完全诚实:我曾经手动升级过一个 Kubernetes 集群，而且[天没有塌下来](https://asterixonline.info/vitalstatistix/)。但是我是一个懒惰的黑客，所以当我可以自动操作并具有可重复性的安全性时，为什么还要手动操作呢？

## 什么是集群 API？

如果您不熟悉集群 API，它是 Kubernetes 的一个子项目，主要提供声明性 API 和工具来简化多个 Kubernetes 集群的供应、升级和操作。打个比方，把集群 API 想象成 Java 接口，它使用 Kubernetes 风格的接口来定义 Kubernetes 集群所需的基础设施。

坚持我们的 Java 类比，为了使用所说的接口，你需要在一个类中实现它。Cluster API 使用一个基础设施模型来扩展对多个基础设施提供者的支持。几乎每个基础设施提供商*都实现了*(我希望你明白这里的双关语！)一。基础设施提供者的实现是将 API 集群化，就像类对于 Java 一样。Oracle 的在这里[实现](https://github.com/oracle/cluster-api-provider-oci)，它可以用于在 Oracle 云中构建集群。有关如何从 OCI 提供商开始的简要介绍，请查看 [Paul Jenkins 的帖子](https://blogs.oracle.com/cloud-infrastructure/post/create-and-manage-kubernetes-clusters-on-oracle-cloud-infrastructure-with-cluster-api)或阅读[文档](https://oracle.github.io/cluster-api-provider-oci/gs/overview.html)。您还应该检查集群 API 的 [awesome book](https://cluster-api.sigs.k8s.io/) ，使用 [mdbook](https://rust-lang.github.io/mdBook/) 从 Markdown 生成。

## 使用集群 API 升级集群

集群 API 的主要目标之一是

> 使用声明性 API 管理符合 Kubernetes 的集群的生命周期(创建、扩展、升级、销毁)。

自动化升级过程是一大成就。我不想必须封锁/清空节点来手动进行滚动更新。工具应该为我做这件事。

我假设您已经启动并运行了一个管理和工作负载集群。如果没有，请按照[入门指南](https://oracle.github.io/cluster-api-provider-oci/gs/gs.html)创建工作负载集群。下面是我如何创建工作负载集群的:

```
OCI_IMAGE_ID=<your image ocid> \  
OCI_COMPARTMENT_ID=<your compartment ocid> \  
NODE_MACHINE_COUNT=3 \  
OCI_REGION=us-phoenix-1 \  
OCI_SHAPE=VM.Standard.E4.Flex \  
OCI_NODE_MACHINE_TYPE_OCPUS=2 \  
OCI_CONTROL_PLANE_MACHINE_TYPE_OCPUS=3 \  
OCI_SHAPE_MEMORY_IN_GBS= \  
OCI_SSH_KEY=<your private key> \  
clusterctl generate cluster oci-cluster-phx --kubernetes-version v1.22.9 \  
--target-namespace default \  
--control-plane-machine-count=3 \  
--from https://github.com/oracle/cluster-api-provider-oci/releases/download/v0.3.0/cluster-template.yaml | kubectl apply -f -
```

既然我们已经启动并运行了一个工作负载集群，那么是时候升级了。升级过程的高级步骤如下:

1.  创建新版本的 Kubernetes 图像
2.  升级控制平面
3.  升级工人机器

在开始之前，让我们检查一下正在运行的工作负载集群的版本。为了访问我们的工作负载集群，我们需要从我们的管理集群中导出 Kubernetes 配置:

```
clusterctl get kubeconfig oci-cluster-phx -n default > oci-cluster-phx.kubeconfig
```

一旦我们有了`kubeconfig`文件，我们就可以检查工作负载集群的版本:

```
kubectl --kubeconfig=oci-cluster-phx.kubeconfig versionClient Version: version.Info{Major:"1", Minor:"23", GitVersion:"v1.23.4", GitCommit:"e6c093d87ea4cbb530a7b2ae91e54c0842d8308a", GitTreeState:"clean", BuildDate:"2022-02-16T12:38:05Z", GoVersion:"go1.17.7", Compiler:"gc", Platform:"darwin/amd64"}
Server Version: version.Info{Major:"1", Minor:"22", GitVersion:"v1.22.9", GitCommit:"6df4433e288edc9c40c2e344eb336f63fad45cd2", GitTreeState:"clean", BuildDate:"2022-04-13T19:52:02Z", GoVersion:"go1.16.15", Compiler:"gc", Platform:"linux/amd64"}
```

注意服务器版本是`v1.22.9`。让我们加速吧。

## 创建新的 Kubernetes 形象

为了升级我们的 worker 节点，我们需要使用 [Kubernetes 映像构建器](https://image-builder.sigs.k8s.io/capi/providers/oci.html)来构建映像。按照[构建图像](https://image-builder.sigs.k8s.io/capi/providers/oci.html#building-images)一节中更详细的步骤完成先决步骤。

然后，我们需要将`kubernetes`信息设置为比当前集群版本更新的版本。现在，正在使用的集群是`1.22.9`，我们想要升级到`1.23.6`(当前发布版本可以在这里找到[https://kubernetes.io/releases/](https://kubernetes.io/releases/))。我们将编辑`images/capi/packer/config/kubernetes.json`并更改以下内容:

```
"kubernetes_deb_version": "1.23.6-00",
"kubernetes_rpm_version": "1.23.6-0",
"kubernetes_semver": "v1.23.6",
"kubernetes_series": "v1.23"
```

在配置更新后，我们将使用 Ubuntu 20.04 构建来创建带有 packer 的新映像:

```
$ cd <root_of_image_builder_repo>/images/capi
$ PACKER_VAR_FILES=json/oci_phx.json make build-oci-ubuntu-2004
```

这将在 OCI 启动一个实例来构建映像。一旦完成，你应该得到图像的 OCID 输出。您也可以通过访问`https://console.us-phoenix-1.oraclecloud.com/compute/images`来检查构建的图像。你会想要保存这个 OCID，因为我们以后会用到它。下面是一个成功输出的截断示例:

```
==> oracle-oci: Creating temporary ssh key for instance...
==> oracle-oci: Creating instance...
==> oracle-oci: Created instance (<your instance OCID will be here>).
==> oracle-oci: Waiting for instance to enter 'RUNNING' state...
==> oracle-oci: Instance 'RUNNING'.
...
...
...
==> oracle-oci: Creating image from instance...
==> oracle-oci: Image created.
==> oracle-oci: Terminating instance (<your instance OCID will be here)...
==> oracle-oci: Terminated instance.
Build 'oracle-oci' finished after 15 minutes 29 seconds.==> Wait completed after 15 minutes 29 seconds==> Builds finished. The artifacts of successful builds are:
--> oracle-oci: An image was created: 'cluster-api-ubuntu-2004-1.23-v1.23.6-1653421889' (OCID: <you image OCID will be here>) in region 'us-phoenix-1'
```

## 升级控制平面

现在我们已经有了新版本 Kubernetes 的计算映像，我们可以升级控制平面了。

首先，让我们为控制平面复制一个机器模板:

```
$ kubectl get ocimachinetemplate oci-cluster-phx-control-plane -o yaml > control-plane-machine-template.yaml
```

我们需要修改以下内容:

*   `spec.template.spec.imageId` -使用之前创建的自定义图像 OCID
*   `metadata.name`——有了新名字。例子:`oci-cluster-phx-control-plane-v1-23-6`
*   在尝试将元数据发送到 API 服务器之前，清除所有无关的元数据，包括`resourceVersion`字段

修改字段后，我们可以将它们应用到集群:

```
$ kubectl apply -f control-plane-machine-template.yamlocimachinetemplate.infrastructure.cluster.x-k8s.io/oci-cluster-phx-control-plane configured
```

请注意，这只会创建新的机器模板。下一步将触发实际的更新。

我们现在想告诉`KubeadmControlPlane`资源关于新的机器模板并升级版本号。

```
$ kubectl get KubeadmControlPlane -o yaml > kubeadm-control-plan-update.yaml
```

我们需要为`oci-cluster-phx-control-plane`修改以下内容:

*   `spec.machineTemplate.infrastructureRef.name` -在此使用新创建的机器模板名称。示例`oci-cluster-phx-control-plane-v1-23-6`
*   `spec.version` -应该是`v1.22.9`你会想换成`v1.23.6`

升级控制平面:

```
$ kubectl apply -f kubeadm-control-plan-update.yaml
```

在`capi-kubeadm-control-plane-controller-manager`中，您应该会看到类似如下的日志:

```
$ kubectl -n capi-kubeadm-control-plane-system logs capi-kubeadm-control-plane-controller-manager-75cc644b4b-pkghd...
...
controller/kubeadmcontrolplane "msg"="Rolling out Control Plane machines" "cluster"="oci-cluster-phx" "name"="oci-cluster-phx-control-plane" "namespace"="default" "reconciler group"="controlplane.cluster.x-k8s.io" "reconciler kind"="KubeadmControlPlane" "needRollout"=["oci-cluster-phx-control-plane-74bkb","oci-cluster-phx-control-plane-8h5zw","oci-cluster-phx-control-plane-pcbjq"]
...
..
```

我们可以通过`clusterctl`查看集群升级的进度:

```
$ clusterctl describe cluster oci-cluster-phx                                                                                                                   
NAME                                                                READY  SEVERITY  REASON                   SINCE  MESSAGE
Cluster/oci-cluster-phx                                             False  Warning   RollingUpdateInProgress  98s    Rolling 3 replicas with outdated spec (1 replicas up to date)
├─ClusterInfrastructure - OCICluster/oci-cluster-phx                True                                      4h50m
├─ControlPlane - KubeadmControlPlane/oci-cluster-phx-control-plane  False  Warning   RollingUpdateInProgress  98s    Rolling 3 replicas with outdated spec (1 replicas up to date)
│ └─4 Machines...                                                   True                                      9m17s  See oci-cluster-phx-control-plane-ptg4m, oci-cluster-phx-control-plane-sg67j, ...
└─Workers
  └─MachineDeployment/oci-cluster-phx-md-0                          True                                      10m
    └─3 Machines...                                                 True                                      4h44m  See oci-cluster-phx-md-0-8667c8d69-47nh9, oci-cluster-phx-md-0-8667c8d69-5r4zc, ...
```

我们还可以看到，随着新实例的创建，滚动更新开始发生:

```
$ kubectl --kubeconfig=oci-cluster-phx.kubeconfig get nodes -ANAME                                  STATUS     ROLES                  AGE     VERSION
oci-cluster-phx-control-plane-464zs   Ready      control-plane,master   4h40m   v1.22.5
oci-cluster-phx-control-plane-7vdxp   NotReady   control-plane,master   27s     v1.23.6
oci-cluster-phx-control-plane-dhxml   Ready      control-plane,master   4h48m   v1.22.5
oci-cluster-phx-control-plane-dmk8j   Ready      control-plane,master   4h44m   v1.22.5
oci-cluster-phx-md-0-cnrbf            Ready      <none>                 4h44m   v1.22.5
oci-cluster-phx-md-0-hc6fj            Ready      <none>                 4h45m   v1.22.5
oci-cluster-phx-md-0-nc2g9            Ready      <none>                 4h44m   v1.22.5
```

在控制平面实例被终止之前，它将按预期被封锁和排空:

```
oci-cluster-phx-control-plane-dmk8j   NotReady,SchedulingDisabled   control-plane,master   4h52m   v1.22.5
```

这个过程大约需要 15 分钟。所有控制平面节点升级后，您应该会看到使用`kubectl version`的新版本:

```
kubectl --kubeconfig=oci-cluster-phx.kubeconfig versionClient Version: version.Info{Major:"1", Minor:"23", GitVersion:"v1.23.4", GitCommit:"e6c093d87ea4cbb530a7b2ae91e54c0842d8308a", GitTreeState:"clean", BuildDate:"2022-02-16T12:38:05Z", GoVersion:"go1.17.7", Compiler:"gc", Platform:"darwin/amd64"}Server Version: version.Info{Major:"1", Minor:"23", GitVersion:"**v1.23.6**", GitCommit:"ad3338546da947756e8a88aa6822e9c11e7eac22", GitTreeState:"clean", BuildDate:"2022-04-14T08:43:11Z", GoVersion:"go1.17.9", Compiler:"gc", Platform:"linux/amd64"}
```

我们的集群现在已经升级，我们可以继续升级工作节点。

## 升级工作节点

升级控制平面节点后，我们现在可以升级工作节点[https://cluster-API . sigs . k8s . io/tasks/updating-machine-templates . html](https://cluster-api.sigs.k8s.io/tasks/updating-machine-templates.html)

首先，我们需要为 worker 节点复制机器模板:

```
$ kubectl get ocimachinetemplate oci-cluster-phx-md-0 -o yaml > worker-machine-template.yaml
```

我们首先修改以下内容:

*   `spec.template.spec.imageId` -使用之前创建的自定义图像 OCID
*   `metadata.name`——有了新名字。示例:`oci-cluster-phx-md-0-v1-23-6`

一旦修改了字段，我们需要将它们应用到集群中。和以前一样，这只会创建新的机器模板。下一步将开始实际的更新。

```
$ kubectl apply -f worker-machine-template.yamlocimachinetemplate.infrastructure.cluster.x-k8s.io/oci-cluster-phx-md-0-v1-23-6 created
```

让我们用刚刚创建的新资源修改 worker 节点的`MachineDeployment`。

```
$ kubectl get MachineDeployment -o yaml > worker-machine-deployment.yaml
```

我们需要为`oci-cluster-phx-md-0`修改以下内容:

*   `spec.template.spec.infrastructureRef.name` -在此使用新创建的机器模板名称。示例`oci-cluster-phx-md-0-v1-23-6`
*   `spec.template.spec` -应该是`v1.22.9`你会想换成`v1.23.6`
*   在试图发送给 API 服务器之前，清除任何无关的元数据，包括`resourceVersion`字段

升级工作节点:

```
$ kubectl apply -f worker-machine-deployment.yaml
```

同样，我们可以通过`clusterctl`命令来观察集群的进度。然而，与控制平面不同，机器部署处理工作机的更新，因此`clusterctl describe cluster`将只显示正在更新的机器部署。如果希望在创建新实例时观察滚动更新的发生，可以执行以下操作:

```
$ kubectl --kubeconfig=oci-cluster-phx.kubeconfig get nodes -A...oci-cluster-phx-md-0-z59t8                   Ready,SchedulingDisabled   <none>                 55m    v1.22.5oci-cluster-phx-md-0-z59t8                   NotReady,SchedulingDisabled   <none>                 56m     v1.22.5
```

如果您在工作机器上有 pod，您将看到它们被迁移到新机器上:

```
$ kubectl --kubeconfig=oci-cluster-phx.kubeconfig get podsNAME                          READY   STATUS       AGE     NODE
echoserver-55587b4c46-2q5hz   1/1     Terminating  89m    oci-cluster-phx-md-0-z59t8
echoserver-55587b4c46-4x72p   1/1     Running      5m24s  oci-cluster-phx-md-0-v1-23-6-bqs8l
echoserver-55587b4c46-tmj4b   1/1     Running      29s    oci-cluster-phx-md-0-v1-23-6-btjzs
echoserver-55587b4c46-vz7gm   1/1     Running      89m    oci-cluster-phx-md-0-z79bd
```

在我们的示例中，大约 10 或 15 分钟后，工人应该得到更新。显然，节点越多，滚动更新的时间就越长。您可以检查所有节点的版本以确认:

```
$ kubectl --kubeconfig=oci-cluster-phx.kubeconfig get nodes -ANAME                                          STATUS                     ROLES                  AGE     VERSION
oci-cluster-phx-control-plane-v1-23-6-926gx   Ready                      control-plane,master   18m     v1.23.6
oci-cluster-phx-control-plane-v1-23-6-vfp5g   Ready                      control-plane,master   24m     v1.23.6
oci-cluster-phx-control-plane-v1-23-6-vprqc   Ready                      control-plane,master   30m     v1.23.6
oci-cluster-phx-md-0-v1-23-6-bqs8l            Ready                      <none>                 9m58s   v1.23.6
oci-cluster-phx-md-0-v1-23-6-btjzs            Ready                      <none>                 5m37s   v1.23.6
oci-cluster-phx-md-0-v1-23-6-z79bd            Ready                      <none>                 71s     v1.23.6
```

## 机器部署策略

集群 API 提供了两种机器部署策略`RollingUpdate`和`OnDelete`。

我们上面的例子使用了`RollingUpdate`策略，您可以用它来修改`maxSurge`和`maxUnavailable`。`maxSurge`和`maxUnavailable`都可以是一个绝对数字(如 5)或所需机器的百分比(如 10%)。

另一个机器部署策略选项是`OnDelete`。这要求用户完全删除旧机器来驱动更新。当机器被完全删除后，一个新的会出现。

为了更好地理解带有集群 API 的机器部署是如何工作的，请查看关于机器部署的文档。

# 结论

我们创建了一个新映像，并将滚动升级推送到我们集群的控制平面和工作节点。我们通过对我们的配置进行一些修改实现了这一点。无论集群大小如何，升级过程都是相同的。

如果这不是集群 API 的卖点，我不知道还有什么。

随着许多新特性的出现，集群 API 项目正在快速发展。OCI 集群 API 提供商团队正在努力为您带来所有最新最棒的产品，如`[ClusterClass](https://cluster-api.sigs.k8s.io/tasks/experimental-features/cluster-class/index.html)`、`[MachinePools](https://cluster-api.sigs.k8s.io/tasks/experimental-features/machine-pools.html)`和`[ManagedClusters](https://cluster-api.sigs.k8s.io/tasks/experimental-features/cluster-class/operate-cluster.html)`。

关于[集群-api-provider-oci](https://github.com/oracle/cluster-api-provider-oci) 的更新，请关注 GitHub repo。我们很高兴能为 Kubernetes 和 CNCF 社区做出贡献，希望你能加入我们。

您也可以[加入我们的公共休闲](https://bit.ly/devrel_slack)讨论！

## 相关资源

集群 API:[https://cluster-api.sigs.k8s.io/](https://cluster-api.sigs.k8s.io/)

https://oracle.github.io/cluster-api-provider-oci/ OCI 集群 API

GitHub 上的卡波奇:[https://github.com/oracle/cluster-api-provider-oci](https://github.com/oracle/cluster-api-provider-oci)

松弛的卡波奇:[https://kubernetes.slack.com/archives/C039CDHABFF](https://kubernetes.slack.com/archives/C039CDHABFF)

*照片由* [*梅尔普尔*](https://unsplash.com/@melpoole?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) *上*[*Unsplash*](https://unsplash.com/s/photos/cluster?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)