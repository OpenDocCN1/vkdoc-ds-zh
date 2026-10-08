# 6. S4 类

S3 类体系是面向对象编程的狂野西部。类层次结构仅通过 `class` 属性隐式存在，泛型方法可由任何类实现或不实现，且无需检查接口和类层次结构设计是否正确实现。一切都依赖于约定，确保任何类似一致性的工作完全由程序员负责。

S3 系统之所以流行，是因为它非常易于使用和编写新类。在大多数情况下，我们倾向于简单而非复杂的设计，而在这些情况下，S3 完美满足我们的需求。当我们实现一个新的统计模型时，很少需要复杂的类层次结构。大多数抽象数据结构只有少数相关操作，在编写具体版本时记住全部实现也没有问题。然而，当软件达到一定复杂度时，就需要更多的结构。与其让设计仅隐式存在于编程约定中，我们希望它在代码中被明确陈述。

S4 系统提供了一个结构更严谨的面向对象系统。在这里，类和类层次结构是显式创建的；它们不仅仅是 `class` 属性中的字符串。为了获得 S4 允许的附加结构，在创建类和方法时需要多写一些代码，但整体系统与 S3 非常相似。如果你熟悉 S3，那么学习 S4 应该不成问题。

## 定义 S4 类

在 S4 中，类是显式创建的。要创建一个新类，我们使用 `methods` 包中的 `setClass` 函数。该函数接受一些参数，用于指定该类对象应包含哪些属性、属性应具有的默认值、该类如何融入类层次结构，以及创建类的许多其他属性。所有这些属性都有默认值，因此我们只需指定类名即可创建新类。

作为示例，我们考虑之前用 S3 实现的栈数据结构。要创建一个抽象栈类，我们可以这样写：

```R
library(methods)
Stack <- setClass("Stack")
```

这将创建一个名为 `"Stack"` 的新类。我们没有指定该类的任何属性，因此 S4 会假定它是一个不应被实例化的抽象类，如果我们尝试实例化，将会报错。

我们可以像这样创建基于向量的栈类：

```R
VectorStack <- setClass("VectorStack",
                        slots = c(
                            elements = "vector"
                        ),
                        contains = "Stack")
```

这里我们为 `setClass` 使用了两个参数：`slots` 和 `contains`。`slots` 参数是该类对象应具有的属性列表。这里指定它应包含一个名为 `elements` 的向量。`contains` 参数指定新类应具有哪些超类。我们将 `VectorStack` 设为 `Stack` 的子类。

由于 `VectorStack` 具有槽（slots），它不被视为抽象类（我们可以通过向 `contains` 参数添加 `"VIRTUAL"` 来显式使其抽象，但我们希望能够实例化它）。我们可以通过调用 `VectorStack()` 来创建该类对象。

```R
(vs <- VectorStack())
## An object of class "VectorStack"
## Slot "elements":
## logical(0)
```

我们可以将 `elements` 作为命名参数传递给 `VectorStack()`。

```R
(vs <- VectorStack(elements = 1:4))
## An object of class "VectorStack"
## Slot "elements":
## [1] 1 2 3 4
```

此处不能使用位置参数；必须是命名参数。

一旦有了 `VectorStack` 类的对象，我们可以使用以下符号访问元素：

```R
vs@elements
## [1] 1 2 3 4
```

通常，`@` 用于访问 S4 对象的槽。

捕获 `setClass` 的结果并将其作为构造函数使用，是我个人创建 S4 构造器的首选方式，但严格来说这不是必需的。一旦用 `setClass` 构建了类，你就可以使用函数 `new`，仅通过类名来创建对象。

```R
new("VectorStack", elements = 1:4)
## An object of class "VectorStack"
## Slot "elements":
## [1] 1 2 3 4
```

## 泛型函数

