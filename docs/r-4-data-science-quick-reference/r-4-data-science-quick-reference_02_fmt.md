# 2. 数据导入：readr

在分析数据之前，我们需要将其加载到 R 中。Tidyverse 中用于此功能的主要包是 `readr`，当你加载 `tidyverse` 包时，它也会被加载：

```
library(tidyverse)
```

但你也可以通过以下命令显式加载它：

```
library(readr)
```

表格数据通常存储在文本文件或压缩文本文件中，其行和列与表格结构相匹配。文件中的每一行都是表格中的一行，列则由已知的分隔符字符隔开。`readr` 包正是为此类数据表示而设计的，它包含了用于读取和写入这种格式的各种变体文件的函数。此外，它还提供了确定每一列数据类型的函数，既可以通过自动推断类型，也可以根据用户的指定来进行。

## 读取数据的函数

`readr` 包提供了以下用于读取表格数据的函数：

| 函数 | 文件格式 |
| --- | --- |
| `read_csv()` | 逗号分隔值 |
| `read_csv2()` | 分号分隔值 |
| `read_tsv()` | 制表符分隔值 |
| `read_delim()` | 通用列分隔符^(²) |
| `read_table()` | 空格分隔值（等宽列） |
| `read_table2()` | 空格分隔值（可变列宽） |

这些函数的接口差异很小。在以下文本中，我将描述 `read_csv`，但会在其他函数不同时加以强调。`read_csv` 函数用于从包含逗号分隔值的文件中读取数据。这样的文件可能如下所示：

```
A,B,C,D
1,a,a,1.2
2,b,b,2.1
3,c,c,13.0
```

与基础 R 的 `read.csv` 函数不同，`read_csv` 还能处理列之间有空格的文件，因此它将把以下数据解释为与上一个文件相同：

```
A,  B,  C,    D
1,  a,  a,   1.2
2,  b,  b,   2.1
3,  c,  c,  13.0
```

如果你改用 R 的 `read.csv` 函数，那么列 B 和 C 前面的空格将被包含在数据中，并且文本列将被解释为因子。

文件中的第一行将被解释为标题行，用于命名各列，其余三行则被解释为数据行。

假设该文件名为 `data/data.csv`，你可以像这样读取其数据：

```
my_data<-read_csv(file ="data/data.csv")
## Rows: 3 Columns: 4
##---- Column specification --------------------------
## Delimiter: ","
## chr (2): B, C
## dbl (2): A, D
##
## i Use 'spec()' to retrieve the full column specification for this data.
## i Specify the column types or set 'show_col_types = FALSE' to quiet this message.
```

从 `read_csv()` 得到的消息告诉你，如果你使用 `spec()` 函数，就可以获得关于它为每列推断的类型的信息：

```
spec(my_data)
## cols(
##   A = col_double(),
##   B = col_character(),
##   C = col_character(),
##   D = col_double()
## )
```

在读取文件时，`read_csv` 会推断列 A 和 D 是数字，而列 `B` 和 `C` 是字符串。

如果你对此感到满意，并且以后不想再看到这些信息，你可以使用以下选项：

```
show_col_types = FALSE:
my_data<-read_csv(file ="data/data.csv",
show_col_types =FALSE)
```

如果文件包含制表符分隔的值：

```
A  B  C  D
1  a  a  1.2
2  b  b  2.1
3  c  c  13.0
```

那么你应该改用 `read_tsv()`。

```
my_data<-read_tsv(file ="data/data.tsv",
show_col_types =FALSE)
```

你用 `read_csv` 读取的文件可以是压缩文件。如果文件名的后缀是 `.gz`、`.bz2`、`.xz` 或 `.zip`，那么在 `read_csv` 加载数据之前，它会被解压缩。

```
my_data<-read_csv(file ="data/data.csv.gz",
show_col_types =FALSE)
```

如果文件名是一个 URL（即前缀为 `http://`、`https://`、`ftp://` 或 `ftps://`），那么该文件将被自动下载。

你也可以提供一个字符串作为 `file` 参数：

```
read_csv(
"A, B, C,    D
1, a, a,  1.2
2, b, b,  2.1
3, c, c, 13.0
", show_col_types = FALSE)
## # A tibble: 3 × 4
##      A B     C         D
##     
## 1    1 a     a       1.2
## 2    2 b     b       2.1
## 3    3 c     c      13
```

这在数据分析项目中很少有用，但你可以用它来创建示例或进行调试。

## 文件标题行

逗号分隔文件中的第一行并不总是列名；该信息可能来自文件外部的其他来源。如果你不想将第一行解释为列名，可以使用选项 `col_names = FALSE`。

```
read_csv(
file ="data/data.csv",
col_names =FALSE,
show_col_types =FALSE
)
## # A tibble: 4 × 4
##   X1    X2    X3    X4
##      
## 1 A     B     C     D
## 2 1     a     a     1.2
## 3 2     b     b     2.1
## 4 3     c     c     13.0
```

由于 `data/data.csv` 文件确实有一个标题行，它被解释为数据的一部分，并且因为标题行由字符串组成，`read_csv` 推断所有列的类型都是字符串。如果我们没有标题行，例如，如果我们有这样的文件 `data/data-no-header.csv`：

```
1, a, a, 1.2
2, b, b, 2.1
3, c, c, 13.0
```

那么我们会得到与之前相同的数据框，只是名称是自动生成的：

```
read_csv(
file ="data/data-no-header.csv",
col_names =FALSE,
show_col_types =FALSE
)
## # A tibble: 3 × 4
##      X1 X2    X3        X4
##      
## 1     1 a     a        1.2
## 2     2 b     b        2.1
## 3     3 c     c       13
```

自动生成的列名都以 `X` 开头，后面跟着文件中从左到右的列序号。

如果你有一个不带标题行的文件数据，但你不想要自动生成的名称，你可以向 `col_names` 选项提供列名：

```
read_csv(
file ="data/data-no-header.csv",
col_names =c("X","Y","Z","W"),
show_col_types =FALSE
)
## # A tibble: 3 × 4
##       X Y     Z         W
##      
## 1     1 a     a       1.2
## 2     2 b     b       2.1
## 3     3 c     c        13
```

