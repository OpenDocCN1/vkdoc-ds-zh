# 7. 经典图表可视化样式

图表是一组共享许多通用格式元素的视觉对象。因此，我认为将它们放在一起讲解是合理的。这有助于你理解这些图表的共通之处，也能让你更容易理解某些高级图表中使用的更复杂的 JSON 结构。

本章将讲解的图表包括：

- 堆积条形图
- 堆积柱形图
- 簇状条形图
- 簇状柱形图
- 折线图
- 面积图

需要学习的视觉对象着实不少。幸运的是，这些图表类型的大部分属性（若非全部）都是通用的。这意味着，一旦你掌握了格式属性应用于基础图表的基本方法，你就可以将相同的代码和知识应用于大部分图表中。这种核心相似性的另一个好处是，它使我们能够在本章后面要查看的 JSON 代码片段中，只关注不同图表类型之间的差异。

本章讨论的每种图表类型都在 `samples` 文件夹中有一个单独的 JSON 文件，其中包含可用于自定义主题文件的代码。

## 堆积条形图

首先需要注意的是，尽管 Power BI Desktop 界面称之为堆积条形图（如果鼠标悬停在“可视化”窗格中的第一个图标上，弹出窗口会显示此名称），但在主题文件中，它被称为 `barChart`。这意味着你需要记住的关键字是 `barChart`，因为这是引入用于指定堆积条形图格式的 JSON 对象的关键字。

表 7-1 概述了堆积条形图格式中可用的 JSON 关键字，以及 Power BI Desktop 格式窗格中与每个关键字对应的部分。

### 表 7-1 堆积条形图关键字

| 关键字 | 格式部分 |
| --- | --- |
| `general` | 常规 |
| `legend` | 图例 |
| `categoryAxis` | Y 轴 |
| `valueAxis` | X 轴 |
| `zoom` | 缩放滑块 |
| `labels` | 数据标签 |
| `totals` | 总计标签 |
| `plotArea` | 绘图区 |
| `title` | 标题 |
| `background` | 背景 |
| `lockAspect` | 锁定纵横比 |
| `border` | 边框 |
| `dropShadow` | 阴影 |
| `visualHeader` | 视觉对象标头 |

示例文件 `Attributes_StackedBarChart.json` 包含了堆积条形图 JSON 格式的完整规范。你可以在以下代码片段中看到此图表类型特有的格式属性：

