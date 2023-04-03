# 访问 AWS 实例的恢复，如果。Pem 密钥/文件丢失

> 原文：<https://medium.com/globant/access-recovery-to-aws-instances-if-pem-key-file-is-lost-928c63301643?source=collection_archive---------0----------------------->

# **简介**

在 AWS 中，Linux 实例通过。pem 密钥/文件，。pem 是一个私钥，其对应的公钥存储在服务器/实例上。当我们通过 SSH 访问实例时，身份验证使用私钥和公钥，如果这两者匹配，我们就可以访问实例。

# **背景**

有时，系统管理员或开发人员会在 AWS 中启动实例。他们曾经通过。pem key 但有时系统会损坏或有人离开组织。在这种情况下，我们失去了信心。pem 文件，最终无法访问实例。本文将帮助您理解连接实例的不同方式。

# **点数覆盖**

1.  先决条件
2.  解决方法
3.  摘要
4.  参考

# **1)先决条件**

*   访问 AWS 平台
*   对 EC2、EBS、IAM 服务的写访问权限

# **2)问题陈述**

在假设的情况下，如果你放错/丢失了。PEM AWS Linux 实例的密钥，并且您希望出于任何目的访问您的服务器，那么我们可以获得它吗？

# **3)解了。Pem 密钥/文件丢失问题**

如果缺少实例，我们可以访问它。pem 键使用以下任一解决方案。

A.高级材料情报(Advanced Material Information)

B.体积交换

C.SSM 代理商

D.用户数据

E.EC2 connect(兼容亚马逊 Linux 2.2 及以上或 ubuntu 16.04 及以上)

# **3-A. AMI:**

*   代表亚马逊机器图像。通过 AMI，创建一个新的 AWS 实例并启动它。
*   选择您当前的实例。
*   右键单击它并选择选项创建 AMI。

![](img/ec5ac21cc9c96ef809d20bc29699d7a3.png)

*   给 AMI 一个名称，它将创建您的服务器的一个副本。

![](img/4256a360cb93a01d53527d4483aa0a3a.png)![](img/52c8e6f6257a0eb1ead780c7deb65b36.png)

*   创建 AMI 后，选择 AMI 并右键单击它以选择启动选项。

![](img/81eea0436630bce5b77664458f2b799d.png)

*   启动过程将开始。
*   现在我们必须为启动 AMI 选择适当的细节，因为它将为我们创建一个新的服务器。

![](img/8732968f91365dce8ef87ed2f903b2d4.png)

*   VPC、子网等详细信息需要从配置实例选项中正确选择(参见下面的屏幕截图以供参考)，然后添加存储。

![](img/2c817ec3d51dc6dac210e816b46809c2.png)

*   选择创建新的密钥对，提供密钥对的名称并启动实例”。

![](img/301556df86cca37721e263411598d77f.png)

*   在我们尝试使用 ssh 连接之前，下载密钥对。

![](img/ee073f3c80501debd328d3d8c81fcfe8.png)![](img/c7536ed00dc232623ed5a5db7dbee128.png)

# **3-B .体积交换**

*   假设您有一台称为主服务器(假设是生产服务器)的服务器要通过卷交换进行恢复。
*   创建恢复服务器。
*   停止生产服务器，并删除/分离生产服务器的根卷。(记下根设备连接，例如这里是 dev/xvda)

![](img/93c93a5ebae44438bbc354b7adeb4548.png)![](img/3e78c81f9b10ebd871ac6a7c4d2a1ac9.png)

*   将主服务器卷(根卷)作为辅助卷连接到恢复服务器

![](img/5b4571cbd1aa6948d56f8271a124022b.png)![](img/5f724aab9899f0432488544a88151b5e.png)

*   登录到恢复服务器，转到此处作为辅助服务器连接的生产服务器的卷，使用以下命令找出可用卷。

> lsblk -f

![](img/dbc6bb1209ca5aea7aefd1e0422cb6dd.png)

为装载目的创建新目录

> sudo mkdir/tmp/tmp 卷

要在旧的 Amazon Linux 上挂载:

> sudo mount/dev/xvd f1/tmp/tmp volume

要在新的 Amazon Linux 上挂载:

> sudo mount-o nouid/dev/xvd f1/tmp/tmp volume

![](img/6593a13e4d0fe305d9bf3ce9f06feb84.png)

*   将恢复实例的私钥复制到主实例 authorized_keys 的装载卷，您可以通过 cat 命令看到相同的内容

> 比较 ssh/authorized _ keys/tmp/tmp volume/home/ec2-user/。ssh/授权密钥

![](img/a793bc56353743830b8b4c68f9be08fd.png)

*   从恢复服务器中卸载并分离我们作为辅助卷装载的卷。
*   卸载命令:

> sudo 卸载/tmp/tmp 卷

要分离卷，请执行以下操作:

*   选择卷，右键单击并选择分离卷。

![](img/909b9abeff3a25b52024803e71b88d10.png)

*   单击是，分离按钮。

![](img/7b595ed0c55e8a36a85890cf6c450853.png)

*   将该卷作为根卷连接回生产服务器。(例如 dev/xvda)

![](img/f09ac5ead88aa3d14af8f6de6573afda.png)

*   再次启动生产服务器。
*   尝试通过新复制的私钥进行 ssh，在这种情况下，使用恢复服务器的 pem 文件登录到主服务器。

![](img/ddd6991171201dc1b8905d86d97e8932.png)

# 3-C. SSM

*   为 EC2 实例创建 IAM 角色

![](img/a0663df7e3b2f02e0b634cd7ca024cfc.png)

*   将 AmazonSSMFullAccess 策略附加到 IAM 角色

