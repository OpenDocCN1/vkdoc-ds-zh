# 附录 A. 安装 Giraph

## 使用 Linux 包管理器安装

从 Apache Bigtop 发行版启用 Hadoop 安装的第一步，是告知你的 Linux 存储库管理器软件包所在的 URL。导航到[`archive.apache.org/dist/bigtop/stable/repos/`](http://archive.apache.org/dist/bigtop/stable/repos/)。确保找到与你 Linux 操作系统版本对应的文件夹，并下载名为`bigtop.XXX`的存储库定义文件（其中`XXX`是特定于你所使用的 Linux 版本的扩展名）。获取文件后，请确保将其复制到你的 Linux 操作系统存储库管理器查找外部存储库定义的位置（别忘了使用`sudo(8)`以便复制到系统位置）。表 A-1 总结了各种 Linux 发行版的存储库定义文件位置。

**表 A-1.** 存储库定义的位置

| Linux 发行版 | 需要复制存储库文件的文件夹 |
| --- | --- |
| Debian 和 Ubuntu | `/etc/apt/sources.list.d` |
| CentOS、RHEL 和 Fedora | `/etc/yum/repos.d` |
| SUSE 和 OpenSUSE | `/etc/zypp/repos.d` |

将 Bigtop 存储库定义文件添加到表 A-1 列出的某个文件夹后，下一步是导入存储库密钥。导入密钥允许软件包管理器确保其安装的是正版软件包。由于存储库密钥建立了信任，请务必始终从安全的[`dist.apache.org/repos/dist/release/bigtop/KEYS`](https://dist.apache.org/repos/dist/release/bigtop/KEYS)下载并存储在当前目录中。完成此操作后，只需添加密钥并刷新本地缓存的存储库定义即可。之后，你就可以使用 Linux 上软件包安装的常规方法来安装 Hadoop 和 Giraph。所需步骤总结在表 A-2 中，各行同样对应不同版本的 Linux。

**表 A-2.** 通过 Linux 二进制软件包安装 Giraph

| Debian 和 Ubuntu | CentOS、RHEL 和 Fedora | SUSE 和 OpenSUSE |
| --- | --- | --- |
| `$ sudo apt-key add < KEYS`<br>`$ sudo apt-get update`<br>`$ sudo apt-get install giraph` | `$ sudo rpm --import KEYS`<br>`$ sudo yum clean metadata`<br>`$ sudo yum install giraph` | `$ sudo rpm --import KEYS`<br>`$ sudo zypper update`<br>`$ sudo install giraph` |

运行最后一条命令的一个有趣附带作用是，Giraph 与 Hadoop 之间的依赖关系会被正确识别，并且合适的 Hadoop 软件包会被隐式地安装到你的系统上。从某种意义上说，一旦你决定通过 Linux 软件包安装 Giraph，安装 Hadoop 的最佳（也是最简单的！）方式就是也以打包形式安装它。事实上，你甚至无需显式执行该步骤：系统会自动获取正确的 Hadoop 软件包。

如果你想知道通过 Linux 软件包安装 Giraph 后需要如何设置环境变量`HADOOP_HOME`和`GIRAPH_HOME`，好消息是你再也不必担心这些了。你可以像在 Linux 系统上运行任何可执行文件一样，直接运行`hadoop`和`giraph`命令行工具：只需输入其名称即可。

不可否认，通过 Linux 软件包安装是安装 Giraph 和 Hadoop 迄今为止最简单的方法。如果你选择商业供应商之一，还可以注册获得专业支持。然而，缺点是，通过安装预打包的组件，你将放弃为你的环境选择独特 Giraph 和 Hadoop 版本组合的权利。如果你发现自己需要将 Giraph 与自定义版本的 Hadoop 及其生态系统组件相匹配，那么通过从源代码构建来安装 Giraph 可能是你唯一的选择。

## 通过源码编译安装 Giraph

Giraph 的源代码以 gzipped tar 文件形式打包，您可以在下载二进制文件的同一项目网站上获取：[`http://giraph.apache.org/releases.html`](http://giraph.apache.org/releases.html)。对于 Giraph 1.1.0 版本，请下载名为 `giraph-dist-1.1.0-src.tar.gz` 的文件，并将其解压到您的开发工作站上的某个位置。

Giraph 的构建基础设施由 Apache Maven 管理。如果您决定通过从源代码构建来安装 Giraph，则必须确保您的环境中安装了 Maven 3 或更高版本。如果您没有安装 Maven，请确保按照项目网站 [`http://maven.apache.org`](http://maven.apache.org/) 提供的安装说明进行操作。本附录的其余部分假设您已设置好 Maven，并且可以成功运行 `mvn` 命令行工具。

> **注意：** 当您现有的 Hadoop 集群所依赖的组件版本（包括 Hadoop 本身）与打包的 Giraph 二进制文件不匹配时，您将通过从源代码构建的方式来安装 Giraph。尽管可以针对任意版本的依赖项构建 Giraph，但请注意，这种组合很可能从未经过测试。

Giraph 的构建基础设施提供了一种便捷的方式来指定 Giraph 需要构建所依赖的确切版本。您无需下载任何这些依赖项，也无需在环境中额外配置它们——Maven 会为您处理。您需要使用 Maven 构建配置文件来指定 Hadoop 的主版本号，以及当您希望将 Giraph 构建为 YARN 客户端而不是纯 MapReduce 应用程序时。最常用的构建配置文件总结在表 A-3 中。

**表 A-3.** 常用的 Maven 构建配置文件

| 配置文件名称 | 配置文件效果 |
| --- | --- |
| `hadoop_1` | 生成与 Hadoop 1.x 兼容的构建 |
| `hadoop_2` | 生成与 Hadoop 2.x 兼容的构建 |
| `hadoop_yarn` | 生成基于 YARN 的 Giraph 构建（假设使用 Hadoop 2.x） |

使用 Maven 构建配置文件可以将大多数属性预设为所需的值，同时仍然允许进行精确修改以匹配依赖项的次版本号和微版本号。虽然从源代码构建 Giraph 时可以调整数十个属性，但最常用的属性总结在表 A-4 中。

**表 A-4.** 用于指定 Giraph 依赖项确切版本的常用属性

| 属性名称 | 属性效果 |
| --- | --- |
| `hadoop.version` | 用于指定 Hadoop 依赖项的确切版本 |
| `dep.accumulo.version` | 用于指定 Accumulo 依赖项的确切版本 |
| `dep.hbase.version` | 用于指定 HBase 依赖项的确切版本 |
| `dep.hcatalog.version` | 用于指定 HCatalog 依赖项的确切版本 |
| `dep.hive.version` | 用于指定 Hive 依赖项的确切版本 |
| `dep.zookeeper.version` | 用于指定 ZooKeeper 依赖项的确切版本 |

从指定配置文件开始，然后指定所有所需的版本属性，可以让您对生成的 Giraph 二进制文件进行非常精细的控制。例如，假设您的集群运行的是 Hadoop 2.7.1 和 HBase 0.98。清单 A-5 概述了构建与您的集群依赖项兼容的、支持 YARN 的 Giraph 的方法。但是，在运行此命令之前，请记住，您所请求的特定依赖项组合可能未经测试。其后果可能包括构建失败、单元测试失败或运行时失败。一旦偏离常规路径，您就需要自行解决问题，尽管它也可能正常工作。因此，如果单元测试失败，请不要气馁。其中一些单元测试对它们所针对开发的依赖项版本较为敏感；可以通过向 Maven 构建传递 `-DskipTests` 来关闭它们。

**清单 A-5.** 针对 Hadoop 2.7.1 和 HBase 0.98 构建 Giraph 的示例

```
$ mvn -Phadoop_yarn -Dhadoop.version=2.7.1 -Ddep.hbase.version=0.98 -DskipTests
```

构建完成后，您将在 `giraph-dist/target` 文件夹下找到与之前章节中下载的非常相似的二进制文件。在那里找到 gzipped tar 文件，并按照上一节中描述的步骤，在您的网关节点上解压并安装该自定义版本的 Giraph。不要忘记像在清单 A-4 中那样设置 `GIRAPH_HOME` 并将 Giraph 二进制文件添加到您的 `$PATH` 变量中。

正如您所见，到目前为止，在系统上安装 Hadoop 和 Giraph 有几种不同的方法。当然，安装完成后，您还需要配置它们。在配置之前，这些安装包实际上无法用于任何操作。这正是下一节的主题。

### Hadoop 及 Hadoop 生态系统项目配置基础

几乎所有 Hadoop 生态系统的成员（包括 Hadoop 本身）都共享通用的配置管理系统，该系统基于一组编码了键值对属性设置的平面 XML 文件。这些配置文件所在的文件夹因项目和安装方法而异。对于通过 Linux 软件包（DEB 或 RPM）安装的任何项目，配置文件的位置在`/etc/<项目名称>/conf`下。但是，如果是手动安装，相同的配置文件则位于项目的`HOME`文件夹下的`conf`子文件夹中。Hadoop 和 Giraph 的配置文件具体位于何处取决于安装方法，总结于表 A-5 中。

**表 A-5.** 按安装方法区分的配置文件位置

| 安装方法 | Hadoop 配置文件夹 | Giraph 配置文件夹 |
| --- | --- | --- |
| Linux 软件包 | `/etc/hadoop/conf` | `/etc/giraph/conf` |
| 手动安装 | `$HADOOP_HOME/conf` | `$GIRAPH_HOME/conf` |

在这些文件夹中，您会找到一个或多个 XML 文件，类似于清单 A-6 中所示的示例。请注意，虽然 Giraph 只需要一个名为`giraph-site.xml`的配置文件，但 Hadoop 将其配置分散在至少三个不同的文件中——`core-site.xml`、`hdfs-site.xml`和`mapred-site.xml`——而`yarn-site.xml`是用于配置作为 Hadoop 2.x 一部分的 YARN 的附加文件。

**清单 A-6.** Hadoop 配置文件示例

```xml
<configuration>
   <property>
    <name>prop.name</name>
    <value>prop.value</value>
    <description>关于此属性作用的自由格式描述</description>
  </property>
</configuration>
```

## 配置 Giraph

严格来说，默认情况下，Giraph 实际上不需要在其 XML 配置文件中进行任何设置。任何可能是必需的配置选项（例如指定工作节点数量）都可以通过其命令行工具传递给 Giraph。不过，很快就会因为不断地在命令行中输入所有必需的选项而感到沮丧，这时你就可以开始利用`giraph-site.xml`来存储特定于你环境的配置（只需确保将其放在表 A-5 列出的文件夹中即可）。例如，如果你发现在本地模式下配置的 Hadoop 之上运行 Giraph 应用程序，那么拥有一个类似于清单 A-7 的 Giraph 配置文件可能会很有用。

**清单 A-7.** 示例 Giraph 配置文件`giraph-site.xml`

```xml
<configuration>
   <property>
      <name>giraph.SplitMasterWorker</name>
      <value>false</value>
      <description>此设置允许 Giraph 应用在单个任务中运行</description>
   </property>
</configuration>
```

尽管 Giraph 有自己的配置文件，但它仍然依赖于通过特定于 Hadoop 的配置文件来正确配置的 Hadoop 客户端。配置 Hadoop 和匹配 Hadoop 客户端是下一节的主题。

## 配置 Hadoop

从根本上说，Hadoop 作业可以在以下三种模式之一中运行：

*   在完全分布式 Hadoop 集群上，并在网关节点上配置客户端
*   在伪分布式 Hadoop 集群上，客户端位于同一位置
*   在本地 Hadoop 配置中，客户端模拟特定于集群的 Hadoop 机制

本书中的绝大多数示例都假设你在本地模式下配置的 Hadoop 之上运行 Giraph 应用程序。本地模式是最容易设置的。它不需要在任何地方运行任何活动进程，并且确保 Giraph 作业在单个 JVM 中与其他所有内容一起运行。本地模式不使用 HDFS，而是读写本地文件系统中的文件，这使得它更容易使用。你甚至不需要配置任何东西来启用本地模式，因为它恰好也是默认模式（因此不需要 XML 配置文件或空文件来启用它）。

本地模式非常适合开发和调试你的 Giraph 应用程序，但它也有一些限制。首先，它一次只能运行一个任务，这要求你将 Giraph 工作节点的数量限制为一个，并将主节点和工作节点合并到同一个任务中。也许一个更大的限制是，本地模式执行不会触发在真实 Hadoop 集群上运行时通常触发的相同代码路径。在调试 Giraph 应用程序时请记住这一点——你可能会看到行为上的差异。最后，尽管本地模式应该是最快的（至少在小型数据集上是这样），但 Hadoop 仍然需要做相当多的工作来引导 MapReduce 框架。这可能会导致 Giraph 代码开始执行之前有长达 20 秒的延迟。

与本地模式完全相反的是完全分布式 Hadoop 集群。这是在生产环境中运行 Giraph 应用程序的常用方式；它假设有一个完全分布的 Hadoop 集群可用。Giraph 充当纯客户端，你需要从集群网关节点提交 Giraph 作业。网关节点的可用 Hadoop 配置需要与集群所有其他节点上运行的 Hadoop 所使用的配置相匹配。这通常由 Hadoop 集群管理员完成；不需要集群用户进行任何显式配置。管理员需要确保的是，Hadoop 配置文件夹的内容在集群中的所有节点（包括网关节点）上保持同步。

## 配置伪分布式模式的 Hadoop

在有些受限的本地模式和需要大量设置的重量级完全分布式模式之间，一个非常好的折衷方案是 Hadoop 的伪分布式配置。在这种模式下，你可以在一个尽可能接近真实集群的环境中运行你的作业，而无需设置多节点集群的麻烦（尽管正如你在第 12 章中所看到的，这种麻烦可以通过使用 Hadoop 云服务来缓解）。伪分布式模式会在同一台主机上运行真实集群所拥有的所有进程（守护进程）。这些进程运行在不同的 JVM 中，并通过回环网络接口进行通信。除此之外，其配置与你将在真实集群上看到的完全相同。伪分布式模式是本地模式和完全分布式模式之间一个很好的中间地带。

启用伪分布式模式的最简单方法是从 Linux 软件包安装 Hadoop。有一个名为`hadoop-conf-pseudo`的特殊软件包，它可以拉取所有正确的依赖项，并在`/etc/hadoop/conf`中预设伪分布式模式所需的配置参数。只需安装该软件包即可。

在无法从 Linux 软件包安装 Hadoop 的情况下，另一种选择是手动将 Hadoop 二进制文件安装到`$HADOOP_HOME`文件夹下（如本附录前面所述），并确保在适当的配置文件中设置表 A-6 中总结的属性。

**表 A-6.** 伪分布式模式所需的 Hadoop 配置

| 配置文件 | 属性名称 | 属性值 | 属性作用 |
| --- | --- | --- | --- |
| `core-site.xml` | `fs.default.name` | `hdfs://localhost/` | 为伪分布式 HDFS 文件系统设置 URL |
| `hdfs-site.xml` | `dfs.replication` | `1` | 设置复制因子为 1，因为在伪分布式模式下，你只有一个数据节点 |
| `mapred-site.xml` | `mapred.job.tracker` | `localhost:8021` | 为伪分布式 Job Tracker 设置 URL |
| `yarn-site.xml` | `yarn.nodemanager.aux-services` | `mapreduce_shuffle` | 让 YARN 知道 MapReduce 框架需要的额外服务 |
| `yarn-site.xml` | `yarn.nodemanager.aux-services.mapreduce_shuffle.class` | `org.apache.hadoop.mapred.ShuffleHandler` | 指定实现`mapreduce_shuffle`服务的类的名称 |

由于伪分布式模式需要运行一些进程，配置后的最后一步是启动必要的服务并初始化其状态。首先，你需要启动 HDFS。根据你是从 Linux 软件包安装还是从二进制 gzip 压缩的 tar 文件安装，使用表 A-7 中提供的说明。当然，如果你没有从软件包安装 Hadoop，不要忘记将`$HADOOP_HOME`添加到你的`$PATH`中，如清单 A-3 所示。

**表 A-7.** 在伪分布式模式下启动 HDFS

| 从 Linux 软件包安装 Hadoop | 从二进制 gzip 压缩的 tar 文件安装 Hadoop |
| --- | --- |
| `$ sudo service hadoop-hdfs-namenode format`<br>`$ sudo service hadoop-hdfs-namenode start`<br>`$ sudo –u hdfs hadoop fs –chmod 777 /` | `$ hadoop namenode –format`<br>`$ hadoop-daemon.sh start namenode`<br>`$ hadoop-daemon.sh start datanode`<br>`$ hadoop fs –chmod 777 /` |

> **注意：** 最后一个命令使 HDFS 的根子目录对所有用户可读可写。在一个真实的、完全分布式的、多租户集群上，这将是一个巨大的安全问题。然而，在伪分布式设置中，它允许你跳过一些设置步骤，而不会损害 HDFS 的功能。

至此，你应该拥有一个功能完备的 HDFS。你可以通过执行清单 A-8 所示的命令来验证其是否已启动并运行。

**清单 A-8.** 验证能否向伪分布式 HDFS 复制文件及从中复制文件

```
$ hadoop fs –put /etc/hosts /test.file
$ hadoop fs –cat /test.file
$ hadoop fs –rm /test.file
```

只要所有命令都执行成功，你就可以确信伪分布式 HDFS 设置无误。设置伪分布式 Hadoop 集群的最后一步是启动 MapReduce 服务（针对 Hadoop 1.x）或 YARN 服务（针对 Hadoop 2.x）。所需命令分别总结在表 A-8 和表 A-9 中。

**表 A-9.** 在伪分布式模式下启动 YARN

| 通过 Linux 软件包安装的 Hadoop | 通过二进制 gzip 压缩 tar 文件安装的 Hadoop |
| --- | --- |
| `$ sudo service hadoop-yarn-resourcemanager start`<br>`$ sudo service hadoop-yarn-nodemanager start`<br>`cd /usr/lib/hadoop-mapreduce` | `$ hadoop-daemon.sh start resourcemanager`<br>`$ hadoop-daemon.sh start nodemanager`<br>`cd $HADOOP_HOME/share/hadoop/mapreduce` |

**表 A-8.** 在伪分布式模式下启动 MapReduce

| 通过 Linux 软件包安装的 Hadoop | 通过二进制 gzip 压缩 tar 文件安装的 Hadoop |
| --- | --- |
| `$ sudo service hadoop-mapred-jobtracker start`<br>`$ sudo service hadoop-mapred-tasktracker start`<br>`cd /usr/lib/hadoop` | `$ hadoop-daemon.sh start jobtracker`<br>`$ hadoop-daemon.sh start tasktracker`<br>`cd $HADOOP_HOME` |

无论你设置的是 Hadoop 1.x 还是 Hadoop 2.x，执行完这些命令后，请务必在当前工作目录（即上一条 `cd` 命令将你定位到的目录）中运行一个测试 MapReduce 作业。如果清单 A-9 中显示的命令成功运行至完成，则意味着你的伪分布式 Hadoop 集群已完全就绪，可以用于你的 Giraph 应用程序了。

**清单 A-9.** 验证是否能在伪分布式 Hadoop 集群上运行 MapReduce 作业

```
$ hadoop jar examples.jar pi 10 1000
....
Job Finished in 35.513 seconds
Estimated value of Pi is 3.14080000000000000000
```

## 总结

你可以通过多种不同的方式安装 Hadoop 和 Giraph。需要牢记的关键问题是，无论安装哪个版本的 Hadoop，它都必须与你将要使用的 Giraph 二进制文件版本相匹配。如果你计划使用 I/O 格式将 Giraph 与 Hadoop 生态系统中的其他成员（例如 Hive、HBase 等）连接，这些版本也必须匹配。安装完成后，最后一步是配置两者。Giraph 默认不需要任何配置。Hadoop 必须配置为以下三种模式之一运行：本地模式、完全分布式模式或伪分布式模式。在本附录中，你了解了以下主题：

*   运行 Hadoop 和 Giraph 的系统要求：Linux 或 Mac OS X 上的 JDK 7+
*   使用两种主要方法安装 Hadoop 的步骤：二进制 gzip 压缩 tar 文件或 Linux 二进制软件包
*   使用三种不同安装方法安装 Giraph 的步骤：二进制 gzip 压缩 tar 文件、Linux 二进制软件包或从源码构建
*   使用本地平面 XML 配置文件配置 Hadoop 和 Giraph
*   执行简单的命令行以确保你的 Giraph 和 Hadoop 部署运行正常

尽管本附录提供了多种可选方案来完成安装和配置这两个基本任务，但如果你是从头开始安装所有内容，只需选择对你环境而言最简便的方法即可。然而，如果你正在处理现有的 Hadoop 集群，你可能会发现其中一些备选方法很有用。

# 索引

## A

- `Accumulo Aggregator API`。另请参见 `MasterCompute`
- `BasicAggregator` 抽象类
- `boolean OR` 函数
- `createInitialValue( )` 错误
- `aggregator` 接口方法
- 设置聚合器值
- 终止
- `Aggregator` 写入器
- 交替最小二乘（ALS）算法
- 亚马逊云服务（AWS）
- 亚马逊云
- `EMR 集群`。参见 (`EMR 集群`)
- 启动 MapReduce 作业
- 遗留集群
- AWS CLI 工具安装
- `EC2`
- `EMR`
- Apache Gora
- 应用场景
- `DoubleWritable` 对象
- `getConf( ).createVertex( )`
- `getGoraKey( )`
- `getGoraVertex( )` 方法
- `GoraVertexReader` API
- `GoraVertexWriter` 类
- `GVertexResult`
- 内存数据模型
- 内存 Java 对象
- 输入数据
- 输出格式实现
- 持久化接口
- `Vertex.initialize( )` 方法
- 顶点对象
- Apache ZooKeeper
- AWS。参见 亚马逊云服务 (AWS)

## B

- 广度优先搜索（BFS）
- `BspServiceMaster.createInputSplits( )` 方法
- `BspServiceMaster` 方法
- `BspServiceWorker.exchangeVertexPartitions( )` 方法
- `BspServiceWorker.finishSuperstep( )` 方法
- `BspServiceWorker.prepareSuperstep( )` 方法
- `BspServiceWorker.startSuperstep( )` 方法
- 批量同步并行模型
- `ByteArrayEdges`

## C

- 云计算
- AWS。参见 (亚马逊云服务 (AWS))
- 公有云服务
- 普通机器
- `compute( )` 方法
- `coordinateSuperstep( )`

## D

- 数据结构
- `MessageStore` 接口
- `ByteArrayMessagesPerVertexStore` 类
- `getMesssagesAsIterable( )`
- `getVertexMessages( )` 方法
- `IntFloatMessageStore`
- `OneMessagePerVertexStore`
- 原始 Java `HashMap` 类
- `PartitionMessages( )` 方法
- `ReusableObjectsOutEdges` 接口
- `SimpleMessageStore`
- `OutEdges` 接口
- `ByteArrayEdges`
- `compute` 方法
- 迭代友好型 API
- 迭代友好型 `OutEdges`
- 标签传播算法
- 随机访问
- 随机访问友好型 `OutEdges`
- 代表性对象
- 分布式存储系统

## E

- `EC2`。参见 弹性计算云 (EC2)
- 基于边的输入格式
- 抽象类 `EdgeReader`
- 抽象类
- `getCurrentSourceId( )` 方法
- `nextEdge( )` 方法
- 文本格式
- Twitter 图谱
- 基于边的表示
- 弹性计算云 (EC2)
- 弹性 MapReduce (EMR)
- EMR 集群
- 亚马逊云
- `ami-version`
- `CORE` 实例组
- 数据迁移
- 数据处理
- 端到端集群生命周期管理
- JSON 规范
- 键值对
- 运行查询命令
- 调试
- 部署 Giraph 应用程序
- 临时图处理
- 存储在 S3 中的 `GiraphHelloWorld` JAR 文件
- 检查日志和输出
- JSON 文件
- 监控集群状态
- 图形表示
- Hadoop 配置
- `InstanceCount`
- `InstanceGroupType`
- 实例大小
- `InstanceType`
- `MASTER` 实例组
- 预留实例
- 竞价实例
- `TASK` 实例组
- `TASK` 实例组
- 探索连通性
- 计算连通分量
- 定义
- 最大连通分量
- `SCC`
- `WCC`
- 计算最短路径
- `BFS`
- 边值
- 米尔格拉姆实验
- `MinValue` 组合器
- `SSSP`
- 以顶点为中心的编程模型

## F

- `finishSuperstep( )` 方法

## G

- 垃圾回收器（GC）
- `generateChangedPartitionOwners( )` 方法
- `getMesssagesAsIterable( )`
- `getVertexMessages( )` 方法
- Giraph 聚合器
- `Aggregator` 类
- `MaxValueAggregator` 类
- `MaxValue` 算法
- 算法，按性别统计 Twitter 关注者
- 普通应用架构
- 大数据
- 从大数据到大图
- 大图
- BSP 模型
- 组合器
- 关联函数
- 交换函数
- 定义
- `MaxValue` 组合器
- `MessageCombiner` 类
- 计算剖析
- `BasicComputation` 类
- `MaxValue` 算法
- 消息
- 超步
- 配置
- 数据分析
- 数据模型
- `Edge` 类
- `Vertex` 类
- 顶点边
- 顶点 ID
- 顶点值
- 顶点
- 电子商务公司
- 二分图
- 协同过滤管道
- Google Pregel
- 图转换算法
- Hadoop。参见 (Hadoop)
- `InOutDegree` 算法
- 安装
- 二进制发布版
- 依赖的暴力展开
- 网关/边缘节点
- Hadoop 1.x
- Hadoop 2.x
- 打包的 Hadoop 发行版
- 重构周期
- 软件项目 vs. 软件栈
- 源代码
- 大规模图处理
- 概念性组织
- 迭代算法
- 社交网络顶点
- MapReduce
- Neo4j
- NoSQL 数据库
- 离线计算
- OrientDB
- 基于每个顶点
- 定位
- 伪代码
- 社交网络公司社区 Facebook 管道
- 典型架构
- 数据处理管道
- 低延迟交互
- 周期性交互
- TinkerPop
- Twitter 图
- 以顶点为中心的编程模型
- 网页搜索公司组件索引管道
- `WorkerContext` 抽象类
- Worker 分布
- Giraph 算法
- 聚合器和组合器中心
- 特性
- 平均路径长度
- 聚类系数
- 度分布
- 国内航线网格图
- 枢纽节点
- 优先连接
- 随机图
- 重连图
- 无标度网络
- 小世界属性
- 美国编号公路系统
- 直径
- 离心率
- 图与潜在向量
- 停止条件
- 独立顶点决策
- LPA 分析
- 聚类系数
- 社区定义
- `LPAValue` 对象
- 大规模并行算法
- 最小化预测误差
- `aggregate( )` 方法
- 二分图分析
- 标签传播机制
- “乒乓”式计算
- `SGD`
- `SumAggregator` 更新函数
- PageRank 分析
- 代码实现组件
- 阻尼因子
- `SumValues` 网页图
- 外围半径
- 可扩展的顶点值
- Giraph 架构
- 引导 Giraph 服务
- 应用协调服务
- 弹性方法
- `GraphTaskManager.execute( )` 方法
- `GraphTaskManager.setup( )` 方法
- 纯 Map 应用 Mapper
- MapReduce 框架
- Master 服务
- 周期性检查点
- Reducer
- Worker 服务
- YARN
- `BspService` 抽象类
- 构建模块
- 协调器
- 功能结构
- Master
- Worker
- `CentralizedService` 接口
- 类层次结构
- 协调服务
- `_addressesAndPartitions`
- `_checkpointAndStop`
- `_cleanedUpDir`
- `_edgeInputSplitDir`
- `_edgeInputSplitDoneDir`

- `_edgeInputSplitsAllDone`
- `_edgeInputSplitsAllReady`
- 临时节点和顺序节点类型
- `giraph.zkBaseZNode` 属性
- `giraph.zkManagerDirectory` 属性
- `_haltComputation`
- 元数据和内部状态
- 数据和元数据管理
- `_partitionExchangeDir`
- `_superstepFinished`
- `_vertexInputSplitDir`
- `_vertexInputSplitDoneDir`
- `_vertexInputSplitsAllDone`
- `_vertexInputSplitsAllReady`
- `_workerFinishedDir`
- `_workerHealthyDir`
- `_workerUnhealthyDir`
- `_workerWroteCheckpointDir`
- `zkCli.sh` 命令行工具
- `zkCli.sh` 或 `zkCli.cmd`
- ZooKeeper
- `ZooKeeperExt` 类
- 分布式计算谬误
- 容错性
- 磁盘故障
- Giraph Master 故障
- Giraph Worker 故障
- 网络故障
- ZooKeeper 服务故障
- `GraphTaskManager.setup( )` 的起源
- Master 服务
- `NettyClient` 和 `NettyServer` 交互
- 节点服务分配
- Worker 服务
- Giraph 编程模型
- 聚合器
- 边计数应用
- `LongSumAggregator`
- Master 计算对象
- `TextAggregatorWriter`
- `TotalNumberOfEdges.java`
- `TotalNumberOfEdgesMC.java`
- 应用启动命令行配置
- Shell 环境
- `BasicComputation`
- `compute( )` 方法
- 泛型类型
- Giraph 代码库
- `GiraphHelloWorld.java`
- 图结构
- `GenerateTwitter.java`
- 图变异
- `Graphviz` 输出格式
- 发送消息
- 单元测试实现
- `LongWritable ID`
- `main( )` 方法
- Maven `pom.xml` 文件
- 项目构建
- 典型算法
- `SimpleShortestPathsComputation.java`
- `Twitter2Facebook.java`
- Twitter 连接 `OutputFormat`
- 用户自定义计算类
- Twitter 关注关系图 ASCII 文件定义
- `GiraphHelloWorld2.java`
- 输入数据 `InputFormat`
- 节点数字标识符
- Unix 哲学
- `voteToHalt( )` 调用
- 图生成器
- 图 IO 格式
- 聚合器写入器
- 图表示
- 输入格式类层次
- 基于边的表示。参见 (基于边的输入格式)
- `EdgeInputFormat` 过滤分离
- `VertexInputFormat`。另见 (基于顶点的输入格式)
- 输出格式
- 基于边的输出格式
- `VertexOutputFormat`。参见 (基于顶点的输出格式)
- 图变异。另请参见 `Mutation` API
- 图处理基本概念
- 核心概念定义
- 建模
- 计算机网络
- 多重图
- 语义图
- 社交网络
- 交通网络
- 在线计算 vs. 离线计算
- `GraphTaskManager.execute( )` 方法
- `GraphTaskManager.setup( )` 方法

## H

- Hadoop 架构
- Cassandra 配置
- 生态系统项目
- 完全分布式 Hadoop 集群
- 本地模式
- 伪分布式模式
- 定义
- Gora
- Hama
- HBase
- HCatalog
- HDFS
- Hive
- 安装
- Mahout
- MapReduce
- Nutch
- ZooKeeper
- Hadoop 集群
- Hadoop 分布式文件系统（HDFS）
- Hive，基于表的格式
- 文章分享事件
- 命令行参数
- 基于边的格式
- `getEdgeValue( )` 方法
- `getSourceVertexId( )` 方法
- `getTargetVertexId( )` 方法
- Hive 查询语言
- `HiveReadableRecord` 接口
- `HiveritableRecord` 接口
- 微博服务
- 输出表
- `SimpleHiveToVertex` 类
- `SimpleVertexToHive` 类
- 类似 SQL 的查询语言
- 静态 `NullWritable.get( )` 方法
- 基于文本的输入格式
- 基于顶点的格式

## I, J, K

- 基础设施即服务（IaaS）
- 内存存储系统

## L

- 标签传播
- 标签传播算法（LPA）
- 分析
- 聚类系数
- 代码实现
- 社区定义
- `LPAValue` 对象
- 最近最少使用（LRU）策略
- `loadCheckpoint( )` 方法

## M

- `MasterCompute`
- 任意逻辑
- 可组合 API
- `compute( )` 方法
- 图转换与连通分量算法
- `setComputation( )`
- 设置计算类
- `compute( )` 方法
- `haltComputation( )` 方法
- `initialize( )` 方法
- `MasterCompute` 抽象类
- 终止
- `voteToHalt( )` 方法
- `MasterGraphPartitioner` 接口
- Mutation API
- 直接变异请求
- `addEdgeRequest( )` 方法
- 算法实现
- 计算接口属性
- `removeVertexRequest( )` 方法
- 来自不同国家的连接用户
- 解决冲突的默认顶点解析器
- `VertexChanges` 接口
- `VertexResolver` 接口
- 通过消息
- 国家间的关注关系数量
- 国家间的关系

## N

- 自然语言处理（NLP）
- 节点故障
- Giraph Master 故障
- Giraph Worker 故障
- ZooKeeper 服务故障

## O

- 核外图（OOCG）
- 核外消息（OOCM）

## P, Q

- 并行性，Giraph
- 辅助类
- `createPartitionStats( )` 方法
- `generateChangedPartitionOwners( )` 方法
- `GraphPartitionerFactory` 实现
- `GraphPartitionerFactory` 接口
- `HashMasterPartitioner`
- `HashWorkerPartitioner` 实现
- `MasterGraphPartitioner` 接口
- `PartitionBalancer` 分区策略
- 哈希码映射顶点
- 每个 Worker 的性能统计。参见 (每个 Worker 的计算)
- 六节点社交图
- 线程安全
- 网页图
- `WorkerGraphPartitioner` 接口
- 分区平衡，Giraph
- PageRank 算法
- `PartitionBalancer` 顶点平衡
- 每个 Worker 的计算，Giraph
- 聚合统计
- Hadoop 计数器
- Hadoop Mapper
- 性能指标
- 性能统计
- 运行时性能
- `WorkerContext`
- 平台即服务（PaaS）

## R

- Redis，内存数据存储

## S

- 情感分析
- 单源最短路径（SSSP）
- 软件即服务（SaaS）
- 随机梯度下降（SGD）
- `storeCheckpoint( )` 方法

## T, U

- 基于表的存储系统
- 调优 Giraph
- 数据结构。参见 (数据结构)
- `OOCG`
- `OOCM`
- 参数
- 性能因素
- 大数据计算状态
- 计算密集型计算
- 数据密集型计算
- 图算法
- 图计算需求
- 硬件相关选择
- 作业相关选择
- 以顶点为中心的编程模型

## V

- `vclose( )` 方法
- 基于顶点的输入格式
- 抽象类
- `checkInputSpecs( )`

- `getCurrentVertex()` 方法
- `getSplits()` 实现
- 基于文本的 `VertexReader` 文本文件
- `VertexReader` 抽象类
- 基于顶点的输出格式
- 抽象类
- `checkOutputSpecs()`
- `initialize()` 方法
- 输出提交器
- 基于文本的 `VertexOutputFormat`
- 基于文本的 `VertexWriter`
- `VertexOutputFormat`
- `VertexReader` 对象
- `VertexWriter` 抽象类
- `VertexWriter` 对象
- `writeVertex()` 方法
- 基于顶点的表示
- 以顶点为中心的编程模型
- 顶点协调。参见 `MasterCompute`

## W, X, Y

- 弱连通分量（WCC）
- 编写模块化应用
- 可组合 API
- 添加反向边
- `MasterCompute`
- 将顶点 ID 传播给邻居
- 逐步过程
- 连通分量算法
- 将有向图转换为无向图
- 逻辑

## Z

- `znode`
- ZooKeeper 集群