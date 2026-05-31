# 8. 整洁求值

所谓的 tidyverse 指的是一组设计上协同工作且基于相似设计的 R 包，这些包本身都可以被视为特定领域语言。这些包包括 `dplyr`、`tidyr` 和 `ggplot2`，主要由执行非标准求值的函数组成。它们管理非标准求值的方式在各包中是一致的，并且基于所谓的整洁求值，这主要依赖于 `rlang` 包中实现的两个特性：quosure 和准引用（quasi-quotation）。

我们将再次使用 `rlang` 包，但为了明确指出何时使用该包而非基本的 R 非标准求值，我通常会使用完全限定名。换句话说，我会写 `rlang::quo` 而不是 `quo`，并且不加载该包。我们还会使用 `purrr` 包，但这里我也将使用完全限定名。不过，我会加载 `magrittr` 以获得管道操作符，以及 `tibble` 和 `dplyr` 包来处理数据框。

```
library(magrittr)
library(dplyr)
library(tibble)
```



## 引号表达式

`rlang` 包提供了替代 `quote` 和 `substitute` 的函数，用于创建引号表达式（quosures）——一种基于公式的表达式，它会携带其所在的环境——而非单纯的引号表达式。要从一个表达式创建引号表达式，可以使用 `quo`。

```
q <- rlang::quo(2 * x)
q
##   expr: ² * x
##   env:  global
```

在函数调用内部，与 `substitute` 对应的引号表达式函数是 `enquo`。

```
f <- function(x) rlang::enquo(x)
f(2 * x)
##   expr: ² * x
##   env:  global
```

在这两个例子中，与引号表达式关联的作用域都是全局环境，因为表达式正是在这个层面上编写的。

引号表达式只是公式的一种特殊类型，因此我们可以像上一节那样访问它的环境和表达式：

```
q[[2]]
## 2 * x
environment(q)
## <environment: R_GlobalEnv>
```

然而，`rlang` 提供了专门处理引号表达式的函数，能让我们的代码意图更加清晰。要获取引号表达式中的表达式，可以使用 `get_expr` 函数。

```
rlang::get_expr(q)
## 2 * x
```

这个术语有些反讽，因为 unquote 函数返回的是一个引号表达式，但它确实剥离了引号表达式的包装，返回了一个原始表达式。还有一个反引号函数 `UQ`，它会在求值的意义上对表达式进行反引号，但其用途不同，我们将在下一节介绍。

获取与引号表达式关联的环境，可以使用前面提到的 `environment` 函数，但 `rlang` 中对应的函数是 `get_env`。

