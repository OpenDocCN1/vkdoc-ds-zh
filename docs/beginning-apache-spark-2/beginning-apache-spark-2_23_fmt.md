# 处理重复数据

数据去重是数据处理领域的常见需求，在批处理中实现起来并不困难。但在流处理中，由于流式数据的无界特性，这一任务更具挑战性。实时流式数据中的重复数据，通常是因为数据生产者在不稳定的网络连接下，为确保特定数据被发送和处理而多次发送同一数据所致。

幸运的是，Structured Streaming 让流式应用能够轻松实现数据去重，这些应用可以通过在数据到达时丢弃重复数据，从而保证精确一次处理。Structured Streaming 提供的数据去重功能可以配合水印使用，也可以不配合。但需要注意的关键点是，在不指定水印的情况下进行数据去重时，Structured Streaming 需要维护的状态会随着流式应用的生命周期无限增长，这可能导致内存溢出问题。使用水印后，早于水印的延迟数据会被自动丢弃，以避免任何重复的可能性。

Structured Streaming 执行数据去重的 API 非常简单，它只有一个输入参数，即用于唯一标识每一行的列名列表。这些列的值将用于执行重复检测，Structured Streaming 会将其作为中间状态存储。用于演示数据去重功能的示例数据与移动事件数据具有相同的模式。计数聚合将基于 `id` 列的分组进行。`id` 和 `ts` 列都用作去重的用户自定义键。此示例的数据位于 `<path>/chapter7/data/deduplication` 目录下，包含两个文件：`file1.json` 和 `file2.json`。这些文件的内容如代码清单 7-19 所示。

```
// file1.json - 每一行在 id 和 ts 列上都是唯一的
{"id":"phone1","action":"open","ts":"2018-03-02T10:15:33"}
{"id":"phone2","action":"open","ts":"2018-03-02T10:22:35"}
{"id":"phone3","action":"open","ts":"2018-03-02T10:23:50"}
// file2.json - 前两行与上面 file1.json 的前两行重复
// 第三行是唯一的
// 第四行是唯一的，但它是延迟到达的，因此不会被处理
{"id":"phone1","action":"open","ts":"2018-03-02T10:15:33"}
{"id":"phone2","action":"open","ts":"2018-03-02T10:22:35"}
{"id":"phone4","action":"open","ts":"2018-03-02T10:29:35"}
{"id":"phone5","action":"open","ts":"2018-03-02T10:01:35"}
```
代码清单 7-19
数据去重示例的样本数据

为了模拟数据去重，首先在 `<path>/chapter7/data` 目录下创建一个名为 `input` 的目录。然后运行代码清单 7-20 中的代码。下一步是将 `file1.json` 文件复制到 `input` 目录并检查输出。最后一步是将 `file2.json` 文件复制到 `input` 目录并检查输出。

```scala
import org.apache.spark.sql.types._
import org.apache.spark.sql.functions._
val mobileDataSchema = new StructType().add("id", StringType, false)
.add("action", StringType, false)
.add("ts", TimestampType, false)
// mobileDataSchema 在前一个示例中定义
val mobileDupSSDF = spark.readStream.schema(mobileDataSchema)
.json("/chapter7/data/deduplication")
val windowCountDupDF = mobileDupSSDF.withWatermark("ts", "10 minutes")
.dropDuplicates("id", "ts")
.groupBy("id").count
val mobileMemoryDupSQ = windowCountDupDF.writeStream
.format("console")
.option("truncate", "false")
.outputMode("update")
.start()
// 将 file1.json 复制到 input 目录后的输出
+-------+------+
|     id| count|
+-------+------+
| phone3|     1|
| phone1|     1|
| phone2|     1|
+-------+------+
// 将 file2.json 复制到 input 目录后的输出
+-------+------+
|     id| count|
+-------+------+
| phone4|     1|
+-------+------+
```
代码清单 7-20
使用 `dropDuplicates` API 进行数据去重

正如预期，将 `file2.json` 复制到 `input` 目录后，控制台只显示了一行。原因是前两行与 `file1.json` 的前两行重复，因此被过滤掉了。最后一行的时间戳为 10:10，由于该时间戳早于十分钟的水印阈值，因此被视为延迟数据，未被处理并被丢弃。

## 容错

在开发重要的流式应用并将其部署到生产环境时，最关键的考虑因素之一是故障恢复。根据墨菲定律，凡是可能出错的事就一定会出错。机器会故障，软件也会有缺陷。幸运的是，Structured Streaming 提供了一种在发生故障时重启或恢复流式应用的方法，并且应用会从上次中断的地方继续执行。要利用这种恢复机制，你需要在设置流式查询时指定检查点位置，从而为流式应用配置检查点和预写日志。理想情况下，检查点位置应该是 HDFS 或 S3 等可靠且容错的文件系统上的路径。Structured Streaming 会定期将所有进度信息（例如正在处理的数据的偏移量详情和中间状态值）保存到检查点位置。为流式查询添加检查点位置非常简单。你只需在流式查询中添加一个选项，将 `checkpointLocation` 作为名称，路径作为值即可。参见代码清单 7-21 中的示例。

```scala
val userSessionSQ = userSessionDS.writeStream.format("console")
.option("truncate",false)
.option("checkpointLocation","/reliable/location")
.outputMode("append")
.start()
```
代码清单 7-21
为流式查询添加 `checkpointLocation` 选项

如果你查看指定的检查点位置，应该会看到以下子目录：`commits`、`metadata`、`offsets`、`sources` 和 `stats`。这些目录中的信息特定于某个流式查询，因此每个查询必须使用不同的检查点位置。

与大多数软件应用一样，流式应用会随着时间推移而演变，原因可能是需要改进处理逻辑或性能，或是修复缺陷。重要的是要记住这可能会如何影响保存在检查点位置的信息，并了解哪些更改被认为是安全的。广义上讲，有两类更改：一类是流式应用代码的更改，另一类是 Spark 运行时的更改。

### 流式应用代码更改

检查点位置中的信息被设计为对流式应用的更改具有一定弹性。有几种类型的更改将被视为不兼容的更改。第一种是更改聚合方式，例如更改键列、添加更多键列或移除现有键列之一。第二种是更改用于存储中间状态的类结构，例如，当移除某个字段或将字段类型从字符串更改为整数时。如果在重启期间检测到不兼容的更改，Structured Streaming 会通过异常通知你。在这种情况下，你必须使用新的检查点位置，或者清除之前检查点位置中的内容。

### Spark 运行时变更

检查点格式被设计为向前兼容，以便流式应用程序能够在 Spark 的补丁版本或次要版本更新（例如，从 Spark 2.2.0 升级到 2.2.1，或从 Spark 2.2.*x* 升级到 2.3.*x*）后，从旧的检查点重新启动。唯一的例外是当存在关键的错误修复时。需要注意的是，当 Spark 引入不兼容的更改时，会在发布说明中明确记录。

如果由于不兼容问题而无法使用现有的检查点位置启动流式应用程序，那么您将需要使用新的检查点位置，并且可能还需要为您的应用程序提供一些关于从何处读取数据的偏移量信息。

## 流式查询指标与监控

与在线服务等其他长时间运行的应用程序类似，了解流式应用程序的进度、传入数据速率或中间状态消耗的内存量非常重要。Structured Streaming 提供了几个 API 来提取有关最近执行进度的信息，以及一种异步监控流式应用程序中所有流式查询的方法。

### 流式查询指标

在任何时刻，关于流式查询最基本有用的信息是其当前状态。您可以通过调用 `StreamingQuery.status` 函数来检索并以人类可读的格式显示此信息。返回的对象类型为 `StreamingQueryStatus`，它可以轻松地将状态信息转换为 JSON 格式。清单 7-22 展示了状态信息的一个示例。

```scala
// 使用上面示例中的流式查询
userSessionSQ.status
// 输出
res11: org.apache.spark.sql.streaming.StreamingQueryStatus =
{
"message" : "等待数据到达",
"isDataAvailable" : false,
"isTriggerActive" : false
}
```
清单 7-22
JSON 格式的查询状态信息

显然，上述状态提供了在调用状态函数时流式查询中正在发生的基本信息。要获取最近进度的更多详细信息，例如传入数据速率、处理速率、水印、数据源的偏移量以及有关中间状态的一些信息，您可以调用 `StreamingQuery.recentProgress` 函数。此函数返回一个 `StreamingQueryProgress` 类实例的数组，该类可以将详细信息转换为 JSON 格式。默认情况下，每个流式查询配置为保留 100 个进度更新，可以通过更新名为 `spark.sql.streaming.numRecentProgressUpdates` 的 Spark 配置来更改此数字。要查看最近的流式查询进度，可以调用 `StreamingQuery.lastProgress` 函数。清单 7-23 展示了一个流式查询进度的示例。

```json
{
"id" : "9ba6691d-7612-4906-b64d-9153544d81e9",
"runId" : "c6d79bee-a691-4d2f-9be2-c93f3a88eb0c",
"name" : null,
"timestamp" : "2018-04-23T17:20:12.023Z",
"batchId" : 0,
"numInputRows" : 3,
"inputRowsPerSecond" : 250.0,
"processedRowsPerSecond" : 1.728110599078341,
"durationMs" : {
"addBatch" : 1548,
"getBatch" : 8,
"getOffset" : 36,
"queryPlanning" : 110,
"triggerExecution" : 1736,
"walCommit" : 26
},
"eventTime" : {
"avg" : "2017-09-06T15:10:04.666Z",
"max" : "2017-09-06T15:11:10.000Z",
"min" : "2017-09-06T15:08:53.000Z",
"watermark" : "1970-01-01T00:00:00.000Z"
},
"stateOperators" : [ {
"numRowsTotal" : 1,
"numRowsUpdated" : 1,
"memoryUsedBytes" : 16127
} ],
"sources" : [ {
"description" : "FileStreamSource[file:/chapter7/data/input]",
"startOffset" : null,
"endOffset" : {
"logOffset" : 0
},
"numInputRows" : 3,
"inputRowsPerSecond" : 250.0,
"processedRowsPerSecond" : 1.728110599078341
} ],
"sink" : {
"description" : "org.apache.spark.sql.execution.streaming.ConsoleSinkProvider@37dc4031"
}
}
```
清单 7-23
流式查询进度详情

查看前面示例流式进度中的详细信息，有几个重要的关键指标需要注意。输入速率表示从输入源流入流式应用程序的传入数据量。处理速率告诉您流式应用程序处理传入数据的速度。在理想状态下，处理速率应高于输入速率，如果不是这种情况，则需要考虑扩展 Spark 集群中的节点数量。如果流式应用程序通过 `groupBy` 转换隐式地或通过任意状态处理 API 显式地维护状态，那么关注 `stateOperators` 部分中的指标就很重要。

Spark UI 在作业、阶段和任务级别提供了丰富的指标集。流式应用程序中的每个触发器都映射到 Spark UI 中的一个作业，可以轻松检查查询计划和任务持续时间。

### 注意

