# 5. 解析和操作表达式

R 编程语言的一个强大特性是，它允许我们将语言本身的表达式视为数据，作为程序的一部分进行检查和修改——即所谓的元编程。在程序中，我们可以获取一段 R 代码，在对它求值之前进行程序化操作。我们需要在代码被求值之前获取它，有几种方法可以实现。最简单的方法是“引用”表达式，这会使表达式保持未求值状态。

在本章中，我们将使用以下库：

```
library(purrr)
library(rlang)
library(magrittr)
```



## 引用与求值

如果你写下像下面这样的表达式，R 会立即尝试对其求值：

```
2 * x + y
```

它会在当前作用域中查找变量 `x` 和 `y`，如果找到了，就对表达式求值；如果找不到，就会报错。当 R 完成表达式求值时，我们要么得到一个值，要么得到一个错误。如果是前者，该表达式本质上就等同于求值结果（计算时间暂且不论）。像这样的字面表达式，我们无法在程序中捕获到它——我们得到的要么是错误，要么是表达式求值后的结果。如果我们想捕获实际的表达式，就需要对其进行“引用”。如果我们将表达式包裹在函数 `quote` 的调用中，就会阻止对表达式求值，并得到一个表示未经求值的表达式的数据结构。

```
quote(2 * x + y)
## 2 * x + y
```

这种表达式的类别是“调用”（call）。

```
expr <- quote(2 * x + y)
class(expr)
## [1] "call"
```

之所以是调用，是因为中缀运算符本质上是函数调用的语法糖，且所有函数调用表达式都属于这种类型。对于“调用”对象，我们可以像对列表那样通过索引来获取其组成部分。第一个元素是函数名，其余元素是函数调用的参数。对于二元运算符，自然会有两个参数。

对于这个表达式，其函数调用是加法运算：

```
expr[[1]]
## `+`
expr[[2]]
## 2 * x
expr[[3]]
## y
```

之所以是加法运算，是因为乘法优先级高于加法，所以该表达式等同于以下式子：

```
(2 * x) + y
```

这是因为乘法在表达式中的嵌套层级比加法更深。乘法的结果作为加法调用的第一个参数被访问，所以该对象中的第二个元素如下所示：

```
expr[[2]][[1]]
## `*`
expr[[2]][[2]]
## [1] 2
expr[[2]][[3]]
## x
```

要对一个已引用的表达式求值，我们可以使用函数 `eval`。下面的表达式：

```
eval(quote(2 * x + y))
```

等同于直接写出其中的字面表达式：

```
2 * x + y
```

`eval` 函数在表达式求值方式上提供了更大的灵活性，因为它可以修改求值的作用域，我们将在第 7 章详细讨论这一点。

将引用表达式与函数结合会引入额外的复杂性，至少在我们想要在函数调用中处理引用时是这样。不过，我们可以将引用表达式作为参数传递给函数，如下所示：

```
f <- function(expr) expr[[1]]
f(quote(2 * x + y))
## `+`
```

但是，如果我们想直接把字面表达式传递给函数，情况就变得更复杂了。

```
f(2 * x + y)
## Error in f(2 * x + y): object 'x' not found
```

在函数 `f` 中，当我们返回 `expr[[1]]` 时，R 会先尝试对表达式求值，但该表达式依赖于未定义的变量 `x` 和 `y`。即使我们定义了 `x` 和 `y`，我们得到的仍然不是一个可供操作的“调用”对象，而是表达式求值的结果。

```
x <- 2
y <- 3
f(2 * x + y)
## [1] 7
```

在函数内部使用 `quote` 也帮不了我们。如果我们写 `quote(expr)` ，得到的是表达式 `expr`——一个单一的符号——而不是我们传递给 `f` 的那个参数。

```
f <- function(expr) {
expr <- quote(expr)
expr[[1]]
}
f(2 * x + y)
## Error in expr[[1]]: object of type 'symbol' is not subsettable
```

为了将实际参数作为引用表达式获取，我们需要使用 `substitute` 函数。

```
f <- function(expr) {
expr <- substitute(expr)
expr[[1]]
}
f(2 * x + y)
## `+`
```

