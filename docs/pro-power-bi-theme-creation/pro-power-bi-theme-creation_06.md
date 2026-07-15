# 6. 卡片和表格视觉样式

为了扩展您对 JSON 格式的了解，现在可以继续学习一些核心的数据驱动视觉对象。一组有用的视觉对象包括：

*   卡片
*   多行卡片
*   表格
*   矩阵

我按特定顺序处理这组视觉对象，因为我倾向于从最简单的开始，并在本章中逐步过渡到更复杂的视觉对象。这也有助于理解如何构建您的知识体系，因为 JSON 主题是通过重复使用（或大体相似）的元素来定义的。

本章讨论的四个视觉对象在样例文件夹中各有一个独立的 JSON 文件，其中包含了可用于定义您自己的主题文件的代码。

## 卡片

最简单的视觉对象之一（这意味着主题文件中的 JSON 也简洁得令人感到轻松）是卡片视觉对象。用于引入卡片格式属性的关键词是 `card`。

表 6-1 描述了与 Power BI Desktop 中每个格式设置区域相对应的 JSON 关键词。

**表 6-1** 卡片格式设置元素

| 关键词 | 格式设置区域 |
| --- | --- |
| `general` | 常规 |
| `labels` | 数据标签 |
| `categoryLabels` | 类别标签 |
| `wordwrap` | 自动换行 |
| `title` | 标题 |
| `background` | 背景 |
| `lockAspect` | 锁定纵横比 |
| `border` | 边框 |
| `dropShadow` | 阴影 |
| `visualTooltip` | 工具提示 |
| `visualHeader` | 视觉对象标头 |

以下代码片段为您提供了用于指定标准卡片格式的核心元素。样例文件 `Attributes_Card.json` 包含了可在主题文件中为卡片设置的所有元素。

```json
"card":
{"*":
{
"general": [{... 代码未显示 ...}]
"labels":   [{
"color": {"solid": {"color": "#12239E"}},
"labelDisplayUnits": 1000,
"labelPrecision": 2,
"fontSize": 36,
"fontFamily": "Arial",
"preserveWhitespace": false
}],
"categoryLabels":  [{
"show": true,
"color": {"solid": {"color": "#12239E"}},
"fontSize": 24,
"fontFamily": "Arial"
}],
"wordwrap": [{
"show": true
}],
"title": [{... 代码未显示 ...}],
"background": [{... 代码未显示 ...}],
"lockAspect": [{... 代码未显示 ...}],
"border": [{... 代码未显示 ...}],
"dropShadow": [{... 代码未显示 ...}],
"visualTooltip":[{
"show": true,
"titleFontColor":{"solid":{"color":"#E8E8E8"}},
"valueFontColor":{"solid":{"color":"#FFFFFF"}},
"fontSize": 11,
"fontFamily": "Arial Black",
"background":{"solid":{"color":"#938888"}},
"transparency": 30
}],
"visualHeader": [{... 代码未显示 ...}]
}
}
```

描述数据标签部分的 `labels` 关键词使用表 6-2 中所示的关键词。

**表 6-2** 数据标签的关键词映射

| 元素 | 关键词 |
| --- | --- |
| 颜色 | `color` |
| 显示单位 | `labelDisplayUnits` |
| 值的小数位数 | `labelPrecision` |
| 文本大小 | `fontSize` |
| 字体系列 | `fontFamily` |
| 源间距 | `preserveWhitespace` |

描述类别标签部分的 `categoryLabels` 关键词使用表 6-3 中所示的关键词。

**表 6-3** 类别标签的关键词映射

| 元素 | 关键词 |
| --- | --- |
| 类别标签 | `show` |
| 颜色 | `color` |
| 文本大小 | `fontSize` |
| 字体系列 | `fontFamily` |

描述自动换行部分的 `wordWrap` 关键词使用表 6-4 中所示的关键词。

**表 6-4** 自动换行的关键词映射

