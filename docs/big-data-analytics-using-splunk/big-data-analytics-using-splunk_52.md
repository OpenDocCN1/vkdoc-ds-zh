# 附录 B

![image](img/frontdot.jpg)

## 有用的 Splunk 应用

本章概述了一些有用的 Splunk 应用和插件，它们属于监控、安全、数据库、应用、应用服务器和网络等类别。

## Splunk 应用

在本书的前面章节中，我们探索并使用了一些 Splunk 应用和插件，例如 Splunk App for Windows、*Nix、Google Maps、Globe、Web Intelligence、Sentimental Analysis、Splunk DB Connect、Splunk Deployment Monitor 等。在深入本章之前，让我们快速回顾一下 Splunk 应用。

Splunk 被设计为一个平台，充当第三方开发者或独立软件供应商可以构建为 Splunk 提供扩展的专用应用程序的基础架构。有两种构建这些扩展的方式：应用和 Technology Add-On。Splunk 应用将扩展功能与标准特性（如已保存搜索、仪表板和已定义的输入）捆绑在一起。此外，它们还在 Splunk 用户界面之上捆绑了自己的用户界面。相比之下，Add-On（或简称为 TA）与应用相比是较小的组件，它们包含附加功能，但没有自己的用户界面。我们将必须使用标准的 Splunk 搜索应用程序来查询通过 Add-On 配置的索引数据。任何拥有相当编程知识的人都可以编写应用和 Add-On。Splunk 拥有一个充满活力的社区，不断创建和分享应用和 Add-On。它托管在 `http://splunk-base.splunk.com/apps/`。

在撰写本书时，Splunkbase 上至少有几百个 Splunk 应用和 Add-On。我们将一些流行且有趣的应用大致分类为监控、网络、安全、数据库、应用和应用服务器。本章的目的是让你熟悉 Splunkbase，并提供一个你可以利用到 Splunk 部署中的热门应用和 Add-On 的简短列表。由于这些应用和 Add-On 经常更新，我们建议你参考 Splunkbase 中的具体应用页面以查找最新信息。本章为每个应用或 Add-On 提供的详细信息仅是基本的，旨在让你快速了解该应用的功能、它捕获的数据来源以及它提供的报告或仪表板。本章不深入介绍列出的应用或 Add-On 的安装、部署、配置和使用细节。你可以利用在前面章节中使用过的应用和 Add-On 的知识和理解，探索一些你可能发现对你的环境有用的列出的应用。我们将要查看的应用和 Add-On 有：

- 应用、数据库和应用服务器
  - Splunk for Oracle WebLogic Server
  - Splunk Hadoop Connect
  - Splunk App for Microsoft Exchange
  - Technology Add-On for Microsoft SQLServer 2012 and 2008r2
  - Splunk App for AWS usage tracking
- 安全
  - Splunk for FISMA
  - Splunk App for PCI Compliance
  - Splunk App for Enterprise Security
  - Splunk for RSA SecurID Appliances
- 监控
  - Splunk App for VMware
  - Splunk App for HadoopOps
  - Splunk App for Active Directory
  - Splunk on Splunk (S.o.S.)
  - Splunk for Nagios
  - Splunk App for Snort
- 网络
  - Cisco Security Suite
  - Splunk for Palo Alto Networks
  - Splunk for Juniper Firewalls
  - Splunk for Barracuda Networks Web Application Firewall

## Splunk for Oracle WebLogic Server

| 字段 | 描述 |
| --- | --- |
| 分类 | 应用、数据库和应用服务器 |
| 应用名称 | Splunk for Oracle WebLogic Server |
| 作者/供应商 | Function1\_Inc |
| 许可证 | Creative Commons BY 3.0 |
| 价格 | 免费 |
| Splunk 版本兼容性 | 5.x, 4.3, 4.2 |
| Splunk 支持 | 否 |
| 应用 URL | `http://splunk-base.splunk.com/apps/72283/splunk-for-oracle-weblogic-server` |
| 描述 | Splunk for Oracle WebLogic Server 是一个通过收集不同日志来监控 WebLogic 服务器组件的应用。Splunk for Oracle WebLogic Server Technology Add-On (TA) 与此应用打包在一起。该应用以不同时间间隔轮询 WLS AdminServer，并将数据加载到 Splunk 中。该应用附带所有域和应用程序的拓扑概览。它提供一组报告，供用户向下钻取到 WLS 实例级别、域内的特定应用程序或多个应用程序、服务器、JMS 实例、JDBC 数据源和 JVM 堆大小。此应用收集整个 WLS 部署的数据，并提供简单易懂的报告和图表。 |

## Splunk Hadoop Connect

| 字段 | 描述 |
| --- | --- |
| 分类 | 应用、数据库和应用服务器 |
| 应用名称 | Splunk Hadoop Connect |
| 作者/供应商 | Splunk |
| 许可证 | Splunk 软件许可协议 |
| 价格 | 免费 |
| Splunk 版本兼容性 | 5.x, 4.3 |
| Splunk 支持 | 是 |
| 应用 URL | `http://splunk-base.splunk.com/apps/57216/splunk-hadoop-connect` |
| 描述 | Splunk Hadoop Connect 应用有助于在 Splunk 和 Hadoop 之间轻松移动数据。你可以使用 Hadoop Connect 的三个关键用例是：将 Splunk 中收集的数据导出到 HDFS 进行进一步批处理。你可以从 Splunk 导出一个收集事件的子集。使用应用用户界面浏览 HDFS 目录和文件。将数据从 HDFS 导入 Splunk，并利用 SPL 和可视化功能构建报告、图表和仪表板。 |

