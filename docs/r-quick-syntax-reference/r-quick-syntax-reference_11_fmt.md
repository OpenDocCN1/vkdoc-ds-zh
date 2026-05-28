# 数值模式与整数类型或双精度类型

对于`numeric`模式以及`integer`和`double`类型，情况会变得稍微复杂一些。最初在 S 语言中，数值对象可以是整数、实数或双精度（用于双精度）。实数选项已被弃用，不应再使用。在 S3 中，整数和双精度选项都属于`numeric`模式。在 S4 中，每个选项都有单独的类型。此处将介绍`numeric()`、`is.numeric()`和`as.numeric()`函数。在 S4 中使用的`integer()`、`as.integer()`、`is.integer()`、`double()`、`as.double()`和`is.double()`函数行为类似。

`numeric()`函数接受一个单一对象作为参数。如果参数等于零或没有参数，`numeric()`返回`numeric(0)`，这是一个模式为`numeric`、长度为零的空对象。对于参数为单个正数值（双精度数字会向下取整为整数）的情况，该函数返回一个长度等于参数值的零向量。对于负参数以及模式不是`numeric`或长度大于一的参数，R 会返回一个错误——但包含单个正数的字符串除外，其行为与正数相同。例如：

```
> numeric()
numeric(0)
> numeric( 3 )
[1] 0 0 0
> numeric( 3.7 )
[1] 0 0 0
> numeric( "3.7" )
[1] 0 0 0
> numeric( -3 )
Error in numeric(-3) : invalid 'length' argument
> numeric( 3:4 )
Error in numeric(3:4) : invalid 'length' argument
```

`as.numeric()`函数尝试将一个对象强制转换为双精度。该函数接受与`as.logical()`相同的两个参数。参数`x`可以是任何原子模式对象。如果参数是`NULL`或未给出参数，则返回`numeric(0)`，其中`numeric(0)`是一个模式为`numeric`、长度为零的空对象。如果对象是`logical`，则对象中的`TRUE`被设置为 1，`FALSE`被设置为 0。如果对象是`numeric`，则元素的值以双精度数形式返回。如果对象是`complex`，则仅返回实部（作为双精度数），并给出一个警告。如果对象的模式是`raw`，`as.numeric()`会将十六进制值转换为双精度。如果对象的模式是`character`，则该函数会为对象的元素返回`NA`，除非某个元素是引号内的数字，在这种情况下会返回该数字。如果创建了 NA，会给出一个警告。如果参数不是原子的，而是一个单层级列表，且列表的每个元素都等于一个单一元素原子对象（模式不是`raw`），则`as.numeric()`返回强制转换为数值后的元素。否则，R 会给出一个错误。值为`NA`的元素会作为`NA`返回。

例如：

```
> as.numeric()
numeric(0)
> as.numeric( NULL )
numeric(0)
> as.numeric( c( F, T, NA ) )
[1]  0  1 NA
> as.numeric( 1:3 + 0.2 )
[1] 1.2 2.2 3.2
> as.numeric( 1:3 + 2+3i )
[1] 3 4 5
Warning message:
imaginary parts discarded in coercion
> as.numeric( c( "1", "a", "3" ) )
[1]  1 NA  3
Warning message:
NAs introduced by coercion
> as.numeric( as.raw( 2 ) )
[1] 2
> as.numeric( list( as.raw( 2 ) ) )
Error: unimplemented type 'raw' in 'asReal'
> as.numeric( list(1:3) )
Error: (list) object cannot be coerced to type 'double'
> as.numeric( list( 1:3 )[[1]] )
[1] 1 2 3
```

`is.numeric()`函数用于测试一个对象是否为`numeric`对象，并且可以处理任何模式的对象。如果对象是`numeric`，则返回`TRUE`，否则返回`FALSE`。

关于`numeric`模式对象的更多信息，可以在 R 提示符下输入`?numeric`，或使用 R Studio 的帮助选项卡来获取。

## 复数模式

`complex`模式是复数的模式。在 R 中，复数可以使用`complex()`创建，或直接在 R 提示符下输入。例如：

