# 彩带图

为了结束本章对主题文件中图表类型格式化的回顾，你需要了解彩带图。这种图表类型与折线图非常相似——至少在 JSON 层面如此。主要区别在于增加了 `ribbonChart` 关键字，该关键字用于引入彩带图格式化的具体部分。令人困惑的是，这个关键字与用于在 JSON 中定义实际彩带图对象的关键字相同。

表 9-23 概述了可在彩带图格式化中使用的 JSON 关键字，以及每个关键字在 Power BI Desktop 格式窗格中对应的分区。

**表 9-23** 彩带图关键字

| 关键字 | 格式化分区 |
| --- | --- |
| `general` | 常规 |
| `legend` | 图例 |
| `categoryAxis` | X 轴 |
| `zoom` | 缩放滑块 |
| `labels` | 数据标签 |
| `plotArea` | 绘图区 |
| `ribbonChart` | 彩带 |
| `title` | 标题 |
| `background` | 背景 |
| `lockAspect` | 锁定宽高比 |
| `border` | 边框 |
| `dropShadow` | 阴影 |
| `visualHeader` | 视觉标头 |

以下代码片段提供了用于预格式化彩带图的 JSON 代码：

```json
"ribbonChart":
{"*":
{
"general": [{... code not shown ...}]
"legend": [{... code not shown ...}],
"categoryAxis": [{
"show": true,
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
"labels": [{
"show": true,
"color": {"solid": {"color": "#ffffff"}},
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
}],
"plotArea": [{... code not shown ...}],
"ribbonChart": [{
"seriesGapRatio": 5,
"colorBands": true,
"bandsTransparency": 50,
"showBorder": true
}],
"title": [{... code not shown ...}],
"background": [{... code not shown ...}],
"lockAspect": [{... code not shown ...}],
"border": [{... code not shown ...}],
"dropShadow": [{... code not shown ...}],
"visualTooltip": [{... code not shown ...}],
"visualHeader": [{... code not shown ...}]        }
}
```

图例关键字见表 7-2。

描述 X 轴分区的 `categoryAxis` 关键字使用表 9-24 中所示的关键字。

**表 9-24** X 轴分区的元素关键字映射

| 元素 | 关键字 |
| --- | --- |
| X 轴 | `show` |
| 颜色 | `color` |
| 文本大小 | `fontSize` |
| 字体 | `fontFamily` |
| 最小类别宽度 | `preferredCategoryWidth` |
| 最大尺寸 | `maxMarginFactor` |
| 合并标签 | `concatenateLabels` |
| 标题 | `showAxisTitle` |
| 样式 | `axisStyle` |
| 标题颜色 | `titleColor` |
| 轴标题 | `titleText` |
| 标题文本大小 | `titleFontSize` |
| (轴) 字体 | `titleFontFamily` |

缩放关键字见表 7-5。

描述数据标签分区的 `labels` 关键字使用表 9-25 中所示的关键字。

**表 9-25** 数据标签分区的元素关键字映射

| 元素 | 关键字 |
| --- | --- |
| 数据标签 | `show` |
| 颜色 | `color` |
| 显示单位 | `labelDisplayUnits` |
| 值小数位数 | `labelPrecision` |
| 方向 | `labelOrientation` |
| 位置 | `labelPosition` |
| 溢出文本 | `labelOverflow` |
| 文本大小 | `fontSize` |
| 字体 | `fontFamily` |
| 显示背景 | `enableBackground` |
| 背景颜色 | `backgroundColor` |
| 透明度 | `backgroundTransparency` |

绘图区关键字见表 7-8。

描述彩带分区的 `ribbonChart` 关键字使用表 9-26 中所示的关键字。

**表 9-26** 彩带分区的元素关键字映射

| 元素 | 关键字 |
| --- | --- |
| 彩带 | `Show` |
| 间距 | `seriesGapRatio` |
| 匹配系列颜色 | `colorBands` |
| 透明度 | `bandsTransparency` |
| 边框 | `showBorder` |

类别关键字见表 7-3。

标签关键字见表 7-6。

绘图区关键字见表 7-8。

你可以在示例文件 `Attributes_RibbonChart.json` 中找到此内容的 JSON 代码。

