## 第 7 章 `data.table` 简介

我们已经简要介绍了 `data.table` 包。这个包是本章的核心，本章涵盖了在数据管理这一广义术语下访问、编辑和操作数据的基础知识。虽然不引人注目，但数据管理是数据可视化或分析的关键第一步。此外，特定分析项目的大部分时间可能来自数据管理。例如，在 R 中运行线性模型只需要一行代码，前提是数据已经清理并符合 `lm()` 函数期望的格式。数据管理可能具有挑战性，因为原始数据种类、形状和格式多样；缺失数据很常见；你还可能需要合并或组合不同的数据源。在本章中，我们使用 `data.table`[9] 和 `extraoperators`[35] R 包：

```r
options(width = 70)
library(data.table)
library(extraoperators)
```

### 7.1 `data.table` 简介

使用数据表（而非内置的数据框类对象）的好处之一是数据表内存效率更高，操作和修改速度更快。`data.table` 包在很大程度上通过就地修改内存中的数据表来实现这一点，而对于数据框，R 通常会复制一份数据，修改它，然后存储。无论正在修改的是所有列还是，例如，100 列中的 1 列，都会进行完整复制。因此，对数据框的操作往往比数据表的类似操作花费更多时间并占用更多内存。

为了展示数据管理的概念，我们使用小数据，因此内存和处理器时间不是问题。然而，我们强调使用数据表而不是数据框，因为数据表可以优雅地扩展到比数据框大得多的数据量。数据表还有其他好处，包括不需要引用所有变量名，我们将在本章中展示。相比之下，数据框的主要优势在于它们存在于基础 R 中，并且更多人熟悉如何使用它们（例如，如果你分享代码，这很有帮助）。

数据表可能非常大。与其查看所有数据，通常查看 `head()` 和 `tail()` 更有帮助。当你在 R 中通过键入名称显示数据表时，默认返回第一行和最后五行。R 还默认打印七位有效数字，这会使数字条目变得混乱。为了清晰起见，我们使用 `options()` 函数控制一些全局选项，以便仅打印前三行和后三行（如果数据表总行数超过 20 行——否则显示所有行）；此外，数字条目仅显示两位有效数字。设置的同时，我们还引入了 Edgar Anderson 的鸢尾花数据。该数据涉及三种鸢尾花物种的花萼和花瓣的长度和宽度（单位：厘米）。

在将鸢尾花数据转换为数据表之前，我们将物种名称转换为字符类型而非因子类型。在以下代码中，我们设置选项、强制物种名称转换为字符、并创建数据表 `diris`：

```r
options(stringsAsFactors = FALSE,
        datatable.print.nrows = 20,  # 如果超过 20 行
        datatable.print.topn = 3,    # 打印前 3 行和后 3 行
        digits = 2)                  # 减少 R 打印的数字位数
iris$Species <- as.character(iris$Species)
diris <- as.data.table(iris)
diris
```

```
   Sepal.Length Sepal.Width Petal.Length Petal.Width Species
1:          5.1         3.5          1.4         0.2  setosa
2:          4.9         3.0          1.4         0.2  setosa
3:          4.7         3.2          1.3         0.2  setosa
---
     Sepal.Length Sepal.Width Petal.Length Petal.Width    Species
148:          6.5         3.0          5.2         2.0 virginica
149:          6.2         3.4          5.4         2.3 virginica
150:          5.9         3.0          5.1         1.8 virginica
```

数据共有 150 行，涵盖三种鸢尾花：setosa、versicolor 和 virginica。每种花都有 50 组测量数据。需要注意的是，R 运行在内存中，因此巨大的数据表可能会引发问题。要检查内存空间，`tables()` 命令会显示内存中所有数据表及其大小。实际上，`tables()` 本身也是一个数据表。请注意，该函数会提供数据表的名称、行数和列数、以兆字节为单位的大小，以及是否存在键值。我们将在接下来的几个段落中讨论带键值的数据表。目前来看，1MB 的内存不太可能给我们带来问题：

```r
tables()
```

```
     NAME NROW NCOL MB
1:   diris  150    5  0
                                          COLS KEY
1: Sepal.Length,Sepal.Width,Petal.Length,Petal.Width,Species
Total: 0MB
```

如果我们想进一步了解每一列的数据类型，可以在目标数据表上调用 `sapply()` 命令，并配合使用 `class()` 函数。

回忆一下，`apply` 类函数会将第二个形式参数中指定的函数应用到第一个形式参数的元素上，并返回结果。在我们的例子中，我们调用了 `class()` 函数来处理 `diris` 数据表，然后可以看到 `diris` 各列存储的值的类型：

```r
sapply(diris, class)
```

```
Sepal.Length  Sepal.Width Petal.Length  Petal.Width      Species
   "numeric"    "numeric"    "numeric"    "numeric"  "character"
```

与其他数据格式相比，数据表在概念上具有一个强大的优势：数据表可以设置键值。有了键值，就可以使用二分查找。对于不熟悉二分查找的人来说，想象一下需要在电话簿中查找一个名字。如果逐行阅读所有名字，会非常耗时。然而，因为我们知道电话簿是按字母顺序排序的，我们可以立刻找到一个按字母顺序排在中间的名字，判断出我们要找的名字是在其之前还是之后，从而在这个过程中从我们的搜索范围中排除一半的电话簿。我们可以对剩下的一半重复这个算法，这样只需两次操作，就只剩下原始数据四分之一的搜索范围！用更数学化的方式来说，对数据框进行全局搜索的时间复杂度是线性的，取决于行数 `n`，而在数据表中，最坏情况下的时间复杂度是 `log n`。为了让这一点更具体，对于我们使用的某些表格，数据量翻倍可能只会让代码运行时间增加几分钟，而不是让运行时间也翻倍——这是一个巨大的优势。

**注意**：在 `data.table` 中，键值是一个索引，它可能唯一也可能不唯一，由数据中的一个或多个列创建而成。数据会按照键值进行排序，从而允许使用键值进行非常快速的操作。示例操作包括子集化或过滤、为每一个唯一的键值执行计算（例如，一个变量的均值）、以及合并两个或多个数据集。

当然，为数据表设置键值确实需要时间，因此在做出这种选择之前，需要考虑你访问数据的频率。另一个需要考虑的因素是使用什么作为键值。一个表只能有一个键值，尽管这个键值可以由多个列组成。函数 `setkey()` 将数据表作为第一个形式参数，然后可以接受不定数量的列名来设置键值。要查看一个数据表是否有键值，只需在你想要测试的数据表上调用 `haskey()` 即可。最后，如果你想知道构成键值的是哪些列，在数据表上调用 `key()` 函数就能提供这些信息。我们在接下来的几行代码中，对我们的数据表 `diris` 演示了这些命令：

```r
haskey(diris)
```
```
[1] FALSE
```
```r
setkey(diris, Species)
```
```r
key(diris)
```
```
[1] "Species"
```
```r
haskey(diris)
```
```
[1] TRUE
```

键值通常基于标识变量来创建。例如，在研究中，每名研究参与者可能会被分配一个唯一的 ID。在商业案例中，每个客户可能都有一个 ID 号码。数据表通常以这类 ID 作为键值，因为许多操作都是基于这些 ID 来执行的。例如，在医疗场景中进行研究时，你可能会有两个数据表，一个包含参与者填写的问卷数据，另一个包含医疗记录的数据。这两个表可以通过参与者 ID 进行合并。在商业中，每当客户进行新的购买时，可能就会向数据集中添加一行，其中包含购买金额和客户 ID。然而，除了了解单次购买记录外，了解每个客户的总消费额——即每个客户 ID 的购买金额总和——也可能很有帮助。

设置键值并不是必须的；二分排序的本质仅仅需要一个逻辑顺序。`order()` 函数调用就能实现这一点。需要注意，数据表还有另一个很好的特性。回到我们电话簿的类比，想象一下我们想按名字而不是姓氏来排序。那么，复杂的方法是把电话簿里的每个人都排成一排，然后让他们移动到新的顺序位置。