如果存在标题行，但你想要重命名列，你不能仅仅通过 `col_names` 向 `read_csv` 提供名称。第一行仍然会被解释为数据。这会使第一行包含你不想要的数据，并且还会影响列的类型推断。

然而，你可以在 `read_csv` 将行解析为数据之前跳过一些行。由于 `data/data.csv` 中有一个标题行，我们可以跳过一次，并设置列名。

read_csv(
file ="data/data.csv",
col_names =c("X","Y","Z","W"),
skip =1,
show_col_types =FALSE
)
## # A tibble: 3 × 4
##       X Y     Z         W
##      
## 1     1 a     a       1.2
## 2     2 b     b       2.1
## 3     3 c     c      13

你也可以使用 `n_max` 选项来限制要加载的数据行数。

```
read_csv(
file ="data/data.csv",
col_names =c("X","Y","Z","W"),
skip =1,
n_max =2,
show_col_types =FALSE
)
```

如果你的输入文件包含注释行（可通过特定字符识别，该行后面的内容应视为注释），你可以通过提供 `comment` 选项来跳过它们：

```
read_csv(
"A, B, C,      D   # this is a comment
1, a, a,   1.2     # another comment
2, b, b,   2.1
3, c, c, 13.0",
comment ="#",
show_col_types =FALSE)
## # A tibble: 3 × 4
##       A B     C         D
##      
## 1     1 a     a       1.2
## 2     2 b     b       2.1
## 3     3 c     c      13
```

你可以将整行都作为注释，但这样你需要让注释字符位于该行的最左侧：

```
read_csv(
"A, B, C,   D   # this is a comment
# whole line comment
1, a, a,   1.2   # another comment
2, b, b,   2.1
3, c, c, 13.0",
comment ="#",
show_col_types =FALSE)
## # A tibble: 3 × 4
##       A B     C         D
##      
## 1     1 a     a       1.2
## 2     2 b     b       2.1
## 3     3 c     c      13
```

如果在注释之前有空格，该函数无法判断是否存在错误——这看起来像是一行缺少了列，而不是一个空行——因此你会得到一个警告，并且注释行的位置会出现一个包含 `NA` 的行。

```r
read_csv(
"A, B, C,   D   # 这是一个注释
## 缩进是个潜在问题；是否缺少列？
1, a, a,   1.2   # 另一个注释
2, b, b,   2.1
3, c, c, 13.0",
comment ="#",
show_col_types =FALSE
)
## 警告：存在一个或多个解析问题，详见
## 'problems()'
## # A tibble: 4 × 4
##       A B     C         D
##      
## 1    NA        NA
## 2     1 a     a       1.2
## 3     2 b     b       2.1
## 4     3 c     c      13
```

关于影响输入文件解析方式的更多选项，请阅读函数文档：`?read_csv`。

## 列类型

当 `read_csv` 解析文件时，它会推断每一列的类型。这种推断可能速度较慢，更糟的是推断结果可能不准确。如果你事先知道列的类型，可以使用 `col_types` 选项来指定。若如此做，则 `read_csv` 将不会猜测类型，但它会将无法按指定类型解析的值替换为 `NA`。^(³)

### 基于字符串的列类型指定

在最简单的字符串指定格式中，你必须提供一个长度与列数相同的字符串，字符串中的每个字符指定一列的类型。指定不同类型的字符如下：

| 字符 | 类型 |
| --- | --- |
| `c` | 字符型 |
| `i` | 整型 |
| `n` | 数值型 |
| `d` | 双精度型 |
| `l` | 逻辑型 |
| `f` | 因子型 |
| `D` | 日期型 |
| `T` | 日期时间型 |
| `t` | 时间型 |
| `?` | 猜测（默认） |
| `_/-` | 跳过该列 |

默认情况下，`read_csv` 会进行猜测，因此我们可以使用类型指定`"????"`来明确表示：

```r
read_csv(
file ="data/data.csv",
col_types ="????"
)
## # A tibble: 3 × 4
##       A B     C         D
##      
## 1      1 a    a       1.2
## 2      2 b    b       2.1
## 3      3 c    c      13
```

猜测的结果是：列 `A` 和 `D` 为双精度型，列 `B` 和 `C` 为字符型。如果要明确指定，可以使用`"dccd"`。

```r
read_csv(
file ="data/data.csv",
col_types ="dccd"
)
## # A tibble: 3 × 4
##       A B     C         D
##      
## 1     1 a     a       1.2
## 2     2 b     b       2.1
## 3     3 c     c      13
```

如果希望列 `A` 为整型，可以使用`"iccd"`：

```r
read_csv(
file ="data/data.csv",
col_types ="iccd"
)
## # A tibble: 3 × 4
##       A B     C         D
##      
## 1     1 a     a       1.2
## 2     2 b     b       2.1
## 3     3 c     c      13
```

如果尝试将列 `D` 也解释为整型，你将收到一系列警告消息，且列 `D` 中的值将全部变为 `NA`；列 `D` 中的数字无法被解释为整数，`read_csv` 也不会将它们四舍五入为整数。

```r
read_csv(
file ="data/data.csv",
col_types ="icci"
)
## 警告：存在一个或多个解析问题，详见
## 'problems()'
## # A tibble: 3 × 4
##       A B     C         D
##      
## 1     1 a     a        NA
## 2     2 b     b        NA
## 3     3 c     c        NA
```

如果指定某列为 `d` 类型，则该列中的数字必须是整数或小数。如果使用 `n` 类型（`read_csv` 默认猜测的类型），你也会得到双精度值，但后者能够处理可被解释为数字的字符串，例如金额、百分比和数字中的千位分隔符。`n` 列类型会忽略首尾文本并处理数字分隔符：

通过以下函数调用

```r
read_csv(
'A, B, C,   D,   E
$1,a,a,1.2%,"1,100,200"
$2,b,b,2.1%,"140,000"
$3,c,c,13.0%,"2,005,000"',
col_types ="nccnn"
)
## # A tibble: 3 × 5
##       A B     C         D        E
##          
## 1     1 a     a       1.2  1100200
## 2     2 b     b       2.1   140000
## 3     3 c     c      13    2005000
```

