# 10. 连续时间马尔可夫链

现在，我们来看一个特定领域语言的例子，它将整洁求值与 `magrittr` 管道运算符结合在一起。我们将编写一种语言，用于指定连续时间马尔可夫链（CTMC），并根据链在不同时间点所处状态的轨迹，计算此类 CTMC 中参数的似然。与上一章中的列表推导示例一样，我们不会使用运算符为语言创建新的语法，而是通过提供可串联起来构建“句子”的函数来创建一种领域特定语言（DSL）。

我们将使用 `magrittr` 和 `rlang` 包来构建语言，使用 `tibble` 包处理数据框，并使用 `expm` 包进行矩阵指数运算。

```r
library(magrittr)
library(rlang)
library(tibble)
library(expm)
```

我们将重用链表代码，以及前面章节中实现的 `collect_symbols_rec` 和 `make_args_list` 函数。

```r
cons <- function(car, cdr) list(car = car, cdr = cdr)
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
collect_symbols_rec <- function(expr, lst, bound) {
if (is.symbol(expr) && expr != "") {
if (as.character(expr) %in% bound) lst
else cons(as.character(expr), lst)
} else if (is.pairlist(expr)) {
for (i in seq_along(expr)) {
lst <- collect_symbols_rec(expr[[i]], lst, bound)
}
lst
} else if (is.call(expr)) {
if (expr[[1]] == as.symbol("function"))
bound <- c(names(expr[[2]]), bound)
for (i in 1:length(expr)) {
lst <- collect_symbols_rec(expr[[i]], lst, bound)
}
lst
} else {
lst
}
}
make_args_list <- function(args) {
res <- replicate(length(args), substitute())
names(res) <- args
as.pairlist(res)
}
```

我们将使用这些函数，通过提取与转移速率相关的表达式中未绑定的符号，从 CTMC 规范中构造函数。我们不会使用之前实现的 `collect_symbols` 函数来收集未绑定的变量，而是使用一个期望其表达式已被引用的版本。

```r
collect_symbols_q <- function(expr, env) {
expr %>%
    collect_symbols_rec(lst = NULL, bound = names(env)) %>%
    lst_to_list() %>%
    unique() %>%
    purrr::discard(exists, env) %>%
    unlist()
}
```

这是因为我们计划在领域特定语言（DSL）函数中引用表达式，然后使用这些被引用的表达式来调用此函数。

## 构造马尔可夫链

在第 3 章中，我们探讨了几种为连续时间马尔可夫链（CTMC）设计语言的方法。在本章中，我们将使用一种变体，该变体将管道运算符 `%>%` 与 `add_edge` 函数结合使用。我们将边收集在三个列表中：一个用于“起始”状态，一个用于“目标”状态，一个用于与转移相关的速率。此外，当我们创建新边时，我们会在速率表达式中收集未绑定的变量，这样后续作用域的变化就不会影响 CTMC 模型的参数。为了表示 CTMC，我们创建一个类和一个包含“起始”状态、“目标”状态、速率和参数列表的列表。

```r
ctmc <- function()
structure(list(from = NULL,
rate = NULL,
to = NULL,
params = NULL),
class = "ctmc")
```

我们希望构造 CTMC 的语法如下所示：

```r
m <- ctmc() %>%
    add_edge(foo, a, bar) %>%
    add_edge(foo, 2*a, baz) %>%
    add_edge(foo, 4, qux) %>%
    add_edge(bar, b, baz) %>%
    add_edge(baz, a + x*b, qux) %>%
    add_edge(qux, a + UQ(x)*b, foo)
```

因此，我们需要实现 `add_edge`，使其接受四个参数：CTMC、“起始”状态、转移速率和“目标”状态。当使用管道运算符时，CTMC 会被隐式传递给函数调用。其他三个参数应作为表达式提供，而 `add_edge` 函数将实现非标准求值来处理它们。

我们希望“起始”和“目标”状态是单个符号，但我们会将它们转换为字符串，以用作 CTMC 速率矩阵的行名和列名。与转移关联的速率应该是一个表达式，为了正确获取表达式的作用域，我们将其转换为一个 quosure（带环境的引用表达）。然后，我们将提取此表达式中的未绑定变量——指在定义该 quosure 的环境中未绑定的变量——并将它们添加到模型的参数中。实现如下所示：

