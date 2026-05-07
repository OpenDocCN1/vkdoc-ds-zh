# 堆积柱形图

接下来要查看的图表是堆积柱形图。其 JSON 结构与你之前看到的代码基本一致，这说明了 Power BI 图表在结构方法上的共通性。

用于指定堆积柱形图格式属性的关键字是 `columnChart`。

表 7-14 概述了在堆积条形图格式设置中可用的 JSON 关键字，以及这些关键字在 Power BI Desktop 格式窗格中对应的部分。

**表 7-14** 堆积条形图关键字

| 关键字 | 格式区域 |
| --- | --- |
| `General` | 常规 |
| `legend` | 图例 |
| `categoryAxis` | X 轴 |
| `valueAxis` | Y 轴 |
| `zoom` | 缩放滑块 |
| `labels` | 数据标签 |
| `totals` | 总计标签 |
| `plotArea` | 绘图区 |
| `title` | 标题 |
| `lockAspect` | 锁定纵横比 |
| `border` | 边框 |
| `dropShadow` | 阴影 |
| `visualHeader` | 视觉对象标头 |

与你之前看到的堆积条形图 JSON 相比，主要区别在于：

-   `categoryAxis` 新增了 `concatenateLabels` 关键字，并移除了 `position` 关键字。
-   `valueAxis` 新增了 `position` 关键字，并移除了 `axisScale` 关键字。
-   `labels` 新增了 `labelOrientation`。
-   现在还有选项可以为该图表类型选择分类轴和值轴的样式。
-   在此 JSON 中，图表中的垂直 (Y) 轴是值轴（因此水平 (X) 轴是分类轴）。

完整定义可在示例文件中的 `Attributes_StackedColumnChart.json` 文件中找到。JSON 代码如下：

```
"columnChart":
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
"zoom: [{... 代码未显示 ...}],
"labels": [{
"show": true,
"color": {"solid": {"color": "#FFFFFF"}},
"labelDisplayUnits": 1,
"labelPrecision": 2,
"labelPosition": "InsideEnd",
"labelOverflow": true,
"labelOrientation": "vertical",
"fontFamily": "Arial",
"fontSize": 7,
"enableBackground": false,
"backgroundColor": {"solid": {"color": "#949494"}},
"backgroundTransparency": 0
}] ,
"totals": [{... 代码未显示 ...}],
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

幸运的是，用于 X 轴、Y 轴和标签部分的堆积柱形图关键字，与用于堆积条形图的关键字基本一致——因此此处不再赘述，而是建议你参考上一节。

JSON 中用于设置标签方向的 `labelOrientation` 关键字（在 `labels` 部分）需要提供表 7-15 中所示的属性之一。

**表 7-15** 标签显示单位选项

| 值 | 格式弹出选项 |
| --- | --- |
| `Vertical` | 垂直 |
| `Horizontal` | 水平 |

JSON 中用于值轴的 `axisStyle` 关键字需要提供表 7-16 中所示的属性之一。

**表 7-16** 分类轴和值轴样式选项

| 值 | 格式弹出选项 |
| --- | --- |
| `ShowTitleOnly` | 仅显示标题 |
| `ShowUnitOnly` | 仅显示单位 |
| `ShowBoth` | 同时显示 |

图例关键字见表 7-2。

分类轴关键字见表 7-3。

值轴关键字见表 7-4。

缩放关键字见表 7-5。对于柱形图，`showOnValueAxis` 指的是 Y 轴。

标签关键字见表 7-6。

总计关键字见表 7-7。

绘图区关键字见表 7-8。

工具提示关键字见表 6-5。

图例位置选项见 7-9 中的描述。

分类轴位置选项见 7-10 中的描述。

值轴显示单位选项见 7-11 中的描述。

值轴样式选项见 7-16 中的描述。

值轴网格线样式选项见 7-13 中的描述。

标签位置选项见 7-12 中的描述。



