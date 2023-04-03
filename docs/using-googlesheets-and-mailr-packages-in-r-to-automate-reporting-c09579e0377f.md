# 在 R 中使用 googlesheets 和 mailR 包来自动化报告

> 原文：<https://medium.com/airbnb-engineering/using-googlesheets-and-mailr-packages-in-r-to-automate-reporting-c09579e0377f?source=collection_archive---------2----------------------->

波林·格利克曼

大多数公司广泛使用电子表格在内部存储、工作和共享数据。

一个重要的用途是用于报告目的。如果您是一名分析师，您经常需要每月、每周甚至每天报告销售、服务水平或营销活动结果。大多数决策者不会提取自己的数据，他们习惯于在电子表格中查看和处理这些数据。例如，在 Airbnb，我以电子表格的形式分享我们每周的绩效总结，以便我的业务利益相关者能够做出数据驱动的决策。

多年来，Excel 一直是允许人们共享内部数据的主要软件。但是使用 Excel 进行报告有其缺点:

1.  首先，手动将数据更新到电子表格中非常耗时，并且占用了分析师大量的时间。在我负责的大部分报告自动化之前，我经常花费超过 30%的时间从我们的数据库中提取数据，将更新的数据集输入到电子表格中，并等待所有计算完成。
2.  手动报告可能会导致错误。将输入的数据移动一个单元格会产生错误的结果，并会引发决策者的愤怒电子邮件！
3.  Excel 文件作为附件通过电子邮件共享。在组织中共享同一文件的多个版本并不罕见。这可能会导致混乱、沟通不畅和效率下降。

凭借谷歌文档套件，谷歌解决了第三点:让任何人都能够创建在线协作电子表格，供团队同时工作，让同事们在一个地方发表评论并相互交流，而不必来回发送不同的标记。

这篇文章提供了第一点和第二点的解决方案，帮助你减少花在报告上的时间。

r 允许报告过程的简单自动化。这意味着分析师提取数据、进行一些计算、将数据上传到一个漂亮的电子表格并通过电子邮件发送给相关业务利益相关者的任务可以在一个脚本中完全自动化。因为使用相同的脚本和相同的数据集，您可以放心地获得相同的可重现结果，而不必再担心出错。

