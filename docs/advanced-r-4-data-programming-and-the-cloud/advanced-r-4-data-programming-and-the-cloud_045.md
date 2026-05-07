# 第 8 章：高级 `data.table` 用法

**`d$S2Q02R` 的直方图**

y

频数

10 20 30 40 50 60 70 80

`d$S2Q02R`

***图 8-2.** 异常值已消失，因为直方图会忽略缺失值*
**`d$S2Q03R` 的直方图**

y

频数

100 150 200 250

`d$S2Q03R`

***图 8-3.** 异常值已消失，因为直方图会忽略缺失值*

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

### 8.3 模糊匹配

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

## 第 8 章 进阶 data.table

我们可以使用 `strsplit()` 通过特定字符来拆分字符串。可以使用任何模式或字符，但这里我们使用空格。我们在正则表达式中使用反斜杠 s（`\s`）作为特殊字符，表示任何类型的空格。需要第二个反斜杠来转义第一个反斜杠。这是因为特殊字符并不是 s（其转义形式为 `\s`），而是复合字符 `\s` —— 因此，转义后的版本是 `\\s`。我们在以下代码中演示了 `strsplit()` 的结果：

```
strsplit(
  x = reference.Names[1],
  split = "\\s")[[1]]
## [1] "This" "Test"
```

```
strsplit(
  x = observed.Names[2],
  split = "\\s")[[1]]
## [1] "test" "this"
```

现在我们可以再次使用 `stringdistmatrix()`。结果显示，第二个观测名称的每个子块与第一个参考名称的某个子块之间只差一个字符操作：

```
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

第 8 章 进阶 data.table

```
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

```
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

第 8 章 进阶 data.table

```
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

```
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

第 8 章 进阶 data.table

我们看到 `This Test` 出现在两行中，因此我们进行汇总并打印最终输出。`This Test` 有两个完美匹配。`Test Thas` 没有完美匹配，但在 `Uncertain` 列下有一个近似匹配。最后，`jack dee` 与 `Jack Dun-Dee` 是近似但不确定的匹配，而 `jane jack` 与 `Jane Mary` 或 `Jack Dun-Dee` 都是不确定的匹配：

```
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

在本章中，你学习了如何基于精确正则表达式或模糊匹配来定位文本。此外，你还掌握了如何用新的字符串片段替换旧的。最后，我们讨论了在`data.table`中创建新变量的方法。表 8-1 总结了本章介绍的关键函数。

**第 8 章 进阶 data.table**

***表 8-1.** 本章描述的关键函数及其功能摘要*

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

### 第 9 章

### 其他数据管理包

比较数据框和数据表引出了一个有趣的问题：如果有更多类型的数据该怎么办？特别是，如果存在不同的数据存储方式，而这些方式本质上都是某种形式的表格呢？除了数据框或数据表之外，还有许多存储数据的方式，其中很多都只是表格。`dplyr` [29] 的理念是，无论数据后端是什么，用户体验都应该是相同的。

为此，`dplyr` 为常见的数据管理任务实现了通用函数。针对每个通用函数，都会编写具体的方法，将通用操作转换为特定后端所需的任何代码或语言。

使用抽象层可确保用户获得一致的体验，无论使用的是哪种特定数据格式或后端。这也使得 `dplyr` 具有可扩展性，只需编写额外的函数或方法，即可添加对新格式的支持，而无需改变用户体验。

与 `data.table` 有些类似，`dplyr` 也拥有自己的数据框增强版本，称为整洁表或 tibbles [12]。结合 `tidyr` [30] 包中的一组数据操作函数，它们协同工作，实现对数据的细致操作与组织。自本书第一版以来，这一系列包已被统称为整洁宇宙或 *tidyverse* [26]。

这种整洁数据科学哲学的一部分，是用描述特定动作的动词来命名函数。也就是说，与其使用少数几个可适应多种任务的函数，整洁方法为最常见的任务提供了独特的函数。这使得代码更具可读性，即使是不熟悉 R 的人也能理解。虽然 `data.table` 对效率的持续专注使其在速度上稳操胜券，但更直观的代码无疑也有其价值。两种方法各有优势与挑战。

