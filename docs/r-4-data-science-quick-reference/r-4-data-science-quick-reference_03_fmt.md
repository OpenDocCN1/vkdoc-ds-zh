# 5. 重新格式化表格：tidyr

即使只考虑表格数据，也仍然有许多不同的格式化方式。Tidyverse 中的包期望数据以所谓的“整洁数据”（这也是 Tidyverse 名称的由来）形式表示。`tidyr` 包可以帮助你将数据格式化为整洁数据。

你可以将 `tidyr` 作为 tidyverse 的一部分加载：

```r
library(tidyverse)
```

或者单独加载：

```r
library(tidyr)
```

## 整洁数据

整洁数据的基本特征是每个变量占据一列，每个观测值占据一行。变量和观测值等术语在统计学中应该很熟悉，但整洁数据的特性表明，你不应该将一个变量的值放在不同的列中，也不应该将多个观测值放在同一行中。

考虑像这样的数据：

```r
mean_income <- tribble(
  ~country,  ~'2001', ~'2002', ~'2003', ~'2004', ~'2005',
  "Numenor",  123456,  132654,  321646,  324156,  325416,
  "Westeros", 314256,  432165,  546123,  465321,  561423,
  "Narnia",   432156,  342165,  564123,  543216,  465321,
  "Gondor",   531426,  321465,  235461,  463521,  561423,
  "Laputa",   14235,   34125,   45123,   51234,    54321
)
```

我们可以想象这个表格包含了 21 世纪头五年五个（虚构）国家的平均收入。假设你感兴趣的是，在考虑不同国家的情况下，收入随时间是否存在某种模式。为此，你可能会考虑一个诸如 `income ~ country + year` 的公式，但数据的格式并不便于实现这一点。如果年份是可以在模型中使用的变量，那么不同观测值（即实际的年份）被分割到不同列中就成问题了。同样，如果每年的平均收入是一个变量，那么一个国家所有观测值都在同一行中也成问题。你无法使用 `income ~ country + year`，因为数据不是整洁的。

## 数据透视（Pivoting）

`pivot_longer()` 函数可以将多个列合并为两个列：一个**键**列和一个**值**列。键列将是一个离散变量，其值取自原始列名，而值列将包含原始列中的数据。

以平均收入数据框为例。我们希望将实际的年份作为 `year` 变量的不同取值，并将每年的收入设为另一个变量 `income`。我们可以通过调用参数如下的 `pivot_longer()` 来获得这样一个 tibble：

```r
mean_income |>
  pivot_longer(
    names_to = "year",
    values_to = "income",
    cols = c(`2001`, `2002`, `2003`, `2004`, `2005`)
  )
## # A tibble: 25 × 3
##    country  year  income
##    <chr>    <chr>  <dbl>
##  1 Numenor  2001  123456
##  2 Numenor  2002  132654
##  3 Numenor  2003  321646
##  4 Numenor  2004  324156
##  5 Numenor  2005  325416
##  6 Westeros 2001  314256
##  7 Westeros 2002  432165
##  8 Westeros 2003  546123
##  9 Westeros 2004  465321
## 10 Westeros 2005  561423
## # . . . with 15 more rows
```

第一个参数是来自 `mean_income` 的输入数据，我使用 `|>` 运算符来提供它。这与我直接将 `mean_income` 作为函数调用的第一个参数效果完全相同，但使用该运算符可以向读者表明 `mean_income` 是流入函数的数据，并因此与其他参数区分开来。

`names_to` 和 `values_to` 参数是两个新列的名称。在 `names_to` 列中，我们得到从中取值的那一列的列名；在 `values_to` 列中，我们得到这些列中对应的值。其余参数指定我们要合并哪些列。

在前面的示例中，我显式列出了所有要包含的列，但 `tidyr` 的函数支持整洁选择（tidy select）（参见第 4 章），因此你也可以使用列的范围，如下所示：

```r
mean_income |>
  pivot_longer(
    names_to = "year",
    values_to = "income",
    cols = '2001':'2005'
  )
## # A tibble: 25 × 3
##    country  year  income
##    <chr>    <chr>  <dbl>
##  1 Numenor  2001  123456
##  2 Numenor  2002  132654
##  3 Numenor  2003  321646
##  4 Numenor  2004  324156
##  5 Numenor  2005  325416
##  6 Westeros 2001  314256
##  7 Westeros 2002  432165
##  8 Westeros 2003  546123
##  9 Westeros 2004  465321
## 10 Westeros 2005  561423
## # . . . with 15 more rows
```

