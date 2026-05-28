# 大多数图表类型共有的元素

既然你已经了解了如何定义共享（或通用）元素的格式，我们就可以进入下一层级的“通用”格式设置。这涉及到设置图表视觉对象的*部分*格式参数。

图表的格式设置有几个方面是多种图表共享的。例如，堆积条形图、堆积柱形图、簇状条形图、簇状柱形图、折线图、面积图、堆积面积图、折线图和簇状柱形图以及折线图和堆积柱形图都包含以下元素：

- 类别（X）轴
- 数值（Y）轴
- 数据标签
- 绘图区
- 图例

事实上，其中某些元素也常见于其他几种图表类型，例如功能区图、瀑布图和散点图（即使这些图表类型仅包含前面列表中的部分格式选项）。甚至还有漏斗图、饼图和环形图等图表，它们至少使用了前面列出的一个格式元素。

为了省去你为每种图表类型分别定义这些相似或相同的图表格式属性的麻烦，你可以指定*标准格式*，该格式将应用于图表元素*（如果存在的话）*。这意味着，如果你创建了一个不包含这些元素之一的图表（例如没有类别和数值轴但*确实*有图例的环形图），但仍在其主题文件中设置了这些属性，那么任何不相关的格式元素定义将*不会应用于*该图表。更重要的是，你*不必定义*格式窗格展开选项卡中存在的*每个格式选项*。也就是说，如果你愿意，你可以仅从所有可用选项中为图例设置一两个格式选项。换句话说，你可以根据需要预定义任意多的图表格式方面，而不必担心是否实际使用它们。Power BI Desktop 不关心 JSON 格式是否被使用——只关心其语法是否准确。这意味着，只要 JSON 语法正确，你可以定义任意多（或少）的通用格式。

下面是一个 JSON 主题文件的摘录，它对一系列共享的图表元素应用了部分（而非全部）格式。你可以在文件`ColorAndTextClassesWithCoreVisualDefaultsAndKeyChartTypes.json`中找到完整的代码。

用于定义一组标准图表格式的 JSON 可能如下所示：

```json
{
"name": "Common chart elements",
"dataColors": ["#12239E", "#118DFF", "#00FF29", "#6B007B", "#FFB000", "#F5F4F0", "#FFFF00", "#FF001E"],
"visualStyles":
{"*":
{"*":
{
"title": [{... code not shown ...}],
"lockAspect": [{... code not shown ...}],
"dropShadow":   [{... code not shown ...}],
"visualHeader": [{... code not shown ...}],
"categoryAxis": [{
"show": true,
"axisScale": "Linear",
"position": "left",
"labelColor": {"solid": {"color": "#949494"}},
"fontFamily": "Arial",
"fontSize": 8,
"showAxisTitle": true,
"titleText": "Add an Axis Title",
"titleFontSize": 8,
"titleFontFamily": "Arial",
"titleColor": {"solid": {"color": "#949494"}},
"gridLineShow": true,
"gridLineColor":  {"solid": {"color": "#949494"}},
"gridLineThickness": 1,
"gridlineStyle": "Solid"
}],
"valueAxis": [{
"show": true,
"position": "Left",
"axisScale": "Linear",
"labelColor": {"solid": {"color": "#949494"}},
"fontFamily": "Arial",
"fontSize": 8,
"showAxisTitle": true,
"titleText": "Add an Axis Title",
"titleFontSize": 8,
"titleFontFamily": "Arial",
"titleColor": {"solid": {"color": "#949494"}},
"gridLineShow": true,
"gridLineColor":  {"solid": {"color": "#949494"}},
"gridLineThickness": 1,
"gridlineStyle": "Solid",
"axisStyle": "ShowTitleOnly"
}],
"labels": [{
"show": true,
"labelPosition": "InsideEnd",
"color": {"solid": {"color": "#ffffff"}},
"fontFamily": "Arial",
"fontSize": 7,
"enableBackground": false,
"backgroundColor": {"solid": {"color": "#E8E8E8"}},
"backgroundTransparency": 0
}],
"plotArea": [{
"transparency": 0
}],
"legend": [{
"show": true,
"position": "Bottom",
"showTitle": true,
"titleText": "Add a legend title",
"legendColor": {"solid": {"color": "#949494"}},
"fontFamily": "Arial",
"fontSize": 8
}]
}
}
}
}
```

