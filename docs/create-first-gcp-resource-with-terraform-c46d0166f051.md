# 用地形创建第一个 GCP 资源

> 原文：<https://medium.com/google-developer-experts/create-first-gcp-resource-with-terraform-c46d0166f051?source=collection_archive---------2----------------------->

让我们在本帖中使用 Terraform 创建我们的第一个 GCP 资源。

![](img/8ac74015446bb0ad6939056626d53c19.png)

Photo by [Markus Spiske](https://unsplash.com/@markusspiske) on [Unsplash](https://unsplash.com/)

# 目标

使用 Terraform 创建一个 Google 云存储(GCS)桶。

# 先决条件

本帖假设如下:

1.  我们已经有一个 GCP 项目和一个 GCS 桶(我们将使用它来存储 Terraform 状态文件)创建。
2.  2.Google Cloud SDK ( `gcloud`)和`Terraform`安装在您的工作站上。如果没有，那么参考我之前的博客-[Terraform 入门](https://pbhadani.com/posts/getting-started-with-terraform/)和[Google Cloud SDK 入门](https://pbhadani.com/posts/getting-started-wth-google-cloud-sdk/)。

# 用 Terraform 创建一个 Google 云存储(GCS)桶

**步骤 1:** 为 Terraform 项目创建一个 unix 目录。

```
mkdir ~/terraform-gcs-example
cd ~/terraform-gcs-example
```

**步骤 2:** 创建定义 GCS 存储桶和提供商的 Terraform 配置文件。

```
vi bucket.tf
```

该文件包含以下内容

```
# Specify the GCP Provider
provider "google" {
  project = var.project_id
  region  = var.region
}

# Create a GCS Bucket
resource "google_storage_bucket" "my_bucket" {
  name     = var.bucket_name
  location = var.region
}
```

**第三步:**定义 Terraform 变量。

```
vi variables.tf
```

该文件看起来像:

```
variable "project_id" {
  description = "Google Project ID."
  type        = string
}

variable "bucket_name" {
  description = "GCS Bucket name. Value should be unique ."
  type        = string
}

variable "region" {
  description = "Google Cloud region"
  type        = string
  default     = "europe-west2"
}
```

**注意:**我们正在为`region`定义一个`default`值。这意味着如果没有为此变量提供值，Terraform 将使用`europe-west2`作为其值。

**第四步:**创建一个`tfvars`文件。

`vi terraform.tfvars`

```
project_id  = ""                  # Put your GCP Project ID.
bucket_name = "my-bucket-48693"   # Put the desired GCS Bucket name.
```

**第五步:**设置远程状态。

`vi backend.tf`

```
terraform {
  backend "gcs" {
    bucket = "my-tfstate-bucket"   # GCS bucket name to store terraform tfstate prefix = "first-app"           # Update to desired prefix name. Prefix name should be unique for each Terraform project having same remote state bucket.
    }
}
```

**第六步:**文件结构如下

```
cntekio:~ terraform-gcs-example$ tree
.
├── backend.tf
├── bucket.tf
├── terraform.tfvars
└── variables.tf

0 directories, 4 files
```

**第七步:**现在，初始化 terraform 项目。

`terraform init`

**输出**

```
Initializing the backend...

Successfully configured the backend "gcs"! Terraform will automatically
use this backend unless the backend configuration changes.

Initializing provider plugins...
- Checking for available provider plugins...
- Downloading plugin for provider "google" (hashicorp/google) 3.3.0...

The following providers do not have any version constraints in configuration,
so the latest version was installed.

To prevent automatic upgrades to new major versions that may contain breaking
changes, it is recommended to add version = "..." constraints to the
corresponding provider blocks in configuration, with the constraint strings
suggested below.

* provider.google: version = "~> 3.3"

Terraform has been successfully initialized!

You may now begin working with Terraform. Try running "terraform plan" to see
any changes that are required for your infrastructure. All Terraform commands
should now work.

If you ever set or change modules or backend configuration for Terraform,
rerun this command to reinitialize your working directory. If you forget, other
commands will detect it and remind you to do so if necessary.
```

**注意:** `terraform init`下载所有需要的提供者和插件。

**第八步:**我们来看看执行计划。

`terraform plan --out 1.plan`

**输出**

```
Refreshing Terraform state in-memory prior to plan...
The refreshed state will be used to calculate this plan, but will not be
persisted to local or remote state storage.
------------------------------------------------------------------------

An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # google_storage_bucket.my_bucket will be created
  + resource "google_storage_bucket" "my_bucket" {
      + bucket_policy_only = (known after apply)
      + force_destroy      = false
      + id                 = (known after apply)
      + location           = "EUROPE-WEST2"
      + name               = "my-bucket-48693"
      + project            = (known after apply)
      + self_link          = (known after apply)
      + storage_class      = "STANDARD"
      + url                = (known after apply)
    }

Plan: 1 to add, 0 to change, 0 to destroy.

------------------------------------------------------------------------

This plan was saved to: 1.plan

To perform exactly these actions, run the following command to apply:
    terraform apply "1.plan"
```

**注意:** `terraform plan`输出显示这段代码将创建一个 GCS bucket。`--out 1.plan` flag 告诉 terraform 将计划保存在文件中。

**第 9 步:**执行计划看起来不错，那么我们继续前进，应用这个计划。

`terraform apply 1.plan`

**输出**

```
google_storage_bucket.my_bucket: Creating...
google_storage_bucket.my_bucket: Creation complete after 3s [id=my-bucket-48693]

Apply complete! Resources: 1 added, 0 changed, 0 destroyed.
```

**第 10 步:**一旦我们不再需要这个基础设施，我们就可以清理以降低成本。

`terraform destroy`

**输出**

```
google_storage_bucket.my_bucket: Refreshing state... [id=my-bucket-48693]

An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
  - destroy

Terraform will perform the following actions:

  # google_storage_bucket.my_bucket will be destroyed
  - resource "google_storage_bucket" "my_bucket" {
      - bucket_policy_only = false -> null
      - force_destroy      = false -> null
      - id                 = "my-bucket-48693" -> null
      - labels             = {} -> null
      - location           = "EUROPE-WEST2" -> null
      - name               = "my-bucket-48693" -> null
      - project            = "my-project-id" -> null
      - requester_pays     = false -> null
      - self_link          = "https://www.googleapis.com/storage/v1/b/my-bucket-48693" -> null
      - storage_class      = "STANDARD" -> null
      - url                = "gs://my-bucket-48693" -> null
    }

Plan: 0 to add, 0 to change, 1 to destroy.

Do you really want to destroy all resources?
  Terraform will destroy all your managed infrastructure, as shown above.
  There is no undo. Only 'yes' will be accepted to confirm.

  Enter a value: yes

google_storage_bucket.my_bucket: Destroying... [id=my-bucket-48693]
google_storage_bucket.my_bucket: Destruction complete after 6s

Destroy complete! Resources: 1 destroyed.
```

*希望这篇博客能帮助你开始使用 Terraform 创建 GCP 资源*

如果您对 Terraform 项目有反馈、问题或需要帮助，请通过 [LinkedIn](https://linkedin.com/in/pradeepbhadani) 或 [Twitter](https://twitter.com/bhadanipradeep) 联系我

*最初发表于*[*https://pbhadani.com*](https://pbhadani.com/posts/first-gcp-resource-with-terraform/)