# 10. 从 R 中导出

能够从 R 中导出数据使得 R 更加有用。对象可以导出到文件或连接中。由于 R Studio 没有专门用于导出对象的方法，这里仅介绍命令行 R 方法。在本章中，我们将介绍导出到硬盘上的外部文件以及控制台。你可以通过在 R 提示符下输入`?connections`或使用 R Studio 中的“帮助”选项卡来查找有关导出到连接的信息。

有许多函数可以导出到外部文本文件，本章将讨论其中的八个。第一个函数是`dump()`。函数`dump()`可以将任意类型的命名对象以文本格式写入外部文件。

下一个函数是`sink()`。函数`sink()`可以将通常显示在控制台的输出以文本格式导入到外部文件中。接下来是函数`write()`。函数`write()`可以将原子数据以文本格式写入外部文件。然后是函数`write.matrix()`。对于矩阵和数据框，函数`write.matrix()`以表格文本格式导出矩阵或数据框。

接下来的两个函数是`write.table()`和`write.csv()`。对于可以强制转换为数据框的对象，`write.table()`和`write.csv()`可以在保持数据框结构的同时将对象写入外部文件。这些函数比`write.matrix()`速度慢，但更复杂，并且写入表格文本数据。

我们介绍的最后两个函数是`save()`和`saveRDS()`。这些函数默认以二进制格式保存对象，是在工作空间之间传输数据集和函数的首选函数。

还有一些函数可以将数据框转换为 Excel、SPSS、SAS 和 Stata 格式，我们将在本章中简要介绍。此外，控制台的输出可以剪切并粘贴到外部文件中。

书中介绍的导入和导出函数表见表 10-1，其中一些函数是成对出现的。

**表 10-1** 成对的导入和导出函数

| 导入 | 导出 | 用途 |
| --- | --- | --- |
| `source()` | `dump()` | 创建并以文本格式来源化外部文件 |
| `scan()` | | 将文本数据作为向量读取 |
| | `sink()` | 写入命令的文本输出 |
| | `write()` | 以表格形式写入文本数据 |
| | `write.matrix()` | 使用单一原子模式写入矩阵或数据框，保持原始结构 |
| `read.table()` `read.csv()` | `write.table()` `write.csv()` | 以文本形式读取和写入矩阵或数据框，保持原始结构 |
| `load()` `data()` `attach()` | `save()` | 读取和写入对象，主要使用二进制格式，用于传输对象 |
| `readRDS()` | `saveRDS()` | 读取和写入一个对象，主要使用二进制格式，用于传输单个对象 |
| `dget()` | `dput()` | 具有历史意义，使用文本格式 |

## 函数 `dump()`

函数`dump()`接受一个对象名称向量，并将这些对象的内容导出到一个文件中。该文件将采用文本格式。（函数`source()`读取转储文件，将其放入一个包含两个元素的列表中：读取的值和一个指示结果是否可见的逻辑值。如果转储了多个对象，则只有最后一个对象被来源化。）

`dump()`的第一个参数是`list`，它是待转储对象的集合。要将对象输入到函数中，需要将对象名称收集到一个字符向量中，对象名称用引号括起来。例如：

```
> a = function(){ print( 1:4 ) }
> b = expression( x~y )
> c = list( 1:4, "a" )
> d = c( 1, 2, 3, 4 )
> dump( c( "a", "b", "c", "d" ), file="" )
a <-
function(){print(1:4)}
b <-
expression(x ~ y)
c <-
list(1:4, "a")
d <-
c(1, 2, 3, 4)
.
```

除了命名对象向量，该函数还接受参数`file`、`append`、`control`、`envir`和`evaluate`。

参数`file`包含函数写入的位置。如果该参数设置为`""`，则转储内容将输出到控制台，或者如果`stdout()`不是控制台，则输出到`stdout()`。硬盘地址是`file`的一个选项，可以是相对于工作目录的地址，也可以是绝对地址。对于硬盘地址，该位置是一个字符串或字符对象。默认值是`"dumpdata.R"`。

参数`append`是一个逻辑变量。如果`append`为`TRUE`且`file`等于一个文件名，则`dump()`将转储内容追加到现有文件。如果为`FALSE`，则覆盖现有文件。默认值为`FALSE`。

参数`envir`是一个模式为`environment`的参数，它告诉`dump()`在哪个环境中查找要转储的对象。默认值是`parent.frame()`。

参数`control`和`evaluate`与保存和重新加载函数有关，其中`dump()`用于保存，`source()`用于加载函数。`control`给出`dump()`使用的反解析选项，默认为`"all"`；`evaluate`是一个逻辑变量，告诉 R 是否评估承诺，默认为`TRUE`。

