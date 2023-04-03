# 在 CentOS 7.6 VPS 上使用 HTTP/2 和 NGINX 设置高响应性站点

> 原文：<https://medium.com/quick-code/setting-up-a-highly-responsive-site-with-http-2-and-nginx-on-a-centos-7-6-vps-d9aa444820f7?source=collection_archive---------2----------------------->

*本教程首次发布* [*这里*](https://andreborud.com/setting-up-a-highly-responsive-ghost-blog-with/#top) *。*

![](img/9b5c53d1e10aef666608300942660626.png)

Photo by [Jordan Harrison](https://unsplash.com/@aligns?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

这是我的前两个教程的更新教程，讲述了如何在 digital ocean CentOS 7.6 VPS Droplet 上设置 Ghost 博客，并使用 HTTP/2.0 使服务器尽可能安全和响应迅速。

[如何用 NGINX 在 CentOs 7.3 上设置 http 2](/@andreborud/how-to-setup-http2-on-centos-7-3-with-nginx-13db65c3eaf)

[如何在新的 DigitalOcean CentOS 7.2 droplet 上使用 HTTPS 设置 Ghost on](/@andreborud/how-to-setup-ghost-on-with-https-on-a-fresh-digitalocean-centos-7-2-droplet-bd3baa1ca055)

在本教程中，我将介绍我采取的一些安全措施，以及如何设置一个响应迅速的 web 应用程序/站点，该应用程序/站点通过自动包含 HTTPS 的 HTTP2 提供服务。我将使用 NGINX 作为服务器，所有这些都将在数字海洋上托管的 CentOS 7.6 VPS 上完成。例如，我将安装一个 ghost 博客，但是如果你主要是在寻找如何在 CentOS 7.6 服务器上运行 HTTP/2，那么请跳到下面的 *Certbot* 和 *Nginx* 部分。

首先，我假设您有一个安装了 CentOS 7.5 的新 Droplet。在撰写本文时，CentOS 7.5 是 DigitalOcean 上可用的最高版本，尽管 CentOS 7.6 已经存在了一段时间。

首先，在您选择的终端中使用 ssh 登录到您的 VPS。

```
# ssh root@host to login and change your password
```

然后，首先，我们将使用以下命令更新 CentOS。

```
# yum update
```

然后，要检查我们使用的是 CentOS 的最新版本，请键入以下内容。

```
# cat /etc/centos-release
```

其在写入时输出:

```
CentOS Linux release 7.6.1810 (Core)
```

# 防火墙

接下来，我们将安装一个防火墙，以便对我们希望向公众开放的内容进行一些额外的控制。Yum 是 CentOS 的“应用商店”,在这里您可以下载应用程序并将其安装到您的操作系统中。而 systemctl 可以启动和停止已安装的应用程序或服务。如果服务器重新启动，systemctl enable 可确保您的应用程序启动，并且 status 会检查应用程序的当前状态。以下命令将安装、启动、启用和检查我们将要安装的应用程序 firewalld 的状态。

```
# yum install firewalld
# systemctl start firewalld
# systemctl enable firewalld
# systemctl status firewalld
```

Status 应该输出类似下面的内容。

```
firewalld.service — firewalld — dynamic firewall daemon
 Loaded: loaded (/usr/lib/systemd/system/firewalld.service; enabled; vendor preset: enabled)
 Active: active (running) since Fri 2018–12–28 15:01:12 UTC; 11s ago
 Docs: man:firewalld(1)
 Main PID: 8509 (firewalld)
 CGroup: /system.slice/firewalld.service
 └─8509 /usr/bin/python -Es /usr/sbin/firewalld — nofork — nopidDec 28 15:01:12 temp-test systemd[1]: Starting firewalld — dynamic firewall daemon…
Dec 28 15:01:12 temp-test systemd[1]: Started firewalld — dynamic firewall daemon.
```

# 更改 ssh 端口

作为额外的安全措施，我喜欢改变用于 ssh 连接的标准端口。我这样做是因为如果我保持端口 22 和远程 root 访问启用，我会有数百次不成功的尝试登录到我的服务器上的 root 帐户。从统计上看，如果你有一个安全的密码，任何人都不可能登录，但是让我们更安全一点。

让我们编辑 ssh 配置文件。我喜欢的文本编辑器是“vi ”,但是你可以使用你喜欢的任何一个。如果你是“vi”的新手，你只需要知道几个命令就可以使用它。

文件打开后，按下 **i** 开始编辑文件。你可以用箭头键移动。一旦你完成编辑，按下 **ESC** 键并键入:和 **wq** 键并按下**回车键/回车键**。 **w** 代表*写*和 **q** 代表*退出*。有些时候，如果你已经做了更改，不想保存它们，但只是退出，添加一个**！**后 **q** 强制退出不做修改(如是 **:q！**)。

```
# vi /etc/ssh/sshd_config
```

找到并取消注释 **#Port 22** (删除该行开头的#)并将端口号更改为您想要的任何值，在本例中我将使用端口 2244。

CentOS 附带 SELinux(安全增强的 Linux ),这是一个内核模块，用于管理 Linux 中的安全策略。我们需要做的是允许 sshd 使用端口 2244。我们可以这样做。

```
# semanage port -a -t ssh_port_t -p tcp 2244
```

我们还需要在防火墙中打开这个端口，然后重新加载防火墙以应用更改。

```
# firewall-cmd --permanent --zone=public --add-port=2244/tcp
# firewall-cmd --reload
```

最后，我们将重新启动 ssh 服务，应用使用端口 2244 而不是端口 22 的更改。

```
# systemctl restart sshd
```

现在，您可以从您的服务器注销，并尝试像第一次一样重新连接到您的服务器，看看它是否被阻止，然后尝试使用新端口登录。
要使用不同端口的 ssh，请在登录时添加“-p ####”以结束。

```
# ssh root@host -p 2244
```

# 新用户

接下来，我们将添加另一个用户，因为使用 root 用户远程登录到您的服务器不是一个好的做法。让我们称这个用户为 toor。

```
# useradd toor
# passwd toor
# usermod -aG wheel toor
```

useradd 创建用户。passwd 允许您向用户添加密码。并且 usermod -aG wheel 授予您的新用户 sudo 权限。现在试着注销，然后用你的新用户登录，看看它能不能工作。

```
# ssh toor@host -p 2244
```

现在我们将允许 ssh 中的证书登录。为此，我们需要再次修改 sshd_config 文件。

```
# sudo vi /etc/ssh/sshd_config
```

因为我们不再是根用户，你需要在你的用户目录之外所做的改变之前添加 sudo。系统将提示您输入密码。

找到带有‘pubkeyauthentication’的行，删除开头的#以取消注释，并确保它后面显示 yes

```
PubkeyAuthentication yes
```

保存并退出 vi。并重新启动 sshd 服务。

```
# sudo systemctl restart sshd
```

如果这工作正常，然后再次注销，我们将创建一个证书，允许无密码登录到您的服务器，使生活稍微好一点。
要创建新的证书，请键入以下内容。

```
# ssh-keygen -t rsa
```

当它要求输入密码时，只需按 enter 键即可创建一个无密码证书。这个命令在 Mac 和 Linux 上都有效。然而，Windows 的命令略有不同，但有一种方法可以在 Windows 10 中原生获得 Ubuntu Bash 终端。如果你还没有，我建议你尝试一下。如何启用可以按照这个教程:[https://www . laptopmag . com/articles/use-bash-shell-windows-10](https://www.laptopmag.com/articles/use-bash-shell-windows-10)

接下来，我们要将证书复制到服务器。对此有一个非常简单的命令。

```
# ssh-copy-id -i ~/.ssh/id_rsa toor@host -p 2244
```

如果成功，将再次尝试登录。

```
# ssh toor@host -p 2244
```

这应该会让你直接进入你的服务器，而不需要密码。
下一步我们将确保不再允许根用户登录。如果你愿意，你也可以禁用密码登录到您的服务器。这意味着登录的唯一方式是使用您的证书。如果你丢失了它，或者你试图从另一台计算机访问你的服务器，你就完了。不过 DigitalOcean 确实有一个备份计划，如果你被锁定，你可以登录到他们的控制面板，通过这种方式获得根 ssh 访问。我把自己锁在了制作本教程的门外，按错误的顺序完成了一些步骤，但是通过在线终端再次获得了访问权限。

现在让我们禁用远程根登录

```
# sudo vi /etc/ssh/sshd_config
```

找到带有 PermitRootLogin 的行，取消对它的注释，并确保它在后面说 no。

```
#PermitRootLogin yes -> PermitRootLogin no
```

然后，如果您愿意，您可以找到写有 PasswordAuthentication yes 的行，并将其更改为

```
PasswordAuthentication 
```

这将禁止任何使用密码通过 ssh 登录到您的服务器的尝试。
保存并退出。并重新启动 sshd 服务。

```
# sudo systemctl restart sshd
```

如果您需要访问您的 root 帐户，请使用您的普通帐户登录，然后只需键入 **su** 和您的 root 密码。

# 节点. js

接下来，我们将安装 node.js。有多种方法可以通过 yum、nvm 等完成。就我个人而言，我更喜欢 nvm(节点版本管理器),因为以后如果需要的话，更改节点版本会更容易。nvm 只为当前登录的用户安装节点，这意味着您可以让多个用户使用不同的节点版本，如果您了解这一点，这将非常方便。

首先前往[https://github.com/creationix/nvm](https://github.com/creationix/nvm)查看最新版本是什么。写这篇文章的时候是 v0.34.0。通过编写安装它

```
# curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.34.0/install.sh | bash
```

然后选择您的节点版本。我通常会选择最新的 LTS 版本。在[https://nodejs.org/en/](https://nodejs.org/en/)检查在编写本文件时哪个版本是 v10.15.1

```
# nvm install 10.15.1
# node -v
```

node -v 应该输出版本。

# 关系型数据库

Ghost 需要一个 MySQL 数据库来运行，所以接下来我们将安装它。你知道 MySQL 中的 My 实际上是造物主的女儿的名字，而不是像英语中的 My(我的)那样发音吗？这个人有两个女儿，你可能听说过一个 MySQL 的替代品叫做 MariaDB，以他的第二个女儿命名。

从前往[https://dev.mysql.com/downloads/repo/yum/](https://dev.mysql.com/downloads/repo/yum/)开始，滚动到底部，找到最新版本。它用一个很小的文字在括号里写着，Linux 发行版在哪里，在 Red Hat enterprice 下的 CentOS check。在写的时候版本是**MySQL 80-community-release-el7–2 . no arch . rpm**。

下载安装包类型。

```
# wget https://dev.mysql.com/get/mysql80-community-release-el7-2.noarch.rpm
```

如果没有安装 wget，你可以用它来下载，然后用上面的命令下载 MySQL。

```
# sudo yum install wget
```

作为额外的安全措施，您可以使用以下命令检查下载包的 MD5 校验和

```
# md5sum mysql80-community-release-el7–2.noarch.rpm
```

然后将结果与我们检查最新 MySQL 版本的网站上同一行的校验和进行比较。如果还是一样，继续安装 mysql，用 systemctl 启动并启用数据库。

```
# sudo rpm -ivh mysql80-community-release-el7–2.noarch.rpm
# sudo yum install mysql-server
# sudo systemctl start mysqld
# sudo systemctl status mysqld
# sudo systemctl enable mysqld
```

在安装过程中，MySQL 为我们需要找到的 root 用户生成了一个临时密码。我们可以这样得到它

```
# sudo grep ‘temporary password’ /var/log/mysqld.log
```

复制密码并继续安装。

```
# sudo mysql_secure_installation
```

首先输入临时密码。然后创建一个新的密码，写下来并存储在某个安全的地方，如果你失去了它只是一个痛苦的创建一个新的没有丢失数据库中的数据。
出于某种原因，我被要求输入两次新的 root 密码。一旦你完成了密码部分，只要对所有问题回答是。

然后，我们将为 ghost 创建一个新的用户和数据库。首先以 root 用户身份登录 MySQL shell。

```
# mysql -u root -p
```

然后我们将创建一个名为 ghost 的用户，密码为[@ ghost 3 ghost](http://twitter.com/ghost3spookY)。选择你想要的密码。我们将授予该用户所有权限。

```
# mysql> CREATE USER ‘ghost’@’localhost’ IDENTIFIED BY ‘[@ghost3spookY](http://twitter.com/ghost3spookY)’;
# mysql> GRANT ALL PRIVILEGES ON *.* TO ‘ghost’@’localhost’ WITH GRANT OPTION;
```

然后，ghost 是为稍微旧一点的 MySQL 版本创建的，但他们已经改变了一些访问 MySQL 的方式，但我们可以通过以下方式再次启用旧方式。

```
# mysql> ALTER USER ‘root’@’localhost’ IDENTIFIED WITH mysql_native_password BY ‘[@SecretPassword2](http://twitter.com/SecretPassword2)’;
# mysql> ALTER USER ‘ghost’@’localhost’ IDENTIFIED WITH mysql_native_password BY ‘[@ghost3spookY](http://twitter.com/ghost3spookY)’;
```

之后，我们将从 MySQL shell 中注销。使用新用户登录，并为 ghost 创建一个新数据库。

```
# mysql> \q
# mysql -u ghost -p
# mysql> CREATE DATABASE ghost_blog;
# mysql> \q
```

# 《人鬼情未了》

是时候安装 Ghost 了，为此我们需要 ghost-cli，我们可以这样安装

```
# npm install -g ghost-cli
```

由于 ghost-cli 不支持 CentOS 官方，我通常做一点黑客，使其与 CentOS 的工作。为了让它正常工作，我们将编辑一个 ghost-cli 文件。

```
# vi .nvm/versions/node/v10.15.1/lib/node_modules/ghost-cli/extensions/systemd/systemd.js
```

找到下面几行

```
isRunning() {
 return this.ui.sudo(`systemctl is-active ${this.systemdName}`)
 .then(() => true)
 .catch((error) => {
 // Systemd prints out “inactive” if service isn’t running
 // or “activating” if service hasn’t completely started yet
 if (error.stdout && error.stdout.match(/inactive|activating/)) {
```

在上面的最后一行，您可以看到 inactive|activating，我们将在这里添加 **`|unknown`** ,所以看起来像这样

```
if (error.stdout && error.stdout.match(/inactive|activating|unknown/))
```

保存并退出。不幸的是，每次更新 ghost-cli 时，您都需要重复这一操作。

Ghost cli 创建了一个新用户，我们需要让这个新用户访问您希望安装 Ghost 的文件夹。因此，我们将更改该文件夹的权限。在我的例子中，我将 ghost 安装在当前用户的主文件夹中的一个新文件夹中。因此，我们将更改主文件夹的权限，以便 ghost 用户可以访问它。

```
# sudo chmod 755 /home/toor
```

接下来创建一个安装 ghost 的文件夹。

```
# mkdir ghost && cd ghost
# ghost install
```

Ghost-cli 会抱怨你不在 Ubuntu 上，只要说是，然后继续，回答下面的所有问题，并匹配 MySQL 安装中使用的密码、用户和数据库，以及你网站的任何 url。

```
? Enter your blog URL: [https://myghostblog.com](https://myghostblog.com)
? Enter your MySQL hostname: localhost
? Enter your MySQL username: ghost
? Enter your MySQL password: [hidden]
? Enter your Ghost database name: ghost_blog
? Sudo Password [hidden]
? Do you wish to set up “ghost” mysql user? No
? Do you wish to set up Nginx? No
? Do you wish to set up Systemd? Yes
? Do you want to start Ghost? No
```

搞定了。安装了 Ghost。但是在我们开始之前，我们需要设置防火墙，SELinux 和 NGINX。我们还将获得一个证书，这样我们就可以使用 HTTPS 和 HTTP/2.0 来访问博客。
在防火墙中，我们希望打开 http 的默认端口 80 和 HTTPS 的默认端口 443，我们还希望将 HTTP 和 https 协议添加到防火墙中。执行以下命令，检查结果是否类似于下面的结果。

```
# sudo firewall-cmd --state
running# sudo firewall-cmd --get-default-zone
public# sudo firewall-cmd --get-active-zones
public
 interfaces: eth0# sudo firewall-cmd --list-all
public (active)
 target: default
 icmp-block-inversion: no
 interfaces: eth0
 sources: 
 services: ssh dhcpv6-client
 ports: 2244/tcp
 protocols: 
 masquerade: no
 forward-ports: 
 source-ports: 
 icmp-blocks: 
 rich rules:# sudo firewall-cmd --zone=public --permanent --add-service=http
success# sudo firewall-cmd --zone=public --permanent --add-service=https
success# sudo firewall-cmd --zone=public --permanent --add-port=80/tcp
success# sudo firewall-cmd --zone=public --permanent --add-port=443/tcp
success# sudo firewall-cmd --reload
success# sudo firewall-cmd --list-all
public (active)
 target: default
 icmp-block-inversion: no
 interfaces: eth0
 sources:
 services: ssh dhcpv6-client http https
 ports: 2244/tcp 80/tcp 443/tcp
 protocols:
 masquerade: no
 forward-ports:
 source-ports:
 icmp-blocks:
 rich rules:
```
```

就这样，防火墙准备好了。

# 让我们加密& Certbot

在本教程中，我将使用“让我们加密”来获得网站的免费证书，以便与 HTTPS 一起使用，但显然你可以使用任何你想要的提供商。以下两个命令将在您的服务器上安装 certbot。

```
# sudo yum install epel-release
# sudo yum install certbot-nginx
```

接下来，我们将为该服务器所连接的域以及您希望您的博客所在的位置申请一个证书。在这一步中，我希望你已经有了一个域，并且它连接到你当前连接的 droplet。

```
# sudo certbot certonly
```

这里你有三个选择，我通常选择:启动临时网络服务器。填写您和您的域名的所有信息。如果一切顺利，您将会收到一条很长的成功消息以及证书的安装位置。

# NGINX

现在是时候安装 NGINX 了，设置它通过 HTTP/2.0 为你的 ghost 博客服务，默认情况下包括 HTTPS，我们会将所有正常的 HTTP 流量重定向到 HTTPS。
Yum 中没有 NGINX repo，所以我们将从添加一个新的 repofile 开始，就像这样

```
# sudo vi /etc/yum.repos.d/nginx.repo
```

并将以下文本粘贴到文件中。

```
[nginx]
name=nginx repo
baseurl=[http://nginx.org/packages/centos/$releasever/$basearch/](http://nginx.org/packages/centos/$releasever/$basearch/)
gpgcheck=0
enabled=1
```

保存并退出，然后安装 nginx。

```
# sudo yum install nginx
# sudo systemctl status nginx
```

我们还没有启动 NGINX，所以状态应该是不活动的。然后我们将编辑 nginx 配置文件。我们想做的是将允许的文件上传大小的最小值更改为 50mb，默认值为 2mb，这样你就可以上传大于 2mb 的照片或文件到你的博客。然后，我们将为 https 添加一些设置，以使用哪些协议和密码。最后三行是为了激活一些缓存，这样你的博客可以得到更快的服务。注意任何重复的行。

```
# sudo vi /etc/nginx/nginx.conf
```

在带有` **#gzip on 的行后添加以下内容；**`

```
client_max_body_size 50M; ssl_session_cache shared:SSL:10m;
 ssl_session_timeout 10m;
 # Forward secrecy settings
 ssl_protocols TLSv1.2 TLSv1.3;
 ssl_prefer_server_ciphers on;
 ssl_ciphers “EECDH+ECDSA+AESGCM EECDH+aRSA+AESGCM EECDH+ECDSA+SHA384 EECDH+ECDSA+SHA256 EECDH+aRSA+SHA384 EECDH+aRSA
+SHA256 EECDH+aRSA+RC4 EECDH EDH+aRSA RC4 !aNULL !eNULL !LOW !3DES !MD5 !EXP !PSK !SRP !DSS !RC4”;ssl_stapling on;
ssl_stapling_verify on;include /etc/nginx/conf.d/*.conf;server {
 listen 127.0.0.1;
 server_name localhost;
}proxy_cache_path /home/toor/ghostcache levels=1:2 keys_zone=ghostcache:60m max_size=300m inactive=24h;
 proxy_cache_key “$scheme$request_method$host$request_uri”;
 proxy_cache_methods GET HEAD;
```

保存退出

接下来，我们将添加一个专门针对您要使用的域的配置文件，我在这里将其命名为“myghostblog.com”。想叫什么名字就叫什么名字。

```
# sudo vi /etc/nginx/conf.d/myghostblog.com.conf
```

你在下面看到的是我在我的域名上使用的设置。我主要是定义应该在端口 443 (https)上发生的一切，最后，我将端口 80 (http)上的任何请求重定向到端口 443。Ghost 通常运行在端口 2368 上，因为它运行在这个服务器上，所以我们会告诉 NGINX 将任何请求指向 [http://127.0.0.1:2368](http://127.0.0.1:2368) ，如果您使用不同的端口，只需在下面两个地方更改到该端口。

然后您可以看到 ssl_certificate 和…_key，它们是在 certbot 步骤中创建的密钥。将正确的文件位置放在下面。我们将再次为 https 指定一些密码和协议。

然后，在从位置开始的 4 个部分中，我们设置了一些缓存、一些响应头和物理文件位置，用于在服务器上提供一些博客内容。

因此，复制并粘贴以下部分与你的变化。

```
limit_req_zone $binary_remote_addr zone=one:10m rate=1r/s;
server {
 listen 443 ssl http2;
 listen [::]:443 ipv6only=on ssl http2; gzip off; server_name myghostblog.com; ssl_certificate /etc/letsencrypt/live/myghostblog.com/fullchain.pem;
 ssl_certificate_key /etc/letsencrypt/live/myghostblog.com/privkey.pem;
 ssl_dhparam /etc/nginx/ssl/dhparam.pem;
 ssl_ciphers “EECDH+ECDSA+AESGCM EECDH+aRSA+AESGCM EECDH+ECDSA+SHA384 EECDH+ECDSA+SHA256 EECDH+aRSA+SHA384 EECDH+aRSA+SHA256 EECDH+aRSA+RC4 EECDH EDH+aRSA RC4 !aNULL !eNULL !LOW !3DES !MD5 !EXP !PSK !SRP !DSS !RC4”;
 ssl_protocols TLSv1.2 TLSv1.3; add_header Strict-Transport-Security max-age=31536000;
 add_header X-Frame-Options SAMEORIGIN;location / {
 proxy_cache ghostcache;
 proxy_cache_valid 60m;
 proxy_cache_valid 404 1m;
 proxy_cache_bypass $http_cache_control;
 proxy_ignore_headers Set-Cookie;
 proxy_hide_header Set-Cookie;
 proxy_cache_use_stale error timeout invalid_header updating http_500 http_502 http_503 http_504;
 proxy_ignore_headers Cache-Control;
 add_header X-Cache-Status $upstream_cache_status;limit_req zone=one burst=20 nodelay;
 proxy_pass [http://127.0.0.1:2368](http://127.0.0.1:2368);
 proxy_set_header Host $http_host;
 proxy_set_header X-Real-IP $remote_addr;
 proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
 proxy_set_header X-Forwarded-Proto $scheme;
 proxy_buffering off;
}location ^~ /assets/ {
 root /home/toor/ghost/content/themes/casper;
}location ^~ /content/images/ {
 root /home/toor/ghost;
}location ^~ /ghost/ {
 proxy_set_header Host $http_host;
 proxy_set_header X-Real-IP $remote_addr;
 proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
 proxy_set_header X-Forwarded-Proto $scheme;
 proxy_pass [http://127.0.0.1:2368](http://127.0.0.1:2368);
 }
}server {
 listen 80;
 listen [::]:80 ipv6only=on;
 server_name myghostblog.com;
 return 301 [https://$server_name$request_uri](https://$server_name$request_uri);
}
```

然后保存退出。

在上面的文件中，您可能已经看到了一个名为 dhparam.pem 的文件的链接。这是一个额外的证书，用于增加 https 上的安全性，我们需要创建它，可以通过以下两个步骤来完成。

```
# sudo mkdir /etc/nginx/ssl
# sudo openssl dhparam -out /etc/nginx/ssl/dhparam.pem 4096
```

这可能需要一段时间，所以去拿点喝的。
完成后，我们还需要创建我们上面指定的缓存文件夹。

```
# sudo mkdir /home/toor/ghostcache
```

搞定了。现在我们要检查所有接下来的 NGINX 配置是否正确。只需键入 nginx -t，-t 用于测试。

```
# sudo nginx -t
```

希望能够输出

```
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful
```

这时你可以尝试启动 NGINX，但是很可能会失败。

```
# sudo systemctl start nginxJob for nginx.service failed because a configured resource limit was exceeded. See “systemctl status nginx.service” and “journalctl -xe” for details.
```

检查 NGINX 状态以找到问题。

```
# sudo systemctl status nginx.service● nginx.service — nginx — high performance web server
 Loaded: loaded (/usr/lib/systemd/system/nginx.service; disabled; vendor preset: disabled)
 Active: failed (Result: resources) since Wed 2019–02–13 11:59:42 UTC; 19s ago
 Docs: [http://nginx.org/en/docs/](http://nginx.org/en/docs/)
 Process: 28161 ExecStart=/usr/sbin/nginx -c /etc/nginx/nginx.conf (code=exited, status=0/SUCCESS)Feb 13 11:59:41 temp-test systemd[1]: Starting nginx — high performance web server…
Feb 13 11:59:42 temp-test nginx[28161]: nginx: [emerg] open() “/var/run/nginx.pid” failed (13: Permission denied)
Feb 13 11:59:42 temp-test systemd[1]: Failed to read PID from file /var/run/nginx.pid: Invalid argument
Feb 13 11:59:42 temp-test systemd[1]: Failed to start nginx — high performance web server.
Feb 13 11:59:42 temp-test systemd[1]: Unit nginx.service entered failed state.
Feb 13 11:59:42 temp-test systemd[1]: nginx.service failed.selinux and nginx problem: nginx: [emerg] open() “/var/run/nginx.pid” failed (13: Permission denied)
```

你可以看到 SELinux 正在阻止 NGINX，所以我们需要让 SELinux 知道 NGINX 很酷。为此，我们需要安装 policycoreutils-devel 并允许 NGINX 运行。

```
# sudo yum install -y policycoreutils-devel
# sudo grep nginx /var/log/audit/audit.log | audit2allow -M nginx
# sudo semodule -i nginx.pp
```

再次尝试启动 NGINX。

```
# sudo systemctl start nginx
# sudo systemctl status nginx.service● nginx.service — nginx — high performance web server
 Loaded: loaded (/usr/lib/systemd/system/nginx.service; disabled; vendor preset: disabled)
 Active: active (running) since Wed 2019–02–13 12:05:49 UTC; 5s ago
 Docs: [http://nginx.org/en/docs/](http://nginx.org/en/docs/)
 Process: 28917 ExecStart=/usr/sbin/nginx -c /etc/nginx/nginx.conf (code=exited, status=0/SUCCESS)
 Main PID: 28918 (nginx)
 CGroup: /system.slice/nginx.service
 ├─28918 nginx: master process /usr/sbin/nginx -c /etc/nginx/nginx.conf
 ├─28919 nginx: worker process
 ├─28920 nginx: cache manager process
 └─28921 nginx: cache loader processFeb 13 12:05:49 temp-test systemd[1]: Starting nginx — high performance web server…
Feb 13 12:05:49 temp-test systemd[1]: PID file /var/run/nginx.pid not readable (yet?) after start.
Feb 13 12:05:49 temp-test systemd[1]: Started nginx — high performance web server.
```

现在 nginx 正在运行。尝试访问您的域名！

它应该向您显示 502 错误网关错误，因为我们还没有启动 ghost。但在此之前，我们需要允许 http 使用 ghost 在 SELinux 中使用的端口。

```
# sudo semanage port --add --type http_port_t --proto tcp 2368
```

我们还需要允许 nginx 直接服务文件，并允许它缓存文件，这也是我们需要告诉 SELinux 的。

```
# sudo chcon -R -t httpd_sys_content_t /home/toor/ghost
```

最后，让我们开始鬼。

```
# cd ghost
# ghost start
```

再次访问您的域名！

让我知道你是否喜欢这个教程。