# 7. 使用 `data.table` 进行数据管理入门

我们已经简要介绍过`data.table`包。这个包是本章的核心，涵盖了在广义的数据管理范畴下访问、编辑和处理数据的基础知识。虽然数据管理并不光鲜亮丽，但它是数据可视化或分析至关重要的第一步。此外，特定分析项目的大部分时间可能都花在数据管理上。例如，一旦数据清洗完毕并符合 R 中`lm()`函数所期望的格式，运行线性模型可能只需要一行代码。数据管理可能具有挑战性，因为原始数据有各种类型、形状和格式；数据缺失很常见；并且您可能还需要合并或融合不同的数据源。在本章中，我们将介绍处理数据管理的机械技巧和哲学思维。本章使用的所有包都已包含在我们的`checkpoint.R`文件中。因此，您只需执行该文件即可开始。

在本章中，我们使用`data.table` R 包（Dowle, Srinivasan, Short, and Lianoglou, 2015）。以下代码加载`checkpoint`包（Microsoft Corporation, 2016）以控制所用 R 包的确切版本，然后加载`data.table`包：

```
library(checkpoint)
checkpoint("2016-05-10")
library(data.table)
```

```
# 加载检查点及所需包

library(checkpoint)
checkpoint("2016-09-04", R.version = "3.3.1")
library(data.table)
options(width = 70) # 每行仅显示 70 个字符
```

### data.table 简介

使用数据表（data table）而非内置的数据框（data frame）类对象的好处之一是，数据表内存效率更高，操作和修改速度更快。`data.table` 包在很大程度上是通过在内存中就地修改数据表来实现这一点的，而对于数据框，R 通常会对数据进行复制、修改然后存储。无论数据的所有列是否被更改，例如 100 列中的 1 列，都会发生完整的复制。因此，与数据表上的类似操作相比，对数据框的操作往往耗时更长，占用内存更多。为了展示数据管理的概念，我们使用小型数据，因此内存和处理器时间不是问题。然而，我们强调使用数据表而非数据框，是因为数据表能优雅地扩展到比数据框大得多的数据量。数据表还有其他优点，包括无需引用所有变量名，我们将在本章中展示这些优点。相比之下，数据框的主要优势在于它们属于 R 的基础部分，并且更多的人熟悉如何使用它们（例如，在分享代码时，这很有帮助）。

数据表可以非常大。查看数据的`head()`和`tail()`通常比查看全部数据更有帮助。在 R 中通过键入其名称显示数据表时，默认会返回前五行和最后五行。R 还默认打印七位有效数字，这可能会使我们的数值条目显得混乱。为了清晰起见，我们使用`options()`函数来控制一些全局选项，以便只打印前三行和后三行（如果数据表总行数超过 20 行——否则，显示所有行）；此外，我们的数值条目仅显示两位有效数字。在设置这些选项的同时，我们引入埃德加·安德森的鸢尾花数据集。该数据涉及三种鸢尾花物种的萼片和花瓣的长度和宽度（单位：厘米）。

在将鸢尾花数据转换为数据表之前，我们将把物种名称转换为字符类型而非因子类型。在以下代码中，我们设置选项，强制物种名称转换为字符类型，并创建我们的数据表`diris`：

```
options(stringsAsFactors = FALSE,
datatable.print.nrows = 20, ## 若行数超过 20
datatable.print.topn = 3, ## 打印前 3 行和后 3 行
digits = 2) ## 减少 R 打印的位数
iris$Species <- as.character(iris$Species)
diris <- as.data.table(iris)
diris
```

我们的数据共有`150`行，包含三个物种：`setosa`、`versicolor`和`virginica`。每个物种有`50`组测量数据。R 在内存中运行，对于巨大的数据表，这可能会引起问题。`tables()`命令显示内存中的所有数据表及其大小。实际上，`tables()`本身就是一个数据表。注意该函数提供了关于数据表名称、行数、列数、以`MB`为单位的大小以及是否有键的信息。我们将在后面几段讨论带键的数据表。目前，注意到`1MB`不太可能给我们带来内存问题：

```
tables()
```

如果我们想了解每列数据类型的更多信息，可以在感兴趣的数据表上调用`sapply()`命令和`class()`函数。回想一下，`*apply`类函数将第二个形参指定的函数应用到第一个形参的元素上，并返回结果。在我们的例子中，对`diris`调用`class()`，我们看到`diris`各列中存储了哪些类型的值：

```
sapply(diris, class)
```