实现这一功能需要两个因素共同作用。首先，R 中的函数参数是惰性求值的，所以只要我们不使用 `expr` 参数，它就不会被求值。因此，即使 `x` 和 `y` 未定义，只要不对 `f` 的参数求值，就不会报错。其次，`substitute` 不会对其参数求值，而是返回一个引用对象，其中变量会被替换为它们在当前作用域中的值。¹ `substitute` 的参数不必是一个单一的变量名，它可以是任何表达式，该表达式会被视为已引用，随后会进行变量替换，返回值将是修改后的引用表达式。

```
f <- function(expr) {
expr <- substitute(expr + expr)
expr
}
f(2 * x + y)
## 2 * x + y + (2 * x + y)
```

另一个复杂情况出现在我们试图在函数内部对一个引用表达式求值时。你可能会认为以下两个函数是等价的，因为 `eval(quote(expr))` 应该等同于 `expr`，但它们并不等价。

```
f <- function(expr) {
expr + expr
}
g <- function(expr) {
x <- substitute(expr + expr)
eval(x)
}
```

如果我们确保 `x` 和 `y` 都已定义，那么函数 `f` 返回的是表达式值的两倍。

```
x <- 2; y <- 3
f(2 * x + y)
## [1] 14
```

而函数 `g` 则会报错，因为 `x` 的类型不正确。

```
g(2 * x + y)
## Error in 2 * x: non-numeric argument to binary operator
```

默认情况下，`eval` 函数会在当前作用域内对表达式求值，而在函数内部，当前作用域就是该函数的求值环境。在 `g` 内部，我们将 `x` 定义为从 `substitute` 调用得到的表达式，因此 `eval` 看到的正是这个 `x`。如果你希望 `eval` 在另一个作用域中求值表达式，则需要将环境作为第二个参数传递给它。如果你希望它在调用函数的作用域（而非函数内部作用域）中对表达式求值，可以使用 `parent.frame` 函数来获取该作用域。

```
g <- function(expr) {
x <- substitute(expr + expr)
eval(x, parent.frame())
}
g(2 * x + y)
## [1] 14
```

我们将在第 7 章更详细地讨论环境、作用域以及表达式求值方式。在本章的剩余部分，我们将重点放在操作表达式上，而不是对它们求值。



## 探索表达式

表达式是一种递归数据结构，你可以像探索其它递归结构一样探索它。我们可以用如下文法来定义表达式：

```
EXPRESSION ::= CONSTANT
|  NAME
|  PRIMITIVE
|  PAIRLIST
|  CALL EXPRESSION_LIST
EXPRESSION_LIST
::= EXPRESSION
|  EXPRESSION EXPRESSION_LIST
```

我们不会进一步展开表达式的文法，只需约定它们都是合法的 R 表达式即可。所有表达式都属于这五类之一。前四类是文法中的终结符，而`call`表达式是递归的；一个调用由函数及其参数构成，而所有这些也都是其它表达式。

我们可以使用递归函数来探索表达式，其中前三个元变量 `CONSTANT`、`NAME` 和 `PRIMITIVE` 是不包含其他表达式的基本情况，而 `PAIRLIST` 可能包含、`CALL` 则必然包含其他表达式，因此必须在递归调用中处理。

在这几个元变量中，`CONSTANT` 指代任何字面数据（如数字或字符串），`NAME` 指代任何变量名，`PRIMTIVE` 指代用 C 语言编写的、作为 R 实现一部分的函数，`PAIRLIST` 指代函数定义中的形参（后面会详述），`CALL` 指代函数调用。函数调用包含了比前四种更复杂的所有情况。由于 R 中任何有实际作用的操作都被视为函数调用，包括函数定义和控制结构等语句，因此它们都属于`CALL`类型。正如我们之前所见，调用类似于列表，并且总是至少包含一个元素：第一个元素是被调用的函数，其余分量是该函数的参数。

为了递归地探索表达式，我们可以编写函数来测试这四种情况。常量通过`is.atomic`函数识别，名称通过`is.name`函数识别，原语通过`is.primitive`函数识别，点对列表通过`is.pairlist`识别，调用通过`is.call`函数识别。一个用于打印表达式结构的函数可以如下所示：

