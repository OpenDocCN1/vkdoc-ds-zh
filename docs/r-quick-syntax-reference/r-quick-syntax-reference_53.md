# 值替换：NA、NaN、Inf 和 -Inf

本节内容涉及缺失数据（`NA`）或非法元素（`NaN`、`Inf` 或 `-Inf`）。假设你想用一个数值（例如 `0`）来替换缺失值。直观的方法可能会输入如下代码：

```
mat[ mat==NA ] = 0
```

但这样行不通。正确的方法应该输入以下代码：

```
mat [ is.na( mat ) ] = 0
```

例如：

```
> mat = matrix( c( 1, NA, 3, 4 ), 2, 2 )
> mat
     [,1] [,2]
[1,]    1    3
[2,]   NA    4
> mat[ mat==NA ]=2
> mat
     [,1] [,2]
[1,]    1    3
[2,]   NA    4
> mat[ is.na( mat ) ]=2
> mat
     [,1] [,2]
[1,]    1    3
[2,]    2    4
```

同样的方法也适用于非法值。在 R 语言中，`NaN`、`Inf` 和 `-Inf` 是为非法运算定义的。例如：

```
> 1/0
[1] Inf
> -1/0
[1] -Inf
> 0/0
[1] NaN
> log( -1 )
[1] NaN
Warning message:
In log(-1) : NaNs produced
```

在这个例子中，正数除以零得到正无穷；负数除以零得到负无穷；零除以零未定义，因此返回 `NaN`。尝试计算负一的自然对数会返回 `NaN`并附带一个警告，因为负一的对数未定义。

函数 `is.finite()`、`is.infinite()` 和 `is.nan()` 可替代 `is.na()`，分别用于检测有限值、`Inf` 和 `-Inf`、以及 `NaN` 元素。例如：

```
> mat = matrix( c( 1, NaN, Inf, -Inf ), 2, 2 )
> mat
     [,1] [,2]
[1,]    1  Inf
[2,]  NaN -Inf
> mat[ is.finite( mat ) ]=2
> mat
     [,1] [,2]
[1,]    2  Inf
[2,]  NaN -Inf
> mat[ is.infinite( mat ) ]=3
> mat
     [,1] [,2]
[1,]    2    3
[2,]  NaN    3
> mat[ is.nan( mat ) ]=4
> mat
     [,1] [,2]
[1,]    2    3
[2,]    4    3
```

注意，`is.infinite()` 将 `Inf` 和 `-Inf` 同等对待。

函数 `sign()` 对于等于 `-Inf` 的参数返回 `-1`。因此，处理符号问题的一个简单方法是先获取对象的符号，然后在为 `-Inf` 赋值后，将替换后对象的绝对值乘以符号对象。例如：

```
> mat=matrix( c( 1, 2, Inf, -Inf ), 2, 2 )
> mat
     [,1] [,2]
[1,]    1  Inf
[2,]    2 -Inf
> sg.mat = sign( mat )
> sg.mat
     [,1] [,2]
[1,]    1    1
[2,]    1   -1
> mat[ is.infinite( mat ) ] = 4
> mat
     [,1] [,2]
[1,]    1    4
[2,]    2    4
> mat = sg.mat*abs( mat )
> mat
     [,1] [,2]
[1,]    1    4
[2,]    2   -4
```

你可以在 R 提示符下输入 `?is.na` 获取关于 `NA` 和 `is.na()` 的更多信息。你可以在 R 提示符下输入 `?is.finite` 获取关于 `NaN`、`Inf`、`-Inf`、`is.nan()`、`is.finite()` 和 `is.infinite()` 的更多信息。或者你也可以使用 R Studio 的帮助页面。