```json
"barChart":
{"*":
{
"general": [{... 代码未显示 ...}],
"legend": [{
"show": true,
"position": "Top",
"showTitle": true,
"titleText": "添加图例标题",
"legendColor": {"solid": {"color": "#949494"}},
"fontFamily": "Arial",
"fontSize": 8
}],
"categoryAxis": [{
"show": true,
"position": "Left",
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
"axisScale": "Linear",
"start": null,
"end": null,
"labelColor": {"solid": {"color": "#949494"}},
"fontSize": 8,
"fontFamily": "Arial",
"labelDisplayUnits": 0,
"labelPrecision": 0,
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
"zoom":[{
"show":true,
"showOnValueAxis" : false,
"showLabels":true,
"showTooltip":true
}],
"labels": [{
"show": true,
"color": {"solid": {"color": "#ffffff"}},
"labelDisplayUnits": 1,
"labelPrecision": 2,
"labelPosition": "InsideEnd",
"labelOverflow": true,
"fontSize": 7,
"fontFamily": "Arial",
"enableBackground": false,
"backgroundColor": {"solid": {"color": "#949494"}},
"backgroundTransparency": 0
}],
"totals": [{
"show": true,
"color": {"solid": {"color": "#FFFFFF"}},
"labelDisplayUnits": 1000,
"labelPrecision": 2,
"fontSize": 8,
"fontFamily": "Arial",
"enableBackground": true,
"backgroundColor": {"solid": {"color": "#949494"}},
"backgroundTransparency": 10,
"showPositiveAndNegative": true
}],
"plotArea": [{
"transparency": 0,
"scaling": "Fill"
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

这里有很多 JSON 代码，但它确实符合你在前两章中看到的结构。无论如何，我已经让 JSON 遵循了 Power BI Desktop 格式窗格中的元素顺序，以便于在 JSON 规范与你 Power BI Desktop 中看到的内容之间建立关联。

这里有几个要点需要说明：

- 在此图表中，垂直（Y）轴是分类轴（因此水平（X）轴是数值轴），这在 JSON 中有体现。
- 使用 JSON 定义中通过 `start` 和 `end` 关键字设置的值轴起始值和结束值，可以设置为特定数值，而非 `null` 值。只需输入所需值（作为数字，不包含逗号分隔符等任何格式）。如果你希望将该值留空，则必须使用 `null` 关键字，否则 JSON 主题文件将无法加载。
- 你不能指定哪种颜色应用于哪个数据点，因为这特定于你创建的每个不同可视化中的数据。
- 仅当数据已添加到 Power BI Desktop 的“图例”字段中时，你才能在 Power BI Desktop 的格式窗格中看到“图例”卡片。不过，无论图例是否存在，你都可以添加用于格式化图例的 JSON，因为只有当图例在可视化中使用时，格式化才会被应用。
- 使用 `axisStyle` 关键字设置的轴样式仅限于在堆积条形图（和柱状图）中显示标题。

描述图例部分的 `legend` 关键字使用了表 7-2 中所示的关键字。

### 表 7-2 图例的关键字映射

| 元素 | 关键字 |
| --- | --- |
| 图例 | `show` |
| 位置 | `position` |
| 标题 | `showTitle` |
| 图例名称 | `titleText` |
| 颜色 | `legendColor` |
| 字体 | `fontFamily` |
| 文本大小 | `fontSize` |

描述 Y 轴部分的 `categoryAxis` 关键字使用了表 7-3 中所示的关键字。

`表 7-3` Y 轴的关键字映射

| 元素 | 关键字 |
| --- | --- |
| X 轴 | `show` |
| 位置 | `position` |
| 颜色 | `color` |
| 文本大小 | `fontSize` |
| 字体 | `fontFamily` |
| 最小类别宽度 | `preferredCategoryWidth` |
| 最大尺寸 | `maxMarginFactor` |
| 内边距 | `innerPadding` |
| 标题 | `showAxisTitle` |
| 样式 | `axisStyle` |
| 标题颜色 | `titleColor` |
| 轴标题 | `titleText` |
| 标题文本大小 | `titleFontSize` |
| （标题）字体 | `titleFontFamily` |

描述 X 轴部分的 `valueAxis` 关键字使用了表 7-4 中所示的关键字。

`表 7-4` X 轴的关键字映射

| 元素 | 关键字 |
| --- | --- |
| Y 轴 | `show` |
| 缩放类型 | `axisScale` |
| 开始 | `start` |
| 结束 | `end` |
| 颜色 | `labelColor` |
| 文本大小 | `fontSize` |
| 字体 | `fontFamily` |
| 显示单位 | `labelDisplayUnits` |
| 值小数位数 | `labelPrecision` |
| 标题 | `showAxisTitle` |
| 样式 | `axisStyle` |
| 标题颜色 | `color` |
| 轴标题 | `titleText` |
| 标题文本大小 | `titleFontSize` |
| （标题）字体 | `titleFontFamily` |
| 网格线 | `gridLineShow` |
| （网格线）颜色 | `gridLineColor` |
| 描边宽度 | `gridLineThickness` |
| 线条样式 | `gridlineStyle` |

描述缩放滑块部分的 `zoom` 关键字使用了表 7-5 中所示的关键字。

`表 7-5` 缩放滑块的关键字映射

| 元素 | 关键字 |
| --- | --- |
| 缩放滑块 | `show` |
| X 轴 | `showOnValueAxis` |
| 滑块标签 | `showLabels` |
| 滑块工具提示 | `showTooltip` |

描述数据标签部分的 `labels` 关键字使用了表 7-6 中所示的关键字。

`表 7-6` 数据标签的关键字映射

| 元素 | 关键字 |
| --- | --- |
| 数据标签 | `show` |
| 颜色 | `color` |
| 显示单位 | `labelDisplayUnits` |
| 值小数位数 | `labelPrecision` |
| 位置 | `labelPosition` |
| 溢出文本 | `labelOverflow` |
| 文本大小 | `fontSize` |
| 字体 | `fontFamily` |
| 显示背景 | `enableBackground` |
| 背景颜色 | `backgroundColor` |
| 透明度 | `backgroundTransparency` |

描述总计标签部分的 `totals` 关键字使用了表 7-7 中所示的关键字。

`表 7-7` 总计标签的关键字映射

| 元素 | 关键字 |
| --- | --- |
| 总计标签 | `show` |
| 颜色 | `color` |
| 显示单位 | `labelDisplayUnits` |
| 值小数位数 | `labelPrecision` |
| 文本大小 | `fontSize` |
| 字体 | `fontFamily` |
| 显示背景 | `enableBackground` |
| 背景颜色 | `backgroundColor` |
| 透明度 | `backgroundTransparency` |
| 拆分正负值 | `showPositiveAndNegative` |

描述绘图区部分的 `plotArea` 关键字使用了表 7-8 中所示的关键字。

`表 7-8` 绘图区的关键字映射

| 元素 | 关键字 |
| --- | --- |
| 绘图区 | `show` |
| 透明度 | `transparency` |
| 缩放 | `scaling` |

工具提示选项在表 6-5 中进行描述。

JSON 中用于图表内图例位置的 `position` 关键字，需要表 7-9 中所示的属性之一。

`表 7-9` 图例位置选项

| 值 | 格式化弹出选项 |
| --- | --- |
| `Top` | 顶部 |
| `Bottom` | 底部 |
| `Left` | 左侧 |
| `Right` | 右侧 |
| `TopCenter` | 顶部居中 |
| `BottomCenter` | 底部居中 |
| `LeftCenter` | 左侧居中 |
| `RightCenter` | 右侧居中 |

> **注意：** 由于这些是 JSON 中的*属性*（值）而非*关键字*，它们不必遵守适用于“纯”关键字的驼峰命名要求。此外，任何文本值都*必须*用双引号括起来。

JSON 中在类别轴部分使用的 `position` 关键字，需要表 7-10 中所示的属性之一。

`表 7-10` 类别轴位置选项

| 值 | 格式化弹出选项 |
| --- | --- |
| `Left` | 左侧 |
| `Right` | 右侧 |

JSON 中在值轴的标签部分使用的 `labelPrecision` 关键字，需要表 7-11 中所示的属性之一。

`表 7-11` 标签显示单位选项

| 值 | 格式化弹出选项 |
| --- | --- |
| `0` | 自动 |
| `1` | 无 |
| `1000` | 千 |
| `1000000` | 百万 |
| `1000000000` | 十亿 |
| `1000000000000` | 万亿 |

> **注意：** 由于这些是 JSON 中的*数值*，它们*不得*用双引号括起来。

JSON 中在标签部分使用的 `labelPosition` 关键字，需要表 7-12 中所示的属性之一。

`表 7-12` 标签位置选项

| 值 | 格式化弹出选项 |
| --- | --- |
| `Auto` | 自动 |
| `InsideEnd` | 内部末端 |
| `InsideCenter` | 内部居中 |
| `InsideBase` | 内部底部 |

JSON 中在 X 轴部分使用的 `gridLineStyle` 关键字，需要表 7-13 中所示的属性之一。

`表 7-13` 网格线选项

| 值 | 格式化弹出选项 |
| --- | --- |
| `Dotted` | 点线 |
| `Dashed` | 虚线 |
| `Solid` | 实线 |

> **注意：** 自定义系列选项（用于数据标签）似乎与所有需要选择源字段的格式化一样，无法通过主题文件进行配置。

# 堆积柱形图

接下来要查看的图表是堆积柱形图。其 JSON 结构与你之前看到的代码基本一致，这说明了 Power BI 图表在结构方法上的共通性。

用于指定堆积柱形图格式属性的关键字是 `columnChart`。

表 7-14 概述了在堆积条形图格式设置中可用的 JSON 关键字，以及这些关键字在 Power BI Desktop 格式窗格中对应的部分。

**表 7-14** 堆积条形图关键字

| 关键字 | 格式区域 |
| --- | --- |
| `General` | 常规 |
| `legend` | 图例 |
| `categoryAxis` | X 轴 |
| `valueAxis` | Y 轴 |
| `zoom` | 缩放滑块 |
| `labels` | 数据标签 |
| `totals` | 总计标签 |
| `plotArea` | 绘图区 |
| `title` | 标题 |
| `lockAspect` | 锁定纵横比 |
| `border` | 边框 |
| `dropShadow` | 阴影 |
| `visualHeader` | 视觉对象标头 |

与你之前看到的堆积条形图 JSON 相比，主要区别在于：
-   `categoryAxis` 新增了 `concatenateLabels` 关键字，并移除了 `position` 关键字。
-   `valueAxis` 新增了 `position` 关键字，并移除了 `axisScale` 关键字。
-   `labels` 新增了 `labelOrientation`。
-   现在还有选项可以为该图表类型选择分类轴和值轴的样式。
-   在此 JSON 中，图表中的垂直 (Y) 轴是值轴（因此水平 (X) 轴是分类轴）。

完整定义可在示例文件中的 `Attributes_StackedColumnChart.json` 文件中找到。JSON 代码如下：

```
"columnChart":
{"*":
{
"general": [{... 代码未显示 ...}]
"legend": [{... 代码未显示 ...}],
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
"gridlineStyle": "Solid"
}],
"zoom: [{... 代码未显示 ...}],
"labels": [{
"show": true,
"color": {"solid": {"color": "#FFFFFF"}},
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
}] ,
"totals": [{... 代码未显示 ...}],
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