在这个例子中，人就是数据。然而，仅仅重新整理电话簿（涉及的数据要少得多）会容易得多。电话目录是人员物理位置的一个索引。我们可以在索引表中将某些条目上移，而无需改变任何人的实际地址。这再次是一种更快的方法，避免了在内存中删除和重新保存数据。同样，当我们对数据表施加排序时，也是通过引用来完成的。注意，这种新排序会取消我们设置的键值，因为键值的一个关键方面就是数据需要按照该键值进行排序：

```r
diris <- diris[order(Sepal.Length)]
diris
```
```
     Sepal.Length Sepal.Width Petal.Length Petal.Width    Species
  1:          4.3         3.0          1.1         0.1     setosa
  2:          4.4         2.9          1.4         0.2     setosa
  3:          4.4         3.0          1.3         0.2     setosa
 ---
148:          7.7         2.8          6.7         2.0 virginica
149:          7.7         3.0          6.1         2.3 virginica
150:          7.9         3.8          6.4         2.0 virginica
```
```r
haskey(diris)
```
```
[1] FALSE
```

或者，我们可以使用多个变量来对数据表进行排序，甚至可以将默认的升序改为降序。要使用多个列，只需在 `order()` 中传入多个列即可。在列名前添加 `-` 可以按降序排序。以下代码根据花萼长度的升序和花萼宽度的降序对 `diris` 进行排序：

```r
diris <- diris[order(Sepal.Length, -Sepal.Width)]
diris
```
```
     Sepal.Length Sepal.Width Petal.Length Petal.Width    Species
  1:          4.3         3.0          1.1         0.1     setosa
  2:          4.4         3.2          1.3         0.2     setosa
  3:          4.4         3.0          1.3         0.2     setosa
 ---
148:          7.7         2.8          6.7         2.0 virginica
149:          7.7         2.6          6.9         2.3 virginica
150:          7.9         3.8          6.4         2.0 virginica
```

如果我们重新设置键值，它会破坏我们之前的排序。虽然这在我们正常调用 `diris` 时可能不会很明显，但如果只查看正确的行数，我们会发现花萼长度严格的升序被打破了：

```r
setkey(diris, Species)
diris[49:52]
```
```
   Sepal.Length Sepal.Width Petal.Length Petal.Width   Species
1:          5.7         3.8          1.7         0.3    setosa
2:          5.8         4.0          1.2         0.2    setosa
3:          4.9         2.4          3.3         1.0 versicolor
4:          5.0         2.3          3.3         1.0 versicolor
```

这告诉我们，关于排序和键的选择是有代价的。数据表速度提升的部分原因在于能够执行快速搜索。选择键需要一次性计算成本，因此我们明智地选择键是有利的。iris 数据集足够小，在我们学习时可以使这些成本无关紧要，但现实生活中的数据集不太可能如此宽容。选择键还会影响我们用于理解数据表某些方面的函数的默认选择。设置键不仅是为了提供数据排序，也是为了表明我们将键作为数据决定性因素的意图。`anyDuplicated()` 函数调用展示了这一点，它返回第一个重复行的索引。由于我们将键设置为 `Species`，它默认只在该列中查找。

注意，`Species` 的第一个重复出现在第二行，而 `Sepal.Length` 的第一个重复条目出现在第三行：

```r
anyDuplicated(diris)
```

## 1. 使用键来确保唯一性

`anyDuplicated(diris$Sepal.Length)`

重复函数族中可能受键影响的其他函数包括 `duplicated()` 本身，它为每一行返回一个布尔值。在较新版本的 `data.table` 中，默认情况下，`duplicated()` 会将行的所有元素与之前的行进行比较。请注意，这与过去 `duplicated()` 的工作方式有所变化。

也就是说，通过使用第二个形参 `by = key()`，此函数会遵循键值。在这种情况下，对 `diris` 的此类调用将为第 1、51 和 101 行分别返回 `FALSE`，因为我们每种物种有 50 行，并且按物种排序，因此我们将其放入一个漂亮的表格中：

```
haskey(diris)
## [1] TRUE
key(diris)
## [1] "Species"
table(duplicated(diris))                # 不遵循键
##
## FALSE TRUE
##   149    1
table(duplicated(diris, by = key(diris))) # 遵循键
##
## FALSE TRUE
##    3  147
```

类似地，`unique()` 函数的现代行为默认忽略键，只会从数据集中删除第 79 行。然而，通过使用 `by = key()`，每个新键的第一个实例才会出现：

```
unique(diris, by = key(diris))
##
##    Sepal.Length Sepal.Width Petal.Length Petal.Width   Species
## 1:          4.3         3.0          1.1         0.1    setosa
## 2:          4.9         2.4          3.3         1.0 versicolor
## 3:          4.9         2.5          4.5         1.7  virginica
```

拥有数据表的好处之一就是能够定义键。键的选择应能代表数据表的真正唯一性。在某些类型的数据中，键可能很明显。分析员工记录可能会使用员工身份证号码或政府纳税人识别号。然而，在其他场景（例如本场景）中，键可能不那么明显。实际上，正如我们在前面对已设置键的 `diris` 数据表调用 `unique()` 的结果中所见，只返回了三行。这可能并不能真正代表任何真正的唯一性。在这种情况下，使用多个列来创建键可能对我们有好处，尽管哪些列相关完全取决于可用数据和最终分析的目标。

## 2. 使用第一个形参选择和子集化数据

数据表的设计是为了方便快速地访问数据。实现这一点的方式之一是没有正式的行名。实际上，键的作用之一可以被视为提供有用的行名，而不是任意的索引。数据表对象的总体格式是 `Data.Table[i, j, by]`，其中 `i` 期望行信息。

特别地，数据表具有一定的自我意识，它们将列名理解为变量，而无需使用 `$` 运算符。如果未指定列变量，则假定我们试图通过行进行调用：

**注意**：通常通过传递包含以下内容之一的向量来选择数据表中的行：(1) 指示要选择的行号的数字，`d[1:5]`；(2) 指示要排除的行号的负数，`d[-(1:5)]`；(3) 指示是否应包含每一行的逻辑向量，`d[v == 1]`，通常使用数据集的列 `v` 进行逻辑测试。

```
diris[1:5]
##
##    Sepal.Length Sepal.Width Petal.Length Petal.Width Species
## 1:          4.3         3.0          1.1         0.1  setosa
## 2:          4.4         3.2          1.3         0.2  setosa
## 3:          4.4         3.0          1.3         0.2  setosa
## 4:          4.4         2.9          1.4         0.2  setosa
## 5:          4.5         2.3          1.3         0.3  setosa
```

注意，如果这是一个数据框，我们会得到整个数据框，因为我们是在调用列，而我们只有五列。在这里，在数据表中，由于我们没有调用特定的列名，因此我们得到的是前五行。与递增或递减类似，我们也可以通过 `-` 运算符执行反向选择。在这种情况下，通过指定我们想要第 1:148 行的补集，我们只得到最后两行。注意左侧的行号。这些是第 149 行和第 150 行，然而，由于数据表不关心行名，它们在打印到屏幕时会即时重新编号。在选择数据表中的行时，谨慎行事是明智的：

```
diris[-(1:148)]
##
##    Sepal.Length Sepal.Width Petal.Length Petal.Width   Species
## 1:          7.7         2.6          6.9         2.3 virginica
## 2:          7.9         3.8          6.4         2.0 virginica
```

更常见的是，我们按列名或键进行选择。由于我们将键设置为 `Species`，选择单个物种变得非常容易。我们只需请求匹配的字符串即可。现在，在本章开头，我们确保物种是字符串。选择这个原因在以下代码中显而易见，能够轻松选择我们想要的键值非常方便。注意第二个命令使用了否定运算符 `!`，表示我们想要选择所有不属于我们键入的字符串的键元素：