```
print_expression <- function(expr, indent = "") {
  if (is.atomic(expr)) {
    cat(indent, "- ", expr, "\n", sep = "")
  } else if (is.name(expr)) {
    cat(indent, "- ", as.character(expr), "\n", sep = "")
  } else if (is.pairlist(expr)) {
    cat(indent, "-  [\n")
    new_indent <- paste0(indent, "    ")
    for (i in seq_along(expr)) {
      cat(new_indent, names(expr)[i], " ->\n")
      print_expression((expr[[i]]), new_indent)
    }
    cat(indent, "    ]\n")
  } else {
    print_expression((expr[[1]]), indent)
    new_indent <- paste0("  ", indent)
    for (i in 2:length(expr)) {
      print_expression(expr[[i]], new_indent)
    }
  }
}
```

这里我们没有显式测试调用类型；如果表达式不属于前四种情况，那它必然是第五种。在这个打印函数中，我们处理了两个特殊情况——函数定义的源码引用和点对列表中的缺失表达式。我们将在后面讨论这些。

我们可以通过在我们之前探索过的表达式上调用该函数来观察其运行效果。

```
print_expression(quote(2 * x + y))
##   -  +
##     -  *
##       -  2
##       -  x
##     -  y
```

这个美化打印的表达式清晰地展示了我们在前一节中探索过的结构。

声明一个函数被视为一个函数调用——即对 `function` 函数的调用。

```
print_expression(quote(function(x) x))
##   -  function
##     -  [
##         x  ->
##            -  MISSING
##        ]
##     -  x
##     -  srcref = function(x) x
```

对于函数定义，我们有一个调用对象，其第一个参数是 `function`，第二个参数是定义函数参数的点对列表，第三个元素是函数体——即另一个表达式。此外还有一个名为 `srcdef` 的第四个元素，它是一个原子向量，捕获了用于定义函数的实际代码。在打印函数中，我们只需打印源码定义的文本表示，这可以通过粘贴表达式得到。

我们声明的函数参数列表就是使用点对列表数据结构的地方。我们可以使用 `names` 函数获取形参的名称，并通过索引点对列表来获取默认参数。没有默认参数的参数在这里是一种特殊情况，它们包含的表达式是一个空字符串。在打印函数中，我们通过将空字符串改为字符串 `MISSING` 来显式表示这一点。如果有默认参数，那么它们会被表示为我们可以递归探索的表达式。

```
print_expression(quote(function(x = 2 * 2 + 4) x))
##   -  function
##     -  [
##         x  ->
##            -  +
##              -  *
##                -  2
##                -  2
##              -  4
##        ]
##     -  x
##     -  srcref = function(x = 2 * 2 + 4) x
print_expression(quote(function(x, y = 2 * x) x + y))
##   -  function
##     -  [
##         x  ->
##            -  MISSING
##         y  ->
##            -  *
##              -  2
##              -  x
##        ]
##     -  +
##       -  x
##       -  y
##     -  srcref = function(x, y = 2 * x) x + y
```

函数调用的通常情况是，“调用”列表中的第一个元素是一个指向函数的符号，在 R 中任何能返回函数的表达式都可以被用作函数。这意味着调用的第一个元素可以是任何表达式。例如，如果我们定义一个函数并立即调用它，那么调用对象的第一个元素将是该函数定义本身。

```
expr <- quote((function(x) x)(2))
print_expression(expr)
##   -  (
##     -  function
##       -  [
##           x  ->
##              -  MISSING
##          ]
##       -  x
##       -  srcref = function(x) x
##     -  2
expr[[1]]
## (function(x) x)
expr[[2]]
## [1] 2
```

作为对表达式进行一些有实际意义操作的示例，我们可以编写一个函数来收集表达式中所有未绑定的变量。如果我们递归遍历一个表达式，可以收集所有已绑定和未绑定的符号。为了获取未绑定的变量，我们可以跟踪那些已绑定的变量并避免收集它们。首先，忽略那些可能在表达式本身之外绑定的变量（即在我们将要调用函数的那个作用域中绑定的变量），已绑定的变量是在函数定义中被命名的那些变量。我们可以通过 `function` 调用的第二个参数（即点对列表）来识别它们。在递归遍历表达式时，我们捕获这些变量并将它们向下传递给递归过程。除此之外，我们只收集符号。在下面的实现中，我使用我们之前见过的链表来收集符号，并在收集时将符号转换为字符。之所以这样做，是因为以后我可以使用符号的字符表示来检查某个符号是否存在于某个环境中。我使用 `cons` 函数在链表中收集符号。

