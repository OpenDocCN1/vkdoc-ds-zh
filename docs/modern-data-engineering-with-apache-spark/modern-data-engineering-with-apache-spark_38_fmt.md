# Spark 调度器池与应用程序监控

默认情况下，当`spark.scheduler.mode`设置为`FAIR`时，Spark会将所有工作分配到default池。通过修改默认池配置，我们可以调整调度权重以优先处理默认（`FAIR`）池，或者划分不同的池用于在`FIFO`或`FAIR`模式下运行低优先级工作负载。利用`fair-scheduler.xml`文件，您可以确保高优先级工作以公平（FAIR）方式进行调度。

如果存在低优先级工作，或者处于共享环境（如Zeppelin）中可能有多个并发会话时，您可以指定一个特定池专门用于低优先级工作。清单14-4中的示例展示了如何从现有的`SparkSession`生成一个临时任务。

```
// spark = SparkSession
new Thread {
val other: SparkSession = spark.newSession()
other.sparkContext.setLocalProperty(
"spark.scheduler.pool", "queued")
other.sparkContext.setJobGroup("batch.job", "runs in thread", true)
other.range(100)
.write
.format("console")
.save()
}
```
**清单 14-4** 为非关键、锦上添花的任务设置低优先级调度器池

您可能会疑惑为什么要在新线程中生成一个新会话。嗯，答案其实很简单。在`SparkContext`上设置本地属性（`setLocalProperty`）的线程将使用分配给它的调度器池。如果我们在主线程（当前线程）上运行一个`StreamingQuery`，那么我们就需要划分出第二个线程来运行非关键工作。

建议在阅读完“Spark监听器”部分后，重新审视此边栏。您可以使用`SparkApplicationListener`或`QueryListener`中提供的`SparkSession`来创建和调度临时查询，如清单14-4所示。鉴于Apache Spark及其众多连接器赋予您的丰富能力，您可以利用临时流程来创建响应式应用程序或控制事件驱动的子程序。

对每个正在运行的Spark应用程序进行某种形式的监控和可观测性至关重要。如果不了解过去的情况，就很难衡量应用程序在构建和发布周期中的变化。希望您能在将应用程序首次发布到生产环境（如果它们是关键任务）之前，就开始捕获其指标。就此而言，有大量可用的（开箱即用的）指标可用于测量、记录和监控Spark应用程序的性能与行为。您将了解两种更常见的Spark监听器接口（`SparkListener`、`QueryListener`）以及`Apache Spark REST端点`提供的指标。结合这些能力，可以监控Spark应用程序的行为，并实现响应式的事件驱动警报。

## Spark 监听器与应用程序监控

关于先有鸡还是先有蛋的争论由来已久。我无法回答这个问题，但监控Spark应用程序以及定义哪些指标对于衡量关键任务Spark应用程序的性能至关重要，这本身就是一门微妙的艺术。为每个应用程序确定关键绩效指标（KPI）会根据Spark应用程序数据合同中定义的`服务水平指标（SLIs）、服务等级目标（SLOs）和服务等级协议（SLAs）`而变化。这些指标有助于定义和塑造每个应用程序的性能特征，并让您了解预期的行为（什么是正常的）。考虑到潜在的大型应用程序表面积以及各个组成部分，包括上游（数据源）和下游（数据接收器），最初可能很难回答关于指标的问题，但将其分解成组、识别所需的数据来源，然后逐步推进会有所帮助。

从广义上讲，监控不仅关乎了解服务的运行方式，同样重要的是能够准确定位系统中出现问题的位置，并在发生故障时缩短平均恢复时间（MTTR），或者仅仅是为了更好地了解应用程序以确保其最佳行为。

监控也有助于指导其他决策。如果这是您的第一次监控尝试，了解我们可以开箱即用地监控哪些内容也会有所帮助，尤其是在Spark方面。这要从监听开始。

### Spark 监听器

`SparkListener`类可以通过`SparkSession`的`SparkContext`对象添加到您的驱动程序应用程序中，或者通过应用程序配置使用`spark.extraListeners`选项进行添加。

```
// spark = SparkSession
spark.sparkContext
.addSparkListener(SparkApplicationListener())
```

`SparkListener`是用于接入运行时应用程序指标和Spark应用程序一般行为的主要候选者。此外，如果您需要对运行中的问题采取更具响应性的方法，监听器可以作为创建临时事件驱动控件的起点。

清单14-5通过一个名为`SparkApplicationListener`的案例类扩展了`SparkListener`类。