```r
add_edge <- function(ctmc, from, rate, to) {
    from <- enexpr(from); stopifnot(is_symbol(from))
    to <- enexpr(to); stopifnot(is_symbol(to))
    from <- as_string(from)
    to <- as_string(to)
    ctmc$from <- cons(from, ctmc$from)
    ctmc$to <- cons(to, ctmc$to)
    r <- enquo(rate)
    ctmc$rate <- cons(r, ctmc$rate)
    ctmc$params <- cons(collect_symbols_q(get_expr(r), get_env(r)),
                        ctmc$params)
    ctmc
}
```

我们对 `from` 和 `to` 使用 `enexpr`，因为我们希望这些符号就只是符号，而不是我们打算在任何上下文中求值的东西。另一方面，我们对 `rate` 参数使用 `enquo`，因为在求值表达式时我们确实需要有它的环境可用。不过，我们目前还不对其求值。在知道模型参数之前，我们无法对其求值，而我们又不希望这些参数在 CTMC 对象内部被固定下来。然而，在提取速率表达式中的未绑定变量时，我们会使用速率环境。

通常，能够通过打印来获取有关我们构建对象的一些信息是一个好主意，但 `ctmc` 对象的默认打印函数会显示列表的列表。这种表示形式，尤其是对于链表，可能难以解读。相反，我们可以通过定义一个名为 `print.ctmc` 的函数来为该类实现打印函数。我们希望显示的信息是模型的参数和边结构，我们可以像这样实现此函数：

```r
print.ctmc <- function(x, ...) {
    from <- x$from %>% rev()
    to <- x$to %>% rev()
    rate <- x$rate %>% rev()
    parameters <- x$params %>%
        unlist() %>% unique() %>% rev()
    cat("CTMC:\n")
    cat("parameters:", paste(parameters), "\n")
    cat("transitions:\n")
    for (i in seq_along(from)) {
        cat(from[[i]], "->", to[[i]],
            "\t[", deparse(get_expr(rate[[i]])), "]\n")
    }
    cat("\n")
}
```

实现很简单。我们将链表转换为列表，以便在遍历边时更容易处理，并将它们反转，以便按添加到模型的顺序显示。对于链表，我们会在前面添加新边，因此它们的表示顺序与添加顺序相反。对于参数，我们也使用 `unique` 去除重复。之后，我们只需将参数打印为列表，并为每条边打印一行，显示“从”和“到”状态以及边上的速率表达式。对于后者，我们使用 `get_expr` 获取裸表达式（而不是 quosure），并使用函数 `deparse` 将表达式转换为可打印的字符串。

使用目前已定义的三个函数，我们现在可以创建并打印一个连续时间马尔可夫链。

```
x <- 0
m <- ctmc(
  foo ~ bar, foo ~ baz, foo ~ qux,
  bar ~ baz, baz ~ qux, qux ~ foo,
  rates = list(a, 2*a, 4, b, a + x*b, a + UQ(x)*b)
)
m
```

此示例表明，我们可以在边上使用常量表达式，例如从 `foo` 到 `qux` 的边，其速率为 4。我们可以使用包含未绑定变量 `a`、`b` 和 `2*a` 的表达式。我们还可以使用包含绑定变量的表达式（最后两条边）。请注意，倒数第二条边（从 `baz` 到 `qux`）的速率表达式包含（未求值的）变量 `x`，而最后一条边（从 `qux` 到 `foo`）包含表达式 `a + 2*b`，其中已插入了 `x` 的值。这就是在表达式中包含绑定变量与对其取消引用的区别。由于 `x` 是一个绑定变量，它不被视为模型的参数，但在倒数第二个表达式中，它在求值速率时会被使用。如果更改其值，我们也会更改速率表达式的值。对于最后一个速率表达式，我们已经插入了 `x` 的值，因此在这里更改 `x` 的值不会改变表达式。

## 构建速率矩阵

我们在第 3 章中看到了如何将边列表转换为速率矩阵，但在本章中，我们希望更进一步。在第 3 章中，边上的速率是数值；现在我们有表达式。我们不会将 `ctmc` 转换为速率矩阵，而是创建一个用于生成速率矩阵的函数——一个给定马尔可夫模型参数值就能提供相应速率矩阵的函数。

我们通过闭包来实现此功能。我们编写一个函数，从 `ctmc` 对象中提取构建速率矩阵所需的信息，然后定义一个用于根据模型参数计算速率矩阵的函数。然后它返回这个闭包函数。实现如下所示：