```
diris["setosa"]
##
##    Sepal.Length Sepal.Width Petal.Length Petal.Width Species
## 1:          4.3         3.0          1.1         0.1  setosa
## 2:          4.4         3.2          1.3         0.2  setosa
## 3:          4.4         3.0          1.3         0.2  setosa
## ---
## 48:         5.7         4.4          1.5         0.4  setosa
## 49:         5.7         3.8          1.7         0.3  setosa
## 50:         5.8         4.0          1.2         0.2  setosa
```

```
diris[!"setosa"]
##
##    Sepal.Length Sepal.Width Petal.Length Petal.Width   Species
## 1:          4.9         2.4          3.3         1.0 versicolor
## 2:          5.0         2.3          3.3         1.0 versicolor
## 3:          5.0         2.0          3.5         1.0 versicolor
## ---
## 98:         7.7         2.8          6.7         2.0  virginica
## 99:         7.7         2.6          6.9         2.3  virginica
## 100:        7.9         3.8          6.4         2.0  virginica
```

当然，虽然到目前为止我们选择使用键元素，但这绝不是必须的。对我们的任何列进行逻辑索引都非常自然。总体格式是将命名列的元素与设置的逻辑测试进行比较。最终结果只显示评估为 `TRUE` 的行子集。对于不等式、多个参数和严格相等（本质上是常用的比较运算符），数据表的运行方式与你预期一致。此外，再次注意，使用数据表时，我们不需要引用变量 `Sepal.Length` 所在的对象。它默认在数据表内进行求值：

```
diris[Sepal.Length < 5]
##
##    Sepal.Length Sepal.Width Petal.Length Petal.Width   Species
## 1:          4.3         3.0          1.1         0.1    setosa
## 2:          4.4         3.2          1.3         0.2    setosa
## 3:          4.4         3.0          1.3         0.2    setosa
## ---
## 20:         4.9         3.0          1.4         0.2    setosa
## 21:         4.9         2.4          3.3         1.0 versicolor
## 22:         4.9         2.5          4.5         1.7  virginica
```

```
diris[Sepal.Length < 5 & Petal.Width < .2]
##
##    Sepal.Length Sepal.Width Petal.Length Petal.Width Species
## 1:          4.3         3.0          1.1         0.1  setosa
## 2:          4.8         3.0          1.4         0.1  setosa
## 3:          4.9         3.6          1.4         0.1  setosa
## 4:          4.9         3.1          1.5         0.1  setosa
```

```
diris[Sepal.Length == 4.3 | Sepal.Length == 4.4]
##
##    Sepal.Length Sepal.Width Petal.Length Petal.Width Species
## 1:          4.3         3.0          1.1         0.1  setosa
## 2:          4.4         3.2          1.3         0.2  setosa
## 3:          4.4         3.0          1.3         0.2  setosa
## 4:          4.4         2.9          1.4         0.2  setosa
```

不过，这里引入了一些新的运算符。在前面的代码中，我们重复输入了 `Sepal.Length`，显得有些冗长。试想一下数据表存储的常见场景：假设我们偶尔会修改一个员工列表。我们可能只需将员工的唯一标识符存入该列表，然后按需从数据表中提取数据。当然，在这个例子中，我们处理的是花萼长度而非员工，我们只想获取列表中那些长度值所在的行。使用的命令是 `%in%`，其左侧接受列名，右侧接受一个向量。自然地，我们可能想要获取该集合的补集，此时可以使用“不在”运算符 `%nin%`。以下代码展示了两种结果：

```
interest <- c(4.3, 4.4)
diris[Sepal.Length %in% interest]
```

```
##    Sepal.Length Sepal.Width Petal.Length Petal.Width Species
## 1:          4.3         3.0          1.1         0.1  setosa
## 2:          4.4         3.2          1.3         0.2  setosa
## 3:          4.4         3.0          1.3         0.2  setosa
## 4:          4.4         2.9          1.4         0.2  setosa
```

```
diris[Sepal.Length %nin% interest]
```

```
##    Sepal.Length Sepal.Width Petal.Length Petal.Width   Species
## 1:          4.5         2.3          1.3         0.3    setosa
## 2:          4.6         3.6          1.0         0.2    setosa
## 3:          4.6         3.4          1.4         0.3    setosa
## ---
## 144:         7.7         2.8          6.7         2.0  virginica
## 145:         7.7         2.6          6.9         2.3  virginica
## 146:         7.9         3.8          6.4         2.0  virginica
```

## 3. 使用第二个形参

到目前为止，我们已经提到数据表没有原生的行名，但我们实际上已经通过多种方式选择了特定的行。回顾我们的通用布局 `Data.Table[i, j, by]`，它允许我们在第一个形参中选择列对象。现在我们把注意力转向第二个形参位置 `j`，目的是只选择部分列而不是所有列。请记住，数据表预设在其内部传递的变量存在于数据表的环境中。因此，要获取单个列中的所有变量变得非常容易：

**注意**：变量通过输入不带引号的变量名 `d[, variable]` 来选择，或者通过 `.( )` 内逗号分隔的多个变量 `d[, .(v1, v2)]` 来选择。

```
diris[, Sepal.Length]
```

```
##   [1] 4.3 4.4 4.4 4.4 4.5 4.6 4.6 4.6 4.6 4.7 4.7 4.8 4.8 4.8 4.8 4.8
##  [17] 4.9 4.9 4.9 4.9 5.0 5.0 5.0 5.0 5.0 5.0 5.0 5.0 5.1 5.1 5.1 5.1
##  [33] 5.1 5.1 5.1 5.1 5.2 5.2 5.2 5.3 5.4 5.4 5.4 5.4 5.4 5.5 5.5 5.7
##  [49] 5.7 5.8 4.9 5.0 5.0 5.1 5.2 5.4 5.5 5.5 5.5 5.5 5.5 5.6 5.6 5.6
##  [65] 5.6 5.6 5.7 5.7 5.7 5.7 5.7 5.8 5.8 5.8 5.9 5.9 6.0 6.0 6.0 6.0
##  [81] 6.1 6.1 6.1 6.1 6.2 6.2 6.3 6.3 6.3 6.4 6.4 6.5 6.6 6.6 6.7 6.7
##  [97] 6.7 6.8 6.9 7.0 4.9 5.6 5.7 5.8 5.8 5.8 5.9 6.0 6.0 6.1 6.1 6.2
## [113] 6.2 6.3 6.3 6.3 6.3 6.3 6.3 6.4 6.4 6.4 6.4 6.4 6.5 6.5 6.5 6.5
## [129] 6.7 6.7 6.7 6.7 6.7 6.8 6.8 6.9 6.9 6.9 7.1 7.2 7.2 7.2 7.3 7.4
## [145] 7.6 7.7 7.7 7.7 7.7 7.9
```

```
is.data.table(diris[,Sepal.Length])
## [1] FALSE
```

另一方面，如前面的向量返回结果所示，输入单个列名并不会返回一个数据表。如果需要返回数据表，有多种方法可以解决。回顾一下，数据表力求在计算速度和编程速度上都表现优异。我们通过将列放入列表中来提取单个或多个列，同时保留数据表结构。这种将 `list()` 作为参数传递给数据的常见需求，在 data.table 中有一个简洁的简写函数调用 `.( )`。我们在以下代码中展示了如何传递一个包含一个或多个元素的列表，这些元素调用特定的列名。两种方式都返回数据表：

```
diris[, .(Sepal.Length, Sepal.Width)]
```

```
##    Sepal.Length Sepal.Width
## 1:          4.3         3.0
## 2:          4.4         3.2
## 3:          4.4         3.0
## ---
## 148:         7.7         2.8
## 149:         7.7         2.6
## 150:         7.9         3.8
```

```
diris[, .(Sepal.Length)]
```

```
##    Sepal.Length
## 1:          4.3
## 2:          4.4
## 3:          4.4
## ---
## 148:         7.7
## 149:         7.7
## 150:         7.9
```

## 4. 使用第二和第三个形参