| 元素 | 关键词 |
| --- | --- |
| 自动换行 | `show` |

描述工具提示部分的 `visualTooltip` 关键词使用表 6-5 中所示的关键词。

**表 6-5** 工具提示的关键词映射

| 元素 | 关键词 |
| --- | --- |
| 工具提示 | `show` |
| 标签颜色 | `titleFontColor` |
| 值颜色 | `valueFontColor` |
| 文本大小 | `fontSize` |
| 字体系列 | `fontFamily` |
| 背景颜色 | `background` |
| 透明度 | `transparency` |

## 多行卡片

多行卡片在许多方面是卡片主题的扩展，但仍有足够多的不同之处，需要一组独立的格式设置元素。

用于指定多行卡片格式属性的关键词是 `multiRowCard`。

表 6-6 概述了可用于多行卡片格式设置的 JSON 关键词。

**表 6-6** 多行卡片格式设置元素

| 关键词 | 格式设置区域 |
| --- | --- |
| `General` | 常规 |
| `dataLabels` | 数据标签 |
| `categoryLabels` | 类别标签 |
| `cardTitle` | 卡片标题 |
| `Card` | 卡片 |
| `Title` | 标题 |
| `Background` | 背景 |
| `lockAspect` | 锁定纵横比 |
| `Border` | 边框 |
| `dropShadow` | 阴影 |
| `visualHeader` | 视觉对象标头 |

格式化多行卡片所需的完整 JSON 可在样例文件 `Attributes_MultiRowCard.json` 中找到。您可以在以下代码片段中看到此视觉对象类型特有的元素：

```json
"multiRowCard":
{"*":
{
"general": [{... 代码未显示 ...}]
"dataLabels": [{
"color": {"solid": {"color": "#12239E"}},
"fontSize": 10,
"fontFamily": "Arial"
}],
"categoryLabels":   [{
"show": true,
"color": {"solid": {"color": "#12239E"}},
"fontSize": 20,
"fontFamily": "Arial"
}],
"cardTitle":    [{
"fontColor": {"solid": {"color": "#ffffff"}},
"fontSize": 16,
"fontFamily": "Arial"
}],
"card":   [{
"outline": "BottomOnly",
"outlineColor": {"solid": {"color": "#12239E"}},
"outlineWeight": 3,
"barShow": true,
"barColor": {"solid": {"color": "#12239E"}},
"barWeight": 5,
"cardPadding": 15,
"cardBackground": {"solid": {"color": "#F5F4F0"}}
}],
"title": [{... 代码未显示 ...}],
"background": [{... 代码未显示 ...}],
"lockAspect": [{... 代码未显示 ...}],
"border": [{... 代码未显示 ...}],
"dropShadow": [{... 代码未显示 ...}],
"visualHeader": [{... 代码未显示 ...}]
}
}
```

描述数据标签部分的 `dataLabels` 关键词使用表 6-7 中所示的关键词。

**表 6-7** 数据标签的关键词映射

| 元素 | 关键词 |
| --- | --- |
| 颜色 | `color` |
| 文本大小 | `fontSize` |
| 字体系列 | `fontFamily` |

多行卡片的类别标签部分可在表 6-3 中找到。

描述卡片标题部分的`cardTitle`关键词使用表6-8中所示的关键词。

**表6-8** 卡片标题的关键词映射

| 元素 | 关键词 |
| --- | --- |
| 颜色 | `fontcolor` |
| 文本大小 | `fontSize` |
| 字体系列 | `fontFamily` |

描述卡片部分的`card`关键词使用表6-9中所示的关键词。

**表6-9** 卡片元素的关键词映射

| 元素 | 关键词 |
| --- | --- |
| 轮廓 | `outline` |
| 轮廓颜色 | `outlineColor` |
| 轮廓粗细 | `outlineWeight` |
| 显示条 | `barShow` |
| 条颜色 | `barColor` |
| 条粗细 | `barWeight` |
| 内边距 | `cardPadding` |
| 背景 | `cardBackground` |

