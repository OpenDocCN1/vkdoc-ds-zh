# 第 3 章：纯函数式编程——尾递归

像我们的搜索函数这样，在递归调用后直接返回结果而不进行进一步计算的函数，被称为**尾递归**函数。这类函数在函数式编程语言中特别受欢迎，因为它们可以被转换为循环，从而消除调用函数的开销。然而，遗憾的是，R 语言并未实现这种尾递归优化。这背后有充分但技术性的原因，与作用域有关。但这并不意味着我们无法利用尾递归以及通过编写尾递归函数所能实现的优化；我们只需将函数显式地转换为循环即可。

我们将在下一节讨论这个问题。首先，我将向你展示一种技巧，将原本非尾递归的函数转化为尾递归函数。

## 转换技巧

只要你的函数只进行零次或一次递归调用，这便是一个非常简单的技巧。你可以在递归调用中传递值，这些值在递归到达基准情况时可用于计算最终结果。

以一个简单的例子来说，我们来看阶乘函数。我们之前编写的方式不是尾递归的。我们进行递归调用，然后将 `n` 乘以结果：

```
factorial <- function(n) {
  if (n == 1) 1
  else n * factorial(n - 1)
}
```

我们可以通过将到目前为止所见数字的乘积传递给递归调用，将其转换为尾递归函数。这种传递的值通常被称为累加器。尾递归函数如下所示：


类似地，我们可以将我们编写的 `find_duplicates` 函数转换为尾递归函数。原始函数如下所示：

```
find_duplicates <- function(x, i = 1) {
  if (i >= length(x)) return(c())
  rest <- find_duplicates(x, i + 1)
  if (x[i] == x[i + 1]) c(i, rest) else rest
}
```

它需要返回一个索引列表，因此我们将这个列表作为累加器传递：

```
find_duplicates <- function(x, i = 1, acc = c()) {
  if (i >= length(x)) return(acc)
  if (x[i] == x[i + 1]) find_duplicates(x, i + 1, c(acc, i)) else find_duplicates(x, i + 1, acc)
}
```

所有最多只进行一次递归调用的函数，都可以使用适当的累加器轻松地转换为尾递归函数。

