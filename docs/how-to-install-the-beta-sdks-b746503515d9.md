# 如何安装 Square 的测试版 SDK

> 原文：<https://medium.com/square-corner-blog/how-to-install-the-beta-sdks-b746503515d9?source=collection_archive---------0----------------------->

## 我们最近发布了对 SDK 的重大更新——以下是如何升级的方法。

> 注意，我们已经行动了！如果您想继续了解 Square 的最新技术内容，请访问我们在 https://developer.squareup.com/blog[的新家](https://developer.squareup.com/blog)

***【更新:测试版已经完成，请在此下载我们 SDK 的最新版本***[](https://docs.connect.squareup.com/articles/client-libraries)****】****作为提醒，如果您需要安装方面的帮助，请向我们反馈新的设计和功能，或者只是想了解有关 API 的更多信息，请在此请求邀请加入我们新的* [***Slack 社区***](https://docs.google.com/forms/d/e/1FAIpQLSfAZGIEZoNs-XryKqUoW3atFQHdQw5UqXLMOVPq3V4DEq-AJw/viewform)*

*我们的新 SDK 的 2.1.0 版本中的差异对于每种语言都是相同的(当然，这些变化在每种语言中看起来有点不同)。主要区别是:*

*   *支持 v1 端点，包括项目和库存管理、员工管理和交易报告的 v1 版本。*
*   *重命名和重新组织一些生成的 API:*

```
 *<=2.0.2                       2.1.0+
      CheckoutApi                     CheckoutApi
      LocationApi                     Location**s**Api
      TransactionApi                  Transaction**s**Api    
      RefundApi                       Transaction**s**Api 
      CustomerApi                     Customer**s**Api
      CustomerCardApi                 Customer**s**Api
                                      **V1EmployeesApi
                                      V1LocationsApi
                                      V1ItemsApi
                                      V1TransactionsApi***
```

*   *我们已经删除了每个请求的授权参数。使用我们的新设计，您只需要在开始时设置一次访问令牌，而不是在每个请求中都设置。为了在`php`中形象化这种差异，我们当前的模型如下所示:*

```
*<?php
require_once(__DIR__ . '/vendor/autoload.php');

$api_instance = new SquareConnect\Api\CheckoutApi();

$result = $api_instance->createCheckout(**$authorization**, $location_id, $body);

?>*
```

*在版本 2.1.0 及更高版本中，相同的请求看起来像:*

```
*<?php
require_once(__DIR__ . '/vendor/autoload.php');

**// Configure OAuth2 access token for authorization: oauth2
SquareConnect\Configuration::getDefaultConfiguration()->setAccessToken('YOUR_ACCESS_TOKEN');**$result = $api_instance->createCheckout($location_id, $body);

?>*
```

# *安装指南*

## *服务器端编程语言（Professional Hypertext Preprocessor 的缩写）*

*您可以使用命令行安装带有 composer 的 SDK 的 beta 2.1.0 版本:*

```
*composer require square/connect:dev-release/2.1.0*
```

*或者将以下内容添加到项目的`composer.json`文件中。*

```
*{
    “require”: {
        “square/connect”: “dev-release/2.1.0”
    }
}*
```

*您也可以从 GitHub 安装:*

```
*git clone [https://github.com/square/connect-php-sdk.git](https://github.com/square/connect-php-sdk.git)
cd connect-php-sdk
git checkout release/2.1.0*
```

*并在应用程序中使用熟悉的`require('connect-php-sdk/autoload.php');`。*

## *Java 语言(一种计算机语言，尤用于创建网站)*

*您需要从 GitHub 获取源代码，并且:*

```
*git clone [https://github.com/square/connect-php-sdk.git](https://github.com/square/connect-php-sdk.git)
cd connect-php-sdk
git checkout release/2.1.0
mvn install -DskipTests*
```

*您现在应该有一个新的`.jar`文件包含在您的项目中。*

## *红宝石*

*使用 Ruby，您可以通过运行以下命令来指定要安装的 square-connect gem 的 2.1.0.beta 版本:*

```
*gem install square_connect -v 2.1.0.beta*
```

## *计算机编程语言*

*您可以使用以下命令直接从 GitHub 安装 Python 包:*

```
*git clone [https://github.com/square/connect-python-sdk.git](https://github.com/square/connect-python-sdk.git)
git checkout release/2.1.0
sudo python setup.py install*
```

## *C#*

*你需要从 GitHub 下载文件并切换到 2.1.0 版本:*

```
*git clone [https://github.com/square/connect-csharp-sdk.git](https://github.com/square/connect-csharp-sdk.git)
git checkout release/2.1.0*
```

*然后，您可以通过在基于 Unix 的系统上运行`./build.sh`或者在 Windows 上运行`build.bat`来构建`.dll`。您可能需要下载依赖项，但是在构建时会提示您这样做。*

*要了解更多关于我们的 SDK 是如何制作的，以及我们的 Java SDK 的发布，请查看我们早先的帖子[宣布我们客户端 SDK 的新版本](/square-corner-blog/announcing-our-new-versions-of-our-client-sdks-1336d26e8099)。*