```
rlang::get_env(q)
## <environment: R_GlobalEnv>
>

使用引号表达式时，就不能再用 `eval` 对其进行求值了。引号表达式本质上是一个公式，而公式的求值结果就是公式本身。

```
eval(q)
##   expr: ² * x
##   env:  global
```

相反，你需要使用 `eval_tidy` 函数。

```
x <- 1
rlang::eval_tidy(q)
## [1] 2
x <- 2
rlang::eval_tidy(q)
## [1] 4
```

引号表达式会在其关联的环境中求值。我们在函数 `f` 内部创建了引号表达式 `q`，但它的环境是全局环境，所以当我们修改 `x` 的值时，会影响 `q` 的求值结果。

如果我们在一个局部函数作用域内创建引号表达式，它会记住这个上下文——就像闭包一样。例如，我们将函数 `f` 定义如下：

```
f <- function(x, y) rlang::quo(x + y + z)
```

这个引号表达式会记住调用 `f` 时的函数参数 `x` 和 `y`，但 `z` 则需要从别处获取。考虑一下直接求值引号表达式与求值表达式 `x + y + z` 的区别：

```
q <- f(1, 2)
x <- y <- z <- 3
rlang::eval_tidy(q) # 1 + 2 + 3
## [1] 6
x + y + z # 3 + 3 + 3
## [1] 9
```

与 `eval` 相似，`eval_tidy` 允许你提供一个列表、数据框或环境，其中包含变量到值的绑定。当你这样做时，你提供的值会覆盖引号表达式环境中的变量——这种效果被称为**超域作用**。示例如下：

```
x <- 1:4
y <- 1:4
q <- quo(x+y)
rlang::eval_tidy(q)
## [1] 2 4 6 8
rlang::eval_tidy(q, list(x = 5:8))
## [1]  6  8 10 12
```

引号表达式 `q` 绑定到全局环境，因此当我们求值时，`x` 和 `y` 都是 `1:4`。但是，当我们向 `eval_tidy` 提供第二个参数时，可以将 `x` 的值覆盖为 `5:8`。这个特性你应该在 `dplyr` 中见过，在那里你可以通过参数访问数据框中的列，并且这些列会覆盖任何可能被使用的全局变量。

这个特性也可以用来在函数调用中用函数参数覆盖变量。考虑以下两个函数：

```
f <- function(expr,x) {
  q <- rlang::enquo(expr)
  rlang::eval_tidy(q)
}
g <- function(expr,x) {
  q <- rlang::enquo(expr)
  rlang::eval_tidy(q, environment())
}
f(x + y, x = 5:8)
## [1] 2 4 6 8
g(x + y, x = 5:8)
## [1]  6  8 10 12
```

函数 `f` 在其作用域内求值引号表达式，该作用域不包含函数参数 `x`；而函数 `g` 使用函数环境进行超域作用。这使得变量 `x` 指向了函数参数而非全局参数。

你在 `eval_tidy` 中求值的表达式不必是引号表达式。该函数同样可以愉快地求值原始表达式，此时它的行为与 `eval` 完全一致。

```
rlang::eval_tidy(quote(x + y))
## [1] 2 4 6 8
```

与 `eval` 一样，`eval_tidy` 接受第三个参数，该参数将作为封闭作用域。这用于处理原始表达式——那些通过 `quote` 创建的表达式。

```
rlang::eval_tidy(quote(xx), env = list2env(list(xx = 5:8)))
## [1] 5 6 7 8
```

对于引号表达式，则不会使用此参数。

```
rlang::eval_tidy(quo(xx), env = list2env(list(xx = 5:8)))
## Error in rlang::eval_tidy(quo(xx), env = list2env(list(xx = 5:8))): object 'xx' not found
>

（正如其名所暗示的）我在这里使用的 `list2env` 函数可以将列表转换为环境。它是快速构造环境并填充变量的方法。

如果你想创建一个具有超域作用的闭包（换句话说，你想创建一个函数，它在求值引号表达式时先查找局部变量，然后再查找引号表达式的环境），那么在创建函数时不能直接调用 `eval_tidy`。这会让 R 尝试求值该闭包，但此时你还没有所需的变量——这些变量是在你调用闭包时才提供的。相反，你可以分别使用 `get_expr` 和 `get_env` 来分离原始表达式和引号表达式的环境。考虑下面所示的 `make_function` 函数：

```
make_function <- function(args, body) {
  body <- rlang::enquo(body)
  rlang::new_function(args, rlang::get_expr(body), rlang::get_env(body))
}
g <- make_function(alist(x = , y = ), x + y)
g(x = 1:4, y = 1:4)
## [1]  3  6  9 12
```

这里，`make_function` 接受两个参数：一个参数的对偶列表和一个函数体的表达式。这比我们在前一章编写的 lambda 表达式更底层一些，但本质上是相同的。在这个例子中，我关注的是我们创建的闭包，而不是语言设计问题。我们将函数体转换为引号表达式，这保证了它有一个关联的环境。然而，在使用 `new_function` 创建的函数中，我们从引号表达式中提取环境来创建新函数的函数体，但我们将函数的环境设置为该引号表达式的环境。

在函数 `f` 中，我们有一个知道 `z` 值的局部作用域，并在该作用域内创建了一个新函数。由此得到的引号表达式与局部作用域 `f` 关联，因此它也知道 `z`。调用 `g` 时我们提供了变量 `x` 和 `y`，但 `z` 的值取自 `f` 的局部作用域。

如果直接调用，使用调用者的环境和使用引号表达式的环境是没有区别的。

