# 结合 S3 与 S4 类系统

你可以在一定程度上结合使用 S3 和 S4 类系统。这两种系统不同，我**不推荐**尝试编写将 S3 和 S4 类层次结构紧密连接起来的软件。那样只会导致徒增烦恼与无尽调试。但如果你已有 S3 代码，并希望用 S4 编写扩展，这是可以做到的。

假设我们有一个 S3 类 `X`，它拥有泛型函数 `foo` 和 `bar`。

```
X <- function(x) {
structure(list(x = x), class = "X")
}
foo <- function(x) UseMethod("foo")
bar <- function(x) UseMethod("bar")
foo.X <- function(x) "foo"
bar.X <- function(x) x$x
x <- X(5)
foo(x)
## [1] "foo"
bar(x)
## [1] 5
```

如果我们想编写 `X` 的一个子类，称之为 `Y`，并且希望用 S4 来编写 `Y`，我们不能在 `setClass` 的 `contains` 选项中直接使用 `X`。虽然语法上可以，但当你尝试实例化该类的对象时会出错。S4 系统不知道任何名为 `X` 的类，因此我们首先需要让它识别。我们可以使用 `setOldClass` 函数来实现。

```
setOldClass("X")
```

这有两个作用：它让 S4 知道这个类的存在，以便我们可以继承它；同时，它使得为该类定义的任何泛型函数都能被我们通过 `setMethod` 来特化。因此，调用 `setOldClass` 后，我们就可以将 `X` 的一个子类定义为 S4 类。

```
Y <- setClass("Y", contains = "X")
```

在 `Y` 类型的对象上调用 `foo` 或 `bar` 同样会通过动态分派系统工作，由于没有更匹配的 `foo.Y` 函数，会调用 `foo.X`。

```
y <- Y()
foo(y)
## [1] "foo"
```

如果我们愿意，可以通过实现 `foo.Y` 来为 `Y` 对象创建特化版本的 `foo`。

```
foo.Y <- function(x) "Y::foo"
foo(y)
## [1] "Y::foo"
```

当然，这属于 S3 中特化泛型函数的方式，既然我们现在处理的是 S4 类，最好使用 `setMethod`。

与 `foo` 类似，调用 `bar` 会触发 `bar.X`。但在这里会导致错误，因为即使 S4 知道 `X` 类，它也不知道该类的构造函数或所创建的 `X` 对象的内部表示。

```
bar(y)
## Error in x$x: $ operator not defined for this S4 class
```

在 S3 中没有构造函数或对象表示的形式化定义，只有非正式的编码约定，所以 S4 无法知道 `bar` 函数期望从 `X` 对象中获取什么。S3 和 S4 的自动集成程度是有限的，需要一些编码工作才能使 S3 版本的功能也适用于 S4 类。

```
Y <- setClass("Y", contains = "X", slots = c(x = "ANY"))
setMethod("bar", signature = "Y",
definition = function(x) x@x)
y <- Y(x = 13)
bar(y)
## [1] 13
```

我**不建议**混合使用 S3 和 S4。如果你已有使用 S3 系统编写的代码，继续坚持使用 S3 可能比尝试结合两个系统更好。但是，如果你正在使用 S4 编写代码，并且需要引入一些 S3 类的功能，上述方法是可行的。

## R6 类系统

我们要看的最后一个对象系统是 R6 系统。这个系统与 S3 和 S4 非常不同，对于 R 语言整体而言也很不寻常，因为它引入了**可变数据**。在 R 中，数据通常是不可变的，任何看似修改对象的操作实际上都是通过复制数据和构造新对象来实现的。在 R 中唯一能修改的东西是环境；你不能修改数据。然而，R6 系统使用环境来提供给我们可以修改的对象。这在 R 中是不寻常的，但其语义却与大多数其他语言中实现面向对象的方式相似，即方法修改对象而非创建新对象。如果你从其他编程语言转到 R，R6 系统可能让你感到更熟悉；但由于它给 R 带来了副作用，如果你更熟悉 R 的传统特性，它可能会让你感到意外。如果你有两个指向同一个 R6 对象的引用，并通过其中一个变量修改了它，另一个变量所指对象的状态也会随之改变。这在另外两个类系统中是不会发生的。

R6 系统是对这种语义的更好实现，相较于内置的引用类系统（RC），后者也称为 R5。在来自 S 语言的 S3 和 S4 对象系统之后，R5 本是 R 中实现的下一个对象系统的自然名称。因为 R6 与 R5 具有相同的语义，并且被认为是其更好的实现，我将不再进一步讨论 R5。



## 定义类

类的定义需要使用 `R6` 包中的 `R6Class` 函数。与 S4 的 `setClass` 类似，我们需要为类指定一个名称，并且有多个可选参数用于定义该类的对象应具有的外观和行为。与 S4 不同的是，我们需要保存调用 `R6Class` 的返回值，以便用它来创建该类的对象。在 S4 中这只是一种约定，但只要我们知道类的名称就可以创建对象。而在 R6 中，类名主要用于设置 `class` 属性，以便对象可以与 S3 多态交互。我们用来创建对象的是 `R6Class` 的返回值。

