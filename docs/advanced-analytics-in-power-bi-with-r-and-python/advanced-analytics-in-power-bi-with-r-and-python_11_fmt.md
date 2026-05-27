# 使用标度控制美学映射

你之前了解到，数据到美学映射的描述是使用`aes()`函数完成的。然而，底层过程是由标度函数控制的。在本节中，你将了解这是如何发生的。

首先介绍标度的背景。图表中的每个美学映射（x 坐标、y 坐标、颜色等）都需要一个标度。它决定了数据在可视化中的“缩放”方式。你不需要显式定义它们。如果你不定义它们，`ggplot2`将使用默认值。让我们以清单 1-19 中的代码片段为例。

```
ggplot(plot.data, aes(x = carat, y = price)) +
  geom_point(aes(color = clarity))
```

Listing 1-19: ggplot2 code minus the underlying helper code

在此示例中，没有显式定义任何标度。上述代码会被`ggplot2`转换为以下清单 1-20 中的代码。

```
ggplot(plot.data, aes(x = carat, y = price)) +
  geom_point(aes(color = clarity)) +
  scale_x_continuous() +
  scale_y_continuous() +
  scale_color_discrete()
```

Listing 1-20: ggplot2 code with helper code that defines the scales

`ggplot2`为这三个标度中的每一个都使用了默认值。

敏锐的读者可能已经注意到标度命名约定中的模式。标度函数使用三部分命名约定。标度函数以单词“scale”开头，后跟它所属的美学映射名称，最后以标度类型结尾。这三部分用下划线分隔。

因此，如果你使用刚刚概述的命名约定查看上述脚本中的前两个标度，你会注意到它们分别用于 x 和 y 美学映射。它们都使用`continuous`标度类型，因为映射到 x 和 y 美学映射的数据是连续数字。最后一个标度用于`color`美学映射。映射到此美学映射的数据是`clarity`字段。该字段包含一系列离散值，这就是标度类型为`discrete`的原因。

如果你对`ggplot2`生成的结果满意，则无需定义标度。你可以使用默认值。但如果你想覆盖`ggplot2`生成的结果，则需要使用适当的标度来实现。

回想一下清单 1-9 中生成图 1-8 的以下代码：

```
library(tidyverse)
library(scales)
plot.data <- diamonds %>%
  filter(color == "D") %>%
  sample_n(200)
ggplot(plot.data, aes(x = carat, y = price)) +
  geom_point(aes(color = clarity)) +
  labs(
    title = "Chart Title",
    subtitle = "Chart Subtitle",
    caption = "Chart Caption"
  ) +
  scale_x_continuous(name = "Size in Carats") +
  scale_y_continuous(
    name = "Price ($)",
    labels = dollar_format()
  )
```

请注意，使用`scale_x_continuous()`更改了 x 美学映射的名称，并使用`scale_y_continuous()`更改了 y 美学映射的标签格式和名称。你将在下一章中看到更多使用标度修改美学映射的示例。