# `$rsId`

## `list()`

我们刚刚建立的连接将R实例链接到了数据库。目前，这个数据库恰好是空的。如果你的角色是分析师，你可能有一个已存满数据的真实数据库。另一方面，你也可能拥有自己的数据，并希望将其迁移到正式的数据库系统中。无论哪种情况，我们都会探讨如何将数据推送到服务器以及从服务器拉取数据。

无论是执行拉取操作还是推送操作，我们需要保持一个活跃的连接处于打开状态。

然而，在我们准备探索这一过程之前，需要简要考虑一些数据库设计理念。因此，现在我们暂时关闭这个连接。这需要两个步骤，因为我们既要断开与`PostgreSQL`的连接，还要卸载允许`DBI`连接到我们特定类型数据库的驱动程序。如果操作成功，`dbDisconnect()`和`dbUnloadDriver()`都会返回`TRUE`。即使它们返回真值，相关信息仍会保留在`全局环境`中。因此，我们还需要使用`rm()`来清理环境：

```
dbDisconnect(conSuper)
[1] TRUE

dbUnloadDriver(drv)
[1] TRUE

rm(conSuper)
rm(drv)
```

如果上述所有步骤均成功执行，你现在已在本地机器上拥有了一个功能完备的`PostgreSQL`数据库服务器。同时，你也具备了将R连接至该服务器以及断开连接的能力。在讨论使用SQL和R的具体技术之前，我们先简要探讨一下数据库的理论基础。

## 第10章 读取大数据

### 10.3 数据库理论基础

虽然本书的宗旨既不是教授通用数据库知识，也不是专门讲解SQL数据库，但了解此类数据存储方式的高层概念仍然很有价值。我们使用的数据库`PostgreSQL`属于SQL数据库，它属于*关系型*数据库。其核心理念是：每一组*相关*的数据都应当存放在一个*表*中。现在，让我们思考一下这些表之间的关联方式。

假设有一个你在本科期间可能参与过（或正在设计）的研究项目。项目可能需要参与者的姓名和联系方式。因此，这些信息应当存放在一个`ADDRESS`表（地址表）中。类似地，可能还收集了参与者的某些基本人口统计数据（例如，生理性别、出生日期、社会经济地位、教育水平等）。这些数据可能存放在一个`DEMOGRAPHICS`表（人口统计表）中。

最后，可能还有定期的时序测量数据（例如，一天中不同时间的压力水平或每晚的睡眠时长），这些数据可以存放在`TIMEMETRICS`表（时间度量表）中。

这些表各自相对独立。`ADDRESS`表包含数字和文本数据；它可能不需要频繁更新。同样，`DEMOGRAPHICS`表中的初始注册信息虽然数据类型略有不同，但主要用来建立基线。相比之下，`TIMEMETRICS`表中的日常或夜间时序测量数据则可能需要频繁变动。

将这些相关但又不同的数据拆分存储有什么好处呢？为什么不直接存储在一个CSV文件中？主要有三个原因：数据大小、保密性和可靠性。

总体而言，大多数操作系统对单个文件的大小都有限制。而SQL数据库（特别是`PostgreSQL`）拥有独立的数据存储文件*以及*数据管理进程。因此，`PostgreSQL`在硬盘上的大小可以显著增大（例如，可能是1 GB与64 TB之间的差异）。虽然通过多个CSV文件也能容纳如此大的数据量，但这些文件的管理需要依靠人工努力，而不是更自动化的数据库方法和标准。

大多数共享文件夹的权限设置数量有限。因此，一个包含CSV文件的文件夹通常对所有用户开放相同的访问权限。相比之下，`PostgreSQL`可以以定制的方式创建多种角色和用户。因此，某些用户可能只具有对部分数据表的只读权限。在我们的人体受试者研究项目中，这意味着只有首席研究员可以访问`ADDRESS`表，而多个研究人员则可能需要访问`TIMEMETRICS`表。

同样，类似于文件大小的问题，并非不存在其他通过文件共享实现的替代方案。更准确地说，问题在于“如果这一切都通过标准协议进行管理，会不会更简单？”

此外，对于某些涉及频繁数据写入或操作的使用场景，并非所有文件都能妥善应对数据移动过程中的故障。`PostgreSQL`所实现的数据库标准包含了对断电或写入错误等意外情况的应对措施。按照这些更高标准存储的数据，在面对不理想事件时，存活下来的可能性更高。

那么，获得这三个特性的代价是什么呢？简而言之，每一个独立的表不仅需要包含其所存储的具体原始数据，还必须通过某种连接*键*来存储每一行数据。这个键允许将表A中的数据与表B中的数据进行关联。在我们的示例中，连接各个表的键就是`UserID`（用户ID）。

每个表至少需要一个键，在某些情况下，可能需要不止一个键列。以我们示例中的`TIMEMETRICS`表为例，该表就包含多个列共同组成键（作为每行的唯一标识符）。由于每个用户每天有多个度量数据，因此还需要包含`SurveyDay`（调查日）和可能的`SurveyStartTime`（调查开始时间）信息才能唯一标识每一行。