```
> complex( real=1:5, imaginary=6:10 )
[1] 1+ 6i 2+ 7i 3+ 8i 4+ 9i 5+10i
> 1:5 + 1i*6:10
[1] 1+ 6i 2+ 7i 3+ 8i 4+ 9i 5+10i
```

注意，对于复数，`i`前面总是有一个数字且没有运算符，这能让 R 知道`i`是负一的虚根。

对于`complex()`函数，如果第一个参数是零或没有参数，则函数返回`complex(0)`，一个模式为`complex`、长度为零的空集。如果参数是单个正数或包含单个正数的字符串，`complex()`返回一个复数零向量，其长度等于该数字向下取整后的整数。如果参数包含一个多元素数值对象、一个多元素字符对象（其第一个元素包含引号内的正数），或者参数是逻辑值（无论是一个元素还是多个元素），则仅使用参数的第一个元素，其中对于逻辑对象，`FALSE`被强制转换为 0，`TRUE`被强制转换为 1。任何其他参数都会导致错误。例如：

```
> complex()
complex(0)
>
> complex( 0 )
complex(0)
>
> complex( 3 )
[1] 0+0i 0+0i 0+0i
>
> complex( "3" )
[1] 0+0i 0+0i 0+0i
> complex( 1:3 )
[1] 0+0i
> complex( c( "1", "2", "3" ) )
[1] 0+0i
> complex( c( T, F ) )
[1] 0+0i
> complex( list( T, F ) )
Error in complex(list(T, F)) : invalid length
```

`complex()`函数也接受参数`real`和`imaginary`或`modulus`和`argument`。参数`real`和`imaginary`或`modulus`和`argument`可以设置为任何数值对象、任何包含引号内数字的字符对象或任何逻辑对象。参数`real`和`imaginary`是数字的实部和虚部，而参数`modulus`和`argument`是数字的极坐标，其中`modulus`等于数字的长度，`argument`等于数字与 x 轴正方向的夹角（以弧度为单位）。这些对象的长度不必相同，并且会循环使用。

对于`real`和`imaginary`这一对，其中一个可以省略，省略的参数将被设置为零。对于`modulus`和`argument`这一对，如果省略了`modulus`，则`modulus`的值将被设置为 1；如果省略了`argument`，则`argument`的值将被设置为零。`complex()`的一些示例如下：

```
> complex( real=3:5 )
[1] 3+0i 4+0i 5+0i
> complex( im=3:5 )
[1] 0+3i 0+4i 0+5i
> complex( mod=3:5 )
[1] 3+0i 4+0i 5+0i
> complex( arg=45/180*pi )
[1] 0.7071068+0.7071068i
> complex( modulus=c( 1, 2 ), argument=45/180*pi )
[1] 0.7071068+0.7071068i 1.4142136+1.4142136i
```

`as.complex()`函数将尝试将一个对象强制转换为`complex`模式。该函数接受与`as.logical()`和`as.numeric()`相同的参数。如果`x`可以被强制转换为数值（原子模式和包含单一元素原子模式元素（`raw`模式除外）的单层级列表）但不是复数，则结果是一个复数对象，其中强制转换后的参数作为实部，虚部为零，但`NA`除外，它们仅作为`NA`返回。对于非原子模式，除了不包含`raw`模式的单值单层级列表外，`as.complex()`会返回一个错误。例如：

# R 的数据模式

## 复杂模式（Complex Mode）

`is.complex()`函数用于测试函数的参数是否为`complex`模式。如果参数是`complex`模式，则函数返回`TRUE`，否则返回`FALSE`。

关于`complex`模式的更多信息，可以在 R 提示符下输入`?complex`，或使用 R Studio 的帮助选项卡来获取。

```r
> as.complex( list( NA, F, 2, 2i, "2" ) )
[1]   NA 0+0i 2+0i 0+2i 2+0i
> as.complex( as.raw( 2 ) )
[1] 2+0i
> as.complex( list( as.raw( 2 ) ) )
Error: unimplemented type 'raw' in 'asComplex'
> as.complex( NULL )
complex(0)
```

### 原始模式（Raw Mode）

