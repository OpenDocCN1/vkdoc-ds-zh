# S3 系统 - 类

要查看对象的类，我们可以使用 `class()` 函数：

```r
class(mtcars)
```

```
[1] "data.frame"
```

要查看对象的类是如何设置的，我们可以观察基础 R 环境中的许多函数，例如 `table()`。这里，我们打印了 `table()` 函数的源代码，省略了中间部分以节省空间：

```r
table
```

```
function (..., exclude = if (useNA == "no") c(NA, NaN), useNA = c("no", 
    "ifany", "always"), dnn = list.names(...), deparse.level = 1) 
{
    list.names <- function(...) {
        l <- as.list(substitute(list(...)))[-1L]
        nm <- names(l)
        fixup <- if (is.null(nm)) 
            seq_along(l)
        else nm == ""
        dep <- vapply(l[fixup], function(x) switch(deparse.level + 
            1, "", if (is.symbol(x)) as.character(x) else "", 
            deparse(x, nlines = 1)[1L]), "")
        if (is.null(nm)) 
            dep
        else {
            nm[fixup] <- dep
            nm
        }
    }
    miss.use <- missing(useNA)
    miss.exc <- missing(exclude)
    useNA <- if (miss.use && !miss.exc && !match(NA, exclude, 
        nomatch = 0L)) 
        "ifany"
    else match.arg(useNA)
    doNA <- useNA != "no"
    if (!miss.use && !miss.exc && doNA && match(NA, exclude, 
        nomatch = 0L)) 
        warning("'exclude' containing NA and 'useNA' != \"no\"' are a bit contradicting")
    args <- list(...)
    if (!length(args)) 
        stop("nothing to tabulate")
    if (length(args) == 1L && is.list(args[[1L]])) {
        args <- args[[1L]]
        if (length(dnn) != length(args)) 
            dnn <- if (!is.null(argn <- names(args))) 
                argn
            else paste(dnn[1L], seq_along(args), sep = ".")
    }
    bin <- 0L
    lens <- NULL
    dims <- integer()
    pd <- 1L
    dn <- NULL
    for (a in args) {
        if (is.null(lens)) 
            lens <- length(a)
        else if (length(a) != lens) 
            stop("all arguments must have the same length")
        fact.a <- is.factor(a)
        if (doNA) 
            aNA <- anyNA(a)
        if (!fact.a) {
            a0 <- a
            a <- factor(a, exclude = exclude)
        }
        add.na <- doNA
        if (add.na) {
            ifany <- (useNA == "ifany")
            anNAc <- anyNA(a)
            add.na <- if (!ifany || anNAc) {
                ll <- levels(a)
                if (add.ll <- !anyNA(ll)) {
                  ll <- c(ll, NA)
                  TRUE
                }
                else if (!ifany && !anNAc) 
                  FALSE
                else TRUE
            }
            else FALSE
        }
        if (add.na) 
            a <- factor(a, levels = ll, exclude = NULL)
        else ll <- levels(a)
        a <- as.integer(a)
        if (fact.a && !miss.exc) {
            ll <- ll[keep <- which(match(ll, exclude, nomatch = 0L) == 
                0L)]
            a <- match(a, keep)
        }
        else if (!fact.a && add.na) {
            if (ifany && !aNA && add.ll) {
                ll <- ll[!is.na(ll)]
                is.na(a) <- match(a0, c(exclude, NA), nomatch = 0L) > 
                  0L
            }
            else {
                is.na(a) <- match(a0, exclude, nomatch = 0L) > 
                  0L
            }
        }
        nl <- length(ll)
        dims <- c(dims, nl)
        if (prod(dims) > .Machine$integer.max) 
            stop("attempt to make a table with >= 2^31 elements")
        dn <- c(dn, list(ll))
        bin <- bin + pd * (a - 1L)
        pd <- pd * nl
    }
    names(dn) <- dnn
    bin <- bin[!is.na(bin)]
    if (length(bin)) 
        bin <- bin + 1L
    y <- array(tabulate(bin, pd), dims, dimnames = dn)
    class(y) <- "table"
    y
}
<bytecode: 0x000002ac81ef2848>
<environment: namespace:base>
```

函数末尾返回的对象 `y` 是一个数组，但具有表格类。对象的类是通过 `class(object) <- "类名"` 这个习语设置的。也可以为一个对象分配多个类。其实现方式与分配单个类大致相同，并按优先级顺序进行。以下示例将调用 `table()` 的结果存储在对象 `x` 中，然后设置了两个类，首先是 `newclass`，接着是原始的表格类：

```r
x <- table(mtcars$cyl)
class(x) <- c("newclass", "table")
class(x)
```

```
[1] "newclass" "table"
```

分配多个类的价值在于提供一种回退机制，主要针对方法而言。例如，如果你创建了一个新类，它是表格或数据框的变体，你可以为打印行为编写专门的方法，但对于其他函数（如绘图或汇总），则可以依赖原始类的方法。

你可以编写的类类型几乎是无穷无尽的。一个简单的起点是创建现有类的特殊或增强版本。在下面的例子中，我们创建了一个 `data.frame` 的特殊情况，它包含 `x` 和 `y` 坐标以及文本标签，称为 `textplot`。“创建”这个类最简单的方法是先创建一个 `data.frame`，然后更改其类。如果我们只使用新的类标签，调用 `print()` 函数将使用默认方法；但如果我们同时将其标记为新的 `textplot` 标签并次要地标记为 `data.frame`，那么调用 `print()` 会回退到 `data.frame` 的方法：

```r
d <- data.frame(
  x = c(1, 3, 5),
  y = c(1, 2, 4),
  labels = c("First", "Second", "Third")
)
class(d) <- "textplot"
print(d)
```

```
$x
[1] 1 3 5

$y
[1] 1 2 4

$labels
[1] "First"  "Second" "Third" 

attr(,"class")
[1] "textplot"

attr(,"row.names")
[1] 1 2 3
```

```r
class(d) <- c("textplot", "data.frame")
print(d)
```

```
  x y  labels
1 1 1   First
2 3 2  Second
```