你可以通过在 R 提示符下输入`?dump`或在 R Studio 的“帮助”选项卡下访问帮助页面。

## 函数 `sink()`

函数`sink()`将命令行命令的输出发送到文件或连接。函数`sink()`会持续写入，直到在 R 提示符下输入`sink()`或`sink(file=NULL)`。该函数接受四个参数：`file`、`append`、`type`和`split`。

`file`参数告诉`sink()`将输出写入何处。如果写入硬盘文件，写入位置是一个字符参数，即用引号括起来的硬盘地址。该地址可以是相对于工作空间文件夹的地址，也可以是绝对地址。选项`file=""`不适用于`sink()`。

第二个参数`append`告诉`sink()`是追加还是覆盖文件。该参数是一个逻辑参数。当`append`等于`TRUE`时，追加到文件；当等于`FALSE`时，覆盖文件。默认值为`FALSE`。

第三个参数`type`告诉`sink()`要导入两个可能的流中的哪一个。该参数是一个字符参数，可以取两个值之一：`output`或`message`。对于`output`，输出流被发送到文件；对于`message`，命令生成的任何消息都被发送到文件。默认值为`output`。

第四个参数`split`是一个逻辑参数，告诉`sink()`如何拆分流。如果为`FALSE`，输出流不会被发送到控制台。如果为`TRUE`，输出流会同时发送到文件和控制台。默认值为`FALSE`。

以下是使用`sink()`的一个示例：

```
> sink( "test.txt" )
> rnorm( 10 )
> sink()
```

文件`"test.txt"`是相对于包含 R 工作空间的文件夹的路径。`test.txt`的内容是：

```
[1] -0.30618294 -0.52505474  0.47243057 -0.89954490 -1.06653790  0.03690703
[7]  1.81562861 -0.74177999 -0.28352208 -1.28133196
```

注意，命令行本身不会被输出。

更多信息，请在 R 提示符下输入`?sink`，或使用 R Studio 中的“帮助”选项卡。

# 函数 `write()`

函数 `write()` 可以将原子对象写入文件，并以表格文本格式进行写入。这些对象作为单个向量输入，例如，作为使用 `c()` 收集的对象集合。如果数据位于矩阵或数组中，`write()` 会按列或维度读取数据，但在二维输出中会按行写入。

第一个参数是 `x`，即要导出的向量。该参数通常是任何 `atomic mode`（原子模式）的对象。（有关可接受模式的更多信息，请参阅 `cat()` 的帮助页面。）

除了要导出的向量外，`write()` 还有四个参数。第一个是字符参数 `file`，它告诉 `write()` 将输出写入何处。该参数可以是一个连接，也可以是硬盘驱动器上的一个位置（相对于工作空间或绝对路径）。如果 `file` 参数为 `""`，则输出将发送到控制台；如果 `stdout()` 不是控制台，则发送到 `stdout()` 的值。默认值为 `"data"`。该对象也可以通过管道传递给 R 中的命令。

第二个参数是 `ncolumns`。参数 `ncolumns` 可以是逻辑型、数值型或复数型，如果不是整数，则会被强制转换为整数。该参数指定导出表的列数。默认情况下，该参数取值为 `if( is.character(x) ) 1 else 5`。因此，如果数据是 `character`（字符）模式，则输出矩阵默认只有一列。否则，输出矩阵默认有五列。

输入文件的长度不需要能被 `ncolumns` 整除。换句话说，最后一行不必是完整的。

第三个参数 `append` 是一个逻辑参数。如果设置为 `TRUE`，输出将追加到文件中。如果设置为 `FALSE`，文件将被覆盖。默认值为 `FALSE`。

第四个参数 `sep` 是一个字符串，指定放置在输出矩阵元素之间的字符。默认值是一个空格。

示例如下：

```
> x=1:4
> y=5:8
> z=rbind( x, y )
> w=paste( "a", 1:3, sep="" )
> b = rep( " ", 4 )
> write( c( x, y, b, z, b, w ), file="", ncol=4, sep=" + " )
1 + 2 + 3 + 4
5 + 6 + 7 + 8
+   +   +
1 + 5 + 2 + 6
3 + 7 + 4 + 8
+   +   +
a1 + a2 + a3
```

请注意，当单独输入时，`x` 和 `y` 各自作为一行导出。当 `x` 和 `y` 使用 `rbind()` 绑定成一个矩阵时，`write()` 会向下读取两列，并将结果按行写入。另请注意，列数为 `ncol` 指定的四列，但最后一行只有三个元素。

