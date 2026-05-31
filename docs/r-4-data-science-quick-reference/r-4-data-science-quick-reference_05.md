# 7. 函数式编程：purrr

基于管道的数据处理方法需要采用函数式编程方法。毕竟，管道是函数的组合，而循环和变量赋值在管道中并不好用。你并非不能使用命令式编程，但你需要将其封装在函数中。

`purrr` 包使得函数式编程更加容易。与本书中的其他包一样，如果你导入 `tidyverse`，它会被加载，但你也可以显式地加载它：

```
library(purrr)
```

我不会描述 `purrr` 包中的所有函数，因为数量很多，而且在我撰写本书到您阅读本书期间可能还会增加更多函数，但我会描述那些你可能经常使用的函数。

本章中的许多示例都非常简单，以至于你不会为此使用 `purrr`。许多都可以简化为向量表达式，而向量表达式既更易于阅读，计算速度也更快。然而，那些不能使用向量表达式的应用则更为复杂，我不希望示例掩盖了语法的本意。

## purrr 函数的一般特性

`purrr` 包中的函数是“高阶”函数，这意味着这些函数要么将其他函数作为输入，要么在调用时返回函数（或两者皆有）。大多数函数将数据作为它们的第一个参数，并返回修改后的数据。因此，它们可以立即用于管道操作。作为额外的参数，它们会接受一个或多个函数，这些函数指定了你希望如何将输入转换为输出。

其他一些函数不转换数据，而是修改函数。使用它们是出于这样的目的：你无需为数据转换函数显式地编写函数；你可以使用一小部分函数，并在需要时对它们进行调整。

## 过滤

最直接的函数式编程模式之一是过滤。在这里，你使用一个高阶过滤器函数。这个函数接受一个谓词函数，即一个返回逻辑值的函数，然后返回所有谓词求值为 `TRUE` 的元素（`keep()` 函数）或所有谓词求值为 `FALSE` 的元素（`discard()` 函数）。

```
is_even <- function(x) x %% 2 == 0
1:6 |> keep(is_even)
## [1] 2 4 6
1:6 |> discard(is_even)
## [1] 1 3 5
```

当你处理谓词时，`negate()` 函数会派上用场。它会改变你谓词的真值，使得如果你的谓词 `p` 返回 `TRUE`，那么 `negate(p)` 返回 `FALSE`，反之亦然。

```
1:6 |> keep(negate(is_even))
## [1] 1 3 5
1:6 |> discard(negate(is_even))
## [1] 2 4 6
```

不过，由于你已经有 `keep()` 和 `discard()` 这对互补函数，你通常不会为了过滤而使用 `negate()`。

一个特例函数 `compact()` 用于从列表中移除 `NULL` 元素。

```
y <- list(1:3, NULL, 1:3) |> compact()
## [[1]]
## [1] 1 2 3
```

如果你访问对象上的属性，而这些属性未被设置，R 会返回 `NULL` 结果，在这种情况下，`compact()` 非常有用。

如果我们定义列表：

```
x <- y <- 1:3
names(y) <- c("one", "two", "three")
```

那么 `y` 的值将具有名称，而 `x` 则没有。如果我们尝试从 `x` 获取名称，我们将得到 `NULL`。

```
names(x)
## NULL
names(y)
## [1] "one"   "two"   "three"
```

将 `compact()` 与 `names()` 一起使用，我们将丢弃 `x`。

```
z <- list(x, y) |> compact(names)
## $y
##   one   two   three
##     1     2       3
```



## 映射（Mapping）

将函数 *f* 映射到序列 *x* = *x* [1], *x* [2], …, *x* [*n*] 上，会返回一个与输入长度相同的新序列，其中每个元素都是该函数应用后的结果：*f*(*x* [1]), *f*(*x* [2]), …, *f*(*x* [*n*])。

函数`map()`用于执行此操作，并返回一个`list`（列表）作为输出结果。列表是 R 语言中的通用序列数据结构，因为它可以容纳所有类型的 R 对象。

```
is_even <- function(x) x %% 2 == 0
list(1, 2, 3, 4) |> map(is_even)
## [[1]]
## [1] FALSE
##
## [[2]]
## [1] TRUE
##
## [[3]]
## [1] FALSE
##
## [[4]]
## [1] TRUE
```

通常，我们需要处理特定类型的向量。对于所有原子类型，`purrr` 都有专门的映射函数。用于逻辑值的函数名为`map_lgl()`。

```
1:4 |> map_lgl(is_even)
## [1] FALSE TRUE FALSE TRUE
```

对于像这样简单的例子，你不应该使用`purrr`。向量表达式更快且更易于使用。

```
1:4 %% 2 == 0
## [1] FALSE TRUE FALSE TRUE
```

然而，你并不总能使用向量表达式。假设你想从一个正态分布中对一个不同 *n* 值的序列分别抽取 *n* 个元素，然后计算均值的标准误。向量表达式

```
sd(rnorm(n = n)) / sqrt(n) # 不是 SEM！
```

无法完成此计算。如果你向`rnorm()`传入一个序列作为其`n`参数，它会将输入的*长度*作为要抽取的元素个数。

```
n <- seq.int(100, 1000, 300) # 我们想要的不同的 n 值
n
## [1]  100  400  700 1000
## 我们是否为每个 n 值都进行了抽样？
rnorm(n) # 不是，rnorm() 使用了 length(n) 作为样本数量
## [1]  0.6691846 -0.4554893 -0.8175801  1.7794664
```

当你有一个非向量化的函数时，可以使用 `map` 系列函数来将其应用于列表中的所有元素。

```
sem <- function(x) sd(x) / sqrt(length(x))
list(1:10, 100:110, 20:30, 50:70) |> map_dbl(sem)
## [1] 0.10197308 0.05138140 0.03673840 0.03057261
```

这里，我使用了`map_dbl()`来获得双精度型（数值型）输出。函数`map_chr()`和`map_int()`将分别返回字符串和整型。

```
1:3 %>% map_dbl(identity) %T>% print() %>% class()
## [1] 1 2 3
## [1] "numeric"
1:3 %>% map_chr(identity) %T>% print() %>% class()
## [1] "1" "2" "3"
## [1] "character"
1:3 %>% map_int(identity) %T>% print() %>% class()
## [1] 1 2 3
## [1] "integer"
```

如果你应用的函数返回的值不符合映射函数期望的类型，不同的映射函数会报错。有时，映射函数会进行类型转换，就像之前那样，但并非总是如此。它通常乐于进行不丢失信息的转换，例如，从逻辑型到整型，从整型到双精度型，但反过来则不行。

例如，即使你可以进行类型转换，但如果你向`map_lgl()`提供`identity()`函数，它也不会将整型转换为逻辑型。类似地，你可以使用`as.numeric()`将字符串转换为整型，但如果我们向`map_dbl()`提供`identity()`，它也不会这样做。映射函数比基础 R 语言的转换函数更加严格。因此，你应该使用能返回正确类型的函数。

