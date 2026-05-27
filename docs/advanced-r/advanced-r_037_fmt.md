# ... with 30 more rows

本节最后再调用一次 `distinct()`，展示在第二个和第三个形式参数中调用多个列。在这里，当 `Sepal.Length` 和 `Sepal.Width` 都不重复时，一行才是唯一的。特别注意第 `2` 到第 `4` 行，它们有相同的 `Sepal.Length`，但这些行并没有重复，因为它们的 `Sepal.Widths` 是不同的：

```
distinct(diris, Sepal.Length, Sepal.Width)
```