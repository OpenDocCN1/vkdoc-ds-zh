# 云中的 Giraph

本章包含：

*   云计算的高级架构概述

*   对云中图处理提出的特定需求的分析

*   深入探讨在亚马逊提供的公有云上使用 Giraph

如你在前几章所学，`Giraph` 利用 `Hadoop` 集群作为其主要执行框架，并且可以从多个不同的数据源获取数据，而不仅仅是 `HDFS`。这使得 `Giraph` 应用非常适合在由各种公有和私有云计算平台提供的共享基础设施上运行。利用云计算服务，你可以专注于图处理，而供应商则负责部署和运营可扩展的 `Hadoop` 集群等事务。本章首先概述云计算服务，重点介绍那些最适合让 `Giraph` 应用尽可能无缝运行的服务。

由于并非所有云计算供应商都提供 `Hadoop` 集群即服务，我们将简要概述更基础但仍然有用的基础设施虚拟化服务。在需要对 `Giraph` 可用的 `Hadoop` 版本进行精确控制的情况下，这些类型的服务也会派上用场。

尽管当今有大量的公有云提供商，并且私有云实现的生态系统一如既往地强大，但本章主要关注亚马逊网络服务（`AWS`）。我们专注于 `AWS` 有两个原因。首先，它是最流行的公有云之一。最重要的是，在 `AWS` 上运行图处理作业的经验将使你使用其他公有云和私有云变得更加自然。在 `AWS` 中，我们将专注于一组称为 `Elastic MapReduce` 的 `API`，它允许你动态地创建任意数量的虚拟服务器，这些服务器对你的 `Giraph` 应用来说就像一个真正的 `Hadoop` 集群。尽管 `Giraph` 并非预装，但它仍然是将你的应用从单节点执行扩展到任意数量节点（只要你愿意付费）的最简单方法。安装、配置和管理 `Apache Hadoop` 的所有复杂性都对你隐藏了。

## 云计算快速入门

试想一下，在贵公司部署任何图处理应用程序所投入的精力。最起码，你需要购买和安装足以满足潜在数据集和处理需求的大量存储和计算硬件。在将这些硬件打造成一个功能完备的 `Hadoop` 集群之前，你还得花时间和金钱管理电力、冷却和网络。之后，你仍然需要在集群中的每个节点上安装并配置操作系统和 `Hadoop`。只有到那时，你才能开始收集数据并运行 `Giraph` 应用程序。当然，为了让一切保持运行，你必须监控任何可能出问题的地方（无论是硬件还是软件），并相应地维护集群。简而言之，这意味着大量的前期投入和有限的扩展性。换句话说，当你对数据集大小的需求发生变化时，你仍然受限于同一个集群。

避免这种初始投入和复杂性的一种方法是在云计算环境中运行你的 `Giraph` 应用程序，由他人负责设置和管理所有基础设施。

简单来说，云计算是一系列服务的集合，其核心理念是通过网络以可扩展且灵活的方式提供各种计算、软件和信息资源。云计算的另一个关键方面是自助服务的概念：它允许任何客户根据应用程序需求请求任意数量的资源，而无需任何人工干预。你不再需要等待 `IT` 人员在公司数据中心采购和部署物理硬件：只需通过定义明确的 `API` 调用在线请求资源即可。例如，通过几次 `API` 调用，你就可以获得一个可通过网络访问的功能完备的 `Hadoop` 集群。

### 云计算能提供哪些类型的服务？

云计算供应商提供的所有服务可分为三大类，每一类都建立在前一类的基础之上：

*   **基础设施即服务（`IaaS`）**：提供物理基础设施（如服务器、存储、负载均衡器、网络设备等）虚拟化实例的一系列服务。

*   **平台即服务（`PaaS`）**：提供构建应用程序所需的基础 `API` 的一系列服务。例如执行运行时、数据库实例、`Web` 和应用服务器等。

*   **软件即服务（`SaaS`）**：通过网络向最终用户提供软件应用程序的一系列服务。应用程序的范围之广可与桌面端软件媲美，但最常见的例子包括电子邮件、通讯、游戏和虚拟桌面。

大多数云服务提供商提供来自这三个类别的组合服务。例如，`Amazon Web Services (AWS)` 既可作为 `IaaS` 使用，让你创建任意规模的虚拟数据中心；也可作为 `PaaS` 使用，不仅提供裸服务器，还提供可扩展的数据库或 `Hadoop` 集群等。在云端运行 `Giraph` 需要 `IaaS` 能力，但不一定需要 `PaaS` 或 `SaaS`。然而，`PaaS` 或 `SaaS` 能力的存在，可能为 `Giraph` 分析提供额外的数据源。

云计算通常与公有云关联：即通过公共互联网向任何人提供的服务。然而，云计算敏捷且自助的服务特性，使其正迅速进入企业公司的私有数据中心。过去，企业数据中心被视为运行着各种操作系统的服务器机架。今天，它很可能被视为一种至少提供 `IaaS` 级别 `API` 的云计算服务。这就是所谓的私有云。

人们可能会认为不同云计算解决方案提供的 `API` 是兼容的，在一个云平台上能做的事情在另一个云平台上也能做。好消息是，在概念上这大致上是成立的（这也是本章只聚焦于 `AWS` 的原因）。坏消息是，实现同一目标所需的 `API` 集在不同云平台之间是不同的。每个云计算解决方案（公有或私有）通常都附带有软件库和工具，让你能够创建和管理各种资源。

## 亚马逊云服务上的 Giraph

`Amazon Web Services (AWS)` 云平台提供的一项主要服务是 `弹性计算云 (EC2)`。`EC2` 允许任何人从静态虚拟机 (`VM`) 镜像中实例化任意数量的虚拟服务器；它可以被视为 `AWS IaaS` 层的基础构建块。这些 `VM` 镜像被称为 `Amazon 机器镜像 (AMI)`。通过将虚拟资源（`CPU`、内存、`I/O` 带宽等）与某个 `AMI` 关联，你可以从一个 `AMI` 实例化任意数量的虚拟服务器。这些服务器是自包含的、虚拟化的操作系统，运行在某个 `AWS` 数据中心内。它们被称为实例。你的账户将根据每小时运行的实例数量以及每个实例所使用的资源量（`CPU`、内存、本地存储、网络带宽等）进行计费。与这些实例交互的唯一方式是通过互联网。例如，你可以决定直接使用 `SSH` 登录它们，或者在它们上面运行任何类型的网络服务，比如 `HTTP` 等。你需要知道的只是每个实例的公共 `IP` 地址。

