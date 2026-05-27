# `data.table` 简介

## `iris` 数据集示例

`1: virginica 50 50 50 50`

如果要将长格式数据更好地转换为宽格式，需要一个唯一标识符，以便公式创建仅包含单个值的唯一单元格。除非需要对结果进行聚合，否则对于给定变量，公式参数应产生单个值，而非多个值。

在本例中，由于长格式数据中 `variable` 列有四个可能取值，只要存在一个永不重复的唯一键（例如 `Sepal.Length`），`dcast()` 就能正常工作。换句话说，如果宽格式数据有一个唯一键，我们可以将该唯一键与长格式数据中的变量名组合起来，从而精确地将长格式数据集中的单元格映射回宽格式数据。公式左侧包含键或 ID 变量。公式左侧（波浪号 `~` 左边）还可以包含任何不发生变化的变量（例如，如果最初将数据从宽格式重塑为长格式，这些变量会通过 `melt()` 的 `id.vars` 参数指定）。公式右侧（波浪号 `~` 右边）包含一个或多个变量，这些变量指示长格式数据集中的某一行应属于新宽格式数据集的哪一列。

在我们的长格式 `iris` 数据集示例中，`ID` 和 `Species` 变量不发生变化，因此应放在公式左侧。名为 `variable` 的变量指示长格式数据集中的特定值代表 `Sepal.Length`、`Sepal.Width`、`Petal.Length` 还是 `Petal.Width`。因此，对于本例，公式为 `ID + Species ~ variable`：

```r
diris.wide2 <- dcast(diris.long2, ID + Species ~ variable)
```

`diris.wide2` 的结果如下：

```
##      ID Species Sepal.Length Sepal.Width Petal.Length Petal.Width
##  1:   1  setosa          5.1         3.5          1.4         0.2
##  2:   2  setosa          4.9         3.0          1.4         0.2
##  3:   3  setosa          4.7         3.2          1.3         0.2
##  ---
## 148: 148 virginica          6.5         3.0          5.2         2.0
## 149: 149 virginica          6.2         3.4          5.4         2.3
## 150: 150 virginica          5.9         3.0          5.1         1.8
```

回到第一个 `diris.long` 数据（其中包含萼片和花瓣的长度与宽度的二水平因子），也可以将其强制转换回宽格式。对 `dcast()` 的调用与之前的示例类似。我们指定长格式数据集、一个包含未堆叠的 `ID` 和 `Species` 变量的公式，以及指示数据每行所属类别的变量 `Type`。

对于这种“不算太长”（姑且这么称呼）的数据，我们需要一个额外的参数 `value.var`，以告知 `dcast()` 长格式数据中哪些变量是重复测量值。该参数可以是一个字符向量，包含长格式数据集中将对应宽格式数据集中多个变量的所有变量。在本例中，这些变量是 `Sepal` 和 `Petal`，它们在长格式数据中同时包含长度和宽度值（由 `Type` 变量指示）。此外，必须选择分隔符，因为 `data.table` 的列名可能有不同的分隔符。通过 `sep =` 形参进行选择，此处为了连续性使用句点，因为原始宽格式 `iris` 数据集中变量就是如此标记的。我们仅展示 `dcast()` 命令，并通过 `all.equal()` 函数检查无论使用哪种方法，都能恢复宽格式数据。要查看结果，请在控制台键入 `diris.wide`：

```r
diris.wide <- dcast(diris.long, ID + Species ~ Type,
                    value.var = list("Sepal", "Petal"),
                    sep = ".")
all.equal(diris.wide2, diris.wide)
```

```
## [1] 真
```

### 7.6 小结

在本章中，您学习了 `R` 中两种强大（且更现代）的数据管理方式之一。具体来说，我们使用 `data.table` 处理的数据类型是列和行，即表格数据。在下一章中，我们将更深入地探讨数据操作。

接下来，您将接触到 `dplyr` [29]，这是 `R` 中另一种现代的数据管理方式。在本节结束时，我们将探讨 `R` 外部的数据库。表 7-1 总结了本章中使用的主要函数。

### 第 7 章 data.table 简介

**表 7-1. 本章所述主要函数及其功能摘要列表**

| 函数 | 功能 |
| --- | --- |
| `d[i, j, by]` | 对数据表进行子集筛选、计算或修改的通用结构。 |
| `as.data.table()` | 将数据结构强制转换为数据表。 |
| `setkey()` | 将数据表作为第一个参数，然后根据后续传递给函数的列名参数创建键。 |
| `haskey()` | 对数据表调用此函数，返回一个布尔值。 |
| `key()` | 返回数据表的键（如果存在）。 |
| `order()` | 控制数据表中某一列数据的升序或降序排列。 |
| `tables()` | 返回当前处于活动状态的所有数据表。 |
| `anyDuplicated()` | 返回第一个重复条目的位置。 |
| `duplicated()` | 对所有行返回 `TRUE` 或 `FALSE` 以指示重复情况。可指定键，否则基于所有列判断。 |
| `unique()` | 返回仅包含唯一行的数据表。同样，可以指定特定的行。如果存在键，则基于该键；如果不存在，则基于所有列。 |
| `setnames()` | 允许修改列名。 |
| `setcolorder()` | 允许调整列的顺序。 |
| `:=` | 数据表内部的赋值运算符。 |
| `.()` | `list()` 的简写形式。 |
| `.N` | 表示数据表中的行数，或在子集（例如按分组变量执行计算操作时）中的行数。 |
| `merge()` | 基于匹配的键列合并两个数据表。 |
| `melt()` | 将宽格式数据转换为长格式数据。 |
| `dcast()` | 将长格式数据转换为宽格式数据。 |