你可以使用多个范围：

```r
mean_income |>
  pivot_longer(
    names_to = "year",
    values_to = "income",
    cols = c('2001':'2002', '2004':'2005')
  )
```

但是范围指定仅在相关列连续时才有效。你还可以使用 `!` 来指定要合并的列的补集。例如，如果你想合并年份，你可以选择除 `country` 之外的所有列，而不是选择所有年份：

```r
mean_income |>
  pivot_longer(
    names_to = "year",
    values_to = "income",
    cols = !country
  )
## # A tibble: 25 × 3
##    country  year  income
##    <chr>    <chr>  <dbl>
##  1 Numenor  2001  123456
##  2 Numenor  2002  132654
##  3 Numenor  2003  321646
##  4 Numenor  2004  324156
##  5 Numenor  2005  325416
##  6 Westeros 2001  314256
##  7 Westeros 2002  432165
##  8 Westeros 2003  546123
##  9 Westeros 2004  465321
## 10 Westeros 2005  561423
## # . . . with 15 more rows
```

你可以在补集中指定多个列，并且可以将补集与范围结合使用。

通常，你可以将 `pivot_longer()` 与所有整洁选择函数结合使用来选择列，例如，你可以使用 `starts_with()`，或者将 `starts_with()` 与补集 `!` 或整洁选择语言允许的任何其他内容结合使用。

使用 `pivot_longer()` 函数时，你可以通过 `starts_with("2")` 选择所有以 "2" 开头的列。例如：

```r
mean_income|>pivot_longer(
  names_to ="year",
  values_to ="income",
  starts_with("2")
)
## # A tibble: 25 × 3
##    country  year  income
##    <chr>    <chr>  <dbl>
##  1 Numenor  2001  123456
##  2 Numenor  2002  132654
##  3 Numenor  2003  321646
##  4 Numenor  2004  324156
##  5 Numenor  2005  325416
##  6 Westeros 2001  314256
##  7 Westeros 2002  432165
##  8 Westeros 2003  546123
##  9 Westeros 2004  465321
## 10 Westeros 2005  561423
## # . . . with 15 more rows
```

你也可以使用负选择器 `!starts_with("c")` 来排除以 "c" 开头的列（例如 `country` 列），效果是选择其他所有列：

```r
mean_income|>pivot_longer(
  names_to ="year",
  values_to ="income",
  !starts_with("c")
)
## # A tibble: 25 × 3
##    country  year  income
##    <chr>    <chr>  <dbl>
##  1 Numenor  2001  123456
##  2 Numenor  2002  132654
##  3 Numenor  2003  321646
##  4 Numenor  2004  324156
##  5 Numenor  2005  325416
##  6 Westeros 2001  314256
##  7 Westeros 2002  432165
##  8 Westeros 2003  546123
##  9 Westeros 2004  465321
## 10 Westeros 2005  561423
## # . . . with 15 more rows
```

进入 `names_to` 列的列名将是字符串。如果你需要其他类型，例如逻辑值或数值，可以通过设置 `names_transform` 参数来实现。如果我们希望所有年份都表示为整数，可以这样做：

```r
mean_income|>pivot_longer(
  names_to ="year",
  values_to ="income",
  !country,
  names_transform =as.integer
)
## # A tibble: 25 × 3
##    country  year  income
##    <chr>    <int>  <dbl>
##  1 Numenor  2001  123456
##  2 Numenor  2002  132654
##  3 Numenor  2003  321646
##  4 Numenor  2004  324156
##  5 Numenor  2005  325416
##  6 Westeros 2001  314256
##  7 Westeros 2002  432165
##  8 Westeros 2003  546123
##  9 Westeros 2004  465321
## 10 Westeros 2005  561423
## # . . . with 15 more rows
```

因为所有年份都是整数，所以年份列现在包含整数。如果我们想分析这个数据集，很可能希望将年份解释为整数。

但是，如果我们想要因子类型，也可以这样做：

```r
mean_income|>pivot_longer(
  names_to ="year",
  values_to ="income",
  !country,
  names_transform =as.factor
)
```

如果你需要转换进入 `values_to` 列的值，还有一个类似的 `values_transform` 参数。

如果你使用的函数不期望整洁数据，而是期望在不同列中找到相关数据，则可以使用 `pivot_wider()` 函数将整洁数据转换为那种格式。它是 `pivot_longer()` 的反操作。^((12))

