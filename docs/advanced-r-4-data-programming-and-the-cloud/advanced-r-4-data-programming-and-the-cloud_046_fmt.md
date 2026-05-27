# 第九章 其他数据管理包

另外，如果我们希望排除具有某些特征的行，可以通过“不在”或 `%nin%` 运算符实现。这个运算符同样可用于移除异常值或错误数据点：

```r
diris %>% filter(Sepal.Length %nin% interest)
```

## # 一个 tibble: 146 x 5
## Sepal.Length Sepal.Width Petal.Length Petal.Width Species
## <dbl> <dbl> <dbl> <dbl> <chr>
## 1 4.5 2.3 1.3 0.3 setosa
## 2 4.6 3.6 1 0.2 setosa
## 3 4.6 3.4 1.4 0.3 setosa
## 4 4.6 3.2 1.4 0.2 setosa
## 5 4.6 3.1 1.5 0.2 setosa
## # ... 还有 141 行

我们展示的最后一个筛选示例使用了多个参数。使用多个参数并非必需，但可以作为一种将代码分解为人类易读块的有用方式。

多个参数用逗号分隔，在逻辑上等同于 `&`：

```r
diris %>% filter(Sepal.Length == 4.3 | Species != "setosa", Petal.Width < 1.5)
```

## # 一个 tibble: 37 x 5
## Sepal.Length Sepal.Width Petal.Length Petal.Width Species
## <dbl> <dbl> <dbl> <dbl> <chr>
## 1 4.3 3 1.1 0.1 setosa
## 2 4.9 2.4 3.3 1 versicolor
## 3 5 2.3 3.3 1 versicolor
## 4 5 2 3.5 1 versicolor
## 5 5.1 2.5 3 1.1 versicolor
## # ... 还有 32 行

到目前为止，我们从所有列中选择了行。如果我们只想选择特定的列，`select()` 函数将数据库作为其第一个形式参数，并在其余形式参数中提供列信息。对于表格结构中的命名列，选择一个或多个列只需列出列名即可。也可以按位置选择。

对比这些按名称和按位置的列参数，并注意两者都可以按任何需要的顺序进行列选择：

```r
diris %>% select(Sepal.Length, Sepal.Width)
```

## # 一个 tibble: 150 x 2
## Sepal.Length Sepal.Width
## <dbl> <dbl>
## 1 4.3 3
## 2 4.4 3.2
## 3 4.4 3
## 4 4.4 2.9
## 5 4.5 2.3
## # ... 还有 145 行

```r
diris %>% select(1, 5, 2)
```

## # 一个 tibble: 150 x 3
## Sepal.Length Species Sepal.Width
## <dbl> <chr> <dbl>
## 1 4.3 setosa 3
## 2 4.4 setosa 3.2
## 3 4.4 setosa 3
## 4 4.4 setosa 2.9
## 5 4.5 setosa 2.3
## # ... 还有 145 行

到目前为止，都是直接指定特定的列。如果需要根据程序或用户需求动态变化列，该怎么办？这种情况下，使用字符串引用是理想的，因为字符串很容易转换为变量。多个作为字符串的变量名可以作为单独的参数传递：

```r
diris %>% select("Sepal.Length", "Sepal.Width")
```

## # 一个 tibble: 150 x 2
## Sepal.Length Sepal.Width
## <dbl> <dbl>
## 1 4.3 3
## 2 4.4 3.2
## 3 4.4 3
## 4 4.4 2.9
## 5 4.5 2.3
## # ... 还有 145 行

这些可以放入一个变量中，例如 `colVar1`，该变量也可以包含多个列名：

```r
colVar1 <- c("Sepal.Length", "Sepal.Width")
diris %>% select(all_of(colVar1))
```

## # 一个 tibble: 150 x 2
## Sepal.Length Sepal.Width
## <dbl> <dbl>
## 1 4.3 3
## 2 4.4 3.2
## 3 4.4 3
## 4 4.4 2.9
## 5 4.5 2.3
## # ... 还有 145 行

此外，`select()` 有一些辅助函数，允许进行更复杂的选择。其中一个辅助函数的例子是 `any_of()`，它尝试在 `colVar2` 集合和 `diris` 集合的列名之间进行尽可能多的匹配。这些辅助函数的名称最近变化不小，因此即使你熟悉辅助函数，简要回顾一下也会有所帮助。注意 `colVar2` 的最后一个条目并非真实列名。尽管如此，由于使用了该辅助函数，仍然得到了正确的结果：

```r
colVar2 <- c("Sepal.Length", "Sepal.Width", "错误名称!")
diris %>% select(any_of(colVar2))
```

## # 一个 tibble: 150 x 2
## Sepal.Length Sepal.Width
## <dbl> <dbl>
## 1 4.3 3
## 2 4.4 3.2
## 3 4.4 3
## 4 4.4 2.9
## 5 4.5 2.3
## # ... 还有 145 行