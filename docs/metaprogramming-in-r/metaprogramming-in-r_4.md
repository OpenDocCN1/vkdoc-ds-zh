# 4. 操作表达式

使用 `quote()` 函数创建的表达式共有四种类型：`原始值`、`名称`、`函数调用或控制结构`，以及 `点对列表`。函数调用包含算术或逻辑运算符等运算符，因为在 R 中这些也是函数调用，而控制结构可以被视为一种特殊的函数调用——它们与函数调用的真正区别仅在于调用时使用的语法。

```r
class(quote(1))
## [1] "numeric"
class(quote("foo"))
## [1] "character"
class(quote(TRUE))
## [1] "logical"
class(quote(x))
## [1] "name"
class(quote(f(x)))
## [1] "call"
class(quote(2+2))
## [1] "call"
class(quote(if (TRUE) "foo" else "bar"))
## [1] "if"
class(quote(for (x in 1:3) x))
## [1] "for"
```

其中，调用和控制结构显然更有趣；值和符号则非常简单，可操作空间不大。点对列表用于处理函数参数，因此除非你在处理函数参数，否则不会在表达式中看到它们。另一方面，调用和控制结构捕捉了表达式中的操作；你可以将它们视为列表，从而可以检查并修改它们。我相信，以这种方式处理表达式是最简单的方法，也是本章的主题。另一种互补的方法是为变量替换值，这将是下一章的主题。

