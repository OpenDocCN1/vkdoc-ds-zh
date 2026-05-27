# 连接操作

对流式数据帧（DataFrame）最酷的操作之一，就是将其与静态数据帧或另一个流式数据帧进行连接。然而，连接是一项复杂的操作，棘手之处在于，进行连接时，流式数据帧的所有数据并非全部可用。因此，连接的结果会在每个触发点增量生成，这与聚合结果的生成方式类似。

从 Spark 2.3 开始，Structured Streaming 支持连接两个流式数据帧。鉴于流式数据帧的无界特性，Structured Streaming 必须保留两个流式数据帧的过往数据，以便与未来尚未接收到的数据进行匹配。为了避免 Structured Streaming 必须维护的流式状态无限膨胀，可以为两个流式数据帧可选地提供水印（watermark），并且必须在连接条件中定义事件时间约束。让我们来看一个物联网用例，该用例连接了一个数据中心中两个与数据传感器相关的数据流。第一个数据流包含数据中心内各个位置的温度读数，第二个数据流包含同一数据中心内每台计算机的负载信息。这两个数据流的连接条件基于位置。清单 6-11 包含了在连接条件中提供水印和事件时间约束的代码。

```
import org.apache.spark.sql.functions.expr
// 在此示例中，具体的流式数据源信息并不重要
val tempDataDF = spark.readStream. ...
val loadDataDF = spark.readStream. ...
val tempDataWatermarkDF = tempDataDF.withWaterMark("temp_taken_time", "1 hour")
val loadDataWatermarkDF = loadDataDF.withWaterMark("load_taken_time", "2 hours")
// 基于位置 ID 以及事件时间约束进行连接
tempWithLoadDataDF = tempDataWatermarkDF.join(loadDataWatermarkDF,
expr(""" temp_location_id = load_location_id AND
load_taken_time >= temp_taken_time AND
load_taken_time <= temp_taken_time + interval 1 hour
""")
)
```
清单 6-11 连接两个流式数据帧

在连接静态数据帧和流式数据帧，以及连接两个流式数据帧时，对外连接有更多限制。表 6-4 提供了一些相关细节。

**表 6-4** 关于连接流式数据帧的一些细节

| 左侧 + 右侧 | 连接类型 | 备注 |
| --- | --- | --- |
| 静态 + 流式 | 内连接 | 支持。 |
| 静态 + 流式 | 左外连接 | 不支持。 |
| 静态 + 流式 | 右外连接 | 支持。 |
| 静态 + 流式 | 全外连接 | 不支持。 |
| 流式 + 流式 | 内连接 | 支持。 |
| 流式 + 流式 | 左外连接 | 有条件支持。必须在右侧指定水印和时间约束。 |
| 流式 + 流式 | 右外连接 | 有条件支持。必须在左侧指定水印和时间约束。 |
| 流式 + 流式 | 全外连接 | 不支持。 |

# 使用数据源

上一节描述了 Structured Streaming 提供的每个内置数据源。本节将更详细地介绍，并提供使用它们的示例代码。

`Socket` 和 `Rate` 数据源仅用于测试和学习目的，不应在生产环境中使用。

## 使用 Socket 数据源

`Socket` 数据源使用起来相当简单，只需要连接的主机和端口信息。在启动 `Socket` 数据源的流式查询之前，务必先使用网络命令行工具（如 Mac 上的 `nc` 或 Windows 上的 `netcat`）启动一个 Socket 服务器。在此示例中，使用了 `nc` 网络工具。你需要两个终端。第一个终端用于启动一个端口号为 9999 的 Socket 服务器；命令是 `nc -lk 9999`。第二个终端运行包含清单 6-12 中代码的 Spark shell。

```
val socketDF = spark.readStream.format("socket")
.option("host", "localhost")
.option("port", "9999").load()
val words = socketDF.as[String].flatMap(_.split(" "))
val wordCounts = words.groupBy("value").count()
val query = wordCounts.writeStream.format("console")
.outputMode("complete")
.start()
```
清单 6-12 从 Socket 数据源读取流式数据

