# 因子操作详解

## 水平拼接

它们有相同的水平，但顺序不同。如果我们拼接 `f1` 和 `f2`，我们会得到按照 `f1` 顺序排列的水平。

```r
fct_c(f1, f2)
##    [1] C B B A D C B B A D
## Levels: A B C D
```

如果我们拼接 `f2` 和 `f1`，那么水平按照 `f2` 中的顺序排列。

```r
fct_c(f2, f1)
##   [1] C B B A D C B B A D
## Levels: C B A D
```

现在考虑一个具有 `f1` 中不存在水平的因子，例如：

```r
f3 <- as_factor(c("X", "Y", "A"))
```

它与 `f1` 共享一个水平 `A`，但 `X` 和 `Y` 是 `f3` 独有的。如果我们以 `f1` 作为第一个参数进行拼接，新的水平会被包含进来，但排在 `f1` 中的水平之后（以及共享水平 `A` 之后）。

```r
fct_c(f1, f3)
## [1] C B B A D X Y A
## Levels: A B C D X Y
```

如果我们以 `f3` 作为第一个参数进行拼接，那么 `f3` 的水平会排在前面。

```r
fct_c(f3, f1)
## [1] X Y A C B B A D
## Levels: X Y A B C D
```

你可以使用 `fct_c()` 拼接多个因子。其规则与拼接两个因子相同。

```r
fct_c(f1, f2, f3)
##   [1] C B B A D C B B A D X Y A
## Levels: A B C D X Y

fct_c(f2, f3, f1)
##   [1] C B B A D X Y A C B B A D
## Levels: C B A D X Y

fct_c(f3, f1, f2)
##   [1] X Y A C B B A D C B B A D
## Levels: X Y A B C D
```

如果你有多个水平不相同的因子，但你希望它们具有相同的水平（在不拼接的情况下），你可以使用 `fct_unify()`。它接收一个因子列表，并返回一个因子列表，其中所有因子都具有输入中所有水平的并集。

```r
fct_unify(fs = list(f1, f2, f3))
## [[1]]
## [1] C B B A D
## Levels: A B C D X Y
##
## [[2]]
## [1] C B B A D
## Levels: A B C D X Y
##
## [[3]]
## [1] X Y A
## Levels: A B C D X Y
```

## 投影（Projection）

有时你的分类数据粒度过细，希望将类别归并到更大的类中。如果是这样，你需要的函数是 `fct_collapse()`。它允许你将现有的水平映射到新的水平。它的第一个参数是一个因子，之后你需要提供命名参数。命名参数的值必须是一个水平名称的列表。每个名称都会成为一个新的水平，并且该水平将包含你作为参数给出的列表中的元素。

```r
fct_collapse(
  fct_c(f3, f1),
  a = c("A", "X"),
  b = c("B", "Y"),
  c = c("C", "D")
)
## [1] a b a c b b a c
## Levels: a b c
```

你不需要重新映射所有水平。那些你没有映射的水平将保持不变。

```r
fct_collapse(
  fct_c(f3, f1),
  a = c("A", "X"),
  b = c("B", "Y")
)
## [1] a b a C b b a D
## Levels: a b C D
```

### 重命名水平

如果你只想重命名水平而不是归并它们，可以使用 `fct_recode()`。

```r
f1
## [1] C B B A D
## Levels: A B C D

fct_recode(f1, a = "A", b = "B")
## [1] C b b a D
## Levels: a b C D

fct_recode(f1, X = "A", X = "B", Y = "C", Y = "D")
## [1] Y X X X Y
## Levels: X Y
```

### 合并不常见的类别

如果你想根据每个水平所含元素的数量来减少水平数量，而不是以显式指定的方式合并水平，那么你可以使用 `fct_lump()`。你可以合并水平，只保留元素最多的 *n* 个水平，例如，*n* `= 5` 和 *n* `= 2`：

```r
f |> fct_lump(n = 5, other_level = "X") |> table()
##
## 3  5  6  7  9  10  X
## 2  4  2  2  4   5  1

f |> fct_lump(n = 2, other_level = "X") |> table()
##
## 5  9  10  X
## 4  4   5  7
```

这里你看到的结果中有多于五个和两个的水平，尽管我们调用了 *n* `= 5` 和 *n* `= 2` 的 `fct_lump()`。如果有几个水平具有相同数量的元素，该函数不会随机选择一些进行保留。你会得到所有出现次数最多的水平。

如果你使用负数，你会得到最不常见的类别。

```r
f |> fct_lump(n = -2, other_level = "X") |> table()
##
## 1  4  8   X
## 0  0  0  20
```

除了选择你想要保留的类别数量，你还可以要求它们包含至少或至多一定比例的数据。为此，你需要使用 `prop` 参数。要得到包含至少 10% 数据的水平，你可以使用 `prop = 0.1`：

```r
f |> fct_lump(prop = 0.1, other_level = "X") |> table()
##
## 5  9  10  X
## 4  4   5  7
```

如果你想要包含少于 10% 数据的类别，也可以使用 `prop`，但要用负值。要得到包含至多 10% 数据的类别，你可以使用：

```r
f |> fct_lump(prop = -0.1, other_level = "X") |> table()
##
## 1  2  3  4  6  7  8  X
## 0  1  2  0  2   2 0 13
```

