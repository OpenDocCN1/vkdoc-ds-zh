# 排版后的文本

`ClaimSubmissionDate`
`PolicyOwnerSSNFEIN`
`ClaimType`
`PolicyOwnerType`
`ClaimAmount`
`PolicyType`
`ClaimStatus`
`PolicyStartDate`
`ClaimWeatherCond`
`PolicyEndDate`
`LastModified`
`LastModified`

**图 11-2. 反范式化的理赔与保单实体**

我将从与理赔相关的批处理视图开始：
1. 该批处理视图的第一步将统计过去 12 个月内每个保单的理赔数量，并生成一个计数大于 5 的临时表。第二步，将该临时表与反范式化的`Policy`实体进行连接，以获取保单持有人的详细信息，构成该批处理视图。
2. 第二个批处理视图将根据天气条件（如雪、雨、暴风雨、雪崩或龙卷风）对理赔进行筛选。

接下来是与保单相关的批处理视图：
3. 第三个批处理视图将列出在过去 12 个月内至少发生 2 次 1 级违规的青少年驾驶员。第一步，使用`PO_Drv_Hist`实体填充一个临时表，其中包含过去 12 个月内发生 2 次或以上 1 级违规的驾驶员。然后，使用反范式化`Policy`实体中的`DOB`（出生日期）筛选出年龄小于或等于 19 岁的驾驶员条目。
4. 第四个批处理视图将使用为第三个批处理视图创建的临时表，并筛选出年龄大于 19 岁的驾驶员条目。

下一步，你需要从存储在`HDFS`中的主数据中推导出这些视图。请注意，这些*视图*实际上就是`Hive`表。我将在下一节中描述具体的处理过程。

## 第 11 章 ■ 案例研究：实现 Lambda 架构

### 实现批处理层

如第 9 章所述，出于多种原因，我将使用`HDFS`来存储主数据和批处理层视图（`Hive`表）。`Hive`实际上用于元数据管理，你可以使用`MySQL`来存储元数据。

此外，对于追加垂直或基于时间的数据，我将使用`Hive`分区（可根据应用需求按日、周或月进行分区）来追加新数据。我使用`Hive`分区来演示如何管理主数据集的新数据，并在创建的批处理视图中对其进行处理。我使用时间戳`LastModified`来对表进行分区。以下是构成主数据的`Hive`表：

```sql
CREATE TABLE ClaimsMaster(
  ClaimId INT,
  ClaimSeqId INT,
  PolicyId INT,
  ClaimSubmissionDate TIMESTAMP,
  ClaimType STRING,
  ClaimAmount INT,
  ClaimSTATUS STRING,
  ClaimWeatherCond STRING
)
PARTITIONED BY (LastModified TIMESTAMP)
ROW FORMAT DELIMITED FIELDS TERMINATED BY "\;";

CREATE TABLE PolicyMaster(
  PolicyId INT,
  PolicyOwnerId INT,
  PolicyOwnerDOB DATE,
  PolicyOwnerSSNFEIN STRING,
  PolicyOwnerType STRING,
  PolicyType STRING,
  PolicyStartDate TIMESTAMP,
  PolicyEndDate TIMESTAMP
)
PARTITIONED BY (LastModified TIMESTAMP)
ROW FORMAT DELIMITED FIELDS TERMINATED BY "\;";

CREATE TABLE PO_Drv_Hist(
  PolicyOwnerId INT,
  ViolationNum SMALLINT,
  ViolationSeverity TINYINT,
  ViolationDate TIMESTAMP,
  ViolationDetails STRING
)
PARTITIONED BY (LastModified TIMESTAMP)
ROW FORMAT DELIMITED FIELDS TERMINATED BY "\;";
```

### 下一步是向这些存储主数据的表中添加新数据

如你所知，这些表以文件形式存储在`HDFS`中，而`Hive`则保存元数据以更有效地管理数据修改。新数据可以每天（或按任何其他必要频率）使用动态分区添加。你可以创建一个暂存表（指向包含新数据的文件），并如下所示向表中添加新分区：

