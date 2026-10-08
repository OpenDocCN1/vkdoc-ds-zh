# 4. 使用 Spark SQL 和 DataFrame API 进行数据转换

上一章介绍了如何使用 Docker 和 Apache Zeppelin 来驱动你的 Spark 探索。你学会了通过应用显式模式，将松散结构的数据转换为可靠、自文档化且最重要的是高度结构化的数据。你编写了第一个端到端的 ETL 任务，从而能够以可靠的方式编码从原始数据到结构化数据的整个过程。然而，我们探讨的过程仅仅是个开始，可以看作是数据转换管道中众多步骤的第一步。我们之所以从原始数据转换开始，原因很简单——你摄入数据管道的数据，极有可能源自数据湖。

本章将继续聚焦于数据转换的过程。你将从一个练习开始，探索如何使用简单的选择和投影来过滤和重塑数据。之后，我们将探讨如何使用连接从多个数据源组合出新的数据视图，而无需编写复杂的代码。最后，我们将研究使用嵌套选择（内层和外层选择）、表达式列和选择表达式来解决问题的直观方法。在本章中，我们将探讨如何使用 Spark SQL 和 DataFrame API 来表达这些转换。

## 数据转换

与任何工具一样，你使用得越多，就会变得越熟练。这有时被称为磨练技能。每当我们用自己最喜欢的编程语言编写代码、学习和记忆跨框架和 API 的通用使用模式、以及解决新问题时，我们都在磨练或精进自己的技能。为了应对你遇到的问题，你必须了解自己有哪些工具可用，并理解如何利用过去解决过的类似问题来正面攻克这些难题。

我们都知道，生活中的大多数问题没有放之四海而皆准的解决方案，这在数据转换领域也同样适用。然而，许多有效的数据问题解决技巧，都是从降低数据的规模和复杂性开始的。这可以发生在数据管道的许多不同步骤中，甚至发生在单个应用程序的逻辑阶段。它通常依赖于选择和投影这两种基本技术来完成工作。

### 基本数据转换

在上一章中，我们针对 CoffeeCo 的任务，在简单的咖啡数据集（咖啡品牌和烘焙程度）上完成了一些入门任务。本着连续性的精神，我们将继续处理这家咖啡公司相关任务中的问题（我想这意味着我们还在职！）。

**练习内容**