由于每个表都需要一个唯一键，使用数据库的代价就是需要在某种程度上提前规划表结构，以确保整个数据库以正确的方式运行。此外，从多个表访问数据需要通过键将所需表中的数据*连接*起来。这确实为数据获取增加了一层复杂性。话虽如此，大多数大型数据系统都采用这种方法进行存储。

在下一节中，我们将使用本书其中一位作者研究项目中的模拟数据，并用这些信息来填充我们的`PostgreSQL`数据库。这种数据填充遵循了前述的理论基础，旨在作为一个示例或样本，而非一个完整的解决方案。

### 10.4 向PostgreSQL添加数据

使用`JWileymisc`包中的`aces_daily`数据，我们将平面数据拆分为`ADDRESS`、`DEMOGRAPHICS`、`THRICEDAILYMETRICS`和`DAILYMETRICS`表。由于这些数据是基于真实研究的模拟数据，`ADDRESS`表进一步简化为只有一列，即一个模拟的邮政编码。为了确保你的模拟结果与我们一致，我们使用`set.seed(42020)`来保证数据相同。在本例中，我们实际上不会直接使用这些伪位置数据进行任何分析。我们还将所有表的列名转换为小写。`PostgreSQL`对变量中的大写字母可能有点挑剔：

```
aces_daily <- as.data.table(aces_daily)

ADDRESS <- aces_daily[, .(UserID)]
ADDRESS <- unique(ADDRESS)

set.seed(42020)
ADDRESS$PostalCode <- round(rnorm(nrow(ADDRESS), mean = 4000, sd = 300), 0) 
colnames(ADDRESS) <- tolower(colnames(ADDRESS))

DEMOGRAPHICS <- aces_daily[, .(UserID, Age, BornAUS, SES_1, EDU)]
DEMOGRAPHICS <- unique(DEMOGRAPHICS)
DEMOGRAPHICS[, SurrogateID := seq_len(.N)]
DEMOGRAPHICS <- DEMOGRAPHICS[, .(UserID, SurrogateID, Age, BornAUS, SES_1, EDU)]
colnames(DEMOGRAPHICS) <- tolower(colnames(DEMOGRAPHICS))

THRICEDAILYMETRICS <- aces_daily[, .(UserID, SurveyInteger,
                                     SurveyStartTimec11, STRESS, PosAff, NegAff)]
colnames(THRICEDAILYMETRICS) <- tolower(colnames(THRICEDAILYMETRICS))

DAILYMETRICS <- aces_daily[, .(UserID, SurveyDay,
                               SOLs, WASONs, SUPPORT,
                               COPEPrb, COPEPrc, COPEExp, COPEDis)]
colnames(DAILYMETRICS) <- tolower(colnames(DAILYMETRICS))

rm(aces_daily)
```

现在我们已经有了样本研究数据，是时候将这些数据保存到数据库中了。要建立数据库，我们必须将表保存其中。回想一下数据库的名称（`dbname = "postgres"`），我们使用超级用户凭证连接数据库：

```
drv <- dbDriver("PostgreSQL")
conSuper <- dbConnect(drv, dbname = "postgres", host = "localhost",
                      port = 5432, user = "postgres", password = "advancedr")
```

连接激活后，我们使用一个新函数`dbWriteTable()`将我们的四个表推送到SQL服务器。为简单起见，我们让SQL表的名称与所写入的值保持一致。每写入一个表，都会返回一个布尔值，表明写入是否成功。将四个数据表写入SQL服务器后，我们从本地R环境中移除它们。

需要注意的一点是：`PostgreSQL`的表名最好全部使用小写。因此，虽然使用这个约定可以更方便地区分表是在R中还是在`PostgreSQL`中，但严格遵守这个约定同样重要。如果你需要在服务器端使用大写表名，也有变通的方法。不过，由于本书主要不是关于SQL的，因此简单地遵循这个全小写的约定似乎更简单：

```
dbWriteTable(conn = conSuper, name = "address", value = ADDRESS)
[1] TRUE

dbWriteTable(conn = conSuper, name = "demographics", value = DEMOGRAPHICS)
[1] TRUE

dbWriteTable(conn = conSuper, name = "thricedailymetrics", value = THRICEDAILYMETRICS)
[1] TRUE

dbWriteTable(conn = conSuper, name = "dailymetrics", value = DAILYMETRICS)
[1] TRUE

rm(ADDRESS, DEMOGRAPHICS, THRICEDAILYMETRICS, DAILYMETRICS)
```

要确认写入服务器的表确实已存储在那里，可以使用`dbListTables()`函数。注意该函数接受的参数是连接到整个数据库的连接（`conSuper`）。除了确认表已上传，这个有用的函数还能帮助我们提醒数据库中包含了哪些表：

```
dbListTables(conSuper)
[1] "address" "demographics" "thricedailymetrics"
[4] "dailymetrics"
```

这就是将数据从R添加到数据库所需的全部步骤。一次处理一个表，我们可以安全地将信息存储在服务器上。