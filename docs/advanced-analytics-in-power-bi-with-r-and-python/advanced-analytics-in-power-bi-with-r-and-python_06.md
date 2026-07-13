# 2. 在 Power BI 中使用 ggplot2 创建 R 自定义可视化效果

在上一章中，您了解了`ggplot2`包，并获得了在 Power BI 中创建可视化效果的基本模板。在本章中，您将获得一些示例，这些示例展示了当您利用`ggplot2`在 Power BI 中创建 R 自定义可视化效果时，可以多么富有表现力。本章包含创建以下图表的示例：

* 一个带有动态标题和注释的标注图
* 一个带有动态标题、显示五个数据维度的气泡图
* 一个详细的预测可视化，预测 Lamar Jackson 和 Deshaun Watson 的维基百科页面浏览量
* 一个折线图，显示按年份划分的 GDP，并根据该时间点的执政政党添加背景阴影
* 一个地图可视化，显示所选州，并根据该县所在的人口五分位数对该州的县进行着色
* 一个四象限图，比较洛杉矶湖人队篮球运动员在 2008-2009 赛季的总得分和篮板数
* 一个美国女性平均体重与身高的散点图，并叠加了回归线

您可以对这些示例进行微小修改以用于您的数据。您也可以发挥创意，从每个可视化效果中汲取一点灵感，创建满足您特定需求的自定义可视化效果。`ggplot2`包非常丰富，以至于很难限制您的创造力。让我们从创建一个标注图开始。

## 标注图

水平条形图是一种非常常见的可视化效果。它非常适合根据指标对分类数据进行排名。Power BI 中的默认可视化效果之一是条形图，但其本质相当基础。您可以进行一些基本的增强，例如通过高亮来强调某个条形，但如果不使用笨拙的技巧，就无法进一步添加诸如动态副标题、说明文字和动态注释等功能。幸运的是，这对于`ggplot2`来说不是限制！

在本节中，您将创建图 2-1 中列出的水平条形图。

![../images/480677_1_En_2_Chapter/480677_1_En_2_Fig1_HTML.jpg](img/480677_1_En_2_Fig1_HTML.jpg)

图 2-1

带有标注条和注释的水平条形图

上图根据政府教育支出占总支出的百分比对前 7 个国家进行排名。它包含一个动态注释，将排名第一的国家与排名第七的国家进行比较。最后，它包含一个说明文字，告诉您数据来源。让我们来看看开发此可视化效果所需的代码。

### 步骤 1：获取必要数据

本示例使用的数据集来自世界银行开放数据。该数据集代表了世界各国政府教育支出占总支出的百分比。执行以下步骤获取数据集：

| 列名 | 数据类型 |
| --- | --- |
| 国家 | 文本 |
| 国家代码 | 文本 |
| 年份 | 整数 |
| 总支出百分比 | 小数 |

1. 访问`data.worldbank.org`。
2. 在页面中上部的搜索栏中输入“Government expenditure on education, total (% of government expenditure)”。按下回车键后，将出现一个可能的选项列表，其中应有一个与您输入的内容完全一致。选择它，您将进入一个专门介绍该主题的页面。
3. 在页面右侧，您会看到一个下载区域，提供 csv、xml 或 Excel 格式的下载选项。点击 csv 选项。
4. 上一步操作将下载一个 zip 文件到您的计算机。截至撰写本文时，该 zip 文件包含三个文件：一个数据文件、一个包含国家信息的元数据文件，以及一个包含指标信息的元数据文件。本练习所需文件是数据文件。在撰写本文时，该文件名为`API_SE.XPD.TOTL.GB.ZS_DS2_en_csv_v2_715566.csv`。请注意，生成这些数据集的政府机构可能会在未事先通知的情况下更改其格式，因此未来格式可能会发生变化。
5. 现在，您需要将数据集导入 Power BI，并将其整理成可视化所需的形状。Power BI 中的`GetData`功能将用于执行此任务。要将内容加载到 Power BI，请转到主页选项卡，然后点击`GetData` ➤ `Text/CSV`，接着导航到数据文件所在的位置。请记住，本示例使用的数据文件名为`API_SE.XPD.TOTL.GB.ZS_DS2_en_csv_v2_715566.csv`，它是 zip 文件中三个文件中最大的一个。该文件也位于本章的代码仓库中。
6. 接下来，您需要使用`GetData (Power Query)`对数据进行必要的转换，使其达到可视化所需的形状。最终输出需要包含以下列及其关联的数据类型：

您将在本章的代码仓库中获得一个`pbix`文件，其中包含将数据整理成上述形状所需的 Power Query 步骤。您只需更改源文件即可。请注意，如果生成数据集的政府实体决定更改格式，这些步骤将来可能需要更改。

### 步骤 2：在“筛选器”窗格中基于年份创建切片器

将切片器图标拖到报表画布的右上角。切片器图标如图 2-2 所示。

![../images/480677_1_En_2_Chapter/480677_1_En_2_Fig2_HTML.jpg](img/480677_1_En_2_Fig2_HTML.jpg)

图 2-2

切片器视觉对象图像

放置好切片器后，将`Year`字段从`ExpenditureOnEducationByCountryByYear`数据集拖到“字段”窗格，如图 2-3 所示。

![../images/480677_1_En_2_Chapter/480677_1_En_2_Fig3_HTML.jpg](img/480677_1_En_2_Fig3_HTML.jpg)

图 2-3

“字段”窗格图像

### 步骤 3：在 Power BI 中配置 R 视觉对象

转到可视化窗格，将一个 R 视觉对象拖到报表画布上。将视觉对象调整到所需大小。将`EducationExpenditures`表中的`Country`、`Total Expend %`和`Year`字段拖到“字段”窗格。如果“字段”窗格未显示，请在报表画布上选择 R 视觉对象，它应该会显示出来。

### 步骤 4：将数据导出到 R Studio 进行开发

选择 R 视觉对象以显示 R 脚本编辑器。您会在 R 视觉对象的底部看到它。如果它没有完全展开，请点击位于 R 脚本编辑器最右侧的向上箭头。展开后，您应该会看到以下代码（图 2-4）。

![../images/480677_1_En_2_Chapter/480677_1_En_2_Fig4_HTML.jpg](img/480677_1_En_2_Fig4_HTML.jpg)

图 2-4

带有默认注释的 Power BI R 脚本编辑器

这些注释告诉您，R 获取了您通过“字段”窗格在 Power BI 中创建的数据集，并将其分配给了名为`dataset`的 R 对象，该对象是一个 data frame。data frame 是一个具有特殊属性的二维数据集。data frame 在上述代码的第 3 行创建。Power BI 只允许将具有唯一行的数据集传递给 R，并通过第 4 行中的`unique()`函数确保`dataset`数据框具有唯一行。

上述编辑器并非理想的开发环境。幸运的是，Microsoft 让您可以轻松地在更理想的环境（如 R Studio）中进行开发。要将开发工作转移到 R Studio，请点击 45° 箭头。该箭头在图 2-5 中突出显示。

![../images/480677_1_En_2_Chapter/480677_1_En_2_Fig5_HTML.jpg](img/480677_1_En_2_Fig5_HTML.jpg)

图 2-5

点击突出显示的箭头将启动您首选的 IDE，以获得更愉悦的开发环境

点击箭头将打开 R Studio，并将传递给 R 视觉对象的数据集保存到计算机上的临时位置。它还会生成代码，创建代表传递给 Power BI 中 R 视觉对象的数据框的`dataset`变量。最后，脚本中的任何 R 代码也将传递给 R Studio。清单 1-1 显示了将传递给 R Studio 的内容。

```
# 输入加载。请勿更改 #
`dataset` =
read.csv('',
check.names = FALSE,
encoding = "UTF-8",
blank.lines.skip = FALSE
);
# 原始脚本。请在此处更新您的脚本内容
# 完成后将以下部分复制回原始
# 编辑窗口。以下用于创建数据框和
# 删除重复行的代码始终会执行，并作为
# 脚本的前言：
# dataset <-
#   data.frame(
#     Country Name,
#     `Percent Education Expenditure Ranking`
#   )
# dataset <- unique(dataset)
# 在此粘贴或输入您的脚本代码：
清单 1-1
从 Power BI 传递给 R Studio 的代码
```

代码已重新格式化，以便在页面上更好地显示。第 2-7 行将显示在一行上，用于将保存到磁盘的数据集读取到 R 数据框中。该数据框将被命名为`dataset`。请注意，这与 Power BI 中使用的名称相同。这是有意为之。使用相同的名称使您能够在 R Studio 中开发的代码直接用于 Power BI，而无需进行任何重构。

### 步骤 5：加载所需的包

本示例中使用的三个包是`tidyverse`、`scales`和`ggthemes`。`tidyverse`中的`dplyr`包用于执行一些数据整理操作，`tidyverse`中的`ggplot2`用于创建可视化，`tidyverse`中的`forcats`用于定义一些排序规则，`scales`包中的一些函数用于数字格式化，而`ggthemes`包提供了`theme_few()`主题。以下是加载这些包的代码：

```
library(tidyverse)
library(scales)
library(ggthemes)
```

### 第 6 步：创建数据验证测试所需的变量

以下代码用于创建数据验证测试：

```
currentColumns <- sort(colnames(dataset))
requiredColumns <- c("Country", "Total Expend %", "Year")
columnTest <- isTRUE(all.equal(currentColumns, requiredColumns))
reportYear <- unique(dataset$Year)
```

你需要确保传递给 R 的数据满足可视化的要求。前面执行了两个测试来检查是否满足要求。首先，测试确保预期的列已传递给 R。其次，测试确保数据集仅代表一个年份。

基于列名的测试在前三行代码中完成。第一行代码创建了一个名为`currentColumns`的字符向量，其中包含`dataset`数据框中按字母顺序排序的列名。它使用`colnames()`函数从`dataset`数据框中获取列名。`sort()`函数用于将返回的列名按字母顺序排序。为了进行比较，需要按字母顺序排列。下一行代码创建了一个名为`requiredColumns`的字符向量，其中包含可视化所期望的列名。列名也必须按字母顺序提供。

为了测试它们是否相等，它们不仅需要包含相同的元素，而且元素的顺序也必须相同。为了测试相等性，使用了基础 R 中的`all.equal()`函数。如果两个字符向量相等，则返回`TRUE`；否则，返回关于它们为何不相等的信息。你需要返回一个 Boolean 值。你可以通过将`all.equal()`函数包装在`isTRUE()`函数中来确保返回布尔值。前面代码中的最后一行获取了`dataset`数据框中包含的唯一年份。该信息将在下一步中使用。

### 第 7 步：创建数据验证测试

以下是验证测试的框架：

```
if (length(reportYear) == 1 & columnTest) {

} else {
plot.new()
title("The data supplied did not meet the requirements of the
chart.")
}
```

如果`dataset`数据框仅包含一个年份，并且`columnTest`评估为`TRUE`，则执行创建可视化的代码。否则，显示一个空白图表，并显示消息“提供的数据不满足图表的要求”。

### 第 8 步：为自定义 R 可视化所需的数据集添加额外的列

现在你需要开始开发在满足要求时创建可视化的脚本。你需要使用额外的列来增强`dataset`数据框，以便创建所需的可视化。用于增强`dataset`数据框的代码如下所示：

```
plotdata <-
dataset %>%
mutate(
rank = dense_rank(desc(`Total Expend %`)),
callout = ifelse(rank == 1, TRUE, FALSE),
Country = fct_reorder(Country, rank, .desc = TRUE)
) %>%
filter(rank <= 7)
```

此代码将放在第 7 步所示模板中的`<code to produce visual>`位置。前面的代码使用`tidyverse`中的`dplyr`包来添加所需的列。第一行代码将后续代码的结果分配给一个名为`plotdata`的变量。第二行代码表示你从`dataset`数据框开始，并通过使用管道（`%>%`）运算符将其作为`mutate()`函数的第一个参数传递。

管道运算符`%>%`来自`magrittr`包，该包随`dplyr`预加载。它将表达式的结果或值传递给后续函数。它使涉及链式操作的代码更具可读性。

`mutate()`函数用于投影两个新列`rank`和`callout`，并更改`Country`列的默认排序顺序。rank 列使用`dplyr`中的`dense_rank()`窗口函数，根据`Total Expend %`按降序对每一行进行排名。

`callout`列用于标识排名第一的国家。它使用`ifelse()`函数，如果 rank 列等于 1，则返回`TRUE`，否则返回`FALSE`。`Country`列使用`forcats`中的`fct_reorder()`函数，根据`rank`列确定其排序顺序。之所以能够这样做，是因为`Country`列的数据类型是 factor。

factor 是 R 中用于分类数据的一种特殊数据类型。它有许多很好的属性，其中之一就是能够根据另一列对因子列进行排序。

`Country`字段将根据其排名进行排序。定义的排序顺序将用于可视化。

最后，使用`filter()`函数过滤`dataset`数据框。对`rank`列应用过滤器，以将`dataset`数据框限制为仅包含排名前 7 的国家。

### 步骤 9：创建用于图表动态部分的变量

R 语言中有一个 Power BI 不具备的功能，即能够动态地向图表添加注释。你不仅可以动态创建要在图表上显示的文本，还可以动态控制文本放置的位置。在此步骤中，你将创建用于生成动态注释的变量，并定义动态注释的位置。你还需要创建用于图表主标题、副标题和图表说明的变量。以下是相关代码：

```r
countryToAnnotate <- plotdata$`Country`[plotdata$rank == 1]
minExpenditure <- min(plotdata$`Total Expend %`)
maxExpenditure <- max(plotdata$`Total Expend %`)
minCountry <-
plotdata$`Country`[
which(plotdata$`Total Expend %` == minExpenditure)
]
minCountry <- paste(minCountry, collapse = " & ")
maxCountry <- plotdata$`Country`[
which(plotdata$`Total Expend %` == maxExpenditure)
]
maxCountry <- paste(maxCountry, collapse = " & ")
mainTitle <- "% of Government Expenditure on Education"
subTitle <-
paste(
"The top 7 ranked countries in",
reportYear,
sep = " "
)
caption <- "Source:  https://databank.worldbank.org/source/world-development-indicators"
label_val <-
str_wrap(
paste(
maxCountry,
"spent",
percent((maxExpenditure/minExpenditure-1)),
"more of their expenditures on education than",
minCountry,
sep = " "
),
width = 35
)
```

以下是每个变量的说明：

- `countryToAnnotate`：这是排名第一的国家。它将是你将要注释的国家。
- `minExpenditure`：此变量保存使用 `min()` 函数获取 `Total Expend %` 列最小值的结果。此信息稍后将用于识别支出最低的国家。
- `maxExpenditure`：此变量保存使用 `max()` 函数获取 `Total Expend %` 列最大值的结果。此信息稍后将用于识别支出最高的国家。
- `minCountry`：你在 `minExpenditure` 变量中确定了前 7 个国家中的最低支出百分比。现在你需要找出哪些国家与之相关。你通过将 `plotdata` 数据框中的 `Country` 列子集化，仅包含 `Total Expend %` 等于 `minExpenditure` 的国家来获取该信息。你使用 `which()` 函数来协助完成此任务。`which()` 函数返回 `TRUE` 索引，可用于告知 R 要返回的 `Country` 列的索引。`plotdata` 数据框可能包含并列情况，需要处理。这将在下一行完成。并列的国家使用 `paste()` 函数合并为一个标量字符向量。新合并的标量字符向量中的元素用 `&` 符号分隔。如果你在 Power BI 报表中选择日历年份 2005，可以看到并列情况的示例。
- `maxCountry`：`which()` 函数也用于帮助我们获取最高支出国家的名称。这里它被用来获取 `rank` 列等于 1 的索引。在此数据集中，第一名没有并列情况，因此你实际上不需要处理它们。
- `mainTitle`：此变量保存一个静态字符串的值，该字符串将用于图表标题。
- `subtitle`：此变量保存一个动态创建字符串的值，该字符串将根据报表用户选择的年份用于图表的副标题。
- `caption`：此变量存储源数据来源的网站 URL。
- `label_val`：此变量包含动态创建的图表注释的结果。该注释将政府教育支出占比最高的国家与排名第七的国家进行了比较。它使用 `paste()` 函数，通过组合 `maxCountry` 和 `minCountry` 变量以及百分比计算结果来动态构建注释。

该注释相对较长，如果显示为一行，将无法在视觉对象中容纳。通过使用 `stringr` 包中的 `str_wrap()` 函数解决了这个问题。该函数提供了丰富的格式化选项，其中之一是控制行最大宽度的能力。在此示例中，你将此参数设置为 35，以确保注释能够适应图表。

