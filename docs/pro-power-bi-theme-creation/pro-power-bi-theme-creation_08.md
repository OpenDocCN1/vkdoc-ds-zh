# 8. 复杂图表视觉样式

你将在本章学习的六种图表类型仅比上一章中的图表稍微复杂一些。然而，它们通常（大多数情况下）比之前看到的图表更详细。幸运的是，任何增加的复杂性都通过以下事实得到了补偿：你在这里看到的 JSON 总是基于并衍生自你在前一章中发现的 JSON。

可以使用 JSON 主题预设格式，本章中你将学习的图表类型有：

* 100% 堆叠条形图
* 100% 堆叠柱形图
* 堆叠面积图
* 折线和堆叠柱形图
* 折线和簇状柱形图
* 散点图

与以往一样，每种图表类型在样本文件夹中都有一个单独的 JSON 文件，其中包含可在你自己的主题文件中使用的代码。

# 百分比堆积柱形图

当你继续研究柱形图和条形图的不同变体时，会发现它们在 JSON 格式上的相似性远多于差异性。实际上，百分比堆积柱形图的格式与堆积柱形图的代码几乎完全一致。

与堆积柱形图 JSON 相比，仅存在以下差异：

*   用于指定百分比堆积柱形图格式化属性的关键字为 `hundredPercentStackedBarChart`。
*   缺少 `totals`（总计）部分。
*   数值轴不再包含 `axisScale` 元素。

表 8-1 概述了可在堆积柱形图格式化中使用的 JSON 关键字，以及它们在 Power BI Desktop 格式窗格中对应的部分。

**表 8-1** 堆积柱形图关键字

| 关键字 | 格式化部分 |
| --- | --- |
| `general` | 常规 |
| `legend` | 图例 |
| `categoryAxis` | X 轴 |
| `valueAxis` | Y 轴 |
| `zoom` | 缩放滑块 |
| `labels` | 数据标签 |
| `plotArea` | 绘图区 |
| `title` | 标题 |
| `background` | 背景 |
| `lockAspect` | 锁定纵横比 |
| `border` | 边框 |
| `dropShadow` | 阴影 |
| `visualTooltip` | 工具提示 |
| `visualHeader` | 视觉对象标头 |

百分比堆积柱形图的 JSON 代码可在示例文件 `Attributes_HundredPercentStackedBarChart.json` 中找到。

以下代码片段提供了用于预格式化百分比堆积柱形图的 JSON 代码：

