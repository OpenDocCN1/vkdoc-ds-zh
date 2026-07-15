# 10. 地图

本章将探讨一种与前几章所介绍内容完全不同的视觉对象类型。在这里，我们将研究如何使用主题文件预格式化地图。你将了解如何准备 JSON 来定义以下视觉对象类型的呈现：

*   地图
*   着色地图
*   形状地图

本章讨论的每种地图类型在示例文件夹中都有一个单独的 JSON 文件，其中包含可用于定义自己的主题文件的代码。

## 地图

本章讨论的三种地图类型在示例文件夹中各自拥有独立的 JSON 文件，其中包含可用于定义自定义主题文件的代码。那么，让我们继续这段旅程吧！

在 Power BI 地图中，您可以设置其外观（除了几乎所有视觉对象都通用的标准元素之外），具体包括：

- 图例
- 数据颜色
- 类别
- 气泡
- 地图控件
- 地图样式
- 热力图

指定地图格式化属性的关键字是 `map`。

表 10-1 概述了您在地图格式化中可使用的 JSON 关键字及其在 Power BI Desktop 格式窗格中对应的部分。

**表 10-1** 地图图表关键字

| 关键字 | 格式设置部分 |
| --- | --- |
| `general` | 常规 |
| `legend` | 图例 |
| `dataPoint` | 数据颜色 |
| `categoryLabels` | 类别 |
| `bubbles` | 气泡 |
| `mapControls` | 地图控件 |
| `mapStyles` | 地图样式 |
| `Title` | 标题 |
| `background` | 背景 |
| `lockAspect` | 锁定纵横比 |
| `border` | 边框 |
| `dropShadow` | 阴影 |
| `visualHeader` | 视觉对象标头 |

您可以在示例文件 `Attributes_Map.json` 中找到对应的 JSON 代码。

以下代码片段提供了用于预格式化地图的 JSON 代码。由于地图是一个新概念，我倾向于展示几乎所有的 JSON 代码：

```
"map":{"*":
{
"general": [{... 此处代码未显示 ...}]
"legend": [{... 此处代码未显示 ...}],
"categoryLabels": [{
"show": true,
"color": {"solid": {"color": "#949494"}},
"fontSize": 10,
"fontFamily": "Arial",
"enableBackground": true,
"backgroundColor": {"solid": {"color": "#ffffff"}},
"transparency": 30
}],
"bubbles": [{
"bubbleSize": 15
}],
"mapControls": [{
"autoZoom": true,
"showZoomButtons": true
}],
"mapStyles": [{
"mapTheme": "grayscale",
"showLabels": true
}],
"heatMap":  [{
"show": true,
"filterRadius": 8,
"transparency": 10,
"unit": "meters",
"color": {"solid": {"color": "#F5F4F0"}},
"color50": {"solid": {"color": "#949494"}},
"color100": {"solid": {"color": "#D5D5D5"}}
}],
"title": [{... 此处代码未显示 ...}],
"background": [{... 此处代码未显示 ...}],
"lockAspect": [{... 此处代码未显示 ...}],
"border": [{... 此处代码未显示 ...}],
"dropShadow": [{... 此处代码未显示 ...}],
"visualHeader": [{... 此处代码未显示 ...}]
}
}
```

图例关键字见表 7-2。

描述类别部分的 `categoryLabels` 关键字使用了表 10-2 中所示的关键字。

**表 10-2** 类别标签部分关键字映射

| 元素 | 关键字 |
| --- | --- |
| 类别标签 | `show` |
| 颜色 | `color` |
| 文本大小 | `fontSize` |
| 字体 | `fontFamily` |
| 显示背景 | `enableBackground` |
| 背景颜色 | `backgroundColor` |
| 透明度 | `transparency` |

描述气泡部分的 `bubbles` 关键字使用了表 10-3 中所示的关键字。

**表 10-3** 气泡部分关键字映射

| 元素 | 关键字 |
| --- | --- |
| 大小 | `bubbleSize` |

描述地图控件部分的 `mapControls` 关键字使用了表 10-4 中所示的关键字。

**表 10-4** 地图控件部分关键字映射

| 元素 | 关键字 |
| --- | --- |
| 地图控件 | `show` |
| 自动缩放 | `autoZoom` |
| 缩放按钮 | `showZoomButtons` |

描述地图样式部分的 `mapStyles` 关键字使用了表 10-5 中所示的关键字。

**表 10-5** 地图样式部分关键字映射

| 元素 | 关键字 |
| --- | --- |
| 主题 | `mapTheme` |
| 显示标签 | `showLabels` |

描述热力图部分的 `heatMap` 关键字使用了表 10-6 中所示的关键字。

**表 10-6** 热力图部分关键字映射

| 元素 | 关键字 |
| --- | --- |
| 热力图 | `show` |
| 半径 | `filterRadius` |
| 透明度 | `transparency` |
| 单位 | `units` |
| 0% 渐变停止点 | `color` |
| 50% 渐变停止点 | `Color50` |
| 100% 渐变停止点 | `Color100` |

地图样式部分中的 `mapTheme` 关键字需要使用表 10-7 中所示的属性之一。

**表 10-7** 地图样式选项

| 值 | 格式设置弹出选项 |
| --- | --- |
| `aerial` | 航拍 |
| `dark` | 深色 |
| `light` | 浅色 |
| `grayscale` | 灰度 |
| `road` | 道路 |

