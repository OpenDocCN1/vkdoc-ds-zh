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