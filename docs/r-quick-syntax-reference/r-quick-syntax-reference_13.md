# 数据框类：`data.frame`

`data.frame` 类是一种模式为 `list` 的类矩阵类。数据框及其使用方法非常重要。R 语言中许多可用的数据集都是数据框。当从外部源读取数据时，许多用于读取的函数都会创建数据框。学习如何处理和创建数据框将带来巨大的回报。

数据框以行和列的形式包含原子数据。在一列内，所有数据的模式必须相同。但列之间的模式可以不同。由于数据框不必只有一种模式，因此它是一种特殊的列表。

访问数据框的元素可以像矩阵一样操作，也可以像列表一样操作，这使得数据框比普通列表更通用。默认情况下，列名会反映构成数据框的原始对象中存在或不存在的元素。

函数 `data.frame()`、`as.data.frame()` 和 `is.data.frame()` 都存在于 R 中。在 `data.frame()` 中，要包含在数据框中的对象首先列出，并用逗号分隔。这些对象可以是任何原子模式的对象，或者是由原子列组成的列表。如果一个对象由多于一个列组成（例如某些矩阵和列表），那么原始对象中的每一列都会成为数据框中的一列。否则，每个对象都将成为一列。如果这些列在原始对象中有名称，则默认情况下这些名称会带入数据框。

用于构成数据框的对象不必具有相同的长度（或矩阵的行数），但它们的长度必须是彼此的倍数。数据框的行数将等于最长列的长度。其他列中的数据将循环，直到该列具有正确的行数。例如：

```
> a.list
[[1]]
a1 a2
[1,]  1  7
[2,]  2  8
[3,]  3  9
[4,]  4 10
[5,]  5 11
[6,]  6 12
[[2]]
[1] "abc" "cde"
>
> data.frame( a.list, 1:3 )
a1 a2 c..abc....cde.. X1.3
1  1  7             abc    1
2  2  8             cde    2
3  3  9             abc    3
4  4 10             cde    1
5  5 11             abc    2
6  6 12             cde    3
```

注意，R 已经为第三和第四列创建了名称，并且第三和第四列都会循环。

除了构成数据框的对象之外，函数 `data.frame()` 还有四个参数。第一个参数是 `row.names`，它为行分配名称，默认值为 `NULL`，即不分配名称。第二个参数是 `check.rows`，它是一个逻辑参数，如果设置为 `TRUE`，将检查行长度和行名称的一致性。默认值为 `FALSE`。第三个参数是 `check.names`，它也是逻辑参数，用于检查列名在语法上是否正确，并纠正不正确的名称。`check.names` 的默认值为 `TRUE`。

最后一个参数是 `stringsAsFactors`。默认情况下，`data.frame()` 会将任何包含字符数据的列转换为因子。参数 `stringsAsFactors` 是一个逻辑变量。如果设置为 `TRUE`，则会创建因子。如果设置为 `FALSE`，则字符列保持为 `character` 模式。实际的默认值由 `default.stingsAsFactors()` 生成。`default.stringsAsFactors()` 的值在 `options()` 中设置（第 15 章），默认为 `TRUE`，但可以在 `options()` 中更改。

函数 `I()` 可用于设置数据框。该函数是阻止 `data.frame()` 将字符向量转换为因子的另一种方法。此外，`I()` 确保矩阵的列结构在数据框中得以保持。在 `data.frame()` 调用中，被 `I()` 包围的对象将被视为数据框的一个元素，即使该对象包含多个列也是如此。被 `I()` 包围的对象不会循环。例如：

```
> mat
one two
row1   1   3
row2   2   4
> a.char
[1] "a1" "a2" "a3" "a4"
> a.df1 = data.frame( mat, a.char )
Warning message:
In data.frame( mat, a.char ) :
row names were found from a short variable and have been discarded
> a.df1
one two a.char
1   1   3     a1
2   2   4     a2
3   1   3     a3
4   2   4     a4
> a.df1[[ 3 ]]
[1] a1 a2 a3 a4
Levels: a1 a2 a3 a4
> a.df2 = data.frame( I( mat  ), I( a.char ) )
Error in data.frame( I(mat), I(a.char) ) :
arguments imply differing number of rows: 2, 4
> a.df2 = data.frame( I( mat ), I( a.char[ 1:2 ] )  )
> a.df2
mat.one mat.two a.char.1.2.
row1       1       3          a1
row2       2       4          a2
> a.df2[[ 1 ]]
one two
row1   1   3
row2   2   4
> a.df2[[ 2 ]]
[1] "a1" "a2"
```

