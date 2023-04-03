# Golang 编程语言设置和初级应用程序

> 原文：<https://medium.easyread.co/golang-programming-language-setup-and-basic-apps-for-beginners-bfec3960fb7d?source=collection_archive---------6----------------------->

![](img/70b6d7434537c70b4908300030c91aab.png)

Photo by [Émile Perron](https://unsplash.com/@emilep?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

伙计们。

这是我关于 golang 编程语言的第一集。在本文中，我将向您展示如何在您的环境中设置 golang 以及如何使用它。

# **安装**

点击此处下载 golang:

[](https://golang.org/dl/) [## 下载

### Go 是一种开源编程语言，它使构建简单、可靠和高效的软件变得容易。

golang.org](https://golang.org/dl/) 

下载后，双击并等待该过程完成。或者你可以用另一种方式安装 golang。

## 第一步:

打开终端，写下以下命令:

```
$ curl -o golang.pkg https://dl.google.com/go/go1.13.3.darwin-amd64.pkg
```

## 第二步:

包下载后，用以下命令执行它:

```
$ sudo open golang.pkg
```

## 第三步:

设置 Go 工作区打开文件。bash_profile。如果你不知道编辑它。你可以关注这篇文章:

 [## 我的麦克 Bash 狂欢简介

### 我花了几年时间收集 Mac bash 别名和快捷方式，让我的生活更轻松。我的全部。bash_profile…

natelandau.com](https://natelandau.com/my-mac-osx-bash_profile/) 

您需要注册 3 个环境变量作为 **GOROOT** 、 **GOPATH** 和 **PATH** 。

**GOROOT** 是您的 golang 安装位置。

```
$ export GOROOT=/usr/local/go
```

**GOPATH** 是您的 golang 项目的位置。

```
$ export GOPATH=$HOME/YOUR PROJECTS PATH
```

所以最后设置 **PATH** 变量来访问 go 二进制系统范围。

```
$ export PATH=$GOPATH/bin:$GOROOT/bin:$PATH
```

您可以在本文中看到详细配置。

[](https://tecadmin.net/install-go-on-macos/) [## 如何在 MacOS 上安装 Go 1.13-tec admin

### Go 是由谷歌的一个团队开发的开源编程语言。它提供了易于构建的简单、可靠的…

tecadmin.net](https://tecadmin.net/install-go-on-macos/) 

# 教程和示例

你可以从下面的资源学习 golang 编程语言:

 [## 围棋运动场

### Go Playground 是一个运行在 golang.org 服务器上的网络服务。该服务接收 Go 程序，vets…

play.golang.org](https://play.golang.org/) 

官方文件:

[](https://golang.org/doc/) [## 证明文件

### Go 编程语言是一个开源项目，旨在提高程序员的工作效率。围棋富有表现力，简洁…

golang.org](https://golang.org/doc/) 

其他文件:

[](https://devdocs.io/go/) [## DevDocs

### Go 1.13 API 文档，包含即时搜索、离线支持、键盘快捷键、移动版本等。

devdocs.io](https://devdocs.io/go/) 

或者可以从 youtube 的 Telusko 频道学习基本的 golang 编程语言。我认为这个频道对学习 golang 来说非常容易。

您可以尝试我在这个存储库中的示例代码:

[](https://github.com/irfanirawansukirman/basic-golang-telusko) [## irfanirawansukirman/basic-golang-telusko

### 此时您不能执行该操作。您已使用另一个标签页或窗口登录。您已在另一个选项卡中注销，或者…

github.com](https://github.com/irfanirawansukirman/basic-golang-telusko) 

这就是如何安装 golang 并尝试用它进行基本编程。我觉得够了。可能有用，享受！。

wassalamu ' alaykum Warahmatullahi Wabarakaatuh