卡片部分中JSON使用的`outline`关键词需要表6-10中所示的一个属性。

**表6-10** 卡片轮廓样式选项

| 值 | 格式设置弹出选项 |
| --- | --- |
| `None` | 无 |
| `BottomOnly` | 仅底部 |
| `TopOnly` | 仅顶部 |
| `LeftOnly` | 仅左侧 |
| `RightOnly` | 仅右侧 |
| `TopBottom` | 顶部 + 底部 |
| `LeftRight` | 左侧 + 右侧 |
| `Frame` | 框架 |

**注意** 再次强调，格式的拼写必须与前面表格中给出的完全一致。如果您添加了空格或不遵循大小写规则，则表格样式将不会生效，而会应用默认值。

# 表格

作为 Power BI 仪表板中最常用的视觉对象之一，你无疑需要了解如何使用 JSON 主题文件来格式化表格的细节（以及复杂性）。

首先，定义表格格式属性的关键关键字是 `tableEx`。

你可以使用主题文件进行格式化（超出标准视觉对象范围）的表格元素有：样式、网格、列标题、值、总计、工具提示。

表 6-11 概述了可用于表格视觉对象格式化的 JSON 关键字，以及每个关键字在 Power BI Desktop 格式窗格中对应的部分。

### 表 6-11 表格关键字

| 关键字 | 格式弹出选项 |
| --- | --- |
| `general` | 常规 |
| `stylePreset` | 样式 |
| `grid` | 网格 |
| `columnHeaders` | 列标题 |
| `values` | 值 |
| `total` | 总计 |
| `visualTooltip` | 工具提示 |
| `title` | 标题 |
| `lockAspect` | 锁定纵横比 |
| `border` | 边框 |
| `dropShadow` | 阴影 |
| `visualHeader` | 视觉对象标头 |

一个完整的主题文件（其中还包含标准方面的格式化设置，例如背景、标题、锁定纵横比、边框、阴影和视觉对象标头）可在示例数据文件中找到，文件名为 `Attributes_Table.json`。

如下所示，这是一段较长的 JSON 代码。毕竟，它必须定义更多的样式选项。但是，它遵循了你迄今在较小 JSON 片段中看到的标准，因此应该是可以理解的。我发现，诀窍在于逐节考虑，而不是试图一次性全部理解。

以下代码片段提供了用于预格式化表格的 JSON 代码：

```
"tableEx":
{"*":
{
"general": [{... code not shown ...}]
"stylePreset":  [{"name": "ContrastAlternatingRows"}],
"grid":    [{
"gridVertical": false,
"gridVerticalColor": {"solid": {"color": "#F5F4F0"}},
"gridVerticalWeight": 1,
"gridHorizontal": false,
"gridHorizontalColor": {"solid": {"color": "#F5F4F0"}},
"gridHorizontalWeight": 1,
"rowPadding": 1,
"outlineColor": {"solid": {"color": "#ffffff"}},
"outlineWeight": 1,
"textSize": 10,
"imageHeight": 60
}],
"columnHeaders": [{
"fontColor": {"solid": {"color": "#ffffff"}},
"backColor": {"solid": {"color": "#118Dff"}},
"outline": "TopBottom",
"autoSizeColumnWidth": true,
"fontFace": "Arial",
"fontSize": 8,
"alignment": "Left",
"wordWrap": true
}],
"values": [{
"fontColorPrimary": {"solid": {"color": "#949494"}},
"backColorPrimary": {"solid": {"color": "#E8E8E8"}},
"fontColorSecondary": {"solid": {"color": "#808080"}},
"backColorSecondary": {"solid": {"color": "#D5D5D5"}},
"outline": "Frame",
"urlIcon": true,
"wordWrap": true,
"fontFamily": "Arial",
"fontSize": 8
}],
"total":    [{
"totals": true,
"label": "TOTAL",
"fontColor": {"solid": {"color": "#000000"}},
"backColor": {"solid": {"color": "#118DFF"}},
"outline": "Frame",
"fontFamily": "Arial",
"fontSize": 10
}],
"title": [{... code not shown ...}],
"background": [{... code not shown ...}],
"lockAspect": [{... code not shown ...}],
"border": [{... code not shown ...}],
"dropShadow": [{... code not shown ...}],
"visualTooltip": [{... code not shown ...}],
"visualHeader": [{... code not shown ...}]
}
}
```

