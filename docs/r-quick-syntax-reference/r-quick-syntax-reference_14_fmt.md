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