对于进行多次递归调用的函数，比如我们之前编写的树函数，则要困难一些。让它们变成尾递归并非不可能，但这需要一种称为*续延传递*的技巧，我将在第[5](https://doi.org/10.1007/978-1-4842-9487-1_5)章中向你展示。

# 第 3 章：纯函数式编程——运行时考量

现在来谈坏消息。我在本章中向你展示的所有使用递归而不是循环来编写纯函数式程序的技术，在 R 语言中相对较慢。

从软件设计的角度来看，你希望编写纯函数，但依赖递归而非循环会带来运行时成本。我们可以将递归线性搜索函数与使用 `for` 循环的版本进行比较，看看会带来多少额外开销。

递归函数如下所示：

```
r_lin_search <- function(element, sequence, i = 1) {
  if (i > length(sequence)) FALSE
  else if (sequence[i] == element) TRUE
  else r_lin_search(element, sequence, i + 1)
}
```

使用 `for` 循环的版本可能如下所示：

```
l_lin_search <- function(element, sequence) {
  for (e in sequence) {
    if (e == element) return(TRUE)
  }
  return(FALSE)
}
```

我们可以使用 `microbenchmark` 包中的 `microbenchmark` 函数来比较两者。如果我们搜索一个不包含在序列中的元素，我们将不得不搜索整个序列，因此我们可以使用这种最坏情况来进行性能衡量：

# 第 3 章：纯函数式编程

```r
x <- 1:1000

microbenchmark::microbenchmark(
  r_lin_search(-1, x),
  l_lin_search(-1, x)
)
```

## Unit: microseconds

### expr
- `r_lin_search(-1, x )`
- `l_lin_search(-1, x )`

### 输出数据

| 指标 | `r_lin_search(-1, x )` | `l_lin_search(-1, x )` |
| :--- | :--- | :--- |
| min | 834.297 | 20.117 |
| lq | 927.9130 | 20.6345 |
| mean | 1121.50154 | 47.32387 |
| median | 986.011 | - |
| uq | 1094.573 | - |
| max | 5361.536 | - |
| neval | 100 | - |
| cld | b | - |

# 递归与循环

递归函数的运行速度比使用循环的函数慢几个数量级。如果有人告诉你 R 语言中循环很慢，请记住这一点；它们可能确实慢，但递归函数更慢。

情况还不止于此。R 语言对函数递归调用的深度有限制，如果我们在一个包含约一千个以上元素的序列中查找，就会达到这个限制，届时 R 将终止调用并返回一个错误。

这并不意味着阅读本章完全是浪费时间。在构建函数时，从递归的角度思考是非常有用的。分治策略经常被用来解决算法问题是有其原因的。你只需要在设计好函数后，将递归解法转化为循环即可。

对于线性搜索这类函数，我们一开始就不会把它编程为递归函数。`for` 循环版本编写起来简单得多，效率也高得多。而其他问题用递归算法来解决则容易得多，并且实现时，最好也先以递归函数的角度进行思考。二分查找就是这类问题的一个例子。它本质上是递归的，因为我们通过在一个更短的字符串上进行另一次搜索来解决本次搜索。它也不太可能达到允许的递归深度限制，因为它进行递归调用的次数仅与输入长度的对数相关，不过这已经是另一个问题了。

递归的二分查找代码如下所示：

```
r_binary_search <- function(element, x, first = 1, last = length(x)) {
  if (last < first) return(FALSE) # empty sequence
  middle <- (last - first) %/% 2 + first
  if (element == x[middle]) TRUE
  else if (element < x[middle]) {
    r_binary_search(element, x, first, middle - 1)
  } else {
    r_binary_search(element, x, middle + 1, last)
  }
}
```

这是一个尾递归函数，我们可以利用这一点将其转化为一个使用循环而非递归调用的版本。要将一个尾递归函数转化为循环函数，只需将函数体放入一个 `repeat` 循环中。除非你显式退出，否则 `repeat` 循环会永远运行下去，但可以利用递归函数中的基本情况测试，通过显式的 `return` 调用来退出循环。在通常需要进行递归调用的地方，你只需更新局部参数。你只需要将它们更新为如果进行递归调用时它们应有的值。因此，对于之前的第一个递归调用 `r_binary_search(element, x, first, middle - 1)`，你需要更新 `element <- element`，`x <- x`，`first <- first`，以及 `last <- middle - 1`。当然，对于那些应该保持当前值的变量，无需更新，所以这个更新只是 `last <- middle - 1`。同样，对于第二个递归调用，`element`、`x` 和 `last` 保持当前值，但 `first` 更新为 `middle + 1`。重写后的结果如下：

```
l_binary_search <- function(element, x, first = 1, last = length(x)) {
  repeat {
    # Base case with no match
    if (last < first) return(FALSE) # empty sequence
    middle <- (last - first) %/% 2 + first
    
    # Base case with a match
    if (element == x[middle]) return(TRUE)
    if (element < x[middle]) {
      last <- middle - 1 # "recursive" call with last = middle - 1
    } else {
      first <- middle + 1 # "recursive" call with first = middle + 1
    }
  }
}
```

这种转换总是遵循这个简单的模式，这也是为什么许多编程语言会自动为你完成转换的原因。

一旦你这样用 `repeat` 循环重写了函数，通常可以进一步简化，得到一个当达到基本情况时就能跳出的 `while` 循环，但这就不那么自动化了（尽管通常也不难）：

```
l_binary_search <- function(element, x, first = 1, last = length(x)) {
  while (last >= first) {
    middle <- (last - first) %/% 2 + first
    
    # Base case with a match
    if (element == x[middle]) return(TRUE)
    if (element < x[middle]) {
      last <- middle - 1 # "recursive" call with last = middle - 1
    } else {
      first <- middle + 1 # "recursive" call with first = middle + 1
    }
  }
  return(FALSE)
}
```

如果你的函数**不是**尾递归的，那么将其转换为使用循环的版本会麻烦得多。你本质上需要自己模拟函数调用栈。这涉及更多的编程工作，但这不属于函数式编程的范畴，因此超出了本书的讨论范围。不过不用担心，使用本书后面会介绍的“续延”这一主题，你通常可以将函数转化为尾递归函数，然后使用一种称为“蹦床”的技巧来用循环替代递归。



# 作用域与闭包

`作用域`或`环境`是与函数和表达式相关联的东西，它告诉它们每个变量引用的是什么值。它用于确定表达式在哪个环境中被求值。同一个变量名可以在程序中的多个地方使用，在不同的位置指代不同的东西，但表达式的作用域会精确地告诉R如何将名称映射到值。在R中，既有隐式环境（我倾向于称之为作用域），也有显式环境。

前者是你大多数日常编程中会使用的，而后者用于更高级的技术，主要是在如何向用户提供接口方面增加灵活性。然而，R将名称映射到值的方式在这两种情况下是相同的，理解其工作原理可以让你更容易编写函数和理解高级包。

但让我们从一个简单的例子开始。如果你编写一个表达式，比如`x + y`，你无法在不了解`x`和`y`指代什么的情况下对其进行求值。R也是如此。如果你已经告诉了R这两个变量指代什么：
```r
x <- 2
y <- 3
x + y
## [1] 5
```
你会得到预期结果。R知道`x`指代2（因为你刚刚将2赋值给了它），也知道`y`指代3。

你可以认为R有一张在名称和值之间进行映射的表；这离真相并不远。如果我们在全局（最外层）级别编写上述代码，这个表就是“全局环境”或“全局作用域”，我们可以使用`globalenv()`函数获取这个表：
```r
genv <- globalenv()
```
并且，从这个表中，我们可以获取我们赋值给变量`x`和`y`的值：
```r
genv$x
## [1] 2

genv$y
## [1] 3
```
当我们对`x + y`求值时，R会自动查找`x`和`y`的值，并且由于我们是在全局环境中编写这个表达式的，所以它会在全局环境中查找。

我们可以用一种更复杂的方式来演示，使环境和求值过程变得明确。你可以使用`quote()`函数创建一个没有关联值的表达式：
```r
expr <- quote(x + y)
```
现在，`expr`指向表达式`x + y`，但这个表达式并未被求值。它只是加法运算的原始语法。要对它求值，我们需要使用`eval()`函数：
```r
eval(expr)
```

