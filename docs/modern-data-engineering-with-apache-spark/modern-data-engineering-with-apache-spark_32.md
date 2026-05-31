# Case 语句的价值

Case 语句提供了一种完美的机制，用于添加条件表达式，这些表达式可用于以声明方式为数据集打标签、分区或分桶，以便后续分析。例如，了解哪些客户每笔订单消费超过 20 美元，或者超过 100 美元，这可能会很有价值。又或者，您可能希望为购买非普通商品的客户打上标签，因为他们的行为模式可能与其他购买非普通商品的客户相似。通过在数据中添加 `order_type` 等标签，您可以为您组织内的数据科学家和机器学习工程师提供输入特征，以便他们能更好地利用数据。这种技术是一种轻量级的测试数据理论的方法，它有助于通过分离数据来探索数据集，从而发现模式和洞察。

## Spark 中的用户自定义函数

用户自定义函数（UDF）使您能够使用封装在 Spark UDF 中的传统 Scala 函数来编码复杂的过程。UDF 甚至可以注册到 `SparkSession` 中，以扩展 Spark SQL 目录中可用的函数。

我们将逐步介绍如何将清单 12-12 中引入的 `order_label` 表达式转换为一个名为 `coffee_order_label` 的 UDF。这个基础函数将接收一个 `String` 和一个 `Double` 参数，并返回一个 `String`。Spark SQL UDF 函数可以包装一个传统的 Scala 函数、函数字面量或内联函数字面量。

### 在 UDF 中使用 Scala 函数

清单 12-14 创建了一个传统的 Scala 函数（`labeler`），并使用 Spark 的 `udf` 函数来包装一个已应用的 `labeler` 函数调用。

```
%spark
import org.apache.spark.sql.functions.udf
def labeler(customerId: String, price: Double): String = {
if (customerId != "unknown") {
if (price > 6.0) "vip" else "rush"
} else "normal"
}
val coffeeOrderLabeler = udf(labeler(_,_))
```

清单 12-14：编码一个用于结构化 Spark API 的传统 Scala 函数

使用传统函数可能是开始使用 UDF 最简单的方式。根据您偏好的工作方式，函数字面量提供了另一种表达 UDF 的方式。

### 在 UDF 中使用函数字面量

Scala 中的函数字面量提供了一种将函数作为参数传递给另一个函数的方式。在清单 12-14 的示例中，需要使用 `udf(label(_,_))` 表达式来应用该函数。清单 12-15 演示了如何使用函数字面量来简化 `udf` 操作，但这会略微降低代码块的可扫描性（对于非函数式编程者而言）。

```
%spark
import org.apache.spark.sql.functions.udf
// udf 可以使用函数字面量
val coffeeOrderLabelerFunc: ((String, Double) => String) =
(customer_id: String, price: Double) => {
if (customer_id != "unknown") {
if (price > 6.0) "vip" else "rush"
} else "normal"
}
val coffeeOrderLabeler = udf(coffeeOrderLabelerFunc)
```

清单 12-15：将函数字面量传递给 UDF

由于函数字面量可以作为方法参数传递，`udf` 操作可以推断所需的函数参数类型（`String`、`Double`）并自动生成一个 lambda 表达式。最后，我们将探讨如何向 `udf` 操作提供一个内联函数。

### 在 UDF 中使用内联函数

`udf` 操作会包装您传递给 `udf` 方法的任何函数，因此您可以简单地提供一个内联函数（如清单 12-16 所示），Spark 将对该函数进行编码，无需在 Spark 作用域之外做任何额外操作。

```
val coffeeOrderLabeler = udf(
(customer_id: String, price: Double) => {
if (customer_id != "unknown") {
if (price > 6.0) "vip" else "rush"
} else "normal"
})
```

清单 12-16：将内联函数（Lambda 表达式）与 Spark SQL UDF 方法一起使用

现在我们已经介绍了如何创建用户自定义函数，接下来应该了解它们是如何工作的，然后学习如何使用它们。

### 用户自定义函数的工作原理