© Matt Wiley and Joshua F. Wiley 2020  
M. Wiley and J. F. Wiley, *Advanced R 4 Data Programming and the Cloud*, <https://doi.org/10.1007/978-1-4842-5973-3_9>

**第 9 章 其他数据管理包**

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

**第 9 章 其他数据管理包**

### 9.1 排序

理解整洁方法的一个有益方式是，它使用动词来描述对数据的操作。在 `tidyverse` 中，数据通过 `arrange()` 函数进行排序，该函数将数据集作为其第一个参数：

```r
diris <- arrange(diris, Sepal.Length)
diris
```

```
## # A tibble: 150 x 5
##   Sepal.Length Sepal.Width Petal.Length Petal.Width Species
##          <dbl>       <dbl>        <dbl>       <dbl> <chr>  
## 1          4.3         3            1.1         0.1 setosa 
## 2          4.4         2.9          1.4         0.2 setosa 
## 3          4.4         3            1.3         0.2 setosa 
## 4          4.4         3.2          1.3         0.2 setosa 
## 5          4.5         2.3          1.3         0.3 setosa 
## # ... with 145 more rows
```

`Tidyverse` 通过函数工作，这些函数通常遵循使用动词的惯例，意在表示正在对数据执行的操作。为了遵循英语常见的“名词-动词”句子结构，这些函数还使用了*管道操作符*。

管道操作符编码为 `%>%`，它消除了在每个函数内部引用数据集的需要。具体而言，它将左侧的数据对象（名词）传递到右侧函数（动词）的*第一个*形式参数中。

比较以下代码与之前的代码，看看第一个形式参数是如何现在位于左侧的：

```r
diris %>% arrange(Sepal.Length)
```

```
## # A tibble: 150 x 5
##   Sepal.Length Sepal.Width Petal.Length Petal.Width Species
##          <dbl>       <dbl>        <dbl>       <dbl> <chr>  
## 1          4.3         3            1.1         0.1 setosa 
## 2          4.4         2.9          1.4         0.2 setosa 
## 3          4.4         3            1.3         0.2 setosa 
## 4          4.4         3.2          1.3         0.2 setosa 
## 5          4.5         2.3          1.3         0.3 setosa 
## # ... with 145 more rows
```



### 9.2 选择与子集化

## 第 9 章 其他数据管理包

值得注意的是，如果某个函数未将*数据*保留为第一个形参，那么在右侧函数参数的该位置使用一个`.`（句点或句号）将能使管道按预期工作。

管道操作符本身不会修改我们的数据集`diris`。与往常一样，需要使用赋值操作符才能进行永久性更改。`arrange()`函数在第一个参数之后接受额外的参数，以提供按这些其他列进行排序的功能。默认是升序排列，可以通过再次调用`desc()`函数来可选地修改为*降序*排列：

```r
diris <- diris %>% arrange(Sepal.Length, desc(Sepal.Width))
diris
```

```
## # A tibble: 150 x 5
##   Sepal.Length Sepal.Width Petal.Length Petal.Width Species
##          <dbl>       <dbl>        <dbl>       <dbl> <chr>
## 1          4.3         3            1.1         0.1 setosa
## 2          4.4         3.2          1.3         0.2 setosa
## 3          4.4         3            1.3         0.2 setosa
## 4          4.4         2.9          1.4         0.2 setosa
## 5          4.5         2.3          1.3         0.3 setosa
## # ... with 145 more rows
```

通过`anyDuplicated()`可以查看整个数据集或特定列首次出现重复时的行索引。当对整个数据集调用此函数时，它会查找所有列之间的匹配，在此数据集中发生在第 78 行和第 79 行。`Sepal.Length`中的重复变量出现在第三行，如下代码所示：

```r
diris %>% anyDuplicated()
## [1] 79

anyDuplicated(diris$Sepal.Length)
## [1] 3
```

`anyDuplicated()`返回首次重复的行索引，而`duplicated()`则简单地告诉我们表中有多少次这样的重复。查看代码，我们看到在所有列完全匹配的情况下，第 79 行是唯一出现的情况。另一方面，萼片长度的重复可能首次发生在第 3 行，但还有更多重复：

