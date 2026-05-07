# 对象的类

在 R 中，对象除了具有模式和类型，还属于某个类。类揭示了对象的结构信息。S3 和 S4 在类方面有所不同。在 S3 中，R 对象归属于一些特定的类。在 S4 中，用户为 S4 对象定义类。S3 中的类称为非正式类，而 S4 中的类称为正式类。本章将涵盖这两种类。



## 关于类的一些基础知识

S3 类是 S3 对象的属性，通常不由用户直接分配。给定一个对象，可以通过使用 `class()` 函数来查找该对象的类。如果一个对象在其所属的包中未被赋予类，那么该对象的类就是其自身的模式。例如，一个模式为 `function` 的对象，其类也是 `function`。

许多函数的输出将拥有一个特定于该函数的类属性。例如，使用 `lm()` 函数拟合线性模型的输出，其类为 `lm`。此外，对象可以属于多个类。一个例子是使用广义线性模型函数 `glm()` 拟合的模型，其输出拥有 `glm` 和 `lm` 两个类。

从更技术性的角度来看，根据 `class()` 的帮助页面，一个对象的类就是该对象所继承的类。因此，`lm()` 的输出继承自 `lm`，而 `glm()` 的输出则同时继承自 `lm` 和 `glm`。

一个对类很有用的函数是 `methods()`。输入 `methods(class=name)`，其中 `name` 是类的名称，将显示专门编写用于应用于该类对象的函数。例如：

```
> methods(class=lm)
[1] add1          alias          anova            case.names
[5] coerce        confint        cooks.distance   deviance
[9] dfbeta        dfbetas        drop1            dummy.coef
[13] effects       extractAIC     family           formula
[17] hatvalues     influence      initialize       kappa
[21] labels        logLik         model.frame      model.matrix
[25] nobs          plot           predict          print
[29] proj          qr             residuals        rstandard
[33] rstudent      show           simulate         slotsFromS3
[37] summary       variable.names vcov
see '?methods' for accessing help and source code
```

S4（形式）类是 S4 方法的起点。一个 S4 类包含一个用户定义的类名称、该类关联方法要使用的变量，以及这些变量的类。

在 R 提示符下输入 `?class` 或使用 R Studio 的“帮助”选项卡，可以获取有关 S3 和 S4 类以及继承的更多信息。

## 向量

尽管没有名为 `vector` 的类，但向量作为最基本的对象类型之一，值得讨论。对于原子模式向量，向量是仅有一维的元素集合。其类就是向量的模式，但整数向量除外，它采用 `integer` 类。向量之所以重要的另一个原因是，对于 `as.name()` 函数（其中 `name` 是原子模式的名称，除了模式 `NULL`），`as.name()` 返回一个向量。

函数 `vector()`、`as.vector()` 和 `is.vector()` 是存在的，它们的工作方式与针对模式的类似函数有些相似。函数 `vector()` 接受参数 `mode` 和 `length`，并创建一个指定模式和长度的向量。可接受的模式是原子模式——除了 `NULL`、`list` 模式和 `expression` 模式。其他模式会导致错误。

对于原子模式，

```
vector( mode="name", length=n )
```

的行为与

```
name( length=n ),
```

相同，其中 `name` 是模式的名称，`n` 是长度参数。请注意，在调用 `vector()` 时，`name` 必须用引号括起来。对于 `list` 模式，`vector()` 返回一个由 `NULL` 组成的列表，列表的长度由长度参数给出。如果将模式设置为 `expression`，`vector()` 会生成一个表达式，其参数为 `NULL`，`NULL` 的数量由长度参数给出。

函数 `as.vector()` 尝试将对象强制转换为向量。对于某些对象，`as.vector()` 只是传递该对象而不创建向量。对于其他一些对象，如果运行 `as.vector()` 函数，则会返回一个错误。

对于矩阵和数组，`as.vector()` 会移除维度信息（例如，矩阵中的列名以及行数和列数），并返回一个由矩阵或数组元素组成的向量。向量的元素顺序从矩阵或数组的第一个维度开始，依次贯穿所有维度。例如：

```
> a=array( 1:8, c( 2, 2, 2 ) )
> dimnames( a )=list( c( "a", "b" ), c( "m", "n" ), c( "y", "z" ) )
>
> a
, , y
  m n
a 1 3
b 2 4
, , z
  m n
a 5 7
b 6 8
> as.vector( a )
[1] 1 2 3 4 5 6 7 8
```

