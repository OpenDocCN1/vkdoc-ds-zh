# 13. 安装和配置 Giraph 与 Hadoop

本章涵盖：
- 运行 Giraph 和 Hadoop 的系统要求
- Giraph 和 Hadoop 的安装方法
- 为 Giraph 应用程序运行 Hadoop 的不同模式
- 为所有三种不同运行模式配置 Hadoop

在本书中，一直假定您有一个可用的 Hadoop 安装环境来运行示例和试验 Giraph。由于 Giraph 运行在 Hadoop 之上，拥有一个可用的 Hadoop 环境是基本前提。本附录首先探讨 Hadoop 和 Giraph 部署的系统依赖关系。接着，它描述了安装 Hadoop 和 Giraph 的各种方法，展示了它们各自的优缺点。然后，它探讨了不同类型的 Hadoop 部署，以及 Giraph 如何处理其需要利用的 Hadoop 生态系统项目的不同版本。最后，本附录概述了 Hadoop 和 Giraph 配置管理的基础知识，并讨论了在不同执行模式下运行 Giraph 所需的 Hadoop 配置。

## 系统要求
Giraph 和 Hadoop 都是使用 Java 编程语言实现的，可以在基于 Unix 的系统、Mac OS X 和 Windows 系统上运行。Giraph 需要 Oracle JDK 7 或更高版本。由于 Giraph 任务由运行 Hadoop 框架的同一个 JVM 执行，这为 Hadoop 部署可使用的 JDK 版本设置了下限。这意味着，如果您有一个需要用于运行 Giraph 应用程序的现有 Hadoop 集群，您必须确保该集群是使用 JDK 7 或更高版本部署的。

您还需要在将要启动 Giraph 应用程序的主机上以及在将用于 Giraph 应用程序开发的主机上安装 JDK。Giraph 和 Hadoop 都在 Oracle 的 JDK 上经过了广泛测试，OpenJDK（Oracle JDK 的一个开源、社区驱动的版本）是部署的次优选择。如果您尚未安装 JDK 且想使用 Oracle 版本，请访问 `www.java.com/jdk` 并按照您操作系统的安装步骤操作。另一方面，如果您决定选择 OpenJDK，您可能会发现它已由供应商为您打包在操作系统中。

无论您如何安装 JDK，请确保安装目录的位置通过环境变量 `JAVA_HOME` 对所有应用程序可用。如果您希望该位置也能提供所有 Java 命令行实用程序（包括启动 JVM 本身）的二进制文件，您可能需要使用该设置更新您的 `PATH`。在 Unix 平台上，通常可以方便地在全局 shell 启动文件（如 `/etc/profile`、`~/.bashrc` 或 `~/.bash_profile`）中设置这些值，类似于 A-1 所示。

**代码清单 A-1** 设置 `JAVA_HOME` 并将 Java 二进制文件添加到 PATH 中

```
JAVA_HOME=/usr/lib/jvm/default-java
export JAVA_HOME
PATH=$JAVA_HOME/bin:$PATH
```

> **注意：** 在操作系统方面，各种 Linux 发行版是测试最广泛的部署平台。虽然可以在 Mac OS X 甚至 Windows 上运行 Giraph 和 Hadoop，但这些平台主要用于开发。

### Hadoop 安装
除非您已经有一个可用的 Hadoop 集群来运行 Giraph 应用程序（无论是本地部署还是云服务），否则您的第一步是决定要使用哪个主要版本的 Hadoop。目前，有两个主要版本可用：Hadoop 1.x 和 Hadoop 2.x。这两个版本都被认为是稳定的，可以安全地用于生产环境。两者的区别归结为：Hadoop 1.x 正逐渐过渡到维护模式，开发活动很少。相比之下，Hadoop 2.x 的开发活动仍然非常活跃，错误修复和新功能开发都在快速推进。这两个版本之间的另一个根本区别是 MapReduce 框架的架构。Hadoop 1.x 忠实实现了原始 Google 论文中描述的 MapReduce 框架，而 Hadoop 2.x 则更进一步，本质上提供了 MapReduce v2 的实现，作为一个位于通用资源调度器 YARN 之上的应用程序。

