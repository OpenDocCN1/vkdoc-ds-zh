# add mariadb driver directly to the spark class path
COPY ./spark/jars/mariadb-java-client-2.7.2.jar /opt/spark/jars/
COPY ./conf/interpreter.json /zeppelin/conf/
COPY ./conf/zeppelin-env.sh /zeppelin/conf/
代码清单 12-1
在官方 Apache Zeppelin Docker 容器之上添加新的文件系统层，引入 Java 11 和 Spark 3.1.2 以及自定义配置和 JAR 包
```

多阶段 Docker 构建允许你创建常用容器的可靠实例，而无需处理更复杂的构建过程。就 `zeppelin-spark` 容器而言，我们可以直接从 `apache-spark-base` 容器中提取 Java 11（JRE）以及 Spark 发行版（见代码清单 12-2）。这与使用模块构建 Spark 应用程序的理念类似，你可以使用 `FROM` 来堆叠容器，或者将容器用作资源的提供者。

```
FROM newfrontdocker/apache-spark-base:${spark_image} as spark
FROM apache/zeppelin:0.9.0
COPY --from=spark /opt/spark /opt/spark
COPY --from=spark /usr/local/openjdk-11 /opt/openjdk-11
代码清单 12-2
使用多阶段构建模式有助于减小容器的最终体积，也可以用来减少管理复杂容器的开销
```

在本地构建容器是一个可选步骤。你可以使用公开的 `newfrontdocker/zeppelin-spark` 容器，但只需运行以下构建命令（从 `zeppelin-spark` 目录）即可轻松构建自己的容器。

```
docker build . -t `whoami`/zeppelin-spark:latest
```

接下来，你可以启动环境了。

### 启动本地环境

在 Docker 目录下，你会找到另一个名为 `zeppelin` 的目录。将该目录复制到 `~/dataengineering/zeppelin-spark`。与前面的章节一样，该目录下的 `docker-compose.yaml` 文件为你提供了启动环境所需的所有配置。

```
docker compose \
-f ~/dataengineering/zeppelin-spark/docker-compose.yaml up \
-d
```

这个 compose 进程将启动 Apache Zeppelin（`localhost:8080`）、MySQL（`:3306`）和 MinIO（`localhost:9000`）文件系统。

## 使用通用 Spark 函数进行分析预处理

第一个练习将是实践性的导览，使用 `org.apache.spark.sql.functions` 包中更常见的*高阶函数*（列式表达式），重点关注那些有助于准备分析数据的函数。

### 练习 12-1：为分析预处理数据集

在浏览器中打开 Zeppelin（`Localhost:8080`），创建一个新的笔记，并为其取一个容易记住的名称，例如 `01_common_dataframe_functions`。从这一点开始，你将学习使用那些有助于修改和准备数据进行分析处理的函数：

*   处理时间戳和日期

*   处理时区

*   处理并替换空列值

*   使用 Case 语句

*   使用用户自定义函数

### 处理时间戳和日期

处理时间可能是一件棘手的事情。你在第 9 章中了解到，时钟漂移和不正确的时区信息如何引发问题，以及如何通过统一使用 UTC 并在服务器上使用 NTP 同步时钟漂移来普遍解决这些问题。虽然标准化有助于未来的项目，但你可能会发现自己需要以不同的方式处理时间，或者需要修正和规范化时间戳。

#### 常用日期和时间戳函数

从空笔记（在 Zeppelin 中）开始，复制代码清单 12-3 中的代码块并运行该段落。这条 SQL 语句会生成一行数据，其列代表 Spark 在运行时捕获的日期和时间信息。

```
%spark
spark.sql("""
SELECT current_timestamp() as ts,
current_timezone() as tz,
current_date() as date,
TIMESTAMP 'yesterday' as yesterday,
TIMESTAMP 'today' as today,
TIMESTAMP 'tomorrow' as tomorrow
""").show(6,0,true)
代码清单 12-3
使用 Spark SQL 获取时间概念
```

结果行的输出将是代码被评估时捕获的标准系统日期和时间信息的快照。

```
-RECORD 0-------------------------------
ts        | 2021-09-12 00:54:03.691464
tz        | Etc/UTC
date      | 2021-09-12
yesterday | 2021-09-11 00:00:00
today     | 2021-09-12 00:00:00
tomorrow  | 2021-09-13 00:00:00
```

`current_timestamp`、`current_timezone`、`current_date` 以及 `TIMESTAMP` 常量 `yesterday`、`today` 和 `tomorrow` 都是 Spark SQL 中的高阶日期时间函数。接下来，我们将使用 Spark DSL 函数直接重新创建相同的输出。



#### 使用 `withColumn` 应用高阶函数

创建一个新的段落。在该段落中，我们将创建一个仅存储一个时间戳的 1x1（行/列）`DataFrame`。模拟 `current_timestamp` 表达式，清单 12-4 展示了如何包装一个简单的 Java `Instant` 来复制 `current_timestamp` 表达式。

```
%spark
val tsDf = Seq(Instant.now).toDF("ts")
清单 12-4
创建一个包含单行单列且存储时间戳的 DataFrame
```

清单 12-4 中的简单技术利用了隐式转换，将 `Scala Seq[Instant]` 编码为带有 `TimestampType` 列的 Catalyst `Row`（`DataFrame`）。使用 `tsDf DataFrame`，我们现在可以通过 `DataFrame` 上的 `withColumn` 方法来添加列。

`DataFrame.withColumn(colName, col)`

`withColumn` 方法会在 `DataFrame` 的所有行上迭代地添加一个新列。`col`（`Column`）参数是一个强大的原语，它封装了一个列式 SQL 表达式，可用于将用户自定义函数应用到你的应用程序中。需要牢记的是，`withColumn` 只能引用一行中相邻列的数据。还有其他技术可以用于处理所有行或行子集，以使用窗口函数生成派生聚合结果。

清单 12-5 中的代码块添加了一个包含时区的列字面量（意思就是“该列的字面值就是你看到的这样”），以及通过列操作 `to_date` 派生出的日期。

```
import org.apache.spark.sql.functions._
import org.apache.spark.sql.types._
val dtInfoDf = tsDf
.withColumn("tz",
lit(spark.conf.get("spark.sql.session.timeZone"))
)
.withColumn("date", to_date($"ts"))
清单 12-5
使用 withColumn 添加列字面量和通过 to_date 派生出的日期列
```

转换的结果是一个新的 `DataFrame`，它代表了在源 `DataFrame` 上添加两个新列的组合。请记住，由于 Spark 是惰性执行的，在到达某个特定的行动操作之前，不会执行任何实际工作。清单 12-5 中的操作添加了在最终系列执行时提供新日期列的承诺。第一列使用了一个名为 `lit` 的特殊列。`lit` 列使用隐式类型编码器（如果可用）将底层数据类型包装并编码为一个类型化列。第二列（`date`）被称为派生列。当你使用 `to_date` 函数并传入一个源列（`$"ts"`）时，Spark 会使用引用列（`ts`）来生成一个 `DateType`。

如果此时打印 `dtInfoDf` 的模式，你会看到以下内容：

```
root
|-- ts: timestamp (nullable = true)
|-- tz: string (nullable = false)
|-- date: date (nullable = true)
```

要处理从清单 12-3 重建 `DataFrame` 所需的其余缺失列，还需要处理 `yesterday`、`today` 和 `tomorrow` 这三列。

#### 使用日期加法和减法

日期时间函数 `date_sub` 和 `date_add`，以及一个简单的 `cast` 表达式，可以组合起来派生 `yesterday`、`today` 和 `tomorrow`，如清单 12-6 所示。

```
tsDf
...
.withColumn("yesterday",
date_sub($"date", 1).cast(TimestampType))
.withColumn("today", $"date".cast(TimestampType))
.withColumn("tomorrow",
date_add($"date", 1).cast(TimestampType))
清单 12-6
使用日期减法、加法和数据类型转换来模拟清单 12-3 的输出
```

现在，你可以创建时间戳、从时间戳派生日期，以及对日期进行加减操作。作为日期时间函数巡览的收尾，我们将介绍 `year`、`month`、`dayofmonth`、`dayofweek` 和 `dayofyear` 这些日历函数。

#### 日历函数

在分析和洞察中，通常使用日历比较显式的时间段（窗口）。趋势分析和时间序列预测是使用统计方法来测量数据点之间随时间变化率（增量）的技术。可以使用 *固定* 或 *相对* 窗口，通过 Spark SQL 对数据集进行分区（分桶）和分析，从而创建按秒、分钟、小时、天、周、月甚至年细分的聚合结果。

**固定窗口与相对窗口**

一个 *固定窗口* 由明确的开始和结束时间定义。例如，昨天是一个由 00:00:00 开始到 23:59:59 结束的 24 小时时间段定义的窗口。固定窗口通常用于计算两个或多个数据集之间的变化，常见的有逐小时、逐日、逐周和逐月计算。

一个 *相对窗口* 使用非固定的时间点来定义基于时间边界的一端。这个边界随后可用于计算开始或结束时间戳，从而产生一个任意窗口来观察统计数据。例如，你可以使用相对时间隔离出最近 30 分钟的数据集，而不是在固定的 30 分钟间隔内拆分一个小时（06:00:00 – 06:29:59 | 06:30:00 – 06:59:59）。

以清单 12-7 中的代码为参考，我们将向 `tsDf DataFrame` 添加五个列。

```
tsDf
...
.withColumn("year", year($"date"))
.withColumn("month", month($"date"))
.withColumn("day", dayofmonth($"date"))
.withColumn("day_of_week", dayofweek($"date"))
.withColumn("day_of_year", dayofyear($"date"))
清单 12-7
使用单个日期时间列派生年、月、日、当前周中的天以及当前年中的天

