# 分解树视觉对象

本章我们将介绍倒数第二个视觉对象——分解树视觉对象。同样，你可以在主题文件中设置的格式化属性本质上专用于此视觉类型。

指定分解树视觉对象格式化属性的关键字是 `decompositionTreeVisual`。

### 表 11-13 分解树视觉对象关键字

| 关键字 | 格式化部分 |
| --- | --- |
| `general` | 常规 |
| `analysis` | 分析 |
| `tree` | 树 |
| `dataBars` | 数据条 |
| `categoryLabels` | 类别标签 |
| `dataLabels` | 数据标签 |
| `levelHeader` | 树 |
| `title` | 标题 |
| `background` | 背景 |
| `lockAspect` | 锁定纵横比 |
| `border` | 边框 |
| `dropShadow` | 阴影 |
| `visualHeader` | 视觉对象标头 |

以下代码片段提供了用于预格式化分解树视觉对象的 JSON 代码：

```
"decompositionTreeVisual":
{"*":
{
"general":  [{"responsive": true }],
"analysis": [{
"aiEnabled": true,
"aiMode": "relative"
}],
"tree": [{
"density": "sparse",
"accentColor": { "solid": { "color": "#F5F4F0"}},
"connectorDefaultColor": { "solid": { "color": "#118DFF"}},
"connectorType": "round",
"defaultClickAction": "toggle",
"responsiveLayout": true,
"barsPerLevel": 10
}],
"dataBars": [{
"positiveBarColor": { "solid": { "color": "#12239E"}},
"negativeBarColor": { "solid": { "color": "#DD001E"}},
"dataBarBackgroundColor": { "solid": { "color": "#118DFF"}},
"dataBarWidthPercent": 100,
"dataBarScalingType": "topNode",
"axisStart": null,
"axisEnd": null
}],
"categoryLabels": [{
"categoryLabelFontFamily": "Arial",
"categoryLabelFontSize": 12,
"categoryLabelFontColor": { "solid": { "color": "#808080"}}
}],
"dataLabels": [{
"dataLabelFontFamily": "Arial",
"dataLabelFontSize": 14,
"dataLabelFontColor": { "solid": { "color": "#808080"}},
"dataLabelDisplayUnits": 1000,
"dataLabelPrecision": 1
}],
"levelHeader": [{
"levelHeaderBackgroundColor": { "solid": { "color": "#000000"}},
"levelTitleFontFamily": "Arial",
"levelTitleFontSize": 20,
"levelTitleFontColor": { "solid": { "color": "#808080"}},
"showSubtitles": false,
"levelSubtitleFontColor": { "solid": { "color": "#FFFFFF"}},
"levelSubtitleFontFamily": "Arial",
"levelSubtitleFontSize": 18
}],
"title": [{... 代码未显示 ...}],
"background": [{... 代码未显示 ...}],
"lockAspect": [{... 代码未显示 ...}],
"border": [{... 代码未显示 ...}],
"dropShadow": [{... 代码未显示 ...}],
"visualHeader": [{... 代码未显示 ...}]
}
}
```

描述分析部分的 `analysis` 关键字使用了表 11-14 中列出的关键字。

### 表 11-14 分析部分的关键字映射

| 元素 | 关键字 |
| --- | --- |
| 启用 AI 拆分 | `aiEnabled` |
| 分析类型 | `aiMode` |

描述树部分的 `tree` 关键字使用了表 11-15 中列出的关键字。

### 表 11-15 树部分的关键字映射

| 元素 | 关键字 |
| --- | --- |
| 密度 | `density` |
| 主题色 | `accentColor` |
| 未选定连接线 | `connectorDefaultColor` |
| 连接线形状 | `connectorType` |
| 默认操作 | `defaultClickAction` |
| 响应式 | `responsiveLayout` |
| 最大显示条数 | `barsPerLevel` |

描述数据条部分的 `dataBars` 关键字使用了表 11-16 中列出的关键字。

### 表 11-16 数据条部分的元素关键字映射

| 元素 | 关键字 |
| --- | --- |
| 正条形图 | `positiveBarColor` |
| 负条形图 | `negativeBarColor` |
| 条形图背景 | `dataBarBackgroundColor` |
| 大小 | `dataBarWidthPercent` |
| 缩放至 | `dataBarScalingType` |
| 开始 | `axisStart` |
| 结束 | `axisEnd` |

