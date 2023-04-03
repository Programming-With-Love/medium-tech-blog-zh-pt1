# 在 Android 上使用 Google 的 SafetyNet reCAPTCHA API

> 原文：<https://medium.com/quick-code/using-googles-safetynet-recaptcha-api-with-android-48e2cf12f370?source=collection_archive---------1----------------------->

![](img/95250ffdbeecee0bca9235d49dacef1c.png)

SafetyNet 服务包括一个 reCAPTCHA API，您可以使用它来保护您的应用程序免受恶意流量的攻击。

reCAPTCHA 是一项免费服务，使用先进的风险分析引擎来保护您的应用程序免受垃圾邮件和其他滥用行为的影响。

## **第一步:浏览 reCAPTCHA 服务条款**

在访问 API 之前，请阅读并理解所有适用的[服务条款](https://developers.google.com/terms/)。

## **步骤 2:** 注册一个 reCAPTCHA 密钥对

要注册密钥对，请访问 [reCAPTCHA Android 注册网站](https://g.co/recaptcha/androidsignup)，为您的密钥添加一个唯一的标签，提供使用此 API 密钥的每个应用程序的包名，并获取出现在下一页的站点密钥和秘密密钥。

## 步骤 3:添加安全网 API 依赖项

```
apply plugin: 'com.android.application'
...
dependencies {
    **compile 'com.google.android.gms:play-services-safetynet:11.8.0'**
}
```

## 第四步:终于到了成为人类的时候了😎

只要您想调用 captcha 小部件，就使用下面的代码:

```
SafetyNet.getClient(this).verifyWithRecaptcha(**YOUR_API_SITE_KEY**)
        .addOnSuccessListener((Executor) this,
            new OnSuccessListener<SafetyNetApi.RecaptchaTokenResponse>() {
                @Override
                public void onSuccess(SafetyNetApi.RecaptchaTokenResponse response) {
                  // Indicates communication with reCAPTCHA service was successful.
                 String userResponseToken=response.getTokenResult();
                    if (!userResponseToken.isEmpty()) {
                       // Validate the user response token using the
                       // [reCAPTCHA siteverify API](https://developers.google.com/recaptcha/docs/verify).
                    }
                }
        })
        .addOnFailureListener((Executor) this, new OnFailureListener() {
                @Override
                public void onFailure(@NonNull Exception e) {
                    if (e instanceof ApiException) {
                     ApiException apiException = (ApiException) e;
                     int statusCode = apiException.getStatusCode();
                    } else {
                        // A unknown type of error occurred.
                        Log.d(TAG, "Error: " + e.getMessage());
                    }
                }
        });
```

## 步骤 5:最后一步在服务器上验证用户的响应令牌

当 reCAPTCHA API 执行`[onSuccess()](https://developers.google.com/android/reference/com/google/android/gms/tasks/OnSuccessListener.html#onSuccess(TResult))`方法时，用户已经成功完成了验证码挑战&我们仍然需要验证来自后端服务器的用户响应令牌。

因此，您需要发送一个 post 请求到 URL:

API 请求 URL:[https://www.google.com/recaptcha/api/siteverify](https://www.google.com/recaptcha/api/siteverify)

方法:邮寄

发布参数:

1.  机密—必需。您的站点和 reCAPTCHA 之间的共享密钥。
2.  响应—必需。reCAPTCHA 提供的用户响应令牌，用于验证您站点上的用户。
3.  remoteip —可选。用户的 IP 地址。

API 响应:

```
{
  "success": true|false,
  "challenge_ts": timestamp,  // timestamp of the challenge load 
  "apk_package_name": string, // the package name of the app
  "error-codes": [...]        // optional
}
```

快乐编码:)

## 结论

您现在知道如何使用 SafetyNet reCAPTCHA API 来保护您的 Android 应用程序和后端基础设施免受僵尸程序的攻击。你再也不用担心自动注册、屏幕抓取或机器人生成的垃圾邮件。

如果你喜欢这篇文章，请给它竖起大拇指，评论它，并与你的朋友分享。

请点击👏按钮下面几下，以示支持！⬇⬇谢谢！不要忘记**遵循下面的**快速代码。

> 找到关于各种编程语言的[快速代码](http://www.quickcode.co/)的免费课程。获取 [Messenger](https://www.messenger.com/t/1493528657352302) 的新更新。