# 如何创建小型 Docker 图像

> 原文：<https://medium.com/oracledevs/how-to-create-small-docker-images-e510b8c3c81e?source=collection_archive---------1----------------------->

说到空间效率，Docker 仍然不尽如人意。Docker 使用的分层文件系统有时会占用比实际需要更多的空间。随着时间的推移，Docker 进行了一些改进，允许构建更节省空间的图像。例如，`[ADD](https://docs.docker.com/engine/reference/builder/#add)` [指令](https://docs.docker.com/engine/reference/builder/#add)足够智能，可以检测(不幸的是，只能检测)本地压缩档案(不幸的是，只能检测`identity`、`gzip`、`bzip2`或`xz`，而**不能检测**、`zip`档案)，并直接将未压缩的内容添加到映像中，这与简单地将文件复制到映像中的`COPY`指令不同。后者的缺点是 zip 存档本身和存档的提取内容占用空间，而前者不会占用存档的任何空间，因为它不会添加到映像中。

![](img/d8711239155a0078d111622c8ea57469.png)

# Docker 多阶段构建

在 Docker 17.05-ce 中，我们得到了[多阶段构建](https://docs.docker.com/engine/userguide/eng-image/multistage-build/)，例如，它允许您在中间映像中构建二进制文件，然后将所构建的二进制文件复制到另一个映像，即最终映像。这里的好处是，所有被占用的空间都来自源文件、构建工具等。永远不要把它变成最终图像，而只留在中间图像中。这对于微型容器来说非常好，并且在静态链接的单个二进制文件的 Go 环境中变得特别流行。然而，当在不同的地方安装软件时，多阶段构建很快就遇到了限制。常见的例子是安装常规的 yum 包，使其位于文件系统上许多不同的位置，创建用户和组，等等。在这种情况下，可能很快就不得不将整个文件系统，即`/`，复制到最终的容器中。唯一的问题是文件所有权丢失了，也就是说，所有文件最终都归 root 所有。这将导致最终图像中出现额外的`RUN`指令，从而导致另一层的构建和潜在的空间浪费。在我的一个小实验中可以看到一个简单的例子，在 Oracle 数据库安装中使用多阶段构建。在这个实验中，我完全按照上面说做了，通过下面的指令复制整个文件系统:

```
COPY --from=builder / /
```

该指令只是将中间图像*生成器*的所有内容(`/`)复制到最终图像的`/`。然而，由于复制后的所有文件都归`root`所有，因此需要一个额外的步骤:将`$ORACLE_BASE`的所有权更改为`oracle`用户，这是通过以下指令完成的:

```
RUN chown -R oracle:dba $ORACLE_BASE
```

虽然`COPY`指令做了预期的事情，只是复制了成功安装后的最终结果，但不幸的是，额外的`RUN`指令又给生成的映像增加了不必要的大小(见第 8 行):

```
[oracle@localhost dockerfiles]$ docker history oracle/database:12.2.0.1-ee
IMAGE           CREATED          CREATED BY                                         SIZE   COMMENT
0328c9d11729    4 minutes ago    /bin/sh -c #(nop) CMD ["/bin/sh" "-c" "ex...         0B
3847d825c5e0    5 minutes ago    /bin/sh -c #(nop) EXPOSE 1521/tcp 5500/tcp           0B
3293438499a2    5 minutes ago    /bin/sh -c #(nop) VOLUME [/opt/oracle/ora...         0B
aa9294c8a9e1    5 minutes ago    /bin/sh -c #(nop) WORKDIR /home/oracle               0B
2b1ce0fb41e2    5 minutes ago    /bin/sh -c #(nop) USER [oracle]                      0B
a4a030e72a3d    5 minutes ago    /bin/sh -c chown -R oracle:dba $ORACLE_BASE      5.97GB
5c36909d4568    6 minutes ago    /bin/sh -c #(nop) COPY dir:bf7a903b4add9a7...    6.14GB
87ba2e289389    9 minutes ago    /bin/sh -c #(nop) ENV PATH=/opt/oracle/pr...         0B
3f1b0c5412bf    9 minutes ago    /bin/sh -c #(nop) ENV ORACLE_BASE=/opt/or...         0B
a6e9e9e5ddc1    12 days ago      /bin/sh -c #(nop) CMD ["/bin/bash"]                  0B
<missing>       12 days ago      /bin/sh -c #(nop) ADD file:7f7d89f7bdf05fd...     118MB
<missing>       4 weeks ago      /bin/sh -c #(nop) MAINTAINER Oracle Linux...         0B
```

当然，复制整个文件系统并不一定是您想要做的，但是实验表明，不幸的是，多阶段构建仍然缺乏某些灵活性，这有望很快得到解决。然而，如果你只添加了几个或者一个文件，比如首先从源文件开始构建，多阶段构建是缩小 Docker 映像大小的好方法。

# docker–壁球选项

幸运的是，还有另一个特性可以帮助缩小 Docker 映像的大小:将所有文件系统层压缩成一个层。我发现这个功能仍然不为世人所知，很可能是因为它仍然是一个实验性的功能。Docker 1.13 (API 1.25)引入了*挤压*特性，当时 Docker 仍然遵循不同的版本控制方案。如前所述，直到今天，17.06-ce 仍被归类为*实验性*，这当然带有警告。虽然我从未遇到过任何关于`--squash`选项的问题，但在早期，偶尔破坏图像是众所周知的。如前所述，我在最近的版本中再也没有遇到过这种情况，但是你知道，请注意，它仍然被归类为*。*

# *启用实验功能*

*为了使用 Docker 中的实验特性，你首先必须[打开那些实验特性](https://github.com/docker/docker-ce/blob/master/components/cli/experimental/README.md)，这意味着你必须将`--experimental`标志传递给 Docker 守护进程。这对于 Mac 上的 Docker 来说相当容易，只需进入*偏好设置- >守护进程*并勾选*实验特性*复选框。*应用&重启*完成剩下的技巧。然而在 Linux 上，你必须自己修改`/etc/docker/daemon.json`并包含`"experimental": true`(重要的是，`true`没有引号！).这样，我的文件如下所示:*

```
*[root@localhost ~]$ cat /etc/docker/daemon.json
{
  "experimental": true,
  "storage-driver": "btrfs"
}*
```

*重启 Docker 守护进程后，当运行`docker info`时，你会发现下面的行`Experimental: true`(见第 41 行):*

```
*[root@localhost ~]$ systemctl restart docker
[root@localhost ~]$ docker info
Containers: 2
Running: 0
Paused: 0
Stopped: 2
Images: 3
Server Version: 17.06.2-ol
Storage Driver: btrfs
Build Version: Btrfs v4.9.1
Library Version: 102
Logging Driver: json-file
Cgroup Driver: cgroupfs
Plugins:
Volume: local
Network: bridge host ipvlan macvlan null overlay
Log: awslogs fluentd gcplogs gelf journald json-file logentries splunk syslog
Swarm: inactive
Runtimes: runc
Default Runtime: runc
Init Binary: docker-init
containerd version: 6e23458c129b551d5c9871e5174f6b1b7f6d1170
runc version: 810190ceaa507aa2727d7ae6f4790c76ec150bd2
init version: 949e6fa
Security Options:
seccomp
Profile: default
selinux
Kernel Version: 4.1.12-94.3.8.el7uek.x86_64
Operating System: Oracle Linux Server 7.3
OSType: linux
Architecture: x86_64
CPUs: 2
Total Memory: 7.795GiB
Name: localhost.localdomain
ID: GZ5A:XQWB:F5TE:56JE:GR3J:VCXJ:I7BO:EGMY:K52K:JAS3:A7ZC:BOHQ
Docker Root Dir: /var/lib/docker
Debug Mode (client): false
Debug Mode (server): false
Registry: [https://index.docker.io/v1/](https://index.docker.io/v1/)
Experimental: true
Insecure Registries:
127.0.0.0/8
Live Restore Enabled: false*
```

*现在你可以使用`--squash`选项了。您也可以通过执行`docker build --squash .`来验证这一点。如果实验功能未打开，您将收到:*

```
*[root@localhost ~]$ docker build --squash .
"--squash" is only supported on a Docker daemon with experimental features enabled*
```

*另一方面，如果打开了实验特性，您将克服这个错误，并让 Docker 抱怨没有 Docker 文件来启动构建:*

```
*[root@localhost ~]$ docker build --squash .
unable to prepare context: unable to evaluate symlinks in Dockerfile path: lstat /root/Dockerfile: no such file or directory*
```

# *使用–squash 选项构建 Oracle 数据库 Docker 映像*

*如果没有`--squash`选项，现在一个 Oracle 数据库企业版 Docker 映像的大小超过 13 GB:*

```
*[oracle@localhost dockerfiles]$ docker images
REPOSITORY           TAG              IMAGE ID          CREATED               SIZE
oracle/database      12.2.0.1-ee      1b5ed2b790a7      20 seconds ago      13.2GB
oraclelinux          7-slim           a6e9e9e5ddc1      3 weeks ago          118MB*
```

*仔细查看该映像时，会发现由于该映像不再需要分层文件系统而浪费了大量空间:*

```
*[oracle@localhost dockerfiles]$ docker history 1b5ed2b790a7
IMAGE             CREATED             CREATED BY                                           SIZE      COMMENT
1b5ed2b790a7      31 minutes ago      /bin/sh -c #(nop) CMD ["/bin/sh" "-c" "ex...           0B
3253c4b724eb      31 minutes ago      /bin/sh -c #(nop) EXPOSE 1521/tcp 5500/tcp             0B
3a2ce9bac206      31 minutes ago      /bin/sh -c #(nop) VOLUME [/opt/oracle/ora...           0B
b7576a4a19e1      31 minutes ago      /bin/sh -c #(nop) WORKDIR /home/oracle                 0B
4597cb7d8aea      31 minutes ago      /bin/sh -c #(nop) USER [oracle]                        0B
fbf4e1b3363d      31 minutes ago      |0 /bin/sh -c $ORACLE_BASE/oraInventory/or...      11.2MB
07acc091e808      31 minutes ago      /bin/sh -c #(nop) USER [root]                          0B
f8b524f5a3f2      31 minutes ago      |0 /bin/sh -c $INSTALL_DIR/$INSTALL_DB_BIN...      5.97GB
6bc32fcfcbaf      37 minutes ago      /bin/sh -c #(nop) USER [oracle]                        0B
75de01f58755      37 minutes ago      |0 /bin/sh -c chmod ug+x $INSTALL_DIR/*.sh...      3.62GB
8cbd3c82fbbe      40 minutes ago      /bin/sh -c #(nop) COPY multi:9d11dcad11365...        22kB
2aeeab9ba02d      40 minutes ago      /bin/sh -c #(nop) COPY multi:10a3dd36c140a...      3.45GB
6c4c8a19d3a7      41 minutes ago      /bin/sh -c #(nop) ENV INSTALL_DIR=/opt/or...           0B
1169e05f0f02      41 minutes ago      /bin/sh -c #(nop) ENV ORACLE_BASE=/opt/or...           0B
7f19c2d035db      41 minutes ago      /bin/sh -c #(nop) MAINTAINER Gerald Venzl...           0B
a6e9e9e5ddc1      3 weeks ago         /bin/sh -c #(nop) CMD ["/bin/bash"]                    0B
<missing>         3 weeks ago         /bin/sh -c #(nop) ADD file:7f7d89f7bdf05fd...       118MB
<missing>         5 weeks ago         /bin/sh -c #(nop) MAINTAINER Oracle Linux...           0B*
```

*第 12 行和第 14 行中的层是通过将安装程序文件复制到映像中，解压缩它们，然后运行安装程序所占用的空间。第 8 行中的层也占用了不必要的空间，因为这一步所做的只是对一些文件设置权限。*

*由于支持将文件系统层压缩成一个层， [Oracle 数据库 Docker 映像](https://github.com/oracle/docker-images) [构建脚本](https://github.com/oracle/docker-images/blob/master/OracleDatabase/dockerfiles/buildDockerImage.sh)也得到了增强，允许通过`-o`将`--squash`等选项传递给 Docker 守护进程。为了为 Oracle 数据库构建一个压缩的 Docker 映像，只需将`-o --squash`添加到构建脚本中。参见[创建 Oracle 数据库 Docker 映像](https://geraldonit.com/2017/08/21/creating-an-oracle-database-docker-image/)了解映像构建本身的更多细节！*

```
*[oracle@localhost dockerfiles]$ ./buildDockerImage.sh -e -o --squash
Checking if required packages are present and valid...
linuxx64_12201_database.zip: OK
==========================
DOCKER info:
Containers: 0
 Running: 0
 Paused: 0
 Stopped: 0
Images: 16
Server Version: 17.06.2-ol
Storage Driver: btrfs
 Build Version: Btrfs v4.9.1
 Library Version: 102
Logging Driver: json-file
Cgroup Driver: cgroupfs
Plugins:
 Volume: local
 Network: bridge host ipvlan macvlan null overlay
 Log: awslogs fluentd gcplogs gelf journald json-file logentries splunk syslog
Swarm: inactive
Runtimes: runc
Default Runtime: runc
Init Binary: docker-init
containerd version: 6e23458c129b551d5c9871e5174f6b1b7f6d1170
runc version: 810190ceaa507aa2727d7ae6f4790c76ec150bd2
init version: 949e6fa
Security Options:
 seccomp
  Profile: default
 selinux
Kernel Version: 4.1.12-94.3.8.el7uek.x86_64
Operating System: Oracle Linux Server 7.3
OSType: linux
Architecture: x86_64
CPUs: 2
Total Memory: 7.795GiB
Name: localhost.localdomain
ID: GZ5A:XQWB:F5TE:56JE:GR3J:VCXJ:I7BO:EGMY:K52K:JAS3:A7ZC:BOHQ
Docker Root Dir: /var/lib/docker
Debug Mode (client): false
Debug Mode (server): false
Registry: [https://index.docker.io/v1/](https://index.docker.io/v1/)
Experimental: true
Insecure Registries:
 127.0.0.0/8
Live Restore Enabled: false

==========================
Proxy settings were found and will be used during the build.
Building image 'oracle/database:12.2.0.1-ee' ...
Sending build context to Docker daemon  3.454GB
Step 1/16 : FROM oraclelinux:7-slim
 ---> a6e9e9e5ddc1
Step 2/16 : MAINTAINER Gerald Venzl <[gerald.venzl@oracle.com](mailto:gerald.venzl@oracle.com)>
 ---> Running in 731e99b1252f
 ---> 3e5ee1e8dcd5
Removing intermediate container 731e99b1252f
Step 3/16 : ENV ORACLE_BASE /opt/oracle ORACLE_HOME /opt/oracle/product/12.2.0.1/dbhome_1 INSTALL_FILE_1 "linuxx64_12201_database.zip" INSTALL_RSP "db_inst.rsp" CONFIG_RSP "dbca.rsp.tmpl" PWD_FILE "setPassword.sh" RUN_FILE "runOracle.sh" START_FILE "startDB.sh" CREATE_DB_FILE "createDB.sh" SETUP_LINUX_FILE "setupLinuxEnv.sh" CHECK_SPACE_FILE "checkSpace.sh" CHECK_DB_FILE "checkDBStatus.sh" USER_SCRIPTS_FILE "runUserScripts.sh" INSTALL_DB_BINARIES_FILE "installDBBinaries.sh"
 ---> Running in 55ec8a41dcf5
 ---> 89d92f9f8c18
Removing intermediate container 55ec8a41dcf5
Step 4/16 : ENV INSTALL_DIR $ORACLE_BASE/install PATH $ORACLE_HOME/bin:$ORACLE_HOME/OPatch/:/usr/sbin:$PATH LD_LIBRARY_PATH $ORACLE_HOME/lib:/usr/lib CLASSPATH $ORACLE_HOME/jlib:$ORACLE_HOME/rdbms/jlib
 ---> Running in ba918281f786
 ---> 71abe664d3eb
Removing intermediate container ba918281f786
Step 5/16 : COPY $INSTALL_FILE_1 $INSTALL_RSP $SETUP_LINUX_FILE $CHECK_SPACE_FILE $INSTALL_DB_BINARIES_FILE $INSTALL_DIR/
 ---> ee1bfa84da00
Removing intermediate container 8859d54f9f64
Step 6/16 : COPY $RUN_FILE $START_FILE $CREATE_DB_FILE $CONFIG_RSP $PWD_FILE $CHECK_DB_FILE $USER_SCRIPTS_FILE $ORACLE_BASE/
 ---> 617a3fef9ab7
Removing intermediate container 7e87f0a3842a
Step 7/16 : RUN chmod ug+x $INSTALL_DIR/*.sh &&     sync &&     $INSTALL_DIR/$CHECK_SPACE_FILE &&     $INSTALL_DIR/$SETUP_LINUX_FILE
 ---> Running in dbf12b6b3eb2
...
...
...
...
...
Removing intermediate container 44e7c6d85022
Step 10/16 : USER root
 ---> Running in 4de8eb80cf7c
 ---> 3024886cc1e9
Removing intermediate container 4de8eb80cf7c
Step 11/16 : RUN $ORACLE_BASE/oraInventory/orainstRoot.sh &&     $ORACLE_HOME/root.sh &&     rm -rf $INSTALL_DIR
 ---> Running in 778aa6699b31
Changing permissions of /opt/oracle/oraInventory.
Adding read,write permissions for group.
Removing read,write,execute permissions for world.

Changing groupname of /opt/oracle/oraInventory to dba.
The execution of the script is complete.
Check /opt/oracle/product/12.2.0.1/dbhome_1/install/root_778aa6699b31_2017-10-24_22-16-14-688141805.log for the output of root script
 ---> c957e036f82e
Removing intermediate container 778aa6699b31
Step 12/16 : USER oracle
 ---> Running in feca45328a95
 ---> 324e1973d1af
Removing intermediate container feca45328a95
Step 13/16 : WORKDIR /home/oracle
 ---> a47ca371ba2d
Removing intermediate container 48750bbc31bb
Step 14/16 : VOLUME $ORACLE_BASE/oradata
 ---> Running in 14f2be4454fb
 ---> 1147c64ecb83
Removing intermediate container 14f2be4454fb
Step 15/16 : EXPOSE 1521 5500
 ---> Running in d4936aba18df
 ---> 6424494c777a
Removing intermediate container d4936aba18df
Step 16/16 : CMD exec $ORACLE_BASE/$RUN_FILE
 ---> Running in 502a55257d11
 ---> dee493e2ff89
Removing intermediate container 502a55257d11
Successfully built 758fd037bf24
Successfully tagged oracle/database:12.2.0.1-ee

  Oracle Database Docker Image for 'ee' version 12.2.0.1 is ready to be extended:

    --> oracle/database:12.2.0.1-ee

  Build completed in 780 seconds.*
```

*您可能会发现图像构建本身现在需要更长的时间。这是因为`--squash`功能是如何工作的。首先，它构建常规映像，然后 Docker 创建一个新映像，其中包含所有文件系统层。结果是两个图像，一个被挤压的图像被相应地标记，另一个未标记的图像是原始图像。**请注意，这也意味着为了使用** `**--squash**` **特性，您的映像构建系统上必须有额外的可用空间！***

```
*[oracle@localhost dockerfiles]$ docker images
REPOSITORY      TAG              IMAGE ID          CREATED               SIZE
oracle/database 12.2.0.1-ee      758fd037bf24      12 minutes ago      6.26GB
<none>          <none>           dee493e2ff89      14 minutes ago      13.2GB
oraclelinux     7-slim           a6e9e9e5ddc1      3 weeks ago          118MB*
```

*未标记的图像与之前的大小完全相同，为 13.2 GB，并且它在所有方面都与您之前拥有的原始图像完全相同，所有层和这些层中的空间都被占用。然而，第二个是压缩的图像，大小只有 6.26 GB，这是图像中所有文件占用的实际空间。这其实挺神奇的。只需使用`--squash`功能，图像尺寸就变成了原来的一半！当然，每张图片的里程数可能会有所不同，这取决于图片中浪费了多少空间。*

*查看新的图像历史记录，您会发现与以前有所不同:*

```
*[oracle@localhost dockerfiles]$ docker history oracle/database:12.2.0.1-ee
IMAGE             CREATED             CREATED BY                                          SIZE      COMMENT
758fd037bf24      14 minutes ago                                                        6.14GB      merge sha256:dee493e2ff89ac1317be5eeed80dfae6f9140be968a1a866c14b0998cbec2d95 to sha256:a6e9e9e5ddc1bdf50d64027c18cd27918c439797925ff16c97feaeee49b22ac2
<missing>         16 minutes ago      /bin/sh -c #(nop) CMD ["/bin/sh" "-c" "ex...          0B
<missing>         16 minutes ago      /bin/sh -c #(nop) EXPOSE 1521/tcp 5500/tcp            0B
<missing>         16 minutes ago      /bin/sh -c #(nop) VOLUME [/opt/oracle/ora...          0B
<missing>         16 minutes ago      /bin/sh -c #(nop) WORKDIR /home/oracle                0B
<missing>         16 minutes ago      /bin/sh -c #(nop) USER [oracle]                       0B
<missing>         16 minutes ago      |0 /bin/sh -c $ORACLE_BASE/oraInventory/or...         0B
<missing>         16 minutes ago      /bin/sh -c #(nop) USER [root]                         0B
<missing>         16 minutes ago      |0 /bin/sh -c $INSTALL_DIR/$INSTALL_DB_BIN...         0B
<missing>         22 minutes ago      /bin/sh -c #(nop) USER [oracle]                       0B
<missing>         22 minutes ago      |0 /bin/sh -c chmod ug+x $INSTALL_DIR/*.sh...         0B
<missing>         25 minutes ago      /bin/sh -c #(nop) COPY multi:9d11dcad11365...         0B
<missing>         25 minutes ago      /bin/sh -c #(nop) COPY multi:10a3dd36c140a...         0B
<missing>         26 minutes ago      /bin/sh -c #(nop) ENV INSTALL_DIR=/opt/or...          0B
<missing>         26 minutes ago      /bin/sh -c #(nop) ENV ORACLE_BASE=/opt/or...          0B
<missing>         26 minutes ago      /bin/sh -c #(nop) MAINTAINER Gerald Venzl...          0B
<missing>         3 weeks ago         /bin/sh -c #(nop) CMD ["/bin/bash"]                   0B
<missing>         3 weeks ago         /bin/sh -c #(nop) ADD file:7f7d89f7bdf05fd...      118MB*
```

*只剩下两层占据空间。第一个(第 20 行)是大小为 118 MB 的基本映像(在`FROM`指令中使用的映像)，第二个(第 3 行)是已安装的 6.14 GB 的 Oracle 数据库。注意它旁边的注释说“*合并 sha256:… 【T3”)，表明这个层是两个散列之间的所有层的合并层，基本上是第 4 行和第 18 行之间的所有层。**

*作为最后一步，您现在可以愉快地删除未标记的图像，因为您不再需要它了:*

```
*[oracle@localhost dockerfiles]$ docker images
REPOSITORY        TAG              IMAGE ID          CREATED            SIZE
oracle/database   12.2.0.1-ee      758fd037bf24      2 hours ago      6.26GB
<none>            <none>           dee493e2ff89      2 hours ago      13.2GB
oraclelinux       7-slim           a6e9e9e5ddc1      3 weeks ago       118MB
[oracle@localhost dockerfiles]$ docker rmi dee493e2ff89
Deleted: sha256:dee493e2ff89ac1317be5eeed80dfae6f9140be968a1a866c14b0998cbec2d95
Deleted: sha256:6424494c777a0b816cebc1255956618398b04d9fb170d23be6cd515832bfdbd9
Deleted: sha256:1147c64ecb83ad9d5ba16c7aec6aad1f967119e7bf64b7efd73bd61d4dd4bca9
Deleted: sha256:a47ca371ba2da6738dec94a7cb9ea5eb06e6d28d86e84ed490bc3a5ad3a047e1
Deleted: sha256:324e1973d1afa1ec595f080c8dfbe5e5744f629a77e04f1bfaf49419a55560de
Deleted: sha256:c957e036f82e969eeed2c10706809263218a571c4a98f42a2173fa9a9da1314c
Deleted: sha256:c0a4621f29f2dd5504648ab6a7d0714bb2c3978931a13dc84bb409653d4a1dfc
Deleted: sha256:3024886cc1e99900e3e98b4f400f5b196a572ddf0c108747cabc21306126917a
Deleted: sha256:25d34b0e9f1f06231d3618a64d71d628814051e3fa6dd6efe50df5cc86c7e561
Deleted: sha256:f96299b83d8e22833550edc5e429d996c77a2955713d0398d499fa470a3d785b
Deleted: sha256:7d882e4cc07ee35d929ef6b674d097dd4781daa19c4492680248e7210bdd84a6
Deleted: sha256:f032bb97ce431a38d6c8e5a9e9e747646f9b17f338a21d2ba3f96bf5ce625e69
Deleted: sha256:58b0605016d39083971e8184dced3aa9a8b0d6399f05fe6a0c8371354f7fc371
Deleted: sha256:617a3fef9ab7df8414317fc60f6fae1bdb396816429431e9e1537cf3979947a7
Deleted: sha256:cc1756dd643713e8d723b4c52ff92ebb6873b94a64d5ae1f84e39f08d7e0284c
Deleted: sha256:ee1bfa84da00e49c8117f3abc5e932003d224c921ae2311126a084188f2934c6
Deleted: sha256:09ac53e179b7c9924d79fd7483570230d18742a1355c44240ab4c08768a2dd2a
Deleted: sha256:71abe664d3eba13768568804098740dfe369346716f5cea90ee60bfdba2ca305
Deleted: sha256:89d92f9f8c18a13ea1b89b5d0856618c200d82ac7cca5002f02c1bd23059a91f
Deleted: sha256:3e5ee1e8dcd592e836ac629ed61bb0bf2dc616bef5d5162f098216539ce00657
[oracle@localhost dockerfiles]$ docker images
REPOSITORY        TAG              IMAGE ID          CREATED            SIZE
oracle/database   12.2.0.1-ee      758fd037bf24      2 hours ago      6.26GB
oraclelinux       7-slim           a6e9e9e5ddc1      3 weeks ago       118MB*
```

# *结论*

*有几种不同的方法可以让你缩小 Docker 图片的尺寸。它们都有优点和缺点。实验性的`--squash`功能通过将所有图层压缩成一个图层，可以轻松地将图像缩小到图像中文件所需的实际大小。但是，请记住，这项功能仍处于*试验*状态，将来可能会消失。*

**原载于 2017 年 11 月 13 日*[*geraldonit.com*](https://geraldonit.com/2017/11/13/how-to-create-small-docker-images/)*。**