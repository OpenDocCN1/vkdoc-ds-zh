# 函数

DataFrame API 旨在对数据集中的单行进行操作或转换，例如过滤或分组。如果我们要转换数据集中每一行某个列的值，例如将字符串从大写转换为驼峰式，那么我们会使用函数来完成。函数本质上是应用于列的方法。Spark SQL 提供了大量常用的内置函数，同时也为我们提供了一种创建自定义函数的简便方法。

## 使用内置函数

要高效地使用 Spark SQL 执行分布式数据处理，你必须熟练掌握 Spark SQL 的内置函数。这些内置函数旨在运行时生成优化代码，因此最好优先使用它们，然后再尝试创建自己的函数。这些函数的一个共同点是，它们都设计为将同一行的一个或多个列作为输入，并且只返回单个列作为输出。Spark SQL 提供了超过 200 个内置函数，它们被分为不同的类别。这些函数可以在 DataFrame 操作中使用，例如 `select`、`filter`、`groupBy` 等。有关内置函数的完整列表，请参阅 `https://spark.apache.org/docs/latest/api/scala/index.html#org.apache.spark.sql.functions$` 上的 Spark API Scala 文档。表 5-3 将它们分为不同的类别。

**表 5-3** 每个类别的部分内置函数

| 类别 | 描述 |
| --- | --- |
| 日期时间 | `unix_timestamp`, `from_unixtime`, `to_date`, `current_date`, `current_timesatmp`, `date_add`, `date_sub`, `add_months`, `datediff`, `months_between`, `dayofmonth`, `dayofyear`, `weekofyear`, `second`, `minute`, `hour`, `month` |
| 字符串 | `concat`, `length`, `levenshtein`, `locate`, `lower`, `upper`, `ltrim`, `rtrim`, `trim`, `lpad`, `rpad`, `repeat`, `reverse`, `split`, `substring`, `base64` |
| 数学 | `cos`, `acos`, `sin`, `asin`, `tan`, `atan`, `ceil`, `floor`, `exp`, `factorial`, `log`, `pow`, `radian`, `degree`, `sqrt`, `hex`, `unhex` |
| 加密 | `cr32`, `hash`, `md5`, `sha1`, `sha2` |
| 聚合 | `approx._count_distinct`, `countDistinct`, `sumDistinct`, `avg`, `corr`, `count`, `first`, `last`, `max`, `min`, `skewness`, `sum` |
| 集合 | `array_contain`, `explode`, `from_json`, `size`, `sort_array`, `to_json`, `size` |
| 窗口 | `dense_rank`, `lag`, `lead`, `ntile`, `rank`, `row_number` |
| 杂项 | `coalesce`, `isNan`, `isnull`, `isNotNull`, `monotonically_increasing_id`, `lit`, `when` |

这些函数大多易于理解且使用简单。以下各节将提供一些有趣函数的工作示例。

### 使用日期时间函数

你使用 Spark 进行数据分析的次数越多，就越有可能遇到包含一个或多个日期或时间相关列的数据集。Spark 内置的日期时间函数大致分为以下三类：将日期或时间戳从一种格式转换为另一种格式、执行日期时间计算以及从日期或时间戳中提取特定值。

