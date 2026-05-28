# 折线图

尽管折线图的外观与条形图和柱形图截然不同，但其格式设置方面与本章中已介绍的所有图表类型实际上非常相似。

与条形图和柱形图相比，折线图的 JSON 中存在的主要差异如下：

- 折线图包含用于描述次坐标轴的 JSON。
- 图例包含一些额外的元素。
- X（类别）轴略有不同。
- 最小类别宽度仍使用关键字 `preferredCategoryWidth`。
- 最大尺寸仍使用关键字 `maxMarginFactor`。
- 有一个额外的元素用于描述线条样式——由 `lineStyles` 关键字引入。
- `labels` 部分包含一个 `labelPosition` 元素——但其值与您在其他图表中看到的不同。

用于指定折线图格式属性的关键字是 `lineChart`。

### 表 7-20 折线图关键字

表 7-20 概述了可在折线图格式中使用的 JSON 关键字，以及每个关键字在 Power BI Desktop 格式窗格中对应的部分。

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
| `lockAspect` | 锁定纵横比 |
| `border` | 边框 |
| `dropShadow` | 阴影 |
| `visualHeader` | 视觉对象标头 |

描述折线图格式属性的 JSON 代码位于示例文件 `Attributes_LineChart.json` 中。代码片段如下所示。需要提醒的是，这是一段相当长的 JSON。

