# 14\. 常见 R 语言任务的操作指南

加载以下包以执行后续章节中的代码：

```
library(tidyverse)
library(readr)
library(datasets)
```

除非你之前已经安装过，否则以下这些包可能不在你的机器上。下面的 `if` 逻辑会检查你的 `R` 库，以确定该包是否已安装。如果没有，它会立即安装它们。这可以避免不必要的重装。此逻辑始终会将指定的包加载到内存中。本章中的一些示例需要额外的包，如下所示。所示的语法用于检查该包是否已安装到你的机器上。如果已安装，则简单地加载该包，就像你在 `library` 命令中所做的那样。如果该包未安装，则会安装它然后加载它。如果你经常使用 `R`，你最终会忘记一些不常用包是否已经安装。

```
if (!require("sjPlot")) install.packages("sjPlot")
if (!require("ggcharts")) install.packages("ggcharts")
if (!require("Hmisc")) install.packages("Hmisc")
if (!require("summarytools")) install.packages("summarytools")
if (!require("skimr")) install.packages("skimr")
```

## 14.1 输入-输出

### 14.1.1 控制台输入

```
my.city.name <- readline(prompt="What is your city name ")
#此时我在控制台中输入了"knoxville"
my.city.name
##[1] "knoxville"
```

### 14.1.2 读写 CSV 文件

设置你的工作目录，写出内置数据集 `mpg`，然后将其读回以演示 `read_csv` 函数。

```
setwd("c:\\temp") # 设置工作目录。你的设置会有所不同
```

使用 `mpg` 内置数据集：

```
write_csv(mpg, "my.mpg.csv")
y
## 1 audi         a4      1.8  1999     4 auto(l5)   f        18    29 p     compa~
## 2 audi         a4      1.8  1999     4 manual(m5) f        21    29 p     compa~
```

### 14.1.3 Windows：复制文件

如果 `R` 是一个人的话，我们会称她为博学家。她不仅能完成所有常规的分析和数据科学工作，还能处理任何编程语言所需的琐碎任务。这里，执行的是标准的 Windows 文件复制（针对 jpeg 文件）。首先，确定包含要复制文件的文件夹。接着，确定目标文件夹。然后，创建以“jpeg”结尾的文件名列表。从正则表达式章节中回忆一下，`$` 是一个结尾锚点。`TRUE` 表示复制成功。

**注意**

此脚本不适用于 Mac。此外，起始文件夹和结束文件夹的名称都需要在运行代码之前存在。

```
starting.folder <- "H:/t"
ending.folder <- "H:/t2"
files.to.be.backed.up <- list.files(starting.folder, ".jpg$")
file.copy(files.to.be.backed.up, ending.folder)
##[1] TRUE
```