在 S3 系统中，我们创建泛型函数的方式与创建普通函数相同，都是通过调用 `UseMethod` 来实现的。在 S4 中，我们必须使用 `setGeneric` 函数显式地定义泛型函数。`setGeneric` 的第一个参数是泛型函数的名称。如果你有一个已有的函数想使其成为泛型函数，只需调用 `setGeneric` 并传入函数名，就会创建一个以该现有函数为默认实现的泛型函数。不过，通常我们会用 `setGeneric` 来创建一个全新的泛型函数，在这种情况下，还需要提供该函数的定义。这通过参数 `def` 来完成。该函数的作用与 S3 中的函数定义相同：它应简单地调用函数 `standardGeneric`，即 S4 中与 `UseMethod` 对应的函数。

我们可以像下面这样定义栈抽象数据结构的接口：

```R
setGeneric("top",
           def = function(stack) standardGeneric("top"))
setGeneric("pop",
           def = function(stack) standardGeneric("pop"))
setGeneric("push",
           def = function(stack, element) standardGeneric("push"))
setGeneric("is_empty",
           def = function(stack) standardGeneric("is_empty"))
```

为了提供泛型函数的实现，我们使用 `setMethod` 函数。我们需要提供泛型函数的名称、方法的签名（用于分派方法的类型），以及函数的定义。

当调用泛型函数时，会根据函数参数的类型选择具体的实现。这就是我们所说的动态分派，在 S3 中它基于单个参数（通常是第一个参数），但在 S4 中，我们可以基于更复杂的类型信息进行分派。为了获得类似于 S3 的行为，只需提供一个类名作为签名即可。这样，S4 会根据泛型函数第一个参数的类来选择给定的实现。

我们可以像下面这样实现向量栈：

```R
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

这些实现只是我们在 S3 实现中定义的函数的变体，我们可以像之前一样使用这个类。

```R
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

### 槽位原型

当你创建对象时，如果没有为槽位提供值，则会获得默认值，这些默认值通常是空向量或空列表。例如，如果要创建一个用于表示自然数的类，可以这样编写：

```R
NaturalNumber <- setClass("NaturalNumber",
                          slots = c(
                              n = "integer"
                          ))
```

如果我们不带参数实例化它，该对象将包含一个空向量，用于表示它应该代表的自然数。

```R
(n <- NaturalNumber())
## An object of class "NaturalNumber"
## Slot "n":
## integer(0)
```

如果希望使用其他默认值，可以使用 `setClass` 的 `prototype` 参数。例如，我们可以指定默认的自然数为零。

```R
NaturalNumber <- setClass("NaturalNumber",
                          slots = c(
                              n = "integer"
                          ),
                          prototype = list(
                              n = as.integer(0)
                          ))
```

现在，当我们创建对象时，它将从原型中获取默认值。

```R
(n <- NaturalNumber())
## An object of class "NaturalNumber"
## Slot "n":
## [1] 0
```

当然，这并不妨碍我们在创建对象时将其他值作为参数传入。

```R
(n <- NaturalNumber(n = as.integer(1)))
## An object of class "NaturalNumber"
## Slot "n":
## [1] 1
```

### 对象有效性

当我们指定槽位时给出的类型对对象施加了类型约束。当我们声明 `NaturalNumber` 中的 `n` 槽位应为整数时，我们就限制了可以分配给该槽位的值。如果我们尝试分配一个 `numeric` 值，将会得到错误。

对于自然数，我们不希望包含负整数，但由于负整数仍然是整数，因此没有约束阻止分配这样的值。

```R
n@n <- as.integer(-1)
```

我们可以通过 `setClass` 的 `validity` 参数对对象施加进一步的约束。该参数应为一个函数，用于测试对象是否有效。如果有效，应返回 `TRUE`；否则应返回 `FALSE`。

```R
NaturalNumber = setClass("NaturalNumber",
                         slots = c(
                             n = "integer"
                         ),
                         validity = function(object) {
                             if (object@n >= 0) TRUE else "Negative number"
                         })
```

有了这个有效性检查，尝试执行以下操作将会导致错误：

```R
n <- NaturalNumber(n = as.integer(-1))
```

不过，有效性测试仅在创建对象时进行。我们可以修改对象并将其置于无效状态。

```R
n@n <- as.integer(-1)
```

