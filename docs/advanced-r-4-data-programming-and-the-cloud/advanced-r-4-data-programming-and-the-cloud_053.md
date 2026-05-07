# 第 9 章 其他数据管理包

## 9.4 数据计算与创建变量

既然你已经对如何在 tidyverse 中管理原始数据有了很好的了解，我们将重点关注如何从旧数据创建新数据。为简单起见，我们从原始 `iris` 数据集重新创建我们的 `diris` 数据集。然后我们使用 `mutate()` 函数创建或替换列。该函数将常规的第一个参数，而后面的形式参数则表达如何创建新列。无论是创建一个还是多个，只需创建表达式让 tidyverse 知道要追加到 tibble 中什么内容。请注意，在这里我们没有将这种变异重新赋值回 `diris`。因此，这个变异不会被保存下来。在探索性分析中，从控制台运行此类变异以便理解数据特征是很方便的：

```
diris <- as_tibble(iris)
diris %>% mutate(V0 = 0, X1 = 1L, X2 = 2L)
```

```
## # A tibble: 150 x 8
##    Sepal.Length Sepal.Width Petal.Length Petal.Width Species    V0
##           <dbl>       <dbl>        <dbl>       <dbl> <chr>   <dbl>
## 1          5.1         3.5          1.4         0.2 setosa      0
## 2          4.9         3            1.4         0.2 setosa      0
## 3          4.7         3.2          1.3         0.2 setosa      0
## 4          4.6         3.1          1.5         0.2 setosa      0
## 5          5           3.6          1.4         0.2 setosa      0
## # ... with 145 more rows, and 2 more variables: X1 <int>, X2 <int>
```

当然，新数据可能并不全是创建全新的列。相反，我们也可以基于现有信息创建新的计算列。也许我们想要一个名为 `V` 的新列，它是 `Petal.Length` 和 `Petal.Width` 的乘积：

```
diris <- mutate(diris, V = Petal.Length * Petal.Width)
diris
```

```
## # A tibble: 150 x 6
##    Sepal.Length Sepal.Width Petal.Length Petal.Width Species     V
##           <dbl>       <dbl>        <dbl>       <dbl> <chr>   <dbl>
## 1          5.1         3.5          1.4         0.2 setosa  0.280
## 2          4.9         3            1.4         0.2 setosa  0.280
## 3          4.7         3.2          1.3         0.2 setosa  0.26
```

