# 我计划如何从应用程序的想法到工作原型(食品技术)

> 原文：<https://medium.com/quick-code/how-i-plan-to-get-from-app-idea-into-working-prototype-food-tech-92f1fdcc432a?source=collection_archive---------0----------------------->

![](img/3c821f97be5ef547e788aecd14b13a8f.png)

from pixabay — free to use images

**此刻我所拥有的**

*   工作 API
*   我在 Ionic 上构建的应用程序示例
*   我需要有每一页的屏幕
*   我可以通过使用高级设计模板来创建某种设计。
*   项目管理计划(你看了。)
*   我有测试数据
*   注册可以通过回送 API(IBM)帮助来完成

你可以看到——很多问题以前就解决了。

## 我们需要回答这个问题

我们将如何创建、提交和托管应用程序？

我们将如何处理订阅(付款)？

决定我们是否需要有博客功能？

增加送餐服务会很酷，但是 [Instacart](https://medium.com/u/45aee787ef17?source=post_page-----92f1fdcc432a--------------------------------) 没有回复他们的营销邮件:(

我们必须为不同的烹饪、过敏(其他类型)准备过滤器。我们如何使用应用程序 UI 使它变得简单？

我在考虑为用户创造一种能力来创建和发布他们自己的烹饪书。很酷的特写，是吧？

我们能否添加一个分析工具，了解我们的用户如何喜欢或使用我们的食谱？

我们如何在应用程序中处理支持材料？

在下一个版本中，我们必须将杂货管理添加到类别中。也许增加餐具室管理

我想加一个预算规划师。重要吗，会是一个好的特性吗？

# 里程碑

0)免费食谱
1)每周菜单
1.2)杂货清单
2)认证
3)付款
4)搜索
5)导入食谱
6)完成的膳食计划软件

## 免费食谱版本

*   食谱列表(每周菜单模拟)
*   带有图像的应用程序介绍(以显示内容结束，测试它如何工作，完成 js，所以应用程序介绍只打开一次)。挑选的图像。控制器已添加。内容已被挑选。
*   没有按钮的杂货清单—长清单
*   复杂配方
*   将样式移动到适当的位置
*   为自由菜单版本添加材料设计版本
*   将 JSON 中的步骤更改为方向

## 每周一餐(阿尔法)

*   膳食计划类型(无过敏、无麸质、两人烹饪、有孩子的家庭(主要选择))
*   示例菜单(不同类型)
*   复杂配方
*   复杂的 JSON 解析
*   主 JSON 文件中的杂货清单
*   杂货清单模板 2——所有类别都在一个页面上的长滚动清单(过滤/搜索会很酷)
*   杂货清单设置和食谱日历
*   主 JSON 文件中的每周菜单
*   用图像介绍
*   食谱详细信息页的营养事实部分
*   食谱图片上传到亚马逊或免费存储或谷歌驱动器，任何快速的选择
*   启用带有助手方法的“服务”(只需实现 API 中的方法——it)
*   问题的答案—如何为 3 种不同的膳食计划准备一份食谱(配料 A1 代替 A2，配料 C 去掉，配料 D 增加 2 倍)—因此在食品清单中可能会有变化
*   单位转换+测量。将字符串“1 杯+ 1 杯”转换为“2 杯”，“1/4 茶匙+ 2/7 茶匙”转换为“15/28 茶匙”，而不是 0.3333336。
*   杂货清单可以自动计算相同的成分从一个类别从不同的食谱，从不同的日子
*   服务可以在不同的食谱，不同的日子改变，这种变化可能适用于食品清单

# 接下来重要的事情

*   数据库ˌ资料库
*   使用 save()方法的杂货清单
*   管理仪表板
*   高级搜索
*   API +完整的文档
*   开发者门户(可选)
*   客户的经常性付款(非膳食计划用户的经常性付款)
*   用户可以添加其他用户到他们的计划，这将增加成本，但也适用于一些折扣。你可以加上你的家人，妈妈。你可以用它在人们之间分享你的食谱和杂货。像 GitHub enterprise，JIRA，谷歌企业应用程序。
*   制作软件包，可以安装在不同的服务器上并在本地运行，更便宜，但没有自动更新的能力。通过 API 使用主服务器

## 服务器端(由我们的 Recipe API 服务器处理)

## 免费菜单发布(已完成)

下面我们有一个使用过的 URL(远程方法)列表

