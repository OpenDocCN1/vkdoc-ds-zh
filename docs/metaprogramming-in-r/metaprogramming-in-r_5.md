# 5. 使用替换处理表达式

你可以将表达式当作字符串进行处理和操作，也可以通过将变量替换为表达式来修改它们。此外，你还可以使用更高级的引用来构建表达式，并在字符串与表达式之间进行转换。

## 关于引用的更多内容

本章首先快速回顾引用机制。你已经了解了如何引用表达式，但除了创建一字不差的表达式之外，你还能做更多事情。我之前详细讨论过 `quote` 函数，但只是顺带提过 `bquote` 函数。`bquote` 函数允许你创建仅部分引用的表达式——你可以在创建表达式时求值某些部分，而将其他部分留待以后处理。`quote` 会将整个表达式包裹在引号中，而 `bquote` 函数则会对表达式进行部分替换。调用 `bquote` 的方式与调用 `quote` 类似，都传入一个表达式，但任何包裹在“点”函数调用（即写为 `.(...)`）中的子表达式都不会被引用，而是会被求值，并将求值结果放入你正在构建的表达式中。如果你使用 `bquote` 时没有使用 `.(...)`，它的行为就和 `quote` 一样。

```r
quote(x + y)
## x + y
bquote(x + y)
## x + y
bquote(.(2+2) + y)
## 4 + y
```

你在微分程序中构建链式法则的项时就用到了 `bquote`。这里你使用了 `bquote(.(dfdz) * .(dzdx))` 来构建微分函数与微分自变量的乘积；而 `dfdz` 和 `dzdx` 的构建方式分别是：对于 `dfdz`，再次使用 `bquote` 构建一个微分函数（如 `bquote(d(.(function_name), .(var)))`），对于 `dzdx`，则调用 `diff_expr` 函数。

在许多需要创建调用对象的场景中，使用 `bquote` 比显式构建调用对象要简单得多。对于前面展示的简单示例，两者差别不大。

```r
bquote(.(2+2) + y)
## 4 + y
call("+", 2+2, quote(y))
## 4 + y
```

然而，一旦你需要创建包含多个嵌套调用的表达式（而实际上你并不需要特别复杂的表达式就能产生多个嵌套调用），显式创建调用就会变得比使用 `bquote` 繁琐得多。

```r
bquote((.(2+2) + x) * y)
## (4 + x) * y
call("*", call("(", call("+", 2+2, quote(x))), quote(y))
## (4 + x) * y
```

## 解析与反解析

当你使用引用来构建表达式时，你会得到可以通过递归函数进行操作的对象；但你也可以将表达式作为字符串来处理，并使用 `parse` 和 `deparse` 函数在字符串与表达式之间进行转换。

`deparse` 函数将一个表达式转换为用于创建该表达式的 R 源代码（表示为字符串形式），而 `parse` 函数则解析一个字符串或文件，并返回其中包含的表达式。

```r
deparse(quote(x + y))
## [1] "x + y"
parse(text = "x + y")
## expression(x + y)
```

在这里调用 `parse` 时，你需要指定正在解析一个文本字符串。否则，`parse` 会假定你给的是一个文件名，并尝试解析该文件的内容。严格来说，`parse` 调用的结果并不直接是一个表达式，然而它输出的类型是 `expression`。对于这个类型来说，这其实是一个不太恰当的选择，因为 `expression` 对象实际上是一个表达式列表。`parse` 函数可以解析多个表达式，这些表达式的序列都以 `expression` 类型表示。你可以通过对该对象进行索引来获取实际的表达式。

```r
(expr <- parse(text = "x + y; z * x"))
## expression(x + y, z * x)
expr[[1]]
## x + y
expr[[2]]
## z * x
```

`deparse` 函数通常在你需要一个表示 R 对象的字符串时使用，例如，用于绘制图形时的标签。许多函数会提取参数中给出的表达式，并将其用作默认标签。在这些情况下，你不能直接使用 `deparse`。那会在将表达式转换为字符串之前就先对其求值。你需要获取实际的参数，为此，你需要使用 `substitute` 函数。

```r
f <- function(x) deparse(x)
g <- function(x) deparse(substitute(x))
x <- 1:4; y <- x**2
f(x + y)
## [1] "c(2, 6, 12, 20)"
g(x + y)
## [1] "x + y"
```

# 替换

