# 14. 在 Spark Standalone 上部署关键任务型 Spark 应用

这是*第一天*。你可能听过人们以复杂的情感谈论*第一天*，从真切兴奋与喜悦，到轻微沮丧，甚至因高压和焦虑而产生的愤怒。任何软件的首次正式发布都注定会引发各种情绪，因为这是*首个*主要发布里程碑。但除了击掌庆贺与祝贺之词外，人们称之为“第一天”的原因在于：这是持续开发与支持周期的起点，是众多版本中的第一个发布。第一天，正是艰苦工作真正开始的地方。

本章致力于确保你的*第一天*顺利进行。你将学习 Spark 应用的各种部署策略，以及一些额外的技巧、窍门和技术，确保你无论潮汐将你带向何方都能做好准备。

## 部署模式

让我们从基础开始。严格来说，在本书中你并未*运行*过很多 Spark 应用。但这并不意味着你之前所做的工作毫无意义，而是迈向应用部署准备过程中的另一个自然步骤。要达到能够部署 Spark 应用的程度，你首先需要理解如何*构建* Spark 应用、使用哪些库，以及如何融合设计模式与最佳实践。

最终，我们必须就哪些流程在起作用（以及工作中）达成共识，例如，要通过配置控制应用的运行时行为，你需要学习 Spark 配置如何被组装、合并和覆盖，从而无需重新编译即可增强应用行为。这项技术使你能够控制应用的诸如处理速率、触发器间隔、检查点目录等属性，并且通过一种元方式（上一章介绍过），你甚至能够接入不同的源和接收器，以利用对替代位置的读写功能。

> **提示**
>
> 出于调试和重放（即数据恢复）的目的，通常会运行结构化流应用的第二个副本，并覆盖其检查点位置和替代的接收器配置。这项技术使你能够在分析并解决问题时，不损坏任何已存储的状态，也不会写入可能最终进入下游系统的重复记录。

能够从外部（由配置驱动）控制应用的行为，为你提供了更多运营关键任务型应用的方式，并能缩短平均恢复时间（MTTR）。尤其是在问题可以通过配置和调优来解决，而无需修复源代码中的错误时。

让我们将注意力转回部署流程。要成功部署应用，我们需要审视核心框架并在其之上构建新层级。本质上，这让我们能够先爬再走，先走再跑，最后先跑再*部署*。那么，*运行应用*和*部署应用*之间有什么区别呢？

### 运行 Spark 应用

在整本书中，你使用 `spark-shell` 和 Apache Zeppelin 构建了大量的纳米级和微型应用。这些执行模式封装了*动态*运行 Spark 应用的模式。通过从一系列代码块（代码）开始构建，并编排基于步骤的例程（程序），你能够通过组合方式即时创建 Spark 应用。这些输出（学习成果）可以转化为完全编译的应用（并包含测试！）。到第 7 章时，我们已经编写了完整应用，并使用容器与托管本地卷挂载（覆盖层）的混合方式，通过 `docker exec` 和 `spark-submit` 在单台机器上（或集群外）*运行* Apache Spark。

*运行*和*部署*之间的区别在于是否存在*物理集群*，或者说，是否缺乏单一故障点来保障关键任务型 Spark 应用的连续性。让我们看看集群模式选项，并学习如何部署 Spark 应用。

### 部署 Spark 应用

*一致性。可靠性。可信赖性。* 如果你依赖某物或某人，失望会令人受伤。类似地，部署你的 Spark 应用（即提交你的驱动程序应用）到外部计算集群上运行，能为你的应用提供容错能力以及其他关键功能，以应对流经你应用的数据洪流（流式或涓流式）中自然的起伏。需要扩展应用以应对增长流量？很好，你可以分配更多机器资源（动态调整，或在静态配置中增加核心数）。缩减资源也是如此。当你的应用处于空闲状态时，你可以释放机器资源并将其归还给集群。

即使在决定在何处、如何以及何时部署你的应用之前，就有许多因素在起作用。让我们先从了解 Apache Spark *资源管理器*以及它们在结构化流应用生命周期中扮演的角色开始这个过程。

### Spark 集群模式与资源管理器

