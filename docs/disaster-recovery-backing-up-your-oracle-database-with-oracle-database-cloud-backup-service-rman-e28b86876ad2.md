# 灾难恢复:使用 Oracle 数据库云备份服务、RMAN 和对象存储备份您的 Oracle 数据库

> 原文：<https://medium.com/oracledevs/disaster-recovery-backing-up-your-oracle-database-with-oracle-database-cloud-backup-service-rman-e28b86876ad2?source=collection_archive---------2----------------------->

## 如何备份 Oracle 云数据库

![](img/183ca9df66b72aed0b1f2c6c1a9728a6.png)

Photo by [Kevin Ku](https://unsplash.com/@ikukevk?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

我们如何为灾难做准备？如果服务器崩溃，技术人员拔掉机器插头，或者流星击中数据中心，企业如何恢复关键数据？在本教程中，我们将演示如何使用 Oracle 数据库云备份服务、RMAN(恢复管理器)和对象存储来备份 Oracle 云数据库。在这里，Oracle 数据库云备份服务支持与 Oracle 云基础架构(OCI)的连接和通信，而 RMAN 则负责将您的 Oracle 云数据库备份到 OCI 对象存储。

> 注意:有许多不同的方法来备份 Oracle 云数据库，这代表了一种选择。
> 
> 注意:鉴于客户环境的多样性以及 Oracle 云基础架构的不断变化，请半信半疑地接受下面列出的命令及其解释。

# 轮廓:

1.  使用 Oracle 数据库云备份服务的先决条件
2.  安装 Oracle 数据库云备份服务的命令完整列表
3.  安装 Oracle 云备份数据库服务的先决条件
4.  安装 RMAN
5.  配置 RMAN 和备份数据库
6.  通过 SQL Plus 访问数据库表
7.  结论

## 使用 Oracle 数据库云备份服务的先决条件

在我们开始之前，[这里有一个链接，指向使用 Oracle 云备份服务所需的所有先决条件](https://docs.oracle.com/en/cloud/paas/db-backup-cloud/csdbb/installing-oracle-database-cloud-backup-module-oci.html#GUID-8BAB6043-F8BD-44CE-AED3-87B57743AE72)。我会尽量为每个先决条件提供尽可能多的上下文，并提供有用的链接和例子。

*   检查并确保您运行的是[正确的数据库版本和操作系统](https://docs.oracle.com/en/cloud/paas/db-backup-cloud/csdbb/supported-databases-and-operating-systems.html)。在本模块中，我使用的是企业极限性能版版本 19c。
*   [在 OCI 设置对象存储](https://docs.cloud.oracle.com/en-us/iaas/Content/Object/Concepts/objectstorageoverview.htm)(Oracle 云基础架构)。我建议简单地使用控制台，访问对象存储，并创建一个存储桶。
*   在托管数据库的虚拟机中，[生成密钥](https://docs.cloud.oracle.com/en-us/iaas/Content/API/Concepts/apisigningkey.htm)以访问 OCI。本质上，这就是它的样子。(下面的脚本假设您已经在托管数据库的 VM 实例中。)[这就是](https://docs.cloud.oracle.com/en-us/iaas/Content/API/Concepts/apisigningkey.htm#three)你将密钥上传到 OCI 的方式，这样你的虚拟机就可以与 OCI 通信/连接。

*   检查您是否有 JDK 1.7 或更高版本。您可以通过在 linux 终端中键入“java -version”来检查您拥有的版本。您应该会得到类似如下的输出:

```
java version “1.8.0_191”
Java(TM) SE Runtime Environment (build 1.8.0_191-b12)
Java HotSpot(TM) 64-Bit Server VM (build 25.191-b12, mixed mode)
```

*   最后，您希望下载最新版本的 Oracle 云备份服务。[这是下载备份服务的链接](https://www.oracle.com/database/technologies/oracle-cloud-backup-downloads.html)。这应该下载一个名为 opc_installer 的 zip 文件。解压后，你应该有两个文件夹，opc_installer 和 oci_installer。这是安装云备份服务的两种方式。在本教程中，我将使用 oci 版的 oci_installer(不是 OCI 经典版)。

> 注意:如果您在不同于托管数据库的计算机上下载了 Oracle 云备份服务，则可以使用此命令将密钥从本地计算机复制到数据库主机实例:

```
#generic command
cp -i <file/path/to/your/private_key> oci_install.jar opc@<vm_db_ip>:~”. #example
cp -i /Users/abibi/.ssh/id_rsa oci_install.jar [opc@130.147.26.83](mailto:opc@129.146.56.93) :~
```

> 注意:要 SSH 到托管数据库的虚拟机:

```
#example
ssh -i /Users/abibi/.ssh/id_rsa [opc@130.246.38.51](mailto:opc@129.146.58.214)1
```

## 安装 Oracle 数据库云备份服务的完整命令列表

我这样做有点倒退，在详细说明命令的先决条件之前，提供了安装 oci_install.jar 文件的命令列表。我这样做是因为我想提供一个良好的命令流。如果您想了解有关这些命令的更多详细信息或遇到问题，只需参考下一节中列出的先决条件。

> 注意:如果你已经执行了上面列出的一些命令，就不需要重复了。

```
#copy the newly downloaded oci_install.jar file to your database instance, assumes that the oci_install file in your current directoryscp -i <private_key> oci_install.jar opc@<db_ip>:~#SSH into the instance
ssh -i <private_key> opc@<db_ip>#maybe log into your Oracle user--see note below
#sudo su - oracle#create directories for installing jar file
mkdir ~/lib
mkdir ~/wallet#directory for oci credentials
mkdir .oci#generating key for OCI
openssl genrsa -out ~/.oci/oci_api_key.pem 2048
openssl rsa -pubout -in ~/.oci/oci_api_key.pem -out ~/.oci/oci_api_key_public.pem#change directory to oci folder
cd .oci#note: to see hidden files, enter "ls -a"#output the key, copy and paste into user ssh key
cat oci_api_key_public.pem#cd into folder containing oci_install.jar file, should be in opc folder#I assume the opc directory is where the jar file is located
cd opc#generic command to install backup service
java -jar oci_install.jar -host <object storage endpoint> -pvtKeyFile ~/.oci/oci_api_key.pem -pubFingerprint <fingerprint> -tOCID <tOCID> -cOCID <cOCID> -uOCID <uOCID> -walletDir ~/wallet -libDir ~/lib -bucket <bucketname>
```

> 注意:根据您正在安装的数据库版本，您可能需要将文件从数据库实例中的一个目录复制到另一个目录。为此，我建议访问 root 用户，复制文件，并更改文件的权限:

```
#becoming root user
sudo su#example of copying wallet credentials from opc to oracle user
cp /home/opc/wallet/cwallet.sso /u01/app/oracle#changing permission access for a particular file such that other users can access the file
chmod 775 /home/opc/wallet/cwallet.sso
```

> 注意:另一个选项是以实例的 Oracle 用户身份登录，并运行上面的命令。要做到这一点，您可以在通过 SSH 连接到实例后输入以下命令:

```
sudo su - oracle
```

## 安装 Oracle 云备份数据库服务的先决条件

为了执行将安装 Oracle 云备份数据库服务的 jar 文件，我们需要从 Oracle 云租赁中收集一些信息:

*   Host:我们的对象存储端点，指示我们创建 bucket 的区域。下面是有效值的[列表。](https://docs.cloud.oracle.com/en-us/iaas/api/#/en/objectstorage/20160918/)
*   pvtKeyFile:OCI _ API _ key 的位置，它是我们之前在数据库虚拟机中生成的。
*   pubFingerprint:对应于 oci_api_key 的指纹。
*   托西德:我们的租赁 OCID
*   cOCID:存放对象存储和数据库实例的隔离舱 OCID。
*   我们的用户是 OCID。

> 注意:OCID 是一个唯一的值，它标识 OCI 的特定组件，如用户、隔离专区、租用和服务。了解更多关于 OCIDs，[这里](https://docs.cloud.oracle.com/en-us/iaas/Content/General/Concepts/identifiers.htm)。

*   walletDir:新创建的钱包的位置。

> 注意:如果您想在当前工作目录中创建一个文件夹，只需键入以下内容:

```
mkdir ~/wallet
```

*   libDir:新创建的库的位置。

> 注意:如果您想在当前工作目录中创建一个文件夹，只需键入以下内容。

```
mkdir ~/lib
```

以下是安装 Oracle 云备份数据库服务的通用命令:

以下是一个命令示例:

> 注意:为了更容易理解，我把命令分开了，但是当在 linux 中执行这些命令时，它们应该都在一行中，用空格隔开。
> 
> 注意:如果您遇到错误，[这里有一些文档](https://docs.oracle.com/en/cloud/paas/db-backup-cloud/csdbb/problems-installing-backup-module.html)有助于诊断一些常见问题。

我们可以通过[检查](https://docs.oracle.com/en/cloud/paas/db-backup-cloud/csdbb/installing-oracle-database-cloud-backup-module-oci.html#GUID-3DB64657-43CD-4100-8326-6EF78C1A7139)是否创建了 wallet 和 lib 文件夹，以及是否看到包含 OPC_HOST、OPC_WALLET、OPC_CONTAINER、OPC_COMPARTMENT 和 OPC_AUTH_SCHEME 值的“config”文件来验证 jar 文件安装成功。

> 注意:要打开和读取 linux 文件，我建议通过键入以下命令来安装 nano(假设您位于可以访问配置文件的目录中):

```
yum install nano
nano config
```

## 安装 RMAN

现在，我们已经成功安装了 Oracle 数据库云备份服务并建立了到 OCI 的连接，让我们设置 RMAN (Oracle Recovery Manager)来管理我们的数据库到对象存储的备份。

先决条件:

*   Oracle DB 口令，预配 Oracle 云数据库实例时输入的口令。
*   RMAN 二进制文件的安装位置

```
#to find the rman binaries
find / -iname rman
```

> 注意:如果有权限错误，请输入命令成为根用户

```
#example output should look something like this:
/opt/oracle/dcs/log/ary/rman
/tmp/dcsserver/rman
/u01/app/19.0.0.0/grid/bin/rman
/u01/app/oracle/product/19.0.0.0/dbhome_1/bin/rman
```

*   将 RMAN 二进制位置添加到当前路径

```
#generic example of path
export PATH=/u01/app/oracle/product/<insert DB version number>/dbhome_1/bin
```

*   设置环境变量 ORACLE_HOME 和 ORACLE_SID

```
#not really sure how important this, but points to database path
export ORACLE_HOME=/u01/app/oracle/product/19.0.0.0/dbhome_1#a unique identifier for your database, can find this in the console where you database is provisioned
export ORACLE_SID=DB1111#outputs all environment variables to confirm
env#outputs config info for the database
srvctl config database -d <database unique identifier>
```

## 配置 RMAN 和备份数据库

现在，我们已经准备好配置 RMAN，以便将 Oracle 云数据库备份到对象存储。只需输入以下命令来设置/授权到数据库的 RMAN 连接:

```
#generic, TARGET is the command, SYS is the user, oracle is the password, and trgt2 is the database unique identifier 
rman TARGET SYS/oracle@trgt2#example
rman TARGET SYS/mypassword@DB111_asc3fp
```

此时，您应该会在终端底部看到一个挥之不去的“RMAN ”,并在上面显示一条消息，说明您已经成功连接到数据库。

> 注意:[这里有一些额外的文档](https://docs.oracle.com/cd/B10501_01/server.920/a96566/rcmcnctg.htm#445078)和[另一个来源](https://docs.oracle.com/database/121/ADMQS/GUID-7ACCA8DF-537B-4DA9-A7C7-306FBFC9D903.htm#ADMQS12427)，有助于使用 RMAN 登录数据库。

接下来，我们需要在备份数据库之前配置一些 RMAN 参数:

```
CONFIGURE CHANNEL DEVICE TYPE 'SBT_TAPE' PARMS 'SBT_LIBRARY=/path/to/libopc.so, SBT_PARMS=(OPC_PFILE=/path/to/config)';
```

一些附加命令:

```
CONFIGURE CONTROLFILE AUTOBACKUP ON;
CONFIGURE DEFAULT DEVICE TYPE TO SBT_TAPE;
CONFIGURE BACKUP OPTIMIZATION ON;
CONFIGURE CONTROLFILE AUTOBACKUP FORMAT FOR DEVICE TYPE SBT_TAPE TO '%F';
CONFIGURE ENCRYPTION FOR DATABASE ON;
SET ENCRYPTION IDENTIFIED BY "password" ONLY;
```

> 注意:关于上面列出的命令和其他选项的细节可以在[这里](https://docs.oracle.com/cd/B19306_01/backup.102/b14192/setup004.htm)找到。

最后:

```
BACKUP DATABASE;
```

此时，该命令应该会将数据库备份到前面定义的对象存储中。要确认备份成功，只需检查数据库内容和元数据文件以及其他相关对象的对象存储。

## 通过 SQL Plus 访问数据库表

顺便说一下，为了连接到数据库表，这里有一个到一些文档的链接。[关于连接数据库的更多信息](https://docs.cloud.oracle.com/en-us/iaas/Content/Database/Tasks/connectingDB.htm)。下面是一个通过 SQL Plus 访问数据库表的命令。

```
#generic script to access database tables as system user
sqlplus system/@*(DESCRIPTION=(CONNECT_TIMEOUT=5)(TRANSPORT_CONNECT_TIMEOUT=3)(RETRY_COUNT=3)(ADDRESS_LIST=(LOAD_BALANCE=on)(ADDRESS=(PROTOCOL=TCP)(HOST=10.0.0.3)(PORT=1521)))(CONNECT_DATA=(SERVICE_NAME=<unique service name defined in database console under database connection>)))*#example script to access database tables as system user
sqlplus system/@meee.subnet.vcn.oraclevcn.com:1521/DB1111_asb3bs.subnet.vcn.oraclevcn.com
```

## 结论

备份 Oracle 云数据库还有其他方法，为什么选择此选项？我相信这提供了细粒度的控制和设置 cron 作业来调度备份的能力。在涉及对象存储的情况下，可以将数据库备份到数据库没有运行的另一个区域，以便在灾难期间提供高可用性。尽管如此，在控制台、Oracle OCI Python SDK 和许多其他选项中仍有自动备份可用以备份您的数据库。我建议在选择此备份解决方案之前考虑使用情形、RPO、RTO 和其他因素，因为实施此解决方案既繁琐又耗时。

[![](img/9feb2f59889762b163b5adb2600d8f85.png)](https://www.buymeacoffee.com/arshyasharifian)