## 网格

描述网格部分的 `grid` 关键字使用表 6-12 中所示的关键字。

### 表 6-12 网格的关键字映射

| 元素 | 关键字 |
| --- | --- |
| 垂直网格 | `gridVertical` |
| 垂直网格颜色 | `gridVerticalColor` |
| 垂直网格粗细 | `gridVerticalWeight` |
| 水平网格 | `gridHorizontal` |
| 水平网格颜色 | `gridHorizontalColor` |
| 水平网格粗细 | `gridHorizontalWeight` |
| 行内边距 | `rowPadding` |
| 轮廓颜色 | `outlineColor` |
| 轮廓粗细 | `outlineWeight` |
| 文本大小 | `textSize` |
| 图像高度 | `imageHeight` |

## 列标题

描述列标题部分的 `columnHeaders` 关键字使用表 6-13 中所示的关键字。

### 表 6-13 列标题的关键字映射

| 元素 | 关键字 |
| --- | --- |
| 字体颜色 | `fontColor` |
| 背景颜色 | `backColor` |
| 轮廓 | `outline` |
| 自动调整列宽 | `autoSizeColumnWidth` |
| 字体系列 | `fontFace` |
| 文本大小 | `fontSize` |
| 对齐方式 | `alignment` |
| 自动换行 | `wordWrap` |

## 值

描述值部分的 `values` 关键字使用表 6-14 中所示的关键字。

### 表 6-14 值的关键字映射

| 元素 | 关键字 |
| --- | --- |
| 字体颜色 | `fontColorPrimary` |
| 背景颜色 | `backColorPrimary` |
| 交替字体颜色 | `fontColorSecondary` |
| 交替背景颜色 | `backColorSecondary` |
| 轮廓 | `outline` |
| URL 图标 | `urlIcon` |
| 自动换行 | `wordWrap` |
| 字体系列 | `fontFamily` |
| 文本大小 | `fontSize` |

## 总计

描述总计部分的 `total` 关键字使用表 6-15 中所示的关键字。

### 表 6-15 总计的关键字映射

| 元素 | 关键字 |
| --- | --- |
| 总计 | `totals` |
| 标签 | `label` |
| 字体颜色 | `fontColor` |
| 背景颜色 | `backColor` |
| 轮廓 | `outline` |
| 字体系列 | `fontFamily` |
| 文本大小 | `fontSize` |

## 样式预设

JSON 中用于整体表格演示样式的 `stylePreset` 关键字需要表 6-16 中显示的属性之一。

### 表 6-16 表格样式选项

| 值 | 格式弹出选项 |
| --- | --- |
| `Default` | 默认 |
| `None` | 无 |
| `Minimal` | 极简 |
| `BoldHeader` | 粗体标头 |
| `AlternatingRows` | 交替行 |
| `ContrastAlternatingRows` | 对比交替行 |
| `FlashyRows` | 鲜明行 |
| `BoldHeaderFlashyRows` | 粗体标头鲜明行 |

| `Sparse` | 稀疏 |
| --- | --- |
| `Condensed` | 紧凑 |

工具提示选项在表 6-5 中进行了描述。

描述 `columnHeaders` 和 `values` 部分的 `outline` 关键字使用上一节表 6-10 中所示的关键字。

以下元素无法通过 `JSON` 主题进行配置：

- 字段格式化
- 条件格式

# 矩阵

## 概述