数据表相对于其他数据格式有一个强大的概念优势。即，数据表可以被设置键。有了键，就可以使用二分查找。对于不熟悉二分查找的人来说，想象一下需要在电话簿中查找一个名字。阅读每个名字会花费相当长的时间。然而，因为我们知道电话簿是按字母顺序排列的，我们可以立即找到一个位于字母表中部的名字，判断我们搜索的名字属于该名字之前还是之后，并在这个过程中排除掉一半的电话簿。我们可以对剩余的一半重复这个算法，在仅仅两次查找后就只留下原始数据的四分之一需要搜索！更数学化地说，虽然搜索数据框的所有条目会基于行数`n`呈线性复杂度，但在数据表中，最坏情况下的复杂度为`log n`。在作者们使用的几个新表中，数据量翻倍仅使代码运行时间增加几分钟，而不是使运行时间翻倍。这是一个巨大的优势。

在`data.table`中，键是一个索引，它可能唯一也可能不唯一，由一个或多个数据列创建。数据按键排序，允许使用键进行非常快速的操作。示例操作包括子集化或筛选、为每个唯一的键值执行计算（例如，变量的平均值）以及合并两个或多个数据集。

当然，为数据表设置键确实需要时间，因此这取决于你在做这种选择之前访问数据的频率。另一个考虑因素是使用什么作为键。一个表只有一个键，尽管该键可以由多个列组成。函数`setkey()`以一个数据表作为第一个形参，然后接收不定数量的列名来设置一个键。要查看数据表是否有键，...

### 处理需要忽略的内容，例如删除空格、句点、连字符

```
rawpool <- pool
for (v in ignore) {
  index <- gsub(v, "", index)
  pool <- gsub(v, "", pool)
}
```

### 如果忽略大小写，则转换为小写

```
if (ignore.case) {
  index <- tolower(index)
  pool <- tolower(pool)
}
```

```
if (!identical(split, FALSE)) {
  index2 <- strsplit(index, split = split)[[1]]
  index2 <- index2[nzchar(index2)]
  pool2 <- strsplit(pool, split = split)
  pool2 <- lapply(pool2, function(x) x[nzchar(x)])

  ## 计算距离，默认为 Damerau-Levenshtein 距离
  distances <- sapply(pool2, function(x) {
    sum(apply(stringdistmatrix(index2, x, method = method),
              1, min, na.rm = TRUE))
  })
} else {
  ## 计算距离，默认为 Damerau-Levenshtein 距离
  distances <- stringdist(index, pool, method = method)
}
```

### 某些方法会导致无穷大的结果，将这些设为缺失值

```
distances[!is.finite(distances)] <- NA
```

### 获取最佳和最差距离

```
best <- min(distances, na.rm = TRUE)
worst <- max(distances, na.rm = TRUE)
```

### 如果使用模糊匹配，则获取在最佳与最差距离差值`fuzz`百分比范围内的所有距离

```
if (fuzz) {
  usedex <- which(distances <= (best + ((worst - best) * fuzz)))
} else {
  usedex <- which(distances == best)
}
```

### 定义一个距离阈值，低于此阈值被视为完全匹配或精确匹配

```
perfect <- distances[usedex] < .01
out <- rawpool[usedex]
```

### 计算完全匹配的数量

```
count <- sum(perfect)
```

### 函数继续到下一页

```
if (any(perfect)) {
  ## 如果存在完全匹配，则只使用一个
  match <- out[perfect][1]

  ## 返回一个包含完全匹配和完全匹配数量的数据表（可能为 1）
  data.table(
    Match = match,
    N = count,
    Written = NA_character_)
} else {
  ## 如果没有完全匹配，则返回近似匹配的列表，用逗号分隔并保持原始书写
  data.table(
    Match = paste(out, collapse = ", "),
    N = count,
    Written = index)
}
```

现在我们遍历每个观察到的名称，并尝试将其与参考名称中最佳匹配的 5%（`.05`）范围内的名称进行匹配。由于输出总是相同的，我们可以按行组合它们。

```
output <- lapply(observed.Names, function(n) {
  matchIt(index = n, pool = reference.Names, ignore = c("\\.", "-"), split = "\\s", fuzz = .05)
})
output <- do.call(rbind, output)
output
```

```
Match                  N   Written
1:               This Test 1        NA
2:               This Test 1        NA
3:               Test Thas 0 test that
4:               Jane Mary 1        NA
5:               Jane Mary 0  mary sou
6:            Jack Dun-Dee 0  jack dee
7: Jane Mary, Jack Dun-Dee 0 jane jack
```

我们可以看到`This Test`出现在两行中，因此我们进行聚合并打印最终输出。`This Test`有两个完全匹配。`Test Thas`没有完全匹配，但在`Uncertain`列下列出了一个近似匹配。最后，`jack dee`是`Jack Dun-Dee`的一个近似但不确定的匹配，`jane jack`是`Jane Mary`或`Jack Dun-Dee`的一个不确定匹配。

