# S4 类

S3 类系统是面向对象编程的“蛮荒西部”。类的层次结构仅通过 `class` 属性隐式存在，任何类都可以任意实现或不实现泛型方法，而无需检查接口和类层次结构是否被正确设计。一切都依赖于约定俗成，完全依赖程序员来确保任何形式的系统一致性。

S3 系统因其极其简单易用、易于编写新类而广受欢迎。在大多数情况下，我们追求简洁性而非复杂的设计，S3 在这些场景下完全能满足需求。当我们实现一个新的统计模型时，很少需要一个复杂的类层次结构。大多数抽象数据结构只有少数几个关联操作，在编写具体实现时，要记住并实现所有这些操作并不困难。然而，一旦软件复杂度达到一定程度，就需要更多的结构化支持。我们不希望设计仅仅存在于编程约定中，而是希望在代码中将其明确阐述。

S4 系统提供了一个结构更严谨的面向对象系统。在此，类和类层次结构被显式创建；它们不仅仅是 `class` 属性中的一个字符串。为了获得 S4 所允许的额外结构，在创建类和方法时需要多写一点代码，但总的来说，该系统的工作方式与 S3 非常相似。如果你熟悉 S3，那么学习 S4 应该没有问题。

## 定义 S4 类

在 S4 中，类是显式创建的。要创建新类，我们使用 `methods` 包中的 `setClass` 函数。该函数接受参数来指定该类对象应具有的属性、属性的默认值、该类如何融入类层次结构，以及所创建类的许多其他属性。所有这些属性都有默认值，因此我们可以仅通过指定类名来创建一个新类。

作为一个示例，我们考虑之前用 S3 实现的栈数据结构。要创建一个抽象栈类，我们可以这样写：

```r
library(methods)
Stack <- setClass("Stack")
```

这将创建一个名为 `"Stack"` 的新类。我们没有指定类的任何属性，因此 S4 会假设它是一个不应被实例化的抽象类，如果我们尝试实例化，将会出错。

我们可以像这样创建基于向量的栈类：

```r
VectorStack <- setClass("VectorStack",
    slots = c(
        elements = "vector"
    ),
    contains = "Stack")
```

这里我们向 `setClass` 传递了两个参数：`slots` 和 `contains`。`slots` 参数是该类对象应具有的属性列表。这里指定它应包含一个名为 `elements` 的向量。`contains` 参数指定新类应继承哪些超类。我们将 `VectorStack` 设为 `Stack` 的子类。

由于 `VectorStack` 具有槽，它不被视为抽象类（我们可以通过向 `contains` 参数添加 `"VIRTUAL"` 来显式使其成为抽象类，但我们确实希望能够实例化它）。我们可以通过调用 `VectorStack` 来创建该类的对象。

```r
(vs <- VectorStack())
## An object of class "VectorStack"
## Slot "elements":
## logical(0)
```

我们可以将 `elements` 作为命名参数传递给 `VectorStack`。

```r
(vs <- VectorStack(elements = 1:4))
## An object of class "VectorStack"
## Slot "elements":
## [1] 1 2 3 4
```

位置参数在此不适用；必须是命名参数。

一旦我们有了一个 `VectorStack` 类的对象，我们可以使用以下符号访问其元素：

```r
vs@elements
## [1] 1 2 3 4
```

通常，`@` 用于访问 S4 对象的槽。

捕获 `setClass` 的结果并将其用作构造对象的函数，是我创建 S4 构造函数时首选的方式，但严格来说，这并不是必需的。一旦使用 `setClass` 创建了一个类，你也可以直接使用类名和 `new` 函数来创建对象。

```r
new("VectorStack", elements = 1:4)
## An object of class "VectorStack"
## Slot "elements":
## [1] 1 2 3 4
```

## 通用函数

