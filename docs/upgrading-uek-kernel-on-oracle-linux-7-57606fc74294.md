# 在 Oracle Linux 7 上升级 UEK 内核

> 原文：<https://medium.com/oracledevs/upgrading-uek-kernel-on-oracle-linux-7-57606fc74294?source=collection_archive---------0----------------------->

![](img/af247bda5eb0f4c70aa09c9b3cc4c5b1.png)

总的来说，这些天我几乎只在苹果电脑上工作。然而，由于工作和其他各种原因，我总是倾向于在我的 Mac 上安装一个带有 Linux 的虚拟机来测试、运行和编写脚本。我的虚拟机在 VirtualBox 上运行 Oracle Linux (OL)。Oracle Linux 对我来说很有意义，因为我们在内部运行 Oracle Linux 上的所有东西，一般来说，它是一个可靠的 Linux 发行版。当然，偶尔我也必须更新我的 Linux 版本，以确保我的 Linux 环境是最新最好的。眼下又是这样一个时代，Oracle Linux 7 Unbreakable Enterprise Kernel 5 刚刚发布。过去，我总是有点害怕升级内核，因为我不知道自己在做什么。🙂事实证明，这比人们想象的要容易得多，以下是你的做法:

# TL；博士；医生

1.  下载新回购列表:`wget [http://yum.oracle.com/public-yum-ol7.repo](http://yum.oracle.com/public-yum-ol7.repo)`
2.  启用新的回购:`yum-config-manager --enable ol7_UEKR5`
3.  升级环境:`yum upgrade`
4.  重新启动环境:`reboot`

# 下载新的回购清单

我想在这篇文章中概述的是如何升级牢不可破的企业内核，短 UEK。步骤实际上相当简单。您需要访问`root`用户，因为此升级只能由`root.`执行

作为第一步，检查你已经拥有的总是好的。因此，让我们来看看 yum 的当前存储库列表是什么样子的，方法是在终端上输入`root`并键入`yum repolist`:

```
[root@localhost ~]# yum repolist
Loaded plugins: langpacks, ulninfo
repo id repo name status
ol7_UEKR4/x86_64 Latest Unbreakable Enterprise Kernel Release 4 for Oracle Linux 7Server (x86_64) 704
ol7_addons/x86_64 Oracle Linux 7Server Add ons (x86_64) 273
ol7_latest/x86_64 Oracle Linux 7Server Latest (x86_64) 26,884
repolist: 27,861
```

在我的示例中，我配置了三个存储库:

1.  UEK4 更新的`ol7_UEKR4`库
2.  各种插件的`ol7_addons`库，比如 Docker Engine。
3.  OL7 核心包最新版本的`ol7_latest`库

为了能够升级到新版本的 UEK 5，我必须为`yum`启用那个存储库(`ol7_UEKR5`)，这样它就可以看到新的包，下载它们并执行升级。幸运的是，Oracle 总是在网上的`[http://yum.oracle.com/public-yum-ol7.repo](http://yum.oracle.com/public-yum-ol7.repo)`保存有最新的 yum repositories 文件，其中包含最新的存储库信息。我所要做的就是将它下载到`yum`期望的正确目录中，也就是`/etc/yum.repos.d/`:

```
[root@localhost ~]# cd /etc/yum.repos.d/
[root@localhost yum.repos.d]# wget [http://yum.oracle.com/public-yum-ol7.repo](http://yum.oracle.com/public-yum-ol7.repo)
--2018-07-02 17:44:13-- [http://yum.oracle.com/public-yum-ol7.repo](http://yum.oracle.com/public-yum-ol7.repo)
Request sent, awaiting response... 200 OK
Length: 11687 (11K) [text/plain]
Saving to: <strong>‘public-yum-ol7.repo.1’</strong>

100%[========================================================================================================&gt;] 11,687 --.-K/s in 0s

2018-07-02 17:44:13 (76.2 MB/s) - ‘public-yum-ol7.repo.1’ saved [11687/11687]

[root@localhost yum.repos.d]# mv public-yum-ol7.repo.1 public-yum-ol7.repo
mv: overwrite ‘public-yum-ol7.repo’? y
[root@localhost yum.repos.d]# ls -al
total 32
drwxr-xr-x. 2 root root 65 Jul 2 17:44 .
drwxr-xr-x. 133 root root 8192 Jul 2 17:10 ..
-rw-r--r--. 1 root root 11687 Jun 21 22:26 public-yum-ol7.repo
```

注意`wget`不会覆盖原来的版本，而是保存新版本，并在结尾加上`.1`。您必须手动覆盖旧文件。然后是启用新存储库和禁用旧存储库的时候了:

