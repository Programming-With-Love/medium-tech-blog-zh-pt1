# 在 Kubernetes 上设置 TLS/SSL

> 原文：<https://medium.easyread.co/setup-tls-ssl-on-kubernetes-8a682aff3b4d?source=collection_archive---------1----------------------->

![](img/69be20e4999a3c4e9eac3a692eba451e.png)

Photo by [Markus Winkler](https://unsplash.com/@markuswinkler?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

在这篇文章中，我将分享如何使用私有证书在 Kubernetes 上安装 TLS/SSL

# 步伐

在你关注这篇文章之前，请确保你的证书和私钥是有效的。如果您还没有任何证书和私钥，您应该生成它。之后，通过执行下面的命令创建一个 Kubernetes 秘密

```
$ kubectl create secret tls (tls secret name) --key (private key filename)  --cert (certificate filename)
```

将机密名称添加到入口配置中。该名称应该与上一步中的秘密名称相匹配

```
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: tls-example-ingress
spec:
  tls:
  - hosts:
      - example.com
    secretName: (tls secret name)
  rules:
  - host: example.com
    http:
      paths:
      - path: /
        backend:
          service:
            name: service1
            port:
              number: 80
```

# 可选择的

如果您已经安装了 ingress，有两种方法可以实现 TLS/SSL:

1.  编辑 ingress 的 YAML 配置文件，然后使用 kubectl 重新应用它

```
$ kubectl apply -f (name-file).yaml
```

2.通过执行以下命令，编辑集群上的现有配置

```
$ kubectl edit secret (tls name secret)
```

要验证是否安装了 TLS/SSL，您应该检查您的浏览器。