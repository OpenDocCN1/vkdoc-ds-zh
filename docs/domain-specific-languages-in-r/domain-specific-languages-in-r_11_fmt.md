# 9. 列表推导式

现在，我们将运用所学知识，在 R 语言中实现一个虽非内置但非常有价值的语言结构：`列表推导式`。列表推导式提供了一种用于映射（mapping）和过滤（filtering）序列的语法。在 R 中，我们通常使用 `Map` 或 `Filter` 这类函数，或是 `purrr` 包中的替代方案来完成此任务；但在 Haskell 或 Python 这类语言中，则存在语法糖（syntactic sugar），使得组合映射和过滤操作更易编程。

以快速排序（quicksort）算法为例。该算法的思想是：从列表中随机选取一个元素作为基准（pivot），然后将数据分割成小于基准、等于基准和大于基准的三部分。接着，递归地对小于和大于基准的部分进行排序，最后将三个列表连接起来，得到最终的排序列表。在 R 中，一种实现方式是使用 `Filter` 函数。

```r
qsort <- function(lst) {
  if (length(lst) < 2) return(lst)
  pivot <- lst[[sample(length(lst), 1)]]
  smaller <- Filter(function(x) x < pivot, lst)
  equal   <- Filter(function(x) x == pivot, lst)
  larger  <- Filter(function(x) x > pivot, lst)
  c(qsort(smaller), equal, qsort(larger))
}
(lst <- sample(1:10))
##  [1]  3  7  8  2  1  4 10  5  9  6
unlist(qsort(lst))
##  [1]  1  2  3  4  5  6  7  8  9 10
```

如果你熟悉函数式编程，这段代码是可读的，但要解析出 `Filter` 表达式及其中的谓词仍需花些功夫。将其与一个执行相同功能（但 Python 中需要采样，因此基准并非随机选取）的 Python 实现进行对比：

```python
def qsort(lst):
    if len(lst) < 2:
        return lst
    pivot = lst[random.randint(0, len(lst)-1)]
    smaller = [x for x in lst if x < pivot]
    equal   = [x for x in lst if x == pivot]
    larger  = [x for x in lst if x > pivot]
    return qsort(smaller) + equal + qsort(larger)
```

或者考虑一个类似的 Haskell 实现：

```
qsort lst =
  if length lst < 2 then lst
  else
    let pivot = head lst
        smaller = [x | x <- lst, x < pivot]
        equal   = [x | x <- lst, x == pivot]
        larger  = [x | x <- lst, x > pivot]
    in qsort(smaller) ++ equal ++ qsort(larger)
```

类似 Python 中的如下表达式：

```
[x for x in lst if x < pivot]
```

或 Haskell 中的：

```
[x | x <- lst, x < pivot]
```

就是我们所说的列表推导式。列表推导式包含三个部分：首先是一个表达式，它将对列表（如果使用多个列表，则对每个列表）中的每个元素进行求值；其次是待映射的一个或多个列表；最后是用于过滤的零个或多个谓词。因此，它是将 `Map` 和 `Filter` 调用组合在一个表达式中。

利用非标准求值（non-standard evaluation），我们可以编写一个 R 函数，提供类似的列表推导式语法。我们将这样设计它：函数的第一个参数必须是一个表达式，我们将对输入列表（或多个列表）中的所有元素对其进行求值；其余参数则用于标识列表或谓词。我们将使用命名参数来标识参数何时定义了一个列表，并使用未命名参数来表示谓词。

该函数的工作原理如下：我们将第一个参数转换为一个 `quosure`，这样我们就拥有了表达式及其定义时的环境。对于剩余的参数，我们同样处理，这些参数通过三点参数捕获，因为我们希望函数能接受任意数量的参数。我们使用 `enquo` 创建第一个 `quosure`，并使用 `quos` 创建其余参数的列表。然后，我们根据参数是否被命名，将其分为列表参数和谓词参数。在此过程中，我们求值命名参数以获取输入列表中的数据，并使用 `get_expr` 提取谓词的表达式。

对于我们创建的函数，无论是谓词还是用于映射列表的函数，我们都必须小心处理表达式应在哪个上下文中求值。我们希望这些表达式成为我们可以映射到列表上的函数体，因此不能直接在 `quosure` 的环境中求值它们；但我们确实希望这些环境处于作用域内，以便表达式能够看到不属于列表推导式的变量。因此，我们使用 `get_expr` 函数从 `quosure` 中获取原始表达式，但由此创建的函数将以 `quosure` 环境作为其封闭作用域。

我们为每个谓词创建一个函数，并为列表推导式的主表达式创建一个函数。将所有谓词组合成一个过滤表达式并映射到所有列表上并不直接，但使用它们来更新一个布尔向量（在该向量中我们跟踪哪些值应包含在最终结果中）却是直接的。我们可以逐一应用谓词，并执行逻辑与操作。然后，我们可以映射输入列表并对每个列表进行子集化——在以下代码中，我使用了 lambda 表达式，因为这些表达式在 `purrr` 包中被定义为公式，其中 `.x` 引用第一个参数。过滤列表后，我们可以将主函数应用于这些列表，得到最终结果。

综合以上所有内容，我们得到以下函数：

