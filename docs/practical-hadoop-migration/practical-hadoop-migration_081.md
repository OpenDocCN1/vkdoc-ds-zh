# 构建批量视图

我将探讨如何为先前设计的基于事实模型的应用程序（信息跟踪系统和销售管理系统）构建批量视图。信息跟踪系统由`DataScourerNinjas`设计，供其潜在客户使用，这些客户是设计和销售具有特定功能（如薪资、客户关系管理等）的软件公司。

根据他们的经验，大多数软件公司寻找在过去五年中保持盈利且拥有大量客户支持团队（至少平均 1000 人）的潜在客户。有些公司则关注过去三年的短期盈利能力，但坚持与 IT 部门中大学毕业生占比高（至少 60%）的公司合作。此外，由于这些软件产品针对特定行业，聚焦于特定业务类别的公司至关重要。考虑到这些因素，他们设计了以下批量视图：

-   过去五年盈利的公司（包含列：`Corporation`，`Profit_last_year`）
-   过去五年平均客户支持团队规模达到 1000 人（或以上）的公司
-   过去三年 IT 部门中大学毕业生比例至少达到 60%（平均）的公司

设计批量视图时还需考虑其大小。在某些情况下，如果数据集非常庞大，批量视图也会变得巨大，您可能需要考虑进一步拆分它们。例如，考虑正在讨论的信息跟踪系统。假设出现巨大繁荣，导致数千家新公司成立，并且`DataScourerNinjas`成功获取了这些公司的数据。如果您按月创建批量视图，查询快速处理的数据量可能仍然很大，您可能希望重新设计批量层，改为按周甚至按天创建视图。这将增加（总）处理时间（因为您将按周或按天处理，而不是按月），但有助于提升性能并减少速度层的大小（以及处理量）。

## 实施批量视图

关于上一节设计的批量视图，有几点需要考虑。该视图可以轻松使用`HDFS`进行存储，并配合任何可用的 NoSQL 解决方案（如`HBase`、`Cassandra`或`MongoDB`）来实现。或者，您可以将数据保留在`HDFS`中，并使用`Hive`进行元数据管理，使用`MySQL`来保存元数据。`Nathan`建议您使用`Java`或任何其他您选择的语言来编写自己的函数和抽象层，因为使用现成的 NoSQL 解决方案会增加复杂性并可能影响性能。

我将使用`Hive`来演示（设计和构建批量视图的）概念，原因有几个。考虑到各种分布式引擎的不断涌现（至少在近期），编写`MapReduce`代码有其自身的局限性。此外，我并不期望所有使用本书的人都精通`Java`或`C#`编程。本章更多是关于理解 Lambda 架构的概念，而不是编写可直接用于生产环境的代码。

此外，为了追加垂直或基于时间的数据，`Nathan`开发了一个基于`Java`的应用程序，名为`Pail`。您可以将`Pail`视为`HDFS`的一个容器。`Thrift`（`Nathan`用于序列化的接口）模式会创建`Java`对象。这些`Thrift Java`对象通过实现`PailStructure`接口来进行序列化和反序列化，并且也有助于实现垂直分区。这些是使用`Java`编程的高级主题——因此，我倾向于采用一种更通用的方法（可能对更广泛的读者来说更容易理解），即使用`Hive`分区（按天、按周或按月，取决于您的应用需求）来追加新数据。我使用`Hive`分区来演示如何管理主数据集的新数据，并在创建的批量视图中进行处理的这一概念。

您可以假设 Web 界面使用基于内存的数据库解决方案（如`VoltDB`、`Apache Geode`或`Ampool`）来保存一天的数据，然后每晚将其传输到`HDFS`。因此，我将创建四个`Hive`表来保存该系统的主数据。这是实施前面讨论的批量视图所需的第一步。我还使用了时间戳来对表进行分区。

如果您需要执行事务或需要 ACID 支持，则需要在创建新表之前对`Hive`进行一些配置更改。您可能知道，从`0.14`版本开始，`Hive`支持 CRUD（创建、读取、更新、删除）操作。要启用对 CRUD 操作的支持，请确保您的`hive-site.xml`文件配置了以下参数：

-   `hive.support.concurrency` – `true`
-   `hive.enforce.bucketing` – `true`
-   `hive.exec.dynamic.partition.mode` – `nonstrict`
-   `hive.txn.manager` –`org.apache.hadoop.hive.ql.lockmgr.DbTxnManager`
-   `hive.compactor.initiator.on` – `true`
-   `hive.compactor.worker.threads` – `1`

