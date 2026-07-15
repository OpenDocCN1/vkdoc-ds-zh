# 1. DAX 机制

本章的构思非常简单。在过去的几年里，我们被问及许多关于如何计算度量和计算列的问题。同时，我们也无数次被问到这样一个问题：“为什么这样不行？”

当我们开始更深入地审视潜在的业务问题时，我们常常会想：“他们为什么会问这个问题？这太简单了。” 然后我们意识到，那些我们认为简单的事情，对其他人来说往往并不简单。

在 Power BI 论坛（`https://community.powerbi.com`）上回答与 DAX 相关的问题，为我赢得了诸如“你真是个传奇！”或“哇，你是个 DAX 忍者！”之类的感谢之词。

诚然，其中一些问题回答起来很有挑战性，但我绝对不是什么传奇，也不是忍者。我从未拥有过，也从未穿过一件能让我随心所欲消失或在我家乡的楼顶上空飞行的黑色睡衣。通过与客户和朋友的多次交谈，我知道外面有很多聪明人正面临着超出他们当前能力的 DAX 挑战，这让他们思考：“我要怎么做才能学会这种黑暗的艺术？”

如果你可能认为 DAX 是一种黑暗的艺术，并且阅读本书能帮你征服世界、学到一些咒语，那么你恐怕会在某种程度上感到失望。是的，你会读到一些能帮你征服世界的 DAX 代码；至少，它会帮你创建富有揭示性的 DAX 语句，从而帮助你发掘隐藏在数据中的全部潜力。但你肯定不会找到任何咒语或诅咒。

DAX 极大地帮助我们从数据中提取见解。有时这种提取相当简单，使用一个 DAX 函数就足够了。不幸的是，也有些时候这种提取必须强力进行，这意味着要使用多个 DAX 函数，并且创建一个“简单”度量的 DAX 代码会横跨多行，甚至多页。正是这些 DAX 语句可能导致人们产生 DAX 是一门艺术，或是一种由女巫、巫师或其他神秘人物使用的古老强大语言的印象，但请放心，它不是。

但如果 DAX 不是一种“施法”语言，为什么在像 Power BI 甚至 Stack Overflow 这样的论坛上还有那么多问题呢？

从我们的角度来看，大多数时候问题在于忽视了简单的东西，也就是我们所说的“活动部件”。人们常常会问，他们是否忽略了某个隐藏的“上下文转换”，或者是否需要更仔细地考虑“影子筛选器”。他们也在谈论掌握计算上下文，仿佛掌握了它就能获得一条黑带。

这里的问题是，有时确实存在隐藏的上下文转换，有时影子筛选器也确实在起作用。但大多数时候，他们忘记了掌握“五指死亡拳”意味着首先必须理解和掌握构成 DAX 基础的那些部分。这些基础部分构成了 DAX 展现其“魔力”的根基。

## 为什么是 DAX 机制

如前所述，使用 DAX 解决分析问题并非少数入门者所掌握的秘密科学。相反，我们将其视为一门手艺。这是因为我们认为，任何愿意投入时间并且不惧怕一些挫折的人，都能够掌握这门手艺。我们认为，将编写 DAX 语句与制作陶器相比较是合理的。如你所知，成功完成学徒期可能需要一些时间，甚至可能要用一生才能成为一门手艺的大师（想到一些日本陶艺）。有时候，一个复杂 DAX 语句的精密运作，可能更像是瑞士制造、计量宝贵时间的杰作，而不是一个帮助我们保持清醒、思考我们的 DAX 语句如何将活动部件连接起来的简单咖啡杯。

但尽管如此，需要完美协同工作的部件数量并不多，而支配这些部件（即活动部件）的法则或原理，并不像支配行星运动的规则那样复杂或困难。因此，我们认为努力为编写 DAX 祛魅，并将其与一门可以掌握的技艺进行比较，是合理的。

## 活动部件

在我们开始编写 DAX 之前，我们需要一个能够执行 DAX 语句的环境，并且该环境还要提供一个我们可以编写 DAX 语句的界面。就本书而言，这个环境是通过使用 Power BI Desktop 来搭建的，该软件可以从 `www.powerbi.com` 下载。

Power BI Desktop 附带一个数据库（用于存储数据并能理解/执行 DAX 查询），同时也提供了编写这些 DAX 语句的界面。除此之外，Power BI Desktop 中最显而易见的方面或许是多种将数据库中存储的数据进行可视化的方法。在本书中，我们会提到 DAX 语句或 DAX 查询，大多数时候两者并无区别。但细微的差别在于：

- DAX 语句

