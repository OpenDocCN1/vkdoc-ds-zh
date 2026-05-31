# Blueprints

正如其名，`Blueprints` 是一个属性图模型接口，并提供了相应的实现。实现了 `Blueprints` 接口的数据库会自动支持启用 `Blueprints` 的应用。

可以将 `Blueprints` 视为图数据库的 JDBC（Java 数据库连接）或 JPA（Java 持久化 API） API。大多数图数据库都实现了 `Blueprints`。图 7-4 展示了如何将 `Blueprints` 与前面提到的 TinkerPop 组件结合起来的可视化表示。

![A978-1-4842-0142-8_7_Fig4_HTML.jpg](img/A978-1-4842-0142-8_7_Fig4_HTML.jpg)

**图 7-4.** TinkerPop Blueprints 实现

至此，我们已经涵盖了 TinkerPop 框架及其组件，以及其他与图相关的概念。下一个问题是，是否存在可以被视为图数据库的基于图的解决方案。在下一节中，我们将回答这些问题。

## 图即数据库

与传统的 RDBMS 相比，NoSQL 数据库更少关注模式，更多关注非规范化的数据形式。但图数据库提供了通过边定义节点之间关系的灵活性，这就是为什么可以很容易地根据 RDBMS 概念来理解它们。使用 RDBMS 构建类似图的查询当然是可能的，但如前所述，其用途非常有限。对于非图数据库，运行基于图的查询以进行遍历或构建图结构的能力不受支持，并且构建起来可能很繁琐。由于固有的图数据结构支持，图数据库将比传统的 RDBMS 更具优势。

RDBMS 和图数据库之间的一些区别如下：

*   图数据库无需索引查找，因为节点或顶点知道它们所连接的属性（例如边），而 RDBMS 则需要依赖索引机制。
*   通过边相互连接的两个顶点可能在属性上有所不同，并且可以动态演变，但 RDBMS 强加了一套固定的模式。
*   在图数据库中，两个顶点之间的关系存储在记录级别，而在 RDBMS 中，它是在表级别定义的。

我们需要记住一件事，当前的应用开发时代是为特定需求使用特定技术的时代，这非常适合构建多语言或混合解决方案。这意味着，当你的需求最适合通过运行类似图的查询来满足，并且你的要求倾向于更快的基于图的模型时，答案很简单：使用图数据库。图数据库使用节点和边及其属性来构建数据并将其存储在图结构中。图数据库中的每个元素都有一个对相邻节点的引用，这意味着不需要索引查找。图 7-4 展示了一个图数据库的示例，它将 Twitter 用户存储为节点，将其关注者存储为边。每个节点包含 `fname`、`id`、`lname` 和 `role` 作为属性，而每条边都有一个属性来表示用户成为其相邻节点（即用户）关注者的日期。

![A978-1-4842-0142-8_7_Fig5_HTML.jpg](img/A978-1-4842-0142-8_7_Fig5_HTML.jpg)

**图 7-5.** 存储 Twitter 用户、其属性和关注者的图数据库

图 7-5 展示了用户 `mevivs`、`chris_n`、`apress_team` 和 `melissa_m` 的 Twitter 连接和关注者图。这里，顶点 `apress_team` 被顶点 `mevivs` 和 `melissa_m` 关注。另一方面，在 `chris_n` 和 `apress_team` 之间存在一个传递关系/遍历，`chris_n` 关注了 `mevivs`，`mevivs` 关注了 `apress_team`，而 `apress_team` 又关注了 `chris_n`。在“Gremlin Shell”和“用例”部分，我们将引用相同的 Twitter 示例来探索示例练习中的命令行工具和 Java API。在考虑此类传递性图查询时，值得讨论的一点是图数据库处理此类查询的方式与 SQL 查询不同。使用 SQL 处理此类传递性查询并不直接，需要执行复杂的连接、并集或交集。但是，使用图数据库处理此类传递性查询要容易得多，只需要根据边的属性跟踪边以进行传入和传出的数据查询即可。“用例”部分将讨论这些图遍历。

让我们来谈谈可用的图数据库。一些流行的图数据库包括：

*   Neo4J
*   OrientDB
*   Objectivity 的 InfiniteGraph
*   Titan

由于本章的目的是详细讨论 Cassandra 和 Titan 图数据库，因此我们只讨论此处提到的其他数据库提供的重要功能。