```r
tidy_income|>pivot_longer(
  names_to ="year",
  values_to ="income",
  !country
)
tidy_income|>pivot_wider(
  names_from ="year",
  values_from ="income"
)
## # A tibble: 5 × 6
##   country   `2001` `2002` `2003` `2004` `2005`
##   <chr>      <dbl>  <dbl>  <dbl>  <dbl>  <dbl>
## 1 Numenor   123456 132654 321646 324156 325416
## 2 Westeros  314256 432165 546123 465321 561423
## 3 Narnia    432156 342165 564123 543216 465321
## 4 Gondor    531426 321465 235461 463521 561423
## 5 Laputa     14235  34125  45123  51234  54321
```

`names_from` 和 `values_from` 参数分别选取用作列名的值和要放入这些列的值。它们与你用于 `pivot_longer()` 的 `names_to` 和 `values_to` 参数的参数相同，用于将结果转换回去。

## 复杂列编码

不同变量并不总是以独立列的形式呈现。例如，一个包含日期的列中，实际上可能同时包含日、月乃至年份信息。最理想的日期表现形式当然是日期对象，但为了举例说明，我们假设需要将日期拆分为“日”和“月”两列。你可以使用 `separate()` 函数来实现。

```r
tbl  
## 1  11     5
## 2  4      7
## 3  21     12
```

第一个参数是数据表，第二个参数是要拆分的列，`into` 参数则是一个列表，用于指定原始日期将被放入哪些列。

默认情况下，原始列会被移除，但如果你希望保留它，可以使用 `remove` 参数。

```r
separate(tbl, date, into = c("day", "month"), remove = FALSE)
## # A tibble: 3 × 3
##   date  day   month
##     
## 1 11/5  11    5
## 2 4/7   4     7
## 3 21/12 21    12
```

将字符串列 `date` 中的字段分离后，你会得到 `day` 和 `month` 两列，这两列仍然包含字符串。要将其转换为数值，可以使用 `convert` 参数。

```r
separate(tbl, date, into = c("day", "month"), convert = TRUE)
## # A tibble: 3 × 2
##    day   month
##     
## 1  11        5
## 2  4         7
## 3  21       12
```

默认情况下，列会以任何非字母数字字符作为分隔符进行拆分。这就是为什么数据中的 `/` 能被正确识别为日和月之间的分隔符。^(¹³) 如果你的分隔符是其他字符，可以使用 `sep` 参数。该参数接受一个正则表达式，列将根据此表达式进行拆分。例如，我们可以像这样按字母字符和空格进行拆分：

```r
tbl 
## 1 11    5
## 2 4     7
## 3 21    12
```

正则表达式 `([[:alpha:]]|[[:space:]])+` 指定了由（最外层）方括号内的字符类（我们放入了字母字符 `[:alpha:]` 和空格 `[:space:]`）组成的任意非零（由 `+` 指定）长度的序列。

此示例中的日期格式在实际应用中并不常见，但如果我们有以下格式的日期：

```r
tbl <- tribble(
  ~date,
  "11th of May",
  "4th of July",
  "21st of December"
)
```

我们很难将分隔符字符串与月份名称区分开来。如果你需要的分隔符不是一个简单的正则表达式，或许可以改用 `extract()` 函数替代 `separate()`。使用 `extract()`，你可以利用正则表达式分组来识别需要提取的字段。

你需要一个带有分组的表达式——即括号内的子表达式——这些分组匹配到的内容将作为值放入新列中。对于我们的日期，可以这样实现：

```
tbl |> extract(
  col = date,
  into = c("day", "month"),
  regex = paste0(
    "([[:digit:]]+)",        # 第一组，表示日
    "[[:alpha:][:space:]]+", # 需要忽略的内容；会匹配 "th of" 等
    "[[:space:]]",           # 月份名称前的最后一个空格
    "([[:alpha:]]+)"         # 第二组，表示月份
  )
)
## # A tibble: 3 × 1
##   date
##   
## 1 11th of May
## 2 4th of July
## 3 21st of December
```

对于`separate()`和`extract()`函数，你都可以生成两个以上的结果列。你可以为`into`参数提供两个以上的名称，当然，用于拆分和提取的表达式也应提供相应数量的值。