您可以在 R 提示符下输入 `?write` 或使用 R Studio 中的“帮助”选项卡来查找关于 `write()` 的更多信息。

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

# 函数 `write.table()` 和 `write.csv()`

函数`write.table()`和`write.csv()`也能以表格文本格式导出矩阵和数据框。两者本质上是同一函数，只是默认参数不同。`write.table()`的所有默认参数均可更改。而`write.csv()`的默认参数`append`、`col.names`、`sep`、`dec`和`qmethod`则无法更改。（与`read.csv()`类似，欧洲用户还可使用`write.csv2()`函数。`write.csv2()`函数使用分号作为分隔符，逗号作为小数点，但除此之外与`write.csv()`相同。）

这些函数接受参数`x`、`file`、`append`、`quote`、`sep`、`eol`、`na`、`dec`、`row.names`、`col.names`、`qmethod`和`fileEncoding`。参数`x`是要导出的对象，必须是一个能强制转换为数据框的对象。

参数`file`指定导出位置。对于外部文件，`file`的模式为`character`，地址可以是相对于工作目录的相对路径，也可以是绝对路径。如果`file`等于`""`，则函数将导出到控制台；如果`stdout()`不是控制台，则导出到`stdout()`。`file`的默认值为`""`。

参数`append`是一个逻辑参数。如果`append`为`TRUE`，则新数据框会追加到文件末尾。如果为`FALSE`，则文件会被覆盖。默认值为`FALSE`。

参数`quote`可以是逻辑值，也可以是列序号构成的数值向量，用于规定元素周围引号的使用规则。默认值为`TRUE`。如果设置为`FALSE`，则所有元素都不加引号。

参数`sep`是一个字符参数，指定导出数据中各元素之间的分隔符。分隔符需放在引号内。对于`write.table()`，默认值为一个空格。对于`write.csv()`，默认值为逗号。

参数`eol`是模式为`character`的参数，指定行尾分隔符。默认情况下，`eol`等于`"\n"`。`eol`的正确值因操作系统而异。Windows 使用`"\n"`，OS X 使用`"\r"`，Linux 使用`"\r\n"`。

参数`na`也是一个字符参数，指定数据缺失时输出的字符串。默认值为`"NA"`。

参数`dec`是另一个字符参数，指定用作小数点的字符。默认情况下，`dec`等于`"."`。

参数`row.names`可以是逻辑值，也可以是行名构成的字符向量。请注意，当`row.names`设置为`TRUE`或行名组成的字符向量时，`write.table()`和`write.csv()`对行名的处理方式不同。如果导出的数据框中包含行名列，`write.table()`不会为该行名列的名称创建一个空字符串，而`write.csv()`会。如果`row.names`等于`FALSE`，则两者在行名方面没有区别，因为不会导出任何行名。

如果未指定行名，且`data.frame`中本身不存在行名（例如，为`x`输入了一个没有行名的矩阵），同时`row.names`为`TRUE`，那么行会被赋予名称，从`"1"`开始，每行递增 1。默认情况下，`row.names`等于`TRUE`。

参数`col.names`可以是逻辑值，也可以是列名构成的字符向量。对于`write.table()`，如果`col.names`设置为`TRUE`，则列名取自数据框；如果数据框中没有列名，则创建列名，从`"V1"`开始，每新增一列，整数递增 1。如果提供了列名，则列名被设置为提供的名称。

如前所述，对于`write.table()`，默认情况下，如果导出文件中存在行名列，则不会为该行名列指定列名值。但是，如果`col.names`设置为`NA`，则列的处理方式与`col.names`设置为`TRUE`时相同，唯一的区别是为行名列添加了一个空字符串。如果`row.names`等于`FALSE`，则将`col.names`设置为`NA`会报错。如果`col.names`设置为`FALSE`，则导出文件中不分配任何列名。

对于`write.csv()`，`col.names`的默认值取决于`row.names`的值。此默认值无法更改。如果`row.names`等于`TRUE`，则`col.names`设置为`NA`。否则，`col.names`设置为`TRUE`。在任何一种情况下，列名要么由数据框中的名称提供，要么（如果数据框中没有列名）从`"V1"`开始创建，每新增一列，整数递增 1。

下一个参数是`qmethod`，可以取值为`"escape"`或`"double"`。默认值为`"escape"`。该参数为双引号值提供处理指令。更多信息请参见`write.table()`的帮助页面。最后一个参数是`fileEncoding`，通常无需指定，但如果指定，它告诉 R 如何对输出进行编码，例如 UTF-8 格式。

