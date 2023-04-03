# 利用 Slack 实现应用监控自动化

> 原文：<https://medium.com/capital-one-tech/automate-application-monitoring-with-slack-9e4e498652a3?source=collection_archive---------0----------------------->

## 使用 Python、Apache Airflow 和 AWS Elasticsearch 构建预警 Slack 机器人的教程

![](img/a05857943d7ad2a3fe8b9cf0a4f95483.png)

应用程序监控在维护强大的应用程序和确保客户获得积极、不间断的体验方面发挥着关键作用。虽然应用程序监控有许多不同的形式和规模，但一些策略被证明比其他策略更有效。Slack bot 是一个很好的工具，团队可以使用它来监控应用程序的健康、性能和可用性。毕竟，开发人员和团队的其他成员白天在哪里总是活跃和登录的呢？懈怠。

工作场所文化的这个方面使得 Slack 成为发送应用程序监控警报的完美候选。Slack 已经吸引了开发团队的注意力。可以利用这一事实来发送关于团队应用程序的智能的、有意义的警报，可以快速有效地对其采取行动。在这篇博文中，我将讲述如何创建一个 Slack bot 的基础知识，它将使用 Python、Apache Airflow 和 Elasticsearch 来监控您的应用程序。

# 我作为 TDP 在 Capital One 工作的经验