```sql
CREATE EXTERNAL TABLE ClaimsMaster_stg(
  ClaimId INT,
  ClaimSeqId INT,
  PolicyId INT,
  ClaimSubmissionDate TIMESTAMP,
  ClaimType STRING,
  ClaimAmount INT,
  ClaimSTATUS STRING,
  ClaimWeatherCond STRING,
  LastModified TIMESTAMP
) ROW FORMAT DELIMITED FIELDS TERMINATED BY "\;"
LOCATION "/InsuranceExample/ClaimsMaster/staging";

FROM ClaimsMaster_stg INSERT OVERWRITE TABLE ClaimsMaster PARTITION
(LastModified) SELECT ClaimId, ClaimSeqId, PolicyId,
```

`ClaimSubmissionDate`, `ClaimType`, `ClaimAmount`, `ClaimSTATUS`, `ClaimWeatherCond`, `LastModified`；

请注意，临时表中包含一个额外列，指向表`ClaimsMaster`的临时目录（用于存放新数据）。同样的原理也适用于向表`PolicyMaster`和`PO_Drv_Hist`添加新数据。此外，将新数据文件复制到相应临时目录、创建临时表以及向基表添加新分区的过程，都可以实现自动化并按计划执行。

现在我来创建批处理视图。首先创建一个表`BatchProcHist`，用于维护已创建批处理视图的历史记录：

```sql
CREATE TABLE BatchProcHist(
  ViewName STRING,
  CreatedAt timestamp)
ROW FORMAT DELIMITED FIELDS TERMINATED BY "\;";
```

现在创建第一个视图，用于列出过去 12 个月内索赔次数超过 5 次的保单持有人。第一步，获取过去 12 个月内索赔次数超过 5 次的保单列表，并写入临时表：

```sql
Create table ClaimDeftemp1 as 
  Select PolicyId, count(ClaimId) as Claimcount 
  from ClaimsMaster 
  where datediff(current_date, add_months(to_date(ClaimSubmissionDate),12)) <= 0
  group by PolicyId 
  having count(ClaimId) > 5
```

请注意，这里我只考虑了`ClaimSubmissionDate`的日期部分，并在其基础上加一年后与当前日期进行比较。

### 第 11 章 ■ 案例研究：实现 Lambda 架构

下一步，将此临时表与反规范化后的保单实体进行关联，以获取保单持有人的详细信息，从而构成批处理视图：

```sql
Create table ClaimDefView as 
  Select P.PolicyOwnerID, P.PolicyId, C.Claimcount 
  from PolicyMaster P, ClaimDeftemp1 C 
  where P.PolicyId = C.PolicyId;
```

最后，写入历史记录表：

```sql
INSERT INTO TABLE BatchProcHist
VALUES ('ClaimDefView', from_unixtime(unix_timestamp()));
```

第二个批处理视图根据天气条件（如雪、雨、暴风雨、雪崩或龙卷风）过滤索赔记录：

```sql
CREATE table ClaimWeatherView as 
  Select distinct ClaimId, ClaimWeatherCond 
  from ClaimsMaster 
  where ClaimWeatherCond in ('Snow','Rain','Storm','Avalanche','Tornado');
```

写入历史记录表：

```markdown
## 第 11 章 ■ 案例研究：实现 Lambda 架构

```sql
INSERT INTO TABLE BatchProcHist
VALUES ('ClaimWeatherView', from_unixtime(unix_timestamp()));
```

第三个批处理视图的目标是列出在过去 12 个月内至少有 2 次 1 级严重违规的青少年驾驶员。首先，我填充一个临时表（列出过去 12 个月内违规次数为 2 次及以上的驾驶员）：

```sql
Create table TeenageVioltemp1 as 
  Select PolicyOwnerId, count(ViolationSeverity) as TotalViolations
  from PO_Drv_Hist 
  where (datediff(current_date, add_months(to_date(ViolationDate),12)) <= 0) 
    and (ViolationSeverity = 1)
  group by PolicyOwnerId
  having count(ViolationSeverity) > 2
