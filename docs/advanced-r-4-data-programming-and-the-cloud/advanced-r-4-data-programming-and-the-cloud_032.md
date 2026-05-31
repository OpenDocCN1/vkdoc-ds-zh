# 3 5 4 Third

## 第 5 章 编写类和方法

在 S3 系统中，没有正式的方法来创建特定类的对象。

然而，创建特定类对象最常见的方式是作为函数的输出。可以编写专用函数来创建特定类的对象，也可以编写执行操作并以特定类对象形式输出结果的函数。在 R 语言中使用 S3 系统时，后一种方法更为典型。

当您创建新类时，所需的某些特征或元素可能已存在于现有类中。如果是这种情况，那么在现有类的基础上进行构建或扩展可能是合理的。例如，数据框是在列表的基础上构建的，要求列表的每个元素都是长度相同的向量。同样，我们之前创建的 `textplot` 类是在数据框的基础上构建的，需要三个分别名为 `x`、`y` 和 `labels` 的元素。在这种情况下，我们说 `textplot` *继承* 自 `data.frame`。也就是说，`textplot` 是父类 `data.frame` 的子类（注意，父类也被称为*超类*或*基类*）。

如果一个类仅继承自另一个类，则称为单继承（即只有一个父类）。如果一个类继承自多个类，则称为多继承（即拥有多个父类）。如果一个类继承自另一个类，它可能继承数据的存储特征，也可能继承方法，即对特定类对象进行操作的函数。继承方法对于避免重复工作尤其有用。

**注意**：继承是指在现有类的特征和方法基础上创建新类。新类称为子类，而派生新类的类称为父类或超类。

子类既可以继承父类的特征，也可以使用父类的方法。

在下一个示例中，我们编写一个函数，利用公式接口从数据框构建一个 `textplot` 对象。该函数有两个参数：一个名为 `f` 的公式，以及一个名为 `d` 的数据。代码的第一部分使用第 4 章介绍的 `stopifnot()` 函数来检查传递给函数的对象类是否与预期相符。为了测试类，我们使用 `inherits()` 函数，该函数评估特定对象是否属于指定类，或是否继承自指定类。例如，我们的 `textplot` 类其次是一个 `data.frame`，因此使用 `inherits(object, "data.frame")` 进行测试会返回 `TRUE`。当我们构建 S4 类时，会看到更正式的类继承定义和系统。函数的下一部分按顺序从指定的数据框中获取公式中的所有变量，重命名列，应用我们的 `textplot` 类，并返回该对象：

```r
textplot_data <- function(f, d) {
  stopifnot(inherits(d, "data.frame"))
  stopifnot(inherits(f, "formula"))
  newdata <- get_all_vars(formula = f, data = d)
  colnames(newdata) <- c("y", "x", "labels")
  class(newdata) <- c("textplot", "data.frame")
  return(newdata)
}

textplot_data(f = mpg ~ hp | cyl, d = mtcars[1:10, ])
##                    y  x labels
## Mazda RX4         21 110      6
## Mazda RX4 Wag     21 110      6
## Datsun 710        23  93      4
## Hornet 4 Drive    21 110      6
## Hornet Sportabout 19 175      8
## Valiant           18 105      6
## Duster 360        14 245      8
## Merc 240D         24  62      4
## Merc 230          23  95      4
## Merc 280          19 123      6
```

这些示例展示了使用 S3 系统创建类是多么容易。接下来，我们将探讨如何为现有类或新类编写方法。

### S3 方法

方法是可以对特定类对象执行的函数或操作。

即使您不编写自己的类，也可以编写自己的方法。编写 S3 方法就像我们在第 4 章中编写函数一样。唯一的区别是 S3 方法有特殊的命名约定，并且需要一个供用户使用的泛型函数，该函数负责将调用分派到适当的方法。