```
cons <- function(car, cdr) list(car = car, cdr = cdr)
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
```

为了处理列表，使用 `list` 对象比使用链表对象更容易操作，因此我们还需要之前提到过的 `lst_to_list` 函数。

```
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
```



我们显式地避免在收集符号时收集空符号。空符号是在我们对没有默认值的函数参数的配对列表进行递归时得到的符号。我们不将其视为变量，无论是否绑定。我们处理符号的方式很直接。对于配对列表，我们收集将被绑定的参数，并递归遍历默认参数以收集其中任何未绑定的变量。对于调用，我们通过扩展绑定变量列表然后递归来处理函数定义。对于其他任何情况——实际上是指任何原子值——我们只需返回调用函数时使用的列表。毕竟常量值中没有未绑定的变量。

该递归函数对一个被引用的表达式进行操作，并收集该表达式内部所有未绑定的符号。我们将其封装在一个函数中，该函数对表达式进行引用，调用递归函数，然后移除在调用作用域（`parent.frame`）中定义的符号。

```r
collect_symbols <- function(expr) {
  expr %>%
    substitute() %>%
    lst_to_list() %>%
    unique() %>%
    purrr::discard(exists, parent.frame()) %>%
    unlist()
}
```

这里，我使用了`purrr`包中的`discard`函数来移除所有满足谓词的元素。对于谓词，我使用了函数`exists`，其第二个参数是调用环境`parent.frame`。这可以移除在调用`collect_symbols`的作用域中定义的符号，包括全局定义的函数，例如`*`、`+`和`function`。

我通过`unlist`对最终结果进行管道操作，将`list`转换为字符向量。这仅出于美化打印的原因。它在控制台打印时能提供更好的输出。对于编程来说，您既可以使用向量，也可以使用`list`。

如果我们移除之前定义的变量`x`和`y`，那么表达式`2 * x + y + z`应该有三个未绑定的变量：`x`、`y`和`z`。这正是我们所发现的：

```r
rm(x) ; rm(y)
collect_symbols(2 * x + y + z)
## [1] "z" "y" "x"
```

如果我们定义了其中一个变量，例如`z`，那么它就不再是未绑定的。

```r
z <- 3
collect_symbols(2 * x + y + z)
## [1] "y" "x"
```

函数定义也会绑定变量，因此这些变量不会被收集。

```r
collect_symbols(function(x) 2 * x + y + z)
## [1] "y"
collect_symbols(function(x) function(y) f(2 * x + y))
## NULL
```

默认值可以包含未绑定的变量；我们收集这些值。

```r
collect_symbols(function(x, y = 2 * w) 2 * x + y)
## [1] "w"
```

关于如何探索表达式，我们尚未完全了解。如前所述，对表达式进行实际的递归探索很简单。但通常，它必须与对表达式的求值相结合。而且，这种求值通常不遵循表达式求值的常规规则，因为我们必须对某些表达式求值，同时保持其他表达式被引用。当我们开始操纵表达式如何求值时，我们称之为非标准求值，这是第 7 章的主题。然而，在这里，我想让您初步了解它涉及的内容。

如果我们编写一个像这样的简单函数：

```r
f <- function(expr) collect_symbols(expr)
```

我们可能期望它能给出表达式中的未绑定变量，但它返回一个空列表，如下所示：

```r
f(2 + y * w)
## NULL
```

这是因为当我们尝试对所谓的非标准求值的函数进行编程时，将会遇到两个问题的结合。首先，当我们在`collect_symbols`函数中使用`substitute`时，我们得到的是调用`substitute`时的字面表达式。我们传递给`f`中`substitute`的参数是`expr`。`f`本身被调用时所用的表达式并不会被传递进去。其次，我们在`collect_symbols`内部测试绑定变量的环境是调用环境。当我们从`f`调用该函数时，调用环境是`f`的函数体。在这个环境中，变量`expr`是已定义的——它是函数的形式参数——因此它将被视为已绑定。

