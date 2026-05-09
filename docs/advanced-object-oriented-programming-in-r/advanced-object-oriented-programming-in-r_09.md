# 5. 运算符重载

重载运算符，即赋予 `+` 或 `-` 等运算符新的或额外的功能，本质上并非面向对象特性。但由于这可以看作是为函数增加了多态性，因此在学习了基于泛型函数的多态之后，在此处讨论运算符重载显得顺理成章。

关于运算符重载是优是劣，众说纷纭。有些语言支持重载，有些则不支持。有些语言允许你创建自己的中缀运算符，但不能修改现有运算符；还有些语言则前后矛盾，允许对内置对象进行某些运算符重载，却不允许对用户自定义对象进行重载。反对重载的一个论点是运算符的预期行为。我们对 `+` 代表加法这一概念根深蒂固，以至于无法接受它同时表示字符串拼接。当然，这种论点忽略了我们早已习惯用 `+` 同时进行整数和浮点数的加法。另一个论点，在我看来也更站得住脚，是记住运算符的功能比记住函数的功能更难，因为函数名至少能给我们一些关于其用途的提示。事实上，如果使用得当，中缀运算符能提供比简单函数调用更便捷的语法。你可能会觉得 `2 * x + 5` 比 `plus(times(2, x), 5)` 更易读，而且大多数编程语言（Lisp 方言是明显的例外）也更倾向于前者。对于用户自定义或用户重载的运算符也是如此。使用 `magrittr` 的管道运算符 `%>%` 能让分析工作流程更易读，而 `ggplot2` 对 `+` 的重载则让绘图代码更清晰易读。

由于在 R 中既可以重载现有运算符，也可以创建新运算符，因此你需要针对每种情况选择最合适的方式。我的经验法则是：除非使用现有运算符感觉自然，否则优先考虑使用新的中缀运算符。当然，这完全是主观判断，但有些运算看起来确实像是“加法”——我认为在 `ggplot2` 中组合图层可以归为此类——而其他像 `magrittr` 中的管道运算则不然。这需要你做出判断，你也可以不断尝试代码，看看哪种方式最自然。

本章我们将学习如何重载运算符。我不会介绍如何创建中缀运算符。如果你对此感兴趣，可以参考我的《R 函数式编程》一书。

## 函数与运算符

R 中的每个操作都涉及函数调用。控制结构、下标操作，甚至括号都与函数有关，同样，运算符也涉及函数调用。这意味着运算符可以被重写。你可以通过定义 `+` 的一个新版本，来替换 `+` 的现有实现。但你确实不应该这么做。用用户自定义的运算符替换内置运算符会影响你所有的代码，拖慢速度，并且极易引入难以发现的错误。不过，为你自己的类定义运算符并不需要这样操作。运算符是泛型的，你可以定义它们的特化版本，实现任何运算符应如何处理你定义的新类。

为了说明如何实现，我们定义一个用于“取模 n”的算术类。这里我假设 `value` 是某种数值类型——在生产代码中，我们会为此编写测试，但在示例中，我们只是隐式地这么假设——然后我们使用一个属性来存储模数 `n`。接着计算 `value` 对 `n` 取模的结果，设置类，并返回结果。

```
modulus <- function(value, n) {
  result <- value %% n
  attr(result, "modulus") <- n
  class(result) <- c("modulus", class(value))
  result
}
```

为了美观地打印该类的值，我们定义了 `print` 方法。我们想要以底层类型的方式打印值 `x`，但上方附带一行显示 `n`。如果直接调用 `print(x)`，我们会递归回到 `print.modulus`，因为 `x` 的类型就是 `modulus`。但我们可以使用 `unclass` 函数来移除 `x` 的类型。不过，这并不能完全去掉类型。如果 `x` 是数值类型，`unclass` 只会将其降级为该类型。当我们打印带有属性的原始值时，属性也会被打印出来，所以我们在打印降级后的 `x` 之前需要去掉所有属性。这里我们可以直接调用 `print(x)`，但使用 `NextMethod()` 也行，我在这里就用了这个方法。

