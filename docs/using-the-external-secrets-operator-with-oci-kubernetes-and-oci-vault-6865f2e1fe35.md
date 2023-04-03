# 对 OCI·库伯内特和 OCI·库使用外部机密操作符

> 原文：<https://medium.com/oracledevs/using-the-external-secrets-operator-with-oci-kubernetes-and-oci-vault-6865f2e1fe35?source=collection_archive---------0----------------------->

一位客户最近向我推荐了[外部秘密运营商(ESO)项目](https://external-secrets.io/v0.5.3/)，我很高兴地看到它已经将[与](https://external-secrets.io/v0.5.3/provider-oracle-vault/) [OCI 金库](https://docs.oracle.com/en-us/iaas/Content/KeyManagement/home.htm#top)集成在一起。在本文中，我们将尝试一下 ESO，看看如何将它与 OKE 结合使用。

## 在 OKE 上安装外部机密操作符

首先，使用 OCI 控制台中的快速创建或使用[https://github . com/Oracle-terra form-modules/terra form-OCI-OKE](https://github.com/oracle-terraform-modules/terraform-oci-oke)创建一个 OKE 集群。现在，您可以使用 Cloud Shell 或 terraform-oci-oke 模块中的操作员主机来运行 kubectl 或 helm 命令。

让我们使用舵图来安装 ESO:

```
helm repo add external-secrets [https://charts.external-secrets.io](https://charts.external-secrets.io)helm install external-secrets \
  external-secrets/external-secrets \
  -n external-secrets \
  --create-namespace
```

## 配置 ESO 以用于 OCI 保险库

如果您是使用 [instance_principal](https://docs.oracle.com/en-us/iaas/Content/Identity/Tasks/callingservicesfrominstances.htm) 的**而不是**，请在 yaml 文件中创建一个 Kubernetes 秘密来保存私钥和指纹:

```
apiVersion: v1
kind: Secret
metadata:
  name: pk-fp
  labels:
    type: oracle
type: Opaque
stringData:
  privateKey: |
    -----BEGIN RSA PRIVATE KEY-----
    abcdefg1234567890/+-
    -----END RSA PRIVATE KEY-----
  fingerprint: ab:cd:ef:gh:12:34
```

创造秘密:

```
kubectl create -f pk-fp.yaml
```

您现在可以创建秘密商店:

```
apiVersion: external-secrets.io/v1beta1
kind: SecretStore
metadata:
  name: alivault
spec:
  provider:
    oracle:
      vault: # The vault OCID
      region: # The vault region
      auth:
        user: # A user OCID
        tenancy: # A user's tenancy
        secretRef:
          privatekey:
            name: pk-fp
            key: privateKey
          fingerprint:
            name: pk-fp
            key: fingerprint
```

注意:如果您使用 instance_principals，请删除从 *auth* 到结尾的部分。您还需要创建一个动态组，并向其中添加工作节点。最后，您还需要创建一个策略，允许动态组使用您将在 OCI 保险库中创建的密钥。

```
kubectl create -f oci-secret-store.yaml
```

## 创建外部 Secret

现在让我们使用 ESO 创建一个 ExternalSecret:

```
apiVersion: external-secrets.io/v1beta1
kind: ExternalSecret
metadata:
  name: oci-secret
spec:
  refreshInterval: 0.03m
  secretStoreRef:
    kind: SecretStore
    name: alivault # Must match SecretStore on the cluster
  target:
    name: my-oke-secret # Name for the secret on the cluster
    creationPolicy: Owner
  data:
  - secretKey: key
    remoteRef:
      key: my-eso-secret
```

我的-eso-secret 是金库中 OCI 秘密的名字:

![](img/3463472b7640be6a901664c096f3ace6.png)

在这种情况下，它只有一个普通的内容(“aSecretInSiV”)，所以我们使用上面的数据，我们可以创建 ExternalSecret:

```
kubectl create -f ext-secret.yaml
```

获取外部秘密:

```
kubectl get es
NAME         STORE      REFRESH INTERVAL   STATUS
oci-secret   alivault   0.03m              SecretSynced
```

我们可以看到秘密已经被同步了。让我们列出这个名称空间中的秘密:

```
kubectl get secrets
NAME                  TYPE                                DATA   AGE
default-token-mnpsm   kubernetes.io/service-account-token   3    41m
my-oke-secret         Opaque                                1   108s
pk-fp                 Opaque                                2    10m
```

我们可以看到 *my-oke-secret* 已经创建。让我们来一窥其中的秘密:

```
kubectl get secret my-oke-secret -o yaml
apiVersion: v1
data:
  key: YVNlY3JldEluU2lW
immutable: false
kind: Secret
metadata:
  annotations:
    reconcile.external-secrets.io/data-hash: cff53a4bddfc2d04ea50e6d0da2c2868
  creationTimestamp: "2022-06-01T02:11:43Z"
  name: my-oke-secret
  namespace: default
  ownerReferences:
  - apiVersion: external-secrets.io/v1beta1
    blockOwnerDeletion: true
    controller: true
    kind: ExternalSecret
    name: oci-secret
    uid: 0039c7ab-91c2-4e15-acc7-f8de87ba1254
  resourceVersion: "9117"
  uid: 3074d172-078f-4214-9f21-a6bc69aae182
type: Opaque
```

我们可以看到密钥已经创建。让我们提取它，看看它是否与我们在《OCI 的秘密》中创造的相匹配:

```
kubectl get secret my-oke-secret -o json | jq -r ."data.key" | base64 -d
aSecretInSiV
```

非常好。这个吻合！

## 更新秘密

现在让我们看看它是否能处理更新。在 OCI 的秘密中，让我们创造一个新的版本:

![](img/9e7c7201567d418858446175c0102b54.png)

等待版本 2 处于活动状态，然后再次运行上面的命令来提取密码:

```
kubectl get secret my-oke-secret -o json | jq -r ."data.key" | base64 -d
SecretVersion2
```

Kubernetes 秘密中的值已更新。

## 将 ESO 与群集中的另一个应用程序一起使用

让我们看看如何使用 ESO 将秘密注入集群中运行的其他应用程序。在这种情况下，我们将尝试向 Grafana 中注入一个密码。

添加 Grafana helm repo:

```
helm repo add grafana https://grafana.github.io/helm-chartshelm show values grafana/grafana > grafana.yaml
```

编辑 grafana.yaml 并找到管理员凭据部分:

```
admin:
  ## Name of the secret. Can be templated.
  existingSecret: "grafana-in-eso"
  userKey: admin-user
  passwordKey: admin-password
```

将 existingSecret 更改为如上(或您选择的值)。

接下来，在 OCI 保险库中创建一个秘密，如下所示:

![](img/4d241aeb0e5506231e8fd59d72371be7.png)

我们现在可以使用上面的代码创建一个 ExternalSecret:

```
apiVersion: external-secrets.io/v1beta1
kind: ExternalSecret
metadata:
  name: oci-secret
spec:
  refreshInterval: 0.03m
  secretStoreRef:
    kind: SecretStore
    name: alivault
  target:
    name: grafana-in-eso
    creationPolicy: Owner
  dataFrom:
  - extract:
      key: grafana
```

现在让我们部署 Grafana:

```
helm install grafana grafana/grafana -f grafana.yaml
```

转到 Grafana 吊舱的左舷:

```
export POD_NAME=$(kubectl get pods --namespace default -l "app.kubernetes.io/name=grafana,app.kubernetes.io/instance=grafana" -o jsonpath="{.items[0].metadata.name}")kubectl --namespace default port-forward $POD_NAME 3000
```

![](img/f2810c0ebbf5b6651cb7c148f8eccb14.png)

请注意，我们现在使用的是在 OCI 保险库中创建的值。我们能够登录。

假设我们现在想要更改管理员用户名和密码。在 OCI 秘密中创建新版本:

![](img/756d1cf3f8591e0f29106a94f3a56588.png)

激活后，再次登录:

![](img/7bdc27d832c5484901857d3e4eaa506b.png)

这将失败。原因是当 Grafana pod 被创建时，它读取密码并使用密码中的 admin-username 和 admin-password 值，并将其存储在配置文件中。所以现在，即使 OCI 秘密和库伯内特秘密的价值观已经改变，格拉法纳还没有意识到这一点。

为了使用新的值，我们必须更新这个文件。我们可以使用 Grafana 的 REST API，但我不知道 Grafana 是否有监听机制来监视秘密变化(这是将来要调查的事情)。目前，强制重新加载用户名和密码的最简单方法是删除 Grafana pod，以强制 Kubernetes 重新创建它:

```
kubectl delete pod $POD_NAME
```

您会注意到创建了一个新的 pod:

```
kubectl get pods
NAME                       READY   STATUS              RESTARTS  AGE
grafana-788976bc4c-q2swm   0/1     ContainerCreating   0         8s
```

等待它准备好，并使用您在 secret 版本 2 中使用的值将它转发到端口。这一次，您将能够登录到 Grafana。

**注意**:这不是更新 Grafana 用户名和密码的推荐方式。这个例子的目的是展示在一个应用程序中通过 ESO 从 OCI 保险库重新装载秘密。

## 摘要

外部秘密运营商是一个很好的方式来整合和使用 OCI 奥凯与 OCI 保险库。OKE 和在 OKE 中运行的应用程序都可以使用 ESO 从 OCI 保险库中检索敏感数据。

我希望这篇文章对你有用。

想讨论一下吗？加入我们的[公共休闲频道](https://bit.ly/devrel_slack)！