`EC2` 是一个非常简单，同时又极具可扩展性的服务，你可以用它来手动在云端部署 `Hadoop` 集群。毕竟，一旦 `EC2` 返回与你创建的每个实例相对应的 `IP` 地址集，你就可以像使用物理主机一样，在其上部署一个 `Hadoop` 集群（关于 `Hadoop` 部署提示，请参考附录）。实际上，在 2006 年 `AWS` 刚推出时，自己动手是将实例组织成任何计算集群的唯一方法。

当 `Amazon` 在其 `AWS` 服务组合中引入一个名为 `Elastic MapReduce (EMR)` 的更高级服务时，情况发生了变化。`EMR` 是首批更像 `PaaS` 而非 `IaaS` 级别服务运行的服务之一。使用 `EMR`，管理单个主机并将其配置成 `Hadoop` 集群的具体细节对你来说是隐藏的。尽管如此，一个 `EMR` 集群仍然由一个或多个实例组成，因此可以被视为 `IaaS` 级别的产品。组成 `EMR` 集群的实例源自 `Amazon` 维护的特定 `AMI`。你可以将其视为 `Amazon` 以 `AMI` 形式打包的自家 `Hadoop` 发行版。该发行版有多个版本可供使用（构建在不同版本的 `Hadoop` 之上）。整个集群的创建和销毁可以作为一个单一操作完成，无需跟踪和管理所有节点。实际上，你甚至可以在运行中向集群添加节点以加速计算或利用 `Amazon` 的定价模式。

然而，在尝试启动第一个 `EMR` 集群之前，你还需要对环境进行一些预先配置，以便能够与 `AWS` 进行交互。

### 开始之前

与 `AWS` 交互的前提是你在亚马逊上拥有一个账户。亚马逊在其《`AWS` 入门》教程中提供了详细的操作指南。设置好账户后，你便可以登录 `AWS` 管理控制台（一个网页应用）、使用命令行工具，并从你的业务应用程序内部直接发起 `AWS API` 调用。`AWS` 管理控制台是最便捷的入门方式，因为它为常见操作提供了直观的网页界面。不过，一旦你在亚马逊云中运行的任务越来越多，就会发现自己需要更高程度的自动化和程序化控制能力。一种选择是让你放弃特定语言的 `SDK`，直接将 `AWS` 集群与基础设施管理逻辑嵌入业务应用程序。这提供了对云基础设施使用最精确的控制级别，但代价是需要投入精力协调与 `AWS` 的所有交互。命令行（`CLI`）工具在网页 `UI` 的临时性和特定语言 `SDK` 的精确控制之间提供了一个便利的中间地带。尽管存在几种不同版本的 `CLI` 工具，但在本章剩余部分，你将使用 `Amazon AWS CLI` 的具体实现。

所有与 `AWS` 的程序化交互都需要用户身份验证。这通过提供两条信息来完成：访问密钥 `ID`（一串类似`AKIAIOSFODNN7EXAMPLE`的字符）和秘密访问密钥（一串类似`wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY`的字符）。这两条信息都可以在注册过程中（或之后）通过 `Amazon AWS` 网页控制台获取。你可以通过环境变量，或者运行`aws configure`命令并在提示符下输入所需信息，来向 `AWS CLI` 工具提供这两条信息。

如果你尚未在桌面上安装 AWS CLI 工具，请务必阅读并遵循`docs.aws.amazon.com/cli/latest/userguide/`上的说明进行安装。请记住，与本书中的其他内容不同，AWS CLI 是用 Python 编程语言实现的，这可能要求你搭建一个 Python 环境。Amazon 用户指南提供了相应的操作说明。一旦 Python 环境就绪，安装 AWS CLI 就简化为列表 12-1 中概述的几个步骤。

**列表 12-1.** 在 Linux 或 Mac OS 上设置 AWS CLI 工具

```
$ wget https://s3.amazonaws.com/aws-cli/awscli-bundle.zip   # 下载捆绑包
$ unzip awscli-bundle.zip                                    # 解压捆绑包
$ ./awscli-bundle/install -b ∼/bin/aws                       # 将 AWS CLI 安装到用户主目录
$ PATH=∼/bin:$PATH                                           # 确保 aws CLI 在 PATH 路径中可用
$ aws configure                                              # 使用用户凭证配置 AWS CLI
AWS Access Key ID [None]: AKIAIOSFODNN7EXAMPLE
AWS Secret Access Key [None]: wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY
Default region name [None]: us-east-1
Default output format [None]: json
$ aws ec2 describe-regions                                   # 运行最简单的 EC2 请求，确保一切正常
```

在通过`aws configure`命令告知你的访问密钥和秘密密钥，并将默认 AWS 数据中心设置为 US EAST（美国东部）后，你几乎就完成了所有设置和配置，可以开始在亚马逊云上实例化你的第一个集群了。但是，要远程登录到集群的各个节点，你还需要设置一组安全外壳（SSH）密钥对。这是你需要设置的最后一个身份验证信息。可以这样理解：访问密钥 ID 和秘密访问密钥用于向 AWS 请求服务，而当这些服务为你实例化出虚拟基础设施后，则需要一组 SSH 密钥对来远程登录到该基础设施。SSH 密钥对需要具有唯一的名称。此处我们将它命名为`giraph-keys`。如果你已经有一个同名的 SSH 密钥对，可以跳过列表 12-2 中的步骤。

**列表 12-2.** 设置一个`giraph-keys` SSH 密钥对

```
$ aws ec2 create-key-pair --key-name giraph-keys --query 'KeyMaterial' \
  --output text > ∼/giraph-keys.pem
$ chmod 500 ∼/giraph-keys.pem
$ aws emr create-default-roles
```

列表 12-2 中的第一个命令请求创建 SSH 密钥对，并将私钥材料输出到用户主目录下的`giraph-keys.pem`文件中。第二个命令用于确保该文件的权限仅允许文件所有者本人读取，而系统上的其他用户无法读取。这是 SSH 所期望的默认权限。最后，最后一个命令用于确保用户具有使用 EMR 的权限；每个用户账户只需执行一次此命令。

你可能已经注意到一个常见模式，即如何从命令行执行 AWS CLI 工具。首先，指定你要操作的亚马逊云 API 子集（例如`EC2`、`EMR` 等），这成为你的主命令。然后，选择你要对该子系统执行的操作（动词）（例如，创建密钥对或列出区域），这成为你的子命令。最后，你可以通过`--flag`选项指定影响操作语义的标志（例如，为你的密钥对命名）。此命令行调用的结果总是 AWS CLI 代表你发起的一个或几个 AWS API 调用，并返回 JSON 格式的响应。AWS CLI 调用 API 的方式毫无神奇之处；你自己的应用程序也可以通过各种特定语言的 SDK 发出完全相同的 API 调用。

