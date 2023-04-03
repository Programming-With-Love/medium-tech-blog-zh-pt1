# Spinner: Pinterest 的工作流平台

> 原文：<https://medium.com/pinterest-engineering/spinner-pinterests-workflow-platform-c5bbe190ba5?source=collection_archive---------0----------------------->

Ace Haidrey |软件工程师，工作流；Ashim Shrestha |现场可靠性工程师，工作流程；鼎航宇|软件工程师，工作流；Euccas Chen |软件工程师，工作流程；Evan Li |工程经理，工作流程；Hannah Chen |产品经理，工作流程；雷宇李|软件工程师，工作流程

*本文转贴自作者原账号* [*此处*](/@acehaidrey/spinner-pinterestsworkflow-platform-d7621ba549f2) *。*

![](img/23199a4b1b5d431efc4d9d9ee94faf1f.png)

Workflow Scale at Pinterest Before Migration to Airflow

自创立以来，Pinterest 的理念一直是以数据为中心。作为一家数据驱动型公司，这意味着所有获取的数据都会存储起来以备将来使用。这看起来像是每天 600 的新数据，包含超过 500 的总数据。在这种规模下，大数据工具在帮助我们公司收集有意义的见解方面发挥着关键作用。这就是工作流团队介入的地方。我们帮助促进 4000 多个工作流，平均每天产生 10，000 个流执行和 38，000 个作业执行。

# 背景

回到 2013 年，Pinterest 建立了一个名为 [Pinball](/@Pinterest_Engineering/pinball-building-workflow-management-88a044c9b9e3) 的内部调度框架。该解决方案满足了该公司当时的需求，但它无法随着需求的增长而扩展，以服务于内部和外部的其他产品和服务。以下限制变得越来越明显:

*   **性能**:
    –计划/作业开始延迟时间(作业计划开始和实际开始之间的时间)高于预期。
*   **可扩展性**:
    –系统的组件是有状态的，使水平扩展变得复杂
    –中央 metastore 服务成为单点故障。
*   **维护**:
    –基础设施升级需要耗尽工作进程，设置备用主机以启动交换和其他复杂任务。
    —由于负载分区而拥有多个集群增加了升级和监控多个(10)集群的额外开销。
    —涉及到分配的工作插槽扩展，这给有状态的主节点增加了负载，从而导致延迟或瓶颈问题
*   **隔离**:
    –对用户来说，代码隔离是有问题的，因为 Pinterest 对所有 python 代码都有一个单一回购。
    –由于依赖关系的纠缠，一个错误的导入可能会中断许多工作流程。
*   **功能**:
    –缺少访问控制列表(ACL)和审计日志
    –缺少执行统计虚拟化
    –我们的系统缺少其他主要功能，如 web 服务器认证、快速导航等
*   **文档**:
    –由于该项目是内部的，除了搜索类似工作流的代码之外，缺少文档和例子。
    –拥有一个分享解决方案的社区，持续改进产品等。给用户更多的支持。
*   **测试**:
    –用户不容易使用他们自己的开发集群来进行端到端测试。

面对上述棘手问题，显然需要进行根本性的变革，这为我们在着手用另一种内部解决方案解决当前系统的问题之前探索现有的解决方案创造了机会。

# 为什么是气流？

