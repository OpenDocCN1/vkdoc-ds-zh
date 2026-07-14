# 7. 数据可视化

对 RStudio 用户来说，最有吸引力的元素之一是可用的丰富数据可视化工具库。在本章中，我们将重点介绍最常使用的图形工具，这些工具将满足您大部分日常工作。当然，在 R 生态系统中，我们还有数以百计的其他库和专用包可用于可视化。

现在，我们将首先关注那些能立即对您产生最大影响的工具，从 `ggplot2`（一个`tidyverse`包）开始。然后，我们将继续介绍其他一些包，这些包能让您对可视化有更多控制，但代价是需要您投入更多精力来使用。

## `ggplot2`

`ggplot2` 是`tidyverse`中进行基本绘图的方式。它替换并增强了我们在前面章节中已经看到的基础 R 的数据绘图方法。正如您从上一章我们对 `dplyr` 的讨论中所期望的那样，`ggplot2` 提供了一种非常独特的数据可视化方式。您将使用 `ggplot2` 进行日常数据探索，也用于在可接受静态图形的报告中进行数据展示。

让我们看看如何使用 `ggplot2`。首先，与其他`tidyverse`包一样，我们需要使用 `library` 函数来加载`tidyverse`包 `library(tidyverse)`。

```r
library(tidyverse)

source("process_data.R")
```

在加载了 `tidyverse` 之后，在第二行代码中，我们使用 `source` 命令重新运行保存在 "process_data.R" 文件中的所有代码。这确保了我们就预期寿命数据集的结构达成了共识。

要使用 `ggplot2`，我们可以通过管道将一个数据集传递给 `ggplot` 函数。这个函数需要我们指定要绘制的变量。

```r
expectancy %>%
  filter(race == "All Races",
         sex == "Both Sexes") %>%
  ggplot(aes(year, life_expectancy))
```

当你运行这段代码时，你会看到一个空白画布，X 轴和 Y 轴已定义好，如图 7-1 所示。

![../images/478254_1_En_7_Chapter/478254_1_En_7_Fig1_HTML.jpg](img/478254_1_En_7_Fig1_HTML.jpg)

图 7-1 空白 `ggplot` 画布

图 7-1 显示了一个画布，X 轴为年份，Y 轴为死亡率。现在我们已经定义了绘图的范畴，可以开始添加视觉元素来展示数据了。通过将 `ggplot` 函数通过管道传递给一组描述数据故事的“动词”来实现。

```r
expectancy %>%
  filter(race == "All Races",
         sex == "Both Sexes") %>%
  ggplot(aes(year, life_expectancy)) +
  geom_line()
```

我们使用了 `geom_line` 动词来声明我们想用一条线来表示数据。与您可能期望的使用 `magrittr` 管道操作符 `%>%` 不同，`ggplot2` 使用加号 `+` 来进行管道操作。请参见图 7-2 查看此折线图的示例。

![../images/478254_1_En_7_Chapter/478254_1_En_7_Fig2_HTML.jpg](img/478254_1_En_7_Fig2_HTML.jpg)

图 7-2 按年份的死亡率折线图

图 7-2 中的图显示了一个有趣的趋势以及一个可能的异常值，这个异常值可能需要在以后进行解释。您可以通过管道操作符 `+` 继续添加可视化图层。如果您想在线条上添加点，可以使用 `geom_point` 动词。

```r
expectancy %>%
  filter(race == "All Races",
         sex == "Both Sexes") %>%
  ggplot(aes(year, life_expectancy)) +
  geom_line() +
  geom_point()
```

这将生成图 7-3 所示的图形。

![../images/478254_1_En_7_Chapter/478254_1_En_7_Fig3_HTML.jpg](img/478254_1_En_7_Fig3_HTML.jpg)

图 7-3 向折线图添加点标记

如果我们想按性别细分查看这一趋势呢？要在 `ggplot` 折线图中查看这样的子组，您只需向您想要分类的 `geom_line` 动词添加一个新的美学参数。

```r
expectancy %>%
  filter(race == "All Races") %>%
  ggplot(aes(year, life_expectancy)) +
  geom_line(aes(color = sex))
```

在上面的代码中，你还可以看到我们使用了`dplyr`动词`select`来以更令人愉悦的方式排列和重命名标签。你可以在图 6-2 中看到结果。

![../images/478254_1_En_6_Chapter/478254_1_En_6_Fig2_HTML.jpg](img/478254_1_En_6_Fig2_HTML.jpg)

图 6-2 应用于`tibble`的性别标签

当你运行上面的代码时，你会看到结果以`tibble`的形式返回。你可以使用赋值操作符`<-`存储结果，以便稍后使用，而不是在控制台中显示结果。