带着这些理解，让我们开始在你的亚马逊云上创建第一个可运行的 Hadoop 集群吧。令人惊奇的是，所要做的仅仅是执行一次 AWS CLI 工具而已。

### 在亚马逊云上创建你的第一个集群

在亚马逊云上操作 Hadoop 集群所需的大部分操作都由`emr`命令提供。你可以运行`aws emr help`来查看哪些子命令可用。在接下来的章节中，你将会使用到其中的不少命令，但现在，让我们先使用`create-cluster`子命令来创建最基本的 Hadoop 集群，如代码清单 12-3 所示。请注意，为了便于阅读，我们再次使用了反斜杠字符来断开冗长的单行命令。

**代码清单 12-3.** 在亚马逊云上创建你的第一个集群

```
$ aws emr create-cluster   \
--ami-version 2.4.11         \
--instance-groups             \
InstanceGroupType=MASTER,InstanceCount=1,InstanceType=m3.xlarge       \
InstanceGroupType=CORE,InstanceCount=2,InstanceType=m3.xlarge            \
--no-auto-terminate                                                    \
--ec2-attributes KeyName=giraph-keys
{
    "ClusterId": "j-DEADBEEF"
}
```

恭喜！你刚刚在亚马逊云上实例化了你的第一个集群。该集群由一个`MASTER`节点和两个`CORE`节点组成（总共三个节点）；它能够运行 MapReduce 作业。你用来创建集群的命令虽然很长，但含义非常清晰。你需要调用`aws`的`emr`子命令，并给它一个动词`create-cluster`来要求创建一个集群。该操作要求你指定想要使用的 Hadoop 版本（`--ami-version 2.4.11`），然后是集群的拓扑结构（`--instance-groups`），描述每个节点的角色、分配给特定角色的节点总数，以及与每个节点关联的 AWS 实例类型。你还要求不立即终止集群（`--no-auto-terminate`），并提供了一个密钥对，以便稍后可以登录集群（`--ec2-attributes KeyName=giraph-keys`）。这是你需要提供给 AWS EMR 以创建集群的最少信息量。运行此命令的结果是以 JSON 格式输出的一个集群 ID（`j-DEADBEAF`）。请留意这个集群 ID，本章中你还会在其他地方用到它。

现在，Hadoop 集群已在亚马逊云中启动并运行，你能用它做什么呢？如果此时你无需再做其他事情就能运行 Giraph 应用程序，那就太棒了。不幸的是，Amazon EMR 集群默认不会像捆绑 Apache Hive、Pig 和其他 Hadoop 生态系统项目那样预装 Giraph 组件。在继续让 Giraph 在集群上可用之前，让我们先确保至少可以在上面运行一个简单的 MapReduce 作业。

你将在新创建的集群上启动测试 MapReduce 作业的方式很简单：登录到它的网关节点，并使用预装的`hadoop`命令行工具来启动一个 Pi 作业（Hadoop 对 MapReduce 中 HelloWorld 的回应），如代码清单 12-4 所示。请注意，设置集群可能需要相当长的时间。因此，第一个命令可能需要几分钟才能让你登录集群，因为需要等待集群完全上线。

**代码清单 12-4.** 登录到 EMR 集群的网关节点并运行一个 Pi MapReduce 作业

# 使用 AWS EMR 集群

首先，我们通过 SSH 连接到集群：

```bash
$ aws emr ssh                                    \
--cluster-id j-DEADBEEF                  \
--key-pair-file ∼/giraph-keys.pem
Waiting for the cluster to start.
ssh -i ∼/giraph-keys.pem hadoop@ec2-54-82-189-117.compute-1.amazonaws.com
% hostname    # 打印主机名以确保我们确实在云中了
ip-172-31-51-237.ec2.internal
% hadoop jar hadoop-*examples.jar pi 10 100   # 运行一个测试作业
...
Estimated value of Pi is 3.14800000000000000000
% exit # 退出集群，返回我们的工作站
```

这里你所做的是使用了 EMR API 集合中的 `ssh` 操作。你提供了代码清单 12-3 输出中的集群 ID（`--cluster-id`），以及你最初创建集群时使用的同一个密钥对（`--key-pair-file`）。运行此命令的结果是一个常规的 `ssh` 会话，它为你提供了一个集群网关节点上的 shell。所有以 `%` 为前缀的命令此时都在远程网关节点的 shell 中执行。你在网关上运行的第一个命令是打印其 `hostname`，以确保你确实在云端。之后，你使用 `hadoop` 命令行工具运行了 Pi 计算作业，并收到了预期的（尽管有些不精确的）结果。这个集群看起来和用起来都像一个真正的 Hadoop 集群。你做的最后一件事是使用 `exit` 命令退出网关节点，返回到你的工作站。

请记住，此时你在亚马逊云上运行着三个节点。即使它们没有执行任何有用的工作，亚马逊仍然会向你收费。一旦你完成实验，请务销毁你的集群，并检查是否有残留的节点在运行，如代码清单 12-5 所示。

## 代码清单 12-5. 查找残留集群并终止它们

```bash
$ aws emr list-clusters
$ aws emr terminate-clusters --cluster-id j-DEADBEAF
```

第一个命令会输出一个非常详细的 JSON，描述了亚马逊正在或曾经为你维护的所有集群。你需要查找那些状态未列为 `TERMINATED` 的集群，并使用 `terminate-clusters` 操作来终止它们。

### EMR 集群的基本构建块

在亚马逊云上执行的大多数操作都可以分解为对虚拟服务器的操作。这些虚拟服务器被称为 `实例`，对于亚马逊云的用户而言，它们与实际服务器机架并无区别。与任何真实服务器一样，`实例` 运行着一个操作系统的副本，你可以通过远程 shell 登录到该操作系统（通过 `ssh`），或者与运行在这些虚拟服务器上的其他软件服务（如 Web 或数据库服务）进行交互。然而，与真实服务器不同的是，只要你愿意付费，你可以创建任意数量的实例。

在亚马逊云上运行的每一个 `实例` 都由以下部分定义：

*   一个完整配置的操作系统镜像。这被称为 Amazon 机器镜像（`AMI`），本质上包含一个操作系统从中启动的磁盘驱动器的内容。

*   物理资源的数量（例如，CPU 数量、RAM 和磁盘大小）。

