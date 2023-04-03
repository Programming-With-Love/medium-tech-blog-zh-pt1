# 将 Docker 和 Kubernetes 安装在 Oracle Linux 上的代理后面

> 原文：<https://medium.com/oracledevs/install-docker-and-kubernetes-on-oracle-linux-behind-a-proxy-aa73a8977b6f?source=collection_archive---------0----------------------->

![](img/7333aa8ceb4eaae12050c6ef50ab9c6a.png)

Photo by [chuttersnap](https://unsplash.com/@chuttersnap?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

本文将带您了解在运行 Oracle Linux (OL) 7.x 的裸机或虚拟机上安装和配置 Docker 和 Kubernetes 所需的步骤，该虚拟机位于企业*代理*之后。

# 先决条件

确保您满足以下先决条件:

*   OL 7.x 使用 Unbreakable Enterprise Kernel Release 4(UEK R4)或更高版本
*   yum 被配置为与您的公司代理对话。为了快速参考，您可以编辑“/etc/yum.conf”并添加或更新代理条目:

```
proxy=http://<proxy-host>:<proxy-port>
```

# 安装 Docker 引擎

编辑“/etc/yum . repos . d/public-yum-ol7 . repo”并启用“ol7_addons”频道。这是将“ol7_addons”部分下的“enabled”选项设置为“1”的问题。接下来，运行 yum 来安装最新的 docker 引擎，该引擎在这个通道上可用:

```
yum install docker-engine
```

# 配置代理

创建文件“/etc/systemd/system/docker . service . d/http-proxy . conf”并添加以下内容:

```
[Service]
Environment="HTTP_PROXY=<proxy-host>:<proxy-port>"
Environment="HTTPS_PROXY=<proxy-host>:<proxy-port>"
Environment="NO_PROXY=localhost,127.0.0.1,<your-no-proxy-entries>"
```

确保用适合您环境的值替换“<proxy-host>”、“<proxy-port>”和“<your-no-proxy-entries>”。</your-no-proxy-entries></proxy-port></proxy-host>

现在运行以下命令来启动 docker 引擎，并确保它在重新启动时重新启动:

```
systemctl daemon-reload
systemctl enable docker
systemctl start docker
```

您可以通过运行以下命令来检查 docker 的状态和版本:

```
systemctl status docker
docker version
```

使用 web 浏览器，登录位于 https://container-registry.oracle.com[的 Oracle Container Registry 网站](https://container-registry.oracle.com)。导航到容器服务类别并接受许可协议。

# 安装 Kubernetes 主节点

确保“ol7_addons”通道已启用(请参考上面的安装 docker 引擎部分)。运行 yum 安装“kubeadm”:

```
yum install kubeadm
```

现在使用 Docker CLI 登录到 Oracle 容器注册中心:

```
docker login container-registry.oracle.com/kubernetes
```

以 root 用户身份运行以下命令，将 sbin 添加到 PATH 变量中:

```
export PATH=$PATH:/sbin
```

以 root 用户身份运行以下命令来添加端口转发规则:

```
iptables -P FORWARD ACCEPT
```

如果您正在运行“防火墙”服务，请以 root 用户身份运行以下命令:

```
firewall-cmd --add-masquerade --permanent
firewall-cmd --add-port=10250/tcp --permanent
firewall-cmd --add-port=8472/udp --permanent
firewall-cmd --add-port=6443/tcp --permanent
```

最后，以 root 用户身份运行以下命令，将主机配置为主节点:

```
kubeadm-setup.sh up
```

如果有任何问题，上面的命令将通知您可能的补救措施。在成功运行之后，该命令将打印下一步，即让普通用户准备运行“kubectl”命令，以及在 kubernetes 集群中充当工作节点的其他主机上运行的命令。记下稍后将用于将工作节点加入集群的令牌和哈希。

# 安装 Kubernetes 工作节点

在每台应该设置为工作节点的额外 OL 计算机上，重复设置主节点时执行的所有步骤，最后一步除外。

以 root 用户身份运行 kubeadm-setup join 命令，而不是从上面运行最后一步:

```
kubeadm-setup.sh join --token <token> <master-host>:6443 \
    --discovery-token-ca-cert-hash <hash>
```

配置工作节点后，您可以返回到主节点并运行以下命令来查看所有节点:

```
kubectl get nodes
```

您已经准备好开始部署服务并使用 Kubernetes。

有关更多信息和深入文档，请参考以下链接:

*   [Docker](https://docs.oracle.com/cd/E52668_01/E87205/html/index.html) 的 Oracle 容器运行时
*   面向 [Kubernetes](https://docs.oracle.com/cd/E52668_01/E88884/html/) 的 Oracle 容器服务