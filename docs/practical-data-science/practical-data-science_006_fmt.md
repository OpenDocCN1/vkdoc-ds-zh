# R 与 Spark 的融合

我想讨论另一个包，因为我认为掌握它是你处理当前及未来数据科学工作所需的一项重要技能。这个包将 R 环境与分布式 Spark 环境相连接，并为 Spark 内置的机器学习算法提供了一个接口。我的许多客户都在使用 Spark 作为其数据环境的标准接口。

理解这种协作能使你支持大规模环境的处理，而无需对 R 处理代码进行重大修改。文档地址位于 [`https://cran.r-project.org/web/packages/sparklyr/sparklyr.pdf`](https://cran.r-project.org/web/packages/sparklyr/sparklyr.pdf)。关于该包的最新信息，请参阅 [`https://CRAN.R-project.org/package=sparklyr`](https://cran.r-project.org/package=sparklyr)。

要安装该包，建议你打开 RStudio IDE 并使用以下命令：

```
install.packages("sparklyr")
```

`sparklyr` 是 Apache Spark 的一个直接 R 接口，旨在提供一个完整的 `dplyr` 后端。一旦 Spark 数据集的过滤和聚合操作在下游的大规模环境中完成，该包就会将数据导入 R 环境进行分析和可视化。

## 示例数据

本书使用了多个示例的数据。在下一节中，我将解释如何使用你已安装的 VKHCG 环境来创建我将在这些示例中使用的数据集。

注意：这些示例数据的处理过程分散在全书中。我这里只是对数据进行快速介绍。在后续章节开始处理数据时，我将更详细地讨论每个数据集。目前，只需留意数据的位置和通用格式即可。

这是完成示例所需的最低限度数据。

注意

请为你的示例选择一个主目录：

- 如果在 Windows 上，我建议使用 `C:/VKHCG`。

- 如果在 Linux 上，我建议使用 `$home/VKHCG`。

在你的 R 环境中执行以下操作，以设置基本的 R 数据集处理：

```
######################################################
rm(list=ls()) # 将移除所有对象
######################################################
MY_INSTALL_DIR = ""
######################################################
if (file.exists (MY_INSTALL_DIR)==0) dir.create(MY_INSTALL_DIR)
subdirname = paste0(MY_INSTALL_DIR,  "/Vermeulen")
if (file.exists(subdirname)==0) dir.create(subdirname)
######################################################
setwd(MY_INSTALL_DIR)
######################################################
if (length(sessionInfo()$otherPkgs) > 0)
lapply(paste('package:',names(sessionInfo()$otherPkgs),sep=""),
detach,character.only=TRUE,unload=TRUE)
######################################################
install.packages("readr")
######################################################
install.packages("data.table")
######################################################
```

现在，我将讨论你将在全书中使用的数据集。

注意

我仅讨论数据源的描述。现在不需要将数据加载到 R 中。在处理示例时，将有充足的时间来加载和处理数据。

### IP 地址数据集

VKHCG 网络采用 IP 版本 4 网络地址。IPv4 协议使用以下结构的结构化地址格式：

```
IP Address = w.x.y.z
```

这四个部分各自可以包含 0 到 255 之间的值。IPv4 协议中共有 2 的 32 次方个 IP 地址，因此从通用角度来看，可能的地址超过 40 亿个。

以下是处理 IPv4 地址时公认的计算公式。给定一个 IP 地址 = `w.x.y.z`，则 IP 数值 = `16777216*w + 65536*x + 256*y + z`。给定一个 IP 数值，则：

- `w = int (IP Number / 16777216) % 256`

- `x = int (IP Number / 65536) % 256`

- `y = int (IP Number / 256) % 256`

- `z = int (IP Number) % 256`

由此生成 IP 地址 = `w.x.y.z`。

地址被分类为 A 类、B 类、C 类或 D 类。

| 类别 | 第一个八位组十进制范围 (w) |
| --- | --- |
| A | 1–126* |
| B | 128–191 |
| C | 192–223 |
| D | 224–239 |
| E | 240–254 |

* A 类地址 `127.0.0.0` 到 `127.255.255.255` 被保留用于回环和诊断功能。

以下地址已被分配为私有地址。

| 类别 | 私有网络 | 子网掩码 | 地址范围 |
| --- | --- | --- | --- |
| A | `10.0.0.0` | `255.0.0.0` | `10.0.0.0`–`10.255.255.255` |
| B | `172.16.0.0`–`172.31.0.0` | `255.240.0.0` | `172.16.0.0`–`172.31.255.255` |
| C | `192.168.0.0` | `255.255.0.0` | `192.168.0.0`–`192.168.255.255` |

这些地址可以被任何公司网络在其内部网络中使用。

我使用 C 类地址（`192.168.0.1`–`192.168.0.255`）生成了一系列 IP 地址，即 255 个地址，您在示例中将需要它们。

以下是供示例使用的数据：

在 `VKHCG\01-Vermeulen\00-RawData` 中：

| 列名 | 描述 |
| --- | --- |
| `w` | 0 到 255 之间的整数 |
| `x` | 0 到 255 之间的整数 |
| `y` | 0 到 255 之间的整数 |
| `z` | 0 到 255 之间的整数 |
| `IP Address` | 格式为 `w.x.y.z` 的文本 |
| `IP Number` | 整数 |

- 一个用于内部网络使用的 C 类地址块：
  - 数据文件：`IP_DATA_C_VKHCG.csv`
  - 文件类型：逗号分隔值 (CSV)
  - 记录数量：255
  - 数据中的列：

让我们来研究下一个数据集。

在 `VKHCG\01-Vermeulen\00-RawData` 中（此数据集包含指南，说明哪个 IP 数值分配给公司客户网络中的哪个位置）：

| 列名 | 描述 |
| --- | --- |
| `ID` | 文本 ID |
| `Country` | 国家代码 |
| `PlaceName` | 位置名称 |
| `PostCode` | 位置邮政编码 |
| `Latitude` | 位置纬度 |
| `Longitude` | 位置经度 |
| `FirstIPNumber` | 位置中的第一个 IP 数值 |
| `LastIPNumber` | 位置中的最后一个 IP 数值 |

- 文件类型：逗号分隔值 (CSV)
- 数据文件：`IP_DATA_ALL.csv`
- 记录数量：1,247,502
- 数据中的列：

让我们来研究下一个数据集。

在 `VKHCG\01-Vermeulen\00-RawData` 中（此数据集包含哪个 IP 数值被分配给公司自有外部网络中哪个位置的信息）：

| 列名 | 描述 |
| --- | --- |
| `ID` | 文本 ID |
| `Country` | 国家代码 |
| `PlaceName` | 位置名称 |
| `PostCode` | 位置邮政编码 |
| `Latitude` | 位置纬度 |
| `Longitude` | 位置经度 |
| `FirstIPNumber` | 位置中的第一个 IP 数值 |
| `LastIPNumber` | 位置中的最后一个 IP 数值 |

# VKHCG 公司数据集概览

- 文件类型：逗号分隔值 (CSV)
- 数据文件：`IP_DATA_CORE.csv`
- 记录数量：3,562

## 客户数据集

VKHCG 将其客户分组到广告牌上，并按照每个广告牌的定价模式付费。

在 `VKHCG\ 02-Krennwallner\00-RawData` 中（此数据集包含所有客户广告牌的位置）：

| 列名 | 描述 |
| --- | --- |
| `ID` | 广告牌的文本 ID |
| `Country` | 广告牌的国家代码 |
| `PlaceName` | 广告牌位置名称 |
| `Latitude` | 广告牌位置纬度 |
| `Longitude` | 广告牌位置经度 |

- 文件类型：逗号分隔值 (CSV)
- 数据文件：`DE_Billboard_Locations.csv`
- 记录数量：8,873

## 物流数据集

VKHCG 拥有多个仓库和商店。我已将这些建筑物的位置分组到三个数据集中。

### 邮政编码

在 `VKHCG\03-Hillman\00-RawData` 中（数据集一包含完整的英国邮政编码列表）：

| 列名 | 描述 |
| --- | --- |
| `ID` | 英国邮政编码的文本 ID |
| `Country` | 国家代码 |
| `PostCode` | 英国邮政编码 |
| `PlaceName` | 英国位置名称 |
| `Region` | 英国地区 |
| `RegionCode` | 英国地区代码 |
| `AreaName` | 英国区域名称 |

- 文件类型：逗号分隔值 (CSV)
- 数据文件：`GB_Postcode_Full.csv`
- 记录数量：1,714,591

### 仓库数据集

在 `VKHCG\03-Hillman\00-RawData` 中（数据集二包含完整的英国仓库位置）：

| 列名 | 描述 |
| --- | --- |
| `id` | 位置文本 ID |
| `postcode` | 仓库的邮政编码 |
| `latitude` | 仓库的纬度 |
| `longitude` | 仓库的经度 |

- 文件类型：逗号分隔值 (CSV)
- 数据文件：`GB_Postcode_Warehouse.csv`
- 记录数量：3,005

### 商店数据集

在 `VKHCG\03-Hillman\00-RawData` 中（数据集三包含完整的英国商店位置）：

| 列名 | 描述 |
| --- | --- |
| `id` | 位置文本 ID |
| `postcode` | 商店的邮政编码 |
| `latitude` | 商店的纬度 |
| `longitude` | 商店的经度 |
| `FirstCode` | 商店邮政编码的第一部分 |
| `SecondCode` | 商店邮政编码的第二部分 |

- 文件类型：逗号分隔值 (CSV)
- 数据文件：`GB_Postcodes_Shops.csv`
- 记录数量：1,048,575

### 汇率数据集

在 `VKHCG\04-Clark\00-RawData` 中（数据集一包含 4,697 天期间内欧元兑各种货币的汇率）：

| 列名 | 描述 |
| --- | --- |
| `Date` | 汇率数据所在行对应的日期 |
| `Exchange rate codes` | 41 列汇率代码 |
| `Exchange rates` | 行与列交叉点处特定汇率的数值网格 |

- 文件类型：逗号分隔值 (CSV) 数据透视表
- 数据文件：`Euro_ExchangeRates.csv`
- 记录数量：4,697

### 损益表数据集

在 `VKHCG\04-Clark\00-RawData` 中（数据集二包含损益表结果）：

| 列名 | 描述 |
| --- | --- |
| `QTR` | 年份/季度 |
| `TypeOfEntry` | 损益条目类型 |
| `ProductClass1` | 类别条目一 |
| `ProductClass2` | 类别条目二 |
| `ProductClass3` | 类别条目三 |
| `Amount` | 金额/价格 |
| `QTY` | 物品数量 |

- 文件类型：逗号分隔值 (CSV)
- 数据文件：`Profit_And_Loss.csv`
- 记录数量：2,442

## 总结

至此，我已经向您介绍了这家公司，以便您能够完成后续章节中的示例。接下来，我将介绍分层框架，向您介绍实用数据科学的基本框架。