```

接下来，使用反规范化保单实体中的出生日期（`DOB`）对临时表中的条目进行过滤，仅保留 19 岁及以下的驾驶员：

```sql
Create table TeenageViolView as 
  Select T.PolicyOwnerId, T.TotalViolations
  from TeenageVioltemp1 T, PolicyMaster P 
  where T.PolicyOwnerId = P.PolicyOwnerId
    and (datediff(current_date, add_months(P.PolicyOwnerDOB,228)) <= 0)
```

写入历史记录表：

```sql
INSERT INTO TABLE BatchProcHist
VALUES ('TeenageViolView', from_unixtime(unix_timestamp()));
```

我使用了 `add_months` 函数为驾驶员的出生日期加上 228 个月（即 19 年），并计算差值（以判断驾驶员是否为青少年）。

第四个批处理视图列出违规次数为 3 次及以上的成年驾驶员。因此，我将创建一个新的临时表（与第三个批处理视图类似），并过滤掉年龄大于 19 岁的驾驶员：

```sql
Create table Violtemp1 as 
  Select PolicyOwnerId, count(ViolationSeverity) as TotalViolations
  from PO_Drv_Hist 
  where (datediff(current_date, add_months(to_date(ViolationDate),12)) <= 0) 
    and (ViolationSeverity = 1)
  group by PolicyOwnerId
  having count(ViolationSeverity) > 3
```

过滤成年驾驶员：

```sql
CREATE TABLE AdultViolView AS
SELECT T.PolicyOwnerId, T.TotalViolations
FROM Violtemp1 T, PolicyMaster P
WHERE T.PolicyOwnerId = P.PolicyOwnerId
  AND (DATEDIFF(ADD_MONTHS(P.PolicyOwnerDOB, 228), CURRENT_DATE) < 0);
```

写入历史表：

```sql
INSERT INTO TABLE BatchProcHist
VALUES ('AdultViolView', FROM_UNIXTIME(UNIX_TIMESTAMP()));
```

`重要提示：` 不要忘记删除您创建的临时表——否则脚本将报错终止。

因此，创建完批次视图后，下一步就是提供或展示它们。

### 实现服务层

我在[第 9 章](http://dx.doi.org/10.1007/978-1-4842-1287-5_9)讨论了服务层的功能和特点。基本上，它“服务”批次视图，或者以最小延迟提供快速访问。这就是为什么服务层需要是一个专门的分布式数据库，它必须能够：
- 存储批次视图，并支持良好的随机和顺序数据访问性能（仅读取）。
- 能够快速将批次视图替换为批次层重建的新版本（即支持批量更新）。
- 容错性（因为视图可以从批次层快速重新部署）。
- 具备索引能力以实现快速检索。

[第 9 章](http://dx.doi.org/10.1007/978-1-4842-1287-5_9)中的解决方案是 `Splout SQL` —— 我将再次使用它。关于 `Splout SQL` 的架构和操作细节，请参见[第 9 章](http://dx.doi.org/10.1007/978-1-4842-1287-5_9)。`Splout SQL` 可用于将批次层视图部署为表空间内的表。`表空间`用于对具有相同键列的多个表进行分组。其优势在于，您可以使用相同的分区键对多个表进行分区。对于 `Splout SQL` 来说，分区非常重要，因为它用于在索引和部署之前平衡数据。请注意，当一个表按单列或多列分区时，`Splout`会将这些列的值连接起来形成一个字符串。因此，分区是行的函数，也可以使用任意函数进行分区（例如，一个只使用某列前八个字符的`JavaScript`函数）。

以我的示例为例，请注意批次视图 1、3 和 4 都使用 `PolicyOwnerId` 作为键列。因此，您可以使用这三个批次视图（或 `Hive` 表）设计一个表空间。

另一个表空间可以容纳使用 `ClaimId` 作为键列的第二个批次视图。

因此，作为下一步，您需要生成表空间和表。

要创建或生成表空间，您需要使用 `generate` 工具。该工具使用一个 `JSON` 表空间描述符，如下所示。您需要指定输入类型以及 `Hive` 数据库和表名。表空间描述符文件可以创建在 `Splout SQL` 安装目录中：

```json
{
  "name": "PolicyTblspace",
  "nPartitions": 12,
  "partitionedTables": [
    {
      "name": "ClaimDefView",
      "partitionFields": "PolicyOwnerId",
      "tableInputs": [
        {
          "inputType": "HIVE",
          "hiveTableName": "ClaimDefView",
          "hiveDbName": "MyHiveDB"
        }
      ]
    },
    {
      "name": "TeenageViolView",
      "partitionFields": "PolicyOwnerId",
      "tableInputs": [
        {
          "inputType": "HIVE",
          "hiveTableName": "TeenageViolView",
          "hiveDbName": "MyHiveDB"
        }
      ]
    },
    {
      "name": "AdultViolView",
      "partitionFields": "PolicyOwnerId",
      "tableInputs": [
        {
          "inputType": "HIVE",
          "hiveTableName": "AdultViolView",
          "hiveDbName": "MyHiveDB"
        }
      ]
    }
  ]
}
```

让我快速回顾一下这个文件（`PolicyTblspace.json`）中的信息。

表空间将被命名为 `PolicyTblspace`，目前定义了三个表（或批次视图）：`ClaimDefView`、`TeenageViolView`和`AdultViolView`，这些是在上一节中创建的。数据库名称是 `MyHiveDB`（您需要使用您创建 `Hive` 表所在的数据库名称），并且我为我的数据创建了 12 个分区。我使用 `PolicyOwnerId` 作为分区列，因为该列将是几乎所有查询的一部分。

要部署此表空间，可以从（Linux）命令行执行以下命令，以生成表空间 `PolicyTblspace`（从 `Splout SQL` 安装目录）：

```bash
hadoop jar splout-*-hadoop.jar generate -tf file:///`pwd`/ PolicyTblspace.json -o out-MyHiveDB_splout_example
```
```

