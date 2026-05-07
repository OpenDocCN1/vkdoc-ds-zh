# ggplot2 内置主题

`ggplot2` 自带一系列主题，可轻松更改图表中非数据元素的显示效果。以下是该软件包作者 Hadley Wickham 描述的可用的主题列表：^(²)

* `theme_bw()`：`theme_gray()` 的变体，使用白色背景和细灰色网格线
* `theme_linedraw()`：一种仅在白色背景上使用各种宽度的黑色线条的主题，类似于线条画
* `theme_light()`：与 `theme_linedraw()` 类似，但使用浅灰色线条和坐标轴，以便将更多注意力引向数据
* `theme_dark()`：`theme_light()` 的深色版本，线条粗细相似，但背景为深色。有助于突出细彩色线条
* `theme_minimal()`：一种极简主题，无背景注释
* `theme_classic()`：经典外观主题，带有 x 轴和 y 轴线条，无网格线
* `theme_void()`：完全空白的主题

## 在 Power BI 服务中使用 R 可视化效果

当您将 R 可视化效果部署到 Power BI 服务时，必须确保使用的是该服务中可用的软件包版本。以下 URL 包含服务器上可用软件包及其版本的列表：[`https://docs.microsoft.com/en-us/power-bi/connect-data/service-r-packages-support`](https://docs.microsoft.com/en-us/power-bi/connect-data/service-r-packages-support)。

## ggplot2 的辅助包

`ggplot2` 是一个功能丰富的软件包，包含处理大多数数据可视化需求所需的工具。但有时您需要执行一些操作，例如在创建可视化之前整理 Power BI 传递给 R 的数据集，以基础 R 或 `ggplot2` 中不可用的方式格式化数据，或使用不属于 `ggplot2` 包的主题。

幸运的是，在开发 R 可视化效果时，您并不局限于 `ggplot2` 包。以下是一些在 Power BI 中与 `ggplot2` 完美互补的软件包。此列表并非详尽无遗：

* `tidyverse`：一个元包，包含一系列共享相同框架的流行 R 包。它包括 `ggplot` 和 `dplyr` 等包。`tidyverse` 包集合有助于在 R 中进行数据科学。有关 `tidyverse` 的更多信息，请访问此 URL：[`www.tidyverse.org/`](http://www.tidyverse.org/)。
* `ggthemes`：`ggplot2` 的辅助包，包含额外的比例尺、几何对象和主题。其中两个值得注意的主题是 `theme_few()` 和 `theme_tufte()`。这两个主题基于两位非常流行的数据可视化专家 Stephen Few 和 Edward Tufte 的最佳实践。有关 `ggthemes` 包的更多信息，请访问此 URL：[`https://cran.r-project.org/web/packages/ggthemes/ggthemes.pdf`](https://cran.r-project.org/web/packages/ggthemes/ggthemes.pdf)。
* `scales`：`scales` 包提供了 `ggplot2` 的内部缩放基础设施，以及许多用于数据转换和数据格式化的函数。您可以在以下 URL 找到有关 `scales` 包的更多信息：[`https://cran.r-project.org/web/packages/scales/scales.pdf`](https://cran.r-project.org/web/packages/scales/scales.pdf)。
* `ggrepel`：`ggrepel` 包提供了重新定位标签以最小化标签重叠的 `几何对象`。当您想要向散点图添加标签时，此包非常有用。您可以在以下 URL 找到有关 `ggrepel` 包的更多信息：[`https://cran.r-project.org/web/packages/ggthemes/ggthemes.pdf`](https://cran.r-project.org/web/packages/ggthemes/ggthemes.pdf)。

## 总结

在本章中，您了解了

* 向 Power BI 添加 R 可视化效果时使用的框架
* `ggplot2` 所基于的`图形分层语法`
* 如何使用几何对象向可视化效果添加图层
* 如何使用比例尺函数修改图表美学
* 如何使用完整主题和 `theme()` 函数修改图表的非数据元素

本章的目标是向您介绍一些关于 `ggplot2` 的重要概念，以便您更容易理解如何构建将在下一章学习的更高级图形。既然您已经了解了如何使用 `ggplot2` 在 Power BI 中创建 R 可视化效果的基础知识，那么让我们更进一步，开发能够帮助您在 Power BI 中讲述精彩数据故事的 R 可视化效果！

脚注 1 2

