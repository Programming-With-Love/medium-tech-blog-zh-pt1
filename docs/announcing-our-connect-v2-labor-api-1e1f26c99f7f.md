# 宣布我们的 Connect v2 人工 API

> 原文：<https://medium.com/square-corner-blog/announcing-our-connect-v2-labor-api-1e1f26c99f7f?source=collection_archive---------4----------------------->

## 获取带有休息时间的员工工作时间和小时工资率

![](img/dcbf41f6cda9e466024bef527feb0484.png)

> 注意，我们已经行动了！如果您想继续了解 Square 的最新技术内容，请访问我们的新家[https://developer.squareup.com/blog](https://developer.squareup.com/blog)

我们非常兴奋地宣布 [Square Connect v2 Labor API](https://docs.connect.squareup.com/more-apis/labor/what-it-does) 的发布。了解日常员工运营和劳动力成本是管理企业的一个关键部分。它有助于企业主更好地了解他们的业务、员工的表现，并最终做出更明智、更具成本效益的人事决策。借助 Labor API，合作伙伴可以代表我们的共同客户构建更强大、更准确、更全面的劳动力管理集成。

为了突出最近实施的*(即休息跟踪、多份工资等)*的变化和附加功能，我们将工时记录卡 API 的名称更新为劳动 API。新名称更好地与我们的合作伙伴可以利用劳动力 API 实现的劳动力管理功能的广度保持一致。

您可以更轻松地管理员工的工作时间、休息时间、轮班工资以及导入/导出劳动力数据。

## **如何创建班次(包括休息和工资信息):**

*Create a new Shift that includes wage and break information*

## **如何从工作周中查询一组已关闭的班次:**

*Query a set of close shift for a given workweek*

您将在下面找到我们在此版本中实现的功能列表:

*   [**员工班次**](https://docs.connect.squareup.com/more-apis/labor/build-with-labor#open-a-new-shift) :查看/创建任何员工工作的班次，包括该班次工作的营业地点、班次的开始/结束时间以及该班次工作的正常、加班和双倍工时。
*   [**员工休息跟踪**](https://docs.connect.squareup.com/more-apis/labor/cookbook/add-shift-breaks#start-a-shift-break) :跟踪员工在一个工作班次中的休息情况，包括休息开始/结束时间、持续时间以及是否带薪。
*   [**员工工作跟踪**](https://docs.connect.squareup.com/more-apis/labor/build-with-labor#prerequisites) :查看/编辑员工在任何给定班次的工作(和工资)，包括一个员工可以有多份工作(和工资)。
*   [**搜索**](https://docs.connect.squareup.com/api/connect/v2#endpoint-labor-searchshifts) :按员工、办公地点、工作日、起止时间、状态(开/闭班)搜索班次。

如果你想及时了解我们的其他内容，一定要关注这个[博客](https://medium.com/square-corner-blog) &我们的[推特](https://twitter.com/SquareDev)账号，并注册我们的[开发者简讯](https://www.workwithsquare.com/developer-newsletter.html?channel=Online%20Social&sqmethod=Blog)！我们还有一个 [Slack](https://squ.re/slack) 社区，用于与实现 Square APIs 的其他开发人员联系和交流。