`map_dfr()`和`map_dfc()`函数返回数据框（tibble）。映射的函数应返回数据框，这些数据框将通过`map_dfr()`按行合并，通过`map_dfc()`按列合并。

```
x <- list(a = 1:2, b = 3:4)
x |> map_dfr(identity)
## # A tibble: 4 × 2
##       a     b
##    <int> <int>
## 1     1     3
## 2     2     4
## 3     1     3
## 4     2     4
list(a = x, b = x) |> map_dfc(identity)
## New names:
## · `a` -> `a...1`
## · `b` -> `b...2`
## · `a` -> `a...3`
## · `b` -> `b...4`
## # A tibble: 2 × 4
##   a...1  b...2  a...3  b...4
##   <int> <int>  <int>  <int>
## 1     1     3     1     3
## 2     2     4     2     4
```

`map_df()`函数与`map_dfr()`功能相同：

```
list(a = x, b = x) |> map_df(identity)
## # A tibble: 4 × 2
##       a     b
##    <int> <int>
## 1     1     3
## 2     2     4
## 3     1     3
## 4     2     4
```

你不需要向数据框函数输入数据框，只要你对输入应用的函数返回数据框即可。这适用于所有映射函数。它们接受任何序列输入；它们仅限制和转换输出。

如果你映射的项本身是序列，你可以通过索引提取元素；无需向 `map` 函数提供函数。

```
x <- list(c(1, 2, 3), c(4, 5, 6))
x |> map_dbl(1)
## [1] 1 4
x |> map_dbl(3)
## [1] 3 6
```

如果这些项有名称，你也可以使用这些名称提取值。

```
x <- list(a = c(a = 42, b = 13), b = c(a = 24, b = 31))
x |> map_dbl("a")
## [1] 42 24
x |> map_dbl("b")
## [1] 13 31
```

这主要用于在数据框上映射时。

```
a <- data.frame(foo = 1:3, bar = 11:13)
b <- data.frame(foo = 4:6, bar = 14:16)
ab <- list(a = a, b = b)
ab |> map("foo")
## $a
## [1] 1 2 3
##
## $b
## [1] 4 5 6
```

与按名称或索引提取元素相关，你可以使用`map_depth()`对输入的不同深度应用函数。深度零是列表本身，因此映射到这个深度等同于直接将函数应用于输入。

```
ab |> map_depth(0, length)
## [1] 2
ab |> length()
## [1] 2
```

深度 1 给我们序列中的每个元素，因此这行为类似普通的映射。深度 2 允许我们对嵌套元素进行映射。考虑之前的列表`ab`。顶层，深度 0，是列表。深度 1 是数据框`a`和`b`。深度 2 是这些数据框中的列。深度 3 是这些列中的各个元素。

```
ab |> map_depth(1, sum) |> unlist()
##  a  b
## 42 60
ab |> map_depth(2, sum) |> unlist()
## a.foo a.bar b.foo b.bar
##     6    36    15    45
ab |> map_depth(3, sum) |> unlist()
## a.foo1 a.foo2 a.foo3 a.bar1 a.bar2 a.bar3 b.foo1
##      1      2      3     11     12     13      4
## b.foo2 b.foo3 b.bar1 b.bar2 b.bar3
##      5      6     14     15     16
```

如果你只想将函数应用于部分元素，可以使用`map_if()`。它接受一个谓词和一个函数，并将该函数应用于谓词为真的元素。它返回一个列表，但如果你想要其他类型，可以进行转换。

```
is_even <- function(x) x %% 2 == 0
add_one <- function(x) x + 1
1:6 |> map_if(is_even, add_one) |> as.numeric()
## [1] 1 3 3 5 5 7
```

请注意，这与组合过滤（filtering）和映射不同；组合操作会移除不满足谓词的元素。

```
1:6 |> keep(is_even) |> map_dbl(add_one)
## [1] 3 5 7
```

使用`map_if()`，你会保留所有元素，但函数仅应用于部分元素。

如果你希望对谓词为真的元素应用一个函数，而对谓词为假的元素应用另一个函数，你可以向`.else`参数提供一个函数：

```
add_two <- function(x) x + 2
1:6 |>
  map_if(is_even, add_one, .else = add_two) |>
  as.numeric()
## [1] 3 3 5 5 7 7
```

如果你知道要应用函数的索引，而不是它们必须满足的谓词，可以使用`map_at()`。此函数接受一个索引序列而不是谓词，但在其他方面与`map_if()`工作方式相同。

```
1:6 |> map_at(2:5, add_one) |> as.numeric()
## [1] 1 3 4 5 6 6
```

如果你映射一个列表`x`，你的函数将被调用并传入列表中的元素`x[[i]]`。如果你希望获取包装在长度为 1 的列表中的元素，即使用索引`x[i]`，你可以使用`lmap()`。

```
list(a = 1:3, b = 4:6) |> map(print) |> invisible()
## [1] 1 2 3
## [1] 4 5 6
list(a = 1:3, b = 4:6) |> lmap(print) |> invisible()
## $a
## [1] 1 2 3
##
## $b
## [1] 4 5 6
```

你应用的函数必须始终返回一个列表，并且`lmap()`会将它们连接起来。

```
f <- function(x) list("foo")
list(a = 1:3, b = 4:6) |> lmap(f)
## [[1]]
## [1] "foo"
##
## [[2]]
## [1] "foo"
f <- function(x) if (x[[1]] == 1) list("foo") else list("bar")
list(1:3, 4:6) |> lmap(f)
## [[1]]
## [1] "foo"
##
## [[2]]
## [1] "bar"
##
## [[3]]
## [1] "foo"
##
## [[4]]
## [1] "bar"
```

例如，虽然你可以使用`map()`和`length()`获取列表中元素的长度：

```
list(a = 1:3, b = 4:8) |> map(length) |> unlist()
## a b
## 3 5
```

但如果你尝试用`lmap()`做同样的事情，你会得到一个错误。这是因为`length()`返回一个数值型，而不是一个列表。你需要用`list()`包裹`length()`，这样结果就是包裹在（长度为 1 的）列表中的长度。


```r
wrapped_length <- function(x) {
  x |>
    length() |>          # 获取 x 的长度（结果为数值型）
    list() |>            # 返回到列表
    set_names(names(x))  # 并赋予原始名称
}
list(a = 1:3, b = 4:8) |> lmap(wrapped_length) |> unlist()
## a b 
## 1 1 
```

如果你对这里的长度值均为 1 感到惊讶，请记住函数在调用时，每个索引位置处理的是长度为 1 的列表。如果你想要获取列表内包含内容的长度，则需要将其提取出来。

