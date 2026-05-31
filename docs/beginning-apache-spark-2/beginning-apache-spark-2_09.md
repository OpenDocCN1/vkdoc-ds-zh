# 数据集简介

在 Spark 的发展历程中，曾有一段时间，人们对 `DataFrame` 和 `Dataset` API 之间的区别感到非常困惑。面对这些选择，我们自然会问：它们之间有何不同？每种选择各有何优缺点？何时该用哪一种？Spark 设计者认识到用户社区中存在这种巨大的困惑，因此在 Spark 2.0 中决定将 `DataFrame` API 与 `Dataset` API 统一起来，以减少用户需要学习和记忆的抽象概念。从 Spark 2.0 版本开始，只有一个名为 `Dataset` 的高级抽象，它有两种风格：强类型 API 和弱类型 API。`DataFrame` 这个术语并没有消失；相反，它被重新定义为 `Dataset` 中通用对象集合的别名。从代码角度来看，我的意思是 `DataFrame` 本质上是 `Dataset[Row]` 的类型别名，其中 `Row` 是一个通用的弱类型 JVM 对象。`Dataset` 被定义为强类型 JVM 对象的集合，在 Scala 中由 `case` 类表示，在 Java 中由类表示。表 4-10 描述了 Spark 支持的每种编程语言中可用的 `Dataset` API 风格。

Python 和 R 语言没有编译时类型安全性；因此，只支持弱类型的 `Dataset` API（即 `DataFrame`）。

**表 4-10** Dataset 风格

| 语言 | 风格 |
| --- | --- |
| Scala | `Dataset[T]` 和 `DataFrame` |
| Java | `Dataset[T]` |
| Python | `DataFrame` |
| R | `DataFrame` |

可以将 `Dataset` 视为 `DataFrame` 的“小弟”；不过，它更侧重于类型安全，并且是面向对象的。`Dataset` 是一个强类型、不可变的数据集合。与 `DataFrame` 类似，`Dataset` 中的数据会映射到已定义的 schema。然而，`DataFrame` 和 `Dataset` 之间有几个重要的区别。

- `Dataset` 中的每一行都由一个用户定义的对象表示，因此你可以将单个列作为该对象的成员变量来引用。这为你提供了编译时安全性。
- `Dataset` 拥有称为 *encoders* 的辅助工具，这些是智能且高效的编码工具，可将每个用户定义对象中的数据转换为紧凑的二进制格式。这意味着，当 `Dataset` 被缓存在内存中时，可以减少内存使用量，并且 Spark 在 shuffle 过程中需要通过网络传输的字节数也会减少。

在局限性方面，`Dataset` API 仅适用于强类型语言，如 Scala 和 Java。此时，你心中可能会浮现一个问题：何时使用 `DataFrame` API，何时使用 `Dataset` API？`Dataset` API 适用于需要定期运行、由数据工程师编写和维护的生产作业。对于大多数交互式和探索性分析用例，使用 `DataFrame` API 就足够了。

### 注意

Scala 语言中的 `case` 类类似于 Java 语言中的 `JavaBean` 类；不过，它有一些内置的有趣特性。`case` 类的实例是不可变的，因此它通常用于对特定领域的对象进行建模。此外，由于 `case` 类的实例是不可变的，因此很容易推断其内部状态。`toString` 和 `equals` 方法会自动生成，以便更轻松地打印 `case` 类的内容以及比较不同的 `case` 类实例。Scala `case` 类与 Scala 语言中的模式匹配特性配合得很好。

## 创建数据集

有几种方法可以创建 `Dataset`，但首先需要做的是定义一个特定领域的对象来表示每一行。第一种方法是使用 `DataFrame` 类的 `as(Symbol)` 函数将 `DataFrame` 转换为 `Dataset`。第二种方法是使用 `SparkSession.createDataset()` 函数从本地集合对象创建 `Dataset`。第三种方法是使用 `toDS` 隐式转换工具。代码清单 4-38 提供了使用上述不同方法创建 `Datasets` 的示例。

