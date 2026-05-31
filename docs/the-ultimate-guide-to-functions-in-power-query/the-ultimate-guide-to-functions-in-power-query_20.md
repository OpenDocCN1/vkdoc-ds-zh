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

`Text.RemoveRange(`
    `**text** as nullable text,`
    `**offset** as number,`
    `optional **count** as nullable text`
`) as nullable text`

`Text.RemoveRange` 接受三个参数：
1.  第一个参数定义引用文本。
2.  第二个参数确定移除的起始位置（请注意，位置从 0 开始）。
3.  第三个参数输入要移除的字符数。此输入是可选的，如果未输入任何值，则默认为 1。

为了更好地检查此函数，表 4-9 中提供了多个示例。

**表 4-9. `Text.RemoveRange` 示例**

| 公式                              | 结果          |
|-----------------------------------|---------------|
| `= Text.RemoveRange("2023-YXL-12",0,5)` | `"YxL-12"`    |
| `= Text.RemoveRange("2023-YXL-12",4,5)` | `"202312"`    |
| `= Text.RemoveRange("2023-YXL-12",8,3)` | `"YxL-12"`    |
| `= Text.RemoveRange("2023-YXL-12",8,5)` | `error`       |

## `Text.PadEnd`

通过使用 `Text.PadEnd`，可以将文本的长度（字符数）增加到所需数量，并在文本末尾用指定字符填充新空格。

`Text.PadEnd(`
    `**text** as nullable text,`
    `**count** as number,`
    `optional **character** as nullable text`
`) as nullable text`

此函数接受三个输入，如下所示：
1.  此函数的第一个输入代表引用文本。
2.  第二个输入通过输入一个数字来确定输出的长度。
3.  此输入确定用于填充空格的所需字符。此输入是可选的；如果未输入，则将考虑使用空格。

根据以上说明，基于以下公式，输出必须包含五个字符，并且引用文本末尾的空格必须用`"X"`填充。因此，结果是`"AWXXX"`。

`= Text.PadEnd("AW",5,"X")`

基于该函数的解释，表 4-10 中给出了该函数的各种示例。

**表 4-10. `Text.PadEnd` 示例**

| 公式                              | 结果          |
|-----------------------------------|---------------|
| `= Text.PadEnd("AW",5,"X")`       | `"aWxxx"`     |
| `= Text.PadEnd("AW",5)`           | `"aW "`       |
| `= Text.PadEnd("AW",1,"X")`       | `"aW"`        |

## `Text.PadStart`

`Text.PadStart`函数与`Text.PadEnd`函数类似。其区别在于，此函数中所需的字符将放置在引用文本的开头。换句话说，通过使用`Text.PadStart`，可以通过在文本开头重复新字符来增加文本的长度。此函数的定义如下：

`Text.PadStart(`
    `**text** as nullable text,`
    `**count** as number,`
    `optional **character** as nullable text`
`) as nullable text`

此函数有三个输入，如下所示：
1.  此函数的第一个输入代表引用文本。
2.  第二个输入指定输出文本所需的长度。
3.  第三个输入输入用于填充空格的所需字符。

基于该函数的解释，表 4-11 中列出了该函数的各种示例。



### 4.11 `Text.PadStart` 示例

| 公式 | 结果 |
| --- | --- |
| `= Text.PadStart("AW",5,"X")` | `“xxxaW”` |
| `= Text.PadStart("AW",5)` | `“   aW”` |
| `= Text.PadStart("AW",1,"X")` | `“aW”` |

### `Text.Replace`

使用此函数，可以用新字符替换文本中的子字符串，如下所示：

`Text.Replace(`  
`    text` as nullable text,  
`    old` as text,  
`    new` as text  
`)` as nullable text

此函数接受以下三个参数：

1.  第一个输入中输入引用文本。
2.  第二个输入中输入应被移除并替换为新字符的子字符串。
3.  第三个输入中定义应替换已删除部分的新文本。

使用此函数，引用文本中第二个输入的子字符串将被替换为第三个输入中指定的文本。

表 4-12 展示了此函数的一些应用。

### 表 4.12 `Text.Replace` 示例

| 公式 | 结果 |
| --- | --- |
| `= Text.Replace("XNW-11-181/MN","-","/")` | `“xnW/11/181/Mn”` |
| `= Text.Replace("XNW-11-181/MN","/","-")` | `“xnW-11-181-Mn”` |
| `= Text.Replace("XNW-11-181/MN","XNW","U")` | `“u-11-181/Mn”` |

### `Text.ReplaceRange`

