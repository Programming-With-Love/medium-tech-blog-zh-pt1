# 在 RHEL 和 Debian 衍生产品上自动安装 Oracle JDK 8 和 10

> 原文：<https://medium.com/oracledevs/automate-the-installation-of-oracle-jdk-8-and-10-on-rhel-and-debian-derivatives-253e56ccaa53?source=collection_archive---------1----------------------->

在 RHEL 衍生产品(如 CentOS、Oracle Linux)和 Debian 衍生产品(如 Mint、Ubuntu)上自动安装 Oracle JDK 有所不同。这是由于不同的包管理器和存储库。在这篇博客中，我将快速介绍如何在不同的 Linux 发行版上自动安装 Oracle JDK 8 和 10。我选择了 JDK 8 和 10，因为它们是目前唯一接受公开更新的甲骨文 JDK 版本(见[此处](http://www.oracle.com/technetwork/java/javase/eol-135779.html))。

![](img/17511a5090687d8d92381ccfd11b2bca.png)

# 德邦衍生工具

使用下面的库的好处是你将经常得到最新的版本，并且如果你想的话，可以很容易地在现有的安装中更新到最新的版本。

## 甲骨文 JDK 8

```
sudo add-apt-repository ppa:webupd8team/java
sudo apt-get update
sudo echo debconf shared/accepted-oracle-license-v1-1 select true | sudo debconf-set-selections
sudo echo debconf shared/accepted-oracle-license-v1-1 seen true | sudo debconf-set-selections
sudo apt-get -y install oracle-java8-installer
sudo apt-get -y install oracle-java8-set-default
```

## 甲骨文 JDK 10

```
sudo add-apt-repository ppa:linuxuprising/java
sudo apt-get update
sudo echo debconf shared/accepted-oracle-license-v1-1 select true | sudo debconf-set-selections
sudo echo debconf shared/accepted-oracle-license-v1-1 seen true | sudo debconf-set-selections
sudo apt-get -y install oracle-java10-installer
sudo apt-get -y install oracle-java10-set-default
```

# RHEL 衍生品

由于 RHEL 衍生品通常由 RedHat 和 Oracle 等商业软件供应商提供，他们喜欢在订阅的基础上为自己的存储库工作，因为人们需要为使用它们付费。当然，特定存储库和订阅的配置因供应商和产品而异。对于 Oracle Linux，你可以在这里查看。关于 RedHat，你可以在这里查看。

以下描述的过程使您独立于供应商特定的订阅，但是您不会获得自动更新，如果您想要最新版本，您必须从[这里](http://www.oracle.com/technetwork/java/javase/downloads)手动更新下载 URL，并在替代命令中更新 Java 安装路径。您还可能会遇到所用 cookie 的有效性问题，这可能需要您更新 URL。

## 甲骨文 JDK 8

```
sudo wget -O ~/jdk8.rpm -N --no-check-certificate --no-cookies --header "Cookie: oraclelicense=accept-securebackup-cookie" http://download.oracle.com/otn-pub/java/jdk/8u181-b13/96a7b8442fe848ef90c96a2fad6ed6d1/jdk-8u181-linux-x64.rpm
sudo yum -y localinstall ~/jdk8.rpm
sudo update-alternatives --install /usr/bin/java java /usr/java/jdk1.8.0_181-amd64/jre/bin/java 1
sudo update-alternatives --install /usr/bin/jar jar /usr/java/jdk1.8.0_181-amd64/bin/jar 1
sudo update-alternatives --install /usr/bin/javac javac /usr/java/jdk1.8.0_181-amd64/bin/javac 1
sudo update-alternatives --install /usr/bin/javaws javaws /usr/java/jdk1.8.0_181-amd64/jre/bin/javaws 1
```

## 甲骨文 JDK 10

```
sudo wget -O ~/jdk10.rpm -N --no-check-certificate --no-cookies --header "Cookie: oraclelicense=accept-securebackup-cookie" http://download.oracle.com/otn-pub/java/jdk/10.0.2+13/19aef61b38124481863b1413dce1855f/jdk-10.0.2_linux-x64_bin.rpm
sudo yum -y localinstall ~/jdk10.rpm
sudo update-alternatives --install /usr/bin/java java /usr/java/jdk-10.0.2/bin/java 1
sudo update-alternatives --install /usr/bin/jar jar /usr/java/jdk-10.0.2/bin/jar 1
sudo update-alternatives --install /usr/bin/javac javac /usr/java/jdk-10.0.2/bin/javac 1
sudo update-alternatives --install /usr/bin/javaws javaws /usr/java/jdk-10.0.2/bin/javaws 1
```

*原载于 2018 年 7 月 27 日*[*javaoraclesoa.blogspot.com*](https://javaoraclesoa.blogspot.com/2018/07/automate-installation-of-oracle-jdk-8.html)*。*