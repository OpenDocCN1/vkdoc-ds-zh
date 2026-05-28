# 优化 YARN 执行

YARN（另一种资源协调器）是 Apache Hadoop 最新的资源管理框架。这本质上是 MapReduce 版本 2，具有许多新功能，例如（为 Mapper 和 Reducer）动态内存分配。YARN 的主要组件如下：

- `ResourceManager`：主守护进程，与客户端（请求方）通信，跟踪集群上的资源可用性，并通过向 NodeManager 分配任务来协调工作。
- `NodeManager`：这是一个工作守护进程，负责启动和管理在工作节点上执行的进程。
- `ApplicationMaster`：管理由 `NodeManager` 在 `Container` 内生成的、由 `ResourceManager` 请求的任务（或进程）。
- `Container`：表示在工作节点上预留资源（CPU/RAM）的请求。例如，MapReduce 是在 YARN 内运行的应用程序，`ApplicationMaster` 会生成 Mapper 和 Reducer 在 `Container` 内运行（并根据需要向 `ResourceManager` 请求额外的容器）。

如图 10-1 所示，当客户端进程向 `ResourceManager` 请求资源时，它会定位一个具有可用资源的节点，并向（该节点上的）`NodeManager` 请求这些资源。`NodeManager` 随后会生成一个 `Container`，并随之调用 `ApplicationMaster`。`ApplicationMaster` 管理应用程序（客户端进程）的资源，并（如果需要）向 `ResourceManager` 请求额外的资源。

图 10-1 展示了 YARN 架构的详细信息。

**工作节点**

**ResourceManager**

持有集群的总 CPU + RAM

**NodeManager**

请求

**Container**

**Container**

向 NodeManager 请求资源

20 个 vcore

生成一个容器

应用程序

20 个 vcore

和

+ 10 GB

+ 10 GB

进程

ApplicationMaster

生成

RAM

RAM

请求

额外容器

资源

根据需要

**ApplicationMaster**

20 个 vcore

20 个 vcore

ApplicationMaster

+ 10 GB

+ 10 GB

如果需要，请求额外资源

RAM

RAM

***图 10-1.** YARN 架构*

请注意以下几点：

- Hadoop 集群每个节点的总内存由属性 `yarn.nodemanager.resource.memory-mb` 决定。
- `ResourceManager` 可以分配给 `ApplicationMaster` 容器的最大内存由属性 `yarn.scheduler.maximum-allocation-mb` 决定。默认的最小分配为 1 GB，但可以使用属性 `yarn.scheduler.minimum-allocation-mb` 进行更改。
- `ApplicationMaster` 只能以属性 `yarn.scheduler.minimum-allocation-mb` 值的增量向 `ResourceManager` 请求资源，且不能超过属性 `yarn.scheduler.maximum-allocation-mb` 的值。
- 对于 MapReduce（作为应用程序），`ApplicationMaster` 会将 `mapreduce.map.memory.mb` 和 `mapreduce.reduce.memory.mb` 的值向上舍入为 `yarn.scheduler.minimum-allocation-mb` 值的倍数。

因此，总结一下，以下是一些您可以调整的 YARN 属性：

- `yarn.scheduler.minimum-allocation-mb`：YARN 允许运行作业的最小容器大小（默认为 1 GB）。
- `yarn.scheduler.maximum-allocation-mb`：YARN 允许运行作业的最大容器大小（默认为 8192m）。
- `yarn.nodemanager.resource.memory-mb`：工作节点上容器的总内存量。该值应为：（总内存）–（操作系统、Hadoop 守护进程和任何其他服务的内存分配）。
- `yarn.nodemanager.vmem-pmem-ratio`：定义虚拟内存与可用物理内存的比率。默认值 2.1 表示虚拟内存将是物理内存大小的两倍。
- `yarn.app.mapreduce.am.resource.mb`：分配给 `ApplicationMaster` 的内存。
- `yarn.app.mapreduce.am.command-opts`：分配给 `ApplicationMaster` 的堆大小（默认为 1 GB）。
- `yarn.nodemanager.resource.cpu-vcores`：节点管理器可以分配给容器的核心数。该值应为：（节点上的总核心数）–（分配给 Hadoop 守护进程和任何其他守护进程的核心数）。