```
get_rate_matrix_function <- function(model) {
  edges <- get_edges(model)
  from <- edges$from %>% rev()
  to <- edges$to %>% rev()
  rate <- edges$rate %>% rev()
  nodes <- c(from, to) %>% unique() %>% unlist()
  parameters <- edges$parameters %>%
    unlist() %>% unique() %>% rev()
  n <- length(nodes)
  f <- function() {
    args <- as_list(environment())
    Q <- matrix(0, nrow = n, ncol = n)
    rownames(Q) <- colnames(Q) <- nodes
    for (i in seq_along(from)) {
      Q[from[[i]], to[[i]]] <- eval_tidy(rate[[i]], args)
    }
    diag(Q) <- -rowSums(Q)
    Q
  }
  formals(f) <- make_args_list(parameters)
  f
}
```

同样，我们将链表转换为 `list` 对象并反转它们。然后，通过组合 `from` 和 `to` 列表、去除重复项，我们获得模型中唯一节点的列表，并将结果列表转换为向量，稍后用于设置速率矩阵的行名和列名。我们通过将链表转换为 `list` 来提取模型的参数，然后将其转换为向量，去除重复项，并反转结果，以获取按添加到边的顺序排列的参数。

我们定义的闭包最初不接收形式参数。我们在定义函数之后根据 CTMC 参数来设置这些参数。我们这样做仅仅是因为与构建表达式并使用类似之前使用的 `new_function` 构造相比，这是定义函数的更简单方法。在返回闭包之前，它会有一个形式参数列表。由于我们不知道这些参数是什么，我们使用一个技巧来在闭包内部获取它们：我们在定义任何局部变量之前获取本地环境——此时它只包含传递给函数调用的参数——并将它们组成一个列表。稍后我们可以使用这个列表来覆盖闭包内部速率表达式的求值作用域。

对于速率矩阵的实际构建，没有什么出乎意料的。我们根据 CTMC 中的状态数获取矩阵的大小。然后创建矩阵，并根据它们表示的节点命名行和列。然后我们（整洁地）求值所有速率表达式以填充矩阵的单元格，最后，调整对角线以使所有行的和为零。

现在我们语言中有了一个用于获取速率矩阵函数的命令。

```
Qf <- m %>% get_rate_matrix_function()
Qf
```

当我们向此函数提供模型参数时，我们得到速率矩阵。

```
Qf(a = 2, b = 4)
```

请记住，从 `baz` 到 `qux` 的边包含一个引用全局变量 `x` 的表达式。如果我们更改此变量的值，我们也会更改求值 `Qf` 函数的结果。

```
x <- 1
Qf(a = 2, b = 4)
```

从 `qux` 到 `foo` 的边（我们在创建边时使用 `UQ` 替换了 `x` 的值）不会改变。

### 轨迹

连续时间马尔可夫链的观测结果称为轨迹——即一系列状态以及观测到这些状态的时间点。在实际数据分析中，我们通常会编写从文件获取数据的函数，但既然我们在探索领域特定语言，那就先编写一个用于定义轨迹的函数。我们将让轨迹依赖于我们想使用的 CTMC，这样就能检验轨迹中的状态是否也是 CTMC 中的状态。如果你想用多个 CTMC 分析同一条轨迹，可以移除这些检验，或者让轨迹对象依赖于一个合法状态列表，而非 `ctmc` 对象。

我们采用与 `ctmc` 类相同的做法：编写一个创建轨迹对象的函数，再编写用于向轨迹添加信息的函数。我们要在轨迹中存储的信息包括状态列表以及观测到这些状态的时间点列表。为了进行 CTMC 与轨迹之间的一致性检验，我们还会存储 CTMC 中的节点。轨迹类的构造函数如下所示：

```
ctmc_trace <- function(ctmc) {
    nodes <- ctmc[["nodes"]] %>% unique %>% unlist
    structure(list(nodes = nodes, states = NULL, at = NULL),
              class = "ctmc_trace")
}
```

我们向该语言添加一个动词，即一个用于添加特定时间点状态观测值的函数。该函数主要检验状态与 `ctmc` 对象的一致性，然后将状态和时间点添加到 `ctmc_trace` 对象的列表中。

```
add_observation <- function(trace, state, at) {
    stopifnot(state %in% trace$nodes)
    stopifnot(at > trace$at$car)
    trace$states <- cons(state, trace$states)
    trace$at <- cons(at, trace$at)
    trace
}
```