2019 年，我们对 Spotify 的 [Luigi](https://github.com/spotify/luigi) ，LinkedIn 的 [Azkaban](https://github.com/azkaban/azkaban) 和一些替代选项做了一些初步分析，但我们最终还是采用了 Apache 的 [Airflow](https://github.com/apache/airflow) ，原因如下:

*   **目标一致**:用户要求的功能要么已经内置在 Airflow 中，要么可以添加到当前的支柱中。
*   **行业**:它被许多组织广泛采用，拥有活跃的社区承诺，引发了大量讨论，并有很好的文档供我们的用户查阅。
*   DSL :它是基于 python 的，与我们以前的系统一致，我们的用户在这里会有更少的差异。
*   **代码** : Airflow 是模块化编写的，更容易有单独的组件连接定制件。
*   **可伸缩性**:它包含无状态组件，重启后可以恢复，UI 从中央数据库中提取，并允许我们插入 kubernetes 基础设施和可分区调度程序的主要部分。
*   **声誉**:总体而言，社区似乎对 Airflow 的产品很满意

*免责声明:如果你不确定 Apache Airflow 是什么，它是一个开源框架，可以帮助调度、创作和维护工作流。你可以在这里* *阅读所有关于它的* [*。*](https://airflow.apache.org/docs/apache-airflow/stable/index.html)

# 标杆管理

为了确定 Airflow 是否能够满足我们的需求，我们使用不同的参数进行了一系列性能、负载和压力测试，这些参数包括:dag 的数量、每个 Dag 的任务数量、同时运行的任务运行和任务实例的数量、不同的并发设置以及数据库中的记录数量。这些测试的目标是模拟我们的一些生产负载来测量性能，确定需要多大程度的扩展，估计所需的集群范围，并确定我们需要解决的瓶颈和限制，以便致力于此框架。

在运行测试场景之前，我们需要对源代码进行修改，以建立一个有效的概念验证(POC)集群。首先，我们修改了气流的版本；我们从 v1.10-stable 中分支出来，并从主分支中引入了精选内容，以支持 [SerializedDAG](https://github.com/apache/airflow/blob/main/airflow/serialization/serialized_objects.py#L897) 模型。此外，由于 Pinterest 上 kubernetes (k8s)环境的不同设置，我们创建了一个经过修改的 k8s executor 来将任务提交给我们的内部 k8s 集群。此外，我们构建了一个经过修改的调度器来进行分区，并添加了额外的统计信息来记录对我们的 [opentsdb](http://opentsdb.net/) 统计信息集合的准确测量。下面显示的测试是我们为帮助我们定义性能而进行的测试之一。

![](img/0779f62bbda12e9ff6947bc8948e212f.png)

All setups were run on AWS nodes and included two UI nodes, one scheduler node (c5.2xlarge), one mysql host (1 primary/1 secondary, c5d.4xlarge), customized k8s executor with worker pods with 300 Mhz CPU, 300 MB requested memory.

进行测试后，我们确定了以下内容:

*   UI 节点性能良好，延迟依赖于不同视图上的数据库记录数量
*   mysql 并不是我们之前假设的瓶颈，因为它是一个由调度器、web 服务器和 workers 连接的单一中央主机
*   维护 1000 个 Dag 后，计划程序性能下降
*   性能测试期间检测到的瓶颈是在单个调度器处于极端负载下时 kubernetes 的提交和结果解析
*   定制的 kubernetes executor 有一个任务队列，当系统负载很重时，这意味着延迟
*   为了满足我们的调度延迟时间目标，我们必须将 Dag 的数量保持在 1000 以下，以确保单个调度器的可接受性能。因此，多调度器是必要的。

性能测试的结果让我们非常清楚地了解了系统在不同设置下的运行情况，以及需要在哪些方面消除瓶颈。

与之前的弹球系统相比，我们发现:

*   在与我们所有弹球集群相加的类似负载下，气流概念验证的调度延迟更小(50 秒对 180 秒)
*   当总负载合并到一个集群中时，该系统拥有 3000 个 Dag，每个 Dag 包含 25 个任务，延迟约为 10 分钟，是弹球的 5 倍
*   对于 95%的用户，UI 页面加载时间< 1s，模拟负载为 250 个并发页面访问，性能受分页时 Dag 数量的影响最小；对于弹球群集，延迟随着 Dag 的数量而增长

基于性能测试，我们确认，如果我们增强 kubernetes executor 以提高性能和稳定性，并将其带到生产级别的可伸缩性，同时扩展多调度程序解决方案以分片/分区负载并保持性能，那么我们可以为整个负载托管单个集群。

从用户的角度来看，我们可以自定义设置并利用现成的气流功能来解决功能需求，我们将为大部分负载提供单个集群(出于安全考虑，我们必须为 pii 和 sox 工作流提供单独的集群)，以便可以在同一个集群中搜索工作流数据。

# Pinterest 工作流系统

![](img/c5c4a3b44aad938758024e2240afed82.png)

*Spinner architecture overview*

上面的示意图显示了端到端的工作流系统。每个组件将在相关章节中进行更深入的解释。示意图中显示的外部客户端(如 EasyFlow、Galaxy、Flohub 和 Monarch)与 Spinner 交互，无需更详细地提及。

## 序列化 DAG

序列化 dag 表示很重要，有两个主要原因:

1.  性能:在同一个集群上有数千个 dag 的情况下，工作流的缓存 db 表示比为每个调用处理 Dag 文件产生了更好的性能
2.  迁移:我们需要将数千个工作流从我们的遗留弹球系统迁移到 Airflow。对于不同的 DSL，我们需要将工作流表示处理到数据库中，以支持所有的 UI 特性，如代码视图、渲染视图等。因为这些视图需要一个 dag 文件，而我们没有。稍后将更详细地讨论迁移的工作流。

## 网络服务器

这个无状态的 web 服务器将成为用户查看其工作流状态和历史的入口点。我们在群集上启用了 dag 级别的访问控制(DLAC ),并强制每个 dag 必须通过至少一个角色(如下例所示),以便只有拥有工作流权限的用户才能访问该工作流。默认情况下，我们已经设置了一个角色 spinner-users，它将被设置到所有工作流中。至少，此默认角色将拥有对每个 dag 的读取权限；这为平台维护人员提供了读取日志、查看历史等的权限。然后，用户可以创建由平台团队提供的其他角色，以分配给他们的特定工作流，并防止其他用户能够查看或对其执行操作。目前，这是一个手动配置角色的过程，我们希望能够自动创建角色并将用户分配给这些角色。

```
dag = DAG(
    dag_id='example_dag',
    access_control={SPINNER_USERS: {'can_dag_read','can_dag_edit'}},
    ...
)
```

当调度程序解析 dag 时，即当 dag 模型被覆盖时，这些权限会从 Dag 文件同步到数据库。用户还可以通过刷新 UI 中的 dag 来强制刷新。当然，如果 webserver 进程重新启动，它将重新构建 webapp，在启动时同步所有工作流权限。我们将同步添加到所有这些时间，而不仅仅是在 DagModel 更新时。

除此之外，我们还使用现成的事件日志记录，以便在用户对工作流执行操作时添加到审计日志中。这有对工作流所做的任何事情的完整跟踪历史。

最后，为了可伸缩性，我们设置了多个 web 服务器节点来托管 UI 服务，每个 web 服务器主机有 4 个线程来处理请求。当部署发生时，我们进行滚动部署，这样用户就不会停机。

## 多分区调度程序

我们的目标是拥有一个 Spinner 集群，通过一个接口来查看所有 Dag，以避免我们在旧工作流系统中拥有的多个集群的维护、开销和混乱。为此，用一个调度程序来管理所有 Dag 是不可行的。即使使用垂直扩展，我们也会遇到限制，因此增加 dag 解析并行性不是一个长期的解决方案。我们需要建立多个调度器，其中每个调度器查看不同的 dag 分区。我们考虑过 [AIP-15](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=103092651) ，但是其实施的时间框架并不符合我们的需求。因此，我们构建了一个支持我们工作流分层要求的内部解决方案。

![](img/76770f4c923bf742cf92bdf610eb5315.png)

*Scheduler partitioning flow comparing old system vs our redesign.*

为了实现这个解决方案，我们修改了调度器的实现，因为当前的调度器是有状态的，不允许同时运行多个实例。同时，我们需要防止试图安排相同 dags 任务的干扰。我们的基于规则的分割器是基于层和分区号的，这是从上面提到的 dag 位置检索的。分割器将给定的 dag 标记为特定的分区，并将每个分区分配给一个调度器来管理。这样，每个调度器将只解析、分派和维护它所拥有的 Dag 的状态，并且没有两个调度器节点会冲突。通过这种多调度器设置，我们还满足了平台要求，以更严格的调度器延迟时间 SLA 支持更高优先级的工作流，因为更高优先级的工作流将被放置在具有更严格负载限制的专用调度器分区中。

我们为每个调度程序主机配置 dag 文件夹路径，以指向特定的分区。尽管进行了分区，UI 仍将呈现所有 dag，因为该过程使用整个 Dag 路径，因此用户仍将体验单一入口。UI 仍然会显示任务实例的分区标签，并确保无论对任务采取什么动作，正确的调度程序都能够选择执行工作。

## Spinner 工作流和 Pinterest 分层

在深入研究存储库结构之前，我们需要讨论一下 Pinterest 特有的分层结构。工作流和系统根据重要程度进行标记，第 1 层为最高，第 3 层为最低。在我们的数据组织中，我们给予第 1 层工作流更高的优先级和更多的资源，这将通过存储库结构中的 dag 路径来解释。我们修改了 DAG 定义，要求传入一个 tier 字段，如下所示:

```
dag = DAG(
    dag_id='example_dag',
    tier=Tier.TIER1,
    ...
)
```

我们创建了一个新的单一存储库，供用户添加工作流，以便控制一些事情:

*   我们需要一种方法来将工作流与调度程序、web 服务器和 kubernetes pods 同步，因此从一个地方进行同步可以使构建这个过程更加简单和可控
*   我们需要建立基于层的路径，以便第 1 层工作流的用户将其工作流放入第 1 层基本路径，第 2 层放入第 2 层路径，以此类推
*   作为平台的维护者，我们需要为代码评审制定规则
*   我们需要所有代码在任何提交时都通过快速单元测试套件，以确保代码安全、代码质量和代码控制

“Dag”文件夹包含用户创作的 Dag。dag 文件夹下的子文件夹由工作流团队按照命名约定进行维护:

```
{cluster_type}_tier_{tier_index}_{partition_index}
```

下面是该结构的一个例子。

*   群集类型将是 spinner、pii、test 或 sox。
*   层级索引将如上所述:1、2 或 3。
*   partition_index 从 0 开始，并根据需要增加。该值表示当某个 cluster_type 的某个层中的 Dag 数量超过我们定义的阈值时，将创建一个新的分区，并且需要将新的 Dag 签入新的分区路径。

```
dags/
  spinner_tier_1_0/
    team1/
      my_dag.py
  spinner_tier_2_0/
  spinner_tier_3_0/
  pii_tier_1_0/
```

这个设置有助于增强我们的多分区调度器组件。这些 dag 文件中的公共模块将放在插件目录中，在所有集群/层/分区中共享。

## 持续集成和持续部署

任何工作流平台的很大一部分都是处理用户和系统级代码的部署，在 Pinterest，我们使用 [Teletraan](https://github.com/pinterest/teletraan) 作为我们的部署系统。

Spinner 的设置涉及三个不同的代码库:

1.  Spinner Repo:这是从 v1.10-stable 派生出来的 airflow 源代码，包含一个精选子集和许多内部添加。
2.  Spinner Workflows:上面提到过，但这是本机气流代码的存储库。
3.  Pinboard:这是 Pinterest 上现有的单一存储库，托管跨任何系统和平台的所有 python 代码。这里写了许多自定义函数和实现，以及所有遗留的工作流和作业。

![](img/154210a61941200bbdc99acdcfcd6f11.png)

*Schematic shows the different ci/cd pipelines.*

如图所示，基础设施代码和用户代码有单独的存储库。我们构建的映像有基础设施代码(pinairflow)的构建，还有 Pinterest mono-repo 代码(pinboard)的构建。然后，有一个单独的过程为 Dag(spinner 工作流)拉入代码，并确保最新的代码在新的运行中得到同步和执行。

该图详细概述了每个组件及其部署周期，但是关于基础设施端(UI/Scheduler/K8s worker) CI/CD 有一些事情需要注意:

*   基础设施代码的更改会触发 Jenkins 作业进行新的构建，并将新的映像推送到注册中心
*   一个单独的 Jenkins 作业通知 Teletraan 刷新节点上的映像和容器
*   kubernetes pods 将使用与启动它们来执行新任务的调度程序进程相同的映像，因此两个进程使用相同的构建
*   进行本地测试的用户将能够加载最新的映像来测试他们的 dag

关于 Dag 部署 CI/CD，需要注意以下几点:

*   用户代码更改会触发 Jenkins 作业将 Dag 同步到所有主机上的 s3
*   一个守护进程运行在 web 服务器和调度程序上，它以非常短的时间间隔(30 秒)同步来自 s3 的文件更改
*   当启动 worker pods 来执行 dag 的作业时，它们还会同步来自 s3 的最新 Dag

## Pinterest Kubernetes 执行人

在 Pinterest，我们有一个内部团队专门提供 Kubernetes 服务。因此，我们必须重新构建开箱即用的 k8s 执行器，以便与我们的设置一起工作。我们重写了所有要与之交互的 kubernetes APIs，重写了 Kubernetes Executor、Kubernetes Scheduler 等等来处理这个逻辑。它保留了开源 executor 展示的基本业务逻辑，而设置特定于我们的环境。我们将这个执行器插入到后端。

这个执行器使我们的集群能够拥有完全的运行时隔离，并根据我们的负载按需伸缩。每个任务都在自己的 pod 中运行，有一个安全的隔离设置，所有的 pod 都与 Pinterest 中的其他服务共享 k8s 节点。Pods 在任务执行后立即被删除，以释放资源计数。这与我们之前的设置相比是一个巨大的改进，因为我们提供了工作主机和固定数量的工作插槽，这使得扩展变得困难。

![](img/663a68a4d5f192d121b51ff2cce298af.png)

*The Pinterest kubernetes cluster and Spinner cluster cross communications.*

kubernetes executor 最初创建一个 pinterest watcher 来获取 pod 状态，这是 Pinterest k8s 设置特有的。我们使用标签来隔离由不同调度程序启动的 pod。这就是我们如何重建这个拉动机制。

然后，任何任务实例的 dag_id、task_id、execution_date 和 try_number 的组合会创建一个唯一的 pod 名称。可以定制 pod 来设置额外的环境变量、需要更多的资源、安装额外的包等等，以满足不同的用例场景，如下所示。

```
customized_task1 = PythonOperator(
    task_id='customized_task1',
    dag=dag,
    python_callable=print_context,
    executor_config={
        "KubernetesExecutor":
            {
                'request_memory': '350Mi',
                'request_cpu': '200m',
                'limit_memory': '1200Mi',
                'limit_cpu': '500m',
                'requirements': 'example_requirements.txt',
            }})
```

我们还在 pod 启动逻辑中构建了一个“服务就绪检查”脚本，它将在 pod 实际开始运行任务逻辑之前执行。这个就绪检查脚本将确保 s3 通信已经建立，mysql 通信可以接受，knox 检索是可能的，等等。这是因为 pod 每次都是动态配置的，需要启用这些需求才能正确运行任务。

对于定制实现，我们还需要帮助改进实时任务的日志提取机制。任务完成后，pod 中的每个任务都会将其日志推送到 s3，但在运行时，日志会同步到 Pinterest Elastic Search。当用户进入 UI 获取任务日志时，这些日志将在 pod 写入标准路径时从 pod 中获取，如果有任何问题，它将退回到从 es 中读取。如果发生故障，这也适用于 pod 就绪日志。如果没有错误，就绪日志将从 UI 上的任务日志显示中清除。

![](img/c72ac780deb53fe597973173d7e2a093.png)

*The Pinterest Kubernetes executor flow end to end.*

任务的端到端生命周期，从创建到执行和完成，以及执行者如何处理，可以在上面的图像中看到。主要组件是 K8s 执行器、K8s 调度程序和 K8s 作业监视器，它们都以蓝色突出显示。每个阶段都会带来下一个阶段。

# 用户体验

## 自定义操作器和传感器

我们的用户是我们所做的每一个决策的中心，我们的目标是以最少的工作量为他们提供最大的利益。这要求我们提供 30 多个自定义操作符。正如下面的照片所示，我们在 Pinterest 上有许多自定义逻辑，它们将被包装在一个操作符或传感器中。在内部，我们有一个名为作业提交服务(JSS)的系统，它将流量路由到执行 hadoop、spark、sparksql、hive、pyspark 作业等的内部 yarn 集群。因此，我们所有的提交操作符都需要重写，以便在我们的设置中工作。除此之外，还有封装在逻辑中的常见操作，我们需要提供与传统工作流系统同等的功能，以激励用户采用气流。

尽管旧系统和新系统之间的 DSL 有很大的不同，但是目标是尽可能地减少用户的负担，这样他们只需要输入执行期望的输出所需的参数。

我们必须克服的最大障碍之一是为 Pinterest 的单一存储库 pinboard 代码提供支持。如前所述，用户在这里有很多自定义逻辑，并且不希望将代码复制到位于不同存储库中的新工作流中。由于我们的基础设施不强制打包子模块，这需要打包整个代码库并将其添加到 python 路径中，这意味着我们需要将其烘焙到我们构建的映像中。出于多种原因，我们最初希望阻止旧代码库的导入，但这似乎是用户的一个痛点，因此我们继续将它添加到映像中，以减少他们编写原生工作流的障碍。

![](img/61d32c3c20cec4d11379420b0bc994f0.png)

*Custom operators built to support Pinterest workflows.*

测试

![](img/21d56eaa0c191e6a510a38d1f55e33e2.png)

*A prompt display once users bring up a custom testing container.*

在我们旧的工作流系统中测试工作流对用户来说并不友好。这是我们想用 Spinner 解决的问题。我们开发了一个脚本，在他们的开发主机上启动一个容器，该容器运行 airflow scheduler 和 webserver 进程，并模拟生产环境。有一些细微的差别，比如使用本地执行器而不是 Kubernetes 执行器(需要一些来自开发主机的配置)，但是体验非常相似。它还设置了一个本地 mysql 主机，供他们在自己的容器内连接，并将文件从他们的本地工作区(我们使用 Pycharm 进行开发)同步到他们的远程开发主机，以同步他们的更改并使用最新的逻辑进行测试。这允许文件被 dev 调度程序发现，用户将在 dev UI 上查看它们的 Dag。我们在用户编写工作流的同一个存储库中维护这个脚本，以便用户熟悉它。这些变化极大地改善了他们的体验和开发速度。

## 单元测试

![](img/e78a92e8c169636bd48f8ace74688b17.png)

*Flow displaying user code submission triggering unit tests and how oode gets pushed to production hosts.*

Pinterest 使用 [Phabricator](https://www.phacility.com/phabricator/) 和 [Archanist](https://secure.phabricator.com/book/phabricator/article/arcanist/) 进行代码审查和提交推送。当用户进行更改时，在它进入生产之前，必须由他们自己的团队(可能还有平台团队)进行审查，然后每个 diff 审查开始一个 jenkins 工作。jenkins 的工作启动了我们编写的自动化单元测试，它有超过 25 个以上的检查。

这些检查包括验证 dag 上存在的某些属性，如访问角色设置、层、有效开始日期、通知设置等。它们还包括我们不希望设置的设置，如 catchup 属性等。它们包括验证某些属性是否在可接受的范围内，例如 start_date、executor_config 内存和 cpu 配置以及并发设置。我们在系统方面也有限制，但我们希望在两方面都加强。还有针对 Pinterest 特定逻辑的独特单元测试。例如，我们在所有 dag 文件中防止对 Hive Metastore (HMS)的顶级调用，因此对于任何进行这种调用的函数，我们会修补常见函数并捕捉试图这样做的用户，因为这将影响 dag 解析时间，并给 HMS 服务带来问题。

当用户提交他们的变更时，这些验证测试有助于获得更高程度的信任，因为我们不会审查它们。虽然它们不能捕获所有的，但是它们确实捕获了大部分，并且帮助我们意识到共同的问题，以便我们可以添加更多的单元测试来成为一个更好的守卫。此外，用户还可以利用这个测试管道为自己的逻辑添加单元测试。

# 监视

![](img/e66fec3fd113cf9122fd8dc811f0f360.png)

*Stats dashboard to monitor health of production system components.*

在 Pinterest，我们广泛使用名为 Statsboard 的内部工具，这是一个指标可视化和警报框架。如上所述，我们有每个组件的大量统计数据，例如:

*   调度程序
*   网络服务器
*   执行者
*   应用程序接口
*   Dag 同步
*   关系型数据库
*   功能及更多

“总体”选项卡是所有集群中所有组件的汇总统计视图。我们可以查看每个集群、每个组件或更高级别的统计信息。因此，通过这种方式，我们可以控制统计数据的粒度，看是否有任何系统指标值得关注。我们对不同的调度程序进行加权平均，以表示其健康检查的重要性(即，较高层的权重较大，以及哪个调度程序的负载较高)。每个调度程序的健康检查实际上是在调度程序上运行的工作流，如果成功，调度程序将发出一个 stat。这每隔 15 分钟发生一次，因此如果成功率低于某个阈值，任何丢失的数据点都会提醒我们的团队。我们还对这一统计数据进行了延迟检查，以防我们看到备份。

收集的许多统计数据都是现成的，但是我们在自己的组件中添加了一些额外的统计数据，以便更清楚地了解系统的健康状况，因为我们希望成为一种预期服务，而不是被动服务。

# 结束语

在这篇文章中，我们希望与同行分享我们的痛点是什么，为什么我们要研究不同的产品，我们如何确定一种产品能够满足我们的需求，我们在投入太多资金之前进行的测试，以及为了实现这一点需要进行哪些修改。然后，我们会谈到我们如何帮助用户并改善他们的体验。

下一阶段是详细讨论遗留工作流向新 Spinner 平台的迁移，在那里我们承担了超过 3000 个工作流的迁移，并与各自的团队领导进行协调。我们构建了自动化工具、定制 API 和一个翻译层来处理这个问题。

我们希望这篇关于我们如何在 Pinterest 修改和部署 Airflow 的文章对你有所帮助。我们很乐意回答任何问题或评论。谢谢大家！

特别感谢 Evan Li 对本项目的管理和领导，以及 Ashim Shreshta、Dinghang Yu、Euccas Chen、Hannah Chen 和 Li 对本项目的贡献。

*要在 Pinterest 了解更多关于工程的知识，请查看我们的* [*工程博客*](https://medium.com/pinterest-engineering) *，并访问我们的*[*Pinterest Labs*](https://www.pinterestlabs.com/?utm_source=medium&utm_medium=blog-article-link&utm_campaign=haidrey-et-al-february-17-2022)*网站。要查看和申请空缺职位，请访问我们的* [*职业*](https://www.pinterestcareers.com/?utm_source=medium&utm_medium=blog-article-link&utm_campaign=haidrey-et-al-february-17-2022) *页面。*