流式查询状态和进度详情可通过流式查询的实例获得。当您的流式应用程序在生产环境中运行时，您无法方便地访问这些流式查询。如果您想从远程主机查看这些信息怎么办？一种选择是在您的流式应用程序中嵌入一个小的 HTTP 服务器，并公开几个简单的 URL 来检索这些信息。

### 监控流式查询

Structured Streaming 提供了一种回调机制，用于异步接收流式应用程序中流式查询的事件和进度。这是通过 `StreamingQueryListener` 接口完成的，该接口会告知流式查询何时启动、何时取得进展以及何时终止。此接口的实现可以控制如何处理提供的信息。一个明显的实现是将此信息发送到 Kafka 主题或其他发布-订阅系统，用于离线分析或供另一个流式应用程序处理。清单 7-24 包含 `StreamingQueryListener` 接口的一个简单实现；它将信息打印到控制台。

```scala
import org.apache.spark.sql.streaming.StreamingQueryListener
import org.apache.spark.sql.streaming.StreamingQueryListener.{QueryStartedEvent, QueryProgressEvent, QueryTerminatedEvent}
class ConsoleStreamingQueryListener extends StreamingQueryListener {
override def onQueryStarted(event: QueryStartedEvent): Unit = {
println(s"流式查询已启动: ${event.id} - ${event.name} - ${event.runId}")
}
override def onQueryProgress(event: QueryProgressEvent): Unit = {
println(s"流式查询进度: ${event.progress}")
}
override def onQueryTerminated(event: QueryTerminatedEvent): Unit = {
println(s"流式查询已终止: ${event.id} - ${event.runId}")
}
}
```
清单 7-24
`StreamingQueryListener` 接口的简单实现

一旦您有了 `StreamingQueryListener` 的实现，下一步就是将其注册到 `StreamQueryManager`，它可以处理多个监听器。请参阅清单 7-25 了解如何注册和注销监听器。

```scala
val listener = new ConsoleStreamingQueryListener
// 注册
spark.streams.addListener(listener)
// 注销
spark.streams.removeListener(listener)
```
清单 7-25
使用 `StreamQueryManager` 注册和注销 `StreamingQueryListener` 实例

需要记住的一点是，每个监听器都会接收来自流式应用程序中所有流式查询的流式查询事件。如果需要对某个特定的流式查询应用特定的事件处理逻辑，则可以利用流式查询的名称。

## 摘要

Spark Structured Streaming 引擎提供了许多高级功能以及构建复杂精密流式应用程序的灵活性。

- 任何严肃的流式处理引擎都必须支持按事件时间处理传入数据的能力。Structured Streaming 不仅支持此功能，还支持基于固定窗口和滑动窗口的窗口聚合。此外，它还会以容错方式自动维护中间状态。
- 随着流式应用程序处理越来越多的数据，维护中间状态会带来内存耗尽的风险。引入水印机制是为了更容易地处理延迟数据，并移除不再需要的中间状态。
- 任意有状态处理支持用户自定义方式处理每个组的值并维护其中间状态。Structured Streaming 通过回调 API 提供了一种简单的方法来实现这一点，并且每个组可以灵活地向输出生成一行或多行。
- Structured Streaming 提供端到端的精确一次（exactly-once）保证。这是通过使用检查点和预写日志机制实现的。只需提供一个位于容错文件系统上的检查点位置，即可轻松启用这两种机制。流式应用程序可以通过读取保存在检查点位置的信息，轻松重启并从故障发生前的状态继续运行。
- 生产级流式应用程序需要能够洞察流式查询的状态和指标。Structured Streaming 提供流式查询状态的简短摘要，以及关于传入数据速率、处理速率以及中间状态内存消耗的一些详细指标。要监控所有流式查询的生命周期及其详细进度，您可以注册一个或多个 `StreamingQueryListener` 接口的实例。

# 8. 使用 Spark 进行机器学习

近年来，围绕人工智能（AI）、机器学习（ML）和深度学习（DL）的讨论异常热烈。AI 专家和研究人员预测，AI 将从根本上改变人类未来生活、工作和经商的方式。对于全球企业而言，AI 被视为其数字化转型旅程中的下一步，有些企业在将 AI 融入其业务战略方面走得更远。企业期望 AI 能够高效、快速地帮助解决业务问题，创造商业价值并提升竞争优势。谷歌、亚马逊、微软、苹果和 Facebook 等互联网巨头在投资、采用并将 AI 融入其产品组合方面处于领先地位。2017 年，全球超过 150 亿美元的风险投资（VC）流向了 AI 相关的初创公司，这一趋势预计将在 2018 年持续。

AI 是计算机科学的一个广阔领域，旨在让机器看起来拥有智能。这是一个旨在推动人类进步的雄心勃勃的目标。机器学习是 AI 的子领域之一，其重点在于教导计算机无需显式编程即可学习。学习过程涉及使用算法分析大量数据集，并构建一个模型来解释世界。这些算法可以根据其设计任务分为不同的类别。这些算法的一个共同点是，它们通过迭代优化内部参数的过程来学习，以达到最优结果。

深度学习（DL）是一种受人类大脑工作方式启发的机器学习方法，它已被证明非常擅长通过将数据表示为嵌套的概念层次结构来学习其中的复杂模式。结合大规模精选数据集的可用性以及图形处理单元（GPU）的进步，DL 已被证明在解决物体识别、图像识别、语音识别和机器翻译等领域的问题上非常有效。事实上，它在一个名为 ImageNet 的图像分类挑战中证明了自身实力，在该挑战中，使用 DL 方法训练的计算机系统在图像分类方面击败了人类。这一成就及其类似成就的意义在于，现在计算机系统能够像其创造者一样看、识别物体和听。图 8-1 展示了 AI、ML 和 DL 之间的关系及其时间线。

![图 8-1：AI、ML 和 DL 之间的关系及其时间线](img/419951_1_En_8_Fig1_HTML.jpg)

图 8-1：AI、ML 和 DL 之间的关系及其时间线

创建 Spark 的动机之一就是帮助应用程序高效地大规模运行迭代算法。在 Spark 的最近几个版本中，MLlib 库通过提供一组常用的 ML 算法和一套工具来促进 ML 模型的构建和评估过程，稳步增加了其功能，使 ML 变得可扩展且易于使用。

要理解 MLlib 库提供的功能，有必要对构建 ML 应用程序的过程有一个基本的了解。本章首先提供这些信息，然后介绍 MLlib 库中可用的功能和 API，以便您能够应用它们，使用机器学习构建智能应用程序。

## 机器学习概述

本节旨在简要介绍机器学习以及开发机器学习应用的典型流程。内容不求详尽；如果你已熟悉机器学习，可以跳过本节。

机器学习是一个广阔而迷人的研究领域，它融合了数学、统计学和计算机科学等其他学科的部分内容。它是一种教导计算机从历史数据中学习模式并获取洞察的方法，通常用于做出决策或预测。与传统硬编码的软件不同，机器学习仅根据你提供的不完美数据给出概率性输出。你提供给机器学习算法的数据越多，输出结果就越准确。机器学习能够解决比传统软件更有趣、更困难的问题，而且这些问题并不局限于任何特定行业或业务领域。相关应用示例包括图像识别、语音识别、语言翻译、欺诈检测、产品推荐、机器人技术、自动驾驶汽车、加速药物发现过程、医疗诊断、客户流失预测、推荐系统等等。

鉴于人工智能的目标是让机器看起来具有智能，衡量这一目标的最佳方法之一就是将机器智能与人类智能进行比较。近几十年来，有几次广为人知且被广泛报道的此类比较演示。第一次是名为 `Deep Blue` 的计算机系统，它在 1997 年严格的锦标赛规则下击败了国际象棋世界冠军。这个例子表明，在可能走法虽多但有限的游戏中，计算机可以比人类思考得更快、更好。第二次是名为 `Watson` 的计算机系统，它在 2011 年参加了《危险边缘》智力竞赛节目，与两位传奇冠军同台竞技，并赢得了 100 万美元的头奖。这个例子表明，计算机能够理解特定问答结构中的人类语言，然后利用其庞大的知识库得出概率性答案。第三次是关于名为 `AlphaGo` 的计算机程序，它在 2016 年一场历史性的对弈中击败了围棋世界冠军。这个例子展示了人工智能领域的巨大飞跃，因为围棋被认为是一种需要直觉、创造性和战略性思维的复杂棋盘游戏，并且由于其可能的走法数量比宇宙中的原子数还多，因此无法进行穷举搜索。

### 机器学习术语

在深入学习机器学习之前，了解机器学习语言中的几个基本术语非常重要。这将在后续章节提到这些术语时有所帮助。为了尽可能让这些术语更容易理解，解释将以经典的机器学习示例——垃圾邮件分类为例进行说明。

- **观测值**
  - 这个术语来自统计学领域。观测值是指用于学习的实体的一个实例。例如，电子邮件被视为观测值。

- **标签**
  - 用于标记观测值的值。例如，“垃圾邮件”或“非垃圾邮件”是用于标记电子邮件的两个可能值。

- **特征**
  - 这些是关于观测值的重要属性，很可能对预测输出有最强的影响。例如，邮件发送者的 IP 地址、单词数量、大写单词数量等等。

- **训练数据**
  - 这是观测值的一部分，用于训练选定的机器学习算法以生成模型。行业中的普遍做法是将收集到的数据分成三部分：训练数据、验证数据和测试数据。测试数据部分大约占原始数据集的 70%或 80%。

- **验证数据**
  - 这是观测值的一部分，用于在模型调优过程中评估机器学习模型的性能。

- **测试数据**
  - 这是观测值的一部分，用于在调优过程完成后评估机器学习模型的性能。

- **机器学习算法**
  - 这是一系列以迭代方式运行的步骤，用于从给定的测试数据中提取洞察或模式。机器学习算法的主要目标是学习从输入到输出的映射。目前已经有一套众所周知的机器学习算法可供选择。挑战在于选择正确的算法来解决特定的机器学习问题。对于电子邮件垃圾邮件检测问题，可能会选择朴素贝叶斯算法。

- **模型**
  - 当机器学习算法从给定的输入数据中学习后，它会生成一个模型，该模型用于对新数据进行预测或做出决策。模型由数学公式表示。目标是生成一个泛化模型，并在任何未见过的数据上表现良好。

机器学习算法、数据和模型之间的关系最好通过图 8-2 来说明。

![图 8-2：机器学习算法、数据和模型之间的关系](img/419951_1_En_8_Fig2_HTML.jpg)

图 8-2：机器学习算法、数据和模型之间的关系

应用机器学习时需要注意的一个重要点是，永远不要用测试数据来训练机器学习算法，因为这会违背生成泛化机器学习模型的目的。另一个需要注意的重要点是，机器学习是一个广阔的领域，随着你深入研究，无疑会发现更多术语和概念。希望这套基本的术语能帮助你开启学习机器学习的旅程。

### 机器学习类型

如前所述，机器学习是关于教导机器从以往数据中学习模式，以便做出决策或预测。这些任务广泛适用于许多不同类型的问题，每种问题类型都需要不同的学习方式。广义上讲，有三种学习类型，如图 8-3 所示。