```json
"hundredPercentStackedBarChart":
{"*":
{
"general": [{... 代码未显示 ...}]
"legend": [{... 代码未显示 ...}],
"categoryAxis": [{
"show": true,
"position": "Left",
"color": {"solid": {"color": "#949494"}},
"fontSize": 8,
"fontFamily": "Arial",
"preferredCategoryWidth": 20,
"maxMarginFactor": 25,
"innerPadding": 15,
"showAxisTitle": true,
"axisStyle": "showTitleOnly",
"titleColor": {"solid": {"color": "#949494"}},
"titleText": "添加轴标题",
"titleFontSize": 8,
"titleFontFamily": "Arial"
}],
"valueAxis": [{
"show": true,
"start": null,
"end": null,
"labelColor": {"solid": {"color": "#949494"}},
"fontSize": 8,
"fontFamily": "Arial",
"labelPrecision": 0,
"labelDisplayUnits": 0,
"showAxisTitle": true,
"axisStyle": "ShowTitleOnly",
"color": {"solid": {"color": "#949494"}},
"titleText": "添加轴标题",
"titleFontSize": 8,
"titleFontFamily": "Arial",
"gridLineShow": false,
"gridLineColor":  {"solid": {"color": "#949494"}},
"gridLineThickness": 1,
"gridlineStyle": "Solid"
}],
"zoom[{... 代码未显示 ...}],
"labels": [{
"show": true,
"color": {"solid": {"color": "#ffffff"}},
"labelDisplayUnits": 1,
"labelPrecision": 2,
"labelPosition": "InsideEnd",
"labelOverflow": true,
"fontSize": 7,
"fontFamily": "Arial",
"enableBackground": false,
"backgroundColor": {"solid": {"color": "#949494"}},
"backgroundTransparency": 0
}],
"plotArea": [{... 代码未显示 ...}],
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

图例关键字见表 7-2。

类别关键字见表 7-3。

值关键字见表 7-4。

缩放关键字见表 7-5。

标签关键字见表 7-6。

绘图区关键字见表 7-8。

工具提示关键字见表 6-5。

图例位置选项见表 7-9。

分类轴样式选项见表 7-16。

数值轴显示单位选项见表 7-11。

数值轴样式选项见表 7-16。

数值轴网格线样式选项见表 7-13。

标签位置选项见表 7-12。

分类轴位置选项见表 7-10。

# 100% 堆叠柱状图

百分百堆叠柱状图的格式与你用于堆叠柱状图的代码几乎相同。主要区别在于：
- 指定百分百堆叠柱状图格式化属性的关键词是 `hundredPercentStackedColumnChart`。
- 没有 `totals` 部分。
- 值轴中没有 `axisScale`。

表 8-2 概述了可在堆叠柱状图格式中使用的 JSON 关键词，以及它们在 Power BI Desktop 格式窗格中对应的部分。

**表 8-2** 堆叠柱状图关键词

| 关键词 | 格式部分 |
| --- | --- |
| `general` | 常规 |
| `legend` | 图例 |
| `categoryAxis` | X 轴 |
| `valueAxis` | Y 轴 |
| `zoom` | 缩放滑块 |
| `labels` | 数据标签 |
| `plotArea` | 绘图区 |
| `title` | 标题 |
| `background` | 背景 |
| `lockAspect` | 锁定纵横比 |
| `border` | 边框 |
| `dropShadow` | 阴影 |
| `visualTooltip` | 工具提示 |
| `visualHeader` | 可视化标头 |

百分百堆叠柱状图的 JSON 代码可在示例文件 `Attributes_HundredPercentStackedColumnChart.json` 中找到。

以下代码片段提供了用于预格式化百分百堆叠柱状图的 JSON 代码：

```json
"hundredPercentStackedColumnChart":
{"*":
{
"general": [{... 代码未显示 ...}]
"legend": [{... 代码未显示 ...}],
"categoryAxis": [{
"show": true,
"color": {"solid": {"color": "#949494"}},
"fontSize": 8,
"fontFamily": "Arial",
"preferredCategoryWidth": 20,
"maxMarginFactor": 25,
"innerPadding": 15,
"concatenateLabels": true,
"showAxisTitle": true,
"axisStyle": "showTitleOnly",
"titleColor": {"solid": {"color": "#949494"}},
"titleText": "添加轴标题",
"titleFontSize": 8,
"titleFontFamily": "Arial"
}],
"valueAxis": [{
"show": true,
"position": "Left",
"start": null,
"end": null,
"labelColor": {"solid": {"color": "#949494"}},
"fontSize": 8,
"fontFamily": "Arial",
"labelPrecision": 0,
"labelDisplayUnits": 0,
"showAxisTitle": true,
"axisStyle": "ShowTitleOnly",
"color": {"solid": {"color": "#949494"}},
"titleText": "添加轴标题",
"titleFontSize": 8,
"titleFontFamily": "Arial",
"gridLineShow": false,
"gridLineColor":  {"solid": {"color": "#949494"}},
"gridLineThickness": 1,
"gridlineStyle": "Solid"
}],
"zoom[{... 代码未显示 ...}],
"labels": [{
"show": true,
"color": {"solid": {"color": "#ffffff"}},
"labelDisplayUnits": 1,
"labelPrecision": 2,
"labelOrientation": 1,
"labelPosition": "InsideEnd",
"labelOverflow": true,
"fontFamily": "Arial",
"fontSize": 7,
"enableBackground": false,
"backgroundColor": {"solid": {"color": "#949494"}},
"backgroundTransparency": 0
}],
"plotArea": [{... 代码未显示 ...}],
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

图例关键词在表 7-2 中给出。类别关键词在表 7-3 中给出。数值关键词在表 7-4 中给出。缩放关键词在表 7-5 中给出。标签关键词在表 7-6 中给出。绘图区关键词在表 7-8 中给出。工具提示关键词在表 6-5 中给出。图例位置选项在表 7-9 中描述。分类轴样式选项在表 7-16 中描述。值轴显示单位选项在表 7-11 中描述。值轴样式选项在表 7-16 中描述。值轴网格线样式选项在表 7-13 中描述。标签位置选项在表 7-12 中描述。标签方向选项在表 7-19 中描述。分类轴位置选项在表 7-10 中描述。

# 堆叠面积图

堆叠面积图本质上是面积图的扩展——只是没有次要 Y 轴。

指定百分比堆叠柱状图格式属性的关键字是 `stackedAreaChart`。

表 8-3 概述了在折线图格式化中可以使用的 JSON 关键字，以及每个关键字在 Power BI Desktop 格式窗格中对应的部分。

**表 8-3：** 堆叠面积图关键字

| 关键字 | 格式化部分 |
| --- | --- |
| `general` | 常规 |
| `legend` | 图例 |
| `categoryAxis` | X 轴 |
| `valueAxis` | Y 轴 |
| `zoom` | 缩放滑块 |
| `labels` | 数据标签 |
| `plotArea` | 绘图区 |
| `title` | 标题 |
| `background` | 背景 |
| `lockAspect` | 锁定纵横比 |
| `border` | 边框 |
| `dropShadow` | 阴影 |
| `visualTooltip` | 工具提示 |
| `visualHeader` | 视觉标头 |

描述堆叠面积图格式属性的完整 JSON 代码可在示例文件中的 `Attributes_StackedAreaChart.json` 中找到。

以下是描述堆叠面积图格式的 JSON：

```json
"stackedAreaChart":
{"*" :
{
"general": [{... code not shown ...}]
"legend": [{
"show": true,
"position": "Top",
"showTitle": true,
"titleText": "Add a legend title",
"legendColor": {"solid": {"color": "#949494"}},
"fontFamily": "Arial",
"fontSize": 8,
"legendMarkerRendering":"lineOnly"
}],
"categoryAxis": [{
"show": true,
"axisType":"Categorical",
"color": {"solid": {"color": "#949494"}},
"fontSize": 8,
"fontFamily": "Arial",
"preferredCategoryWidth": 20,
"maxMarginFactor": 30,
"concatenateLabels": true,
"showAxisTitle": true,
"axisStyle": "showTitleOnly",
"titleColor": {"solid": {"color": "#949494"}},
"titleText": "Add an Axis Title",
"titleFontSize": 8,
"titleFontFamily": "Arial"
}],
"valueAxis": [{
"show": true,
"position": "Left",
"axisScale": "Linear",
"start": null,
"end": null,
"labelColor": {"solid": {"color": "#949494"}},
"fontSize": 8,
"fontFamily": "Arial",
"labelPrecision": 0,
"labelDisplayUnits": 0,
"showAxisTitle": true,
"axisStyle": "ShowUnitOnly",
"color": {"solid": {"color": "#949494"}},
"titleText": "Add an Axis Title",
"titleFontSize": 8,
"titleFontFamily": "Arial",
"gridLineShow": true,
"gridLineColor": {"solid": {"color": "#949494"}},
"gridLineThickness": 2,
"gridlineStyle": "Dashed"
}],
"zoom[{... code not shown ...}],
"labels": [{
"show": true,
"color": {"solid": {"color": "#ffffff"}},
"labelDisplayUnits": 1,
"labelPrecision": 2,
"labelOrientation": 1,
"labelPosition": "InsideEnd",
"labelOverflow": true,
"fontFamily": "Arial",
"fontSize": 7,
"enableBackground": false,
"backgroundColor": {"solid": {"color": "#949494"}},
"backgroundTransparency": 0
}],
"totals": [{
"show": true,
"color": {"solid": {"color": "#ffffff"}},
"enableBackground": true
}],
"lineStyles": [{
"strokeWidth": 1,
"strokeLineJoin": "Bevel",
"lineStyle": "lineOnly",
"showMarker": true,
"markerShape": "longDash",
"markerSize": 3,
"markerColor":  {"solid": {"color": "#949494"}},
"stepped": true,
"showSeries": false
}],
"plotArea": [{... code not shown ...}],
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

相关关键字可参考以下表格：

- 图例关键字见表 7-2。
- 类别关键字见表 7-3。
- 值关键字见表 7-4。
- 缩放关键字见表 7-5。
- 标签关键字见表 7-6。
- 绘图区关键字见表 7-8。
- 工具提示关键字见表 6-5。
- 图例位置选项见表 7-9。
- 类别轴样式选项见表 7-16。
- 值轴显示单位选项见表 7-11。
- 值轴样式选项见表 7-16。
- 值轴网格线样式选项见表 7-13。
- 线条样式选项见表 7-28。
- 标签位置选项见表 7-12。
- 标签方向选项见表 7-19。
- 类别轴位置选项见表 7-10。
- 线条样式 `lineStyle` 样式的选项见表 7-28。
- 线条样式 `strokeLineJoin` 的选项见表 7-29。
- 线条样式 `markerShape` 的选项见表 7-30。

在 Power BI Desktop 中，堆叠面积图还包含一个用于“总计”标签的部分。这些内容的 JSON 格式化目前似乎不生效。但根据底层的 `.PBIX` 文件，我预计像下面这样的代码片段或许在未来的某一天能够生效：

```json
"totals": [{
"show": true,
"color": {"solid": {"color": "#ffffff"}},
"enableBackground": true
}]
```

# 折线图和堆积柱形图

组合图表类型有两种，折线图和堆积柱形图是其中之一。就 JSON 主题代码而言，它是柱形图和折线图的混合体。

指定折线图和堆积柱形图格式属性的关键字是 `lineStackedColumnComboChart`。

就 JSON 而言，本质区别在于数值轴——多个序列在此区分开来。

表 8-4 概述了可在折线图和堆积柱形图格式中使用的 JSON 关键字，以及 Power BI Desktop 格式窗格中每个关键字对应的部分。

**表 8-4** 折线图和堆积柱形图关键字

| 关键字 | 格式部分 |
| --- | --- |
| `general` | 常规 |
| `legend` | 图例 |
| `categoryAxis` | X 轴 |
| `valueAxis` | Y 轴 |
| `zoom` | 缩放滑块 |
| `labels` | 数据标签 |
| `lineStyles` | 形状 |
| `plotArea` | 绘图区 |
| `title` | 标题 |
| `background` | 背景 |
| `lockAspect` | 锁定纵横比 |
| `border` | 边框 |
| `dropShadow` | 阴影 |
| `visualTooltip` | 工具提示 |
| `visualHeader` | 视觉对象标头 |

以下是描述折线图和堆积柱形图格式的 JSON。它是所有视觉对象中最长的 JSON 描述之一。

```
"lineStackedColumnComboChart":
{"*":
{
"general": [{... code not shown ...}]
"legend": [{
"show": true,
"position": "Top",
"showTitle": true,
"titleText": "Add a legend title",
"legendColor": {"solid": {"color": "#949494"}},
"fontFamily": "Arial",
"fontSize": 8,
"legendMarkerRendering":"markerOnly"
}],
"categoryAxis": [{
"show": true,
"color": {"solid": {"color": "#949494"}},
"fontSize": 8,
"fontFamily": "Arial",
"preferredCategoryWidth": 20,
"maxMarginFactor": 25,
"innerPadding": 15,
"concatenateLabels": true,
"showAxisTitle": true,
"axisStyle": "showTitleOnly",
"titleColor": {"solid": {"color": "#949494"}},
"titleText": "Add an Axis Title",
"titleFontSize": 8,
"titleFontFamily": "Arial"
}],
"valueAxis": [{
"show": true,
"position": "Left",
"start": null,
"end": null,
"labelColor": {"solid": {"color": "#949494"}},
"fontSize": 8,
"fontFamily": "Arial",
"labelPrecision": 0,
"labelDisplayUnits": 0,
"showAxisTitle": true,
"axisStyle": "ShowTitleOnly",
"color": {"solid": {"color": "#949494"}},
"titleText": "Add an Axis Title",
"titleFontSize": 8,
"titleFontFamily": "Arial",
"gridLineShow": false,
"gridLineColor":  {"solid": {"color": "#949494"}},
"gridLineThickness": 1,
"gridlineStyle": "Solid",
"secShow": true,
"alignZeros": true,
"secPosition": "Right",
"secStart": null,
"secEnd": null,
"secAxisScale": "Linear",
"secLabelColor": {"solid": {"color": "#949494"}},
"secFontSize": 8,
"secFontFamily": "Arial",
"secLabelDisplayUnits": 0,
"secLabelPrecision": 0,
"secShowAxisTitle": true,
"secTitleText": "Add an Axis Title",
"secAxisStyle": "ShowTitleOnly",
"secTitleColor": {"solid": {"color": "#949494"}},
"secTitleText": "Add an Axis Title",
"secTitleFontSize": 8,
"secTitleFontFamily": "Arial"
}],
"zoom[{... code not shown ...}],
"labels": [{
"show": true,
"color": {"solid": {"color": "#ffffff"}},
"labelDisplayUnits": 1,
"labelPrecision": 2,
"labelOrientation": 1,
"labelPosition": "InsideEnd",
"labelOverflow": true,
"labelOrientation": "Vertical",
"fontFamily": "Arial",
"fontSize": 7,
"enableBackground": false,
"backgroundColor": {"solid": {"color": "#949494"}},
"backgroundTransparency": 0
}],
"lineStyles": [{
"strokeWidth": 1,
"strokeLineJoin": "Bevel",
"lineStyle": "Solid",
"showMarker": true,
"markerShape": "longDash",
"markerSize": 3,
"markerColor":  {"solid": {"color": "#949494"}},
"stepped": true,
"showSeries": false
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

图例关键字如表 7-2 所示。它包含一个附加选项：`legendMarkerRendering`。

类别关键字如表 7-3 所示。它们还包含 `concatenateLabels` 和 `showAxisTitle` 元素。

描述 Y 轴部分的 `valueAxis` 关键字使用了表 8-5 中所示的关键字。

**表 8-5** Y 轴的关键字映射

| 元素 | 关键字 |
| --- | --- |
| Y 轴（Y 轴柱形） | `show` |
| （Y 轴柱形） | `position` |
| 刻度类型（Y 轴柱形） | `axisScale` |
| 起始值（Y 轴柱形） | `start` |
| 结束值（Y 轴柱形） | `end` |
| 颜色（Y 轴柱形） | `labelColor` |
| 文本大小（Y 轴柱形） | `fontSize` |
| 字体系列（Y 轴柱形） | `fontFamily` |
| 显示单位（Y 轴柱形） | `labelDisplayUnits` |
| 值小数位数（Y 轴柱形） | `labelPrecision` |
| 标题（Y 轴柱形） | `showAxisTitle` |
| 样式（Y 轴柱形） | `axisStyle` |
| 标题颜色（Y 轴柱形） | `color` |
| 轴标题（Y 轴柱形） | `titleText` |
| 标题文本大小（Y 轴柱形） | `titleFontSize` |

| （标题）字体系列（Y 轴柱形） | `titleFontFamily` |
| --- | --- |
| 网格线（Y 轴柱形） | `gridLineShow` |
| （网格线）颜色（Y 轴柱形） | `gridLineColor` |
| 描边宽度（Y 轴柱形） | `gridLineThickness` |
| 线条样式（Y 轴柱形） | `gridlineStyle` |
| 显示次坐标轴 | `secShow` |
| 对齐零值 | `alignZeros` |
| （Y 轴行） | `secPosition` |
| 刻度类型（Y 轴行） | `secAxisScale` |
| 起始值（Y 轴行） | `secStart` |
| 结束值（Y 轴行） | `secEnd` |
| 颜色（Y 轴行） | `secLabelColor` |
| 文本大小（Y 轴行） | `secFontSize` |
| 字体系列（Y 轴行） | `secFontFamily` |
| 显示单位（Y 轴行） | `secLabelDisplayUnits` |
| 值小数位数（Y 轴行） | `secLabelPrecision` |
| 标题（Y 轴行） | `secShowAxisTitle` |
| 样式（Y 轴行） | `secAxisStyle` |
| 标题颜色（Y 轴行） | `secTitleColor` |
| 轴标题（Y 轴行） | `secTitleText` |
| 标题文本大小（Y 轴行） | `secTitleFontSize` |
| （标题）字体系列（Y 轴行） | `secTitleFontFamily` |

缩放关键字如表 7-5 所示。

标签关键字如表 7-6 所示。其中还包含 `labelOrientation` 选项。

线条样式关键字如表 7-28 所示。

工具提示关键字如表 6-5 所示。

对于折线图和堆积柱形图，JSON 图例部分中的 `legendMarkerRendering` 关键字需要表 8-6 中所示的某个属性。

### 表 8-6 图例位置选项

| 值 | 格式弹出选项 |
| --- | --- |
| `markerOnly` | 仅标记 |
| `lineAndMarker` | 线条和标记 |
| `lineOnly` | 仅线条 |

类别关键字如表 7-3 所示。

值关键字如表 7-4 所示。

缩放关键字如表 7-5 所示。

绘图区关键字如表 7-8 所示。

图例位置选项如表 7-9 所述。

类别轴样式选项如表 7-16 所述。

值轴显示单位选项如表 7-11 所述。

值轴样式选项如表 7-16 所述。

值轴网格线样式选项如表 7-13 所述。

线条样式选项如表 7-28 所述。

标签位置选项如表 7-12 所述。

标签方向选项如表 7-19 所述。

类别轴样式选项如表 7-16 所述。

线条样式 `lineStyle` 的选项见表 7-28。

线条样式 `strokeLineJoin` 的选项见表 7-29。

线条样式 `markerShape` 的选项见表 7-30。

用于描述折线图和堆积柱形图格式化属性的完整 JSON 代码位于文件 `Attributes_LineAndStackedColumnChart.json` 中。

## 折线图和簇状柱形图

另一种可选的组合图类型是折线图和簇状柱形图。此视觉对象的 JSON 代码与折线图和堆积柱形图的代码非常相似。

指定折线图和簇状柱形图格式化属性的关键字是 `lineClusteredColumnComboChart`。

表 8-7 概述了可在折线图和簇状柱形图格式化中使用的 JSON 关键字，以及 Power BI Desktop 格式窗格中与每个关键字对应的部分。

### 表 8-7 折线图和簇状柱形图关键字

| 关键字 | 格式设置部分 |
| --- | --- |
| `general` | 常规 |
| `legend` | 图例 |
| `categoryAxis` | X 轴 |
| `valueAxis` | Y 轴 |
| `zoom` | 缩放滑块 |
| `labels` | 数据标签 |
| `lineStyles` | 形状 |
| `plotArea` | 绘图区 |
| `title` | 标题 |
| `background` | 背景 |
| `lockAspect` | 锁定纵横比 |
| `border` | 边框 |
| `dropShadow` | 阴影 |
| `visualTooltip` | 工具提示 |
| `visualHeader` | 视觉对象标头 |

以下是描述折线图和簇状柱形图格式设置的 JSON：
```json
// 此处为示例，表示JSON代码块的位置
{
  // 具体的JSON属性配置
}
```

# 配置示例

你可以在示例文件`Attributes_LineAndClusteredColumnChart.json`中找到此 JSON 代码。

## 配置关键字参考

类别关键字见表 7-3。值关键字见表 7-4。缩放关键字见表 7-5。绘图区关键字见表 7-8。图例位置选项见表 7-9。分类轴样式选项见表 7-16。值轴显示单位选项见表 7-11。值轴样式选项见表 7-16。值轴网格线样式选项见表 7-13。线条样式选项见表 7-28。标签位置选项见表 7-12。标签方向选项见表 7-19。分类轴样式选项见表 7-16。线条样式部分`lineStyle`的选项见表 7-28。线条样式部分`strokeLineJoin`的选项见表 7-29。线条样式部分`markerShape`的选项见表 7-30。

```
"lineClusteredColumnComboChart":
{"*":
{
"general": [{... 代码未显示 ...}]
"legend": [{
"show": true,
"position": "Top",
"showTitle": true,
"titleText": "添加图例标题",
"legendColor": {"solid": {"color": "#949494"}},
"fontFamily": "Arial",
"fontSize": 8,
"legendMarkerRendering":"markerOnly"
}],
"categoryAxis": [{
"show": true,
"color": {"solid": {"color": "#949494"}},
"fontSize": 8,
"fontFamily": "Arial",
"preferredCategoryWidth": 20,
"maxMarginFactor": 25,
"innerPadding": 15,
"concatenateLabels": true,
"showAxisTitle": true,
"axisStyle": "showTitleOnly",
"titleColor": {"solid": {"color": "#949494"}},
"titleText": "添加轴标题",
"titleFontSize": 8,
"titleFontFamily": "Arial"
}],
"valueAxis": [{
"show": true,
"position": "Left",
"start": null,
"end": null,
"labelColor": {"solid": {"color": "#949494"}},
"fontSize": 8,
"fontFamily": "Arial",
"labelPrecision": 0,
"labelDisplayUnits": 0,
"showAxisTitle": true,
"axisStyle": "ShowTitleOnly",
"color": {"solid": {"color": "#949494"}},
"titleText": "添加轴标题",
"titleFontSize": 8,
"titleFontFamily": "Arial",
"gridLineShow": false,
"gridLineColor":  {"solid": {"color": "#949494"}},
"gridLineThickness": 1,
"gridlineStyle": "Solid",
"secShow": true,
"alignZeros": true,
"secPosition": "Right",
"secStart": null,
"secEnd": null,
"secAxisScale": "Linear",
"secLabelColor": {"solid": {"color": "#949494"}},
"secFontSize": 8,
"secFontFamily": "Arial",
"secLabelDisplayUnits": 0,
"secLabelPrecision": 0,
"secShowAxisTitle": true,
"secTitleText": "添加轴标题",
"secAxisStyle": "ShowTitleOnly",
"secTitleColor": {"solid": {"color": "#949494"}},
"secTitleText": "添加轴标题",
"secTitleFontSize": 8,
"secTitleFontFamily": "Arial"
}],
"zoom[{... 代码未显示 ...}],
"labels": [{
"show": true,
"color": {"solid": {"color": "#ffffff"}},
"labelDisplayUnits": 1,
"labelPrecision": 2,
"labelOrientation": 1,
"labelPosition": "InsideEnd",
"labelOverflow": true,
"labelOrientation": "Vertical",
"fontFamily": "Arial",
"fontSize": 7,
"enableBackground": false,
"backgroundColor": {"solid": {"color": "#949494"}},
"backgroundTransparency": 0
}],
"lineStyles": [{
"shadeArea": true,
"strokeWidth": 1,
"strokeLineJoin": "Bevel",
"lineStyle": "Solid",
"showMarker": true,
"stepped": true
}],
"plotArea": [{... 代码未显示 ...}],
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

# 散点图

指定散点图格式属性的关键字是 `scatterChart`。

## 表 8-8 散点图关键字

| 关键字 | 格式分区 |
| --- | --- |
| `general` | 常规 |
| `legend` | 图例 |
| `categoryAxis` | X 轴 |
| `valueAxis` | Y 轴 |
| `bubbles` | 形状 |
| `categoryLabels` | 类别标签 |
| `colorBorder` | 颜色边框 |
| `plotArea` | 绘图区 |
| `title` | 标题 |
| `background` | 背景 |
| `lockAspect` | 锁定纵横比 |
| `border` | 边框 |
| `dropShadow` | 阴影 |
| `visualHeader` | 视觉对象标头 |

以下 JSON 描述了散点图的格式：

```json
"scatterChart":
{"*":
{
"general": [{... code not shown ...}],
"legend": [{... code not shown ...}],
"categoryAxis": [{
"show": true,
"axisScale": "Linear",
"start": null,
"end": null,
"color": {"solid": {"color": "#949494"}},
"fontSize": 8,
"fontFamily": "Arial",
"preferredCategoryWidth": 20,
"maxMarginFactor": 25,
"innerPadding": 15,
"concatenateLabels": true,
"showAxisTitle": true,
"axisStyle": "showTitleOnly",
"titleColor": {"solid": {"color": "#949494"}},
"titleText": "Add an Axis Title",
"titleFontSize": 8,
"titleFontFamily": "Arial",
"gridLineShow": true,
"gridLineColor":  {"solid": {"color": "#949494"}},
"gridLineThickness": 1,
"gridlineStyle": "Solid",
"treatNullsAsZero": true
}],
"valueAxis": [{
"show": true,
"position": "Left",
"start": null,
"end": null,
"labelColor": {"solid": {"color": "#949494"}},
"fontSize": 8,
"fontFamily": "Arial",
"labelPrecision": 0,
"labelDisplayUnits": 0,
"showAxisTitle": true,
"axisStyle": "ShowTitleOnly",
"color": {"solid": {"color": "#949494"}},
"titleText": "Add an Axis Title",
"titleFontSize": 8,
"titleFontFamily": "Arial",
"gridLineShow": false,
"gridLineColor":  {"solid": {"color": "#949494"}},
"gridLineThickness": 1,
"gridlineStyle": "Solid",
"treatNullsAsZero": true
}],
"bubbles": [{
"bubbleSize": 15,
"markerShape": "square",
"customizeSeries": true
}],
"categoryLabels": [{
"show": true,
"fontColor": { "solid": { "color": "#000000"}},
"fontSize": 11,
"fontFamily": "Arial",
"enableBackground": true,
"backgroundColor": { "solid": { "color": "#949494"}},
"backgroundTransparency": 25
}],
"colorBorder": [{
"show": true
}],
"plotArea": [{... code not shown ...}],
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

图例关键字见表 7-2。

## 表 8-9 X 轴的关键字映射

描述 X 轴分区的 `categoryAxis` 关键字使用下表所示的关键字。

| 元素 | 关键字 |
| --- | --- |
| X 轴 | `show` |
| 刻度类型 | `axisScale` |
| 起始 | `start` |
| 结束 | `end` |
| 颜色 | `labelColor` |
| 文本大小 | `fontSize` |
| 字体系列 | `fontFamily` |
| 显示单位 | `labelDisplayUnits` |
| 值小数位数 | `labelPrecision` |
| 标题 | `showAxisTitle` |
| 网格线 | `gridLineShow` |
| （网格线）颜色 | `gridLineColor` |
| 描边宽度 | `gridLineThickness` |
| 线条样式 | `gridlineStyle` |
| 显示空白值 | `treatNullsAsZero` |

## 表 8-10 Y 轴的关键字映射

描述 Y 轴分区的 `valueAxis` 关键字使用下表所示的关键字。

| 元素 | 关键字 |
| --- | --- |
| Y 轴 | `show` |
| 位置 | `position` |
| 刻度类型 | `axisScale` |
| 起始 | `start` |
| 结束 | `end` |
| 颜色 | `labelColor` |
| 文本大小 | `fontSize` |
| 字体系列 | `fontFamily` |
| 显示单位 | `labelDisplayUnits` |
| 值小数位数 | `labelPrecision` |
| 标题 | `showAxisTitle` |
| 样式 | `axisStyle` |
| 标题颜色 | `color` |
| 轴标题 | `titleText` |
| 标题文本大小 | `titleFontSize` |
| （标题）字体系列 | `titleFontFamily` |
| 网格线 | `gridLineShow` |
| （网格线）颜色 | `gridLineColor` |
| 描边宽度 | `gridLineThickness` |
| 线条样式 | `gridlineStyle` |
| 显示空白值 | `treatNullsAsZero` |

缩放关键字见表 7-5。
标签关键字见表 7-6。

## 表 8-11 形状的关键字映射

描述形状分区的 `bubbles` 关键字使用下表所示的关键字。

| 元素 | 关键字 |
| --- | --- |
| 形状 | `show` |
| 大小 | `bubbleSize` |
| 标记形状 | `markerShape` |

在 JSON 中定义形状分区中标记形状的 `markerShape` 关键字需要使用表 8-12 中所示的属性之一。

## 表 8-12 标记形状选项

| 值 | 格式弹出选项 |
| --- | --- |
| `Circle` | • (圆形) |
| `Square` | ■ (方形) |
| `Diamond` | ◆ (菱形) |
| `Triangle` | ▲ (三角形) |

绘图区关键字见表 7-8。
颜色边框分区仅包含 `show` 关键字。

您可以在此处的示例文件 `Attributes_ScatterChart.json` 中找到相应的 JSON 代码。

# 结论

本章向您介绍了六种图表类型：100% 堆叠条形图、100% 堆叠柱形图、堆叠面积图、折线图和堆叠柱形图、折线图和簇状柱形图以及散点图。它们都基于您在上一章中看到的图表样式（和 JSON 代码）构建，并扩展了您的知识，使您能够创建可定义所有经典数据驱动图表类型的图表格式的主题文件。为了结束图表 JSON 格式的探讨，下一章将探讨可添加到主题文件中的其余 Power BI 图表类型。

