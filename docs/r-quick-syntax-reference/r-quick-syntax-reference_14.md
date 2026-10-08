# 11. 描述性函数与对象操作

对于数组、矩阵、向量、列表和表达式，在命令行 R 中，有许多函数可以描述对象的各种属性。在 R Studio 中，许多属性，例如矩阵的列数或列表的长度，会显示在右上角窗口“环境”选项卡下对象名称的右侧。

此外，还有许多函数可以操作对象以创建新对象。本章涵盖的描述性函数包括 `dim()`、`nrow()`、`NROW()`、`ncol()`、`NCOL()`、`length()`、`nchar()` 和 `nzchar()`；操作对象的函数包括：`cbind()` 和 `rbind()`；apply 类函数、`sweep()`、`scale()` 和 `aggregate()`；表函数以及函数 `tabulate()` 和 `ftable()`；以及字符串函数：`grep()`、`grepl()`、`agrep()`、`grepRaw()`、`sub()`、`gsub()`、`regexpr()`、`gregexp()`、`regexec()`、`substr()`、`substring()` 和 `strsplit()`。

# 描述性函数

描述性函数用于描述对象的性质。本节讨论一些在编写函数或创建对象时非常有用的描述性函数。这些函数包括 `dim()`、`nrow()`、`ncol()`、`NROW()`、`NCOL()`、`length()` 和 `nchar()`。

## 函数 `dim()`

对于具有维度概念的对象（如矩阵、数据框、表格或数组），`dim()` 函数会返回该对象在每个维度上的层数。对于其他类别的对象，`dim()` 返回 `NULL`。示例如下：

```
> a = 1:2
> b = 1:3
> dim( a )
NULL
> a %o% b %o% a
, , 1
[,1] [,2] [,3]
[1,]    1    2    3
[2,]    2    4    6
, , 2
[,1] [,2] [,3]
[1,]    2    4    6
[2,]    4    8   12
> dim( a %o% b %o% a )
[1] 2 3 2
```

如果原始维度的乘积等于结果维度的乘积，则可以改变对象的维度。示例如下：

```
> a.ar = a %o% b
> a.ar
[,1] [,2] [,3]
[1,]    1    2    3
[2,]    2    4    6
> dim( a.ar )
[1] 2 3
> dim( a.ar )= c( 3, 2 )
> a.ar
[,1] [,2]
[1,]    1    4
[2,]    2    3
[3,]    2    6
```

你可以通过在 R 提示符下输入 `?dim` 或使用 R Studio 的“帮助”选项卡来获取有关 `dim()` 的更多信息。

## 函数 `nrow()`、`ncol()`、`NROW()` 和 `NCOL()`

对于矩阵、数据框和数组，`nrow()` 和 `ncol()` 分别返回矩阵、数据框或数组的第一维度和第二维度的层数。其他类别的对象返回 `NULL`。以下示例使用了上一节的 `a` 和 `b`：

```
> a %o% b
[,1] [,2] [,3]
[1,]    1    2    3
[2,]    2    4    6
> nrow( a %o% b )
[1] 2
> ncol( a %o% b )
[1] 3
> nrow( 1:20 )
NULL
```

有时需要将向量视为矩阵或数组。函数 `NROW()` 和 `NCOL()` 将向量视为单列矩阵，除此之外与 `nrow()` 和 `ncol()` 相同。示例如下：

```
> NROW( 1:20  )
[1] 20
> NCOL( 1:20 )
[1] 1
```

你可以通过在 R 提示符下输入 `?nrow` 或使用 R Studio 的“帮助”选项卡来获取有关 `nrow()`、`ncol()`、`NROW()` 和 `NCOL()` 的更多信息。

## 函数 `length()`

我们将解释的下一个描述性函数是 `length()`。`length()` 的参数可以是任何模式的对象。对于原子对象，`length()` 返回对象中元素的数量。对于列表对象，`length()` 返回最低层级元素的数量。对于函数，`length()` 返回 1。对于调用，`length()` 返回创建调用时输入的参数数量。对于表达式，`length()` 返回表达式中元素的数量。示例如下：

```
> a.mat=matrix( 1:4, 2, 2 )
> a.mat
[,1] [,2]
[1,]    1    3
[2,]    2    4
> length( a.mat )
[1] 4
> a.list=list( mat, c( "abc", "cde" ) )
> a.list
[[1]]
[,1] [,2]
[1,]    1    3
[2,]    2    4
[[2]]
[1] "abc" "cde"
> length( a.list )
[1] 2
> a.fun = function( mu, se=1, alpha=.05 ){
z_value = qnorm( 1-alpha/2, mu, se )
print( z_value )
}
> length( a.fun )
[1] 1
> a.call=call( "lm", y~x )
> a.call
lm(y ~ x)
> length( a.call )
[1] 2

> a.exp = expression( a.call, sin( 1:5/180 * pi ) )
> a.exp
expression(a.call, sin(1:5/180 * pi))
> length(a.exp)
[1] 2
```

可以使用 `length()` 来赋值原子对象或列表对象的长度。对于其他模式的对象，尝试用 `length()` 赋值会返回错误。如果 `n` 是原子对象的长度，则将长度设置为大于 `n` 的值会为多余的元素生成 `NA`。将长度设置为小于 `n` 则会移除元素。在这两种情况下，都会返回一个向量，除非长度没有改变，此时返回原始对象。示例如下：

```
> a.mat
[,1] [,2]
[1,]    1    3
[2,]    2    4
> a.mat.2 = a.mat
> length( a.mat.2 )=6
> a.mat.2
[1]  1  2  3  4 NA NA
> a.mat.2 = a.mat
> length( a.mat.2 )=3
> a.mat.2
[1] 1 2 3
> a.mat.2 = a.mat
> length( a.mat.2 )=4
> a.mat.2
[,1] [,2]
[1,]    1    3
[2,]    2    4
```

对于模式为 `list` 的对象，增加列表长度会在最低层级添加 `NULL` 元素，而缩短列表长度则会移除最低层级的元素。示例如下：

```
> a.list
[[1]]
cl1 cl2
[1,]   1   3
[2,]   2   4
[[2]]
[1] "abc" "cde"
> length( a.list )=3
> a.list
[[1]]
cl1 cl2
[1,]   1   3
[2,]   2   4
[[2]]
[1] "abc" "cde"
[[3]]
NULL
> length( a.list )=1
> a.list
[[1]]
cl1 cl2
[1,]   1   3
[2,]   2   4
```

你可以通过在 R 提示符下输入 `?length` 或使用 R Studio 的“帮助”选项卡来获取有关 `length()` 的更多信息。

### 函数 `nchar()` 和 `nzchar()`

函数 `nchar()` 用于计算可强制转换为 `character` 模式的对象的字符数。函数 `nzchar()` 返回一个逻辑向量，指示哪些元素包含非空字符串。

函数 `nchar()` 有四个参数：`x`、`type`、`allowNA` 和 `keepNA`。参数 `x` 是对象。该函数将对象强制转换为字符类型，计数的字符是强制转换后对象每个元素中的字符。例如，重新定义上一节中定义的 `a.list`：

```
> a.list = list( matrix( 1:4, 2,2 ), c( "abc", "cde" ), NULL )
> a.list
[[1]]
     [,1] [,2]
[1,]    1    3
[2,]    2    4
[[2]]
[1] "abc" "cde"
[[3]]
NULL
> as.character( a.list )
[1] "1:4"                "c(\"abc\", \"cde\")" "NULL"               
> nchar( a.list )
[1]  3 15  4
```