![图 8-3：不同的机器学习类型](img/419951_1_En_8_Fig3_HTML.jpg)

图 8-3：不同的机器学习类型

#### 监督学习

在各种学习类型中，这一种应用广泛且更为流行，因为它有助于解决分类和回归领域中的大量问题。

分类是将观测值归入离散或分类标签中的某一类。分类问题的例子包括：预测一封电子邮件是否为垃圾邮件；一条产品评论是正面还是负面；一张图片中是否包含狗、猫、海豚或鸟；一篇新闻文章的主题是关于体育、医学、政治还是宗教；某个手写数字是 1 还是 2；以及第四季度的收入是否达到预期。当分类结果只有两个离散值时，称为*二分类*；当结果有两个以上离散值时，称为*多分类*。

回归是根据观测值预测实数值。与分类不同，预测值不是离散的，而是连续的。回归问题的例子包括：根据房屋的位置和面积预测房价；预测一家公司的股票价格；根据一组人的背景和教育程度预测其收入，等等。

这种学习类型与其他类型的一个关键区别在于，训练数据中的每个观测值都必须包含一个标签，无论该标签是离散的还是连续的。换句话说，算法会获得正确答案，从而通过迭代并逐步改进对训练数据的预测来进行学习，一旦达到可接受的误差范围，学习便会停止。

区分分类与回归的一个简单心智模型是：分类是将数据分入不同的桶中，而回归是为数据拟合最佳直线。该心智模型的可视化表示见图 8-4。

![图 8-4：分类与回归的心智模型](img/419951_1_En_8_Fig4_HTML.jpg)

图 8-4：分类与回归的心智模型

有大量算法专为解决分类和回归的机器学习问题而设计。本章仅关注 Spark MLlib 组件中支持的算法，如表 8-1 所列。

表 8-1：MLlib 中的监督学习算法

| 任务 | 算法 |
| --- | --- |
| 分类 | 逻辑回归决策树随机森林梯度提升树线性支持向量机朴素贝叶斯 |
| 回归 | 线性回归广义线性回归决策树回归随机森林回归梯度提升回归 |

#### 无监督学习

这种学习方法的名称意味着没有监督；换句话说，用于训练机器学习算法的数据不包含标签，由学习算法自行发现结果。这种学习类型旨在解决另一类问题，即发现数据中隐藏的结构或模式，而由我们人类来解释这些洞察背后的含义。事实证明，一种称为*聚类*的隐藏结构（这是数据分析中的一种探索性分析技术）是组织信息以推导有意义的关系或发现聚类内观测值相似性的好方法。图 8-5 展示了聚类的示例。

![图 8-5：聚类的可视化](img/419951_1_En_8_Fig5_HTML.jpg)

图 8-5：聚类的可视化

令人惊讶的是，许多实际问题都可以通过这种学习方法解决。假设有大量文档，且事先不知道某篇特定文档属于哪个主题；你可以使用无监督学习来发现相关文档的聚类，然后为每个聚类分配一个主题。无监督学习方法可以解决的另一个有趣且常见的问题是信用卡欺诈检测领域的问题，这是一种异常检测。将用户信用卡交易分组为聚类后，不难发现异常值，这些异常值可能代表信用卡被盗后出现的异常交易。

表 8-2 列出了无监督学习方法支持的算法。

表 8-2：MLlib 中的无监督学习算法

| 任务 | 算法 |
| --- | --- |
| 聚类 | K 均值潜在狄利克雷分配二分 K 均值高斯混合模型 |

#### 强化学习

与前两种学习类型不同，强化学习并非从数据中学习。相反，它通过与环境的交互（通过一系列动作）来学习，反馈循环提供信息，使其进行调整，以最大化某种奖励为目标。换句话说，它从自身经验中学习。

直到最近，这种学习类型受到的关注仍不如前两种，因为除了计算机游戏之外，它尚未取得显著的实践成功。2016 年，谷歌 DeepMind 成功地将这种学习类型应用于玩 Atari 游戏，随后又将其整合到 AlphaGo 程序中，该程序击败了一位围棋世界冠军。

目前，Spark MLlib 不包含任何强化学习算法。以下各节将重点介绍前两种学习类型。

### 注意

术语*监督*比喻性地指代一位教师（人类），他通过专门提供答案（标签）以及一组示例（训练数据）来“监督”学习者（即机器学习算法）。

### 机器学习流程

为了高效地将机器学习应用于智能应用的开发，你应当考虑学习并采纳大多数机器学习从业者遵循的一套最佳实践。人们常说，有效应用机器学习是一门手艺，半是科学，半是艺术。幸运的是，存在一个广为人知且结构化的流程，它由一系列步骤组成，有助于提供合理的可重复性和一致性，如图 8-6 所示。

![图 8-6：机器学习应用开发流程](img/419951_1_En_8_Fig6_HTML.jpg)

图 8-6：机器学习应用开发流程

显而易见，该流程的第一步是清晰理解你认为机器学习能助力的业务目标或挑战。评估机器学习的替代方案以了解其成本和权衡是很有益的。有时，先采用简单的基于规则的解决方案会更快。如果确信机器学习在无需人工干预的情况下，能够高效、快速且广泛地在多种场景中提供有价值的业务洞察，那么就可以进入下一步。在问题被清晰理解后，下一步是建立一套所有利益相关者都能认同的成功指标。

接下来的步骤是识别并收集必要类型和适当数量的数据，以支持当前的问题。所收集数据的质量和数量将直接影响训练出的机器学习模型的性能。需要牢记的一个重要点是，确保收集的数据尽可能代表你试图解决的问题。“垃圾进，垃圾出”这句话在描述机器学习的一个关键局限性时仍然非常适用。

特征工程是该流程中最重要且最耗时的步骤之一。这一步主要涉及数据清洗，并利用领域知识来识别关于观测数据的关键属性或特征，这些特征将有助于机器学习算法学习训练数据与提供的标签之间的直接关系。数据清洗任务通常使用探索性数据分析框架来完成，以便更好地理解数据在数据分布、相关性、异常值等方面的特征。特征工程是一个相当昂贵的步骤，因为它需要人工参与并运用其关于待解决问题的领域知识。深度学习已被证明是一种优于机器学习的卓越学习方法，因为它可以在无需人工干预的情况下自动提取特征。

特征工程之后的下一步是选择合适的机器学习模型或算法并进行训练。鉴于有许多可用的算法来解决类似的机器学习任务，问题在于，使用哪个或哪些模型最好？与大多数事情一样，决定最佳方案需要结合对当前问题的深入理解、对每种算法不同特性的良好工作知识，以及过去将其应用于类似问题的经验。换句话说，选择最佳算法是半科学、半艺术的过程。可能需要一些实验才能找到最佳算法。一旦选定了算法，就让它从特征工程步骤产生的数据中学习。预期的输出是一个模型，然后你继续进行模型评估，以了解其性能如何。前面所有步骤的目标是生成一个泛化能力强的模型，这意味着它在从未见过的数据上也能表现良好。

机器学习开发流程中的另一个重要步骤是模型评估任务。它既必要又具有挑战性。这一步的目标不仅是回答模型性能如何的问题，还要知道何时停止调整模型，因为其性能已达到第一步中设定的成功指标。评估过程可以离线进行，也可以在线进行。前者指使用训练数据评估模型，后者指使用实时或新数据评估模型。有一组常用的指标来了解模型性能，例如精确率、召回率、F1 分数、AUC 等。这一步的艺术在于理解哪些指标适用于特定的机器学习任务。模型性能的结果决定了是进入生产部署步骤，还是返回收集更多数据或不同类型数据的步骤。

这些信息旨在提供机器学习开发流程的概述，而非全面详尽的说明。要充分涵盖每个步骤的内部细节和最佳实践，很容易就需要整整一章的篇幅。

## Spark 机器学习库

本章的剩余部分将介绍 Spark MLlib 组件中的主要特性，并提供在 Spark 中将机器学习算法应用于以下机器学习任务的示例：分类、回归、聚类和推荐。

### 注意

在 Python 世界中，`scikit-learn` 是最受欢迎的开源机器学习库之一。它构建在 `NumPy`、`SciPy` 和 `matplotlib` 库之上，并提供了一套监督学习和无监督学习算法。它被设计成一个简单高效的工具；因此，它是在单机上学习和实践机器学习的完美工具。一旦数据大小超过单机的存储容量，就该切换到 Spark MLlib 了。

有许多机器学习库可供选择。在大数据时代，选择 Spark MLlib 而非其他选项有两个原因。第一个是易用性。Spark SQL 提供了一种用户友好的方式来执行数据探索性分析，而 MLlib 库则提供了一种构建、管理和持久化复杂机器学习流水线的方法。第二个原因是在大规模数据上执行机器学习。Spark 统一数据分析引擎与 MLlib 库的结合，可以支持训练具有数十亿观测数据和数千个特征的机器学习模型。

### 机器学习流水线

从前面的章节可以看出，机器学习流程本质上是一个流水线，由一系列按顺序运行的步骤组成，并且通常需要重复多次才能得到最优模型。为了与使实用机器学习变得简单的目标保持一致，Spark MLlib 提供了一套抽象，以帮助简化数据清洗、特征工程、模型训练、模型调优和评估等步骤，并将它们组织成流水线，使其易于理解、维护和重复。流水线的概念实际上源于前面提到的 `scikit-learn` 库。

形成端到端机器学习流水线有四个主要抽象：转换器、估计器、评估器和流水线。它们提供了一套标准接口，使理解他人的流水线变得容易。图 8-7 展示了机器学习流程中的核心步骤与 MLlib 提供的主要抽象之间的相似性。

![图 8-7：机器学习主要步骤与 MLlib 流水线主要概念之间的相似性](img/419951_1_En_8_Fig7_HTML.jpg)

图 8-7：机器学习主要步骤与 MLlib 流水线主要概念之间的相似性

这些抽象的一个共同点是，它们的输入和输出大多是 `DataFrame`，这意味着需要将输入数据转换为 `DataFrame` 才能使用这些抽象。

### 注意

与 Spark 统一数据分析引擎中的其他组件一样，MLlib 正在转向基于 DataFrame 的 API，以提供更友好的用户接口，并利用 Spark SQL 引擎提供的优化功能。新的 API 位于 `org.apache.spark.ml` 包中。第一个 MLlib 版本基于 RDD 的 API 开发，该版本目前仍受支持，但仅处于维护模式。旧的 API 位于 `org.apache.spark.mllib` 包中。一旦功能对等实现，基于 RDD 的 API 将被弃用。

#### 转换器

转换器旨在通过特征工程步骤和模型评估步骤中操作一个或多个列来转换 DataFrame 中的数据。转换过程是在构建特征以供给 ML 算法学习的背景下进行的。此过程通常涉及添加或删除列（特征）、将列值从文本转换为数值，或对特定列的值进行归一化。

在 MLlib 中使用 ML 算法有一个严格的要求：它们要求所有特征（包括标签）都必须是 `Double` 数据类型。

