# 处理因子：`forcats`

如果你处理分类数据，通常可以用字符串来表示类别。然而，字符串也有一些缺点。例如，拼写错误很容易不被发现。如果你希望类别有序，那么字符串的字典顺序并不总是你需要的顺序。作为将类别编码为字符串的替代方案，我们有因子。在本章中，我们将介绍 `forcats` 包提供的用于创建和操作因子的功能。

当你导入 `tidyverse` 时，该包会被加载：

```r
library(tidyverse)
```

但你也可以显式加载它：

```r
library(forcats)
```

## 创建因子

内置的 `factor()` 函数仍然是从头构建因子的绝佳选择，尤其是当你想在创建时指明因子的水平（levels）。

如果你在创建因子时没有明确指定水平，那么每个输入中的唯一元素都会成为一个水平。

```r
factor(c("A","C","B"))
## [1] A C B
## Levels: A B C
```

否则，你会得到传递给 `levels` 参数的那些水平。

```r
factor(c("A","C","B"),levels =c("A","B","C","D"))
## [1] A C B
## Levels: A B C D

factor(c("A","C","B"),levels =c("D","C","B","A"))
## [1] A C B
## Levels: D C B A
```

如果有一个值在水平中不存在，它会被设置为 `NA`。

```r
factor(c("A","C","B"),levels =c("A","B"))
## [1] A     B
## Levels: A B
```

通常，`factor()` 函数是创建因子的正确选择。然而，该函数并非泛型函数，因此如果你希望有一种方法来转换自己实现的数据结构（并且这种转换需要特定的方法），那么你可能需要特化 `forcats` 包中的 `as_factor()` 函数。

如果你特化了 `as_factor()` 函数，你可以做任何你想做的事情。但对于普通向量，`as_factor()` 的工作方式类似于基础 R 中的 `as.factor()`。不过，这两个函数在设置水平的方式上有所不同。如果你使用 `as.factor()`，水平会被排序。

```r
f1<-as.factor(c("C","B","B","A","D"))
f1# levels ordered alphabetically
## [1] C B B A D
## Levels: A B C D
```

使用 `as_factor()` 时，水平按照它们在输入中出现的顺序排列。

```r
f2<-as_factor(c("C","B","B","A","D"))
f2# levels in the order they appear in the input
## [1] C B B A D
## Levels: C B A D
```

## 拼接（Concatenation）

如果你有两个因子，你可以使用 `fct_c()` 来拼接它们。结果因子的水平是输入因子水平的并集。水平的顺序取决于输入因子的顺序。第一个因子的水平排在前面，然后是第二个因子的水平。如果因子共享某些水平，则顺序由第一个向量决定。

例如，重新考虑之前的因子 `f1` 和 `f2`。

```r
f1
## [1] C B B A D
## Levels: A B C D

f2
## [1] C B B A D
## Levels: C B A D
```