我们稍后将详细探讨环境以及如何使用非标准求值进行编程，但针对这些问题的通用解决方案是避免在您计划从其他函数调用的函数中使用非标准求值。这是一种用于编写领域特定语言的强大技术，但请将其限制在语言的接口层，而不是内部函数。对于`collect_symbols`，我们可以通过编写另一个函数来解决这个问题，该函数接受一个被引用的表达式和一个我们应该在其中查找变量的环境作为参数。然后，当我们想要进行非标准求值时，可以从`collect_symbols`调用此函数；如果我们想从其他函数中使用它，则直接调用另一个函数。

```r
collect_symbols_ <- function(expr, env) {
  expr %>%
    lst_to_list() %>%
    unique() %>%
    purrr::discard(exists, env) %>%
    unlist()
}
collect_symbols <- function(expr) {
  collect_symbols_(substitute(expr), parent.frame())
}
```



## 操作表达式

我们不仅能检查表达式，还能在程序中修改或创建新的表达式。两个原始表达式——常量和符号——无法被修改，因为它们只是数据。不过，我们可以修改调用和配对列表，尽管后者并非我们通常的做法。我们在创建新函数时会用到配对列表，但通常要么创建新的配对列表来设置函数的形参，要么从另一个函数中获取参数，很少修改现有的配对列表。无论如何，配对列表和调用都可以通过对其组件进行索引来赋值。

先解决这个问题，下面是一个修改配对列表的示例。我们可以像这样构造定义函数的表达式：

```
f <- quote(function(x) 2 * x)
f
## function(x) 2 * x
```

这是一个“调用”类型的表达式——它调用了定义函数的 `function` 函数（试试快速念出这句话）——其第二个参数是定义函数参数的配对列表。

```
f[[2]]
## $x
```

如果我们对此配对列表中的元素进行赋值，就相当于为函数提供了默认参数。赋给它们的值必须是加引号的表达式。

```
f[[2]][[1]] <- quote(2 * y)
f
## function(x = 2 * y) 2 * x
```

要改变函数参数的名称，就必须修改配对列表组件的名称。我们可以使用 `names<-` 函数来实现这一点。

```
names(f[[2]]) <- c("a")
f[[3]] <- quote(2 * a)
f
## function(a = 2 * y) 2 * a
```

在这个示例中，我们还看到了如何通过第三个组件来修改函数体。

通过这个示例，我们已经掌握了修改调用表达式所需的一切知识。我们修改的只是调用的一个特例——对 `function` 的调用。任何其他调用都可以用相同方式修改。

```
expr <- quote(2 * x + y)
expr
## 2 * x + y
expr[[1]] <- as.symbol("/")
expr
## 2 * x/y
expr[[2]][[1]] <- as.symbol("+")
expr
## (2 + x)/y
```

我们可以使用 `call` 函数构造新的调用对象。该函数的第一个参数是所要调用的函数，可以是符号或字符串，并且会自动被加引号。之后，您可以传入数量可变的参数，这些参数在被放入构造的表达式之前会先被求值。

```
call("+", quote(2 * x), quote(y))
## 2 * x + y
call("+", call("*", 2, quote(x)), quote(y))
## 2 * x + y
```

如果您要创建的是对带有具名参数的函数的调用（而非运算符调用），也可以将这些参数提供给 `call` 函数。

```
call("f", a = quote(2 * x), b = quote(y))
## f(a = 2 * x, b = y)
```

如果您不希望参数被求值，那么务必对其加引号。`call` 函数不会为您自动完成这一步。

```
z <- 2
call("+", 2 * z, quote(y))
## 4 + y
```

在 `rlang` 包中，还有两个用于创建调用的函数。`lang` 函数的功能与 `call` 函数类似，但您可以指定所调用函数应在其中被查找到的命名空间。`new_language` 函数则允许您以显式的配对列表形式提供调用参数。

```
library(rlang)
lang("+", quote(2 * x), quote(y))
## 2 * x + y
new_language(as.symbol("+"), pairlist(quote(2 * x), quote(y)))
## 2 * x + y
```

