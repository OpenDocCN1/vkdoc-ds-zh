# 7. 字符类

字符类是正则表达式用来理解哪些字符应被视为匹配或“反匹配”（即除了所显示字符以外的任何字符）的方式。请注意，此处的“类”与 R 语句`class(x)`（它会给出对象`x`的类）无关。

字符类起初很简单。只需将要匹配的字符括在方括号内即可。`[fk]`表示选择任何包含“f”或“k”的内容。哪个字符在前无关紧要，你可以根据需要包含任意数量的字母、数字或其他字符。

两个字符之间的连字符表示一个范围。范围应始终是从低到高（a 到 z，而不是 z 到 a），例如，`[0-9]`表示所有数字，`[a-z]`表示小写字母。一个字符类只匹配*单个*字符。例如，`gr[ae]y`不匹配`graay`或`graey`。*请记住“ae”表示 a 或 e，但不同时包含两者*。

否定字符类：如果你在左方括号*后面*输入一个脱字符 (^)，则模式匹配将基于任何*不在*该字符类中的字符。需要在表达式中包含换行符以确保结果准确。例如，`[⁰-7\r\n]`匹配任何不是 0 到 7 且不是换行符的字符。另一个例子是`t[^i]`，它表示模式是一个`t`后跟一个不是`i`的单个字符。

## 7.1 `DOT`

`Dot`意为“匹配任意字符”。唯一的不确定点在于是否包含换行符。各种正则表达式风格对此处理方式不同。在 R 语言中，包含`dotall = TRUE`可以确保换行符（`\n`）被包含在内。以下示例中，第一种情况展示了被换行符包围的`X`。如果未设置`dotall = TRUE`，则匹配失败。在第二种情况下，包含了换行符，因此匹配成功。

`dotall`：

```
str_detect("\nX\n", ".X.")
## [1] FALSE
str_detect("\nX\n", regex(".X.", dotall = TRUE))
## [1] TRUE
my.regex <- ".Z."
x <- "aZa"
```

`Z`两侧均有字符：

```
str_detect(x, regex(my.regex, dotall = TRUE))
## [1] TRUE
x <- "Za"
str_detect(x, regex(my.regex, dotall = TRUE))
## [1] FALSE
```

## 7.2 锚点

锚点，就像船只的锚一样，提供了一个附着点。通过锚点字符，正则表达式可以从字符串的开头或结尾开始匹配。例如，假设你在 10,000 张发票中搜索所有以“IBM”开头的供应商。你可能希望找到“IBM 零件部”、“IBM 软件服务”等，但不会对“南方 IBMATTER 公司”感兴趣。表 7-1 总结了锚点字符。图 7-1 是一张帮助记忆模式起点和终点的示意图。

![../images/509102_1_En_7_Chapter/509102_1_En_7_Fig1_HTML.jpg](img/509102_1_En_7_Fig1_HTML.jpg)

图 7-1 表示适用模式起点和终点的锚点

表 7-1 锚点语法

| 字符 | 匹配内容 |
| --- | --- |
| `^` | 字符串的开头。 |
| `$` | 字符串的结尾。 |
| `\A` | 多行字符串（`multiline = TRUE`）的输入开头。 |
| `\z` | 多行字符串（`multiline = TRUE`）的输入结尾。 |
| `\Z` | 输入结尾，但在最终行终止符之前（如果存在）（`multiline = TRUE`）。 |

## 7.3 多行指定

字符向量`x`包含三个换行符。由于其他两行位于不同行，因此只提取了第一行的实例。

```
x <- "Line 1\nLine 2\nLine 3\n"
str_extract_all(x, "^Line..")[[1]]
## [1] "Line 1"
```

指定了`multiline`后，三个行字符串均被捕获：

```
str_extract_all(x, regex("^Line..", multiline = TRUE))[[1]]
## [1] "Line 1" "Line 2" "Line 3"
str_extract_all(x, regex("\\ALine..", multiline = TRUE))[[1]]
## [1] "Line 1"
```

只提取了一个“Line”，因为`\A`表示仅提取多行字符串的开头部分。

## 7.4 单词边界

单词边界可以出现在以下三种位置之一：

- 如果字符串的第一个字符是单词字符，则在第一个字符之前
- 如果字符串的最后一个字符是单词字符，则在最后一个字符之后
- 在字符串的两个字符之间，其中一个字符是单词字符，另一个不是单词字符

