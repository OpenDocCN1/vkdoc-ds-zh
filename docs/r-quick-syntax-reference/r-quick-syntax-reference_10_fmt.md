# 4. 对象的模式和类型

R 对象存在于一个对象系统中。R 有三种对象系统，本书涵盖了其中两种：`S3` 和 `S4`。`S4` 是 R 的一个较新版本，包含了一种处理 R 编程的新方式。`S3` 是一个较早的版本。两个版本可以同时运行。`S4` 提供了强大的新方法，但要使用这些方法，扎实掌握 `S3` 是必要的。本书包含 `S4` 语法，但重点在于 `S3` 语法。

## 模式与类型概述

模式描述了一个对象所包含的信息类型，是一种 `S3` 级别的分类。对象的模式可以通过使用函数 `mode()` 来查找。`S4` 级别的分类是按类型划分的，可以通过函数 `typeof()` 来查找。目前，R 对象属于以下模式之一：`NULL`、`logical`、`numeric`、`complex`、`raw`、`character`、`list`、`expression`、`name`、`function`、`pairlist`、`language`、`char`、`...`、`environment`、`externalptr`、`weakref`、`bytecode`、`promise`、`any` 和 `S4`。由于 R 在不断变化，模式列表也可能会改变。除了少数例外，类型和模式是相同的，并且大多数模式都可以在类型列表中找到。类型和模式的列表可以在 `typeof()` 的帮助页面上找到。`mode()` 和 `typeof()` 给出不同结果的大多数情况可以在 `mode()` 的帮助页面中找到，具体如下：`typeof()` 返回 `integer` 或 `double` 时，`mode()` 返回 `numeric`；`typeof()` 返回 `closure`、`special` 或 `built-in` 时，`mode()` 返回 `function`；`typeof()` 返回 `symbol` 时，`mode()` 返回 `name`；`typeof()` 返回 `{` 或 `call` 时，`mode()` 返回 `language`。

### 常用模式

大多数用户只会使用部分模式。常用的模式包括 `NULL`、`logical`、`numeric`、`complex`、`raw`、`character`、`list`、`function`、`call`、`expression`、`environment` 和 `S4`。模式 `NULL` 是无模式的空对象的模式。模式为 `logical` 的对象包含可以取值为 `TRUE`、`FALSE` 或 `NA` 的元素，其中 `NA` 代表缺失值。模式为 `numeric` 的对象可以取整数值、实数值或 `NA`。模式为 `complex` 的对象可以取复数值或 `NA`。模式为 `raw` 的对象由字节组成。`NA` 会转换为 `00`，并附带一条警告信息。

模式为 `character` 的对象由字符串或 `NA` 组成。字符对象的元素（`NA` 除外）需要用引号括起来。模式为 `list` 的对象是其他对象的列表，这些对象可以是任何模式。模式为 `function` 的对象是函数。模式为 `call` 的对象是函数及其参数。模式为 `expression` 的对象是诸如调用和名称等对象的集合。模式为 `environment` 的对象是 R 环境，例如包。模式为 `S4` 的对象是那些复杂的 S4 对象（这里的“复杂”指的是对象的结构，而非复数）。S4 使用了比 S3 更专门化的结构。

以上信息的来源是 `mode()` 和 `typeof()` 的帮助页面。

### 原子模式、递归模式和语言模式

模式分为四种：原子模式、递归模式、语言模式和 S4 模式。原子模式包括 `NULL`、`logical`、`numeric`、`complex`、`raw` 和 `character`。*原子* 指的是对象的元素像原子一样。对于原子模式，对象内的所有元素都具有相同的原子模式。递归模式是对象的集合，可以包含不同模式的对象。两种递归模式是 `list` 和 `function`。大多数非原子对象都是递归的。语言模式包括 `name`、`call` 和 `expression`。有关模式种类的更多信息，可以在用于测试对象模式种类的函数的帮助页面中找到：`is.atomic()`、`is.recursive()` 和 `is.language()`。S4 模式指的是 S4 数据对象。

## 原子模式（类型）的若干函数

对于原子对象而言，其模式与类型是相同的，但 `numeric` 模式（包含 `integer` 和 `double` 类型）除外。我们在此处讨论的是模式。除了 `NULL` 之外，每种原子模式都关联着三个函数。假设 *name* 是该模式的名称，那么这三个函数分别是：以该模式命名的函数 `name()`；一个 `as.name()` 函数；以及一个 `is.name()` 函数。`name()` 函数会根据参数或参数（当参数具有正确的模式和允许的值时）的长度创建一个向量。

