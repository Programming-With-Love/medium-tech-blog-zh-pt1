# Mendix 工作流程——概述

> 原文：<https://medium.com/mendix/mendix-workflow-an-overview-a97aadf7cbc1?source=collection_archive---------1----------------------->

![](img/38e205b2316576db8d148bb2f536c7f3.png)

Mendix Workflow — An Overview

# 总的来说，业务流程管理(BPM)是一个重要的企业应用程序，它可以帮助组织减少在纸上完成的手工工作，将所有这些文字、excel 文档和 pdf 批准表格转换为数字格式。它使的用户能够维护、跟踪和轻松交付高质量的输出。今天，我将演示 Mendix，这个低代码平台，有能力创建一个 BPM 流程。

> D ***下载源代码*** [***这里***](https://github.com/qntbkk/StaffOnBoard)

[](https://github.com/qntbkk/StaffOnBoard) [## GitHub - qntbkk/StaffOnBoard

### 此时您不能执行该操作。您已使用另一个标签页或窗口登录。您已在另一个选项卡中注销，或者…

github.com](https://github.com/qntbkk/StaffOnBoard) 

让我们从 Mendix 版本 9.6 中的员工入职流程示例开始。

首先，我要感谢 Mendix 学院在工作流方面的学习之路。我写这篇博客是为了解释更多的细节，并提供对 Studio Pro 版本 9.6.0 中工作流引擎的新更新的见解。所以让我们开始吧。

请 [**下载 Mendix Studio Pro 9.6.0**](https://docs.mendix.com/releasenotes/studio-pro/9.6#960) ，如果您遇到任何问题，请查看本[指南](https://docs.mendix.com/howto/general/install)以排除安装故障。

# 步骤 1—创建项目

当您打开 studio pro 9.6.0 时，您将看到欢迎页面，其中包含您的项目或应用程序，如下图所示。

![](img/4abe9eb18b6f7d106034cd98f77d1450.png)

选择靠近应用程序屏幕左侧的“创建新应用程序”按钮。然后选择“空白 Web App”。

![](img/7098a299b9fe7de9e26405c0601388aa.png)

按下“使用此起点”按钮。

![](img/1997b32b2301fbf07cd75cf6e23337cf.png)

将应用程序命名为您想要的任何名称。我把它命名为“StaffOnboard”。

![](img/4c5747f7d730907ee752fe460ca846a0.png)

第一次打开项目时，初始化 team server 和免费云环境可能需要几分钟的时间。如果您想节省时间，您可以在上图中的第二个红色框上将“启用在线服务”设置为“否”，它会将您快速跳转到项目，因为不会有云创建活动，并且该项目将仅在本地运行。您应该会看到如下图所示的屏幕。

![](img/6e511cd701f775baeb6d596941f64224.png)

# 步骤 2—添加额外模块

该项目需要“工作流公共”模块。要下载，请遵循以下 3 个简单的步骤:

1)转到 Studio Pro 内的 Marketplace 中的“Workflow Commons”并点击“下载”。

![](img/bfddcb8b78a3aaa1dcfd46b3eca1c7e1.png)

2)确认导入。

![](img/a9bea1c7c07a88438f01d2c977d38f4f.png)

3)点击“确定”确认导入

![](img/652438681c3fb9a7cd963a8e4b3b20ee.png)

当一切完成后，您可以看到该模块将被放置在 marketplace modules 下。

![](img/706811b1722b42acaa58eb4ad092c838.png)

# 步骤 3—更改您的安全设置

将您的应用安全模式更改为生产。

![](img/57c623a0e6f6cedb262efcff2e50a5a2.png)

在“用户角色”选项卡上，我们需要为工作流程添加几个角色。按照以下 4 个步骤添加另外 4 个角色。即设施、人力资源、经理、工作流管理员。

![](img/9e7775ca58deee6c0baa50832c5699b9.png)

按照以下 3 个步骤配置协作室用户角色:

1)在用户角色下，单击新建。

2)将新角色命名为“工厂”，然后单击“模块角色”上的编辑。

3)将新角色指定为所有模块的“用户”，但在主模块中除外，在主模块中，他们应被指定模块角色“设施”。

![](img/d89d72da5d97e36cdbec1da8e681a547.png)

继续 HR 角色。

![](img/4bfac80c2147bffb48ae57f4bdca6fab.png)

继续经理角色。

![](img/fb646f0fede8b2d70b16fdff11972a97.png)

最后一个是 WorkflowAdministrator 角色。

![](img/5fe0ef46f604fdf333ebaf603192fa34.png)

