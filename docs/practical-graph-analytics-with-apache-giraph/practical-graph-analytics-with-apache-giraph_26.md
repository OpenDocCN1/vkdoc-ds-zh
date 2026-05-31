# 13. 安装和配置 Giraph 与 Hadoop

本章涵盖：

- 运行 Giraph 和 Hadoop 的系统要求
- Giraph 和 Hadoop 的安装方法
- 为 Giraph 应用程序运行 Hadoop 的不同模式
- 为所有三种不同运行模式配置 Hadoop

在本书中，一直假定您有一个可用的 Hadoop 安装环境来运行示例和试验 Giraph。由于 Giraph 运行在 Hadoop 之上，拥有一个可用的 Hadoop 环境是基本前提。本附录首先探讨 Hadoop 和 Giraph 部署的系统依赖关系。接着，它描述了安装 Hadoop 和 Giraph 的各种方法，展示了它们各自的优缺点。然后，它探讨了不同类型的 Hadoop 部署，以及 Giraph 如何处理其需要利用的 Hadoop 生态系统项目的不同版本。最后，本附录概述了 Hadoop 和 Giraph 配置管理的基础知识，并讨论了在不同执行模式下运行 Giraph 所需的 Hadoop 配置。

## 系统要求

Giraph 和 Hadoop 都是使用 Java 编程语言实现的，可以在基于 Unix 的系统、Mac OS X 和 Windows 系统上运行。Giraph 需要 Oracle JDK 7 或更高版本。由于 Giraph 任务由运行 Hadoop 框架的同一个 JVM 执行，这为 Hadoop 部署可使用的 JDK 版本设置了下限。这意味着，如果您有一个需要用于运行 Giraph 应用程序的现有 Hadoop 集群，您必须确保该集群是使用 JDK 7 或更高版本部署的。