`raw` 模式用于按字节进行分析。原始对象中的数字采用十六进制格式，每个元素由两位数字组成，每位数字的取值范围为 0 到 9 或 `a` 到 `f`。原始元素的十进制等效值不能大于 255（即，不能是超过两位的十六进制数），也不能为负数。

函数 `raw()` 返回一个由参数指定长度的 `00` 向量。如果未提供参数或参数为零，`raw()` 将返回 `raw(0)`，即一个长度为零的原始空集。如果输入单个数字或带引号的数字作为参数，`raw()` 将返回一个长度等于该数字向下取整后的整数的向量。如果输入任何其他类型的对象作为参数，`raw()` 会报错。例如：

```r
> raw( 0 )
raw(0)
> raw( 2 )
[1] 00 00
> raw( "2" )
[1] 00 00
> raw( 1:2 )
Error in raw(1:2) : invalid 'length' argument
> raw( "a" )
Error in raw("a") : vector size cannot be NA/NaN
In addition: Warning message:
In raw("a") : NAs introduced by coercion
```

函数 `as.raw()` 会尝试将函数的参数强制转换为原始类型。如果一个原子对象可以被强制转换为数值，并且生成的数值大于等于零且小于 256，那么 `as.raw()` 会返回该强制转换后元素的十六进制值。双精度数值会向下取整为整数。对于 `NULL`，`as.raw()` 返回 `raw(0)`。对于逻辑模式对象，`FALSE` 被设置为 `00`，`TRUE` 被设置为 `01`。对于 `numeric` 模式对象，若值小于零或大于等于 256，R 会返回 `00` 并给出警告。对于 `complex` 模式的对象，其实部会像数值对象一样处理，虚部被丢弃。系统会给出一个转换已发生的警告。`character` 模式的对象会返回 `00`，除非字符串包含引号内的有效数字。原子模式以外的对象模式，除非是仅包含单一合法值的单层列表，否则会报错。例如：

```r
> as.raw( NULL )
raw(0)
> as.raw( 0 )
[1] 00
> as.raw( c( T, F ) )
[1] 01 00
> as.raw( c( 1, 2 ) + 0.1 )
[1] 01 02
> as.raw( c( 1, 2 ) + 0.1i )
[1] 01 02
Warning message:
imaginary parts discarded in coercion
> as.raw( c( "1", "2" ) )
[1] 01 02
> as.raw( list( "1.1", "2.1" ) )
[1] 01 02
> as.raw( -30 )
[1] 00
Warning message:
out-of-range values treated as 0 in coercion to raw
> as.raw( c( "-1", "200", "a" ) )
[1] 00 c8 00
Warning messages:
1: NAs introduced by coercion
2: out-of-range values treated as 0 in coercion to raw
> as.raw( list( c( "-1", "200" ), 1:3 ) )
Error: (list) object cannot be coerced to type 'raw'
```

函数 `is.raw()` 用于测试一个对象是否为 `raw` 模式。如果对象是 `raw` 模式，则返回 `TRUE`，否则返回 `FALSE`。任何对象都可以作为 `is.raw()` 的参数。

更多关于 `raw` 模式的信息，可以在 R 提示符下输入 `?raw` 或使用 R Studio 的“帮助”选项卡找到。

### 字符模式（Character Mode）

`Character` 模式的对象由带引号的字符串组成。根据 `character()` 的帮助页面，如果对象是文本，对于超过 500 个字符的对象，每 500 个字符会插入一个换行符。但实际情况似乎并非如此。通常的三个函数也适用于 `character` 模式。

函数 `character()` 创建一个空字符串向量，并且只接受 `numeric` 模式的对象或包含引号内数字的 `character` 模式对象。该对象只能包含一个元素。默认值为零。如果参数大于等于一，则参数向下取整为整数，函数返回一个长度等于该整数的 “s” 向量。如果参数小于一且大于负一，则返回长度为零的字符空集 `character(0)`。其他参数返回错误。例如：