列 `A`、`D` 和 `E` 将被读取为数值型。如果使用类型指定 `d`，则不会如此，所有值都会变为 `NA`。

小数点指示符和千位分隔符在全球各地有所不同。默认情况下，`read_csv` 使用美国惯例：小数点用点号，千位分隔符用逗号。在许多欧洲国家，情况则相反。你可以使用 `locale` 选项来更改这些设置：

```markdown
在前一个示例中，我明确指定了`read_csv`应如何解释数字，但你也可以使用ISO 639-1语言代码。^(⁴) 如果这样做，你还能获得本地的时间惯例以及本地化的月份和星期名称。默认语言是英语，但如果你的数据来自丹麦，例如，你想要使用丹麦语惯例，你可以使用本地化的`locale("da")`。对于法语数据，你可以使用`locale("fr")`。如果你在R控制台中输入这些，你将会看到这些语言下的月份和星期名称，包括它们的缩写形式。

更多选项请参阅`?locale`文档。

在使用逗号作为小数点、“`.`”作为数字分组符的文件中，列分隔符通常是“`;`”而非“`,`”。这样就不必用小括号将十进制数字括起来。`read_csv2`函数的作用与`read_csv`相同，但使用“`;`”作为列分隔符，“`.`”用于数字分组。

*逻辑*类型用于布尔值。如果某一列只包含`TRUE`和`FALSE`（不区分大小写）：

```
read_csv(
'A, B, C,   D
TRUE, a, a,   1.2
false, b, b,   2.1
true, c, c, 13',
show_col_types =FALSE
)
## # A tibble: 3 × 4
##   A     B     C         D
##      
## 1 TRUE  a     a       1.2
## 2 FALSE b     b       2.1
## 3 TRUE  c     c      13
```

那么`read_csv`将会推测该列的类型是逻辑型。

然而，用0和1来编码布尔值的情况也并不少见，由于默认情况下它们会被解释为数字，你可以使用`l`来明确指定其类型：

```
read_csv(
'A, B, C,   D
1, a, a,   1.2
0, b, b,   2.1
1, c, c, 13',
col_types ="lccn"
)
## # A tibble: 3 × 4
##   A     B     C         D
##      
## 1 TRUE  a     a       1.2
## 2 FALSE b     b       2.1
## 3 TRUE  c     c      13
```

如果你使用类型`l`，你可以将`TRUE/FALSE`（忽略大小写）与0/1混合使用。任何其他数字或字符串都将被转换为`NA`。

`D`、`t`和`T`类型分别用于*日期*、*时间点*和*日期时间*。日期和时间正如你所期望的那样。一个日期指定一个天数范围，例如，一天、一周、一个月或一年。一个时间点指定一天的特定时间，例如，一小时、一分钟或一秒钟。日期时间则结合了某一天和某个时间，也就是说，它指定了特定某一天的特定时间。

```
read_csv(
'D, T, t
"2018-08-23", "2018-08-23T14:30", 14:30',
col_types ="DTt"
)
## # A tibble: 1 × 3
##   D          T                   t
##                      
## 1 2018-08-23 2018-08-23 14:30:00 14:30
```

如果你使用这些类型规范之一，时间和日期应采用ISO 8601格式。^(⁵) 然而，书写时间和日期的本地惯例差异很大，很少是ISO 8601格式。当你的时间数据不是ISO 8601格式时，你需要告诉`read_csv`如何读取它们。

默认的时间解析器处理`hh:mm`、`hh:mm:ss`格式的时间，并支持`am`和`pm`后缀；它足以应对大多数时间格式（但请注意，它要求时间采用`hh:mm`或`hh:mm:ss`格式；它对表示小时的字符数很灵活，并且你可以省略秒，但不能省略分钟）。日期和日期时间的变化比时间格式大得多，因此通常需要指定编码格式。

你可以使用区域设置选项来改变`read_csv`解析日期 (`D`) 和时间 (`t`) 的方式。

```
read_csv(
'D, t
"23 Oct 2018", 2pm',
col_types ="Dt",
locale =locale(
date_format ="%d %b %Y",
time_format ="%I%p"
)
)
## # A tibble: 1 × 2
##   D          t
##        
## 1 2018-10-23 14:00
```

`date_format "%d %b %Y"`表示日期写作日、三个字母的月份缩写和四位年份，三者之间用空格分隔。`time_format "%I%p"`表示我们希望时间写作从1到12的数字，不含分钟信息，小时后紧跟着am/pm且中间没有任何空格。

对于日期时间 (`T`)，我们无法通过`locale`指定格式。我们需要一个更详细的类型规范，稍后会再讨论。我们稍后也会回到用于解析日期和时间的格式规范。

未被立即解析为数字、布尔值、日期或时间的列将被解析为*字符串*。如果你希望这些列被识别为因子，请使用`f`类型规范。

```
read_csv(
'A, B, C,   D
1, a, a,   1.2
0, b, b,   2.1
1, c, c, 13',
col_types ="lcfn")
## # A tibble: 3 × 4
##   A     B     C         D
##      
## 1 TRUE  a     a       1.2
## 2 FALSE b     b       2.1
## 3 TRUE  c     c      13
```

如果你只想使用部分列，你可以使用“类型”`-`或`_`来跳过其余列：

```
read_csv(
file ="data/data.csv",
col_type ="_cc-"
)
## # A tibble: 3 × 2
##   B     C
##    
## 1 a     a
## 2 b     b
## 3 c     c
```

如果你使用字符串指定列类型，则应指定所有列的类型。如果你只想定义列的子集的类型，你可以使用函数`cols()`来指定类型。你使用命名参数来调用这个函数，其中名称是列名，参数是类型。

```
read_csv(
file ="data/data.csv",
col_types =cols(A ="c")
)
## # A tibble: 3 × 4
##   A     B     C        D
##      
## 1 1     a     a       1.2
## 2 2     b     b       2.1
## 3 3     c     c      13
read_csv(
file ="data/data.csv",
col_types =cols(A ="c",D ="c")
)
## # A tibble: 3 × 4
##   A     B     C      D
##      
## 1 1     a     a     1.2
## 2 2     b     b     2.1
## 3 3     c     c     13.0
```

### 基于函数的列类型指定

如果你和我一样，可能会觉得记住不同列类型的单字符代码很困难。如果是这样，你可以使用通过函数调用指定的更长类型名称。这些函数以`col_`开头，因此你可以利用自动补全功能来获取它们的列表。通过函数指定的类型与使用字符指定的类型当然是一样的，具体函数如下：

| **函数** | **类型** |
| --- | --- |
| `col_character()` | 字符型 |
| `col_integer()` | 整型 |
| `col_number()` | 数值型 |
| `col_double()` | 双精度型 |
| `col_logical()` | 逻辑型 |
| `col_factor()` | 因子型 |
| `col_date()` | 日期型 |
| `col_datetime()` | 日期时间型 |
```

