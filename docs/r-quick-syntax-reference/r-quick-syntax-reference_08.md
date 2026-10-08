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

# 对象的类

在 R 中，对象除了具有模式和类型，还属于某个类。类揭示了对象的结构信息。S3 和 S4 在类方面有所不同。在 S3 中，R 对象归属于一些特定的类。在 S4 中，用户为 S4 对象定义类。S3 中的类称为非正式类，而 S4 中的类称为正式类。本章将涵盖这两种类。

## 关于类的一些基础知识

S3 类是 S3 对象的属性，通常不由用户直接分配。给定一个对象，可以通过使用`class()`函数来查找该对象的类。如果一个对象在其所属的包中未被赋予类，那么该对象的类就是其自身的模式。例如，一个模式为`function`的对象，其类也是`function`。

许多函数的输出将拥有一个特定于该函数的类属性。例如，使用`lm()`函数拟合线性模型的输出，其类为`lm`。此外，对象可以属于多个类。一个例子是使用广义线性模型函数`glm()`拟合的模型，其输出拥有`glm`和`lm`两个类。

从更技术性的角度来看，根据`class()`的帮助页面，一个对象的类就是该对象所继承的类。因此，`lm()`的输出继承自`lm`，而`glm()`的输出则同时继承自`lm`和`glm`。

一个对类很有用的函数是`methods()`。输入`methods(class=name)`，其中`name`是类的名称，将显示专门编写用于应用于该类对象的函数。例如：

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

在 R 提示符下输入`?class`或使用 R Studio 的“帮助”选项卡，可以获取有关 S3 和 S4 类以及继承的更多信息。

## 向量

尽管没有名为`vector`的类，但向量作为最基本的对象类型之一，值得讨论。对于原子模式向量，向量是仅有一维的元素集合。其类就是向量的模式，但整数向量除外，它采用`integer`类。向量之所以重要的另一个原因是，对于`as.name()`函数（其中`name`是原子模式的名称，除了模式`NULL`），`as.name()`返回一个向量。

函数`vector()`、`as.vector()`和`is.vector()`是存在的，它们的工作方式与针对模式的类似函数有些相似。函数`vector()`接受参数`mode`和`length`，并创建一个指定模式和长度的向量。可接受的模式是原子模式——除了`NULL`、`list`模式和`expression`模式。其他模式会导致错误。

对于原子模式，

```
vector( mode="name", length=n )
```

的行为与

```
name( length=n ),
```

相同，其中`name`是模式的名称，`n`是长度参数。请注意，在调用`vector()`时，`name`必须用引号括起来。对于`list`模式，`vector()`返回一个由`NULL`组成的列表，列表的长度由长度参数给出。如果将模式设置为`expression`，`vector()`会生成一个表达式，其参数为`NULL`，`NULL`的数量由长度参数给出。

函数`as.vector()`尝试将对象强制转换为向量。对于某些对象，`as.vector()`只是传递该对象而不创建向量。对于其他一些对象，如果运行`as.vector()`函数，则会返回一个错误。

对于矩阵和数组，`as.vector()`会移除维度信息（例如，矩阵中的列名以及行数和列数），并返回一个由矩阵或数组元素组成的向量。向量的元素顺序从矩阵或数组的第一个维度开始，依次贯穿所有维度。例如：

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

这里，`c()`函数被用来为 2x2x2 的`array()`创建维度向量，并为数组的三个维度创建名称。

对于模式为`list`的对象，`as.vector()`会直接传递该列表。根据列表的结构，在结果上运行`is.vector()`可能返回`TRUE`或`FALSE`。模式不会改变。

对于模式为`function`的对象，`as.vector()`会返回一个错误。

对于模式为`call`的对象，`as.vector()`会传递该对象但不创建向量。模式不会改变。

对于模式为`environment`的对象，`as.vector()`会返回一个错误。

对于模式为`expression`的对象，`as.vector()`会传递该表达式，并且结果在`is.vector()`上返回`TRUE`。模式不会改变。

对于`S4`模式，`as.vector()`会返回一个错误。

函数`is.vector()`在对象是向量时返回`TRUE`，否则返回`FALSE`，尽管一些看起来不像向量的对象也可能返回`TRUE`。

关于`vector()`、`as.vector()`和`is.vector()`的更多信息，可以在 R 提示符下输入`?vector`或使用 R Studio 的帮助选项卡找到。

## 一些常见的 S3 类