引号不计算在内。

参数 `type` 是一个字符参数，可以取 `"bytes"`、`"chars"` 或 `"width"` 这些值。如果选择 `"bytes"`，则计算字符串的字节数。如果选择 `"chars"`，则计算标准的文本字符数。如果选择 `"width"`，则计算函数 `cat()` 分配给字符串的字符数。默认值是 `"chars"`。通常，这三者之间没有区别。

参数 `allowNA` 是一个逻辑参数。如果设置为 `TRUE`，则无效的字符串将被设置为 `NA`。如果设置为 `FALSE`，则无效的字符串会报错并导致函数停止。默认值是 `FALSE`。

参数 `keepNA` 是一个逻辑参数，告诉 `nchar()` 是将 `NA` 转换为字符串还是保持为 `NA`。默认值是 `NA`，它会告诉 `nchar()`：如果 `type` 是 `"bytes"` 或 `"char"`，则将参数设置为 `TRUE`；如果 `type` 是 `"width"`，则设置为 `FALSE`。如果参数是一个数据框，由于数据框是一个列表，每个列都会被转换为字符串，并且 `NA` 也会在 `nchar()` 计数之前被转换成字符串，无论 `keepNA` 设置为 `TRUE` 还是 `FALSE`。对于向量、矩阵和数组，`NA` 不会被转换为字符串。

例如：

```r
> a.df=data.frame( 1, NA, 12 )
> as.character( a.df )
[1] "1"  "NA" "12"
> nchar( a.df, keepNA=F )
  X1 NA. X12
1  2   2
>  nchar( a.df, keepNA=T )
  X1 NA. X12
1  2   2
> a.mat = as.matrix( a.df )
> as.character( a.mat )
[1] "1"  NA   "12"
> nchar( a.mat, keepNA=F )
  X1 NA. X12
[1,]  1   2   2
> nchar( a.mat, keepNA=T )
  X1 NA. X12
[1,]  1  NA   2
```

函数 `nzchar()` 返回一个逻辑向量，其长度与分配给第一个参数的对象相同。该函数返回一个由 `TRUE`、`FALSE` 和 `NA` 组成的向量，具体取决于元素是非空字符串、空字符串还是缺失值。该函数有两个参数：`x` 和 `keepNA`。参数 `x` 是一个可以强制转换为字符向量的对象。参数 `keepNA` 是逻辑型的，可以取 `TRUE`、`FALSE` 或 `NA` 这些值。如果 `keepNA` 是 `TRUE`，`NA` 返回 `NA`；如果是 `FALSE` 或 `NA`，`NA` 返回 `TRUE`。默认值是 `FALSE`。

例如：

```r
> nzchar( c( "1", NA, "12", "" ), keepNA=F )
[1]  TRUE  TRUE  TRUE FALSE
> nzchar( c( "1",  NA,   "12", "" ), keepNA=NA )
[1]  TRUE  TRUE  TRUE FALSE
> nzchar( c( "1",  NA,   "12", "" ), keepNA=T )
[1]  TRUE    NA  TRUE FALSE
```

你可以通过在 R 提示符下输入 `?nchar` 或使用 R Studio 中的帮助选项卡来查找关于 `nchar()` 和 `nzchar()` 的更多信息。

## 操作对象

有许多函数可以操作 R 对象并使编程更简单。本小节涵盖了一些函数，包括 `cbind()`、`rbind()`、`apply()`、`lapply()`、`sapply()`、`vapply()`、`tapply()`、`mapply()`、`eapply()`、`sweep()`、`scale()`、`aggregate()`、`table()`、`tabulate()` 和 `ftable()`。

### 函数 `cbind()` 和 `rbind()`

对于向量、矩阵和数据框而言，函数 `cbind()` 和 `rbind()` 是不言自明的。函数 `cbind()` 用于按列绑定。函数 `rbind()` 用于按行绑定。

对于不是矩阵形式的列表，这些函数会返回列表中每个最底层元素的类型和数量，从而创建一个由类型组成的矩阵。列表可以与非列表对象绑定。结果将是一个列表，但非列表参数不会像结果中的列表部分那样被转换。

在调用该函数时，要绑定的对象用逗号分隔。对于 `cbind()`，向量被视为列。对于 `rbind()`，向量被视为行。

对于向量，被绑定的向量不必具有相同的长度。向量会与自身以及更高维度的对象进行循环。对于更高维度的对象，对象不会循环。如果对于 `rbind()`，列数不匹配，或者对于 `cbind()`，行数不匹配，则会报错。

结果对象采用所输入的最高层级对象的类型，其中层级从低到高为：`raw`、`logical`、`integer`、`double`、`complex`、`character` 和 `list`。

`cbind()` 和 `rbind()` 除了要绑定的对象之外，还有一个参数——`deparse.level` 参数，用于为不是矩阵形式的对象创建标签。该参数是一个整数参数，可以取 `0`、`1` 或 `2` 这些值，不过任何可以强制转换为整数的值都可以使用。强制转换为整数后不等于 `1` 或 `2` 的值会产生与 `0` 相同的结果。默认值是 `1`。

对于数据框，如果在要绑定的对象中包含一个数据框，并且不包含不是数据框的列表，那么结果也是一个数据框。在这种情况下，除非另有指定，否则任何字符列都会变为因子。

对于时间序列，`cbind()` 会生成一个多元时间序列，而对于 `rbind()`，时间序列会变回普通矩阵。

示例如下：

```r
> ab.list = list( one=1:3, two=1:5 )
> ab.list
$one
[1] 1 2 3
$two
[1] 1 2 3 4 5
> cbind( ab.list, 1:2 )
        ab.list  
one     Integer,3 1
two     Integer,5 2
> cbind( ab.list, 1:2, deparse.level=0 )
      [,1]       [,2]
one   Integer,3 1
two   Integer,5 2
> cbind( ab.list, 1:2, deparse.level=2 )
        ab.list   1:2
one     Integer,3 1
two     Integer,5 2
```

你可以通过在 R 提示符下输入 `?cbind` 或使用 R Studio 中的帮助选项卡来查找关于 `cbind()` 和 `rbind()` 的更多信息。

### Apply 函数系列

R 中有几个函数用于在对象的子集上应用函数，这里涵盖其中七个。这七个函数是 `apply()`、`lapply()`、`sapply()`、`vapply()`、`tapply()`、`mapply()` 和 `eapply()`。要应用的函数可以是用户自定义的，这非常有用。

#### 函数 `apply()`

函数 `apply()` 有三个参数——`X`、`MARGIN` 和 `FUN`——以及任何传递给函数 `FUN` 的参数。第一个参数 `X` 是一个数组（包括矩阵）。第二个参数指定函数要操作的边际（们），`FUN` 是要应用的函数。

对于矩阵，为 `MARGIN` 输入 `1` 表示在列上应用函数。输入 `2` 表示在行上向下应用函数。

要应用的函数不带括号输入。该函数的任何参数接下来输入，用逗号分隔。结果是一个数组、矩阵或向量。示例如下：

```r
> a.mat=matrix( 1:4, 2, 2, dimnames=list( c( "r1", "r2" ), c( "c1", "c2" ) ) )
> a.mat
   c1 c2
r1  1  3
r2  2  4
> apply( a.mat, 1, sum )
r1 r2 
 4  6 
> apply( a.mat, 1, pnorm, 3, 1 )
          r1        r2
c1 0.02275013 0.1586553
c2 0.50000000 0.8413447
```

