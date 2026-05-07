# 操作按钮

操作按钮的格式设置方式与主题文件中的其他视觉元素完全相同。

指定操作按钮格式属性的关键字是 `actionButton`。

表 12-1 概述了可用于操作按钮格式设置的 JSON 关键字。

**表 12-1** 操作按钮关键字

| 关键字 | 格式设置区域 |
| --- | --- |
| `general` | 常规 |
| `text` | 按钮文本 |
| `icon` | 图标 |
| `outline` | 轮廓 |
| `fill` | 填充 |
| `title` | 标题 |
| `background` | 背景 |
| `lockAspect` | 锁定纵横比 |
| `border` | 边框 |
| `dropShadow` | 阴影 |
| `visualHeader` | 视觉标头 |

以下代码片段给出了操作按钮格式设置的 JSON 主题。它的长度惊人，因为多个元素可以根据按钮状态进行不同的格式设置。这实际上会倍增代码中的某些子部分。

```
"actionButton":
{"*":
{
"text": [{
"show": true},
{
"$id": "default",
"text": "Button Text",
"fontColor": {"solid": {"color": "#808080"}},
"padding": 3,
"verticalAlignment": "middle",
"horizontalAlignment": "center",
"fontSize": 10,
"fontFamily": "Arial"
},
{
"$id": "hover",
"text": "Hover button Text",
"fontColor": {"solid": {"color": "#808080"}},
"padding": 3,
"verticalAlignment": "middle",
"horizontalAlignment": "center",
"fontSize": 10,
"fontFamily": "Arial"
},
{
"$id": "selected",
"text": "Press button Text",
"fontColor": {"solid": {"color": "#808080"}},
"padding": 3,
"verticalAlignment": "middle",
"horizontalAlignment": "center",
"fontSize": 10,
"fontFamily": "Arial"
},
{
"$id": "disabled",
"text": "Disabled button Text",
"fontColor": {"solid": {"color": "#808080"}},
"padding": 3,
"verticalAlignment": "middle",
"horizontalAlignment": "center",
"fontSize": 10,
"fontFamily": "Arial"
}],
"icon": [{
"show": true
},
{
"$id": "default",
"shapeType": "help",
"padding": 3,
"verticalAlignment": "middle",
"horizontalAlignment": "center",
"lineColor": {"solid": {"color": "#F5F4F0"}},
"lineTransparency": 10,
"lineWeight": 2
},
{
"$id": "hover",
"shapeType": "help",
"padding": 3,
"verticalAlignment": "middle",
"horizontalAlignment": "center",
"lineColor": {"solid": {"color": "#F5F4F0"}},
"lineTransparency": 10,
"lineWeight": 2
},
{
"$id": "selected",
"shapeType": "help",
"padding": 3,
"verticalAlignment": "middle",
"horizontalAlignment": "center",
"lineColor": {"solid": {"color": "#F5F4F0"}},
"lineTransparency": 10,
"lineWeight": 2
},
{
"$id": "disabled",
"shapeType": "help",
"padding": 3,
"verticalAlignment": "middle",
"horizontalAlignment": "center",
"lineColor": {"solid": {"color": "#F5F4F0"}},
"lineTransparency": 10,
"lineWeight": 2
}],
"outline": [{
"show": true
},
{
"$id": "default",
"lineColor": {"solid": {"color": "#F5F4F0"}},
"transparency": 10,
"weight": 2,
"roundEdge": 3
},
{
"$id": "hover",
"lineColor": {"solid": {"color": "#F5F4F0"}},
"transparency": 5,
"weight": 2,
"roundEdge": 3
},
{
"$id": "selected",
"lineColor": {"solid": {"color": "#F5F4F0"}},
"transparency": 15,
"weight": 2,
"roundEdge": 3
},
{
"$id": "disabled",
"lineColor": {"solid": {"color": "#F5F4F0"}},
"transparency": 20,
"weight": 2,
"roundEdge": 3
}],
"fill":
[{
"show":true
},
{
"$id": "default",
"transparency": 0,
"fillColor": {"solid":{"color":"#949494"}}
},
{
"$id": "hover",
"transparency": 0,
"fillColor": {"solid":{"color":"#949494"}}
},                                                   {
"$id": "selected",
"transparency": 0,
"fillColor": {"solid":{"color":"#111111"}}
},                                                   {
"$id": "disabled",
"transparency": 0,
"fillColor": {"solid":{"color":"#111111"}}
}],
"title": [{...代码未显示...}],
"background": [{...代码未显示...}],
"lockAspect": [{...代码未显示...}],
"border": [{...代码未显示...}],
"dropShadow": [{...代码未显示...}],
"visualHeader": [{...代码未显示...}]
}
}
```

