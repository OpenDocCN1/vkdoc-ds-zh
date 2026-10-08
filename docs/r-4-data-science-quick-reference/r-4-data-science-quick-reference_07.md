# 列操作：`select()` 与 `filter()`

## 选择列

`select()`函数用于提取数据框的列子集。以下是一些常见的使用场景。

当然，第 4 章中描述的所有其他函数也都适用于`select()`。

```r
iris_df |>
  select(starts_with("Petal")) |>
  print(n = 3)
```

```
## # A tibble: 150 × 2
##   Petal.Length Petal.Width
##          <dbl>       <dbl>
## 1          1.4         0.2
## 2          1.4         0.2
## 3          1.3         0.2
## # … with 147 more rows
```

```r
iris_df |>
  select(-starts_with("Petal")) |>
  print(n = 3)
```

```
## # A tibble: 150 × 3
##   Sepal.Length Sepal.Width Species
##          <dbl>       <dbl> <chr>  
## 1          5.1         3.5 setosa 
## 2          4.9         3    setosa 
## 3          4.7         3.2 setosa 
## # … with 147 more rows
```

```r
iris_df |>
  select(starts_with("Petal"), Species) |>
  print(n = 3)
```

```
## # A tibble: 150 × 3
##   Petal.Length Petal.Width Species
##          <dbl>       <dbl> <chr>  
## 1          1.4         0.2 setosa 
## 2          1.4         0.2 setosa 
## 3          1.3         0.2 setosa 
## # … with 147 more rows
```

```r
iris_df |>
  select(starts_with("PETAL", ignore.case = TRUE)) |>
  print(n = 3)
```

```
## # A tibble: 150 × 2
##   Petal.Length Petal.Width
##          <dbl>       <dbl>
## 1          1.4         0.2
## 2          1.4         0.2
## 3          1.3         0.2
## # … with 147 more rows
```

```r
iris_df |>
  select(starts_with("S")) |>
  print(n = 3)
```

```
## # A tibble: 150 × 3
##   Sepal.Length Sepal.Width Species
##          <dbl>       <dbl> <chr>  
## 1          5.1         3.5 setosa 
## 2          4.9         3    setosa 
## 3          4.7         3.2 setosa 
## # … with 147 more rows
```

```r
iris_df |>
  select(ends_with("Length")) |>
  print(n = 3)
```

```
## # A tibble: 150 × 2
##   Sepal.Length Petal.Length
##          <dbl>       <dbl>
## 1          5.1         1.4
## 2          4.9         1.4
## 3          4.7         1.3
## # … with 147 more rows
```

```r
iris_df |>
  select(contains("ng")) |>
  print(n = 3)
```

```
## # A tibble: 150 × 2
##   Sepal.Length Petal.Length
##          <dbl>       <dbl>
## 1          5.1         1.4
## 2          4.9         1.4
## 3          4.7         1.3
## # … with 147 more rows
```

你还可以使用`select()`来重命名列。如果你使用一个命名参数，你选择的列将获得你为函数参数使用的名称。

```r
iris_df |>
  select(sepal_length = Sepal.Length,
         sepal_width = Sepal.Width) |>
  print(n = 3)
```

```
## # A tibble: 150 × 2
##   sepal_length sepal_width
##          <dbl>       <dbl>
## 1          5.1         3.5
## 2          4.9         3
## 3          4.7         3.2
## # … with 147 more rows
```

由于`select()`会移除你没有选中的列，它可能并不总是满足你需求的最佳选择。一个类似于`select()`但不会丢失列的函数是`rename()`。使用这个函数，你可以重命名列，同时保留其余列。

```r
iris_df |>
  rename(sepal_length = Sepal.Length,
         sepal_width = Sepal.Width) |>
  print(n = 3)
```

```
## # A tibble: 150 × 5
##   sepal_length sepal_width Petal.Length Petal.Width Species
##          <dbl>       <dbl>        <dbl>       <dbl> <chr>  
## 1          5.1         3.5          1.4         0.2 setosa 
## 2          4.9         3            1.4         0.2 setosa 
## 3          4.7         3.2          1.3         0.2 setosa 
## # … with 147 more rows, and 2 more variables:
## # Petal.Width <dbl>, Species <chr>
```

## 筛选行

`select()`函数用于提取列的子集，而`filter()`函数则对行进行同样的操作。

`iris_df`数据集包含三个不同的物种。我们可以使用`distinct()`函数来查看这一点。该函数也来自`dplyr`包，它会从选定的列中返回所有唯一的行。

```r
iris_df |> distinct(Species)
```

```
## # A tibble: 3 × 1
##   Species
##   <chr>  
## 1 setosa 
## 2 versicolor
## 3 virginica
```

我们可以使用`filter()`来选择物种为“setosa”的行：

```r
iris_df |>
  filter(Species == "setosa") |>
  print(n = 3)
```

```
## # A tibble: 50 × 5
##   Sepal.Length Sepal.Width Petal.Length Petal.Width Species
##          <dbl>       <dbl>        <dbl>       <dbl> <chr>  
## 1          5.1         3.5          1.4         0.2 setosa 
## 2          4.9         3            1.4         0.2 setosa 
## 3          4.7         3.2          1.3         0.2 setosa 
## # … with 47 more rows, and 2 more variables:
## # Petal.Width <dbl>, Species <chr>
```

我们可以在一个管道中组合使用`filter()`和`select()`，同时获取列和行的子集。

```r
iris_df |>
  filter(Species == "setosa") |>
  select(ends_with("Length"), Species) |>
  print(n = 3)
```