`substitute`函数用于在表达式中将变量替换为值。你刚才看到的`deparse(substitute(x))`结构正是利用了这一点：它使用`substitute`获取函数参数`x`所引用的表达式，然后将其转换为字符串。如果直接引用`x`，会强制求值参数并得到求值结果；而使用`substitute`则可以得到`x`所引用的表达式。

获取用作函数参数的表达式（而非表达式的值）是`substitute`的常见用途。与`deparse`配合使用时，它用于创建图形标签。它也用于所谓的非标准求值（nonstandard evaluation）——即不遵循环境默认规则对参数进行求值的函数。非标准求值将在下一节中介绍，它使用`substitute`获取参数中的表达式，然后在不同于函数求值环境的环境中使用`eval`对表达式进行求值。

在考虑求值表达式之前，你应先了解`substitute`的工作原理。这在一定程度上取决于它被调用的位置。在全局环境中，`substitute`不会做任何事，至少在你没有给它提供多于表达式本身参数的情况下（稍后会说明）。在所有其他环境中，如果你仅使用一个表达式调用`substitute`，该函数会在表达式中搜索变量。如果发现某个变量在当前环境中有值（无论是函数调用的承诺（promise）还是你已赋值的变量），它会将该变量替换为值。如果变量在环境中没有值，则保持不变。在全局环境中，它会让所有变量保持不变。

在下面的示例中，你会看到即使在全局环境中定义了变量`x`和`y`，`substitute(x + y)`也没有被修改。但在函数`f`的环境中，这两个变量会被替换为它们的值。

```
x <- 2; y <- 3
substitute(x + y)
## x + y
f <- function(x, y) substitute(x + y)
f(2, 3)
## 2 + 3
```

使用`substitute`时，变量的查找方式与`eval`不同。当`substitute`在环境中查找时，它不会沿着父环境指针向上查找。如果在调用它的确切环境中未找到要替换的变量，它就不会进一步查找。因此，如果你编写如下函数：

```
y - 3
## [1] 0
f <- function(x) substitute(x + y)
f(2)
## 2 + y
g <- function(x) function(y) substitute(x + y)
h <- g(2)
h(3)
## x + 3
```

当调用函数`f`时，其求值环境中有`x`，父环境（即全局环境）中有`y`，但`substitute`只会替换局部变量`x`。对于`h`，它会知道`y`是局部变量，而`x`来自其闭包；然而，只有局部变量`y`会被替换。

`substitute`实际用于查找变量的环境作为其第二个参数给出。默认值为当前的求值环境。你可以通过提供一个环境或一个包含变量到值映射的列表来更改此参数。

```
e <- new.env(parent = emptyenv())
e$x <- 2
e$y <- 3
substitute(x + y, e)
## 2 + 3
substitute(x + y, list(x = 2, y = 3))
## 2 + 3
```

同样，`substitute`不会沿着父环境指针查找。无论这些父环境是隐式设置还是显式设置在传递给函数的环境中，都是如此。

```
x <- 2 ; y <- 3
e <- new.env(parent = globalenv())
substitute(x + y, e)
## x + y
e <- new.env(parent = globalenv())
e$x <- 2
e$y <- 3
e2 <- new.env(parent = e)
substitute(x + y, e2)
## x + y
```

如果你希望某个变量被替换，需要确保该变量存在于你提供给`substitute`的确切环境中。

## 替换存储在变量中的表达式

当你操作表达式时，一个常见情况是持有对表达式的引用（例如来自函数参数），并希望修改它。在全局环境中，你不能直接用`substitute`做到这一点。如果你给`substitute`一个变量，它只会返回该变量。

```
expr <- quote(x + y)
substitute(expr)
## expr
```

这是因为`substitute`不会替换全局环境中的变量。你可以通过显式地将表达式放在环境或列表中提供给`substitute`来使表达式被替换。

```
substitute(expr, list(expr = expr))
## x + y
```

不过，通常你并不是在全局环境中操作表达式，在函数内部你可以替换表达式。

```
f <- function() {
expr <- quote(x + y)
substitute(expr)
}
f()
## x + y
```

但如果你想要在这里替换表达式中的`y`为 2 呢？无论使用显式列表的全局环境还是在函数内部，替换都会将`expr`替换为`quote(x + y)`，但你还想进一步将`y`替换为 2。你无法直接从局部环境获取`y`并显式地将其交给`substitute`；这样也行不通。

```
f <- function() {
expr <- quote(x + y)
y <- 2
substitute(expr)
}
f()
## x + y
f <- function() {
expr <- quote(x + y)
substitute(expr, list(y = 2))
}
f()
## expr
```