最终的 `DataFrame` 代表当前日期，其中的列表达了从初始时间戳列生成的不同观测值。

```
-RECORD 0--------------------------------
ts          | 2021-09-12 01:04:39.04086
tz          | Etc/UTC
date        | 2021-09-12
yesterday   | 2021-09-11 00:00:00
today       | 2021-09-12 00:00:00
tomorrow    | 2021-09-13 00:00:00
year        | 2021
month       | 9
day         | 12
day_of_week | 1
day_of_year | 255
```

现在，每当你需要快速复习日期和时间操作时，都有一个可靠的参考。但是，实际的时区呢？

### 时区与 Spark SQL 会话

处理基于时间的数据需要进行时区之间的转换，其原因不仅仅是遵守像 UTC 这样的通用时区。你还可以将时区视为一个透镜，从特定事件观察者的视角来查看数据集。当产生与特定地理位置和时区相关的洞察时，就会出现这种用例。

#### 配置时区

Spark 默认使用其环境的本地系统时间（你的笔记本电脑或远程服务器）。在处理数据时，使用默认系统时间可能会导致不一致。为了确保无论应用程序在何处运行都能保持行为一致，你可以使用配置项 `spark.sql.session.timeZone` 来设置默认时区。



#### 在运行时修改 Spark 时区