```
[root@localhost yum.repos.d]# yum-config-manager --enable ol7_UEKR5
Loaded plugins: langpacks
================================================================ repo: ol7_UEKR5 =================================================================
[ol7_UEKR5]
async = True
bandwidth = 0
base_persistdir = /var/lib/yum/repos/x86_64/7Server
baseurl = [https://yum.oracle.com/repo/OracleLinux/OL7/UEKR5/x86_64/](https://yum.oracle.com/repo/OracleLinux/OL7/UEKR5/x86_64/)
cache = 0
cachedir = /var/cache/yum/x86_64/7Server/ol7_UEKR5
check_config_file_age = True
compare_providers_priority = 80
cost = 1000
deltarpm_metadata_percentage = 100
deltarpm_percentage =
enabled = 1
enablegroups = True
exclude =
failovermethod = priority
ftp_disable_epsv = False
gpgcadir = /var/lib/yum/repos/x86_64/7Server/ol7_UEKR5/gpgcadir
gpgcakey =
gpgcheck = True
gpgdir = /var/lib/yum/repos/x86_64/7Server/ol7_UEKR5/gpgdir
gpgkey = file:///etc/pki/rpm-gpg/RPM-GPG-KEY-oracle
hdrdir = /var/cache/yum/x86_64/7Server/ol7_UEKR5/headers
http_caching = all
includepkgs =
ip_resolve =
keepalive = True
keepcache = False
mddownloadpolicy = sqlite
mdpolicy = group:small
mediaid =
metadata_expire = 21600
metadata_expire_filter = read-only:present
metalink =
minrate = 0
mirrorlist =
mirrorlist_expire = 86400
name = Latest Unbreakable Enterprise Kernel Release 5 for Oracle Linux 7Server (x86_64)
old_base_cache_dir =
password =
persistdir = /var/lib/yum/repos/x86_64/7Server/ol7_UEKR5
pkgdir = /var/cache/yum/x86_64/7Server/ol7_UEKR5/packages
proxy = False
proxy_dict =
proxy_password =
proxy_username =
repo_gpgcheck = False
retries = 10
skip_if_unavailable = False
ssl_check_cert_permissions = True
sslcacert =
sslclientcert =
sslclientkey =
sslverify = True
throttle = 0
timeout = 30.0
ui_id = ol7_UEKR5/x86_64
ui_repoid_vars = releasever,
basearch
username =[root@localhost yum.repos.d]# yum-config-manager --disable ol7_UEKR4
Loaded plugins: langpacks
================================================================ repo: ol7_UEKR4 =================================================================
[ol7_UEKR4]
async = True
bandwidth = 0
base_persistdir = /var/lib/yum/repos/x86_64/7Server
baseurl = [https://yum.oracle.com/repo/OracleLinux/OL7/UEKR4/x86_64/](https://yum.oracle.com/repo/OracleLinux/OL7/UEKR4/x86_64/)
cache = 0
cachedir = /var/cache/yum/x86_64/7Server/ol7_UEKR4
check_config_file_age = True
compare_providers_priority = 80
cost = 1000
deltarpm_metadata_percentage = 100
deltarpm_percentage =
enabled = 0
enablegroups = True
exclude =
failovermethod = priority
ftp_disable_epsv = False
gpgcadir = /var/lib/yum/repos/x86_64/7Server/ol7_UEKR4/gpgcadir
gpgcakey =
gpgcheck = True
gpgdir = /var/lib/yum/repos/x86_64/7Server/ol7_UEKR4/gpgdir
gpgkey = file:///etc/pki/rpm-gpg/RPM-GPG-KEY-oracle
hdrdir = /var/cache/yum/x86_64/7Server/ol7_UEKR4/headers
http_caching = all
includepkgs =
ip_resolve =
keepalive = True
keepcache = False
mddownloadpolicy = sqlite
mdpolicy = group:small
mediaid =
metadata_expire = 21600
metadata_expire_filter = read-only:present
metalink =
minrate = 0
mirrorlist =
mirrorlist_expire = 86400
name = Latest Unbreakable Enterprise Kernel Release 4 for Oracle Linux 7Server (x86_64)
old_base_cache_dir =
password =
persistdir = /var/lib/yum/repos/x86_64/7Server/ol7_UEKR4
pkgdir = /var/cache/yum/x86_64/7Server/ol7_UEKR4/packages
proxy = False
proxy_dict =
proxy_password =
proxy_username =
repo_gpgcheck = False
retries = 10
skip_if_unavailable = False
ssl_check_cert_permissions = True
sslcacert =
sslclientcert =
sslclientkey =
sslverify = True
throttle = 0
timeout = 30.0
ui_id = ol7_UEKR4/x86_64
ui_repoid_vars = releasever,
basearch
username =
```

