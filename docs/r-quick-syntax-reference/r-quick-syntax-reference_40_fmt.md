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