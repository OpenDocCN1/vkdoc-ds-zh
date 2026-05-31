# S4 方法

S4 方法用于构建 S4 的函数结构。一个 S4 方法包括：所创建函数的名称、该函数将要处理的数据的类，以及函数定义。该方法还可以指定在何处存储该方法（如果与创建工作区不同的话），以及是否密封该定义（即不允许未来修改）。S4 方法的定义依赖于一个同名的泛型函数的存在。

在 S3 中，泛型函数是指其行为方式取决于某个参数所属类的函数。在 S4 中，所有函数都是泛型的。在 S4 中，泛型函数要么被创建，要么已存在，然后为该泛型函数创建方法。一个方法依赖于该方法所使用的数据对象的类。`setMethod()` 的帮助页面指出，当对 S3 泛型函数运行 `setMethod()` 时，S4 通常会**自动**创建泛型函数，但对于新函数，则必须手动创建。方法可以添加到已有的 S4 泛型函数中。

函数 `setGeneric()` 用于创建一个泛型函数。`setGeneric()` 有十个参数，其中两个通常需要赋值。第一个是 "name"，它是一个字符串，包含要分配给该函数的名称。S4 函数名称的标准是 `小写开头后接大写`。第二个是 `def`，它是一个函数定义，在某些情况下是可选的。对于新的泛型函数，参数 `def` 被设置为等于 `standardGeneric()` 函数，该函数通常有一个参数 `f`，其值被设置为用引号括起来的函数名称。`setGeneric()` 的其余参数是可选的，或具有应使用的默认值。在 R 提示符下输入 `?setGeneric` 和 `?standardGeneric`，或使用 R Studio 中的“帮助”选项卡，以获取关于这两个函数的更多信息。

函数 `setMethod()` 与 `setGeneric()` 类似，但还包括了与方法相关的类。第一个参数是 `f`，它是一个字符串，表示要分配给该函数的名称。对于新定义的函数，在创建方法之前，必须先用所需的名称创建泛型函数。（参见后续示例。）如果泛型函数已存在，则名称必须匹配。第二个参数是 `signature`，它是一个字符向量，给出了该方法所使用的数据对象相关的类。

第三个参数 `definition` 定义了该方法的函数。函数定义通常是 S3 和 S4 的混合体；然而，通过签名类传入的变量使用 `@` 或 `slots()` 函数进行下标引用，而不是使用 `$` 或 `[[`。但是，假设在类 `mats` 中有一个槽 `mat`，且 `mat` 是一个矩阵，那么 `mat` 可以使用 S3 和 S4 方法的组合进行下标引用；例如，`mats@mat[ 1:3, 4 ]`。

`setMethod()` 的最后三个参数通常保留为其默认值。参数 `where` 告诉 R 在哪里存储该方法，默认是正在为其定义函数的包的命名空间。参数 `valueClass` 已过时，默认设置为 `NULL`。参数 `sealed` 允许你冻结对该方法的修改，默认设置为 `FALSE`。

此信息来自 `setMethod()` 的帮助页面，可通过在 R 提示符下输入 `?setMethod` 或使用 R Studio 中的“帮助”选项卡访问。

第二个创建和运行方法的示例如下（第 5 章中有一个示例）：

```
> setClass( "xyz", slots=c( x="numeric", y="numeric" ) )
> setMethod( "lmFunction", signature="xyz", function( x="xyz", y="missing", ... )
{ print( lm( x@y ~ x@x ) ) } )
Error in setMethod("lmFunction", signature = "xyz", function(x = "xyz",  :
no existing definition for function ‘lmFunction’
> setGeneric( "lmFunction", function( x, y, ... ) { standardGeneric( "lmFunction" ) } )
[1] "lmFunction"
> setMethod( "lmFunction", signature="xyz", function( x="xyz", y="missing", ... )
{ print( lm( x@y ~ x@x ) ) } )
> xy1=new( "xyz", x=1:10, y=21:30 )
> lmFunction( xy1 )
Call:
lm(formula = x@y ~ x@x)
Coefficients:
(Intercept)          x@x
20            1
```

可以看出，只有在泛型函数定义之后，才能定义方法。请注意，在 `setMethod()` 中定义的 `x` 包含了来自 `xy1` 定义的 `x` 和 `y`，并且 `y` 被设置为 `missing`。在 `setMethod()` 中，被设置为 `missing` 的变量不会被使用。还要注意，该函数是一个新函数，不在任何已加载的包中。

有一些测试函数用于确定函数的属性。函数 `isGeneric()`、`isS4()`、`isS3method()` 和 `isS3stdGeneric()` 可以帮助确定是否可以使用某个函数名称定义 S4 方法。例如：

```
> isGeneric( "lm" )
[1] FALSE
> isS4( "lm" )
[1] FALSE
> isS3method( "lm" )
[1] FALSE
> isS3stdGeneric( "lm" )
[1] FALSE
> setClass( "lm", slots=c( fo="formula", df="data.frame" ) )
Error in setClass("lm", slots = c(fo = "formula", df = "data.frame")) :
"lm" has a sealed class definition and cannot be redefined
```

这里，`lm()` 有一个密封的类定义，因此无法为该函数定义新方法。对于某些 S3 函数，可以定义方法。例如：

```
> isGeneric( "plot" )
[1] FALSE
> isS4( "plot" )
[1] FALSE
> isS3method( "plot" )
[1] FALSE
> isS3stdGeneric( "plot" )
plot
TRUE
> setClass( "plot", slots=c( x="numeric", y="numeric" ) )
> setMethod( "plot", signature="plot", definition=function( x, y ,... ){ plot( x@x, x@y ) } )
> tester=new( "plot", x=1:10, y=21:30 )
> plot ( tester )
> isGeneric( "plot" )
[1] TRUE
> isS4(  "plot"  )
[1] FALSE
> isS3method( "plot" )
[1] FALSE
> isS3stdGeneric( "plot" )
[1] FALSE
```

请注意，在为 `plot()` 创建方法后，该函数变成了一个 S4 泛型函数，而不再是 S3 标准泛型函数，至少在**工作区环境**中是如此。

还有许多其他与 S4 方法相关的函数。函数 `selectMethod()`、`findMethod()`、`getMethod()`、`existsMethod()` 和 `hasMethod()` 都被分组在同一个帮助页面中。函数 `selectMethod()` 和 `getMethod()` 返回该函数及其方法所属的类。函数 `existsMethod()` 和 `hasMethod()` 根据是否找到该方法而返回逻辑值 `TRUE` 或 `FALSE`。这些函数的区别在于它们是否允许继承。函数 `selectMethod()` 和 `existsMethod()` 允许继承。函数 `findMethod()` 返回该方法的位置。

所有这些函数的第一个参数都是 `f`，即泛型函数的名称。第二个参数是 `signature`，一个由类名称组成的字符向量，这些类是为其定义方法的类。函数 `selectMethod()` 和 `getMethod()` 行为相似，不同之处在于 `selectMethod()` 有三个 `getMethod()` 没有的参数：`useInherited`、`verbose` 和 `doCache`，这几个参数通常不会使用。