Apache Spark 自带多种开箱即用的资源管理器，其中（在撰写本文时）最流行的是*独立模式*、*Kubernetes* 和 *Yarn* 集群资源管理器。那么，什么是集群资源管理器呢？

集群资源管理器负责许多事项——其中最重要的是维护和管理集群状态，以及提供通用的能力（API）来通信、调度、跟踪、分配和分发计算资源，以供在集群中运行的 Spark Driver 应用程序使用。

从高层次来看，资源管理器监控和观察集群（一个或多个网络连接的计算节点），跟踪物理计算节点的最新统计数据，检查工作节点（或执行器）的状态和健康情况，并本质性地询问节点（或进程）是否仍然存活。此外，资源管理器还会维护一个关于总可调度资源（物理节点、CPU 核心和内存）的状态账本，记录哪些资源尚未被调度，从而使集群资源管理器能够控制或委托批准或拒绝 Driver 应用程序请求额外计算资源的决策。由于集群是动态的，新节点（能够运行 Spark 进程的服务器）可以被添加到集群中，而节点如果失去响应、未通过健康检查或出于任何其他原因离线，也可以被移除。通过确保存在一个通用通道供 Spark 应用程序传达其需求，资源管理器已准备好处理新的资源分配请求——当现有资源丢失，或应用程序负载超出当前计算资源（在不进行横向扩展的情况下）的承受能力并发出求助信号时。

这一切可能让你联想到 CAP 定理以及第 9 章中描述的“惊群效应”问题和多租户问题。像 Kubernetes、Yarn（Hadoop）和 Spark Standalone 这样的分布式系统提供了委托控制系统和服务的机制，而 Spark 的资源管理器都是容错的，能够在各种故障场景下维持一致性并保证可靠性。这一切归根结底，集群资源管理器是基础，它使您的结构化流应用程序能够承受故障（例如服务器离线），并在大多数情况下无需人工干预就能恢复。

我们将在此章讨论 Spark Standalone，并在下一章以 Spark on Kubernetes 作为结尾。如果您对 Yarn 或 Mesos 资源管理器感兴趣，网上有很多很好的资料可以帮助您快速上手，但它们不在本书的讨论范围之内。

## Spark 独立模式

运行一个 Spark 独立集群，对于一个简单的测试集群来说，最少需要三个节点。一个集群协调节点（主节点），以及两个工作节点，它们可以分担一个或多个 Spark 应用程序的工作。独立模式允许您提交 Spark Driver 应用程序（以客户端或集群模式），在底层 Spark 集群的计算资源子集上运行分布式工作负载。

虽然您可以使用最少三个节点来运行，但这样集群不具备容错能力，因为协调器（主节点）可能会离线，导致集群状态消失。对于生产环境用例，以高可用模式运行 Spark Standalone 至关重要。

### Spark Standalone：高可用模式

要实现高可用，就必须具备容错能力，这本质上意味着在故障情况下需要保持可靠和一致。Spark 采用了一种与您之前看到的针对集群状态进行检查点（在第 10、11 和 13 章中）类似的过程。活跃的主节点只需将集群状态写入一个可靠（持久）的原子文件系统（Apache ZooKeeper），而第一个上线并连接到 ZooKeeper 的 Spark 主节点将赢得活跃状态。图 14-1 展示了 Spark Standalone 的高可用（HA）设置，包括 (1) Spark 主节点、(2) Spark 工作节点和 (3) Apache ZooKeeper。

![](img/505711_1_En_14_Fig1_HTML.png)

图 14-1 Spark Standalone 集群模式

图 14-1 中的集群图展示了一个六节点集群（不包括 ZooKeeper (`zk`) 集群，由于 Apache ZooKeeper 基于仲裁的架构，该集群需要至少三个节点来实现高可用，因此完全高可用大约需要九台服务器）。从 (1) 开始，两个 Spark 主节点正在监督（监视）集群。一个处于活动状态（视为主节点），而另一个则在备用模式（辅助节点）下等待，准备在当前活动（主）主节点离线时投入行动。 (2) 工作节点（又名执行器）显示在图 14-1 的底部。当工作节点服务上线并向 Spark 主节点注册时，该过程有效地增加了集群的总可用计算资源。集群可以随着工作节点的加入和离开而动态增长和收缩，使 Spark 集群能够处理网络问题，优化性能和成本，并使集群在其他故障中保持连续性。常见的操作，如推送更新、推出新服务或在集群中替换主机（服务器），对任何正在运行的应用程序都影响极小。