```
make_function_quo <- function(args, body) {
  body <- rlang::enquo(body)
  rlang::new_function(args, rlang::get_expr(body), rlang::get_env(body))
}
make_function_quote <- function(args, body) {
  body <- substitute(body)
  rlang::new_function(args, body, rlang::caller_env())
}
g <- make_function_quo(alist(x=, y=), x + y)
h <- make_function_quote(alist(x=, y=), x + y)
g(x = 1:4, y = 1:4)
## [1] 2 4 6 8
h(x = 1:4, y = 1:4)
## [1] 2 4 6 8
```

但是，考虑一个更复杂的例子：我们将表达式收集到一个列表中，并编写一个函数将所有表达式转换为函数，然后使用 `purrr` 包中的 `invoke_map` 函数将这些函数应用于数值。我们可以像这样使用之前用过的链表结构来构建表达式：

```
cons <- function(elm, lst) list(car=elm, cdr=lst)
lst_length <- function(lst) {
  len <- 0
  while (!is.null(lst)) {
    lst <- lst$cdr
    len <- len + 1
  }
  len
}
lst_to_list <- function(lst) {
  v <- vector(mode = "list", length = lst_length(lst))
  index <- 1
  while (!is.null(lst)) {
    v[[index]] <- lst$car
    lst <- lst$cdr
    index <- index + 1
  }
  v
}
expressions <- function() list(ex = NULL)
add_expression <- function(ex, expr) {
  ex$ex <- cons(rlang::enquo(expr), ex$ex)
  ex
}
```



将表达式转换为函数非常直接。如果我们希望函数的顺序与添加顺序一致，则需要对结果列表进行反转，因为我们在使用链表时总是将表达式添加到列表头部。

```
make_functions <- function(ex, args) {
  results <- vector("list", length = lst_length(ex$ex))
  i <- 1; lst <- ex$ex
  while (!is.null(lst)) {
    results[[i]] <-
      rlang::new_function(args, rlang::get_expr(lst$car),
                          rlang::get_env(lst$car))
    i <- i + 1
    lst <- lst$cdr
  }
  rev(results)
}
```

借助这个用于收集表达式的小型领域特定语言，我们可以编写一个函数，该函数根据给定的截距创建用于计算直线上各点 `y` 坐标的表达式。

```
make_line_expressions <- function(intercept) {
  new_expr_list() %>%
    add_expression(coef + intercept) %>%
    add_expression(2*coef + intercept) %>%
    add_expression(3*coef + intercept) %>%
    add_expression(4*coef + intercept)
}
```

当调用`make_line_expressions`时，这些表达式就已经知晓截距（`intercept`）的值——至少这是设计意图——但斜率（`coef`）应该在后续的函数调用中提供。我们可以通过另一个函数为这些表达式创建具体的函数。

```
eval_line <- function(ex, coef) {
  ex %>%
    make_functions(alist(coef=)) %>%
    purrr::invoke_map(coef = coef) %>% unlist()
}
```

`invoke_map`函数类似于`purrr`包中的各种`map`函数，但它不是将一个函数应用于多个值，而是接受一系列函数，并将每个函数应用于同一个值。

现在，我们可以将这些函数通过管道连接起来，以获取直线上的点。

```
make_line_expressions(intercept = 0) %>% eval_line(coef = 1)
## [1] 1 2 3 4
make_line_expressions(intercept = 0) %>% eval_line(coef = 2)
## [1] 2 4 6 8
make_line_expressions(intercept = 1) %>% eval_line(coef = 1)
## [1] 2 3 4 5
```

这里一切都按预期工作，但如果使用引号（即裸表达式，而非`quo`/`expr`）会怎样呢？编写对应的函数很简单。

```
add_expression <- function(ex, expr) {
  ex$ex <- cons(substitute(expr), ex$ex)
  ex
}
make_functions <- function(ex, args) {
  results <- vector("list", length = lst_length(ex$ex))
  i <- 1; lst <- ex$ex
  while (!is.null(lst)) {
    results[[i]] <- rlang::new_function(args, lst$car, rlang::caller_env())
    i <- i + 1
    lst <- lst$cdr
  }
  rev(results)
}
```

然而，如果我们尝试像之前那样使用它们，会得到一个错误。

```
make_line_expressions(intercept = 0) %>% eval_line(coef = 1)
## Error in (function (coef) : object 'intercept' not found
```

