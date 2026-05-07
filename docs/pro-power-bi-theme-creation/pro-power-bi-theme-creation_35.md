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