我们一直将列名作为可直接访问的变量来使用。它们会在我们的数据表 `diris` 的框架内被求值。这个特性可以通过 `with` 参数来开启或关闭，`with` 是我们在通用布局 `Data.Table[i, j, by]` 中最后一个形参的选项之一。默认值是 `with = TRUE`，此时默认行为是在数据表 `diris` 的框架内对 `j` 形参进行求值。将值改为 `with = FALSE` 会改变默认行为。这种差异类似于在 R 交互式环境中工作时的情形。我们将文本 `example` 赋值给一个变量 `x`。如果在 R 控制台中输入不带引号的 `x`，R 会查找名为 `x` 的对象。如果输入带引号的 `"x"`，R 会将其视为一个字符串，而不是 R 对象的名称：

```
x <- "example"
x
## [1] "example"
"x"
## [1] "example"
```

# 第7章 data.table简介

## 1. `x`

当 `with = TRUE`（默认值）时，数据表的行为类似于交互式 R 控制台。不带引号的变量名会被当作数据表中的变量进行搜索。带引号的变量名或数字则会被直接当作向量本身进行求值。当 `with = FALSE` 时，数据表的行为更像数据框，不会将数字或字符当作向量本身处理，而是搜索与之匹配的列名或列位置。在此上下文中，我们的数据表期望 `j` 是一个数字形式的列位置，或者是列名的字符向量。两种情况下，返回的仍然是数据表：

```r
diris[, 1, with = FALSE]
```

```r
diris[, "Sepal.Length", with = FALSE]
```

如果列需要动态选择，这会成为一个非常有用的特性。字符字符串通过变量传递给数据表，例如以下代码：`v <- "Sepal.Length"`

```r
diris[, v, with = FALSE]
```

当然，如果我们不通过第三个形参关闭默认行为，可能会导致意想不到的结果。需要小心谨慎，以确保得到你想要的数据。不过，请记住每种方式返回的是什么；以下结果实际上既不是错误也不是失误。它们被证明非常有用：

```r
diris[, v]
```

```r
diris[, 1]
```

正如你所见，我们可以根据需求，通过多种方式调用一个或多个列。回顾一下使用 `-` 从数据表中进行减法的有用符号，我们还可以从数据表中移除一个或多个列，从而获取剩余的所有列。特别要注意的是，如果需要，我们可以使用变量 `v` 来保存多个列，这样就能轻松地保留一个需要移除的列列表。例如，这可以用于删除数据中的敏感信息，使其从仅供内部使用变为可供外部使用。在以下的减法示例中，为了节省空间和篇幅，我们只请求第一行：

```r
diris[1, -"Sepal.Length", with=FALSE]
```

```r
diris[1, !"Sepal.Length", with=FALSE]
```

```r
diris[1, -c("Sepal.Length", "Petal.Width"), with=FALSE]
```

```r
diris[1, -v, with=FALSE]
```

相比之下，有时我们确实希望以向量的形式返回列，例如 `diris[,Sepal.Length]`。在这种情况下，有几种简便的方法可以访问该向量。这里，我们将所有函数调用的示例与 `head()` 函数结合，以截断输出。请注意，第二个示例在你需要以向量形式对该列进行变量访问时非常方便，而最后一个示例则提醒我们，数据表是基于数据框构建的：

```r
head(diris[["Sepal.Length"]])
head(diris[[v]])
head(diris[, Sepal.Length])
head(diris$Sepal.Length)
```

### 7.3 变量重命名与排序

数据表包含变量和列的顺序。有时，可能需要更改该顺序或重命名变量。在本节中，我们将展示一些精确实现此目的的技术，并尽量保持注释简洁，让代码自己说话。

**注意：** 数据表中的变量名可以通过 `names(data)` 查看，使用 `setnames(data, oldnames, newnames)` 更改，并使用 `setcolorder(data, neworder)` 重新排序。

虽然我们尚未在本书中使用此函数，但 `names()` 是 R 基础代码的一部分，是一个泛型函数。数据表只有列名，因此基础包中的 `colnames()` 函数调用也会返回相同的结果：

```r
names(diris)
colnames(diris)
```

对于 R 基础代码中的对象（如数据框），`names()` 甚至可以用来设置新值。然而，数据表的运作方式不同，为了实现内存效率，它使用了不同的函数。请记住，数据表会尽可能避免复制数据，而只是更改引用指针。通常，在 R 中重命名对象会复制数据，这不仅需要时间，还会占用内存空间。

数据表对应的函数调用是 `setnames()`，其形式参数包括数据表、旧列名和新列名：

```r
setnames(diris, old = "Sepal.Length", new = "SepalLength")
names(diris)
```

我们也可以使用数字位置而非变量名来引用旧列。当列名过长或事先知道数据位置时，这会很方便：

```r
setnames(diris, old = 1, new = "SepalL")
names(diris)
```

此外，列也可以完全重新排序。同样，我们在命令前加上 `set` 前缀，因为 `setcolorder()` 命令在设置列顺序的同时，避免了任何底层数据的复制。所有改变的仅是指向各列的指针顺序。

因此，这是数据表的一个快速命令，其速度仅取决于列的数量，而非列内元素的数量：

```r
setcolorder(diris, c("SepalL", "Petal.Length", "Sepal.Width", "Petal.Width", "Species"))
diris
```

与 `setnames()` 一样，`setcolorder()` 也可使用数字位置而非变量名。我们在此也提一下，所有这些都是通过引用更改完成的，并且我们的数据表 `diris` 在整个过程中始终保持键控状态：

```r
v <- c(1, 3, 2, 4, 5)
setcolorder(diris, v)
diris
```

### 7.4 创建变量

接下来，我们将注意力转向在数据表内创建新变量。回想一下 for 循环结构，其目标是遍历数据表的每一行并进行一些分析或更改。数据表具有非常高效的代码来精确实现此目的，而无需正式调用 for 循环甚至使用 `*apply()` 系列函数。

所谓高效，指的是在所有或几乎所有情况下，速度都比这两种方法更快。

为了稳妥起见，我们根据原始的 `iris` 数据集重新创建并重新设置数据表的键，以消除我们在前面章节中所做的任何更改：

```r
diris <- as.data.table(iris)
setkey(diris, Species)
```

**注意：** 在数据表中，通过使用 `j` 形式参数中的 `:=` 运算符来创建新变量，即 `data[, newvar := value]`。可以使用数据集中的变量，也可以使用函数。

在数据表中创建新变量就是创建一个新列。因此，我们在通用结构 `Data.Table[i, j, by]` 的第二个形式参数（即 `j` 区域）中进行操作。

列创建涉及使用按引用赋值运算符 `:=`。列创建可以采取不同的形式，具体取决于我们希望创建的内容。我们可以仅创建一个列并用零填充，创建多个具有不同变量的列，甚至基于现有列的计算来创建一个新列。请注意，所有这些操作都位于数据表调用的第二个形式参数位置，并要特别注意我们一次创建多个列（命名为 `X1` 和 `X2`）的方式，即在左侧使用 `.()` 传递列名向量，在右侧传递列表形式的列值：

```r
diris[, V0 := 0]
diris[, Sepal.Length := NULL]
diris[, c("X1", "X2") := .(1L, 2L)]
diris[, V := Petal.Length * Petal.Width]
diris
```

向 `NULL` 赋值会删除列。这种完全删除的方式会移除恢复该列的任何实际能力，但速度很快。当然，另一种选择是使用 `-` 生成原始数据表的一个子集，然后将其复制到另一个数据表。

区别在于速度和内存使用。制作一个副本可能会使内存使用量几乎翻倍，并且复制所需的时间与总元素数量呈线性关系。当然，如果你希望或需要恢复所有原始数据，制作副本可以保留原始数据。可以通过向 `NULL` 赋值一次性删除多个列。我们仅显示第一行以验证删除成功：

```r
diris[, c("V", "V0") := NULL]
diris[1]
```

能够基于当前列的计算生成新列是非常有益的。此外，此过程可以很容易地通过第一个形式参数区域的命令进行修改，该区域允许我们选择关键数据结果。回想一下，我们的键是物种，其中一个是 `setosa`。我们通过两个当前列的乘法运算重新创建已删除的 `V` 列，并且仅对具有 `setosa` 物种键的行执行此操作。请注意，不匹配键的行会填充 `NA`：

