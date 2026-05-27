# ... 还有 30 行

本部分以最后一次调用 `distinct()` 结束，该调用在第二和第三个形式参数中显示了多个列。在这里，只要 `Sepal.Length` 和 `Sepal.Width` 没有同时重复，一行就是唯一的。请特别注意第 `2` 行到第 `4` 行，它们具有相同的 `Sepal.Length`，但这些行没有重复，因为它们的 `Sepal.Width` 不同：

```
distinct(diris, Sepal.Length, Sepal.Width)
```