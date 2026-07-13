

## 第 8 章 高级 `data.table`

我们已经介绍了 `data.table` 包，它是本章的核心，涵盖了在广义的数据管理范畴内访问、编辑和操作数据的基础知识。数据管理虽然不显眼，但却是数据可视化或分析的关键第一步。此外，特定分析项目的大部分时间通常都花在数据管理上。例如，一旦数据清洗完毕并符合预期格式，在 `R` 中运行线性模型只需一行代码。数据管理具有挑战性，因为原始数据的类型、形状和格式多种多样，并且缺失数据很常见。此外，您可能还需要合并或结合不同的数据源。在本章中，我们将超越 `data.table` 的基本用法，处理更复杂的数据管理任务。

数据整理通常有两种风格。一次性转换通常是手动的，因为如果代码不被重复使用（例如，更改一两个变量名或重命名数据文件以保持一致性），编写代码往往效率不高。另一方面，对于需要重复执行的操作（例如，重命名或标记数百个变量）或处理较大规模的数据，则更多地使用编程来进行数据管理。如果需要，请安装 `stringdist` 包 [23]；另一个需要的库是 `foreign` [15]：

```r
library(stringdist)
library(data.table)
library(foreign)
options(stringsAsFactors = FALSE,
        datatable.print.nrows = 20, ## 如果超过 20 行
        datatable.print.topn = 3,   ## 打印前 3 行和后 3 行
        digits = 2)                 ## 减少 R 打印的小数位数
```

