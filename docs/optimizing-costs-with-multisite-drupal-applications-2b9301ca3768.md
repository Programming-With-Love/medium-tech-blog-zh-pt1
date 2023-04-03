# 使用多站点 Drupal 应用程序优化成本

> 原文：<https://medium.com/globant/optimizing-costs-with-multisite-drupal-applications-2b9301ca3768?source=collection_archive---------2----------------------->

# 简介:-

我写这篇文章是为了分享我在多站点模式下设置 Drupal 应用程序时获得的经验和知识。当您谈到 Drupal 多站点设置时，您必须选择子域或子目录方法。

这两种方法都工作得很好，但是它们都有各自的优缺点，这将在以后讨论。子域法需要购买域名，这转化为成本，而 subdir 节省你。除此之外，子目录方法还有其他优点，比如减少管理开销、减少需要维护的代码等。我已经在下面的相应章节中详细讨论了这些内容。

最常见的用例是子站点多站点:每个域一个站点**site1.example.com**、**site2.example.com**等等。然而，有时你想要一个子目录多站点:每个目录一个站点【example.com/site1、**、**等等。你既不能创建子网站/子域(因为你没有访问 DNS 的权限)，也不想出于 SEO(搜索引擎优化)的考虑而这样做。多站点 Drupal 托管中最有趣的事情是每个网站都有自己的数据库、内容和主题，但是所有的站点都有相同的代码库。

本文涵盖了以下几点

1.背景

2.为什么选择 Subdir？

3.先决条件

4.要遵循的步骤

5.赞成的意见

6.骗局

7.建议

8.结论

9.参考

**1。背景:-**

今天，许多公司都在进行多站点设置，因为他们可以控制所有站点的相同代码库。下面是一些可以通过使用多站点子目录 Drupal 解决的用例

*   如果你有几个功能相似的网站。在这种情况下，多站点是更容易管理所有网站的一个很好的解决方案。
*   如果您需要多个具有相同核心、模块、主题或发行版的站点。
*   如果您的资源和/或劳动力有限，并且需要管理大量站点。

**2。为什么选择 Subdir？**

顾名思义，子目录有助于在多站点 drupal 应用程序中设置子目录，而不是为每个站点使用子域。它帮助你解决问题，例如-

*   管理多个域的开销—当您通过子域方法托管多个站点时，您需要管理这些域的注册和续订流程。更不用说每次你升级你的网站或者发布一些新功能的时候对每个子域的维护和测试了。
*   成本开销——您必须为您购买和在多站点应用程序中使用的每个子域付费。

子目录方法有助于解决上述问题。除此之外，这种设置还有助于满足多个网站的语言翻译需求，例如

*   **es.example.com/site1**
*   【es.example.com/site2 号

**3。先决条件:-**

下面是这篇文章的先决条件

I .)对 Drupal 结构的基本理解

二。)了解使用 drush (Drupal shell)命令编译、构建和部署 Drupal 应用程序的过程。
这里是 Drush 命令的参考:-[https://drushcommands.com/](https://drushcommands.com/)

三。)已经建立了一个单一的 Drupal 网站(对我们来说，我们以前的网站是 example.com，然后我们用一个域把它增强到另外 3 个网站)，然后这篇文章将指导你建立一个多站点子目录 Drupal。你应该已经安装了 php，mysql，drush，composer，nginx 来实现基本的设置。

四。)文中讨论的 Drupal 应用程序有如下 4 个站点示例:example.com 可以用你的网站登陆页面
a . example.com
b . example.com/site1
c . example.com/site2
d . example.com/site3

动词 （verb 的缩写）)对 Linux 目录结构的基本了解

不及物动词)对 Nginx Web 服务器的基本了解

**4。接下来的步骤:-**

安装和设置多站点不像其他 Drupal 特性那样简单，但是，下面的说明将帮助您启用多站点特性并完美运行。让我们开始建立一个只有单一代码库的新站点。

A.)在 sites.php 境内输入:-

导航到网站根目录/sites/并找到 sites.php 文件。在对 sites.php 进行任何更改之前，请备份该文件，并使用您选择的编辑器(即 nano，vi )
编辑 sites.php。在本文中，我们在一个域中有四个站点，即 example.com、example.com/site1,、example.com/site2,、example.com/site3，sites.php 的条目应如下所示。

```
$sites[‘example.com’] = ‘example’;
$sites[‘example.com.site1’] = ‘site1’;
$sites[‘example.com.site2’] = ‘site2’;
$sites[‘example.com.site3’] = ‘site3’;
```

B.)为多站点创建目录结构