在下一节你了解了表达式的基础知识后，本章剩余部分将介绍一些现实世界中可能用到的元编程示例。你可以在本书可下载源代码的 `dfdr` 包中找到这些示例的完整版本，该代码可通过 [www.apress.com/9781484228807](http://www.apress.com/9781484228807) 获取。

### 表达式基础

函数调用和控制结构都可以作为列表来操作。在这两者中，我将主要关注调用，因为根据我的经验，它们在程序中更常被修改。那么，我们先来处理控制结构。我只会描述 `if` 和 `for`；其余的控制结构类似。

#### 访问和操作控制结构

包含控制结构的语句与 R 中的任何其他表达式一样，你可以使用 `quote()` 创建它们的未求值版本。正如我之前解释的，你可以将这个表达式对象视为一个列表。因此，你可以获取对象的长度，并访问对象中的元素。对于单个 `if` 语句，你会得到一个长度为 3 的表达式；而对于 `if-else` 语句，你会得到一个长度为 4 的表达式。第一个元素是名称 `if`。对于所有控制结构和函数调用，第一个元素始终是函数名，因此如果你将控制结构视为语法略为奇特的函数，就完全不必将它们视为特殊情况。第二个元素是 `if` 语句中的测试条件，之后是语句的主体部分。如果是 `if-else` 语句，第四个元素就是表达式的 `else` 部分。

```r
x <- quote(if (foo) bar)
length(x)
## [1] 3
x[[1]]
## `if`
x[[2]]
## foo
x[[3]]
## bar
y <- quote(if (foo) bar else baz)
length(y)
## [1] 4
y[[1]]
## `if`
y[[2]]
## foo
y[[3]]
## bar
y[[4]]
## baz
```

对于 `for` 循环，你会得到一个长度为 4 的表达式，其中第一个元素当然是名称 `for`。第二个是迭代变量，第三个是你迭代的表达式，第四个是循环体。

```r
z <- quote(for (x in 1:4) print(x))
length(z)
## [1] 4
z[[1]]
## `for`
z[[2]]
## x
z[[3]]
## 1:4
z[[4]]
## print(x)
```

你可以像求值任何其他表达式一样求值这些控制结构。

```r
eval(z)
## [1] 1
## [1] 2
## [1] 3
## [1] 4
```

你可以通过为其组件赋值来修改它们，从而在求值前改变其行为。下面是一个改变循环对象的示例：

```r
z[[3]] <- 1:2
eval(z)
## [1] 1
## [1] 2
```

下面是一个改变函数体内操作的示例：

```r
z[[4]] <- quote(print(x + 2))
eval(z)
## [1] 3
## [1] 4
```

下面是一个改变索引变量和循环体的示例：

```r
z[[2]] <- quote(y)
z[[4]] <- quote(print(y))
eval(z)
## [1] 1
## [1] 2
```

#### 访问和操作函数调用

对于函数调用，其类是 `call`，当你将它们视为列表时，第一个元素是被调用函数的名称，其余元素是函数调用的参数。

```r
x <- quote(f(x,y,z))
class(x)
## [1] "call"
length(x)
## [1] 4
x[[1]]
## f
x[[2]]
## x
x[[3]]
## y
x[[4]]
## z
```

你可以使用 `is.call()` 函数测试一个表达式是否是函数调用。

```r
is.call(quote(x))
## [1] FALSE
is.call(quote(f(x)))
## [1] TRUE
```

在这里，你无法访问任何函数体、环境或类似的东西。这仅仅是一个函数名；直到你求值表达式时，你才需要将这个名称与实际的函数关联起来。当然，你始终可以使用 `eval()` 来求值函数调用，并且如果需要改变求值方式，你可以修改表达式。

```r
x <- quote(sin(2))
eval(x)
## [1] 0.9092974
x[[1]] <- quote(cos)
eval(x)
## [1] -0.4161468
x[[2]] <- 0
eval(x)
## [1] 1
```

请注意，最后一条赋值语句中我没有引用零（0）；这并非必须，因为数值已经是表达式，不需要被引用。

要探查一个表达式，通常需要一个递归函数。递归中的两种基本情况是 `is.atomic()`（用于值）和 `is.name()`（用于符号），递归情况包括 `is.call()`（用于函数调用）以及如果你想处理点对列表则用 `is.pairlist()`。在下面的函数中，它只是打印出表达式的结构（此函数未处理表达式是点对列表的情况）：

```r
f <- function(expr, indent = "") {
  if (is.atomic(expr)) {
    print(paste0(indent, expr))
  } else if (is.name(expr)) {
    print(paste0(indent, deparse(expr)))
  } else if (is.call(expr)) {
    print(paste0(indent, deparse(expr[[1]])))
    n <- length(expr)
    if (n > 1) {
      new_indent <- paste0(indent, "  ")
      for (i in 2:n) {
        f(expr[[i]], new_indent)
      }
    }
  } else {
    print(paste0(indent, "未预料到的表达式: ", expr[[1]]))
  }
}
f(quote(2 + 3*(x + y)))
## [1] "+"
## [1] "  2"
## [1] "  *"
## [1] "    3"
## [1] "    ("
## [1] "      +"
## [1] "        x"
## [1] "        y"
```

你可能会觉得这里的输出有点奇怪，但它捕捉到了表达式 `2+3*(x+y)` 的结构。最外层的函数调用是 `+` 函数，它有两个参数：`2` 和对 `*` 的调用。对 `*` 的调用也有两个参数——理所当然——其中一个是 `3`，另一个是对函数 `(` 的调用。如果你觉得这很奇怪，那么欢迎加入我们，但在 R 中圆括号就是函数。对 `(` 的调用只有一个参数，这个参数恰好是对 `+` 的函数调用，其参数是 `x` 和 `y`。

以上就是直接操作函数调用的全部内容，当然，这些简单的工具可以做很多事情。接下来的几节将展示如何使用它们来实现强大的效果。

### 表达式简化

为了直观展示表达式的处理过程，我们考虑一个简化表达式的场景。假设你想要计算那些可以立即求值的子表达式（因为它们仅由不依赖变量的原子值组成），并希望化简乘以 1 或加 0 的操作。大致如下：

```r
simplify_expr(quote(2*(0 + ((4 + 5)*x)*1)))
## 2 * (9 * x)
```

这并非完美；如果真正简化表达式，你会发现可以重新排列括号并将 2 乘以 9，但此处你仅对表达式进行局部化简，并不尝试重写它们。

# 表达式简化函数

由于你处理的是表达式，需要一个递归函数来处理基本案例（原子值和名称）以及递归案例（调用和成对列表）。你预料到表达式中不会出现成对列表，因此如果遇到除原子、名称或调用对象之外的内容，就直接放弃。如果遇到任何基本案例，直接返回该值——你无法进一步简化它们。对于调用对象，你会调用一个名为`simplify_call`的函数，该函数负责处理调用。

## simplify_expr 函数
```r
simplify_expr <- function(expr) {
if (is.atomic(expr) || is.name(expr)) {
expr
} else if (is.call(expr)) {
simplify_call(expr)
} else {
stop(paste0("Unexpected expression ",
deparse(expr),
" in simplifying"))
}
}
```

## simplify_call 函数
对于调用的简化，我并不会尝试简化函数调用。我不知道任何泛型函数的具体行为，因此几乎无法简化涉及函数的表达式。不过，我假设在简化表达式时，其中的函数表现为具有按值调用语义，并且会简化它们的参数。这是一个假设，可能是错误的，但针对本练习，你可以这样假定。因此，对于一般函数调用，我只需简化其参数。对于算术表达式，我会尝试进一步简化。我也可以对其他操作进行类似处理，但仅处理算术运算符足以展示处理运算符的细节，我相信亲爱的读者，如果需要，你能够自行处理其他运算符。

调用处理可以写成这样：
```r
simplify_call <- function(expr) {
if (expr[[1]] == as.name("+"))
return(simplify_addition(expr[[2]], expr[[3]]))
if (expr[[1]] == as.name("-")) {
if (length(expr) == 2)
return(simplify_unary_subtraction(expr[[2]]))
else
return(simplify_subtraction(expr[[2]], expr[[3]]))
}
if (expr[[1]] == as.name("*"))
return(simplify_multiplication(expr[[2]], expr[[3]]))
if (expr[[1]] == as.name("/"))
return(simplify_division(expr[[2]], expr[[3]]))
if (expr[[1]] == as.name("^"))
return(simplify_exponentiation(expr[[2]], expr[[3]]))
if (expr[[1]] == as.name("(")) {
subexpr <- simplify_expr(expr[[2]])
if (is.atomic(subexpr) || is.name(subexpr))
return(subexpr)
else if (is.call(subexpr) && subexpr[[1]] == as.name("("))
return(subexpr)
else
return(call("(", subexpr))
}
simplify_function_call(expr)
}
```

这段代码大多不言自明，但仍有几点需要说明：首先，你需要用名称对象来比较调用名。它们实际上并非字符串，而是`name`类型；因此需要使用`as.name`。其次，减号有两种形式：二元减法和一元取反。你可以通过检查调用有 1 个还是 2 个参数来区分它们（即长度是 2 还是 3；记住第一个元素是调用名），并用两个不同的函数来处理这两种情况。第三，括号也是调用，因此你需要处理它们。你只需获取括号内的表达式。如果该内容不需要括号（单个值、名称或已被括号包围的表达式），则直接返回该子表达式。否则，在其周围加上括号。最后，如果不知如何处理，就将该表达式视为函数调用。

### 加法简化
现在，你依次处理每个运算符。它们处理方式类似，区别仅在于各运算符可简化的情况不同。对于加法，你可以消除加 0 的情况；如果两个参数都是数字，则可直接求值。否则，需要返回一个带有已简化操作数的`+`调用。
```r
simplify_addition <- function(f, g) {
left <- simplify_expr(f)
right <- simplify_expr(g)
if (left == 0) return(right)
if (right == 0) return(left)
if (is.numeric(left) && is.numeric(right))
return(left + right)
call("+", left, right)
}
```

### 一元减法简化
如果一元减法的参数是数字，你可以直接求值。否则，可以消除参数中已有的负号（因为两个负号得正）。如果其他方法都失败，则只能返回带有负号的简化表达式。
```r
simplify_unary_subtraction <- function(f) {
simplified <- simplify_expr(f)
if (is.numeric(simplified))
-simplified
else if (is.call(simplified) && simplified[[1]] == "-")
simplified[[2]]
else
bquote(-.(simplified))
}
```
最后这种情况，你将使用`bquote`函数。它的功能类似于`quote`，但可以在`.(...)`处替换值。因此，你本质上是在写`quote(-simplified)`，只不过将简化后的表达式插入到了表达式中。

### 二元减法简化
二元减法与加法类似，但当从 0 中减去时，需要多做一点工作。这里，你需要再次使用`bquote`：
```r
simplify_subtraction <- function(f, g) {
left <- simplify_expr(f)
right <- simplify_expr(g)
if (left == 0) {
if (is.numeric(right))
return(-right)
else
return(bquote(-.(right)))
}
if (right == 0)
return(left)
if (is.numeric(left) && is.numeric(right))
return(left - right)
call("-", left, right)
}
```

### 乘法简化
对于乘法，你可以简化涉及 0 或 1 的情况，但除此之外，该函数与之前所见类似。
```r
simplify_multiplication <- function(f, g) {
left <- simplify_expr(f)
right <- simplify_expr(g)
if (left == 0 || right == 0)
return(0)
if (left == 1)
return(right)
if (right == 1)
return(left)
if (is.numeric(left) && is.numeric(right))
return(left * right)
call("*", left, right)
}
```

### 除法与乘方简化
除法和乘方只是类似的更多处理，但需要处理不同的情况。
```r
simplify_division <- function(f, g) {
left <- simplify_expr(f)
right <- simplify_expr(g)
if (right == 1)
return(left)
if (is.numeric(left) && is.numeric(right))
return(left / right)
call("/", left, right)
}
simplify_exponentiation <- function(f, g) {
left <- simplify_expr(f)
right <- simplify_expr(g)
if (right == 0) return(1)
if (left == 0) return(0)
if (left == 1) return(1)
if (right == 1) return(left)
if (is.numeric(left) && is.numeric(right))
return(left ^ right)
call("^", left, right)
}
```

你需要的最后一个函数是函数调用的简化。这里，你只需在返回调用之前简化函数的所有参数。你可以将参数收集到一个列表中，并用如下表达式创建函数调用：

`do.call("call", c(list(function_name), arguments))`

这会以列表形式接受参数，并在对`call`的调用中将其转换为参数。如果`function_name`的值是函数名称，这将正常工作，但像`f(x,y)(z)`这样的表达式也是函数调用；此处函数`name`是`f(x,y)`，参数是`z`。无法将这样的表达式包装在对`call`的调用中，但可以使用`as.call`直接获取一个列表并将其转换为调用。

```
simplify_function_call <- function(expr) {
  function_name <- expr[[1]]
  arguments <- vector("list", length(expr) - 1)
  for (i in seq_along(arguments)) {
    arguments[i] <- list(simplify_expr(expr[[i + 1]]))
  }
  as.call(c(list(function_name), arguments))
}
```

出于同样的原因，必须修补`simplify_call`函数。在此函数中，将`expr[[1]]`与名称进行比较以分派到各种算术运算符。这仅在`expr[[1]]`是名称时才有效，因此必须确保仅在其为名称时才进行这些比较。

```
simplify_call <- function(expr) {
  if (is.name(expr[[1]])) {
    ### Dispatch to operators...
  }
  simplify_function_call(expr)
}
```

你也可以尝试更激进的做法，尝试在函数的所有参数都是值且已知函数功能时对其求值——或者至少合理预期你能够知道。你总是可以检查是否能在相关环境中找到该名称，以及它是否是一个函数，但由于你在简化表达式时不期望知道非函数的变量，要求所有函数符号都已知可能要求过高。尽管如此，你可以说像`sin`、`cos`、`exp`、`log`这样的函数是它们通常的自身，然后执行如下操作：

```
simplify_function_call <- function(expr) {
  function_name <- expr[[1]]
  arguments <- vector("list", length(expr) - 1)
  for (i in seq_along(arguments)) {
    arguments[i] <- list(simplify_expr(expr[[i + 1]]))
  }
  if (all(unlist(Map(is.numeric, arguments)))) {
    if (as.character(function_name) %in%
        c("sin", "cos", "exp", "log")) {
      result <- do.call(as.character(function_name), arguments)
      return(result)
    }
  }
  as.call(c(list(function_name), arguments))
}
```

现在你有了一个能在一定程度上简化表达式的简单程序。

```
simplify_expr(quote(2*(0 + ((4 + 5)*x)*1)))
## 2 * (9 * x)
```

两种函数调用解决方案都无法处理命名参数。你只需处理位置参数，并丢弃名称信息。

```
f <- function(x, y) x
expr1 <- quote(f(x = 2, y = 1))
expr2 <- quote(f(y = 2, x = 1))
eval(expr1)
## [1] 2
eval(expr2)
## [1] 1
simplify_expr(expr1)
## f(2, 1)
simplify_expr(expr2)
## f(2, 1)
eval(simplify_expr(expr1))
## [1] 2
eval(simplify_expr(expr2))
## [1] 2
```

不过，修补这个问题并不困难。在处理函数调用时，使用命名参数无需特殊操作；只需通过`names`函数访问它们即可。

```
names(expr1)
## [1] ""  "x" "y"
names(expr2)
## [1] ""  "y" "x"
```

如果确保简化结果与原始表达式具有相同的名称，那么就没问题了。

```
simplify_function_call <- function(expr) {
  function_name <- expr[[1]]
  arguments <- vector("list", length(expr) - 1)
  for (i in seq_along(arguments)) {
    arguments[i] <- list(simplify_expr(expr[[i + 1]]))
  }
  result <- as.call(c(list(function_name), arguments))
  names(result) <- names(expr)
  result
}
simplify_expr(expr1)
## f(x = 2, y = 1)
simplify_expr(expr2)
## f(y = 2, x = 1)
eval(simplify_expr(expr1))
## [1] 2
eval(simplify_expr(expr2))
## [1] 1
```

## 自动微分

作为第二个仅略复杂的示例，我们考虑自动微分，即自动将计算表达式的函数转换为计算其导数的函数。我假设你有一个函数，其函数体只包含一个表达式——不涉及控制结构或语句序列，仅是一个算术表达式——然后递归遍历此表达式，应用微分规则。尽管你在此元编程中所做的比刚刚实现的表达式简化更复杂，但你会发现程序的形式是相似的。

我们从主函数开始，将其命名为`d`表示微分。它接受两个参数：待微分的函数和求导的变量。如果你希望函数能够处理内置数学函数，则需要将这些函数作为特例处理。这些函数以所谓的原始（primitive）函数形式实现，没有函数体。你需要在`d`函数中显式处理它们。对于所有其他函数，只需计算函数体中表达式的导数。如果你想返回一个新的导数函数，可以修改原函数并替换其函数体。由于 R 不允许修改函数参数，这将创建一个副本供返回，同时保留原函数不变。以这种方式重用参数可确保新函数与原函数具有相同的参数、名称和默认值。它还确保导数具有与原函数相同的封闭环境，这在求值时可能很重要。

`d`函数可以如下所示，这里我只处理了三个原始函数——其余函数留作练习：

```
d <- function(f, x) {
  if (is.null(body(f))) {
    if (identical(f, sin)) return(cos)
    if (identical(f, cos)) return(function(x) -sin(x))
    if (identical(f, exp)) return(exp)
    stop("unknown primitive")
  } else {
    df <- f
    e <- environment(f)
    body(df) <- simplify_expr(diff_expr(body(f), x, e))
    df
  }
}
```

你将函数环境随递归一起传递，因为稍后处理函数调用时需要用到它。届时，你需要查找函数并分析它们接受哪些参数，以便应用链式法则。目前，你只需在递归中将其传递下去。

出于美观原因，你使用上一节编写的代码简化从`f`的函数体微分得到的表达式。你可以像这样使用`d`：

```
f <- function(x) x² + sin(x)
df <- d(f, "x")
df
## function (x)
## 2 * x + cos(x)
```

为了计算函数体的导数，你遵循表达式简化中使用的模式：编写一个用于处理表达式的递归函数，在该函数中，将函数调用分派到不同算术运算的不同情况。

递归函数的两个基本情形是数字和名称。我们假设你不会遇到其他原子值（如逻辑向量）；反正你也不知道如何对它们求导。对于数字，导数始终是 0；而对于名称，则取决于它是否是你对其求导的变量还是其他变量。函数调用的递归情形是，你只需调用另一个函数来处理该情形。

```
diff_expr <- function(expr, x, e) {
  if (is.numeric(expr)) {
    quote(0)
  } else if (is.name(expr)) {
    if (expr == x) quote(1)
    else quote(0)
  } else if (is.call(expr)) {
    diff_call(expr, x, e)
  } else {
    stop(paste0("Unexpected expression ",
                deparse(expr), " in parsing."))
  }
}
```

### 函数调用与表达式求导

#### 调用分发策略

对于调用，你需要根据调用类型进行分发。因此，通过为每个运算符定义函数来处理算术表达式，而处理括号的方式与你之前在表达式简化和不同函数调用求导时的方式类似。

你同样需要将原始函数（primitive functions）和用户定义函数作为两个独立的案例来处理。对于用户定义函数，你可以对其进行分析，找出其形式参数，并应用链式法则。对于原始函数，`formals`会返回一个空列表，因此该策略不适用于它们。所以，你需要将它们作为特例来处理。

这里假设你有一个包含原始函数名称的列表。例如，如果你只需要处理以下三种情况，可以这样定义：

```r
.built_in_functions <- c("sin", "cos", "exp")
```

根据需要扩展该列表。

处理函数调用的函数如下所示：

```r
diff_call <- function(expr, x, e) {
if (is.name(expr[[1]])) {
if (expr[[1]] == as.name("+"))
return(diff_addition(expr[[2]], expr[[3]], x, e))
if (expr[[1]] == as.name("-")) {
if (length(expr) == 2)
return(call("-", diff_expr(expr[[2]], x, e)))
else
return(diff_subtraction(expr[[2]], expr[[3]], x, e))
}
if (expr[[1]] == as.name("*"))
return(diff_multiplication(expr[[2]], expr[[3]], x, e))
if (expr[[1]] == as.name("/"))
return(diff_division(expr[[2]], expr[[3]], x, e))
if (expr[[1]] == as.name("^"))
return(diff_exponentiation(expr[[2]], expr[[3]], x, e))
if (expr[[1]] == as.name("(")) {
subexpr <- diff_expr(expr[[2]], x, e)
if (is.atomic(subexpr) || is.name(subexpr))
return(subexpr)
else if (is.call(subexpr) && subexpr[[1]] == as.name("("))
return(subexpr)
else
return(call("(", subexpr))
}
}
if (is.name(expr[[1]]) &&
as.character(expr[[1]]) %in% .built_in_functions)
return(diff_built_in_function_call(expr, x, e))
else
return(diff_general_function_call(expr, x, e))
}
```

# 算术运算求导

处理算术运算时，只需遵循你在微积分课上学到的规则。

```r
diff_addition <- function(f, g, x, e) {
call("+", diff_expr(f, x, e), diff_expr(g, x, e))
}
diff_subtraction <- function(f, g, x, e) {
call("-", diff_expr(f, x, e), diff_expr(g, x, e))
}
diff_multiplication <- function(f, g, x, e) {
# f' g + f g'
call("+",
call("*", diff_expr(f, x, e), g),
call("*", f, diff_expr(g, x, e)))
}
diff_division <- function(f, g, x, e) {
# (f' g − f g' )/g**2
call("/",
call("-",
call("*", diff_expr(f, x, e), g),
call("*", f, diff_expr(g, x, e))),
call("^", g, 2))
}
diff_exponentiation <- function(f, g, x, e) {
#### 使用链式法则进行通用处理。
dydf <- call("*", g,
call("^", f, substitute(n - 1, list(n = g))))
dfdx <- diff_expr(f, x, e)
call("*", dydf, dfdx)
}
```

## 函数调用与链式法则

对于函数调用，你需要应用链式法则。对于原始函数，你无法获取形式参数列表，因此无法通过检查函数本身来处理它们；你必须使用它们的名称来确定其参数和导数。下面展示了一些案例，但我会将其他函数的处理留作练习：

```r
diff_built_in_function_call <- function(expr, x, e) {
# chain rule with a known function to differentiate...
if (expr[[1]] == as.name("sin"))
return(call("*", call("cos", expr[[2]]),
diff_expr(expr[[2]], x, e)))
if (expr[[1]] == as.name("cos"))
return(call("*", call("-", call("sin", expr[[2]])),
diff_expr(expr[[2]], x, e)))
if (expr[[1]] == as.name("exp"))
return(call("*", call("exp", expr[[2]]),
diff_expr(expr[[2]], x, e)))
}
```

对于其他函数调用，你可以通过检查该函数来找出它包含哪些变量，并对这些变量应用链式法则。这种方法仅在你能确定所引用的是哪个函数时有效，因此无法处理需要计算函数值的情况。在这些情况下，你只能放弃。然而，如果你拥有函数的符号名称，你可以查找并检查它。但这对于一般用途来说并不完全安全。如果你计算了一个函数的导数，然后更改了它所引用的全局函数，那么该导数将使用旧的全局函数，而实际函数则使用新的全局函数。对此你几乎无能为力。在应用链式法则时，你需要知道函数接受哪些参数。这意味着你需要知道你正在处理的是哪个函数。

你可以假设函数调用中使用的参数是应用链式法则时需要考虑的相关参数。那些未在函数调用中传递的参数将具有默认值，并且不会依赖于传递给导数函数的参数，因此你可以忽略它们。因此，你可以获取函数调用中的参数，并对它们求和以应用链式法则。你需要知道这些参数的名称来计算函数的导数，并且需要处理位置参数和命名参数，这正是你需要查找实际函数的地方。

在递归过程中传递的环境——即你要计算其导数的原始函数的环境——中，你查找需要应用链式法则的函数。有了该函数，你可以使用`match.call`函数来获取函数调用中所有参数的名称。`match.call`函数负责合并命名参数和位置参数。对于每个参数，你通过将函数更改为其对相应变量的导数来构建一个函数调用。你使用`bquote`函数调用`d`来计算这些导数。然后，你将函数调用与原始变量的参数导数相乘。将这些项求和就完成了链式法则。

```r
diff_general_function_call <- function(expr, x, e) {
function_name <- expr[[1]]
if (!is.name(function_name))
stop(paste0("Unexpected call ", deparse(expr)))
func <- get(as.character(function_name), e)
full_call <- match.call(func, expr)
variables <- names(full_call)
arguments <- vector("list", length(full_call) - 1)
for (i in seq_along(arguments)) {
var <- variables[i + 1]
dfdz <- full_call
dfdz[[1]] <- bquote(d(.(function_name), .(var)))
dzdx <- diff_expr(expr[[i + 1]], x, e)
arguments[[i]] <- bquote(.(dfdz) * .(dzdx))
}
as.call(c(list(sum), arguments))
}
```

## 注意事项与局限

这个解决方案有一个注意事项：即使原始函数是向量化的，其导数也不会是向量化的。例如，如果你定义以下函数，那么`g`和`h`应该是相同的函数：

```r
f <- function(x, y) x² * y
g <- function(z) f(2*z, z²)
h <- function(z) 4*z⁴
```

然而，如果你计算`d(g,"z")`和`d(h,"z")`，并用一个向量值调用它们，前者会将所有结果相加，而后者会返回一个向量值。`d(g,"z")`导数中的`sum`调用会吞掉所有值。你可以通过在`d(g,"z")`上调用`Vectorize`来解决这个问题。

除此之外，你现在拥有一个元程序，可以将函数转换为其导数。它并不能处理所有可能的函数；函数必须能够计算简单表达式。链式法则只能应用于通过名称引用的已知函数，并且你只处理了部分原始函数，但我相信你可以看出如何在现有基础上构建更多功能。

## 脚注

1 在 R 语言中可以修改数据的程度上。当然，你是在使用替换运算符创建新对象。

2 它们基本上只是调用（call）的特殊情况。`is.call`函数会为它们返回`TRUE`，并且处理它们的方式没有区别。唯一的区别在于编写控制结构表达式与函数调用的语法上。