一旦我们考虑哪些环境中包含`intercept`的信息，原因就很明显了。这个变量存在于`make_line_expressions`调用作用域内，但当我们创建函数时，是从`eval_line`内部调用`make_functions`的。这些函数创建时，其闭包是`eval_line`的局部环境，而`intercept`并不在那里。

通常，在非标准求值中使用`quosure`比使用裸表达式更安全，这正是因为它们携带着定义时的环境，缓解了我们在跟踪求值环境时遇到的问题。

在进入下一个主题之前，需要提到`quos`函数。这个函数的工作方式类似于`quo`，但它接收一系列参数，并以`quosure`列表的形式返回。

```
rlang::quos(x, y, x+y)
## [[1]]
## 
##   expr: ^x
##   env:  global
##
## [[2]]
## 
##   expr: ^y
##   env:  global
##
## [[3]]
## 
##   expr: ^x + y
##   env:  global
```

`quos`的主要用途是将三点参数（`...`）转换为一个`quosure`列表。

```
f <- function(...) rlang::quos(...)
f(x, y, z)
## [[1]]
## 
##   expr: ^x
##   env:  global
##
## [[2]]
## 
##   expr: ^y
##   env:  global
##
## [[3]]
## 
##   expr: ^z
##   env:  global
```

## 准引用

本章的最后一个主题涉及准引用。这是一种机制，我们可以处理引用的表达式，同时用子表达式的求值结果替换表达式的某些部分。当直接调用执行非标准求值的函数时，我们通常可以按需提供表达式，但一旦我们在程序中使用这些非标准求值函数（例如从其他函数中调用它们），我们就需要一定的灵活性来构建表达式。我们可以通过修改`call`对象的元编程方式来实现，但更好的方法是使用`rlang`包实现的准引用机制。

考虑这个简单的例子。我们有一个数据框，想要过滤掉某个指定列包含缺失数据的行。我们可以使用`dplyr`的`filter`函数实现：

```
df <- tibble::tibble(x = c(1, NA, 3, 4, 5, NA, 7), 
                     y = c(1, 2, 3, NA, 5, 6, NA))
df %>% dplyr::filter(!is.na(x))
## # A tibble: 5 x 2
##       x     y
##    <dbl> <dbl>
## 1     1     1
## 2     3     3
## 3     4    NA
## 4     5     5
## 5     7    NA
df %>% dplyr::filter(!is.na(y))
## # A tibble: 5 x 2
##       x     y
##    <dbl> <dbl>
## 1     1     1
## 2    NA     2
## 3     3     3
## 4     5     5
## 5    NA     6
```

这里，我们对两个不同的变量使用了相同的代码。这段代码很简单，所以为了演示，我们不会写一个函数来避免重复，但对于更复杂的管道，我们通常会这样做。因此，我们尝试将这个管道封装成一个函数。

```
filter_on_na <- function(df, column) {
  column <- substitute(column)
  df %>% dplyr::filter(!is.na(column))
}
df %>% filter_on_na(x)
## Warning in is.na(column): is.na() applied to non-
## (list or vector) of type 'symbol'
## # A tibble: 7 x 2
##       x     y
##    <dbl> <dbl>
## 1     1     1
## 2    NA     2
## 3     3     3
## 4     4    NA
## 5     5     5
## 6    NA     6
## 7     7    NA
```

我们使用`substitute`将列名转换为符号，然后应用`filter`管道。当然，它不起作用，原因是`filter`也执行非标准求值，并将谓词`!is.na(column)`直接当作表达式处理。所以，它需要知道变量`column`。现在，由于`filter`将其参数当作`quosure`求值，它可以找到变量`column`，但它发现这是一个符号——在本例中，它是符号`x`——但这并不是我们想要它看到的内容。我们想让`filter`看到列`x`，但这并不是`filter`的工作方式。

我们想要做的是将变量`column`中持有的符号替换到`filter`所看到的表达式（`quosure`）中。我们可以通过“砰-砰”（bang-bang）操作符`!!`来实现。