![](img/6a6b72f34655c5691a41306b9e511444.png)

*   将角色附加到 EC2 实例

![](img/1297f903e47f89a930b423b4f44ed6c0.png)

*   在实例上的 SSM 代理尝试重新连接或重启它以触发重试之前，最多等待 30 分钟。

![](img/1b4c6e7d9cc6b252cf280d35ee3d4fd5.png)

# **三维用户数据**

*   使用 AWS CLI 创建新的密钥对。

> AWS ec2 create-key-pair-key-name my-key-pair-query“key material”-output text > my-key-pair . PEM

![](img/bea76e95c8f40fead2fd355b8a91e551.png)![](img/9cd3722c1a83bece413dd953023183c4.png)

*   如果您在 Amazon EC2 控制台中创建了私钥，那么检索密钥对的公钥。

> ssh-keygen-y-f/pathofpemfile/PEM file

![](img/42687eadfda54f4cfba0a353047cee4c.png)

*   打开 Amazon EC2 控制台。
*   停止您的实例。

![](img/84fde5c0dac0ac774828082d04ed4dd3.png)

*   选择操作、实例设置、编辑用户数据。

![](img/55927b4c29dfdba9e147eefc90d52777.png)

*   将以下脚本复制到“编辑用户数据”对话框中:

![](img/210066e255001e477693f3764a1096fe.png)

下面是用户数据文件

> 内容类型:多部分/混合；boundary = "//"
> MIME-Version:1.0
> —//
> Content-Type:text/cloud-config；charset = " us-ascii "
> MIME-Version:1.0
> Content-Transfer-Encoding:7 bit
> Content-Disposition:附件；filename = " cloud-config . txt "
> *# cloud-config*
> cloud _ final _ modules:
> -【users-groups，once】
> users:
> -name:username
> ssh-authorized _ keys:
> -您的公钥对

*   用您的用户名替换 username，例如 ec2-user。您可以输入默认用户名，也可以输入自定义用户名。
*   用从上述步骤中检索到的公钥替换 PublicKeypair。确保输入完整的公钥，从 ssh-rsa 开始。
*   选择保存。
*   启动您的实例。
*   从用户数据字段中删除脚本很重要，因为它包含一个密钥对。
*   停止您的实例。
*   选择操作、实例设置、编辑用户数据。
*   删除“编辑用户数据”对话框中的所有文本，然后选择“保存”。
*   启动您的实例。

# **3-E. Ec2-连接**

**限制**

*   支持的 Linux 发行版支持:
*   亚马逊 Linux 2(任何版本)
*   Ubuntu 16.04 或更高版本
*   您需要检查登录到系统(AWS 控制台)的用户的权限—您的用户应该具有 EC2 连接权限。否则，在通过 ec2 connect 连接到 Ec2 时，您会收到一条错误消息
*   您可以向您的用户添加权限
*   转到 IAM
*   选择您的用户
*   点击添加权限
*   选择适当的权限，如 here-ec2 instacnecconnect
*   点击添加权限。

一旦添加，它将如下所示..

![](img/afeda0dd705a9d0e5c0d9c487f6aa800.png)

*   要使用 Amazon EC2 控制台(基于浏览器的客户端)进行连接，实例必须有一个公共 IPv4 地址。
*   如果实例没有公共 IP 地址，您可以使用 SSH 客户端或 EC2 Instance Connect CLI 通过专用网络连接到实例，例如从同一个 VPC 内的计算机或从通过 VPN 或 AWS Direct Connect 连接到 VPC 的计算机。
*   EC2 实例连接不支持使用 IPv6 地址的连接。
*   在 https://console.aws.amazon.com/ec2/.[打开亚马逊 EC2 控制台](https://console.aws.amazon.com/ec2/.)
*   在导航窗格中，选择实例。
*   选择实例，然后选择“连接”。

![](img/1b3fc45de7aa2a74088c31b4b6b4f5f0.png)

*   选择 EC2 实例连接。

![](img/199bc95ad51cd1b2edf0c1696217450b.png)

*   验证用户名，然后选取“连接”以打开终端窗口。

![](img/ecd928b65e56b8aadb9641195b1846cc.png)

*   现在，一旦通过浏览器连接到实例，就可以复制系统的 pub 密钥或通过 ssh-keygen 创建一个新密钥，并将其保存到 authorized_keys 下。ssh 文件夹。

![](img/ce32e2f61550cd1bf82a1964f858db2d.png)

*   现在，您可以通过 ssh 连接实例

![](img/773b9e330c5c5d22dc2642be7a25421f.png)

这样，您就可以连接到您的实例，而无需重新启动它。

# **4)结论**

根据这篇文章，以下是解决方案选择的优先顺序。

*   Ec2-Connect:将是最高优先级，因为这样我们可以避免停机。
*   SSM:可以选择作为下一个重点，因为我们需要安装一个 SSM 代理。这种方法也可以避免停机。
*   User-Data:在这种情况下，需要重新启动以应用对用户数据的更改。
*   卷交换:这种方法需要时间来完成，也需要一些停机时间。
*   AMI:将是最后一种方法，因为它将改变 IP 地址和实例 ID。

# **5)参考文献**

1.  [https://docs . AWS . Amazon . com/AWS C2/latest/user guide/session-manager . html](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/session-manager.html)
2.  [https://docs . AWS . Amazon . com/AWS C2/latest/user guide/Connect-using-EC2-Instance-Connect . html](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/Connect-using-EC2-Instance-Connect.html)
3.  [https://AWS . Amazon . com/premium support/knowledge-center/ec2-windows-replace-lost-key-pair/](https://aws.amazon.com/premiumsupport/knowledge-center/ec2-windows-replace-lost-key-pair/)