以下是一些示例。对象`mat.df.x`是一个带有行名和列名的数据框。对象`mat`是一个没有行名或列名的矩阵。

```
> mat.df.x
c1 c2    C3
r1  1  3   art
r2  2  4 birth
> write.table( mat.df.x )
"c1" "c2" "C3"
"r1" 1 3 "art"
"r2" 2 4 "birth"
> write.table( mat.df.x, sep="," )
"c1","c2","C3"
"r1",1,3,"art"
"r2",2,4,"birth"
> write.table( mat.df.x, sep=",", col.names=NA )
"","c1","c2","C3"
"r1",1,3,"art"
"r2",2,4,"birth"
> write.table( mat.df.x, col.names=F )
"r1" 1 3 "art"
"r2" 2 4 "birth"
> write.table( mat.df.x, row.names=F, col.names=F )
1 3 "art"
2 4 "birth"
> write.table( mat.df.x, sep=",", row.names=F )
"c1","c2","C3"
1,3,"art"
2,4,"birth"
> write.csv( mat.df.x )
"","c1","c2","C3"
"r1",1,3,"art"
"r2",2,4,"birth"
> write.csv( mat.df.x, row.names=F )
"c1","c2","C3"
1,3,"art"
2,4,"birth"
> mat
[,1] [,2]
[1,]    1    3
[2,]    2    4
> write.table( mat )
"V1" "V2"
"1" 1 3
"2" 2 4
> write.table( mat, row.names=c( "r1", "r2" ), col.names=NA )
"" "V1" "V2"
"r1" 1 3
"r2" 2 4
> write.table( mat, row.names=F, col.names=F )
1 3
2 4
> write.csv( mat )
"","V1","V2"
"1",1,3
"2",2,4
> write.csv( mat, row.names=c( "r1", "r2" ) )
"","V1","V2"
"r1",1,3
"r2",2,4
```

要访问`write.table()`和`write.csv()`的帮助页面，请在 R 提示符下输入`?write.table`，或使用 R Studio 的“帮助”选项卡。

# 函数 `save()`

函数 `save()` 用于保存 R 对象，默认以二进制格式保存到文件中。保存的对象可以通过 `load()` 或偶尔使用 `data()` 加载到工作空间中，或通过 `attach()` 附加到工作空间。关于 `load()`、`data()` 和 `attach()` 的信息，请参阅前一章。

函数 `save()` 接受参数 `...`、`list`、`file`、`ascii`、`version`、`envir`、`compress`、`compression_level`、`eval.promises` 和 `precheck`。待保存对象的名称可以通过两种方式输入：用逗号分隔的符号或包含对象名称的字符串，或包含对象名称的字符向量（或两者兼用）。参数 `file` 指定对象保存的位置。例如：

```
> save( "ClintonCorpus", "mat", list=c( "junk", "trst" ), file="save.bin" )
> load( "save.bin", ver=T )
Loading objects:
junk
trst
ClintonCorpus
mat
> class( junk )
[1] "list"
> class( trst )
[1] "asS4"
attr(,"package")
[1] ".GlobalEnv"
> class( ClintonCorpus )
[1] "SimpleCorpus" "Corpus"
> class( mat )
[1] "data.frame"
```

此处，四个对象被保存到文件 `"save.bin"` 中，随后重新加载。四个对象属于不同的类。

任何类型的对象都可以使用 `save()` 保存。加载时，对象会以其原始名称加载到工作空间中，并且不会在控制台显示。

参数 `ascii` 如果赋值为 `TRUE`，则告诉 `save()` 写入一个 ASCII 文件。如果赋值为 `FALSE`（默认值），则创建二进制文件。对于 `NA`，请参阅 `save()` 的帮助页面。根据 `save()` 的帮助页面，参数 `version` 告诉 `save()` 使用哪个版本的工作空间格式。可选值为 `NULL`（表示当前默认格式），以及 `1`、`2` 或 `3`（分别对应 R 0.99.0 到 R 1.3.1、R 1.4.0 以及从 R 3.5.0 开始的默认格式）。

参数 `envir` 告诉 `save()` 查找对象的环境。该参数的模式为环境，默认值为 `parent.frame()`。参数 `compress` 指示进行何种压缩，或者是否进行压缩。如果为 `FALSE`，则不进行压缩。如果为 `TRUE`，则进行 `"gzip"` 压缩。如果将该值设置为 `"gzip"`、`"bzip2"` 或 `"xz"`，则告诉 `save()` 使用该压缩方法。默认值为 `isTRUE(!ascii)`，因此如果 `ascii` 为 `FALSE`，则默认进行压缩。根据 `save()` 的帮助页面，如果文件参数是一个连接，或者工作空间格式是版本 1，则忽略此参数。