回到“模块状态”选项卡，模块“数据小部件”有一个实体访问通知。按照以下 6 个步骤解决通知问题。

1)在“模块状态”下，选择 DataWidgets 模块并双击“实体访问”

2)一旦进入实体内部，点击"新建"

3)在“规则适用于以下模块角色”下选择“用户”

4)将“允许创建新对象”和“允许删除现有对象”设置为真，并将新成员的默认权限设置为“读、写”

5)点击"全部设为"进行读取，写入

6)单击“确定”

![](img/c5350ac0c3198e264bc961fcc58de686.png)

当一切都完成时。您应该会看到类似下面的内容。

![](img/597a29113df38bd3b32dabb74d5582b6.png)

最后，按确定。

# 步骤 4—更改模块名称并创建实体

现在我们可以将模块“MyFirstModule”重命名为“StaffOnBoard”。

![](img/172c7f3d853d7c0424c09125e842f2ab.png)![](img/9b4e79ccae9155aa8189d9f78ea85b45.png)

在“StaffOnBoard”模块中，我们需要在域模型中创建几个新的实体。

**从名为“StaffOnBoard”的主表开始**

![](img/c7da31354d9078568a719006f63b8c83.png)

ENU 笔记本电脑模型

![](img/f1dc343ce42d4a70b4ffd8eb9596c88a.png)

ENU _ 手机模型

![](img/690abe45a2d43a5d46be8eab2df930cb.png)

StaffOnBoard 实体访问设置

![](img/7e7dd3d835fce5faf8aa986df7e07ffb.png)

**我们需要创建另一个名为$WorkflowInstance 的实体，它将被系统实体$Workflow 泛化。$Workflow 是一个核心 Mendix 实体，当用于创建从它继承属性的对象时，它拥有创建工作流所需的一切。这一切分两步完成，我将在下面展示。确保您与您的 StaffOnBoard 实体建立了一对一的关联。**

1)在实体的属性中，单击选择通用化。

2)选择工作流程并点击确定

![](img/d92c63d260c485dfe018ca5e9bd105df.png)

将与工作流实体交互的所有用户角色都需要对 WorkflowInstance 实体具有读写权限。

![](img/558090a980dbf08534a93d3fe2995d11.png)

现在，需要定义设施项目。在某些情况下，它应该与采购订单流程相联系。一旦确定了人数，这些采购订单应在新的人数审批流程开始之前触发或变为可用。你可以接受这个想法，并以你自己的方式继续发展。对于这个场景，每当 HR staff 用户触发新的入职流程时，我将自动添加 Facilities 项目。

**设施实体。**

![](img/43fdf79cd73476ee23f6bda9eee31d8b.png)

ENU _ 项目类型

![](img/6b2b52d6045e5cbfe46f74cf6254e0e8.png)

设施实体访问设置。

![](img/07dd9079b9b78a70e6a76f26827154a8.png)

设施项目页面概述

![](img/9555fd284412d66717db8535cec59726.png)

几乎完成了所有的实体。我们还需要另外 4 个实体来配合工作流中的用户任务。

**实体需要指定新员工可以获得的设备。**

![](img/8f6f1d0b4448d91ad4dd86db15dcd10b.png)

ManagerSpecifyDevice 任务的实体访问权限

![](img/0a77e9f3dfc1860991425033bc5dc11e.png)![](img/5ebb7f38a4f202e33c3d4921620f399d.png)

我们还需要指定员工的工作地点。

![](img/455b5eacd390c70995f8c767a9eff032.png)

实体访问设置

![](img/38d1a86645fb920f8579b2cb244000be.png)

**我们必须为员工分配和准备一张桌子。**

![](img/92b642b779e76707a8f55134bcb72578.png)

实体访问设置

![](img/75cdb4935a5eecaa7ddade3ce6656807.png)

**我们还可以为他们的移动设备添加一个实体。**

![](img/5ce305b9933921a902532a4919e8db8d.png)

实体访问设置

![](img/93167ef77c4e9e4e9bf53b0bf2460d52.png)

这就是我的领域模型最终的结果。

![](img/40929b4438e7bfa07e3e892804b88c10.png)

# 步骤 5 —创建页面

**要继续，我们需要为人力资源人员创建一个页面，以便能够添加新员工并启动入职流程。**

![](img/d8f358a51de37c007c6948f36506cb42.png)

我创建了一个像这样的页面，要做到这一点，你可以按照以下步骤

![](img/aab518f8f65fa92c312c44f854d4a298.png)

1)添加构造块“headerhero”，布局列是自动填充的

![](img/e874bfa80e4655003b9fe65097d84c0b.png)

