# 创建 Oracle 数据库 Docker 映像

> 原文：<https://medium.com/oracledevs/creating-an-oracle-database-docker-image-95ded5be894d?source=collection_archive---------2----------------------->

Oracle 在 Github 上发布了 Oracle 数据库的 [Docker 构建文件。有了这些构建文件，用户就可以为 Oracle 数据库构建自己的 Docker 映像了。如果你不知道 Docker](https://github.com/oracle/docker-images/tree/master/OracleDatabase) 是什么，你应该去看看。这是一项基于 Linux 容器技术的很酷的技术，它允许你容器化你的应用程序，不管这个应用程序是什么。自然，没过多久，人们也开始关注容器化数据库，这很有意义，尤其是对于开发和测试环境，但不仅限于此。这里有一篇关于如何使用 Oracle 提供的构建文件来封装 Oracle 数据库的详细博文。

# 你需要什么

*   Oracle 安装 zip 文件，你可以从 [Oracle 技术网](http://www.oracle.com/technetwork/database/enterprise-edition/downloads/index.html)下载
*   来自 Github 的构建文件，你可以[下载或者克隆库](https://github.com/oracle/docker-images/)

# 环境

我的环境如下:

*   Oracle Linux 7.3(4 . 1 . 12–94 . 3 . 8 . El 7 uek . x86 _ 64)
*   docker 17 . 03 . 1-ce(docker-engine . x86 _ 64 17 . 03 . 1 . ce-3 . 0 . 1 . el7)
*   Oracle 数据库 12.2.0.1 企业版

# Docker 设置

第一件事，如果还没有做的话，就是在环境中设置 Docker。幸运的是，这相当简单。Docker 是作为 Oracle Linux 7 UEK4 的一个插件提供的。当我在这样的环境中运行时，我所要做的就是启用`addons` yum 库并安装`docker-engine`包。注意，这是作为`root` Linux 用户完成的:

# 启用 OL7 附件报告

```
[root@localhost ~]# yum-config-manager enable *addons*
Loaded plugins: langpacks
================================================================== repo: ol7_addons ==================================================================
[ol7_addons]
async = True
bandwidth = 0
base_persistdir = /var/lib/yum/repos/x86_64/7Server
baseurl = [http://public-yum.oracle.com/repo/OracleLinux/OL7/addons/x86_64/](http://public-yum.oracle.com/repo/OracleLinux/OL7/addons/x86_64/)
cache = 0
cachedir = /var/cache/yum/x86_64/7Server/ol7_addons
check_config_file_age = True
compare_providers_priority = 80
cost = 1000
deltarpm_metadata_percentage = 100
deltarpm_percentage =
enabled = True
enablegroups = True
exclude =
failovermethod = priority
ftp_disable_epsv = False
gpgcadir = /var/lib/yum/repos/x86_64/7Server/ol7_addons/gpgcadir
gpgcakey =
gpgcheck = True
gpgdir = /var/lib/yum/repos/x86_64/7Server/ol7_addons/gpgdir
gpgkey = file:///etc/pki/rpm-gpg/RPM-GPG-KEY-oracle
hdrdir = /var/cache/yum/x86_64/7Server/ol7_addons/headers
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
name = Oracle Linux 7Server Add ons (x86_64)
old_base_cache_dir =
password =
persistdir = /var/lib/yum/repos/x86_64/7Server/ol7_addons
pkgdir = /var/cache/yum/x86_64/7Server/ol7_addons/packages
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
ui_id = ol7_addons/x86_64
ui_repoid_vars = releasever,
basearch
username =
```

# 安装对接发动机

```
[root@localhost ~]# yum install docker-engine
Loaded plugins: langpacks, ulninfo
Resolving Dependencies
--> Running transaction check
---> Package docker-engine.x86_64 0:17.03.1.ce-3.0.1.el7 will be installed
--> Processing Dependency: docker-engine-selinux >= 17.03.1.ce-3.0.1.el7 for package: docker-engine-17.03.1.ce-3.0.1.el7.x86_64
--> Running transaction check
---> Package selinux-policy-targeted.noarch 0:3.13.1-102.0.3.el7_3.16 will be updated
---> Package selinux-policy-targeted.noarch 0:3.13.1-166.0.2.el7 will be an update
--> Processing Dependency: selinux-policy = 3.13.1-166.0.2.el7 for package: selinux-policy-targeted-3.13.1-166.0.2.el7.noarch
--> Running transaction check
---> Package selinux-policy.noarch 0:3.13.1-102.0.3.el7_3.16 will be updated
---> Package selinux-policy.noarch 0:3.13.1-166.0.2.el7 will be an update
--> Finished Dependency Resolution

Dependencies Resolved

======================================================================================================================================================
Package Arch Version Repository Size
======================================================================================================================================================
Installing:
docker-engine x86_64 17.03.1.ce-3.0.1.el7 ol7_addons 19 M
Updating:
selinux-policy-targeted noarch 3.13.1-166.0.2.el7 ol7_latest 6.5 M
Updating for dependencies:
selinux-policy noarch 3.13.1-166.0.2.el7 ol7_latest 435 k

Transaction Summary
======================================================================================================================================================
Install 1 Package
Upgrade 1 Package (+1 Dependent package)

Total download size: 26 M
Is this ok [y/d/N]: y
Downloading packages:
No Presto metadata available for ol7_latest
(1/3): selinux-policy-3.13.1-166.0.2.el7.noarch.rpm | 435 kB 00:00:00
(2/3): selinux-policy-targeted-3.13.1-166.0.2.el7.noarch.rpm | 6.5 MB 00:00:01
(3/3): docker-engine-17.03.1.ce-3.0.1.el7.x86_64.rpm | 19 MB 00:00:04
------------------------------------------------------------------------------------------------------------------------------------------------------
Total 6.2 MB/s | 26 MB 00:00:04
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
Updating : selinux-policy-3.13.1-166.0.2.el7.noarch 1/5
Updating : selinux-policy-targeted-3.13.1-166.0.2.el7.noarch 2/5
Installing : docker-engine-17.03.1.ce-3.0.1.el7.x86_64 3/5
Cleanup : selinux-policy-targeted-3.13.1-102.0.3.el7_3.16.noarch 4/5
Cleanup : selinux-policy-3.13.1-102.0.3.el7_3.16.noarch 5/5
Verifying : selinux-policy-targeted-3.13.1-166.0.2.el7.noarch 1/5
Verifying : selinux-policy-3.13.1-166.0.2.el7.noarch 2/5
Verifying : docker-engine-17.03.1.ce-3.0.1.el7.x86_64 3/5
Verifying : selinux-policy-targeted-3.13.1-102.0.3.el7_3.16.noarch 4/5
Verifying : selinux-policy-3.13.1-102.0.3.el7_3.16.noarch 5/5

Installed:
docker-engine.x86_64 0:17.03.1.ce-3.0.1.el7

Updated:
selinux-policy-targeted.noarch 0:3.13.1-166.0.2.el7

Dependency Updated:
selinux-policy.noarch 0:3.13.1-166.0.2.el7

Complete!
```

就是这样！Docker 现已安装在机器上。在继续构建映像之前，我首先必须适当地配置我的环境。

# 启用非超级用户

我想做的第一件事是让一个非 root 用户能够与 Docker 引擎通信。启用非 root 用户也相当简单。安装 Docker 时，一个新的 Unix 组`docker`也随之创建。如果您希望允许用户直接与 Docker 守护进程通信，从而避免作为`root`用户运行，您所要做的就是将该用户添加到`docker`组。在我的例子中，我想将`oracle`用户添加到该组:

```
[root@localhost ~]# id oracle
uid=1000(oracle) gid=1001(oracle) groups=1001(oracle),1000(dba)
[root@localhost ~]# usermod -a -G docker oracle
[root@localhost ~]# id oracle
uid=1000(oracle) gid=1001(oracle) groups=1001(oracle),1000(dba),981(docker)
```

# 增加基本图像大小

在运行映像构建之前，我想仔细检查一个重要参数:Docker 容器的默认基本映像大小。过去，默认情况下，Docker 的最大容器大小为 10 GB。虽然这对于在 Docker 容器中运行一些应用程序来说已经足够了，但是对于 Oracle 数据库来说，这需要增加。Oracle 数据库 12.2.0.1 映像需要大约 13GB 的空间来构建映像。

最近，默认大小已经增加到 25GB，这对于 Oracle 数据库映像来说已经足够了。可以在`/etc/sysconfig/docker-storage`中找到并再次检查设置，作为`storage-opt dm.basesize`参数:

```
[root@localhost ~]# cat /etc/sysconfig/docker-storage
# This file may be automatically generated by an installation program.

# By default, Docker uses a loopback-mounted sparse file in
# /var/lib/docker. The loopback makes it slower, and there are some
# restrictive defaults, such as 100GB max storage.

# If your installation did not set a custom storage for Docker, you
# may do it below.

# Example: Use a custom pair of raw logical volumes (one for metadata,
# one for data).
# DOCKER_STORAGE_OPTIONS = --storage-opt dm.metadatadev=/dev/mylogvol/my-docker-metadata --storage-opt dm.datadev=/dev/mylogvol/my-docker-data
DOCKER_STORAGE_OPTIONS= --storage-driver devicemapper --storage-opt dm.basesize=25G
```

# 启动并启用 Docker 服务

最后一步是启动`docker`服务，并将其配置为在引导时启动。这是通过`systemctl`命令完成的:

```
[root@localhost ~]# systemctl start docker
[root@localhost ~]# systemctl enable docker
Created symlink from /etc/systemd/system/multi-user.target.wants/docker.service to /usr/lib/systemd/system/docker.service.
[root@localhost ~]# systemctl status docker
● docker.service - Docker Application Container Engine
Loaded: loaded (/usr/lib/systemd/system/docker.service; enabled; vendor preset: disabled)
Drop-In: /etc/systemd/system/docker.service.d
└─docker-sysconfig.conf
Active: active (running) since Sun 2017-08-20 14:18:16 EDT; 5s ago
Docs: [https://docs.docker.com](https://docs.docker.com)
Main PID: 19203 (dockerd)
Memory: 12.8M
CGroup: /system.slice/docker.service
├─19203 /usr/bin/dockerd --selinux-enabled --storage-driver devicemapper --storage-opt dm.basesize=25G
└─19207 docker-containerd -l unix:///var/run/docker/libcontainerd/docker-containerd.sock --metrics-interval=0 --start-timeout 2m --state...
```

最后一步，您可以通过`docker info`验证设置和基本图像尺寸(检查`Base Device Size:`):

```
[root@localhost ~]# docker info
Containers: 0
Running: 0
Paused: 0
Stopped: 0
Images: 0
Server Version: 17.03.1-ce
Storage Driver: devicemapper
Pool Name: docker-249:0-202132724-pool
Pool Blocksize: 65.54 kB
Base Device Size: 26.84 GB
Backing Filesystem: xfs
Data file: /dev/loop0
Metadata file: /dev/loop1
Data Space Used: 14.42 MB
Data Space Total: 107.4 GB
Data Space Available: 47.98 GB
Metadata Space Used: 581.6 kB
Metadata Space Total: 2.147 GB
Metadata Space Available: 2.147 GB
Thin Pool Minimum Free Space: 10.74 GB
Udev Sync Supported: true
Deferred Removal Enabled: false
Deferred Deletion Enabled: false
Deferred Deleted Device Count: 0
Data loop file: /var/lib/docker/devicemapper/devicemapper/data
WARNING: Usage of loopback devices is strongly discouraged for production use. Use `--storage-opt dm.thinpooldev` to specify a custom block storage device.
Metadata loop file: /var/lib/docker/devicemapper/devicemapper/metadata
Library Version: 1.02.135-RHEL7 (2016-11-16)
Logging Driver: json-file
Cgroup Driver: cgroupfs
Plugins:
Volume: local
Network: bridge host macvlan null overlay
Swarm: inactive
Runtimes: runc
Default Runtime: runc
Init Binary: docker-init
containerd version: 4ab9917febca54791c5f071a9d1f404867857fcc
runc version: 54296cf40ad8143b62dbcaa1d90e520a2136ddfe
init version: 949e6fa
Security Options:
seccomp
Profile: default
selinux
Kernel Version: 4.1.12-94.3.8.el7uek.x86_64
Operating System: Oracle Linux Server 7.3
OSType: linux
Architecture: x86_64
CPUs: 1
Total Memory: 7.795 GiB
Name: localhost.localdomain
ID: D7CR:3DGV:QUGO:X7EB:AVX3:DWWW:RJIA:QVVT:I2YR:KJXV:ALR4:WLBV
Docker Root Dir: /var/lib/docker
Debug Mode (client): false
Debug Mode (server): false
Registry: [https://index.docker.io/v1/](https://index.docker.io/v1/)
Experimental: false
Insecure Registries:
127.0.0.0/8
Live Restore Enabled: false
```

Docker 本身的安装到此结束。

# 构建 Oracle 数据库 Docker 映像

现在 Docker 已经启动并运行，我可以开始构建映像了。首先，我需要获得 Docker 构建文件和 Oracle 安装二进制文件，两者都很容易获得，如下所示。注意，我在以下所有步骤中使用了`oracle` Linux 用户，我之前已经启用了这个用户来与 Docker 守护进程通信:

# 获取所需的文件

# Github 构建文件

首先，我必须下载 Docker 构建文件。有各种方法可以做到这一点。例如，我可以直接克隆 Git 存储库。但是为了简单起见，对于不熟悉 git 的人，我只使用 Github 上的下载选项。如果你转到主存储库 URL[https://github.com/oracle/docker-images/](https://github.com/oracle/docker-images/)，你会看到一个绿色按钮，上面写着“`Clone or download`”，点击它，你会看到选项“`Download ZIP`”。或者，您也可以通过静态 URL 直接下载存储库:[https://github.com/oracle/docker-images/archive/master.zip](https://github.com/oracle/docker-images/archive/master.zip)

```
[oracle@localhost ~]$ wget [https://github.com/oracle/docker-images/archive/master.zip](https://github.com/oracle/docker-images/archive/master.zip)
--2017-08-20 14:31:32-- [https://github.com/oracle/docker-images/archive/master.zip](https://github.com/oracle/docker-images/archive/master.zip)
Resolving github.com (github.com)... 192.30.255.113, 192.30.255.112
Connecting to github.com (github.com)|192.30.255.113|:443... connected.
HTTP request sent, awaiting response... 302 Found
Location: [https://codeload.github.com/oracle/docker-images/zip/master](https://codeload.github.com/oracle/docker-images/zip/master) [following]
--2017-08-20 14:31:33-- [https://codeload.github.com/oracle/docker-images/zip/master](https://codeload.github.com/oracle/docker-images/zip/master)
Resolving codeload.github.com (codeload.github.com)... 192.30.255.120, 192.30.255.121
Connecting to codeload.github.com (codeload.github.com)|192.30.255.120|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: unspecified [application/zip]
Saving to: ‘master.zip’

[ ] 4,411,616 3.37MB/s in 1.2s

2017-08-20 14:31:34 (3.37 MB/s) - ‘master.zip’ saved [4411616]

[oracle@localhost ~]$ unzip master.zip
Archive: master.zip
21041a743e4b0a910b0e51e17793bb7b0b18efef
creating: docker-images-master/
extracting: docker-images-master/.gitattributes
inflating: docker-images-master/.gitignore
inflating: docker-images-master/.gitmodules
inflating: docker-images-master/CODEOWNERS
inflating: docker-images-master/CONTRIBUTING.md
...
...
...
creating: docker-images-master/OracleDatabase/
extracting: docker-images-master/OracleDatabase/.gitignore
inflating: docker-images-master/OracleDatabase/COPYRIGHT
inflating: docker-images-master/OracleDatabase/LICENSE
inflating: docker-images-master/OracleDatabase/README.md
creating: docker-images-master/OracleDatabase/dockerfiles/
...
...
...
inflating: docker-images-master/README.md
[oracle@localhost ~]$
```

# Oracle 安装二进制文件

对于 Oracle 二进制文件，只需从您通常下载它们的地方下载即可。[甲骨文科技网](http://www.oracle.com/technetwork/database/enterprise-edition/downloads/index.html)大概是大多数人去的地方。下载后，您可以继续构建映像:

```
[oracle@localhost ~]$ ls -al *database*zip
-rw-r--r--. 1 oracle oracle 1354301440 Aug 20 14:40 linuxx64_12201_database.zip
```

# 建立形象

现在我有了所有的文件，是时候构建 Docker 映像了。您将在`docker-images-master/OracleDatabase`目录中找到一个单独的 [README.md](https://github.com/oracle/docker-images/blob/master/OracleDatabase/README.md) ，它更详细地解释了构建过程。确保你总是阅读那个文件，因为它将总是反映构建文件中的最新变化！您还会在`docker-images-master/OracleDatabase/dockerfiles`目录中找到一个`buildDockerImage.sh` shell 脚本，它会为您完成构建的前期工作。对于构建，我必须将安装文件复制到正确的版本目录中。因为我要创建一个 Oracle 数据库 12.2.0.1 映像，所以我需要将安装 zip 文件复制到`docker-images-master/OracleDatabase/dockerfiles/12.2.0.1`:

```
[oracle@localhost ~]$ cd docker-images-master/OracleDatabase/dockerfiles/12.2.0.1/
[oracle@localhost 12.2.0.1]$ cp ~/linuxx64_12201_database.zip .
[oracle@localhost 12.2.0.1]$ ls -al
total 3372832
drwxrwxr-x. 2 oracle oracle 4096 Aug 20 14:44 .
drwxrwxr-x. 5 oracle oracle 77 Aug 19 00:35 ..
-rwxr-xr-x. 1 oracle oracle 1259 Aug 19 00:35 checkDBStatus.sh
-rwxr-xr-x. 1 oracle oracle 909 Aug 19 00:35 checkSpace.sh
-rw-rw-r--. 1 oracle oracle 62 Aug 19 00:35 Checksum.ee
-rw-rw-r--. 1 oracle oracle 62 Aug 19 00:35 Checksum.se2
-rwxr-xr-x. 1 oracle oracle 2964 Aug 19 00:35 createDB.sh
-rw-rw-r--. 1 oracle oracle 9203 Aug 19 00:35 dbca.rsp.tmpl
-rw-rw-r--. 1 oracle oracle 6878 Aug 19 00:35 db_inst.rsp
-rw-rw-r--. 1 oracle oracle 2550 Aug 19 00:35 Dockerfile.ee
-rw-rw-r--. 1 oracle oracle 2552 Aug 19 00:35 Dockerfile.se2
-rwxr-xr-x. 1 oracle oracle 2261 Aug 19 00:35 installDBBinaries.sh
-rw-r--r--. 1 oracle oracle 3453696911 Aug 20 14:45 linuxx64_12201_database.zip
-rwxr-xr-x. 1 oracle oracle 6151 Aug 19 00:35 runOracle.sh
-rwxr-xr-x. 1 oracle oracle 1026 Aug 19 00:35 runUserScripts.sh
-rwxr-xr-x. 1 oracle oracle 769 Aug 19 00:35 setPassword.sh
-rwxr-xr-x. 1 oracle oracle 879 Aug 19 00:35 setupLinuxEnv.sh
-rwxr-xr-x. 1 oracle oracle 689 Aug 19 00:35 startDB.sh
[oracle@localhost 12.2.0.1]$
```

现在 zip 文件已经准备好了，我准备调用`dockerfiles`文件夹中的`buildDockerImage.sh` shell 脚本。该脚本有两个参数，`-v`表示版本，`-e`表示我想要企业版。**注意:映像的构建将提取 Oracle Linux slim base 映像，并在容器内执行一个** `**yum install**` **和一个** `**yum upgrade**` **。要想成功，必须具备互联网连接**:

```
[oracle@localhost 12.2.0.1]$ cd ..
[oracle@localhost dockerfiles]$ ./buildDockerImage.sh -v 12.2.0.1 -e
Checking if required packages are present and valid...
linuxx64_12201_database.zip: OK
==========================
DOCKER info:
Containers: 0
Running: 0
Paused: 0
Stopped: 0
Images: 0
Server Version: 17.03.1-ce
Storage Driver: devicemapper
Pool Name: docker-249:0-202132724-pool
Pool Blocksize: 65.54 kB
Base Device Size: 26.84 GB
Backing Filesystem: xfs
Data file: /dev/loop0
Metadata file: /dev/loop1
Data Space Used: 14.42 MB
Data Space Total: 107.4 GB
Data Space Available: 47.98 GB
Metadata Space Used: 581.6 kB
Metadata Space Total: 2.147 GB
Metadata Space Available: 2.147 GB
Thin Pool Minimum Free Space: 10.74 GB
Udev Sync Supported: true
Deferred Removal Enabled: false
Deferred Deletion Enabled: false
Deferred Deleted Device Count: 0
Data loop file: /var/lib/docker/devicemapper/devicemapper/data
WARNING: Usage of loopback devices is strongly discouraged for production use. Use `--storage-opt dm.thinpooldev` to specify a custom block storage device.
Metadata loop file: /var/lib/docker/devicemapper/devicemapper/metadata
Library Version: 1.02.135-RHEL7 (2016-11-16)
Logging Driver: json-file
Cgroup Driver: cgroupfs
Plugins:
Volume: local
Network: bridge host macvlan null overlay
Swarm: inactive
Runtimes: runc
Default Runtime: runc
Init Binary: docker-init
containerd version: 4ab9917febca54791c5f071a9d1f404867857fcc
runc version: 54296cf40ad8143b62dbcaa1d90e520a2136ddfe
init version: 949e6fa
Security Options:
seccomp
Profile: default
selinux
Kernel Version: 4.1.12-94.3.8.el7uek.x86_64
Operating System: Oracle Linux Server 7.3
OSType: linux
Architecture: x86_64
CPUs: 1
Total Memory: 7.795 GiB
Name: localhost.localdomain
ID: D7CR:3DGV:QUGO:X7EB:AVX3:DWWW:RJIA:QVVT:I2YR:KJXV:ALR4:WLBV
Docker Root Dir: /var/lib/docker
Debug Mode (client): false
Debug Mode (server): false
Registry: [https://index.docker.io/v1/](https://index.docker.io/v1/)
Experimental: false
Insecure Registries:
127.0.0.0/8
Live Restore Enabled: false
==========================
Building image 'oracle/database:12.2.0.1-ee' ...
Sending build context to Docker daemon 3.454 GB
Step 1/16 : FROM oraclelinux:7-slim
7-slim: Pulling from library/oraclelinux
3152c71f8d80: Pull complete
Digest: sha256:e464042b724d41350fb3ac2c2f84bd9d28d98302c9ebe66048a5367682e5fad2
Status: Downloaded newer image for oraclelinux:7-slim
---> c0feb50f7527
Step 2/16 : MAINTAINER Gerald Venzl
---> Running in e442cae35367
---> 08f875cea39d
...
...
...

Step 15/16 : EXPOSE 1521 5500
---> Running in 4476c1c236e1
---> d01d39e39920
Removing intermediate container 4476c1c236e1
Step 16/16 : CMD exec $ORACLE_BASE/$RUN_FILE
---> Running in 8757674cc3d5
---> 98129834d5ad
Removing intermediate container 8757674cc3d5
Successfully built 98129834d5ad

Oracle Database Docker Image for 'ee' version 12.2.0.1 is ready to be extended:

--> oracle/database:12.2.0.1-ee

Build completed in 802 seconds.
```

# 在 Docker 容器中启动并连接到 Oracle 数据库

构建成功后，我现在可以在 Docker 容器中启动和运行 Oracle 数据库了。我所要做的就是发出`docker run`命令并传入适当的参数。一个重要的参数是用于将容器内部的端口映射到外部世界的`-p`。这是必需的，这样我也可以从 Docker 容器外部连接到数据库。另一个重要的参数是`-v`参数，它允许我将数据库的数据文件保存在 Docker 容器之外的位置。这很重要，因为它允许我保存我的数据，即使容器被扔掉。您应该始终使用`-v`参数或创建一个命名的 Docker 卷！我要使用的最后一个有用的参数是`--name`参数，它指定了 Docker 容器本身的名称。如果省略，将生成一个随机名称。但是，传递一个名称将允许我稍后通过该名称来引用容器:

```
[oracle@localhost dockerfiles]$ cd ~
[oracle@localhost ~]$ mkdir oradata
[oracle@localhost ~]$ chmod a+w oradata
[oracle@localhost ~]$ docker run --name oracle-ee -p 1521:1521 -v /home/oracle/oradata:/opt/oracle/oradata oracle/database:12.2.0.1-ee
ORACLE PASSWORD FOR SYS, SYSTEM AND PDBADMIN: 3y4RL1K7org=1

LSNRCTL for Linux: Version 12.2.0.1.0 - Production on 20-AUG-2017 19:07:55

Copyright (c) 1991, 2016, Oracle. All rights reserved.

Starting /opt/oracle/product/12.2.0.1/dbhome_1/bin/tnslsnr: please wait...

TNSLSNR for Linux: Version 12.2.0.1.0 - Production
System parameter file is /opt/oracle/product/12.2.0.1/dbhome_1/network/admin/listener.ora
Log messages written to /opt/oracle/diag/tnslsnr/e3d1a2314421/listener/alert/log.xml
Listening on: (DESCRIPTION=(ADDRESS=(PROTOCOL=ipc)(KEY=EXTPROC1)))
Listening on: (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=0.0.0.0)(PORT=1521)))

Connecting to (DESCRIPTION=(ADDRESS=(PROTOCOL=IPC)(KEY=EXTPROC1)))
STATUS of the LISTENER
------------------------
Alias LISTENER
Version TNSLSNR for Linux: Version 12.2.0.1.0 - Production
Start Date 20-AUG-2017 19:07:56
Uptime 0 days 0 hr. 0 min. 0 sec
Trace Level off
Security ON: Local OS Authentication
SNMP OFF
Listener Parameter File /opt/oracle/product/12.2.0.1/dbhome_1/network/admin/listener.ora
Listener Log File /opt/oracle/diag/tnslsnr/e3d1a2314421/listener/alert/log.xml
Listening Endpoints Summary...
(DESCRIPTION=(ADDRESS=(PROTOCOL=ipc)(KEY=EXTPROC1)))
(DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=0.0.0.0)(PORT=1521)))
The listener supports no services
The command completed successfully
[WARNING] [DBT-10102] The listener configuration is not selected for the database. EM DB Express URL will not be accessible.
CAUSE: The database should be registered with a listener in order to access the EM DB Express URL.
ACTION: Select a listener to be registered or created with the database.
Copying database files
1% complete
13% complete
25% complete
Creating and starting Oracle instance
26% complete
30% complete
31% complete
35% complete
38% complete
39% complete
41% complete
Completing Database Creation
42% complete
43% complete
44% complete
46% complete
47% complete
50% complete
Creating Pluggable Databases
55% complete
75% complete
Executing Post Configuration Actions
100% complete
Look at the log file "/opt/oracle/cfgtoollogs/dbca/ORCLCDB/ORCLCDB.log" for further details.

SQL*Plus: Release 12.2.0.1.0 Production on Sun Aug 20 19:16:01 2017

Copyright (c) 1982, 2016, Oracle. All rights reserved.

Connected to:
Oracle Database 12c Enterprise Edition Release 12.2.0.1.0 - 64bit Production

SQL>
System altered.

SQL>
Pluggable database altered.

SQL> Disconnected from Oracle Database 12c Enterprise Edition Release 12.2.0.1.0 - 64bit Production
#########################
DATABASE IS READY TO USE!
#########################
The following output is now a tail of the alert.log:
Completed: alter pluggable database ORCLPDB1 open
2017-08-20T19:16:01.025829+00:00
ORCLPDB1(3):CREATE SMALLFILE TABLESPACE "USERS" LOGGING DATAFILE '/opt/oracle/oradata/ORCLCDB/ORCLPDB1/users01.dbf' SIZE 5M REUSE AUTOEXTEND ON NEXT 1280K MAXSIZE UNLIMITED EXTENT MANAGEMENT LOCAL SEGMENT SPACE MANAGEMENT AUTO
ORCLPDB1(3):Completed: CREATE SMALLFILE TABLESPACE "USERS" LOGGING DATAFILE '/opt/oracle/oradata/ORCLCDB/ORCLPDB1/users01.dbf' SIZE 5M REUSE AUTOEXTEND ON NEXT 1280K MAXSIZE UNLIMITED EXTENT MANAGEMENT LOCAL SEGMENT SPACE MANAGEMENT AUTO
ORCLPDB1(3):ALTER DATABASE DEFAULT TABLESPACE "USERS"
ORCLPDB1(3):Completed: ALTER DATABASE DEFAULT TABLESPACE "USERS"
2017-08-20T19:16:01.889003+00:00
ALTER SYSTEM SET control_files='/opt/oracle/oradata/ORCLCDB/control01.ctl' SCOPE=SPFILE;
ALTER PLUGGABLE DATABASE ORCLPDB1 SAVE STATE
Completed: ALTER PLUGGABLE DATABASE ORCLPDB1 SAVE STATE
```

在容器第一次启动时，会创建一个新的数据库。同一容器或指向同一卷的新创建容器的后续启动将再次启动数据库。创建和/或启动数据库后，容器将对 Oracle 数据库 alert.log 文件运行`tail -f`。这样做是为了方便，这样发出一个`docker logs`命令将会实际打印出在该容器中运行的数据库的日志。一旦数据库被创建或启动，您将在输出中看到行`**DATABASE IS READY TO USE!**`。之后，您可以连接到数据库。

# 重置数据库管理员帐户密码

启动脚本还为数据库管理员帐户生成了一个密码。您可以在输出中的行`ORACLE PASSWORD FOR SYS, SYSTEM AND PDBADMIN:`旁边找到密码。您可以继续使用该密码，也可以将其重置为您选择的密码。该容器提供了一个名为`setPassword.sh`的脚本，用于重置密码。在新的 shell 中，只需对正在运行的容器执行以下命令:

```
[oracle@localhost ~]$ docker exec oracle-ee ./setPassword.sh LetsDocker
The Oracle base remains unchanged with value /opt/oracle

SQL*Plus: Release 12.2.0.1.0 Production on Sun Aug 20 19:17:08 2017

Copyright (c) 1982, 2016, Oracle. All rights reserved.

Connected to:
Oracle Database 12c Enterprise Edition Release 12.2.0.1.0 - 64bit Production

SQL>
User altered.

SQL>
User altered.

SQL>
Session altered.

SQL>
User altered.

SQL> Disconnected from Oracle Database 12c Enterprise Edition Release 12.2.0.1.0 - 64bit Production
```

# 连接到 Oracle 数据库

现在容器正在运行，端口 1521 映射到外部世界，我可以连接到容器内部的数据库:

```
[oracle@localhost ~]$ sql system/LetsDocker@//localhost:1521/ORCLPDB1

SQLcl: Release 4.2.0 Production on Sun Aug 20 19:56:43 2017

Copyright (c) 1982, 2017, Oracle. All rights reserved.

Last Successful login time: Sun Aug 20 2017 12:21:42 -07:00

Connected to:
Oracle Database 12c Enterprise Edition Release 12.2.0.1.0 - 64bit Production

SQL> grant connect, resource to gvenzl identified by supersecretpwd;

Grant succeeded.

SQL> conn gvenzl/supersecretpwd@//localhost:1521/ORCLPDB1
Connected.
SQL>
```

# 停止 Oracle 数据库 Docker 容器

如果你想停止 Docker 容器，你可以通过`docker stop`命令来完成。您所要做的就是发出命令并传递容器名或 id。这将触发容器为容器内部的数据库发出一个`shutdown immediate`。默认情况下，Docker 在杀死容器之前只允许 10 秒钟关闭容器。对于应用程序来说，这可能没什么问题，但是对于 Oracle 数据库容器这样的持久性容器，您可能需要给容器多一点时间来适当地关闭数据库。您可以通过`-t`选项来实现这一点，该选项允许您传递一个新的超时值，以秒为单位，让容器成功关闭。我将给数据库 30 秒的关闭时间，但是需要指出的是，给容器多长时间的关闭时间并不重要。一旦数据库关闭，容器将正常退出。它不会等待您指定的所有秒数，直到返回控制权。因此，即使您给它 10 分钟(600 秒)，它仍然会在数据库关闭后立即返回。在为繁忙的数据库容器指定超时时，请记住这一点:

```
[oracle@localhost ~]$ docker stop -t 30 oracle-ee
oracle-ee
```

# 重新启动 Oracle 数据库 Docker 容器

停止的集装箱总是可以通过`docker start`命令重新启动:

```
[oracle@localhost ~]$ docker start oracle-ee
oracle-ee
```

`docker start`命令会将容器放入后台并立即返回控制权。您可以通过`docker logs`命令检查容器的状态，该命令应打印相同的`**DATABASE IS READY TO USE!**`行。您还将看到，这一次数据库只是被重新启动，而不是被创建。注意，日志输出后会出现一个`docker logs -f`，即继续打印新行:

```
[oracle@localhost ~]$ docker logs oracle-ee
...
...
...
SQL*Plus: Release 12.2.0.1.0 Production on Sun Aug 20 19:30:31 2017

Copyright (c) 1982, 2016, Oracle.  All rights reserved.

Connected to an idle instance.

SQL> ORACLE instance started.

Total System Global Area 1610612736 bytes
Fixed Size          8793304 bytes
Variable Size         520094504 bytes
Database Buffers     1073741824 bytes
Redo Buffers            7983104 bytes
Database mounted.
Database opened.
SQL> Disconnected from Oracle Database 12c Enterprise Edition Release 12.2.0.1.0 - 64bit Production
#########################
DATABASE IS READY TO USE!
#########################
The following output is now a tail of the alert.log:
ORCLPDB1(3):Undo initialization finished serial:0 start:6800170 end:6800239 diff:69 ms (0.1 seconds)
ORCLPDB1(3):Database Characterset for ORCLPDB1 is AL32UTF8
ORCLPDB1(3):Opatch validation is skipped for PDB ORCLPDB1 (con_id=0)
ORCLPDB1(3):Opening pdb with no Resource Manager plan active
2017-08-20T19:30:43.703897+00:00
Pluggable database ORCLPDB1 opened read write
```

既然数据库已经启动并再次运行，我可以再次连接到内部的数据库:

```
[oracle@localhost ~]$ sql gvenzl/supersecretpwd@//localhost:1521/ORCLPDB1

SQLcl: Release 4.2.0 Production on Sun Aug 20 20:10:28 2017

Copyright (c) 1982, 2017, Oracle. All rights reserved.

Connected to:
Oracle Database 12c Enterprise Edition Release 12.2.0.1.0 - 64bit Production

SQL> select sysdate from dual;

SYSDATE
---------
20-AUG-17

SQL> exit

Disconnected from Oracle Database 12c Enterprise Edition Release 12.2.0.1.0 - 64bit Production
```

# 摘要

本文总结了如何使用 Docker 将 Oracle 数据库容器化。请注意，Oracle 还为其他 Oracle 数据库版本提供了构建文件。上述步骤基本相同，但是您应该始终参考构建文件附带的 README.md。在那里，您还可以找到更多关于如何运行 Oracle 数据库容器的选项。

Github 资源库:[https://github . com/Oracle/docker-images/tree/master/Oracle database](https://github.com/oracle/docker-images/tree/master/OracleDatabase)

*原载于 2017 年 8 月 21 日 geraldonit.com*[](https://geraldonit.com/2017/08/21/creating-an-oracle-database-docker-image/)**。**