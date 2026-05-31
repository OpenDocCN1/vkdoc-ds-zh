# `mv mysql-connector-java-5.1.36-bin.jar /usr/lib/sqoop/lib`

之后，你就可以使用该连接器进行导入/导出操作。例如，以下命令将从 MySQL 数据库服务器导入一个名为 `MyTbl` 的表到 HDFS 目录 `MyData`：

```
$ sqoop import \
--connect jdbc:mysql://localhost/userdb \
--username root \
--table MyTbl \
--m 1 \
--target-dir /MyData
```

现在，上面的命令将从 MySQL 获取整个表的数据。但是，如果你只需要最近一天的数据呢？或者最近一周的？Sqoop 支持利用数据表中任意列的值进行增量数据加载。因此，如果 `MyTbl` 表有一个包含时间戳或最后修改日期的日期列（`modified_date`），你可以用它来获取最近一天的数据，如下所示（假设你在 2016 年 2 月 29 日进行增量加载，并希望加载 2016 年 2 月 28 日的数据）：

```
$ sqoop import \
--connect jdbc:mysql://localhost/userdb \
--username Myusr \
--password Mypwd \
--table MyTbl \
--m 1 \
--target-dir /MyData/incremental_table \
--check-column modified_date \
--incremental lastmodified \
--last-value 2016-01-27
```

但是，请注意，上面的命令将获取 `MyTbl` 表中自 2016 年 2 月 28 日 00:01 起所有新增（或修改）的行。因此，如果你的应用程序允许对现有记录进行更新或修改，此命令也会为你获取更新后的行。如果你只需要新添加的行呢？

Sqoop 提供了另一种增量加载模式，称为 `append`（而不是 `lastmodified`），它只会为你获取（某张表的）新追加的记录。显然，你不能在此模式下使用时间戳或最后修改日期。你需要使用一个具有递增值的 ID 列，并且需要知道上一次增量加载加载的最大值。例如，如果 `MyTbl` 有一个保存自增 ID 的 `MyId` 列，并且你确定上一次增量加载加载的最大 ID 值为 9834，那么以下命令将获取在该次加载之后添加的所有记录：

```
$ sqoop import \
--connect jdbc:mysql://localhost/userdb \
--username Myusr \
--password Mypwd \
--table MyTbl \
--m 1 \
--target-dir /MyData/incremental_table \
--check-column modified_date \
--incremental append \
--last-value 9834
```

概括来说，Sqoop 支持两种类型的增量导入：`append` 和 `lastmodified`，可以与 `--incremental` 参数结合使用，以指定你需要执行的增量导入。如果你要导入某张表的新行，且新行的行 ID 值是递增的，你可以使用 `append` 模式。然后，你可以使用 `--check-column` 选项指定行 ID 列，并导入检查列的值大于 `--last-value` 指定值的行。

或者，如果你的表没有行 ID 列，但包含一个最后修改列，你可以使用 `lastmodified` 模式。在这种情况下，你可以将最后修改列与 `--check-column` 选项配合使用，并导入检查列的值大于 `--last-value` 指定的时间戳（或日期）的行。

在生产环境中，你需要创建一个脚本，该脚本首先确定上一次增量加载的 `--last-value`，然后将该值替换到你的 Sqoop 命令中，以获取所需的记录。然后，你可以将此脚本安排为增量加载的任务。

