# 函数中的函数

除了你可以在自己编写的函数中使用的常规 R 代码和函数外，还存在一些特殊的函数。这些函数**仅**或**主要**在其他函数内部使用。即使你是经验丰富的 R 用户，如果之前没有编写过函数，对这些函数也可能不熟悉。

### `match.arg()`

`match.arg()` 函数对于执行参数模糊匹配非常有用。这样做还有一个好处，就是如果参数与有效选项之一（即无效）不匹配，它会抛出错误。以下代码展示了两个相似函数，区别在于其中一个使用了 `match.arg()` 函数。示例演示了模糊匹配以及使用无效参数时会发生什么：

```
f1 <- function(type = c("first", "second")) {
type
}
f2 <- function(type = c("first", "second")) {
type <- match.arg(type)
type
}
f1("fi")
[1] "fi"
f2("fi")
[1] "first"
f1("test")
[1] "test"
f2("test")
Error in match.arg(type) : 'arg' should be one of "first", "second"
```

参数匹配还有助于确保在传递文本字符串时只使用有效选项，否则会抛出一个信息丰富的错误。以下代码扩展了我们构建的未使用 `match.arg()` 的函数，当 `type = "first"` 时计算平均值，当 `type = "second"` 时计算标准差。然而，当向 `type` 参数传递无效选项时，我们会得到一个相对隐晦的错误消息，提示找不到对象 `x`：

```
f1b <- function(type = c("first", "second")) {
if (type == "first") {
x <- mean(1:5)
} else if (type == "second") {
x <- sd(1:5)
}
return(x)
}
f1b("test")
Error in f1b("test"): object 'x' not found
```

### `missing()`

另一个专门针对函数参数的函数是 `missing()`。它返回一个逻辑值，指示特定参数在函数调用中是否缺失。这在编写可能以不同方式使用的函数时很有帮助。下面是一个计算 Cohen's d 效应量的函数示例，对于单组而言，其定义为变量的平均值除以标准差。Cohen's d 也可以针对重复测量（例如干预前后测量的组）进行计算。这是通过计算两个变量的差值，然后按前述方法进行处理来完成的。我们使用 `missing()` 函数来确定用户是传递了单个变量 `x`，还是两个变量 `x` 和 `y`，从而使我们的函数能够优雅地处理单样本数据和重复测量数据的 Cohen's d 计算：

```
cohend <- function(x, y) {
if (!missing(y)) {
x <- y - x
}
mean(x) / sd(x)
}
cohend(x = c(0.61, 0.99, 1.47, 1.52, 0.45,
3.34, 1.05, -1.47, 1.3, 0.33),
y = c(-0.69, 1.6, 0.44, 1, 0.88,
1.17, 2.4, 1.21, 0.87, 2.15))
[1] 0.09522249
cohend(x = c(0.61, 0.99, 1.47, 1.52, 0.45,
3.34, 1.05, -1.47, 1.3, 0.33))
[1] 0.796495
```

### `match.call()`

同样与确定函数调用特性相关的函数是 `match.call()`。`missing()` 用于判断特定参数是否缺失，`match.arg()` 用于判断参数是否与有效选项匹配，而 `match.call()` 则捕获整个函数调用。这或许比解释更容易演示。下面的小函数计算变异系数（样本标准差除以样本均值）。它还通过使用 `match.call()` 来捕获并返回函数调用：

```
cv <- function(x, na.rm = FALSE) {
fcall <- match.call()
est <- sd(x, na.rm = na.rm) / mean(x, na.rm = na.rm)
return(list(CV = est, Call = fcall))
}
cv(1:5)
$CV
[1] 0.5270463
$Call
cv(x = 1:5)
cv(1:8, na.rm = TRUE)
$CV
[1] 0.5443311
$Call
cv(x = 1:8, na.rm = TRUE)
```

在上述两个示例的输出中，`match.call()` 精确地捕获了对函数的调用，尽管它添加了显式的参数名。有时这有助于记录特定输出是由什么调用创建的。也许最常见使用此功能的地方是 R 中回归模型的输出。例如，以下代码展示了一个线性模型，其输出中呼应了函数调用，这是通过 `match.call()` 实现的：

```
lm(mpg ∼ hp, data = mtcars)
Call:
lm(formula = mpg ∼ hp, data = mtcars)
Coefficients:
(Intercept)           hp
30.09886     -0.06823
```

### `return()`

`return()` 函数通常用在函数的末尾以返回特定对象，正如你已在前面一些示例中看到的那样，尽管没有明确讨论过。然而，`return()` 也可以在函数内部的任意点用于返回值，从而结束函数的执行。以下函数有一个 `if` 语句，如果为真，则导致函数提前终止。请注意，如果条件不为真，最终的结果甚至没有用 `return()` 包裹。R 默认返回函数中的最后一个对象，因此严格来说，显式调用 `return()` 并非必需：

```
f <- function(x) {
if (x < 4) return("I'm done!")
paste(x, "- Fin!")
}
f(10)
[1] "10 - Fin!"
f(3)
[1] "I'm done!"
```

尽管你可以在函数的前半部分使用 `return()`，但这是不鼓励的，因为它可能会让用户以及任何阅读或调试代码的人感到意外。与上述代码相同的效果可以通过使用流程控制来实现：

```
f <- function(x) {
if (x < 4) {
"I'm done!"
} else {
paste(x, "- Fin!")
}
}
f(10)
[1] "10 - Fin!"
f(3)
[1] "I'm done!"
```

除了不在函数中途使用 `return()` 之外，一些人还认为在函数末尾也不应使用显式的 `return()` 调用，因为它没有必要。这仍然是一个偏好问题，因为它有助于将注意力引向函数末尾确切返回的内容。

### `invisible()`

虽然 `invisible()` 函数并非仅在函数内部使用，但它经常与函数返回的对象一起使用。之前我们创建了一个计算变异系数并返回函数调用的函数。我们可以修改该函数，使其打印变异系数并隐式地返回其余部分，如以下代码所示。使用 `invisible()` 意味着即使函数返回了与之前相同的对象，该对象也不会显示。`invisible()` 函数可能最常用于设计用来创建美观输出的函数，例如调用 `summary()` 或绘图函数。这些函数的主要目的是显示摘要或图表，但以防有人想要或需要编辑该对象，实际对象会隐式返回，因此可以被捕获并保存以供后续使用：

```
cv <- function(x, na.rm = FALSE) {
fcall <- match.call()
est <- sd(x, na.rm = na.rm) / mean(x, na.rm = na.rm)
print(est)
return(invisible(list(CV = est, Call = fcall)))
}
cv(1:8, na.rm = TRUE)
[1] 0.5443311
res <- cv(1:8, na.rm = TRUE)
[1] 0.5443311
res$Call
cv(x = 1:8, na.rm = TRUE)
```