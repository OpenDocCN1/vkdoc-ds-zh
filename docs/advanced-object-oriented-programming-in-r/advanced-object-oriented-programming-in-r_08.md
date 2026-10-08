# R6 类

我们要看的最后一个对象系统是 R6 系统。这个系统与 S3 和 S4 非常不同，在 R 中通常也不常见，因为它引入了可变数据。在 R 中，数据通常是不可变的，任何看似修改对象的操作实际上都是通过复制数据并构造新对象来实现的。在 R 中，唯一可以修改的是环境；你不能修改数据。然而，R6 系统利用了环境来提供可以修改的对象。这在 R 中并不常见，但其语义类似于大多数其他语言中面向对象的实现方式，即方法修改对象而不是创建新对象。如果你是从其他编程语言转到 R，R6 系统可能更熟悉；但如果你更熟悉 R 本身，它引入的副作用可能会让你感到惊讶。如果有两个引用指向同一个 R6 对象，并通过其中一个变量修改它，那么另一个变量所引用的对象状态也会改变。这种情况在另外两个类系统中是不会发生的。

与内置的引用类（RC）系统（也称为 R5）相比，R6 系统是这种语义的更好实现。R5 是 R 中继 S3 和 S4 对象系统之后实现的下一个对象系统的自然名称，而 S3 和 S4 最初源自 S 语言。由于 R6 具有与 R5 相同的语义，并且被认为是其更好的实现，因此我不再进一步介绍 R5。

# 定义类

类通过 `R6` 包中的 `R6Class` 函数来定义。与 S4 中的 `setClass` 类似，我们需要为类指定名称，并可以使用多个可选参数来定义该类对象的外观和行为。与 S4 不同的是，我们需要捕获对 `R6Class` 调用的结果才能创建类对象。在 S4 中，这是一种约定，但只要知道类的名称，我们就能创建对象。在 R6 中，名称主要用于设置 `class` 属性，以便对象能够与 S3 的多态性进行交互。我们使用 `R6Class` 的返回值来创建对象。

举例来说，我们再次实现一个栈。这次我不会纠结于抽象超类，而是直接实现一个 `VectorStack`。一种实现方式如下：

