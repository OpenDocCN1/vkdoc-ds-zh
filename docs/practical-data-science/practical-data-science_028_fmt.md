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