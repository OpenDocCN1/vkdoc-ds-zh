# 23.087 25.269 2341.506 100 a

递归函数的运行速度比使用循环的函数慢几个数量级。如果有人告诉你 R 语言中循环很慢，请记住这一点；它们可能确实慢，但递归函数更慢。

情况还不止于此。R 语言对函数递归调用的深度有限制，如果我们在一个包含约一千个以上元素的序列中查找，就会达到这个限制，届时 R 将终止调用并返回一个错误。

这并不意味着阅读本章完全是浪费时间。在构建函数时，从递归的角度思考是非常有用的。分治策略经常被用来解决算法问题是有其原因的。你只需要在设计好函数后，将递归解法转化为循环即可。

对于线性搜索这类函数，我们一开始就不会把它编程为递归函数。`for`循环版本编写起来简单得多，效率也高得多。而其他问题用递归算法来解决则容易得多，并且实现时，最好也先以递归函数的角度进行思考。二分查找就是这类问题的一个例子。它本质上是递归的，因为我们通过在一个更短的字符串上进行另一次搜索来解决本次搜索。它也不太可能达到允许的递归深度限制，因为它进行递归调用的次数仅与输入长度的对数相关，不过这已经是另一个问题了。

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