```r
diris %>% duplicated() %>% table()
## .
## FALSE  TRUE
##   149     1

table(duplicated(diris$Sepal.Length))
##
## FALSE  TRUE
##    35   115
```

如果我们希望看到仅包含不同值的表格结果，`distinct()`的用法与`arrange()`非常相似。第一个形参是数据，其余的是指定列名的变量。如果未指定任何列名，`distinct()`将对所有列进行操作。回想`duplicated()`的调用结果，共有 149 个唯一的行：

```r
diris %>% distinct()
```

```
## # A tibble: 149 x 5
##   Sepal.Length Sepal.Width Petal.Length Petal.Width Species
##          <dbl>       <dbl>        <dbl>       <dbl> <chr>
## 1          4.3         3            1.1         0.1 setosa
## 2          4.4         3.2          1.3         0.2 setosa
## 3          4.4         3            1.3         0.2 setosa
## 4          4.4         2.9          1.4         0.2 setosa
## 5          4.5         2.3          1.3         0.3 setosa
## # ... with 144 more rows
```

类似地，回想一下，如果只考虑萼片长度，则只有 35 个唯一的行。因此，使用`distinct()`的第二个形参来保存`Sepal.Length`列，在我们的 tibble 中只会产生 35 行。这里显示这些行的前五行：

```r
diris %>% distinct(Sepal.Length)
```

```
## # A tibble: 35 x 1
##   Sepal.Length
##          <dbl>
## 1          4.3
## 2          4.4
## 3          4.5
## 4          4.6
## 5          4.7
## # ... with 30 more rows
```

本节最后，再次调用`distinct()`，并在第二和第三个形参中指定多个列。在这里，只要同时满足`Sepal.Length`和`Sepal.Width`不重复，该行就被视为是唯一的。请特别注意第 2–4 行，它们具有相同的`Sepal.Length`，但这些行并未被视为重复，因为它们的`Sepal.Width`不同：

```r
diris %>% distinct(Sepal.Length, Sepal.Width)
```

```
## # A tibble: 117 x 2
##   Sepal.Length Sepal.Width
##          <dbl>       <dbl>
## 1          4.3         3
## 2          4.4         3.2
## 3          4.4         3
## 4          4.4         2.9
## 5          4.5         2.3
## # ... with 112 more rows
```

### 9.2 选择与子集化

在`tidyverse`中选择存储数据的部分与`data.table`的方法不同。请记住，`tidyverse`通过函数调用来工作。这些函数被设计为独立于底层数据结构。尽管如此，我们确实希望得到相同的结果。按位置选择行通过`slice()`函数实现，它接收两个形参。第一个是数据对象，在我们的例子中是`diris`，第二个允许我们选择特定的行。请记住我们正在使用*管道*操作符`%>%`。因此，我们的第一个形参*在*函数之外！下面两行代码只选择前五行，一行是传统的函数表示法，另一行使用了管道操作符。目前使用管道可能看起来稍微更简洁但有些多余。当我们开始执行更复杂的操作时，管道的真正优势会变得更加明显：

```r
slice(diris, 1:5)
```

```
## # A tibble: 5 x 5
##   Sepal.Length Sepal.Width Petal.Length Petal.Width Species
##          <dbl>       <dbl>        <dbl>       <dbl> <chr>
## 1          4.3         3            1.1         0.1 setosa
## 2          4.4         3.2          1.3         0.2 setosa
## 3          4.4         3            1.3         0.2 setosa
## 4          4.4         2.9          1.4         0.2 setosa
## 5          4.5         2.3          1.3         0.3 setosa
```

```r
diris %>% slice(1:5)
```

```
## # A tibble: 5 x 5
##   Sepal.Length Sepal.Width Petal.Length Petal.Width Species
##          <dbl>       <dbl>        <dbl>       <dbl> <chr>
## 1          4.3         3            1.1         0.1 setosa
## 2          4.4         3.2          1.3         0.2 setosa
## 3          4.4         3            1.3         0.2 setosa
## 4          4.4         2.9          1.4         0.2 setosa
## 5          4.5         2.3          1.3         0.3 setosa
```