在 S3 系统中，我们像创建普通函数一样创建通用函数，只不过它会调用 `UseMethod`。而在 S4 中，我们必须使用 `setGeneric` 函数显式定义通用函数。`setGeneric` 的第一个参数是通用函数的名称。如果要将一个现有函数变为通用函数，只需以其名称调用 `setGeneric`，这将以该现有函数作为默认实现来创建通用函数。然而，通常情况下，我们使用 `setGeneric` 来创建一个全新的通用函数，此时我们需要通过参数 `def` 提供函数的定义。这个函数扮演着 S3 中函数定义的角色：它应该简单地调用 `standardGeneric` 函数，这是 S4 中与 `UseMethod` 对等的功能。

我们可以像这样定义栈（stack）抽象数据结构的接口：

```r
setGeneric("top",
    def = function(stack) standardGeneric("top"))
setGeneric("pop",
    def = function(stack) standardGeneric("pop"))
setGeneric("push",
    def = function(stack, element) standardGeneric("push"))
setGeneric("is_empty",
    def = function(stack) standardGeneric("is_empty"))
```

为了提供通用函数的实现，我们使用函数 `setMethod`。我们需要提供通用函数的名称、方法的签名（即用于分派方法的类型），以及函数的定义。

当调用通用函数时，具体的实现会根据传递给函数的参数的类型来选择。这就是我们所说的*动态分派*。在 S3 中，它基于单个参数（通常是第一个），但在 S4 中，我们可以基于更复杂的类型信息进行分派。为了获得与 S3 类似的行为，我们只需提供一个仅包含类名的签名。这样，S4 就会根据传递给通用函数的第一个参数的类来选择给定的实现。

我们可以这样实现向量栈（`VectorStack`）：

```r
setMethod("top", signature = "VectorStack",
    definition = function(stack) stack@elements[1])
setMethod("pop", signature = "VectorStack",
    definition = function(stack) {
        VectorStack(elements = stack@elements[-1])
    })
setMethod("push", signature = "VectorStack",
    definition = function(stack, element) {
        VectorStack(elements = c(element, stack@elements))
    })
setMethod("is_empty", signature = "VectorStack",
    definition = function(stack) length(stack@elements) == 0)
```

这些实现只是我们在 S3 实现中定义的函数的变体，并且我们可以像以前一样使用这个类。

```r
stack <- VectorStack()
stack <- push(stack, 1)
stack <- push(stack, 2)
stack <- push(stack, 3)
stack
## An object of class "VectorStack"
## Slot "elements":
## [1] 3 2 1
while (!is_empty(stack)) {
    stack <- pop(stack)
}
stack
## An object of class "VectorStack"
## Slot "elements":
## numeric(0)
```

## 槽原型

当你创建一个对象时，如果没有为槽（slot）提供值，你将得到默认值，通常是空向量或空列表。例如，如果我们创建一个表示自然数的类，我们可以这样写：

```r
NaturalNumber <- setClass("NaturalNumber",
    slots = c(
        n = "integer"
    ))
```

如果我们实例化它时不提供参数，该对象将为其本应表示的自然数包含一个空向量。

```r
(n <- NaturalNumber())
## An object of class "NaturalNumber"
## Slot "n":
## integer(0)
```

如果我们想要其他默认值，可以使用 `setClass` 的 `prototype` 参数。例如，我们可以声明默认的自然数是零。

```r
NaturalNumber <- setClass("NaturalNumber",
    slots = c(
        n = "integer"
    ),
    prototype = list(
        n = as.integer(0)
    ))
```

现在，当我们创建一个对象时，它会从原型（prototype）中获取默认值。

```r
(n <- NaturalNumber())
## An object of class "NaturalNumber"
## Slot "n":
## [1] 0
```

当然，这并不妨碍我们在创建对象时指定其他值作为参数。

```r
(n <- NaturalNumber(n = as.integer(1)))
## An object of class "NaturalNumber"
## Slot "n":
## [1] 1
```

## 对象有效性

