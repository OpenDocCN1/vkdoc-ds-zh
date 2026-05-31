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



