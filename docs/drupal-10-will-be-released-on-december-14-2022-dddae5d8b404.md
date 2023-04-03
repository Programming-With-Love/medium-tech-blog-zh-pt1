# Drupal 10 将于 2022 年 12 月 14 日发布

> 原文：<https://medium.com/globant/drupal-10-will-be-released-on-december-14-2022-dddae5d8b404?source=collection_archive---------0----------------------->

![](img/75852ee6b6191e4af146428f564df895.png)

By [Gábor Hojtsy](https://www.flickr.com/photos/gaborhojtsy/), [CC BY-SA 2.0](https://creativecommons.org/licenses/by-sa/2.0/) [Flickr](https://www.flickr.com/photos/gaborhojtsy/279354232)

正如之前在核心开发和战略计划博客上宣布的，Drupal 10 发布时间表包括了 Drupal 10 发布日期的三个可能窗口。最近决定 2022 年 12 月 14 日的第三个也是最后一个发布窗口。这将给我们部署了 Drupal 9 的网站所有者大约 11 个月的时间来计划升级到 Drupal 10。

# 八月不再是一个选项

专注于核心开发的开发人员在过去几个月里一直在努力工作，以完成 Drupal 10 的需求和战略目标。社区一直在努力工作，删除不推荐使用的代码，删除不必要的依赖项，更新我们的 JavaScript，并为迁移到 contribute 准备模块

Drupal 10 最关键的需求是我们与 CKEditor 版本 5 的集成。CKEditor 4 在 2023 年底寿终正寝，所以 Drupal 10 应该改用 CKEditor 5。为了将这个新版本的 CKEditor 集成到 Drupal 中，以及与 CKEditor 团队密切合作，已经投入了数千小时的工作。通过已经完成的工作，发现了需要解决的其他关键问题，以使 CKEditor 5 稳定，这些问题将无法在 8 月发布所需的 5 月 13 日测试版截止日期前完成。

# 这个新发布日期的优势

在 12 月而不是 8 月发布 Drupal 10 的主要优势是让我们有更多的时间来稳定 CKEditor，但这也意味着网站所有者有更多的时间来测试将他们的内容从 CKEditor 4 迁移到 Drupal 9 的新版本，因此我们可以确保这一重大变化的平稳和安全的升级路径。

此外，随着 12 月份的发布，核心开发团队获得了更多的时间来完成 Drupal 界面和主题的战略需求，包括将 JavaScript 依赖项更新到最新的主要版本，将奥利韦罗和克拉罗 Drupal core 构建为默认主题，以赋予 Drupal 10 新的外观，并稳定 StarterKit 主题构建器，以改善主题开发人员的体验，并使 Drupal core 更易于维护。

对于像我这样的 PHP 开发人员来说，最好的消息是 12 月的发布意味着 Drupal 10 将与 Symfony 6.2 一起发布，这将比当前的 6.0 版本有所改进和漏洞修复，并且还将减少安全团队的工作量。

正如之前宣布的那样，Drupal 10 将需要 PHP 8.1，12 月的发布意味着大多数主机提供商将支持 PHP 8.1，因此网站不必等待平台修复才能开始更新。Drupal 10。PHP 8.2 也计划在 11 月发布，Drupal 10 将尽可能多地包含与它的兼容性。(PHP 8.1 在 2024 年 11 月之前仍将是 Drupal 10 的最低要求)。

# Drupal 9.4 的发布

根据 12 月的时间表，Drupal 9.4.0 将于 6 月 15 日发布。在此之后，Drupal 9 和 10 的开发将在 9.5.x 和 10.0.x 上继续。Drupal 10 的所有要求必须在 2022 年 9 月 9 日的测试截止日期前完成。9 月 12 日那一周，Drupal 9.5 和 Drupal 10 的测试版将会发布，稳定和测试阶段将会开始。

# 摘要

虽然这个新的日期会给核心开发团队额外几个月的时间来完成战略需求，给我们 Drupal 站点所有者将近一年的时间来计划期待已久的升级，但仍然有很多事情要做，社区需要很多帮助。