在这个 JSON 片段中——再次提醒——这些关键字对应于 Power BI Desktop 中格式窗格的部分。这里引用的部分对于条形图、柱形图、折线图和面积图是通用的。

- *类别（X）轴*由关键字`categoryAxis`定义。
- *数值（Y）轴*由关键字`valueAxis`定义。
- *数据标签*由关键字`labels`定义。
- *绘图区*由关键字`plotArea`定义。
- *图例*由关键字`legend`定义。

## 扩展特定图表类型的通用格式

前面的 JSON 片段展示了如何为五种图表元素定义核心格式。然而，有些情况下，你可能希望添加一些仅适用于少数几种图表类型（甚至单一图表类型）的元素。幸运的是，这只需对 JSON 代码进行简单扩展即可。

例如，假设你想在主题文件中添加以下两个元素：

- 折线图、面积图、堆叠面积图、折线图与簇状柱形图、以及折线图与堆叠柱形图中使用的线条和连接格式
- 圆环图中使用的中心半径百分比

你可以通过以下代码片段来扩展前面的 JSON（并确保在 JSON 文件中用逗号分隔这两段代码）。完整的 JSON 主题文件可在示例文件中找到，名为`ColorAndTextClassesWithCoreVisualDefaultsAndKeyChartTypesExtended.json`。添加到先前片段中的代码如下：

```json
"lineStyles": [{
"ShadedArea": true,
"strokeWidth": 1,
"lineStyle": "Solid",
"showMarker": true,
"markerShape": "Triangle",
"markerSize": 2,
"markerColor": {"solid": {"color": "#949494"}}
}],
"slices": [{
"innerRadiusRatio": 20
}]
```

此 JSON 将仅应用于那些在 Power BI Desktop 中实际包含以下格式部分的图表：

- **形状**（对于折线图和面积图，在 JSON 中用`lineStyles`关键字表示）
- **形状/内部半径**（对于圆环图，用`slices`关键字表示）

正如你所见，Power BI Desktop 格式部分与相应 JSON 关键字之间的关联并非总是完全直观。然而，一旦理解了这些映射关系，创建和修改 JSON 主题文件就变得相当直接了。

## 表格元素

当然，并非 Power BI 中的所有视觉对象都是图表。有些核心视觉对象与图表截然不同，例如表、矩阵和卡片，你可能在仪表板中经常使用它们。幸运的是，你也可以快速轻松地定义这些视觉对象的核心格式。

以下代码片段为以下表格（和矩阵）元素添加了格式规范：

- 网格
- 列标题
- 值
- 总计

幸运的是，JSON 关键字与 Power BI Desktop 格式窗格中针对表和矩阵的节标题非常对应。此外，格式元素是简单的键/值对，与 Power BI Desktop 中的实际格式属性紧密映射，因此这里无需额外解释。

## 再次说明

并非所有在 Power BI Desktop 中可用于总计、值、列标题和网格的格式选项都已通过 JSON 主题文件应用。同样地，任何未在 JSON 文件中设置的格式元素都将保留为应用此主题的 Power BI Desktop 文件中的默认值。此 JSON 代码可在示例文件 `ColorsAndTextClassesWithVisualDefaults.json` 中找到。

## 最后的说明

如果你将本章中探讨的所有 JSON 片段添加到一个主题文件中，完整的 JSON 主题文件将包含以下高级格式定义：

-   调色板
-   全局颜色应用
-   标准字体规范
-   核心图表元素
-   核心表格元素

一个包含所有这些元素并组装成可用主题的文件可在示例数据目录中找到，名为 `GenericBaseFormatting.json`。你可以查看整个文件，内容如下。我意识到这是一段庞大的代码（大约 230 行），但值得全面了解你创建的所有内容以及它们是如何组合成一个连贯主题的。

