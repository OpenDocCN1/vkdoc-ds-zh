# 5.5

对于单个值，这两种方法的工作原理相同，但它们的语义略有不同，这在提供序列作为参数时会体现出来。将命名参数直接提供给 `Map` 与提供未命名参数的作用相同（区别在于你可以按名称而不是按位置选择特定变量），因此 `Map` 会假定你想要将函数应用于参数的每个元素。这在单个参数下有效的原因是，正如 R 的常规做法，较短的序列会根据需要重复多次。对于单个参数，这正是我们想要的，但对于序列则不一定。

如果我们想要这种行为，我们可以直接使用 `Map` 的命名参数，但如果我们希望每次调用函数时都使用整个序列，则必须将该序列作为 `MoreArgs` 参数的一个参数。

作为一个例子，我们可以考虑我们信赖的朋友 `scale` 函数，并创建一个版本，其中向量 `x` 通过另一个向量 `y` 的均值和标准差进行缩放：

```r
scale <- function(x, y) (x - mean(y))/sd(y)
```

如果我们只为 `scale` 提供两个参数，`Map` 会独立地评估所有配对，我们会得到很多 `NA` 值，因为我们在单个值上调用了 `sd` 函数：

```r
unlist(Map(scale, 1:10, 1:5))
## [1] NA NA NA NA NA NA NA NA NA NA
```

如果我们命名参数 `y`，也会发生同样的情况：

```r
unlist(Map(scale, 1:10, y = 1:5))
## [1] NA NA NA NA NA NA NA NA NA NA
```

但是如果我们使用 `MoreArgs`，整个向量 `y` 会在每次调用时提供给 `scale`：

```r
unlist(Map(scale, 1:10, MoreArgs = list(y = 1:5)))
## [1] -1.2649111 -0.6324555  0.0000000  0.6324555
## [5]  1.2649111  1.8973666  2.5298221  3.1622777
## [9]  3.7947332  4.4271887
```

与 `Filter` 一样，只要我们的函数可以处理不同类型的列表，`Map` 就可以处理任意类型的列表：

```r
s <- list(a = 1:10, b = list(1,2,3,4,5,6),
          c = y ~ x1 + x2 + x3, d = vector("numeric"))
unlist(Map(length, s))
##  a  b  c  d
## 10  6  3  0
```

## 缩减序列

`Filter` 和 `Map` 生成列表，而 `Reduce` 函数则将列表转换为一个值。当然，这个值也可以是一个列表——列表也是值——但 `Reduce` 并非简单地独立处理其输入列表中的每个元素。相反，它通过迭代地将一个函数应用于成对的元素来对列表进行汇总。你提供一个接受两个元素的函数 `f`，它首先会对列表中的前两个元素调用 `f`。然后，它将获取这个结果，并用此结果与下一个元素调用 `f`，并继续在整个列表中执行此操作。

所以调用 `Reduce(f, 1:5)` 等同于调用 `f(f(f(f(1, 2), 3), 4), 5)`。写 `Reduce(f, 1:5)` 只是更易读，至少在你习惯了之后。

我们可以使用 `+` 作为函数来观察它的实际应用：

```r
Reduce(`+`, 1:5)
## [1] 15
```

如果你使用参数 `accumulate`，还可以获得逐步结果。这将返回一个包含对 `f` 的所有调用结果的列表，并且列表中包含第一个值，因此 `Reduce(f, 1:5, accumulate = TRUE)` 将返回列表：

```
c(1, f(1, 2), f(f(1 ,2), 3), f(f(f(1, 2), 3), 4),
  f(f(f(f(1, 2), 3), 4), 5))
```

所以，对于加法，我们得到：

```r
Reduce(`+`, 1:5, accumulate = TRUE)
## [1]  1  3  6 10 15
```

默认情况下，`Reduce` 从左到右进行计算，但是通过将选项 `right` 设置为 `TRUE`，你将得到从右到左的结果：

```r
Reduce(`+`, 1:5, right = TRUE, accumulate = TRUE)
## [1] 15 14 12  9  5
```

