# 在 `f1b("test")` 中出错：未找到对象 `'x'`

另一个专门用于函数参数的函数是 `missing()`。它返回一个逻辑值，用于指示函数调用中是否缺少某个特定参数。这在编写可能以不同方式使用的函数时很有帮助。下面是一个计算*科恩 d* 效应量的函数示例。对于单组数据，科恩 d 定义为变量的均值除以标准差。科恩 d 也可以用于重复测量数据，例如在干预前后对同一组进行测量。其计算方法为先计算两个变量的差值，然后按之前的步骤进行。我们使用 `missing()` 函数来判断用户传递的是单个变量 `x`，还是两个变量 `x` 和 `y`，这样我们的函数就能优雅地处理单样本和重复测量数据的科恩 d 计算：

```
cohend <- function(x, y) {
  if (!missing(y)) {
    x <- y - x
  }
  mean(x) / sd(x)
}
```

```
cohend(x = c(0.61, 0.99, 1.47, 1.52, 0.45, 3.34, 1.05, -1.47, 1.3, 0.33),
       y = c(-0.69, 1.6, 0.44, 1, 0.88, 1.17, 2.4, 1.21, 0.87, 2.15))
## [1] 0.095
```

```
cohend(x = c(0.61, 0.99, 1.47, 1.52, 0.45, 3.34, 1.05, -1.47, 1.3, 0.33))
## [1] 0.8
```

与确定函数调用特性相关的另一个函数是 `match.call()`。`missing()` 确定特定参数是否缺失，`match.arg()` 确定参数是否匹配有效选项之一，而 `match.call()` 则捕获整个函数调用。这可能演示起来比解释更容易。下面这个小型函数计算变异系数，即样本标准差除以样本均值。它还通过使用 `match.call()` 捕获并返回函数调用：

```
cv <- function(x, na.rm = FALSE) {
  fcall <- match.call()
  est <- sd(x, na.rm = na.rm) / mean(x, na.rm = na.rm)
  return(list(CV = est, Call = fcall))
}
```

```
cv(1:5)
## $CV
## [1] 0.53
##
## $Call
## cv(x = 1:5)
```

```
cv(1:8, na.rm = TRUE)
## $CV
## [1] 0.54
##
## $Call
## cv(x = 1:8, na.rm = TRUE)
```

在这两个示例的输出中，`match.call()` 精确地捕获了对函数的调用，尽管它添加了显式的参数名称。这对于记录生成特定输出的确切调用有时非常有用。也许最常见的应用场景是在 R 语言回归模型的输出中。

例如，以下代码展示了一个线性模型，其输出中会回显函数调用，这是通过使用 `match.call()` 实现的：

```
lm(formula = mpg ~ hp, data = mtcars)
##
## Call:
## lm(formula = mpg ~ hp, data = mtcars)
##
## Coefficients:
## (Intercept)         hp
##    30.0989     -0.0682
```

`return()` 函数通常用于函数末尾以返回特定对象，正如你在之前的一些示例中已经看到的那样，尽管没有明确讨论。然而，`return()` 也可以用于从函数内部的任何点返回值，从而终止函数的执行。下面的函数包含一个 `if` 语句，当条件为 TRUE 时，会导致函数提前终止。请注意，当条件不为真时，最终结果甚至没有用 `return()` 包裹。R 默认返回函数中的最后一个对象，因此并非严格需要显式调用 `return()`：

```
f <- function(x) {
  if (x < 4) return("结束了！")
  paste(x, "- 完成！")
}
```

```
f(10)
## [1] "10 - 完成！"
```

```
f(3)
## [1] "结束了！"
```

尽管你可以在函数早期使用 `return()`，但不鼓励这样做，因为这可能会让用户以及其他任何阅读或调试代码的人感到意外。上述代码的效果可以通过使用流程控制来实现：

```
f <- function(x) {
  if (x < 4) {
    "结束了！"
  } else {
    paste(x, "- 完成！")
  }
}
```

```
f(10)
## [1] "10 - 完成！"
```

```
f(3)
## [1] "结束了！"
```

除了不在函数中途使用 `return()` 之外，有些人认为也不应该在函数末尾使用显式的 `return()` 调用，因为它不是必需的。这仍属于个人偏好问题，因为使用它可以有助于提醒注意函数末尾到底返回了什么。