### 列类型函数

你可以在 `cols()` 调用中使用基于函数的类型规范。

| `col_time()` | 时间型 |
| --- | --- |
| `col_guess()` | 猜测（默认） |
| `col_skip()` | 跳过该列 |

```r
read_csv(
  file = "data/data.csv",
  col_types = cols(A = col_integer())
)
## # 一个 tibble: 3 × 4
##       A B     C         D
##      
## 1     1 a     a       1.2
## 2     2 b     b       2.1
## 3     3 c     c      13

read_csv(
  file = "data/data.csv",
  col_types = cols(D = col_character())
)
## # 一个 tibble: 3 × 4
##       A B     C     D
##      
## 1     1 a     a     1.2
## 2     2 b     b     2.1
## 3     3 c     c     13.0
```

大多数 `col_` 函数不接受任何参数，但它们的处理方式与字符串规范一样，都会受到 `locale` 参数的影响。

然而，对于因子、日期、时间和日期时间类型，你可以使用 `col_` 函数对格式进行更多控制。你可以使用这些函数的参数来指定 `read_csv` 应如何解析日期以及如何构建因子。

对于因子，你可以显式地设置其水平。如果不设置，列解析器将按照列中不同字符串出现的顺序来设置水平。例如，在 `data/data.csv` 中，C 列和 D 列中的字符串顺序为 `a`、`b` 和 `c`：

```
A, B, C,   D
1, a, a,  1.2
2, b, b,  2.1
3, c, c, 13.0
```

默认情况下，这两列将被解释为字符型，但如果我们将 C 指定为因子，则会得到一个水平顺序为 `a`、`b`、`c` 的因子。

```r
my_data <- read_csv(
  file = "data/data.csv",
  col_types = cols(C = col_factor())
)
my_data$C
## [1] a b c
## Levels: a b c
```

如果我们希望水平按不同的顺序排列，可以给 `col_factor()` 一个 `levels` 参数。

```r
my_data <- read_csv(
"A, B, C
Foo, 12.4, Medium
Bar, 5.2,   Small
Baz, 42.0, Large
",
  col_types = cols(
    C = col_factor(levels = c("Small", "Medium", "Large"))
  )
)
my_data$C
## [1] Medium Small   Large
## Levels: Small Medium Large
```

我们还可以使用 `ordered` 参数将因子设置为有序因子。

```r
my_data <- read_csv(
  file = "data/data.csv",
  col_types = cols(
    B = col_factor(ordered = TRUE),
    C = col_factor(levels = c("c", "b", "a"))
  )
)
my_data$B
## [1] a b c
## Levels: a b c
my_data$C
## [1] a b c
## Levels: c b a
```

### 解析时间和日期

读取（或写入）时最复杂的类型是日期和时间（以及日期时间），仅仅是因为它们有多种不同的书写方式。你可以使用一个包含代码的字符串来指定日期和日期时间的格式，这些代码指示时间信息是如何表示的。

这些代码如下：

| 代码 | 时间格式 | 示例字符串 | 解释 |
| --- | --- | --- | --- |
| `%Y` | 4 位年份 | 1975 | 年份 1975 |
| `%y` | 2 位年份^(⁶) | 75 | 同样表示年份 1975 |
| `%m` | 2 位月份 | 02 | 二月 |
| `%b` | 缩写的月份名称^(⁷) | Feb | 二月 |
| `%B` | 完整的月份名称 | February | 二月 |
| `%d` | 2 位日期 | 15 | 某月的第 15 天 |
| `%H` | 24 小时制的小时数 | 18 | 下午六点 |
| `%I` | 12 小时制的小时数^(⁸) | 6 pm | 18:00 小时 |
| `%p` | 上午/下午指示符 | 6 pm | 18:00 小时 |
| `%M` | 两位数的分钟数 | 18:30 | 六点半 |
| `%S` | 整数秒数 | 18:30:10 | 18:00 过 10 秒 |
| `%Z` | 时区名称^(⁹) | America/Chicago | 中部时间 |
| `%z` | 相对于 UTC 的时区偏移量 | “+0100” | 欧洲中部时间 |

为常用格式提供了一些快捷方式：

| 快捷方式 | 格式 |
| --- | --- |
| `%D` | `%m/%d/%y` |
| `%x` | `%y/%m/%d` |
| `%F` | `%Y-%m-%d` |
| `%R` | `%H:%M` |
| `%T` | `%H:%M:%S` |

正如我们之前所见，你可以使用 `locale()` 函数设置日期和时间格式。如果不设置，默认代码会分别是针对日期的 `%AD` 和针对时间的 `%AT`（日期时间没有 `locale()` 参数）。这些代码分别指定了 `YMD` 和 `H:M`/`H:M:S` 格式，但在模式匹配方面更为宽松。例如，日期解析器允许使用不同的分隔符。对于日期，“1975-02-15”和“1975/02/15”都会被解读为 1975 年 2 月 15 日；对于时间，“18:00”和“6:00 pm”都会被解读为下午六点。

在下面的文字中，我将给出几个例子。我将使用 `parse_date`、`parse_time` 和 `parse_datetime` 函数，而不是带有列类型规范的 `read_csv`。当你指定日期、时间或日期时间列类型时，`read_csv` 内部会使用这些函数，但在这个例子中使用 `read_csv` 会显得过于冗长。每个函数都接受一个表示日期和时间的字符串向量。获取更多示例，你可以阅读函数文档 `?col_datetime`。

