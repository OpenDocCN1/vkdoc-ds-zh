# ... with 144 more rows
```

类似地，回想一下，如果我们只考虑萼片长度，只有 `35` 个唯一的行。因此，使用 `distinct()` 的第二个形式参数来保存 `Sepal.Length` 列，我们的 `tibble` 中只有 `35` 行。我们在这里展示这些行的前五行：

```
distinct(diris, Sepal.Length)