```r
wrapped_length <- function(x) {
  x |>
    pluck(1) |>          # purrr 中的 pluck(x, 1) 等同于 x[[1]]
    length() |>          # 现在得到底层向量并获取长度
    list() |>            # 返回到列表
    set_names(names(x))  # 并赋予原始名称
}
list(a = 1:3, b = 4:8) %>% lmap(wrapped_length) %>% unlist()
## a b 
## 3 5 
```

不过，如果你要提取嵌套数据，可能应该使用 `map()` 而非 `lmap()`。

函数 `lmap_if()` 和 `lmap_at()` 的工作方式与 `map_if()` 和 `map_at()` 类似，区别在于它们如何索引输入以及将输出作为要拼接的列表进行处理。

有时，我们只想调用函数以产生副作用。在这种情况下，可以将映射的结果通过管道传递给 `invisible()`。函数 `walk()` 可以代你完成这一操作，使用它会让意图更明确，但它本质上只是 `map()` + `invisible()` 的语法糖。

```r
1:3 |> map(print) |> invisible()
## [1] 1
## [1] 2
## [1] 3
1:3 |> walk(print)
## [1] 1
## [1] 2
## [1] 3
```

如果需要映射多个序列，你可以从两种映射函数中选择。有些函数专门映射两个序列。对于每个 `map()` 函数，都存在对应的 `map2()` 函数。这些函数将两个序列作为前两个参数。

```r
x <- 1:3
y <- 3:1
map2_dbl(x, y, `+`)
## [1] 4 4 4
```

你也可以创建序列列表并使用 `pmap()` 函数。

```r
list(x, y) |> pmap_dbl(`+`)
## [1] 4 4 4
```

就像 `map()` 和 `map2()` 一样，也存在特定类型的版本，但通过 `pmap()` 函数，你可以映射两个以上的输入序列。

```r
z <- list(a = 1:3, b = 4:8, c = 10:12)
f <- function(a, b, c) a - b - c
list(x, y, z) |> pmap_dbl(f)
## [1]  0 -1 -2
```

如果你需要知道映射时每个值的索引，可以使用 `imap()` 变体。使用这些函数映射序列时，你的函数需要接受两个参数，第一个参数是序列值，第二个参数是该值在输入中的索引。

```r
x <- c("foo", "bar", "baz")
f <- function(val, idx) paste0(idx, ": ", val)
x |> imap_chr(f)
## [1] "1: foo" "2: bar" "3: baz"
```

还有另一类映射函数变体：`modify()` 函数。它们没有类型变体（但有 `_at`、`_if`、`_depth` 等变体）；相反，它们总是返回与输入相同类型的输出：

```r
modify2(1:3, 3:1, `+`)
## [1] 4 4 4
x <- c("foo", "bar", "baz")
f <- function(val, idx) paste0(idx, ": ", val)
x |> imodify(f)
## [1] "1: foo" "2: bar" "3: baz"
```

## Reduce 和 Accumulate

如果你想把所有输入汇总为一个值，你可能需要使用 `reduce()` 函数。`Reduce` 会反复对输入序列应用一个函数。如果你有一个接受两个参数的函数 *f*(*a*, *x*)，以及一个序列 *x* [1], *x* [2], …, *x* [*n*]，那么 `reduce(f)` 将计算 *f*(… *f*(*f*(*x*[1], *x*[2]), *x*[3]), …, *x*[*n*])，即：先对序列的前两个元素调用函数，结果与下一个元素配对，以此类推。可以将参数 *a* 视为一个累加器，用于保存到目前为止的计算结果。

为了明确函数应用的顺序，我定义一个"配对"类型：

```r
pair <- function(x, y) {
  ret <- list(x, y)
  ret |>
    toString() |> cat() |> invisible()
  ret
}
```

如果我们使用 `pair()` 进行 reduce，可以看到函数被调用时值是如何配对的：

```r
1:4 |> reduce(pair)
## [[[1, 2], 3], 4]
```

如果反转输入，就可以按相反的顺序进行 reduce，先组合最后一对，然后按此顺序传播累加器。

```r
1:4 |> rev() |> reduce(pair)
## [[[4, 3], 2], 1]
```

如果由于某些原因，你想要应用函数并将累加器作为最后一个参数，可以使用 `.dir = "backward"` 参数。

```r
1:4 |> reduce(pair, .dir = "backward")
## [1, [2, [3, 4]]]
```

输入中的第一个（或最后一个）元素不必作为初始累加器的值。如果你想要一个特定的起始值，可以通过 `.init` 参数将其传递给 `reduce()`。

```r
1:3 |> reduce(pair, .init = 0)
## [[[0, 1], 2], 3]
1:3 |> rev() |> reduce(pair, .init = 4)
## [[[4, 3], 2], 1]
1:3 |> reduce(pair, .init = 4, .dir = "backward")
## [1, [2, [3, 4]]]
```

如果你的函数接受多个参数，你可以向 `reduce()` 提供额外参数，然后是输入序列和函数。例如，考虑一个像这样的三参数函数：

```r
## 额外参数
loud_pair <- function(x, y, volume) {
  ret <- list(x, y)
  ret |>
    toString() |>
    paste(volume, '\n', sep = '') |>
    cat()
  # 然后返回新的配对
  ret
}
```

它构建一个配对对象，但作为副作用，它会打印该配对，并后跟一个字符串，指示打印值的"音量"。我们可以将 volume 作为额外参数提供给 `reduce()`：

```r
1:3 |>
  reduce(loud_pair, volume = '!') |>
  invisible()
## [1, 2]!
## [[1, 2], 3]!
1:3 |>
  reduce(loud_pair, volume = '!!') |>
  invisible()
## [1, 2]!!
## [[1, 2], 3]!!
```

如果你想 reduce 两个序列而不是一个——类似于 `reduce()` 的第二个参数，但该参数是一个序列而不是单个值——可以使用 `reduce2()`：

```r
volumes <- c('!', '!!', '!!!')
1:3 |>
  reduce2(volumes, loud_pair) |> invisible()
## [1, 2]!
## [[1, 2], 3]!!
1:3 |>
  reduce2(c('!', '!!', '!!!'), .init = 0, loud_pair) |>
  invisible()
## [0, 1]!
## [[0, 1], 2]!!
## [[[0, 1], 2], 3]!!!
```

如果你想要所有 reduce 操作的中间值，可以使用 `accumulate()` 函数。它会返回每次函数应用结果的序列。

```r
res <- 1:3 |>
  accumulate(pair)
print(res[[1]])
## [1] 1
print(res[[2]])
## [1, 2]
print(res[[3]])
## [[1, 2], 3]
res <- 1:3 |>
  accumulate(pair, .init = 0)
print(res[[1]])
## [1] 0
print(res[[4]])
## [[[0, 1], 2], 3]
res <- 1:3 |>
  accumulate(
    pair, .init = 0,
    .dir = "backward"
  )
print(res[[1]])
## [1, [2, [3, 0]]]
print(res[[4]])
## [1] 0
```