### 步骤 10：通过定义 `ggplot()` 函数开始构建图表

`ggplot()` 函数定义如下：

```r
p <-
ggplot(
data = plotdata,
aes(x = `Country`, y = `Total Expend %`, fill = callout,
label = percent(`Total Expend %`)
)
)
```

`x` 和 `y` 美学映射的目的不言自明。`fill` 美学映射告诉 `ggplot()` 你想要基于哪一列（变量）来设置条形图的颜色，而 `label` 美学映射则告诉 R 将使用哪一列来向图表添加标签。

### 步骤 11：向 R 视觉对象添加柱状图层

在此步骤中，你使用 `geom_col()` 几何对象向图表添加第一个图层，即柱状图。你可以选择使用此几何对象或 `geom_bar()` 几何对象。你选择了 `geom_col()`，因为 `stat` 参数的默认值是 `stat_identity`，因此将显示 `y` 美学映射的真实值，而不是 `y` 中元素数量的计数。如果我们使用 `geom_bar()`，则必须明确告诉 `ggplot()` 你想要将 `stat` 参数设置为 `stat_identity`：

```r
p <-
ggplot(
data = plotdata,
aes(x = `Country`, y = `Total Expend %`, fill = callout,
label = percent(`Total Expend %`)
)
) +
geom_col()
```

### 步骤 12：向 R 视觉对象添加文本图层

以下代码获取了截至步骤 11 构建的内容，并为图表添加了一个标签图层：

```r
p <-
ggplot(
data = plotdata,
aes(x = `Country`, y = `Total Expend %`, fill = callout,
label = percent(`Total Expend %`)
)
) +
geom_bar(stat="identity", aes(fill = callout)) +
geom_text(nudge_y = -0.05)
```

在此步骤中，你向图表添加了另一个图层。你使用 `geom_text()` 几何对象向图表添加标签。`geom_text()` 几何对象必需的参数是 `x`、`y` 和 `label`。这三个参数都继承自在 `ggplot()` 函数中定义的美学映射。`x` 和 `y` 参数用于定义标签的位置，`label` 参数用于定义显示的内容。在此示例中，你显示的是格式化为百分比的 `Total Expend %` 列。标签的自然位置部分位于图表柱状图的末端之外。你需要将它们完全包含在柱状图中，这样标签就不会与稍后添加的注释重叠。你通过 `nudge_y` 参数将标签从部分位于柱状图外部移动到完全位于柱状图内部。图 2-6 显示了未使用 `nudge_y` 参数时标签的显示效果。

![../images/480677_1_En_2_Chapter/480677_1_En_2_Fig6_HTML.jpg](img/480677_1_En_2_Fig6_HTML.jpg)

图 2-6 带有数据标签的标注柱状图

图 2-7 显示了将 `nudge_y` 设置为 –0.05 时标签的外观。

![../images/480677_1_En_2_Chapter/480677_1_En_2_Fig7_HTML.jpg](img/480677_1_En_2_Fig7_HTML.jpg)

图 2-7 数据标签位于条形内部的标注柱状图

### 步骤 13：修改 y 轴

以下代码使用 `scale_y_continuous()` 标度对 `y` 轴进行修改：

```r
p <-
ggplot(
data = plotdata,
aes(x = `Country`, y = `Total Expend %`, fill = callout,
label = percent(`Total Expend %`)
)
) +
geom_bar(stat="identity", aes(fill = callout)) +
geom_text(nudge_y = -0.05) +
scale_y_continuous(
limits = c(0,0.75),
labels = NULL,
breaks = NULL
)
```

你明确将 `y` 轴的显示范围设置为 0 到 0.75。这样做是为了给报告用户提供一致的体验。默认情况下，与许多可视化工具一样，`ggplot2` 会根据数据自动调整标度。这可能会在视觉上错误地呈现数据在不同年份之间的重要差异。因此，你通过明确定义范围来防止重新缩放。`labels` 和 `breaks` 参数用于帮助报告用户解读图表中各列的值。由于你已经明确提供了这些值，因此不需要标签和刻度线。所以，通过将 `labels` 和 `breaks` 参数设置为 `NULL` 来移除它们。

### 步骤 14：将图表从垂直柱状图转换为水平条形图

此操作通过向脚本的下一行添加 `coord_flip()` 函数来完成，如下所示：

```r
p <-
ggplot(
data = plotdata,
aes(x = `Country`, y = `Total Expend %`, fill = callout,
label = percent(`Total Expend %`)
)
) +
geom_bar(stat="identity", aes(fill = callout)) +
geom_text(nudge_y = -0.05) +
scale_y_continuous(
limits = c(0,0.75),
labels = NULL,
breaks = NULL
) +
coord_flip()
```

`coord_flip()` 函数会使 `x` 轴变成 `y` 轴，`y` 轴变成 `x` 轴。结果是一个水平条形图，如图 2-8 所示。

![../images/480677_1_En_2_Chapter/480677_1_En_2_Fig8_HTML.jpg](img/480677_1_En_2_Fig8_HTML.jpg)

图 2-8 之前的柱状图转换为水平条形图

### 步骤 15：为 R 可视化添加动态注释

你使用 `annotate()` 函数为图表添加一个注释层，如下所示：

```r
p <-
ggplot(
data = plotdata,
aes(x = `Country`, y = `Total Expend %`, fill = callout,
label = percent(`Total Expend %`)
)
) +
geom_bar(stat="identity", aes(fill = callout)) +
geom_text(nudge_y = -0.05) +
scale_y_continuous(
limits = c(0,0.75),
labels = NULL,
breaks = NULL
) +
coord_flip() +
annotate(
"text",
label = label_val,
x = countryToAnnotate[1],
y = maxExpenditure + 0.12
)
```

你想向图表添加一个文本注释，因此第一个参数设置为 `"text"`。接下来，你将 `label_val` 变量赋值给 `label` 参数，因为该变量包含注释的文本。最后，你使用 `x` 和 `y` 参数定义注释的位置。你将 `x` 设置为你想要注释的国家名称。将 `x` 设置为国家名称，可以将注释对齐到该指定国家所在的 `x 轴` 位置。你将 `y` 轴上的位置设置为比该国家支出值高几个单位的位置，这样注释就不会覆盖该国家的柱形。请注意，因为你之前使用了 `coord_flip()` 函数，所以看起来你是在处理 `x 轴`，但实际上你是在处理 `y` 轴，反之亦然。添加注释层的结果如图 2-9 所示。

![../images/480677_1_En_2_Chapter/480677_1_En_2_Fig9_HTML.jpg](img/480677_1_En_2_Fig9_HTML.jpg)

图 2-9 带有注释的水平条形图

### 步骤 16：为 R 可视化添加动态标题和说明文字

接下来，你使用 `labs()` 函数以及 `mainTitle`、`subTitle` 和 `caption` 变量，为图表添加`图表标题`、`副标题`和`说明文字`，如下列代码所示：

```r
p <-
ggplot(
data = plotdata,
aes(x = `Country`, y = `Total Expend %`, fill = callout,
label = percent(`Total Expend %`)
)
) +
geom_bar(stat="identity", aes(fill = callout)) +
geom_text(nudge_y = -0.05) +
scale_y_continuous(
limits = c(0,0.75),
labels = NULL,
breaks = NULL
) +
coord_flip() +
annotate(
"text",
label = label_val,
x = countryToAnnotate[1],
y = maxExpenditure + 0.12
) +
labs(
title = mainTitle,
subtitle = subTitle,
caption = caption
)
```

设置 `labs` 函数的作用不言自明，因此无需解释。

### 步骤 17：移除 x 轴和 y 轴的标签

由于你已向图表添加了标签，因此无需显式定义 `x` 和 `y` 轴。你通过将 `xlab()` 和 `ylab()` 函数中的 `label` 参数设置为 `NULL` 来移除 `x` 和 `y` 轴的标签，如下列代码的最后一行所示：

```r
p <-
ggplot(
data = plotdata,
aes(x = `Country`, y = `Total Expend %`, fill = callout,
label = percent(`Total Expend %`)
)
) +
geom_bar(stat="identity", aes(fill = callout)) +
geom_text(nudge_y = -0.05) +
scale_y_continuous(
limits = c(0,0.75),
labels = NULL,
breaks = NULL
) +
coord_flip() +
annotate(
"text",
label = label_val,
x = countryToAnnotate[1],
y = maxExpenditure + 0.12
) +
labs(
title = mainTitle,
subtitle = subTitle,
caption = caption
) +
xlab(label = NULL) +
ylab(label = NULL)
```

### 步骤 18：移除图例

如果你回头参考图 2-10，你会注意到一个关于 `callout` 的图例。`callout` 列与 `fill` 参数相关联，该参数用于确定柱形的颜色。你用它来将排名第一的国家与其他国家区分开来。不需要图例来解释颜色，因此将其移除。你通过将 `guides()` 函数中的 `fill` 参数设置为 `FALSE` 来实现。这样做会从图表中移除图例，因为该图例是针对 `fill` 标度的。用于执行此任务的代码位于以下脚本的最后一行：

```r
p <-
ggplot(
data = plotdata,
aes(x = `Country`, y = `Total Expend %`, fill = callout,
label = percent(`Total Expend %`)
)
) +
geom_bar(stat="identity", aes(fill = callout)) +
geom_text(nudge_y = -0.05) +
scale_y_continuous(
limits = c(0,0.75),
labels = NULL,
breaks = NULL
) +
coord_flip() +
annotate(
"text",
label = label_val,
x = countryToAnnotate[1],
y = maxExpenditure + 0.12
) +
labs(
title = mainTitle,
subtitle = subTitle,
caption = caption
) +
xlab(label = NULL) +
ylab(label = NULL) +
guides(fill=FALSE)
```

### 步骤 19：使用 `theme_few()` 更改可视化的外观和风格

接下来，你通过使用 `ggthemes` 包中的 `theme_few()` 主题，应用数据可视化专家 Stephen Few 的原则来更改图表的外观，如下列代码的最后一行所示：

```r
p <-
ggplot(
data = plotdata,
aes(x = `Country`, y = `Total Expend %`, fill = callout,
label = percent(`Total Expend %`)
)
) +
geom_bar(stat="identity", aes(fill = callout)) +
geom_text(nudge_y = -0.05) +
scale_y_continuous(
limits = c(0,0.75),
labels = NULL,
breaks = NULL
) +
coord_flip() +
annotate(
"text",
label = label_val,
x = countryToAnnotate[1],
y = maxExpenditure + 0.12
) +
labs(
title = mainTitle,
subtitle = subTitle,
caption = caption
) +
xlab(label = NULL) +
ylab(label = NULL) +
guides(fill=FALSE) +
theme_few()
```

### 步骤 20：标题居中对齐

以下代码的最后一行使用了 `theme()` 函数来处理此任务：

```r
p <-
ggplot(
data = plotdata,
aes(x = `Country`, y = `Total Expend %`, fill = callout,
label = percent(`Total Expend %`)
)
) +
geom_bar(stat="identity", aes(fill = callout)) +
geom_text(nudge_y = -0.05) +
scale_y_continuous(
limits = c(0,0.75),
labels = NULL,
breaks = NULL
) +
coord_flip() +
annotate(
"text",
label = label_val,
x = countryToAnnotate[1],
y = maxExpenditure + 0.12
) +
labs(
title = mainTitle,
subtitle = subTitle,
caption = caption
) +
xlab(label = NULL) +
ylab(label = NULL) +
guides(fill=FALSE) +
theme_few() +
theme(
plot.title = element_text(hjust = 0.5),
plot.subtitle = element_text(hjust = 0.5)
)
```

`theme()` 函数用于修改图表主题的各个组件。图表主题代表图表中非数据部分的组件。你可以在控制台中输入 `?theme()` 来访问 `theme()` 的帮助文件，查看哪些组件可供修改以及与之关联的元素函数。`element_text()` 函数是与 `plot.title` 和 `plot.subtitle` 组件关联的元素函数。将 `element_text()` 的 `hjust` 参数设置为 0.5 即可使文本居中对齐。

### 步骤 21：将代码添加到 Power BI

完整的 R 脚本见代码清单 2-2。复制代码并将其粘贴到 Power BI 中与此视觉对象关联的 R 脚本编辑器中。如果你正确遵循了步骤，则无需重构。

```r
library(tidyverse)
library(scales)
library(ggthemes)
currentColumns <-
  c("Country", "Year", "Total Expend %", "callout")
if (all(currentColumns %in% names(dataset))) {
  plotdata <- dataset %>%
    mutate(
      rank = dense_rank(desc(`Total Expend %`)),
      `Country` = fct_reorder(`Country`, rank, .desc = TRUE),
      callout = ifelse(rank == 1, TRUE, FALSE)
    ) %>%
    filter(rank <= 7)
  countryToAnnotate <- plotdata$`Country`[plotdata$rank == 1]
  minExpenditure <- min(plotdata$`Total Expend %`)
  maxExpenditure <- max(plotdata$`Total Expend %`)
  minCountry <-
    plotdata$`Country`[
      which(plotdata$`Total Expend %` == minExpenditure)]
  minCountry <- paste(minCountry, collapse = " & ")
  maxCountry <-
    plotdata$`Country`[
      which(plotdata$`Total Expend %` == maxExpenditure)]
  maxCountry <- paste(maxCountry, collapse = " & ")
  mainTitle = "% of Government Expenditure on Education"
  subTitle =
    paste(
      "The top 7 ranked countries in",
      reportYear,
      sep = " ")
  caption =
    "Source: https://databank.worldbank.org/source/world-development-indicators"
  label_val <-
    str_wrap(
      paste(maxCountry, "spent",
            percent((maxExpenditure/minExpenditure-1)),
            "more than", minCountry, sep = " "),
      width = 25
    )
  p <-
    ggplot(
      data = plotdata,
      aes(x = `Country`, y = `Total Expend %`, fill = callout,
          label = percent(`Total Expend %`))
    ) +
    geom_bar(stat="identity", aes(fill = callout)) +
    geom_text(nudge_y = -0.05) +
    scale_y_continuous(
      limits = c(0,0.75), labels = NULL, breaks = NULL) +
    coord_flip() +
    annotate(
      "text",
      label = label_val,
      x = countryToAnnotate[1],
      y = maxExpenditure + 0.1) +
    labs(
      title = mainTitle,
      subtitle = subTitle,
      caption = caption
    ) +
    xlab(label = NULL) +
    ylab(label = NULL) +
    guides(fill=FALSE) +
    theme_few() +
    theme(
      plot.title = element_text(hjust = 0.5),
      plot.subtitle = element_text(hjust = 0.5))
  p
} else {
  plot.new()
  title("The data supplied did not meet the requirements of the chart.")
}
```

代码清单 2-2 标注图 R 脚本

## 气泡图

`气泡图`是另一种流行的图表类型。你可以将其视为散点图，但增加了一个属性，即使用大小来表示另一个数据维度。因此，使用传统的`气泡图`，你最多可以展示五个维度的数据：

*   每个气泡的 `x` 和 `y` 代表两个维度。
*   `fill`（气泡内部的颜色）代表一个维度。
*   `size` 代表一个维度。
*   `labels` 代表一个维度。

使用 `ggplot2`，你可以将事情提升到一个新的水平。你可以为气泡添加边框颜色，从而在你的`气泡图`中展示六个维度的数据！在本示例中，你将使用一个虚构的职业橄榄球联盟的数据，该数据在六个维度上绘制跑卫的信息：`name`、`height`、`weight`、`total rushing yards`、`conference` 和 `division`。你将在本练习中创建的图表如图 2-11 所示。让我们逐步了解创建该图表的步骤。

![../images/480677_1_En_2_Chapter/480677_1_En_2_Fig10_HTML.jpg](img/480677_1_En_2_Fig10_HTML.jpg)

图 2-10 一个展示六个数据维度的气泡图

### 步骤 1：获取必要的数据

本示例中使用的数据集是一个虚构的职业橄榄球联盟的模拟数据集。该联盟有两个联盟：`AL（美国联盟）`和`USL（美国联盟）`。每个联盟包含四个分区：`North`、`South`、`East` 和 `West`。该数据集包含该虚构联盟中跑卫的姓名、身高、体重、冲球码数、联盟和分区信息。包含该数据集的文件名是 `FakeFootballLeagueData.csv`，可以在本章的代码仓库中找到。

### 步骤 2：将数据加载到 Power BI

使用 `GetData` 将数据加载到 Power BI 数据模型中。下表显示了每列应具有的数据类型：