从技术角度来看，转换器有一个名为 `transform` 的函数，该函数对输入列执行转换，并将结果存储在输出列中。输入列和输出列的名称可以在构造转换器时指定。如果未指定，则使用默认列名（`"inputCol"`、`"outputCol"`）。图 8-8 描述了转换器的外观；DF1 中的阴影列代表输入列，DF2 中颜色较深的阴影列代表输出列。

![../images/419951_1_En_8_Chapter/419951_1_En_8_Fig8_HTML.jpg](img/419951_1_En_8_Fig8_HTML.jpg)

图 8-8

转换器的输入与输出

每种数据类型都有多种数据转换类型；因此，MLlib 中大约有 30 个转换器也就不足为奇了。表 8-3 展示了针对每种数据转换类型的各种转换器。

表 8-3

不同转换类型的转换器

| 类型 | 转换器 |
| --- | --- |
| 通用 | `SQLTransformer`、`VectorAssembler` |
| 数值数据 | `Bucketizer`、`QuantileDiscretizer`、`StandardScaler`、`MixMaxScaler`、`MaxAbsScaler`、`Normalizer` |
| 文本数据 | `IndexToString`、`OneHotEncoder`、`Tokenizer`、`RegexTokenizer`、`StopWordsRemover`、`NGram`、`HashingTF` |

以下部分将介绍几个常用的转换器。

`Binarizer` 转换器简单地将输入列的值转换为两组。第一组包含小于或等于指定阈值的值，输出列中的值为零。其他值在输出列中的值为 1。输入列必须是 `double` 或 `VectorUDT` 类型。清单 8-1 将温度列的值转换为两个桶。

```
import org.apache.spark.ml.feature.Binarizer
val arrival_data = spark.createDataFrame(Seq(("SFO", "B737", 18, 95.1, "late"),
("SEA", "A319", 5, 65.7, "ontime"),
("LAX", "B747", 15, 31.5, "late"),
("ATL", "A319", 14, 40.5, "late") ))
.toDF("origin", "model", "hour", "temperature", "arrival")
val binarizer = new Binarizer().setInputCol("temperature")
.setOutputCol("freezing")
.setThreshold(35.6)
binarizer.transform(arrival_data).show
// 显示 binarizer 转换器中参数的当前值
binarizer.explainParams
inputCol: 输入列名称 (当前值: temperature)
outputCol: 输出列名称 (默认值: binarizer_60430bb4e97f__output, 当前值: freezing)
threshold: 用于二值化连续特征的阈值 (默认值: 0.0, 当前值: 35.6)
// 显示转换结果
binarizer.transform(arrival_data).select("temperature", "freezing").show
+------------+---------+
| temperature| freezing|
+------------+---------+
|        95.1|      1.0|
|        65.7|      1.0|
|        31.5|      0.0|
|        40.5|      1.0|
+------------+---------+
清单 8-1
使用 Binarizer 转换器将温度转换为两个桶
```

`Bucketizer` 转换器是 `Binarizer` 的通用版本，它可以将列值转换为您选择的桶。控制桶的数量以及每个桶的值范围的方法是通过指定一个双精度值数组形式的桶边界列表。此转换器在列值为连续值，并且您希望将其转换为更易于理解的表示形式时非常有用。例如，您有一个包含特定州每位居民收入金额的列，并且您希望将他们的收入分桶为以下桶：高收入、中等收入、低收入等。

值桶边界数组必须是 `double` 类型，并且必须满足以下要求：

*   最小的桶边界值必须小于 DataFrame 中输入列的最小值。
*   最大的桶边界值必须大于 DataFrame 中输入列的最大值。
*   输入数组中必须至少有三个桶边界，这将创建两个桶。

对于个人收入的情况，很容易知道最小收入金额为 0；那么最小的桶边界值可以设置为小于 0 的值。如果无法预测列的最小值，则指定负无穷大。类似地，如果无法预测列的最大值，则指定正无穷大。请参见清单 8-2 了解使用此转换器将温度列分桶为三个桶的示例，这意味着桶边界数组必须至少包含四个值。输出按温度列排序，以便于查看。

```
import org.apache.spark.ml.feature.Bucketizer
val bucketBorders = Array(-1.0, 32.0, 70.0, 150.0)
val bucketer = new Bucketizer().setSplits(bucketBorders)
.setInputCol("temperature")
.setOutputCol("intensity")
val output = bucketer.transform(arrival_data)
output.select("temperature", "intensity")
.orderBy("temperature")
.show
+------------+----------+
| temperature| intensity|
+------------+----------+
|        31.5|       0.0|
|        40.5|       1.0|
|        65.7|       1.0|
|        95.1|       2.0|
+------------+----------+
清单 8-2
使用 Bucketizer 转换器将温度转换为三个桶
```

`OneHotEncoder` 转换器通常用于处理数值型分类值。如果分类值是字符串类型，则首先应用 `StringIndexer` 估计器将其转换为数值类型。`OneHotEncoder` 转换器本质上将数值型分类值映射为二进制向量，以有意消除数值型分类值的隐含排序。例如，以下数据表示学生专业，每个专业被分配一个序数值，这似乎暗示某个专业比其他专业更高。为了在 ML 训练步骤中消除这种无意的偏差，使用此转换器将序数值转换为向量。请参见清单 8-3 了解使用此转换器的示例。

```scala
import org.apache.spark.ml.feature.OneHotEncoder
val student_major_data = spark.createDataFrame(Seq(("John", "Math", 3),
("Mary", "Engineering", 2),
("Jeff", "Philosophy", 7),
("Jane", "Math", 3),
("Lyna", "Nursing", 4) ))
.toDF("user", "major", "majorIdx")
val oneHotEncoder = new OneHotEncoder().setInputCol("majorIdx")
.setOutputCol("majorVect")
oneHotEncoder.transform(student_major_data).show()
+----+------------+---------+---------------+
|user|       major| majorIdx|      majorVect|
+----+------------+---------+---------------+
|John|        Math|        3|  (7,[3],[1.0])|
|Mary| Engineering|        2|  (7,[2],[1.0])|
|Jeff|  Philosophy|        7|      (7,[],[])|
|Jane|        Math|        3|  (7,[3],[1.0])|
|Lyna|     Nursing|        4| ( 7,[4],[1.0])|
+----+------------+---------+---------------+
清单 8-3
使用 OneHotEncoder 转换器将分类值的序数值进行转换
```

处理字符串类型分类值时，另一个常见需求是将它们转换为序数值，这可以通过 `StringIndexer` 估计器来实现。该估计器将在“估计器”部分进行介绍。

在许多有趣的机器学习用例中，输入是自由格式的文本。这需要经过一些转换，才能将自由格式文本转换为机器学习算法可以处理的数值表示形式。其中就包括分词和词频统计。

你很可能已经猜到 `Tokenizer` 转换器的作用了。它对由空格分隔的单词字符串执行分词操作，并返回一个单词数组。如果需要使用不同的分隔符进行分词，则可以使用 `RegexTokenizer`。清单 8-4 展示了使用 `Tokenizer` 转换器的示例。

```scala
import org.apache.spark.ml.feature.Tokenizer
import org.apache.spark.sql.functions._
val text_data = spark.createDataFrame(Seq(
(1, "Spark is a unified data analytics engine"),
(2, "It is fun to work with Spark"),
(3, "There is a lot of exciting sessions at upcoming Spark summit"),
(4, "mllib transformer estimator evaluator and pipelines")  )
).toDF("id", "line")
val tokenizer = new Tokenizer().setInputCol("line").setOutputCol("words")
val tokenized = tokenizer.transform(text_data)
tokenized.select("words").withColumn("tokens", size(col("words"))).show(false)
+-------------------------------------------------------------------------+
|    words                                                        | tokens|
+-------------------------------------------------------------------------+
|[spark, is, a, unified, data, analytics, engine]                 |      7|
|[spark, is cool, and, it, is, fun, to, work, with,               |     11|
|[there, is, a, lot, of, exciting, sessions, at, upcoming, spark, summit]                                        |     11|
|[mllib, transformer, estimator, evaluator, and, pipelines]       |      6|
+-------------------------------------------------------------------------+
清单 8-4
使用 Tokenizer 转换器执行分词
```

停用词是一种语言中常用的词汇。在自然语言处理或机器学习的语境中，停用词往往只会增加不必要的噪声，而不会提供任何有意义的贡献。因此，通常在分词步骤之后立即进行停用词移除。`StopWordsRemover` 转换器正是为此步骤设计的。自 Spark 2.3 起，Spark 发行版中包含了以下语言的停用词供你使用：丹麦语、荷兰语、英语、芬兰语、法语、德语、匈牙利语、意大利语、挪威语、葡萄牙语、俄语、西班牙语、瑞典语和土耳其语。它的设计非常灵活，可以通过从提供的目录路径读取用户提供的停用词集来使用。要使用特定语言的停用词，首先调用 `StopWordsRemover.loadDefaultStopWords(<小写语言名称>)` 加载它们，然后将它们提供给 `StopWordsRemover` 的实例。此外，如有必要，你可以要求此转换器在停用词过滤时忽略大小写。清单 8-5 展示了使用 `StopWordsRemover` 转换器移除英语停用词的示例。

![../images/419951_1_En_8_Chapter/419951_1_En_8_Figa_HTML.jpg](img/419951_1_En_8_Figa_HTML.jpg)

```scala
import org.apache.spark.ml.feature.StopWordsRemover
val enStopWords = StopWordsRemover.loadDefaultStopWords("english")
val remover = new StopWordsRemover().setStopWords(enStopWords)
.setInputCol("words")
.setOutputCol("filtered")
// 使用清单 8-5 示例中的 tokenized 结果
val cleanedTokens = remover.transform(tokenized)
cleanedTokens.select("words","filtered").show(false)
清单 8-5
使用 StopWordsRemover 转换器从分词示例的单词中移除英语停用词
```

`HashingTF` 转换器用于通过计算每行中每个单词的频率，将单词转换为数值表示。每个单词通过应用名为 `MurmurHash3` 的哈希函数映射到一个索引。这种方法效率很高，但存在潜在的哈希冲突问题，即多个单词可能映射到同一个索引。最小化冲突的一种方法是指定一个较大的桶数量（最好是 2 的幂），以帮助均匀分布单词。清单 8-6 中的示例将把清单 8-6 示例中的 `filtered` 列输入到 `HashingTF` 转换器中。

![../images/419951_1_En_8_Chapter/419951_1_En_8_Figb_HTML.jpg](img/419951_1_En_8_Figb_HTML.jpg)

```scala
import org.apache.spark.ml.feature.HashingTF
val tf = new HashingTF().setInputCol("filtered")
.setOutputCol("TFOut")
.setNumFeatures(4096)
val tfResult = tf.transform(cleanedTokens)
tfResult.select("filtered", "TFOut").show(false)
清单 8-6
使用 HashingTF 转换器通过哈希和计数将单词转换为数值表示

```

