# 第 9 章 其他数据管理包

要将列重新排序，使长度和宽度分别配对，只需使用`select()`函数并为你数据对象重新赋值。请务必记住，我们已经将`SepalLength`重命名，中间不再有句点！

```
diris <- diris %>% select(
  SepalLength,
  Petal.Length,
  Sepal.Width,
  Petal.Width,
  Species)
```

```
diris
```

```
## # A tibble: 150 x 5
##    SepalLength Petal.Length Sepal.Width Petal.Width Species
##         <dbl>        <dbl>       <dbl>       <dbl> <chr>
## 1         4.3          1.1         3           0.1 setosa
## 2         4.4          1.3         3.2         0.2 setosa
## 3         4.4          1.3         3           0.2 setosa
## 4         4.4          1.4         2.9         0.2 setosa
## 5         4.5          1.3         2.3         0.3 setosa
## # ... with 145 more rows
```

早些时候，你看到numeric选择调用可以重新排序列，因此我们用最后一个重新排序的示例来结束本节。在这里，我们使用一个变量来保存数字位置，尽管我们也可以使用字符名称代替数字。然后我们使用`select()`函数将我们的列重新组织回其原始顺序：`colVar3 <- c(1, 3, 2, 4, 5)`

```
diris %>% select(all_of(colVar3))
```

```
## # A tibble: 150 x 5
##    SepalLength Sepal.Width Petal.Length Petal.Width Species
##         <dbl>       <dbl>        <dbl>       <dbl> <chr>
## 1         4.3         3            1.1         0.1 setosa
## 2         4.4         3.2          1.3         0.2 setosa
## 3         4.4         3            1.3         0.2 setosa
## 4         4.4         2.9          1.4         0.2 setosa
## 5         4.5         2.3          1.3         0.3 setosa
## # ... with 145 more rows
```