| 列名 | 列数据类型 |
| --- | --- |
| ID | 整数 |
| Year | 整数 |
| Key | 整数 |
| FN | 文本 |
| LN | 文本 |
| Weight | 整数 |
| Height | 整数 |
| Rushing Yards | 整数 |
| Division | 文本 |
| Conference | 文本 |

将数据转换为上述格式所需的转换步骤包含在 `PBI_FakeFootballLeague.pbix` 文件中。

### 步骤 3：基于年份创建筛选切片器

首先将 `Year` 字段拖到报表画布上，然后在`可视化`窗格中选择`切片器`视觉对象，从而基于年份字段创建一个报表切片器。确保你拖动的 `Year` 字段在选中`切片器`视觉对象之前处于高亮状态，以便将其转换为切片器。切片器视觉对象是图像中包含筛选器图标的那个。

### 步骤 4：进行初始 R 视觉对象配置

转到可视化窗格，将一个 R 视觉对象拖到报表画布上。将视觉对象调整到所需大小。将 `FakeFootballLeague` 表中的 `ID`、`Year`、`FN`、`LN`、`Weight`、`Height`、`Rushing Yards`、`Division` 和 `Conference` 字段拖到`字段`窗格中。如果`字段`窗格未显示，请选中 R 视觉对象，它应该会出现。

### 步骤 5：将数据导出到 R Studio 进行开发

单击 `R 脚本编辑器`标题栏上的 45° 箭头。此操作会将传递给 R 的数据框以及 R 起始代码导出到 `R Studio`。如果需要更详细的说明，请参考“标注图”部分中的步骤 4。

### 步骤 6：加载所需的包

以下代码用于加载所需的包：

```r
library(tidyverse)
library(ggrepel)
library(ggthemes)
```

本脚本中使用了 `tidyverse` 中的 `dplyr` 和 `ggplot2` 包。`dplyr` 包用于将 Power BI 传递给 R 的数据整理成所需的形状，而 `ggplot2` 用于创建视觉对象。`ggrepel` 包用于在视觉格式中重新定位数据点的标签，`ggthemes` 包用于更改视觉对象非数据组件的显示。

### 步骤 7：创建数据验证测试所需的变量

```r
currentColumns <- sort(colnames(dataset))
requiredColumns <- c("Conference", "Division", "FN", "Height",
"ID", "LN", "Rusing Yards", "Weight",
"Year")
columnTest <- isTRUE(all.equal(currentColumns, requiredColumns))
reportYear <- unique(dataset$Year)
```

`currentColumns` 变量用于保存从 Power BI 传递给 R 的数据框的列名。这些名称按字母顺序排序，以便进行比较。`requiredColumns` 变量用于保存所需列的名称。它们也按字母顺序列出，以便进行比较。`columnTest` 变量保存比较测试的结果。它使用 `all.equal()` 函数来比较 `currentColumns` 变量和 `requiredColumns` 变量是否相等。如果这两个变量相等，则返回 `TRUE`；否则，返回关于两个变量之间哪些部分不相等的信息。你需要一个布尔响应，因此将 `all.equal()` 函数包裹在 `isTRUE()` 函数中，当 `all.equal()` 返回 `TRUE` 时，`isTRUE()` 将返回 `TRUE`；当 `all.equal()` 返回非 `TRUE` 值时，`isTRUE()` 将返回 `FALSE`。接下来，使用 `unique()` 函数从 `Year` 列中获取唯一元素列表。

### 步骤 8：创建数据验证测试

用于执行验证的模板如下：

```r
if(length(reportYear) == 1 & columnTest) {

} else{
plot.new()
title("The data supplied did not meet the requirements of the
chart.")
}
```

`if` 语句执行两个测试。首先，它测试 `reportYear` 向量的长度是否等于 1。其次，它测试 `columnTest` 变量是否评估为 `TRUE`。如果这两个条件都满足，则执行创建可视化的代码。否则，执行创建空白图表的代码，并附带一条消息，告知用户为何未返回预期的可视化结果。

### 步骤 9：为会议和会议分区定义颜色

`ggplot2` 的一个有用特性是，`ggplot2` 可以轻松自定义图表中想要使用的颜色。你可以使用 `ggplot2` 识别的颜色名称来定义图表，或者如果你知道颜色的十六进制表示，也可以使用它。在这里，你使用后者，通过命名字符向量来定义颜色。你使用命名字符向量来定义分区和会议的颜色。命名字符向量中元素的名称代表分区或会议，而命名字符向量中元素的值是代表你想要使用的颜色的十六进制数。以下是用于定义分区和会议颜色的代码：

```r
divisionColors <- c("East"="#56B4E9", "West"="#33FAFF",
"North"="#F0E442", "South"="#8B8D8D")
conferenceColors <- c("USL"="#000000", "AL"="#FC4E07")
```