## 第 8 章 高级 `data.table`

我们已经介绍了 `data.table` 包，它是本章的核心，涵盖了在广义的数据管理范畴内访问、编辑和操作数据的基础知识。数据管理虽然不显眼，但却是数据可视化或分析的关键第一步。此外，特定分析项目的大部分时间通常都花在数据管理上。例如，一旦数据清洗完毕并符合预期格式，在 `R` 中运行线性模型只需一行代码。数据管理具有挑战性，因为原始数据的类型、形状和格式多种多样，并且缺失数据很常见。此外，您可能还需要合并或结合不同的数据源。在本章中，我们将超越 `data.table` 的基本用法，处理更复杂的数据管理任务。

数据整理通常有两种风格。一次性转换通常是手动的，因为如果代码不被重复使用（例如，更改一两个变量名或重命名数据文件以保持一致性），编写代码往往效率不高。另一方面，对于需要重复执行的操作（例如，重命名或标记数百个变量）或处理较大规模的数据，则更多地使用编程来进行数据管理。如果需要，请安装 `stringdist` 包 [23]；另一个需要的库是 `foreign` [15]：

```r
library(stringdist)
library(data.table)
library(foreign)
options(stringsAsFactors = FALSE,
        datatable.print.nrows = 20, ## 如果超过 20 行
        datatable.print.topn = 3,   ## 打印前 3 行和后 3 行
        digits = 2)                 ## 减少 R 打印的小数位数
```

© Matt Wiley and Joshua F. Wiley 2020
M. Wiley and J. F. Wiley, *Advanced R 4 Data Programming and the Cloud*,
[`doi.org/10.1007/978-1-4842-5973-3_8`](https://doi.org/10.1007/978-1-4842-5973-3_8#DOI)

### 8.1 数据清洗/整理

为了获得需要*编程*风格整理的数据，我们从全国儿童健康调查（2003 年）[4] 的网站 [`doi.org/10.3886/ICPSR04691.v1`](https://doi.org/10.3886/ICPSR04691.v1) 下载数据，这里我们选择了 *Stata* 格式。我们将文件夹放置在工作目录中的 `data/ch08/` 文件夹下，并使用 `read.dta()` 将数据导入 `R`。将其转换为数据表后，我们将键设置为识别号列 `IDNUMR`：

```r
d <- read.dta("data/ch08/ICPSR_04691/DS0001/04691-0001-Data.dta")
d <- as.data.table(d)
setkey(d, IDNUMR)
```

为了了解这个大数据集的结构（有超过 300 个变量），我们使用 `str()` 函数。我们抑制属性以专注于列名和每列内部的数据类型。设置 `strict.width = "cut"` 以强制执行我们之前设置的选项，并且我们只看前 20 列：

```r
str(d, give.attr = FALSE, strict.width = "cut", list.len = 20)
```

```
## Classes 'data.table' and 'data.frame': 102353 obs. of 301 variables:
## $ IDNUMR   : int  1 2 3 4 5 6 7 8 9 10 ...
## $ STATE    : Factor w/ 51 levels "1-AK","2-AL",..: 35 25 45 18 36 37..
## $ MSA_STAT : Factor w/ 4 levels "-2 - MISSING",..: 4 4 4 4 4 4 4 4 4..
## $ AGEYR_CH : int  12 1 10 7 9 11 9 15 17 1 ...
## $ TOTKIDS4 : Factor w/ 4 levels "1 - 1 CHILD",..: 1 1 1 3 4 2 3 3 1 ..
## $ AGEPOS4  : Factor w/ 5 levels "1 - ONLY CHILD",..: 1 1 1 4 2 2 3 2..
## $ S1Q01    : Factor w/ 8 levels "-4 - PARTIAL INTERVIEW",..: 6 5 6 5..
## $ RELATION : Factor w/ 6 levels "-2 - MISSING",..: 3 2 3 3 2 2 2 4 3..
## $ TOTADULT : Factor w/ 6 levels "-2 - MISSING",..: 4 3 2 3 2 3 3 2 4..
## $ EDUCATIO : Factor w/ 6 levels "-2 - MISSING",..: 4 4 4 4 3 4 4 3 4..
## $ PLANGUAG : Factor w/ 5 levels "-2 - MISSING",..: 2 2 2 2 2 2 2 2 2..
## $ S2Q01    : Factor w/ 11 levels "-4 - PARTIAL INTERVIEW",..: 5 5 5 ..
## $ S2Q02R   : int  59 29 49 45 96 64 51 64 65 33 ...
## $ HGHT_FLG : int  0 0 0 0 0 0 0 0 0 0 ...
## $ S2Q03R   : int  100 20 55 60 98 115 64 135 115 26 ...
## $ WGHT_FLG : int  0 0 0 0 0 0 0 0 0 0 ...
## $ BMICLASS : Factor w/ 5 levels "-2 - MISSING",..: 3 1 3 5 1 3 3 3 3..
## $ S2Q04    : Factor w/ 8 levels "-4 - PARTIAL INTERVIEW",..: 5 5 5 5..
## $ S2Q05    : Factor w/ 8 levels "-4 - PARTIAL INTERVIEW",..: 4 4 4 4..
## $ S2Q06    : Factor w/ 8 levels "-4 - PARTIAL INTERVIEW",..: 4 4 4 4..
```