```
"style":  [{"styleType": "Minimal"}],
"grid":    [{
"gridVertical": false,
"gridVerticalColor": {"solid": {"color": "#171796"}},
"gridVerticalWeight": 1,
"gridHorizontal": false,
"gridHorizontalColor": {"solid": {"color": "#171796"}},
"gridHorizontalWeight": 1,
"textSize": 8,
"rowPadding": 1,
"outlineColor": {"solid": {"color": "#ffffff"}},
"outlineWeight": 1
}],
"columnHeaders": [{
"fontColor": {"solid": {"color": "#ffffff"}},
"backColor": {"solid": {"color": "#E34B20"}},
"outline": "Frame",
"alignment": "Left",
"wordWrap": true
}],
"values": [{
"fontColorPrimary": {"solid": {"color": "#3D4E57"}},
"backColorPrimary": {"solid": {"color": "#F5F4F0"}},
"fontColorSecondary": {"solid": {"color": "#3D4E57"}},
"backColorSecondary": {"solid": {"color": "#E3DFD4"}},
"outline": "Frame",
"alignment": "Left",
"wordWrap": true
}],
"total": [{
"totals": true,
"fontColor": {"solid": {"color": "#000000"}},
"backColor": {"solid": {"color": "#D1CAB8"}},
"outline": "Frame"
}]
```

```json
{
"name": "通用基础格式",
"dataColors": ["#171796", "#E34B20", "#F4B7A6", "#465437", "#E2EB00", "#615E55", "#00AC98", "#73268c"],
"good": "#1AAB40",
"neutral": "#D9B300",
"bad": "#D64554",
"maximum": "#118DFF",
"center": "#D9B300",
"minimum": "#DEEFFF",
"null": "#FF7F48",
"background": "#FFFFFF",
"firstLevelElements": "#949494",
"secondLevelElements": "#949494",
"thirdLevelElements": "#D5D5D5",
"fourthLevelElements": "#B7B7B7",
"secondaryBackground": "#E8E8E8",
"tableAccent": "#118DFF",
"textClasses": {
"label": {
"fontSize": 9,
"fontFace": "Arial",
"color": "#949494"
},
"callout": {
"fontSize": 18,
"fontFace": "Arial",
"color": "#808080"
},
"title": {
"fontSize": 12,
"fontFace": "Arial",
"color": "#949494"
},
"header": {
"fontSize": 12,
"fontFace": "Arial",
"color": "#808080"
},
"largeTitle": {
"fontSize": 14,
"fontFace": "Arial",
"color": "#808080"
},
"largeLabel": {
"fontSize": 12,
"fontFace": "Arial",
"color": "#808080"
},
"smallLabel": {
"fontSize": 8,
"fontFace": "Arial",
"color": "#949494"
},
"semiboldLabel": {
"fontSize": 8,
"fontFace": "Arial",
"color": "#808080"
},
"boldLabel": {
"fontSize": 8,
"fontFace": "Arial",
"color": "#808080"
},
"lightLabel": {
"fontSize": 8,
"fontFace": "Arial",
"color": "#949494"
},
"largeLightLabel": {
"fontSize": 8,
"fontFace": "Arial",
"color": "#949494"
},
"smallLightLabel": {
"fontSize": 8,
"fontFace": "Arial",
"color": "#949494"
}
},
"visualStyles": {
"*": {
"*": {
"background": [{"color": {"solid": {"color": "#F5F4F0"}}, "transparency": 0}],
"visualTooltip": [{"titleFontColor": {"solid": {"color": "#D7CACA"}}, "valueFontColor": {"solid": {"color": "#FFFFFF"}}, "background": {"solid": {"color": "#938888"}}}],
"background": [{"color": {"solid": {"color": "#7A0A0A"}}}],
"title": [{"show": true, "text": "视觉对象标题", "fontColor": {"solid": {"color": "#ffffff"}}, "background": {"solid": {"color": "#171796"}}, "alignment": "Center", "fontSize": 8, "fontFamily": "Arial"}],
"lockAspect": [{"show": false}],
"border": [{"show": true, "color": {"solid": {"color": "#171796"}}}],
"dropShadow": [{"show": true, "color": {"solid": {"color": "#D7CACA"}}, "position": "外", "preset": "左上"}],
"visualHeader": [{"show": true, "background": {"solid": {"color": "#ffffff"}}, "border": {"solid": {"color": "#171796"}}, "foreground": {"solid": {"color": "#171796"}}}],
"categoryAxis": [{"show": true, "axisScale": "线性", "position": "左", "labelColor": {"solid": {"color": "#949494"}}, "fontFamily": "Arial", "fontSize": 8, "showAxisTitle": true, "titleText": "添加轴标题", "titleFontSize": 8, "titleFontFamily": "Arial", "titleColor": {"solid": {"color": "#949494"}}, "gridLineShow": true, "gridLineColor": {"solid": {"color": "#949494"}}, "gridLineThickness": 1, "gridLineStyle": "实线"}],
"valueAxis": [{"show": true, "position": "左", "axisScale": "线性", "labelColor": {"solid": {"color": "#949494"}}, "fontFamily": "Arial", "fontSize": 8, "showAxisTitle": true, "titleText": "添加轴标题", "titleFontSize": 8, "titleFontFamily": "Arial", "titleColor": {"solid": {"color": "#949494"}}, "gridLineShow": true, "gridLineColor": {"solid": {"color": "#949494"}}, "gridLineThickness": 1, "gridLineStyle": "实线", "axisStyle": "仅显示标题"}],
"labels": [{"show": true, "labelPosition": "内部末端", "color": {"solid": {"color": "#ffffff"}}, "fontFamily": "Arial", "fontSize": 7, "enableBackground": false, "backgroundColor": {"solid": {"color": "#d9d9d9"}}, "backgroundTransparency": 0}],
"plotArea": [{"transparency": 0}],
"legend": [{"show": true, "position": "底部", "showTitle": true, "titleText": "添加图例标题", "legendColor": {"solid": {"color": "#949494"}}, "fontFamily": "Arial", "fontSize": 8}],
"lineStyles": [{"ShadedArea": true, "strokeWidth": 1, "lineStyle": "实线", "showMarker": true, "markerShape": "三角形", "markerSize": 2, "markerColor": {"solid": {"color": "#949494"}}}],
"slices": [{"innerRadiusRatio": 20}],
"grid": [{"gridVertical": true, "gridVerticalColor": {"solid": {"color": "#171796"}}, "gridVerticalWeight": 1, "gridHorizontal": true, "gridHorizontalColor": {"solid": {"color": "#171796"}}, "gridHorizontalWeight": 1, "textSize": 10, "rowPadding": 2, "outlineColor": {"solid": {"color": "#ffffff"}}, "outlineWeight": 1}],
"columnHeaders": [{"fontColor": {"solid": {"color": "#000000"}}, "backColor": {"solid": {"color": "#E34B20"}}, "outline": "框架", "alignment": "左", "wordWrap": true}],
"values": [{"fontColorPrimary": {"solid": {"color": "#3D4E57"}}, "backColorPrimary": {"solid": {"color": "#F5F4F0"}}, "fontColorSecondary": {"solid": {"color": "#3D4E57"}}, "backColorSecondary": {"solid": {"color": "#E3DFD4"}}, "outline": "框架", "alignment": "左", "wordWrap": true}],
"total": [{"totals": true, "fontColor": {"solid": {"color": "#000000"}}, "backColor": {"solid": {"color": "#D1CAB8"}}, "outline": "框架"}]
}
}
}
}
```

