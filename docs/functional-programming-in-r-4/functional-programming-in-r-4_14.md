# 第 3 章：纯函数式编程——运行时考量

现在来谈坏消息。我在本章中向你展示的所有使用递归而不是循环来编写纯函数式程序的技术，在 R 语言中相对较慢。

从软件设计的角度来看，你希望编写纯函数，但依赖递归而非循环会带来运行时成本。我们可以将递归线性搜索函数与使用 for 循环的版本进行比较，看看会带来多少额外开销。

递归函数如下所示：

```
r_lin_search <- function(element, sequence, i = 1) {
  if (i > length(sequence)) FALSE
  else if (sequence[i] == element) TRUE
  else r_lin_search(element, sequence, i + 1)
}
```

使用 for 循环的版本可能如下所示：

```
l_lin_search <- function(element, sequence) {
  for (e in sequence) {
    if (e == element) return(TRUE)
  }
  return(FALSE)
}
```

我们可以使用 `microbenchmark` 包中的 `microbenchmark` 函数来比较两者。如果我们搜索一个不包含在序列中的元素，我们将不得不搜索整个序列，因此我们可以使用这种最坏情况来进行性能衡量：