我是第一资本公司的高级助理软件工程师，在技术开发项目中工作。技术发展计划(也称为 TDP)是一项为期两年的轮换计划，面向计算机科学专业的应届毕业生。作为该计划的一部分，员工被嵌入到全职敏捷开发团队中，在整个企业中从事各种项目。[TDP 员工的一天](https://www.capitalonecareers.com/a-day-in-the-life-of-a-technology-development-program-associate-cul-101)可能包括参加团队的每日例会、实施新的应用功能、在午餐时间参加高管演讲活动，然后在一天结束前将该功能部署到生产中！在该计划的第一年结束时，员工轮换到一个新的团队，以便掌握新的技能，扩大他们的专业网络，并使用新技术。许多 TDP 还选择超越他们的日常职责，参与招聘工作或导师计划，如[程序员](https://coders.capitalone.com/)，这是一个旨在激励下一代技术人员的 Capital One 计划。在 TDP 的两年经历中，我利用了一个叫做 TDP 委员会的[领导机会。TDP 委员会是由 TDP 领导者共同选举产生的委员会，其任务是为 1000 多名成员组织网络和参与活动。](https://www.capitalonecareers.com/how-a-tech-internship-prepared-carlton-to-lead-cul-cdev)

我目前的团队开发并维护了一个 web 应用程序，领导层使用该应用程序来查看有关每天为我们的员工提供动力的技术的指标。在这个使用 Python、Angular、Apache Airflow 和 AWS 构建的 web 应用程序上，用户可以找到关于硬件合规性、漏洞发现和内部软件应用程序使用数据的指标。我的团队还使用 Slack bot 来监控我们的应用程序。当我们的 Elasticsearch 数据库中的数据丢失时，我们的 Slack bot 就会被触发，并向我们团队的 Slack 频道发送一条消息。此警报消息指示缺少哪些数据集，以及来自哪个区域或环境。我还开发了这个 Slack bot 的增强版，它可以计算我们的弹性搜索指数每天变化的百分比。如果与 30 天平均值相比，某个特定索引的大小增长或收缩超过设定的阈值，就会发送这些警报。这向我们的团队表明，可能存在数据重复问题或部分数据删除问题。这些警报使我们的开发团队能够在客户受到影响之前调查并修复数据问题。

# 了解不同类型的应用程序监控

在进入警报 Slack bot 的技术实现步骤之前，我们必须了解不同类型的应用程序监控是什么，以及它们为什么如此重要。

归根结底，作为工程师，我们的工作是为我们的用户提供出色的用户体验。这包括直观的用户界面、全面的用户体验以及快速且高度可用的应用程序，这意味着停机时间和延迟非常短。让我们以一个允许用户查看账户信息的银行 web 应用程序为例。开发团队可能有兴趣监控该应用程序的各个方面，以确保它在一致的基础上为客户交付价值。这些应用程序监控类别可能包括:

*   **正常运行时间和停机时间**e——开发团队可能会监控应用程序的正常运行时间和停机时间，这是指银行网站运行的频率。
*   **性能** —开发团队可能会监控应用程序的性能，这指的是用户关于其帐户的请求被返回的速度。
*   **缺失数据**答——开发团队可能会监控应用程序中缺失的数据，这些数据是指已经丢失、删除或损坏的客户帐户数据。
*   **一般日志** —开发团队可能会监控来自应用程序的一般日志，这涉及到对所有用户活动的跟踪。
*   **异常活动** —开发团队可能会监控应用程序上的异常帐户活动，这可能是安全漏洞的迹象。

所有这些都是开发团队想要监控的应用程序的一些最常见方面的例子。如果不检查应用程序的这些方面，结果可能是灾难性的。继续以银行 web 应用程序为例，下面是一些可能出错的地方:

*   **正常运行时间和停机时间** —如果银行应用程序停机时间过长，客户将无法访问其账户，也无法进行转账或查看余额。
*   **性能** —如果银行应用程序太慢，客户可能会感到沮丧，银行的客户服务热线可能会不堪重负。更糟糕的是，顾客可能会把生意转移到别处。
*   **丢失数据** —如果客户发现其银行账户中的数据丢失，这可能会对银行产生严重影响，如果这是一个普遍问题，银行的品牌可能会受到严重损害。
*   **一般日志** —如果在使用银行应用程序时没有正确保存用户行为的日志，这可能会带来法律和道德风险。虽然银行希望出于审计目的保留日志，但他们也必须牢记消费者隐私。
*   **异常活动** —如果银行应用程序没有适当的机制来监控和检测恶意活动，那么这种活动可能会在银行的监管下发生。

显然，应用程序监控是构建和维护健壮应用程序的一个绝对重要的方面。在本教程中，我们将揭示如何设置一个 Slack bot 来监视应用程序，并在检测到丢失数据时发送警告消息。

# 使用 Apache Airflow 和 Python 构建 Slack bot 的教程

我的团队为我们的 web 应用程序使用了一个技术栈，包括 Apache Airflow、Python、Angular 和 Elasticsearch。当然，所有这些都被部署到 Amazon Web Services(也称为 AWS)中，以便具有高度可伸缩性、可用性和弹性。虽然 Capital One 是一家银行，但我们的运营方式很像一家科技公司，并且已经[退出了我们所有的数据中心](https://aws.amazon.com/solutions/case-studies/capital-one/)，全力投入云计算和 AWS，

[Apache Airflow](https://airflow.apache.org/) 是一个开源的工作流管理平台，我们使用它来定义批处理过程，以提取、转换和加载数据到 Elasticsearch 集群中。 [Elasticsearch](https://www.elastic.co/elasticsearch/) 是一个 NoSQL 数据库，使用了一个被称为指数的概念，这是 Elasticsearch 中最大的数据单位。索引是文档(JSON 对象)的逻辑分区，可以比作关系数据库中的数据库。Airflow 提出了一个被称为 DAG 的概念，或[有向非循环图](http://airflow.apache.org/docs/apache-airflow/1.10.12/concepts.html#:~:text=In%20Airflow%2C%20a%20DAG%20%E2%80%93%20or,and%20their%20dependencies)%20as%20code.)，它本质上是一个工作流或您想要运行的任务的集合，它在 Python 脚本中定义。我们的团队有许多 Dag 全天运行，从不同的来源提取数据，将这些数据转换成适当的格式，然后加载到 Elasticsearch 中，以便我们的 web 应用程序可以使用这些数据。有时，这些 Dag 或工作流会因各种原因而失败。对此，我们的团队开发了一个 DAG 来检查 Elasticsearch 中所有不同数据索引的存在。如果数据丢失，就会触发警报，使用 Slack bot 向我们团队的 Slack 通道发送消息。

现在让我们深入研究使用 Apache Airflow 和 Python 创建 Slack bot 的步骤，以便在 Elasticsearch 数据库中的数据丢失时发送警报。本教程假设您已经设置并部署了 Apache Airflow 的一个实例，现在准备开始开发新的 Dag。

## 步骤 1 —文件结构

对于这个例子，下面是我们的目录结构的样子。我们有一个名为`send_slack_alert.py`的文件，其中包含向 Slack 发送警报消息的代码和业务逻辑。文件`slack_alert_dag.py`包含管道定义的代码。`slack_alert_dag.py`将最终被解释为 Apache Airflow 用户界面上的 DAG，`send_slack_alert.py`将是在工作流中执行的代码。

```
.
├── Slack Bot Project
└── src
    ├── send_slack_alert.py
    └── slack_alert_dag.py
```

## 步骤 2 —设置 DAG

```
"""Airflow DAG to run the missing index Slack alert process."""
from datetime import datetime
from datetime import timedelta

from airflow import DAG
from airflow.models import Variable
from airflow.operators.bash import BashOperator

script_dir = Variable.get("script_dir")

default_args = {
    "retries": 1,
    "start_date": datetime(2020, 1, 19),
    "retry_delay": timedelta(minutes=5),
}

# 4:00 PM EST
schedule = "0 20 * * *"

dag = DAG(
    "slack_alert_example",
    default_args=default_args,
    catchup=False,
    schedule_interval=schedule,
)

slack_alert = """
    python {{ params.script_dir }}/send_slack_alert.py
"""

t1 = BashOperator(
    task_id="slack_alert",
    bash_command=slack_alert,
    dag=dag,
    params={
        "script_dir": script_dir,
    },
)
```

在`slack_alert_dag.py`文件中，有包含我们 DAG 的管道定义的代码。该代码定义了我们的 DAG 的结构和行为，它将最终在工作流程中发出我们的 Slack 警报。

*   首先，我们声明一个变量`script_dir`,它包含 Apache Airflow 部署后脚本的目录。[气流变量](https://airflow.apache.org/docs/apache-airflow/stable/howto/variable.html)是一种在气流中存储和检索元数据的通用方法，可以使用用户界面、代码或命令行界面进行更新。
*   第二，我已经为 DAG 声明了一些参数，例如它应该重试多少次，DAG 应该何时开始，以及 DAG 应该在重试之间等待多长时间。这些参数被分配给`default_args`变量。
*   第三，我为 DAG 声明了`schedule`,并将其赋给调度变量。我希望 DAG 每天在美国东部时间下午 4:00 运行。时间以 [cron 符号](https://en.wikipedia.org/wiki/Cron)表示，并基于格林威治标准时间(GMT)。
*   第四，我定义了 DAG 的一些特征，比如它的名称、调度和默认参数，并将其分配给`dag`变量。
*   第五，`t1`是指将在此工作流中执行的第一个任务。当 DAG 运行时，它将运行由 slack_alert 变量定义的 bash 脚本，该脚本将运行我们的 Python 代码来发送来自 Elasticsearch 的任何缺失数据的 slack 警报。

## 步骤 3 —编写发送时差警报的代码

```
def main():
    generate_missing_index_alerts()

if __name__ == "__main__":
    main()
```

既然我们已经编写了定义将在 Apache Airflow 中运行的 DAG 的代码，现在我们必须编写实际发送 Slack 警报的代码，以用于应用程序监控。这是我们的主函数，程序的入口点。当 DAG 工作流启动时，将执行这段 Python 代码。程序的主要功能调用`generate_missing_index_alerts()` ，它包含程序的业务逻辑。

```
def generate_missing_index_alerts():
    """
    Generates Slack alerts for missing Elasticsearch indices.
    """
    indices_to_check = [
        "accounts_index",
        "users_index",
        "employees_index"
    ]
    elasticsearch_url = 'https://my-elasticsearch-url.us-east-1.es.amazonaws.com/_cat/indices?s=index'

    # Query Elasticsearch URL for string of all indices that exist.
    elasticsearch_indices = str(urlopen(elasticsearch_url).read())

    # Generate a list of any indices which are missing from Elasticsearch.
    missing_indices = generate_missing_indices(indices_to_check, elasticsearch_indices)

    # If there are missing indices in the Elasticsearch instance.
    if missing_indices:
        alert_message = f"Alert! The following indices are missing from Elasticsearch:\n"
        # For each missing index.
        for missing_index in missing_indices:
            # Add the index name to the alert message.
            alert_message += f"`{missing_index}`\n"

        # Send the alert message to Slack.
        send_slack_alert(alert_message)
```

`generate_missing_index_alerts()`函数是我们程序的核心，用来发出松弛警报。首先，代码查询我们的 Elasticsearch 实例的 URL 来检索所有可用的索引，并将这些数据赋给`elasticsearch_indices`变量。接下来，调用`generate_missing_indices()`函数，该函数将返回我们的 Elasticsearch 实例中缺失的索引列表。

我们将在`generate_missing_indices()`函数中传递三个要检查的索引列表:

1.  `accounts_index`
2.  `users_index`
3.  `employees_index`

这是我们的 Elasticsearch 实例中应该存在的三个样本索引。一旦我们从 Elasticsearch 实例中收集到所有缺失指数的列表，我们就可以发出一个 Slack 警报！

```
def generate_missing_indices(indices_list: list, elasticsearch_indices: str) -> list:
    """
    Returns list of indices which are missing from Elasticsearch.

    :param indices_list: List of indices to check for existence.
    :param elasticsearch_indices: String of all existing Elasticsearch indices.
    :return: List of missing indices from Elasticsearch.
    """
    list_of_missing_indicies = []

    # For each index that should exist.
    for index in indices_list:
        # If the index does not exist in Elasticsearch.
        if index not in elasticsearch_indices:
            # Add this index to the list of missing indices.
            list_of_missing_indicies.append(index)

    return list_of_missing_indicies
```

这里我们可以看到`generate_missing_indices()`函数的代码，它从我们的 Elasticsearch 实例中生成一个缺失索引列表。给定我们期望存在的索引列表和当前存在的索引列表，该函数将返回所有缺失索引的列表。一旦我们有了缺失弹性搜索指数的列表，我们就可以向他们发出警告信息。

```
def send_slack_alert(message: str):
    """
    Sends alert messages to Slack for any missing Elasticsearch indices.

    :param message: Message to be sent to Slack channel.
    """

    slack_message = {
        "channel": "#my_teams_channel",
        "username": "My Slack Bot",
        "text": message
    }

    # Messages sent to this webhook will be sent to the Slack channel
    # which has been configured.
    slack_webhook = "https://hooks.slack.com/services/my-webhook"

    # Send a POST request to the Slack webhook, which will send the message
    # to the configured Slack channel.
    requests.post(
        slack_webhook, json.dumps(slack_message).encode("utf-8")
    )
```

在这里，我们可以看到最终发出松弛警报的代码。给定要发送的消息，该函数使用 requests Python 库向 Slack webhook 发送 HTTP POST 请求。可以在 Slack 仪表板上配置 Slack webhook。当消息发布到这个 webhook URL 时，Slack 会将消息发布到您选择的已经配置的 Slack 频道。

## 步骤 4 —测试应用程序监控警报是否已发送！

![](img/db1585470c077fe422cbbff24a8810d3.png)

现在我们已经实现了所有的代码，我们可以看到 Slack bot 已经发送了一条 Slack 警报消息！在我们的例子中，Elasticsearch 中似乎缺少了`accounts_index`索引。多亏了我们的 Slack bot，我们才能够在客户受到影响之前发现并修复这个问题。

# 结论

正如我所描述的，应用程序监控在维护一个健壮的应用程序中起着关键的作用。敏捷开发团队可能会对应用程序的许多方面感兴趣。这些包括监控停机时间、性能、丢失数据、一般日志和异常活动。如果应用程序的这些方面中的任何一个不受监控，那么该应用程序的最终用户就会陷入混乱。因此，开发团队必须将系统放置在适当的位置，以便尽早并经常对这些项目发出警报。实现这一点的一个很好的策略是创建一个 Slack bot，它向团队的 Slack 通道发送关于应用程序的监控警报。团队成员在一天中与同事交流时已经进入了松弛状态。通过将智能的、可操作的和信息丰富的应用程序监控警报交织到现有的空闲通道中，开发团队可以及时地采取行动。在本教程中，我解释了如何使用 Python、Apache Airflow 和 Elasticsearch 建立一个这样的应用程序来监控 Slack bot。然而，我列出的原则几乎可以应用于任何技术堆栈或应用程序。企业环境中开发团队的责任在不断增加。对团队来说，将尽可能多的自动化措施落实到位是有利的。通过设置一个全天 24 小时监控应用程序的 Slack bot，开发团队能够节省时间并处理真正有趣的任务。但是，一旦发出警报的 Slack bot 检测到有关应用程序的异常，开发团队可以立即得到通知，并立即采取行动解决问题。

*披露声明:2021 资本一。观点是作者个人的观点。除非本帖中另有说明，否则 Capital One 不隶属于所提及的任何公司，也不被这些公司认可。使用或展示的所有商标和其他知识产权是其各自所有者的财产。*

*最初发表于*[T5【https://www.capitalone.com】](https://www.capitalone.com/tech/software-engineering/how-to-automate-application-monitoring-slack-bots/)*。*