术语 `DAX 语句` 在我们指代用于定义度量或计算列的一段 DAX 代码时使用。

- DAX 查询

术语 `DAX 查询` 在我们指代由 Power BI Desktop “自动”创建/组合，用于从数据库中检索数据以“填充”可视化元素（无论是卡片图、表格图还是簇状柱形图）的查询时使用。

### 数据库

关于数据库，有很多可说，它帮助我们找到组织中各个部门关键问题的答案，无论这些组织是大型企业还是小型公司。

这个数据库是 Analysis Services 表格模型，其引擎官方称为“xVelocity 内存分析引擎”。该引擎提供两种访问数据的模式：

- **本地数据** – 数据以内存列式数据存储形式保存在数据库中；此模式通常被称为 `VertiPaq`。

- **远程数据** – 从数据源查询数据；此模式通常被称为 `DirectQuery`。

为简单起见，此处统称为 `VertiPaq` 或更简单地称为 Analysis Services 表格模型；该名称源于“商业智能语义模型表格模型”，该名称随 SQL Server 2012 的发布而引入，用于区分自那时起随 SQL Server 提供的两种分析引擎。

`VertiPaq` 为微软商业智能产品中的以下产品提供能力：

- MSFT SQL Server Analysis Services (`SSAS`；本地部署，自 SQL Server 2012 起)

- Azure Analysis Services

- Power BI Desktop

- Power BI Service

- Power Pivot（与 MSFT Excel 2010 至 MSFT Excel 2016 结合使用）

如未特别说明，我们始终指代随 Power BI Desktop 提供的版本。本书不旨在涵盖 `VertiPaq` 引擎的所有技术细节。这本身就需要另一本书来阐述，但有两点必须提及：

- 数据以列式结构存储。

- 数据保存在内存中。

数据的列式和内存存储方式使 `VertiPaq` 引擎有别于 SQL Server 关系数据库和 SQL Server Analysis Services 多维数据库（`MD`）。有人可能认为内存存储限制了可存储和分析的数据集大小。但与关系数据库引擎基于行的数据存储相比，可以将数据压缩数个数量级。

尽管如此，我们将重点关注对您（Power BI 用户）来说更显而易见的对象。这些对象是：

- 表

- 这些表之间的关系

- 度量值和计算列

我们使用以下示意图（图 1-1）来解释特定 DAX 语句的工作原理。

![../images/471413_1_En_1_Chapter/471413_1_En_1_Fig1_HTML.jpg](img/471413_1_En_1_Fig1_HTML.jpg)

**图 1-1** 示意图表

上表有五列：

- **两列文本列（T）** – 这些列用于描述数据，例如客户名称或产品名称。

- **一列数值列（#）** – 此列不应被聚合。这也适用于数据类型为 `datetime` 和 `date` 的列。这些列通常代表源系统中的键值，如订单编号。