`SparkSession` 会自动为您全局处理时间戳转换。不过，有时您可能希望显式更改特定查询所使用的时区。在 Zeppelin 中创建一个新段落，并添加清单 12-8 中的示例代码块，该代码块演示了如何动态设置时区。

```
%spark
import java.time._
val ts = Seq(Instant.now).toDF("ts")
spark.conf.set("spark.sql.session.timeZone", "UTC")
ts.show(truncate=false) // utc
spark.conf.set("spark.sql.session.timeZone", "America/Los_Angeles")
ts.show(truncate=false) // pst
清单 12-8
观察基于 SparkSession 时区配置的 Spark 时间输出中的时区变化
```

运行该段落，您将亲眼目睹 Spark 观测到的时间如何与时区配置值同步变化。输出分别捕捉到了通过 UTC 和 PST 时区视角观察到的同一不可变时间戳（`ts`）的引用。

```
UTC |2021-09-12 03:02:32.434387
PST |2021-09-11 20:02:32.434387
```

利用 Spark 运行时配置来切换时间观测视角的能力，使您能够使用单一且不可变更的真实来源（UTC 时间戳）作为底层数据，同时简化下游应用程序为特定时区构建查询的过程。这一切都无需忍受为处理时间戳时区转换而创建多个数据集副本的烦恼。