对于像 `+` 这样的结合性操作，如果我们不要求累积的函数调用，结果显然是相同的。

在许多函数式编程语言中（所有这些语言都有这个函数，尽管有时它被称为 `fold` 或 `accumulate`），你需要为计算提供一个初始值。然后，这个初始值会在对 `f` 的首次调用中使用，因此如果 `init` 指的是初始值，`x` 是序列，则折叠从 `f(init, x[1])` 开始。在 R 中，你也可以通过参数 `init` 为 `Reduce` 显式提供一个初始值来获得相同的行为：

```r
Reduce(`+`, 1:5, init = 10, accumulate = TRUE)
## [1] 10 11 13 16 20 25
```

你只是通常不需要指定这个初始值。在需要它的语言中，它用于在累积结果时获得正确的起点。对于加法，如果我们希望 `Reduce` 计算总和，我们会使用零作为初始值，因为将零加到序列中的第一个值只会得到第一个元素。对于乘法，我们则必须使用 1 作为初始值，因为这样第一个函数应用就会得到初始值。

在 R 中，如果我们满足于让第一个函数调用作用于列表中的前两个元素，则不需要提供这些初始值，因此乘法与加法一样有效，无需提供 `init`：

```r
Reduce(`*`, 1:5)
## [1] 120

Reduce(`*`, 1:5, accumulate = TRUE)
## [1]   1   2   6  24 120

Reduce(`*`, 1:5, right = TRUE, accumulate = TRUE)
## [1] 120 120  60  20   5
```

你通常不会使用 `Reduce` 来汇总值（如求和或乘积），R 中已经有了用于此目的的函数（分别是 `sum` 和 `prod`），而且它们作为用 C 实现的底层函数要快得多，而 `Reduce` 必须是高级函数才能处理任意函数。对于我们已经没有现成函数来汇总列表的更复杂数据，`Reduce` 通常是首选方法。

下面是一个例子，来自 Hadley Wickham 的 *Advanced R* 一书：

```r
samples <- replicate(3, sample(1:10, replace = TRUE), simplify = FALSE)
str(samples)
## List of 3
## $ : int [1:10] 1 3 7 8 9 5 9 1 6 4
## $ : int [1:10] 4 5 8 3 1 6 9 10 5 3
## $ : int [1:10] 9 9 2 10 9 2 9 8 7 10

Reduce(intersect, samples)
## [1] 8 9
```

我们有一个包含三个向量的列表，每个向量都有从 1 到 10 的十个样本，我们想要获取这三个列表的交集。这意味着取前两个的交集，然后取该结果与第三个列表的交集。非常适合使用 `Reduce`。我们只需将其与 `intersect` 函数结合使用。

## 集合使用这些函数

这三个函数经常一起使用，`Filter` 首先剔除不应处理的元素，然后 `Map` 处理列表，最后 `Reduce` 合并所有结果。

本节将展示几个如何使用这些函数的示例。我们从处理树开始。请记住，我们可以使用之前编写的 `make_node` 函数构建树，并且我们可以创建一个树列表，因为我们可以创建任何内容的列表：

```r
A <- make_node("A")
C <- make_node("C", make_node("A"), make_node("B"))
E <- make_node("E",
              make_node("C", make_node("A"), make_node("B")),
              make_node("D"))
trees <- list(A = A, C = C, E = E)
```

打印一棵树会得到它的列表表示形式。如果我们 `unlist` 一棵树，我们会得到相同的表示形式，只是被扁平化了，因此结构体现在结果向量的名称中，但我们编写了一个 `print_tree` 函数，它能够以 Newick 格式给出字符串表示形式：

```markdown
trees[[2]]
## $name
## [1] "C"
##
## $left
## $left$name
## [1] "A"
##
## $left$left
## NULL
##
## $left$right
## NULL
##
##
## $right
## $right$name
## [1] "B"
##
## $right$left
## NULL
##
## $right$right
## NULL

unlist(trees[[2]])
##    name left.name right.name
##     "C"       "A"        "B"

print_tree(trees[[2]])
## [1] "(A,B)"
```

