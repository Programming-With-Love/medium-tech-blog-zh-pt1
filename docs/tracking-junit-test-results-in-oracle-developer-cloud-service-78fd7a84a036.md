# 在 Oracle 云中跟踪 JUnit 测试结果

> 原文：<https://medium.com/oracledevs/tracking-junit-test-results-in-oracle-developer-cloud-service-78fd7a84a036?source=collection_archive---------0----------------------->

就 JUnit 而言，开发者云服务为您提供了以下功能

*   查看所有已执行测试的列表和**累积测试指标**
*   测试结果历史以**图形**的形式跟踪所有测试的**历史**

我们将学习如何

*   **设置**开发者云服务实例 Git 存储库中的源代码
*   **配置**构建过程以及与 JUnit 测试相关的动作
*   **执行**构建和**跟踪**测试结果

# 单元测试

让我们看一下单元测试——这个项目在这里[可用](https://github.com/abhirockzz/junit-sample-project),它展示了一个简单的基于 JPA 的应用程序及其单元测试

# 设置

## 项目和代码库创建

[在您的 Oracle 开发人员云实例中创建一个项目](http://docs.oracle.com/cloud/latest/devcs_common/CSDCS/GUID-3317B279-A9C0-4566-A289-BD651A89D7B5.htm#GUID-7B30C8EC-6CDA-4F14-9791-8AE3BB3E8343)

![](img/d2539d9f9426bb323876fe31140bfe90.png)![](img/bc9969af6eb2184ab3c2afe704de7da3.png)![](img/7c8f967a0cbbf838cde88fdadb7e0131.png)

[创建一个 Git 存储库](http://docs.oracle.com/cloud/latest/devcs_common/CSDCS/GUID-37938DB0-544E-4A94-B8A1-2B7E8ED8A972.htm#CSDCS3240) —浏览到**主页**选项卡，点击**新建存储库**并按照步骤操作

![](img/076ba4f098123a2d3a997266272ddb6e.png)![](img/a1a88aa39d3e6d8e08ee15b8109924f8.png)

您应该看到您的新存储库已经创建

![](img/f363bea6cdd56423f7309be9ba6289fd.png)

## 填充 Git repo

[将项目](http://docs.oracle.com/cloud/latest/devcs_common/CSDCS/GUID-B4C03296-8497-4356-8C74-2031D1FB96FC.htm#CSDCS-GUID-A33E83CE-845C-4393-8C93-936527033715)从您的本地系统推送到您刚刚创建的开发者云 **Git** repo。我们将通过*命令行*来完成这项工作，您所需要的就是在您的本地机器上安装 *Git 客户端*。你可以使用[这个](https://git-scm.com/downloads)或者你选择的任何其他工具

```
cd <project_folder> git init git remote add origin <developer_cloud_git_repo> 
//e.g. [https://john.doe@developer.us.oraclecloud.com/developer007-foodomain/s/developer007-foodomain-project_2009/scm/junit-sample-app-repo.git](https://john.doe@developer.us.oraclecloud.com/developer007-foodomain/s/developer007-foodomain-project_2009/scm/junit-sample-app-repo.git) git add . git commit -m "first commit" git push -u origin master  
//Please enter the password for your Oracle Developer Cloud account when prompted
```

您应该能够在您的开发人员云控制台中看到代码

![](img/e00f18c6efe9c4d1fb2781882dcb5e4b.png)

## 配置生成作业

![](img/83085cec377dd9b76ac729bcecda4996.png)![](img/f737f45962b8a6503c1a441391267424.png)![](img/398d350729087e324a0a10282ab431cc.png)

激活以下生成后操作

*   发布 JUnit 测试结果报告
*   测试报告的存档(如果需要)

![](img/b2b259b1856f8d7320c7f6bb96c6e9b1.png)

## 触发构建

![](img/2653ede2e3ee6db48aac7d73256dd6dc.png)

# 检查测试结果

构建过程结束后(在这种情况下会失败)，检查构建页面的右上角，然后单击**测试**

![](img/938d71e7faa97d14bcf352f8919ee6d2.png)

**总体指标**

![](img/c00030b992cf912bc92020f9563448ff.png)

**失败测试快照**

![](img/341945e57097ed7b330141ce4f890ddd.png)

**失败测试详情**

![](img/3430ce4b0ba165159f813b2b0c075706.png)

**通过测试的示例**

![](img/2f315bbd32f8badcfbf1d2ac60331e10.png)

**成绩历史**

![](img/333dc761d4d17c1cc9b83b3c7c5bdd5c.png)

> 本文表达的观点是我个人的观点，不一定代表甲骨文的观点