你想要做的是，首先将`expr`替换为表达式`quote(x + y)`，然后将`y`替换为 2。因此，自然的方法是编写以下代码，但这行不通：

```
substitute(substitute(expr, list(expr = expr)), list(y = 2))
## substitute(expr, list(expr = expr))
```

这里的问题是，`y`没有出现在最外层`substitute`所给的表达式中任何位置，因此它不会被替换进去。你得到的只是以下表达式：

```
substitute(expr, list(expr = expr))
```

你可以计算这个表达式得到`x + y`。

```
eval(substitute(substitute(expr, list(expr = expr)), list(y = 2)))
```

然而，求值首先会将`y`替换到最内层的`substitute`表达式中（那里没有`y`变量），然后才会将`expr`替换到表达式`expr`中。顺序是错误的。

要替换存储在其他变量中的表达式中的变量，必须以与直觉相反的顺序编写表达式。你不想先在最内层替换`expr`，再在外层替换`y`；你应该先将`expr`替换到一个能够处理`y`替换的`substitute`表达式中。最外层将`expr`替换到`substitute(expr, list(y = 2))`中，然后你可以对该表达式求值，以得到将`y`替换到原表达式的结果。

因此，你需要这样创建待求值的表达式：

```
substitute(substitute(expr, list(y = 2)), list(expr = expr))
## substitute(x + y, list(y = 2))
```

这样完成替换：

```
eval(substitute(substitute(expr, list(y = 2)), list(expr = expr)))
## x + 2
```

这可能需要一些时间来适应，但你只需记住，你需要按照这个顺序进行替换。

## 替换函数参数

函数参数作为未求值的 promise 传递，但一旦你访问它们，它们就会被求值。如果你希望在不求值的情况下获取这些 promise，可以使用 `substitute` 函数。它会将参数作为未求值（或称引述）表达式返回。

当你想要操作表达式或以不同于常规的方式求值时（你将在下一节中探索），这非常有用，但这样做会丢失关于表达式应在哪个上下文中求值的信息。考虑以下示例：

```
f <- function(x) function(y = x) substitute(y)
g <- f(2)
g()
## x
x <- 4
g(x)
## x
```

在第一次调用 `g` 时，`y` 使用其默认参数，即从闭包中继承的参数，因此它会替换为值为 2 的 `x`。然而在第二次调用中，你传入的是全局环境中的表达式 `x`，此时 `x` 的值为 4。在这两种情况下，你得到的都只是表达式 `quote(x)`。从 R 语言内部来看，没有机制能从 promise 中提取环境，因此你无法编写这样的代码：修改输入表达式，然后在默认参数的封闭作用域和函数参数的调用作用域中对其求值。

# 非标准求值

在其它函数内部调用 `substitute` 时也需要格外小心。当你使用 `substitute` 时得到的表达式，正是函数被调用时所使用的确切表达式。这个表达式不会在其它函数之间传播。在下面的示例中，你用表达式 `x + y` 调用了函数 `g`，但由于 `g` 用 `expr` 调用了 `f`，因此替换时得到的就是 `expr`。

```r
f <- function(expr) substitute(expr)
f(x + y)
## x + y
g <- function(expr) f(expr)
g(x + y)
## expr
x <- 2; y <- 3
eval(f(x + y))
## [1] 5
eval(g(x + y))
## [1] 5
```

`substitute` 函数比使用 `bquote` 和显式修改 `call` 对象更难安全、正确地使用，但它正是实现非标准求值所必需的函数。

## 非标准求值

非标准求值指的是任何不遵循在本地求值环境中对表达式求值规则的求值方式。当你使用 `eval` 在默认环境（即通过调用 `environment()` 获得的环境）之外的其它环境中对函数参数求值时，你就是在以非标准方式对表达式求值。

非标准求值的典型用途包括在调用作用域中对表达式求值（你已经看过相关示例），以及在数据框中对表达式求值。你已经知道在使用 `substitute` 时可以用列表来提供变量到值的映射，在使用 `eval` 时同样可以这样做。

```r
eval(quote(x + y), list(x = 2, y = 3))
## [1] 5
```

由于数据框本质上是一个由相同长度的向量元素组成的列表，你同样可以在数据框的上下文中对表达式求值。

```r
d <- data.frame(x = 1:2, y = 3:3)
eval(quote(x + y), d)
## [1] 4 5
```