## Splunk App for Microsoft Exchange



| 字段 | 描述 |
| --- | --- |
| 类别 | 应用程序、数据库和应用服务器 |
| 应用名称 | Splunk App for Microsoft Exchange |
| 作者/供应商 | Ahall_splunk |
| 许可证 | Creative Commons BY 3.0 |
| 价格 | 免费 |
| Splunk 版本兼容性 | 5.x, 4.3 |
| Splunk 官方支持 | 是 |
| 应用 URL | `http://splunk-base.splunk.com/apps/28976/splunk-app-for-microsoft-exchange` |
| 描述 | Splunk App for Microsoft Exchange 可消费来自 Microsoft Exchange 环境的日志，并为 Exchange 部署提供监控能力。该应用采集以下数据：Exchange 服务器的 Internet 信息服务 (IIS) 日志、Blackberry Enterprise Server (BES) v5.03 日志、Windows 事件日志、安全日志、Exchange 审计日志、应用程序日志、性能监控数据以及 Senderbase/信誉数据。Splunk App for Microsoft Exchange 提供了多个仪表板，用于概览拓扑结构、消息跟踪、客户端行为、运维、容量规划，识别基础设施问题，监控消息环境中所有服务器的性能，跟踪消息环境中的消息流转，监控客户端使用情况，监控安全事件（如病毒爆发和异常登录），跟踪环境的管理变更，并分析长期邮件运营趋势。 |

## Technology Add-on for Microsoft SQLServer 2012 and 2008r2（Microsoft SQLServer 2012 和 2008r2 技术附加组件）

| 字段 | 描述 |
| --- | --- |
| 类别 | 应用程序、数据库和应用服务器 |
| 应用名称 | Technology Add-On for Microsoft SQLServer 2012 and 2008r2 |
| 作者/供应商 | Ahall_splunk |
| 许可证 | Creative Commons BY 3.0 |
| 价格 | 免费 |
| Splunk 版本兼容性 | 5.x |
| Splunk 官方支持 | 否 |
| 应用 URL | `http://splunk-base.splunk.com/apps/63361/technology-add-on-for-microsoft-sql-server-2008r2` `http://splunk-base.splunk.com/apps/62876/technology-add-on-for-microsoft-sql-server-2012` |
| 描述 | 用于 Microsoft SQL Server 2012 和 2008R2 的附加组件可采集与配置和审计信息相关的数据库日志。这些附加组件可与本书第二章中安装和配置的 Splunk TA for Windows 结合使用。与 Windows TA 配合使用时，你可以从 SQL Server 日志中采集数据，并深入了解 Microsoft SQL Server 2012 和 2008R2 的使用情况。这些附加组件提供有关 SQL Server 配置和数据库利用率的信息。你可以利用标准的 Splunk 搜索来处理和分析安全日志中的信息，并构建自定义仪表板。在撰写本章时，这两个附加组件仅以 beta 版本提供。 |

## Splunk App for AWS usage tracking（Splunk AWS 使用情况跟踪应用）

| 字段 | 描述 |
| --- | --- |
| 类别 | 应用程序、数据库和应用服务器 |
| 应用名称 | Splunk App for AWS usage tracking |
| 作者/供应商 | Nkhetia |
| 许可证 | Creative Commons BY 3.0 |
| 价格 | 免费 |
| Splunk 版本兼容性 | 5.x, 4.3, 4.2, 4.1, 4.x |
| Splunk 官方支持 | 否 |
| 应用 URL | `http://splunk-base.splunk.com/apps/65926/splunk-app-for-aws-usage-tracking` |
| 描述 | Splunk App for AWS 采集与 AWS 上使用的计算和存储资源相关的数据，以及账单数据。通过该应用采集的数据有助于分析 AWS 使用情况，并优化 Amazon EC2 环境的使用。AWS 应用提供了多个预构建的报告和仪表板，展示账单分析、按子账户划分的消费趋势、账单预测、月度账单环比、基于用户指定消费限额的消费警报、实例使用情况、按需实例使用情况、预留实例使用情况、任意时刻未使用的预留实例、按实例类型、可用区、标签和子账户进行的实例使用分析、基线计算和建议。 |

## Splunk for FISMA（Splunk FISMA 应用）

| 字段 | 描述 |
| --- | --- |
| 类别 | 安全 |
| 应用名称 | Splunk for FISMA |
| 作者/供应商 | Mike Wilson |
| 许可证 | Creative Commons BY 3.0 |
| 价格 | 免费 |
| Splunk 版本兼容性 | 5.x, 4.3, 4.2, 4.1, 4.x |
| Splunk 官方支持 | 否 |
| 应用 URL | `http://splunk-base.splunk.com/apps/44883/splunk-for-fisma` |
| 描述 | FISMA 应用帮助企业基于 NIST 800-53 指南进行审计。与大多数采集数据的 Splunk 应用不同，FISMA 应用是一个框架，它依赖其他技术附加组件（如 Splunk TA for Windows 和 Splunk for Unix and Linux 技术附加组件 (*Nix)）来获取数据。其他数据来源包括代理服务器、防火墙、无线安全、漏洞扫描器、网络扫描器、防病毒系统等。一旦来自外部附加组件的数据被映射，FISMA 应用会提供作为仪表板一部分的搜索，你可以在其中查看与访问控制、审计与问责、安全评估和授权、配置管理、应急计划、标识与认证、事件响应、人员安全、风险评估、系统和通信保护以及系统和信息完整性相关的数据。 |