© Matt Wiley and Joshua F. Wiley 2020
M. Wiley and J. F. Wiley, *Advanced R 4 Data Programming and the Cloud*,
[`doi.org/10.1007/978-1-4842-5973-3_8`](https://doi.org/10.1007/978-1-4842-5973-3_8#DOI)

### 8.1 数据清洗/整理

为了获得需要*编程*风格整理的数据，我们从全国儿童健康调查（2003 年）[4] 的网站 [`doi.org/10.3886/ICPSR04691.v1`](https://doi.org/10.3886/ICPSR04691.v1) 下载数据，这里我们选择了 *Stata* 格式。我们将文件夹放置在工作目录中的 `data/ch08/` 文件夹下，并使用 `read.dta()` 将数据导入 `R`。将其转换为数据表后，我们将键设置为识别号列 `IDNUMR`：

```r
d <- read.dta("data/ch08/ICPSR_04691/DS0001/04691-0001-Data.dta")
d <- as.data.table(d)
setkey(d, IDNUMR)
```

为了了解这个大数据集的结构（有超过 300 个变量），我们使用 `str()` 函数。我们抑制属性以专注于列名和每列内部的数据类型。设置 `strict.width = "cut"` 以强制执行我们之前设置的选项，并且我们只看前 20 列：

```r
str(d, give.attr = FALSE, strict.width = "cut", list.len = 20)
```

```
## Classes 'data.table' and 'data.frame': 102353 obs. of 301 variables:
## $ IDNUMR   : int  1 2 3 4 5 6 7 8 9 10 ...
## $ STATE    : Factor w/ 51 levels "1-AK","2-AL",..: 35 25 45 18 36 37..
## $ MSA_STAT : Factor w/ 4 levels "-2 - MISSING",..: 4 4 4 4 4 4 4 4 4..
## $ AGEYR_CH : int  12 1 10 7 9 11 9 15 17 1 ...
## $ TOTKIDS4 : Factor w/ 4 levels "1 - 1 CHILD",..: 1 1 1 3 4 2 3 3 1 ..
## $ AGEPOS4  : Factor w/ 5 levels "1 - ONLY CHILD",..: 1 1 1 4 2 2 3 2..
## $ S1Q01    : Factor w/ 8 levels "-4 - PARTIAL INTERVIEW",..: 6 5 6 5..
## $ RELATION : Factor w/ 6 levels "-2 - MISSING",..: 3 2 3 3 2 2 2 4 3..
## $ TOTADULT : Factor w/ 6 levels "-2 - MISSING",..: 4 3 2 3 2 3 3 2 4..
## $ EDUCATIO : Factor w/ 6 levels "-2 - MISSING",..: 4 4 4 4 3 4 4 3 4..
## $ PLANGUAG : Factor w/ 5 levels "-2 - MISSING",..: 2 2 2 2 2 2 2 2 2..
## $ S2Q01    : Factor w/ 11 levels "-4 - PARTIAL INTERVIEW",..: 5 5 5 ..
## $ S2Q02R   : int  59 29 49 45 96 64 51 64 65 33 ...
## $ HGHT_FLG : int  0 0 0 0 0 0 0 0 0 0 ...
## $ S2Q03R   : int  100 20 55 60 98 115 64 135 115 26 ...
## $ WGHT_FLG : int  0 0 0 0 0 0 0 0 0 0 ...
## $ BMICLASS : Factor w/ 5 levels "-2 - MISSING",..: 3 1 3 5 1 3 3 3 3..
## $ S2Q04    : Factor w/ 8 levels "-4 - PARTIAL INTERVIEW",..: 5 5 5 5..
## $ S2Q05    : Factor w/ 8 levels "-4 - PARTIAL INTERVIEW",..: 4 4 4 4..
## $ S2Q06    : Factor w/ 8 levels "-4 - PARTIAL INTERVIEW",..: 4 4 4 4..
```

# [列表输出已截断]

许多列包含以因子水平形式存储的缺失数据，而非使用 R 语言的 `NA` 来向 R 指示这些值为缺失。由于数据超过 10 万行，我们绝对需要自动化处理这些更改。根据研究文档，我们找到了那些需要重新编码为缺失的特定值。这些值具有一致的标签，但其具体数字会随变量中合法水平的数量而变化。我们使用 `table()` 函数生成变量中唯一值的频数表。

这对于查看唯一值以及熟悉数据并了解缺失量（例如，像 *education* 这样的变量缺失相对较少，而 BMI 类别则更多）都很有用。我们希望将部分 *interview（部分采访）、not in universe（不在范围内）、missing（缺失）、legitimate skip（合法跳过）、don‘t know（不知道）* 和 *refused（拒绝回答）* 重新编码为 `NA`。然而，标签并不完全相同；`EDUCATIO` 中代表“拒绝回答”的数字是 97，而 `S1Q01` 中则是 7：

```
table(d[, EDUCATIO])

##
## -2 - MISSING 1 - LESS THAN HIGH SCHOOL  
## 3 4661  
## 2 - 12 YEARS, HIGH SCHOOL GRADUATE 3 - MORE THAN HIGH SCHOOL  
## 21238 76022  
## 96 - DON'T KNOW 97 - REFUSED  
## 324 105  
```

```
table(d[, PLANGUAG])

##
## -2 - MISSING 1 - ENGLISH 2 - ANY OTHER LANGUAGE  
## 1 94380 7912  
## 3 - DON'T KNOW 4 - REFUSED  
## 51 9  
```

```
table(d[, BMICLASS])

##
## -2 - MISSING 1-UNDERWEIGHT  
## 19963 5921  
## 2-NORMAL WEIGHT 3-AT RISK OF OVERWEIGHT  
## 45650 12207  
## 4-OVERWEIGHT  
## 18612  
```

```
table(d[, S1Q01])

##
## -4 - PARTIAL INTERVIEW -3 - NOT IN UNIVERSE -2 - MISSING  
## 0 0 1  
## -1 - LEGITIMATE SKIP 1 - MALE 2 - FEMALE  
## 0 52554 49719  
## 6 - DON'T KNOW 7 - REFUSED  
## 14 65  
```

## 重新编码数据

除了通过编程方式查找需要重新编码为缺失的案例（例如，`S1Q01` 中的 `7 - REFUSED` 和 `EDUCATIO` 中的 `97 - REFUSED`）之外，我们还希望在将这些水平转换为缺失后，从因子中删除这些水平。我们可以通过使用正则表达式来搜索已知的字符串来解决这个问题。例如，我们知道无论 `REFUSED` 是以 `7 -`、`97 -` 还是其他内容开头，它总是以该词结尾。我们可以通过 `grep()` 函数使用正则表达式进行搜索，该函数返回匹配项的值或数值位置；或者使用 `grepl()` 函数，该函数返回一个逻辑向量（因此带字母 `l`），指示每个元素是否匹配。

### 注意

正则表达式是查找和匹配字符串模式的强大工具。在 R 中，`grep()` 和 `grepl()` 返回匹配预期模式的向量元素的位置或逻辑向量。结合替换操作，正则表达式有助于重新编码数据。

我们通过一个简单的示例来展示这两个函数的实际应用。第一个形式参数 `pattern` 是用于匹配的正则表达式，而 `x` 是需要搜索的数据。请注意逻辑向量和数值位置之间的区别。无论哪种方式，`x` 的第二和第四位置都包含我们要匹配的字母 `abc`：

```
grep(
  pattern = "abc",
  x = c("a", "abc", "def", "abc", "d"))
## [1] 2 4
```

```
grepl(
  pattern = "abc",
  x = c("a", "abc", "def", "abc", "d"))
## [1] FALSE  TRUE FALSE  TRUE FALSE
```

到目前为止，我们有了一个基本的正则表达式。我们可以将其应用于我们的数据，搜索 `REFUSED` 而非 `abc`，尽管尽可能精确总是好的。例如，假设某个变量包含 `1 - REFUSED TREATMENT`（1 - 拒绝治疗）、`2 - DID NOT REFUSED TREATMENT`（2 - 没有拒绝治疗）、`3 - REFUSED`（3 - 拒绝回答）（暂且忽略古怪的语法示例），其中 1 和 2 是有效响应，而 3 表示拒绝回答问题。如果搜索 `REFUSED`，则会匹配所有这些值。我们最终希望使用 `grepl()`，因为逻辑值可作为有用的索引来将特定案例设置为 `NA`。为确保在检查正则表达式准确性的同时抓取到正确的值，我们使用 `grep()` 并设置 `value = TRUE`，这会返回匹配的字符串。这让我们能轻松看到匹配的内容：

```
grep(
  pattern = "REFUSED",
  x = c(
    "1 - REFUSED TREATMENT",
    "2 - DID NOT REFUSED TREATMENT",
    "3 - REFUSED"),
  value = TRUE)
## [1] "1 - REFUSED TREATMENT"         "2 - DID NOT REFUSED TREATMENT"
## [3] "3 - REFUSED"
```

为了更精确，我们可以添加连字符（`-`），虽然这样更精确了，但仍然会出现误报：

```
grep(
  pattern = "- REFUSED",
  x = c(
    "1 - REFUSED TREATMENT",
    "2 - DID NOT REFUSED TREATMENT",
    "3 - REFUSED"),
  value = TRUE)
## [1] "1 - REFUSED TREATMENT" "3 - REFUSED"
```

更进一步，我们指定 `- REFUSED` 必须是字符串的最后一部分。也就是说，`- REFUSED` 后面不能再有任何内容。这通过在正则表达式中使用 `$` 来实现，表示模式字符串的*结尾*：

```
grep( pattern = "- REFUSED$",
  x = c(
    "1 - REFUSED TREATMENT",
    "2 - DID NOT REFUSED TREATMENT",
    "3 - REFUSED"),
  value = TRUE)
## [1] "3 - REFUSED"
```

现在，你的数据可能有所不同，因此我们需要多种方法来精确调整所需的匹配类型。正则表达式允许我们指定期望查找的内容。下面的模式虽然简短，但包含了很多信息。`[0-9]` 表示数字 0-9，`+` 表示前面的表达式应至少出现一次或多次。这段代码允许匹配像 `1` 和 `97` 这样的数字，但不会匹配空值，并且必须是数字。在至少一个数字之后，正则表达式指示我们期望找到 `- REFUSED`，然后通过美元符号指定的字符串结尾。我们扩展可能的值来匹配我们的 `x` 值，并精确指定模式，结果如下：

```
grep( pattern = "[0-9]+ - REFUSED$",
  x = c(
    "1 - TREATMENT REFUSED TREATMENT",
    "2 - DID NOT REFUSED TREATMENT",
    "3 - JACK - REFUSED",
    "4 - REFUSED",
    "97 - REFUSED",
    "- REFUSED"),
  value = TRUE)
## [1] "4 - REFUSED"  "97 - REFUSED"
```

作为最后一步，我们知道如果要在真实数据集中找到真正的 `REFUSED`，该字符串也是以数字开头的。由于它可能是负数，我们扩展之前的表达式，在字符串开头搜索零次或多次的负号运算符，后跟至少一次的数字。我们使用 `^` 表示字符串开头，使用 `*` 表示零次或多次。通过使用这个正则表达式，我们有助于防止匹配数据中的合法值，并错误地将它们转换为 `NA`。我们对所需内容非常具体，任何不匹配我们模式的内容都会被拒绝：

```
grep( pattern = "^[-]*[0-9]+ - REFUSED$",
  x = c(
    "1 - TREATMENT REFUSED TREATMENT",
    "2 - DID NOT REFUSED TREATMENT",
    "3 - JACK - REFUSED",
    "4 - REFUSED",
    "97 - REFUSED",
    "-97 - REFUSED",
    "- REFUSED",
    "TRICK CASE 4 - REFUSED"),
  value = TRUE)
```

## [1] "4 - REFUSED" "97 - REFUSED" "-97 - REFUSED"

默认情况下，正则表达式也是区分大小写的。完整地讲解如何使用正则表达式超出了本书的范围。我们推荐对正则表达式有全面了解需求的读者阅读 Jeffrey Friedl 所著的《精通正则表达式》（O’Reilly Media, 2006 年出版）。正则表达式在匹配和处理字符串数据时非常有用，因为它允许我们编码非常具体的搜索。

回到我们的数据，我们希望匹配的不仅仅是 `REFUSED` 这个单词。一种方法是创建多个单独的正则表达式并循环遍历，但这既繁琐又低效。相反，我们可以修改正则表达式，使其在某些位置支持不同的选项。这是通过使用管道符 `|` 来实现的，它起到*或*的作用，用来分隔不同的选项。在下面的代码中，我们保留了字符串开头的具体格式，但允许文本结尾为 `REFUSED` 或 `MISSING`：

```
grep(
  pattern = "ˆ[-]*[0-9]+ - REFUSED$|MISSING$",
  x = c(
    "1 - TREATMENT REFUSED TREATMENT",
    "2 - DID NOT REFUSED TREATMENT",
    "3 - JACK - REFUSED",
    "4 - REFUSED",
    "97 - REFUSED",
    "-97 - REFUSED",
    "-2 - MISSING",
    "- REFUSED",
    "TRICK CASE 4 - REFUSED",
    "4 - REFUSED HELP"),
  value = TRUE)
```

## [1] "4 - REFUSED" "97 - REFUSED" "-97 - REFUSED" "-2 - MISSING"

现在，我们已经准备好将我们想要的模式对应的变量替换为 `NA`。如你所见，正则表达式已经变得相当复杂。需要提醒的是，许多符号在正则表达式中都具有特殊含义。搜索特殊符号时需要特别小心。例如，你已经看到 `*` 用于表示零次或多次，而不是字面上的星号。要搜索字面字符，必须使用反斜杠 (`\`) 进行转义。我们现在来构建一个模式，用于识别我们希望在儿童健康数据集中看到的所有缺失值。为了提高可读性，我们将模式单独提取出来，并在我们虚构的 `x` 数据上运行最后一次测试。

值得注意的是，分配给 `p` 的正则表达式为了适应书籍排版而加入了换行符，但在实际编写时，它应该写在一行中，并且在 `NOT IN UNIVERSE$` 部分之前不应该有空格或换行符：

```
p <- "ˆ[-]*[0-9]+ - REFUSED$|MISSING$|DON'T KNOW$|LEGITIMATE SKIP$|PARTIAL
INTERVIEW$|NOT IN UNIVERSE$"

grep( pattern = p,
  x = c(
    "1 - TREATMENT REFUSED TREATMENT",
    "2 - DID NOT REFUSED TREATMENT",
    "3 - JACK - REFUSED",
    "4 - REFUSED",
    "97 - REFUSED",
    "-97 - REFUSED",
    "-2 - MISSING",
    "96 - DON'T KNOW",
    "-4 - LEGITIMATE SKIP",
    "-3 - PARTIAL INTERVIEW",
    "-2 - NOT IN UNIVERSE",
    "- REFUSED",
    "TRICK CASE 4 - REFUSED",
    "4 - PARTIAL INTERVIEW OF DOCTOR"),
  value = TRUE)
```

## [1] "4 - REFUSED" "97 - REFUSED"

## [3] "-97 - REFUSED" "-2 - MISSING"

## [5] "96 - DON'T KNOW" "-4 - LEGITIMATE SKIP"

## [7] "-3 - PARTIAL INTERVIEW" "-2 - NOT IN UNIVERSE"

现在我们知道了如何找到需要设置为缺失值的案例，是时候行动了！

我们不必逐个输入每个变量，而是在 `data.table` 中循环遍历它们，将匹配的案例设置为 `NA`。如果变量是因子类型，我们使用 `droplevels()` 来移除未使用的因子水平，否则保持原样。要替换每个变量，我们以一种新的方式使用上一章介绍的 `:=` 操作符。之前，你已经看到如何在数据表中创建变量，如 `dat[, NewVar := value]`。现在我们要一次性替换多个变量。为此，我们在赋值操作符 `:=` 的左侧传递一个变量名向量，在右侧传递一个值列表。因为这些变量已经存在于数据集中，我们是在覆盖它们，而不是创建新的。变量名存储在向量 `v` 中。我们将 `v` 括在括号内，这样 `data.table` 就会将 `v` 视为一个包含变量名向量的 R 对象名称。如果我们没有将 `v` 括在括号内，`data.table` 会尝试将结果赋值给一个名为 `v` 的变量。

下一个挑战是选择变量。我们通常在数据表中输入不带引号的变量名，但我们的变量名存储在向量 `v` 中。为了实现这一点，我们利用 `data.table` 中的一个内部对象 `.SD`，它本质上是数据表的一个列表版本。但是，我们不想遍历数据表中的每一个变量。虽然 `.SD` 默认包含数据表中的所有变量（或列），但我们可以通过使用 `.SDcols` 参数，让 `.SD` 只包含数据表中变量子集。`.SDcols` 是一个形式参数，它接受一个字符向量，并用这个向量来控制数据表中内部 `.SD` 对象中包含的变量。

因此，我们通过编写 `.SDcols = v` 来指定要在 `.SD` 中包含的变量。这个操作不会返回任何易于阅读的值，因此我们转而查看 `EDUCATIO` 和 `S1Q01` 这两个变量：

```
v <- c("EDUCATIO", "PLANGUAG", "BMICLASS", "S1Q01", "S2Q01")
d[, (v) := lapply(.SD, function(x) {
  x[grepl(pattern = p, x)] <- NA
  if (is.factor(x)) droplevels(x) else x
}), .SDcols = v]
```

```
table(d[, EDUCATIO])
```

##

## 1 - LESS THAN HIGH SCHOOL 2 - 12 YEARS, HIGH SCHOOL GRADUATE

## 4661 21238

## 3 - MORE THAN HIGH SCHOOL

## 76022

```
table(d[, S1Q01])
```

##

## 1 - MALE 2 - FEMALE

## 52554 49719

请注意，由于在数据表中运行函数时每次都会复制数据，因此存在一些效率低下的问题。更高效的方法是在 `data.table` 中选择相关的行，然后将这些行设置为缺失值。这里的难点在于我们的结果是因子类型，我们想要丢弃多余的因子水平或将其强制转换为字符类型。我们一开始并没有将它们强制转换为因子类型；这些数据在从 Stata 数据文件导入时就已经是这个格式了。这仅仅是处理我们所获数据的一部分工作。如果我们所有的数据都是字符类型，我们可能会使用略微高效一些的流程。如果我们愿意，我们也可以在一开始就将所有因子转换为字符类。

### 注意

`gsub()` 函数将正则表达式匹配与替换功能结合在一起，它能将字符串中匹配正则表达式的部分替换为新文本，其形式如下：`gsub(pattern = "regex", replacement = "new values", x = "original data")`.

我们可能需要的另一个重新编码任务是去除数字，只保留标签。

这可以使用 `gsub()` 函数来完成，该函数在字符串内执行正则表达式匹配，然后将匹配项替换为指定的值（可以是零长度字符串）。我们先看下面这个简单的例子：

```
gsub(
  pattern = "abc",
  replacement = "",
  x = c("a", "abcd", "123abc456"))
```

## 重新编码数值

由于数值数据具有更系统的结构，对其重新编码比重新编码字符串数据更容易。为了在数值数据中指示不同类型的“缺失”，人们经常使用超出范围的值，例如负数或非常大的数字（如 `999`）。从数据角度来看，编码不同类型的缺失值是有意义的，因为这些代码提供了额外的信息（例如，跳过了问题、问题不适用）。然而，从实际使用的角度来看，我们通常希望忽略所有缺失数据（例如，在计算平均值时，如果数据中使用 `-2` 表示父母拒绝报告孩子年龄，或 `-3` 表示父母忘记，都会影响输出结果）。我们可以使用 `data.table` 包中的 `%between%` 运算符来查找落在某个范围内的值，并通过使用否定运算符 `!` 来定位补集。
例如，对于身高，文档指示使用零或负值以及大于 `90` 的值来编码各种类型的缺失。类似地，对于体重，使用零或负值以及大于 `900` 的值来编码不同类型的缺失值。要让 R 知道这些值表示缺失，我们需要重新编码它们。从我们的 `table()` 数据中，我们可以看到确实存在一些缺失值，并且可以确切地看到用于编码它们的值（`-2`、`96`、`96`、`996`、`997`）：
```
table(d[!S2Q02R %between% c(0, 90), S2Q02R])
```
```
##
##   -2   96   97
##    4 8096  131
```
```
table(d[!S2Q03R %between% c(0, 900), S2Q03R])
```
```
##
##   -2  996  997
##    4 2375   80
```
同样，我们可以逐个处理这些变量。但是，对于许多变量来说，这样做会变得繁琐。常见的模式是，如果有效值小于 `9`，那么 `9` 就是缺失值。如果值达到两位数，那么 `90` 就是缺失值，依此类推。我们通过检查最大值并相应地设置边界来自动化重新编码过程。通过以下代码和图 8-1 可以查看变量原始的样子。
```
par(mfrow = c(1,2))
hist(d$S2Q02R)
hist(d$S2Q03R)
```
我们重点关注三个需要进行数值重新编码的列。对于每个变量，我们循环遍历列表中的列名。将 `m` 设为我们尝试的最大值，首先将 `j` 和 `i` 设为 `1`。只要 `j` 等于 `1` 且 `i` 小于或等于要尝试的值的数量（例如 `9`、`99`、`999`），我们就继续执行下一步计算。如果变量的最大值（忽略缺失值）`k` 小于第 `i` 个 `m` 值的最大值，则将 `j` 设为 `0`（这会中断循环），并将任何超出 `0` 到第 `i` 个 `m` 之间范围的值替换掉。如果第 `i` 个 `m` 大于 `90` 或减去某个极小值，则将该变量设为缺失值（`NA_integer_`）。本质上，我们是按列遍历列表，并检查整个列中的最大值是否是我们最大值 `m` 之一。一旦找到匹配项，我们就会确保将 `9` 到 `10`、`90` 到 `100` 或 `900` 到 `1000` 范围内的值设为缺失值。注意，我们使用 `NA_integer_` 而不是 `NA`，因为我们是在替换现有变量中的子集值。`data.table` 要求用于替换现有变量中值的数据类别与该变量的类别相匹配。我们之前可以使用 `NA`，因为那时我们是覆盖整个变量，而不是替换变量中的部分选定值。
```
v2 <- c("S2Q02R", "S2Q03R", "AGEYR_CH")
m <- sort(c(9, 99, 999))

for (k in v2) {
  j <- i <- 1
  while(j == 1 & i <= length(m)) {
    if (max(d[[k]], na.rm = TRUE) < m[i]) {
      j <- 0
      d[!(get(k) %between% c(0, ifelse(m[i] > 90, m[i] - 9, m[i] - 1e-9))), (k) := NA_integer_]
    } else {
      i <- i + 1
    }
  }
}
```
`d$S2Q`的直方图`d$S2Q`的直方图

![图 8-1](https://via.placeholder.com/400x200?text=直方图)

`图 8-1.`接近 `>900` 和 `>90` 的异常值`

我们通过使用与之前相同的直方图函数来展示重新编码后的结果，如图 8-2 和图 8-3 所示。显然，重新编码后 x 轴的范围更窄了：
```
hist(d$S2Q02R)
hist(d$S2Q03R)
```
至此，我们关于数据卫生的章节就结束了。接下来，我们将展示如何创建新变量以简化分析过程。从数据科学的角度来看，变量创建有时被称为特征创建。在社会科学和行为研究的许多领域中，问卷或调查被普遍使用。在教育领域，考试常被实施。无论数据来自考试、问卷还是调查，一个常见的任务是将对单个项目或变量的回答进行汇总，以创建量表得分、总体测试得分或其他一些汇总指标。

## 创建新变量

一份问卷询问医生或健康专业人士是否曾告诉受访者，其焦点孩子患有九种可能病症中的任何一种（例如，哮喘、ADD 或 ADHD、抑郁或焦虑问题、糖尿病、发育迟缓或身体障碍）。我们的目标是创建复合变量，以捕捉一系列问题。
有些问题仅适用于年龄较大的孩子，有些受访者可能不知道答案，或者拒绝回答。因此，我们可以计算“是”回答的平均数量，从而得出每位受访者所有有效回答中“是”回答的比例。
我们首先创建一个变量列表，用于提取这九列数据。然后，为了获取实际的变量，我们通过 `unlist()` 将它们从数据表中取出，以查看所有可能的回答，这些回答可能需要清理以简化为“是/否”场景。请注意，在以下代码中，存在几种缺失数据的情况：合理的跳过、不知道以及拒绝回答：
```
v.health <- paste0("S2Q", c(19, 20, 21, 22, 23, 24, 26, 35, 37))
v.health
```
```
## [1] "S2Q19" "S2Q20" "S2Q21" "S2Q22" "S2Q23" "S2Q24" "S2Q26" "S2Q35"
## [9] "S2Q37"
```
```
table(unlist(d[, v.health, with = FALSE]))
```
```
##
##  -4 - 部分访谈   -3 - 不在范围内     -2 - 缺失
##                        0                        0                      120
## -1 - 合理跳过          0 - 否              1 – 是
##                  48388               833454                37720
## 6 - 不知道          7 - 拒绝回答
##                        0                        0
```
`1362 133`

# 第 8 章：高级 `data.table` 用法

**`d$S2Q02R` 的直方图**

y

频数

10 20 30 40 50 60 70 80

`d$S2Q02R`

**图 8-2.** 异常值已消失，因为直方图会忽略缺失值

**`d$S2Q03R` 的直方图**

y

频数

100 150 200 250

`d$S2Q03R`

**图 8-3.** 异常值已消失，因为直方图会忽略缺失值

我们已经知道如何使用正则表达式和 `grep()` 来清理这类变量。清理完成后，我们使用参数 `useNA = "ifany"` 在表中检查所有响应，包括`NA`值。需要注意的是，尽管为了适应书籍排版，正则表达式进行了换行，但在实际编写时，它应该是一个单行，并且在 `NOT IN UNIVERSE$` 部分之前不应有空格或换行：

```
p <- "ˆ[-]*[0-9]+ - REFUSED$|MISSING$|DON'T KNOW$|LEGITIMATE SKIP$|PARTIAL
INTERVIEW$|NOT IN UNIVERSE$"
```

```
d[, (v.health) := lapply(.SD, function(x) {
  x[grepl(pattern = p, x)] <- NA
  if (is.factor(x)) droplevels(x) else x
}), .SDcols = v.health]
```

```
table(unlist(d[, v.health, with = FALSE]), useNA = "ifany")
## 
## 0 - NO   1 - YES   <NA> 
## 833454   37720    50003
```

现在我们可以创建一个新变量，来表示“是”响应（`YES`）的平均值。为了按行计算平均值，我们使用 `rowMeans()` 函数。我们利用逻辑值存储为 `FALSE = 0` 和 `TRUE = 1` 这一事实。因此，我们测试字符数据是否等于 `1 - YES`，这会返回一个布尔值，然后取这些逻辑值的行均值。我们通过设置 `na.rm = TRUE` 来忽略（剔除）缺失值：

```
d[, HealthConditions := rowMeans(d[, v.health, with = FALSE] == "1 - YES", na.rm = TRUE)]
```

```
table(round(d$HealthConditions, 2),
      useNA = 'ifany')
## 
##     0  0.11  0.12  0.14  0.17   0.2  0.22  0.25  0.29  0.33  0.38 
## 77426 16116   239    20     5   676  4386   156    11  1933    75 
##   0.4  0.43  0.44   0.5  0.56  0.57   0.6  0.62  0.67  0.71  0.75 
##    81    12   768    51   264     3     3    13    80     1     5 
##  0.78  0.88  0.89   NaN 
##    19     1     3     6
```

我们可以遵循类似的过程来获取行计数。然而，查看计数时，发现出现了一些状况：没有缺失值了！这是因为在使用 `rowSums()` 时，当 `na.rm = TRUE` 且某行没有有效数据时，其和为零，而非缺失值。为了纠正这一点，我们需要手动将所有值都缺失的行/案例设置为缺失：

```
d[, NHealthConditions := rowSums(d[, v.health, with = FALSE] == "1 - YES", na.rm = TRUE)]
```

```
table(d$NHealthConditions, useNA = 'ifany')
## 
##     0     1     2     3     4     5     6     7     8 
## 77432 17068  4630  2018   820   278    84    20     3
```

我们计算每个受访者缺失响应的数量，选择缺失数量等于总变量数的行，并将这些行设置为缺失。再次注意，当替换 `data.table` 中变量的一个子集值时，缺失值的类型应与变量的类型匹配（例如，整数型用 `NA_integer_`，数值型用 `NA_real_`，字符型用 `NA_character_`，逻辑型用 `NA`）。这样我们就能得到更好的计数：

```
d[, NMissHealthConditions := rowSums(is.na(d[, v.health, with = FALSE]))]
d[NMissHealthConditions == length(v.health), NHealthConditions := NA_integer_]
```

```
table(d$NHealthConditions, useNA = 'ifany')
## 
##     0     1     2     3     4     5     6     7     8  <NA> 
## 77426 17068  4630  2018   820   278    84    20     3     6
```

使用 `rowMeans()` 或 `rowSums()` 创建新变量是一种相对优雅的解决方案。然而，这并不是一种很 `data.table` 的做法。计算是在原始的 `data.table` 之外进行的；我们第二次调用了对象 `d`，并为其子集化列以供使用。当第二次调用数据对象时，我们失去了 `data.table` 的许多特性，例如仅对数据的某些子集执行操作或按某个分组因子执行操作。

如果忽略缺失值不是问题，我们可以通过使用 `Reduce()` 函数直接在 `data.table` 中轻松处理。`Reduce()` 将一个函数（通常是二元运算符）作为其第一个参数，并将一个向量或参数列表作为第二个参数。我们在此展示向量和列表在加法、除法和幂运算中的示例：

```
Reduce('+', c(1, 2, 3))
## [1] 6
```

```
Reduce('+', list(1:3, 4:6, 7:9))
## [1] 12 15 18
```

```
Reduce('*', list(1:3, 4:6, 7:9))
## [1]  28  80 162
```

```
Reduce('ˆ', list(1:3, 4:6, 3:1))
## [1]    1 1024  729
```

```
Reduce('-', list(1:3, 4:6, 7:9))
## [1] -10 -11 -12
```

```
Reduce('/', list(1:3, 4:6, 7:9))
## [1] 0.036 0.050 0.056
```

这种方法可以轻松地应用到`data.table` 中的变量上。我们不能直接对健康变量进行加法运算，因为它们是因子类的数据。不过，我们可以编写一个函数来处理这个问题：

```
fplus <- function(e1, e2) {
  if (is.factor(e1)) {
    e1 <- as.numeric(e1) - 1
  }
  if (is.factor(e2)) {
    e2 <- as.numeric(e2) - 1
  }
  e1 + e2
}
```

我们通过 `.SDcols` 选择列，然后 `.SD` 将是一个变量列表，我们可以对其进行归约，并将结果存储在新变量中：

```
d[, NHealthConditions2 := Reduce(fplus, .SD), .SDcols = v.health]
```

```
table(d$NHealthConditions2)
## 
##     0     1     2     3     4     5     6     7     8 
## 65459 16116  4386  1927   768   264    79    19     3
```

## 8.3 模糊匹配

近似字符串匹配，或 *模糊* 匹配，是一种将观测值与参考列表进行链接的技术。例如，你可能有一个注册用户列表，然后个人在签到表上写下自己的名字。或者，你可能正在处理应与某些信息片段匹配的文件名。

我们从两个列表开始；参考名称是我们的注册用户列表。这些是我们信任并希望与假设的签到表中快速填写的观测名称进行匹配的名字。用户可能参加了不止一个活动，因此可能多次出现，也可能根本不出现。另外请注意，其中一些单词之间有双空格，这是匹配后续输出所必需的：

```
reference.Names <- c("This Test", "Test Thas", 
                     "Jane Mary", "Jack Dun-Dee")
observed.Names <- c("this test", "test this", "test that", 
                    "JaNe Mary.", "Mary Sou", "Jack Dee", "Jane Jack")
```

挑战在于找出注册用户参加的活动数量（即签署了签到表的数量）。可以通过使用 `stringdist` 包中的 `stringdistmatrix()` 函数进行初步筛选。我们使用 D-L 距离（Damerau-Levenshtein distance）方法，该方法主要计算将一个字符串转换为另一个字符串需要更改的字符数。由此，我们可以看出每个观测名称需要更改的最小字符数，以便匹配我们的一个参考名称。对于这个测试，匹配第一个参考名称需要更改两个字符，匹配第二个参考名称需要更改八个字符，以此类推。请注意，默认情况下，`stringdistmatrix()` 是区分大小写的，因此转换大小写算作一次更改。注意，该矩阵为我们提供了匹配的网格，其中矩阵元素就是距离：

```
stringdistmatrix(reference.Names, 
                 observed.Names, 
                 method = "dl")
##      [,1] [,2] [,3] [,4] [,5] [,6] [,7]
## [1,]    2    8    7   10    8    7    8
## [2,]    8    3    3    9    8    8    8
## [3,]    8    8    8    3    7    6    3
## [4,]   11   11   11    9   10    4    9
```

如果我们只想要匹配结果，可以使用近似匹配。它的参数类似，但还需要指定最大容许距离，超过该距离则不视为匹配。注意，它返回的是参考名称向量中的位置索引：

```
amatch(observed.Names, 
       reference.Names, 
       method = "dl", maxDist = 4)
```

## [1] 1 2 2 3 NA 4 3

我们可以稍作扩展，通过 `tolower()` 转换为小写或通过 `toupper()` 转换为大写，使其不区分大小写。此外，我们还可以移除一些不应成为匹配项的文本，例如各种标点符号。这两种方法都有可能让我们总体上得到更低的距离值。尽管在我们的例子中没有需要移除的句点，但可以使用之前学过的 `gsub` 方法来执行其他文本清洗操作。

```
stringdistmatrix(
  tolower(reference.Names),
  tolower(observed.Names),
  method = "dl")
##      [,1] [,2] [,3] [,4] [,5] [,6] [,7]
## [1,]    0    6    5   10    8    7    8
## [2,]    6    1    1    9    8    8    8
## [3,]    8    8    8    2    7    6    3
## [4,]   11   11   11    9   10    4    9
```

```
stringdistmatrix(
  tolower(gsub("\\.", "", reference.Names)),
  tolower(gsub("\\.", "", observed.Names)), method = "dl")
##      [,1] [,2] [,3] [,4] [,5] [,6] [,7]
## [1,]    0    6    5    9    8    7    8
## [2,]    6    1    1    8    8    8    8
## [3,]    8    8    8    1    7    6    3
## [4,]   11   11   11    9   10    4    9
```

# 第 8 章 进阶 `data.table`

我们可以使用 `strsplit()` 通过特定字符来拆分字符串。可以使用任何模式或字符，但这里我们使用空格。我们在正则表达式中使用反斜杠 s（`\s`）作为特殊字符，表示任何类型的空格。需要第二个反斜杠来转义第一个反斜杠。这是因为特殊字符并不是 `s`（其转义形式为 `\s`），而是复合字符 `\s` —— 因此，转义后的版本是 `\\s`。我们在以下代码中演示了 `strsplit()` 的结果：

```r
strsplit(
  x = reference.Names[1],
  split = "\\s")[[1]]
## [1] "This" "Test"
```

```r
strsplit(
  x = observed.Names[2],
  split = "\\s")[[1]]
## [1] "test" "this"
```

现在我们可以再次使用 `stringdistmatrix()`。结果显示，第二个观测名称的每个子块与第一个参考名称的某个子块之间只差一个字符操作：

```r
stringdistmatrix(
  strsplit(
    x = reference.Names[1],
    split = "\\s")[[1]],
  strsplit(
    x = observed.Names[2],
    split = "\\s")[[1]],
  method = "dl")
##      [,1] [,2]
## [1,]    4    1
## [2,]    1    4
```

如果我们忽略大小写，就能得到完美匹配。观测名称 2 的第二个块匹配参考名称 1 的第一个块，而观测名称 2 的第一个块完美匹配参考名称 1 的第二个块：

```r
stringdistmatrix(
  strsplit(
    x = tolower(reference.Names[1]),
    split = "\\s")[[1]],
  strsplit(
    x = tolower(observed.Names[2]),
    split = "\\s")[[1]],
  method = "dl")
##      [,1] [,2]
## [1,]    3    0
## [2,]    0    3
```

要返回最佳匹配的总和，我们可以选取最小值然后求和。结果表明，忽略大小写和顺序后，第二个观测名称与第一个参考名称完美匹配。如果我们将其作为一个整体字符串进行测试，永远无法得到这个结果：

```r
sum(apply(stringdistmatrix(
  strsplit(
    x = tolower(reference.Names[1]),
    split = "\\s")[[1]],
  strsplit(
    x = tolower(observed.Names[2]),
    split = "\\s")[[1]],
  method = "dl"), 1, min))
## [1] 0
```

综合以上所有讨论，我们得到了一系列可用于字符串匹配的技术。我们找出要忽略的值（例如标点符号）、拆分字符串（例如按空格拆分）以及忽略大小写。我们编写一个函数来帮助匹配名称。为此，我们还添加了一个可选参数 `fuzz`，用于控制是否包含在特定容差范围内的近似匹配。我们还统计精确匹配的数量。这段代码并非针对速度或效率进行优化，而是一个结合了您所学知识的示例。它还将这些知识应用于一组名称，其中每个手写名称（`index` 参数）与一个可能的候选向量（`pool`）进行比较。我们在函数内部添加了内联注释来解释各部分，而不是在开头写一大段文字：

```r
matchIt <- function(index,
                    pool,
                    ignore = c("\\.", "-"),
                    split = FALSE,
                    ignore.case = TRUE,
                    fuzz = .05,
                    method = "dl") {
  # 对于想要忽略的内容，将其删除
  # 例如，移除空格、句点、破折号
  rawpool <- pool
  for (v in ignore) {
    index <- gsub(v, "", index)
    pool <- gsub(v, "", pool)
  }
  # 如果忽略大小写，则转换为小写
  if (ignore.case) {
    index <- tolower(index)
    pool <- tolower(pool)
  }
  if (!identical(split, FALSE)) {
    index2 <- strsplit(index, split = split)[[1]]
    index2 <- index2[nzchar(index2)]
    pool2 <- strsplit(pool, split = split)
    pool2 <- lapply(pool2, function(x)
      x[nzchar(x)])
    # 计算距离，默认使用 Damerau-Levenshtein 距离
    distances <- sapply(pool2, function(x) {
      sum(apply(
        stringdistmatrix(index2, x, method = method),
        1,
        min,
        na.rm = TRUE
      ))
    })
  } else {
    # 计算距离，默认使用 Damerau-Levenshtein 距离
    distances <- stringdist(index, pool, method = method)
  }
  # 某些方法会导致无穷大结果，将这些设为缺失值
  distances[!is.finite(distances)] <- NA
  # 获取最佳和最差匹配
  best <- min(distances, na.rm = TRUE)
  worst <- max(distances, na.rm = TRUE)
  # 如果启用模糊匹配，则抓取所有距离在最佳与最差值之差
  # 的 fuzz 百分比范围内的值，旨在捕获与最佳匹配非常接近的潜在匹配
  if (fuzz) {
    usedex <- which(distances <= (best + ((worst - best) * fuzz)))
  } else {
    usedex <- which(distances == best)
  }
  # 定义一个距离阈值，低于该值则视为完美或精确匹配
  perfect <- distances[usedex] < .01
  out <- rawpool[usedex]
  # 统计完美匹配的数量
  count <- sum(perfect)
  if (any(perfect)) {
    # 如果有完美匹配，只使用其中一个
    match <- out[perfect][1]
    # 返回包含完美匹配及其数量的数据表（数量通常为 1）
    data.table(Match = match,
               N = count,
               Written = NA_character_)
  } else {
    # 如果没有完美匹配，
    # 返回一个以逗号分隔的近似匹配列表
    # 同时返回实际手写的内容
    data.table(
      Match = paste(out, collapse = ", "),
      N = count,
      Written = index
    )
  }
}
```

现在我们遍历每个观测名称，尝试将其与参考名称进行匹配，容差设置在最接近匹配的 5% 以内（`.05`）。由于输出格式总是一致的，我们可以按行合并它们：

```r
output <- lapply(observed.Names, function(n) {
  matchIt(
    index = n,
    pool = reference.Names,
    ignore = c("\\.", "-"),
    split = "\\s",
    fuzz = .05)
})
output <- do.call(rbind, output)
output
##              Match N    Written
## 1:       This Test 1       <NA>
## 2:       This Test 1       <NA>
## 3:       Test Thas 0 test that
## 4:       Jane Mary 1       <NA>
## 5:       Jane Mary 0   mary sou
## 6:     Jack Dun-Dee 0  jack dee
## 7: Jane Mary, Jack Dun-Dee 0 jane jack
```

我们看到 `This Test` 出现在两行中，因此我们进行汇总并打印最终输出。`This Test` 有两个完美匹配。`Test Thas` 没有完美匹配，但在 `Uncertain` 列下有一个近似匹配。最后，`jack dee` 与 `Jack Dun-Dee` 是近似但不确定的匹配，而 `jane jack` 与 `Jane Mary` 或 `Jack Dun-Dee` 都是不确定的匹配：

```r
finaloutput <- output[, .(
  N = sum(N),
  Uncertain = paste(na.omit(Written), collapse = ", ")),
  by = Match]
finaloutput
##              Match N Uncertain
## 1:       This Test 2
## 2:       Test Thas 0 test that
## 3:       Jane Mary 1   mary sou
## 4:     Jack Dun-Dee 0  jack dee
```

### 5：简·玛丽，杰克·邓迪 0 jane jack

虽然要涵盖所有示例和用例颇具挑战性，但这些基本工具在组合使用时，应足以应付多种类型的数据管理任务。

特别地，两位作者将模糊匹配的变体应用于不同场景中，包括学生同伴排名（课堂活动）和签到表匹配（用于招聘和专业发展名册）。

### 8.4 小结

在本章中，你学习了如何基于精确正则表达式或模糊匹配来定位文本。此外，你还掌握了如何用新的字符串片段替换旧的。最后，我们讨论了在 `data.table` 中创建新变量的方法。表 8-1 总结了本章介绍的关键函数。

**表 8-1.** 本章描述的关键函数及其功能摘要

| 函数 | 功能 |
|---|---|
| `read.dta()` | 读取 Stata 文件 |
| `str()` | 展示 R 对象结构 |
| `grep()` | 模式匹配，返回一个向量，显示哪些元素匹配 |
| `grepl()` | 模式匹配，返回一个布尔向量，对应 x 中的所有值 |
| `gsub()` | 匹配一个模式并进行替换，返回修改后的原始向量 |
| `rowMeans()`, `rowSums()` | 计算数据表或矩阵中每行数值的平均值或总和 |
| `Reduce()` | 将一个二元函数作为第一个参数，并将其应用于其余参数 |
| `.SD` | `data.table` 中的保留名称，可将数据表内的所有变量作为一个列表引用 |
| `.SDcols` | `data.table` 的参数，允许指定要包含在 `.SD` 中的变量 |
| `strsplit()` | 基于特定的分隔字符拆分字符串 |
| `stringdistmatrix()` | 创建一个矩阵，显示观测值与期望值之间的距离 |
| `amatch()` | 提供一个向量，显示期望值中哪个索引与观测值最匹配 |
| `tolower()` | 将给定字符串中的所有字符转换为小写 |

# 第 9 章 其他数据管理包

比较数据框和数据表引出了一个有趣的问题：如果有更多类型的数据该怎么办？特别是，如果存在不同的数据存储方式，而这些方式本质上都是某种形式的表格呢？除了数据框或数据表之外，还有许多存储数据的方式，其中很多都只是表格。`dplyr` [29] 的理念是，无论数据后端是什么，用户体验都应该是相同的。

为此，`dplyr` 为常见的数据管理任务实现了通用函数。针对每个通用函数，都会编写具体的方法，将通用操作转换为特定后端所需的任何代码或语言。

使用抽象层可确保用户获得一致的体验，无论使用的是哪种特定数据格式或后端。这也使得 `dplyr` 具有可扩展性，只需编写额外的函数或方法，即可添加对新格式的支持，而无需改变用户体验。

与 `data.table` 有些类似，`dplyr` 也拥有自己的数据框增强版本，称为整洁表或 tibbles [12]。结合 `tidyr` [30] 包中的一组数据操作函数，它们协同工作，实现对数据的细致操作与组织。自本书第一版以来，这一系列包已被统称为整洁宇宙或 *tidyverse* [26]。

这种整洁数据科学哲学的一部分，是用描述特定动作的动词来命名函数。也就是说，与其使用少数几个可适应多种任务的函数，整洁方法为最常见的任务提供了独特的函数。这使得代码更具可读性，即使是不熟悉 R 的人也能理解。虽然 `data.table` 对效率的持续专注使其在速度上稳操胜券，但更直观的代码无疑也有其价值。两种方法各有优势与挑战。

© Matt Wiley and Joshua F. Wiley 2020
M. Wiley and J. F. Wiley, *Advanced R 4 Data Programming and the Cloud*, <https://doi.org/10.1007/978-1-4842-5973-3_9>

两位作者最熟悉的是 `data.table`——因此我们可能有明显的偏好，这情有可原。然而，在某些情况下，tibbles 要么更合理，要么对更广泛的受众更易上手。因此，一本关于 R 高级用法的书籍若不讨论整洁方法，便显得不够全面。

和之前一样，我们设置一些选项以减少打印的行数。我们不单独调用 `dplyr`、`tibble` 和 `tidyr`，而是通过一个名为 `tidyverse` 的整合包来调用它们：

```r
library(tidyverse)
library(extraoperators)
library(stringdist)
options(stringsAsFactors = FALSE,
        tibble.print_max = 20, ## 如果超过 20 行
        tibble.print_min = 5,  ## 打印前 5 行
        digits = 2)            ## 减少 R 打印的数字位数
```

对于 `data.table`，我们使用 `sapply()` 来获取每列或每个变量的类别向量。虽然我们可以对 tibbles 做同样的事情，但 tibbles 会自动提供每列数据的类型或类别信息，以及总列数和行数（例如，`<dbl>` 表示 *double* 或数值型，`<chr>` 表示 *character* 类数据）。尽管存在差异，但处理数据的共同目标依然不变：排序、子集选取、排序、计算和重塑：

```r
iris$Species <- as.character(iris$Species)
diris <- as_tibble(iris)
diris
```

```
## # A tibble: 150 x 5
##   Sepal.Length Sepal.Width Petal.Length Petal.Width Species
##          <dbl>       <dbl>        <dbl>       <dbl> <chr>  
## 1          5.1         3.5          1.4         0.2 setosa 
## 2          4.9         3            1.4         0.2 setosa 
## 3          4.7         3.2          1.3         0.2 setosa 
## 4          4.6         3.1          1.5         0.2 setosa 
## 5          5           3.6          1.4         0.2 setosa 
## # ... with 145 more rows
```