```
// 定义 Movie case 类
case class Movie(actor_name:String, movie_title:String, produced_year:Long)
// 将 DataFrame 转换为强类型 Dataset
val moviesDS = movies.as[Movie]
// 使用 SparkSession.createDataset() 和 toDS 隐式函数创建 Dataset
val localMovies = Seq(Movie("John Doe", "Awesome Movie", 2018L),
Movie("Mary Jane", "Awesome Movie", 2018L))
val localMoviesDS1 = spark.createDataset(localMovies)
val localMoviesDS2 = localMovies.toDS()
localMoviesDS1.show
+----------+--------------+--------------+
|actor_name|   movie_title| produced_year|
+----------+--------------+--------------+
|  John Doe| Awesome Movie|          2018|
| Mary Jane| Awesome Movie|          2018|
+----------+--------------+--------------+
代码清单 4-38
创建数据集的不同方法
```

在创建 `Datasets` 的三种方法中，第一种方法最常用。在使用 Scala `case` 类将 `DataFrame` 转换为 `Dataset` 的过程中，Spark 会执行验证，以确保 Scala `case` 类中的成员变量名称与 `DataFrame` schema 中的列名匹配。如果存在不匹配，Spark 会通知你。



### 使用数据集

现在你已经有了一个 `Dataset`，就可以使用前面介绍的转换和操作来对其进行处理。之前，你通过前面介绍的选项之一来引用 `DataFrame` 中的列。而对于 `Dataset`，每一行都由一个强类型对象表示；因此，你可以直接使用成员变量名来引用列，这不仅能提供类型安全，还能在编译时进行验证。如果名称拼写错误，编译器会在开发阶段立即标记出来。有关操作 `Dataset` 的示例，请参见清单 4-39。

```
// 使用 filter 筛选出 2010 年制作的电影
moviesDS.filter(movie => movie.produced_year == 2010).show(5)
+-------------------+---------------------+--------------+
|         actor_name|          movie_title| produced_year|
+-------------------+---------------------+--------------+
|  Cooper, Chris (I)|             The Town|          2010|
|    Jolie, Angelina|                 Salt|          2010|
|    Jolie, Angelina|          The Tourist|          2010|
|     Danner, Blythe|       Little Fockers|          2010|
| Byrne, Michael (I)| Harry Potter and ...|          2010|
+-------------------+---------------------+--------------+
// 显示 moviesDS 中第一部电影的标题
moviesDS.first.movie_title
String = Coach Carter
// 尝试拼错 movie_title，会得到编译错误
moviesDS.first.movie_tile
error: value movie_tile is not a member of Movie
// 使用 map 转换进行投影
val titleYearDS = moviesDS.map(m => ( m.movie_title, m.produced_year))
titleYearDS.printSchema
|-- _1: string (nullable = true)
|-- _2: long (nullable = false)
// 演示一个在编译时失败的类型安全转换，对字符串类型的列执行减法操作
// 对于 DataFrame，该问题直到运行时才会被发现
movies.select('movie_title - 'movie_title)
// 该问题在编译时即被发现
moviesDS.map(m => m.movie_title - m.movie_title)
error: value - is not a member of String
// 执行动作操作，将行作为 Movie 对象返回给驱动程序
moviesDS.take(5)
Array[Movie] = Array(Movie(McClure, Marc (I),Coach Carter,2005), Movie(McClure, Marc (I),Superman II,1980), Movie(McClure, Marc (I),Apollo 13,1995))
清单 4-39
以类型安全的方式操作数据集
```

对于经常使用 Scala 编程语言的人来说，使用强类型的 `Dataset` API 会感觉很自然，并且会让你觉得 `Dataset` 中的这些对象就驻留在本地。