虽然并非专用于函数，但 `invisible()` 函数经常与函数返回的对象一起使用。之前我们创建了一个计算变异系数并返回函数调用的函数。我们可以修改该函数，使其打印变异系数并不可见地返回其余部分，如下代码所示。使用 `invisible()` 意味着，即使函数返回了与之前相同的对象，该对象也不会显示。`invisible()` 函数可能最常用于旨在创建美观输出的函数中，例如对 `summary()` 或绘图函数的调用。函数的主要目的是显示摘要或图形，但以防有人需要或想要编辑该对象，实际的对象会被不可见地返回，因此可以被捕获并保存以备后用：

```
cv <- function(x, na.rm = FALSE) {
  fcall <- match.call()
  est <- sd(x, na.rm = na.rm) / mean(x, na.rm = na.rm)
  print(est)
  return(invisible(list(CV = est, Call = fcall)))
}
```

```
cv(1:8, na.rm = TRUE)
## [1] 0.54
```

```
res <- cv(1:8, na.rm = TRUE)
## [1] 0.54
```

```
res$Call
## cv(x = 1:8, na.rm = TRUE)
```

`on.exit()` 函数可用于保证在函数退出或完成时执行一组特定的命令。即使函数发生错误或未按预期正常完成，`on.exit()` 中的表达式也会被执行。下面的代码中的这个小函数的下一个用例展示了这一点。一个错误导致函数终止，一旦发生这种情况，`on.exit()` 中的表达式就会被执行。当函数修改其自身之外的任何值时，使用 `on.exit()` 尤其有价值。例如，有时绘图函数会修改默认绘图参数，并在完成时将它们恢复到原始状态。使用 `on.exit()` 可以确保即使出现问题且函数失败或出错，用户仍然拥有所有原始设置：

```
f <- function(x) {
  on.exit(print("游戏结束"))
  x + 5
}
```

```
f(3)
## [1] "游戏结束"
## [1] 8
```

```
f("a")
## Error in x + 5: non-numeric argument to binary operator
```



## [1] "游戏结束"

我们介绍的最后一组特定功能函数与向软件或用户发送信号有关。按严重程度排序，它们分别是 `stop()`、`warning()` 和 `message()`。接下来的例子稍微更贴近实际，它会计算一个变量的均值，该均值既可以在原始尺度上计算，也可以在转换后的尺度上计算，然后再反向转换回均值。对数尺度对异常值的抵抗力相对更强。如果使用了对数变换，示例代码会通过 `stop()` 函数检查变量是否包含任何负值，这些负值未定义且会导致完全错误。传递给 `stop()` 的参数是要显示的错误信息。`warning()` 的处理过程类似，同样需要传递要在警告中显示的信息。警告与错误不同。通过 `stop()` 触发的错误会导致函数停止评估并终止。警告则在函数结束时发出，但函数仍允许继续评估。最后，`message()` 可用于向用户发送消息或信号，但与警告不同，它不表示存在真实或潜在的问题。虽然未在示例中展示，但有一个相关的便捷函数 `stopifnot()`，它允许传入一个逻辑表达式，如果表达式计算结果不为真，则会发出错误。尽管此选项具有节省代码量的优点，但缺点是您无法编写自定义错误信息来明确指出具体出了什么问题。

使用警告或消息，而不仅仅是调用 `print()` 或 `cat()` 让函数打印消息，是有益的，原因之一是警告和消息可以通过使用命名恰当的函数 `suppressWarnings()` 和 `suppressMessages()` 来（可选地）抑制。所有这些函数均在紧随其后的代码中演示：

```
f <- function(x, trans = c("identity", "log")) {
  trans <- match.arg(trans)
  if (trans == "log") {
    if (any(x < 0)) stop("Log is not defined for negative values")
    if (any(x < 1e-16)) warning("Some x values close or equal to zero, results may be unstable")
    x <- log(x)
    message("x successfully log transformed")
    exp(mean(x))
  } else {
    mean(x)
  }
}
```

```
f(c(1, 2, 100))
## [1] 34
```

```
f(c(1, 2, 100), trans = "log")
## x successfully log transformed
## [1] 5.8
```

```
suppressMessages(f(c(1, 2, 100), trans = "log"))
## [1] 5.8
```

```
f(c(0, 1, 2, 100), trans = "log")
## Warning in f(c(0, 1, 2, 100), trans = "log"): Some x values close or equal to zero, results may be
## x successfully log transformed
## [1] 0
```

