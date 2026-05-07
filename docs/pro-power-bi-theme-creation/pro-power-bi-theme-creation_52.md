# 筛选窗格

令人惊讶的是——毕竟它并非可视化对象——但你也可以像在 Power BI Desktop 中交互式设置格式一样，在主题文件中设置筛选窗格的格式。

没有单个关键词用于引入筛选窗格的格式属性。这是因为筛选窗格的格式是在**通用格式**级别设置的。你首次在第 4 章中遇到了这个概念。实际上，如果需要快速回顾通用格式的概念，建议参考第 4 章。

筛选窗格的格式在此级别使用两个关键词定义。它们是：

* `outspacePane`
* `filterCard`

表 12-16 概述了可在筛选窗格格式中使用的 JSON 关键词。

**表 12-16** 切片器关键词

| 关键词 | 格式部分 |
| --- | --- |
| `outspacePane` | 筛选窗格 |
| `filterCard` | 筛选卡片 |

```
"visualStyles":
{"*":
{"*":
{
"outspacePane": [{
"backgroundColor": {"solid": {"color": "#999999"}},
"transparency": 30,
"foregroundColor": {"solid": {"color": "#374649"}},
"titleSize": 18,
"headerSize": 12,
"fontFamily": "Arial",
"border": true,
"borderColor": {"solid": {"color": "#909090"}},
"width": 190,
"checkboxAndApplyColor": {"solid": {"color": "#374649"}},
"searchTextSize": 11,
"inputBoxColor": {"solid": {"color": "#ffffff"}}
}],
"filterCard": [{
"$id": "Applied",
"backgroundColor": {"solid": {"color": "#171796"}},
"transparency": 0,
"border": true,
"borderColor": {"solid": {"color": "#909090"}},
"foregroundColor": {"solid": {"color": "#ffffff"}},
"textSize": 10,
"fontFamily": "Arial",
"inputBoxColor": {"solid": {"color": "#C8C8C8"}}
},
{
"$id": "Available",
"backgroundColor": {"solid": {"color": "#171796"}},
"transparency": 40,
"border": true,
"borderColor": {"solid": {"color": "#909090"}},
"foregroundColor": {"solid": {"color": "#ffffff"}},
"textSize": 9,
"fontFamily": "Arial",
"inputBoxColor": {"solid": {"color": "#777777"}}
}]
}
}
}
}
```

描述筛选窗格部分的 `outspacePane` 关键词使用了表 12-17 中所示的关键词。

**表 12-17** 筛选窗格部分的关键词映射

| 元素 | 关键词 |
| --- | --- |
| 背景颜色 | `backgroundColor` |
| 透明度 | `transparency` |
| 字体和图标颜色 | `foregroundColor` |
| 标题文本字号 | `titleSize` |
| 表头文本字号 | `headerSize` |
| 字体系列 | `fontFamily` |
| 边框 | `border` |
| 边框颜色 | `borderColor` |
| 宽度 | `width` |
| 复选框和应用颜色 | `checkboxAndApplyColor` |
| 搜索文本字号 | `searchTextSize` |
| 输入框颜色 | `inputBoxColor` |

在查看这些元素和关键词之前，值得注意的是，有两种类型的筛选卡片：

* 已应用
* 未应用

它们分别代表已应用或未应用筛选器的卡片。

这两种筛选卡片状态通过 `$id` 关键词引入。这意味着为每种筛选器状态（正如你在本章前面看到的操作按钮状态那样）重复了 JSON 代码。

在定义筛选卡片类型的 JSON 中，`$id` 关键词使用了表 12-18 中所示的关键词。

**表 12-18** 按钮状态选项

| 值 | 格式弹出选项 |
| --- | --- |
| `Applied` | 已应用 |
| `Available` | 可用 |

描述筛选卡片部分的 `filterCard` 关键词使用了表 12-19 中所示的关键词。

**表 12-19** 筛选卡片部分的关键词映射

| 元素 | 关键词 |
| --- | --- |
| 背景颜色 | `backgroundColor` |
| 透明度 | `transparency` |
| 边框 | `border` |
| 边框颜色 | `borderColor` |
| 字体和图标颜色 | `foregroundColor` |
| 文本字号 | `textSize` |
| 字体系列 | `fontFamily` |
| 输入框颜色 | `inputBoxColor` |

你可以在示例文件 `Attributes_FilterCard.json` 中找到此 JSON 代码。

