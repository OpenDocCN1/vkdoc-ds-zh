# 9. 其他图表视觉样式

要完成主题文件图表格式的完整学习，您需要检查最后一组图表类型的 JSON。这些图表包括：

-   饼图
-   环形图
-   树状图
-   瀑布图
-   漏斗图
-   功能区图

所有这些图表都比您目前看到的一些图表简单——在某些情况下要简单得多。尽管如此，在本章中您会发现一些新的关键字，这些关键字特定于描述这些图表类型格式的 JSON。

本章讨论的所有图表类型在可供下载的示例文件夹中都有一个单独的 JSON 文件。每个文件都是独立的，可用于格式化相关的图表类型。

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

```json
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

注意：与主题文件中的常见做法略有不同的是，用于指定标签样式选项的值包含空格和标点符号。

在饼图或环形图中定义标签位置的 JSON 中的 `position` 关键字需要表 9-4 中所示的属性之一。

| 值 | 格式弹出选项 |
| --- | --- |
| `outside` | 外部 |
| `inside` | 内部 |
| `preferOutside` | 首选外部 |
| `preferInside` | 首选内部 |

注意：有趣的是，用于指定标签位置选项的值必须采用驼峰式大小写。

在饼图或环形图中（在标签部分中）定义背景的 JSON 中的 `background` 关键字需要表 9-5 中所示的属性之一。

| 值 | 格式弹出选项 |
| --- | --- |
| `on` | 开 |
| `off` | 关 |

图例位置选项见表 7-9。

标签显示单位选项见表 7-11。

# 环形图

就格式化而言，环形图与饼图几乎完全相同。然而，它有一个额外的功能。这就是你可以在下面给出的 JSON 中看到的 `slices` 关键字定义的 `inner radius`（内半径）。

指定散点图格式属性的关键字是 `donutChart`。

表 9-6 概述了可用于环形图格式的 JSON 关键字，以及每个关键字在 Power BI Desktop 格式窗格中对应的部分。

| 关键字 | 格式部分 |
| --- | --- |
| `general` | 常规 |
| `legend` | 图例 |
| `slices` | 形状 |
| `title` | 标题 |
| `background` | 背景 |
| `lockAspect` | 锁定纵横比 |
| `border` | 边框 |
| `dropShadow` | 阴影 |
| `visualHeader` | 视觉对象标头 |

以下代码片段提供了用于预格式化环形图的 JSON 代码：

```
"donutChart":
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
"fontFamily": "Arial",
"fontSize": 8,
"position": "preferInside"
}],
"slices": [{
"innerRadiusRatio": 20
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

用于形状部分的 `slices` 关键字使用了表 9-7 中所示的关键字。

| 元素 | 关键字 |
| --- | --- |
| 内半径 | `innerRadiusRatio` |

图例关键字见表 7-2。

图例位置选项见表 7-9。

标签显示单位选项见表 7-11。

你可以在文件 `Attributes_Donut.json` 中找到环形图 JSON 格式的完整定义。

## 树状图

作为较为简单的可视化图表之一（至少在显示属性方面），树状图除了所有可视化图表共有的元素外，仅包含三个元素。它们是：

- 图例

- 数据标签

- 类别标签

其中，后两者包含树状图特有的元素。

指定散点图格式化属性的关键字是 `treemap`。

表 9-8 概述了可用于树状图格式化的 JSON 关键字。

**表 9-8** 树状图关键字

| 关键字 | 格式化部分 |
| --- | --- |
| `general` | 常规 |
| `legend` | 图例 |
| `labels` | 数据标签 |
| `categoryLabels` | 类别标签 |
| `title` | 标题 |
| `background` | 背景 |
| `lockAspect` | 锁定纵横比 |
| `border` | 边框 |
| `dropShadow` | 阴影 |
| `visualHeader` | 视觉对象标头 |

你可以在取自示例文件 `Attributes_Treemap.json` 的以下代码片段中看到这一点：

```
"treemap":
{"*":
{
"general": [{... 未显示代码 ...}]
"legend": [{... 未显示代码 ...}],
"labels": [{
"show": true,
"color": {"solid": {"color": "#000000"}},
"labelDisplayUnits": 1000,
"labelPrecision": 1,
"fontFamily": "Arial",
"fontSize": 10
}],
"categoryLabels": [{
"show": true,
"color": {"solid": {"color": "#ffffff"}},
"fontFamily": "Arial",
"fontSize": 9
}],
"title": [{... 未显示代码 ...}],
"background": [{... 未显示代码 ...}],
"lockAspect": [{... 未显示代码 ...}],
"border": [{... 未显示代码 ...}],
"dropShadow": [{... 未显示代码 ...}],
"visualTooltip": [{... 未显示代码 ...}],
"visualHeader": [{... 未显示代码 ...}]
}
}
```

图例关键字见表 7-2。

描述数据标签部分的 `labels` 关键字使用了表 9-9 中列出的关键字。

**表 9-9** 数据标签部分的元素关键字映射

| 元素 | 关键字 |
| --- | --- |
| 数据标签 | `show` |
| 颜色 | `color` |
| 显示单位 | `labelDisplayUnits` |
| 值小数位数 | `labelPrecision` |
| 文本大小 | `fontFamily` |
| 字体系列 | `fontSize` |

描述类别标签部分的 `categoryLabels` 关键字使用了表 9-10 中列出的关键字。

**表 9-10** 类别标签部分的元素关键字映射

| 元素 | 关键字 |
| --- | --- |
| 类别标签 | `show` |
| 颜色 | `color` |
| 文本大小 | `fontFamily` |
| 字体系列 | `fontSize` |

标签显示单位选项见表 7-11。

## 瀑布图

瀑布图是一种包含一些有趣的新格式化元素的图表。具体来说，它包含了你需要能够配置的涨跌色部分。

指定散点图格式化属性的关键字是 `waterfallChart`。

表 9-11 概述了可用于瀑布图的 JSON 关键字，以及每个关键字在 Power BI Desktop 格式窗格中对应的部分。

**表 9-11** 瀑布图关键字

| 关键字 | 格式化部分 |
| --- | --- |
| `general` | 常规 |
| `legend` | 图例 |
| `categoryAxis` | X 轴 |
| `valueAxis` | Y 轴 |
| `labels` | 数据标签 |
| `plotArea` | 绘图区 |
| `sentimentColors` | 涨跌色 |
| `title` | 标题 |
| `background` | 背景 |
| `lockAspect` | 锁定纵横比 |
| `border` | 边框 |
| `dropShadow` | 阴影 |
| `visualHeader` | 视觉对象标头 |

以下代码片段提供了用于预格式化瀑布图的 JSON 代码：

# 瀑布图

瀑布图的JSON配置示例如下：

```json
"waterfallChart":
{"*":
{
"general": [{... 未显示代码 ...}]
"legend": [{... 未显示代码 ...}],
"categoryAxis": [{
"show": true,
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
"position": "Left",
"start": null,
"end": null,
"labelColor": {"solid": {"color": "#949494"}},
"fontSize": 8,
"fontFamily": "Arial",
"labelDisplayUnits": 1000,
"labelPrecision": 2,
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
"labels": [{
"show": true,
"color": { "solid": { "color": "#EEEEEE"}},
"labelDisplayUnits": 1000,
"labelPrecision": 2,
"labelOrientation": "vertical",
"labelPosition": "OutsideEnd",
"fontSize": 11,
"fontFamily": "Arial",
"enableBackground": true,
"backgroundColor": { "solid": { "color": "#949494"}},
"backgroundTransparency": 30
}],
"plotArea": [{
"transparency": 0,
"scaling": "Fill"
}],
"sentimentColors": [{
"increaseFill": { "solid": { "color": "#949494"}},
"decreaseFill": { "solid": { "color": "#D1CAB8"}},
"totalFill": { "solid": { "color": "#E2EB00"}}
}],
"title": [{... 未显示代码 ...}],
"background": [{... 未显示代码 ...}],
"lockAspect": [{... 未显示代码 ...}],
"border": [{... 未显示代码 ...}],
"dropShadow": [{... 未显示代码 ...}],
"visualTooltip": [{... 未显示代码 ...}],
"visualHeader": [{... 未显示代码 ...}]
}
}
```

图例关键字见表 7-2。

## X 轴

描述 X 轴部分的 `categoryAxis` 关键字使用了表 9-12 中列出的关键字。

### `表 9-12` X 轴部分的元素关键字映射

| 元素 | 关键字 |
| --- | --- |
| X 轴 | `show` |
| 颜色 | `color` |
| 文本大小 | `fontSize` |
| 字体系列 | `fontFamily` |
| 最小类别宽度 | `preferredCategoryWidth` |
| 最大大小 | `maxMarginFactor` |
| 内部填充 | `innerPadding` |
| 标题 | `showAxisTitle` |
| 样式 | `axisStyle` |
| 标题颜色 | `titleColor` |
| 轴标题 | `titleText` |
| 标题文本大小 | `titleFontSize` |
| (标题) 字体系列 | `titleFontFamily` |

## Y 轴

描述 Y 轴部分的 `valueAxis` 关键字使用了表 9-13 中列出的关键字。

### `表 9-13` Y 轴部分的元素关键字映射

| 元素 | 关键字 |
| --- | --- |
| Y 轴 | `show` |
| 位置 | `position` |
| 起点 | `start` |
| 终点 | `end` |
| 颜色 | `color` |
| 文本大小 | `fontSize` |
| 字体系列 | `fontFamily` |
| 显示单位 | `labelDisplayUnits` |
| 数值小数位数 | `labelPrecision` |
| 标题 | `showAxisTitle` |
| 样式 | `axisStyle` |
| 标题颜色 | `labelColor` |
| 轴标题 | `titleText` |
| 标题文本大小 | `titleFontSize` |
| (标题) 字体系列 | `titleFontFamily` |
| 网格线 | `gridLineShow` |
| (网格线) 颜色 | `gridLineColor` |
| 描边宽度 | `gridLineThickness` |
| 线条样式 | `gridlineStyle` |

## 标签

描述标签（labels）部分的 `labels` 关键字使用表 9-14 中所示的关键字。

### `表 9-14` 标签部分的元素关键字映射

| 元素 | 关键字 |
| --- | --- |
| 数据标签 | `show` |
| 颜色 | `color` |
| 显示单位 | `labelDisplayUnits` |
| 数值小数位数 | `labelPrecision` |
| 方向 | `labelOrientation` |
| 位置 | `labelPosition` |
| 文本大小 | `fontSize` |
| 字体系列 | `fontFamily` |
| 显示背景 | `enableBackground` |
| 背景颜色 | `backgroundColor` |
| 透明度 | `backgroundTransparency` |

分类（Categories）关键字见表 7-3。数值（Values）关键字见表 7-4。标签（Labels）关键字见表 7-6。总计（Totals）关键字见表 7-7。绘图区（Plot area）关键字见表 7-8。

## 情感颜色

描述情感颜色（sentiment colors）部分的 `sentimentColors` 关键字需要使用表 9-15 中所示的关键字之一。

### `表 9-15` 情感颜色部分的元素关键字映射

| 元素 | 关键字 |
| --- | --- |
| 增加 | `increaseFill` |
| 减少 | `decreaseFill` |
| 总计 | `totalFill` |

你可以在示例文件 `Attributes_Waterfall.json` 中找到对应的 JSON 代码。情感颜色的定义简单明了——你只需为瀑布图的每个方面定义所需的颜色，否则你需要在 Power BI Desktop 中直接设置格式。

工具提示（Tooltip）关键字见表 6-5。图例位置选项见表 7-9。类别轴位置选项见表 7-10。值轴显示单位选项见表 7-11。值轴样式选项见表 7-16。值轴网格线样式选项见表 7-13。标签位置选项见表 7-12。

# 漏斗图

本章中我们将要介绍的倒数第二个图表是漏斗图。这种图表类型仅添加了一个新的部分关键字：`percentBarLabel`。

指定漏斗图格式属性的关键字是 `funnel`。

表 9-16 概述了在漏斗图格式设置中可以使用的 JSON 关键字，以及每个关键字在 Power BI Desktop 格式窗格中对应的部分。

### `表 9-16` 漏斗图关键字

| 关键字 | 格式设置部分 |
| --- | --- |
| `general` | 常规 |
| `categoryAxis` | 类别标签 |

## 漏斗图配置说明

| 属性 | 描述 |
| :--- | :--- |
| `dataPoint` | 数据颜色 |
| `labels` | 数据标签 |
| `percentBarLabel` | 转化率标签 |
| `title` | 标题 |
| `background` | 背景 |
| `lockAspect` | 锁定纵横比 |
| `border` | 边框 |
| `dropShadow` | 阴影 |
| `visualHeader` | 视觉对象页眉 |

以下代码片段提供了用于预格式化漏斗图的 JSON 代码：

```json
"funnel":
{"*":
{
"general": [{... 代码未显示 ...}]
"legend": [{... 代码未显示 ...}],
"categoryAxis": [{
"show": true,
"color": {"solid": {"color": "#949494"}}",
"fontSize": 8,
"fontFamily": "Arial
}],
"labels": [{
"show": true,
"style": "PercentFirst",
"color": {"solid": {"color": "#ffffff"}},
"labelDisplayUnits": 1,
"labelPrecision": 2,
"labelPosition": "InsideCenter",
"fontFamily": "Arial",
"fontSize": 7
}],
"percentBarLabel": [{
"show": true,
"color": {"solid": {"color": "#12239E"}},
"fontFamily": "Arial",
"fontSize": 16
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

### 类别标签

描述类别标签（Category labels）部分的 `categoryAxis` 关键字使用表 9-17 中所示的关键字。

**表 9-17** 类别标签部分的元素关键字映射

| 元素 | 关键字 |
| :--- | :--- |
| 类别标签 | `show` |
| 颜色 | `color` |
| 文本大小 | `fontSize` |
| 字体系列 | `fontFamily` |

### 数据颜色

描述数据颜色（Data colors）部分的 `dataPoint` 关键字使用表 9-18 中所示的关键字。

**表 9-18** 数据颜色部分的元素关键字映射

| 元素 | 关键字 |
| :--- | :--- |
| 全部显示 | `showAllDataPoints` |
| 默认颜色 | `defaultColor` |

### 数据标签

描述数据标签（Data labels）部分的 `labels` 关键字使用表 9-19 中所示的关键字。

**表 9-19** 数据标签部分的元素关键字映射

| 元素 | 关键字 |
| :--- | :--- |
| 数据标签 | `show` |
| 标签样式 | `style` |
| 颜色 | `color` |
| 显示单位 | `labelDisplayUnits` |
| 数值小数位数 | `labelPrecision` |
| 位置 | `labelPosition` |
| 文本大小 | `fontSize` |
| 字体系列 | `fontFamily` |

### 转化率标签

描述转化率标签（Conversion Rate Label）部分的 `percentBarLabel` 关键字使用表 9-20 中所示的关键字。

**表 9-20** 转化率标签部分的元素关键字映射

| 元素 | 关键字 |
| :--- | :--- |
| 转化率标签 | `Show` |
| 颜色 | `Color` |
| 文本大小 | `fontSize` |
| 字体系列 | `fontFamily` |

### 标签样式与位置

在漏斗图中定义标签位置的 JSON 中的 `style` 关键字需要使用表 9-21 中所示的属性之一。

**表 9-21** 标签样式选项

| 值 | 格式设置弹出选项 |
| :--- | :--- |
| `Data` | 数据值 |
| `Percent of first` | 第一个的百分比 |
| `Percent of previous` | 上一个的百分比 |
| `Data, percent of first` | 数据值，第一个的百分比 |
| `Data, percent of previous` | 数据值，上一个的百分比 |

在漏斗图中定义标签样式的 JSON 中的 `position` 关键字需要使用表 9-22 中所示的属性之一。

**表 9-22** 标签位置选项

| 值 | 格式设置弹出选项 |
| :--- | :--- |
| `OutsideEnd` | 外部终点 |
| `InsideCenter` | 内部中心 |

你可以在示例文件 `Attributes_FunnelChart.json` 中找到对应的 JSON 代码。

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

# 结论

本章介绍了格式化其余六种图表类型所需的 JSON 代码。你了解了为饼图、环形图、树状图、瀑布图、漏斗图和彩带图准备 JSON 的具体细节。

现在，关于图表类型的三章巡礼已经结束，是时候进入 Power BI 中另一类截然不同的视觉对象——地图了。这些将是下一章的主题。