```
print.modulus <- function(x, ...) {
  cat("Modulus", attr(x, "modulus"), "values:\n")
  # 移除属性以获得纯数值打印效果
  x <- unclass(x)
  attributes(x) <- NULL
  NextMethod()
}
(x <- modulus(1:6, 3))
## Modulus 3 values:
## [1] 1 2 0 1 2 0
```

### 定义单个运算符

我们可以通过定义函数 `` `+.modulus` `` 来定义加法对此类型意味着什么：

```
`+.modulus` <- function(x, y) {
  n <- attr(x, "modulus")
  x <- unclass(x)
  y <- unclass(y)
  modulus(x + y, n)
}
```

我们首先从 `x` 中获取 `n`。然后移除两个操作数的类信息，以便调用原始的 `+` 来计算它们的和。如果不这样做，当我们尝试将 `x` 与 `y` 相加时，又会发生递归，然后我们只需计算结果，并使用 `modulus` 构造函数返回具有正确类型的结果。

这类运算符函数的分派与我们之前看到的泛型函数略有不同。在泛型函数中，分派基于第一个参数的类型（除非你明确指定其他方式），但对于运算符，分派基于两个操作数的类型。如果两个操作数都有实现了某个运算符的类，那么它们的类必须相同。如果只有一个操作数有类（这里 `"numeric"` 或 `"integer"` 这样的原始类不计算在内），则分派基于该操作数进行。因此，如果 `x` 的类型是 `"modulus"`，那么 `x + 1:6` 和 `1:6 + x` 都会调用 `` `+.modulus` ``。

```
x + 1:6
## Modulus 3 values:
## [1] 2 1 0 2 1 0
1:6 + x
## Modulus values:
## numeric(0)
```

第一个表达式符合预期，但第二个却没有。这不是因为分派没起作用，而是因为我们只从第一个操作数中获取了 `n`。由于我们只能保证两个操作数中有一个是 `"modulus"` 类型，所以我们需要同时检查两个操作数。我们可以简单地通过检查属性 `"modulus"` 是否为 `NULL` 来实现：

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

根据我们想让这些类型的加法具有何种语义，我们可能不希望允许具有不同 `n` 的类型相加。如果按照目前的方式相加，`n` 是从第一个操作数中取得的。

```
y <- modulus(1:6, 2)
x + y
## Modulus 3 values:
## [1] 2 2 1 1 0 0
```

通过增加一点检查，我们可以确保两个操作数是兼容的：

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

通过使用泛型函数，我们可以为用户自定义类型定义所有相关的运算符，但也可以在一个单独的函数`Ops`中处理所有运算符。这个函数被称为“分组泛型方法”（group generic method），因为它处理一组泛型函数；其他分组方法还包括`Math`、`Complex`和`Summary`，这些我们在此不作详述。

如果我们定义了函数`Ops.modulus`，那么对于`modulus`对象中未定义运算符函数的所有运算符，都会调用该函数。也就是说，如果我们像上面那样定义了`` `+.modulus` ``，那么该函数将优先于`Ops.modulus`被调用；但除此之外，如果其中一个操作数或两个操作数都是`modulus`类型，则会调用`Ops.modulus`。

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

函数开头的输入值测试与`` `+.modulus` ``相同。测试之后，我们使用`NextMethod`来调用基于底层类型的运算。这会将`modulus`类从操作数中剥离，并执行我们当前处理的任何运算。我们对结果使用`unclass`，这是必需的，因为结果会继承`Ops`操作数的属性，如果不这样做，结果将具有`modulus`类，然后我们计算模`n`。如果不使用`unclass`，这将变成一个递归调用，但由于我们去除了类，我们只是在底层类型上进行取模运算。最后，我们创建一个结果的`modulus`对象。