```r
avg_death_rates_by_gender <-
  expectancy %>%
  filter(race == "All Races") %>%
  group_by(sex) %>%
  summarize(avg = mean(death_rate)) %>%
  inner_join(labels, by = c("sex" = "key")) %>%
  mutate(avg = round(avg, 1)) %>%
  select(gender = new_label,
         avg_death_rate = avg)
```

### stringr

我们将详细研究的最后一个`tidyverse`包将帮助你处理字符串。处理字符串很棘手，因为这种数据类型可能比简单的数字更复杂。`stringr`包含用于检测模式、分割字符串和连接字符串的函数。

你可以单独使用`stringr`函数，也可以与`dplyr`动词（如`filter`和`mutate`）一起使用。例如，要过滤在`race`列中包含字符模式“All”的记录，你可以在`filter`动词中使用`str_detect`，如下所示：

```r
expectancy %>%
  filter(str_detect(race, "All"))
```

我们可以使用`str_replace`将一个字符模式替换为另一个。要将`race`列中的条目“All Races”更改为“Most Races”，你可以这样做：

```r
expectancy %>%
  mutate(race = str_replace(race, "All", "Most"))
```

有许多`stringr`函数可以帮助你处理字符串。其中许多只是与基础 R 函数一起工作的包装器。`stringr`的价值主要在于它提供了一致的接口。每个函数都会将输入字符串作为第一个参数，这样它们就可以与`magrittr`管道操作符一起工作。

## 结论

`tidyverse` 集合中还有许多其他包，但上面介绍的这些包将为您提供使用任何`tidyverse`包所需的知识。这些也是您最有可能日常使用的包，并且它们所涉及的概念对基础 R 的扩展程度足以让我们认为它们值得更详细的介绍。不过，让我们也花点时间提一下其他一些包，以便您了解还有哪些其他工具可用。

`ggplot2` 是一个`tidyverse`包，它真正为 R 语言中的数据可视化设定了标准。这个包将在数据可视化章节中得到详细讲解。`tidyr` 帮助将数据转换为整洁的`tibble`数据集，便于添加到您的分析流程中。`readr`、`readxl` 和 `haven` 帮助您导入文本数据、Excel 数据以及 SAS/Stata/SPSS 数据。`lubridate` 帮助您处理棘手的日期和日期时间。

请注意，我们在该可视化的第一部分不再过滤“Both Sexes”，因为我们希望查看所有三个组。此代码将生成图 7-4 所示的图形。

![../images/478254_1_En_7_Chapter/478254_1_En_7_Fig4_HTML.jpg](img/478254_1_En_7_Fig4_HTML.jpg)

图 7-4

在`ggplot`中绘制子组

此图向我们展示了有所数据的两个性别之间的关系。线条显示为彩色，因为我们在`geom_line`中指定了颜色美学参数。