当下次发出`yum repolist`时，您现在应该看到`ol7_UEKR5`存储库，而不是`ol7_UEKR4`存储库:

```
[root@localhost yum.repos.d]# yum repolist
Loaded plugins: langpacks, ulninfo
ol7_UEKR5 | 1.2 kB 00:00:00
ol7_latest | 1.4 kB 00:00:00
(1/2): ol7_UEKR5/x86_64/updateinfo | 5.0 kB 00:00:00
(2/2): ol7_UEKR5/x86_64/primary | 627 kB 00:00:00
ol7_UEKR5 83/83
repo id repo name status
ol7_UEKR5/x86_64 Latest Unbreakable Enterprise Kernel Release 5 for Oracle Linux 7Server (x86_64) 83
ol7_latest/x86_64 Oracle Linux 7Server Latest (x86_64) 26,884
repolist: 26,967
```

请注意，`ol7_addons`存储库也消失了，因为它在默认情况下是不启用的。我可以很容易地通过`yum-config-manager --enable ol7_addons`再次启用它。

# 升级环境

下一步是升级环境。这将安装 UEK 的新版本，以及许多为新内核提供新版本的软件包。在这一点上，我应该指出，您不应该轻率地键入下面的命令，因为它将在 Linux 内核上执行完整的升级。`yum`实际上允许您决定是更新到最新的包还是删除旧的包，即`upgrade`，还是更新到最新的包并保留旧的包，即`update`。由于我对旧包没有任何需求，我通常选择`yum upgrade`，我们开始吧:

```
[root@localhost yum.repos.d]# yum upgrade
Loaded plugins: langpacks, ulninfo
ol7_UEKR5 | 1.2 kB 00:00:00
ol7_addons | 1.2 kB 00:00:00
ol7_latest | 1.4 kB 00:00:00
Resolving Dependencies
--> Running transaction check
---> Package ModemManager.x86_64 0:1.6.0-2.el7 will be updated
---> Package ModemManager.x86_64 0:1.6.10-1.el7 will be an update
---> Package ModemManager-glib.x86_64 0:1.6.0-2.el7 will be updated
---> Package ModemManager-glib.x86_64 0:1.6.10-1.el7 will be an update
---> Package NetworkManager.x86_64 1:1.4.0-20.el7_3 will be obsoleted
---> Package NetworkManager.x86_64 1:1.10.2-16.el7_5 will be obsoleting
---> Package NetworkManager-adsl.x86_64 1:1.4.0-20.el7_3 will be updated
...
...
...
xmlsec1 x86_64 1.2.20-7.el7_4 ol7_latest 177 k
xmlsec1-openssl x86_64 1.2.20-7.el7_4 ol7_latest 75 k
xz-libs i686 5.2.2-1.el7 ol7_latest 109 k
zlib i686 1.2.7-17.el7 ol7_latest 90 k

Transaction Summary
==================================================================================================================================================
Install 18 Packages (+96 Dependent packages)
Upgrade 780 Packages
Remove 2 Packages

Total download size: 906 M
Is this ok [y/d/N]: y
Downloading packages:
No Presto metadata available for ol7_UEKR5
No Presto metadata available for ol7_latest
(1/894): ModemManager-1.6.10-1.el7.x86_64.rpm | 735 kB 00:00:00
(2/894): ModemManager-glib-1.6.10-1.el7.x86_64.rpm | 231 kB 00:00:00
(3/894): NetworkManager-1.10.2-16.el7_5.x86_64.rpm | 1.7 MB 00:00:00
(4/894): NetworkManager-adsl-1.10.2-16.el7_5.x86_64.rpm | 158 kB 00:00:00
...
...
...
(890/894): yum-rhn-plugin-2.0.1-10.0.1.el7.noarch.rpm | 81 kB 00:00:00
(891/894): yum-utils-1.1.31-45.0.2.el7.noarch.rpm | 119 kB 00:00:00
(892/894): yelp-xsl-3.20.1-1.el7.noarch.rpm | 285 kB 00:00:01
(893/894): zenity-3.22.0-1.el7.x86_64.rpm | 743 kB 00:00:02
(894/894): zlib-1.2.7-17.el7.i686.rpm | 90 kB 00:00:01
--------------------------------------------------------------------------------------------------------------------------------------------------
Total 5.0 MB/s | 906 MB 00:03:01
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
Updating : libgcc-4.8.5-28.0.1.el7_5.1.x86_64 1/1685
Installing : 1:grub2-common-2.02-0.65.0.4.el7_4.2.noarch 2/1685
Updating : xkeyboard-config-2.20-1.el7.noarch 3/1685
Updating : 1:liberation-fonts-common-1.07.2-16.el7.noarch 4/1685
Updating : 1:control-center-filesystem-3.26.2-9.el7_5.x86_64 5/1685
Updating : 1:redhat-release-server-7.5-8.0.5.el7.x86_64 6/1685
Updating : 1:emacs-filesystem-24.3-20.el7_4.noarch 7/1685
Updating : mobile-broadband-provider-info-1.20170310-1.el7.noarch 8/1685
...
...
...
xorg-x11-xinit.x86_64 0:1.3.4-2.el7 yelp.x86_64 1:3.22.0-1.el7
yelp-libs.x86_64 1:3.22.0-1.el7 yelp-xsl.noarch 0:3.20.1-1.el7
yum.noarch 0:3.4.3-158.0.1.el7 yum-rhn-plugin.noarch 0:2.0.1-10.0.1.el7
yum-utils.noarch 0:1.1.31-45.0.2.el7 zenity.x86_64 0:3.22.0-1.el7

Replaced:
NetworkManager.x86_64 1:1.4.0-20.el7_3 grub2.x86_64 1:2.02-0.44.0.1.el7 grub2-tools.x86_64 1:2.02-0.44.0.1.el7
iproute.x86_64 0:3.10.0-74.0.1.el7 pygobject3.x86_64 0:3.14.0-3.el7 pygobject3-base.x86_64 0:3.14.0-3.el7
python-caribou.noarch 0:0.4.16-1.el7 rdma.noarch 0:7.3_4.7_rc2-6.el7_3 usbmuxd.x86_64 0:1.0.8-11.el7

Complete!
```

