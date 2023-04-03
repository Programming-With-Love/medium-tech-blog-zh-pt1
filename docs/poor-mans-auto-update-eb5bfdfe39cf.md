# 可怜人的自动更新

> 原文：<https://medium.com/oracledevs/poor-mans-auto-update-eb5bfdfe39cf?source=collection_archive---------1----------------------->

由克里斯·本森

![](img/51bd1642d9a04c44e2ce272e8e5e2b73.png)

Photo by Miguel Á. Padriñán: [https://www.pexels.com/photo/close-up-shot-of-keys-on-a-red-surface-2882668/](https://www.pexels.com/photo/close-up-shot-of-keys-on-a-red-surface-2882668/)

回到过去，GitHub 允许匿名下载。现在它需要用户名/密码或 ssh 密钥。当 Pi 不在你的控制范围内时，那就不好了。因此，介绍克里斯本森穷人的自动更新。无论你做什么，不要在家里这样做，我是专业人士，了解安全漏洞。

场景是这样的:你有一个运行在你的 Raspberry Pi 上的脚本，你需要更新它。让我们假设您的脚本被设置为作为一个同步作业在启动时运行。你可以在这里阅读关于在树莓 Pi [上设置一个 chron 作业的信息。](https://bc-robotics.com/tutorials/setting-cron-job-raspberry-pi/)

# 步伐

1.  在 Pi 上创建本地目录结构，其中“file.py”是您的脚本。

```
/updater
├── repo
│ ├── file.py
├── file.py
├── updater.py 
```

2.复制这个 bash 脚本。

[updater.sh](https://github.com/chrisbensen/chris-blogs/blob/main/WeekendHacks/PoorManAutoUpdate/files/updater.sh)

```
#!/bin/bashcurl -X GET \
  <UrlToFile> \
  -o file.pyfile1=”/users/pi/updater/repo/file.py”
file2=”/users/pi/updater/file.py”if cmp -s “$file1” “$file2”; then
  # same, do nothing
  echo “same”
else
  # different
  echo “different”
  cp $file1 $file2 reboot
fi
```

3.创建 Oracle 云对象存储桶。
4。公开那个桶。
5。将您的文件复制到桶中。
6。通过更改< UrlToFile >来编辑脚本。从 Object Details 中获取 URL，并从末尾删除文件名，这样就只有 URL 的以下部分:`{accountRestEndpointURL}/{containerName}/{objectName}`
7。编辑脚本，使路径正确。
8。设置一个同步作业，根据需要随时运行“updater.py”来检查更新。每 5 分钟一次可能有点过了，但是让我们从这开始吧。你可以在这里阅读关于在树莓 Pi [上设置一个 chron 作业的信息。](https://bc-robotics.com/tutorials/setting-cron-job-raspberry-pi/)

您可以在这里阅读关于从对象存储[下载文件的文档。](https://docs.oracle.com/en/cloud/cloud-at-customer/occ-get-started/download-file.html)

# 概观

文件`updater/repo/file.py`是被复制下来的文件。然后，该脚本将其与更新程序目录中的文件进行比较。如果它们是不同的，更新目录用新的更新。`updater/file.py`是您希望符号链接指向的人。

`updater.py`中的**重启**将重启 Pi，启动更新后的`file.py`脚本。我知道这不是绝对最好的方法，但它确实有效，而且相当简单。

所以你有它，一个穷人的自动更新。

# 结论

现在我确实知道，有了 OCI 对象存储，你可以使用一个 API 进行预认证请求。如果您使用来自 Pi 的 REST 调用，您可以保持对象存储私有。然而，这是额外的工作，我将把它留给用户，或者以后的文章！

同时，你可以在我们的公共 Slack 上谈论这个[，或者](https://bit.ly/devrel_slack)[注册免费等级](https://signup.cloud.oracle.com/?language=en&sourceType=:ex:tb:::::&SC=:ex:tb:::::&pcode=)并亲自尝试。