#### 故障转移过程

一旦当前活跃的主节点断开与 (3) ZooKeeper 的连接，备用主节点将接过接力棒，从被动状态切换到活动状态，以保持集群存活（故障转移到备用主节点通常需要 1-5 分钟，具体取决于您的网络和集群规模）。

> **警告**
>
> 值得指出的是，只有在故障转移事件发生后，集群才能保持高可用（`HA`），例如，当前活跃的主节点离线，Spark 将被动备用主节点提升为活跃节点。监控节点故障并自动化部署替换节点的过程，对于您所运维的集群的健康至关重要。

如果您确实丢失了集群状态，请不要惊慌。Spark 应用程序将继续运行，无需与集群协调器（主节点）通信，直到某个 Spark 应用程序需要获取新的执行器（在独立模式下，这些执行器运行在工作节点上）为止。这为您恢复集群赢得了时间。因此，如果您自己运行 Spark（并且没有为托管服务付费），请确保您和您的团队针对一个或两个主节点丢失的情况进行实验，并自动化保持集群在故障下持续运行的过程。

默认情况下，在独立集群模式下部署应用程序将允许第一个部署的应用程序消耗集群中的所有资源，除非配置了限制其使用的 CPU 核心和内存。正如您可能想象到的，单个应用程序独占所有集群资源可能会引发问题，但有一些现成的流程可以利用，以确保应用程序之间能够良好协作。这一切都始于应用程序的部署。

应用程序部署从将 Driver 应用程序提交到集群开始，在我们进一步深入之前，理解 Driver 应用程序如何以分布式方式运行非常重要。对于 Driver 应用程序，有两种主要的部署风格，由 `spark.submit.deployMode` 应用程序设置控制。

# 部署模式：客户端模式与集群模式

当部署 Spark Driver 应用时，`spark-submit` 配置提供了两种部署方式供选择。我们首先探讨*客户端*模式，然后再了解*集群*模式。

## 客户端模式

在客户端模式下部署 driver，本质上意味着 Driver 程序将在本地机器上*本地运行*，并向 Spark 主节点发出 `spark-submit` 命令。例如，清单 14-1 中的配置来自第 11 章，如果我们更改配置以指向一个活动的独立集群（`--master "spark://{master-hostname}:7077"`），实际上我们是向集群资源管理器声明：我们只希望分配执行器来分担应用工作负载，而 Driver 程序的资源分配我们已经自行处理好了。

```
docker run \
...
-it `whoami`/spark-kafka-coffee-orders:latest \
/opt/spark/bin/spark-submit \
--master "..." \
--class "com.coffeeco.data.SparkKafkaCoffeeOrdersApp" \
--deploy-mode "client" \
...
/opt/spark/app/spark-kafka-coffee-orders.jar
```

清单 14-1 以客户端模式部署 Spark 应用

图 14-2 展示了在客户端模式下，Driver 程序运行在集群外部，而执行器则被分配给该远程应用。

![](img/505711_1_En_14_Fig2_HTML.png)

图 14-2 以客户端模式运行的 Spark Driver 应用

将 Driver 运行在 Spark 集群之外有许多原因。其中最主要的三点原因是：

- 保护您的 Spark 应用并将其与集群内“吵闹的邻居”或其他不良行为造成的干扰隔离开来。
- 通过限制对集群外部 driver 的访问，保护机密和凭证不被整个集群访问，这对于运行由众多不同团队生成、具有各种管道需求的工作负载的多租户共享集群至关重要。
- 提供一个已知的主机（端点），用于连接 driver 应用以查看 Spark UI 或生成监控指标。

## 集群模式