在示例中，第一个 `apply` 计算行的和。对于第二个 `apply`，传递给 `pnorm()` 的参数是：`mat` 中的行作为 `q` 值，`3` 作为 `mean` 的值，`1` 作为 `sd` 的值。请注意，结果中的矩阵被转置了。

你可以通过在 R 提示符下输入 `?apply` 或使用 R Studio 中的帮助选项卡来查找关于 `apply()` 的更多信息。

#### `lapply()`、`sapply()` 和 `vapply()` 函数

`lapply()`、`sapply()` 和 `vapply()` 函数适用于向量（包括列表）和表达式。如果 `X` 不是一个列表，那么 `X` 会被强制转换为一个列表。元素必须是为被应用的函数所要求的正确模式。

函数 `lapply()` 是最简单的，它只有两个参数，外加任何传递给被应用函数的参数。函数 `sapply()` 有四个参数，外加任何传递给被应用函数的额外参数。函数 `vapply()` 也有四个参数，外加任何传递给被应用函数的额外参数。

##### `lapply()` 函数

函数 `lapply()` 接收参数 `X` 和 `FUN`，外加任何传递给 `FUN` 的额外参数。函数 `FUN` 被应用于向量的每个元素，或列表的每个第二级元素。结果是一个列表。示例如下：

```
> b.list=list( 1:7, 3:4 )
> b.list
[[1]]
[1] 1 2 3 4 5 6 7
[[2]]
[1] 3 4
> lapply( b.list, sum )
[[1]]
[1] 28
[[2]]
[1] 7
```

你可以通过将运算符括在引号内来输入算术运算符。例如：

```
> lapply( 1:2, "^", 2 )
[[1]]
[1] 1
[[2]]
[1] 4
```

### `sapply()` 函数

函数 `sapply()` 同样适用于向量（包括列表）和表达式。该函数接收参数 `X` 和 `FUN`，然后是对 `FUN` 的任意参数，接着是参数 `simplify` 和 `USE.NAMES`。

参数 `simplify` 可以是逻辑值或字符串 `"array"`。参数 `simplify` 告诉 `sapply()`，如果为 `TRUE`，则将列表简化为一个向量或矩阵；如果设置为 `"array"`，则简化为一个数组。如果设置为 `FALSE`，则不进行简化，返回一个列表。默认值是 `TRUE`。

参数 `USE.NAMES` 是一个逻辑参数。对于类型为 `character` 的对象，参数 `USE.NAMES` 告诉 `sapply()` 使用该对象的元素作为结果的名称。默认值是 `TRUE`。示例如下：

```
> ac.list = list( one=1:5, two=3:7 )
> ac.list
$one
[1] 1 2 3 4 5
$two
[1] 3 4 5 6 7
> sapply( ab.list, sum )
one two
15  25
> a.char = paste0( "a", 7:10 )
> a.char
[1] "a7"  "a8"  "a9"  "a10"
> sapply( a.char, paste, "b", sep="" )
a7     a8     a9    a10
"a7b"  "a8b"  "a9b" "a10b"
> sapply( a.char, paste,  "b", sep="", USE.NAMES=F )
[1] "a7b"  "a8b"  "a9b" "a10b"
```

### `vapply()` 函数

函数 `vapply()` 按顺序接收参数 `X`、`FUN`、`FUN.VALUE`、任何传递给 `FUN` 的参数，以及 `USE.NAMES`。

参数 `FUN.VALUE` 是函数输出的一个结构。该结构是将 `FUN` 应用于 `X` 的单个元素后所得结果的结构。该结构中使用正确模式的虚拟值。虚拟元素的数量和模式必须正确。任何传递给 `FUN` 的额外参数都放在 `FUN.VALUE` 之后。`USE.NAMES` 的默认值是 `TRUE`。示例如下：

```
> set.seed( 382765 )
> ab.val=1:2
> vapply( ab.val, rnorm, matrix( .1, 2, 2 ), n=4, sd=1 )
, , 1
[,1]      [,2]
[1,] 1.701435 1.1422971
[2,] 2.068151 0.9604146
, , 2
[,1]     [,2]
[1,] 0.3541925 1.186276
[2,] 2.6841000 1.745577
```

在这个例子中，`ab.val` 是一个均值向量，被输入到函数 `rnorm()` 中，而传递给 `rnorm()` 的其他参数是 `n=4` 和 `sd=1`。

函数 `vapply()` 返回一个数组、矩阵或向量，其对象类型由参数 `FUN.VALUE` 指定。

你可以通过在 R 提示符下输入 `?lapply` 或使用 R Studio 中的“帮助”标签，找到有关 `lapply()`、`sapply()` 和 `vapply()` 的更多信息。

### `tapply()` 函数

函数 `tapply()` 将函数应用于交叉制表数据。其参数为 `X`、`IND`、`FUN`、任何传递给 `FUN` 的额外参数、`default` 和 `simplify`。`FUN` 的默认值是 `NULL`，`default` 的默认值是 `NA`，`simplify` 的默认值是 `TRUE`。

参数 `X` 必须是一个原子对象，并被强制转换为一个向量。该参数可以是一个由 `table()` 创建的列联表。`X` 的长度等于该列联表维度的乘积。

参数 `IND` 必须是一个可被强制转换为因子的向量，或一个由可被强制转换为因子的向量组成的列表。`X` 的长度与这些因子向量的长度必须全部相同。

`X` 的值是给定因子组合的观测值数量，其中因子组合是通过并列因子值得到的。如果组合重复，函数将无法正确工作。对于没有观测值的因子组合，无需输入零，但也可以包含零。

在不带函数的情况下使用 `tapply()` 会返回包含观测值的单元格的索引，而带函数使用时则给出因子交叉表，并将函数应用于单元格的内容。示例如下：

```
> cbind( c( "a", "b", "b", "c" ), c( 5, 5, 6, 5) )
[,1] [,2]
[1,] "a"  "5"
[2,] "b"  "5"
[3,] "b"  "6"
[4,] "c"  "5"
> tapply( 1:4, list( c( "a", "b", "b", "c" ), c( 5, 5, 6, 5) ) )
[1] 1 2 5 3
> tapply( 1:4, list( c( "a", "b", "b", "c" ), c( 5, 5, 6, 5) ),
"^", 3 )
5  6
a  1 NA
b  8 27
c 64 NA
```

在这个例子中，四个观测值分别位于单元格 a5、b5、b6 和 c5 中，这一点可以通过并列这两个因子列看出。总共有六个可能的单元格：a5、b5、c5、a6、b6 和 c6。第一次调用 `tapply()` 给出了四个表计数的单元格标识符。第二次调用将立方函数应用于表计数，并打印出结果的完整表格，对空单元格返回 NA。

你可以通过在 R 提示符下输入 `?tapply` 或使用 R Studio 中的“帮助”标签，找到有关 `tapply()` 的更多信息。

# `mapply()` 函数

函数 `mapply()` 接受一个原子向量或列表作为参数，并将一个函数应用于该向量或列表的每个元素。如果输入的对象不是原子向量或列表，`mapply()` 会尝试将其强制转换为原子向量或列表。结果对象的元素必须符合所应用函数的要求。`mapply()` 的返回值是一个原子向量、矩阵或列表。