关于可与 Sqoop 一起使用的命令行选项，请参阅 Sqoop 用户手册：
[`sqoop.apache.org/docs/1.4.6/SqoopUserGuide.html#_incremental_imports`](https://sqoop.apache.org/docs/1.4.6/SqoopUserGuide.html#_incremental_imports)

最后，我将提及另一种增量加载数据的方法。你可以利用查询参数，使用 SQL select 语句将导入范围限制为仅包含新增或更改的记录，如下所示：

```
$ sqoop import \
--connect jdbc:mysql://localhost/userdb \
--username Myusr \
--password Mypwd \
--table MyTbl \
--m 1 \
--target-dir /MyData/incremental_table \
--query 'select * from MyTbl where modified_date > 2016-01-27'
```

你可以根据需要构建查询，以匹配增量加载需求或源数据表的结构。使用查询选项的好处是，你可以更好地控制需要导入的数据，并且可以指定多个条件来更有效地过滤数据。

### 使用 Flume 进行数据传输

Flume（作为一种数据传输机制）的优势在于其处理流式数据和整合来自多个来源数据的能力。这就是 Flume 被用于事件和审计日志整合的原因。此外，Flume 与基于 Log4j 的日志记录配合良好，这种日志记录在 Hadoop 安装中非常流行。在排查作业问题或调查安全漏洞时，浏览各种 Hadoop 日志可能很困难，并且容易引入手动错误。定义一个 Flume 代理（及相关配置）可能需要一些初始时间，但你只需操作一次。后续对代理的修改会更简单快捷，最重要的是，整合（针对某个问题的）日志的过程会变得更容易、更快速。

在某些情况下，日志数据量可能非常庞大，而一个定义良好的 Flume 代理可以在无需人工干预的情况下过滤必要的数据。Flume 可靠、可扩展且易于定制。Flume 还支持动态配置和上下文路由。

### Flume 架构

Flume 架构围绕 *client*（客户端）生成 *event*（事件，由 Flume 传输的原子数据单元）以及 *agent*（代理）对其进行处理而展开。以下是关键术语的定义：

- **Client（客户端）：** 产生事件并将其提供给一个或多个代理进行处理的实体。
- **Event（事件）：** Flume 从起点（源）传输到终点（目标）的原子数据单元。事件是一个带有（或不带）头部信息的字节数组。
- **Source(s)（数据源）：** 事件数据接收器。数据源接收由事件（或从通道）生成的数据，并将其输出到一个或多个通道。例如，`Syslog` 可以用作数据源。
- **Channel（通道）：** 临时数据管道。通道是事件从数据源接收后被输出到目标之前的一个临时存储区域。内存 (RAM) 或文件都可以用作通道。
- **Sink（目标）：** 数据目的地。目标用作事件数据的目的地。最流行的目标之一是 HDFS，即日志或其他事件以文件形式存储在 HDFS 中。
- **Interceptor（拦截器）：** 根据需要检查、转换或过滤事件。例如，时间戳拦截器为事件添加时间戳头部，或者 `regex_filter` 拦截器根据指定的正则表达式过滤掉事件。
- **Channel selector（通道选择器）：** 根据头部信息将事件写入一个或多个已配置通道的过程。一个事件可以被 *replicated*（复制，写入所有通道）或 *multiplexed*（多路复用，根据端口等头部信息定向到不同通道）。例如，在端口 12345 上接收的事件进入 `channel1`，在端口 54321 上接收的事件进入 `channel2`，依此类推。
- **Sink processor（目标处理器）：** 根据（选定的）负载均衡或故障转移策略，将事件写入（来自已定义的目标组的）目标的过程。
- **Agent（代理）：** 一个进程或 Java 虚拟机 (JVM)，它利用拦截器（用于过滤事件）或通道（用于临时存储）等其他组件运行数据流，以将事件从数据源传输到目标。Flume 根据需要部署为一个或多个代理。

图 8-6 展示了这些 Flume 组件在整体架构中的位置。下一节将简要讨论这些 Flume 组件。

```
Agent (代理)
Client (客户端)
Event (事件)
replicated (复制) or
multiplexed (多路复用)
Channel (通道)
to channel(s) (到通道)
Source (数据源)
Interceptor (拦截器)
Selector (选择器)
Generates (生成)
events (事件)
Source (数据源)
Transforms (转换) or
Channel1
Channel2
Channel3
accepts (接受)
filters out (过滤掉)
event (事件)
Event written (事件写入)
based on load
balancing (基于负载均衡)
or failover strategy (或故障转移策略)
Sink (目标)
Processor (处理器)
To next
channel if
required (如有必要，转到下一通道)
Sink1
Sink2
Sink3
```

**图 8-6.** Flume 主要组件架构

### 理解并使用 Flume 组件

上一节回顾了主要 Flume 组件的定义。我们还了解了每个组件在整体 Flume 架构中的位置。现在是时候详细讨论其中两个最重要的组件了。

### 数据源 (Source)

*数据源*是 Flume 中接收客户端事件数据并将其放置到一个或多个通道（记住，数据源需要至少一个通道才能运行）的组件。流行的数据源包括 `Syslog`、`Netcat`、`Exec` 等。

`Exec` 数据源在 Flume 外部运行一个命令，并将输出转换为 Flume 事件。要使用 `Exec` 数据源，我将定义一个数据源 `s1`，并在 Flume 代理配置文件（名为 `flume.conf`，位于 `/etc/flume/conf`）中设置其所有相关属性。

我将从 `type` 属性开始，并将其设置为 `exec`：

```
s_agent.sources=s1
s_agent.sources.s1.type=exec
```

Flume 中的所有数据源都需要使用 `channels`（复数）属性指定要写入事件的通道列表。这里我们将使用单个通道：

```
s_agent.sources.s1.channels=c1
```

唯一的另一个必需参数是 `command` 属性，它告诉 Flume 要向操作系统传递什么命令。例如，我们可以使用以下命令来打印 HDFS 审计日志中新增的行：

```
s_agent.sources.s1.command= tail -f hdfs-audit.log
```

请注意，Linux `tail` 命令的 `-f`（或 follow）选项会在文件增长时追加数据。默认情况下，输入文件（此处为 `hdfs-audit.log`）每隔一秒检查一次是否有增长，但你可以根据需要更改该时间间隔。你还可以为 `Exec` 数据源定义其他属性，有关不同 Flume 数据源的属性，请参阅：
[`flume.apache.org/FlumeUserGuide.html#flume-sources`](https://flume.apache.org/FlumeUserGuide.html#flume-sources)

总而言之，这就是我们如何为名为 `s_agent` 的代理配置单个数据源 `s1`。该数据源是一个 `exec` 类型的数据源，它将从 HDFS 审计日志文件 `hdfs-audit.log` 中提取作业的相关行。所有事件将被写入 `c1` 通道。

### 目标 (Sink)

*目标*是 Flume 中从通道取出事件并将其传输到下一个目的地（可能是另一个数据源）的组件。这种传输是事务性的，也就是说，代理使用事务交换来保证跨目的地的可靠交付——除非数据传输到下一个目的地成功，否则数据不会从通道中移除。目标需要恰好一个通道才能运行。以下是不同类型的目标：

- 终端目标：将事件存入其最终目的地（例如，HDFS、HBase）
- 自消费目标（例如，Null 目标）
- 用于代理间通信的 IPC 目标（例如，Avro、Thrift）

如果你的需求无法通过这些目标中的任何一个来满足，你可以通过扩展 `org.apache.flume.sink.AbstractSink` 类来编写自定义目标。

HDFS 是与 Flume 一起使用的流行目标。HDFS 目标可以用于在 HDFS 中打开一个文件，将流式数据写入其中，并在数据流结束或停止后关闭它。

要使用 HDFS 目标，你需要在命名目标上将 `type` 参数设置为 `hdfs`：

```
s_agent.sinks.sink1.type=hdfs
```

这为名为 `s_agent` 的代理定义了一个名为 `sink1` 的 HDFS 目标。你需要指定一些额外的必需参数，首先是要写入数据的 HDFS 路径：

```
s_agent.sinks.sink1.hdfs.path=/usr/flume/mydata
```

我使用了不包含服务器名称的绝对路径，但你可以根据需要包含服务器名称的绝对路径（`hdfs://namenode/usr/flume/mydata`）或相对路径（`mydata`）。HDFS 目标（或任何目标）的最后一个强制参数（`channel`）指定了它将从中读取数据的通道名称：

```
s_agent.sinks.sink1.channel=ch1
```

这告诉 `sink1` 目标从 `ch1` 通道读取事件。有关目标属性的完整列表，请参阅
[`flume.apache.org/FlumeUserGuide.html#flume-sinks`](https://flume.apache.org/FlumeUserGuide.html#flume-sinks)

### 使用 Flume 实现日志整合

在本节前面，我讨论了 Flume 架构，并简要介绍了数据源和目标。这将帮助你理解如何使用 Flume 进行数据传输。

为了加深理解并说明如何使用 Flume 执行聚合和整合，我现在将介绍一个简短的工作示例。由于 Flume 被广泛用于整合审计日志和其他 Hadoop 组件日志，我将以此为例。

本例的目标是在我的每个源服务器上设置代理，并在我的目标服务器（位于我的 Hadoop 集群内）上设置一个用于收集或整合日志的代理。随后，我将演示源服务器（因为该设置可以为你需要的任意多数量的源进行复制）和目标服务器的设置，每个都有自己的配置文件。我已经在所有源服务器和我的 Hadoop 集群上安装了 Flume。以下是包含关键条目的源代理配置文件。



## 源代理 ##

配置文件位置：`/etc/flume/conf/flume-src.conf`

启动代理：`/etc/flume/bin/flume-ng agent -c conf -f conf/flume-src.conf -n s_agent`

### exec-source（执行源）

`s_agent.sources = MyServer`

`s_agent.sources.apache_server.type = exec`

`s_agent.sources.apache_server.command = tail -f /etc/httpd/logs/access_log`

`s_agent.sources.apache_server.batchSize = 1`

`s_agent.sources.apache_server.channels = memoryChannel`

`s_agent.sources.apache_server.interceptors = itime`

### timestamp-interceptor（时间戳拦截器）

`s_agent.sources.apache_server.interceptors.itime.type = timestamp`

### memory-channel（内存通道）

`s_agent.channels = memoryChannel`

`s_agent.channels.memoryChannel.type = memory`

`s_agent.channels.memoryChannel.capacity = 100`

### 发送到 Flume 收集器

### avro-sink（AVRO 输出端）

`s_agent.sinks = avro_sink`

`s_agent.sinks.avro_sink.type = avro`

`s_agent.sinks.avro_sink.channel = memoryChannel`

`s_agent.sinks.avro_sink.hostname = 10.243.169.122`

`s_agent.sinks.avro_sink.port = 4545`

从这个配置可以看出，源服务器被命名为 `MyServer`。数据捕获所使用的命令是 Linux 的 `tail` 命令。日志条目保存在内存中（通道 `memoryChannel`）而非文件中。使用拦截器进行过滤，这里的拦截器 `itime` 利用时间戳来确定哪些条目是新的。然后，源代理将日志条目发送到相应的 Flume 代理进行收集（该代理位于我的 Hadoop 集群上——此 IP 地址对应我网络的外部 IP 地址）。目标服务器配置了相应的配置文件，如下所示：

## 目标代理 ##

配置文件位置：`/etc/flume/conf/flume-col.conf`

启动代理：`flume-ng agent -c conf -f /etc/flume/conf/flume-col.conf -n target`

### avro-source（AVRO 源）

`target.sources = AvroIn`

`target.sources.AvroIn.type = avro`

`target.sources.AvroIn.bind = 0.0.0.0`

`target.sources.AvroIn.port = 4545`

`target.sources.AvroIn.channels = mc1`

### 通道 ##

## 源将数据写入一个用于单个输出端的通道

`target.channels = mc1`

### memory-channel（内存通道）

`target.channels.mc1.type = memory`

`target.channels.mc1.capacity = 500`

### 输出端 ##

`target.sinks = LogConsolidator`

### 写入 HDFS

### hdfs-sink（HDFS 输出端）

`target.sinks.LogConsolidator.type = hdfs`

`target.sinks.LogConsolidator.channel = mc1`

`target.sinks.LogConsolidator.hdfs.path = /user/flume/MyData/%{log_type}/%y%m%d`

`target.sinks.LogConsolidator.hdfs.fileType = DataStream`

`target.sinks.LogConsolidator.hdfs.writeFormat = Text`

`target.sinks.LogConsolidator.hdfs.rollSize = 0`

`target.sinks.LogConsolidator.hdfs.rollCount = 10000`

`target.sinks.LogConsolidator.hdfs.rollInterval = 600`

请注意，数据在传输时使用的是 Apache AVRO 文件格式，Flume 在 4545 端口上监听（因为源代理正在向该端口发送数据）。输出端收集器通道 `mc1` 将文件条目写入 HDFS。Flume 在此通道中存储的最大事件数（此处为日志条目）为 500。这意味着，如果目标服务器崩溃，并且有超过 500 个日志事务被排队，则只有 500 个事务会被保存——除非你能清除该通道（通过将事务写入 HDFS）。如果你有足够的内存或磁盘空间，可以通过修改参数 `target.channels.mc1.capacity` 的值来增加这个限制。

作为最后一步，我需要同时在源服务器和目标 Hadoop 集群上启动 Flume 代理。在源服务器上：

```
$ /etc/flume/bin/flume-ng agent -c conf -f conf/flume-src.conf -n s_agent
```

在我的目标 Hadoop 集群上：

```
$ /etc/flume/bin/flume-ng agent -c conf -f /etc/flume/conf/flume-col.conf -n target
```

你可以添加更多的源并安装 Flume 代理，以便将文件发送到你的目标集群。Flume 本身不执行任何数据转换，但你可以根据需要使用 Linux shell 工具或诸如 Python 或 Scala 之类的编程语言对源数据应用转换。

### 总结

Hadoop 世界和大数据领域正处于一个非常活跃的时期，几乎每周都会增加新的 Apache 组件（以及商业解决方案）。这些解决方案中的许多都专注于分析。有些涉及实时分析，另一些则提供文本分析和情感分析。甚至有分析解决方案能够分析其他语言（如中文、德语或西班牙语）的社交媒体数据。还有数据发现解决方案，可以对敏感数据进行分类和脱敏，或为你的数据创建元数据。然而，在 Apache 的产品系列中，并没有针对 ETL 处理的新增组件。市场上确实有一些商业解决方案，但它们在价值上并不足以让你花费金钱和时间（用于重新培训你的团队）。

流式分析正变得越来越流行，`Kafka`、`Storm`、`Spark` 和 `Samza` 被广泛使用。这也是我简要介绍它们的原因，但在 ETL 领域，人们仍然高度依赖 `Sqoop` 和 `Flume`。这些组件的较新版本正在增加有用的功能，如果它们能在数据导入或导出之外执行一些真正的数据转换，那将是非常好的。与此同时，由于这些组件的成熟度，已经有大量现成的解决方案可用，并且会定期为 `Sqoop` 添加新的插件连接器。

如果你计划使用 `Flume`、`Sqoop`，甚至是 `Kafka`、`Spark` 和 `Storm`，在网上做一些关于你的 ETL 需求的研究将会对你有所帮助。



当然，请参考以下排版后的内容：

当然，你需要保持谨慎，不要被“Spark 狂热”冲昏头脑，因为 Spark 是用`Scala`开发的，用户随着时间的推移会发现很多问题。此外，从客观角度来看，`Scala`的市场饱和度为`0.5%`（与`Lisp`差不多），而`Java`为`21.5%`。事实上，随着`Java`（从 1.8 版本开始）引入原生函数式编程结构，使用`Scala`已经没有任何优势。再加上`Spark`（和`Scala`）引入了大量的传递依赖（与其他替代方案相比），这已经不是一个有吸引力的选择了。由于开发者需要掌握所有这些依赖关系才能对`Spark`有良好的工作理解，市场上真正的专家非常少。`Spark Python`现在也是一个流行的选择（因为它和`Scala`一样快），而且更多的数据科学家使用它感到很舒适。

那么，有哪些替代方案呢？`Apache Ignite`、`Apache Drill`、`Apache Kylin`、`Apache Geode`和`Apache Beam`都是很好的选择。如果你对处理流式数据感兴趣，应该研究一下这些组件。

