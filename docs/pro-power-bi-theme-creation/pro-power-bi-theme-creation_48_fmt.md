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