# 第 4 章 文本函数

## `Text.EndsWith`

要检查文本的结尾字符，可按如下方式使用 `Text.EndsWith`：

`Text.EndsWith(
    text as nullable text,
    substring as text,
    optional comparer as nullable function
) as nullable logical`

通过使用此函数，可以检查一个文本是否以特定字符结尾。该函数接收以下三个输入，其输出始终等于 `True` 或 `False`。

1.  参考文本。
2.  希望与参考文本的结尾字符进行比较的指定字符。
3.  此输入为可选，用于确定比较逻辑，其不同情况包括：
    -   `Comparer.Equals`
    -   `Comparer.FromCulture`
    -   `Comparer.Ordinal`
    -   `Comparer.OrdinalIgnoreCase`

基于上述说明，表 4-19 提供了使用此函数的不同示例。

**表 4-19.** `Text.EndsWith` 示例

| 公式 | 结果 |
|---------|--------|
| `= Text.EndsWith("XN18-M13","M13")` | `true` |
| `= Text.EndsWith("XN18-M13","13")` | `true` |
| `= Text.EndsWith("XN18-M13","m13")` | `False` |
| `= Text.EndsWith("XN18-M13","m13", Comparer.OrdinalIgnoreCase)` | `true` |

## `Text.Contains`

要检查参考文本中是否存在子字符串，可按如下方式使用 `Text.Contains`：

`Text.Contains(
    text as nullable text,
    substring as text,
    optional comparer as nullable function
) as nullable logical`

该函数接收以下三个输入：

1.  第一个输入是参考文本。
2.  第二个输入输入要在参考文本中搜索的子字符串。
3.  此输入为可选，用于确定比较逻辑。如果未输入此参数，则该函数区分大小写。输入表达式 `Comparer.OrdinalIgnoreCase` 表示该函数不再区分大小写。此输入的不同情况包括：
    -   `Comparer.Equals`
    -   `Comparer.FromCulture`
    -   `Comparer.Ordinal`
    -   `Comparer.OrdinalIgnoreCase`

根据函数说明，表 4-20 给出了该函数针对不同示例的结果。

**表 4-20.** `Text.Contains` 示例

| 公式 | 结果 |
|---------|--------|
| `= Text.Contains("XN18-M13","M")` | `true` |
| `= Text.Contains("XN18-M13","8-M")` | `true` |
| `= Text.Contains("XN18-M13","m")` | `False` |
| `= Text.Contains("XN18-M13","M", Comparer.OrdinalIgnoreCase)` | `true` |

## `Text.PositionOf`

要获取子字符串在参考文本中的位置，可按如下方式使用 `Text.PositionOf`：

`Text.PositionOf(
    text as text,
    substring as text,
    optional occurrence as nullable number,
    optional comparer as nullable function
) as any`

该函数具有以下四个输入：

1.  第一个输入是参考文本。
2.  第二个输入是要在参考文本中搜索的子字符串。
3.  如果第二个输入的子字符串在参考文本中出现多次，则在此输入中输入数字 0、1 或 2，或使用表达式 `Occurrence.First`、`Occurrence.Last` 或 `Occurrence.All` 来确定所需的位置。此输入为可选，其默认值为 0。
4.  在此输入中确定子字符串的比较逻辑。此输入为可选，其默认状态为区分大小写，但在此输入中输入 `Comparer.OrdinalIgnoreCase` 表示不区分大小写。此输入的不同状态包括：
    -   `Comparer.Equals`
    -   `Comparer.FromCulture`
    -   `Comparer.Ordinal`
    -   `Comparer.OrdinalIgnoreCase`

表 4-21 提供了此函数的多个示例。

**表 4-21.** `Text.PositionOf` 示例