**注意**：S3 方法是遵循特定命名约定的常规 R 函数：`FunctionVerb.classname()`。用户调用泛型函数 `FunctionVerb()`，该函数根据作为参数传入的对象的类，将调用分派到适当的方法。如果不存在泛型函数，则必须编写一个包含对 `UseMethods()` 调用的泛型函数，后者处理实际的方法分派。

首先，我们为我们开发的 `textplot` 对象类编写一个简单的绘图方法。为了创建 S3 绘图方法，我们使用函数动词 `plot`，后跟类名 `textplot`，在本例中即为 `plot.textplot()`。只要我们的函数以此方式命名，并且存在一个泛型 `plot()` 函数（我们稍后将讨论此主题），它就能作为 S3 方法工作。

我们的绘图函数如下代码所示。调用 `par()` 函数来调整图形的默认边距，以减少图形顶部和右侧的多余空白区域。结果存储在对象 `op` 中，因为它保存了原始的图形参数。然后，在函数退出时，可以通过调用 `par(op)` 来恢复用户原始的图形参数状态，这是您在第 4 章中学到的函数。接下来，我们通过调用 `plot.new()` 创建一个新的绘图区域，并通过调用 `plot.window()` 来设置新绘图的尺寸，其 `x` 和 `y` 的限制由数据的 `range` 决定。

然后，我们使用 `text()` 函数绘制标签，该函数接收坐标和标签。最后，我们在底部（`side = 1`）和左侧（`side = 2`）添加坐标轴。原始的 `textplot` 数据对象在函数末尾以不可见的方式返回：

```r
#### S3 System - Methods ####

plot.textplot <- function(d) {
  ## adjust the margins through a call to the
  ## graphical parameters, and store old parameters in op
  op <- par(mar = c(4, 4, 1, 1))
  ## ensure graphing parameters restored to what
  ## they were when function completes
  on.exit(par(op))
  ## create a new plot area
  plot.new()
  ## size the plot appropriate for the data
  plot.window(xlim = range(d$x, na.rm = TRUE),
              ylim = range(d$y, na.rm = TRUE))
  ## add the text to the plot
  text(d$x, d$y, labels = d$labels)
  ## create some axes to show the limits of the data
  axis(side = 1, range(d$x, na.rm = TRUE))
  axis(side = 2, range(d$y, na.rm = TRUE))
}
```



## 隐式返回对象`invisible(d)`

接下来，我们需要生成一些数据并用`plot()`绘图。注意，由于这是一种方法，我们无需通过完整的函数名称`plot.textplot()`来调用它，只需直接调用`plot()`，R 便会根据对象类`textplot`自动将数据对象分派到正确的方法。结果如图 5-1 所示，代码如下：

```
dat <- textplot_data(f = mpg ~ hp | cyl, d = mtcars[1:10, ])
plot(dat)
```

此示例展示了使用 S3 系统是多么简单。几乎不需要特殊函数或额外的工作。只需按照你的意愿创建一个常规的 R 对象，添加自定义类标签，编写一个函数，并为其指定一个特殊的名称即可。这基本上就是所有需要做的。不过，S3 方法确实有一些特殊的函数和工具。首先，我们来看看查看`plot()`的源代码时会发生什么。这个泛型函数只包含三个参数。

**第 5 章 编写类和方法**

.424 4

.314

***图 5-1.** 在特定坐标上绘制文本标签，演示了为自定义类使用自定义方法*

函数体仅包含对`UseMethod()`的调用，该调用告诉 R 检查参数类并分派到合适的方法。如果你在编写方法，而泛型函数尚不存在，那么你也需要编写该泛型函数。

在 S3 系统中编写泛型函数是一项直接的任务，只需要考虑包含默认参数即可：

```
plot
```

## [1] 5

要查看可用的方法，我们使用`methods()`函数。结果显示`plot()`有许多特定方法可用，包括我们新编写的`plot.textplot`：

```
methods(plot)
```