```r
diris["setosa", V := Petal.Length * Petal.Width]
unique(diris)
```

然而，如果我们后来决定 `virginica` 也应在 `V` 列中拥有一些值，这是可以做到的，并且不必采用与 `setosa` 相同的计算方法。我们再次使用 `unique()` 函数调用，仅查看每个物种的第一个示例。虽然这个示例是人为构造的，但请想象一下，基于数据中已存储的坐标创建了一个距离列。根据键值，某些距离计算可能应采用曼哈顿距离，而另一些自然应采用欧几里得距离：

```r
diris["virginica", V := sqrt(Petal.Length * Petal.Width)]
unique(diris)
```

## 143: 3.0 5.1 1.8 virginica 1 2 3.03

此类计算并不仅限于创建列。假设我们想知道 `Sepal.Width` 中所有值的均值。该计算可以通过多种方式执行，每种方式可能因你的最终目标而各有优势。回忆一下，数据表也是数据框；我们可以对合适的数据框样式调用我们的算术均值函数。然而，由于我们想要找出某一特定列值的算术平均值，我们也可以从第二个形参中调用它。还记得我们之前提到过对 `j` 位置中的项需要谨慎吗？可以将 `j` 中的值作为向量进行访问。相比之下，如果我们希望返回一个数据表，这也是可以做到的。毕竟，这三种方法都返回相同的均值。不过，最后一种方法可能最具启发性：

```r
mean(diris$Sepal.Width)
## [1] 3.1

diris[, mean(Sepal.Width)]
## [1] 3.1

diris[, .(M = mean(Sepal.Width))]
## M
## 1: 3.1
```

最后一行可能是最好的，因为它易于扩展。假设我们想计算前文代码中的算术均值，但要按物种进行分组。这就需要同时使用第二个和第三个形参。再假设我们还想知道不止一个列的均值。那么，`.()` 记法将允许我们以一种便捷的方式将该新信息存储为数据表格式，从而使其随时可用于分析。注意，现在我们得到了物种内部的均值，而在代码的第二部分中，我们得到了不止一个列的均值，并且在这两种情况下，返回的结果都是数据表。

因此，我们可以像之前使用 `diris` 一样，从这些新构造中访问信息。就像爱丽丝掉进兔子洞到达仙境一样，这变成了一个“你想探索多深？”的问题，而不是“我们能探索多深？”。

```r
diris[, .(M = mean(Sepal.Width)), by = Species]

## Species M
## 1: setosa 3.4
## 2: versicolor 2.8
## 3: virginica 3.0

diris[, .(M1 = mean(Sepal.Width),
          M2 = mean(Petal.Width)), by = Species]

## Species M1 M2
## 1: setosa 3.4 0.25
## 2: versicolor 2.8 1.33
## 3: virginica 3.0 2.03
```

为了展示我们所构建的这些新数据表的全部功能，假设我们想看看不同物种的萼片宽度和花瓣宽度的均值之间是否存在任何相关性。再进一步想象，我们可能会在将来某个时间点，除了相关性分析之外，还会用到这个均值数据。我们可以将前面代码片段中的第二个数据表视为一个数据表（因为它本来就是），并使用第二个形参来运行相关性分析。此代码遵循 `Data.Table[i, j, by][i, j, by]` 的布局，用文字描述起来相当冗长；请查看以下代码以了解其工作原理：

```r
diris[, .(M1 = mean(Sepal.Width),
          M2 = mean(Petal.Width)), by = Species][,
  .(r = cor(M1, M2))]

## r
## 1: -0.76
```

变量可以创建变量。换句话说，可以创建一个新变量，用于指示某个花瓣宽度是大于还是小于该特定物种的花瓣宽度中位数。在接下来的代码中，按 `Species` 计算 `median(Petal.Width)`，并将布尔值存储在新创建的列 `MedPW` 中，总共 150 行：

```r
diris[, MedPW := Petal.Width > median(Petal.Width), by = Species]

diris
## Sepal.Width Petal.Length Petal.Width Species X1 X2 V MedPW
## 1: 3.5 1.4 0.2 setosa 1 2 0.28 FALSE
## 2: 3.0 1.4 0.2 setosa 1 2 0.28 FALSE
## 3: 3.2 1.3 0.2 setosa 1 2 0.26 FALSE
## ---
## 148: 3.0 5.2 2.0 virginica 1 2 3.22 FALSE
## 149: 3.4 5.4 2.3 virginica 1 2 3.52 TRUE
## 150: 3.0 5.1 1.8 virginica 1 2 3.03 FALSE
```

正如可以向第二个形参传递多个参数一样，也可以通过相同的 `.()` 列表函数调用向第三个形参传递多个参数。再次强调，因为返回的是一个数据表，所以我们可以将其视为带有方括号的数据表，并对结果执行另一层计算：

```r
diris[, .(M1 = mean(Sepal.Width),
          M2 = mean(Petal.Width)),
      by = .(Species, MedPW)]

## Species MedPW M1 M2
## 1: setosa FALSE 3.4 0.19
## 2: setosa TRUE 3.5 0.37
## 3: versicolor TRUE 3.0 1.50
## 4: versicolor FALSE 2.6 1.19
## 5: virginica TRUE 3.1 2.27
## 6: virginica FALSE 2.8 1.81

diris[, .(M1 = mean(Sepal.Width),
          M2 = mean(Petal.Width)),
      by = .(Species, MedPW)][,
  .(r = cor(M1, M2)), by = MedPW]

## MedPW r
## 1: FALSE -0.78
```

### 2：TRUE -0.76

在 `j` 参数中进行计算是数据表最强大的功能之一。例如，我们演示了相对简单的均值、中位数和相关系数计算。然而，几乎任何函数都可以使用。唯一的注意事项是，如果你希望输出也是一个数据表，函数应返回或经过操作后返回适合数据表的输出。例如，一个线性模型对象（长度各异的元素列表）转换为数据表会很麻烦，因为它不是表格数据。不过，你可以轻松地将回归系数提取为向量，然后将其转换为数据表。

### 7.5 数据合并与重塑

数据处理通常涉及将不同来源的数据合并到单个对象中，或者将对象中的数据重塑为不同格式。在合并数据时，有三种不同的合并类型：一对一、一对多（或多对一）以及多对多。本节将介绍这些合并方式。其一般过程是查看两个或多个数据集中的数据，然后比较键，目标是根据行成功连接信息。这称为水平合并，因为它增加了目标数据集的水平长度。

#### 合并数据

每个数据集中每个键都唯一的合并属于一对一形式。例如，合并基于政府社保号（SSN）键的数据集，一个包含合法居住地址，另一个包含首选电子邮件地址。在这种情况下，我们期望真实世界的数据集中每个 SSN 只有一个地址，而首选电子邮件数据集每个 SSN 也只有一行。相比之下，如果我们有官方居住地址数据集以及所有已知的电子邮件地址（其中每个与一个 SSN 关联的新电子邮件地址都占一行），则可能发生一对多或多对一合并。如果目标是将所有电子邮件附加到单个真实世界地址行，那么就是多对一，并且像 `Email001` 和 `Email002` 这样的列名可能会坍缩到一行。另一方面，如果目标是将一个真实世界地址附加到每个电子邮件，那么可能只添加一个新列，尽管数据会复制到电子邮件地址的每一行。最后，还有多对多合并，其中多行数据附加到多行。

值得注意的是，合并的主要困难可能在于理解正在进行的操作，而不是理解代码本身。对于数据表，函数调用是 `merge()`，参数控制所执行的合并类型。由于数据表基于键的特性，按键选择合并可以非常高效。选择按非键列合并也是可行的，但计算效率会较低。

