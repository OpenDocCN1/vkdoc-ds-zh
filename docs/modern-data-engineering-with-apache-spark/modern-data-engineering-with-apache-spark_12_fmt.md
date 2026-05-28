# 6. 数据发现与 Spark SQL 目录

能够连接并处理大多数公司现代技术栈中包含的数据系统和服务，是数据工程师的一项关键技能。幸运的是，Spark 提供了处理和转换数据的机制，使您能够采取行动并解决问题，而无需编写和维护另一套自定义基础设施代码。通过依赖 Spark 的核心能力，您学会了利用 `JDBC` 的强大功能与传统数据库中存储的数据进行互操作。访问任何兼容 `JDBC` 的 `RDBMS`，使您能够一次性编写 `SQL` 查询，这意味着您无需为支持具有不同业务逻辑的独立应用程序而烦恼。

本章延续了第 5 章的学习内容。这将是我们首次探讨如何结合使用数据湖和关系数据库来填充 `Spark SQL` 目录的内容。此过程使您能够为数据源添加丰富的元数据注释，这些元数据描述了 `Spark` 应用程序生成的表。这些上下文信息详细说明了数据管道的输入和输出格式，还可以扩展用于自动生成数据血缘图，或用作内部乃至外部数据目录的通用数据可发现性服务。这不仅对您和您的团队至关重要，对数据组织中任何希望重用或分析 `Spark` 作业输出数据的其他成员也同样宝贵。

在本章的最后，我们将通过扩展之前的练习，使用 `Spark` 的强类型数据集，来了解 `Spark` 如何优化您的转换流程。这个最终练习将向您介绍 `Spark` 如何编码应用程序代码的意图，并通过内部的 `Catalyst` 优化器，神奇地生成并优化从开始到结束运行应用程序所需的逻辑执行阶段。

本章分为以下几个部分：

- `Hive Metastore` 与 `Spark SQL` 目录
- `Catalyst` 优化器
- `Spark` 数据集

## 练习内容