一些常见的 S3 类包括`integer`、`numeric`、`matrix`和`array`。`integer`和`numeric`类的对象是向量。矩阵就是由行和列中的元素（所有元素模式相同）组成的对象。数组类似于矩阵，但它们可以拥有两个以上的维度。

其他一些常见的 S3 类包括：用于时间序列的`ts`和`mts`；用于因子的`factor`；用于日期的`Date`；以及用于带时间日期的`POSIXct`，所有这些类都是数值型的。

一些常见的模式为`list`的类有：用于数据框的`data.frame`；用于日期和时间的`POSTXlt`；以及包中大部分高级函数的输出，例如`lm`和`glm`。

`formula`类包含公式，其模式为`call`。

### 矩阵类：`matrix`

`matrix`类的对象是由原子模式（`NULL`除外）、`list`或`expression`模式中的元素组成的矩阵。存在`matrix()`、`as.matrix()`和`is.matrix()`这三个函数，其行为与原子模式的对应函数类似。

`matrix()`函数用于创建矩阵。该函数接受五个可能的参数。第一个参数是`atomic`、`list`或`expression`模式的对象。第二个参数是`nrow`，即行数。第三个参数是`ncol`，即列数。第四个参数是`byrow`，它告诉 R 按行而非按列填充矩阵。默认值为`FALSE`。`byrow`参数对于将表格化的原子数据扫描到矩阵中非常有用。第五个参数是`dimnames`，用于在调用`matrix()`时为行和列分配名称。`dimnames`的默认值是`NULL`，如果提供，它应该是一个包含两个名称向量的列表。可以使用`NULL`替代其中任何一个向量。

使用向量章节中的数组`a`，以下是创建矩阵的两个示例：

# R语言中的矩阵与数组

## 矩阵的创建与警告

R 语言使用 `matrix()` 函数来创建矩阵。其基本用法如下：

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

也可以通过指定 `byrow` 参数按行填充，并通过 `dimnames` 参数设置行名和列名：

```
> matrix(a,3,3, byrow=T, dimnames=list( NULL, c("c1","c2","c3") ) )
     c1 c2 c3
[1,]  1  2  3
[2,]  4  5  6
[3,]  7  8  1
警告信息：
在 matrix(a, 3, 3, byrow = T, dimnames = list(NULL, c("c1", "c2", "c3")  :
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

## 矩阵的转换

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

## 矩阵的测试

`is.matrix()` 函数用于测试对象是否属于 `matrix` 类。如果参数的类别是 `matrix`，则函数返回 `TRUE`，否则返回 `FALSE`。如果使用模式及类别均为 `expression` 的对象创建矩阵或将其强制转换为矩阵，则结果将具有 `matrix` 类别，即使结果的结构并不类似矩阵。

关于 `matrix()`、`as.matrix()` 和 `is.matrix()` 的更多信息，可以在 R 提示符下输入 `?matrix` 找到。关于 `data.matrix()` 的更多信息，可以在 R 提示符下输入 `?data.matrix` 找到。您也可以使用 R Studio 中的“帮助”选项卡。

### 数组类：`array`

`array` 类是一种使用维度组织数据的类，例如多维列联表。矩阵可以设置为二维数组，向量可以设置为一维数组。由 `array()` 创建的向量属于 `array` 类；然而，二维数组则属于 `matrix` 类，即便该对象是由 `array()` 创建的。

函数 `array()` 从某个对象创建一个数组。该函数接受三个参数。第一个参数是任何可以强制转换为向量的对象。第二个参数是一个向量，包含每个维度的大小，其长度等于数组的维度数。第三个参数是每个维度的名称列表，可以省略。默认值为 `NULL`。

以下是一个设置数组的示例：

```
```r
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

```r
start = c('年份', '月份编号')
```

并将 `frequency` 设置为

```r
frequency = 12
```

或者将 `deltat` 设置为

```r
deltat = 1/12,
```

其中 `year` 是起始年份，`month number` 是起始月份的编号（`1` 代表一月，`2` 代表二月，依此类推），这样就将月份和年份分配给了正在转换为时间序列的对象中的数据点。

要生成一个月度时间序列，请包含 `end` 参数：

```r
end = c('年份', '月份编号'),
```

其中 `year` 是结束年份，`month number` 是结束月份的编号。`ts()` 函数会循环使用第一个参数，直到时间序列填充完毕。（对于任何时间序列，提供起始值、结束值和频率，都会通过循环使用第一个参数来创建一个时间序列。如果第一个参数是一个矩阵，每一列会独立循环。）

对于季度数据，遵循相同的步骤，但使用的频率为 4。例如：

```r
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