获取[http://localhost:3000/API/menu？access _ token = % token %](http://localhost:3000/api/menu?access_token=%token%)
*获取所有已创建菜单的列表(带菜谱 id)*

获取[http://localhost:3000/API/menu/593 AC 56 C2C 941720 BC 3091 b 1？access _ token = % token %](http://localhost:3000/api/menu/593ac56c2c941720bc3091b1?access_token=%token%)
*按 Id 获取一个菜单*

GET[http://localhost:3000/API/menu/last？access _ token = % token %](http://localhost:3000/api/menu/last?access_token=%token%)
*只获取一个最新发布的菜单*

获取[http://localhost:3000/API/recipe？access _ token = % token %](http://localhost:3000/api/recipe?access_token=%token%)
*获取所有已创建配方的列表*

获取[http://localhost:3000/API/recipe/593 Abe 383199170 e 50 a 5272d？access _ token = % token %](http://localhost:3000/api/recipe/593abe383199170e50a5272d?access_token=%token%)
*按 Id 获取一个配方*

获取菜谱/:id/full
*获取包含所有必要数据(如配料)的菜谱。*[*@ todo*](http://twitter.com/todo)*以后再添加类似过敏等东西。*

获取[http://localhost:3000/API/杂货/菜单？groceryId = 594d 45227741 a 0312874 c 465&access _ token = % token %](http://localhost:3000/api/grocery/menu?groceryId=594d45227741a0312874c465&access_token=%token%)

按发布日期筛选:ASC/DESC

## 仅杂货 API(作为独立项目工作— GroceriStar)

//POST[/grocerylist/department]department 化字符串列表—用于临时添加杂货列表项

// DELETE [/grocerylist]删除购物清单上的所有项目；比同步已删除项目更快的操作。

// GET [/grocerylist]获取用户的杂货清单。用户由基本身份验证确定。

// POST [/grocerylist/recipe]将食谱添加到杂货列表中。在请求数据中，传入 recipeId、scale (scale=1.0 表示保持配方与最初发布的大小相同)、markAsPending (true/false)来表示配方中的行应该标记为“待定”(用户未确认)状态。

// POST [/grocerylist/sync]同步杂货清单。通过发布到/grocerylist/sync 来调用它

// POST [/grocerylist/item]将单行项目添加到杂货列表中

//DELETE[/grocerylist/item/{ guid }][/grocerylist/item/{ guid }]DELETE 会删除此项目(假设您拥有它)。

//PUT[/grocerylist/item/{ guid }]按 GUID 更新杂货项目

## 必须创建的主屏幕(默认内容)

*   应用程序加载器
*   认证屏幕:登录/注册/密码恢复
*   社交屏幕:用户资料/资料设置/通知/联系人/订阅源
*   食谱列表和食谱详情
*   聊天列表/聊天详情/评论
*   仪表盘
*   游戏攻略
*   信用卡
*   不同的导航类型
*   设置

*继续解释应用里程碑*

2)认证

*   脸书第一
*   使用令牌的 Auth 可以在我们的 Recipe API 的帮助下处理。

3)付款

*   连接 Stripe，支持定期支付。
*   应用内购买
*   递归启用是下一个优先事项

4)搜索
部分完成，可以在我们的配方 API 的帮助下处理。

5)导入配方
考虑到这个特性。不想有很多乱七八糟的表格。如果我们能够扫描并识别文本——我会同意致力于这个里程碑。

6)完成膳食计划软件
的工作版本，不同的食品科技初创公司都有。

所以我有一个不同的选择，到底是什么应用程序创建。

它可以是带食谱的自由菜单版本。只是比以前的基于离子的版本更好。

它可以是连接到我的配方 api 服务器的注册版本

它可以只是搜索应用程序与像 Yummly，Allrecipes 大鲨鱼的 API 连接…因为我们已经完成了表格字段

它可以是一个订阅应用程序(应用程序内支付)

可以是 GroceriStar 项目的 app。

可以是一个饮食管理的 app。

你觉得做什么会比较容易:像我之前做的那样，不用设计就创建一个 app？然后创造很酷的设计。

还是拿一些 React 原生启动器带设计的预定义页面？

第二种选择会更“肥”，但它会节省大量的时间。因为我讨厌创造以前创造过的东西(很蠢)

感谢阅读。喜欢就鼓掌(可以不止一次鼓掌)。享受这种生物。

![](img/07ccdb483dde5350cd7101247e31b60b.png)

Me when i’m trying to create an app