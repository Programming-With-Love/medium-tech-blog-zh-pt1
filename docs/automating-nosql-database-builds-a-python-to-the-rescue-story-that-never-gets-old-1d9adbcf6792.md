# 自动化 NoSQL 数据库构建

> 原文：<https://medium.com/capital-one-tech/automating-nosql-database-builds-a-python-to-the-rescue-story-that-never-gets-old-1d9adbcf6792?source=collection_archive---------1----------------------->

## 一个永不过时的“蟒蛇拯救”故事

![](img/3132b4183b594b6cd72a267a8597a803.png)

*这种感觉*当您的应用程序如此之大，如此重要，以至于您知道存储需求很快就会急剧增长。*“这个‘庞大’的分析工具将同时受到另一项业务、一项收购和强劲的有机增长的冲击。哦，翻倍的时间缩短了一半。”*看起来有人需要建立新的数据库服务器群。

从远处看，在这种情况下提供 NoSQL 数据库服务器群的想法似乎非常简单。**分配服务器。分配存储。构建软件。完成满足企业标准、信息安全标准和生产支持标准所需的所有其他数据库配置。** 这不就是 DevOps 的意义所在吗？只是*“嘿！转眼间。数据库！”*对吧？

然而，当我们深究细节时，事情就没那么简单了。是的，这将会变成一个*“蟒蛇营救”*的故事。

## 涉及到什么？不，真的吗？

提供数据库服务器场的基本框架需要多种资源。最重要的资源是耐心。这些往往是具有大量磁盘存储的大型服务器。(30Gb 至 60Gb 内存。)对于一些数据库——比如 Cassandra——串行构建节点似乎是最明智的。在这种情况下，第一个和最后一个节点的处理方式与其他节点不同，第一个节点包含其他节点需要的种子信息。因此，似乎最简单的方法是避免构建数据库细节，直到所有节点都构建好，并且可以开始共享角色、用户和其他定义。这拖延了构建下一个版本*“cyclo pean 数据库”的时间*

从抽象的角度来看，我们将做以下事情来创建服务器场的每个单独的节点:

**1。** **从供应服务器运行一个 Chef recipe 来构建每个节点。**

**2。** **更新域名。**

**3。在任何用于跟踪资产的配置管理数据库中创建条目。**

**4。** **计划备份(如果相关。)**

这大部分只是 API 调用；它们非常简单，尤其是在 Python 3 中。厨师食谱问题是真正的工作出现的地方。我们需要在使用简单的 Chef 脚本和保持灵活性之间取得适当的平衡。此外，配方需要有足够的参数，这样我们就可以避免每次在我们构建的东西有变化的时候调整它们。

问题是环境设置的变化速度很快，因为这些不是一般的安装:它们是为我们的企业需求定制的。昨天的企业最佳实践是今天“勉强够好”的实践。我们不想不断调整主厨的食谱。一个替代解决方案是动态收集数据。但是，动态收集数据的方法意味着我们可能很难恢复动态数据来重建现有的服务器。

***中间地带在哪里？***

## 寻找灵活性

构建 Chef 食谱迫使我们看到有大量的参数驱动着 Chef。如此之多，以至于我们需要外部工具来收集数据，并用它来构建有用的东西。这些参数分为几个部分:

——**用户必须提供的东西。**应用程序名称(“Cyclopean”)。估计大小(“1Tb”)。他们的业务线和成本中心信息。显然，这推动了数据库构建请求。

- **企业需要提供的东西。**云配置详情、子网、其他公司级详情。其中一些配置细节经常变化。有 API 可以检索其中一些。在 Chef 菜谱中进行这些查找似乎是错误的，因为菜谱变成了动态的，并且失去了它的幂等性。

- **数据库工程师需要定义的东西。**命名约定、存储首选项、规模计算和其他环境细节。这可能是厨师食谱本身的一部分。

