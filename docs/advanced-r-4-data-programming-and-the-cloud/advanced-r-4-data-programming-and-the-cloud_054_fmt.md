# 第 9 章 其他数据管理包

```
## 4          4.6         3.1          1.5         0.2 setosa  0.3
## 5          5           3.6          1.4         0.2 setosa  0.280
## # ... with 145 more rows
```

更复杂的计算仅在满足某些条件时执行。在这种情况下，`if_else()` 需要三个参数。第一个是要测试 if/else 语句的逻辑条件。第二个和第三个形式参数指示在 `if` 或 `else` 情况下要执行的操作。此外，`mutate()` 不仅允许我们创建包含数据的列，还可以更改现有列的数据。因此，如果我们第一次创建列 `V2` 时，仅对 setosa 物种计算花瓣宽度和长度之间的乘法关系还不够，我们可以将其扩展到对 virginica 物种进行平方根运算。为了确保我们看到每个物种的一个样本，我们使用 `slice()` 函数查看三个特定的行：

```
diris <- diris %>% mutate(V2 = if_else(Species == "setosa", Petal.Length * Petal.Width, NA_real_))
diris <- diris %>% mutate(V2 = if_else(Species == "virginica", sqrt(Petal.Length * Petal.Width), V2))
diris %>% slice(c(1, 51, 101))
```

```
## # A tibble: 3 x 7
##   Sepal.Length Sepal.Width Petal.Length Petal.Width Species     V    V2
##          <dbl>       <dbl>        <dbl>       <dbl> <chr>   <dbl> <dbl>
## 1          5.1         3.5          1.4         0.2 setosa  0.280 0.280
## 2          7           3.2          4.7         1.4 versic˜ 6.58  2.56
## 3          6.3         3.3          6           2.5 virgin˜15     3.87
```

并非必须创建新列；也可以直接使用 `summarise()` 函数计算表格上的信息。第一个形式参数一如既往地告诉函数使用哪个数据（尽管由于管道符的使用而隐藏了），其余参数用于保存各个计算结果。请注意，可以编码一个或多个计算。在这里，我们演示了算术均值 `mean()` 和标准差 `sd()` 的两种计算：

```
## summarise 中的计算允许进行多项操作
diris %>% summarise(M = mean(Sepal.Width), SD = sd(Sepal.Width))
```