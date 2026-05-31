# 参数

给定一个函数，该函数的参数列表可在其帮助页面中找到。大多数帮助页面会区分该函数的 S3 和 S4 版本。S3 版本列出了函数 S3 形式的参数。S4 版本则仅列出那些必须包含的参数，以及 `“...”` 参数。在 S4 中，泛型函数的每个方法都各不相同，因此参数可能因方法而异。

对于某些函数，用户需要了解该函数背后的理论才能理解其参数，但对于许多函数而言，参数是直截了当的。如上一节所述，调用函数时，具有默认值的参数不必指定值。

函数的参数必须具有正确的模式和类别。在函数的帮助页面中，参数描述列在 `Arguments` 部分，有时会给出其模式和/或类别，但并非总是如此。有时，模式和/或类别是显而易见的。有时，可以在 `Details` 部分找到更多信息。有时，查看 `Examples` 部分就足以弄清参数的形式。

有一个需要稍加解释的参数是 `“...”` 参数。`“...”` 参数告知用户可能还有更多参数可以输入。这些参数将传递给由高级函数调用的底层函数。示例如下。

该示例首先列出两个向量 `“x”` 和 `“y”`，然后继续两次调用 `lm()` 函数，并为参数 `“tol”` 赋予了不同的值。（函数 `lm()` 用于拟合线性模型。）在 `lm()` 的帮助页面中，没有 `“tol”` 参数。然而，却有 `“...”` 参数，这表明 `lm()` 会调用另一个函数，并且可以为该函数输入参数。

`lm.fit()` 是 `lm()` 调用的一个底层函数，并且 `lm.fit()` 具有 `“tol”` 参数。（参数 `“tol”` 给出了 QR 分解中用于判断矩阵是否奇异的容差。）在第一次调用 `lm()` 时，由于未指定 `“tol”`，因此使用了 `“tol”` 的默认值。在第二次调用中，`lm()` 将 `“tol”` 的值传递给了 `lm.fit()`。

```
> x
[1] 2.001 2.000 2.000
> y
[1] 4.03 4.00 4.01
> lm( y~x )
Call:
lm(formula = y ~ x)
Coefficients:
(Intercept)            x
-45.99        25.00
> lm( y~x, tol=.001 )
Call:
lm(formula = y ~ x, tol = 0.001)
Coefficients:
(Intercept)            x
4.013           NA
```

在第一次调用中，`“tol”` 的默认值为 1.0e-7，因此 `lm.fit()` 在由一列常量 1 和 `“x”` 组成的矩阵中未发现线性相关性。结果拟合了两个系数。

在第二次调用中，`“tol”` 被设置为 1.0e-3，`lm()` 判定由一列常量 1 和 `“x”` 组成的矩阵中存在线性相关性，因此只拟合了一个系数。

## 函数的输出

函数的输出因函数而异。绘图函数主要生成图形。摘要函数给出汇总结果。检验假设的函数会给出检验结果。

大多数打包函数会将某些结果直接输出到屏幕，但大多数打包函数也具有可通过下标访问的输出。例如，查看函数 `lm()` 的帮助页面，在 `Value` 部分下，`coefficients`、`residuals`、`fitted.values`、`rank`、`weights`、`df.residual`、`call`、`terms`、`contrasts`、`xlevels`、`offset`、`y`、`x`、`model` 和 `na.action` 都是可以从对该函数的调用中访问的值。

访问值最常用的方法是使用 `$` 运算符，当然也可以使用索引下标。对于大多数函数，输出是列表模式。列表的元素可以是任何模式。

对于上一节中拟合的第一个简单回归模型，可访问的 15 个值如下所示：

```
> a.lm = lm( y~x )
> a.lm$coef
(Intercept)           x
-45.995      25.000
> a.lm$res
1             2             3
-4.336809e-19 -5.000000e-03  5.000000e-03
> a.lm$fit
1     2     3
4.030 4.005 4.005
> a.lm$rank
[1] 2
> a.lm$weights
NULL
> a.lm$df
[1] 1
> a.lm$call
lm(formula = y ~ x)
> a.lm$terms
y ~ x
attr(,"variables")
list(y, x)
attr(,"factors")
x
y 0
x 1
attr(,"term.labels")
[1] "x"
attr(,"order")
[1] 1
attr(,"intercept")
[1] 1
attr(,"response")
[1] 1
attr(,".Environment")

attr(,"predvars")
list(y, x)
attr(,"dataClasses")
y         x
"numeric" "numeric"
> a.lm$contrasts
NULL
> a.lm$xlevels
named list()
> a.lm$offset
NULL
> a.lm$y
NULL
> a.lm$x
named list()
> a.lm$model
y     x
1 4.03 2.001
2 4.00 2.000
3 4.01 2.000
> a.lm$na.action
NULL
```

在示例中，对 `lm()` 的调用被赋了一个名称，但 `lm()` 也可以直接被下标引用。例如 `lm(y~x)$coef`。从函数调用中访问的值通常用于另一个函数。

运行 R 函数需要稍加注意，但通过一些尝试和决心，其结果可能非常有用。