为了提升性能，您可能需要向表空间添加索引，`Splout`允许您轻松添加索引。注意，您必须使用一个名为`simple-generate`的不同生成器，而不是用于生成`PolicyTblspace`表空间的`generate`表空间生成器。使用`simple-generate`生成器的限制是，您的表空间只能包含一个表。由于示例中的另一个表空间只有一个表（或批次视图），我将针对该表空间演示`simple-generate`的用法。以下命令将在生成表空间`ClaimTblspace`时创建一个额外的索引：

```bash
hadoop jar splout-hadoop-*-hadoop.jar simple-generate –it HIVE –hdb MyHiveDB –htn ClaimWeatherView -o out-MyHiveDB_splout_example -pby ClaimId -p 1 -idx "ClaimWeatherCond" -t ClaimWeatherView -tb ClaimTblspace
```

请注意，我没有包含`ClaimId`列，因为它是一个分区列并且已经被索引。`-idx`选项只是向索引中添加更多列（在此例中是`ClaimWeatherCond`）。另请注意，这里没有`JSON`配置文件，因此所有配置（例如`Hive`数据库、表名、分区列等）都通过命令指定。

表空间成功生成后，您需要按如下方式部署它们：

```bash
hadoop jar splout-hadoop-*-hadoop.jar deploy -q http://localhost:4412 -root out-MyHiveDB1_splout_example -ts PolicyTblspace

hadoop jar splout-hadoop-*-hadoop.jar deploy -q http://localhost:4412 -root out-MyHiveDB2_splout_example -ts ClaimTblspace
```

`localhost`是运行中的`QNode`主机（客户端与之连接），`localhost`将在运行时自动替换为第一个有效的私有 IP 地址（根据配置文件中的指定）。

一旦表空间被部署，您就可以在任何查询中使用它。例如，如果需要检查某个特定理赔（`ClaimId=14124736`）是否是在恶劣天气条件下提交的，您可以使用`REST API`，如下所示：

```
http://localhost:4412/api/query/ClaimTblspace?sql=SELECT * FROM ClaimWeatherView;&key=14124736
```

您可以使用`Splout SQL`（或任何其他您喜欢的数据库解决方案）来部署批次层视图，如本节所示。接下来，我将讨论如何访问批次层尚未处理的数据，并将其包含在查询结果中。

### 实现速度层