当你以这种方式使用 `eval` 时（即显式引述表达式），其实并未真正进行非标准求值。被引述的表达式不会以任何其它标准方式进行求值。毕竟，你显式地引述了它，而如果你不在这里引述它，那么 `x+y` 将在调用作用域内求值，而不是在数据框内部。

```r
x <- 2; y <-  3
eval(x + y, d)
## [1] 4 5
```

要进行非标准求值，你必须显式替换参数，从而避免在调用作用域中对参数 `promise` 求值，然后在替代作用域中对其求值。例如，你可以像这样实现你自己的 `with` 函数版本：

```r
my_with <- function(df, expr) {
eval(substitute(expr), df)
}
d <- data.frame(x = rnorm(5), y = rnorm(5))
my_with(d, x + y)
## [1] -1.4437635  0.9121930 -1.8908632 -0.2156309
## [5]  1.8495491
```

在这里，表达式 `x + y` 在函数调用中并未被引述，因此通常情况下你会预期 `x + y` 在调用作用域内求值。但由于你在 `my_with` 中显式使用了 `substitute` 来替换参数，这种情况并没有发生。相反，你是在数据框的上下文中对表达式求值。这就是非标准求值。

真正的 `with` 函数工作得比你的版本更好一些。如果你求值的表达式包含数据框中未找到的变量，它会从调用作用域中获取这些变量。你的版本也能处理数据框中不存在的变量，但工作方式略有不同。

如果你在全局作用域中使用这两个函数，看不出差别。

```r
z <- 1
with(d, x + y + z)
## [1] -0.4437635  1.9121930 -0.8908632  0.7843691
## [5]  2.8495491
my_with(d, x + y + z)
## [1] -0.4437635  1.9121930 -0.8908632  0.7843691
## [5]  2.8495491
```

然而，如果你在函数内部使用它们，差别就显现出来了。

```r
f <- function(z) with(d, x + y + z)
f(2)
## [1] 0.5562365 2.9121930 0.1091368 1.7843691
## [5] 3.8495491
g <- function(z) my_with(d, x + y + z)
g(2)
## [1] -0.4437635  1.9121930 -0.8908632  0.7843691
## [5]  2.8495491
```

这是怎么回事？

其实，`eval` 接受第三个参数，用于指定求值时的封闭作用域。在 `my_with` 中你没有提供这个参数，因此使用了默认值，即调用 `eval` 时的封闭作用域，也就是 `my_with` 的求值环境。在这个环境中你没有定义 `z`，但封闭作用域包含全局环境，而你在全局环境中定义了 `z`。当你在 `my_with` 中对表达式求值时，会从全局环境中找到 `z`。相比之下，使用 `with` 时，封闭环境是调用环境。

你可以像这样修改 `my_with` 使其行为一致：

```r
my_with <- function(df, expr) {
eval(substitute(expr), df, parent.frame())
}
f <- function(z) with(d, x + y + z)
f(2)
## [1] 0.5562365 2.9121930 0.1091368 1.7843691
## [5] 3.8495491
g <- function(z) my_with(d, x + y + z)
g(2)
## [1] 0.5562365 2.9121930 0.1091368 1.7843691
## [5] 3.8495491
```

现在，你掌握了非标准求值的两种典型用法：在数据框中求值以及在调用作用域中求值。

### 函数内部的非标准求值

一旦你开始在其他函数内部使用非标准求值，就很难正确掌握它。当你以交互方式使用 `R`，或在全局作用域中编写分析管道时，这是一种简化许多操作语法的便捷方法，但由于替换倾向于对你提供给函数的参数进行逐字处理，因此一旦参数从一个函数传递到另一个函数，NSE 就会变得棘手。

考虑以下示例：

```r
x <- 2; y <- 3
f <- function(d, expr) my_with(d, expr)
f(d, x + y)
## [1] 5
g <- function(d, expr) my_with(d, substitute(expr))
g(d, x + y)
## expr
```

在这里，你尝试在函数内部使用 `my_with` 两次，但都没有达到预期效果。在 `f` 中，`expr` 在全局作用域中被求值。当你在函数内部使用该变量时，`promise` 会在传递到 `my_with` 之前被求值。在 `g` 中，你使用了 `substitute`，但 `my_with` 看到的是 `substitute(expr)`——记住，它不会将表达式视为 `promise`，而是对其进行替换以得到一个表达式——因此你实际上并没有让参数被替换。`my_with` 中的 NSE 阻止了这一点。