解析时间是最简单的；时间点的书写方式变化不大。主要区别在于你是使用 24 小时制还是 12 小时制。`%R` 和 `%T` 代码期望使用 24 小时制，区别在于是否包含秒数。

```r
parse_time(c("18:00"), format = "%R")
## 18:00:00

parse_time(c("18:00:30"), format = "%T")
## 18:00:30
```

没有用于 12 小时制的快捷方式，但你必须将 `%I` 与 `%p` 结合使用才能读取上午/下午格式。

```r
parse_time(c("6 pm"), format = "%I %p")
## 18:00:00
```

在这里，我指定了输入仅包含小时而不包含分钟。如果我们想在 24 小时制中只表示小时（而不包含分钟），则需要使用 `%H` 而不是 `%R`。

```r
parse_time(c("18"), format = "%H")
## 18:00:00
```

对于日期，ISO 8601 规定格式应为 `YYYY-MM-DD`。默认的日期解析器会接受这种格式，但显式的格式字符串是：

```r
parse_date(c("1975-02-05"), format = "%Y-%m-%d")
## [1] "1975-02-05"
```

如果你不想包含日期，并且想使用两位数的年份，你需要：

```r
parse_date(c("75-02"), format = "%y-%m")
## [1] "1975-02-01"
```

这是 1975 年 2 月；请记住，`%y` 代码假定大于 68 的数字属于 20 世纪。

日期以 `15/02/75` 形式书写时，根据你在世界上的位置，可能表示 1975 年 2 月 15 日，也可能表示 1975 年 5 月 2 日。欧洲使用合理的 `DD/MM/YY` 格式，顺序是从最小的时间单位（天）到中等的时间单位（月），再到年。在美国，他们使用 `MM/DD/YY` 格式。要得到 2 月 15 日，你需要使用以下格式之一：

```r
parse_date(c("15/02/75"), format = "%d/%m/%y")
## [1] "1975-02-15"

parse_date(c("02/15/75"), format = "%m/%d/%y")
## [1] "1975-02-15"
```

## 日期解析与本地化

仅使用数字的日期规范不受本地语言影响，但如果包含月份名称，则会受到影响。月份名称及其缩写因语言而异，这很明显。星期几的名称也是如此，但在撰写本文时，`readr` 暂不支持解析周和星期几。如果你使用语言代码，可以通过 `locale()` 获取名称信息。在以下示例中，我解析了英文和丹麦文的日期。月份名称几乎相同，但丹麦文的缩写后面需要跟一个点，并且日期后面也跟一个点。

```r
parse_date(c("Feb 15 1975"), format = "%b %d %Y", locale = locale("en"))
## [1] "1975-02-15"
parse_date(c("15. feb. 1975"), format = "%d. %b %Y", locale = locale("da"))
## [1] "1975-02-15"
parse_date(c("February 15 1975"), format = "%B %d %Y", locale = locale("en"))
## [1] "1975-02-15"
parse_date(c("15. feb. 1975"), format = "%d. %b %Y", locale = locale("da"))
## [1] "1975-02-15"
parse_date(c("Oct 15 1975"), format = "%b %d %Y", locale = locale("en"))
## [1] "1975-10-15"
parse_date(c("15. okt. 1975"), format = "%d. %b %Y", locale = locale("da"))
## [1] "1975-10-15"
parse_date(c("October 15 1975"), format = "%B %d %Y", locale = locale("en"))
## [1] "1975-10-15"
parse_date(c("15. oktober 1975"), format = "%d. %B %Y", locale = locale("da"))
## [1] "1975-10-15"
```

### 时区处理

“日期时间”可以通过组合日期和时间字符串来解析。此外，你还需要考虑时区。对于仅限日期和时间的情况，你可以忽略时区，但除非你确定永远不需要考虑时区，否则不应依赖默认时区（即 UTC）。¹⁰

你可以使用 `%z` 指定相对于 UTC 的时区，或者如果输入中给出了时区，使用 `%Z` 指定基于位置的时区；如果所有输入的时区都相同，你也可以使用 `locale()`。

如果你指定基于位置的时区，R 会自动调整夏令时；但如果使用相对于 UTC 的日期，则不会自动调整——UTC 没有夏令时。欧洲中部时间（CET）是“+0100”，夏令时为“+0200”。美国太平洋时间（PST）是“-0800”，夏令时（PDT）为“-0700”。在夏令时之间来回切换的时间由你的位置决定。

以下两个日期时间是相同的：

```r
parse_datetime(c("Feb 15 1975 18:00 US/Pacific"), format = "%b %d %Y %R %Z")
## [1] "1975-02-16 02:00:00 UTC"
parse_datetime(c("Feb 15 1975 18:00 -0800"), format = "%b %d %Y %R %z")
## [1] "1975-02-16 02:00:00 UTC"
```

以下两个也是相同的：

```r
parse_datetime(c("Jun 15 1975 18:00 US/Pacific"), format = "%b %d %Y %R %Z")
## [1] "1975-06-16 01:00:00 UTC"
parse_datetime(c("Jun 15 1975 18:00 -0700"), format = "%b %d %Y %R %z")
## [1] "1975-06-16 01:00:00 UTC"
```

如果你使用 `locale()` 来指定时区，则不能使用相对于 UTC 的时区。使用 `locale()` 的目的是本地格式，而非时区。不过，解析器仍然会为你处理夏令时。以下两个日期时间相同：

```r
parse_datetime(
  c("Aug 15 1975 18:00"),
  format = "%b %d %Y %R",
  locale = locale(tz = "US/Pacific")
)
## [1] "1975-08-15 18:00:00 PDT"
parse_datetime(
  c("Aug 15 1975 18:00 US/Pacific"),
  format = "%b %d %Y %R %Z"
)
## [1] "1975-08-16 01:00:00 UTC"
```

它们打印出来的结果不同，但正如我们之前所见，下午 6 点（`18:00`）PDT 与次日的 UTC 时间 `01:00` 相同。

如果你打印解析出的对象，使用 `locale()` 和使用 `%Z` 会有区别，但时间本身是相同的。使用 `%Z` 时，时间会自动转换为 UTC；使用 `locale()` 时则不会。但你可以比较两次调用的结果，并发现它们是等价的对象：

