# 簇状条形图

指定簇状条形图格式的 JSON 与您之前看到的堆积条形图代码几乎完全相同——当然，除了 JSON 对象的初始关键字。唯一的主要区别在于它不包含 `totals` 部分。

指定簇状条形图格式属性的关键字是 `clusteredBarChart`。

表 7-17 概述了可在簇状条形图格式中使用的 JSON 关键字，以及 Power BI Desktop“格式”窗格中每个关键字对应的部分。

**表 7-17** 簇状条形图关键字

| 关键字 | 格式部分 |
| --- | --- |
| `general` | 常规 |
| `legend` | 图例 |
| `categoryAxis` | Y 轴 |
| `valueAxis` | X 轴 |
| `zoom` | 缩放滑块 |
| `labels` | 数据标签 |
| `plotArea` | 绘图区域 |
| `title` | 标题 |
| `lockAspect` | 锁定纵横比 |
| `border` | 边框 |
| `dropShadow` | 阴影 |
| `visualHeader` | 视觉对象标头 |

指定簇状条形图格式的 JSON 代码可在示例文件 `Attributes_ClusteredBarChart.json` 中找到。

为求完整，以下是定义簇状条形图格式的 JSON 代码：

```
"clusteredBarChart":
{"*" :
{
"general": [{... code not shown ...}]
"legend": [{... code not shown ...}],
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
"titleText": "Add an Axis Title",
"titleFontSize": 8,
"titleFontFamily": "Arial"
}],
"valueAxis": [{
"show": true,
"axisScale": "Linear",
"start": null,
"end": null,
"labelColor": {"solid": {"color": "#949494"}},
"fontSize": 8,
"fontFamily": "Arial",
"labelPrecision": 0,
"labelDisplayUnits": 2,
"showAxisTitle": true,
"axisStyle": "ShowTitleOnly",
"color": {"solid": {"color": "#949494"}},
"titleText": "Add an Axis Title",
"titleFontSize": 8,
"titleFontFamily": "Arial",
"gridLineShow": false,
"gridLineColor":  {"solid": {"color": "#949494"}},
"gridLineThickness": 1,
"gridlineStyle": "Solid"
}],
"zoom: [{... code not shown ...}]
"labels": [{
"show": true,
"color": {"solid": {"color": "#FFFFFF"}},
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

缩放关键字见表 7-5。对于条形图，`showOnValueAxis` 指的是 X 轴。

标签关键字见表 7-6。

总计关键字见表 7-7。

绘图区域关键字见表 7-8。

工具提示关键字见表 6-5。

图例位置选项见表 7-9。

分类轴位置选项见表 7-10。

值轴显示单位选项见表 7-11。

值轴样式选项见表 7-16。

值轴网格线样式选项见表 7-13。

标签位置选项见表 7-12。