如果你想要进行 NSE 的函数，你真的应该编写那些处理表达式并对其执行“正常”求值的函数。你可以制作一个 `my_with` 的版本，它期望表达式已经被引用，这样你可以在其他函数中使用它，然后像这样定义 `my_with` 来执行 NSE：

```r
my_with_q <- function(df, expr) {
eval(expr, df, parent.frame())
}
my_with <- function(df, expr) my_with_q(d, substitute(expr))
g <- function(d, expr) my_with_q(d, substitute(expr))
g(d, x + y)
## [1] -1.4437635  0.9121930 -1.8908632 -0.2156309
## [5]  1.8495491
my_with(d, x + y)
## [1] -1.4437635  0.9121930 -1.8908632 -0.2156309
## [5]  1.8495491
```

### 使用 NSE 编写宏

由于 NSE 允许你在调用作用域中求值表达式，你可以用它来编写宏，即作为它们被调用处语句的快捷方式的函数。这与普通函数不同，普通函数通常不能修改其自身作用域之外的数据。然而，除非你有非常充分的理由，否则我不建议使用宏——数据的不可变性是 `R` 的一个重要特性，使用宏违反这一特性是与它相悖的。但是，由于这是一个很好的例子，说明你可以用 NSE 做什么，我将在这里包含这个示例。

