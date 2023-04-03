# Oracle Container Engine for Kubernetes(OKE)支持自定义工作节点启动脚本

> 原文：<https://medium.com/oracledevs/oci-container-engine-for-kubernetes-oke-supports-custom-worker-node-start-up-scripts-78e717ffe0f8?source=collection_archive---------1----------------------->

![](img/ae00341a31548169e76d7e19c1086b7e.png)

Use start up scripts to tailor-make worker nodes that match your workloads

# 介绍

[用于 Kubernetes 的 Oracle 容器引擎(OKE)](https://docs.oracle.com/en-us/iaas/Content/ContEng/home.htm#top) 现在支持使用启动脚本定制 [Kubernetes 工作节点](https://kubernetes.io/docs/concepts/architecture/nodes/)。该功能使用户能够更好地控制运行 Kubernetes 工作负载的 worker 节点主机的配置。

# 背景

Cloud-init 是初始化和配置云实例的行业标准方法。它受到所有主要公共云提供商、私有云基础架构的配置系统和裸机安装的支持。有关云初始化的更多信息，请参见[云初始化文档](https://cloudinit.readthedocs.io/en/latest/)。

OKE 在每个 [Oracle 云基础设施(OCI)计算实例](https://docs.oracle.com/en-us/iaas/Content/Compute/home.htm)上安装一个默认的 cloud-init 启动脚本，用于将该实例配置为 Kubernetes 工作节点。当实例第一次启动时，cloud-init 脚本运行。默认启动脚本包含以下逻辑:

```
#!/bin/bash
curl --fail -H "Authorization: Bearer Oracle" -L0 [http://169.254.169.254/](http://169.254.169.254/)
opc/v2/instance/metadata/oke_init_script | base64 --decode >/var/run/oke-
init.sh
bash /var/run/oke-init.sh
```

OKE 设置的默认主机配置不一定匹配每个用户的用例，这可能导致用户需要对工作节点进行自己的定制。以前，用户可以通过 SSH 连接到工作节点，进行更改，然后将这些更改保存为自定义映像，用作未来工作节点的模板，从而一次自定义一个工作节点或按比例自定义工作节点。然而，这种方法的运营成本很高:每当发布新的映像时，用户都需要创建新的自定义映像。相比之下，支持自定义 cloud-init 使用户能够编写一次自定义脚本，并将其应用于节点池中创建的所有未来工作节点。

# 用例

用户可以在 OKE 逻辑运行之前或之后，通过向脚本添加自己的逻辑来自定义默认的启动脚本。这为用户提供了执行某些需要在运行 OKE 脚本之前运行的动作的灵活性。例如，这可以用于配置内部 yum 存储库或设置公司代理。

定制默认启动脚本支持大量用例。以下是一些例子:

*   使用 [kubelet-extra-args](https://kubernetes.io/docs/reference/command-line-tools-reference/kubelet/) 来配置 [kubelet 污点](https://kubernetes.io/docs/concepts/scheduling-eviction/taint-and-toleration/)以控制 pods 被调度到哪些节点上。
*   出于安全性和合规性的目的，在所有工作节点主机上配置一个 [SELinux](https://selinuxproject.org/page/Main_Page) 策略。
*   在启动时取消分配实例的临时公共 IP，而是为实例重新分配保留的公共 IP。
*   配置公司代理。
*   配置自定义 yum 代理。
*   安装强制性防病毒软件和其他安全工具

用户可以在创建新群集、创建新节点池和修改现有节点池时自定义默认启动脚本。这可以在 OKE 控制台和[中使用 CLI、SDK、API 和其他界面](https://docs.oracle.com/en-us/iaas/Content/ContEng/Tasks/contengusingcustomcloudinitscripts.htm#contengusingcustomcloudinitscripts_topic_Using_the_CLI)完成[。自定义启动脚本在托管工作节点的实例启动时运行。在定制了默认的启动脚本之后，最好运行 Node Doctor 脚本来确认新启动的实例上的工作节点是否按预期工作。更多信息，请参考使用节点诊断脚本](https://docs.oracle.com/en-us/iaas/Content/ContEng/Tasks/contengusingcustomcloudinitscripts.htm#contengusingcustomcloudinitscripts_topic_Using_the_Console)的[节点故障排除。](https://docs.oracle.com/en-us/iaas/Content/ContEng/Tasks/contengtroubleshooting_topic-node_troubleshooting.htm)

# 使用定制的 Cloud-init 脚本来设置 kubelet-extra-args

您可以使用一个定制的 cloud-init 脚本在工作节点上的 kubelet(主节点代理)上配置许多额外的选项。这些额外的选项有时被称为 kubelet-extra-args。一个这样的 kubelet- extra-args 选项是配置调试级别日志详细程度的选项。

要配置调试级别日志详细程度，请使用以下云初始化脚本:

```
#!/bin/bash
curl --fail -H "Authorization: Bearer Oracle" -L0 [http://169.254.169.254/](http://169.254.169.254/)
opc/v2/instance/metadata/oke_init_script | base64 --decode >/var/run/oke-
init.sh
bash /var/run/oke-init.sh --kubelet-extra-args "--v=4"
```

要确认调试级别日志详细程度的设置，请连接到一个 worker 节点并使用 sudo systemctl status -l kubelet 命令。当上面的 cloud-init 脚本在 worker 节点上运行时，sudo systemctl status -l kubelet 命令将详细级别返回为 4。kubelet 日志还包含更多细节。

# 使用定制的云初始化脚本来配置 SELinux(安全性增强的 Linux)

SELinux 是对 Linux 的安全增强，使管理员能够根据策略中的规则限制哪些用户和应用程序可以访问哪些资源。SELinux 还为访问控制增加了更精细的粒度。用户可以使用定制的 cloud-init 脚本在 worker 节点上配置 SELinux。

SELinux 可以处于两种状态之一，启用或禁用。启用后，SELinux 可以以两种模式之一运行，强制模式或许可模式。默认情况下，SELinux 在 OKE 工作节点上是启用的，并设置为在许可模式下运行。在许可模式下运行时，SELinux 不强制执行访问规则，只执行日志记录。

要实施访问规则，请将 SELinux 设置为在实施模式下运行。在强制模式下运行时，SELinux 会阻止违反策略的操作，并在审计日志中记录相应的事件。

要将 SELinux 设置为在强制模式下运行，请使用以下 cloud-init 脚本:

```
#!/bin/bash
curl --fail -H "Authorization: Bearer Oracle" -L0 [http://169.254.169.254/](http://169.254.169.254/)
opc/v2/instance/metadata/oke_init_script | base64 --decode >/var/run/oke-
init.sh
bash /var/run/oke-init.sh
setenforce 1
sed -i 's/^SELINUX=.*/SELINUX=enforcing/' /etc/selinux/config
```

要确认工作节点上运行的 SELinux 的状态和模式，请连接到工作节点并使用`getenforce`命令。当上面的 cloud-init 脚本已经在 worker 节点上运行时，`getenforce`命令返回`Enforcing`。

# 结论

使用启动脚本定制工作节点使高级用户能够定制符合其用例的工作节点。用户不再需要决定是使用默认节点配置还是接受手动管理自定义映像的额外管理开销。现在，用户可以在 OKE 脚本运行之前或之后简单地添加一个定制脚本，以便在他们认为合适的时候修改他们的 worker 节点。

# 想了解更多？

要了解更多信息或亲自动手，请使用以下资源:

*   访问 [OKE 资源中心](https://www.oracle.com/cloud-native/container-engine-kubernetes/)了解产品详情和客户评价。
*   从我们的[文档](https://docs.oracle.com/en-us/iaas/Content/ContEng/Concepts/contengoverview.htm)中了解更多关于 OKE 的信息。
*   自己使用定制的 worker 节点启动脚本来尝试[。](https://docs.oracle.com/en-us/iaas/Content/ContEng/Tasks/contengusingcustomcloudinitscripts.htm)
*   从我们的 [Oracle 云免费层](https://www.oracle.com/cloud/free/)开始使用 Oracle 云基础架构。