2)将按钮添加到“创建对象”,然后如下所示进行配置

![](img/c3c328f3b6371aae7c9165abac650f03.png)

编辑页面应该是这样的

![](img/db3501a5ead4c9a747aa7fb2fddd1f2e.png)

访问该页面的角色应该是 HR。

![](img/0763e9f0d94f5d2ccff90b1d4a723c8c.png)![](img/eb6f66f0a822c787e1efaa5947377f82.png)

3)列表视图

显示新员工的全名，并允许人力资源部通过“开始入职”按钮开始入职流程。

![](img/0a845c2ff02c905271f6011576074331.png)

**要创建工作流程，只需右击模块 StaffOnBoard 并选择“添加工作流程”。**

![](img/d492e1655d6806bf3aa14acbf39a4617.png)

# 步骤 6-触发工作流

**从 Start Onboarding 按钮，它将触发一个微流。这个微流需要检查手头的设施。如果设施是空的，it 部门将不得不增加一个，以确保新员工能够成功地保留他们工作所需的物品。**

让我们看看整个微流程

![](img/f2ea34508d7f80f9d2608e30804d8be8.png)

1)检查电话是否可用，如果是空的，创建一个新的。如果不是空的，再加一个。

![](img/f63bf4b346ce282bf0e7f898c790a009.png)

2)与步骤 1 相同，但检查笔记本电脑和办公桌。

![](img/0aae270c1ef6e64180d2db5d4887b9d3.png)

3)调用工作流，检查工作流的状态并显示名称。这是开发人员检查工作流状态非常重要的一点。如果是“进行中”,则表示工作流已启动，并已到达第一个用户活动。

![](img/40edfc11826dbe06a9509613c8b33daf.png)

**现在我们可以在工作流编辑器中查看工作流的草稿**

![](img/05ba6bef0675cc6ebbb5192562508c83.png)![](img/08ee653dd1599286d1776407b045bf81.png)

一个工作流需要添加 **3 个重要项目**。首先，**工作流实例**我们已经在开始做了。第二个是 **workflowcontext，它是 StaffOnBoard 实体**。第三，**管理页面**。

![](img/68b18f462096db00a67da0267ad84d0e.png)![](img/ae68511d3683d95260f4f7278432c495.png)![](img/e65a0737e1c0215dc830e765028d55d6.png)![](img/956fdd7bfad418a20650a30b1842766d.png)![](img/c7a3e6a650219fbc6125a0e62d5f89b5.png)

# 第 7 步—创建工作流

***1)HR 启动入职后，创建一个经理任务，为新员工指定一个设备。***

![](img/cebfb8460a6b8c4ae69364a5dce597d0.png)

在下一个选项卡上，显示信息。您可以选择创建新页面

![](img/26a2c9268c1c1987e6b90a0c2360e837.png)

将页面命名为指定设备，并选择布局类型用户任务扩展。

![](img/a790abd18a74bb2132129bcad466fa0a.png)![](img/274f7daa781afd0d35d73a997d346f1e.png)![](img/e3203ab60fd257b60c14ab7a28e35d3d.png)

“数据”选项卡用于保存用户任务信息，需要对 System.WorkflowUserTask .进行概括，我们之前已经完成了。

![](img/3d0b1c7c963dce19aa0df18d3e4da7e0.png)

当所有配置完成后，错误应该都消失了。

![](img/665aabf83747212c85b349543cdf7b28.png)

***2)创建经理任务，为新员工指定一个位置，以便进行设施管理。***

![](img/8f90780f7eea73e2fe23ba64bdafe1bc.png)

您可以创建一个新页面，就像我们为指定设备所做的那样

![](img/0416b644000dc0dd44ab68e28c14afd4.png)![](img/4eb4a02f05849fdc68aa3e7469eb4bad.png)

同样在“Data”选项卡上，我们将链接到我们在前面的步骤中定义的一个实体——ManagerSpecifyLocation

![](img/31310428f409b171ceaf06557c1ab2c9.png)

到目前为止，这就是我们现在所拥有的:工作流中的 2 个用户任务

![](img/64bd4ff5d87bf921a555a562b0fd9e2c.png)

现在，在经理定义了员工应该在哪里工作之后。工作流程将会分成两个不同的方向。如果用户在家工作，那么设备将发货，我们将扣除设施项目。另一方面，如果工作人员需要在办公室，则应准备好办公桌。

***3)如果工作人员决定在家工作。***

选择属性后，分割的结果将包含 true 或 false。

![](img/a86f29ae6172fb241b254c5c5dc29cb0.png)

