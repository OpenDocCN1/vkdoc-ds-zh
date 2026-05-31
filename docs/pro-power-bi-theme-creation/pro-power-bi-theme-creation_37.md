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

```
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

图例关键字见表 7-2。
类别关键字见表 7-3。
值关键字见表 7-4。
缩放关键字见表 7-5。
标签关键字见表 7-6。
绘图区关键字见表 7-8。
工具提示关键字见表 6-5。
图例位置选项见表 7-9。
类别轴样式选项见表 7-16。
值轴显示单位选项见表 7-11。
值轴样式选项见表 7-16。
值轴网格线样式选项见表 7-13。
线条样式选项见表 7-28。
标签位置选项见表 7-12。
标签方向选项见表 7-19。
类别轴位置选项见表 7-10。
线条样式 `lineStyle` 样式的选项见表 7-28。
线条样式 `strokeLineJoin` 的选项见表 7-29。
线条样式 `markerShape` 的选项见表 7-30。

在 Power BI Desktop 中，堆叠面积图还包含一个用于“总计”标签的部分。这些内容的 JSON 格式化目前似乎不生效。但根据底层的 `.PBIX` 文件，我预计像下面这样的代码片段或许在未来的某一天能够生效：

```
"totals": [{
"show": true,
"color": {"solid": {"color": "#ffffff"}},
"enableBackground": true
}]
```