我们可以使用 `Map` 将一个树列表转换为它们的 Newick 格式，并将此列表扁平化以获得一个字符向量：

```
Map(print_tree, trees)
## $A
## [1] "A"
##
## $C
## [1] "(A,B)"
##
## $E
## [1] "((A,B),D)"

unlist(Map(print_tree, trees))
##          A          C          E
##        "A"     "(A,B)"   "((A,B),D)"
```

`"A"` `"(A,B)"` `"((A,B),D)"`

我们可以将其与 `Filter` 结合，只获取非单叶的树；这里可以使用之前编写的 `size_of_tree` 函数：`unlist(Map(print_tree, Filter(function(tree) size_of_tree(tree) > 1, trees)))`

`C` `E`

`"(A,B)"` `"((A,B),D)"`

## 第 6 章 Filter、Map 和 Reduce

或者我们可以获取所有树的大小，并通过组合 `Map` 与 `Reduce` 计算它们的总和：

`unlist(Map(size_of_tree, trees))`

`A` `C` `E`

1 3 5

`Reduce(` + `, Map(size_of_tree, trees), 0)`

[1] 9

我们也可以搜索特定节点的深度，例如获取所有树中节点“B”的深度：

`node_depth_B <- function(tree) node_depth(tree, "B")`

`unlist(Map(node_depth_B, trees))`

`A` `C` `E`

NA 1 2

结果中的名称会让人困惑。它们对应我们在构建列表时为树赋予的名称，可以通过 `unlist` 的 `use.names` 参数去除。通常，如果不需要向量的名称，应始终这样做；这样能加快计算速度，因为 R 无须在数据上拖带名称。

`unlist(Map(node_depth_B, trees), use.names = FALSE)`

[1] NA 1 2

对于不包含“B”节点的树，搜索节点深度时会得到 `NA`，我们可以使用 `Filter` 轻松移除它们：

`Filter(function(x) !is.na(x), unlist(Map(node_depth_B, trees), use.names = FALSE))`

[1] 1 2

## 第 6 章 Filter、Map 和 Reduce

或者我们可以在对树应用 `Map` 之前，显式检查树是否包含节点“B”：

```
has_B <- function(node) {
  if (node$name == "B") return(TRUE)
  if (is.null(node$left) && is.null(node$right)) return(FALSE)
  has_B(node$left) || has_B(node$right)
}
```

`unlist(Map(node_depth_B, Filter(has_B, trees)), use.names = FALSE)`

[1] 1 2

先过滤再映射的解决方案可能更可取，因为如果我们用处理任意节点名称的通用函数替换 `has_B` 和 `node_depth_B`，则无需特意匹配它们，但两种方案都可行。

### Apply 函数族

`Map` 函数是对列表元素进行映射的通用解决方案，但 R 有一整套类似 `Map` 的函数族，它们适用于不同类型的输入。这些函数均以“something-apply”命名，我们已在上一章中见过 `sapply`。`Map` 函数实际上只是其中一个函数 `mapply` 的包装器，鉴于我们已经了解 `Map` 的用法，这里不再讨论 `mapply`，但我会简要介绍 `apply` 族中的其他函数。

### sapply、vapply 和 lapply

`sapply`、`vapply` 和 `lapply` 都作用于序列。区别在于：`sapply` 尝试*简化*其输出；`vapply` 接受一个值作为参数，并将输出强制转换为该值的类型，若无法转换则报错；`lapply` 则对列表进行映射。

## 第 6 章 Filter、Map 和 Reduce

在交互式会话中使用 `sapply` 很方便，因为当映射结果可以转换为向量时，它本质上类似于 `Map` 与 `unlist` 的组合。与 `unlist` 不同，它不会展平列表，因此如果映射结果比向量更复杂，`sapply` 仍会返回列表。正因如此，在程序中使用 `sapply` 可能存在风险：你无法预先知道输出类型，因此必须防御性编程，检查得到的是向量还是列表。