至此，我关于 YARN 相关调优和重要参数的讨论就结束了。接下来，我将探讨如何优化与 NoSQL 解决方案一起使用的数据模型，以及如何为您环境选择合适的 NoSQL 解决方案。

## 选择最佳文件格式

理解您选择用于存储数据的文件格式会直接影响对该数据的查询性能，这一点非常重要。在选择合适的格式时，需要考虑许多参数。您计划执行的查询类型当然是最重要的。下一个是您需要的压缩量。并且您需要确保计划使用的 NoSQL 解决方案支持该存储格式。幸运的是，大多数 NoSQL 解决方案都使得在不同格式之间转换数据变得容易。

文件格式会影响查询性能，因为它决定了系统在响应特定查询时读取数据集的快慢。数据检索速度是查询性能的最大决定因素。目前，数据检索的主要（也是最耗时的）任务是将数据从磁盘移动到内存，反之亦然（除非你使用内存数据库）。例如，如果你的数据集为 1TB，并且你使用接口为 SATA 6 GB/s 的 7200 RPM 硬盘，由于磁盘读取速率接近 160 MB/s，处理 1TB 数据大约需要两小时。对于更大的数据集，类似的性能将无法接受。

如何提高读取性能？有三种方法。首先，你可以通过利用分布式存储和计算技术来并行化读写操作。这将帮助你分割数据并将其分布在多个节点上，同时查询也会相应分布。`HDFS` 提供分布式存储（具备容错性），而像 `MapReduce`、`YARN`（或 `Spark`）这样的处理框架则帮助你实现“将处理移至数据”或在各个工作节点本地处理数据。这构成了在集群的所有节点上优化分布处理，从而以最佳性能完成任务（或作业）。

其次，你可以通过将数据存储在高效使用压缩的文件格式中来减少查询引擎处理的数据总量。文件格式支持不同的压缩算法，并以多种方式将这些算法应用于数据。请注意，压缩算法之间的权衡在于速度与压缩比。更高的压缩比会花费更多时间（同时消耗更多 CPU 资源）来进行压缩或解压缩。当然，你的文件会更小，从而节省磁盘空间。

使用各种文件格式的压缩方案能实现多大的压缩比？这取决于文件格式与压缩方案的组合。例如，对于大约 1GB 的文本（`CSV`）文件，`Snappy` 或 `GZIP` 可以将其压缩至约 500MB（如果存储为 `SequenceFile`）或约 300MB（如果使用 `ORCFile` 或 `Parquet` 格式）。表 10-1 列出了适用于各种行导向和列导向文件格式的压缩方案。

### 表 10-1. 文件格式及其支持的压缩方案

| **压缩方案** | **文件格式**   | **Snappy** | **GZIP** | **ZLIB** | **BZIP2** | **None** |
|--------------|----------------|------------|----------|----------|-----------|----------|
|              | `TextFile`     |            |          |          |           | X        |
|              | `SequenceFile` | X          | X        | X        |           |          |
|              | `ORCFile`      | X          | X        |          |           |          |
|              | `RCFile`       |            | X        | X        | X         |          |
|              | `Parquet`      | X          | X        |          |           |          |

提高读取性能的第三种方法是使用预存储的聚合统计信息。这仅受`Parquet`和`ORC`等格式支持，它们可以获取基于最小值/最大值和空值计数的聚合统计信息作为预计算数据。此外，`Parquet`和`ORCFile`格式以牺牲写入性能为代价来优化读取性能（这遵循了 Hadoop“一次写入，多次读取”的理念）。

最后，数据的组织方式会影响各种类型查询的性能。

例如，当数据以行导向格式存储时，返回大量列（针对行子集）的查询速度更快。根据经验，返回超过 60%列的查询受益于行导向格式。或者，如果数据以列导向格式存储，则那些跨表（或其子集）的所有行对少数列进行汇总或聚合的查询性能更佳。因此，如果你期望大多数查询的形式为`select * from MyTable;`，则应使用行导向格式。

但如果你的查询形式为`select sum(MyColumn) from MyTable`（即，你只对数据集中的少数列执行聚合或生成汇总统计），那么你应该使用列导向格式。接下来，我将讨论一些行格式和列格式及其属性。

## 基于行的格式