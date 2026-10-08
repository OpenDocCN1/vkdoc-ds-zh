# 结合 S3 与 S4 类系统

你可以在一定程度上结合使用 S3 和 S4 类系统。这两种系统不同，我`不推荐`尝试编写将 S3 和 S4 类层次结构紧密连接起来的软件。那样只会导致徒增烦恼与无尽调试。但如果你已有 S3 代码，并希望用 S4 编写扩展，这是可以做到的。

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

我`不建议`混合使用 S3 和 S4。如果你已有使用 S3 系统编写的代码，继续坚持使用 S3 可能比尝试结合两个系统更好。但是，如果你正在使用 S4 编写代码，并且需要引入一些 S3 类的功能，上述方法是可行的。

## R6 类系统

我们要看的最后一个对象系统是 R6 系统。这个系统与 S3 和 S4 非常不同，对于 R 语言整体而言也很不寻常，因为它引入了`可变数据`。在 R 中，数据通常是不可变的，任何看似修改对象的操作实际上都是通过复制数据和构造新对象来实现的。在 R 中唯一能修改的东西是环境；你不能修改数据。然而，R6 系统使用环境来提供给我们可以修改的对象。这在 R 中是不寻常的，但其语义却与大多数其他语言中实现面向对象的方式相似，即方法修改对象而非创建新对象。如果你从其他编程语言转到 R，R6 系统可能让你感到更熟悉；但由于它给 R 带来了副作用，如果你更熟悉 R 的传统特性，它可能会让你感到意外。如果你有两个指向同一个 R6 对象的引用，并通过其中一个变量修改了它，另一个变量所指对象的状态也会随之改变。这在另外两个类系统中是不会发生的。

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

# 私有与公共属性

栈中的元素是私有的，因此我们无法像访问公共方法那样访问它们。你可能希望`stack$elements`会报错，但遗憾的是并非如此。

```
stack$elements
## NULL
```

这是因为访问一个不包含某个键的列表会返回`NULL`，你看到的行为正是如此。

```
list()$elements
## NULL
```

通过这个小例子，我们可以看到私有与公共属性之间的区别：

```
A <- R6Class("A", public = list(x = 5), private = list(y = 13))
```

根据类`A`的这个定义，我们应该能够访问对象的`x`属性，事实也确实如此。

```
a <- A$new()
a$x
## [1] 5
a$x <- 7
a$x
## [1] 7
```

我们也可以获取`y`，但它的行为与我们上面看到的栈的行为一致，并且我们不允许修改它，因为它并非对象真正的属性。

```
a$y
## NULL
a$y <- 12
## Error in a$y <- 12: cannot add bindings to a locked environment
a$y
## NULL
```

通常，你不能像在 S3 中那样，仅仅通过为`$`索引赋值来为 R6 对象创建新属性。属性必须在类定义中定义。如果你尝试类似下面的操作，将会收到错误：

```
a$z <- "foo"
```

你可以修改公共的数据属性，正如我们上面对于`x`所做的，但请不要自作聪明地尝试修改方法。首先，为单个对象更改方法本身就是非常糟糕的做法，但幸运的是，这在 R6 中也是“被禁止的”，如果你尝试，同样会收到错误。

通常，将数据保持为私有，而将属于类接口一部分的方法设为公共，被认为是良好的实践。这有几个原因：如果数据只能通过类的方法进行修改，那么你对对象的状态就有更多的控制权，可以确保在所有方法调用前后对象始终处于有效状态；但（或许更重要的是）隐藏对象的内部表示，可以限制类与使用该类的代码之间的依赖关系。如果任何代码都能访问对象的内部运作，那么很可能最终会有大量代码这样做。这意味着，如果你改变了类的对象的表示方式，就必须修改所有使用该类的代码。另一方面，如果代码仅通过公共接口访问对象，那么只要你保持公共接口不变，就可以随心所欲地修改所有私有属性。当然，你可能需要修改类的一些方法，但改动范围将仅限于此。

在 R6 系统中，私有属性只能由你为该类定义的方法或子类中定义的方法访问。如果你熟悉 C++ 或 Java 等语言，这可能会让你惊讶，但 R6 中的私有属性更类似于那些语言中的*受保护*属性，而非*私有*属性。

## 活动绑定

有一种方法可以获得访问数据属性的语法，而无需实际这样做。如果你的代码已经使用了公共属性，而你又想将其改为函数以隐藏或修改实现细节，你可以利用这一点。如果你只是更喜欢数据的语法而非方法调用，也可以使用它。

这是通过 `R6Class` 的 `active` 参数实现的。在这里，你可以提供一个属性列表，就像 `private` 和 `public` 一样，但这些属性应该是函数，它们将定义一个类似值调用的语法来调用这些函数。

举个例子，我们可以拿向量栈中的元素为例。我们希望可以写 `stack$elements`，但又不希望将元素设为公共。因此，我们为 `elements` 写一个函数，并将其添加到 `active` 中。同一个名称不能同时出现在 `private` 和 `active`（或 `public`）中，所以我们首先必须更改私有数据属性的名称，当然还要更新所有现有的方法。完成这些之后，我们可以像这样添加 `active` 函数：

