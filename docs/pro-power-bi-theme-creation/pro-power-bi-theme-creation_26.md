# 6. 卡片和表格视觉样式

为了扩展您对 JSON 格式的了解，现在可以继续学习一些核心的数据驱动视觉对象。一组有用的视觉对象包括：

*   卡片
*   多行卡片
*   表格
*   矩阵

我按特定顺序处理这组视觉对象，因为我倾向于从最简单的开始，并在本章中逐步过渡到更复杂的视觉对象。这也有助于理解如何构建您的知识体系，因为 JSON 主题是通过重复使用（或大体相似）的元素来定义的。

本章讨论的四个视觉对象在样例文件夹中各有一个独立的 JSON 文件，其中包含了可用于定义您自己的主题文件的代码。

## 卡片

最简单的视觉对象之一（这意味着主题文件中的 JSON 也简洁得令人感到轻松）是卡片视觉对象。用于引入卡片格式属性的关键词是 `card`。

表 6-1 描述了与 Power BI Desktop 中每个格式设置区域相对应的 JSON 关键词。

**表 6-1** 卡片格式设置元素

| 关键词 | 格式设置区域 |
| --- | --- |
| `general` | 常规 |
| `labels` | 数据标签 |
| `categoryLabels` | 类别标签 |
| `wordwrap` | 自动换行 |
| `title` | 标题 |
| `background` | 背景 |
| `lockAspect` | 锁定纵横比 |
| `border` | 边框 |
| `dropShadow` | 阴影 |
| `visualTooltip` | 工具提示 |
| `visualHeader` | 视觉对象标头 |

以下代码片段为您提供了用于指定标准卡片格式的核心元素。样例文件 `Attributes_Card.json` 包含了可在主题文件中为卡片设置的所有元素。

```json
"card":
{"*":
{
"general": [{... 代码未显示 ...}]
"labels":   [{
"color": {"solid": {"color": "#12239E"}},
"labelDisplayUnits": 1000,
"labelPrecision": 2,
"fontSize": 36,
"fontFamily": "Arial",
"preserveWhitespace": false
}],
"categoryLabels":  [{
"show": true,
"color": {"solid": {"color": "#12239E"}},
"fontSize": 24,
"fontFamily": "Arial"
}],
"wordwrap": [{
"show": true
}],
"title": [{... 代码未显示 ...}],
"background": [{... 代码未显示 ...}],
"lockAspect": [{... 代码未显示 ...}],
"border": [{... 代码未显示 ...}],
"dropShadow": [{... 代码未显示 ...}],
"visualTooltip":[{
"show": true,
"titleFontColor":{"solid":{"color":"#E8E8E8"}},
"valueFontColor":{"solid":{"color":"#FFFFFF"}},
"fontSize": 11,
"fontFamily": "Arial Black",
"background":{"solid":{"color":"#938888"}},
"transparency": 30
}],
"visualHeader": [{... 代码未显示 ...}]
}
}
```

描述数据标签部分的 `labels` 关键词使用表 6-2 中所示的关键词。

**表 6-2** 数据标签的关键词映射

| 元素 | 关键词 |
| --- | --- |
| 颜色 | `color` |
| 显示单位 | `labelDisplayUnits` |
| 值的小数位数 | `labelPrecision` |
| 文本大小 | `fontSize` |
| 字体系列 | `fontFamily` |
| 源间距 | `preserveWhitespace` |

描述类别标签部分的 `categoryLabels` 关键词使用表 6-3 中所示的关键词。

**表 6-3** 类别标签的关键词映射

| 元素 | 关键词 |
| --- | --- |
| 类别标签 | `show` |
| 颜色 | `color` |
| 文本大小 | `fontSize` |
| 字体系列 | `fontFamily` |

描述自动换行部分的 `wordWrap` 关键词使用表 6-4 中所示的关键词。

**表 6-4** 自动换行的关键词映射

| 元素 | 关键词 |
| --- | --- |
| 自动换行 | `show` |

描述工具提示部分的 `visualTooltip` 关键词使用表 6-5 中所示的关键词。

**表 6-5** 工具提示的关键词映射

| 元素 | 关键词 |
| --- | --- |
| 工具提示 | `show` |
| 标签颜色 | `titleFontColor` |
| 值颜色 | `valueFontColor` |
| 文本大小 | `fontSize` |
| 字体系列 | `fontFamily` |
| 背景颜色 | `background` |
| 透明度 | `transparency` |

