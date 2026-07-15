# 5. 对象视觉样式

从本书的这一点开始，你将深入探索 `JSON` 主题文件的最深层。这可能会令人不知所措，因此我打算循序渐进地展示 `JSON` 格式化在很大程度上是一个逐步构建所需知识的过程。此外，由于 `Power BI` 主题包含许多重复的代码片段，你将学会从每个可视化特有的代码中，辨别出在许多（如果不是大多数）可视化中反复使用的核心元素。这门艺术不仅在于学会理解 `JSON` 如何被用于定义视觉格式，还在于利用已获得的知识和原则作为跳板，去发现可以应用主题的新领域。

在本章中，你将看到如何编写 `JSON` 主题代码，以完全、极其细致地定义以下可视化对象：

- 文本框
- 图片
- 形状

这些可视化对象的优势在于比某些其他可用的可视化对象更简单，这使它们成为介绍更复杂和详细的 `JSON` 格式化的理想主题。此外，它们不依赖于数据，这使得格式化——以及使用主题文件进行预定义——更加简单。另一个加分点是，它们的许多格式化属性与 `Power BI` 中可用的许多可视化对象通用。这意味着你可以通过这些可视化对象学习一些基本的格式化关键字和方法，然后在阅读本书的过程中，你发现的格式化技巧应用于一系列其他可视化对象。

本章讨论的三种可视化类型在示例文件夹中各自有一个独立的 `JSON` 文件，其中包含可用于定义你自己的主题文件的代码。那么，让我们继续这段旅程吧！

# 文本框

要开始深入探索详细的 JSON 格式设置，文本框可视化对象是一个不错的起点。因为这类可视化对象本质上包含了几乎所有可视化对象共有的元素集。这些元素（按其在 Power BI Desktop 格式窗格中出现的顺序排列）包括：

*   常规
*   标题
*   背景
*   锁定纵横比
*   边框
*   阴影
*   视觉对象标头

你可以在示例文件 `Attributes_Textbox.json` 中找到用于格式化文本框的 JSON 代码。它是一个完整的、独立的主题文件，可以用作 Power BI 主题。然而，它只会格式化调色板、仪表板背景和前景色，当然，以及你添加到仪表板中的任何文本框。

## 格式化文本框的JSON代码示例

以下代码片段提供了用于预格式化文本框的 JSON 代码：

```json
{
"name": "文本框属性",
"dataColors": ["#171796", "#E34B20", "#D1CAB8", "#465437", "#E2EB00", "#615E55", "#00AC98", "#73268c"],
"background": "#FFFFFF",
"foreground": "#949494",
"visualStyles":
{
"textbox": {
"*" :
{
"general": [{"responsive": true}]
"title":    [{
"show": false,
"text": "形状标题",
"titleWrap": false,
"fontColor": {"solid": {"color": "#ffffff"}},
"background": {"solid": {"color": "#171796"}},
"alignment": "居中",
"fontSize": 8,
"fontFamily": "Arial"
}],
"background":   [{
"color": {"solid": {"color": "#F5F4F0"}},
"transparency": 0
}],
"lockAspect": [{
"show": false
}],
"border": [{
"show": true,
"color": {"solid": {"color": "#F5F4F0"}}
}],
"dropShadow":   [{
"show": true,
"color": {"solid": {"color": "#D7CACA"}},
"position":"内部",
"preset":"顶部",
"shadowSpread": 15,
"shadowBlur": 20,
"angle": 50,
"shadowDistance": 15,
"transparency": 80
}],
"visualHeader":    [{
"show": true,
"background": {"solid": {"color": "#ffffff"}},
"border": {"solid": {"color": "#171796"}},
"foreground": {"solid": {"color": "#171796"}},
"showVisualInformationButton": false,
"showVisualWarningButton": false,
"showVisualErrorButton": false,
"showDrillRoleSelector": true,
"showDrillToggleButton": false,
"showDrillUpButton": false,
"showDrillDownLevelButton": false,
"showDrillDownExpandButton": false,
"showPinButton": false,
"showFocusModeButton": false,
"showFilterRestatementButton": false,
"showSeeDataLayoutToggleButton": false,
"showOptionsMenu": false,
"showTooltipButton": true
}]
}
}
}
}
```