```r
library(rlang)
library(purrr)
lc <- function(expr, ...) {
  expr <- enquo(expr)
  rest <- quos(...)
  lists <- map(rest[names(rest) != ""], eval_tidy)
  predicates <- map(rest[names(rest) == ""], get_expr)
  keep_index <- rep(TRUE, length(lists[[1]]))
  for (pred in predicates) {
    p <- new_function(lists, body = pred, env = get_env(expr))
    keep_index <- keep_index & unlist(pmap(lists, p))
  }
  filtered_lists <- map(lists, ~.x[keep_index])
  f <- new_function(lists, body = get_expr(expr), env = get_env(expr))
  pmap(filtered_lists, f)
}
```

我们可以像这样使用它来实现快速排序：

```r
qsort <- function(lst) {
  if (length(lst) < 2) return(lst)
  pivot <- lst[[sample(length(lst), 1)]]
  smaller <- lc(x, x = lst, x < pivot)
  equal   <- lc(x, x = lst, x == pivot)
  larger  <- lc(x, x = lst, x > pivot)
  c(qsort(smaller), equal, qsort(larger))
}
(lst <- sample(1:10))
##  [1]  9  5  7  8 10  2  1  4  3  6
unlist(qsort(lst))
##  [1]  1  2  3  4  5  6  7  8  9 10
```

在这个函数中，我们只使用了列表推导式的过滤功能，但 `lc` 函数可用于更复杂的表达式。一个有趣的小例子是，我们可以使用 `lc` 计算小于给定数字 `n` 的质数。

这是埃拉托色尼筛法（sieve of Eratosthenes）算法的一个变体。我们计算所有非质数（它们是其他数的倍数），然后找出不在该列表中的数。我们让 `x` 从 2 到 10——要找出小于 `n` 的质数，只需做到 `sqrt(n)` 即可——并为每个 `x` 创建其各种倍数的列表。然后我们去除重复项以加快下一步的速度；在该步骤中，我们只需筛选出非质数。

```r
not_primes <- lc(x * y, x = 2:10, y = 2:10) %>%
  unlist %>% unique
not_primes
##  [1]   4   6   8  10  12  14  16  18  20  22  24
## [12]  26  28  30  32  34  36  38  40  42  44  46
## [23]  48  50  52  54  56  58  60  62  64  66  68
## [34]  70  72  74  76  78  80  82  84  86  88  90
## [45]  92  94  96  98 100   9  15  21  27  33  39
## [56]  45  51  57  63  69  75  81  87  93  99  25
## [67]  35  55  65  85  95  49  77  91
primes <- lc(p, p = 2:100, !(p %in% not_primes)) %>%
  unlist
primes
##  [1]  2  3  5  7 11 13 17 19 23 29 31 37 41 43 47
## [16] 53 59 61 67 71 73 79 83 89 97
```

一个适用于通用 `n` 的解决方案如下：

```r
get_primes <- function(n) {
  not_primes <- lc(x * y, x = 2:sqrt(n), y = 2:floor(n / x)) %>%
    unlist %>% unique
  lc(p, p = 2:n, !(p %in% not_primes)) %>% unlist
}
get_primes(100)
##  [1]  2  3  5  7 11 13 17 19 23 29 31 37 41 43 47
## [16] 53 59 61 67 71 73 79 83 89 97
```

传统上，该算法并不首先创建非质数列表，而是从一个候选质数列表开始——即从 2 到 `n` 的所有数。然后，迭代地取出列表中的第一个元素（它本身是质数），并移除所有能被该数整除的候选者。我们也可以使用列表推导式来实现这个移除候选者的版本：

```r
get_primes <- function(n) {
  lst <- lc(x, x = 2:n) %>% lst_to_list
  primes <- list()
  while (length(lst) > 0) {
    p <- first(lst)
    primes <- cons(p, primes)
    lst <- lc(x, x = lst[2:length(lst)], x %% p != 0)
  }
  primes %>% lst_to_list %>% unlist %>% rev
}
get_primes(100)
## Error in cons(p, primes): could not find function "cons"
```

作为另一个例子，其中我们有多个列表作为输入，并且我们使用列表推导式来构造新值而非过滤列表，我们可以像这样实现一个用于压缩两个列表的函数：

```r
zip <- function(x, y) {
  lc(c(x_i, y_i), x_i = x, y_i = y) %>%
    { do.call(rbind,.) }
}
zip(1:4,1:4)
##      [,1] [,2]
## [1,]    1    1
## [2,]    2    2
## [3,]    3    3
## [4,]    4    4
```

在这里，我们通过列表推导将列表 `x` 和 `y` 中的元素配对，然后使用 `bind` 合并列表。必须结合使用 `do.call` 和 `bind` 才能从中得到表格，而花括号则是必要的，以便将 `lc` 的结果作为 `do.call` 的第二个参数（而非第一个参数）。关于如何将花括号与管道运算符一起使用的说明，请参阅 `magrittr` 文档。

列表推导是另一个例子，说明极少量的代码就能创造出一种新的语言结构。将其称为一种语言可能有些牵强，但我们确实在创造一种新的语法来帮助我们编写更易读的代码——当然，前提是你认为列表推导比 `map` 和 `filter` 的组合更易读。