实施这些配置更改后，重启`Hive`。此外，在使用`ZooKeeper Hive`锁管理器时，必须运行`ZooKeeper`实例。

有关其他设置细节，请参阅以下链接：

-   `http://zookeeper.apache.org/doc/r3.1.2/recipes.html#sc_recipes_Locks`
-   `https://cwiki.apache.org/confluence/display/Hive/Hive+Transactions#HiveTransactions-LockManager`

请记住，仅对使用`ORC`文件格式存储的对象支持事务。最后，您需要在创建表时启用对 ACID 属性的支持，并指定分桶列或键列（顺便提一下，这些列不能被更新）。因此，您的`CREATE TABLE`语句需要包含以下构造：

```
clustered by <key column(s)> into <number of buckets> stored as orc TBLPROPERTIES ('transactional'='true')
```

我的示例未使用事务支持，因此以下是创建这些`Hive`表的代码：

```sql
CREATE TABLE CorpBusDetails(
    Corporation STRING,
    BusCategory STRING,
    BusDetails STRING
)
PARTITIONED BY (AsOf TIMESTAMP)
ROW FORMAT DELIMITED FIELDS TERMINATED BY "\;";

CREATE TABLE CorpCustSupDetails(
    Corporation STRING,
    CustSuppStrength INT,
    Males INT)
PARTITIONED BY (AsOf TIMESTAMP)
ROW FORMAT DELIMITED FIELDS TERMINATED BY "\;";

CREATE TABLE CorpITDetails(
    Corporation STRING,
    ITStrength INT,
    ColGrads INT)
PARTITIONED BY (AsOf TIMESTAMP)
ROW FORMAT DELIMITED FIELDS TERMINATED BY "\;";

CREATE TABLE CorpFinDetails(
    Corporation STRING,
    YrlyRevenue BIGINT,
    ProfLastYear BIGINT)
PARTITIONED BY (AsOf TIMESTAMP)
ROW FORMAT DELIMITED FIELDS TERMINATED BY "\;";
```

请注意，这些表以文件形式存储在`HDFS`中，而`Hive`仅保存元数据，以便更有效地管理数据修改（以及通过查询进行的数据操作）。那么，新数据如何每日添加呢？使用动态分区。