#### 使用 SET TIME ZONE

您还可以使用 `SET TIME ZONE`（如清单 12-9 所示）通过 Spark SQL 直接设置时区配置，从而动态切换时区。

```
%sql
SET TIME ZONE 'America/Los_Angeles';
SELECT TIMESTAMP 'now' as now_pst;
%sql
SET TIME ZONE 'UTC';
SELECT TIMESTAMP 'now' as now_utc;
清单 12-9
使用 SET TIME ZONE 配置 Spark 会话时区
```

现在，您可以通过 `SparkSession` 在 Spark 中以声明方式更改时区。这个简单的配置使得每个应用程序在查询和展示基于时间的数据时，能够选择如何观测时间。请记住为您的 Spark 应用程序设置一个默认时区，以确保获得可靠且可重复的结果。

### 季节性、时区与洞察

考虑以下示例。您的任务是设计一个系统，该系统能够自动追踪并比较 CoffeeCo 销售商品中观察到的客户购物行为的相对变化，从而得出购买趋势的洞察。在测试系统是否按预期运行时，您偶然发现了一个起初看似异常的商品销售数据。经过进一步调查，发现该商品是*南瓜拿铁*，其销售额从日均零销售，在短短几天内就飙升至与类别中所有其他商品相比创纪录的水平。这是异常吗？还需要哪些其他数据？

有些商品仅在特定时间段或一年中的特定时间才有供应。南瓜拿铁具有季节性，因为它被视为秋季饮品，但季节（节假日）是如何影响销售的呢？情绪因素又扮演了什么角色？

我在美国东北海岸长大，对我来说，十月总是标志着季节的明显变化。树叶从树上飘落，气温降低意味着要穿毛衣和夹克，以及新英格兰地区雪景带来的宁静与反思。伴随着这种变化，是对冬季假期的期盼，这意味着苹果酒（加香料的）和南瓜派，而作为成年人，南瓜拿铁唤起了美好的回忆。这是否仅对我个人而言？这是否与美国东海岸的更高销售额存在关联？销售额是由温度驱动，还是由暴风雪或降雨等其他天气模式驱动？

虽然您可能对新英格兰的秋天没有任何情感联系，也可能讨厌南瓜拿铁，但重要的是思考哪些类型的数据能够用于从分析观察中产生有意义的洞察。归根结底，要像数据侦探一样思考，或者与优秀的数据分析师合作，确保数据收集、聚合和分析工作能够产生新颖的洞察，从而推动创造让客户满意的体验。

### 时间戳与日期总结

思考从单个时间戳中能够衍生出的不同角度、视角或视图，为您开辟了一条通往基于事件洞察的道路。您可以计算一年中的时间信息（季节性）、判断某个日期是周末还是工作日（特定于事件的地理位置，甚至本地时区），或者检查事件是否发生在节假日，甚至该日期是否接近任何其他有意义的历史日期。将发生的事件与现实世界的行为关联起来并将数据与之关联，是许多成功分析和洞察计划的驱动因素。如果您需要更多证据，可以在线搜索“diapers beer correlations”（尿布与啤酒的关联），看看零售商如何从客户交易中获取洞察，将常共同购买的商品摆放得更近，从而增加看似不相关商品的销售额。接下来，我们将探讨一些为分析准备数据的技术。

### 准备分析数据