## Splunk App for PCI Compliance（Splunk PCI 合规性应用）

| 字段 | 描述 |
| --- | --- |
| 类别 | 安全 |
| 应用名称 | Splunk App for PCI Compliance |
| 作者/供应商 | Splunk |
| 许可证 | Splunk 软件许可协议 |
| 价格 | 请联系 Splunk |
| Splunk 版本兼容性 | 5.x, 4.3 |
| Splunk 官方支持 | 是 |
| 应用 URL | `http://splunk-base.splunk.com/apps/55494/splunk-app-for-pci-compliance` |
| 描述 | Splunk App for PCI Compliance 基于 PCI 数据安全标准 (PCI DSS) 的要求采集数据并提供监控能力。此应用可与不同的技术附加组件集成，这些附加组件可用于从无线设备、代理服务器、防火墙、入侵检测系统、网络设备、防病毒软件、漏洞管理系统、操作系统以及 Oracle 等数据库采集数据。此应用中的仪表板提供了持卡人数据环境的高级概览，以及调查特定事件或合规性问题的能力。该应用包含的其他仪表板有：* PCI 合规性状态仪表板，提供当前合规状态，可用于每日监控 PCI 合规状态。* 事件审查仪表板，帮助识别威胁并快速响应这些威胁。* 审计仪表板，验证环境的持续监控情况，有助于在事件审查仪表板中查看审计变更。* 资产中心仪表板，帮助识别持卡人数据环境中包含的资产。 |

## Splunk App for Enterprise Security（Splunk 企业安全应用）



| 字段 | 描述 |
| --- | --- |
| 类别 | 安全 |
| 应用名称 | Splunk 企业安全应用 |
| 作者/供应商 | Splunk |
| 许可证 | 商业版 |
| 价格 | 联系 Splunk |
| Splunk 版本兼容性 | 5.x |
| Splunk 官方支持 | 是 |
| 应用 URL | `http://splunk-base.splunk.com/apps/22297/splunk-app-for-enterprise-security` |
| 描述 | Splunk 企业安全应用是一款用于分析 Splunk 中收集的安全数据的应用。该应用有助于收集系统性能信息，并将其与端点、网络和访问安全数据进行关联。可使用技术插件与该应用配合，收集来自防火墙、入侵检测系统等来源的安全相关数据。Splunk 企业安全应用专注于安全事件数据的收集与关联，并提供带有告警功能的监控解决方案。用户可以从聚合数据轻松下钻到原始系统数据，并使用仪表盘可视化数据。应用中的工作流功能允许用户跨数据源调查事件。仪表盘有助于审查事件、端点、网络、身份和审计信息。 |

## Splunk for RSA SecurID 设备

| 字段 | 描述 |
| --- | --- |
| 类别 | 安全 |
| 应用名称 | Splunk for RSA SecurID 设备 |
| 作者/供应商 | Joshd |
| 许可证 | 知识共享 BY 3.0 |
| 价格 | 免费 |
| Splunk 版本兼容性 | 5.x, 4.3, 4.2, 4.1, 4.x |
| Splunk 官方支持 | 否 |
| 应用 URL | `http://splunk-base.splunk.com/apps/33495/splunk-for-rsa-securid-appliances` |
| 描述 | 此 Splunk 应用提供与 RSA SecurID 设备上活动相关的有用数据。该应用设计用于 RSA SecurID Appliance 130 和 230 型号。该应用利用 SNMP 从 RSA 设备捕获数据。Splunk 服务器对来自 SNMP 陷阱的传入数据进行索引，并使事件可用于搜索和分析。此应用提供多个报表，汇总所有访问设备的用户、事件数量、总共失败/成功的登录、连接次数最多的前十台主机以及前十项操作。在用户活动类别报表中包括：成功操作、失败操作、成功操作原因、失败操作原因、按用户统计的登录失败次数、非工作时间事件、系统级操作、运行时级操作和管理员级操作。在网络活动类别报表中包括：按接口统计的接收千字节数、按接口统计的传输千字节数、按接口统计的入站数据包总数、按接口统计的出站数据包总数、TCP 入站/出站数据段总数、UDP 入站/出站数据段总数、TCP 活跃/被动连接打开总数、TCP 和 UDP 错误计数总数等。 |

## Splunk App for VMWare

| 字段 | 描述 |
| --- | --- |
| 类别 | 监控 |
| 应用名称 | Splunk App for VMware |
| 作者/供应商 | Splunk |
| 许可证 | Splunk for VMWare 解决方案的 Splunk 软件许可协议 |
| 价格 | 免费 |
| Splunk 版本兼容性 | 5.x, 4.3 |
| Splunk 官方支持 | 是 |
| 应用 URL | `http://splunk-base.splunk.com/apps/28423/splunk-app-for-vmware` |
| 描述 | Splunk App for VMWare 提供了仪表盘和已保存搜索，可深入洞察虚拟化层。该应用包含四个主要组件：Splunk App for VMWare、TA for vCenter（用于将 vCenter 数据转发到 Splunk）、Splunk for VMWare 虚拟设备转发器（也称为 Splunk FA VM，用于从 VMWare 环境收集数据）以及 TA for VMWare（随 Splunk FA VM 打包）。利用这些组件，您可以直接从 vSphere 主机收集日志和性能指标。Splunk App for VMWare 包含针对从虚拟化层收集的 VMWare 数据的报表、图表、搜索和索引定义。该应用通过从 VMWare 环境收集数据来提供运维智能。您可以使用标准的 Splunk 搜索来分析数据，并为 VMWare 环境中的数据创建报表和告警。 |