现在你已经构建了一个JSON主题文件，它可以立即格式化Power BI仪表板的大部分内容。值得回顾一下你创建的内容。

该文件本质上只是JSON，但它可以细分为三个主要区域：

*   颜色（`Colors`）
*   文本（`textClasses`）
*   视觉元素（`visualStyles`）

## 累积性元素
此文件包含累积性的元素 —— 即它可以一次性应用于多个不同的格式区域。

## 样式层级结构
存在一个样式层级结构。也就是说，你可以应用一个初始的“顶层”样式（例如，通过定义`textClasses`中的`title`元素），然后还可以指定图表的标题采用不同的格式。这些细微差别将在最后一章中解释。

## 格式定义的灵活性
正如我之前提到的，在定义格式时，你不必定义格式化元素的每一个方面。换句话说，并非所有“部分”都是必须的，你可以选择使用主题文件格式化视觉对象的哪些部分（或共享此公共元素的视觉对象集合）。

## 关键注意事项
主题文件中元素的顺序并不重要。因此，你可以将`visualStyles`元素放在`textClasses`元素之上。然而，真正重要的是：

*   确保JSON语法正确。
*   检查定义样式元素的关键字是否为Power BI所期望的。
*   任何文本元素（例如字体名称或Power BI Desktop弹出窗口中的选择）都包含Power BI期望的值。

此外，你当然可以获取完整的“通用”主题文件（`GenericBaseFormatting.json`）并对其进行调整，以使用你偏好的颜色和其他设置。你还可以扩展它，添加更多高级格式化属性。在接下来的八章中，你将学习详细的JSON格式化的完整范围。