关于`ggplot`的内容远不止本章所能涵盖的。您有大量选项来显示不同类型的数据，以及标签和标题选项。这个包基本上是 RStudio 进行可视化的主力工具。请访问`ggplot2`的官方网站以获取更多关于如何使用此包的信息：[`https://ggplot2.tidyverse.org`](https://ggplot2.tidyverse.org)。

### htmlwidgets

虽然`ggplot2`提供了您进行基本绘图所需的大部分功能，但有时您可能希望为分析添加交互性或非常专业的图形。`htmlwidgets`是一系列软件包的集合，您可以使用它们将这种更高层次的可视化功能添加到您的 R 分析中。

`htmlwidgets`使您能够集成原本为网站开发的开源 Javascript 小组件。请访问 [`http://htmlwidgets.org`](http://htmlwidgets.org) 网站，获取所有可用小组件的完整列表。请注意，许多小组件对于您试图可视化的问题类型非常具体。您将找到用于地图、时间序列、网络图以及一些 D3 图形的小组件。

我们可以使用一个`htmlwidget` —— `plotly`，将我们在上一节中创建的图形转换为一个可用于网页的交互式小组件。`Plotly`的有趣之处在于它与`ggplot2`协同工作，并能真正将`ggplot2`提升到一个新的水平。以下是您为`ggplot2`图形添加交互性所需做的全部工作，使用`plotly`。

```
library(plotly)
i_plot <- expectancy %>%
  filter(race == "All Races") %>%
  ggplot(aes(year, life_expectancy)) +
  geom_line(aes(color = sex))
ggplotly(i_plot)
```

这段代码与我们使用`ggplot2`时所做的非常相似。然而，这次我们将结果赋值给一个对象，并使用该对象作为`ggplotly`的参数，来创建图 7-5 中所示的交互式图形。

![../images/478254_1_En_7_Chapter/478254_1_En_7_Fig5_HTML.jpg](img/478254_1_En_7_Fig5_HTML.jpg)

图 7-5 带有弹出窗口的 Plotly HTML 小组件

现在，这个图形包含了详细信息的弹出窗口以及顶部带有导航图标的工具栏。在后面的章节中，当我们学习如何创建连贯的报告和仪表板时，我们将能包含像这样的小组件，为用户创造丰富的交互式体验。

在我们继续之前，让我们重点介绍 DT 包的数据表格组件。数据表格是一个非常有用的交互式 html 小组件，它将为您的数据集创建一个可搜索的 HTML 展示。例如，如果我们想直接向用户提供一个可搜索版本的数据集，我们可以简单地这样做：

```
library(DT)
datatable(expectancy,
          options = list(pageLength = 10))
```

这将创建图 7-6 所示的小组件，它是一个填充了我们的数据的可搜索交互式表格。

![../images/478254_1_En_7_Chapter/478254_1_En_7_Fig6_HTML.jpg](img/478254_1_En_7_Fig6_HTML.jpg)

图 7-6 数据表格小组件

### r2d3

本章前两个部分讨论的软件包都提供了丰富的对象来可视化大多数类型的数据。然而，最终我们的图形看起来会像其他人设计的、所有 R 开发者都能使用的图形。

在某些用例中，我们希望绝对控制可视化的每一个方面。要么是因为没有现成的小组件能帮到我们，要么是我们需要做一些高度定制化的工作。这就是 D3 可以发挥作用的地方，而`r2d3`是一个我们可以用来将 D3 整合到 RStudio 项目中的软件包。

与`htmlwidgets`一样，D3 是一个基于网页的 Javascript 解决方案。然而，使用 D3 比我们使用`htmlwidgets`要复杂得多。D3 本质上是一种编程语言。

D3 的核心思想是，您可以将数据集与图形原语（如线条和圆形）绑定。这些图形将根据数据集中的值而增大或缩小。D3 图形可以变得非常复杂，因为您可以对其外观进行大量控制。

虽然教授 D3 远远超出了本书的范围，但我们可以向您展示一个快速入门的方法。

#### 注意

此处讨论的功能需要 RStudio 1.2，该版本应在您下载的当前 RStudio 版本中可用。但是，本章使用的版本来自 RStudio 的预览版，因此屏幕截图可能看起来略有不同。

要使用 D3，只需转到 RStudio 菜单，然后选择“File”、“New File”，再选择“D3 Script”。一个包含 D3 代码的文件将会出现。

```
// !preview r2d3 data=c(0.3, 0.6, 0.8, 0.95, 0.40)
//
// r2d3: https://rstudio.github.io/r2d3
//
var barHeight = Math.ceil(height / data.length);
svg.selectAll('rect')
  .data(data)
  .enter().append('rect')
  .attr('width', function(d) { return d * width; })
  .attr('height', barHeight)
  .attr('y', function(d, i) { return i * barHeight; })
  .attr('fill', 'steelblue');
```

此文件中的代码是 Javascript，您可以通过修改此代码来调整可视化效果。保存此文件并点击“Preview”按钮来查看 D3 可视化的效果。与我们前面强调的预打包解决方案相比，它看起来并不起眼，但这没关系，因为 D3 实际上更像是提供了一块供您绘制的画布，而不是一组绘图小组件。

预览功能仅使用了文件注释中的代码，但如果您更希望从 R 脚本文件中提供自己的数据，则可以使用`r2d3`库。

```
library(r2d3)
r2d3(data = c(.1,.2,.3,.4,.3,.2,.1),
     script = "d3_plot.js")
```

这段代码将生成图 7-7 中的图形。

![../images/478254_1_En_7_Chapter/478254_1_En_7_Fig7_HTML.jpg](img/478254_1_En_7_Fig7_HTML.jpg)

图 7-7 基本的 D3 图形

显然，这是一个非常基础的可视化入门。请查看`r2d3`网站，了解您可以用 D3 完成的一整套令人惊叹的可视化效果 [`https://rstudio.github.io/r2d3/articles/gallery.html`](https://rstudio.github.io/r2d3/articles/gallery.html)。这些示例还包含了重现它们所需的代码，以便您可以将其作为自己定制 D3 可视化的起点。

### 结论

除了基础的 R 语言`plot`函数之外，您在 R 中进行数据可视化还有许多选择。`ggplot2`提供了一种非常强大且风格化的方式来满足大多数分析师大部分时间内的需求。像`r2d3`和`htmlwidgets`这样的软件包则为更专业化的用例提供支持。还有更多专门的可视化选项可用，所有这些功能都可以在我们将在后续章节讨论的仪表板和报告选项中加以利用。