在集群模式下部署 Driver 程序，本质上意味着该应用将在 Spark 集群内部远程运行，并接受接收了传入 `spark-submit` 命令的 Spark 主节点的监管。例如，清单 14-2 中的配置是对清单 14-1 的修订。它替换了部署模式的值，同时指定了运行时资源（driver 和执行器）以及特殊的 `supervise` 标志。

```
docker run \
...
-it `whoami`/spark-kafka-coffee-orders:latest \
/opt/spark/bin/spark-submit \
--master "..." \
--class "com.coffeeco.data.SparkKafkaCoffeeOrdersApp" \
--deploy-mode "cluster" \
--driver-memory "2g" \
--executor-memory "4g" \
--total-executor-cores 4 \
--supervise \
...
/opt/spark/app/spark-kafka-coffee-orders.jar
```

清单 14-2 部署由集群管理的 Spark Driver 应用

如前所述，当向集群部署应用时，Driver 程序会*共同部署*在集群内部的某个 worker 上。图 14-3 展示了 Driver 应用如何与集群内其他程序共存，并占用所分配 worker 上可用总资源的一部分。

![](img/505711_1_En_14_Fig3_HTML.png)

图 14-3 以集群模式运行的 Spark Driver 应用

考虑到在集群内运行的 Driver 应用的服务等级协议，了解每个共存应用在多租户集群中运行的开销是一个需要牢记的重要事实。如果没有资源来运行 Driver 程序（在发生故障后或因首次部署），那么 Driver 将处于 `Queued` 状态。

正如将 driver 程序运行在集群之外有好处一样，将 driver 运行在 Spark 集群内部也有其优势。本质上，通过消除在集群外部运行外部主机或额外进程的需求，您可以减少运营流式应用或管道基础设施所需的资源面积。此外，对于经过测试且成熟的 Spark 应用，使用 `--supervise` 标志的基于集群的监管，使得应用在崩溃后能够重新启动。

另一方面，如果 Spark 独立集群专用于一个共同的总体任务，例如处理 CoffeeCo 客户订单或合并商店交易和客户事件数据以创建窗口化分析流，那么可以启动集群来解决类似问题，复用已批准的访问权限（遵守数据治理规则和法规）、身份验证、授权以及共享数据访问。

两种部署策略（客户端模式和集群模式）各有利弊。它们允许您使用适合相应应用的 SLI、SLO 和 SLA 的策略。因此，理解如何在一个多租户环境中共享资源至关重要。现在，我们将探讨 Spark Driver 应用如何在独立集群中部署和分发。

## 多租户的分布式共享资源与资源调度

请将 Spark 独立集群视作一个分布式操作系统。您拥有总计的*核心数*（CPU）、总计的*内存量*（RAM），以及每个*节点*、worker（执行器）和 driver 上的磁盘空间，这些资源都在 Spark 进程（*pid*）、操作系统进程（也是 pid！）以及分布式集群中每台服务器上运行的任何其他服务之间共享。

> **提示**

> 使用 Linux 的 `top` 命令可以查看每个服务器上正在运行的进程及其消耗的资源。对于 Docker 的资源开销，可以使用 `docker stats` 命令查看运行中容器的 CPU、内存和网络消耗。

为什么这很重要？很高兴您问这个问题。本质上，您不能现实地期望使用集群中任何*节点*（*主节点、worker、driver*）上 100% 的资源，而不落得两手空空或在操作系统层或应用层面临资源受限的困境。*一条经验法则是至少为操作系统保留 1 个 CPU 核心和 1GB 内存*。您还必须权衡在 Apache Spark 范围之外运行的任何其他进程的成本，例如指标收集器、网络分析器等。一旦您了解了非 Spark 进程所需的开销，就可以配置 worker 的行为，使其友好共存，避免对特定服务器的资源过度贪婪。

### 控制资源分配、应用行为与调度

在多租户（共享）Apache Spark 集群中，可以通过表 14-1 中的 Spark 设置在部署时控制分配给每个运行中 Spark 应用的资源数量。

**表 14-1** 控制 Spark 应用的资源分配与调度方式

