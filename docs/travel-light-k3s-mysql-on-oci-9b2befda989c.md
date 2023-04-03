# 旅行灯:OCI K3S+MySQl

> 原文：<https://medium.com/oracledevs/travel-light-k3s-mysql-on-oci-9b2befda989c?source=collection_archive---------0----------------------->

![](img/2344829396db90e44d970d0b8fbd748b.png)

在之前的一篇帖子中，我利用 T2 K3S T3 对 OCI 进行了一次旋转，我们创建了一个基本的单节点服务器/代理，它使用 SQLite 作为数据存储。K3S 的好处之一是它为您提供了许多数据存储选项

*   [MySQL](https://www.mysql.com/)
*   嵌入式或外部 etcd
*   一种数据库系统

在本文中，我们将尝试使用 MySQL 作为数据存储。由于 [MySQL 在 OCI](https://docs.oracle.com/en-us/iaas/mysql-database/index.html) 上作为一项服务可用，我们也将使用它。我们还将添加代理节点。下面是 K3S 集群的基础设施，以及子网、堡垒和 MySQL。我们还需要 2 个 NSG(用于服务器和代理)以及一个安全列表(用于 MDS 子网)。您可以在 [K3S 文档](https://rancher.com/docs/k3s/latest/en/installation/installation-requirements/#networking)中找到为安全规则打开的端口列表。

![](img/894bb997ba8709062ab1a6d134355393.png)

## 设置服务器

我们现在将只创建 1 个。创建一个计算实例，将其添加到 servre 子网，并确保您也设置了服务器 NSG。

设置完成后，登录服务器主机并安装 MySQL Shell:

```
sudo dnf install -y mysql-shell
```

从 OCI 控制台获取 MDS 端点，并使用 MySQL Shell 连接到它:

```
mysqlsh <admin_user>@<private_endpoint>
```

![](img/16e30f3100f59141c84a4b746a4fda9a.png)

在上面的截图中，我的 MDS 端点位于 10.0.0.40。设置 K3S _ DATASTORE _ ENDPOINT 变量:

```
K3S_DATASTORE_ENDPOINT='mysql://admin:V3ry_Secure!@tcp(10.0.0.40:3306)/k3s'
```

如果你能连接，你就可以走了；否则请检查您的安全列表和 NSG。您也可以使用 netcat 来检查您的连接:

```
nc -v 10.0.0.40 3306
```

在服务器上安装 mysql-shell 是很有用的，尤其是当您面临权限问题时。

您现在可以安装 K3S 了:

```
curl -sfL [https://get.k3s.io](https://get.k3s.io) | sh -s — server \
  --datastore-endpoint=$K3S_DATASTORE_ENDPOINT
```

设置 K3S 后，如前所述更改 k3s.yaml 文件的权限，并验证您是否可以看到一个节点:

```
$ sudo chmod go+r /etc/rancher/k3s/k3s.yaml$ kubectl get nodes
NAME     STATUS   ROLES                  AGE   VERSIONserver   Ready    control-plane,master   34m   v1.21.5+k3s2
```

获取 K3S 令牌，您将需要该令牌向服务器注册代理:

```
$ sudo cat /var/lib/rancher/k3s/server/node-token
K104214ab68054dadeb1616eda4c1b824de120a9f297dee24a08014cbcf89802220::server:38123df6165f4573e47def57e601aeea
```

最后，配置 firewalld 以允许端口 6443 上的流量:

```
sudo firewall-cmd --permanent --add-port=6443/tcp
sudo firewall-cmd --reload
```

## 设置代理

![](img/8b970ac0fb9ba9e5e8c056d3c0c19b0e.png)

接下来，我们将创建代理。我们现在只创建 1 个。创建一个计算实例，将其添加到代理子网，并确保您已经设置了代理 NSG。

一旦提供了代理节点，就可以使用第二个终端对其进行 ssh。验证您是否可以使用 netcat 连接到 API 服务器(我的分配为 10.0.0.18):

```
$ nc -v 10.0.0.18 6443
Ncat: Version 7.70 ( [https://nmap.org/ncat](https://nmap.org/ncat) )
Ncat: Connected to 10.0.0.18:6443.
```

您现在可以继续安装代理了。首先导出以下环境变量:

```
K3S_URL=https://10.0.0.18:6443
K3S_TOKEN=K104214ab68054dadeb1616eda4c1b824de120a9f297dee24a08014cbcf89802220::server:38123df6165f4573e47def57e601aeea
```

请注意，因为我们正在安装代理，所以需要令牌。在安装过程中使用 K3S_URL 将会配置节点成为代理。太棒了。我们开始吧:

```
curl -sfL [https://get.k3s.io](https://get.k3s.io) | K3S_URL=$K3S_URL K3S_TOKEN=$K3S_TOKEN sh -
```

现在，当您检查服务器上的节点时，您可以看到工作节点:

```
$ kubectl get nodes
NAME     STATUS   ROLES                  AGE   VERSION
server   Ready    control-plane,master   34m   v1.21.5+k3s2
a1       Ready    <none>                 47s   v1.21.5+k3s2
```

这里，我们使用一个计算实例来创建代理节点，您可以重复这一步。但是也可以用实例池来创建代理。

首先，创建一个实例配置。确保在云初始化中添加以下内容:

```
#cloud-config
runcmd:
 — curl -sfL https://get.k3s.io | K3S_URL=https://10.0.0.18:6443 K3S_TOKEN=K104214ab68054dadeb1616eda4c1b824de120a9f297dee24a08014cbcf89802220::server:38123df6165f4573e47def57e601aeea sh -
```

用您的值替换上面的令牌值和 url。现在可以创建实例池了。

实例池创建的代理准备就绪后，检查它们是否成功注册:

```
kubectl get nodes
```

## 结论

这篇文章是为了探索 OCI·MDS 运行 K3S 的可行性。在未来的帖子中，我们将探索更多的 K3S 功能以及与 OCI 的集成。

参考资料:

*   [https://rancher . com/docs/k3s/latest/en/installation/install-options/](https://rancher.com/docs/k3s/latest/en/installation/install-options/)
*   【https://rancher.com/docs/k3s/latest/en/quick-start/ 号
*   [https://rancher . com/docs/k3s/latest/en/installation/datastore/](https://rancher.com/docs/k3s/latest/en/installation/datastore/)