幸运的是，用于 X 轴、Y 轴和标签部分的堆积柱形图关键字，与用于堆积条形图的关键字基本一致——因此此处不再赘述，而是建议你参考上一节。

JSON 中用于设置标签方向的 `labelOrientation` 关键字（在 `labels` 部分）需要提供表 7-15 中所示的属性之一。

**表 7-15** 标签显示单位选项

| 值 | 格式弹出选项 |
| --- | --- |
| `Vertical` | 垂直 |
| `Horizontal` | 水平 |

JSON 中用于值轴的 `axisStyle` 关键字需要提供表 7-16 中所示的属性之一。

**表 7-16** 分类轴和值轴样式选项

| 值 | 格式弹出选项 |
| --- | --- |
| `ShowTitleOnly` | 仅显示标题 |
| `ShowUnitOnly` | 仅显示单位 |
| `ShowBoth` | 同时显示 |

图例关键字见表 7-2。
分类轴关键字见表 7-3。
值轴关键字见表 7-4。
缩放关键字见表 7-5。对于柱形图，`showOnValueAxis` 指的是 Y 轴。
标签关键字见表 7-6。
总计关键字见表 7-7。
绘图区关键字见表 7-8。
工具提示关键字见表 6-5。
图例位置选项见 7-9 中的描述。
分类轴位置选项见 7-10 中的描述。
值轴显示单位选项见 7-11 中的描述。
值轴样式选项见 7-16 中的描述。
值轴网格线样式选项见 7-13 中的描述。
标签位置选项见 7-12 中的描述。