您需要为您的 Hive 安装启用动态分区并调整一些配置参数。详细信息请阅读 [`cwiki.apache.org/confluence/display/Hive/LanguageManual+DML#LanguageManualDML-DynamicPartitionInserts`](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DML#LanguageManualDML-DynamicPartitionInserts) 。

## 第 9 章 ■ 用于实时 Hadoop 应用的 Lambda 架构

任何新数据都可以通过创建一个临时表（指向包含新数据的文件）并向表中添加新分区来追加，如下所示：

```sql
CREATE EXTERNAL TABLE CorpBusDetails_stg(
Corporation STRING,
BusCategory STRING,
BusDetails STRING,
AsOf TIMESTAMP
) ROW FORMAT DELIMITED FIELDS TERMINATED BY "\;"
LOCATION "/TrackingInfo/CorpBusDetails/staging";

FROM CorpBusDetails_stg INSERT OVERWRITE TABLE CorpBusDetails PARTITION
(AsOf) SELECT Corporation,BusCategory,BusDetails,AsOf;
```

您会注意到，临时表多了一个列，并指向一个用于`CorpBusDetails`表的临时目录（存放新数据）。同样的原则可以应用于向`CorpCustSupDetails`和`CorpProfitDetails`表添加新数据。此外，将新数据文件复制到相应的临时目录、创建临时表以及向基表添加新分区的过程可以自动化和安排调度。

下一步是创建批处理视图。我首先会创建一个名为`BatchProcHist`的表，用于记录批处理视图的历史信息：

```sql
CREATE TABLE BatchProcHist(
ViewName STRING,
CreatedAt timestamp)
ROW FORMAT DELIMITED FIELDS TERMINATED BY "\;";
```

接下来，我将创建第一个视图：*过去 5 年盈利的公司*。最简单的判断方法是将过去五年的利润相加，并确保其和为正整数。以下是如何使用临时表快速计算的方法：

```sql
Create table Profitemp1 as Select ProfLastYear, AsOf, Corporation from
CorpProfitDetails where year(AsOf) <= year(from_unixtime(unix_timestamp())) and year(AsOf) >= (year(from_unixtime(unix_timestamp())) – 5)

Create table ProfLFiveView as select Corporation, sum(ProfLastYear) as
ProfLastFive from Profitemp1 group by Corporation having sum(ProfLastYear) > 0

INSERT INTO TABLE BatchProcHist
VALUES ('ProfLFiveView', from_unixtime(unix_timestamp()));
```

正如您所观察到的，在创建第一个批处理视图后，我还向`BatchProcHist`表写入了一条历史记录。

第二个视图涉及计算平均值（*过去五年平均客户支持人员数量等于或超过 1000 的公司*）。由于您需要在速度层计算最新的平均值，因此还需要存储用于计算该平均值的记录计数：

```sql
Create table CustSuptemp1 as Select CustSuppStrength, AsOf, Corporation from CorpCustSupDetails where year(AsOf) <= year(from_unixtime(unix_timestamp())) and year(AsOf) >= (year(from_unixtime(unix_timestamp())) – 5)

Create table AveCS1000View as select Corporation, sum(CustSuppStrength)
as TotalCustSup, count(CustSuppStrength) as CountCustSup,
(sum(CustSuppStrength) / count(CustSuppStrength)) as AveLastFive from
CustSuptemp1 group by Corporation having (AveLastFive >= 1000)

INSERT INTO TABLE BatchProcHist
VALUES ('AveCS1000View', from_unixtime(unix_timestamp()));
```

此时可以删除临时表（`Profitemp1`、`Profitemp2`和`CustSuptemp1`）。下次重新构建这些批处理视图时，它们会被重新创建。

您可以构建最后一个批处理视图（作为练习），并且可以根据需要支持的功能类似地构建其他视图。最后一步，需要创建二级索引以促进快速检索，但这在服务层完成。举个例子，针对设计的第一个批处理视图，以下二级索引将有助于提升性能：

* `Corporation`
* `ProfLastFive`, `Corporation`

请记住，创建二级索引会消耗处理时间和磁盘空间。因此，在设计这些索引并优化其使用时需要格外小心。此外，由于对视图进行索引是一个非常关键的步骤（从性能、磁盘空间和处理时间的角度来看），这需要由既熟悉您的数据又了解功能需求（分析和频繁执行的查询）的人员来执行。如果您的索引与查询不匹配，您将遇到性能问题，并且还会浪费宝贵的系统资源（和时间）来构建它们。

关于批处理视图，需要重点注意的一点是数据集的大小和可变性。如果您的数据集非常庞大或高度动态，生成的批处理视图可能会非常巨大，或者可能会丢失大量新数据，您可能需要以更小的批次进行处理。例如，考虑跟踪系统示例。假设市场突然繁荣，每周都有大量新公司加入到`DataScourerNinjas`的数据库中。如果您每月重新构建一次批处理视图，那么在此期间可能会添加大量不属于批处理视图的新数据（虽然可以通过速度层提供查询，但由于记录量更大，可能会降低速度层的效率和速度）。因此，您可能希望每周而不是每月重新构建批处理视图。这也有助于保持速度层的规模较小且易于管理。

对于庞大的数据集，您可能希望按周（而不是按月）拆分批处理视图，因为这将减少每次视图的处理资源和时间（因为您只需要处理一周的数据，而不是一个月），但可能会使您的检索策略复杂化（因为您需要确定要查询的正确视图，如果查询的时间跨度跨越多周，还可能需要查询多个视图）。

最后，您可能会对这种方法的明显疑问是关于创建此类批处理视图所需的时间或延迟。由于这些视图是从整个主数据集创建的，显然它们会消耗大量的系统资源，并且计算可能无法在可用的维护窗口内完成。即使可以完成，它们也可能不包含系统收集的所有数据，因为数据可能在视图计算期间（或之后）被添加，而您的查询结果可能已经过时数小时。Lambda 如何提供近实时的结果？答案在于 Lambda 的后续几层以及它们克服数据延迟问题的方式。

### 服务层

服务层“服务”批处理视图，或以最小延迟提供快速访问。因此，服务层需要是一个专门化的分布式数据库，能够托管批处理视图，并支持良好的随机和顺序数据访问性能。服务层还需要能够在批处理层重新构建批处理视图时快速地将旧版本替换为新版本，以便用户查询能够返回最新的结果。因此，它需要支持批量更新。