- **每个数据库产品特有的东西。**例如，定义用户、角色、权限、企业 LDAP 连接的独特方式。我们需要灵活性，让这因业务线而异。与其创建许多不同的配方，我们更愿意将它作为构建的整个参数集合的一部分。

- **简单的文字值，但会比菜谱需要更改的次数更多。**例如，分发套件的版本号将会改变，但需要参数化。厨师属性已经提供了令人愉快的处理方式。15 步属性优先表显示了配方如何提供默认属性，属性文件如何提供值，以及环境如何覆盖这些值。

我们的目标是尽量减少对主厨食谱的调整，经验表明这涉及到很多参数。对于我们的一些 NoSQL 数据库安装方法，这可能多达 200 个不同的值。

Python 为我们提供了一种从各种来源收集数据并构建必要的属性和配置选项的简便方法，以便使用相对稳定的 Chef 食谱。 ***(还记得我们在介绍中说过这会成为一个“Python 拯救”的故事吗？)*** 想法是用厨师菜谱缓存参数，让我们随时重建任意节点。拥有一个静态模板文件为我们提供了所需的幂等性。

下一个要解决的问题是设计 Python 应用程序，使其能够支持可重复但灵活的构建。

## 使用 Python 字典的天真设计

先说 Python 吧。配方的参数可以序列化为一个大的 JSON(或 YAML)文档。如果我们创建一个字典中的字典结构，Python 使得这变得非常简单，这可以很容易地序列化为 JSON/YAML 文件。(这是一个“json.dump(object，file)”级别的琐碎。)

我们如何建立这本字典中的字典？

让我们以存储定义为例。我们的主厨食谱中需要一些参数。细节包括一些计算和文字。我们可以试试这个:

```
storage = {
    'devices': [ 
        'device_name': '/dev/xvdz',
        'configuration': {
            'volume_type': get_volume_type(),
            'iops': get_iops(),
            'delete_on_termination': True,
            'volume_size': get_volume_size(),
            'snapshot_id': get_snapshot_id(get_line_of_business()),
        }
    ]
}
```

强调词**【尝试】**。

这将构建一个整洁的字典-字典数据结构。细节由获取和计算值的函数填充。为了一致性，我们甚至可以将文字值包装成函数，使所有参数更加一致:

```
def device_name():
 return ‘/dev/xvdz’
```

问题是这些函数要么有许多参数，要么被迫使用全局变量。事实证明，配置信息有许多外部来源。将它们都作为参数传递是不方便的；每个函数都需要一个配置名称空间对象。

一些计算是有状态的。举个具体的例子，考虑一个在数据中心和机架之间分配数据库节点的循环算法:每个节点的分配都会导致全局变量的更新。像这样有副作用的函数是一个令人头疼的设计问题，也是一个单元测试的噩梦。

## 声明性 Python

我们如何提供一种更好的方法来使用参数而不是全局变量？我们如何让有状态的对象填充我们的模板呢？

我们的答案是使用声明式编程风格。我们可以——不做任何实质性的工作——使用 Python 类定义创建一种特定于领域的语言。这个想法是构建懒惰对象，它将在需要时发出值。

继续以存储为例，该方法如下所示:

```
class Storage(Template):
    device_name = Literal("/dev/xvdz")
    configuration = Document(
        volume_size = VolumeSizeItem("/dev/xvdz", Request('size'),
        "volume_size", conversion=int),
        snapshot_id = ResourceProfileItem(Request('lob'),
            Request('env'), Request('dc'), "Snapshot"),
        delete_on_termination = Literal(True),
        volume_type = ResourceProfileItem (Request('lob'),
            Request('env'), Request('dc'), "VolumeType"),
        iops = ResourceProfileItem (Request('lob'),
            Request('env'), Request('dc'), "IOPS", conversion=int)
)
```

为此，细节由帮助构建 JSON 配置对象的类的实例和填充配置对象中的项目的类的实例创建。这些类有一个层次结构，提供不同种类的值和计算。它们都是基本项目类的扩展。

