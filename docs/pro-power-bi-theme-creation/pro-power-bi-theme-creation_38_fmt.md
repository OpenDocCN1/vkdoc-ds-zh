# 折线图和堆积柱形图

组合图表类型有两种，折线图和堆积柱形图是其中之一。就 JSON 主题代码而言，它是柱形图和折线图的混合体。

指定折线图和堆积柱形图格式属性的关键字是 `lineStackedColumnComboChart`。

就 JSON 而言，本质区别在于数值轴——多个序列在此区分开来。

表 8-4 概述了可在折线图和堆积柱形图格式中使用的 JSON 关键字，以及 Power BI Desktop 格式窗格中每个关键字对应的部分。

**表 8-4** 折线图和堆积柱形图关键字

| 关键字 | 格式部分 |
| --- | --- |
| `general` | 常规 |
| `legend` | 图例 |
| `categoryAxis` | X 轴 |
| `valueAxis` | Y 轴 |
| `zoom` | 缩放滑块 |
| `labels` | 数据标签 |
| `lineStyles` | 形状 |
| `plotArea` | 绘图区 |
| `title` | 标题 |
| `background` | 背景 |
| `lockAspect` | 锁定纵横比 |
| `border` | 边框 |
| `dropShadow` | 阴影 |
| `visualTooltip` | 工具提示 |
| `visualHeader` | 视觉对象标头 |

以下是描述折线图和堆积柱形图格式的 JSON。它是所有视觉对象中最长的 JSON 描述之一。