## Splunk App for HadoopOps

| 字段 | 描述 |
| --- | --- |
| 类别 | 监控 |
| 应用名称 | Splunk App for HadoopOps |
| 作者/供应商 | Splunk |
| 许可证 | Splunk 软件许可协议 |
| 价格 | 免费 |
| Splunk 版本兼容性 | 5.x, 4.3 |
| Splunk 官方支持 | 是 |
| 应用 URL | `http://splunk-base.splunk.com/apps/57004/splunk-app-for-hadoopops` |
| 描述 | Apache Hadoop 的典型部署以集群方式运行，这使得管理和监控 Hadoop 集群具有挑战性。这正是 Splunk App for HadoopOps 发挥作用的地方，它支持 Cloudera 和 Hortonworks 的 Hadoop 发行版。该应用提供了集群的整体视图，并能对单个日志事件、告警和通知进行索引，以提供对 Hadoop 操作的监控、分析和可视化。该应用在仪表盘中附带了一组预打包的报表，有助于查看集群的当前健康状况以及单个节点的状态以及各个节点上运行的服务。应用的默认仪表盘提供了集群健康状况和状态的视图。节点仪表盘提供了与集群内节点相关的图表，活动仪表盘提供了当前作业活动，作业历史仪表盘提供了历史作业活动。 |

## Splunk App for Active Directory



| 字段 | 描述 |
| --- | --- |
| 类别 | 监控 |
| 应用名称 | Splunk App for Active Directory |
| 作者/供应商 | Ahall_splunk |
| 许可证 | Creative Commons By 3.0 |
| 价格 | 免费 |
| Splunk 版本兼容性 | 5.x, 4.3 |
| Splunk 官方支持 | 是 |
| 应用 URL | `http://splunk-base.splunk.com/apps/51338/splunk-app-for-active-directory` |
| 描述 | Splunk App for Active Directory 为 Windows Server Active Directory (AD) 部署提供监控功能。该应用收集 Windows 事件日志，包括安全、应用程序、系统、分布式文件系统复制、NT 文件复制服务、DNS 服务器、AD 架构变更、AD 林范围运行状况、信息和复制统计信息、域控制器运行状况和性能指标以及 DNS 服务器运行状况信息。该应用提供不同的仪表板。运维仪表板包含 AD 拓扑、域和 DNS 状态的信息。安全仪表板提供 AD 安全配置文件和操作的信息。此外，该仪表板还提供登录失败、试图破坏用户安全设置、用户利用率等信息，并显示环境中所有 AD 对象的审计和报告。预构建的审计报告提供用户、计算机、组和组策略对象的当前状态详情。变更管理仪表板提供对 AD 环境最近所做变更的详细信息。 |

## Splunk on Splunk (S.o.S)

| 字段 | 描述 |
| --- | --- |
| 类别 | 监控 |
| 应用名称 | Splunk on Splunk (S.o.S.) |
| 作者/供应商 | Splunk |
| 许可证 | Splunk 软件许可协议 |
| 价格 | 免费 |
| Splunk 版本兼容性 | 5.x, 4.3, 4.2, 4.1, 4.x |
| Splunk 官方支持 | 是 |
| 应用 URL | `http://splunk-base.splunk.com/apps/29008/sos-splunk-on-splunk` |
| 描述 | Splunk on Splunk (S.o.S.) 是一款利用 Splunk 的监控能力来分析和排查 Splunk 环境中问题的应用。该应用从 Splunk 日志和其他配置文件中收集数据，同时收集内存和 CPU 使用情况（针对 Splunk Web、`splunkd` 和搜索进程）以及其他系统资源信息。该应用提供不同的报告和仪表板，展示 Splunk 相关信息以及不同 Splunk 进程的 CPU/内存使用情况。 |

## Splunk for Nagios

| 字段 | 描述 |
| --- | --- |
| 类别 | 监控 |
| 应用名称 | Splunk for Nagios |
| 作者/供应商 | Luke Harris |
| 许可证 | Lesser GPL |
| 价格 | 免费 |
| Splunk 版本兼容性 | 5.x, 4.3, 4.2, 4.1, 4.x |
| Splunk 官方支持 | 是 |
| 应用 URL | `http://splunk-base.splunk.com/apps/22374/splunk-for-nagios` |
| 描述 | Nagios 是一个监控系统，使组织能够识别和解决 IT 基础设施问题。Splunk for Nagios 应用与 Nagios 集成，可以轻松地将日志文件从 Nagios 服务器加载到 Splunk。该应用在 Splunk 中提供多个报告、仪表板和计划的已保存搜索，用于向 Nagios 发送警报。一些仪表板包括：状态仪表板，显示最近的警报和通知；警报仪表板，用于显示警报历史记录；主机仪表板，包含显示不同指标的图表，如从 Nagios 收集的 CPU、内存、交换空间、负载、磁盘使用率、网络接口利用率、进程等；NAS 仪表板提供存储和配额使用情况的图表；Cisco 网络仪表板附带网络利用率图表。 |

## Splunk App for Snort