`Text.ReplaceRange` 用于根据字符在引用文本中的位置，用新字符替换多个字符。此函数在 Power Query 中定义如下：

`Text.ReplaceRange(`  
`    text` as nullable text,  
`    offset` as number,  
`    count` as number,  
`    newText` as text  
`)` as nullable text

此函数接受四个参数：

1.  第一个参数代表引用文本。
2.  第二个参数通过输入一个数字来确定从引用文本中删除的起始点。
3.  此函数的第三个输入表示要从引用文本中移除的字符数。
4.  此输入中定义替换文本。

基于上述描述，通过以下公式，第六个字符后的三个字符被替换为`“A”`，结果等于 `“2023-A-12”`。

`= Text.RemoveRange("2023-YXL-12",5,3,"A")`

### `Text.Insert`

要向引用文本中添加新字符，可以使用 `Text.Insert`，如下所示：

`Text.Insert(`  
`    text` as nullable text,  
`    offset` as number,  
`    newText` as text  
`)` as nullable text

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

`Text.Repeat(`  
`    text` as nullable text,  
`    count` as number  
`)` as nullable text

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

`Text.Combine(`  
`    texts` as list,  
`    optional separator` as nullable text  
`)` as text

此函数接受两个参数：

1.  在第一个参数中，输入要合并的文本列表。
2.  第二个参数中确定分隔符。此输入是可选的；如果不输入分隔符，则不会考虑。



表 4-15 提供了该函数的各种示例。

***表 4-15.** Text.Combine 示例*

| **公式** | **结果** |
| --- | --- |
| `= Text.Combine({"A","B","C"})` | “ABC” |
| `= Text.Combine({"A","B","C"},"-")` | “A-B-C” |
| `= Text.Combine({"A","B","C"}," ")` | “A B C” |

### 文本校正

Power Query 提供了多种修改文本的函数。这些函数大多数以文本作为输入，在移除多余空格、特殊字符，并将参考文本中的字母进行大小写转换后，将校正后的文本作为输出显示。表 4-16 列出了这些函数及其应用。

***表 4-16.** 文本校正函数列表*

| **函数** | **说明** |
| --- | --- |
| `Text.Lower*` | 使用此函数，输入文本中的所有英文字母都将转换为小写。 |
| `Text.Upper*` | 此函数的输出与输入文本相同，只是输出中的所有字母都将显示为大写。 |
| `Text.Proper*` | 此函数的输出与输入文本相同，只是在输出文本中，所有单词的首字母将显示为大写，其余字母显示为小写。 |
| `Text.Reverse` | 使用此函数，参考文本中字符的顺序将被反转。 |
| `Text.Trim*` | 使用此函数，可以移除文本中的所有多余空格（开头、结尾或相邻的空格）。 |
| `Text.TrimEnd*` | 使用此函数，可以移除文本末尾的多余空格。 |
| `Text.TrimStart*` | 使用此函数，可以移除文本开头的多余空格。 |
| `Text.Clean` | 使用此函数，除了移除多余空格之外，还可以移除文本中的特殊字符，例如 `#(if)`。 |
| `Text.Length` | 此函数用于确定参考文本中的字符数。 |

表 4-17 列出了这些函数的几个示例。

***表 4-17.** 文本校正函数示例*

| **公式** | **结果** |
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

#### Text.StartsWith

要检查文本的开头字符，可以按如下方式使用 `Text.StartsWith`：

```
Text.StartsWith(
text as nullable text,
substring as text,
optional comparer as nullable function
) as nullable logical
```

使用此函数，您可以检查文本是否以某些字符开头。此函数接受三个参数，其输出始终为 `True` 或 `False`。

1. 第一个参数输入参考文本。
2. 第二个输入用于输入您想要与参考文本开头字符进行比较的子字符串。
3. 此输入为可选参数，用于确定比较逻辑。如果未输入此参数，函数区分大小写。输入表达式 `Comparer.OrdinalIgnoreCase` 意味着函数不再区分大小写。此输入的不同情况如下：
   - `Comparer.Equals`
   - `Comparer.FromCulture`
   - `Comparer.Ordinal`
   - `Comparer.OrdinalIgnoreCase`

基于上述说明，表 4-18 中提供了使用此函数的不同示例。

***表 4-18.** Text.StartsWith 示例*

| **公式** | **结果** |
| --- | --- |
| `= Text.StartsWith("XN18-M13","X")` | true |
| `= Text.StartsWith("XN18-M13","XN1")` | true |
| `= Text.StartsWith("XN18-M13","xn")` | false |