这是一个网站的 URL，你可以用它来获取各种颜色的十六进制数：[`www.hexcolortool.com/#010328`](http://www.hexcolortool.com/%2523010328)。当你想要在原生 Power BI 可视化中使用某种颜色并需要其十六进制值时，也可以使用此网站。

### 步骤 10：动态定义图表标题

以下是创建图表标题的代码：

```r
chartTitle <- paste("Runningback Quad Chart for",
reportYear,
sep = " "
)
```

该代码将字符串“Runningback Quad Chart for”与报告用户选择的报告年份连接起来，以动态创建图表标题。

### 步骤 11：创建图表的数据集

传递给 Power BI 的数据集尚未完全准备好用于图表。你想为图表中每个跑卫的数据点添加一个标签。如果同时使用他们的名和姓，标签会太长，因此你需要一种方法来缩短它们。一个好方法是取名的首字母，并将其与姓连接起来。这在以下代码中实现：

```r
chartData %>%
mutate(Name = paste0(str_sub(FN,1,1),". ", LN))
```

上述代码从 `dataset` 数据框开始，然后向其投影一个名为 `Name` 的新列。它基于使用 `paste0` 函数连接三个字符串。第一个字符串使用 `stringr` 包中的 `str_sub()` 函数从 `FN` 列中提取第一个字符。第二个字符串是 `". "`，第三个字符串是 `LN`。因此，如果 `FN` 是 John，`LN` 是 Doe，结果将是 `J. Doe`。

### 步骤 12：通过定义 ggplot 函数开始创建图表

你正在创建的气泡图将有六个维度。这六个维度是：
*   图表的 x 坐标，表示跑卫的体重
*   y 坐标，表示跑卫的身高
*   气泡的大小，表示跑卫的总冲球码数
*   图表的填充（内部颜色），表示跑卫所在的分区
*   颜色（气泡边框），表示跑卫所在的会议
*   标签，表示跑卫的名字

你可以使用 `ggplot()` 函数中的 `aes()` 函数来设置这些维度，如下所示：

```
p <- ggplot(
chartData,
aes(
x = Weight, y = Height, size = `Rusing Yards`,
color = Conference, fill = Division, stroke = 2,
label = Name
)
)
```

### 步骤 13：使用 geom_point 几何对象为气泡图添加图层

在 `geom_point()` 几何对象中，你可以使用 26 种形状。图 2-11 是一个展示它们的可视化图。

![../images/480677_1_En_2_Chapter/480677_1_En_2_Fig11_HTML.jpg](img/480677_1_En_2_Fig11_HTML.jpg)

**图 2-11** 展示了 `geom_point()` 中可用的形状。该可视化图是使用 `ggplot` 包创建的。

有三种形状可用于创建气泡图。它们是形状 `1`、`16` 和 `21`。只有形状 `21` 允许你同时控制点的颜色（圆的边框）和填充（内部阴影）。形状 `1` 仅提供颜色，形状 `16` 仅提供填充。以下是使用形状 `21` 将 `geom_point()` 图层添加到可视化中的代码：

```
p <- ggplot(
chartData,
aes(
x = Weight, y = Height, size = `Rusing Yards`,
color = Conference, fill = Division, stroke = 2,
label = Name
)
) +
geom_point(shape = 21)
```

结果如图 2-12 所示。

![../images/480677_1_En_2_Chapter/480677_1_En_2_Fig12_HTML.jpg](img/480677_1_En_2_Fig12_HTML.jpg)

**图 2-12** 使用形状 `21` 的初始气泡图。

你已经构建了气泡图的基础。请注意，`geom_point()` 几何对象继承了 `ggplot()` 函数的美学映射，因此你无需显式定义它们。现在，你需要进行一些外观上的调整，以使图表在视觉上更具吸引力。

### 步骤 14：为气泡图添加标签

图表上散布着气泡，但你不知道每个气泡代表哪位运动员。你可以使用 `ggrepel` 包中的 `geom_text_repel()` 几何对象为可视化图表添加文本。`geom_text_repel()` 几何对象在添加标签时会尽量避免重叠。以下是添加了 `geom_text_repel()` 层的代码：

```
p <- ggplot(
chartData,
aes(
x = Weight, y = Height, size = `Rusing Yards`,
color = Conference, fill = Division, stroke = 2,
label = Name
)
) +
geom_point(shape = 21) +
geom_text_repel(size = 5)
```

添加 `geom_text_repel()` 层的结果如图 2-13 所示。

![../images/480677_1_En_2_Chapter/480677_1_En_2_Fig13_HTML.jpg](img/480677_1_En_2_Fig13_HTML.jpg)

图 2-13 添加了标签的气泡图

请注意，我们能够通过 `size` 参数控制标签的文本大小。如果没有添加 `size` 参数，标签的字体大小会根据气泡的大小而变化。

### 步骤 15：更改气泡边框颜色和气泡填充颜色

接下来，你使用 `conferenceColors` 命名字符向量中的预定义颜色来更改气泡的颜色。具体做法是将 `conferenceColors` 设置为 `scale_color_manual()` 标度中 `values` 参数的值。你还需要使用 `divisionColors` 变量中的预定义颜色来设置 `fill`。具体做法是将 `divisionColors` 设置为 `scale_fill_manual()` 标度中 `values` 参数的值。包含这两个新标度的代码如下所示：

```
p <- ggplot(
chartData,
aes(
x = Weight, y = Height, size = `Rusing Yards`,
color = Conference, fill = Division, stroke = 2,
label = Name
)
) +
geom_point(shape = 21) +
geom_text_repel(size = 5) +
scale_color_manual(values = conferenceColors) +
scale_fill_manual(values = divisionColors)
```

生成的可视化图表如图 2-14 所示。

![../images/480677_1_En_2_Chapter/480677_1_En_2_Fig14_HTML.jpg](img/480677_1_En_2_Fig14_HTML.jpg)

图 2-14 使用自定义颜色的气泡图

### 步骤 16：创建 ggtitle

你将 `chartTitle` 变量传递给 `ggtitle()` 函数，为图表添加标题，如下面代码的最后一行所示：

```
p <- ggplot(
chartData,
aes(
x = Weight, y = Height, size = `Rusing Yards`,
color = Conference, fill = Division, stroke = 2,
label = Name
)
) +
geom_point(shape = 21) +
geom_text_repel(size = 5) +
scale_color_manual(values = conferenceColors) +
scale_fill_manual(values = divisionColors) +
ggtitle(chartTitle)
```

### 步骤 17：设置主题

在下面的代码中，你将可视化图表的主题设置为 `ggthemes` 包中的 `theme_few()` 主题：

```
p <- ggplot(
chartData,
aes(
x = Weight, y = Height, size = `Rusing Yards`,
color = Conference, fill = Division, stroke = 2,
label = Name
)
) +
geom_point(shape = 21) +
geom_text_repel(size = 5) +
scale_color_manual(values = conferenceColors) +
scale_fill_manual(values = divisionColors) +
ggtitle(chartTitle) +
theme_few()
```

### 步骤 18：将代码添加到 Power BI

现在你有了一个能生成所需可视化图表的可用脚本。完整的脚本如下。将清单 2-3 中的脚本添加到 Power BI 中与该可视化图表关联的 `R 脚本编辑器` 中。该可视化图表将对报表中使用的 `Year` 筛选器做出响应。

```
library(tidyverse)
library(ggrepel)
library(ggthemes)
currentColumns <- c("FN", "LN", "Weight", "Height", "Rusing Yards", "Conference", "Division", "Year")
if (all(currentColumns %in% colnames(dataset))) {
chartData <- dataset %>%
filter(Year == reportYear) %>%
mutate(Name = paste0(substring(FN,1,1),". ", LN))
divisionColors <-
c("East"="#56B4E9", "West"="#009E73", "North"="#F0E442",
"South"="#0072B2")
conferenceColors <- c("USL"="#000000", "AL"="#FC4E07")
chartTitle <-
paste("Runningback Quad Chart for", reportYear, sep = " ")
p <- ggplot(
chartData,
aes(
x = Weight, y = Height, size = `Rusing Yards`,
color = Conference, fill = Division, stroke = 2,
label = Name
)
) +
geom_point(shape = 21) +
geom_text_repel(size = 5) +
scale_color_manual(values = conferenceColors) +
scale_fill_manual(values = divisionColors) +
ggtitle(chartTitle) +
theme_few()
p
} else{
plot.new()
title("The data supplied did not meet the requirements of the
chart.")
}
```

清单 2-3 气泡图 R 脚本

### 预测

预测业务结果是一种被公司使用多年的技术。许多商业智能工具都提供了预测可视化功能，你可以将其添加到仪表板中显示预测结果，但这些功能提供的信息量非常有限。它们之所以有限，是因为通常只显示预测结果，而忽略了影响预测的重要组件。这些组件包括 `趋势` 和 `季节性`。预测的 `趋势` 用于显示数据在长期内是下降还是上升。预测的 `季节性` 用于显示由发生时间引起的数据变化。幸运的是，Facebook 的数据科学团队为 R 语言开发了一个名为 `Prophet` 的包，它不仅使你能够创建强大的预测可视化图表，还能让你创建可视化图表，从而获得关于构成预测的不同组件的宝贵信息。

在本练习中，你将构建一个受 `Prophet` 文档中示例启发的可视化图表。`Prophet` 中的示例创建了一个预测佩顿·曼宁维基百科页面浏览量的可视化图表。佩顿是前 NFL 四分卫。你将在 `Prophet` 示例的基础上进行扩展，构建一个交互式可视化图表，使你能够在两位现役 NFL 四分卫拉马尔·杰克逊和德肖恩·沃森之间切换。该可视化图表不仅包含预测图表，还包含显示预测趋势的图表，以及显示周度和年度季节性的图表。该可视化图表的图像如图 2-15 所示。

![../images/480677_1_En_2_Chapter/480677_1_En_2_Fig15_HTML.jpg](img/480677_1_En_2_Fig15_HTML.jpg)

图 2-15 使用 Prophet 和 ggplot2 生成的预测可视化图表

让我们来看看创建这个自定义 R 可视化图表所需的步骤！

### 步骤 1：获取必要数据

你需要获取包含拉马尔·杰克逊和德肖恩·沃森每日维基百科浏览量的数据。以下是获取数据所需执行的步骤。请注意，你需要将这些步骤执行两次，一次针对拉马尔·杰克逊，一次针对德肖恩·沃森：
1.  访问 [`https://en.wikipedia.org/wiki/Wikipedia:Web_statistics_tool`](https://en.wikipedia.org/wiki/Wikipedia:Web_statistics_tool) 以打开 *维基百科网络统计工具*。
2.  点击 *当前工具* 部分中的 [toollabs:​pageviews](https://tools.wmflabs.org/pageviews) 链接。
3.  点击搜索栏上方的 *X 清除* 以清空页面名称，然后输入你想要测试的页面名称。第一次迭代使用 *Lamar Jackson*，第二次迭代使用 *Deshaun Watson*。
4.  点击 *日期* 文本框，然后选择自定义范围。
5.  选择一个至少包含 2 年时间跨度的数据范围。此要求是为了让 *Prophet* 包有足够的信息来检测年度季节性趋势。在本示例中，我选择 2018 年 1 月 1 日作为开始日期，2019 年 12 月 31 日作为结束日期。
6.  点击 *页面* 文本框下方 *下载* 按钮中的向下箭头，然后选择 *CSV* 选项。该操作将启动下载过程，并将查询结果以 *csv* 格式保存到你的计算机上。默认文件名不会提供关于查询主题的信息。请将文件重命名为能代表文件内容的名称。我为每个文件添加了主题的姓名首字母作为前缀。拉马尔·杰克逊使用 *lj*，德肖恩·沃森使用 *dw*。
7.  接下来，你需要将拉马尔·杰克逊和德肖恩·沃森的数据集加载到 Power BI 中，进行可视化所需的一些数据转换，然后将它们合并成一个数据集。这将使用 Power Query 来完成。最终的数据集需要包含以下字段及其对应的数据类型：

| 列名 | 数据类型 |
| --- | --- |
| 日期 | 文本 |
| 页面浏览量 | 整数 |
| 四分卫 | 文本 |
| 页面浏览量（Log10） | 小数 |

使用 Power Query 进行转换以获取上述格式数据的具体步骤，可在本章代码仓库中的 *pbix* 文件中找到。

### 步骤 2：基于四分卫创建切片器

将 *四分卫* 字段拖到报表画布的右上方。接下来，转到右上角的 *可视化* 窗格，将可视化效果更改为 *切片器* 可视化对象。*切片器* 可视化对象是 Power BI 中预装的，其图标包含一个筛选器图片。

### 步骤 3：配置 R 可视化对象

转到可视化窗格，将一个 R 可视化对象拖到报表画布上。将可视化对象调整到所需大小。将 *WikipediaPageViews* 表中的 *日期*、*页面浏览量*、*页面浏览量（Log10）* 和 *四分卫* 字段拖放到 *字段* 窗格中。如果字段窗格未显示，请选中 *R 可视化对象*，它应该就会出现。

### 步骤 4：将数据导出到 R Studio 进行开发

点击 *R 脚本编辑器* 标题栏上的 45° 箭头。此操作会将传递给 *R 可视化对象* 的数据框以及 R 起始代码导出到 *R Studio*。如果需要更详细的解释，请参考“*标注图*”部分中的步骤 4。

### 步骤 5：将所需包加载到脚本中

以下是加载脚本所需包的代码：

```
library(tidyverse)
library(prophet)
library(ggthemes)
library(gridExtra)
library(lubridate)
```

来自 `tidyverse` 的 `dplyr` 包用于执行一些数据整理任务，来自 `tidyverse` 的 `ggplot2` 包用于创建可视化，`prophet` 包用于执行预测并帮助创建可视化，`gridExtra` 包用于在单个可视化对象中实现多图表显示，`lubridate` 包用于执行一些日期操作任务。

### 步骤 6：创建数据验证测试所需的变量

用于创建验证变量的代码如下所示：

```
currentColumns <- sort(colnames(dataset))
requiredColumns <- c("Date", "Page Views", "Page Views (Log10)",
"Quarterback")
columnTest <- isTRUE(all.equal(currentColumns, requiredColumns))
qb <- unique(dataset$Quarterback)
```

`currentColumns` 字符向量变量用于保存从 Power BI 传递给 R 的数据框的列名。这些名称按字母顺序排序以便比较。`requiredColumns` 字符向量变量用于保存所需列的名称。它们也按字母顺序列出以便比较。`columnTest` 变量保存比较测试的结果。它使用 `all.equal()` 函数比较 `currentColumns` 变量和 `requiredColumns` 变量是否相等。如果这两个变量相等，则返回 `TRUE`；否则，返回关于两个变量之间哪些内容不相等的信息。你需要一个布尔响应，因此将 `all.equal()` 函数包裹在 `isTRUE()` 函数中，当 `all.equal()` 返回 `TRUE` 时，`isTRUE()` 将返回 `TRUE`，当 `all.equal()` 返回非 `TRUE` 值时，`isTRUE()` 将返回 `FALSE`。最后要做的是获取 *四分卫* 列中的所有唯一元素，并将结果赋值给 `qb` 变量。

### 步骤 7：创建数据验证测试

用于执行验证测试的模板如下：

```
if (length(qb) == 1 & columnTest) {

} else {
plot.new()
title("提供的数据不符合图表的要求。")
}
```

该可视化对象需要一个仅包含一名四分卫且具有所需列的数据集。第一个测试检查 `qb` 变量的长度是否为 1。如果等于 1，则表示数据集中只有一名四分卫，因此该测试通过。第二个测试检查从 Power BI 传递的数据框是否包含所需的列。如果 `columnTest` 变量等于 `TRUE`，则该测试通过。如果两个测试结果都为 `TRUE`，则 R 将尝试执行创建可视化对象的代码。否则，将执行创建空白图表的代码，并附带一条消息，说明为何未创建预期的可视化对象。

### 步骤 8：创建动态图表标题

以下代码创建一个变量，用于保存基于四分卫姓名动态创建的图表标题：

```r
chartTitle <-
paste0(qb, " 的维基百科页面浏览量预测分析")
```

它将四分卫的姓名与字符串“ 的维基百科页面浏览量预测分析”连接起来，中间不使用任何分隔符。`paste0()` 函数与 `paste()` 函数类似。它用于在不需要分隔符的情况下连接字符串。

### 步骤 9：创建生成预测所需的数据集

```r
dfPageViews %>%
transmute(
Quarterback,
ds = ymd(Date),
y = `Page Views (Log10)`
)
```

`prophet` 包只需要两个变量：`ds` 和 `y`。`ds` 变量用于日期。日期应该是连续的，并且至少跨越 2 年的时间段。`y` 变量代表你想要预测的结果，即 `页面浏览量` 的对数。

请注意，你使用的是页面浏览量的对数，而不是实际的页面浏览量。这是一种常见的技术，适用于像本示例中数据分散度较高的情况。使用页面浏览量的对数可以减少分散度，使数据更易于可视化。在构建数据科学模型时，使用对数变换也是特征工程中常用的一种技术。

### 步骤 10：生成预测

以下三行代码用于生成预测：

```r
m <- prophet(dfPageViews)
future <- make_future_dataframe(
m,
periods = 365,
freq = "day",
include_history = TRUE
)
forecast <- predict(m, future)
```

第一行基于 `dfPageViews` 数据框生成一个预测模型对象。第二行代码创建一个数据框，该数据框在 `dfPageViews` 数据框的日期末尾添加了 `periods` 参数中指定的周期数。周期的频率由 `freq` 参数定义，如果将 `include_history` 参数设置为 `TRUE`，则历史日期也会包含在数据框中。第三行通过 `predict()` 函数，使用第一行创建的模型和 `future` 数据框生成预测。

### 步骤 11：生成图表

在此步骤中，您将生成四个图表。这四个图表分别是：

- 预测图
- 显示基础数据趋势的图
- 显示周季节性的图
- 显示年季节性的图

用于创建这些图表的代码如下所示：

```r
p1 <- plot(m, forecast) + ggtitle(chartTitle) + theme_few()
p <- prophet_plot_components(m, forecast)
p2 <- p[[1]] + theme_few()
p3 <- p[[2]] + theme_few()
p4 <- p[[3]] + theme_few()
p5 <- grid.arrange(p1, p2, p3, p4, nrow =4)
p5
```

生成的视觉效果已在图 2-15 中展示。图 2-15 中的视觉效果非常有价值，因为您不仅拥有预测的视觉效果，还拥有影响预测的各个组件的视觉效果。您可以通过 `trend` 图查看数据的趋势，通过 `weekly` 图查看数据在一周内的典型变化，以及通过 `yearly` 图查看数据在一年内的典型变化。

您需要做的第一件事是分别定义每个图表。以下是每个图表如何单独定义的说明：

- `p1` 用于保存实际预测的视觉效果。它是使用 `Prophet` 包中的 `plot()` 函数构建的，该函数底层基于 `ggplot` 构建。因此，您可以对其应用 `theme_few()` 主题。
- `p` 用于保存由 `Prophet` 通过 `prophet_plot_components()` 函数生成的组件图表。此函数返回一个 `ggplot` 图表列表，代表预测的不同组件。在本例中，它是一个趋势图、一个周季节性图和一个年季节性图。
- `p2`、`p3` 和 `p4` 保存由 `p` 生成的三个单独的组件图表。如前所述，`p` 是一个 `ggplot` 图表列表。您需要将每个图表作为 `ggplot` 图表从 `p` 中提取出来。这可以通过使用双方括号 `[[` 来实现。当您在 R 中使用单方括号 `[` 从列表中提取对象时，返回的项目是一个包含一个元素的列表，而不是底层元素实际数据类型的单个对象。因此，在此示例中，`p[1]` 不会返回一个 `ggplot` 图表，而是返回一个包含一个项目的列表，该项目是一个 `ggplot` 图表。如果您想显式地以其真实数据类型返回该项目，则需要像前面代码中那样使用双方括号 `[[` 进行提取。请注意，由于每个图表都是作为 `ggplot` 图表提取的，因此您可以对它们每个应用 `theme_few()` 主题。

现在，您已将预测视觉效果保存为 `ggplot` 对象，并将每个预测组件的视觉效果保存为 `ggplot` 对象，您可以使用 `gridExtra` 包中的 `grid.arrange()` 函数将它们组合成一个视觉效果。这在前面的代码的第 6 行中完成。您希望图表彼此堆叠。通过将 `nrow` 参数设置为 4 来完成此任务。您可以使用此参数控制视觉效果在屏幕上的显示方式。例如，如果将该参数设置为 2，则会将图表组合成两行，每行两个图表。

### 步骤 12：将代码添加到 Power BI

用于创建预测视觉效果的完整脚本位于清单 [2-4 中。将其复制并粘贴到 Power BI 的 `R 脚本编辑器` 中。该视觉效果将对 Quarterback 切片器完全响应。

```r
library(tidyverse)
library(prophet)
library(ggthemes)
library(gridExtra)
library(lubridate)
library(rlang)
# 我需要加载 rlang。请查看此包。
currentColumns <- dfPageViews %>%
  mutate(Date = ymd(Date)) %>%
  rename(ds = Date, y = `Page Views (Log10)`)
m <- prophet(dfPageViews, yearly.seasonality=TRUE)
future <- make_future_dataframe(m, periods = 365)
forecast <- predict(m, future)
p1 <- plot(m, forecast) + ggtitle(chartTitle) + theme_few()
p <- prophet_plot_components(m, forecast)
p2 <- p[[1]] + theme_few()
p3 <- p[[2]] + theme_few()
p4 <- p[[3]] + theme_few()
p5 <- grid.arrange(p1, p2, p3, p4, nrow =4)
p5
} else {
plot.new()
title("提供的数据不符合图表要求。")
}
```

清单 2-4 生成预测图表的 R 脚本

## 带阴影的折线图

折线图可以说是数据可视化中最流行的图表之一。它非常适合绘制跨越一段时间的数据。由于其使用广泛，所有 BI 工具都原生支持它。

在本练习中，您将学习如何使用 R 在 Power BI 中创建折线图。此折线图将不是您典型的折线图。它将包含截至撰写本文时 Power BI 中尚不可用的增强功能。您将创建的 R 自定义视觉效果灵感来自 Hadley Wickham 在他的书 *Ggplot2: Elegant Graphics for Data Analysis*（第 2 版，第 44 页）中创建的视觉效果。他在书中创建的视觉效果使用背景阴影技术来识别图表中特定时间点在职的总统。您将在 Power BI 中创建一个交互式 R 自定义视觉效果，该视觉效果将执行类似的操作，但有所增强。您将创建的视觉效果如图 2-16 所示。

![../images/480677_1_En_2_Chapter/480677_1_En_2_Fig16_HTML.jpg](img/480677_1_En_2_Fig16_HTML.jpg)

图 2-16 折线图显示 GDP 百分比变化，背景阴影基于阴影时间段内执政的政党

R 自定义视觉效果绘制了从 1947 日历年到 2018 日历年的 `GDP 百分比变化`。此图表的独特之处在于，背景阴影显示了给定时间点总统所属的政党。您利用 Power BI 的交互性进一步增强视觉效果。除了总统所属政党的视图外，您还让最终用户能够更改背景，以显示在给定时间点代表美国参议院多数席位的政党、代表美国众议院多数席位的政党以及总体多数党。除此之外，您还将让最终用户能够在显示 `实际 GDP` 和 `GDP 百分比变化` 之间切换。下图 2-17 显示了实际 GDP 的总体多数党视图。

![../images/480677_1_En_2_Chapter/480677_1_En_2_Fig17_HTML.jpg](img/480677_1_En_2_Fig17_HTML.jpg)

图 2-17 折线图显示实际 GDP，背景阴影基于阴影时间段内总体执政的政党

现在您已经看到了 R 自定义视觉效果，让我们来看看在 Power BI 中创建该视觉效果必须采取的步骤！

### 步骤 1：获取必要数据

1. 访问 [`https://fred.stlouisfed.org/series/GDP`](https://fred.stlouisfed.org/series/GDP)，该链接将跳转至圣路易斯联储官网中专门展示历史 GDP 信息的页面。点击 `EDIT GRAPH` 按钮，将 `Modify frequency` 组合框中的值更改为 `Annual`，并将 `Aggregation method` 更改为 `End of Period`。点击弹出表单右上角的 `X` 关闭表单。
2. 点击 `DOWNLOAD` 按钮，选择 `CSV` 格式。在撰写本文时，此操作会将一个名为 `GDP.csv` 的 `csv` 文件下载到我的电脑上。
3. 进入本示例的代码仓库，找到 `PoliticalInfoWithGDP.xlsx` 工作簿。该工作簿包含用于创建本示例中 `PoliticalInfoWithGDP` 数据集的数据集和 Power Query 查询。`PoliticalInfoWithGDP` 是本练习中将要生成的视觉对象的数据源。大部分工作已为您完成，但您可以检查工作簿，了解如何将以下数据集合并以生成 `PoliticalInfoWithGDP` 数据集。以下是在 `PoliticalInfoWithGDP.xlsx` 工作簿中使用 Power Query 开发的每个查询的描述：
    - `GDP`：此查询导入步骤 2 中下载的 GDP 数据，并在数据集中添加一个 `% GDP Change` 列。
    - `Presidents`：此查询构建了一个数据集，列出了分析期间内的所有美国总统，并附带一列显示其政治派别。
    - `Political Info`：此查询从以下 URL 的维基百科文章中获取信息：[`https://en.wikipedia.org/wiki/Party_divisions_of_United_States_Congresses`](https://en.wikipedia.org/wiki/Party_divisions_of_United_States_Congresses)。该 URL 的信息用于获取 `Senate` 和 `House of Representative` 的多数党以及当时的在任总统。使用 `Power Query` 重塑数据以用于可视化。您可以按照 `Power Query` 中的步骤查看具体操作。
        > `Power Query` 是一种内置于多种 Microsoft 技术（如 Microsoft Excel、Power BI、Power BI Dataflows 和 SQL Server Analysis Services）中的数据整理工具。它拥有图形用户界面，能大幅缩短执行简单数据整理所需的时间。我强烈推荐不熟悉 Microsoft 生态系统的 R 和 Python 开发者了解一下这个出色的工具！
    - `PoliticalInfoWithGDP`：合并上述数据集，并通过 `Power Query` 将结果整理成本示例可视化所需的形状。
4. 如前所述，`PoliticalInfoWithGDP` 数据集是用于可视化的数据集，并已通过上述 Power Query 构建完成。它已保存为名为 `PoliticalInfoWithGDP.csv` 的 `csv` 文件。您可以在本示例的代码仓库中找到该文件。

### 步骤 2：将数据加载到 Power BI

通过选择 `Home` ➤ `Get Data` ➤ `Text/CSV`，将 `PoliticalInfoWithGDP.csv` 加载到 Power BI，然后浏览到您保存文件的位置。点击 `Transform Data` 按钮进入 Power Query 编辑器。检查并确保字段具有以下数据类型：

| 列 | 数据类型 |
| --- | --- |
| Index | 整数 |
| Year | 整数 |
| GDP | 小数 |
| % GDP Change | 小数 |
| Senate Majority | 文本 |
| House Majority | 文本 |
| President's Majority | 文本 |
| Overall Majority | 文本 |

如果列不具备上述数据类型，请将其更改为相应的数据类型。更改数据类型最简单的方法是右键单击相关列，然后选择 `Change Type`。将出现可用数据类型列表。选择适用于该列的数据类型。确认数据类型正确后，点击 `Close & Apply` 将数据加载到 Power BI 数据模型中。

转到表格，确保 `Year` 和 `Index` 的默认行为设置为 `Do Not Summarize`。为此，请在 `Fields` 窗格中选择该字段，然后转到 `Modeling` ➤ `Default Summarization`（位于 `Properties` 部分），并选择 `Don't Summarize`。

### 步骤 3：创建报表切片器

报表有两个切片器。一个切片器用于让最终用户选择政治视图（`House`、`Senate`、`President` 或 `Overall`），另一个切片器用于选择用户想要显示的 GDP 统计指标（`Actual GDP` 或 `GDP % Change`）。此步骤的工作已在您可以在本示例代码仓库中找到的入门 `pbix` 模板中为您完成。

### 步骤 4：配置 R 视觉对象

转到 `Visualization pane`，将 `R visual` 拖到报表画布上。R 视觉对象图标位于 `Visualization` 窗格的右下部分。将视觉对象调整到所需大小。将 `Year`、`GetPoliticalLevel`、`GetPoliticalLevelName`、`GetGDPStat` 和 `GetGDPStatName` 添加到位于 `Visualization` 窗格下方的 `Fields` 窗格中。有时 Microsoft Power BI 会默认对数字字段应用聚合函数。检查它是否尝试对 `Year` 字段执行此操作。如果应用了聚合函数，请点击 `Fields` 窗格中该字段的下拉箭头，然后选择 `Don't Summarize` 将其移除。

### 步骤 5：将数据导出到 R Studio 进行开发

点击 `R script editor` 标题栏上的 45° 箭头。此操作会将传递给 R 的数据框和 R 入门代码导出到 `R Studio`。如果您需要更详细的说明，请参阅“标注图”部分中的步骤 4。

### 步骤 6：将所需包加载到脚本中

加载所需包所需的代码如下所示：

```r
library(tidyverse)
library(ggthemes)
library(scales)
```

来自 `tidyverse` 的 `dplyr` 包将用于执行一些数据整理任务，来自 `tidyverse` 的 `ggplot2` 包将用于创建可视化，来自 `ggthemes` 包的 `economist` 主题用于格式化图表的非数据部分，而 `scales` 包中的一些函数将用于数字格式化。

### 第 7 步：创建数据验证测试所需的变量

以下是验证测试的代码：

```
currentColumns <- sort(colnames(dataset))
requiredColumns <-
c("GetGDPStat", "GetGDPStatName", "GetPoliticalLevel",
"GetPoliticalLevelName", "Year")
columnTest <- isTRUE(all.equal(currentColumns, requiredColumns))
politicalLevelName <- unique(dataset$GetPoliticalLevelName)
gdpStatName <- unique(dataset$GetGDPStatName)
```

将执行以下三项测试：
-   一项测试，确保传递给可视化的数据集包含必要的字段
-   一项测试，确保数据集中仅包含一种政治观点
-   一项测试，确保数据集中仅包含一项 GDP 统计数据

我们来详细讲解第一个测试中使用的代码。代码如下：

```
currentColumns <- sort(colnames(dataset))
requiredColumns <-
c("GetGDPStat", "GetGDPStatName", "GetPoliticalLevel",
"GetPoliticalLevelName", "Year")
columnTest <- isTRUE(all.equal(currentColumns, requiredColumns))
```

第一行代码从传递给 R 的数据集中获取列名，并将其赋值给一个名为 `currentColumns` 的字符向量。`sort()` 函数用于按字母顺序对列名进行排序。下一行代码创建了一个包含所需字段的字符向量，并将其命名为 `requiredColumns`。请注意，这些名称是按字母顺序提供的。这样做是为了确保可以直接与 `currentColumns` 字符向量进行比较。第三行代码通过使用 `all.equal()` 函数比较 `currentColumns` 字符向量和 `requiredColumns` 字符向量来执行测试。当 `currentColumns` 等于 `requiredColumns` 时，返回 `TRUE`。否则，会返回试图解释它们为何不匹配的信息。你需要一个布尔响应，因此使用 `isTRUE()` 函数包裹 `all.equal()`。这样做后，当 `all.equal()` 返回 `TRUE` 时，将返回 `TRUE`；否则，将返回 `FALSE`。

接下来，使用以下代码创建两个字符向量，分别用于存储 `GetPoliticalLevelName` 字段和 `GetGDPStatName` 字段中的唯一元素：

```
politicalLevelName <- unique(dataset$GetPoliticalLevelName)
gdpStatName <- unique(dataset$GetGDPStatName)
```

理想情况下，两者都只包含一个元素，这将在下一步中进行测试。

### 第 8 步：创建数据验证测试

验证所使用的模板如下：

```
if(length(politicalLevelName) == 1 &
length(gdpStatName) == 1 & columnTest) {

} else {
plot.new()
title("提供的数据不符合图表要求。")
}
```

如果 `GetPoliticalLevelName` 字段中只有一个值，`GetGDPStatName` 字段中只有一个值，并且 `columnTest` 评估为 `TRUE`，则前面的 `if` 语句将执行构建可视化所需的代码。

### 第 9 步：基于 `dataset` 数据框创建一个新的数据框

最佳实践是不修改从 Power BI 传递给 R 的 `dataset` 数据框。为了避免修改该数据框，你需要基于 `dataset` 数据框创建一个新的数据框，然后修改这个新数据框。以下是用于创建新数据框的代码：

```
dfPI <- dataset
```

### 第 10 步：创建将用于图表动态部分的变量

以下是定义这些变量所需的代码：

```
politicalLevelName <- unique(dfPI$GetPoliticalLevelName)
gdpStatName <- unique(dfPI$GetGDPStatName)
yAxisName <-
paste(
gdpStatName,
ifelse(gdpStatName == "Actual GDP","(单位：十亿)",""),
sep = " "
)
chartTitle <- paste(gdpStatName, "分析", sep = " ")
chartSubtitle <- paste(politicalLevelName, "视图", sep = " ")
```

前面的代码动态生成了 `y` 轴的标签、图表标题和图表副标题。前两行获取 `政治级别` 和 `GDP 统计数据`，并将信息存储起来供后续使用。下一行代码（由于格式原因，在前面的代码片段中实际为六行）用于创建 `y` 轴的标签。它使用 `paste()` 函数将 `gdpStatName` 变量的值与字符串 `(单位：十亿)` 组合起来（如果所使用的 GDP 统计数据是 `实际 GDP`）。结果赋值给 `yAxisName` 变量。第四行代码通过使用空格分隔符连接 `gdpStatName` 变量的值和单词“分析”，动态构建主图表标题。最后一行代码通过使用空格分隔符连接 `politicalLevelName` 变量的值和单词“视图”，动态创建图表副标题。

### 步骤 11：创建背景阴影所需的数据集

在此步骤中，你将使用 `dfPI` 数据框中的数据，并将其转换为可视化背景阴影部分所需的数据集。首先，让我们快速查看一下图 2-18 中的数据框。

![../images/480677_1_En_2_Chapter/480677_1_En_2_Fig18_HTML.jpg](img/480677_1_En_2_Fig18_HTML.jpg)

图 2-18 `dfPI` 数据框预览

你需要使用上述数据创建一个数据集，该数据集包含每个连续政党执政期的开始时间和结束时间。你需要获取上述数据框，并编写代码将其转换为图 2-19 中的数据框格式。

![../images/480677_1_En_2_Chapter/480677_1_En_2_Fig19_HTML.jpg](img/480677_1_En_2_Fig19_HTML.jpg)

图 2-19 所需的数据框格式

为了实现这种转换，你需要能够唯一标识每个连续的政党执政期。目前，每个政党执政期没有唯一的标识符，因此你需要添加一个。一旦添加完成，你就可以使用传统的聚合方法来获取每个执政期的开始和结束年份。让我们先来了解一下添加唯一标识符（你将其命名为 `group_id`）所需的步骤。生成 `group_id` 信息的代码如下所示：

```
dfPI$GetPoliticalLevel <- as.character(dfPI$GetPoliticalLevel)
runs <- rle(dfPI$GetPoliticalLevel)
group_id <- rep(seq_along(runs$lengths), runs$lengths)
```

第 1 行首先进行的是数据类型转换。在 R 语言中，对于 R 4.0 之前的版本，包含字符串的数据框列的默认数据类型是 `factor`（因子）。

在撰写本书时，Power BI 服务尚未更新到 R 4.0 版本。如果你在练习时仍处于这种情况，那么你也需要进行转换。如果 Power BI 服务已更新到 R 4.0 或更高版本，则无需执行转换步骤。

由于 `GetPoliticalLevel` 列中的数据都是字符串，R 语言已将其转换为 `factor` 数据类型。为了执行此步骤所需的数据处理，你需要将 `GetPoliticalLevel` 转换为字符数据类型。该数据类型与 DAX 中的 `Text` 数据类型类似。这通过第一行代码完成。接下来，你要使用基础 R 中的 `rle()` 函数来定义 `GetPoliticalLevel` 列中的分组。`rle()` 函数中的 “rle” 代表游程编码。当你将 `GetPoliticalLevel` 列传递给 `rle()` 函数时，会得到如图 2-20 所示的输出结果。

![../images/480677_1_En_2_Chapter/480677_1_En_2_Fig20_HTML.jpg](img/480677_1_En_2_Fig20_HTML.jpg)

图 2-20 `rle()` 函数的输出结果

`rle()` 函数返回了一个包含两个向量的列表。第一个向量名为 `lengths`，在此示例中，它包含每个执政期的长度。第二个向量名为 `values`，在此示例中，它包含每个执政期执政的政党。`rle()` 函数返回的信息保存在 `runs` 变量中。

`runs` 变量中包含的信息用于通过以下代码创建 `group_id` 向量：

```
group_id <- rep(seq_along(runs$lengths), runs$lengths)
```

首先，使用 `seq_along()` 函数生成一个整数序列，该序列将代表 `group_id`。由于 `runs$length` 的长度为 9，因此将返回一个包含数字 1 到 9 的整数向量。该表达式作为第一个参数放入 `rep()` 函数中，而向量 `runs$length` 作为第二个参数放入。由于两个参数都包含长度相同的向量，`rep` 函数将取 `seq_along()` 函数生成的第一个元素（在本例中为数字 1），并按照第二个整数向量中表示的次数（在本例中为 3）重复它。因此，数字 1 将被重复三次。接下来，数字 2 将被重复四次，因为 `runs$lengths` 中第二个元素的值为 4。此逻辑对 `runs$lengths` 中的每个元素重复执行，最终输出结果如图 2-21 所示。

![../images/480677_1_En_2_Chapter/480677_1_En_2_Fig21_HTML.jpg](img/480677_1_En_2_Fig21_HTML.jpg)

图 2-21 `group_id` 变量的输出结果

接下来，你使用以下代码创建用于可视化的数据集：

```
dfShadeInfo <- cbind(dfPI, group_id) %>%
  transmute(group_id, Year, Party = GetPoliticalLevel) %>%
  group_by(group_id, Party) %>%
  summarize(start = min(Year), end = max(Year)+0.99) %>%
  ungroup()
```

上述代码首先使用 `cbind()` 函数将 `group_id` 向量作为新列追加到 `dfPI` 数据框中，其中 `cbind()` 中的 `c` 代表列（column）。接下来，使用管道操作符 `%>%` 将生成的数据框作为第一个参数传递给下一行的 `transmute()` 函数。

`transmute()` 动词类似于 `dplyr` 中的 `mutate()` 动词，用于投影新列，但 `transmute()` 函数的不同之处在于，它创建的数据框仅基于函数中定义的列。

上述 `transmute()` 动词将生成一个包含三列的数据框，分别名为 `group_id`、`Year` 和 `Party`。图 2-22 展示了前九行的预览，以显示上述代码中使用 `transmute()` 动词的结果。

![../images/480677_1_En_2_Chapter/480677_1_En_2_Fig22_HTML.jpg](img/480677_1_En_2_Fig22_HTML.jpg)

图 2-22 使用 `transmute()` 动词的结果

请注意，现在每个执政期都有了唯一的标识符！现在你可以使用传统的聚合函数来获取每个执政期的开始和结束时间。再次使用管道操作符将 `transmute()` 动词创建的数据框作为第一个参数传递给下一行的 `group_by()` 动词。`group_by()` 动词将根据 `group_id` 和 `Party` 列对传递给它的数据框进行分组。`group_by()` 的结果将作为第一个参数传递给下一行的 `summarize()` 动词。`summarize()` 动词允许你根据分组对数据集应用聚合函数。因此，你可以创建一个名为 `start` 的列，表示基于分组中最小年份的政党执政期开始时间；还可以创建一个名为 `end` 的列，表示基于分组中最大年份的政党执政期结束时间。将值 0.99 添加到 `end` 列，以最小化旧执政期结束与新执政期开始之间的间隙。

### 步骤 12：创建折线图所需的数据集

用于创建折线图数据集的代码如下：

```
dfLineChartInfo <- dfPI %>%
  transmute(Year, GetGDPStat)
```

目标是开发一个折线图，显示所选 GDP 统计量按年份的变化，其时间跨度覆盖整个数据集。为此，我们需要为数据集提供两列数据，即 `Year` 和 `GetGDPStat`。你可以通过使用 `transmute()` 函数从 `dfPI` 数据框中提取这些列来创建所需的数据集。

### 步骤 13：创建一个用于为阴影区域着色的命名字符向量

```
partyColors = c("Republican"="red", "Democrat"="blue",
"Tie" = "white")
```

共和党的颜色是红色，民主党的颜色是蓝色。你可以通过命名字符向量 `partyColors` 强制可视化工具为各党派使用这些颜色。`partyColors` 命名字符向量将在脚本后续部分被使用，以强制可视化工具在共和党执政期间将背景渲染为红色，在民主党执政期间将背景渲染为蓝色。

### 步骤 14：通过定义 ggplot 函数开始创建图表

前面的步骤专注于数据准备。这些数据准备步骤展示了 Power BI 中 R 可视化的一项优秀特性。如果传递给可视化工具的数据集格式不正确，你可以使用 R 来重塑数据集，使其达到所需格式。

在此步骤中，你将通过以下代码设置 `ggplot()` 函数来开始创建图表：

```
p <- ggplot()
```

你可能已经注意到，你是在不传递任何参数的情况下，通过 `ggplot()` 函数来初始化图表的。你这样做的原因是你的图表将包含两个图层，每个图层都有独立的数据源和美学映射，因此它们将在图层级别进行设置。

### 步骤 15：添加图层以创建背景阴影

这通过以下代码实现：

```
geom_rect(
data = dfShadeInfo,
aes(xmin = start, xmax = end, fill = Party),
ymin = -Inf, ymax = Inf, alpha = 0.4, color = NA
)
```

该图层的数据源是 `dfShadeInfo` 数据框。图 2-23 展示了当选择*总体*政治观点时的效果。

![../images/480677_1_En_2_Chapter/480677_1_En_2_Fig23_HTML.jpg](img/480677_1_En_2_Fig23_HTML.jpg)

图 2-23 `dfShadeInfo` 数据框

上图显示了九个政治统治时期，因此 `geom_rect()` 函数将使用上述数据框，根据每个政治统治时期执政党的颜色来为背景着色。每个政治统治时期的起点和终点通过在 `aes()` 函数中将 `start` 列设置为 `xmin` 参数，并将 `end` 列设置为 `xmax` 参数来定义。上边界通过将 `ymax` 参数设置为 `Inf` 来定义，下边界通过将 `ymin` 设置为 `-Inf` 来定义。使用无穷大作为上下边界可确保阴影区域在垂直方向上填满。每个统治时期都会绘制阴影，最终结果如图 2-24 所示的带阴影背景。

![../images/480677_1_En_2_Chapter/480677_1_En_2_Fig24_HTML.jpg](img/480677_1_En_2_Fig24_HTML.jpg)

图 2-24 由 `geom_rect()` 几何对象生成的背景阴影

请注意，阴影区域与每个统治时期的时间段相对应。各党派使用的颜色将在脚本后续部分进行更改。

### 步骤 16：根据所选统计指标添加折线图

以下代码用于创建可视化的折线图层：

```
geom_line(data = dfLineChartInfo, aes(x = Year, y = GetGDPStat))
```

`geom_line()` 几何对象用于创建折线图。它所基于的数据框是 `dfLineChartInfo` 数据框，该数据框中用于 x 轴的列是 `Year` 列，用于 y 轴的列是 `GetGDPStat` 列。

### 步骤 17：使用基于党派的预定义颜色重新为背景着色

这通过以下代码完成：

```
scale_fill_manual(values=partyColors)
```

`scale_fill_manual()` 函数可用于覆盖 `ggplot2` 使用的默认颜色。之前，你在 `partyColors` 命名字符向量中定义了要为每个党派使用的颜色。通过将 `values` 参数设置为 `partyColors` 变量，`ggplot2` 将使用 `partyColors` 中指定的颜色为背景着色。

## 步骤 18：根据所选统计指标格式化 y 轴

接下来，你根据所选的 GDP 统计指标，使用以下代码格式化 y 轴：

```r
scale_y_continuous(labels=
ifelse(gdpStatName == "ActualGDP",
comma_format(),
percent_format()
)
) +
```

如果选择返回实际 GDP，则 y 轴将使用 `comma_format()` 进行格式化，因为 `ActualGDP` 需要格式化为数字。否则，y 轴将使用 `percent_format()` 格式化为百分比，因为图表将显示 GDP 的百分比变化。`comma_format()` 和 `percent_format()` 函数都来自 `scales` 包。

## 步骤 19：为 x 轴和 y 轴添加标签

以下代码为 y 轴添加动态创建的标签，为 x 轴添加固定标签：

```r
ylab(yAxisName) +
xlab("Year")
```

## 步骤 20：为自定义 R 可视化添加动态标题和说明文字

`ggtitle()` 函数用于通过以下代码动态创建图表标题和副标题：

```r
ggtitle(chartTitle, subtitle = chartSubtitle)
```

## 步骤 21：应用基于《经济学人》出版物的主题

由于你正在可视化经济数据，你将使用 `ggthemes` 包中的 `theme_economist()` 主题来格式化你的可视化。`theme_economist()` 主题使用了《经济学人》出版物所使用的格式化规则。你通过将这行代码添加到脚本中来将 `theme_economist()` 主题应用于你的可视化：

```r
theme_economist()
```

## 步骤 22：将代码添加到 Power BI

本练习中生成可视化的完整脚本列在代码清单 2-5 中。该脚本是功能完整的，因此你可以将其复制并粘贴到 Power BI 中。请确保将其粘贴到 Power BI `R 脚本编辑器` 中注释部分之后。

```r
library(tidyverse)
library(ggthemes)
library(scales)
currentColumns % 
transmute(group_id, Year, Party = GetPoliticalLevel) %>%
group_by(group_id, Party) %>%
summarize(start = min(Year), end = max(Year)+0.99) %>%
ungroup()
#创建折线图层的数据集
dfLineChartInfo % 
transmute(Year, GetGDPStat)
#定义阴影颜色
partyColors = c("Republican"="red", "Democrat"="blue", "Tie" = "white")
#创建可视化
p <- ggplot() +
geom_rect(
data = dfShadeInfo,
aes(xmin = start, xmax = end, fill = Party),
ymin = -Inf, ymax = Inf, alpha = 0.4, color = NA
) +
geom_line(data = dfLineChartInfo, aes(x = Year, y = GetGDPStat)) +
scale_fill_manual(values=partyColors) +
scale_y_continuous(
labels=ifelse(gdpStatName == "Actual GDP", comma_format(),percent_format())
) +
ylab(yAxisName) +
xlab("Year") +
ggtitle(chartTitle, subtitle = chartSubtitle) +
theme_economist()
p
} else {
plot.new()
title("提供的数据不符合图表的要求。")
}
```

代码清单 2-5：生成带阴影折线图的 R 脚本

# 地图

近年来，`空间分析`变得越来越重要。在商业领域，一种流行的空间分析形式是`基于地理的空间分析`。Power BI 提供了内置的地图功能，在大多数情况下都能很好地工作。但在这个示例中，我将展示如何使用 R 来构建自定义地图。您将绘制的可视化效果是一张`州热力图`，它根据该县所在的人口五分位数对州内的县进行着色。颜色越深，表示该县人口越多。图 2-25 是选择印第安纳州时您将创建的可视化效果示例。

![../images/480677_1_En_2_Chapter/480677_1_En_2_Fig25_HTML.jpg](img/480677_1_En_2_Fig25_HTML.jpg)

图 2-25 选择印第安纳州时使用 `ggplot2` 生成的地图可视化效果

该可视化效果是使用一个包含定义美国本土各县边界坐标的数据集创建的。当选择一个州时，`ggplot2` 会使用这些地理坐标来绘制地图。各县填充了不同深浅的蓝色，人口较多的县颜色较深，人口较少的县颜色较浅。您可以利用 Power BI 的交互功能轻松地在各州之间切换。如果将切片器从印第安纳州更改为肯塔基州，则会显示下图 2-26 中的可视化效果。

![../images/480677_1_En_2_Chapter/480677_1_En_2_Fig26_HTML.jpg](img/480677_1_En_2_Fig26_HTML.jpg)

图 2-26 选择肯塔基州时使用 `ggplot2` 生成的地图可视化效果

这种可视化效果的一个出色特性是，它将地理范围限制在所选的地理区域。因此，它不会在其他地理区域上浪费宝贵的图表空间。许多地图可视化效果除了关注的地理区域外，通常还会包含其他地理区域。此外，由于它是使用 `ggplot2` 创建的，因此您可以添加其他动态功能，例如动态`图表标题`、动态`图表副标题`和动态`图表说明`。让我们来看看创建这个令人惊叹的可视化效果所需的步骤！

## 步骤 1：获取必要的数据

1. 获取一个数据集，其中列出各州的完整名称和缩写名称。所使用的数据集来自 [`https://en.wikipedia.org/wiki/List_of_U.S._state_abbreviations`](https://en.wikipedia.org/wiki/List_of_U.S._state_abbreviations)，可以在 *DataWrangling.xlsx* 工作簿的 *Abbreviations* 选项卡中找到。
2. 构建一个包含可视化效果所需的县信息的数据集。用于创建该数据集的信息来自 [`www.nrcs.usda.gov/wps/portal/nrcs/detail/national/home/?cid=nrcs143_013697`](http://www.nrcs.usda.gov/wps/portal/nrcs/detail/national/home/%253Fcid%253Dnrcs143_013697)，可以在 *DataWrangling.xlsx* 工作簿的 *County FIPS Codes* 选项卡中找到。
3. 从人口普查局获取一个按县列出人口的数据集。该数据集可以在 *DataWrangling.xlsx* 工作簿的 *PopulationInfo* 选项卡中找到，同时还有用于获取该数据集的 Python 脚本。
4. 创建一个包含缺失的 `FIPS 代码` 的数据集。一些缺失的 `FIPS 代码` 是由于映射问题造成的，另一些则是因为它们不在 `County FIPS Codes` 数据集中。该数据集将用于纠正此问题。它可以在 *DataWrangling.xlsx* 工作簿的 *TheMissingInfo* 工作表中找到。
5. 从 `ggplot2` 包中获取一个包含美国本土各县边界信息的数据集。该信息存储在 *DataWrangling.xlsx* 工作簿的 *Map Info* 选项卡中，以下是用于获取该数据集的代码：

    ```r
    library(tidyverse)
    us_counties ”
    write_csv(us_counties, path)
    ```

6. 使用 *Power Query* 将前面数据集中的信息合并成可视化所需的形式。该数据集可以在 *DataWrangling.xlsx* 工作簿的 *ChartData* 选项卡中找到。用于创建该数据集的查询也名为 *ChartData*，您可以查看该查询以了解创建数据集所用的步骤。这是 Power BI 将用于创建 R 可视化效果的数据集。
7. 将数据保存到工作簿的 *ChartData* 选项卡中。该数据也保存到了一个名为 `chartdata.csv` 的 `csv` 文件中。

与大多数可视化项目和一般的数据科学项目一样，项目中最耗时的部分通常是获取、清理和整理数据。在本练习中，我为您完成了这些艰巨的工作，以便您可以专注于数据可视化方面。您可以查看本练习的代码仓库中的材料，了解我整理数据所采取的步骤。对于有 R 和/或 Python 背景的人来说，这样做是熟悉 *Power Query* 的好方法。

## 步骤 2：将数据加载到 Power BI 中

您需要将 `chartdata.csv` 文件导入 Power BI，然后在将其加载到数据模型之前对数据集进行一些小的调整。您可以参考此示例的 `pbix` 文件来查看这些转换。

## 步骤 3：在筛选器窗格中基于州创建切片器

首先将 `State` 列拖到报表画布上，基于 `State` 列创建一个切片器。完成此操作后，您需要将可视化类型更改为`切片器`可视化，从而将该可视化效果更改为切片器。*切片器*是其图标中包含漏斗图像的可视化效果。

## 步骤 4：配置 R 可视化效果

转到可视化效果窗格，将一个 R 可视化效果拖到报表画布上。将可视化效果调整到所需大小。将 `chartdata` 表中的 `index`、`lat`、`long`、`County`、`Total Population` 和 `State` 字段拖放到*字段*窗格中。如果字段窗格未显示，请选择 R 可视化效果，它应该会出现。

## 步骤 5：将数据导出到 R Studio 进行开发

单击 *R 脚本编辑器* 标题栏上的 45° 箭头。此操作会将传递给 R 的数据框和 R 起始代码导出到 *R Studio*。如果您需要更详细的说明，请参考“标注图”部分中的步骤 4。

## 步骤 6：加载所需的包

此脚本将使用的包如下所示：

```r
library(tidyverse)
library(ggthemes)
```

来自 `tidyverse` 的 `dplyr` 包将用于一些数据整理，来自 `tidyverse` 的 `stringr` 包用于重新格式化一些标题，来自 `tidyverse` 的 `ggplot2` 包将用于创建地图可视化效果。来自 `ggthemes` 包的 `theme_map()` 主题将用于格式化地图可视化效果的非数据元素。

## 步骤 7：创建数据验证测试所需的变量

以下是创建验证测试变量的代码：

```r
currentColumns <- sort(colnames(dataset))
requiredColumns <- c("County", "Index", "lat", "long", "State", "Total Population")
columnTest <- isTRUE(all.equal(currentColumns, requiredColumns))
state <- str_to_title(unique(dataset$State))
```

第一行从传递给 R 的数据集中获取列名，并按字母顺序排序，然后将结果放入 `currentColumns` 字符向量变量中。第二行创建一个字符向量，其中包含可视化效果期望的列名（按字母顺序），并将其分配给名为 `requiredColumns` 的字符向量。第三行执行一个测试，将 `currentColumns` 字符向量中的列名与 `requiredColumns` 字符向量中的列名进行比较。如果它们匹配，则返回 `TRUE`；否则，返回 `FALSE`。第四行获取 `dataset` 数据框的 `State` 列中的所有唯一元素，并使用 `stringr` 包中的 `str_to_title()` 函数将州名转换为正确的名称格式。

## 步骤 8：创建数据验证测试

数据验证的框架如下：

```r
if (length(state) == 1 & columnTest) {

} else {
plot.new()
title("提供的数据不符合图表要求。")
}
```

可视化的源数据框只需包含一个州，并且需要包含所需的列。上述 `if` 语句用于测试数据框中是否只有一个州，以及数据框是否通过了列测试。如果两个条件都满足，则继续执行创建地图可视化的代码；否则，将生成一个空白可视化，并附带一条消息，告知您 R 没有足够的信息，无法创建可视化。

## 步骤 9：创建图表标题变量

以下是创建图表标题所需的代码：

```r
chartTitle <- paste0(state, "的县人口分析")
subTitle <- "(颜色越深，人口越多)"
```

主图表标题通过将所选州与字符串 `"的县人口分析"` 拼接而成，并赋值给 `chartTitle` 变量。字符串 `"(颜色越深，人口越多)"` 用作副标题，并赋值给 `subTitle` 变量。

## 步骤 10：向数据集添加五分位数列

如果您还记得，此可视化的目标是根据人口数量将每个州的县按五分位数分组。您可以使用 `dplyr` 中的 `ntile()` 函数将州内的县按五分位数分组，如下列代码所示：

```r
chartdata <- dataset %>%
mutate(quintile = factor(ntile(`总人口`, 5)))
```

该代码从 `dataset` 数据框开始，创建一个名为 `chartdata` 的新数据框，然后使用 `dplyr` 的管道操作符将其作为第一个参数传递给下一行的 `mutate()` 动词。`mutate()` 动词用于投影一个名为 `quintile` 的新列，该列将使用 `ntile()` 函数创建。`ntile()` 函数被包裹在 `factor` 函数中，以将五分位数转换为因子数据类型。此操作的效果类似于 Power BI 中的 *不汇总*。将五分位数转换为因子后，它们将被视为分类数据，而非数值数据。

## 步骤 11：定义用于地图着色的颜色

您将使用不同深浅的蓝色来为所选州的县着色。您将使用以下命名字符向量中列出的五种不同深浅的蓝色：

```r
quintileColors <-
c(
"1" = "dodgerblue",
"2" = "dodgerblue1",
"3" = "dodgerblue2",
"4" = "dodgerblue3",
"5" = "dodgerblue4"
)
```

为 `五分位数 1` 列出的 `dodgerblue` 是列表中最浅的蓝色，而为 `五分位数 5` 列出的 `dodgerblue4` 是最深的蓝色。这里，您使用颜色名称而非十六进制值来定义它们。请访问以下网站，了解 `ggplot2` 中可用的更多颜色：[`www.stat.columbia.edu/~tzheng/files/Rcolor.pdf`](http://www.stat.columbia.edu/%257Etzheng/files/Rcolor.pdf)。您将看到可用于引用特定颜色的可用名称。

## 步骤 12：定义 `ggplot()` 函数

首先，使用以下代码定义 `ggplot()` 函数：

```r
ggplot(chartdata, aes(long, lat, group = County,
fill = quintile))
```

您使用 `chartdata` 数据框作为图表的数据源。接下来，您配置 `aes()` 函数，将 `long` 列设置为 `x` 坐标，将 `lat` 列设置为 `y` 坐标，将 `County` 设置为 `group` 美学映射，将 `quintile` 设置为 `fill` 美学映射。这是我们第一次使用 `group` 美学映射，因为在此示例之前，我们一直使用的是*单个几何对象*，而 `geom_polygon()` 几何对象被称为*集合几何对象*。

*单个* 几何对象仅依赖于一行数据，但 *集合* 几何对象依赖于多行数据。此示例将使用 `geom_polygon()` 几何对象，它是一个 *集合* 几何对象，并将使用多行数据来定义将要创建的多边形可视化的边界。

此示例中的 `geom_polygon()` 使用数据集中每个 `group`（在此示例中为数据集中的 `County`）的纬度和经度点绘制多边形。`fill` 属性用于告诉 `ggplot2` 您希望如何为每个县的内部着色。在这里，您将根据每个县所在的五分位数为其内部着色。

您能够使用*笛卡尔坐标系*（在此示例中，是基于 x 轴和 y 轴的坐标系）来表示您的空间数据，因为您查看的是地球上一个相对较小的区域。尽管地球是球体形状，但当您查看一个相对较小的地理区域时，您可以使用与分析平面相同的技术来分析它，并获得相对较好的结果。

## 步骤 13：添加地图图层

以下是添加地图图层的代码：

```r
geom_polygon(show.legend = FALSE, color = "black")
```

您已经在 `ggplot()` 函数中定义了地图所需的美学映射。它们将延续到 `geom_polygon()` 几何对象，因为在 `ggplot()` 函数中定义的任何内容都可用于所有后续图层。此可视化中不需要图例，因此您将 `show.legend` 参数设置为 `FALSE`。您希望边框颜色为黑色。通过将 `color` 参数设置为 `black` 来使其变为黑色。

请注意，当您想要将图表属性设置为特定颜色时，需要在不使用 `aes()` 函数的情况下进行。原因已在第 1 章中详细解释。

## 步骤 14：格式化 x 轴和 y 轴

以下代码用于重新格式化 `x` 轴和 `y` 轴：

```r
scale_x_continuous(name = NULL, labels = NULL, breaks = NULL) +
scale_y_continuous(name = NULL, labels = NULL, breaks = NULL)
```

您通过将名称、标签和刻度标记设置为 `NULL` 来移除它们。这样做是因为您正在可视化地图，不需要这些信息。

## 步骤 15：根据五分位数对县进行着色

以下代码将用于对县进行着色：

```r
scale_fill_manual(values = quintileColors)
```

`ggplot2` 包具有很好的默认设置，这也是该包如此出色的原因之一。更棒的是，您可以轻松覆盖这些默认设置。如果使用 `ggplot2` 的默认设置，地图将如图 2-27 所示进行着色。

![../images/480677_1_En_2_Chapter/480677_1_En_2_Fig27_HTML.jpg](img/480677_1_En_2_Fig27_HTML.jpg)

图 2-27 选择佛罗里达州时，使用默认颜色生成的地图可视化

图 2-28 是您通过 `scale_fill_manual()` 函数使用自定义颜色后地图的外观。

![../images/480677_1_En_2_Chapter/480677_1_En_2_Fig28_HTML.jpg](img/480677_1_En_2_Fig28_HTML.jpg)

图 2-28 选择佛罗里达州时，使用自定义颜色生成的地图可视化

第二个版本使报告用户能够根据颜色深浅轻松判断哪些县人口较少，哪些县人口较多。

## 步骤 16：改进所选州的近似显示

以下代码用于基于*墨卡托地图*投影进行快速宽高比近似：

```r
coord_quickmap()
```

`geom_polygon()` 在创建地图方面做得很好，但使用上述代码可以快速使地图显示得更精确一些。

## 步骤 17：为自定义 R 视觉对象添加动态标题和说明文字

以下代码使用 `ggtitle()` 函数以及存储图表标题和副标题信息的变量来添加动态标题：

```r
ggtitle(chartTitle, subtitle = subTitle)
```

## 步骤 18：应用 `theme_map()` 主题

您可以使用 `ggthemes` 包中的 `theme_map()` 主题来进一步增强地图的视觉吸引力。

### 步骤 19：将代码添加到 Power BI

完整的脚本见代码清单 2-6。将其复制并粘贴到 Power BI 的 `R 脚本编辑器` 中。该 R 视觉对象将完全响应 Power BI 报表中的 `state` 切片器。

```r
library(tidyverse)
library(ggthemes)
currentColumns <- chartdata %>%
  mutate(quintile = factor(ntile(`Total Population`, 5)))
quintileColors <-
  c(
    "1" = "dodgerblue",
    "2" = "dodgerblue1",
    "3" = "dodgerblue2",
    "4" = "dodgerblue3",
    "5" = "dodgerblue4"
  )
ggplot(chartdata, aes(long, lat, group = County,
                      fill = quintile)) +
  geom_polygon(show.legend = FALSE, color = "black") +
  scale_x_continuous(name=NULL, labels=NULL, breaks=NULL) +
  scale_y_continuous(name=NULL, labels=NULL, breaks=NULL) +
  scale_fill_manual(values = quintileColors) +
  coord_quickmap() +
  ggtitle(chartTitle, subtitle = subTitle) +
  theme_map()
} else {
  plot.new()
  title("提供的数据不符合图表要求。")
}
```

代码清单 2-6：生成地图图表的 R 脚本

## 四象限图

作为 Microsoft 数据专业人士，您一定很熟悉 Gartner 生成的图 2-29 中的视觉对象。

![../images/480677_1_En_2_Chapter/480677_1_En_2_Fig29_HTML.jpg](img/480677_1_En_2_Fig29_HTML.jpg)

图 2-29：2020 年 Gartner 魔力象限图

Microsoft 在 `商业智能` 领域相比竞争对手表现出色，Gartner 也对此予以认可。Microsoft 一直使用像 `Gartner 魔力象限图` 这样的行业对比来宣传其在行业中的地位。

Gartner 使用的视觉对象——`四象限图`，在基于两个指标比较不同实体时非常有效。在此案例中，Gartner 根据各 BI 供应商的 `执行能力` 和 `愿景完整性` 进行比较。Gartner 通过添加一些细节使图表更具视觉吸引力。其中一些细节包括：

-   为象限添加标签
-   为象限 2 和 3 添加背景阴影
-   为 x 轴和 y 轴使用非传统标签
-   添加图表说明文字

在 Power BI 中创建这些细微之处并不容易，但在 R 中使用 `ggplot2` 则很简单。

在本节中，我将使用基于 2008-2009 赛季洛杉矶湖人队“逐回合”数据的数据集来说明我的观点。这些信息将用于创建图 2-30 中的四象限图，该图根据 `总得分` 和 `总篮板数` 对球员进行比较。

![../images/480677_1_En_2_Chapter/480677_1_En_2_Fig30_HTML.jpg](img/480677_1_En_2_Fig30_HTML.jpg)

图 2-30：基于篮板和得分比较洛杉矶湖人队球员的四象限图

与之前的可视化类似，您将利用 Power BI 的交互功能，为视觉对象添加切片器，以便查看不同场景下的情况。将有一个用于比赛类型（主场或客场）的筛选器，以及一个用于比赛时段的筛选器。图 2-31 中的视觉对象显示了仅考虑客场比赛时的比较情况。

![../images/480677_1_En_2_Chapter/480677_1_En_2_Fig31_HTML.jpg](img/480677_1_En_2_Fig31_HTML.jpg)

图 2-31：基于客场比赛的四象限图

让我们来看看创建这个精美可视化所需的步骤！

### 步骤 1：获取必要的数据

本例中使用的数据集来自 R 中的 `lubridate` 包。获取该数据集的代码如下：

```r
library(tidyverse)
library(lubridate)
chartData <- playbyplay %>%
  filter(
    (player != "" & team == "LAL" &
       result == "made" &
       etype %in% c("shot","free throw")
    ) |
      (player != "" &
         team == "LAL" &
         etype == "rebound")
  ) %>%
  mutate(
    rebound = ifelse(etype == "rebound",1,0)
  ) %>%
  group_by(game_type, period, player) %>%
  summarize(
    `Total Points` = sum(points),
    Rebounds = sum(rebound)
  ) %>%
  rename(
    `Game Type` = game_type,
    Period = period,
    Player = player
  )
path <- "lakers.csv"
write_csv(chartdata, path)
```

输出结果保存为一个 `csv` 文件，因为这种格式便于 Power BI 使用。如果您不理解上述代码，请不要担心，您将在本书后面学习到所使用的技术。现在，您只需知道，如果需要重新创建此可视化中使用的数据集，可以通过运行上述 R 脚本来实现。

### 步骤 2：将数据加载到 Power BI

使用 `获取数据` 从步骤 1 创建的 `csv` 文件中将数据加载到 Power BI。加载后，每个列的数据类型应如下表所示：

| 列名 | 数据类型 |
| --- | --- |
| 比赛类型 | 文本 |
| 时段 | 整数 |
| 球员 | 文本 |
| 总得分 | 整数 |
| 篮板 | 整数 |

该文件已加载到代码仓库中本例文件夹内的 `pbix` 文件中。您可以查看 Power Query 步骤以了解应用了哪些转换。

### 步骤 3：为比赛类型和时段创建切片器

基于 `比赛类型` 字段创建报表切片器：首先将 `比赛类型` 字段拖到报表画布上，然后在 `可视化` 窗格中选择 `切片器` 视觉对象。在将 `切片器` 视觉对象转换为切片器之前，请确保添加到画布上的字段处于高亮状态。切片器视觉对象是图像中包含筛选器图标的那个。对 `时段` 字段执行相同操作。

### 步骤 4：在报表画布上配置 R 视觉对象

转到可视化窗格，将 R 视觉对象拖到报表画布上。将视觉对象调整到所需大小。将 `chartData` 表中的 `球员`、`篮板` 和 `总得分` 字段拖到 `字段` 窗格中。如果 `字段` 窗格未显示，请选择 R 视觉对象，它应该会显示出来。

### 步骤 5：将数据导出到 R Studio 进行开发

单击 `R 脚本编辑器` 标题栏上的 45 度箭头。此操作会将传递给 R 的数据框以及 R 起始代码导出到 `R Studio`。如果需要更详细的说明，请参阅“标注图”部分中的步骤 4。

### 步骤 6：加载所需的包

此脚本所需的包如下：

```r
library(tidyverse)
library(ggrepel)
library(ggthemes)
library(scales)
```

`tidyverse` 中的 `dplyr` 包用于执行一些数据整理任务，`tidyverse` 中的 `ggplot2` 用于创建可视化，`ggrepel` 包用于向可视化中的数据点添加标签，`ggthemes` 包中的 `theme_tufte()` 主题用于格式化视觉对象的非数据元素，`scales` 包中的 `rescale()` 函数用于重新缩放部分数据。

### 第 7 步：创建数据验证测试所需的变量

验证测试所需的变量通过以下代码定义：

```r
currentColumns <- sort(colnames(dataset))
requiredColumns <- c("Player", "Rebounds", "Total Points")
columnTest <- isTRUE(all.equal(currentColumns, requiredColumns))
noPlayerDups <-
length(unique(dataset$Player)) == length(dataset$Player)
```

以下是对上述代码片段中每一行作用的说明：

-   第一行代码基于 `dataset` 数据框的列名创建了一个名为 `currentColumns` 的字符向量变量，这些列名按字母顺序排序。
-   第二行代码创建了一个名为 `requiredColumns` 的字符向量变量，其中包含四象限图所需的列，并按字母顺序排列。
-   第三行代码使用基础 R 语言中的 `all.equal()` 函数来测试 `currentColumns` 变量和 `requiredColumns` 变量是否等价。如果它们相等，则 `all.equal()` 返回 `TRUE`；否则，它会返回关于为何不相等的信息。我们使用 `isTRUE()` 包裹 `all.equal()` 以获取布尔值响应。
-   第四行代码（由于格式原因实际为两行）使用 `noPlayerDups` 变量来保存检查数据集中是否存在重复球员的测试结果。它通过比较 `Players` 列中唯一元素的数量与 `Players` 列的长度来实现。如果两者相等，则说明没有重复的球员。

### 第 8 步：创建数据验证测试

验证测试的模板如下所示：

```r
if (noPlayerDups & columnTest) {

} else {
plot.new()
title("The data supplied did not meet the requirements of the
chart.")
}
```

验证测试执行两个测试：`noPlayerDups` 测试和 `columnTest` 测试。如果两个测试结果都为 `TRUE`，则执行创建图表的代码。否则，会创建一个空白图表，并显示关于 `ggplot2` 为何无法创建图表的信息。

### 第 9 步：创建图表标题

本例中，图表标题、副标题和说明文字均使用静态字符串。它们通过以下代码定义：

```r
chart.title <- "LA Lakers player comparison"
chartsubtitle <- "(2008 - 2009 Season)"
chart_source <- "Source:  lubridate package"
```

### 第 10 步：向数据集添加额外列

您正在创建一个四象限图，用于根据篮板和得分比较洛杉矶湖人队的篮球运动员。篮板和得分处于两个不同的量级，因为得分的可能取值范围远高于篮板。四象限图要求它们处于同一量级。这一步通过以下代码实现：

```r
graph_data %>%
mutate(
Scaled.Rebounds =
round(rescale(Rebounds, to = c(-10, 10)), 1)
,Scaled.TotalPoints =
round(rescale(`Total Points`, to = c(-10, 10)), 1)
)
```

在这一步中，使用 `mutate` 动词向数据框添加了两个新列：`Scaled.Rebounds` 和 `Scaled.TotalPoints`。这两列使用 `scales` 包中的 `rescale()` 函数将它们都缩放到 -10 到 +10 之间。这些新列将在可视化中用于表示每位球员的篮板和总得分。

### 第 11 步：通过定义 ggplot 函数开始创建图表

以下代码用于定义 `ggplot()` 函数：

```r
p <- ggplot(graph_data,
aes(x = Scaled.Rebounds, y = Scaled.TotalPoints)
)
```

此可视化中使用的数据集是 `graph_data` 数据框，并且在 `ggplot()` 函数中仅定义了两个美学映射，即 `x` 和 `y` 美学映射。

### 第 12 步：使用 geom_point() 几何对象在图表上绘制球员点

接下来，您向图表添加点来表示球员。点的位置基于给定球员的篮板数和总得分的缩放版本。这通过向代码添加 `geom_point()` 几何对象来实现，如下所示：

```r
p <- ggplot(
graph_data,
aes(x = Scaled.Rebounds, y = Scaled.TotalPoints)
) +
geom_point()
```

`geom_point()` 几何对象继承了 `ggplot()` 函数的美学映射，因此无需在 `geom_point()` 图层中定义所需的 `x` 和 `y` 美学映射。

### 第 13 步：为每个象限添加标签

接下来，您向可视化添加一层标签，用于标识点所代表的球员名称。这通过使用 `geom_label_repel()` 几何对象来实现，如下列代码所示：

```r
ggplot(graph_data,
aes(x = Scaled.Rebounds,
y = Scaled.TotalPoints)
) +
geom_point() +
geom_label_repel(aes(
label = Player),
size = 4, show.legend = FALSE
)
```

`geom_label_repel()` 几何对象继承了 `ggplot()` 函数的 `x` 和 `y` 参数，因此您无需定义这两个美学映射。唯一需要定义的美学映射是 `label` 美学映射，您将其设置为 `Player` 列。该列中的值将用于用球员姓名标记其对应的点。您在 `aes()` 函数外部设置 `size` 参数，因为您希望所有标签具有相同的字体大小。此外，默认情况下，`ggplot2` 会为添加到 `x` 和 `y` 比例尺之外的每个比例尺创建一个图例。`label` 不需要图例，因此为了防止显示图例，您将 `show.legend` 属性设置为 `FALSE`。

### 第 14 步：在 x 轴和 y 轴上绘制垂直和水平线

可视化需要为 `x` 轴和 `y` 轴绘制清晰的线条。以下代码中的最后两行添加了所需的水平线和垂直线：

```r
p <- ggplot(
graph_data,
aes(x = Scaled.Rebounds,
y = Scaled.TotalPoints)
) +
geom_point() +
geom_label_repel(
aes(label = Player),
size = 4,
show.legend = FALSE
) +
geom_hline(yintercept = 0) +
geom_vline(xintercept = 0)
```

使用 `geom_hline()` 和 `geom_vline()` 几何对象来绘制线条。将 `geom_hline()` 函数中的 `yintercept` 设置为 0，可以绘制一条穿过 y 轴 0 点的水平线；将 `geom_vline()` 函数中的 `xintercept` 设置为 0，可以绘制一条穿过 x 轴 0 点的垂直线。

### 步骤 15：为图表添加象限标签

图 2-32 展示了执行前述步骤后的视觉效果。

![../images/480677_1_En_2_Chapter/480677_1_En_2_Fig32_HTML.jpg](img/480677_1_En_2_Fig32_HTML.jpg)

**图 2-32** 象限图的雏形

图表进展顺利，你正朝着最终目标稳步前进。在此步骤中，你将使用以下代码为每个象限添加标签：

```
annotate("text", x = -5, y = 11, label = "High Scorer",
alpha = 0.2, size = 6) +
annotate("rect", xmin = -3.5, xmax = -6.5, ymin = 10.5,
ymax = 11.5, alpha = .2) +
annotate("text", x = 5, y = 11, label = "Beast Mode",
alpha = 0.2, size = 6) +
annotate("rect", xmin = 3.5, xmax = 6.5, ymin = 10.5,
ymax = 11.5, alpha = .2)
annotate("text", x = -5, y = -11, label = "Average",
alpha = 0.2, size = 6) +
annotate("rect", xmin = -3.5, xmax = -6.5, ymin = -11.5,
ymax = -10.5, alpha = .2) +
annotate("text", x = 5, y = -11, label = "High Rebounder",
alpha = 0.2, size = 6) +
annotate("rect", xmin = 3.5, xmax = 6.5, ymin = -11.5,
ymax = -10.5, alpha = .2) +
```

上述代码为象限图中的每个象限构建了带边框的标签。左上角的第一个象限标记为`高分得分手`，因为该组球员得分排名高但篮板排名低；右上角的第二个象限命名为`野兽模式`，因为该象限球员在篮板和得分方面均排名靠前；左下角的第三个象限标记为`普通球员`，因为该象限球员在篮板和得分方面均相对较低；右下角的第四个象限标记为`篮板高手`，因为该象限球员篮板排名高但得分排名低。

象限标签的文本和边框均使用 `annotate()` 函数创建。`annotate()` 函数的第一个参数用于指定注释所使用的几何对象类型。本示例中创建象限标签所使用的几何对象类型是 `text` 和 `rect`。在上述代码中，相应象限的标签文本通过 `text` 几何对象创建，然后使用 `rect` 几何对象在文本周围放置矩形边框。首先，我们来了解 `text` 注释的工作原理，然后再介绍 `rect` 注释。

如前所述，创建 `text` 注释时，需将 `annotate()` 函数的第一个参数值设置为 `text`。`x` 和 `y` 参数用于确定“文本”的位置。在此可视化图表中，`text` 注释的 `x` 值位于各自象限的中心，而 `y` 值则位于可能数据点区域之外。这样做是为了尽量减少球员数据与象限标签的重叠。每个文本注释的 `alpha` 参数设置为 `0.2`，以确保即使球员姓名与标签位置重合，也能显示在标签之上。

`alpha` 参数用于设置图表中几何对象的透明度。数值越接近零，元素越透明；数值越接近一，元素越不透明。

使用 `text` 注释为给定象限创建象限标签后，再使用 `rect` 注释创建标签的边框。这通过将 `annotate()` 函数的第一个参数设置为 `rect` 来实现。要绘制的矩形通过 `xmin`、`xmax`、`ymin` 和 `ymax` 参数定义。以下是各参数用途的说明：

- `xmin` 参数用于确定左上角和左下角的 `x` 位置。
- `xmax` 参数用于确定右上角和右下角的 `x` 位置。
- `ymin` 参数用于确定左下角和右下角的 `y` 位置。
- `ymax` 参数用于确定左上角和右上角的 `y` 位置。

定义好角点后，`annotate()` 函数会用线条连接这些点，最终在标签周围形成一个矩形边框。

### 步骤 16：为 X 轴和 Y 轴添加标签

以下是用于为 X 轴和 Y 轴标签名称添加箭头的代码：

```
xlab(bquote("Rebounding" ~ symbol('\256'))) +
ylab(bquote("Scoring" ~ symbol('\256'))) +
```

这是一种技巧，我使用了通常用于在 R 中创建数学表达式的 `bquote()` 函数。在此示例中，你并非用它来创建数学表达式，而是为标签添加箭头符号。这通过 `xlab()` 和 `ylab()` 函数实现。`xlab()` 函数用于重命名 X 轴标签，`ylab()` 函数用于重命名 Y 轴标签。代码 `symbol('\256')` 是绘制箭头所需的代码。

### 步骤 17：为自定义 R 可视化添加动态标题和说明

使用 `labs()` 函数，通过以下代码为可视化添加图表标题、副标题和图表来源：

```
labs(
title = chart.title,
subtitle = chartsubtitle,
caption = chart_source
)
```

### 步骤 18：添加主题

图表的主题通过 `ggthemes` 包中的 `theme_tufte()` 主题，运用爱德华·塔夫特的可视化原则进行更改。这通过将以下代码添加到脚本中来实现：

```
theme_tufte() +
```

### 步骤 19：执行最后的清理工作

执行前面的步骤将得到图 2-33 中的可视化效果。

![../images/480677_1_En_2_Chapter/480677_1_En_2_Fig33_HTML.jpg](img/480677_1_En_2_Fig33_HTML.jpg)

图 2-33

执行最后清理前的四象限图

您需要重新格式化图表标题和坐标轴标签，使其看起来像下面的可视化效果。

![../images/480677_1_En_2_Chapter/480677_1_En_2_Fig34_HTML.jpg](img/480677_1_En_2_Fig34_HTML.jpg)

图 2-34

四象限图的最终版本

您可以使用以下代码来实现：

```
theme(
plot.title = element_text(hjust = 0.5, size = 25)
, plot.subtitle = element_text(hjust = 0.5, size = 20)
, panel.border = element_rect(
colour = "black",
size = 2,
fill = NA)
, axis.title.x = element_text(hjust = 0.1, size = 18)
, axis.title.y = element_text(hjust = 0.1, size = 18)
) +
scale_x_continuous(labels = NULL, breaks = NULL) +
scale_y_continuous(labels = NULL, breaks = NULL)
```

让我们详细分析一下前面的代码。如您所见，大部分所需的更改都是通过 `theme()` 函数处理的。以下是此步骤中由 `theme()` 函数修改的组件列表，以及它们是如何被修改的描述：

- `plot.title`：此组件用于控制图表标题，并通过 `element_text()` 函数实现。`element_text()` 函数的 `hjust` 参数用于控制文本的水平位置，它接受 `0` 到 `1` 之间的值。值为 `0` 表示文本左对齐，值为 `0.5` 表示文本居中，值为 `1` 表示文本靠右对齐。您使用了值 `0.5`，因为您希望文本居中。您使用 `size` 参数将字体大小增加到 `25`。

- `plot.subtitle`：此组件用于控制图表副标题，同样通过 `element_text()` 函数实现。`hjust` 参数的设置方式与 `plot.title` 组件中的相同，因为您希望像处理 `plot.title` 组件一样将文本居中。您希望副标题比主标题小，因此将其设置为较小的数值 `20`。

- `panel.border`：此组件用于控制可视化周围的边框，并通过 `element_rect()` 函数实现。`colour` 参数用于设置边框的颜色，`size` 参数用于设置边框的粗细。

- `axis.title.x`：此组件用于控制 x 轴的标签，并通过 `element_text()` 函数实现。您希望标签位于 x 轴的左侧，因此将 `hjust` 参数的值设置为 `0.1`。您使用 `size` 参数将文本的字体大小更改为 `18`。

- `axis.title.y`：此组件用于控制 y 轴的标签，其配置方式与 `axis.title.x` 类似。不同之处在于定位是在垂直线上而不是水平线上。因此，值越接近 `0`，其在 Y 轴上的位置越低；值越接近 `1`，其在 Y 轴上的位置越高。您使用了值 `0.1`，以便将 Y 轴标签定位在靠近原点处。

最后，使用 `scale_x_continuous()` 和 `scale_y_continuous()` 比例尺来重新格式化坐标轴。两个比例函数中的 `labels` 和 `breaks` 都设置为 `NULL`，以将它们从图表中移除。这些操作的结果已在之前的图 2-34 中展示。

### 步骤 20：将代码添加到 Power BI

生成该可视化效果的完整脚本见代码清单 2-7。

```
library(tidyverse)
library(ggrepel)
library(ggthemes)
library(scales)
currentColumns %
mutate(
Scaled.Rebounds =
round(rescale(Rebounds, to = c(-10, 10)), 1)
,Scaled.TotalPoints =
round(rescale(`Total Points`, to = c(-10, 10)), 1)
)
p <- ggplot(graph_data, aes(x = Scaled.Rebounds, y = Scaled.TotalPoints)) +
geom_point() +
geom_label_repel(aes(label = Player), size = 4, show.legend = FALSE) +
geom_hline(yintercept = 0) +
geom_vline(xintercept = 0) +
# quad 1
annotate("text", x = -5, y = 11, label = "High Scorer", alpha = 0.2, size = 6) +
annotate("rect", xmin = -3.5, xmax = -6.5, ymin = 10.5, ymax = 11.5, alpha = .2) +
# quad 2
annotate("text", x = 5, y = 11, label = "Beast Mode", alpha = 0.2, size = 6) +
annotate("rect", xmin = 3.5, xmax = 6.5, ymin = 10.5, ymax = 11.5, alpha = .2) +
# quad 3
annotate("text", x = -5, y = -11, label = "Average", alpha = 0.2, size = 6) +
annotate("rect", xmin = -3.5, xmax = -6.5, ymin = -11.5, ymax = -10.5, alpha = .2) +
# quad 4
annotate("text", x = 5, y = -11, label = "High Rebounder", alpha = 0.2, size = 6) +
annotate("rect", xmin = 3.5, xmax = 6.5, ymin = -11.5, ymax = -10.5, alpha = .2) +
# Shade lower left quadrant
annotate("rect", xmin = -Inf, xmax = 0.0, ymin = -Inf, ymax = 0, alpha = 0.1, fill = "lightskyblue") +
# Shade upper right quadrant
annotate("rect", xmin = 0.0, xmax = Inf, ymin = 0.0, ymax = Inf, alpha = 0.1, fill = "lightskyblue") +
# Titles
xlab(bquote("Rebounding" ~ symbol('\256'))) +
ylab(bquote("Scoring" ~ symbol('\256'))) +
labs(title = chart.title, subtitle = chartsubtitle, caption = chart_source) +
# Prettying things up
theme_tufte() +
theme(
plot.title = element_text(hjust = 0.5, size = 25)
, plot.subtitle = element_text(hjust = 0.5, size = 20)
, panel.border = element_rect(colour = "black", size = 2, fill = NA)
, axis.title.x = element_text(hjust = 0.1, size = 18)
, axis.title.y = element_text(hjust = 0.1, size = 18)
) +
scale_x_continuous(labels = NULL, breaks = NULL) +
scale_y_continuous(labels = NULL, breaks = NULL)
p
} else {
plot.new()
title("The data supplied did not meet the requirements of the chart.")
}
```

代码清单 2-7

生成四象限图的 R 脚本

复制该脚本，然后将其粘贴到此可视化效果关联的 `R 脚本编辑器` 中。生成的 R 可视化效果将完全响应报表中的筛选器。

## 添加回归线

数据分析师最常为其散点图添加的增强功能之一是趋势线。使用 `ggplot2` 在 R 中为散点图添加趋势线非常简单，而且您拥有的配置选项通常比其他可视化工具更多。例如，您可以选择：

- 为趋势线使用线性模型、广义线性模型、广义加性模型以及其他一些模型
- 确定您想要的置信水平
- 为趋势线使用您自己的公式
- 使用更多可配置的选项，数量多到无法一一列举

在本节中，您将使用一个基于 R 中预装数据集的简单示例。该数据集名为 `women`，它显示的是美国女性按身高的平均体重。您将使用散点图绘制这些信息，然后在散点图上叠加一条基于线性回归模型的趋势线。生成的可视化效果见图 2-35。

![../images/480677_1_En_2_Chapter/480677_1_En_2_Fig35_HTML.jpg](img/480677_1_En_2_Fig35_HTML.jpg)

图 2-35

添加了线性回归线的散点图

让我们逐步了解如何生成此可视化效果！

### 步骤 1：获取必要的数据

本示例中使用的代码是基础 R 预装的数据集之一。以下是用于创建作为本示例源数据的 `csv` 文件的 R 代码：

```
library(tidyverse)
data(women)
setwd("")
write_csv(women, "women.csv")
```

运行上述代码将生成一个名为 `women.csv` 的文件，该文件将在下一步中作为 Power BI 的数据源。

### 步骤 2：将数据加载到 Power BI

使用 `GetData` 将数据读取到 Power BI。为此，请单击 Power BI 中的 `Home` 选项卡，然后单击 `GetData` ➤ `Text/CSV`。接下来，浏览到您保存 `women.csv` 文件的位置。加载数据时，请确保 `height` 和 `weight` 列均为数字格式。

### 步骤 3：配置 R 可视化对象

转到可视化窗格，将一个 R 可视化对象拖到报表画布上。将可视化对象调整到所需大小。将 `women` 表中的 `height` 和 `weight` 列拖放到 `Fields` 窗格中。如果 `Fields` 窗格未显示，请选中 R 可视化对象，它应该会出现。

### 步骤 4：将数据导出到 R Studio 进行开发

单击 `R script editor` 标题栏上的 45° 箭头。此操作会将传递给 R 的数据框和 R 起始代码导出到 `R Studio`。如果您需要更详细的说明，请参考“标注图”部分中的步骤 4。

### 步骤 5：加载所需的包

此脚本中将使用的 R 包是 `tidyverse` 和 `ggthemes`。来自 `tidyverse` 的 `ggplot` 包用于创建可视化，来自 `tidyverse` 的 `dplyr` 包用于为可视化塑造数据。`ggthemes` 包提供了 `theme_tufte()` 主题，该主题将用于更改图表的主题。

### 步骤 6：创建数据验证测试所需的变量

以下代码用于创建数据验证测试的变量：

```r
currentColumns <- sort(colnames(dataset))
requiredColumns <- c("height", "weight")
columnTest <- isTRUE(all.equal(currentColumns, requiredColumns))
```

您需要确保从 Power BI 传递给 R 的数据集具有正确的结构，并且需要准备好处理结构不正确的情况。在此示例中执行的测试是一个确保传递给 R 的数据集仅包含 R 可视化所需列的测试。

为此，您首先获取 `dataset` 数据框中列的名称，并将其分配给一个名为 `currentColumns` 的字符向量变量。该变量中的元素按字母顺序排序。下一行代码创建另一个字符向量，其中包含创建 R 可视化所需的列。它们也按字母顺序提供。代码片段中的最后一行使用 `all.equal()` 函数执行测试，以查看 `currentColumns` 和 `requiredColumns` 字符向量是否相等。`all.equal()` 函数用于比较两个 R 对象是否相等。如果它们相等，则返回 `TRUE`；否则，返回试图解释它们为何不相等的信息。在此示例中，您只关心 `currentColumns` 和 `requiredColumns` 变量是否相等。您只需要一个布尔响应。您可以通过使用 `isTRUE()` 函数包装 `all.equal()` 来获得它。

### 步骤 7：创建数据验证测试

以下是数据验证测试的框架：

```r
if (columnTest) {

} else {
plot.new()
title("提供的数据不符合图表的要求。")
}
```

回想一下，`columnTest` 变量基于一个返回布尔值的测试。如果 `columnTest` 变量的值为 `TRUE`，则将执行代码以构建可视化；否则，将执行 `else` 块中的代码。`else` 块中的代码使用 `plot.new()` 函数生成一个空白图，并向空白图添加文本，向报表用户解释为何未创建所需的图表。

### 步骤 8：通过初始化 ggplot 函数开始创建图表

以下是此步骤中使用的代码：

```r
chartdata <- dataset
ggplot(chartdata, aes(x = weight, y = height)) +
```

首先，您通过将 `dataset` 数据框分配给一个名为 `chartdata` 的变量来复制它。接下来，您定义 `ggplot()` 函数。第一个参数是可视化将使用的数据集。第二个参数使用 `aes()` 函数定义一些视觉美学。正在定义的美学是 `x` 和 `y` 坐标。

### 步骤 9：向 R 可视化对象添加散点图层

通过使用 `geom_point()` 几何对象添加散点图层。`x` 和 `y` 参数的值继承自 `ggplot()` 函数，并用于确定散点图中每个点的位置。以下是用于添加几何对象的代码：

```r
geom_point() +
```

将 `geom_point()` 几何对象添加到图表的结果如图 2-36 所示。

![../images/480677_1_En_2_Chapter/480677_1_En_2_Fig36_HTML.jpg](img/480677_1_En_2_Fig36_HTML.jpg)

图 2-36

使用基础 R 自带的“women”数据集绘制的美国女性平均体重与身高的散点图

### 步骤 10：向 R 可视化对象添加回归线图层

在此步骤中，您使用 `geom_smooth()` 几何对象添加回归线图层。用于添加该图层的代码如下：

```r
geom_smooth(method='lm') +
```

上述代码将根据散点图中的数据向图表添加一条线性回归线。此函数之所以特别，是因为它非常可配置。`geom_smooth()` 函数使您能够：

- 指定您自己的线性回归公式
- 为您的线性回归线添加置信区间
- 选择您要使用的置信区间水平
- 使用其他平滑方法，例如 `广义线性回归 (glm)`、`局部回归 (loess)`、`稳健线性模型 (rlm)` 和 `广义加性模型 (gam)`

以下图 2-37 中的可视化显示了添加回归线后图表的外观。

![../images/480677_1_En_2_Chapter/480677_1_En_2_Fig37_HTML.jpg](img/480677_1_En_2_Fig37_HTML.jpg)

图 2-37

添加了回归线的散点图

### 步骤 11：为图表添加标题

使用 `ggtitle()` 函数添加图表标题和副标题，如下所示：

```r
ggtitle(
"美国女性身高与平均体重",
subtitle = "(添加了回归线和置信区间)"
) +
```

请注意，`ggtitle()` 函数是 `labs()` 函数的替代方案，但它具有此示例所需的功能。

### 步骤 12：更改图表的主题

您希望使用 Stephen Few 的原则来更改图表的外观和感觉。您可以使用以下代码将 `theme_tufte()` 主题添加到图表中：

```r
theme_tufte() +
```

### 步骤 13：进行最后的清理工作

`theme()` 函数用于更改图表标题、副标题和轴标签的字体大小。图表标题的字体使用 `plot.title` 组件更改，图表的副标题使用 `plot.subtitle` 组件更改，轴标签使用 `axis.title` 组件更改。所有三个组件都使用 `element_text()` 函数进行更改：

```r
theme(
axis.title = element_text(size = 20),
plot.title = element_text(size = 30),
plot.subtitle = element_text(size = 20)
)
```

### 步骤 14：向 Power BI 添加代码

完整的脚本如代码清单 2-8 所示。复制该脚本并将其粘贴到 Power BI 的 *R 脚本编辑器*中。

```r
library(tidyverse)
library(ggthemes)
currentColumns <- sort(colnames(dataset))
requiredColumns <- c("height","weight")
columnTest <- isTRUE(all.equal(currentColumns, requiredColumns))
if (columnTest) {
chartdata <- dataset
ggplot(chartdata, aes(x = weight, y = height)) +
geom_point() +
geom_smooth(method='lm') +
ggtitle(
"美国女性身高对应的平均体重",
subtitle = "（附回归线与置信区间）"
) +
theme_tufte() +
theme(
axis.title = element_text(size = 20),
plot.title = element_text(size = 30),
plot.subtitle = element_text(size = 20)
)
} else {
plot.new()
title("提供的数据不符合图表要求。")
}
代码清单 2-8
生成带回归线的散点图的 R 脚本
```

在本章中，你学习了几个示例，展示了如何通过 `ggplot2` 包在 Power BI 中实现富有表现力的可视化。你可以轻松修改这些示例以满足自身需求，或将其作为灵感来源，创建完全不同的可视化效果。`ggplot2` 包及其辅助包提供了一个出色的框架，让你能够以相对低代码的方式为 Power BI 构建 R 可视化。`ggplot2` 包是 Power BI 的绝佳搭档！

