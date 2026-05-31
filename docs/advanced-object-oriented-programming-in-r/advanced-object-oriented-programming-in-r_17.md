# 5. 运算符重载

运算符重载，即为诸如 `+` 或 `-` 这样的运算符赋予新的或额外的功能，其本身并非面向对象编程的固有特性。但由于它可以被视为为函数添加多态性，因此在介绍了通过泛型函数实现的多态性之后，将这部分内容放在这里非常自然。

关于运算符重载是好是坏，观点不一。有些语言允许；有些则不允许。有些语言允许你创建自己的中缀运算符，但不允许更改现有的；还有些语言只是不一致地允许对内置对象进行某些运算符重载，但对用户定义的则不行。反对重载的一个论点是运算符的预期行为。我们根深蒂固地认为 `+` 意味着加法，如果它还表示字符串连接，我们可能会难以接受。当然，这个论点忽略了我们对 `+` 同时用于整数和浮点数加法并无问题的事实。另一个论点，在我看来更有说服力，是记住一个运算符的功能比记住一个函数的功能更难，因为函数名至少给了我们一些关于函数用途的提示。事实是，如果谨慎使用，中缀运算符可以为我们提供比简单函数调用更方便的语法。你可能觉得 `2 * x + 5` 比 `plus(times(2, x), 5)` 更容易阅读，大多数编程语言（Lisp 方言是一个明显的例外）也更倾向于前者而不是后者。对于用户定义或用户重载的运算符也是如此。使用 `magrittr` 的管道运算符 `%>%` 可以让分析工作流的可读性大大提高，而 `ggplot2` 对 `+` 的重载也让绘图代码更易读。

由于在 R 语言中，你既可以重载现有运算符，也可以创建自己的新运算符，因此你必须为任何给定的情况选择最合适的方式。我的经验法则是：除非使用现有运算符感觉非常自然，否则优先考虑新的中缀运算符。当然，这是一个非常主观的评价，但有些操作看起来就像“加法”——我会说 `ggplot2` 中的组合操作可以被如此合理化——而其他操作则不像，比如 `magrittr` 中的管道操作。这是一个需要判断的决定，你始终可以通过代码实验来找到你认为最自然的方式。

在本章中，我们将看到如何重载运算符。我不会介绍如何创建中缀运算符。如果你对此感兴趣，可以在我的《R 语言函数式编程》一书中找到相关描述。

## 函数与运算符

R 语言中的每一个操作都涉及一个函数调用。控制结构、下标、甚至括号都涉及函数，自然，运算符也涉及函数调用。这意味着运算符可以被覆盖。你可以通过定义一个新版本的 `+` 来替换其原有的实现。但你其实不应该这样做。用用户定义的运算符替换内置运算符会影响你所有的代码，降低运行速度，并且很可能引入难以发现的错误。不过，为你自己的类定义运算符时无需这样做。运算符是泛型的，你可以为它们定义专门的版本，实现任何运算符应如何处理你定义的新类。

为了说明如何做到这一点，我们定义一个对某个数字 `n` 取模的算术类。这里我假设 `value` 是某种数值类型——在生产代码中，我们会为此编写测试，但在此示例中，我们只是隐含地假定——并且我们使用一个属性来存储数字 `n`。然后我们计算值对 `n` 取模的结果，设置类，并返回结果。

```
modulus <- function(value, n) {
  result <- value %% n
  attr(result, "modulus") <- n
  class(result) <- c("modulus", class(value))
  result
}
```

为了美观地打印此类的值，我们定义 `print` 方法。我们希望使用基础类型打印值 `x`，但在其上方附加一行显示 `n`。如果我们直接调用 `print(x)`，由于 `x` 的类型，我们会递归回到 `print.modulus`，但我们可以使用函数 `unclass` 来去除 `x` 的类。不过，它并不能完全去除类型。如果 `x` 是数值类型，`unclass` 只是将 `x` 还原为该类型。当我们打印带有属性的基本值时，属性也会被打印出来，所以我们在打印还原后的 `x` 之前要去掉所有属性。这里我们本可以直接调用 `print(x)`，但 `NextMethod()` 同样有效，所以我在此使用了它。

```
print.modulus <- function(x, ...) {
  cat("Modulus", attr(x, "modulus"), "values:\n")
  # 移除属性以获得纯数值打印
  x <- unclass(x)
  attributes(x) <- NULL
  NextMethod()
}
(x <- modulus(1:6, 3))
## Modulus 3 values:
## [1] 1 2 0 1 2 0
```

