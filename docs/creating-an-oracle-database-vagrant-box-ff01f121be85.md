# 创建 Oracle 数据库漫游箱

> 原文：<https://medium.com/oracledevs/creating-an-oracle-database-vagrant-box-ff01f121be85?source=collection_archive---------1----------------------->

甲骨文最近发布了一个新的 GitHub 知识库，为人们提供在流浪者盒子中的甲骨文软件。如果您曾经在 VirtualBox VM 中设置过 Oracle 数据库，无论是作为您的沙盒环境、探索 Oracle 数据库，还是将其用作完整的开发环境，事情对您来说都会变得容易得多。有了 vagger，您现在只需几个简单的步骤就可以在 VirtualBox 中配置 Oracle 数据库。

# 你需要什么

1.  您计算机上安装的 Oracle VirtualBox
2.  你的机器上安装了流浪者
3.  Oracle 安装 zip 文件，你可以从 [Oracle 技术网](http://www.oracle.com/technetwork/database/enterprise-edition/downloads/index.html)下载
4.  GitHub 的流浪配置文件，你可以[下载或者克隆库](https://github.com/oracle/vagrant-boxes)

# 设置

我不打算讨论 VirtualBox 或 vagger 的设置，因为它们确实很简单，但也取决于你使用的平台。您可以在 [VirtualBox 安装文档](https://www.virtualbox.org/manual/ch02.html)中找到 VirtualBox 安装说明。同样，你可以在 travel[下载页面](https://www.vagrantup.com/downloads.html)找到 travel 安装程序。

# 创建 Oracle 数据库漫游箱

在您的环境中成功设置了 VirtualBox 和 After 之后，您现在可以继续创建 Oracle 数据库 vagger box 了。首先，您必须[将 Oracle 数据库安装程序 zip 文件](http://www.oracle.com/technetwork/database/enterprise-edition/downloads/index.html)下载到您的本地机器上。您需要下载您想要构建的版本的**Linux x86–64**zip 文件，在我的例子中是 12.2.0.1。完成后，就该克隆/下载 GitHub 库了:

```
gvenzl-mac:vagrant gvenzl$ git clone [https://github.com/oracle/vagrant-boxes](https://github.com/oracle/vagrant-boxes)
Cloning into 'vagrant-boxes'...
remote: Counting objects: 74, done.
remote: Total 74 (delta 0), reused 0 (delta 0), pack-reused 74
Unpacking objects: 100% (74/74), done.
```

接下来，您将进入您想要构建的版本的`vagrant-boxes/OracleDatabase/<version>`文件夹，然后将 Oracle 数据库安装程序 zip 文件复制到该文件夹中:

```
gvenzl-mac:vagrant gvenzl$ cd vagrant-boxes/OracleDatabase/12.2.0.1/
gvenzl-mac:12.2.0.1 gvenzl$ cp ~/Downloads/linuxx64_12201_database.zip .
```

现在你已经准备好建造流浪者盒子了。在安装过程中，脚本会将虚拟机中的 Linux 更新到最新最好的版本，并安装 Oracle 数据库所需的所有软件包，因此**请确保您拥有互联网连接！**为了开始创建虚拟机，您只需键入`vagrant up`，然后等待其完成:

```
gvenzl-mac:12.2.0.1 gvenzl$ vagrant up
Bringing machine 'default' up with 'virtualbox' provider...
==> default: Box '[http://yum.oracle.com/boxes/oraclelinux/latest/ol7-latest.box'](http://yum.oracle.com/boxes/oraclelinux/latest/ol7-latest.box') could not be found. Attempting to find and install...
default: Box Provider: virtualbox
default: Box Version: >= 0
==> default: Box file was not detected as metadata. Adding it directly...
==> default: Adding box '[http://yum.oracle.com/boxes/oraclelinux/latest/ol7-latest.box'](http://yum.oracle.com/boxes/oraclelinux/latest/ol7-latest.box') (v0) for provider: virtualbox
default: Downloading: [http://yum.oracle.com/boxes/oraclelinux/latest/ol7-latest.box](http://yum.oracle.com/boxes/oraclelinux/latest/ol7-latest.box)
==> default: Successfully added box '[http://yum.oracle.com/boxes/oraclelinux/latest/ol7-latest.box'](http://yum.oracle.com/boxes/oraclelinux/latest/ol7-latest.box') (v0) for 'virtualbox'!
==> default: Importing base box '[http://yum.oracle.com/boxes/oraclelinux/latest/ol7-latest.box'](http://yum.oracle.com/boxes/oraclelinux/latest/ol7-latest.box')...
==> default: Matching MAC address for NAT networking...
==> default: Setting the name of the VM: oracle-12201-vagrant
==> default: Clearing any previously set network interfaces...
==> default: Preparing network interfaces based on configuration...
default: Adapter 1: nat
==> default: Forwarding ports...
default: 1521 (guest) => 1521 (host) (adapter 1)
default: 5500 (guest) => 5500 (host) (adapter 1)
default: 22 (guest) => 2222 (host) (adapter 1)
==> default: Running 'pre-boot' VM customizations...
==> default: Booting VM...
==> default: Waiting for machine to boot. This may take a few minutes...
default: SSH address: 127.0.0.1:2222
default: SSH username: vagrant
default: SSH auth method: private key
default:
default: Vagrant insecure key detected. Vagrant will automatically replace
default: this with a newly generated keypair for better security.
default:
default: Inserting generated public key within guest...
default: Removing insecure key from the guest if it's present...
default: Key inserted! Disconnecting and reconnecting using new SSH key...
==> default: Machine booted and ready!
...
...
...
==> default: Creating Pluggable Databases
==> default: 55% complete
==> default: 75% complete
==> default: Executing Post Configuration Actions
==> default: 100% complete
==> default: Look at the log file "/opt/oracle/cfgtoollogs/dbca/ORCLCDB/ORCLCDB.log" for further details.
==> default:
==> default: SQL*Plus: Release 12.2.0.1.0 Production on Fri Feb 9 23:02:02 2018
==> default:
==> default: Copyright (c) 1982, 2016, Oracle. All rights reserved.
==> default:
==> default: Connected to:
==> default: Oracle Database 12c Enterprise Edition Release 12.2.0.1.0 - 64bit Production
==> default: SQL>
==> default: Pluggable database altered.
==> default: SQL>
==> default: Disconnected from Oracle Database 12c Enterprise Edition Release 12.2.0.1.0 - 64bit Production
==> default: INSTALLER: Database created
==> default: INSTALLER: Oratab configured
==> default: Created symlink from /etc/systemd/system/multi-user.target.wants/oracle-rdbms.service to /etc/systemd/system/oracle-rdbms.service.
==> default: INSTALLER: Created and enabled oracle-rdbms systemd's service
==> default: INSTALLER: setPassword.sh file setup
==> default: ORACLE PASSWORD FOR SYS, SYSTEM AND PDBADMIN: VIxtcphKIQs=1
==> default: INSTALLER: Installation complete, database ready to use!
```

成功完成创建后，您将看到消息:`INSTALLER: Installation complete, database ready to use!`此外，您还将看到数据库中自动生成的管理帐户密码(更多关于密码默认设置和重置的信息，见下文)，在我的例子中:`ORACLE PASSWORD FOR SYS, SYSTEM AND PDBADMIN: VIxtcphKIQs=1`

# 使用虚拟机内部的数据库

一旦配置完成，您现在就可以继续在虚拟机中使用数据库了。有几种方法可以做到这一点，都是为了让你的生活更轻松。

# 使用 SSH 进入虚拟机内部

vagger 允许您 ssh 到虚拟机，就像它是您网络中的任何一个随机的 Linux 服务器一样。如果您精通命令行并且这就是您所需要的，那么您可以做一个简单的`vagrant ssh`来在 VM 中获得一个终端。您将以`vagrant` Linux 用户的身份登录，他拥有`sudo`权限来执行任何特权命令:

```
gvenzl-mac:12.2.0.1 gvenzl$ vagrant ssh

Welcome to Oracle Linux Server release 7.4 (GNU/Linux 4.1.12-112.14.13.el7uek.x86_64)

The Oracle Linux End-User License Agreement can be viewed here:

* /usr/share/eula/eula.en_US

For additional packages, updates, documentation and community help, see:

* [http://yum.oracle.com/](http://yum.oracle.com/)

[vagrant@oracle-12201-vagrant ~]$ sudo su - oracle
Last login: Thu May 24 06:19:28 UTC 2018 on pts/0
[oracle@oracle-12201-vagrant ~]$ sqlplus / as sysdba

SQL*Plus: Release 12.2.0.1.0 Production on Thu May 24 06:20:20 2018

Copyright (c) 1982, 2016, Oracle. All rights reserved.

Connected to:
Oracle Database 12c Enterprise Edition Release 12.2.0.1.0 - 64bit Production

SQL>
```

# 通过端口转发直接连接

漫游箱自动设置 VirtualBox 虚拟机和您的主机之间的端口转发。端口转发是 VirtualBox 使用的一种技术，它会自动将给定端口上的所有流量转发到另一端的端口。这项技术的好处在于**它允许您继续使用安装在笔记本电脑上的本地开发工具，并将它们连接到虚拟机内部的数据库！**例如，您可以将本地 SQL Developer 或 SQLcl 连接到虚拟机内部的数据库，就像连接到网络中的任何其他数据库一样。唯一需要记住的技巧是连接到`localhost`，例如:

浮动虚拟机被自动设置为将主机上的端口`1521`转发到虚拟机内部的端口`1521`。这意味着，如果您有任何将打开并与您的`localhost`上的端口`1521`对话的东西，比如 SQLcl，它将自动转发到 VM 内的端口`1521`，在那里正好有一个 Oracle 数据库监听器等待传入的流量。端口`5500`也是如此。如果您打开浏览器并导航到`[https://localhost:5500/em](https://localhost:5500/em)`，您浏览器的流量将被转发到 VM 内的端口`5500`，在那里您有 OEM Express 等待为您的浏览器提供服务。

```
gvenzl-mac:12.2.0.1 gvenzl$ sql system@//localhost:1521/ORCLCDB

SQLcl: Release 18.1.1 Production on Wed May 23 23:24:43 2018

Copyright (c) 1982, 2018, Oracle. All rights reserved.

Password? (**********?) *************
Last Successful login time: Wed May 23 2018 23:24:45 -07:00

Connected to:
Oracle Database 12c Enterprise Edition Release 12.2.0.1.0 - 64bit Production

SQL> exit

Disconnected from Oracle Database 12c Enterprise Edition Release 12.2.0.1.0 - 64bit Production
gvenzl-mac:12.2.0.1 gvenzl$
```

# 重置数据库密码

默认情况下，`SYS`、`SYSTEM`和`PDBADMIN`用户的密码都是相同的自动生成的密码，在设置完成后会打印到输出中。当然，您可以继续使用该密码，或者您可能希望将其重置为更容易记忆的密码。VM 提供了一个简单的`setPassword.sh` shell 脚本，允许您一次将所有密码重置为您选择的密码。只需拨打`/home/oracle/setPassword.sh <your new password>`电话，剩下的事情就会为您完成:

```
gvenzl-mac:12.2.0.1 gvenzl$ vagrant ssh
Last login: Thu May 24 06:25:54 2018 from 10.0.2.2

Welcome to Oracle Linux Server release 7.4 (GNU/Linux 4.1.12-112.14.13.el7uek.x86_64)

The Oracle Linux End-User License Agreement can be viewed here:

* /usr/share/eula/eula.en_US

For additional packages, updates, documentation and community help, see:

* [http://yum.oracle.com/](http://yum.oracle.com/)

[vagrant@oracle-12201-vagrant ~]$ sudo su - oracle
Last login: Thu May 24 06:26:53 UTC 2018 on pts/0
[oracle@oracle-12201-vagrant ~]$ ./setPassword.sh LetsVagrant
The Oracle base remains unchanged with value /opt/oracle

SQL*Plus: Release 12.2.0.1.0 Production on Thu May 24 06:27:42 2018

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
[oracle@oracle-12201-vagrant ~]$ sqlplus system/LetsVagrant@ORCLPDB1

SQL*Plus: Release 12.2.0.1.0 Production on Thu May 24 06:27:56 2018

Copyright (c) 1982, 2016, Oracle. All rights reserved.

Last Successful login time: Thu May 24 2018 06:24:45 +00:00

Connected to:
Oracle Database 12c Enterprise Edition Release 12.2.0.1.0 - 64bit Production

SQL> exit
Disconnected from Oracle Database 12c Enterprise Edition Release 12.2.0.1.0 - 64bit Production
[oracle@oracle-12201-vagrant ~]$
```

# 停止和重新启动虚拟机

如果你想停止虚拟机，你只需输入一个简单的`vagrant halt`命令:

```
gvenzl-mac:12.2.0.1 gvenzl$ vagrant halt
==> default: Attempting graceful shutdown of VM...
gvenzl-mac:12.2.0.1 gvenzl$ vagrant ssh
VM must be running to open SSH connection. Run `vagrant up`
to start the virtual machine.
gvenzl-mac:12.2.0.1 gvenzl$
```

同样，如果您想再次启动 VM，只需再次使用`vagrant up`命令。这一次，vagger 将检测到虚拟机已经存在，并启动它:

```
gvenzl-mac:12.2.0.1 gvenzl$ vagrant up
Bringing machine 'default' up with 'virtualbox' provider...
==> default: Clearing any previously set forwarded ports...
==> default: Clearing any previously set network interfaces...
==> default: Preparing network interfaces based on configuration...
default: Adapter 1: nat
==> default: Forwarding ports...
default: 1521 (guest) => 1521 (host) (adapter 1)
default: 5500 (guest) => 5500 (host) (adapter 1)
default: 22 (guest) => 2222 (host) (adapter 1)
==> default: Running 'pre-boot' VM customizations...
==> default: Booting VM...
==> default: Waiting for machine to boot. This may take a few minutes...
default: SSH address: 127.0.0.1:2222
default: SSH username: vagrant
default: SSH auth method: private key
==> default: Machine booted and ready!
==> default: Checking for guest additions in VM...
==> default: Setting hostname...
==> default: Mounting shared folders...
default: /vagrant => /Users/gvenzl/Downloads/vagrant/vagrant-boxes/OracleDatabase/12.2.0.1
==> default: Machine already provisioned. Run `vagrant provision` or use the `--provision`
==> default: flag to force provisioning. Provisioners marked to run always will still run.
gvenzl-mac:12.2.0.1 gvenzl$ vagrant ssh
Last login: Thu May 24 06:27:34 2018 from 10.0.2.2

Welcome to Oracle Linux Server release 7.4 (GNU/Linux 4.1.12-112.14.13.el7uek.x86_64)

The Oracle Linux End-User License Agreement can be viewed here:

* /usr/share/eula/eula.en_US

For additional packages, updates, documentation and community help, see:

* [http://yum.oracle.com/](http://yum.oracle.com/)

[vagrant@oracle-12201-vagrant ~]$ exit
logout
Connection to 127.0.0.1 closed.
gvenzl-mac:12.2.0.1 gvenzl$
```

# 销毁/删除虚拟机

如果您不再需要该虚拟机，您可以通过`vagrant destroy`命令轻松删除它。此命令将从您的计算机中删除虚拟机以及与其关联的所有文件。无论虚拟机是否在运行，该命令都会起作用，因此要小心:

```
gvenzl-mac:12.2.0.1 gvenzl$ vagrant destroy
default: Are you sure you want to destroy the 'default' VM? [y/N] y
==> default: Forcing shutdown of VM...
==> default: Destroying VM and associated drives...
gvenzl-mac:12.2.0.1 gvenzl$
```

请注意，一旦执行了`vagrant destroy`，一个`vagrant up`将再次为您提供一个新的虚拟机。

# 结论

流浪者是一个伟大的工具，以简化 VirtualBox 虚拟机(以及更多)的创建。只需使用简单的一行命令，您就可以创建、启动、恢复、暂停、停止和销毁虚拟机，而无需了解所有复杂的配置步骤。

*原载于 2018 年 6 月 11 日*[*【geraldonit.com】*](https://geraldonit.com/2018/06/11/creating-an-oracle-database-vagrant-box/)*。*