现在回到第一个窗口，输入 `Spark is great`，然后按回车键。接着输入 `Spark is awesome`，再按回车键。按回车键会告诉 Netcat 服务器将输入的内容发送给 Socket 监听器。如果一切顺利，Spark shell 控制台中应该会输出两个批次，如清单 6-13 所示，每个批次都包含每个单词的计数。由于 Structured Streaming 会跨批次维护状态，因此它能够将 `Spark` 和 `is` 这两个单词的计数更新为 2。

```
批次: 0

+------+------+
| value| count|
+------+------+
| great|     1|
|    is|     1|
| Spark|     1|
+------+------+

批次: 1

+-------+------+
|  value| count|
+-------+------+
|  great|     1|
|     is|     2|
|awesome|     1|
|  Spark|     2|
+-------+------+
```
清单 6-13 Spark shell 控制台中 Socket 数据源的输出

当你完成对 Socket 数据源的测试后，可以通过调用 `stop` 函数来停止流式查询，如清单 6-14 所示。正如预期的那样，流式查询停止后，在第一个终端中输入任何内容都不会在 Spark shell 中显示任何结果。

```
query.stop
```
清单 6-14 停止 Socket 数据源的流式查询

## 使用 Rate 数据源

与 `Socket` 数据源类似，`Rate` 数据源仅用于测试和学习目的。它支持少量选项，其中之一是每秒生成的行数。如果该数值较高，则可以提供另一个可选配置，即达到每秒行数所需的预热时间。`Rate` 数据源生成的每条数据仅包含两列：时间戳和自增值。清单 6-15 包含了从 `Rate` 数据源打印数据的代码，以及控制台中第一批数据的显示效果。

```
// 配置为每秒生成 10 行数据
val rateSourceDF = spark.readStream.format("rate")
.option("rowsPerSecond","10")
.load()
val rateQuery = rateSourceDF.writeStream
.outputMode("update")
.format("console")
.option("truncate", "false")
.start()

Batch: 1

+-----------------------+------+
|              timestamp| value|
+-----------------------+------+
|2018-03-19 10:30:21.952|     0|
|2018-03-19 10:30:22.052|     1|
|2018-03-19 10:30:22.152|     2|
|2018-03-19 10:30:22.252|     3|
|2018-03-19 10:30:22.352|     4|
|2018-03-19 10:30:22.452|     5|
|2018-03-19 10:30:22.552|     6|
|2018-03-19 10:30:22.652|     7|
|2018-03-19 10:30:22.752|     8|
|2018-03-19 10:30:22.852|     9|
+-----------------------+------+
```
清单 6-15 使用 Rate 数据源

一个值得注意的有趣点是，`value` 列中的数字在所有分区中保证是连续的。清单 6-16 展示了三个分区时的输出效果。

```
import org.apache.spark.sql.functions._
// 使用 3 个分区
val rateSourceDF2 = spark.readStream.format("rate")
.option("rowsPerSecond","10")
.option("numPartitions",3).load()
// 添加分区 ID 列以便检查
val rateWithPartitionDF = rateSourceDF2.withColumn("partition_id", spark_partition_id())
val rateWithPartitionQuery = rateWithPartitionDF.writeStream
.outputMode("update")
.format("console")
.option("truncate", "false")
.start()
// 第一批的输出

Batch: 1

+-----------------------+------+-------------+
|              timestamp| value| partition_id|
+-----------------------+------+-------------+
|2018-03-24 08:46:43.412|     0|            0|
|2018-03-24 08:46:43.512|     1|            0|
|2018-03-24 08:46:43.612|     2|            0|
|2018-03-24 08:46:43.712|     3|            1|
|2018-03-24 08:46:43.812|     4|            1|
|2018-03-24 08:46:43.912|     5|            1|
|2018-03-24 08:46:44.012|     6|            2|
|2018-03-24 08:46:44.112|     7|            2|
|2018-03-24 08:46:44.212|     8|            2|
|2018-03-24 08:46:44.312|     9|            2|
+-----------------------+------+-------------+
```
清单 6-16 带有分区 ID 的 Rate 数据源输出