`mapply()` 的参数包括 `FUN`、`...`、`MoreArgs`、`SIMPLIFY` 和 `USE.NAMES`。参数 `FUN` 是要应用的函数。参数 `...` 指代 `FUN` 所操作的原子向量或列表，可以是使用 `c()` 收集的多个列表和/或向量的集合。参数 `MoreArgs` 指代 `FUN` 的任何其他参数，默认值为 `NULL`。这些参数必须为 `list` 模式，每个参数对应一个独立的列表。

参数 `SIMPLIFY` 指示 `mapply()` 尝试将结果简化为向量或矩阵，默认值为 `TRUE`。参数 `USE.NAMES` 指示 `mapply()` 使用元素的名称作为输出的名称，如果向量是 `character` 模式，则直接使用字符本身。默认值为 `TRUE`。示例如下：

```
> set.seed( 382765 )
> a.mat = matrix( 1, 4, 4 )
> b.mat = matrix( runif( 9 ), 3, 3 )
> c.vec = 1:2
> mapply( det, list( a.mat, b.mat ) )
[1]  0.0000000 -0.3349038
> mapply( mean, c( list( a.mat, b.mat ), c.vec ) )
[1] 1.0000000 0.6208733 1.0000000 2.0000000
> mapply( mean, c( list( a.mat, b.mat ), list( c.vec ) ) )
[1] 1.0000000 0.6208733 1.5000000
```

这里，`det` 用于计算各元素的行列式，`mean` 用于计算各元素的均值。

另一个使用 `MoreArgs` 的示例如下：

```
> set.seed( 382765 )
> mapply( cor, c( list( a.mat, b.mat ), list( c.vec ) ),
list( y=1:4, y=1:3, y=3:4 ),
list( use="everything" ),
list( method="pearson" ) )
[[1]]
[,1]
[1,]   NA
[2,]   NA
[3,]   NA
[4,]   NA
[[2]]
[,1]
[1,]  0.1872769
[2,]  0.8836377
[3,] -0.4585219
[[3]]
[1] 1
Warning message:
In (function (x, y = NULL, use = "everything", method = c("pearson",  :
the standard deviation is zero
```

这里，函数是相关函数，参数 `y`、`use` 和 `method` 以列表形式提供。对于第一个矩阵，给出了四个 `y` 值，因此 `cor()` 被调用了四次，因为矩阵中有 16 个元素。对于第二个矩阵，给出了三个 `y` 值，因此 `cor()` 被调用了三次。对于第三个矩阵，给出了两个 `y` 值，因此 `cor()` 只被调用了一次。结果是一个包含三个元素的列表。列表中第一个元素出现 NA，是因为第一个矩阵只包含单一值，因此无法估算其相关性。

你可以在 R 提示符下输入 `?mapply` 或使用 R Studio 的 Help 选项卡，找到关于 `mapply()` 的更多信息。

## `eapply()` 函数

函数 `eapply()` 将一个函数应用于环境中的所有对象，并将结果以列表形式返回到父环境中。该函数接受五个参数：`env`、`FUN`、`...`、`all.names` 和 `USE.NAMES`。参数 `env` 是环境名称。参数 `FUN` 是要应用的函数。参数 `...` 提供传递给函数的任意参数，用逗号分隔。参数 `all.names` 是一个逻辑变量，指示是否包含名称以句点开头的对象，默认值为 `FALSE`。参数 `USE.NAMES` 是一个逻辑变量，指示结果列表的元素是否被赋予名称，默认值为 `TRUE`。

例如：

```
> nwenv = new.env()
> nwenv$a = 1:10
> nwenv$b = 11:20
> nwenv$c = rnorm( 100 )
> eapply( nwenv, sd )
$a
[1] 3.02765
$b
[1] 3.02765
$c
[1] 0.9947994
> ls( nwenv )
[1] "a" "b" "c"
```

这里，创建了一个环境并填充了三个数值对象。函数 `sd()`（用于计算数值对象中值的标准差的函数）被应用于这三个对象，计算得到的标准差以包含三个元素的列表形式返回到了 `.GlobalEnv` 中。

你可以在 R 提示符下输入 `?eapply` 或使用 R Studio 的 Help 选项卡，找到关于 `eapply()` 的更多信息。

## `sweep()` 和 `scale()` 函数

`sweep()` 函数作用于数组（包括矩阵以及已转换为矩阵的向量），而 `scale()` 函数作用于数值型的矩阵类对象。`sweep()` 函数使用一个函数（例如减法运算符）和一组值（例如列均值），对数组的某个（些）边际（例如矩阵的列）进行扫除。`scale()` 函数默认通过减去均值并除以每列的标准差，来对矩阵的列进行中心化和归一化。

### `sweep()` 函数

`sweep()` 函数接受参数 `x`、`MARGIN`、`STATS`、`FUN`、`check.margin` 和 `...`。参数 `x` 是数组，可以是任意原子模式。

参数 `MARGIN` 指定要进行扫除的边际。对于矩阵，`MARGIN` 等于 `1`、`2` 或 `1:2`（或 `c(1,2)`）。如果 `MARGIN` 等于 `1:2`，则对整个矩阵进行扫除，而不是按列或按行进行。对于超过二维的数组，`MARGIN` 可以是边际的任意子集，包括所有边际。

参数 `STATS` 提供用于扫除的值。例如，要使用列均值，可以应用 `apply()` 函数，即 `apply(mat, 2, mean)` 可作为 `STATS` 的一个值，其中 `mat` 是被扫除的矩阵。`STATS` 的值会循环使用。

参数 `FUN` 是要使用的函数。默认情况下，`FUN` 等于 `"-"`，即减法运算符，但 `FUN` 可以是任何对于数组值合法的函数。例如，`paste` 可用于字符模式的数组。

参数 `check.margin` 用于检查 `STATS` 的维度或长度是否与 `MARGIN` 给定的维度一致。如果不一致，仅发出警告。函数不会停止运行，而是循环使用 `STATS` 中的值。默认值为 `TRUE`。

参数 `...` 提供传递给函数 `FUN` 的任何额外参数。示例如下：

```
> a.mat = matrix( 1:8, 2, 4 )
> a.mat
     [,1] [,2] [,3] [,4]
[1,]    1    3    5    7
[2,]    2    4    6    8
> a.cent = sweep( a.mat, 2, apply( a.mat, 2, mean ) )
> a.cent
     [,1] [,2] [,3] [,4]
[1,] -0.5 -0.5 -0.5 -0.5
[2,]  0.5  0.5  0.5  0.5
> sweep( a.cent, 2, apply( a.mat, 2, sd ), "/" )
          [,1]       [,2]       [,3]       [,4]
[1,] -0.7071068 -0.7071068 -0.7071068 -0.7071068
[2,]  0.7071068  0.7071068  0.7071068  0.7071068
```

由于 `MARGIN` 被设为 `2`，函数 `mean()` 计算每列的均值，函数 `sd()` 计算每列的标准差。在第二条语句中，每列元素的均值被从该列的元素中减去。因为减法函数是默认函数，所以无需输入。在第三条语句中，列中已中心化的元素被该列的标准差所除。

注意，该函数返回一个矩阵。你可以在 R 提示符下输入 `?sweep` 或使用 R Studio 的 Help 选项卡，找到关于 `sweep()` 的更多信息。

#### 函数 `scale()`

函数 `scale()` 用于对矩阵的列进行缩放——即，将列中心化到指定的中心值，并将列缩放至指定的标准差。函数 `scale()` 接受三个参数：`x`、`center` 和 `scale`。参数 `x` 是一个矩阵或类似矩阵的数值对象（例如数据框或时间序列）。

