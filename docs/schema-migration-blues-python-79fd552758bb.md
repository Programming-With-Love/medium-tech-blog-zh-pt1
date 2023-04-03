# 数据库设计变革的忧郁

> 原文：<https://medium.com/capital-one-tech/schema-migration-blues-python-79fd552758bb?source=collection_archive---------1----------------------->

## 用 Python 的调子表演

![](img/483ed7bb8ebd5ff6dbd996aca7fbba9a.png)

很久以前，我在一个酒吧乐队演奏贝斯。我们有一些歌曲之间的混合过渡，对我们来说，是标志性的摇滚。然而，对我来说，这些混合曲目令人心痛。有些夜晚会有一个微小的瞬间，当我随着一个节拍打雷时，我突然开始想我在这首歌的什么地方，接下来会发生什么。这是格洛丽亚的介绍吗？还是这就是 outro？

对我来说，更糟糕的是，在第一场演出中，每当我们重新编排曲目时，我都是一片空白。我知道我们在做什么，但是我不太能确定我在从这里到布景结束时的路标上的位置。

但这实际上是一个关于 SQL 数据库设计和 Python 如何能够拯救的故事。对我来说，Python 是一个值得信赖的键盘手，她可以把大脑中的任何键转换成我们想要的键。这是一个音乐混乱的时刻，尽管贝斯手失去了他的位置，但这一时刻却变成了正确的歌曲。

# 作为歌曲创作的 SQL 数据库

对我来说，数据库是一首正在播放的歌曲。数据库中的音乐是一个有生命的东西，表演者和观众都绑定在一起。数据库的模式是歌曲的和弦和歌词。想象一下当乐队停下来，调音，争论独奏，然后重新开始时的混乱。这对于排练来说很好(或者用 IT 术语来说，“发展”)，但这不是观众想要听到的。然而，我们可以将歌曲作为表演的一部分进行调整。

基于这个类比，我对模式变化有两种截然不同的看法:

*   这是一首**变**的歌。我们需要做好准备，平稳地转换到新的和弦进行。音乐后面有一个更长的弧线，我们现在演奏的部分适合一个更大、更复杂的作品。对我来说，这就是像 Yes 这样的老派进步摇滚乐队的长篇音乐的感觉。
*   这是一首**的新歌。我们将平稳地从老歌过渡到新歌，即使它们是不同的。这感觉就像一个即兴乐队，就像感恩而死乐队演奏混合歌曲的方式，歌曲之间有自由形式的过渡。**