```
finaloutput <- output[, .(
  N = sum(N),
  Uncertain = paste(na.omit(Written), collapse = ", ")),
  by = Match]
finaloutput
```

```
Match                  N Uncertain
1:               This Test 2
2:               Test Thas 0 test that
3:               Jane Mary 1  mary sou
4:            Jack Dun-Dee 0  jack dee
5: Jane Mary, Jack Dun-Dee 0 jane jack
```

虽然很难涵盖所有示例和用例，但结合使用这些基本工具应该足以应对许多类型的数据管理任务。

## 总结

在本章中，您学习了如何基于精确的正则表达式或模糊匹配来定位文本。此外，您还学习了如何用新的字符串片段替换旧的字符串片段。最后，我们讨论了在`data.table`中创建新变量的方法。表 8-1 总结了本章介绍的关键函数。

**表 8-1. 本章描述的关键函数**

| 函数 | 功能描述 |
| --- | --- |
| `read.dta()` | 读取 Stata 文件 |
| `str()` | 显示 R 对象的结构 |
| `grep()` | 模式匹配，返回显示哪些元素匹配的向量 |
| `grepl()` | 模式匹配，返回显示`x`中所有值布尔值的向量 |
| `gsub()` | 匹配模式并进行替换，返回更改后的原始向量 |
| `rowMeans()`, `rowSums()` | 计算数据表或矩阵中每行的平均值或总和 |
| `Reduce()` | 接受一个二元函数作为第一个参数，并将其应用于其余参数 |
| `.SD` | `data.table`中的保留名称，可用于指代数据表中的所有变量作为一个列表 |
| `.SDcols` | `data.table`的参数，允许指定要包含在`.SD`中的变量 |
| `strsplit()` | 根据特定的分隔字符拆分字符串 |
| `stringdistmatrix()` | 创建一个包含观测值与期望值之间距离的矩阵 |
| `amatch()` | 提供一个向量，显示期望值中哪个索引最接近观测值 |
| `tolower()` | 将给定字符串中的所有字符强制转换为小写 |

# 9. 其他数据管理工具

比较数据框和数据表会引出一个有趣的问题。如果存在更多类型的数据呢？尤其是，如果有不同方式存储的数据，而这些方式本质上都是某种表格，会怎么样？除了数据框或表之外，还有许多存储数据的方法，其中很多只是表格。`dplyr`（Wickham 和 Francois，2015）背后的理念是，无论数据后端可能是什么，我们的体验都应该是一样的。为了实现这一点，`dplyr`为常见的数据管理任务实现了泛型函数。对于每个泛型函数，都会编写特定的方法，将通用操作转换为特定后端所需的任何代码或语言。使用抽象层可以确保用户获得一致的体验，无论使用的具体数据格式或后端是什么。这也使得`dplyr`具有可扩展性，因为可以通过简单地编写额外的函数或方法来添加对新格式的支持。用户体验无需改变。对于本章，我们的`checkpoint`头文件需要安装并添加`tibble`和`dplyr`包：

```
library(checkpoint)
checkpoint("2016-09-04", R.version = "3.3.1")
library(dplyr)
library(tibble)
```

`dplyr`包中的函数命名基于完成特定动作的动词。也就是说，`dplyr`不是有几个能完成许多任务的函数，而是有许多通常只完成特定任务的函数。与`data.table`（Dowle、Srinivasan、Short 和 Lianoglou，2015）不同，`dplyr`是函数式的，因为它总是接收输入并返回输出，在这里输出是数据。它不像`data.table`那样直接在内存中修改数据。然而，它仍然相当快，因为许多函数都经过了优化，用 C++ 编写，并且`dplyr`尽量避免不必要的拷贝。

和之前一样，我们设置一些选项来减少打印的行数。`dplyr`的默认数据格式是`tibble`（Wickham、Francois 和 Müller，2016）。Tibble 在`tibble`包中实现。从本质上讲，tibbles 是数据框的扩展，它们有一些额外的限制（例如默认为字符串而不是将字符串强制转换为因子），以及更友好的打印或屏幕显示方法。数据操作由`dplyr`处理，而`tibble`处理数据的实际存储。从技术上讲，`tibble`包实现了 R 对象的`class`以及 tibbles 的一些基本方法。

```
options(stringsAsFactors = FALSE,
tibble.print_max = 20, ## if over 20 rows
tibble.print_min = 5, ## print first 5
digits = 2) ## reduce digits printed by R
iris$Species
1          5.1         3.5          1.4         0.2  setosa
2          4.9         3.0          1.4         0.2  setosa
3          4.7         3.2          1.3         0.2  setosa
4          4.6         3.1          1.5         0.2  setosa
5          5.0         3.6          1.4         0.2  setosa
```