我们的想法是构建一个模板类的实例，其中包含所有复杂的数据，这些数据需要组装并导出为一个大的 JSON 文档，供厨师菜谱使用。微妙之处在于，我们希望保留属性显示的顺序。这不是一个*要求*，但是如果 JSON 与 Python 匹配，阅读起来就容易多了。

此外，我们需要稍微扩展 Python 的继承模型，以便 Template 的每个子类都有自己属性的具体列表，以及父属性。这也使得调试输出更加容易。

我们将调整模板的元类定义来提供这些额外的特性。看起来是这样的:

```
**class TemplateMeta**(type):
    @classmethod
    **def __prepare__**(metaclass, name, bases):
    """*Changes the internal dictionary to a :class:`bson.SON` object."""*
        **return** SON()

    **def** __new__(cls, name, bases, kwds):
    """*Create a new instance by merging attribute names.*
       *Sets the ``_attr_order`` to be parent attributes + child attributes.*
    """
    local_attr_list = [a_name
        **for** a_name **in** kwds
            **if** isinstance(kwds[a_name], Item)]
    parent_attr_list = []
    **for** b **in** bases:
        parent_attr_list.extend(b._attr_order)
    **for** name **in** local_attr_list:
        **if** name **not in** parent_attr_list:
            parent_attr_list.append(name)
    kwds['_attr_order'] = parent_attr_list
    **return** super(TemplateMeta, cls).__new__(cls, name, bases, kwds)
```

元类用 bson 替换类级 __dict__ 对象。儿子反对。(是的，我们大量使用 Mongo。)SON 对象保留了键条目顺序信息，非常类似于 Python 的原生 OrderedDict。

元类定义还构建了一个额外的类级属性 _attr_order，它提供了这个 Template 子类及其所有父类的完整属性列表。顺序总是从父属性开始。注意，我们不依赖于所有提供 _attr_order 属性的父节点；我们实际上搜索了每个父类，以确保我们已经找到了所有的东西。

模板的 substitute()方法收集所有需要的数据。我们可以在这里生成 JSON 数据，我们更愿意等到请求输出时再生成。

```
**def substitute**(self, sourceContainer, request, **kw):
    self._source = sourceContainer
    self._request = request.copy()
    self._request.update(kw)
    **return** self
```

构建数据的参数来自三个地方:包含所有各种配置文件的 sourceContainer、指定下一版本“Cyclopean”有多少节点的详细信息的初始请求，以及可能出现的任何关键字覆盖。

当模板作为用 JSON 表示法序列化的 SON 对象发出时，输出就出现了。

```
**def to_dict**(self):
    result = SON()
    **for** key **in** self._attr_order:
        item = getattr(self.__class__, key)
        value = item.get(self._source, self._request)
        i**f** value **is not None**:
            result[key]= value
    **return** result
```

所有填充属性的条目实例都有一个通用的 get()方法来执行任何计算。这也可以更新该项目的任何内部状态。模板遍历所有的条目，计算 get()。

每个 Item 对象的 get()方法被赋予配置细节。模板没有自由浮动的全局变量，而是有一个严格定义的配置细节的简短列表；这些被提供给每个单独的项目。

这避免了依赖(可能模糊的)全局变量集合。奖金！因为它们是对象，有状态计算不包括更新全局变量的可怕技术。状态可以封装在项目实例中。单元测试进行得很好，因为每一项都可以单独测试。

这给了我们一些高度可测试的东西，并不比天真的设计复杂多少。我们可以有一个稳定，简单的厨师食谱。为 Chef 准备值的所有查找和计算都在我们的 Python 应用程序中。具体来说，它们被隔离在 Item 子类和模板的定义中。

## Python 的价值

Python 为我们工作有两个原因:

**1。灵活性。**

**2。和灵活性。**

