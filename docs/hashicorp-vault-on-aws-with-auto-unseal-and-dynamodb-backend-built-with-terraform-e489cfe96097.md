# AWS 上的 Hashicorp Vault 具有自动解封功能，DynamoDB 后端由 Terraform 构建

> 原文：<https://medium.com/version-1/hashicorp-vault-on-aws-with-auto-unseal-and-dynamodb-backend-built-with-terraform-e489cfe96097?source=collection_archive---------0----------------------->

# 关于我自己

我是为[版本 1](https://www.version1.com/) 工作的 DevOps 工程师。我在 2008 年以 Linux 管理员和 Oracle Applications DBA 的身份加入了版本 1。

第 1 版让所有员工都可以选择跳槽到其他内部角色，2019 年，我决定尝试进入第 1 版数字和云团队，担任 DevOps 工程师。

为此，我开始学习一些主要的 DevOps 工具，如 Terraform 和 Docker。我还获得了 AWS 认证解决方案架构师认证。

因此，2020 年 1 月，我从 Linux 和 Oracle 转到了 DevOps，并且从未回头。

# 目的

本文档的目的是展示如何在[亚马逊 Web 服务](https://aws.amazon.com/)中运行单节点 [Hashicorp Vault](https://www.hashicorp.com/products/vault) 实例，使用 [AWS DynamoDB](https://aws.amazon.com/dynamodb/) 后端并使用 [AWS 密钥管理服务](https://aws.amazon.com/kms/)在安装或重启时自动解封 Vault。

# 为什么

当我需要在 AWS 中启用自动解封来实现 Vault 时，我花了一些时间来解决这个问题。我能找到的信息并不多。所以现在我有它的工作，为什么不分享它，希望它可以帮助一些人在 AWS 上安装他们的 Vault。

# 我们将涵盖哪些内容

这将涵盖 AWS 上带有 DynamoDB 后端的单节点 Vault 实例。在生产中，很可能会有一个使用 AWS 自动扩展组和 AWS 负载平衡器的 Vault 集群，但这将使测试环境中的 Vault 运行变得更加复杂。

因此，本指南将涵盖

1.  创建 EC2 实例以运行 vault。
2.  写入 EC2 用户数据以安装和配置 vault。
3.  为保管库存储设置 dynamodb 后端。
4.  演示从 Terraform 工作站、vault EC2 实例和 Vault web 控制台登录到 Vault。

# 地形版本

该代码已经在 Terraform 版本 0.13、0.14、0.15 和 1.0 中测试过，对于本指南，我将只使用 Terraform 1.0。

# 我们开始吧

我假设您已经安装了 Terraform，并使用 Linux 服务器进行开发。

这里下载源代码，然后我们将通过它是如何工作的。

创建一个目录，并从 GitHub—[https://github.com/nicklunt/vault.git](https://github.com/nicklunt/vault.git)下载这个库构建的源代码

将目录更改为新的保管库目录。这里创建一个 SSH 密钥，AWS 将使用它来允许对 AWS 实例的 SSH 访问。这是我使用的方法

```
ssh-keygen -y -f ~/.ssh/id_rsa > public_key
```

这个 public_key 将在 main.tf 中被引用。

# 创建实例— main.tf

创建 AWS 密钥对，以便在 Vault 实例联机后允许我们访问它。

这使用了我们刚刚创建的 public_key。

```
resource “aws_key_pair” “ssh-keypair” {
 key_name = “ssh-keypair”
 public_key = file(var.ssh-key)
}
```

main.tf 文件也将创建实例，这里没有什么特别的，但是 user_data 是我们很快就会感兴趣的。

```
*resource “aws_instance” “vault” {
 ami = var.ami 
 key_name = aws_key_pair.ssh-keypair.id
 instance_type = var.aws_instance_type
 subnet_id = aws_subnet.public-subnet.id 
 vpc_security_group_ids = [aws_security_group.sg-vault.id]
 associate_public_ip_address = true
 iam_instance_profile = aws_iam_instance_profile.vault-kms-unseal.name
 user_data = data.template_file.userdata.rendered
 root_block_device {
 volume_size = var.root_volume_size
}**tags = {
 Name = “Vault Server”
}**depends_on = [aws_kms_key.vault-unseal-key,     aws_dynamodb_table.vault-table]
}*
```

main.tf 中的 depends_on 语句告诉 Terraform 在实例之前创建解封密钥和 DynamoDB 表，我们指定这一点是为了确保在安装 Vault 之前 DynamoDB 表和密钥都已准备好，否则，Vault 安装将会失败，因为它需要 DynamoDB 来存储数据，并且需要 KMS 密钥来解封自身。

aws_instance 资源也引用 main.tf 中的 aws_key_pair，因此我们可以 SSH 到实例。

在进入 user_data 和 iam_instance_profile 之前，让我们在线获取 DynamoDB 表。

注意 Vault 的 DynamoDB 插件来自社区，没有得到 Hashicorp 的官方支持。

# DynamoDB 后端— dynamodb.tf

```
resource “aws_dynamodb_table” “vault-table” {
 name = var.dynamodb-table
 read_capacity = var.dynamo-read-write
 write_capacity = var.dynamo-read-write
 hash_key = “Path”
 range_key = “Key”
 attribute {
 name = “Path”
 type = “S”
 }attribute {
 name = “Key”
 type = “S”
}tags = {
 Name = “vault-table”
 }
}
```

这就像为 Vault 创建一个 DynamoDB 表一样简单，这也是 Vault 使用 DynamoDB 作为后端所需要的。

hash _ key、range_key 和属性都是 vault 本身需要的。

# 开启保险库的 KMS 密钥— kms.tf

只需创建一个密钥

```
*resource “aws_kms_key” “vault-unseal-key” {
 description = “KMS Key to unseal Vault”
 deletion_window_in_days = 7
}*
```

# 存储库根令牌和恢复密钥-secrets-manager . TF

当 Vault 首次启动时，它会初始化并发出根令牌和解封密钥。
我们将希望保持这些安全可靠，所以我们把它们放入 AWS 秘密管理器。
此处的 recovery_window_in_days 设置为零，因此我们可以根据测试需要随时重新创建密码。

```
resource “aws_secretsmanager_secret” “vault-root-token” {
 name = var.vault-root-token
 description = “Vault root token”
 # recovery set to 0 so we can recreate the secret as required for testing.
 recovery_window_in_days = 0 tags = {
 Name = var.vault-root-token
 }
}resource “aws_secretsmanager_secret” “vault-recovery-key” {
 name = var.vault-recovery-key
 description = “Vault recovery key”
 # recovery set to 0 so we can recreate the secret as required for testing.
 recovery_window_in_days = 0

tags = {
 Name = var.vault-recovery-key
 }
}
```

# 用户数据-template/vault . sh . TPL

实例 user_data 是使 Vault 正常工作的基础，并作为 data.tf 中的 Terraform template_file 引用，以填充＄{ variable } s。

下面描述了实例 user_data 中所需的一切。

```
# Pull Vault from Hashicorp releases and unzip it ready for configurationwget [https://releases.hashicorp.com/vault/1.7.3/vault_1.7.3_linux_amd64.zip](https://releases.hashicorp.com/vault/1.7.3/vault_1.7.3_linux_amd64.zip) -O /tmp/vault.zipunzip /tmp/vault.zip -d /usr/bin/ && rm -f /tmp/vault.zipwget [https://github.com/stedolan/jq/releases/download/jq-1.6/jq-linux64](https://github.com/stedolan/jq/releases/download/jq-1.6/jq-linux64)
chmod +x jq-linux64
mv jq-linux64 /usr/bin/jq# Create a vault user and group
groupadd --force --system vault
if ! getent passwd vault >/dev/null; then
 adduser --system --gid vault --no-create-home --comment “vault owner” --shell /bin/false vault >/dev/null
fi# A default profile for local vault addresscat << EOF > /etc/profile.d/vault.sh
export VAULT_ADDR=http://127.0.0.1:8200
EOF# Enable systemd on Amazon Linux for Vaultcat > /usr/lib/systemd/system/vault.service <<-EOF
[Unit]
Description=Vault Service
Requires=network-online.target
After=network-online.target
[Service]
Restart=on-failure
PermissionsStartOnly=true
ExecStartPre=/sbin/setcap ‘cap_ipc_lock=+ep’ /bin/vault
ExecStart=/bin/vault server -config /etc/vault/vault.conf
ExecReload=/bin/kill -HUP $MAINPID
KillSignal=SIGTERM
User=vault
Group=vault
[Install]
WantedBy=multi-user.target
EOF# Reload systemd and enable vaultsystemctl daemon-reload
systemctl enable vault# Vault requires a config file, so here we create that config file, set ownership and permissions then start the Vault service.mkdir /etc/vault
cat > /etc/vault/vault.conf <<-EOF
listener "tcp" {
  address = "0.0.0.0:8200"
  tls_disable = 1
}
ui = true
storage "dynamodb" {
  region = "${region}"
  table = "${dynamodb-table}"
  read_capacity = 3
  write_capacity = 3
}
seal "awskms" {
  region = "${region}"
  kms_key_id = "${unseal-key}"
}
EOFchown -R vault:vault /etc/vault
chmod -R 0644 /etc/vault/*
mkdir /var/log/vault
chown vault:vault /var/log/vault
systemctl start vault
```

配置文件将我们创建的 DynamoDB 表指定为后端，并将我们创建的 AWS KMS 键指定为解封 Vault。

这将检查 Vault 是否已经初始化，如果是，它将简单地检查实例是否可以使用其实例配置文件登录，然后用户数据脚本退出。

否则，如果保险库未初始化，我们将对其进行初始化，并将根令牌和恢复密钥保存到 AWS Secrets Manager。

继续处理用户数据。

```
export VAULT_ADDR=http://127.0.0.1:8200# Check if vault is already initialisedINITIALIZED=$(curl $VAULT_ADDR/v1/sys/init | jq '.initialized')
if [ "$${INITIALIZED}" != "true" ]; then
  echo "[] Vault DB not initialised, initialising now"
  ## Initialise vault and save token and unseal key
  vault operator init -recovery-shares=1 -recovery-threshold=1 2>&1 | tee ~/vault-init-out.txt
  echo “[] Vault status output”
  vault status | tee -a ~/vault-status.txt
  # Get the VAULT_TOKEN so we can interact with vault
  export VAULT_TOKEN=$(grep '^Initial Root Token:' ~/vault-init-out.txt | awk '{print $NF}') # Get the unseal key
  export RECOVERY_KEY=$(grep '^Recovery Key' ~/vault-init-out.txt | awk '{print $NF}')
  # Save the root token and recovery key to aws secrets manager, then we can delete ~/vault-init-out.txt # The secret resources have already been created by terraform (secrets-manager.tf) aws secretsmanager update-secret — secret-id ${secret_token_id} — secret-string "$${VAULT_TOKEN}" — region ${region} aws secretsmanager update-secret — secret-id ${secret_recovery_id} --secret-string "$${RECOVERY_KEY}" — region ${region} # Remove the temp file which has the root token details
  rm -f ~/vault-init-out.txt
else
  # Vault already initialised, which means the db is up which has our role, so login with that role, then exit this script. echo "[] Vault DB already initialised. Check we can login with aws method and exit"
  vault login -method=aws role=admin
  echo “[] Userdata finished.”
  exit
fi
```

如果保险库尚未初始化，上述 If 语句不会退出，用户数据将继续。
它启用了 Vault 审计文件，启用了 aws auth 和 kv secrets 引擎。
然后，它提取我们上传到 S3 的管理策略，并将该策略写入 Vault，将该策略分配给 Vault 实例角色。

继续处理用户数据。

```
# Enable vault logging
vault audit enable file file_path=/var/log/vault/vault.log# Enable the vault AWS and kv engine
vault auth enable awsvault secrets enable -path=secret -version=2 kv# Get the vault-admin-policy.hcl file that was uploaded to S3 in s3.tf
aws s3 cp s3://${vault_bucket}/vault-admin-policy.hcl /var/tmp/# Create the admin policy in vault
vault policy write "admin-policy" /var/tmp/vault-admin-policy.hcl# Give this instance admin privileges to vault, tied to this instances vault_instance_role.vault write \
auth/aws/role/admin \
auth_type=iam \
policies=admin-policy \
max_ttl=1h \
bound_iam_principal_arn=${vault_instance_role}echo "[] Userdata finished."
```

这是 Vault complete 的 user_data。在 data.tf 中查找传递到文件中的${variables}。

# 用户数据模板文件— data.tf

```
data "template_file" "userdata" {
 template = file("${path.module}/templates/vault.sh.tpl")
 vars = {
   region = var.region
   dynamodb-table = var.dynamodb-table
   unseal-key = aws_kms_key.vault-unseal-key.id
   instance-role = aws_iam_role.vault-kms-unseal.name
   vault_instance_role =  "arn:aws:iam:${var.region}:${var.account_id}:role/${aws_iam_role.vault-kms-unseal.name}"
   vault_bucket = var.bucket_name
   secret_token_id = aws_secretsmanager_secret.vault-root-token.id
   secret_recovery_id = aws_secretsmanager_secret.vault-recovery-key.id
 }
}
```

# 实例配置文件—实例配置文件. tf

正如在 Vault 的用户数据中看到的，实例本身需要访问 S3、秘密管理器、KMS 和 DynamoDB。我们创建一个 IAM 角色来满足这些要求，然后将其分配给 main.tf 中的 Vault iam_instance_profile。

```
data “aws_iam_policy_document” “assume_role” {
  statement {
    effect = “Allow”
    actions = [“sts:AssumeRole”]
    principals {
    type = “Service”
    identifiers = [“ec2.amazonaws.com”]
    }
  }
}data “aws_iam_policy_document” “vault-kms-unseal” {
  statement {
    sid = “VaultKMSUnseal”
    effect = “Allow”
    resources = [“*”]
    actions = [
      “kms:Encrypt”,
      “kms:Decrypt”,
      “kms:DescribeKey”
    ]
  }
  statement {
    sid = “VaultDynamoDB”
    effect = “Allow”
    resources = [aws_dynamodb_table.vault-table.arn]
    actions = [
      “dynamodb:DescribeLimits”,
      “dynamodb:DescribeTimeToLive”,
      “dynamodb:ListTagsOfResource”,
      “dynamodb:DescribeReservedCapacityOfferings”,
      “dynamodb:DescribeReservedCapacity”,
      “dynamodb:ListTables”,
      “dynamodb:BatchGetItem”,
      “dynamodb:BatchWriteItem”,
      “dynamodb:CreateTable”,
      “dynamodb:DeleteItem”,
      “dynamodb:GetItem”,
      “dynamodb:GetRecords”,
      “dynamodb:PutItem”,
      “dynamodb:Query”,
      “dynamodb:UpdateItem”,
      “dynamodb:Scan”,
      “dynamodb:DescribeTable”
    ]
  }
  statement {
    sid = “IAM”
    effect = “Allow”
    resources = [“*”]
    actions = [
      “iam:GetInstanceProfile”,
      “iam:GetRole”,
      “iam:CreateAccessKey”,
      “iam:DeleteAccessKey”,
      “iam:GetAccessKeyLastUsed”,
      “iam:GetUser”,
      “iam:ListAccessKeys”,
      “iam:UpdateAccessKey”,
      “sts:AssumeRole”
    ]
  }
  statement {
    sid = “S3”
    effect = “Allow”
    resources = [“arn:aws:s3:::${var.bucket_name}/*”]
    actions = [
      “s3:GetObject”
    ]
  }
  statement {
    sid = “SecretsManager”
    effect = “Allow”
    resources = [
      aws_secretsmanager_secret.vault-root-token.id,         aws_secretsmanager_secret.vault-recovery-key.id
    ]
    actions = [
      “secretsmanager:UpdateSecret”,
      “secretsmanager:GetSecretValue”
    ]
  }
}resource “aws_iam_role” “vault-kms-unseal” {
  name = var.instance-role
  assume_role_policy = data.aws_iam_policy_document.assume_role.json
}resource “aws_iam_role_policy” “vault-kms-unseal” {
  name = var.instance-role-policy
  role = aws_iam_role.vault-kms-unseal.id
  policy = data.aws_iam_policy_document.vault-kms-unseal.json
}resource “aws_iam_instance_profile” “vault-kms-unseal” {
  name = var.instance-profile
  role = aws_iam_role.vault-kms-unseal.name
}
```

# 未包括在内

我没有在这里包括 VPC 设置，安全组，互联网网关和变量。tf，因为这篇文章是关于与地形的 Vault。

要获得完整代码，请从[https://github.com/nicklunt/vault.git](https://github.com/nicklunt/vault.git)下载，并更改 variables.tf 中的以下变量以适应您的环境。

```
# Change region for your env
variable “region” {
  type = string
  default = “eu-west-2”
}# Change this to your aws account
variable “account_id” {
  description = “AWS Account ID”
  type = string
  default = “01234567890”
}# Change this to your external IP
variable “my_ip” {
  description = “my external IP address”
  type = string
  default = “aa.bb.cc.dd/32”
}# May need to change the AMI if not in eu-west-2
variable “ami” {
  description = “eu-west-2 Amazon Linux 2 AMI”
  type = string
  default = “ami-0a669382ea0feb73a”
}# I’m using a small instance type for testing
variable “aws_instance_type” {
  description = “aws instance type”
  type = string
  default = “t2.micro”
}# Size of the OS volume
variable “root_volume_size” {
  description = “size of the os volume in GB”
  type = string
  default = “50”
}# May need to change the bucket to something unique
variable “bucket_name” {
  description = “Bucket to upload any required files”
  type = string
  default = “vault-conf-bucket-01010101”
}# If running terraform from linux, generate the ssh-key with
# $ cd <vault terraform directory>
# $ ssh-keygen -y -f ~/.ssh/id_rsa > public_key
variable “ssh-key” {
  type = string
  description = “File holding my public ssh key: ssh-keygen -y -f ~/.ssh/id_rsa > public_key”
  default = “public-key”
}
```

# 运行地形

在运行 Terraform 之前，请记住在与 Terraform 文件相同的目录中创建密钥，以便在构建实例后可以通过 SSH 连接到实例。

```
$ ssh-keygen -y -f ~/.ssh/id_rsa > public_key
$ terraform plan*Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:
+ create
<= read (data resources)
Plan: 18 to add, 0 to change, 0 to destroy.*
```

运行 terraform apply 以创建资源

```
$ terraform apply -auto-approve*Apply complete! Resources: 18 added, 0 changed, 0 destroyed.**Outputs:
Authenticate_to_vault = “vault login -method=aws role=admin”
Connect_to_vault_UI = “http://123.123.123.123:8200"
Connect_to_vault_instance = “ssh* [*ec2-user@*](mailto:ec2-user@35.177.126.83)*123.123.123.123”*
```

所以现在我们在 AWS 中运行 Vault，使用 DynamoDB 作为后端，并且自动解封。

让我们试着连接到它。

# 连接到保管库

Terraform 输出(outputs.tf)向我们展示了如何连接。

从我们的开发站测试跳马

```
$ export VAULT_ADDR=http://*123.123.123.123*:8200
$ vault statusKey Value
— — — -
Recovery Seal Type shamir
Initialized true
Sealed false
Total Recovery Shares 1
Threshold 1
Version 1.7.3
Cluster Name vault-cluster-3a7ac91b
Cluster ID 12f3f95e-4be3–75bb-735e-f4a485317b1d
HA Enabled false
```

成功，我们可以看到保险库被初始化和解封。

测试我们可以从我们的开发站进入[http://](http://35.177.126.83:8200)*123.123.123.123*[:8200](http://35.177.126.83:8200)的金库 UI

如果您进入 AWS Secrets Manager 并搜索“vault-root-token”机密，您可以在此处使用它登录 vault。

最后，让我们通过 SSH 访问 Vault 实例，并尝试使用实例概要文件登录 Vault

```
$ ssh ec2-user@*123.123.123.123* [ec2-user ~]$ vault login -method=aws role=adminSuccess! You are now authenticated. The token information displayed below is already stored in the token helper. You do NOT need to run “vault login” again. Future Vault requests will automatically use this token.Key Value
— — — -
token s.BqtXOSb59vbBi9qyn52zEwRt
token_accessor UY7sX672lOgZaxdZqYpJTKLO
token_duration 1h
token_renewable true
token_policies [“admin-policy” “default”]
identity_policies []
policies [“admin-policy” “default”]
token_meta_account_id 01234567890
token_meta_auth_type iam
token_meta_role_id ae364a87–95t7–84n7–5835–73h74b875
```

更加成功！这证明 Vault 实例本身可以根据实例配置文件登录 Vault。

# 最后

希望这已经展示了如何在使用 Terraform 部署 vault 时自动解封 Vault，虽然本指南中有相当多的代码，但实际的解封只是 Vault 配置文件中引用 KMS 密钥来解封自身的一节。

在制作过程中，我们会将跳马聚集在一个 ASG 中，前面是一个 ELB。当然，让它运行 HTTPS 而不是 HTTP。

如果你有兴趣测试这一切，完整的代码可以在 GitHub、[https://github.com/nicklunt/vault.git](https://github.com/nicklunt/vault.git)获得，其中包括 VPC、安全组等。只需更改 variables.tf 顶部注释良好的变量即可。

谢谢你读到这里，祝你好运。

![](img/ae690eb6ceb40ff42b66ca9f4d81a16e.png)

**关于作者**
尼克·伦克是 Version 1 的 DevOps 工程师。