```r
> character()
character(0)
> character( -0.5 )
character(0)
> character( -3 )
Error in character(-3) : invalid 'length' argument
> character( 3 )
[1] "" "" ""
>
> character( "3" )
[1] "" "" ""
> character( c( 3, 4 ) )
Error in character(c(3, 4)) : invalid 'length' argument
```

函数 `as.character()` 尝试将参数 `x` 转换为字符串。该函数具有与 `as.logical()`、`as.numeric()` 和 `as.complex()` 相同的参数。如果未给出 `x` 或将其设置为 `NULL`，则返回 `character(0)`，即字符模式的空对象。对于原子模式，转换是字面意义的，但元素会以引号形式返回。对于双精度浮点数，最多使用 15 位有效数字。与除 `NULL` 外的其他原子模式不同，函数 `as.character()` 也会为某些递归模式返回结果。例如：

```r
> as.character()
character(0)
> as.character( NULL )
character(0)
> as.character( c( T, F ) )
[1] "TRUE"  "FALSE"
> as.character( 1:4 )
[1] "1" "2" "3" "4"
> as.character( 1:4 + 2i )
[1] "1+2i" "2+2i" "3+2i" "4+2i"
> as.character( as.raw( 100 ) )
[1] "64"
```

`list` 模式的对象将在下一节中描述。在本节中，列表是对象的集合，这些对象可以是任何模式。后续示例中使用的函数 `lm()` 用于拟合线性回归模型，波浪线左侧的值为因变量，右侧的值为自变量。`lm()` 的输出是一个列表。

当使用 `list` 模式的对象作为参数时，`as.character()` 可能会根据列表的不同返回一些奇怪的内容。该函数返回的结果可能与在 R 提示符下输入参数时返回的结果不同。示例如下：

## 常见的递归模式与语言模式

本书涵盖的递归模式和语言模式包括 `list`、`function`、`call`、`environment` 和 `expression`。`list`、`function`、`call`、`environment` 和 `expression` 模式都是递归模式。`call` 和 `expression` 模式同时也是语言模式。

### 列表模式（List Mode）

列表是对象的集合，这些对象可以是任何模式，并且同一列表中的对象不必具有相同模式。`list`模式拥有与原子模式相同的三个函数；不过，还有一些额外的函数。

函数 `list()` 根据其参数创建一个列表。在括号内，参数之间用逗号分隔。这些参数可以是任意类型的对象。创建空列表的方式与原子模式不同。要创建包含指定数量对象且这些对象均为 `NULL` 的列表，请使用：

```
vector( "list", n ),
```

其中 `n` 是列表中对象的数量。变量 `n` 必须是数值型且大于负一，若为正数则向下取整为整数，若为负数则向上取整，并且只能包含一个元素。如果 `n` 等于零、为负数或被省略，则会创建一个长度为零的列表。例如：

```
> list( 1:2 )
[[1]]
[1] 1 2
> list( 1, 2 )
[[1]]
[1] 1
[[2]]
[1] 2
> list( -0.5 )
[[1]]
[1] -0.5
> vector( "list", -0.5)
list()
```

函数 `as.list()` 尝试将其参数强制转换为模式 `list`。如果提供了多个参数，则仅强制转换第一个参数，其他参数被忽略。参数 `NULL` 会返回一个长度为零的列表。例如：

```
> as.list( NULL )
list()
>
> as.list( 1:2, 3:4 )
[[1]]
[1] 1
[[2]]
[1] 2
```

函数 `is.list()` 用于测试参数是否为列表（或成对列表，此处不涉及）。如果对象是模式 `list`，则返回 `TRUE`，否则返回 `FALSE`。

函数 `unlist()` 会移除原子元素列表的列表属性，并针对这些列表，返回一个包含其中所有对象元素的向量。例如：

```
> list( 1:2, 3:4 )
[[1]]
[1] 1 2
[[2]]
[1] 3 4
> unlist( list( 1:2, 3:4 ) )
[1] 1 2 3 4
```

函数 `alist()` 创建一个列表，其中变量的值不必指定。`alist()` 最常用于函数求值过程中，此时某些变量可以预先指定，而其他变量则在每次运行函数时被赋值。