有一些工具可以帮助管理模式迁移。从一个 SQL 模式迁移到一个新模式的基本概念是我们已经做了几十年的事情。我们可以使用类似于 [**Flyway**](https://flywaydb.org/documentation/commandline/migrate) 或 [**Liquibase**](https://www.liquibase.org/) 的工具来帮助管理这个。因为我是一个 Python 爱好者，所以使用 [**Alembic**](https://alembic.sqlalchemy.org/en/latest/) 来管理[**SQLAlchemy**](https://www.sqlalchemy.org/)schema 中的模式变更对我来说听起来不错。

当我们已经非常非常努力地将数据库升级脚本保持在配置控制之下时，我们能做什么…(击鼓声)，但是…(击鼓声，大和弦)我们。还是。有。疑惑？

歌曲正在播放，数据库正在使用中。鼓手刚刚意味深长地看了我一眼。一些事情应该发生，而且…我应该放下新的节拍吗？我们到底要过渡到哪首歌？

# 本质问题

是否需要从头开始重建生产数据库在很大程度上取决于使用数据库的上下文。对于可安装产品，安装脚本必须构建数据库模式的当前版本。然而，对于企业信息系统，生产数据库在任何给定时刻都是一系列模式更改的最终结果。有点像一首非常非常长的歌。在某些情况下，追溯到几年前。

我们最新的史诗是准备一个更广泛的内部发行的应用程序。这揭示了我的 sprint 团队中的成员之间可能存在的脱节。模式和脚本存储库的当前生产状态受到不确定性、恐惧和怀疑的困扰。节奏突然变得很难跟上。大多数——但不是全部——增量变更脚本都有类似的名字，遵循 **date-slug.sql** 模式。当有日期的时候，他们遵循一年一个月一天的格式。子弹往往是描述性的。但是当我们看到一些不规则的名字时，我们不确定这是否会制造出一个类似生产的克隆体。谁要独唱？如果我们在接下来的三拍中不选择，将会有一个听起来很尴尬的沉默。

有一个单独的存储库“一次性启动”脚本，但它很旧，而且似乎不完整。这个剧本把水搅浑了。我们是盲人，凭听觉演奏。数据库已经变得像一块没有归宿的滚石。我们需要详细检查 SQL，看看发生了什么。

# 蟒蛇来了

我们的 GitHub repo 中有一系列文件。因此，我们可以发出 **git log** 请求来查找每个文件的历史。这让我们可以确定文件的历史。在很大程度上，初始提交是保持事情有序的相关日期。然而，有时其他日期可能会有所帮助。

我们将定义一个方便的 Python `NamedTuple`子类来保存日期和路径。然后，我们可以提取 git 存储库中给定路径的日志细节:

```
from typing import NamedTuple
from pathlib import Path
import datetime
import subprocessclass Source(NamedTuple):
    first_date: datetime.datetime
    last_date: datetime.datetime
    path: Pathdef git_dates(path):
    “””First and last commit dates”””
    command = [
        ‘git’, ‘ — no-pager’, ‘log’, ‘ — pretty=format:%at|%s’, ‘ — ‘,
         str(path)]
    output = subprocess.run(
        command, encoding=’utf-8', stdout=subprocess.PIPE,
        stderr=subprocess.STDOUT, check=True
    )
    def parse_log(text):
        timestamp_unix, message = text.split(‘|’)
        timestamp = datetime.datetime.fromtimestamp(
            float(timestamp_unix))
        return timestamp, message
     commit_log = list(map(parse_log, output.stdout.splitlines()))
     first = commit_log[-1]
     last = commit_log[0]
     return Source(first[0], last[0], path)
```

`NamedTuple`子类 Source 是将三段数据捆绑在一起的简便方法，这三段数据是第一次提交日期、最后一次提交日期和路径。现在我们有了 Python 3.7，使用一个冻结的`dataclass`几乎和`NamedTuple`一样好。

`git_dates()`函数为一个特定的文件运行 **git log** 命令，为我们提供所有的作者日期。(%at 格式的确切定义是“作者日期，UNIX 时间戳”)输出的每一行都有两个值，由管道字符分隔。内部的`parse_log()`函数将两个值分开，将时间戳转换成适当的`datetime.datetime`对象。

创建一个解析后的时间戳列表可以很方便地将 Python [-1]用于列表中的最后一个条目，按时间顺序排在第一位。[0]是第一个条目，在模块历史中按时间顺序是最后一个。虽然按照正确的顺序请求日期可能有意义，但是日期顺序的音乐反转只是有点混乱。最后一次提交似乎反映了错误修复和拉请求终结；它似乎没有告诉我们 SQL 最初是什么时候设计的。第一次提交似乎更好地反映了 SQL 的设计。

虽然 Python 中有几个用于 git 访问的库，但我更倾向于使用`subprocess.run()`。它提供了一种简单、可移植的方式来进行这种一次性的数据收集。

# 标准化名称

各种脚本名称可以使用如下函数进行标准化:

```
pat_1 = re.compile(r”^(\d{4}-\d\d-\d\d)_?-_?(.*)$”)def preferred_name(source):
   m1 = pat_1.match(source.path.name)
   if m1:
       migration_id, slug = m1.group(1), m1.group(2)
   else:
       migration_id, slug = source.first_date.strftime(“%Y-%m-%d”), source.path.name
   return migration_id, slug.replace(“_”, “-”)
```

`pat_1`正则表达式匹配现有的文件名。文件名中混合了可选的 _ 和-字符。正则表达式正确地定位了所有的变化。我不需要一个`pat_2` ,因为，嗯，尽管这些文件看起来很奇怪，正则表达式可以匹配它们。

`preferred_name()` 功能在首选名称的两个备选项中进行选择:

*   如果文件有一个类似日期的前缀，这是值得的。
*   如果文件没有类似日期的前缀，则使用第一个 git 作者日期。

根据工作组对 git 的使用情况，其他策略在这里也有意义。尽管名字不规则，但这个特殊的名字似乎产生了正确的更新顺序。

复杂名称变化的可能性感觉就像较长歌曲中发生的巧妙的小和弦替换。一首超过两三节的歌曲通常会有一些变化。爵士乐编曲者可以用 Em7 代替 CMaj7，以避免简单的重复。在爵士乐俱乐部的环境中很有趣。建数据库的时候就不好玩了。

# 键盘独奏

当然，这还不是全部。SQL 脚本实际上是两个模式定义的一部分。没有简单的方法根据文件名进行区分。我们必须在每个文件中寻找隐藏在 SQL 中的表名。这可以分解成一对正则表达式来检查任何表名。

```
schema_a = re.compile(r“\W(TABLE1|TABLE2|TABLE3)\W”, re.M)
schema_b = re.compile(r“\W(TABLE4|TABLE5|TABLE6)\W”, re.M)
```

这两个模式成为决定哪个模式匹配的`get_schema()`函数的一部分。

这其中有些微妙之处。起初，看起来文字或注释可能碰巧具有混淆表匹配的值。通过返回每个模式中的表数来提供一点灵活性似乎是合适的。请考虑以下情况:

```
text = source.path.read_text().upper()
def to_pat(names):
   return re.compile(rf”\W({‘|’.join(names)})\W”, re.M|re.I)
pa = to_pat(SCHEMA_A_NAMES)
pb = to_pat(SCHEMA_B_NAMES)
pa_tables = set(t.group(1) for t in p1.finditer(text))
pb_tables = set(t.group(1) for t in p2.finditer(text))
```

这将从表名列表中构建一个正则表达式模式。然后我们可以使用这个模式和`finditer()`方法来定位所有的匹配。每个 match 对象的`group(1)` 方法将是表名；`pa_tables`或`pb_tables`的值是 SQL 脚本中实际命名的给定模式的表列表。

我们可以使用`len(pa_tables)` 和`len(pb_tables)`来评估可能的内容。原来我们所有的文件都是`len(pa_tables) > 0` 和`len(pb_tables) == 0`、`or len(pa_tables) == 0`和`len(pb_tables) > 0`。任何对假阳性模式匹配的焦虑都是不必要的。这些文件可以按照模式整齐地划分。

事实证明，这需要对每个模式中表名的最终集合有一些深入的了解。这是针对生产数据库元数据的查询。

在更复杂的情况下，表名可能不是唯一的。这可以产生一组更微妙的匹配规则来区分 SQL 语句序列背后的意图，在这种情况下，简单地查看表名并不是一个好的指标。我没有这个问题，所以我没有聪明的 Python 来解决它。

我的第一次乐队经历是一个键盘-贝斯-鼓组合，几乎每首歌都是一个延长的键盘独奏。优秀的音乐才能有一席之地，但也许它不属于每首歌。事实证明，我们可以通过简单的模式匹配轻松地对文件进行分区。

# 关键变化

给定模式信息后，脚本可以为每个 SQL 脚本决定首选目录和首选文件名。最终的决策看起来是这样的:

```
base = Path.cwd()
(base/”schema_a”).mkdir(exist_ok=True)
(base/”schema_b”).mkdir(exist_ok=True)sql_iter = base.glob(‘*.sql’)
sources = [git_dates(path) for path in sql_iter]
sources.sort()for source in sources:
   migration_id, name = preferred_name(source)
   folder = get_schema(source)
   if folder is None:
       print(f”*** problem with {source} ***”)
       continue
   shutil.copy2(source.path, base/folder/f”{migration_id}_{name}”)
```

该脚本对 SQL 脚本集合应用两个规则。`preferred_name()`函数为每个脚本文件提供日期和标准化的 slug。`get_schema()`函数将文件的平面列表分成两个子集。

知识库是怎么变成这样的？我想起了*是的*的《上帝的启示科学》中的歌词，“这首我们曾经如此熟悉的歌怎么了？”拉请求历史涉及到很多人，所以在遵循我们的模式变更规范方面，这是一个团队范围的差距。这是一种音乐才能的问题——我们以为我们知道这首歌，但是，不知何故，我们把它变成了不和谐的混乱。然而，一旦它被整理出来，我们就可以更有信心地继续这首歌。

现在是困难的部分。随着歌曲的发展，我们需要坚持乐队的风格。SQL 脚本需要以一种我们无需采取重大措施就能重现的方式来包含更改。我们需要更好地倾听对方。对歌曲的改变不应该被视为破坏性的:歌曲不断演变和变化，我们需要确保我们是和谐的。

# 相关:

*   [宇宙的热寂和使用 Python Dict 和 Set 的更快算法](/capital-one-tech/heat-death-of-the-universe-and-faster-algorithms-using-python-dict-and-set-f31517e7fa76)
*   Python 3 类型提示:填充还是装饰？
*   [规范到小黄瓜到代码](/capital-one-tech/spec-to-gherkin-to-code-902e346bb9aa)
*   [猛烈抨击——用 Python 替换 Shell 脚本](/capital-one-tech/bashing-the-bash-replacing-shell-scripts-with-python-d8d201bc0989)

*披露声明:这些观点是作者的观点。除非本帖中另有说明，否则 Capital One 不属于所提及的任何公司，也不被其认可。使用或展示的所有商标和其他知识产权都是其各自所有者的所有权。本文为 2019 首都一。*