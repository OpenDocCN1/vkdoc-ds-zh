# 2. 大数据、机器学习与深度学习框架

本章将详细介绍用于并行数据处理的大数据框架——`Apache Spark`。同时，还将介绍几个有助于构建可扩展应用的机器学习（ML）和深度学习（DL）框架。阅读本章后，你将了解如何使用具有弹性和容错性的技术来收集、操作和检查大数据。本章讨论了 `Scikit-Learn`、`Spark MLlib` 和 `XGBoost` 框架，还介绍了一个名为 `Keras` 的深度学习框架。最后，讨论了有效设置和管理这些框架的方法。

大数据框架支持并行数据处理。它们使你能够将大数据分布在多个集群中。最流行的大数据框架是 `Apache Spark`，它构建在 `Hadoop` 框架之上。

## 大数据

大数据对不同的人有不同的含义。在本书中，我们将大数据定义为使用经典方法无法充分处理和操作的大量数据。我们必须使用可扩展的框架和现代技术来处理这些数据并从中获取洞察。当数据无法容纳在当前的内存存储空间中时，我们通常认为数据是“大”的。例如，如果你有一台个人电脑，而手头的数据量超过了电脑的存储容量，这就是大数据。这同样适用于拥有大型存储集群的大型企业。当我们使用 `Hadoop/Spark` 技术栈时，我们经常会谈论大数据。

### 大数据的特征

大数据的特征被描述为四个 V——速度（Velocity）、体量（Volume）、多样性（Variety）和真实性（Veracity）。表 2-1 重点介绍了大数据的这些特征。

**表 2-1 大数据特征**

| 要素 | 描述 |
| --- | --- |
| 速度 | 现代技术和改进的连接性使你能够以前所未有的速度生成数据。速度的特征包括批量数据、近实时或实时数据以及数据流。 |
| 体量 | 数据增长的规模。数据源和基础设施的性质会影响数据的体量。体量的特征包括艾字节、泽字节等。 |
| 多样性 | 数据可能来自独特的来源。现代技术设备到处留下数字足迹，这增加了企业和个人获取数据的来源数量。多样性的特征包括数据的结构和复杂性。 |
| 真实性 | 数据必须来自可靠的来源。同时，数据必须高质量、一致且完整。 |

## 大数据对商业和人们的影响

毫无疑问，大数据影响着我们的思维方式和商业运作方式。数据驱动的组织通常为基于证据的管理奠定基础。大数据涉及使用定量方法衡量业务的关键方面。它有助于支持决策。接下来的部分将讨论大数据影响企业和人们的方式。

### 优化客户关系

来自大数据的洞察有助于管理客户关系。掌握客户大数据的企业可以研究客户的行为模式，并运用描述性分析来驱动客户管理策略。

### 精细化产品开发

数据驱动型企业利用大数据分析和预测性分析来推动产品开发与管理策略。这种方法对于应用的增量迭代交付非常有用。

### 提升决策质量

当企业拥有大数据时，可以利用它来揭示现象的复杂模式，从而影响战略制定。这种方法有助于管理层基于证据而非主观推理做出明智决策。数据驱动型企业会培养一种基于证据管理的文化。

我们还在生命科学、物理学、经济学和医学等领域运用大数据。大数据影响世界的方式多种多样。本章并未考虑所有因素。接下来的章节将解释大数据仓库和 `ETL` 活动。

## 大数据仓库

过去几十年里，企业在本地数据库中投入了大量资源，包括 `Microsoft Access`、`Microsoft SQL Server`、`SAP Hana`、`Oracle Database` 等。近年来，像 `Microsoft Azure SQL` 和 `Oracle XE` 这样的云数据库得到了广泛采用。此外，还有像 `Cassandra` 和 `HBase` 等标准的大数据（分布式）数据库。企业正转向可扩展的基于云的数据库，以利用不断增强的计算能力、容错技术和可扩展解决方案带来的优势。

### 大数据 `ETL`

尽管数据库管理取得了显著进步，但人们操作数据库数据的方式基本保持不变。提取、转换和加载（`ETL`）在分析和报告中仍然扮演着不可或缺的角色。表 2-2 讨论了 `ETL` 活动。

**表 2-2 ETL 活动**