更多信息可以在 R 提示符下输入 `?list` 或 `?unlist()` 获得，这会调出关于 `list()`、`alist()` 和 `unlist()` 的帮助页面。

### 函数模式与闭包、特殊函数及内建类型

R 中的函数属于模式 `function`。模式为函数（`function`）的对象可以是闭包、特殊函数或内建类型。闭包类型的函数使用 R 语言编写，拥有参数列表、函数主体以及运行环境。特殊函数和内建类型的函数是原始函数，使用 C 语言编写。原始函数仅存在于基础包（base package）中，这两种类型的区别在于参数求值方式的不同。

在原子模式列出的三个函数中，对于模式 `function`，仅存在 `function()` 和 `is.function()`。函数 `function()` 用于创建函数，但函数的结构与原子模式及 `list` 模式不同，并且 `function()` 的帮助页面也与 `is.function()` 的不同。我们将在第 7 章中介绍函数的创建。如果参数是一个函数，函数 `is.function()` 返回 `TRUE`，否则返回 `FALSE`。

函数 `is.primitive()` 用于测试一个函数是否为原始函数。

作为不同类型函数的示例，函数 `seq()` 是一个闭包， `log()` 是一个特殊函数，而 `cos()` 是一个内建函数。

关于 `function` 模式和原始函数的更多信息，可以在 R 提示符下输入 `?is.function` 或使用 R Studio 中的“帮助”选项卡获得。

### 调用模式（Call Mode）

模式为 `call` 的对象是未求值的函数及其参数（如果该函数接受参数的话）。原子模式拥有的那三个函数同样存在于 `call` 模式：`call()`、`as.call()` 和 `is.call()`。

函数 `call()` 创建一个模式为 `call` 的对象。`call()` 的第一个参数是函数名称（需用引号括起来）。`call()` 的其余参数是该函数的参数。以下是一些示例：

```
> a.call = call( "lm",  y~x )
> a.call
lm(y ~ x)
> b.call = call( "ls" )
> b.call
ls()
> c.call = call( "ls", pattern="abc" )
> c.call
ls(pattern = "abc")
```

请注意，可以使用函数 `eval()` 来求值一个模式为 `call` 的对象。如果调用中的所有变量都存在于工作空间中，`eval()` 将对该函数进行求值；否则，`eval()` 会报错。例如：

```
> x
[1] 1 2 3
> y
[1] 2 3 4
> eval( a.call )
Call:
lm(formula = y ~ x)
Coefficients:
(Intercept)            x
1            1
> z
Error: object 'z' not found
> a.call = call( "lm", z~x )
> eval(a.call)
Error in eval(expr, envir, enclos) : object 'z' not found
```

函数 `as.call()` 尝试将其参数强制转换为模式为 `call` 的对象。如果参数是一个列表，则进行转换；否则会返回一个错误。然而，如果该列表并非由一个函数名称及其参数构成，则该对象无法被求值。例如：

```
> a.list
[[1]]
[,1] [,2]
[1,]    1    3
[2,]    2    4
[[2]]
[1] 1 2 3 4
[[3]]
[1] "a" "b"
>
> as.character( a.list )
[1] "1:4"             "1:4"             "c(\"a\", \"b\")"
>
> a.lm
Call:
lm(formula = y ~ x)
Coefficients:
(Intercept)            x
1            1
> as.character( a.lm )
[1] "c(0.999999999999999, 1)"
[2] "c(0, 0, 0)"
[3] "c(-5.19615242270663, -1.41421356237309, 0)"
[4] "2"
[5] "c(2, 3, 4)"
[6] "0:1"
[7] "list(qr = c(-1.73205080756888, 0.577350269189626, 0.577350269189626, -3.46410161513776, -1.41421356237309, 0.965925826289068), qraux = c(1.57735026918963, 1.25881904510252), pivot = 1:2, tol = 1e-07, rank = 2)"
[8] "1"
[9] "list()"
[10] "lm(formula = y ~ x)"
[11] "y ~ x"
[12] "list(y = 2:4, x = 1:3)"
```

尝试各种不同类型的列表，看看 `as.character()` 的表现如何。