参数 `center` 可以是逻辑值，也可以是长度与 `x` 的列数相等的数值向量。如果设置为 `TRUE`，则从列的每个元素中减去该列的均值。如果设置为数值向量，则从向量中每个数字对应的列的元素中减去该数字。如果设置为 `FALSE`，则不进行任何减法操作。默认值为 `TRUE`。

参数 `scale` 也可以是逻辑值或数值向量。如果 `scale` 设置为 `TRUE`，则每个已中心化（如果进行了中心化操作）的元素除以该列元素的标准差，其中 `NA` 被忽略，并且除以 n-1。如果设置为数值向量，则列的每个（中心化后的）元素除以向量中对应的数字。除以零会得到 `NaN`，但不会停止执行。如果 `scale` 设置为 `FALSE`，则不进行除法操作。默认值为 `TRUE`。示例如下：

```
> a.mat
     [,1] [,2] [,3] [,4]
[1,]    1    3    5    7
[2,]    2    4    6    8
> scale( a.mat )
           [,1]       [,2]       [,3]       [,4]
[1,] -0.7071068 -0.7071068 -0.7071068 -0.7071068
[2,]  0.7071068  0.7071068  0.7071068  0.7071068
attr(,"scaled:center")
[1] 1.5 3.5 5.5 7.5
attr(,"scaled:scale")
[1] 0.7071068 0.7071068 0.7071068 0.7071068
> a2.mat = matrix( c( 1:8, NA, 2 ), 2, 5 )
> a2.mat
     [,1] [,2] [,3] [,4] [,5]
[1,]    1    3    5    7   NA
[2,]    2    4    6    8    2
> scale( a2.mat, center=rep( 3, 5 ), scale=rep( 4, 5 ) )
      [,1] [,2] [,3] [,4]  [,5]
[1,] -0.50 0.00 0.50 1.00    NA
[2,] -0.25 0.25 0.75 1.25 -0.25
attr(,"scaled:center")
[1] 3 3 3 3 3
attr(,"scaled:scale")
[1] 4 4 4 4 4
```

请注意，`scale()` 返回缩放后的矩阵、用于中心化元素的值以及用于缩放元素的值。

更多信息，请在 R 提示符下输入 `?scale` 或使用 R Studio 中的“帮助”选项卡。

### 函数 `aggregate()`、`table()`、`tabulate()` 和 `ftable()`

与 `apply` 函数类似，函数 `aggregate()` 用于查找数据组的统计量。函数 `table()`、`tabulate()` 和 `ftable()` 用于根据数据创建列联表。

#### 函数 `aggregate()`

函数 `aggregate()` 根据另一个对象的值对一个对象的元素应用函数。被操作的对象可以是时间序列、数据框或可强制转换为数据框的对象。另一个对象的值必须是一个列表，其元素可解释为因子，并且在第二层级上，其长度必须等于数据框或时间序列的行数。该函数对数据框和时间序列的处理方式不同。

##### 数据框

对于数据框，参数包括 `x`、`by`、`FUN`、`...`、`simplify` 和 `drop`。参数 `x` 是一个数据框。参数 `by` 是一个模式为 `list` 的对象，由可解释为因子的元素组成。`by` 中的元素用于对 `x` 的行进行分组。

参数 `FUN` 是要应用的函数，`...` 是该函数的任何额外参数。参数 `simplify` 告诉 `aggregate()` 是否尝试将结果简化为向量或矩阵。默认值为 `TRUE`。参数 `drop` 是一个逻辑变量。如果为 `TRUE`，则丢弃 `by` 因子中未使用的组合。从 R 3.5.0 开始，默认值为 `TRUE`。

对于数据框，`aggregate()` 的结果是一个数据框。示例如下：

```
>  x=rep( 1:2, 3 )
>  y1=1:6
>  y2=7:12
>  a.df=data.frame( y1, y2, x )
> a.df
  y1 y2 x
1  1  7 1
2  2  8 2
3  3  9 1
4  4 10 2
5  5 11 1
6  6 12 2
> aggregate( a.df[,1:2], by=list( x ), FUN=mean )
  Group.1 y1 y2
1       1  3  9
2       2  4 10
```

该函数为 `x` 中的两个分组值找到每列中的均值。

对于数据框，也可以使用公式来对 `x` 进行分类，而不是使用参数 `by`。对于公式选项，参数包括 `formula`、`data`、`FUN`、`...`、`subset` 和 `na.action`。参数 `formula` 的形式为 `y~x`，其中 `y` 是数值型，可以有多列，`x` 是诸如 `x1` 或 `x1+x2` 的公式，其中 `x1` 和 `x2` 均可解释为因子。

参数 `data` 给出数据框的名称，并且必须包含。参数 `FUN` 是要应用的函数，`...` 包含 `FUN` 的任何额外参数。默认值为 `sum`。参数 `subset` 给出要操作的数据框的行。参数 `na.action` 给出处理缺失值的选择，是一个字符串。默认值为 `"na.omit"`，它告诉 `aggregate()` 忽略缺失值。示例如下：

```
> a.df
  y1 y2 x
1  1  7 1
2  2  8 2
3  3  9 1
4  4 10 2
5  5 11 1
6  6 12 2
> aggregate( cbind( y1, y2 )~x, data=a.df, sum, subset=1:3 )
  x y1 y2
1 1  4 16
2 2  2  8
```

基于 `x` 的值，对 `y1` 和 `y2` 的前三行进行了求和。

请注意，`by` 变量必须是一个列表，而公式的右侧不能是列表。

### 时间序列

时间序列同时具有**频率**和**周期**。在 R 中，频率是周期的倒数，反之亦然。例如，可以将一年作为感兴趣的周期。那么，月份具有频率 12，其子周期为 1/12。

对于时间序列，参数包括 `x`、`nfrequency`、`FUN`、`ndeltat`、`ts.eps` 和 `...`。参数 `x` 必须是一个时间序列。参数 `nfrequency` 是在 `FUN` 对时间序列操作后，每个周期内的子周期数量。该值必须能整除原始时间序列的频率。对于月度时间序列，通过将 `nfrequency` 设置为 4 可以聚合为季度数据。该参数默认值为 `1`。（原始时间序列频率除以 `nfrequency` 得到的是被分组在一起的元素数量——`FUN` 对这些分组元素进行操作。）

参数 `FUN` 是要应用的函数，`...` 提供传递给 `FUN` 的任何额外参数。参数 `...` 位于参数列表的末尾。函数 `FUN` 必须对时间序列的值合法，默认值为 `sum`。

参数 `ndeltat` 告知 `aggregate()` 输出结果的子周期长度，默认值为 `1`。该参数的值是 1 除以 `nfrequency` 的结果。原始时间序列的频率与 `ndeltat` 的乘积必须是一个整数。

可以设置 `nfrequency` 或 `ndeltat` 中的任意一个，但不能同时设置两者。`nfrequency` 与 `ndeltat` 的倒数的乘积等于原始时间序列的频率；若 `nfrequency` 小于 1，则该乘积等于其倒数。

参数 `ts.eps` 给出了判断 `nfrequency` 能否整除时间序列频率的容忍度。默认情况下，`ts.eps` 等于 `getOption("ts.eps")`，该值可以通过在 R 提示符下输入 `options("ts.eps")` 来查看。该值为数值型，可以手动设置。

