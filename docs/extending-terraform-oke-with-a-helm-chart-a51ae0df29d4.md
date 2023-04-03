# 用舵图扩展地形图

> 原文：<https://medium.com/oracledevs/extending-terraform-oke-with-a-helm-chart-a51ae0df29d4?source=collection_archive---------1----------------------->

在设计 [Terraform OKE](https://github.com/oracle/sample-oke-for-terraform) 供应脚本时，我们想要做的事情之一就是使其可重用。这意味着扩展基本样例回购并添加您自己的扩展。

在本帖中，我们将使用 helm charts 为 OKE 部署一个 redis 集群。Terraform 有一个[头盔供应商](https://www.terraform.io/docs/providers/helm/index.html)，所以我们将使用它。

首先，像我们之前做的那样克隆回购:

```
git clone [https://github.com/oracle/sample-oke-for-terraform.git](https://github.com/oracle/sample-oke-for-terraform.git) tfoke
cd tfoke
```

按照[指令](https://github.com/oracle/sample-oke-for-terraform/blob/master/docs/instructions.md)创建你的地形变量文件。

## 添加 helm 提供程序和存储库

在 oke 模块中，创建一个文件 redis.tf，首先我们需要配置 helm provider。因为我们已经有了 kubeconfig 文件，所以我们将使用 File Config 方法。将以下内容添加到 redis.tf:

```
provider "helm" {
    kubernetes {
        config_path = "${path.root}/generated/kubeconfig"
    }
}
```

接下来，我们将添加一个 helm 存储库:

```
data "helm_repository" "stable" {
    name = "stable"
    url = "https://kubernetes-charts.storage.googleapis.com"
}
```

## 使用舵释放添加 redis

我们将使用 [redis 头盔图](https://github.com/helm/charts/tree/master/stable/redis)创建一个头盔释放。但是，我们希望 helm 仅在工作节点变为活动状态后部署。在[示例报告](https://github.com/oracle/sample-oke-for-terraform/blob/master/modules/oke/activeworker.tf)中，有一个 null _ resource“is _ worker _ active ”,您可以使用它来设置显式依赖关系。将以下内容添加到 redis.tf 中:

```
resource "helm_release" "redis" { depends_on = ["null_resource.is_worker_active",    "local_file.kube_config_file"] provider = "helm"
    name = "oke-redis"
    repository = "${data.helm_repository.stable.metadata.0.name}"
    chart = "redis"
    version = "6.4.5" set {
        name  = "cluster.enabled"
        value = "true"
    } set {
        name = "cluster.slaveCount"
        value = "3"
    }

    set {
        name = "master.persistence.size"
        value = "50Gi"
    }
}
```

如果您更喜欢使用 yaml 文件自定义 helm 版本，请在 oke 模块下创建一个名为 resources 的文件夹，并将文件 values.yaml 从 redis chart repo 复制到 redis_values.yaml:

```
curl -o modules/oke/resources/redis_values.yaml https://raw.githubusercontent.com/helm/charts/master/stable/redis/values.yaml
```

从 terraform 代码中删除 redis 版本中的单独设置，并添加以下内容:

```
values = [
   "${file("${path.module}/resources/redis_values.yaml")}"
]
```

您的发布应该如下所示:

```
resource "helm_release" "redis" { depends_on = ["null_resource.is_worker_active",    "local_file.kube_config_file"] provider = "helm"
    name = "my-redis-release"
    repository = "${data.helm_repository.stable.metadata.0.name}"
    chart = "redis"
    version = "6.4.5" values = [
    "${file("${path.module}/resources/redis_values.yaml")}"
  ]
}
```

请注意，您也可以组合上述两种配置方法，但我更喜欢将我的方法放在一个位置。您可以更改 yaml 文件中的值，例如，我将默认的 cluster.slaveCount 更改为 3，将 persistence.size 更改为 50Gi。

运行 terraform init 下载 helm provider，然后再次应用:

```
terraform init
terraform apply -auto-approve
```

登录堡垒，做一个舵手列表:

```
helm listNAME            REVISION        UPDATED                         STATUS          CHART           APP VERSION     NAMESPACE                        
oke-redis       1               Wed Apr 24 12:05:40 2019        DEPLOYED        redis-6.4.5     4.0.14          default
```

接下来，获取 redis 图表提供的注释:

```
helm status
```

## 与 Redis 集群交互

当您运行 helm status 时，可以获得以下步骤。

1.  获取 Redis 密码:

```
export REDIS_PASSWORD=$(kubectl get secret --namespace default oke-redis -o jsonpath="{.data.redis-password}" | base64 --decode)
```

2.运行 Redis pod:

```
kubectl run --namespace default oke-redis-client --rm --tty -i --restart='Never' \                                                            
    --env REDIS_PASSWORD=$REDIS_PASSWORD \                                                                                                       
   --image docker.io/bitnami/redis:4.0.14 -- bash
```

3.使用 Redis cli 连接:

```
redis-cli -h oke-redis-master -a $REDIS_PASSWORD
```

4.键入 redis 命令:

```
oke-redis-master:6379> ping                                                                                                                      
PONG
```

## 检查您的集群

回想一下，在 yaml 文件中，我们将 redis 从服务器的数量设置为 3。让我们验证一下:

```
kubectl get pods
NAME                               READY   STATUS    RESTARTS   AGE                                                                              
oke-redis-master-0                 1/1     Running   0          42m                                                                              
oke-redis-slave-79c45c57d8-67bxj   1/1     Running   1          42m                                                                              
oke-redis-slave-79c45c57d8-s6znq   1/1     Running   0          42m                                                                              
oke-redis-slave-79c45c57d8-wnfrh   1/1     Running   0          42m
```

您可以看到有 3 个 pod 在运行 redis slaves。

## 更新您的版本

比如说，我们现在想更新头盔版本来改变一些设置，比如把奴隶的数量从 3 个变成 2 个。我们可以使用 helm cli 手动完成:

```
helm upgrade oke-redis stable/redis --set cluster.slaveCount=2
```

或者，我们可以在 redis_values.yaml 中更改它，然后再次运行 terraform apply:

```
..
..
..
module.oke.helm_release.redis: Still modifying… (ID: oke-redis, 10s elapsed)
module.oke.helm_release.redis: Still modifying… (ID: oke-redis, 20s elapsed)
module.oke.helm_release.redis: Still modifying… (ID: oke-redis, 30s elapsed)
module.oke.helm_release.redis: Still modifying… (ID: oke-redis, 40s elapsed)
module.oke.helm_release.redis: Still modifying… (ID: oke-redis, 50s elapsed)
module.oke.helm_release.redis: Still modifying… (ID: oke-redis, 1m1s elapsed)
module.oke.helm_release.redis: Modifications complete after 1m9s (ID: oke-redis)Apply complete! Resources: 1 added, 1 changed, 1 destroyed.
```

同时，从另一个终端，我们可以看到正在更新的 pod 数量:

```
kubectl get pods -woke-redis-master-0                 0/1     Terminating   0          61s                                                                          
oke-redis-slave-6bd9dc8d89-jdrs2   0/1     Running       0          3s                                                                           
oke-redis-slave-6bd9dc8d89-kvc8r   0/1     Running       0          3s                                                                           
oke-redis-slave-6fdd8c4b56-44qpb   0/1     Terminating   0          63s
```

在以后的文章中，我们将研究扩展 terraform-oci-oke 模块的其他方法，以便在 oke 上部署软件。