```
suppressWarnings(f(c(0, 1, 2, 100), trans = "log"))
## x successfully log transformed
## [1] 0
```

```
f(c(-1, 1, 2, 100), trans = "log")
## Error in f(c(-1, 1, 2, 100), trans = "log"): Log is not defined for negative values
```



## `f(c(-1, 1, 2, 100), trans = "log")` 中的错误：负数无法计算对数

### 4.4 调试

到目前为止演示的函数要么运行正常，要么包含易于发现的刻意错误。有时，查找错误（即调试所写代码和函数）的过程会花费更长时间。幸运的是，有一些工具可以帮助我们进行调试，也有一些实践方法可以缩小问题范围。

虽然调试适用于任何代码而不仅仅是函数，但如果没有额外的工具，函数调试可能会特别棘手，因为通常情况下，函数的所有代码都会不间断地执行，我们无法中途查看正在发生的事情。

在本节中，我们将编写一个函数，该函数使用一个公式，通过 `tapply()`（你在第 3 章中学习过）来计算一个变量在另一个变量不同水平上的均值；然后，该函数使用以下代码绘制原始数据，并用更大的点表示均值，如图 4-1 所示。

```
meanPlot <- function(formula, d) {
  v <- all.vars(formula)
  m <- tapply(d[, v[1]], d[, v[2]],
              FUN = mean, na.rm = TRUE)
  plot(formula, data = d, type = "p")
  points(x = unique(d[, v[2]]), y = m,
         col = "blue", pch = 16, cex = 2)
}
meanPlot(mpg ~ cyl, d = mtcars)
```

**图 4-1.** 尚未完善的 `meanPlot()`

这些均值看起来有些不正确。当 `cyl = 8` 时，点的均值看起来没问题；然而，其他两个均值似乎落在了极端值上。`debug()` 函数允许我们调试一个函数，并单步执行每一行代码。

要使用它，我们首先在要调试的函数上调用 `debug()`：

```
debug(meanPlot)
```

然后，当我们使用原始函数时，它会触发 R 进入调试模式。在第一步，R 会通过进入调试模式告知我们正在调试的函数调用及其位置。这会将控制台从 `>` 变为 `Browse[2]>`，调试命令包括使用 `ENTER`（或 `RETURN`）键逐步执行，或使用 `Q` 退出调试（这在调试较长的函数时很有帮助）。此外，由于调试是一次一步地执行函数的操作，我们可以在控制台运行代码来查看变量在不同阶段的状态。由于 `debug` 的交互特性，最好在自己的计算机上实时操作——不过我们会在这里解释你会看到什么。我们确实在图 4-2 中包含了一个你可能看到的输出示例。

运行以下代码行 `meanPlot(mpg ~ cyl, d = mtcars)` 后，调试模式启动，如图 4-2 中阶段 1 所示，由 `debug at #1` 指示。此外，在 RStudio 中，环境窗口会聚焦于 `meanPlot()`。继续在 `Browse[2]>` 处按 `ENTER` 或 `RETURN`，直到到达阶段 7 或附近，此时出现 `debug at #7: points(x = unique(d[, v[2]]), y = m, col = "blue", pch = 16, cex = 2)` —— 这就是均值点添加到图中的步骤。在该阶段，检查传入点图的变量。通过运行 `Browse[2]> unique(d[, v[2]])` 来完成此操作。这表明 `x` 轴变量的顺序是错误的！在看到函数代码中的这个错误后，在调试浏览器中输入大写 `Q` 以退出调试模式：

**图 4-2.** `debug()` 的截图

```
meanPlot(mpg ~ cyl, d = mtcars)
```

我们现在知道问题出在哪里，并通过 `Q` + `ENTER` 退出了调试模式。我们通过首先对数据进行排序来修复函数，以便 `tapply()` 和 `unique()` 以相同的顺序给出结果。代码如下所示，结果如图 4-3 所示。

```
meanPlot <- function(formula, d) {
  v <- all.vars(formula)
  d <- d[order(d[, v[2]]), ] # 此行通过先排序进行了修复！
  m <- tapply(d[, v[1]], d[, v[2]],
              FUN = mean, na.rm = TRUE)
  plot(formula, data = d, type = "p")
  points(x = unique(d[, v[2]]), y = m,
         col = "blue", pch = 16, cex = 2)
}
meanPlot(mpg ~ cyl, d = mtcars)
```