这里，`c()` 函数被用来为 2x2x2 的 `array()` 创建维度向量，并为数组的三个维度创建名称。

对于模式为 `list` 的对象，`as.vector()` 会直接传递该列表。根据列表的结构，在结果上运行 `is.vector()` 可能返回 `TRUE` 或 `FALSE`。模式不会改变。

对于模式为 `function` 的对象，`as.vector()` 会返回一个错误。

对于模式为 `call` 的对象，`as.vector()` 会传递该对象但不创建向量。模式不会改变。

对于模式为 `environment` 的对象，`as.vector()` 会返回一个错误。

对于模式为 `expression` 的对象，`as.vector()` 会传递该表达式，并且结果在 `is.vector()` 上返回 `TRUE`。模式不会改变。

对于 `S4` 模式，`as.vector()` 会返回一个错误。

函数 `is.vector()` 在对象是向量时返回 `TRUE`，否则返回 `FALSE`，尽管一些看起来不像向量的对象也可能返回 `TRUE`。

关于 `vector()`、`as.vector()` 和 `is.vector()` 的更多信息，可以在 R 提示符下输入 `?vector` 或使用 R Studio 的帮助选项卡找到。

## 一些常见的 S3 类

一些常见的 S3 类包括 `integer`、`numeric`、`matrix` 和 `array`。`integer` 和 `numeric` 类的对象是向量。矩阵就是由行和列中的元素（所有元素模式相同）组成的对象。数组类似于矩阵，但它们可以拥有两个以上的维度。

其他一些常见的 S3 类包括：用于时间序列的 `ts` 和 `mts`；用于因子的 `factor`；用于日期的 `Date`；以及用于带时间日期的 `POSIXct`，所有这些类都是数值型的。

一些常见的模式为 `list` 的类有：用于数据框的 `data.frame`；用于日期和时间的 `POSTXlt`；以及包中大部分高级函数的输出，例如 `lm` 和 `glm`。

`formula` 类包含公式，其模式为 `call`。



### 矩阵类：`matrix`

`matrix` 类的对象是由原子模式（`NULL`除外）、`list` 或 `expression` 模式中的元素组成的矩阵。存在 `matrix()`、`as.matrix()` 和 `is.matrix()` 这三个函数，其行为与原子模式的对应函数类似。

`matrix()` 函数用于创建矩阵。该函数接受五个可能的参数。第一个参数是 `atomic`、`list` 或 `expression` 模式的对象。第二个参数是 `nrow`，即行数。第三个参数是 `ncol`，即列数。第四个参数是 `byrow`，它告诉 R 按行而非按列填充矩阵。默认值为 `FALSE`。`byrow` 参数对于将表格化的原子数据扫描到矩阵中非常有用。第五个参数是 `dimnames`，用于在调用 `matrix()` 时为行和列分配名称。`dimnames` 的默认值是 `NULL`，如果提供，它应该是一个包含两个名称向量的列表。可以使用 `NULL` 替代其中任何一个向量。

使用向量章节中的数组 `a`，以下是创建矩阵的两个示例：

```
> matrix( a, 3, 3 )
     [,1] [,2] [,3]
[1,]    1    4    7
[2,]    2    5    8
[3,]    3    6    1
警告信息：
在 matrix(a, 3, 3) 中：
数据长度 [8] 不是行数 [3] 的约数或倍数
```

和

```
> matrix(a,3,3, byrow=T, dimnames=list( NULL, c("c1","c2","c3") ) )
     c1 c2 c3
[1,]  1  2  3
[2,]  4  5  6
[3,]  7  8  1
警告信息：
在 matrix(a, 3, 3, byrow = T, dimnames = list(NULL, c("c1", "c2",  :
数据长度 [8] 不是行数 [3] 的约数或倍数
```

请注意，如果行数与列数的乘积不是第一个参数中元素数量的倍数，R 会发出警告。警告信息不影响结果。