您还需要在将要启动 Giraph 应用程序的主机上以及在将用于 Giraph 应用程序开发的主机上安装 JDK。Giraph 和 Hadoop 都在 Oracle 的 JDK 上经过了广泛测试，OpenJDK（Oracle JDK 的一个开源、社区驱动的版本）是部署的次优选择。如果您尚未安装 JDK 且想使用 Oracle 版本，请访问 [`www.java.com/jdk`](http://www.java.com/jdk) 并按照您操作系统的安装步骤操作。另一方面，如果您决定选择 OpenJDK，您可能会发现它已由供应商为您打包在操作系统中。

无论您如何安装 JDK，请确保安装目录的位置通过环境变量 `JAVA_HOME` 对所有应用程序可用。如果您希望该位置也能提供所有 Java 命令行实用程序（包括启动 JVM 本身）的二进制文件，您可能需要使用该设置更新您的 `PATH`。在 Unix 平台上，通常可以方便地在全局 shell 启动文件（如 `/etc/profile`、`~/.bashrc` 或 `~/.bash_profile`）中设置这些值，类似于 A-1 所示。

**代码清单 A-1** 设置 `JAVA_HOME` 并将 Java 二进制文件添加到 PATH 中

```
JAVA_HOME=/usr/lib/jvm/default-java
export JAVA_HOME
PATH=$JAVA_HOME/bin:$PATH
```

> **注意：** 在操作系统方面，各种 Linux 发行版是测试最广泛的部署平台。虽然可以在 Mac OS X 甚至 Windows 上运行 Giraph 和 Hadoop，但这些平台主要用于开发。


好的，作为高级文档工程师和翻译员，我将严格遵循您的注意事项和示例格式，将以下英文文本翻译成中文。


### Hadoop 安装

除非您已经有一个可用的 Hadoop 集群来运行 Giraph 应用程序（无论是本地部署还是云服务），否则您的第一步是决定要使用哪个主要版本的 Hadoop。目前，有两个主要版本可用：Hadoop 1.x 和 Hadoop 2.x。这两个版本都被认为是稳定的，可以安全地用于生产环境。两者的区别归结为：Hadoop 1.x 正逐渐过渡到维护模式，开发活动很少。相比之下，Hadoop 2.x 的开发活动仍然非常活跃，错误修复和新功能开发都在快速推进。这两个版本之间的另一个根本区别是 MapReduce 框架的架构。Hadoop 1.x 忠实实现了原始 Google 论文中描述的 MapReduce 框架，而 Hadoop 2.x 则更进一步，本质上提供了 MapReduce v2 的实现，作为一个位于通用资源调度器 YARN 之上的应用程序。

#### MapReduce v1 有什么问题？

MapReduce 的原始实现（现在称为 MapReduce v1）在架构上决定将特定于 MapReduce 的逻辑与更底层的集群资源管理和调度混为一谈。虽然这为功能性实现提供了最快的途径（并为 Hadoop 的世界统治铺平了道路），但它也存在许多技术限制：可扩展性问题、容错性差、以及在同一集群上运行非 MapReduce 框架的困难是其中的主要问题。事实上，正如您在第 6 章中看到的，Giraph 实现必须欺骗 MapReduce v1，让它认为 Giraph 是作为一个通用的 map-reduce 应用程序在运行，而实际上，Giraph 应用程序根本不符合通用的 MapReduce 模型。

Hadoop 2.x 试图通过提供一个称为 YARN（Yet Another Resource Negotiator，另一种资源协调者）的独立层来提供底层集群资源管理和调度能力，并在 YARN 之上运行 MapReduce 框架，从而解决这些限制。这种架构使得其他分布式框架可以与 MapReduce 应用程序并排运行，而无需假装映射到 MapReduce 模型。值得重申的是，这纯粹是一个实现层面的变化；所有现有的 MapReduce v1 应用程序仍然与 MapReduce v2 API 兼容。尽管 MapReduce 工作负载如今仍主导着 Hadoop 2.x 集群，但人们对于将其他分布式计算框架移植到 YARN 上运行有浓厚的兴趣：Giraph、Apache Spark（用于数据处理的内存引擎）和 Hamster（OpenMPI）只是其中的几个例子。

在本书中，您一直使用 Hadoop 1.x 实现，通过纯 map 的 MapReduce 作业来运行示例。同样的工作流程仍然适用于在 Hadoop 2.x MapReduce 实现上运行。尽管您还没有探索如何在 YARN 上运行（因为它仍被认为有些实验性质），但如果您决定安装 Hadoop 2.x，除了通过 MapReduce 运行 Giraph 之外，您还可以尝试将 Giraph 作为 YARN 客户端运行。无论您选择哪个 Hadoop 版本，下一步都是在您的主机上安装 Hadoop 的二进制发行版。

除非您需要担心在大型 Linux 主机集群上安装 Hadoop，否则获取 Hadoop 二进制发行版的最简单方法是从 Apache 软件基金会 Hadoop 发布页面 [`http://hadoop.apache.org/releases.html`](http://hadoop.apache.org/releases.html) 下载打包为 gzip 压缩 tar 文件的稳定版本。

将下载的文件解压到您文件系统中的某个子目录中（稍后您将使用相同的子目录来安装 Giraph），如清单 A-2 所示。

**清单 A-2.** 在 Unix 或 Mac OS X 工作站上安装 Hadoop 1.2.1

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
| `$ sudo apt-key add < KEYS` `$ sudo apt-get update` `$ sudo apt-get install giraph` | `$ sudo rpm --import KEYS` `$ sudo yum clean metadata` `$ sudo yum install giraph` | `$ sudo rpm --import KEYS` `$ sudo zypper update` `$ sudo install giraph` |

运行最后一条命令的一个有趣附带作用是，Giraph 与 Hadoop 之间的依赖关系会被正确识别，并且合适的 Hadoop 软件包会被隐式地安装到你的系统上。从某种意义上说，一旦你决定通过 Linux 软件包安装 Giraph，安装 Hadoop 的最佳（也是最简单的！）方式就是也以打包形式安装它。事实上，你甚至无需显式执行该步骤：系统会自动获取正确的 Hadoop 软件包。

如果你想知道通过 Linux 软件包安装 Giraph 后需要如何设置环境变量`HADOOP_HOME`和`GIRAPH_HOME`，好消息是你再也不必担心这些了。你可以像在 Linux 系统上运行任何可执行文件一样，直接运行`hadoop`和`giraph`命令行工具：只需输入其名称即可。

不可否认，通过 Linux 软件包安装是安装 Giraph 和 Hadoop 迄今为止最简单的方法。如果你选择商业供应商之一，还可以注册获得专业支持。然而，缺点是，通过安装预打包的组件，你将放弃为你的环境选择独特 Giraph 和 Hadoop 版本组合的权利。如果你发现自己需要将 Giraph 与自定义版本的 Hadoop 及其生态系统组件相匹配，那么通过从源代码构建来安装 Giraph 可能是你唯一的选择。



### 通过源码编译安装 Giraph

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

几乎所有 Hadoop 生态系统的成员（包括 Hadoop 本身）都共享通用的配置管理系统，该系统基于一组编码了键值对属性设置的平面 XML 文件。这些配置文件所在的文件夹因项目和安装方法而异。对于通过 Linux 软件包（DEB 或 RPM）安装的任何项目，配置文件的位置在 `/etc/<项目名称>/conf` 下。但是，如果是手动安装，相同的配置文件则位于项目的 `HOME` 文件夹下的 `conf` 子文件夹中。Hadoop 和 Giraph 的配置文件具体位于何处取决于安装方法，总结于表 A-5 中。

**表 A-5.** 按安装方法区分的配置文件位置

| 安装方法 | Hadoop 配置文件夹 | Giraph 配置文件夹 |
| --- | --- | --- |
| Linux 软件包 | `/etc/hadoop/conf` | `/etc/giraph/conf` |
| 手动安装 | `$HADOOP_HOME/conf` | `$GIRAPH_HOME/conf` |

在这些文件夹中，您会找到一个或多个 XML 文件，类似于清单 A-6 中所示的示例。请注意，虽然 Giraph 只需要一个名为 `giraph-site.xml` 的配置文件，但 Hadoop 将其配置分散在至少三个不同的文件中——`core-site.xml`、`hdfs-site.xml` 和 `mapred-site.xml`——而 `yarn-site.xml` 是用于配置作为 Hadoop 2.x 一部分的 YARN 的附加文件。

**清单 A-6.** Hadoop 配置文件示例

```
<configuration>
   <property>
    <name>prop.name</name>
    <value>prop.value</value>
    <description>关于此属性作用的自由格式描述</description>
  </property>
</configuration>
```


好的，作为一名高级文档工程师和翻译员，我将严格遵循您的注意事项和示例格式，将以下英文文本翻译成中文。


## 配置 Giraph

严格来说，默认情况下，Giraph 实际上不需要在其 XML 配置文件中进行任何设置。任何可能是必需的配置选项（例如指定工作节点数量）都可以通过其命令行工具传递给 Giraph。不过，很快就会因为不断地在命令行中输入所有必需的选项而感到沮丧，这时你就可以开始利用 `giraph-site.xml` 来存储特定于你环境的配置（只需确保将其放在表 A-5 列出的文件夹中即可）。例如，如果你发现在本地模式下配置的 Hadoop 之上运行 Giraph 应用程序，那么拥有一个类似于清单 A-7 的 Giraph 配置文件可能会很有用。

**清单 A-7.** 示例 Giraph 配置文件 `giraph-site.xml`

```
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

启用伪分布式模式的最简单方法是从 Linux 软件包安装 Hadoop。有一个名为 `hadoop-conf-pseudo` 的特殊软件包，它可以拉取所有正确的依赖项，并在 `/etc/hadoop/conf` 中预设伪分布式模式所需的配置参数。只需安装该软件包即可。

在无法从 Linux 软件包安装 Hadoop 的情况下，另一种选择是手动将 Hadoop 二进制文件安装到 `$HADOOP_HOME` 文件夹下（如本附录前面所述），并确保在适当的配置文件中设置表 A-6 中总结的属性。

**表 A-6.** 伪分布式模式所需的 Hadoop 配置

| 配置文件 | 属性名称 | 属性值 | 属性作用 |
| --- | --- | --- | --- |
| `core-site.xml` | `fs.default.name` | `hdfs://localhost/` | 为伪分布式 HDFS 文件系统设置 URL |
| `hdfs-site.xml` | `dfs.replication` | `1` | 设置复制因子为 1，因为在伪分布式模式下，你只有一个数据节点 |
| `mapred-site.xml` | `mapred.job.tracker` | `localhost:8021` | 为伪分布式 Job Tracker 设置 URL |
| `yarn-site.xml` | `yarn.nodemanager.aux-services` | `mapreduce_shuffle` | 让 YARN 知道 MapReduce 框架需要的额外服务 |
| `yarn-site.xml` | `yarn.nodemanager.aux-services.mapreduce_shuffle.class` | `org.apache.hadoop.mapred.ShuffleHandler` | 指定实现 `mapreduce_shuffle` 服务的类的名称 |

由于伪分布式模式需要运行一些进程，配置后的最后一步是启动必要的服务并初始化其状态。首先，你需要启动 HDFS。根据你是从 Linux 软件包安装还是从二进制 gzip 压缩的 tar 文件安装，使用表 A-7 中提供的说明。当然，如果你没有从软件包安装 Hadoop，不要忘记将 `$HADOOP_HOME` 添加到你的 `$PATH` 中，如清单 A-3 所示。

**表 A-7.** 在伪分布式模式下启动 HDFS

| 从 Linux 软件包安装 Hadoop | 从二进制 gzip 压缩的 tar 文件安装 Hadoop |
| --- | --- |
| `$ sudo service hadoop-hdfs-namenode format` `$ sudo service hadoop-hdfs-namenode start` `$ sudo service hadoop-hdfs-datanode start` `$ sudo –u hdfs hadoop fs –chmod 777 /` | `$ hadoop namenode –format` `$ hadoop-daemon.sh start namenode` `$ hadoop-daemon.sh start datanode` `$ hadoop fs –chmod 777 /` |

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
| `$ sudo service hadoop-yarn-resourcemanager start` `$ sudo service hadoop-yarn-nodemanager start` `$ cd /usr/lib/hadoop-mapreduce` | `$ hadoop-daemon.sh start resourcemanager` `$ hadoop-daemon.sh start nodemanager` `$ cd $HADOOP_HOME/share/hadoop/mapreduce` |

**表 A-8.** 在伪分布式模式下启动 MapReduce

| 通过 Linux 软件包安装的 Hadoop | 通过二进制 gzip 压缩 tar 文件安装的 Hadoop |
| --- | --- |
| `$ sudo service hadoop-mapred-jobtracker start` `$ sudo service hadoop-mapred-tasktracker start` `$ cd /usr/lib/hadoop` | `$ hadoop-daemon.sh start jobtracker` `$ hadoop-daemon.sh start tasktracker` `$ cd $HADOOP_HOME` |

无论你设置的是 Hadoop 1.x 还是 Hadoop 2.x，执行完这些命令后，请务必在当前工作目录（即上一条 `cd` 命令将你定位到的目录）中运行一个测试 MapReduce 作业。如果清单 A-9 中显示的命令成功运行至完成，则意味着你的伪分布式 Hadoop 集群已完全就绪，可以用于你的 Giraph 应用程序了。

**清单 A-9.** 验证是否能在伪分布式 Hadoop 集群上运行 MapReduce 作业

```
$ hadoop jar *examples*jar pi 10 1000
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