如果你在调试函数时不想单步执行每一行代码，可以将 `browser()` 函数插入到函数代码中。然后，当函数执行到该点时，会调用一个浏览器，你可以检查函数局部环境中变量的当前状态。在以下示例代码中，我们在函数中添加了对 `browser()` 的调用。运行 `meanPlot(mpg ~ cyl, d = mtcars)` 后，在随后出现的浏览器中，我们按一次 `ENTER`，然后使用 `ls()` 检查当前可用的对象，接着查看对象 `v` 的内容，最后输入大写 `Q` 退出调试。由于浏览器是交互式的，我们在图 4-4 中显示了控制台的大致输出。

```
meanPlot <- function(formula, d) {
  v <- all.vars(formula)
  d <- d[order(d[, v[2]]), ] ## 先排序
  m <- tapply(d[, v[1]], d[, v[2]],
              FUN = mean, na.rm = TRUE)
  browser()
  plot(formula, data = d, type = "p")
  points(x = unique(d[, v[2]]), y = m,
         col = "blue", pch = 16, cex = 2)
}
meanPlot(mpg ~ cyl, d = mtcars)
```

**图 4-3.** 现已完善的 `meanPlot()`

**图 4-4.** `browser()` 的截图

在交互式调试时，一个有用的函数是 `traceback()`。例如，如果你调用一个函数（如 `lm()`）然后收到一条错误消息，有时很难确切知道错误发生的位置或原因。该错误通常甚至不是直接来自你调用的函数，因为你经常使用的函数可能会在内部调用许多其他函数。以下示例展示了如何在导致错误的代码之后立即使用 `traceback()` 来做到这一点。输出显示了调用堆栈，追踪了从初始调用到生成错误的最终代码的路径。如果你需要查看代码以确定错误发生的原因，这些信息可能很有用：

```
lm(mpg ~ jack, data = mtcars)
## Error in eval(predvars, data, env): object 'jack' not found

traceback()
## 7: eval(predvars, data, env)
## 6: eval(predvars, data, env)
## 5: model.frame.default(formula = mpg ~ jack, data = mtcars, drop.unused.levels = TRUE)
## 4: stats::model.frame(formula = mpg ~ jack, data = mtcars, drop.unused.levels = TRUE)
## 3: eval(mf, parent.frame())
## 2: eval(mf, parent.frame())
```



### 1： `lm(mpg ˜ jack, data = mtcars)`

在这个例子中，错误产生的原因是 `jack` 不是 `mtcars` 数据框的列名。

## 第 4 章 编写函数

最后，有时代码中的错误并不在你自己的代码里，而在于你使用的其他代码，例如来自另一个 R 包。虽然在官方推荐的 R 包中发现错误的情况很少见，但在成千上万个其他 R 包中，这种情况则更为普遍。此外，有时问题本身并非真正的 bug，而是你使用函数的方式与原始作者设想的使用方式之间存在差异。尽管 CRAN 上所有 R 包的源代码都公开可下载和编辑，但为了验证仅仅修改某个函数中的一个小细节能否解决问题，就去下载整个包的源代码、进行编辑并重新安装，这可能会非常麻烦。

请思考下面的挑战。假设你使用的数据中，出于某些原因偶尔会包含无穷值，而你想只保留有限值的情况。下面的代码展示了使用 `Hmisc` 包中的 `wtd.quantile()` 函数的一个示例：

```
wtd.quantile(c(1, 2, 3, Inf, NA),
             weights = c(.6, .9, .4, .2, .6))
```

## *0% 25% 50% 75% 100%*

## **NaN Inf Inf Inf Inf**

查看 `wtd.quantile()` 的代码，我们发现其主要计算由另一个函数 `wtd.table()` 完成，如下所示：

