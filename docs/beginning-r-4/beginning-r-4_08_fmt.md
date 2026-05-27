# 3. 数据输入与输出

一般来说，在进行统计分析时，大部分时间都花在处理数据上。这包括成功输入数据以及组织或“清洗”数据。目前，我们的目标是识别各种类型的数据文件，并利用这些文件类型将数据导入和导出`R`。

到本章结束时，你应该能够：

*   通过后缀识别常见的文件类型。
*   应用 R 包和函数将数据*导入*`R`。
*   应用 R 包和函数将数据*导出*`R`。

## 3.1 环境设置

为了练习输入和输出你可能用到的各种文件类型，我们需要做一些环境设置。为了继续练习创建和使用项目，我们将为本章创建一个新项目。在该项目中，我们还将创建一些文件夹，并从本书的 `GitHub` 网站下载一些数据文件。

如有必要，请回顾第 1 章中创建新项目的步骤。启动 RStudio 后，在左上方的菜单栏中，选择 *File* 并点击 *New Project*。选择 *New Directory* ➤ *New Project*，在 *Directory name* 中输入 `03DataIO`，然后选择 *Create Project*。要创建一个 `R` 脚本文件，请在顶部工具栏中，*File* 字样正下方，点击那个带有加号的白纸小图标，然后选择 *R Script* 菜单选项。点击软盘形状的 *save* 图标，将此文件命名为 `DataIO.R`，然后点击 *Save*。

在右下方面板中，你应该能看到你项目中的两个文件。在 *Files* 选项卡正下方，点击标题为 *New Folder* 的按钮。在弹出的 *New Folder* 对话框中，输入 `data` 并选择 *OK*。在右下方面板中，点击你新建的 *data* 文件夹。重复创建文件夹的步骤，创建一个名为 `ch03` 的新文件夹。