在进入第一个合并代码示例之前，我们生成四个包含各自独特信息的数据表。它们都以 `iris` 数据集中的 `Species` 为键。在实际应用中，几乎不需要这样的代码，因为数据表通常是已经分离好的。此处包含此代码仅是为了合并结果的可复现性。另请注意，这些是非常短的数据集；目的是让它们可以被独立查看并完全理解。这为理解最终的合并结果提供了最佳环境：

```
diris <- diris[, .(Sepal.Width = Sepal.Width[1:3]), keyby = Species]

diris2 <- unique(diris)

dalt1 <- data.table(
  Species = c("setosa", "setosa", "versicolor", "versicolor",
              "virginica", "virginica", "other", "other"),
  Type = c("wide", "wide", "wide", "wide",
           "narrow", "narrow", "moderate", "moderate"),
  MedPW = c(TRUE, FALSE, TRUE, FALSE, TRUE, FALSE, TRUE, FALSE))
setkey(dalt1, Species)

dalt2 <- unique(dalt1)[1:2]
```

```
diris
## Species Sepal.Width
## 1: setosa 3.5
## 2: setosa 3.0
## 3: setosa 3.2
## 4: versicolor 3.2
## 5: versicolor 3.2
## 6: versicolor 3.1
## 7: virginica 3.3
## 8: virginica 2.7
## 9: virginica 3.0
```

```
diris2
## Species Sepal.Width
## 1: setosa 3.5
## 2: setosa 3.0
## 3: setosa 3.2
## 4: versicolor 3.2
## 5: versicolor 3.1
## 6: virginica 3.3
## 7: virginica 2.7
## 8: virginica 3.0
```

```
dalt1
## Species Type MedPW
## 1: other moderate TRUE
## 2: other moderate FALSE
## 3: setosa wide TRUE
## 4: setosa wide FALSE
## 5: versicolor wide TRUE
## 6: versicolor wide FALSE
## 7: virginica narrow TRUE
## 8: virginica narrow FALSE
```

```
dalt2
## Species Type MedPW
## 1: other moderate TRUE
## 2: other moderate FALSE
```

**注意**：一对一的合并需要两个已设置键的数据表，且每行具有唯一的键：`merge(x = dataset1, y = dataset2)`。合并会精确地将一个数据表中的一行与另一数据表匹配，结果包含两个数据表中的所有列（变量）。是否包含不匹配的键由 `all` 参数控制，其默认值为 `FALSE`，仅包含匹配的键，但可设置为 `TRUE` 以同时包含不匹配的键。

在一对一合并中，每个键至多有一行。以 `diris2` 为例，共有三行，每行都有一个独特的物种。类似地，`dalt2` 中有两行，每行也都有一个独特的物种。这两个数据集之间唯一的公共键是 `setosa`。对这个一对一数据调用 `merge()` 会比较键，默认情况下会创建一个仅合并那些键匹配的行的新数据表。在此例中，合并结果是一个只有一行的小数据表。原始的两个数据表在此操作中不会改变。`merge()` 调用至少需要两个参数；第一个是 `x`，第二个是 `y`，它们代表要合并的两个数据集：

```
merge(diris2, dalt2)
## Empty data.table (0 rows and 4 cols): Species,Sepal.Width,Type,MedPW
```

请注意，此过程创建了一个完整的数据集。然而，它非常小，因为返回的结果只包含那些可能完全匹配的行。

根据具体情况，从一对一合并中可能需要不同的结果。可能希望保留 `diris2` 的所有行，无论它们是否与 `dalt2` 匹配。反之亦然，也可能希望保留 `dalt2` 的所有行，无论它们是否与 `diris2` 匹配。最后，为了获得所有可能的数据，可能希望保留出现在任一数据集中的全部行。这些目标可以通过 `all = TRUE` 形式参数的不同变体来实现。具体来说，请注意如何通过 `all` 参数的 `.x` 或 `.y` 后缀来控制保留哪个单一数据集作为关键数据集。如果特定行或变量没有可用数据，则单元格会被填充为缺失值（`NA`）：

```
merge(diris2, dalt2, all.x = TRUE)
## Species Sepal.Width Type MedPW
## 1: setosa 3.5 <NA> NA
## 2: setosa 3.0 <NA> NA
## 3: setosa 3.2 <NA> NA
## 4: versicolor 3.2 <NA> NA
## 5: versicolor 3.1 <NA> NA
## 6: virginica 3.3 <NA> NA
## 7: virginica 2.7 <NA> NA
## 8: virginica 3.0 <NA> NA
```

```
merge(diris2, dalt2, all.y = TRUE)
## Species Sepal.Width Type MedPW
## 1: other NA moderate TRUE
## 2: other NA moderate FALSE
```

```
merge(diris2, dalt2, all = TRUE)
## Species Sepal.Width Type MedPW
## 1: other NA moderate TRUE
## 2: other NA moderate FALSE
## 3: setosa 3.5 <NA> NA
## 4: setosa 3.0 <NA> NA
## 5: setosa 3.2 <NA> NA
## 6: versicolor 3.2 <NA> NA
## 7: versicolor 3.1 <NA> NA
## 8: virginica 3.3 <NA> NA
## 9: virginica 2.7 <NA> NA
## 10: virginica 3.0 <NA> NA
```

## 10: virginica 3.0 <NA> NA

**注意**：一对多或多对一的合并涉及一个数据表，其中每行都有一个唯一键，而另一个数据表中多行可能共享同一个键。具有唯一键的数据表中的行会根据需要重复，以匹配具有重复键的数据表。在 R 中，一对一合并使用的代码与此相同：`merge(x = dataset1, y = dataset2)`。

涉及 `diris` 和 `dalt2` 的合并属于一对多或多对一类型。

同样，当涉及只保留完全匹配项或允许各种不完整数据在合并中保留时，返回结果的行为可以控制。提供的三个示例并非详尽无遗，但它们代表了不同可能性。最后两种情况可能并不可取，因为 `other` 只有一行，而大多数物种都有三行：
```
merge(diris, dalt2)

## Empty data.table (0 rows and 4 cols): Species,Sepal.Width,Type,MedPW

merge(diris, dalt2, all.y = TRUE)

## Species Sepal.Width Type MedPW
## 1: other NA moderate TRUE
## 2: other NA moderate FALSE

merge(diris, dalt2, all = TRUE)

## Species Sepal.Width Type MedPW
## 1: other NA moderate TRUE
## 2: other NA moderate FALSE
## 3: setosa 3.5 <NA> NA
## 4: setosa 3.0 <NA> NA
## 5: setosa 3.2 <NA> NA
## 6: versicolor 3.2 <NA> NA
## 7: versicolor 3.2 <NA> NA
## 8: versicolor 3.1 <NA> NA
## 9: virginica 3.3 <NA> NA
## 10: virginica 2.7 <NA> NA
## 11: virginica 3.0 <NA> NA
```
到目前为止，已经演示了一对一和一对多合并。这些类型的合并（或称连接）最终合并的行数最多为两个数据表行数之和。只要合并的至少一个维度上限为一，就没有问题。然而，在多对多的场景中，大小的上限变为两个数据表行数的乘积。在许多研究环境中，多对多合并相对少见。因此，数据表的合并默认行为是防止这种结果，`allow.cartesian = FALSE` 是通过作为 `merge()` 的正式参数来强制执行这一机制的机制。对于其他数据结构则并非如此，这是数据表特有的。