```json
"lineChart":
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
"legendMarkerRendering":"lineOnly",
"matchLineColor":true,
"defaultToCircle":true
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
"position": "Right",
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
"gridLineShow": false,
"gridLineColor":  {"solid": {"color": "#949494"}},
"gridLineThickness": 2,
"gridlineStyle": "dotted"
}],
"y2Axis": [{
"show": true,
"secStart": null,
"secEnd": null,
"secAxisScale": "Linear",
"secLabelColor": {"solid": {"color": "#949494"}},
"secFontSize": 8,
"secSontFamily": "Arial",
"secLabelPrecision": 0,
"secLabelDisplayUnits": 0,
"secShowAxisTitle": true,
"secAxisStyle": "ShowUnitOnly",
"secColor": {"solid": {"color": "#949494"}},
"secTitleText": "Add an Axis Title",
"secTitleFontSize": 8,
"secTitleFontFamily": "Arial"
}],
"zoom: [{... code not shown ...}],
"labels": [{
"show": true,
"color": {"solid": {"color": "#ffffff"}},
"labelDisplayUnits": 1,
"labelPrecision": 2,
"labelOrientation": 1,
"labelPosition": "Above",
"labelOverflow": true,
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

与新的线条样式和次 Y 轴部分中的元素一样，折线图还提供了一个迄今为止在其他图表视觉对象中未曾见过的选项，即轴类型（位于分类轴中）。实际上，图例和分类轴部分差异显著，值得专门审查。

图例部分对应的关键词 `legend` 使用了表 7-21 中所示的关键词。

### 表 7-21 图例关键词映射

| 元素 | 关键词 |
| --- | --- |
| 图例 | `show` |
| 位置 | `position` |
| 标题 | `showTitle` |
| 图例名称 | `titleText` |
| 颜色 | `legendColor` |
| 字体 | `fontFamily` |
| 字号 | `fontSize` |
| 样式 | `legendMarkerRendering` |
| 匹配线条颜色 | `matchLineColor` |
| 圆形默认图标 | `defaultToCircle` |

X 轴部分对应的关键词 `categoryAxis` 使用了表 7-22 中所示的关键词。

### 表 7-22 X 轴关键词映射

| 元素 | 关键词 |
| --- | --- |
| X 轴 | `show` |
| 轴类型 | `axisType` |
| 颜色 | `color` |
| 字号 | `fontSize` |
| 字体 | `fontFamily` |
| 最小分类宽度 | `preferredCategoryWidth` |
| 最大尺寸 | `maxMarginFactor` |
| 标题 | `showAxisTitle` |
| 样式 | `axisStyle` |
| 标题颜色 | `titleColor` |
| 轴标题 | `titleText` |
| 标题字号 | `titleFontSize` |
| （标题）字体 | `titleFontFamily` |

一如既往，您在 JSON 主题中使用的值必须与预期值**完全**匹配，否则将使用默认值。同样，请记住这些值区分大小写。

`valueAxis` 选项与表 7-3 中所示的选项相同，只是增加了 `position` 元素。

次 Y 轴部分对应的关键词 `y2Axis` 使用了表 7-23 中所示的关键词。

### 表 7-23 次 Y 轴关键词映射

| 元素 | 关键词 |
| --- | --- |
| 次 Y 轴 | `secShow` |
| 起始值 | `secStart` |
| 终止值 | `secEnd` |
| 刻度类型 | `secAxisScale` |
| 颜色 | `secLabelColor` |
| 字号 | `secFontSize` |
| 字体 | `secFontFamily` |
| 显示单位 | `secLabelDisplayUnits` |
| 值小数位数 | `secLabelPrecision` |
| 标题 | `secShowAxisTitle` |
| 样式 | `secAxisStyle` |
| 标题颜色 | `secColor` |
| 轴标题 | `secTitleText` |
| 标题字号 | `secTitleFontSize` |
| （标题）字体 | `secTitleFontFamily` |
| 网格线 | `secGridLineShow` |

缩放选项如表 7-5 所示。
标签选项如表 7-6 所示。

`labels` 部分中使用的 `labelPosition` 关键词需要表 7-24 中所示的某一属性值。

### 表 7-24 标签位置选项

| 元素 | 关键词 |
| --- | --- |
| 无 | `None` |
| 上方 | `Above` |
| 下方 | `Under` |

形状部分对应的关键词 `lineStyle` 使用了表 7-25 中所示的关键词。

### 表 7-25 形状关键词映射

| 元素 | 关键词 |
| --- | --- |
| 描边宽度 | `strokeWidth` |
| 连接类型 | `strokeLineJoin` |
| 线条样式 | `lineStyle` |
| 显示标记 | `showMarker` |
| 标记形状 | `markerShape` |
| 标记大小 | `markerSize` |
| 标记颜色 | `markerColor` |
| 阶梯线 | `Stepped` |
| 自定义序列 | `showSeries` |

JSON 中用于定义折线图（或面积图、堆叠面积图）分类轴的 `axisType` 关键词需要表 7-26 中所示的某一属性值。

### 表 7-26 X 轴类型选项

| 值 | 格式化弹出选项 |
| --- | --- |
| `Categorical` | 分类的 |
| `Continuous` | 连续的 |

JSON 中用于指定值轴刻度的 `axisScale` 关键词需要表 7-27 中所示的某一属性值。

### 表 7-27 值轴刻度选项

| 值 | 格式化弹出选项 |
| --- | --- |
| `Linear` | 线性 |
| `Log` | 对数 |

JSON 中用于线条样式部分的 `lineStyle` 关键词需要表 7-28 中所示的某一属性值。

### 表 7-28 线条样式选项

| 值 | 格式化弹出选项 |
| --- | --- |
| `markerOnly` | 仅标记 |
| `lineAndMarker` | 线条和标记 |
| `lineOnly` | 仅线条 |

JSON 中用于定义线条样式部分连接形状的 `strokeLineJoin` 关键词需要表 7-29 中所示的某一属性值。

### 表 7-29 连接类型选项

| 值 | 格式化弹出选项 |
| --- | --- |
| `Miter` | 斜接 |
| `Round` | 圆角 |
| `Bevel` | 斜角 |

JSON 中用于定义线条样式部分标记形状的 `markerShape` 关键词需要表 7-30 中所示的某一属性值。

### 表 7-30 标记形状选项

| 值 | 格式弹出选项 |
| --- | --- |
| `Round` | 圆角 |
| `Square` | 方形 |
| `Diamond` | 菱形 |
| `Triangle` | 三角形 |
| `x` | x |
| `shortDash` | - |
| `longDash` | — |
| `+` | + |

标签位置选项见表 7-19。
图例位置选项见表 7-9。
类别轴位置选项见表 7-10。
值轴显示单位选项见表 7-11。
值轴样式选项见表 7-16。
值轴网格线样式选项见表 7-13。
标签位置选项见表 7-12。

据我目前的理解，目前还无法在 Power BI Desktop 格式窗格中定义自定义序列（即形状/自定义序列）的格式。我可以理解为什么会出现这种情况，因为此选项需要指定一个字段，因此无法通用定义。

## 面积图

用于指定面积图格式的 JSON 与折线图的代码几乎相同，只是使用了不同的初始关键字（即必须使用 `areaChart`）。

表 7-31 概述了可在面积图格式中使用的 JSON 关键字，以及这些关键字在 Power BI Desktop 格式窗格中对应的部分。

### 表 7-31 面积图关键字

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
| `lockAspect` | 锁定纵横比 |
| `border` | 边框 |
| `dropShadow` | 阴影 |
| `visualHeader` | 视觉对象标头 |

用于描述面积图格式属性的完整 JSON 代码可在示例文件的 `Attributes_AreaChart.json` 中找到。

以下是描述面积图格式的 JSON：

### 示例代码：AreaChart JSON 配置

```json
"areaChart":
{"*" :
{
"general": [{... 代码省略 ...}]
"legend": [{
"show": true,
"position": "Top",
"showTitle": true,
"titleText": "添加图例标题",
"legendColor": {"solid": {"color": "#949494"}},
"fontFamily": "Arial",
"fontSize": 8,
"legendMarkerRendering":"lineOnly",
"matchLineColor":true,
"defaultToCircle":true
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
"titleText": "添加轴标题",
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
"titleText": "添加轴标题",
"titleFontSize": 8,
"titleFontFamily": "Arial",
"gridLineShow": true,
"gridLineColor":  {"solid": {"color": "#949494"}},
"gridLineThickness": 2,
"gridlineStyle": "dashed"
}],
"y2Axis": [{
"show": true,
"secStart": null,
"secEnd": null,
"secAxisScale": "Linear",
"secLabelColor": {"solid": {"color": "#949494"}},
"secFontSize": 8,
"secSontFamily": "Arial",
"secLabelPrecision": 0,
"secLabelDisplayUnits": 0,
"secShowAxisTitle": true,
"secAxisStyle": "ShowUnitOnly",
"secColor": {"solid": {"color": "#949494"}},
"secTitleText": "添加轴标题",
"secTitleFontSize": 8,
"secTitleFontFamily": "Arial"
}],
"zoom: [{... 代码省略 ...}],
"labels": [{
"show": true,
"color": {"solid": {"color": "#ffffff"}},
"labelDisplayUnits": 1,
"labelPrecision": 2,
"labelPosition": "InsideEnd",
"labelOverflow": true,
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
"plotArea": [{... 代码省略 ...}],
"title": [{... 代码省略 ...}],
"background": [{... 代码省略 ...}],
"lockAspect": [{... 代码省略 ...}],
"border": [{... 代码省略 ...}],
"dropShadow": [{... 代码省略 ...}],
"visualTooltip": [{... 代码省略 ...}],
"visualHeader": [{... 代码省略 ...}]
}
}
```

### 格式参考

*   图例关键字见`表 7-2`。
*   类别关键字见`表 7-3`。
*   值关键字见`表 7-4`。
*   缩放关键字见`表 7-5`。
*   标签关键字见`表 7-6`。
*   汇总关键字见`表 7-7`。
*   绘图区关键字见`表 7-8`。
*   工具提示关键字见`表 6-5`。
*   图例位置选项见`表 7-9`。
*   类别轴位置选项见`表 7-10`。
*   值轴显示单位选项见`表 7-11`。
*   值轴样式选项见`表 7-16`。
*   值轴网格线样式选项见`表 7-13`。
*   标签位置选项见`表 7-12`。
*   线条样式位置选项见`表 7-22`。

用于描述面积图格式属性的完整JSON代码可在示例代码文件中以`Attributes_AreaChart.json`形式获取。

## 本章小结

在本章中，你学习了如何准备JSON主题，以对六种核心图表类型应用标准格式：堆叠条形图、堆叠柱形图、簇状条形图、簇状柱形图、折线图和面积图。

你已看到它们共享许多共同特征，这导致了格式定义的JSON代码库在很大程度上得以共享。

下一步是深入学习六种更复杂一些的图表格式规范——但它们都建立在你本章所学的JSON定义之上。