`as.name()` 函数尝试将其参数强制转换为指定的模式。如果无法进行强制转换，`as.name()` 函数会返回一个由 `NA` 组成的向量，或者报错。请注意，如果参数是矩阵或数组，则会返回一个由该矩阵或数组元素组成的向量，其中向量的转换会依次遍历矩阵或数组的每个维度（对于矩阵，则是先遍历第一列的每一行，然后是第二列，依此类推）。

`is.name()` 函数用于测试其参数是否属于指定的模式，并根据参数是否符合条件返回 `TRUE` 或 `FALSE`。

### NULL 模式

`NULL` 是 R 语言中的一个保留对象，同时也是一种模式。虽然在 R 语言中没有 `NULL()` 函数，但存在 `as.null()` 和 `is.null()` 函数。无论使用任何对象作为参数，还是不使用参数，`as.null()` 都只返回一个 `NULL`。如果参数等于 `NULL`，则 `is.null()` 函数返回 `TRUE`；否则返回 `FALSE`。例如：

```r
> a0=1:3
> as.null( a0 )
NULL
> is.null( a0 )
[1] FALSE
> is.null( as.null( a0 ) )
[1] TRUE
```

### 逻辑模式

不带参数或以零作为参数调用 `logical()` 函数，会返回 `logical(0)`，这是一个长度为零的逻辑空集。以大于零的整数作为参数调用 `logical()` 函数，会返回一个长度等于该整数的、由 `FALSE` 组成的向量。如果参数是单个双精度元素，则该元素会被向下取整，并创建一个长度等于取整后整数的、由 `FALSE` 组成的向量。如果参数是一个数字对象，但不是单个数字，则该函数会报错。如果参数的模式是 `NULL`、`logical`、`character`、`complex`、`raw` 或非原子模式，那么 `logical()` 会报错。例如：

```r
> logical()
logical(0)
> logical( 0 )
logical(0)
> logical( 2 )
[1] FALSE FALSE
> logical( 2.7 )
[1] FALSE FALSE
> logical( 1, 2 )
Error in logical(1, 2) : unused argument (2)
```

`as.logical()` 函数接受两个参数：`x` 和 `"..."`。参数 `"..."` 用于在调用中提供给其他函数使用。参数 `x` 是单个对象。`as.logical()` 函数会尽可能地将参数 `x` 强制转换为逻辑型，并返回一个包含 `TRUE`、`FALSE` 和/或 `NA` 的向量。如果没有参数或参数为 `NULL`，`as.logical()` 会返回 `logical(0)`，即一个长度为零的逻辑空集。如果参数的模式是 `numeric`，零会被返回为 `FALSE`，所有其他数字会被返回为 `TRUE`。

如果参数是复数对象，该函数对于 `0+0i` 返回 `FALSE`，对于其他情况返回 `TRUE`。如果模式是 `raw`，`00` 会返回 `FALSE`，任何其他值会返回 `TRUE`。如果参数的模式或类型是 `character`，该函数会返回一个长度等于参数长度、由 `NA` 组成的向量。如果参数包含 `NA`，对于除 `raw` 以外的所有模式，包含 `NA` 的元素将返回 `NA`。对于 `raw` 模式，不存在 `NA`，因为 `NA` 在该模式下会被强制转换为 `00`。对于列表模式，如果列表中的每个元素仅包含单个原子值，并且这些元素的模式可以强制转换为逻辑型，则进行强制转换。否则，列表对象会报错。对于任何其他模式或类型，`as.logical()` 会报错。例如：

```
> as.logical()
logical(0)
> as.logical( 0, 2 )
[1] FALSE
> as.logical( c(0, 2) )
[1] FALSE  TRUE
> as.logical( list( 0, 2.5, 0+1i, as.raw( NA ), NA, "2" ) )
[1] FALSE  TRUE  TRUE FALSE    NA    NA
Warning message:
out-of-range values treated as 0 in coercion to raw
> as.logical( list( c( T, T ), 0:3 ) )
Error: (list) object cannot be coerced to type 'logical'
```

如果参数是逻辑对象，则 `is.logical()` 函数返回 `TRUE`，否则返回 `FALSE`。`is.logical(logical(0))` 的结果是 `TRUE`。

有关 `logical` 模式的更多信息，请在 R 提示符下输入 `?logical`，或使用 R Studio 中的“帮助”选项卡。