你可以在本书 GitHub 仓库的 `ch-04/` 目录下找到本章所需的材料：[`https://github.com/newfront/spark-moderndataengineering/tree/main/ch-04`](https://github.com/newfront/spark-moderndataengineering/tree/main/ch-04)。只需使用运行脚本启动 Zeppelin，然后访问 `http://localhost:8080` 即可。

```
cd ch-04/docker && ./run.sh start
```

### 练习 4-1：选择与投影

首先，打开名为“4_1_DataSelectionAndProjection”的笔记。你会在屏幕顶部看到一个名为“数据生成”的段落。继续运行该段落以初始化 Spark。本练习的其余部分将在此基础上进行。

**注意**

你可以像我们在第 3 章中所做的那样，通过添加新段落并在 Zeppelin 中运行每个步骤来跟随本练习。如果你想查看最终的笔记本，它位于 `complete/4_1_DataSelectionAndProjectionFinal`。

#### 数据生成

CoffeeCo 规模不大，对我们来说幸运的是，只有几家主要门店。首先，我们将创建一个名为 stores 的临时 SQL 视图，以代表我们公司的旗舰店（参见代码清单 4-1）。如果你正在 Zeppelin 环境中跟随操作，请继续运行该段落。

```
%spark
case class Store(
name: String,
capacity: Int,
opens: Int,
closes: Int)
val stores = Seq(
Store("a", 24, 8, 20),
Store("b", 36, 7, 21),
Store("c", 18, 5, 23)
)
val df = spark.createDataFrame(stores)
df.createOrReplaceTempView("stores")
```

代码清单 4-1
生成门店数据和临时 SQL 视图

从代码清单 4-1 的代码中可以看出，我们只是生成了一个门店序列（列表）。每家门店在 24 小时制下，营业时间介于早上 5 点到晚上 11 点之间，并且还包含一些关于容量限制的信息。数据生成完毕后，让我们来学习数据选择的过程。

#### 选择

选择过程可以说是减少你正在处理的数据规模的最基本方法。任何具备 SQL 基础知识的人都会熟悉这个概念。简而言之，选择使我们能够通过一个条件来减少查询返回的行集。

现在让我们从基础开始，稍后在本章“再谈选择”部分，我们将探讨更广泛的选择能力。假设我们想找出所有在特定时间或之后营业的门店。

我们可以通过对 stores 视图执行一个简单的条件查询来解决这个问题。代码清单 4-2 中的代码片段展示了这个查询。

```
%spark
val q = spark.sql("select * from stores where closes >= 22")
q.show()
```

代码清单 4-2
通过简单选择，仅返回满足条件 `closes >= 22` 的行

鉴于只有一家门店在晚上 10 点后关门（门店 C），这个问题只有一个答案。

#### 过滤

如果选择过程让你感觉有点像过滤，那你的感觉是对的。事实上，如果你看看如何使用 DataFrame 来回答同样的问题，你会发现我们可以互换地使用 filter 或 where 函数。

**提示**

当我们在 DataFrame 中选择一列时，有几种方法可以标识该列。有四种不同的方式可以为选择提供目标列。符号别名 `'` 和 `$` 是隐式转换，可以通过从 SparkSession 导入隐式函数来使用：

`df.where(df("closes") >= 22)`

`df.where(col("closes") >= 22)`

`df.where('closes >= 22)`

`df.where($"closes" >= 22)`

请随意使用 Zeppelin 中代码清单 4-3 的代码片段来探索这些选项。

```
%spark
import org.apache.spark.sql.functions._
import spark.implicits._
val filter = df.filter($"closes" >= 22)
val where = df.where('closes >= 22)
filter.show()
where.show()
```

代码清单 4-3
where 子句与 DataFrame 的 filter 函数可互换

那么，如果过滤在功能上等同于选择，并且该过程使我们能够减少查询返回的数据行数，那么你可能会问自己，在我们的 `select * from stores` 中的 `select *` 到底在做什么？嗯，有趣的是，这个过程被称为投影！

## 投影

如果说选择是减少查询返回的总行数的过程，那么你可以将投影视为减少查询返回的总列数的过程。这可以作为一种优化技术，因为当只需要查询结果（或答案）所需的列子集时，返回所有行的所有列会带来内存成本。

让我们通过 stores 视图中的数据，来看一个同时使用投影和选择的示例。

假设我们要查找所有最小容量大于 20 的店铺。在这种情况下，我们可以假设无需关心店铺的营业或打烊时间，而只需找到店铺的名称（参见代码清单 4-4）。

```
%spark
// 查找所有容量大于 20 的店铺
val pq = spark.sql(
"select name from stores where capacity > 20")
pq.show()
```

代码清单 4-4
通过投影减少查询返回的列数

代码清单 4-4 中的查询展示了如何同时使用投影和选择。投影决定了查询将返回哪些列，如 `select name` 所示，它指示 Spark 仅返回名为 `name` 的列。查询的选择部分（一种高级过滤器或条件谓词）决定了哪些行满足条件被查询返回，如 `where capacity > 20` 所示。

让我们看看如何直接使用 DataFrame API 构建相同的查询（参见代码清单 4-5）。

```
%spark
df
.select("name")
.where('capacity > 20)
.show
```

代码清单 4-5
使用函数进行 DataFrame 投影和选择

混合使用投影和选择的过程可以高效地对数据进行转换。到目前为止，你看到的只是基础知识，但你可以利用这个基础，在充分发挥选择能力时，探索一些更强大的功能。

## 练习 4-1：总结

在本章之前的内容中，我们研究了通过基本选择过程减少查询返回的总行数的简单方法。我们还研究了如何通过列投影减少查询返回的列数。虽然许多用例都属于这种简单的过滤和转换操作（例如基本报表），但就可能性而言，我们仅仅触及了冰山一角。


选择远不止条件示例，它使你能够使用许多运算符编写转换查询，这些运算符通过使用常见运算符，涵盖了传统 SQL（OLTP）和更具分析性的查询（OLAP）处理。随着本章的深入，我们将探讨使用连接、列和表达式别名、条件选择表达式甚至嵌套查询的过程。


# 连接

在现实世界中，大多数数据集并非以完美形式出现，以便解决你被分配的问题。如果你碰巧偶然发现了完全符合你需求的完美数据集，请放下一切，立即感谢那些有远见并理解人们未来将如何使用其数据的人！更可能的情况是，你必须将来自几个不同来源的数据拼接在一起，才能创建解决当前问题所需的数据表示。

连接在数据管道中很常见，是组合数据的解决方案。这些工作流程属于 ETL 的范畴，当你需要策略性地组合和转换多个数据源，形成一个统一的视图以解决更具体的问题时，都可以使用它们。

例如，假设我们的任务是创建一个作业，生成咖啡店当前的可用容量数据。为了练习，假设我们已经有一个数据源，可以输出每家咖啡店的已占用座位数。我们可以使用这些数据与我们的咖啡 `stores` 数据进行连接，创建一个新的视图，通过查询该视图可以找到哪家店铺能容纳不同规模的团体。

在本书的后续部分，我们将研究流式应用程序和管道，并介绍使用 `Kafka` 和 `Redis Streams` 发出事件数据（例如每家咖啡店的已占用座位数）的过程。我们甚至会逐步讲解如何从这些事件流中可靠地读取和组合新的物化视图！

**注意**
为了本次练习的目的，我们将以一种也能展示 `Spark` 中不同常见连接类型的方式生成容量数据。

## 练习 4-2：通过连接扩展数据

连接数据时，我们通常使用一个或多个列作为连接键（或选择表达式），连接我们要组合的两个数据集。在本例中，我们将使用咖啡店名称作为公共键。

在 `Zeppelin` 中创建一个新段落，并输入代码清单 4-6 中的代码片段。这将创建可用于与 `stores` 数据连接的视图。

```
%spark
case class StoreOccupants(storename: String, occupants: Int)
val occupants = Seq(
StoreOccupants("a", 8),
StoreOccupants("b", 20),
StoreOccupants("c", 16),
StoreOccupants("d", 55),
StoreOccupants("e", 8)
)
val occupancy = spark.createDataFrame(occupants)
occupancy.createOrReplaceTempView("store_occupants")
```

代码清单 4-6 为连接生成容量视图。

此时，在 `Spark` 中生成数据的模式应该开始让人感到熟悉。我们从基于代码清单 4-6 中的 `occupancy` `DataFrame` 创建一个新的临时视图开始。值得指出的是，`StoreOccupants` 类包含一些未知的店铺，标识为 `d` 和 `e`。这些仅用于展示 `Spark` 中的各种连接功能。接下来，让我们使用以下连接类型来探索连接这两个数据源（`stores` 和 `occupancy`）：*内连接*、*右连接*、*左连接*、*半连接*、*反连接*和*全连接*。这有助于我们理解不同连接类型的工作方式。选择正确的连接类型对于在物化新数据视图时显著减少所需的工作量至关重要。对于每种连接类型，你可以在 `Zeppelin` 笔记中创建一个新段落来实时操作。

### 内连接

内连接是最容易理解的，恰好也是 `Spark` 中的默认连接操作（因为通常人们希望以这种方式连接数据）。内连接的工作原理是仅选择在连接的两侧数据中都满足连接选择条件的行。

**Spark SQL 内连接**

```
%sql
select * from stores
inner join store_occupants on stores.`name` == store_occupants.`storename`
```

**DataFrame 内连接**

```
%spark
val inner = df
.join(occupancy)
.where(df("name") === occupancy("storename"))
inner.show()
```

连接操作的结果是一个新的 `DataFrame`，它组合了两个数据源中满足连接条件的所有列。在本例中，即两个数据源中存在匹配的店铺名称。

内连接的表格输出展示了组合方式。

**内连接操作的结果**

```
+----+--------+-----+------+---------+---------+
|name|capacity|opens|closes|storename|occupants|
+----+--------+-----+------+---------+---------+
|   a|      24|    8|    20|        a|        8|
|   b|      36|    7|    21|        b|       20|
|   c|      18|    5|    23|        c|       16|
+----+--------+-----+------+---------+---------+
```

内连接简单地忽略所有不满足连接条件的行。接下来，让我们看看右连接。

### 右连接

右连接，或称右外连接，会返回右侧数据源中的所有行，并显式连接所有满足选择条件且与左侧数据匹配的行。当数据不匹配时，会插入 `null` 值。

**Spark SQL 右连接**

```
%sql
select stores.*, store_occupants.`occupants` from stores
right join store_occupants on stores.`name` == store_occupants.`storename`
```

**DataFrame 右连接**

```
%spark
// df 是我们的 stores 数据
val rightJoined = df
.join(occupancy,
df("name") === occupancy("storename"),
"right")
rightJoined.show()
```

连接操作的结果同样是一个新的 `DataFrame`，它合并了两个数据源的所有列，但不会跳过两个数据源之间缺失的数据，而是产生如下输出。

**右连接操作的结果**

```
+----+--------+-----+------+---------+---------+
|name|capacity|opens|closes|storename|occupants|
+----+--------+-----+------+---------+---------+
|   a|      24|    8|    20|        a|        8|
|   b|      36|    7|    21|        b|       20|
|   c|      18|    5|    23|        c|       16|
|null|    null| null|  null|        d|       55|
|null|    null| null|  null|        e|        8|
+----+--------+-----+------+---------+---------+
```

当你希望保留两个数据源之间缺失数据的详细信息时，可以使用右连接。目标数据集（本例中为 `occupancy`）决定了操作返回的总行数。由于右侧数据源（`occupancy`）的行数（5 行）多于 `stores` 数据（3 行），缺失的值将以 `null` 列的形式添加到左侧。

现在让我们看看左连接，它实际上就是右连接在左侧的等价操作。

### 左连接

左连接，或称左外连接，会返回左侧数据源中的所有行，并显式连接所有满足选择条件且与右侧数据匹配的行。当数据不匹配时，会插入 `null` 值。

**Spark SQL 左连接**

```
%sql
select stores.*, store_occupants.`occupants` from stores
left join store_occupants on stores.`name` == store_occupants.`storename`
```

**DataFrame 左连接**

```
%spark
// df 是我们的 stores 数据
val leftJoined = df
.join(occupancy,
df("name") === occupancy("storename"),
"left")
leftJoined.show()
```

根据右连接操作的输出，你认为左连接的输出会是什么？如果你猜是*只有三行*，那恭喜你答对了！

**左连接操作的结果**

```
|name|capacity|opens|closes|storename|occupants|
|---|---|---|---|---|---|
|a|24|8|20|a|8|
|b|36|7|21|b|20|
|c|18|5|23|c|16|

正如我们在右连接操作中看到的，支撑目标数据源的数据对操作结果至关重要。在本例中，`stores`数据有 3 行，而`occupancy`数据有 5 行，因此结果中最多只能有与目标数据（3 行）相同的行数。

### 半连接

半连接，或称左半连接，通过查找右侧所有满足选择条件的行，来过滤左侧返回的行集。本质上，进行连接只是为了选择从左侧返回哪些结果。这个过程通过查找连接选择表达式的交集，并仅返回左侧与交集匹配的行来实现。我喜欢把它看作一个选择代理过滤器，因为它使用另一个数据源作为自己的选择标准。

考虑以下用例来更好地理解这个概念。假设我们想找出座位数少于 20 个的咖啡店，并将它们存储在一个名为`boutiques`的新视图中。人们喜欢不同大小的店铺，而我个人更愿意待在较小、较安静的店里。

我们有两种方法可以实现。我们可以通过选择创建一个新视图，或者使用所谓的内联视图。现在让我们来看看这些过程。

为了创建新的数据源（并展示半连接的使用），我们有几种方法可以实现这个目标：

- 我们可以使用内联表手动创建数据，作为半连接的右侧数据源。

- 我们可以通过从现有数据源中进行选择来创建一个新视图。

**创建一个名为 Boutiques 的内联表**

```sql
%sql
create or replace
temporary view boutiques as (
select * from VALUES ("c") as data(boutiquename)
)
```

使用内联表是创建临时数据表的一种简单方法，可用于多种不同目的：

- 当你发现自己想“如果我们有这些数据该多好……”时，可以测试一个新想法。这可以在构建应用程序时节省时间，因为你无需创建任何正式的类，甚至不需要想出列名。

- 通过添加有助于过滤或复杂选择的合成数据，简化 SQL 选择语句。

- 在探索新数据源或想添加一些额外的默认列时非常有用，这可以通过与临时内联视图进行全连接来实现。

在我们的例子中，我们可以使用这个表通过半连接来帮助过滤数据。然而，考虑到我们的用例，`stores`视图中已经存在很好的真实数据源，我们也可以用它来实现这个目的。重用现有视图的好处在于，你无需管理另一个数据源；相反，你只需应用一个过滤器`where capacity < 20`，其他人就能很容易理解`boutiques`视图的概念。

**通过选择和投影别名创建 Boutiques 视图**

```sql
%sql
create or replace
temporary view boutiques as (
select stores.`name` as boutiquename
from stores
where capacity < 20
)
```

现在我们已经创建了`boutiques`视图，剩下的就是应用半连接操作了。

**Spark SQL 半连接**

```sql
%sql
select * from stores
semi join botiques
on stores.`name` == botiques.`botiquename`
```

**DataFrame 半连接**

```spark
%spark
// 半连接
val botiques = spark.sql("select * from botiques")
val semiJoin = df
.join(botiques,
df("name") === botiques("botiquename"),
"semi")
semiJoin.show()
```

操作的结果是，我们找到了一家精品咖啡店。它恰好是`stores`数据中与`boutiques`数据相交的那一行。现在让我们看看半连接的反向操作，即反连接。

### 反连接

反连接，或称左反连接，是半连接的反向操作。它基于连接选择表达式，利用左侧和右侧数据之间的集合差，来减少左侧数据返回的行数。

**Spark SQL 反连接**

```sql
%sql
select * from stores
anti join boutiques
on stores.`name` == boutiques.`boutiquename`
```

**DataFrame 反连接**

```spark
%spark
val boutiques = spark.sql("select * from boutiques")
val antiJoin = df
.join(boutiques,
df("name") === boutiques("botiquename"),
"anti")
antiJoin.show()
```

此操作的结果是，我们找到了两家非精品咖啡店。

### 半连接和反连接的别名

虽然半连接和反连接允许你使用另一个数据源作为选择过滤器，但有一种更易于理解的方式来声明相同的意图。你可以分别使用`IN`和`NOT IN`运算符，而不是使用半连接或反连接，来达到完全相同的结果。实际上，Spark 会在后台直接使用半连接或反连接。

#### 使用 IN 运算符

*将 IN 运算符用作半连接*

```spark
%spark
val inOper = spark.sql(
"""
select * from stores
where stores.`name` in (
select boutiquename from boutiques
)
"""
)
inOper.explain("formatted")
```

Spark 会理解你 `IN` 条件的意图，并在后台将其转换为半连接。鉴于半连接和反连接在你的团队中可能不是常见的连接类型，优化代码的可维护性总是更好的选择，有时这意味着遵循通用惯例（或者你也可以始终在注释中记录你所做的事情）。

通过在 `inOper` DataFrame 上调用 `explain` 获得的输出，展示了 Spark 在后台将 Spark SQL 表达式转换为一系列转换和操作所采取的步骤。处理这些后台操作的引擎是 Catalyst 引擎，我们将在第 6 章中进一步探讨。

*Spark 执行计划：展示 Spark 解析、优化和生成执行计划步骤的格式化视图*

```spark
== Physical Plan ==
* BroadcastHashJoin LeftSemi BuildRight (4)
:- * LocalTableScan (1)
+- BroadcastExchange (3)
+- LocalTableScan (2)
(1) LocalTableScan [codegen id : 1]
Output [4]: [name#0, capacity#1, opens#2, closes#3]
Arguments: [name#0, capacity#1, opens#2, closes#3]
(2) LocalTableScan
Output [1]: [boutiquename#248]
Arguments: [boutiquename#248]
(3) BroadcastExchange
Input [1]: [boutiquename#248]
Arguments: HashedRelationBroadcastMode(List(input[0, string, true])), [id=#557]
(4) BroadcastHashJoin [codegen id : 1]
Left keys [1]: [name#0]
Right keys [1]: [boutiquename#248]
Join condition: None
```

学会阅读 Spark 执行计划可以在优化复杂查询时提供帮助，但目前你只需用它来了解使用 Apache Spark 时后台发生的过程。现在回到连接的话题。

#### 否定 IN 运算符

继续创建另一个段落，看看当你从使用 `IN` 切换到 `NOT IN` 时会发生什么。

*将 NOT IN 运算符用作反连接*

```spark
%spark
val notInOper = spark.sql(
"""
select * from stores
where stores.`name`
not in (
select boutiquename from boutiques
)
"""
)
```

我们已经探讨了在 Spark 中连接数据源的多种技术。现在，让我们通过研究全连接操作来结束这段旅程。
```

#### 全连接

我们将探讨的最后一个连接操作称为*全连接*。此操作会考虑两个数据源，在满足选择条件的地方连接数据，然后在两个数据集中没有匹配项的一侧填充 `null`。

为了展示全连接操作的工作原理，我们将向 `stores` DataFrame (`df`) 中添加一些额外的记录，以便在左侧和右侧 DataFrame 中都存在缺失数据（参见清单 4-7）。

```spark
%spark
// 创建 2 个新商店
val addStores = spark.createDataFrame(Seq(
("f", 42, 5, 23),
("g", 19, 7, 18)))
.toDF("name","capacity","opens","closes")
val fullJoined = df
.union(addStores)
.join(occupancy,
df("name") === occupancy("storename"),
"full")
fullJoined.show()
清单 4-7
使用全连接和联合来合并数据集
```

清单 4-7 中的代码展示了如何执行全外连接。它还展示了 Spark 提供的另一种连接——*联合*。此外，这段代码示例的顶部还隐藏了另一个技巧，即创建 DataFrame 的第三种方法。我们已经了解了使用 case 类、使用内联表的过程，现在又了解了如何使用元组序列。内联表和使用带有显式列名的元组在风格上相似。

**联合**

联合类似于连接，但两个数据源需要具有相同的模式（匹配的列名和顺序）。此过程可以通过在一个或多个 DataFrame 上进行投影和列别名化（使用 `union` 或 `unionAll` 函数）来实现，然后再创建聚合临时视图。考虑到强制协调许多可能不同但相关数据集的底层模式所需的工作量，Spark 3 引入了 `unionByName` 方法，以便你可以忽略任何缺失的列：`df.unionByName(addStores, allowMissingColumns=true)`

就复杂性而言，全外连接是所探讨的连接中最昂贵的。这是因为它需要对左侧和右侧数据源都进行排序，以便在数据源之间执行*排序-合并-连接*操作。让我们看一下全连接操作的输出，以了解其工作原理。

*stores 和 store_occupancy 数据全连接后的输出*

```spark
+----+--------+-----+------+---------+---------+
|name|capacity|opens|closes|storename|occupants|
+----+--------+-----+------+---------+---------+
|   g|      19|    7|    18|     null|     null|
|   f|      42|    5|    23|     null|     null|
|null|    null| null|  null|        e|        8|
|null|    null| null|  null|        d|       55|
|   c|      18|    5|    23|        c|       16|
|   b|      36|    7|    21|        b|       20|
|   a|      24|    8|    20|        a|        8|
+----+--------+-----+------+---------+---------+
```

输出显示行已按逆序排序。更重要的是，我们没有跳过或忽略两个数据集之间的任何缺失数据。使用左连接、右连接或全外连接可以让你了解两个数据集之间缺失了什么，然后由你决定如何继续。这是一个重要的概念，因为它可以策略性地用于衡量上游数据生产者产生的损坏或缺失数据，并在监控数据管道中缺失数据频率时作为指标进行跟踪。

#### 练习 4-2：总结

本练习探讨了 Apache Spark 中可用的多种连接风格，旨在为你提供连接和转换数据的策略。接下来，我们将综合所学知识，解决一个与咖啡店相关的有趣的座位问题。

## 综合应用

让我们将本章所学的内容整合起来，解决最初的问题：根据我们手头可用的占用数据，为特定规模的团体寻找可用座位。我们可以将解决此问题所需的步骤分解为以下几个阶段：

1.  计算每家商店当前的可用座位数。
2.  找到至少有足够座位容纳该团体的商店。
3.  返回有空位的商店名称。

我们的工具箱中几乎已经拥有了实现这一目标所需的所有工具。你可能想知道如何计算每家商店的可用座位数？

### 练习 4-3：使用 SQL 表达式和条件查询解决问题

首先，我们知道可以连接 `stores` 和 `store_occupants` 数据来获取 `capacity` 和 `occupants` 列，但我们仍然需要将这两列相减，以便在筛选中使用结果。幸运的是，这个问题恰好有一个相当简单的解决方案。我们可以使用*表达式*来实现这一目标。在本练习中，你可以在 Zeppelin 的同一个笔记中继续操作。

### 表达式作为列

使用表达式列可以通过别名运算符（`as`）实现。我们也可以结合使用简单的减法运算符（`-`）和别名来达成这一目标。让我们先通过计算门店内的可用容量来解决任务的第一部分。

*SparkSQL：使用列表达式生成当前可用容量*

```sql
%sql
select name, (capacity-occupants) as availability
from stores
join store_occupants
on stores.`name` == store_occupants.`storename`
```

此操作的结果是一个新的 DataFrame，包含列名和可用容量。

```
+----+------------+
|name|availability|
+----+------------+
|   a|          16|
|   b|          16|
|   c|           2|
+----+------------+
```

现在我们离解决方案越来越近了。下一步只需选择可用容量大于或等于聚会人数的行。我们之前已经创建过这类条件，那么来看看如果尝试查找四人聚会的可用容量会发生什么。

```sql
%sql
select name, (capacity-occupants) as availability
from stores
join store_occupants
on stores.`name` == store_occupants.`storename`
where availability > 4
```

当你尝试运行此查询时，会发现 Spark 无法解析 `availability` 列。

```
org.apache.spark.sql.AnalysisException: cannot resolve '`availability`' given input columns: stores.capacity, stores.closes, stores.name, store_occupants.occupants, stores.opens, store_occupants.storename
```

Spark 无法解析此列的原因在于，该列在表达式首次解析之后才存在。解决这个困境有两种方法：
- 使用内层查询
- 将表达式列改为选择表达式

### 使用内层查询

内层查询通过投影返回列，供外层查询使用，从而允许我们解析名为 `availability` 的列。这只需要我们创建一个包裹性的 `select` 语句，在其中应用条件选择逻辑。

*在外层查询中使用内层查询来完全解析列（包括表达式）*

```sql
%sql
select name, availability from (
select name, (capacity-occupants) as availability
from stores
join store_occupants
on stores.`name` == store_occupants.`storename`
) where availability > 4
```

内层查询模式允许我们在外层查询的作用域内使用投影产生的列。如果我们想返回 `availability` 列，这是必要的；但如果只关心有可用容量的门店名称，我们可以通过对查询语法稍作调整来大幅简化查询。

### 使用条件选择表达式

有一个更好的方法来解决这个问题。我们只需将可用容量过滤条件从已知（已解析）列改为选择表达式。这同时也消除了对内层查询的需求。

*使用条件选择表达式过滤结果*

```sql
%sql
select name from stores
join store_occupants
on stores.`name` == store_occupants.`storename`
where (capacity-occupants) > 4
```

# 总结

本章向你介绍了数据投影和选择的概念。我们从基础开始，通过实践性的动手示例逐步磨练技能。你学会了应用条件过滤器，以及如何减少 DataFrame 中的列数。你学到了创建 DataFrame 的各种技巧，从 case 类到内联表，甚至元组序列。我们一起学习了跨多种连接风格的数据连接，从内连接到联合，甚至半连接和反连接。最后，我们整合了本章的所有概念，回答了一个基本的预订查询：“哪些门店目前有可供四人聚会的可用容量。”这些技能对于任何数据工程师的日常工作都具有可迁移性，你会发现自己在日常工作中频繁使用这些技术。

在移动表达式位置并移除内层查询后，优化后的查询本身更紧凑且更易理解。为完整起见，我们再来看看如何使用纯 DataFrame 转换来解决同样的问题（参见清单 4-8）。

## 清单 4-8：使用纯 DataFrame 转换查找咖啡店座位可用性

```scala
%spark
import spark.implicits._
val partySize = 4
val hasSeats = df
.join(occupancy, df("name") === occupancy("storename"))
.withColumn("availability", $"capacity".minus($"occupants"))
.where($"availability" >= partySize)
.select("name")
hasSeats.show
```

清单 4-8 中展示的 DataFrame 转换将 `occupancy` 数据与 `stores` DataFrame（`df`）连接，然后在 DataFrame 上引入 `withColumn` 函数。此操作在功能上等同于之前使用内层查询完全解析 `availability` 列的过程。在这个例子中，我们只是采用了更声明式的方式。结果是一系列函数式转换，其读起来几乎与前面概述的三个步骤完全一致。

### 练习 4-3：总结

本练习向你展示了如何基于 SQL 表达式创建动态（派生）列，以及如何使用嵌套（内层）查询从外层查询中按条件查询派生列。这使我们能够返回一份咖啡店列表，其中包含为任意规模人群提供的可用座位。

下一章将介绍如何使用 JDBC 与外部数据库协同工作。你将设置本地容器环境以使用 MySQL 8，并且第 5 章的工作将延续到第 6 章，届时你将学习使用 Hive Metastore 和 Spark SQL Catalog。


