# 将 Terraform 与 Oracle PaaS 服务管理器(PSM)结合使用

> 原文：<https://medium.com/oracledevs/using-terraform-with-oracle-paas-service-manager-psm-d21f2ddbae3f?source=collection_archive---------2----------------------->

虽然面向 Oracle 云平台的 [Terraform 提供商](https://www.terraform.io/docs/providers/oraclepaas/index.html)支持越来越多的资源用于在 Oracle 云上部署和供应 [Oracle PaaS](http://cloud.oracle.com/paas) 服务，但该提供商目前并不支持所有的 [PaaS](https://www.oracle.com/cloud/what-is-paas/) 服务。因此，如果您需要在 Terraform 配置中提供一项 Oracle 云平台提供商尚不支持的额外服务，该怎么办呢？一种方法是将 Terraform 与**Oracle PaaS Service Manager(PSM)CLI**工具结合使用。

## 将 Terraform 与 PSM 一起使用的限制

在深入了解如何将 PSM 与 Terraform 结合使用的细节之前，有必要先强调一下这种方法的一些局限性。

*   PSM CLI 必须安装在运行 terraform 的本地计算机上
*   使用 PSM 和 Terraform 创建服务仅处理创建/销毁用例。在 Terraform 中修改实例配置将导致实例被销毁和重新创建。
*   Terraform 不会检测或纠正所需状态和当前配置状态之间的任何差异。
*   不要同时运行针对不同环境的多个配置任务，因为它们可能会覆盖彼此的设置。

# 从 Terraform 调用 PSM

Terraform 将使用 [local-exec](https://www.terraform.io/docs/provisioners/local-exec.html) provisioner 调用 PSM，因此 PSM cli 工具必须安装在运行 Terraform 的本地主机上。有关安装说明，请参考 [PaaS 服务管理器文档](https://docs.oracle.com/en/cloud/paas/java-cloud/pscli/abouit-paas-service-manager-command-line-interface.html)。

```
$ psm --version
Oracle PaaS CLI client
Version 1.1.23
```

如果您完成了[配置命令行界面](https://docs.oracle.com/en/cloud/paas/java-cloud/pscli/configuring-command-line-interface.html)的步骤，您将会看到 PSM 工具针对一个目标环境配置了一组凭证，这些设置存储在`~/.psm/`下的本地用户帐户中。

为了确保 PSM 针对 Terraform 正在配置的正确环境，我们需要在每组 PSM 配置命令之前调用`psm setup`，传入目标环境的详细信息。我们可以使用`local_file`资源来创建所需的安装配置文件

```
resource "local_file" "psm_setup" {
  filename = "psm-setup-payload.json"
  content  = <<JSON
  {
      "username":"${var.psm_user}",
      "password":"${var.psm_password}",
      "identityDomain":"${var.psm_domain}"
  }
JSON
}data "local_file" "psm_setup" {
  depends_on = [ "local_file.psm_setup" ]
  filename = "${local_file.psm_setup.filename}"
}
```

使用`local-exec` provisioner 调用`psm setup`，将本地配置文件名传递给`--config-payload`选项。

```
resource ... { provisioner "local-exec" {
    command = "**psm setup --config-payload** ${**data.local_file.psm_setup.filename**}"
  }
  ...
}
```

任何后续的 PSM 调用现在都将针对已配置的环境。

# 使用 PSM 调配 PaaS 服务

PSM cli 通过将服务配置 JSON 有效负载传递给所需服务的`create-service`动作来提供新服务。PSM 文档中提供了每项服务的示例负载，通过从 CLI 运行 help 命令，例如

```
$ psm IntegrationCloud create-service help
```

为了从 Terraform 调用 PSM 和所需的服务创建有效负载，我们遵循上面使用的相同模式来执行 PSM 设置，即:

1.  使用`local_file`资源创建 JSON 有效负载，设置适当的属性值
2.  调用所需的 PSM cli 命令，引用配置文件

```
resource "local_file" "integration_service" {
  filename = "integration-cloud-${var.service_name}-create-service.json"
  content  = <<JSON
  {
    "vmPublicKeyText": "${file("~/.ssh/id_rsa.pub")}",
    "featureSet": "ics",
    "serviceName": "icsdemo1",
    "description": "created by Terraform and PSM",
    "cloudStorageContainer": "${var.backup_storage_container}",
    "cloudStorageUser": "${var.backup_storage_user}",
    "cloudStoragePassword": "${var.backup_storage_password}",
    "confirmation": true,
    "meteringFrequency": "HOURLY",
    "components": {
      "WLS": {
       "dbServiceName": "${var.database_service_name}",
       "dbaName": "${var.database_user}",
       "dbaPassword": "${var.database_password}",
       "managedServerCount": 2
      }
    }
  }
JSON
}data "local_file" "integration_service" {
  depends_on = [ "local_file.integration_service" ]
  filename = "${local_file.integration_service.filename}"
}resource null_resource "integration_service" { triggers {
    payload = "${md5(local_file.integration_service.content)}"
  } provisioner "local-exec" {
    command = "**psm setup --config-payload** ${**data.local_file.psm_setup.filename**}"
  }

  provisioner "local-exec" {
    command     = "**psm IntegrationCloud create-service** --wait-until-complete true **--config-payload** ${**data.local_file.integration_service.filename**}"  }}
```

注意`psm setup`命令在`create-service`之前执行，这是为了确保正确的环境设置应用于该特定部署。

在`triggers`块中使用了配置内容的 MD5 散列，因此对配置的任何更改都将触发资源的重新创建。

## 添加销毁时间置备程序

为了确保 PaaS 服务在`terraform destroy`上被销毁，或者当服务定义发生变化并且需要重新创建实例时，我们需要添加一个销毁时间供应器。同样，除了要执行的主 PSM 命令之外，我们还调用`psm setup`来确保以正确的环境为目标，因为 PSM 配置在最初应用后可能已经更改。

```
resource null_resource "integration_service" {

  ... provisioner "local-exec" {
    **when        = "destroy"**
    command     = "**psm setup --config-payload** ${**data.local_file.psm_setup.filename**}"
  } provisioner "local-exec" {
    **when        = "destroy"
**    command     = "**psm IntegrationCloud delete-service** --wait-until-complete true **--service-name ${var.service_name}** --dba-name ${var.database_user} --dba-password ${var.database_password} --skip-backup-on-terminate ${var.skip_backup_on_terminate} --force true"
  }
}
```

# 从 PaaS 服务实例获取属性

已配置实例的属性，无论是使用上述方法配置的还是已经配置的，都可以使用 PSM cli 作为`external`数据源读回到 Terraform 中

`[external](https://www.terraform.io/docs/providers/external/index.html)`数据源类型执行返回 JSON 数据结构的本地命令，并将结果放入 Terraform 状态。不幸的是，Terraform 并不完全支持从 PSM 命令返回的 JSON 样式，但是可以通过一个简单的包装器脚本提取所需的属性并返回一个简化的 JSON 响应来解决这个问题。

下面的`psm_get_jcs.sh`示例使用`jq`获取 Java 云服务实例响应并提取属性子集

```
#!/usr/bin/env bash
# usage psm_get_jcs.sh <service-name>RESP=$(psm jcs service -s $1 -of json)echo $RESP | jq "{ service_name: .serviceName, fmw_root: .FMW_ROOT, wls_root: .WLS_ROOT }"
```

以上只是一个有限的例子，根据您的具体需求添加附加属性。要测试包装器脚本的输出:

```
$ ./psm_get_jcs.sh jcsdemo1
**{
"service_name":** "jcsdemo1"**,
"fmw_root":** "https://129.144.208.50:7002/em"**,
"wls_root":** "https://129.144.208.50:7002/console"
**}**
```

当从数据源`external`调用包装器脚本时，从脚本返回的 JSON 属性在数据源`result`属性下可用。例如，获取 Java 云服务实例 WLS 服务实例的 IP 地址:

```
data "external" "jcs" {
  program = ["./psm_get_jcs.sh", "${var.service-name}"]
}output "wls_ip" {
  value = "${replace(element(split(":",data.external.jcs.**result.wls_root**),1),"////","")}"
}
```