如今，亚马逊云上有成千上万种包含各种操作系统的 `AMI`。有些由操作系统供应商维护，有些由志愿者维护。有些是免费的（如基于 Linux 的镜像），有些则需要付费使用（如基于 Microsoft Windows 的镜像）。如果你找不到合适的 `AMI`，亚马逊会提供所有必要工具供你自行维护 `AMI`。如果你在寻找喜欢的操作系统镜像，可以访问 Cloud Market 网站 [`http://thecloudmarket.com`](http://thecloudmarket.com/)。请记住，选择运行哪个 `AMI` 的决定与分配给它的资源数量是相互独立的。

虽然亚马逊云不允许你为 `实例` 指定任意数量的资源，但你可以从一组预选的模板中进行选择。这些模板被称为 `实例类型`。亚马逊在 [`http://aws.amazon.com/ec2/instance-types/`](http://aws.amazon.com/ec2/instance-types/) 提供了分配给每种 `实例类型` 的不同类型资源的详细概述。如果你想了解运行不同 `实例类型` 的计费影响，这也是一个值得访问的页面。

为了便于管理操作，实例可以分组为称为 `预留`（或 `实例组`）的块。一个组内的所有实例在所使用的镜像（`AMI` 规格）以及分配给组内每个实例的资源数量（`实例类型`）方面都是相同的。这使得向亚马逊云指示始终维护一定数量的相同服务器变得简单。无论你在一个 `预留` 中请求一个实例还是一百个，你都是在创建一个组。

这些是亚马逊 IaaS 层为你提供的基本构建块。通过使用它们，你可以选择不自行搭建 Hadoop 集群，或者请求亚马逊服务为你完成这项工作。

### EMR 集群的组成：实例组

基于 `实例组` 的基本概念，一个 Amazon EMR 集群由属于三个不同 `实例组` 的若干节点组成。

*   `MASTER` 实例组。该实例组始终只包含一个节点，该节点配置为运行 Hadoop 集群的所有集中式服务：HDFS `NameNode`、YARN `ResourceManager` 或 MapReduce `JobTracker`。它被配置为网关，以便你可以通过使用 `aws emr ssh` 操作手动启动临时的 MapReduce 或 YARN 作业。亚马逊还确保客户端捆绑软件（如 Hive、Pig 等）默认可用。不幸的是，Giraph 不在此列表之列，需要单独安装在此节点上。如果你运行的是一个单节点集群，该节点必须属于此组。它还将运行来自 `CORE` 实例组的所有服务。

*   `CORE` 实例组。该实例组包含一个或多个作为 Hadoop 从节点的节点。此组中每个节点运行的两个基本服务是 HDFS `DataNode`（用于存储数据）和 YARN `NodeManager`/MapReduce `TaskTracker`。此组中的节点执行所有数据处理。无论何时你想加快计算速度，都可以动态地向此组添加节点，但无法动态地移除它们。

*   `TASK` 实例组。该实例组是可选的，包含的节点功能与 `CORE` 实例组中的节点几乎完全相同，但有一个例外：它们不运行 HDFS `DataNode` 服务，因此无法在本地处理数据（数据始终需要从外部位置获取）。由于此实例组中的节点是完全临时的（它们不存储 HDFS 块），你可以增加或减少此组中的节点数量。当处理通常驻留在外部存储库（例如 Amazon Simple Storage Service (S3)）中的数据时，或者当你希望使用竞价型实例来降低运营成本时，此实例组非常方便。

综合来看，你现在可以理解 `aws emr create-cluster` 子命令给出的前两个选项的含义了（此处重复列出，见清单 12-6）。

## 代码清单 12-6. 在亚马逊云上创建你的第一个集群

```bash
$ aws emr create-cluster
--ami-version 2.4.11
--instance-groups
InstanceGroupType=MASTER,InstanceCount=1,InstanceType=m3.xlarge
InstanceGroupType=CORE,InstanceCount=2,InstanceType=m3.xlarge
```

第一个选项（`--ami-version`）指定了用于在三个组中启动实例的 `AMI`。这是一种间接指定，因为你实际上引用的是亚马逊维护的 EMR 堆栈的版本，而不是 `AMI` ID。你可以将其视为任何其他 Hadoop 发行版：你只能选择发行版本身的版本。无法选择该发行版中 Apache Hadoop 及其生态系统组件的版本。如果你想知道哪些版本的 Amazon EMR 堆栈对应哪些组件，可以访问网页 [`http://docs.aws.amazon.com/ElasticMapReduce/latest/DeveloperGuide/ami-versions-supported.html`](http://docs.aws.amazon.com/ElasticMapReduce/latest/DeveloperGuide/ami-versions-supported.html)。你在示例中使用的版本对应于基于 Hadoop 1.0.x 的堆栈。必须指出的是，亚马逊作为给定 Hadoop 堆栈的一部分为你维护的 `AMI` 毫无神秘之处。你可以创建该 `AMI` 的实例，就像创建具有任意操作系统的任何其他 `AMI` 的实例一样。

你提供给 `create-cluster` 命令的第二个选项（`--instance-groups`）是对每个实例组大小和类型的精确说明。它由一组、两组或三组键值对列表组成，描述了你要添加到集群的三个实例组中的每一个（`MASTER` 是唯一必需的组）。以下键是必需的。

*   `InstanceGroupType` 定义实例组。此处的值为 `MASTER`、`CORE` 或 `TASK`。

*   `InstanceCount` 定义组中的实例（节点）数量。

*   `InstanceType` 指定组中每个节点获得的实例类型（分配给每个节点的资源量）。此处的值是 AWS 可识别的任何有效实例类型说明。

你创建的集群的图形表示如图 [12-1] 所示。

![A978-1-4842-1251-6_12_Fig1_HTML.jpg](img/A978-1-4842-1251-6_12_Fig1_HTML.jpg)

**图 12-1.** 由两个实例组和三个节点组成的集群

图 [12-1] 中的白色方框代表一个 AMI 模板，实际实例由此模板派生并放置到请求的实例组中：一个放入`MASTER`实例组，两个放入`CORE`实例组。

### 将 Giraph 应用部署到 EMR 集群

如前所述，Amazon 并未将 Giraph 纳入其 Hadoop 发行版。如果未来有所改变（Amazon 以倾听客户需求著称），那当然很好，但鉴于 Giraph 主要是一个框架，而非应用，这倒也不算太严重的问题。

从 Hadoop 集群的角度来看，任何基于 Giraph 构建的图处理应用看起来都像一个自定义的 MapReduce 或 YARN 作业。考虑到这一点，让我们创建一个 JAR 包，它包含第 5 章中的一个示例应用，并同时打包 Giraph 及其所有依赖项。使用 Maven 可以很轻松地做到这一点。你只需告诉 Maven 创建一个 `jar-with-dependencies` 构件包，如清单 12-7 所示（请确保先回到你在清单 5-4 中为示例应用创建的文件夹）。

**清单 12-7.** 将 Giraph 应用部署到 EMR 集群

```
$ mvn clean compile assembly:assembly -DdescriptorId=jar-with-dependencies
```

```
$ aws emr put --cluster-id j-DEADBEEF --key-pair-file ∼/giraph-keys.pem  \
  --src target/*jar-with-dependencies.jar
```

第一条命令指示 Maven 构建一个自包含的 JAR 文件，然后你使用 `put` 操作将该文件复制到 EMR 集群的网关节点。和往常一样，你需要指定集群 ID（`--cluster-id`）和密钥对（`--key-pair-file`）以允许访问指定的集群。给 `put` 操作的额外参数是要复制的 JAR 文件的位置（`--src`）。

此时，你可以使用 `aws emr ssh` 命令登录到 EMR 集群，并像在清单 11-3 中执行 Pi 估算作业一样，执行图处理示例。作为替代，我们将考虑将其作为 EMR 集群处理规范中的一个步骤进行远程调度。

### EMR 集群数据处理步骤

EMR 集群最常见的用例是按需创建，只要还有需要执行的数据处理步骤就保持运行，然后让 Amazon 云关闭集群。当然，你也可以请求集群保持运行，等待明确的关闭 API 调用。这正是你使用 `--no-auto-terminate` 选项时所做的事情。拥有一个可用于交互式操作的集群对于探索 EMR 来说很方便。然而，一旦你开始转向数据管道的完全自动化，让 Amazon 云管理集群生命周期就变得更加自然。

如果你希望依赖 Amazon 云进行端到端的集群生命周期管理，那么在创建集群时需要向 AWS 传达一些额外的信息：

-   要在 EMR 集群上安装的附加应用集。目前，你可以通过这种方式安装 Hive、Pig、HBase、Ganglia、Impala 和 MapR 发行版。

-   你想要在实例创建之后、集群启动之前执行的一系列引导操作。这可能包括安装额外的软件、调整集群以及操作系统配置等。简单来说，引导操作是存储在外部可见存储（S3、HTTP 等）上的脚本，并在集群节点上运行。

-   集群启动并运行后，你想要执行的一系列数据处理步骤。目前，这些步骤可以包括以下内容：

    -   从自包含的 JAR 包运行自定义的 MapReduce 或 YARN 作业

    -   在 MASTER 节点上运行自定义脚本

    -   运行 Hadoop Streaming 作业

    -   运行 Hive、Pig 或 Impala 查询

使用引导操作和数据处理操作的模式非常相似。由于你不需要对集群进行任何特殊的调整，我们将推迟展示引导操作示例，先专注于数据处理步骤。一个有趣的附注是：虽然无法为已经运行的集群请求额外的引导操作，但可以请求额外的数据处理步骤。

使用 AWS CLI 工具时，引导操作和数据处理步骤都需要通过 JSON 规范或简写符号传递给 `aws emr create-cluster` 命令。清单 12-8 使用 JSON 规范向一个已运行的集群添加了一个 Giraph 数据处理步骤。

**清单 12-8.** 运行 Giraph 数据处理步骤

```
$ aws emr add-steps --cluster-id j-DEADBEAF
```

```
--steps ’[{
"Type":"CUSTOM_JAR",
"Name":"GiraphApp",
"MainClass":"org.apache.giraph.GiraphRunner",
"ActionOnFailure":"CONTINUE",
"Jar":"file:///home/hadoop/book-examples-1.0.0-jar-with-dependencies.jar",
"Args":["GiraphHelloWorld",
"-vip", "input",
"-vif", "org.apache.giraph.io.formats.IntIntNullTextInputFormat",
"-w", "1"]}]’
```

```
{ "StepIds": ["s-STEPID"]}
```

上述命令为立即执行添加了一个处理步骤到由 `--cluster-id` 标识的已可用集群上。处理步骤的描述是一个格式良好的 JSON 数组，每个条目对应你希望在集群上运行的一个处理步骤。每个条目包含以下键值对：

-   `Type`：定义处理步骤的类型。在这种情况下，你请求通过 Java 执行一个自定义 JAR 包。还有其他类型的步骤可用，包括执行 Pig 或 Hive 脚本、Streaming 作业以及其他操作。`CUSTOM_JAR` 类型是最灵活的。

-   `Name`：一个符号名称，用于在所有未来的输出中标识该处理步骤；可以是任何字符串。

-   `MainClass`：对于 `CUSTOM_JAR` 类型的步骤；标识入口点类的名称。

-   `ActionOnFailure`：指定步骤失败时执行的操作。有效值包括 `CONTINUE`、`TERMINATE_CLUSTER` 和 `CANCEL_AND_WAIT`。

-   `Jar`：对于 `CUSTOM_JAR` 类型的步骤；指定 JAR 文件的位置。

-   `Args`：对于 `CUSTOM_JAR` 类型的步骤；指定要传递给 main 方法的参数。

命令的输出提供了一个步骤 ID，如果你想了解该步骤的状态或结果，可以引用它。最后，如果你想知道是否可以避免在命令行中输入冗长的 JSON 规范，答案是肯定的。`--steps` 标志可以从文件读取 JSON，前提是你要给它一个文件 URL 而不是实际的 JSON，就像这样：`--steps file://./step.json`。

总的来说，向 EMR 集群添加数据处理步骤非常简单。实际上，你可以在请求创建集群时，通过向 `create-cluster` 操作提供完全相同的 `--steps` JSON 规范来指定一系列步骤。这样，你可以让 Amazon 云启动一个集群，运行一系列数据处理步骤，最后关闭集群，从而最大限度地减少你为 EC2 资源付费的时间。本章稍后会尝试这个操作，但现在，让我们看看你的处理步骤是否成功。你可以运行清单 12-9 中所示的查询命令，并在 JSON 输出中查找 `State` 键。

**清单 12-9.** 运行 Giraph 数据处理步骤

```
$ aws emr describe-step --cluster-id j-DEADBEEF --step-id s-STEPID
```

```
{
"Step": {
"Status": {
"Timeline": {
"EndDateTime": 1440475780.643,
"CreationDateTime": 1440475759.648,
"StartDateTime": 1440475780.635
},
"State": "FAILED",
"StateChangeReason": {}
},
....
```

信不信由你，数据处理步骤失败了。你现在需要找出失败的原因。

### 当事情出错时：调试 EMR 集群

当 EMR 集群或它应该处理的作业出现任何问题时，你需要能够追踪问题所在。通常，最好的选择是分析日志文件并尝试推断故障根源。EMR 集群有两个可用的日志文件来源：S3 上的聚合日志和集群节点上的本地日志文件。在仍然运行中的集群上，你可以通过 `ssh` 进入网关节点，并直接查看收集在 `/mnt/var/log/` 下的日志文件。例如，要找出上一节的数据处理步骤失败的原因，让我们查看一下 `/mnt/var/log/hadoop/steps`。该文件夹包含与提交给集群的每个数据处理步骤相对应的子文件夹。在这些子文件夹下，你会看到三个日志文件：
*   `controller`：包含数据处理步骤精确调用的日志文件。在本例中，它包含一个自定义 jar 的 Hadoop 调用命令。
*   `stdout`：包含控制器命令在其标准输出流上产生的输出的日志文件。
*   `stderr`：包含控制器命令在其错误输出流上产生的输出的日志文件。
首先，让我们看看当你通过自定义 JAR 提交 Giraph 数据处理步骤时，`controller` 调用了什么。你会看到与列表 12-10 非常相似的内容。
列表 12-10。`/mnt/var/log/hadoop/steps/s-STEPID/controller` 的内容
```
2014-07-14T19:36:33.251Z INFO startExec ’hadoop jar /home/hadoop/book-examples-1.0.0-jar-with-dependencies.jar org.apache.giraph.GiraphRunner GiraphHelloWorld -vip inputt -vif org.apache.giraph.io.formats.IntIntNullTextInputFormat -w 1 -ca giraph.SplitMasterWorker=false’
...
```
这看起来就像是你通过 `'hadoop jar ...'` 命令手动提交来运行 Giraph 工作流时所用的完全相同命令。也许，如果你查看一下该命令产生的错误执行情况，就能找到它失败的原因。你在那里看到的内容与列表 12-11 非常相似。
列表 12-11。`/mnt/var/log/hadoop/steps/s-STEPID/stderr` 的内容
```
Exception in thread "main" java.lang.IllegalArgumentException: Invalid vertex input path (-vip): input
at org.apache.giraph.utils.ConfigurationUtils.populateGiraphConfiguration(ConfigurationUtils.java:406)
at org.apache.giraph.utils.ConfigurationUtils.parseArgs(ConfigurationUtils.java:207)
      at org.apache.giraph.GiraphRunner.run(GiraphRunner.java:74)
```
当然，这说得通——你试图在一个不存在的输入数据集上运行 Giraph 应用程序。如何使数据集可用于 EMR 集群并获取结果是下一节的主题。

### 我的东西在哪里？EMR 集群的数据迁入与迁出

EMR 集群是一个临时的节点集合。它们按需创建以完成特定的数据处理步骤序列；之后通常会被立即销毁。这是一种便捷且符合云理念的资源利用模式，但它确实要求初始数据集来自某个永久存储位置，并且在集群及其 HDFS 存储层消失之前，以某种方式捕获结果数据集。
通常，Amazon 云依赖 S3 作为其永久存储层。几乎所有 AWS 服务都支持将 S3 作为数据源或数据目标。例如，AMI 存储在 S3 中；它也可以用作大多数 AWS 服务日志记录的目标。S3 将所有对象存储在存储桶（bucket）中。你可以将存储桶视为在你的 AWS 账户下管理的顶层文件系统文件夹。创建存储桶后，你可以使用它来创建类似文件的对象，以保存任何类型的数据。一个任意、唯一且长度不超过 1024 字节的键引用每个对象。将对象命名得如同文件系统中的文件一样是很方便的（但这并非强制要求），使用传统的 Unix 路径名约定（用 `/` 字符分隔文件夹）。遵循此约定使得使用 S3 或 HTTP URI 引用 S3 对象变得自然。例如，如果你创建一个名为 `giraph.examples` 的存储桶，并将一个名为 `datasets/1/data.txt` 的对象放入其中，那么你可以将其引用为 `s3://giraph.examples/datasets/1/data.txt` 或 `https://s3.amazonaws.com/giraph.examples/datasets/1/data.txt`。
请记住，存储桶名称在 AWS 上共享一个全局命名空间。这意味着你可能需要发挥一些创意。很可能，任何像 `mybucket` 这样简单的名称都已被占用。考虑到这一点，为 Giraph 应用程序提供数据的第一步是创建一个唯一的存储桶，并将你在第 5 章中使用的示例输入数据复制到其下，如列表 12-12 所示。
列表 12-12。将输入数据放入 S3
```
$ aws s3 mb s3://unique.bucket
$ aws s3 cp src/main/resources/1/graph.txt s3://unique.bucket/datasets/1/graph.txt
```
既然示例图描述已存在于 S3 中，距离将其提供给 Giraph 处理就完成了一半。Amazon EMR 方便地提供了一个自定义 JAR 文件，该文件实现了从 S3 存储桶到 HDFS 的高效数据传输，Giraph 可以在 HDFS 中获取数据。触发此传输的方法是为集群再添加一个步骤，如列表 12-13 所示。请注意，这次你使用简写符号来指定所有必需的键值对，而不是使用格式正确的 JSON 对象。两种方式都可以。
列表 12-13。通过自定义 JAR 步骤将数据从 S3 传输到 HDFS
```
$ aws emr add-steps --cluster-id j-DEADBEAF                                \
--steps Type=CUSTOM_JAR,Name=UploadInputData,                              \
Jar=/home/hadoop/lib/emr-s3distcp-1.0.jar,                                \
Args=--src,s3://unique.bucket/datasets/1/,                               \
--dest,hdfs:///user/hadoop/input
```
你传递给自定义 JAR 的参数通过 `--src` 指定源 S3 存储桶，并通过 `--dest` 指定 HDFS 中的目标位置。当然，这个步骤也可以反向工作。如果你在集群销毁后需要捕获任何数据集，只需添加一个将一批文件从 HDFS 复制到 S3 存储桶的步骤即可。
提醒一句：S3 很昂贵，并且它会一直保存你的数据，直到你明确删除它；请仅将其用于最终结果。S3 中保留的任何数据都会根据其大小按月向你的账户收费。
此时，你可以重新运行列表 12-8 中的图处理步骤，以查看这次它是否能够运行完成，并显示退出状态为 `COMPLETED`。然而，与其这样做，不如让我们将所有内容整合起来，看看如何使用一个真正的临时集群进行数据处理。

### 整合所有要素：临时图处理 EMR 集群

至此，你已经掌握了在 Amazon 云上运行图处理作业所需的全部要素，该作业将尽可能接近生产环境中的实际使用情况。我们将沿用经典的“Hello World”图处理示例，但需要确保数据和自定义 JAR 文件从 S3 上传到临时 EMR 集群，并将最终结果数据集记录到某个 S3 存储桶中。同时，你还需确保所有日志均被传输至 S3，以便在集群消失后仍能进行诊断。鉴于上述需求，数据处理步骤的定义会变得相当长。为了避免在命令行中逐一指定，你将采用 JSON 文件来定义，如代码清单 12-14 所示。
**代码清单 12-14.** 数据处理步骤的 JSON 定义文件 `steps.json`

# Amazon EMR 上的 Giraph 作业部署指南

## 部署与执行

```json
[
  {
    "Type": "CUSTOM_JAR",
    "Name": "UploadInputData",
    "ActionOnFailure": "CANCEL_AND_WAIT",
    "Jar": "/home/hadoop/lib/emr-s3distcp-1.0.jar",
    "Args": [
      "--src",
      "s3://unique.bucket/datasets/1/",
      "--dest",
      "hdfs:///user/hadoop/input"
    ]
  },
  {
    "Type": "CUSTOM_JAR",
    "Name": "GiraphJob",
    "MainClass": "org.apache.giraph.GiraphRunner",
    "ActionOnFailure": "CANCEL_AND_WAIT",
    "Jar": "s3://unique.bucket/jars/giraph-hello.jar",
    "Args": [
      "GiraphHelloWorld",
      "-vip",
      "input",
      "-op",
      "output",
      "-vif",
      "org.apache.giraph.io.formats.IntIntNullTextInputFormat",
      "-vof",
      "org.apache.giraph.io.formats.GraphvizOutputFormat",
      "-w",
      "1",
      "-ca",
      "giraph.SplitMasterWorker=false"
    ]
  },
  {
    "Type": "CUSTOM_JAR",
    "Name": "DownloadOutputData",
    "ActionOnFailure": "CANCEL_AND_WAIT",
    "Jar": "/home/hadoop/lib/emr-s3distcp-1.0.jar",
    "Args": [
      "--src",
      "hdfs:///user/hadoop/output",
      "--dest",
      "s3://unique.bucket/output/"
    ]
  }
]
```

一个有趣的附带说明是：由于 EMR 集群允许从 S3 引入自定义 JAR，你实际上无需额外执行 put 操作。你只需将 `GiraphHelloWorld` JAR 上传至 S3 即可，如代码清单 12-15 所示。

**代码清单 12-15.** 将 `GiraphHelloWorld` JAR 复制到 S3 存储桶

```bash
$ aws s3 cp target/*-jar-with-dependencies.jar s3://unique.bucket/jars/giraph-hello.jar
```

现在，所有必需的文件都已存放在 S3 中，剩下唯一要做的就是启动一个临时 EMR 集群，并使其按照 `steps.json` 中指定的数据处理步骤运行，如代码清单 12-16 所示，并使用熟悉的相关步骤。但你还需添加日志存储位置（`--log-uri`），并要求在处理步骤完成后终止集群（`--auto-terminate`）。

**代码清单 12-16.** 在 EMR 上启动一个临时图处理作业

```bash
$ aws emr create-cluster --ami-version 2.4.11 \
  --instance-groups \
  InstanceGroupType=MASTER,InstanceCount=1,InstanceType=m3.xlarge \
  --log-uri s3://unique.bucket/logs/ --auto-terminate \
  --steps file://./steps.json
```

```json
{
  "ClusterId": "j-DEADBEEF2"
}
```

和之前一样，该命令会输出集群 ID，你需要等待该集群退出后才能检查整体执行日志。使用代码清单 12-17 中的命令可以方便地监控集群状态。该命令基于 `describe-cluster` 操作的 JSON 输出，并通过 `--query` 标志指定的自定义 JSON 查询请求来打印集群状态。这是一种减少 JSON 输出量的便捷方式。关于如何指定 JSON 查询，请参阅 `aws help`。

**代码清单 12-17.** 监控集群状态

```bash
$ aws emr describe-cluster --cluster-id j-DEADBEEF2 \
  --query Cluster.Status.State
```

```
"TERMINATED"
```

一旦集群终止，你只需检查留在 S3 中的日志，确认处理步骤已成功完成，并获取作业的最终输出。这可以通过一系列 `s3` 命令完成，如代码清单 12-18 所示。第一个命令将 Giraph 应用的运行输出复制到你的工作站，并命名为 `output.txt`；第二个命令演示了如何列出存储桶某部分的内容（如同操作文件夹），以查找可能感兴趣的日志文件。

**代码清单 12-18.** 集群终止后检查日志和输出

```bash
$ aws s3 cp s3://unique.bucket/output/part-m-00000 output.txt
$ aws s3 ls s3://unique.bucket/logs/j-DEADBEEF2/
```

```
PRE daemons/
PRE jobs/
```

至此，所有输出数据（包括日志）都已保存在 S3 中。这对于集中存储而言非常理想，但如果你不清除不再需要的日志并精简输出数据集，最终可能会产生高昂的费用。

在 EMR 上运行时降低成本是首要关注点之一，而存储成本可能对最终账单贡献巨大。不过，Amazon 提供了多种有吸引力的定价模型，使 EMR 集群的使用更具成本效益。其中一种模型——竞价实例（Spot Instances）——将在下一节中介绍。

## 物超所值：Amazon EMR 竞价实例

到目前为止，您所了解的亚马逊云定价模型假定每种实例类型都有固定的计费率。然而，这并不是唯一的模式。另一种为计算资源付费的方式是针对亚马逊云中的闲置容量进行竞价投标。这适用于所有实例类型，其每小时价格通常低于同类实例类型的固定计费率。竞价实例的缺点在于，当需求增加且您的出价低于当前市场价时，亚马逊会关闭这些实例。对于传统的 MapReduce 工作负载来说，这并非大问题（毕竟，Hadoop 的设计初衷就是能够承受节点故障），并且它为您提供了一种非常便捷的方式来利用廉价的计算资源加速数据处理。

竞价实例在 EMR 集群中的典型用途是作为任务（TASK）实例组的一部分，这样节点可以随时添加（和移除）而不会中断集群操作。要指示某个实例组需要启用竞价实例计费，您只需在实例组规范中添加 `BidPrice` 属性即可，如 12-19 清单所示。

**清单 12-19.** 使用竞价实例 TASK 实例组创建集群

```bash
$ aws emr create-cluster --ami-version 2.4.11      \
--instance-groups                                   \
InstanceGroupType=MASTER,InstanceCount=1,InstanceType=m3.xlarge           \
InstanceGroupType=CORE,InstanceCount=2,InstanceType=m3.xlarge                \
InstanceGroupType=TASK,InstanceCount=3,InstanceType=m3.xlarge,BidPrice=0.10 \
--log-uri s3://giraph.examples/logs/ --auto-terminate    \
--steps file://./steps.json
```

如果您运行上述命令，亚马逊云会立即为您创建三个节点：一个在主控（MASTER）实例组，两个在核心（CORE）实例组。这些节点用于管理集群上的存储（HDFS），并为图处理应用提供最低保证的计算资源量。您可能还会期望任务（TASK）实例组中的另外三个节点随时添加到您的集群中（也可能随时被从您那里移除）。对于传统的 MapReduce 应用，这些额外节点能带来暂时的性能优势。然而，Giraph 应用给这种模式带来了些许复杂之处。

大多数传统 MapReduce 工作负载对映射器可用的计算节点总数没有严格的要求。如果某些节点发生故障，通常只会导致整体执行速度变慢，但您的作业最终仍会完成。Giraph 将映射器用作并行工作节点，并要求在任何时候都有特定数量的工作节点（通过 `--w` 选项指定的值）可用。如果一个节点发生故障，Giraph 期望 Hadoop 在另一个节点上重新调度一个映射器，这只有在该 Giraph 应用可用的总计算资源量超过其预期工作节点数时才可能实现。这给 Giraph 应用使用竞价实例带来了复杂性。毕竟，竞价实例的全部意义在于，在价格合适时为您的集群提供临时的性能提升，并在价格上涨时缩减规模。

将竞价实例与 Giraph 一起使用被视为一项实验性功能，仅推荐给高级用户。如果您有兴趣尝试，请务必注意以下配置属性，您需要在配置中明确指定这些属性（通过 `--ca` 命令行选项或 `giraph-site.xml`）。

- `giraph.maxWorkers`: Giraph 可以在集群上利用的工作节点总数。请根据您期望亚马逊分配给集群的最大节点数（CORE + TASK）进行设置实验。
- `giraph.minWorkers`: Giraph 进入下一个超步所需的最小工作节点数。请根据您期望亚马逊保证分配给集群的节点数（CORE）进行设置实验。
- `giraph.minPercentResponded`: 进入下一个超步所需的最少健康工作节点百分比。请尝试将其设置为约 100 × `giraph.minWorkers` ÷ `giraph.maxWorkers`。
- `giraph.checkpointFrequency`: 工作节点状态检查点之间连续的迭代步数。请尝试将其设置为 1，并根据您应用的性能特征逐步增加。

至此，您已经了解了运行普通 EMR 集群的所有不同方式。但是，如果您需要优化价格、性能，或两者兼而有之，以最好地满足您的图处理应用需求，又该怎么办呢？

## 优化 EMR 集群

到目前为止，您一直在使用 EMR 集群的默认配置，而没有过多关注其针对图处理任务的优化程度。作为第一步，这没问题（而且这些简单的示例本身也不需要太多额外配置），但对于任何实际工作负载，都需要进行一定程度的调优。您需要调优 EMR 集群的原因有两个且相互关联：性能与成本。

每个 Giraph 应用程序都各不相同，但鉴于其灵活的架构，任何使用 Giraph 的应用程序都可能对三种基本资源造成压力：CPU、内存和 I/O。上一章中提到的所有性能调优建议仍然适用于在云端运行 Giraph。当然，您需要从实际为所有资源使用付费的角度来看待它。因此，确定能提供足够性能所需的最少资源数量就成了关键。

在 EMR 上，您能做的最有价值的选择就是为您的任务选择正确的实例规格。如前所述，EMR 提供数十种不同的实例规格，要猜对合适的规格可能令人生畏。这里的最佳建议是从较小的规格开始，部署诸如 Ganglia 之类的性能监控工具，然后试验不同的实例规格，以找到成本与性能之间的最佳平衡点。将实例类型参考表保存在手边（[`http://aws.amazon.com/ec2/instance-types/`](http://aws.amazon.com/ec2/instance-types/)），并做一些实验，看看您的方向是否正确。

某些工作负载在具有大量 RAM 的实例上运行时性能最佳。如果您属于这种情况，请务必告知 EMR 为此类环境配置您的集群。通常，任何特定于集群的调整都可以作为集群引导操作的一部分来实现。引导操作本质上是在集群启动时执行的脚本，并会影响 Hadoop 配置文件。例如，为内存密集型工作负载配置 Hadoop 的最简单方法是添加一个引导操作，引用 Amazon 自己的脚本，如 12-20 所示。

**代码清单 12-20** 为内存密集型工作负载配置 Hadoop

```
$ aws emr create-cluster --bootstrap-actions                                      \
     Path=s3n://elasticmapreduce/bootstrap-actions/configurations/latest/memory-intensive, \
     Name=mem-config                                                              \
     Args=string1,string2                                                         \
  --ami-version 2.4.6 ...
```

由于引导操作是调整 Hadoop 配置的主要工具，建议您下载 2-20 中提到的 `memory-intensive` 脚本，并将其作为创建您自己的引导操作的参考。

另一个需要考虑的基本变量是 CORE 和 TASK 实例组中的实例数量。同样，EMR 使得从小规模开始，并动态调整 TASK 实例组的大小以查看是否有助于加快处理速度变得很容易。

最后，在使用任何 AWS 服务时，请确保您所做的一切都限制在同一 AWS 区域内；更好的是，限制在同一可用区。跨区域数据传输成本高昂，并且与同一区域内的 I/O 相比，提供的带宽要少得多。最佳情况是您的数据处理集群的所有元素都位于同一可用区。

## 总结

在云端运行 Giraph 提供了一种可扩展、弹性且易于使用的方式来执行图处理应用程序，而无需担心管理基础设施。所有集群配置都已为您完成，同时保留了足够的灵活性来进行所有必要的调整。并非所有云都是相同的。虽然本章只讨论了亚马逊云，但相同的核心原则也适用于其他公有云和私有云。如果您的云提供商不是亚马逊，那么至少您现在知道应该寻找哪些服务了。