当然，在实际应用中，一个完整的 JSON 主题文件可能包含数十个可视化对象的规范。不过，我想向你展示可视化对象的代码如何融入主题文件的整体结构，同时向你展示可以调整以定义特定可视化对象呈现方式的精确代码。当然，你可以获取文本框对象的代码并将其添加到任何现有的主题文件中，或者用此代码完整或部分地替换现有主题文件中的文本框定义。

## 代码结构解析

那么，你在这里看到了什么？

首先要注意的是，所有可视化对象都被定义为 `visualStyles` 对象的一部分。也就是说，关键字 `visualStyles` 后面跟着一个冒号和一对包含其他对象的花括号。

第二点是，所有可视化对象的定义都包含在第二个对象中——目前定义为一个星号（`*`）。这不过是一个为将来使用而保留的空对象。因此，实际的可视化对象定义嵌套在两个外部对象内部。

第三点是，可视化对象的定义本身也是一个对象——正如你所见，它包含在花括号内。以文本框可视化对象为例，引入该对象的关键字是 `textbox`。在前面的 JSON 代码中，此关键字以粗体突出显示。`textbox` 对象内部，为引入文本框中某个元素实际格式设置而嵌套的关键字以斜体突出显示，以引起你对结构定义方式的注意。

最后重要的一点是，特定可视化对象的 JSON 代码由 Power BI Desktop 格式窗格部分元素到你必须在主题文件中指定以格式化该部分元素所用的 JSON 关键字的映射关系组成。

## 文本框格式的关键字映射

表 5-1 描述了与 Power BI Desktop 格式窗格中每个部分对应的 JSON 关键字。

**表 5-1** 文本框格式设置元素

| 关键字 | 格式设置部分 |
| --- | --- |
| `General` | 常规 |
| `Title` | 标题 |
| `Background` | 背景 |
| `lockAspect` | 锁定纵横比 |
| `Border` | 边框 |
| `dropShadow` | 阴影 |
| `visualHeader` | 视觉对象标头 |

你可以看到，文本框实际上不包含任何文本框特有的元素。所有七个键/值对描述的都是大多数可视化对象中都会出现的元素。文本框的 JSON 代码仅描述这七个元素，原因在于文本框内的文本本质上是为你创建的每个文本框单独设置格式的。

为了开始详细解释可以格式化的项目，起点是格式窗格中的开/关开关。这是许多元素共有的，并且总是由以下 JSON 代码表示：

```json
"show": true
```

或者（用于关闭它）

```json
"show": false
```

其他元素必然更具体。我们依次来看一下。

描述标题部分的 `title` 关键字使用的关键字如表 5-2 所示。

**表 5-2** 标题的关键字映射

| 元素 | 关键字 |
| --- | --- |
| 标题 | `show` |
| 标题文本 | `text` |
| 自动换行 | `titleWrap` |
| 字体颜色 | `fontColor` |
| 背景色 | `background` |
| 对齐方式 | `alignment` |
| 字号 | `fontSize` |
| 字体 | `fontFamily` |

描述背景部分的 `background` 关键字使用的关键字如表 5-3 所示。

**表 5-3** 背景的关键字映射

| 元素 | 关键字 |
| --- | --- |
| 颜色 | `Color` |
| 透明度 | `transparency` |

描述锁定纵横比部分的 `lockAspect` 关键字使用的关键字如表 5-4 所示。

**表 5-4** 锁定纵横比的关键字映射

| 元素 | 关键字 |
| --- | --- |
| 锁定纵横比 | `show` |

描述边框部分的 `border` 关键字使用的关键字如表 5-5 所示。

**表 5-5** 边框的关键字映射

| 元素 | 关键字 |
| --- | --- |
| 边框 | `show` |
| 颜色 | `color` |

描述阴影部分的 `shadow` 关键字使用的关键字如表 5-6 所示。

**表 5-6** 阴影的关键字映射

| 元素 | 关键字 |
| --- | --- |
| 阴影 | `show` |
| 颜色 | `color` |
| 阴影位置 | `position` |
| 预设 | `Preset` |