描述类别标签部分的 `categoryLabels` 关键字使用了表 11-17 中列出的关键字。

### 表 11-17 类别标签部分的元素关键字映射

| 元素 | 关键字 |
| --- | --- |
| 字体系列 | `categoryLabelFontFamily` |
| 文本大小 | `categoryLabelFontSize` |
| 文本颜色 | `categoryLabelFontColor` |

描述数据标签部分的 `dataLabels` 关键字使用了表 11-18 中列出的关键字。

### 表 11-18 数据标签部分的关键字映射

| 元素 | 关键字 |
| --- | --- |
| 字体系列 | `dataLabelFontFamily` |
| 文本大小 | `dataLabelFontSize` |
| 文本颜色 | `dataLabelFontColor` |
| 显示单位 | `dataLabelDisplayUnits` |
| 值小数位数 | `dataLabelPrecision` |

描述级别标头部分的 `levelHeader` 关键字使用了表 11-19 中列出的关键字。

### 表 11-19 级别标头部分的关键字映射

| 元素 | 关键字 |
| --- | --- |
| 背景颜色 | `levelHeaderBackgroundColor` |
| 标题字体系列 | `levelTitleFontFamily` |
| 标题字体大小 | `levelTitleFontSize` |
| 标题颜色 | `levelTitleFontColor` |
| 显示副标题 | `showSubtitles` |
| 副标题字体系列 | `levelSubtitleFontColor` |
| 副标题字体大小 | `levelSubtitleFontFamily` |
| 副标题颜色 | `levelSubtitleFontSize` |

分析部分中的 `aiMode` 关键字需要表 11-20 中显示的属性之一。

### 表 11-20 AI 模式选项

| 值 | 格式化弹出选项 |
| --- | --- |
| `absolute` | 绝对值 |
| `relative` | 相对值 |

树 JSON 中的 `density` 关键字需要表 11-21 中显示的属性之一。

### 表 11-21 密度选项

| 值 | 格式化弹出选项 |
| --- | --- |

# 格式化分解树和 KPI 的 JSON 关键字

## 分解树

树 JSON 中的 `connectorType` 关键字需要表 11-22 中显示的属性之一。

### 表 11-22 连接线类型选项

| 值 | 格式化弹出选项 |
| --- | --- |
| `default` | 默认 |
| `round` | 圆形 |

树 JSON 中的 `defaultAction` 关键字需要表 11-23 中显示的属性之一。

### 表 11-23 默认操作选项

| 值 | 格式化弹出选项 |
| --- | --- |
| `toggle` | 折叠 |
| `click` | 默认 |

数据条 JSON 中的 `dataBarScalingType` 关键字需要表 11-24 中显示的属性之一。

### 表 11-24 数据条缩放类型选项

| 值 | 格式化弹出选项 |
| --- | --- |
| `topNode` | 顶部节点 |
| `parentNode` | 父节点 |
| `levelMaximum` | 级别最大值 |

同样，无法在 JSON 主题文件中定义条件格式。

数据标签显示单位选项如表 7-11 所示。

你可以在示例文件 `Attributes_DecompositionTree.json` 中找到对应的 JSON 代码。

## KPI

本章中你将要看到的最后一段 JSON 代码是针对 KPI 可视化对象的。它通过主题文件中的 `kpi` 关键字引入。

表格 11-25 概述了在格式化分解树可视化对象时可以使用的 JSON 关键字，以及每个关键字在 Power BI Desktop 格式窗格中对应的部分。

### 表 11-25 KPI 可视化对象关键字

| 关键字 | 格式部分 |
| --- | --- |
| `general` | 常规 |
| `indicator` | 指标 |
| `trendline` | 趋势轴 |
| `goals` | 目标 |
| `status` | 颜色编码 |
| `title` | 标题 |
| `background` | 背景 |
| `lockAspect` | 锁定纵横比 |
| `border` | 边框 |
| `dropShadow` | 阴影 |
| `visualHeader` | 视觉对象标头 |

格式化 KPI 的代码如下：