本节介绍的最后一个转换器是`VectorAssembler`，它简单地将一组列合并成一个向量列。在机器学习术语中，这相当于将各个特征组合成单一向量特征，供机器学习算法学习。各个输入列的类型必须是以下类型之一：数值型、布尔型或向量型。输出向量列包含所有列的值，顺序与指定顺序一致。该转换器几乎用于每一个机器学习流水线中，其输出将传递给一个估计器。清单 8-7 展示了使用`VectorAssembler`转换器的示例。

```scala
import org.apache.spark.ml.feature.VectorAssembler
val arrival_features  = spark.createDataFrame(Seq(
(18, 95.1, true),
(5, 65.7, true), (15, 31.5, false),
(14, 40.5, false) ))
.toDF("hour", "temperature", "on_time")
val assembler = new VectorAssembler().setInputCols(Array("hour", "temperature", "on_time"))
.setOutputCol("features")
val output = assembler.transform(arrival_features)
output.show
+-----+------------+--------+----------------+
| hour| temperature| on_time|        features|
+-----+------------+--------+----------------+
|   18|        95.1|    true| [18.0,95.1,1.0]|
|    5|        65.7|    true|  [5.0,65.7,1.0]|
|   15|        31.5|   false| [15.0,31.5,0.0]|
|   14|        40.5|   false| [14.0,40.5,0.0]|
+-----+------------+--------+----------------+
清单 8-7
使用 VectorAssembler 转换器将特征组合成向量特征
```

了解转换器的工作原理以及 MLlib 中可用的转换器，在机器学习开发流程的特征工程步骤中扮演着重要角色。通常，`VectorAssembler`转换器的输出将由一个估计器（estimator）消费，这将在下一节中介绍。

## 估计器

下一个概念是估计器，它是对在数据上进行训练的机器学习算法或任何其他在数据上操作的算法的抽象。令人困惑的是，一个估计器可以是两种算法中的一种。第一种类型的例子是名为`LinearRegression`的机器学习算法，用于回归任务，例如预测房价。第二种算法的例子是`StringIndexer`，它将一列的分类值编码为索引，使得每个分类值的索引值基于它在 DataFrame 整个输入列中出现的频率。从高层次来看，这种估计器将一列的值转换为另一列；然而，它需要对整个 DataFrame 进行两次遍历才能产生预期的输出。

从技术角度来看，估计器有一个名为`fit`的函数，该函数将算法应用于输入列，结果封装在名为`Model`的对象类型中，而`Model`是一种`Transformer`类型。输入列和输出列的名称可以在构造估计器时指定。图 8-9 描述了估计器的外观及其输入和输出。

![../images/419951_1_En_8_Chapter/419951_1_En_8_Fig9_HTML.jpg](img/419951_1_En_8_Fig9_HTML.jpg)

图 8-9

估计器及其输入和输出

为了了解这两种类型的估计器，表 8-4 列出了 MLlib 中可用估计器的一个子集。

表 8-4

MLlib 中可用的估计器示例

| 类型 | 估计器 |
| --- | --- |
| 机器学习算法 | `LogisticRegression` `DecisionTreeClassifier` `RandomForestClassifier` `LinearRegression` `RandomForestRegressor` `KMeans` `LDA` `BisectingKMeans` |
| 数据转换算法 | `IDF` `RFormula` `StringIndexer` `OneHotEncoderEstimator` `StandardScaler` `MixMaxScaler` `MaxAbsScaler` `Word2Vec` |

以下部分提供了在处理文本和数值数据时常用估计器的一些示例。

`RFormula`是一个有趣且通用的估计器，其转换逻辑以声明方式表达。它可以处理数值和分类值，其输出是一个特征向量。MLlib 从 R 语言中借鉴了这个估计器的思想，目前仅支持 R 中可用运算符的一个子集。基本且受支持的运算符列在表 8-5 中。需要花一点时间来理解这种转换语言，才能充分利用`RFormula`估计器的灵活性和强大功能。

表 8-5

`RFormula`转换器中支持的运算符

| 运算符 | 描述 |
| --- | --- |
| `~` | 分隔目标与项。 |
| `+` | 连接项。 |
| `-` | 移除一个项。 |
| `:` | 其他项之间的交互以创建新特征。对于数值类型将使用乘法，对于分类值则进行二值化。 |
| `.` | 除目标之外的所有列。 |

以下示例指定标签为到达列，并使用所有剩余列作为特征。此外，它还利用`hour`和`temperature`列之间的交互创建了一个新特征。由于这两列是数值类型，它们的值将被相乘。清单 8-8 包含了前面所述示例的代码。

![../images/419951_1_En_8_Chapter/419951_1_En_8_Figc_HTML.jpg](img/419951_1_En_8_Figc_HTML.jpg)

```scala
import org.apache.spark.ml.feature.RFormula
val arrival_data = spark.createDataFrame(Seq(("SFO", "B737", 18, 95.1, "late"),
("SEA", "A319", 5, 65.7, "ontime"),
("LAX", "B747", 15, 31.5, "late"),
("ATL", "A319", 14, 40.5, "late") ))
.toDF("origin", "model", "hour", "temperature", "arrival")
val formula = new RFormula().setFormula("arrival ~ . + hour:temperature")
.setFeaturesCol("features")
.setLabelCol("label")
// 先调用 fit 函数，返回一个模型（转换器类型），再调用 transform
val output = formula.fit(arrival_data).transform(arrival_data)
output.select("*").show(false)
```

清单 8-8 使用`RFormula`转换器创建特征向量

处理文本时常用的估计器之一是`IDF`估计器。其名称是逆文档频率（inverse document frequency）的缩写。该估计器通常在文本完成分词并计算词频后使用。其背后的思想是通过统计每个词出现在多少个文档中来计算其重要性或权重。其直觉是，出现频率高且范围广的词重要性较低，例如单词the。反之，出现频率高但仅出现在少数文档中的词则表明其重要性更高，例如单词classification。在 DataFrame 的上下文中，文档指的是每一行。细心的读者会发现，要计算每个词的重要性，需要遍历每一行，因此`IDF`是一个估计器，而不是转换器。清单 8-9 中的示例将`Tokenizer`和`HashingTF`转换器与`IDF`估计器串联起来。估计器的`fit`函数是一个急切求值函数，会触发作业执行。

```scala
import org.apache.spark.ml.feature.Tokenizer
import org.apache.spark.ml.feature.HashingTF
import org.apache.spark.ml.feature.IDF
val text_data = spark.createDataFrame(Seq(
(1, "Spark is a unified data analytics engine"),
(2, "Spark is cool and it is fun to work with Spark"),
(3, "There is a lot of exciting sessions at upcoming Spark summit"),
(4, "mllib transformer estimator evaluator and pipelines")  )
).toDF("id", "line")
val tokenizer = new Tokenizer().setInputCol("line")
.setOutputCol("words")
// Tokenizer 转换器的输出列是 HashingTF 的输入
val tf = new HashingTF().setInputCol("words")
.setOutputCol("wordFreqVect")
.setNumFeatures(4096)
val tfResult = tf.transform(tokenizer.transform(text_data))
// HashingTF 转换器的输出是 IDF 估计器的输入
val idf = new IDF().setInputCol("wordFreqVect")
.setOutputCol("features")
// 由于 IDF 是估计器，调用 fit 函数
val idfModel = idf.fit(tfResult)
// 返回的对象是一个模型，属于 Transformer 类型
val weightedWords = idfModel.transform(tfResult)
weightedWords.select("label", "features").show(false)
weightedWords.printSchema
|-- id: integer (nullable = false)
|-- line: string (nullable = true)
|-- words: array (nullable = true)
|    |-- element: string (containsNull = true)
|-- wordFreqVect: vector (nullable = true)
|-- features: vector (nullable = true)
// 特征列包含每个词权重的向量，由于内容较长，以下未包含输出
weightedWords.select("wordFreqVect", "features").show(false)
```

清单 8-9 使用`IDF`估计器计算每个词的权重

处理包含分类值的文本数据时，一个值得了解的优秀估计器是`StringIndexer`估计器。它根据分类值的频率将其编码为索引，使得最频繁的分类值获得索引值 0，依此类推。为了让该估计器为分类值生成索引值，它首先必须统计每个分类值的频率，最后再分配索引值；换句话说，它必须看到 DataFrame 中输入列的所有值。如果输入列是数值型的，该估计器会在计算频率之前将其转换为字符串类型。清单 8-10 提供了一个使用`StringIndexer`估计器对电影类型进行编码的示例。

```scala
import org.apache.spark.ml.feature.StringIndexer
val movie_data = spark.createDataFrame(Seq(
(1, "Comedy"),
(2, "Action"),
(3, "Comedy"),
(4, "Horror"),
(5, "Action"),
(6, "Comedy")  )
).toDF("id", "genre")
val movieIndexer = new StringIndexer().setInputCol("genre")
.setOutputCol("genreIdx")
// 首先拟合数据
val movieIndexModel = movieIndexer.fit(movie_data)
// 使用返回的转换器转换数据
val indexedMovie = movieIndexModel.transform(movie_data)
indexedMovie.orderBy("genreIdx").show()
+---+-------+---------+
| id|  genre| genreIdx|
+---+-------+---------+
|  3| Comedy|      0.0|
|  6| Comedy|      0.0|
|  1| Comedy|      0.0|
|  5| Action|      1.0|
|  2| Action|      1.0|
|  4| Horror|      2.0|
+---+-------+---------+
```

清单 8-10 使用`StringIndexer`估计器对电影类型进行编码

如上所示，该估计器根据频率降序分配索引。这种默认行为可以轻松更改为频率升序；事实上，它还支持另外两种排序类型：字母降序和字母升序。要更改默认排序类型，只需调用`setStringOrderType("<排序类型>")`函数，并传入以下值之一：`frequencyDesc`、`frequencyAsc`、`alphabetDesc`、`alphabetAsc`。

另一个处理分类值的有用估计器是`OneHotEncoderEstimator`，它将分类值的索引编码为二进制向量。从 Spark 2.3.0 开始，`OneHotEncoder`转换器已被弃用，因为它无状态，导致无法用于新测试数据（其类别数量可能与训练数据不同）。该估计器通常与`StringIndexer`估计器结合使用，其中`StringIndexer`的输出成为该估计器的输入。清单 8-11 演示了如何一起使用`StringIndexer`和`OneHotEncoderEstimator`。

```scala
import org.apache.spark.ml.feature.OneHotEncoderEstimator
// 输入列 genreIdx 是清单 8-9 中 StringIndex 的输出列
val oneHotEncoderEst = new OneHotEncoderEstimator().setInputCols(Array("genreIdx"))
.setOutputCols(Array("genreIdxVector"))
// 拟合清单 8-10 中生成的 indexedMovie 数据
val oneHotEncoderModel = oneHotEncoderEst.fit(indexedMovie)
val oneHotEncoderVect = oneHotEncoderModel.transform(indexedMovie)
oneHotEncoderVect .orderBy("genre").show()
+---+-------+---------+---------------+
| id| genre | genreIdx| genreIdxVector|
+---+-------+---------+---------------+
|  5| Action|      1.0|  (2,[1],[1.0])|
|  2| Action|      1.0|  (2,[1],[1.0])|
|  3| Comedy|      2.0|      (2,[],[])|
|  6| Comedy|      2.0|      (2,[],[])|
|  1| Comedy|      2.0|      (2,[],[])|
|  4| Horror|      0.0|  (2,[0],[1.0])|
+---+--------+--------+---------------+
```