例如，我们再次实现一个栈。这次我将不麻烦地创建一个抽象超类，而是直接实现一个 `VectorStack`。一种实现可能如下所示：

```
library(R6)
VectorStack <- R6Class("VectorStack",
private = list(elements = NULL),
public = list(
top = function() {
private$elements[1]
},
pop = function() {
private$elements <-
private$elements[-1]
invisible(self)
},
push = function(e) {
private$elements <-
c(e, private$elements)
invisible(self)
},
is_empty = function() {
length(private$elements) == 0
}
))
```

在这里，除了类名，我们向 `R6Class` 传入了两个参数：`private` 和 `public`。这两个参数用于定义类的属性，无论是存储在对象中的值还是可以在对象上调用的方法。对于两者，参数都是列表。列表元素的名称成为属性的名称，而其值——理所当然地——成为属性的值。

这两个参数的区别在于，`public` 列表中的属性可以在任何地方访问，而 `private` 列表中的属性只能在为该类定义的方法中访问。在方法中，你可以使用变量 `self` 访问 `public` 列表中的元素，并使用变量 `private` 访问 `private` 列表中的属性。在这个 `VectorStack` 实现中，我们将用于存储栈的向量设为私有，并将栈接口实现为公共方法。在方法内部，我们通过 `private$elements` 访问元素，在 `push` 和 `pop` 方法中，我们使用变量 `self` 返回对象本身。我们将对象包裹在 `invisible` 中返回，这样在调用这些方法时它不会自动打印出来，但我们并非必须这样做。我们甚至根本不必为这些方法返回对象，但这样做允许我们链式调用方法，正如我们将在下面看到的，因此这是一个好的实践。

请注意，`self` 和 `private` 对象并不是方法的参数。它们只是作为 R6 用于实现其可变对象语义的一部分魔法，存在于函数的命名空间中。

我们通过这种方式创建的 `VectorStack` 对象本身并不是构造函数，这与 S4 不同。它是一个所谓的对象生成器。要创建一个对象，我们使用这个生成器的 `new` 属性，如下所示：

```
(stack <- VectorStack$new())
## <VectorStack>
##   Public:
##     clone: function (deep = FALSE)
##     is_empty: function ()
##     pop: function ()
##     push: function (e)
##     top: function ()
##   Private:
##     elements: NULL
```

打印这个对象相当冗长，但如果我们为类定义 `print` 方法，就可以修改它的显示方式。

```
VectorStack <- R6Class("VectorStack",
private = list(elements = NULL),
public = list(
# ... 其余的方法
print = function() {
cat("Stack elements:\n")
print(private$elements)
}
))
```

这不会修改现有对象。

```
stack
## <VectorStack>
##   Public:
##     clone: function (deep = FALSE)
##     is_empty: function ()
##     pop: function ()
##     push: function (e)
##     top: function ()
##   Private:
##     elements: NULL
```

但是，如果我们创建一个新对象，它就会生效。

```
(stack <- VectorStack$new())
## Stack elements:
## NULL
```

我们可以使用 `$` 索引访问栈对象的（公共）属性，如果属性是函数，则可以调用方法：

```
stack$push(1)$push(2)$push(3)
stack
## Stack elements:
## [1] 3 2 1
while (!stack$is_empty()) stack$pop()
stack
## Stack elements:
## numeric(0)
```

这里之所以能够链式调用 `push`，是因为 `push` 方法返回了对象本身。与之前返回新对象的实现不同，对于 R6 对象，修改的是现有对象。我们不需要将三次 `push` 调用的结果重新赋值给 `stack`，也不需要将 `pop` 的调用结果重新赋值给 `stack`。在这些函数中返回对象本身允许我们链式调用方法，但仅此而已。R6 对象不是不可变的。

## 对象初始化

如果我们想在构造对象时设置其属性，需要做的工作比在 S4 中稍多一些。我们不能仅仅在构造函数中使用命名参数；这样的调用会给我们一个错误：

```
stack <- VectorStack$new(elements = 1:4)
```

为了能够以这种方式初始化对象，我们需要显式地为其编写一个函数。这个函数必须是一个名为 `initialize` 的 `public` 方法。如果你希望构造函数接受参数，则必须在此函数中指定参数。你不能将此函数设为 `private`；将一个名为 `initialize` 的函数放在 `private` 列表中是一个错误。

要用一系列元素初始化 `VectorStack` 对象，我们可以像这样实现它的 `initialize` 函数：

```
VectorStack <- R6Class("VectorStack",
private = list(elements = NULL),
public = list(
initialize = function(elements = NULL) {
private$elements <- elements
},
# ... 其余的方法
))
```

有了这个初始化函数，我们现在就可以用已初始化的 `elements` 构造对象了。

```
(stack <- VectorStack$new(elements = 1:4))
## Stack elements:
## [1] 1 2 3 4
```



