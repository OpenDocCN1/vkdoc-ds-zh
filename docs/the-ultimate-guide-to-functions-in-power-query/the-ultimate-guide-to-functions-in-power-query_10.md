# 第四章 文本函数

在数据转换和处理的领域中，文本数据是一个基础构建块。无论是处理客户名称、产品描述、日志条目还是任何其他文本内容，提取、清理和转换文本的能力变得至关重要。这正是 Power Query 的强大之处。在本章中，您将深入探讨如何在 Power Query 中运用文本函数的艺术，揭示一套能够赋能数据专业人员攻克非结构化与半结构化文本数据复杂性的工具集。

您在本章中的旅程将从基础知识开始，例如理解文本编码的细微差别、文本操作，以及 Power Query 文本函数在这些过程中的作用。您将探索如何从字符串中高效提取特定元素，例如从段落中分离出 URL，或从长篇叙述中提取日期。

## 文本函数

随着深入探索，你将发现文本函数在清洗和标准化数据方面的潜力。从删除多余空格和标点符号，到转换大小写格式，这些操作是进行一致且可靠的文本分析的基础。

这些函数分为以下五组：

- 提取文本的一部分
- 删除、替换和组合文本
- 文本修正
- 在参考文本中搜索字符
- 文本转换

© Omid Motamedisedeh 2024

O. Motamedisedeh, *The Ultimate Guide to Functions in Power Query*,

