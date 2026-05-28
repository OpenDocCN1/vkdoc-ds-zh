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