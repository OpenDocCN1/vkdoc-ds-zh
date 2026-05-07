# 2. Vermeulen-Krennwallner-Hillman-Clark

让我们从构建一个客户开始。我虚构了一家公司，您将在学习本书的过程中为其执行实际的数据科学任务。您可以在 Windows 或 Linux 环境中运行您的示例。您只需下载所需的示例集即可。

本书中引用的任何源代码或其他补充材料均可在 GitHub 上通过本书的产品页面获取，网址为 [`www.apress.com/9781484230534`](http://www.apress.com/9781484230534)。

### Windows

我建议您创建一个名为 `c:\VKHCG` 的目录来处理本书中的所有示例。然后，从 GitHub 下载 `DS_VKHCG_Windows.zip` 文件并将其解压到此目录中。

### Linux

我也建议您创建一个名为 `./VKHCG` 的目录来处理本书中的所有示例。然后，从 GitHub 下载 `DS_VKHCG_Linux.tar.gz` 文件并将其解压到此目录中。

**警告：** 如果您将此目录更改为新位置，则需要将示例脚本中的所有内容都更改为指向这个新位置，才能从示例中获得最大收益。

这些文件用于创建示例公司的脚本和数据目录，我将使用它来指导您完成本书其余部分的流程和示例。

## 现在是时候见见您的客户了

Vermeulen-Krennwallner-Hillman-Clark 集团（VKHCG）是一家虚构的中型国际公司。它由四家子公司组成：Vermeulen PLC、Krennwallner AG、Hillman Ltd 和 Clark Ltd。

### Vermeulen PLC

Vermeulen PLC 是一家数据处理公司，负责处理集团内所有公司的数据，这是其对集团职责的一部分。该公司负责业务的所有信息技术方面。

这就是您刚刚被聘为数据科学家的公司。祝您未来一切顺利。

该公司提供的服务包括：

- 数据科学
- 网络、服务器和通信系统
- 内部和外部网站
- 数据分析业务活动
- 决策科学
- 流程自动化
- 管理报告

就本书而言，我将解释在框架的每个部分您需要研究哪些其他技术，但示例将仅集中于讨论中的特定概念，因为整个数据科学领域比所选的一些示例更为全面。

通过示例，我将帮助您构建一个基础的数据科学技术栈，然后通过额外的讨论为您提供建议，说明如何使该技术栈能够大规模运行。

这些示例将向您展示如何处理以下业务数据：

- 客户
- 产品
- 地点
- 业务流程
- 一些有用的数据科学算法

我将解释如何：

- 使用地理空间分析创建网络路由图
- 使用图论为作业调度构建有向无环图（DAG）

如果您想更详细地了解公司的数据，可以浏览公司示例目录中的这些数据集（`./VKHCG/01-Vermeulen/00-RawData`）。

在本章后面，我将更详细地引导您了解每个数据集。

### Krennwallner AG

Krennwallner AG 是一家广告和媒体公司，为集团的客户准备广告和媒体内容。

它提供的服务包括：

- 广告牌广告
- 在线投放的广告和内容管理
- 关键客户的活动管理

通过多种技术，它记录谁观看了哪些媒体流。我们将详细阐述的具体需求是如何识别需要观看特定媒体内容的客户群体。我将解释如何：

- 为特定的广告牌挑选内容
- 了解每个国家在线网站访问者的数据
- 为新天鹅堡的前十大客户策划活动

如果您想更深入地了解公司的数据，请浏览公司示例目录中的示例数据集（`./VKHCG/02-Krennwallner/00-RawData`）。

### Hillman Ltd

Hillman 公司是一家供应链和物流公司。它为业务提供全球供应链解决方案，包括：

- 第三方仓储
- 国际运输
- 门到门物流

我将通过示例展开讨论的主要需求是如何设计客户在线购买产品的配送。

通过示例，我将追踪产品从工厂到仓库，以及从仓库到客户家门的整个过程。

我将解释如何：

- 规划英国境内仓库的位置
- 规划最佳匹配国际物流的运输规则
- 为给定的一组产品选择最佳的集装箱打包方案
- 为苏格兰的一组客户创建最优配送路线

如果您想更详细地了解公司的数据，可以浏览公司示例目录中的数据集（`./VKHCG/03-Hillman/00-RawData`）。

### Clark Ltd

Clark 公司是一家风险投资和会计公司，负责处理集团的以下财务职责：

- 财务洞察
- 风险资本管理
- 投资规划
- 外汇交易

我将利用集团公司的财务方面来解释如何将实用的数据科学和数据工程应用于虚构财务数据的常见问题。

我将向您解释如何准备：

- 一个简单的外汇交易规划器
- 会计比率：
  - 盈利能力
  - 销售毛利润
  - 税后销售毛利润
  - 已动用资本回报率（ROCE）
  - 资产周转率
  - 存货周转率
  - 应收账款周转天数
  - 应付账款周转天数



### 处理生态系统

五年前，VKHCG 将其集中处理需求转移至 Vermeulen PLC，作为集团服务执行数据科学，从而整合了其处理能力。这导致其他集团公司承担了集团 20%的业务活动；然而，合并后集团业务活动中 90%的数据处理工作被重新分配给了核心团队。自那时起，Vermeulen 已将 `Spark`、`Python`、`Mesos`、`Akka`、`Cassandra`、`Kafka`、`elastic search` 和 `MQTT`（MQ 遥测传输）处理整合为一个集团服务提供商和处理实体。

在示例中，我将使用 `R` 或 `Python` 进行数据处理。我还将讨论互补技术，并就您在自己的环境中应考虑和请求的内容提供建议。

**注意：** 互补技术在数据科学环境中经常使用。尽管我简要介绍了它们，但这并不意味着它们不重要。

VKHCG 在 80%的公司业务活动中使用 `R` 处理引擎进行数据处理，其余 20%由 `Python` 完成。因此，我们将准备一个 `R` 和一个 `Python` 环境来执行示例。如果您因为特定的业务需求需要这些额外的环境，我将快速告知您如何获取它们。

我将简要介绍示例中未使用但已知有益的技术。

### Scala

`Scala` 在数据科学界很受欢迎，因为它支持大规模并行处理。您可以从以下核心网站安装该语言：[`www.scala-lang.org/download/`](http://www.scala-lang.org/download/)。提供了速查表和参考资料，可引导您找到帮助掌握此编程语言的资源。

**注意：** 我的许多大客户正在使用 `Scala` 作为他们的战略性开发语言。

### Apache Spark

`Apache Spark` 是一个用于大规模数据处理的快速通用引擎，目前是大型数据科学项目中增长最快的处理引擎。您可以从以下核心网站安装该引擎：[`http://spark.apache.org/`](http://spark.apache.org/)。对于大型项目，我在 `DataStax Enterprise` ([`www.datastax.com`](http://www.datastax.com))、`Hortonworks` ([`https://hortonworks.com/`](https://hortonworks.com/))、`Cloudera` ([`www.cloudera.com/`](http://www.cloudera.com/)) 和 `MapR` ([`https://mapr.com/`](https://mapr.com/)) 中使用 `Spark` 环境。

**注意：** `Spark` 现在是规模化数据处理中最受欢迎且最抢手的通用处理引擎，其支持与日俱增。如果您希望在规模化数据科学领域发展职业生涯，我建议您掌握此引擎。

### Apache Mesos

`Apache Mesos` 从机器（物理或虚拟）中抽象出 CPU、内存、存储和其他计算资源，使容错且弹性的分布式系统能够轻松地构建并有效运行处理解决方案。它在业界已被证明可扩展到数万个节点。这使数据科学家能够以高效的方式运行大规模并行分析和处理。处理环境可从以下核心网站获取：[`http://mesos.apache.org/`](http://mesos.apache.org/)。我想特别提一下 `Mesosphere Enterprise DC/OS`，因为我在许多项目中都使用它。详情请参阅 [`https://mesosphere.com`](https://mesosphere.com)。

**注意：** `Mesos` 是一种经济高效的处理方法，支持规模化处理环境中不断增长的动态处理需求。

### Akka

`Akka` 支持构建强大的并发和分布式应用程序，以执行大规模并行处理，同时共享公共的规模化处理平台。您可以从以下核心网站安装该引擎：[`http://akka.io/`](http://akka.io/)。我在 `Mesosphere Enterprise DC/OS` 环境中使用 `Akka` 处理。

### Apache Cassandra

`Apache Cassandra` 数据库在不牺牲性能的前提下提供可扩展性和高可用性支持。它具有线性可扩展性和良好的容错性，被众多大型公司广泛使用。您可以从以下核心网站安装该引擎：[`http://cassandra.apache.org/`](http://cassandra.apache.org/)。

我在 `Mesosphere Enterprise DC/OS` 环境中使用 `Cassandra` 处理，并使用 `DataStax Enterprise` 进行我的 `Cassandra` 安装。

**注意：** 我建议您将 `Cassandra` 视为一种规模化数据库，因为它以稳定的数据处理能力支持数据科学环境。

### Kafka

`Kafka` 用于构建实时数据管道和流式应用。它具有水平可扩展性、容错性且速度惊人。您可以从以下核心网站安装该引擎：[`http://kafka.apache.org/`](http://kafka.apache.org/)。我在 `Mesosphere Enterprise DC/OS` 环境中使用 `Kafka` 处理来处理进入我数据科学环境的数据。

**注意：** 我建议您将 `Kafka` 视为一种数据传输工具，因为它以强大的数据收集设施支持数据科学环境。

### 消息队列遥测传输

消息队列遥测传输（MQTT）是一种机器对机器（M2M）和物联网连接协议。它是一种特别轻量级的发布/订阅消息传输协议。它能够连接到代码占用空间小且网络带宽不足是通信障碍的位置。详情请参阅 [`http://mqtt.org/`](http://mqtt.org/)。

**注意：** 此协议在传感器环境中很常见，因为它提供了传感器所需的小代码占用空间和较低带宽。

现在我已经介绍了您应该了解但不会在示例中使用的项目，让我们来看看您将实际用到的东西。

## 示例生态系统

示例需要以下环境。VKHCG 环境中需要的两种设置是 `Python` 和 `R`。

### Python

`Python` 是由 Guido van Rossum 创建并于 1991 年首次发布的高级编程语言。如今，它的声誉日益增长，因为各种培训机构都将该语言作为其数据科学课程的一部分。

我建议您安装 `Anaconda`，以增强您的 `Python` 开发。它是 `Python` 的一个开源发行版，简化了功能包的管理和部署（请参阅 [`www.continuum.io/downloads`](http://www.continuum.io/downloads)）。

### Ubuntu

我使用 `Ubuntu` 桌面和服务器安装来执行数据科学（请参阅 [`www.ubuntu.com/`](http://www.ubuntu.com/)），如下所示：

```
sudo apt-get install python3 python3-pip python3-setuptools
```

### CentOS/RHEL

如果您想使用 `CentOS/RHEL`，我建议您采用以下安装过程：

```
sudo yum install python3 python3-pip python3-setuptools
```

### Windows

如果您想使用 `Windows`，我建议您采用以下安装过程：

从 [`www.python.org/downloads/windows/`](http://www.python.org/downloads/windows/) 下载软件。

### Python3 就绪了吗？

安装完成后，您必须按如下方式测试您的环境：

```
python3 --version
```

成功后，您应该会看到类似这样的响应：

```
Python 3.4.3+
```

恭喜，`Python` 现在已准备就绪。



#### [P](https://www.python.org/)ython 库

Python 最显著的特性之一是其丰富的库，这些库广泛可用，让你能够轻松将经过验证的数据科学流程集成到自己的环境中。

如需探索更多扩展包，建议查看 PyPI（Python 包索引，[`https://pypi.python.org/`](https://pypi.python.org/)）。

你需要安装一组有限的 Python 库，才能完成后续示例。

**警告**  
请确保你已验证所使用的所有包。切记：开源即“开放”——务必保持警惕！

##### Pandas

该库为你的数据科学工作提供了一套高性能的数据结构和数据分析工具。

### Ubuntu

使用以下命令安装：

```
sudo apt-get install python-pandas
```

###### Centos/RHEL

使用以下命令安装：

```
yum install python-pandas
```

###### PIP

使用以下命令安装：

```
pip install pandas
```

更多关于 Pandas 开发的信息，请访问 [`http://pandas.pydata.org/`](http://pandas.pydata.org/)。建议参考速查表（[`https://github.com/pandas-dev/pandas/blob/master/doc/cheatsheet/Pandas_Cheat_Sheet.pdf`](https://github.com/pandas-dev/pandas/blob/master/doc/cheatsheet/Pandas_Cheat_Sheet.pdf)），它可以帮助你快速掌握 Pandas 的基本用法。

我将通过示例讲解如何使用这些 Pandas 工具。

**注意**  
建议你熟练掌握此库，因为它将支持你完成大部分数据加载和存储流程，从而实现完整的数据科学处理。

##### Matplotlib

Matplotlib 是一个 Python 2D 和 3D 绘图库，可以生成各种图表，包括曲线图、直方图、功率谱图、条形图、误差图、散点图，以及数据科学成果的无限高级可视化。

### Ubuntu

使用以下命令安装：

```
sudo apt-get install python-matplotlib
```

### CentOS/RHEL

使用以下命令安装：

```
sudo yum install python-matplotlib
```

###### PIP

使用以下命令安装：

```
pip install matplotlib
```

访问 [`http://matplotlib.org/`](http://matplotlib.org/) 了解更多关于可视化的详细信息，你可以通过该库包含的练习来实现这些功能。

**注意**  
建议你花时间精通可视化技能。没有这些技能，几乎无法有效传达你的数据科学成果。

##### NumPy

NumPy 是科学计算的基础库，基于通用的同构多维数组结构及其处理工具。访问 [`www.numpy.org/`](http://www.numpy.org/) 了解更多详情。

我将在示例中使用其中部分工具，但建议你多加练习通用工具，这将有助于你未来的数据科学工作。

##### SymPy

SymPy 是一个用于符号数学的 Python 库。它可以帮助你在将复杂代数公式嵌入代码之前进行简化。访问 [`www.sympy.org`](http://www.sympy.org) 了解该库的功能详情。

##### Scikit-Learn

Scikit-Learn 是一套高效的数据挖掘和数据分析工具集，提供对数据科学分类、回归、聚类、降维，以及特征提取和归一化的预处理支持。该工具支持监督学习和非监督学习流程。

我将在示例中使用该库的许多功能。访问 [`http://scikit-learn.org`](http://scikit-learn.org) 了解更多关于这个功能广泛库的详细信息。

恭喜！你现在已经准备好运行 Python 示例了。接下来，我将引导你完成 R 环境的第二个配置步骤。

### R

R 是用于统计计算和图形处理的核心引擎。请从 [`www.r-project.org/`](http://www.r-project.org/) 下载软件，并根据你需要的特定 R 安装版本，遵循安装指南进行操作。

### Ubuntu

使用以下命令安装：

```
sudo apt-get install r-base
```

### CentOS/RHEL

使用以下命令安装：

```
sudo yum install R
```

### Windows

从 [`https://cran.r-project.org/bin/windows/base/`](https://cran.r-project.org/bin/windows/base/) 安装与你环境匹配的软件。

### 开发环境

VKHCG 内部的数据科学和工程工作使用 RStudio 开发环境。

#### R Studio

RStudio 提供了一个轻松便捷的 R 生态系统，包含代码编辑器、调试工具和可视化工具集。请从 [`www.rstudio.com/`](http://www.rstudio.com/) 下载相关软件，并根据你需要的特定版本，遵循安装指南进行操作。

### Ubuntu

使用以下命令安装：

```
wget https://download1.rstudio.org/rstudio-1.0.143-amd64.deb
sudo dpkg -i *.deb
rm *.deb
```

### CentOS/RHEL

使用以下命令安装：

```
wget https://download1.rstudio.org/rstudio-1.0.143-x86_64.rpm
sudo yum install --nogpgcheck rstudio-1.0.143-x86_64.rpm
```

### Windows

安装 [`https://download1.rstudio.org/RStudio-1.0.143.exe`](https://download1.rstudio.org/RStudio-1.0.143.exe)。

### R 包

建议安装以下额外的 R 包，以增强默认的 R 环境。

#### Data.Table 包

Data.Table 使你能够更高效地处理数据文件。建议多加练习使用 Data.Table 处理数据，这样你就能在 R 环境中快速处理数据，并有能力处理高达 100GB 的数据集。

文档可在 [`https://cran.r-project.org/web/packages/data.table/data.table.pdf`](https://cran.r-project.org/web/packages/data.table/data.table.pdf) 获取。请访问 [`https://CRAN.R-project.org/package=data.table`](https://cran.r-project.org/package=data.table) 获取该包的最新信息。

要安装此包，建议你打开 RStudio IDE 并使用以下命令：

```
install.packages ("data.table")
```

#### ReadR 包

ReadR 包支持将文本数据快速加载到 R 环境中。文档可在 [`https://cran.r-project.org/web/packages/readr/readr.pdf`](https://cran.r-project.org/web/packages/readr/readr.pdf) 获取。请访问 [`https://CRAN.R-project.org/package=readr`](https://cran.r-project.org/package=readr) 获取该包的最新信息。

要安装此包，建议你打开 RStudio IDE 并使用以下命令：

```
install.packages("readr")
```

建议你通过导入和导出不同格式的文件来练习，以理解该包的工作原理并掌握其用法。同时，建议你深入研究 ReadR 包中的以下函数：

- `Spec_delim()`：支持在无需将文件读入内存的情况下获取文件规格
- `read_delim()`：支持将分隔符文件读入 R 环境
- `write_delim()`：将 R 环境中的数据导出到磁盘文件



### JSONLite 包

该包能够让你轻松处理 JSON 文件，因为它是一个专为统计数据优化的 JSON 解析器和生成器。

文档地址位于 [`https://cran.r-project.org/web/packages/jsonlite/jsonlite.pdf`](https://cran.r-project.org/web/packages/jsonlite/jsonlite.pdf)。关于该包的最新信息，请参阅 [`https://CRAN.R-project.org/package=jsonlite`](https://cran.r-project.org/package=jsonlite)。要安装该包，建议你打开 RStudio IDE 并使用以下命令：

```
install.packages("jsonlite")
```

我还建议你研究该包中的以下函数：

*   `fromJSON()`：该函数支持你从 JSON 数据源直接导入数据到 R 环境。
*   `prettify()`：通过格式化 JSON 来提高人类可读性，使其更易于阅读。
*   `minify()`：移除所有 JSON 缩进/空白，使其具有机器可读性并经过优化。
*   `toJSON()`：将 R 数据转换为 JSON 格式的数据。
*   `read_json()`：从磁盘文件读取 JSON。
*   `write_json()`：将 JSON 写入磁盘文件。

#### Ggplot2 包

数据可视化是数据科学家的一项重要技能。该包为你提供了一个环境，用于为数据构建复杂的图形格式。它在创建详细图形方面非常成功，因此被称为“图形语法”。文档地址位于 [`https://cran.r-project.org/web/packages/ggplot2/ggplot2.pdf`](https://cran.r-project.org/web/packages/ggplot2/ggplot2.pdf)。关于该包的最新信息，请参阅 [`https://CRAN.R-project.org/package=ggplot2`](https://cran.r-project.org/package=ggplot2)。

要安装该包，建议你打开 RStudio IDE 并使用以下命令：

```
install.packages("ggplot2")
```

我建议你掌握这个包，它能让你将数据转化为图形，并向你的业务方展示结果的价值。

我们现在已安装的包将支持后续示例。