与 CTMC 对象类似，我们也需要为轨迹编写打印函数。这里我将采用与 `ctmc` 打印函数不同的方法。我会将轨迹转换为数据框——准确来说是 `tibble` 对象——然后打印结果。如果我要编写一个 CTMC 包，可能会采用不同方法，但我稍后会利用这种数据框转换来计算似然值，因此我也在打印函数中利用这一转换。要将 `ctmc_trace` 对象转换为 `tibble` 对象，我们需要专门化 `as_tibble` 函数。之后，再专门化 `print` 函数。

```
as_tibble.ctmc_trace <- function(x, ...) {
    states <- x[["states"]] %>% lst_to_list() %>% unlist() %>% rev()
    at     <- x[["at"]]     %>% lst_to_list() %>% unlist() %>% rev()
    tibble(state = states, at = at)
}
print.ctmc_trace <- function(x, ...) {
    df <- as_tibble(x)
    cat("CTMC trace:\n")
    print(df)
}
```

现在我们已经具备了创建和打印轨迹的功能。

```
tr <- ctmc_trace(ctmc) %>%
    add_observation(foo, at = 0.0) %>%
    add_observation(bar, at = 0.1) %>%
    add_observation(baz, at = 0.3) %>%
    add_observation(qux, at = 0.5) %>%
    add_observation(foo, at = 0.7) %>%
    add_observation(baz, at = 1.1)
tr
## CTMC trace:
## # A tibble: 6 x 2
##   state    at
##   <chr> <dbl>
## 1 foo   0.
## 2 bar   0.100
## 3 baz   0.300
## 4 qux   0.500
## 5 foo   0.700
## 6 baz   1.10
```

### 计算似然值

我们将为本示例实现的最后一个功能是：给定 CTMC 和轨迹，计算参数的似然值。在第 3 章中，我们看到了如何将速率矩阵转换为转移概率矩阵——首先将速率矩阵乘以一个标量（即两次观测之间经过的时间段），然后对结果进行（矩阵）指数运算。我们将重用在那里实现的函数。

```
transition_probabilities <- function(Q, t) expm(Q * t)
```

为了计算似然值，我们将在领域特定语言中创建一个动词，用于将 CTMC 和轨迹转换为一个函数。该函数将 CTMC 的参数作为参数（与之前编写的速率矩阵创建函数类似），然后返回这些参数的似然值。我们可以将这个函数与优化算法结合使用进行最大似然估计——R 的各种包中提供了多种优化算法。

实现方法很直接。我们从`ctmc`对象中获取速率矩阵函数，将轨迹转换为数据框，并将结果存储在函数的闭包中。然后使用与之前相同的技巧获取闭包内的参数，评估速率矩阵函数以获得速率矩阵，并将数据框中的数据转换为计算所需的格式。计算过程就是遍历轨迹，计算连续两次观测之间的转移概率。由于这是一个马尔可夫模型，联合概率就是这些概率的乘积。创建闭包后，我们设置其形式参数，就像我们对速率矩阵函数所做的那样。

```
get_likelihood_function <- function(ctmc, trace) {
    rate_func <- ctmc %>% get_rate_matrix_function()
    trace_df  <- as_tibble(trace)
    lhd_function <- function() {
        args <- as_list(environment())
        Q <- do.call(rate_func, args)
        n <- length(trace_df$state)
        from <- trace_df$state[-n]
        to <- trace_df$state[-1]
        delta_t <- trace_df$at[-1] - trace_df$at[-n]
        lhd <- 1
        for (i in seq_along(from)) {
            P <- transition_probabilities(Q, delta_t[i])
            lhd <- lhd * P[from[i], to[i]]
        }
        lhd
    }
    formals(lhd_function) <- formals(rate_func)
    lhd_function
}
```

这就完成了；我们现在可以计算 CTMC 的似然值了。

```
lhd <- get_likelihood_function(ctmc, tr)
lhd(a = 2, b = 4)
## [1] 0.00120108
```

在实际数据分析场景中，我们可能更想计算对数似然值。对于任何有意义的轨迹长度，实际的似然值都会导致下溢，因为我们处理的是有限位数的浮点数。将似然函数修改为对数似然函数很简单，只需将乘积改为求和，并对`P[from[i], to[i]]`取对数即可。

你可能还想为这样的语言添加更多功能，但即使只实现到目前为止的几个函数，我们也拥有了一个有用的领域特定语言。我们没有使用任何运算符重载来实现它；我们没有必要这样做。不过，我们广泛使用了整洁评估（tidy evaluation）来实现速率表达式中的非标准评估。
```