日期和时间转换函数有助于将字符串转换为日期、时间戳或 Unix 时间戳，反之亦然。它在内部使用 Java 日期格式模式语法，该语法记录在 [`http://docs.oracle.com/javase/tutorial/i18n/format/simpleDateFormat.html`](http://docs.oracle.com/javase/tutorial/i18n/format/simpleDateFormat.html) 中。这些函数使用的默认日期格式是 `yyyy-MM-dd HH:mm:ss`。因此，如果日期或时间戳列的日期格式不同，则需要向这些转换函数提供该模式。清单 5-34 展示了一个将字符串类型的日期和时间戳转换为 Spark 日期和时间戳类型的示例。

```
// 最后两列不遵循默认日期格式
val testDateTSDF = Seq((1, "2018-01-01", "2018-01-01 15:04:58:865", "01-01-2018", "12-05-2017 45:50"))
.toDF("id", "date", "timestamp", "date_str", "ts_str")
// 将这些字符串转换为日期、时间戳和 Unix 时间戳
// 并指定自定义的日期和时间戳格式
val testDateResultDF =  testDateTSDF.select(to_date('date).as("date1"),
to_timestamp('timestamp).as("ts1"),
to_date('date_str, "MM-dd-yyyy").as("date2"),
to_timestamp('ts_str, "MM-dd-yyyy mm:ss").as("ts2"),
unix_timestamp('timestamp).as("unix_ts")).show(false)
// date1 和 ts1 分别是 date 和 timestamp 类型
testDateResultDF.printSchema
|-- date1: date (nullable = true)
|-- ts1: timestamp (nullable = true)
|-- date2: date (nullable = true)
|-- ts2: timestamp (nullable = true)
|-- unix_ts: long (nullable = true)
testDateResultDF.show
+---------+-------------------+----------+--------------------+-----------+
|    date1|                ts1|     date2|                 ts2|    unix_ts|
+---------+-------------------+----------+--------------------+-----------+
|2018-01-01| 2018-01-01 15:04:58| 2018-01-01| 2017-12-05 00:45:50| 1514847898|
+---------+-------------------+----------+--------------------+-----------+
清单 5-34
将字符串类型的日期和时间戳转换为 Spark 日期和时间戳类型。
```

使用 `date_format` 函数并指定自定义日期格式，或使用 `from_unixtime` 函数将 Unix 时间戳（以秒为单位）转换为字符串，同样可以轻松地将日期或时间戳转换为时间字符串。有关转换示例，请参见清单 5-35。

```
testDateResultDF.select(date_format('date1, "dd-MM-YYYY").as("date_str"),
date_format('ts1, "dd-MM-YYYY HH:mm:ss").as("ts_str"),
from_unixtime('unix_ts,"dd-MM-YYYY HH:mm:ss").as("unix_ts_str")).show
+-----------+--------------------+--------------------+
|   date_str|              ts_str|         unix_ts_str|
+-----------+--------------------+--------------------+
| 01-01-2018| 01-01-2018 15:04:58| 01-01-2018 15:04:58|
+-----------+--------------------+--------------------+
清单 5-35
将日期、时间戳和 Unix 时间戳转换为字符串
```

日期时间计算函数对于计算两个日期或时间戳之间的差异以及执行日期或时间算术非常有用。清单 5-36 提供了日期时间计算的工作示例。



```scala
val employeeData = Seq( ("John", "2016-01-01", "2017-10-15"),
("May", "2017-02-06", "2017-12-25"))
.toDF("name", "join_date", "leave_date")
employeeData.show
+-----+-----------+-----------+
| name|  join_date| leave_date|
+-----+-----------+-----------+
| John| 2016-01-01| 2017-10-15|
|  May| 2017-02-06| 2017-12-25|
+-----+-----------+-----------+
// 执行日期和月份计算
employeeData.select('name, datediff('leave_date, 'join_date).as("days"),
months_between('leave_date, 'join_date).as("months"),
last_day('leave_date).as("last_day_of_mon"))
.show
+-----+-----+-----------+----------------+
| name| days|     months| last_day_of_mon|
+-----+-----+-----------+----------------+
| John|  653| 21.4516129|      2017-10-31|
|  May|  322|10.61290323|      2017-12-31|
+-----+-----+-----------+----------------+
// 执行日期加减操作
val oneDate = Seq(("2018-01-01")).toDF("new_year")
oneDate.select(date_add('new_year, 14).as("mid_month"),
date_sub('new_year, 1).as("new_year_eve"),
next_day('new_year, "Mon").as("next_mon")).show
+-----------+-------------+-----------+
|  mid_month| new_year_eve|   next_mon|
+-----------+-------------+-----------+
| 2018-01-15|   2017-12-31| 2018-01-08|
+-----------+-------------+-----------+
清单 5-36
日期时间计算示例
```

在处理时间序列数据时，能够提取日期或时间戳值的特定字段（如年、月、小时、分钟和秒）非常方便。例如，当需要按季度、月份或周对所有股票交易进行分组时，只需从交易日期中提取该信息并按这些值分组即可。清单 5-37 展示了从日期或时间戳中提取字段是多么简单。

```scala
val valentimeDateDF = Seq(("2018-02-14 05:35:55")).toDF("date")
valentimeDateDF.select(year('date).as("year"),
quarter('date).as("quarter"),
month('date).as("month"),
weekofyear('date).as("woy"),
dayofmonth('date).as("dom"),
dayofyear('date).as("doy"),
hour('date).as("hour"),
minute('date).as("minute"),
second('date).as("second"))
.show
+-----+--------+------+----+----+----+-----+-------+-------+
| year| quarter| month| woy| dom| doy| hour| minute| second|
+-----+--------+------+----+----+----+-----+-------+-------+
| 2018|       1|     2|   7|  14|  45|    5|     35|     55|
+-----+--------+------+----+----+----+-----+-------+-------+
清单 5-37
从日期值中提取特定字段
```

### 使用字符串函数

毫无疑问，大多数数据集中的大部分列都是字符串类型。Spark SQL 内置的字符串函数提供了通用且强大的方式来操作这种类型的列。广义上讲，这些函数分为两类。第一类是转换字符串，第二类是应用正则表达式来替换字符串的某些部分或根据模式提取字符串的特定部分。

转换字符串的方法有很多种。最常见的是修剪、填充、大写、小写和拼接。清单 5-38 演示了使用各种内置字符串函数转换字符串的不同方法。

```scala
val sparkDF = Seq(("  Spark  ")).toDF("name")
// 修剪
sparkDF.select(trim('name).as("trim"),
ltrim('name).as("ltrim"),
rtrim('name).as("rtrim"))
.show
+-----+------+------+
| trim| ltrim| rtrim|
+-----+------+------+
|Spark| Spark| Spark|
+-----+------+------+
// 使用给定的填充字符串将字符串填充到指定长度
// 首先修剪字符串 "Spark" 两边的空格，然后填充它，使最终长度为 8 个字符
sparkDF.select(trim('name).as("trim"))
.select(lpad('trim, 8, "-").as("lpad"),
rpad('trim, 8, "=").as("rpad"))
.show
+--------+--------+
|    lpad|    rpad|
+--------+--------+
|---Spark|Spark===|
+--------+--------+
// 通过拼接、大写、小写和反转来转换字符串
val sparkAwesomeDF = Seq(("Spark", "is", "awesome")).toDF("subject", "verb", "adj")
sparkAwesomeDF.select(concat_ws(" ",'subject, 'verb, 'adj).as("sentence"))
.select(lower('sentence).as("lower"),
upper('sentence).as("upper"),
initcap('sentence).as("initcap"),
reverse('sentence).as("reverse"))
.show
+-----------------+-----------------+-----------------+-----------------+
|            lower|            upper|          initcap|          reverse|
+-----------------+-----------------+-----------------+-----------------+
| spark is awesome| SPARK IS AWESOME| Spark Is Awesome| emosewa si krapS|
+-----------------+-----------------+-----------------+-----------------+
// 将一个字符转换为另一个字符
sparkAwesomeDF.select('subject, translate('subject, "ar", "oc").as("translate")).show
+--------+----------+
| subject| translate|
+--------+----------+
|   Spark|     Spock|
+--------+----------+
清单 5-38
使用内置字符串函数转换字符串的不同方法
```

正则表达式是一种强大且灵活的方式，用于替换字符串的某些部分或从字符串中提取子字符串。`regexp_extract` 和 `regexp_replace` 函数正是为此目的而设计的。Spark 利用 Java 正则表达式库来实现这两个字符串函数的底层逻辑。

`regexp_extract` 函数的输入参数包括一个字符串列、一个要匹配的模式以及一个组索引。字符串中可能存在多个模式匹配，因此需要组索引（从 0 开始）来标识具体是哪一个。如果指定模式没有匹配项，该函数将返回一个空字符串。请参见清单 5-39 中关于 `regexp_extract` 函数的示例。

```scala
val rhymeDF = Seq(("A fox saw a crow sitting on a tree singing \"Caw! Caw! Caw!\"")).toDF("rhyme")
// 使用模式
rhymeDF.select(regexp_extract('rhyme, "[a-z]*o[xw]",0).as("substring")).show
+------------+
|   substring|
+------------+
|         fox|
+------------+
清单 5-39
使用 regexp_extract 字符串函数通过模式提取 "fox"
```

`regexp_replace` 字符串函数的输入参数包括字符串列、要匹配的模式以及要替换的值。请参见清单 5-40 中关于 `regexp_replace` 函数的示例。

```scala
val rhymeDF = Seq(("A fox saw a crow sitting on a tree singing \"Caw! Caw! Caw!\"")).toDF("rhyme")
// 下面两行代码产生相同的输出
rhymeDF.select(regexp_replace('rhyme, "fox|crow", "animal").as("new_rhyme")).show(false)
rhymeDF.select(regexp_replace('rhyme, "[a-z]*o[xw]", "animal").as("new_rhyme")).show(false)
+-------------------------------------------------------------------------+
|                                 new_rhyme                               |
+-------------------------------------------------------------------------+
|A animal saw a animal sitting on a tree singing "Caw! Caw! Caw!"         |
+-------------------------------------------------------------------------+
清单 5-40
使用 regexp_replace 字符串函数将 "fox" 和 "crow" 替换为 "animal"
```



### 使用数学函数

第二常见的列类型是数值类型。这在客户交易或物联网传感器相关数据集中尤为常见。大多数数学函数都相当直观且易于使用。本节将介绍一个非常有用且常用的函数 `round`，它根据给定的精度对数值执行四舍五入。精度决定了要保留的小数位数。该函数有两种变体。第一种接受一个包含浮点值的列和一个精度参数，第二种仅接受一个包含浮点值的列。第二种变体本质上是调用第一种变体，并将精度参数设为 0。清单 5-41 演示了 `round` 函数的行为。

```
numberDF.select(round('pie).as("pie0"),
round('pie, 1).as("pie1"),
round('pie, 2).as("pie2"),
round('gpa).as("gpa"),
round('year).as("year"))
.show
// 因为是四舍五入，所以 gpa 值被舍入为 4.0
+-----+------+-----+----+-----+
| pie0|  pie1| pie2| gpa| year|
+-----+------+-----+----+-----+
|  3.0|   3.1| 3.14| 4.0| 2018|
+-----+------+-----+----+-----+
清单 5-41
演示 round 函数在不同精度下的行为
```

### 使用集合函数

集合函数旨在处理复杂数据类型，例如数组、映射和结构体。本节将介绍两种特定类型的集合函数。第一种是关于处理数组数据类型，第二种是关于处理 JSON 数据格式。

有时数据集中的特定列包含的不是单个标量值，而是一个值列表。一种建模方式是使用数组数据类型。例如，假设有一个关于每天需要完成的任务的数据集。在这个数据集中，每一行代表每天的任务列表，因此它有一个日期列，另一列包含任务列表。你可以使用与数组相关的集合函数轻松获取数组大小、检查某个值是否存在或对数组进行排序。清单 5-42 包含了使用各种数组相关函数的示例。

```
// 创建一个任务 DataFrame
val tasksDF = Seq(("Monday", Array("Pick Up John", "Buy Milk", "Pay Bill"))).toDF("day", "tasks")
// tasksDF 的模式
tasksDF.printSchema
|-- day: string (nullable = true)
|-- tasks: array (nullable = true)
|    |-- element: string (containsNull = true)
// 获取数组大小，对其进行排序，并检查数组中是否存在特定值
tasksDF.select('day, size('tasks).as("size"),
sort_array('tasks).as("sorted_tasks"),
array_contains('tasks, "Pay Bill").as("shouldPayBill"))
.show(false)
+------+-----+------------------------------------+--------------+
| day  | size|           sorted_tasks             | shouldPayBill|
+------+-----+------------------------------------+--------------+
|Monday|    3| [Buy Milk, Pay Bill, Pick Up John] |     true     |
+------+-----+------------------------------------+--------------+
// explode 函数会为数组中的每个元素创建新的一行
tasksDF.select('day, explode('tasks)).show
+-------+-------------+
|    day|          col|
+-------+-------------+
| Monday| Pick Up John|
| Monday|     Buy Milk|
| Monday|     Pay Bill|
+-------+-------------+
清单 5-42
使用数组集合函数操作任务列表
```

许多非结构化数据集采用 JSON 格式，这是一种在业界经常使用的自描述数据格式。一个常见的例子是将 Kafka 消息负载编码为 JSON 格式。由于这种格式在大多数主流编程语言中都得到广泛支持，因此用其中一种编程语言编写的 Kafka 消费者可以轻松解码这些 Kafka 消息。与 JSON 相关的集合函数对于在 JSON 字符串和结构体数据类型之间进行转换非常有用。主要的函数有 `from_json`、`get_json_object` 和 `to_json`。一旦 JSON 字符串被转换为 Spark 结构体数据类型，你就可以轻松提取这些值。请参阅清单 5-43 了解使用 `from_json` 和 `to_json` 函数的示例。

```
import org.apache.spark.sql.types._
// 创建一个包含 JSON 字符串的字符串
val todos = """{"day": "Monday","tasks": ["Pick Up John","Buy Milk","Pay Bill"]}"""
val todoStrDF = Seq((todos)).toDF("todos_str")
// 此时，todoStrDF 是一个只有一列字符串类型的 DataFrame
todoStrDF.printSchema
|-- todos_str: string (nullable = true)
// 为了将 JSON 字符串转换为 Spark 结构体数据类型，我们需要向 Spark 描述其结构
val todoSchema = new StructType().add("day", StringType).add("tasks",  ArrayType(StringType))
// 使用 from_json 转换 JSON 字符串
val todosDF = todoStrDF.select(from_json('todos_str, todoSchema).as("todos"))
// todos 是一个包含两个字段（day 和 tasks）的结构体数据类型
todosDF.printSchema
|-- todos: struct (nullable = true)
|    |-- day: string (nullable = true)
|    |-- tasks: array (nullable = true)
|    |    |-- element: string (containsNull = true)
// 使用 Column 类的 getItem 方法从结构体数据类型中检索值
todosDF.select('todos.getItem("day"),
'todos.getItem("tasks"),
'todos.getItem("tasks").getItem(0).as("first_task")
).show(false)
+---------+-------------------------------------------------+-------------+
|todos.day| todos.tasks                                     | first_task  |
+---------+-------------------------------------------------+-------------+
|   Monday| [Pick Up John, Buy Milk, Pay Bill]| Pick Up John|
+---------+-------------------------------------------------+-------------+
// 要将 Spark 结构体数据类型转换为 JSON 字符串，我们可以使用 to_json 函数
todosDF.select(to_json('todos)).show(false)
+-------------------------------------------------------------------------+
|                          structstojson(todos)                           |
+-------------------------------------------------------------------------+
| {"day":"Monday","tasks":["Pick Up John","Buy Milk","Pay Bill"]}         |
+-------------------------------------------------------------------------+
清单 5-43
使用 from_json 和 to_json 函数的示例
```



### 使用杂项函数

杂项类别中的一些函数在某些情况下既有趣又实用。本节将介绍以下函数：`monotonically_increasing_id`、`when`、`coalesce` 和 `lit`。

有时需要为数据集中的每一行生成单调递增的唯一 ID（不要求连续）。如果你花点时间思考一下，会发现这是一个相当有趣的问题。例如，如果一个数据集有 2 亿行，并且分布在许多分区（机器）上，你如何确保这些值同时具有唯一性和递增性？这就是 `monotonically_increasing_id` 函数的工作，它会生成 64 位整数形式的 ID。其算法的关键部分是将分区 ID 放在高 31 位。清单 5-44 展示了使用 `monotonically_increasing_id` 函数的示例。

```
// 首先创建一个包含 1 到 10 的值，并分布在 5 个分区中的 DataFrame
val numDF = spark.range(1,11,1,5)
// 验证有 5 个分区
numDF.rdd.getNumPartitions
Int = 5
// 现在生成单调递增的数字，并查看它们分别位于哪个分区
numDF.select('id, monotonically_increasing_id().as("m_ii"),
spark_partition_id().as("partition")).show
+---+------------+----------+
| id|        m_ii| partition|
+---+------------+----------+
|  1|           0|         0|
|  2|           1|         0|
|  3|  8589934592|         1|
|  4|  8589934593|         1|
|  5| 17179869184|         2|
|  6| 17179869185|         2|
|  7| 25769803776|         3|
|  8| 25769803777|         3|
|  9| 34359738368|         4|
| 10| 34359738369|         4|
+---+------------+----------+
// 上表显示 m_ii 列中的值在每个分区中具有不同的范围。
清单 5-44
monotonically_increasing_id 的实际应用
```

如果需要根据一组条件评估一个值并返回结果，典型的解决方案是使用 `switch` 语句，这在大多数高级编程语言中都是可用的。当需要对 DataFrame 中某列的值执行此操作时，可以使用 `when` 函数。请参见清单 5-45 中关于使用 `when` 函数的示例。

```
// 创建一个包含 1 到 7 值的 DataFrame，代表一周的每一天
val dayOfWeekDF = spark.range(1,8,1)
// 将每个数值转换为字符串
dayOfWeekDF.select('id, when('id === 1, "Mon")
.when('id === 2, "Tue")
.when('id === 3, "Wed")
.when('id === 4, "Thu")
.when('id === 5, "Fri")
.when('id === 6, "Sat")
.when('id === 7, "Sun").as("dow")
).show
+---+----+
| id| dow|
+---+----+
|  1| Mon|
|  2| Tue|
|  3| Wed|
|  4| Thu|
|  5| Fri|
|  6| Sat|
|  7| Sun|
+---+----+
// 处理默认情况时，我们可以使用列类的 otherwise 函数
dayOfWeekDF.select('id, when('id === 6, "Weekend")
.when('id === 7, "Weekend")
.otherwise("Weekday").as("day_type")
).show
+--+--------+
|id|day_type|
+--+--------+
| 1| Weekday|
| 2| Weekday|
| 3| Weekday|
| 4| Weekday|
| 5| Weekday|
| 6| Weekend|
| 7| Weekend|
+--+--------+
清单 5-45
使用 when 函数将数值转换为字符串
```

在处理数据时，正确处理空值非常重要。其中一种方法是将它们转换为数据处理逻辑中代表空值的其他值。借鉴 SQL 领域，Spark 提供了一个名为 `coalesce` 的函数，该函数接受一个或多个列值，并返回第一个非空的值。`coalesce` 函数中的每个参数都必须是 `Column` 类型，因此如果你想填充一个字面量值，可以利用 `lit` 函数。该函数的工作方式是接受一个字面量值作为输入，并返回一个包装了该输入的 `Column` 类实例。请参见清单 5-46 中同时使用 `coalesce` 和 `lit` 函数的示例。

```
// 创建一个包含空标题的电影
case class Movie(actor_name:String, movie_title:String, produced_year:Long)
val badMoviesDF = Seq( Movie(null, null, 2018L),
Movie("John Doe", "Awesome Movie", 2018L)).toDF
// 使用 coalesce 处理标题列中的空值
badMoviesDF.select(coalesce('actor_name, lit("no_name")).as("new_title")).show
+----------+
| new_title|
+----------+
|   no_name|
|  John Doe|
+----------+
清单 5-46
使用 coalesce 处理列中的空值
```



### 使用用户自定义函数

尽管 Spark SQL 为大多数常见用例提供了大量的内置函数，但总会有一些情况，这些函数无法满足你的用例所需的功能。不过，不必担心。Spark SQL 提供了一个相当简单的工具来编写用户自定义函数（UDF），并可以像使用内置函数一样，在 Spark 数据处理逻辑或应用程序中使用它们。UDF 实际上是扩展 Spark 功能以满足特定需求的方式之一。关于 Spark，我另一个非常喜欢的特点是，UDF 可以用 Python、Java 或 Scala 编写，并且可以利用和集成任何必要的库。由于你可以使用自己最熟悉的编程语言来编写 UDF，因此开发和测试 UDF 变得极其简单和快速。

从概念上讲，UDF 就是普通的函数，接收一些输入并提供一个输出。尽管 UDF 可以用 Scala、Java 或 Python 编写，但你必须注意，当 UDF 用 Python 编写时，会存在性能差异。UDF 在使用前必须在 Spark 中注册，这样 Spark 才能知道将它们发送到执行器上使用和执行。鉴于执行器是用 Scala 编写的 JVM 进程，它们可以在同一进程内原生执行 Scala 或 Java UDF。如果 UDF 是用 Python 编写的，那么执行器无法原生执行它，因此必须启动一个单独的 Python 进程来执行 Python UDF。除了启动 Python 进程的开销之外，对于数据集中的每一行数据，来回序列化数据也会产生巨大的成本。

使用 UDF 涉及三个步骤。第一步是编写一个函数并对其进行测试。第二步是通过将函数名及其签名传递给 Spark 的 `udf` 函数，向 Spark 注册该函数。最后一步是在 DataFrame 代码中或执行 SQL 查询时使用 UDF。在 SQL 查询中使用 UDF 时，注册过程略有不同。清单 5-47 通过一个简单的 UDF 演示了前面提到的三个步骤。

```
// 创建学生成绩 DataFrame
case class Student(name:String, score:Int)
val studentDF = Seq(Student("Joe", 85),
Student("Jane", 90),
Student("Mary", 55)).toDF()
// 注册为视图
studentDF.createOrReplaceTempView("students")
// 创建一个将分数转换为等级的函数
def letterGrade(score:Int) : String = {
score match {
case score if score > 100 => "作弊"
case score if score >= 90 => "A"
case score if score >= 80 => "B"
case score if score >= 70 => "C"
case _ => "F"
}
}
// 注册为 UDF
val letterGradeUDF = udf(letterGrade(_:Int):String)
// 使用 UDF 将分数转换为等级
studentDF.select($"name",$"score",
letterGradeUDF($"score").as("grade")).show
+-----+------+------+
| name| score| grade|
+-----+------+------+
|  Joe|    85|     B|
| Jane|    90|     A|
| Mary|    55|     F|
+-----+------+------+
// 注册为 UDF 以在 SQL 中使用
spark.sqlContext.udf.register("letterGrade", letterGrade(_: Int): String)
spark.sql("select name, score, letterGrade(score) as grade from students").show
+-----+------+------+
| name| score| grade|
+-----+------+------+
|  Joe|    85|     B|
| Jane|    90|     A|
| Mary|    55|     F|
+-----+------+------+
清单 5-47
一个用 Scala 编写的简单 UDF，用于将数字分数转换为字母等级
```

## 高级分析函数

前面的章节介绍了 Spark SQL 为基本分析需求（如聚合、连接、透视和分组）提供的内置函数。所有这些函数要么从单行中获取一个或多个值并产生一个输出值，要么获取一组行并返回一个输出。

本节将介绍 Spark SQL 提供的高级分析能力。第一个是关于多维聚合，这对于涉及分层数据分析的用例非常有用，在这些用例中，通常需要计算一组分组列的小计和总计。第二个能力是基于时间窗口执行聚合，这在处理时间序列数据（如来自物联网设备的事务或传感器值）时非常有用。第三个能力是在逻辑分组的行内执行聚合，这被称为*窗口*。此功能使你能够轻松执行诸如移动平均、累计和或每行排名之类的计算。

### 使用 Rollup 和 Cube 进行聚合

Rollup 和 cube 基本上是更高级的多列分组版本，它们通常用于在这些列的组合和排列上生成小计和总计。提供的列集的顺序被视为分组的分层结构。

#### Rollup

当处理分层数据（例如跨越不同部门和分部的收入数据）时，rollup 可以轻松计算它们的小计和总计。Rollup 遵循给定 rollup 列集的指定层次结构，并且始终从层次结构中的第一列开始汇总过程。总计在输出中列出，其中所有列的值均为 null。清单 5-48 演示了 rollup 的工作原理。

```
// 读取航班摘要数据
val flight_summary = spark.read.format("csv")
.option("header", "true")
.option("inferSchema","true")
.load(/chapter5/data/flights/flight-summary.csv)
// 将数据过滤到更小的规模，以便更容易查看 rollup 结果
val twoStatesSummary = flight_summary.select('origin_state, 'origin_city, 'count)
.where('origin_state === "CA" || 'origin_state === "NY")
.where('count > 1 && 'count < 20)
.where('origin_city =!= "White Plains")
.where('origin_city =!= "Newburgh")
.where('origin_city =!= "Mammoth Lakes")
.where('origin_city =!= "Ontario")
// 让我们看看数据的样子
twoStatesSummary.show
+-------------+--------------+------+
| origin_state|   origin_city| count|
+-------------+--------------+------+
|           CA|     San Diego|    18|
|           CA| San Francisco|     5|
|           CA| San Francisco|    14|
|           CA|     San Diego|     4|
|           CA| San Francisco|     2|
|           NY|      New York|     4|
|           NY|      New York|     2|
|           NY|        Elmira|    15|
|           NY|        Albany|     5|
|           NY|        Albany|     3|
|           NY|      New York|     4|
|           NY|        Albany|     9|
|           NY|      New York|    10|
+-------------+--------------+------+
// 按州、城市执行 rollup，然后计算计数的总和，最后按 null 值最后排序
twoStateSummary.rollup('origin_state, 'origin_city)
.agg(sum("count") as "total")
.orderBy('origin_state.asc_nulls_last, 'origin_city.asc_nulls_last).show
+-------------+--------------+------+
| origin_state|   origin_city| total|
+-------------+--------------+------+
|           CA|     San Diego|    22|
|           CA| San Francisco|    21|
|           CA|          null|    43|
|           NY|        Albany|    17|
|           NY|        Elmira|    15|
|           NY|      New York|    20|
|           NY|          null|    52|
|         null|          null|    95|
+-------------+--------------+------+
清单 5-48
使用航班摘要数据执行 Rollup
```

此输出显示了第三行和第七行每个州的小计，总计显示在最后一行，其中 `origin_state` 和 `origin_city` 列的值均为 null。诀窍是使用 `asc_nulls_last` 选项进行排序，这样 Spark SQL 会将 null 值排在最后。



#### 多维数据集

多维数据集本质上是上卷（rollup）的更高级版本。它对分组列的所有组合执行聚合。因此，其结果既包含上卷提供的内容，也包含其他组合。在我们按 `origin_state` 和 `origin_city` 进行多维聚合的示例中，结果将包含每个原始城市的聚合值。使用 `cube` 函数的方式与使用 `rollup` 函数类似。示例请参见清单 5-49。

```
// 对 origin_state 和 origin_city 执行多维聚合
twoStateSummary.cube('origin_state, 'origin_city)
.agg(sum("count") as "total")
.orderBy('origin_state.asc_nulls_last, 'origin_city.asc_nulls_last).show
// 结果如下
+-------------+--------------+------+
| origin_state|   origin_city| total|
+-------------+--------------+------+
|           CA|     San Diego|    22|
|           CA| San Francisco|    21|
|           CA|          null|    43|
|           NY|        Albany|    17|
|           NY|        Elmira|    15|
|           NY|      New York|    20|
|           NY|          null|    52|
|         null|        Albany|    17|
|         null|        Elmira|    15|
|         null|      New York|    20|
|         null|     San Diego|    22|
|         null| San Francisco|    21|
|         null|          null|    95|
+-------------+--------------+------+
清单 5-49
对 origin_state 和 origin_city 列执行多维聚合
```

在结果表中，`origin_state` 列中值为 null 的行表示该州所有城市的聚合值。因此，多维数据集的结果行数总是多于上卷的结果行数。

### 时间窗口聚合

时间窗口聚合是在 Spark 2.0 中引入的，旨在简化时间序列数据的处理。时间序列数据由一系列按时间顺序排列的数据点组成。这类数据集在金融或电信等行业中很常见。例如，股票市场交易数据集包含每只股票代码的交易日期、开盘价、收盘价、成交量和其他信息。时间窗口聚合有助于回答诸如苹果股票的周平均收盘价或苹果股票跨周的月移动平均收盘价等问题。

窗口函数有几种版本，但它们都需要一个时间戳类型的列和一个窗口长度，窗口长度可以以秒、分钟、小时、天或周为单位指定。窗口长度代表一个具有开始时间和结束时间的时间窗口，用于确定特定时间序列数据应属于哪个桶。另一个版本额外接收滑动窗口大小作为输入，该参数指示在计算下一个桶时，时间窗口应滑动多少。这些版本的窗口函数是世界事件处理中翻转窗口和滑动窗口概念的实现，将在第 6 章中更详细地描述。

以下示例将使用苹果股票交易数据，这些数据可以在雅虎财经网站上找到，网址为 [`https://in.finance.yahoo.com/q/hp?s=AAPL`](https://in.finance.yahoo.com/q/hp?s=AAPL)。清单 5-50 基于一年的数据计算了苹果股票的周平均价格。

```
val appleOneYearDF = spark.read.format("csv")
.option("header", "true")
.option("inferSchema","true")
.load("/chapter5/data/stocks/aapl-2017.csv")
// 显示模式，第一列是交易日期
appleOneYearDF.printSchema
|-- Date: timestamp (nullable = true)
|-- Open: double (nullable = true)
|-- High: double (nullable = true)
|-- Low: double (nullable = true)
|-- Close: double (nullable = true)
|-- Adj Close: double (nullable = true)
|-- Volume: integer (nullable = true)
// 使用 groupBy 转换中的窗口函数计算周平均价格
// 这是翻转窗口（即固定窗口）的示例
val appleWeeklyAvgDF = appleOneYearDF.groupBy(window('Date, "1 week"))
.agg(avg("Close").as("weekly_avg"))
// 结果模式包含窗口的开始和结束时间
appleWeeklyAvgDF.printSchema
|-- window: struct (nullable = true)
|    |-- start: timestamp (nullable = true)
|    |-- end: timestamp (nullable = true)
|-- weekly_avg: double (nullable = true)
// 按开始时间排序并四舍五入到两位小数显示结果
appleWeeklyAvgDF.orderBy("window.start")
.selectExpr("window.start", "window.end",
"round(weekly_avg, 2) as weekly_avg")
.show(5)
// 注意开始时间包含在内，结束时间不包含在内
+--------------------+--------------------+---------------+
|               start|                 end|     weekly_avg|
+--------------------+--------------------+---------------+
| 2016-12-28 16:00:00| 2017-01-04 16:00:00|         116.08|
| 2017-01-04 16:00:00| 2017-01-11 16:00:00|         118.47|
| 2017-01-11 16:00:00| 2017-01-18 16:00:00|         119.57|
| 2017-01-18 16:00:00| 2017-01-25 16:00:00|         120.34|
| 2017-01-25 16:00:00| 2017-02-01 16:00:00|         123.12|
+--------------------+--------------------+---------------+
清单 5-50
使用时间窗口函数计算苹果股票的平均收盘价
```

前面的示例使用了一周的翻转窗口，其中没有重叠。因此，每笔交易仅被使用一次来计算移动平均。清单 5-51 中的示例使用了滑动窗口。这意味着某些交易在计算月平均移动平均时会被多次使用。窗口大小为四周，每次滑动一周。

```
// 窗口长度为 4 周，每次滑动 1 周
val appleMonthlyAvgDF = appleOneYearDF.groupBy(window('Date, "4 week", "1 week"))
.agg(avg("Close").as("monthly_avg"))
// 按开始时间排序显示结果
appleMonthlyAvgDF.orderBy("window.start")
.selectExpr("window.start", "window.end", "round(monthly_avg, 2) as monthly_avg")
.show(5)
+--------------------+--------------------+------------+
|               start|                 end| monthly_avg|
+--------------------+--------------------+------------+
| 2016-12-07 16:00:00| 2017-01-04 16:00:00|      116.08|
| 2016-12-14 16:00:00| 2017-01-11 16:00:00|      117.79|
| 2016-12-21 16:00:00| 2017-01-18 16:00:00|      118.44|
| 2016-12-28 16:00:00| 2017-01-25 16:00:00|      119.03|
| 2017-01-04 16:00:00| 2017-02-01 16:00:00|      120.42|
+--------------------+--------------------+------------+
清单 5-51
使用时间窗口函数计算苹果股票的月平均收盘价
```

由于滑动窗口间隔为一周，上述结果表显示，连续两行之间的开始时间相差一周。在连续两行之间，大约有三周的交易是重叠的，这意味着同一笔交易被多次用于计算移动平均。



### 窗口函数

到目前为止，你已经知道如何使用诸如 `concat` 或 `round` 之类的函数，根据单行的一个或多个列值计算输出，并利用诸如 `max` 或 `sum` 之类的聚合函数为每一组行计算输出。有时，我们需要对一组行进行操作，并为每一行输入返回一个值。窗口函数提供了这种独特的能力，可以轻松执行诸如移动平均、累计求和或每行排名之类的计算。

使用窗口函数主要有两个步骤。第一步是定义一个窗口规范，该规范定义了行的逻辑分组，称为*帧*，这是评估每一行的上下文。第二步是应用一个适合你试图解决问题的窗口函数。你可以在以下章节中找到关于可用窗口函数的更多详细信息。

窗口规范定义了窗口函数将使用的三个重要组成部分。第一个组成部分称为*分区依据*，在这里你可以指定一个或多个列来对行进行分组。第二个组成部分称为*排序依据*，它定义了如何根据一个或多个列对行进行排序，以及排序是升序还是降序。在这三个组成部分中，最后一个更为复杂，需要详细解释。最后一个组成部分称为*帧*，它定义了相对于当前行的窗口边界。换句话说，“帧”限制了在计算当前行的值时应包含哪些行。可以使用行索引或排序依据表达式的实际值来指定要包含在*窗口帧*中的行范围。最后一个组成部分适用于某些窗口函数，因此在某些场景下可能不是必需的。窗口规范是使用 `org.apache.spark.sql.expressions.Window` 类中定义的函数构建的。`rowsBetween` 和 `rangeBetween` 函数分别用于通过行索引和实际值来定义范围。

窗口函数可以分为三种不同的类型：排名函数、分析函数和聚合函数。排名函数和分析函数分别在表 5-4 和表 5-5 中描述。对于聚合函数，你可以使用前面提到的任何聚合函数作为窗口函数。你可以在 [`https://spark.apache.org/docs/latest/api/java/org/apache/spark/sql/functions.html`](https://spark.apache.org/docs/latest/api/java/org/apache/spark/sql/functions.html) 找到窗口函数的完整列表。

**表 5-5** 分析函数

| 名称 | 描述 |
| --- | --- |
| `cume_dist` | 返回帧内值的累积分布。换句话说，即低于当前行的行所占的比例。 |
| `lag(col, offset)` | 返回当前行之前 `offset` 行的列值。 |
| `lead(col, offset)` | 返回当前行之后 `offset` 行的列值。 |

**表 5-4** 排名函数

| 名称 | 描述 |
| --- | --- |
| `rank` | 根据某种排序顺序返回帧内行的排名或顺序。 |
| `dense_rank` | 类似于 `rank`，但在出现并列时，排名中不留空位。 |
| `percent_rank` | 返回帧内行的相对排名。 |
| `ntile(n)` | 返回有序窗口分区中的 ntile 组 ID。例如，如果 `n` 是 4，则前四分之一的行将获得值 1，第二个四分之一的行将获得值 2，依此类推。 |
| `row_number` | 返回从 1 开始的帧内顺序编号。 |

让我们通过处理一个小样本数据集来将上述步骤结合起来，以演示窗口函数的功能。表 5-6 包含两个虚构用户 John 和 Mary 的购物交易数据。

**表 5-6** 用户购物交易

| 名称 | 日期 | 金额 |
| --- | --- | --- |
| `John` | 2017-07-02 | 13.35 |
| `John` | 2016-07-06 | 27.33 |
| `John` | 2016-07-04 | 21.72 |
| `Mary` | 2017-07-07 | 69.74 |
| `Mary` | 2017-07-01 | 59.44 |
| `Mary` | 2017-07-05 | 80.14 |

利用这些购物交易数据，让我们尝试使用窗口函数来回答以下问题：

- 对于每个用户，交易金额最高的两笔是多少？
- 每个用户的交易金额与其最高交易金额之间的差额是多少？
- 每个用户的移动平均交易金额是多少？
- 每个用户的交易金额累计总和是多少？

为了回答第一个问题，你可以在一个窗口规范上应用 `rank` 窗口函数，该规范按用户对数据进行分区，并按金额降序排序。排名窗口函数根据每个帧中每行的排序顺序为每行分配一个排名。解决第一个问题的实际代码见清单 5-52。

```
// 两个用户的小型购物交易数据集
val txDataDF= Seq(("John", "2017-07-02", 13.35),
("John", "2017-07-06", 27.33),
("John", "2017-07-04", 21.72),
("Mary",  "2017-07-07", 69.74),
("Mary",  "2017-07-01", 59.44),
("Mary",  "2017-07-05", 80.14))
.toDF("name", "tx_date", "amount")
// 导入 Window 类
import org.apache.spark.sql.expressions.Window
// 定义窗口规范，按名称分区并按金额降序排序
val forRankingWindow = Window.partitionBy("name").orderBy(desc("amount"))
// 添加一个新列以包含每行的排名，应用 rank 函数对每行进行排名
val txDataWithRankDF = txDataDF.withColumn("rank", rank().over(forRankingWindow))
// 根据排名过滤行以找到前两名并显示结果
txDataWithRankDF.where('rank < 3).show(10)
+------+-----------+-------+-----+
|  name|    tx_date| amount| rank|
+------+-----------+-------+-----+
|  Mary| 2017-07-05|  80.14|    1|
|  Mary| 2017-07-07|  69.74|    2|
|  John| 2017-07-06|  27.33|    1|
|  John| 2017-07-04|  21.72|    2|
+------+-----------+-------+-----+
清单 5-52
应用排名窗口函数找出每个用户的前两笔交易
```

解决第二个问题的方法涉及在金额列上应用 `max` 函数，该函数作用于每个分区的所有行。除了按用户名分区外，还需要定义一个包含每个分区中所有行的帧边界。为此，你使用 `Window.rangeBetween` 函数，并将 `Window.unboundedPreceding` 作为起始值，`Window.unboundedFollowing` 作为结束值。清单 5-53 根据前面定义的逻辑定义了一个窗口规范，并对其应用了 `max` 函数。



```scala
// 使用 rangeBetween 定义包含每个分区内所有行的窗口边界
val forEntireRangeWindow = Window.partitionBy("name")
.orderBy(desc("amount"))
.rangeBetween(Window.unboundedPreceding,
Window.unboundedFollowing)
// 对 amount 列应用 max 函数，然后计算差值
val amountDifference = max(txDataDF("amount")).over(forEntireRangeWindow) - txDataDF("amount")
// 使用上述逻辑添加 amount_diff 列
val txDiffWithHighestDF = txDataDF.withColumn("amount_diff", round(amountDifference, 3))
// 显示结果
txDiffWithHighestDF.show
+------+-----------+-------+-------------+
|  name|    tx_date| amount|  amount_diff|
+------+-----------+-------+-------------+
|  Mary| 2017-07-05|  80.14|          0.0|
|  Mary| 2017-07-07|  69.74|         10.4|
|  Mary| 2017-07-01|  59.44|         20.7|
|  John| 2017-07-06|  27.33|          0.0|
|  John| 2017-07-04|  21.72|         5.61|
|  John| 2017-07-02|  13.35|        13.98|
+------+-----------+-------+-------------+
清单 5-53
应用 max 窗口函数计算每行与最高金额的差值
```

为了按交易日期顺序计算每个用户的交易金额移动平均值，你将利用 `avg` 函数，基于窗口中的一组行来计算每行的平均金额。在此特定示例中，你希望每个窗口包含三行：当前行及其前一行和后一行。根据特定的用例，窗口可能包含当前行之前和之后的更多行。与之前的示例类似，窗口规范将按用户对数据进行分区，但每个窗口中的行将按交易日期排序。清单 5-54 展示了如何对前面描述的窗口规范应用 `avg` 函数。

```scala
// 定义窗口规范
// 最佳实践是相对于 Window.currentRow 指定偏移量
val forMovingAvgWindow = Window.partitionBy("name").orderBy("tx_date")
.rowsBetween(Window.currentRow-1,Window.currentRow+1)
// 对窗口规范中的 amount 列应用平均值函数
// 并将移动平均值四舍五入到两位小数
val txMovingAvgDF = txDataDF.withColumn("moving_avg",
round(avg("amount").over(forMovingAvgWindow), 2))
// 显示结果
txMovingAvgDF.show
+------+-----------+-------+-----------+
|  name|    tx_date| amount| moving_avg|
+------+-----------+-------+-----------+
|  Mary| 2017-07-01|  59.44|      69.79|
|  Mary| 2017-07-05|  80.14|      69.77|
|  Mary| 2017-07-07|  69.74|      74.94|
|  John| 2017-07-02|  13.35|      17.54|
|  John| 2017-07-04|  21.72|       20.8|
|  John| 2017-07-06|  27.33|      24.53|
+-------+----------+-------+-----------+
清单 5-54
应用平均值窗口函数计算交易金额的移动平均值
```

为了计算每个用户交易金额的累计总和，你将在一个包含截至当前行的所有行的窗口上应用 `sum` 函数。`partition by` 和 `order by` 子句与移动平均示例相同。清单 5-55 展示了如何对前面描述的窗口规范应用 `sum` 函数。

```scala
// 定义窗口规范，每个窗口包含所有之前的行和当前行
val forCumulativeSumWindow = Window.partitionBy("name").orderBy("tx_date")
.rowsBetween(Window.unboundedPreceding,Window.currentRow)
// 对窗口规范应用 sum 函数
val txCumulativeSumDF = txDataDF.withColumn("culm_sum",
round(sum("amount").over(forCumulativeSumWindow),2))
// 显示结果
txCumulativeSumDF.show
+------+-----------+-------+---------+
|  name|    tx_date| amount| culm_sum|
+------+-----------+-------+---------+
|  Mary| 2017-07-01|  59.44|    59.44|
|  Mary| 2017-07-05|  80.14|   139.58|
|  Mary| 2017-07-07|  69.74|   209.32|
|  John| 2017-07-02|  13.35|    13.35|
|  John| 2017-07-04|  21.72|    35.07|
|  John| 2017-07-06|  27.33|     62.4|
+------+-----------+-------+---------+
清单 5-55
应用 sum 窗口函数计算交易金额的累计总和
```

窗口规范的默认窗口包含所有之前的行以及当前行。对于前面的示例，无需指定窗口，因此你应该得到相同的结果。

前面的窗口函数示例是使用 DataFrame API 编写的。使用 SQL 中的 `PARTITION BY`、`ORDER BY`、`ROWS BETWEEN` 和 `RANGE BETWEEN` 关键字也可以实现相同的目标。窗口边界可以使用以下关键字指定：`UNBOUNDED PRECEDING`、`UNBOUNDED FOLLOWING`、`CURRENT ROW`、`<value> PRECEDING` 和 `<value> FOLLOWING`。清单 5-56 展示了在 SQL 中使用窗口函数的示例。

```sql
// 将 txDataDF 注册为名为 tx_data 的临时视图
txDataDF.createOrReplaceTempView("tx_data")
// 使用 RANK 窗口函数查找前两笔最高交易金额
spark.sql("select name, tx_date, amount, rank from
(
select name, tx_date, amount,
RANK() OVER (PARTITION BY name ORDER BY amount DESC) as rank from tx_data
) where rank < 3").show
// 最大交易金额的差值
spark.sql("select name, tx_date, amount, round((max_amount - amount),2) as amount_diff from
(
select name, tx_date, amount, MAX(amount) OVER
(PARTITION BY name ORDER BY amount DESC
RANGE BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING
) as max_amount from tx_data)"
).show
// 移动平均值
spark.sql("select name, tx_date, amount, round(moving_avg,2) as moving_avg from
(
select name, tx_date, amount, AVG(amount) OVER
(PARTITION BY name ORDER BY tx_date
ROWS BETWEEN 1 PRECEDING AND 1 FOLLOWING
) as moving_avg from tx_data)"
).show
// 累计总和
spark.sql("select name, tx_date, amount, round(culm_sum,2) as moving_avg from
(
select name, tx_date, amount, SUM(amount) OVER
(PARTITION BY name ORDER BY tx_date
ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW
) as culm_sum from tx_data)"
).show
清单 5-56
SQL 中窗口函数的示例
```

在 SQL 中使用窗口函数时，`partition by`、`order by` 和窗口框架必须在单个语句中指定。



