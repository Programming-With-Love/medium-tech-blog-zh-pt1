# 将 Kubernetes 配置图资源用于动态应用程序

> 原文：<https://medium.com/capital-one-tech/using-kubernetes-configmap-resources-for-dynamic-apps-9e23ef589121?source=collection_archive---------0----------------------->

![](img/b47784c2c40e1003f7b5899116baf504.png)

# 什么是配置图？

根据[文档](https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/)，在 Kubernetes 中，ConfigMap 资源*“允许您将配置工件从图像内容中分离出来，以保持容器化的应用程序的可移植性。”*与 Kubernetes pods 一起使用，configmaps 可用于动态添加或更改容器使用的文件。

# 用例

作为 Kubernetes 安装程序的一部分，我们的团队希望为 Kubernetes 集群部署一个轻量级文件服务器来处理默认(根路径)入口请求。而且，我们认为，如果我们能够编辑 index.html 和 CSS 文件，而不必重新部署应用程序，那就太好了。

为了解决这个用例，我们决定构建一个 Golang 应用程序，将它的部分文件系统映射到 Kubernetes configmap 资源。

# Golang 文件服务器

文件服务器应用程序非常简单。它只是为了提供静态内容，帮助 Kubernetes 用户使用入口功能。

```
package mainimport (
“log”
“net/http”
)func main() {
fs := http.FileServer(http.Dir(“html”))
http.Handle(“/”, fs)log.Println(“Listening…”)
http.ListenAndServe(“:8080”, nil)
}
```

应用程序容器映像是用下面的`Dockerfile`构建的。它是一个两阶段 Dockerfile，首先在 Alpine 容器中执行 Golang 构建，然后将编译后的二进制文件和空的`html`目录复制到最终的基于暂存的映像。

```
# build stage  
FROM golang:alpine AS builder  
WORKDIR /usr/local/go/src  
COPY  main.go .  
RUN CGO_ENABLED=0 GOOS=linux go build -o main .  

# final stage  
FROM scratch  
WORKDIR /  
COPY --from=builder /usr/local/go/src/main main  
COPY html html  
EXPOSE 8080  
ENTRYPOINT ["/main"]
```