```scala
case class SparkApplicationListener()
extends SparkListener {
val logger: Logger = Logger.getLogger(classOf[SparkApplicationListener])
val session: SparkSession = SparkSession.getDefaultSession.getOrElse {
throw new RuntimeException("There is no SparkSession")
}
override def onApplicationStart(applicationStart: SparkListenerApplicationStart): Unit = {
super.onApplicationStart(applicationStart)
logger.info(s"app.start app.name=${applicationStart.appName}")
}
override def onJobStart(jobStart: SparkListenerJobStart): Unit = {
super.onJobStart(jobStart)
logger.info(s"job.start jobId=${jobStart.jobId} jobStart.time=${jobStart.time}")
}
override def onStageSubmitted(stageSubmitted: SparkListenerStageSubmitted): Unit = {
super.onStageSubmitted(stageSubmitted)
val stageInfo: StageInfo = stageSubmitted.stageInfo
logger.info(s"stage.submitted stage.id=${stageInfo.stageId}")
}
override def onStageExecutorMetrics(executorMetrics: SparkListenerStageExecutorMetrics): Unit = {
super.onStageExecutorMetrics(executorMetrics)
val em: SparkListenerStageExecutorMetrics = executorMetrics
logger.info(s"stage.executor.metrics stageId=${em.stageId} stage.attempt.id=${em.stageAttemptId} exec.id=${em.execId}")
}
override def onTaskStart(taskStart: SparkListenerTaskStart): Unit = {
super.onTaskStart(taskStart)
val ts = taskStart
val taskInfo: TaskInfo = ts.taskInfo
// taskInfo 包含丰富的状态和时序信息
logger.info(s"task.start " +
s"task.id=${taskInfo.taskId} task.status=${taskInfo.status} task.attempt.number=${taskInfo.attemptNumber} " +
s"stage.id=${ts.stageId} stage.attempt.id=${ts.stageAttemptId}"
)
}
override def onTaskGettingResult(taskGettingResult: SparkListenerTaskGettingResult): Unit = {
super.onTaskGettingResult(taskGettingResult)
logger.info(s"task.getting.result ${taskGettingResult.taskInfo.taskId}")
}
override def onTaskEnd(taskEnd: SparkListenerTaskEnd): Unit = {
super.onTaskEnd(taskEnd)
val te = taskEnd
val taskInfo: TaskInfo = te.taskInfo
val taskEndReason: TaskEndReason = te.reason
val taskMetrics: TaskMetrics = te.taskMetrics
logger.info(s"task.end task.type=${taskEnd.taskType} end.reason=${taskEndReason.toString} task.id=${taskInfo.taskId}" +
s"executor.cpu.time=${taskMetrics.executorCpuTime}")
}
override def onStageCompleted(stageCompleted: SparkListenerStageCompleted): Unit = {
super.onStageCompleted(stageCompleted)
val stc = stageCompleted
val stageInfo: StageInfo = stc.stageInfo
//val stm: TaskMetrics = stageInfo.taskMetrics
logger.info(s"stage.completed stage.id=${stageInfo.stageId} stage.completion.time=${stageInfo.completionTime.getOrElse(0)}")
}
override def onJobEnd(jobEnd: SparkListenerJobEnd): Unit = {
super.onJobEnd(jobEnd)
logger.info(s"job.end job.id=${jobEnd.jobId} end.time=${jobEnd.time}")
}
override def onExecutorAdded(executorAdded: SparkListenerExecutorAdded): Unit = {
super.onExecutorAdded(executorAdded)
}
override def onExecutorMetricsUpdate(executorMetricsUpdate: SparkListenerExecutorMetricsUpdate): Unit = {
super.onExecutorMetricsUpdate(executorMetricsUpdate)
}
override def onExecutorRemoved(executorRemoved: SparkListenerExecutorRemoved): Unit = {
super.onExecutorRemoved(executorRemoved)
}
override def onEnvironmentUpdate(environmentUpdate: SparkListenerEnvironmentUpdate): Unit = {
super.onEnvironmentUpdate(environmentUpdate)
}
override def onOtherEvent(event: SparkListenerEvent): Unit = {
super.onOtherEvent(event)
}
override def onApplicationEnd(applicationEnd: SparkListenerApplicationEnd): Unit = {
super.onApplicationEnd(applicationEnd)
logger.info(s"application.ended end.time=${applicationEnd.time}")
}
}
```

## 列表 14-5 `SparkApplicationListener` 案例类

列表 14-5 中的案例类继承了 `SparkListener`，为进一步探索指标提供了一个简单的切入点。事件的顺序很重要。每个作业启动并产生一个或多个阶段，这些阶段将任务分发给分配给应用程序的执行器。能够跟踪失败任务或失败阶段的比率，可以使你从高层次上深入了解应用程序的运行时行为。