```
"lineStackedColumnComboChart":
{"*":
{
"general": [{... code not shown ...}]
"legend": [{
"show": true,
"position": "Top",
"showTitle": true,
"titleText": "Add a legend title",
"legendColor": {"solid": {"color": "#949494"}},
"fontFamily": "Arial",
"fontSize": 8,
"legendMarkerRendering":"markerOnly"
}],
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
"titleText": "Add an Axis Title",
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
"titleText": "Add an Axis Title",
"titleFontSize": 8,
"titleFontFamily": "Arial",
"gridLineShow": false,
"gridLineColor":  {"solid": {"color": "#949494"}},
"gridLineThickness": 1,
"gridlineStyle": "Solid",
"secShow": true,
"alignZeros": true,
"secPosition": "Right",
"secStart": null,
"secEnd": null,
"secAxisScale": "Linear",
"secLabelColor": {"solid": {"color": "#949494"}},
"secFontSize": 8,
"secFontFamily": "Arial",
"secLabelDisplayUnits": 0,
"secLabelPrecision": 0,
"secShowAxisTitle": true,
"secTitleText": "Add an Axis Title",
"secAxisStyle": "ShowTitleOnly",
"secTitleColor": {"solid": {"color": "#949494"}},
"secTitleText": "Add an Axis Title",
"secTitleFontSize": 8,
"secTitleFontFamily": "Arial"
}],
"zoom[{... code not shown ...}],
"labels": [{
"show": true,
"color": {"solid": {"color": "#ffffff"}},
"labelDisplayUnits": 1,
"labelPrecision": 2,
"labelOrientation": 1,
"labelPosition": "InsideEnd",
"labelOverflow": true,
"labelOrientation": "Vertical",
"fontFamily": "Arial",
"fontSize": 7,
"enableBackground": false,
"backgroundColor": {"solid": {"color": "#949494"}},
"backgroundTransparency": 0
}],
"lineStyles": [{
"strokeWidth": 1,
"strokeLineJoin": "Bevel",
"lineStyle": "Solid",
"showMarker": true,
"markerShape": "longDash",
"markerSize": 3,
"markerColor":  {"solid": {"color": "#949494"}},
"stepped": true,
"showSeries": false
}],
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

图例关键字如表 7-2 所示。它包含一个附加选项：`legendMarkerRendering`。

类别关键字如表 7-3 所示。它们还包含 `concatenateLabels` 和 `showAxisTitle` 元素。

描述 Y 轴部分的 `valueAxis` 关键字使用了表 8-5 中所示的关键字。

**表 8-5** Y 轴的关键字映射

| 元素 | 关键字 |
| --- | --- |
| Y 轴（Y 轴柱形） | `show` |
| （Y 轴柱形） | `position` |
| 刻度类型（Y 轴柱形） | `axisScale` |
| 起始值（Y 轴柱形） | `start` |
| 结束值（Y 轴柱形） | `end` |
| 颜色（Y 轴柱形） | `labelColor` |
| 文本大小（Y 轴柱形） | `fontSize` |
| 字体系列（Y 轴柱形） | `fontFamily` |
| 显示单位（Y 轴柱形） | `labelDisplayUnits` |
| 值小数位数（Y 轴柱形） | `labelPrecision` |
| 标题（Y 轴柱形） | `showAxisTitle` |
| 样式（Y 轴柱形） | `axisStyle` |
| 标题颜色（Y 轴柱形） | `color` |
| 轴标题（Y 轴柱形） | `titleText` |
| 标题文本大小（Y 轴柱形） | `titleFontSize` |

| （标题）字体系列（Y 轴柱形） | `titleFontFamily` |
| --- | --- |
| 网格线（Y 轴柱形） | `gridLineShow` |
| （网格线）颜色（Y 轴柱形） | `gridLineColor` |
| 描边宽度（Y 轴柱形） | `gridLineThickness` |
| 线条样式（Y 轴柱形） | `gridlineStyle` |
| 显示次坐标轴 | `secShow` |
| 对齐零值 | `alignZeros` |
| （Y 轴行） | `secPosition` |
| 刻度类型（Y 轴行） | `secAxisScale` |
| 起始值（Y 轴行） | `secStart` |
| 结束值（Y 轴行） | `secEnd` |
| 颜色（Y 轴行） | `secLabelColor` |
| 文本大小（Y 轴行） | `secFontSize` |
| 字体系列（Y 轴行） | `secFontFamily` |
| 显示单位（Y 轴行） | `secLabelDisplayUnits` |
| 值小数位数（Y 轴行） | `secLabelPrecision` |
| 标题（Y 轴行） | `secShowAxisTitle` |
| 样式（Y 轴行） | `secAxisStyle` |
| 标题颜色（Y 轴行） | `secTitleColor` |
| 轴标题（Y 轴行） | `secTitleText` |
| 标题文本大小（Y 轴行） | `secTitleFontSize` |
| （标题）字体系列（Y 轴行） | `secTitleFontFamily` |

缩放关键字如表 7-5 所示。

标签关键字如表 7-6 所示。其中还包含 `labelOrientation` 选项。

线条样式关键字如表 7-28 所示。

工具提示关键字如表 6-5 所示。

对于折线图和堆积柱形图，JSON 图例部分中的 `legendMarkerRendering` 关键字需要表 8-6 中所示的某个属性。

### 表 8-6 图例位置选项

| 值 | 格式弹出选项 |
| --- | --- |
| `markerOnly` | 仅标记 |
| `lineAndMarker` | 线条和标记 |
| `lineOnly` | 仅线条 |

类别关键字如表 7-3 所示。

值关键字如表 7-4 所示。

缩放关键字如表 7-5 所示。

绘图区关键字如表 7-8 所示。

图例位置选项如表 7-9 所述。

类别轴样式选项如表 7-16 所述。

值轴显示单位选项如表 7-11 所述。

值轴样式选项如表 7-16 所述。

值轴网格线样式选项如表 7-13 所述。

线条样式选项如表 7-28 所述。

标签位置选项如表 7-12 所述。

标签方向选项如表 7-19 所述。

类别轴样式选项如表 7-16 所述。

线条样式 `lineStyle` 的选项见表 7-28。

线条样式 `strokeLineJoin` 的选项见表 7-29。

线条样式 `markerShape` 的选项见表 7-30。

用于描述折线图和堆积柱形图格式化属性的完整 JSON 代码位于文件 `Attributes_LineAndStackedColumnChart.json` 中。

## 折线图和簇状柱形图

另一种可选的组合图类型是折线图和簇状柱形图。此视觉对象的 JSON 代码与折线图和堆积柱形图的代码非常相似。

指定折线图和簇状柱形图格式化属性的关键字是 `lineClusteredColumnComboChart`。

表 8-7 概述了可在折线图和簇状柱形图格式化中使用的 JSON 关键字，以及 Power BI Desktop 格式窗格中与每个关键字对应的部分。

### 表 8-7 折线图和簇状柱形图关键字

| 关键字 | 格式设置部分 |
| --- | --- |
| `general` | 常规 |
| `legend` | 图例 |
| `categoryAxis` | X 轴 |
| `valueAxis` | Y 轴 |
| `zoom` | 缩放滑块 |
| `labels` | 数据标签 |
| `lineStyles` | 形状 |
| `plotArea` | 绘图区 |
| `title` | 标题 |
| `background` | 背景 |
| `lockAspect` | 锁定纵横比 |
| `border` | 边框 |
| `dropShadow` | 阴影 |
| `visualTooltip` | 工具提示 |
| `visualHeader` | 视觉对象标头 |

以下是描述折线图和簇状柱形图格式设置的 JSON：
```json
// 此处为示例，表示JSON代码块的位置
{
  // 具体的JSON属性配置
}
```

# 配置示例

你可以在示例文件`Attributes_LineAndClusteredColumnChart.json`中找到此 JSON 代码。

## 配置关键字参考

类别关键字见表 7-3。值关键字见表 7-4。缩放关键字见表 7-5。绘图区关键字见表 7-8。图例位置选项见表 7-9。分类轴样式选项见表 7-16。值轴显示单位选项见表 7-11。值轴样式选项见表 7-16。值轴网格线样式选项见表 7-13。线条样式选项见表 7-28。标签位置选项见表 7-12。标签方向选项见表 7-19。分类轴样式选项见表 7-16。线条样式部分`lineStyle`的选项见表 7-28。线条样式部分`strokeLineJoin`的选项见表 7-29。线条样式部分`markerShape`的选项见表 7-30。

```
"lineClusteredColumnComboChart":
{"*":
{
"general": [{... 代码未显示 ...}]
"legend": [{
"show": true,
"position": "Top",
"showTitle": true,
"titleText": "添加图例标题",
"legendColor": {"solid": {"color": "#949494"}},
"fontFamily": "Arial",
"fontSize": 8,
"legendMarkerRendering":"markerOnly"
}],
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
"gridlineStyle": "Solid",
"secShow": true,
"alignZeros": true,
"secPosition": "Right",
"secStart": null,
"secEnd": null,
"secAxisScale": "Linear",
"secLabelColor": {"solid": {"color": "#949494"}},
"secFontSize": 8,
"secFontFamily": "Arial",
"secLabelDisplayUnits": 0,
"secLabelPrecision": 0,
"secShowAxisTitle": true,
"secTitleText": "添加轴标题",
"secAxisStyle": "ShowTitleOnly",
"secTitleColor": {"solid": {"color": "#949494"}},
"secTitleText": "添加轴标题",
"secTitleFontSize": 8,
"secTitleFontFamily": "Arial"
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
"labelOrientation": "Vertical",
"fontFamily": "Arial",
"fontSize": 7,
"enableBackground": false,
"backgroundColor": {"solid": {"color": "#949494"}},
"backgroundTransparency": 0
}],
"lineStyles": [{
"shadeArea": true,
"strokeWidth": 1,
"strokeLineJoin": "Bevel",
"lineStyle": "Solid",
"showMarker": true,
"stepped": true
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