```
tbl |> extract(
  col = date,
  into = c("day", "fluf", "month"),
  regex = paste0(
    "([[:digit:]]+)",        # 第一组，表示日
    "([[:alpha:]]+)",        # st, nd, rd, th 这类内容放入 fluf 列
    "[[:alpha:][:space:]]+", # 日和月之间的内容；不是分组，因此丢弃
    "([[:alpha:]]+)"         # 第三组，表示月份
  )
)
## # A tibble: 3 × 1
##   date
##   
## 1 11th of May
## 2 4th of July
## 3 21st of December
```

拆分操作的反向操作是合并。例如，假设`tbl2`包含`day`和`month`两列。下面的代码构建了这样一个表格：

```
tbl2 <- separate(tbl, col = date, into = c("day", "month"))
```

在`tbl2`中创建这样一个 tibble 后，我们可以使用`unite()`函数来撤销`separate()`操作，就像我们发现`pivot_longer/wider()`可以互相撤销一样（当然，需要注意数据可能会丢失）。`col`参数应为我们新创建的列名，后续参数则是组成新列的原始列。

```
tbl2 |> unite(col = "date", day, month)
## # A tibble: 3 × 1
##   date
##   
## 1 11_5
## 2 4_7
## 3 21_12
```

默认情况下，新列中的分隔符是`_`。这对于日期来说并不合适，但我们可以使用`sep`参数来更改它。

```
tbl2 |> unite(col = "date", day, month, sep = "/")
## # A tibble: 3 × 1
##   date
##   
## 1 11/5
## 2 4/7
## 3 21/12
```

如果我们希望保留原始列，可以将`remove`参数设置为`FALSE`。

```
tbl2 |> unite(
  col = "date", day, month,
  sep = "/", remove = FALSE
)
## # A tibble: 3 × 3
##   date   day   month
##      
## 1 11/5   11    5
## 2 4/7    4     7
## 3 21/12  21    12
```

包含多个值的列，其值的数量不一定总是相同。例如，我们可能有这样的数据：第一次世界大战和第二次世界大战中各主要集团的伤亡人数。

```
military_casualties <- tribble(
  ~war, ~groups, ~deaths,
  'WW1',
  "Allied Powers/Central Powers",
  "5.7,4.0",
  'WW2',
  "Germany/Japan/USSR/British Empire/USA",
  "5.3,2.1,10.7,0.6,0.4"
)
```

两次战争中的分组并不相同，因此我们无法将数据拆分成不同的列。但是，我们可以使用`separate_rows()`将其拆分为更多的行。

```
military_casualties |> separate_rows(
  groups, deaths,
  sep = "/|,"
)
## # A tibble: 7 × 3
##   war   groups          deaths
##               
## 1 WW1   Allied Powers   5.7
## 2 WW1   Central Powers  4.0
## 3 WW2   Germany         5.3
## 4 WW2   Japan           2.1
## 5 WW2   USSR            10.7
## 6 WW2   British Empire  0.6
## 7 WW2   USA             0.4
```

我们在`sep`中使用的表达式表示按`/`或`,`进行拆分（字符串中的`|`是正则表达式中表示“或”的含义）。

我们可能不希望这里的`deaths`是字符串，但使用`convert = TRUE`可以将它们转换为数值：

```
military_casualties |> separate_rows(
  groups, deaths,
  sep = "/|,",
  convert = TRUE
)
## # A tibble: 7 × 3
##   war   groups         deaths
##              
## 1 WW1   Allied Powers  5.7
## 2 WW1   Central Powers 4
## 3 WW2   Germany        5.3
## 4 WW2   Japan          2.1
## 5 WW2   USSR           10.7
## 6 WW2   British Empire 0.6
## 7 WW2   USA            0.4
```

### 扩展、交叉和补全

对于某些应用场景，创建两个或多个列中所有值的组合（即使这些组合在数据中缺失）非常有用。为此，你可以使用`expand()`函数：

```
tbl %>% expand(A, B)
## # A tibble: 6 × 2
##       A     B
##   <int> <int>
## 1     1    11
## 2     1    13
## 3     2    11
## 4     2    13
## 5     4    11
## 6     4    13
```

这里，我们获取了列`A`和`B`中所有值的组合，而列`C`被忽略。如果某列包含的值来自一个比数据中实际值更大的集合，你可以向`expand()`传递一个值向量：

```
tbl %>% expand(A = 1:4, B)
## # A tibble: 8 × 2
##       A     B
##   <int> <int>
## 1     1    11
## 2     1    13
## 3     2    11
## 4     2    13
## 5     3    11
## 6     3    13
## 7     4    11
## 8     4    13
```