清单 8-11 `OneHotEncoderEstimator`消费`StringIndexer`估计器的输出

## Word2Vec 估计器

在处理自由文本时，另一个值得了解的估计器是`Word2Vec`估计器，其名称意为词转向量。该估计器利用一种名为词嵌入的著名技术，将词元转换为数值向量表示，使得语义相似的词被映射到邻近的点。这项技术背后的直觉是，相似的词往往共同出现并具有相似的上下文。换句话说，两个具有相似相邻词的不同词，其含义很可能相似或相关。该技术已被证明在诸多自然语言处理应用中相当有效，例如词语类比、词语相似度、实体识别和机器翻译。

`Word2Vec` 估计器有几个重要的配置项，需要提供适当的值来控制基于输入的输出。表 8-6 描述了这些配置。

**表 8-6** `Word2Vec` 配置项

| 名称 | 默认值 | 描述 |
| --- | --- | --- |
| `vectorSize` | 100 | 输出向量的大小。 |
| `windowSize` | 5 | 用作上下文的词的数量。 |
| `minCount` | 5 | 一个词元必须出现的最小次数，才能被包含在输出中。 |
| `maxSentenceLength` | 1000 | 指定与其他项交互以创建新特征。数值型将使用乘法，类别型将使用二值化。 |

清单 8-12 中的示例演示了如何使用 `Word2Vec` 估计器，并展示了如何查找相似词。

![../images/419951_1_En_8_Chapter/419951_1_En_8_Figd_HTML.jpg](img/419951_1_En_8_Figd_HTML.jpg)

```scala
import org.apache.spark.ml.feature.Word2Vec

val documentDF = spark.createDataFrame(Seq(
  "Unified data analytics engine Spark".split(" "),
  "People use Hive for data analytics".split(" "),
  "MapReduce is not fading away".split(" ")
).map(Tuple1.apply)).toDF("word")

val word2Vec = new Word2Vec().setInputCol("word")
  .setOutputCol("feature") .setVectorSize(3).setMinCount(0)

val model = word2Vec.fit(documentDF)
val result = model.transform(documentDF)
result.show(false)
```

**清单 8-12** 使用 `Word2Vec` 估计器计算词嵌入并查找相似词

```scala
// 查找与 Spark 相似的词，结果显示 Hive 和 MapReduce 都相似。
model.findSynonyms("Spark", 3).show
+----------+-------------------+
|      word|         similarity|
+----------+-------------------+
|    engine| 0.9133241772651672|
| MapReduce| 0.7623026967048645|
|      Hive| 0.7179173827171326|
+----------+-------------------+

// 查找与 Hive 相似的词，结果显示 Spark 是相似的
model.findSynonyms("Hive", 3).show
+-------+--------------------+
|   word|          similarity|
+-------+--------------------+
|  Spark|  0.7179174423217773|
| fading|  0.5859972238540649|
| engine| 0.43200281262397766|
+-------+--------------------+
```

# 数值数据的归一化与标准化

接下来的估计器是关于数值数据的归一化和标准化。使用这些估计器的原因是确保使用距离作为度量的学习算法不会对具有较大值的特征赋予比具有较小值的特征更多的权重。

数值数据归一化是将其原始范围映射到零到一之间的过程。当观测值具有多个不同范围的属性时，这尤其有用。例如，假设你有员工的薪水及其身高。薪水的数值是数千，而身高的数值是个位数。这就是 `MinMaxScaler` 估计器的设计目的。该估计器使用列摘要统计信息，将每个特征（列）单独线性重新缩放到一个共同的、由最小值和最大值指定的值范围。如果最小值是 0.0，最大值是 3.0，那么所有值都将落在这个范围内。清单 8-13 提供了一个使用 `MinMaxScaler` 处理包含薪水和身高信息的 `employee_data` 数据集的示例。这两个特征值之间的幅度差异很大，但经过 `MinMaxScaler` 处理后，情况就不再如此了。

```scala
import org.apache.spark.ml.feature.MinMaxScaler
import org.apache.spark.ml.linalg.Vectors

val employee_data = spark.createDataFrame(Seq(
  (1, Vectors.dense(125400, 5.3)),
  (2, Vectors.dense(179100, 6.9)),
  (3, Vectors.dense(154770, 5.2)),
  (4, Vectors.dense(199650, 4.11))))
  .toDF("empId", "features")

val minMaxScaler = new MinMaxScaler().setMin(0.0)
  .setMax(5.0)
  .setInputCol("features")
  .setOutputCol("scaledFeatures")

val scalerModel = minMaxScaler.fit(employee_data)
val scaledData = scalerModel.transform(employee_data)

println(s"Features scaled to range: [${minMaxScaler.getMin}, ${minMaxScaler.getMax}]")
// Features scaled to range: [0.0, 5.0]
scaledData.select("features", "scaledFeatures").show(false)
+----------------+----------------------------------------+
|        features|                          scaledFeatures|
+----------------+----------------------------------------+
|  [125400.0,5.3]|                [0.0,2.1326164874551963]|
|  [179100.0,6.9]|                 [3.616161616161616,5.0]|
|  [154770.0,5.2]| [1.9777777777777779,1.9534050179211468]|
| [199650.0,4.11]|                               [5.0,0.0]|
+----------------+----------------------------------------+
```

**清单 8-13** 使用 `MinMaxScaler` 重新缩放特征

除了数值数据归一化之外，另一种常用于处理数值数据的操作称为*标准化*。当数值数据的分布接近钟形曲线时，此操作尤其适用。标准化操作有助于将数据转换为归一化形式，使数据落在 -1 到 1 的范围内，均值为 0。这样做的原因是，当数据围绕均值 0 有良好分布时，有助于某些机器学习算法更好地工作。`StandardScaler` 估计器专为标准化操作而设计。清单 8-14 中的示例使用了与清单 8-13 相同的输入数据集。输出显示特征值现在以 0 为中心，标准差为 1 个单位。

```scala
import org.apache.spark.ml.feature.StandardScaler
import org.apache.spark.ml.linalg.Vectors

val employee_data = spark.createDataFrame(Seq(
  (1, Vectors.dense(125400, 5.3)),
  (2, Vectors.dense(179100, 6.9)),
  (3, Vectors.dense(154770, 5.2)),
  (4, Vectors.dense(199650, 4.11))))
  .toDF("empId", "features")

// 设置单位标准差为 true，并以均值为中心
val standardScaler = new StandardScaler().setWithStd(true)
  .setWithMean(true)
  .setInputCol("features")
  .setOutputCol("scaledFeatures")

val standardMode = standardScaler.fit(employee_data)
val standardData = standardMode.transform(employee_data)
standardData.show(false)
+------+----------------+-------------------------------------------+
| empId|        features|                             scaledFeatures|
+------+----------------+-------------------------------------------+
|     1|  [125400.0,5.3]| [-1.2290717420781212,-0.06743742573177587]|
|     2|  [179100.0,6.9]|    [0.4490658767775897,1.3248191055048935]|
|     3|  [154770.0,5.2]| [-0.3112523404805006,-0.15445345893406737]|
|     4| [199650.0,4.11]|     [1.091258205781032,-1.102928220839048]|
+------+----------------+-------------------------------------------+
```

**清单 8-14** 使用 `StandardScaler` 将特征标准化到均值为零

MLlib 中还有更多的估计器可用于执行多种数据转换和映射，它们都遵循一个标准的抽象：拟合输入数据并生成一个模型实例。前面的示例旨在说明如何使用这些估计器。第二类估计器（涉及机器学习算法）的示例将在后续章节中介绍。

### 管道

在机器学习中，通常需要按顺序执行一系列步骤来清洗和转换数据，然后训练一个或多个机器学习算法从数据中学习，最后调整模型以达到最佳性能。MLlib 中的管道抽象旨在使这一工作流程更易于开发和维护。从技术角度来看，MLlib 有一个名为 `Pipeline` 的类，它被设计用于管理一系列阶段，每个阶段由 `PipelineStage` 表示。一个 `PipelineStage` 可以是转换器或估计器。`Pipeline` 抽象本身是一种估计器。

设置管道的第一个步骤是创建一系列阶段，然后创建一个 `Pipeline` 类的实例，并用一个阶段数组对其进行配置。`Pipeline` 将按指定顺序运行这些阶段。如果某个阶段是转换器，则会调用 `transform()` 函数。如果某个阶段是估计器，则会调用 `fit()` 函数来生成一个转换器，然后再调用其 `transform()` 函数。让我们通过一个小型工作流程来演示如何使用前面章节介绍的转换器和估计器处理文本。图 8-10 中描绘的小型管道由两个转换器和一个估计器组成。当调用 `Pipeline.fit()` 函数时，包含原始文本的输入 DataFrame 将被传入 `Tokenizer` 转换器，其输出将被传入 `HashingTF` 转换器，该转换器将单词转换为特征。`Pipeline` 识别出 `LogisticRegression` 是一个估计器，因此它将使用计算出的特征调用 `fit` 函数，以生成一个 `LogisticRegressionModel`。

图 8-10 中描绘的 `Pipeline` 的代码见清单 8-15。请记住，`Pipeline` 抽象是一种估计器。因此，一旦创建并配置了 `Pipeline` 的实例，就必须以训练数据作为输入调用 `fit()` 函数来触发各阶段的执行，这将生成一个 `PipelineModel` 实例，它是一种转换器。此时，你可以使用测试数据调用 `transform()` 函数来执行预测。

MLlib 提供了一项名为 *ML 持久化* 的功能，它可以轻松地将管道或模型持久化到磁盘，并在之后加载使用。很酷的一点是，持久化功能被设计为以语言无关的格式保存信息，因此，在 Scala 中持久化的管道或模型可以在 Java 或 Python 中读回，反之亦然。

实际的生产管道包含许多阶段。当阶段数量增多时，理解流程会变得困难，维护也颇具挑战。MLlib 的管道抽象确实能在这些方面提供帮助。另一个需要重点注意的关键点是，`Pipeline` 和 `PipelineModel` 对象都被设计为有助于确保训练数据和测试数据流经相同的特征处理步骤。

![../images/419951_1_En_8_Chapter/419951_1_En_8_Fig10_HTML.jpg](img/419951_1_En_8_Fig10_HTML.jpg)

图 8-10 一个小型管道的示例