如果在调用 `data.frame()` 时未输入行名，则如果第一列有行标签且不循环，行名将取自第一列。否则，行名设置为 `1`、`2`、`3` 等。请参见上面的示例。

函数 `as.data.frame()` 尝试将对象强制转换为数据框。如果该对象是由原子元素（以及一些其他简单列表）组成的列表，或者是原子模式的对象，那么 `as.data.frame()` 会从该对象创建数据框。否则，`as.data.frame()` 会报错。

该函数接受四个参数：要强制转换的对象、`row.names`、`optional` 和 `stringsAsFactors`。`row.names` 和 `stringsAsFactors` 参数的行为与 `data.frame()` 中的相同。`optional` 参数是一个逻辑变量，如果设置为 `TRUE`，则告诉 `as.data.frame()` 设置列名是可选的。如果设置为 `TRUE`，并且原始对象中没有设置列名，则结果中将不会出现列名。`optional` 的默认值是 `FALSE`。

函数 `is.data.frame()` 测试一个对象是否为 `data.frame` 类，如果是，则返回 `TRUE`。否则，`is.data.frame()` 返回 `FALSE`。

函数 `as.matrix()` 和 `data.matrix()` 可用于将数据框转换为矩阵。有关两种转换类型的更多信息，请参阅关于 `matrix` 类的部分。

有关 `data.frame()` 的更多信息，请在 R 提示符下输入 `?data.frame`。有关 `as.data.frame()` 和 `is.data.frame()` 的更多信息，请在 R 提示符下输入 `?as.data.frame`。有关 `I()` 的更多信息，请在 R 提示符下输入 `?I`。或者，使用 R Studio 中的“帮助”选项卡访问帮助页面。



### 日期与时间类：`Date`、`POSIXct`、`POSIXlt` 和 `difftime`

有时，处理日期和时间是很有用的，例如在打印和绘制时间相关图表时。R 语言提供了用于日期以及日期和时间的类。这些类是 `Date`、`POSIXct`、`POSIXlt` 和 `difftime`。`Date`、`POSIXct` 或 `difftime` 类的对象其模式为 `numeric`，而 `POSIXlt` 类的对象其模式为 `list`。`Date` 是日期类，`POSIXlt` 和 `POSIXct` 是日期和时间类。`difftime` 类包含通过计算两个日期对象或两个日期时间对象之差而形成的对象。在上述类通常对应的三类函数中，只有 `as.Date()`、`as.POSIXct()` 和 `as.POSIXlt()` 函数适用于日期以及日期时间对象。`difftime()` 和 `as.difftime()` 函数都存在。

`POSIX` 代表便携式操作系统接口，是 IEEE 计算机学会使用的一系列标准。`Date`、`POSIXct` 和 `POSIXlt` 类中使用的格式基于 POSIX 标准，但这些标准并非跨平台通用。

要在 R 中仅获取日期和时间戳，在 R 提示符下输入 `date()`，它将返回星期几、日期和时间。结果的模式和类均为 `character`。系统日期函数 `Sys.Date()` 返回系统日期，其模式为 `numeric`，类为 `Date`。系统日期和时间函数是 `Sys.time()`，它返回系统日期、时间和时区，其模式为 `numeric`，类为 `POSIXct` 和 `POSIXlt`。默认情况下，日期以“年-月-日”格式读取和返回，时间以“时:分:秒”格式返回。

有许多函数可操作日期和时间类，包括 `weekdays()`，它返回 `Date`、`POSIXlt` 或 `POSIXct` 类对象对应的星期几；函数 `difftime()` 接受两个日期或日期时间对象，并逐元素计算两者之间的时间差。对于 `Date` 类的对象，日期差以天为单位。对于 `POSIXlt` 和 `POSIXct` 对象，时间差以秒为单位。