如果您计划在 R 中进行大量元编程，那么 `rlang` 包值得探索。它提供了多个用于操作和创建表达式、函数以及管理环境的函数。我们将在第 8 章中进一步探讨这个包。

如果所创建的调用是针对 `function` 的，则会有一个额外的复杂情况。这个函数需要一个配对列表作为其第二个参数，因此您必须创建这样一个对象。如果您想创建一个没有默认参数的函数，则需要创建一个在命名位置上包含“缺失”元素的列表。创建缺失参数的方法是不带参数地调用 `substitute`，因此一个用于创建无默认参数函数参数列表的函数可以像下面这样：

```
make_args_list <- function(args) {
res <- replicate(length(args), substitute())
names(res) <- args
as.pairlist(res)
}
```

我们可以用它来像这样构造一个对 `function` 的调用：

```
f <- call("function",
make_args_list(c("x", "y")),
quote(2 * x + y))
f
## function(x, y) 2 * x + y
```

但请记住，这只是创建函数的表达式，并非函数本身，它不会像函数那样运作。

```
f(2, 3)
## Error in f(2, 3): could not find function "f"
```

这里的错误信息看起来有点奇怪。R 并不是在抱怨 `f` 不是函数，而是说找不到函数 `f`。这是因为当您使用符号进行函数调用时，R 会去查找函数，并且不会将值 `f` 与函数 `f` 混淆。这里我们只有一个值版本的 `f`。总之，要得到实际的函数，我们需要对该调用进行求值。

```
f <- eval(f)
f
## function (x, y)
## 2 * x + y
f(2, 3)
## [1] 7
```

创建函数更直接的方法是使用 `rlang` 包中的 `new_function` 函数。

```
f <- new_function(make_args_list(c("x", "y")),
quote(2 * x + y))
f
## function (x, y)
## 2 * x + y
f(2, 3)
## [1] 7
```

作为最后一个示例，我们可以将前面学到的表达式创建方法与上一节中的表达式探索函数结合起来，将包含未绑定变量的表达式转换为函数。我们可以使用前面的 `collect_symbols_` 函数收集表达式中的所有未绑定变量，然后使用 `new_function` 创建函数。

```
expr_to_function <- function(expr) {
expr <- substitute(expr)
unbound <- collect_symbols_(expr, caller_env())
new_function(make_args_list(unbound), expr, caller_env())
}
```

这里，我使用了 `rlang` 中的另一个函数 `caller_env`。该函数的功能与我们之前使用的 `parent.frame` 相同，但名称更具信息量。因此，我推荐使用 `caller_env` 替代 `parent.frame`。

在此次对 `new_function` 的调用中，我们提供了比之前使用它时更多的参数。之前我们只提供了两个参数：函数的参数及其函数体。这里，我们还提供了函数的环境。这将是函数的封闭环境。函数将在此环境中找到那些非函数局部变量或非函数参数的变量值。由于我们将调用者环境中的变量视为已绑定的，我们必须确保创建的函数也能找到它们，因此我们将函数置于同一环境中。如果此解释对您来说不清晰，那么在阅读第 7 章（其中我们将更详细地探讨环境）之后，再回来看这个示例。理想情况下，到那时它会更清晰。

`expr_to_function` 完全实现了我们的预期功能。它根据一个表达式创建函数，该函数的参数就是表达式中的未绑定变量。

```
f <- expr_to_function(2 * x + y)
f
## function (y, x)
## 2 * x + y
f(x = 2, y = 3)
## [1] 7
g <- expr_to_function(function(x) 2 * x + y)
g
## function (y)
## function(x) 2 * x + y
g(y = 3)(x = 2)
## [1] 7
```

函数中变量的顺序取决于它们在表达式中出现的顺序，以及 `unique` 函数处理后的顺序。因此，调用生成的函数时最好使用具名参数。

脚注 1

`substitute` 函数会用变量在当前作用域中的值，或在作为第二个参数提供的环境中的值来替换变量，但全局环境中的变量除外。这些变量保持不变。如果您试验 `substitute` 函数，请注意它在函数作用域内的行为与在全局作用域内的行为不同。