描述视觉对象标头部分的 `visualHeader` 关键字使用的关键字如表 5-7 所示。

**表 5-7** 视觉对象标头的关键字映射

| 元素 | 关键字 |
| --- | --- |
| 背景 | `Background` |
| 边框 | `Border` |
| 图标颜色 | `Foreground` |

视觉对象信息图标：`showVisualInformationButton`
视觉对象警告图标：`showVisualWarningButton`
视觉对象错误图标：`showVisualErrorButton`
向下钻取下拉列表：`showDrillRoleSelector`
向下钻取图标：`showDrillToggleButton`
向上钻取图标：`showDrillUpButton`
显示下一级别图标：`showDrillDownLevelButton`
展开到下一级别图标：`showDrillDownExpandButton`
固定图标：`showPinButton`
焦点模式图标：`showFocusModeButton`
筛选器图标：`showFilterRestatementButton`
查看数据布局图标：`showSeeDataLayoutToggleButton`
更多选项图标：`showOptionsMenu`
视觉对象标头工具提示图标：`showTooltipButton`

当为某些属性指定一个对应于 Power BI Desktop 中弹出列表选项的元素时，主题文件需要你了解这一点。例如，用于视觉标题、列标题以及其他某些属性的标题对齐方式。在前面展示的 JSON 中，你可以看到以下用于标题的代码行：

```json
"alignment": "Center"
```

你可能已经猜到，这与在 Power BI Desktop 的格式设置窗格中选择表示“居中”的图标相对应。

当设置此类键/值对时，你只需要确切知道可以在 JSON 中使用什么值。在某些情况下，这些值与 Power BI Desktop 中使用的弹出列表中的值直接匹配。遗憾的是，情况并非总是如此。因此，在接下来的章节中，我将始终提供关键字映射。

在标题部分使用的`alignment`关键字需要表 5-8 中显示的属性之一。

**表 5-8** 标题对齐选项

| 值 | 格式设置弹出选项 |
| --- | --- |
| `Auto` | 自动 |
| `Left` | 左对齐 |
| `Center` | 居中 |
| `Right` | 右对齐 |

在投影部分使用的`position`关键字需要表 5-9 中显示的属性之一。

**表 5-9** 阴影位置选项

| 值 | 格式设置弹出选项 |
| --- | --- |
| `Outer` | 外部 |
| `Inner` | 内部 |

在投影部分使用的`preset`关键字需要表 5-10 中显示的属性之一。

**表 5-10** 阴影预设选项

| 值 | 格式设置弹出选项 |
| --- | --- |
| `BottomRight` | 右下 |
| `Bottom` | 底部 |
| `BottomLeft` | 左下 |
| `Center` | 居中 |
| `Left` | 左 |
| `TopRight` | 右上 |
| `Top` | 顶部 |
| `TopLeft` | 左上 |
| `Custom` | 自定义 |

由于在 JSON 中定义的许多详细值在多个视觉对象中以相同的方式使用，并且在同一个视觉对象内部也以相同的方式使用，因此我不会在讨论每个视觉对象时一遍又一遍地详尽解释每一个值。

> **注意**：在主题文件中输入与特定格式设置选项相对应的值时，使用 Power BI 所期望的、并且在参考表中给出的精确拼写和大小写是**非常重要的**。

## 图像

接下来我建议查看的视觉对象是图像。这是因为图像视觉对象除了你在文本框视觉对象中看到的标准元素之外，只包含一个额外元素。实际上，这个选项仅在 Power BI 中的另外少数几个元素中存在——即你可以应用的缩放。这是通过`imageScaling`关键字定义的。

图像格式（如下面的 JSON 片段所示）由`image`关键字引入。

表 5-11 描述了在 Power BI Desktop 中，就图像而言，与每个格式设置部分相对应的 JSON 关键字。

**表 5-11** 图像格式设置元素

| 关键字 | 格式设置部分 |
| --- | --- |
| `general` | 常规 |
| `imageScaling` | 缩放 |
| `title` | 标题 |
| `background` | 背景 |
| `lockAspect` | 锁定纵横比 |
| `border` | 边框 |
| `dropShadow` | 阴影 |
| `visualHeader` | 视觉对象标头 |