## 多行卡片

多行卡片在许多方面是卡片主题的扩展，但仍有足够多的不同之处，需要一组独立的格式设置元素。

用于指定多行卡片格式属性的关键词是 `multiRowCard`。

表 6-6 概述了可用于多行卡片格式设置的 JSON 关键词。

**表 6-6** 多行卡片格式设置元素

| 关键词 | 格式设置区域 |
| --- | --- |
| `General` | 常规 |
| `dataLabels` | 数据标签 |
| `categoryLabels` | 类别标签 |
| `cardTitle` | 卡片标题 |
| `Card` | 卡片 |
| `Title` | 标题 |
| `Background` | 背景 |
| `lockAspect` | 锁定纵横比 |
| `Border` | 边框 |
| `dropShadow` | 阴影 |
| `visualHeader` | 视觉对象标头 |

格式化多行卡片所需的完整 JSON 可在样例文件 `Attributes_MultiRowCard.json` 中找到。您可以在以下代码片段中看到此视觉对象类型特有的元素：

```json
"multiRowCard":
{"*":
{
"general": [{... 代码未显示 ...}]
"dataLabels": [{
"color": {"solid": {"color": "#12239E"}},
"fontSize": 10,
"fontFamily": "Arial"
}],
"categoryLabels":   [{
"show": true,
"color": {"solid": {"color": "#12239E"}},
"fontSize": 20,
"fontFamily": "Arial"
}],
"cardTitle":    [{
"fontColor": {"solid": {"color": "#ffffff"}},
"fontSize": 16,
"fontFamily": "Arial"
}],
"card":   [{
"outline": "BottomOnly",
"outlineColor": {"solid": {"color": "#12239E"}},
"outlineWeight": 3,
"barShow": true,
"barColor": {"solid": {"color": "#12239E"}},
"barWeight": 5,
"cardPadding": 15,
"cardBackground": {"solid": {"color": "#F5F4F0"}}
}],
"title": [{... 代码未显示 ...}],
"background": [{... 代码未显示 ...}],
"lockAspect": [{... 代码未显示 ...}],
"border": [{... 代码未显示 ...}],
"dropShadow": [{... 代码未显示 ...}],
"visualHeader": [{... 代码未显示 ...}]
}
}
```

描述数据标签部分的 `dataLabels` 关键词使用表 6-7 中所示的关键词。

**表 6-7** 数据标签的关键词映射

| 元素 | 关键词 |
| --- | --- |
| 颜色 | `color` |
| 文本大小 | `fontSize` |
| 字体系列 | `fontFamily` |

多行卡片的类别标签部分可在表 6-3 中找到。

描述卡片标题部分的 `cardTitle` 关键词使用表 6-8 中所示的关键词。

**表 6-8** 卡片标题的关键词映射

| 元素 | 关键词 |
| --- | --- |
| 颜色 | `fontcolor` |
| 文本大小 | `fontSize` |
| 字体系列 | `fontFamily` |

描述卡片部分的 `card` 关键词使用表 6-9 中所示的关键词。

**表 6-9** 卡片元素的关键词映射

| 元素 | 关键词 |
| --- | --- |
| 轮廓 | `outline` |
| 轮廓颜色 | `outlineColor` |
| 轮廓粗细 | `outlineWeight` |
| 显示条 | `barShow` |
| 条颜色 | `barColor` |
| 条粗细 | `barWeight` |
| 内边距 | `cardPadding` |
| 背景 | `cardBackground` |

卡片部分中 JSON 使用的 `outline` 关键词需要表 6-10 中所示的一个属性。

**表 6-10** 卡片轮廓样式选项

| 值 | 格式设置弹出选项 |
| --- | --- |
| `None` | 无 |
| `BottomOnly` | 仅底部 |
| `TopOnly` | 仅顶部 |
| `LeftOnly` | 仅左侧 |
| `RightOnly` | 仅右侧 |
| `TopBottom` | 顶部 + 底部 |
| `LeftRight` | 左侧 + 右侧 |
| `Frame` | 框架 |

**注意** 再次强调，格式的拼写必须与前面表格中给出的完全一致。如果您添加了空格或不遵循大小写规则，则表格样式将不会生效，而会应用默认值。



