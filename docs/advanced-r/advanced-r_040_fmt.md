# A tibble: 5 x 5

| Sepal.Length | Sepal.Width | Petal.Length | Petal.Width | Species |
|------------|------------|-------------|-------------|---------|
| 7.7        | 3.8        | 6.7         | 2.2         | virginica |
| 7.7        | 3.0        | 6.1         | 2.3         | virginica |
| 7.7        | 2.8        | 6.7         | 2.0         | virginica |
| 7.7        | 2.6        | 6.9         | 2.3         | virginica |
| 7.9        | 3.8        | 6.4         | 2.0         | virginica |

我们也可以不直接选择行，而是对它们设置逻辑条件。同样，这类参数属于函数调用的第二个形式参数。但是，在这种情况下，我们使用`filter()`而不是`slice()`。`filter()`函数使用逻辑索引。通过编写逻辑测试，很容易选择`Species`等于（或不等）`setosa`的所有行：

```r
filter(diris, Species == "setosa")
```