不出所料，定义矩阵格式的 JSON 与表格格式的 JSON 在很大程度上相似——实际上是基于后者构建并进行了扩展。

与表格 JSON 中相同的元素有：

- `Style`
- `Grid`

其他元素（例如值和列标题）则略有不同。如果你查看这些元素的完整 JSON，可以对比这两个代码片段。小计和总计元素的差异足够大，值得详细研究。

引入矩阵格式属性的关键字是 `pivotTable`。

格式化矩阵时使用的关键字及它们在 Power BI Desktop 格式窗格中对应的部分列于表 6-17。

**表 6-17** 矩阵关键字

| 关键字 | 格式化弹出选项 |
| --- | --- |
| `general` | 常规 |
| `stylePreset` | 样式 |
| `grid` | 网格 |
| `columnHeaders` | 列标题 |
| `rowHeaders` | 行标题 |
| `values` | 值 |
| `subTotals` | 小计 |
| `total` | 总计 |
| `title` | 标题 |
| `lockAspect` | 锁定纵横比 |
| `border` | 边框 |
| `dropShadow` | 阴影 |
| `visualTooltip` | 工具提示 |
| `visualHeader` | 视觉对象标头 |

## 代码示例与关键字参考

以下代码片段展示了如何在 JSON 文件中格式化矩阵。其中包含所有可预设元素的完整 JSON 代码可在示例文件 `Attributes_Matrix.json` 中找到。

```
"pivotTable":
{"*" :
{
"general": [{... 代码未显示 ...}]
"stylePreset":  [{"name": "ContrastAlternatingRows"}],
"grid":  [{
"gridVertical": false,
"gridVerticalColor": {"solid": {"color": "#E8E8E8"}},
"gridVerticalWeight": 1,
"gridHorizontal": false,
"gridHorizontalColor": {"solid": {"color": "#E8E8E8"}},
"gridHorizontalWeight": 1,
"rowPadding": 1,
"outlineColor": {"solid": {"color": "#ffffff"}},
"outlineWeight": 1,
"textSize": 10,
"imageHeight": 60
}],
"columnHeaders": [{
"fontColor": {"solid": {"color": "#ffffff"}},
"backColor": {"solid": {"color": "#12239E"}},
"outline": "TopBottom",
"autoSizeColumnWidth": true,
"fontFace": "Arial",
"fontSize": 8,
"alignment": "Left",
"titleAlignment": "Center",
"wordWrap": true
}],
"rowHeaders": [{
"fontColor": {"solid": {"color": "#ffffff"}},
"backColor": {"solid": {"color": "#118DFF"}},
"outline": "TopBottom",
"stepped": true,
"steppedLayoutIndentation": 14,
"urlIcon": true,
"wordWrap": true,
"fontFamily": "Arial",
"fontSize": 8,
"titleAlignment": "Center",
"showExpandCollapseButtons": true,
"expandCollapseButtonsColor": {"solid": {"color": "#ffffff"}},
"expandCollapseButtonsSize": 12
}],
"values": [{
"fontColorPrimary": {"solid": {"color": "#3D4E57"}},
"backColorPrimary": {"solid": {"color": "#F5F4F0"}},
"fontColorSecondary": {"solid": {"color": "#3D4E57"}},
"backColorSecondary": {"solid": {"color": "#E3DFD4"}},
"bandedRowHeaders": true,
"valuesOnRow": false,
"outline": "Frame",
"urlIcon": true,
"wordWrap": true,
"fontFamily": "Arial",
"fontSize": 8
}],
"subTotals": [{
"rowSubTotals": true,
"rowSubtotalsLabel": "Total",
"columnSubTotals": true,
"columnSubtotalsLabel": "Total",
"fontColor": {"solid": {"color": "#FFFFFF"}},
"fontFace": "Arial",
"backColor": {"solid": {"color": "#B7B7B7"}},
"fontSize": 8,
"applyToHeaders": false,
"rowSubTotalPosition": "Bottom",
"perRowLevel": false,
"perColumnLevel": false
}],
"total": [{
"totals": true,
"fontColor": {"solid": {"color": "#3D4E57"}},
"fontFamily": "Arial",
"backColor": {"solid": {"color": "#D1CAB8"}},
"applyToHeaders": true,
"fontSize": 11
}],
"title": [{... 代码未显示 ...}],
"background": [{... 代码未显示 ...}],
"lockAspect": [{... 代码未显示 ...}],
"border": [{... 代码未显示 ...}],
"dropShadow": [{... 代码未显示 ...}],
"visualTooltip": [{... 代码未显示 ...}],
"visualHeader": [{... 代码未显示 ...}]
}
}
```