```scala
import org.apache.spark.ml.{Pipeline, PipelineModel}
import org.apache.spark.ml.classification.LogisticRegression
import org.apache.spark.ml.feature.{HashingTF, Tokenizer}

val text_data = spark.createDataFrame(Seq(
  (1, "Spark is a unified data analytics engine", 0.0),
  (2, "Spark is cool and it is fun to work with Spark", 0.0),
  (3, "There is a lot of exciting sessions at upcoming Spark summit", 0.0),
  (4, "signup to win a million dollars", 0.0)  )
).toDF("id", "line", "label")

val tokenizer = new Tokenizer().setInputCol("line").setOutputCol("words")
val hashingTF = new HashingTF().setInputCol(tokenizer.getOutputCol)
  .setOutputCol("features")
  .setNumFeatures(4096)
val logisticReg = new LogisticRegression().setMaxIter(5)
  .setRegParam(0.01)

val pipeline = new Pipeline().setStages(Array(tokenizer, hashingTF, logisticReg))
val logisticRegModel = pipeline.fit(text_data)

// 持久化模型和管道
logisticRegModel.write.overwrite().save("/tmp/logistic-regression-model")
pipeline.write.overwrite().save("/tmp/logistic-regression-pipeline")

// 加载模型和管道
val prevModel = PipelineModel.load("/tmp/spark-logistic-regression-model")
val prevPipeline = Pipeline.load("/tmp/logistic-regression-pipeline")
```

**清单 8-15** 使用管道创建一个小型工作流程

# 模型调优

模型调优步骤的目标是训练一个具有正确参数集的模型，以实现最佳性能，从而满足机器学习开发流程第一步中定义的目标。这一步通常繁琐、重复且耗时，因为它可能涉及尝试不同的机器学习算法或几组参数。本节旨在介绍`MLlib`提供的几种工具，以帮助完成模型调优步骤中繁重的工作。本节并非旨在展示如何执行模型调优。

在深入探讨`MLlib`提供的两种工具的细节之前，让我们先明确理解以下术语，其中一个是模型调优过程的输入。

- 模型超参数是
  - 用于控制机器学习算法训练过程的配置
  - 模型外部且无法从训练数据中学习到的配置
  - 由机器学习从业者在训练过程开始前提供的配置
  - 通过迭代方式为给定机器学习任务调整的配置
- 模型参数是
  - 并非由机器学习从业者提供的属性
  - 在训练过程中从训练数据中学习到的属性
  - 在训练过程中会被优化的属性
  - 用于执行预测的模型属性

模型超参数的示例包括 K-means 聚类算法中的聚类数量、逻辑回归算法中应用的正则化量或学习率。

模型参数的示例包括线性回归模型中的系数或决策树模型中的分支位置。

`MLlib`中用于帮助模型调优的两个常用类是`CrossValidator`和`TrainValidationSplit`，它们都属于`Estimator`类型。这些类也被称为验证器。它们都需要以下输入才能正常工作：

- 第一个输入是指定需要调优的内容，可以是机器学习算法或`Pipeline`的实例。换句话说，它必须是`Estimator`类型。
- 第二个输入是一组用于调优所提供估计器的参数。这些参数也被称为参数网格，用于搜索以找到最佳模型。有一个名为`ParagramGridBuilder`的便捷工具可用于构建参数网格。
- 最后一个输入是一个评估器，用于根据保留的测试数据评估模型的性能。对于每种不同的机器学习任务，`MLlib`都提供了特定的评估器，它可以生成一个或多个评估指标，供你了解模型性能。支持常用的机器学习指标，例如均方根误差、精确率、召回率和准确率。

从高层次来看，上述验证器将使用给定的输入执行以下步骤：

1. 包含特征的数据根据指定的比例被分割为训练集和测试集。
2. 对于每一对训练集和测试集，对每一对应用以下步骤。
   - 对于“参数网格”中的每种组合，使用训练数据和该参数组合来拟合给定的估计器。然后，由指定的评估器根据测试数据对输出模型进行评估。记录性能指标并进行比较。
3. 返回产生最佳性能的模型及其使用的参数集。

前面的步骤在图 8-11 中进行了说明，这使得更容易可视化验证器内部发生的事情。

![../images/419951_1_En_8_Chapter/419951_1_En_8_Fig11_HTML.jpg](img/419951_1_En_8_Fig11_HTML.jpg)

图 8-11 验证器内部

我要讨论的第一个验证器是`TrainValidationSplit`，它根据指定的比例将给定的输入数据分割成一对训练集和验证集，然后针对每个参数组合训练和评估这对数据集。例如，如果给定的参数集有六种组合，那么给定的估计器将被训练和评估六次，每次使用不同的参数组合。清单 8-16 提供了一个使用`TrainValidationSplit`来调优线性回归估计器的示例，其参数网格包含六种参数组合。由于此示例的重点是`TrainValidationSplit`，因此假设输入数据的特征工程已经完成，并且它有一个名为`features`的列。

```scala
import org.apache.spark.ml.{Pipeline, PipelineModel}
import org.apache.spark.ml.classification.LogisticRegression
import org.apache.spark.ml.feature.{HashingTF, Tokenizer}
import org.apache.spark.ml.tuning.{ParamGridBuilder, TrainValidationSplit}
import org.apache.spark.ml.evaluation.BinaryClassificationEvaluator
val text_data = spark.createDataFrame(Seq(
(1, "Spark is a unified data analytics engine", 0.0),
(2, "Spark is cool and it is fun to work with Spark", 0.0),
(3, "There is a lot of exciting sessions at upcoming Spark summit", 0.0),
(4, "signup to win a million dollars", 0.0)  )
).toDF("id", "line", "label")
val tokenizer = new Tokenizer().setInputCol("line").setOutputCol("words")
val hashingTF = new HashingTF().setInputCol(tokenizer.getOutputCol)
.setOutputCol("features")
val logisticReg = new LogisticRegression().setMaxIter(5)
val pipeline = new Pipeline().setStages(Array(tokenizer, hashingTF, logisticReg))
// 第一个参数有 3 个值，第二个参数有 2 个值，
// 因此总参数组合数为 6
val paramGrid = new ParamGridBuilder().addGrid(hashingTF.numFeatures, Array(10, 100, 250))
.addGrid(logisticReg.regParam, Array(0.1, 0.05))
.build()
// 使用所需输入设置验证器 - 估计器、评估器、参数网格和训练比例
val trainValidationSplit = new TrainValidationSplit().setEstimator(pipeline)
.setEvaluator(new BinaryClassificationEvaluator)
.setEstimatorParamMaps(paramGrid)
.setTrainRatio(0.8)
// 训练线性回归估计器
val model = trainValidationSplit.fit(training)
```

清单 8-16 `TrainValidationSplit`示例

我要讨论的下一个验证器是`CrossValidator`，它是机器学习从业者社区中广泛使用的一种技术的实现，用于帮助模型调优步骤。该技术通过将观测值随机分成大致相等的 k 组（或称折）来最大化用于训练和验证的数据量。第一折用于验证目的，其余折用于训练目的。此过程重复 k 次，每次针对随机划分的训练折和验证折对估计器进行训练和评估。图 8-12 说明了此过程。选择 k 值时要确保每个训练组和验证组在统计上能代表可用的观测值，并且每折的样本数据量大致相同。

![../images/419951_1_En_8_Chapter/419951_1_En_8_Fig12_HTML.jpg](img/419951_1_En_8_Fig12_HTML.jpg)

图 8-12 k=4 的 K 折示例

## 关于使用验证器的注意事项

使用此验证器处理大量参数组合时，必须注意其高昂的计算成本。这是因为图 8-12 中描述的每个实验都会针对每个参数组合执行。例如，如果`k`为 4，参数组合数量为 6，则估计器将被训练和评估的总次数为 24。清单 8-17 将 8-16 中的`TrainValidationSplit`替换为配置了`k`值为 2 的`CrossValidator`实例。在实践中，`k`的值通常为 10 或更高。清单 8-17 中的示例最终对估计器进行了 12 次训练和评估。

```scala
import org.apache.spark.ml.tuning.CrossValidator
val crossValidator = new CrossValidator().setEstimator(pipeline)
.setEvaluator(new BinaryClassificationEvaluator)
.setEstimatorParamMaps(paramGrid)
.setNumFolds(2)
val model = crossValidator.fit(text_data)
```

清单 8-17 `CrossValidator`示例

## 机器学习任务实战

本节尝试将`MLlib`提供的所有概念和工具整合在一起，并通过以下机器学习任务进行实践：分类、回归和推荐。通过使用真实数据集完成机器学习开发过程，理想情况下，各部分如何协同工作将变得更加清晰，并且看到可运行的代码总是有益的。

本节并非旨在全面涵盖每个机器学习算法的超参数，模型调优步骤将作为练习留给您。

### 分类

分类是研究最广泛、使用最多的机器学习任务之一，因为它能够帮助解决许多现实生活中的分类相关问题。例如：这是一笔欺诈性信用卡交易吗？这封邮件是垃圾邮件吗？这张图片是猫、狗还是鸟？

分类有三种类型。

*   **二分类**：待预测的标签只有两个可能的类别（例如，欺诈或非欺诈、会议论文被接受或不被接受、肿瘤是良性还是恶性）。
*   **多分类**：待预测的标签有两个以上可能的类别（例如，图像是狗、猫还是鸟）。
*   **多标签分类**：每个观测值可以属于多个类别。电影类型就是一个很好的例子。一部电影可以同时被归类为动作片和喜剧片。`MLlib`原生不支持这种分类类型。

`MLlib`为分类任务提供了几种机器学习算法。它们列举如下：

*   逻辑回归
*   决策树
*   随机森林
*   梯度提升树
*   线性支持向量机
*   一对多
*   朴素贝叶斯

#### 模型超参数

以下示例将使用逻辑回归算法，以下是其模型超参数的一个子集。每个模型超参数都有一个默认值。

*   `family`：可能的值为`auto`、`binomial`和`multinomial`。默认值为`auto`，这意味着算法将根据`label`列中的值自动选择`binomial`或`multinomial`。`binomial`用于二分类。`multinomial`用于多分类。
*   `regParam`：这是用于控制过拟合的正则化参数。默认值为 0.0。

#### 示例