| 活动 | 描述 |
| --- | --- |
| 提取 | 涉及从某个数据库中获取数据。 |
| 转换 | 涉及将数据库中的数据转换为适合分析和报告的格式。 |
| 加载 | 涉及将数据存储在数据库管理系统中。 |

要执行 `ETL` 活动，必须使用查询语言。最流行的查询语言是 `SQL`（结构化查询语言）。随着开源运动的发展，还出现了其他查询语言，例如 `HiveQL` 和 `BigQuery`。`Python` 编程语言支持 `SQL`。`Python` 框架可以通过实现诸如 `SQLAlchemy`、`pyodbc`、`SQLite`、`SparkSQL` 和 `pandas` 等库来连接数据库。

## 大数据框架

大数据框架使开发人员能够收集、管理和操作分布式数据。大多数开源大数据框架都使用内存集群计算。最流行的框架包括 `Hadoop`、`Spark`、`Flink`、`Storm` 和 `Samza`。本书使用 `PySpark` 来执行 `ETL` 活动、探索数据以及构建机器学习流水线。

### `Apache Spark`

`Apache Spark` 执行内存集群计算。它使开发人员能够使用 `Java`、`Scala`、`Python`、`R` 和 `SQL` 构建可扩展的应用程序。它包含诸如驱动程序、集群管理器和执行器之类的集群组件。你可以将其用作独立的集群管理器，或者部署在 `Mesos`、`Hadoop`、`YARN` 或 `Baronets` 之上。你可以使用它来访问 `Hadoop` 文件系统（`HDFS`）、`Cassandra`、`HBase` 和 `Hive` 等数据源中的数据。`Spark` 数据结构被认为是一种弹性分布式数据集。本书介绍了一个集成了 `Python` 和 `Apache Spark`（`PySpark`）的框架。本书使用它来操作 `Spark MLlib`。要理解这个框架，你首先需要掌握弹性分布式数据集背后的概念。

#### 弹性分布式数据集

弹性分布式数据集（`RDD`）是用于并行化数据或转换现有数据的不可变元素。主要的 `RDD` 操作包括转换和动作。我们将它们存储在 `Hadoop` 支持的任何存储中。例如，在 `Hadoop` 分布式文件系统（`HDFS`）、`Cassandra`、`HBase`、`Amazon S3` 等中。

#### `Spark` 配置

`Spark` 配置的领域包括 `Spark` 属性、环境变量和日志记录。默认配置目录是 `SPARK_HOME/conf`。

你可以在环境中使用 `pip install findspark` 安装 `findspark` 库，并使用 `pip install pyspark` 安装 `pyspark` 库。

清单 2-1 使用 `findspark` 框架准备 `PySpark` 框架。

```python
import findspark as initiate_pyspark
initiate_pyspark.init("filepath\spark-3.0.0-bin-hadoop2.7")
```

**清单 2-1 准备 PySpark 框架**

清单 2-2 使用 `SparkConf()` 方法规定 `PySpark` 应用程序。

```python
from pyspark import SparkConf
pyspark_configuration = SparkConf().setAppName("pyspark_linear_method").setMaster("local")
```

**清单 2-2 规定 PySpark 应用程序**

清单 2-3 使用 `SparkSession()` 方法准备 `PySpark` 会话。

```python
from pyspark.sql import SparkSession
pyspark_session = SparkSession(pyspark_context)
```

**清单 2-3 准备 Spark 会话**

#### `Spark` 框架

`Spark` 框架扩展了 `Spark API` 的核心。有四个主要的 `Spark` 框架——`SparkSQL`、`Spark Streaming`、`Spark MLlib` 和 `GraphX`。

##### `SparkSQL`

`SparkSQL` 使您能够使用关系查询语言，如 `SQL`、`HiveQL` 和 `Scala`。它包含一个包含行对象和模式的 `schemaRDD`。您可以使用现有的 `RDD`、`parquet` 文件或 `JSON` 数据集来创建它。执行 `Spark Context` 以创建 `SQL` 上下文。

##### `Spark Streaming`