因为我们已经有了 example.com 设置，所以在 webroot/sites 目录中已经有了一个名为“abc”的目录。因此，我们需要为其他 3 个站点创建另外 3 个目录。跳转到 webroot/sites 并运行以下命令-

```
mkdir site1mkdir site2mkdir site3
```

请确保上述所有目录至少拥有 775 权限。

C.)为所有站点创建 settings.php:-

现在我们需要为所有三个新创建的站点创建一个 settings.php 文件。让我们从默认目录(在你的 webroot/sites 目录下)到新创建的 site1，site2，site3 目录下创建一个 default.settings.php 的拷贝，名字应该是 settings.php

```
cp <webroot>/sites/default/default.settings.php <webroot>/sites/site1/settings.phpcp <webroot>/sites/default/default.settings.php <webroot>/sites/site2/settings.phpcp <webroot>/sites/default/default.settings.php <webroot>/sites/site3/settings.php
```

D.)创建符号链接:-

现在，我们需要为我们所有的 3 个站点创建符号链接，因为如果我们不创建符号链接，nginx 将不会获得各个站点的 index.php 文件。

请注意，您需要创建一个符号链接，其名称与您放在 sites.php 的名称相同，并且与您创建的目录名称相同

转到你的 webroot 文件夹，运行下面的命令来创建符号链接

```
cd <webroot>ln -s . site1ln -s . site2ln -s . site3
```

在正确的位置创建符号链接是一个重要的步骤，因为这样做的错误不会让 nginx 找到“index.php ”,并将通过错误“请求 URL 未找到”

E.)数据库修改或创建:-

现在我们需要继续处理数据库部分。根据我们的要求，我们可以使用主站点(即 example.com)已经使用的现有数据库，或者我们可以创建一个新的数据库

I .)如果您想要使用另一个站点已经使用的现有数据库(这里，该数据库正由主站点 example.com 使用)，那么您需要修改每个站点的 settings.php 文件。我们以 site1 为例。

打开 <webroot>/sites/site1/settings.php，将现有站点的 settings.php 中的数据粘贴到本站点的末尾。</webroot>

请记住将“$ settings[' config _ sync _ directory ']”的值从以前的目录更改为当前的目录名称，即

来自"../config/abc/sync "。到”../config/site1/sync "

您将在返回 webroot 的一个路径中找到 config 目录。

二。)如果您想使用一个新的数据库，那么您需要创建一个数据库，在数据库提示符下运行命令“create database site1”这将为“site1”创建一个空白数据库，您还需要为其他两个站点做同样的事情。

创建新数据库后，如果您想要使用旧数据库的数据/内容/主题，则需要创建现有数据库的备份，并且需要恢复到新创建的数据库，如下所示

a.)进入 <webroot>/sites 目录:-</webroot>

```
cd <webroot>/sites
```

b.)为数据库“abc”创建备份

```
mysqldump -u <database username that you will get from settings.php for abc site > -p<Password for the database which also you will get from settings.php> abc > /tmp/abcdatabasebackup.sql
```

例如，如果“abc”数据库的用户名是“drupal ”,而“abc”数据库的密码是“1234$ ”,那么命令将如下所示

```
mysqldump -u drupal -p1234$ abc > /tmp/abcdatabasebackup.sql
```

请注意，数据库“-p”和密码之间没有空格

c.)将数据库备份还原到站点 1

```
drush -l site1 sql-cli < /tmp/abcdatabasebackup.sql
```

数据库恢复完成后，请确保检查 settings.php，并按照第 5a 点所述，从“$ settings[' config _ sync _ directory ']”开始对行进行相关更改

F.)创建“文件”目录:-

进入 <webroot>/sites/site1，检查是否有一个“文件”目录，如果你正在恢复任何旧的数据库。如果您创建了一个新的数据库，并将其留空而不恢复任何内容，那么您需要在所有站点的每个子目录中创建一个“文件”目录。</webroot>

这个文件目录将包含所有的图像，媒体文件，并确保这个“文件”目录应该至少有“775”权限，并应由“nginx:nginx”拥有

G.)Nginx 配置

现在，我们需要在 nginx 中做一些修改，以便 nginx 可以理解新创建的站点。

如果您使用 centos，那么进入“/etc/nginx/conf.d”目录，您将获得一个 nginx 配置文件。然而，它可以根据您的 web 服务器配置而变化，有时您也可以在“/etc/nginx/sites-available/”目录中有一个 nginx 配置文件。

如果你使用的是“Ubuntu ”,你大部分时间都会在“/etc/nginx/sites-available/”里面有 nginx 配置文件。

我们需要提到 nginx 中的特定位置指令，如下所示，适用于 3 个站点。