定义了此函数后，我们就能一次性获得所有二元运算符。

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

它甚至还包括一元运算符。然而，如果我们使用一元负号，参数`e2`在函数调用中将会缺失，而我们现在并未正确处理这种情况。

```
- x
## Error in Ops.modulus(x): argument "e2" is missing, with no default
```

不过，我们只需检查`e2`是否缺失，就能轻松修复此问题。除此之外，该函数将按原样工作。

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

### 单位示例

以一个稍微复杂的示例来说明，我们定义一个用于将物理单位与数值关联的类。这将使我们能够检查所操作的单位是否兼容——这样我们就不会将米与秒相减等——并将单位分析作为算术运算的一部分。这个示例是`units`包的简化版本。`units`包还处理单位换算和单位化简。在这里，我们仅实现符号单位的简单算术运算及其简单的相等性检查。

其思路是表示物理单位，然后将它们与数值关联起来。这里的物理单位指的是诸如平方公里、米每秒等单位。通常，这些将是符号表达式，但我们只考虑稍简单的情况，即单位是物理常数的分数。在这种情况下，我们可以将它们表示为分子项列表和分母项列表。如果我们始终保持这些列表有序，我们就有了它们的规范表示，并且可以通过检查分子和分母列表的相等性来检查两个单位是否相等。我们可以像这样实现构造函数：

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

我们可以将这些单位转换为分数形式的字符串表示，以便美观地打印输出。

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

物理量的加减运算只有在它们具有相同单位时才可能进行，但单位之间的乘除运算则始终可行。得到的单位是通过对（符号）单位执行与对数量相同的操作来获得的。为了能够处理这个问题，我们在符号单位上定义了乘法和除法。

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

现在，表示单位所需的一切都已就绪。我们只需定义用于将单位与数量关联的类。这个类与我们之前编写的`modulus`类非常相似。我们取一个（数值）值，在属性中关联一个符号单位，并设置类。

```
units <- function(value, nominator, denominator = "") {
attr(value, "units") <- symbolic_unit(nominator, denominator)
class(value) <- c("units", class(value))
value
}
```

美观打印输出遵循我们在`modulus`中看到的模式。我们需要剥离类和属性，以使用通过`NextMethod`调用的底层打印方法，但仅此而已。

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



## 处理 `units` 的运算符

处理 `units` 的运算符仅比处理 `modulus` 稍显复杂。我们需要区分两种运算符：一种要求单位匹配，另一种则需要构造新单位。前者是加法、减法和比较运算（假设我们只认为在数量和关联单位上都一致的数值相等）；后者是乘法和除法，其结果单位必须根据操作数计算得出。如何处理物理量上的逻辑运算符并不明确（如果这甚至有意义的话），因此对于不属于这两类的运算符，我们应默认使用底层类型的处理方式。

我们使用 `Ops` 组函数来实现这些运算符。在此函数内部，我们可以通过变量 `.Generic` 获取正在评估的实际运算符。这不是函数的参数，但在函数被调用时会被设置为正在评估的运算符，我们可以通过 switch 检查该运算符并进行相应处理。

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
# 对于剩余运算符，我们没有很好的方法处理单位，
# 因此移除该信息，回归到数值本身
e1 <- unclass(e1)
e2 <- unclass(e2)
attributes(e1) <- attributes(e2) <- NULL
NextMethod()
}
```

通过这种单位运算符的定义，我们可以将单位与标量结合：

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

如果我们尝试将两个类型不兼容的量相加，将会收到警告，提示这是不正确的。

```
(y <- units(1:6, "m", "s"))
## Units:  m / s
## [1] 1 2 3 4 5 6
x + y
## Error in Ops.units(x, y): Incompatible units
```

然而，当单位兼容时，我们可以进行加法和减法。

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

乘法和除法始终允许进行，结果单位由操作数推导得出。

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

