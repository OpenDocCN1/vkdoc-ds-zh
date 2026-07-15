# 7. 检索超步骤

`Retrieve` 超步骤是一种实用方法，用于将包含各种外部数据源的数据湖完整导入处理生态系统。`Retrieve` 超步骤是您的数据科学与源系统之间的首次接触。我将引导您掌握一种方法论，指导您如何通过运用数据科学技能，处理从数据发现到获取评估当前系统所需全部数据的整个过程。

成功检索数据是确保您实施优质数据科学的重要基石。数据血缘以最低粒度级别提供数据元素的审计追踪，确保完整的数据治理。数据治理支持系统指南、处理策略、政策制定及处理实施的元数据管理。数据质量和主数据管理有助于在您提供完整数据源元数据的前提下，用更多业务价值丰富数据血缘。

> **提示**
>
> 我建议导入所有通过该层的数据，这将确保数据血缘和治理。

`Retrieve` 超步骤支撑着生态系统的边界，您的数据科学在此与外部数据世界直接接触。我将推荐一套当前可用的数据结构，用于处理探索关键业务知识所需应对的数据洪流。

## 数据湖

Pentaho 公司的 CTO 詹姆斯·狄克逊被公认为创造了“数据湖”这一术语。正如他在如今著名的博客¹中所描述的那样：

> 如果你将数据集市视为瓶装水商店——经过净化、包装并结构化以便轻松饮用——那么数据湖就是一片更接近自然状态的大水域。

该博客中还有一段不那么常被引用的内容：

> 数据湖中的内容从源头源源不断地流入，充盈湖泊，而湖的各种使用者可以前来考察、潜入或取样。

自这篇博客以来，大量的研究和商业活动分类使该术语声名鹊起，甚至声名狼藉。不幸的是，该术语包罗万象，以至于你可以将生态系统的边缘以及业务涉及的任何数据都描述为数据湖的一部分。我曾花费数小时甚至数天，在智库、理论和科学辩论中探讨数据湖的复杂性和细微差别。

因此，基于本书的实践和流程，我将数据湖定义如下：公司的数据湖涵盖您的业务被授权处理的所有数据，旨在提升企业核心成果的盈利能力。数据湖是您的公司在业务生命周期内与之交互的完整数据世界。简而言之，如果您为执行业务任务而生成或消费数据，那么该数据就属于您公司的数据湖。

我将使用与天然湖泊相关的术语来解释具体的数据湖概念。因此，正如湖泊需要河流和溪流来补给水源，数据湖将不可避免地消耗来自上游的数据源洪流，并将其输送给下游合作伙伴。

> **提示**
>
> 切勿忽视未处理数据源的价值。

我曾有一位商业客户，他收到了物流公司整个季度的航运清单光盘，却仅仅将其收集在一个塑料容器中长达五年，因为他认为这些数据是多余的，因为他已在线获取了每批货物的电子邮件。幸运的是，我的一名实习生花时间加载了一张光盘，并注意到完整的供应链路径和编码程序也包含在该光盘中。通过将这些新数据添加到公司现有的数据湖中，这一新信息使数据湖在物流实际成本方面丰富了超过 30%。不要限制您数据湖的集水区域，因为最有利可图的知识通常来自将您过去认为不重要的数据进行融合。

> **注意**
>
> 我在本书中使用了大量代码片段来演示各种概念。我假定您已准备好支持 `R` 和 `Python` 的环境来尝试这些示例。执行这些流程有助于知识传递。

## 数据沼泽

既然我谈到了数据湖以及加载业务数据以发现未知价值的重要性，我也必须解释数据沼泽。我听过并见过各种断言数据湖是邪恶的演示。我持不同观点。任何不受管理的数据结构都是邪恶的。另一方面，我坦然承认，我曾花费大量时间修复包含这些邪恶数据生态系统的结构。

数据沼泽就是未经管理的数据湖。它们并不可怕。它们需要被驯服。

以下是我避免数据沼泽的四个关键步骤。

### 从具体的业务问题开始

将大量数据随意倾倒进数据湖，而心中没有明确目的，将带来巨大的业务风险。我曾无数次看到数据加载协议中声称要加载所有数据。请参阅关于业务需求的第 4 章中关于“模糊性”的部分。

我的建议是，对您拥有的全部数据集进行全面分析，然后对数据应用元数据分类，明确完整的数据血缘，以允许其进入数据湖。必须使数据湖能够收集回答业务问题所需的数据。

例如，如果您想计算过去五年的货运模式，则需要数据湖中存在过去五年与货运相关的所有数据。您还需要启动一个流程，按照约定的时间间隔收集未来的相关数据，以确保一年后您能使用相同的数据科学方法来评估之前五年的数据。数据血缘确保您在得到结果后能够追溯到数据源。

> **提示**
>
> 至少，要确定数据的真实来源以及数据进入数据湖时的完整时间戳。您能提供的数据血缘越多越好。确保您拥有经过认证的数据源。

**示例：**

更好的说法是：“我发现您的货运模式良好，并且我已经通过让我们的物流经理签字确认真实性和完整性，确保我们拥有过去五年的所有数据。” 这种陈述为您的数据科学提供了坚实的基础和经过认证的血缘。

去年，我手下的一名初级数据科学家试图说服一位首席财务官，声称他在档案室发现的那些来源不明、日期不详、装有货运发票的光盘，足以作为他四周数据科学工作的良好数据血缘。遗憾的是，我必须报告，那份结果文档加上那些光盘，现在已成为我的“如何不进行数据科学”演示的一部分！

同一名数据科学家，十天后，使用相同的数据科学算法，但配以经过认证的血缘，为我的客户提供了重大的成本节约方案。

### 数据治理

数据治理、数据访问和数据安全的作用并不会因为数据湖中的数据量庞大而消失。如果不加以管理，它们只会汇聚成一个更糟糕的问题。

> **警告**
>
> 危险区域！在拍字节甚至太字节量级的数据湖中查找一条未经管理的数据，将耗费您数小时的时间。

请提前在数据治理上投入时间，因为事后恢复并不容易。我通常会将项目 70%以上的时间用于数据源的治理和分类。我将在第 8 章至第 11 章中演示，本章所建立的完善数据湖如何使数据处理变得如此简单。

#### 数据源目录

将摄入数据链接到数据源的元数据是任何数据湖的必备之物。建议你针对所处理的数据，遵循以下通用规则。

-   唯一数据目录编号：我通常使用 `YYYYMMDD/NNNNNN/NNN` 格式。例如，`20171230/000000001/001` 表示该数据于 2017 年 12 月 30 日首次注册到元数据注册表中，是数据类型 1 的数据源 1。这是一个关键要求。

-   简短描述（不超过 100 个字符）：国家代码与国家名称（国家代码—ISO 3166）

-   详细描述（尽可能完整）：VKHC 用作国家条目标准的国家代码与国家名称

-   外部数据源联系信息：来自 [`www.iso.org/iso-3166-country-codes.html`](http://www.iso.org/iso-3166-country-codes.html) 的 ISO 3166-1:2013 代码列表

-   预期更新频率：不定期（即无固定频率，也称为临时）。其他选项包括准实时、每 5 秒、每分钟、每小时、每日、每周、每月或每年。

-   内部业务目的：验证国家代码和名称。

我发现在数据处理中，如果数据源目录是最新的，那么处理过程的其余部分就会轻松无压力。

#### 业务术语表

业务术语表映射数据源字段，并将其分类到相应的业务线中。对于任何优秀的数据湖来说，此术语表都是必备之物。

创建一个包含以下信息的数据映射注册表：

-   唯一数据目录编号：我通常使用 `YYYYMMDD/NNNNNN/NNN` 格式。

-   唯一数据映射编号：我通常使用 `NNNNNNN/NNNNNNNNN` 格式。例如，`0000001/000000001` 表示将外部字段 1 映射到内部字段 1。

-   外部数据源字段名称：列出原始数据源中的字段名称。

-   外部数据源字段类型：记录加载数据湖时该字段的完整数据类型集合。

-   内部数据源字段名称：记录从数据湖加载后要使用的每个内部数据字段名称。

-   内部数据源字段类型：记录加载后在内部使用的字段类型的完整集合。

-   数据映射最后验证时间戳：我通常使用支持精确到千分之一秒的 `YYYYMMDD-HHMMSS-SSS` 格式。

**注意**

外部和内部映射之间可能存在多对多的关系。请将映射控制在合理的整合程度。但是，要在解决映射所花费的时间与由此带来的效率提升之间取得平衡。

业务术语表记录了准备好进行检索处理以加载数据的数据源。在我处理的多个客户系统中，我必须使用机器学习成功地执行了这种分类过程。

这仅需将分类机器人指向数据湖，就能以这种方式发现任何新的或更改过的元数据。一旦收集到新识别的元数据，人工操作员只需处理例外情况即可。

我有几个机器人可以执行这些发现任务，但本书不会花更多篇幅介绍我是如何实现的，因为通常情况下，每个系统都需要为每个客户的生态系统进行定制构建。我建议你投入时间自动化你的数据湖，因为这能让你有更多时间执行数据科学中激动人心的数据分析部分。

#### 分析模型使用

在各个分析模型中标记的数据定义了需要加载的数据概况，并指导数据科学家需要进行哪些额外的处理。默认情况下，我会对数据湖中的每个数据集执行以下数据分析模型。

让我们从加载一个样本数据集开始。

**注意**

我将使用一个样本数据集来展示此过程的每个步骤。因此，你将需要使用 `R` 或 `Python` 来执行一些数据处理。

**免责声明**

在本书中，当我向你介绍几个 `R` 和 `Python` 数据处理包时，我只会提供这些工具的基本介绍。我建议你自行研究这些包的更多细节，以提高你的整体理解和能力。

对于 `R`，请执行以下命令：
```

启动你的 RStudio 编辑器。

**注意**

在本章的其余部分，我将在 Windows 生态系统下使用 `setwd("C:/VKHCG")`，在 Linux 生态系统下使用 `setwd("/home/<youruser>/VKHCG")`。

执行以下命令以导入数据加载包：

```r
library(readr)
```

**注意**

更多详情请参见 [`https://cran.r-project.org/web/packages/readr/index.html`](https://cran.r-project.org/web/packages/readr/index.html)。

这应该会加载一个有趣的 R 数据加载包。那么，让我们加载 Vermeulen 公司的示例数据。

让我们加载一个名为 `IP_DATA_ALL.csv` 的表。

```r
Base <- getwd()
FileName <- paste0(Base, '/01-Vermeulen/00-RawData/IP_DATA_ALL.csv')
IP_DATA_ALL <- read_csv(FileName)
```

你应该已经成功加载了一个包含八列的数据集。

使用 `spec(IP_DATA_ALL)` 命令应产生以下结果：

```r
cols(
  ID = col_integer(),
  Country = col_character(),
  'Place Name' = col_character(),
  'Post Code' = col_character(),
  Latitude = col_double(),
  Longitude = col_double(),
  'First IP Number' = col_integer(),
  'Last IP Number' = col_integer()
)
```

这告诉你包含以下八个列：
- `ID`（整数类型）
- `Place name`（字符类型）
- `Post code`（字符类型）
- `Latitude`（数值双精度类型）
- `Longitude`（数值双精度类型）
- `First IP number`（整数类型）
- `Last IP number`（整数类型）

现在你可以通过 `View(IP_DATA_ALL)` 查看数据。你将看到一个包含数据的数据网格出现。

## 数据字段名称验证

我用它来以简单的方式验证和核实检索处理中的数据字段名称。要添加一个额外的库，你需要运行以下命令：

**警告**

通过执行 `install.packages("tibble")` 确保你拥有最新的包。因此，如果我的某个校对人员报告说 `set_tidy_names()` 命令不存在，我会说明我使用的是 R 3.4.1 和 `tibble` 1.3.4 版本。

```r
library(tibble)
```

（参见 [`https://cran.r-project.org/web/packages/tibble/`](https://cran.r-project.org/web/packages/tibble/) 。）

```r
set_tidy_names(IP_DATA_ALL, syntactic = TRUE, quiet = FALSE)
```

你会发现某些字段名称不易使用。

```
New names:
Place Name -> Place.Name
Post Code -> Post.Code
First IP Number -> First.IP.Number
Last IP Number -> Last.IP.Number
```

这告诉你四个字段名称无效，并建议了有效的、新的字段名称。

**提示**

我总是使用现有的可用工具，这可以节省你验证它们是否按预期工作的时间。优秀的数据科学家会向他人学习。

你可以通过执行以下命令来修复任何检测到的无效列名：

```r
IP_DATA_ALL_FIX <- set_tidy_names(IP_DATA_ALL, syntactic = TRUE, quiet = TRUE)
```

通过使用命令 `View(IP_DATA_ALL_FIX)`，你可以检查列是否已修复。

干得好！你刚刚修复了数据字段名称。那么，现在让我们保存你的进度。

```r
Base <- getwd()
FileDir <- paste0(Base, '/01-Vermeulen/01-Retrieve/01-EDS')
dir.create(FileDir)
FileDir <- paste0(Base, '/01-Vermeulen/01-Retrieve/01-EDS/01-R')
dir.create(FileDir)
FileName <- paste0(FileDir, '/IP_DATA_ALL_FIX.csv')
write.csv(IP_DATA_ALL_FIX, FileName)
```

你现在应该有一个名为 `IP_DATA_ALL_FIX.csv` 的文件。

### 每条数据条目的唯一标识符

在系统内分配一个与给定文件名无关的唯一标识符。这确保了系统能够处理来自不同路径的不同文件，并以有效的方式跟踪所有数据条目。然后，为检索到的文件中的每条记录或每个数据元素分配一个唯一标识符。

> **提示**  
> 我发现，额外花时间执行此任务可以防止重大的数据引用问题。在规模化情况下，将相似甚至相同的数据多次加载到数据湖中的概率很高。

现在，检查你的样本数据。对于 R 语言，请参见下文。要添加唯一标识符，请运行以下命令：

```r
IP_DATA_ALL_with_ID <- rowid_to_column(IP_DATA_ALL_FIX, var = "RowID")
```

检查你是否成功添加了唯一标识符，如下所示：

```r
View(IP_DATA_ALL_with_ID)
```

这将显示每条记录都有一个名为“Row ID”的唯一 ID。

那么，让我们保存进度。

```r
Base <- getwd()
FileName <- paste0(FileDir, '/IP_DATA_ALL_with_ID.csv')
write.csv(IP_DATA_ALL_with_ID, FileName)
```

你现在应该有一个名为 `IP_DATA_ALL_with_ID.csv` 的文件。

### 每个数据列的数据类型

确定每列的最佳数据类型，以协助你完成业务术语表，并确保你记录了正确的导入处理规则。使用以下 R 命令：

```r
sapply(IP_DATA_ALL_with_ID, typeof)
```

你应该会看到已加载数据的完整数据类型分析。

| RowId | ID | Country | Place.Name | Post.Code | Latitude | Longitude | First.IP.Number | Last.IP.Number |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| integer | integer | character | character | character | double | double | integer | integer |

> **警告**  
> 留意那些看似数值型但实际上应为文本列的数据类型，例如账号或识别号。

### 每列的直方图

我通常会为每一列生成直方图，以确定数据值的分布情况。在本章的实践部分，我将针对样本数据集生成直方图，以演示如何执行此操作。

现在我们将数据集转换为数据表，这将帮助我们以更增强的方式研究数据。我将在 R 中向你展示如何查找国家数据的直方图，如下所示：

| 排名前两位的国家代码 | 频数 |
| --- | --- |
| US | 512714 |
| GB | 127069 |

```r
library(data.table)
hist_country <- data.table(Country = unique(IP_DATA_ALL_with_ID[is.na(IP_DATA_ALL_with_ID['Country']) == 0, ]$Country))
setorder(hist_country, 'Country')
hist_country_with_id <- rowid_to_column(hist_country, var = "RowIDCountry")
View(hist_country_with_id)
IP_DATA_COUNTRY_FREQ <- data.table(with(IP_DATA_ALL_with_ID, table(Country)))
View(IP_DATA_COUNTRY_FREQ)
```

**业务洞察：**
- 数据量最大的两个子集分别来自美国和英国。
- 美国的数据量是英国的四倍多一点。

这些信息对于设计已加载数据的并行处理非常有用。

在并行处理期间，由于并行处理的特定拆分标准，数据倾斜可能导致不必要的等待时间或处理器过载。在上述案例中，如果你按每个国家代码分配一个处理器来分散数据，由于数据量差异，美国和英国的处理时间会比其他国家的代码更长。即使是英国和美国之间的数据量不匹配，也会导致更多的处理倾斜。

> ### 谨慎
>
> 并行处理是数据科学的一项基本要求，了解你的数据模型和/或算法在处理非均匀数据分布时的行为非常重要。有时，不采用并行处理可能更好，因为拆分过程所花费的时间可能超过它给整个流程带来的节省。

那么，让我们保存进度。

```
Base=getwd()
FileName=paste0(FileDir,'/IP_DATA_COUNTRY_FREQ.csv')
IP_DATA_COUNTRY_FREQ.to_csv(FileName)
```

你现在应该有一个名为`IP_DATA_COUNTRY_FREQ.csv`的文件。

> ### 提示
>
> 当你开始了解数据湖的分析特征时，你将开始制定如何处理数据的计划。我通常会在数据湖的数据概况中保存一些关键笔记。
>
> 对数据湖进行概况分析通常是一种结构化的方法。我和我的团队会为每个数据源保存一份简单的速查表，记录其数据概况。我们通过 `GitHub` 源代码管理共享此表，以确保任何新发现都能传递给所有相关人员。

让我们看看你可能想要研究的一些其他关键特征。

直方图解释了整个数据集在值集合上的分布情况。我将在`R`中向你展示如何查找纬度数据字段的直方图。

| 排名前三的纬度 | 频数 |
| --- | --- |
| 35.6427 | 4838 |
| 51.5092 | 4442 |
| 48.8628 | 3230 |

```
hist_latitude =data.table(Latitude=unique(IP_DATA_ALL_with_ID [is.na(IP_DATA_ALL_with_ID ['Latitude']) == 0, ]$Latitude))
setkeyv(hist_latitude, 'Latitude')
setorder(hist_latitude)
hist_latitude_with_id=rowid_to_column(hist_latitude, var = "RowID")
View(hist_latitude_with_id)
IP_DATA_Latitude_FREQ=data.table(with(IP_DATA_ALL_with_ID, table(Latitude)))
View(IP_DATA_Latitude_FREQ)
```

### 业务洞察

- 数据量最大的两个纬度是`35.6427`和`51.5092`。

- 排名前两位的纬度之间的分布似乎几乎相等。

那么，让我们保存进度。

```
Base=getwd()
FileName=paste0(FileDir,'/IP_DATA_Latitude_FREQ.csv')
IP_DATA_Latitude_FREQ.to_csv(FileName)
```

你现在应该有一个名为`IP_DATA_Latitude_FREQ.csv`的文件。

我将在`R`中向你展示如何查找经度数据的直方图。

| 排名前三的经度 | 频数 |
| --- | --- |
| 139.7677 | 4837 |
| -0.0955 | 4436 |
| 2.3292 | 3230 |

```
hist_longitude =data.table(Longitude=unique(IP_DATA_ALL_with_ID [is.na(IP_DATA_ALL_with_ID ['Longitude']) == 0, ]$Longitude))
setkeyv(hist_longitude, 'Longitude')
setorder(hist_longitude,'Longitude')
hist_longitude_with_id=rowid_to_column(hist_longitude, var = "RowID")
View(hist_longitude_with_id)
IP_DATA_Longitude_FREQ=data.table(with(IP_DATA_ALL_with_ID, table(Longitude)))
View(IP_DATA_Longitude_FREQ)
```

那么，让我们保存进度。

```
Base=getwd()
FileName=paste0(FileDir,'/IP_DATA_Longitude_FREQ.csv')
IP_DATA_Longitude_FREQ.to_csv(FileName)
```

你现在应该有一个名为`IP_DATA_Longitude_FREQ.csv`的文件。

# 最小值

确定特定列中的最小值。

## 使用 `sapply()` 函数

在 R 中，使用以下代码。我建议你检查一下国家（`country`）数据。

```R
min(hist_country$Country)
```

应返回 `AD`。`AD` 是安道尔（一个位于比利牛斯山脉、介于法国和西班牙之间的独立公国）的国家代码。

**提示**
我将使用 R 中一个常用的处理函数 `sapply()`。如果你之前未使用过它，建议你查阅 [`www.r-bloggers.com/using-apply-sapply-lapply-in-r/`](http://www.r-bloggers.com/using-apply-sapply-lapply-in-r/)。简而言之，它是将一个函数应用于一个数据集。请好好阅读相关材料，因为你在数据科学中会频繁用到它。

现在，我将把 `min` 函数应用于 `hist_country` 数据集的 `Country` 字段。你也可以使用

```R
sapply(hist_country[,'Country'], min, na.rm=TRUE)
```

应返回 `AD`。

## 纬度数据分析

接下来，我建议你检查纬度（`latitude`）数据。

在 R 中执行

```R
sapply(hist_latitude_with_id[,'Latitude'], min, na.rm=TRUE)
```

结果是 `-54.2767`。这说明了什么？

**事实：** 南半球的纬度范围是从 -90 到 0。因此，如果你的数据中没有比 `-54.2767` 更靠南的纬度，你就可以优化检索流程。

在 R 中计算以下内容：

```R
round(1-(-54.2767/-90),4)*100
```

结果是 `39.69%`。利用这一发现，可以在处理过程中实现 39% 的最小节省。

## 经度数据分析

接下来，我建议你检查经度（`longitude`）数据。

在 R 中执行

```R
sapply(hist_longitude_with_id[,'Longitude'], min, na.rm=TRUE)
```

结果是 `-176.5`。这说明了什么？

**事实：** 西半球的经度范围是从 -180 到 0。因此，如果你的数据中没有比 `-176.5` 更靠西的经度，你就可以优化检索流程。

在 R 中计算以下内容：

```R
round(1-(-176.5/-180),4)*100
```

结果是 `1.94%`。利用这一发现，可以在处理过程中实现约 1% 的小幅节省。

## 商业洞察

*   关于安道尔的洞察很有趣，但不会影响数据检索的处理方案。

*   南半球的纬度范围是从 -90 到 0，确认的最小纬度大于 -55。了解这一点后，你无需再处理南半球约 +/-39% 的范围数据。这是效率上的重大提升。

*   西半球的经度范围是从 -180 到 0，确认的最小经度大于 -177。因此，你无法获得类似的效率提升，因为最多只能不加载东半球总范围的不到 2%。

你发现，当你计划用 180 个进程加载各个纬度带时，你已经消除了其中 35 个进程的需求。你还发现，如果按经度带加载，改进效果并不显著。

# 最大值

确定特定列中的最大值。

在 R 中，使用

```R
max(hist_country$Country)
```

应返回 `ZW`。你也可以使用

```R
sapply(hist_country[,'Country'], max, na.rm=TRUE)
```

应返回 `ZW`。我建议你接下来检查纬度数据。

在 R 中执行

```R
sapply(hist_latitude_with_id[,'Latitude'], max, na.rm=TRUE)
```

结果是 `78.2167`。这说明了什么？

**事实：** 北半球的纬度范围是从 0 到 90。因此，如果你的数据中没有比 78.2167 更靠北的纬度，你就可以优化检索流程。

在 R 中计算以下内容：

```R
round(1-(78.2167/90),4)*100
```

结果是 `13.09%`。利用这一发现，可以在处理过程中实现 13%的最小节省。

在 R 中执行

```R
sapply(hist_longitude_with_id[,'Longitude'], max, na.rm=TRUE)
```

结果是 `179.2167`。这说明了什么？

**事实：** 东半球的经度范围是从 0 到 180。因此，如果你的数据中没有比 179.2167 更靠东的经度，你就可以优化检索流程。

在 R 中计算以下内容：

```R
round(1-(179.2167/180),4)*100
```

结果是 `0.44%`。利用这一发现，可以在处理过程中实现约 0.4%的小幅节省。

# 平均值

如果列是数值类型，确定特定列中的平均值。

让我们检查纬度。在 R 中，使用：
```R
sapply(hist_latitude_with_id[,'Latitude'], mean, na.rm=TRUE)
```
结果是 `38.23803`。

让我们检查经度。在 R 中，使用：
```R
sapply(hist_longitude_with_id[,'Longitude'], mean, na.rm=TRUE)
```
结果是 `-13.87398`。

# 中位数

确定将特定列的数据集分成两半的值。

在 R 中，对纬度列使用以下命令：

```R
sapply(hist_latitude_with_id[,'Latitude'], median, na.rm=TRUE)
```

结果是`43.2126`。

在 R 中，对经度列使用以下命令：

```R
sapply(hist_longitude_with_id[,'Longitude'], median, na.rm=TRUE)
```

结果是`43.2126`。

# 众数
确定特定列中出现频率最高的值。
在R中，对`country`列使用以下命令：
```R
IP_DATA_COUNTRY_FREQ=data.table(with(IP_DATA_ALL_with_ID, table(Country)))
setorder(IP_DATA_COUNTRY_FREQ,-N)
IP_DATA_COUNTRY_FREQ[1,'Country']
```
结果是`US`。
在R中，对纬度列使用以下命令：
```R
IP_DATA_Latitude_FREQ=data.table(with(IP_DATA_ALL_with_ID, table(Latitude)))
setorder(IP_DATA_Latitude_FREQ,-N)
IP_DATA_Latitude_FREQ[1,'Latitude']
```
结果是`35.6427`。
在R中，对经度列使用以下命令：
```R
IP_DATA_Longitude_FREQ=data.table(with(IP_DATA_ALL_with_ID, table(Longitude)))
setorder(IP_DATA_Longitude_FREQ,-N)
IP_DATA_Longitude_FREQ[1,'Longitude']
```
结果是`139.7677`。

# 极差

对于数值类型，通过将最大值减去最小值来确定值的范围。
在 R 中，对纬度列使用以下命令：
```R
sapply(hist_latitude_with_id[,'Latitude'], range, na.rm=TRUE)
```
纬度的范围是 -54.2767 到 78.2167。
在 R 中，对经度列使用以下命令：
```R
sapply(hist_longitude_with_id[,'Longitude'], range, na.rm=TRUE)
```
经度的范围是 -176.5000 到 179.2167。
在 R 中，对 `country` 列使用以下命令：
```R
sapply(hist_country_with_id[,'Country'], range, na.rm=TRUE)
Country Range is: "AD" to "ZW"
```

# 四分位数

四分位数是将数据集分成四等份的基础值。只需对数据列进行排序，然后将其分成四个大小相等的组即可。

在 R 中，对纬度列使用以下命令：

```R
sapply(hist_latitude_with_id[,'Latitude'], quantile, na.rm=TRUE)
```

结果如下：

|   | 纬度 (Latitude) |
|---|----------|
| 0% | -54.2767 |
| 25% | 36.4151 |
| 50% | 43.2126 |
| 75% | 48.6219 |
| 100% | 78.2167 |

**知识检查点**

你能对经度列执行同样的操作吗？你还能获得其他洞察吗？你会如何完成这个任务？

以下是我的尝试：

在 R 中，对经度列使用以下命令：

```R
sapply(hist_longitude_with_id[,'Longitude'], quantile, na.rm=TRUE)
```

结果如下：

|   | 经度 (Longitude) |
|---|-----------|
| 0% | -176.50000 |
| 25% | -80.20185 |
| 50% | 1.81810 |
| 75% | 12.69480 |
| 100% | 179.21670 |

### 标准差

在 R 中，对纬度列使用以下命令：

```R
sapply(hist_latitude_with_id[,'Latitude'], sd, na.rm=TRUE)
```

结果是 `19.86071`。

在 R 中，对经度列使用以下命令：

```R
sapply(hist_longitude_with_id[,'Longitude'], sd, na.rm=TRUE)
```

结果是 `68.981`。

### 偏度

`Skewness`（偏度）描述了列中数据分布的形态或轮廓。

**请记住：** 这是一种为你进行并行处理提供轮廓的特征。

在 R 中，对纬度列使用以下命令：

**警告：** 你现在将安装一个新的包：`install.packages('e1071')`。这是一组名为 `e1071` 的有用函数，由维也纳科技大学统计系概率论研究小组编写。全世界有数千个 R 研究小组，在数据处理方面取得了极其有用的进展。我通过添加他们已有的、经过验证的处理能力，来丰富我们自己的处理能力。

```R
library(e1071)
skewness(hist_latitude_with_id$Latitude, na.rm = FALSE, type = 2)
```

结果是 `-2.504929`。

**负偏度：** 分布的质量集中在右侧，这种分布被称为左偏。该结果只是数据如何偏斜的一个指标。通过将其与直方图结合，你可以深入了解数据的分布情况。

**示例：** 如果你正在查看按升序（从右到左）排列的纬度分布，并发现偏度为负，那么你很可能会在左侧找到较少的位点，而更可能在右侧找到更多的位点。这只是一个快速的估计。尺寸直方图将为你提供每个尺寸的精确计数。

**知识点检查：** 经度也是如此吗？使用 `View(hist_longitude_with_id)`，你能预测会发现什么吗？你能自己猜测或计算偏度吗？

在 R 中，对经度列使用以下命令：

```R
skewness(hist_longitude_with_id$Longitude,na.rm = FALSE, type = 2)
```

结果是 `0.4852374`。

**正偏度：** 分布的质量集中在左侧，这种分布被称为右偏。

**警告：** 数据缺失或未知值是数据科学中常见但不可避免的事实。

**示例：** 如果你只拥有美国和德国客户的数据，这是否意味着你在英国没有客户？如果你的数据谱系推断你拥有数据，但在数据湖中只是没有 100% 填满呢？你会如何处理？

### 缺失或未知值

识别数据集中是否存在缺失或未知值。

在 R 中，对国家列使用以下命令：

```R
missing_country=data.table(Country=unique(IP_DATA_ALL_with_ID[is.na(IP_DATA_ALL_with_ID ['Country']) == 1, ]))
View(missing_country)
```

### 数据模式

多年来，我一直使用以下流程来确定数据值本身的模式。这是我的标准版本：

将所有字母值替换为大写字母 A，所有数字替换为大写字母 N，并将所有空格替换为小写字母 b，将所有其他未知字符替换为小写字母 u。因此，“Good Book 101” 变成 “AAAAbAAAAbNNNu”。

这种模式创建有助于在第 10 章 中设计任何特定的评估规则，因为可以通过处理特定不需要的模式来删除或修复无效模式。这种数据的模式视图是一种快速识别常见模式或确定标准布局的方法。

**示例 1：** 如果对于一个日期列，你有两种模式——`NNNNuNNuNN` 和 `uuNNuNNuNN`，就很容易看出 `uuNNuNNuNN` 模式存在问题。在正则表达式中创建一个质量匹配网格也很容易：`regexpr()`。

参见 [`https://cran.r-project.org/web/packages/stringr/vignettes/regular-expressions.html`](https://cran.r-project.org/web/packages/stringr/vignettes/regular-expressions.html)。

**示例 2：** 它还有助于确定如何以并行方式加载数据，因为每种模式都可以在独立的检索过程中加载。如果发现相同的两种模式 `NNNNuNNuNN` 和 `uuNNuNNuNN`，你可以直接将 `NNNNuNNuNN` 发送去转换为日期，而 `uuNNuNNuNN` 则经过一个质量改进过程，然后在其符合要求后重新路由回与 `NNNNuNNuNN` 相同的队列。这是使用模式来分离通用标准和结构的常见做法。

那么，让我们看看如何实现这一点。让我们为第一列创建模式。我建议从国家列开始。

在 R 中，使用以下命令：

```R
library(readr)
library(data.table)
Base=getwd()
FileName=paste0(Base,'/01-Vermeulen/00-RawData/IP_DATA_ALL.csv')
IP_DATA_ALL <- read_csv(FileName)
hist_country=data.table(Country=unique(IP_DATA_ALL$Country))
pattern_country=data.table(Country=hist_country$Country, PatternCountry=hist_country$Country)
oldchar=c(letters,LETTERS)
newchar=replicate(length(oldchar),"A")
for (r in seq(nrow(pattern_country))){
s=pattern_country[r,]$PatternCountry;
for (c in seq(length(oldchar))){
s=chartr(oldchar[c],newchar[c],s)
};
for (n in seq(0,9,1)){
s=chartr(as.character(n),"N",s)
};
s=chartr(" ","b",s)
s=chartr(".","u",s)
pattern_country[r,]$PatternCountry=s;
};
View(pattern_country)
```

**注意：** 生成这些模式的过程可以转移到自定义函数甚至包中。如果你对这个过程感兴趣，关于函数，请参见 [`www.r-bloggers.com/how-to-write-and-debug-an-r-function/`](http://www.r-bloggers.com/how-to-write-and-debug-an-r-function/)。关于包，请参见 [`https://cran.r-project.org/doc/manuals/R-exts.html`](https://cran.r-project.org/doc/manuals/R-exts.html)。

那么，我们来保存一下进度。

```R
Base=getwd()
FileName=paste0(FileDir,'/pattern_country.csv')
pattern_country.to_csv(FileName)
```

你现在应该有一个名为 `pattern_country.csv` 的文件。

让我们为另一列创建模式。我建议从纬度列开始。

在 R 中，使用以下命令：

```
library(readr)
library(data.table)
Base=getwd()
FileName=paste0(Base,'/01-Vermeulen/00-RawData/IP_DATA_ALL.csv')
IP_DATA_ALL <- read_csv(FileName)
hist_latitude=data.table(Latitude=unique(IP_DATA_ALL$Latitude))
pattern_latitude=data.table(latitude=hist_latitude$Latitude, Patternlatitude=as.character(hist_latitude$Latitude))
oldchar=c(letters,LETTERS)
newchar=replicate(length(oldchar),"A")
for (r in seq(nrow(pattern_latitude))){
s=pattern_latitude[r,]$Patternlatitude;
for (c in seq(length(oldchar))){
s=chartr(oldchar[c],newchar[c],s)
};
for (n in seq(0,9,1)){
s=chartr(as.character(n),"N",s)
};
s=chartr(" ","b",s)
s=chartr("+","u",s)
s=chartr("-","u",s)
s=chartr(".","u",s)
pattern_latitude[r,]$Patternlatitude=s;
};
setorder(pattern_latitude,latitude)
View(pattern_latitude[1:3])
```

结果如下：

| | latitude | Patternlatitude |
|--- |--- |--- |
| 1 | -54.2767 | uNNuNNNN |
| 2 | -54.1561 | uNNuNNNN |
| 3 | -51.7 | uNNuN |

那么，我们来保存一下进度。

```
Base=getwd()
FileName=paste0(FileDir,'/pattern_country_asc.csv')
pattern_country.to_csv(FileName)
```

你现在应该有一个名为 `pattern_country_asc.csv` 的文件。

```
setorder(pattern_latitude,-latitude)
View(pattern_latitude[1:3])
```

结果如下：

| | latitude | Patternlatitude |
|--- |--- |--- |
| 1 | 78.2167 | NNuNNNN |
| 2 | 77.3025 | NNuNNNN |
| 3 | 72.7 | NNuN |

那么，我们来保存一下进度。

```
Base=getwd()
FileName=paste0(FileDir,'/pattern_country_desc.csv')
pattern_country.to_csv(FileName)
```

你现在应该有一个名为 `pattern_country_desc.csv` 的文件。

**注意：** 此时，你可以保存所有工作，因为我接下来将讨论各种非编程信息。

### 数据质量

数据点更多并不意味着数据质量就不那么重要了。如果处理不当，数据质量问题可能导致整个数据集失效。

**警告：** 质量差的数据会破坏即使是设计得最好的数据科学模型。因此，要花时间确保在流程早期了解任何数据问题。

在第 8 章中，我将详细讨论如何解决任何数据质量问题。

### 审计与版本管理

到目前为止，我只是简单允许你以一次性使用的方式保存和处理数据。这并不是执行数据科学的正确方法。你必须始终报告以下内容：

- 谁使用了该流程？

- 何时使用？

- 使用了哪个版本的代码？

关于处理数据的流程和审计要求，我建议你参考第 6 章，版本管理部分。在此，我想讨论成功的数据科学流程的另一部分：你和你的团队。

## 培训培训师模式

为了防止数据沼泽，培训你的团队至关重要。数据科学是一项团队协作的工作。

人员、流程和技术是确保数据得到妥善管理和保护的三大基石。你对自己的人员负有责任；请分享你从本书中获取的知识。我教给你的流程，你需要教给他们。仅凭你一人，无法取得成功。

技术需要你投入时间去充分理解。我们才刚刚迎来数据工程和数据科学领域重大发展的曙光。请记住：这个流程很大一部分在于确保业务用户和数据科学家理解“从小处着手”的必要性，心中要有具体问题，并意识到需要处理所有数据才能取得成功。

## 理解数据湖的业务动态

现在你已经掌握了在数据科学流程中处理“检索”步骤的基本技巧。接下来，让我们处理我提供的示例。现在，你可以对照示例来衡量自己的能力。

### R 语言检索解决方案

首先，我将引导你完成一个基于 R 语言的“检索”超步骤解决方案。

#### Vermeulen PLC 公司

我将引导你了解第一家公司的操作。启动你的 RStudio 编辑器。

> **注意**
>
> 在本章的剩余部分，对于 Windows 系统，我将使用 `setwd("C:/VKHCG")`；对于 Linux 系统，使用 `setwd("/home/<youruser>/VKHCG")`。加载 `IP_DATA_ALL`。

现在，通过 RStudio 执行以下命令：

```
getwd()
```

测试你的工作目录是否正确。你应该会看到你设置的目录。

#### 加载 IP_DATA_ALL

该数据集包含世界上所有的 IP 地址分配信息。当你在线上与客户互动时，它将帮助你定位客户。

> **警告**
>
> 我使用的是默认目录引用：对于 Windows，为 `C:/VKHCG`；对于 Linux，为 `/home/<youruser>/VKHCG`。
>
> 如果你的代码和数据都不在这些位置，那么你将需要对本书中的每个示例进行代码修改。

让我们开始数据处理的第一次加载。创建一个新的 R 脚本文件，并将其保存为 `Retrieve-IP_DATA_ALL.r`，放在目录 `C:\VKHCG\01-Vermeulen\01-Retrieve` 中。现在，开始将以下代码复制到该文件中：
```

保存并执行该脚本。

如果你正确完成了代码，你应该会看到如下四个结果：

| 行 ID | 国家 | 地名 | 邮政编码 | 纬度 | 经度 |
| --- | --- | --- | --- | --- | --- |
| 1 | BW | 哈博罗内 | NA | -24.6464 | 25.9119 |
| 2 | BW | 哈博罗内 | NA | -24.6464 | 25.9119 |
| 3 | BW | 哈博罗内 | NA | -24.6464 | 25.9119 |
| 4 | BW | 哈博罗内 | NA | -24.6464 | 25.9119 |

1. 在目录`C:\VKHCG\01-Vermeulen\01-Retrieve`下，你应该有一个名为`Retrieve-IP_DATA_ALL.r`的文件。
2. 在目录`C:\VKHCG\01-Vermeulen\01-Retrieve\01-EDS\01-R\log\Run0001`下，你应该有三个文件：`debug.Log`、`error.Log`和`info.Log`。如果你在文本编辑器中打开`info.Log`，你应该看到以下内容：

```r
rm(list=ls()) #will remove ALL objects
if (Sys.info()['sysname'] == "Windows")
{
BaseSet = "C:/VKHCG"
setwd(BaseSet)
} else {
BaseSet = paste0("/home/",  Sys.info()['user'],  "/VKHCG")
setwd(BaseSet)
}
Base=getwd() FileDir= paste0(Base,'/01-Vermeulen/01-Retrieve/01-EDS/01-R')
dir.create(FileDir)
FileDirLog=paste0(FileDir,'/log')
dir.create(FileDirLog)
FileDirRun=paste0(FileDirLog,'/Run0001')
dir.create(FileDirRun)
StartTime=Sys.time()
# Set up logging
debugLog=paste0(FileDirRun,'/debug.Log')
infoLog=paste0(FileDirRun,'/info.Log')
errorLog=paste0(FileDirRun,'/error.Log')
write(paste0('Start Debug Log File ',
format(StartTime, "%Y/%d/%m %H:%M:%S")),
file=debugLog,append = FALSE)
write(paste0('Start Information Log File ',
format(StartTime, "%Y/%d/%m %H:%M:%S")),
file=infoLog,append = FALSE)
write(paste0('Start Error Log File ',
format(StartTime, "%Y/%d/%m %H:%M:%S")),
file=errorLog,append = FALSE)
UserName='Practical Data Scientist'
write(paste0(UserName,' Load library: ', 'readr'),
infoLog,append = TRUE)
library(readr)
write(paste0(UserName,' Load library: ', 'data.table'),
infoLog,append = TRUE)
library(data.table)
write(paste0(UserName,' Load library: ', 'tibble'),
infoLog,append = TRUE)
library(tibble)
FileName=paste0(Base,'/01-Vermeulen/00-RawData/IP_DATA_ALL.csv')
write(paste0(UserName,' Retrieve data file: ', FileName),
file=infoLog,append = TRUE)
IP_DATA_ALL <- read_csv(FileName,
col_types = cols(
Country = col_character(),
ID = col_skip(),
'First IP Number' = col_skip(),
'Last IP Number' = col_skip(),
Latitude = col_double(),
Longitude = col_double(),
'Place Name' = col_character(),
'Post Code' = col_character()
), na = "empty")
IP_DATA_ALL_FIX=set_tidy_names(IP_DATA_ALL, syntactic = TRUE, quiet = TRUE)
IP_DATA_ALL_with_ID=rowid_to_column(IP_DATA_ALL_FIX, var = "RowID")
FileNameOut=paste0(FileDir,'/Retrieve_IP_DATA.csv')
fwrite(IP_DATA_ALL_with_ID, FileNameOut)
write(paste0(UserName,' Stores Retrieve data file: ', FileNameOut),
file=infoLog,append = TRUE)
StopTime=Sys.time()
write(paste0('Stop Debug Log File ',
format(StopTime, "%Y/%d/%m %H:%M:%S")),
file=debugLog,append = TRUE)
write(paste0('Stop Information Log File ',
format(StopTime, "%Y/%d/%m %H:%M:%S")),
file=infoLog,append = TRUE)
write(paste0('Stop Error Log File ',
format(StopTime, "%Y/%d/%m %H:%M:%S")),
file=errorLog,append = TRUE)
View(IP_DATA_ALL_with_ID)
```



```
Start Information Log File 2017/05/08 03:35:54
Practical Data Scientist Load library: readr
Practical Data Scientist Load library: data.table
Practical Data Scientist Load library: tibble
Practical Data Scientist Retrieve data file: C:/VKHCG/01-Vermeulen/00-RawData/IP_DATA_ALL.csv
Practical Data Scientist Stores Retrieve data file: C:/VKHCG/01-Vermeulen/01-Retrieve/01-EDS/01-R/Retrieve_IP_DATA.csv
Stop Information Log File 2017/05/08 03:35:57
```

在目录`C:\VKHCG\01-Vermeulen\01-Retrieve\01-EDS\01-R`下，你应该有一个名为`Retrieve_IP_DATA.csv`的文件。

你还会看到你加载的文件显示如下：

### 实现了什么？

你已经：

*   将`IP_DATA_ALL.csv`加载到 R 中

*   使用`col_skip()`删除了三列：`ID`、`First IP Number`和`Last IP Number`

*   使用`col_character()`将三列的数据类型设置为字符串或字符：`Country`、`Place Name`和`Post Code`

*   将所有缺失数据转换为`empty`

*   将`Place Name`和`Post Code`更改为`Place.Name`和`Post.Code`

*   学会了如何在程序中记录进度

哇，你已经迈出了成为实践数据科学家的第一步。现在，我建议你关闭`Retrieve-IP_DATA_ALL.r`，我们继续加载下一个文件。

## 加载 IP_DATA_C_VKHCG

这是一个特定的数据加载，因为它只包含 VKHCG 用于其数据系统的一个 C 类地址。创建一个新的 R 脚本文件，并将其保存为目录`C:\VKHCG\01-Vermeulen\01-Retrieve`下的`Retrieve-IP_DATA_ALL.r`。

现在，你可以开始将以下代码复制到脚本文件中：



```r
###########################################
rm(list=ls()) #will remove ALL objects
###########################################
###########################################
if (Sys.info()['sysname'] == "Windows")
{
BaseSet = "C:/VKHCG"
setwd(BaseSet)
} else {
BaseSet = paste0("/home/",  Sys.info()['user'],  "/VKHCG")
setwd(BaseSet)
}
###########################################
Base=getwd()
FileDir= paste0(Base,'/01-Vermeulen/01-Retrieve/01-EDS/01-R')
dir.create(FileDir)
FileDirLog=paste0(FileDir,'/log')
dir.create(FileDirLog)
FileDirRun=paste0(FileDirLog,'/Run0002')
dir.create(FileDirRun)
###########################################
StartTime=Sys.time()
###########################################
# Set up logging
###########################################
debugLog=paste0(FileDirRun,'/debug.Log')
infoLog=paste0(FileDirRun,'/info.Log')
errorLog=paste0(FileDirRun,'/error.Log')
###########################################
write(paste0('Start Debug Log File ',
format(StartTime, "%Y/%d/%m %H:%M:%S")),
file=debugLog,append = FALSE)
write(paste0('Start Information Log File ',
format(StartTime, "%Y/%d/%m %H:%M:%S")),
file=infoLog,append = FALSE)
write(paste0('Start Error Log File ',
format(StartTime, "%Y/%d/%m %H:%M:%S")),
file=errorLog,append = FALSE)
###########################################
UserName='Practical Data Scientist'
###########################################
write(paste0(UserName,' Load library: ', 'readr'),
infoLog,append = TRUE)
library(readr)
write(paste0(UserName,' Load library: ', 'data.table'),
infoLog,append = TRUE)
library(data.table)
write(paste0(UserName,' Load library: ', 'tibble'),
infoLog,append = TRUE)
library(tibble)
###########################################
FileName=paste0(Base,'/01-Vermeulen/00-RawData/IP_DATA_C_VKHCG.csv')
write(paste0(UserName,' Retrieve data file: ', FileName),
file=infoLog,append = TRUE)
IP_DATA_C_VKHCG <- read_csv (FileName,
col_types = cols(
`IP Address` = col_character(),
`IP Number` = col_double(),
w = col_integer(),
x = col_integer(),
y = col_integer(),
z = col_integer()))
###########################################
IP_DATA_C_VKHCG_FIX=set_tidy_names(IP_DATA_C_VKHCG,
syntactic = TRUE, quiet = TRUE)
###########################################
IP_DATA_C_VKHCG_with_ID=rowid_to_column(IP_DATA_C_VKHCG_FIX, var = "RowID")
###########################################
setorderv(IP_DATA_C_VKHCG_with_ID, 'IP Number', order= -1L, na.last=FALSE)
###########################################
FileNameOut=paste0(FileDir,'/Retrieve_IP_C_VKHCG.csv')
fwrite(IP_DATA_C_VKHCG_with_ID, FileNameOut)
write(paste0(UserName,' Stores Retrieve data file: ', FileNameOut),
file=infoLog,append = TRUE)
###########################################
StopTime=Sys.time()
###########################################
write(paste0('Stop Debug Log File ',
format(StopTime, "%Y/%d/%m %H:%M:%S")),
file=debugLog,append = TRUE)
write(paste0('Stop Information Log File ',
format(StopTime, "%Y/%d/%m %H:%M:%S")),
file=infoLog,append = TRUE)
write(paste0('Stop Error Log File ',
format(StopTime, "%Y/%d/%m %H:%M:%S")),
file=errorLog,append = TRUE)
###########################################
View(IP_DATA_C_VKHCG_with_ID)
###########################################
```

保存脚本文件 `Retrieve-IP_DATA_ALL.r`。现在，获取它以执行。

如果按要求完成，你应该会有一个名为 `Retrieve_IP_C_VKHCG.csv` 的新文件。

#### 加载 `IP_DATA_CORE`

下一个数据源提供 VKHCG 内所有核心路由器的地址。现在，您将把 `IP_DATA_CORE.csv` 加载到 `Retrieve_IP_DATA_CORE.csv` 中。

创建一个新的 R 脚本文件，并将其保存为目录 `C:\VKHCG\01-Vermeulen\01-Retrieve` 下的 `Retrieve-IP_DATA_CORE.r`。现在，您可以开始将以下代码复制到脚本文件中：

### R 脚本代码

以下是完整的R脚本代码：

```r
###########################################
rm(list=ls()) #will remove ALL objects
###########################################
setwd("C:/VKHCG")
Base=getwd()
FileDir= paste0(Base,'/01-Vermeulen/01-Retrieve/01-EDS/01-R')
dir.create(FileDir)
FileDirLog=paste0(FileDir,'/log')
dir.create(FileDirLog)
FileDirRun=paste0(FileDirLog,'/Run0003')
dir.create(FileDirRun)
###########################################
StartTime=Sys.time()
###########################################
# Set up logging
###########################################
debugLog=paste0(FileDirRun,'/debug.Log')
infoLog=paste0(FileDirRun,'/info.Log')
errorLog=paste0(FileDirRun,'/error.Log')
###########################################
write(paste0('Start Debug Log File ',
format(StartTime, "%Y/%d/%m %H:%M:%S")),
file=debugLog,append = FALSE)
write(paste0('Start Information Log File ',
format(StartTime, "%Y/%d/%m %H:%M:%S")),
file=infoLog,append = FALSE)
write(paste0('Start Error Log File ',
format(StartTime, "%Y/%d/%m %H:%M:%S")),
file=errorLog,append = FALSE)
###########################################
UserName='Practical Data Scientist'
###########################################
write(paste0(UserName,' Load library: ', 'readr'),
infoLog,append = TRUE)
library(readr)
write(paste0(UserName,' Load library: ', 'data.table'),
infoLog,append = TRUE)
library(data.table)
write(paste0(UserName,' Load library: ', 'tibble'),
infoLog,append = TRUE)
library(tibble)
###########################################
FileName=paste0(Base,'/01-Vermeulen/00-RawData/IP_DATA_CORE.csv')
write(paste0(UserName,' Retrieve data file: ', FileName),
file=infoLog,append = TRUE)
IP_DATA_CORE <- read_csv (FileName,
col_types = cols(
Country = col_character(),
`First IP Number` = col_double(),
ID = col_integer(),
`Last IP Number` = col_double(),
Latitude = col_double(),
Longitude = col_double(),
`Place Name` = col_character(),
`Post Code` = col_character()
),
na = "empty"
)
###########################################
IP_DATA_CORE_FIX=set_tidy_names(IP_DATA_CORE,
syntactic = TRUE, quiet = TRUE)
###########################################
IP_DATA_CORE_with_ID=rowid_to_column(IP_DATA_CORE_FIX, var = "RowID")
###########################################
setorderv(IP_DATA_CORE_with_ID, c('Country','Place.Name','Post.Code'),
order= 1L, na.last=FALSE)
###########################################
FileNameOut=paste0(FileDir,'/Retrieve_IP_ DATA_CORE.csv')
fwrite(IP_DATA_CORE_with_ID, FileNameOut)
write(paste0(UserName,' Stores Retrieve data file: ',
FileNameOut),
file=infoLog,append = TRUE)
###########################################
StopTime=Sys.time()
###########################################
write(paste0('Stop Debug Log File ',
format(StopTime, "%Y/%d/%m %H:%M:%S")),
file=debugLog,append = TRUE)
write(paste0('Stop Information Log File ',
format(StopTime, "%Y/%d/%m %H:%M:%S")),
file=infoLog,append = TRUE)
write(paste0('Stop Error Log File ',
format(StopTime, "%Y/%d/%m %H:%M:%S")),
file=errorLog,append = TRUE)
###########################################
View(IP_DATA_CORE_with_ID)
###########################################
```

保存 `Retrieve-IP_DATA_CORE.r` 文件并 `source` 它以执行。现在，您已经完成了文件 `Retrieve_IP_ DATA_CORE.csv`，应该能够理解检索文件的基本原理。

### 加载 `COUNTRY-CODES`

现在，我们向主数据集添加一个参考文件，以协助满足第 10 章中访问数据处理的需求。目前，它只是另一个数据文件，但我将引导您完成如何使用此参考数据的过程。

创建一个新的 R 脚本文件，并将其保存为目录 `C:\VKHCG\01-Vermeulen\01-Retrieve` 下的 `Retrieve-Country_Code.r`。现在，您可以开始将以下代码复制到脚本文件中：

# R脚本：数据检索与日志记录

## 基础设置与环境准备

```r
###########################################
rm(list=ls()) #will remove ALL objects
###########################################
if (Sys.info()['sysname'] == "Windows")
{
BaseSet = "C:/VKHCG"
setwd(BaseSet)
} else {
BaseSet = paste0("/home/",  Sys.info()['user'],  "/VKHCG")
setwd(BaseSet)
}
###########################################
Base=getwd()
FileDir= paste0(Base,'/01-Vermeulen/01-Retrieve/01-EDS/01-R')
dir.create(FileDir)
FileDirLog=paste0(FileDir,'/log')
dir.create(FileDirLog)
FileDirRun=paste0(FileDirLog,'/Run0004')
dir.create(FileDirRun)
###########################################
StartTime=Sys.time()
###########################################
# Set up logging
###########################################
debugLog=paste0(FileDirRun,'/debug.Log')
infoLog=paste0(FileDirRun,'/info.Log')
errorLog=paste0(FileDirRun,'/error.Log')
###########################################
write(paste0('Start Debug Log File ',
format(StartTime, "%Y/%d/%m %H:%M:%S")),
file=debugLog,append = FALSE)
write(paste0('Start Information Log File ',
format(StartTime, "%Y/%d/%m %H:%M:%S")),
file=infoLog,append = FALSE)
write(paste0('Start Error Log File ',
format(StartTime, "%Y/%d/%m %H:%M:%S")),
file=errorLog,append = FALSE)
###########################################
UserName='Practical Data Scientist'
###########################################
write(paste0(UserName,' Load library: ', 'readr'),
infoLog,append = TRUE)
library(readr)
write(paste0(UserName,' Load library: ', 'data.table'),
infoLog,append = TRUE)
library(data.table)
write(paste0(UserName,' Load library: ', 'tibble'),
infoLog,append = TRUE)
library(tibble)
###########################################
FileName=paste0(Base,'/01-Vermeulen/00-RawData/Country_Code.csv')
write(paste0(UserName,' Retrieve data file: ', FileName),
file=infoLog,append = TRUE)
Country_Code <- read_csv (FileName,
col_types = cols(Country = col_character(),
'ISO-2-CODE' = col_character(),
'ISO-3-Code' = col_character(),
'ISO-M49' = col_integer()
),
na = "empty"
)
###########################################
Country_Code_with_ID=rowid_to_column(Country_Code, var = "RowID")
###########################################
setorderv(Country_Code_with_ID, 'ISO-2-CODE',order= 1L, na.last=FALSE)
###########################################
FileNameOut=paste0(FileDir,'/Retrieve_Country_Code.csv')
fwrite(Country_Code_with_ID, FileNameOut)
write(paste0(UserName,' Stores Retrieve data file: ', FileNameOut),
file=infoLog,append = TRUE)
###########################################
StopTime=Sys.time()
###########################################
write(paste0('Start Debug Log File ',
format(StartTime, "%Y/%d/%m %H:%M:%S")),
file=debugLog,append = TRUE)
write(paste0('Stop Information Log File ',
format(StopTime, "%Y/%d/%m %H:%M:%S")),
file=infoLog,append = TRUE)
write(paste0('Stop Error Log File ',
format(StopTime, "%Y/%d/%m %H:%M:%S")),
file=errorLog,append = TRUE)
###########################################
View(Country_Code_with_ID)
###########################################
```

保存 `Retrieve-Country_Code.r` 文件并 `source` 它以执行。

> **注**  
> 从此处开始，我移除了日志记录代码，因为我希望您专注于核心数据检索过程。当您将新技能部署到真实数据湖时，必须实现日志记录。

## Krennwallner AG

请记住，这家公司在全国各地的广告牌上宣传我们客户的产品。

> **注**  
> 我将从一个名为 `C:\VKHCG\02-Krennwallner\00-RawData` 的新位置加载数据。

### 加载 DE_Billboard_Locations

接下来，我将引导你检索德国的广告牌位置。首先，将 `DE_Billboard_Locations.csv` 加载到 `Retrieve_DE_Billboard_Locations.csv` 中。

创建一个新的 R 脚本文件，并将其保存为 `Retrieve-DE_Billboard_Locations.r`，存放于目录 `C:\VKHCG\02-Krennwallner\01-Retrieve`。现在，你可以开始将以下代码复制到脚本文件中。


```
rm(list=ls()) #will remove ALL objects
if (Sys.info()['sysname'] == "Windows")
{
`BaseSet` = "C:/VKHCG"
`setwd(`BaseSet`)`
} else {
`BaseSet` = paste0("/home/",  `Sys.info()['user']`,  "/VKHCG")
`setwd(`BaseSet`)`
}
`Base`=`getwd()`
`FileDir`= paste0(`Base`,'/02-Krennwallner/01-Retrieve/01-EDS/01-R')
`library(`readr`)`
`library(`data.table`)`
`FileName`=paste0(`Base`,'/02-Krennwallner/00-RawData/DE_Billboard_Locations.csv')
`DE_Billboard_Locations` <- `read_csv(`FileName`,
col_types = cols(
Country = col_character(),
ID = col_integer(),
Latitude = col_double(),
Longitude = col_double(),
PlaceName = col_character()
), na = "empty")`
`setnames(`DE_Billboard_Locations`,'PlaceName','Place.Name')`
`setorder(`DE_Billboard_Locations`,Latitude,Longitude)`
`FileNameOut`=paste0(`FileDir`,'/Retrieve_DE_Billboard_Locations.csv')
`fwrite(`DE_Billboard_Locations`, FileNameOut)`
`View(`DE_Billboard_Locations`)`
```

保存 `Retrieve-DE_Billboard_Locations.r` 并运行（`source`）它来执行。

**提示**

你是否注意到了 `Place.Name` 列中的数据问题？我将在第 10 章中帮助你修复它们。

你发现的数据错误是由于德语名称中的特殊字符造成的。将数据交付到数据湖的导出程序导致了这个问题。这是数据科学家在将数据湖转化为业务知识时必须处理的常见问题。

Krennwallner 公司的数据已加载完毕，我们可以继续处理下一家公司了。

### Hillman Ltd

这是我们的物流公司。

**注意**

我将从一个新位置加载数据：`..\VKHCG\03-Hillman\00-RawData`。

#### 加载 `GB_Postcode_Full`

此数据源包含英国所有邮政编码信息。首先，将 `DE_Billboard_Locations.csv` 加载到 `Retrieve_GB_Postcode_Full.csv` 中。创建一个新的 R 脚本文件，并将其保存为 `Retrieve-GB_Postcode_Full.r`，存放于目录 `C:/VKHCG/03-Hillman/01-Retrieve/01-EDS/01-R`。

```
rm(list=ls()) #will remove ALL objects
library(readr)
library(data.table)
library(tibble)
if (Sys.info()['sysname'] == "Windows")
{
BaseSet = "C:/VKHCG"
setwd(BaseSet)
} else {
BaseSet = paste0("/home/",  Sys.info()['user'],  "/VKHCG")
setwd(BaseSet)
}
Base=getwd()
FileDir= paste0(Base,'/03-Hillman/01-Retrieve/01-EDS/01-R')
FileName=paste0(Base,'/03-Hillman/00-RawData/GB_Postcode_Full.csv')
GB_Postcode_Full <- read_csv(FileName,
col_types = cols(
AreaName = col_character(),
Country = col_character(),
ID = col_integer(),
PlaceName = col_character(),
PostCode = col_character(),
Region = col_character(),
RegionCode = col_character()
),
na = "empty")
FileNameOut=paste0(FileDir,'/Retrieve_GB_Postcode_Full.csv')
fwrite(GB_Postcode_Full, FileNameOut)
View(GB_Postcode_Full)
```

保存 `Retrieve-GB_Postcode_Full.r` 并运行（`source`）它来执行。

#### 加载 `GB_Postcode_Warehouse`

以下数据包含了所有仓库的位置信息。首先，将 `GB_Postcode_Warehouse.csv` 加载到 `Retrieve_GB_Postcode_Warehouse.csv` 中。然后，我将引导你以 JSON 格式创建并读回数据。我还将向你展示如何使用 `library(jsonlite)` 生成两个文件：`Retrieve_GB_Postcode_Warehouse_A.json` 和 `Retrieve_GB_Postcode_Warehouse_B.json`。

**警告**

我假设你了解 JSON 数据格式。如果你不熟悉，请阅读下一段。

JSON（JavaScript 对象表示法）是一种轻量级的数据交换格式，源系统使用它生成数据以便于传输。

创建一个新的 R 脚本文件，并将其保存为 `Retrieve-GB_Postcode_Warehouse.r`，存放于目录 `../VKHCG/03-Hillman/01-Retrieve/01-EDS/01-R`。


```
## R 脚本：Retrieve-GB_Postcode_Warehouse.r

```r
rm(list=ls()) #will remove ALL objects
library(readr)
library(data.table)
library(jsonlite)

if (Sys.info()['sysname'] == "Windows")
{
BaseSet = "C:/VKHCG"
setwd(BaseSet)
} else {
BaseSet = paste0("/home/",  Sys.info()['user'],  "/VKHCG")
setwd(BaseSet)
}

Base=getwd()
FileDir= paste0(Base,'/03-Hillman/01-Retrieve/01-EDS/01-R')
FileName=paste0(Base,'/03-Hillman/00-RawData/GB_Postcode_Warehouse.csv')
GB_Postcode_Warehouse <- read_csv(FileName,
col_types = cols(
id = col_integer(),
latitude = col_double(),
longitude = col_double(),
postcode = col_character()
),
na = "empty")

FileNameOut=paste0(FileDir,'/Retrieve_GB_Postcode_Warehouse.csv')
fwrite(GB_Postcode_Warehouse, FileNameOut)

GB_Postcode_Warehouse_JSON_A=toJSON(GB_Postcode_Warehouse, pretty=TRUE)

FileNameJSONA=paste0(FileDir,'/Retrieve_GB_Postcode_Warehouse_A.json')
write(GB_Postcode_Warehouse_JSON_A, FileNameJSONA)

GB_Postcode_Warehouse_JSON_B=toJSON(GB_Postcode_Warehouse, pretty=FALSE)

FileNameJSONB=paste0(FileDir,'/Retrieve_GB_Postcode_Warehouse_B.json')
write(GB_Postcode_Warehouse_JSON_B, FileNameJSONB)

View(GB_Postcode_Warehouse)

GB_Postcode_Warehouse_A=json_data <- fromJSON(paste(readLines(FileNameJSONA), collapse=""))

View(GB_Postcode_Warehouse_A)

GB_Postcode_Warehouse_B=json_data <- fromJSON(paste(readLines(FileNameJSONB), collapse=""))

View(GB_Postcode_Warehouse_B)
```

保存`Retrieve-GB_Postcode_Warehouse.r`并运行（`source`）它来执行。

你可以创建并加载 JSON 文件。这是你在与数据湖交互时会发现有用的另一项技能。

## 加载 GB_Postcode_Shops

以下数据集包含了所有商店的位置信息。首先，将`GB_Postcodes_Shops.csv`加载到`Retrieve_GB_Postcodes_Shops.csv`中。

我还将引导你创建并以 XML 格式读取五个商店的数据。我还将向你展示如何使用`library(XML)`生成文件`Retrieve_GB_Postcodes_Shops.xml`。

**警告**

我假设你了解 XML 数据格式。如果你不熟悉，请阅读下一段。

XML 代表可扩展标记语言（EXtensible Markup Language）。XML 是一种与 HTML 类似的标记语言，但 XML 用于在互联网上传输数据。它是一种使用标准 HTTP 技术传输数据的协议。

**提示**

生成 XML 文件非常耗时，并且必须在数据湖生态系统中尽量少用。应尽快将其转换为更简洁、更不冗长的格式。

创建一个新的 R 脚本文件，并将其保存为`../VKHCG/03-Hillman/01-Retrieve/01-EDS/01-R`目录下的`Retrieve-GB_Postcode_Shops.r`。
```

## Clark Ltd

Clark 是集团内财务信息的来源。

> **注意**：我正在从一个新位置加载数据：`../VKHCG/04-Clark/00-RawData`。

### 加载 Euro_ExchangeRates

首先，将 `Euro_ExchangeRates.csv` 加载到 `Retrieve_Euro_ExchangeRates_Pivot.csv` 中。创建一个新的 R 脚本文件，并将其保存为 `C:/VKHCG/04-Clark/00-RawData/01-Retrieve/01-EDS/01-R` 目录下的 `Retrieve-Euro_ExchangeRates_Pivot.r`。

```
rm(list=ls()) #will remove ALL objects
library(readr)
library(data.table)
library(stringi)
if (Sys.info()['sysname'] == "Windows")
{
BaseSet = "C:/VKHCG"
setwd(BaseSet)
} else {
BaseSet = paste0("/home/",  Sys.info()['user'],  "/VKHCG")
setwd(BaseSet)
}
Base=getwd()
FileDir= paste0(Base,'/04-Clark/01-Retrieve/01-EDS/01-R')
FileName=paste0(Base,'/04-Clark/00-RawData/Euro_ExchangeRates.csv')
Euro_ExchangeRates <- read_csv(FileName,
col_types = cols(
.default = col_double(),
Date = col_date(format = "%d/%m/%Y")
),
locale = locale(asciify = TRUE), na = "empty")
FileNameOut=paste0(FileDir,'/Retrieve_Euro_ExchangeRates_Pivot.csv')
fwrite(Euro_ExchangeRates, FileNameOut)
View(Euro_ExchangeRates)
```

你得到的这个数据结构的文件被称为数据透视表。这是财务人员生成的常见数据输出。然而，这并不是数据科学家希望加载数据的格式。因此，我必须引导你完成一个可用于解决此透视结构的过程。

将 `Euro_ExchangeRates.csv` 重新加载到 `Retrieve_Euro_ExchangeRates.csv` 中，创建一个新的 R 脚本文件，并将其保存为 `C:/VKHCG/04-Clark/00-RawData/01-Retrieve/01-EDS/01-R` 目录下的 `Retrieve-Euro_ExchangeRates.r`。

```
rm(list=ls()) #will remove ALL objects
library(readr)
library(data.table)
if (Sys.info()['sysname'] == "Windows")
{
BaseSet = "C:/VKHCG"
setwd(BaseSet)
} else {
BaseSet = paste0("/home/",  Sys.info()['user'],  "/VKHCG")
setwd(BaseSet)
}
Base=getwd()
FileDir= paste0(Base,'/04-Clark/01-Retrieve/01-EDS/01-R')
FileName=paste0(Base,'/04-Clark/00-RawData/Euro_ExchangeRates.csv')
Euro_ExchangeRates <- read_csv(FileName,
col_types = cols(
.default = col_double(),
Date = col_date(format = "%d/%m/%Y")
),
locale = locale(asciify = TRUE), na = "empty")
FileNameOut=paste0(FileDir,'/Retrieve_Euro_ExchangeRates.csv')
fwrite(Euro_ExchangeRates, FileNameOut)
View(Euro_ExchangeRates)
```

## Python 检索解决方案

现在，我将引导你完成检索超步骤的 Python 解决方案。

### 加载：`Profit_And_Loss`

首先，将 `Profit_And_Loss.csv` 加载到 `Retrieve_Profit_And_Loss.csv` 中。创建一个新的 R 脚本文件，并将其保存为 `Retrieve-Profit_And_Loss.r`，路径为 `../VKHCG/04-Clark/00-RawData/01-Retrieve/01-EDS/01-R`。

```
###########################################
###########################################
rm(list=ls()) #will remove ALL objects
###########################################
library(readr)
library(data.table)
library(tibble)
###########################################
if (Sys.info()['sysname'] == "Windows")
{
BaseSet = "C:/VKHCG"
setwd(BaseSet)
} else {
BaseSet = paste0("/home/",  Sys.info()['user'],  "/VKHCG")
setwd(BaseSet)
}
###########################################
Base=getwd()
FileDir= paste0(Base,'/04-Clark/01-Retrieve/01-EDS/01-R')
FileName=paste0(Base,'/04-Clark/00-RawData/Profit_And_Loss.csv')
###########################################
Profit_And_Loss <- read_csv(FileName,
col_types = cols(
Amount = col_double(),
ProductClass1 = col_character(),
ProductClass2 = col_character(),
ProductClass3 = col_character(),
QTR = col_character(),
QTY = col_double(),
TypeOfEntry = col_character()
), na = "empty")
###########################################
### Sort the results
###########################################
keyList=c('QTR','TypeOfEntry','ProductClass1','ProductClass2','ProductClass3')
setorderv(Profit_And_Loss, keyList, order= c(-1L,1L,1L,1L,1L), na.last=FALSE)
###########################################
FileNameOut=paste0(FileDir,'/Retrieve_Profit_And_Loss.csv')
fwrite(Profit_And_Loss, FileNameOut)
###########################################
View(Profit_And_Loss)
###########################################
```

保存 `Retrieve_Profit_And_Loss.csv` 并引用该文件以执行。

### 概述

保存脚本 `Retrieve-Euro_ExchangeRates.r` 并通过 `source` 执行它。你已通过将其转换为特定日期两种汇率之间的关系，解决了数据透视表的问题。这在第 10 章和第 11 章的数据科学工作中将非常有用。

```
###########################################
rm(list=ls()) #will remove ALL objects
###########################################
library("data.table")
library("readr")
###########################################
if (Sys.info()['sysname'] == "Windows")
{
BaseSet = "C:/VKHCG"
setwd(BaseSet)
} else {
BaseSet = paste0("/home/",  Sys.info()['user'],  "/VKHCG")
setwd(BaseSet)
}
###########################################
Base=getwd()
FileDir= paste0(Base,'/04-Clark/01-Retrieve/01-EDS/01-R')
FileName=paste0(Base,'/04-Clark/00-RawData/Euro_ExchangeRates.csv')
Euro_ExchangeRates 1) Exchange=data.table(rbind(Exchange,ExchangeRates))
}
###########################################
Exchange2=Exchange
###########################################
colnames(Exchange)[colnames(Exchange)=="Rate"] <- "RateIn"
colnames(Exchange)[colnames(Exchange)=="Code"] <- "CodeIn"
colnames(Exchange2)[colnames(Exchange2)=="Rate"] <- "RateOut"
colnames(Exchange2)[colnames(Exchange2)=="Code"] <- "CodeOut"
ExchangeRate=merge(Exchange, Exchange2, by=c("Base","Date"), all=TRUE,
sort=FALSE,allow.cartesian=TRUE)
ExchangeRates <- data.table(ExchangeRate,
Rate=with(
ExchangeRate,
round((as.numeric(RateOut) / as.numeric(RateIn)),9)
))
###########################################
### Remove work columns
###########################################
ExchangeRates$Base <-NULL
ExchangeRates$RateIn <-NULL
ExchangeRates$RateOut  <-NULL
###########################################
### Make entries unique
###########################################
ExchangeRate=unique(ExchangeRates)
###########################################
### Sort the results
###########################################
setorderv(ExchangeRate, c('Date','CodeIn','CodeOut'),
order= c(-1L,1L,1L), na.last=FALSE)
###########################################
### Write Results
###########################################
FileNameOut=paste0(FileDir,'/Retrieve_Euro_ExchangeRates.csv')
fwrite(ExchangeRate, FileNameOut)
###########################################
### View Results
###########################################
View(ExchangeRate)
###########################################
```

我将用 Python 重复一个已经完成的 R 步骤，以说明你可以互换技术栈，同时仍然完成类似的数据科学原则。

## 从数据湖中获取可操作的业务知识

我将引导你了解几个可操作的业务流程，你可以直接从样本数据集中的数据中制定这些流程。

## 设计一个实用的检索超步骤

### Vermeulen PLC

我将使用 Python 引导你完成第一个公司的任务。我只想向你展示，使用 Python，你可以达到与 R 相同的结果。

注意：为了说明我的观点，我只展示第一次数据加载。

启动你的 Python 编辑器。创建一个名为 `Retrieve-IP_DATA_ALL.py` 的文本文件，路径为 `..\VKHCG\01-Vermeulen\01-Retrieve`。

以下是必须复制到文件中的 Python 代码：

```python
################################################################
# -*- coding: utf-8 -*-
################################################################
import os
import pandas as pd
################################################################
if sys.platform == 'linux':
    Base=os.path.expanduser('~') + '/VKHCG'
else:
    Base='C:/VKHCG'
################################################################
sFileName=Base + '/01-Vermeulen/00-RawData/IP_DATA_ALL.csv'
print('Loading :',sFileName)
IP_DATA_ALL=pd.read_csv(sFileName,header=0,low_memory=False)
################################################################
sFileDir=Base + '/01-Vermeulen/01-Retrieve/01-EDS/02-Python'
if not os.path.exists(sFileDir):
    os.makedirs(sFileDir)
print('Rows:', IP_DATA_ALL.shape[0])
print('Columns:', IP_DATA_ALL.shape[1])
print('### Raw Data Set #####################################')
for i in range(0,len(IP_DATA_ALL.columns)):
    print(IP_DATA_ALL.columns[i],type(IP_DATA_ALL.columns[i]))
print('### Fixed Data Set ###################################')
IP_DATA_ALL_FIX=IP_DATA_ALL
for i in range(0,len(IP_DATA_ALL.columns)):
    cNameOld=IP_DATA_ALL_FIX.columns[i] + '     '
    cNameNew=cNameOld.strip().replace(" ", ".")
    IP_DATA_ALL_FIX.columns.values[i] = cNameNew
    print(IP_DATA_ALL.columns[i],type(IP_DATA_ALL.columns[i]))
################################################################
#print(IP_DATA_ALL_FIX.head())
################################################################
print('Fixed Data Set with ID')
IP_DATA_ALL_with_ID=IP_DATA_ALL_FIX
IP_DATA_ALL_with_ID.index.names = ['RowID']
#print(IP_DATA_ALL_with_ID.head())
sFileName2=sFileDir + '/Retrieve_IP_DATA.csv'
IP_DATA_ALL_with_ID.to_csv(sFileName2, index = True)
################################################################
print('### Done!! ############################################')
################################################################
```

现在，保存该文本文件。使用你偏好的 Python 编译器执行 `Retrieve-IP_DATA_ALL.py`。

注意：我使用的是 Anaconda 环境下的 Spyder 3 和 Python 3。

完成此任务后，你就证明了使用不同的工具以及一位优秀的数据科学家，你可以轻松处理大多数数据。

我在本书中引用的任何源代码或其他补充材料，均可通过本书的产品页面在 GitHub 上获取，地址为 [`www.apress.com/9781484230534`](http://www.apress.com/9781484230534)。请注意，此源代码假设你已经完成了第 2 章中的源代码设置。

现在我已经解释了检索超步骤的各个方面，我将解释如何帮助我们的公司进行数据处理。

方法如下：

*   确定所需的数据源。
*   识别源数据格式（CSV、XML、JSON 或数据库）。
*   对数据分布进行数据剖析（偏度、直方图、最小值、最大值）。
*   识别任何加载特征（列名、数据类型、数据量）。
*   确定交付格式（CSV、XML、JSON 或数据库）。

警告：本书后续部分将使用 Python 作为处理引擎，并随着我们的进展逐步添加和解释新的库和函数。我使用的是 Anaconda 生态系统，配有 Spyder 3 编辑器和 Python 3 编译器。

### Vermeulen PLC

这家公司有两项主要工作需要你重点关注：

*   为公司设计路由图。
*   为路由网络规划要执行的任务计划。

为了执行这些任务，我将引导你完成两个独立的数据检索操作。对于这个实践性数据科学任务，你需要准备好你的 Python 编辑器。

# 为公司设计路由图

网络路由图就像是公司网络中所有潜在路线的一张地图，类似于道路图。我将帮助您使用经纬度创建一组起点和终点位置。我将通过计算位置之间的距离（以公里和英里为单位）来实现特征提取。

## 打开您的 Python 编辑器

在目录`..\VKHCG\01-Vermeulen\01-Retrieve`中创建一个名为`Retrieve-IP_Routing.py`的文本文件。

## 让我们检索该公司的核心 IP 数据结构

以下是您必须复制到文件中的 Python 代码。

```
################################################################
# -*- coding: utf-8 -*-
################################################################
import os
import pandas as pd
from math import radians, cos, sin, asin, sqrt
################################################################
def haversine(lon1, lat1, lon2, lat2,stype):
### 将十进制度数转换为弧度
lon1, lat1, lon2, lat2 = map(radians, [lon1, lat1, lon2, lat2])
### 半正矢公式
dlon = lon2 - lon1
dlat = lat2 - lat1
a = sin(dlat/2)**2 + cos(lat1) * cos(lat2) * sin(dlon/2)**2
c = 2 * asin(sqrt(a))
### 距离类型（公里/英里）
if stype == 'km':
r = 6371 # 地球半径（公里）
else:
r = 3956 # 地球半径（英里）
d=round(c * r,3)
return d
################################################################
Base='C:/VKHCG'
sFileName=Base + '/01-Vermeulen/00-RawData/IP_DATA_CORE.csv'
print('正在加载 :',sFileName)
IP_DATA_ALL=pd.read_csv(sFileName,header=0,low_memory=False,
usecols=['Country','Place Name','Latitude','Longitude'])
################################################################
sFileDir=Base + '/01-Vermeulen/01-Retrieve/01-EDS/02-Python'
if not os.path.exists(sFileDir):
os.makedirs(sFileDir)
IP_DATA = IP_DATA_ALL.drop_duplicates(subset=None, keep="first", inplace=False)
IP_DATA.rename(columns={'Place Name': 'Place_Name'}, inplace=True)
IP_DATA1 = IP_DATA
IP_DATA1.insert(0, 'K', 1)
IP_DATA2 = IP_DATA1
print(IP_DATA1.shape)
IP_CROSS=pd.merge(right=IP_DATA1,left=IP_DATA2,on='K')
IP_CROSS.drop('K', axis=1, inplace=True)
IP_CROSS.rename(columns={'Longitude_x': 'Longitude_from', 'Longitude_y': 'Longitude_to'}, inplace=True)
IP_CROSS.rename(columns={'Latitude_x': 'Latitude_from', 'Latitude_y': 'Latitude_to'}, inplace=True)
IP_CROSS.rename(columns={'Place_Name_x': 'Place_Name_from', 'Place_Name_y': 'Place_Name_to'}, inplace=True)
IP_CROSS.rename(columns={'Country_x': 'Country_from', 'Country_y': 'Country_to'}, inplace=True)
IP_CROSS['DistanceBetweenKilometers'] = IP_CROSS.apply(lambda row:
haversine(
row['Longitude_from'],
row['Latitude_from'],
row['Longitude_to'],
row['Latitude_to'],
'km')
,axis=1)
IP_CROSS['DistanceBetweenMiles'] = IP_CROSS.apply(lambda row:
haversine(
row['Longitude_from'],
row['Latitude_from'],
row['Longitude_to'],
row['Latitude_to'],
'miles')
,axis=1)
print(IP_CROSS.shape)
sFileName2=sFileDir + '/Retrieve_IP_Routing.csv'
IP_CROSS.to_csv(sFileName2, index = False)
################################################################
print('### 完成！！ ############################################')
################################################################
```

## 现在，保存该文本文件

使用您偏好的 Python 编译器执行`Retrieve-IP_Routing.py`。您将在`C:\VKHCG\01-Vermeulen\01-Retrieve\01-EDS\02-Python`中看到一个名为`Retrieve_IP_Routing.csv`的文件。打开此文件，您将看到一个类似以下布局的新数据集：

| Country_from | Place_Name_from | Latitude_from | Longitude_from | Country_to | Place_Name_to | Latitude_to | Longitude_to | DistanceBetweenKilometers | DistanceBetweenMiles |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| US | New York | 40.7528 | -73.9725 | US | New York | 40.7528 | -73.9725 | 0 | 0 |
| US | New York | 40.7528 | -73.9725 | US | New York | 40.7214 | -74.0052 | 4.448 | 2.762 |

所以，纽约（40.7528, -73.9725）到纽约（40.7214, -74.0052）之间的距离是 4.45 公里，即 2.77 英里。

做得好！您已经从一个 IP 地址数据集中提取了一个特征以及一个隐藏特征——IP 地址之间的距离。

# 构建任务调度图

您可以提取核心路由器的位置来安排维护任务。既然我们知道路由器在哪里，就必须制定一个每月必须完成的维护任务时间表。为了实现这一点，我们必须检索每个路由器的位置，以便为维护它们的员工准备时间表。

打开您的 Python 编辑器，并在目录`..\VKHCG\01-Vermeulen\01-Retrieve`中创建一个名为`Retrieve-Router-Location.py`的文本文件。以下是您必须复制到文件中的 Python 代码：

```python
################################################################
# -*- coding: utf-8 -*-
################################################################
import os
import pandas as pd
################################################################
InputFileName='IP_DATA_CORE.csv'
OutputFileName='Retrieve_Router_Location.csv'
################################################################
if sys.platform == 'linux':
Base=os.path.expanduser('~') + '/VKHCG'
else:
Base='C:/VKHCG'
################################################################
sFileName=Base + '/01-Vermeulen/00-RawData/' + InputFileName
print('正在加载 :',sFileName)
IP_DATA_ALL=pd.read_csv(sFileName,header=0,low_memory=False,
usecols=['Country','Place Name','Latitude','Longitude'])
IP_DATA_ALL.rename(columns={'Place Name': 'Place_Name'}, inplace=True)
################################################################
sFileDir=Base + '/01-Vermeulen/01-Retrieve/01-EDS/02-Python'
if not os.path.exists(sFileDir):
os.makedirs(sFileDir)
ROUTERLOC = IP_DATA_ALL.drop_duplicates(subset=None, keep="first", inplace=False)
print('行数 :',ROUTERLOC.shape[0])
print('列数 :',ROUTERLOC.shape[1])
sFileName2=sFileDir + '/' + OutputFileName
ROUTERLOC.to_csv(sFileName2, index = False)
################################################################
print('### 完成！！ ############################################')
################################################################
```

现在保存该 Python 文件。使用您偏好的 Python 编译器执行`Retrieve-Router-Location.py`。您将在`C:\VKHCG\01-Vermeulen\01-Retrieve\01-EDS\02-Python`中看到一个名为`Retrieve_Router_Location.csv`的文件。

打开此文件，您应该会看到类似以下内容的数据集：

| Country | Place_Name | Latitude | Longitude |
| :--- | :--- | :--- | :--- |
| US | New York | 40.7528 | -73.9725 |
| US | New York | 40.7214 | -74.0052 |
| US | New York | 40.7662 | -73.9862 |

您已成功检索到 150 个路由器的位置。

还有另一组数据，您可以运用新学的技能进行处理。首先，修改您刚创建的代码，更改以下代码值：

```python
InputFileName='IP_DATA_ALL.csv'
OutputFileName='Retrieve_All_Router_Location.csv'
```

现在，将 Python 文件另存为`Retrieve-Router-All-Location.py`，并使用您偏好的 Python 编译器执行新代码。您将在`C:\VKHCG\01-Vermeulen\01-Retrieve\01-EDS\02-Python`中看到一个名为`Retrieve_All_Router_Location.csv`的文件。

**注意**
您可能还记得第 5 章中关于实用程序的讨论。此代码是一个实用程序的开端。

做得好，您刚刚完成了 Vermeulen 的检索任务。

让我们回顾一下您所取得的成就。

* 您现在可以使用 Python 从 CSV 文件中提取数据。
* 您知道如何从数据集中提取隐藏特征，即两个位置之间的距离。
* 您可以通过使用通用变量进行少量代码更改来节省编程时间。

您现在已经成功生成了超级数据科学代码。

## 科伦沃尔纳股份公司

公司有两项主要工作需要您关注：

-   挑选广告牌内容：我将指导您完成所需的数据科学工作，以便为公司内的每个广告牌挑选广告。
-   理解您的在线访客数据：我将指导您评估广告牌在线网络服务器的网络流量。

### 挑选广告牌内容

让我们检索在德国的广告牌数据。启动您的 Python 编辑器，在目录`..\VKHCG\02-Krennwallner\01-Retrieve`中创建一个名为`Retrieve-DE-Billboard-Locations.py`的文本文件。

以下是您必须复制到文件中的 Python 代码：

```
################################################################
# -*- coding: utf-8 -*-
################################################################
import os
import pandas as pd
################################################################
InputFileName='DE_Billboard_Locations.csv'
OutputFileName='Retrieve_DE_Billboard_Locations.csv'
Company='02-Krennwallner'
################################################################
if sys.platform == 'linux':
Base=os.path.expanduser('~') + '/VKHCG'
else:
Base='C:/VKHCG'
################################################################
sFileName=Base + '/' + Company + '/00-RawData/' + InputFileName
print('Loading :',sFileName)
IP_DATA_ALL=pd.read_csv(sFileName,header=0,low_memory=False,
usecols=['Country','PlaceName','Latitude','Longitude'])
IP_DATA_ALL.rename(columns={'PlaceName': 'Place_Name'}, inplace=True)
################################################################
sFileDir=Base + '/' + Company + '/01-Retrieve/01-EDS/02-Python'
if not os.path.exists(sFileDir):
os.makedirs(sFileDir)
ROUTERLOC = IP_DATA_ALL.drop_duplicates(subset=None, keep="first", inplace=False)
print('Rows :',ROUTERLOC.shape[0])
print('Columns :',ROUTERLOC.shape[1])
sFileName2=sFileDir + '/' + OutputFileName
ROUTERLOC.to_csv(sFileName2, index = False)
################################################################
print('### Done!! ############################################')
################################################################
```

现在保存这个 Python 文件，并用您偏好的 Python 编译器执行`Retrieve-DE-Billboard-Locations.py`。您将在`C:\VKHCG\02-Krennwallner\01-Retrieve\01-EDS\02-Python`中看到一个名为`Retrieve_Router_Location.csv`的文件。

打开这个文件，您应该会看到一个类似这样的数据集：

| Country (国家) | Place_Name (地名) | Latitude (纬度) | Longitude (经度) |

| --- | --- | --- | --- |

| DE | Lake (湖) | 51.7833 | 8.5667 |

| DE | Horb (霍尔布) | 48.4333 | 8.6833 |

| DE | Hardenberg (哈登贝格) | 51.1 | 7.7333 |

| DE | Horn-bad Meinberg (霍恩-巴特迈恩贝格) | 51.9833 | 8.9667 |

#### 理解您的在线访客数据

让我们检索在德国广告牌的访客数据。

**问题**

我们在科伦沃尔纳没有访客数据。

我建议您查看所有可用的数据源。

**已解决**

数据嵌入在*弗穆伦公司*的数据源中。请参见`IP_DATA_ALL.csv`。

我发现，如果您小心谨慎地收集业务信息（我在第 4 章中介绍过），您将为每个数据源在数据映射矩阵（见第 4 章）中建立一个条目。这种数据源的交叉使用可以成为您数据科学工作中的可靠解决方案。

在我与客户的多次合作中，我发现常见且重要的信息常常埋藏在公司各个数据源的某个角落。我建议调查与特定业务流程相关的任何直接供应商或消费者的上游或下游数据源。这是您应用于数据科学的部分技能。我本人就在围绕客户业务流程的数据源中发现了许多富有洞察力的信息片段。

既然我们已经知道了访客数据的位置，我将指导您加载它。启动您的 Python 编辑器，在目录`.\VKHCG\02-Krennwallner\01-Retrieve`中创建一个名为`Retrieve-Online-Visitor.py`的文本文件。

```python
################################################################
# -*- coding: utf-8 -*-
################################################################
import sys
import os
import pandas as pd
import gzip as gz
################################################################
InputFileName='IP_DATA_ALL.csv'
OutputFileName='Retrieve_Online_Visitor'
CompanyIn= '01-Vermeulen'
CompanyOut= '02-Krennwallner'
################################################################
if sys.platform == 'linux':
    Base=os.path.expanduser('~') + '/VKHCG'
else:
    Base='C:/VKHCG'
print('################################')
print('Working Base :',Base, ' using ', sys.platform)
print('################################')
################################################################
Base='C:/VKHCG'
sFileName=Base + '/' + CompanyIn + '/00-RawData/' + InputFileName
print('Loading :',sFileName)
IP_DATA_ALL=pd.read_csv(sFileName,header=0,low_memory=False,
                        usecols=['Country','Place Name','Latitude','Longitude','First IP Number','Last IP Number'])
IP_DATA_ALL.rename(columns={'Place Name': 'Place_Name'}, inplace=True)
IP_DATA_ALL.rename(columns={'First IP Number': 'First_IP_Number'}, inplace=True)
IP_DATA_ALL.rename(columns={'Last IP Number': 'Last_IP_Number'}, inplace=True)
################################################################
sFileDir=Base + '/' + CompanyOut + '/01-Retrieve/01-EDS/02-Python'
if not os.path.exists(sFileDir):
    os.makedirs(sFileDir)
visitordata = IP_DATA_ALL.drop_duplicates(subset=None, keep="first", inplace=False)
visitordata10=visitordata.head(10)
print('Rows :',visitordata.shape[0])
print('Columns :',visitordata.shape[1])
print('Export CSV')
sFileName2=sFileDir + '/' + OutputFileName + '.csv'
visitordata.to_csv(sFileName2, index = False)
print('Store All:',sFileName2)
sFileName3=sFileDir + '/' + OutputFileName + '_10.csv'
visitordata10.to_csv(sFileName3, index = False)
print('Store 10:',sFileName3)
for z in ['gzip', 'bz2', 'xz']:
    if z == 'gzip':
        sFileName4=sFileName2 + '.gz'
    else:
        sFileName4=sFileName2 + '.' + z
    visitordata.to_csv(sFileName4, index = False, compression=z)
    print('Store :',sFileName4)
################################################################
print('Export JSON')
for sOrient in ['split','records','index', 'columns','values','table']:
    sFileName2=sFileDir + '/' + OutputFileName + '_' + sOrient + '.json'
    visitordata.to_json(sFileName2,orient=sOrient,force_ascii=True)
    print('Store All:',sFileName2)
    sFileName3=sFileDir + '/' + OutputFileName + '_10_' + sOrient + '.json'
    visitordata10.to_json(sFileName3,orient=sOrient,force_ascii=True)
    print('Store 10:',sFileName3)
    sFileName4=sFileName2 + '.gz'
    file_in = open(sFileName2, 'rb')
    file_out = gz.open(sFileName4, 'wb')
    file_out.writelines(file_in)
    file_in.close()
    file_out.close()
    print('Store GZIP All:',sFileName4)
    sFileName5=sFileDir + '/' + OutputFileName + '_' + sOrient + '_UnGZip.json'
    file_in = gz.open(sFileName4, 'rb')
    file_out = open(sFileName5, 'wb')
    file_out.writelines(file_in)
    file_in.close()
    file_out.close()
    print('Store UnGZIP All:',sFileName5)
################################################################
print('### Done!! ############################################')
################################################################
```

现在保存该 Python 文件。使用您偏好的 Python 编译器执行 `Retrieve-Online-Visitor.py`。您会在 `C:\VKHCG\02-Krennwallner\01-Retrieve\01-EDS\02-Python` 目录下看到一个名为 `Retrieve_Online_Visitor.csv` 的文件。打开此文件，您应该会看到如下所示的数据集：

| Country | Place_Name | Latitude | Longitude | First_IP_Number | Last_IP_Number |
| --- | --- | --- | --- | --- | --- |
| BW | Gaborone | -24.6464 | 25.9119 | 692781056 | 692781567 |
| BW | Gaborone | -24.6464 | 25.9119 | 692781824 | 692783103 |
| BW | Gaborone | -24.6464 | 25.9119 | 692909056 | 692909311 |
| BW | Gaborone | -24.6464 | 25.9119 | 692909568 | 692910079 |

我已经演示了您可以以多种 JSON 格式导出相同的数据。

> **警告**
> 请记住：仅符合 JSON 规范不足以保证文件可读。方向格式化对于处理文件至关重要。如果您使用的布局方向与源系统不同，数据文件将无法加载。

您还可以看到以下仅包含十条记录的 JSON 文件。在文本编辑器中打开它们，如下所示：

- `split`：使用格式 `{index -> [index], columns -> [columns], data -> [values]}`。请参见 `Retrieve_Online_Visitor_10_split.json`。

```json
{
"columns":
"Country","Place_Name","Latitude","Longitude","First_IP_Number","Last_IP_Number"
],
"index":
[
0,1
],
"data":[
["BW","Gaborone",-24.6464,25.9119,692781056,692781567],
["BW","Gaborone",-24.6464,25.9119,692781824,692783103]
]
}
```

- `records`：使用格式 `[{column -> value}, . . .,  {column -> value}]`。请参见 `Retrieve_Online_Visitor_10_records.json`。

```json
[
{
"Country":"BW",
"Place_Name":"Gaborone",
"Latitude":-24.6464,
"Longitude":25.9119,
"First_IP_Number":692781056,
"Last_IP_Number":692781567
}
,
{
"Country":"BW",
"Place_Name":"Gaborone",
"Latitude":-24.6464,
"Longitude":25.9119,
"First_IP_Number":692781824,
"Last_IP_Number":692783103
}
]
```

- `index`：使用格式 `{index -> {column -> value}}`。请参见 `Retrieve_Online_Visitor_10_index.json`。

```json
{
"0":
{
"Country":"BW",
"Place_Name":"Gaborone",
"Latitude":-24.6464,
"Longitude":25.9119,
"First_IP_Number":692781056,
"Last_IP_Number":692781567
}
,
"1":
{
"Country":"BW",
"Place_Name":"Gaborone",
"Latitude":-24.6464,
"Longitude":25.9119,
"First_IP_Number":692781824,
"Last_IP_Number":692783103
}
}
```

- `columns`：使用格式 `{column -> {index -> value}}`。请参见 `Retrieve_Online_Visitor_10_columns.json`。

```json
{
"Country":
{"0":"BW","1":"BW"},
"Place_Name":
{"0":"Gaborone","1":"Gaborone"},
"Latitude":
{"0":-24.6464,"1":-24.6464},
"Longitude":
{"0":25.9119,"1":25.9119},
"First_IP_Number":
{"0":692781056,"1":692781824},
"Last_IP_Number":
{"0":692781567,"1":692783103}
}
```

- `values`：使用简单的值数组格式。请参见 `Retrieve_Online_Visitor_10_values.json`。

```json
[
["BW","Gaborone",-24.6464,25.9119,692781056,692781567]
,
["BW","Gaborone",-24.6464,25.9119,692781824,692783103]
]
```

> **警告**
> 这种 JSON 格式会导致您丢失所有元数据，即列名和数据类型不会传递给后续处理环节。您必须在流程的每一步都重新映射和重命名所有数据项。

- `table`：使用格式 `{'schema': {schema}, 'data': {data}}`。请参见 `Retrieve_Online_Visitor_10_table.json`。

```json
{"schema":
{
"fields":
[
{"name":"index","type":"integer"},
{"name":"Country","type":"string"},
{"name":"Place_Name","type":"string"},
{"name":"Latitude","type":"number"},
{"name":"Longitude","type":"number"},
{"name":"First_IP_Number","type":"integer"},
{"name":"Last_IP_Number","type":"integer"}
]
,
"primaryKey":["index"],
"pandas_version":"0.20.0"},
"data":
[
{
"index":0,
"Country":"BW",
"Place_Name":"Gaborone",
"Latitude":-24.6464,
"Longitude":25.9119,
"First_IP_Number":692781056,
"Last_IP_Number":692781567
}
,
{
"index":1,
"Country":"BW",
"Place_Name":"Gaborone",
"Latitude":-24.6464,
"Longitude":25.9119,
"First_IP_Number":692781824,
"Last_IP_Number":692783103
}
]
}
```

> **注意**
> `table` JSON 格式会将完整的元数据传递给下一个数据处理环节。如果您有选择，我建议您始终支持这种 JSON 格式。

对于访客数据，我建议我们也考虑在数据检索步骤中使用 XML 格式。

> **警告**
> 在实际应用中，XML 可能具有复杂的结构。以下代码可以修改以处理更复杂的结构。这通常是一个耗时的探索过程。我建议您尝试使用 XML 包，我建议我们使用它来完成此任务。

以下是一个简单但有效的 XML 处理入门介绍。启动您的 Python 编辑器，在目录 `.\VKHCG\02-Krennwallner\01-Retrieve` 中创建一个名为 `Retrieve-Online-Visitor-XML.py` 的文本文件。

以下是您必须复制到文件中的 Python 代码：

```python
################################################################
# -*- coding: utf-8 -*-
################################################################
import os
import pandas as pd
import xml.etree.ElementTree as ET
################################################################
def df2xml(data):
    header = data.columns
    root = ET.Element('root')
    for row in range(data.shape[0]):
        entry = ET.SubElement(root, 'entry')
        for index in range(data.shape[1]):
            schild = str(header[index])
            child = ET.SubElement(entry, schild)
            if str(data[schild][row]) != 'nan':
                child.text = str(data[schild][row])
            else:
                child.text = 'n/a'
            entry.append(child)
    result = ET.tostring(root)
    return result
################################################################
def xml2df(xml_data):
    root = ET.XML(xml_data)
    all_records = []
    for i, child in enumerate(root):
        record = {}
        for subchild in child:
            record[subchild.tag] = subchild.text
        all_records.append(record)
    return pd.DataFrame(all_records)
################################################################
InputFileName = 'IP_DATA_ALL.csv'
OutputFileName = 'Retrieve_Online_Visitor.xml'
CompanyIn = '01-Vermeulen'
CompanyOut = '02-Krennwallner'
################################################################
if sys.platform == 'linux':
    Base = os.path.expanduser('~') + '/VKHCG'
else:
    Base = 'C:/VKHCG'
print('################################')
print('工作基目录 :', Base, ' 使用 ', sys.platform)
print('################################')
################################################################
sFileName = Base + '/' + CompanyIn + '/00-RawData/' + InputFileName
print('加载 :', sFileName)
IP_DATA_ALL = pd.read_csv(sFileName, header=0, low_memory=False)
IP_DATA_ALL.rename(columns={'Place Name': 'Place_Name'}, inplace=True)
IP_DATA_ALL.rename(columns={'First IP Number': 'First_IP_Number'}, inplace=True)
IP_DATA_ALL.rename(columns={'Last IP Number': 'Last_IP_Number'}, inplace=True)
IP_DATA_ALL.rename(columns={'Post Code': 'Post_Code'}, inplace=True)
################################################################
sFileDir = Base + '/' + CompanyOut + '/01-Retrieve/01-EDS/02-Python'
if not os.path.exists(sFileDir):
    os.makedirs(sFileDir)
visitordata = IP_DATA_ALL.head(10000)
print('原始子数据框')
print('行数 :', visitordata.shape[0])
print('列数 :', visitordata.shape[1])
print(visitordata)
print('导出 XML')
sXML = df2xml(visitordata)
sFileName = sFileDir + '/' + OutputFileName
file_out = open(sFileName, 'wb')
file_out.write(sXML)
file_out.close()
print('存储 XML:', sFileName)
xml_data = open(sFileName).read()
unxmlrawdata = xml2df(xml_data)
print('原始 XML 数据框')
print('行数 :', unxmlrawdata.shape[0])
print('列数 :', unxmlrawdata.shape[1])
print(unxmlrawdata)
unxmldata = unxmlrawdata.drop_duplicates(subset=None, keep="first", inplace=False)
print('去重后的 XML 数据框')
print('行数 :', unxmldata.shape[0])
print('列数 :', unxmldata.shape[1])
print(unxmldata)
################################################################
# print('### 完成!! ############################################')
################################################################
```

现在保存 Python 文件，并用你偏好的 Python 编译器执行 `Retrieve-Online-Visitor-XML.py`。你将在 `C:\VKHCG\02-Krennwallner\01-Retrieve\01-EDS\02-Python` 目录下看到一个名为 `Retrieve_Online_Visitor.xml` 的文件。这使你能在数据检索环节以 XML 格式交付数据。

我已经协助你使用多种格式来处理并传递处理管道各步骤间的数据。在本章的剩余部分，我仅演示其中一小部分。请记住：这些处理方法和技术适用于所有数据格式。那么，让我们看看 Hillman 的数据能为我们的知识库带来什么。

## Hillman 公司

该公司有四项主要工作需要你关注：

- **规划仓库位置**：Hillman 在英国拥有无数仓库，但由于财务困境，公司希望将仓库数量缩减 20%。

- **规划最佳国际物流的运输规则**：Hillman Global Logistics 一直承担从国际仓库向英国门店运输货物的费用，但这种模式已不可持续。联营门店现在希望在运输选项方面获得更高的可行性。

- **采用最优集装箱装箱方案**：Hillman 引入了一种新型三尺寸运输集装箱方案。它需要一套涵盖仓库、门店和客户的装箱解决方案。

- **创建配送路线**：Hillman 需要为每个仓库到门店预先规划配送路线，以实现 30% 的运输成本节约。

规划仓库位置。我将协助你检索仓库位置数据。

> **警告**
>
> 停下！该数据已加载完毕。

在本章前面部分，你已在 R 处理过程中完成了数据加载。该文件名为 `Retrieve_GB_Postcode_Warehouse.csv`，位于目录 `C:\VKHCG\03-Hillman\01-Retrieve\01-EDS\01-R` 中。我建议你定期更新数据映射矩阵（见第 4 章）。最新的数据映射矩阵能避免你为数据检索源重复编写新的解决方案。

### 规划最佳国际物流的运输规则

现在我将引导你完成运输规则的检索过程。是的，你必须理解业务，才能将其业务流程映射到数据发现中。

> **注**  
> 我不指望你能在第一次阅读时就掌握所有规则。但作为数据科学家，你必须充分理解业务，才能创建所需的数据集。

#### 重要运输信息

在运输领域，有一系列你必须首先了解的关键术语。

##### 运输条款

这些条款决定了运输的规则及条件。通常，它们会列在运输单据上。目前，Hillman 的所有运输都采用 DDP 条款。这并非我们所愿！我将在后续几页中详细讨论这一运输条款。

##### 卖方

在运输单据上实际发送产品的人/公司即为卖方。在我们的案例中，这可能是仓库、门店或客户。请注意，这不是一个地点，而是发送产品的法律实体。

##### 承运人

在运输单据上实际负责运输产品的人/公司即为承运人。请注意，这不是一个地点，而是运输产品的法律实体。

##### 港口

港口是指你必须离开或进入某个国家的任何地点。通常，这指海港或空港，但也可能包括陆路边境口岸。请注意，在整个流程中存在两个港口，这一点很重要：一个出口港和一个进口港。

##### 运输工具

运输工具是对货物实际运输方式的统称。它可以指货船、飞机、卡车甚至个人，但必须通过唯一的分配编号进行标识。

##### 转运站

转运站是货物在物理运输的下一阶段进行交接的实体地点。

##### 指定地点

这是货物所有权从卖方合法转移至买方的地点。这是整个流程中的一个特定位置。请记住这一点，因为它是物流行业许多法律纠纷的根源。

##### 买方

在运输单据上接收产品的人/公司即为买方。在我们的案例中，这可能是仓库、门店或客户。请注意，这不是一个地点，而是接收产品的法律实体。

通常，真实运输路线中的完整端到端流程由多条运输单据链组成。是的，你没看错，每条运输路线涉及不止一份运输单据！

### Incoterm 2010

首先，我们来了解一下标准的运输条款。以下是由标准委员会制定并发布为《2010 年国际贸易术语解释通则》的基本选项摘要。

它们以简单表格形式呈现如下：

| 运输术语 | 卖方 | 承运人 | 港口 | 船舶 | 港口 | 终端 | 指定地点 | 买方 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| EXW | 卖方 | 买方 | 买方 | 买方 | 买方 | 买方 | 买方 | 买方 |
| FCA | 卖方 | 卖方 | 买方 | 买方 | 买方 | 买方 | 买方 | 买方 |
| CPT | 卖方 | 卖方 | 买方 | 买方 | 买方 | 买方 | 买方 | 买方 |
| CIP | 卖方 | 卖方 | 保险方 | 保险方 | 保险方 | 保险方 | 保险方 | 买方 |
| DAT | 卖方 | 卖方 | 卖方 | 卖方 | 卖方 | 卖方 | 买方 | 买方 |
| DAP | 卖方 | 卖方 | 卖方 | 卖方 | 卖方 | 卖方 | 卖方 | 买方 |
| DDP | 卖方 | 卖方 | 卖方 | 卖方 | 卖方 | 卖方 | 卖方 | 卖方 |

如果你现在和我第一次看到这张表格时的感受一样——迷茫、孤独、急需一杯咖啡——请不要合上这本书。我将快速引导你从头到尾理解它。首先讨论的是运输条款。

##### EXW——工厂交货（指定交货地点）

根据此术语，卖方在其所在地或其他指定地点将货物交给买方处置。该术语对买方承担的义务最大，对卖方承担的义务最小。

以下是数据科学版的解释：如果我在当地书店购买《实用数据科学》并带回家，而该书店采用 EXW（工厂交货）方式发货，那么在我结账付款的那一刻，所有权就转移给了我。如果书出现任何问题，我都必须自费更换。

那么，让我们看看使用我们示例公司的数据科学揭示了什么。启动你的 Python 编辑器，并在目录 `.\VKHCG\03-Hillman\01-Retrieve` 中创建一个名为 `Retrieve-Incoterm-EXW.py` 的文本文件。以下是你必须复制到文件中的 Python 代码：

```python
################################################################
# -*- coding: utf-8 -*-
################################################################
import os
import pandas as pd
################################################################
IncoTerm='EXW'
InputFileName='Incoterm_2010.csv'
OutputFileName='Retrieve_Incoterm_' + IncoTerm + '_RuleSet.csv'
Company='03-Hillman'
################################################################
if sys.platform == 'linux':
Base=os.path.expanduser('~') + '/VKHCG'
else:
Base='C:/VKHCG'
print('################################')
print('Working Base :',Base, ' using ', sys.platform)
print('################################')
################################################################
sFileDir=Base + '/' + Company + '/01-Retrieve/01-EDS/02-Python'
if not os.path.exists(sFileDir):
os.makedirs(sFileDir)
################################################################
### Import Incoterms
################################################################
sFileName=Base + '/' + Company + '/00-RawData/' + InputFileName
print('###########')
print('Loading :',sFileName)
IncotermGrid=pd.read_csv(sFileName,header=0,low_memory=False)
IncotermRule=IncotermGrid[IncotermGrid.Shipping_Term == IncoTerm]
print('Rows :',IncotermRule.shape[0])
print('Columns :',IncotermRule.shape[1])
print('###########')
print(IncotermRule)
################################################################
sFileName=sFileDir + '/' + OutputFileName
IncotermRule.to_csv(sFileName, index = False)
################################################################
print('### Done!! ############################################')
################################################################
```

现在保存 Python 文件，并使用你喜欢的 Python 编译器执行 `Retrieve-Incoterm-EXW.py` 文件。你将会在 `C:\VKHCG\03-Hillman\01-Retrieve\01-EDS\02-Python` 中看到一个名为 `Retrieve_Incoterm_EXW.csv` 的文件。打开此文件，你应该会看到如下数据集：

| 运输术语 | 卖方 | 承运人 | 港口 | 船舶 | 港口 | 终端 | 指定地点 | 买方 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |

| EXW | 卖方 | 买方 | 买方 | 买方 | 买方 | 买方 | 买方 | 买方 |

**注意：** 所有权在供应链中过早转移。所有成本和风险都由买方承担。

## FCA——货交承运人（指定交货地点）

在此条件下，卖方在指定地点将已办妥出口清关的货物交付给买方指定的承运人。以下是数据科学版的解释。

如果我在海外免税店购买《实用数据科学》，然后在带回家之前到免税柜台取货，而该商店采用 FCA（货交承运人）方式将货物发至免税柜台，那么在我结账付款的那一刻，所有权就转移给了我。但是，如果书在从商店到免税柜台之间发生任何问题，商店必须赔偿。只有在我在柜台取货后，如果发生任何问题，才需要由我负责。因此，当我拿到书的那一刻，交易就变成了 EXW 模式，所以我需要在抵达我的国家时支付任何必要的进口关税。

让我们看看数据科学发现了什么。启动你的 Python 编辑器，并在目录 `.\VKHCG\03-Hillman\01-Retrieve` 中创建一个名为 `Retrieve-Incoterm-FCA.py` 的文本文件。以下是你必须复制到文件中的 Python 代码：

```python
################################################################
# -*- coding: utf-8 -*-
################################################################
import os
import pandas as pd
################################################################
IncoTerm='FCA'
InputFileName='Incoterm_2010.csv'
OutputFileName='Retrieve_Incoterm_' + IncoTerm + '_RuleSet.csv'
Company='03-Hillman'
################################################################
if sys.platform == 'linux':
    Base=os.path.expanduser('~') + '/VKHCG'
else:
    Base='C:/VKHCG'
print('################################')
print('Working Base :',Base, ' using ', sys.platform)
print('################################')
################################################################
sFileDir=Base + '/' + Company + '/01-Retrieve/01-EDS/02-Python'
if not os.path.exists(sFileDir):
    os.makedirs(sFileDir)
################################################################
### Import Incoterms
################################################################
sFileName=Base + '/' + Company + '/00-RawData/' + InputFileName
print('###########')
print('Loading :',sFileName)
IncotermGrid=pd.read_csv(sFileName,header=0,low_memory=False)
IncotermRule=IncotermGrid[IncotermGrid.Shipping_Term == IncoTerm]
print('Rows :',IncotermRule.shape[0])
print('Columns :',IncotermRule.shape[1])
print('###########')
print(IncotermRule)
################################################################
sFileName=sFileDir + '/' + OutputFileName
IncotermRule.to_csv(sFileName, index = False)
################################################################
print('### Done!! ############################################')
################################################################
```

现在保存 Python 文件，并使用你喜欢的 Python 编译器执行 `Retrieve-Incoterm-FCA.py` 文件。你将会在 `C:\VKHCG\03-Hillman\01-Retrieve\01-EDS\02-Python` 中看到一个名为 `Retrieve_Incoterm_FCA.csv` 的文件。打开此文件，你应该会看到如下数据集：

| 运输术语 | 卖方 | 承运人 | 港口 | 船舶 | 港口 | 终端 | 指定地点 | 买方 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| FCA | 卖方 | 卖方 | 买方 | 买方 | 买方 | 买方 | 买方 | 买方 |

**注意：** 所有权在供应链的港口处过早转移。大部分风险和成本仍然由买方承担。

## CPT——运费付至（指定目的地）

在该术语下，卖方支付将货物运至指定目的地的运费。但当货物被交给第一承运人时，即视为已交货，因此风险在出口国装运地将货物交给承运人时转移给买方。卖方负责原产地费用，包括出口清关费用和运至指定目的地的运费。（此处目的地可能是最终目的地，如买方工厂，或目的港。但这必须由买卖双方共同商定。）

现在来看数据科学的版本：如果我在一家海外书店购买《实用数据科学》一书，然后在出口处取书带回家，并且书店免费将书以 CPT（运费付至）的方式运至关税服务台，在我付款的那一刻，所有权就转移给了我。但是，如果在从书店到书店关税服务台的途中书本发生任何意外，我都必须自行承担。只有当我从服务台取书后，如果发生任何意外我才需要赔付。所以，在我取书的那一刻，交易就变成了 EXW（工厂交货），因此我必须支付任何抵达我祖国时所需的出口和进口关税。

我们来看看数据科学如何处理。打开你的 Python 编辑器，在目录 `.\VKHCG\03-Hillman\01-Retrieve` 下创建一个名为 `Retrieve-Incoterm-CPT.py` 的文本文件。以下是必须复制到该文件中的 Python 代码：

# 国际贸易术语数据处理

## 概述与 CPT 术语实现

```python
################################################################
# -*- coding: utf-8 -*-
################################################################
import os
import pandas as pd
################################################################
IncoTerm='CPT'
InputFileName='Incoterm_2010.csv'
OutputFileName='Retrieve_Incoterm_' + IncoTerm + '_RuleSet.csv'
Company='03-Hillman'
################################################################
if sys.platform == 'linux':
    Base=os.path.expanduser('~') + '/VKHCG'
else:
    Base='C:/VKHCG'
print('################################')
print('Working Base :',Base, ' using ', sys.platform)
print('################################')
################################################################
################################################################
sFileDir=Base + '/' + Company + '/01-Retrieve/01-EDS/02-Python'
if not os.path.exists(sFileDir):
    os.makedirs(sFileDir)
################################################################
### Import Incoterms
################################################################
sFileName=Base + '/' + Company + '/00-RawData/' + InputFileName
print('###########')
print('Loading :',sFileName)
IncotermGrid=pd.read_csv(sFileName,header=0,low_memory=False)
IncotermRule=IncotermGrid[IncotermGrid.Shipping_Term == IncoTerm]
print('Rows :',IncotermRule.shape[0])
print('Columns :',IncotermRule.shape[1])
print('###########')
print(IncotermRule)
################################################################
sFileName=sFileDir + '/' + OutputFileName
IncotermRule.to_csv(sFileName, index = False)
################################################################
print('### Done!! ############################################')
################################################################
```

现在保存该 Python 文件，并用你喜欢的 Python 编译器执行 `Retrieve-Incoterm-CPT.py` 文件。你会在 `C:\VKHCG\03-Hillman\01-Retrieve\01-EDS\02-Python` 目录下看到一个名为 `Retrieve_Incoterm_CPT.csv` 的文件。打开这个文件，你应该会看到类似如下的数据集：

| 运输条款 | 卖方 | 承运人 | 港口 | 船舶 | 港口 | 码头 | 指定地点 | 买方 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| CPT | 卖方 | 卖方 | 买方 | 买方 | 买方 | 买方 | 买方 | 买方 |

### 注意

所有权在供应链中的港口过早转移。大部分风险和成本由买方承担。如果我们是买方，这很糟糕；但如果我们是卖方，则很有利。

## CIP——运费和保险费付至（指定目的地）

该术语通常与前面的 CPT 相似，区别在于卖方需要为运输途中的货物购买保险。以下是数据科学版本。

如果我在一家海外书店购买《实用数据科学》一书，然后在出口处取书带回家，并且书店免费将书以 CPT（运费付至）的方式运至关税服务台，在我付款的那一刻，所有权就转移给了我。但是，如果从书店到书店关税服务台的途中发生任何意外，我必须购买保险来支付损失。只有当我从服务台取书后，如果发生任何意外我才需要赔付。因此，在我取书的那一刻，交易就变成了 EXW（工厂交货），所以我必须支付任何抵达我祖国时所需的出口和进口关税。请注意，保险只覆盖从书店到关税服务台这段交易。

我们来看看数据科学如何处理。打开你的 Python 编辑器，在目录 `.\VKHCG\03-Hillman\01-Retrieve` 下创建一个名为 `Retrieve-Incoterm-CIP.py` 的文本文件。以下是必须复制到该文件中的 Python 代码：

# DAT——终点站交货（指定目的港或目的地）

```python
################################################################
# -*- coding: utf-8 -*-
################################################################
import os
import pandas as pd
################################################################
IncoTerm='CIP'
InputFileName='Incoterm_2010.csv'
OutputFileName='Retrieve_Incoterm_' + IncoTerm + '_RuleSet.csv'
Company='03-Hillman'
################################################################
if sys.platform == 'linux':
    Base=os.path.expanduser('~') + '/VKHCG'
else:
    Base='C:/VKHCG'
print('################################')
print('Working Base :',Base, ' using ', sys.platform)
print('################################')
################################################################
sFileDir=Base + '/' + Company + '/01-Retrieve/01-EDS/02-Python'
if not os.path.exists(sFileDir):
    os.makedirs(sFileDir)
################################################################
### Import Incoterms
################################################################
sFileName=Base + '/' + Company + '/00-RawData/' + InputFileName
print('###########')
print('Loading :',sFileName)
IncotermGrid=pd.read_csv(sFileName,header=0,low_memory=False)
IncotermRule=IncotermGrid[IncotermGrid.Shipping_Term == IncoTerm]
print('Rows :',IncotermRule.shape[0])
print('Columns :',IncotermRule.shape[1])
print('###########')
print(IncotermRule)
################################################################
sFileName=sFileDir + '/' + OutputFileName
IncotermRule.to_csv(sFileName, index = False)
################################################################
print('### Done!! ############################################')
################################################################
```

现在保存该 Python 文件，并用你喜欢的 Python 编译器执行 `Retrieve-Incoterm-CIP.py` 文件。你会在 `C:\VKHCG\03-Hillman\01-Retrieve\01-EDS\02-Python` 目录下看到一个名为 `Retrieve_Incoterm_CIP.csv` 的文件。打开这个文件，你应该会看到类似如下的数据集：

| 运输条款 | 卖方 | 承运人 | 港口 | 船舶 | 港口 | 码头 | 指定地点 | 买方 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| CIP | 卖方 | 卖方 | 保险方 | 保险方 | 保险方 | 保险方 | 保险方 | 买方 |

## 注意

所有权在供应链中的港口过早转移。大部分风险和成本由买方承担，部分由保险覆盖。与房屋或汽车保险计算的相比，风险较低。如果你是买方，这不算太糟；但如果你是卖方，就不太理想了！

该国际贸易术语要求卖方将货物卸载至指定终点站。卖方承担所有运输费用（出口费用、运费、目的港卸货费及目的港杂费），并承担货物抵达目的港或终点站前的所有风险。

终点站可以是港口、机场或内陆货运枢纽，但必须是有能力接收货物的设施。如果卖方无法安排卸货，应考虑改用 DAP 条款。卸货后的所有费用（例如进口关税、税费、报关费和后续运输费）均由买方承担。

以下是数据科学版的解释。假如我在海外购买《实用数据科学》，然后到本地书店自提回家，而海外书店采用 DAT（本地书店）方式发货——当我付款时，所有权就转移给我。但是，如果书在付款后到提货前出了任何问题，由本地书店赔偿。这本书只在本地书店提取一次。如果出了任何问题，由我负责。所以，一旦我取走书，交易就变成了 EXW，因此我需要支付任何进口关税。

让我们看看数据科学是怎么做的。启动你的 Python 编辑器，在目录 `.\VKHCG\03-Hillman\01-Retrieve` 中创建一个名为 `Retrieve-Incoterm-DAT.py` 的文本文件。以下是需要复制到文件中的 Python 代码：

```
################################################################
# -*- coding: utf-8 -*-
################################################################
import os
import pandas as pd
################################################################
IncoTerm='DAT'
InputFileName='Incoterm_2010.csv'
OutputFileName='Retrieve_Incoterm_' + IncoTerm + '_RuleSet.csv'
Company='03-Hillman'
################################################################
if sys.platform == 'linux':
Base=os.path.expanduser('~') + '/VKHCG'
else:
Base='C:/VKHCG'
print('################################')
print('Working Base :',Base, ' using ', sys.platform)
print('################################')
################################################################
sFileDir=Base + '/' + Company + '/01-Retrieve/01-EDS/02-Python'
if not os.path.exists(sFileDir):
os.makedirs(sFileDir)
################################################################
### Import Incoterms
################################################################
sFileName=Base + '/' + Company + '/00-RawData/' + InputFileName
print('###########')
print('Loading :',sFileName)
IncotermGrid=pd.read_csv(sFileName,header=0,low_memory=False)
IncotermRule=IncotermGrid[IncotermGrid.Shipping_Term == IncoTerm]
print('Rows :',IncotermRule.shape[0])
print('Columns :',IncotermRule.shape[1])
print('###########')
print(IncotermRule)
################################################################
sFileName=sFileDir + '/' + OutputFileName
IncotermRule.to_csv(sFileName, index = False)
################################################################
print('### Done!! ############################################')
################################################################
```

现在保存 Python 文件，并用你偏好的 Python 编译器执行 `Retrieve-Incoterm-DAT.py` 文件。你将看到在 `C:\VKHCG\03-Hillman\01-Retrieve\01-EDS\02-Python` 中生成一个名为 `Retrieve_Incoterm_DAT.csv` 的文件。打开这个文件，你应该会看到如下数据集：

| 运输条款 | 卖方 | 承运人 | 港口 | 船舶 | 港口 | 终点站 | 指定地点 | 买方 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| DAT | 卖方 | 卖方 | 卖方 | 卖方 | 卖方 | 卖方 | 买方 | 买方 |

**注意**：现在，所有权在供应链中较晚的指定地点转移。大部分风险和成本由卖方承担。如果你在购买，这是有利的；如果你在销售，则反之。

## DAP——目的地交货（指定目的地）

根据《2010 年国际贸易术语解释通则》的定义，DAP——目的地交货——是指当货物已置于抵达的运输工具上、准备在指定目的地卸载时，卖方即完成交付，货物交由买方处置。在 DAP 条款下，风险在交货合同中指定的目的地从卖方转移给买方。

一旦货物备妥待运，卖方自费进行必要的包装，以确保货物安全抵达最终目的地。卖方自担风险和费用，完成出口国所有必要的法律手续，办理货物出口清关。

货物抵达目的国后，进口清关必须由买方自担风险和费用完成，包括所有关税和税费。但是，与 DAT 条款一样，任何延迟或滞期费均由卖方承担。

在 DAP 条款下，所有运输费用以及任何终点站费用均由卖方支付，直至约定的目的地。在 DAP 条款下，最终目的地的必要卸货费用必须由买方承担。

以下是数据科学版的解释。假如我从海外网站购买 100 本《实用数据科学》，然后到本地书店自提回家，而书店采用 DAP（本地书店）方式发货——当我付款时，所有权就转移给我。但是，如果书在付款后到提货前出了任何问题，由网站所有者赔偿。一旦在本地书店提货，我需要自行承担在书店拆包的费用。所以，一旦我取走书，交易就变成了 EXW，因此我将需要承担取书后的所有费用。

让我们看看数据科学是怎么做的。启动你的 Python 编辑器，在目录 `.\VKHCG\03-Hillman\01-Retrieve` 中创建一个名为 `Retrieve-Incoterm-DAP.py` 的文本文件。以下是需要复制到文件中的 Python 代码：

```python
# (此处应为Retrieve-Incoterm-DAP.py的完整Python代码)
```

# DAP—所在地交货（指定目的地）

根据此术语，卖方在指定目的地将货物交付给买方，此时货物已准备好卸载。所有权在目的地交付时转移给买方。卖方承担将货物运至指定目的地的所有风险和成本。

### 示例代码与数据集

以下是完整的 Python 代码示例，用于检索 DAP 术语数据：

```python
################################################################
# -*- coding: utf-8 -*-
################################################################
import os
import pandas as pd
################################################################
IncoTerm='DAP'
InputFileName='Incoterm_2010.csv'
OutputFileName='Retrieve_Incoterm_' + IncoTerm + '_RuleSet.csv'
Company='03-Hillman'
################################################################
if sys.platform == 'linux':
Base=os.path.expanduser('~') + '/VKHCG'
else:
Base='C:/VKHCG'
print('################################')
print('Working Base :',Base, ' using ', sys.platform)
print('################################')
################################################################
sFileDir=Base + '/' + Company + '/01-Retrieve/01-EDS/02-Python'
if not os.path.exists(sFileDir):
os.makedirs(sFileDir)
################################################################
### Import Incoterms
################################################################
sFileName=Base + '/' + Company + '/00-RawData/' + InputFileName
print('###########')
print('Loading :',sFileName)
IncotermGrid=pd.read_csv(sFileName,header=0,low_memory=False)
IncotermRule=IncotermGrid[IncotermGrid.Shipping_Term == IncoTerm]
print('Rows :',IncotermRule.shape[0])
print('Columns :',IncotermRule.shape[1])
print('###########')
print(IncotermRule)
################################################################
sFileName=sFileDir + '/' + OutputFileName
IncotermRule.to_csv(sFileName, index = False)
################################################################
print('### Done!! ############################################')
################################################################
```

现在保存 Python 文件。

执行`Retrieve-Incoterm-DAP.py`文件，使用你偏好的 Python 编译器。你会在`C:\VKHCG\03-Hillman\01-Retrieve\01-EDS\02-Python`目录下看到一个名为`Retrieve_Incoterm_DAP.csv`的文件。打开该文件，你应看到如下数据集：

| 运输术语 | 卖方 | 承运人 | 港口 | 船舶 | 港口 | 码头 | 指定地点 | 买方 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| DAP | 卖方 | 卖方 | 卖方 | 卖方 | 卖方 | 卖方 | 卖方 | 买方 |

## 注意

此时，所有权在供应链中的买方所在地点之后才发生转移。所有的风险和成本都由卖方承担。如果你是买方，这非常有利；但如果你是卖方，则非常不利。你将需要对运输过程中的任何意外负责。你唯一不需要承担的部分是卸货。

# DDP—完税后交货（指定目的地）

根据此术语，卖方负责将货物运至买方所在国的指定地点，并支付将货物运至目的地所产生的一切成本，包括进口关税和税费。卖方不负责卸货。该术语将最大义务置于卖方，最小义务置于买方。在货物于指定目的地交付之前，没有任何风险或责任转移给买方。

关于 DDP 条款，最重要的考量是卖方负责在买方所在国办理货物清关手续，包括支付关税和税费，以及从该国当局获取必要的授权和注册。

以下是数据科学版本的类比。如果我在一个海外网站上购买 100 本《Practical Data Science》，然后在将它们带回家之前，先到当地书店自提，并且书店采用 DDP（完税后交货，送到我家）的方式发货——那么在我付款的那一刻，所有权就转移给了我。然而，如果从付款到送货到我家这段时间内，书籍发生任何意外，书店必须更换书籍，因为此条款涵盖了送到我家的运输过程。

让我们看看数据科学的结果。启动你的 Python 编辑器，在目录`.\VKHCG\03-Hillman\01-Retrieve`中创建一个名为`Retrieve-Incoterm-DDP.py`的文本文件。以下是需要复制到该文件中的 Python 代码：

### Python 脚本：`Retrieve-Incoterm-DDP.py`

```python
# -*- coding: utf-8 -*-
import os
import pandas as pd

IncoTerm = 'DDP'
InputFileName = 'Incoterm_2010.csv'
OutputFileName = 'Retrieve_Incoterm_' + IncoTerm + '_RuleSet.csv'
Company = '03-Hillman'

if sys.platform == 'linux':
    Base = os.path.expanduser('~') + '/VKHCG'
else:
    Base = 'C:/VKHCG'

print('################################')
print('Working Base :', Base, ' using ', sys.platform)
print('################################')

sFileDir = Base + '/' + Company + '/01-Retrieve/01-EDS/02-Python'
if not os.path.exists(sFileDir):
    os.makedirs(sFileDir)

### Import Incoterms
sFileName = Base + '/' + Company + '/00-RawData/' + InputFileName
print('###########')
print('Loading :', sFileName)
IncotermGrid = pd.read_csv(sFileName, header=0, low_memory=False)
IncotermRule = IncotermGrid[IncotermGrid.Shipping_Term == IncoTerm]
print('Rows :', IncotermRule.shape[0])
print('Columns :', IncotermRule.shape[1])
print('###########')
print(IncotermRule)

sFileName = sFileDir + '/' + OutputFileName
IncotermRule.to_csv(sFileName, index=False)

print('### Done!! ############################################')
```

现在保存 Python 文件，并使用你偏好的 Python 编译器执行`Retrieve-Incoterm-DDP.py`文件。你会在`C:\VKHCG\03-Hillman\01-Retrieve\01-EDS\02-Python`目录下看到一个名为`Retrieve_Incoterm_DDP.csv`的文件。打开该文件，你应看到如下数据集：

| 运输术语 | 卖方 | 承运人 | 港口 | 船舶 | 港口 | 码头 | 指定地点 | 买方 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| DDP | 卖方 | 卖方 | 卖方 | 卖方 | 卖方 | 卖方 | 卖方 | 卖方 |

### 注意

此时，所有权在供应链中的买方所在地点之后才发生转移。所有的风险和成本都由卖方承担。如果你是买方，这非常有利；但如果你是卖方，则非常不利。你将需要对运输过程中的任何意外负责。

### 运输链

该代码适用于运输路线上的任意两点之间。根据买方和卖方之间的协议，这些链条可以包含众多不同的国际贸易术语选项。

### 警告

可能存在多条运输链。从法律上讲，有无数种组合和排列是可能的。

### 咨询专家

因此，在处理复杂业务流程时，我有一条重要的经验之谈：找到该领域的专家。尽可能让具有领域知识的专家参与到流程中至关重要。

在下面的场景中，我将扮演专家 Maximillian Logistics 先生的角色。Max 建议你：

- 停止到处使用 DDP 发货。
- 仅在仓库之间使用 CIP 发货。
- 在仓库和商店之间使用 CIP 或 DDP 发货。
- 在商店之间使用 CIP 或 EXW 发货。
- 使用 EXW 向客户发货。

Max 确认 Hillman 的政策，并指出当前情况如下：

- 运输成本为每公里 10 英镑。
- 保险成本为每公里 5 英镑。
- 进出口关税为每件商品 2 英镑的固定费率。
- 只能从最近的三个仓库向商店发货。
- 只能在同一个国家的商店之间发货，即商店仅限在同一国家内。
- 只能从最近的国内商店向客户发货。
- 不能向距离超过 20 公里的客户发货。

作为检索超步骤级别的数据科学家，这对你意味着什么？

- 你必须知道仓库的位置。在本章前面，你已经在 R 处理过程中创建了该加载文件。这是目录`..\VKHCG\03-Hillman\01-Retrieve\01-EDS\01-R`下名为`Retrieve_GB_Postcode_Warehouse.csv`的文件。因此，该数据已经准备就绪。
- 你必须知道英国商店的位置。在本章前面，你已经在 R 处理过程中创建了该加载文件。文件名为`Retrieve_GB_Postcodes_Shops.csv`，位于目录`..\VKHCG\03-Hillman\01-Retrieve\01-EDS\01-R`中。因此，该数据已经准备就绪。
- 你必须知道客户的位置。在本章前面，你已经在 R 处理过程中创建了该加载文件。这是目录`..\VKHCG\01-Vermeulen\01-Retrieve\01-EDS\01-R`下名为`Retrieve_IP_DATA.csv`的文件。因此，所需数据已经准备就绪。

Max（也就是我）的其余建议将在第 8 章至第 11 章中使用，用于制定所需信息，以实现对物流流程的认知。

# 可能的运输路线

公司可选的潜在运输路线数量众多。`retrieve`（检索）步骤可通过路线组合生成器产生潜在路线集。这将为你提供一组路线，但你极不可能沿所有路线进行运输。它仅仅是数据科学用于寻找最优解的一组候选路线。

启动你的 Python 编辑器，在目录`.\VKHCG\03-Hillman\01-Retrieve`中创建一个名为`Retrieve-Warehouse-Incoterm-Chains.py`的文本文件。以下是需复制到文件中的 Python 代码：

```python
################################################################
# -*- coding: utf-8 -*-
################################################################
import os
import pandas as pd
from math import radians, cos, sin, asin, sqrt
################################################################
```

在导航学中，半正矢公式（`haversine` formula）可根据两点的经纬度计算球面上两点间的大圆距离。以下是我实现该公式的代码：

```python
def haversine(lon1, lat1, lon2, lat2, stype):
    """
    计算地球表面两个给定 GPS 点之间的大圆距离（以十进制度数指定）
    """
    ### 将十进制度数转换为弧度
    lon1, lat1, lon2, lat2 = map(radians, [lon1, lat1, lon2, lat2])
    ### 半正矢公式
    dlon = lon2 - lon1
    dlat = lat2 - lat1
    a = sin(dlat/2)**2 + cos(lat1) * cos(lat2) * sin(dlon/2)**2
    c = 2 * asin(sqrt(a))
    if stype == 'km':
        r = 6371 # 地球半径，单位为千米
    else:
        r = 3956 # 地球半径，单位为英里
    d = round(c * r, 3)
    return d
```

既然你已经掌握了这个公式，我建议你加载以下数据：

```python
################################################################
InputFileName='GB_Postcode_Warehouse.csv'
OutputFileName='Retrieve_Incoterm_Chain_GB_Warehouse.csv'
Company='03-Hillman'
################################################################
if sys.platform == 'linux':
    Base=os.path.expanduser('~') + '/VKHCG'
else:
    Base='C:/VKHCG'
print('################################')
print('Working Base :',Base, ' using ', sys.platform)
print('################################')
################################################################
sFileDir=Base + '/' + Company + '/01-Retrieve/01-EDS/02-Python'
if not os.path.exists(sFileDir):
    os.makedirs(sFileDir)
```

我们需要仓库的位置信息。这些信息非常重要，因为它们决定了产品应该从哪里运送到哪里。

```python
################################################################
##### 导入仓库数据
################################################################
sFileName=Base + '/' + Company + '/00-RawData/' + InputFileName
print('###########')
print('Loading :',sFileName)
Warehouse=pd.read_csv(sFileName,header=0,low_memory=False)
```

现在，你需要选择包含已知位置的全部有效仓库列表。还记得关于处理缺失数据的讨论吗？在这种情况下，我们只是简单地将未知数据移除。

```python
WarehouseGood=Warehouse[Warehouse.latitude != 0]
```

你的仓库命名格式为 `WH-` 加上邮政编码。这是一个标准模式，请按如下方式将其应用于数据：

```python
WarehouseGood['Warehouse_Name']=WarehouseGood.apply(lambda row:
    'WH-' + row['postcode']
    ,axis=1)
```

数据集中存在不相关的数据。请用以下代码将其移除：

```python
WarehouseGood.drop('id', axis=1, inplace=True)
WarehouseGood.drop('postcode', axis=1, inplace=True)
```

现在，我们将仓库数量限制为 100 个。这纯粹是为了加快示例的进度。你可以尝试不同数量的仓库，以观察选择数量带来的影响。

```python
################################################################
WarehouseFrom=WarehouseGood.head(100)
################################################################
```

接下来，你将构建两个数据集来模拟仓库间的运输。任何仓库都可以向其他任何仓库发货。

```python
################################################################
for i in range(WarehouseFrom.shape[1]):
    oldColumn=WarehouseFrom.columns[i]
    newColumn=oldColumn + '_from'
    WarehouseFrom.rename(columns={oldColumn: newColumn}, inplace=True)
    WarehouseFrom.insert(3,'Keys', 1)
################################################################
WarehouseTo=WarehouseGood.head(100)
for i in range(WarehouseTo.shape[1]):
    oldColumn=WarehouseTo.columns[i]
    newColumn=oldColumn + '_to'
    WarehouseTo.rename(columns={oldColumn: newColumn}, inplace=True)
    WarehouseTo.insert(3,'Keys', 1)
################################################################
WarehouseCross=pd.merge(right=WarehouseFrom,
    left=WarehouseTo,
    how='outer',
    on='Keys')
```

恭喜！你已成功为仓库内部运输构建了一个矩阵模型。现在，你应该移除用于连接的多余键。

```python
WarehouseCross.drop('Keys', axis=1, inplace=True)
```

对于这些运输，你的贸易术语（`Incoterm`）是 `DDP`。你还记得 `DDP` 运输的含义吗？它表示卖方仓库承担所有费用。

```python
WarehouseCross.insert(0,'Incoterm', 'DDP')
```

现在，你将使用距离公式来计算运输所涉及的两个仓库之间的距离（以千米为单位）。

```python
WarehouseCross['DistanceBetweenKilometers'] = WarehouseCross.apply(lambda row:
    haversine(
        row['longitude_from'],
        row['latitude_from'],
        row['longitude_to'],
        row['latitude_to'],
        'km')
    ,axis=1)
```

你还需要知道英里制的距离。拥有两种距离单位便于根据成本模型选择使用哪一种。始终提供更多特征，因为这将丰富你的结果。

```
WarehouseCross['DistanceBetweenMiles'] = WarehouseCross.apply(lambda row:
haversine(
row['longitude_from'],
row['latitude_from'],
row['longitude_to'],
row['latitude_to'],
'miles')
,axis=1)
```

清理模型中现在多余的字段。

```
WarehouseCross.drop('longitude_from', axis=1, inplace=True)
WarehouseCross.drop('latitude_from', axis=1, inplace=True)
WarehouseCross.drop('longitude_to', axis=1, inplace=True)
WarehouseCross.drop('latitude_to', axis=1, inplace=True)
```

在验证检查中，我们发现模型支持了现实生态系统中不存在的同一仓库内部运输。因此，请将其从你的模拟模型中移除。

```
WarehouseCrossClean=WarehouseCross[WarehouseCross.DistanceBetweenKilometers !=0]
```

恭喜！你已成功构建了一个产品运输模型。现在，你可以对模型进行一些快速的顶层检查，并将其保存到磁盘。

```
print('###########')
print('Rows :',WarehouseCrossClean.shape[0])
print('Columns :',WarehouseCrossClean.shape[1])
print('###########')
################################################################
sFileName=sFileDir + '/' + OutputFileName
WarehouseCrossClean.to_csv(sFileName, index = False)
################################################################
print('### Done!! ############################################')
################################################################
```

现在保存这个 Python 文件。

使用你偏好的 Python 编译器执行 `Retrieve-Warehouse-Incoterm-Chains.py` 文件。

你已经端到端地运行了模型。干得好！你将会在 `C:\VKHCG\03-Hillman\01-Retrieve\01-EDS\02-Python` 目录下看到一个名为 `Retrieve_Incoterm_Chain_GB_Warehouse.csv` 的文件。打开该文件，你应该会看到类似于以下内容的数据集：

| Incoterm 贸易术语 | Warehouse_Name_to 目标仓库名称 | Warehouse_Name_from 始发仓库名称 | Distance Between Kilometers 千米距离 | Distance Between Miles 英里距离 |
| --- | --- | --- | --- | --- |
| DDP | WH-AB10 | WH-AB11 | 1.644 | 1.021 |
| DDP | WH-AB10 | WH-AB12 | 3.818 | 2.371 |
| DDP | WH-AB10 | WH-AB13 | 7.872 | 4.888 |
| DDP | WH-AB10 | WH-AB14 | 10.02 | 6.222 |

我需要指出，当前数据仍然将产品标记为 DDP 运输。你将在本书的指导下，运用新的数据科学技能来解决这个问题。

### 采用新型运输集装箱

要采用最佳的集装箱运输包装方案，我需要引入一个新概念。集装箱运输基于这样一个概念：将使用的包装减少到一组符合以下要求的最优尺寸：

- 产品必须能放入由立方体四个侧面构成的盒子内。
- 产品可以使用包装泡沫固定，泡沫会填充包装内的所有空隙。
- 包装必须能零空隙地装入运输集装箱。
- 集装箱只能装运发往单个仓库、商店或客户的产品。

**注意**：由于这对 Hillman 来说是一个新概念，您需要生成数据供您的数据科学团队使用。

因此，我将建议一组精心设计的生成数据，作为真实数据的可接受替代方案，以便按照您期望的未来工作方式来构建模型。不过，一旦您开始收集业务流程的结果，您就可以随时将生成数据替换为真实的生产数据。

现在，我将指导您为数据科学团队制定一组种子数据。启动您的 Python 编辑器，在目录`.\VKHCG\03-Hillman\01-Retrieve`中创建一个名为`Retrieve-Container-Plan.py`的文本文件。以下是您必须复制到文件中的 Python 代码。

**注意**：请跟随后续代码输入。我将根据需要讨论编码的重要部分。

```
################################################################
# -*- coding: utf-8 -*-
################################################################
import os
import pandas as pd
################################################################
ContainerFileName='Retrieve_Container.csv'
BoxFileName='Retrieve_Box.csv'
ProductFileName='Retrieve_Product.csv'
Company='03-Hillman'
################################################################
if sys.platform == 'linux':
Base=os.path.expanduser('~') + '/VKHCG'
else:
Base='C:/VKHCG'
print('################################')
print('Working Base :',Base, ' using ', sys.platform)
print('################################') ################################################################
sFileDir=Base + '/' + Company + '/01-Retrieve/01-EDS/02-Python'
if not os.path.exists(sFileDir):
os.makedirs(sFileDir)
################################################################
### Create the Containers
################################################################
```

业务需求是一个能够模拟尺寸从 1 米 × 1 米 × 1 米到 21 米 × 10 米 × 6 米的运输集装箱的模型。那么，我们来模拟这些尺寸。

## 集装箱模拟模型

```python
containerLength=range(1,21)
containerWidth=range(1,10)
containerHeigth=range(1,6)
containerStep=1
c=0
for l in containerLength:
    for w in containerWidth:
        for h in containerHeigth:
            containerVolume=(l/containerStep)*(w/containerStep)*(h/containerStep)
            c=c+1
            ContainerLine=[('ShipType', ['Container']),
                           ('UnitNumber', ('C'+format(c,"06d"))),
                           ('Length',(format(round(l,3),".4f"))),
                           ('Width',(format(round(w,3),".4f"))),
                           ('Height',(format(round(h,3),".4f"))),
                           ('ContainerVolume',(format(round(containerVolume,6),".6f")))]
            if c==1:
                ContainerFrame = pd.DataFrame.from_items(ContainerLine)
            else:
                ContainerRow = pd.DataFrame.from_items(ContainerLine)
                ContainerFrame = ContainerFrame.append(ContainerRow)
            ContainerFrame.index.name = 'IDNumber'
print('################')
print('## Container')
print('################')
print('Rows :',ContainerFrame.shape[0])
print('Columns :',ContainerFrame.shape[1])
print('################')
################################################################
sFileContainerName=sFileDir + '/' + ContainerFileName
ContainerFrame.to_csv(sFileContainerName, index = False)
################################################################
```

干得漂亮！您刚刚根据未来系统的需求完成了一个模拟模型。因此，当“真实”集装箱数据到来时，您只需用真实数据替换这个模拟数据集，下游的数据科学工作都不需要更改。

## 纸板箱模拟模型

您的第二个模拟是针对产品包装用的纸板箱。需求是尺寸从 100 厘米 × 100 厘米 × 100 厘米到 2.1 米 × 2.1 米 × 2.1 米的箱子。您还可以使用 0 到 600 厘米的包装泡沫来固定箱子内的任何产品。

您可以按如下方式模拟这些需求：

```python
################################################################
## Create valid Boxes with packing foam
################################################################
boxLength=range(1,21)
boxWidth=range(1,21)
boxHeigth=range(1,21)
packThick=range(0,6)
boxStep=10
b=0
for l in boxLength:
    for w in boxWidth:
        for h in boxHeigth:
            for t in packThick:
                boxVolume=round((l/boxStep)*(w/boxStep)*(h/boxStep),6)
                productVolume=round(((l-t)/boxStep)*((w-t)/boxStep)*((h-t)/boxStep),6)
                if productVolume > 0:
                    b=b+1
                    BoxLine=[('ShipType', ['Box']),
                             ('UnitNumber', ('B'+format(b,"06d"))),
                             ('Length',(format(round(l/10,6),".6f"))),
                             ('Width',(format(round(w/10,6),".6f"))),
                             ('Height',(format(round(h/10,6),".6f"))),
                             ('Thickness',(format(round(t/5,6),".6f"))),
                             ('BoxVolume',(format(round(boxVolume,9),".9f"))),
                             ('ProductVolume',(format(round(productVolume,9),".9f")))]
                    if b==1:
                        BoxFrame = pd.DataFrame.from_items(BoxLine)
                    else:
                        BoxRow = pd.DataFrame.from_items(BoxLine)
                        BoxFrame = BoxFrame.append(BoxRow)
                    BoxFrame.index.name = 'IDNumber'
print('#################')
print('## Box')
print('#################')
print('Rows :',BoxFrame.shape[0])
print('Columns :',BoxFrame.shape[1])
print('#################')
################################################################
sFileBoxName=sFileDir + '/' + BoxFileName
BoxFrame.to_csv(sFileBoxName, index = False)
################################################################
```

## 产品模拟模型

两个模拟已经完成，您进展神速。现在，您的第三个模型是针对产品的。需求是尺寸从 100 厘米 × 100 厘米 × 100 厘米到 2.1 米 × 2.1 米 × 2.1 米的产品。您可以按如下方式构建：

# 创建有效产品

以下Python代码用于生成产品种子数据。

```python
################################################################
## Create valid Product
################################################################
productLength=range(1,21)
productWidth=range(1,21)
productHeigth=range(1,21)
productStep=10
p=0
for l in productLength:
for w in productWidth:
for h in productHeigth:
productVolume=round((l/productStep)*(w/productStep)*(h/productStep),6)
if productVolume > 0:
p=p+1
ProductLine=[('ShipType', ['Product']),
('UnitNumber', ('P'+format(p,"06d"))),
('Length',(format(round(l/10,6),".6f"))),
('Width',(format(round(w/10,6),".6f"))),
('Height',(format(round(h/10,6),".6f"))),
('ProductVolume',(format(round(productVolume,9),".9f")))]
if p==1:
ProductFrame = pd.DataFrame.from_items(ProductLine)
else:
ProductRow = pd.DataFrame.from_items(ProductLine)
ProductFrame = ProductFrame.append(ProductRow)
BoxFrame.index.name = 'IDNumber'
print('#################')
print('## Product')
print('#################')
print('Rows :',ProductFrame.shape[0])
print('Columns :',ProductFrame.shape[1])
print('#################')
################################################################
sFileProductName=sFileDir + '/' + ProductFileName
ProductFrame.to_csv(sFileProductName, index = False)
################################################################
#################################################################
print('### Done!! ############################################')
#################################################################
#################################################################
```

现在保存Python文件。用您偏好的Python编译器执行`Retrieve-Container-Plan.py`文件。

您刚刚根据简单的需求模拟了一个复杂的装箱解决方案，并准备将数据科学应用于第8至11章的模型中。您将会看到一个格式如下的容器数据文件：`C:\VKHCG\03-Hillman\01-Retrieve\01-EDS\02-Python`中的`Retrieve_Container.csv`。打开此文件，您应该会看到类似下面的数据集：

| 运输类型 | 单元编号 | 长度 | 宽度 | 高度 | 容器体积 |
| --- | --- | --- | --- | --- | --- |
| 容器 | C000001 | 1 | 1 | 1 | 1 |
| 容器 | C000002 | 1 | 1 | 2 | 2 |
| 容器 | C000003 | 1 | 1 | 3 | 3 |

您将会看到一个与`Retrieve_Box.csv`格式相似的箱子包装文件，位于`C:\VKHCG\03-Hillman\01-Retrieve\01-EDS\02-Python`。

| 运输类型 | 单元编号 | 长度 | 宽度 | 高度 | 厚度 | 箱子体积 | 产品体积 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| 箱子 | B000001 | 0.1 | 0.1 | 0.1 | 0 | 0.001 | 0.001 |
| 箱子 | B000002 | 0.1 | 0.1 | 0.2 | 0 | 0.002 | 0.002 |
| 箱子 | B000003 | 0.1 | 0.1 | 0.3 | 0 | 0.003 | 0.003 |

您还将会看到一个与`'Retrieve_Box.csv'`格式相似的箱子包装文件，位于`C:\VKHCG\03-Hillman\01-Retrieve\01-EDS\02-Python`。

| 运输类型 | 单元编号 | 长度 | 宽度 | 高度 | 产品体积 |
| --- | --- | --- | --- | --- | --- |
| 产品 | P034485 | 0.1 | 0.1 | 0.1 | 0.001 |
| 产品 | P034485 | 0.1 | 0.1 | 0.2 | 0.002 |
| 产品 | P034485 | 0.1 | 0.1 | 0.3 | 0.003 |

现在，我已经成功协助您创建了一个包含所有支持的装箱种子数据的数据集，供您的数据科学使用。接下来，我要指出，如果您在代码中更改以下任一参数：

```python
containerLength=range(1,21)
containerWidth=range(1,10)
containerHeigth=range(1,6)
containerStep=1
boxLength=range(1,21)
boxWidth=range(1,21)
boxHeigth=range(1,21)
packThick=range(0,6)
boxStep=10
productLength=range(1,21)
productWidth=range(1,21)
productHeigth=range(1,21)
productStep=10
```

您将得到一个不同的装箱解决方案，这将在数据科学研究过程中产生有趣的结果。当您熟悉技能后，我将把试验的环节留给您自己完成。

希尔曼现在拥有其容器的数据了。干得好。

## 创建配送路线

我将引导您完成为客户创建配送路线的过程。要做到这一点，我需要向您介绍一个我发现用于运输物品的卓越模型。

我想向您介绍一个非常实用的Python包，名为`geopy`。这个包是Python地理编码工具箱，它支持大多数与位置相关的查询。

该模型使您能够为公司生成复杂的航运路线规划方案。启动您的Python编辑器，并在目录`.\VKHCG\03-Hillman\01-Retrieve`中创建一个名为`Retrieve-Route-Plan.py`的文本文件。以下是您必须复制到该文件中的Python代码：

```python
################################################################
# -*- coding: utf-8 -*-
################################################################
import os
import pandas as pd
from geopy.distance import vincenty
################################################################
InputFileName='GB_Postcode_Warehouse.csv'
OutputFileName='Retrieve_GB_Warehouse.csv'
Company='03-Hillman'
################################################################
if sys.platform == 'linux':
Base=os.path.expanduser('~') + '/VKHCG'
else:
Base='C:/VKHCG'
print('################################')
print('Working Base :',Base, ' using ', sys.platform)
print('################################') ################################################################
sFileDir=Base + '/' + Company + '/01-Retrieve/01-EDS/02-Python'
if not os.path.exists(sFileDir):
os.makedirs(sFileDir)
################################################################
sFileName=Base + '/' + Company + '/00-RawData/' + InputFileName
print('###########')
print('Loading :',sFileName)
Warehouse=pd.read_csv(sFileName,header=0,low_memory=False)
WarehouseClean=Warehouse[Warehouse.latitude != 0]
WarehouseGood=WarehouseClean[WarehouseClean.longitude != 0]
WarehouseGood.drop_duplicates(subset='postcode', keep="first", inplace=True)
WarehouseGood.sort_values(by='postcode', ascending=1)
################################################################
sFileName=sFileDir + '/' + OutputFileName
WarehouseGood.to_csv(sFileName, index = False)
################################################################
WarehouseLoop = WarehouseGood.head(20)
for i in range(0,WarehouseLoop.shape[0]):
print('Run :',i,' =======>>>>>>>>>>',WarehouseLoop['postcode'][i])
WarehouseHold = WarehouseGood.head(10000)
WarehouseHold['Transaction']=WarehouseHold.apply(lambda row:
'WH-to-WH'
,axis=1)
OutputLoopName='Retrieve_Route_' + 'WH-' + WarehouseLoop['postcode'][i] + '_Route.csv'
WarehouseHold['Seller']=WarehouseHold.apply(lambda row:
'WH-' + WarehouseLoop['postcode'][i]
,axis=1)
WarehouseHold['Seller_Latitude']=WarehouseHold.apply(lambda row:
WarehouseHold['latitude'][i]
,axis=1)
WarehouseHold['Seller_Longitude']=WarehouseHold.apply(lambda row:
WarehouseLoop['longitude'][i]
,axis=1)
WarehouseHold['Buyer']=WarehouseHold.apply(lambda row:
'WH-' + row['postcode']
,axis=1)
WarehouseHold['Buyer_Latitude']=WarehouseHold.apply(lambda row:
row['latitude']
,axis=1)
WarehouseHold['Buyer_Longitude']=WarehouseHold.apply(lambda row:
row['longitude']
,axis=1)
WarehouseHold['Distance']=WarehouseHold.apply(lambda row: round(
vincenty((WarehouseLoop['latitude'][i],WarehouseLoop['longitude'][i]),
(row['latitude'],row['longitude'])).miles,6)
,axis=1)
WarehouseHold.drop('id', axis=1, inplace=True)
WarehouseHold.drop('postcode', axis=1, inplace=True)
WarehouseHold.drop('latitude', axis=1, inplace=True)
WarehouseHold.drop('longitude', axis=1, inplace=True)
################################################################
sFileLoopName=sFileDir + '/' + OutputLoopName
WarehouseHold.to_csv(sFileLoopName, index = False)
#################################################################
print('### Done!! ############################################')
#################################################################
```

现在保存这个Python文件。使用您偏好的Python编译器执行`Retrieve-Route-Plan.py`文件。

您将会在`C:\VKHCG\03-Hillman\01-Retrieve\01-EDS\02-Python`中看到一系列与`Retrieve_Route_WH-AB10_Route.csv`格式相似的文件。打开此文件，您应该会看到类似下面的数据集：

| 交易类型 | 卖家 | 卖家纬度 | 卖家经度 | 买家 | 买家纬度 | 买家经度 | 距离 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| 仓库到仓库 | WH-AB10 | 57.13514 | -2.11731 | WH-AB10 | 57.13514 | -2.11731 | 0 |
| 仓库到仓库 | WH-AB10 | 57.13514 | -2.11731 | WH-AB11 | 57.13875 | -2.09089 | 1.024914 |
| 仓库到仓库 | WH-AB10 | 57.13514 | -2.11731 | WH-AB12 | 57.101 | -2.1106 | 2.375854 |
| 仓库到仓库 | WH-AB10 | 57.13514 | -2.11731 | WH-AB13 | 57.10801 | -2.23776 | 4.906919 |

上述代码使您能够为企业生成众多的路线规划方案。例如，将`WarehouseLoop = WarehouseGood.head(20)`改为`WarehouseGood.head(200)`将生成 200 个新的路线规划方案。该代码将为您加载的每个仓库、商店或客户生成一个路线图。

# 全球邮政编码

我将引导你了解一个全球邮政编码数据集，该数据集将构成大部分非公司地址和发货需求的基础。

## 提示

通常，我发现最佳的邮政编码信息来自当地邮局或货运代理。请记住我说过的关于专家的话。邮局的专家每天每分钟都在处理包裹。他们是获取邮政编码信息的最佳联系人。

## 警告

你必须在你的 R 环境中执行一些数据科学操作。

## 注意

如果你有现成的代码可以执行任务，那就用它吧！

现在，打开你的 RStudio，并使用 R 处理以下 R 脚本：`Retrieve-Postcode-Global.r`。

```r
library(readr)
All_Countries <- read_delim("C:/VKHCG/03-Hillman/00-RawData/All_Countries.txt",
"\t", col_names = FALSE,
col_types = cols(
X12 = col_skip(),
X6 = col_skip(),
X7 = col_skip(),
X8 = col_skip(),
X9 = col_skip()),
na = "null", trim_ws = TRUE)
write.csv(All_Countries,
file = "C:/VKHCG/03-Hillman/01-Retrieve/01-EDS/01-R/Retrieve_All_Countries.csv")
```

你刚刚成功上传了一个名为 `Retrieve_All_Countries.csv` 的新文件。这是希尔曼（Hillman）检索任务的最后一步。

## 克拉克有限公司（Clark Ltd）

克拉克是该集团的财务核心。它必须处理所有与资金相关的数据源。

### 外汇（Forex）

公司的首要财务职责是执行所有外汇交易。

#### 外汇基础数据

之前，你在克拉克找到了一个用于外汇汇率的数据源（`Euro_ExchangeRates.csv`）。在本章前面部分，我帮助你创建了加载过程，作为你 R 处理的一部分。相关文件是目录 `C:\VKHCG\04-Clark\01-Retrieve\01-EDS\01-R` 下的 `Retrieve_Retrieve_Euro_ExchangeRates.csv`。因此，该数据已准备就绪。

### 财务（Financials）

克拉克负责生成集团所有公司的财务报表。

#### 财务基础数据

你在克拉克找到了一个用于财务的数据源（`Profit_And_Loss.csv`），并且如前所述，还有一个用于外汇汇率的数据源（`Euro_ExchangeRates.csv`）。在本章前面部分，我帮助你创建了加载过程，作为你 R 处理的一部分。相关文件是目录 `C:\VKHCG\04-Clark\01-Retrieve\01-EDS\01-R` 下的 `Retrieve_Profit_And_Loss.csv`。

#### 个人基础数据

现在，我将解释如何从三个压缩数据结构中提取个人信息，然后将这些单独的文件合并在一起，构成个人信息的基础。

启动你的 Python 编辑器，并在目录 `.\VKHCG\04-Clark\01-Retrieve` 下创建一个名为 `Retrieve-PersonData.py` 的文本文件。以下是必须复制到该文件中的 Python 代码：

```python
################################################################
# -*- coding: utf-8 -*-
################################################################
import sys
import os
import shutil
import zipfile
import pandas as pd
################################################################
if sys.platform == 'linux':
Base=os.path.expanduser('~') + 'VKHCG'
else:
Base='C:/VKHCG'
print('################################')
print('Working Base :',Base, ' using ', sys.platform)
print('################################')
################################################################
Company='04-Clark'
ZIPFiles=['Data_female-names','Data_male-names','Data_last-names']
for ZIPFile in ZIPFiles:
InputZIPFile=Base+'/'+Company+'/00-RawData/' + ZIPFile + '.zip'
OutputDir=Base+'/'+Company+'/01-Retrieve/01-EDS/02-Python/' + ZIPFile
OutputFile=Base+'/'+Company+'/01-Retrieve/01-EDS/02-Python/Retrieve-'+ZIPFile+'.csv'
zip_file = zipfile.ZipFile(InputZIPFile, 'r')
zip_file.extractall(OutputDir)
zip_file.close()
t=0
for dirname, dirnames, filenames in os.walk(OutputDir):
for filename in filenames:
sCSVFile = dirname + '/' + filename
t=t+1
if t==1:
NameRawData=pd.read_csv(sCSVFile,header=None,low_memory=False)
NameData=NameRawData
else:
NameRawData=pd.read_csv(sCSVFile,header=None,low_memory=False)
NameData=NameData.append(NameRawData)
NameData.rename(columns={0 : 'Name'},inplace=True)
NameData.to_csv(OutputFile, index = False)
shutil.rmtree(OutputDir)
print('Process: ',InputZIPFile)
#################################################################
print('### Done!! ############################################')
#################################################################
```

现在保存该 Python 文件，并使用你首选的 Python 编译器执行 `Retrieve-PersonData.py` 文件。这将生成三个文件，分别命名为 `Retrieve-Data_female-names.csv`、`Retrieve-Data_male-names.csv` 和 `Retrieve-Data_last-names.csv`。

至此，你已完成所有检索任务。克拉克的检索数据已准备就绪。

## 连接到其他数据源

我将提供一个高层次概述，说明如何处理非 CSV 数据源。基本的数据流和处理方式保持不变；因此，数据科学操作也保持不变。只有连接方法会发生变化。

以下部分将展示一些我经常使用的常见数据存储。

### SQLite

这需要一个名为 `sqlite3` 的包。让我们将 `IP_DATA_ALL.csv` 数据加载到位于 `.\VKHCG\01-Vermeulen\00-RawData\SQLite\` 路径下的 SQLite 数据库 `vermeulen.db` 中。打开你的 Python 编辑器，在 `.\VKHCG\03-Hillman\01-Retrieve` 目录下创建一个名为 `Retrieve-IP_DATA_ALL_2_SQLite.py` 的文本文件。

以下是必须复制到该文件中的 Python 代码：


### 脚本：Retrieve-IP_DATA_ALL_2_SQLite.py

```python
################################################################
# -*- coding: utf-8 -*-
################################################################
import sqlite3 as sq
import pandas as pd
################################################################
Base='C:/VKHCG'
sDatabaseName=Base + '/01-Vermeulen/00-RawData/SQLite/vermeulen.db'
conn = sq.connect(sDatabaseName)
################################################################
sFileName=Base + '/01-Vermeulen/00-RawData/IP_DATA_ALL.csv'
print('Loading :',sFileName)
IP_DATA_ALL=pd.read_csv(sFileName,header=0,low_memory=False)
IP_DATA_ALL.index.names = ['RowIDCSV']
sTable='IP_DATA_ALL'
print('Storing :',sDatabaseName,' Table:',sTable)
IP_DATA_ALL.to_sql(sTable, conn, if_exists="replace")
print('Loading :',sDatabaseName,' Table:',sTable)
TestData=pd.read_sql_query("select * from IP_DATA_ALL;", conn)
print('################')
print('## Data Values')
print('################')
print(TestData)
print('################')
print('## Data Profile')
print('################')
print('Rows :',TestData.shape[0])
print('Columns :',TestData.shape[1])
print('################')
################################################################
print('### Done!! ############################################')
#################################################################
```

现在保存此 Python 文件，并使用你偏好的 Python 编译器执行 `Retrieve-IP_DATA_ALL_2_SQLite.py` 文件。数据即被加载到 SQLite 中。

### 在 SQLite 内部传输数据

接下来，要在 SQLite 内部传输数据，你需要执行以下操作。打开你的 Python 编辑器，在 `.\VKHCG\03-Hillman\01-Retrieve` 目录下创建一个名为 `Retrieve-IP_DATA_ALL_SQLite.py` 的文本文件。

以下是必须复制到该文件中的 Python 代码：

```python
################################################################
import pandas as pd
import sqlite3 as sq
################################################################
Base='C:/VKHCG'
sDatabaseName=Base + '/01-Vermeulen/00-RawData/SQLite/Vermeulen.db'
conn = sq.connect(sDatabaseName)
print('Loading :',sDatabaseName)
IP_DATA_ALL=pd.read_sql_query("select * from IP_DATA_ALL;", conn)
################################################################
print('Rows:', IP_DATA_ALL.shape[0])
print('Columns:', IP_DATA_ALL.shape[1])
print('### Raw Data Set #####################################')
for i in range(0,len(IP_DATA_ALL.columns)):
    print(IP_DATA_ALL.columns[i],type(IP_DATA_ALL.columns[i]))
print('### Fixed Data Set ###################################')
IP_DATA_ALL_FIX=IP_DATA_ALL
for i in range(0,len(IP_DATA_ALL.columns)):
    cNameOld=IP_DATA_ALL_FIX.columns[i] + '     '
    cNameNew=cNameOld.strip().replace(" ", ".")
    IP_DATA_ALL_FIX.columns.values[i] = cNameNew
    print(IP_DATA_ALL.columns[i],type(IP_DATA_ALL.columns[i]))
################################################################
#print(IP_DATA_ALL_FIX.head())
################################################################
print('################')
print('Fixed Data Set with ID')
print('################')
IP_DATA_ALL_with_ID=IP_DATA_ALL_FIX
print('################')
print(IP_DATA_ALL_with_ID.head())
print('################')
sTable2='Retrieve_IP_DATA'
IP_DATA_ALL_with_ID.to_sql(sTable2,conn, index_label="RowID", if_exists="replace")
################################################################
print('### Done!! ############################################')
#################################################################
```

现在保存此 Python 文件。使用你偏好的 Python 编译器执行 `Retrieve-IP_DATA_ALL_SQLite.py` 文件。数据将在两个 SQLite 表之间加载。

### 日期和时间

要生成数据并将其直接存储到 SQLite 数据库中，请执行以下操作。打开你的 Python 编辑器，在 `.\VKHCG\04-Clark\01-Retrieve` 目录下创建一个名为 `Retrieve-Calendar.py` 的文本文件。

以下是必须复制到该文件中的 Python 代码：


```
################################################################
import sys
import os
import sqlite3 as sq
import pandas as pd
import datetime
################################################################
if sys.platform == 'linux':
    Base=os.path.expanduser('~') + 'VKHCG'
else:
    Base='C:/VKHCG'
print('################################')
print('Working Base :',Base, ' using ', sys.platform)
print('################################')
################################################################
Company='04-Clark'
################################################################
sDataBaseDir=Base + '/' + Company + '/01-Retrieve/SQLite'
if not os.path.exists(sDataBaseDir):
    os.makedirs(sDataBaseDir)
################################################################
sDatabaseName=sDataBaseDir + '/clark.db'
conn = sq.connect(sDatabaseName)
################################################################
start = pd.datetime(1900, 1, 1)
end = pd.datetime(2018, 1, 1)
date1Data= pd.DataFrame(pd.date_range(start, end))
################################################################
start = pd.datetime(2000, 1, 1)
end = pd.datetime(2020, 1, 1)
date2Data= pd.DataFrame(pd.date_range(start, end))
################################################################
dateData=date1Data.append(date2Data)
dateData.rename(columns={0 : 'FinDate'},inplace=True)
################################################################
print('################')
sTable='Retrieve_Date'
print('Storing :',sDatabaseName,' Table:',sTable)
dateData.to_sql(sTable, conn, if_exists="replace")
print('################')
################################################################
print('################################')
print('Rows : ',dateData.shape[0], ' records')
print('################################')
################################################################
t=0
for h in range(24):
    for m in range(60):
        for s in range(5):
            nTime=[str(datetime.timedelta(hours=h,minutes=m,seconds=30))]
            if h == 0 and m == 0:
                timeData= pd.DataFrame(nTime)
            else:
                time1Data=  pd.DataFrame(nTime)
                timeData= timeData.append(time1Data)
timeData.rename(columns={0 : 'FinTime'},inplace=True)
################################################################
print('################')
sTable='Retrieve_Time'
print('Storing :',sDatabaseName,' Table:',sTable)
timeData.to_sql(sTable, conn, if_exists="replace")
print('################')
################################################################
print('################################')
print('Rows : ',timeData.shape[0], ' records')
print('################################')
################################################################
print('### Done!! ############################################')
################################################################
```

现在保存此 Python 文件。使用你偏好的 Python 编译器执行 `Retrieve-Calendar.py` 文件。

数据将被加载为两个新的 `SQLite` 表，分别名为 `Retrieve_Date` 和 `Retrieve_Time`。

### 其他数据库

需要理解的关键点是，你只需更改我已在数据科学中验证有效的代码中的库：

```python
import sqlite3 as sq
```

以下库与相同的代码配合使用时同样有效。

#### PostgreSQL

`PostgreSQL` 被广泛应用于众多公司，它支持与数据库建立连接。有两种选项：一种是直接连接，使用

```python
from sqlalchemy import create_engine
engine = create_engine('postgresql://scott:tiger@localhost:5432/vermeulen')
```

另一种是通过 `psycopg2` 接口进行连接

```python
from sqlalchemy import create_engine
engine = create_engine('postgresql+psycopg2://scott:tiger@localhost/mydatabase')
```

#### Microsoft SQL Server

`Microsoft SQL Server` 在企业中很常见，此连接器支持您连接到该数据库。

有两种选项。通过 `ODBC` 连接接口，使用

```python
from sqlalchemy import create_engine
engine = create_engine('mssql+pyodbc://scott:tiger@mydsnvermeulen')
```

通过直接连接，使用

```python
from sqlalchemy import create_engine
engine = create_engine('mssql+pymssql://scott:tiger@hostname:port/vermeulen')
```

#### MySQL

`MySQL` 被众多公司广泛用于存储数据。只需更改一个简单的连接字符串，即可将这些数据开放给您的数据科学工作。

有两种选项。对于直接连接到数据库，使用

```python
from sqlalchemy import create_engine
engine = create_engine('mysql+mysqldb://scott:tiger@localhost/vermeulen')
```

对于通过 `DSN` 服务连接，使用

```
from sqlalchemy import create_engine
engine = create_engine('mssql+pyodbc://mydsnvermeulen')
```

### Oracle

Oracle 是较大型公司中常见的数据库存储方案。它能让你轻松从以下数据源加载数据：

```
from sqlalchemy import create_engine
engine = create_engine('oracle://andre:vermeulen@127.0.0.1:1521/vermeulen')
```

### Microsoft Excel

Excel 在数据共享生态系统中很常见，它能让你轻松加载此格式的文件。打开你的 Python 编辑器，在目录 `.\VKHCG\04-Clark\01-Retrieve` 中创建一个名为 `Retrieve-Country-Currency.py` 的文本文件。

以下是必须复制到该文件中的 Python 代码：

```
################################################################
# -*- coding: utf-8 -*-
################################################################
import os
import pandas as pd
################################################################
Base='C:/VKHCG'
################################################################
sFileDir=Base + '/01-Vermeulen/01-Retrieve/01-EDS/02-Python'
if not os.path.exists(sFileDir):
os.makedirs(sFileDir)
################################################################
CurrencyRawData = pd.read_excel('C:/VKHCG/01-Vermeulen/00-RawData/Country_Currency.xlsx')
sColumns = ['Country or territory', 'Currency', 'ISO-4217']
CurrencyData = CurrencyRawRawData[sColumns]
CurrencyData.rename(columns={'Country or territory': 'Country', 'ISO-4217': 'CurrencyCode'}, inplace=True)
CurrencyData.dropna(subset=['Currency'],inplace=True)
CurrencyData['Country'] = CurrencyData['Country'].map(lambda x: x.strip())
CurrencyData['Currency'] = CurrencyData['Currency'].map(lambda x: x.strip())
CurrencyData['CurrencyCode'] = CurrencyData['CurrencyCode'].map(lambda x: x.strip())
print(CurrencyData)
################################################################
sFileName=sFileDir + '/Retrieve-Country-Currency.csv'
CurrencyData.to_csv(sFileName, index = False)
################################################################
```

现在保存该 Python 文件。使用你偏好的 Python 编译器执行 `Retrieve-Calendar.py` 文件。该代码会生成 `Retrieve-Country-Currency.csv`。

### Apache Spark

Apache Spark 正成为分布式数据处理的下一个标准。处理生态系统的普遍接受和支持，正使掌握这项技术变成一项必备技能。

使用 `pyspark.sql` 包。通过以下代码建立连接：

```
import pyspark
sc = pyspark.SparkContext()
sql = SQLContext(sc)
df = (sql.read
.format("IP_DATA_ALL.csv")
.option("header", "true")
.load("/path/to_csv.csv"))
```

### Apache Cassandra

Cassandra 正成为企业界广泛使用的分布式数据库引擎。这项技术和生态系统的优势在于，它们能以极佳的扩展方式进行大规模扩展。该数据库可以将节点加入或排除到数据库生态系统中，而不会中断数据处理。

要访问它，请使用 Python 包 `cassandra`。

```
from cassandra.cluster import Cluster
cluster = Cluster()
session = cluster.connect(‘vermeulen’)
```

### Apache Hive

访问 Hive 可将其高度分布式的生态系统开放给数据科学家使用。为实现此目的，我建议使用以下方式：

```
Import pyhs2
with pyhs2.connect(host='localhost',
port=10000,
authMechanism="PLAIN",
user='andre',
password='vermeulen',
database='vermeulen') as conn:
with conn.cursor() as cur:
#Execute query
cur.execute("select * from IP_DATA_ALL")
for i in cur.fetch():
print (i)
```

这样，你就能从存储在 Hadoop 之上的 Hive 数据湖中读取数据。

### Apache Hadoop

Hadoop 是高度分布式数据科学中最成功的数据湖生态系统之一。如果你想要进行大规模数据科学工作，我建议你了解如何访问这种数据结构中的数据。

### PyDoop

`pydoop` 包包含用于 Hadoop 的 Python MapReduce 和 HDFS API。Pydoop 是 Hadoop 的一个 Python 接口，允许你用纯 Python 编写 MapReduce 应用并与 HDFS 交互。参见 [`https://pypi.python.org/pypi/pydoop`](https://pypi.python.org/pypi/pydoop)。

### Luigi

`Luigi` 提供一系列 Python 功能，让你能够将复杂管道构建成批处理作业。作为包的一部分，它处理依赖解析和工作流管理。

这将使你免于执行复杂的编程，同时保证高质量的处理。参见 [`https://pypi.python.org/pypi/luigi`](https://pypi.python.org/pypi/luigi)。

## Amazon S3 存储

S3，即 Amazon Simple Storage Service（Amazon S3），创建了简单实用的方法来收集、存储和分析数据，无论何种格式，均可完全大规模进行。我将大部分基础数据存储在 S3 中，因为它比大多数其他方法更便宜。

### s3 包

Python 的 `s3` 模块连接到 Amazon 的 S3 REST API。参见 [`https://pypi.python.org/pypi/s3`](https://pypi.python.org/pypi/s3)。

### Boto 包

`Boto` 包是我推荐的另一个有用工具。它可以从 `boto` 库中访问，如下所示：

```
from boto.s3.connection import S3Connection
conn = S3Connection('', '')
```

### Amazon Redshift

Amazon Redshift 是云中一个完全托管、PB 级的数据仓库服务。Python 包 `redshift-sqlalchemy` 是适用于 `sqlalchemy` 的 Amazon Redshift 方言，可将此数据源开放给您的数据科学工作。参见 [`https://pypi.python.org/pypi/redshift-sqlalchemy`](https://pypi.python.org/pypi/redshift-sqlalchemy)。

这是一个典型的连接：
```

# 数据检索与 AWS 集成

```python
from sqlalchemy import create_engine
engine = create_engine("redshift+psycopg2://username@host.amazonaws.com:5439/database"
```

你也可以通过 `pandas-redshift` 包连接到 Redshift。（参见 [`https://pypi.python.org/pypi/pandas-redshift`](https://pypi.python.org/pypi/pandas-redshift)。）它结合了 `psycopg2`、`pandas` 和 `boto3` 包的功能，并使用以下连接：

```python
engine = create_engine('redshift+psycopg2://username:password@us-east-1.redshift.amazonaws.com:port/vermeulen')
```

你可以毫不费力地连接到众多数据源，我建议你探究如何将它们的能力应用于你的数据科学工作中。

## Amazon Web Services

在使用 Amazon Web Services 时，我建议你查看 `boto3` 这个包。（参见：[`https://pypi.python.org/pypi/boto3`](https://pypi.python.org/pypi/boto3)。）

它是一个 Amazon Web Services 的 Python 库包，提供了访问 Amazon Web Services 的接口。例如，以下 Python 代码将创建一个 EC2 云计算数据处理生态系统：

```python
import boto3
ec2 = boto3.resource('ec2')
instance = ec2.create_instances(
ImageId='ami-1e299d7e',
MinCount=1,
MaxCount=1,
InstanceType='t2.micro')
print instance[0].id
```

完成后，你可以从 S3 的数据存储中下载文件。

```python
import boto3
import botocore
BUCKET_NAME = 'vermeulen' # 请替换为你的存储桶名称
KEY = ' Key_IP_DATA_ALL'# 请替换为你的对象键
s3 = boto3.resource('s3')
try:
s3.Bucket(BUCKET_NAME).download_file(KEY, 'IP_DATA_ALL.csv')
except botocore.exceptions.ClientError as e:
if e.response['Error']['Code'] == "404":
print("对象不存在。")
else:
raise
```

通过 `boto3`，你有多种选择，可以在云环境中创建所需的服务，然后加载所需的数据。掌握这一过程是数据科学家的一项重要要求。

## 总结

恭喜！你已经完成了“检索”步骤。我引导你完成了大数据处理的第一步。你所取得的结果将构成第 8 章“评估”步骤的基础数据。

那么，在这个阶段你应该了解哪些内容呢？

-   如何将第 4 章的功能需求转换为数据映射矩阵。请记住：如果数据未通过检索层加载，则不属于该流程的一部分。
-   在执行检索处理时，如何转换和更新数据映射矩阵。你检索的每个数据源都是潜在的第二隐藏信息源。
-   了解源系统的数据概况。并非所有源信息都具有一致和/或良好的数据格式。许多数据需要定期进行特征工程。
-   了解从哪里获取其他数据源，甚至是诸如 Google、Amazon Web Services 和 Microsoft Azure 等基于云的系统。

既然你已经掌握了检索步骤，我建议你先喝杯饮料，然后我们继续学习第 8 章，在那里我们将大致修复你保存数据中的所有数据质量问题。

脚注 1

James Dixon, “Pentaho, Hadoop, and Data Lakes,” James Dixon 的博客，[`https://jamesdixon.wordpress.com/2010/10/14/pentaho-hadoop-and-data-lakes/`](https://jamesdixon.wordpress.com/2010/10/14/pentaho-hadoop-and-data-lakes/)，2010 年 10 月 14 日。

# 评估步骤

本章的目标是向你展示如何评估你的数据科学数据，以发现无效或错误的数据值。

**注意**
我发现在我 90% 的项目中，有 70% 的处理工作都花在了这个步骤上，目的是提高所用数据的质量。这个步骤无疑会提高你数据科学工作的质量。

我强烈建议你在进行数据科学工作之前，花时间对数据进行“清理”，因为错误的数据条目会对后续步骤产生重大影响。也请针对“错误”数据开展数据科学项目，以理解上游流程为何会产生错误数据。通过调查这些错误数据，我发现了客户生态系统中许多未知的问题。

现在，我将向你介绍一系列评估技术和算法，我会在项目检索阶段，科学地将它们应用于导入的数据。

**注意**
你的特定数据源会有额外要求。请根据你的特定数据，探索所需的其他质量技术和算法。

## 评估步骤

数据质量指的是一组定性或定量变量的状况。数据质量是衡量特定数据集可接受性的一个多维指标。在商业中，衡量数据质量是为了确定数据是否可以作为可靠情报提取的基础，以支持组织决策。

数据剖析涉及在你的数据源中观察信息提供的所有视角。主要目标是确定各个视角是否准确和完整。评估步骤决定了对于不符合要求的数据条目需要应用哪些额外的处理。

我应该指出，执行此任务并不像大多数客户想象的那么简单。少量错误数据可能会对后续数据处理步骤产生重大影响，并可能影响数据科学的质量。

**注意**
你总会遇到错误。正是这些质量技术和算法解决了这些错误，引导你走上成功数据科学之路。

### 错误

你发现了错误或问题吗？通常，我可以对数据采取以下四种处理方式之一。

#### 接受错误

如果错误在可接受的标准范围内（例如，“West Street”而不是“West St.”），我可以决定接受它并继续处理下一个数据条目。

请注意，如果你接受错误，将会影响执行分类的数据科学技术和算法，例如分箱、回归、聚类和决策树，因为这些过程假设此示例中的值是不同的。这是一种简单的选择，但并非总是最佳选择。

#### 拒绝错误

偶尔，主要是在首次数据导入时，信息损坏严重，以至于最好有方法地直接删除数据条目，而不是试图纠正它。请注意：删除数据是最后的手段。我通常会添加一个质量标志，并使用此标志来避免这些错误数据被用于会受其负面影响的数据科学技术和算法。我将在本书的其余部分讨论具体的数据科学技术和算法，并在每个阶段解释如何处理错误数据。

#### 纠正错误

这是评估步骤中大部分工作致力于实现的选择。客户姓名、地址和位置中的拼写错误是常见的错误来源，这些错误会被有条不紊地纠正。如果名称存在变体，我建议将一个数据源设置为“主数据源”，并以此主数据源为基础，跨所有数据库保持数据的一致和正确。我还建议将原始错误存储在一个单独的值中，因为它有助于发现持续产生错误的数据源的模式。

#### 创建默认值

这是我在企业咨询工作中常见的一种选择。大多数系统开发者假设，如果业务方没有输入值，他们就应该输入一个默认值。我见过的常见默认值有“未知”或“不适用”。不幸的是，我也见过许多不理想的选择，例如用生日作为日期，用宠物名字作为名字和姓氏，用父母的地址作为地址……当多个使用相同服务进行营销的公司向父母地址发送超过 300 封附带样品的营销信函时，这种地址选择当然会出问题。我建议你与客户详细讨论默认值，并就官方的“缺失数据”值达成一致。

### 数据分析

我总是为所有导入的数据生成一份健康报告。我建议使用以下六个数据质量维度。

## 完整性

我计算每个数据源字段中错误条目数占总数据量的百分比。若数据源因包含关键数据（客户姓名、电话号码、电子邮件地址等）而具有特殊重要性，我优先分析这些字段，确保数据源具备进入下一阶段分析的条件，以验证非关键数据的完整性。例如，要实现个人数据的唯一性，至少需要名字、姓氏和出生日期这三项。如果其中任何信息缺失，则该个人数据条目即为不完整。请注意，完整性是针对你所处理数据的业务领域而言的。

## 唯一性

我评估特定值相对于该字段中其他数据的唯一程度。同时，还需将该值与其他已知同数据集来源的值进行比对。最后一项唯一性检验是找出同一字段存在于多个数据源中的情况。通常，你会以直方图的形式报告每个数据源中所有唯一值的分布情况，以此来呈现唯一性。

## 时效性

记录日期和时间对数据源的影响。数据是否存在稳定期或不稳定期？在进行源系统提取调度时，这一检查十分有用。我曾见过无数在月底结束前就执行的月末快照提取。这些提取数据毫无价值。我建议你与客户的运营人员紧密合作，确保在业务周期的正确时间点执行数据提取。

## 有效性

有效性需根据已知且获批的标准进行检验。记录结果以不符合标准的数据所占百分比呈现。我发现大多数数据条目都有对应的标准。例如，国家代码使用 ISO 3166-1 标准；货币使用 ISO 4217 标准。

我还建议你关注客户特定的标准，例如国际疾病分类（ICD）标准 ICD-10。请注意：标准会随时间变化。例如，ICD-10 是标准的第十版。ICD-7 于 1958 年生效，ICD-8A 于 1968 年，ICD-9 于 1979 年，ICD-10 于 1999 年。因此，当你验证数据时，务必在正确的数据时段应用正确的标准。

## 准确性

准确性是针对数据源中记录的真实人物或对象来衡量数据准确程度。存在一些法规，例如欧盟的《通用数据保护条例》（GDPR），要求数据在准确性方面必须合规。我建议你调查自己需要遵守哪些关于准确性的标准和法规。

## 一致性

该度量指标记录数据模式的变化。衡量每次数据加载后数据的变化情况。我建议你测量数据源的模式和校验和。

在第九章 `9`，我将介绍一种名为“数据仓库”的数据结构，它将帮助你完成此过程。在第十章 `10`，我将演示如何使用缓慢变化维度来追踪这些一致性。

**注意**
对系统中错误的研究与对有效数据的研究同等重要。许多真实的业务问题都是由数据错误引起的。

现在，我将继续指导你将刚刚学到的知识付诸实践。

## 实践操作

在第七章 `7`，我向你介绍了 Python 包 `pandas`。该包具备多种自动错误管理功能。

### Pandas 中的缺失值

我将首先引导你了解一些基本的错误处理概念，然后将所学内容应用于样本数据。我将使用带有 `pandas` 包的 Python 进行演示，但这些基本概念同样适用于许多其他工具。

以下是四个基本的处理概念。

#### 删除所有元素均为缺失值的列

打开你的 Python 编辑器，在目录 `C:\VKHCG\01-Vermeulen\02-Assess` 下创建一个名为 `Assess-Good-Bad-01.py` 的文件。然后将以下代码复制到文件中：


```
################################################################
# -*- coding: utf-8 -*-
################################################################
Import sys
import os
import pandas as pd
################################################################
if `sys.platform` == 'linux':
`Base`=os.path.expanduser('~') + '/VKHCG'
else:
`Base`='C:/VKHCG'
print('################################')
print('Working Base:',`Base`,'using',`sys.platform`)
print('################################') `sInputFileName`='Good-or-Bad.csv'
`sOutputFileName`='Good-or-Bad-01.csv'
`Company`='01-Vermeulen'
################################################################
################################################################
`sFileDir`=`Base` + '/' + `Company` + '/02-Assess/01-EDS/02-Python'
if not os.path.exists(`sFileDir`):
os.makedirs(`sFileDir`)
################################################################
### Import Warehouse
################################################################
`sFileName`=`Base` + '/' + `Company` + '/00-RawData/' + `sInputFileName`
print('Loading:',`sFileName`)
`RawData`=pd.read_csv(`sFileName`,header=0)
print('################################')
print('## Raw Data Values')
print('################################')
print(`RawData`)
print('################################')
print('## Data Profile')
print('################################')
print('Rows:',`RawData`.shape[0])
print('Columns:',`RawData`.shape[1])
print('################################')
################################################################
`sFileName`=`sFileDir` + '/' + `sInputFileName`
`RawData`.to_csv(`sFileName`, index = False)
################################################################
## This is the important action! The rest of this code snippet
## Only supports this action.
################################################################
`TestData`=`RawData`.dropna(axis=1, how="all")
################################################################
print('################################')
print('## Test Data Values')
print('################################')
print(`TestData`)
print('################################')
print('## Data Profile')
print('################################')
print('Rows:',`TestData`.shape[0])
print('Columns:',`TestData`.shape[1])
print('################################')
################################################################
`sFileName`=`sFileDir` + '/' + `sOutputFileName`
`TestData`.to_csv(`sFileName`, index = False)
################################################################
print('################################')
print('### Done!! #####################')
print('################################')
################################################################
```

保存文件 `Assess-Good-Bad-01.py`，然后使用你的 Python 编译器编译并执行它。这将在目录 `C:/VKHCG/01-Vermeulen/02-Assess/01-EDS/02-Python` 下生成一组显示值以及两个名为 `Good-or-Bad.csv` 和 `Good-or-Bad-01.csv` 的文件。

该代码加载以下原始数据：

| ID | FieldA | FieldB | FieldC | FieldD | FieldE | FieldF | FieldG |
| --- | --- | --- | --- | --- | --- | --- | --- |
| 1 | 好 | 更好 | 最好 | 1024 |   | 10241 | 1 |
| 2 | 好 |   | 最好 | 512 |   | 5121 | 2 |
| 3 | 好 | 更好 |   | 256 |   | 256 | 3 |
| 4 | 好 | 更好 | 最好 |   |   | 211 | 4 |
| 5 | 好 | 更好 |   | 64 |   | 6411 | 5 |
| 6 | 好 |   | 最好 | 32 |   | 32 | 6 |
| 7 |   | 更好 | 最好 | 16 |   | 1611 | 7 |
| 8 |   |   | 最好 | 8 |   | 8111 | 8 |
| 9 |   |   |   | 4 |   | 41 | 9 |
| 10 | A | B | C | 2 |   | 21111 | 10 |
|   |   |   |   |   |   |   | 11 |
| 10 | 好 | 更好 | 最好 | 1024 |   | 102411 | 12 |
| 10 | 好 |   | 最好 | 512 |   | 512 | 13 |
| 10 | 好 | 更好 |   | 256 |   | 1256 | 14 |
| 10 | 好 | 更好 | 最好 |   |   |   | 15 |
| 10 | 好 | 更好 |   | 64 |   | 164 | 16 |
| 10 | 好 |   | 最好 | 32 |   | 322 | 17 |
| 10 |   | 更好 | 最好 | 16 |   | 163 | 18 |
| 10 |   |   | 最好 | 8 |   | 844 | 19 |
| 10 |   |   |   | 4 |   | 4555 | 20 |
| 10 | A | B | C | 2 |   | 111 | 21 |

`TestData=RawData.dropna(axis=1, how="all")` 这行代码会执行如下纠错：

| ID | FieldA | FieldB | FieldC | FieldD | FieldF | FieldG |
| --- | --- | --- | --- | --- | --- | --- |
| 1 | 好 | 更好 | 最好 | 1024 | 10241 | 1 |
| 2 | 好 |   | 最好 | 512 | 5121 | 2 |
| 3 | 好 | 更好 |   | 256 | 256 | 3 |
| 4 | 好 | 更好 | 最好 |   | 211 | 4 |
| 5 | 好 | 更好 |   | 64 | 6411 | 5 |
| 6 | 好 |   | 最好 | 32 | 32 | 6 |
| 7 |   | 更好 | 最好 | 16 | 1611 | 7 |
| 8 |   |   | 最好 | 8 | 8111 | 8 |
| 9 |   |   |   | 4 | 41 | 9 |
| 10 | A | B | C | 2 | 21111 | 10 |
| 10 | 好 | 更好 | 最好 | 1024 | 102411 | 12 |
| 10 | 好 |   | 最好 | 512 | 512 | 13 |
| 10 | 好 | 更好 |   | 256 | 1256 | 14 |
| 10 | 好 | 更好 | 最好 |   |   | 15 |
| 10 | 好 | 更好 |   | 64 | 164 | 16 |
| 10 | 好 |   | 最好 | 32 | 322 | 17 |
| 10 |   | 更好 | 最好 | 16 | 163 | 18 |
| 10 |   |   | 最好 | 8 | 844 | 19 |
| 10 |   |   |   | 4 | 4555 | 20 |
| 10 | A | B | C | 2 | 111 | 21 |


#### 删除任何元素存在缺失值的列

|  ---  |  ---  |  ---  |  ---  |  ---  |  ---  |  ---  |
| --- | --- | --- | --- | --- | --- | --- |

| 1 | 好 | 更好 | 最好 | 1024 | 10241 | 1 |
| 2 | 好 |   | 最好 | 512 | 5121 | 2 |
| 3 | 好 | 更好 |   | 256 | 256 | 3 |
| 4 | 好 | 更好 | 最好 |   | 211 | 4 |
| 5 | 好 | 更好 |   | 64 | 6411 | 5 |
| 6 | 好 |   | 最好 | 32 | 32 | 6 |
| 7 |   | 更好 | 最好 | 16 | 1611 | 7 |
| 8 |   |   | 最好 | 8 | 8111 | 8 |
| 9 |   |   |   | 4 | 41 | 9 |
| 10 | A | B | C | 2 | 21111 | 10 |
|   |   |   |   |   |   | 11 |
| 10 | 好 | 更好 | 最好 | 1024 | 102411 | 12 |
| 10 | 好 |   | 最好 | 512 | 512 | 13 |
| 10 | 好 | 更好 |   | 256 | 1256 | 14 |
| 10 | 好 | 更好 | 最好 |   |   | 15 |
| 10 | 好 | 更好 |   | 64 | 164 | 16 |
| 10 | 好 |   | 最好 | 32 | 322 | 17 |
| 10 |   | 更好 | 最好 | 16 | 163 | 18 |
| 10 |   |   | 最好 | 8 | 844 | 19 |
| 10 |   |   |   | 4 | 4555 | 20 |
| 10 | A | B | C | 2 | 111 | 21 |

E 列已被完全删除，因为该列的所有值均为缺失值或错误值。

打开您的 Python 编辑器，在目录`C:\VKHCG\01-Vermeulen\02-Assess`中创建一个名为`Assess-Good-Bad-02.py`的文件。然后将以下代码复制到该文件中：

```python
################################################################
# -*- coding: utf-8 -*-
################################################################
Import sys
import os
import pandas as pd
################################################################
if sys.platform == 'linux':
Base=os.path.expanduser('~') + '/VKHCG'
else:
Base='C:/VKHCG'
print('################################')
print('Working Base:',Base,'using',sys.platform)
print('################################') sInputFileName='Good-or-Bad.csv'
sOutputFileName='Good-or-Bad-02.csv'
Company='01-Vermeulen'
################################################################
Base='C:/VKHCG'
################################################################
sFileDir=Base + '/' + Company + '/02-Assess/01-EDS/02-Python'
if not os.path.exists(sFileDir):
os.makedirs(sFileDir)
################################################################
### Import Warehouse
################################################################
sFileName=Base + '/' + Company + '/00-RawData/' + sInputFileName
print('Loading:',sFileName)
RawData=pd.read_csv(sFileName,header=0)
print('################################')
print('## Raw Data Values')
print('################################')
print(RawData)
print('################################')
print('## Data Profile')
print('################################')
print('Rows:',RawData.shape[0])
print('Columns:',RawData.shape[1])
print('################################')
################################################################
sFileName=sFileDir + '/' + sInputFileName
RawData.to_csv(sFileName, index = False)
################################################################
## This is the important action! The rest of this code snippet
## Only supports this action.
################################################################
TestData=RawData.dropna(axis=1, how="any")
################################################################
print('################################')
print('## Test Data Values')
print('################################')
print(TestData)
print('################################')
print('## Data Profile')
print('################################')
print('Rows:',TestData.shape[0])
print('Columns:',TestData.shape[1])
print('################################')
################################################################
sFileName=sFileDir + '/' + sOutputFileName
TestData.to_csv(sFileName, index = False)
################################################################
print('################################')
print('### Done!! #####################')
print('################################')
################################################################
```

保存`Assess-Good-Bad-02.py`文件，然后使用 Python 编译器进行编译和执行。这将生成一组显示值，并在目录`C:/VKHCG/01-Vermeulen/02-Assess/01-EDS/02-Python`中生成两个文件，分别为`Good-or-Bad.csv`和`Good-or-Bad-02.csv`。

代码加载了以下原始数据：

| ID | FieldA | FieldB | FieldC | FieldD | FieldE | FieldF | FieldG |
| --- | --- | --- | --- | --- | --- | --- | --- |

| 1 | 好 | 更好 | 最好 | 1024 |   | 10241 | 1 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| 2 | 好 |   | 最好 | 512 |   | 5121 | 2 |
| 3 | 好 | 更好 |   | 256 |   | 256 | 3 |
| 4 | 好 | 更好 | 最好 |   |   | 211 | 4 |
| 5 | 好 | 更好 |   | 64 |   | 6411 | 5 |
| 6 | 好 |   | 最好 | 32 |   | 32 | 6 |
| 7 |   | 更好 | 最好 | 16 |   | 1611 | 7 |
| 8 |   |   | 最好 | 8 |   | 8111 | 8 |
| 9 |   |   |   | 4 |   | 41 | 9 |
| 10 | A | B | C | 2 |   | 21111 | 10 |
|   |   |   |   |   |   |   | 11 |
| 10 | 好 | 更好 | 最好 | 1024 |   | 102411 | 12 |
| 10 | 好 |   | 最好 | 512 |   | 512 | 13 |
| 10 | 好 | 更好 |   | 256 |   | 1256 | 14 |
| 10 | 好 | 更好 | 最好 |   |   |   | 15 |
| 10 | 好 | 更好 |   | 64 |   | 164 | 16 |
| 10 | 好 |   | 最好 | 32 |   | 322 | 17 |
| 10 |   | 更好 | 最好 | 16 |   | 163 | 18 |
| 10 |   |   | 最好 | 8 |   | 844 | 19 |
| 10 |   |   |   | 4 |   | 4555 | 20 |
| 10 | A | B | C | 2 |   | 111 | 21 |

`TestData=RawData.dropna(axis=1, how="any")` 这行代码会执行如下纠错：

| FieldG |
| --- |
| 1 |
| 2 |
| 3 |
| 4 |
| 5 |
| 6 |
| 7 |
| 8 |
| 9 |
| 10 |
| 11 |
| 12 |
| 13 |
| 14 |
| 15 |
| 16 |
| 17 |
| 18 |
| 19 |
| 20 |
| 21 |

列 A、B、C、D、E 和 F 已被移除，因为其中一些值存在缺失值或错误。当您需要隔离完整且正确的列时，移除整个列的操作非常有用。

## 仅保留最多包含两个缺失值的行

打开你的 Python 编辑器，并在目录 `C:\VKHCG\01-Vermeulen\02-Assess` 中创建一个名为 `Assess-Good-Bad-03.py` 的文件。现在将以下代码复制到该文件中：

```python
################################################################
# -*- coding: utf-8 -*-
################################################################
Import sys
import os
import pandas as pd
################################################################
if sys.platform == 'linux':
Base=os.path.expanduser('~') + '/VKHCG'
else:
Base='C:/VKHCG'
print('################################')
print('Working Base:',Base,'using',sys.platform)
print('################################') ################################################################
sInputFileName='Good-or-Bad.csv'
sOutputFileName='Good-or-Bad-03.csv'
Company='01-Vermeulen'
################################################################
Base='C:/VKHCG'
################################################################
sFileDir=Base + '/' + Company + '/02-Assess/01-EDS/02-Python'
if not os.path.exists(sFileDir):
os.makedirs(sFileDir)
################################################################
### Import Warehouse
################################################################
sFileName=Base + '/' + Company + '/00-RawData/' + sInputFileName
print('Loading:',sFileName)
RawData=pd.read_csv(sFileName,header=0)
print('################################')
print('## Raw Data Values')
print('################################')
print(RawData)
print('################################')
print('## Data Profile')
print('################################')
print('Rows:',RawData.shape[0])
print('Columns:',RawData.shape[1])
print('################################')
################################################################
sFileName=sFileDir + '/' + sInputFileName
RawData.to_csv(sFileName, index = False)
################################################################
## This is the important action! The rest of this code snippet
## Only supports this action.
################################################################
TestData=RawData.dropna(thresh=2)
################################################################
print('################################')
print('## Test Data Values')
print('################################')
print(TestData)
print('################################')
print('## Data Profile')
print('################################')
print('Rows:',TestData.shape[0])
print('Columns:',TestData.shape[1])
print('################################')
################################################################
sFileName=sFileDir + '/' + sOutputFileName
TestData.to_csv(sFileName, index = False)
################################################################
print('################################')
print('### Done!! #####################')
print('################################')
################################################################
```

保存 `Assess-Good-Bad-03.py` 文件，然后使用你的 `Python` 编译器编译并执行它。这将产生一组显示值，以及在目录 `C:/VKHCG/01-Vermeulen/02-Assess/01-EDS/02-Python` 中生成的两个文件，分别为 `Good-or-Bad.csv` 和 `Good-or-Bad-03.csv`。

该代码加载以下原始数据：

| ID | FieldA | FieldB | FieldC | FieldD | FieldE | FieldF | FieldG |
| --- | --- | --- | --- | --- | --- | --- | --- |
| 1 | Good | Better | Best | 1024 |   | 10241 | 1 |
| 2 | Good |   | Best | 512 |   | 5121 | 2 |
| 3 | Good | Better |   | 256 |   | 256 | 3 |
| 4 | Good | Better | Best |   |   | 211 | 4 |
| 5 | Good | Better |   | 64 |   | 6411 | 5 |
| 6 | Good |   | Best | 32 |   | 32 | 6 |
| 7 |   | Better | Best | 16 |   | 1611 | 7 |
| 8 |   |   | Best | 8 |   | 8111 | 8 |
| 9 |   |   |   | 4 |   | 41 | 9 |
| 10 | A | B | C | 2 |   | 21111 | 10 |
|   |   |   |   |   |   |   | 11 |
| 10 | Good | Better | Best | 1024 |   | 102411 | 12 |
| 10 | Good |   | Best | 512 |   | 512 | 13 |
| 10 | Good | Better |   | 256 |   | 1256 | 14 |
| 10 | Good | Better | Best |   |   |   | 15 |
| 10 | Good | Better |   | 64 |   | 164 | 16 |
| 10 | Good |   | Best | 32 |   | 322 | 17 |
| 10 |   | Better | Best | 16 |   | 163 | 18 |
| 10 |   |   | Best | 8 |   | 844 | 19 |
| 10 |   |   |   | 4 |   | 4555 | 20 |

| 10 | A | B | C | 2 |   | 111 | 21 |

`TestData=RawData.dropna(thresh=2)` 的结果如下，即错误修正后的数据：

| ID | FieldA | FieldB | FieldC | FieldD | FieldE | FieldF | FieldG |
| --- | --- | --- | --- | --- | --- | --- | --- |
| 1 | Good | Better | Best | 1024 |   | 10241 | 1 |
| 2 | Good |   | Best | 512 |   | 5121 | 2 |
| 3 | Good | Better |   | 256 |   | 256 | 3 |
| 4 | Good | Better | Best |   |   | 211 | 4 |
| 5 | Good | Better |   | 64 |   | 6411 | 5 |
| 6 | Good |   | Best | 32 |   | 32 | 6 |
| 7 |   | Better | Best | 16 |   | 1611 | 7 |
| 8 |   |   | Best | 8 |   | 8111 | 8 |
| 9 |   |   |   | 4 |   | 41 | 9 |
| 10 | A | B | C | 2 |   | 21111 | 10 |
| 10 | Good | Better | Best | 1024 |   | 102411 | 12 |
| 10 | Good |   | Best | 512 |   | 512 | 13 |
| 10 | Good | Better |   | 256 |   | 1256 | 14 |
| 10 | Good | Better | Best |   |   |   | 15 |
| 10 | Good | Better |   | 64 |   | 164 | 16 |
| 10 | Good |   | Best | 32 |   | 322 | 17 |
| 10 |   | Better | Best | 16 |   | 163 | 18 |
| 10 |   |   | Best | 8 |   | 844 | 19 |
| 10 |   |   |   | 4 |   | 4555 | 20 |
| 10 | A | B | C | 2 |   | 111 | 21 |

第 11 行已被删除，原因在于它的两个以上的值为缺失值/错误值。

#### 使用特定数值列的均值、中位数、众数、最小值和最大值填充所有缺失值

打开你的 Python 编辑器，并在目录 `C:\VKHCG\01-Vermeulen\02-Assess` 中创建一个名为 `Assess-Good-Bad-04.py` 的文件。现在将以下代码复制到该文件中：

# Python 数据填充处理脚本

## 基本设置

```python
################################################################
# -*- coding: utf-8 -*-
################################################################
import sys
import os
import pandas as pd
################################################################
```

## 变量定义

`Base`=`'C:/VKHCG'`
`sInputFileName`=`'Good-or-Bad.csv'`
`sOutputFileNameA`=`'Good-or-Bad-04-A.csv'`
`sOutputFileNameB`=`'Good-or-Bad-04-B.csv'`
`sOutputFileNameC`=`'Good-or-Bad-04-C.csv'`
`sOutputFileNameD`=`'Good-or-Bad-04-D.csv'`
`sOutputFileNameE`=`'Good-or-Bad-04-E.csv'`
`Company`=`'01-Vermeulen'`

## 平台路径设置

```python
if sys.platform == 'linux':
    `Base`=os.path.expanduser('~') + '/VKHCG'
else:
    `Base`='C:/VKHCG'
print('################################')
print('Working Base:',`Base`,'using',sys.platform)
print('################################')
```

## 目录创建

`sFileDir`=`Base` + '/' + `Company` + '/02-Assess/01-EDS/02-Python'

```python
if not os.path.exists(`sFileDir`):
    os.makedirs(`sFileDir`)
```

## 导入仓库

`sFileName`=`Base` + '/' + `Company` + '/00-RawData/' + `sInputFileName`

```python
print('Loading:',`sFileName`)
`RawData`=pd.read_csv(`sFileName`,header=0)
print('################################')
print('## 原始数据值')
print('################################')
print(`RawData`)
print('################################')
print('## 数据概况')
print('################################')
print('Rows:',`RawData`.shape[0])
print('Columns:',`RawData`.shape[1])
print('################################')
```

## 保存原始数据

`sFileName`=`sFileDir` + '/' + `sInputFileName`

```python
`RawData`.to_csv(`sFileName`, index = False)
```

## 填充方法：平均值

```python
`TestData`=`RawData`.fillna(`RawData`.mean())
```

```python
print('################################')
print('## 测试数据值 - 平均值')
print('################################')
print(`TestData`)
print('################################')
print('## 数据概况')
print('################################')
print('Rows:',`TestData`.shape[0])
print('Columns:',`TestData`.shape[1])
print('################################')
```

`sFileName`=`sFileDir` + '/' + `sOutputFileNameA`

```python
`TestData`.to_csv(`sFileName`, index = False)
```

**关键操作**：本代码片段剩余部分仅用于支持此操作。

## 填充方法：中位数

```python
`TestData`=`RawData`.fillna(`RawData`.median())
```

```python
print('################################')
print('## 测试数据值 - 中位数')
print('################################')
print(`TestData`)
print('################################')
print('## 数据概况')
print('################################')
print('Rows:',`TestData`.shape[0])
print('Columns:',`TestData`.shape[1])
print('################################')
```

`sFileName`=`sFileDir` + '/' + `sOutputFileNameB`

```python
`TestData`.to_csv(`sFileName`, index = False)
```

## 填充方法：众数

```python
`TestData`=`RawData`.fillna(`RawData`.mode())
```

```python
print('################################')
print('## 测试数据值 - 众数')
print('################################')
print(`TestData`)
print('################################')
print('## 数据概况')
print('################################')
print('Rows:',`TestData`.shape[0])
print('Columns:',`TestData`.shape[1])
print('################################')
```

`sFileName`=`sFileDir` + '/' + `sOutputFileNameC`

```python
`TestData`.to_csv(`sFileName`, index = False)
```

## 填充方法：最小值

```python
`TestData`=`RawData`.fillna(`RawData`.min())
```

```python
print('################################')
print('## 测试数据值 - 最小值')
print('################################')
print(`TestData`)
print('################################')
print('## 数据概况')
print('################################')
print('Rows:',`TestData`.shape[0])
print('Columns:',`TestData`.shape[1])
print('################################')
```

`sFileName`=`sFileDir` + '/' + `sOutputFileNameD`

```python
`TestData`.to_csv(`sFileName`, index = False)
```

## 填充方法：最大值

```python
`TestData`=`RawData`.fillna(`RawData`.max())
```

```python
print('################################')
print('## 测试数据值 - 最大值')
print('################################')
print(`TestData`)
print('################################')
print('## 数据概况')
print('################################')
print('Rows:',`TestData`.shape[0])
print('Columns:',`TestData`.shape[1])
print('################################')
```

`sFileName`=`sFileDir` + '/' + `sOutputFileNameE`

```python
`TestData`.to_csv(`sFileName`, index = False)
```

## 完成

```python
print('################################')
print('### 完成！！ #####################')
print('################################')
```

将文件保存为`Assess-Good-Bad-04.py`，然后使用Python编译器编译并执行。这将生成一系列显示值，以及六个文件：`Good-or-Bad.csv`、`Good-or-Bad-04-A.csv`、`Good-or-Bad-04-B.csv`、`Good-or-Bad-04-C.csv`、`Good-or-Bad-04-D.csv`和`Good-or-Bad-04-E.csv`，它们位于目录`C:/VKHCG/01-Vermeulen/02-Assess/01-EDS/02-Python`中。我建议您分析每个结果，并理解它们是如何通过代码转换得到的。

## 构建一个实用的Assess超级步骤

我在本书中引用的任何源代码或其他辅助材料，读者都可以通过本书的产品页面在GitHub上获取，该页面位于[`www.apress.com/9781484230534`](http://www.apress.com/9781484230534)。请注意，此源代码假设您已经完成了第2章中概述的源代码配置，因为它会创建您完成示例所需的所有目录。

现在我已经解释了Assess超级步骤的各个方面，我将说明如何帮助我们的公司处理其数据。

### Vermeulen PLC

当我引导您完成公司的Assess超级步骤时，编码将变得越来越复杂。我将全程协助您完成这些探索阶段。Vermeulen PLC有两个我即将引导您完成的主要流程：网络路由和作业调度。

#### 创建网络路由图

接下来的步骤是为公司生成一个完整的网络路由解决方案，以解决数据检索中的数据问题。

**注意**

在接下来的示例中，我关闭了`SettingWithCopyWarning`检查，因为我要对同一数据的副本进行操作：`pd.options.mode.chained_assignment = None`。

打开你的Python编辑器，在目录`C:\VKHCG\01-Vermeulen\02-Assess`下创建一个名为`Assess-Network-Routing-Company.py`的文件。

现在将以下代码复制到文件中：

```python
################################################################
import sys
import os
import pandas as pd
################################################################
pd.options.mode.chained_assignment = None
################################################################
################################################################
if sys.platform == 'linux':
Base=os.path.expanduser('~') + '/VKHCG'
else:
Base='C:/VKHCG'
print('################################')
print('Working Base:',Base,'using',sys.platform)
print('################################') print('################################')
print('Working Base:',Base,'using',sys.platform)
print('################################')
################################################################
sInputFileName1='01-Retrieve/01-EDS/01-R/Retrieve_Country_Code.csv'
sInputFileName2='01-Retrieve/01-EDS/02-Python/Retrieve_Router_Location.csv'
################################################################
sOutputFileName='Assess-Network-Routing-Company.csv'
Company='01-Vermeulen'
################################################################
################################################################
#### 导入国家数据
################################################################
sFileName=Base + '/' + Company + '/' + sInputFileName1
print('################################')
print('加载:',sFileName)
print('################################')
CountryData=pd.read_csv(sFileName,header=0,low_memory=False, encoding="latin-1")
print('已加载国家数据:',CountryData.columns.values)
print('################################')
################################################################
## 评估国家数据
################################################################
print('################################')
print('更改前:',CountryData.columns.values)
```

**提示**

在我的数据科学项目中，我大量使用`pandas`的内置函数来操作数据。探究`pandas`库的功能对未来很有帮助。请参考`https://pandas.pydata.org/pandas-docs/stable/api.html`。

# 数据导入与处理脚本

## 数据预处理

```python
CountryData.rename(columns={'Country': 'Country_Name'}, inplace=True)
CountryData.rename(columns={'ISO-2-CODE': 'Country_Code'}, inplace=True)
CountryData.drop('ISO-M49', axis=1, inplace=True)
CountryData.drop('ISO-3-Code', axis=1, inplace=True)
CountryData.drop('RowID', axis=1, inplace=True)
print('更改后:', CountryData.columns.values)
```

## 导入公司数据

```python
sFileName = Base + '/' + Company + '/' + sInputFileName2
print('加载:', sFileName)
CompanyData = pd.read_csv(sFileName, header=0, low_memory=False, encoding="latin-1")
print('已加载公司数据:', CompanyData.columns.values)
```

## 评估公司数据

```python
print('更改前:', CompanyData.columns.values)
CompanyData.rename(columns={'Country': 'Country_Code'}, inplace=True)
print('更改后:', CompanyData.columns.values)
```

## 导入客户数据

```python
sFileName = Base + '/' + Company + '/' + sInputFileName3
print('加载:', sFileName)
CustomerRawData = pd.read_csv(sFileName, header=0, low_memory=False, encoding="latin-1")
print('已加载客户数据:', CustomerRawData.columns.values)
```

## 数据清洗

```python
CustomerData = CustomerRawData.dropna(axis=0, how="any")
print('删除空白的国家代码')
print('行数从', CustomerRawData.shape[0], '减少到', CustomerData.shape[0])
```

### 客户数据重命名

```python
print('更改前:', CustomerData.columns.values)
CustomerData.rename(columns={'Country': 'Country_Code'}, inplace=True)
print('更改后:', CustomerData.columns.values)
```

## 合并公司和国家数据

```python
print('合并公司和国家数据')
CompanyNetworkData = pd.merge(
    CompanyData,
    CountryData,
    how='inner',
    on='Country_Code'
)
```

## 重命名公司网络数据列

```python
for i in CompanyNetworkData.columns.values:
    j = 'Company_' + i
    CompanyNetworkData.rename(columns={i: j}, inplace=True)
print('更改为', CompanyNetworkData.columns.values)
```

## 保存结果

```python
sFileDir = Base + '/' + Company + '/02-Assess/01-EDS/02-Python'
if not os.path.exists(sFileDir):
    os.makedirs(sFileDir)
sFileName = sFileDir + '/' + sOutputFileName
print('存储:', sFileName)
CompanyNetworkData.to_csv(sFileName, index=False, encoding="latin-1")
```

## 完成

```python
print('### 完成!! #####################')
```

保存 `Assess-Network-Routing-Company.py` 文件，然后使用你的 Python 编译器编译并执行。这将在屏幕上显示一组演示值，并生成一个名为 `Assess-Network-Routing-Company.csv` 的文件。

| Company_Country_Code | Company_Place_Name | Company_Latitude | Company_Longitude | Company_Country_Name |
| --- | --- | --- | --- | --- |
| US | New York | 40.7528 | -73.9725 | United States of America |
| US | New York | 40.7214 | -74.0052 | United States of America |
| US | New York | 40.7662 | -73.9862 | United States of America |

打开你的 Python 编辑器，在目录 `C:\VKHCG\01-Vermeulen\02-Assess` 下创建一个名为 `Assess-Network-Routing-Customer.py` 的文件。接下来，创建一个新文件，然后继续下面的示例。

以下是该示例的代码：

```python
################################################################
import sys
import os
import pandas as pd
################################################################
pd.options.mode.chained_assignment = None
################################################################
if sys.platform == 'linux':
    Base = os.path.expanduser('~') + 'VKHCG'
else:
    Base = 'C:/VKHCG'
print('################################')
print('工作基础目录:', Base, '使用', sys.platform)
print('################################')
################################################################
sInputFileName1 = '01-Retrieve/01-EDS/01-R/Retrieve_Country_Code.csv'
sInputFileName2 = '01-Retrieve/01-EDS/02-Python/Retrieve_All_Router_Location.csv'
################################################################
sOutputFileName = 'Assess-Network-Routing-Customer.csv'
Company = '01-Vermeulen'
################################################################
################################################################
#### 导入国家数据
################################################################
sFileName = Base + '/' + Company + '/' + sInputFileName1
print('################################')
print('正在加载:', sFileName)
print('################################')
CountryData = pd.read_csv(sFileName, header=0, low_memory=False, encoding="latin-1")
print('已加载国家数据列:', CountryData.columns.values)
print('################################')
################################################################
## 评估国家数据
################################################################
print('################################')
print('修改前:', CountryData.columns.values)
CountryData.rename(columns={'Country': 'Country_Name'}, inplace=True)
CountryData.rename(columns={'ISO-2-CODE': 'Country_Code'}, inplace=True)
CountryData.drop('ISO-M49', axis=1, inplace=True)
CountryData.drop('ISO-3-Code', axis=1, inplace=True)
CountryData.drop('RowID', axis=1, inplace=True)
print('修改后:', CountryData.columns.values)
print('################################')
################################################################
#### 导入客户数据
################################################################
sFileName = Base + '/' + Company + '/' + sInputFileName2
print('################################')
print('正在加载:', sFileName)
print('################################')
CustomerRawData = pd.read_csv(sFileName, header=0, low_memory=False, encoding="latin-1")
print('################################')
print('已加载客户数据列:', CustomerRawData.columns.values)
print('################################')
################################################################
CustomerData = CustomerRawData.dropna(axis=0, how="any")
print('################################')
print('移除空白国家代码')
print('行数从', CustomerRawData.shape[0], '减少至', CustomerData.shape[0])
print('################################')
################################################################
print('################################')
print('修改前:', CustomerData.columns.values)
CustomerData.rename(columns={'Country': 'Country_Code'}, inplace=True)
print('修改后:', CustomerData.columns.values)
print('################################')
################################################################
print('################################')
print('合并客户与国家数据')
print('################################')
CustomerNetworkData = pd.merge(
    CustomerData,
    CountryData,
    how='inner',
    on='Country_Code'
)
################################################################
print('################################')
print('修改', CustomerNetworkData.columns.values)
for i in CustomerNetworkData.columns.values:
    j = 'Customer_' + i
    CustomerNetworkData.rename(columns={i: j}, inplace=True)
print('至', CustomerNetworkData.columns.values)
print('################################')
################################################################
sFileDir = Base + '/' + Company + '/02-Assess/01-EDS/02-Python'
if not os.path.exists(sFileDir):
    os.makedirs(sFileDir)
################################################################
sFileName = sFileDir + '/' + sOutputFileName
print('################################')
print('正在存储:', sFileName)
print('################################')
CustomerNetworkData.to_csv(sFileName, index=False, encoding="latin-1")
################################################################
print('################################')
print('### 完成!! #####################')
print('################################')
################################################################
```

保存文件`Assess-Network-Routing-Customer.py`，然后使用 Python 编译器编译并执行。这将在屏幕上生成一组展示值，并生成一个名为`Assess-Network-Routing-Customer.csv`的文件。

| `Customer_Country_Code` | `Customer_Place_Name` | `Customer_Latitude` | `Customer_Longitude` | `Customer_Country_Name` |
| --- | --- | --- | --- | --- |
| BW | Gaborone | -24.6464 | 25.9119 | Botswana |
| BW | Francistown | -21.1667 | 27.5167 | Botswana |
| BW | Maun | -19.9833 | 23.4167 | Botswana |

打开 Python 编辑器，在目录`C:\VKHCG\01-Vermeulen\02-Assess`中创建一个名为`Assess-Network-Routing-Node.py`的文件。然后将以下代码复制到文件中：

```python
################################################################
import sys
import os
import pandas as pd
################################################################
pd.options.mode.chained_assignment = None
################################################################
if sys.platform == 'linux':
Base=os.path.expanduser('~') + 'VKHCG'
else:
Base='C:/VKHCG'
print('################################')
print('Working Base:',Base,'using',sys.platform)
print('################################')
################################################################
sInputFileName='01-Retrieve/01-EDS/02-Python/Retrieve_IP_DATA.csv'
################################################################
sOutputFileName='Assess-Network-Routing-Node.csv'
Company='01-Vermeulen'
################################################################
#### Import IP Data
################################################################
sFileName=Base + '/' + Company + '/' + sInputFileName
print('################################')
print('Loading:',sFileName)
print('################################')
IPData=pd.read_csv(sFileName,header=0,low_memory=False, encoding="latin-1")
print('Loaded IP:', IPData.columns.values)
print('################################')
################################################################
print('################################')
print('Changed:',IPData.columns.values)
IPData.drop('RowID', axis=1, inplace=True)
IPData.drop('ID', axis=1, inplace=True)
IPData.rename(columns={'Country': 'Country_Code'}, inplace=True)
IPData.rename(columns={'Place.Name': 'Place_Name'}, inplace=True)
IPData.rename(columns={'Post.Code': 'Post_Code'}, inplace=True)
IPData.rename(columns={'First.IP.Number': 'First_IP_Number'}, inplace=True)
IPData.rename(columns={'Last.IP.Number': 'Last_IP_Number'}, inplace=True)
print('To:',IPData.columns.values)
print('################################')
################################################################
print('################################')
print('Change',IPData.columns.values)
for i in IPData.columns.values:
j='Node_'+i
IPData.rename(columns={i:j}, inplace=True)
print('To', IPData.columns.values)
print('################################')
################################################################
sFileDir=Base + '/' + Company + '/02-Assess/01-EDS/02-Python'
if not os.path.exists(sFileDir):
os.makedirs(sFileDir)
################################################################
sFileName=sFileDir + '/' + sOutputFileName
print('################################')
print('Storing:', sFileName)
print('################################')
IPData.to_csv(sFileName, index = False, encoding="latin-1")
################################################################
print('################################')
print('### Done!! #####################')
print('################################')
################################################################
```

保存`Assess-Network-Routing-Node.py`文件，然后使用 Python 编译器编译并执行。这将在屏幕上生成一组展示值，并生成一个名为`Assess-Network-Routing-Node.csv`的文件。

| `Node_Country_Code` | `Node_Place_Name` | `Node_Post_Code` | `Node_Latitude` | `Node_Longitude` | `Node_First_IP_Number` | `Node_Last_IP_Number` |
| --- | --- | --- | --- | --- | --- | --- |
| GB | Lerwick | ZE1 | 60.15 | -1.15 | 523478016 | 523479039 |
| GB | Lerwick | ZE1 | 60.15 | -1.15 | 1545369984 | 1545370111 |
| GB | Selby | YO8 | 53.7833 | -1.0667 | 35231232 | 35231359 |

### 图论

# 图论基础简介

在进入示例之前，我先简单介绍一下图论。图用于表示现实世界中实体之间的关系。基本构建块是两个不同的图组件，如下所示。

## 节点（Node）

节点是任意特定的单个实体。例如，在“Andre 吻 Zhaan”中，有两个节点：Andre 和 Zhaan。

![A435693_1_En_8_Fig1_HTML.jpg](img/A435693_1_En_8_Fig1_HTML.jpg)

**图 8-1** 节点 Andre 和 Zhaan

## 边（Edge）

边是任意两个节点之间的特定关系。例如，在“Andre 吻 Zhaan”中，有两个节点，即 Andre 和 Zhaan。边是“吻”。边可以记录为无向的。这将把“吻”记录为“互相亲吻”。

![A435693_1_En_8_Fig2_HTML.jpg](img/A435693_1_En_8_Fig2_HTML.jpg)

**图 8-2** 节点 Andre 和 Zhaan 互相亲吻

边可以记录为有向的。这将把“吻”记录为“吻向”。

![A435693_1_En_8_Fig3_HTML.jpg](img/A435693_1_En_8_Fig3_HTML.jpg)

**图 8-3** 节点 Andre 吻向 Zhaan

这强制规定了边的方向。当处理必须按特定顺序发生的动作，或者需要遵循特定路径时，方向的概念非常有用。下图展示了一个示例。

![A435693_1_En_8_Fig4_HTML.jpg](img/A435693_1_En_8_Fig4_HTML.jpg)

**图 8-4** 边的方向

你只能从 A 经过 B 到达 C，从 A 经过 B 到达 D，从 A 到 B，从 B 到 C，以及从 B 到 D。你不能从 C 到 B，从 D 到 B，从 B 到 A，从 C 到 A，以及从 D 到 A。有向边阻止了这些动作。

## 有向无环图（DAG）

有向无环图是一种特殊图，图中只有一条路径。下图显示了一个属于 `DAG` 的图，而另一个则不属于 `DAG`。

![A435693_1_En_8_Fig6_HTML.jpg](img/A435693_1_En_8_Fig6_HTML.jpg)

**图 8-6** 此图不是 `DAG`

![A435693_1_En_8_Fig5_HTML.jpg](img/A435693_1_En_8_Fig5_HTML.jpg)

**图 8-5** 此图是 `DAG`

图 8-6 不是 `DAG` 的原因是在 A 和 C 之间有两条路径：A 到 C，以及 A 经过 B 到 C。现在你已经了解了基础知识，接下来让我展示如何在数据科学中使用它。

# 为作业调度构建 DAG

以下示例说明了如何安排网络设备维护作业，以便在一个月内执行所需的维修。为了创建 DAG，我首先要定义它是什么：DAG 是一种数据结构，它使您能够在只能按特定顺序执行的数据条目之间生成关系。DAG 是核心数据科学环境中的重要结构，因为它是使 `Hadoop` 中的 `Spark`、`Pig` 和 `Tez` 等工具能够工作的基本结构。它也用于记录任务调度和处理数据时的流程相互依赖关系。

我将介绍一个名为 `networkx` 的新 Python 库。

## 准备工作

打开您的 Python 编辑器，并在目录 `..\VKHCG\01-Vermeulen\02-Assess` 中创建一个名为 `Assess-DAG-Location.py` 的文件。现在将以下代码复制到该文件中：

```python
################################################################
import networkx as nx
import matplotlib.pyplot as plt
import sys
import os
import pandas as pd
################################################################
if sys.platform == 'linux':
Base=os.path.expanduser('~') + 'VKHCG'
else:
Base='C:/VKHCG'
print('################################')
print('Working Base:',Base,'using',sys.platform)
print('################################')
################################################################
sInputFileName='01-Retrieve/01-EDS/02-Python/Retrieve_Router_Location.csv'
sOutputFileName1='Assess-DAG-Company-Country.png'
sOutputFileName2='Assess-DAG-Company-Country-Place.png'
Company='01-Vermeulen'
################################################################
### Import Company Data
################################################################
sFileName=Base + '/' + Company + '/' + sInputFileName
print('################################')
print('Loading:',sFileName)
print('################################')
CompanyData=pd.read_csv(sFileName,header=0,low_memory=False, encoding="latin-1")
print('Loaded Company:',CompanyData.columns.values)
print('################################')
################################################################
print(CompanyData)
print('################################')
print('Rows:',CompanyData.shape[0])
print('################################')
```

## 构建第一个有向图

我们现在将创建两个有向图：`G1` 和 `G2`。这需要使用 `G=DiGraph()`，如果只想要一个图，则使用 `G=Graph()`。

```python
################################################################
G1=nx.DiGraph()
G2=nx.DiGraph()
################################################################
```

我们现在将为数据中的每个地点创建一个节点。为此，请使用 `G.add_node()` 函数。

> **注意**
>
> 如果你执行 `G.add_node("A")`，然后再执行 `G.add_node("A")`，你只会得到一个节点：`"A"`。该图会自动解析任何重复的节点请求。

我们现在将遍历所有国家和地点-名称-国家的记录。

```python
for i in range(CompanyData.shape[0]):
    G1.add_node(CompanyData['Country'][i])
    sPlaceName= CompanyData['Place_Name'][i] + '-' + CompanyData['Country'][i]
    G2.add_node(sPlaceName)
```

我们现在将通过成对循环所有节点（`G.nodes()`）来互连所有节点。

```python
print('################################')
for n1 in G1.nodes():
    for n2 in G1.nodes():
        if n1 != n2:
            print('Link:',n1,' to ', n2)
            G1.add_edge(n1,n2)
print('################################')
```

现在，我们可以看到您创建的节点和边了。

```python
print('################################')
print("Nodes of graph:")
print(G1.nodes())
print("Edges of graph:")
print(G1.edges())
print('################################')
```

我们现在可以保存我们的图，如下所示：

```python
################################################################
sFileDir=Base + '/' + Company + '/02-Assess/01-EDS/02-Python'
if not os.path.exists(sFileDir):
    os.makedirs(sFileDir)
################################################################
sFileName=sFileDir + '/' + sOutputFileName1
print('################################')
print('Storing:', sFileName)
print('################################')
```

现在，您可以将图形显示为图片。

```python
nx.draw(G1,pos=nx.spectral_layout(G1),
        nodecolor='r',edge_color='b',
        with_labels=True,node_size=8000,
        font_size=12)
plt.savefig(sFileName) # save as png
plt.show() # display
################################################################
```

现在，您可以完成第二个图了。

# 构建与绘制图形

```
print('################################')
for n1 in G2.nodes():
    for n2 in G2.nodes():
        if n1 != n2:
            print('Link:',n1,'to',n2)
            G2.add_edge(n1,n2)
print('################################')
print('################################')
print("Nodes of graph:")
print(G2.nodes())
print("Edges of graph:")
print(G2.edges())
print('################################')
################################################################
sFileDir=Base + '/' + Company + '/02-Assess/01-EDS/02-Python'
if not os.path.exists(sFileDir):
    os.makedirs(sFileDir)
################################################################
sFileName=sFileDir + '/' + sOutputFileName2
print('################################')
print('Storing:', sFileName)
print('################################')
nx.draw(G2,pos=nx.spectral_layout(G2),
        nodecolor='r',edge_color='b',
        with_labels=True,node_size=8000,
        font_size=12)
plt.savefig(sFileName) # save as png
plt.show() # display
################################################################
```

保存`Assess-DAG-Location.py`文件，然后用你的`Python`编译器编译并执行。这将在屏幕上生成一组演示值，以及两个图形文件，分别名为`Assess-DAG-Company-Country.png`（图 8-7）和`Assess-DAG-Company-Country-Place.png`（图 8-8）。

![A435693_1_En_8_Fig8_HTML.jpg](img/A435693_1_En_8_Fig8_HTML.jpg)

**图 8-8** `Assess-DAG-Company-Country-Place.png`文件

![A435693_1_En_8_Fig7_HTML.jpg](img/A435693_1_En_8_Fig7_HTML.jpg)

**图 8-7** `Assess-DAG-Company-Country.png`文件

干得好。您刚刚完成了一个简单但有效的图。让我们尝试一个更大的图。

> **记住**
>
> 只有两个实体。大小对构建方式没有直接影响。

> **注意**
>
> `networkx`库可以很好地处理相当大的图，但它受限于您的内存。我的两个 32 核 `AMD` CPU 搭配 `1TB DRAM5` 可以轻松容纳 1 亿个节点和边。但是，当我处理超过 10 亿个节点和边时，我会切换到分布式图引擎，使用我的 12 节点 `DataStax Enterprise` 数据平台，并通过`dse-graph`库，利用`DSE Graph`来处理图。我可以确认，本书中的示例在我的四核 4MB `RAM` 笔记本电脑上可以轻松运行。

## 编写GPS数据处理脚本

打开您的`Python`编辑器，并在目录`C:\VKHCG\01-Vermeulen\02-Assess`中创建一个名为`Assess-DAG-GPS.py`的文件。现在将以下代码复制到该文件中：

```
################################################################
import networkx as nx
import matplotlib.pyplot as plt
import sys
import os
import pandas as pd
################################################################
if sys.platform == 'linux':
    Base=os.path.expanduser('~') + 'VKHCG'
else:
    Base='C:/VKHCG'
print('################################')
print('Working Base:',Base,'using',sys.platform)
print('################################')
################################################################
sInputFileName='01-Retrieve/01-EDS/02-Python/Retrieve_Router_Location.csv'
sOutputFileName='Assess-DAG-Company-GPS.png'
Company='01-Vermeulen'
################################################################
### Import Company Data
################################################################
sFileName=Base + '/' + Company + '/' + sInputFileName
print('################################')
print('Loading:',sFileName)
print('################################')
CompanyData=pd.read_csv(sFileName,header=0,low_memory=False, encoding="latin-1")
print('Loaded Company:',CompanyData.columns.values)
print('################################')
################################################################
print(CompanyData)
print('################################')
print('Rows:',CompanyData.shape[0])
print('################################')
################################################################
G=nx.Graph()
################################################################
```

> **提示**
>
> 我在以下代码中使用了一个简单的数据科学技术，即在纬度和经度值上添加了`round(x,1)`。这将所需节点数从 150 个减少到 16 个，节省了 90% 的节点处理量。边数从 11,175 条减少到 120 条，节省了 99% 的边处理量。如果您使用`round(x,2)`，您会得到 117 个节点和 6786 条边，分别节省了 22% 和 39%。当您只想在预估水平上测试发现时，这是一个有用的技术。

保存文件 `Assess-DAG-GPS.py`，然后用你的 Python 编译器编译并执行。这将在屏幕上生成一组演示值，以及一个名为 `Assess-DAG-Company-GPS.png` 的图形文件（图 8-9）。

![A435693_1_En_8_Fig9_HTML.jpg](img/A435693_1_En_8_Fig9_HTML.jpg)

**图 8-9** `Assess-DAG-Company-GPS.png` 文件

我已经引导您了解了使用图论数据结构评估数据实体之间关系是否正确的概念。现在，我将演示如何将我们检索到的 IP 数据转换为有效的路由网络（图 8-10）。

![A435693_1_En_8_Fig10_HTML.jpg](img/A435693_1_En_8_Fig10_HTML.jpg)

**图 8-10** 有效的路由网络

基本验证如下：

- 国家之间相互连接，形成一个网状网络。

- 国家内的地点连接到所有国内节点，即国内所有网络连接集中在一起的某个点，称为“地点集中器”。

- 邮政编码只能通过邮政编码集中器（类似于地点集中器的设备，但每个邮政编码一个）连接到地点集中器。

- 位置只能连接到其邮政编码集中器。

> **注意**
>
> 互连节点的规则不止一套。然而，当应用所有规则时，结果仍然只有一个图。如果规则之间存在重叠连接，图将只解析一种关系。这使您免于解决重叠问题。记住这点不错！

现在，用这个例子来测试一下您的技能。我将引导您完成 `Retrieve_IP_DATA_CORE.csv` 的评估步骤。打开您的 Python 编辑器，并在目录 `C:\VKHCG\01-Vermeulen\02-Assess` 中创建一个名为 `Assess-DAG-Schedule.py` 的文件。

现在将此代码复制到以下文件中：

```python
################################################################
import networkx as nx
import matplotlib.pyplot as plt
import sys
import os
import pandas as pd
################################################################
if sys.platform == 'linux':
    Base=os.path.expanduser('~') + 'VKHCG'
else:
    Base='C:/VKHCG'
print('################################')
print('Working Base:',Base,'using',sys.platform)
print('################################')
################################################################
sInputFileName='01-Retrieve/01-EDS/01-R/Retrieve_IP_DATA_CORE.csv'
sOutputFileName='Assess-DAG-Schedule.gml'
Company='01-Vermeulen'
################################################################
### Import Core Company Data
################################################################
sFileName=Base + '/' + Company + '/' + sInputFileName
print('################################')
print('Loading:',sFileName)
print('################################')
CompanyData=pd.read_csv(sFileName,header=0,low_memory=False, encoding="latin-1")
print('Loaded Company:',CompanyData.columns.values)
print('################################')
################################################################
print(CompanyData)
print('################################')
print('Rows:',CompanyData.shape[0])
print('################################')
################################################################
G=nx.Graph()
################################################################
for i in range(CompanyData.shape[0]):
    sGroupName0= str(CompanyData['Country'][i])
    sGroupName1= str(CompanyData['Place.Name'][i])
    sGroupName2= str(CompanyData['Post.Code'][i])
    nLatitude=round(CompanyData['Latitude'][i],6)
    nLongitude=round(CompanyData['Longitude'][i],6)
```

> **注意**
>
>

> 您可以在这里看到，您可以在节点和边上存储额外信息，以增强图中存储的数据，同时仍不会导致基本的节点-边关系出现问题。

您可以通过使用 `G.add_node()` 特性的扩展来添加额外信息。

```
CountryName=sGroupName0
print('Add Node:',sGroupName0)
G.add_node(CountryName,
routertype='CountryName',
group0=sGroupName0)
sPlaceName= sGroupName1 + '-' + sGroupName0
G.add_node(sPlaceName,
routertype='PlaceName',
group0=sGroupName0,
group1=sGroupName1)
sPostCodeName= sGroupName1 + '-' + sGroupName2 + '-' + sGroupName0
print('Add Node:',sPostCodeName)
G.add_node(sPostCodeName,
routertype='PostCode',
group0=sGroupName0,
group1=sGroupName1,
group2=sGroupName2)
if nLatitude < 0:
sLatitude = str(nLatitude*-1) + 'S'
else:
sLatitude = str(nLatitude) + 'N'
if nLongitude < 0:
sLongitude = str(nLongitude*-1) + 'W'
else:
sLongitude = str(nLongitude) + 'E'
sGPS= sLatitude + '-' + sLongitude
print('Add Node:',sGPS)
G.add_node(sGPS,routertype='GPS',
group0=sGroupName0,
group1=sGroupName1,
group2=sGroupName2,
sLatitude=sLatitude,
sLongitude=sLongitude,
nLatitude=nLatitude,
nLongitude=nLongitude)
################################################################
```

您通过使用 `G.node[][]` 特性的扩展来使用额外信息。同样要注意 `nodes_iter(G)`，它让您能够访问所有节点。我们现在将在节点之间添加规则。

```
print('################################')
print('Link Country to Country')
print('################################')
for n1 in nx.nodes_iter(G):
    if G.node[n1]['routertype'] == 'CountryName':
        for n2 in nx.nodes_iter(G):
            if G.node[n2]['routertype'] == 'CountryName':
                if n1 != n2:
                    print('Link:',n1,'to',n2)
                    G.add_edge(n1,n2)
print('################################')
print('################################')
print('Link Country to Place')
print('################################')
for n1 in nx.nodes_iter(G):
    if G.node[n1]['routertype'] == 'CountryName':
        for n2 in nx.nodes_iter(G):
            if G.node[n2]['routertype'] == 'PlaceName':
                if G.node[n1]['group0'] == G.node[n2]['group0']:
                    if n1 != n2:
                        print('Link:',n1,'to',n2)
                        G.add_edge(n1,n2)
print('################################')
print('################################')
print('Link Place to Post Code')
print('################################')
for n1 in nx.nodes_iter(G):
    if G.node[n1]['routertype'] == 'PlaceName':
        for n2 in nx.nodes_iter(G):
            if G.node[n2]['routertype'] == 'PostCode':
                if G.node[n1]['group0'] == G.node[n2]['group0']:
                    if G.node[n1]['group1'] == G.node[n2]['group1']:
                        if n1 != n2:
                            print('Link:',n1,'to',n2)
                            G.add_edge(n1,n2)
print('################################')
print('################################')
print('Link Post Code to GPS')
print('################################')
for n1 in nx.nodes_iter(G):
    if G.node[n1]['routertype'] == 'PostCode':
        for n2 in nx.nodes_iter(G):
            if G.node[n2]['routertype'] == 'GPS':
                if G.node[n1]['group0'] == G.node[n2]['group0']:
                    if G.node[n1]['group1'] == G.node[n2]['group1']:
                        if G.node[n1]['group2'] == G.node[n2]['group2']:
                            if n1 != n2:
                                print('Link:',n1,'to',n2)
                                G.add_edge(n1,n2)
print('################################')
print('################################')
print("Nodes of graph:",nx.number_of_nodes(G))
print("Edges of graph:",nx.number_of_edges(G))
print('################################')
################################################################
sFileDir=Base + '/' + Company + '/02-Assess/01-EDS/02-Python'
if not os.path.exists(sFileDir):
    os.makedirs(sFileDir)
################################################################
sFileName=sFileDir + '/' + sOutputFileName
print('################################')
print('Storing:', sFileName)
print('################################')
nx.write_gml(G,sFileName)
sFileName=sFileName +'.gz'
nx.write_gml(G,sFileName)
################################################################
```

保存 `Assess-DAG-Schedule.py` 文件，然后用你的 Python 编译器编译并执行。这将在屏幕上生成一组演示值，以及一个名为 `Assess-DAG-Schedule.gml` 的图形数据文件。

> **提示**
>
> 您可以使用文本编辑器查看 GML 格式。我经常用它来查找图的错误。

如果您用文本编辑器打开该文件，可以看到它生成的格式。这种格式简单但有效。这是一个邮政编码节点：

```
node [
    id 327
    label "Munich-80331-DE"
    routertype "PostCode"
    group0 "DE"
    group1 "Munich"
    group2 "80331"
]
```

这是一个 GPS 节点：

### node

```dot
node [
id 328
label "48.1345 N-11.571 E"
routertype "GPS"
group0 "DE"
group1 "Munich"
group2 "80331"
sLatitude "48.1345 N"
sLongitude "11.571 E"
nLatitude 48.134500000000003
nLongitude 11.571
]
```

### edge

这是一条连接两个节点的边：

```dot
edge [
source 327
target 328
]
```

此转换到图形数据结构现在已经验证了全面的 `IP` 结构，方法是消除由不同规则或基础数据中的任何重复条目创建的任何重复节点或边。当有多个系统向同一系统加载请求时，这种自然的去重功能非常有用。

例如：两个运输代理可以请求一辆卡车将两个不同的箱子从地点 A 运送到地点 B。在卡车离开之前，物流程序将创建一个运输图，然后发送一辆卡车运送这两个箱子。

我已经证明了这在较小的 `IP` 核心数据上是可行的。现在，我们可以将其应用于更大的 `IP` 数据集 `Retrieve_IP_DATA.csv`。

考虑到更大数据集的范围，我建议我们通过更改输入和导出源设计来包含 `SQLite` 数据库，以协助解决数据处理问题，从而处理这些数据。作为一名数据科学家，您应该不断探索可用的工具，以提高解决方案的有效性和效率。我将引导您完成以下处理模式，使您能够理解我如何利用数据库引擎的优势来解决更大数据集完成某些关系所需的复杂性。我们将把图数据结构的处理能力与 `SQLite` 数据库的处理能力结合起来。

> **注意**：如果您的系统上尚未使用 `SQLite`，则必须安装它。请参阅 [www.sqlite.org](http://www.sqlite.org) 了解如何实现此安装的详细信息。您可以通过使用 `sqlite3` 版本来检查安装。如果得到 `3.20.x` 的结果，您就可以继续了。确保您也安装了 `python` `SQLite` 库。在您的 `IPython` 控制台中执行 `import SQLite`。如果这不起作用，请执行库安装。

打开您的 `Python` 编辑器，并在目录 `C:\VKHCG\01-Vermeulen\02-Assess` 中创建一个名为 `Retrieve-IP_2_SQLite_2_DAG.py` 的文件。现在将以下代码复制到该文件中：

```python
################################################################
# -*- coding: utf-8 -*-
################################################################
import networkx as nx
import sys
import os
import sqlite3 as sq
import pandas as pd
################################################################
if sys.platform == 'linux':
Base=os.path.expanduser('~') + 'VKHCG'
else:
Base='C:/VKHCG'
print('################################')
print('Working Base:',Base,'using',sys.platform)
print('################################')
################################################################
sDatabaseName=Base + '/01-Vermeulen/00-RawData/SQLite/vermeulen.db'
conn = sq.connect(sDatabaseName)
################################################################
sFileName=Base + '/01-Vermeulen/01-Retrieve/01-EDS/02-Python/Retrieve_IP_DATA.csv'
sOutputFileName='Assess-DAG-Schedule-All.csv'
Company='01-Vermeulen'
```

现在，我们将基础数据作为 `pandas` 数据结构加载到内存中，然后使用 `df.tosql()` 命令将其存储到 `SQLite` 数据库中。

```python
################################################################
print('Loading :',sFileName)
IP_DATA_ALL=pd.read_csv(sFileName,header=0,low_memory=False, encoding="latin-1")
IP_DATA_ALL.index.names = ['RowIDCSV']
IP_DATA_ALL.rename(columns={'Place.Name': 'PlaceName'}, inplace=True)
IP_DATA_ALL.rename(columns={'Post.Code': 'PostCode'}, inplace=True)
#print(IP_DATA_ALL)
print('################')
sTable='Assess_IP_DATA'
print('Storing:',sDatabaseName,'Table:',sTable)
IP_DATA_ALL.to_sql(sTable, conn, if_exists="replace")
print('################')
```

我们使用 `G=nx.Graph()` 创建一个无向图。

```python
################################################################
G=nx.Graph()
################################################################
```

现在，我们从 `SQLite` 创建已加载数据的一个子集，以匹配您所需的格式。

> **提示**：这种将处理任务卸载到第三方处理引擎的方式，使您能够连接到核心资源（例如数据库），然后执行复杂的连接和更繁重的处理。我通常也会通过 `DSE Graph` 以相同的方式卸载我的图处理，以增强更复杂的图。能够成功卸载复杂的数据处理是数据科学家应该具备的一项好技能。

> **注意**：我可以使用像 `树莓派 3` 这样的小型计算机来驱动我的集群，轻松执行大规模处理任务。

那么，让我们重新开始我们的例子。您必须从一个包含重复数据的数据集返回不重复的国家列表。我建议您使用 `SQL DISTINCT` 命令，通过 `SQLite` 数据引擎来执行此任务。

```python
print('################')
sTable = 'Assess_IP_DATA'
print('Loading:',sDatabaseName,'Table:',sTable)
sSQL="select distinct"
sSQL=sSQL+ "A.Country,"
sSQL=sSQL+ "A.Country AS NodeName,"
sSQL=sSQL+ "A.Country AS GroupName0,"
sSQL=sSQL+ "'Country-Router' AS RouterType"
sSQL=sSQL+ "from"
sSQL=sSQL+ "Assess_IP_DATA as A"
sSQL=sSQL+ "ORDER BY A.Country;"
CompanyData=pd.read_sql_query(sSQL, conn)
print('################')
```

您现在在 `CompanyData` 中有了不重复的国家列表。干得好。

您现在需要将这些节点添加到您之前创建的图中。您之前已经完成过这个任务，所以只需遵循以下代码：

## 创建国家节点

干得好，您在这里取得了进展。您拥有了所有的国家节点。

我建议我们将国家列表存储为表 `Assess_IP_Country` 以备后用。

```python
for i in range(CompanyData.shape[0]):
    sNode=str(CompanyData['NodeName'][i])
    sRouterType=str(CompanyData['RouterType'][i])
    sGroupName0=str(CompanyData['GroupName0'][i])
    G.add_node(sNode,
        routertype=sRouterType,
        group0=sGroupName0)
    print('################################')
    print("Nodes of graph:",nx.number_of_nodes(G))
    print("Edges of graph:",nx.number_of_edges(G))
    print('################################')
```

```python
print('################')
sTable='Assess_IP_Country'
print('Storing:',sDatabaseName,'Table:',sTable)
CompanyData.to_sql(sTable, conn, if_exists="replace")
```

## 提取不重复的国家和地点

既然您已经掌握了基本流程，您会发现重复这个过程很容易。接下来，提取不重复的国家和地点，添加节点，并将其存储为表 `Assess_IP_PlaceName`。

```python
print('################')
################################################################
print('################')
sTable = 'Assess_IP_DATA'
print('Loading:',sDatabaseName,'Table:',sTable)
sSQL="select distinct"
sSQL=sSQL+ "A.Country,"
sSQL=sSQL+ "A.PlaceName,"
sSQL=sSQL+ "A.PlaceName || '-' || A.Country AS NodeName,"
sSQL=sSQL+ "A.Country AS GroupName0,"
sSQL=sSQL+ "A.PlaceName AS GroupName1,"
sSQL=sSQL+ "'Place-Router' AS RouterType"
sSQL=sSQL+ "from"
sSQL=sSQL+ "Assess_IP_DATA as A"
sSQL=sSQL+ "ORDER BY A.Country AND"
sSQL=sSQL+ "A.PlaceName;"
CompanyData=pd.read_sql_query(sSQL, conn)
print('################')
for i in range(CompanyData.shape[0]):
    sNode=str(CompanyData['NodeName'][i])
    sRouterType=str(CompanyData['RouterType'][i])
    sGroupName0= str(CompanyData['Country'][i])
    sGroupName1= str(CompanyData['PlaceName'][i])
    G.add_node(sNode,
        routertype=sRouterType,
        group0=sGroupName0,
        group1=sGroupName1)
    print('################################')
    print("Nodes of graph:",nx.number_of_nodes(G))
    print("Edges of graph:",nx.number_of_edges(G))
    print('################################')
print('################')
sTable='Assess_IP_PlaceName'
print('Storing:',sDatabaseName,'Table:',sTable)
CompanyData.to_sql(sTable, conn, if_exists="replace")
print('################')
################################################################
```

您现在在流程中取得了更多成功。您能看出使用数据库执行工作的优势了吗？

## 处理邮政编码数据

接下来，对选定的国家、地点名称和节点名称数据重复此操作。添加节点并将表存储为 `Assess_IP_PostCode`。

```python
print('################')
sTable = 'Assess_IP_DATA'
print('Loading:',sDatabaseName,'Table:',sTable)
sSQL="select distinct"
sSQL=sSQL+ "A.Country,"
sSQL=sSQL+ "A.PlaceName,"
sSQL=sSQL+ "A.PostCode,"
sSQL=sSQL+ "A.PlaceName || '-' || A.PostCode || '-' || A.Country AS NodeName,"
sSQL=sSQL+ "A.Country AS GroupName0,"
sSQL=sSQL+ "A.PlaceName AS GroupName1,"
sSQL=sSQL+ "A.PostCode AS GroupName2,"
sSQL=sSQL+ "'Place-Router' AS RouterType"
sSQL=sSQL+ "from"
sSQL=sSQL+ "Assess_IP_DATA as A"
sSQL=sSQL+ "ORDER BY A.Country AND"
sSQL=sSQL+ "A.PlaceName AND"
sSQL=sSQL+ "A.PostCode;"
CompanyData=pd.read_sql_query(sSQL, conn)
print('################')
for i in range(CompanyData.shape[0]):
    sNode=str(CompanyData['NodeName'][i])
    sRouterType=str(CompanyData['RouterType'][i])
    sGroupName0= str(CompanyData['GroupName0'][i])
    sGroupName1= str(CompanyData['GroupName1'][i])
    sGroupName2= str(CompanyData['GroupName2'][i])
    G.add_node(sNode,
        routertype=sRouterType,
        group0=sGroupName0,
        group1=sGroupName1,
        group2=sGroupName2)
    print('################################')
    print("Nodes of graph:",nx.number_of_nodes(G))
    print("Edges of graph:",nx.number_of_edges(G))
    print('################################')
print('################')
sTable='Assess_IP_PostCode'
print('Storing:',sDatabaseName,'Table:',sTable)
CompanyData.to_sql(sTable, conn, if_exists="replace")
print('################')
################################################################
```

## 处理GPS数据

接下来，对选定的国家、地点名称、邮政编码、纬度和经度数据重复此操作。添加节点并将表存储为 `Assess_IP_GPS`。

```python
print('################')
sTable = 'Assess_IP_DATA'
print('Loading:',sDatabaseName,'Table:',sTable)
sSQL="select distinct"
sSQL=sSQL+ "A.Country,"
sSQL=sSQL+ "A.PlaceName,"
sSQL=sSQL+ "A.PostCode,"
sSQL=sSQL+ "A.Latitude,"
sSQL=sSQL+ "A.Longitude,"
sSQL=sSQL+ "(CASE WHEN A.Latitude < 0 THEN"
sSQL=sSQL+ "'S' || ABS(A.Latitude)"
sSQL=sSQL+ "ELSE "
sSQL=sSQL+ "'N' || ABS(A.Latitude)"
sSQL=sSQL+ "END ) AS sLatitude,"
sSQL=sSQL+ "(CASE WHEN A.Longitude < 0 THEN"
sSQL=sSQL+ "'W' || ABS(A.Longitude)"
sSQL=sSQL+ "ELSE"
sSQL=sSQL+ "'E' || ABS(A.Longitude)"
sSQL=sSQL+ "END ) AS sLongitude"
sSQL=sSQL+ "from"
sSQL=sSQL+ "Assess_IP_DATA as A;"
CompanyData=pd.read_sql_query(sSQL, conn)
print('################')
sTable='Assess_IP_GPS'
CompanyData.to_sql(sTable, conn, if_exists="replace")
sSQL="select distinct"
sSQL=sSQL+ "A.Country,"
sSQL=sSQL+ "A.PlaceName,"
sSQL=sSQL+ "A.PostCode,"
sSQL=sSQL+ "A.Latitude,"
sSQL=sSQL+ "A.Longitude,"
sSQL=sSQL+ "A.sLatitude,"
sSQL=sSQL+ "A.sLongitude,"
sSQL=sSQL+ "A.sLatitude || '-' || A.sLongitude AS NodeName,"
sSQL=sSQL+ "A.Country AS GroupName0,"
sSQL=sSQL+ "A.PlaceName AS GroupName1,"
sSQL=sSQL+ "A.PostCode AS GroupName2,"
sSQL=sSQL+ "'GPS-Client' AS RouterType"
sSQL=sSQL+ "from"
sSQL=sSQL+ "Assess_IP_GPS as A"
sSQL=sSQL+ "ORDER BY A.Country AND"
sSQL=sSQL+ "A.PlaceName AND"
sSQL=sSQL+ "A.PostCode AND"
sSQL=sSQL+ "A.Latitude AND"
sSQL=sSQL+ "A.Longitude;"
CompanyData=pd.read_sql_query(sSQL, conn)
for i in range(CompanyData.shape[0]):
sNode=str(CompanyData['NodeName'][i])
sRouterType=str(CompanyData['RouterType'][i])
sGroupName0= str(CompanyData['GroupName0'][i])
sGroupName1= str(CompanyData['GroupName1'][i])
sGroupName2= str(CompanyData['GroupName2'][i])
nLatitude=round(CompanyData['Latitude'][i],6)
nLongitude=round(CompanyData['Longitude'][i],6)
sLatitude= str(CompanyData['sLatitude'][i])
sLongitude= str(CompanyData['sLongitude'][i])
G.add_node(sNode,
routertype=sRouterType,
group0=sGroupName0,
group1=sGroupName1,
group2=sGroupName2,
sLatitude=sLatitude,
sLongitude=sLongitude,
nLatitude=nLatitude,
nLongitude=nLongitude)
print('################################')
print("Nodes of graph:",nx.number_of_nodes(G))
print("Edges of graph:",nx.number_of_edges(G))
print('################################')
print('################')
sTable='Assess_IP_GPS'
print('Storing:',sDatabaseName,'Table:',sTable)
CompanyData.to_sql(sTable, conn, if_exists="replace")
print('################')
###############################################################
```

干得好。您已经创建了所有节点。现在您将看到使用数据库的真正威力。

我们必须将每个国家与所有其他国家链接起来，但不能将国家与自己链接。如果没有我们在加载节点时创建的表，这项任务会很复杂。现在，我们可以对`Assess_IP_Country`执行两次连接，并通过添加一个额外的条件（即一个国家小于另一个国家）来在选取过程中增加一个增强，只创建单向边。使用`Graph()`类型的图可以免费提供`add_edge()`在相反方向的链接。因此，我们节省了大约 +/- 50% 的处理工作量。那么，继续示例来链接不同类型的节点。

# 克伦瓦尔纳股份公司

克伦瓦尔纳股份公司是负责处理我们所有媒体业务的德国公司。我们将用这家公司来演示三种基本的评估解决方案。

## 为广告牌挑选内容

克伦瓦尔纳负责在集团拥有的一系列广告牌上发布客户的广告。我将解释评估与广告牌相关信息所需的概念，这些信息是我们必须处理的。

你将使用 `sqlite3` 和 `pandas` 包来创建一个解决方案。你之前已经执行过与弗穆伦公司类似的步骤，所以下面这个示例你应该能够处理。基本流程是将两组数据合并，然后根据访问德国广告牌的 IP 地址范围，计算每日访客数量。

打开你的 Python 编辑器，在目录 `C:\VKHCG\02-Krennwallner\02-Assess` 中创建一个名为 `Assess-DE-Billboard.py` 的文件。然后将以下代码复制到文件中：

```python
################################################################
import sys
import os
import sqlite3 as sq
import pandas as pd
################################################################
if sys.platform == 'linux':
    Base=os.path.expanduser('~') + 'VKHCG'
else:
    Base='C:/VKHCG'
print('################################')
print('Working Base:',Base,'using',sys.platform)
print('################################')
################################################################
```

我们需要合并的两个数据源如下所示：

```python
sInputFileName1='01-Retrieve/01-EDS/02-Python/Retrieve_DE_Billboard_Locations.csv'
sInputFileName2='01-Retrieve/01-EDS/02-Python/Retrieve_Online_Visitor.csv'
```

结果将存储在此输出文件中：

```python
sOutputFileName='Assess-DE-Billboard-Visitor.csv'
Company='02-Krennwallner'
################################################################
sDataBaseDir=Base + '/' + Company + '/02-Assess/SQLite'
if not os.path.exists(sDataBaseDir):
    os.makedirs(sDataBaseDir)
################################################################
```

以下是为该示例处理过程提供支持的数据库：

```python
sDatabaseName=sDataBaseDir + '/krennwallner.db'
conn = sq.connect(sDatabaseName)
################################################################
################################################################
# 导入广告牌数据
################################################################
sFileName=Base + '/' + Company + '/' + sInputFileName1
print('################################')
print('Loading:',sFileName)
print('################################')
```

加载第一个文件并清理重复项。

```python
BillboardRawData=pd.read_csv(sFileName,header=0,low_memory=False, encoding="latin-1")
BillboardRawData.drop_duplicates(subset=None, keep="first", inplace=True)
BillboardData=BillboardRawData
print('Loaded Company:',BillboardData.columns.values)
print('################################')
################################################################
```

将清洗后的数据存储到 SQLite 中。

```python
print('################')
sTable='Assess_BillboardData'
print('Storing:',sDatabaseName,'Table:',sTable)
BillboardData.to_sql(sTable, conn, if_exists="replace")
print('################')
```

---

# 链接国家到国家

```python
print('################################')
print('Link Country to Country')
print('################################')
print('################')
print('Loading:',sDatabaseName,'Table:',sTable)
sSQL="select distinct"
sSQL=sSQL+ "A.NodeName as N1,"
sSQL=sSQL+ "B.NodeName as N2"
sSQL=sSQL+ "from"
sSQL=sSQL+ "Assess_IP_Country as A,"
sSQL=sSQL+ "Assess_IP_Country as B"
sSQL=sSQL+ "WHERE "
sSQL=sSQL+ "A.Country  B.NodeName;"
CompanyData=pd.read_sql_query(sSQL, conn)
print('################')
n1= str(CompanyData['N1'][i])
n2= str(CompanyData['N2'][i])
print('Link Country:',n1,'to Country:',n2)
G.add_edge(n1,n2)
print('################################')
print('################################')
print("Nodes of graph:",nx.number_of_nodes(G))
print("Edges of graph:",nx.number_of_edges(G))
print('################################')
```

# 链接国家到地点

```python
###############################################################
print('################################')
print('Link Country to Place')
print('################################')
print('################')
print('Loading:',sDatabaseName,'Table:',sTable)
sSQL="select distinct"
sSQL=sSQL+ "A.NodeName as N1,"
sSQL=sSQL+ "B.NodeName as N2"
sSQL=sSQL+ "from"
sSQL=sSQL+ "Assess_IP_Country as A,"
sSQL=sSQL+ "Assess_IP_PlaceName as B"
sSQL=sSQL+ "WHERE "
sSQL=sSQL+ "A.Country = B.Country AND"
sSQL=sSQL+ "A.NodeName  B.NodeName;"
CompanyData=pd.read_sql_query(sSQL, conn)
print('################')
for i in range(CompanyData.shape[0]):
    n1= str(CompanyData['N2'][i])
    n2= str(CompanyData['N2'][i])
```

## 数据处理流程

### 导入数据

打印`BillboardData`的前几行和行数。

```python
print(BillboardData.head())

print('################################')

print('Rows:', BillboardData.shape[0])

print('################################')
```

加载第二个文件并清理重复项。

```python
sFileName = Base + '/' + Company + '/' + sInputFileName2

print('################################')

print('Loading:', sFileName)

print('################################')

VisitorRawData = pd.read_csv(sFileName, header=0, low_memory=False, encoding="latin-1")

VisitorRawData.drop_duplicates(subset=None, keep="first", inplace=True)
```

### 清理与过滤

利用 `country=DE` 的结果，你现在可以将数据限制为仅包含德国数据。尽早丢弃多余数据是高效数据科学的关键一环。

**提示**

将调查中的数据集保持在所需的最小大小，这可以为您和您的客户节省时间和金钱。

```python
VisitorData = VisitorRawData[VisitorRawData.Country=='DE']

print('Loaded Company:', VisitorData.columns.values)

print('################################')
```

将第二组干净加载的数据存储到`SQLite`中。

```python
print('################')

sTable = 'Assess_VisitorData'

print('Storing:', sDatabaseName, 'Table:', sTable)

VisitorData.to_sql(sTable, conn, if_exists="replace")

print('################')
```

打印`VisitorData`的前几行和行数。

```python
print(VisitorData.head())

print('################################')

print('Rows:', VisitorData.shape[0])

print('################################')
```

### 合并与存储

从`SQLite`中检索间接清洗的数据集，因为您已经存储了干净数据。第二个数据集的缩减也提高了流程效率。

```python
print('################')

sTable = 'Assess_BillboardVisitorData'

print('Loading:', sDatabaseName, 'Table:', sTable)

sSQL = "select distinct"

sSQL = sSQL + "A.Country AS BillboardCountry,"

sSQL = sSQL + "A.Place_Name AS BillboardPlaceName,"

sSQL = sSQL + "A.Latitude AS BillboardLatitude,"

sSQL = sSQL + "A.Longitude AS BillboardLongitude,"

sSQL = sSQL + "B.Country AS VisitorCountry,"

sSQL = sSQL + "B.Place_Name AS VisitorPlaceName,"

sSQL = sSQL + "B.Latitude AS VisitorLatitude,"

sSQL = sSQL + "B.Longitude AS VisitorLongitude,"

sSQL = sSQL + "(B.Last_IP_Number - B.First_IP_Number) * 365.25 * 24 * 12 AS VisitorYearRate"

sSQL = sSQL + "from"

sSQL = sSQL + "Assess_BillboardData as A"

sSQL = sSQL + "JOIN"

sSQL = sSQL + "Assess_VisitorData as B"

sSQL = sSQL + "ON"

sSQL = sSQL + "A.Country = B.Country"

sSQL = sSQL + "AND"

sSQL = sSQL + "A.Place_Name = B.Place_Name;"

BillboardVistorsData = pd.read_sql_query(sSQL, conn)

print('################')
```

将合并后的数据集存回`SQLite`。

```python
print('################')

sTable = 'Assess_BillboardVistorsData'

print('Storing:', sDatabaseName, 'Table:', sTable)

BillboardVistorsData.to_sql(sTable, conn, if_exists="replace")

print('################')
```

打印`BillboardVistorsData`的前几行和行数。

```python
print(BillboardVistorsData.head())

print('################################')

print('Rows:', BillboardVistorsData.shape[0])

print('################################')
```

### 保存文件并结束

```python
sFileDir = Base + '/' + Company + '/02-Assess/01-EDS/02-Python'

if not os.path.exists(sFileDir):

    os.makedirs(sFileDir)

print('################################')

print('Storing:', sFileName)

print('################################')

sFileName = sFileDir + '/' + sOutputFileName

BillboardVistorsData.to_csv(sFileName, index=False)

print('################################')

print('### Done!! ############################################')
```

保存 `Assess-DE-Billboard.py`，然后使用您的 `Python` 编译器编译并执行。这将在屏幕上生成一组演示值，以及一个名为 `Assess-DE-Billboard-Visitor.csv` 的数据文件。

| 广告牌国家 | 广告牌地点名称 | 广告牌纬度 | 广告牌经度 | 访客国家 | 访客地点名称 | 访客纬度 | 访客经度 | 访客年增长率 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| DE | Lake | 51.7833 | 8.5667 | DE | Lake | 51.7833 | 8.5667 | 26823960 |
| DE | Horb | 48.4333 | 8.6833 | DE | Horb | 48.4333 | 8.6833 | 26823960 |
| DE | Horb | 48.4333 | 8.6833 | DE | Horb | 48.4333 | 8.6833 | 53753112 |

您现在应该明白，您可以将 `SQL` 卸载到 `SQLite` 数据库，以便轻松利用该 `SQL` 系统提供的数据处理能力。

我们现在将研究下一个示例。

### 了解您的在线访客数据

必须将在线访客映射到离他们最近的广告牌，以确保我们了解他们可以访问的位置和内容。为了实现这一点，我将引导您完成一个图数据处理示例，将访客活动中涉及的不同实体链接到一个图中。

然而，我们拥有的数据存储存在一些问题。

*   广告牌名称缺失。通过一些特征工程，我们可以从经度和纬度值推断出这些值。
*   广告牌与访客之间的距离未知。通过特征工程，使用文森特公式（Vincenty’s formulae）可以找到这个距离。
*   需要对经度和纬度进行平滑处理，以符合广告牌命名格式协议。

什么是文森特公式？撒迪厄斯·文森特公式（Thaddeus Vincenty’s formulae）是大地测量学中用于计算椭球体表面两点之间距离的两种相关迭代方法。它们假设地球的真实形状是一个扁球体，因此，比假设地球为球体的方法更准确。该距离被称为大圆距离。（见图 8-11）

![A435693_1_En_8_Fig11_HTML.jpg](img/A435693_1_En_8_Fig11_HTML.jpg)

**图 8-11** 图形形式的撒迪厄斯·文森特公式

在数据科学中，使用经过其他专家验证的成熟技术总是明智的。撒迪厄斯·文森特是计算地球距离的专家。`geopy` 库也有一个经过验证的文森特计算器。明智的数据科学家会使用这些经过验证的公式，因为它们能为您的工作增加额外的可信度。

您将使用 `networkx`、`sqlite3`、`pandas` 和 `geopy` 包来创建解决方案。打开您的 `Python` 编辑器，在目录 `C:/VKHCG/02-Krennwallner/02-Assess` 中创建一个名为 `Assess-Billboard_2_Visitor.py` 的文件。现在将以下代码复制到文件中：

```python
################################################################
```

# 代码分析：广告牌访客数据网络图生成

本文档介绍了一个使用 Python 分析广告牌访客数据并生成关系网络图的项目代码。

## 环境与模块导入

代码首先导入了 `networkx`、`sys`、`os`、`sqlite3`、`pandas` 和 `geopy.distance` 等必要的库。

```python
import networkx as nx
import sys
import os
import sqlite3 as sq
import pandas as pd
from geopy.distance import vincenty
```

## 基础路径设置

根据不同的操作系统（Linux 或 Windows），代码设置了不同的基础工作目录 `Base`，并打印了相关信息。

```python
if sys.platform == 'linux':
    Base=os.path.expanduser('~') + 'VKHCG'
else:
    Base='C:/VKHCG'

print('################################')
print('Working Base:',Base,'using',sys.platform)
print('################################')
```

## 公司与数据库配置

代码定义了公司名称 `Company`、数据库表名 `sTable` 和输出文件名 `sOutputFileName`，并创建了存储数据库的目录。

```python
Company='02-Krennwallner'
sTable='Assess_BillboardVisitorData'
sOutputFileName='Assess-DE-Billboard-Visitor.gml'

sDataBaseDir=Base + '/' + Company + '/02-Assess/SQLite'
if not os.path.exists(sDataBaseDir):
    os.makedirs(sDataBaseDir)
```

## 数据库连接与数据查询

代码连接到 SQLite 数据库，并构建了一个复杂的 SQL 查询，用于从 `Assess_BillboardVistorsData` 表中选取数据。查询对经纬度进行了四舍五入处理，并生成了带有方向标识（N/S/E/W）的经纬度字符串。

我们使用`round(x, 3)`公式对数据进行平滑处理。这确保没有广告牌包含非标准数字。

```sql
sSQL="select"
sSQL=sSQL+ "A.BillboardCountry,"
sSQL=sSQL+ "A.BillboardPlaceName,"
sSQL=sSQL+ "ROUND(A.BillboardLatitude,3) AS BillboardLatitude,"
sSQL=sSQL+ "ROUND(A.BillboardLongitude,3) AS BillboardLongitude,"
sSQL=sSQL+ "(CASE WHEN A.BillboardLatitude < 0 THEN"
sSQL=sSQL+ "'S' || ROUND(ABS(A.BillboardLatitude),3)"
sSQL=sSQL+ "ELSE "
sSQL=sSQL+ "'N' || ROUND(ABS(A.BillboardLatitude),3)"
sSQL=sSQL+ "END ) AS sBillboardLatitude,"
sSQL=sSQL+ "(CASE WHEN A.BillboardLongitude < 0 THEN"
sSQL=sSQL+ "'W' || ROUND(ABS(A.BillboardLongitude),3)"
sSQL=sSQL+ "ELSE "
sSQL=sSQL+ "'E' || ROUND(ABS(A.BillboardLongitude),3)"
sSQL=sSQL+ "END ) AS sBillboardLongitude,"
sSQL=sSQL+ "A.VisitorCountry,"
sSQL=sSQL+ "A.VisitorPlaceName,"
sSQL=sSQL+ "ROUND(A.VisitorLatitude,3) AS VisitorLatitude,"
sSQL=sSQL+ "ROUND(A.VisitorLongitude,3) AS VisitorLongitude,"
sSQL=sSQL+ "(CASE WHEN A.VisitorLatitude < 0 THEN"
sSQL=sSQL+ "'S' || ROUND(ABS(A.VisitorLatitude),3)"
sSQL=sSQL+ "ELSE "
sSQL=sSQL+ "'N' ||ROUND(ABS(A.VisitorLatitude),3)"
sSQL=sSQL+ "END ) AS sVisitorLatitude,"
sSQL=sSQL+ "(CASE WHEN A.VisitorLongitude < 0 THEN"
sSQL=sSQL+ "'W' || ROUND(ABS(A.VisitorLongitude),3)"
sSQL=sSQL+ "ELSE "
sSQL=sSQL+ "'E' || ROUND(ABS(A.VisitorLongitude),3)"
sSQL=sSQL+ "END ) AS sVisitorLongitude,"
sSQL=sSQL+ "A.VisitorYearRate"
sSQL=sSQL+ "from"
sSQL=sSQL+ "Assess_BillboardVistorsData AS A;"
BillboardVistorsData=pd.read_sql_query(sSQL, conn)
```

## 应用文森特公式计算距离

代码使用 `geopy.distance` 库中的 `vincenty` 函数，计算每条记录中广告牌与访客之间的地理距离（单位为英里）。

仅需这一条命令，我们便能应用文森特公式，并确保结果能以英里为单位进行支持。

```python
BillboardVistorsData['Distance']=BillboardVistorsData.apply(lambda row:
round(
    vincenty((row['BillboardLatitude'],row['BillboardLongitude']),
    (row['VisitorLatitude'],row['VisitorLongitude'])).miles
,4)
,axis=1)
```

## 构建关系网络图

现在，利用新计算出的距离，再加上广告牌命名修正，我们即可生成广告牌与访客的关系图。

代码创建了一个 `networkx` 图 `G`，并遍历数据集，为每条记录添加广告牌节点（类型 `Billboard`）和访客位置节点（类型 `Mobile`）。节点标识符结合了经纬度信息，并附加了国家、地点名称等属性。

```python
G=nx.Graph()

for i in range(BillboardVistorsData.shape[0]):
    sNode0='MediaHub-' + BillboardVistorsData['BillboardCountry'][i]
    sNode1='B-'+ BillboardVistorsData['sBillboardLatitude'][i] + '-'
    sNode1=sNode1 + BillboardVistorsData['sBillboardLongitude'][i]
    G.add_node(sNode1,
        Nodetype='Billboard',
        Country=BillboardVistorsData['BillboardCountry'][i],
        PlaceName=BillboardVistorsData['BillboardPlaceName'][i],
        Latitude=round(BillboardVistorsData['BillboardLatitude'][i],3),
        Longitude=round(BillboardVistorsData['BillboardLongitude'][i],3))

    sNode2='M-'+ BillboardVistorsData['sVisitorLatitude'][i] + '-'
    sNode2=sNode2 + BillboardVistorsData['sVisitorLongitude'][i]
    G.add_node(sNode2,
        Nodetype='Mobile',
```

# 你的成果总结

- 你能够运用自己的公式提取特征，例如广告牌名称。
- 你成功地使用了他人开发的公式来提取特征，例如广告牌与访客之间的距离。
- 你能够利用 `df.apply(lambda row: ...)` 技术，将单个公式应用于内存中的数据集。

# 为前十名客户策划活动

定期生成您所评估数据的预审批视图是必要的。我将引导您完成一个名为“处理卸载”的流程，以找出前十名客户。我还将向您展示如何使用 SQLite 通过视图来卸载处理负载。这样做的结果是，繁重的处理工作将由数据库引擎来执行。这在运行长时间进程时也很有用。

> **提示：** 这就是您将处理负载卸载到 Spark、Hadoop 或 Cassandra 等系统的方式。

接下来的示例将演示如何通过创建视图，将处理负载永久性地卸载到数据库引擎。一旦创建，您将来就可以将这些视图用于其他数据科学项目。

您将使用 `sqlite3` 和 `pandas` 包来创建解决方案。打开您的 Python 编辑器，并在目录 `C:\VKHCG\02-Krennwallner\02-Assess` 中创建一个名为 `Assess-Visitors.py` 的文件。

现在将以下代码复制到文件中：

```python
################################################################
import sys
import os
import sqlite3 as sq
import pandas as pd
from pandas.io import sql
################################################################
if sys.platform == 'linux':
    Base=os.path.expanduser('~') + 'VKHCG'
else:
    Base='C:/VKHCG'
print('################################')
print('Working Base:',Base,'using',sys.platform)
print('################################')
################################################################
Company='02-Krennwallner'
sInputFileName='01-Retrieve/01-EDS/02-Python/Retrieve_Online_Visitor.csv'
################################################################
sDataBaseDir=Base + '/' + Company + '/02-Assess/SQLite'
if not os.path.exists(sDataBaseDir):
    os.makedirs(sDataBaseDir)
################################################################
sDatabaseName=sDataBaseDir + '/krennwallner.db'
conn = sq.connect(sDatabaseName)
################################################################
##### Import Country Data
################################################################
```

此时，我们将数据加载到内存中。

```python
sFileName=Base + '/' + Company + '/' + sInputFileName
print('################################')
print('Loading:',sFileName)
print('################################')
VisitorRawData=pd.read_csv(sFileName,
                           header=0,
                           low_memory=False,
                           encoding="latin-1",
                           skip_blank_lines=True)
VisitorRawData.drop_duplicates(subset=None, keep="first", inplace=True)
VisitorData=VisitorRawData
print('Loaded Company:',VisitorData.columns.values)
print('################################')
################################################################
```

此时，我们将数据从内存存储到数据库中。

```python
print('################')
sTable='Assess_Visitor'
print('Storing:',sDatabaseName,'Table:',sTable)
```

```markdown
# VisitorData 处理流程

首先，我们使用 `VisitorData.to_sql(sTable, conn, if_exists="replace")` 将 `VisitorData` 存储到数据库表 `sTable` 中。

```
print('################')
################################################################
print(VisitorData.head())
print('################################')
print('Rows:',VisitorData.shape[0])
print('################################')
################################################################
```

此时，我们将第一组数据规则存储到数据库中。

接下来，我们创建视图 `Assess_Visitor_UseIt`。

```
print('################')
sView='Assess_Visitor_UseIt'
print('Creating:',sDatabaseName,'View:',sView)
sSQL="DROP VIEW IF EXISTS"+ sView + ";"
sql.execute(sSQL,conn)
sSQL="CREATE VIEW "+ sView + "AS"
sSQL=sSQL+ "SELECT"
sSQL=sSQL+ "A.Country,"
sSQL=sSQL+ "A.Place_Name,"
sSQL=sSQL+ "A.Latitude,"
sSQL=sSQL+ "A.Longitude,"
sSQL=sSQL+ "(A.Last_IP_Number - A.First_IP_Number) AS UsesIt"
sSQL=sSQL+ "FROM"
sSQL=sSQL+ "Assess_Visitor as A"
sSQL=sSQL+ "WHERE"
sSQL=sSQL+ "Country is not null"
sSQL=sSQL+ "AND"
sSQL=sSQL+ "Place_Name is not null;"
sql.execute(sSQL,conn)
#################################################################
```

此时，我们将第二组数据规则存储到数据库中。

接着，我们创建视图 `Assess_Total_Visitors_Location`。

```
print('################')
sView='Assess_Total_Visitors_Location'
print('Creating:',sDatabaseName,'View:',sView)
sSQL="DROP VIEW IF EXISTS"+ sView + ";"
sql.execute(sSQL,conn)
sSQL="CREATE VIEW"+ sView + "AS"
sSQL=sSQL+ "SELECT"
sSQL=sSQL+ "Country,"
sSQL=sSQL+ "Place_Name,"
sSQL=sSQL+ "SUM(UsesIt) AS TotalUsesIt"
sSQL=sSQL+ "FROM"
sSQL=sSQL+ "Assess_Visitor_UseIt"
sSQL=sSQL+ "GROUP BY"
sSQL=sSQL+ "Country,"
sSQL=sSQL+ "Place_Name"
sSQL=sSQL+ "ORDER BY"
sSQL=sSQL+ "TotalUsesIt DESC"
sSQL=sSQL+ "LIMIT 10;"
sql.execute(sSQL,conn)
#################################################################
```

此时，我们将第三组数据规则存储到数据库中。

然后，我们创建视图 `Assess_Total_Visitors_GPS`。

```
print('################')
sView='Assess_Total_Visitors_GPS'
print('Creating:',sDatabaseName,'View:',sView)
sSQL="DROP VIEW IF EXISTS "+ sView + ";"
sql.execute(sSQL,conn)
sSQL="CREATE VIEW"+ sView + "AS"
sSQL=sSQL+ "SELECT"
sSQL=sSQL+ "Latitude,"
sSQL=sSQL+ "Longitude,"
sSQL=sSQL+ "SUM(UsesIt) AS TotalUsesIt"
sSQL=sSQL+ "FROM"
sSQL=sSQL+ "Assess_Visitor_UseIt"
sSQL=sSQL+ "GROUP BY"
sSQL=sSQL+ "Latitude,"
sSQL=sSQL+ "Longitude"
sSQL=sSQL+ "ORDER BY"
sSQL=sSQL+ "TotalUsesIt DESC"
sSQL=sSQL+ "LIMIT 10;"
sql.execute(sSQL,conn)
#################################################################
```

我们现在只需遍历业务规则视图，并将所需数据提取到文本文件中。

```
sTables=['Assess_Total_Visitors_Location', 'Assess_Total_Visitors_GPS']
for sTable in sTables:
    print('################')
    print('Loading:',sDatabaseName,'Table:',sTable)
    sSQL="SELECT"
    sSQL=sSQL+ "*"
    sSQL=sSQL+ "FROM"
    sSQL=sSQL+ " "+ sTable + ";"
    TopData=pd.read_sql_query(sSQL, conn)
    print('################')
    print(TopData)
    print('################')
    print('################################')
    print('Rows:',TopData.shape[0])
    print('################################')
################################################################
print('### Done!! ############################################')
################################################################
```

保存文件`Assess-Visitors.py`，然后使用您的 Python 编译器编译并执行。您应该会看到如下所示的两个汇总结果：

| Assess_Total_Visitors_GPS |   |
|---|---|
|   | 纬度 | 经度 | 使用次数总计 |
|---|---|---|---|
| 0 | 39.9289 | 116.3883 | 53139732 |
| 1 | 37.3762 | -122.1826 | 33551404 |
| 2 | 31.5273 | -110.3607 | 33472427 |
| 3 | 35.6427 | 139.7677 | 31439772 |
| 4 | 23.1167 | 113.25 | 17577053 |
| 5 | 42.3646 | -71.1028 | 16890698 |
| 6 | 40.7355 | -74.1741 | 16813373 |
| 7 | 42.3223 | -83.1763 | 16777212 |
| 8 | 35.7977 | -78.6253 | 16761084 |
| 9 | 32.8072 | -117.1649 | 16747680 |

| Assess_Total_Visitors_Location |
|---|
|   | 国家 | 地名 | 使用次数总计 |
|---|---|---|---|
| 0 | CN | 北京 | 53139475 |
| 1 | US | 帕罗奥图 | 33682341 |
| 2 | US | 瓦丘卡堡 | 33472427 |
| 3 | JP | 东京 | 31404799 |
| 4 | US | 剑桥 | 25598851 |
| 5 | US | 圣地亚哥 | 17751367 |
| 6 | CN | 广州 | 17563744 |
| 7 | US | 纽瓦克 | 17270604 |
| 8 | US | 罗利 | 17167484 |
| 9 | US | 达勒姆 | 16914033 |

这是 Krennwallner 评估流程的最终要求。

### 与 Krennwallner AG 达成的成就总结

- 您已成功使用 `pandas` 来处理内存中和磁盘上的数据生成。
- 您已成功使用 `networkx` 来处理图数据。
- 您已成功使用 `sqlite3` 访问 SQLite 表以协助数据处理。
- 您已成功使用 `pandas` 设置 SQLite 视图，将 SQL 规则持久化到数据库中，并将处理负载卸载到 SQLite 数据库引擎。

## Hillman Ltd

Hillman Ltd 是我们的物流公司，负责处理与地理位置以及这些位置之间距离相关的数据。

### 规划仓库选址

规划仓库的选址需要评估这些仓库的 GPS 位置，以满足希尔曼公司的物流需求。

**警告**

提供信息的在线服务商对 Nominatim API 的限制是每秒最多一个请求。请参阅[`https://operations.osmfoundation.org/policies/nominatim/`](https://operations.osmfoundation.org/policies/nominatim/)。我通过限制每个会话的请求数量来控制请求频率。

您可以安装自己的服务：请参阅[`https://wiki.openstreetmap.org/wiki/Nominatim/Installation`](https://wiki.openstreetmap.org/wiki/Nominatim/Installation)。出于本书的写作目的，这种有限的使用方式已经达到了我的预期效果。

您将使用`geopy`和`pandas`包来创建解决方案。打开您的 Python 编辑器，并在目录`C:\VKHCG\03-Hillman\02-Assess`中创建一个名为`Assess-Warehouse-Address.py`的文件。

现在，将以下代码复制到该文件中：

```python
################################################################

################################################################
```

# -*- coding: utf-8 -*-

## 环境准备

```python
import os
import pandas as pd
from geopy.geocoders import Nominatim

geolocator = Nominatim()
```

## 文件路径与目录配置

```python
InputDir = '01-Retrieve/01-EDS/01-R'
InputFileName = 'Retrieve_GB_Postcode_Warehouse.csv'
EDSDir = '02-Assess/01-EDS'
OutputDir = EDSDir + '/02-Python'
OutputFileName = 'Assess_GB_Warehouse_Address.csv'
Company = '03-Hillman'
Base = 'C:/VKHCG'
```

确保输出目录存在。

```python
sFileDir = Base + '/' + Company + '/' + EDSDir
if not os.path.exists(sFileDir):
    os.makedirs(sFileDir)

sFileDir = Base + '/' + Company + '/' + OutputDir
if not os.path.exists(sFileDir):
    os.makedirs(sFileDir)
```

## 数据加载与预览

读取仓库数据并按邮编排序。

```python
sFileName = Base + '/' + Company + '/' + InputDir + '/' + InputFileName
print('###########')
print('Loading:', sFileName)
Warehouse = pd.read_csv(sFileName, header=0, low_memory=False)
Warehouse.sort_values(by='postcode', ascending=1)
```

由于地址查询服务的限制，这里仅处理10个记录。我们选取前5个和后5个纬度不为0的记录。

```python
WarehouseGoodHead = Warehouse[Warehouse.latitude != 0].head(5)
WarehouseGoodTail = Warehouse[Warehouse.latitude != 0].tail(5)
```

## 数据处理步骤

接下来，我们将获取这十个仓库的位置，并使用在线服务为 API 调用提供的经纬度获取完整的地址。查询分两轮进行，每轮五个记录。首先，我们修复内存中每行所需的参数。

```python
WarehouseGoodHead['Warehouse_Point'] = WarehouseGoodHead.apply(
    lambda row: (str(row['latitude']) + ',' + str(row['longitude'])),
    axis=1
)
```

其次，我们使用新参数来调用地址定位服务。

```python
WarehouseGoodHead['Warehouse_Address'] = WarehouseGoodHead.apply(
    lambda row: geolocator.reverse(row['Warehouse_Point']).address,
    axis=1
)
```

第三，我们从内存中的数据中删除不需要的列。

```python
WarehouseGoodHead.drop('Warehouse_Point', axis=1, inplace=True)
WarehouseGoodHead.drop('id', axis=1, inplace=True)
WarehouseGoodHead.drop('postcode', axis=1, inplace=True)
```

此处，我们简单地重复上述三个步骤以进行第二轮的五次查询。

```python
WarehouseGoodTail['Warehouse_Point'] = WarehouseGoodTail.apply(
    lambda row: (str(row['latitude']) + ',' + str(row['longitude'])),
    axis=1
)
WarehouseGoodTail['Warehouse_Address'] = WarehouseGoodTail.apply(
    lambda row: geolocator.reverse(row['Warehouse_Point']).address,
    axis=1
)
WarehouseGoodTail.drop('Warehouse_Point', axis=1, inplace=True)
WarehouseGoodTail.drop('id', axis=1, inplace=True)
WarehouseGoodTail.drop('postcode', axis=1, inplace=True)
```

合并两轮查询的结果。

```python
WarehouseGood = WarehouseGoodHead.append(WarehouseGoodTail, ignore_index=True)
print(WarehouseGood)
```

## 数据存储

此处，我们存储已增强的数据集。

```python
sFileName = sFileDir + '/' + OutputFileName
WarehouseGood.to_csv(sFileName, index=False)

print('### Done!! ############################################')
```

```
保存`Assess-Warehouse-Address.py`文件，然后使用您的 Python 编译器编译并执行它。这将在屏幕上生成一组演示值，以及一个名为`Assess_GB_Warehouse_Address.csv`的图形数据文件。

| 纬度 | 经度 | 仓库地址 |
| --- | --- | --- |
| 57.13514 | -2.11731 | 35, Broomhill Road, Kaimhill, Aberdeen, Aberdeen City, Scotland, UK |
| 57.13875 | -2.09089 | South Esplanade West, Torry, Aberdeen, Aberdeen City, Scotland, UK |
| 57.101 | -2.1106 | A92, Kincorth, Aberdeen, Aberdeen City, Scotland, UK |

#### 成就总结

*   您现在可以通过 API 将处理工作卸载到在线服务。
*   您可以调整内部参数以匹配外部要求。
*   您可以将此模型扩展到许多其他服务提供商。（请参阅`https://geopy.readthedocs.io/en/1.11.0/`）

#### 全球新仓库

Hillman 希望增设全球仓库，你需要评估这些仓库应该选址何处。我们目前只需收集可能的地点。在第 9 章和第 10 章中，我们将利用数据科学，通过聚类分析来确定公司的新仓库群。

下面的示例将向你展示如何修改读取到的、含义模糊的数据列。打开你的 Python 编辑器，在目录`C:\VKHCG\03-Hillman\02-Assess`中创建一个名为`Assess-Warehouse-Global.py`的文件。然后将以下代码复制到文件中：

```
################################################################
```
```

# -*- coding: utf-8 -*-

```python
import sys
import os
import pandas as pd
from geopy.geocoders import Nominatim

geolocator = Nominatim()

if sys.platform == 'linux':
    Base=os.path.expanduser('~') + 'VKHCG'
else:
    Base='C:/VKHCG'

print('################################')
print('Working Base:',Base,' using',sys.platform)
print('################################')

Company='03-Hillman'
InputDir='01-Retrieve/01-EDS/01-R'
InputFileName='Retrieve_All_Countries.csv'
EDSDir='02-Assess/01-EDS'
OutputDir=EDSDir + '/02-Python'
OutputFileName='Assess_All_Warehouse.csv'

sFileDir=Base + '/' + Company + '/' + EDSDir
if not os.path.exists(sFileDir):
    os.makedirs(sFileDir)

sFileDir=Base + '/' + Company + '/' + OutputDir
if not os.path.exists(sFileDir):
    os.makedirs(sFileDir)

sFileName=Base + '/' + Company + '/' + InputDir + '/' + InputFileName
print('###########')
print('Loading:',sFileName)
Warehouse=pd.read_csv(sFileName,header=0,low_memory=False, encoding="latin-1")
```

这些数据列的含义非常模糊，需要重新命名，以便于后续处理。

```python
sColumns={'X1': 'Country',
    'X2': 'PostCode',
    'X3': 'PlaceName',
    'X4': 'AreaName',
    'X5': 'AreaCode',
    'X10': 'Latitude',
    'X11': 'Longitude'}
Warehouse.rename(columns=sColumns,inplace=True)
WarehouseGood=Warehouse

sFileName=sFileDir + '/' + OutputFileName
WarehouseGood.to_csv(sFileName, index = False)

print('### Done!! ############################################')
```

保存 `Assess-Warehouse-Global.py` 文件，然后使用你的 Python 编译器编译并执行。这将在屏幕上显示一组演示值，并生成一个名为 `Assess_All_Warehouse.csv` 的图形数据文件。

| Country | PostCode | PlaceName | AreaName | AreaCode | Latitude | Longitude |
| --- | --- | --- | --- | --- | --- | --- |
| HU | 8313 | Balatongyörök | Zala | ZA | 46.75 | 17.3667 |
| HU | 8314 | Vonyarcvashegy | Zala | ZA | 46.75 | 17.3167 |
| HU | 8315 | Gyenesdiás | Zala | ZA | 46.7667 | 17.2833 |

### 规划最佳匹配国际物流的运输规则

Hillman 需要一个国际物流解决方案来支持所有必需的运输路线。

#### 提示

在过去的十年里，我几乎为每一位服务过的客户都做过运输路线方案。公司需要根据有效的路线和时间表来运输产品。因此，请务必关注这些技术和业务逻辑，因为许多公司都在使用它们。

本示例将结合你在本章学到的所有技能。打开你的 Python 编辑器，在目录 `C:\VKHCG\03-Hillman\02-Assess` 中创建一个名为 `Assess-Best-Fit-Logistics.py` 的文件。

然后将以下代码复制到文件中：

```python
################################################################
```

在本示例过程中，我们将使用许多库，如下所示：

```python
import sys
import os
import pandas as pd
import networkx as nx
from geopy.distance import vincenty
import sqlite3 as sq
from pandas.io import sql
```

又开始了——使用基本的、众所周知的参数来运行进程。

```python
################################################################
if sys.platform == 'linux':
    Base=os.path.expanduser('~') + 'VKHCG'
else:
    Base='C:/VKHCG'
print('################################')
print('Working Base:',Base,'using',sys.platform)
print('################################')
################################################################
Company='03-Hillman'
InputDir='01-Retrieve/01-EDS/01-R'
InputFileName='Retrieve_All_Countries.csv'
EDSDir='02-Assess/01-EDS'
OutputDir=EDSDir + '/02-Python'
OutputFileName='Assess_Best_Logistics.gml'
################################################################
sFileDir=Base + '/' + Company + '/' + EDSDir
if not os.path.exists(sFileDir):
    os.makedirs(sFileDir)
################################################################
sFileDir=Base + '/' + Company + '/' + OutputDir
if not os.path.exists(sFileDir):
    os.makedirs(sFileDir)
################################################################
sDataBaseDir=Base + '/' + Company + '/02-Assess/SQLite'
if not os.path.exists(sDataBaseDir):
    os.makedirs(sDataBaseDir)
################################################################
sDatabaseName=sDataBaseDir + '/Hillman.db'
conn = sq.connect(sDatabaseName)
################################################################
```

我们必须从数据源加载数据并修正数据列。

```python
sFileName=Base + '/' + Company + '/' + InputDir + '/' + InputFileName
print('###########')
print('Loading:',sFileName)
Warehouse=pd.read_csv(sFileName,header=0,low_memory=False, encoding="latin-1")
################################################################
sColumns={'X1': 'Country',
    'X2': 'PostCode',
    'X3': 'PlaceName',
    'X4': 'AreaName',
    'X5': 'AreaCode',
    'X10': 'Latitude',
    'X11': 'Longitude'}
Warehouse.rename(columns=sColumns,inplace=True)
WarehouseGood=Warehouse
#print(WarehouseGood.head())
```

现在数据已加载到内存中。我们开始对其进行操作，以满足我们的需求。你需要数据源中每个国家的平均纬度和经度。因此，要提取这个特征，你必须计算每个国家的 `mean()`。

```python
################################################################
RoutePointsCountry=pd.DataFrame(WarehouseGood.groupby(['Country'])[['Latitude','Longitude']].mean())
print(RoutePointsCountry.head())
```

现在你将获得一个包含所有已知国家单一平均点的国家列表。现在，将此列表作为 `Assess_RoutePointsCountry` 存储到 SQLite 数据库中。

```python
print('################')
sTable='Assess_RoutePointsCountry'
print('Storing:',sDatabaseName,'Table:',sTable)
RoutePointsCountry.to_sql(sTable, conn, if_exists="replace")
print('################')
```

你需要数据源中每个国家和邮政编码组合的平均纬度和经度。因此，要提取此特征，你必须为每个国家和邮政编码计算 `mean()`，并将此列表作为 `Assess_RoutePointsPostCode` 存储在 SQLite 数据库中。

```python
################################################################
RoutePointsPostCode=pd.DataFrame(WarehouseGood.groupby(['Country', 'PostCode'])[['Latitude','Longitude']].mean())
#print(RoutePointsPostCode.head())
print('################')
sTable='Assess_RoutePointsPostCode'
print('Storing :',sDatabaseName,'Table:',sTable)
RoutePointsPostCode.to_sql(sTable, conn, if_exists="replace")
print('################')
```

你需要数据源中每个国家、邮政编码和地名的平均纬度和经度。因此，要提取此特征，你必须为每个国家、邮政编码和地名计算 `mean()`，并将此列表作为 `Assess_RoutePointsPlaceName` 存储在 SQLite 数据库中。

```python
################################################################
RoutePointsPlaceName=pd.DataFrame(WarehouseGood.groupby(['Country', 'PostCode','PlaceName'])[['Latitude','Longitude']].mean())
#print(RoutePointsPlaceName.head())
print('################')
sTable='Assess_RoutePointsPlaceName'
print('Storing:',sDatabaseName,'Table:',sTable)
RoutePointsPlaceName.to_sql(sTable, conn, if_exists="replace")
print('################')
```

现在，你将匹配国家与国家的路线。我限制了处理的数据范围，仅包含国家代码 `GB`、`DE`、`BE`、`AU`、`US` 和 `IN` 以减少数据处理时间。我在集群上处理完整数据集大约花了 20 分钟，但在性能一般的笔记本电脑上却用了两个小时！因此做了这个限制。

### Fit Country to Country

```python
print('################')
sView='Assess_RouteCountries'
print('Creating:',sDatabaseName,'View:',sView)
sSQL="DROP VIEW IF EXISTS" + sView + ";"
sql.execute(sSQL,conn)
sSQL="CREATE VIEW" + sView + "AS"
sSQL=sSQL+ "SELECT DISTINCT"
sSQL=sSQL+ "S.Country AS SourceCountry,"
sSQL=sSQL+ "S.Latitude AS SourceLatitude,"
sSQL=sSQL+ "S.Longitude AS SourceLongitude,"
sSQL=sSQL+ "T.Country AS TargetCountry,"
sSQL=sSQL+ "T.Latitude AS TargetLatitude,"
sSQL=sSQL+ "T.Longitude AS TargetLongitude"
sSQL=sSQL+ "FROM"
sSQL=sSQL+ "Assess_RoutePointsCountry AS S"
sSQL=sSQL+ ","
sSQL=sSQL+ "Assess_RoutePointsCountry AS T"
sSQL=sSQL+ "WHERE S.Country  T.Country"
sSQL=sSQL+ "AND"
sSQL=sSQL+ "S.Country in('GB','DE','BE','AU','US','IN')"
sSQL=sSQL+ "AND"
sSQL=sSQL+ "T.Country in('GB','DE','BE','AU','US','IN');"
sql.execute(sSQL,conn)
print('################')
print('Loading:',sDatabaseName,'Table:',sView)
sSQL="SELECT"
sSQL=sSQL+ "*"
sSQL=sSQL+ "FROM"
sSQL=sSQL+ " " + sView + ";"
RouteCountries=pd.read_sql_query(sSQL, conn)
```

然后，你应用文森特公式来计算每对组合之间的距离（以英里为单位）。

```python
RouteCountries['Distance']=RouteCountries.apply(lambda row:
round(
vincenty((row['SourceLatitude'],row['SourceLongitude']),
(row['TargetLatitude'],row['TargetLongitude'])).miles
,4)
,axis=1)
print(RouteCountries.head(5))
```

接下来，你将匹配国家到邮政编码的路线，并像之前一样应用文森特公式。

### Fit Country to Post Code

```python
print('################')
sView='Assess_RoutePostCode'
print('Creating:',sDatabaseName,'View:',sView)
sSQL="DROP VIEW IF EXISTS" + sView + ";"
sql.execute(sSQL,conn)
sSQL="CREATE VIEW" + sView + "AS"
sSQL=sSQL+ "SELECT DISTINCT"
sSQL=sSQL+ "S.Country AS SourceCountry,"
sSQL=sSQL+ "S.Latitude AS SourceLatitude,"
sSQL=sSQL+ "S.Longitude AS SourceLongitude,"
sSQL=sSQL+ "T.Country AS TargetCountry,"
sSQL=sSQL+ "T.PostCode AS TargetPostCode,"
sSQL=sSQL+ "T.Latitude AS TargetLatitude,"
sSQL=sSQL+ "T.Longitude AS TargetLongitude"
sSQL=sSQL+ "FROM"
sSQL=sSQL+ "Assess_RoutePointsCountry AS S"
sSQL=sSQL+ ","
sSQL=sSQL+ "Assess_RoutePointsPostCode AS T"
sSQL=sSQL+ "WHERE S.Country = T.Country"
sSQL=sSQL+ "AND"
sSQL=sSQL+ "S.Country in ('GB','DE','BE','AU','US','IN')"
sSQL=sSQL+ "AND"
sSQL=sSQL+ "T.Country in ('GB','DE','BE','AU','US','IN');"
sql.execute(sSQL,conn)
print('################')
print('Loading:',sDatabaseName,'Table:',sView)
sSQL="SELECT"
sSQL=sSQL+ "*"
sSQL=sSQL+ "FROM"
sSQL=sSQL+ " " + sView + ";"
RoutePostCode=pd.read_sql_query(sSQL, conn)
RoutePostCode['Distance']=RoutePostCode.apply(lambda row:
round(
vincenty((row['SourceLatitude'],row['SourceLongitude']),
(row['TargetLatitude'],row['TargetLongitude'])).miles
,4)
,axis=1)
print(RoutePostCode.head(5))
```

现在，你将匹配邮政编码到地名的路线，并像之前一样应用文森特公式。

### Fit Post Code to Place Name

```python
print('################')
sView='Assess_RoutePlaceName'
print('Creating:',sDatabaseName,'View:',sView)
sSQL="DROP VIEW IF EXISTS" + sView + ";"
sql.execute(sSQL,conn)
sSQL="CREATE VIEW" + sView + "AS"
sSQL=sSQL+ "SELECT DISTINCT"
sSQL=sSQL+ "S.Country AS SourceCountry,"
sSQL=sSQL+ "S.PostCode AS SourcePostCode,"
sSQL=sSQL+ "S.Latitude AS SourceLatitude,"
sSQL=sSQL+ "S.Longitude AS SourceLongitude,"
sSQL=sSQL+ "T.Country AS TargetCountry,"
sSQL=sSQL+ "T.PostCode AS TargetPostCode,"
sSQL=sSQL+ "T.PlaceName AS TargetPlaceName,"
sSQL=sSQL+ "T.Latitude AS TargetLatitude,"
sSQL=sSQL+ "T.Longitude AS TargetLongitude"
```

## SQL 查询执行

```sql
sSQL=sSQL+ "FROM"
sSQL=sSQL+ "Assess_RoutePointsPostCode AS S"
sSQL=sSQL+ ","
sSQL=sSQL+ "Assess_RoutePointsPLaceName AS T"
sSQL=sSQL+ "WHERE"
sSQL=sSQL+ "S.Country = T.Country"
sSQL=sSQL+ "AND"
sSQL=sSQL+ "S.PostCode = T.PostCode"
sSQL=sSQL+ "AND"
sSQL=sSQL+ "S.Country in('GB','DE','BE','AU','US','IN')"
sSQL=sSQL+ "AND"
sSQL=sSQL+ "T.Country in('GB','DE','BE','AU','US','IN');"
sql.execute(sSQL,conn)
print('################')
print('Loading:',sDatabaseName,'Table:',sView)
sSQL="SELECT"
sSQL=sSQL+ "*"
sSQL=sSQL+ "FROM"
sSQL=sSQL+ " " + sView + ";"
```

执行查询并计算路线距离：

```python
RoutePlaceName=pd.read_sql_query(sSQL, conn)
RoutePlaceName['Distance']=RoutePlaceName.apply(lambda row:
round(
    vincenty((row['SourceLatitude'],row['SourceLongitude']),
             (row['TargetLatitude'],row['TargetLongitude'])).miles
    ,4)
,axis=1)
print(RoutePlaceName.head(5))
```

## 图表创建

现在，你将把新的位置标注到一张图表上。首先是国家。

```python
G = nx.Graph()
```

打印`RouteCountries`的形状：

```python
print('Countries:',RouteCountries.shape)
```

遍历`RouteCountries`的每一行，为每个源国家和目标国家创建节点，并添加边：

```python
for i in range(RouteCountries.shape[0]):
    sNode0='C-' + RouteCountries['SourceCountry'][i]
    G.add_node(sNode0,
               Nodetype='Country',
               Country=RouteCountries['SourceCountry'][i],
               Latitude=round(RouteCountries['SourceLatitude'][i],4),
               Longitude=round(RouteCountries['SourceLongitude'][i],4))
    sNode1='C-' + RouteCountries['TargetCountry'][i]
    G.add_node(sNode1,
               Nodetype='Country',
               Country=RouteCountries['TargetCountry'][i],
               Latitude=round(RouteCountries['TargetLatitude'][i],4),
               Longitude=round(RouteCountries['TargetLongitude'][i],4))
    G.add_edge(sNode0,sNode1,distance=round(RouteCountries['Distance'][i],3))
```

### 处理邮政编码

```python
print('Post Code:',RoutePostCode.shape)
for i in range(RoutePostCode.shape[0]):
    sNode0='C-' + RoutePostCode['SourceCountry'][i]
    G.add_node(sNode0,
               Nodetype='Country',
               Country=RoutePostCode['SourceCountry'][i],
               Latitude=round(RoutePostCode['SourceLatitude'][i],4),
               Longitude=round(RoutePostCode['SourceLongitude'][i],4))
    sNode1='P-' + RoutePostCode['TargetPostCode'][i]  + '-' + RoutePostCode['TargetCountry'][i]
    G.add_node(sNode1,
               Nodetype='PostCode',
               Country=RoutePostCode['TargetCountry'][i],
               PostCode=RoutePostCode['TargetPostCode'][i],
               Latitude=round(RoutePostCode['TargetLatitude'][i],4),
               Longitude=round(RoutePostCode['TargetLongitude'][i],4))
    G.add_edge(sNode0,sNode1,distance=round(RoutePostCode['Distance'][i],3))
```

### 处理地名

```python
print('Place Name:',RoutePlaceName.shape)
for i in range(RoutePlaceName.shape[0]):
    sNode0='P-' + RoutePlaceName['TargetPostCode'][i]  + '-'
    sNode0=sNode0 + RoutePlaceName['TargetCountry'][i]
    G.add_node(sNode0,
               Nodetype='PostCode',
               Country=RoutePlaceName['SourceCountry'][i],
               PostCode=RoutePlaceName['TargetPostCode'][i],
               Latitude=round(RoutePlaceName['SourceLatitude'][i],4),
               Longitude=round(RoutePlaceName['SourceLongitude'][i],4))
    sNode1='L-' + RoutePlaceName['TargetPlaceName'][i]  + '-'
    sNode1=sNode1 + RoutePlaceName['TargetPostCode'][i]  + '-'
    sNode1=sNode1 + RoutePlaceName['TargetCountry'][i]
    G.add_node(sNode1,
               Nodetype='PlaceName',
               Country=RoutePlaceName['TargetCountry'][i],
               PostCode=RoutePlaceName['TargetPostCode'][i],
               PlaceName=RoutePlaceName['TargetPlaceName'][i],
               Latitude=round(RoutePlaceName['TargetLatitude'][i],4),
               Longitude=round(RoutePlaceName['TargetLongitude'][i],4))
    G.add_edge(sNode0,sNode1,distance=round(RoutePlaceName['Distance'][i],3))
```

现在你拥有了一个包含所有可能路径的完整图。让我们存储你的工作成果。

```python
```

## 成就总结

你现在能够从图中查询特征，例如位置之间的最短路径以及从给定位置出发的路径。
你可以对数据科学环境进行清理，以最大化处理能力。

## 为集装箱运输选择最佳包装方案

希尔曼公司希望在其物流策略中引入新型集装箱。你需要构建一个能支持此项目的数据集。

我将引导你完成评估潜在集装箱尺寸的流程。

### 提示

本示例引入了带有范围或公差的功能特性。

这些不够精确的数据值可能会在数据科学体系中引发重大问题。在第 9 章和第 10 章中，我将指导你如何进行精确测量、添加给定的公差，并创建数据科学所需的值范围。

打开你的 Python 编辑器，并在目录`C:\VKHCG\03-Hillman\02-Assess`中创建一个名为`Assess-Shipping-Containers.py`的文件。

现在将以下代码复制到文件中：

```
sFileName=sFileDir + '/' + OutputFileName

print('################################')

print('Storing:', sFileName)

print('################################')

nx.write_gml(G,sFileName)

sFileName=sFileName +'.gz'

nx.write_gml(G,sFileName)

```

你现在将开始提取图结构中存储的额外特征。你需要寻找的特征是两个位置之间的最短路径。

```python
print('################################')

print('Path:', nx.shortest_path(G,source='P-SW1-GB',target='P-01001-US',weight='distance'))

print('Path length:', nx.shortest_path_length(G,source='P-SW1-GB',target='P-01001-US',weight='distance'))

print('Path length (1):', nx.shortest_path_length(G,source='P-SW1-GB',target='C-GB',weight='distance'))

print('Path length (2):', nx.shortest_path_length(G,source='C-GB',target='C-US',weight='distance'))

print('Path length (3):', nx.shortest_path_length(G,source='C-US',target='P-01001-US',weight='distance'))

print('################################')
```

你需要寻找的特征是给定位置与一跳范围内的任何其他位置之间的最短路径。

```python
print('Routes from P-SW1-GB < 2:', nx.single_source_shortest_path(G,source='P-SW1-GB', cutoff=1))

print('Routes from P-01001-US < 2:', nx.single_source_shortest_path(G,source='P-01001-US', cutoff=1))

print('################################')
```

你构建的图中隐藏着许多特征。如果你想测试新技能，建议你查看`networkx`的在线文档，网址为[https://networkx.github.io/documentation/networkx-1.10/reference/algorithms.html](https://networkx.github.io/documentation/networkx-1.10/reference/algorithms.html)。那里有数百种可能的选项和组合。祝你玩得开心！

但在开始测试技能之前，你必须执行一些数据库维护。

```python
print('################')

print('Vacuum Database')

sSQL="VACUUM;"

sql.execute(sSQL,conn)

print('################')

print('### Done!! ############################################')
```

保存`Assess-Best-Fit-Logistics.py`文件，然后使用 Python 编译器编译并执行。这将在屏幕上生成一组演示值，以及一个名为`Assess_Best_Logistics.gml`的图数据文件。

维护是有意义的，因为多年来，我见过许多数据科学家抱怨他们的数据科学运行缓慢，但却忽视了对系统进行简单的清理，而清理可以恢复昂贵的处理能力。

**提示**：在每次重要的数据科学会话之后，务必清理环境。压缩数据库、删除未使用的文件并清理环境。爱护你的工具，它们也会爱护你。

现在将以下代码复制到文件中：

```
################################################################
```

```markdown
# -*- coding: utf-8 -*-

################################################################

import sys

import os

import pandas as pd

import sqlite3 as sq

from pandas.io import sql

################################################################

if `sys.platform` == 'linux':

    `Base`=`os.path.expanduser`('~') + 'VKHCG'

else:

    `Base`='C:/VKHCG'

print('################################')

print('Working Base:',`Base`,'using',`sys.platform`)

print('################################')

################################################################

`Company`='03-Hillman'

`InputDir`='01-Retrieve/01-EDS/02-Python'

`InputFileName1`='Retrieve_Product.csv'

`InputFileName2`='Retrieve_Box.csv'

`InputFileName3`='Retrieve_Container.csv'

`EDSDir`='02-Assess/01-EDS'

`OutputDir`=`EDSDir` + '/02-Python'

`OutputFileName`='Assess_Shipping_Containers.csv'

################################################################

`sFileDir`=`Base` + '/' + `Company` + '/' + `EDSDir`

if not `os.path.exists`(`sFileDir`):

    `os.makedirs`(`sFileDir`)

################################################################

`sFileDir`=`Base` + '/' + `Company` + '/' + `OutputDir`

if not `os.path.exists`(`sFileDir`):

    `os.makedirs`(`sFileDir`)

################################################################

`sDataBaseDir`=`Base` + '/' + `Company` + '/02-Assess/SQLite'

if not `os.path.exists`(`sDataBaseDir`):

    `os.makedirs`(`sDataBaseDir`)

################################################################

`sDatabaseName`=`sDataBaseDir` + '/hillman.db'

`conn` = `sq.connect`(`sDatabaseName`)

################################################################

################################################################

##### 导入产品数据

################################################################

`sFileName`=`Base` + '/' + `Company` + '/' + `InputDir` + '/' + `InputFileName1`

print('###########')

print('Loading:',`sFileName`)

`ProductRawData`=`pd.read_csv`(`sFileName`,

    header=0,

    low_memory=False,

    encoding="latin-1"

)

`ProductRawData`.`drop_duplicates`(subset=None, keep="first", inplace=True)

`ProductRawData`.index.name = 'IDNumber'

为了加快处理速度，我对数据集做了限制。此代码仅引用当前长度值至少为半米的前十种产品。

**注**

如果你想的话，可以随意尝试不同的参数。试想一下，如果你只使用长度在 25 厘米到 50 厘米之间的产品，或者整个宽度大于 1 米的产品范围会怎样？这就是数据科学。好好享受吧。

`ProductData`=`ProductRawData`[`ProductRawData`.Length <= 0.5].head(10)

print('Loaded Product:',`ProductData`.columns.values)

print('################################')

################################################################

print('################')

`sTable`='Assess_Product'

print('Storing:',`sDatabaseName`,'Table:',`sTable`)

`ProductData`.`to_sql`(`sTable`, `conn`, if_exists="replace")

print('################')

################################################################

print(`ProductData`.head())

print('################################')

print('Rows:',`ProductData`.shape[0])

print('################################')

################################################################

################################################################

##### 导入包装盒数据

################################################################

`sFileName`=`Base` + '/' + `Company` + '/' + `InputDir` + '/' + `InputFileName2`

print('###########')

print('Loading:',`sFileName`)

`BoxRawData`=`pd.read_csv`(`sFileName`,

    header=0,

    low_memory=False,

    encoding="latin-1"

)

`BoxRawData`.`drop_duplicates`(subset=None, keep="first", inplace=True)

`BoxRawData`.index.name = 'IDNumber'

为了加快处理速度，我对数据集做了限制。此代码仅引用当前长度值小于一米的前 1000 个包装盒。如果你愿意，可以更改这些限制条件。

`BoxData`=`BoxRawData`[`BoxRawData`.Length <= 1].head(1000)

print('Loaded Product:',`BoxData`.columns.values)

print('################################')

################################################################

print('################')

`sTable`='Assess_Box'

print('Storing:',`sDatabaseName`,'Table:',`sTable`)

`BoxData`.`to_sql`(`sTable`, `conn`, if_exists="replace")

print('################')

################################################################

print(`BoxData`.head())

print('################################')

print('Rows:',`BoxData`.shape[0])
```

### 导入集装箱数据

```python
print('################################')

################################################################

################################################################

sFileName=Base + '/' + Company + '/' + InputDir + '/' + InputFileName3

print('###########')

print('Loading:',sFileName)

ContainerRawData=pd.read_csv(sFileName,

header=0,

low_memory=False,

encoding="latin-1"

)

ContainerRawData.drop_duplicates(subset=None, keep="first", inplace=True)

ContainerRawData.index.name = 'IDNumber'

```

为了加快处理速度，我对数据集做了限制。此代码仅引用当前长度值小于两米的前十个集装箱。你可以按自己的意愿更改这些限制条件。

```python
ContainerData=ContainerRawData[ContainerRawData.Length <= 2].head(10)

print('Loaded Product:',ContainerData.columns.values)

print('################################')

################################################################

print('################')

sTable='Assess_Container'

print('Storing:',sDatabaseName,'Table:',sTable)

BoxData.to_sql(sTable, conn, if_exists="replace")

print('################')

################################################################

print(ContainerData.head())

print('################################')

print('Rows:',ContainerData.shape[0])

print('################################')

################################################################

```

你的任务是为你选择的每个产品找到合适的包装盒。产品必须能装进盒子，并且要有足够的包装泡沫来保护产品。

### 将产品装入箱子

```sql
################################################################

##### 将产品装入箱子

################################################################

print('################')

sView='Assess_Product_in_Box'

print('Creating:',sDatabaseName,'View:',sView)

sSQL="DROP VIEW IF EXISTS " + sView + ";"

sql.execute(sSQL,conn)

sSQL="CREATE VIEW" + sView + "AS"

sSQL=sSQL+ "SELECT"

sSQL=sSQL+ "P.UnitNumber AS ProductNumber,"

sSQL=sSQL+ "B.UnitNumber AS BoxNumber,"

sSQL=sSQL+ "(B.Thickness * 1000) AS PackSafeCode,"

sSQL=sSQL+ "(B.BoxVolume - P.ProductVolume) AS PackFoamVolume,"

```

我们提供了一个计算空运体积重量（计费重量）的公式，因为当你通过空运发货时，你需要按每公斤支付运费，并取空运体积重量（计费重量）和物品实际重量两者中的较高值。通用系数为每立方厘米`167`公斤。

```sql
sSQL=sSQL+ "((B.Length*10) * (B.Width*10) * (B.Height*10)) * 167 AS Air_Dimensional_Weight,"

```

我们提供了一个计算陆运体积重量（计费重量）的公式，因为当你通过陆运发货时，你需要按每公斤支付运费，并取陆运体积重量（计费重量）和物品实际重量两者中的较高值。通用系数为每立方厘米`333`公斤。

```sql
sSQL=sSQL+ "((B.Length*10) * (B.Width*10) * (B.Height*10)) * 333 AS Road_Dimensional_Weight,"

```

我们提供了一个计算海运体积重量（计费重量）的公式，因为当你通过海运发货时，你需要按每公斤支付运费，并取海运体积重量（计费重量）和物品实际重量两者中的较高值。通用系数为每立方厘米`1000`公斤。

```sql
sSQL=sSQL+ "((B.Length*10) * (B.Width*10) * (B.Height*10)) * 1000 AS Sea_Dimensional_Weight,"

sSQL=sSQL+ "P.Length AS Product_Length,"

sSQL=sSQL+ "P.Width AS Product_Width,"

sSQL=sSQL+ "P.Height AS Product_Height,"

sSQL=sSQL+ "P.ProductVolume AS Product_cm_Volume,"

sSQL=sSQL+ "((P.Length*10) * (P.Width*10) * (P.Height*10)) AS Product_ccm_Volume,"

```

我之前提到过你将需要范围或选项。这里我们设置了一个公差，用于将产品包装在指定的箱子中，公差范围是实际尺寸的上下`5%`。因此，一个产品可能适合多个箱子，其包装材料的厚度会有所不同。

> **注意**
> 
> 我曾经通过使用更小的包装箱，帮助一位客户每天节省了近`2000`英镑的运费。请记住，你支付的体积重量（计费重量）会占用额外的运输空间。

```sql
sSQL=sSQL+ "(B.Thickness * 0.95) AS Minimum_Pack_Foam,"

sSQL=sSQL+ "(B.Thickness * 1.05) AS Maximum_Pack_Foam,"

sSQL=sSQL+ "B.Length - (B.Thickness * 1.10) AS Minimum_Product_Box_Length,"

sSQL=sSQL+ "B.Length - (B.Thickness * 0.95) AS Maximum_Product_Box_Length,"

sSQL=sSQL+ "B.Width - (B.Thickness * 1.10) AS Minimum_Product_Box_Width,"

sSQL=sSQL+ "B.Width - (B.Thickness * 0.95) AS Maximum_Product_Box_Width,"

sSQL=sSQL+ "B.Height - (B.Thickness * 1.10) AS Minimum_Product_Box_Height,"

sSQL=sSQL+ "B.Height - (B.Thickness * 0.95) AS Maximum_Product_Box_Height,"

sSQL=sSQL+ "B.Length AS Box_Length,"

sSQL=sSQL+ "B.Width AS Box_Width,"

sSQL=sSQL+ "B.Height AS Box_Height,"

sSQL=sSQL+ "B.BoxVolume AS Box_cm_Volume,"

sSQL=sSQL+ "((B.Length*10) * (B.Width*10) * (B.Height*10)) AS Box_ccm_Volume,"

sSQL=sSQL+ "(2 * B.Length * B.Width) + (2 * B.Length * B.Height) + (2 * B.Width * B.Height) AS Box_sqm_Area,"

```

下一个限制是箱子的承重等级。每个箱子都有一个最大核准承重。

> **注意**
>
> 在物流中，如果你超载了箱子并导致其损坏，你的保险将不承担费用。这种微小的计算失误是物流中造成损坏的最常见原因。切勿超载箱子！

## 箱子承重等级

我们的示例使用三种不同强度的箱子等级：每立方厘米 `3.5` 公斤、`7.7` 公斤和 `10` 公斤。

```sql
sSQL=sSQL+ "((B.Length*10) * (B.Width*10) * (B.Height*10)) *  3.5 AS Box_A_Max_Kg_Weight,"
sSQL=sSQL+ "((B.Length*10) * (B.Width*10) * (B.Height*10)) *  7.7 AS Box_B_Max_Kg_Weight,"
sSQL=sSQL+ "((B.Length*10) * (B.Width*10) * (B.Height*10)) * 10.0 AS Box_C_Max_Kg_Weight"
sSQL=sSQL+ "FROM"
sSQL=sSQL+ "Assess_Product as P"
sSQL=sSQL+ ","
sSQL=sSQL+ "Assess_Box as B"
sSQL=sSQL+ "WHERE"
```

以下是产品体积与箱子之间的主要范围映射过程：

```sql
sSQL=sSQL+ "P.Length >= (B.Length - (B.Thickness * 1.10))"
sSQL=sSQL+ "AND"
sSQL=sSQL+ "P.Width >= (B.Width - (B.Thickness * 1.10))"
sSQL=sSQL+ "AND"
sSQL=sSQL+ "P.Height >= (B.Height - (B.Thickness * 1.10))"
sSQL=sSQL+ "AND"
sSQL=sSQL+ "P.Length <= (B.Length - (B.Thickness * 0.95))"
sSQL=sSQL+ "AND"
sSQL=sSQL+ "P.Width <= (B.Width - (B.Thickness * 0.95))"
sSQL=sSQL+ "AND"
sSQL=sSQL+ "P.Height <= (B.Height - (B.Thickness * 0.95))"
```

此外，建议箱子比产品大。但你可能已经知道这一点。不过，我的一位客户并不清楚，因此每年订购了 `20000` 个过小的箱子。

```sql
sSQL=sSQL+ "AND"
sSQL=sSQL+ "(B.Height - B.Thickness) >= 0"
sSQL=sSQL+ "AND"
sSQL=sSQL+ "(B.Width - B.Thickness) >= 0"
sSQL=sSQL+ "AND"
sSQL=sSQL+ "(B.Height - B.Thickness) >= 0"
sSQL=sSQL+ "AND"
sSQL=sSQL+ "B.BoxVolume >= P.ProductVolume;"
sql.execute(sSQL,conn)
```

## 托盘装箱方案

接下来我将向你展示如何将箱子装到运输托盘上。

```python
################################################################
##### 将箱子装到托盘上
################################################################
t=0
for l in range(2,8):
    for w in range(2,8):
        for h in range(4):
            t += 1
            PalletLine=[('IDNumber',[t]),
                       ('ShipType',['Pallet']),
                       ('UnitNumber',('L-'+format(t,"06d"))),
                       ('Box_per_Length',(format(2**l,"4d"))),
                       ('Box_per_Width',(format(2**w,"4d"))),
                       ('Box_per_Height',(format(2**h,"4d")))]
            if t==1:
                PalletFrame = pd.DataFrame.from_items(PalletLine)
            else:
                PalletRow = pd.DataFrame.from_items(PalletLine)
                PalletFrame = PalletFrame.append(PalletRow)
            PalletFrame.set_index(['IDNumber'],inplace=True)
################################################################
PalletFrame.head()
print('################################')
print('Rows:',PalletFrame.shape[0])
print('################################')
################################################################
##### 将箱子装到托盘上
################################################################
print('################')
sView='Assess_Box_on_Pallet'
print('Creating:',sDatabaseName,'View:',sView)
sSQL="DROP VIEW IF EXISTS" + sView + ";"
sql.execute(sSQL,conn)
sSQL="CREATE VIEW" + sView + "AS"
sSQL=sSQL+ "SELECT DISTINCT"
sSQL=sSQL+ "P.UnitNumber AS PalletNumber,"
sSQL=sSQL+ "B.UnitNumber AS BoxNumber,"
```

托盘的大小取决于你的堆叠方案。你是按三个箱子宽、四个箱子长、三个箱子高来堆叠，还是按五个乘五个乘五个来堆叠？这是个问题。一旦获得数据，就可以进行实验。

```sql
sSQL=sSQL+ "round(B.Length*P.Box_per_Length,3) AS Pallet_Length,"
sSQL=sSQL+ "round(B.Width*P.Box_per_Width,3) AS Pallet_Width,"
sSQL=sSQL+ "round(B.Height*P.Box_per_Height,3) AS Pallet_Height,"
sSQL=sSQL+ "P.Box_per_Length * P.Box_per_Width * P.Box_per_Height AS Pallet_Boxes"
sSQL=sSQL+ "FROM"
sSQL=sSQL+ "Assess_Box as B"
sSQL=sSQL+ ","
sSQL=sSQL+ "Assess_Pallet as P"
```

我建议的唯一限制是，托盘尺寸不要超过 `20` 米 × `9` 米 × `5` 米，这是可用起重机能够吊起的最大物品。不过，如果你想试验，也可以随意更改此限制。

```sql
sSQL=sSQL+ "WHERE"
sSQL=sSQL+ "round(B.Length*P.Box_per_Length,3) <= 20"
sSQL=sSQL+ "AND"
sSQL=sSQL+ "round(B.Width*P.Box_per_Width,3) <= 9"
sSQL=sSQL+ "AND"
sSQL=sSQL+ "round(B.Height*P.Box_per_Height,3) <= 5;"
sql.execute(sSQL,conn)
```

现在让我们来计算产品装箱和箱子装盘的各种选项。

```python
################################################################
sTables = ['Assess_Product_in_Box', 'Assess_Box_on_Pallet']
for sTable in sTables:
    print('################')
    print('Loading:', sDatabaseName, 'Table:', sTable)
    sSQL = "SELECT "
    sSQL = sSQL + "*"
    sSQL = sSQL + "FROM"
    sSQL = sSQL + " " + sTable + ";"
```

```python
SnapShotData = pd.read_sql_query(sSQL, conn)

print('################')

sTableOut = sTable + '_SnapShot'

print('Storing:', sDatabaseName, 'Table:', sTable)

SnapShotData.to_sql(sTableOut, conn, if_exists="replace")

print('################')
```

接下来，我将向您展示如何计算将托盘装入正确海运集装箱的选项。规则很简单：如果托盘能装入集装箱的开口，那么就可以通过该集装箱运输。

```python
################################################################

##### 将托盘装入集装箱

################################################################

sTables = ['Length', 'Width', 'Height']

for sTable in sTables:

    sView = 'Assess_Pallet_in_Container_' + sTable

    print('Creating:', sDatabaseName, 'View:', sView)

    sSQL = "DROP VIEW IF EXISTS" + sView + ";"

    sql.execute(sSQL, conn)

    sSQL = "CREATE VIEW" + sView + "AS"

    sSQL = sSQL + "SELECT DISTINCT"

    sSQL = sSQL + "C.UnitNumber AS ContainerNumber,"

    sSQL = sSQL + "P.PalletNumber,"

    sSQL = sSQL + "P.BoxNumber,"

    sSQL = sSQL + "round(C." + sTable + "/P.Pallet_" + sTable + ",0)"

    sSQL = sSQL + "AS Pallet_per_" + sTable + ","

    sSQL = sSQL + "round(C." + sTable + "/P.Pallet_" + sTable + ",0)"

    sSQL = sSQL + "* P.Pallet_Boxes AS Pallet_" + sTable + "_Boxes,"

    sSQL = sSQL + "P.Pallet_Boxes"

    sSQL = sSQL + "FROM"

    sSQL = sSQL + "Assess_Container as C"

    sSQL = sSQL + ","

    sSQL = sSQL + "Assess_Box_on_Pallet_SnapShot as P"

    sSQL = sSQL + "WHERE"
```

检查原则是：如果装得下，就通过。一个集装箱可以装载许多托盘，但前提是必须装得下。

```python
sSQL = sSQL + "round(C.Length/P.Pallet_Length,0) > 0"

sSQL = sSQL + "AND"

sSQL = sSQL + "round(C.Width/P.Pallet_Width,0) > 0"

sSQL = sSQL + "AND"

sSQL = sSQL + "round(C.Height/P.Pallet_Height,0) > 0;"

sql.execute(sSQL, conn)

print('################')

print('Loading:', sDatabaseName, 'Table:', sView)

sSQL = "SELECT"

sSQL = sSQL + "*"

sSQL = sSQL + "FROM"

sSQL = sSQL + " " + sView + ";"

SnapShotData = pd.read_sql_query(sSQL, conn)

print('################')

sTableOut = sView + '_SnapShot'

print('Storing:', sDatabaseName, 'Table:', sView)

SnapShotData.to_sql(sTableOut, conn, if_exists="replace")

print('################')
```

现在，我们只需提取托盘装入集装箱的选项，供后续使用。

```python
################################################################

print('################')

sView = 'Assess_Pallet_in_Container'

print('Creating:', sDatabaseName, 'View:', sView)

sSQL = "DROP VIEW IF EXISTS" + sView + ";"

sql.execute(sSQL, conn)

sSQL = "CREATE VIEW" + sView + "AS"

sSQL = sSQL + "SELECT"

sSQL = sSQL + "CL.ContainerNumber,"

sSQL = sSQL + "CL.PalletNumber,"

sSQL = sSQL + "CL.BoxNumber,"

sSQL = sSQL + "CL.Pallet_Boxes AS Boxes_per_Pallet,"

sSQL = sSQL + "CL.Pallet_per_Length,"

sSQL = sSQL + "CW.Pallet_per_Width,"

sSQL = sSQL + "CH.Pallet_per_Height,"

sSQL = sSQL + "CL.Pallet_Length_Boxes * CW.Pallet_Width_Boxes * CH.Pallet_Height_Boxes AS Container_Boxes"

sSQL = sSQL + "FROM"

sSQL = sSQL + "Assess_Pallet_in_Container_Length_SnapShot as CL"

sSQL = sSQL + "JOIN"

sSQL = sSQL + "Assess_Pallet_in_Container_Width_SnapShot as CW"

sSQL = sSQL + "ON"

sSQL = sSQL + "CL.ContainerNumber = CW.ContainerNumber"

sSQL = sSQL + "AND"

sSQL = sSQL + "CL.PalletNumber = CW.PalletNumber"

sSQL = sSQL + "AND"

sSQL = sSQL + "CL.BoxNumber = CW.BoxNumber"

sSQL = sSQL + "JOIN"

sSQL = sSQL + "Assess_Pallet_in_Container_Height_SnapShot as CH"

sSQL = sSQL + "ON"

sSQL = sSQL + "CL.ContainerNumber = CH.ContainerNumber"

sSQL = sSQL + "AND"

sSQL = sSQL + "CL.PalletNumber = CH.PalletNumber"

sSQL = sSQL + "AND"

sSQL = sSQL + "CL.BoxNumber = CH.BoxNumber;"

sql.execute(sSQL, conn)

################################################################

sTables = ['Assess_Product_in_Box', 'Assess_Pallet_in_Container']

for sTable in sTables:

    print('################')

    print('Loading:', sDatabaseName, 'Table:', sTable)

    sSQL = "SELECT "

    sSQL = sSQL + "*"

    sSQL = sSQL + "FROM"

    sSQL = sSQL + " " + sTable + ";"

    PackData = pd.read_sql_query(sSQL, conn)
```

# 创建配送路线

```python
print('################')

print(PackData)

print('################')

print('################################')

print('Rows:', PackData.shape[0])

print('################################')

sFileName = sFileDir + '/' + sTable + '.csv'

print(sFileName)

PackData.to_csv(sFileName, index=False)

################################################################

print('### 完成！！ ############################################')

################################################################
```

将文件保存为 `Assess-Shipping-Containers.py`，然后使用Python编译器进行编译和执行。这将在屏幕上生成一组演示数值，并生成一个名为 `Assess_Shipping_Containers.csv` 的数据文件。

Hillman 需要公司完整的配送路线网格计划，以确保其新策略能够覆盖供应商、仓库、商店和客户。这项新计划将实现供应商、仓库、商店和客户之间的最优路线。

打开你的 Python 编辑器，在目录 `C:\VKHCG\03-Hillman\02-Assess` 中创建一个名为 `Assess-Shipping-Routes.py` 的文件。

现在将以下代码复制到文件中：

```python
################################################################
```

# -*- coding: utf-8 -*-

## 导入模块

```python
import sys
import os
import pandas as pd
import sqlite3 as sq
from pandas.io import sql
import networkx as nx
from geopy.distance import vincenty
```

## 全局参数

```python
nMax = 100
nMaxPath = 100
nSet = True
nVSet = False
```

## 路径与平台设置

```python
if sys.platform == 'linux':
    Base = os.path.expanduser('~') + 'VKHCG'
else:
    Base = 'C:/VKHCG'
print('################################')
print('Working Base:', Base, 'using', sys.platform)
print('################################')
```

## 公司与文件路径

```python
Company = '03-Hillman'
InputDir1 = '01-Retrieve/01-EDS/01-R'
InputDir2 = '01-Retrieve/01-EDS/02-Python'
InputFileName1 = 'Retrieve_GB_Postcode_Warehouse.csv'
InputFileName2 = 'Retrieve_GB_Postcodes_Shops.csv'
EDSDir = '02-Assess/01-EDS'
OutputDir = EDSDir + '/02-Python'
OutputFileName1 = 'Assess_Shipping_Routes.gml'
OutputFileName2 = 'Assess_Shipping_Routes.txt'
```

## 创建目录

```python
sFileDir = Base + '/' + Company + '/' + EDSDir
if not os.path.exists(sFileDir):
    os.makedirs(sFileDir)

sFileDir = Base + '/' + Company + '/' + OutputDir
if not os.path.exists(sFileDir):
    os.makedirs(sFileDir)

sDataBaseDir = Base + '/' + Company + '/02-Assess/SQLite'
if not os.path.exists(sDataBaseDir):
    os.makedirs(sDataBaseDir)
```

## 数据库连接

```python
sDatabaseName = sDataBaseDir + '/hillman.db'
conn = sq.connect(sDatabaseName)
```

## 导入仓库数据

```python
sFileName = Base + '/' + Company + '/' + InputDir1 + '/' + InputFileName1
print('###########')
print('Loading:', sFileName)
WarehouseRawData = pd.read_csv(sFileName,
                               header=0,
                               low_memory=False,
                               encoding="latin-1"
                               )
WarehouseRawData.drop_duplicates(subset=None, keep="first", inplace=True)
WarehouseRawData.index.name = 'IDNumber'
```

> 注意：为了加快处理速度，我在这里选取了仓库位置的一个子集。如果需要更多或更少的位置，欢迎更改参数。

```python
WarehouseData = WarehouseRawData.head(nMax)
WarehouseData = WarehouseData.append(WarehouseRawData.tail(nMax))
WarehouseData = WarehouseData.append(WarehouseRawData[WarehouseRawData.postcode == 'KA13'])
WarehouseData = WarehouseData.append(WarehouseRawData[WarehouseRawData.postcode == 'SW1W'])
WarehouseData.drop_duplicates(subset=None, keep="first", inplace=True)
print('Loaded Warehouses:', WarehouseData.columns.values)
print('################################')
```

### 存储仓库数据

```python
print('################')
sTable = 'Assess_Warehouse_UK'
print('Storing:', sDatabaseName, 'Table:', sTable)
WarehouseData.to_sql(sTable, conn, if_exists="replace")
print('################')
```

```python
print(WarehouseData.head())
print('################################')
print('Rows:', WarehouseData.shape[0])
print('################################')
```

## 导入商店数据

```python
sFileName = Base + '/' + Company + '/' + InputDir1 + '/' + InputFileName2
print('###########')
print('Loading:', sFileName)
ShopRawData = pd.read_csv(sFileName,
                          header=0,
                          low_memory=False,
                          encoding="latin-1"
                          )
ShopRawData.drop_duplicates(subset=None, keep="first", inplace=True)
ShopRawData.index.name = 'IDNumber'
ShopData = ShopRawData
print('Loaded Shops:', ShopData.columns.values)
print('################################')
```

> 注意：为了加快处理速度，我在这里选取了商店位置的一个子集。如果需要更多或更少的位置，欢迎更改参数。

## 初始数据操作

```
print('################')

sTable='Assess_Shop_UK'

print('Storing:',sDatabaseName,'Table:',sTable)

ShopData.to_sql(sTable, conn, if_exists="replace")

print('################')
```

```
print(ShopData.head())

print('################################')

print('Rows:',ShopData.shape[0])

print('################################')
```

## 连接总部

```
print('################')

sView='Assess_HQ'

print('Creating:',sDatabaseName,'View:',sView)

sSQL="DROP VIEW IF EXISTS" + sView + ";"

sql.execute(sSQL,conn)

sSQL="CREATE VIEW" + sView + "AS"

sSQL=sSQL+ "SELECT"

sSQL=sSQL+ "W.postcode AS HQ_PostCode,"

sSQL=sSQL+ "'HQ-' || W.postcode AS HQ_Name,"

sSQL=sSQL+ "round(W.latitude,6) AS HQ_Latitude,"

sSQL=sSQL+ "round(W.longitude,6) AS HQ_Longitude"

sSQL=sSQL+ "FROM"

sSQL=sSQL+ "Assess_Warehouse_UK as W"

sSQL=sSQL+ "WHERE"

sSQL=sSQL+ "TRIM(W.postcode) in ('KA13','SW1W');"

sql.execute(sSQL,conn)
```

为了计算仓库到仓库的路线，我建议对所有仓库采用一个简单的流程：所有仓库向所有仓库发货。

## 连接仓库

```
print('################')

sView='Assess_Warehouse'

print('Creating:',sDatabaseName,'View:',sView)

sSQL="DROP VIEW IF EXISTS" + sView + ";"

sql.execute(sSQL,conn)

sSQL="CREATE VIEW" + sView + "AS"

sSQL=sSQL+ "SELECT"

sSQL=sSQL+ "W.postcode AS Warehouse_PostCode,"

sSQL=sSQL+ "'WH-' || W.postcode AS Warehouse_Name,"

sSQL=sSQL+ "round(W.latitude,6) AS Warehouse_Latitude,"

sSQL=sSQL+ "round(W.longitude,6) AS Warehouse_Longitude"

sSQL=sSQL+ "FROM"

sSQL=sSQL+ "Assess_Warehouse_UK as W;"

sql.execute(sSQL,conn)
```

为了计算仓库到商店的路线，我建议对所有仓库采用一个简单的流程：所有仓库仅向同一邮政编码的商店发货。

## 通过邮政编码连接仓库与商店

```
print('################')

sView='Assess_Shop'

print('Creating:',sDatabaseName,'View:',sView)

sSQL="DROP VIEW IF EXISTS" + sView + ";"

sql.execute(sSQL,conn)

sSQL="CREATE VIEW" + sView + "AS"

sSQL=sSQL+ "SELECT"

sSQL=sSQL+ "TRIM(S.postcode) AS Shop_PostCode,"

sSQL=sSQL+ "'SP-' || TRIM(S.FirstCode) || '-' || TRIM(S.SecondCode) AS Shop_Name,"

sSQL=sSQL+ "TRIM(S.FirstCode) AS Warehouse_PostCode,"

sSQL=sSQL+ "round(S.latitude,6) AS Shop_Latitude,"

sSQL=sSQL+ "round(S.longitude,6) AS Shop_Longitude"

sSQL=sSQL+ "FROM"

sSQL=sSQL+ "Assess_Warehouse_UK as W"

sSQL=sSQL+ "JOIN"

sSQL=sSQL+ "Assess_Shop_UK as S"

sSQL=sSQL+ "ON"

sSQL=sSQL+ "TRIM(W.postcode) = TRIM(S.FirstCode);"

sql.execute(sSQL,conn)
```

我建议你现在运用图论知识来计算路线。按如下方式创建一个图：

```
G=nx.Graph()
```

```
print('################')

sTable = 'Assess_HQ'

print('Loading:',sDatabaseName,'Table:',sTable)

sSQL="SELECT DISTINCT"

sSQL=sSQL+ "*"

sSQL=sSQL+ "FROM"

sSQL=sSQL+ " " + sTable + ";"

RouteData=pd.read_sql_query(sSQL, conn)

print('################')
```

将总部节点添加到图中。

```
print(RouteData.head())

print('################################')

print('HQ Rows:',RouteData.shape[0])

print('################################')
```

```
for i in range(RouteData.shape[0]):

sNode0=RouteData['HQ_Name'][i]

G.add_node(sNode0,

Nodetype='HQ',

PostCode=RouteData['HQ_PostCode'][i],

Latitude=round(RouteData['HQ_Latitude'][i],6),

Longitude=round(RouteData['HQ_Longitude'][i],6))
```

添加仓库节点。

```
print('################')
```

# 数据加载与节点创建

## 仓库数据

加载仓库数据表 `Assess_Warehouse`。

```python
sTable = 'Assess_Warehouse'
print('Loading:', sDatabaseName, 'Table:', sTable)

sSQL = "SELECT DISTINCT"
sSQL = sSQL + "*"
sSQL = sSQL + "FROM"
sSQL = sSQL + " " + sTable + ";"

RouteData = pd.read_sql_query(sSQL, conn)
print('################')
print(RouteData.head())
print('################################')
print('Warehouse Rows:', RouteData.shape[0])
print('################################')
```

遍历仓库数据，将每个仓库添加为节点。

```python
for i in range(RouteData.shape[0]):
    sNode0 = RouteData['Warehouse_Name'][i]
    G.add_node(sNode0,
               Nodetype='Warehouse',
               PostCode=RouteData['Warehouse_PostCode'][i],
               Latitude=round(RouteData['Warehouse_Latitude'][i], 6),
               Longitude=round(RouteData['Warehouse_Longitude'][i], 6))
```

## 商店数据

加载商店数据表 `Assess_Shop`。

```python
sTable = 'Assess_Shop'
print('Loading:', sDatabaseName, 'Table:', sTable)

sSQL = " SELECT DISTINCT"
sSQL = sSQL + "*"
sSQL = sSQL + "FROM"
sSQL = sSQL + " " + sTable + ";"

RouteData = pd.read_sql_query(sSQL, conn)
print('################')
print(RouteData.head())
print('################################')
print('Shop Rows:', RouteData.shape[0])
print('################################')
```

遍历商店数据，将每个商店添加为节点。

```python
for i in range(RouteData.shape[0]):
    sNode0 = RouteData['Shop_Name'][i]
    G.add_node(sNode0,
               Nodetype='Shop',
               PostCode=RouteData['Shop_PostCode'][i],
               WarehousePostCode=RouteData['Warehouse_PostCode'][i],
               Latitude=round(RouteData['Shop_Latitude'][i], 6),
               Longitude=round(RouteData['Shop_Longitude'][i], 6))
```

## 创建边

现在我们来创建配送路线。我们将为每条路线计算文森特距离，并使用合适的卡车或叉车来指定运输方式。

```python
print('################################')
print('Loading Edges')
print('################################')
```

让我们循环遍历所有节点，并设置有效的路线。

```python
for sNode0 in nx.nodes_iter(G):
    for sNode1 in nx.nodes_iter(G):
```

让我们创建总部之间的路线，并计算总部的文森特距离：

```python
        if G.node[sNode0]['Nodetype'] == 'HQ' and \
           G.node[sNode1]['Nodetype'] == 'HQ' and \
           sNode0 != sNode1:
            distancemeters = round(
                vincenty(
                    (
                        G.node[sNode0]['Latitude'],
                        G.node[sNode0]['Longitude']
                    ),
                    (
                        G.node[sNode1]['Latitude'],
                        G.node[sNode1]['Longitude']
                    )
                ).meters,
                0)
            distancemiles = round(
                vincenty(
                    (
                        G.node[sNode0]['Latitude'],
                        G.node[sNode0]['Longitude']
                    ),
                    (
                        G.node[sNode1]['Latitude'],
                        G.node[sNode1]['Longitude']
                    )
                ).miles,
                3)

            if distancemiles >= 0.05:
                cost = round(150 + (distancemiles * 2.5), 6)
                vehicle = 'V001'
            else:
                cost = round(2 + (distancemiles * 0.10), 6)
                vehicle = 'ForkLift'

            G.add_edge(sNode0, sNode1, DistanceMeters=distancemeters,
                       DistanceMiles=distancemiles,
                       Cost=cost, Vehicle=vehicle)

            if nVSet == True:
                print('Edge-H-H:', sNode0, ' to ', sNode1,
                      ' Distance:', distancemeters, 'meters',
                      distancemiles, 'miles', 'Cost', cost, 'Vehicle', vehicle)
```

让我们创建总部到仓库的路线，并计算总部与仓库的文森特距离。

```python
        if G.node[sNode0]['Nodetype'] == 'HQ' and \
           G.node[sNode1]['Nodetype'] == 'Warehouse' and \
           sNode0 != sNode1:
            distancemeters = round(
                vincenty(
                    (
                        G.node[sNode0]['Latitude'],
                        G.node[sNode0]['Longitude']
                    ),
                    (
                        G.node[sNode1]['Latitude'],
                        G.node[sNode1]['Longitude']
                    )
                ).meters,
                0)
            distancemiles = round(
                vincenty(
                    (
                        G.node[sNode0]['Latitude'],
                        G.node[sNode0]['Longitude']
                    ),
                    (
                        G.node[sNode1]['Latitude'],
                        G.node[sNode1]['Longitude']
                    )
                ).miles,
                3)

            if distancemiles >= 10:
                cost = round(50 + (distancemiles * 2), 6)
                vehicle = 'V002'
            else:
                cost = round(5 + (distancemiles * 1.5), 6)
                vehicle = 'V003'

            if distancemiles <= 50:
                G.add_edge(sNode0, sNode1, DistanceMeters=distancemeters,
                           DistanceMiles=distancemiles,
                           Cost=cost, Vehicle=vehicle)

                if nVSet == True:
                    print('Edge-H-W:', sNode0, 'to', sNode1, ...)
```

# 创建路线与计算文森特距离

让我们创建仓库内部的路线，并计算仓库间的文森特距离。

```python
if nSet==True and \
G.node[sNode0]['Nodetype']=='Warehouse' and\
G.node[sNode1]['Nodetype']=='Warehouse' and\
sNode0 != sNode1:
    distancemeters=round(\
        vincenty(\
            (\
                G.node[sNode0]['Latitude'],\
                G.node[sNode0]['Longitude']\
            ),\
            (\
                G.node[sNode1]['Latitude']\
                ,\
                G.node[sNode1]['Longitude']\
            )\
        ).meters\
        ,0)
    distancemiles=round(\
        vincenty(\
            (\
                G.node[sNode0]['Latitude'],\
                G.node[sNode0]['Longitude']\
            ),\
            (\
                G.node[sNode1]['Latitude']\
                ,\
                G.node[sNode1]['Longitude']\
            )\
        ).miles\
        ,3)
    if distancemiles >= 10:
        cost = round(50+(distancemiles * 1.10),6)
        vehicle='V004'
    else:
        cost = round(5+(distancemiles * 1.05),6)
        vehicle='V005'
    if distancemiles <= 20:
        G.add_edge(sNode0,sNode1,DistanceMeters=distancemeters,\
                    DistanceMiles=distancemiles,\
                    Cost=cost,Vehicle=vehicle)
    if nVSet==True:
        print('Edge-W-W:',sNode0,'to',sNode1,\
              'Distance:',distancemeters,'meters',\
              distancemiles,'miles','Cost',cost,'Vehicle',vehicle)
```

让我们创建仓库到商店的路线，并计算文森特距离。

```python
if G.node[sNode0]['Nodetype']=='Warehouse' and \
G.node[sNode1]['Nodetype']=='Shop' and \
G.node[sNode0]['PostCode']==G.node[sNode1]['WarehousePostCode'] and\
sNode0 != sNode1:
    distancemeters=round(\
        vincenty(\
            (\
                G.node[sNode0]['Latitude'],\
                G.node[sNode0]['Longitude']\
            ),\
            (\
                G.node[sNode1]['Latitude']\
                ,\
                G.node[sNode1]['Longitude']\
            )\
        ).meters\
        ,0)
    distancemiles=round(\
        vincenty(\
            (\
                G.node[sNode0]['Latitude'],\
                G.node[sNode0]['Longitude']\
            ),\
            (\
                G.node[sNode1]['Latitude']\
                ,\
                G.node[sNode1]['Longitude']\
            )\
        ).miles\
        ,3)
    if distancemiles >= 10:
        cost = round(50+(distancemiles * 1.50),6)
        vehicle='V006'
    else:
        cost = round(5+(distancemiles * 0.75),6)
        vehicle='V007'
    if distancemiles <= 10:
        G.add_edge(sNode0,sNode1,DistanceMeters=distancemeters,\
                    DistanceMiles=distancemiles,\
                    Cost=cost,Vehicle=vehicle)
    if nVSet==True:
        print('Edge-W-S:',sNode0,'to',sNode1,\
              'Distance:',distancemeters,'meters',\
              distancemiles,'miles','Cost', cost,'Vehicle',vehicle)
```

让我们创建商店内部的路线，并计算文森特距离。

```python
if nSet==True and\
G.node[sNode0]['Nodetype']=='Shop' and\
G.node[sNode1]['Nodetype']=='Shop' and\
G.node[sNode0]['WarehousePostCode']==G.node[sNode1]['WarehousePostCode'] and\
sNode0 != sNode1:
    distancemeters=round(\
        vincenty(\
            (\
                G.node[sNode0]['Latitude'],\
                G.node[sNode0]['Longitude']\
            ),\
            (\
                G.node[sNode1]['Latitude']\
                ,\
                G.node[sNode1]['Longitude']\
            )\
        ).meters\
        ,0)
    distancemiles=round(\
        vincenty(\
            (\
                G.node[sNode0]['Latitude'],\
                G.node[sNode0]['Longitude']\
            ),\
            (\
                G.node[sNode1]['Latitude']\
                ,\
                G.node[sNode1]['Longitude']\
            )\
        ).miles\
        ,3)
    if distancemiles >= 0.05:
        cost = round(5+(distancemiles * 0.5),6)
        vehicle='V008'
    else:
        cost = round(1+(distancemiles * 0.1),6)
        vehicle='V009'
    if distancemiles <= 0.075:
        G.add_edge(sNode0,sNode1,DistanceMeters=distancemeters,\
                    DistanceMiles=distancemiles, \
                    Cost=cost,Vehicle=vehicle)
    if nVSet==True:
        print('Edge-S-S:',sNode0,'to',sNode1,\
              'Distance:',distancemeters,'meters',\
              distancemiles,'miles','Cost',cost,'Vehicle',vehicle)
```

如果商店属于不同仓库邮区，其成本与距离计算规则略有不同。

```python
if nSet==True and\
G.node[sNode0]['Nodetype']=='Shop' and\
G.node[sNode1]['Nodetype']=='Shop' and\
G.node[sNode0]['WarehousePostCode']!=G.node[sNode1]['WarehousePostCode'] and \
sNode0 != sNode1:
    distancemeters=round(\
        vincenty(\
            (\
                G.node[sNode0]['Latitude'],\
                G.node[sNode0]['Longitude']\
            ),\
            (\
                G.node[sNode1]['Latitude']\
                ,\
                G.node[sNode1]['Longitude']\
            )\
        ).meters\
        ,0)
    distancemiles=round(\
        vincenty(\
            (\
                G.node[sNode0]['Latitude'],\
                G.node[sNode0]['Longitude']\
            ),\
            (\
                G.node[sNode1]['Latitude']\
                ,\
                G.node[sNode1]['Longitude']\
            )\
        ).miles\
        ,3)
    cost = round(1+(distancemiles * 0.1),6)
    vehicle='V010'
    if distancemiles <= 0.025:
        G.add_edge(sNode0,sNode1,DistanceMeters=distancemeters,\
                    DistanceMiles=distancemiles,\
                    Cost=cost,Vehicle=vehicle)
    if nVSet==True:
        print('Edge-S-S:',sNode0,'to',sNode1,\
              'Distance:',distancemeters,'meters',\
              distancemiles,'miles','Cost',cost,'Vehicle',vehicle)
```

### 配送计划图分析与路径提取

```
'Distance:',distancemeters,'meters',\
distancemiles,'miles','Cost', cost,'Vehicle',vehicle)
```

```
sFileName=sFileDir + '/' + OutputFileName1
print('################################')
print('Storing:',sFileName)
print('################################')
nx.write_gml(G,sFileName)
sFileName=sFileName +'.gz'
nx.write_gml(G,sFileName)
```

```
print('Nodes:',nx.number_of_nodes(G))
print('Edges:',nx.number_of_edges(G))
```

```
sFileName=sFileDir + '/' + OutputFileName2
print('################################')
print('Storing:',sFileName)
print('################################')
```

#### 提取最短路径

现在我将引导你完成从配送计划图中提取路线或路径的过程。我们将使用 `nx.shortest_path(G,x,y)` 来提取图 `G` 上节点 `x` 和 `y` 之间的最短路径。

#### 创建路径

```
print('################################')
print('Loading Paths')
print('################################')
f = open(sFileName,'w')
l=0
sline = 'ID|Cost|StartAt|EndAt|Path|Measure'
if nVSet==True: print ('0', sline)
f.write(sline+ '\n')
for sNode0 in nx.nodes_iter(G):
    for sNode1 in nx.nodes_iter(G):
        if sNode0 != sNode1 and\
           nx.has_path(G, sNode0, sNode1)==True and\
           nx.shortest_path_length(G, \
                                   source=sNode0, \
                                   target=sNode1, \
                                   weight='DistanceMiles') < nMaxPath:
            l+=1
            sID='{:.0f}'.format(l)
            spath = ','.join(nx.shortest_path(G,\
                                              source=sNode0,\
                                              target=sNode1,\
                                              weight='DistanceMiles'))
            slength= '{:.6f}'.format(\
                nx.shortest_path_length(G,\
                                        source=sNode0,\
                                        target=sNode1,\
                                        weight='DistanceMiles'))
            sline = sID + '|"DistanceMiles"|"' + sNode0 + '"|"'\ + sNode1 + '"|"' + spath + '"|' + slength
            if nVSet==True: print (sline)
            f.write(sline + '\n')
            l+=1
            sID='{:.0f}'.format(l)
            spath = ','.join(nx.shortest_path(G,\
                                              source=sNode0,\
                                              target=sNode1,\
                                              weight='DistanceMeters'))
            slength= '{:.6f}'.format(\
                nx.shortest_path_length(G,\
                                        source=sNode0,\
                                        target=sNode1,\
                                        weight='DistanceMeters'))
            sline = sID + '|"DistanceMeters"|"' + sNode0 + '"|"'\ + sNode1 + '"|"' + spath + '"|' + slength
            if nVSet==True: print(sline)
            f.write(sline + '\n')
            l+=1
            sID='{:.0f}'.format(l)
            spath = ','.join(nx.shortest_path(G,\
                                              source=sNode0,\
                                              target=sNode1,\
                                              weight='Cost'))
            slength= '{:.6f}'.format(\
                nx.shortest_path_length(G,\
                                        source=sNode0,\
                                        target=sNode1,\
                                        weight='Cost'))
            sline = sID + '|"Cost"|"' + sNode0 + '"|"'\ + sNode1 + '"|"' + spath + '"|' + slength
            if nVSet==True: print (sline)
            f.write(sline + '\n')
f.close()
```

```
print('Nodes:',nx.number_of_nodes(G))
print('Edges:',nx.number_of_edges(G))
print('Paths:',sID)
```

```
print('################')
print('Vacuum Database')
sSQL="VACUUM;"
sql.execute(sSQL,conn)
print('################')
```

```
print('### Done!! ############################################')
```

保存 `Assess-Shipping-Routes.py` 文件，然后使用你的 Python 编译器编译并执行。这将在屏幕上生成一组演示值，并生成一个名为 `Assess_Shipping_Routes.gml` 的图形数据文件。

> **注意**：你可以使用文本编辑器查看 GML 文件。

路径查找器会生成一个包含最短路径的文件，名为 `Assess_Shipping_Routes.txt`。你现在拥有了一个有效的配送路线图基础，你可以用它来针对解决方案生成你想要的任何查询。这个最终的 Python 脚本完成了 Hillman Ltd. 的接入步骤。

#### Clark 有限公司

Clark 有限公司是一家会计公司，负责处理与 VKHCG 财务和人事相关的所有事务。我将让你运用新学到的知识去调查 Clark 公司。你能做到！

#### 简易外汇交易规划器

Clark 公司需要评估集团的外汇数据，以处理数据质量问题。我将引导你完成一个外汇解决方案的示例。

打开你的 Python 编辑器，在目录 `C:\VKHCG\04-Clark\02-Assess` 中创建一个名为 `Assess-Forex.py` 的文件。

现在将以下代码复制到该文件中：

```
################################################################
import sys
import os
import sqlite3 as sq
import pandas as pd
```

# 环境与平台设置

```python
if sys.platform == 'linux':
    Base=os.path.expanduser('~') + 'VKHCG'
else:
    Base='C:/VKHCG'

print('################################')
print('Working Base:', Base, 'using', sys.company)
print('################################')
```

# 公司与文件路径定义

```python
Company='04-Clark'
sInputFileName1='01-Vermeulen/01-Retrieve/01-EDS/02-Python/Retrieve-Country-Currency.csv'
sInputFileName2='04-Clark/01-Retrieve/01-EDS/01-R/Retrieve_Euro_ExchangeRates.csv'
```

# 数据库目录与连接

```python
sDataBaseDir=Base + '/' + Company + '/02-Assess/SQLite'
if not os.path.exists(sDataBaseDir):
    os.makedirs(sDataBaseDir)

sDatabaseName=sDataBaseDir + '/clark.db'
conn = sq.connect(sDatabaseName)
```

## 导入国家数据

```python
sFileName1=Base + '/' + sInputFileName1
print('################################')
print('Loading:', sFileName1)
print('################################')
CountryRawData=pd.read_csv(sFileName1, header=0, low_memory=False, encoding="latin-1")
CountryRawData.drop_duplicates(subset=None, keep="first", inplace=True)
CountryData=CountryRawData
print('Loaded Company:', CountryData.columns.values)
print('################################')
```

## 存储国家数据

```python
print('################')
sTable='Assess_Country'
print('Storing:', sDatabaseName, ' Table:', sTable)
CountryData.to_sql(sTable, conn, if_exists="replace")
print('################')
```

## 查看国家数据概览

```python
print(CountryData.head())
print('################################')
print('Rows: ', CountryData.shape[0])
print('################################')
```

## 导入外汇数据

```python
sFileName2=Base + '/' + sInputFileName2
print('################################')
print('Loading:', sFileName2)
print('################################')
ForexRawData=pd.read_csv(sFileName2, header=0, low_memory=False, encoding="latin-1")
ForexRawData.drop_duplicates(subset=None, keep="first", inplace=True)
ForexData=ForexRawData.head(5)
print('Loaded Company:', ForexData.columns.values)
print('################################')
```

## 存储外汇数据

```python
print('################')
sTable='Assess_Forex'
print('Storing:', sDatabaseName, 'Table:', sTable)
ForexData.to_sql(sTable, conn, if_exists="replace")
print('################')
```

## 查看外汇数据概览

```python
print(ForexData.head())
print('################################')
print('Rows:', ForexData.shape[0])
print('################################')
```

## 查询外汇唯一货币代码

```python
print('################')
sTable='Assess_Forex'
print('Loading:', sDatabaseName, 'Table:', sTable)
sSQL="select distinct"
sSQL=sSQL+ "A.CodeIn"
sSQL=sSQL+ "from"
sSQL=sSQL+ "Assess_Forex as A;"
CodeData=pd.read_sql_query(sSQL, conn)
print('################')
```

## 循环合并外汇与国家数据

```python
for c in range(CodeData.shape[0]):
    print('################')
    sTable='Assess_Forex & 2x Country > ' + CodeData['CodeIn'][c]
    print('Loading:', sDatabaseName, 'Table:', sTable)
    sSQL="select distinct"
    sSQL=sSQL+ "A.Date,"
    sSQL=sSQL+ "A.CodeIn,"
    sSQL=sSQL+ "B.Country as CountryIn,"
    sSQL=sSQL+ "B.Currency as CurrencyNameIn,"
    sSQL=sSQL+ "A.CodeOut,"
    sSQL=sSQL+ "C.Country as CountryOut,"
    sSQL=sSQL+ "C.Currency as CurrencyNameOut,"
    sSQL=sSQL+ "A.Rate"
    sSQL=sSQL+ "from"
    sSQL=sSQL+ "Assess_Forex as A"
    sSQL=sSQL+ "JOIN"
    sSQL=sSQL+ "Assess_Country as B"
```

sSQL=sSQL+ "ON A.CodeIn = B.CurrencyCode"

sSQL=sSQL+ "JOIN"

sSQL=sSQL+ "Assess_Country as C"

sSQL=sSQL+ "ON A.CodeOut = C.CurrencyCode"

sSQL=sSQL+ "WHERE"

sSQL=sSQL+ "A.CodeIn ='" + CodeData['CodeIn'][c] + "';"

ForexData=pd.read_sql_query(sSQL, conn).head(1000)

print('################')

print(ForexData)

print('################')

sTable='Assess_Forex_' + CodeData['CodeIn'][c]

print('Storing:',sDatabaseName,'Table:',sTable)

ForexData.to_sql(sTable, conn, if_exists="replace")

print('################')

print('################################')

print('Rows:',ForexData.shape[0])

print('################################')

################################################################

print('### Done!! ############################################')

################################################################

```

保存 `Assess-Forex.py` 文件，然后用你的 Python 编译器编译并执行。这将在屏幕上生成一组演示值。

## 财务状况

Clark 要求你处理 VKHCG 集团旗下公司的资产负债表。我将引导你完成一个示例资产负债表数据评估，以确保仅处理有效数据。

打开你的 Python 编辑器，在 `C:\VKHCG\04-Clark\02-Assess` 目录下创建一个名为 `Assess-Financials.py` 的文件。然后将以下代码复制到文件中：

```python
################################################################
import sys
import os
import sqlite3 as sq
import pandas as pd
################################################################
if sys.platform == 'linux':
    Base=os.path.expanduser('~') + 'VKHCG'
else:
    Base='C:/VKHCG'
print('################################')
print('Working Base:',Base,'using',sys.platform)
print('################################')
################################################################
Company='04-Clark'
sInputFileName='01-Retrieve/01-EDS/01-R/Retrieve_Profit_And_Loss.csv'
################################################################
sDataBaseDir=Base + '/' + Company + '/02-Assess/SQLite'
if not os.path.exists(sDataBaseDir):
    os.makedirs(sDataBaseDir)
################################################################
sDatabaseName=sDataBaseDir + '/clark.db'
conn = sq.connect(sDatabaseName)
################################################################
##### Import Financial Data
################################################################
sFileName=Base + '/' + Company + '/' + sInputFileName
print('################################')
print('Loading:',sFileName)
print('################################')
FinancialRawData=pd.read_csv(sFileName,header=0,low_memory=False, encoding="latin-1")
FinancialData=FinancialRawData
print('Loaded Company:',FinancialData.columns.values)
print('################################')
################################################################
print('################')
sTable='Assess-Financials'
print('Storing:',sDatabaseName,'Table:',sTable)
FinancialData.to_sql(sTable, conn, if_exists="replace")
print('################')
################################################################
print(FinancialData.head())
print('################################')
print('Rows:',FinancialData.shape[0])
print('################################')
################################################################
################################################################
print('### Done!! ############################################')
################################################################
```

保存 `Assess-Financials.py` 文件，然后用你的 Python 编译器编译并执行。这将在屏幕上显示一组演示值。

## 财务日历

Clark 存储了财务日历的所有主记录。因此，我建议我们从检索步骤的数据存储中导入日历。

打开你的 Python 编辑器，在 `C:\VKHCG\04-Clark\02-Assess` 目录下创建一个名为 `Assess-Calendar.py` 的文件。然后将以下代码复制到文件中：

```python
################################################################
import sys
import os
import sqlite3 as sq
import pandas as pd
################################################################
if sys.platform == 'linux':
    Base=os.path.expanduser('~') + 'VKHCG'
else:
    Base='C:/VKHCG'
print('################################')
print('Working Base:',Base,'using',sys.platform)
print('################################')
```

### 文档说明

保存`Assess-Calendar.py`文件，然后用你的Python编译器编译并执行。这将生成名为`Assess_Date`和`Assess_Time`的数据库表。

### 人员

Clark Ltd负责处理薪资，因此它保存了所有员工的记录。Clark还处理对所有供应商的付款，并接收来自所有公司客户的付款详情。

打开你的Python编辑器，在`C:\VKHCG\04-Clark\02-Assess`目录下创建一个名为`Assess-People.py`的文件。然后将以下代码复制到文件中：

```python
################################################################
import sys
import os
import sqlite3 as sq
import pandas as pd
################################################################
if sys.platform == 'linux':
    Base = os.path.expanduser('~') + 'VKHCG'
else:
    Base = 'C:/VKHCG'
print('################################')
print('Working Base:', Base, 'using', sys.platform)
print('################################')
################################################################
Company = '04-Clark'
sInputFileName1 = '01-Retrieve/01-EDS/02-Python/Retrieve-Data_female-names.csv'
sInputFileName2 = '01-Retrieve/01-EDS/02-Python/Retrieve-Data_male-names.csv'
```

# 数据准备脚本

## 初始设置

`sInputFileName3` = `'01-Retrieve/01-EDS/02-Python/Retrieve-Data_last-names.csv'`
`sOutputFileName1` = `'Assess-Staff.csv'`
`sOutputFileName2` = `'Assess-Customers.csv'`

## 数据库连接

`sDataBaseDir` = `Base` + `'/'` + `Company` + `'/02-Assess/SQLite'`

```python
if not os.path.exists(sDataBaseDir):
    os.makedirs(sDataBaseDir)
```

`sDatabaseName` = `sDataBaseDir` + `'/clark.db'`
`conn` = `sq.connect(sDatabaseName)`

## 导入女性姓名数据

`sFileName` = `Base` + `'/'` + `Company` + `'/'` + `sInputFileName1`

```python
print('################################')
print('加载中 :', sFileName)
print('################################')
```

`FemaleRawData` = `pd.read_csv(sFileName, header=0, low_memory=False, encoding="latin-1")`
`FemaleRawData.rename(columns={'NameValues': 'FirstName'}, inplace=True)`
`FemaleRawData.drop_duplicates(subset=None, keep="first", inplace=True)`
`FemaleData` = `FemaleRawData.sample(100)`

```python
print('################################')
print('################')
```

`sTable` = `'Assess_FemaleName'`

```python
print('存储中:', sDatabaseName, '表:', sTable)
```

`FemaleData.to_sql(sTable, conn, if_exists="replace")`

```python
print('################')
print('################################')
print('行数:', FemaleData.shape[0], '条记录')
print('################################')
```

## 导入男性姓名数据

`sFileName` = `Base` + `'/'` + `Company` + `'/'` + `sInputFileName2`

```python
print('################################')
print('加载中:', sFileName)
print('################################')
```

`MaleRawData` = `pd.read_csv(sFileName, header=0, low_memory=False, encoding="latin-1")`
`MaleRawData.rename(columns={'NameValues': 'FirstName'}, inplace=True)`
`MaleRawData.drop_duplicates(subset=None, keep="first", inplace=True)`
`MaleData` = `MaleRawData.sample(100)`

```python
print('################################')
print('################')
```

`sTable` = `'Assess_MaleName'`

```python
print('存储中:', sDatabaseName, '表:', sTable)
```

`MaleData.to_sql(sTable, conn, if_exists="replace")`

```python
print('################')
print('################################')
print('行数:', MaleData.shape[0], '条记录')
print('################################')
```

## 导入姓氏数据

`sFileName` = `Base` + `'/'` + `Company` + `'/'` + `sInputFileName3`

```python
print('################################')
print('加载中:', sFileName)
print('################################')
```

`SurnameRawData` = `pd.read_csv(sFileName, header=0, low_memory=False, encoding="latin-1")`
`SurnameRawData.rename(columns={'NameValues': 'LastName'}, inplace=True)`
`SurnameRawData.drop_duplicates(subset=None, keep="first", inplace=True)`
`SurnameData` = `SurnameRawData.sample(200)`

```python
print('################################')
print('################')
```

`sTable` = `'Assess_Surname'`

```python
print('存储中:', sDatabaseName, '表:', sTable)
```

`SurnameData.to_sql(sTable, conn, if_exists="replace")`

```python
print('################')
print('################################')
print('行数:', SurnameData.shape[0], '条记录')
print('################################')
```

## 加载数据表

```python
print('################')
```

`sTable` = `'Assess_FemaleName & Assess_MaleName'`

```python
print('加载中:', sDatabaseName, '表:', sTable)
```

```
sSQL = "select distinct"
sSQL = sSQL + " A.FirstName,"
sSQL = sSQL + " 'Female' as Gender"
sSQL = sSQL + " from"
sSQL = sSQL + " Assess_FemaleName as A"
sSQL = sSQL + " UNION"
sSQL = sSQL + " select distinct"
sSQL = sSQL + " A.FirstName,"
sSQL = sSQL + " 'Male' as Gender"
sSQL = sSQL + " from"
sSQL = sSQL + " Assess_MaleName as A;"
FirstNameData = pd.read_sql_query(sSQL, conn)
print('################')
#################################################################
```

# 数据库表操作与数据采样

## 评估姓氏数据存储

将评估姓氏数据存入数据库表中。

```python
print('################')

sTable = 'Assess_FirstName'

print('存储中:', sDatabaseName, '表:', sTable)

FirstNameData.to_sql(sTable, conn, if_exists="replace")

print('################')
```

## 构建并采样姓名组合数据

从多个表中组合姓名数据并进行采样。

```python
################################################################

################################################################

print('################')

sTable = 'Assess_FirstName x2 & Assess_Surname'

print('加载中:', sDatabaseName, '表:', sTable)

sSQL = "select distinct"

sSQL = sSQL + " A.FirstName,"

sSQL = sSQL + " B.FirstName AS SecondName,"

sSQL = sSQL + " C.LastName,"

sSQL = sSQL + " A.Gender"

sSQL = sSQL + " from"

sSQL = sSQL + " Assess_FirstName as A"

sSQL = sSQL + " ,"

sSQL = sSQL + " Assess_FirstName as B"

sSQL = sSQL + " ,"

sSQL = sSQL + " Assess_Surname as C"

sSQL = sSQL + " WHERE"

sSQL = sSQL + " A.Gender = B.Gender"

sSQL = sSQL + " AND"

sSQL = sSQL + " A.FirstName <> B.FirstName;"

PeopleRawData = pd.read_sql_query(sSQL, conn)

People1Data = PeopleRawData.sample(10000)
```

## 构建并采样姓名与姓氏组合数据

将姓名与姓氏进行组合查询并采样。

```python
sTable = 'Assess_FirstName & Assess_Surname'

print('加载中:', sDatabaseName, '表:', sTable)

sSQL = "select distinct"

sSQL = sSQL + " A.FirstName,"

sSQL = sSQL + " '' AS SecondName,"

sSQL = sSQL + " B.LastName,"

sSQL = sSQL + " A.Gender"

sSQL = sSQL + " from"

sSQL = sSQL + " Assess_FirstName as A"

sSQL = sSQL + " ,"

sSQL = sSQL + " Assess_Surname as B;"

PeopleRawData = pd.read_sql_query(sSQL, conn)

People2Data = PeopleRawData.sample(10000)
```

## 合并并打印最终数据

将两种采样结果合并，并输出最终数据集。

```python
PeopleData = People1Data.append(People2Data)

print(PeopleData)

print('################')
```

```python
#################################################################
```

# print('################')

sTable = 'Assess_People'

print('存储中:', sDatabaseName, '表:', sTable)

PeopleData.to_sql(sTable, conn, if_exists="replace")

print('################')

################################################################

sFileDir = Base + '/' + Company + '/02-Assess/01-EDS/02-Python'

if not os.path.exists(sFileDir):

    os.makedirs(sFileDir)

################################################################

sOutputFileName = sTable + '.csv'

sFileName = sFileDir + '/' + sOutputFileName

print('################################')

print('存储中:', sFileName)

print('################################')

PeopleData.to_csv(sFileName, index=False)

print('################################')

################################################################

print('### 完成!! ############################################')

################################################################

```

保存`Assess-People.py`文件，然后使用`Python`编译器编译并执行。这将在屏幕上生成一组演示值。

## 总结

恭喜！你已经完成了评估超步骤。现在你有了一个稳定的基础，可以将多个数据源混合到数据仓库中。

我已经引导你完成了首个数据质量处理步骤。你所取得的成果将作为第 9 章的基础数据。

那么，在本书的这个阶段，你应该了解些什么呢？你应该知道如何：

*   有效解决不同的数据质量问题。
*   在执行评估处理时，转换并更新数据映射矩阵。你评估的每个数据源都是一个清晰的数据科学信息来源。
*   成功执行特征工程。
*   通过评估，引入针对其他数据源的标准，并查阅货币代码和国家代码等标准。
*   将列格式数据转换为图形关系。这是一项需要掌握的重要数据科学技能。

**注意：** 在现实世界的数据科学项目中，到目前为止，你可能已经消耗了高达 50% 的项目资源，才达到你今天所实现的稳定数据集。做得好！数据准备是消耗你人力、预算和计算资源的最大环节。

现在，你已经掌握了评估超步骤，并熟练掌握了处理数据质量的技巧。接下来的步骤在本质上更具结构性，因为你将创建一个通用的数据结构，为第 9 章开始的全方位数据科学探索奠定基础。

我建议你准备些好吃的零食，然后我们就可以进入下一章，在那里我们将为你的数据科学构建一个数据仓库。