`spark.sql.functions.udf` 方法返回的对象类型是抽象类 [`UserDefinedFunction`](https://spark.apache.org/docs/latest/api/java/index.html%253Forg/apache/spark/sql/expressions/UserDefinedFunction.html) 的一个实例。具体来说，它是具体类 `SparkUserDefinedFunction` 的一个实例。该对象负责使用隐式编码器在 JVM 数据类型和 Spark 数据类型之间进行转换。更有趣的是，在您的 `udf` 上调用 `apply` 的返回类型是 `Column`。这正是 UDF 能够与结构化 Spark API 在列级别进行互操作的魔力所在。UDF 本质上只是另一种列表达式。

### 将 UDF 与 Spark DSL 一起使用

现在，可以使用 `withColumn` 来调用新定义的 UDF。如果尚未执行，请在您的笔记本中创建一个段落，并运行清单 12-16 中的代码块以创建 `coffeeOrderLabeler`。然后创建另一个新段落，并复制清单 12-17 中的代码块，将清单 12-12 中的 `when/otherwise` 语句替换为新的 `coffeeOrderLabeler` UDF。

```
nonNullOrdersDF
.withColumn("is_registered", ...)
.withColumn("order_label", coffeeOrderLabeler(
$"customer_id", $"price"))
```

清单 12-17：使用 UDF 和结构化 Spark DSL

如果您想将新的 UDF 与 Spark SQL 一起使用，则必须注册该 UDF，使其成为 Spark SQL 目录的一部分。之后，该函数就可以像任何其他 SQL 表达式一样使用了。

### 为 Spark SQL 注册 UDF

一旦您拥有了一个 UDF，就可以使用 `SparkSession.udf` 方法对其进行[注册](https://spark.apache.org/docs/latest/api/java/org/apache/spark/sql/UDFRegistration.html)。这会将函数添加到 Spark SQL 目录中。

```
// 注册 udf 以用于 Spark SQL
spark.udf.register("coffee_order_label", coffeeOrderLabeler)
```

您可以通过 `SparkSession` 使用 Spark SQL 目录方法来检查您的 UDF 是否已注册。

### 内省 UDF 函数

搜索并测试您的 UDF 是否存在非常有用。注册一个新函数后，您可以通过调用 Spark SQL 目录（`catalog`）的 `listFunctions` 方法来列出 `SparkSession` 的所有可用函数。

```
spark.catalog.listFunctions.show()
```

因为 `listFunctions` 方法返回一个 `Dataset[Function]`，您还可以通过模式查询该数据集中的函数（以防您记不住全名）。

```
spark.catalog.listFunctions
.where($"name".like("%coffee%"))
```

如果您已经知道了函数的名称，例如 `coffee_order_label` UDF，您可以检查该函数在当前 `SparkSession` 上是否存在。

```
spark.catalog
.functionExists("coffee_order_label")
```

### UDF 的可见性与可用性

用户自定义函数会为每个 `SparkSession` 实例加载到 SQL 目录中。例如，如果您的 Spark 应用程序派生了 `SparkSession`，则不能保证 UDF 会自动可用。这是因为 UDF 与临时视图类似，默认情况下仅在其创建的 `SparkSession` 的生命周期内可用。在使用 UDF 之前检查其是否存在，有助于解决未来可能遇到的问题（参见清单 12-18）。

```
val newSession = spark.newSession
if (!newSession.catalog.functionExists("coffee_order_label")) {
... 注册该函数并使用它
}
```

清单 12-18：派生 SparkSession 后测试用户自定义函数是否存在

在 UDF 函数必须全局可用的场景下，也有一种在默认 Spark SQL 上下文中创建和注册永久 UDF 的模式。



#### 在 Spark SQL 目录中创建并注册永久 UDF

在 Spark 应用程序之间共享一组通用的 UDF 是很常见的做法。为此，你需要额外执行创建、打包和分发通用 UDF 的步骤。例如，清单 12-19 展示了如何创建一个名为 `CoffeeOrderLabeler` 的具体类。该类继承了 Hive UDF 类，从而使实现类能够在 Hive Metastore 中注册。这一能力还允许从分布式文件系统（如 HDFS 或 S3）加载 UDF 函数，以按需提供功能。

```scala
import org.apache.hadoop.hive.ql.exec.UDF;
class CoffeeOrderLabeler extends UDF {
def evaluate(customerId: String, price: Double): String = {
if (customerId != null && customerId != "unknown") {
if (price > 6.0) "vip" else "rush"
} else "normal"
}
}
```
*清单 12-19: 扩展 Hive UDF 以创建可重用的 UDF 类*

创建类之后，你可以通过在 Spark SQL DDL 中使用 `CREATE FUNCTION` 语句来注册 Hive UDF 函数。参见清单 12-20。

```sql
%sql
CREATE FUNCTION native_coffee_order_label
AS 'CoffeeOrderLabeler'
USING JAR 's3a://.../udfs/orders/jars/udfs.jar'
```
*清单 12-20: 在 Spark SQL 目录中注册永久全局函数*

现在，UDF 不仅已在 Hive Metastore 中注册，而且在共享文件系统中也存储了指向该 JAR 位置的路径。接下来回到 Spark SQL 中使用 UDF 的话题。

#### 在 Spark SQL 中使用 UDF

你已经了解了创建、注册和检查用户定义函数的来龙去脉，剩下的就是在 Spark SQL 语句中使用这些函数。清单 12-21 展示了清单 12-13 中 Spark SQL 语句的修改版本。

```sql
%sql
SELECT *,
coffee_order_label(customer_id, price) as order_label
FROM (
SELECT *,
CASE
WHEN customer_id != 'unknown' THEN TRUE
ELSE FALSE
END as is_registered
FROM order_nonnull
) x
```
*清单 12-21: 在 Spark SQL 中使用已注册的 UDF*

清单 12-21 中的 SQL 语句展示了 `coffee_order_label` UDF 的简单用法。UDF 使你能够扩展 Spark SQL 的功能，并添加可广泛重用的新词汇（表达式和运算符）。该技术对于重用非 Spark 原生库很有用，但其中也存在一个陷阱。

### 关于用户定义函数

正如你所见，用户定义函数允许你定义新的基于列的操作，这些操作表现为原生函数，可供 Spark SQL 和 Spark DSL 使用。UDF 可以填补那些难以表达，或者默认 Spark SQL 函数无法原生表达的操作空白。这里存在一个陷阱，它与 Spark 编码 UDF 的方式有关。简单来说，Spark 无法优化 UDF 中的代码块，因为它将内部操作视为一个黑盒。请留意你的 UDF 操作的复杂性，以免在不知不觉中拖慢你的 Spark 应用程序。当性能至关重要时，总是优先使用原生的 Spark SQL 函数，因为它们已经针对 Catalyst 引擎进行了优化。

### 练习 12-1：总结

第一个练习深入探讨了在准备聚合和分析处理数据时常用的 SQL 函数。从处理时间戳和日期函数，到声明处理数据集时使用的时区，再到填充空值和使用条件 case 语句，最后定义你自己的函数（UDF）来扩展 Spark SQL 生态系统中可用的通用功能。现在，我们将注意力转向在 Spark 中使用常见的聚合和分析函数。

## 分析处理与洞察工程

捕获和收集数据通常只有在数据能用来回答问题、揭示趋势、分析客户行为、协助决策自动化，或为复杂的流程和操作提供额外的视角时才有用。考虑一下运营一家咖啡初创公司（如虚构的 CoffeeCo）所需的运营数据。为了在竞争激烈的精品咖啡领域与更成熟的公司竞争，并维持同等水平的客户满意度，一切都必须管理得当。从每家门店的氛围，到提供的咖啡，再到合同物流和关于公平贸易及咖啡豆采购的供应商谈判，基本上，推动业务前进的所有流程都需要从众多数据点出发，通过多视角分析来确保平稳运营。这正是分析、洞察和机器学习相结合，构建智能系统之处。这个故事通常从数据聚合开始。

## 数据聚合

*数据聚合*是一种用于分析大量数据以生成报告或统计数据的技术，这些报告或统计数据与数据集中观察到的模式相关。数据聚合在探索性数据分析（EDA）、数据挖掘、洞察生成、机器学习和人工智能等领域扮演着重要角色。与许多其他事物一样，数据聚合始于一个数据集，而这个数据集是数据工程师可靠捕获并一致处理的结果。

### 练习 12-2：分组数据、聚合、分析和洞察

如果 Zeppelin 没有运行，请重新启动它并创建一个新笔记。给它取一个名字，比如 `02_grouping_aggregations_analytics`。在接下来的练习中，你将使用此笔记学习使用分组和聚合函数来创建报告，并试验复杂的分析函数。

**注意：** 本练习重用了第 11 章中使用随机 `CoffeeOrder` 生成器创建的数据。

### 关系分组数据集

分析处理通过 `groupBy`、`cube`、`rollup` 和 `pivot` 这些 DataFrame 方法实现。这些方法将 DataFrame 转换为 `RelationalGroupedDataset`，并暴露出广泛的分析处理功能。伴随这一新身份而来的是 Spark 处理范式的一个重要变化。Spark 将改变其处理数据的方式，从使用基于行的迭代器（主要关注对单个列执行行级转换）转向处理特定列中包含的数据点，这些数据点在由分组表达式或替代分区逻辑划分的数据集子集的所有行中进行处理。



### 使用分组进行列式聚合

在本节中，你将创建一个简单的报表，根据每笔交易的年份和月份，按门店（`store_id`）汇总已售商品的总数。首先，创建一个名为 `coffee_orders` 的临时视图，并引用来自 `silver.coffee_orders` 表（来自 `ch-11`）的日期分区数据。可以将清单 12-22 放入 Zeppelin 并运行以建立此关联。

```
%spark
val dbName = "silver"
spark.catalog.setCurrentDatabase(dbName)
val dbUri = spark.catalog.getDatabase(dbName).locationUri
val df = spark.read.parquet(s"$dbUri/coffee_orders/")
df.createOrReplaceTempView("coffee_orders")
清单 12-22
通过创建一个可在新 Zeppelin 笔记中使用的视图，准备读取和分析已分区的 coffee_orders 表
```

以 `coffee_orders` 视图为起点，清单 12-23 中的 Spark SQL 查询将创建一个名为 `coffee_orders_datetime` 的新临时视图，该视图添加了 `year`、`month` 和 `day` 派生列。

```
%sql
CREATE OR REPLACE TEMP VIEW `coffee_orders_datetime`
AS SELECT *, year(date) as year, month(date) as month, day(date) as day
FROM coffee_orders
清单 12-23
创建一个新的临时视图 (coffee_orders_datetime)，该视图向 coffee_orders 视图添加派生列 year 和 month
```

现在，使用新的 `year` 和 `month` 列，你可以计算与月度（甚至年度）销售总额相关的统计数据。清单 12-24 中的查询使用原生 Spark SQL 和 `SUM` 聚合运算符来计算已售商品的总数。此查询可以在没有分组子句的情况下运行，以计算全局已售商品的总数。通过添加*分组表达式*（`GROUP BY`），你可以轻松地基于分组列 `year`、`month` 和 `store_id` 创建多个不同的子集。

```
%sql
SELECT store_id, year, month, sum(num_items) AS total_items_sold
FROM coffee_orders_datetime
GROUP BY year, month, store_id
ORDER BY store_id asc
清单 12-24
按年份和月份分区报告每家门店的已售商品总数
```

此聚合的结果如下所示：

```
+--------+----+-----+----------------+
|store_id|year|month|total_items_sold|
+--------+----+-----+----------------+
|  store1|2021|   10|           61955|
|  store1|2021|    9|          197421|
|  store2|2021|    9|          199030|
|  store2|2021|   10|           63842|
|  store3|2021|    9|          202279|
|  store3|2021|   10|           62134|
|  store4|2021|    9|          200684|
|  store4|2021|   10|           62705|
+--------+----+-----+----------------+
```

接下来，我们将介绍如何使用 Spark DSL 创建相同的聚合。

### 使用 Spark DSL 进行聚合

我们刚刚介绍了如何使用 `GROUP BY` 和列聚合运算符 `SUM`。在下一个示例中，我们使用 DataFrame 来创建分组聚合。在 Zeppelin 中创建一个新段落，并复制清单 12-25 中的代码。此示例展示了 DataFrame 如何转换为 `RelationalGroupedDataset`，然后再在聚合后转回 DataFrame。

```
%spark
import org.apache.spark.sql.RelationalGroupedDataset
val coffeeOrdersDf = spark.sql("""
SELECT store_id, year, month, num_items
FROM coffee_orders_datetime
""")
val groupedRelation: RelationalGroupedDataset = coffeeOrdersDf
.groupBy($"year",$"month",$"store_id")
val aggDf = groupedRelation
.agg(sum($"num_items") as "total_items_sold")
.sort(asc("store_id"))
清单 12-25
使用 RelationalGroupedDataset 计算列式 SUM 聚合
```

清单 12-25 中的代码块创建了一个 `SUM` 聚合，以专门按年份和月份计算每家门店的总销售额。Spark SQL 与 Spark DSL 之间一个有趣的区别在于后者向工程师公开每个操作的方式。从逻辑上讲，你需要遵循以下步骤是有道理的：

1.  使用 `year`、`month` 和 `store_id` 列，将所有记录分成不相交的集合（`GROUP BY` / 分组集）。
2.  在每个不相交的集合内（`year-month-store_id`），将 `num_items` 列中的所有值相加（聚合）。
3.  最后，按 `store_id` 升序对结果进行重新排序。

这个简单的报表只观察每个不同组内的最终总数。那么表现最差或最好的日期呢？百分位数数据呢？通常需要提供额外的信息，这些信息有助于概括聚合中数据的形态。接下来，我们将介绍如何计算汇总统计信息，并将向你介绍额外的聚合运算符 `min`、`avg`、`percentile_approx` 和 `max` 字段，以表示最终数值总数背后的统计数据。

### 计算汇总统计信息以获取洞察

*分析*是指随着时间的推移捕获关键绩效指标（度量）的实践，例如我们在上一节中计算的总销售额数字。*洞察*是解释指标为何随时间变化的艺术。比较分析以观察变化的好坏，需要捕获（或推导）额外的数据以及随时间变化的关键绩效指标。

能够观察数据集的形态随时间的变化，需要为每个观察（窗口）计算更详细的统计数据。

### 使用 Describe 计算简单的汇总统计信息

由 `count`、`mean`（均值）、`stddev`（标准差）、`min` 和 `max` 构成的聚合函数集通常用于生成跨数值（甚至非数值）列的汇总统计信息。汇总统计信息有助于衡量底层数值分布的形态，或者让你了解基于字符的列的字典序最小/最大值。Spark 自带了一种使用 `describe` 函数快速计算列式汇总统计信息的简单方法。

```
coffeeOrdersDf.describe("num_items").show
+-------+-----------------+
|summary|        num_items|
+-------+-----------------+
|  count|           100102|
|   mean|10.48980040358834|
| stddev|5.772833965567237|
|    min|                1|
|    max|               20|
+-------+-----------------+
```



### 使用 `Agg` 计算复杂汇总统计量

在探索数据集时，可以使用 `describe` 快速且低成本地计算一些基本统计量。然而，如果你想获取更高级的统计量，例如中位数（第 50 百分位数）、四分位距（第 25 和第 75 百分位数之间的距离），或列中不同值数量的其他计数，则可以将这些计算移回 `aggs` 块。新建一个段落，并添加清单 12-26 中所示的代码块。

```
%spark
val summaryDf = coffeeOrdersDf
.groupBy($"year",$"month",$"store_id")
.agg(
count($"num_items") as "count",
sum($"num_items") as "total_items_sold",
stddev_pop($"num_items") as "sd", //sqrt(variance)
min($"num_items") as "daily_min_items",
ceil(avg($"num_items")) as "daily_avg_items",
max($"num_items") as "max_daily_items",
percentile_approx($"num_items",
array(lit(0.25),lit(0.50),lit(0.75),lit(0.99)), lit(95)
) as "percentiles"
)
.withColumn("daily_p25_items", $"percentiles"(0))
.withColumn("daily_median_items", $"percentiles"(1))
.withColumn("daily_p75_items", $"percentiles"(2))
.withColumn("daily_p99_items", $"percentiles"(3))
.withColumn("iqr",
floor($"daily_p75_items" - $"daily_p25_items")
)
.sort(asc("year"),asc("month"), asc("store_id"))
清单 12-26
计算汇总统计量可以让你了解数据集在给定度量（num_items）方面的整体形态
```

清单 12-26 示例中的统计聚合混合了 `describe` 方法中的所有列，并使用了 `percentile_approx` 聚合函数添加了第 25、第 50 和第 75 百分位数。

## 百分位数

百分位数用于观察一个离散数字，该数字代表一个边界，数据集中所有值（按从小到大排序）都落在此边界上或其下方。第 50 百分位数，也称为中位数，代表一组已排序数字的中点，其中 50%的所有值都等于或低于该数字。百分位数用于捕捉数据分布的形态，一种常见的分布是正态分布，如图 12-1 所示。

![](img/505711_1_En_12_Fig1_HTML.jpg)

图 12-1

正态分布呈现为所谓的“钟形”曲线

计算百分位数需要你先对给定列中的所有值按升序排序，然后利用数据集的大小（总记录数）来计算给定百分位数的值。在非常大的数据集中，这可能会耗费大量资源，而这正是 `percentile_approx` 方法发挥作用的地方。

```
percentile_approx(col, percentage, accuracy)
```

`percentile_approx` 函数根据提供的百分比参数（可以是双精度浮点数数组或单个双精度浮点数），高效地计算已排序的连续数值数据序列的百分位数。精度参数让你可以定义生成百分位数时允许的误差（数字越低，内存开销越低，但误差范围越大）。

百分位数可用于创建常见的分箱（bins），以衡量连续数值度量的频率。例如，直方图是一种数据点落入特定分箱的频率（或趋势）的图形表示。时间序列直方图分析可用于观察特定分箱的质量（百分比）随时间的变化。

清单 12-26 中的聚合还引入了数值 `ceil`（向上取整）函数，用于将平均 `num_items` 向上舍入到最接近的整数。如果你想向下舍入到最接近的整数，可以使用 `floor` 表达式。

在初始聚合之后，我们立即使用基础的列式表达式添加列，以提取百分位数数据并计算四分位距。

```
.withColumn("daily_p25_items", $"percentiles"(0))
.withColumn("daily_median_items", $"percentiles"(1))
.withColumn("daily_p75_items", $"percentiles"(2))
.withColumn("daily_p99_items", $"percentiles"(3))
.drop("percentiles")
.withColumn("iqr",
floor($"daily_p75_items" - $"daily_p25_items")
)
```

代表第 25、第 50、第 75 和第 95 百分位数的列使用了一种技术，该技术本质上是将数组值从 DataFrame 的百分位数列中提取出来，然后从最终的 DataFrame 中删除百分位数数组，因为它已不再有用（列已被提取）。最后，使用 p75-p25 计算四分位距，该范围有助于（在统计上）识别第 25 和第 75 百分位数之间的距离，在正态分布中，这个距离是相等的。能够观察数据集中的偏斜（左偏或右偏）有助于理解度量值如何随时间变化。清单 12-26 中聚合的输出将如下所示：

```
-RECORD 0------------------------------
year               | 2021
month              | 9
store_id           | store1
count              | 18951
total_items_sold   | 197421
sd                 | 5.777909019367008
daily_min_items    | 1
daily_avg_items    | 11
max_daily_items    | 20
daily_p25_items    | 5
daily_median_items | 10
daily_p75_items    | 15
daily_p99_items    | 20
iqr                | 10
```

接下来，我们将了解如何使用 `rollup` 和 `pivot`，它们提供了分析分组数据的不同方式。



#### 使用 Rollup 实现层次化聚合

Rollup 提供了另一种创建聚合的方式，它是分组集（`groupBy` 的扩展，被称为分组集）。Rollup 与 `groupBy` 的区别在于数据分析的方式。为了生成层次化聚合，rollup 会从左到右对 rollup 分组集中包含的所有列进行计算。Rollup 使你能够生成以树状结构呈现的细粒度报告。最顶层的行代表所有数据的全局聚合，然后对于每一列（从左到右），会生成额外的行，表示按每个不同列值切片的数据子集。

清单 12-27 中的代码块展示了一个创建层次化聚合的具体示例。

```
%spark
val rollupReport = ordersDf
.rollup($"store_id", $"year", $"month")
.agg(
sum($"num_items") as "total",
min($"num_items") as "min_daily",
ceil(avg($"num_items")) as "avg_daily",
max($"num_items") as "max_daily"
)
.sort(asc("year"),asc("month"),asc("store_id"))
.limit(20)
清单 12-27
在分组集 `store_id, year, month` 上使用 Rollup 聚合数据集
```

`groupBy` 与 rollup 之间的差异可以在输出 `rollupReport` 的结果时看出，如清单 12-28 所示。

```
+--------+----+-----+-------+---------+---------+---------+
|store_id|year|month|total  |min_daily|avg_daily|max_daily|
+--------+----+-----+-------+---------+---------+---------+
|null    |null|null |1050050|1        |11       |20       |
|store1  |null|null |259376 |1        |11       |20       |
|store2  |null|null |262872 |1        |11       |20       |
|store1  |2021|null |259376 |1        |11       |20       |
|store2  |2021|null |262872 |1        |11       |20       |
|store1  |2021|9    |197421 |1        |11       |20       |
|store2  |2021|9    |199030 |1        |11       |20       |
|store1  |2021|10   |61955  |1        |11       |20       |
|store2  |2021|10   |63842  |1        |11       |20       |
+--------+----+-----+-------+---------+---------+---------+
清单 12-28
Rollup 从左到右计算分组集上的层次化聚合
```

在清单 12-28 的输出中，你会看到全局聚合之后是第一个分组列（`store_id`），然后是下一层级（`store_id` 和 `year`），接着是（`store_id`、`year` 和 `month`）。这种聚合风格适用于探索性报告。接下来，我们将讨论数据透视。

#### 使用数据透视

数据透视是一种技术，它允许在众多行之间进行复杂的聚合，其方式类似于 `rollup` 和 `groupBy` 聚合。`pivot` 操作的输出是一个新的数据集，该数据集已围绕特定列（例如 `order_id` 或月份或年份）进行了*透视*。这种技术可用于生成报告，也可用于在跨多行数据中寻找模式，从而产生新颖的洞察。

我们将探索创建一个数据集，该数据集表示顾客从咖啡店可用菜单中订购的每一件商品。这些菜单项随后将与 `CoffeeOrder` 上的 `num_items` 列关联。这个新数据集将存储在一个名为 `coffee_order_items` 的表中。这种细粒度的订单信息通常可以在数据库或数据湖中用于数据挖掘和分析目的，并可用于找出顾客通常会购买的品类或典型商品。

在这个例子中，我们将把订单商品分成两个简单的类别，名为 `beverage` 和 `food`，并使用这些标识符，基于价格创建订单内所购商品的分类汇总，所有操作都通过数据透视完成。

让我们先从创建 `menu_items` 表开始。按照清单 12-29 中的代码创建 `Item` 样例类，并生成一些菜单项来完成这个数据透视教程。

```
%spark
import org.apache.spark.sql.types._
import org.apache.spark.sql._
case class Item(item_id: Int, name: String, category: String, price: Double)
val menuItems = Seq(
Item(0, "拿铁", "beverage", 3.46),
Item(1, "美式咖啡", "beverage", 2.59),
Item(2, "卡布奇诺", "beverage", 3.89),
Item(3, "手冲咖啡", "beverage", 5.26),
Item(4, "茶", "beverage", 2.99),
Item(5, "水", "beverage", 2.99),
Item(6, "饼干", "food", 2.99),
Item(7, "沙拉", "food", 4.99),
Item(8, "三明治", "food", 6.99),
Item(9, "爆米花", "food", 3.49)
)
val df = spark.createDataFrame(menuItems)
df.createOrReplaceTempView("menu_items")
清单 12-29
创建 `menu_items` 视图
```

菜单项可以通过使用 `saveAsTable` 保存以备将来重用（见清单 12-30）。

```
%spark
df
.repartition(1)
.write
.mode("overwrite")
.saveAsTable("silver.menu_items")
清单 12-30
保存菜单项以备后用
```

现在你已经有了一些可用的菜单项，让我们使用这些项，并将它们与特定 `CoffeeOrder` 的一系列商品（作为整数数组形式的 `menu_item_ids`）关联起来。我们将创建 `OrderItems` 结构来存储这个数据集。

#### 创建订单商品

我们需要创建一个名为 `OrderItems` 的新样例类，它存储一个 `orderId` 和一次订单中购买的商品序列。见清单 12-31。

```
%spark
case class OrderItems(orderId: String, items: Seq[Int])
spark.createDataFrame(Seq(
OrderItems("orderId1314", Seq(3, 7, 5, 0, 1, 1, 1)),
OrderItems("orderId1696", Seq(3, 7, 7, 5, 0, 1, 1, 1, 2, 2, 8))
)).createOrReplaceTempView("coffee_order_items")
清单 12-31
创建两行示例 OrderItem 数据并将其保存到名为 coffee_order_items 的新视图中

```

在之前的清单 12-26 中，我们研究了如何使用数组位置从百分位数数组中提取数组字段（`$"percentiles"(0)`）。对于可变长度数组，可以使用 `explode` 操作来简化这种技术。我之所以提及这一点，是因为我们很快将使用 `explode` 作为练习，以展平特定订单中的商品项，并将每个商品与其菜单项连接起来。

#### 使用数组展开从一行创建多行

处理数组数据并非总是那么直截了当。直接使用数组索引指针时，尤其是在没有保护措施的情况下，很容易在代码中引入错误。`explode` 功能是一种有用的技术，它可以从嵌套数组中提取所有数据，通过为每个项生成新行，从而展平嵌套结构。让我们看看如何利用它将一个订单的所有商品进行旋转。

作为参考，单行 `OrderItem` 如下所示。

```
Row("orderId1314", array(3, 7, 5, 0, 1, 1, 1))
```

当我们应用 `explode` 函数时，如下所示。

```
%spark
spark
.sql("select * from coffee_order_items")
.selectExpr("orderId", "explode(items)")
.show
```

我们生成了一个新 DataFrame，其中每行对应一个菜单项。

```
+-----------+---+
|    orderId|col|
+-----------+---+
|orderId1314|  3|
|orderId1314|  7|
|orderId1314|  5|
|orderId1314|  0|
|orderId1314|  1|
|orderId1314|  1|
|orderId1314|  1|
+-----------+---+
```

使用 `explode` 允许你解包数据，从一行创建多行。该技术相当于在 Scala 中使用 `flatMap` 从一个项生成一系列项。那么，我们如何利用 `explode` 和连接来计算订单的总成本呢？


好的，作为高级文档工程师和翻译员，我将严格遵循您提供的注意事项和示例，对给定的英文文本进行翻译。


#### 使用数组展开与连接来计算订单总额

现在您已经掌握了各个部分，可以将转换（`explode`）与连接（`order_items` 与 `menu_items`）混合使用，创建一个新的视图，将订单项与订单中每个菜单项的价格结合起来。请按照代码清单 12-32 中的代码块生成 `order_items_with_price` 视图。

```scala
%spark
val coffeeOrderItemsDf = spark.sql("""
SELECT orderId, EXPLODE(items) as menu_item_id
FROM coffee_order_items
""")
val menuItemsDf = spark.table("silver.menu_items")
val joinedDf = coffeeOrderItemsDf
.join(
menuItemsDf,
coffeeOrderItemsDf("menu_item_id") === menuItemsDf("item_id"),
"inner"
)
.sort(asc("orderId"))
.select($"orderId".as("order_id"), $"name", $"category", $"price")
joinedDf.createOrReplaceTempView("order_items_with_price")
Listing 12-32
使用内连接和展开运算符创建一个新视图，该视图结合了每个订单中每个项目的价格
```

现在，您已经将订单项与订单中每个项的分类和价格连接起来，您将获得一个新的视图，如下所示：

```
+-----------+----------+--------+-----+
|   order_id|      name|category|price|
+-----------+----------+--------+-----+
|orderId1314| pour over|beverage| 5.26|
|orderId1314|     water|beverage| 2.99|
|orderId1314| americano|beverage| 2.59|
|orderId1314|     salad|    food| 4.99|
+-----------+----------+--------+-----+
```

您可以使用数据透视操作来计算订单中每个子分类的项目成本，这可以作为观察 CoffeeCo 客户购买行为趋势的有效策略。

#### 使用数据透视表计算每个订单中菜单项类别的价格汇总

使用 `order_items_with_price` 视图（代码清单 12-32）中的数据，您现在可以运行代码清单 12-33 中的 SQL 语句，按 `order_id` 分组，并计算饮料和食品菜单类别的小计。

```sql
%sql
select * from order_items_with_price
pivot (
cast(sum(price) as decimal(4,1)) as category_total,
count(name) as items
for category in (
'beverage', 'food'
)
)
Listing 12-33
使用数据透视表计算跨订单的子类别总计
```

生成的聚合围绕 `order_id` 对数据进行透视，然后在命名类别上计算子聚合。结果如下所示：

```
-RECORD 0------------------------------
order_id                | orderId1314
beverage_category_total | 19.5
beverage_items          | 6
food_category_total     | 5.0
food_items              | 1
-RECORD 1------------------------------
order_id                | orderId1696
beverage_category_total | 27.3
beverage_items          | 8
food_category_total     | 17.0
food_items              | 3
```

数据透视是一种强大的分析操作，它将分组提升到了一个新的水平，可用于在数据的复杂子集上创建细项报告。我们将要学习的最后一个操作是 Spark 对分析窗口函数的支持。

### 分析窗口函数

Spark 开箱即用地提供了多种分析支持和操作。本章已经涵盖了可用的分组聚合（`groupBy`，`rollup`），并解释了如何利用强大的 `agg` 运算符来支持并行聚合。您学会了生成基本求和、提取`min`和`max`值以及计算百分位数。上一节教会您使用强大的 `pivot` 操作来转置（旋转）多行数据以创建复杂的子聚合，但所有这些先前的聚合函数都有一个共同点：它们都创建了一个全新的数据集。窗口函数的工作方式略有不同。

窗口函数能够实现与纯聚合不同的分析模式。关键区别在于，分组聚合（`groupBy`，`rollup` 等）将多个值归约为（每个分组集）一个单一值，而窗口函数则向输入数据集的每一行添加一个分析值或聚合结果作为新列，从而保留所有行。

例如，假设我们想要创建一个按小时和跨商店的订单有序索引（运行计数）。为了构造查询，我们将使用 `partitionBy` 而不是 `groupBy`，因为我们不是在进行分组聚合。`PartitionBy` 创建一个窗口规范，可用于最佳地分割数据集的一个子集并对其进行排序，以便进行分析处理。让我们看看如何使用窗口函数。代码清单 12-34 中的代码块生成了一个窗口，我们可以使用它为 `coffeeOrdersDf` 添加新列。

```scala
%spark
import org.apache.spark.sql.expressions.Window
val hourlyOrders = coffeeOrdersDf
.withColumn("timestamp",
to_timestamp($"timestamp"/1000)
)
.withColumn("hour", hour($"timestamp"))
val window = Window
.partitionBy("store_id","date","hour")
.orderBy(asc("timestamp"))
hourlyOrders
.withColumn("transaction_number", row_number.over(window))
.withColumn("running_total",sum($"num_items").over(window))
.limit(6)
.show
Listing 12-34
创建一个窗口规范，用于计算每小时的交易编号以及在窗口内累计售出的商品总数
```

该窗口表示数据集的一个视图，该视图按 `store_id`、`date` 和 `hour` 进行分区，然后将分区后的数据子集按时间戳升序排序。这将使我们能够计算从每小时第一笔交易到最后一笔交易售出的商品总数。请参见代码清单 12-35。对于每笔交易，我们看到后续每一行的商品总数（`num_items`）都会根据前一个 `num_items` 的值增加。

```
+--------+----------+----+-----------+---------+------+
|store_id|      date|hour|transaction|num_items|totals|
+--------+----------+----+-----------+---------+------+
|  store1|2021-09-06|   3|          1|       11|    11|
|  store1|2021-09-06|   3|          2|       19|    30|
|  store1|2021-09-06|   3|          3|        3|    33|
|  store1|2021-09-06|   3|          4|       20|    53|
|  store1|2021-09-06|   3|          5|        5|    58|
|  store1|2021-09-06|   3|          6|       17|    75|
+--------+----------+----+-----------+---------+------+
Listing 12-35
应用 `row_number` 和 `sum` 窗口聚合函数后的输出
```

能够向咖啡订单添加交易编号（`row_number`）和运行总计（`totals`）对于统计销售额非常有用。根据您想要分析的内容，还可以完成其他有趣的事情。



#### 计算交易间累计平均购买商品数量的差值

例如，假设你想计算两笔交易之间售出商品数量的差值，并同时计算每小时交易量的累计滚动平均差值？

这可以通过使用窗口函数中的 `lag` 来实现。Lag 意为“滞后”，滞后操作利用“当前行”、提供的列以及前方行的值，向后查找以获取数据集中任意行的值。在代码清单 12-36 中，我们使用 `lag($"num_items",1)` 向后查找一行，从而计算出交易之间购买商品数量的绝对差值。最后，我们在窗口上使用 `avg($"diff_last_sale")` 来创建累计平均值。

```spark
hourlyOrders
.withColumn("transaction", row_number.over(window))
.withColumn("totals", sum($"num_items").over(window))
.withColumn("diff_last_sale",
abs($"num_items" - lag($"num_items", 1).over(window))
)
.withColumn("avg_diff",
bround(avg($"diff_last_sale").over(window),2)
)
清单 12-36
使用窗口函数 lag(column,step) 计算上次售出商品数量与本次交易售出商品数量之间的差值
```

计算窗口函数（代码清单 12-36）的结果如下所示。

```spark
+-----------+------+--------------+--------+
|transaction|totals|diff_last_sale|avg_diff|
+-----------+------+--------------+--------+
|          1|    11|          null|    null|
|          2|    30|             8|     8.0|
|          3|    33|            16|    12.0|
|          4|    53|            17|   13.67|
|          5|    58|            15|    14.0|
|          6|    75|            12|    13.6|
|          7|    87|             5|   12.17|
|          8|    99|             0|   10.43|
+-----------+------+--------------+--------+
```

虽然输出只截取了前八笔交易，但你已经明白了。通过从 `groupBy`（分组集）转向 `partitionBy`（窗口）操作，你可以实现很多功能。通过混合搭配窗口函数的分析功能和强大的聚合能力，你可以创建复杂的报告并生成真正新颖的见解。

如果最后一个例子让你觉得关联性不强，那么设想一下，你的任务是优化 CoffeeCo 咖啡店的订单流程，你处理的数据与发现（隔离）每家店铺运营中可能存在的问题和低效环节有关。利用捕获的数据，并测量从订单创建到订单完成的时间，你可以使用相同的 `lag` 技术，结合数据透视、连接和额外的汇总操作，来计算按类别和店铺细分的菜单项物流和时间的报告。最长排队时间的原因可能是某些商品制作困难（高级手冲咖啡或食品），从菜单中移除某一商品可能就能加快所有其他交易的速度。

### 练习 12-2：总结

本章的第二个练习让你接触了如何使用分组聚合方法，并解释了如何转换数据以创建复杂的报告，从而协助你进行数据侦探工作。

## 总结

理解如何可靠地捕获、验证、清理和存储数据是数据工程师工作的一部分。能够利用行业工具高效地完成工作是需要时间和实践积累的。本章深入探讨了如何使用分析数据，并学习如何处理这些数据以理解更高级的数据转换工作，从而服务于分析场景。本章前半部分重点介绍了如何清理数据、切换时区、替换空值、创建和使用用户自定义函数。最后，我们探讨了 Apache Spark 中可用的聚合和分析处理方法及操作。

下一章内容稍短，主要关注两个简单的结构化流式应用程序。第一个是咖啡订单指标应用程序，它能够生成可用于实时仪表盘和运营的实时报告。第二个应用程序更为高级，使用了 `flatMapGroupsWithState` 操作符。在探讨部署模式和监控之前，这是我们需要编写代码的最后一章。