举个具体的例子，你可以直接在 Spark 单元测试中观察 `SparkApplicationListener`，以查看应用程序运行时生成的事件。尝试启用位于 `TypedRevenueAggregatesSpec`（来自第 13 章）中的监听器。只需取消注释该行并运行应用程序单元测试，即可观察实际效果。

# Spark监听器使用说明

```scala
val testSession = SparkTypedStatefulAggregationsApp
.sparkSession
.newSession()
testSession.sparkContext
.addSparkListener(SparkApplicationListener())
```

`SparkListener` 可用于通过 [`stats.d`](https://github.com/statsd/statsd) 发布指标。结合你内部的指标系统，或使用 [DataDog](https://www.datadoghq.com/) 等付费服务，你可以存储与应用程序运行时相关的重要指标。

**提示**：应用程序指标可通过 Spark 应用程序驱动程序上的 metrics REST API 从 `Driver` 应用程序 UI（默认端口 4040）获取。请使用官方“Spark 监控”文档，查看可用于通过驱动程序和独立主进程消费指标的各种 API。

除了主要的 `SparkListener` 之外，还有一个有用的类可用于向应用程序添加控制流，称为 `StreamingQueryListener`。

### 使用 `StreamingQueryListener` 观察结构化流行为

`StreamingQueryListener` 用于观察 `SparkSession.streams` 对象，可以接入驱动程序管理的所有 `StreamingQuery` 实例。该监听器追踪的三种可观察状态分别对应所有流查询的 `started`（启动）、`progress`（微批次完成）和 `termination`（终止）事件。

代码清单 14-6 继承了 `StreamingQueryListener`，并提供了示例方法实现、监听器接口方法名称以及事件对象。为帮助您入门，它演示了如何接入部分事件元数据以增强日志记录。

```scala
case class QueryListener() extends StreamingQueryListener {
  val logger: Logger = Logger.getLogger(classOf[QueryListener])
  val session: SparkSession = SparkSession.getDefaultSession.getOrElse {
    throw new RuntimeException("没有可用的 SparkSession")
  }
  override def onQueryStarted(event: StreamingQueryListener.QueryStartedEvent): Unit = {
    logger.info(s"query.started stream.id=${event.id} stream.name=${event.name} " +
      s"stream.run.id=${event.runId} stream.start.time=${event.timestamp}")
  }
  override def onQueryProgress(event: StreamingQueryListener.QueryProgressEvent): Unit = {
    val queryProgress: StreamingQueryProgress = event.progress
    val batchId = queryProgress.batchId
    val batchDuration = queryProgress.batchDuration
    val inputRowsRate = queryProgress.inputRowsPerSecond
    val processedRowsRate = queryProgress.processedRowsPerSecond
    val outputRowsPerBatch = queryProgress.sink.numOutputRows
    val progressSummary = queryProgress.prettyJson
    logger.info(s"query.progress stream.id=${queryProgress.id} stream.name=${queryProgress.name} " +
      s" batch.id=$batchId batch.duration=$batchDuration" +
      s" rate: input.rows.per.second=$inputRowsRate processed.rows.per.second=$processedRowsRate " +
      s" sink.output.rows.per.batch=$outputRowsPerBatch " +
      s"\n progressSummary=$progressSummary")
  }
  override def onQueryTerminated(event: StreamingQueryListener.QueryTerminatedEvent): Unit = {
    if (event.exception.nonEmpty) {
      logger.error(s"query.terminated.with.exception exception=${event.exception.get}")
      // 决定后续操作
    } else {
      logger.info(s"query.terminated stream.id=${event.id} stream.run.id=${event.runId}")
    }
  }
}
```

代码清单 14-6
`StreamingQueryListener` 使您能够观察结构化流应用程序的运行时行为

`QueryListener` 类可用于监控任何结构化流应用程序。与 `SparkApplicationListener` 类似，您可以使用第 13 章的 `TypedRevenueAggregatesSpec` 来测试 `QueryListener`。

```scala
testSession.streams.addListener(QueryListener())
```

现在您有了直接设置断点并插入创造性控制流的方法，以应对各种进度或终止事件。通过采用测试驱动的方法来理解 `QueryListener` 的能力，您可以在 IDE 中轻松创建更好的测试来驱动意外行为。

最终，查询观察是一项有助于确保应用程序持续按预期运行的功能。通过扩展监听器，您可以收集重要的流式指标（输入和处理速率），从而了解应用程序的运行状况。

最后请注意，代码清单 14-5 中的 `SparkApplicationListener` 和代码清单 14-6 中的 `QueryListener` 都直接运行在 Spark Driver 上。了解这一点后，您可以在应用程序出现异常时，编写连接到 Slack 或 [PagerDuty](https://www.pagerduty.com/) 的钩子，或者利用 `SparkSession` 句柄（两个示例监听器类中都可用）来触发基于事件的子程序。例如，使用 `QueryListener`，您可以根据活动查询进程的原因（异常）主动停止所有正在运行的查询并优雅地关闭故障驱动程序。代码清单 14-7 展示了 `QueryListener` 的钩子，让您了解如何接入事件来控制应用程序。终止 `StreamingQuery` 的异常可能是可恢复的。否则，您可能需要关闭应用程序。

```scala
// QueryListener
def resetTerminated(): Unit = {
  session.streams.resetTerminated()
}
def haltAllStreams(): Unit = {
  session.streams.active.foreach { query =>
    logger.info(s"stream.stop name=${query.name} stream.id=${query.id}")
    query.stop()
  }
}
def shutdown(): Unit = {
  haltAllStreams()
  session.sparkContext.cancelAllJobs()
  session.stop()
}
override def onQueryTerminated(event: StreamingQueryListener.QueryTerminatedEvent): Unit = {
  if (event.exception.nonEmpty) {
    logger.error(s"query.terminated.with.exception exception=${event.exception.get}")
    if (exception.is.recoverable) {
      resetTerminated()
    } else {
      shutdown()
    }
  }
}
```

代码清单 14-7
使用 `QueryListener` 响应 `StreamingQuery` 中接收到的异常

## 代码清单 14-7 接入 `StreamingQueryManager` 和 `SparkContext`，以重置任何已终止的流或停止任何额外的流查询。然后显式调用取消所有活动作业（给打开的和活动的作业时间优雅地停止和清理）。代码最后要求驱动程序应用优雅关闭。选择何时停止应用程序，对于保护下游应用程序免受损坏数据或其他未知因素的影响至关重要。当遇到不可恢复的异常时，通常更好的做法是让应用程序失败并通知应用程序所有者，因为知道这（可能并且很可能会）在短期内导致流量阻塞，但这可以保护任何下游系统免受运行中数据问题的影响。

有许多策略可以确保应用程序无论环境如何变化都能持续运行。了解让应用程序保持稳定的正确模式需要耐心和实践，但有了合适的工具，就可以更容易地实现自动化恢复，并直接在应用程序中广播需要知晓的事件（在它们崩溃之前）。

### 监控模式

连接到指标聚合器（如 [Prometheus](https://prometheus.io/) 或 [DataDog](https://www.datadoghq.com/)）的连接器可以整合到您的 Spark 监听器中，从而创建由驱动程序发出的可观测性中心点。这比监控和观察所有执行器更好，因为异常信息和执行器指标最终会传回驱动程序，然后可以通过 `SparkApplicationListener` 接入。

您的应用程序发出的指标——通过 Spark 监听器接口或 Spark 内部收集并通过 JMX 或 REST 接口提供的指标——可用于驱动仪表盘和其他警报系统。此外，一旦操作指标开始流动，您就可以使用应用程序的历史指标来确保它们在新版本发布到生产环境后仍能保持同样好或更好的性能。这些先前的观察（历史指标）有助于确保对每个应用程序的更新和升级（例如错误修复或性能调优的结果）可以被监控和衡量，从而保持您的 SLA、SLO 和 SLI 处于受控状态。

### Spark 独立集群与应用迁移策略

互联网上充满了最佳实践和建议。在 Spark 独立集群上运行多租户应用程序时，最好制定一个迁移应用程序和集群组件的策略，以最大程度地减少停机时间。

### 反模式：心存侥幸

部署和滚动（按比例替换）服务器集群的工具已经存在一段时间了。其中一种很快失效的模式是针对 Spark Worker 的“滚动并祈祷”方法。如果您正在运行结构化流式处理应用程序，您将不断中断正在运行的驱动器应用程序，而应用程序可以处理间歇性暂停、请求新的执行器、分配以及重建任何丢失的应用程序状态。持续中断的过程，例如每五分钟滚动 5% 的 Worker，可能会侵蚀服务等级协议（SLA）。当机器上线或下线时，应用程序中断可以自然处理，但通常更好的方法是启动一个替代的 Spark Standalone 集群并迁移应用程序（具体取决于延迟和您的工具）。

### 最佳实践

1.  创建一个新集群。

2.  将现有应用程序迁移到新集群（根据 SLA 从优先级最低的应用程序开始反向启动）。

3.  关闭之前的集群（如果您不再需要计算资源：走向绿色）。

这是一份最佳实践清单，因为 Spark 应用程序及其运行的集群会进行更新，以处理新版本的 Apache Spark、Java、Scala 以及其他 Python 或系统依赖项，同时修补应用程序配置或 Master、Worker 和 Driver 上的全局设置。最好了解这些更改如何影响性能，因此，在您对此过程感到得心应手之前，请使用“矿井中的金丝雀”技巧，优先迁移最低公共分母（风险最低）的应用程序。

接下来，您将了解可用于监控 Spark 应用程序性能和行为的事件监听器。

### 关于容器和 Spark Standalone

Spark Standalone 可以使用容器运行，但这超出了本书的涵盖范围。本质上，您可以使用来自第 11 章中运行 Kafka 集群的 Zookeeper 容器（应已缓存）。然后，您必须修改 Spark Master 和 Worker 的环境变量（`spark-env.sh`），为每个组件分配一个唯一的主机名（例如 `spark-master1`、`spark-master2`、`spark-worker1`、`spark-worker2`、`spark-worker3`），或者重用 Docker 网络（`mde`），或者创建一个名为 `spark` 的新网络。这将实现 Spark 集群组件与 Spark Driver 应用程序之间的跨集群通信。

Apache Spark 官方文档涵盖了“为网络安全配置端口”，可用于查看端口需求以配置防火墙规则。例如，Spark Driver 应用程序需要对 7077 端口的 Spark Master 进行入站访问，以提交（部署）应用程序。Spark Master 还暴露了一个可选的基于 REST 的服务，该服务在端口 6066 上运行，可用于接受在集群中运行的远程作业。对于 Spark Master，其用户界面（UI）默认在端口 8080 上运行；Spark Worker 在端口 8081 上公开 UI；每个 Driver 应用程序在端口 4040 上运行一个 UI。Driver 端口可以通过设置 `spark.ui.enabled: false` 来关闭，以节省 Driver 的处理开销，或限制用户访问应用程序日志的方式。

## 托管 Spark

本章讨论了用于构建 Spark 集群以及接入 Apache Spark 应用程序操作指标的架构组件和 Spark 类。您可能想知道为什么没有讨论使用 [Apache Spark on Amazon EMR](https://aws.amazon.com/emr/features/spark/) 或 [Databricks](https://databricks.com/) 来简化运行 Spark 应用程序的过程，从而避免所有麻烦。使用任一平台都可以大大减少启动和运行所需的工作量，应在您的短期和长期业务规划中予以考虑。本书采用开源方法（从各方面来看）作为揭开其神秘面纱的一种方式。目标是让任何人都能免费了解 Spark 并学习现代数据平台的支柱（不包括笔记本电脑和书籍）。

## 总结

Spark Standalone 集群更常见的是使用传统的 Linux 部署和 DevOps 模式来运行，包括用于创建和布局文件系统的引导脚本，以及使用 Yum 和 Apt-Get 等 Linux 包管理器来搭建运行时环境。在没有明确划分应用程序进程、CPU 分配和内存限制（又名 cgroups）的情况下运行多租户服务器环境各有利弊——主要是您可以绕过容器，依靠传统的 RPM 包来管理运行时安装的内容。

根据您平台支持的应用程序的要求，通常存在一些叠加挑战，与运行多个版本的 Java（支持 Java 8 和 Java 11）、多个版本的 Scala（2.12、2.13）、处理 PySpark 的原生需求，或者在无容器环境下调试性能下降时哪个进程是罪魁祸首有关。

最后一点，虽然在同一台机器上支持多个版本的 Spark 也是可能的，但这可能是一场管理噩梦。更简单的做法是使用将在集群中运行的工作负载的共同依赖项运行一个 Standalone 集群，并根据性能特征和可伸缩性（扇出、扇入）需求，为将在集群中运行的工作负载划分出隔离的集群。如果这听起来需要很多工作，确实可能如此，但您也可以使用 [Chef](https://chef.io/) 或 [Terraform](https://www.terraform.io/) 等框架自动化许多细微差别。

考虑到本书主要采用容器驱动的方法编写，并且考虑到容器从操作角度提供了延续到 Spark 运行时的一致性和可靠性，我们旅程的终点最有可能是将 Spark 应用程序部署到 Kubernetes 上。（实际上，您从第 7 章开始构建的应用程序都可以使用 Spark on Kubernetes 运行。）我们将在剩下的时间里学习如何在 Kubernetes 上运行关键任务应用程序。