您可以在本书 `GitHub` 仓库的 `ch-06/` 文件夹中找到本章所需的材料，地址为 [`https://github.com/newfront/spark-moderndataengineering`](https://github.com/newfront/spark-moderndataengineering)。只需使用运行脚本启动环境，然后访问 `http://localhost:8080` 即可。

```
cd ch-06/docker && ./run.sh start
```

## 数据发现与数据目录

在上一章中，我们探讨了在 `Apache Spark` 中通过 `JDBC` 连接数据库（`RDBMS`）是多么容易。虽然我们能够连接到为练习而启动的数据库，但有一个主要问题可能会在日后造成麻烦。这个障碍具体是：如果事先不了解数据库的位置、数据库中物理表所代表的数据类型，以及这些数据在底层表模式中的表示方式，那么简单的数据发现行为几乎就不存在。然而，数据发现实际上是数据驱动型企业成功的关键，作为数据工程师，这确实应该引起您的重视。

### 数据目录为何重要

数据发现可能看起来不是我们当前需要立即关注的问题，因为本书上下文中我们正在处理的数据，仅代表一家虚构公司初始客户数据库中的少量客户、少量咖啡品牌及一些基本元数据（以浓度表示的烘焙程度）、少量门店以及白天每家门店客流量的部分信息。然而，在现实世界中，数据发现问题是一个更为重大的关切。让我为您描绘一下这个场景。

### 数据的一厢情愿

随着任何公司的发展，新员工的加入自然会导致小团队扩张。随着团队持续壮大，在某个节点上，团队自然会发生分叉，再次拆分成更小的团队，并拥有更具体的职责范围和关注领域。由原团队成员组成的新团队，会随着公司的有机增长而继续这种成长与分裂的循环。在一段时间内，团队可以依赖部落知识，或者依赖在公司长期积累但文档记录不足的流程，而跨团队沟通使得团队能够直接分享部落知识、数据位置、表结构和原始数据格式。

出于某种原因，许多初创公司，甚至一些非常成熟的公司，都陷入了这样一种想法：在云时代，团队能够更加敏捷、快速行动，甚至打破常规，以跟上令人目眩的创新步伐。这样一来，工程师和产品经理很容易跳过或忽视详尽文档和软件测试这一步骤。这种做法不言而喻的副作用是，技术债务很容易堆积，成为难以启齿的隐患。这是为了公司持续向前发展而必须承担的风险！不幸的是，历史往往会重演，这种高额的技术债务可能导致流程和模式迅速变得不可持续。

随着公司发展壮大、收购新公司、工程师更替、部门重组，以及其他跨团队/跨项目数据平台的出现，过去依赖部落知识，或者期望团队等待核心团队成员回复 `Slack` 消息、加入视频会议，甚至亲自到场，来引导新团队了解过时流程的做法，会变得对公司及其员工的福祉有害。在处理数据时，清晰的文档和已建立的跨职能工作方式显得尤为重要。

对于数据团队而言，依赖手动沟通数据模式变更，或者玩那种古老的“传话游戏”（你知道的，鲍勃告诉爱丽丝，爱丽丝又告诉我，`X` 团队正在更改他们的数据格式），会带来巨大的成本和进展阻碍。不可靠的流程在压力下会迅速崩溃，其副作用是，将原本核心、可靠的数据变成了不可靠、不可信的数据。对某个数据库表的模式，或数据湖中记录的原始格式的变更沟通不足，将导致生产中断，因为团队可能甚至不知道谁依赖于他们的数据，或者谁会受到上游破坏性变更的影响。中断意味着人们会生气，然后开始互相指责。这是一个转折点，一个分岔路口，也是当公司达到一定规模时，数据平台、集中化流程和其他“做事方式”出现的原因之一。

### 数据目录来救场

幸运的是，在公司内部环境之外，存在着标准的做事方式。就像大多数设计模式一样，人们遵循它们是因为它们可以节省时间，并避免在事情崩溃时遭受打击带来的潜在痛苦。我之前描绘的画面，是当公司内部核心数据层的信任崩溃时可能发生的最坏情况。事实是，数据文档与任何 `API` 文档同等重要。这些基于元数据的数据定义代表并充当了一个集中式契约，规定了如何、何时以及为何消费和使用表中存储的数据。而实现这一目标最广泛使用的标准方法之一，就是使用 `Apache Hive Metastore`。

## Apache Hive Metastore

`Hive Metastore` 最初是为了提供重要的元数据而构建的，这些元数据可用于简化不同团队处理其数据湖中分布式数据的方式。这些伪表定义映射了关键缺失的信息，包括与存储在这些分布式 `SQL` 表中的数据进行互操作所需的模式、分区、编码器和解码器。

这些元数据在运行时被用作粘合剂，使 [Apache Hive](https://cwiki.apache.org/confluence/display/Hive/Home) 能够对存储在 `HDFS` 中的数据运行类似 `SQL` 的分布式查询。回顾第 1 章，我们讨论了分布式文件系统、数据管道和 `ETL` 的出现。曾几何时，对于使用 `MapReduce` 编写和维护 `ETL` 作业的数据工程师来说，`Apache Hive` 简直是天赐之物。

大数据实际上只是一种用于存储、处理和查询大规模分区且完全分布式数据的技术，这些数据无法放入传统的 `SQL` 数据库中。`Hive` 提供了一种机制，让人们可以像对待传统数据库那样对待这些分布式文件系统，即将其视为带有模式的表。这使得数据工程师能够专注于将数据摄入 `HDFS` 的管道、将数据表示为具有代表性数据结构和模式的表，以及将所有这些信息汇总到每个分布式 `Hive` 表的表定义中的数据血缘信息。

这些表元数据存在于 `Hive Metastore` 中，并提供了描述每个 `Hive` 表的谁、什么、哪里和为什么的关键信息。此外，元数据存储使有权限访问它的团队能够发现新表，并找到重用此分布式数据仓库中可用数据的新途径。所有这一切都无需依赖大量的跨团队沟通、冗长的会议和分享部落知识，就能开始高效工作。

### 具有现代风格的元数据

如今，`Hive Metastore` 依然健在且运行良好，但其用途已与最初设计有所不同。现在，典型的做法是运行元数据存储，却从未打算使用 `Hive` 本身。这种变化可以被视为一种演进，它与现代数据技术栈中的其他变化同步发生。基于云的分布式对象存储（如 `Amazon S3` 和 `Azure Blob Storage`）的日益普及，使得行业焦点从 `Hadoop` 和 `HDFS` 转移开来。

类似的迁移也发生在公司身上，它们正在远离许多较老的 `Hadoop` 技术，比如 `Hive` 本身。在第 2 章中，你了解到 `Spark` 是如何从 `MapReduce` 成功的精华中诞生的。它改变了核心执行模型，以在内存中重用计算，并允许对同一数据进行多次迭代，而无需显式返回数据源，这本身就是一次自然的技术演进。

类似于回收利用的概念，生态系统只是重用了 `MapReduce` 的组件，但做了一些改变。`Spark SQL` 和 `Hive` 之间的关系也是如此。`Spark SQL` 完全有能力作为一个功能完备的 `SQL` 引擎运行，并且正如你即将亲身体验的那样，它也可以利用 `Hive Metastore`。虽然它可以与 `Hive` 互操作，但它自己也能运行得很好。

## 练习 6-1：使用 Hive Metastore 增强 Spark SQL

`Hive Metastore` 为描述表的位置、数据编码（例如 `Parquet`、`ORC`）、列式模式以及统计信息提供了单一可信源，旨在实现数据民主化，供数据工程师、分析师、机器学习工程师和数据科学家等人员使用。借助 `Metastore`，人们无需四处询问或大海捞针就能找到数据集！

下一节将引导您完成启动并运行 `Hive Metastore` 所需的五个简单步骤。与上一章的练习类似，您可以使用章节源材料实时运行所有内容。

### 引导选项

`run.sh` 提供了一个引导选项。如果您只想引导此环境而不关心所有曲折细节，我保证我不会介意。标题为“6_1_HiveMetaStore”的 Apache Zeppelin 笔记以 `Bootstrap the Environment` 开头，它将引导您完成命令和设置。您可以跳到 Spark SQL Catalog 部分，稍后再回到本节以获取有关初始化数据库表、用户权限和 Spark 配置的更多信息。

## 配置 Hive Metastore

配置 Hive Metastore 需要几个步骤，让我们从让它在 Spark 上下文中工作开始。幸运的是，这些步骤相当简单：

1.  创建 `metastore` 数据库。
2.  授予您的 MySQL 用户对该数据库的访问权限。
3.  创建数据库表。
4.  配置 Spark 环境以访问 `metastore`。
5.  为 Spark 环境提供 Hive 依赖项。

这些步骤应该不会太多。实际上，Spark 能够运行 Hive Metastore，而无需运行任何额外的服务（或容器）。唯一真正的要求是您已初始化数据库和表，并已授予您的 Spark `dataeng` 用户对数据库的访问权限。

## 创建 Metastore 数据库

为了简化在完成实际 `metastore` 数据库创建后初始化 Hive Metastore 表的过程，表定义 SQL 文件已提供在章节的 `exercise` 目录中。`docker file copy` 命令已包含在 `run.sh` 中。命令行参数 `hiveInit` 将代表您将必要的文件复制到正在运行的 `mysql` 容器中。只需执行清单 6-1 中的命令即可。

```
cd /path/to/ch-06/docker &&
./run.sh start &&
./run.sh hiveInit
```

启动章节练习环境并初始化 Apache Hive Metastore。

`hiveInit` 命令将运行以下两个 Docker `copy` 命令。

```
docker cp hive/install/hive-schema-2.3.0.mysql.sql mysql:/
docker cp hive/install/hive-txn-schema-2.3.0.mysql.sql mysql:/
```

### 连接到 MySQL Docker 容器

使用以下 `docker exec` 命令登录到容器上下文。

```
docker exec -it mysql bash
```

### 以 root 用户身份认证 MySQL 用户

登录到本地 MySQL 容器后，使用 `mysql` shell 以 MySQL root 用户身份进行认证。

```
mysql -u root -p
```

`MYSQL_ROOT_PASSWORD` 值可以在 `ch-06/docker/docker-compose-all.yaml` 中找到。

### 创建 Hive Metastore 数据库

进入 `mysql` shell 后，继续创建 `metastore` 数据库。

```
mysql> CREATE DATABASE `metastore`;
```

现在数据库已创建，是时候授予您的 Spark `mysql` 用户访问权限了。

### 授予对 Metastore 的访问权限

授予 `dataeng` 用户对 `metastore` 数据库的访问权限。

```
REVOKE ALL PRIVILEGES, GRANT OPTION FROM 'dataeng'@'%';
GRANT ALL PRIVILEGES ON `default`.* TO 'dataeng'@'%';
GRANT ALL PRIVILEGES ON `metastore`.* TO 'dataeng'@'%';
FLUSH PRIVILEGES;
```

**提示**：通常的做法是创建特定于用例的用户来管理对 Hive Metastore 数据库的访问。应根据应用程序的访问模式，授予用户对 `metastore` 的读/写或只读访问权限。许多应用程序仅消费数据，并将表数据复制到 Hive Metastore 上下文之外的不同位置。请记住，数据访问是一种特权！

您现在已完成 `root` 用户会话。您应该退出此会话，并以 `dataeng` MySQL 用户身份重新登录。

```
mysql> exit
```

## 创建 Metastore 表

我们即将完成 `metastore` 的设置。接下来要做的是创建底层的 Hive Metastore 表本身。配置 `metastore` 数据库表需要一次性设置成本。幸运的是，我们可以直接将表定义导入到 `mysql` 控制台中。

### 以 dataeng 用户身份认证

在 MySQL 容器上下文中，将您的认证 MySQL 用户更改为 `dataeng`。

```
mysql -u dataeng -p
```

### 切换到 Metastore 数据库

```
mysql> use metastore;
```

### 导入 Hive Metastore 表

`SOURCE` 命令可用于轻松读取和应用适用于我们 Hive 版本（v2.3.0）的 SQL 表配置命令。此文件在 `mysql` Docker 容器中可用，因为您在设置过程的早期使用了 `hiveInit` 命令。

```
mysql> SOURCE /hive-schema-2.3.0.mysql.sql;
```

就是这样。您现在是一个全新安装的 Hive Metastore 的快乐拥有者。如果您想查看在此过程中初始化了哪些表，可以从当前的 MySQL 会话运行以下命令。

```
use metastore;
show tables;
```

运行 `show tables` 命令将产生一长串表名（超过 50 个）。您可以随意使用 `DESCRIBE` 命令查看任何表以获取更多上下文，或者直接退出控制台。现在，您的 `metastore` 已准备好投入使用。接下来，我们将添加 Spark 访问 Hive Metastore 所需的配置。

## 配置 Spark 以使用 Hive Metastore

Hive Metastore 传统上通过 `hive-site.xml` 进行配置。Spark 会自动在 `$SPARK_HOME` 目录下的 `conf` 目录中查找此配置文件。此文件及其他文件已为您预先配置好，并可在 `ch-06/docker/spark/conf/` 目录下的 `exercise` 文件中找到。

如果您现在不想查看配置，可以跳过本节，直接转到 Spark SQL Catalog。

### 配置 Hive Site XML

`hive-site.xml` 文件仅包含四个属性。它们为 Spark 提供了将在您的应用程序中使用的 Hive Metastore 数据库的默认连接属性。这些属性如表 6-1 所示。

**表 6-1** Hive Metastore 配置选项

| 属性 | 描述 |
| --- | --- |
| `javax.jdo.option.ConnectionURL` | 定义到您的 `metastore` 的 JDBC 连接 URL。 |
| `javax.jdo.option.ConnectionDriverName` | 定义用于连接到 `metastore` 的 JDBC 驱动 `ClassName`。 |
| `javax.jdo.option.ConnectionUserName` | 定义 `metastore` 数据库用户名。 |
| `javax.jdo.option.ConnectionPassword` | 定义 `metastore` 用户的密码。 |

官方 Hive 文档中提供了更多配置选项，包括对共享 Hive Metastore 的不同访问模式。这些包括运行完全独立的 Hive Metastore 服务器（v3.x 及以上版本）以及 *Hive Thriftserver*，以及如何为域分片提供多个 thrift URL，这有助于减少因过多连接到单个 Hive Metastore 实例而导致的拥塞。由于 `metastore` 是无状态的，这意味着服务本身不存储物理状态，您可以简单地创建多个前端服务，以便对 thrift 服务器的连接进行负载均衡，然后连接到底层的 `metastore` 数据库。

### 配置 Apache Spark 以连接到外部 Hive Metastore

您刚刚了解到 Apache Spark 使用 `hive-site.xml` 文件连接到 Hive Metastore。练习开始时我们没有提到的是，所有必需的配置都已为您提供在练习的 `conf` 目录中。然后，该目录通过 `docker-compose-all.yaml` 文件挂载到 Docker 环境中。

这意味着您无需将任何文件复制到`$SPARK_HOME`位置。打开 Docker Compose 文件`/ch-06/docker/docker-compose-all.yaml`，查看`services:zeppelin:volumes`部分以了解更多详情。

在`ch-06/docker`目录的上下文中，以下三个目录被挂载为卷：

- `/spark/conf` – 此目录包含`spark-defaults.conf`和`hive-site.xml`。
- `/spark/jars` – 此目录包含我们启动 Spark 时希望在本地包含的所有 MySQL JDBC 驱动程序 JAR 包。
- `/spark/sql` – 此目录被挂载，以便我们可以将其用作 Spark SQL Warehouse 的根目录。

`spark-defaults.conf`文件是一个可选文件，可用于覆盖在该 Spark 安装上下文中运行的所有 Spark 作业的默认 Spark 配置值。对于当前的用例，我们只关心表 6-2 中详述的七个属性。

**表 6-2** Hive Metastore 配置属性

| Spark 属性名称 | 描述 | 示例 |
| --- | --- | --- |
| `spark.sql.warehouse.dir` | 读取和写入分布式 SQL 表的默认位置。此位置可以位于本地文件系统或任何兼容 HDFS 的文件系统上。 | `hdfs://metastores/hive/` `file:///spark/warehouse/` `s3a://your-bucket/hive/` |
| `spark.sql.catalogImplementation` | 定义 Spark 会话的后端 SQL 目录。 | `hive` |
| `spark.sql.hive.metastore.version` | 定义 Metastore 的 Hive 版本。 | 2.3.7 |
| `spark.sql.hive.metastore.jars` | 定义要加载到 Spark 类路径中的 Hive JAR 包的位置。Spark 默认使用内置的 JAR 包。 | `builtin`、`maven` 或 `/path/to/the/jars` |
| `spark.sql.hive.metastore.sharedPrefixs` | 用于搜索和加载特定版本 Hive Metastore 的逗号分隔的类名前缀。 | `com.mysql.cj.jdbc`、`com.mysql.jdbc`、`org.postgresql` |
| `spark.sql.hive.metastore.schema.verification` | 如果设置为`true`，则 Spark Metastore 版本必须与 Hive Metastore 中`VERSION`的值匹配。 | `true` |
| `spark.sql.hive.metastore.schema.verification.record.version` | 如果设置为`true`，Spark 将针对 Hive Metastore 中发布的内容强制执行记录级模式验证。 | `true` |

表 6-2 中描述的配置已在章节练习的`spark-defaults.conf`文件中预先配置。为简单起见，我们使用标准的 Hive 内置 JAR 包（撰写本文时为 v2.3.7）。

### 使用 Hive Metastore 进行模式强制

值得指出的是，Hive Metastore 可以作为写入分布式表的应用程序之间任何模式争议的可靠仲裁者。这意味着不良数据不会污染您的数据湖。这是使用 Hive Metastore 的众多原因之一。此选项通过`spark.sql.hive.metastore.schema.verification.record.version`设置进行配置（如表 6-2 所述）。

> **注意：** 您可以使用表 6-2 中的配置升级与 Spark 一起使用的 Hive Metastore 版本。具体来说，使用`spark.sql.hive.metastore.jars`和`spark.sql.hive.metastore.version`属性。您可以从官方 [Apache Hive 网站](https://hive.apache.org/downloads.html) 下载所需的 Hive Metastore JAR 包，以运行替代版本的 Hive Metastore。

### Spark 默认配置

`spark-defaults.conf`文件中定义的默认值使我们能够“一次定义”特定的集群范围设置，这些设置将自动应用于在默认值上下文中启动的任何 Spark 应用程序。在我们的案例中，这意味着在 Zeppelin 环境中启动的任何应用程序。可以使用应用程序级覆盖或在应用程序启动时提供显式配置`--conf`值来显式覆盖默认值。

#### 使用 Spark 默认值保护集群

您可以将默认配置值视为重要的通用设置，这些设置提供给您的 Spark 应用程序，以利于应用程序运行时并保护集群中运行的其他应用程序。

例如，如果您正在管理或运行自己的 Spark 集群，您很可能需要平衡运营成本，并且通常会部署一个固定大小的集群，该集群考虑了将按日或按小时运行的应用程序的需求。此外，您还需要考虑这些是批处理应用程序还是流式应用程序，以及应用程序是 IO 密集型还是 CPU 密集型。然后，启动到集群中的每个应用程序都可以从集群总资源中获取固定或动态数量的资源（最小/最大份额）。如果所有应用程序都尊重其他应用程序资源的边界，那么这种策略可以很好地发挥作用。

#### 提供默认限制

鉴于每个应用程序可以从共享集群资源中请求非固定数量的资源，至少可以说，集群管理可能很困难。尤其是当您期望数据管道中的每个作业都能运行时。因此，在设置应用程序可以消耗的资源总数的预定义限制时，采取高度防御性的措施变得非常重要，因为并非每个应用程序都需要相同数量的计算核心或 RAM 才能成功运行。这就是默认值发挥作用的地方。这里的妙处在于，应用程序所有者实际上需要了解某些配置的工作原理才能覆盖它们，根据我的经验，许多应用程序可以在默认的少量核心和 RAM 上正常运行。

例如，如果您考虑常见的 T 恤尺码方法（S、M、L、XL），一个小型应用程序可能包含以下内容：

```
spark.cores.max 8
spark.executor.cores 1
spark.executor.memory 1g
```

此配置会将一个应用程序固定到八个计算核心和八个运行 1GB JVM 大小的执行器上。

通过让其他应用程序所有者更容易地声明共享集群的一部分，或者在像 Kubernetes 这样更动态的环境中，有许多配置选项可以简单地抽象到默认值中。第 14 章介绍了使用 Spark Standalone 部署应用程序，第 15 章介绍了使用 Kubernetes 部署应用程序。

### 生产环境 Hive Metastore 注意事项

使用外部 Hive Metastore 的好处在于，你的表定义将在 Spark SQL 会话之间持久保存。如果没有为`SparkSession`提供`hive-site.xml`，则会使用 Derby 创建一个本地 Metastore。基于 Derby 的 Metastore 不适用于生产环境，且只能与单个`SparkContext`配合使用，你可以将其理解为“单一位置”。

同样值得指出的是，Hive Metastore 并非直接与 MySQL 绑定。大多数常见的关系型数据库（MS SQL Server、MariaDB、Oracle 和 Postgres）也受支持。有关配置和使用其他外部关系型数据库的更多信息，你可以在线查看管理手册。

最后，为了运行生产级、高可用的 Metastore，必须防范任何单点故障，包括底层数据库本身。运行兼容 SQL/JDBC 的托管式云数据库，例如 [Amazon Aurora DB](https://aws.amazon.com/rds/aurora/%253Faurora-whats-new.sort-by%253Ditem.additionalFields.postDateTime%2526aurora-whats-new.sort-order%253Ddesc) 或 [Google Cloud Spanner](https://cloud.google.com/spanner)，为减少在没有资深 DBA 的情况下运营数据库时可能出现的故障提供了良好选择。如果你不具备自行运维的条件，建议使用任何具备监控、备份和自动故障转移功能的完全托管型关系型数据库，而不是自行搭建。

### 练习 6-1：总结

这个初始练习引导你完成了引导 Hive Metastore 以创建外部、可靠的 Hive Metastore 的过程。现在你已经完成了 Hive Metastore 的引导、`hive-site.xml` 文件的配置以及 `spark-defaults.conf` 文件的准备，接下来可以专注于 Spark SQL Catalog，并学习如何使用外部 Hive Metastore 来驱动更可靠的 SQL 体验。

## Spark SQL Catalog

`Spark SQL Catalog` 是 Spark 内部的 SQL 元数据管理器。Spark SQL Catalog 提供的功能为处理数据库、临时表或托管表、视图、函数等提供了资源。你刚刚了解了 Hive Metastore；Spark SQL Catalog 提供了简单的抽象，使你能够轻松地与 Hive Metastore 进行互操作。如前所述，确保你生成的数据以自文档化的方式呈现，以便其他团队能够按需获取数据，而无需再安排会议来理解 `wU35` 或 `l2W` 这些列的含义（当然，它们分别代表 35 岁以下女性和最近两周），这对于数据平台和组织目标的成功至关重要。显然，代号并不会让数据团队的工作变得简单。如果每个表定义都有模式支持，并且每个列不仅存储数据类型，还存储简短的注释，那么使用你表的其他工程师就可以通过 Spark SQL Catalog 以自助方式了解你的数据。

### 练习 6-2：使用 Spark SQL Catalog

Spark SQL Catalog 可直接通过 `SparkSession` 访问，并提供了许多有用的方法来检查数据仓库和数据湖中可用的数据。

**注意**

请使用标题为`6_2_SparkSQLCatalog`的 Zeppelin 笔记跟随本练习。与之前的章节一样，在笔记中创建一个新段落，并使用 `%spark` 代码块来构建工作笔记。

#### 创建 Spark Session

让我们学习从头开始创建 `SparkSession`。使用代码清单 6-2 中的代码片段，在你的 Zeppelin 笔记中创建一个新段落并运行它以生成 `SparkSession`。

```
%spark
import org.apache.spark.sql._
val sparkSessionHive = SparkSession.builder
.master(spark.conf.get("spark.master"))
.config(spark.sparkContext.getConf)
.appName("hive-support")
.enableHiveSupport()
.getOrCreate()
代码清单 6-2
创建支持 Apache Hive 的新 SparkSession
```

我们刚刚创建的 `SparkSession` 可用于进一步探索 Spark SQL API。接下来，我们将通过一个有趣的探索过程来使用 `SparkSession`。鉴于我们在数据库（本地 MySQL 数据库）中有数据，第一个探索领域将是列出并查看可用的内容。

#### Spark SQL 数据库

刚开始时，我们知道希望能够查找并找到可用的数据库。这个过程可以利用我们熟悉的老朋友 SQL，帮助我们站稳脚跟并理解 SQL Catalog 的功能。

##### 列出可用数据库

```
%spark
sparkSessionHive
.sql("show databases;")
.show
```

此方法允许我们查看可用的数据库。在我们的例子中，只有 `default` 数据库。你可能注意到了一条明显缺失的信息。你能发现它吗？也许问问自己 `default` 到底是什么意思？这是指上一章中我们命名为 `default` 的 MySQL 数据库，还是其他含义？

要了解更多信息，你需要深入 Spark SQL Catalog，如代码清单 6-3 所示。

```
%spark
sparkSessionHive
.catalog
.listDatabases
.show(truncate=false)
代码清单 6-3
使用 Spark SQL Catalog 列出数据库
```

运行代码清单 6-3 中 `listDatabases` 代码块的结果如表 6-3 所示。

**表 6-3**  

默认的 Spark SQL 数据库，即仓库

| 名称 | 描述 | locationUri |
| --- | --- | --- |
| `default` | 默认 Hive 数据库 | `file:/spark/sql/warehouse` |

这说得通。当我们在本章前面配置 Hive Metastore 时，我们只提供了连接到 Metastore 的 JDBC 信息，而没有提供连接到实际 MySQL 数据库的信息，而该数据库恰好也被命名为 `default`。