`sapply(trees, size_of_tree)`

`A` `C` `E`

1 3 5

`sapply(trees, identity)`

`A` `C` `E`

name "A" "C" "E"

left NULL list,3 list,3

right NULL list,3 list,3

使用 `vapply` 时，如果结果可以转换为向量，你会得到与 `sapply` 相同的简化效果，但必须向函数告知输出应为何种类型。这通过提供一个期望输出的示例来实现。如果 `vapply` 无法将结果转换为该类型，你会得到错误而非不同类型的意外结果，这使得该函数在程序中使用更安全。毕竟，相比因类型混淆导致的意外结果，得到错误要好得多。

`vapply(trees, size_of_tree, 1)`

`A` `C` `E`

1 3 5

## 第 6 章 Filter、Map 和 Reduce

`lapply` 是与 `Map` 最相似的函数。它接受一个列表作为输入，并返回一个列表。`lapply` 与 `Map` 的主要区别在于，`lapply` 始终作用于*单个*列表，而 `Map` 可以接受多个列表（这也解释了 `mapply` 的名称由来，`Map` 正是它的包装器）。

`lapply(trees, size_of_tree)`

## $A

[1] 1

##

## $C

[1] 3

##

## $E

[1] 5

### apply 函数

`apply` 函数作用于矩阵和高维数组，而非序列。它接受三个参数，外加任何应传递给被调用函数的额外参数。

第一个参数是要映射的数组，第二个参数是应沿哪个维度（或多个维度）进行边缘化，第三个参数是要应用的函数。

我们可以通过创建一个矩阵来观察其运行：

`(m <- matrix(1:6, nrow=2, byrow=TRUE))`

[,1] [,2] [,3]

[1,] 1 2 3

[2,] 4 5 6

## 第 6 章 Filter、Map 和 Reduce

为了看清实际发生的情况，我们创建一个收集所接收数据的函数，以便准确看到它被调用时的参数：

`collaps_input <- function(x) paste(x, collapse = ":")`

如果按行边缘化，它将对每一行调用，函数将接收整个行向量：

`apply(m, 1, collaps_input)`

[1] "1:2:3" "4:5:6"

如果按列边缘化，它将对每一列调用，并生成三个字符串：

`apply(m, 2, collaps_input)`

[1] "1:4" "2:5" "3:6"

如果同时按行和列边缘化，它将对每个元素单独调用：

`apply(m, c(1, 2), collaps_input)`

[,1] [,2] [,3]

[1,] "1" "2" "3"

[2,] "4" "5" "6"

### tapply 函数
```

# 第6章 Filter、Map 和 Reduce

`tapply` 函数作用于所谓的“不规则表格”：即行长度可以不同的表格。你无法直接创建行维度大小不一的数组，但可以使用一个扁平向量，结合指示虚拟维度的因子。`tapply` 根据因子对向量进行分组，然后对每组调用指定函数：

```r
(x <- rnorm(10))
```

```
## [1]  1.58827631 -0.99044548 -1.56081835  0.38316964  1.82773685 -0.96031940
## [7] -1.32439441  0.09413377 -0.02306317 -0.60627476
```

```r
(categories <- sample(c("A", "B", "C"), size = 10, replace = TRUE))
```

```
##  [1] "A" "A" "A" "B" "C" "A" "B" "A" "C" "A"
```

```r
tapply(x, categories, mean)
```

```
         A         B         C 
-0.4059080 -0.4706124  0.9023368 
```

你可以使用多个因子，只需将它们包装在列表中：

```r
(categories2 <- sample(c("X", "Y"), size = 10, replace = TRUE))
```

```
##  [1] "X" "Y" "X" "Y" "Y" "X" "X" "Y" "Y" "X"
```

```r
tapply(x, list(categories, categories2), mean)
```

```
           X         Y
A -0.3847840 -0.4481559
B -1.3243944  0.3831696
```