回顾一下，速度层的目的是使批次层视图尚未处理的数据能够无延迟地可用。速度层视图与批次层视图的另一个区别是，批次层通过重新计算（或重建）来更新视图，而速度层对视图执行增量处理，只处理自上次增量处理之后发生的增量（或新）事务。因此，如果您的传入数据事务带有时间戳，并且您将其从主数据集中提取出来，那么根据记录是被修改还是被添加，您可以相应地修改您的速度层视图。

接下来需要考虑的是，是否需要同步更新速度层（将对主数据的任何更新直接应用于速度层视图），还是异步更新（将请求排队，实际更新稍后进行）。在本例中，异步更新将更为有用，因为分析应用更侧重于复杂的计算和聚合，而非交互式用户输入。此外，考虑到数据量庞大，能够对更新有更多控制权会很有益（例如，通过临时分配额外的请求来处理变化的工作负载）。

我将使用`Spark`来实现速度层。更具体地说，我将使用`Spark`处理引擎和`Spark SQL`。

由于 Lambda 架构定义速度层由批处理层尚未处理过的记录组成，因此需要确定这些记录是什么。你可能还记得，每次构建批处理视图后，都会在表`BatchProcHist`中插入一条历史记录。因此，批处理视图的最新记录可以告诉我们最近一次构建的日期/时间，从而帮助我们确定哪些是未处理的记录。我将把第一个批处理视图的最新记录写入一个表（因为 Hive 不支持将查询结果赋值给变量）：

```
Create table MaxTable as select ViewName, max(CreatedAt) as MaxDate from BatchProcHist group by ViewName having ViewName = 'ClaimDefView';
```

这样就能得到批处理层视图构建的最新日期/时间。

然而，由于速度层视图的处理频率更高，你还需要确定速度层上次处理记录的日期/时间（因为只需要考虑未处理的记录），以便更新视图（这里是一个注册为表的 Spark 数据帧）。我将速度层视图命名为`ClaimDefView_S`。由于速度层视图也会写入审计表`BatchProcHist`，我将把第一个速度层视图的最新记录写入同一个表（其中已经捕获了第一个批处理视图的最新记录）：

```
Insert into MaxTable select ViewName, max(CreatedAt) from BatchProcHist group by ViewName having ViewName = 'ClaimDefView_S';
```

现在，我只需要确定这些记录中哪一个是最近的（以防批处理层在上次速度层构建之后又重新构建），并将其作为处理第一个速度层视图记录的基础：

```
Create table MaxTbl1 as select max(MaxDate) as MaxDate from MaxTable;
```

最后，从主数据集中获取未处理的记录，并创建速度层视图。同时，添加时间戳并将一条记录写入审计历史表：

```
Create table ClaimDeftemp11 as Select PolicyId, ClaimId from ClaimsMaster a, MaxTbl1 b where where a.LastModified > b.MaxDate;

Create table ClaimDeftemp12 as Select PolicyId, count(ClaimId) as Claimcount from ClaimDeftemp11 where datediff(current_date, add_months(to_date(ClaimSubmissionDate),12)) <= 0 group by PolicyId having count(ClaimId) > 5
```

作为最后一步，我将把这个临时表与反规范化的`Policy`实体进行连接，以获取构成批处理视图的策略所有者详细信息，并将一条记录写入历史表：

```
Create table ClaimDefView_S as Select P.PolicyOwnerID, P.PolicyId, C.Claimcount from PolicyMaster P, ClaimDeftemp12 C where P.PolicyId = C.PolicyId;

INSERT INTO TABLE BatchProcHist VALUES ('ClaimDefView_S', from_unixtime(unix_timestamp());
```

现在删除临时表（因为我们可能会为下一个速度层视图重新创建其中一些表）：

```
Drop Table MaxTable;
Drop Table MaxTbl1;
Drop Table ClaimDeftemp11;
Drop Table ClaimDeftemp12;
```

其他速度层视图可以类似地创建如下：

```
--视图 2;
Create table MaxTable as select ViewName, max(CreatedAt) as MaxDate from BatchProcHist group by ViewName having ViewName = 'ClaimWeatherView';

Insert into MaxTable select ViewName, max(CreatedAt) from BatchProcHist group by ViewName having ViewName = 'ClaimWeatherView_S';
```