| 字段 | 描述 |
| --- | --- |
| 类别 | 监控 |
| 应用名称 | Splunk App for Snort |
| 作者/供应商 | Ayn |
| 许可证 | Creative Commons BY – NC-SA 2.5 |
| 价格 | 免费 |
| Splunk 版本兼容性 | 5.x, 4.3, 4.2, 4.1, 4.x |
| Splunk 官方支持 | 否 |
| 应用 URL | `http://splunk-base.splunk.com/apps/22369/splunk-for-snort-splunk-4x` |
| 描述 | Snort 是由 Sourcefire 开发的开源网络入侵防御和检测系统 (IDS/IPS)。Splunk for Snort 捕获 Snort 日志，并提供仪表板，将捕获的数据以不同的报告和图表形式呈现。此应用提供的最基本功能是从 Snort 日志中提取字段。提取的字段包括：源 IP 地址、目标 IP 地址、源端口、目标端口、网络协议、ID 值、网络接口、名称、签名名称、签名类别、签名分类、签名优先级、服务类型、数据包 IP 头部长度、数据包总长度。该应用包含一个自定义搜索界面。Splunk for Snort 的“地图视图”提供了一个用于查看已触发警报的源 IP 地理位置的仪表板。 |

## Cisco Security Suite

| 字段 | 描述 |
| --- | --- |
| 类别 | 网络 |
| 应用名称 | Cisco Security Suite |
| 作者/供应商 | Will Hayes |
| 许可证 | Splunk 软件许可协议 |
| 价格 | 免费 |
| Splunk 版本兼容性 | 5.x, 4.3, 4.2,4.1, 4.x |
| Splunk 官方支持 | 否 |
| 应用 URL | `http://splunk-base.splunk.com/apps/22300/cisco-security-suite` |
| 描述 | Cisco Security Suite 应用为 Cisco Security Suite 中其他应用和附加组件收集的所有数据提供了一个通用接口。与 Cisco Security Suite 集成的应用和附加组件包括：Splunk for Cisco Client Security Agent (CSA)、Splunk for Cisco IronPort Email Security Appliance (ESA)、Splunk for Cisco IronPort Web Security Appliance (WSA)、Splunk for Cisco Firewalls (PIX, FWSM, ASA)、Splunk for Cisco IPS、Splunk for Cisco MARS。此套件应用利用一个或多个应用或附加组件从不同的 Cisco 设备收集数据。至少需要配置其他应用或附加组件之一以及此主应用才能查看数据。此套件应用附带报告、视图和搜索，这些功能利用从各种 Cisco 设备捕获并索引到 Splunk 的数据。 |

## Splunk for Palo Alto Networks

| 字段 | 描述 |
| --- | --- |
| 类别 | 网络 |
| 应用名称 | Splunk for Palo Alto Networks |
| 作者/供应商 | Monzy |
| 许可证 | Creative Commons BY 3.0 |
| 价格 | 免费 |
| Splunk 版本兼容性 | 5.x |
| Splunk 官方支持 | 否 |
| 应用 URL | `http://splunk-base.splunk.com/apps/22327/splunk-for-palo-alto-networks` |
| 描述 | Splunk for Palo Alto Networks 应用利用 Palo Alto Networks 防火墙提供的数据，为 Palo Alto Networks 设备提供了一个简单易用的报告和分析工具。Palo Alto 设备带有不同的日志，此 Splunk 应用设计用于与默认日志配置配合使用。此应用帮助用户关联跨不同来源的数据，例如不同网络域中的应用程序和用户活动。该应用提供了一套全面的报告和仪表板，涵盖威胁详情、添加和删除特定主机、IP 地址、用户 ID 等功能。还可以使用 Splunk 标准搜索机制，基于从 Palo Alto Networks 设备日志捕获的数据创建自定义报告。 |

## Splunk for Juniper Firewalls



| 字段 | 描述 |
| --- | --- |
| 类别 | 网络 |
| 应用名称 | Splunk for Juniper Firewalls |
| 作者/供应商 | Defensive-ISS |
| 许可证 | Creative Commons BY 3.0 |
| 价格 | 免费 |
| 兼容的 Splunk 版本 | 5.x, 4.3, 4.2, 4.1, 4.x |
| Splunk 官方支持 | 否 |
| 应用 URL | `http://splunk-base.splunk.com/apps/57437/splunk-for-juniper-firewalls` |
| 描述 | Splunk for Juniper Firewalls 应用用于从 Juniper 防火墙设备 ISG 和 SSG 平台收集数据。该应用提供了多种不同的仪表板，包括：威胁摘要仪表板、深度检测摘要仪表板、防火墙摘要仪表板、地理摘要仪表板以及变更管理仪表板。 |

## Splunk for Barracuda Networks Web Application Firewall

| 字段 | 描述 |
| --- | --- |
| 类别 | 网络 |
| 应用名称 | Splunk for Barracuda Networks Web Application Firewall |
| 作者/供应商 | Barracuda_networks |
| 许可证 | Creative Commons BY 3.0 |
| 价格 | 免费 |
| 兼容的 Splunk 版本 | 5.x, 4.3, 4.2, 4.1, 4.x |
| Splunk 官方支持 | 否 |
| 应用 URL | `http://splunk-base.splunk.com/apps/67085/splunk-for-barracuda-networks-web-application-firewall` |
| 描述 | Splunk App for Barracuda Networks Web Application Firewall 用于收集防火墙提供的日志，以及报告和仪表板。该应用与 Barracuda Web Application Firewall 固件 7.7 或更高版本兼容，并使用 TCP/UDP 收集数据。其实时仪表板、报告和搜索字段可用于调查收集到的数据。还提供了特定的报告来查看安全、流量和审计数据。报告还利用地理位置映射来显示攻击客户端的所在位置。 |

## 索引

![images](img/square1.jpg) A