`call` 和 `expression` 模式的对象也可以强制转换为字符。`function`、`environment` 和 `S4` 模式的对象则不能。

函数 `is.character()` 用于测试该函数的参数是否为字符模式，如果是则返回 `TRUE`，否则返回 `FALSE`。任何对象都可以用作参数。

有关字符模式的更多信息，请在 R 提示符下输入 `?character` 或使用 R Studio 的“帮助”选项卡。

# 模式

```
> as.call( list( sum, y, x ) )
.Primitive("sum")(c(0.246754763464546, 2.1732142053051, -0.8773010303324
), 1:3)
> eval( as.call( list( sum, y, x ) ) )
[1] 7.542668
> as.call( list( sum, y~x ) )
.Primitive("sum")(y ~ x)
> eval( as.call( list( sum, y~x ) ) )
Error in .Primitive("sum")(y ~ x) : invalid 'type' (language) of argument
```

函数`is.call()`用于测试参数：如果参数是模式为`call`的对象则返回`TRUE`，否则返回`FALSE`。

关于模式`call`的更多信息，可以在 R 提示符下输入`?call`获得。

## 表达式模式

`expression`模式与`list`模式类似，但主要针对`class`或`function`等类型的对象。`expression`模式的对象可以像列表一样进行子集操作，并且在创建时不会被求值。`expression`模式使用原子模式所对应的三个函数：`expression()`、`as.expression()`和`is.expression()`。

函数`expression()`会列出输入到函数中的对象。对象之间用逗号分隔，且可以是任何模式。函数`eval()`可用于对表达式进行求值。在`eval()`下，仅对表达式中的最后一个对象进行求值。示例如下：

```
> expression( sin( c(  45, 90 )/180*pi ) )
expression(sin(c(45, 90)/180 * pi))
> eval( expression( sin( c( 45, 90 )/180*pi ) ) )
[1] 0.7071068 1.0000000
> expression( sin( c(  45, 90 )/180*pi, lm( y~x ) ) )
expression(sin(c(45, 90)/180 * pi, lm(y ~ x)))
> eval( expression( sin( c( 45, 90 )/180*pi ), lm( y~x ) ) )
Call:
lm(formula = y ~ x)
Coefficients:
(Intercept)            x
1.638       -0.562
```

函数`as.expression()`尝试将参数强制转换为`expression`模式。`NULL`、`call`、`name`和`pairlist`这些模式会被强制转换为一个单元素表达式。除`NULL`外的原子模式会按元素逐个强制转换。如果提供了多个对象，则仅使用第一个对象。列表在转换时，其模式会改变，但其他内容不变。如果尝试对其他模式的对象进行强制转换，则会报错。

```
> a.exp1=as.expression ( call( "sum", y, x ) )
> a.exp1
expression(sum(c(0.246754763464546, 2.1732142053051, -0.8773010303324
), 1:3))
> typeof( a.exp1 )
[1] "expression"
> eval( a.exp1 )
[1] 7.542668
> a.exp2=as.expression ( y, x )
> a.exp2
expression(0.246754763464546, 2.1732142053051, -0.8773010303324)
> typeof( a.exp2 )
[1] "expression"
> eval( a.exp2 )
[1] -0.877301
> a.exp3=as.expression ( list( y, x ) )
> a.exp3
expression(c(0.246754763464546, 2.1732142053051, -0.8773010303324
), 1:3)
> typeof( a.exp3 )
[1] "expression"
> eval( a.exp3 )
[1] 1 2 3
```

函数`is.expression()`对参数进行测试，如果参数的模式是表达式，则返回`TRUE`，否则返回`FALSE`。

关于`expression`模式的更多信息，可以在 R 提示符下输入`?expression`来获取。

## 环境模式

环境是 R 运行所依托的结构。R 启动时的环境是全局环境，即`R_GlobalEnv`。全局环境包含启动 R 会话时的工作空间。R 中的每个包都有自己的环境，可以通过`attach()`、`library()`或`require()`将其附加，并使用`detach()`将其分离。当函数运行时，它会创建自己的环境，该环境在函数运行结束后消失。环境之间存在嵌套关系，最低级别的环境是空环境。

