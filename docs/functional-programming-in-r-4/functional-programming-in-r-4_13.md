# 第 3 章：纯函数式编程

```
factorial <- function(n, acc = 1) {
  if (n == 1) acc
  else factorial(n - 1, acc * n)
}
```

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