### 定义单运算符

我们可以通过定义函数 `` `+.modulus` `` 来为此类型定义加法的含义：

```
`+.modulus` <- function(x, y) {
  n <- attr(x, "modulus")
  x <- unclass(x)
  y <- unclass(y)
  modulus(x + y, n)
}
```

我们首先从 `x` 中获取 `n`。然后去除两个操作数的类信息，以便调用原始的 `+` 来计算它们的和。如果我们不这样做，当我们尝试将 `x` 和 `y` 相加时，又会发生递归调用。接着我们计算结果并使用 `modulus` 构造函数以正确的类型返回它。

这类运算符函数的调度与我们之前看到的泛型函数略有不同。对于泛型函数，调度基于第一个参数的类型（至少除非你明确说明否则如此），但对于运算符，调度基于两个操作数的类型。如果两者都有一个实现了该运算符的类，它们必须是相同的类。如果其中只有一个有类（这里原始类如 `"numeric"` 或 `"integer"` 不算），那么调度就基于那个有类的操作数。因此，如果 `x` 是 `"modulus"` 类型，那么 `x + 1:6` 和 `1:6 + x` 都会调用 `` `+.modulus` ``。

```
x + 1:6
## Modulus 3 values:
## [1] 2 1 0 2 1 0
1:6 + x
## Modulus values:
## numeric(0)
```

第一个表达式符合我们的预期，但第二个没有。这不是因为调度没有生效，而是因为我们只从第一个操作数获取了 `n`。由于我们只能保证两个操作数中有一个是 `"modulus"` 类型，我们需要检查两者。我们可以简单地通过检查属性 `"modulus"` 是否为 `NULL` 来做到：

```
`+.modulus` <- function(x, y) {
  n <- ifelse(!is.null(attr(x, "modulus")),
              attr(x, "modulus"), attr(y, "modulus"))
  x <- unclass(x)
  y <- unclass(y)
  modulus(x + y, n)
}
x + 1:6
## Modulus 3 values:
## [1] 2 1 0 2 1 0
1:6 + x
## Modulus 3 values:
## [1] 2 1 0 2 1 0
```

根据我们希望此类加法具有的语义，我们可能不允许 `n` 不同的类型相加。如果我们现在将这样的类型相加，`n` 将从第一个操作数获取。

```
y <- modulus(1:6, 2)
x + y
## Modulus 3 values:
## [1] 2 2 1 1 0 0
```

通过多加一点检查，我们可以确保两个操作数是兼容的：

```
`+.modulus` <- function(x, y) {
  nx <- attr(x, "modulus")
  ny <- attr(y, "modulus")
  if (!is.null(nx) && !is.null(ny) && nx != ny)
    stop("Incompatible types")
  n <- ifelse(!is.null(nx), nx, ny)
  x <- unclass(x)
  y <- unclass(y)
  modulus(x + y, n)
}
x + y
## Error in `+.modulus`(x, y): Incompatible types
y <- modulus(rev(1:6), 3)
x + y
## Modulus 3 values:
## [1] 1 1 1 1 1 1
```



### 分组运算符

使用泛型函数，我们可以为用户定义的类型定义所有相关的运算符，但也可以在一个函数 `Ops` 中处理所有运算符。此函数被称为“分组泛型方法”，因为它处理一组泛型函数；其他分组方法包括 `Math`、`Complex` 和 `Summary`，这些我们在此不作讨论。

如果我们定义函数 `Ops.modulus`，它将为所有 `modulus` 对象上未定义运算符函数的情况被调用。也就是说，如果我们像上面那样定义了 `` `+.modulus` ``，那么该函数将优先于 `Ops.modulus` 被调用；否则，如果一个或两个操作数的类型是 `modulus`，那么 `Ops.modulus` 将被调用。

我们可以这样定义它：

```
Ops.modulus <- function(e1, e2) {
nx <- attr(e1, "modulus")
ny <- attr(e2, "modulus")
if (!is.null(nx) && !is.null(ny) && nx != ny)
stop("Incompatible types")
n <- ifelse(!is.null(nx), nx, ny)
result <- unclass(NextMethod()) %% n
modulus(result, n)
}
```