热力图中的 `unit` 关键字（位于热力图部分）需要使用表 10-8 中所示的属性之一。

**表 10-8** 单位选项

| 值 | 格式设置弹出选项 |
| --- | --- |
| `pixels` | 像素 |
| `meters` | 米 |

图例位置选项见表 7-9。

## 着色地图

格式化着色地图所需的 JSON 代码与用于格式化地图的代码非常相似。

指定着色地图格式化属性的关键字是 `filledMap`。

表 10-9 概述了您可以在着色地图格式化中使用的 JSON 关键字及其在 Power BI Desktop 格式窗格中对应的部分。

**表 10-9** 着色地图图表关键字

| 关键字 | 格式设置部分 |
| --- | --- |
| `general` | 常规 |
| `dataPoint` | 数据颜色 |
| `mapControls` | 地图控件 |
| `mapStyles` | 地图样式 |
| `title` | 标题 |
| `background` | 背景 |
| `lockAspect` | 锁定纵横比 |
| `border` | 边框 |
| `dropShadow` | 阴影 |
| `visualHeader` | 视觉对象标头 |

您可以在示例文件 `Attributes_FilledMap.json` 中找到对应的 JSON 代码。

以下代码片段提供了用于预格式化着色地图的 JSON 代码：

```
"filledMap":
{"*":
{
"general": [{... 此处代码未显示 ...}]
"mapControls": [{
"autoZoom": true,
"showZoomButtons": true
}],
"mapStyles": [{
"mapTheme": "grayscale",
"showLabels": true
}],
"title": [{... 此处代码未显示 ...}],
"background": [{... 此处代码未显示 ...}],
"lockAspect": [{... 此处代码未显示 ...}],
"border": [{... 此处代码未显示 ...}],
"dropShadow": [{... 此处代码未显示 ...}],
"visualHeader": [{... 此处代码未显示 ...}]
}
}
```

地图控件元素见表 10-4。

地图样式元素见表 10-7。

图例位置选项见表 7-9。

# 形状地图

最后一种可以预格式化的地图类型是形状地图。此地图的样式属性与您刚才看到的两种地图有很大不同。它引入了两个新关键字：`shape` 和 `zoom`。

指定形状地图格式化属性的关键字是 `shapeMap`。

### 表 10-10 形状地图图表关键字

下表概述了可在形状地图格式化中使用的 JSON 关键字及其在 Power BI Desktop 格式窗格中的对应部分。

| 关键字 | 格式设置部分 |
| --- | --- |
| `general` | 常规 |
| `dataPoint` | 数据颜色 |
| `shape` | 形状 |
| `zoom` | 缩放 |
| `title` | 标题 |
| `background` | 背景 |
| `lockAspect` | 锁定纵横比 |
| `border` | 边框 |
| `dropShadow` | 阴影 |
| `visualHeader` | 视觉对象标题 |

您可以在示例文件 `Attributes_ShapeMap.json` 中找到其 JSON 代码。

以下代码片段提供了用于预格式化形状地图的 JSON 代码：

```
"shapeMap":
{"*":
{
"general": [{... code not shown ...}]
"dataPoint": [{
"defaultCategoryColor": { "solid": { "color": "#12239E"}},
"showAllDataPoints": true
}],
"shape": [{
"type": "uk.countries.topo",
"projectionEnum": "mercator"
}],
"zoom": [{
"autoZoom": true,
"selectionZoom": true,
"manualZoom": true
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

地图控件元素在表 10-4 中进行了说明。

在形状部分使用的 JSON 中的 `projection` 关键字需要表 10-11 中显示的属性之一。

### 表 10-11 投影选项

| 值 | 格式弹出选项 |
| --- | --- |
| `orthographic` | 正射投影 |
| `mercator` | 墨卡托投影 |
| `equirectangular` | 等距圆柱投影 |

在形状部分使用的 JSON 中的 `map` 关键字需要表 10-12 中显示的属性之一。

### 表 10-12 图例位置选项

| 值 | 格式弹出选项 |
| --- | --- |
| `australia.states.topo` | 澳大利亚：州 |
| `austria.states.topo` | 奥地利：州 |
| `brazil.states.topo` | 巴西：州 |
| `canada.provinces.topo` | 加拿大：省 |
| `france.regions.topo` | 法国：大区 |
| `germany.states.topo` | 德国：州 |
| `ireland.counties.topo` | 爱尔兰：郡 |
| `italy.regions.topo` | 意大利：大区 |
| `mexico.states.topo` | 墨西哥：州 |
| `netherlands.provinces.topo` | 荷兰：省 |
| `uk.countries.topo` | 英国：国家 |
| `usa.states.topo` | 美国：州 |

描述缩放部分的 `zoom` 关键字使用表 10-13 中所示的关键字。

### 表 10-13 缩放部分的关键字映射

| 元素 | 关键字 |
| --- | --- |
| 自动缩放 | `autoZoom` |
| 选择缩放 | `selectionZoom` |
| 手动缩放 | `manualZoom` |

## 结论

在这短短的一章中，您了解了如何为地图、着色地图和形状地图视觉对象创建 JSON。它们使用了相当简单的 JSON（假设您熟悉主题文件创建），但依然允许您定义地图视觉对象格式化的基本方面。

