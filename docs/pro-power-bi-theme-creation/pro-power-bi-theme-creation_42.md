# 饼图

饼图无疑是一种较为简单的图表——因此，它对应着一个同样简洁的 JSON 主题文件。

指定饼图格式属性的关键字是 `pieChart`。

表 9-1 概述了可用于饼图格式的 JSON 关键字，以及每个关键字在 Power BI Desktop 格式窗格中对应的部分。

| 关键字 | 格式部分 |
| --- | --- |
| `general` | 常规 |
| `legend` | 图例 |
| `labels` | 详细标签 |
| `title` | 标题 |
| `background` | 背景 |
| `lockAspect` | 锁定纵横比 |
| `border` | 边框 |
| `dropShadow` | 阴影 |
| `visualHeader` | 视觉对象标头 |

你可以在示例文件 `Attributes_PieChart.json` 中找到相应的 JSON 代码。

以下代码片段提供了用于预格式化饼图的 JSON 代码：

```
"pieChart":
{"*":
{
"general": [{... 代码未显示 ...}]
"legend": [{... 代码未显示 ...}],
"labels": [{
"show": true,
"labelStyle": "All detail labels",
"color": {"solid": {"color": "#000000"}},
"labelDisplayUnits": 1000,
"labelPrecision": 1,
"percentageLabelPrecision": 1,
"fontSize": 8,
"fontFamily": "Arial",
"position": "preferInside",
"overflow": true,
"background": "off"
}],
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

描述详细标签部分的 `labels` 关键字使用了表 9-2 中所示的关键字。

| 元素 | 关键字 |
| --- | --- |
| 详细标签 | `show` |
| 标签样式 | `labelStyle` |
| 颜色 | `color` |
| 显示单位 | `labelDisplayUnits` |
| 值小数位数 | `labelPrecision` |
| % 小数位数 | `percentageLabelPrecision` |
| 文本大小 | `fontSize` |
| 字体系列 | `fontFamily` |
| 标签位置 | `position` |
| 溢出文本 | `overflow` |
| 背景 | `background` |

在饼图或环形图中定义标签样式的 JSON 中的 `labelStyle` 关键字需要表 9-3 中所示的属性之一。

| 值 | 格式弹出选项 |
| --- | --- |
| `Category` | 类别 |
| `DataValue` | 数据值 |
| `Percent of total` | 占总数的百分比 |
| `Category, data value` | 类别，数据值 |
| `Category, percent of total` | 类别，占总数的百分比 |
| `Data value, percent of total` | 数据值，占总数的百分比 |
| `All detail labels` | 所有详细标签 |

**注意：** 与主题文件中的常见做法略有不同的是，用于指定标签样式选项的值包含空格和标点符号。

在饼图或环形图中定义标签位置的 JSON 中的 `position` 关键字需要表 9-4 中所示的属性之一。

| 值 | 格式弹出选项 |
| --- | --- |
| `outside` | 外部 |
| `inside` | 内部 |
| `preferOutside` | 首选外部 |
| `preferInside` | 首选内部 |

**注意：** 有趣的是，用于指定标签位置选项的值必须采用驼峰式大小写。

在饼图或环形图中（在标签部分中）定义背景的 JSON 中的 `background` 关键字需要表 9-5 中所示的属性之一。

| 值 | 格式弹出选项 |
| --- | --- |
| `on` | 开 |
| `off` | 关 |

图例位置选项见表 7-9。

标签显示单位选项见表 7-11。