首先值得注意的是，您可以为 `icon`、`fill` 和 `text` 设置属性，以反映以下四种按钮状态的不同设置：

- 默认
- 悬停
- 按下
- 禁用

引入了一个新概念——按钮状态——这会影响主题文件 JSON 中的对象嵌套。实际上，在操作按钮的每个主要元素内部都应用了一层额外的嵌套。

每种状态都使用 JSON 中定义按钮状态的 `$id` 关键字来定义。对于 JSON 中指定了 `$id` 关键字的两个部分（`text` 和 `icon`），您需要使用表 12-2 中显示的值之一。这样做的结果是，JSON 中会有*四个*几乎相同的子部分来描述文本和图标的格式设置——每个按钮状态对应一个。

**表 12-2** 按钮状态选项

| 值 | 格式弹出选项 |
| --- | --- |
| `default` | 默认状态 |
| `hover` | 悬停时 |
| `selected` | 按下时 |
| `disabled` | 已禁用 |

描述按钮文本区域的 `text` 关键字使用表 12-3 中显示的关键字。

**表 12-3** 按钮文本区域的关键字映射

| 元素 | 关键字 |
| --- | --- |
| 按钮文本 | `show` |
| 按钮文本 | `text` |
| 字体颜色 | `fontColor` |
| 内边距 | `padding` |
| 垂直对齐 | `verticalAlignment` |
| 水平对齐 | `horizontalAlignment` |
| 文本大小 | `fontSize` |
| 字体 | `fontFamily` |

描述图标区域的 `icon` 关键字使用表 12-4 中显示的关键字。

**表 12-4** 图标区域的关键字映射

| 元素 | 关键字 |
| --- | --- |
| 图标 | `show` |
| 形状 | `shapeType` |
| 内边距 | `padding` |
| 垂直对齐 | `verticalAlignment` |
| 水平对齐 | `horizontalAlignment` |
| 线条颜色 | `lineColor` |
| 透明度 | `lineTransparency` |
| 粗细 | `lineWeight` |

描述轮廓区域的 `outline` 关键字使用表 12-5 中显示的关键字。

**表 12-5** 轮廓区域的关键字映射

| 元素 | 关键字 |
| --- | --- |
| 轮廓 | `show` |
| 轮廓颜色 | `lineColor` |
| 透明度 | `transparency` |
| 轮廓粗细 | `weight` |
| 圆角 | `roundEdge` |

描述填充区域的 `fill` 关键字使用表 12-6 中显示的关键字。

**表 12-6** 填充区域的关键字映射

| 元素 | 关键字 |
| --- | --- |
| 填充 | `show` |
| 填充颜色 | `fillColor` |
| 透明度 | `transparency` |

表 12-7 显示了图标的垂直对齐属性。

**表 12-7** 垂直对齐选项

| 值 | 格式弹出选项 |
| --- | --- |
| `top` | 顶部 |
| `middle` | 居中 |
| `bottom` | 底部 |

表 12-8 显示了图标的水平对齐属性。

**表 12-8** 水平对齐选项

| 值 | 格式弹出选项 |
| --- | --- |
| `left` | 左对齐 |
| `center` | 居中对齐 |
| `right` | 右对齐 |

您可以在示例文件 `Attributes_ActionButton.json` 中找到操作按钮的 JSON 代码。



## 切片器

正如你所料，切片器的格式也可以在主题文件中进行设置。

指定切片器格式属性的关键字是 `slicer`。

表 12-9 概述了可在切片器格式设置中使用的 JSON 关键字。

**表 12-9**
**切片器关键字**