对于原子模式，如果只提供第一个参数，R 会创建一个矩阵，其行数等于对象中的元素数量，列数等于一。如果只提供 `nrow` 或 `ncol`，R 会根据第一个参数中的对象创建矩阵，行数或列数由给定值决定，并尽可能多地填充列或行，以用完第一个参数中的所有元素——必要时会循环使用。如果同时存在 `nrow` 和 `ncol`，R 会遍历第一个参数的元素，直到矩阵填满，必要时会循环使用。可以使用 `byrow` 参数让第一个参数按行循环，而非按列循环。

对于 `list` 模式的对象，`matrix()` 创建一个矩阵，该矩阵描述列表的每个最底层元素的内容。列表中的元素不必具有相同的模式。描述给出了元素的模式及其大小。有时，`?` 会被放置在矩阵的单元格中。引用矩阵中的单元格将返回该单元格对应列表的内容。以下代码给出了一个示例：

```
> a.list = list( matrix( 1:4, 2, 2 ), c( "abc", "cde" ), 1:3, function(){ print( 1:3 ) } )
> a.list
[[1]]
     [,1] [,2]
[1,]    1    3
[2,]    2    4
[[2]]
[1] "abc" "cde"
[[3]]
[1] 1 2 3
[[4]]
function ()
{
print(1:3)
}
> matrix( a.list, 2, 2 )
     [,1]        [,2]
[1,] Integer,4   Integer,3
[2,] Character,2 ?
> matrix( a.list, 2, 2 )[2, 2]
[[1]]
function ()
{
print(1:3)
}
```

`expression` 模式的对象对于 `matrix()` 是合法的。`matrix()` 的结果是返回表达式的内容，其中内容会循环填充矩阵的大小，并被封装在一个表达式函数语句中。

`as.matrix()` 函数尝试将对象强制转换为 `matrix` 类，并且主要与 `data.frame` 一起使用。如果 `as.matrix()` 的参数可以强制转换为向量，并且它既不是矩阵也不是 `data.frame`，那么 `as.matrix()` 会创建一个由强制转换后的元素组成的单列矩阵。其类别为 `matrix`。如果对象是矩阵，`as.matrix()` 仅返回该矩阵，并保留行名和列名。

如果对象是 `data.frame`，那么 `as.matrix()` 会将数据框强制转换为矩阵。（`data.frame` 是一种特殊的列表，其中列表的所有元素长度相同，并且列表列中的元素都具有相同的原子模式，但列之间的模式不一定相同。）如果 `data.frame` 中存在包含字符数据或原始数据的列，则整个 `data.frame` 会被强制转换为字符型。否则，如果所有列都是逻辑型，则 `data.frame` 会被强制转换为逻辑矩阵；如果存在整数列但不存在数值型或复数型列，则强制转换为整数矩阵；如果存在数值型列且不存在复数型列，则强制转换为数值矩阵；如果存在复数型列，则强制转换为复数矩阵。

也可以使用 `data.matrix()` 函数将数据框转换为矩阵。`data.matrix()` 函数通过将数据框中的所有元素强制转换为数值型来将数据框转换为矩阵。对于复数元素，虚部会被丢弃。该函数将字符列强制转换为 `NA`，并将因子列强制转换为从 `1` 开始的整数。（当创建数据框时，`character` 模式的列默认会被更改为因子。有关 `data.frame()` 如何处理字符模式列的更多信息，请参阅关于 `data.frame()` 的章节。）

以下示例展示了使用名为 `a.df` 的 `data.frame` 时，`as.matrix()` 和 `data.matrix()` 的结果：

```
> a.df = data.frame( c( T, F ), 1:2, 1:2+.5, 1:2+1i, c( as.raw( 1 ), as.raw( 10 ) ), c( "a", "b" ) )
> dimnames(a.df)=list( 1:2, c( "logical",  "integer", "double", "complex", "raw", "character" ))
> a.df
  logical integer double complex raw character
1    TRUE       1    1.5    1+1i  01         a
2   FALSE       2    2.5    2+1i  0a         b
> as.matrix( a.df )
     logical integer double complex raw  character
1 " TRUE" "1"     "1.5"  "1+1i"  "01" "a"
2 "FALSE" "2"     "2.5"  "2+1i"  "0a" "b"
> as.matrix( a.df[,1:5] )
     logical integer double complex raw
1 " TRUE" "1"     "1.5"  "1+1i"  "01"
2 "FALSE" "2"     "2.5"  "2+1i"  "0a"
> as.matrix( a.df[,1:4] )
     logical integer double complex
1    1+0i    1+0i 1.5+0i    1+1i
2    0+0i    2+0i 2.5+0i    2+1i
> as.matrix( a.df[,1:3] )
     logical integer double
1       1       1    1.5
2       0       2    2.5
> as.matrix( a.df[,1:2] )
     logical integer
1       1       1
2       0       2
> as.matrix( a.df[,1] )
      [,1]
[1,]  TRUE
[2,] FALSE
> data.matrix( a.df )
  logical integer double complex raw character
1       1       1    1.5       1   1         1
2       0       2    2.5       2  10         2
警告信息：
在 data.matrix(a.df) 中：强制转换中丢弃了虚部
```