使用`\`的单词边界有两种表示法：

- `\b`表示单词边界
- `\B`表示非单词边界

对于这段代码，R 语言要求使用双斜杠。`\\b`表示整个单词的锚点（起点和终点）。注意，只有“time”被捕获，而非“timex”：

```
x <- "My timex shows the correct time   "
str_extract(x, "\\btime\\b")
## [1] "time"
str_extract (x, "\\Btimex\\B") # 错误
## [1] NA
string1 <- "Mytimexshows the correct time   "
str_extract(string1, "\\Btimex\\B")
## [1] "timex"
```

使用相同的正则表达式，在`string1`中“time”被匹配，因为“time”两侧没有空格（单词边界）。

匹配结果已显示，但只输出了第一个匹配项：

```
string2 <- "Mytimexisagenuinetimexforsure!"
str_extract (string2, "\\Btimex\\B")
## [1] "timex"
```

在第一个示例中，“time”被夹在完整单词“My”和“x”之间。在第二个示例中，要匹配的模式是`timex`，且两侧无单词边界。对于第三个示例，“timex”被夹在两个`\\B`非单词边界之间，因此输出为“timex”。

## 7.5 空白符

在正则表达式中，最常用的空白符形式包括空格（␣）、制表符（`\t`）、换行符（`\n`）和回车符（`\r`）（在 Windows 环境中很有用）。这些特殊字符各自匹配对应的空白符。此外，空白符特殊字符`\s`可以匹配前述任意一种特定空白符，在处理原始输入文本时非常有用。例如，当你导入一个包含许多多余空格的文本文件或 CSV 文件时，可以将多个连续空格替换为单个空格。

```
test.vector <- "I pressed the space bar          too many times"
gsub("\\s+"," ",test.vector)
## [1] "I pressed the space bar too many times"
```

`\\s+`会匹配任意空白字符或其重复，并将其替换为一个空格“ ”。

使用内置字符类（如`[:space:]`）可以使代码更易读：

```
some.text <- c("tree","frog  ","House99",
"Claudia_Carol_Cathy")
i <- grep("[[:space:][:digit:]]+", some.text)
some.text[i]
## [1] "frog  "  "House99"
```

“Frog”末尾有空格，“House”末尾有数字 99。通过`some.text`匹配到了一个或多个空格或数字。

## 7.6 扩展正则表达式

某些命名的字符类是预定义的。其解释取决于 POSIX 区域设置。区域设置对象会尝试捕获可能因国家/地理区域而异的所有默认值。你只需设置一次区域设置，详细信息便会自动传递给列解析器。默认设置已选择尽可能匹配 R 语言（即美式英语）。

在 R 语言中，某些字符类（分组）被便捷地预定义。表 7-2 展示了最常见的类。

表 7-2 预定义的扩展正则表达式术语

| 类 | 含义 |
| --- | --- |
| `[:alnum:]` | 字母数字字符：`[:alpha:]`和`[:digit:]`。 |
| `[:alpha:]` | 字母字符：`[:lower:]`和`[:upper:]`。 |
| `[:blank:]` | 空白字符：空格和制表符，以及可能其他依赖于区域设置的字符，如不间断空格。 |
| `[:cntrl:]` | 控制字符。在 ASCII 中，这些字符的八进制码为 000–037 和 177 (DEL)。在其他字符集中，它们是对应的等效字符（如果有）。 |
| `[:digit:]` | 数字：0 1 2 3 4 5 6 7 8 9。 |
| `[:graph:]` | 图形字符：`[:alnum:]`和`[:punct:]`。 |
| `[:lower:]` | 当前区域设置中的小写字母。 |
| `[:print:]` | 可打印字符：`[:alnum:]`、`[:punct:]`和空格。 |
| `[:punct:]` | 标点字符：! " # $ % & ' ( ) * + , . / : ; < = > ? @ [ \ ] ^ _ ` { &#124; } ~ |
| `[:space:]` | 空格字符：制表符、换行符、垂直制表符、换页符、回车符和空格。 |
| `[:upper:]` | 当前区域设置中的大写字母。 |
| `[:xdigit:]` | 十六进制数字：0 1 2 3 4 5 6 7 8 9 A B C D E F a b c d e f。 |

## 7.7 如何设置区域设置

如下所示，`locale`（需要`readr`包）用于设置特定于某个国家的参数。`locale()`则会重置为 R 的“开箱即用”值：

```
locale()
## 
## Numbers:  123,456.78
## Formats:  %AD / %AT
## Timezone: UTC
## Encoding: UTF-8
## 
## Days:   Sunday (Sun), Monday (Mon), Tuesday (Tue), Wednesday (Wed),
##         Thursday (Thu), Friday (Fri), Saturday (Sat)
## Months: January (Jan), February (Feb), March (Mar), April (Apr), May
##         (May), June (Jun), July (Jul), August (Aug), September
##         (Sep), October (Oct), November (Nov), December (Dec)
## AM/PM:  AM/PM
locale ("fr")
## 
## Days:   dimanche (dim.), lundi (lun.), mardi (mar.), mercredi (mer.),
##         jeudi (jeu.), vendredi (ven.), samedi (sam.)
## Months: janvier (janv.), fÃ©vrier (fÃ©vr.), mars (mars), avril (avr.), mai
##         (mai), juin (juin), juillet (juil.), aoÃ»t (aoÃ»t),
##         septembre (sept.), octobre (oct.), novembre (nov.),
##         dÃ©cembre (dÃ©c.)
## AM/PM:  AM/PM
```

南美区域设置：

```
locale ("es", decimal_mark = ",")
## 
## Numbers:  123.456,78
## Formats:  %AD / %AT
## Timezone: UTC
## Encoding: UTF-8
## 
## Days:   domingo (dom.), lunes (lun.), martes (mar.), miÃ©rcoles (miÃ©.),
##         jueves (jue.), viernes (vie.), sÃ¡bado (sÃ¡b.)
## Months: enero (ene.), febrero (feb.), marzo (mar.), abril (abr.), mayo
##         (may.), junio (jun.), julio (jul.), agosto (ago.),
##         septiembre (sept.), octubre (oct.), noviembre (nov.),
##         diciembre (dic.)
## AM/PM:  a. m./p. m.
```

脚注 1 2 3 4 5