#### MapReduce v1 有什么问题？
MapReduce 的原始实现（现在称为 MapReduce v1）在架构上决定将特定于 MapReduce 的逻辑与更底层的集群资源管理和调度混为一谈。虽然这为功能性实现提供了最快的途径（并为 Hadoop 的世界统治铺平了道路），但它也存在许多技术限制：可扩展性问题、容错性差、以及在同一集群上运行非 MapReduce 框架的困难是其中的主要问题。事实上，正如您在第 6 章中看到的，Giraph 实现必须欺骗 MapReduce v1，让它认为 Giraph 是作为一个通用的 map-reduce 应用程序在运行，而实际上，Giraph 应用程序根本不符合通用的 MapReduce 模型。

Hadoop 2.x 试图通过提供一个称为 YARN（Yet Another Resource Negotiator，另一种资源协调者）的独立层来提供底层集群资源管理和调度能力，并在 YARN 之上运行 MapReduce 框架，从而解决这些限制。这种架构使得其他分布式框架可以与 MapReduce 应用程序并排运行，而无需假装映射到 MapReduce 模型。值得重申的是，这纯粹是一个实现层面的变化；所有现有的 MapReduce v1 应用程序仍然与 MapReduce v2 API 兼容。尽管 MapReduce 工作负载如今仍主导着 Hadoop 2.x 集群，但人们对于将其他分布式计算框架移植到 YARN 上运行有浓厚的兴趣：Giraph、Apache Spark（用于数据处理的内存引擎）和 Hamster（OpenMPI）只是其中的几个例子。

在本书中，您一直使用 Hadoop 1.x 实现，通过纯 map 的 MapReduce 作业来运行示例。同样的工作流程仍然适用于在 Hadoop 2.x MapReduce 实现上运行。尽管您还没有探索如何在 YARN 上运行（因为它仍被认为有些实验性质），但如果您决定安装 Hadoop 2.x，除了通过 MapReduce 运行 Giraph 之外，您还可以尝试将 Giraph 作为 YARN 客户端运行。无论您选择哪个 Hadoop 版本，下一步都是在您的主机上安装 Hadoop 的二进制发行版。

除非您需要担心在大型 Linux 主机集群上安装 Hadoop，否则获取 Hadoop 二进制发行版的最简单方法是从 Apache 软件基金会 Hadoop 发布页面 `http://hadoop.apache.org/releases.html` 下载打包为 gzip 压缩 tar 文件的稳定版本。

将下载的文件解压到您文件系统中的某个子目录中（稍后您将使用相同的子目录来安装 Giraph），如清单 A-2 所示。

**清单 A-2.** 在 Unix 或 Mac OS X 工作站上安装 `Hadoop 1.2.1`

```
$ mkdir ∼/dist
$ cd ∼/dist
$ tar xzf hadoop-1.2.1.tar.gz
```

至此，您已拥有 Hadoop 的二进制安装，并且需要将该安装的位置暴露给您将要运行的所有其他命令行工具。这可以通过设置几个环境变量来实现，如清单 A-3 所示（别忘了，您可以像之前添加 `JAVA_HOME` 一样，将它们添加到您的 profile 文件中）。

**清单 A-3.** 设置环境以运行 Hadoop 应用程序

```
$ HADOOP_HOME=∼/dist/hadoop-1.2.1
$ export HADOOP_HOME
$ PATH=$HADOOP_HOME/bin:$PATH
$ hadoop version
Hadoop 1.2.1
Subversion https://svn.apache.org/repos/asf/hadoop/common/branches/branch-1.2
Compiled by mattf on Mon Jul 22 15:23:09 PDT 2013
From source with checksum 6923c86528809c4e7e6f493b6b413a9a
This command was run using /Users/shapor/dist/hadoop-1.2.1/hadoop-core-1.2.1.jar
```

清单 A-3 中的最后一个命令通过运行 `hadoop` 命令行工具并查看预期输出来证明 Hadoop 安装成功。这是确保您的 Hadoop 安装正确并且可以在您的系统上找到 Java 的最简单方法。

> **注意**：按照描述在所有集群机器上安装 Hadoop。

尽管上述安装 Hadoop 的方法非常简单，并且应该适用于大多数操作系统，但您可能希望使用包管理器从二进制发行版安装。这样做将保证 Giraph 和 Hadoop 二进制文件来自同一个发行版，并且它们已经过集成和测试，可以协同工作。以下部分提供了有关如何进行此操作的更多信息。