这种行为是必要的，因为在修改对象时，它很可能在修改完成之前处于无效状态。但是，在修改对象完成后的任何时候，都可以调用 `validObject` 函数来再次检查有效性，如下所示：

```R
validObject(n)
```

当然，在这里它会失败，因为我们刚刚将 `n` 置于无效状态。

## 泛型函数与类继承体系

为了解 S4 如何处理类继承和泛型函数，我们回到之前的 `A`、`B`、`C` 示例。我们可以这样构建类以及三个对象：

```R
A <- setClass("A", contains = "NULL")
B <- setClass("B", contains = "A")
C <- setClass("C", contains = "B")
x <- A()
y <- B()
z <- C()
```

这里我让 `A` 继承自伪类 `"NULL"`，使其非抽象，这样即使它没有任何槽位也能实例化。

我们可以定义一个泛型函数 `f`，并仅为类 `A` 实现它，如下所示：

```R
setGeneric("f", def = function(x) standardGeneric("f"))
setMethod("f", signature = "A",
          definition = function(x) print("A::f"))
```

如果这样做，那么当我们对所有三个对象调用它时，都会调用这个版本。

```R
f(x)
## [1] "A::f"
f(y)
## [1] "A::f"
f(z)
## [1] "A::f"
```

如果我们定义另一个函数 `g`，并为 `A` 和 `B` 都实现它，那么对 `x` 调用它将调用 `A` 版本。对 `y` 和 `z` 调用它则会调用 `B` 版本，因为这是针对这两个类的最特化函数形式。

```R
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

如果我们定义一个为所有三个类都实现的函数，那么对 `x`、`y` 和 `z` 调用它时，在三种情况下都会调用最特化的版本。

```R
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

在 S4 中，与 `NextMethod` 对应的函数是 `callNextMethod`，其工作方式非常相似。

```R
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

方法本身没有 `.default` 版本，但我们可以使用 `setGeneric` 函数来创建一个。如果我们定义一个普通函数，并仅通过其名称调用 `setGeneric`，那么当我们没有更特化的版本时，该函数将成为默认调用的函数。

```R
d <- function(x) print("default::d")
setGeneric("d")
d(x)
## [1] "default::d"
d(y)
## [1] "default::d"
d(z)
## [1] "default::d"
```

当然，当我们在特化时使用 `callNextMethod`，这也同样有效。

```R
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

### 要求实现方法

我们之前定义的抽象类`Stack`没什么实际用途。它是一个没有关联数据或函数的抽象类。堆栈接口中的所有函数都在`VectorStack`中实现了，我们并没有从继承`Stack`中得到任何好处。然而，在S4中，我们可以将诸如堆栈之类的接口形式化，并确保接口的实现确实实现了接口中的所有函数。

这并不算多。R中的类型检查很少，你从S4那里也得不到太多帮助，但至少有一种方法可以在调用尚未实现的泛型函数时让错误消息更具信息性。

让我们实现一个不起作用的堆栈。我们可以为基于列表的堆栈创建这个类。它继承自`Stack`，但不添加任何槽位或功能。

```r
ListStack <- setClass("ListStack", contains = "Stack")
```

即使没有实现，我们也可以创建一个`ListStack`类型的对象。`Stack`类是抽象的，因为我们没有向它添加任何槽位，但`ListStack`并不被解释为抽象的，即使它也没有添加任何槽位，因为它`contains`一个超类。然而，如果我们对`ListStack`调用`pop`，我们会得到一个错误，这确实是应该的。我们期望在这里得到一个错误，并且在这种情况下，从错误消息中很可能不难找出问题所在。错误会告诉我们R无法找到该函数的继承方法。但如果`Stack`实现了`pop`的某个版本，或者我们设置了默认函数，那么我们会转而调用那个版本，这本身会是一个错误，但可能不会触发错误消息。

我们可以使用函数`requireMethods`来指定`Stack`的所有子类必须实现堆栈接口：

```r
requireMethods(functions = c("top", "pop", "push", "is_empty"),
signature = "Stack")
```