## [1] plot,ANY-method plot,color-method
## [3] plot.aareg* plot.acf*
## [5] plot.agnes* plot.areg*
## [7] plot.areg.boot* plot.aregImpute*
## [9] plot.biVar* plot.clusGap*
## [11] plot.cox.zph* plot.curveRep*
## [13] plot.data.frame* plot.decomposed.ts*
## [15] plot.default plot.dendrogram*
## [17] plot.density* plot.describe*
## [19] plot.diana* plot.drawPlot*
## [21] plot.ecdf plot.factor*
## [23] plot.formula* plot.function*
## [25] plot.gbayes* plot.ggplot*
## [27] plot.gtable* plot.hcl_palettes*
## [29] plot.hclust* plot.histogram*
## [31] plot.HoltWinters* plot.isoreg*
## [33] plot.lm* plot.medpolish*
## [35] plot.mlm* plot.mona*
## [37] plot.partition* plot.ppr*
## [39] plot.prcomp* plot.princomp*
## [41] plot.profile.nls* plot.Quantile2*
## [43] plot.R6* plot.raster*
## [45] plot.rm.boot* plot.rpart*
## [47] plot.shingle* plot.silhouette*
## [49] plot.spec* plot.spline*
## [51] plot.stepfun plot.stl*
## [53] plot.summary.formula.response* plot.summary.formula.reverse*
## [55] plot.summaryM* plot.summaryP*
## [57] plot.summaryS* plot.Surv*
## [59] plot.survfit* plot.table*
## [61] plot.textplot plot.trans*
## [63] plot.transcan* plot.trellis*
## [65] plot.ts plot.tskernel*
## [67] plot.TukeyHSD* plot.varclus*
## [69] plot.xyVector*

## 查看 '?methods' 获取帮助和源代码

部分函数后面带有星号，表示这些方法非公开或无法直接访问，例如`plot.table()`。举个例子，如果我们输入该函数名，会得到“无法找到”的错误：

```
plot.table
```

## Error in eval(expr, envir, enclos): object 'plot.table' not found

**第 5 章 编写类和方法**

**注意**：`::`运算符用于引用特定包中公开导出的函数，主要用于多个包导出同名函数的情况：`package::FunctionVerb()`。`:::`运算符用于访问包命名空间中未导出的函数。请谨慎使用非公开函数，因为它们可能在没有通知的情况下更改。

未公开或未从包命名空间导出的函数仍然可以作为方法访问。这些函数也可以通过指定其来源包并使用`:::`运算符直接访问，从而显示函数的源代码：

```
graphics:::plot.table
```

## function (x, type = "h", ylim = c(0, max(x)), lwd = 2, xlab = NULL,
## ylab = NULL, frame.plot = is.num, ...)
## {
## xnam <- deparse1(substitute(x))
## rnk <- length(dim(x))
## if (rnk == 0L)
## stop("invalid table 'x'")
## if (rnk == 1L) {
## dn <- dimnames(x)
## nx <- dn[[1L]]
## if (is.null(xlab))
## xlab <- names(dn)
## if (is.null(xlab))
## xlab <- ""
## if (is.null(ylab))
## ylab <- xnam
## is.num <- suppressWarnings(! any(is.na(xx <- as.numeric(nx))))
## x0 <- if (is.num)
## xx
## else seq_along(x)
## plot(x0, unclass(x), type = type, ylim = ylim, xlab = xlab,
## ylab = ylab, frame.plot = frame.plot, lwd = lwd,
## ..., xaxt = "n")
## localaxis <- function(..., col, bg, pch, cex, lty, log) axis(...)
## if (!isFALSE(list(...)$axes))
## localaxis(1, at = x0, labels = nx, ...)
## }
## else {
## if (length(dots <- list(...)) && !is.null(dots$main))
## mosaicplot(x, xlab = xlab, ylab = ylab, ...)
## else mosaicplot(x, xlab = xlab, ylab = ylab, main = xnam,
## ...)
## }
## }
## <bytecode: 0x000002ac901d97f8>
## <environment: namespace:graphics>

