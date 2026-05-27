# 第九章 其他数据管理包

到目前为止，我们的所有工作都返回另一个 tibble 作为输出。将原始数据提取为向量有几种技术。如果我们知道包含所需数据的列名，可以直接输入名称。类似地，如果我们的列存储在变量中，则可以调用该变量（确保一次只请求一列！）。无论哪种方法，控制台都会打印相同的数据。注意这些数据是向量，而非 tibble 结构。同样注意，我们将函数包裹在`head()`函数中以节省空间：

```R
head(diris[["Sepal.Length"]]) # 如果你有 R 变量，很简单
```

```
## [1] 4.3 4.4 4.4 4.4 4.5 4.6
```

```R
head(diris[[colVar1[1]]]) # 如果你知道位置，很简单
```

```
## [1] 4.3 4.4 4.4 4.4 4.5 4.6
```

```R
head(diris$Sepal.Length) # 输入简单
```

```
## [1] 4.3 4.4 4.4 4.4 4.5 4.6
```

排除一列或多列使用取反操作。同样，要排除的列可以通过位置或直接命名来引用：

```R
diris %>% select(-1)
```

```
## # A tibble: 150 × 4
##   Sepal.Width Petal.Length Petal.Width Species
##         <dbl>        <dbl>       <dbl> <chr>
## 1         3            1.1         0.1 setosa
## 2         3.2          1.3         0.2 setosa
## 3         3            1.3         0.2 setosa
## 4         2.9          1.4         0.2 setosa
## 5         2.3          1.3         0.3 setosa
## # … with 145 more `行`
```

```R
diris %>% select(-Sepal.Length, -Petal.Width)
```

```
## # A tibble: 150 × 3
##   Sepal.Width Petal.Length Species
##         <dbl>        <dbl> <chr>
## 1         3            1.1 setosa
## 2         3.2          1.3 setosa
## 3         3            1.3 setosa
## 4         2.9          1.4 setosa
## 5         2.3          1.3 setosa
## # … with 145 more `行`
```

当需要排除变量时，我们设置一个变量来保存该向量，然后直接对该变量取反即可。或者，我们可以结合辅助函数使用取反：

```R
diris %>% select(-colVar1)
```

```
## Warning: Using an external vector in selections was deprecated in tidyselect 1.1.0.
## ℹ Please use `all_of(colVar1)` instead of `colVar1` to silence this message.
## ℹ See <https://tidyselect.r-lib.org/reference/faq-external-vector.html>.
## This message is displayed once per session.
## # A tibble: 150 × 3
##   Petal.Length Petal.Width Species
##          <dbl>       <dbl> <chr>
## 1          1.1         0.1 setosa
## 2          1.3         0.2 setosa
## 3          1.3         0.2 setosa
## 4          1.4         0.2 setosa
## 5          1.3         0.3 setosa
## # … with 145 more `行`
```

```R
diris %>% select(-any_of(colVar2))
```

```
## # A tibble: 150 × 3
##   Petal.Length Petal.Width Species
##          <dbl>       <dbl> <chr>
## 1          1.1         0.1 setosa
## 2          1.3         0.2 setosa
## 3          1.3         0.2 setosa
## 4          1.4         0.2 setosa
## 5          1.3         0.3 setosa
## # … with 145 more `行`
```