```
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

`active` 列表中的函数应该接受一个参数 `value`。当我们读取属性时，这个值将是缺失的；当我们给属性赋值时，它将包含数据。在这个实现中，我们认为给元素赋值是一个错误，当读取属性时，我们返回私有的 `elements_`。

这将为我们提供元素：

```
stack <- VectorStack$new(elements = 1:4)
stack$elements
## [1] 1 2 3 4
```

同时，这将引发一个错误：

```
stack$elements <- rev(1:3)
## Error in (function (value) : elements are read-only
```

你可以使用这些 `active` 函数来修改你赋的值，无论是为了保证对象的一致性，还是为了模拟一个并非直接存储，而是可以从其他数据计算得出的“虚拟”属性。这一切都取决于你选择如何使用它们。

## 继承

我们指定类层次结构的方式，以及方法调用被派发到最具体方法实现的方式，相对比较直接。我们可以拿之前见过两次的三个类为例，在 R6 中实现它。要指定一个类继承自另一个类，我们使用 `R6Class` 的 `inherit` 参数；而要编写一个方法的更特化版本，我们只需将方法添加到 `public` 或 `private` 列表中。总体而言，与 S3 和 S4 相比，在 R6 中编写方法和类层次结构的代码要简单得多。

```
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

实例化这些类的对象的方式并无意外；我们必须使用对象生成器中的 `new` 方法。

```
x <- A$new()
y <- B$new()
z <- C$new()
```

对于方法 `f`，我们只有类 `A` 的实现，因此在所有三个对象上调用 `f` 都将调用该版本。除了方法调用的语法与 S3 和 S4 的实现不同之外，这里没有意外。

```
x$f()
## [1] "A::f"
y$f()
## [1] "A::f"
z$f()
## [1] "A::f"
```

对于 `g`，我们在 `A` 和 `B` 中都有实现，`C` 对象将调用 `B` 的实现，因为这是该类最具体的版本。

```
x$g()
## [1] "A::g"
y$g()
## [1] "B::g"
z$g()
## [1] "B::g"
```

最后，对于 `h`，我们在三个类中都有实现，因此我们为三个对象调用了不同的方法。

```
x$h()
## [1] "A::h"
y$h()
## [1] "B::h"
z$h()
## [1] "C::h"
```

关于 R6 中继承和方法派发的工作原理，不应该有任何意外之处。

## S3 系统与运算符重载的交互

虽然我们无法为 R6 对象定义新的运算符，但可以利用 S3 系统来实现这一目标。从 R6 对象生成器创建的对象会被赋予一个`class`属性——这是一个列表，包含我们创建生成器时为类指定的名称，以及`"R6"`——因此我们可以为它们定义泛型函数的特化方法。

我们可以用 R6 这样实现`modulus`类：

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
      cat("Modulus", private$n_, "values:\n")
      print(private$value_)
    }
  ),
  active = list(
    value = function(value) {
      if (missing(value)) private$value_
      else private$value_ <- value %% private$n_
    },
    n = function(value) {
      if (!missing(value)) stop("Cannot change n")
      private$n_
    }
  )
)
(x <- modulus$new(value = 1:6, n = 3))
## Modulus 3 values:
## [1] 1 2 3 4 5 6
```

在这个类定义中有几点需要注意。我们在`private`列表中定义了用于保存数据的属性，定义了一个初始化函数和一个打印函数，然后定义了两个`active`属性用于访问数据。我们允许该类的使用者修改`value`，但不允许修改`n`（这没有特别的理由，只是为了展示两种不同的行为示例），并且对于`value`属性，我们确保在将数据存储到私有变量`value_`之前对其进行修改。

此类对象的`class`属性包含以下内容：

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
    stop("Incompatible types")
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

其实现与 S3 版本略有不同，因为对象中的数据表示方式不同，但总体的控制流程是相同的。通过这个定义，我们就实现了模运算：

```
x + 1:6
## Modulus 3 values:
## [1] 2 1 0 2 1 0
1:6 + x
## Modulus 3 values:
## [1] 2 1 0 2 1 0
2 * x
## Modulus 3 values:
## [1] 2 1 0 2 1 0
```

如果你基于一个 R6 类（如`modulus`）创建子类，你得到的`class`属性也会反映这一点，因此 S3 的派发机制在 R6 系统中对于子类也同样有效：

```
modulus2 <- R6Class("modulus2", inherit = modulus)
y <- modulus2$new(value = 1:2, n = 3)
class(y)
## [1] "modulus2" "modulus"  "R6"
x + y
## Modulus 3 values:
## [1] 2 1 1 0 0 2
```

话虽如此，也不要过于疯狂地将 R6 和 S3 组合使用；这只会让你的代码维护者（很可能包括未来的你自己）感到困惑。

## 结论

关于 R 语言面向对象编程的这本书到此就结束了。你现在已经了解了 R 中三种不同的面向对象编程系统，以及如何使用它们来定义类层次结构和多态函数。

至少在 S3 和 S4 系统中，R 的面向对象编程与大多数其他面向对象编程语言有所不同。大多数语言认为对象是可变的，大多数面向对象的软件设计都涉及将对象相互引用关联起来，使得它们的行为依赖于其他对象不断变化的状态。R6 系统更接近这种类型的语言设计。然而，S3 和 S4 系统结合了两种强大的编程语言范式：函数式编程和面向对象编程。基于参数类型的动态函数派发与高阶函数式编程的结合，使你能够构建灵活且可扩展的软件。

在同一门语言中存在三种截然不同的面向对象编程系统可能会让人困惑，我建议你在任何一个单独的项目中坚持使用一种。然而，了解所有三种系统，并了解使用它们的优缺点，能让你为任何特定的工作选择正确的工具。S3 系统是三者中最简单的，适用于在短时间内快速搭建和运行小型模型。S4 更正式的类结构使得组织更复杂的框架更容易，而 R6 的引用语义则使得实现传统的可变数据结构比在 R 中通常能轻易实现的方式更简单。

当然，熟悉这些系统需要实践，仅仅阅读本书并不能让你成为面向对象编程的专家。但你现在掌握的知识已足够让你开始实践了。
