# 面向实时 Hadoop 应用的 Lambda 架构

在仔细评估了各种选项后，我筛选出了 `Spark SQL` 和 `VoltDB`。然而，我很快意识到 `VoltDB` 无法从 Hive（或 HDFS）读取数据，因此将其排除。虽然在比较这两个选项时，我发现 `Spark SQL` 自身也存在一些缺陷，并且，尽管这些缺陷与我举例说明的场景关系不大，但在某些情况下可能至关重要。它们是：

- `Spark SQL` 不支持事务。
- `Spark SQL` 的 DataFrame 不支持索引。索引有助于提升随机读/写性能。
- `Spark SQL` 本身不含存储功能，因此你需要使用外部数据存储。

我在第 5 章的“[查询工具](http://dx.doi.org/10.1007/978-1-4842-1287-5_5)”一节以及“Spark SQL”小节中详细讨论了 `Spark SQL`。这有助于你理解 Spark 架构以及 `Spark SQL` 在其中所处的位置。现在，让我演示如何将 `Spark SQL` 用作速度层。我已经演示了如何为跟踪信息系统构建批处理层和服务层。现在我将构建一个速度层。

Lambda 架构将速度层定义为由批处理层尚未处理的记录组成。因此，第一步需要确定哪些是尚未处理的记录。你或许还记得，在构建批处理视图后，会向 `BatchProcHist` 表中插入一条历史记录。因此，批处理视图的最新记录可以告诉我们最近一次构建的日期/时间，从而有助于确定哪些是未处理的记录。由于 Hive 不支持将查询结果赋值给变量，我将把第一个批处理视图的最新记录写入一个表：

```sql
Create table MaxTable as select ViewName, max(CreatedAt) as MaxDate from
BatchProcHist group by ViewName having ViewName = 'ProfLFiveView';
```

但是，如果在此批处理视图构建之后，速度层已经处理了主数据集中的任何新记录，会发生什么？嗯，你需要检查这一点，并仅考虑未处理的记录来更新视图（此视图现在是注册为表的 Spark DataFrame）。我将速度层视图称为 `ProfLFiveView_S`。与批处理视图类似，速度层视图也会写入审计表 `BatchProcHist`。因此，我将把第一个速度层视图的最新记录写入同一个表（即我之前捕获第一个批处理视图最新记录的表）：

```sql
Insert into MaxTable select ViewName, max(CreatedAt) from BatchProcHist
group by ViewName having ViewName = 'ProfLFiveView_S';
```

现在，我只需要确定这些记录中哪一条是最新的，并以此为基础来处理第一个速度层视图的记录：

```sql
Create table MaxTbl1 as select max(MaxDate) as MaxDate from MaxTable;
```

最后，从主数据集中获取未处理的记录，并创建速度层视图。同时，添加时间戳并向审计历史表中写入一条记录：

```sql
Create table Profitemp1_S as Select a.ProfLastYear, a.AsOf, a.Corporation
from CorpProfitDetails a, MaxTbl1 b where a.AsOf > b.MaxDate;

Create table ProfLFiveView_S as select Corporation, sum(ProfLastYear) as
ProfLastFive from Profitemp1 group by Corporation

INSERT INTO TABLE BatchProcHist
VALUES ('ProfLFiveView_S', from_unixtime(unix_timestamp());
```

此时可以删除临时表 `MaxTable`、`Profitemp1_S` 和 `MaxTbl1`。另请注意，在创建视图 `ProfLFiveView_S` 时，我并没有使用 `having sum(ProfLastYear) > 0` 这个条件。原因是这个总和仅针对过去五年盈利的公司的那些新记录。要获取最新的总和，需要将其与批处理层的总和相加，更新的利润将自动确定（新记录的总利润或亏损将由正数或负数来体现）。

假设速度层视图每周构建一次。那么，当你需要更新速度层视图以适应新数据并移除已属于批处理层视图一部分的数据时（如果批处理层视图在此期间被重建），会发生什么？

有两种可行的方法：

-   仅考虑未处理的记录来重建速度层视图（就像我们第一次构建的那样）。
-   在第一轮处理之后不要删除临时表 `Profitemp1_S`，而是从中删除已过期的记录（这些记录已是重建后的批处理视图的一部分）并添加新记录。然后，只需执行聚合操作并重建速度层视图 `ProfLFiveView_S`。

请记住，要执行删除操作，你需要更改 Hive 的 CRUD 支持配置，并为表 `Profitemp1_S` 启用事务支持（在批处理层视图部分已讨论过）。

我会根据速度层视图的大小来决定选择哪种方案。如果视图不算特别大并且可以快速重建，我会选择重建方案。然而，如果视图很大，需要大量的处理和时间来重建，我会启用事务支持，只需向临时表 `Profitemp1` 中添加新记录并移除已处理（由批处理层处理）的记录。可以使用相同的逻辑（如本节前面所述）来获取批处理和速度层视图的最新处理日期/时间，然后根据需要从主数据集中检索未处理的记录。在调整了 `Profitemp1` 表中的记录（删除过期记录并添加未处理记录）之后，即可执行聚合操作以构建更新后的视图。

另一个需要考虑的关键点是速度层处理对批处理层处理的依赖。至少，你需要过期或移除已被批处理层处理的速度层记录。当然，你也可以重建或向速度层视图中添加任何新记录。

接下来，我将讨论为第二个批处理视图构建速度层视图。你可能还记得，这涉及计算平均值（过去五年平均客户支持人员数量等于或超过 1,000 的公司）。我将讨论如何为对应的速度层视图计算更新后的平均值。

作为第一步，我们获取视图 `AveCS1000View` 的最远处理时间（批处理层和速度层）：

```sql
Create table MaxTable as select ViewName, max(CreatedAt) from BatchProcHist group by ViewName having ViewName = 'AveCS1000View';

Insert into MaxTable select ViewName, max(CreatedAt) from BatchProcHist
group by ViewName having ViewName = 'AveCS1000View _S';
```

现在，我只需要确定这些记录中哪一条是最新的，并以此为基础来处理第一个速度层视图的记录：

```sql
Create table MaxTbl1 as select max(MaxDate) as MaxDate from MaxTable;
```

最后，从主数据集中获取未处理的记录，并创建速度层视图。同时，添加时间戳并向审计历史表中写入一条记录：

```sql
Create table CustSuptemp1_S as Select a.CustSuppStrength, a.AsOf,
a.Corporation from CorpCustSupDetails a, MaxTbl1 b where a.AsOf > b.MaxDate;

Create table CustSuptemp2_S as select Corporation, sum(CustSuppStrength) as TotalCustSup, count(CustSuppStrength) as CountCustSup from CustSuptemp1_S
group by Corporation;

Create table AveCS1000View_S as select a.Corporation, (a.TotalCustSup +
b.TotalCustSup) as TotalCustSup, (a.CountCustSup + b.CountCustSup) as
CountCustSup, ((a.TotalCustSup + b.TotalCustSup) / (a.CountCustSup +
b.CountCustSup)) as AveLastFive from CustSuptemp2_S a, AveCS1000View b where a.Corporation = b.Corporation and (AveLastFive >= 1000)

INSERT INTO TABLE BatchProcHist
VALUES ('AveCS1000View_S', from_unixtime(unix_timestamp());
```



请注意，我不得不使用一个额外的临时表，以便能够为未处理的记录以及来自相应批处理视图的记录添加并计数 `CustSuppStrength` 数值——因为该视图计算的是平均值。

在讨论了开发速度层的所有这些逻辑之后，让我转向使用 Spark 的接口和速度层的实现。如你所知，Spark 使用数据框和 RDD（弹性分布式数据集）作为内存结构，你可以利用它们进行查询和性能优化。Spark 还允许你使用 `SQLContext` 对 Hive 数据库执行查询。这两个概念对于速度层的实现都很有用。你可以在 Spark shell 中使用 Scala、Python 或 R。

首先，你需要构建一个 `HiveContext`，它继承自 `SQLContext`，使你能够查找 Hive MetaStore 中的表，并支持使用 HiveQL 进行查询。你不需要现有的 Hive 部署来创建 `HiveContext`。如果没有指定 Hive 配置和目录的 `hive-site.xml`，该上下文会自动在当前目录中创建 `metastore_db` 和 `warehouse`。这里，我使用 Scala，并且 `sc` 是一个现有的 `SparkContext`：

```scala
val sqlContext = new org.apache.spark.sql.hive.HiveContext(sc)
```

```sql
val sqlContext.sql("Create table MaxTable as select ViewName, max(CreatedAt) from BatchProcHist group by ViewName having ViewName = 'AveCS1000View'")
val sqlContext.sql("Insert into MaxTable select ViewName, max(CreatedAt) from BatchProcHist group by ViewName having ViewName = 'AveCS1000View _S'")
```

你可以类似地执行所有必要的 HiveQL 命令来创建速度层视图 `AveCS1000View_S`。对于最后一步（当视图创建时），你可以不创建视图，而直接执行 select 语句并将结果读入数据框，如下所示：

```scala
val resultsDF = sqlContext.sql("select a.Corporation, (a.TotalCustSup + b.TotalCustSup) as TotalCustSup, (a.CountCustSup + b.CountCustSup) as CountCustSup, ((a.TotalCustSup + b.TotalCustSup) / (a.CountCustSup + b.CountCustSup)) as AveLastFive from CustSuptemp2_S a, AveCS1000View b where a.Corporation = b.Corporation and (AveLastFive >= 1000)")
```

你可以将结果数据框注册为临时表，然后对其执行任何查询：

```scala
val resultsDF.registerTempTable("AveCS1000View _S")
val results = sqlContext.sql("SELECT Corporation FROM AveCS1000View _S")
```

你需要使用一个能够从 Hive 和 Spark 读取数据的查询工具，以结合批处理层和速度层视图的结果。有足够多的选择，当然你也可以使用 Spark SQL 作为查询工具。

关于 Hive 和 Spark SQL 的集成，有几点需要注意。由于 Hive 依赖项数量庞大，它并未包含在默认的 Spark 程序集中。你可以通过向 Spark 的构建添加 `-Phive` 和 `-Phive-thriftserver` 标志来启用 Hive 支持。这将构建一个新的包含 Hive 的程序集 jar。确保此 Hive 程序集 jar 存在于所有数据节点上，因为它们需要访问 Hive 的序列化和反序列化库（SerDes）才能访问存储在 Hive 中的数据。

此外，通过将你的 `hive-site.xml` 文件复制到 `$HADOOP_HOME/conf` 目录中来提供 Hive 配置。请注意，如果你使用的是 YARN 集群（`yarn-cluster` 模式），则 datanucleus 的 jar 文件应位于 `lib_managed/jars` 目录中，而 `hive-site.xml` 应位于 `$HADOOP_HOME/conf` 目录下，以便 YARN 集群启动的驱动程序和所有执行器使用。实现这一点的最简单方法是通过 `spark-submit` 命令的 `--jars` 选项和 `--file` 选项来添加它们。

人们常说 Lambda 架构最大的问题在于需要为批处理层和速度层维护两套独立的代码。通过使用 Spark SQL 和 Hive，我认为无需维护两套代码——一套就够了。这无疑简化了实现。



### 使用 Lambda 架构的优缺点

`Lambda` 架构定义了批处理和流处理如何协同工作，以在使用 `NoSQL` 解决方案的同时提供完整的解决方案。`Lambda` 架构使你能够以近乎实时的方式高效地对所有数据运行即席查询。

其核心理念是将结果预计算为一组视图，并查询这些视图而非主数据集。与传统的 `NoSQL` 架构（或缺乏架构的情况）相比，该架构提供了诸多优势，并重新定义了 `NoSQL` 和 `Hadoop` 在数据处理中的角色。然而，`Lambda` 架构也存在一些问题。它无法解决你所有的数据处理难题。本节将讨论 `Lambda` 架构的优缺点，首先从优点开始。

## Lambda 架构的优点

最常被讨论的优点之一是数据的不可变性。此外，存储层和查询层可以拥有不同的存储结构。另一个受欢迎的特性是 `Lambda` 架构的重新处理能力。下面简要介绍所有这些特性：

-   `Lambda` 架构强调数据的不可变性。输入数据被保留且不做任何更改。处理并转换后的数据会单独写出，但始终可以访问其原始形式接收到的输入数据。这也保护了数据免受人为错误或硬件故障的破坏，并增强了架构的弹性。

-   数据通过一系列模块化阶段进行转换，这有助于调试（每个阶段独立进行），并使工作流程易于管理。

-   可以轻松地重新处理数据（再次处理输入数据以重新导出输出）。为什么需要这样做？你的应用程序中总有可能存在代码变更（由于功能变更或发现问题/错误）。如果使用修改后的代码从输入流中导出输出数据，你将需要使用新代码重新处理输入。回想一下，每次处理新数据时，批处理层都会被重建。因此，默认情况下就支持和执行数据的重新处理。

-   `Lambda` 架构使你能够以规范化形式存储主数据集，同时根据需要在反规范化实时视图和批处理视图时提供灵活性。这有助于提升对 `NoSQL` 数据的查询性能，因为 `NoSQL` 数据库通常不擅长处理连接或子查询。数据分离还允许你根据需要微调每个数据层。

-   主数据集（作为不可变的数据存储）可以作为可靠的分析来源。由于它包含所有应用程序数据的完整记录或历史，因此可以分析任何数据子集或在其中寻找模式。

-   `Lambda` 架构便于在你的查询中使用近乎实时的数据。

## Lambda 架构的问题

`Lambda` 架构有很多有用的特性，但它能适用于所有情况和所有类型的数据吗？在使用它时是否存在任何需要解决的固有弱点或缺陷？我将探讨这些问题的答案以及更多内容：

-   `Lambda` 架构（以及`Nathan`）假设实时处理与批处理相比准确性更低、能力更弱且不必要地更复杂。这并不一定正确。例如，许多领先的流处理系统（`Storm`、`Spark`、`Amazon Kinesis`）能够提供与任何批处理系统一样强大的语义保证。

-   “`Lambda` 架构击败了 CAP 定理”的说法是不正确的。`Lambda` 本质上是一种用于异步处理的架构。因此，计算出的结果与输入数据并不立即保持一致。

-   `Lambda` 架构的一个大问题是你需要在两个独立的分布式系统（分别用于批处理层和速度层）中维护代码（这些代码需要产生相同的结果）。由于你需要专门为代码所运行的框架进行工程化设计，因此实现 `Lambda` 架构的系统所产生的操作复杂性是巨大的。

解决此问题的一种建议方法是使用一种能够抽象化实时框架和批处理框架的语言或框架。这样，你的代码可以根据需要用于流处理或`MapReduce`。`Kappa` 架构和`Butterfly` 架构（由 `Milind Bhandarkar` 提出）旨在克服这个问题。本章将讨论这两种架构。

-   管理和调优两个操作系统（分别用于批处理层和速度层）的运营负担非常重。此外，每当你计划添加新功能时，你只能考虑那些两个系统都能支持的功能。这可能会阻止你使用流行的 `Hadoop` 组件和工具，例如 `Hive`、`Pig`、`Crunch`、`Cascading`、`Oozie` 等。

`Lambda` 架构有其优缺点，并不适用于所有类型的数据或业务目标。目前已有一种名为 `Kappa` 的替代方案，我将在下一节中介绍。

## Kappa 架构

2014 年夏天，`Jay Kreps` 在 `O’Reilly Radar` 的一篇文章中创造了 `Kappa` 架构这个术语。`Jay` 只是在评论 `Lambda` 架构及其问题。那么，什么是 `Kappa`？它与 `Lambda` 有何不同？

概念非常简单。使用一个允许你保留可能需要重新处理的数据的完整日志的流处理引擎（`Spark`、`Kafka` 等）。如果需要重新处理，则启动流处理作业的第二个实例，该实例将从所保留数据的开头开始处理，并将输出写入新的目标位置（如表或文件）。当第二个作业完成处理后，将应用程序切换为从新的输出目标位置读取数据。之后，你可以停止旧版本的作业并删除旧的输出目标位置。图 9-8 展示了 `Kappa` 架构。

```
客户端

流处理引擎

查询
请求

当前流
当前作业的输出
处理作业

输入数据
流

新流
新作业的输出
处理作业

服务层
```

**图 9-8.** *Kappa 架构*

设计 `Kappa` 架构的原因之一是为了避免为批处理层和速度层维护两个独立的代码库。此外，如果批处理层和速度层被流处理层（由流处理引擎处理）取代，那么单一流处理引擎需要处理实时数据处理以及持续的数据重新处理（当代码更改时需要重新处理数据）。

因此，`Kappa` 架构包含两层：流处理层和服务层。流处理层负责执行流处理作业。通常，一个流处理作业负责实时数据处理。如果流处理作业发生代码更改并且需要重新处理数据，则额外执行修改后的流处理作业以完成该任务。服务层用于查询结果。

各种开源技术，如 `Apache Kafka`、`HBase`、`HDFS`、`Spark`、`Drill`、`Storm` 或 `Samza`，都可以用来实现 `Kappa` 架构。例如，可以使用像 `Apache Kafka` 这样的*发布-订阅*消息系统来摄取数据。`HDFS` 可用于持久化存储。任何低延迟系统（如 `Apache Storm`、`Samza` 或 `Spark Streaming`）都可用于实现 `Kappa` 架构中的流处理层。

现在，可以使用 `Apache Spark` 来开发 `Lambda` 架构中的批处理层和速度层（以实现单一代码库）。服务层可以使用 `Apache Drill` 通过 `Splout` 查询来实现。如何在两种架构之间进行选择？这取决于需要实现的应用程序的特性。



例如，当需要应用于实时数据和历史数据的算法完全相同时，采用`Kappa 架构`是一个很好的选择。但如果实时算法和批处理算法的预期输出不同，那么批处理层和实时层就无法合并，此时必须使用`Lambda 架构`。是否存在这种情况呢？考虑这样一个场景：你的批处理层需要处理十亿条记录，并计算每日和每周平均值。此时，生成批处理模型将耗费大量时间和资源，以至于你需要使用近似模型来计算实时视图。因此，你无法合并批处理层和实时层的处理流程，也就不能使用`Kappa`——`Lambda`将是唯一可能的模型。

我已经强调了`Kappa 架构`的多个优势，但你需要明白，它并非适用于所有场景。它不能解决你所有的大数据处理问题。当然还有其他替代方案，例如由 Michael Hausenblas 提出的`Zeta 架构`或`Iot-a`。目前已有多种架构方案可供选择，你应该评估自身环境，并根据你的数据、处理需求、业务目标和可用硬件资源做出审慎决策。