- 加速
- 报告
- 访问计数
- 航空公司
- `dedup` 命令
- 大型数据集
- 负载与使用
- 问题
- 报告命令
- Splunk
- 摘要索引
- 摘要状态
- 统计信息
- 到达延误
- 商业航班
- `count` 函数
- `delete` 命令
- `distinct` 函数
- 航班数据
- `groupby` 子句
- `max` 函数
- 准点航班
- 计划飞行时间
- Splunk
- `stats` 命令
- `tscollect` 命令
- `tsidx`
- `tstats` 命令
- “高级搜索”
- 航空公司准点表现项目
- 机场流量分析
- 数据分析
- 初生方法论
- 航班数据
- 航班追踪
- 初步方法论
- `lookup` 表
- Splunk
- 可视化
- 航空公司数据分析
- `AirlineID` 字段
- `carrier` 字段



计数

航班数据

策略

UniqueCarrier 字段

可视化

面积图

柱状图

效果

limit 参数

折线图

按月

迷你图

stats 函数

timechart 命令

按年

机场数据分析

航空公司产出

计数

达拉斯/沃斯堡

仪表板

dedup 命令

Origin 和 Dest 字段

OriginCityName 字段

valid 选项

警报

严重错误

创建警报

相关事件

为警报选择节流

触发的警报

定义

登录失败

警报操作

警报管理器

创建警报

触发失败警报的事件

搜索故障事件

基于结果的警报

产品销售

警报操作

警报管理器

创建警报

计划警报

共享警报

时间修饰符

昨日总销量

昨日购买警报

滚动窗口警报

计划警报

印度尼西亚安塔拉新闻社

append 命令

![images](img/square1.jpg) B

大数据

替代数据处理技术

商业潜力

CARWINGS 系统

分类

定义

端到端交易

Facebook

互联网统计

LinkedIn

机器数据

Netflix

Pandora

关系数据库

社交媒体

结构化数据

Twitter

非结构化数据

体量、速度和多样性

沃尔玛

bigdbook 服务器

跳出率

![images](img/square1.jpg) C

Chart 命令



-   eval 命令

-   GET 和 POST 请求

-   HTTP 404 错误

-   购买量与浏览量，产品类别

-   购买趋势

-   交易时长

-   类型

-   思科安全套件

-   集群

-   索引

-   主节点功能

-   原始数据

-   复制因子

-   复制过程

-   搜索因子

-   设置

-   集群概览报告

-   完整概览报告

-   启用集群对等节点

-   启用主节点

-   搜索头仪表盘

-   典型集群拓扑

-   命令行界面 (CLI)

-   逗号分隔值 (CSV)

-   下载数据

-   航班数据

-   优势

-   航空公司

-   航班准点性能

-   到达性能

-   AWK

-   取消与改航

-   取消与未取消航班

-   CHECK_FOR_HEADER 属性

-   clean 命令

-   正确的事件断点

-   数据描述

-   数据预览

-   延误

-   目的地点

-   Div2TailNum

-   改航机场信息

-   熟悉度

-   航班摘要

-   登机口返回信息

-   索引

-   出发地点

-   弹出窗口

-   props.conf 配置

-   源类型

-   电子表格

-   strptime() 表达式

-   时间段

-   时间戳定义

-   时间戳处理器修改

-   TranStats 网站

-   类型

-   用户界面

-   字段映射

-   时间戳考量

-   消费者报告

-   count 函数

-   CRSElapsedTime 字段

-   ![images](img/square1.jpg)  D

-   仪表盘

-   高级分析数据

-   列作为可视化

-   创建

-   编辑

-   编辑可视化

-   404 错误报告

-   索引活动

-   输入活动




关键指标

监控（*参见* 网站监控）

`MyGizmoStore.com`

*Nix 应用

*Nix CPU 概览

*Nix 日志记录

*Nix 内存概览

购买与浏览

购买趋势报告

搜索活动

服务器活动

Splunk 搜索应用

Splunk Web 用户界面

“状态”菜单

热门产品购买报告

事务持续时间报告

数据库服务器

数据源

应用日志数据

应用和插件

“应用”菜单

启用 Windows 事件日志

事件

*Nix TA（技术插件）

*Nix 技术

脚本化输入

搜索栏

设置界面

Splunk 应用

Splunk *Nix (TA)

Windows

广泛类别

点击流数据文件

配置文件

数据生成器

文件和目录

access.log

BigDBook-Test

数据源

数据类型

事件

索引创建

索引页面

`MyGizmoStore.com`

mygizmostoreindex

一次性模式

预览数据

设置来源类型

来源类型

时间戳

防火墙日志

转发器

硬件基础设施

机器数据

网络来源

操作系统层面

样本数据

访问日志文件

主机字段

监控

MyGizmoStore 访问日志

mygizmostoreindex

Python 脚本

运行测试

已保存的配置

设置主机选项

Unix 命令

压缩技术

脚本输出

社交媒体来源

Splunk CLI（命令行界面）

Splunk Web

Web 日志文件

Windows 数据

数据可视化

优势



柱状图格式

仪表板（*参见* 仪表板）

格式化图表

地球仪（*参见* 地球仪应用）

Google Maps 应用（*参见* Google Maps 应用）

洞察

报表构建器

chart 命令（*参见* Chart 命令）

图表标题

clientip 字段

定义报表

格式化报表

径向仪表

稀有值

stats 命令

timechart 命令（*参见* Timechart 命令）

最高值

随时间变化的值

电子表格/表格报表

表格格式

时间线条形图

更新后的图表

dedup 命令

延误分析

航空公司

机场

ArrDel115 字段

条形图

原因

eval 命令

PCTDelayed 字段

重新排序的列

航班数据

冬季 *与* 夏季

起飞表现

部署监控器

转发器详细信息

按来源类型的信息

来自的警告

