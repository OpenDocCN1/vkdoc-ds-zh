# 6. SQL、NoSQL 与 PySparkSQL

在本章中，我们将探讨各种 Spark SQL 方法，当你需要对数据应用类似 SQL 的查询时，这些方法会非常有用。Apache Spark 的特色之一，就是它能让用户以编程方式和类似 ANSI SQL 的方法来应用数据整理技术。对于仅有纯 SQL 背景、对编程式数据操作接触不多的读者来说，这些 SQL 方法堪称一站式解决方案。几乎所有的 Spark 编程 API 都可以通过 Spark SQL 应用于数据。此外，这些 SQL 符合 ANSI 标准，这使得任何目前使用 SQL 技术的人都能立即开始处理基于 Apache Spark 的大数据项目。

一旦我们创建好 DataFrame，就可以直接开始对 DataFrame 对象应用 SQL，并根据需要操作数据。在本章中，我们将从简单的 SQL 入手，探讨 Apache Spark 中的各种 SQL 应用。

我们将学习以下方法，它们会循序渐进地引导你从初学者成长为高级 Spark SQL 用户。我们建议你逐一练习这些方法，以获得最佳学习效果。

-   方法 6-1. 从 CSV 文件创建 DataFrame
-   方法 6-2. 从 DataFrame 创建临时表
-   方法 6-3. 从 DataFrame 创建简单 SQL
-   方法 6-4. 在 Spark SQL 上应用 Spark UDF 方法
-   方法 6-5. 创建新的 PySpark UDF
-   方法 6-6. 使用 SQL 连接两个 DataFrame
-   方法 6-7. 使用 SQL 连接多个 DataFrame

## 方法 6-1. 从 CSV 文件创建 DataFrame

### 问题描述

你想从一个 CSV 文件创建 DataFrame。

### 解决方案

为简单起见，我们将使用非常简单的数据，以便我们能专注于逻辑和 Spark API，而不被数据本身的问题或干扰所困扰。我们将使用表 6-1 中众所周知的**学生数据**。该数据包含三列：`studentId`、`name` 和 `gender`。

**表 6-1 示例数据**

| studentId | name   | gender |
| --------- | ------ | ------ |
| si1       | Robin  | M      |
| si2       | Maria  | F      |
| si3       | Julie  | F      |
| si4       | Bob    | M      |
| si6       | William| M      |

任务是根据这些数据创建一个 DataFrame。

尽管你在之前的章节中可能已经执行过从 CSV 创建 DataFrame 的步骤，但为了保持连续性，我们还是从这里开始。此外，对于直接从本章开始学习的纯 SQL 背景读者来说，没有这个步骤会感到困惑。那么，让我们开始吧：

```
>>> studentDf = spark.read.csv('studentData.csv',
...                           header=True, inferSchema=True)
>>> studentDf.show()
+---------+-------+-------+
|studentId|   name| gender|
+---------+-------+-------+
|      si1|  Robin|      M|
|      si2|  Maria|      F|
|      si3|  Julie|      F|
|      si4|    Bob|      M|
|      si6|William|      M|
+---------+-------+-------+
```

我们已经成功地将数据转换成了 Spark DataFrame。你可能会注意到这非常简单。是的，Spark 的抽象 API 使得用户能够非常轻易地将数据加载到 DataFrame 中。

### 工作原理

让我们深入探讨一下创建 DataFrame 的代码。`spark.read.csv` 是 Spark 提供的用于读取 CSV 文件的 API。它返回一个 DataFrame 作为输出。你还可以看到在文件名之外还提供了两个参数：`inferSchema` 和 `header`。这些参数告诉 Spark，需要绑定到 DataFrame 的模式（schema）应从表头行中获取。

为了确保我们按照预期创建了正确的模式，我们可以使用 DataFrame 的 `printSchema()` 方法，如下所示。

```
>>> studentDf.printSchema()
root
|-- studentId: string (nullable = true)
|-- name: string (nullable = true)
|-- gender: string (nullable = true)
```