函数 `strftime()` 和 `strptime()` 允许用户转换到任何日期时间格式，或从任何日期时间格式转换回来。

关于日期和时间类的更多信息，可以在 `DateTimeClasses` 的帮助页面找到，方法是在 R 提示符下输入 `?DateTimeClasses`，或使用 R Studio 中的“帮助”选项卡访问帮助页面。各种日期和时间函数的详细信息可在其各自的帮助页面找到。

函数 `as.Date()` 创建一个日期对象。`as.Date()` 的参数包括：要转换为日期的对象；`format`，它以年、月、日的形式给出对象的格式；`tryFormats`，这是一个字符串向量，包含在未提供 `format` 时尝试的格式；`optional`，这是一个逻辑值，当设置为 `TRUE` 时，如果格式匹配返回错误，`as.Date()` 将返回 `NA`；`origin`，这是第一个参数的起点，必须是 `Date` 或 `POSIXct` 类；以及 `tz`，用于指定时区名称。

如果使用了 `origin`，则要转换的对象可以是任何数值对象。如果提供了 `origin`，该函数会将对象的值加上或减去 `origin` 参数给定的日期，并将结果转换为日期。一个以周为间隔的示例如下：

```
> as.Date( 0:1*7, origin="2019-1-1" )
[1] "2019-01-01" "2019-01-08"
```

如果将日期作为对象使用，且日期不是“年-月-日”格式，则必须给出日期的格式。格式是一个字符变量，其中年份的 POSIX 标准是 `%Y`，日是 `%d`，月是 `%m`，例如：

```
> as.Date( "1/20/2000", format="%m/%d/%Y" )
[1] "2000-01-20"
```

请注意，`format` 是要转换对象的格式，而不是结果的格式。

参数 `tz` 用于指定时区名称。有些时区被识别，有些则不被识别。有关更多信息，请参见 `as.Date()` 的帮助页面。

函数 `as.POSIXct()` 和 `as.POSIXlt()` 接受与 `Date()` 相同的参数，只是日期也可以包含时间。时间的默认格式是 `%H:%M:%S`，分别代表小时、分钟和秒。例如：

```
> as.POSIXct( "1/13/2000 00:30:00", format="%m/%d/%Y %H:%M:%S" )
[1] "2000-01-13 00:30:00 CST"
```

日期以及日期和时间可以通过加法和减法进行操作。时间的小数部分会被正确转换。`Date()` 函数中的日期按天递增；两个日期时间函数中的时间按秒递增。示例如下：

```
> as.POSIXct( Sys.time() + 1:2*3600 )
[1] "2018-11-01 14:59:27 CDT"
[2] "2018-11-01 15:59:27 CDT"
> mode( as.POSIXct( Sys.time() + 1:2*3600 ) )
[1] "numeric"
> as.POSIXlt( Sys.time() + 1:2*3600 )
[1] "2018-11-01 15:02:53 CDT"
[2] "2018-11-01 16:02:53 CDT"
> mode( as.POSIXlt( Sys.time() + 1:2*3600 ) )
[1] "list"
> as.POSIXlt( Sys.time() ) + 1:2*3600
[1] "2018-11-01 15:07:43 CDT"
[2] "2018-11-01 16:07:43 CDT"
> mode( as.POSIXlt( Sys.time() ) + 1:2*3600 )
[1] "numeric"
```

函数 `difftime()` 和 `as.difftime()` 在这里不做介绍。一个日期差的示例如下：

```
> ( Sys.Date() - as.Date( "2000-1-1" ) )
Time difference of 5125 days
> mode( Sys.Date() - as.Date( "2000-1-1" ) )
[1] "numeric"
> class( Sys.Date() - as.Date( "2000-1-1" ) )
[1] "difftime"
```

关于日期和时间函数的更多信息，可以在 R 提示符下输入 `?as.Date`、`?as.POSIXct`、`?as.POSIXlt`、`?difftime` 或 `?as.difftime` 找到，或使用 R Studio 中的“帮助”选项卡访问帮助页面。



