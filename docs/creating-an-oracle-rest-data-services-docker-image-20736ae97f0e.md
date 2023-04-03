# 创建 Oracle REST 数据服务 Docker 映像

> 原文：<https://medium.com/oracledevs/creating-an-oracle-rest-data-services-docker-image-20736ae97f0e?source=collection_archive---------7----------------------->

![](img/337d7b9f44c5830d777adf3c0de1a93e.png)

Oracle REST Data Services

Oracle 已经将 Oracle REST 数据服务(ORDS)添加到 GitHub 上的 [Docker 构建文件家族中，这意味着您现在可以轻松地对 ORDS 进行 dockerize。如果你还不知道 ORDS 是什么，它是甲骨文公司的一项免费的*技术，可以让你的甲骨文数据库实现 REST-enable。更具体地说，使用 ORDS，您可以直接启动常规的 REST 调用来修改或从一个或多个 Oracle 数据库中检索数据，而不必知道如何编写 SQL 并不是说懂 SQL 就是坏事！🙂在现代应用程序和微服务架构中，REST 越来越普遍地用于交换信息。ORDS 使您能够通过 REST 轻松地与 Oracle 数据库交换数据，而无需自己编写一行又一行的代码。更多关于 ORDS 是什么以及它能做什么的信息，请查看杰夫·史密斯关于 ORDS 的博客文章。*](https://github.com/oracle/docker-images/tree/master/OracleRestDataServices/)

# *你需要什么*

*   *ORDS 的安装 zip 文件，你可以从[甲骨文技术网](http://www.oracle.com/technetwork/developer-tools/rest-data-services/downloads/index.html)下载*
*   *一个 Java 服务器 JRE 8 Docker 基础映像*
*   *运行在某个地方的 Oracle 数据库，ORDS 应该通过 REST 公开它*

*我写这篇博客时的环境如下:*

*   *Oracle Linux 7.4(4 . 1 . 12–112 . 14 . 15 . El 7 uek . x86 _ 64)*
*   *docker 17 . 06 . 2-ol(docker-engine . x86 _ 64 17 . 06 . 2 . ol-1 . 0 . 1 . el7)*
*   *Java 服务器 JRE 1.8.0_172*
*   *Oracle REST 数据服务 18.1.1*

# *构建 Oracle REST 数据服务 Docker 映像*

*与所有来自 Oracle 的 GitHub 构建文件一样，您首先必须下载它们。有多种方法可以下载它们。例如，您可以直接克隆 Git 存储库。或者你可以从 GitHub 下载一个包含所有需要的文件的 zip 文件。这个选项最适合不懂 Git 的人。zip 文件的 URL 是[https://github.com/oracle/docker-images/archive/master.zip](https://github.com/oracle/docker-images/archive/master.zip)，你可以通过`wget`或你的浏览器下载，然后解压:*

```
*$ wget [https://github.com/oracle/docker-images/archive/master.zip](https://github.com/oracle/docker-images/archive/master.zip)
--2018-05-04 14:20:26-- [https://github.com/oracle/docker-images/archive/master.zip](https://github.com/oracle/docker-images/archive/master.zip)
Resolving github.com (github.com)... 192.30.255.113, 192.30.255.112
Connecting to github.com (github.com)|192.30.255.113|:443... connected.
HTTP request sent, awaiting response... 302 Found
Location: [https://codeload.github.com/oracle/docker-images/zip/master](https://codeload.github.com/oracle/docker-images/zip/master) [following]
--2018-05-04 14:20:26-- [https://codeload.github.com/oracle/docker-images/zip/master](https://codeload.github.com/oracle/docker-images/zip/master)
Connecting to codeload.github.com (codeload.github.com)|192.30.255.120|:443... connected.
Proxy request sent, awaiting response... 200 OK
Length: unspecified [application/zip]
Saving to: ‘master.zip’

[ <=> ] 7,601,317 5.37MB/s in 1.3s

2018-05-04 14:20:28 (5.37 MB/s) - ‘master.zip’ saved [7601317]

$ unzip master.zip
Archive: master.zip
184cded65f147766c41b2179ee31ba5551185507
creating: docker-images-master/
extracting: docker-images-master/.gitattributes
inflating: docker-images-master/.gitignore
extracting: docker-images-master/.gitmodules
inflating: docker-images-master/CODEOWNERS
inflating: docker-images-master/CONTRIBUTING.md
...
...
...
creating: docker-images-master/OracleRestDataServices/
extracting: docker-images-master/OracleRestDataServices/.gitignore
inflating: docker-images-master/OracleRestDataServices/COPYRIGHT
inflating: docker-images-master/OracleRestDataServices/LICENSE
inflating: docker-images-master/OracleRestDataServices/README.md
creating: docker-images-master/OracleRestDataServices/dockerfiles/
...
...
...
inflating: docker-images-master/README.md
$*
```

*接下来，你必须下载 ORDS 安装压缩文件。如上所述，您可以从[甲骨文技术网](http://www.oracle.com/technetwork/developer-tools/rest-data-services/downloads/index.html)获得:*

```
*$ ls -al ords.18*.zip
-rw-r--r--. 1 oracle oracle 61118609 May 4 14:38 ords.18.1.1.95.1251.zip*
```

# *构建 Java 服务器 JRE 基础映像*

*ORDS 码头形象建立在`oracle/serverjre:8`基础形象的基础上。该图像不在 Docker Hub 上，因此 Docker 不能自动提取图像。相反，您必须先构建该映像，然后才能继续构建 ORDS 映像。构建 Java 服务器 JRE 映像非常简单。首先，您需要从[甲骨文技术网](http://www.oracle.com/technetwork/java/javase/downloads/index.html)下载最新的`server-jre-8*linux-x64.tar.gz`文件:*

```
*$ ls -al server-jre*.tar.gz
-rw-r--r--. 1 oracle oracle 54817401 May 4 14:30 server-jre-8u172-linux-x64.tar.gz*
```

*一旦有了这个文件，将它复制到`OracleJava/java-8`文件夹中，并运行 Java Server JRE Docker `build.sh`脚本:*

```
*$ cd docker-images-master/OracleJava/java-8
$ cp ~/server-jre-8u172-linux-x64.tar.gz .
$ ./build.sh
Sending build context to Docker daemon 54.82MB
Step 1/5 : FROM oraclelinux:7-slim
---> 9870bebfb1d5
Step 2/5 : MAINTAINER Bruno Borges <[bruno.borges@oracle.com](mailto:bruno.borges@oracle.com)>
---> Running in b1847c1a647e
---> 3bc9baedf526
Removing intermediate container b1847c1a647e
Step 3/5 : ENV JAVA_PKG server-jre-8u*-linux-x64.tar.gz JAVA_HOME /usr/java/default
---> Running in 50998175529b
---> 017598682688
Removing intermediate container 50998175529b
Step 4/5 : ADD $JAVA_PKG /usr/java/
---> 6704a281de8b
Removing intermediate container b6b6a08d3c38
Step 5/5 : RUN export JAVA_DIR=$(ls -1 -d /usr/java/*) && ln -s $JAVA_DIR /usr/java/latest && ln -s $JAVA_DIR /usr/java/default && alternatives --install /usr/bin/java java $JAVA_DIR/bin/java 20000 && alternatives --install /usr/bin/javac javac $JAVA_DIR/bin/javac 20000 && alternatives --install /usr/bin/jar jar $JAVA_DIR/bin/jar 20000
---> Running in 281fe2343d2c
---> f65b2559f3a5
Removing intermediate container 281fe2343d2c
Successfully built f65b2559f3a5
Successfully tagged oracle/serverjre:8
$*
```

*就是这样！现在你有了一个全新的`oracle/serverjre:8`码头工人形象:*

```
*$ docker images
REPOSITORY          TAG            IMAGE ID        CREATED           SIZE
oracle/serverjre    8              f65b2559f3a5    45 seconds ago    269MB
oracle/database     12.2.0.1-se2   323887b92e8f    3 weeks ago       6.38GB
oracle/database     12.2.0.1-ee    08d230aa1d55    3 weeks ago       6.39GB
oracle/database     12.1.0.2-se2   b4999e09453e    3 weeks ago       5.08GB
oracle/database     12.1.0.2-ee    aee62bc26119    3 weeks ago       5.18GB
oracle/database     11.2.0.2-xe    50712d409891    3 weeks ago       809MB
oraclelinux         7-slim         9870bebfb1d5    5 months ago      118MB
$*
```

# *树立 ORDS 码头工人的形象*

*一旦你的机器上有了`oracle/serverjre:8` Docker 映像，你现在就可以开始构建实际的 ORDS Docker 映像了。这也是一个相当简单的任务，只需将安装程序 zip 文件放入`OracleRestDataServices/dockerfiles/`文件夹并运行`buildDockerImage.sh`脚本:*

```
*$ cd ../../OracleRestDataServices/dockerfiles/
$ mv ~/ords.18.1.1.95.1251.zip .
$ ./buildDockerImage.sh
Checking if required packages are present and valid...
ords.18.1.1.95.1251.zip: OK
==========================
DOCKER info:
Containers: 2
Running: 0
Paused: 0
Stopped: 2
Images: 10
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
Kernel Version: 4.1.12-112.14.15.el7uek.x86_64
Operating System: Oracle Linux Server 7.4
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
Proxy settings were found and will be used during build.
Building image 'oracle/restdataservices:18.1.1' ...
Sending build context to Docker daemon 61.14MB
Step 1/10 : FROM oracle/serverjre:8
---> f65b2559f3a5
Step 2/10 : LABEL maintainer "[gerald.venzl@oracle.com](mailto:gerald.venzl@oracle.com)"
---> Running in d90ea0da50ae
---> 8959f49c8b7b
Removing intermediate container d90ea0da50ae
Step 3/10 : ENV ORDS_HOME /opt/oracle/ords INSTALL_FILE ords*.zip CONFIG_PROPS "ords_params.properties.tmpl" STANDALONE_PROPS "standalone.properties.tmpl" RUN_FILE "runOrds.sh"
---> Running in 84ae509c3b08
---> 595f8228d224
Removing intermediate container 84ae509c3b08
Step 4/10 : COPY $INSTALL_FILE $CONFIG_PROPS $STANDALONE_PROPS $RUN_FILE $ORDS_HOME/
---> 992d15c48302
Removing intermediate container 3088dd27464e
Step 5/10 : RUN mkdir -p $ORDS_HOME/doc_root && chmod ug+x $ORDS_HOME/*.sh && groupadd dba && useradd -d /home/oracle -g dba -m -s /bin/bash oracle && cd $ORDS_HOME && jar -xf $INSTALL_FILE && rm $INSTALL_FILE && mkdir -p $ORDS_HOME/config/ords && java -jar $ORDS_HOME/ords.war configdir $ORDS_HOME/config && chown -R oracle:dba $ORDS_HOME
---> Running in a84bc25b8eb3
May 04, 2018 6:42:22 PM
INFO: Set config.dir to /opt/oracle/ords/config in: /opt/oracle/ords/ords.war
---> 6ccfc92744ed
Removing intermediate container a84bc25b8eb3
Step 6/10 : USER oracle
---> Running in c41c77b49add
---> 2bd11f2f8008
Removing intermediate container c41c77b49add
Step 7/10 : WORKDIR /home/oracle
---> bc31d79cfb4a
Removing intermediate container efef9dccf774
Step 8/10 : VOLUME $ORDS_HOME/config/ords
---> Running in dfbc7ee6f967
---> 0ee4e7ed71b1
Removing intermediate container dfbc7ee6f967
Step 9/10 : EXPOSE 8888
---> Running in deaebbf2950b
---> 25d777caccca
Removing intermediate container deaebbf2950b
Step 10/10 : CMD $ORDS_HOME/$RUN_FILE
---> Running in 0c2270a7fac4
---> 4ee1ac73e1f9
Removing intermediate container 0c2270a7fac4
Successfully built 4ee1ac73e1f9
Successfully tagged oracle/restdataservices:18.1.1

Oracle Rest Data Services version 18.1.1 is ready to be extended:

--> oracle/restdataservices:18.1.1

Build completed in 19 seconds.

$*
```

*现在你有一个全新的 ORDS 码头形象，在我的情况下包含 ORDS 18.1.1:*

```
*$ docker images
REPOSITORY                 TAG             IMAGE ID        CREATED           SIZE
oracle/restdataservices    18.1.1          4ee1ac73e1f9    52 seconds ago    395MB
oracle/serverjre           8               f65b2559f3a5    10 minutes ago    269MB
oracle/database            12.2.0.1-se2    323887b92e8f    3 weeks ago       6.38GB
oracle/database            12.2.0.1-ee     08d230aa1d55    3 weeks ago       6.39GB
oracle/database            12.1.0.2-se2    b4999e09453e    3 weeks ago       5.08GB
oracle/database            12.1.0.2-ee     aee62bc26119    3 weeks ago       5.18GB
oracle/database            11.2.0.2-xe     50712d409891    3 weeks ago       809MB
oraclelinux                7-slim          9870bebfb1d5    5 months ago      118MB*
```

*这里还有最后一点需要补充:默认情况下，`buildDockerImage.sh`脚本会对 ORDS zip 文件运行`md5sum`校验和，以确保文件完好无损。您会看到这是构建脚本的第一个输出。您可以通过传递`-i`选项跳过该校验和。一般来说，没有必要跳过校验和步骤，但是，ORDS 每季度发布一次，可能 GitHub repo 还没有更新最新的校验和文件。在这种情况下，您仍然可以通过`-i`绕过校验和来构建您最新最棒的 ORDS Docker 映像。*

# *在 Docker 容器中运行 ORDS*

*现在您已经有了一个 ORDS Docker 映像，是时候运行它的实际容器了。由于 ORDS 是 Oracle 数据库前面的 REST 服务器，您需要一个 Oracle 数据库，ORDS 可以为您启用 REST。我已经在同一台机器上安装了 Oracle 数据库 Docker 映像，因此我将继续在 Docker 容器中启用 REST 数据库。然而，我应该指出，在 Docker 容器中拥有一个 **Oracle 数据库并不是在 Docker 中运行 ORDS 的必要条件！**事实上，无论您的 Oracle 数据库在哪里运行，Docker、本地、服务器、云中等等，您都可以用 ORDS 管理许多 Oracle 数据库。*

# *设置 Docker 网络*

***如果您的 Oracle 数据库没有在 Docker 容器中运行，您可以跳过这一步！***

*因为数据库和 ORDS 都运行在 Docker 中，所以我首先必须建立一个 Docker 网络，这两个容器可以用它来相互通信。创建网络很容易，只需一个简单的命令`docker network create`:*

```
*$ docker network create ords-database-network
b96c9fd9062f3aa0fb37db3dcd6319c6e3daefb99f52b82819e06f84b3ad38a0
$ docker network ls
NETWORK ID NAME DRIVER SCOPE
0cad4fa350c8 bridge bridge local
0e6f604bfce9 host host local
26709c337f2f none null local
b96c9fd9062f ords-database-network bridge local
$*
```

# *运行 Oracle 数据库 Docker 容器*

***如果您的 Oracle 数据库没有在 Docker 容器中运行，您可以跳过这一步！***

**关于如何在 Docker 中运行 Oracle 数据库，请参见* [*创建 Oracle 数据库 Docker 映像*](https://geraldonit.com/2017/08/21/creating-an-oracle-database-docker-image/) *。**

*一旦定义了网络，现在就可以启动一个新的 Oracle 数据库容器。`docker run`命令中的`--network`选项将允许您将数据库容器连接到 Docker 网络:*

```
*$ docker run -d --name ords-db --network=ords-database-network -v /home/oracle/oradata:/opt/oracle/oradata oracle/database:12.2.0.1-ee
074e2e85ccdaf00d4ae5d93f56a2532154070a8f95083a343e78a753d748505b
$*
```

# *运行 Oracle REST 数据服务 Docker 容器*

*要运行 ORDS 码头集装箱，您必须了解以下详细信息:*

*   *`ORACLE_HOST`:运行 Oracle 数据库的主机(默认:localhost)*
*   *`ORACLE_PORT`:运行 Oracle 数据库的端口(默认为 1521)*
*   *`ORACLE_SERVICE`:ORDS 应该连接的 Oracle 数据库服务名(默认:ORCLPDB1)*
*   *`ORACLE_PWD`:Oracle 数据库的 SYS 口令*
*   *`ORDS_PWD`:您想要使用的 ORDS 用户密码*
*   *存储 ORDS 配置文件的卷*

*一旦你拥有了所有这些，你就可以通过`docker run`命令运行你的 ORDS Docker 容器了。*

***注意:**因为我的数据库也在 Docker 中运行，所以我必须指定`--network`参数，以便允许两个容器进行通信。Docker 网络中我的数据库主机的主机名与我的数据库 Docker 容器名相同。因此我将使用`-e ORACLE_HOST=ords-db`。*

***如果 Docker 中没有运行 Oracle 数据库，可以跳过** `**--network**` **参数！***

```
*$ docker run --name ords \
> -p 8888:8888 \
> --network=ords-database-network \
> -e ORACLE_HOST=ords-db \
> -e ORACLE_PORT=1521 \
> -e ORACLE_SERVICE=ORCLPDB1 \
> -e ORACLE_PWD=LetsDocker \
> -e ORDS_PWD=LetsORDS \
> -v /home/oracle/ords:/opt/oracle/ords/config/ords:rw \
> oracle/restdataservices:18.1.1
May 07, 2018 3:47:43 AM
INFO: Updated configurations: defaults, apex_pu
May 07, 2018 3:47:43 AM oracle.dbtools.installer.InstallerBase log
INFO: Installing Oracle REST Data Services version 18.1.1.95.1251
May 07, 2018 3:47:43 AM oracle.dbtools.installer.Runner log
INFO: ... Log file written to /opt/oracle/ords/logs/ords_install_core_2018-05-07_034743_00762.log
May 07, 2018 3:47:44 AM oracle.dbtools.installer.Runner log
INFO: ... Verified database prerequisites
May 07, 2018 3:47:45 AM oracle.dbtools.installer.Runner log
INFO: ... Created Oracle REST Data Services schema
May 07, 2018 3:47:45 AM oracle.dbtools.installer.Runner log
INFO: ... Created Oracle REST Data Services proxy user
May 07, 2018 3:47:45 AM oracle.dbtools.installer.Runner log
INFO: ... Granted privileges to Oracle REST Data Services
May 07, 2018 3:47:48 AM oracle.dbtools.installer.Runner log
INFO: ... Created Oracle REST Data Services database objects
May 07, 2018 3:47:56 AM oracle.dbtools.installer.Runner log
INFO: ... Log file written to /opt/oracle/ords/logs/ords_install_datamodel_2018-05-07_034756_00488.log
May 07, 2018 3:47:57 AM oracle.dbtools.installer.Runner log
INFO: ... Log file written to /opt/oracle/ords/logs/ords_install_apex_2018-05-07_034757_00832.log
May 07, 2018 3:47:59 AM oracle.dbtools.installer.InstallerBase log
INFO: Completed installation for Oracle REST Data Services version 18.1.1.95.1251\. Elapsed time: 00:00:15.397

2018-05-07 03:48:00.688:INFO::main: Logging initialized [@1507ms](http://twitter.com/1507ms) to org.eclipse.jetty.util.log.StdErrLog
May 07, 2018 3:48:00 AM
INFO: HTTP and HTTP/2 cleartext listening on port: 8888
May 07, 2018 3:48:00 AM
INFO: The document root is serving static resources located in: /opt/oracle/ords/doc_root
2018-05-07 03:48:01.505:INFO:oejs.Server:main: jetty-9.4.z-SNAPSHOT, build timestamp: 2017-11-21T21:27:37Z, git hash: 82b8fb23f757335bb3329d540ce37a2a2615f0a8
2018-05-07 03:48:01.524:INFO:oejs.session:main: DefaultSessionIdManager workerName=node0
2018-05-07 03:48:01.525:INFO:oejs.session:main: No SessionScavenger set, using defaults
2018-05-07 03:48:01.526:INFO:oejs.session:main: Scavenging every 600000ms
May 07, 2018 3:48:02 AM
INFO: Creating Pool:|apex|pu|
May 07, 2018 3:48:02 AM
INFO: Configuration properties for: |apex|pu|
cache.caching=false
cache.directory=/tmp/apex/cache
cache.duration=days
cache.expiration=7
cache.maxEntries=500
cache.monitorInterval=60
cache.procedureNameList=
cache.type=lru
db.hostname=ords-db
db.password=******
db.port=1521
db.servicename=ORCLPDB1
db.username=ORDS_PUBLIC_USER
debug.debugger=false
debug.printDebugToScreen=false
error.keepErrorMessages=true
error.maxEntries=50
jdbc.DriverType=thin
jdbc.InactivityTimeout=1800
jdbc.InitialLimit=3
jdbc.MaxConnectionReuseCount=1000
jdbc.MaxLimit=10
jdbc.MaxStatementsLimit=10
jdbc.MinLimit=1
jdbc.statementTimeout=900
log.logging=false
log.maxEntries=50
misc.compress=
misc.defaultPage=apex
security.disableDefaultExclusionList=false
security.maxEntries=2000

May 07, 2018 3:48:02 AM
WARNING: *** jdbc.MaxLimit in configuration |apex|pu| is using a value of 10, this setting may not be sized adequately for a production environment ***
May 07, 2018 3:48:02 AM
WARNING: *** jdbc.InitialLimit in configuration |apex|pu| is using a value of 3, this setting may not be sized adequately for a production environment ***
May 07, 2018 3:48:02 AM
INFO: Oracle REST Data Services initialized
Oracle REST Data Services version : 18.1.1.95.1251
Oracle REST Data Services server info: jetty/9.4.z-SNAPSHOT

2018-05-07 03:48:02.710:INFO:oejsh.ContextHandler:main: Started o.e.j.s.ServletContextHandler@48eff760{/ords,null,AVAILABLE}
2018-05-07 03:48:02.711:INFO:oejsh.ContextHandler:main: Started o.e.j.s.h.ContextHandler@402f32ff{/,null,AVAILABLE}
2018-05-07 03:48:02.711:INFO:oejsh.ContextHandler:main: Started o.e.j.s.h.ContextHandler@573f2bb1{/i,null,AVAILABLE}
2018-05-07 03:48:02.721:INFO:oejs.AbstractNCSARequestLog:main: Opened /tmp/ords_log/ords_2018_05_07.log
2018-05-07 03:48:02.755:INFO:oejs.AbstractConnector:main: Started ServerConnector@2aece37d{HTTP/1.1,[http/1.1, h2c]}{0.0.0.0:8888}
2018-05-07 03:48:02.755:INFO:oejs.Server:main: Started [@3576ms](http://twitter.com/3576ms)*
```

*现在 ORDS 已经启动并运行了，您可以开始启用您的数据库了。注意，所有的配置文件都在一个卷中，在我的例子中是`-v /home/oracle/ords:/opt/oracle/ords/config/ords:rw`。如果您想更改任何 ORDS 配置，只需在卷中进行更改，然后根据需要重新启动容器。*

**原载于 2018 年 5 月 14 日【geraldonit.com】[](https://geraldonit.com/2018/05/14/creating-an-oracle-rest-data-services-docker-image/)**。****