示例如下：

# R语言中 `aggregate()` 函数与表格操作

```r
> a.ts=ts( cbind( 1:12, 11:22 ), start=c( 1, 1 ), freq=4 )
> a.ts
Series 1 Series 2
1 Q1        1       11
1 Q2        2       12
1 Q3        3       13
1 Q4        4       14
2 Q1        5       15
2 Q2        6       16
2 Q3        7       17
2 Q4        8       18
3 Q1        9       19
3 Q2       10       20
3 Q3       11       21
3 Q4       12       22
> aggregate( a.ts, nfreq=2 )
Time Series:
Start = c(1, 1)
End = c(3, 2)
Frequency = 2
Series 1 Series 2
1.0        3       23
1.5        7       27
2.0       11       31
2.5       15       35
3.0       19       39
3.5       23       43
> aggregate( a.ts, ndelt=1/2 )
Time Series:
Start = c(1, 1)
End = c(3, 2)
Frequency = 2
Series 1 Series 2
1.0        3       23
1.5        7       27
2.0       11       31
2.5       15       35
3.0       19       39
3.5       23       43
> aggregate( a.ts, nfreq=1/2 )
Time Series:
Start = 1
End = 1
Frequency = 0.5
Series 1 Series 2
1       36      116
> aggregate( a.ts, ndelt=2 )
Time Series:
Start = 1
End = 1
Frequency = 0.5
Series 1 Series 2
1       36      116
```

请注意，`nfreq` 可以小于 1，但必须满足与 `freq` 相乘后得到整数。在 `nfreq=1/2` 的示例中，前八行被求和，但后四行被忽略。

你可以通过在 R 提示符下输入 `?aggregate` 或使用 R Studio 中的“帮助”选项卡来获取关于 `aggregate()` 的更多信息。

## 函数 `table()`、`as.table()` 和 `is.table()`

有三个与使用 `table()` 创建表格相关的函数。函数 `table()` 从原子数据或某些列表中创建列联表。数据必须能够被解释为因子。其结果属于 `table` 类。函数 `as.table()` 尝试将对象强制转换为 `table` 类。函数 `is.table()` 测试某个对象是否为 `table` 类。

`table()` 的参数包括 `...`、`exclude`、`useNA`、`dnn` 和 `deparse.level`。

参数 `...` 指代要被交叉分类的一个或多个对象。对象之间用逗号分隔，对于原子对象，它们必须具有相同的长度。对于列表对象，第二层级的元素必须全部具有相同的长度且是原子类型。原子对象和列表对象不能在同一个 `table()` 调用中混合使用。

参数 `exclude` 给出了要从列联表中排除的值。默认情况下，`exclude` 等于 `if(useNA=="no") c(NA, NaA)`，这告知 `table()` 如果参数 `useNA` 等于 `"no"`，则不为缺失值或非法值（例如 1 除以 0）设置水平。

参数 `useNA` 是一个字符参数，可以取值 `"no"`、`"ifany"` 或 `"always"`。对于 `"no"`，不为缺失值设置水平。对于 `"ifany"`，如果存在缺失值则设置一个水平。对于 `"always"`，总是为缺失值设置一个水平。默认水平是 `"no"`。

参数 `dnn` 是一个列表参数，用于指定列联表的维度名称。默认值为 `list.names(...)`。函数 `list.names()` 在 `table()` 内部定义，用于给出被制表的维度的名称。

参数 `deparse.level` 是一个整数参数，可以取值 `0`、`1` 或 `2`。当未提供 `dnn` 时，该参数控制 `list.names()` 的行为。对于 `0`，不给出名称。对于 `1`，使用列名称。对于 `2`，对列名称进行反解析。默认值为 `1`。示例如下：

```r
> set.seed(203846)
> a1.samp=sample( 3, 100, replace=T )
> a2.samp=sample( 3, 100, replace=T )
> table( a1.samp, a2.samp )
a2.samp
a1.samp  1  2  3
1 12 10 14
2 13  9  9
3 15  8 10
> a2.samp[10]=NA
> table( a1.samp, a2.samp )
a2.samp
a1.samp  1  2  3
1 12 10 14
2 12  9  9
3 15  8 10
> table( a1.samp, a2.samp, useNA="ifany" )
a2.samp
a1.samp  1  2  3
1 12 10 14    0
2 12  9  9    1
3 15  8 10    0
```

请注意，第二个表格没有包含缺失值，但第三个表格包含了。

函数 `as.table()` 接受参数 `x` 和 `...`。参数 `x` 是要强制转换为 `table` 类的对象。该参数必须是 `numeric` 模式。参数 `...` 为下层函数提供任何参数。

函数 `is.table()` 接受参数 `x`，如果 `x` 是 `table` 类则返回 `TRUE`，否则返回 `FALSE`。

你可以通过在 R 提示符下输入 `?table()` 或使用 R Studio 中的“帮助”选项卡来获取关于 `table()`、`as.table()` 和 `is.table()` 的更多信息。

## 函数 `tabulate()`

函数 `tabulate()` 将数值或因子对象强制转换为向量，并对结果进行分箱。参数包括 `bin` 和 `nbins`。参数 `bin` 是要分箱的对象。如果该对象不是整数或因子对象，则元素会被向下取整为整数。得到的整数必须为正数。如果存在非法元素，则该元素会被忽略。

参数 `nbins` 给出了要分箱的最大整数，默认值等于 `max(1, bin, na.rm=T)`——即 `bin` 中的最大值，假设 `bin` 中的最大值大于 1。默认情况下，NAs 会被移除。

如果 `nbins` 小于 `bin` 中的最大值，则只有值小于或等于 `nbins` 的那些元素会被分箱。从 1 到 `nbins` 之间的所有整数都会被分箱，即使给定箱中的元素个数为零。该函数创建一个没有标签的向量。分箱总是从 1 开始。示例如下：

```r
> tabulate( c( -3.5, .9, 1, 4, 5.6, 5.4, 4, 1, 3) )
[1] 2 0 1 2 2
> tabulate( c( -3.5, .9, 1, 4, 5.6, 5.4, 4, 1, 3 ), nbins=3 )
[1] 2 0 1
```

在这个例子中，化简后的对象中有两个 1、零个 2、一个 3、两个 4 和两个 5。

当需要所有箱（包括元素个数为零的箱）时，函数 `tabulate()` 非常有用。你可以通过在 R 提示符下输入 `?tabulate` 或使用 R Studio 中的“帮助”选项卡来获取关于 `tabulate()` 的更多信息。

# 函数 `ftable()`

函数 `ftable()` 从一个列联表创建一个矩阵——即一个扁平表。其参数为 `...`、`exclude`、`row.vars` 和 `col.vars`。参数 `...` 可以是那些能被强制转换为向量并解释为因子的对象，各对象之间用逗号分隔。该参数也可以是一个列表，其元素能被解释为因子，或者该参数可以是 `table` 或 `ftable` 类的对象。

参数 `exclude` 指定在构建扁平表时要排除的值。默认情况下，`exclude` 等于 `c(NA, NaN)`。

参数 `row.vars` 和 `col.vars` 指定要放在行和列上的维度。这些值可以从 1 到表格的维度数——换句话说，一个三维表格可以设置 `row.vars` 和 `col.vars` 为 `1:2` 和 `3`；或 `2:1` 和 `3`；或 `1` 和 `3`；或 `c(3,1)` 和 `2`；等等。示例如下：