与环境相关的函数有几个。函数`new.env()`用于为新的环境分配名称。函数`environment()`接受一个函数、一个公式或`NULL`作为参数，该函数要么返回参数的环境，要么为参数分配一个环境。

函数`is.environment()`用于测试参数是否为环境模式。函数`parent.env()`返回包含当前环境的上层环境。函数`search()`按照每个环境的位置顺序（从位置一开始）返回工作空间中存在的环境。

你可以在 R 会话中创建一个环境，并向该环境分配对象，例如：

```
> ne=new.env()
> ne

> attach( ne )
> environment( lm )=ne
> environment( lm )

> x=1:10
> y=2:11
> search()
[1] ".GlobalEnv"       "ne"                "tools:rstudio"
[4] "package:stats"    "package:graphics"  "package:grDevices"
[7] "package:utils"    "package:datasets"  "package:methods"
[10] "Autoloads"        "package:base"
> assign( "x", 0:9, pos=2 )
> assign( "y", 2:11, pos=2 )
> ls.str( "ne" )
x :  int [1:10] 0 1 2 3 4 5 6 7 8 9
y :  int [1:10] 2 3 4 5 6 7 8 9 10 11
> lm( y~x )
Call:
lm(formula = y ~ x)
Coefficients:
(Intercept)            x
1            1
> rm( y )
> rm( x )
> lm( y~x )
Call:
lm(formula = y ~ x)
Coefficients:
(Intercept)            x
2            1
> detach( ne )
> rm( ne )
```

首先，创建环境`ne`并附加它。然后，将函数`lm()`与`ne`关联。接着，在全局环境级别为变量`x`和`y`赋值，随后在环境`ne`级别再次赋值。函数`ls.str()`显示`ne`的内容。接下来，`lm()`运行了两次。函数`lm()`首先在全局环境中搜索`x`和`y`。如果在全局环境中未找到`x`和`y`，该函数会继续搜索`ne`环境。最后，`ne`被分离并移除。

在前面的示例中，`ne`的模式是环境。在 R Studio 中，环境位于“Data”下。可以通过在 R Studio 的“Environment”选项卡下打开“Global Environment”下拉菜单，并选择环境名称，来查看环境的内容。

关于环境及其相关函数的更多信息，可以在 R 提示符下输入`?environment`，或使用 R Studio 中的“Help”选项卡来获取。

## S4 模式

`S4`模式用于标识包含数据并被分配了 S4 类的对象。S4 类包含了供 S4 方法使用的数据结构。S4 类的数据被放入一个标识该类的对象中。数据以槽（slot）的形式输入，并通过名称引用。S4 方法是与一个或多个类相关联的函数。如果参数的模式是`S4`，则函数`mode()`返回`S4`。如果参数是 S4 对象，则`isS4()`函数返回`TRUE`，否则返回`FALSE`。

例如：

# 代码示例

```
> setClass( "linearmodel", slots=c(x="numeric", y="numeric" ) )
> setGeneric( "lm.fun", function( object ) { standardGeneric( "lm.fun" ) } )
[1] "lm.fun"
> setMethod( "lm.fun", "linearmodel", function( object ) { lm( object@y~object@x ) } )
> lm.data=new( "linearmodel", x=1:10, y=2:11 )
> lm.fun( lm.data )
Call:
lm(formula = object@y ~ object@x)
Coefficients:
(Intercept)     object@x
1               1
```

首先，定义 S4 类。接着，创建 S4 方法。然后，输入 S4 数据对象，并在该数据对象上运行函数。数据对象`lm.data`的模式是 S4。方法`lm.fun`的模式是函数。`lm.data`和`lm.fun`在输入`isS4()`时均返回`TRUE`。

在 R Studio 中，S4 函数位于“Values”下。S4 数据对象位于“Data”下。关于 S4 类的更多信息在第 5 章中给出，关于 S4 函数的更多信息在第 7 章中给出。

你可以在 R 提示符下输入`?S4`，或使用 R Studio 中的“Help”选项卡来获取更多信息。