```
filter_on_na <- function(df, column) {
  column <- rlang::enexpr(column)
  df %>% dplyr::filter(!is.na(!!column))
}
df %>% filter_on_na(x)
## # A tibble: 5 x 2
##       x     y
##    <dbl> <dbl>
## 1     1     1
## 2     3     3
## 3     4    NA
## 4     5     5
## 5     7    NA
df %>% filter_on_na(y)
## # A tibble: 5 x 2
##       x     y
##    <dbl> <dbl>
## 1     1     1
## 2    NA     2
## 3     3     3
## 4     5     5
## 5    NA     6
```

这个操作符对其后面的表达式进行“反引用”（unquote），求值并将结果插入到`filter`看到的引用表达式中。所以，是将变量`column`的值（而不是符号`column`）插入到`!is.na(!!column)`中。

你可能已经注意到，我使用了另一个函数`rlang::enexpr`来创建引用的列名。在`rlang`包中，有两个函数的行为类似于`quote`和`substitute`，它们创建裸表达式，但允许使用`!!`操作符进行准引用，这是`substitute`和`quote`所不具备的。考虑如下定义的函数`f`和`g`：

```
f <- function(x) substitute(x)
g <- function(x) rlang::enexpr(x)
```

当直接调用时，两者都会返回作为参数`x`传入的表达式，但考虑从另一个函数`h`中调用它们的情况，我们希望在`h`中将其参数值替换为`f`或`g`的参数`x`。



```r
h <- function(func, var) func(!!var)
h(f, quote(x))
## !(!var)
h(g, quote(x))
## x
```

在`f`中，当我们使用`substitute`时，我们得到表达式`!!var`返回，但在函数`g`中，当我们使用`enexpr`时，替换完成并得到了期望的结果`x`。

函数`enexpr`的工作方式类似于`enquo`，将函数参数转换为表达式，但它会将它们转换为裸表达式而不是 quosure。作为`quo`的类比——它直接接收表达式并创建 quosure——我们有`expr`，它创建一个裸表达式（类似`quote`），但允许准引用。

```r
x 
##   expr: ² * x + 1
##   env:  global
```

使用 bang-bang 操作符时需要格外小心，具体取决于您使用的`rlang`包版本。它由取反操作符`!`构建而成，其优先级较低。唯一优先级更低的操作符是逻辑操作符和赋值操作符。这意味着，如果您试图在表达式`x + y`中对`x`进行反引用，那么如果您简单地写成`!!x + y`，实际上可能是对整个表达式进行反引用。只有当表达式中使用的操作符是逻辑操作符时，`!!`才会绑定到`x`。

如果您尝试评估这个表达式，您将看到您使用的是旧版本还是新版本的包。如果您使用的是旧版本，由于`!`的优先级低于`+`，第一个表达式将评估`x + y`；如果您使用的是较新版本的`rlang`，那么只有`x`的值会被替换到表达式中。

```r
x <- y <- 2
rlang::expr(!!x + y)
## 2 + y
```

该包的最新版本（撰写本文时）已解决了此问题；它不依赖 R 的优先级规则，而是会在评估未引用表达式之前检查表达式，并赋予 bang-bang 操作符比取反操作符更紧密的优先级。然而，如果您没有最新版本的包，您可以使用括号或使用反引用的函数变体`UQ`来绕过此问题。

```r
rlang::expr(UQ(x) + y)
## 2 + y
```

当我们将函数参数转换为表达式（使用`enexpr`）或 quosure（使用`enquo`）时，bang-bang 操作符或`UQ`函数会将表达式的结果替换到我们创建的表达式/quosure 中，这就是第二个`filter_on_na`函数起作用的原因。

您也可以在函数调用的命名参数左侧进行反引用，但 R 解析器不允许编写如下代码：

```r
f(UQ(var) = expr)
```

这是因为函数调用参数左侧必须是符号。为解决此问题，`rlang`包提供了`:=`操作符的实现，它允许此类赋值。考虑以下示例：

```r
f % mutate(UQ(summary_name) := UQ(summary_expr))
}
tibble(x = 1:4, y = 1:4) %>% f(z, x + y)
## # A tibble: 4 x 3
##       x     y     z
##     
## 1     1     1     2
## 2     2     2     4
## 3     3     3     6
## 4     4     4     8
```

以下命名函数调用：

```r
df %>% mutate(UQ(summary_name) := UQ(summary_expr))
```