***4)如果他们在家工作，则运送设备。***

配置应该类似于指定设备和指定位置。然而，该活动的所有者是工厂员工，不再是经理。

![](img/1abaeabee8684f4d2857632f90e178ad.png)![](img/eea1ade18a693ca70212d34bbc463191.png)

不允许工厂员工通过禁用数据视图的可编辑性来调整信息。

![](img/a98b894527482ee7b27609029d942c39.png)

数据被映射到从 System.WorkflowUserTask 概括的实体。

![](img/9486aace383bc831f22bd0e045cdb55d.png)

如果他们将在办公室工作，准备好书桌。

![](img/b5c6f30fde1032776bac90246761411f.png)![](img/19b6f07166ebf430e6573a5f1e6f4081.png)

同样，我们不允许编辑表单数据。

![](img/c24a2b0b2a4e59846a66ce870677d4a1.png)![](img/7e5138037f681fe269ee3ec4b32ee9a8.png)

到目前为止的工作流程。

![](img/80f67f72c6070adeb34dba5bc07ae2a9.png)

***6)船舶设备逻辑。***

当我们装运设备时，应扣除设备项目。工作流允许我们在用户任务活动之后进行微流调用。

![](img/afa2b731316ca36a239afb18db85bed4.png)![](img/ee364378e597d27b7220dc4121d702f3.png)![](img/858391376317dded5e9763d4bf7b0070.png)

***⑦准备案头逻辑。***

![](img/f5d81d7422ee508e0ef3bb1c4f255e37.png)![](img/c09c9204e42aa5fb37e163a8865ff85f.png)![](img/f0740c4974e328284f2a4918d9b397d2.png)

# 步骤 8 —最终项目设置(演示用户、页面访问和导航)。

为了测试这个项目，我们需要设置一些演示用户。

![](img/c279971640493a006215cbf179ca3faa.png)

所有用户角色的页面访问权限

![](img/d4ad377ebaa9b77f4649af4f93457419.png)

转到导航并为每个角色添加主页，如下图所示。我们可以使用 workflow commons 模块中的页面，以及我们之前制作的页面。

![](img/fdaf99fa46c4e61eb05749ded9065fc6.png)

将默认主页更改为 StaffOnBoard 页面。

![](img/3a340aae3dc5620562004256c7b8ca0a.png)

更新菜单，使其类似下图

![](img/83dc43495b57dd7052a88778b3d7d0e8.png)

一旦你完成了，你就可以运行你的应用程序并测试它，下面是我测试应用程序的录音

# 演示视频

# 来源

[](https://github.com/qntbkk/StaffOnBoard) [## GitHub - qntbkk/StaffOnBoard

### 此时您不能执行该操作。您已使用另一个标签页或窗口登录。您已在另一个选项卡中注销，或者…

github.com](https://github.com/qntbkk/StaffOnBoard) 

## 阅读更多

 [## 更改工作流状态- Studio Pro 9 指南| Mendix 文档

### 此活动只能在微流中使用。这个活动是在 Studio Pro 9.6.0 中引入的。变更工作流程…

docs.mendix.com](https://docs.mendix.com/refguide/change-workflow-state)  [## 工作流活动- Studio Pro 9 指南| Mendix 文档

### 本文档中描述的活动位于工具箱的工作流活动部分。以下是…

docs.mendix.com](https://docs.mendix.com/refguide/workflow-activities)  [## 工作流共享-市场指南| Mendix 文档

### 工作流公共模块为想要构建工作流的用户提供了现成的入门体验…

docs.mendix.com](https://docs.mendix.com/appstore/modules/workflow-commons) 

*来自发布者-*

*如果你喜欢这篇文章，你可以在我们的* [*中页*](https://medium.com/mendix) *找到更多喜欢的。对于精彩的视频和直播会话，您可以前往*[*MxLive*](https://www.mendix.com/live/)*或我们的社区*[*Youtube PAG*](https://www.youtube.com/c/MendixCommunity/community)*e .*

*希望入门的创客，可以注册一个* [*免费账号*](https://signup.mendix.com/link/signup/?source=direct) *，通过我们的* [*学苑*](https://academy.mendix.com/link/home) *获得即时学习。*

有兴趣加入我们的社区吗？你可以加入我们的 [*Slack 社区频道*](https://join.slack.com/t/mendixcommunity/shared_invite/zt-hwhwkcxu-~59ywyjqHlUHXmrw5heqpQ) *或者想更多参与的人，看看加入我们的*[*Meet ups*](https://developers.mendix.com/meetups/#meetupsNearYou)*。*