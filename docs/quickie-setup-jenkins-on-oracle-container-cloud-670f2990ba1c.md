# Quickie:使用 Docker 设置 Jenkins

> 原文：<https://medium.com/oracledevs/quickie-setup-jenkins-on-oracle-container-cloud-670f2990ba1c?source=collection_archive---------5----------------------->

在 [Oracle 容器云](http://cloud.oracle.com/container)上使用 Jenkins 实例非常容易。你可以利用 OCC 提供的现成的服务(名为**詹金斯**)或者创建自己的服务。在这个例子中，我们将创建一个新的服务(名称**-另一个-jenkins** ):

![](img/d41bdbf0e36734d7676ea69957f54d02.png)

使用现有的 Docker 图像或您选择的另一个图像(标签)(例如，来自 Docker Hub)

![](img/82d0aea4ade537ac43f76423023d2192.png)

# 地图卷

如果您想保存您的 Jenkins 数据(例如插件、配置等)。)容器重启后，您需要将容器路径映射到主机容器上的持久卷

Docker Hub Jenkins 图像将数据存储在`/var/jenkins_home`

这很容易做到，因为 Oracle Container Cloud 允许通过 SSH 访问 worker 节点和 Manager 节点

你需要做的就是下面的事情

## SSH 到您的工作节点

更多[细节在此](https://docs.oracle.com/en/cloud/iaas/container-cloud/contu/connecting-oracle-container-cloud-service-manager-and-worker-nodes-ssh.html#GUID-6B085862-5BA9-41D6-8B69-621E5F0D535F)

## 创建 Jenkins 数据目录

这需要在 worker 节点上完成，并且需要分配权限

```
cd /home/opc mkdir jenkins sudo chmod 777 jenkins
```

## 在 OCCS 詹金斯服务中配置卷

[更多信息请点击此处](http://docs.oracle.com/en/cloud/iaas/container-cloud/contu/service-configuration-option-reference.html)

![](img/1812c9342ab9cb94b6da7e9985df4a32.png)

## 部署服务

好了..现在只需点击**部署**来启动您的 Jenkins 容器。您应该会在**部署**列表中看到它

![](img/806e0d764d1874bcc2f114a86812d226.png)

# 访问詹金斯

## 获取管理员密码

在 Oracle 容器云中访问正在运行的 Jenkins 容器，并单击**查看日志**(向下滚动查看密码)

![](img/aa69e8a9676003de581e52f2ed873c1f.png)

grab the Jenkins admin password from the startup logs

Jenkins 容器公开端口 9002(默认情况下)。只需浏览到[**http://**](http://community.oracle.com/)**<occs-host-IP>:9002/**输入密码即可入门

![](img/bafccaa9096c03f1d167d8eb10511c4b.png)

根据您的要求配置 Jenkins

> 本文表达的观点是我个人的观点，不一定代表甲骨文的观点