如果你有想以这种方式组合的向量，也可以使用`crossing()`函数。

```
crossing(A = 1:3, B = 11:13)
## # A tibble: 9 × 2
##       A     B
##   <int> <int>
## 1     1    11
## 2     1    12
## 3     1    13
## 4     2    11
## 5     2    12
## 6     2    13
## 7     3    11
## 8     3    12
## 9     3    13
```

如果你只想要数据中存在的组合，可以将`expand()`与`nesting()`函数结合使用：

```
tbl  
## 1     1    11
## 2     2    11
## 3     4    13
```

这种组合会为你提供所有指定列的组合（所有其他列被忽略），并且只返回唯一的组合，这与通过索引提取列不同。如果你的数据包含大量重复项，但只需计算唯一组合的某些统计量，这会非常有用。

如果你需要数据框中的所有组合，但允许缺失值出现在那些不存在的组合中，可以使用`complete()`函数。它确保你拥有所有组合，并为原始数据中不存在的组合在剩余列中放入缺失值。

```r
complete(tbl, A = 1:4)
## # A tibble: 6 × 3
##       A     B     C
##   <int> <int> <int>
## 1     1    11    21
## 2     2    11    22
## 3     2    11    12
## 4     3    NA    NA
## 5     4    13    42
## 6     4    13    32
complete(tbl, B = 11:13)
## # A tibble: 6 × 3
##       B     A     C
##   <int> <int> <int>
## 1    11     1    21
## 2    11     2    22
## 3    11     2    12
## 4    12    NA    NA
## 5    13     4    42
## 6    13     4    32
complete(tbl, A = 1:4, B = 11:13)
## # A tibble: 14 × 3
##       A     B     C
##   <int> <int> <int>
## 1     1    11    21
## 2     1    12    NA
## 3     1    13    NA
## 4     2    11    22
## 5     2    11    12
## 6     2    12    NA
## 7     2    13    NA
## 8     3    11    NA
## 9     3    12    NA
## 10    3    13    NA
## 11    4    11    NA
## 12    4    12    NA
## 13    4    13    42
## 14    4    13    32
```

### 缺失值

当数据存在缺失值时，通常需要应用领域的知识才能正确处理。`tidyr`包提供了一些基础功能用于清理包含缺失值的数据；你可能需要更多功能来妥善处理缺失值，而这些功能很可能在`dplyr`包中找到（参见第8章）。

处理缺失值最简单的方法是将其移除。一种粗略的做法是删除所有包含一个或多个缺失变量值的观测。`drop_na()`函数正是为此而生。

```r
mean_income           
## # A tibble: 5 × 5
##   country  `2002` `2003` `2004` `2005`
##   <chr>     <dbl>  <dbl>  <dbl>  <dbl>
## 1 Numenor  123456 132654     NA 324156
## 2 Westeros 314256     NA     NA 465321
## 3 Narnia   432156     NA     NA     NA
## 4 Gondor   531426 321465 235461 463521
## 5 Laputa    14235  34125  45123  51234
```

在这个例子中，如果任何一个年份有缺失数据，我们就会完全失去一个国家。这通常难以接受，也是支持整洁数据的又一个有力论据。如果我们重新格式化表格并删除缺失值，则只会移除那些特定年份收入缺失的观测。

```r
mean_income %>% pivot_longer(
  names_to = "year",
  values_to = "mean_income",
  -country
) %>% drop_na()
## # A tibble: 14 × 3
##    country  year  mean_income
##    <chr>    <chr>       <dbl>
## 1  Numenor  2002      123456
## 2  Numenor  2003      132654
## 3  Numenor  2005      324156
## 4  Westeros 2002      314256
## 5  Westeros 2005      465321
## 6  Narnia   2002      432156
## 7  Gondor   2002      531426
## 8  Gondor   2003      321465
## 9  Gondor   2004      235461
## 10 Gondor   2005      463521
## 11 Laputa   2002       14235
## 12 Laputa   2003       34125
## 13 Laputa   2004       45123
## 14 Laputa   2005       51234
```

有时，你可以用适当的数据替换缺失值。例如，我们可以将`mean_income`表中的缺失数据替换为每列的平均值。`replace_na()`函数可以做到这一点。它的第一个参数是一个数据框，第二个参数是一个指定替换值的列表。这个列表应为每个你想要替换缺失值的列设置一个名称，并且分配给这些名称的值应该是你希望用来替换的值。