首先，R 最重要的特性之一是它允许可再现的结果:使用相同的脚本和相同的数据集，您将自信地获得相同的结果，而不必担心出错。例如，在 Airbnb，我们的数据科学团队构建了一个 R 包，允许员工协作并共享可复制的 R 代码。更多关于 Airbnb 如何通过使用 R 包[扩展数据科学的信息，请点击这里](/airbnb-engineering/using-r-packages-and-education-to-scale-data-science-at-airbnb-906faa58e12d#.ta3j1iwrj)。

因此，无论你目前正在运行显示销售或服务水平的每日报告，这篇文章都可以帮助你提高你的生产力。

# 安装 googlesheets 包和 mailR 包

在我们开始之前，假设您已经在计算机上安装了 R 和 Rstudio，让我们安装我们需要的所有东西:

**Googlesheets 包**

如果您从未在计算机上安装过此软件包，请在控制台中键入以下命令进行安装:

```
install.packages(“googlesheets”)
```

您现在可以通过键入以下内容来加载 googlesheets 包:

```
library(“googlesheets”)
```

您现在已经准备好在 R 中处理您的 google 电子表格了！

**安装 rJava 和 mailR**

在安装 mailR 之前，您需要安装另一个名为 rJava 的包。在 Rstudio 中:

```
install.packages(“rJava”)
library(rJava)
install.packages(“mailR”)
library(mailR)
```

# 用 googlesheets 包构建您的报告

出于本文的目的，我们将使用[这个电子表格](https://docs.google.com/spreadsheets/d/1LYxV8Z324o-OALSwO2h99fUecBdb_t-8BYgBbe0G1KU/edit#gid=0)。

要授权 googlesheets 查看和管理您的文件，您首先需要键入:

```
gs_auth()
```

你将被引导到一个网络浏览器，要求登录你的谷歌账户，并授予谷歌工作表许可，以你的名义使用谷歌工作表和谷歌驱动。

现在，让我们假设您被要求在 google 电子表格中创建一份每日报告。你需要做的第一件事是获取一些数据。这可以通过网络、API 或数据库来实现。在 Airbnb，我们内部的 R 包叫做 rbnb，让公司里的任何人都可以快速访问我们的数据库，而不用离开 Rstudio 的友好环境。

出于本帖的目的，我们将使用 mtcars 数据集，我们将假设您需要每天更新的报告显示了 mpg 最高的前 10 辆汽车。

我们可以通过以下方式选择前 10 辆汽车:

```
# select 10 cars with highest mgp in order and store it in a
# dataframe called data
data = head(mtcars[with(mtcars, order(-mpg)), ], 10)
```

现在，您有一些数据要上传到您的电子表格。为此，您将首先使用 **gs_title()** 或 **gs_url()** 函数加载您的电子表格。使用**GS _ URL(" URL _ of _ your _ spread sheet ")**可能是一个好的做法，以防电子表格的名称随时间而改变。

然后，您可以使用 **gs_edit_cells()** 并用 ws 参数指定您想要更新的工作表:

```
# To load your spreadsheet use the gs_title() or gs_url() functionstop_10_cars <- gs_url(“https://docs.google.com/spreadsheets/d/1LYxV8Z324o-OALSwO2h99fUecBdb_t-8BYgBbe0G1KU/edit#gid=0")# To edit the cells of your spreadsheet with your newly pulled data
# use gs_edit_cells and specify the work sheet with the ws argumenttop_10_cars <- top_10_cars %>%gs_edit_cells(ws = “Tab 1”, input =data)
```

搞定了。你现在可以每天运行你的脚本，它会比加载你的浏览器更快地更新你的工作表！

# mailR:用自动发送的电子邮件更新你的利益相关者

报告更新后，您可能希望向查看您仪表板的人发送电子邮件。如果你每天都在运行这个脚本，打开你的 gmail 并起草一封邮件似乎是浪费时间。

别担心，mailR 会支持你的！

先去[这个页面](https://myaccount.google.com)添加一个特定的 app 密码。这将生成一个随机密码，您可以仅用于 mailR，允许您将它保存在一个脚本中，而不会暴露您的实际凭证。您也可以随时从您的 Google 帐户设置页面撤销此密码。但是要小心，如果这个密码被泄露，它可以被用来从任何地方完全访问您的帐户！因此，请确保您不会与任何其他人共享包含您的密码的脚本。

然后在你的脚本末尾添加这几行，观察其中的神奇之处:

```
# Write the content of your email
msg <- paste(“Hi team,”,””,”The mpg car dashboard is up-to-date as of”,as.character(date()),”and can be accessed here: https://docs.google.com/spreadsheets/d/1LYxV8Z324o-OALSwO2h99fUecBdb_t-8BYgBbe0G1KU/edit#gid=0","","Best,","Your name”)# Define who the sender is
sender <- “firstname.lastname@email.com”# Define who should get your email
recipients <- c(“email_of_recipient1”,
    ”email_of_recipient2",
    ”email_of_recipient3")# Send your email with the send.mail function
send.mail(from = sender,
    to = recipients,
    subject = “Top 10 cars dashboard”,
    body = msg,
    smtp = list(host.name = “smtp.gmail.com”, port = 587,
                user.name = “firstname.lastname@email.com”,
                passwd = “your_app_specific_password”, ssl = TRUE),
    authenticate = TRUE,
    send = TRUE)
```

# 附录:googlesheets 上还有一件事…

googlesheets 软件包有一个主要的限制，那就是它依赖于一个明显很慢的 Google API(见[这个](https://github.com/jennybc/googlesheets/issues/183))。因此，将大型数据集上传到您的电子表格将无法一次完成。

我发现并一直在使用的一个解决方法是批量处理我的数据，并将其成批加载到电子表格中。

您可以使用以下代码将数据分块:

```
# Chunk your data in batches of 500 rows to ease the upload
data_in_chunks = list()
batches = round(nrow(data)/500)
for(i in 1:batches){
  data_in_chunks[[i]] =  data[(((i-1)*500)+1):(i*500),]
}# Then upload your batches with a for loop
for(i in 1:(length(data_in_chunks)-1)){
  row = paste("A",as.character(2+(i*500)), sep='')
  top_10_cars <- top_10_cars %>% gs_edit_cells(ws = "Tab 1", input = data_in_chunks[[i+1]], anchor=row, col_names = FALSE, verbose = TRUE)
}
```

## 在 airbnb.io 查看我们所有的开源项目，并在 Twitter 上关注我们:[@ Airbnb eng](https://twitter.com/AirbnbEng)+[@ Airbnb data](https://twitter.com/AirbnbData)