除了为新类编写方法外，有时为现有类编写方法也很有帮助。例如，流行的`ggplot2`包没有用于处理线性模型或回归对象的默认方法。首先，我们使用`mtcars`数据建立一个简单的回归模型。然后，我们通过调用`factor()`预测`mpg`，预测变量包括`hp`、`vs`、它们的交互项（由`hp * vs`生成，展开后包括两个主效应及其交互项或乘积项），以及虚拟编码的`cyl`。下面对该对象调用`summary()`显示结果：

```
m <- lm(mpg ~ hp * vs + factor(cyl), data = mtcars)
summary(m)
```

##
## Call:
## lm(formula = mpg ~ hp * vs + factor(cyl), data = mtcars)
##
## Residuals:
## Min 1Q Median 3Q Max
## -4.76 -1.44 -0.17 1.59 6.94
##
## Coefficients:
## Estimate Std. Error t value Pr(>|t|)
## (Intercept) 26.9291 2.7176 9.91 2.6e-10 ***
## hp -0.0152 0.0155 -0.98 0.3372
## vs 8.5335 4.9530 1.72 0.0968 .
## factor(cyl)6 -4.2112 1.9389 -2.17 0.0392 *
## factor(cyl)8 -8.6510 2.6974 -3.21 0.0035 **
## hp:vs -0.0910 0.0436 -2.09 0.0469 *
## ---
## Signif. codes: 0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
##
## Residual standard error: 3 on 26 degrees of freedom
## Multiple R-squared: 0.791, Adjusted R-squared: 0.751
## F-statistic: 19.7 on 5 and 26 DF, p-value: 4.18e-08

我们可以使用以下代码检查对象的类，并确认当前没有`ggplot()`方法：

```
class(m)
```

## [1] "lm"

```
methods(ggplot)
```

## [1] ggplot.default* ggplot.function* ggplot.summaryP*
## [4] ggplot.transcan*



## 查看 `?methods` **以访问帮助和源代码**

尽管 `fortify()` 函数为线性模型提供了一个方法，可以创建适用于 `ggplot()` 的数据框，但它只提取原始数据、拟合值和残差。为了展示模型的效果，将预测值作为某个变量的函数（同时将其他变量保持在特定值）绘制出来是很有帮助的。下文所述的方法实现了一个实现此功能的系统。

该方法采用与 `ggplot()` 相同的基本参数，但增加了 `vars` 参数，并且模型中使用的每个变量都传递一组特定的值，以便在预测时将其保持在该值。通过使用 `do.call()` 函数将这些列表作为参数传递给 `expand.grid()` 函数，可以创建这些值的所有可能组合。然后，从模型公式中提取因变量（即 `yvar`），并将其转换为字符。生成新的预测值以及预测值的标准误差。基于拟合值或预测值以及正态分位数乘以拟合值的标准误差，生成一个 95% 的置信区间，其中包含下限 `LL` 和上限 `UL`。最后，列名从 `fit` 更改为因变量的实际名称，然后将预测数据和预测值合并到一个新的数据框中。该数据框被传递给 `ggplot()`，后者会分派到数据框的 `ggplot()` 方法：

```r
ggplot.lm <- function(data, mapping, vars, ...) {
  newdat <- do.call(expand.grid, vars)
  yvar <- as.character(formula(data)[[2]])
  d <- as.data.frame(predict(data, newdata = newdat, se.fit = TRUE))
  d <- within(d, {
    LL <- fit + qnorm(.025) * se.fit
    UL <- fit + qnorm(.975) * se.fit
  })
  colnames(d)[1] <- yvar
  data <- cbind(newdat, d[, c(yvar, "LL", "UL")])
  ggplot(data = data, mapping = mapping, ...)
}
```

有了这个方法，我们可以轻松地从线性回归模型创建一些图表。以下代码使用了我们的新方法，指定了预测变量的确切值，通过 `geom_line()` 添加了一条线，并使用 `theme_bw()` 应用了黑白主题。结果如图 5-2 所示，代码如下：

