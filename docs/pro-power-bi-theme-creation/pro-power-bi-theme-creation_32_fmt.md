# 簇状柱形图

用于指定簇状柱形图格式的 JSON 与之前看到的堆叠柱形图代码非常相似——当然，除了 JSON 对象的初始关键字之外。这个关键字是 `clusteredColumnChart`。

主要区别在于：

- 它*不*包含 `totals` 部分。
- 在 `labels` 部分中新增了 `labelOrientation` 关键字。

表 7-18 概述了可在堆叠条形图格式中使用的 JSON 关键字，以及每个关键字在 Power BI Desktop 格式窗格中对应的部分。

**表 7-18** 簇状柱形图关键字

| 关键字 | 格式部分 |
| --- | --- |
| `general` | 常规 |
| `legend` | 图例 |
| `categoryAxis` | X 轴 |
| `valueAxis` | Y 轴 |
| `zoom` | 缩放滑块 |
| `labels` | 数据标签 |
| `plotArea` | 绘图区 |
| `title` | 标题 |
| `lockAspect` | 锁定纵横比 |
| `border` | 边框 |
| `dropShadow` | 阴影 |
| `visualHeader` | 视觉对象标头 |

以下代码片段提供了用于预先格式化簇状柱形图的 JSON 代码：

```
"clusteredColumnChart":
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
"axisStyle": "ShowUnitOnly",
"color": {"solid": {"color": "#949494"}},
"titleText": "添加轴标题",
"titleFontSize": 8,
"titleFontFamily": "Arial",
"gridLineShow": false,
"gridLineColor":  {"solid": {"color": "#949494"}},
"gridLineThickness": 1,
"gridlineStyle": "Solid"
}],
"zoom: [{... 代码未显示 ...}],
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

JSON 中用于设置标签方向的 `labelOrientation` 关键字（位于 `labels` 部分内）需要提供表 7-19 中所示的其中一个属性。

**表 7-19** 标签显示单位选项

| 值 | 格式弹出选项 |
| --- | --- |
| `vertical` | 0 |
| `horizontal` | 1 |

**注意：** 与堆叠柱形图不同，簇状柱形图使用 `0` 和 `1` 作为关键字，而不是 `horizontal` 和 `vertical`。此外，这些是数值，因此不得用双引号括起来。

值得注意的是，簇状柱形图的 JSON 也提供了选项（类似于簇状条形图），用于选择类别轴和值轴的样式。

簇状柱形图的 JSON 代码可在示例文件 `Attributes_ClusteredColumnChart.json` 中找到。

图例关键字见表 7-2。

类别关键字见表 7-3。

值关键字见表 7-4。

缩放关键字见表 7-5。对于柱形图，`showOnValueAxis` 指的是 Y 轴。

标签关键字见表 7-6。

总计关键字见表 7-7。

绘图区关键字见表 7-8。

工具提示关键字见表 6-5。

图例位置选项见表 7-9。

类别轴位置选项见表 7-10。

值轴显示单位选项见表 7-11。

值轴样式选项见表 7-16。

值轴网格线样式选项见表 7-13。

标签位置选项见表 7-12。