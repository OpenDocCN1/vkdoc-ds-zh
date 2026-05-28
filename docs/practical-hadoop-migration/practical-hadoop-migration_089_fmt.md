# Acme 数据基础设施分析

Acme 的架构师和技术专家们决定用现代闪存和基于内存的架构替换整个数据基础设施，并与 Acme 数据管道开发者进行了深入探讨。他们意识到，必须用这些崭新、陌生且尚未成熟的技术重写过去五年的数据分析工作。仅对数据从业者进行再培训就需要数年时间。于是，他们决定对数据基础设施进行渐进式的、零散升级，从当前的 `Lambda` 架构转向蝴蝶架构，并使用 `Ampool` 以内存为中心的存储方案。

## 数据

Acme 数据分析管道中精确使用四个大型数据集：用户画像、广告、内容元数据和广告投放日志。

### 用户画像

用户画像包含每个注册用户的详细信息。用户画像的模式如下：

-   `UserID`：`UUID`
-   `Age`：`0..255`
-   `Sex`：`M/F/Unknown`
-   `Location`：`Lat-Long`
-   `Registration timestamp`：`TS`
-   `Interests`：`(topic:subtopic:keyword)` 的逗号分隔列表

### 广告

该数据集包含所有可用于在内容中展示的广告的详细信息。该数据集的模式如下：

-   `AdID`：`UUID`
-   `CampaignID`：`UUID`
-   `CustomerID`：`UUID`
-   `AdType`：`{banner, modal, search, video}`
-   `AdPlatform`：`{web, mobile}`
-   `Keywords`：`(topic:subtopic:keyword)` 的逗号分隔列表
-   `PPC`：`$ per click`
-   `PPM`：`$ per 1000 display`
-   `PPB`：`$ per conversion`

### 内容元数据

内容数据集包含有关内容的所有元数据。该数据集的模式如下：

-   `ContentID`：`UUID`
-   `ContentType`：`{news, video, audio, photo}`
-   `Keywords`：`(topic:subtopic:keyword)` 的逗号分隔列表

### 广告投放日志

该数据集从广告服务器持续流式传输。此日志中的每个条目都包含以下字段，其中一些可能为空：

-   `TimeStamp`：`TS`
-   `IPAddress`：`IPv4/IPv6`
-   `UserID`：`UUID`
-   `AdID`：`UUID`
-   `ContentID`：`UUID`
-   `AdType`：`{banner, modal, search, video}`
-   `AdPlatform`：`{web, mobile}`
-   `EventType`：`{View, Click, Conversion}`

## 计算

在 Acme 的广告分析数据管道中，对数据执行以下计算步骤。

### 数据摄取与流式分析

广告服务器将广告点击、浏览和转化事件生成到 `Kafka` 代理。`Kafka` 消费者嵌入在 `Apache Apex (DataTorrent)` 流式分析平台中。对于每条消费的事件，执行以下计算：

1.  解析事件记录
2.  提取时间戳、广告 ID、事件类型和广告类型
3.  根据广告 ID 查找活动 ID
4.  对每个广告 ID 和活动 ID 的事件类型进行窗口聚合
5.  将这些聚合结果存储到 `Ampool` 中
6.  在 `DataTorrent` 的流式可视化仪表板中，为某个活动 ID 及其下的所有广告可视化这些聚合结果
7.  流式分析的输出如下：
    -   `(Ad-ID, Time-Window, Number-Of-Views, Number-Of-Clicks, Number-Of-Conversions, Total-PPV, Total, PPC, Total PPConversion)`
    -   `(Campaign-ID, Time Window, Number-Of-Views, Number-Of-Clicks, Number-Of-Conversions, Total-PPV, Total, PPC, Total PPConversion)`

第二个流式摄取管道负责保持用户表、广告表和内容表及时更新。在广告被展示、点击和转化的同时，有新用户注册，现有用户信息被更新。新的广告活动被创建，现有活动被修改，新广告被创建，现有广告被更新。

这些插入和更新是同时进行的，而非周期性的。在第二个管道中，我们使用 `Kafka` 消费者获取插入和更新记录，并将这些插入和更新实时应用于 `Ampool` 中的相应表。该管道的步骤如下：

1.  从 `Kafka` 代理摄取一个 `{user, campaign, ad}` `{update, insert}` 事件。
2.  解析事件以确定需要更新哪个表。
3.  更新相应的表。
4.  跟踪总的更新次数。
5.  当有 1% 的记录是新增或已更新时，启动批处理计算并重置更新计数器。

### 批量模型构建

在此面向批处理的计算中，我们构建广告定向模型。该管道的输入是用户表、广告表和内容表。该管道的输出是两个新表：

1.  `(User-ID, Ad-Id1, weight1, Ad-Id2, weight2, Ad-Id3, weight3)`
2.  `(Content-ID, Ad-Id1, weight1, Ad-Id2, weight2, Ad-Id3, weight3)`

这些表代表了每个用户和所有内容的最相关的三个广告。这些表随后用于广告投放系统，以便当用户访问特定内容时，系统会根据在用户表和内容表中的各一次查找，从这些广告中选择最佳匹配项。

广告与用户或内容的相关性由关键词、主题和子主题列表的余弦相似度决定。这个使用 `CDAP` 构建的批量模型构建管道包含以下步骤：

1.  一个 `MapReduce` 作业，用于从用户表和广告表中提取相关字段，并根据主题、子主题和关键词进行连接。
2.  一个 `Spark` 作业，用于筛选出前三个匹配的关键词，并使用余弦相似度计算这些关键词对应的广告权重。
3.  对内容表和广告表重复上述步骤。

### 交互式和临时 SQL 查询

使用 `Apache Trafodion (EsgynDB)` 对活动和广告的不同窗口聚合结果执行交互式和临时查询。以下是一些查询示例：

1.  某个广告的`{每分钟、每小时、每日}`转化率是多少？某个活动呢？
2.  对于某个活动，每小时有多少广告被点击（占浏览量的百分比）？
3.  某个活动全天应付给 `Acme.io` 多少钱？
4.  每小时点击量最高的广告和活动是哪些？
5.  `Acme` 有多少年龄在 `0–21`、`21–40` 的男性用户？

这些查询的结果可以显示在屏幕上（交互式），对于产生时间窗口数据的查询，可以使用 `Tableau` 等工具进行可视化。

### 总结

几年前，当 Nathan Marz 首次提出 `Lambda` 架构时，大数据社区一片欢腾，因为当时还没有使用 `Hadoop` 实时处理流式数据的方案。`Hadoop` 和 `NoSQL` 的角色仅限于 ETL 或最多是批处理。`Lambda` 的引入改变了这一切。它首次证明了 `Hadoop` 在实时处理方面也有用武之地。

然而，随着兴奋感消退，这种架构的问题很快显现出来。`Lambda` 是一个不错的起点，但你需要根据自身环境进行调整。在大多数情况下，使用 `Lambda` 也会限制你对 `Hadoop` 生态系统组件的选择。Nathan 通过为每个与 `Lambda` 相关的小任务开发 `Java` 代码来演示他的概念，但这并不实用。如果你打算利用环境中的各种生产系统来构建数据湖（我认为这是 `Lambda` 最主要的应用场景），那么为这些系统开发基于事实的模型将是一项艰巨且耗时的任务。我已经通过一个实际示例演示了该过程。这应该能让你对所需的工作量有清晰的认识。

那么，你可以在哪里使用 `Lambda`？它真的有实际用途吗？您可以遵循