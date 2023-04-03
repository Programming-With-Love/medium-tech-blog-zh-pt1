# 使用 gron 的 JSON 处理管道

> 原文：<https://medium.com/capital-one-tech/json-processing-pipelines-with-gron-6fbd531155d7?source=collection_archive---------0----------------------->

## 使用 Linux 工具解析和操作用 gron 转换的 JSON

![](img/cbf2e26597ba9ac8c011dd3be5d364e5.png)

作为一名通晓多种语言的程序员，我总是努力使用最简单的方法和最好的工具来完成工作。我在 Java、Python 和 Go 中解析过 JSON，但是我认为太多时候我们忽略了诸如 sed、awk、cut 等 UNIX/Linux 工具。太多的程序员编写笨重的数据解析器，这是多余的。有了 [gron](https://github.com/tomnomnom/gron) 转换，我发现利用这些强大的 UNIX/Linux 文本编辑、操作和过滤工具变得更加容易。

虽然 jq 在解析已知的 JSON 结构方面很强大，但是它的主要缺点是需要知道正在解析的 JSON 结构。gron 限制较少，可以很容易地与上面的 Linux 工具结合起来，构建非常强大的解析管道，而不需要确切地知道在哪里可以得到特定的结构或值。

# **安装 gron**

安装 gron 的说明可以在[这里](https://github.com/tomnomnom/gron#installation)找到。我使用 brew 安装 gron，然后，出于稍后将显而易见的原因，我添加了以下别名:

别名 norg="gron — ungron "。

# **使 JSON 可 greppable**

显然，作为基于文本的，JSON 已经是“greppable”了。然而，gron 的优势在于它能够将 JSON 分解成被称为“离散赋值”的行。

给出下面的 JSON 片段(来自 AWS EC2 CLI 调用):

```
{  
     "Reservations": [  
         {  
             "OwnerId": "<OWNER_ID>",   
             "ReservationId": "<RES_ID>",   
             "Groups": [],   
             "Instances": [  
                 {  
                     "Monitoring": {  
                         "State": "disabled"  
                     },   
                     "PublicDnsName": "",   
                     "State": {  
                         "Code": 16,   
                         "Name": "running"  
                     },   
                     "EbsOptimized": false,   
                     "LaunchTime": "2016-08-31T22:39:37.000Z",   
                     "PublicIpAddress": "<PUBLIC_IP>",   
                     "PrivateIpAddress": "<PRIVATE_IP>",   
                     "ProductCodes": [],   
                     "VpcId": "<VPC_ID>",   
                     "StateTransitionReason": "",   
                     "InstanceId": "<ID>",   
                     "ImageId": "<AMI_ID>",   
                     "PrivateDnsName": "<PRIVATE_DNS_NAME>",   
                     "KeyName": "<KEY_NAME>",   
                     "SecurityGroups": [...
```

gron 将解析(cat ~/ec2.json | gron)并将 json 转换成离散赋值行:

```
json = {};  
json.Reservations = [];  
json.Reservations[0] = {};  
json.Reservations[0].Groups = [];  
json.Reservations[0].Instances = [];  
json.Reservations[0].Instances[0] = {};  
json.Reservations[0].Instances[0].AmiLaunchIndex = 0;  
json.Reservations[0].Instances[0].Architecture = "x86_64";  
json.Reservations[0].Instances[0].BlockDeviceMappings = [];  
json.Reservations[0].Instances[0].BlockDeviceMappings[0] = {};  
json.Reservations[0].Instances[0].BlockDeviceMappings[0].DeviceName = "/dev/xvda";  
json.Reservations[0].Instances[0].BlockDeviceMappings[0].Ebs = {};  
json.Reservations[0].Instances[0].BlockDeviceMappings[0].Ebs.AttachTime = "2016-08-21T22:00:41.000Z";  
json.Reservations[0].Instances[0].BlockDeviceMappings[0].Ebs.DeleteOnTermination = true;  
json.Reservations[0].Instances[0].BlockDeviceMappings[0].Ebs.Status = "attached";  
json.Reservations[0].Instances[0].BlockDeviceMappings[0].Ebs.VolumeId = "<VOL_ID>";  
json.Reservations[0].Instances[0].ClientToken = "<CLIENT_TOKEN>";  
json.Reservations[0].Instances[0].EbsOptimized = false;  
json.Reservations[0].Instances[0].Hypervisor = "xen";  
json.Reservations[0].Instances[0].ImageId = "<AMI_ID>";  
json.Reservations[0].Instances[0].InstanceId = "<ID>";  
json.Reservations[0].Instances[0].InstanceType = "t2.small";  
json.Reservations[0].Instances[0].KeyName = "<KEY_NAME>";  
json.Reservations[0].Instances[0].LaunchTime = "2016-08-31T22:39:37.000Z";  
json.Reservations[0].Instances[0].Monitoring = {};  
json.Reservations[0].Instances[0].Monitoring.State = "disabled";  
json.Reservations[0].Instances[0].NetworkInterfaces = [];  
json.Reservations[0].Instances[0].NetworkInterfaces[0] = {};  
json.Reservations[0].Instances[0].NetworkInterfaces[0].Association = {};  
json.Reservations[0].Instances[0].NetworkInterfaces[0].Association.IpOwnerId = "<OWNER_ID>";  
json.Reservations[0].Instances[0].NetworkInterfaces[0].Association.PublicDnsName = "";  
json.Reservations[0].Instances[0].NetworkInterfaces[0].Association.PublicIp = "<PUBLIC_IP>";  
json.Reservations[0].Instances[0].NetworkInterfaces[0].Attachment = {};  
json.Reservations[0].Instances[0].NetworkInterfaces[0].Attachment.AttachTime = "2016-08-21T22:00:40.000Z";  
json.Reservations[0].Instances[0].NetworkInterfaces[0].Attachment.AttachmentId = "<ENI_ID>";  
json.Reservations[0].Instances[0].NetworkInterfaces[0].Attachment.DeleteOnTermination = true;  
json.Reservations[0].Instances[0].NetworkInterfaces[0].Attachment.DeviceIndex = 0;  
json.Reservations[0].Instances[0].NetworkInterfaces[0].Attachment.Status = "attached";  
json.Reservations[0].Instances[0].NetworkInterfaces[0].Description = "Primary network interface";  
json.Reservations[0].Instances[0].NetworkInterfaces[0].Groups = [];  
json.Reservations[0].Instances[0].NetworkInterfaces[0].Groups[0] = {};  
json.Reservations[0].Instances[0].NetworkInterfaces[0].Groups[0].GroupId = "<SG_ID>";  
json.Reservations[0].Instances[0].NetworkInterfaces[0].Groups[0].GroupName = "Bastion";  
json.Reservations[0].Instances[0].NetworkInterfaces[0].MacAddress = "<MAC_ADDRESS>";  
json.Reservations[0].Instances[0].NetworkInterfaces[0].NetworkInterfaceId = "<ENI_ID>";  
json.Reservations[0].Instances[0].NetworkInterfaces[0].OwnerId = "<OWNER_ID>";  
json.Reservations[0].Instances[0].NetworkInterfaces[0].PrivateIpAddress = "<PRIVATE_IP>";  
json.Reservations[0].Instances[0].NetworkInterfaces[0].PrivateIpAddresses = [];  
json.Reservations[0].Instances[0].NetworkInterfaces[0].PrivateIpAddresses[0] = {};  
json.Reservations[0].Instances[0].NetworkInterfaces[0].PrivateIpAddresses[0].Association = {};  
json.Reservations[0].Instances[0].NetworkInterfaces[0].PrivateIpAddresses[0].Association.IpOwnerId = "<OWNER_ID>";  
json.Reservations[0].Instances[0].NetworkInterfaces[0].PrivateIpAddresses[0].Association.PublicDnsName = "";  
json.Reservations[0].Instances[0].NetworkInterfaces[0].PrivateIpAddresses[0].Association.PublicIp = "<PUBLIC_IP>";  
json.Reservations[0].Instances[0].NetworkInterfaces[0].PrivateIpAddresses[0].Primary = true;  
json.Reservations[0].Instances[0].NetworkInterfaces[0].PrivateIpAddresses[0].PrivateIpAddress = "<PRIVATE_IP>";  
json.Reservations[0].Instances[0].NetworkInterfaces[0].SourceDestCheck = true;  
json.Reservations[0].Instances[0].NetworkInterfaces[0].Status = "in-use";  
json.Reservations[0].Instances[0].NetworkInterfaces[0].SubnetId = "<SUBNET_ID>";  
json.Reservations[0].Instances[0].NetworkInterfaces[0].VpcId = "<VPC_ID>";  
json.Reservations[0].Instances[0].Placement = {};  
json.Reservations[0].Instances[0].Placement.AvailabilityZone = "us-east-1a";  
json.Reservations[0].Instances[0].Placement.GroupName = "";  
json.Reservations[0].Instances[0].Placement.Tenancy = "default";  
json.Reservations[0].Instances[0].PrivateDnsName = "<DNS_NAME>";  
json.Reservations[0].Instances[0].PrivateIpAddress = "<PRIVATE_IP>";  
json.Reservations[0].Instances[0].ProductCodes = [];  
json.Reservations[0].Instances[0].PublicDnsName = "";  
json.Reservations[0].Instances[0].PublicIpAddress = "<PUBLIC_IP>";  
json.Reservations[0].Instances[0].RootDeviceName = "/dev/xvda";  
json.Reservations[0].Instances[0].RootDeviceType = "ebs";  
json.Reservations[0].Instances[0].SecurityGroups = [];...
```

# **通过命令行流水线输出 Munging gron】**

JSON 比 gron 输出更紧凑，适合用于传输和集成的数据结构。虽然更冗长，但 gron 输出是一种更有用的格式，可以通过 Linux 的文本操作和过滤工具，甚至是 [sed](https://www.gnu.org/software/sed/manual/sed.html) 和 [awk](https://www.gnu.org/software/gawk/manual/gawk.html) 进行文本搜索、过滤和操作。例如，考虑以下命令:

```
$ cat ~/ec2.json | gron | grep AvailabilityZonejson.Reservations[0].Instances[0].Placement.AvailabilityZone = “us-east-1a”;
```

上面的命令“pipeline”在 gronned JSON 中搜索文本“AvailabilityZone”值，并返回离散赋值行。

```
$ cat ~/ec2.json | gron | grep AvailabilityZone|cut -d\” -f2us-east-1a
```

上面的管道通过 Linux cut 命令提取 AvailabilityZone 值。

```
$ cat ~/ec2s.json | gron | grep InstanceId | cut -d\” -f2…
<ID_1>
<ID_2>
<ID_3>
…
```

上面的管道从 AWS EC2 CLI 输出中提取所有 EC2 实例 id，并创建一个 id 列表。

# **用 gron 和 ungron(又名 norg)转换 JSON**

前面，我引用了指向 ungron 命令的 norg 别名。使用这个命令，gron 将把 gron 离散赋值转换回 JSON。考虑下面的命令:

**注:**去掉了 cat，直接调用了 gron。

```
$ gron ~/ec2s.json | grep InstanceId | norg...
{
      "Instances": [
        {
          "InstanceId": "<ID>"
        }
      ]
    },
    {
      "Instances": [
        {
          "InstanceId": "<ID>"
        }
      ]
    },
...
```

上面的管道 grons JSON，greps for instance id 字段，然后转换离散赋值的行

```
(json.Reservations[999].Instances[0].InstanceId = “<ID>”;) 
```

从 grepped gron 输出返回到可用且简化的 JSON。

```
$ gron ~/ec2s.json | egrep InstanceId\|ImageId | norg...
    {
      "Instances": [
        {
          "ImageId": "<AMI_ID>",
          "InstanceId": "<ID>"
        }
      ]
    },
    {
      "Instances": [
        {
          "ImageId": "<AMI_ID>",
          "InstanceId": "<ID>"
        }
      ]
    },
...
```

上面的管道使用 egrep 将 ImageId 添加到转换后的 JSON 中(是的，我知道 GNU 已经弃用 egrep 来代替 grep -E)。

# **sed**

sed 是一个强大的流编辑器，可以方便地对文本文件执行查找/替换算法。

```
$ gron ~/ec2s.json | egrep InstanceId\|ImageId\|InstanceType | sed -e ‘s/Instances/node/g;s/ImageId/ami/g;s/InstanceType/type/g;s/InstanceId/id/g’ | norg...
{
      "node": [
        {
          "ami": "<AMI_ID>",
          "id": "<ID>",
          "type": "t2.small"
        }
      ]
    },
    {
      "node": [
        {
          "ami": "<AMI_ID>",
          "id": "<ID>",
          "type": "t2.micro"
        }
      ]
    },
...
```

上面的管道添加了 sed 流编辑，以执行多个内联字符串替换。

```
$ gron ~/ec2s.json | egrep InstanceId\|ImageId\|InstanceType | sed -e ‘s/Instances/node/g;s/ImageId/ami/g;s/InstanceType/type/g;s/InstanceId/id/g’ | norg | tr -d ‘\n’ | sed “s/ //g”...
{"node":[{"ami":"<AMI_ID>","id":"<ID>","type":"t2.small"}]},{"node":[{"ami":"<AMI_ID>","id":"<ID>","type":"t2.micro"}]},
...
```

上面的管道添加了 translate 命令 tr 来删除换行符，然后添加了另一个 sed 命令来删除剩余的空白。这对于最小化 JSON 文件很方便。

# **总结**

gron 将结构化 JSON 转换成离散赋值行。这种转换使流程能够将文本传输到 grep 和 sed 等本地工具，以执行强大的文本操作。一旦被操作，离散赋值可以通过 gron -u| — ungron 命令转换回 JSON。这使得 gron 成为 grep 和 sed 等现有工具的补充，用于管理 JSON 数据。

*声明:这些观点仅代表作者个人观点。除非本帖中另有说明，否则 Capital One 不属于所提及的任何公司，也不被其认可。使用或展示的所有商标和其他知识产权都是其各自所有者的所有权。本文为 Capital One 2018。*