数据形态各异，规模不一。不幸的是，这也意味着数据可能因为数据损坏或用户错误之外的原因而缺失（为空）。例如，数据集中的空值并不一定需要在静态状态下用默认值填充，因为这可能只会增加给定数据集的整体存储占用。

此外，根据数据集存在的时间长短，以及数据是否仍在被积极收集并追加到该数据集中，数据的底层模式很有可能随着时间的推移缓慢变化。缓慢变化的模式是导致空值的常见原因之一，因为较旧的记录需要被强制转换为新的通用模式（`StructType`），才能与来自数据集（表）的较新数据项并行处理。

除了简单的缺失值之外，您还知道数据集经常被转换并与其他数据集连接，以实现更具体的视图，以满足不同数据域的需求，并帮助回答更专门化的问题（查询）。

我们将探讨条件填充空值的过程，以及使用 case 语句（列表达式）来生成派生列，这些派生列可用于条件标记（标签）、分桶（分区）以及解决常见的数据问题。



### 替换 DataFrame 中的空值

在 DataFrame 上使用 `na.fill` 可以有条件地填充缺失数据。在 Zeppelin 中新建一个段落，将代码清单 12-10 中的代码块复制进去，然后运行。该示例创建了一个简单的 DataFrame，代表 CoffeeCo 已注册和未注册客户下的四笔订单，每笔订单价格各异。这里我们选择使用 null 值来表示`customer_id`列中未注册的客户。

```
%spark
val nullOrdersDF = Seq(
(1, null, "decafe", 3.12),
(2, "cust123", "pour_over", 5.15),
(3, "cust234", "latte", 3.89),
(4, "cust345", "special_pour_over", 6.99)
)
.toDF("order_id", "customer_id", "item_name", "price")
nullOrdersDF.show()
Listing 12-10
创建一个包含空 customer_id 的 DataFrame 以使用 na.fill
```

运行该段落后，生成的 DataFrame 将如下所示：

```
+--------+-----------+-----------------+-----+
|order_id|customer_id|item_name        |price|
+--------+-----------+-----------------+-----+
|1       |null       |decafe           |3.12 |
|2       |cust123    |pour_over        |5.15 |
|3       |cust234    |latte            |3.89 |
|4       |cust345    |special_pour_over|6.99 |
+--------+-----------+-----------------+-----+
```

现在，你可以使用 `na.fill` 操作，用一个通用值替换一个或多个列中的空值，并返回一个新的干净 DataFrame。以代码清单 12-11 为参考，测试将 `customer_id` 列填充为值 `"unknown"`。

```
%spark
val nonNullOrdersDF = nullOrdersDF
.na.fill("unknown", Seq("customer_id"))
nonNullOrdersDF.createOrReplaceTempView("order_nonnull")
nonNullOrdersDF.show(truncate=false)
Listing 12-11
使用值 Unknown 替换列 customer_id 中的空值
```

通过调用代码清单 12-11 中的 `na.fill` 创建的非空（已清理）DataFrame，被保存到新的引用变量以及名为 `order_nonnull` 的临时视图中。操作结果如下所示：

```
+--------+-----------+-----------------+-----+
|order_id|customer_id|item_name        |price|
+--------+-----------+-----------------+-----+
|1       |unknown    |decafe           |3.12 |
|2       |cust123    |pour_over        |5.15 |
|3       |cust234    |latte            |3.89 |
|4       |cust345    |special_pour_over|6.99 |
+--------+-----------+-----------------+-----+
```

使用像 `unknown` 这样的特定常量填充空值，有助于声明列的含义，而不是让其他工程师去猜测该列的意图。

作为替代方案，如果该表在 Spark SQL Catalog 中管理，那么作为优秀的数据管理员，你可以更进一步，在 Hive Metastore 中描述空列的含义。这样，其他数据团队就可以一致地以相同方式处理这些空值。

