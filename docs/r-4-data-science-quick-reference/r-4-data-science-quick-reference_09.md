# 数据分组

我使用了`distinct()`来获取`mean_x`和`mean_y`组合的唯一值，这能很好地说明结果。这里只有一行值，这是因为我们计算的是整个数据集的均值，而不是针对数据的不同分组。

相反，如果在向数据添加变量之前进行分组，那么汇总就是按组计算的。

## 按列分组后计算

```r
df |> group_by(`x_category`) |>                       # 仅按 `x_category` 分组
    mutate(mean_x = mean(x), mean_y = mean(y)) |> # 计算每个 `x` 组的均值和
    distinct(mean_x, mean_y)                      # 获取唯一值以便打印

## # A tibble: 3 × 3
## # Groups:   x_category [3]
##   x_category mean_x  mean_y
##   <chr>       <dbl>   <dbl>
## 1 medium      0.123  0.0631
## 2 large       2.08   1.33
## 3 small      -2.55  -1.12
```

这里，针对不同的`x_category`因子有不同的值，表明我们计算了数据中每个不同`x_category`的均值。

## 分组前计算全局汇总

如果你在分组之前计算全局汇总，可以将整个数据范围的汇总与分组汇总结合起来。

```r
df |> mutate(mean_y = mean(y)) |> # 获取所有数据点的 `y` 均值
    group_by(`x_category`)     |> # 然后分组，以便后续操作按组进行
    mutate(mean_x = mean(x)) |> # 获取每个组的 `x` 均值（不是全局的）
    distinct(mean_x, mean_y)    # 获取唯一值以便打印结果

## # A tibble: 3 × 3
## # Groups:    x_category [3]
##   x_category mean_y  mean_x
##   <chr>       <dbl>   <dbl>
## 1 medium     0.0520   0.123
## 2 large      0.0520   2.08
## 3 small      0.0520  -2.55
```

注意，输出行中的`mean_y`值是相同的；这是因为我们计算的是全局均值，而不是每个组的均值。

## 基于多个变量进行分组

如果你需要根据不同分组计算汇总，例如，分别计算每个`x_category`的`x`均值以及每个`y_category`的`y`均值，那么你可以先调用`group_by(x_category)`来汇总`x`，接着调用`group_by(y_category)`来更改分组以便汇总`y`。

```r
df |> group_by(`x_category`) |>
    mutate(mean_x = mean(x)) |>
    group_by(`y_category`) |>
    mutate(mean_y = mean(y)) |>
    distinct(
        x_category, mean_x,
        y_category, mean_y
    )

## # A tibble: 5 × 4
## # Groups: y_category [3]
##   x_category y_category mean_x mean_y
##   <chr>       <chr>       <dbl>  <dbl>
## 1 medium   medium    0.123      0.127
## 2 medium   small     0.123     -2.38
## 3 large    medium    2.08       0.127
## 4 medium   large     0.123      2.69
## 5 small    medium   -2.55       0.127
```