```r
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

这里，除了类名之外，我们还使用了 `R6Class` 的两个参数：`private` 和 `public`。这些参数用于定义类的属性，可以是存储在对象中的值，也可以是我们可以对对象调用的方法。对于这两个参数，其值都是列表。列表元素的名称为属性名称，值即为属性值。

这两个参数的区别在于，`public` 列表中的属性可以在任何地方被访问，而 `private` 列表中的属性只能在为类定义的方法中访问。在方法中，您可以通过变量 `self` 访问 `public` 列表中的元素，并通过变量 `private` 访问 `private` 列表中的属性。在这个 `VectorStack` 实现中，我们将用于存储栈的向量设为私有，并将栈接口实现为公有方法。在方法内部，我们通过 `private$elements` 访问元素，并在 `push` 和 `pop` 中通过变量 `self` 返回对象本身。我们将此对象包裹在 `invisible` 中返回，以便在调用这些方法时不会自动打印，但这并非强制要求。对于这些方法，我们完全可以不返回任何对象，但返回对象本身允许我们进行方法链式调用（如下文所示），因此这是一种良好实践。

请注意，`self` 和 `private` 对象并不是方法的参数。它们作为 R6 实现其可变对象语义的魔法的一部分，存在于函数的命名空间中。

我们以这种方式创建的 `VectorStack` 对象本身并不是构造函数（如 S4 那样）。它是一个所谓的对象生成器。要创建对象，我们使用该生成器的属性 `new`，如下所示：

```r
(stack <- VectorStack$new())
##   Public:
##     clone: function (deep = FALSE)
##     is_empty: function ()
##     pop: function ()
##     push: function (e)
##     top: function ()
##   Private:
##     elements: NULL
```

打印这个对象会显得相当冗长，但如果为类定义 `print` 方法，我们就可以修改它的显示方式。

```r
VectorStack <- R6Class("VectorStack",
private = list(elements = NULL),
public = list(
# ... 其余方法
print = function() {
cat("栈元素：\n")
print(private$elements)
}
))
```

这并不会修改现有的对象。

```r
stack
## 
##   Public:
##     clone: function (deep = FALSE)
##     is_empty: function ()
##     pop: function ()
##     push: function (e)
##     top: function ()
##   Private:
##     elements: NULL
```

不过，如果创建一个新对象，它就会生效。

```r
(stack <- VectorStack$new())
## 栈元素：
## NULL
```

我们可以通过 `$` 索引访问栈对象的（公有）属性，如果属性是函数，则调用相应方法：

```r
stack$push(1)$push(2)$push(3)
stack
## 栈元素：
## [1] 3 2 1
while (!stack$is_empty()) stack$pop()
stack
## 栈元素：
## numeric(0)
```

这里能够实现链式调用 `push`，是因为 `push` 方法返回了对象本身。与先前 `push` 返回新对象的实现不同，对于 R6 对象，现有对象会被修改。我们不需要将三次 `push` 调用的结果重新赋值给 `stack`，也不需要将 `pop` 的调用结果重新赋值给 `stack`。在这些函数中返回对象本身允许我们进行链式方法调用，其作用也仅限于此。R6 对象不是不可变的。

## 对象初始化

如果希望在构造对象时设置属性，我们需要比在 S4 中做更多的工作。我们不能仅使用构造函数中的命名参数；以下调用会报错：

```r
stack <- VectorStack$new(elements = 1:4)
```

要能够以这种方式初始化对象，我们需要显式编写一个函数。该函数必须是一个名为 `initialize` 的 `public` 方法。如果希望构造函数接受参数，则必须在此函数中指定参数。您不能将此函数设为 `private`；将名为 `initialize` 的函数放入 `private` 列表会报错。

为了用一个元素序列初始化 `VectorStack` 对象，我们可以像下面这样实现其 `initialize` 函数：

```r
VectorStack <- R6Class("VectorStack",
private = list(elements = NULL),
public = list(
initialize = function(elements = NULL) {
private$elements <- elements
},
# ... 其余方法
))
```

有了这个初始化函数，我们现在就可以构造带有初始化 `elements` 的对象了。

```r
(stack <- VectorStack$new(elements = 1:4))
## 栈元素：
## [1] 1 2 3 4
```

## 私有属性和公共属性

栈中的元素是私有的，因此我们不能像访问公共方法那样直接访问它们。你可能会期望 `stack$elements` 会报错，但可惜并不会。

```r
stack$elements
## NULL
```

这是因为使用一个不存在的键去访问列表时会返回 `NULL`，你遇到的就是这种机制。

```r
list()$elements
## NULL
```

通过下面这个小例子，我们可以看出私有属性和公共属性的区别：

```r
A <- R6Class("A", public = list(x = 5), private = list(y = 13))
```

根据类 `A` 的这个定义，我们应该能够访问对象的 `x` 属性，事实也确实如此。

```r
a <- A$new()
a$x
## [1] 5
a$x <- 7
a$x
## [1] 7
```

我们也可以尝试获取 `y`，但它的行为与我们之前看到的栈的情况相同，而且我们无法修改它，因为它实际上并不是该对象的一个属性。

```r
a$y
## NULL
a$y <- 12
## Error in a$y <- 12: cannot add bindings to a locked environment
a$y
## NULL
```

一般来说，你不能像在 S3 中那样，仅通过向 `$` 索引的值赋值来为 R6 对象创建新属性。属性必须在类定义中声明。如果你尝试类似下面这样的操作，就会报错：

```r
a$z <- "foo"
```

你可以修改公共数据属性，就像我们上面修改 `x` 那样，但不要企图耍小聪明去修改方法。一开始就为单个对象更改方法是非常糟糕的实践，幸运的是，在 R6 中这也是“被禁止的”，如果你尝试的话，将会收到错误提示。

通常，将数据设为私有并将作为类接口一部分的方法设为公共，被认为是良好的实践。这有几个原因：如果数据只能通过类的方法来修改，那么你就能更好地控制对象的状态，并确保在调用所有方法前后，对象始终处于有效状态。但（也许更重要的是）将对象的表示方式隐藏起来，可以限制该类与使用该类的代码之间的依赖关系。如果任何代码都能访问对象的内部工作方式，那么最终很可能会有大量代码依赖于此。这意味着，如果你改变了类对象的表示方式，就必须修改所有使用该类的代码。反之，如果代码仅通过公共接口访问对象，那么只要你保持公共接口不变，就可以随心所欲地修改所有私有属性。当然，你需要修改该类的某些方法，但修改范围将被限制在类内部。

在 R6 系统中，私有属性只能由你为该类定义的方法或在其子类中定义的方法访问。如果你习惯使用像 C++ 或 Java 这样的语言，这可能会让你感到惊讶。实际上，R6 中的私有属性类似于那些语言中的受保护（protected）属性，而非私有（private）属性。

## 主动绑定

有一种方法可以让你在语法上像访问数据属性那样，但实际上并未直接访问它们。如果你有一段代码已经使用了某个公共属性，而你想将其改为一个函数以隐藏或修改实现细节，就可以使用这种方法。如果你只是更喜欢数据属性的语法而非方法调用，也可以使用它。

这是通过 `R6Class` 的 `active` 参数实现的。在这里，你可以像为 `private` 和 `public` 参数一样提供一个属性列表，但这些属性应该是函数，它们将为调用这些函数定义一种类似值的语法。

举个例子，我们可以看看向量栈中的元素。我们希望可以写成 `stack$elements`，但又不想让元素变成公共的。因此，我们为 `elements` 编写一个函数并将其添加到 `active` 中。我们不能在 `private` 和 `active`（或 `public`）中使用相同的名称，所以必须先更改私有数据属性的名称，当然还要更新所有现有的方法。完成这些操作后，我们可以像这样添加 `active` 函数：

```r
VectorStack <- R6Class("VectorStack",
private = list(elements_ = NULL),
public = list(
# ... 方法
),
active = list(
elements = function(value) {
if (!missing(value))
stop("elements are read-only")
private$elements_
}
))
```

`active` 列表中的函数应接受一个参数 `value`。当我们读取属性时，`value` 会缺失；当我们为属性赋值时，`value` 将包含数据。在此实现中，我们将赋值给元素视为错误，并在读取属性时返回私有的 `elements_`。

这样我们就可以获取元素了：

```r
stack <- VectorStack$new(elements = 1:4)
stack$elements
## [1] 1 2 3 4
```

同时，下面这种操作会引发错误：

```r
stack$elements <- rev(1:3)
## Error in (function (value) : elements are read-only
```

你可以使用这些 `active` 函数来修改你赋予的值，无论是为了确保对象一致性，还是为了伪造一个并非直接存储，而是隐含存在（可通过其他数据计算得出）的属性。这完全取决于你如何使用它们。

## 继承

在 R6 中，指定类层次结构以及将方法调用分派到最具体的方法实现的方式相对直接。我们可以以之前见过两次的三个类为例，在 R6 中实现它。要指定一个类继承自另一个类，我们使用 `R6Class` 的 `inherit` 参数；要编写一个方法的更具体版本，我们只需将该方法添加到 `public` 或 `private` 列表中。总的来说，在 R6 中编写方法和类层次结构的代码比在 S3 和 S4 中都更简单。

```r
A <- R6Class("A",
public = list(
f = function() print("A::f"),
g = function() print("A::g"),
h = function() print("A::h")
))
B <- R6Class("B", inherit = A,
public = list(
g = function() print("B::g"),
h = function() print("B::h")
))
C <- R6Class("C", inherit = B,
public = list(
h = function() print("C::h")
))
```

实例化这些类的对象时没有意外之处；我们必须使用对象生成器中的 `new` 方法。

```r
x <- A$new()
y <- B$new()
z <- C$new()
```

对于方法 `f`，我们只在类 `A` 中有一个实现，因此在三个对象上调用 `f` 都会调用该版本。除了方法调用的语法与 S3 和 S4 的实现不同之外，这里没有意外。

```r
x$f()
## [1] "A::f"
y$f()
## [1] "A::f"
z$f()
## [1] "A::f"
```

对于 `g`，我们在 `A` 和 `B` 中都有实现，而 `C` 对象将调用 `B` 的实现，因为这是对该类来说最具体的方法。

```r
x$g()
## [1] "A::g"
y$g()
## [1] "B::g"
z$g()
## [1] "B::g"
```

最后，对于 `h`，我们在所有三个类中都有实现，因此三个对象会调用不同的方法。

```r
x$h()
## [1] "A::h"
y$h()
## [1] "B::h"
z$h()
## [1] "C::h"
```

关于 R6 中继承和方法分派的工作原理，应该没有任何令人意外的地方。

## 与 S3 交互及运算符重载

我们没有为 R6 对象定义新运算符的机制，但可以利用 S3 系统来实现。由 R6 对象生成器创建的对象会被赋予一个 `class` 属性，该属性是一个列表，包含创建生成器时指定的类名以及 `"R6"`，因此我们可以为它们定义泛型函数的特化版本。

我们可以像这样用 R6 实现 `modulus` 类：

```
modulus <- R6Class("modulus",
private = list(
  value_ = c(),
  n_ = c()
),
public = list(
  initialize = function(value, n) {
    private$value_ <- value
    private$n_ <- n
  },
  print = function() {
    cat("模数", private$n_, "值:\n")
    print(private$value_)
  }
),
active = list(
  value = function(value) {
    if (missing(value)) private$value_
    else private$value_ <- value %% private$n_
  },
  n = function(value) {
    if (!missing(value)) stop("无法修改 n")
    private$n_
  }
))
(x <- modulus$new(value = 1:6, n = 3))
## 模数 3 值:
## [1] 1 2 3 4 5 6
```

在这个类定义中有几个要点。我们在 `private` 列表中定义了存放数据的属性，定义了初始化函数和打印函数，然后定义了两个用于访问数据的 `action` 属性。我们允许类的使用者修改 `values`，但不能修改 `n`（没有特别的理由，只是为了展示两种不同的行为）。对于 `values` 属性，我们确保在将数据存入私有变量 `values_` 之前对其进行修改。

该类对象的 `class` 属性包含以下内容：

```
class(x)
## [1] "modulus" "R6"
```

这意味着我们可以像这样使用 S3 系统为该类定义算术运算：

```
Ops.modulus <- function(e1, e2) {
  nx <- ny <- NULL
  if (inherits(e1, "modulus")) nx <- e1$n
  if (inherits(e2, "modulus")) ny <- e2$n
  if (!is.null(nx) && !is.null(ny) && nx != ny)
    stop("类型不兼容")
  n <- ifelse(!is.null(nx), nx, ny)
  v1 <- e1
  v2 <- e2
  if (inherits(e1, "modulus")) v1 <- e1$value
  if (inherits(e2, "modulus")) v2 <- e2$value
  e1 <- v1 ; e2 <- v2
  result <- NextMethod() %% n
  modulus$new(result, n)
}
```

该实现与 S3 版本略有不同，因为对象中数据的表示方式不同，但总体控制流程是相同的。有了这个定义，我们就实现了模数算术。

```
x + 1:6
## 模数 3 值:
## [1] 2 1 0 2 1 0
1:6 + x
## 模数 3 值:
## [1] 2 1 0 2 1 0
2 * x
## 模数 3 值:
## [1] 2 1 0 2 1 0
```

如果你为 R6 类（如 `modulus`）创建子类，会得到一个同样反映该继承关系的 `class` 属性，因此 S3 的分派机制也适用于 R6 系统中的子类。

```
modulus2 <- R6Class("modulus2", inherit = modulus)
y <- modulus2$new(value = 1:2, n = 3)
class(y)
## [1] "modulus2" "modulus"  "R6"
x + y
## 模数 3 值:
## [1] 2 1 1 0 0 2
```

话虽如此，也不要过度混用 R6 和 S3；这只会让你的代码维护者感到困惑（很可能未来的你也包括在内）。
