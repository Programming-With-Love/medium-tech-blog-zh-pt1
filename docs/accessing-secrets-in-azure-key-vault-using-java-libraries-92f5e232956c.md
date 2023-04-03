# 使用 Java 库访问 Azure Key Vault 中的机密

> 原文：<https://medium.com/version-1/accessing-secrets-in-azure-key-vault-using-java-libraries-92f5e232956c?source=collection_archive---------3----------------------->

![](img/5be3be52b19c2f8854d4cdb604f2af37.png)

用于管理数字认证证书的工具和方法被称为秘密管理。这包括在应用程序、服务和特权帐户中使用的密码、密钥、API 和令牌。术语“机密”和“机密管理”在 IT 中更常用于开发运维环境、工具和流程。*每个应用程序、脚本、自动化工具和其他非人类身份都依赖某种形式的特权凭证来访问其他工具、应用程序和数据*。*对这些秘密的不当管理会使您的应用程序暴露在类似于糟糕的编程实践或错误所导致的漏洞之下。*

## 什么是秘密？

秘密是数字认证凭证。机密是单独命名的敏感信息集，涉及广泛的安全数据。机密基本上是您想要限制和完全控制访问的任何数据。秘密不一定与数据有关，而是与访问数据有关。

一些最常见的秘密类型包括:

1.  特权帐户凭据
2.  密码
3.  证书
4.  嘘
5.  API 键
6.  加密密钥

很容易忘记在许多环境中使用的许多类型的秘密，或者在整个企业中一致地应用它们。这就是秘密管理的由来。

## 什么是秘密管理？

机密管理用于安全地存储、传输和审计机密。它是一套为您的敏感信息提供保密性的工具和技术。它确保资源只能由经过身份验证和授权的实体访问。机密管理部门必须考虑并降低这些机密的风险，包括传输中的和静态的，因为它们必须安全传输。

## 使用 Azure 密钥库进行秘密管理

Azure Key Vault 是一种云服务，为密码和数据库连接字符串等机密提供安全存储。Azure Key Vault Secrets 客户端库允许您安全地存储并严格控制对令牌、密码、API 密钥和其他机密的访问。该库提供创建、检索、更新、删除、清除、备份、恢复和列出机密及其版本的操作。

## 先决条件

1.  Azure 订阅。
2.  Java 开发工具包(JDK)版本 8 或更高版本。
3.  阿帕奇 Maven。
4.  Azure CLI。

## 必需的 Maven 依赖项

在 POM.xml 文件的 dependency 部分下添加以下依赖项。

```
 <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-security-keyvault-secrets</artifactId>
        <version>4.2.3</version>
  </dependency>
  <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-identity</artifactId>
        <version>1.7.0</version>
  </dependency>
  <dependency>
        <groupId>com.fasterxml.jackson.core</groupId>
        <artifactId>jackson-core</artifactId>
        <version>2.14.0</version>
  </dependency>
```

## 设置环境变量

为密钥库名称设置环境变量，如下所示。打开命令提示符并执行以下命令

```
set KEY_VAULT_NAME=<your-key-vault-name>
set AZURE_CLIENT_ID=<your-azure-client-id>
set AZURE_CLIENT_SECRET=<your-azure-client-secret>
set AZURE_TENANT_ID=<your-azure-tenant-id>
```

## 资格证书

凭证是一个包含或可以获取服务客户端验证请求所需数据的类。Azure SDK 上的服务客户端在构造凭证时接受凭证。服务客户端使用这些凭证对服务请求进行身份验证。

验证 Azure 托管的应用程序。

1.  DefaultAzureCredential:提供简化的身份验证体验，快速开始开发在 Azure 中运行的应用程序。
2.  ChainedTokenCredential:允许用户定义由多个凭据组成的自定义身份验证流。
3.  EnvironmentCredential:通过环境变量中指定的凭据信息对服务主体或用户进行身份验证。
4.  ManagedIdentityCredential:验证 Azure 资源的托管身份。

“DefaultAzureCredential”适用于应用程序最终要在 Azure 中运行的大多数场景。这是因为“DefaultAzureCredential”将通常用于在部署时进行身份验证的凭据与用于在开发环境中进行身份验证的凭据相结合。

## 秘密管理课。

秘密管理 java 类有四种不同的方法与 azure 密钥库交互。

1.  《秘密建造者》。
2.  “设定秘密”。
3.  “获取秘密”。
4.  “删除机密”。

## 秘密建造者方法。

Secret Builder 方法用于验证与 Azure 密钥库的连接。它接受一个参数密钥库名称。

1.  该方法使用密钥库名称生成密钥库 URL。
2.  “secretClientBuilder”类用于向 azure 密钥库进行身份验证。
3.  “secretclientbuilder”类中存在的三种方法，如“vaultUrl”、“credential”和“buildcontent”方法，用于向 azure 密钥库进行身份验证。
4.  DefaultAzureCredentialBuilder 类用于对 azure 密钥库进行身份验证。
5.  DefaultAzureCredentialBuilder 类使用 AZURE 门户中为 AZURE 密钥库配置的 AZURE_CLIENT_ID、AZURE_CLIENT_SECRET 和 AZURE_TENANT_ID。
6.  AZURE_CLIENT_ID、AZURE_CLIENT_SECRET 和 AZURE_TENANT_ID 的值是从系统环境变量中获取的，以便成功进行身份验证。

## 设置秘密方法。

“setSecret”方法在 azure 密钥库中创建秘密。它接受参数密钥库名称、秘密名称和秘密值。

1.  该方法使用诸如密钥库名称、秘密名称和秘密值之类的参数来创建秘密。
2.  “setSecret”方法使用“keyVaultSecret”类的实例在 azure 密钥库中创建秘密。

## 获取秘法。

“getSecret”方法用于获取 azure key vault 中的秘密。它接受参数密钥库名称和机密名称。

1.  该方法使用参数(如密钥库名称和机密名称)获取机密。
2.  该方法获取“KeyVaultSecret”类型的机密。
3.  使用“retrievedSecret.getValue()”将“KeyVaultSecret”类型的机密转换为字符串。
4.  方法以字符串格式返回机密。

## 删除秘密方法。

“deleteSecret”方法用于删除 azure 密钥库中存在的秘密。它接受参数密钥库名称和机密名称。

1.  该方法使用参数(如密钥库名称和机密名称)删除机密。
2.  该方法使用“beginDeleteSecret”来删除秘密。
3.  “SyncPoller”用于等待删除过程的完成。
4.  “deletionpoller . waitforcompletion()”用于实现这一点。

## 结论

就是这样，这些是使用 Java 库从 Azure Key Vault 获取秘密所需要执行的步骤。

作者简介:
Rajeev Kalal 是 Version 1 的测试自动化顾问。