部署服务器

类

配置

转发器配置

三个基于 Unix 系统上的转发器

拓扑

典型多层应用

工作原理

备降机场信息

![images](img/square1.jpg)  E

早期结构绑定

错误饼图

eval 命令

eventstats 命令

![images](img/square1.jpg)  F

联邦航空管理局 (FAA)

Feedory

航班数据分析

CRSElapsedTime 字段

dedup 命令

飞行时间

最长计划航班

MaxFlightTime

输出

计划飞行时间

最短计划航班

stats 命令

表格形式

时间—条形图

转发器

数据收集方法

定义

功能特性

安装



类型与操作系统

在 Unix 系统上

在 Windows 系统上

拓扑

混合操作系统拓扑

路由拓扑

在服务器上

共享磁盘拓扑

在 Windows 系统上

类型

Foursquare 签到

分类字段

签到格式

签到——柱状图

签到模式

每日签到模式

每小时签到模式

核心 API

定义

性别活动

性别字段

格林威治标准时间（GMT）

谷歌地图应用

活动索引概览界面

JetSetter

JSON

位置字段

Mashup 应用

在纽约

数量

纽约指数

父级字段

披萨店（Pizzaiolo）

复数名称字段

实时 API

反向地理编码

在旧金山

旧金山指数

Splunk

Splunk 用户时区

4sqwifi 应用

周日早午餐搜索

早午餐场所

分类

日期时间字段

eval 命令

食品分类事件

全名字段

大都市区域

时区注意事项

timeZone 字段

Unix 时间戳

用户字段

验证搜索

Zagat

![images](img/square1.jpg) G

登机口返回信息

geoip 命令

地理位置信息。*另请参阅* Foursquare 签到

谷歌地图应用

Splunk 总部

Globe 应用

ChromeGlobe

客户活动

安装

保存搜索

视图

谷歌地图应用

geoip 命令

安装

`MyGizmoStore.com` IP 地址全球分布

`MyGizmoStore.com` IP 地址在美国的分布

保存搜索

格林威治标准时间（GMT）

![images](img/square1.jpg) H



Hadoop 软件框架

半正矢公式（Haversine formula）

重型转发器

高可用性。*参见* 集群

HTTP 状态

- 钻取图表
- HTTP 状态不佳的热门页面
- HTTP 状态不佳的流量
- HTTP 状态良好的流量

![images](img/square1.jpg) I

多式联运交通数据库

互联网

![images](img/square1.jpg) J

Java 服务器桥 (JSB)

JBoss 服务器

JetSetter

Juniper 防火墙

![images](img/square1.jpg) K

知识包

![images](img/square1.jpg) L

语言命令

后期结构绑定

词库

轻型转发器

行合并属性

Lingo

Linux 服务器

日志文件

- 语义日志记录（*参见* 语义日志记录）
- 使用 Splunk
- 传统分析
- 典型日志文件

查找表

- 航空公司名称
- 高级选项
- 自动查找
- 代码和描述
- CSV 文件
- 定义
- 目标应用
- 事件
- 航班计数
- 准点率
- 优化
- 正则表达式
- 替换函数
- `tostring` 函数
- `tstats` 命令
- `UniqueCarrier` 字段
- 上传
- 美联航 871 航班
- 飞机信息
- `ARData.pdf` 文件
- 自动查找
- CSV 文件
- 数据库查找
- 导出结果链接
- 航班数据事件
- `ltrim` 函数
- 主文件
- `MFR_MDL_CODE` 字段
- `MFR` 和 `MODEL`
- 网络图
- `N_NUMBER` 字段
- 随时间变化
- `ltrim` 函数

![images](img/square1.jpg) M

宏