### Giraph 安装

现在你已经完成了 Hadoop 的安装，可能会觉得在系统上安装 Giraph 理应更简单。好消息是，由于 Giraph 恰好是一个纯客户端应用，它不需要安装在集群的所有主机上。只需要让 Giraph 安装在集群中通常提交 MapReduce 或 YARN 任务的某台机器上即可（这些机器被称为网关节点或边缘节点）。坏消息是，与 Hadoop 不同，选择与集群中部署的其他 Hadoop 生态系统组件兼容的现成 Giraph 二进制发行版可能并非易事。

这里的棘手问题在于依赖关系。Giraph 至少有一个对 Hadoop 的基本依赖；但实际情况是，其依赖项集合至少与 Giraph I/O 格式需要交互的 Hadoop 生态系统项目数量一样多。Apache Hadoop 生态系统项目有幸从开源和商业社区的不同贡献者那里获得了大量代码贡献。项目的高速发展导致代码库经历了多次重大重构，也催生了许多基于 Hadoop 发展历程中不同节点的商业产品。

总体而言，Hadoop 生态系统开发社区值得称赞，因为他们非常注重公共 API 层面的向后兼容性。就像 Linux 内核禁止通过更改公共 API 来破坏用户态应用程序一样，Hadoop 在不过度影响应用程序开发者方面有着良好的记录。除了一些小错误，针对 Hadoop 1.x 编写的 MapReduce 应用程序（且未使用任何私有或不断演进的 API）应该能在未修改的情况下运行于 Hadoop 2.x。但问题是，这需要对应用程序的 Java 代码进行重新编译。

这对 Giraph 的二进制版本意味着，在构建时它们必须精确地指向运行时集群中将要部署的同一版本的 Hadoop（以及 Hadoop 生态系统项目）。至少，Giraph 必须发布两个二进制版本：一个针对 Hadoop 1.x 构建，另一个针对 Hadoop 2.x 构建。然而，这仍然没有考虑到其他依赖项的差异，例如 Hive、HBase 等。

这个问题并非 Giraph 特有；它被称为“依赖组合爆炸”：不同版本依赖项的组合数量会随着依赖项数量的增加而呈指数级增长。

迄今为止，软件工程界已经开发出两种不同的策略来处理依赖组合爆炸问题：

-   作为软件安装过程的一部分，从源代码构建二进制文件

-   发布紧密集成组件的完整栈（或二进制软件分发版），而不是提供单个组件的独立二进制文件并期望任意版本组合能协同工作

在你深入了解这些策略的详细描述之前，我们首先要认识到第一个策略实际上是第二个策略的子集。决定从源代码构建你的 Giraph 安装，实际上意味着你正在着手一项任务：生成你自己的二进制软件分发版，其中包含部署到你集群上的组件确切版本。

#### 软件项目 vs. 软件栈

有趣的是，一般而言，Apache 软件基金会试图避免发布其项目的二进制发行版，而是将这一责任留给下游打包者。对于成熟的项目（例如 Apache HTTP 服务器），这些打包者通常是各种操作系统（Linux 等）的分发者。操作系统分发者会确保最终进入其特定操作系统版本的所有各种 ASF 软件项目能够彼此顺畅地协同工作。然而，Hadoop 及其生态系统项目尚未被列入操作系统打包者的议程。

为了填补这一空白，各种 Hadoop 发行版的商业供应商开始提供完全集成的软件包，这些软件包不需要重新编译，并且已知能够彼此良好协作。这些商业发行版大多基于 Apache Bigtop 的工作：一个 100% 开源、社区驱动的 Apache Hadoop 大数据管理发行版。对于大多数 Hadoop 及其生态系统项目的用户来说，安装来自 Apache Bigtop 或商业供应商的完全集成发行版是入门最简便的方式。对于那些拥有现有集群的用户来说，从源代码编译 Giraph 以匹配集群上部署的依赖项的确切版本，可能是唯一的选择。

### 安装 Giraph 的二进制发行版

