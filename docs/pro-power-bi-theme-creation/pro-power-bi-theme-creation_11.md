# 11. 其他视觉样式

有一类视觉对象既不是基于文本的，也不是图表或地图。我将这些视觉对象集中到一章中，以解释它们在主题文件中使用的JSON。这些视觉对象包括：

* 仪表

* `Python` 视觉对象

* `R` 脚本视觉对象

* 关键影响因素视觉对象

* 分解树视觉对象

* `KPI`

本章讨论的每个视觉对象在示例文件夹中都有一个单独的JSON文件，其中包含可用于定义您自己的主题文件的代码。

不可否认，这些视觉对象的集合有些混杂，因为它们几乎没有共同点。让我们来看看它们。

## 仪表

可以使用主题文件标准化 `Power BI` 仪表视觉对象的外观。指定仪表格式化属性的关键字是 `gauge`。

表 11-1 概述了可在仪表格式化中使用的JSON关键字及其在 `Power BI Desktop` 格式窗格中的对应部分。

**表 11-1** `gauge` 关键字

| 关键字 | 格式设置部分 |
| --- | --- |
| `general` | 常规 |
| `dataPoint` | 数据颜色 |
| `labels` | 数据标签 |
| `target` | 目标 |
| `calloutValue` | 标注值 |
| `title` | 标题 |
| `background` | 背景 |
| `lockAspect` | 锁定纵横比 |
| `border` | 边框 |
| `dropShadow` | 阴影 |
| `visualHeader` | 视觉对象标题 |

以下代码片段提供了用于预格式化仪表的JSON代码：