接下来，你需要从本书的网站下载一些文件。这些文件应该存放在 *GitHub* 的 [`http://github.com/apress/beginning-r-4`](http://github.com/apress/beginning-r-4) 上。不过，你也可以在网站 [`http://mattwiley.org/publications.html`](http://mattwiley.org/publications.html) 上找到永久链接，方法是点击 *Beginning R* 对应的 *Code*。导航到本章的文件夹，将德克萨斯州各县数据 [3] 以及美国社区调查 [4] 数据下载到你刚刚创建的 `ch03` 文件夹中。总计，你的项目子文件夹中应该有六个文件。

完成这些设置后，你就可以开始练习导入和导出数据了。

如果在阅读本章过程中，你需要回顾关于在 RStudio 中如何查找内容的快速提示，可以查看这份简短的“速查表”，它包含了 RStudio 大部分功能的图示和标签：[`https://github.com/rstudio/cheatsheets/raw/master/rstudio-ide.pdf`](https://github.com/rstudio/cheatsheets/raw/master/rstudio-ide.pdf)。

## 3.2 数据输入

将文件输入到 `R` 时，数据通常以某种表格格式呈现，其中包含带名称的列（或称`变量`）和带编号的行（或称`观测值`）。因此，每一列代表一个类别，每一行代表对这些变量的一个特定测量值。

输入这类“大体规整”数据时，常见的挑战通常在于成功读取存储数据的文件的`类型`，并适应不同数据文件的独特性（例如，前几行的格式与预期不符）。这种小问题的例子可能是，数据收集者/创建者可能会在 Excel 文件的前几行中包含一些注释或说明，在这些注释之后，数据列和行才真正开始。

**注意**

当我们说文件的`类型`时，指的是诸如 Excel 电子表格、逗号分隔的文本文件或来自其他专有软件的数据集之类的东西。文件的`类型`几乎总是通过*扩展名*来指示。文件扩展名是文件名中句点之后的最后几个字符。例如，一个 Excel 文件可能被命名为 `data.xlsx`。在此例中，文件名是 `data`，扩展名是 `.xlsx`；正是这个 `.xlsx` 扩展名告诉我们这是一个 Excel 文件。其他常见的扩展名有 `.csv`，表示单元格由逗号分隔的文本文件；以及 `.rds`，这是 R 自身用于存储数据的格式。所有这些都可以通过查看文件扩展名来判断。

因此，我们的首要任务是确定用于读取数据的正确函数（及包），并判断是否需要跳过某些行。大多数用于输入数据的函数都至少有三个可选参数，包括数据文件的位置、要 `skip`（跳过）的行数，以及数据是否包含列名的 `header`（表头）。值得注意的是，大多数数据输入函数默认假定第一行是列名表头，并且不应跳过任何行。

大多数输入函数都包含许多选项——但通常大多数选项都不需要使用。实际上，处理文件中一些杂乱内容的最简单方法通常是手动打开文件，手动修复，然后再导入到 `R` 中。另一方面，如果你定期收到一个文件（例如来自某个公司或第三方的每日更新），并且该文件在格式上存在可预测的不规整性，那么花时间研究额外的函数选项以编写自动读取脚本可能是值得的。

### 手动输入

虽然我们通常将输入聚焦于读取某些外部文件，但在 `R` 中也可以手动输入数据。这在多种场景下都很有用，从设置一些变量到更大量的数据输入。在 `R` 中，*赋值*操作符是 `<-`。这两个符号——小于号和 `-`——都位于键盘的右侧，分别靠近 M 和 P 键。给我们的数据取一个有意义的名字会很有帮助；例如，如果使用年份 2020 作为变量，我们可能会称它为 `academicYear`。

**注意**

为了使代码便于口头表达，我们经常将数据变量命名为名词。出于同样的原因，函数通常被命名为动词（例如 `plot()`）。

要将以下代码行读出来，我们会说“变量 `academicYear` 被赋值为数字 `2020`”：

```
academicYear <- 2020
```

请注意，在运行了上一行代码后，在你的右上角 *Environment*（环境）面板中，现在出现了一个 `academicYear` 的值，显示为 `2020`。如果我们需要在代码中使用 academic year（学年），我们现在可以简单地输入 `academicYear` 来获取我们的结果：

```
academicYear
## [1] 2020
```

回顾一个熟悉的数据集 `mtcars`，让我们探索如何以另一种方式使用赋值。回想一下，`wt` 变量是汽车的重量值。如果我们想使用这些值，正如我们之前所见，我们可以通过将其赋值给一个变量名来专门调用该列。同样，在赋值之后，我们就可以直接使用我们的变量了：

```
mtcarsWeight <- mtcars$wt
mtcarsWeight
##  [1]  2.620 2.875 2.320 3.215 3.440 3.460 3.570 3.190 3.150 3.440 3.440
## [12]  4.070 3.730 3.780 5.250 5.424 5.345 2.200 1.615 1.835 2.465 3.520
## [23]  3.435 3.840 3.845 1.935 2.140 1.513 3.170 2.770 3.570 2.780
```

虽然手动赋值很简单，但将所有会用到的数据直接手动输入到 `R` 中将非常繁琐。接下来，我们将转向使用我们在第 2 章中安装的一些包。

### CSV：.csv

最常见的数据类型之一，是以*逗号分隔值*格式存储的信息表格，其文件扩展名为 `.csv`。虽然 `R` 本身可以读取此类文件，但 `data.table` 包提供了一个名为 `fread()` 的函数，它能非常快速地读取 CSV 文件。该函数有许多可能的输入或参数，不过我们现在只讨论其中三个。该函数的第一个输入或参数是 `file = ""`。由于 RStudio 项目默认将项目设为*工作目录*（我们在第 1 章中展示了如何设置），我们只需向此参数提供文件夹文件路径 `data/ch03/` 和文件名 `Counties_in_Texas.csv`。第二个参数 `header` 默认为自动特性，通常能很好地猜测是否存在表头。如果你知道文件有或没有表头，你可以手动将此参数设置为 `TRUE` 或 `FALSE`。我们讨论的第三个参数是 `skip`。它接受一个整数（即不带小数位的整数，如 0、1、2、3）输入，表示你希望跳过的行数。默认值为零；因此，与 `header` 一样，你通常可以安全地忽略此参数。你不能跳过行的一部分，因此像 `skip = 1.25` 或 `skip = 3.5` 这样的写法是无效的。

虽然包通常放在一组代码的开头，但在这里，我们将对 `data.table` 包的 `library()` 调用包含在内，以提醒我们，如果没有这个库调用，`fread()` 将无法工作。我们再次遇到了赋值运算符，因为我们将数据赋值给一个名为 `csvData` 的变量：

```
library(data.table)
## data.table 1.13.0 使用 6 个线程（参见 ?getDTthreads）。最新消息：r-datatable.com
csvData <- fread(file = "data/ch03/Counties_in_Texas.csv",
                 header = TRUE,
                 skip = 0)
```

注意，在运行完上面的代码行后，虽然*控制台*没有输出，但我们的*环境*窗格中确实有 254 个观测值和 10 个变量。你可以点击 `csvData` 左侧的箭头图标，查看刚刚读取的数据的类型。例如，`CountyName` 的类型是 `chr`，代表*字符*数据，而 `CountyNumber` 的类型是 `int`，代表*整数*或整数。请查看 [`https://github.com/rstudio/cheatsheets/raw/master/rstudio-ide.pdf`](https://github.com/rstudio/cheatsheets/raw/master/rstudio-ide.pdf) 以了解通过 RStudio（而非仅通过代码）与数据交互的更多方式。

由于有 254 个观测值或行，我们的数据可能有点长，无法直观检查所有行。一种较新的探索数据的方法可以在右上角的“环境”窗格中看到。每个数据集的左侧都有一个带箭头的点，右侧有一个方形表格网格。点击左侧带箭头的点，可以查看每个数据集中包含的数据类型。除了显示*列名*，它还会显示数据类型（例如，`int`、`Date` 或 `num`）。另一个选择是点击最右侧的方形表格网格。这将打开数据集并在新标签页中查看。关闭该标签页即可返回代码区域。

通常，仅查看前几行就足够了。`head()` 函数允许我们默认检查前六行：

```
head(csvData)
##    CountyName FIPSNumber CountyNumber PublicHealthRegion
## 1:   Anderson          1            1                  4
## 2:    Andrews          3            2                  9
## 3:   Angelina          5            3                  5
## 4:    Aransas          7            4                 11
## 5:     Archer          9            5                  2
## 6:  Armstrong         11            6                  1
##    HealthServiceRegion MetropolitanStatisticalArea_MSA
## 1:                4/5N                              --
## 2:                9/10                              --
## 3:                4/5N                              --
## 4:                  11                  Corpus Christi
## 5:                 2/3                   Wichita Falls
## 6:                   1                        Amarillo
##    MetropolitanDivisions MetroArea NCHSUrbanRuralClassification_2006
## 1:                    -- Non-Metro                      Micropolitan
## 2:                    -- Non-Metro                      Micropolitan
## 3:                    -- Non-Metro                      Micropolitan
## 4:                    --     Metro                      Medium Metro
## 5:                    --     Metro                       Small Metro
## 6:                    --     Metro                       Small Metro
##    NCHSUrbanRuralClassification_2013
## 1:                      Micropolitan
## 2:                      Micropolitan
## 3:                      Micropolitan
## 4:                      Medium Metro
## 5:                       Small Metro
## 6:                      Medium Metro
```

虽然现在看起来可能还不明显，但你其实已经在做统计了。探索性数据分析（EDA）是大多数分析的第一步。它可能并不花哨，但 `head()` 就是这种探索的一个例子。

### Excel：`.xlsx` 或 `.xls`

微软的 Excel 格式是另一种常见的数据格式。来自 `readxl` 包的 `read_excel()` 函数可以读取此类文件，并且同样具有一些有用的默认选项。除了通常的文件路径、列名和跳过这三个特性外，我们讨论第四个参数 `sheet`。Excel 文件通常有多个工作表。默认是第一个工作表，但你可以输入一个数字表示工作表的顺序，或者引用命名工作表的文本字符串（Excel 通常默认将第一个工作表命名为“Sheet1”）。

对于我们的数据集，变量 `excelData` 被赋值为在德克萨斯州县数据的 `.xlsx` 版本上使用 `read_excel()` 函数的输出。虽然我们设置了 `sheet = 1` 以按工作表顺序捕获数据，但我们也可以使用 `sheet = "Counties_in_Texas"`。为了在我们的代码中展示这一点，我们使用注释，即通过使用井号或标签符号字符来实现。代码中的注释供人类阅读，**不会**被 `R` 执行：

```r
library(readxl)
excelData <- read_excel(path = "data/ch03/Counties_in_Texas.xlsx",
sheet = 1, # 或 sheet = "Counties_in_Texas"
col_names = TRUE,
skip = 0
)
```

这与从 CSV 文件读取的数据集是同一个。真的是这样吗？对我们的新数据集使用 `head()` 函数，比较 `FIPSNumber` 列，注意它们看起来略有不同。更仔细地检查（例如通过环境窗格中的箭头下拉图标）显示，`csvData` 中的 `FIPSNumber` 是 `int` 类型，而 `excelData` 中是 `chr` 类型：

```r
head(excelData)
## # 一个 tibble：6 x 10
##   CountyName FIPSNumber CountyNumber PublicHealthReg˜ HealthServiceRe˜
##                                   
## 1 Anderson   001                   1                4 4/5N
## 2 Andrews    003                   2                9 9/10
## 3 Angelina   005                   3                5 4/5N
## 4 Aransas    007                   4               11 11
## 5 Archer     009                   5                2 2/3
## 6 Armstrong  011                   6                1 1
## # ... 还有 5 个变量：MetropolitanStatisticalArea_MSA ,
## #   MetropolitanDivisions , MetroArea ,
## #   NCHSUrbanRuralClassification_2006 ,
## #   NCHSUrbanRuralClassification_2013 
```

当使用由不同包作者编写的函数时，一些默认设置和一些选择往往并不相同。在将数据读入 `R` 时，通常明智的一步是检查你的数据，并更深入地了解刚刚读取的内容。你可能注意到的另一个区别是，我们的 Excel 数据提到了 tibble 这个词。我们将对数据结构选择的深入讨论留给我们编写的《高级 R：数据编程与云端》[23] 一书。虽然注意到这个区别是好的，但我们现在暂且忽略它。

### RDS: `.rds`

我们首先展示了如何从 CSV 或 Excel 文件中读取数据集，因为这些可能是存储和共享数据集最常用的方式。它们之所以流行，部分原因是几乎任何电脑上的任何人都可以打开并查看它们，而无需额外安装他们尚未拥有的软件。

`R` 也有自己的数据格式，名为 R Data “Storage”，其扩展名为 `.rds`。读取和写入 `.rds` 文件的功能内置于 `R` 中，因此我们不需要任何额外的包。此外，由于这些文件来自 `R`，因此只有一个感兴趣的参数，即 `file =` 参数。`R` 会自动知道如何读取它们、哪些内容指示变量、变量的类型、变量名与数据的关系、任何注释等信息：

```r
rData <- readRDS(file = "data/ch03/Counties_in_Texas.rds")
```

重复查看相同的数据会显得乏味。因此，我们引入了 `tail()` 函数，它显示数据集的后六行：

```r
tail(rData)
##    CountyName FIPSNumber CountyNumber PublicHealthRegion
## 1:       Wise        497          249                  3
## 2:       Wood        499          250                  4
## 3:     Yoakum        501          251                  1
## 4:      Young        503          252                  2
## 5:     Zapata        505          253                 11
## 6:     Zavala        507          254                  8
##    HealthServiceRegion MetropolitanStatisticalArea_MSA
## 1:                 2/3     Dallas-Fort Worth-Arlington
## 2:                4/5N                              --
## 3:                   1                              --
## 4:                 2/3                              --
## 5:                  11                              --
## 6:                   8                              --
##      MetropolitanDivisions MetroArea
## 1: Fort Worth-Arlington MD     Metro
## 2:                      -- Non-Metro
## 3:                      -- Non-Metro
## 4:                      -- Non-Metro
## 5:                      -- Non-Metro
## 6:                      -- Non-Metro
##    NCHSUrbanRuralClassification_2006
## 1:                Large Fringe Metro
## 2:                           Noncore
## 3:                           Noncore
## 4:                           Noncore
## 5:                           Noncore
## 6:                           Noncore
##    NCHSUrbanRuralClassification_2013
## 1:                Large Fringe Metro
## 2:                          Non-core
## 3:                          Non-core
## 4:                          Non-core
## 5:                      Micropolitan
## 6:                          Non-core
```

### 其他专有格式

还有其他几种常用的统计软件用于存储和分析数据。如果你的同事使用这些程序之一，你可能会收到一个使用该软件数据格式的数据文件。我们将简要介绍其中三种，以便你今后遇到此类数据时知道如何将其导入 `R`。

#### SPSS: `.sav`

我们讨论的最后三种格式都使用 `haven` 包。请记住，在每个 `R` 会话中，只需要运行一次 `library(haven)`。因此，我们仅在此处运行它。到目前为止，常见的参数已经熟悉。IBM 公司拥有名为 SPSS 的数据软件。该软件的文件扩展名可以是 `.sav`。来自 `haven` 的用于读取此格式的函数是 `read_spss()`：

```r
library(haven)
spssData <- read_spss(file = "data/ch03/Counties_in_Texas.sav",
skip = 0)
```

#### Stata: `.dta`

Stata 是一种统计数据软件，其文件扩展名为 `.dta`。`haven` 包也有一个 `read_stata()` 函数用于读取此类文件：

```r
stataData <- read_stata("data/ch03/Counties_in_Texas.dta",
skip = 0)
```

#### SAS: `.sas7bdat`

我们要介绍的最后一个统计软件数据格式是 SAS，其文件扩展名相对较长，为 `.sas7bdat`。为增加多样性，我们使用美国人口普查局美国社区调查 [23] 中的一个文件。这个特定文件有超过 3.8 万条观测值。如今的数据可能变得非常庞大。尽管如此，请注意 `R` 读取该文件的速度足够快。通常，只有在数据量变得非常大时，现代内存才会出现处理困难：

```r
sasData <- read_sas(file = "data/ch03/ACS_2019_5yr_B00001_0001E.sas7bdat")
head(sasData)
## 1 ACSSF B00001 0001     NA        7 "1 CE˜   NA UNWEIGH˜ "Unweighted˜
## 2 ACSSF B00001 0001     NA       NA ""       NA Univers˜ ""
## 3 ACSSF B00001 0001      1       NA ""       NA Total    ""
## 4 ACSSF B00002 0001     NA        8 "1 CE˜    2 UNWEIGH˜ "Unweighted˜
## 5 ACSSF B00002 0001     NA       NA ""       NA Univers˜ ""
## 6 ACSSF B00002 0001      1       NA ""       NA Total    ""
```

## 3.3 输出

将数据导入 `R` 通常是分析项目的第一阶段。将数据从 `R` 中写出以与利益相关者或协作者共享通常是最后阶段之一。在 `R` 项目中，将读入的数据与写出的数据分开通常是个好主意。我们已经创建了一个名为 `data` 的文件夹用于输入文件。现在，我们使用右下方面板中的相同 *New Folder* 图标创建一个名为 `output` 的新文件夹。

任何在 `R` 中的数据都可以被写出。数据来自哪里并不重要，重要的是它们存在于我们的全局环境中。因此，以下示例中用于写出的选项包括 `mtcars` [1]、`penguins` [11] 以及 `csvData`、`excelData`、`rData`、`sasData`、`spssData` 和 `stataData`。这些都属于我们当前 `R` 实例的一部分。由于 `mtcars` 和 `penguins` 数据集内置于包中，它们不会出现在环境选项卡中，但仍然可以被写出。换句话说，我们可以将 `sasData` 写入为 CSV 文件，一切正常。虽然这不是 `R` 的“官方”功能，但它可以用于在文件类型之间进行转换。为避免出现奇怪的数据调用，我们将使用 `R` 数据。

### CSV

为了实现通用可移植性，CSV 文件格式通常是最佳答案；大多数软件套件默认都能读取 CSV 文件。请记住，即使是 `R` 也有一个读取 CSV 文件的基础函数；我们选择使用 `data.table` 选项仅仅是我们自己的选择。类似地，大多数软件系统都能轻松保存、写入或导出 CSV 文件。同样，`R` 也有这个内置功能，尽管我们选择使用 `data.table` 的 `fwrite()` 函数。该函数接受两个参数。第一个参数设置为我们希望写出的数据。第二个参数是我们数据将要写入的文件路径和名称：

```r
fwrite(x = rData,
file = "output/ch3_txCounties.csv")
```

### Excel

微软的 Excel 格式是另一种广泛使用的格式，`writexl` 包提供了 `write_xlsx()` 函数来实现此功能。该函数的两个额外功能包括：可以选择隐藏列名（默认值为 `TRUE`，如上所示），以及可以选择不居中和加粗列名（同样，默认值为 `TRUE`，如上所示）。对于同事使用 Excel 的办公环境，这些默认设置通常是以非常人性化的方式向决策者共享数据。特别是默认加粗列名有助于减少对共享数据含义的混淆：

```r
library(writexl)
write_xlsx(x = rData,
path = "output/ch3_txCounties.xlsx",
col_names = TRUE,
format_headers = TRUE)
```

### RDS

我们最后一种导出的格式是 `R` 的默认格式。有时，对象只是为以后使用而保存。如果你打算稍后重新加载数据集以供后续工作使用，`saveRDS()` 函数是一个非常节省内存的选择。在我们的系统上，RDS 格式的大小不到 CSV 文件的一半：

```r
saveRDS(object = rData,
file = "output/ch3_txCounties.RDS")
```

## 3.4 总结

本章讨论了在 `R` 中读取和写入数据的方法。使用表 3-1 中总结的函数和包，你将能够访问和共享来自许多信息源的数据。

**表 3-1** 章节总结

| 概念 | 含义 |
| --- | --- |
| `<-` | 赋值运算符。 |
| `fread()` | 来自 `data.table` 包的 CSV 文件读取函数。 |
| `head()` | 显示数据集的前六行。 |
| `read_excel()` | 来自 `readxl` 包的 Excel XLSX 或 XLS 文件读取函数。 |
| `readRDS()` | RDS（`R` 原生格式）文件读取函数。 |
| `tail()` | 显示数据集的最后六行。 |
| `read_spss()` | 来自 `haven` 包的 SPSS SAV 文件读取函数。 |
| `read_stata()` | 来自 `haven` 包的 Stata DTA 文件读取函数。 |
| `read_sas()` | 来自 `haven` 包的 SAS SAS7BDAT 文件读取函数。 |
| `fwrite()` | 来自 `data.table` 包的 CSV 文件写入函数。 |
| `write_xlsx()` | 来自 `writexl` 包的 Excel XLSX 文件写入函数。 |
| `saveRDS()` | RDS（`R` 原生格式）文件写入函数。 |

## 3.5 通过实践掌握

通过完成一些练习来检查你的进度并在实践中成长。理解力检查提出批判性思考问题，这些问题最好以书面或口头形式回答。统计学的艺术之一在于成功地向利益相关者或受众传达结果。有时受众技术性很强，有时则非常不技术。练习则是对本章探讨的概念更直接的应用。

### 理解力检查

1.  四处问问！向你的同学、同事或朋友了解他们使用的数据类型。常见的文件格式有哪些？他们数据的列和行是如何组织的？

### 练习题

1.  使用本章学到的技巧，将 `penguins` 数据集写入一个 `.csv` 文件。

2.  使用本章学到的技巧，将 `mtcars` 数据集写入一个 `.xlsx` 文件。

3.  有许多数据集可供公众使用（尽管并非所有都可在书中免费使用）。从你的公司、地方政府或其他你感兴趣的团体中找一个数据集。下载该文件，并使用你在本章学到的一个函数将数据读取到 `R` 中。