以下是你可以用来格式化图像的 JSON 代码。你可以在示例文件（`Attributes_Image.json`）中找到此文件。同样，新的或关键的关键字以斜体突出显示。请注意，从现在开始，我将仅展示正在讨论的视觉对象特有的 JSON。封装特定 JSON 代码的通用代码将不会显示——即使它在相应的示例文件中。

```json
"image":
{"*":
{
"general": [{... code not shown ...}]
"imageScaling": [{
"imageScalingType": "Fit"
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

在`imageScaling`部分中使用的 JSON 中的`imageScalingType`关键字需要表 5-12 中显示的属性之一。

**表 5-12** 缩放位置选项

| 值 | 格式设置弹出选项 |
| --- | --- |
| `Normal` | 正常 |
| `Fit` | 适应 |
| `Fill` | 填充 |

## 形状

接下来（并结束本章）要查看的视觉对象是形状视觉对象。指定形状格式的关键字是`basicShape`。此视觉对象的格式使用以下三个关键字定义：
*   `line`
*   `fill`
*   `rotation`

因此，你需要了解如何在主题文件中使用 JSON 配置这些格式设置元素。

表 5-13 描述了在 Power BI Desktop 中与每个格式设置部分相对应的 JSON 关键字。

**表 5-13** 形状格式设置元素

| 关键字 | 格式设置部分 |
| --- | --- |
| `general` | 常规 |
| `line` | 线条 |
| `fill` | 填充 |
| `rotation` | 旋转 |
| `title` | 标题 |
| `background` | 背景 |
| `lockAspect` | 锁定纵横比 |
| `border` | 边框 |
| `dropShadow` | 阴影 |
| `visualHeader` | 视觉对象标头 |

因此，对于形状元素，代码应类似于以下 JSON 片段（此代码可在示例文件`Attributes_Shape.json`中找到）：

```json
"basicShape":
{"*" :
{
"general": [{... code not shown ...}]
"line":   [{
"lineColor": {"solid": {"color": "#F5F4F0"}},
"transparency": 0,
"weight": 1,
"roundedge": 0
}],
"fill":   [{
"show": true,
"fillColor": {"solid": {"color": "#FFFFFF"}},
"transparency": 0
}],
"rotation":    [{
"angle": 0
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

描述线条部分的`line`关键字使用表 5-14 中所示的关键字。

**表 5-14** 线条元素的关键字映射

| 元素 | 关键字 |
| --- | --- |
| 线条颜色 | `lineColor` |

| 属性 | 关键字 |
| --- | --- |
| 透明度 | `transparency` |
| 粗细 | `weight` |
| 圆角 | `roundedge` |

描述填充部分的`fill`关键字使用表 5-15 中所示的关键字。

**表 5-15** 填充元素的关键字映射

| 元素 | 关键字 |
| --- | --- |
| 填充 | `show` |
| 填充颜色 | `fillColor` |
| 透明度 | `transparency` |

描述旋转部分的`rotation`关键字使用表 5-16 中所示的关键字。

**表 5-16** 旋转的关键字映射

| 元素 | 关键字 |
| --- | --- |
| 旋转 | `rotation` |

## 结论

本章介绍了如何使用 Power BI 中的 JSON 主题文件来准备和标准化视觉对象的深入格式设置。具体来说，你了解了 Power BI 中的三个关键视觉对象（文本框、形状和图像）以及如何对它们进行格式化。

每个视觉对象都由一个 JSON 关键字定义，该关键字又包含任何格式属性（对应于该视觉对象的 Power BI Desktop 格式设置窗格的各个部分）。

某些属性很简单，只需定义一个值；其他属性则是嵌套对象和/或数组。

然而，创建和修改 JSON 主题文件的技巧和科学始终在于：找到哪个 JSON 关键字映射到哪个 Power BI 格式属性，以及可以使用哪些值来定义此属性应如何作为标准被定义。

现在你已经了解了许多核心元素，是时候继续查看不同的视觉对象组了，从卡片和表格视觉对象类型开始。下一章将介绍这些内容。