- **两列数值列（Σ(#)）** – 这些列代表数值数据类型的列，如整数或小数值。这些列将被聚合，并且最常用于度量值中。

**关系**对数据分析至关重要，并对高性能的 DAX 语句起着关键作用，将在第 2 章“数据建模”中详细阐述。基本上，它们将表格数据模型中的表关联起来。

**度量值**可能是 xVelocity 引擎中最强大的功能。这仅仅是因为每当表中的数据不足以提取我们所需的见解时，我们就使用 DAX 来创建计算。

### 定义

度量值返回一个标量值；这意味着一个单一的值。此标量值基于表（在应用所有筛选器后剩余的行）进行计算。因此，可以稳妥地说：度量值是通过聚合筛选后的行来计算的。

如果您觉得这个定义有些奇怪，并且您想到了像 `SUMX` 这样的迭代函数——其表达式允许引用迭代中当前行的单个值——请记住，最终值是被聚合的。

#### 注意

度量值不能在切片器中使用，也不能在 Power BI 报表的筛选器窗格中作为报表级筛选器或页面级筛选器使用。在此，它们仅能用作视觉对象级筛选器。

**计算列**为表增加了额外的分析能力。使用 Power BI Desktop 创建数据模型时，始终需要回答一个问题：是使用 Power Query 还是 DAX 来创建附加列。有时使用 DAX 创建列似乎更简单，但使用 DAX 创建列是需要付出代价的。

- 使用 DAX 创建的计算列会延长处理模型所需的时间。

- 使用 DAX 创建的计算列的压缩效果不如使用 Power Query 创建的列。

上述内容并非普遍建议不要使用 DAX 向数据模型添加列。在某些情况下，使用 DAX 添加列可能会减少数据刷新和模型处理的总时间，直到模型准备好用于分析。

# Power BI中的DAX

必须意识到这样一个事实：每一列都会增加数据模型的内存占用。因此，您可能应考虑在未来尽可能创建度量值。

## 注意

计算列可以在 Power BI 报表中用于切片器、作为报表级筛选器以及页面级筛选器，并构成 Power BI 中视觉对象分类轴的内容。

## Power BI Desktop

Analysis Services 数据库最强大的功能之一是可以向数据模型添加计算列和度量值。除了这个数据库引擎接口之外，Power BI 还搭建了舞台，让我们的 `DAX` 语句大放异彩，那就是视觉对象。这些视觉对象有其独特之处。它们像矩阵视觉对象一样提供行和列标题，或者为分类值（除数值数据类型字段外的所有内容）提供 x 轴。有时，显示我们想要可视化的数据会变得和创建度量值本身一样困难。第 8 章“使用 DAX 解决高级报表需求”将数据建模技术与 `DAX` 语句相结合，以创建满足以下要求的视觉对象：

- 在簇状柱形图中显示最近 N 个月，并且用户必须能够选择某个特定的月份作为锚点
- 在 Top N 客户柱形图旁边，显示一个额外的列，代表所有其他客户的值

目前，只需始终记住我们使用 `DAX` 创建的对象（即`计算列`和`度量值`）的“交互层级”就足够了。其含义如图 1-2 所示。

![../images/471413_1_En_1_Chapter/471413_1_En_1_Fig2_HTML.jpg](img/471413_1_En_1_Fig2_HTML.jpg)

**图 1-2** `DAX` 如何与表格数据模型交互

从图 1-2 中可以了解到以下内容：

## 规则

- 在表格数据模型中使用 `DAX` 创建的对象无法从 Power Query 中使用。
- 计算列在执行查询时不会重新计算。如果 `DAX` 语句在初始提交时以及数据刷新期间，其计算结果将持久保存在底层表中。
- 在度量值定义之外创建的计算表将在数据模型内部“创建”，这意味着 `DAX` 语句在初始创建时、定义更改时以及（当然）数据刷新期间执行。

# DAX：初识

本书将使用 Git 上提供的"Wide World Importers"（WWI）数据模型来演示 DAX 语句，该模型并非过于简单。本章所使用的数据集则更为简单。我认为这对于更好地理解实际发生的情况是必要的。

## 隐式筛选器

在创建第一个 DAX 语句之前，有必要先查看本章所用简单数据模型的基础数据。图 1-3 显示了`simple table values`表的内容。

![../images/471413_1_En_1_Chapter/471413_1_En_1_Fig3_HTML.jpg](img/471413_1_En_1_Fig3_HTML.jpg)

图 1-3
表 – simple table values

如果将此表用于具有以下设置的矩阵视觉对象：

* `Brand` 列作为行
* `Color` 列作为列
* `Amount` 列作为值

你将得到图 1-4 所示的结果（圆形标记除外）。

![../images/471413_1_En_1_Chapter/471413_1_En_1_Fig4_HTML.jpg](img/471413_1_En_1_Fig4_HTML.jpg)

图 1-4
`simple table values` 矩阵

在 Phil 的著作《Beginning DAX with Power BI》中，他解释了 Power BI 会将行标题`Brand`和列标题`Color`中的值添加到矩阵视觉对象值字段中所用度量的计算上下文中。

#### 注意

源自列标题、行标题或切片器选择的筛选器被称为隐式筛选器。这同样适用于簇状柱形图 x 轴上使用的值（此逻辑可推广到所有其他视觉对象）。

我猜你对`B3`/`red`交叉点显示的值并不感到惊讶，即使我们尚未定义任何度量。只需在数据视图中筛选该表即可轻松验证结果，见图 1-5。

![../images/471413_1_En_1_Chapter/471413_1_En_1_Fig5_HTML.jpg](img/471413_1_En_1_Fig5_HTML.jpg)

图 1-5
`simple table values` – 筛选后的`B3`/`red`

很容易验证`Amount`列中值的总和等于 26。我们可以推断出以下几点：

* 视觉对象会对包含用作值的列（此处为`Amount`列）的表进行筛选。
* 在应用筛选器后，会使用聚合函数从剩余的两行中计算出值 26。

很明显，聚合函数是`SUM`。`SUM`是数值列用作值时的默认聚合函数。默认函数可以针对每列进行更改，并且至少应进行检查。如有必要，可以在"建模"菜单的"属性"功能区中更改`默认汇总方式`的值。可以从"报表"或"数据"视图中访问该函数；必须选中已检查的列。

图 1-6 展示了如何更改所选字段`Amount`的默认汇总方式。

![../images/471413_1_En_1_Chapter/471413_1_En_1_Fig6_HTML.jpg](img/471413_1_En_1_Fig6_HTML.jpg)

图 1-6
列默认聚合函数

### 说明

如果更改了列的默认汇总方式，则必须先从视觉对象中移除该列，然后再重新添加。

我不得不承认，在很长一段时间里，我一直认为矩阵视觉对象中的每个单元格（包括行和列上的总计值）都会创建一个单独的 DAX 查询，因为每个单元格的筛选上下文都会发生变化。幸运的是，情况并非如此。其幕后真正发生的事情可以通过 `DAX Studio` 来控制，这是一个主要由 `sqlbi.com` 和 `Darren Gosbell` 的成员开发的开源工具。`DAX Studio` 是创建 DAX 语句（其格式化功能更智能）以及在你对 DAX 语句的性能不满意时必不可少的工具。因此，第 12 章“DAX Studio”中描述了 `DAX Studio` 的一些功能——至少是优化性能低下的 DAX 语句所必需的功能。

为了发现幕后发生了什么，`DAX Studio` 能够捕获由 Power BI Desktop 创建的 DAX 查询，以检索数据来填充视觉对象。

当包含简单矩阵的报表页被激活时，会生成以下 DAX 代码（见清单 1-1）。

```
DEFINE
VAR __DS0Core =
SUMMARIZECOLUMNS(
ROLLUPADDISSUBTOTAL('simple table values'[Brand], "IsGrandTotalRowTotal"),
ROLLUPADDISSUBTOTAL('simple table values'[Color], "IsGrandTotalColumnTotal"),
"SumAmount", CALCULATE(SUM('simple table values'[Amount]))
)
VAR __DS0Primary =
TOPN(
102,
SUMMARIZE(__DS0Core, 'simple table values'[Brand], [IsGrandTotalRowTotal]),
[IsGrandTotalRowTotal],
0,
'simple table values'[Brand],

)
VAR __DS0Secondary =
TOPN(
102,
SUMMARIZE(__DS0Core, 'simple table values'[Color], [IsGrandTotalColumnTotal]),
[IsGrandTotalColumnTotal],
1,
'simple table values'[Color],

)
EVALUATE
__DS0Secondary
ORDER BY
[IsGrandTotalColumnTotal], 'simple table values'[Color]
EVALUATE
NATURALLEFTOUTERJOIN(
__DS0Primary,
SUBSTITUTEWITHINDEX(
__DS0Core,
"ColumnIndex",
__DS0Secondary,
[IsGrandTotalColumnTotal],
ASC,
'simple table values'[Color],
ASC
)
)
ORDER BY
[IsGrandTotalRowTotal] DESC, 'simple table values'[Brand], [ColumnIndex]
```

清单 1-1
简单矩阵

我们将在第 12 章“DAX Studio”中更详细地探讨这类 DAX 查询。但现在，我只想让你关注 DAX 查询的第一部分。在这里你会看到以下代码片段，它是 DAX 函数 `SUMMARIZECOLUMNS` 的一部分。

```
"SumAmount", CALCULATE(SUM('simple table values'[Amount]))
```

清单 1-2
隐式度量定义

如果我们自己编写度量，它看起来几乎一样。因此，我们在 *simple table values* 表中使用如下 DAX 语句创建一个度量，但没有使用 `CALCULATE` 函数（见清单 1-3）。

```
Simple SUM Amount =
SUM('simple table values'[Amount])
```

清单 1-3
度量 – Simple SUM Amount

如果你想要检查结果，你会发现我们检索到了与第一个查询相同的值，但这正是我们所期望的。

但我们真正关心的是第一个度量出现在 Power BI 创建的查询中。下面的清单显示了该查询的第一部分。我们可以看到，“自然”列被当作显式定义的度量来处理：

```
VAR __DS0Core =
SUMMARIZECOLUMNS(
ROLLUPADDISSUBTOTAL('simple table values'[Brand], "IsGrandTotalRowTotal"),
ROLLUPADDISSUBTOTAL('simple table values'[Color], "IsGrandTotalColumnTotal"),
"SumAmount", CALCULATE(SUM('simple table values'[Amount])),
"Simple_SUM_Amount", 'simple table values'[Simple SUM Amount]
)
```

到目前为止，你需要相信我，这两种定义是相同的，即使看起来缺少了包裹我们度量的 `CALCULATE` 函数，除非你已经读过 Phil 的书或其他一些现有的优秀 DAX 书籍。但我们也可以通过移除 `CALCULATE` 并在 `DAX Studio` 中执行查询来证明这两个度量产生完全相同的结果。

在解释什么是显式筛选器之前，我只想稍微修改一下矩阵视觉对象。你可以在报表页“第 1 章 – 隐式筛选器 – b”中找到这个矩阵。除了将应用于查询执行的隐式筛选器之外，我还添加了列 *Brand* 作为页面级筛选器（只是为了避免与其他报表页产生不必要的干扰）。筛选器设置请见图 1-7。

![../images/471413_1_En_1_Chapter/471413_1_En_1_Fig7_HTML.jpg](img/471413_1_En_1_Fig7_HTML.jpg)
图 1-7 页面级筛选器 `Brand` 不等于 `B4`

这是一项重要任务，因为对于报表级筛选器、页面级筛选器以及视觉对象级筛选器的行为存在一些误解。配置如下：移除列 `Brand` 的值等于 `B4` 的行。清单 1-4 显示了 Power BI Desktop 创建的查询（至少是重要部分）。

```
VAR __DS0FilterTable =
FILTER(
KEEPFILTERS(VALUES('simple table values'[Brand])),
'simple table values'[Brand]  "B4"
)
VAR __DS0Core =
SUMMARIZECOLUMNS(
ROLLUPADDISSUBTOTAL('simple table values'[Brand], "IsGrandTotalRowTotal"),
ROLLUPADDISSUBTOTAL('simple table values'[Color], "IsGrandTotalColumnTotal"),
__DS0FilterTable,
"SumAmount", CALCULATE(SUM('simple table values'[Amount]))
)
```
清单 1-4 `FilterTable`

这里需要注意的重要一点是创建了一个名为 `__DS0FilterTable` 的变量，该变量将在查询的所有后续部分中使用。

为了更有趣，我将使用第三个报表页“第 1 章 – 隐式筛选器 – c”。这个报表页也有相同的页面级筛选器，但此外还有一个同样使用列 `Brand` 的切片器。在这里，我将选择品牌 `B1` 和 `B2`。图 1-8 显示了报表的外观。

![../images/471413_1_En_1_Chapter/471413_1_En_1_Fig8_HTML.jpg](img/471413_1_En_1_Fig8_HTML.jpg)
图 1-8 页面级筛选器和切片器

清单 1-5 显示了查询的重要部分。

```
VAR __DS0FilterTable =
FILTER(
KEEPFILTERS(VALUES('simple table values'[Brand])),
AND(
'simple table values'[Brand] IN {"B2",
"B1"},
'simple table values'[Brand]  "B4"
)
)
VAR __DS0Core =
SUMMARIZECOLUMNS(
ROLLUPADDISSUBTOTAL('simple table values'[Brand], "IsGrandTotalRowTotal"),
ROLLUPADDISSUBTOTAL('simple table values'[Color], "IsGrandTotalColumnTotal"),
__DS0FilterTable,
"SumAmount", CALCULATE(SUM('simple table values'[Amount]))
)
```
清单 1-5 `FilterTable` 页面级筛选器和切片器

你可能想知道为什么这让我如此兴奋，或者为什么我觉得变量 `__DS0FilterTable` 这么有趣。但答案其实很简单。

不可能创建一个能够显示这三个品牌 Amount 之和的度量。必要的是移除切片器隐式添加的筛选器，但保留来自页面级筛选器的筛选器。要创建这样的度量，就需要在数据模型中预先采取一些预防措施。

### 规则

无法移除来自切片器的筛选器，同时保留来自报表级筛选器或页面级筛选器的筛选器。

### 显式筛选器

每当我们需要编写度量值时，都必须应对计算上下文的挑战。计算上下文描述了度量值（以及计算列）被计算时所处的上下文。

决定此上下文有两个组成部分：

*   筛选上下文

*   行上下文

筛选上下文可以非常直观地展现；只需创建一个矩阵视觉对象，你就能欣赏到筛选上下文的全部魅力。行标题和列标题会被添加到 `FilterTable` 中，代表该单元格上下文中 DAX 公式计算时的筛选上下文。而行上下文有时则不那么明显。即使是创建一个简单的计算列，也可能变得很棘手。每当 `CALCULATE` 使用多个参数时，我们都需要通过提供显式筛选器来改变筛选上下文。

这一点非常重要，因此我们专门用两个章节来讨论它：第 4 章“这个奇怪的上下文”和第 5 章“DAX 中的筛选”。