```
location /site1 {try_files $uri /site1/index.php?$args;}location /site2 {try_files $uri /site2/index.php?$args;}location /site3 {try_files $uri /site3/index.php?$args;}
```

请注意，如果你没有在 nginx 中设置“位置”,那么它会给出“找不到页面”的错误，因为 Nginx 找不到“index.php”..

H.)为此子网站配置 Drupal

现在，您需要进入浏览器，点击站点 1 的“https:/example . com/site1”(在测试其他站点时，将 site 1 替换为 site2、site3)，使用您的用户 id 和密码登录，这将带您完成该站点的新 drupal 配置。

如果您得到一个错误，说明“文件”目录没有写权限，那么运行下面的命令

```
chcon -R -t httpd_sys_rw_content_t <websiteroot>/sites/site1/files
```

如果“settings.php”出现同样的错误，你也需要对 settings.php 运行同样的命令。还要确保“settings.php”应该拥有 444 权限。

```
chcon -R -t httpd_sys_rw_content_t <websiteroot>/sites/site1/settings.php
```

I .)改变 UUID :-

在配置好 Drupal 之后，您需要为这个特定的数据库更改 uuid。

进入 <website root="">/../config/sync”并打开“system.site.yml”</website>

找到这个特定站点“site1”的“uuid”参数，复制 UUID 并粘贴到下面命令中的“uuid”之后。一旦设置了 uuid，就用“config-get”命令进行检查。

```
drush -l site1 config-set “system.site” uuid 16799278-c199–46cf-961b-58f3a0fbc980drush -l site1 config-get “system.site” uuid
```

完成上述更改后，按顺序运行以下所有命令-

```
drush -l site1 cr → For Cache Rebuilddrush -l site1 cim -y → For Import Configurationdrush -l site1 cr →Again For Cache Rebuild
```

所有这些完成后，你可以访问你的网站—[https://example.com/site1](https://example.com/site1)

重复上述所有步骤来设置站点 2 和站点 3。

**5。优点:-**

Drupal 多站点的第一个优势是我们所有的站点都使用单一的代码库，在这种情况下，开发人员和管理任务使用单一的代码库更容易。

二。)代码和库更新只需要做一次。

三。)虽然所有的网站都包含相同的代码库，但你可以用选定的主题/模块定制所有的网站。

四。)这也给了我们成本上的优势。支持所有网站流量的单一主机方案可能比托管单个网站更便宜。

**6。CONS:-**

I .)如果站点决定定制由特征模块提供的功能，那么它将丢失对该模块的任何进一步更新，因为它们已经偏离了该模块。

二。)如果所有站点的管理员都运行于相同的代码库、同一个人或一个高度相互信任的小组，那么您可能需要重新考虑使用 Drupal 的多站点配置。

三。)由于我们在一台服务器上托管所有 4 个站点，因此这可以被视为单点故障，但我们可以使用负载平衡器/自动扩展来消除这种情况。

四。)请注意，这一点适用于公共云，但不一定适用于私有云。

动词 （verb 的缩写）)如果代码出现任何问题，那么它将影响所有的网站

不及物动词)Drupal 在某些情况下容易出现安全漏洞，如果处理不当；允许未经授权的用户访问 Drupal 核心代码的错误。一旦黑客获得了您的核心代码，他们就可以通过一次黑客攻击尝试访问您的所有网站。非多站点设置可能需要单独的攻击，这可能会提供足够的时间来修复漏洞。

七。)此外，我们需要从基础设施和开发者端对这个多站点进行适当的维护。

7 .**。使用多站点时的建议:-**

I .)当您已经构建了 Drupal 多站点设置时，请始终使用 drush 进行新的部署或站点升级。当您有一个多站点设置时，使用 drush 要容易得多，因为 drush 允许您在构建代码或升级代码时指定不同的站点名称。

二。)总是用 sites.php 来管理站点，你会从 <webroot>/sites/sites，php(Drupal 7)和 example.sites.php(Drupal 8)那里得到 sites.php 的借鉴</webroot>

**8。结论:-**

本文解释了如何创建 subdir 多站点 drupal。使用一个域、一个代码库管理一个多站点 drupal 应用程序，使用多站点子目录设置要容易得多。适当的措施和预防措施可以帮助我们克服上述缺点，并使 Drupal 多站点设置的使用变得更容易。甚至大型组织现在也在使用这种技术，我们可以说 Drupal 多站点有着美好的未来。从单站点迁移到多站点的人可以参考这篇文章。

**9。参考:-**

Drupal——开源 CMS | Drupal.org

二。)[多站点 Drupal | Drupal | Drupal Drupal.org 维基指南](https://www.drupal.org/docs/multisite-drupal)