```
> a.list = list( 1:2, 3:4, 5:6 )
> ftable( a.list )
x.3 5 6
x.1 x.2
1   3       1 0
4       0 0
2   3       0 0
4       0 1
> a1 = 1:2
> a2 = 3:4
> a3 = 5:6
> ftable( a1, a2, a3, row.vars=3, col.vars=2:1 )
a2 3   4
a1 1 2 1 2
a3
5     1 0 0 0
6     0 0 0 1
> a.table = table( 1:2, 3:4, 5:6 )
> ftable( a.table, row.vars=2, col.vars=3 )
5 6
3  1 0
4  0 1
```

在这些示例中，两个观测值为 `(1,3,5)` 和 `(2,4,6)`。

您可以通过在 R 提示符下输入 `?ftable` 或使用 R Studio 中的“帮助”选项卡来查找关于 `ftable()` 的更多信息。

## 一些字符串函数

有许多函数用于在字符串中搜索模式，以及基于匹配将字符串的一部分替换为其他字符串。本节涵盖 `grep()` 函数、`sub()` 函数、`regex` 函数、`str` 函数以及字符大小写转换函数。

### grep 函数

`grep()` 和 `grepl()` 函数用于在字符串向量中搜索与模式匹配的内容。函数 `grep()` 返回包含该模式的字符串的索引或值。函数 `grepl()` 返回一个长度与字符向量相同的逻辑向量，对于字符向量的每个元素，如果匹配，则元素等于 `TRUE`，如果不匹配，则等于 `FALSE`。

`grep()` 的参数有 `pattern`、`x`、`ignore.case`、`perl`、`value`、`fixed` 和 `useBytes`。参数 `pattern` 是一个字符串，或者是一个可以使用 `as.character()` 强制转换为字符串的对象。如果该参数包含多个元素，则只使用第一个元素。参数 `x` 是要在其中查找匹配项的字符向量。参数 `ignore.case` 如果设置为 `TRUE`，则告诉 `grep()` 在匹配时忽略大小写。默认值为 `FALSE`。

参数 `perl` 和 `fixed` 告诉 `grep()` 进行何种类型的匹配。（更多信息请参见 `regex` 的帮助页面。）这两个参数默认值都是 `FALSE`。参数 `value` 如果设置为 `TRUE`，则告诉 `grep()` 返回元素的值；如果设置为 `FALSE`，则返回元素的索引。默认值为 `FALSE`。参数 `useBytes` 如果设置为 `TRUE`，则告诉 `grep()` 按字节而非字符进行匹配。默认值为 `FALSE`。参数 `inverse` 如果设置为 `TRUE`，则告诉 `grep()` 返回不包含匹配项的元素，而不是包含匹配项的元素。默认值为 `FALSE`。

一个示例：

```
> ab.char=c( "achar1", "achar2", "achar3" )
> ab.char
[1] "achar1" "achar2" "achar3"
> grep( "achar", ab.char )
[1] 1 2 3
> grep( "1", ab.char, value=T )
[1] "achar1"
> grep( "Achar", ab.char )
integer(0)
> grep( "Achar", ab.char, ignore.case=T )
[1] 1 2 3
> grep( "Achar", ab.char, ignore.case=T, invert=T )
integer(0)
```

函数 `grepl()` 接受与 `grep()` 相同的参数，只是没有 `value` 或 `invert` 参数。该函数返回一个逻辑向量，例如：

```
> grepl( "1", ab.char )
[1]  TRUE FALSE FALSE
> grepl( "Achar", ab.char )
[1] FALSE FALSE FALSE
```

函数 `agrep()` 和 `agrepl()` 类似于 `grep()` 和 `grepl()`，不同之处在于 `agrep()` 和 `agrepl()` 执行“模糊”匹配。例如：

```
> grepl( "Achar", ab.char )
[1] FALSE FALSE FALSE
> agrepl( "Achar", ab.char )
[1] TRUE TRUE TRUE
```

有关如何进行匹配的更多信息，请参见 `agrep()` 的帮助页面。

函数 `grepRaw()` 对原始向量进行模式匹配。该函数接受参数 `pattern`、`x`、`offset`、`ignore.case`、`value`、`fixed`、`all` 和 `invert`。

参数 `pattern` 是要匹配的模式，它可以是一个原始向量或单个字符串。参数 `x` 也是一个原始向量或单个字符串，是用于搜索模式的对象。在 `grepRaw()` 中，搜索之前，会使用函数 `charToRaw()` 将字符串转换为原始向量。

参数 `offset` 给出开始搜索的原始向量的索引。该值必须能够被强制转换为正整数。如果该值是长度大于 1 的对象，则只使用第一个元素。`offset` 的默认值为 `1L`。

参数 `ignore.case` 如果设置为 `TRUE`，则告诉 `grepRaw()` 在给定一个字母时，同时匹配大写字母和小写字母。默认值为 `FALSE`。

参数 `value` 如果设置为 `TRUE`，则返回包含该匹配的第一个原始向量或包含所有匹配的原始向量列表，具体取决于参数 `all` 是 `FALSE` 还是 `TRUE`。如果 `value` 为 `FALSE`，则返回第一个匹配的第一个元素的索引，或者所有匹配的第一个元素的索引，具体取决于参数 `all` 的值——`FALSE` 或 `TRUE`。`value` 的默认值为 `FALSE`。

参数 `all` 告诉 `grepRaw()`：如果设置为 `FALSE`，则只返回第一个匹配项；如果设置为 `TRUE`，则返回所有匹配项。默认值为 `FALSE`。参数 `fixed` 和 `invert` 的定义与 `grep()` 中相同，默认值均为 `FALSE`。

示例：

```
> a=charToRaw( "abc123" )
> a
[1] 61 62 63 31 32 33
> grepRaw( "b", a )
[1] 2
> grepRaw( "b", a, value=T )
[1] 62
> grepRaw( "B", a, value=T, ignore.case=T )
[1] 62
> grepRaw( "ab", "abab" )
[1] 1
> grepRaw( "ab", "abab", all=T )
[1] 1 3
> grepRaw( "ab", "abab", value=T, all=T )
[[1]]
[1] 61 62
[[2]]
[1] 61 62
> grepRaw( "ab", "Abab", value=T, all=T )
[[1]]
[1] 61 62
> grepRaw( "ab", "Abab", value=T, all=T, ignore.case=T )
[[1]]
[1] 41 62
[[2]]
[1] 61 62
```

函数 `sub()` 和 `gsub()` 用于将对象（可强制转换为字符向量）的元素中的子字符串替换为新字符串。这两个函数的参数包括 `pattern`、`replacement`、`x`、`ignore.case`、`perl`、`fixed` 和 `useBytes`。唯一的新参数是 `replacement`，即替换值。替换值必须是可强制转换为字符串的对象。如果替换对象包含多个元素，则仅使用第一个元素，并给出警告。函数 `sub()` 替换 `x` 每个元素中模式的第一个匹配项。函数 `gsub()` 替换所有匹配项。

例如：

```
> sub( "b1", "c", c( "b1b2b1", "cb1" ) )
[1] "cb2b1" "cc"
> gsub( "b1", "c", c( "b1b2b1", "cb1" ) )
[1] "cb2c" "cc"
```

## `regexpr()`、`gregexpr()` 和 `regexec()` 函数