矩阵网格部分的关键字如表 6-12 所示。

矩阵列标题元素与表 6-13 中所示的元素几乎相同——额外增加了表 6-18 中给出的元素关键字映射。

**表 6-18** 列标题的关键字映射

| 元素 | 关键字 |
| --- | --- |
| 标题对齐方式 | `titleAlignment` |

描述行标题部分的 `rowHeaders` 关键字使用表 6-19 中所示的关键字。

**表 6-19** 行标题部分的元素关键字映射

| 元素 | 关键字 |
| --- | --- |
| 字体颜色 | `fontColor` |
| 背景颜色 | `backColor` |
| 大纲/轮廓 | `outline` |
| 阶梯布局 | `stepped` |
| 阶梯布局缩进 | `steppedLayoutIndentation` |
| URL 图标 | `urlIcon` |
| 自动换行 | `wordWrap` |
| 字体系列 | `fontFamily` |
| 字体大小 | `fontSize` |
| 对齐方式 | `titleAlignment` |
| +/– 图标 | `showExpandCollapseButtons` |
| 图标颜色 | `expandCollapseButtonsColor` |
| 图标大小 | `expandCollapseButtonsSize` |

描述小计部分的 `subTotals` 关键字使用表 6-20 中所示的关键字。

**表 6-20** 小计的关键字映射

| 元素 | 关键字 |
| --- | --- |
| 行小计 | `rowSubTotals` |
| 行小计标签 | `rowSubtotalsLabel` |
| 列小计 | `columnSubTotals` |
| 列小计标签 | `columnSubtotalsLabel` |
| 字体颜色 | `fontColor` |
| 字体系列 | `fontFace` |
| 背景颜色 | `backColor` |
| 文本大小 | `fontSize` |
| 应用于标签 | `applyToHeaders` |

# 第6章 视觉对象格式定义

| 行小计位置 | `rowSubTotalPosition` |
| --- | --- |
| 按行级别 | `perRowLevel` |
| 按列级别 | `perColumnLevel` |

工具提示选项见表 6-5 中的描述。

矩阵值元素与表 6-14 中所示的元素几乎相同——额外增加了表 6-21 中给出的元素关键字映射。

### 表 6-21 矩阵值的关键字映射

| 元素 | 关键字 |
| --- | --- |
| 带区行样式 | `bandedRowHeaders` |
| 在行上显示 | `valuesOnRow` |

小计 JSON 部分中的 `rowSubTotalPosition` 关键字需要表 6-22 中所示的属性之一。

### 表 6-22 小计位置选项

| 值 | 格式化弹出选项 |
| --- | --- |
| `Top` | 顶部 |
| `Bottom` | 底部 |

## 结论

本章深入介绍了如何使用主题文件定义视觉对象格式。具体来说，你了解了如何为卡片、多行卡片、表格和矩阵视觉对象预置格式的完整细节。你看到了它们如何共享许多共同元素，以及相似的视觉对象类型如何共享格式化方法。因此，无论你是格式化卡片还是矩阵，其基本原则都是相同的。

下一步是学习图表。正如你将看到的，许多图表类型也共享共同元素。然而，考虑到 Power BI 中可用的图表类型数量众多，我将用三章的篇幅来讨论这个主题。下一章将介绍核心图表类型。

