# drone.io CI/CD 平台简介第 2 部分

> 原文：<https://medium.com/globant/introduction-to-drone-io-ci-cd-platform-part-2-fa9fdc6a3659?source=collection_archive---------0----------------------->

![](img/81ca5064f77861a2a89d92d9e54f8d38.png)

# 介绍

这是我几周前写的关于 Drone.io 的文章的第二部分。在本文中，我们将使用 [Helm charts](https://helm.sh/) 在 Kubernetes 集群上部署无人机，并使用无人机在该集群上构建和部署一个示例应用程序。

像在上一篇文章中一样，我们将仅出于测试目的进行本地无人机部署，需要为正确的生产安装配置许多其他元素，如 https、卷配置、机密处理、权限、负载平衡器或入口控制器等。这些话题将在以后的文章中讨论。

对于本实验室，我们将使用 [minikube](https://minikube.sigs.k8s.io/docs/) 作为 Kubernetes 集群，以遵循本实验室的指示，但也可以使用任何其他 Kubernetes 引擎。要再次访问无人机界面，我们将使用 [ngrok](https://ngrok.com/) ，请参考[之前的帖子](/globant/introduction-to-drone-io-ci-cd-platform-1d43f8bc1728)了解如何设置它。

本文将回顾以下部分:

*   体系结构
*   Kubernetes 部署
*   进入无人机界面
*   部署示例应用程序
*   结论

# 体系结构

让我们回顾一下无人机架构组件:

*   无人机服务器
*   无人机特工(称为奔跑者)
*   无人机管道配置文件(在每个 git 存储库上)
*   一个 SCM 存储库，由 Drone 提供应用程序

这是前一篇文章中描述所有组件之间交互的架构图:

![](img/4a8d7dcc608e129f3595f16d34d4f4e9.png)

Drone.io architecture diagram

现在让我们看看这些组件是如何融入 Kubernetes 集群的:

![](img/57c48dc37825de6e6645556eee55b255.png)

Drone.io Kubernetes deployment diagram

在 Kubernetes 集群中，服务器和运行器将作为由 [*ReplicaSet*](https://kubernetes.io/docs/concepts/workloads/controllers/replicaset/) 控制并由 [*Deployment*](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/) 资源部署的 pod 运行，这为无人机安装提供了高可用性和可伸缩性。靶标管道将在任何集群节点中可用的任何运行程序单元中执行。pod 可以根据在服务器和运行程序中执行的构建负载逐步升级。

# Kubernetes 部署

# 先决条件

*   像 [minikube](https://minikube.sigs.k8s.io/) 这样的 Kubernetes 星团。
*   掌舵包经理。
*   Kubernetes CLI， [kubectl](https://kubernetes.io/docs/reference/kubectl/kubectl/) 命令
*   指向无人机服务器的可公开访问的主机名。因为本实验是本地安装，所以我们将再次使用 ngrok 来获取公共主机名。
*   SCM 平台 OAuth ID 和令牌。在这篇文章中，我们将使用 GitHub，请参考上一篇文章来阅读如何设置它，或者参考[官方文档](https://docs.drone.io/server/overview/)来了解如何设置其他 SCM 平台。
*   服务器和跑步者之间通信的秘密。这只是一串文本，将用作它们之间的密码。
*   GitHub 和 Docker Hub 帐户。

让我们回顾一下在开始在 Kubernetes 部署无人机之前您需要做的事情:

*   运行 ngrok:

```
$ ngrok http 80
```

*   创建一个 GitHub OAuth 应用程序(或者更新现有的应用程序，如果您已经完成了上一篇文章中的实验)，按照上一篇文章中的说明进行配置，使用上一步中的 ngrok URL。

现在我们已经准备好开始这个实验了！

# 安装 minikube

Minikube 安装相当简单，我们使用 Linux 作为这个实验室的平台，如果你使用任何其他平台，请参考 minikube [安装说明](https://minikube.sigs.k8s.io/docs/start/)。首先，下载 minikube 二进制文件并安装。从终端运行:

```
$ curl -LO [https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64](https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64)sudo install minikube-linux-amd64 /usr/local/bin/minikube
```

完成这些之后，我们就可以启动一个 minikube 本地集群了。以非 root 用户身份从终端运行:

```
$ minikube start
```

等待几分钟，minikube 将为主节点和工作节点启动一个虚拟机器。minikube 集群完成启动后，通过运行 *kubectl* 命令对其进行测试。可以[安装 *kubectl*](https://kubernetes.io/docs/tasks/tools/) ，或者使用 minikube 根据当前 minikube Kubernetes 版本安装:

```
$ minikube kubectl — get po -A
```

为了便于使用，将其作为 shell 脚本的别名添加，将以下行添加到 shell 的配置文件中:

```
alias kubectl=”minikube kubectl --“
```

现在我们可以使用 kubectl 来访问集群。例如，让我们来看看集群节点:

```
$ kubectl get nodes
NAME     STATUS ROLES                AGE VERSIONminikube Ready  control-plane,master 29d v1.21.2
```

如果您看不到类似的输出或任何类型的错误，您应该遵循[故障排除](https://minikube.sigs.k8s.io/docs/handbook/troubleshooting/)文档。

# 安装舵

头盔安装就像 minikube 一样简单。有适用于不同操作系统的软件包，但要安装最新版本，请下载官方安装脚本并运行它:

```
$ curl -fsSL -o get_helm.sh [https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3](https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3)
$ chmod 700 get_helm.sh
$ ./get_helm.sh
```

测试舵安装是否正确:

```
$ helm version
version.BuildInfo{Version:”v3.8.2", GitCommit:”6e3701edea09e5d55a8ca2aae03a68917630e91b”, GitTreeState:”clean”, GoVersion:”go1.17.5"}
```

我们现在准备在 minikube 集群上安装无人机。

# 使用 Helm 安装服务器和转轮

在 Kubernetes 上安装无人机包括安装两个组件:服务器和运行器。首先要做的是添加官方无人机头盔库:

```
$ helm repo add drone [https://charts.drone.io](https://charts.drone.io)
“drone” has been added to your repositories$ helm repo update
Hang tight while we grab the latest from your chart repositories……Successfully got an update from the “drone” chart repositoryUpdate Complete. ⎈Happy Helming!⎈
```

添加 Helm 存储库后，下一步是创建一个名称空间，在其中部署无人机组件:

```
$ kubectl create ns drone
namespace/drone created
```

# 安装服务器

安装 helm 图表时，必须向 Helm 命令提供一个 yaml 文件，其中包含正在部署的应用程序的定制。这些文件包含了我们在无人机简介的第一部分中提到的相同配置参数，但是是 yaml 格式，加上任何特定于 Kubernetes 的配置选项。有关这些值的解释，请参考该文章。这是服务器的 *values.yaml* 文件:

```
env: DRONE_SERVER_HOST: xxxxxxxxxx.ngrok.io
  DRONE_SERVER_PROTO: http
  DRONE_RPC_SECRET: xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
  DRONE_GITHUB_CLIENT_ID: xxxxxxxxxxxxxxxxxxxx
  DRONE_GITHUB_CLIENT_SECRET: xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
  DRONE_TLS_AUTOCERT: false
  DRONE_USER_CREATE: username:githubuser,admin:true # Optional, needed in case to debug
  DRONE_LOGS_DEBUG: true
  DRONE_LOGS_TRACE: true
```

编辑值文件后，用您自己的值替换它们，并继续安装服务器:

```
$ helm install — namespace drone drone drone/drone -f drone-server-values.yaml
NAME: drone
LAST DEPLOYED: Thu May 26 14:15:22 2022
NAMESPACE: drone
STATUS: deployed
REVISION: 1
TEST SUITE: NoneNOTES:
1\. Get the application URL by running these commands:
export POD_NAME=$(kubectl get pods — namespace drone -l “app.kubernetes.io/name=drone,app.kubernetes.io/instance=drone” -o jsonpath=”{.items[0].metadata.name}”)
export CONTAINER_PORT=$(kubectl get pod — namespace drone $POD_NAME -o jsonpath=”{.spec.containers[0].ports[0].containerPort}”)
echo “Visit [http://127.0.0.1:8080](http://127.0.0.1:8080) to use your application”
kubectl — namespace drone port-forward $POD_NAME 8080:$CONTAINER_PORT
```

# 安装转轮

同理，我们需要为跑者准备好 helm *values.yaml* 文件:

```
rbac:
  buildNamespaces:
    - droneEnv:
  # This secret has to be the same configured on the serverDRONE_RPC_SECRET: xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
  DRONE_NAMESPACE_DEFAULT: drone
  # This is the drone server hostname internal to kubernetes, not the external hostname
  DRONE_RPC_HOST: drone
  DRONE_RPC_PROTO: http
  DRONE_RUNNER_NAME: “Drone_kube_runner_1”
  DRONE_DEBUG: true
  DRONE_TRACE: true
```

编辑值文件后，继续安装流道:

```
$ helm install — namespace drone drone-runner-kube drone/drone-runner-kube -f drone-runner-kube-values.yamlNAME: drone-runner-kube
LAST DEPLOYED: Thu May 26 14:16:34 2022
NAMESPACE: drone
STATUS: deployed
REVISION: 1
TEST SUITE: None
```

# 验证安装

要验证安装是否成功，请检查部署的资源，所有资源都应处于*运行*状态:

```
$ kubectl get all -n droneNAME                                  READY STATUS RESTARTS AGE
pod/drone-586c4d64bf-46qt9             1/1  Running 0      2m26s
pod/drone-runner-kube-7f79d87b75-fffn5 1/1  Running 0      75sNAME                      TYPE      CLUSTER-IP EXTERNAL-IP PORT(S) AGE
service/drone             ClusterIP 10.101.218.16 <none>   80/TCP 2m26s
service/drone-runner-kube ClusterIP 10.111.102.5 <none>    3000/TCP 75sNAME                              READY UP-TO-DATE AVAILABLE AGE
deployment.apps/drone             1/1   1          1         2m26s
deployment.apps/drone-runner-kube 1/1   1          1         75sNAME                                         DESIRED CURRENT READY AGE
replicaset.apps/drone-586c4d64bf             1       1       1  2m26s
replicaset.apps/drone-runner-kube-7f79d87b75 1       1       1    75s
```

如果没有，则检查 pods 日志:

```
$ kubectl — namespace drone logs \
-l ‘app.kubernetes.io/name=drone’ \
-l ‘app.kubernetes.io/component=server’
```

如果服务器成功启动，您应该会看到如下消息:

```
time=”2020–05–26T13:49:03Z” level=info msg=”starting the server” addr=”:3000"
```

对于跑步者，您应该会看到这样的消息:

```
time=”2022–05–26T14:05:05Z” level=info msg=”successfully pinged the remote server”
time=”2022–05–26T14:05:05Z” level=info msg=”polling the remote server” capacity=100 endpoint=”http://drone" kind=pipeline type=kubernetes
```

如果其中任何一个有错误，请在 Drone 的[对话服务器](http://discourse.drone.io/)上寻求帮助。

# 进入无人机界面

要访问无人机接口，我们需要公开部署服务器时创建的 Kubernetes 服务。有许多方法可以将 Kubernetes 服务公开给外界，但是由于我们使用 minikube 和 ngrok 来提供对集群的外部访问，我们将使用 kubectl 的 port-forward 选项将无人机接口公开给 ngrok 转发请求的端口。任何其他方法，如*负载平衡器*、*外部名称*或*入口控制器*不在本教程的范围之内。要将无人机服务转发到端口 80，请运行以下命令

```
$ sudo kubectl — namespace drone port-forward svc/drone 80:80
Password:Forwarding from 127.0.0.1:80 -> 80
Forwarding from [::1]:80 -> 80
```

我们需要使用 sudo，因为我们绑定到一个低于 1024 的端口。现在访问 [http://localhost](http://localhost) ，您将看到我们在上一篇文章中看到的相同界面:

![](img/b41f2af9d7278cca88395c67b8c1a114.png)

# 部署示例应用程序

既然无人机服务器和 runner 已经部署在 Kubernetes 集群上，我们将部署一个示例应用程序。我们将使用 Kubernetes 文档中的一个示例应用程序，即[演示留言簿](https://kubernetes.io/docs/tutorials/stateless-application/guestbook/)。无人机管道将构建图像，将其推送到 docker hub，并部署在 minikube Kubernetes 集群中。

对于 Kubernetes 集群中的应用程序部署，有各种无人机插件，我们将[使用来自](https://plugins.drone.io/plugins/plugin-kube)[插件市场](https://plugins.drone.io/)的一个。这是示例应用程序的 [my fork](https://github.com/juanluisbaptiste/kubernetes-engine-samples) 中的 [*.drone.yml*](https://github.com/juanluisbaptiste/kubernetes-engine-samples/blob/main/.drone.yml) 管道文件:

```
---
kind: pipeline
type: kubernetes
name: guestbook-demosteps:
  - name: docker
    image: plugins/docker
    settings:
      repo: juanluisbaptiste/guestbook-demo
      dockerfile: guestbook/php-redis/Dockerfile
      context: guestbook/php-redis
      username:
        from_secret: docker_username
      password:
        from_secret: docker_password
      tags:
        - latest
  - name: Deploy demo guestbook redis follower service
    image: danielgormly/drone-plugin-kube:0.2.0
    settings:
      build_number: ${DRONE_BUILD_NUMBER}
      template: guestbook/redis-follower-service.yaml
      ca:
        from_secret: k8s_crt
      server:
        from_secret: k8s_server
      token:
        from_secret: k8s_token
  - name: Deploy demo guest book redis follower deployment
    image: danielgormly/drone-plugin-kube:0.2.0
    settings:
      build_number: ${DRONE_BUILD_NUMBER}
      template: guestbook/redis-follower-deployment.yaml
      ca:
        from_secret: k8s_crt
      server:
        from_secret: k8s_server
      token:
        from_secret: k8s_token
  - name: Deploy demo guestbook redis leader service
    image: danielgormly/drone-plugin-kube:0.2.0
    settings:
      build_number: ${DRONE_BUILD_NUMBER}
      template: guestbook/redis-leader-service.yaml
      ca:
        from_secret: k8s_crt
      server:
        from_secret: k8s_server
      token:
        from_secret: k8s_token
  - name: Deploy demo guest book redis leader deployment
    image: danielgormly/drone-plugin-kube:0.2.0
    settings:
      build_number: ${DRONE_BUILD_NUMBER}
      template: guestbook/redis-leader-deployment.yaml
      ca:
        from_secret: k8s_crt
      server:
        from_secret: k8s_server
      token:
        from_secret: k8s_token
  - name: Deploy demo guestbook frontend service
    image: danielgormly/drone-plugin-kube:0.2.0
    settings:
      build_number: ${DRONE_BUILD_NUMBER}
      template: guestbook/frontend-service.yaml
      ca:
        from_secret: k8s_crt
      server:
        from_secret: k8s_server
      token:
        from_secret: k8s_token
  - name: Deploy demo guest book frontend deployment
    image: danielgormly/drone-plugin-kube:0.2.0
    settings:
      build_number: ${DRONE_BUILD_NUMBER}
      template: guestbook/frontend-deployment.yaml
      ca:
        from_secret: k8s_crt
      server:
        from_secret: k8s_server
      token:
        from_secret: k8s_token
```

管道将构建留言簿 PHP 前端，并将其推送到 docker hub。然后，它将为应用程序创建一个 Kubernetes 名称空间，并部署刚刚构建的映像以及一组 Redis leader 和 follower 数据库。不幸的是，Kubernetes 插件还处于开发的早期阶段，目前它还不支持定义多个资源的清单的部署，所以我们需要为每个资源使用一个单独的清单。

现在，分叉我的存储库并在无人机中启用它。启用后，创建以下秘密，将需要这些秘密来将映像推送到 docker hub 并在 Kubernetes 集群中部署应用程序:

*   docker _ 用户名:Docker hub 用户名
*   docker_password: Docker hub 密码
*   k8s _ CRT:Kubernetes CA 证书的 Base-64 编码字符串
*   k8s_server:指向 Kubernetes 管理器节点的 URL。对于本实验，它将是 minikube 实例。
*   k8s_token: Kubernetes 服务帐户令牌(非 base64 编码)

前两个秘密是 docker hub 凭证，因此 Drone 可以推送在管道运行期间构建的图像。另外三个秘密是关于 Kubernetes 部署插件配置的。

要配置 *k8s_crt* 密码，可在 *~/上找到 minikube CA 证书。minikube/ca.crt* 文件。要对其进行 base64 编码，请运行以下命令:

```
$ cd ~/.minikube
$ cat ca.crt|base64 -w 0
```

将输出复制并粘贴到新的 *k8s_crt* secret 中，确保它没有被分成多行，它必须在一行中。对于 *k8s_token* secret，要获取服务帐户令牌，在 *kube-system* 名称空间中查找名为 *default-token-xxxx* 的 Kubernetes secret，并获取其值:

```
$ kubectl get secret -n kube-system|grep default
**default**-token-vpbs9 kubernetes.io/service-account-token 3 301d$ kubectl describe secret default-token-vpbs9 -n kube-systemName: default-token-vpbs9
Namespace: kube-system
Labels: <none>
Annotations: kubernetes.io/service-account.name: default
kubernetes.io/service-account.uid: 5e9b0fec-3d0f-4734-b2d8-d6bee635671a
Type: kubernetes.io/service-account-tokenData
====
ca.crt: 1111 bytes
namespace: 11 bytes
**token: eyJhbGciOiJSUzI1NiIsImtpZCI6IlBzV0dVbkFEVVI4cUNjNDVlbml5WG1qMVN2cFY3TG5qclZyMlB0QmZWM2MifQ.eyJpc3MiOiJrdWJlcm5ldGVzL3NlcnZpY2VhY2NvdW50Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9uYW1lc3BhY2UiOiJrdWJlLXN5c3RlbSIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VjcmV0Lm5hbWUiOiJkZWZhdWx0LXRva2VuLXZwYnM5Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9zZXJ2aWNlLWFjY291bnQubmFtZSI6ImRlZmF1bHQiLCJrdWJlcm5ldGVzLmlvL3NlcnZpY2VhY2NvdW50L3NlcnZpY2UtYWNjb3VudC51aWQiOiI1ZTliMGZlYy0zZDBmLTQ3MzQtYjJkOC1kNmJlZTYzNTY3MWEiLCJzdWIiOiJzeXN0ZW06c2VydmljZWFjY291bnQ6a3ViZS1zeXN0ZW06ZGVmYXVsdCJ9.AKT7mFFdAwRcy8phR8MeIllsWGo2kU52SejZ5nD05K99M8BsHBdYTwM0HBLJTeDtAk8fs1KMaZ0OY0__Ct52E4r2RM3XCyYKXwMNPRgrgyvQ3QuHxD6Qux7O5LT6gX0LKjbzFk7VKey3trgQTgJZ4oOan_b3NMQs4nDSR2mM6Yt848x92w-vBOZ5Z0XUAbpjnSszK46zNQEqBeT3qX6PUfqOQfwDGqDJbwiZ033KwycPgZQ4ZLdD1mo6DHQtoRRXEyaetgCVlEqGIgHBTJyPAlQSxwu_nY4L4PMI8M8VcTJKC40wENUlNCfQzw_JWnwbtjfsFVwqd5theAzXVt3qKA**
```

您可以尝试使用此命令提取“token”字段的值:

```
$ kubectl get secret `kubectl get secret -n kube-system|grep default|awk '{print $1}'|grep -v NAME` -o go-template='{{ .data.token | base64decode }}' -n kube-system
```

您应该得到这个值:

```
eyJhbGciOiJSUzI1NiIsImtpZCI6IlBzV0dVbkFEVVI4cUNjNDVlbml5WG1qMVN2cFY3TG5qclZyMlB0QmZWM2MifQ.eyJpc3MiOiJrdWJlcm5ldGVzL3NlcnZpY2VhY2NvdW50Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9uYW1lc3BhY2UiOiJrdWJlLXN5c3RlbSIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VjcmV0Lm5hbWUiOiJkZWZhdWx0LXRva2VuLXZwYnM5Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9zZXJ2aWNlLWFjY291bnQubmFtZSI6ImRlZmF1bHQiLCJrdWJlcm5ldGVzLmlvL3NlcnZpY2VhY2NvdW50L3NlcnZpY2UtYWNjb3VudC51aWQiOiI1ZTliMGZlYy0zZDBmLTQ3MzQtYjJkOC1kNmJlZTYzNTY3MWEiLCJzdWIiOiJzeXN0ZW06c2VydmljZWFjY291bnQ6a3ViZS1zeXN0ZW06ZGVmYXVsdCJ9.AKT7mFFdAwRcy8phR8MeIllsWGo2kU52SejZ5nD05K99M8BsHBdYTwM0HBLJTeDtAk8fs1KMaZ0OY0__Ct52E4r2RM3XCyYKXwMNPRgrgyvQ3QuHxD6Qux7O5LT6gX0LKjbzFk7VKey3trgQTgJZ4oOan_b3NMQs4nDSR2mM6Yt848x92w-vBOZ5Z0XUAbpjnSszK46zNQEqBeT3qX6PUfqOQfwDGqDJbwiZ033KwycPgZQ4ZLdD1mo6DHQtoRRXEyaetgCVlEqGIgHBTJyPAlQSxwu_nY4L4PMI8M8VcTJKC40wENUlNCfQzw_JWnwbtjfsFVwqd5theAzXVt3qKA
```

复制该值并创建一个名为 *k8s_token* 的秘密，还要确保它没有被分成多行，它必须在一行中。在您创建了前面的秘密之后，创建一个名为*的名称空间 guestbook-demo* :

```
$ kubectl create ns guestbook-demo
namespace/guestbook-demo created
```

现在我们已经准备好启动一个新的构建。在无人机界面上，在示例应用程序存储库中，单击“+ NEW BUILD”按钮启动新的构建。在左侧，您将看到正在执行的管道步骤，如果其中任何一个步骤失败，您可以单击它查看详细信息:

![](img/c14e2f5822194ed7929a336ba6e7b030.png)

当管道成功完成时，您可以通过检查创建了哪些资源来检查完成了什么:

```
$ kubectl get all -n guestbook-demo
NAME                                READY STATUS  RESTARTS AGE
pod/frontend-64bcc69c4b-6cc22       1/1   Running 2        4h
pod/frontend-64bcc69c4b-f9djm       1/1   Running 2        4h
pod/frontend-64bcc69c4b-g4n9v       1/1   Running 2        4h
pod/redis-follower-594666cdcd-xs9rk 1/1   Running 2        4h
pod/redis-follower-594666cdcd-z5tqj 1/1   Running 2        4h
pod/redis-leader-fb76b4755-vh7cm    1/1   Running 2        4hNAME                   TYPE         CLUSTER-IP    EXTERNAL-IP   PORT(S)      AGE
service/frontend       LoadBalancer 10.101.46.1   <pending> 80:31526/TCP 4h
service/redis-follower ClusterIP    10.109.70.101 <none>     6379/TCP     4h
service/redis-leader   ClusterIP    10.99.118.103 <none>     6379/TCP     4hNAME                           READY UP-TO-DATE AVAILABLE AGE
deployment.apps/frontend       3/3   3          3         4h
deployment.apps/redis-follower 2/2   2          2         4h
deployment.apps/redis-leader 1/1     1          1         4hNAME                                      DESIRED CURRENT READY AGE
replicaset.apps/frontend-64bcc69c4b       3       3       3     4h
replicaset.apps/redis-follower-594666cdcd 2       2       2     4h
replicaset.apps/redis-leader-fb76b4755    1       1       1     4h
```

您应该看到前端和 Redis pods、服务、部署以及它们的副本集，它们都处于运行状态。现在，要访问留言簿应用程序，我们需要公开应用程序的前端服务。因为前端服务属于*负载平衡器*类型，我们可以使用 minikube 服务命令来公开它:

```
$ minikube service frontend -n guestbook-demo
|--------------|--------|-----------|---------------------------|
|   NAMESPACE  |  NAME  |TARGET PORT|           URL             |
|--------------|--------|-----------|---------------------------|
|guestbook-demo|frontend|        80 |[http://192.168.99.100:31856](http://192.168.99.100:31856)|
|--------------|--------|-----------|---------------------------|[https://medium.com/p/fa9fdc6a3659/edit](https://medium.com/p/fa9fdc6a3659/edit)
```

🎉在默认浏览器中打开服务留言簿-演示/前端…

该命令将打开一个浏览器窗口，该窗口将访问前端服务 URL。该 URL 是创建服务时随机分配的 minikube IP 地址和端口。您现在可以测试留言簿应用程序了！

![](img/d251b1388e02e16bfe4321d1e84f6288.png)

# 结论

在本文中，我们将无人机部署从使用 *docker-compose* 的基本 docker 安装扩展到在本地 minikube 集群上使用 Helm charts 的 Kubernetes 部署。但是，这种无人机安装相当简单，它缺少一些生产部署中预期的功能，这些功能将在未来的帖子中介绍。Kubernetes 将允许我们做负载平衡、pod 水平扩展、应用程序版本推出和回滚等事情。

此外，Kubernetes 插件似乎还处于非常早期的开发阶段，我测试的那个插件可以工作，但缺少一些功能，比如创建名称空间，或者在单个清单文件中部署多个资源。我希望 Harness 在未来发布一个适当支持的部署插件。

总的来说，我对 Drone 的易部署性非常满意，即使是在 Kubernetes 集群上。在以后的文章中，我将继续探索在这个平台上可以用它做什么，以及它与更成熟的产品相比如何。