我们在指定槽时给出的类型，对对象施加了类型约束。当我们声明 `NaturalNumber` 中的 `n` 槽应为整数（integer）时，我们就限制了可以分配给该槽的值。如果我们尝试分配一个 `numeric` 类型的值，将会得到一个错误。

对于自然数，我们不想包含负整数，但由于负整数仍是整数，所以分配这样的值并没有约束。

```r
n@n <- as.integer(-1)
```

我们可以通过 `setClass` 的 `validity` 参数对对象施加进一步的约束。这个参数应该是一个测试对象是否有效的函数。如果有效，它应该返回 `TRUE`。否则，应返回 `FALSE`。

```r
NaturalNumber <- setClass("NaturalNumber",
    slots = c(
        n = "integer"
    ),
    prototype = list(
        n = as.integer(0)
    ),
    validity = function(object) {
        if (object@n < 0) {
            return("negative natural number")
        }
        return(TRUE)
    })
```

有了这个有效性检查，尝试编写以下代码将导致错误：

```r
n <- NaturalNumber(n = as.integer(-1))
```

然而，有效性测试只在创建对象时执行。我们可以修改对象并将其置于无效状态。

```r
n@n <- as.integer(-1)
```

这种行为是必要的，因为在修改对象时，很可能在完成修改之前都处于无效状态。但是，当你在任何时候完成对象的修改后，都可以调用 `validObject` 函数再次检查有效性，使用方法如下：

```r
validObject(n)
```

当然，在这种情况下会失败，因为我们刚刚将 `n` 留在了无效状态。

## 泛型函数与类层次结构

要了解 S4 如何处理类层次结构和泛型函数，我们回到前面提到的 `A`、`B`、`C` 示例。我们可以像下面这样构造这些类和三个对象：

```r
A <- setClass("A", contains = "NULL")
B <- setClass("B", contains = "A")
C <- setClass("C", contains = "B")
x <- A()
y <- B()
z <- C()
```

这里我让 `A` 继承自伪类 `"NULL"`，使其成为非抽象类，这样即使它没有任何槽，我也可以实例化它。

我们可以定义一个泛型函数 `f`，并且只针对类 `A` 实现它，像这样：

```r
setGeneric("f", def = function(x) standardGeneric("f"))
setMethod("f", signature = "A",
    definition = function(x) print("A::f"))
```

如果我们这样做，那么当我们在所有三个对象上调用它时，就会调用这个版本。

```r
f(x)
## [1] "A::f"
f(y)
## [1] "A::f"
f(z)
## [1] "A::f"
```

如果我们定义另一个函数 `g`，并为 `A` 和 `B` 都实现它，那么在 `x` 上调用它将调用 `A` 版本。在 `y` 和 `z` 上调用它将调用 `B` 版本，因为对于这两个类来说，这是该函数最特化的形式。

```r
setGeneric("g", def = function(x) standardGeneric("g"))
setMethod("g", signature = "A",
    definition = function(x) print("A::g"))
setMethod("g", signature = "B",
    definition = function(x) print("B::g"))
g(x)
## [1] "A::g"
g(y)
## [1] "B::g"
g(z)
## [1] "B::g"
```

如果我们定义一个函数，并为所有三个类都实现了它，那么在 `x`、`y` 和 `z` 上调用它时，在所有三种情况下都会调用最特化的版本。

```r
setGeneric("h", def = function(x) standardGeneric("h"))
setMethod("h", signature = "A",
    definition = function(x) print("A::h"))
setMethod("h", signature = "B",
    definition = function(x) print("B::h"))
setMethod("h", signature = "C",
    definition = function(x) print("C::h"))
h(x)
## [1] "A::h"
h(y)
## [1] "B::h"
h(z)
## [1] "C::h"
```

S4 中 `NextMethod` 的对应物是 `callNextMethod`，其工作方式非常相似。

