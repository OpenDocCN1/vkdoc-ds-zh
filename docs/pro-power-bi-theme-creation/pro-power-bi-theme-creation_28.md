# 矩阵

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
| 行小计位置 | `rowSubTotalPosition` |
| 按行级别 | `perRowLevel` |
| 按列级别 | `perColumnLevel` |

工具提示选项见表 6-5 中的描述。

矩阵值元素与表 6-14 中所示的元素几乎相同——额外增加了表 6-21 中给出的元素关键字映射。

**表 6-21** 矩阵值的关键字映射

| 元素 | 关键字 |
| --- | --- |
| 带区行样式 | `bandedRowHeaders` |
| 在行上显示 | `valuesOnRow` |

小计 JSON 部分中的 `rowSubTotalPosition` 关键字需要表 6-22 中所示的属性之一。

**表 6-22** 小计位置选项

| 值 | 格式化弹出选项 |
| --- | --- |
| `Top` | 顶部 |
| `Bottom` | 底部 |

## 结论

本章深入介绍了如何使用主题文件定义视觉对象格式。具体来说，你了解了如何为卡片、多行卡片、表格和矩阵视觉对象预置格式的完整细节。你看到了它们如何共享许多共同元素，以及相似的视觉对象类型如何共享格式化方法。因此，无论你是格式化卡片还是矩阵，其基本原则都是相同的。

下一步是学习图表。正如你将看到的，许多图表类型也共享共同元素。然而，考虑到 Power BI 中可用的图表类型数量众多，我将用三章的篇幅来讨论这个主题。下一章将介绍核心图表类型。