在`yum`完成升级后，您可以通过`yum list installed kernel-uek`再次验证 UEK 的升级是否成功:

```
[root@localhost yum.repos.d]# yum list installed kernel-uek
Loaded plugins: langpacks, ulninfo
Installed Packages
kernel-uek.x86_64 4.1.12-94.3.8.el7uek [@ol7_UEKR4](http://twitter.com/ol7_UEKR4)
kernel-uek.x86_64 4.14.35-1818.0.9.el7uek [@ol7_UEKR5](http://twitter.com/ol7_UEKR5)
```

输出显示来自`ol7_UEKR5`仓库的新`kernel-uek`版本已经成功安装。

# 重启环境

最后，也是最重要的一点，是时候重新引导操作系统使用新的内核了。这可以通过`reboot`命令轻松完成:

```
[root@localhost yum.repos.d]# reboot
Connection to localhost closed by remote host.
Connection to localhost closed.
```

重启后，您可以通过`uname -r`命令验证操作系统是否运行了最新的内核。您应该看到这个版本与`yum list installed kernel-uek`命令显示的`ol7_UEKR5`安装的内核的版本相匹配:

```
[root@localhost ~]# uname -r
4.14.35-1818.0.9.el7uek.x86_64
```

# 额外任务:卸载旧内核

当所有东西都是最新的并重新启动后，你就可以开始了。然而，你可能想要删除旧的内核，因为操作系统在升级自己的时候正在运行旧的内核。你不必这样做，但如果你想保持环境清洁，这是一件好事。通过输入`yum list kernel*`,我可以很快看到哪些内核还在安装:

```
[root@localhost ~]# yum list installed kernel*
Loaded plugins: langpacks, ulninfo
Installed Packages
kernel.x86_64 3.10.0-862.6.3.el7 [@ol7_latest](http://twitter.com/ol7_latest)
kernel-devel.x86_64 3.10.0-514.2.2.el7 [@ol7_latest](http://twitter.com/ol7_latest)
kernel-devel.x86_64 3.10.0-514.26.2.el7 [@ol7_latest](http://twitter.com/ol7_latest)
kernel-devel.x86_64 3.10.0-862.6.3.el7 [@ol7_latest](http://twitter.com/ol7_latest)
kernel-headers.x86_64 3.10.0-862.6.3.el7 [@ol7_latest](http://twitter.com/ol7_latest)
kernel-tools.x86_64 3.10.0-862.6.3.el7 [@ol7_latest](http://twitter.com/ol7_latest)
kernel-tools-libs.x86_64 3.10.0-862.6.3.el7 [@ol7_latest](http://twitter.com/ol7_latest)
kernel-uek.x86_64 4.1.12-94.3.8.el7uek [@ol7_UEKR4](http://twitter.com/ol7_UEKR4)
kernel-uek.x86_64 4.14.35-1818.0.9.el7uek [@ol7_UEKR5](http://twitter.com/ol7_UEKR5)
kernel-uek-devel.x86_64 4.1.12-61.1.23.el7uek [@ol7_UEKR4](http://twitter.com/ol7_UEKR4)
kernel-uek-devel.x86_64 4.1.12-94.3.8.el7uek [@ol7_UEKR4](http://twitter.com/ol7_UEKR4)
kernel-uek-devel.x86_64 4.14.35-1818.0.9.el7uek [@ol7_UEKR5](http://twitter.com/ol7_UEKR5)
kernel-uek-firmware.noarch 4.1.12-61.1.14.el7uek [@ol7_UEKR4](http://twitter.com/ol7_UEKR4)
kernel-uek-firmware.noarch 4.1.12-61.1.23.el7uek [@ol7_UEKR4](http://twitter.com/ol7_UEKR4)
kernel-uek-firmware.noarch 4.1.12-94.3.8.el7uek [@ol7_UEKR4](http://twitter.com/ol7_UEKR4)
```