### Neo4J

Neo4J ([www.neo4j.org/](http://www.neo4j.org/)) 是一个基于 GPU（通用公共许可证）许可的开源数据库。它由 Neo Technology Inc. 开发。它以节点的形式存储数据，节点通过有向边连接。它提供的一些重要功能包括：

*   可扩展且高可用的数据
*   完全的 ACID 事务支持
*   基于 REST 的访问
*   支持 `Cypher` 和 `Gremlin` 图查询语言

### OrientDB

OrientDB ([www.orientechnologies.com/orientdb/](http://www.orientechnologies.com/orientdb/)) 是一个基于 Apache 2 许可的 NoSQL 图数据库。它由 Orient Technologies 的 Luca Garulli 管理和开发。OrientDB 提供的一些重要功能包括：

*   基于 REST 的访问
*   完全的 ACID 事务支持
*   用于查询支持的类似 SQL 的接口

### InfiniteGraph

InfiniteGraph ([www.infinitegraph.com/](http://www.infinitegraph.com/)) 是由 Objectivity Inc. 构建的企业级分布式图数据库。InfiniteGraph 支持的一些重要功能包括：

*   支持并发性和一致性
*   完全的 ACID 事务支持
*   可视化工具



#### Titan

`Titan`（`thinkaurelius.github.io/titan/`）是一个基于 Apache 许可的可扩展图数据库，用于以节点和边的形式存储大量数据。它支持 `Cassandra` 作为后端存储。其一些重要特性包括：

- 通过 `Lucene`/`ES` 支持全文搜索和地理空间查询
- 支持 `ACID`
- 最终一致性和中间一致性
- 支持多种数据库，适用于多语言图应用
- 支持 `Gremlin` 和 `Cypher`

`Titan` 及其各个组件将在后续章节中讨论。

## Titan 图数据库

`Titan` 是一个事务性图数据库，允许数千名并发用户实时执行复杂的图遍历查询。它还通过 `Hadoop` 集成提供了图数据分析、报表和 `ETL` 支持。它内建了对 `Elasticsearch` 和 `Lucene` 的支持，用于地理空间查询和全文搜索。它还原生支持 `Blueprint` `TinkerPop` 图栈。

图 7-6 展示了 `Titan` 生态系统的图形化表示。

![A978-1-4842-0142-8_7_Fig6_HTML.jpg](img/A978-1-4842-0142-8_7_Fig6_HTML.jpg)

图 7-6. Titan 生态系统

### 基本概念

在本节中，我们将介绍一些对理解 `Titan` 图数据库至关重要的基本概念。

#### 以顶点为中心的索引

以顶点为中心的索引特定于某个顶点。大多数对引用或非引用顶点的遍历都是通过边及其属性进行的。对这些属性或边标签进行索引可以避免性能开销。这类索引也可称为局部索引。以顶点为中心的索引的目的是根据边的属性和标签对顶点的邻接边进行排序和索引。

`Titan` 还支持 `Elasticsearch`，它可以以独立模式或内嵌模式与 `Titan` 一起运行。使用 `Elasticsearch` 还可以执行全文搜索、地理空间查询和数值范围查询。`Elasticsearch` 也允许我们对未索引的属性进行查询。

#### 边压缩

通过边压缩，`Titan` 可以存储压缩后的元数据并保持较低的内存占用。它还可以将所有边的标签和属性存储在同一数据块中，以实现更快的检索。

#### 图分区

这是底层数据库最为关键的地方。在 `Cassandra` 中，我们知道某一行数据会存储在单个 `Cassandra` 节点上。`Titan` 理解，通过按顶点 ID 排序键，它可以有效地对图数据进行分区并分布到 `Cassandra` 节点上。顶点 ID 是一个 64 位的唯一值。

默认情况下，`Titan` 采用随机分区策略，将顶点随机分配给节点。随机分区效率高，且能使集群保持平衡，但如果采用多种后端存储来存储图，则会导致性能问题，并需要在实例之间进行跨数据库通信。使用 `Titan`，我们还可以配置显式分区。通过显式分区，我们可以控制并将遍历的子图存储在同一节点上。

我们可以按如下方式在 `Titan` 中启用显式分区：

```
cluster.partition = true
cluster.max-partitions = 16
ids.flush = false
```

这里`cluster.max-partitions`是每个集群的最大分区数。同时，我们还需要禁用刷新 ID。

当使用 `Cassandra` 作为存储后端选项时，必须使用 `ByteOrderedPartitioner` 进行配置。

`Titan` 将顶点及其邻接边和属性存储为一个集合。这种机制称为“数据并置”。通过这种机制，连接到某个顶点的边及其属性将被存储在一起。每一行将包含一个顶点 ID 作为键，邻接边和属性作为单元格。这种数据表示格式在所有支持的数据库中都是通用的。

图 7-7 和 7-8 展示了 `Titan` 的数据布局和边存储机制。（图片来源于 `https://github.com/thinkaurelius/titan/wiki/Titan-Data-Model` 上的 `Titan` 维基页面，并依据 Apache 许可证 2.0 版本重复使用，许可证地址为 `http://www.apache.org/licenses/LICENSE-2.0`。）

![A978-1-4842-0142-8_7_Fig8_HTML.jpg](img/A978-1-4842-0142-8_7_Fig8_HTML.jpg)

图 7-8. Titan 的边存储机制

![A978-1-4842-0142-8_7_Fig7_HTML.jpg](img/A978-1-4842-0142-8_7_Fig7_HTML.jpg)

图 7-7. Titan 的数据布局机制

底层数据存储会将每个顶点及其邻接边和属性存储为单行数据。此外，这些行将按顶点 ID 排序，单元格将按属性和边键排序。动态单元格可以在运行时添加。数据并置非常重要，这就是为什么将顶点和邻接边存储为单行有助于实现高可用性。

#### 后端存储

`Titan` 的存储架构是完全解耦的，支持多种 NoSQL 数据库，包括：

- `Cassandra`
- `HBase`
- `BerkeleyDB`
- `Persistit`

支持多种 NoSQL 数据存储允许根据应用需求选择合适的技术。换句话说，您可以根据特定需求选择特定的技术。基于 CAP 定理，我们可以选择任何一种支持的数据库。

使用 `Titan`，我们可以通过 `storage.backend` 选项动态配置后端存储。本章中的示例将介绍如何结合 `Cassandra` 使用此选项。

#### 事务处理

`Titan` 是一个事务性图数据库，因此每个读/写操作都将在事务边界内发生。

以下代码片段展示了 `Titan` 如何将顶点 `mevivs` 包裹在事务边界内并提交它：

```
TitanGraph g = TitanFactory.open("/home/vivek/Titan");
Vertex mevivs = g.addVertex(null); //隐式包裹在事务中
mevivs.setProperty("fname", "vivek");
g.commit(); //提交事务
```

在处理非常大量数据和多语言场景时，可能会发生永久性或临时性故障。这里的临时性故障包括网络故障、节点无响应等类似情况。在这种情况下，我们可以像这样配置 `Titan` 的重试延迟属性：

```
Configuration conf = new BaseConfiguration();
conf.setProperty("storage.attempt-wait", 250); // 时间单位为毫秒
```

此类临时性故障可以通过重试来处理，但永久性故障（例如硬件故障）则需要用户显式处理 `TitanException`：

```
try
{
    TitanGraph g = TitanFactory.open("/home/vivek/Titan");
    Vertex mevivs = g.addVertex(null); //隐式包裹在事务中
    mevivs.setProperty("fname", "vivek");
    g.commit(); //提交事务
} catch (TitanException e) {
    //配置显式重试或快速失败场景。
}
```

以上是 `Titan` 的基本概念及其架构。接下来，我们将介绍 `Titan` 图数据库的搭建和安装。

### 安装与搭建

我们可以从 `https://github.com/thinkaurelius/titan/wiki/Downloads` 下载最新的 `Titan` 发行版。写作时的最新版本是 0.5.0。下载发行版后，将其解压到本地文件夹。在本章中，我们将在多处引用 `TITAN_HOME` 变量。我们可以按如下方式设置它：

```
export TITAN_HOME=/home/vivek/software/titan
```

### 命令行工具与客户端

完成搭建和安装后，首先想到的问题是：是否有任何命令行客户端？就像 `Cassandra` 有 `CQL` `shell` 一样，`Titan` 是否也提供了用于服务端脚本编写和快速分析的选项？本节将探讨 `Gremlin` `shell` 和 `Rexster` 这两个命令行选项。