函数开头对输入值的测试与 `` `+.modulus` `` 中的相同。测试之后，我们使用 `NextMethod` 来调用基于底层类型的操作。这会从操作数中剥离 `modulus` 类，并计算我们当前正在处理的任何操作。我们对结果进行 `unclass` 处理，这是必要的，因为结果将继承 `Ops` 操作数的属性，所以如果我们不这样做，结果将具有 `modulus` 类，然后我们计算模数 `n`。如果我们不进行 `unclass` 处理，这将导致递归调用，但由于我们确实移除了类，我们只在底层类型中计算模数。最后，我们为结果创建一个 `modulus` 对象。

定义了这个函数后，我们就能一次性获得所有二元运算符。

```
y <- modulus(rev(1:6), 3)
x - y
## Modulus 3 values:
## [1] 1 0 2 1 0 2
x * y
## Modulus 3 values:
## [1] 0 1 0 0 1 0
```

这也包括比较运算符：

```
x == y
## Modulus 3 values:
## [1] 0 1 0 0 1 0
x == x
## Modulus 3 values:
## [1] 1 1 1 1 1 1
x != y
## Modulus 3 values:
## [1] 1 0 1 1 0 1
x != x
## Modulus 3 values:
## [1] 0 0 0 0 0 0
```

它甚至包括一元运算符。但是，如果我们使用一元负号，函数调用中的参数 `e2` 将会缺失，目前我们没有正确处理这种情况。

```
- x
## Error in Ops.modulus(x): argument "e2" is missing, with no default
```

不过，我们可以通过检查 `e2` 是否缺失来轻松修复这个问题。除此之外，函数将按原样工作。

```
Ops.modulus <- function(e1, e2) {
nx <- attr(e1, "modulus")
ny <- if (!missing(e2)) attr(e2, "modulus") else NULL
if (!is.null(nx) && !is.null(ny) && nx != ny)
stop("Incompatible types")
n <- ifelse(!is.null(nx), nx, ny)
result <- unclass(NextMethod()) %% n
modulus(result, n)
}
- x
## Modulus 3 values:
## [1] 2 1 0 2 1 0
```

## 单位示例

作为一个稍微复杂一些的示例，我们定义一个类，用于将物理单位与数值关联起来。这将允许我们检查所操作的单位是否兼容——这样我们就不会从秒中减去米等等——并且会将单位分析作为算术操作的一部分。这个示例是 `units` 包的一个简化版本。`units` 包还处理单位转换和单位简化。这里我们只实现简单的符号单位算术以及一个简单的单位相等性检查。

其思想是拥有物理单位的表示，然后将这些单位与数值关联起来。这里的物理单位指的是像平方公里、米每秒等单位。通常，这些将是符号表达式，但我们只考虑稍简单一些的情况，即单位是物理常数的分数。在这种情况下，我们可以将这些单位表示为分子的项列表和分母的项列表。如果我们始终保持这些列表有序，我们就有了它们的规范表示，并且我们可以通过检查分子和分母列表是否相等来检查两个单位是否相等。我们可以像这样实现构造函数：

```
symbolic_unit <- function(nominator, denominator = "") {
non_empty <- function(x) x != ""
nominator <- sort(Filter(non_empty, nominator))
denominator <- sort(Filter(non_empty, denominator))
structure(list(nominator = nominator,
denominator = denominator),
class = "symbolic_unit")
}
```

我们可以将这些单位转换为分数的字符串表示，以便美观地打印。

```
as.character.symbolic_unit <- function(x, ...) {
format_terms <- function(terms, op) {
if (length(terms) == 0) return("1")
paste0(terms, collapse = op)
}
nominator <- format_terms(x$nominator, "*")
denominator <- format_terms(x$denominator, "/")
paste(nominator, "/", denominator)
}
print.symbolic_unit <- function(x, ...) {
cat(as.character(x, ...), "\n")
}
(x <- symbolic_unit("m"))
## m / 1
(y <- symbolic_unit("m", "s"))
## m / s
```

比较两个符号单位涉及检查分子和分母是否相等。

```
`==.symbolic_unit` <- function(x, y) {
if (!(inherits(x, "symbolic_unit") &&
inherits(y, "symbolic_unit")))
stop("Incompatible types")
return(identical(x$nominator, y$nominator) &&
identical(x$denominator, y$denominator))
}
`!=.symbolic_unit` <- function(x, y) !(x == y)
x == y
## [1] FALSE
x != y
## [1] TRUE
```