考虑以下代码。此示例是 Thomas Lumley (R Journal, 2001) 在 Programmer’s Niche: Macros in `R` ( [`www.r-project.org/doc/Rnews/Rnews_2001-3.pdf`](https://www.r-project.org/doc/Rnews/Rnews_2001-3.pdf) ) 中展示的宏代码的修改实现。我简化了宏制造函数；你可以在原始文章在线版中看到完整版本。

```
make_param_names <- function(params) {
param_names <- names(params)
if (is.null(param_names))
param_names <- rep("", length(params))
for (i in seq_along(param_names)) {
if (param_names[i] == "") {
param_names[i] <- paste(params[[i]])
}
}
param_names
}
make_macro <- function(..., body) {
params <- eval(substitute(alist(...)))
body <- substitute(body)
#### 构造宏
f <- eval(substitute(
function() eval(substitute(body), parent.frame())
))
#### 设置宏参数
param_names <- make_param_names(params)
names(params) <- param_names
params <- as.list(params)
formals(f) <- params
f
}
```

第一个函数只是从函数参数列表中提取名称，并确保所有参数都有名称。这在稍后构造函数的 `formals` 列表时是必要的。在 `formals` 列表中，所有参数都必须有名称，你可以用这个函数来构造这些名称。第二个函数是那个有趣的函数。这里，你通过构造一个函数来创建宏，该函数的主体将在其调用作用域中被求值。你首先获取宏应该具有的参数和主体。然后，通过替换 `...` 并求值 `alist(...)` 表达式，你将参数放入一个 `list` 中。如果没有替换，你会得到只包含 `...` 的参数列表，但有了替换，你会得到传递给 `make_macro` 函数的参数（除了命名参数 `body`，你通过另一个 `substitute` 调用将其直接转换为传递给 `make_macro` 的表达式）。

你构造的函数是魔法发生的地方。你创建了表达式

```
substitute(function() eval(substitute(body), parent.frame()))
```

其中 `body` 将被替换为你传递给宏的表达式。当你求值这个表达式时，你得到函数

```
function() eval(substitute(), parent.frame())
```

其中 `<body>` 不再是符号 `body`，而是 `body` 表达式。这是一个（目前）不接受任何参数的函数，但在求值结果表达式之前，它会替换 `<body>` 中在其调用作用域（即 `parent.frame()`）中已知的变量。

你可以通过这个小例子看到它的实际效果：

```
(m <- make_macro(x + y))
```

当你调用 `m` 时，它会在其调用作用域中求值 `x + y`，因此你可以在全局作用域中设置变量 `x` 和 `y` 并像这样求值：

```
x <- 2; y <- 4
m()
## [1] 6
```

`make_macro` 代码的最后一部分设置了宏的形式参数。它只是获取你已经指定的参数，确保它们都有名称，然后将它们转换为一个列表并设置 `formals(f)`。之后，`make_macro` 返回构造好的函数。

你可以使用这个函数来创建一个宏，将数据框中某一列的特定值替换为 `NA`，如下所示：

```
set_NA_val <- make_macro(df, var, na_val,
body = df$var[df$var == na_val] <- NA)
```

你构造的宏接受三个参数：数据框 `df`；数据框中的变量（列）`var`；以及对应 `NA` 的值 `na_val`。然后，它的主体是以下表达式，其中的 `df`、`var` 和 `na_val` 被传递给宏的参数替换：

```
df$var[df$var == na_val] <- NA
```

你可以像这样使用它：

```
(d <- data.frame(x = c(1,-9,3,4), y = c(1,2,-9,-9)))
##    x  y
## 1  1  1
## 2 -9  2
## 3  3 -9
## 4  4 -9
set_NA_val(d, x, -9); d
##    x  y
## 1  1  1
## 2 NA  2
## 3  3 -9
## 4  4 -9
set_NA_val(d, y, -9); d
##    x  y
## 1  1  1
## 2 NA  2
## 3  3 NA
## 4  4 NA
```

在这里，你看到构造的 `set_NA_val` 宏在调用作用域中修改了一个数据框。它省去了一些样板代码的编写，但代价是保持了参数值的不可变性。对于大多数 `R` 程序员来说，返回更新值的更传统函数解决方案可能更具可读性。

```
set_NA_val_fun <- function(df, var, na_val) {
df[df[,var] == na_val, var] <- NA
df
}
(d <- data.frame(x = c(1,-9,3,4), y = c(1,2,-9,-9)))
##    x  y
## 1  1  1
## 2 -9  2
## 3  3 -9
## 4  4 -9
(d <- set_NA_val_fun(d, "x", -9))
##    x  y
## 1  1  1
## 2 NA  2
## 3  3 -9
## 4  4 -9
(d <- set_NA_val_fun(d, "y", -9))
##    x  y
## 1  1  1
## 2 NA  2
## 3  3 NA
## 4  4 NA
```

或者，这是 `magrittr` 管道版本：

```
library(magrittr)
d % set_NA_val_fun("x", -9) %>% set_NA_val_fun("y", -9)
d
##    x  y
## 1  1  1
## 2 NA  2
## 3  3 NA
## 4  4 NA
```

### 评估中的环境修改

你之所以能在宏中修改变量，是因为可以修改环境。实际值是不可变的，但在前一个示例中修改数据框时，你实际上是将环境中指向该数据的引用替换为修改后的数据。如果其他变量引用同一个数据框，它们仍会指向原始版本。即使使用宏，你实际上也无法修改数据，但可以修改环境。由于你可以在不同于当前评估环境的环境中评估表达式，因此可以制造出在调用环境中修改数据的效果。

通过显式检查赋值时的评估环境，你可以观察到这一点。如果你显式创建一个环境并在其中评估赋值，就能看到该环境被修改了。

```
e <- list2env(list(x = 2, y = 3))
eval(quote(z <- x + y), e)
as.list(e)
## $z
## [1] 5
##
## $y
## [1] 3
##
## $x
## [1] 2
```

此环境的父环境是全局环境。不要尝试以空环境作为其父环境，否则它将无法识别 `<-` 函数。你可以看到，通过设置变量 `z`，你修改了 `e`。

你也可以在列表（lists）中评估表达式，因此可以尝试相同操作：

```
l <- list(x = 2, y = 3)
eval(quote(z <- x + y), l)
l
## $x
## [1] 2
##
## $y
## [1] 3
```

这里可以看到，列表并未被修改。你只能修改列表中的环境，虽然可以使用 `eval` 在列表中评估赋值，但实际上无法修改列表本身。

### 使用 `pryr` 包访问承诺（Promises）

正如我之前提到的，纯 R 语言中没有机制可以直接访问承诺的内部结构。因此，如果你使用 `substitute` 将函数参数转换为其对应的表达式，就会丢失关于表达式应在哪个环境中评估的信息。不过，你可以使用 `pryr` 包来检查承诺。这个包提供了 `promise_info` 函数，它能告诉你表达式是什么以及它所属的环境。

考虑以下代码：


在R中，你可以使用`pryr`包来查看承诺的内部结构。

```
library(pryr)
f <- function(x, y) function(z = x + y) {
  promise_info(z)
}
g <- f(2, 3)
g()
## $code
## x + y
## 
## $env
## <environment: 0x7f8e4e8a6b60>
## 
## $evaled
## [1] FALSE
## 
## $value
## NULL
x <- 2
y <- 3
g(x + y)
## $code
## x + y
## 
## $env
## <environment: R_GlobalEnv>
## 
## $evaled
## [1] FALSE
## 
## $value
## NULL
```

对于承诺，你可以从 `code` 字段获取其对应的表达式，从 `env` 字段获取其所属的环境，以及从 `evaled` 字段获取它是否已经被评估。如果已被评估，相应的值会放在 `value` 字段中。在两次对 `g` 的不同调用中，可以看到 `code` 字段相同，但环境不同。在第一次调用中，你使用了参数 `z` 的默认值，环境是 `f` 的闭包；在第二次调用中，你使用全局环境中的表达式调用 `g`，承诺的环境也是全局环境。

在以下示例中，你可以看到承诺在被评估之前和之后的区别：

```
g <- function(x, y) function(expr = x + y) {
  pi <- promise_info(expr)
  cat("Before evaluation:\n")
  str(pi)
  pi$value <- eval(expr, pi$env)
  cat("After evaluation:\n")
  str(pi)
}
h <- g(4, 5)
h()
## Before evaluation:
## List of 4
## $ code  : language x + y
## $ env   : <environment: 0x7f8e4e8a6b60>
## $ evaled: logi FALSE
## $ value : NULL
## 
## After evaluation:
## List of 4
## $ code  : language x + y
## $ env   : NULL
## $ evaled: logi TRUE
## $ value : num 9
```

当你评估一个承诺时，代码不会改变，但环境会被移除。你不再需要持有一个不再需要的环境的引用。如果你是唯一持有这个环境的人，那么在不再需要它时，可以释放它以进行垃圾回收。评估承诺的结果被放入 `value` 中，并且 `evaled` 被设置为 `TRUE`。

你可以利用承诺信息来修改环境，并仍然在正确的作用域中评估它。你只需要获取代码，这可以通过以下表达式实现：

```
eval(substitute(
  substitute(code, list(y = quote(2 * y))),
  list(code = pi$code)))
```

或者通过以下表达式：

```
eval(substitute(
  substitute(expr, list(y = quote(2 * y)))))
```

其中 `expr` 是持有承诺的参数，`pi` 是调用 `promise_info(expr)` 的结果。这两种方式都不特别优雅，并且你必须记住由内而外地构建表达式，但这就是你能够替换持有表达式的变量中的表达式并进行修改的方法。在这两者中，传统的方法——即第二种——可能最简单。

在两种情况下，你都在创建以下表达式：

```
substitute(<expr>, list(y = quote(2 * y)))
```

其中 `<expr>` 指的是承诺中的表达式，然后你评估这个表达式，将 `y` 替换为 `quote(2 * y)`。

相比之下，一旦你修改了表达式，评估它就几乎微不足道了。你只需使用 `eval(expr, pi$env)` 即可。

```
f <- function(x, y) function(expr = x + y) {
  pi <- promise_info(expr)
  expr <- eval(substitute(
    substitute(expr, list(y = quote(2 * y)))))
  value <- eval(expr, pi$env)
  list(expr = expr, value = value)
}
g <- f(2, 2)
g()
## $expr
## x + 2 * y
##
## $value
## [1] 6
x <- y <- 4
g(x + y)
## $expr
## x + 2 * y
##
## $value
## [1] 12
z <- 4
g(z)
## $expr
## z
##
## $value
## [1] 4
```

在替换过程中，当你创建 `expr` 时，重要的是你将 `y` 替换为 `quote(2 * y)`，而不是简单的 `2 * y`。如果你选择了后者，那么 `y` 会以标准方式被评估，并会引用封闭作用域中的 `y`，也就是传递给创建 `g` 的 `f` 调用的那个参数。当然，这可能正是你想要的：将输入表达式中的任何 `y` 替换为封闭作用域中的 `y`。在这种情况下，代码看起来会像这样：

```
f <- function(x, y) function(expr = x + y) {
  pi <- promise_info(expr)
  expr <- eval(substitute(
    substitute(expr, list(y = 2 * y))))
  value <- eval(expr, pi$env)
  list(expr = expr, value = value)
}
g <- f(2, 2)
g()
## $expr
## x + 4
##
## $value
## [1] 6
x <- y <- 4
g(x + y)
## $expr
## x + 4
##
## $value
## [1] 8
z <- 4
g(z)
## $expr
## z
##
## $value
## [1] 4
```

在两种情况下，你都修改了承诺中的表达式——只是修改方式不同——然后在承诺作用域中对其进行评估。在第一种情况中，如果 `y` 出现在修改后的表达式中，它会从承诺作用域中获取；在第二种情况中，`y` 被替换为你在封闭作用域中拥有的值。

在 `pryr` 包中（本章末尾会再次回到这个包），有一些用 C 语言编写的函数，它们确实提供了对承诺内部结构的访问。使用 `pryr`，你可以在需要时获取承诺的表达式及其关联环境。

**脚注** 1


# 后记

至此，你已经了解了从 `R` 程序内部操作 `R` 语言实际构造的各种技术。操作实际语言，进行元编程，为你提供了以多种方式扩展 `R` 的工具。你可以编写用于修改其他函数的函数——就像你计算函数导数时所做的那样——或者你可以编写小型嵌入式领域特定语言来操作或查询数据框，就像在 `dplyr` 和 `ggplot2` 中所做的那样。你知道了如何进行非标准求值，改变表达式的求值方式，以便在不同于通常求值的作用域中对它们进行求值，这通常可以简化函数在管道中的使用方式；但在编写程序时，应谨慎使用此类编程技术，因为很难推理在函数间传递的表达式。

你不应过度使用元编程。阅读你代码的人，大概知道如何阅读 `R` 代码，因此你不应打破他们对代码求值方式的预期。如果你开发了一个领域特定语言并为此使用元编程，这通常没问题；能够阅读你实现的语言表达式的人会知道它们的行为方式，但要谨慎使用元编程。它是一个强大的工具，但如果使用不当，也非常容易让你搬起石头砸自己的脚。

## 索引

- A

  - 访问承诺, `pryr` 包

  - 实际函数参数

    - `call` 函数

    - `deparse` 函数

    - 全局变量

    - `match.call` 函数

    - `plot` 函数

    - `substitute`

  - `alist(...)` 表达式

  - 自动加载环境

  - 自动微分

- B

  - `bquote` 函数

- C

  - 调用函数

    - 访问实际函数参数 (参见 实际函数参数)

    - 访问调用环境中的元素

    - `bind` 函数

    - `bindings$bindings`

    - `delayedAssign`

    - 求值环境

    - `expression` 函数

    - `expression` 函数, 嵌套

    - `parent.frame`

  - 组件, 当前函数

  - 构造函数

- D

  - `delayedAssign` 函数

  - `deparse` 函数

  - `diff_expr` 函数

- E

  - 空环境

  - 环境图

  - 环境

    - 自动加载

    - `base` 包

    - `baseenv()` 环境

    - 调用栈, 父框架环境

    - 链接链

    - `environmentName`

    - 显式创建与表达式求值

    - 函数调用图, 嵌套函数图, 包

  - Depends: graphics

  - Imports: MASS

  - `NAMESPACE`

  - `sd` 函数

  - `stats`

  - `stats::sd` 和 `base::ls`

  - Suggests: 导入的函数

  - 操作

  - `new.env` 函数

  - `parent.frame` 函数

  - `search` 函数

- 求值环境

- 表达式

  - 访问与操作

  - 控制结构

  - 函数调用中的自动微分

  - 简化

  - 参数

  - `bquote`

  - 字符串

  - 分割与导出

  - `expr[[1]]`

  - 函数调用

  - 函数符号

  - 处理调用

  - 乘法

  - 命名函数

  - `quote(-simplified)`

  - `simplify_call` 函数

- F, G, H, I, J, K, L

  - 形参

- 函数参数

  - 函数

    - 实际参数 (参见 实际函数参数)

    - 调用组件, 当前函数

    - 环境

    - 函数形参

    - `parent.env`

    - `sys.function`

  - 构造

  - `delayedAssign`

  - 操作

  - 环境

  - `eval` 函数

  - 形参

  - 函数体

    - 修改

  - M

  - `make_macro`

  - `match.call` 函数

  - 修改函数

- N, O

  - 非标准求值 (`NSE`)

    - 显式求值表达式

    - 函数内部使用 `substitute`

    - 修改环境

    - `my_with`

    - 与函数一起使用 `substitute`

  - 编写宏

- P

  - `parse` 函数

  - `pryr` 包

- Q

  - `Quote` 函数

  - 引号表达式

- R

  - 递归函数

- S, T, U

  - `search` 函数

  - 替换

    - `deparse(substitute(x))`

    - 表达式, 变量

    - 函数参数隐式/显式

    - `substitute` 函数

    - 变量

- V, W, X, Y, Z

  - 逐字表达式