```
## # A tibble: 50 × 3
##   Sepal.Length Petal.Length Species
##          <dbl>       <dbl> <chr>  
## 1          5.1         1.4 setosa 
## 2          4.9         1.4 setosa 
## 3          4.7         1.3 setosa 
## # … with 47 more rows
```

通常，我们可以在管道中按需串联任意多个`dplyr`（或`purrr`）函数。

```r
filter(Species != "setosa") |>

distinct(Species) |>

print(n = 3)

## # A tibble: 2 × 1

##   Species

##   

## 1 versicolor

## 2 virginica
```

我们可以使用多个列来进行筛选：

```r
iris_df |>

filter(Sepal.Length > 5, Petal.Width 

print(n = 3)

## # A tibble: 15 × 5

##   Sepal.Length Sepal.Width Petal.Length

##            

## 1          5.1   3.5   1.4

## 2          5.4   3.7   1.5

## 3          5.8   4     1.2

## # . . . with 12 more rows, and 2 more variables:

## # Petal.Width , Species 
```

我们也可以使用函数作为谓词，例如，使用`between()`函数来选择给定范围内的数值。

```r
iris_df |>

filter(between(Sepal.Width, 2, 2.5)) |>

print(n = 3)

## # A tibble: 19 × 5

##   Sepal.Length Sepal.Width Petal.Length

##                         

## 1          4.5         2.3          1.3

## 2          5.5         2.3          4

## 3          4.9         2.4          3.3

## # . . . with 16 more rows, and 2 more variables:

## # Petal.Width , Species 
```

我们不能使用与`select()`配合使用的`starts_with()`、`ends_with()`等函数。然而，这并不意味着我们不能使用字符串模式来筛选行。我们只是需要不同的函数。例如，我们可以使用`stringr`包中的`str_starts()`。我们将在第 9 章中回到`stringr`。

```r
iris_df |>

filter(str_starts(Species, "v")) |>

print(n = 3)

## # A tibble: 100 × 5

##   Sepal.Length Sepal.Width Petal.Length

##                         

## 1          7           3.2          4.7

## 2          6.4         3.2          4.5

## 3          6.9         3.1          4.9

## # . . . with 97 more rows, and 2 more variables:

## # Petal.Width , Species 

iris_df |>

filter(str_ends(Species, "r")) |>

print(n = 3)

## # A tibble: 50 × 5

##   Sepal.Length Sepal.Width Petal.Length

##                         

## 1          7           3.2          4.7

## 2          6.4         3.2          4.5

## 3          6.9         3.1          4.9

## # . . . with 47 more rows, and 2 more variables:

## # Petal.Width , Species 
```

`filter()`通过在单个列上应用谓词来选择行，而其他筛选变体则会在多个列上使用谓词。

`filter_all()`函数将对所有列应用谓词。你必须提供一个表达式，其中使用`.`（点）代表表格单元格的值，并将该表达式包裹在`any_var()`或`all_var()`两个函数之一中。如果你使用`any_var()`，则只要有一个列满足谓词，该行就会被包含；如果你使用`all_var()`，则所有列都必须满足谓词。

我们可以要求`iris_df`数据中的任何值都必须大于五：

```r
iris_df |>

select(-Species) |>

filter_all(any_vars(. > 5)) |>

print(n = 3)

## # A tibble: 118 × 4

##   Sepal.Length Sepal.Width Petal.Length

##                         

## 1          5.1         3.5          1.4

## 2          5.4         3.9          1.7

## 3          5.4         3.7          1.5

## # . . . with 115 more rows, and 1 more variable:

## # Petal.Width 
```

如果`.`是字符串，则表达式`. > 5`没有意义，所以我在筛选之前必须删除`Species`列。我们很少会接受丢失一个信息丰富的变量。如果你想保留某个列，但不将谓词应用于其中的值，你可以使用`filter_at()`函数。

```r
iris_df |>

filter_at(vars(-Species), any_vars(. > 5)) |>

print(n = 3)

## # A tibble: 118 × 5

##   Sepal.Length Sepal.Width Petal.Length

##                         

## 1          5.1         3.5          1.4

## 2          5.4         3.9          1.7

## 3          5.4         3.7          1.5

## # . . . with 115 more rows, and 2 more variables:

## # Petal.Width , Species 
```

你可以给`filter_at()`传递一个字符串向量或一个变量向量（未加引号的列名）。因为我需要排除`Species`以选择所有其他列，所以这里我使用了`vars()`。在其他情况下，你可以使用任一选项。

```r
iris_df |>

filter_at(c("Petal.Length", "Sepal.Length"),

any_vars(. > 0)) |>

print(n = 3)

## # A tibble: 150 × 5

##   Sepal.Length Sepal.Width Petal.Length

##                         

## 1          5.1         3.5          1.4

## 2          4.9         3            1.4

## 3          4.7         3.2          1.3

## # . . . with 147 more rows, and 2 more variables:

## # Petal.Width , Species 

iris_df |>

filter_at(vars(Petal.Length, Sepal.Length),

any_vars(. > 0)) |>

print(n = 3)

## # A tibble: 150 × 5

##   Sepal.Length Sepal.Width Petal.Length

##                         

## 1          5.1         3.5          1.4

## 2          4.9         3            1.4

## 3          4.7         3.2          1.3

## # . . . with 147 more rows, and 2 more variables:

## # Petal.Width , Species 
```

你可以使用`filter_if()`对列应用谓词：

```r
iris_df |>

filter_if(is.numeric, all_vars(. 

print(n = 3)
```