| 属性名称 | 默认值 | 功能说明 |
| --- | --- | --- |
| `spark.cores.max` | （未设置） | 限制应用在运行时最多可调度的总核数。若不设置，应用将占用所有可用核心。 |
| `spark.task.cpus` | `1` | 告知调度器 Spark 应用的真正需求。如果存在需要线程池或其他 I/O 的额外 CPU 密集型任务，可考虑将此值提升至 `2` 或更高（取决于每个任务的实际 CPU 消耗）。 |
| `spark.driver.cores` | `1` | 定义运行驱动程序所需的核心（物理 CPU）数量。如果应用通过调用 `collect()` 对 DataFrame/Dataset/RDD 进行最终加工，应设置更高的数值。 |
| `spark.executor.memory` | `1g` | 定义运行 Spark Executor 的 JVM 进程所分配内存的软限制。该值为执行器进程的分配量，在 Standalone 模式下并非硬性限制，即 JVM 可以动态分配更多内存。 |
| `spark.executor.cores` | `ALL` | 此设置需要重点关注，除非另行指定，否则 Spark 应用将占用所有可用核心。对于多租户场景，可以通过将该值设为 `1`、`2` 或 `N` 个核心来共享资源。 |

表 14-1 仅列出了在将应用部署到 Standalone 共享（多租户）Spark 集群时，需要仔细关注的部分重要配置项。

请记住，结构化流式应用是 7x24 小时不间断运行的，因此为每个应用设置资源限制变得至关重要，因为每个应用在其驱动程序启动时就会分配并持有所声明的资源。这意味着，如果应用以静态分配模式运行，总核心数（物理 CPU）和内存（JVM 分配的堆空间）将在启动时一次性被占用。

例如，图 14-4 展示了一个新的 Spark 驱动程序向集群请求资源的流程。这里有四个节点，每个节点都运行着一个 Spark Worker 进程。每个 Worker 拥有 8 个核心和 16 GB 内存，这意味着整个集群共有 32 个核心和 64 GB 内存可供一个或多个 Spark 驱动程序分配。假设 Spark 应用 (A) 的配置如下：

![](img/505711_1_En_14_Fig4_HTML.png)

**图 14-4** 新 Spark 应用部署到集群时的协商流程图

# Spark 集群配置与弹性伸缩

```
spark.cores.max: 16
spark.executor.cores: 8
spark.executor.memory: 16gb
```

那么驱动程序将被分配两个执行器实例（JVM），这两个实例将完全占用四个可用 Worker 节点中的两个。换言之，该配置的意思是：“我想要占用整整两个 Worker。”

图 14-4 中的事件序列展示了 Spark 应用部署到集群的过程。它首先与 (1) *活动主节点* 协商，根据应用设置（或集群默认设置）中关于运行分布式工作负载所需的总资源（核心/内存）来 (2) *分配执行器*。在此示例中，我们设置了 16 个总核心的限制，指示集群资源管理器（Standalone 模式下的活动主节点）为该应用分配四个 Worker 中的两个。至此，Spark 集群的 50% 被分配出去，另外 50% 为空闲。一旦执行器通过 `SparkContext` 分配给 Spark 驱动程序，驱动程序就不再需要与主节点通信，除非有执行器丢失（服务器宕机或 JVM 因内存不足而退出），或者启用了 `spark.dynamic.allocation` 并且应用向集群请求或归还资源。

这也意味着，当前集群设置可以轻松处理已分配执行器中断（例如两个 Worker 同时离线）的故障场景。驱动程序 (A) 只需向资源管理器（Spark 活动主节点）请求新的执行器即可——鉴于集群中还有两个完全空闲的 Worker，这个请求是可以被处理的。

我们再看图 14-5 中的例子，该图展示了第二个 Spark 应用加入共享集群的过程。为举例说明，第二个应用将采用以下部署设置。

![](img/505711_1_En_14_Fig5_HTML.png)

**图 14-5** 共享集群上的 Spark 驱动程序多租户部署

```
spark.cores.max: 4
spark.executor.cores: 2
spark.executor.memory: 4gb
```

第二个驱动程序将向集群中剩余的两个 Worker（它们还有可供调度的资源）请求资源。至此，所有 Worker 都被安排了执行器。好处是资源浪费更少（因为没有核心或内存被闲置），且集群中运行着不止一个应用；但另一方面，如果此时丢失一个 Worker，会发生什么呢？