当你使用强类型的 `Dataset` API 时，Spark 会隐式地将每个 `Row` 实例转换为你提供的领域特定对象。这种转换在性能方面会有一些开销；然而，它提供了更大的灵活性。

一个有助于决定何时使用 `DataSet` 而非 `DataFrame` 的通用准则是：你是否希望在编译时获得更高程度的类型安全。

## 在 Spark SQL 中使用 SQL

在大数据时代，SQL 被誉为大数据分析的通用语言。Spark 提供的最酷功能之一就是能够使用 SQL 来执行大规模的分布式数据操作或分析。精通 SQL 的数据分析师现在可以高效地使用 Spark 对大型数据集进行数据分析。需要记住的一个重要点是，Spark 中的 SQL 是为在线分析处理（OLAP）用例设计的，而非在线事务处理（OLTP）用例。换句话说，它不适用于低延迟的用例。

SQL 随着时间的推移不断发展和改进。Spark 实现了 ANSI SQL:2003 修订版的一个子集，大多数流行的 RDBMS 服务器都支持该版本。符合这一特定修订版意味着 Spark SQL 数据处理引擎可以通过一个现有且广泛使用的行业标准决策支持基准测试（称为 TPC-DS）进行评估。

作为 Spark SQL 引擎强大功能的证明，2016 年底，Facebook 开始将其一些最大的 Apache Hive 工作负载迁移到 Spark，以利用 Spark SQL 引擎的强大功能。更多详情请参见此文章：[`https://code.facebook.com/posts/1671373793181703/apache-spark-scale-a-60-tb-production-use-case/`](https://code.facebook.com/posts/1671373793181703/apache-spark-scale-a-60-tb-production-use-case/) 。

### 注意

结构化查询语言（SQL）是一种领域特定语言，广泛用于对以表格形式组织的结构化数据进行数据分析和操作。SQL 的概念基于关系代数；然而，它是一门易于学习的语言。SQL 与 Scala 或 Python 等其他编程语言的一个关键区别在于，SQL 是一种声明式编程语言，这意味着你只需表达你想对数据做什么，然后让 SQL 执行引擎找出必要的优化措施来加速执行时间。如果你是 SQL 新手，可以在 [`https://www.datacamp.com/courses/intro-to-sql-for-data-science`](https://www.datacamp.com/courses/intro-to-sql-for-data-science) 找到一门免费课程。



### 在 Spark 中运行 SQL

Spark 提供了几种不同的方式来运行 SQL。

- Spark SQL CLI (`./bin/spark-sql`)
- JDBC/ODBC 服务器
- 在 Spark 应用程序中以编程方式运行

前两种方式提供了与 Apache Hive 的集成，以利用 Hive 元存储。元存储是一个仓库，包含各种系统表和用户定义表的元数据和模式信息。本节将只介绍最后一种方式。

`DataFrames` 和 `Datasets` 本质上类似于数据库中的表。在你可以发出 SQL 查询来操作它们之前，需要将它们注册为临时视图。每个视图都有一个名称，该名称在 `select` 子句中用作表名。Spark 为临时视图提供了两个作用域级别。一个是 Spark 会话级别。当 `DataFrame` 在此级别注册时，只有同一会话中发出的查询才能引用该 `DataFrame`。当 Spark 会话关闭时，会话作用域级别将消失。第二个作用域级别是全局级别，这意味着这些视图对所有 Spark 会话中的 SQL 语句都可用。所有已注册的视图都维护在 Spark 元数据目录中，可以通过 `SparkSession` 访问。有关注册视图以及使用 Spark 目录检查这些视图元数据的示例，请参见清单 4-40。

```
// 显示目录中的表，预期为空列表
spark.catalog.listTables.show
+-----+---------+------------+----------+------------+
| name| database| description| tableType| isTemporary|
+-----+---------+------------+----------+------------+
// 现在将 movies DataFrame 注册为临时视图
movies.createOrReplaceTempView("movies")
// 应该能在目录中看到 movies 视图
spark.catalog.listTables.show
+-------+---------+------------+----------+------------+
|   name| database| description| tableType| isTemporary|
+-------+---------+------------+----------+------------+
| movies|     null|        null| TEMPORARY|        true|
+-------+---------+------------+----------+------------+
// 显示目录中 movies 视图的列列表
spark.catalog.listColumns("movies").show
+--------------+------------+---------+---------+------------+---------+
|          name| description| dataType| nullable| isPartition| isBucket|
+--------------+------------+---------+---------+------------+---------+
|    actor_name|        null|   string|     true|       false|    false|
|   movie_title|        null|   string|     true|       false|    false|
| produced_year|        null|   bigint|     true|       false|    false|
+--------------+------------+---------+---------+------------+---------+
// 将 movies 注册为名为 movies_g 的全局临时视图
movies.createOrReplaceGlobalTempView("movies_g")
清单 4-40
将 movies DataFrame 注册为临时视图并检查 Spark 元数据目录
```

前面的示例为你提供了几个可供选择的视图。以编程方式发出 SQL 查询的方法是使用 `SparkSession` 类的 `sql` 函数。在 SQL 语句内部，你可以访问所有 SQL 表达式和内置函数。一旦 `SparkSession.sql` 函数执行了给定的 SQL 查询，它将返回一个 `DataFrame`。发出 SQL 语句以及使用 `DataFrame` 转换和操作的能力，为你选择如何在 Spark 中执行分布式数据处理提供了极大的灵活性。清单 4-41 提供了执行简单和复杂 SQL 语句的示例。

```
// 执行不带注册视图的 SQL 语句的简单示例
val infoDF = spark.sql("select current_date() as today , 1 + 100 as value")
infoDF.show
+----------+------+
|     today| value|
+----------+------+
|2017-12-27|   101|
+----------+------+
// 从视图中查询
spark.sql("select * from movies where actor_name like '%Jolie%' and produced_year > 2009").show
+---------------+----------------+--------------+
|     actor_name|     movie_title| produced_year|
+---------------+----------------+--------------+
|Jolie, Angelina|            Salt|          2010|
|Jolie, Angelina| Kung Fu Panda 2|          2011|
|Jolie, Angelina|     The Tourist|          2010|
+---------------+----------------+--------------+
// 混合使用 SQL 语句和 DataFrame 转换
spark.sql("select actor_name, count(*) as count from movies group by actor_name")
.where('count > 30)
.orderBy('count.desc)
.show
+-------------------+------+
|         actor_name| count|
+-------------------+------+
|   Tatasciore, Fred|    38|
|      Welker, Frank|    38|
| Jackson, Samuel L.|    32|
|      Harnell, Jess|    31|
+-------------------+------+
// 使用子查询计算每年制作的电影数量。
// 利用 """ 格式化多行 SQL 语句
spark.sql("""select produced_year, count(*) as count
from (select distinct movie_title, produced_year from movies)
group by produced_year""")
.orderBy('count.desc).show(5)
+-------------+------+
|produced_year| count|
+-------------+------+
|         2006|    86|
|         2004|    86|
|         2011|    86|
|         2005|    85|
|         2008|    82|
+-------------+------+
// 从全局视图查询需要在视图名称前加上关键字 'global_temp'
spark.sql("select count(*) from global_temp.movies_g").show
+-----+
|count|
+-----+
|31393|
+-----+
清单 4-41
在 Spark 中以编程方式执行 SQL 语句
```

除了通过 `DataFrameReader` 读取数据文件，然后将新创建的 `DataFrame` 注册为临时视图之外，还可以直接从文件发出 SQL 查询。有关示例，请参见清单 4-42。

```
spark.sql("SELECT * FROM parquet.`/chapter4/data/movies/movies.parquet`").show(5)
清单 4-42
直接从文件发出 SQL 查询
```