在准备分析数据时，你很可能会发现自己需要使用一个或多个条件表达式来添加派生列。无需创建自定义用户定义函数（UDF），直接使用 SQL case 语句和 Spark SQL 的 `when`/`then`/`otherwise` 表达式即可。Case 语句的工作方式类似于模式匹配块或传统的 `switch` 语句。

### 使用 Case 语句标记数据

Case 语句提供了一种基于一个或多个链式布尔表达式（谓词）的结果来添加新列的机制。你可以在处理数据时，使用 case 语句来*打标签*、*分类*或*标记*数据。Case 语句的语义会有所不同，具体取决于你是在 DataFrame 上使用 Spark SQL 函数，还是在表或视图上使用 SQL 表达式。

#### Dataset 上的 Case 语句

在 Zeppelin 笔记本中新建一个段落。你将通过添加两个名为 `is_registered` 和 `order_label` 的列来转换目标 DataFrame `nonNullOrdersDF`（代码清单 [12-11]），从而创建一个带标签的订单。代码清单 12-12 中的代码块展示了如何使用 *when, otherwise* 表达式。第一个列 `is_registered` 测试订单是否有已知或未知的 `customer_id`，而 `order_label` 表达式则使用 `boolean` 和 `numeric` 条件判断，有条件地将订单标记为 VIP、加急或普通。

```
%spark
nonNullOrdersDF
.withColumn("is_registered",
when(col("customer_id").notEqual("unknown"), true)
.otherwise(false)
)
.withColumn("order_label",
when(col("is_registered").equalTo(true).and(col("price") > 6.00), "vip")
.when(col("is_registered").equalTo(true).and(col("price") > 4.00), "rush").otherwise("normal")
)
.show(truncate=false)
Listing 12-12
使用 When, Then, Otherwise 在 DataFrame 上创建复杂的派生列
```

如前所述，第一个新列（`is_registered`）代表客户的注册状态。该列表达式是一个谓词，用于检查求值条件是否满足。*当*列的值不等于 `unknown` *时*，我们将列值设置为 *true*，*否则*回退到将该列值设置为 *false*。

第二个派生列使用 `is_registered` 的值，根据客户的注册状态及其订单价格，有条件地装饰客户的订单。结果是一个具有三个可能值的列：`normal`、`rush` 或 `vip`。该列表达式通过引入表达式*与*（也可以是或条件）的概念，使用了更复杂的分支条件。思考 `when-then-otherwise` 表达式最简单的方法，就是将其视为典型的分支条件。

```
if (row.is_registered) {
if (row.price > 6.00) "vip" else "rush"
} else "normal"
```

让我们看看如何使用 Spark SQL 实现相同目标。

#### Spark SQL 表上的 Case 语句

可以使用 `order_nonnull` 临时视图以及嵌套的内联 select 语句来实现与代码清单 12-12 所示相同的效果。新建一个 Zeppelin 段落，复制代码清单 12-13 中的 SQL 语句并运行。你会发现输出结果与代码清单 12-12 相同。

```
%sql
SELECT *,
CASE
WHEN x.is_registered = TRUE and x.price > 6.00 THEN 'vip'
WHEN x.is_registered = TRUE and x.price > 4.00 THEN 'rush'
ELSE 'normal'
END as order_label
FROM (
SELECT *,
CASE
WHEN customer_id != 'unknown' THEN TRUE
ELSE FALSE
END as is_registered
FROM order_nonnull
) x
Listing 12-13
在 Spark SQL 表上使用解释型 SQL 创建复杂的派生列
```

代码清单 12-13 中的 Spark SQL case 语句为你提供了添加复杂条件表达式的通用框架，这些表达式有助于在数据集中进行打标签、分桶或查找其他模式。

> **提示**
> 当你只想让 Spark 完成工作，而不必提供在 DataFrame API 上执行相同操作所需的全部函数式转换时，Spark SQL 解释器是一个极好的支持工具。最终，在摸索时，如果你在批处理模式下（包括通过 Zeppelin）使用 DataFrame 和 Dataset API，可以轻松混合执行模式。