```r
x <- parse_datetime(
  c("Aug 15 1975 18:00"),
  format = "%b %d %Y %R",
  locale = locale(tz = "US/Pacific")
)
y <- parse_datetime(
  c("Aug 15 1975 18:00 US/Pacific"),
  format = "%b %d %Y %R %Z"
)
x == y
## [1] TRUE
```

`parse_datetime()` 的输出在打印时看起来像字符串，但对象的类并非 `character`，这也是比较能够正常工作的原因之一。

## 空格分隔的列

前面的函数都用于读取分隔符分隔的列。它们期望使用单个字符来分隔列。如果参数 `trim_ws` 为真，它们会忽略空白。该参数在 `read_csv`、`read_csv2` 和 `read_tsv` 中默认为真，但在 `read_delim` 中默认为假。

`read_table` 函数则通过一个或多个空格来分隔列：

```r
read_table(
  "A   B   C   D
  1   2   3   4
  15  16  17  18"
)
## # A tibble: 2 × 4
##       A     B     C     D
##   <int> <int> <int> <int>
## 1     1     2     3     4
## 2    15    16    17    18
```

与前面的函数一样，第一行被解释为表头，但你可以使用 `col_names = FALSE` 禁用它：

```r
read_table(
  "A   B   C   D
  1   2   3   4
  15  16  17  18",
  col_names = FALSE
)
## # A tibble: 3 × 4
##   X1    X2    X3    X4
##   <chr> <chr> <chr> <chr>
## 1 A     B     C     D
## 2 1     2     3     4
## 3 15    16    17    18
```

`read_table` 函数接受许多与 `read_csv` 或 `read_tsv` 相同的参数，主要区别在于它对列分隔符的定义——该函数使用空白而非特定的字段分隔符（如逗号或分号）。

`readxl` 包在加载 `tidyverse` 包时不会被加载，但它非常有用。其 `read_excel` 函数顾名思义，用于将 Excel 电子表格读入 R。其接口与 `readr` 中的函数类似。不同之处在于 Excel 特定的选项，例如要读取哪个工作表。这些选项显然仅在读取 Excel 文件时需要。

### 写入数据的函数

将数据写入文件比读取数据更直接，因为我们拥有正确类型的数据，无需处理不同格式。借助 `readr` 的写入函数，我们用于格式化输出的选项较少——例如，我们无法为函数提供 `locale()`，也无法指定日期和时间格式，但我们可以使用不同函数来指定分隔符，时间将按 ISO 8601 格式输出，这也是读取函数默认使用的格式。

相关函数包括 `write_delim`、`write_csv`、`write_csv2` 和 `write_tsv`，以及针对 Excel 可读格式的 `write_excel_csv` 和 `write_excel_csv2`。`write_csv` 与 `write_excel_csv` 之间的区别，以及 `write_csv2` 与 `write_excel_csv2` 之间的区别在于：Excel 函数会包含一个 UTF-8 字节顺序标记，以便 Excel 识别文件是 UTF-8 编码的。

这些函数的第一个参数是要写入的数据，第二个参数是目标文件路径。如果该文件的后缀为`.gz`、`.bz2`或`.xz`，输出将自动压缩。

此处我不会列出这些函数的所有参数，但你可以从R控制台中查阅它们的文档。你最可能用到的参数是`col_names`，如果设为`true`，函数会将列名作为输出文件的第一行写入；如果设为`false`，则不会。如果你使用`write_delim`，可能还需要通过`delim`参数指定分隔符。默认情况下，它是一个空格；如果你使用默认选项的`write_delim`写入文件，你将获得一种可以通过`read_table`读取的数据格式。

`write_csv`、`write_csv2`和`write_tsv`的分隔符和小数点与对应的读取函数相同。

## 表示表格：`tibble`

`readr`包返回的数据以`tibble`对象表示。这些是类似于基础R数据框的表格数据表示形式，但属于更现代的版本。

实现`tibble`的包是`tibble`。你可以通过以下方式加载它：

```r
library(tibble)
```

或者作为tidyverse的一部分：

```r
library(tidyverse)
```

## 创建 Tibble

创建表格数据的Tidyverse函数将生成`tibble`而非数据框。例如，当我们使用`read_csv`将文件读入内存时，结果就是一个`tibble`：

```r
x <- read_csv("some_file.csv")
x
## # A tibble: 3 × 4
##       A B     C         D
##   <int> <chr> <chr> <dbl>
## 1     1 a     a       1.2
## 2     2 b     b       2.1
## 3     3 c     c      13
```

`read_csv()`创建的表具有多个超类，但最后一个类是`data.frame`。

```r
class(x)
## [1] "spec_tbl_df" "tbl_df"    "tbl"
## [4] "data.frame"
```

这意味着，泛型函数如果未在其他类中特化，将使用`data.frame`版本，而这也意味着你通常可以在期望数据框的函数中使用`tibble`。但这并不表示你*始终*能用`tibble`替代数据框。如果遇到此问题，你可以使用`as.data.frame()`将`tibble`转换为数据框：

```r
y <- as.data.frame(x)
y
##   A B C     D
## 1 1 a a   1.2
## 2 2 b b   2.1
## 3 3 c c  13.0
class(y)
## [1] "data.frame"
```

注意，这两个对象在打印时输出不同。打印`tibble`时，会包含更多关于列类型的信息。

如果你有一个数据框，可以使用`as_tibble()`将其转换为`tibble`：

```r
z <- as_tibble(y)
z
## # A tibble: 3 × 4
##       A B     C         D
##   <int> <chr> <chr> <dbl>
## 1     1 a     a       1.2
## 2     2 b     b       2.1
## 3     3 c     c      13
```

你可以使用`tibble()`函数从向量创建`tibble`：

```r
x <- tibble(
  x = 1:100,
  y = x²,
  z = y²
)
x
## # A tibble: 100 × 3
##        x     y     z
##    <int> <dbl> <dbl>
##  1     1     1      1
##  2     2     4     16
##  3     3     9     81
##  4     4    16    256
##  5     5    25    625
##  6     6    36   1296
##  7     7    49   2401
##  8     8    64   4096
##  9     9    81   6561
## 10    10   100  10000
## # . . . with 90 more rows
```