```json
"kpi":
{"*" :
{
"general":  [{"responsive": true }],
"indicator":    [{
"indicatorDisplayUnits": 0,
"indicatorPrecision": 2,
"fontSize": 9,
"fontColor": {"solid": {"color": "#F5F4F0"}},
"fontFamily": "Arial",
"horizontalAlignment": "left",
"verticalAlignment": "top",
"showIcon": true,
"iconSize": 15
}],
"trendline":   [{
"show": true,
"transparency": 30
}],
"goals": [{
"showGoal": true,
"goalText": "Target",
"showDistance": true,
"goalFontColor": {"solid": {"color": "#000000"}},
"goalFontFamily": "Arial",
"fontSize": 9,
"showDistance": true,
"distanceLabel": "Value",
"labelPrecision": 2,
"distanceFontFamily": "Arial",
"distanceFontColor": {"solid": {"color": "#FFFFFF"}},
"titleFontSize": 13,
"direction": "Low is good"
}] ,
"status": [{
"direction": "Positive",
"goodColor": {"solid": {"color": "#F5F4F0"}},
"neutralColor": {"solid": {"color": "#949494"}},
"badColor": {"solid": {"color": "#FF001E"}}
}],
"title": [{... code not shown ...}],
"background": [{... code not shown ...}],
"lockAspect": [{... code not shown ...}],
"border": [{... code not shown ...}],
"dropShadow": [{... code not shown ...}],
"visualHeader": [{... code not shown ...}]
}
}
```

描述指标部分的 `indicator` 关键字使用了表 11-26 中所示的关键字。

### 表 11-26 指标部分的关键字映射

| 元素 | 关键字 |
| --- | --- |
| 显示单位 | `indicatorDisplayUnits` |
| 值的小数位数 | `indicatorPrecision` |
| 文本大小 | `fontSize` |
| 字体颜色 | `fontColor` |
| 字体系列 | `fontFamily` |
| 水平对齐 | `horizontalAlignment` |
| 垂直对齐 | `verticalAlignment` |
| 显示图标 | `showIcon` |
| 图标大小 | `iconSize` |

描述趋势轴部分的 `trendline` 关键字使用了表 11-27 中所示的关键字。

### 表 11-27 趋势轴部分的关键字映射

| 元素 | 关键字 |
| --- | --- |
| 趋势轴 | `show` |
| 透明度 | `transparency` |

描述目标部分的 `goals` 关键字使用了表 11-28 中所示的关键字。

### 表 11-28 目标部分的元素关键字映射

| 元素 | 关键字 |
| --- | --- |
| 目标 | `showGoal` |
| 标签 | `goalText` |
| 字体颜色 | `goalFontColor` |
| 字体系列 | `goalFontFamily` |
| 文本大小 | `fontSize` |
| 距离 | `showDistance` |
| 标签 | `distanceLabel` |
| 值的小数位数 | `labelPrecision` |
| (距离) 字体颜色 | `distanceFontColor` |
| (距离) 字体系列 | `distanceFontFamily` |
| (距离) 文本大小 | `titleFontSize` |
| 距离方向 | `direction` |

描述颜色编码部分的 `status` 关键字使用了表 11-29 中所示的关键字。

### 表 11-29 颜色编码部分的关键字映射

| 元素 | 关键字 |
| --- | --- |
| 方向 | `direction` |
| 良好颜色 | `goodColor` |
| 中性颜色 | `neutralColor` |
| 不佳颜色 | `badColor` |

目标 JSON 中的 `distanceLabel` 关键字需要使用表 11-30 中所示的一个属性。

### 表 11-30 距离标签选项

| 值 | 格式弹出选项 |
| --- | --- |
| `Value` | 值 |
| `Percent` | 百分比 |
| `Value, percent` | 值, 百分比 |

目标 JSON 中的 `direction` 关键字需要使用表 11-31 中所示的一个属性。

### 表 11-31 目标方向选项

| 值 | 格式弹出选项 |
| --- | --- |
| `High is good` | 增长为正 |
| `Low is good` | 下降为正 |

颜色编码（状态）JSON 中的 `direction` 关键字需要使用表 11-32 中所示的一个属性。

### 表 11-32 颜色编码方向选项

| 值 | 格式弹出选项 |
| --- | --- |
| `Positive` | 高值为好 |
| `Negative` | 低值为好 |

你可以在示例文件 `Kpi.json` 中找到用于格式化 KPI 的完整 JSON 代码。

## 结论

在本章中，你了解了如何为仪表、Python 可视化对象、R 脚本可视化对象、关键影响因素可视化对象、分解树和 KPI 定义 JSON 格式。


现在，你可以继续学习最后一系列可视化格式属性——那些用于定义实际仪表板本身以及不用于数据展示的可视化对象的属性。这是下一章的主题，也是详细讲解可视化对象格式化的最后一章。