`accumulate2()` 函数的工作方式类似于 `reduce2()`，区别在于它像 `accumulate()` 一样保留中间值。
```


## 部分求值与函数组合

当你对序列进行筛选、映射或规约时，有时需要修改函数以匹配 `purrr` 函数的接口。如果你有一个函数参数过多（超出了接口要求），但可以通过固定某些参数来得到你想要的应用程序，那么你就可以进行所谓的*部分求值*。这仅仅意味着你创建一个新函数，该函数以固定了某些参数的原始函数来调用。

例如，当你进行筛选时，你需要一个接收一个输入值并返回一个（布尔）输出值的函数。如果你想筛选出大于或小于（比如说）3 的值，你可以为此创建函数。

```
greater_than_three <- function(x) x > 3
less_than_three <- function(x) x < 3
1:6 |> keep(greater_than_three)
## [1] 4 5 6
1:6 |> keep(less_than_three)
## [1] 1 2
```

这样做的一个缺点是，你可能需要定义许多这样的函数，即使每个函数在你的管道中只使用一次。

使用 `partial()` 函数，你可以绑定参数，而无需显式定义新函数。例如，要绑定 `<` 的第一个参数（如 `greater_than_three()` 函数那样），你可以使用 `partial()`：

```
1:6 |> keep(partial(`<`, 3))
## [1] 4 5 6
```

默认情况下，你总是绑定第一个（或前几个）参数。要绑定其他参数，你需要指定要绑定的参数名称。小于运算符有以下参数名称：

```
`<`
## function (e1, e2) .Primitive("<")
```

因此，你可以对 `less_than_three()` 使用这种部分求值：

```
1:6 |> keep(partial(`<`, e2 = 3))
## [1] 1 2
```

类似地，你可以在映射中使用部分求值：

```
1:6 |> map_dbl(partial(`+`, 2))
## [1] 3 4 5 6 7 8
1:6 |> map_dbl(partial(`-`, 1))
## [1] 0 -1 -2 -3 -4 -5
1:3 |> map_dbl(partial(`-`, e1 = 4))
## [1] 3 2 1
1:3 |> map_dbl(partial(`-`, e2 = 4))
## [1] -3 -2 -1
```

如果你需要应用多个函数，例如

```
1:3 |>
map_dbl(partial(`+`, 2)) |>
map_dbl(partial(`*`, 3))
## [1] 9 12 15
```

你也可以简单地组合这些函数。函数组合 ° 的工作原理是：(*g* ° *f*)(*x*) = *g*(*f*(*x*))。

因此，上面的管道也可以写成：

```
1:3 |> map_dbl(
compose(partial(`*`, 3), partial(`+`, 2))
)
## [1] 9 12 15
```

使用 `partial()` 和 `combine()`，你可以修改函数，但使用它们并不一定能让你获得易于阅读的代码。一个更易读的替代方案是使用 *lambda 表达式*。

## Lambda 表达式

Lambda 表达式是一种定义匿名函数的简洁语法，匿名函数即我们不命名的函数。名称 "lambda 表达式" 源于 "lambda 演算"（形式逻辑中的一门学科），但在计算机科学中，它主要用作匿名函数的同义词。在某些编程语言中，你不能创建匿名函数；你需要命名一个函数来定义它。在其他语言中，你有用于 lambda 表达式的特殊语法。在 R 中，你定义匿名函数的方式与定义命名函数相同。你总是以相同的方式定义函数；只有在将函数定义赋值给变量时，你才给它一个名字。

如果这听起来太理论化，请考虑这个例子。当我们筛选偶数值时，我们定义了一个函数 `is_even()` 作为谓词使用。

```
is_even <- function(x) x %% 2 == 0
1:6 |> keep(is_even)
## [1] 2 4 6
```

我们使用表达式 `function(x) x %% 2 == 0` 定义了该函数，然后将该函数赋值给名称 `is_even`。与其将函数赋值给一个名称，我们可以在调用 `keep()` 时直接使用函数定义：

```
1:6 |> keep(function(x) x %% 2 == 0)
## [1] 2 4 6
```

R 4 引入了用于定义函数的新语法，为我们提供了一种稍短的形式：

```
1:6 |> keep(\(x) x %% 2 == 0)
## [1] 2 4 6
```

我倾向于在这种情况下使用它，但这完全是个品味问题。`function(`…`)` … 语法本身并没有错。

所以，R 已经有了 lambda 表达式，但其语法可能有些冗长，尤其是在 R 4 及 `\(...) ...` 语法出现之前。在 `purrr` 中，你可以使用公式作为 lambda 表达式，而不是函数。你可以像这样使用公式来定义一个 `is_even()` 函数：

```
is_even_lambda <- ~ .x %% 2 == 0
1:6 |> keep(is_even_lambda)
## [1] 2 4 6
```

或者直接在管道中使用该公式。

```
1:6 |> keep(~ .x %% 2 == 0)
## [1] 2 4 6
```

表达式中的变量 `.x` 将被解释为 lambda 表达式的第一个参数。

Lambda 表达式并不是一种可以普遍用于定义函数的方法。它们之所以有效，只是因为 `purrr` 函数将公式理解为函数规范。你不能这样写：

```
is_even_lambda <- ~ .x %% 2 == 0
is_even_lambda(3)
```

这会导致一个错误。不幸的是，错误信息不是告诉你试图将公式用作函数，而只是说找不到函数 `is_even_lambda`。这是因为当你将变量作为函数调用时，R 会查找函数，而忽略同名的其他变量。如果你将变量重新赋值

```
f <- function(x) 2 * x
f <- 5
f(2)
## Error in f(2): could not find function "f"
```

你会被告知找不到你调用的名称对应的函数——而不是该变量不指向一个函数。这就是如果你试图在 `purrr` 函数（或某些其他 Tidyverse 函数）外部调用 lambda 表达式时遇到的错误。

如果在内部作用域有一个变量，外部作用域有一个函数，R 查找函数的规则可能会更加令人困惑：

```
f <- function(x) 2 * x
g <- function() {
f <- 5 # 不是一个函数
f(2) # 将查找一个函数
}
g()
## [1] 4
```

在这里，外部作用域中的 `f()` 函数被调用，因为它是一个函数；内部作用域中的变量被忽略。

回到 `purrr` 函数中的 lambda 表达式，你可以将它们用作部分求值的更易读版本。

```
1:4 |> map_dbl(~ .x / 2)
## [1] 0.5 1.0 1.5 2.0
1:3 |> map_dbl(~ 2 + .x)
## [1] 3 4 5
1:3 |> map_dbl(~ 4 - .x)
## [1] 3 2 1
1:3 |> map_dbl(~ .x - 4)
## [1] -3 -2 -1
```

或者你可以将它们用于更易读的函数组合版本。

```
1:3 |> map_dbl(~ 3 * (.x + 2))
## [1] 9 12 15
```

如果你需要一个有两个参数的 lambda 表达式，你可以分别使用 `.x` 和 `.y` 作为第一个和第二个参数。

```
map2_dbl(1:3, 1:3, ~ .x + .y)
## [1] 2 4 6
```

如果你需要两个以上的参数，你可以使用 `.*n*` 来表示第 *n* 个参数：

```
list(1:3, 1:3, 1:3) %>% pmap_dbl(~ .1 + .2 + .3)
## [1] 0.6 0.6 0.6
```

## 操作数据框：`dplyr`

`dplyr` 包的功能类似于 `purrr` 包，但其设计用于操作数据框。它会随 `tidyverse` 包一起加载，但你也可以使用以下命令单独加载它：

```
library(dplyr)
```

使用 `dplyr` 的常用方式类似于使用 `purrr`。你将一系列操作串联在一个管道中，操作之间用 `%>%` 运算符分隔。这两个包的区别在于，`purrr` 函数作用于序列，而 `dplyr` 函数作用于数据框。

这个包非常庞大，并且每个新版本都会增加更多功能，因此我不可能描述你可以用 `dplyr` 做的所有事情。我只能让你领略一下这个包的功能，并建议你查阅文档以获取更多信息。



### 选择列

对数据框执行的最简单操作之一就是选择其中的一列或多列。你可以通过使用 `$` 进行索引来实现这一点：

```
iris_df                
## 1          5.1         3.5          1.4
## 2          4.9         3            1.4
## 3          4.7         3.2          1.3
## # . . . 还有 147 行，以及另外 2 个变量：
## # Petal.Width , Species 
head(iris_df$Species)
## [1] setosa setosa setosa setosa setosa setosa
## Levels: setosa versicolor virginica
```

然而，这种方法在处理数据框在动作之间传递的管道操作时效果不佳。在 `dplyr` 中，你可以使用 `select()` 函数来挑选列。我们在第 4 章中探讨过它，但让我们再看几个例子。

你可以向该函数提供一个或多个列名，它将返回一个仅包含这些列的数据框。

```
iris_df |>
select(Sepal.Length, Species) |>
print(n = 3)
## # 一个 tibble：150 × 2
##   Sepal.Length Species
##           
## 1          5.1 setosa
## 2          4.9 setosa
## 3          4.7 setosa
## # . . . 还有 147 行
```

在选择列时，你也可以使用补集；只需在列名前加上 `!` 即可。

```
iris_df |>
select(!Species) |>
print(n = 3)
## # 一个 tibble：150 × 4
##   Sepal.Length Sepal.Width Petal.Length
##                         
## 1          5.1         3.5          1.4
## 2          4.9         3            1.4
## 3          4.7         3.2          1.3
## # . . . 还有 147 行，以及另外 1 个变量：
## # Petal.Width 
iris_df |>
select(!c(Species, Sepal.Length)) |>
print(n = 3)
## # 一个 tibble：150 × 3
##   Sepal.Width Petal.Length Petal.Width
##                        
## 1         3.5          1.4         0.2
## 2         3            1.4         0.2
## 3         3.2          1.3         0.2
## # . . . 还有 147 行
```

你也可以使用减号从选择中移除列：

```
iris_df |>
select(-Species) |>
print(n = 3)
## # 一个 tibble：150 × 4
##   Sepal.Length Sepal.Width Petal.Length
##                         
## 1          5.1         3.5          1.4
## 2          4.9         3            1.4
## 3          4.7         3.2          1.3
## # . . . 还有 147 行，以及另外 1 个变量：
## # Petal.Width 
iris_df |>
select(-c(Species, Sepal.Length)) |>
print(n = 3)
## # 一个 tibble：150 × 3
##   Sepal.Width Petal.Length Petal.Width
##                        
## 1         3.5          1.4         0.2
## 2         3            1.4         0.2
## 3         3.2          1.3         0.2
## # . . . 还有 147 行
```

但是，当你向 `select()` 提供多个参数时，`!` 和 `-` 的行为并不完全相同。

使用 `!` 时，你请求的是除了你排除的列之外的所有列，所以如果你写

```
iris_df |>
select(!Species, !Sepal.Length) |>
print(n = 3)
## # 一个 tibble：150 × 5
##   Sepal.Length Sepal.Width Petal.Length
##                         
## 1          5.1         3.5          1.4
## 2          4.9         3            1.4
## 3          4.7         3.2          1.3
## # . . . 还有 147 行，以及另外 2 个变量：
## # Petal.Width , Species 
```

你首先请求除了 `Species` 之外的所有列，然后请求除了 `Sepal.Length` 之外的所有列，你得到的是这两个选择的并集（其中包括所有内容）。

使用减号时，你请求的是要移除的列，所以使用

```
iris_df |>
select(-Species, -Sepal.Length) |>
print(n = 3)
## # 一个 tibble：150 × 3
##   Sepal.Width Petal.Length Petal.Width
##                        
## 1         3.5          1.4         0.2
## 2         3            1.4         0.2
## 3         3.2          1.3         0.2
## # . . . 还有 147 行
```

你首先移除了 `Species`，然后移除了 `Sepal.Length`，因此两者都不包含在最终表格中。

你不需要使用列名。你也可以使用索引。

```
iris_df |>
select(1) |>
print(n = 3)
## # 一个 tibble：150 × 1
##   Sepal.Length
##          
## 1          5.1
## 2          4.9
## 3          4.7
## # . . . 还有 147 行
iris_df |>
select(1:3) |>
print(n = 3)
## # 一个 tibble：150 × 3
##   Sepal.Length Sepal.Width Petal.Length
##                         
## 1          5.1         3.5          1.4
## 2          4.9         3            1.4
## 3          4.7         3.2          1.3
## # . . . 还有 147 行
```

当然，当你阅读代码时，这并不能提供关于选中了什么的信息。前面的第二个选择展示了你可以像使用列名一样，使用索引来提取范围。

```
iris_df |>
select(Petal.Length:Species) |>
print(n = 3)
## # 一个 tibble：150 × 3
##   Petal.Length Petal.Width Species
##                  
## 1          1.4         0.2 setosa
## 2          1.4         0.2 setosa
## 3          1.3         0.2 setosa
## # . . . 还有 147 行
```

当然，第 4 章中描述的所有其他函数也都适用于 `select()`：

```
iris_df |>
select(starts_with("Petal")) |>
print(n = 3)
## # 一个 tibble：150 × 2
##   Petal.Length Petal.Width
##                 
## 1          1.4         0.2
## 2          1.4         0.2
## 3          1.3         0.2
## # . . . 还有 147 行
iris_df |>
select(-starts_with("Petal")) |>
print(n = 3)
## # 一个 tibble：150 × 3
##   Sepal.Length Sepal.Width Species
##                    
## 1          5.1         3.5 setosa
## 2          4.9         3   setosa
## 3          4.7         3.2 setosa
## # . . . 还有 147 行
iris_df |>
select(starts_with("Petal"), Species) |>
print(n = 3)
## # 一个 tibble：150 × 3
##   Petal.Length Petal.Width Species
##                  
## 1          1.4         0.2 setosa
## 2          1.4         0.2 setosa
## 3          1.3         0.2 setosa
## # . . . 还有 147 行
iris_df |>
select(starts_with("PETAL", ignore.case = TRUE)) |>
print(n = 3)
## # 一个 tibble：150 × 2
##   Petal.Length Petal.Width
##                 
## 1          1.4         0.2
## 2          1.4         0.2
## 3          1.3         0.2
## # . . . 还有 147 行
iris_df |>
select(starts_with("S")) |>
print(n = 3)
## # 一个 tibble：150 × 3
##   Sepal.Length Sepal.Width Species
##                  
## 1          5.1         3.5 setosa
## 2          4.9         3   setosa
## 3          4.7         3.2 setosa
## # . . . 还有 147 行
iris_df |>
select(ends_with("Length")) |>
print(n = 3)
## # 一个 tibble：150 × 2
##   Sepal.Length Petal.Length
##                  
## 1          5.1          1.4
## 2          4.9          1.4
## 3          4.7          1.3
## # . . . 还有 147 行
iris_df |>
select(contains("ng")) |>
print(n = 3)
## # 一个 tibble：150 × 2
##   Sepal.Length Petal.Length
##                  
## 1          5.1          1.4
## 2          4.9          1.4
## 3          4.7          1.3
## # . . . 还有 147 行
```

你还可以使用 `select()` 来重命名列。如果你使用一个命名参数，你选择的列将获得你为函数参数使用的名称。

```
iris_df |>
select(sepal_length = Sepal.Length,
sepal_width = Sepal.Width) |>
print(n = 3)
## # 一个 tibble：150 × 2
##   sepal_length sepal_width
##                 
## 1          5.1         3.5
## 2          4.9         3
## 3          4.7         3.2
## # . . . 还有 147 行
```

由于 `select()` 会移除你*没有*选中的列，它可能并不总是满足你需求的最佳选择。一个类似于 `select()` 但不会丢失列的函数是 `rename()`。使用这个函数，你可以重命名列，同时保留其余列。

```
iris_df |>
rename(sepal_length = Sepal.Length,
sepal_width = Sepal.Width) |>
print(n = 3)
## # 一个 tibble：150 × 5
##   sepal_length sepal_width Petal.Length
##                         
## 1          5.1         3.5          1.4
## 2          4.9         3            1.4
## 3          4.7         3.2          1.3
## # . . . 还有 147 行，以及另外 2 个变量：
## # Petal.Width , Species 
```



### 筛选

`select()`函数用于提取列的子集，而`filter()`函数则对行进行同样的操作。

`iris_df`数据集包含三个不同的物种。我们可以使用`distinct()`函数来查看这一点。该函数也来自`dplyr`包，它会从选定的列中返回所有唯一的行。

```
iris_df |> distinct(Species)
## # A tibble: 3 × 1
##   Species
##   
## 1 setosa
## 2 versicolor
## 3 virginica
```

我们可以使用`filter()`来选择物种为“setosa”的行：

```
iris_df |>
filter(Species == "setosa") |>
print(n = 3)
## # A tibble: 50 × 5
##   Sepal.Length Sepal.Width Petal.Length
##                         
## 1          5.1         3.5          1.4
## 2          4.9         3            1.4
## 3          4.7         3.2          1.3
## # . . . with 47 more rows, and 2 more variables:
## # Petal.Width , Species 
```

我们可以在一个管道中组合使用`filter()`和`select()`，同时获取列和行的子集。

```
iris_df |>
filter(Species == "setosa") |>
select(ends_with("Length"), Species) |>
print(n = 3)
## # A tibble: 50 × 3
##   Sepal.Length Petal.Length Species
##                   
## 1          5.1          1.4 setosa
## 2          4.9          1.4 setosa
## 3          4.7          1.3 setosa
## # . . . with 47 more rows
```

通常，我们可以在管道中按需串联任意多个`dplyr`（或`purrr`）函数。

```
iris_df |>
filter(Species != "setosa") |>
distinct(Species) |>
print(n = 3)
## # A tibble: 2 × 1
##   Species
##   
## 1 versicolor
## 2 virginica
```

我们可以使用多个列来进行筛选：

```
iris_df |>
filter(Sepal.Length > 5, Petal.Width 
print(n = 3)
## # A tibble: 15 × 5
##   Sepal.Length Sepal.Width Petal.Length
##            
## 1          5.1   3.5   1.4
## 2          5.4   3.7   1.5
## 3          5.8   4     1.2
## # . . . with 12 more rows, and 2 more variables:
## # Petal.Width , Species 
```

我们也可以使用函数作为谓词，例如，使用`between()`函数来选择给定范围内的数值。

```
iris_df |>
filter(between(Sepal.Width, 2, 2.5)) |>
print(n = 3)
## # A tibble: 19 × 5
##   Sepal.Length Sepal.Width Petal.Length
##                         
## 1          4.5         2.3          1.3
## 2          5.5         2.3          4
## 3          4.9         2.4          3.3
## # . . . with 16 more rows, and 2 more variables:
## # Petal.Width , Species 
```

我们不能使用与`select()`配合使用的`starts_with()`、`ends_with()`等函数。然而，这并不意味着我们不能使用字符串模式来筛选行。我们只是需要不同的函数。例如，我们可以使用`stringr`包中的`str_starts()`。我们将在第 9 章中回到`stringr`。

```
iris_df |>
filter(str_starts(Species, "v")) |>
print(n = 3)
## # A tibble: 100 × 5
##   Sepal.Length Sepal.Width Petal.Length
##                         
## 1          7           3.2          4.7
## 2          6.4         3.2          4.5
## 3          6.9         3.1          4.9
## # . . . with 97 more rows, and 2 more variables:
## # Petal.Width , Species 
iris_df |>
filter(str_ends(Species, "r")) |>
print(n = 3)
## # A tibble: 50 × 5
##   Sepal.Length Sepal.Width Petal.Length
##                         
## 1          7           3.2          4.7
## 2          6.4         3.2          4.5
## 3          6.9         3.1          4.9
## # . . . with 47 more rows, and 2 more variables:
## # Petal.Width , Species 
```

`filter()`通过在单个列上应用谓词来选择行，而其他筛选变体则会在多个列上使用谓词。

`filter_all()`函数将对所有列应用谓词。你必须提供一个表达式，其中使用`.`（点）代表表格单元格的值，并将该表达式包裹在`any_var()`或`all_var()`两个函数之一中。如果你使用`any_var()`，则只要有一个列满足谓词，该行就会被包含；如果你使用`all_var()`，则所有列都必须满足谓词。

我们可以要求`iris_df`数据中的任何值都必须大于五：

```
iris_df |>
select(-Species) |>
filter_all(any_vars(. > 5)) |>
print(n = 3)
## # A tibble: 118 × 4
##   Sepal.Length Sepal.Width Petal.Length
##                         
## 1          5.1         3.5          1.4
## 2          5.4         3.9          1.7
## 3          5.4         3.7          1.5
## # . . . with 115 more rows, and 1 more variable:
## # Petal.Width 
```

如果`.`是字符串，则表达式`. > 5`没有意义，所以我在筛选之前必须删除`Species`列。我们很少会接受丢失一个信息丰富的变量。如果你想保留某个列，但不将谓词应用于其中的值，你可以使用`filter_at()`函数。

```
iris_df |>
filter_at(vars(-Species), any_vars(. > 5)) |>
print(n = 3)
## # A tibble: 118 × 5
##   Sepal.Length Sepal.Width Petal.Length
##                         
## 1          5.1         3.5          1.4
## 2          5.4         3.9          1.7
## 3          5.4         3.7          1.5
## # . . . with 115 more rows, and 2 more variables:
## # Petal.Width , Species 
```

你可以给`filter_at()`传递一个字符串向量或一个变量向量（未加引号的列名）。因为我需要排除`Species`以选择所有其他列，所以这里我使用了`vars()`。在其他情况下，你可以使用任一选项。

```
iris_df |>
filter_at(c("Petal.Length", "Sepal.Length"),
any_vars(. > 0)) |>
print(n = 3)
## # A tibble: 150 × 5
##   Sepal.Length Sepal.Width Petal.Length
##                         
## 1          5.1         3.5          1.4
## 2          4.9         3            1.4
## 3          4.7         3.2          1.3
## # . . . with 147 more rows, and 2 more variables:
## # Petal.Width , Species 
iris_df |>
filter_at(vars(Petal.Length, Sepal.Length),
any_vars(. > 0)) |>
print(n = 3)
## # A tibble: 150 × 5
##   Sepal.Length Sepal.Width Petal.Length
##                         
## 1          5.1         3.5          1.4
## 2          4.9         3            1.4
## 3          4.7         3.2          1.3
## # . . . with 147 more rows, and 2 more variables:
## # Petal.Width , Species 
```

你可以使用`filter_if()`对列应用谓词：

```
iris_df |>
filter_if(is.numeric, all_vars(. 
print(n = 3)
## # A tibble: 22 × 5
##   Sepal.Length Sepal.Width Petal.Length
##                         
## 1          4.9          3           1.4
## 2          4.7          3.2         1.3
## 3          4.6          3.1         1.5
## # . . . with 19 more rows, and 2 more variables:
## # Petal.Width , Species 
```

你可以使用函数或 lambda 表达式作为谓词。

考虑一个表格中某些数值包含缺失数据的情况。如果我们想筛选出所有值都大于三的行，我们可以使用`filter_all()`：

```
df  filter_all(all_vars(. > 3))
## # A tibble: 1 × 3
##       A    B      C
##     
## 1    11    12    13
```

它删除了前两行和最后一行，因为它们都包含小于或等于三的值。它还删除了第三行，因为`NA`不被视为大于三。我们可以将测试限制在不包含缺失值的列上：

```
df |> filter_if(~ all(!is.na(.)), all_vars(. > 3))
## # A tibble: 3 × 3
##       A     B     C
##     
## 1     4     5    NA
## 2    11    12    13
## 3    22    22    1
```

这会将列`C`排除在测试之外。这意味着即使`C`的值小于三，我们也保留了最后一行。


如果您想保留中间两行，但不要第一行或最后一行，可以编写一个更复杂的谓词，并使用 `filter_all()`。

```
df |> filter_all(all_vars(is.na(.) | . > 3))
## # 一个 tibble：2 × 3
##       A     B     C
##     
## 1     4     5    NA
## 2    11    12    13
```

你需要使用向量“或”运算符 `|`。表达式必须是向量表达式，因此不能使用 `||`。

许多 `dplyr` 函数都有 `_all`、`_at` 和 `_if` 变体，但此处我仅描述常用的函数。其他函数请参考软件包文档。

### 排序

如果要按选定列的值对行进行排序，所需的函数是 `arrange()`。你可以按一列或多列排序：

```
iris_df |>
arrange(Petal.Length) |>
print(n = 5)
## # 一个 tibble：150 × 5
##   Sepal.Length Sepal.Width Petal.Length
##                         
## 1          4.6         3.6          1
## 2          4.3         3            1.1
## 3          5.8         4            1.2
## 4          5           3.2            1.2
## 5          4.7         3.2          1.3
## # . . . 还有 145 行，以及 2 个更多变量：
## # Petal.Width , Species 
iris_df |>
arrange(Sepal.Length, Petal.Length) |>
print(n = 5)
## # 一个 tibble：150 × 5
##   Sepal.Length Sepal.Width Petal.Length
##                         
## 1          4.3         3            1.1
## 2          4.4         3            1.3
## 3          4.4         3.2          1.3
## 4          4.4         2.9          1.4
## 5          4.5         2.3          1.3
## # . . . 还有 145 行，以及 2 个更多变量：
## # Petal.Width , Species 
```

如果要按降序排序，可以使用函数 `desc()`。

```
iris_df |>
arrange(desc(Petal.Length)) |>
print(n = 5)
## # 一个 tibble：150 × 5
##   Sepal.Length Sepal.Width Petal.Length
##                         
## 1          7.7         2.6          6.9
## 2          7.7         3.8          6.7
## 3          7.7         2.8          6.7
## 4          7.6         3            6.6
## 5          7.9         3.8          6.4
## # . . . 还有 145 行，以及 2 个更多变量：
## # Petal.Width , Species 
```

### 修改数据框

在处理数据框时，我们通常希望根据表中的变量（列）计算值。仅靠筛选行和列是不够的。

`mutate()` 函数允许我们根据涉及任何现有列的表达式，向数据框添加新列。考虑一个包含宽度和高度的表格。

```
df <- tribble(
~height, ~width,
10,     12,
42,     24,
14,     12
)
```

我们可以使用 `mutate()` 和以下表达式添加一个面积列：

```
df |> mutate(area = height * width)
## # 一个 tibble：3 × 3
##   height  width  area
##       
## 1     10     12   120
## 2     42     24  1008
## 3     14     12   168
```

如果向数据框添加列，可以引用已经添加的变量。例如，如果高度和宽度数据以英寸为单位，而你想转换为厘米，并且还想要平方厘米的面积，可以这样做：

```
cm_per_inch <- 2.54
df |> mutate(
height_cm = cm_per_inch * height,
width_cm = cm_per_inch * width,
area_cm = height_cm * width_cm
)
## # 一个 tibble：3 × 5
##   height width height_cm width_cm area_cm
##                 
## 1     10    12       25.4      30.5  774.
## 2     42    24      107\.       61.0 6503.
## 3     14    12       35.6      30.5 1084.
```

然而，以下表达式将无法工作，因为你无法在 `mutate()` 调用中引用稍后添加的变量：

```
df %>% mutate(
area_cm = height_cm * width_cm,
height_cm = cm_per_inch * height,
width_cm = cm_per_inch * width
)
## Error in `mutate()`:
## ! 计算 `area_cm = height_cm * width_cm` 时出现问题。
## 由以下错误引起：
## ! 找不到对象 'height_cm'
```

如果不为新变量命名，即调用 `mutate()` 时不使用命名参数，那么表达式将成为变量名：

```
df |> mutate(cm_per_inch * height)
## # 一个 tibble：3 × 3
##   height width `cm_per_inch * height`
##                       
## 1     10    12                   25.4
## 2     42    24                  107.
## 3     14    12                   35.6
```

在此示例中，单位（英寸和厘米）未与数据一起编码，在未来的计算中可能会混淆不同的单位。如果使用 `units` 包（不属于 Tidyverse），可以显式指定单位，在合理的情况下自动进行单位转换，并且还能获得单位类型安全性作为额外好处。

```
library(units)
df |> mutate(
height_in = units::as_units(height, "in"),
width_in = units::as_units(width, "in"),
area_in = height_in * width_in,
height_cm = units::set_units(height_in, "cm"),
width_cm = units::set_units(width_in, "cm"),
area_cm = units::set_units(area_in, "cmˆ2")
)
## # 一个 tibble：3 × 8
##   height width height_in width_in area_in
##            [in]     [in] [in²]
## 1     10    12        10        12    120
## 2     42    24        42        24   1008
## 3     14    12        14        12   168
## # . . . 还有 3 个更多变量：height_cm [cm],
## # width_cm [cm], area_cm [cm²]
```

这是可行的，因为 tibble 可以包含各种类型的类，`units` 包正是利用了这一点。Tibble 使用一个接口，允许你定义自己的类应如何显示，但这超出了本书的范围。

之前，我们为原始数据表创建了携带单位的值，但原始的高度和宽度列仍然存在。我们可以使用 `select()` 来移除它们，但函数 `transmute()` 已经为我们完成了这项工作。


```r
df %>% transmute(
  height_in = units::as_units(height, "in"),
  width_in = units::as_units(width, "in"),
  area_in = height_in * width_in,
  height_cm = units::set_units(height_in, "cm"),
  width_cm = units::set_units(width_in, "cm"),
  area_cm = units::set_units(area_in, "cm²")
)
## # A tibble: 3 × 6
##   height_in width_in area_in height_cm width_cm
##         [in]     [in]  [in²]      [cm]    [cm]
## 1        10       12     120       25.4    30.5
## 2        42       24    1008      107.     61.0
## 3        14       12     168       35.6    30.5
## # . . . with 1 more variable: area_cm [cm²]
```

当你使用 `transmute()` 而非 `mutate()` 时，未赋值的变量将被移除。

与过滤操作类似，在转换数据框时使用的表达式必须是向量化表达式。请看以下示例：

```r
df %>% mutate(~ if (.x < 0) -.x else .x)
## Error in `mutate()`:
## ! Problem while computing `..1 = ~if (.x < 0) -.x else .x`.
## x `..1` must be a vector, not a `formula` object.
```

该操作失败，因为函数 `my_abs()`（我自定义的 `abs()` 函数版本）不是向量化表达式。由于 `if` 表达式中的 `x` 使然，它并非向量化表达式。

如果我们使用内置的 `abs()` 函数，那么该表达式就没有问题；它支持向量运算。

```r
df %>% mutate(abs(x))
## # A tibble: 3 × 2
##       x `abs(x)`
##    <dbl>    <dbl>
## 1  11.5    11.5
## 2  20.9    20.9
## 3  17.2    17.2
```

如果你使用 `ifelse()` 这种 `if` 表达式的向量化版本，就能得到一个向量化表达式。

```r
df %>% mutate(ifelse(x < 0, -x, x))
## # A tibble: 3 × 2
##       x `ifelse(x < 0, -x, x)`
##    <dbl>                 <dbl>
## 1  11.5                  11.5
## 2  20.9                  20.9
## 3  17.2                  17.2
```

或者，你也可以使用 `Vectorize()` 函数，将一个不处理向量的函数转化为向量化表达式。

```r
my_abs <- Vectorize(function(x) if (x < 0) -x else x)
df %>% mutate(my_abs(x))
## # A tibble: 3 × 2
##      x `my_abs(x)`
##   <dbl>      <dbl>
## 1  11.5       11.5
## 2  20.9       20.9
## 3  17.2       17.2
```

如果你需要将输入映射到多个不同的输出值，你可以无限嵌套 `ifelse()` 表达式，但这会导致代码难以阅读。`case_when()` 函数能极大地缓解这个问题。你可以为其提供一组谓词及对应的表达式，它将返回第一个匹配谓词所对应的表达式。

请看以下示例，我们使用 `case_when()` 根据变量 `x` 的值对其进行分类：

```r
df %>% 
  mutate(
    x_category = case_when(
      x - mean(x) > 2 * sd(x) ~ "large",
      x - mean(x) < -2 * sd(x) ~ "small",
      TRUE                     ~ "medium"
    )
  ) |>
  print(n = 3)
## # A tibble: 100 × 2
##        x x_category
##     <dbl> <chr>
## 1  -0.736 medium
## 2  -0.478 medium
## 3   0.796 medium
## # . . . with 97 more rows
```

其中的 `TRUE` 行对应于 `if-else` 语句中的 `else` 部分。

### 分组与汇总

在许多分析中，我们需要数据的汇总统计。如果你能将一个或多个列映射为单个汇总值，可以使用 `summarise()` 函数。

```r
df %>% summarise(mean_x = mean(x), mean_y = mean(y))
## # A tibble: 1 × 2
##   mean_x mean_y
##    <dbl>  <dbl>
## 1 -0.154 0.0224
```

在此示例中，我们将数据汇总为变量 `x` 和 `y` 的均值。

如果你将数据拆分为不同类别，并希望按组处理数据，可以使用 `group_by()` 函数。

```r
classify <- function(x) {
  case_when(
    x - mean(x) > 2 * sd(x) ~ "large",
    x - mean(x) < -2 * sd(x) ~ "small",
    TRUE                     ~ "medium"
  )
}
df %>% mutate(x_category = classify(x)) |>
  group_by(x_category) |>
  print(n = 3)
## # A tibble: 100 × 3
## # Groups:    x_category [3]
##        x       y x_category
##    <dbl>   <dbl> <chr>
## 1  -2.08 -1.52   medium
## 2  0.580 -0.990  medium
## 3 -0.995 -0.443  medium
## # . . . with 97 more rows
```

结果是一个数据框，在打印时看起来与分组前没有区别。然而，在标题中你会注意到这一行：

```
