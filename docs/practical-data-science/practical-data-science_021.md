# 7. 检索超步骤

`Retrieve` 超步骤是一种实用方法，用于将包含各种外部数据源的数据湖完整导入处理生态系统。`Retrieve` 超步骤是您的数据科学与源系统之间的首次接触。我将引导您掌握一种方法论，指导您如何通过运用数据科学技能，处理从数据发现到获取评估当前系统所需全部数据的整个过程。

成功检索数据是确保您实施优质数据科学的重要基石。数据血缘以最低粒度级别提供数据元素的审计追踪，确保完整的数据治理。数据治理支持系统指南、处理策略、政策制定及处理实施的元数据管理。数据质量和主数据管理有助于在您提供完整数据源元数据的前提下，用更多业务价值丰富数据血缘。

> **提示**  
> 我建议导入所有通过该层的数据，这将确保数据血缘和治理。

`Retrieve` 超步骤支撑着生态系统的边界，您的数据科学在此与外部数据世界直接接触。我将推荐一套当前可用的数据结构，用于处理探索关键业务知识所需应对的数据洪流。

### 数据湖

Pentaho 公司的 CTO 詹姆斯·狄克逊被公认为创造了“数据湖”这一术语。正如他在如今著名的博客¹中所描述的那样：

> 如果你将数据集市视为瓶装水商店——经过净化、包装并结构化以便轻松饮用——那么数据湖就是一片更接近自然状态的大水域。

该博客中还有一段不那么常被引用的内容：

> 数据湖中的内容从源头源源不断地流入，充盈湖泊，而湖的各种使用者可以前来考察、潜入或取样。

自这篇博客以来，大量的研究和商业活动分类使该术语声名鹊起，甚至声名狼藉。不幸的是，该术语包罗万象，以至于你可以将生态系统的边缘以及业务涉及的任何数据都描述为数据湖的一部分。我曾花费数小时甚至数天，在智库、理论和科学辩论中探讨数据湖的复杂性和细微差别。

因此，基于本书的实践和流程，我将数据湖定义如下：公司的数据湖涵盖您的业务被授权处理的所有数据，旨在提升企业核心成果的盈利能力。数据湖是您的公司在业务生命周期内与之交互的完整数据世界。简而言之，如果您为执行业务任务而生成或消费数据，那么该数据就属于您公司的数据湖。

我将使用与天然湖泊相关的术语来解释具体的数据湖概念。因此，正如湖泊需要河流和溪流来补给水源，数据湖将不可避免地消耗来自上游的数据源洪流，并将其输送给下游合作伙伴。

> **提示**  
> 切勿忽视未处理数据源的价值。

我曾有一位商业客户，他收到了物流公司整个季度的航运清单光盘，却仅仅将其收集在一个塑料容器中长达五年，因为他认为这些数据是多余的，因为他已在线获取了每批货物的电子邮件。幸运的是，我的一名实习生花时间加载了一张光盘，并注意到完整的供应链路径和编码程序也包含在该光盘中。通过将这些新数据添加到公司现有的数据湖中，这一新信息使数据湖在物流实际成本方面丰富了超过 30%。不要限制您数据湖的集水区域，因为最有利可图的知识通常来自将您过去认为不重要的数据进行融合。

> **注意**  
> 我在本书中使用了大量代码片段来演示各种概念。我假定您已准备好支持 R 和 Python 的环境来尝试这些示例。执行这些流程有助于知识传递。

### 数据沼泽

既然我谈到了数据湖以及加载业务数据以发现未知价值的重要性，我也必须解释数据沼泽。我听过并见过各种断言数据湖是邪恶的演示。我持不同观点。任何不受管理的数据结构都是邪恶的。另一方面，我坦然承认，我曾花费大量时间修复包含这些邪恶数据生态系统的结构。

数据沼泽就是未经管理的数据湖。它们并不可怕。它们需要被驯服。

以下是我避免数据沼泽的四个关键步骤。

### 从具体的业务问题开始