```r
setMethod("h", signature = "A",
    definition = function(x) {
        print("A::h")
    })
setMethod("h", signature = "B",
    definition = function(x) {
        print("B::h")
        callNextMethod()
    })
setMethod("h", signature = "C",
    definition = function(x) {
        print("C::h")
        callNextMethod()
    })
h(x)
## [1] "A::h"
h(y)
## [1] "B::h"
## [1] "A::h"
h(z)
## [1] "C::h"
## [1] "B::h"
## [1] "A::h"
```

不存在像 `.default` 这样的方法版本，但我们可以使用 `setGeneric` 函数来创建一个。如果我们定义一个普通的旧函数，并仅以其名称调用 `setGeneric`，那么该函数将成为当我们没有更特化版本时调用的默认函数。

```r
d <- function(x) print("default::d")
setGeneric("d")
d(x)
## [1] "default::d"
d(y)
## [1] "default::d"
d(z)
## [1] "default::d"
```

当然，这在我们特化方法并使用 `callNextMethod` 时也同样有效。

```r
setMethod("d", signature = "A",
    definition = function(x) {
        print("A::d")
        callNextMethod()
    })
setMethod("d", signature = "B",
    definition = function(x) {
        print("B::d")
        callNextMethod()
    })
setMethod("d", signature = "C",
    definition = function(x) {
        print("C::d")
        callNextMethod()
    })
d(x)
## [1] "A::d"
## [1] "default::d"
d(y)
## [1] "B::d"
## [1] "A::d"
## [1] "default::d"
d(z)
## [1] "C::d"
## [1] "B::d"
## [1] "A::d"
## [1] "default::d"
```

## 要求实现方法

我们之前定义的抽象类 `Stack` 没有任何实际用途。它是一个没有任何关联数据或函数的抽象类。栈接口中的所有函数都在 `VectorStack` 中实现，我们从继承 `Stack` 中并没有获得任何好处。然而，在 S4 中，我们可以形式化像栈这样的接口，并确保接口的实现实际上实现了接口中的所有函数。

这其实作用不大。R 中的类型检查非常少，你也不会从 S4 得到太多帮助，但有一种方法至少可以在你调用一个尚未实现的泛型函数时，使错误信息更具参考性。

让我们实现一个非功能性的栈。我们可以为基于列表的栈创建这个类。它继承自 `Stack`，但不添加任何槽或功能。

```
ListStack <- setClass("ListStack", contains = "Stack")
```

即使没有实现，我们也可以创建一个 `ListStack` 类型的对象。`Stack` 类是抽象的，因为我们没有给它添加任何槽，但 `ListStack` 不会被视为抽象类，即使它也没有添加任何槽，因为它 `contains` 一个超类。然而，如果我们在 `ListStack` 上调用 `pop`，我们会得到一个错误，这是正确的。我们期望在这里得到一个错误，在这种情况下，从错误信息中很可能不难弄清楚问题所在。错误会告诉我们 R 无法找到该函数的继承方法。但是，如果 `Stack` 实现了 `pop` 的版本，或者我们设置了默认函数，那么我们反而会调用那个版本，这虽然是一个错误，但可能不会抛出错误信息。

我们可以使用 `requireMethods` 函数来指定 `Stack` 的所有子类必须实现栈接口：

```
requireMethods(functions = c("top", "pop", "push", "is_empty"),
signature = "Stack")
```

通过这样做，我们确保在 `ListStack` 上调用这些方法中的任何一个时，都会得到错误信息。

```
pop(stack)
## An object of class "VectorStack"
## Slot "elements":
## numeric(0)
```

这对于确保接口正确实现来说，并不是一个很强大的安全检查，我通常认为它的用处不大，但如果你需要，它就在那里。

## 构造函数

在创建对象时，你可以通过提供命名参数来为槽赋值，但你也可以通过 `initialize` 方法获得对对象初始化的更多控制。这个方法充当构造函数的角色，只是它不创建对象；它只负责设置属性，使对象处于一致状态。如果你定义了这个函数，它将替换默认的构造函数，你将负责决定构造函数应该接受哪些参数、如何设置槽以及是否应该调用其超类的构造函数。

