# 函数 `write.matrix()`

函数 `write.matrix()` 位于 `MASS` 包中，该包不是默认加载的包。由于 `MASS` 在安装 R 时默认安装，因此可以通过在 R 提示符下输入 `library(MASS)` 来加载它。根据 CRAN 作者的说法，对于大型数据集，`write.matrix()` 比 `write.table()` 快得多，因此如果矩阵或 `data.frame` 很大，并且数据框是合适的，那么该函数可能更可选。

## 参数说明

该函数包含参数 `x`、`file`、`sep` 和 `blocksize`。参数 `x` 是要导出的对象，应该是一个矩阵或一个仅包含一种模式对象的 `data.frame`。如果模式混合，可能会发生一些奇怪的事情。该函数只以一种模式导出，这就是 `write.matrix()` 比 `write.table()` 更快的原因。

参数 `file` 指定写入的位置。对于硬盘驱动器上的地址，该参数是字符模式，并且相对于工作空间或绝对路径。默认值为 `""`，它将输出定向到控制台；如果 `stdout()` 不是控制台，则定向到 `stdout()` 的值。

参数 `sep` 是一个字符串，指定输出元素之间的分隔符。该参数默认为空格。

参数 `blocksize` 没有默认值，也不需要输入。如果输入了，该参数会告诉 `write.matrix()` 一次传输的数据块大小。根据 CRAN 作者的说法，该值应在可用内存允许的情况下尽可能大。

## 示例

这是一个示例。对象 `mat` 是一个矩阵，对象 `mat.df` 是一个单一模式的 `data.frame`，对象 `mat.df.x` 是一个混合了数值和字符模式的 `data.frame`。`file` 的默认值如下所示，因此输出会发送到控制台。

```r
> mat = matrix( 1:4, 2, 2, dimnames=list( c( "r1", "r2"), c( "c1", "c2" ) ) )
> mat
c1 c2
r1  1  3
r2  2  4
> write.matrix( mat )
c1 c2
1 3
2 4
> mat.df=data.frame( mat )
> mat.df
c1 c2
r1  1  3
r2  2  4
> write.matrix( mat.df )
c1 c2
1 3
2 4
> mat.df.x = data.frame( mat, c( "art", "birth" ) )
> mat.df.x
c1 c2 c..art....birth..
r1  1  3               art
r2  2  4             birth
> write.matrix(mat.df.x)
c1 c2 c..art....birth..
1     3     art
2     4     birth
```

## 更多信息

您可以在 R 提示符下输入 `?MASS::write.matrix` 或在 R Studio 中加载 `MASS` 然后使用“帮助”选项卡，找到关于 `write.matrix()` 的更多信息。