```
wtd.quantile
## function (x, weights = NULL, probs = c(0, 0.25, 0.5, 0.75, 1),
##           type = c("quantile", "(i-1)/(n-1)", "i/(n+1)", "i/n"), normwt = FALSE,
##           na.rm = TRUE)
## {
##     if (!length(weights))
##         return(quantile(x, probs = probs, na.rm = na.rm))
##     type <- match.arg(type)
##     if (any(probs < 0 | probs > 1))
##         stop("Probabilities must be between 0 and 1 inclusive")
##     nams <- paste(format(round(probs * 100, if (length(probs) >
##         1) 2 - log10(diff(range(probs))) else 2)), "%", sep = "")
##     i <- is.na(weights) | weights == 0
##     if (any(i)) {
##         x <- x[!i]
##         weights <- weights[!i]
##     }
##     if (type == "quantile") {
##         w <- wtd.table(x, weights, na.rm = na.rm, normwt = normwt,
##             type = "list")
##         x <- w$x
##         wts <- w$sum.of.weights
##         n <- sum(wts)
##         order <- 1 + (n - 1) * probs
##         low <- pmax(floor(order), 1)
##         high <- pmin(low + 1, n)
##         order <- order%%1
##         allq <- approx(cumsum(wts), x, xout = c(low, high), method = "constant",
##             f = 1, rule = 2)$y
##         k <- length(probs)
##         quantiles <- (1 - order) * allq[1:k] + order * allq[-(1:k)]
##         names(quantiles) <- nams
##         return(quantiles)
##     }
##     w <- wtd.Ecdf(x, weights, na.rm = na.rm, type = type, normwt = normwt)
##     structure(approx(w$ecdf, w$x, xout = probs, rule = 2)$y,
##         names = nams)
## }
## <bytecode: 0x000002ac904e4750>
## <environment: namespace:Hmisc>
```

采用同样的方法，我们来检查 `wtd.table()` 函数。在检查过程中，我们发现虽然它能自动移除缺失值，但对于非有限值却没有检查或移除的方法：

```
wtd.table
## function (x, weights = NULL, type = c("list", "table"), normwt = FALSE,
##     na.rm = TRUE)
## {
##     type <- match.arg(type)
##     if (!length(weights))
##         weights <- rep(1, length(x))
##     isdate <- testDateTime(x)
##     ax <- attributes(x)
##     ax$names <- NULL
##     if (is.character(x))
##         x <- as.factor(x)
##     lev <- levels(x)
##     x <- unclass(x)
##     if (na.rm) {
##         s <- !is.na(x + weights)
##         x <- x[s, drop = FALSE]
##         weights <- weights[s]
##     }
##     n <- length(x)
##     if (normwt)
##         weights <- weights * length(x)/sum(weights)
##     i <- order(x)
##     x <- x[i]
##     weights <- weights[i]
##     if (anyDuplicated(x)) {
##         weights <- tapply(weights, x, sum)
##         if (length(lev)) {
##             levused <- lev[sort(unique(x))]
##             if ((length(weights) > length(levused)) && any(is.na(weights)))
##                 weights <- weights[!is.na(weights)]
##             if (length(weights) != length(levused))
##                 stop("program logic error")
##             names(weights) <- levused
##         }
##         if (!length(names(weights)))
##             stop("program logic error")
##         if (type == "table")
##             return(weights)
##         x <- all.is.numeric(names(weights), "vector") 86
##         if (isdate)
##             attributes(x) <- c(attributes(x), ax)
##         names(weights) <- NULL
##         return(list(x = x, sum.of.weights = weights))
##     }
##     xx <- x
##     if (isdate)
##         attributes(xx) <- c(attributes(xx), ax)
##     if (type == "list")
##         list(x = if (length(lev)) lev[x] else xx, sum.of.weights = weights)
##     else {
##         names(weights) <- if (length(lev))
##             lev[x]
##         else xx
##         weights
##     }
## }
## <bytecode: 0x000002ac904f2cc8>
## <environment: namespace:Hmisc>
```

也许直接修改数据看起来更简单，但有时数据是自动生成并传递过来的，因此修改函数可能比修改数据更简便。我们可以直接将 `wtd.table()` 的代码复制粘贴到编辑器中并进行修改，如下所示：

