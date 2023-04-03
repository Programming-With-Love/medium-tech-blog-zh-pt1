# 探索 Pinterest:云工程

> 原文：<https://medium.com/pinterest-engineering/discover-pinterest-cloud-engineering-a818413deca4?source=collection_archive---------3----------------------->

全球每月有超过 1 亿个活跃 pin，每秒钟有 500 亿个 pin 和 15 万个请求，可靠而安全的云工程是在我们所有平台(iOS、Android 和 web)上提供良好用户体验的关键。我们最近举办了一场云工程活动，让工程师们更深入地了解我们如何在云中扩展，并促进关于 sre 角色的对话。演讲者包括 Pinterest 工程师[拉杰·帕特尔](https://www.pinterest.com/patel0538/)、[宋宝刚](https://www.pinterest.com/baogangs/)、[杰瑞米·卡罗尔](https://www.pinterest.com/jeremycarroll/)和[张天英](https://www.pinterest.com/changtianying/)，以及来自[诺里·海基宁](https://www.pinterest.com/n0r1/)的主题演讲，他是谷歌的资深 SRE。观看完整视频[此处](https://www.youtube.com/watch?v=Enxd6U7zrJc)及以下。

## 故障服务器上的桥梁:SRE 简史

过去八年里，诺里一直是谷歌的 SRE，负责管理所有内部产品和谷歌云平台共享的负载平衡和代理基础设施层。在她的演讲中，诺里关注了网站可靠性工程的发展，这项工作最近已经成为网站可用性的同义词。她讲述了 SRE 角色是如何产生的，它现在意味着什么，以及从结构角度实现大规模可靠性需要什么。

## 在 Pinterest 部署

包钢是我们内部开发工具团队的工程主管，该团队构建服务来帮助开发人员快速安全地交付功能。在他的演讲中，他谈到了我们内部的代码部署系统 [Teletraan](https://engineering.pinterest.com/blog/under-hood-teletraan-deploy-system) 。它支持重要功能，如滚动部署、回滚、热修复和自动部署，以及部署暂停和恢复、webhook、权限控制等。它已经在生产中运行了一年，我们的工程师很喜欢使用它。睁大你的眼睛——我们计划在未来开源它。

## 在云中运行数据库的经验教训

Jeremy 是我们现场可靠性工程团队的创始成员之一。他帮助设计、构建和监控我们的应用和系统基础设施，处理每月数十亿的页面浏览量，应对巨大的增长和可扩展性挑战。Tian-Ying 是存储和缓存团队的一名软件工程师。她为 HBase 构建了灾难恢复框架，负责修复各种 bug，并为 HBase 的性能和可靠性问题构建工具。Tian-Ying 和 Jeremy 共同介绍了大规模复杂故障的不可避免性和不可预测性，并概述了 Pinterest 如何在云中不可靠的组件上构建弹性存储系统。

## Pinterest 在公共云中的旅程

Raj 是我们的云工程主管，管理着负责我们快速增长的服务基础架构的团队。他专注于跨堆栈的站点可靠性工程、公共和混合云基础架构、产品和基础架构安全性、开发人员系统和发布工程、可见性和指标平台以及内部应用程序开发。在他的演讲中，他分享了在扩展 Pinterest 和实现云工程的使命方面的观察，以推动我们服务的弹性、速度、效率和信任。

对云工程感兴趣？[加入我们的团队](https://careers.pinterest.com/careers/engineering/san-francisco)！