`Spark Streaming` 是一个可扩展的流处理框架，支持 `Apache Kafka`、`Apache Flume`、`HDFS` 和 `Apache Kensis` 等。它使用 `DStream` 以小批量方式处理输入数据，您可以通过 `HDFS`、数据库和仪表板推送这些数据。较新版本的 `Python` 不支持 `Spark Streaming`。因此，本书不涉及该框架。您可以使用 `Spark Streaming` 应用程序从任何数据源读取输入，并将数据副本存储在 `HDFS` 中。这使您能够构建和启动一个 `Spark Streaming` 应用程序，该应用程序处理传入数据并对其运行算法。

##### `Spark MLlib`

MLlib 是一个机器学习框架，允许您开发和测试机器学习和深度学习模型。在 Python 中，这些框架与 `NumPy` 框架协同工作。Spark `MLlib` 可以与多个 `Hadoop` 数据源一起使用，并集成到 `Hadoop` 工作流中。常见算法包括回归、分类、聚类、协同过滤和降维。关键的工作流工具包括特征转换、标准化和归一化、流水线开发、模型评估和超参数优化。

## GraphX

`GraphX` 是一个可扩展且容错的框架，用于迭代和快速的图并行计算、社交网络和语言建模。它包括图算法，例如用于估计图中每个顶点重要性的 `PageRank`、用于使用图中编号最小的顶点 ID 标记图连通分量的 `Connected Components`，以及用于查找通过每个顶点的三角形数量的 `Triangle Counting`。

## 机器学习框架

要解决机器学习问题，您需要一个支持构建和扩展机器学习模型的框架。机器学习模型并不缺乏——有数不清的机器学习框架。有多个机器学习框架可供您使用。后续章节将介绍 `Scikit-Learn`、Spark `MLlib`、`H2O` 和 `XGBoost` 等框架。

### Scikit-Learn

`Scikit-Learn` 框架包含回归、分类和聚类等机器学习算法。你可以将其与 `NumPy` 和 `SciPy` 等其他框架一起使用。它可以执行机器学习项目所需的大部分任务，例如数据处理、转换、数据拆分、归一化、超参数优化、模型开发和评估。`Scikit-Learn` 随大多数支持 Python 的发行包一起提供。在你的 Python 环境中使用 `pip install sklearn` 进行安装。

### H2O

`H2O` 是一个使用无人驾驶技术的机器学习框架。它使你能够加速 AI 解决方案的采用。它非常易于使用，并且不需要任何技术专业知识。不仅如此，它还支持数值和分类数据，包括文本。在训练机器学习模型之前，你必须先将数据加载到 `H2O` 集群中。它支持 `CSV`、`Excel` 和 `Parquet` 文件。默认数据源包括本地文件系统、远程文件、`Amazon S3`、`HDFS` 等。它拥有回归、分类、聚类分析和降维等机器学习算法。它还可以执行机器学习项目所需的大部分任务，例如数据处理、转换、数据拆分、归一化、超参数优化、模型开发、检查点、评估和生产化。在你的环境中使用 `pip install h2o` 安装该包。

清单 2-4 准备了 `H2O` 框架。

```python
import h2o
h2o.init()
```

清单 2-4 初始化 `H2O` 框架

### XGBoost

`XGBoost` 是一个支持包括 Python 在内的编程语言的机器学习框架。它执行可扩展的梯度提升模型，并快速学习并行和分布式计算，而不会牺牲内存效率。不仅如此，它还是一个集成学习器。如第 a 章所述，集成学习器可以解决回归和分类问题。`XGBoost` 使用提升方法从前序树的错误中学习。当基于树的模型过拟合时，它非常有用。在你的 Python 环境中使用 `pip install xgboost` 安装该模型。

## 深度学习框架

深度学习框架提供了支持扩展人工神经网络的结构。你可以单独使用它，也可以与其他模型一起使用。它通常包含程序和代码框架。主要的深度学习框架包括 `TensorFlow`、`PyTorch`、`Deeplearning4j`、`Microsoft Cognitive Toolkit` (`CNTK`) 和 `Keras`。

### Keras

`Keras` 是一个使用 Python 编写的高级深度学习框架；它运行在名为 `TensorFlow` 的机器学习平台之上。它对于深度学习模型的快速原型设计非常有效。你可以在张量处理单元或大规模图形处理单元上运行 `Keras`。主要的 `Keras` API 包括模型、层和回调。第 7 章将介绍此框架。执行 `pip install Keras` 和 `pip install tensorflow` 来使用 `Keras` 框架。