下面展示了一个非常简单的示例，其中我们有一个超类和一个子类：

```
A <- setClass("A", slots = list(x = "numeric", y = "numeric"))
B <- setClass("B", contains = "A", slots = list(z = "numeric"))
setMethod("initialize", signature = "A",
definition = function(.Object, x, y) {
print("A initialize")
.Object@x <- x
.Object@y <- y
.Object
})
## [1] "initialize"
setMethod("initialize", signature = "B",
definition = function(.Object, z) {
.Object <- callNextMethod(.Object, x = z, y = z)
.Object@z <- z
.Object
})
## [1] "initialize"
(a <- A(x = 1:3, y = 4:6))
## [1] "A initialize"
## An object of class "A"
## Slot "x":
## [1] 1 2 3
##
## Slot "y":
## [1] 4 5 6
(b <- B(z = 6:9))
## [1] "A initialize"
## An object of class "B"
## Slot "z":
## [1] 6 7 8 9
##
## Slot "x":
## [1] 6 7 8 9
##
## Slot "y":
## [1] 6 7 8 9
```

## 基于类型签名的分派

到目前为止，我们在定义特定方法时使用的签名仅包含类名。如果我们以这种方式使用它们，S4 方法的工作方式与 S3 泛型函数相同，但 S4 方法的分派机制更为通用，可以根据函数所有参数的类型进行分派。

例如，我们可以定义一个双参数函数 `f`，并根据两个参数的类型以不同方式进行细化。比如，当参数为数值型时使用一个版本，当参数为逻辑型时使用另一个版本。

```
setGeneric("f", def = function(x, y) standardGeneric("f"))
setMethod("f", signature = c("numeric", "numeric"),
definition = function(x, y) x + y)
setMethod("f", signature = c("logical", "logical"),
definition = function(x, y) x & y)
```

当调用 `f` 时，系统会根据参数类型选择合适的函数。

```
f(2, 3)
## [1] 5
f(TRUE, FALSE)
## [1] FALSE
```

类型匹配遵循从最具体到最抽象的原则，对于类来说遵循类的层次结构，对于数值则按整数 > 数值型 > 复数型的优先级进行匹配。因此，如果我们定义一个 `f` 版本，使其第一个值匹配整数类型，那么当提供整数参数时就会调用该版本，而使用 `numeric` 值调用时则使用上面定义的版本。

```
setMethod("f", signature = c("integer", "complex"),
definition = function(x, y) x - y)
f(2, 2)
## [1] 4
f(as.integer(2), 2)
## [1] 4
```

在这里，第二个参数会匹配任何复数，但我们可以将其特化以匹配整数和数值型：

```
setMethod("f", signature = c("integer", "numeric"),
definition = function(x, y) 2*x + y)
f(as.integer(2), 2)
## [1] 6
```

如果我们像之前在类的情况下那样，只给签名一个字符串，那么它只根据第一个参数的类型进行分派。

```
setMethod("f", signature = "character",
definition = function(x, y) x)
f("foo", "bar")
## [1] "foo"
```

通常，如果没有为所有参数提供类型，签名列表仅匹配参数前缀的类型。

```
setGeneric("g", def = function(x, y, z) standardGeneric("g"))
setMethod("g", signature = "character",
definition = function(x, y, z) "g(character)")
setMethod("g", signature = c("numeric", "character"),
definition = function(x, y, z) "g(numeric, character)")
g("foo", NA, NA)
## [1] "g(character)"
g(12, "bar", NA)
## [1] "g(numeric, character)"
```

如果你想匹配任何类型，可以使用类型 `"ANY"`。

```
setMethod("f", signature = "ANY",
definition = function(x, y) "any")
f(list(), NULL)
## [1] "any"
```