```r
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
```

第五个参数是`ordered`，它存在于`factor()`中，但不存在于`ordered()`中。参数`ordered`指示`factor()`创建一个具有有序水平的因子。将`ordered`设置为`TRUE`的`factor()`函数与`ordered()`函数（仅出于与S语言向后兼容的目的而包含在当前版本的R中）的结果相同。第六个参数是`nmax`，描述为要使用的最大水平数，适用于要转换为因子的对象中包含许多值的情况。该参数似乎效果不佳。

有时会关注因子与原始数据之间的转换。如果在`factor()`中没有分配标签：
```
as.mode( levels( fac.obj ) )[ fac.obj ],
```
将返回对象的原始值，其中`mode`是原始对象的模式，`fac.obj`是因子对象。请注意，函数：
```
as.numeric( fac.obj ),
```
将返回与水平相关联的整数，即使原始对象不是`numeric`模式也是如此。如果分配了标签，则通常无法提取原始数据。

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

`as.factor()`函数的操作方式与`factor()`相同，但只接受一个参数，即要转换为因子的对象。

`is.factor()`函数用于测试对象是否为因子，如果是则返回`TRUE`，否则返回`FALSE`。

还有一个相关的函数`addNA()`。该函数创建一个包含缺失数据（`NA`）水平的因子对象。该函数接受两个参数。第一个参数是一个对象，可以从中创建`factor`类的对象。第二个参数是`ifany`。`ifany`参数是逻辑型的，如果仅在存在`NA`时才添加额外水平，则取值为`TRUE`；如果要始终包含该额外水平，则取值为`FALSE`。

有关这七个函数的更多信息，可以在R提示符下输入`?factor`，或使用R Studio中的“帮助”选项卡来查找。

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

## 日期与时间类：`Date`、`POSIXct`、`POSIXlt` 和 `difftime`

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

# 公式类（Formula Class）：formula

在 R 语言中，公式被多种函数使用，例如：`lm()`、`glm()`、`nls()`、`plot()`、`coplot()` 和 `boxplot()`。公式拥有自己的类，可以通过将一个对象赋值为公式、使用 `formula()` 函数或使用 `as.formula()` 函数来创建。公式的模式为 `call`。

如果在使用公式的函数中指定了数据框，那么该函数会首先在数据框中查找公式中的变量。如果没有分配数据框，或者变量不在数据框中，那么查找位置取决于创建公式所用的函数。这三种方法的区别在于 R 搜索公式中变量的环境不同。直接输入的公式会在使用该公式的环境中进行求值。使用 `formula()` 创建的公式会在创建该公式的环境中进行求值。使用 `as.formula()` 创建的公式会通过 `env` 参数分配一个环境，该参数默认为父环境。对于每个函数，公式都必须被引号括起来，才能进行环境分配。例如：

```
> a.fun
function() {
# 在第一层函数中
# 使用表达式和 formula() 定义的公式
cat( "\nlevel a \n\n" )
print( parent.frame() )
print( environment() )
x=1:10
y=11:20
cat( "\nx=", x )
cat( "\ny=", y, "\n" )
a.formula="y~x"
b.formula=formula("y~x")
b.fun=function() {
# 在第二层函数中
# 对在第一层定义的公式运行 lm()
cat( "\nlevel b \n\n" )
x=1:10
y=21:30
print( parent.frame() )
print( environment() )
print( lm( a.formula ) )
cat( "\nx=", x )
cat( "\ny=", y, "\n" )
print( lm( b.formula ) )
# cc 环境在第二层定义
# 运行来自 as.formula() 的公式
cat( "\nenvironment cc \n\n" )
cc=new.env()
assign( "x", 1:10, env=cc )
assign( "y", 31:40, env=cc )
c.formula=as.formula( "y~x", env=cc )
cat( "\nx=", cc$x )
cat( "\ny=", cc$y, "\n" )
print( lm( c.formula ) )
}
# 在第一层运行第二层函数
b.fun()
}
```

执行 `a.fun()` 函数。

```
> a.fun()
level a

x= 1 2 3 4 5 6 7 8 9 10
y= 11 12 13 14 15 16 17 18 19 20
level b

Call:
lm(formula = a.formula)
Coefficients:
(Intercept)            x
20            1
x= 1 2 3 4 5 6 7 8 9 10
y= 21 22 23 24 25 26 27 28 29 30
Call:
lm(formula = b.formula)
Coefficients:
(Intercept)            x
10            1
environment cc
x= 1 2 3 4 5 6 7 8 9 10
y= 31 32 33 34 35 36 37 38 39 40
Call:
lm(formula = c.formula)
Coefficients:
(Intercept)            x
30            1
```