执行 `studentDf.printSchema()` 将为我们提供 DataFrame 的列名。

#### 步骤 6-1-1. 从 JSON 创建 DataFrame

在实时项目中，我们经常会收到 JSON 格式的数据文件。JSON 是一种高度使用的数据格式。那么，我们就从一个 JSON 文件来创建 DataFrame。为简单起见，我们使用相同的学生数据（JSON 格式）来创建 DataFrame。JSON 格式的数据如下所示。JSON 数据文件中的属性将被放置到 DataFrame 的各个列中。DataFrame 中的每个文档都将表示为 DataFrame 中的一行。手动创建 JSON 数据时很容易出现错误。如果存在任何错误，并且不会给出精确的错误信息，那么调试此类数据问题就会变得非常困难。为了避免所有此类情况，最好的办法始终是以编程方式创建 JSON 数据。

图 6-1 展示了 JSON 格式的学生数据。其中有五个文档，每个文档都包含相同的属性集合。

**图 6-1 JSON 格式的学生数据**

让我们完成从 JSON 文件创建 DataFrame 的过程：

```
>>> studentDf_json = spark.read.csv('students.json')
>>> studentDf.printSchema()
```

以下是输出结果，展示了 DataFrame 的模式。请注意，这与我们从 CSV 文件创建 DataFrame 时得到的模式完全相同。

```
root
|-- studentId: string (nullable = true)
|-- name: string (nullable = true)
|-- gender: string (nullable = true)
```

让我们使用 `show` 方法查看 DataFrame 的内容。

```
>>> studentDf.show()
```

以下是输出：

```
+---------+-------+--------+
|studentId|   name|  gender|
+---------+-------+--------+
|      si1|  Robin|       M|
|      si2|  Maria|       F|
|      si3|  Julie|       F|
|      si4|    Bob|       M|
|      si6|William|       M|
+---------+-------+--------+
```

#### 步骤 6-1-2. 从 Parquet 创建 DataFrame

到目前为止，我们已经了解了 CSV 和 JSON 文件。在处理海量数据的大数据领域，Parquet 格式因其对列式存储的支持而备受赞誉。Parquet 具有高效的压缩机制以及编码方案。Parquet 以其对查询性能的提升而闻名。

虽然我们展示了 CSV 和 JSON 文件，但我们将不展示 Parquet 文件，因为它不易于人类阅读。那么，让我们直接开始从 Parquet 文件创建 DataFrame。

这里我们有一个名为 `studentsData.parquet` 的 Parquet 文件，其中包含与加载到 CSV 和 JSON 格式中相同的**学生数据**。

```
>>> studentdf_parquet = spark.read.parquet('studentsData.parquet')
```

让我们查看 DataFrame 的模式。

```
>>> studentdf_parquet.printSchema()
```

以下是输出。你可以看到，这与我们为 CSV 和 JSON 格式看到的模式完全相同。

```
root
|-- studentId: string (nullable = true)
|-- name: string (nullable = true)
|-- gender: string (nullable = true)
```

让我们使用 `show` 方法查看 DataFrame 的内容。

```
>>> studentdf_parquet.show()
```

以下是输出：

```
+---------+-------+------+
|studentId|   name|gender|
+---------+-------+------+
|      si1|  Robin|     M|
|      si2|  Maria|     F|
|      si3|  Julie|     F|
|      si4|    Bob|     M|
|      si6|William|     M|
+---------+-------+------+
```

至此，我们拥有了应用 SQL 查询所必需的 DataFrame。Spark 的美妙之处在于，它如何能够将来自各种格式的数据抽象到其统一的 DataFrame 抽象中。这也意味着应用于 DataFrame 的数据处理操作是与文件格式无关的。例如，假设你已经完成了针对 JSON 格式数据的编程，现在想将其改为 Parquet 格式。除了在读取时更改数据格式的 API 之外，你无需修改任何代码。除此之外，为 JSON 编写的程序同样适用于 Parquet。