```r
ggplot(m, aes(hp, mpg), vars = list(
  hp = min(mtcars$hp):max(mtcars$hp),
  vs = mean(mtcars$vs),
  cyl = 8)) +
  geom_line(size=2) +
  theme_bw()
```

**图 5-2.** 使用 `ggplot.lm()` 方法从模型得到的预测回归线

由于我们的方法中使用 `expand.grid()` 创建了预测变量值的所有可能组合，我们可以绘制多条预测线，例如将 `vs` 保持在 0 和 1 时，如以下示例代码和图 5-3 所示。

```r
ggplot(m, aes(hp, mpg, linetype = factor(vs), group = factor(vs)), vars =
  list(
    hp = min(mtcars$hp):max(mtcars$hp),
    vs = c(0, 1),
    cyl = 8)) +
  geom_ribbon(aes(ymin = LL, ymax = UL), alpha = .25) +
  geom_line(size=2) +
  theme_bw()
```

**图 5-3.** 使用 `ggplot.lm()` 方法从模型得到的带置信区间的预测回归线

这些例子简单介绍了编写新类和新方法，或扩展现有类和方法时可以做些什么。由于 S3 系统是 R 中最常用的系统，它可能是需要学习的最重要的系统；它可以被广泛使用，并能扩展许多对象类。

## 5.2 S4 系统

与 S3 系统相比，S4 系统是一个形式化的系统。这种形式化的好处是能够更好地保证特定类的对象完全包含预期的内容。形式化的缺点是复杂性更高：设置类和方法需要更多的函数，并且编程要求更严格。

S3 系统允许编写快速而粗糙的代码，而 S4 系统需要更周密的规划，并且开销更高。

对象有三个组成部分：类、槽和方法。对象的名称和结构（即对象包含的内容）是类。存储在对象中的变量或其他对象是槽。最后，可以对对象操作的函数是它的方法。在本节中，我们将逐一介绍这些组成部分。关于 S4 系统的进一步阅读，John Chambers 的《*Software for Data Analysis: Programming with R*》（Springer，2010）是一本极好的指南。

### S4 类

由于 S4 类更具形式化，在编写新类之前需要规划。与 S3 类不同，在 S4 中，必须指定要包含的每个变量的名称和类型。之前我们使用 S3 系统定义了一个名为 `textplot` 的类。我们可以使用 S4 系统定义相同的类。`x` 和 `y` 参数都应为数值类型，`labels` 参数应为字符串。我们还知道所有参数的长度应该相同。最后，我们可能希望断言，虽然允许缺少标签，但不允许空白标签。所有这些都需要提前考虑和指定，因为虽然在 S3 系统中无法控制这些，但在 S4 系统中我们可以在创建类时定义一切。

为了创建第一个 S4 类，我们使用 `setClass()` 函数，如下例所示。并非每个参数都是必需的，但通常认为在定义新类时尽可能明确是一种好的实践。

**注意** S4 类通过调用 `setClass()` 创建。`Class` 和 `slots` 参数是必需的。类名称指定为字符串，槽（持有变量）定义为一个命名字符向量；名称对应槽名称，值对应每个槽的类。可以使用 `prototype` 参数指定一个“空”对象，并且可以通过向 `validity` 参数传递一个函数来设置有效性检查。

第一个参数 `Class` 提供了类的名称。接下来，使用一个命名字典来定义槽，其中名称表示槽名称，值表示每个槽的类型。`prototype` 参数不是必需的，但定义它很有帮助，因为它决定了如何创建一个“空”对象，或者当未指定具体数据时如何创建该类的一个对象。`validity` 参数也不是必需的，但允许进行显式检查，以确保对象符合预期。R 默认情况下确保适当类型的对象被传递到槽中，但可以添加许多其他测试和有效性检查，以减少创建不按预期工作的对象的可能性。以下是 `setClass()` 的示例：

