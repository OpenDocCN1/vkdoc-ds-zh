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