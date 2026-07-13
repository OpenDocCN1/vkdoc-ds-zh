### 第 10 章 读取大数据

PostgreSQL 集成了许多非常有用的数据库特性，包括数据可靠性措施和不同级别的用户访问控制。虽然本书主要侧重于 R，但我们将在专门章节中简要介绍足够的数据库管理信息，供希望自行搭建数据库的读者参考。尽管如此，我们的重点是练习足够的关键技能，使你能够访问和分析数据库数据。

本章需要以下包：`DBI`、`RPostgreSQL`、`data.table`、`keyring` 和 `JWileymisc`。

```r
library(RPostgreSQL)
library(DBI)
library(data.table)
library(JWileymisc)
library(keyring)

options(width = 70,
        stringsAsFactors = FALSE,
        datatable.print.nrows = 20,  ## 如果超过 20 行
        datatable.print.topn = 3,    ## 打印前 3 行和后 3 行
        digits = 2)                  ## 减少 R 打印的小数位数
```

### 10.1 在 Windows 上安装 PostgreSQL

访问 PostgreSQL 网站 [www.postgresql.org/download/windows/](http://www.postgresql.org/download/windows/) 并选择其中一个安装程序选项。我们选择了 64 位架构和版本 12.2（这是撰写本书时的最新版本）的安装程序。下载后，你应该会得到一个名为 `postgresql-12.2-2-windows-x64.exe` 的文件，这就是你的安装程序。当然，你的版本号可能高于 12.2。较新的版本很可能不会对你跟上本章内容产生重大影响。

继续安装程序，根据需要点击*下一步*按钮，并接受安装目录的默认设置。安装程序会要求为名为 `postgres` 的数据库超级用户设置密码，我们将其设置为 `advancedr`。如果你选择了不同的密码，请务必安全地记录下来。顺便提一下，这也会创建一个与超级用户同名的数据库。PostgreSQL 默认使用端口 5432，如果没有显示为默认选项，你可能已经在计算机上运行了另一个版本的 PostgreSQL。再点击几次*下一步*，接受默认设置，应该就会进入安装环节。在最后一步，可能会询问有关*堆栈生成器*的问题；**不**需要它，你可以在点击*完成*前*取消勾选*该选项。

至此，PostgreSQL 安装完成，并已在你的计算机上运行。如果你熟悉这个数据库，你可以直接向其中添加数据（并且可能不需要这个入门指南）。我们通过 R 添加数据，并快速开始访问这些数据的子集。

### 10.2 对接 R 和 PostgreSQL

我们的第一个任务是连接到数据库。这需要使用 `DBI` 包来建立与我们刚刚安装的数据库的连接。`dbDriver()` 函数是一个 DBI 函数，用于设置默认的接口指令。在这里，我们需要一个 PostgreSQL 驱动程序，它由 `RPostgreSQL` 提供。这一点值得注意，因为 DBI 是一个适用于多种数据库类型的包装器。除了 PostgreSQL，还有其他选择，其中许多都可以通过 DBI 函数进行访问。因此，无论使用何种数据库，本章的大部分内容都是通用的。对于需要与多种不同数据库交互的读者来说，通过留在 RStudio 环境中并主要使用 R 函数，可以大大减轻认知负担。实际上，在不同数据库之间切换并几乎察觉不到变化，是非常流畅的体验。`dbDriver()` 的结果被赋值给一个变量，我们称之为 `drv`。

有了正确的接口机制或驱动程序，就可以具体连接到我们的数据库了。安装 PostgreSQL 时，我们创建了一个名为 `postgres` 的超级用户，密码为 `advancedr`。此外，安装过程还创建了第一个数据库，其名称与我们的超级用户相同，即 `postgres`。这个超级用户帐户拥有数据库中所有表的完全权限。我们稍后将更深入地讨论用户权限。

要连接到这个名为 `postgres` 的数据库，我们将使用驱动程序，并且必须指定主机位置。由于我们在本地机器上安装了服务器，我们将使用 `host = "localhost"`。但是，如果你选择使用云服务（我们将在后续章节中更深入地介绍），你只需在引号内输入你的服务器网址。设置端口也很重要，你可能还记得这是安装过程的一部分。这些必须匹配，在安装过程中，我们保留了默认端口 5432。如果你使用云服务，你的端口可能也不同。最后，我们使用安装过程中设置的用户名和密码。

所有这些信息都用于 `dbConnect()` 函数，以建立到我们数据库服务器的连接。在此，我们将其命名为 `conSuper`，代表“超级用户的连接”。这将使用我们超级用户的用户名和密码创建的连接。

我们*首次*连接过程的最后一步是使用 `dbGetInfo()` 确认我们已成功连接到数据库。并非每次连接都需要使用此函数。我们在此使用它来验证所有系统是否正常运行。假设你的输出与我们的输出大致匹配，你就知道安装过程成功了：

```r
drv <- dbDriver("PostgreSQL")
conSuper <- dbConnect(drv,
                      dbname = "postgres",
                      host = "localhost",
                      port = 5432,
                      user = "postgres",
                      password = "advancedr")
dbGetInfo(conSuper)
```

# `$host`

## `[1] "localhost"`

# `$port`

## `[1] "5432"`

# `$user`
## `[1] "postgres"`

# `$dbname`

## `[1] "postgres"`

# `$serverVersion`

## `[1] "12.0.2"`


# `$protocolVersion`

## `[1] 3`

第 10 章 阅读大数据


# `$backendPId`
## `[1] 23164`

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

# 第 10 章 读取大数据

## 10.5 创建具有特定权限的用户

数据的保密性是我们提到运行数据库的三个好处之一。虽然密码保护访问是保密性的一方面，但保密性不仅仅只有密码。添加一个以上的用户可以为精确信息保护增加一个有用的层级。每个用户只能被授予特定表的特定类型的访问权限。因此，除了移除不再需要任何访问权限的用户之外，还可以微调用户的*权限*以促进数据保密性。

创建新用户需要*超级*用户的权限。请记住 PostgreSQL 是 SQL 语言生态系统的一部分。因此，除了少数 DBI 特有功能外，更高级或更精细的命令必须使用 SQL。这些 SQL 代码的通用 DBI 封装器是 `dbGetQuery()`。此函数接受两个参数。第一个是经过身份验证的连接，第二个是包裹在引号中的 SQL 代码，作为一个文本字符串。该函数既会将文本字符串发送到数据库进行处理，也会收集并将任何生成的输出返回到 R 中。

为了避免深入讨论 SQL，我们重点分享创建新用户并赋予该用户特定权限的格式。要创建新用户，格式为 `CREATE USER 用户名 WITH PASSWORD '单引号内的文本'`。我们将采用 SQL 惯例：SQL 命令大写，变量小写。

这里，我们创建三个新的研究生用户来协助我们的研究项目。第一个用户我们只授予*读取*权限（并且只在某个特定表上）。第二个用户将获得*插入*权限（并且只在某个特定表上）。我们的第三个用户能够*更新*和读取一个表。当然，在实际生活中，你可以授予单个用户对任何表组合更多或更少的访问权限。这里，我们将保持用户权限相当简单：

`dbGetQuery(conSuper, "CREATE USER gradstudentr WITH PASSWORD 's3cur3'")`

`dbGetQuery(conSuper, "CREATE USER gradstudenti WITH PASSWORD 'saf3'")`

`dbGetQuery(conSuper, "CREATE USER gradstudentu WITH PASSWORD 'sur3'")`

# 第 10 章 读取大数据

一旦你创建了一个或多个用户，就该将特定权限 `GRANT`（授予）给特定表上的特定用户。权限或权利选项包括用于只读访问的 `SELECT`，以及用于两种不同写入访问类型的 `UPDATE` 或 `INSERT`。`INSERT` 授予向现有表中添加新行的能力。`UPDATE` 授予更改表中现有行中值的能力。然而，因为通常只需要更新某些行，`SELECT` 权限会有所帮助：

```
dbGetQuery(conSuper, "GRANT SELECT ON dailymetrics TO gradstudentr;")
```

## 数据框，0 列 0 行

```
dbGetQuery(conSuper, "GRANT INSERT ON address TO gradstudenti;")
```

## 数据框，0 列 0 行

```
dbGetQuery(conSuper, "GRANT SELECT, UPDATE ON address TO gradstudentu;")
```

## 数据框，0 列 0 行

关于前面代码中的 SQL 结构，有一种语法需要注意。布局是 `GRANT **权限** ON **表名** TO **用户名**`。SQL 允许使用逗号，`权限`的选项可以是 `SELECT`、`UPDATE` 或 `INSERT` 的任意组合。

现在我们有了三个新用户，必须将它们连接到我们的数据库。在实际生活中，在单个 R 实例中，很少（即使有过）会拥有超过一个到同一数据库的连接。相反，每个用户通常代表一个具有不同数据需求/角色的不同人员。请注意在前面的两个代码块中，可以 `CREATE` 任意用户组合，并且可以在任何表组合上 `GRANT` 任何权限组合。`GRANT` 不是一次性事件——随着时间的推移，用户可能会根据具体情况获得或失去访问权限。在我们大学研究实验室的例子中，新学生可能只有对少数几个表的只读访问权限。随着研究的深入，学生可能需要访问研究的更多领域，甚至在执行自己的工作时需要写入权限。

话虽如此，用户权限的最佳实践是基于*角色*，而非资历。无论如何，我们将两个新连接命名为 `conRead` 和 `conWrite`。在你继续阅读本节剩余部分时，请仔细注意使用的是*哪个*用户的连接：

# 第 10 章 读取大数据

## 数据库连接与访问权限

```
conRead <- dbConnect(drv, dbname = "postgres", host = "localhost",port = 5432, user = "gradstudentr", password = "s3cur3" )

conInsert <- dbConnect(drv, dbname = "postgres", host = "localhost",port = 5432, user = "gradstudenti", password = "saf3" )

conUpdate <- dbConnect(drv, dbname = "postgres", host = "localhost",port = 5432, user = "gradstudentu", password = "sur3" )
```

现在这三个新的连接（具有不同级别的访问权限）已建立，我们的首要任务是了解每个用户能做什么。与我们的超级用户类似，所有用户都可以使用`dbListTables()`来查看数据库中的所有表。这可能看起来有悖常理，因为这些用户`只`被授予了每个表上的特定权限。在我们创建的通用数据库结构中，任何用户都能看到数据库中的表名：

`dbListTables(conRead)`

## 1 "demographics" "thricedailymetrics" "dailymetrics"

## 4 "address"

`dbListTables(conInsert)`

## 1 "demographics" "thricedailymetrics" "dailymetrics"

## 4 "address"

`dbListTables(conUpdate)`

## 1 "demographics" "thricedailymetrics" "dailymetrics"

## [4] "address"（地址表）

尽管我们的用户能够`查看`表名，但他们并`没有`超级用户那样的权限。他们无法删除表。虽然我们这里还没有为超级用户演示这一点，但请放心，`dbRemoveTable()`对该用户是有效的。现在，我们完全有理由认为，仅拥有只读权限且作用域仅限于`dailymetrics`表的`gradstudentr`用户，无法删除表。

然而，请特别注意我们的`gradstudenti`和`gradstudentu`用户，他们虽然对`address`表拥有`UPDATE`或`INSERT`权限，但同样无法删除该表。这很可能是一个很好的实践范例，说明为什么数据库可能比平面表格文件（例如CSV）更优越。

虽然这些用户拥有一定程度的访问权限，但他们进行大规模更改（无论是意外的还是故意的）的能力是受限的：

```
dbRemoveTable(conRead, "address")
## Error in postgresqlExecStatement(conn, statement, ...) :
## RS-DBI driver: (could not Retrieve the result : ERROR: must be owner of table address
## )
## Warning in postgresqlQuickSQL(conn, statement, ...): Could not create
execute: DROP TABLE "address"
## [1] TRUE
```

```
dbRemoveTable(conInsert, "address")
## Error in postgresqlExecStatement(conn, statement, ...) :
## RS-DBI driver: (could not Retrieve the result : ERROR: must be owner of table address
## )
## Warning in postgresqlQuickSQL(conn, statement, ...): Could not create
execute: DROP TABLE "address"
## [1] TRUE
```

```
dbRemoveTable(conUpdate, "address")
## Error in postgresqlExecStatement(conn, statement, ...) :
## RS-DBI driver: (could not Retrieve the result : ERROR: must be owner of table address
## )
## Warning in postgresqlQuickSQL(conn, statement, ...): Could not create
execute: DROP TABLE "address"
## [1] TRUE
```

我们的用户能够列出所有数据库表的`名称`，但不能删除任何表。到目前为止一切顺利。不过，这些功能并不那么激动人心。实际的表访问权限如何呢？请记住，每个用户拥有一项或多项能力：`SELECT`（查询）、`UPDATE`（更新）或`INSERT`（插入）。是时候学习一点SQL了。我们将在本节的后面再次讨论这些结构的具体细节。然而，SQL是一种非常直白的语言，因此即使我们还没有讨论确切含义，通过这些简单命令所发生的事情也应该是可以理解的。

我们的`gradstudentr`用户对`dailymetrics`表拥有`SELECT`权限。使用我们的R封装函数`dbGetQuery()`，我们向数据库服务器发送了两条略有不同的SQL语句。每条语句的唯一区别在于所操作的表。请注意，一条语句生效了，而另一条没有。原因正是在于我们的`conRead`连接仅拥有`gradstudentr`的权限，而这些权限只对`dailymetrics`表有效。`*`是表示列名的通配符。因此，被允许的表的所有列名都会被显示出来。

此命令将不会生效。我们的用户没有从`demographics`表进行查询的权限：

```
dbGetQuery(conRead, "SELECT * FROM demographics;") #does not work
## Error in postgresqlExecStatement(conn, statement, ...) :
## RS-DBI driver: (could not Retrieve the result : ERROR: permission denied for table demographics
## )
## Warning in postgresqlQuickSQL(conn, statement, ...): Could not create
execute: SELECT * FROM demographics;
## NULL
```

此命令将会生效。我们的用户确实有权限从`dailymetrics`表进行查询。为了节省文本空间，我们将结果赋值给变量`query`，然后仅显示前几行：

```
query <- dbGetQuery(conRead, "SELECT * FROM dailymetrics;")
head(query)
## row.names userid surveyday sols wasons support copeprb copeprc
## 1 1 1 2017-02-24 NA NA NA NA NA
## 2 2 1 2017-02-24 0.0 0 7.0 2.3 2.4
## 3 3 1 2017-02-25 NA NA NA NA NA
## 4 4 1 2017-02-25 NA NA NA NA NA
## 5 5 1 2017-02-25 6.9 0 6.2 NA NA
## 6 6 1 2017-02-26 NA NA NA NA NA
## copeexp copedis
## 1 NA NA
## 2 2.4 2.2
## 3 NA NA
## 4 NA NA
## 5 2.0 NA
## 6 NA NA
```

我们的`gradstudenti`用户仅对`address`表拥有`INSERT`权限；而`gradstudentu`用户对`address`表同时拥有`SELECT`和`UPDATE`权限。

使用我们的R封装函数`dbGetQuery()`，我们向数据库服务器发送了两条相同的SQL语句。每条语句的唯一区别在于用户。请注意，一条语句生效了，而另一条没有。原因正是在于我们的`conInsert`连接拥有`gradstudenti`的权限，这些权限仅适用于在`address`表中添加一行或`插入`数据。该用户没有`SELECT`权限。

虽然这些不同的用户权限在此例中略有编造成分，但它们展示了不同级别的访问权限。通过利用权限的不同组合，您很可能能够找到适合您需求的可行的安全与访问级别。

此命令将不会生效。我们的用户仅拥有对`address`表执行`INSERT`（例如，将新行绑定到表的末尾）的权限：

```
dbGetQuery(conInsert, "SELECT * FROM address;")
## Error in postgresqlExecStatement(conn, statement, ...) :
## RS-DBI driver: (could not Retrieve the result : ERROR: permission denied for table address
## )
## Warning in postgresqlQuickSQL(conn, statement, ...): Could not create
execute: SELECT * FROM address;
## NULL
```

此命令将会生效。`gradstudentu`用户拥有对`address`表执行`SELECT`和`UPDATE`的权限。为了节省文本空间，我们将结果赋值给变量`query`，然后仅显示最后几行：

```
query <- dbGetQuery(conUpdate, "SELECT * FROM address;")
tail(query)
## row.names userid postalcode
## 186 186 186 3807
## 187 187 187 4118
## 188 188 188 3843
## 189 189 189 3952
## 190 190 190 4181
## 191 191 191 4582
```

那么`gradstudenti`究竟`可以`做什么呢？他们可以向`address`表执行`INSERT`（插入）操作。命令是`INSERT INTO table VALUES (list, values, here, separated, by, commas, one, value, for, each column)`。因为我们的`gradstudenti`用户无法查询和查看表中的内容，我们使用超级用户来查看更新后的表值：

```
dbGetQuery(conInsert, "INSERT INTO address VALUES (200, 200, 9999)")
## data frame with 0 columns and 0 rows

query <- dbGetQuery(conSuper, "SELECT * FROM address")
tail(query)
## row.names userid postalcode
## 187 187 187 4118
## 188 188 188 3843
## 189 189 189 3952
## 190 190 190 4181
## 191 191 191 4582
## 192 200 200 9999
```

请注意，我们的表现在有192行，而不只是191行。

那么`gradstudentu`用户究竟`可以`向表中写入什么呢？他们可以对`address`表执行`SELECT`或`UPDATE`操作。

请记住，`address`表包含`userid`和`postalcode`列。这些`userid`对应的是我们的研究参与者。假设第一个参与者更改了地址。为了便于可视化，假设他们的邮政编码变为了`1111`。

这里，我们在SQL语句中添加了一个额外的短语：`WHERE userid = 1`。这允许我们只显示关于第一个参与者的行。虽然这对于我们的`SELECT`语句来说是一个有用的节省空间的手段，但对于我们的`UPDATE`语句来说则是至关重要的（否则，**所有**行的`postalcode`都会被更新为`1111`）：

```
dbGetQuery(conUpdate, "SELECT * FROM address WHERE userid = 1")
## row.names userid postalcode
## 1 1 1 4112

dbGetQuery(conUpdate, "UPDATE address SET postalcode = 1111 WHERE userid = 1")
## data frame with 0 columns and 0 rows

dbGetQuery(conUpdate, "SELECT * FROM address WHERE userid = 1")
## row.names userid postalcode
## 1 1 1 1111
```

在展示了具有不同权限的用户的一些细微差别之后，我们通过演示如何撤销用户的访问权限来结束本节。在完全删除或`DROP`（移除）一个用户之前，必须先撤销其所有访问权限。然而，请注意，`REVOKE`（撤销）语句与`GRANT`（授予）语句非常相似，可以进行自定义。换句话说，可以根据任何给定时间点的具体需求，定期修改用户的权限。

首先，我们使用`dbDisconnect()`断开三个用户的连接，并从全局环境中清除这些变量：

```
dbDisconnect(conRead)
## [1] TRUE

dbDisconnect(conInsert)
## [1] TRUE

dbDisconnect(conUpdate)
```

# 第 10 章 读取大数据

请注意，我们保留超级用户不动。这是为了对数据库进行最终修改所必需的。虽然在本例中，我们确实针对每个表上的每个用户专门执行了`REVOKE`（撤销）其被授予的权限（再次为了演示权限可以更动态），但我们也可以使用`ALL`来代替权限列表：

`dbGetQuery(conSuper, "REVOKE SELECT ON dailymetrics FROM gradstudentr;")`

```
## 包含 0 列和 0 行的数据框
```

`dbGetQuery(conSuper, "REVOKE INSERT ON address FROM gradstudenti;")`

```
## 包含 0 列和 0 行的数据框
```

`dbGetQuery(conSuper, "REVOKE SELECT, UPDATE ON address FROM gradstudentu;")`

```
## 包含 0 列和 0 行的数据框
```

现在我们的用户已经没有权限了，我们可以从数据库中`DROP USER`（删除用户）。此后，他们将不复存在，也无法再连接到服务器。他们的记录已被移除：

`dbGetQuery(conSuper, "DROP USER gradstudentr")`

```
## 包含 0 列和 0 行的数据框
```

`dbGetQuery(conSuper, "DROP USER gradstudenti")`

```
## 包含 0 列和 0 行的数据框
```

`dbGetQuery(conSuper, "DROP USER gradstudentu")`

```
## 包含 0 列和 0 行的数据框
```

为了证实这一点，我们展示一个示例：如果其中一位用户在被`DROP`（删除）后尝试重新连接到服务器，他们可能会看到如下情况：

`dbConnect(drv, dbname = "postgres", host = "localhost", port = 5432, user = "gradstudentr", password = "s3cur3")`

```
## 错误在于 postgresqlNewConnection(drv, ...): RS-DBI 驱动: (无法连接
## gradstudentr@localhost:
## 用户"gradstudentr"密码认证失败
## )
```

如你所见，一个数据库可以有多个用户，他们对数据库中任何表集拥有不同级别的访问权限。管理员或超级用户可以根据需要添加和删除用户，也可以更改他们的访问权限。

## 10.6 密码安全

值得注意的是，在我们之前的数据库连接示例中，我们采用了一种相当不安全的方式，即以明文编写密码。更糟的是，密码并非从我们电脑上的文件中读取，而是直接写在代码里。仅仅分享这段代码就会泄露我们的密码。

大多数操作系统，包括 Windows、Mac OS 和 Unix，都有一个更安全的区域来存储用户名和密码。数字安全技术变化迅速，很可能存在其他更简单或更安全的方法来存储密码和验证数据库访问。尽管如此，我们提供一种可能的解决方案，它至少可以防止你的代码中硬编码明文密码。

R 包`keyring`有一组函数，可以将特定服务和用户的密码存储在你的操作系统中更安全的区域。需要注意的是，这个密码在另一台机器的不同操作系统上将不可用。

为了演示，我们将断开超级用户的连接，将该密码存储在密钥库中，并展示如何使用密钥库进行连接，为你的数据库访问增加一层安全性：

`dbDisconnect(conSuper)`

```
## [1] 真值
```

`rm(conSuper)`

函数`key_set_with_value`接受四个参数。`service`（服务）可用于组织不同服务的不同用户。在我们的例子中，我们将其称为`advancedrBookSQLch10`数据库。`username`（用户名）将用于从密钥库中检索特定用户的密码。在本例中，我们使用超级用户的用户名`postgres`。最后，我们使用超级用户的密码`advancedr`。

值得注意的是，虽然这创建了初始记录，但如果用不同的密码运行相同的代码，则只会更新密码：

`key_set_with_value(service = "advancedrBookSQLch10", username = "postgres", password = "advancedr")`

设置好密码后，我们可以通过使用`key_list()`函数来确认已创建了一条新的密钥库记录。注意，我们的函数不会显示密码——它仅显示已存储了一条记录：

`key_list()`

```
##     service            username
## 1 advancedrBookSQLch10 postgres
```

请注意，上述代码确认了我们的操作系统现在正在为我们记住密码。通常，无需多次运行前两个代码块。只有在需要将密码添加到新的本地系统或更新密码时，才需要使用那段代码。

更常见的做法是，为了获取密码使用，通过`key_get()`函数将密码保存到一个局部变量中，例如`usrPassword`。服务名称和用户名必须与记录中的匹配。现在，重要的是要理解密码是以明文形式存储在你的全局 R 环境中的。有人从你身后偷看仍然可能看到你的密码！以下两个代码块是每个会话中为了连接数据库而最常运行一次的代码：

`usrPassword <- key_get(service = "advancedrBookSQLch10", username = "postgres")`

现在 R 可以访问你的密码，我们再次运行连接协议。唯一的不同之处在于，这次我们不再使用引号内的明文密码，而是使用变量名`password = usrPassword`。一旦连接建立，我们就从全局环境中删除该密码。但请注意，这并非安全删除；它只是防止旁观者目视到密码：

`conSuper <- dbConnect(drv, dbname = "postgres", host = "localhost", port = 5432, user = "postgres", password = usrPassword)`

`rm(usrPassword)`

连接已重新建立，我们已准备好进行任何所需的工作。以防你之后需要从操作系统中删除密码和记录，我们展示以下代码。同样，类似于添加密钥，这是一次性为每个系统执行的代码：

`key_delete("advancedrBookSQLch10", "postgres")`

`key_list()`

```
## [1] service username
## <0 行> (或 0 长度的行名)
```

## 10.7 用 R 执行 SQL

`DBI`包提供了一些函数，使我们无需直接使用 SQL 也能获得结果。我们之前已经介绍过`dbListTables()`函数，它可以显示数据库中的所有表。`dbListFields`函数类似于 R 的`colnames()`：

`dbListTables(conSuper)`

```
## [1] "demographics"         "thricedailymetrics"   "dailymetrics"
## [4] "address"
```

`dbListFields(conSuper, "thricedailymetrics")`

```
## [1] "row.names"          "userid"             "surveyinteger"
## [4] "surveystarttimec11" "stress"             "posaff"
## [7] "negaff"
```

这些函数的一个优点是它们名副其实。虽然我们之前展示了如何使用纯 SQL 获取表的输出，但还有一个用于读取表的函数`dbReadTable()`。决定使用多少 SQL，以及在服务端执行哪些操作与在 R 的内存端执行哪些操作，是一种权衡。如果这是你第一次使用第二种编程语言，你可能会在这两个世界之间慢慢摸索前进。

如果你经常使用 SQL 数据库，就像我们的一位作者几年前开始做的那样，你可能会发现，随着时间的推移，某些操作在服务端完成更有意义。

在大多数情况下，`dbGetQuery(conSuper, "SELECT * FROM thricedailymetrics")`和我们下面的函数之间没有显著区别：

`query <- dbReadTable(conSuper, "thricedailymetrics")`

`head(query)`

```
##   userid surveyinteger surveystarttimec11 stress posaff negaff
## 1      1             2              1.9e-01      5    1.5    1.7
## 2      1             3              4.9e-01      1    1.5    1.0
## 3      1             1              1.2e-05      1    1.6     NA
## 4      1             2              1.9e-01      2    1.6    1.4
## 5      1             3              4.1e-01      0    1.1    1.0
```

## 6.1.1. `1.6e-02` `0` `1.3` `1.7`

最后，虽然我们已经向数据库写入了几个表格，但我们再次提醒你注意`dbWriteTable()`函数。该函数接受`overwrite`和`append`两个选项，可设置为`FALSE`或`TRUE`。这类似于`UPDATE`与`INSERT`。在合适的情况下，两者都很方便。我们首先将`iris`数据集写入表格，展示这两个变量的*默认*选项。注意，如果我们第二次运行该函数，它会返回一个错误：

```
dbWriteTable(conn = conSuper, name = "iris", value = iris, overwrite =
FALSE, append = FALSE)
```

```
## [1] TRUE
```

```
dbWriteTable(conn = conSuper, name = "iris", value = iris, overwrite =
FALSE, append = FALSE)
```

```
## Warning in postgresqlWriteTable(conn, name, value, ...): table iris
exists in database: aborting assignTable
## [1] FALSE
```

如果你希望在数据框末尾追加（例如`INSERT`）数据，只需设置`append = TRUE`。另一方面，如果需要完全覆盖（例如`UPDATE`所有元素），则设置`overwrite = TRUE`。

回忆一下基本的 SQL 查询：`SELECT`特定列，`FROM`特定表。我们使用`dbGetQuery()`将查询发送到 PostgreSQL 并执行。通过这种方式，数据库中的表可以被导入到 R 中：

第 10 章 读取大数据

```
query <- dbGetQuery(conSuper,
statement = "SELECT *
FROM address")
query <- as.data.table(query)
head(query)
```

```
## row.names userid postalcode
## 1: 2 2 4132
## 2: 3 3 3880
## 3: 4 4 3781
## 4: 5 5 4058
## 5: 6 6 4627
## 6: 7 7 3996
```

记住，`*`是一个通配符。如果我们只需要一列数据，我们会`SELECT`该特定列，而不是使用通配符值：

```
query <- dbGetQuery(conSuper,
"SELECT postalcode
FROM address")
query <- as.data.table(query)
head(query)
```

```
## postalcode
## 1: 4132
## 2: 3880
## 3: 3781
## 4: 4058
## 5: 4627
## 6: 3996
```

回想一下，我们曾（在`UPDATE`命令时）在列值上使用了`WHERE`。请记住，这同样可以轻松地在 R 中使用`query[userid = 1]`来实现。

然而，也许在 SQL 而非 R 中使用它的一个原因是内存大小。某些表可能非常大，通过只选择特定行，可以减少对 RAM 的占用：

第 10 章 读取大数据

```
query <- dbGetQuery(conSuper,
"SELECT postalcode
FROM address
WHERE userid = 1")
query <- as.data.table(query)
head(query)
```

```
## postalcode
## 1: 1111
```

正如我们在分配权限时所见，在 SQL 中可以使用逗号一次处理多个项目。在此例中，我们使用更高级的`WHERE`子句从`thricedailymetrics`表中选择两列：

```
query <- dbGetQuery(conSuper,
"SELECT userid, stress
FROM thricedailymetrics
WHERE surveyinteger >= 2")
query <- as.data.table(query)
head(query)
```

```
## userid stress
## 1: 1 5
## 2: 1 1
## 3: 1 2
## 4: 1 0
## 5: 1 3
## 6: 1 1
```

PostgreSQL 的一个用途可能是处理超过物理 RAM 容量的大数据。它可以像我们演示的那样分块读取，或者我们可以将大量数据从 R 推送到数据库以释放内存。我们的下一个示例提供了一个选择。如果你的系统 RAM 和硬盘有足够的可用空间，请按原样进行 2 亿行的示例。否则，将`rnorm()`中的变量数量改为较小的值，也许是一个 7000 万行的较小示例。

第 10 章 读取大数据

以下代码创建了 2 亿个完全虚构的数据观测值，均值为 1，默认标准差为 1。如你所见，其大小约为 1.6 GB（将鼠标悬停在 RStudio 全局环境窗格中的值上即可查看）。当然，数据可能更大，但这作为测试案例来查看它在你系统上的表现已经足够了。运行可能需要几分钟，所以请稍作等待：

```
BiggerData <- rnorm(200000000, 1)
BiggerData <- as.data.table(BiggerData)
dbWriteTable(conSuper, "biggerdata", BiggerData)
```

```
## [1] TRUE
```

```
rm(BiggerData)
dbListTables(conSuper)
```

```
## [1] "demographics" "thricedailymetrics" "dailymetrics"
## [4] "address" "iris" "biggerdata"
```

同样，这可能需要几分钟在你的系统上运行。但是，如果成功了，你就能体会到有多少数据可以传输到数据库中。由于我们不需要这个`BiggerData`（除了作为系统的“压力测试”），我们使用`dbRemoveTable()`函数：

```
dbRemoveTable(conSuper, "biggerdata")
```

```
## [1] TRUE
```

```
dbListTables(conSuper)
```

```
## [1] "demographics" "thricedailymetrics" "dailymetrics"
## [4] "address" "iris"
```

虽然我们（至少拥有超级用户权限）能够删除一张表，但这并不一定会释放 PostgreSQL（以及硬盘）中的空间。现在，有了所有可用的额外空间，向数据库写入新数据不会占用更多磁盘空间。但是，如果我们需要强制清理“死”字节，SQL 有一个`VACUUM FULL`操作，可以将数据库缩减到仅包含活动数据。在我们的案例中，这不会花太长时间。我们的结构很简单。更复杂的数据库和服务器可能会发现此操作的代价较高。因此，这是一种数据库维护操作，可能仅定期或根据需要执行：

第 10 章 读取大数据

```
dbGetQuery(conSuper, "VACUUM FULL")
```

```
## data frame with 0 columns and 0 rows
```

至此，我们已经学习了很多关于在 R 中使用 SQL 数据库的知识。虽然 SQL 本身是一种语言，但这足以让高级 R 用户开始进行数据分析和数据维护了。

我们加入这倒数第二段代码仅为了完整性。通过从 SQL 数据库中删除这些表，你现在可以重新运行之前的所有代码并获得相同结果。否则，尝试写入一张表（就像我们在本章早期填充数据库时所做的那样）会导致错误：

```
dbRemoveTable(conSuper, "address")
```

```
## [1] TRUE
```

```
dbRemoveTable(conSuper, "demographics")
```

```
## [1] TRUE
```

```
dbRemoveTable(conSuper, "thricedailymetrics")
```

```
## [1] TRUE
```

```
dbRemoveTable(conSuper, "dailymetrics")
```

```
## [1] TRUE
```

```
dbRemoveTable(conSuper, "iris")
```

```
## [1] TRUE
```

```
dbGetQuery(conSuper, "VACUUM FULL")
```

```
## data frame with 0 columns and 0 rows
```

要关闭此连接，需要两步而不是一步，因为我们必须断开与 PostgreSQL 的连接并卸载允许 DBI 建立该连接的驱动程序。如果成功，两者都将返回`TRUE`：

第 10 章 读取大数据

```
dbDisconnect(conSuper)
```

```
## [1] TRUE
```

```
dbUnloadDriver(drv)
```

### 1. TRUE

`rm(conSuper, drv, query)`

至此，我们完成了 PostgreSQL 的演示。真正让您目前掌握的知识发挥更大作用的方法，是进一步深入 SQL 语法，在更复杂的查询中使用 `dbGetQuery()`。不过，就目前而言，一个足够小的数据量应该能够读入 R 中，以便进行一些分析。

## 10.8 小结

本章介绍了如何使用 R 直接访问 SQL 数据库。虽然许多功能并非为数据整理而设计，但 R 仍然提供了一种快速处理大数据的方法。这类数据集似乎变得越来越常见，虽然让研究人员熟悉每一个数据库可能不切实际，但这并非必需。

使用 R，完全有可能提取出包含所有感兴趣项目且足够小（能放入工作内存）的数据子集。熟练的 R 用户（例如本书的读者）可以进一步在 R 本身中精简数据，并据此执行所需的分析。

表 10-1 总结了本章的关键函数和短语。

第 10 章 读取大数据

*表 10-1. 本章所述关键函数及其功能摘要*

| 函数                   | 功能说明                                                     |
| ---------------------- | ------------------------------------------------------------ |
| `dbConnect()`          | 使用 `dBi` 包及驱动程序连接到 PostgreSQL 数据库。            |
| `dbDriver()`           | 连接 PostgreSQL（及其他数据库）所需的驱动程序。                    |
| `dbWriteTable()`       | 将数据从 R 写入 PostgreSQL（用户必须拥有写入权限）。         |
| `dbListTables()`       | 列出数据库中的所有表。                                       |
| `dbListFields()`       | 列出数据库中的所有字段（列名）。                             |
| `dbGetQuery()`         | 向 PostgreSQL 数据库发送一个 SQL 查询并在那里执行。             |
| `dbRemoveTable()`      | 从 PostgreSQL 中删除一个表（用户必须拥有权限）；不一定会执行 `VACUUM`。 |
| `dbDisconnect()`       | 关闭 `dBi` 包与 PostgreSQL 的连接。                          |
| `dbUnloadDriver()`     | 卸载 PostgreSQL 驱动程序。                                   |
| `key_set_with_value()` | 使用 `keyring` 包存储密码。                                |
| `key_list()`           | 列出存储在操作系统密钥库中的密钥。                           |
| `key_get()`            | 从操作系统密钥库中检索指定的密钥。                           |
| `CREATE`               | 用于创建用户的 SQL 语句。                                     |
| `DROP`                 | 用于删除用户的 SQL 语句（例如，删除）。                      |
| `GRANT`                | 用于授予用户特定权限的 SQL 语句。                            |
| `REVOKE`               | 用于撤销用户某些权限的 SQL 语句。                            |
| `SELECT`               | 用于从表中选择数据的 SQL 语句。                              |
| `UPDATE`               | 用于更新表内数据的 SQL 语句。                                |
| `INSERT`               | 用于向表追加数据的 SQL 语句。                                |