只有当物理量具有相同单位时，才能进行加减运算；但乘除单位总是可能的。然后，通过对（符号）单位执行与对量相同的操作来获得结果单位。为了能够处理这一点，我们定义符号单位的乘法和除法。

```
`*.symbolic_unit` <- function(x, y) {
symbolic_unit(c(x$nominator, y$nominator),
c(x$denominator, y$denominator))
}
`/.symbolic_unit` <- function(x, y) {
symbolic_unit(c(x$nominator, y$denominator),
c(x$denominator, y$nominator))
}
x * y
## m*m / s
x / y
## m*s / m
```

现在我们已经具备了表示单位所需的一切。我们只需要定义将单位与量关联起来的类。这个类与我们之前编写的 `modulus` 类非常相似。我们取一个（数值）值，将一个符号单位作为属性关联起来，并设置类。

```
units <- function(value, nominator, denominator = "") {
attr(value, "units") <- symbolic_unit(nominator, denominator)
class(value) <- c("units", class(value))
value
}
```

美观打印遵循我们在 `modulus` 中看到的模式。我们需要剥离类和属性以使用底层的打印方法（通过 `NextMethod` 调用），但仅此而已。

```
print.units <- function(x, ...) {
cat("Units: ", as.character(attr(x, "units")), "\n")
# remove attributes to get plain numeric printing
x <- unclass(x)
attributes(x) <- NULL
NextMethod()
}
(x <- units(1:6, "m"))
## Units:  m / 1
## [1] 1 2 3 4 5 6
```



处理 `units` 的运算符比处理 `modulus` 稍显复杂。我们需要区分两种运算符：一种是要求单位必须匹配的，另一种是需要构造新单位的。前者包括加法、减法和比较运算（假设我们仅当数值及其关联单位均一致时才认为它们相等）；后者包括乘法和除法，其结果的单位必须由操作数计算得出。对于逻辑运算符如何处理物理量（如果确实有意义的话）并不显而易见，因此对于不属于这两类范畴的运算符，我们应该默认采用底层类型的处理方式。

我们利用 `Ops` 组函数来实现这些运算符。在该函数内部，我们可以通过变量 `.Generic` 访问当前正在被求值的实际运算符。这不是函数的参数，但当函数被调用时，它会被设置为正在求值的运算符，我们可以通过 `switch` 语句来检查并妥善处理。

```
Ops.units =", ">")) {
if (!is.null(su1) && !is.null(su2) && su1 != su2)
stop("Incompatible units")
su <- ifelse(!is.null(su1), su1, su2)
return(NextMethod())
}
if (.Generic == "*" || .Generic == "/") {
if (is.null(su1))
su1 <- symbolic_unit("")
if (is.null(su2))
su2 <- symbolic_unit("")
su <- switch(.Generic, "*" = su1 * su2, "/" = su1 / su2)
result <- NextMethod()
attr(result, "units") <- su
return(result)
}
# 对于其余运算符，我们并无良策处理单位
# 因此我们剥离单位信息，回归到数值运算
e1 <- unclass(e1)
e2 <- unclass(e2)
attributes(e1) <- attributes(e2) <- NULL
NextMethod()
}
```

通过如此定义的单位运算符，我们可以将单位与标量结合：

```
2 * x
## Units:  m / 1
## [1]  2  4  6  8 10 12
x + 2
## Units:  m / 1
## [1] 3 4 5 6 7 8
x - 2
## Units:  m / 1
## [1] -1  0  1  2  3  4
```

如果我们尝试相加两个单位不兼容的量，将会收到错误信息。

```
(y <- units(1:6, "m", "s"))
## Units:  m / s
## [1] 1 2 3 4 5 6
x + y
## Error in Ops.units(x, y): Incompatible units
```

然而，当单位兼容时，我们便可以进行加减运算。

```
(z <- units(1:6, "m"))
## Units:  m / 1
## [1] 1 2 3 4 5 6
x + z
## Units:  m / 1
## [1]  2  4  6  8 10 12
x - z
## Units:  m / 1
## [1] 0 0 0 0 0 0
```

乘法和除法始终是允许的，结果的单位由操作数推导得出。

```
2 * x
## Units:  m / 1
## [1]  2  4  6  8 10 12
x * y
## Units:  m*m / s
## [1]  1  4  9 16 25 36
x / y
## Units:  m*s / m
## [1] 1 1 1 1 1 1
```

