# 标签：a b c d

接下来，我们为 `[` 函数定义一个方法，该方法用于对数据进行子集选取。这个函数更为复杂，因为我们必须构建一个逻辑树，以支持参数的多种可能组合。函数的第一个参数 `x` 代表要进行子集选取的对象。按照惯例，`i` 指代行或观测值，`j` 指代列或变量。如果只有 `i` 非缺失（即指定了行或观测值），通常包含所有变量。如果只有 `j`（变量）非缺失，通常包含所有观测值。如果两者都指定，则只包含选定的变量和观测值。这是通过使用一系列 `if` 和 `else if` 语句实现的。这里有三个新函数：

- `validObject()` 是一个泛型函数，用于对特定对象类执行有效性检查（如果存在）。
- `slotNames()` 返回一个字符向量，给出每个槽位的名称。
- `slot()` 的工作方式与 `@` 运算符类似，但可以使用字符串按名称提取槽位。

第 5 章 编写类和方法

```
setMethod(
  f = "[",
  signature = "textplot",
  definition = function(x, i, j, drop) {
    if (missing(i) & missing(j)) {
      out <- x
      validObject(out)
    } else if (!missing(i) & missing(j)) {
      out <- textplot(
        x = x@x[i],
        y = x@y[i],
        labels = x@labels[i])
      validObject(out)
    } else if (!missing(j)) {
      if (missing(i)) {
        i <- seq_along(x@x)
      }
      if (is.character(j)) {
        out <- lapply(j, function(n) {
          slot(x, n)[i]
        })
        names(out) <- j
      } else if (is.numeric(j)) {
        n <- slotNames(x)
        out <- lapply(j, function(k) {
          slot(x, n[j])[i]
        })
        names(out) <- n[j]
      } else {
        stop("j is not a valid type")
      }
    }
    return(out)
  }
)
```

现在方法已设定，下面展示几个使用示例：

```
dat[]
```

```
## X: 1 2 3 4
## Y: 1 3 5 2
## 标签: a b c d
```

```
dat[i = 1:2]
```

```
## X: 1 2
## Y: 1 3
## 标签: a b
```

```
dat[j = 1]
```

```
## $x
## [1] 1 2 3 4
```

```
dat[j = "y"]
```

```
## $y
## [1] 1 3 5 2
```

```
dat[i = 1:2, j = c("x", "y")]
```

```
## $x
## [1] 1 2
##
## $y
## [1] 1 3
```

通过为 `textplot` 类定义 `show` 方法和子集选取方法，我们可以按组循环遍历对象、选取子集并显示每个子集，从而轻松地为 `groupedtextplot` 类创建 `show` 方法：

```
setMethod(
  f = "show",
  signature = "groupedtextplot",
  definition = function(object) {
    n <- unique(object@group)
    i <- lapply(n, function(index) {
      cat("组: ", index, fill = TRUE)
      show(object[which(object@group == index)])
    })
  }
)
```

```
gdat
```

```
## 组: 1
## X: 1 2 3 4
## Y: 1 3 4 2
## 标签: a b c d
## 组: 2
## X: 5 6 7 8
## Y: 6 8 7 10
```