这里需要注意两件事：当你打印一个`tibble`时，只会看到前10行。这是因为如果`tibble`行数足够多，打印全部会淹没控制台。如果一个`tibble`有超过20行，你只会看到前10行；如果行数更少，你将看到所有行。

你可以使用`print()`的`n`选项来更改显示的行数：

```r
print(x, n = 2)
## # A tibble:  100 × 3
##        x     y     z
##    <int> <dbl> <dbl>
## 1     1     1     1
## 2     2     4    16
## # . . . with 98 more rows
```

如果一个`tibble`的列数超出控制台显示范围，则只打印部分列。你可以使用`print`的`width`选项来更改打印的字符数。

```r
print(x, n = 2, width = 15)
## # A tibble:
## #   100 × 3
##       x     y
##    <int> <dbl>
## 1     1     1
## 2     2     4
## # . . . with 98
## #   more rows,
## #   and 1 more
## #   variable:
## #   z
```

你可以将任一选项设置为`Inf`。如果`n`为`Inf`，你将看到所有行；如果`width`为`Inf`，你将看到所有列。

第二件要注意的事情是，在指定后面的列时可以引用前面的列。当我们创建`x`时，我们使用了

```r
tibble(
  x = 1:100,
  y = x²,
  z = y²
)
```

其中列`y`引用了列`x`，列`z`引用了列`y`。你不能引用后续列中的变量，因此以下代码会报错：

```r
tibble(
  w = x/2,
  x = 1:100
)
```

当你使用`tibble()`创建数据框时，需要将列指定为命名参数。如果你像我之前那样缩进代码，可以将此视为每行定义一个列。你也可以按行创建`tibble`，每行对应一行数据。

```r
tribble(
  ~x, ~y, ~z,
  1, 10, 100,
  2, 20, 200,
  3, 30, 300
)
## # A tibble: 3 × 3
##       x     y     z
##   <dbl> <dbl> <dbl>
## 1     1    10    100
## 2     2    20    200
## 3     3    30    300
```

第一行命名列，名称前必须加`~`。对于大型`tibble`，使用`tribble()`并不是很有帮助，但对于示例代码或小型表格，则很方便。

## 索引 tibble

你可以像索引数据框（data frame）一样索引`tibble`。使用单括号索引（`[]`）可以通过列名或索引提取一列：

```
x["A"]
## # A tibble: 3 × 1
##       A
##   <dbl>
## 1     1
## 2     2
## 3     3
y["A"]
##   A
## 1 1
## 2 2
## 3 3
x[1]
## # A tibble: 3 × 1
##       A
##   <dbl>
## 1     1
## 2     2
## 3     3
y[1]
##   A
## 1 1
## 2 2
## 3 3
```

结果分别是一个包含单个列的`tibble`或`data.frame`。

如果使用双括号（`[[]]`），你将获得列中包含的向量，而不是`tibble`或data frame：

```
x[["A"]]
## [1] 1 2 3
y[["A"]]
## [1] 1 2 3
```

如果使用`$`索引，你也会获得列底层的向量：

```
x$A
## [1] 1 2 3
y$A
## [1] 1 2 3
```

使用`[]`，你可以提取多列。

## Tidy Select

所谓的“tidy select”并不是一个你单独使用的包（尽管你可以导入它，它叫做 `tidyselect`），而是一种用于在 `data frame` 或 `tibble` 中选择列的小型语言。后续章节中的许多包都支持这种语言，因此与其在每个章节中描述它，我决定将其放在自己的章节中，这里就是一个合适的位置。然而，如果没有后续章节中描述的包的功能，我们无法使用这种语言，因此我将使用 `dplyr` 包中的 `select` 函数；请参阅第 8 章。

`select` 函数帮助您从 `tibble` 或 `data frame` 中选择列。它的第一个参数是一个表格，因此我们可以将其与管道操作符一起使用，然后剩下的参数可以是我们想要提取的列。

```
tblselect(sample, min_size, min_weight)
## # A tibble: 2 × 3
## sample min_size min_weight
##            
## 1 foo         13      45.2
## 2 bar         12      83.1
```

这种简单的选择列方式本身就很有用，但相对于直接索引表格并没有增加太多功能。`tidy select` 的强大之处在于我们可以使用一种小型语言，而不是显式列出列名。

### 范围

如果你想要一个列的范围，例如从 `min_size` 到 `max_weight`，你可以使用 `:` 来选择这些列：

```
tbl|>select(min_size:max_weight)
## # A tibble: 2 × 4
## min_size max_size min_weight max_weight
##                    
## 1     13       16       45.2       67.2
## 2     12       17       83.1      102.
```

这只适用于连续的列，但如果你愿意，你也可以选择多个范围：

```
tbl|>select(sample:min_size, min_weight:max_weight)
## # A tibble: 2 × 4
##   sample min_size min_weight max_weight
##                    
## 1  foo         13       45.2       67.2
## 2  bar         12       83.1      102.
```

### 补集

有时，指定你*不*想要的列更容易，然后你可以使用补集运算符 `!`。这既适用于单个列的补集，即“除了这一列之外的所有内容”：

```
tbl|>select(!sample)
## # A tibble: 2 × 4
## min_size max_size min_weight max_weight
##                   
## 1     13       16       45.2      67.2
## 2     12       17       83.1     102.
```

但也适用于通过其他方式选择列，例如通过范围。如果 `sample:min_size` 选择了 `sample` 和 `min_size` 列，那么 `!sample:min_size` 就是除这些列之外的所有内容：

```
tbl|>select(!(sample:min_size))
## # A tibble: 2 × 3
##  max_size min_weight max_weight
##                 
## 1      16      45.2        67.2
## 2      17      83.1       102.
```

### 并集与交集

如果你同时包含列的补集和列本身，情况会有点奇怪，但你会得到所有你要求的列，所以使用

```
tbl|>select(sample,!(sample:min_size))
## # A tibble: 2 × 4
## sample max_size min_weight max_weight
##                  
## 1 foo        16      45.2        67.2
## 2 bar        17      83.1       102.
```