- 反引号（`` ` ``）
- `calcp`
- `countd`
- 基于 `eval` 的定义
- 搜索
- Splunk
- 字符串
- `makemv` 命令



N, O

- 朴素贝叶斯分类器

P, Q

- Palo Alto Networks
- `per_hour` 函数
- 每个结果警报
- 极性
- `props.conf` 文件
- Python 程序

R

- 简易信息聚合 (RSS)
- 远程数据采集
- 部署监视器 (*参见* 部署监视器)
- 部署服务器 (*参见* 部署服务器)
- 转发器 (*参见* 转发器)
- `restartSplunkd` 属性
- 转推
- 反向地理编码
- 滚动窗口警报

S

- 桑德斯模型
- `sanders/train` 子目录
- 扩展
  - 部署服务器
  - 分布式搜索
  - 启用
  - 挂载包
  - 多搜索头
  - 搜索头
  - 拓扑结构
  - 转发器分发
  - 地理分布式拓扑
  - 负载均衡
  - 搜索对等节点
- 预定警报
- 调度器活动
- 搜索处理语言 (SPL)
  - 合并访问日志
  - 消费者/业务用户
  - 描述
  - 用户代理/浏览器
  - Web 服务器
  - `eval` 命令
  - 过滤
    - `dedup` 命令
    - `head` 命令
    - 产品
    - `tail` 命令
  - 分组
    - 加入购物车和购买
    - 分布
    - 事件限定
    - 失败事务
    - HTTP 状态码
    - 最大跨度与最大事件数
    - 购买动作
    - `sort` 命令
    - `stats` 命令
    - 事务
  - 日志数据
    - 操作字段
    - 操作方法
    - 分析机器
    - 数据生成器
    - 字段列表
    - 列表类别
    - `MyGizmoStore.com`
    - 处理与分析
    - 选择并感兴趣的字段
    - `sourcetype` 字段



时间线条形图

时间选择器

报表功能

addcoltotals 命令

count 函数

distinct_count

fields 命令

HTTP 404 状态

HTTP 状态码

IP 地址

购买行为

引荐站点

保存链接

保存搜索选项

search 命令

stats 命令

sum 函数

热门浏览器

top 命令

URL

useragent 字段

Web 应用

排序

语义化日志记录

最佳实践/技巧

c7 索引

自定义时间范围

日均交易时间

按产品的日销售额

数据预览

定义

每小时平均交易时间

未完成交易

索引活动概览

已加载数据验证

加载新文件

伪代码

search 命令

transaction 命令

用户画像

情感分析

商业用途

频率

互联网

词库

最大熵

多值字段

朴素贝叶斯分类器

否定

N-gram

意见、观点、信念、信条

词性

极性

极性范围

情感分析应用

颜色范围选择对话框

自定义径向仪表盘

默认径向仪表盘

编辑可视化

全局启用命令

语言

好恶仪表盘

训练与测试数据

Twitter 数据

语法

词项存在性

训练数据

训练文档

基于 Twitter 的情感分析

向量机

世界情感指标项目

准确率

分类

RSS


Splunk

培训选项

可视化

服务级别协议 (SLA)

社交媒体

固态硬盘 (SSD)

迷你图

Splunk

CSV 文件（*参见*逗号分隔值 (CSV)）

关系数据库

数据库连接

FD-DB 数据库连接

人类可读格式

JDBC 驱动程序

键值格式

监控

MySQL 表

SQL_FlightData

时间戳

Splunk 应用

Active Directory

附加组件

AWS 用量跟踪

Barracuda Networks Web 应用防火墙

Cisco 安全套件

企业安全

FISMA

功能

Hadoop Connect

HadoopOps

Juniper 防火墙

Microsoft Exchange

Microsoft SQLServer 2012 和 2008 r2

Nagios

Oracle WebLogic Server

Palo Alto Networks

PCI 合规性

RSA SecurID 设备

Snort

S.o.S

Splunkbase

VMWare

Splunk 开发者论坛

Splunk on Splunk (S.o.S)

Splunk 性能

磁盘速度与搜索

环境

耗费资源的搜索

头节点性能

历史运行时间

搜索类型

状态选项

摘要选项卡

索引性能

数据类型

JBoss 日志文件

基于 SPARC 的服务器

系统日志文件

搜索类型

CPU 密集型或 I/O 密集型

密集搜索

执行成本

大海捞针式搜索

关键词

罕见搜索

资源消耗

搜索头节点

搜索性能

稀疏搜索

SpeedOfLight

字符串

使用情况

Web 访问日志文件

stateOnClient 属性

stats 命令


统计功能

![images](img/square1.jpg) T

时间图命令

页面浏览量和购买量

跨度选项

按产品统计的热门购买量

tostring 函数

train.py 程序

transaction 命令

tscollect 命令

tstats 命令

推文

140 字符限制

定义

话题标签

语言

行话

加载到 Splunk

created_at 字段

删除事件

JSON 事件

Python 程序

源字段

spath 命令

测试事件

__time 字段

时间戳

时间戳位置规范

user.created_at 字段

私信

@回复

转发

示例流

created_at 字段

删除事件

实体字段

followers_count 字段

格式

friends_count 字段

JSON 格式

lang 字段

名称和显示名称字段

retweeted_status 字段

SampleTweets.out 文件

源字段

statuses_count 字段

文本字段

__time 字段

用户字段

@用户名

utc_offset 字段

Twitter

API

应用

语料库

date_hour 字段

datetime 字段

删除结构

favstar_bot

消防水带

关注者

关注者数量 (followers_count)

正在关注

朋友数量 (friends_count)

地理位置信息

地理标签

语言

位置字段

微博服务

模型

最热门词汇

气泡图

前 200 条推文中的

inputlookup 命令

查找表

makemv 命令

多值字段



搜索命令

停用词列表

`StopWords.csv`

子搜索

top 命令

可视化

词云

每小时推文数量

个性化趋势

实时趋势

应用仪表板

应用安装

黑莓时区

实体对象

`eval` 命令

话题标签

`mvexpand` 命令

`mvfilter` 函数过滤器

饼图

正则表达式

`rex` 命令

脚本化输入

搜索命令

`source` 字段

`spath` 命令

`table` 命令

文本字段

时区面板

用户代理

`statuses_count`

测试数据

`time_zone` 字段

推文 (*参见* 推文)

美国、巴西和日本

用户资料

`utc_offset` 字段

基于 Twitter 的情绪分析

![images](img/square1.jpg)  U

通用转发器

Unix 时间戳

`Usenull` 选项

用户人口统计学

字段

`geoip` 命令

按地区

UTC 时区

![images](img/square1.jpg)  V

多样性

速度

体量

![images](img/square1.jpg)  W, X, Y

Web 服务器

Web 站点监控

业务

跳出率

独立访客数

用户人口统计学

创建仪表板

所有报告的仪表板

编辑仪表板

编辑可视化

按主机面板统计的点击量

IT 运维

按主机统计的点击量

排除内部访问后按主机统计的点击量

HTTP 状态不正常的热门页面

HTTP 状态不正常的流量

HTTP 状态正常的流量

启动仪表板

报告

按主机统计的点击量的表格数据

世界情绪指标项目

分类

数据库连接

`fy_items` 数据库表

`id` 字段

`postid` 字段

`pubDate` 字段

RSS

Splunk

SQL 查询

`title` 字段

可视化

![images](img/square1.jpg)  Z

Zagat