首先，我们可以灵活地修改用于 Chef 供应的 JSON 文档。Chef 脚本调试起来很累，因为每次我们测试一个脚本时，似乎都要花很长时间来配置一个节点。作为 Chef recipe 输入的文档可以通过单元测试来定义，在做出更改后，运行测试套件只需不到一秒钟的时间。每一个新想法都可以很快得到检验。

例如，考虑改变企业分配子网的方式。昨天，“Cyclopean”在一个子网上，生活很好。现在它变得越来越大，它必须被移动，数据库从网络服务器中分离出来。子网的规范从简单的 Item 子类变成了基于环境和服务器目的的复杂查找。

我们以前有这样的:

```
class SubnetTemplate(Template):
    subnet_id = Literal('name of net')
```

现在我们有了这个:

```
class Expanded_SubnetTemplate(Template):
    subnet_id = ResourceProfileField(Request('env'),
        Request('purpose'), 'Subnet')
```

然而，这一变化对厨师的食谱没有任何影响。它在配置文件中添加了一些细节，并对这些文件进行了一些额外的查找。我们可以快速设计和单元测试变更。

其次，我们可以灵活地将所有配置步骤集成到一个统一的框架中。很多工作都是通过 RESTful API 完成的。使用 Python 3 和新的 urllib 使这变得相对简单。针对不同云供应商的附加库符合 Python 扩展模块的世界观，可以解决独特的问题。

我们为此使用了一个**命令**设计模式。构建过程中的每一步都是 NodeCommand 的子类。

```
**class NodeCommand**:
    """*Abstract superclass for all commands related to building a node.
    """* **def** __init__(self):
        self.logger = logging.getLogger(self.__class__.__name__)

    **def** __repr__(self):
        **return** self.__class__.__name__

    **def execute**(self, configuration, build, node_number):
        """*Executes command, returns a dictionary with 'status', 
           'log'.

        Sources for some parameters::

        build_id = build[‘_id’]
        node = build[‘nodes’][node_number]* ***:param*** *configuration: Global configuration* ***:param*** *build: overall :class:`dbbuilder.model.Build` 
               document* ***:param*** *node_number: number for the node within the sequence 
               of nodes* ***:returns****: dictionary with final status information plus any 
                  additional details created by this command.
       """* **raise** NotImplementedError
```

NodeCommand 最重要的子类之一是 ChefCommand，它使用所有正确的参数执行 chef 供应脚本。

使用多个命令实例意味着我们可以——通过简单的导入——将许多功能打包到一个高级 Python 脚本中。集成并不止于此。供应自动化引擎通过 Flask 容器提供。import 语句让 Flask 容器向任何能够编写 curl 请求或简短 Python 脚本的内部客户提供复杂的批处理脚本功能。

## 蟒蛇来了

*那种感觉*当您的应用程序如此之大，如此重要，以至于您知道存储需求很快就会急剧增长…

我们认为我们已经找到了一种方法，以代码包的形式直接向业务线提供高级 TechOps 服务，以及 RESTful web 服务。我们认为 Python 是满足大规模构建 NoSQL 数据库的业务需求不可或缺的一部分。

我们尝试直接使用 Chef，但是我们想要比这个工具更灵活的功能。不断调整方案的想法并不像拥有一个可以可靠地重建任何服务器的工具的最佳方式。

我们试图使用相对简单的 Python 创建 Chef 参数，但是这导致了太多的全局变量和太多的显式函数参数。这成了单元测试噩梦。

喘过气后，我们意识到我们需要的是一种声明式的编程风格。我们没有发明新的 DSL，我们只是根据我们的需要修改了 Python 现有的语法。一个简单的类结构和一个元类定义为我们提供了构建配置参数文件所需的一切。

现在，我们可以为“Cyclopean”的下一个版本创建巨大的服务器群。

要了解更多关于 Capital One 的 API、开源、社区活动和开发人员文化的信息，请访问我们的一站式开发人员门户网站 DevExchange。[](https://developer.capitalone.com/)