```r
setClass(
  Class = "textplot",
  slots = c(
    x = "numeric",
    y = "numeric",
    labels = "character"
  ),
  prototype = list(
    x = numeric(),
    y = numeric(),
    labels = character()
  ),
  validity = function(object) {
    if (length(object@x) != length(object@y)) {
      stop("Lengths of x and y must match")
    }
    if (length(object@x) != length(object@labels)) {
      stop("Lengths of x, y and labels must match")
    }
    if (any(object@labels == "")) {
      stop("Blank labels are not allowed")
    }
    return(TRUE)
  }
)
```



#### S4 系统 - 类 ####

`setClass`(
Class = "textplot",
slots = c(
x = "numeric",
y = "numeric",
labels = "character"),
prototype = list(
x = numeric(0),
y = numeric(0),
labels = character(0)),
validity = `function`(object) {
stopifnot(
`length`(object@x) == `length`(object@y),
`length`(object@x) == `length`(object@labels))
`if` (! `all`(nchar(object@labels) > 0, na.rm = TRUE)) {
stop("所有标签必须缺失或为非零长度字符")
}
`return`(TRUE)
}
)

要在 S4 系统中创建特定类的对象，我们使用`new()`函数。在接下来的代码中，我们将检查三次创建新对象的尝试。首先，我们创建一个正确且有效的对象。然后，我们观察如果尝试使用错误类型的参数会发生什么。最后，我们来看一个测试我们创建的验证函数的示例：

`new("textplot",
x = c(1, 3, 5),
y = c(1, 2, 4),
labels = c("First", "Second", "Third"))`

```
## 一个类为"textplot"的对象
## 槽 "x":
## [1] 1 3 5
```

```
## 
## 槽 "y":
## [1] 1 2 4
## 
## 槽 "labels":
## [1] "First" "Second" "Third"
```

`new("textplot",
x = c(1, 3, 5),
y = c(1, 2, 4),
labels = 1:3)`

```
## Error in validObject(.Object): invalid class "textplot" object: invalid object for slot "labels"
```

`new("textplot",
x = c(1, 3, 5),
y = c(1, 2, 4),
labels = c("First", "Second", ""))`

```
## Error in validityMethod(object): All labels must be missing or non zero length characters
```

如果我们使用 S3 系统来定义类，这些错误就不会发生，因为 S3 系统没有类型检查或验证检查。在前面的例子中，每次尝试创建新对象时最多只有一个错误。下一个例子有两个错误：传递给`y`槽的向量与其他两个长度不同，并且有一个零长度的字符标签。然而，按照当前的编写方式，只有第一个错误会被捕获，因为验证函数一旦遇到任何问题就会停止：

```
## 多个错误
new("textplot",
x = c(1, 3, 5),
y = c(1, 2),
labels = c("First", "Second", ""))
## Error in validityMethod(object): length(object@x) == length(object@y) is not TRUE
```

另一种编写验证函数的方式是，不针对任何问题立即抛出错误，而是收集所有错误并在最后全部返回。在修改验证函数的同时，我们还可以考虑如何让错误信息更具信息量。目前来看，问题是什么相当直观，但可能无法确切了解问题所在。例如，显然长度不相等，但到底是`x`太长还是`y`太短？这一点在用于定义类的修订代码中得到了实现。

然而，在深入改进验证函数之前，我们需要简要了解一下在 R 中创建和格式化字符串的方法。首先，在编写文本时，可以通过包含特殊字符`\n`来插入换行符。为了查看示例，我们使用`cat()`函数，它代表拼接和打印，并将文本输出到 R 控制台（如果指定了文件，则输出到其他位置）。以下两个示例除了第二个示例中`a`和`b`之间的换行符外，完全相同：

```
## 关于格式化文本字符串的简短说明
cat("ab", fill = TRUE)
## ab
cat("a\nb", fill = TRUE)
## a
## b
```