在图 14-5 的场景中，如果集群丢失一个 Worker，结果取决于丢失的是哪一个。这里有两种情况。第一种，驱动程序 (B) 会完全运行在一个 Worker 上，因为每个执行器配置了 2 个核心和 4GB 内存。为了继续运行，新的分配将被执行，工作负载转移到另一个有空闲资源的 Worker 上，也就是应用 (B) 已有执行器正在运行的那个 Worker。

第二种情况，如果驱动程序 (A) 丢失了一个 Worker，那么其配置文件所需的所有核心或内存（8 核心 x 16GB 内存）就无法在集群中得到满足。这将导致应用 (A) 以降级状态运行，但至少它仍在运行。

谈到计算资源的调度，一个很好的经验法则是为集群预留一些扩展空间。你可以通过以下方式实现：当集群资源调度超过 75% 时添加新节点；采用自动扩缩组或基于云的规则（如 Amazon Elastic Kubernetes Service 中的托管节点组）；或者更改执行器设置，使每个执行器能够适应不同的配置。例如，将配置改为 4 核 x 8GB，同时保持 16 个最大核心数不变，这样驱动程序 (A) 就会分布在更多 JVM 上，从而在丢失一个 Worker 时仍能正常运行，而不会进入降级状态。

只要有足够的资源来应对故障或扩展，动态分配过程就能实现更具弹性的伸缩。接下来，你将学习如何控制这个过程。

### 注意

下一章将介绍 Kubernetes (k8s) 资源管理器，并学习如何以弹性可伸缩的方式启动 Spark 应用，这同样会用到动态分配。动态分配过程为 Spark 应用程序提供了一种协议，用于根据当前处理的数据量的起伏来请求或归还额外的资源。

## 使用动态资源分配弹性伸缩 Spark 应用

Spark 应用的处理需求会动态变化，这些需求由执行器级别的资源（`cores`/`memory`/`GPUs`）组成，并会随应用生命周期而改变。为了管理并行运行的众多应用的不同需求，动态分配允许每个 Spark 应用选择启用该功能，并定义适当的设置来控制如何释放和重新分配额外资源。表 14-2 列出了常见的 Spark 配置。

**表 14-2.** 使用动态分配为 Spark 应用启用弹性伸缩

| 属性名称 | 默认值 | 功能说明 |
| --- | --- | --- |
| `spark.dynamicAllocation.enabled` | `false` | 按条件选择是否启用动态资源分配。 |
| `spark.shuffle.service.enabled` | `false` | 要将执行器释放回 Spark 集群，Spark Worker 必须运行*外部 shuffle 服务*，这是动态分配的前提条件。即使执行器仍持有 Spark 应用所需的部分 shuffle（数据），此服务也允许将其释放。 |
| `spark.dynamicAllocation.minExecutors` | `0` | 建议将此值设置为 `1` 或更高，以确保您的应用在等待可用 Worker 分配资源时不会因资源匮乏而停滞。 |
| `spark.dynamicAllocation.maxExecutors` | `INF` | 设置此值可以阻止应用请求集群中所有（可用的）执行器。 |
| `spark.dynamicAllocation.schedulerBacklogTimeout` | `1s` | 当有任务挂起时，驱动程序将等待此时间长度，之后才向主节点请求新的执行器。 |

动态分配实现了对弹性伸缩工作负载的自动化管理，能够根据需求按需从集群请求和归还资源。根据作业（或应用）的优先级或预期表现，动态分配可以有效地管理跨共享计算集群运行多个共置（多租户）工作负载的成本。然而，在启用动态分配时，需要考虑每个应用的不同（动态）需求以及工作负载的优先级，因为很容易出现许多应用处于空闲等待状态，等待集群资源变得可用的情况。

对于 Spark Structured Streaming 应用，每个微批次往往以更频繁的方式处理更少量的数据，这与典型的基于大批次、一次性读取并处理大量数据的应用行为不同。是的，您也可以运行使用 `Trigger.Once` 的有状态批处理作业，但这需要仔细考虑应用的语义以及与应用性质相关的服务等级协议或优先级。