| 关键字 | 格式设置部分 |
| --- | --- |
| `general` | 常规 |
| `slicerHeader` | 切片器标头 |
| `numericInputStyle` | 数字输入 |
| `slider` | 滑块 |
| `selection` | 选择控件 |
| `header` | 标头 |
| `date` | 日期 |
| `items` | 项 |
| `title` | 标题 |
| `background` | 背景 |
| `lockAspect` | 锁定纵横比 |
| `border` | 边框 |
| `dropShadow` | 阴影 |
| `visualHeader` | 视觉对象标头 |

需要记住的是，在 Power BI Desktop 中，并非所有切片器部分的定义元素都会同时可见，这取决于你定义的切片器类型。不过，你仍然可以使用相应的关键字在主题文件中指定所有可用的选项。

以下代码片段展示了用于格式化切片器的 JSON：

```json
"slicer":
{"*":
{
"general":  [{
"outlineColor": {"solid": {"color": "#909090"}},
"outlineWeight": 1,
"orientation": "Horizontal",
"responsive": false
}],
"selection": [{
"show": true,
"singleSelect": true,
"strictSingleSelect": false,
"selectAllCheckboxEnabled": false
}],
"header":   [{
"show": false,
"text": "Slicer Text",
"fontColor": {"solid": {"color": "#ffffff"}},
"background": {"solid": {"color": "#7A8C97"}},
"outline": "None",
"textSize": 9,
"fontFamily": "Arial"
}],
"numericInputStyle":[{
"fontColor": {"solid": {"color": "#ffffff"}},
"backgroundColor": {"solid": {"color": "#B7B7B7"}},
"fontSize": 9,
"fontFamily": "Arial"
}],
"slider":   [{
"color": {"solid": { "color": "#F5F4F0"} }
}],
"items":    [{
"fontColor": {"solid": {"color": "#F5F4F0"}},
"backgroundColor": {"solid": {"color": "#B7B7B7"}},
"outline": "None",
"textSize": 8,
"fontFamily": "Arial"
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

描述选择控件部分的 `general` 关键字使用了表 12-10 中所示的关键字。

**表 12-10**
**常规部分的关键字映射**

| 元素 | 关键字 |
| --- | --- |
| 轮廓线颜色 | `outlineColor` |
| 轮廓线粗细 | `outlineWeight` |
| 方向 | `orientation` |
| 响应式 | `responsive` |

描述选择控件部分的 `selection` 关键字使用了表 12-11 中所示的关键字。

**表 12-11**
**选择控件部分的关键字映射**

| 元素 | 关键字 |
| --- | --- |
| 选择控件 | `show` |
| 单选 | `singleSelect` |
| 按住 CTRL 键多选 | `strictSingleSelect` |
| 显示“全选”选项 | `selectAllCheckboxEnabled` |

描述标头部分的 `header` 关键字使用了表 12-12 中所示的关键字。

**表 12-12**
**切片器标头部分的关键字映射**

| 元素 | 关键字 |
| --- | --- |
| 切片器标头 | `show` |
| 标题文本 | `text` |
| 字体颜色 | `fontColor` |
| 背景 | `background` |
| 轮廓线 | `outline` |
| 文本大小 | `textSize` |
| 字体系列 | `fontFamily` |

描述数字输入部分的 `numericInputStyle` 关键字使用了表 12-13 中所示的关键字。

**表 12-13**
**数字输入部分的关键字映射**

| 元素 | 关键字 |
| --- | --- |
| 字体颜色 | `fontColor` |
| 背景 | `backgroundColor` |
| 文本大小 | `textSize` |
| 字体系列 | `fontFamily` |

描述滑块部分的 `slider` 关键字使用了表 12-14 中所示的关键字。

**表 12-14**
**滑块部分的元素关键字映射**

| 元素 | 关键字 |
| --- | --- |
| 滑块 | `show` |
| 颜色 | `color` |

描述项部分的 `items` 关键字使用了表 12-15 中所示的关键字。

**表 12-15**
**项部分的关键字映射**

| 元素 | 关键字 |
| --- | --- |
| 字体颜色 | `fontColor` |
| 背景 | `backgroundColor` |
| 轮廓线 | `outline` |
| 文本大小 | `textSize` |
| 字体系列 | `fontFamily` |

你可以在示例文件 `Attributes_Slicer.json` 中找到相应的 JSON 代码。

标头和项的轮廓线选项见表 6-10。

方向选项见表 7-15。