公式对象 `formula.a` 使用其在 `b` 层运行时所在层级的数据。公式对象 `formula.b` 使用其在 `a` 层创建时所在层级的数据。公式对象 `formula.c` 使用在为此示例创建的 `cc` 环境中的数据。

公式函数使用一些专门的符号。符号 `~` 用于分隔公式的左侧和右侧。符号 `+` 告诉 R 将 `+` 两侧的变量包含在模型中。符号 `–` 告诉 R 不要使用 `–` 右侧的变量。（使用 `-1` 可以不用截距。）符号 `:` 告诉 R 使用 `:` 两侧变量之间的交互作用。符号 `*` 告诉 R 使用 `*` 两侧变量之间所有层级的交互作用。如果调用中提供了数据框，右侧的符号 `.` 告诉 R 使用数据框中尚未在模型中使用的所有变量。符号 `^` 告诉 R 使用直到 `^` 指定层级的所有交互作用。运算符 `%in%` 可用于嵌套变量。变量函数可以在公式中使用，但涉及算术表达式的函数需要包含在 `I()` 函数内，以避免混淆 R，因为这些符号在公式语句中具有特殊含义。

有关公式的更多信息，请在 R 提示符下输入 `?formula` 或使用 R Studio 中的“帮助”选项卡。

## S4 类（The S4 Class）

在 S4 中，数据对象具有用户定义的 S4（正式）类。有几个与 S4 类相关的函数，包括 `setClass()`、`removeClass()`、`getClass()`、`getClasses()` 和 `isClass()`。S4 类与 S4 方法一起使用，这将在第 [7] 章中介绍。

函数 `setClass()` 用于设置一个类，接受参数 `Class`、`representation`、`prototype`、`contains`、`validity`、`access`、`where`、`version`、`sealed`、`package`、`S3methods` 和 `slots`。参数 `Class` 是一个包含类名的字符串。字符串中不应有空格。名称之后最重要的参数是 `slots`，它是唯一必须包含的额外参数。参数 `slots` 是一个向量，向量中的每个元素都有一个名称和一个 S4 类（大多数 S3 类都有对应的 S4 版本）。例如：

```
> setClass( "example", slots=c( x="numeric", y="numeric",  z="matrix" ) )
> getClass( "example" )
Class "example" [in ".GlobalEnv"]
Slots:
Name:        x       y       z
Class: numeric numeric  matrix
```

第二个重要的参数是 `contains`。该参数包含要包含到正在定义的类中的其他类的名称。`contains` 参数中列出的类中的槽会被包含到新类中。这些名称是一个字符串向量。例如：

```
> setClass( "example.2", slots=c( xx="numeric", yy="numeric", zz="matrix" ), contains="example" )
> getClass( "example.2" )
Class "example.2" [in ".GlobalEnv"]
Slots:
Name:       xx      yy      zz
Class: numeric numeric  matrix
Name:        x       y       z
Class: numeric numeric  matrix
Extends: "example"
```

根据 CRAN 的作者所述，参数 `where`、`sealed` 和 `package` 是多余的，无需包含。参数 `prototype` 用于为槽提供默认值，最好使用 `initialize()` 函数来实现；参数 `validity` 用于对槽中的值设置限制，最好使用 `setValidity()` 函数来实现。

根据 CRAN 的作者所述，参数 `representation`、`access`、`version` 和 `S3methods` 已被弃用，不应再使用。

函数 `removeClass()` 用于移除一个类。它接受两个参数。第一个参数是要移除的类的名称，需用引号括起来。第二个参数是 `where`——开始查找该类的环境。默认值是运行 `removeClass()` 的环境。（要修改一个类，需先移除该类然后重新定义。）

函数 `getClass()` 返回一个类的内容。该函数接受三个参数：类的名称（需用引号括起来）、`.Force` 和 `where`。参数 `.Force` 是一个逻辑变量。如果设置为 `TRUE`，当类不存在时返回 `NULL` 而不是报错。默认值为 `FALSE`。参数 `where` 的含义与上一段中描述的一致。上面给出的两个示例都使用了 `getClass()`。