将大量数据随意倾倒进数据湖，而心中没有明确目的，将带来巨大的业务风险。我曾无数次看到数据加载协议中声称要加载所有数据。请参阅关于业务需求的第 4 章中关于“模糊性”的部分。

我的建议是，对您拥有的全部数据集进行全面分析，然后对数据应用元数据分类，明确完整的数据血缘，以允许其进入数据湖。必须使数据湖能够收集回答业务问题所需的数据。

例如，如果您想计算过去五年的货运模式，则需要数据湖中存在过去五年与货运相关的所有数据。您还需要启动一个流程，按照约定的时间间隔收集未来的相关数据，以确保一年后您能使用相同的数据科学方法来评估之前五年的数据。数据血缘确保您在得到结果后能够追溯到数据源。

> **提示**  
> 至少，要确定数据的真实来源以及数据进入数据湖时的完整时间戳。您能提供的数据血缘越多越好。确保您拥有经过认证的数据源。

**示例：**

更好的说法是：“我发现您的货运模式良好，并且我已经通过让我们的物流经理签字确认真实性和完整性，确保我们拥有过去五年的所有数据。” 这种陈述为您的数据科学提供了坚实的基础和经过认证的血缘。

去年，我手下的一名初级数据科学家试图说服一位首席财务官，声称他在档案室发现的那些来源不明、日期不详、装有货运发票的光盘，足以作为他四周数据科学工作的良好数据血缘。遗憾的是，我必须报告，那份结果文档加上那些光盘，现在已成为我的“如何不进行数据科学”演示的一部分！

同一名数据科学家，十天后，使用相同的数据科学算法，但配以经过认证的血缘，为我的客户提供了重大的成本节约方案。

### 数据治理

数据治理、数据访问和数据安全的作用并不会因为数据湖中的数据量庞大而消失。如果不加以管理，它们只会汇聚成一个更糟糕的问题。

> **警告**  
> 危险区域！在拍字节甚至太字节量级的数据湖中查找一条未经管理的数据，将耗费您数小时的时间。

请提前在数据治理上投入时间，因为事后恢复并不容易。我通常会将项目 70%以上的时间用于数据源的治理和分类。我将在第 8 章至第 11 章中演示，本章所建立的完善数据湖如何使数据处理变得如此简单。



#### 数据源目录

将摄入数据链接到数据源的元数据是任何数据湖的必备之物。建议你针对所处理的数据，遵循以下通用规则。