鉴于 Structured Streaming 应用始终在线的特性，动态分配和外部 shuffle 服务有时会干扰并损害您关键任务型流式应用的性能。有时，唯一阻碍应用以最高速度运行的是内部应用调度器，它控制着 Spark Driver 应用内运行的内部（作业）的并行度和处理模式。

## 调度模式：FIFO 对比 FAIR

默认情况下，Spark 应用以 *FIFO*（先进先出）调度模式运行。由于 Spark Structured Streaming 应用采用分而治之的策略处理一个或多个数据源的连续（无界）流，这些流通过微批处理范式（产生确定性、容错的数据集）被分块和分解，因此 Spark Driver 应用会努力确保每个微批次内的每个转换阶段，即由 `StreamingQuery`（例如 `spark.readStream...writeStream.start()`）定义的阶段，能够基于定义流式应用任务的 `QueryPlan` 获得优先级。

那么，如果一个应用有多个 `StreamingQuery` 呢？在上一章中，您了解了 `SparkStructuedStreamingApplication` trait，它包含了以下两个方法：`awaitTermination` 和 `awaitAnyTermination`。

```scala
def awaitTermination(query: StreamingQuery): Unit = {
  query.awaitTermination()
}
def awaitAnyTermination(): Unit = {
  sparkSession.streams.awaitAnyTermination()
}
```

在一个基本的 Spark Structured Streaming 应用中，通常只有一个上游源和一个下游汇。因此，对主 `StreamingQuery` 调用 `awaitTermination` 将使 Spark Driver 应用保持运行，直到该 `StreamingQuery` 终止。对于 `FIFO` 模式调度器来说，这种单一应用关注点是完全没问题的，因为只有一个主要关注点。

然而，一旦您决定在方程式中引入第二个流式查询，您在每个微批次中就需要排队等待资源，这似乎不太公平，不是吗？

幸运的是，Spark 内置了一个 `FAIR` 调度器。这种执行模式允许并行共享资源。这使得单个 Spark 应用内的总资源分配更加均衡。

### `FAIR` 模式调度器

您可以通过在驱动程序应用配置中设置调度器模式来选择使用 `FAIR` 调度器。

```
spark.scheduler.mode=FAIR
```

除了设置调度器模式之外，Spark 还要求您的应用使用分配文件定义一个或多个调度器池。

```
spark.scheduler.allocation.file="s3a:///path/to/fair-scheduler.xml"
```

调度器分配文件是一个简单的 XML 文件，用于定义一个或多个命名池，并为相应的池定义 `schedulingMode`、`weight` 和 `minShare` 选项。

- **`schedulingMode`**: 此选项默认为 `FIFO`，但也可以设置为 `FAIR`。`FIFO` 会优先处理池中第一个被调度的工作负载，让额外的工作在队列中堆积。`FAIR` 会将工作均匀地分配到池资源中。

- **`weight`**: 池权重声明了在特定池上进行调度的优先级。此优先级默认为 `1`，可以设置得更高以创建用于调度的优先级池（类似于优先级队列）。

- **`minShare`**: 设置给定池的核心分配数量。调度器将首先尝试分配预定义数量的核心，以确保每个池在将任何额外的 CPU 核心资源分配到其余已定义的池之前，都拥有其应有的核心数量。

```xml
<allocations>
  <pool name="FAIR">
    <schedulingMode>FAIR</schedulingMode>
    <weight>2</weight>
    <minShare>2</minShare>
  </pool>
  <pool name="FIFO">
    <schedulingMode>FIFO</schedulingMode>
    <weight>1</weight>
    <minShare>1</minShare>
  </pool>
</allocations>
```

`代码清单 14-3.` `fair-scheduler.xml` 分配文件示例

代码清单 14-3 中的示例配置旨在进一步定义调度模式在各调度器池中的工作方式。由于驱动程序应用使用 `SparkContext` 调度工作，`FAIR` 调度器可用于修改每个 `SparkSession`（重用同一 Spark 上下文）的行为，以管理和共享为 Spark 应用分配的公共集群资源池。

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