一个常用的函数是`paste()`，它可以组合向量或将其合并。第一个示例展示了组合操作。两个向量通过分隔符（在我们的例子中是空字符串）进行组合，分隔符由`sep`参数定义。在第二个示例中，一个包含多个元素的向量被合并成一个单一的字符串。向量元素如何组合成一个字符串由`collapse`参数决定，在我们的例子中，这个参数是换行符：

`paste(c("a", "b"), c(1, 2), sep = "")`
```
## [1] "a1" "b2"
```

`paste(c("a", "b"), collapse = "\n")`
```
## [1] "a\nb"
```

这些函数在编写验证函数时非常有用，因为它们允许我们根据需要将多个错误用换行符组合成一个字符串。我们使用的另一个关键函数是`sprintf()`。它的第一个参数是用户自定义的字符串，其中包含特殊符号（总是以百分号`%`开头），用于表示需要替换的值。后续参数是要替换的值。用一个例子来展示可能最清晰。这里，`%d`用于指示要替换一个整数，然后 R 会依次替换为`98`、`80`和`75`。替换的顺序就是出现的顺序：`sprintf("First (%d), Second (%d), Third (%d)", 98, 80, 75)`

```
## [1] "First (98), Second (80), Third (75)"
```

常用的格式化替换选项包括：`%d`用于整数，`%f`用于定点小数，`%s`用于字符串，`%%`用于字面百分号。每个选项都在下一个示例中演示，更多文档可在帮助页面`?sprintf`中找到。

对于数值，我们使用`0.2`来指定数字应四舍五入到小数点后两位：

`sprintf("Integer %d, Numeric %0.2f, String %s, They won by 58%%", 5, 3.141593, "some text")`
```
## [1] "Integer 5, Numeric 3.14, String some text, They won by 58%"
```

掌握了`paste()`和`sprintf()`之后，我们可以继续修改`textplot`类的验证函数，以提供更具信息量的错误信息，并运行所有检查，在过程中收集错误，最后全部返回。最后一点：之前我们使用`new("classname", arguments)`这个惯用法来创建特定类的新对象。虽然这完全可以接受，但有一种快捷方式。调用`setClass()`主要是为了定义新类的副作用，但它也会不可见地返回一个构造函数。如果我们保存`setClass()`调用的结果（按照惯例，保存在与类同名的对象中），我们就可以使用结果对象来创建该类的新对象：

```
## 修订版，以捕获多个错误并提供更多信息
textplot <- setClass(
  Class = "textplot",
  slots = c(
    x = "numeric",
    y = "numeric",
    labels = "character"),
  prototype = list(
    x = numeric(0),
    y = numeric(0),
    labels = character(0)),
  validity = function(object) {
    errors <- character()
    if (length(object@x) != length(object@y)) {
      errors <- c(errors,
                  sprintf("x (长度为 %d) 和 y (长度为 %d) 不相等", length(object@x), length(object@y)))
    }
    if (length(object@x) != length(object@labels)) {
      errors <- c(errors,
                  sprintf("x (长度为 %d) 和 labels (长度为 %d) 不相等", length(object@x), length(object@labels)))
    }
    if (! all(nchar(object@labels) > 0, na.rm = TRUE)) {
      errors <- c(errors, sprintf(
        "有 %d 个标签长度为零。所有标签必须缺失或为非零长度",
        sum(nchar(object@labels) == 0, na.rm = TRUE)))
    }
    if (length(errors)) {
      stop(paste(c("\n", errors), collapse = "\n"))
    } else {
      return(TRUE)
    }
  }
)
```

现在，当我们创建同一个具有多个问题的对象时，我们会得到更多信息，并且节省了一些按键操作。我们可以看到`x`和`y`的长度，还能知道标签存在问题：

```
## 多个错误，信息更丰富
textplot(
  x = c(1, 3, 5),
  y = c(1, 2),
  labels = c("First", "Second", ""))
## Error in validityMethod(object):
##
## x (长度为 3) 和 y (长度为 2) 不相等
```