通过这样做，我们确保对`ListStack`调用这些方法中的任何一个都会给我们一条错误消息。

```r
pop(stack)
## An object of class "VectorStack"
## Slot "elements":
## numeric(0)
```

这对于接口的正确实现来说，算不上什么安全检查，我通常也觉得它没什么用，但如果你需要，它就在那里。

### 构造函数

当你创建对象时，可以通过提供具名参数来为槽位提供值，但你也可以通过`initialize`方法对对象初始化获得更多控制。这个方法起到构造函数的作用，只是它不创建对象；它只负责设置属性，使对象保持一致的状态。如果你定义了这个函数，它会替换默认构造函数，并且由你来控制构造函数应该接受哪些参数、如何设置槽位，以及是否应该调用其超类的构造函数。

下面是一个非常简单的例子，其中我们有一个超类和一个子类：

```r
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

## 基于类型签名的方法分派

到目前为止，我们在定义特定方法时所使用的签名只包含一个类名。如果我们这样使用，S4方法的工作方式与S3泛型函数类似，但S4的分派机制比这更通用，它可以根据函数所有参数的类型进行分派。

例如，我们可以定义一个包含两个参数的函数`f`，并根据这两个参数的类型以不同的方式对其进行细化。比如，我们可以有一个参数为数值型时的版本，以及另一个参数为逻辑型时的版本。

```r
setGeneric("f", def = function(x, y) standardGeneric("f"))
setMethod("f", signature = c("numeric", "numeric"),
definition = function(x, y) x + y)
setMethod("f", signature = c("logical", "logical"),
definition = function(x, y) x & y)
```

当调用`f`时，会根据参数的类型选择相应的函数。

```r
f(2, 3)
## [1] 5
f(TRUE, FALSE)
## [1] FALSE
```

类型匹配遵循类的继承层级，从最具体到最抽象。对于数值，它会优先匹配整型，然后才是数值型和复数型。因此，如果我们为`f`定义一个匹配第一个参数为整型的版本，那么当传入整型参数时，会调用这个版本；而当传入`numeric`类型的值时，则会调用上面定义的版本。

```r
setMethod("f", signature = c("integer", "complex"),
definition = function(x, y) x - y)
f(2, 2)
## [1] 4
f(as.integer(2), 2)
## [1] 4
```

这里，第二个参数可以匹配任何复数类型，但我们可以将其特化，以匹配整型和数值型：

```r
setMethod("f", signature = c("integer", "numeric"),
definition = function(x, y) 2*x + y)
f(as.integer(2), 2)
## [1] 6
```

如果我们只为签名提供一个字符串（就像之前处理类时那样），它只会根据第一个参数的类型进行分派。

```r
setMethod("f", signature = "character",
definition = function(x, y) x)
f("foo", "bar")
## [1] "foo"
```

通常，如果你没有为所有参数提供类型，签名列表只会匹配函数参数列表前缀部分的类型。

```r
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

如果你想匹配任意类型，可以使用`"ANY"`类型。

```r
setMethod("f", signature = "ANY",
definition = function(x, y) "any")
f(list(), NULL)
## [1] "any"
```

这可以用来定义一个通用的默认实现，当没有更具体的实现能匹配参数时，就会调用它。

你甚至可以使用`"missing"`类型来匹配某些参数未提供的情况。

```r
setMethod("f", signature = c("ANY", "missing"),
definition = function(x, y) "missing")
f(list(), NULL)
## [1] "any"
f(list())
## [1] "missing"
```

在这里，第一个调用匹配了接受任意参数的版本，因为第二个参数并非缺失，它只是`NULL`；而另一个调用则匹配了更具体的签名，即第二个参数是缺失的情况。

## 运算符重载

S4也支持运算符重载，方式与S3非常相似，只是使用了针对泛型方法的方法机制。我们可以像这样尝试将模数类实现为一个S4类：