这可以用来定义一个通用的默认实现，当没有更具体的实现匹配参数时使用。

你甚至可以使用类型 `"missing"` 来匹配某些参数未提供的情况。

```
setMethod("f", signature = c("ANY", "missing"),
definition = function(x, y) "missing")
f(list(), NULL)
## [1] "any"
f(list())
## [1] "missing"
```

在这里，第一个调用匹配了接受任意参数的版本，因为第二个参数并未缺失，它只是 `NULL`；而另一个调用则匹配了第二个参数缺失的、更具体的签名。

## 运算符重载

S4 也支持运算符重载，其方式与 S3 非常相似，只是使用泛型方法的分派机制。我们可以尝试将模数类实现为一个 S4 类，如下所示：

```
modulus <- setClass("modulus",
slots = c(
value = "numeric",
n = "numeric"
))
setMethod("show", signature = "modulus",
definition = function(object) {
cat("Modulus", object@n, "values:\n")
print(object@value)
})
(x <- modulus(value = 1:6, n = 3))
## Modulus 3 values:
## [1] 1 2 3 4 5 6
```

我们在这里实现的 `show` 方法等同于 S3 中的 `print` 方法，我们用它来漂亮地打印 `modulus` 对象。

如果我们想实现单个运算符，可以像处理 S3 泛型函数那样，为其定义特定方法。但是我们可以利用签名类型匹配来捕获参数的不同组合，而不必像在 S3 中那样在泛型函数的开头编写类型检查代码。

下面我们处理模数运算所需的三种情况：两个操作数都是 `modulus` 对象，以及其中一个操作数是 `modulus` 对象而另一个是 `numeric` 的两种情况。

```
setMethod("+", signature = c("modulus", "modulus"),
definition = function(e1, e2) {
if (e1@n != e2@n) stop("Incompatible modulus")
modulus(value = e1@value + e2@value,
n = e1@n)
})
setMethod("+", signature = c("modulus", "numeric"),
definition = function(e1, e2) {
modulus(value = e1@value + e2,
n = e1@n)
})
setMethod("+", signature = c("numeric", "modulus"),
definition = function(e1, e2) {
modulus(value = e1 + e2@value,
n = e2@n)
})
```

现在，我们可以在加法运算中组合 `numeric` 和 `modulus` 类型，并且无需在函数中显式检查类型，因为类型分派机制已经为我们处理了。

```
x + 1:6
## Modulus 3 values:
## [1]  2  4  6  8 10 12
1:6 + x
## Modulus 3 values:
## [1]  2  4  6  8 10 12
```

我们还处理了两个 `modulus` 对象的情况，并检查它们的 `n` 槽是否相等。

```
y <- modulus(value = 1:6, n = 2)
x + y
## Error in x + y: Incompatible modulus
y <- modulus(value = 1:6, n = 3)
x + y
## Modulus 3 values:
## [1]  2  4  6  8 10 12
```

S4 中也有函数组解决方案，为了定义算术运算，我们需要 `Arith` 组。其工作方式与 S3 中的 `Ops` 泛型函数非常相似，只是我们再次可以使用类型匹配，而不是在函数内部显式检查参数类型。

```
setMethod("Arith",
signature = c("modulus", "modulus"),
definition = function(e1, e2) {
if (e1@n != e2@n) stop("Incompatible modulus")
modulus(value = callGeneric(e1@value, e2@value),
n = e1@n)
})
setMethod("Arith",
signature = c("modulus", "numeric"),
definition = function(e1, e2) {
modulus(value = callGeneric(e1@value, e2),
n = e1@n)
})
setMethod("Arith",
signature = c("numeric", "modulus"),
definition = function(e1, e2) {
modulus(value = callGeneric(e1, e2@value),
n = e2@n)
})
x * y
## Modulus 3 values:
## [1]  1  4  9 16 25 36
2 * x
## Modulus 3 values:
## [1]  2  4  6  8 10 12
```