函数`regexpr()`、`gregexpr()`和`regexec()`返回字符向量中字符串的位置和长度，以及一些其他属性（如表达式类型）。这三个函数都返回一个列表。如果未找到匹配项，则返回负一。这三个函数的参数包括`pattern`、`text`、`ignore.case`、`perl`、`fixed`和`useBytes`。其中，`text`是用于搜索模式的对象。其他参数如前所述。

### `regexpr()` 函数

函数`regexpr()`为`text`的每个元素查找模式的第一个匹配项，并返回一个向量和若干属性。该向量是整数向量，对于`text`中的每个元素，该整数表示模式在该元素中首次出现的位置。如果模式不在该元素中，则使用负一。

结果的第一个属性是`"match.length"`——一个整数向量，包含模式第一个匹配项的字符数或字节数（取决于`useBytes`是`FALSE`还是`TRUE`）。同样，如果没有匹配项，则使用负一。另外两个可能的属性是`"index.type"`和`"useBytes"`。

要将向量与属性分离，可以对结果使用函数`as.vector()`。要访问属性，可以使用函数`attr()`。

例如：

```
> a=regexpr( "ab", c( "ababab", "ba" ) )
> a
[1]  1 -1
attr(,"match.length")
[1]  2 -1
attr(,"index.type")
[1] "chars"
attr(,"useBytes")
[1] TRUE
> as.vector( a )
[1]  1 -1
> attr( a, "match.length" )
[1]  2 -1
```

### `gregexpr()` 函数

函数`gregexpr()`在`text`的每个元素中查找参数`pattern`的所有匹配项。该函数采用与`regexpr()`相同的参数，并返回一个与`text`长度相同的列表。列表的第一个元素包含`text`第一个元素的信息；第二个元素包含第二个元素的信息，依此类推。列表中每个元素的结构与`regexpr()`的输出类似，只是引用了该元素中的所有匹配项，而非每个元素中的第一个匹配项。

例如：

```
> ag=gregexpr( "ab", c( "ababab", "ba" ) )
> ag
[[1]]
[1] 1 3 5
attr(,"match.length")
[1] 2 2 2
attr(,"index.type")
[1] "chars"
attr(,"useBytes")
[1] TRUE
[[2]]
[1] -1
attr(,"match.length")
[1] -1
attr(,"index.type")
[1] "chars"
attr(,"useBytes")
[1] TRUE
> as.vector( ag[[1]] )
[1] 1 3 5
> as.vector( ag[[2]] )
[1] -1
```

### `regexec()` 函数

函数`regexec()`的输出形式与`regexpr()`相同，但与`gregexpr()`类似。

### 相关帮助

有关`grep()`、`grepl()`、`sub()`、`gsub()`、`regexpr()`、`gregexpr()`和`regexec()`的更多信息，请在 R 提示符下输入`?grep`或使用 R Studio 中的“帮助”选项卡。有关`agrep()`和`grepRaw()`的更多信息，请在 R 提示符下输入`?agrep`和`?grepRaw`，或使用 R Studio 中的“帮助”选项卡。

## 用于操作字符串大小写的函数

可用于更改字符串大小写的三个函数是`tolower()`、`toupper()`和`chartr()`。函数`tolower()`和`toupper()`接受一个参数`x`，该参数可以是任何可通过使用`as.character()`强制转换为字符的向量。这些函数将整个向量的大小写更改为小写或大写。非字母字符不变。

例如：

```
> tolower( c( "Jane Doe", "John Doe" ) )
[1] "jane doe" "john doe"
> toupper( c( "Jane Doe", "John Doe" ) )
[1] "JANE DOE" "JOHN DOE"
```

函数`chartr()`将向量`x`中的字符更改为其他字符。该函数接受三个参数：`old`、`new`和`x`。参数`old`和`new`必须是字符串且长度相同。要替换的字符构成`old`，而替换字符位于`new`中，两者之间是一一对应的关系。字符串中的每个字符都会被单独评估。字符可以通过范围来引用。

例如：

```
> chartr( "ao", "oa", c( "Jane Doe", "John Doe" ) )
[1] "Jone Dae" "Jahn Dae"
> chartr( "a-e", "ABCDE", c( "Jane Doe", "John Doe" ) )
[1] "JAnE DoE" "John DoE"
```

有关`tolower()`、`toupper()`和`chartr()`的更多信息，可以在 R 提示符下输入`?tolower`或使用 R Studio 中的“帮助”选项卡找到。

## 函数 `substr()`、`substring()` 和 `strsplit()`

函数`substr()`、`substring()`和`strsplit()`通过指定字符串中的操作位置来处理字符串。

### `substr()` 和 `substring()` 函数

函数`substring()`接受三个参数：`x`、`start`和`stop`。参数`x`是一个字符向量；参数`start`告诉`substr()`在选取或修改子字符串之前，需要进入字符字符串多深的位置；参数`stop`告诉`substr()`在哪里停止。这两个值都应为正整数。其中任何一个整数都可以大于字符串中的字符数。`start`和`stop`都没有默认值。

例如：

```
> substr( c( "Jane Doe", "John Doe", "Ms. X" ), 2, 7 )
[1] "ane Do" "ohn Do" "s. X"
> substr( c( "Jane Doe", "John Doe", "Ms. X" ), 6, 7 )
[1] "Do" "Do" ""
> a.str=c( "Jane Doe", "John Doe", "Ms. X" )
> substr( a.str, 6, 7 ) = "soA"
> a.str
[1] "Jane soe" "John soe" "Ms. X"
```

在本示例的第一部分中，`substr()`作用于向量每个元素中的第二到第七个字符。在第二部分中，`substr()`作用于每个元素中的第六到第七个字符。请注意，第三个元素只有五个字符。在第三部分中，仅替换了两个字符，即第六和第七个字符。

函数`substring()`的功能与`substr()`非常相似，区别在于其三个参数是`text`、`first`和`last`。`last`的默认值为`1000000L`，因此不必指定。

使用上述示例中的`a.str`：

```
> a.str
[1] "Jane Doe" "John Doe" "Ms. X"
> substring( a.str, 2 ) = "osa"
> a.str
[1] "Josa Doe" "Josa Doe" "MosaX"
```

### `strsplit()` 函数

函数`strsplit()`根据一个字符串或可强制转换为字符串的对象，将字符向量的元素分割成一个由更小向量组成的列表。该函数接受五个参数：`x`、`split`、`fixed`、`perl`和`useBytes`。参数`fixed`、`perl`和`useBytes`如前所述，详见帮助页面。参数`x`是要分割的对象，必须是一个字符向量。参数`split`是用于分割的字符串。字符串中的值不会包含在分割结果中。要按句点进行分割，请使用字符串`"[.]"`而不是`"."`。要将字符串分割成单个字符，请将`string`设置为`""`、`NULL`或`character(0)`。

例如：

```
> strsplit( "a.b.b", "b." )
[[1]]
[1] "a." "b"
> strsplit( "a.b.b", "." )
[[1]]
[1] "" "" "" "" ""
> strsplit( "a.b.b", "[.]" )
[[1]]
[1] "a" "b" "b"
> strsplit( c( "a.b.b", "d.f.d" ), "" )
[[1]]
[1] "a" "." "b" "." "b"
[[2]]
[1] "d" "." "f" "." "d"
```
```

关于 `substr()` 和 `substring()` 的更多信息，可以在 R 提示符下输入 `?substr` 或使用 RStudio 的帮助选项卡找到。对于 `strsplit()`，请输入 `?strsplit` 或使用帮助选项卡。
