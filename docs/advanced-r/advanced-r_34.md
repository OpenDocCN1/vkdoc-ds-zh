# A tibble: 5 x 5
Sepal.Length Sepal.Width Petal.Length Petal.Width   Species

1          7.7         3.8          6.7         2.2 virginica
2          7.7         3.0          6.1         2.3 virginica
3          7.7         2.8          6.7         2.0 virginica
4          7.7         2.6          6.9         2.3 virginica
5          7.9         3.8          6.4         2.0 virginica
```

除了直接选择行，我们也可以对它们设置逻辑条件。再次强调，这些类型的参数属于函数调用的第二个形式参数。但在此情况下，我们使用 `filter()` 而非 `slice()`。`filter()` 函数使用逻辑索引。通过编写一个逻辑测试，可以很容易地选择 `Species` 是（或不是）`setosa` 的所有行：

```
filter(diris, Species == "setosa")

