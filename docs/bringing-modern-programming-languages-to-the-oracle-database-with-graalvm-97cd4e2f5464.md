# 通过 GraalVM 将现代编程语言引入 Oracle 数据库

> 原文：<https://medium.com/oracledevs/bringing-modern-programming-languages-to-the-oracle-database-with-graalvm-97cd4e2f5464?source=collection_archive---------0----------------------->

来自 Guess 作者[巴斯蒂安·霍斯巴赫](/@bastianhossbach?source=post_header_lockup)

本出版物的原始版本发布于[https://medium . com/graalvm/bring-modern-programming-languages-to-the-Oracle-database with-graalvm-80914 d0c 0167](/graalvm/bringing-modern-programming-languages-to-the-oracle-database-with-graalvm-80914d0c0167)

*GraalVM 是一个通用虚拟机，用于在同一个平台上运行多种高性能的编程语言。它可以很容易地嵌入到数据库等现有系统中。通过将 GraalVM 嵌入 Oracle 数据库，我们可以为事务处理、数据分析和机器学习提供许多现代语言及其生态系统。我们只需要维护一个独立于支持的语言数量的运行时，而不是为每种支持的语言嵌入一个运行时。数据类型转换或服务器内部 SQL 驱动程序等基本组件可以通过 GraalVM 跨所有语言共享。*

## 介绍

在本文中，我们将探索当 [GraalVM](https://www.graalvm.org/) 和 [Oracle 数据库](https://oracle.com/database)结合在一起时会发生什么。熟悉 Oracle 数据库的读者可能知道，可以用 PL/SQL 编写的用户定义函数来扩展它。然而，嵌入到 Oracle 数据库中的 GraalVM 允许用各种主流编程语言(如 JavaScript 和 Python)编写用户定义的函数。让我们来看看 [SQL*Plus](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/sqpug/toc.htm) 中的一个简短会话示例:

使用第一个命令，我们在数据库中创建一个名为 *hello.js* 的新的源对象。它保存了 JavaScript 函数 *greet()* 的源代码。第二个命令是在 JavaScript 的动态类型世界和 Oracle 数据库的静态类型世界之间架起一座桥梁。因此，我们可以像在 Oracle 数据库中调用任何其他用户定义的函数一样调用 JavaScript 函数 *greet()* 。例如，我们可以从 SQL 查询中调用它，如上面的示例所示。

## GraalVM——一种通用的嵌入式虚拟机

[GraalVM](https://blogs.oracle.com/developers/announcing-graalvm) 是一个通用虚拟机，在同一个平台上运行用多种编程语言(JavaScript、Python 3、Ruby、R、基于 JVM 的语言、基于 LLVM 的语言)编写的应用程序，性能很高。支持多种编程语言允许 GraalVM 在所有支持的语言之间共享基本的基础设施，如 JIT 编译、内存管理、配置和工具。在使用多种编程语言的现代项目中，配置和工具的共享带来了统一的开发人员体验。

支持多种语言并不是 GraalVM 的唯一好处。尽管 GraalVM 主要作为 JVM 的一部分运行，但它为构建用 Java 编程语言编写的本机映像提供了一个解决方案。这个[原生映像特性](/graalvm/instant-netty-startup-using-graalvm-native-image-generation-ed6f14ff7692)允许[编译一个完整的 Java 应用](/graalvm/understanding-class-initialization-in-graalvm-native-image-generation-d765b7e4d6ed)，可能将 GraalVM 支持的多种客户语言嵌入到一个原生可执行文件或共享库中，包括所需的虚拟机基础设施。GraalVM 本机映像的主要优点是缩短启动时间和减少内存占用。这使得不是用 Java 编写的现有应用程序和平台能够嵌入 GraalVM 作为共享库，并从其通用虚拟机技术中受益。

## 数据库编程

说到数据库，在过去的几十年中，SQL 已经被证明是查询数据的首选语言。事实上，基本上所有新的查询语言(例如，用于流、图形或文档处理的语言)都被设计成 SQL 的方言，这突出了它的成功。然而，一旦必须实现更复杂的业务逻辑，SQL 的局限性就达到了。数据库社区认识到了这一点，并使用[程序特性](https://en.wikipedia.org/wiki/SQL#Procedural_extensions)扩展了 SQL，允许将多个 SQL 语句和计算组合成任意复杂的数据处理工作流。最流行的扩展是 PL/SQL，Oracle 对 SQL 的过程化扩展。这些对 SQL 的过程性扩展是特定于数据库领域的。这解释了为什么与 JavaScript 和 Python 等通用编程语言的社区相比，他们周围的社区相当小。JavaScript 和 Python 开发人员比 PL/SQL 开发人员多得多。所以找 JavaScript 或者 Python 开发者比找 PL/SQL 开发者容易多了。此外，流行的现代语言伴随着公共软件注册中心中随时可用的开源库的巨大生态系统(例如，[【NPM】](https://www.npmjs.com)， [PyPI](https://pypi.org/) )。在这些注册表中，很容易找到基本上适合每个任务的高质量库。

现代通用编程语言的吸引力最终推动数据库系统支持它们。例如，从 Oracle 数据库中的 Java 到 Microsoft Azure Cosmos DB 中的 JavaScript，再到 Amazon Redshift 中的 Python。到目前为止，将一种新的编程语言与数据库系统集成涉及到嵌入一个全新的运行时，它有自己的内存管理、线程机制等等。这不仅工作量很大。它还显著增加了数据库系统的架构和代码库的复杂性。GraalVM 的多语言能力和对嵌入的支持为这个问题提供了一个解决方案。只需要嵌入一个运行时，就可以在数据库系统中实现多种编程语言的高性能实现。

## 多语言引擎

在 Oracle，我们目前正在努力将 GraalVM 嵌入到 Oracle 数据库和 MySQL 中。我们称这些扩展为多语言引擎(MLE)。在本文中，我们只关注 Oracle 数据库的 MLE。Oracle 数据库 MLE 目前还处于试验阶段。

除了将 GraalVM 嵌入到 Oracle 数据库中，我们还致力于开发一些工具来尽可能方便地使用 MLE。例如，我们目前正在开发一些工具，可以自动打包整个应用程序，并使用一条命令将其部署到 Oracle 数据库中。

另一个主要工作领域是我们在 Oracle 数据库中提供的语言。语言需要扩展才能变得有用。例如，我们需要一个可以在数据库类型和语言类型之间转换的转换引擎，以及 Oracle 数据库的 SQL 引擎和新语言的 SQL API 之间的桥梁。

MLE 提供了两种不同的方法来执行由 MLE 支持的语言编写的代码。首先，存储过程和用户定义函数可以用 MLE 语言编写。其次，提供了一个名为 *DBMS_MLE* 的新 PL/SQL 包，用于动态脚本，即在运行时定义匿名脚本并执行它们。在下一节中，我们首先讨论使用 *DBMS_MLE* 的动态脚本执行。接下来的部分将解释如何创建可以在 SQL 和 PL/SQL 中使用的用户定义的扩展。

## 使用动态 MLE 临时执行脚本

*DBMS_MLE* 可以执行 PL/SQL 中以字符串形式给出的脚本。数据通过所谓的绑定变量与脚本双向交换(输入和输出)。最后，脚本可以打印将放入数据库的[输出
缓冲区](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/arpls/DBMS_OUTPUT.html)中的消息。让我们来看一个具体的例子:

示例中的匿名 PL/SQL 块使用变量 *script_source* 来保存一段 JavaScript 代码。这个变量被传递给函数 *DBMS_MLE。CREATE_SCRIPT()* 创建一个新的动态 MLE 脚本，然后可以通过函数 *DBMS_MLE 执行该脚本。EXECUTE_SCRIPT()* 。在执行脚本之前，我们通过 *DBMS_MLE 定义并设置一个名为 *hello* 的绑定变量。BIND_VARIABLE()* 。可以在周围的 PL/SQL 程序或动态 MLE 脚本中定义、设置和读取绑定变量。该脚本使用内置的 MLE SQL 驱动程序(自动可用为 *mle.sql* )来查询 *EMP* 表中所有雇员的工资。出于演示的目的，我们为薪水创建一个简单的直方图，并将其放入输出缓冲区( *console.log()* )。在将控制转移回 PL/SQL 之前，脚本操纵绑定变量 *hello* 。然后，PL/SQL 块使用 *DBMS_MLE 提取绑定变量的值。VARIABLE_VALUE()* 函数，并将其打印到输出缓冲区。为了执行 PL/SQL 块，我们可以将它作为单个语句从任何客户机发送到数据库。例如，可以将整个块复制到 SQL*Plus 会话中，并通过输入斜杠字符来执行。

在上面的匿名 PL/SQL 块被执行后(例如，在 [SQL*Plus](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/sqpug/toc.htm) 中)，数据库输出缓冲区将有以下内容(在 SQL*Plus 中使用 *SET SERVEROUTPUT ON* 或使用 *DBMS_OUTPUT 显示)。*GET _ LINE()(检索):

当然，我们可以轻松地使用 Python 来完成同样的事情:

执行这个 PL/SQL 块会在输出缓冲区中放置以下行:

例如，MLE 中的动态脚本执行可用于[将 JavaScript 和 Python 引入 APEX](https://community.oracle.com/community/groundbreakers/database/developer-tools/multilingual-engine/blog/2018/11/02/oracle-database-apex-javascriptpython-awesome) 。

## 跨语言的组件共享

在我们的第一个例子中，我们介绍了 MLE SQL 驱动程序，并展示了如何在 JavaScript 和 Python 中使用它。它看起来像是用所用语言实现的模块，但它不是。由于 GraalVM 的多语言特性，我们不必为我们添加的每种语言实现从一种语言的 SQL API 到 Oracle 数据库的 SQL 引擎的完整桥梁，而只需完成主要部分的工作一次。简而言之，多语言特性使运行在 GraalVM 上的语言能够访问属于另一种语言的对象和调用属于另一种语言的函数。因此，我们实现了所有语言所需的基本组件，如数据转换和 MLE SQL 驱动程序，作为新的内部语言，可以直接从所有其他语言中使用。为了实现新的语言，GraalVM 提供了我们用于此目的的 [Truffle 框架](http://www.graalvm.org/docs/graalvm-as-a-platform/implement-language/)。我们只是在每种 MLE 语言之上添加了一个薄薄的特定于语言的层，以隐藏一些内部信息，并使它们看起来真正是本地的。Truffle 框架不仅允许实现可共享的组件，还允许充分利用 GraalVM 的推测性 JIT 编译器。在数据库环境中，后者最为重要，因为数据转换通常是主要的成本因素。

## MLE 存储过程

虽然在许多情况下运行用现代语言编写的脚本很方便，但对于开发大型复杂的应用程序来说并不理想。动态 MLE 需要 PL/SQL 中的框架，不能直接使用第三方库。此外，代码最好由数据库管理，类似于数据。为了释放 MLE 的全部能力，我们允许以由用户定义的函数和存储过程组成的模块的形式在数据库中持久地存储和维护用户代码。对于模块的无痛打包和部署，我们计划提供外部工具，用一个命令就能完成所有事情。

存储过程允许开发人员运行需要在数据库服务器进程中执行几个 SQL 语句的代码。这避免了数据库客户机(通常是中间件)和数据库之间昂贵的网络往返。今天，Oracle 数据库允许开发人员用 PL/SQL 或 Java 实现存储过程。有了 MLE，开发者还可以用 JavaScript 和 Python 实现存储过程。

假设我们想提高一名员工的工资，但禁止非经理人员的工资超过 10，000 美元。我们可以从更新雇员工资并返回新工资的 JavaScript 函数开始:

注意，为了提高安全性和性能，我们在 SQL 语句中使用了[绑定变量](https://blogs.oracle.com/sql/improve-sql-query-performance-by-using-bind-variables)。在这个特殊的例子中，我们通过给出一个数组值来设置绑定变量的值。这意味着数组*【raise，empno】*中的值的位置决定了它所替代的绑定变量(即，第一个绑定变量将被设置为 *raise* 的值，第二个绑定变量将被设置为 *empno* 的值)。或者，绑定变量可以通过名称设置[。](https://oracle.github.io/oracle-db-mle/js/callouts/#in-binds)

接下来，我们可以定义一个函数来检查员工是否是经理:

有了这两个助手函数，我们现在可以实现我们的业务逻辑了:

对 *module.exports* 的赋值用于将函数 *salraise()* 导出到数据库。将所有这些放在一个名为 *load_salraise.js* 的文件中，我们可以添加额外的代码来完成数据库的部署:

我们现在可以运行脚本，部署模块代码并通过 Node.js 将函数 *salraise()* 注册为存储过程:

新创建的 JavaScript 存储过程可以像任何其他过程一样被调用。例如，在 SQL*Plus 中:

## MLE 用户定义函数

MLE 还允许开发人员用 JavaScript 和 Python 实现用户定义的函数。不同之处在于，UDF 可以像任何其他 SQL 函数一样使用，而存储过程不能从 SQL 语句中调用。JavaScript 社区创建了一大套软件包。MLE 允许数据库开发人员轻松地重用来自软件注册中心(如 NPM)的代码。

例如，假设我们想从一个大型数据库表中过滤掉语法上无效的电子邮件地址。为此，我们想要重用 JavaScript [验证器](https://www.npmjs.com/package/validator)包。具体来说，我们想使用其中的 *isEmail()* 函数:

我们可以首先使用 NPM 包管理器下载验证器包:

由于 MLE JavaScript 模块总是被描述为包含所有代码的单个文件，我们可以使用 [Webpack](https://webpack.js.org/) 创建一个导出所有函数的包:

例如，下面的 JavaScript 代码存储在名为 *load_validator.js* 的文件中，可用于部署模块并使 *isEmail()* 函数可用:

然后，我们可以通过 Node.js 执行部署脚本:

部署完成后，我们可以调用如下函数:

## 结论

我们了解了多语言引擎(MLE)如何让您在 GraalVM 的帮助下在 Oracle 数据库中使用 JavaScript 和 Python，将它们巨大的生态系统引入到您的数据密集型计算中。有了 GraalVM，我们不仅可以快速地将新语言引入 Oracle 数据库，而且我们还拥有了一个高性能的推测性 JIT 编译器。它可以用来为查询的关键部分生成高效的代码，比如运行时的数据转换。

从[甲骨文技术网](https://www.oracle.com/technetwork/database/multilingual-engine/overview/index.html)下载基于 Oracle 数据库 12.2 的 Oracle 数据库 MLE 预览版。尝试多语言引擎并查看[文档](https://oracle.github.io/oracle-db-mle/)。