函数 `getClasses()` 用于获取一个环境中的类。该函数接受两个参数：`where` 和 `inherits`。参数 `where` 告诉 R 要搜索的特定环境。参数 `inherits` 是一个逻辑变量，当设置为 `TRUE` 时，告诉函数在所有的父环境中查找。默认情况下，如果未使用 `where` 参数，则 `inherits` 等于 `TRUE`，否则为 `FALSE`。例如：

```
> getClasses( .GlobalEnv )
[1] "example.2" "example"
```

不带参数运行 `getClasses()` 会返回父环境中的所有类，这些类可能很多。

函数 `isClass()` 用于测试某个类是否为 S4（形式）类。该函数接受三个参数：`Class`、`formal` 和 `where`。参数 `Class` 是类的名称，需用引号括起来。参数 `formal` 始终设置为 `TRUE`，表示测试针对 S4（形式）类。参数 `where` 告诉 R 在哪个环境中查找该类。默认情况下，使用调用环境层级。例如：

```
> isClass( "example" )
[1] TRUE
> isClass( "numeric" )
[1] TRUE
```

此处，`numeric` 既是 S3 类也是 S4 类。

更多关于 S4（形式）类的信息，可通过在 R 提示符下输入 `?setClass`、`?getClass` 和 `?getClasses`，或使用 R Studio 中的“帮助”选项卡获得。

## 向量、矩阵、数组和列表的名称

关于对象的章节不能缺少如何为向量、矩阵、数组和列表设置名称的信息。维度名称始终为 `character` 模式。对于多维对象，名称对象会组合在一个 `list` 中。

查看向量名称或为向量分配名称时，可使用 `names()` 函数。该函数仅接受一个参数，即对象本身。例如：

```
> cde
[1] 21 22 23 24 25 26 27 28 29 30
> names( cde )
NULL
> names( cde ) = paste( "v", 1:10, sep="" )
> cde
v1  v2  v3  v4  v5  v6  v7  v8  v9 v10
21  22  23  24  25  26  27  28  29  30
> names( cde )
[1] "v1"  "v2"  "v3"  "v4"  "v5"  "v6"  "v7"  "v8"  "v9"  "v10"
> mode( names( cde ) )
[1] "character"
> class( names( cde ) )
[1] "character"
```

你也可以在创建向量时直接为其分配名称。例如：

```
> a.vec = c( a=1, b=2, c=3 )
> a.vec
a b c
1 2 3
```

有些 `list` 模式的对象本身就是向量。对于这类列表，可使用 `names()` 或通过直接分配的方式为列表最低层级设置名称。

对于矩阵，有三个函数可用于查看或分配名称：`rownames()`、`colnames()` 和 `dimnames()`。函数 `rownames()` 和 `colnames()` 接受三个参数：R 对象、`do.NULL` 和 `prefix`。参数 `do.NULL` 是逻辑值，默认值为 `TRUE`，表示当行名或列名为 `NULL` 时不执行任何操作。如果 `do.NULL` 为 `FALSE`，则行名或列名将使用参数 `prefix` 的值作为前缀进行索引。例如：

```
> mat
[,1] [,2]
[1,]    1    3
[2,]    2    4
> colnames( mat )
NULL
> colnames( mat ) = colnames( mat, do.NULL=F, prefix="cl" )
> mat
cl1 cl2
[1,]   1   3
[2,]   2   4
```

注意，第三个表达式右侧仅返回列的名称，并未实际执行分配。

函数 `dimnames()` 可用于查看或为矩阵和数组分配名称。如果对某个对象使用 `dimnames()`，则会以列表形式返回该对象各维度的名称。如果使用 `dimnames()` 分配名称，则赋值号右侧的对象必须是一个列表，其最低层级元素的数量与该对象的维度数量相同，且每个最低层级元素要么是 `NULL`，要么长度与该矩阵或数组每个维度中的元素数量相同。例如：

```
> a
, , d31
d21 d22
d11   1   3
d12   2   4
, , d32
d21 d22
d11   5   7
d12   6   8
>
> dimnames( a )
[[1]]
[1] "d11" "d12"
[[2]]
[1] "d21" "d22"
[[3]]
[1] "d31" "d32"
>
> dimnames( a ) = list( c( "11", "12") ,c( "21", "22" ),c( "31", "32" ) )
>
> a
, , 31
21 22
11  1  3
12  2  4
, , 32
21 22
11  5  7
12  6  8
```

更多关于名称的信息，可通过在 R 提示符下输入 `?names`、`?rownames` 或 `?dimnames`，或使用 R Studio 中的“帮助”选项卡获得。