尝试合并 `diris` 和 `dalt1` 会因这个默认设置而报错，该设置不允许合并后的表行数大于总行数之和。由于 `diris` 中有三个 `setosa` 键，`dalt1` 中有两个 `setosa` 键，合并后的表仅针对 `setosa` 就会产生 3 x 2 = 6 行。由于多对多合并涉及总行数的乘法，很容易导致得到一个非常大的数据库。允许这样做会得到以下结果。注意这个结果只包含完全匹配的行：
```
merge(diris, dalt1, allow.cartesian = TRUE)

## Species Sepal.Width Type MedPW
## 1: setosa 3.5 wide TRUE
## 2: setosa 3.5 wide FALSE
## 3: setosa 3.0 wide TRUE
## 4: setosa 3.0 wide FALSE
## 5: setosa 3.2 wide TRUE
## 6: setosa 3.2 wide FALSE
## 7: versicolor 3.2 wide TRUE
## 8: versicolor 3.2 wide FALSE
## 9: versicolor 3.2 wide TRUE
## 10: versicolor 3.2 wide FALSE
## 11: versicolor 3.1 wide TRUE
## 12: versicolor 3.1 wide FALSE
## 13: virginica 3.3 narrow TRUE
## 14: virginica 3.3 narrow FALSE
## 15: virginica 2.7 narrow TRUE
## 16: virginica 2.7 narrow FALSE
## 17: virginica 3.0 narrow TRUE
## 18: virginica 3.0 narrow FALSE
```
前面只包含完全匹配的行，并且缺少了 `other` 物种。允许所有可能的组合会再多两行，总共 20 行。所得数据是否有用则是另一个完全不同的问题。在合并数据时，始终值得考虑可用的信息类型以及特定分析可能需要什么信息：
```
merge(diris, dalt1, all = TRUE, allow.cartesian = TRUE)

## Species Sepal.Width Type MedPW
## 1: other NA moderate TRUE
## 2: other NA moderate FALSE
## 3: setosa 3.5 wide TRUE
## 4: setosa 3.5 wide FALSE
## 5: setosa 3.0 wide TRUE
## 6: setosa 3.0 wide FALSE
## 7: setosa 3.2 wide TRUE
## 8: setosa 3.2 wide FALSE
## 9: versicolor 3.2 wide TRUE
## 10: versicolor 3.2 wide FALSE
## 11: versicolor 3.2 wide TRUE
## 12: versicolor 3.2 wide FALSE
## 13: versicolor 3.1 wide TRUE
## 14: versicolor 3.1 wide FALSE
## 15: virginica 3.3 narrow TRUE
## 16: virginica 3.3 narrow FALSE
## 17: virginica 2.7 narrow TRUE
## 18: virginica 2.7 narrow FALSE
## 19: virginica 3.0 narrow TRUE
## 20: virginica 3.0 narrow FALSE
```
对于所有这些，键都是 `Species`，并且这只是一个列。此外，它并不是唯一的。基于多个列设置键也是可能的。要认识到键的选择可能会决定合并是一对一还是多对多。考虑下面这个有启发性的示例，其中仅使用单个列作为键来合并数据：
```
d2key1 <- data.table(
  ID1 = c(1, 1, 2, 2),
  ID2 = c(1, 2, 1, 2),
  X = letters[1:4])

d2key2 <- data.table(
  ID1 = c(1, 1, 2, 2),
  ID2 = c(1, 2, 1, 2),
  Y = LETTERS[1:4])

d2key1

## ID1 ID2 X
## 1: 1 1 a
## 2: 1 2 b
## 3: 2 1 c
## 4: 2 2 d

d2key2

## ID1 ID2 Y
## 1: 1 1 A
## 2: 1 2 B
## 3: 2 1 C
## 4: 2 2 D

setkey(d2key1, ID1)
setkey(d2key2, ID1)
merge(d2key1, d2key2)

## ID1 ID2.x X ID2.y Y
## 1: 1 1 a 1 A
## 2: 1 1 a 2 B
## 3: 1 2 b 1 A
## 4: 1 2 b 2 B
## 5: 2 1 c 1 C
## 6: 2 1 c 2 D
## 7: 2 2 d 1 C
## 8: 2 2 d 2 D
```
由于合并后列名重复，为了成功合并，创建了 `ID2.x` 和 `ID2.y`。事实上，这些数据在合并后本应只产生四行，因为这两列中的数据是相同的！通过将两个数据集的键设置为同时包含 `ID1` 和 `ID2` 这两个列，不仅将多对多合并缩减为一对一合并，而且数据也更具实用性。当合并多个长数据集时，使用多个列作为键尤其有用。例如，当对多个个体进行随时间重复测量时，可能存在一个参与者 ID 列和另一个时间变量，但两者都需要用于正确合并：
```
setkey(d2key1, ID1, ID2)
setkey(d2key2, ID1, ID2)
merge(d2key1, d2key2)

## ID1 ID2 X Y
## 1: 1 1 a A
## 2: 1 2 b B
## 3: 2 1 c C
```

# 4: 2 2 d D

## 数据重塑

除了数据合并外，数据还可以进行重塑。以我们的`iris`数据集为例：每一行代表一个独特的花卉测量值，但每一朵被测量的花都使用了多个列。尽管没有唯一的键，但每朵花（行）都有多种测量值。较宽的数据可以重塑为长数据，其中每个键出现多次，并将变量存储在更少的列中。首先，我们将数据重置为`iris`数据集，并根据行位置创建一个唯一的`ID`作为键。回想一下，数据表会隐藏行号，因此我们通过使用数据表的另一个有用简写`.N`（表示行数）来将这些行号添加到它们的`ID`列中：

```
diris <- as.data.table(iris)

diris[, ID := 1:.N]

setkey(diris, ID)
```

**注意：** 重塑用于将数据从宽格式转换为长格式，或从长格式转换为宽格式。在宽格式中，重复测量的变量具有单独的列，列名表示评估指标。在长格式中，重复变量只有一列，而另一列则表示评估指标或“时间”。将宽格式转换为长格式可通过使用`melt()`函数完成，该函数需要多个参数。

要将数据重塑为长格式，我们使用`melt()`将数据表的测量变量堆叠成一种堆叠格式。此函数需要多个参数，以下将逐一讨论。要查看所有参数，请查阅帮助文档`?melt.data.table`。指定确切的方法`melt.data.table`非常重要，因为通用函数的参数是不同的。

第一个形式参数`data`是要熔化的数据表名称，接着是`id.vars`，它包含 ID 变量以及任何不应被重塑为长格式的其他变量。默认情况下，`id.vars`是未在下一个形式参数中明确列出的任何列名。分配给`id.vars`的任何列名都不会被重塑为长格式；它们会被重复，但保持原样。第三个形式参数用于测量变量，`measure.vars`使用这些列名来堆叠数据。通常，这是一个包含单独字符向量的列表，用于指示哪些变量应该在长数据中堆叠在一起。

第四个形式参数是`variable.name`，它是新创建的变量名称，用于指示新长数据集中特定行属于`measure.vars`的哪个级别。在下面的例子中，第一个级别对应长度测量值，而第二个级别对应`iris`花朵的宽度测量值。最后，`value.name`是熔化后（或长格式）列的名称，即长数据中包含原始宽变量值的列的名称。

描述重塑及其所需参数相当复杂。对比现在熟悉的`iris`数据集与重塑后的长数据集之间的输出可能更容易理解，如下所示：

```
diris.long <- melt(diris,
                   measure.vars = list(
                     c("Sepal.Length", "Sepal.Width"),
                     c("Petal.Length", "Petal.Width")),
                   variable.name = "Type",
                   value.name = c("Sepal", "Petal"),
                   id.vars = c("ID", "Species"))
diris.long
```

```
## ID Species Type Sepal Petal
## 1: 1 setosa 1 5.1 1.4
## 2: 2 setosa 1 4.9 1.4
## 3: 3 setosa 1 4.7 1.3
## ---
## 298: 148 virginica 2 3.0 2.0
## 299: 149 virginica 2 3.4 2.3
## 300: 150 virginica 2 3.0 1.8
```

请注意，我们的数据现在因为熔化而变成了 300 行。将`Type` 1 和 2 等同于萼片和花瓣的长度与宽度测量值可能会让人困惑。数据表的`melt()`方法默认只创建一个整数值，以指示它是第一个、第二个还是第三个（等等）级别。为了使标签更具信息性，我们使用`factor()`。因为`factor`会对列进行操作并更改该列的行元素，所以它属于数据表的第二个形式参数`j`：

```
diris.long[, Type := factor(Type, levels = 1:2, labels = c("Length", "Width"))]
diris.long
```