```
revised.wtd.table <- function (x, weights = NULL, type = c("list",
    "table"), normwt = FALSE, na.rm = TRUE)
{
    type <- match.arg(type)
    if (!length(weights))
        weights <- rep(1, length(x))
    isdate <- testDateTime(x)
    ax <- attributes(x)
    ax$names <- NULL
    if (is.character(x))
        x <- as.factor(x)
    lev <- levels(x)
    x <- unclass(x)
    if (na.rm) {
        s <- !is.na(x + weights) & is.finite(x + weights) ##此处为修复点
        x <- x[s, drop = FALSE]
        weights <- weights[s]
    }
    n <- length(x)
    if (normwt)
        weights <- weights * length(x)/sum(weights)
    i <- order(x)
    x <- x[i]
    weights <- weights[i]
    if (anyDuplicated(x)) {
        weights <- tapply(weights, x, sum)
        if (length(lev)) {
            levused <- lev[sort(unique(x))]
            if ((length(weights) > length(levused)) && any(is.na(weights)))
                weights <- weights[!is.na(weights)]
            if (length(weights) != length(levused))
                stop("program logic error")
            names(weights) <- levused
        }
        if (!length(names(weights)))
            stop("program logic error")
        if (type == "table")
            return(weights)
        x <- all.is.numeric(names(weights), "vector")
        if (isdate)
            attributes(x) <- c(attributes(x), ax)
        names(weights) <- NULL
        return(list(x = x, sum.of.weights = weights))
    }
    xx <- x
    if (isdate)
        attributes(xx) <- c(attributes(xx), ax)
    if (type == "list")
        list(x = if (length(lev)) lev[x] else xx, sum.of.weights = weights)
    else {
        names(weights) <- if (length(lev))
            lev[x]
        else xx
        weights
    }
}
```

与原始的 `wtd.table()` 函数不同，修改后的函数完全按照我们的预期工作：

```
wtd.table(c(1, 2, 3, Inf, NA),
          weights = c(.6, .9, .4, .2, .6))
## $x
## [1] 1 2 3 Inf
##
## $sum.of.weights
## [1] 0.6 0.9 0.4 0.2

revised.wtd.table(c(1, 2, 3, Inf, NA),
                  weights = c(.6, .9, .4, .2, .6))
## $x
## [1] 1 2 3
##
## $sum.of.weights
```



## [1] 0.6 0.9 0.4

然而，挑战在于`wtd.quantile()`函数；而其他使用`wtd.table()`的 Hmisc 函数仍然无法按预期工作，因为它们并未调用我们修改后的函数。在全局环境中将函数赋值给`wtd.table()`这一名称是不够的，因为作用域规则意味着 Hmisc 函数会优先从 Hmisc 环境（而非我们的全局环境）中查找`wtd.table()`函数。这就像你的电脑上有两个同名文件，但位于不同文件夹中。为了使所有使用`wtd.table()`的 Hmisc 函数都能调用我们修改后的函数，我们不仅需要正确命名它，还需要将其放置在正确的位置。我们可以通过`assignInNamespace()`函数将对象分配到特定命名空间：

## 第 4 章 编写函数

```
assignInNamespace(x = "wtd.table", #使用其他所有 Hmisc 函数中预期的名称
                  value = revised.wtd.table, #强制使用我们修改后的函数
                  ns = "Hmisc") #在 Hmisc 的命名空间中
```

```
wtd.quantile(c(1, 2, 3, Inf, NA),
             weights = c(.6, .9, .4, .2, .6))
```

## *0% 25% 50% 75% 100%*

## 2.0 2.2 2.4 2.7 2.9

```
wtd.Ecdf(c(1, 2, 3, Inf, NA),
         weights = c(.6, .9, .4, .2, .6))
```

## $x

## [1] 1 1 2 3

##

## $ecdf



[1] 0.00 0.32 0.79 1.00

尽管 `assignInNamespace()` 函数没有输出，但我们可以发现，在此之后，`wtd.quantile()` 函数以及依赖 `wtd.table()` 的其他函数（如 `wtd.Ecdf()`）都按我们预期的方式正常运行了。关于 `assignInNamespace()` 有一个注意事项：你不能将任何对象分配给该命名空间中尚不存在的对象（即，你只能覆盖已存在的对象）。此外，在任何你可能想要提交到 CRAN 的包中，都不允许使用 `assignInNamespace()`（更多内容见第 6 章）。即使这种方法有时很方便，但如果大家都将其作为常规做法，就会造成混乱。如果出现这种情况，包 A 中函数的定义将取决于你是否加载了包 B，因为包 B 可以覆盖（不仅仅是屏蔽）包 A 中的函数定义。请注意，你通过这种方式所做的任何更改都是临时性的，当你重启 R 时会消失。然而，这可以成为调试现有包的一个有用技术，因为这是一种相对简单的方法，可以确保使用 `wtd.quantile()` 时遇到的问题确实通过建议对 `wtd.table()` 的修改而得到了“修复”。此时，如果这确实是一个错误，或者仅仅是一个期望的功能，你可以自信地给包维护者发送邮件，建议进行修改，因为你的建议代码是可行的。若要查看当前的包维护者，运行 `maintainer("Hmisc")`，并将 `Hmisc` 替换为你感兴趣的包名称即可。