以下示例尝试预测泰坦尼克号事故中哪些乘客幸存。这是一个二分类机器学习问题，作为起点，选择了逻辑回归算法。此示例基于 kaggle.com 上的一个竞赛，信息和数据可在[https://www.kaggle.com/c/titanic](https://www.kaggle.com/c/titanic)获取。数据以 CSV 格式提供，包含两个文件：`train.csv`和`test.csv`。`train.csv`文件包含`label`列。

提供的数据包含许多有趣的特征；然而，清单 8-18 中的代码将仅使用`age`、`gender`和`ticket_class`作为特征。

```scala
import org.apache.spark.ml.Pipeline
import org.apache.spark.ml.feature.StringIndexer
import org.apache.spark.ml.feature.VectorAssembler
import org.apache.spark.ml.classification.LogisticRegression
import org.apache.spark.ml.evaluation.BinaryClassificationEvaluator
val titanic_data = spark.read.format("csv").option("header", "true")
.option("inferSchema","true")
.load("//train.csv")
// explore the schema
titanic_data.printSchema
|-- PassengerId: integer (nullable = true)
|-- Survived: integer (nullable = true)
|-- Pclass: integer (nullable = true)
|-- Name: string (nullable = true)
|-- Sex: string (nullable = true)
|-- Age: double (nullable = true)
|-- SibSp: integer (nullable = true)
|-- Parch: integer (nullable = true)
|-- Ticket: string (nullable = true)
|-- Fare: double (nullable = true)
|-- Cabin: string (nullable = true)
|-- Embarked: string (nullable = true)
// to start out with, we will use only three features
// filter out rows where age is null
val titanic_data1 = titanic_data.select('Survived.as("label"), 'Pclass.as("ticket_class"),
'Sex.as("gender"), 'Age.as("age"))
.filter('age.isNotNull)
// split the data into training and test with 80% and 20% split
val Array(training, test) = titanic_data1.randomSplit(Array(0.8, 0.2))
println(s"training count: ${training.count}, test count: ${test.count}")
// estimator:  to convert gender string to numbers
val genderIndxr = new StringIndexer().setInputCol("gender").setOutputCol("genderIdx")
// transfomer: assemble the features into a vector
val assembler = new VectorAssembler().setInputCols(Array("ticket_class", "genderIdx", "age"))
.setOutputCol("features")
// estimator: the algorithm
val logisticRegression = new LogisticRegression().setFamily("binomial")
// set up the pipeline with three stages
val pipeline = new Pipeline().setStages(Array(genderIndxr, assembler, logisticRegression))
// train the algorithm with the training data
val model = pipeline.fit(traininng)
// perform the predictions
val predictions = model.transform(test)
// perform the evaluation of the model performance, the default metric is the area under the ROC
val evaluator = new BinaryClassificationEvaluator()
evaluator.evaluate(predictions)
res10: Double = 0.8746657754010692
evaluator.getMetricName
res11: String = areaUnderROC
```

清单 8-18 使用逻辑回归算法预测泰坦尼克号乘客的生存情况

`BinaryClassificationEvaluator`产生的指标值为 0.87，这对于仅使用三个特征来说是不错的性能。前面的示例没有探索各种超参数和训练参数。

### 回归

另一个流行的机器学习任务称为**回归**，它旨在预测一个实数或连续值。例如，您想预测下一季度的销售收入、某个群体的收入金额或世界某个地区的降雨量。

`MLlib`为回归任务提供了几种机器学习算法。它们列举如下：

*   线性回归
*   广义线性回归
*   决策树
*   随机森林
*   梯度提升树
*   保序回归

#### 模型超参数

以下示例将使用`LinearRegression`算法，其模型超参数的一个子集如下所示：

*   `regParam`：这是用于控制过拟合的正则化参数。默认值为 0.0。
*   `fitIntercept`：此参数用于确定是否拟合截距。默认值为`true`。

#### 示例

以下示例将尝试根据一组房屋信息来预测房价。详细信息和数据可在 kaggle.com 的[https://www.kaggle.com/c/house-prices-advanced-regression-techniques/data](https://www.kaggle.com/c/house-prices-advanced-regression-techniques/data)上获取。数据以 CSV 格式提供，包含两个文件：`train.csv`和`test.csv`。`train.csv`文件中的标签列名为`SalePrice`。

提供的数据包含许多有趣的特征；然而，清单 8-19 中的代码将仅使用其中的一部分。



```scala
import org.apache.spark.sql.functions._
import org.apache.spark.ml.Pipeline
import org.apache.spark.ml.feature.StringIndexer
import org.apache.spark.ml.feature.VectorAssembler
import org.apache.spark.ml.regression.LinearRegression
import org.apache.spark.ml.feature.RFormula
import org.apache.spark.ml.evaluation.RegressionEvaluator
import org.apache.spark.mllib.evaluation.RegressionMetrics

val house_data = spark.read.format("csv").option("header", "true")
    .option("inferSchema","true")
    .load("/train.csv")

// 选择用作特征的列
val cols = SeqString
val colNames = cols.map(n => col(n))

// 仅选择需要的列
val skinny_house_data = house_data.select(colNames:_*)

// 通过将 "1stFlrSF" 和 "2ndFlrSF" 列的值相加，创建一个名为 "TotalSF" 的新列
// 将 "SalePrice" 列转换为 double 类型
val skinny_house_data1 = skinny_house_data.withColumn("TotalSF", col("1stFlrSF") +
    col("2ndFlrSF"))
    .drop("1stFlrSF", "2ndFlrSF")
    .withColumn("SalePrice", $"SalePrice".cast("double"))

// 检查名为 "SalePrice" 的标签列的统计信息
skinny_house_data1.describe("SalePrice").show
// +--------+-------------------+
// | summary|          SalePrice|
// +--------+-------------------+
// |   count|               1460|
// |    mean| 180921.19589041095|
// |  stddev|  79442.50288288663|
// |     min|            34900.0|
// |     max|           755000.0|
// +--------+-------------------+

// 创建估计器和转换器以设置管道
// 将无效分类值的处理策略设置为跳过，以避免在评估时出错
val roofStyleIndxr = new StringIndexer().setInputCol("RoofStyle")
    .setOutputCol("RoofStyleIdx")
    .setHandleInvalid("skip")
val heatingIndxr = new StringIndexer().setInputCol("Heating")
    .setOutputCol("HeatingIdx")
    .setHandleInvalid("skip")
val linearRegression = new LinearRegression().setLabelCol("SalePrice")

// 组装器，用于将特征组装成特征向量
val assembler = new VectorAssembler().setInputCols(
    Array("LotArea", "RoofStyleIdx", "HeatingIdx",
    "LotArea", "BedroomAbvGr", "KitchenAbvGr", "GarageCars",
    "TotRmsAbvGrd", "YearBuilt", "TotalSF"))
    .setOutputCol("features")

// 设置管道
val pipeline = new Pipeline().setStages(Array(roofStyleIndxr, heatingIndxr, assembler, linearRegression))

// 将数据拆分为训练集和测试集
val Array(training, test) = skinny_house_data1.randomSplit(Array(0.8, 0.2))

// 训练管道
val model = pipeline.fit(training)

// 执行预测
val predictions = model.transform(test)
val evaluator = new RegressionEvaluator().setLabelCol("SalePrice")
    .setPredictionCol("prediction")
    .setMetricName("rmse")
val rmse = evaluator.evaluate(predictions)
// rmse: Double = 37579.253919082395
```

清单 8-19
使用线性回归算法预测房价

`RMSE` 代表均方根误差。在此例中，`RMSE` 值约为 37,000 美元，这表明还有很大的改进空间。

### 推荐

推荐系统是最直观、最著名的机器学习应用之一。这大概是因为几乎每个人都曾在亚马逊和 Netflix 等热门网站上看到过推荐系统的实际应用。事实上，互联网上几乎每一个热门网站都有一个或多个推荐系统的实例。常见的推荐系统例子包括 Spotify 上你可能喜欢的歌曲、Twitter 上你可能想关注的人、Coursera 或 Udacity 上你可能喜欢的课程等等。很酷的一点是，推荐系统对用户和网站背后的公司都有好处。用户无需花费太多精力就能愉快地找到或发现他们喜欢的物品。公司则会因为用户参与度和忠诚度的提高以及利润的增长而感到满意。如果一个推荐系统设计得当且性能良好，那将是一个双赢的局面。

构建推荐系统的常见方法包括基于内容的过滤、协同过滤以及两者的混合。第一种方法需要收集被推荐物品的信息以及每个用户的画像。第二种方法则需要通过显式或隐式方式仅收集用户的活动或行为。显式行为的例子包括在亚马逊上对电影或物品进行评分。隐式行为的例子包括观看电影预告片或描述。第二种方法背后的直觉是“群体智慧”的概念，即过去意见一致的人在未来也倾向于意见一致。

本节将重点介绍协同过滤方法，其中一种流行的算法叫做 `ALS`，它代表交替最小二乘法。该算法所需的唯一输入是用户-物品评分矩阵，通过一个称为矩阵分解的过程来发现用户偏好和物品属性。一旦找到这两部分信息，就可以用来预测用户对未曾见过的物品的偏好。MLlib 提供了 `ALS` 算法的实现。

#### 模型超参数

MLlib 中实现的 `ALS` 算法有几个你需要了解的重要超参数。以下部分仅包含其中一部分。请查阅 [https://spark.apache.org/docs/latest/ml-collaborative-filtering.html](https://spark.apache.org/docs/latest/ml-collaborative-filtering.html) 上的文档。

*   `rank`：此参数指定在训练过程中将要学习的关于用户和物品的潜在因子或属性的数量。`rank` 的最优值通常通过实验以及对准确描述一个物品所需属性数量的直觉来确定。默认值为 10。
*   `regParam`：这是用于处理过拟合的正则化量。此参数的最优值通常通过实验确定。默认值为 0.1。
*   `implicitPrefs`：`ALS` 算法同时支持显式和隐式的用户活动或行为。此参数用于指示输入数据代表哪一种。默认值为 false，表示活动或行为是显式的。

#### 示例

你将通过一个示例来构建电影推荐系统，该示例使用来自 `grouplens.com` 的电影评分数据集，数据地址为 [https://grouplens.org/datasets/movielens/](https://grouplens.org/datasets/movielens/)。具体使用的数据集是最新的 MovieLens 100k 数据集，下载地址为 [http://files.grouplens.org/datasets/movielens/ml-latest-small.zip](http://files.grouplens.org/datasets/movielens/ml-latest-small.zip)。该数据集包含约 700 名用户对 9000 部电影的大约 100,000 条评分。压缩包中包含四个文件：`links.csv`、`movies.csv`、`ratings.csv` 和 `tags.csv`。文件 `ratings.csv` 中的每一行代表一位用户对一部电影的一条评分，格式如下：`userId`、`movieId`、`rating`、`timestamp`。评分范围为 0 到 5，以半星为增量。

清单 8-20 中的代码使用一组参数训练 ALS 算法，然后基于 RMSE 指标评估模型性能。此外，它还会调用 `ALSModel` 类中一些有趣的 API 来获取针对电影和用户的推荐。

```scala
import org.apache.spark.mllib.evaluation.RankingMetrics
import org.apache.spark.ml.evaluation.RegressionEvaluator
import org.apache.spark.ml.recommendation.ALS
import org.apache.spark.ml.tuning.{ParamGridBuilder, CrossValidator}
import org.apache.spark.sql.functions._

// 我们不需要时间戳列，因此立即删除它
val ratingsDF = spark.read.option("header", "true")
    .option("inferSchema", "true")
    .csv("/ratings.csv").drop("timestamp")

// 快速检查评分数量
ratingsDF.count
// res14: Long = 100004

// 快速查看哪些是活跃的电影评分者
val ratingsByUserDF = ratingsDF.groupBy("userId").count()
ratingsByUserDF.orderBy($"count".desc).show(10)
// +-------+------+
// | userId| count|
// +-------+------+
// |    547|  2391|
// |    564|  1868|
// |    624|  1735|
// |     15|  1700|
// |     73|  1610|
// |    452|  1340|
// |    468|  1291|
// |    380|  1063|
// |    311|  1019|
// |     30|  1011|
// +-------+------+

println("# of rated movies: " + ratingsDF.select("movieId").distinct().count)
```

