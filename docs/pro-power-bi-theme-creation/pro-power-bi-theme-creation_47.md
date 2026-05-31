# 形状地图

最后一种可以预格式化的地图类型是形状地图。此地图的样式属性与您刚才看到的两种地图有很大不同。它引入了两个新关键字：`shape` 和 `zoom`。

指定形状地图格式化属性的关键字是 `shapeMap`。

表 10-10 概述了可在形状地图格式化中使用的 JSON 关键字及其在 Power BI Desktop 格式窗格中的对应部分。

**表 10-10** 形状地图图表关键字

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

**表 10-11** 投影选项

| 值 | 格式弹出选项 |
| --- | --- |
| `orthographic` | 正射投影 |
| `mercator` | 墨卡托投影 |
| `equirectangular` | 等距圆柱投影 |

在形状部分使用的 JSON 中的 `map` 关键字需要表 10-12 中显示的属性之一。

**表 10-12** 图例位置选项

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

**表 10-13** 缩放部分的关键字映射

| 元素 | 关键字 |
| --- | --- |
| 自动缩放 | `autoZoom` |
| 选择缩放 | `selectionZoom` |
| 手动缩放 | `manualZoom` |

## 结论

在这短短的一章中，您了解了如何为地图、着色地图和形状地图视觉对象创建 JSON。它们使用了相当简单的 JSON（假设您熟悉主题文件创建），但依然允许您定义地图视觉对象格式化的基本方面。