如前所述，安装二进制发行版是最简单的选择，但也是限制最多的。如果你决定走这条路，基本上就是让 Giraph 的二进制发行版来决定你将使用的 Hadoop 和 Hadoop 生态系统组件的版本。虽然它对于实际工作可能用处不大——除了快速搭建一个用于运行本书示例的环境之外——但它很简单，并且与本附录开头描述的 Hadoop 安装过程非常相似。

与 Hadoop 一样，Giraph 也有两个可用的二进制发行版：一个针对 Hadoop 1.x 的最新版本构建，另一个针对 Hadoop 2.x 的最新版本构建。就像 Hadoop 的二进制发行版一样，Giraph 的二进制发行版被打包成 gzip 压缩的 tar 文件。每个发布的 Giraph 版本都包含两个二进制工件：一个针对 Hadoop 1.x 的 Giraph 二进制发行版（可下载为 `giraph-dist-X.Y.Z-bin.tar.gz`），以及一个针对 Hadoop 2.x 的 Giraph 二进制发行版（可下载为 `giraph-dist-X.Y.Z-hadoop2-bin.tar.gz`）。这两个 gzip 压缩的 tar 文件都可以从 Giraph 项目网站 [`giraph.apache.org/releases.html`](http://giraph.apache.org/releases.html) 下载。

由于本书一直使用在 Hadoop 1.2.1 上运行的 Giraph 1.1.0 的示例，你需要获取的二进制文件是 `giraph-dist-1.1.0-bin.tar.gz`。下载该二进制文件后，请确保将其解压到之前安装 Hadoop 的同一台工作站上。请注意，解压归档文件后创建的顶层文件夹在其名称中嵌入了 Hadoop 1.x 的确切版本，看起来像 `giraph-1.1.0-for-hadoop-1.2.1`。另外，请记住，在真正的集群上，这是一个网关节点。如果你不知道你的网关节点在哪里，请咨询你的 Hadoop 管理员。无论你是在笔记本电脑还是网关节点的安装，都必须按照清单 A-4 所示的一系列步骤进行操作。

```
Listing A-4. 安装为在 Hadoop 1.2.1 上运行而构建的 Giraph 1.1.0

$ cd ∼/dist
$ tar xzf giraph-dist-1.1.0-bin.tar.gz
$ GIRAPH_HOME=∼/dist/giraph-1.1.0-for-hadoop-1.2.1
$ export GIRAPH_HOME
$ PATH=$GIRAPH_HOME/bin:$PATH
$ giraph
   Usage: giraph [-D<Hadoop property>] <jar containing vertex>
                 <parameters to jar>
   At a minimum one must provide a path to the jar containing the vertex to be executed.
```

如你所见，清单 A-4 与你使用 Hadoop 所做的（参见清单 A-3）非常相似，并且与 Hadoop 一样，清单中的最后一行证明了 Giraph 已安装并准备就绪。

### 作为打包版 Hadoop 发行版的一部分安装 Giraph

几乎所有的 Hadoop 集群实际部署都是在 Linux 操作系统上，以来自 Hadoop 二进制软件分发的原生 Linux 软件包（DEB 或 RPM）形式进行的。这些软件包有两个来源：Hadoop 发行版的商业供应商和 Apache Bigtop。鉴于几乎所有商业 Hadoop 发行版都源自 Apache Bigtop，本节将重点介绍它。Bigtop 与商业发行版之间的区别归结为技术支持（你无法购买 Bigtop 的技术支持）以及新版 Hadoop 生态系统组件被整合到打包中的速度（Bigtop 往往比商业发行版更快）。

将 Hadoop 及其生态系统项目作为 Linux 上打包版 Hadoop 发行版的一部分进行安装，可以确保每个组件都已集成，并且已知能与来自同一发行版的其他组件协同工作。不仅如此，这些软件包还通过遵循特定 Linux 发行版的打包指南，确保与底层 Linux 操作系统良好集成。最终结果是，以这种方式使用安装好的 Hadoop，与使用任何随 Linux 操作系统捆绑的系统软件并无二致。这种安装方法的唯一缺点在于它仅限于 Linux（尽管 Max OS X 的 brew 移植版正在开发中），并且需要提升超级用户权限。请务必与你的系统管理员沟通（或查阅 Linux 操作系统文档），以便你的账户能够使用`sudo(8)`以提升权限运行命令。