`is.matrix()` 函数用于测试对象是否属于 `matrix` 类。如果参数的类别是 `matrix`，则函数返回 `TRUE`，否则返回 `FALSE`。如果使用模式及类别均为 `expression` 的对象创建矩阵或将其强制转换为矩阵，则结果将具有 `matrix` 类别，即使结果的结构并不类似矩阵。

关于 `matrix()`、`as.matrix()` 和 `is.matrix()` 的更多信息，可以在 R 提示符下输入 `?matrix` 找到。关于 `data.matrix()` 的更多信息，可以在 R 提示符下输入 `?data.matrix` 找到。您也可以使用 R Studio 中的“帮助”选项卡。



### 数组类：`array`

`array` 类是一种使用维度组织数据的类，例如多维列联表。矩阵可以设置为二维数组，向量可以设置为一维数组。由 `array()` 创建的向量属于 `array` 类；然而，二维数组则属于 `matrix` 类，即便该对象是由 `array()` 创建的。

函数 `array()` 从某个对象创建一个数组。该函数接受三个参数。第一个参数是任何可以强制转换为向量的对象。第二个参数是一个向量，包含每个维度的大小，其长度等于数组的维度数。第三个参数是每个维度的名称列表，可以省略。默认值为 `NULL`。

以下是一个设置数组的示例：

```
> array( 1:12, c(  2, 3, 2 ), dimnames=list( c( "", "" ), c( "a", "b", "c" ), NULL )  )
, , 1
  a b c
1 3 5
2 4 6
, , 2
  a  b  c
7  9 11
8 10 12
.
```

除了包含两个以上的维度外，`array()` 的行为与 `matrix()` 相同。

函数 `as.array()` 尝试将对象强制转换为 `array` 类。该对象必须是原子模式（`NULL` 模式除外），或者属于 `list` 或 `expression` 模式。否则，`as.array()` 会返回一个错误。对于合法的模式，`as.array()` 的行为类似于 `as.matrix()`。

函数 `is.array()` 测试一个对象，查看该对象的类是否为 `array`。如果类是数组，则函数返回 `TRUE`，否则返回 `FALSE`。矩阵返回 `TRUE`，与矩阵的创建方式无关。

更多关于 `array()`、`as.array()` 和 `is.array()` 的信息，可以在 R 提示符后输入 `?array` 或在 R Studio 的“帮助”选项卡下找到。

### 时间序列类：`ts` 和 `mts`

`ts` 和 `mts` 类指的是具有起点、终点以及已定义的频率或周期的对象，并且假定观测值以相等的时间间隔出现。时间序列观测值向量的默认时间序列类是 `ts`。对于并行时间序列观测值的矩阵，默认类依次为 `mts`、`ts` 和 `matrix`。在创建时间序列对象时，可以更改时间序列的类。

时间序列对象可以从 `vector`、`matrix`、某些 `list` 和 `expression` 对象，以及其他类别的对象（如 `factor` 和 `Date`）中创建，通过使用 `ts()` 函数。模式为 `array` 的对象会报错。除 `NULL` 模式外，所有原子模式对于 `ts()` 函数来说都是合法的参数。对于 `list` 对象，根据列表的内容和结构，`ts()` 函数有时会创建一个奇怪的时间序列对象。类似地，对模式为 `expression` 的对象使用 `ts()` 操作不会报错，但会产生奇怪的结果。

如果 `ts()` 的参数是一个数据框，则该数据框会由 `data.matrix()` 函数强制转换为一个矩阵。对于矩阵参数，不同的时间序列跨越列，而时间则沿行向下延伸。

