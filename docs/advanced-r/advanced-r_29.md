# ... 还有 144 行
```

类似地，回想一下，如果我们只考虑萼片长度，则只有 `35` 个唯一行。因此，使用 `distinct()` 的第二个形式参数来包含 `Sepal.Length` 列，会在我们的 `tibble` 中仅产生 `35` 行。我们在此显示前五行：

```
distinct(diris, Sepal.Length)