### 选择性保留/丢弃类别

一种更直接的映射类别的方法是将固定数量的类别映射到一个新类别。`fct_other()` 允许你选择一个要保留或丢弃的类别列表。如果你选择保留某些类别，那么所有其他类别都会被归入同一个“其他”类别。如果你选择丢弃某些类别，那么被丢弃的类别会被归入“其他”类别。你可以使用 `other_level` 参数来命名这个“其他”类别。

```r
f |> fct_other(keep = 1:5, other_level = "X")
## [1] 2 X X X 5 X X 3 3 X X 5 X 5 X X X 5 X X
## Levels: 1 2 3 4 5 X

f |> fct_other(drop = 1:5, other_level = "X")
## [1] X 9 9 9 X 7 7 X X 6 10 X 10 X 9
## [16] 6 10 X 10 10
## Levels: 6 7 8 9 10 X
```

### 删除空水平

有时，你想要的操作甚至更简单。你的数据中可能会有一些实际不存在的水平。你可以使用 `fct_drop()` 来删除这些空的水平。

```r
f1
## [1] C B B A D
## Levels: A B C D

levels(f1) <- LETTERS[1:10]
f1
## [1] C B B A D
## Levels: A B C D E F G H I J

fct_drop(f1)
## [1] C B B A D
## Levels: A B C D
```

## 添加水平

`fct_drop()` 的反向操作是添加额外的水平。正如我们之前所做的那样，对 `levels()` 进行赋值会添加那些因子中不一定存在的水平，但同时也可能会重命名现有的水平。水平被重命名了，而不仅仅是添加。这一点在之前的 `f1` 上就发生了。当我们添加新水平时，它原有的水平 1、2、3 和 4 被替换为了 A、B、C 和 D。

使用 `fct_expand()` 函数，你可以添加因子中不存在的水平，而任何数据中已有的水平都会被忽略。

```r
f1
## [1] C B B A D
## Levels: A B C D E F G H I J

f3
## [1] X Y A
## Levels: X Y A

fct_expand(f1, levels(f3))
## [1] C B B A D
## Levels: A B C D E F G H I J X Y
```

在 R 中，缺失数据并不被视为缺失数据，我们通常可以直接忽略它。然而，有时缺失数据中也包含我们需要考虑的信息。由于 R 在许多统计检验中无法很好地处理缺失数据（我补充这一点是有充分理由的，并没有一种普遍正确的方法来处理它），最好的办法是将其转换为 R **能够**处理的类别。你可以使用 `fct_explicit_na()` 将所有缺失数据映射到一个新的类别中：

```r
f2
# [1] C B B A D
# Levels: C B A D
fna
# [1] A D
# Levels: C B A D
fna <- fct_explicit_na(fna, na_level = "Missing")
fna
# [1] C    Missing Missing A   D
# Levels: C B A D Missing
```

## 重排序水平

如果你使用 `factor()` 构造函数，可以通过 `levels` 参数来控制水平的顺序。如果你使用 `as.factor()`，水平将按名称排序；如果你使用 `as_factor()`，水平将按它们在数据中出现的顺序排序。不过，如果有需要，你也可以在这些顺序之间进行转换。

如果你使用 `factor()` 创建因子时没有指定 `levels`，那么水平将是输入中出现的所有元素，并按它们的自然顺序排序。

```r
f <- factor(sample(LETTERS[1:5], 10, replace = TRUE))
f
# [1] C A B D E C B A B B
# Levels: A B C D E
```

`fct_inorder()` 函数会将你的水平顺序调整为与数据中类别出现的顺序一致——即使用 `as_factor()` 时得到的顺序。

```r
fct_inorder(f)
# [1] C A B D E C B A B B
# Levels: C A B D E
```

用于重排序水平的函数也可以使因子成为有序因子。^(¹⁸)

```r
fct_inorder(f, ordered = TRUE)
# [1] C A B D E C B A B B
# Levels: C < A < B < D < E
```

如果你想对水平进行排序，并不需要 `forcats` 包中的函数。使用传统的 `factor()` 就可以很好地完成。

```r
factor(f, levels = sort(levels(f)))
# [1] C A B D E C B A B B
# Levels: A B C D E

factor(f, levels = sort(levels(f)), ordered = TRUE)
# [1] C A B D E C B A B B
# Levels: A < B < C < D < E
```

你可以使用 `fct_infreq()` 根据水平在因子中出现的频率对其进行排序：

```r
table(f)
# f
# A B C D E
# 2 4 2 1 1

fct_infreq(f)
# [1] C A B D E C B A B B
# Levels: B A C D E

f |> fct_infreq() |> table()
# 
# B A C D E
# 4 2 2 1 1

f |> fct_infreq(ordered = TRUE)
# [1] C A B D E C B A B B
# Levels: B < A < C < D < E

f |> fct_infreq() |> fct_rev() |> table()
# 
# E D C A B
# 1 1 2 2 4
```

在最后一个示例中，我反转了水平顺序，因此顺序是从最小到最大，而不是从最大到最小。`fct_rev()` 函数用于反转水平顺序；它的一个常见用途是当你绘制图表，并且某个坐标轴由因子决定时，你可能希望反转其顺序。

脚注 1

---