-   唯一数据目录编号：我通常使用`YYYYMMDD/NNNNNN/NNN`格式。例如，`20171230/000000001/001` 表示该数据于 2017 年 12 月 30 日首次注册到元数据注册表中，是数据类型 1 的数据源 1。这是一个关键要求。
-   简短描述（不超过 100 个字符）：国家代码与国家名称（国家代码—ISO 3166）
-   详细描述（尽可能完整）：VKHC 用作国家条目标准的国家代码与国家名称
-   外部数据源联系信息：来自[`www.iso.org/iso-3166-country-codes.html`](http://www.iso.org/iso-3166-country-codes.html) 的 ISO 3166-1:2013 代码列表
-   预期更新频率：不定期（即无固定频率，也称为临时）。其他选项包括准实时、每 5 秒、每分钟、每小时、每日、每周、每月或每年。
-   内部业务目的：验证国家代码和名称。

我发现在数据处理中，如果数据源目录是最新的，那么处理过程的其余部分就会轻松无压力。

#### 业务术语表

业务术语表映射数据源字段，并将其分类到相应的业务线中。对于任何优秀的数据湖来说，此术语表都是必备之物。

创建一个包含以下信息的数据映射注册表：

-   唯一数据目录编号：我通常使用`YYYYMMDD/NNNNNN/NNN`格式。
-   唯一数据映射编号：我通常使用`NNNNNNN/NNNNNNNNN`格式。例如，`0000001/000000001` 表示将外部字段 1 映射到内部字段 1。
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

我将使用一个样本数据集来展示此过程的每个步骤。因此，你将需要使用 **R** 或 **Python** 来执行一些数据处理。

**免责声明**

在本书中，当我向你介绍几个 **R** 和 **Python** 数据处理包时，我只会提供这些工具的基本介绍。我建议你自行研究这些包的更多细节，以提高你的整体理解和能力。

对于 **R**，请执行以下命令：

启动你的 RStudio 编辑器。

**注意**

在本章的其余部分，我将在 Windows 生态系统下使用 `setwd(C:/VKHCG)`，在 Linux 生态系统下使用 `setwd(/home/<youruser>/VKHCG)`。

执行以下命令以导入数据加载包：

```
library(readr)
```

**注意**

更多详情请参见[`https://cran.r-project.org/web/packages/readr/index.html`](https://cran.r-project.org/web/packages/readr/index.html)。

这应该会加载一个有趣的 R 数据加载包。那么，让我们加载 Vermeulen 公司的示例数据。

让我们加载一个名为 `IP_DATA_ALL.csv` 的表。

```
Base=getwd()
FileName=paste0(Base,'/01-Vermeulen/00-RawData/IP_DATA_ALL.csv')
IP_DATA_ALL <- read_csv(FileName)
```

你应该已经成功加载了一个包含八列的数据集。

使用 `spec(IP_DATA_ALL)` 命令应产生以下结果：

```
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

-   ID（整数类型）
-   Place name（字符类型）
-   Post code（字符类型）
-   Latitude（数值双精度类型）
-   Longitude（数值双精度类型）
-   First IP number（整数类型）
-   Last IP number（整数类型）

现在你可以通过 `View(IP_DATA_ALL)` 查看数据。你将看到一个包含数据的数据网格出现。

## 数据字段名称验证

我用它来以简单的方式验证和核实检索处理中的数据字段名称。要添加一个额外的库，你需要运行以下命令：

**警告**

通过执行`install.packages(tibble)` 确保你拥有最新的包。因此，如果我的某个校对人员报告说`set_tidy_names()` 命令不存在，我会说明我使用的是 R 3.4.1 和 `tibble` 1.3.4 版本。

```
library(tibble)
```

（参见[`https://cran.r-project.org/web/packages/tibble/`](https://cran.r-project.org/web/packages/tibble/) 。）

```
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

```
IP_DATA_ALL_FIX=set_tidy_names(IP_DATA_ALL, syntactic = TRUE, quiet = TRUE)
```

通过使用命令 `View(IP_DATA_ALL_FIX)`，你可以检查列是否已修复。

干得好！你刚刚修复了数据字段名称。那么，现在让我们保存你的进度。

```
Base=getwd()
FileDir= paste0(Base,'/01-Vermeulen/01-Retrieve/01-EDS)
dir.create(FileDir)
FileDir= paste0(Base,'/01-Vermeulen/01-Retrieve/01-EDS/01-R')
dir.create(FileDir)
FileName=paste0(FileDir,'/IP_DATA_ALL_FIX.csv')
write.csv(IP_DATA_ALL_FIX, FileName)
```

你现在应该有一个名为 `IP_DATA_ALL_FIX.csv` 的文件。



##### 每条数据条目的唯一标识符

在系统内分配一个与给定文件名无关的唯一标识符。这确保了系统能够处理来自不同路径的不同文件，并以有效的方式跟踪所有数据条目。然后，为检索到的文件中的每条记录或每个数据元素分配一个唯一标识符。

> **提示**  
> 我发现，额外花时间执行此任务可以防止重大的数据引用问题。在规模化情况下，将相似甚至相同的数据多次加载到数据湖中的概率很高。

现在，检查你的样本数据。

对于 R 语言，请参见下文。

要添加唯一标识符，请运行以下命令：

```
IP_DATA_ALL_with_ID=rowid_to_column(IP_DATA_ALL_FIX, var = "RowID")
```

检查你是否成功添加了唯一标识符，如下所示：

```
View(IP_DATA_ALL_with_ID)
```

这将显示每条记录都有一个名为“Row ID”的唯一 ID。

那么，让我们保存进度。

```
Base=getwd()
FileName=paste0(FileDir,'/IP_DATA_ALL_with_ID.csv')
write.csv(IP_DATA_ALL_with_ID,FileName)
```

你现在应该有一个名为 `IP_DATA_ALL_with_ID.csv` 的文件。

##### 每个数据列的数据类型

确定每列的最佳数据类型，以协助你完成业务术语表，并确保你记录了正确的导入处理规则。

使用以下 R 命令：

```
sapply(IP_DATA_ALL_with_ID, typeof)
```

你应该会看到已加载数据的完整数据类型分析。

| RowId | ID | Country | Place.Name | Post.Code | Latitude | Longitude | First.IP.Number | Last.IP.Number |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| integer | integer | character | character | character | double | double | integer | integer |

> **警告**  
> 留意那些看似数值型但实际上应为文本列的数据类型，例如账号或识别号。

##### 每列的直方图

我通常会为每一列生成直方图，以确定数据值的分布情况。在本章的实践部分，我将针对样本数据集生成直方图，以演示如何执行此操作。

现在我们将数据集转换为数据表，这将帮助我们以更增强的方式研究数据。我将在 R 中向你展示如何查找国家数据的直方图，如下所示：

| 排名前两位的国家代码 | 频数 |
| --- | --- |
| US | 512714 |
| GB | 127069 |

```
library(data.table)
hist_country=data.table(Country=unique(IP_DATA_ALL_with_ID[is.na(IP_DATA_ALL_with_ID ['Country']) == 0, ]$Country))
setorder(hist_country,'Country')
hist_country_with_id=rowid_to_column(hist_country, var = "RowIDCountry")
View(hist_country_with_id)
IP_DATA_COUNTRY_FREQ=data.table(with(IP_DATA_ALL_with_ID, table(Country)))
View(IP_DATA_COUNTRY_FREQ)
```

**业务洞察：**
- 数据量最大的两个子集分别来自美国和英国。
- 美国的数据量是英国的四倍多一点。

这些信息对于设计已加载数据的并行处理非常有用。

在并行处理期间，由于并行处理的特定拆分标准，数据倾斜可能导致不必要的等待时间或处理器过载。在上述案例中，如果你按每个国家代码分配一个处理器来分散数据，由于数据量差异，美国和英国的处理时间会比其他国家的代码更长。即使是英国和美国之间的数据量不匹配，也会导致更多的处理倾斜。

> **谨慎**  
> 并行处理是数据科学的一项基本要求，了解你的数据模型和/或算法在处理非均匀数据分布时的行为非常重要。有时，不采用并行处理可能更好，因为拆分过程所花费的时间可能超过它给整个流程带来的节省。

那么，让我们保存进度。

```
Base=getwd()
FileName=paste0(FileDir,'/IP_DATA_COUNTRY_FREQ.csv')
IP_DATA_COUNTRY_FREQ.to_csv(FileName)
```

你现在应该有一个名为 `IP_DATA_COUNTRY_FREQ.csv` 的文件。

> **提示**  
> 当你开始了解数据湖的分析特征时，你将开始制定如何处理数据的计划。我通常会在数据湖的数据概况中保存一些关键笔记。  
> 对数据湖进行概况分析通常是一种结构化的方法。我和我的团队会为每个数据源保存一份简单的速查表，记录其数据概况。我们通过 GitHub 源代码管理共享此表，以确保任何新发现都能传递给所有相关人员。

让我们看看你可能想要研究的一些其他关键特征。

直方图解释了整个数据集在值集合上的分布情况。我将在 R 中向你展示如何查找纬度数据字段的直方图。

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

**业务洞察：**
- 数据量最大的两个纬度是 35.6427 和 51.5092。
- 排名前两位的纬度之间的分布似乎几乎相等。

那么，让我们保存进度。

```
Base=getwd()
FileName=paste0(FileDir,'/IP_DATA_Latitude_FREQ.csv')
IP_DATA_Latitude_FREQ.to_csv(FileName)
```

你现在应该有一个名为 `IP_DATA_Latitude_FREQ.csv` 的文件。

我将在 R 中向你展示如何查找经度数据的直方图。

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

你现在应该有一个名为 `IP_DATA_Longitude_FREQ.csv` 的文件。