```r
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

我们在这里实现的`show`方法是`print`的S4等价物，用于美化打印`modulus`对象。

如果我们接着想要实现单个运算符，可以像为S3定义泛型函数那样，特化该运算符对应的方法。但我们可以使用签名类型匹配来捕获不同的参数组合，而不必像在S3中那样，在泛型函数开头编写类型检查代码。

下面我们处理了模数运算所需的三种情况：两个操作数都是`modulus`对象，以及一个操作数是`modulus`对象而另一个是`numeric`的两种情况。

```r
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

现在，我们可以将`numeric`和`modulus`进行加法运算了，而且由于类型分派已经为我们处理了类型检查，我们无需在函数内部显式检查类型。

```r
x + 1:6
## Modulus 3 values:
## [1]  2  4  6  8 10 12
1:6 + x
## Modulus 3 values:
## [1]  2  4  6  8 10 12
```

我们还处理了两个`modulus`对象相加的情况，并检查它们的`n`槽位是否相等。

```r
y <- modulus(value = 1:6, n = 2)
x + y
## Error in x + y: Incompatible modulus
y <- modulus(value = 1:6, n = 3)
x + y
## Modulus 3 values:
## [1]  2  4  6  8 10 12
```

S4也支持函数组解决方案。要定义算术运算，我们需要使用`Arith`函数组。它的工作方式与S3中的`Ops`泛型函数非常相似，区别在于，我们再次可以使用类型匹配，而无需在函数内部显式检查参数类型。

```r
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

## 结合 S3 与 S4 类

你可以在有限程度上结合S3和S4。这两种体系各不相同，我不建议你尝试编写将S3和S4类层次结构紧密联系在一起的软件，这只会导致痛苦与懊悔。但如果你已有S3代码，并希望用S4编写扩展，这也是可行的。

假设我们有一个S3类`X`，以及泛型函数`foo`和`bar`。

```r
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

如果我们想编写`X`的一个子类，不妨称之为`Y`，并且希望用S4来编写`Y`，则不能在`setClass`的`contains`选项中使用`X`。其实你确实可以用，但如果尝试实例化该类的对象，则会出错。S4系统并不知道存在名为`X`的类，因此我们首先需要让它知道这个类。我们可以通过使用`setOldClass`函数来实现这一点。

```r
setOldClass("X")
```

这做了两件事：它让S4知道了这个类，以便我们可以继承它；同时，它使为该类定义的任何泛型函数都成为可以通过`setMethod`进行特化的函数。因此，在调用`setOldClass`之后，我们可以将`X`的子类创建为S4类。

```r
Y <- setClass("Y", contains = "X")
```

对类型为`Y`的对象调用`foo`或`bar`也会通过动态分派系统工作，并会调用`foo.X`，因为没有更匹配的`foo.Y`存在。

```r
y <- Y()
foo(y)
## [1] "foo"
```

如果愿意，我们可以通过实现`foo.Y`来为`Y`对象创建一个特化版本的`foo`。

```r
foo.Y <- function(x) "Y::foo"
foo(y)
## [1] "Y::foo"
```

当然，这属于S3方式下精炼泛型函数的做法，而由于我们现在处理的是S4类，使用`setMethod`会更好。

与`foo`类似，调用`bar`会调用`bar.X`。此时会导致错误，因为尽管S4知道`X`类，但它并不知道构造函数或其所创建的`X`对象的表示方式。

```r
bar(y)
## Error in x$x: $ operator not defined for this S4 class
```

在S3中，没有关于构造函数或对象表示形式的正式定义，只有非正式的编码惯例，因此S4无法知道`bar`函数期望从`X`对象中获取什么。我们自动集成S3和S4的能力是有限的，需要编写一些代码才能使S3版本的功能也与S4类相匹配。

```r
Y <- setClass("Y", contains = "X", slots = c(x = "ANY"))
setMethod("bar", signature = "Y",
definition = function(x) x@x)
y <- Y(x = 13)
bar(y)
## [1] 13
```

我不建议混合使用S3和S4。如果代码是用S3系统编写的，那么坚持使用S3可能比尝试将这两种系统结合起来更好。但如果你是用S4编写代码，需要包含一些来自S3类的功能，那么上述方法就是可行的做法。
