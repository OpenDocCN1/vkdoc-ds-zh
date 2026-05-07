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

**注意**：正则表达式是查找和匹配字符串模式的强大工具。在 R 中，`grep()` 和 `grepl()` 返回匹配预期模式的向量元素的位置或逻辑向量。结合替换操作，正则表达式有助于重新编码数据。

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

**注意** `gsub()` 函数将正则表达式匹配与替换功能结合在一起，它能将字符串中匹配正则表达式的部分替换为新文本，其形式如下：`gsub(pattern = "regex", replacement = "new values", x = "original data")`.

我们可能需要的另一个重新编码任务是去除数字，只保留标签。

这可以使用 `gsub()` 函数来完成，该函数在字符串内执行正则表达式匹配，然后将匹配项替换为指定的值（可以是零长度字符串）。我们先看下面这个简单的例子：

```
gsub(
  pattern = "abc",
  replacement = "",
  x = c("a", "abcd", "123abc456"))
```

## [1] "a" "d" "123456"

因为它只是使用正则表达式来匹配和移除数字，我们可以重复使用之前的模式来测试这一点：

```
p.remove <- "ˆ[-]*[0-9]+ - "
gsub(
  pattern = p.remove,
  replacement = "",
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
    "4 - PARTIAL INTERVIEW OF DOCTOR"))
```

## [1] "TREATMENT REFUSED TREATMENT" "DID NOT REFUSED TREATMENT"
## [3] "JACK - REFUSED" "REFUSED"
## [5] "REFUSED" "REFUSED"
## [7] "MISSING" "DON'T KNOW"
## [9] "LEGITIMATE SKIP" "PARTIAL INTERVIEW"
## [11] "NOT IN UNIVERSE" "- REFUSED"



## [13] "诡计案例 4 - 拒绝回答" "医生部分访谈"

为了提高效率，我们可以将这部分代码与之前的代码结合，将某些案例设为缺失值。一步完成可以避免重复处理。不过，我们需要先重新读取数据，以便获得原始数据：

```
d <- read.dta("data/ch08/ICPSR_04691/DS0001/04691-0001-Data.dta")
d <- as.data.table(d)
```

`setkey(d, IDNUMR)`

因为 `gsub()` 会将输入强制转换为字符，无论我们传入的是因子还是字符，结果都会得到字符。所以，为了知道输入原本是否为因子，我们创建一个对象 `f`，它是一个逻辑值，用于指示 `x` 最初是否为因子。然后，我们不再直接丢弃因子水平，而是根据需要将其转换回因子。除此之外，我们将之前的代码与移除代码合并在一起：

```
d[, (v) := lapply(.SD, function(x) {
  f <- is.factor(x)
  x[grepl(pattern = p, x)] <- NA
  x <- gsub(pattern = p.remove, replacement = "", x)
  if (f) factor(x) else x
}), .SDcols = v]
```

`table(d[, EDUCATIO])`

```
##
##               高中毕业（12 年教育）       高中以下教育
##                             21238                             4661
##                   高中以上教育
##                                76022
```

`table(d[, S1Q01])`

```
##
##   女性     男性
##  49719   52554
```

`table(d[, S2Q01])`

```
##          极好       良好       尚可       差     非常好
##         65252      2189     10680       297     23903
```

### 重新编码数值

由于数值数据具有更系统的结构，对其重新编码比重新编码字符串数据更容易。为了在数值数据中指示不同类型的“缺失”，人们经常使用超出范围的值，例如负数或非常大的数字（如 999）。从数据角度来看，编码不同类型的缺失值是有意义的，因为这些代码提供了额外的信息（例如，跳过了问题、问题不适用）。然而，从实际使用的角度来看，我们通常希望忽略所有缺失数据（例如，在计算平均值时，如果数据中使用 -2 表示父母拒绝报告孩子年龄，或 -3 表示父母忘记，都会影响输出结果）。我们可以使用 `data.table` 包中的 `%between%` 运算符来查找落在某个范围内的值，并通过使用否定运算符 `!` 来定位补集。

例如，对于身高，文档指示使用零或负值以及大于 90 的值来编码各种类型的缺失。类似地，对于体重，使用零或负值以及大于 900 的值来编码不同类型的缺失值。要让 R 知道这些值表示缺失，我们需要重新编码它们。从我们的 `table()` 数据中，我们可以看到确实存在一些缺失值，并且可以确切地看到用于编码它们的值（-2、96、96、996、997）：

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

同样，我们可以逐个处理这些变量。但是，对于许多变量来说，这样做会变得繁琐。常见的模式是，如果有效值小于 9，那么 9 就是缺失值。如果值达到两位数，那么 90 就是缺失值，依此类推。我们通过检查最大值并相应地设置边界来自动化重新编码过程。通过以下代码和图 8-1 可以查看变量原始的样子。

```
par(mfrow = c(1,2))
hist(d$S2Q02R)
hist(d$S2Q03R)
```

我们重点关注三个需要进行数值重新编码的列。对于每个变量，我们循环遍历列表中的列名。将 `m` 设为我们尝试的最大值，首先将 `j` 和 `i` 设为 1。只要 `j` 等于 1 且 `i` 小于或等于要尝试的值的数量（例如 9、99、999），我们就继续执行下一步计算。如果变量的最大值（忽略缺失值）`k` 小于第 `i` 个 `m` 值的最大值，则将 `j` 设为 0（这会中断循环），并将任何超出 0 到第 `i` 个 `m` 之间范围的值替换掉。如果第 `i` 个 `m` 大于 90 或减去某个极小值，则将该变量设为缺失值（`NA_integer_`）。本质上，我们是按列遍历列表，并检查整个列中的最大值是否是我们最大值 `m` 之一。一旦找到匹配项，我们就会确保将 9 到 10、90 到 100 或 900 到 1000 范围内的值设为缺失值。注意，我们使用 `NA_integer_` 而不是 `NA`，因为我们是在替换现有变量中的子集值。`data.table` 要求用于替换现有变量中值的数据类别与该变量的类别相匹配。我们之前可以使用 `NA`，因为那时我们是覆盖整个变量，而不是替换变量中的部分选定值。

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

**d$S2Q 的直方图 d$S2Q 的直方图**

![图 8-1](https://via.placeholder.com/400x200?text=直方图)

***图 8-1.** 接近 >900 和 >90 的异常值*

我们通过使用与之前相同的直方图函数来展示重新编码后的结果，如图 8-2 和图 8-3 所示。显然，重新编码后 x 轴的范围更窄了：

```
hist(d$S2Q02R)
hist(d$S2Q03R)
```

至此，我们关于数据卫生的章节就结束了。接下来，我们将展示如何创建新变量以简化分析过程。从数据科学的角度来看，变量创建有时被称为特征创建。在社会科学和行为研究的许多领域中，问卷或调查被普遍使用。在教育领域，考试常被实施。无论数据来自考试、问卷还是调查，一个常见的任务是将对单个项目或变量的回答进行汇总，以创建量表得分、总体测试得分或其他一些汇总指标。

### 8.2 创建新变量

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



## 1362 133

