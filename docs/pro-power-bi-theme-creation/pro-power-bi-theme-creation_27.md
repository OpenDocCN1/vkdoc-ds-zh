# 表格

作为 Power BI 仪表板中最常用的视觉对象之一，你无疑需要了解如何使用 JSON 主题文件来格式化表格的细节（以及复杂性）。

首先，定义表格格式属性的关键关键字是 `tableEx`。

你可以使用主题文件进行格式化（超出标准视觉元素范围）的表格元素有：

- 样式
- 网格
- 列标题
- 值
- 总计
- 工具提示

这些元素在以下代码片段中以粗体显示。

表 6-11 概述了可用于表格视觉对象格式化的 JSON 关键字，以及每个关键字在 Power BI Desktop 格式窗格中对应的部分。

**表 6-11**  
**表格关键字**

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

描述网格部分的 `grid` 关键字使用表 6-12 中所示的关键字。

**表 6-12**  
**网格的关键字映射**

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

描述列标题部分的 `columnHeaders` 关键字使用表 6-13 中所示的关键字。

**表 6-13**  
**列标题的关键字映射**

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

描述值部分的 `values` 关键字使用表 6-14 中所示的关键字。

**表 6-14**  
**值的关键字映射**

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

描述总计部分的 `total` 关键字使用表 6-15 中所示的关键字。

**表 6-15**  
**总计的关键字映射**

| 元素 | 关键字 |
| --- | --- |
| 总计 | `totals` |
| 标签 | `label` |
| 字体颜色 | `fontColor` |
| 背景颜色 | `backColor` |
| 轮廓 | `outline` |
| 字体系列 | `fontFamily` |
| 文本大小 | `fontSize` |

JSON 中用于整体表格演示样式的 `stylePreset` 关键字需要表 6-16 中显示的属性之一。

**表 6-16**  
**表格样式选项**

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
| `Condensed` | 紧凑 |

工具提示选项在表 6-5 中进行了描述。

描述 `columnHeaders` 和 `values` 部分的 `outline` 关键字使用上一节表 6-10 中所示的关键字。

以下元素*无法*通过 JSON 主题进行配置：

- 字段格式化
- 条件格式



