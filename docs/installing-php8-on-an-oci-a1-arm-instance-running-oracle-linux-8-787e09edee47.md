# 在运行 Oracle Linux 8 的 OCI A1 Arm 实例上安装 PHP8

> 原文：<https://medium.com/oracledevs/installing-php8-on-an-oci-a1-arm-instance-running-oracle-linux-8-787e09edee47?source=collection_archive---------0----------------------->

![](img/38712b9e18156d95feabeb3b4502ea53.png)

Photo by [Jean van der Meulen from Pexels](https://www.pexels.com/photo/black-and-white-penguin-walking-on-sand-2078474/)

你喜欢 PHP 吗？也许您喜欢编写 PHP 应用程序，或者您需要使用众多使用 PHP 的开源应用程序之一？有太多的在这里列出，但你在一个很好的公司！下面是我们将在本文中看到的内容的一个快速快照:运行 Oracle Linux 8 的 A1 (Arm) OCI 实例上的 PHP8。

Oracle Linux 有 PHP 作为易于安装的 RPM ( *sudo dnf install php* )。让我们来看看:

```
$ dnf info php
Last metadata expiration check: 6:04:47 ago on Tue 03 May 2022 02:19:09 PM GMT.
Available Packages
Name         : php
Version      : 7.2.24
Release      : 1.module+el8.2.0+5510+6771133c
Architecture : aarch64
Size         : 1.4 M
Source       : php-7.2.24-1.module+el8.2.0+5510+6771133c.src.rpm
Repository   : ol8_appstream
Summary      : PHP scripting language for creating dynamic web sites
URL          : [http://www.php.net/](http://www.php.net/)
License      : PHP and Zend and BSD and MIT and ASL 1.0
Description  : PHP is an HTML-embedded scripting language. PHP attempts to make it
             : easy for developers to write dynamically generated web pages. PHP also
             : offers built-in database integration for several commercial and
             : non-commercial database management systems, so writing a
             : database-enabled webpage with PHP is fairly simple. The most common
             : use of PHP coding is probably as a replacement for CGI scripts.
             :
             : The php package contains the module (often referred to as mod_php)
             : which adds support for the PHP language to Apache HTTP Server.
```

如果您想使用 PHP7，这没问题，但是由于 PHP8 是最新的主要版本，有时可能需要 PHP8。如果你使用的是基于 x86_64(又名 amd64)的实例， [Remirepo](https://blog.remirepo.net/) 已经预建了 rpm，使得安装 PHP8 变得很容易。有一个问题——remi repo 没有 arm64(又名 aarch 64)rpm。

让 PHP8 在 arm64(和 OL8)上运行有几个选项:

1.  使用预构建的 RPM 安装
2.  从源代码编译

# 选项 1 —按 RPM 安装

选项 1 听起来是一个很棒的主意，除了没有一个众所周知的、可信的预构建 RPM 的来源(至少在我写这篇文章的时候是这样)。去找一个不可信的第三方 RPM 提供商可能会让人害怕，而且通常不是一个好主意。目前，这排除了这个选项。

# 选项 2 —从源代码编译

我们只能从源代码编译 PHP8。这并不可怕。它只是比预先构建的 RPM 路线更复杂和更慢一些。下面是实现这一点的“简单方法”。

您需要问问自己是否需要 Apache。对于许多 PHP 应用程序来说，它们是基于网络的，需要一个网络服务器。Apache 只是一个可以使用的 web 服务器(这是我在本文中挑选的)。请随意适应您的特定需求！

# 使用阿帕奇

[PHP 文档](https://www.php.net/manual/en/install.unix.apache2.php)有说明，这是基于。从安装 Apache 开始:

```
sudo dnf install -y httpd httpd-devel
sudo systemctl start httpd
```

现在打开所需的端口/协议:

```
sudo firewall-cmd --zone=public --add-port=80/tcp
sudo systemctl restart firewalld
```

请注意，您可能需要打开不同的端口。TCP/80 通常用于未加密的 HTTP(如果有的话，除了受控的简单测试之外，您真的不想使用它)。

从[https://www.php.net/downloads](https://www.php.net/downloads)获得最新的 PHP 下载网址，并用它来代替我下面提供的网址(以确保你得到你需要的最新最好的版本):

```
wget [https://www.php.net/distributions/php-8.1.4.tar.gz](https://www.php.net/distributions/php-8.1.4.tar.gz)
tar -xzvf php-8.1.4.tar.gz
cd php-8.1.4
```

显然，随着 PHP URL 的改变，文件名也会改变。您的里程数可能会有所不同，请确保引用正确的 URL 和文件名(因为这篇文章将会过时)！

接下来，让我们安装大量需要从源代码编译的开发工具:

```
sudo dnf group install -y "Development Tools"
sudo dnf install -y re2c bison autoconf make libtool ccache libxml2-devel sqlite-devel libxml2 sqlite sqlite-devel
```

这将安装*批*个软件包。这可能会减少很多，但除非你的存储空间非常紧张和/或互联网连接非常糟糕，这应该不是一个问题。

现在我们准备编译和安装:

```
./buildconf
./configure --with-apxs2=$(which apxs) --enable-opcache
make -j1 (should match number of cores - get via nproc)
make TEST_PHP_ARGS=-j4 test (set -j to # cores)
sudo make install
```

此时我们已经安装了 PHP，但是 Apache 不知道。是时候告诉阿帕奇如何处理了。php 文件。编辑(或创建)一个文件*/etc/httpd/conf . modules . d/00-PHP . conf*(你需要 sudo 来做这件事——类似于*sudo nano/etc/httpd/conf . modules . d/00-PHP . conf*或类似的东西)并将以下内容放入其中:

*/etc/httpd/conf . modules . d/00-PHP . conf:*

```
<FilesMatch \.php$>
    SetHandler application/x-httpd-php
</FilesMatch>
```

接下来需要重启 Apache:

```
sudo systemctl restart httpd
```

是时候进行测试并确保它正常工作了！为此，在 */var/www/html/index.php* 处创建一个新文件，并将以下内容放入其中:

*/var/www/html/index . PHP:*

```
<html>
<body>
<h1><?php print("Hello world!"); ?>
</body>
</html>
```

在你说之前…我知道。上面的 HTML 很恐怖，不完整(没有 head 标签等。).虽然它很短，但是它在浏览器中的表现很好！

要查看它的运行情况，请使用您的网络浏览器查看[http://<IP>/index . PHP](/oracledevs/<ip>/index.php)。这并不花哨，但它确实向我们展示了 PHP 与 Apache 配合得很好(呈现“Hello world！”屏幕上的文本)。

如果你只需要 PHP，而不需要 Apache 呢？很高兴你问了。这就是我们接下来要讨论的…

# 没有阿帕奇

从从[https://www.php.net/downloads](https://www.php.net/downloads)获得最新的 PHP 下载网址开始，用它代替下面的网址(确保你得到你需要的最新最好的版本):

```
wget [https://www.php.net/distributions/php-8.1.4.tar.gz](https://www.php.net/distributions/php-8.1.4.tar.gz)
tar -xzvf php-8.1.4.tar.gz
cd php-8.1.4
```

就像 Apache 安装一样，您需要使用正确的(更新的)URL 和文件名！

继续安装从源代码编译所需的许多开发工具:

```
sudo dnf group install -y "Development Tools"
sudo dnf install -y re2c bison autoconf make libtool ccache libxml2-devel sqlite-devel libxml2 sqlite sqlite-devel
```

这是“懒惰”的做法。Gcc 和 friends(以及其他需要的编译器)可以单独安装，可能会节省一点时间、存储和带宽。但由于存储和带宽相对便宜且高度可用，我选择了简单/懒惰的路线。

让我们编译并安装 PHP:

```
./buildconf
./configure
make -j1 (should match number of cores - get via nproc)
make TEST_PHP_ARGS=-j4 test (set -j to # cores)
sudo make install
```

瞧啊。我们成功了。PHP 已经安装好了，可以开始编写脚本了。

# 作为一个容器

有时候我们想在容器中部署一个 PHP8 应用程序。这里有一个 docker 文件，您可能会觉得有帮助(同样，请确保更新最新的 URL 和文件名):

```
ARG TARGETPLATFORM
FROM --platform=$TARGETPLATFORM oraclelinux:8-slim
RUN dnf update -y && dnf groupinstall "Development Tools" && dnf install -y sqlite sqlite-devel libxml2-devel libxml2 httpd httpd-devel
RUN cd /tmp && \
  wget -O /tmp/php-8.1.5.tar.gz [https://www.php.net/distributions/php-8.1.5.tar.gz](https://www.php.net/distributions/php-8.1.5.tar.gz) && \
  tar -xzvf /tmp/php-8.1.5.tar.gz -C /tmp && \
  cd /tmp/php-8.1.5 && \
  make clean && \
  ./buildconf && \
  ./configure --with-apxs2=$(which apxs) --enable-opcache && \
  make && \
  make install
RUN printf '<html>\n<body>\n<h1><?php print("Hello world!"); ?>\n</body>\n</html>' > /var/www/html/index.php && \
  printf '<FilesMatch \.php$>\n    SetHandler application/x-httpd-php\n</FilesMatch>' > /etc/httpd/conf.modules.d/00-php.conf
RUN systemctl restart httpd
RUN firewall-cmd --zone=public --add-port=80/tcp
RUN systemctl restart firewalld

EXPOSE 80/tcp
ENTRYPOINT []
CMD []
```

注意构建参数 *TARGETPLATFORM* 是如何使用的。这允许您轻松地为不同的体系结构构建容器。假设您在 amd64(又名 x86_64)系统上，想要为 arm64(又名 aarch64)构建一个容器映像。这可以通过以下方式实现:

```
docker build --build-arg TARGETPLATFORM=linux/arm64 -t php8_ol8:latest .
```

多酷啊。！是啊，我也喜欢！

# 结论

我们经历了一段美好的旅程。我们现在有办法在运行 Oracle Linux 8 的 Arm (A1) OCI 实例上轻松运行 PHP8。拥有针对实例和容器工作负载的解决方案意味着我们可以灵活地处理遇到的任何问题！保持位流，直到下一次…

想讨论一下吗？[加入我们的 Slack 频道](https://bit.ly/devrel_slack)，与其他开发者交流！