上面的输出显示我安装了三个不同的内核版本:

1.  `kernel.x86_64` `3.10.0-862.6.3.el7`来自`@ol7_latest`资源库
2.  `kernel-uek.x86_64`来自`@ol7_UEKR4`储存库的`4.1.12-94.3.8.el7uek`
3.  `kernel-uek.x86_64`来自`@ol7_UEKR5`储存库的`4.14.35-1818.0.9.el7uek`

我实际上既不需要`kernel.x86_64`也不需要`kernel-uek.x86_64` `4.1.12-94.3.8.el7uek`版本，可以直接删除它们。幸运的是，`yum`将跳过当前运行的内核，所以我可以只做一个`yum remove kernel* kernel-uek*`:

```
[root@localhost ~]# yum remove kernel* kernel-uek*
Loaded plugins: langpacks, ulninfo
Skipping the running kernel: kernel-uek-4.14.35-1818.0.9.el7uek.x86_64
Skipping the running kernel: kernel-uek-4.14.35-1818.0.9.el7uek.x86_64
Resolving Dependencies
--> Running transaction check
---> Package kernel.x86_64 0:3.10.0-862.6.3.el7 will be erased
---> Package kernel-devel.x86_64 0:3.10.0-514.2.2.el7 will be erased
--> Processing Dependency: kernel-devel-uname-r for package: systemtap-devel-3.2-8.el7_5.x86_64
---> Package kernel-devel.x86_64 0:3.10.0-514.26.2.el7 will be erased
---> Package kernel-devel.x86_64 0:3.10.0-862.6.3.el7 will be erased
---> Package kernel-headers.x86_64 0:3.10.0-862.6.3.el7 will be erased
--> Processing Dependency: kernel-headers for package: 1:compat-glibc-headers-2.12-4.el7.x86_64
--> Processing Dependency: kernel-headers >= 2.2.1 for package: 1:compat-glibc-headers-2.12-4.el7.x86_64
--> Processing Dependency: kernel-headers for package: glibc-headers-2.17-222.el7.x86_64
--> Processing Dependency: kernel-headers >= 2.2.1 for package: glibc-headers-2.17-222.el7.x86_64
---> Package kernel-tools.x86_64 0:3.10.0-862.6.3.el7 will be erased
---> Package kernel-tools-libs.x86_64 0:3.10.0-862.6.3.el7 will be erased
---> Package kernel-uek.x86_64 0:4.1.12-94.3.8.el7uek will be erased
---> Package kernel-uek-devel.x86_64 0:4.1.12-61.1.23.el7uek will be erased
---> Package kernel-uek-devel.x86_64 0:4.1.12-94.3.8.el7uek will be erased
---> Package kernel-uek-devel.x86_64 0:4.14.35-1818.0.9.el7uek will be erased
---> Package kernel-uek-firmware.noarch 0:4.1.12-61.1.14.el7uek will be erased
---> Package kernel-uek-firmware.noarch 0:4.1.12-61.1.23.el7uek will be erased
---> Package kernel-uek-firmware.noarch 0:4.1.12-94.3.8.el7uek will be erased
--> Running transaction check
---> Package compat-glibc-headers.x86_64 1:2.12-4.el7 will be erased
--> Processing Dependency: compat-glibc-headers = 1:2.12-4.el7 for package: 1:compat-glibc-2.12-4.el7.x86_64
---> Package glibc-headers.x86_64 0:2.17-222.el7 will be erased
--> Processing Dependency: glibc-headers for package: glibc-devel-2.17-222.el7.x86_64
--> Processing Dependency: glibc-headers = 2.17-222.el7 for package: glibc-devel-2.17-222.el7.x86_64
---> Package systemtap-devel.x86_64 0:3.2-8.el7_5 will be erased
--> Processing Dependency: systemtap-devel = 3.2-8.el7_5 for package: systemtap-3.2-8.el7_5.x86_64
--> Running transaction check
---> Package compat-glibc.x86_64 1:2.12-4.el7 will be erased
---> Package glibc-devel.x86_64 0:2.17-222.el7 will be erased
--> Processing Dependency: glibc-devel for package: oracle-rdbms-server-12cR1-preinstall-1.0-6.el7.x86_64
--> Processing Dependency: glibc-devel >= 2.2.90-12 for package: gcc-4.8.5-28.0.1.el7_5.1.x86_64
---> Package systemtap.x86_64 0:3.2-8.el7_5 will be erased
--> Running transaction check
---> Package gcc.x86_64 0:4.8.5-28.0.1.el7_5.1 will be erased
--> Processing Dependency: gcc for package: libdtrace-ctf-0.8.0-1.el7.x86_64
--> Processing Dependency: gcc = 4.8.5-28.0.1.el7_5.1 for package: libquadmath-devel-4.8.5-28.0.1.el7_5.1.x86_64
--> Processing Dependency: gcc for package: libdtrace-ctf-0.8.0-1.el7.x86_64
--> Processing Dependency: gcc = 4.8.5-28.0.1.el7_5.1 for package: gcc-c++-4.8.5-28.0.1.el7_5.1.x86_64
--> Processing Dependency: gcc = 4.8.5-28.0.1.el7_5.1 for package: gcc-gfortran-4.8.5-28.0.1.el7_5.1.x86_64
--> Processing Dependency: gcc = 4.8.5 for package: libtool-2.4.2-22.el7_3.x86_64
---> Package oracle-rdbms-server-12cR1-preinstall.x86_64 0:1.0-6.el7 will be erased
--> Running transaction check
---> Package gcc-c++.x86_64 0:4.8.5-28.0.1.el7_5.1 will be erased
---> Package gcc-gfortran.x86_64 0:4.8.5-28.0.1.el7_5.1 will be erased
---> Package libdtrace-ctf.x86_64 0:0.8.0-1.el7 will be erased
---> Package libquadmath-devel.x86_64 0:4.8.5-28.0.1.el7_5.1 will be erased
---> Package libtool.x86_64 0:2.4.2-22.el7_3 will be erased
--> Finished Dependency Resolution

Dependencies Resolved

==================================================================================================================================================
Package Arch Version Repository Size
==================================================================================================================================================
Removing:
kernel x86_64 3.10.0-862.6.3.el7 [@ol7_latest](http://twitter.com/ol7_latest) 62 M
kernel-devel x86_64 3.10.0-514.2.2.el7 [@ol7_latest](http://twitter.com/ol7_latest) 34 M
kernel-devel x86_64 3.10.0-514.26.2.el7 [@ol7_latest](http://twitter.com/ol7_latest) 34 M
kernel-devel x86_64 3.10.0-862.6.3.el7 [@ol7_latest](http://twitter.com/ol7_latest) 37 M
kernel-headers x86_64 3.10.0-862.6.3.el7 [@ol7_latest](http://twitter.com/ol7_latest) 3.6 M
kernel-tools x86_64 3.10.0-862.6.3.el7 [@ol7_latest](http://twitter.com/ol7_latest) 278 k
kernel-tools-libs x86_64 3.10.0-862.6.3.el7 [@ol7_latest](http://twitter.com/ol7_latest) 18 k
kernel-uek x86_64 4.1.12-94.3.8.el7uek [@ol7_UEKR4](http://twitter.com/ol7_UEKR4) 153 M
kernel-uek-devel x86_64 4.1.12-61.1.23.el7uek [@ol7_UEKR4](http://twitter.com/ol7_UEKR4) 38 M
kernel-uek-devel x86_64 4.1.12-94.3.8.el7uek [@ol7_UEKR4](http://twitter.com/ol7_UEKR4) 38 M
kernel-uek-devel x86_64 4.14.35-1818.0.9.el7uek [@ol7_UEKR5](http://twitter.com/ol7_UEKR5) 63 M
kernel-uek-firmware noarch 4.1.12-61.1.14.el7uek [@ol7_UEKR4](http://twitter.com/ol7_UEKR4) 2.9 M
kernel-uek-firmware noarch 4.1.12-61.1.23.el7uek [@ol7_UEKR4](http://twitter.com/ol7_UEKR4) 2.9 M
kernel-uek-firmware noarch 4.1.12-94.3.8.el7uek [@ol7_UEKR4](http://twitter.com/ol7_UEKR4) 2.9 M
Removing for dependencies:
compat-glibc x86_64 1:2.12-4.el7 [@anaconda](http://twitter.com/anaconda)/7.2 6.7 M
compat-glibc-headers x86_64 1:2.12-4.el7 [@anaconda](http://twitter.com/anaconda)/7.2 2.0 M
gcc x86_64 4.8.5-28.0.1.el7_5.1 [@ol7_latest](http://twitter.com/ol7_latest) 37 M
gcc-c++ x86_64 4.8.5-28.0.1.el7_5.1 [@ol7_latest](http://twitter.com/ol7_latest) 16 M
gcc-gfortran x86_64 4.8.5-28.0.1.el7_5.1 [@ol7_latest](http://twitter.com/ol7_latest) 16 M
glibc-devel x86_64 2.17-222.el7 [@ol7_latest](http://twitter.com/ol7_latest) 1.0 M
glibc-headers x86_64 2.17-222.el7 [@ol7_latest](http://twitter.com/ol7_latest) 2.2 M
libdtrace-ctf x86_64 0.8.0-1.el7 [@ol7_UEKR5](http://twitter.com/ol7_UEKR5) 66 k
libquadmath-devel x86_64 4.8.5-28.0.1.el7_5.1 [@ol7_latest](http://twitter.com/ol7_latest) 18 k
libtool x86_64 2.4.2-22.el7_3 [@ol7_latest](http://twitter.com/ol7_latest) 2.2 M
oracle-rdbms-server-12cR1-preinstall x86_64 1.0-6.el7 [@ol7_latest](http://twitter.com/ol7_latest) 52 k
systemtap x86_64 3.2-8.el7_5 [@ol7_latest](http://twitter.com/ol7_latest) 199 k
systemtap-devel x86_64 3.2-8.el7_5 [@ol7_latest](http://twitter.com/ol7_latest) 7.3 M

Transaction Summary
==================================================================================================================================================
Remove 14 Packages (+13 Dependent packages)

Installed size: 563 M
Is this ok [y/N]: y
Downloading packages:
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
Erasing : oracle-rdbms-server-12cR1-preinstall-1.0-6.el7.x86_64 1/27
Erasing : 1:compat-glibc-2.12-4.el7.x86_64 2/27
Erasing : 1:compat-glibc-headers-2.12-4.el7.x86_64 3/27
Erasing : kernel-uek-4.1.12-94.3.8.el7uek.x86_64 4/27
Erasing : kernel-uek-devel.x86_64 5/27
Erasing : libtool-2.4.2-22.el7_3.x86_64 6/27
Erasing : kernel-uek-devel.x86_64 7/27
Erasing : systemtap-3.2-8.el7_5.x86_64 8/27
Erasing : kernel-uek-devel.x86_64 9/27
Erasing : systemtap-devel-3.2-8.el7_5.x86_64 10/27
Erasing : gcc-gfortran-4.8.5-28.0.1.el7_5.1.x86_64 11/27
Erasing : libquadmath-devel-4.8.5-28.0.1.el7_5.1.x86_64 12/27
Erasing : kernel-devel.x86_64 13/27
Erasing : kernel-uek-firmware.noarch 14/27
Erasing : kernel-uek-firmware.noarch 15/27
Erasing : kernel-devel.x86_64 16/27
Erasing : kernel-devel.x86_64 17/27
Erasing : kernel-3.10.0-862.6.3.el7.x86_64 18/27
Erasing : kernel-uek-firmware.noarch 19/27
Erasing : libdtrace-ctf-0.8.0-1.el7.x86_64 20/27
Erasing : gcc-c++-4.8.5-28.0.1.el7_5.1.x86_64 21/27
Erasing : gcc-4.8.5-28.0.1.el7_5.1.x86_64 22/27
Erasing : glibc-devel-2.17-222.el7.x86_64 23/27
Erasing : glibc-headers-2.17-222.el7.x86_64 24/27
Erasing : kernel-tools-3.10.0-862.6.3.el7.x86_64 25/27
Erasing : kernel-headers-3.10.0-862.6.3.el7.x86_64 26/27
Erasing : kernel-tools-libs-3.10.0-862.6.3.el7.x86_64 27/27
Verifying : kernel-uek-devel-4.14.35-1818.0.9.el7uek.x86_64 1/27
Verifying : 1:compat-glibc-headers-2.12-4.el7.x86_64 2/27
Verifying : gcc-gfortran-4.8.5-28.0.1.el7_5.1.x86_64 3/27
Verifying : systemtap-3.2-8.el7_5.x86_64 4/27
Verifying : systemtap-devel-3.2-8.el7_5.x86_64 5/27
Verifying : glibc-devel-2.17-222.el7.x86_64 6/27
Verifying : kernel-devel-3.10.0-514.26.2.el7.x86_64 7/27
Verifying : libquadmath-devel-4.8.5-28.0.1.el7_5.1.x86_64 8/27
Verifying : glibc-headers-2.17-222.el7.x86_64 9/27
Verifying : kernel-uek-devel-4.1.12-61.1.23.el7uek.x86_64 10/27
Verifying : 1:compat-glibc-2.12-4.el7.x86_64 11/27
Verifying : kernel-uek-4.1.12-94.3.8.el7uek.x86_64 12/27
Verifying : kernel-uek-firmware-4.1.12-61.1.14.el7uek.noarch 13/27
Verifying : kernel-tools-3.10.0-862.6.3.el7.x86_64 14/27
Verifying : kernel-tools-libs-3.10.0-862.6.3.el7.x86_64 15/27
Verifying : libtool-2.4.2-22.el7_3.x86_64 16/27
Verifying : kernel-uek-firmware-4.1.12-94.3.8.el7uek.noarch 17/27
Verifying : kernel-3.10.0-862.6.3.el7.x86_64 18/27
Verifying : oracle-rdbms-server-12cR1-preinstall-1.0-6.el7.x86_64 19/27
Verifying : kernel-uek-devel-4.1.12-94.3.8.el7uek.x86_64 20/27
Verifying : gcc-4.8.5-28.0.1.el7_5.1.x86_64 21/27
Verifying : kernel-devel-3.10.0-862.6.3.el7.x86_64 22/27
Verifying : kernel-devel-3.10.0-514.2.2.el7.x86_64 23/27
Verifying : kernel-uek-firmware-4.1.12-61.1.23.el7uek.noarch 24/27
Verifying : kernel-headers-3.10.0-862.6.3.el7.x86_64 25/27
Verifying : gcc-c++-4.8.5-28.0.1.el7_5.1.x86_64 26/27
Verifying : libdtrace-ctf-0.8.0-1.el7.x86_64 27/27

Removed:
kernel.x86_64 0:3.10.0-862.6.3.el7 kernel-devel.x86_64 0:3.10.0-514.2.2.el7
kernel-devel.x86_64 0:3.10.0-514.26.2.el7 kernel-devel.x86_64 0:3.10.0-862.6.3.el7
kernel-headers.x86_64 0:3.10.0-862.6.3.el7 kernel-tools.x86_64 0:3.10.0-862.6.3.el7
kernel-tools-libs.x86_64 0:3.10.0-862.6.3.el7 kernel-uek.x86_64 0:4.1.12-94.3.8.el7uek
kernel-uek-devel.x86_64 0:4.1.12-61.1.23.el7uek kernel-uek-devel.x86_64 0:4.1.12-94.3.8.el7uek
kernel-uek-devel.x86_64 0:4.14.35-1818.0.9.el7uek kernel-uek-firmware.noarch 0:4.1.12-61.1.14.el7uek
kernel-uek-firmware.noarch 0:4.1.12-61.1.23.el7uek kernel-uek-firmware.noarch 0:4.1.12-94.3.8.el7uek

Dependency Removed:
compat-glibc.x86_64 1:2.12-4.el7 compat-glibc-headers.x86_64 1:2.12-4.el7 gcc.x86_64 0:4.8.5-28.0.1.el7_5.1
gcc-c++.x86_64 0:4.8.5-28.0.1.el7_5.1 gcc-gfortran.x86_64 0:4.8.5-28.0.1.el7_5.1 glibc-devel.x86_64 0:2.17-222.el7
glibc-headers.x86_64 0:2.17-222.el7 libdtrace-ctf.x86_64 0:0.8.0-1.el7 libquadmath-devel.x86_64 0:4.8.5-28.0.1.el7_5.1
libtool.x86_64 0:2.4.2-22.el7_3 oracle-rdbms-server-12cR1-preinstall.x86_64 0:1.0-6.el7 systemtap.x86_64 0:3.2-8.el7_5
systemtap-devel.x86_64 0:3.2-8.el7_5

Complete!
```

就这样，我的环境升级到了最新版本的 UEK 内核，旧的也从系统中移除了。

*原载于 2018 年 7 月 9 日 geraldonit.com**的*[T22。](https://geraldonit.com/2018/07/09/upgrading-uek-kernel-on-oracle-linux-7/)