列 `sample`，然后我们得到所有不在范围 `sample:min_size` 内的列。

你可以使用 `&`（交集）和 `|`（并集）显式地请求选择的并集或交集。

```
tbl|>select(
sample:min_weight   # sample, min_size, max_size, and min_weight
&                   # 与以下范围取交集
max_size:max_weight # max_size, min_weight, max_weight
)
## # A tibble: 2 × 2
##   max_size min_weight
##           
## 1       16      45.2
## 2       17      83.1
tbl|>select(
sample:min_weight    # sample, min_size, max_size, and min_weight
|                    # 与以下范围取并集
max_size:max_weight  # max_size, min_weight, max_weight
)
## # A tibble: 2 × 5
##   sample min_size max_size min_weight max_weight
##                        
## 1 foo          13      16        45.2       67.2
## 2 bar          12      17        83.1      102.
```

### 按名称选择列

有多个函数允许你根据列名来选择列。`starts_with()` 函数会选择名称以特定字符串开头的列：

## dplyr中的整洁选择

```r
tbl|>select(starts_with("min"))
## # A tibble: 2 × 2
##   min_size min_weight
##             
## 1       13        45.2
## 2       12        83.1
```

类似地，`ends_with()` 函数允许你选择名称以特定字符串结尾的列：

```r
tbl|>select(ends_with("weight"))
## # A tibble: 2 × 2
##   min_weight max_weight
##              
## 1       45.2       67.2
## 2       83.1      102.
```

如果你想选择名称包含某个字符串的所有列，可以使用 `contains()` 函数：

```r
tbl|>select(contains("_"))
## # A tibble: 2 × 4
## min_size max_size min_weight max_weight
##                    
## 1     13       16       45.2       67.2
## 2     12       17       83.1      102.
```

对于更复杂的匹配，`matches()` 函数允许你基于正则表达式来选择列。这里，我将再次选择名称中包含下划线的列，因为在这个示例表中实际上没有更复杂的选择需求：

```r
tbl|>select(matches(".*_.*"))
## # A tibble: 2 × 4
## min_size max_size min_weight max_weight
##                    
## 1     13       16       45.2       67.2
## 2     12       17       83.1      102.
```

### 选择所有列

选择所有列似乎算不上什么功能，因为它只是返回原始表而已，但确实有一个函数 `everything()` 就是用来做这件事的。

```r
tbl|>select(everything())
## # A tibble: 2 × 5
##   sample min_size max_size min_weight max_weight
##                        
## 1 foo          13       16      45.2        67.2
## 2 bar          12       17      83.1       102.
```

当然，你不会在 `select()` 中使用这个函数，因为它什么也没做。但其他某些函数在对数据进行转换时需要你选择列，这时就可以使用 `everything()` 来选择数据框中的所有列。

### 从最后一列开始索引

如果你只想选择最后一列，可以使用 `last_col()` 函数：

```r
tbl|>select(last_col())
## # A tibble: 2 × 1
##   max_weight
##        
## 1       67.2
## 2      102.
```

你也可以使用该函数通过从右侧开始索引来选择列。如果给它一个整数参数 `i`，它会选择从右边数第 `i` 列：

```r
tbl|>select(last_col(0))
## # A tibble: 2 × 1
##   max_weight
##       
## 1      67.2
## 2     102.
```

```r
tbl|>select(last_col(1))
## # A tibble: 2 × 1
##   min_weight
##        
## 1       45.2
## 2       83.1
```

```r
tbl|>select(last_col(2))
## # A tibble: 2 × 1
##    max_size
##       
## 1        16
## 2        17
```

```r
tbl|>select(last_col(3))
## # A tibble: 2 × 1
##   min_size
##      
## 1       13
## 2       12
```

当然，你也可以在范围中使用它：

```r
tbl|>select(last_col(3):last_col())
## # A tibble: 2 × 4
##   min_size max_size min_weight max_weight
##                     
## 1       13       16       45.2      67.2
## 2       12       17       83.1     102.
```

### 从字符串中选择

如果你有一个包含要选择的列名的字符串向量：

```r
vars<-c("min_size","min_weight")
```

你可以使用 `all_of()` 或 `any_of()` 函数来选择它们：

```r
tbl|>select(all_of(vars))
## # A tibble: 2 × 2
##   min_size min_weight
##         
## 1       13    45.2
## 2       12    83.1
```

```r
tbl|>select(any_of(vars))
## # A tibble: 2 × 2
##   min_size min_weight
##           
## 1       13      45.2
## 2       12      83.1
```

这两个函数的区别在于，如果 `vars` 包含表中不存在的名称，`all_of()` 会将其视为错误，而 `any_of()` 则不会。

```r
varsselect(all_of(vars))
## Error in 'select()':
## ! Can't subset columns past the end.
## x Column 'foo' doesn't exist.
```

```r
tbl|>select(any_of(vars))
## # A tibble: 2 × 2
##   min_size min_weight
##         
## 1       13    45.2
## 2       12    83.1
```

### 根据内容选择列

也许最强大的选择函数是 `where`。你需要将一个函数作为参数传递给它，该函数会应用于每一列，函数返回 `TRUE` 的列将被选中。

例如，如果你只想要数值类型的列，可以使用 `where` 结合 `is.numeric`：

```r
tbl|>select(where(is.numeric))
## # A tibble: 2 × 4
## min_size max_size min_weight max_weight
##                 
## 1     13       16     45.2      67.2
## 2     12       17     83.1     102.
```

或者，如果你想要数值类型并且最大值大于 100 的列，你可以使用：

```r
tbl|>select(where(\(x)is.numeric(x)&&max(x)>100.0))
## # A tibble: 2 × 1
##   max_weight
##       
## 1      67.2
## 2     102.
```

（语法 `\(args) body` 是 R 4 引入的，作为 `function(args)` body 的简短形式，但含义相同）。

## 这是一种不断发展的语言，请留意变化

支持整洁选择的函数所用的语言正在演变和成长，因此当你读到本文时，可能已经可以做更多事情了。请查阅 `select` 函数的文档：

```r
?select
```

以获取最新的描述。