在准引用替换完成后，其行为类似于带有命名参数的常规函数调用。

您必须了解的最后一个准引用函数是`UQS`。该函数的行为类似于`UQ`，它会评估其参数并将结果放入引用表达式中，但其目的是将一组 quosure 拼接（splice）到函数调用中。

考虑这个示例：

```r
args <- rlang::quos(x = 1, y = 2)
q <- rlang::expr(f(rlang::UQS(args)))
q
## f(x = ~1, y = ~2)
```

在这里，我们为参数`x`和`y`创建了一组 quosure，并创建了一个表达式来调用带有这些参数的函数`f`。`UQS`函数的作用就是将参数拼接到函数调用的表达式中。如果使用`UQ`，我们会得到不同的表达式。

```r
q <- rlang::expr(f(rlang::UQ(args)))
q
## f(list(x = ~1, y = ~2))
```

与`UQ`类似，`UQS`也有一个操作符版本——三重感叹号操作符：`!!!`。

```r
q <- rlang::expr(f(!!!args))
q
## f(x = ~1, y = ~2)
```

为了观察`UQS`的实际应用，我们再考虑一个玩具示例。我们编写一个函数，用于在给定数据框的情况下计算表达式的均值。假设我们希望遍历多个数据框并计算同一个表达式的均值（或类似操作）。为此，我们构建一个函数，该函数将我们捕获为 quosure 的表达式进行评估，并计算该表达式在数据框中的均值，同时使用额外的参数来修改对`mean`的调用。

```r
mean_expr <- function(ex, ...) {
ex <- rlang::enquo(ex)
extra_args <- rlang::dots_list(...)
mean_call <- rlang::expr(with(
data,
mean(!!rlang::get_expr(ex), !!!extra_args))
)
rlang::new_function(args = alist(data=),
body = mean_call,
env = rlang::get_env(ex))
}
mean_sum <- mean_expr(x + y, na.rm = TRUE)
mean_sum
## function (data)
## with(data, mean(x + y, na.rm = TRUE))
```

这里有几个值得注意的新特性。我们使用`dots_list`来评估`...`参数中的参数。我们也可以通过其他方式处理，但我们的目的既不是让它们被延迟评估，又是要获取一个列表作为对`mean`调用的额外参数。我们从`ex`参数创建了一个表达式。这里，我们将`ex`中的裸表达式包装在一个涉及`with`函数的表达式中，以包含数据框形式的数据。我们使用`!!!`操作符将额外的参数拼接到对`mean`的调用中。然后，我们构建一个新函数，它接受一个参数`data`；在其函数体中评估`with(mean(...))`表达式；并且评估作用域为`ex`定义时的环境。

我们可以使用之前创建的数据框来测试它：

```r
df
## # A tibble: 7 x 2
##       x     y
##    
## 1    1\.    1.
## 2   NA     2.
## 3    3\.    3.
## 4    4\.   NA
## 5    5\.    5.
## 6   NA     6.
## 7    7\.   NA
mean_sum(df)
## [1] 6
```

为了验证我们评估表达式的环境正是它被定义的环境，我们可以编写一个函数在局部作用域中捕获一个参数，并观察在调用均值函数时该作用域仍然可用。

```r
f 
g(df)
## [1] 9
```

使用 quosures 和准引号会增加您在非标准评估中跟踪环境时可能遇到的问题。我强烈建议您使用这些方法，而不是使用原始的 R 函数`quote`和`substitute`。但是，它们并不能解决何时引用参数以及何时调用将参数转换为表达式的函数这一决策问题。如果您从一个已经引用了参数的函数中调用一个使用`enquo`的函数，那么您将得到双重引用的结果。

```r
f 
##   expr: ^x + y
##   env:  global
g(x + y)
## 
##   expr: ^rlang::enquo(expr)
##   env:  0x7f9b016be9c8
rlang::eval_tidy(f(x + y), list(x = 1, y = 2))
## [1] 3
rlang::eval_tidy(g(x + y), list(x = 1, y = 2))
## 
##   expr: ^x + y
##   env:  global
```

这里的解决方案与之前相同：如果您需要使用已经引用的表达式来调用函数，请创建两个版本——一个期望其参数被引用，另一个则为您完成引用。