上述输出显示，这十行数据分布在三个分区中，但数值仍然是连续的，就像它们是为单个分区生成的一样。如果你对这个数据源的实现感到好奇，可以查看 [`https://github.com/apache/spark/blob/master/sql/core/src/main/scala/org/apache/spark/sql/execution/streaming/RateSourceProvider.scala`](https://github.com/apache/spark/blob/master/sql/core/src/main/scala/org/apache/spark/sql/execution/streaming/RateSourceProvider.scala)。

## 使用 File 数据源

`File` 数据源是最容易理解和使用的一种。假设需要处理定期复制到某个目录中的新文件，那么这将是该场景下的完美数据源。它开箱即用地支持所有常用的文件格式，包括文本、CSV、JSON、ORC 和 Parquet。有关支持的文件格式的完整列表，请查阅 `DataStreamReader` 接口。在 `File` 数据源支持的四个选项中，只有用于读取文件的输入目录是必需的。

当新文件被复制到指定目录时，`File` 数据源会拾取所有这些文件进行处理。也可以配置 `File` 数据源，使其仅选择性地拾取固定数量的新文件进行处理。用于指定文件数量的选项是 `maxFilesPerTrigger`。清单 6-17 提供了一个示例，演示如何从目录中读取 JSON 移动数据事件，并使用清单 6-3 中定义的相同模式。`File` 数据源支持的另一个有趣的可选选项是，是否在处理旧文件之前先处理最新文件。文件的最后时间戳用于判断哪个文件更新。默认行为是从最旧的文件开始处理到最新的文件。当有大量积压文件需要处理，并且你想优先处理新文件时，这个特定选项会非常有用。

```
val mobileSSDF = spark.readStream.schema(mobileDataSchema).json("")
// 如果我们想指定 maxFilesPerTrigger
val mobileSSDF =  spark.readStream.schema(mobileDataSchema).
option("maxFilesPerTrigger", 5).json("")
// 如果我们想先处理新文件
val mobileSSDF =  spark.readStream.schema(mobileDataSchema).
option("latestFirst", "true").json("")
```
清单 6-17 使用 File 数据源

### 使用 Kafka 数据源

Kafka 数据源可能是生产环境流式应用中最常用的数据源。要高效使用该数据源，你需要掌握一定的 Kafka 基础知识。从高层来看，该数据源扮演着 Kafka 消费者的角色；因此，它所需的信息与 Kafka 消费者所需的信息类似。其中包含两项必填信息和若干可选信息。

两项必填信息分别是：要连接的 Kafka 服务器列表，以及要读取数据的一个或多个主题信息。为了支持多种方式选择要读取数据的主题及主题分区，它提供了三种不同的方式来指定这些信息。你只需选择最适合自己用例的方式即可。表 6-5 详细说明了这两项必填选项。

#### 表 6-5 Kafka 数据源的必填选项

| 选项 | 值 | 描述 |
| --- | --- | --- |
| `kafka.bootstrap.servers` | `host1:port1`, `host2:port2` | 这是 Kafka 代理服务器列表，以逗号分隔。请咨询你的 Kafka 管理员以获取要使用的主机名和端口号。 |
| `subscribe` | `topic1`, `topic2` | 这是该数据源要读取数据的主题名称列表，以逗号分隔。 |
| `subscribePattern` | `topic.*` | 这是一个正则表达式模式，用于指定要读取数据的主题。这比 `subscribe` 选项更灵活一些。 |
| `assign` | `{ "topic1": [1,2], "topic2": [3,4] }` | 使用此选项，你可以指定要读取数据的具体主题分区列表。此信息必须以 JSON 格式提供。 |

指定这些必填选项后，你可以选择性地指定表 6-6 中的选项，该表仅包含一些常用选项。有关可选选项的完整列表，请参阅 Structured Streaming 和 Kafka 集成指南，网址为 [`https://spark.apache.org/docs/latest/structured-streaming-kafka-integration.html`](https://spark.apache.org/docs/latest/structured-streaming-kafka-integration.html)。这些选项之所以是可选的，是因为它们都有默认值。

`startingOffsets` 和 `endingOffsets` 选项允许你精细控制从特定主题的特定分区的特定点开始处理 Kafka 中的数据。这种灵活性在以下场景中极为有用：因故障或软件新版本引入的某些错误而需要重新处理数据时，或者重新训练机器学习模型时。能够重新处理 Kafka 中的数据是 Kafka 在大数据处理领域广受欢迎的原因之一。显而易见，Kafka 数据源使用 `startingOffsets` 来确定从 Kafka 中的何处开始读取数据，因此，一旦处理开始，该选项便不再使用。Kafka 数据源使用 `endingOffsets` 选项来确定何时停止从 Kafka 读取数据。例如，如果你希望流式应用从 Kafka 读取最新数据并继续处理新传入的数据，那么 `startingOffsets` 和 `endingOffsets` 的值都应为 `latest`。

#### 表 6-6 Kafka 数据源的可选选项

| 选项 | 默认值 | 值 | 描述 |
| --- | --- | --- | --- |
| `startingOffsets` | `latest` | `earliest`、`latest` 或每个主题起始偏移量的 JSON 字符串，例如 `{ "topic1": { "0":45, "1": -1}, "topic2": { "0":-2} }` | `earliest` 表示主题的开头。`latest` 表示主题中的最新数据。使用 JSON 字符串格式时，-2 表示特定分区中的最早偏移量，-1 表示特定分区中的最新偏移量。 |
| `endingOffsets` | `latest` | `latest` 或 JSON 字符串，例如 `{ "topic1": { "0":45, "1": -1}, "topic2": { "0":-2} }` | `latest` 表示主题中的最新数据。使用 JSON 字符串格式时，-1 表示特定分区中的最新偏移量。自然，-2 不适用于此选项。 |
| `maxOffsetsPerTrigger` | `none` | 长整型，例如 500 | 此选项是一种速率限制机制，用于控制每个触发间隔要处理的记录数。如果指定了值，它表示所有分区中的记录总数，而非每个分区。 |

默认情况下，Kafka 数据源不包含在 [`https://spark.apache.org/downloads.html`](https://spark.apache.org/downloads.html) 提供的 Apache Spark 二进制包中。如果你打算从 Spark shell 使用 Kafka 数据源，那么务必使用一个额外选项启动 Spark shell，以要求其下载并包含正确的 JAR 文件。Structured Streaming 和 Kafka 集成文档的部署部分（[`https://spark.apache.org/docs/latest/structured-streaming-kafka-integration.html`](https://spark.apache.org/docs/latest/structured-streaming-kafka-integration.html)）提供了关于该额外选项的信息。它类似于清单 6-18 所示。

```
./bin/spark-shell --packages org.apache.spark:spark-sql-kafka-0-10_2.11:2.3.0
// 如果未提供上述包，将遇到以下问题
java.lang.ClassNotFoundException: Failed to find data source: kafka. Please find packages at http://spark.apache.org/third-party-projects.html
at org.apache.spark.sql.execution.datasources.DataSource$.lookupDataSource(DataSource.scala:635)
at org.apache.spark.sql.streaming.DataStreamReader.load(DataStreamReader.scala:159)
```

#### 清单 6-18 使用 Kafka 数据源 JAR 文件启动 Spark Shell

让我们从一个简单示例开始，该示例从名为 `pageviews` 的 Kafka 主题的开头开始处理数据，并持续处理 Kafka 中到达的新数据。代码见清单 6-19。

```
import org.apache.spark.sql.functions._
val pvDF = spark.readStream.format("kafka")
.option("kafka.bootstrap.servers","localhost:9092")
.option("subscribe", "pageviews")
.option("startingOffsets", "earliest")
.load()
pvDF.printSchema
|-- key: binary (nullable = true)
|-- value: binary (nullable = true)
|-- topic: string (nullable = true)
|-- partition: integer (nullable = true)
|-- offset: long (nullable = true)
|-- timestamp: timestamp (nullable = true)
|-- timestampType: integer (nullable = true)
```

#### 清单 6-19 Kafka 数据源示例