# 簇状条形图

指定簇状条形图格式的 JSON 与您之前看到的堆积条形图代码几乎完全相同——当然，除了 JSON 对象的初始关键字。唯一的主要区别在于它不包含 `totals` 部分。

指定簇状条形图格式属性的关键字是 `clusteredBarChart`。

表 7-17 概述了可在簇状条形图格式中使用的 JSON 关键字，以及 Power BI Desktop“格式”窗格中每个关键字对应的部分。

`表 7-17` 簇状条形图关键字

| 关键字 | 格式部分 |

| --- | --- |

| `general` | 常规 |

| `legend` | 图例 |

| `categoryAxis` | Y 轴 |

| `valueAxis` | X 轴 |

| `zoom` | 缩放滑块 |

| `labels` | 数据标签 |

| `plotArea` | 绘图区域 |

| `title` | 标题 |

| `lockAspect` | 锁定纵横比 |

| `border` | 边框 |

| `dropShadow` | 阴影 |

| `visualHeader` | 视觉对象标头 |

指定簇状条形图格式的 JSON 代码可在示例文件 `Attributes_ClusteredBarChart.json` 中找到。

为求完整，以下是定义簇状条形图格式的 JSON 代码：

```
"clusteredBarChart":
{"*" :
{
"general": [{... code not shown ...}]
"legend": [{... code not shown ...}],
"categoryAxis": [{
"show": true,
"position": "Left",
"color": {"solid": {"color": "#949494"}},
"fontSize": 8,
"fontFamily": "Arial",
"preferredCategoryWidth": 20,
"maxMarginFactor": 25,
"innerPadding": 15,
"showAxisTitle": true,
"axisStyle": "showTitleOnly",
"titleColor": {"solid": {"color": "#949494"}},
"titleText": "Add an Axis Title",
"titleFontSize": 8,
"titleFontFamily": "Arial"
}],
"valueAxis": [{
"show": true,
"axisScale": "Linear",
"start": null,
"end": null,
"labelColor": {"solid": {"color": "#949494"}},
"fontSize": 8,
"fontFamily": "Arial",
"labelPrecision": 0,
"labelDisplayUnits": 2,
"showAxisTitle": true,
"axisStyle": "ShowTitleOnly",
"color": {"solid": {"color": "#949494"}},
"titleText": "Add an Axis Title",
"titleFontSize": 8,
"titleFontFamily": "Arial",
"gridLineShow": false,
"gridLineColor":  {"solid": {"color": "#949494"}},
"gridLineThickness": 1,
"gridlineStyle": "Solid"
}],
"zoom: [{... code not shown ...}]
"labels": [{
"show": true,
"color": {"solid": {"color": "#FFFFFF"}},
"labelDisplayUnits": 1,
"labelPrecision": 2,
"labelPosition": "InsideEnd",
"labelOverflow": true,
"fontFamily": "Arial",
"fontSize": 7,
"enableBackground": false,
"backgroundColor": {"solid": {"color": "#949494"}},
"backgroundTransparency": 0
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

类别关键字见表 7-3。

值关键字见表 7-4。

缩放关键字见表 7-5。对于条形图，`showOnValueAxis` 指的是 X 轴。

标签关键字见表 7-6。

总计关键字见表 7-7。

绘图区域关键字见表 7-8。

工具提示关键字见表 6-5。

图例位置选项见表 7-9。

分类轴位置选项见表 7-10。

值轴显示单位选项见表 7-11。

值轴样式选项见表 7-16。

值轴网格线样式选项见表 7-13。

标签位置选项见表 7-12。

# 簇状柱形图

用于指定簇状柱形图格式的 JSON 与之前看到的堆叠柱形图代码非常相似——当然，除了 JSON 对象的初始关键字之外。这个关键字是 `clusteredColumnChart`。

主要区别在于：

- 它*不*包含 `totals` 部分。
- 在 `labels` 部分中新增了 `labelOrientation` 关键字。

表 7-18 概述了可在堆叠条形图格式中使用的 JSON 关键字，以及每个关键字在 Power BI Desktop 格式窗格中对应的部分。

**表 7-18** 簇状柱形图关键字

| 关键字 | 格式部分 |
| --- | --- |
| `general` | 常规 |
| `legend` | 图例 |
| `categoryAxis` | X 轴 |
| `valueAxis` | Y 轴 |
| `zoom` | 缩放滑块 |
| `labels` | 数据标签 |
| `plotArea` | 绘图区 |
| `title` | 标题 |
| `lockAspect` | 锁定纵横比 |
| `border` | 边框 |
| `dropShadow` | 阴影 |
| `visualHeader` | 视觉对象标头 |

以下代码片段提供了用于预先格式化簇状柱形图的 JSON 代码：

```
"clusteredColumnChart":
{"*":
{
"general": [{... 代码未显示 ...}]
"legend": [{... 代码未显示 ...}],
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
"axisStyle": "ShowUnitOnly",
"color": {"solid": {"color": "#949494"}},
"titleText": "添加轴标题",
"titleFontSize": 8,
"titleFontFamily": "Arial",
"gridLineShow": false,
"gridLineColor":  {"solid": {"color": "#949494"}},
"gridLineThickness": 1,
"gridlineStyle": "Solid"
}],
"zoom: [{... 代码未显示 ...}],
"labels": [{
"show": true,
"color": {"solid": {"color": "#ffffff"}},
"labelDisplayUnits": 1,
"labelPrecision": 2,
"labelOrientation": 1,
"labelPosition": "InsideEnd",
"labelOverflow": true,
"fontFamily": "Arial",
"fontSize": 7,
"enableBackground": false,
"backgroundColor": {"solid": {"color": "#949494"}},
"backgroundTransparency": 0
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

JSON 中用于设置标签方向的 `labelOrientation` 关键字（位于 `labels` 部分内）需要提供表 7-19 中所示的其中一个属性。

**表 7-19** 标签显示单位选项

| 值 | 格式弹出选项 |
| --- | --- |
| `vertical` | 0 |
| `horizontal` | 1 |

**注意：** 与堆叠柱形图不同，簇状柱形图使用 `0` 和 `1` 作为关键字，而不是 `horizontal` 和 `vertical`。此外，这些是数值，因此不得用双引号括起来。

值得注意的是，簇状柱形图的 JSON 也提供了选项（类似于簇状条形图），用于选择类别轴和值轴的样式。

簇状柱形图的 JSON 代码可在示例文件 `Attributes_ClusteredColumnChart.json` 中找到。

图例关键字见表 7-2。

类别关键字见表 7-3。

值关键字见表 7-4。

缩放关键字见表 7-5。对于柱形图，`showOnValueAxis` 指的是 Y 轴。

标签关键字见表 7-6。

总计关键字见表 7-7。

绘图区关键字见表 7-8。

工具提示关键字见表 6-5。

图例位置选项见表 7-9。

类别轴位置选项见表 7-10。

值轴显示单位选项见表 7-11。

值轴样式选项见表 7-16。

值轴网格线样式选项见表 7-13。

标签位置选项见表 7-12。

# 折线图

尽管折线图的外观与条形图和柱形图截然不同，但其格式设置方面与本章中已介绍的所有图表类型实际上非常相似。

与条形图和柱形图相比，折线图的 JSON 中存在的主要差异如下：

- 折线图包含用于描述次坐标轴的 JSON。
- 图例包含一些额外的元素。
- X（类别）轴略有不同。
- 最小类别宽度仍使用关键字 `preferredCategoryWidth`。
- 最大尺寸仍使用关键字 `maxMarginFactor`。
- 有一个额外的元素用于描述线条样式——由 `lineStyles` 关键字引入。
- `labels` 部分包含一个 `labelPosition` 元素——但其值与您在其他图表中看到的不同。

用于指定折线图格式属性的关键字是 `lineChart`。

### 表 7-20 折线图关键字

表 7-20 概述了可在折线图格式中使用的 JSON 关键字，以及每个关键字在 Power BI Desktop 格式窗格中对应的部分。

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
| `lockAspect` | 锁定纵横比 |
| `border` | 边框 |
| `dropShadow` | 阴影 |
| `visualHeader` | 视觉对象标头 |

描述折线图格式属性的 JSON 代码位于示例文件 `Attributes_LineChart.json` 中。代码片段如下所示。需要提醒的是，这是一段相当长的 JSON。

```json
"lineChart":
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
"legendMarkerRendering":"lineOnly",
"matchLineColor":true,
"defaultToCircle":true
}],
"categoryAxis": [{
"show": true,
"axisType":"Categorical",
"color": {"solid": {"color": "#949494"}},
"fontSize": 8,
"fontFamily": "Arial",
"preferredCategoryWidth": 20,
"maxMarginFactor": 30,
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
"position": "Right",
"axisScale": "Linear",
"start": null,
"end": null,
"labelColor": {"solid": {"color": "#949494"}},
"fontSize": 8,
"fontFamily": "Arial",
"labelPrecision": 0,
"labelDisplayUnits": 0,
"showAxisTitle": true,
"axisStyle": "ShowUnitOnly",
"color": {"solid": {"color": "#949494"}},
"titleText": "Add an Axis Title",
"titleFontSize": 8,
"titleFontFamily": "Arial",
"gridLineShow": false,
"gridLineColor":  {"solid": {"color": "#949494"}},
"gridLineThickness": 2,
"gridlineStyle": "dotted"
}],
"y2Axis": [{
"show": true,
"secStart": null,
"secEnd": null,
"secAxisScale": "Linear",
"secLabelColor": {"solid": {"color": "#949494"}},
"secFontSize": 8,
"secSontFamily": "Arial",
"secLabelPrecision": 0,
"secLabelDisplayUnits": 0,
"secShowAxisTitle": true,
"secAxisStyle": "ShowUnitOnly",
"secColor": {"solid": {"color": "#949494"}},
"secTitleText": "Add an Axis Title",
"secTitleFontSize": 8,
"secTitleFontFamily": "Arial"
}],
"zoom: [{... code not shown ...}],
"labels": [{
"show": true,
"color": {"solid": {"color": "#ffffff"}},
"labelDisplayUnits": 1,
"labelPrecision": 2,
"labelOrientation": 1,
"labelPosition": "Above",
"labelOverflow": true,
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

与新的线条样式和次 Y 轴部分中的元素一样，折线图还提供了一个迄今为止在其他图表视觉对象中未曾见过的选项，即轴类型（位于分类轴中）。实际上，图例和分类轴部分差异显著，值得专门审查。

图例部分对应的关键词 `legend` 使用了表 7-21 中所示的关键词。

### 表 7-21 图例关键词映射

| 元素 | 关键词 |
| --- | --- |
| 图例 | `show` |
| 位置 | `position` |
| 标题 | `showTitle` |
| 图例名称 | `titleText` |
| 颜色 | `legendColor` |
| 字体 | `fontFamily` |
| 字号 | `fontSize` |
| 样式 | `legendMarkerRendering` |
| 匹配线条颜色 | `matchLineColor` |
| 圆形默认图标 | `defaultToCircle` |

X 轴部分对应的关键词 `categoryAxis` 使用了表 7-22 中所示的关键词。

### 表 7-22 X 轴关键词映射

| 元素 | 关键词 |
| --- | --- |
| X 轴 | `show` |
| 轴类型 | `axisType` |
| 颜色 | `color` |
| 字号 | `fontSize` |
| 字体 | `fontFamily` |
| 最小分类宽度 | `preferredCategoryWidth` |
| 最大尺寸 | `maxMarginFactor` |
| 标题 | `showAxisTitle` |
| 样式 | `axisStyle` |
| 标题颜色 | `titleColor` |
| 轴标题 | `titleText` |
| 标题字号 | `titleFontSize` |
| （标题）字体 | `titleFontFamily` |

一如既往，您在 JSON 主题中使用的值必须与预期值**完全**匹配，否则将使用默认值。同样，请记住这些值区分大小写。

`valueAxis` 选项与表 7-3 中所示的选项相同，只是增加了 `position` 元素。

次 Y 轴部分对应的关键词 `y2Axis` 使用了表 7-23 中所示的关键词。

### 表 7-23 次 Y 轴关键词映射

| 元素 | 关键词 |
| --- | --- |
| 次 Y 轴 | `secShow` |
| 起始值 | `secStart` |
| 终止值 | `secEnd` |
| 刻度类型 | `secAxisScale` |
| 颜色 | `secLabelColor` |
| 字号 | `secFontSize` |
| 字体 | `secFontFamily` |
| 显示单位 | `secLabelDisplayUnits` |
| 值小数位数 | `secLabelPrecision` |
| 标题 | `secShowAxisTitle` |
| 样式 | `secAxisStyle` |
| 标题颜色 | `secColor` |
| 轴标题 | `secTitleText` |
| 标题字号 | `secTitleFontSize` |
| （标题）字体 | `secTitleFontFamily` |
| 网格线 | `secGridLineShow` |

缩放选项如表 7-5 所示。
标签选项如表 7-6 所示。

`labels` 部分中使用的 `labelPosition` 关键词需要表 7-24 中所示的某一属性值。

### 表 7-24 标签位置选项

| 元素 | 关键词 |
| --- | --- |
| 无 | `None` |
| 上方 | `Above` |
| 下方 | `Under` |

形状部分对应的关键词 `lineStyle` 使用了表 7-25 中所示的关键词。

### 表 7-25 形状关键词映射

| 元素 | 关键词 |
| --- | --- |
| 描边宽度 | `strokeWidth` |
| 连接类型 | `strokeLineJoin` |
| 线条样式 | `lineStyle` |
| 显示标记 | `showMarker` |
| 标记形状 | `markerShape` |
| 标记大小 | `markerSize` |
| 标记颜色 | `markerColor` |
| 阶梯线 | `Stepped` |
| 自定义序列 | `showSeries` |

JSON 中用于定义折线图（或面积图、堆叠面积图）分类轴的 `axisType` 关键词需要表 7-26 中所示的某一属性值。

### 表 7-26 X 轴类型选项

| 值 | 格式化弹出选项 |
| --- | --- |
| `Categorical` | 分类的 |
| `Continuous` | 连续的 |

JSON 中用于指定值轴刻度的 `axisScale` 关键词需要表 7-27 中所示的某一属性值。

### 表 7-27 值轴刻度选项

| 值 | 格式化弹出选项 |
| --- | --- |
| `Linear` | 线性 |
| `Log` | 对数 |

JSON 中用于线条样式部分的 `lineStyle` 关键词需要表 7-28 中所示的某一属性值。

### 表 7-28 线条样式选项

| 值 | 格式化弹出选项 |
| --- | --- |
| `markerOnly` | 仅标记 |
| `lineAndMarker` | 线条和标记 |
| `lineOnly` | 仅线条 |

JSON 中用于定义线条样式部分连接形状的 `strokeLineJoin` 关键词需要表 7-29 中所示的某一属性值。

### 表 7-29 连接类型选项

| 值 | 格式化弹出选项 |
| --- | --- |
| `Miter` | 斜接 |
| `Round` | 圆角 |
| `Bevel` | 斜角 |

JSON 中用于定义线条样式部分标记形状的 `markerShape` 关键词需要表 7-30 中所示的某一属性值。

### 表 7-30 标记形状选项

| 值 | 格式弹出选项 |
| --- | --- |
| `Round` | 圆角 |
| `Square` | 方形 |
| `Diamond` | 菱形 |
| `Triangle` | 三角形 |
| `x` | x |
| `shortDash` | - |
| `longDash` | — |
| `+` | + |

标签位置选项见表 7-19。
图例位置选项见表 7-9。
类别轴位置选项见表 7-10。
值轴显示单位选项见表 7-11。
值轴样式选项见表 7-16。
值轴网格线样式选项见表 7-13。
标签位置选项见表 7-12。

据我目前的理解，目前还无法在 Power BI Desktop 格式窗格中定义自定义序列（即形状/自定义序列）的格式。我可以理解为什么会出现这种情况，因为此选项需要指定一个字段，因此无法通用定义。

## 面积图

用于指定面积图格式的 JSON 与折线图的代码几乎相同，只是使用了不同的初始关键字（即必须使用 `areaChart`）。

表 7-31 概述了可在面积图格式中使用的 JSON 关键字，以及这些关键字在 Power BI Desktop 格式窗格中对应的部分。

### 表 7-31 面积图关键字

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
| `lockAspect` | 锁定纵横比 |
| `border` | 边框 |
| `dropShadow` | 阴影 |
| `visualHeader` | 视觉对象标头 |

用于描述面积图格式属性的完整 JSON 代码可在示例文件的 `Attributes_AreaChart.json` 中找到。

以下是描述面积图格式的 JSON：

### 示例代码：AreaChart JSON 配置

```json
"areaChart":
{"*" :
{
"general": [{... 代码省略 ...}]
"legend": [{
"show": true,
"position": "Top",
"showTitle": true,
"titleText": "添加图例标题",
"legendColor": {"solid": {"color": "#949494"}},
"fontFamily": "Arial",
"fontSize": 8,
"legendMarkerRendering":"lineOnly",
"matchLineColor":true,
"defaultToCircle":true
}],
"categoryAxis": [{
"show": true,
"axisType":"Categorical",
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
"valueAxis": [{
"show": true,
"position": "Left",
"axisScale": "Linear",
"start": null,
"end": null,
"labelColor": {"solid": {"color": "#949494"}},
"fontSize": 8,
"fontFamily": "Arial",
"labelPrecision": 0,
"labelDisplayUnits": 0,
"showAxisTitle": true,
"axisStyle": "ShowUnitOnly",
"color": {"solid": {"color": "#949494"}},
"titleText": "添加轴标题",
"titleFontSize": 8,
"titleFontFamily": "Arial",
"gridLineShow": true,
"gridLineColor":  {"solid": {"color": "#949494"}},
"gridLineThickness": 2,
"gridlineStyle": "dashed"
}],
"y2Axis": [{
"show": true,
"secStart": null,
"secEnd": null,
"secAxisScale": "Linear",
"secLabelColor": {"solid": {"color": "#949494"}},
"secFontSize": 8,
"secSontFamily": "Arial",
"secLabelPrecision": 0,
"secLabelDisplayUnits": 0,
"secShowAxisTitle": true,
"secAxisStyle": "ShowUnitOnly",
"secColor": {"solid": {"color": "#949494"}},
"secTitleText": "添加轴标题",
"secTitleFontSize": 8,
"secTitleFontFamily": "Arial"
}],
"zoom: [{... 代码省略 ...}],
"labels": [{
"show": true,
"color": {"solid": {"color": "#ffffff"}},
"labelDisplayUnits": 1,
"labelPrecision": 2,
"labelPosition": "InsideEnd",
"labelOverflow": true,
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
"plotArea": [{... 代码省略 ...}],
"title": [{... 代码省略 ...}],
"background": [{... 代码省略 ...}],
"lockAspect": [{... 代码省略 ...}],
"border": [{... 代码省略 ...}],
"dropShadow": [{... 代码省略 ...}],
"visualTooltip": [{... 代码省略 ...}],
"visualHeader": [{... 代码省略 ...}]
}
}
```

### 格式参考

*   图例关键字见`表 7-2`。
*   类别关键字见`表 7-3`。
*   值关键字见`表 7-4`。
*   缩放关键字见`表 7-5`。
*   标签关键字见`表 7-6`。
*   汇总关键字见`表 7-7`。
*   绘图区关键字见`表 7-8`。
*   工具提示关键字见`表 6-5`。
*   图例位置选项见`表 7-9`。
*   类别轴位置选项见`表 7-10`。
*   值轴显示单位选项见`表 7-11`。
*   值轴样式选项见`表 7-16`。
*   值轴网格线样式选项见`表 7-13`。
*   标签位置选项见`表 7-12`。
*   线条样式位置选项见`表 7-22`。

用于描述面积图格式属性的完整JSON代码可在示例代码文件中以`Attributes_AreaChart.json`形式获取。

## 本章小结

在本章中，你学习了如何准备JSON主题，以对六种核心图表类型应用标准格式：堆叠条形图、堆叠柱形图、簇状条形图、簇状柱形图、折线图和面积图。

你已看到它们共享许多共同特征，这导致了格式定义的JSON代码库在很大程度上得以共享。

下一步是深入学习六种更复杂一些的图表格式规范——但它们都建立在你本章所学的JSON定义之上。