函数 `ts()` 接受八个参数。第一个参数是要转换为时间序列的对象。第二个参数是 `start`，用于指定序列的起始值。第三个参数是 `end`，用于指定序列的结束值。第四个参数是 `frequency`，用于指定序列的周期频率。第五个参数是 `deltat`，它是频率的倒数。`frequency` 或 `deltat` 只提供其中一个，不能同时提供。

第六个参数是 `ts.eps`，用于指定比较不同时间序列之间频率的可接受容差。第七个参数是 `class`，用于告知 R 将哪个类分配给时间序列对象。第八个参数是 `names`，用于为时间序列矩阵中的时间序列命名。如果没有指定名称，R 会分配名称 `Series 1`、`Series 2` 等。

第二个、第三个、第四个和第五个参数可能会令人困惑。R 在处理打印和绘图时，会将月度或季度数据视为特殊情况。其他类型的周期数据则需要特殊处理。对于月度数据，将 `start` 设置为

```
start = c('年份', '月份编号')
```

并将 `frequency` 设置为

```
frequency = 12
```

或者将 `deltat` 设置为

```
deltat = 1/12,
```

其中 `year` 是起始年份，`month number` 是起始月份的编号（`1` 代表一月，`2` 代表二月，依此类推），这样就将月份和年份分配给了正在转换为时间序列的对象中的数据点。

要生成一个月度时间序列，请包含 `end` 参数：

```
end = c('年份', '月份编号'),
```

其中 `year` 是结束年份，`month number` 是结束月份的编号。`ts()` 函数会循环使用第一个参数，直到时间序列填充完毕。（对于任何时间序列，提供起始值、结束值和频率，都会通过循环使用第一个参数来创建一个时间序列。如果第一个参数是一个矩阵，每一列会独立循环。）

对于季度数据，遵循相同的步骤，但使用的频率为 4。例如：

```
> ts( 1:12, start=c( 2019, 2 ), freq=12 )
    Jan Feb Mar Apr May Jun Jul Aug Sep Oct Nov Dec
2019       1   2   3   4   5   6   7   8   9  10  11
2020  12
> ts( 1:12, start=c( 2019, 2 ), freq=4 )
     Qtr1 Qtr2 Qtr3 Qtr4
2019         1    2    3
2020    4    5    6    7
2021    8    9   10   11
2022   12
```

在更一般的层面上，假设有一周零三天的日数据，起始周是第 32 周。令 `d.data` 为数据。那么，可以如下创建时间序列：

```
> ts( 1:12, start=c( 3, 2 ), freq=7 )
Time Series:
Start = c(3, 2)
End = c(4, 6)
Frequency = 7
[1]  1  2  3  4  5  6  7  8  9 10 11 12
> print( ts( 1:12, start=c( 3, 2 ), freq=7 ), calendar=T )
   p1 p2 p3 p4 p5 p6 p7
3     1  2  3  4  5  6
4  7  8  9 10 11 12
```

请注意，打印时间序列的默认方式不是按周期显示的——除非频率为 4 和 12，对于这两种情况，R 假定数据是月度或季度数据。可以使用 `print()` 的 `calendar` 参数来开启或关闭按周期打印。

如果给 `start` 和 `end` 各只使用一个数字而不是两个，那么只能使用数量 (n+i/f) 作为起始点和结束点，其中 `n` 是第一个周期的整数部分，`f` 是频率，`i` 可以取 0 到 (f-1) 之间的整数值。如果手动输入，数量 (n+i/f) 必须精确到至少五位小数，除非将 `ts.eps` 参数从其默认值 1.0E-5 更改。`ts.eps` 的值在 `options()` 中设置。R 在这方面非常严格。

函数 `as.ts()` 尝试将对象强制转换为 `ts` 类。类似 `vector` 或 `matrix` 的对象会被强制转换。数组不会，函数不会，调用不会，环境也不会；表达式和列表会。

函数 `is.ts()` 测试一个对象是否属于 `ts` 类，如果是则返回 `TRUE`，否则返回 `FALSE`。

更多关于 `ts()`、`as.ts()` 和 `is.ts()` 的信息，可以在 R 提示符后输入 `?ts` 或使用 R Studio 中的“帮助”选项卡找到。



### 因子类：`factor` 和 `ordered`

