# 在 Strapi v4 中将存储目标从本地磁盘更改为 Google 云存储

> 原文：<https://medium.easyread.co/changes-storage-destination-from-local-disk-into-google-cloud-storage-in-strapi-v4-bad6e41e14a0?source=collection_archive---------0----------------------->

## 在生产模式下，您还可以在 Strapi v4 中集成 Google 云存储

![](img/4d33407edf0c1d6baf8e1514891f1e87.png)

Photo by [Markus Winkler](https://unsplash.com/@markuswinkler?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

大家好，我是潘杜，我是一名软件工程师。欢迎来到我的博客，快乐阅读！

今天我就和大家分享一下如何在 Heroku 中解决连接 Google 云存储时上传文件的问题。

当我第一次在 Heroku 上部署时(没有带文件上传功能——read:media on Google Cloud Storage ),当所有功能都运行良好时，这对我来说是个好结果。我可以在生产模式下做任何事情，直到我发现无法查看和上传媒体或查看图片的问题。

简而言之，我在社区中发现我可以在 Heroku 做到这一点，因为…

 [## 为什么我上传的文件丢失/被删除了？

### 文件被上传到应用程序，但过一会儿就会消失或被删除。Heroku 文件系统是短暂的…

help.heroku.com](https://help.heroku.com/K1PPS2WM/why-are-my-file-uploads-missing-deleted) 

当您部署它时，您的`public/uploads`将被自动删除。您可以尝试部署，然后使用 Heroku CLI `heroku run bash`和`ls public/uploads`。那边的图像会是空的。为此，你必须阅读我在上面提供的 FAQ。

这就是为什么你必须在项目中改变目标存储。以我为例，我把它修改成了谷歌云存储。

Strapi 支持在 Google 云存储上进行集成。任何流行的包，包是
Vanessa Lith 的`strapi-provider-upload-google-cloud-storage`(CMIIW 关于名字)。

[](https://www.npmjs.com/package/strapi-provider-upload-google-cloud-storage) [## strapi-提供商-上传-谷歌-云存储

### 非官方的谷歌云存储提供商从你的应用根目录安装软件包。

www.npmjs.com](https://www.npmjs.com/package/strapi-provider-upload-google-cloud-storage) 

该包支持 Strapi v3 和 Strapi v4。文档也直截了当，非常清晰。请检查这个超酷的包装。

[](https://github.com/Lith/strapi-provider-upload-google-cloud-storage) [## GitHub-Lith/strapi-Provider-Upload-Google-Cloud-Storage:Google Cloud Storage Upload Provider for…

### 非官方的谷歌云存储提供商从你的应用根目录安装软件包。

github.com](https://github.com/Lith/strapi-provider-upload-google-cloud-storage) 

好的，继续向下滚动每个人。

# 1.装置

从您的应用程序根目录安装软件包。

与`npm`

```
npm install strapi-provider-upload-google-cloud-storage --save
```

或者`yarn`

```
yarn add strapi-provider-upload-google-cloud-storage
```

# 2.在 Google 云存储上创建您的存储桶

应该使用**细粒度的**访问控制来创建 bucket，因为插件将使用公共读取权限来配置上传的文件。

## 如何创建一个桶？

*   [https://cloud.google.com/storage/docs/creating-buckets](https://cloud.google.com/storage/docs/creating-buckets)

## 我的水桶在哪里？

*   [https://cloud.google.com/storage/docs/locations](https://cloud.google.com/storage/docs/locations)

# 3.设置 Google 身份验证

如果你部署到一个支持[应用默认凭证](https://cloud.google.com/docs/authentication/production#finding_credentials_automatically)的 Google 云平台产品(比如 App Engine、Cloud Run、Cloud Functions 等。)，那么可以跳过这一步。

如果您在 GCP 以外部署，请按照以下步骤设置身份验证:

1.  在 GCP 控制台中，转至“创建服务帐户密钥”页面。[转到创建服务帐户密钥页面](https://console.cloud.google.com/apis/credentials/serviceaccountkey)
2.  从**服务帐户**列表中，选择新服务帐户。
3.  在“服务帐户名称”字段中，输入一个名称。
4.  从角色列表中，选择**云存储** > **存储管理员**。
5.  选择`JSON`键类型
6.  单击创建。一个 JSON 文件，包含下载到您计算机的密钥。
7.  复制下载的 JSON 文件的全部内容
8.  打开 Strapi 配置文件
9.  将其粘贴到“服务帐户 JSON”字段(如`string`或`JSON`，注意缩进)

# 4.在生产目录中创建 plugins.js 文件

创建或编辑`config/env/production/plugins.js`

```
const fs = require('fs');
require('dotenv').config();
module.exports = ({ env }) => ({
    upload: {
        config: {
            provider: 'strapi-provider-upload-google-cloud-storage',
            providerOptions: {
                serviceAccount: JSON.parse(fs.readFileSync(process.env.GCS_SERVICE_ACCOUNT)),
                bucketName: env('GCS_BUCKET_NAME'),
                basePath: env('GCS_BASE_PATH'),
                baseUrl: env('GCS_BASE_URL'),
                publicFiles: true,
                uniform: false,
                gzip: true,
            },
        },
    },
});
```

不要忘记为附加包安装`dotenv`。

如果您的环境有不同的上传提供者，您可以根据环境覆盖`plugins.js`文件:

*   `config/env/development/plugins.js`
*   `config/env/production/plugins.js`

在`config/env/{env}/`下的这个文件将覆盖主文件夹`config`中的默认配置。

环境变量可以按照你的方式改变。

# 如何配置变量？

## `serviceAccount`:

Google 账号提供的 JSON 数据(之前解释过)。如果您要部署到支持应用程序默认凭据的 GCP 产品，则可以省略此步骤，身份验证将自动运行。

可以设置为字符串、JSON 对象，也可以省略。

## `bucketName`:

Google 云存储上的存储桶的名称。

*   需要

您可以在 Google Cloud 文档中找到更多信息。

## `baseUrl`:

定义您的基本 Url，首先是默认值:

*   [https://storage.googleapis.com/{bucket-name}](https://storage.googleapis.com/%7Bbucket-name%7D)
*   [https://{bucket-name}](/{bucket-name})
*   [http://{桶名}](/{bucket-name})

## `basePath`:

定义保存每个媒体文档的基本路径。

*   可选择的

## `publicFiles`:

布尔值，用于在文件上传到存储时为文件定义公共属性。

*   默认值:`true`
*   可选择的

## `uniform`:

启用统一存储桶级别访问时，用于定义统一访问的布尔值。

*   默认值:`false`
*   可选择的

## `cacheMaxAge`:

为上传的文件设置缓存控制头的数字。

*   默认值:`3600`
*   可选择的

## `gzip`:

用于定义文件是否使用 gzip 压缩上传和存储的值。

*   可能的值:`true`、`false`、`auto`
*   默认值:`auto`
*   可选择的

# `metadata`:

上传文件时执行的计算文件元数据的功能。

如果没有提供函数，则使用以下元数据:

```
{
  contentDisposition: `inline; filename="${file.name}"`,
  cacheControl: `public, max-age=${config.cacheMaxAge || 3600}`,
}
```

*   默认值:`undefined`
*   可选择的

示例:

```
metadata: (file) => ({
    cacheControl: `public, max-age=${60 * 60 * 24 * 7}`, // One week
    contentLanguage: 'en-US',
    contentDisposition: `attachment; filename="${file.name}"`,
  }),
```

可用的属性可以在[云存储 JSON API 文档](https://cloud.google.com/storage/docs/json_api/v1/objects/insert#request_properties_JSON)中找到。

# `generateUploadFileName`:

执行该函数以生成上传文件的名称。这种方法可以对文件名进行更多的控制，例如可以用于包含自定义散列函数或动态路径。

当没有提供功能时，使用默认算法。

*   默认值:`undefined`
*   可选择的

示例:

```
generateUploadFileName: (file) => {
    const hash = ...; // Some hashing function, for example MD-5
    const extension = file.ext.toLowerCase().substring(1);
    return `${extension}/${slugify(path.parse(file.name).name)}-${hash}.${extension}`;
  },
```

# 5.设置`strapi::security`中间件以避免 CSP 阻止 URL

创建或编辑`./config/env/production/middlewares.js`

*   在字段`img-src`和`media-src`中添加您自己的 CDN URL，默认为`storage.googleapis.com`，但您需要添加您自己的 CDN URL

```
module.exports = [
  'strapi::errors',
  {
    name: 'strapi::security',
    config: {
      contentSecurityPolicy: {
        useDefaults: true,
        directives: {
          'connect-src': ["'self'", 'https:'],
          'img-src': ["'self'", 'data:', 'blob:', 'storage.googleapis.com'],
          'media-src': ["'self'", 'data:', 'blob:', 'storage.googleapis.com'],
          upgradeInsecureRequests: null,
        },
      },
    },
  },
  'strapi::cors',
  'strapi::poweredBy',
  'strapi::logger',
  'strapi::query',
  'strapi::body',
  'strapi::favicon',
  'strapi::public',
];
```

# 6.部署到 Heroku

最后，如果你已经遵循了所有的指令，请将你的代码放入 Heroku。尝试上传或在媒体菜单中获取，如果你一直发现错误，请在评论区评论。

在我的例子中，**我曾经得到错误内部服务器错误和状态代码 500** 。

解决问题的方法可能是因为:

*   您还没有添加安全中间件

```
...
{
    name: 'strapi::security',
    config: {
      contentSecurityPolicy: {
        useDefaults: true,
        directives: {
          'connect-src': ["'self'", 'https:'],
          'img-src': ["'self'", 'data:', 'blob:', 'storage.googleapis.com'],
          'media-src': ["'self'", 'data:', 'blob:', 'storage.googleapis.com'],
          upgradeInsecureRequests: null,
        },
      },
    },
  },
...
```

*   或者你应该在`extensions/upload/config/`中创建文件`settings.json`

```
{
    "provider": "strapi-provider-upload-google-cloud-storage",
    "providerOptions": {
        "serviceAccount": {
            "type": "service_account",
            "project_id": "xxx-xxx-xxxxxxxx",
            "private_key_id": "xxxx2b00abxx243b17d8xxd7fe5bf5xx7970cxxx",
            "private_key": "xxxxxxxxxxx and so on",
            "client_email": "strapi-storage@xxx-xxx-xxxxxxxx.iam.gserviceaccount.com",
            "client_id": "xxxxxxnumberxxxxxxxxxx",
            "auth_uri": "https://accounts.google.com/o/oauth2/auth",
            "token_uri": "https://oauth2.googleapis.com/token",
            "auth_provider_x888_cert_url": "https://www.googleapis.com/oauth2/v1/certs",
            "client_x888_cert_url": "https://www.googleapis.com/robot/v1/metadata/x888/strapi-storage%40xxx-xxx-xxxxxxxx.iam.gserviceaccount.com"
        },
        "bucketName": "cms-strapi-storage",
        "basePath": "",
        "baseUrl": "https://storage.googleapis.com/strapi-storage",
        "publicFiles": true,
        "uniform": false,
        "gzip": true
    }
}
```

有关详细信息，请访问

[](https://github.com/strapi/strapi/issues/9701) [## 无法从 UI 问题#9701 strapi/strapi 将文件上传到媒体资产

### 错误报告描述无法从管理 UI 界面上传媒体的错误重现该问题的步骤请转到…

github.com](https://github.com/strapi/strapi/issues/9701) 

是的，这是一个将 Strapi 与 Google 云存储集成的小故事。

感谢 Vanessa Lith 和她的团队创建了一个很棒的项目，将 Strapi 连接到 Google 云存储。

结束了。希望我能帮你解决这个问题。快乐阅读&快乐编码。谢谢你。

# 警惕！

如果你们来自**印尼**，想要支持我写更多的东西，希望你们能从钱包里拿出一点来。你可以通过一些方式分享你的天赋，

## 萨韦里亚

【https://saweria.co/pandhuwibowo 

![](img/0089e6c52b43886c90f7f853a7b8a73b.png)

## 特拉克特尔

[https://trakteer.id/goodpeopletogivemoney](https://trakteer.id/goodpeopletogivemoney)

![](img/d26e4901a3c5106b7c99a3c8a99d74a0.png)

# 参考

[](https://github.com/strapi/strapi/issues/8275) [## 无法上传到媒体库(500 内部服务器错误)生产问题#8275 …

### 描述在生产环境中尝试向媒体库上传任何内容时出现的错误，我将获得一台 500 内部服务器…

github.com](https://github.com/strapi/strapi/issues/8275) [](https://github.com/strapi/strapi/issues/9701) [## 无法从 UI 问题#9701 strapi/strapi 将文件上传到媒体资产

### 错误报告描述无法从管理 UI 界面上传媒体的错误重现该问题的步骤请转到…

github.com](https://github.com/strapi/strapi/issues/9701) [](https://github.com/Lith/strapi-provider-upload-google-cloud-storage) [## GitHub-Lith/strapi-Provider-Upload-Google-Cloud-Storage:Google Cloud Storage Upload Provider for…

### 非官方的谷歌云存储提供商从你的应用根目录安装软件包。

github.com](https://github.com/Lith/strapi-provider-upload-google-cloud-storage) [](https://www.npmjs.com/package/strapi-provider-upload-google-cloud-storage#setup-auth) [## strapi-提供商-上传-谷歌-云存储

### 非官方的谷歌云存储提供商从你的应用根目录安装软件包。

www.npmjs.com](https://www.npmjs.com/package/strapi-provider-upload-google-cloud-storage#setup-auth) [](https://githubplus.com/Lith/strapi-provider-upload-google-cloud-storage/issues) [## strapi-提供商-上传-谷歌-云存储- Github Plus

### 描述一下我在 Cloud Run 上部署了一个 strapi 应用程序，它使用 Google 云存储作为媒体存储…

githubplus.com](https://githubplus.com/Lith/strapi-provider-upload-google-cloud-storage/issues)  [## 为什么我上传的文件丢失/被删除了？

### 文件被上传到应用程序，但过一会儿就会消失或被删除。Heroku 文件系统是短暂的…

help.heroku.com](https://help.heroku.com/K1PPS2WM/why-are-my-file-uploads-missing-deleted)