如前所述，`-`可用于否定。因此，我们也可以通过丢弃第 1–145 行来仅保留最后五行：

```r
diris %>% slice(-(1:145))
```

```
## # A tibble: 5 x 5
##   Sepal.Length Sepal.Width Petal.Length Petal.Width Species
##          <dbl>       <dbl>        <dbl>       <dbl> <chr>
## 1          7.7         3.8          6.7         2.2 virginica
## 2          7.7         3            6.1         2.3 virginica
## 3          7.7         2.8          6.7         2   virginica
## 4          7.7         2.6          6.9         2.3 virginica
## 5          7.9         3.8          6.4         2   virginica
```

除了直接选择行，我们还可以对它们设置逻辑条件。同样，这类参数属于函数调用的第二个形参。但在这种情况下，我们使用`filter()`而不是`slice()`。`filter()`函数使用逻辑索引。通过编写一个逻辑测试，很容易选择`Species`是或不是`setosa`的所有行：

```r
diris %>% filter(Species == "setosa")
```

```
## # A tibble: 50 x 5
##   Sepal.Length Sepal.Width Petal.Length Petal.Width Species
##          <dbl>       <dbl>        <dbl>       <dbl> <chr>
## 1          4.3         3            1.1         0.1 setosa
## 2          4.4         3.2          1.3         0.2 setosa
## 3          4.4         3            1.3         0.2 setosa
## 4          4.4         2.9          1.4         0.2 setosa
## 5          4.5         2.3          1.3         0.3 setosa
## # ... with 45 more rows
```

```r
diris %>% filter(Species != "setosa")
```

```
## # A tibble: 100 x 5
##   Sepal.Length Sepal.Width Petal.Length Petal.Width Species
##          <dbl>       <dbl>        <dbl>       <dbl> <chr>
## 1          4.9         2.5          4.5         1.7 virginica
## 2          4.9         2.4          3.3         1   versicolor
## 3          5           2.3          3.3         1   versicolor
## 4          5           2            3.5         1   versicolor
## 5          5.1         2.5          3           1.1 versicolor
## # ... with 95 more rows
```

也可以基于数值与不等号、等号或不等号的比较，以及逻辑索引来选择行。数值和字符测试可以混合匹配使用，以精确提取所需的行：

```r
iris %>% filter(Sepal.Length < 5 & Petal.Width > .2)
```

```
##   Sepal.Length Sepal.Width Petal.Length Petal.Width    Species
## 1          4.6         3.4          1.4         0.3     setosa
## 2          4.5         2.3          1.3         0.3     setosa
## 3          4.8         3.0          1.4         0.3     setosa
## 4          4.9         2.4          3.3         1.0 versicolor
## 5          4.9         2.5          4.5         1.7  virginica
```

```r
diris %>% filter(Sepal.Length == 4.3 | Species != "setosa")
```

```
## # A tibble: 101 x 5
##   Sepal.Length Sepal.Width Petal.Length Petal.Width Species
##          <dbl>       <dbl>        <dbl>       <dbl> <chr>
## 1          4.3         3            1.1         0.1 setosa
## 2          4.9         2.5          4.5         1.7 virginica
## 3          4.9         2.4          3.3         1   versicolor
## 4          5           2.3          3.3         1   versicolor
## 5          5           2            3.5         1   versicolor
```



## # ... 还有 **96** 行

若我们想在一列中查找多个匹配项，可使用`%in%`运算符，并配合一个感兴趣的取值列表。这使我们能够将编码与选择项分离，然后根据兴趣进行筛选：

```
interest <- c(4.3, 4.4)
diris %>% filter(Sepal.Length %in% interest)
```

## # 一个 tibble: 4 x 5
## Sepal.Length Sepal.Width Petal.Length Petal.Width Species
## <dbl> <dbl> <dbl> <dbl> <chr>
## 1 4.3 3 1.1 0.1 setosa
## 2 4.4 3.2 1.3 0.2 setosa
## 3 4.4 3 1.3 0.2 setosa
## 4 4.4 2.9 1.4 0.2 setosa