`factor` 类是因子水平的对象类。具有有序因子水平的因子属于两个类：`ordered` 和 `factor`。因子和有序因子用于至少包含某些分类数据的建模中。因子和有序因子的模式是数值型的，其水平与从 1 开始递增的整数相关联。然而，在打印时，显示的是名义上的水平。

默认情况下，因子水平通常按字母顺序或数字顺序排序，具体取决于参数的模式，但也可以指定不同的顺序。

存在三个函数 `factor()`、`as.factor()` 和 `is.factor()`，以及 `ordered()`、`as.ordered()` 和 `is.ordered()`。第二组函数在创建和测试因子对象方面的行为与第一组相同，因此我们在此只讨论第一组函数。

函数 `factor()` 创建一个因子水平向量和一个相关联的水平列表。该函数有六个参数。第一个参数是生成因子的对象。该参数必须是原子模式或列表类型。并非所有列表都能形成因子。第二个参数是 `levels`，用于设置因子水平的顺序。`levels` 参数是可选的。

第三个参数是 `labels`，用于为水平指定标签。第三个参数是可选的，默认为对象元素的值。第四个参数是 `exclude`，用于指定结果中要排除的任何水平。被排除的水平会被设置为 `<NA>`。该参数是可选的，默认为 `NA`。

第五个参数是 `ordered`，它存在于 `factor()` 中，但不存在于 `ordered()` 中。参数 `ordered` 指示 `factor()` 创建一个具有有序水平的因子。将 `ordered` 设置为 `TRUE` 的 `factor()` 函数与 `ordered()` 函数（仅出于与 S 语言向后兼容的目的而包含在当前版本的 R 中）的结果相同。第六个参数是 `nmax`，描述为要使用的最大水平数，适用于要转换为因子的对象中包含许多值的情况。该参数似乎效果不佳。

有时会关注因子与原始数据之间的转换。如果在 `factor()` 中没有分配标签：

```
as.mode( levels( fac.obj ) )[ fac.obj ],
```

将返回对象的原始值，其中 `mode` 是原始对象的模式，`fac.obj` 是因子对象。请注意，函数：

```
as.numeric( fac.obj ),
```

将返回与水平相关联的整数，即使原始对象不是 `numeric` 模式也是如此。如果分配了标签，则通常无法提取原始数据。

示例如下：

```
> a.log = c( T, T, F, T )
> a.log
[1]  TRUE  TRUE FALSE  TRUE
> af1 = factor( a.log )
> af1
[1] TRUE  TRUE  FALSE TRUE
Levels: FALSE TRUE
> as.logical( levels( af1 ) )[ af1 ]
[1]  TRUE  TRUE FALSE  TRUE
> as.numeric( af1 )
[1] 2 2 1 2
> af2 = factor( a.log, levels=c( T, F ) )
> af2
[1] TRUE  TRUE  FALSE TRUE
Levels: TRUE FALSE
> as.logical( levels( af2 ) )[ af2 ]
[1]  TRUE  TRUE FALSE  TRUE
> as.numeric( af2 )
[1] 1 1 2 1
> af3 =factor( a.log, labels=c( "flab", "tlab" ) )
> af3
[1] tlab tlab flab tlab
Levels: flab tlab
> as.logical( levels( af3 ) )[ af3 ]
[1] NA NA NA NA
> as.numeric( af3 )
[1] 2 2 1 2
> as.character( levels( af3 )) [ af3 ]
[1] "tlab" "tlab" "flab" "tlab"
```

`as.factor()` 函数的操作方式与 `factor()` 相同，但只接受一个参数，即要转换为因子的对象。

`is.factor()` 函数用于测试对象是否为因子，如果是则返回 `TRUE`，否则返回 `FALSE`。

还有一个相关的函数 `addNA()`。该函数创建一个包含缺失数据（NA）水平的因子对象。该函数接受两个参数。第一个参数是一个对象，可以从中创建 `factor` 类的对象。第二个参数是 `ifany`。`ifany` 参数是逻辑型的，如果仅在存在 `NA` 时才添加额外水平，则取值为 `TRUE`；如果要始终包含该额外水平，则取值为 `FALSE`。

有关这七个函数的更多信息，可以在 R 提示符下输入 `?factor`，或使用 R Studio 中的“帮助”选项卡来查找。