```
## ID Species Type Sepal Petal
## 1: 1 setosa Length 5.1 1.4
## 2: 2 setosa Length 4.9 1.4
## 3: 3 setosa Length 4.7 1.3
## ---
## 298: 148 virginica Width 3.0 2.0
## 299: 149 virginica Width 3.4 2.3
## 300: 150 virginica Width 3.0 1.8
```

数据集`diris.long`是一个很好的长数据示例。然而，它并非来自`diris`的可能最长数据集——这并不是说最长数据本身是目标。但是，如果仅对`diris`调用`melt()`并指定`id.vars`，那么得到的数据表可能是`iris`数据的最长形式。正如`id.vars`默认为不在`measure.vars`中的任何值一样，`measure.vars`也默认为不在`id.vars`中的任何值。同时，你还可以看到，`variable.name`和`value.name`分别默认为`variable`和`value`。请注意，`diris.long2`有 600 行，而原先的四列萼片/花瓣长度和宽度现在变成了两列。还请注意，当这样做时，`melt()`会自动将`variable`的值设置为数据集中最初的变量名称：

```
diris.long2 <- melt(diris, id.vars = c("ID", "Species"))
diris.long2
```

```
## ID Species variable value
## 1: 1 setosa Sepal.Length 5.1
## 2: 2 setosa Sepal.Length 4.9
## 3: 3 setosa Sepal.Length 4.7
## ---
## 598: 148 virginica Petal.Width 2.0
## 599: 149 virginica Petal.Width 2.3
## 600: 150 virginica Petal.Width 1.8
```

**注意：** 要将数据从长格式重塑为宽格式，可使用`dcast()`函数。其两个必要参数是`data`和`formula`，用于指示数据应如何从长格式重塑为宽格式。该公式必须指明单元格中的唯一值，否则需要在单元格内对数据进行聚合。

虽然`melt()`用于将宽数据变长，但`dcast()`用于将长数据变宽。如果没有数据的唯一标识符，则必须聚合多个值以适合一个单元格。因此，不加考虑地调用我们的转换函数可能会产生不想要的结果（尽管有时聚合多个值正是我们想要的）。

请注意，在第二个形式参数中，`Species`被指定为标识键，而`variable`被指定为查找新列名的位置：

```
dcast(diris.long2, Species ~ variable)
```

```
## Aggregate function missing, defaulting to 'length'
## Species Sepal.Length Sepal.Width Petal.Length Petal.Width
## 1: setosa 50 50 50 50
## 2: versicolor 50 50 50 50
```

# `data.table` 简介

## `iris` 数据集示例

`1: virginica 50 50 50 50`

如果要将长格式数据更好地转换为宽格式，需要一个唯一标识符，以便公式创建仅包含单个值的唯一单元格。除非需要对结果进行聚合，否则对于给定变量，公式参数应产生单个值，而非多个值。

在本例中，由于长格式数据中 `variable` 列有四个可能取值，只要存在一个永不重复的唯一键（例如 `Sepal.Length`），`dcast()` 就能正常工作。换句话说，如果宽格式数据有一个唯一键，我们可以将该唯一键与长格式数据中的变量名组合起来，从而精确地将长格式数据集中的单元格映射回宽格式数据。公式左侧包含键或 ID 变量。公式左侧（波浪号 `~` 左边）还可以包含任何不发生变化的变量（例如，如果最初将数据从宽格式重塑为长格式，这些变量会通过 `melt()` 的 `id.vars` 参数指定）。公式右侧（波浪号 `~` 右边）包含一个或多个变量，这些变量指示长格式数据集中的某一行应属于新宽格式数据集的哪一列。

在我们的长格式 `iris` 数据集示例中，`ID` 和 `Species` 变量不发生变化，因此应放在公式左侧。名为 `variable` 的变量指示长格式数据集中的特定值代表 `Sepal.Length`、`Sepal.Width`、`Petal.Length` 还是 `Petal.Width`。因此，对于本例，公式为 `ID + Species ~ variable`：

```r
diris.wide2 <- dcast(diris.long2, ID + Species ~ variable)
```

`diris.wide2` 的结果如下：

```
##      ID Species Sepal.Length Sepal.Width Petal.Length Petal.Width
##  1:   1  setosa          5.1         3.5          1.4         0.2
##  2:   2  setosa          4.9         3.0          1.4         0.2
##  3:   3  setosa          4.7         3.2          1.3         0.2
##  ---
## 148: 148 virginica          6.5         3.0          5.2         2.0
## 149: 149 virginica          6.2         3.4          5.4         2.3
## 150: 150 virginica          5.9         3.0          5.1         1.8
```

回到第一个 `diris.long` 数据（其中包含萼片和花瓣的长度与宽度的二水平因子），也可以将其强制转换回宽格式。对 `dcast()` 的调用与之前的示例类似。我们指定长格式数据集、一个包含未堆叠的 `ID` 和 `Species` 变量的公式，以及指示数据每行所属类别的变量 `Type`。

对于这种“不算太长”（姑且这么称呼）的数据，我们需要一个额外的参数 `value.var`，以告知 `dcast()` 长格式数据中哪些变量是重复测量值。该参数可以是一个字符向量，包含长格式数据集中将对应宽格式数据集中多个变量的所有变量。在本例中，这些变量是 `Sepal` 和 `Petal`，它们在长格式数据中同时包含长度和宽度值（由 `Type` 变量指示）。此外，必须选择分隔符，因为 `data.table` 的列名可能有不同的分隔符。通过 `sep =` 形参进行选择，此处为了连续性使用句点，因为原始宽格式 `iris` 数据集中变量就是如此标记的。我们仅展示 `dcast()` 命令，并通过 `all.equal()` 函数检查无论使用哪种方法，都能恢复宽格式数据。要查看结果，请在控制台键入 `diris.wide`：

```r
diris.wide <- dcast(diris.long, ID + Species ~ Type,
                    value.var = list("Sepal", "Petal"),
                    sep = ".")
all.equal(diris.wide2, diris.wide)
```

```
## [1] 真
```

### 7.6 小结

在本章中，您学习了 `R` 中两种强大（且更现代）的数据管理方式之一。具体来说，我们使用 `data.table` 处理的数据类型是列和行，即表格数据。在下一章中，我们将更深入地探讨数据操作。

接下来，您将接触到 `dplyr` [29]，这是 `R` 中另一种现代的数据管理方式。在本节结束时，我们将探讨 `R` 外部的数据库。表 7-1 总结了本章中使用的主要函数。

### 第 7 章 data.table 简介

**表 7-1. 本章所述主要函数及其功能摘要列表**

| 函数 | 功能 |
| --- | --- |
| `d[i, j, by]` | 对数据表进行子集筛选、计算或修改的通用结构。 |
| `as.data.table()` | 将数据结构强制转换为数据表。 |
| `setkey()` | 将数据表作为第一个参数，然后根据后续传递给函数的列名参数创建键。 |
| `haskey()` | 对数据表调用此函数，返回一个布尔值。 |
| `key()` | 返回数据表的键（如果存在）。 |
| `order()` | 控制数据表中某一列数据的升序或降序排列。 |
| `tables()` | 返回当前处于活动状态的所有数据表。 |
| `anyDuplicated()` | 返回第一个重复条目的位置。 |
| `duplicated()` | 对所有行返回 `TRUE` 或 `FALSE` 以指示重复情况。可指定键，否则基于所有列判断。 |
| `unique()` | 返回仅包含唯一行的数据表。同样，可以指定特定的行。如果存在键，则基于该键；如果不存在，则基于所有列。 |
| `setnames()` | 允许修改列名。 |
| `setcolorder()` | 允许调整列的顺序。 |
| `:=` | 数据表内部的赋值运算符。 |
| `.()` | `list()` 的简写形式。 |
| `.N` | 表示数据表中的行数，或在子集（例如按分组变量执行计算操作时）中的行数。 |
| `merge()` | 基于匹配的键列合并两个数据表。 |
| `melt()` | 将宽格式数据转换为长格式数据。 |
| `dcast()` | 将长格式数据转换为宽格式数据。 |