```r
replace_na(mean_income, list(
  `2003` = mean(mean_income$`2003`, na.rm = TRUE)
))
## # A tibble: 5 × 5
##   country  `2002` `2003`  `2004` `2005`
##   <chr>     <dbl>  <dbl>   <dbl>  <dbl>
## 1 Numenor  123456 132654     NA  324156
## 2 Westeros 314256 162748     NA  465321
## 3 Narnia   432156 162748     NA      NA
## 4 Gondor   531426 321465  235461 463521
## 5 Laputa    14235  34125   45123  51234
replace_na(mean_income, list(
  `2003` = mean(mean_income$`2003`, na.rm = TRUE),
  `2004` = mean(mean_income$`2004`, na.rm = TRUE),
  `2005` = mean(mean_income$`2005`, na.rm = TRUE)
))
## # A tibble: 5 × 5
##   country  `2002` `2003` `2004` `2005`
##   <chr>     <dbl>  <dbl>  <dbl>  <dbl>
## 1 Numenor  123456 132654 140292 324156
## 2 Westeros 314256 162748 140292 465321
## 3 Narnia   432156 162748 140292 326058
## 4 Gondor   531426 321465 235461 463521
## 5 Laputa    14235  34125  45123  51234
```

在这个例子中，使用每年平均值可能并不实用；更合理的做法是用每个国家的平均值来替换缺失数据。不过，仅靠`tidyr`函数无法直接实现这一点。借助`dplyr`，我们拥有相应的工具，我将在第8章中再讨论这个问题。

最后一个用于处理缺失数据的函数是`fill()`函数。它会用列中上面的值（或者，如果将参数`.direction`设为`"up"`，则用下面的值）替换缺失值。假设我们有一个每年每季度收入的表格，但年份只在第一季度标明。

```r
tbl <- read_csv(
  "year, quarter, income
  2011, Q1, 13
  , Q2, 12
  , Q3, 14
  , Q4, 11
  2012, Q1, 12
  , Q2, 14
  , Q3, 15
  , Q4, 17",
  show_col_types = FALSE
)
spec(tbl)
## cols(
##   year = col_double(),
##   quarter = col_character(),
##   income = col_double()
## )
```

我们可以使用`fill()`将年份向下重复填充到各个季度：

```
fill(tbl, year)

```
## # A tibble: 8 × 3
##    year quarter income
##   <dbl> <chr>    <dbl>
## 1  2011 Q1          13
## 2  2011 Q2          12
## 3  2011 Q3          14
## 4  2011 Q4          11
## 5  2012 Q1          12
## 6  2012 Q2          14
## 7  2012 Q3          15
## 8  2012 Q4          17
```

## 嵌套数据

`tidyr` 提供的最后一项功能是嵌套数据。`tibble` 条目通常包含简单数据，例如数字或字符串，但也可以容纳复杂数据，例如其他表格。

考虑以下表格：

```r
tbl <- tribble(
~A, ~B, ~C,
1, 11, 21,
1, 11, 12,
2, 42, 22,
2, 15, 22,
2, 15, 32,
4, 13, 32
)
```

从这个表格中，我们可以创建一个新的表格，针对 `A` 列中的每个值，包含一个由该 `A` 值对应的 `B` 和 `C` 列组成的表格。

```r
nested_tbl <- tbl |> nest(BC = c(B, C))
nested_tbl
```

```
## # A tibble: 3 × 2
##       A BC
##    
## 1     1 
## 2     2 
## 3     4 
```

```r
nested_tbl[[1,2]]
```

```
## [[1]]
## # A tibble: 2 × 2
##       B     C
##    
## 1     11   21
## 2     11   12
```

包含嵌套表格的列被命名为 `BC`，因为我们使用了带有该名称的命名参数 `BC = c(B, C)`。

反向操作是 `unnest()`：

```r
nested_tbl |> unnest(cols = c(BC))
```

```
## # A tibble: 6 × 3
##       A     B     C
##     
## 1     1    11    21
## 2     1    11    12
## 3     2    42    22
## 4     2    15    22
## 5     2    15    32
## 6     4    13    32
```

嵌套在数据分析中不是典型操作，但如果你需要它，它就在那里，并且在第 12 章的包中，我们会看到一些用法。

脚注 1 2 3
```