```
"gauge":
{"*":
{
"general":  [{"responsive": true }],
"dataPoint":  [{
"fill": {"solid": {"color": "#E34B20"}},
"target": {"solid": {"color": "#E2EB00"}}
}],
"labels": [{
"show": true,
"color": {"solid": {"color": "#615E55"}},
"labelDisplayUnits": 0,
"labelPrecision": 0,
"fontSize": 8,
"fontFamily": "Arial"
}],
"target": [{
"show": true,
"color": {"solid": {"color": "#E34B20"}},
"labelDisplayUnits": 0,
"labelPrecision": 0,
"fontSize": 8
"fontFamily": "Arial",
}],
"calloutValue": [{
"show": true,
"color": {"solid": {"color": "#F5F4F0"}},
"labelDisplayUnits": 0,
"labelPrecision": 0
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

描述数据颜色部分的 `dataPoint` 关键字使用表 11-2 中所示的关键字。

**表 11-2** 数据颜色部分的关键字映射

| 元素 | 关键字 |
| --- | --- |
| 填充 | `fill` |
| 目标 | `target` |

描述数据标签部分的 `labels` 关键字使用表 11-3 中所示的关键字。

**表 11-3** 数据标签部分的关键字映射

| 元素 | 关键字 |
| --- | --- |
| 数据标签 | `show` |
| 颜色 | `color` |
| 显示单位 | `labelDisplayUnits` |
| 值小数位数 | `labelPrecision` |
| 文本大小 | `fontSize` |
| 字体系列 | `fontFamily` |

描述目标部分的 `target` 关键字使用表 11-4 中所示的关键字。

**表 11-4** 目标部分的关键字映射

| 元素 | 关键字 |
| --- | --- |
| 目标 | `show` |
| 颜色 | `color` |
| 显示单位 | `labelDisplayUnits` |
| 值小数位数 | `labelPrecision` |
| 文本大小 | `fontSize` |
| 字体系列 | `fontFamily` |

描述标注值部分的 `calloutValue` 关键字使用表 11-5 中所示的关键字。

**表 11-5** 标注值部分的关键字映射

| 元素 | 关键字 |
| --- | --- |
| 标注值 | `show` |
| 颜色 | `color` |
| 显示单位 | `labelDisplayUnits` |
| 值小数位数 | `labelPrecision` |

标签和目标显示单位元素在表 7-11 中进行了描述。

您可以在示例文件 `Attributes_Gauge.json` 中找到其JSON代码。

### Python Visual

`Python Visual` 在标准 `Power BI` 视觉对象中较为特殊，因为只有标准视觉元素（背景、锁定纵横比、边框、阴影和视觉标头）可以修改。这是因为所有其他格式均由其创建视觉对象的 `Python` 代码定义。

指定 `Python Visual` 格式属性的关键字是 `pythonVisual`。

表 11-6 概述了在格式化 `Python Visual` 时可使用的JSON关键字，以及每个关键字在 `Power BI Desktop` 格式窗格中对应的部分。

**表 11-6** `Python Visual` 关键字

| 关键字 | 格式部分 |
| --- | --- |
| `general` | 常规 |
| `title` | 标题 |
| `background` | 背景 |
| `lockAspect` | 锁定纵横比 |
| `border` | 边框 |
| `dropShadow` | 阴影 |
| `visualHeader` | 视觉标头 |

你可以在示例文件 `Attributes_Python.json` 中找到对应的JSON代码。

以下代码片段提供了用于预格式化 `Python Visual` 的JSON代码。我知道你之前见过它，但我想强调一点：即使无法设置其他元素，也可以在此视觉对象的主题文件中指定通用视觉元素。

## 可视化对象格式设置

### R Script Visual

`R Visual` 在可格式化内容方面与 `Python Visual` 几乎完全相同。也就是说，只有标准视觉元素（背景、锁定纵横比、边框、阴影和视觉标头）可以修改。所有其他格式均由其创建视觉对象的 R 代码定义。

指定 `R Visual` 格式属性的关键字是 `scriptVisual`。

表 11-7 概述了在格式化 `R Visual` 时可使用的 JSON 关键字，以及每个关键字在 Power BI Desktop 格式窗格中对应的部分。

**表 11-7 R Visual 关键字**

| 关键字 | 格式部分 |
| --- | --- |
| `general` | 常规 |
| `title` | 标题 |
| `background` | 背景 |
| `lockAspect` | 锁定纵横比 |
| `border` | 边框 |
| `dropShadow` | 阴影 |
| `visualHeader` | 视觉标头 |

你可以在示例文件 `Attributes_R.json` 中找到对应的 JSON 代码。

以下代码片段提供了用于预格式化 `R Visual` 的 JSON 代码：

```
"scriptVisual":
{"*":
{
"general":  [{"responsive": true }],
"plotArea": [{... code not shown ...}],
"title": [{... code not shown ...}],
"background": [{... code not shown ...}],
"lockAspect": [{... code not shown ...}],
"border": [{... code not shown ...}],
"dropShadow": [{... code not shown ...}],
"visualHeader": [{... code not shown ...}]
}
}
```

### Key Influencers Visual

`Key Influencers Visual` 允许你设置三个完全特定于此视觉对象类型的主题选项。它们是：
*   分析
*   分析视觉颜色
*   钻取视觉颜色

指定 `Key Influencers Visual` 格式属性的关键字是 `keyDriversVisual`。

表 11-8 概述了在格式化 `Key Influencers Visual` 时可使用的 JSON 关键字，以及每个关键字在 Power BI Desktop 格式窗格中对应的部分。

**表 11-8 Key Influencers Visual 关键字**

| 关键字 | 格式部分 |
| --- | --- |
| `general` | 常规 |
| `keyDrivers` | 分析 |
| `keyInfluencersVisual` | 分析视觉颜色 |
| `keyDriversDrillVisual` | 钻取视觉颜色 |
| `title` | 标题 |
| `background` | 背景 |
| `lockAspect` | 锁定纵横比 |
| `border` | 边框 |
| `dropShadow` | 阴影 |
| `visualHeader` | 视觉标头 |

以下代码片段提供了用于预格式化 `Key Influencers Visual` 的 JSON 代码：

```
"keyDriversVisual":
{"*":
{
"general":  [{"responsive": true }],
"keyDrivers": [{
"allowKeyDrivers": true,
"allowProfiles": true,
"allowKeyDriversCounting": true,
"countType": "relative"
}],
"keyInfluencersVisual": [{
"primaryColor": {"solid": {"color": "#615E55"}},
"primaryFontColor": {"solid": {"color": "#00AC98"}},
"secondaryColor": {"solid": {"color": "#73268c"}},
"secondaryFontColor": {"solid": {"color": "#465437"}},
"canvasColor": {"solid": {"color": "#465437"}},
"fontColor": {"solid": {"color": "#FFFFFF"}}
}],
"keyDriversDrillVisual": [{
"defaultColor": {"solid": {"color": "#465437"}},
"referenceLineColor": {"solid": {"color": "#00AC98"}}
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

描述分析部分的 `keyDrivers` 关键字使用表 11-9 中所示的关键字。

**表 11-9 分析部分的元素关键字映射**

| 元素 | 关键字 |
| --- | --- |
| 启用关键影响因素 | `allowKeyDrivers` |
| 启用分段 | `allowProfiles` |
| 启用计数 | `allowKeyDriversCounting` |
| 计数类型 | `countType` |

描述分析视觉颜色部分的 `keyInfluencersVisual` 关键字使用表 11-10 中所示的关键字。

**表 11-10 分析视觉颜色部分的关键字映射**

| 元素 | 关键字 |
| --- | --- |
| 主要颜色 | `primaryColor` |
| 主要文本颜色 | `primaryFontColor` |

## 次要颜色和字体颜色

| 次要颜色 | `secondaryColor` |
| --- | --- |
| 次要文本颜色 | `secondaryFontColor` |
| 背景颜色 | `canvasColor` |
| 字体颜色 | `fontColor` |

描述钻取视觉颜色部分的 `keyDriversDrillVisual` 关键字使用表 11-11 中所示的关键字。

### 表 11-11 钻取视觉颜色部分的关键字映射

| 元素 | 关键字 |
| --- | --- |
| 默认颜色 | `defaultColor` |
| 参考线颜色 | `referenceLineColor` |

`keyDrivers` 部分中的 `countType` 关键字需要表 11-12 中所示的属性之一。

### 表 11-12 图例位置选项

| 值 | 格式弹出选项 |
| --- | --- |
| `absolute` | 绝对 |
| `relative` | 相对 |

你可以在示例文件 `Attributes_KeyInfluencers.json` 中找到对应的 JSON 代码。

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