| 公式 | 结果 |
|---------|--------|
| `= Text.PositionOf("XNW-11-181-M","-")` | `3` |
| `= Text.PositionOf("XNW-11-181-M","-",1)` | `10` |
| `= Text.PositionOf("XNW-11-181-M","-", Occurrence.Last)` | `10` |
| `= Text.PositionOf("XNW-11-181-M","-",2)` | `{3,6,10}` |
| `= Text.PositionOf("XNW-11-181-M","R")` | `-1` |

## `Text.PositionOfAny`

`Text.PositionOfAny` 类似于 `Text.PositionOf`。区别在于，使用 `Text.PositionOfAny` 可以同时在参考文本中搜索多个字符串，如下所示：

`Text.PositionOfAny(
    text as text,
    characters as list,
    optional occurrence as nullable number
) as any`

该函数接收以下三个输入：

1.  参考文本。
2.  第二个输入采用列表形式，包含要在参考文本中查找其位置的子字符串。
3.  此输入为可选。通过输入数字 0、1 或 2，可以指定输出中应显示最小数字（文本开头处的首次出现）、最大数字（文本结尾处的最后一次出现）还是所有数字。

表 4-22 提供了此函数的示例。

**表 4-22.** `Text.PositionOfAny` 示例

| 公式 | 结果 |
|---------|--------|
| `= Text.PositionOfAny("XNW-11-181-M",{"-"})` | `3` |
| `= Text.PositionOfAny("XNW-11-181-M",{"1"})` | `4` |
| `= Text.PositionOfAny("XNW-11-181-M",{"-","1"})` | `3` |
| `= Text.PositionOfAny("XNW-11-181-M",{"-","1"},1)` | `10` |
| `= Text.PositionOfAny("XNW-11-181-M",{"-","1"},2)` | `{3,4,5,6,7,9,10}` |

### 文本转换

在 Power Query 中，值的格式很重要，每个函数都适用于特定范围的格式。例如，在 `Text.Start` 的第一个输入中，必须输入文本，输入数字会导致错误。因此，要分离数字 `20231123` 中的前四个字符（年份），首先应将其转换为文本，然后才能对其执行所需操作。

Power Query 中有多种用于值格式转换的函数，如表 4-23 所述。

**表 4-23.** 文本转换函数

| 函数 | 描述 |
|----------|-------------|
| `Character.FromNumber` | 此函数接收一个数字（ASCII 码）作为输入，并在输出中显示对应的字符。 |
| `Character.ToNumber` | 此函数接收一个字符作为输入，并在输出中显示对应的 ASCII 码。 |
| `Text.From` | 此函数接收一个数字作为输入，并在输出中以文本格式显示相同的数字。 |
| `Text.ToList` | 此函数接收一个文本作为输入，分离该数字的每个字符，并将结果显示为列表中的列表。 |
| `Value.FromText` | 此函数接收一个文本格式的数字作为输入，并在输出中以数字格式显示相同的数字。 |

表 4-24 显示了这些函数的结果。

**表 4-24.** 文本转换函数示例

| 公式 | 结果 |
|---------|--------|
| `Character.FromNumber(65)` | `"A"` |
| `Character.ToNumber("A")` | `65` |
| `Text.From(83)` | `"83"` |
| `Text.ToList("Hello")` | `{"H","e","l","l","o"}` |
| `Value.FromText("83")` | `83` |

### 总结

在本章中，您踏上了 Power Query 文本函数世界的旅程，揭开了一个工具包的面纱，它使数据专业人员能够攻克非结构化和半结构化文本数据的复杂性。从客户反馈到产品描述，文本信息是我们数字生活中不可或缺的一部分，掌握其处理技巧对于数据转换至关重要。

您首先了解了文本编码、处理的基础知识以及 Power Query 文本函数在这些过程中的作用。您探索了从字符串中高效提取特定元素的技巧，这项技能使您能够从段落中分离 URL 或从冗长的叙述中捕获日期。

接着，您深入研究了清洗和标准化数据的技术，包括删除多余空格、标点符号以及转换大小写格式。这些操作构成了一致且可靠的文本分析的基础。