[`doi.org/10.1007/978-1-4842-9754-4_4`](https://doi.org/10.1007/978-1-4842-9754-4_4#DOI)

# 第 4 章 文本函数

## 提取文本的一部分

在本节中，将解释 Power Query 中与从文本中提取字符相关的函数。这些函数用于根据字符在文本开头、中间或末尾的位置来提取值。

### `Text.At`

`Text.At` 用于根据位置提取文本中的单个字符，语法如下：

```
Text.At(
   text as nullable text,
   index as number
) as nullable text
```

此函数接收以下两个参数，其结果始终是参考文本中的一个字符：

1. 第一个参数输入参考文本。
2. 第二个输入（数字）确定要从参考文本中提取的字符位置（编号从 0 开始）。

表 4-1 展示了此函数的不同用法。

**表 4-1.** `Text.At` 的示例

| **公式** | **结果** |
| --- | --- |
| `= Text.At("XNW-11-1",0)` | `"x"` |
| `= Text.At("XNW-11-1",1)` | `"n"` |
| `= Text.At("XNW-11-1",3)` | `"-"` |

### `Text.Start`

`Text.Start` 用于从文本开头提取指定数量的字符，语法如下：

```
Text.Start(
   text as nullable text,
   count as number
) as nullable text
```

此函数包含以下两个参数：

1. 参考文本（此函数的第一个输入必须为文本格式；否则，函数结果等于错误）。
2. 要从文本开头提取的字符数，在第二个输入中定义。

在第二个输入中键入数字 2，将提取参考文本的前两个字符。为进一步考察此函数，表 4-2 提供了多个示例。

**表 4-2.** `Text.Start` 的示例

| **公式** | **结果** |
| --- | --- |
| `=Text.Start("Xn1281",2)` | `"xn"` |
| `=Text.Start("Xn1281",3)` | `"xn1"` |
| `=Text.Start("Xn1281",1)` | `"x"` |
| `=Text.Start(121,2)` | `error` |

最后一个公式的结果是 `Error`，因为第一个输入是数字，但应输入文本值。因此，在这种情况下，在使用 `Text.Start` 之前，需要使用其他函数（如 `Text.From`）将第一个输入转换为文本。所以，最后一个公式的修正版本如下：

```
=Text.Start(Text.From(121),2)
```

在这种情况下，函数的结果为 `"12"`。

### `Text.End`

此函数的定义类似于 `Text.Start`，但该函数用于从参考文本的末尾提取指定数量的字符。

```
Text.End(
   text as nullable text,
   count as number
) as nullable text
```

此函数有以下两个输入：

1. 第一个输入指定要提取末尾字符的参考文本。
2. 第二个输入确定要提取的字符数。

表 4-3 展示了此函数的几种应用。

**表 4-3.** `Text.End` 的示例

| **公式** | **结果** |
| --- | --- |
| `=Text.End("Xn1281",2)` | `"81"` |
| `=Text.End("Xn1281",3)` | `"281"` |
| `=Text.End("Xn1281",1)` | `"1"` |

此函数的输出始终是文本，不可直接求和，除非在公式中使用前将结果转换为数字。

### `Text.Range`

另一个用于提取文本中字符的函数是 `Text.Range`。它用于提取文本中间的字符，语法如下：

```
Text.Range(
   text as nullable text,
   offset as number,
   optional count as nullable number
) as nullable text
```

该函数接受三个参数：

1. 第一个参数定义参考文本。
2. 第二个参数确定提取的起始点（从你想提取字符的哪个字符位置开始）。
3. 要提取的字符数在第三个参数中指定。

在此函数中，在第一个输入中输入值 `"Xn192"`，在第二个输入中输入数字 `2`，在第三个输入中输入数字 `1`，意味着从第三个字符开始分离（字符位置从 0 开始计数），并且只提取一个字符。因此，结果是 `"1"`。

表 4-4 展示了该函数的不同示例。

**表 4-4.** `Text.Range` 的示例

| **公式** | **结果** |
| --- | --- |
| `=Text.Range("Xn1281",2,1)` | `"1"` |
| `=Text.Range("Xn1281",0,3)` | `"xn1"` |
| `=Text.Range("Xn1281",1,2)` | `"n1"` |
| `=Text.Range("Xn1281",3,12)` | 错误 |

根据表 4-4 中的最后一个示例，`Text.Range("Xn1281",3,12)` 的结果是一个错误。在此公式中，你正在参考文本的第三个字符之后查找 12 个字符，但第三个字符之后只有三个字符 `"381"`，所以该函数的结果是错误。在这种情况下，可以使用 `Text.Middle`。该函数的参数与 `Text.Range` 相同，但区别在于 `Text.Middle("Xn1281",3,12)` 的结果等于 `"281"`。

在 `Text.Middle` 中，第三个参数是可选的。如果你不为该参数输入值，分离将持续到参考文本的末尾。

### `Text.AfterDelimiter`

`Text.AfterDelimiter` 用于提取特定分隔符之后的文本部分，如下所示：

```
Text.AfterDelimiter(
   text as nullable text,
   delimiter as text,
   optional index as any
) as any
```

该函数接受三个参数：

1. 第一个参数是参考文本。
2. 用于确定分离起始点的分隔符在第二个参数中输入。它作为文本值输入。
3. 如果分隔符在参考文本内重复出现多次，则使用第三个输入来指定应考虑哪一个重复出现来进行分离（计数从 0 开始）。此输入是可选的，如果未输入，则默认使用数字 `0`。

此函数的输出总是参考文本中出现在分隔符之后的部分。为了阐明此函数，表 4-5 提供了各种示例。

**表 4-5.** `Text.AfterDelimiter` 的示例

| **公式** | **结果** |
| --- | --- |
| `= Text.AfterDelimiter("XNW-11-181-M","-")` | `"11-181-M"` |
| `= Text.AfterDelimiter("XNW-11-181-M","-",0)` | `"11-181-M"` |
| `= Text.AfterDelimiter("XNW-11-181-M","-",1)` | `"181-M"` |
| `= Text.AfterDelimiter("XNW-11-181-M","-",2)` | `"M"` |
| `= Text.AfterDelimiter("FMn12N16","N")` | `"16"` |
| `= Text.AfterDelimiter("FMn12N16","n")` | `"12n16"` |
| `= Text.AfterDelimiter("XNW-11-181-M","u",2)` | 错误 |

对于表 4-5 中的最后一个示例，如果参考文本不包含该分隔符，结果将为空。

在第三个输入中，除了输入一个数字来确定参考文本中所需分隔符的重复出现次数外，还可以通过在一个列表中随数字一起输入 `RelativePosition.FromStart` 或 `RelativePosition.FromEnd` 来指定在参考文本中搜索分隔符的方向。

例如，以下公式的结果是第一个 `"-"` 之后的字符，等于 `"11-181-M"`：

```
= Text.AfterDelimiter(
"XNW-11-181-M",
"-",
0)
```

在另一个示例中，以下公式的结果相同，但它强调了是从文本的开头（从左到右）搜索第一个 `"-"`：

```
= Text.AfterDelimiter(
"XNW-11-181-M",
"-",
{0,RelativePosition.FromStart})
```

但如果使用以下公式，结果则不同。在这种情况下，你要查找的是第一个破折号之后的文本，但它会强调从文本末尾（从右到左）搜索第一个 `-`。因此，该函数的结果等于 `M`：

```
= Text.AfterDelimiter(
"XNW-11-181-M",
"-",
{0,RelativePosition.FromEnd})
```

## 第 4 章 文本函数

### `Text.BeforeDelimiter`

`Text.BeforeDelimiter` 的定义与 `Text.AfterDelimiter` 类似，用于提取参考文本中分隔符之前的字符。该函数在 Power Query 中的定义如下：

```
Text.BeforeDelimiter(
   text as nullable text,
   delimiter as text,
   optional index as any
) as any
```

该函数接收三个参数，如下所示：

1.  第一个参数输入参考文本。

2.  第二个输入中，分隔符以文本值的形式输入。

3.  如果分隔符在文本中重复出现多次，则使用第三个输入来指定应将哪一次出现作为分隔依据。此输入为可选参数，如果未输入，则默认使用数字 `0`。

此函数的输出始终是分隔符（在第二个输入中定义）之前的那部分参考文本。为了阐明此函数，表 4-6 中提供了多个示例。

### 表 4-6. `Text.BeforeDelimiter` 示例

| 公式 | 结果 |
| --- | --- |
| `= Text.BeforeDelimiter("XNW-11-181-M","-")` | `"XNW"` |
| `= Text.BeforeDelimiter("XNW-11-181-M","-",0)` | `"XNW"` |
| `= Text.BeforeDelimiter("XNW-11-181-M","-",1)` | `"XNW-11"` |
| `= Text.BeforeDelimiter("XNW-11-181-M","-",2)` | `"XNW-11-181"` |

与 `Text.AfterDelimiter` 一样，通过在第三个输入中使用 `RelativePosition.FromStart` 或 `RelativePosition.FromEnd` 加上输入的数字（以列表形式），可以改变在参考文本中搜索所需分隔符出现位置的方向。

例如，以下公式表示应输出第一个 `-` 之前注册的文本。该函数的结果是 `"XNW"`。

```
= Text.BeforeDelimiter("XNW-11-181-M","-",0)
```

以下公式的结果与上述公式相同，不同之处在于该公式通过从参考文本的开头（左侧）搜索来显示第一个 `-` 之前的文本：

```
= Text.BeforeDelimiter(
   "XNW-11-181-M",
   "-",
   {0,RelativePosition.FromStart})
```

下一个公式通过从参考文本的末尾（靠近文本结尾处）搜索来提取第一个破折号之前的文本。因此，该函数的结果是 `"XNW-11-181"`。

```
= Text.BeforeDelimiter(
   "XNW-11-181-M",
   "-",
   {0,RelativePosition.FromEnd})
```

### `Text.BetweenDelimiters`

`Text.BetweenDelimiters` 用于分隔两个分隔符之间的部分文本，例如 `-` 和 `/` 之间的文本，如下所示：

```
Text.BetweenDelimiters(
   text as nullable text,
   startDelimiter as text,
   endDelimiter as text,
   optional startIndex as any,
   optional endIndex as any
) as any
```

此函数接收以下五个参数：

1.  此函数的第一个输入代表参考文本。

2.  第二个输入确定用于界定分隔起点的分隔符。

3.  用于界定分隔终点所需的分隔符在此函数的第三个输入中指定。

4.  如果第二个输入中插入的分隔符在参考文本中重复出现多次，则可以在此输入中确定所需进行分隔的重复位置。

5.  如果第三个输入中插入的分隔符在参考文本中重复出现多次，则可以使用此函数的第五个输入来确定分隔应在哪次重复时结束。

根据函数说明，以下公式的结果等于 `-` 字符（其第一次重复）和 `/` 字符（其第一次重复）之间的文本，即等于 `"11-181"`：

```
= Text.BetweenDelimiters(
   "XNW-11-181/MN-12/2-",
   "-",
   "/")
```

# Power Query 文本函数详解

## `Text.BetweenDelimiters`

要开始从第二个重复的“-”进行分隔，只需在该函数的第四个输入中如下输入数字 `1`，这样公式的结果为“181”：

```
= Text.BetweenDelimiters(
"XNW-11-181/MN-12/2-",
"-",
"/",
1)
```

类似地，如果在函数的第五个输入中也输入数字 `1`，则分隔操作将继续直到第二个重复的“/”，结果将等于“181/MN-12”。

```
= Text.BetweenDelimiters(
"XNW-11-181/MN-12/2-",
"-",
"/",
1,
1)
```

需要注意的是，与之前的函数一样，为了在第四个和第五个输入中确定查找分隔符的方向，可以在列表格式中使用 `RelativePosition.FromStart` 和 `RelativePosition.FromEnd` 以及数字。

## `Text.Select`

要从参考文本中提取特殊字符，可以如下使用 `Text.Select`：

```
Text.Select(
text as nullable text,
selectChars as any
) as nullable text
```

该函数接收两个参数：

1.  第一个参数中输入参考文本。
2.  第二个输入中，将要提取的字符或字符列表以文本或文本列表的格式输入。在此输入中输入“XZ”分别表示提取参考文本中的所有`X`和`Z`，但输入 `{"X", "Z"}` 表示提取参考文本中的所有`X`或`Z`。

因此，通过以下公式，应从第一个输入中输入的参考文本中提取字符“2”、“X”和“-”的所有重复，结果等于“22-X-2”：

```
= Text.Select("2023-YXL-12",{"2","X","-"})
```

在下一个示例中，应从参考文本中提取所有大写字母字符，结果等于“YXL”。

```
= Text.Select("2023-YXL-12",{"A".."Z"})
```

## `Text.Split`

`Text.Split` 用于基于分隔符将参考文本分割成多个文本。例如，可以通过此函数将“X-12-M81”分割成 `{"X", "12", "M81"}`（在本例中，分隔符是“-”）：

```
Text.Split(
text as text,
separator as text
) as list
```

该函数接收以下两个参数，并返回一个由分割后的文本组成的列表：

1.  第一个参数中输入参考文本。
2.  第二个参数表示作为分割文本的分隔符的字符或字符序列。在此输入中输入“-”意味着参考文本中“-”之后的部分应该被分割。

此函数的各种示例如下 `表 [4-7]` 所示。

**表 4-7.** `Text.Split` 示例

| **公式** | **结果** |
| :--- | :--- |
| `= Text.Split("XNW-11- 181-M","-")` | `{"XNW","11","181","M"}` |
| `= Text.Split("XNW-11- 181-M","1")` | `{"XNW-","","-","8","-M"}` |
| `= Text.Split("XNW-11-181- M","n")` | `{"XNW-11-181-M"}` |
| `= Text.Split("XNW-11-181- M","N")` | `{"X","W-11-181-M"}` |

同样地，可以使用 `Text.SplitAny` 根据第二个参数中插入的单个字符的每次重复来分割参考文本。所以以下公式的结果是 `{"XNW", "11", "181", "M"}`，而使用 `Text.Split` 的结果是 `{"XNW", "11-181-M"}`：

```
= Text.SplitAny("XNW-11-181-M","W-")
```

## 删除、替换和组合文本

在本节中，将解释用于删除文本的一部分或用新字符替换它或组合不同文本的函数。

### `Text.Remove`

可以通过如下使用 `Text.Remove` 从参考文本中移除特定字符：

```
Text.Remove(
text as nullable text,
removeChars as any
) as nullable text
```

该函数接收这两个输入：

1.  第一个输入是参考文本。
2.  第二个输入定义了你想要从参考文本中移除的字符。要移除一个字符或子字符串，可以在该输入中将其作为文本输入，但要移除多个字符或子字符串，应在一个`{}`内以列表形式输入。

`表 [4-8]` 提供了此函数的各种示例。

**表 4-8.** `Text.Remove` 示例

| **公式** | **结果** |
| :--- | :--- |
| `= Text.Remove("XNW-11-181/MN","1")` | `"XNW--8/MN"` |

# Power Query 文本函数示例

## `Text.Remove`

`= Text.Remove("XNW-11-181/MN","-")`

结果：`"xnW11181/Mn"`

`= Text.Remove("XNW-11-181/MN","/")`

结果：`"xnW-11-181Mn"`

`= Text.Remove("XNW-11-181/MN",{"1","-","/"})`

结果：`"xnW8Mn"`

`= Text.Remove("XNW-11-181/MN",{"A".."Z"})`

结果：`"-11-181/"`

在另一个案例中，通过使用以下公式，可以移除引用文本中的所有数字。此时，公式结果等于`"XNW--/MN"`。

`= Text.Remove("XNW-11-181/MN",{"0".."9"})`

通过使用以下公式，可以移除文本中的所有大写字母。此时，公式结果等于`" -11-181/ "`。

`= Text.Remove("XNW-11-181/MN",{"A".."Z"})`

在另一个示例中，通过使用以下公式，可以移除文本中的所有字母：

`= Text.Remove("XNW-11-181/MN",{"a".."z","A".."Z"})`

## `Text.RemoveRange`

`Text.RemoveRange`函数用于基于字符位置移除引用文本中的一系列字符（例如，移除特定文本中的第七到第十个字符）。该函数在 Power Query 中的定义如下：

```
Text.RemoveRange(
    text as nullable text,
    offset as number,
    optional count as nullable text
) as nullable text
```

`Text.RemoveRange` 接受三个参数：

1.  第一个参数定义引用文本。
2.  第二个参数确定移除的起始位置（请注意，位置从 0 开始）。
3.  第三个参数输入要移除的字符数。此输入是可选的，如果未输入任何值，则默认为 1。

为了更好地检查此函数，表 4-9 中提供了多个示例。

### 表 4-9. `Text.RemoveRange` 示例

| 公式 | 结果 |
|---|---|
| `= Text.RemoveRange("2023-YXL-12",0,5)` | `"YxL-12"` |
| `= Text.RemoveRange("2023-YXL-12",4,5)` | `"202312"` |
| `= Text.RemoveRange("2023-YXL-12",8,3)` | `"YxL-12"` |
| `= Text.RemoveRange("2023-YXL-12",8,5)` | `error` |

## `Text.PadEnd`

通过使用 `Text.PadEnd`，可以将文本的长度（字符数）增加到所需数量，并在文本末尾用指定字符填充新空格。

```
Text.PadEnd(
    text as nullable text,
    count as number,
    optional character as nullable text
) as nullable text
```

此函数接受三个输入，如下所示：

1.  此函数的第一个输入代表引用文本。
2.  第二个输入通过输入一个数字来确定输出的长度。
3.  此输入确定用于填充空格的所需字符。此输入是可选的；如果未输入，则将考虑使用空格。

根据以上说明，基于以下公式，输出必须包含五个字符，并且引用文本末尾的空格必须用`"X"`填充。因此，结果是`"AWXXX"`。

`= Text.PadEnd("AW",5,"X")`

基于该函数的解释，表 4-10 中给出了该函数的各种示例。

### 表 4-10. `Text.PadEnd` 示例

| 公式 | 结果 |
|---|---|
| `= Text.PadEnd("AW",5,"X")` | `"aWxxx"` |
| `= Text.PadEnd("AW",5)` | `"aW "` |
| `= Text.PadEnd("AW",1,"X")` | `"aW"` |

## `Text.PadStart`

`Text.PadStart`函数与`Text.PadEnd`函数类似。其区别在于，此函数中所需的字符将放置在引用文本的开头。换句话说，通过使用`Text.PadStart`，可以通过在文本开头重复新字符来增加文本的长度。此函数的定义如下：

```
Text.PadStart(
    text as nullable text,
    count as number,
    optional character as nullable text
) as nullable text
```

此函数有三个输入，如下所示：

1.  此函数的第一个输入代表引用文本。
2.  第二个输入指定输出文本所需的长度。
3.  第三个输入输入用于填充空格的所需字符。

基于该函数的解释，表 4-11 中列出了该函数的各种示例。

### 表 4-11. `Text.PadStart` 示例

| 公式 | 结果 |
|---|---|
| `= Text.PadStart("AW",5,"X")` | `"xxxaW"` |
| `= Text.PadStart("AW",5)` | `"   aW"` |
| `= Text.PadStart("AW",1,"X")` | `"aW"` |

## `Text.Replace`

使用此函数，可以用新字符替换文本中的子字符串，如下所示：

```
Text.Replace(
    text as nullable text,
    old as text,
    new as text
) as nullable text
```

此函数接受以下三个参数：

1.  第一个输入中输入引用文本。
2.  第二个输入中输入应被移除并替换为新字符的子字符串。
3.  第三个输入中定义应替换已删除部分的新文本。

使用此函数，引用文本中第二个输入的子字符串将被替换为第三个输入中指定的文本。

表 4-12 展示了此函数的一些应用。

### 表 4-12. `Text.Replace` 示例

| 公式 | 结果 |
|---|---|
| `= Text.Replace("XNW-11-181/MN","-","/")` | `"xnW/11/181/Mn"` |
| `= Text.Replace("XNW-11-181/MN","/","-")` | `"xnW-11-181-Mn"` |
| `= Text.Replace("XNW-11-181/MN","XNW","U")` | `"u-11-181/Mn"` |

## `Text.ReplaceRange`

`Text.ReplaceRange` 用于根据字符在引用文本中的位置，用新字符替换多个字符。此函数在 Power Query 中定义如下：

```
Text.ReplaceRange(
    text as nullable text,
    offset as number,
    count as number,
    newText as text
) as nullable text
```

此函数接受四个参数：

1.  第一个参数代表引用文本。
2.  第二个参数通过输入一个数字来确定从引用文本中删除的起始点。
3.  此函数的第三个输入表示要从引用文本中移除的字符数。
4.  此输入中定义替换文本。

基于上述描述，通过以下公式，第六个字符后的三个字符被替换为`"A"`，结果等于 `"2023-A-12"`。

`= Text.ReplaceRange("2023-YXL-12",5,3,"A")`

## `Text.Insert`

要向引用文本中添加新字符，可以使用 `Text.Insert`，如下所示：

```
Text.Insert(
    text as nullable text,
    offset as number,
    newText as text
) as nullable text
```

此函数接受以下三个输入：

1.  第一个输入中输入引用文本。

2.  第二个输入中确定添加新文本的位置。

3.  最后，在此函数的第三个输入中确定要添加到引用文本的文本或字符。

表 4-13 展示了此函数的几个示例。

### 表 4.13 `Text.Insert` 示例

| 公式 | 结果 |
| --- | --- |
| `= Text.Insert("2023-YXL- 12",0,"AB-")` | `“aB-2023-YxL-12”` |
| `= Text.Insert("2023-YXL- 12",5,"AB-")` | `“2023-aB-YxL-12”` |
| `= Text.Insert("2023-YXL- 12",9,"AB-")` | `“2023-YxL-aB-12”` |

### `Text.Repeat`

`Text.Repeat` 函数用于按所需次数重复一段文本，如下所示：

```
Text.Repeat(
    text as nullable text,
    count as number
) as nullable text
```

此函数接收两个输入：

1.  在此函数的第一个输入中输入文本。

2.  第二个输入中通过输入一个数字来指定重复次数。

结合函数说明，此函数对不同值的结果列于表 4-14 中。

### 表 4.14 `Text.Repeat` 示例

| 公式 | 结果 |
| --- | --- |
| `= Text.Repeat("X",3)` | `“xxx”` |
| `= Text.Repeat("X-",3)` | `“x-x-x-”` |
| `= Text.Repeat("X-Y",3)` | `“x-Yx-Yx-Y”` |

### `Text.Combine`

使用 `Text.Combine`，可以合并两个或多个文本，并得到一个文本。

```
Text.Combine(
    texts as list,
    optional separator as nullable text
) as text
```

此函数接受两个参数：

1.  在第一个参数中，输入要合并的文本列表。

2.  第二个参数中确定分隔符。此输入是可选的；如果不输入分隔符，则不会考虑。

表 4-15 提供了该函数的各种示例。

### 表 4-15. `Text.Combine` 示例

| 公式 | 结果 |
| --- | --- |
| `= Text.Combine({"A","B","C"})` | “ABC” |
| `= Text.Combine({"A","B","C"},"-")` | “A-B-C” |
| `= Text.Combine({"A","B","C"}," ")` | “A B C” |

### 文本校正

Power Query 提供了多种修改文本的函数。这些函数大多数以文本作为输入，在移除多余空格、特殊字符，并将参考文本中的字母进行大小写转换后，将校正后的文本作为输出显示。表 4-16 列出了这些函数及其应用。

### 表 4-16. 文本校正函数列表

| 函数 | 说明 |
| --- | --- |
| `Text.Lower` | 使用此函数，输入文本中的所有英文字母都将转换为小写。 |
| `Text.Upper` | 此函数的输出与输入文本相同，只是输出中的所有字母都将显示为大写。 |
| `Text.Proper` | 此函数的输出与输入文本相同，只是在输出文本中，所有单词的首字母将显示为大写，其余字母显示为小写。 |
| `Text.Reverse` | 使用此函数，参考文本中字符的顺序将被反转。 |
| `Text.Trim` | 使用此函数，可以移除文本中的所有多余空格（开头、结尾或相邻的空格）。 |
| `Text.TrimEnd` | 使用此函数，可以移除文本末尾的多余空格。 |
| `Text.TrimStart` | 使用此函数，可以移除文本开头的多余空格。 |
| `Text.Clean` | 使用此函数，除了移除多余空格之外，还可以移除文本中的特殊字符，例如 `#(if)`。 |
| `Text.Length` | 此函数用于确定参考文本中的字符数。 |

表 4-17 列出了这些函数的几个示例。

### 表 4-17. 文本校正函数示例

| 公式 | 结果 |
| --- | --- |
| `=Text.Lower("MN-12qw sx")` | “mn-12qw sx” |
| `=Text.Upper("MN-12qw sx")` | “MN-12QW SX” |
| `=Text.Proper("MN-12qw sx")` | “Mn-12Qw Sx” |
| `= Text.Trim(" WS mn- 1 ")` | “WS mn- 1” |
| `= Text.TrimStart(" WS mn- 1 ")` | “WS mn- 1 ” |
| `= Text.TrimEnd(" WS mn- 1 ")` | “ WS mn- 1” |
| `= Text.TrimEnd(" WS mn- 1 X X ",{"X"})` | “ WS mn- 1 ” |

#### 字符搜索

本节描述了在参考文本中搜索特定字符的不同函数。这些函数分为两组。

第一组中的函数将返回 `True` 或 `False`，而第二组中的函数则以输出形式显示所搜索字符在参考文本中的位置。

#### `Text.StartsWith`

要检查文本的开头字符，可以按如下方式使用 `Text.StartsWith`：

```
Text.StartsWith(
    text as nullable text,
    substring as text,
    optional comparer as nullable function
) as nullable logical
```

使用此函数，您可以检查文本是否以某些字符开头。此函数接受三个参数，其输出始终为 `True` 或 `False`。

1.  第一个参数输入参考文本。

2.  第二个输入用于输入您想要与参考文本开头字符进行比较的子字符串。

3.  此输入为可选参数，用于确定比较逻辑。如果未输入此参数，函数区分大小写。输入表达式 `Comparer.OrdinalIgnoreCase` 意味着函数不再区分大小写。此输入的不同情况如下：
    - `Comparer.Equals`
    - `Comparer.FromCulture`
    - `Comparer.Ordinal`
    - `Comparer.OrdinalIgnoreCase`

基于上述说明，表 4-18 中提供了使用此函数的不同示例。

### 表 4-18. `Text.StartsWith` 示例

| 公式 | 结果 |
| --- | --- |
| `= Text.StartsWith("XN18-M13","X")` | true |
| `= Text.StartsWith("XN18-M13","XN1")` | true |
| `= Text.StartsWith("XN18-M13","xn")` | false |

# 第 4 章 文本函数

## `Text.EndsWith`

要检查文本的结尾字符，可按如下方式使用 `Text.EndsWith`：

```
Text.EndsWith(
    text as nullable text,
    substring as text,
    optional comparer as nullable function
) as nullable logical
```

通过使用此函数，可以检查一个文本是否以特定字符结尾。该函数接收以下三个输入，其输出始终等于 `True` 或 `False`。

1.  参考文本。

2.  希望与参考文本的结尾字符进行比较的指定字符。

3.  此输入为可选，用于确定比较逻辑，其不同情况包括：
    -   `Comparer.Equals`
    -   `Comparer.FromCulture`
    -   `Comparer.Ordinal`
    -   `Comparer.OrdinalIgnoreCase`

基于上述说明，表 4-19 提供了使用此函数的不同示例。

表 4-19. `Text.EndsWith` 示例

| 公式 | 结果 |
|---------|--------|
| `= Text.EndsWith("XN18-M13","M13")` | `true` |
| `= Text.EndsWith("XN18-M13","13")` | `true` |
| `= Text.EndsWith("XN18-M13","m13")` | `False` |
| `= Text.EndsWith("XN18-M13","m13", Comparer.OrdinalIgnoreCase)` | `true` |

## `Text.Contains`

要检查参考文本中是否存在子字符串，可按如下方式使用 `Text.Contains`：

```
Text.Contains(
    text as nullable text,
    substring as text,
    optional comparer as nullable function
) as nullable logical
```

该函数接收以下三个输入：

1.  第一个输入是参考文本。
2.  第二个输入输入要在参考文本中搜索的子字符串。
3.  此输入为可选，用于确定比较逻辑。如果未输入此参数，则该函数区分大小写。输入表达式 `Comparer.OrdinalIgnoreCase` 表示该函数不再区分大小写。此输入的不同情况包括：
    -   `Comparer.Equals`
    -   `Comparer.FromCulture`
    -   `Comparer.Ordinal`
    -   `Comparer.OrdinalIgnoreCase`

根据函数说明，表 4-20 给出了该函数针对不同示例的结果。

表 4-20. `Text.Contains` 示例

| 公式 | 结果 |
|---------|--------|
| `= Text.Contains("XN18-M13","M")` | `true` |
| `= Text.Contains("XN18-M13","8-M")` | `true` |
| `= Text.Contains("XN18-M13","m")` | `False` |
| `= Text.Contains("XN18-M13","M", Comparer.OrdinalIgnoreCase)` | `true` |

## `Text.PositionOf`

要获取子字符串在参考文本中的位置，可按如下方式使用 `Text.PositionOf`：

```
Text.PositionOf(
    text as text,
    substring as text,
    optional occurrence as nullable number,
    optional comparer as nullable function
) as any
```

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

表 4-21. `Text.PositionOf` 示例

| 公式 | 结果 |
|---------|--------|
| `= Text.PositionOf("XNW-11-181-M","-")` | `3` |
| `= Text.PositionOf("XNW-11-181-M","-",1)` | `10` |
| `= Text.PositionOf("XNW-11-181-M","-", Occurrence.Last)` | `10` |
| `= Text.PositionOf("XNW-11-181-M","-",2)` | `{3,6,10}` |
| `= Text.PositionOf("XNW-11-181-M","R")` | `-1` |

## `Text.PositionOfAny`

`Text.PositionOfAny` 类似于 `Text.PositionOf`。区别在于，使用 `Text.PositionOfAny` 可以同时在参考文本中搜索多个字符串，如下所示：

```
Text.PositionOfAny(
    text as text,
    characters as list,
    optional occurrence as nullable number
) as any
```

该函数接收以下三个输入：

1.  参考文本。
2.  第二个输入采用列表形式，包含要在参考文本中查找其位置的子字符串。
3.  此输入为可选。通过输入数字 0、1 或 2，可以指定输出中应显示最小数字（文本开头处的首次出现）、最大数字（文本结尾处的最后一次出现）还是所有数字。

表 4-22 提供了此函数的示例。

表 4-22. `Text.PositionOfAny` 示例

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

表 4-23. 文本转换函数

| 函数 | 描述 |
|----------|-------------|
| `Character.FromNumber` | 此函数接收一个数字（ASCII 码）作为输入，并在输出中显示对应的字符。 |
| `Character.ToNumber` | 此函数接收一个字符作为输入，并在输出中显示对应的 ASCII 码。 |
| `Text.From` | 此函数接收一个数字作为输入，并在输出中以文本格式显示相同的数字。 |
| `Text.ToList` | 此函数接收一个文本作为输入，分离该数字的每个字符，并将结果显示为列表中的列表。 |
| `Value.FromText` | 此函数接收一个文本格式的数字作为输入，并在输出中以数字格式显示相同的数字。 |

表 4-24 显示了这些函数的结果。

## 表 4-24. 文本转换函数示例

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
