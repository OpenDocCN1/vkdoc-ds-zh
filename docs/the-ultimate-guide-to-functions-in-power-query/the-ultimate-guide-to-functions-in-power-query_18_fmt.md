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