在 Golang 应用程序中使用 [scratch containers](https://blog.codeship.com/building-minimal-docker-containers-for-go-applications/) 是部署 Golang 容器的一种更加安全和轻量级的方法。

# 构建和运行

我使用`[make](https://www.gnu.org/software/make/manual/html_node/Introduction.html)`来自动化码头操作。下面是该应用的`Makefile`。

```
VERSION ?= 0.0.1  
NAME ?= "ingress-default"  
AUTHOR ?= "Jimmy Ray"  
PORT_EXT ?= 8080  
PORT_INT ?= 8080   
NO_CACHE ?= true  

.PHONY: build run stop clean  

build:  
docker build -f scratch.dockerfile . -t $(NAME)\:$(VERSION) --no-cache=$(NO_CACHE)  

run:  
docker run --name $(NAME) -d -p $(PORT_EXT):$(PORT_INT) $(NAME)\:$(VERSION) && docker ps -a --format "{{.ID}}\t{{.Names}}"|grep $(NAME)  

stop:  
docker rm $$(docker stop $$(docker ps -a -q --filter "ancestor=$(NAME):$(VERSION)" --format="{{.ID}}"))  

clean:  
[@rm](http://twitter.com/rm) -f main  

DEFAULT: build
```

使用`make`可以消除重复性任务之间的可变性。有了上面的`Makefile`，在我将测试过的应用程序部署到 Kubernetes 之前，我可以在 Docker 中构建并运行我的应用程序。

# 配置 Kubernetes

对于这个解决方案，我们需要配置 Kubernetes 名称空间、configmap、部署、服务和入口。我们通过使用 [](https://kubernetes-v1-4.github.io/docs/user-guide/kubectl/kubectl_apply/) `[kubectl apply](https://kubernetes-v1-4.github.io/docs/user-guide/kubectl/kubectl_apply/) -f`方法来做到这一点。这是一种将更改应用到 Kubernetes 集群资源的声明性方法。

下面是我们将要购买的 Kubernetes 资源的 YAML 文件。

```
apiVersion: v1  
kind: Namespace  
metadata:  
  name: ingress-default 
  labels:  
    app: ingress-default  
---  
kind: ConfigMap  
apiVersion: v1  
metadata:  
  name: ingress-default-static-files  
  namespace: ingress-default   
  labels:  
    app: ingress-default   
data:  
  index.html: |  
    <!doctype html>  
    <html>  
      <head>  
        <meta charset="utf-8">  
        <title>Cluster Ingress Index</title>  
        <link rel="stylesheet" href="main.css">  
      </head>  
      <body>  
        <table class="class1">  
          <tr>  
            <td class="class2">Kubernetes Platform</td>  
          </tr>  
          <tr>  
            <td class="class1">  
              <table class="class3">  
                <tr><td><h1>Cluster Ingress Index</h1></td></tr>  
              </table>  
            </td>  
          </tr>  
          <tr>  
            <td>  
              <table class="class3">  
                <tr>  
                <td>  
                   <h2>The following are links to this cluster's ingress resources:</h2>  
                  </td>  
                </tr>  
                <tr>  
                <td class="class4">  
                    <a href="https://<ROOT_INGRESS_PATH>" target="_blank">Root Ingress</a><br/>  
                    <a href="https://<OTHER_INGRESS_PATH>" target="_blank">Other Ingress</a><br/>   
                 </td>  
               </tr>  
              </table>  
            </td>  
          </tr>  
         </table>  
      </body>  
    </html>  
  main.css: |  
    body {  
      background-color: rgb(224,224,224);  
      font-family: Verdana, Arial, Helvetica, sans-serif;  
      font-size: 100%;  
    }  
    .class1 {  
  ... 
    }  
    .class2 {  
  ... 
    }  
    .class3 {  
  ... 
    }  
    .class4 {  
     ...
    }   
---  
apiVersion: apps/v1  
kind: Deployment  
metadata:  
  labels:  
    app: ingress-default   
  name: ingress-default  
  namespace: ingress-default 
spec:  
  selector:  
    matchLabels:  
      app: ingress-default  
  replicas: 1  
  template:  
    metadata:  
      labels:  
        app: ingress-default    
      name: ingress-default  
    spec:  
      containers:  
        - name: ingress-default  
          image: <IMAGE_REGISTRY_REPO_TAG>  
          imagePullPolicy: Always  
          resources:  
            limits:  
              cpu: 100m  
              memory: 10Mi  
            requests:  
              cpu: 100m  
              memory: 10Mi  
          volumeMounts:  
            - readOnly: true  
              mountPath: html  
              name: html-files  
      volumes:  
        - name: html-files  
          configMap:  
            name: ingress-default-static-files  
---  
kind: Service  
apiVersion: v1  
metadata:  
  name: ingress-default  
  namespace: ingress-default  
  labels:  
    app: ingress-default  
spec:  
  selector:  
    app: ingress-default  
  ports:  
  - name: http  
    protocol: TCP  
    port: 80  
    targetPort: 8080  
---  
apiVersion: extensions/v1beta1  
kind: Ingress  
metadata:  
  name: default-ingress  
  namespace: ingress-default  
  annotations:   
    nginx.ingress.kubernetes.io/rewrite-target: /  
    kubernetes.io/ingress.class: "nginx"    
  labels:  
    app: ingress-default  
spec:  
  rules:  
  - http:  
      paths:  
      - path: /  
        backend:  
          serviceName: ingress-default  
          servicePort: 80
```

正如您在 YAML 中看到的，`ingress-default-static-files`配置图包含了`index.html`和`main.css`文件的内容。通过编辑或替换此配置图，我们可以更改 Golang 文件服务器应用程序提供的这些文件。

# 将配置图用作卷

在 Docker 和 Kubernetes 的世界中，体积用于解决两个问题:

1.  对持久文件系统的需求。
2.  需要在容器之间共享文件系统。

对于我们的解决方案，我们将部署的容器中的卷映射到 configmap 资源。在下面的代码片段中，`html-files`卷被配置为可能被 pod 中的所有容器使用。卷映射到在`ingress-default-static-files`配置图中配置的数据。

```
...volumes:  
     - name: html-files  
       configMap:  
         name: ingress-default-static-files...
```

一旦在 pod 级别配置了卷，我们就在容器级别配置卷挂载。该卷挂载映射到 pod 中配置的`html-files`卷。有了这个映射，应用程序容器现在可以访问 configmap 中的两个文件— `html/index.html`和`html/main.css`。

```
...volumeMounts:  
     - readOnly: true  
       mountPath: html  
       name: html-files
```

当 Golang 应用程序在 Kubernetes 集群中启动时，`ingress-default`入口规则会导致在 NGINX 入口控制器中配置一个上游规则。由此产生的路径将通过 NGINX 入口控制器将集群的边缘连接到`ingress-default`服务。该服务指向 Golang 文件服务器应用程序 pod。运行时，它在入口控制器的根路径上提供默认的 web 应用程序。如果需要更改此网页，我们只需编辑/替换 configmap 资源。

# 结论

容器编排的一个关键好处是有望消除供应和管理多个容器化工作负载所需的“无差别的繁重工作”。通过使用 Kubernetes 的声明性配置特性，如 ConfigMap，可以提高应用程序部署和集群状态更改的效率和速度。通过使用 ConfigMap 资源作为安装的卷，使用运行的容器，可以从容器中提取配置和内容，从而减少对映像重构和容器重新部署的需求。

# **相关**

*   [策略启用了开放策略代理的 Kubernetes】](/capital-one-tech/policy-enabled-kubernetes-with-open-policy-agent-3b612b3f0203)
*   [使用 Terraform 部署多个环境](/capital-one-tech/deploying-multiple-environments-with-terraform-kubernetes-7b7f389e622)
*   [使用 Terraform 进行多区域部署](/capital-one-tech/multi-region-deployments-with-terraform-kubernetes-a1f51bb96974)

*声明:这些观点仅代表作者个人观点。除非本帖中另有说明，否则 Capital One 不属于所提及的任何公司，也不被其认可。使用或展示的所有商标和其他知识产权都是其各自所有者的所有权。本文为 2018 首都一。*