参数 `compression_level` 在 `compress` 不等于 `FALSE` 时给出压缩级别。如果压缩方法是 `"gzip"`，默认级别是 `"6"`。对于 `"bzip2"` 或 `"xz"`，默认级别是 `"9"`。

参数 `precheck` 是一个逻辑参数，当设置为 `TRUE`（默认值）时，告诉 `save()` 在打开文件或连接之前检查对象是否存在。如果设置为 `FALSE`，则即使没有保存任何内容，也会打开文件或连接。对于版本 1，`precheck` 不适用——根据 `save()` 的帮助页面。

参数 `safe` 是一个逻辑参数，当设置为 `TRUE` 时，告诉 `save()` 在保存工作空间时打开一个临时文件，以防保存失败。`TRUE` 是默认值，但会导致保存期间使用更多磁盘空间。如果设置为 `FALSE`，则如果保存失败，工作空间可能会丢失。

有关 `save()` 的更多信息，请在 R 提示符下输入 `?save` 或使用 R Studio 中的“帮助”选项卡。

## 函数 `saveRDS()`

函数 `saveRDS()` 将单个对象保存到文件中。使用 `saveRDS()` 保存的对象可以使用 `readRDS()` 加载——请参阅前一章。`saveRDS()` 的参数有 `object`、`file`、`ascii`、`compress` 和 `refhook`。

参数 `object` 设置为对象的名称，不加引号。参数 `file` 是要分配给文件的名称，是一个字符串或连接。参数 `ascii` 的行为与 `save()` 相同。

下一个参数是 `version`。根据 `saveRDS()` 的帮助页面，将 `version` 设置为 `NULL` 会告诉函数使用默认值——目前是 `2`——自 R 1.4.0 起。对于 R 3.5.0 及更高版本，`version` 的合法选项是 `2` 和 `3`。

参数 `compress` 的行为与 `save()` 中的相同。有关参数 `refhook` 的信息，请参阅帮助页面。

更多信息请在 R 提示符下输入 `?saveRDS` 或使用 R Studio 中的“帮助”选项卡。

## 匹配的导入和导出函数

许多导入和导出函数是成对出现的。例如：`source()` 与 `dump()`；`save()` 与 `load()`、`data()` 或 `attach()`；`dput()` 与 `dget()`；以及 `write.table()` 与 `read.table()`。表 10-1 列出了基于配对关系的导入和导出函数。

## 其他导出函数

与读取数据的函数类似，也有多种写入数据的函数。CRAN 上关于用于导入和导出数据的 `rio` 包的页面列出了许多包及其功能。CRAN 插件文档可在 [`https://cran.r-project.org/web/packages/rio/vignettes/rio.html`](https://cran.r-project.org/web/packages/rio/vignettes/rio.html) 找到。

对于 SPSS、SAS 和 Stata，函数 `write.foreign()`（可在 `foreign` 包中找到）可以以正确的格式导入和导出。`write.foreign()` 函数还可以导出其他一些格式。其他导出函数也可以在 `foreign` 包中找到。

`foreign` 包是默认安装的包之一。要查看 `foreign` 的内容，请在 R 提示符下输入 `help(package=foreign)` 或使用 R Studio 中的“包”选项卡。在包列表中单击 `"foreign"`。要加载 `foreign`，请在 R 提示符下输入 `library(foreign)`，或在 R Studio 的“包”选项卡下勾选 `"foreign"` 左侧的复选框。

一个用于读写 SPSS、SAS 和 Stata 文件的较新的包是 `haven` 包。与 `foreign` 包不同，`haven` 包并非默认安装，因此必须先安装 `haven` 才能加载它。安装 `haven` 后，您可以通过在 R 提示符下输入 `help(package=haven)` 或使用 R Studio 中的“包”选项卡来查看 `haven` 的内容。在包列表中单击 `"haven"`。要加载 `haven`，请在 R 提示符下输入 `library(haven)`，或在 R Studio 的“包”选项卡下勾选 `"haven"` 左侧的复选框。

对于 Excel，有一个专门的包 `xlsx` 用于处理 Excel 文件。`xlsx` 包不是 R 中的默认包，因此必须安装。安装 `xlsx` 后，可以通过在 R 提示符下输入 `help(package=xlsx)` 找到关于 `xlsx` 的信息。对于较旧的 Excel 文件，包 `readxl` 提供了写入和读取 Excel 文件的函数。与 `xlsx` 包一样，`readxl` 并非默认安装，因此必须在加载前先安装。
